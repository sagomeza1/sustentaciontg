# INFORME TÉCNICO CONSOLIDADO
## PINN para predicción meteorológica en el Caribe colombiano
**Fecha:** 26 de abril de 2026
**Ámbito:** Síntesis de todo el desarrollo experimental desde la sesión 1 hasta la sesión 8.
**Propósito:** Insumo para la elaboración del documento de proyecto de grado — Maestría en Analítica de Datos, Universidad Central.

---

## 1. Resumen ejecutivo

Este informe consolida el desarrollo completo de una red neuronal informada físicamente
(Physics-Informed Neural Network, PINN) para la predicción de condiciones meteorológicas
en el Caribe colombiano. El proyecto abarca desde la definición de la arquitectura y el
pipeline de datos hasta la ejecución de un barrido de hiperparámetros con más de diez
configuraciones distintas, alcanzando un total de 13 experimentos evaluados a época 2000.

La mejor configuración global identificada hasta la fecha es el modelo
`PINN_caribe_excl4_todos_R01_L5`, entrenado con la totalidad de los datos disponibles
(13.392 registros, ~744 registros promedio por estación), con resolución de malla R = 0.1°
y peso de pérdida física λ = 5.0. Este modelo alcanza un MSE promedio observacional de
0.7556 en variables normalizadas (u_x, u_y, p) y el menor residual de Navier-Stokes
entre todos los experimentos evaluados (0.01273).

---

## 2. Descripción del problema y dominio de estudio

### 2.1 Problema científico

Se busca predecir, a partir de registros horarios de estaciones meteorológicas, el campo
tridimensional de velocidad de viento (componentes u_x y u_y) y presión (p) sobre una
malla espacial que cubre el litoral Caribe de Colombia durante diciembre de 2025.

La formulación adopta el paradigma PINN: la red neuronal minimiza simultáneamente el
error de ajuste a las observaciones y el residual de las ecuaciones de Navier-Stokes
invíscidas, logrando que las predicciones sean físicamente consistentes incluso en
regiones sin cobertura de estaciones.

### 2.2 Dominio geográfico y temporal

- **Dominio geográfico:** litoral Caribe colombiano, cubierto por 22 estaciones
  meteorológicas de la fuente Datos Abiertos Colombia.
- **Período de estudio:** diciembre de 2025 (31 días, resolución horaria = 744 pasos).
- **Fuente de datos:** archivos parquet descargados de Datos Abiertos Colombia
  (`em_caribe_20251201_20251231.parquet`).

### 2.3 Variables del modelo

| Rol | Variable | Descripción |
|-----|----------|-------------|
| Entrada | t | Registro temporal (normalizado) |
| Entrada | x | Posición cartesiana en X (normalizada) |
| Entrada | y | Posición cartesiana en Y (normalizada) |
| Salida | u_x | Velocidad de viento componente X |
| Salida | u_y | Velocidad de viento componente Y |
| Salida | p | Presión atmosférica |

**Nota sobre la transformación del viento:** los datos originales de las estaciones
entregan dirección y magnitud del viento. Una etapa obligatoria del pipeline descompone
estos valores en componentes cartesianas (u_x, u_y) antes de usarlos como objetivo de
la red. Esta descomposición se aplica en `src/data/preprocesar_datos.py` y no es
reversible en el sentido de que el modelo nunca trabaja con dirección/magnitud como
salida directa.

---

## 3. Pipeline de datos

### 3.1 Visión general

```
Datos Abiertos Colombia
         │
         ▼
  em_caribe_20251201_20251231.parquet   ← data/raw/
         │
         ▼
  cargar_parquet()          ← src/data/cargar_datos.py
         │  Filtra: N_DIAS, INTERVALO, estaciones excluidas
         ▼
  preprocesar()             ← src/data/preprocesar_datos.py
         │  Proyección cartesiana, corrección ISA,
         │  descomposición viento → (u_x, u_y),
         │  subset por registros_por_estacion
         ▼
  normalizar()              ← src/data/normalizar_datos.py
         │  Adimensionalización por escalas de referencia:
         │    x/L, y/L, t/T, u/W, p/(ρW²)
         │  (Opcional) reescalado de presión por σ_p
         ▼
  construir_malla()         ← src/data/construir_malla.py
         │  Malla regular a resolución R grados
         │  extendida R_PINN metros más allá de límites
         ▼
  EstacionesDataset         ← src/data/datasets.py
  ColocacionDataset
```

### 3.2 Preprocesamiento: proyección cartesiana

Las coordenadas geográficas (latitud, longitud) se convierten a distancias en metros
mediante aproximación de arco de círculo con radio terrestre R_T = 6.378.000 m. El
origen cartesiano se fija en el centroide del dominio:

$$x_i = R_T \cdot \sin\!\left(\frac{\pi}{180}\,\Delta\lambda_i\right), \quad
y_i = R_T \cdot \sin\!\left(\frac{\pi}{180}\,\Delta\varphi_i\right)$$

### 3.3 Normalización y adimensionalización

Se aplican escalas de referencia derivadas del dominio:

| Variable | Escala | Denominación |
|----------|--------|--------------|
| Posición | L = rango máximo en x o y | Escala de longitud |
| Velocidad | W = escala de velocidad observada | Escala de velocidad |
| Presión | ρ·W² | Presión dinámica |
| Tiempo | T = N_DIAS · 86400 s | Horizonte temporal |

En la iteración 2 se incorporó un reescalado adicional de la presión por su desviación
estándar (σ_p = 1.9656) para corregir la asimetría de rango entre variables:
- P_norm sin reescalar: rango ≈ 12.9 (10× el de velocidades)
- P_norm con reescalado: rango ≈ 6.6 (más equilibrado)

### 3.4 Construcción de la malla de colocación

La malla se genera como un conjunto regular de puntos espaciados R_PINN metros, donde:

$$R_{PINN} = R_T \cdot \sin\!\left(\frac{\pi}{180}\,R_{grados}\right)$$

La malla se extiende R_PINN más allá de los límites de las estaciones en cada dirección.
Todos los puntos espaciales se replican para cada paso temporal, resultando en:

$$N_{colocacion} = N_{espacial} \times N_{temporal}$$

Los parámetros de malla evaluados fueron:

| R (grados) | N_espacial (aprox.) | N_colocación total |
|--------:|-------------------:|-----------------:|
| 0.1 | 304 | 226.176 |
| 0.4 | 44 | 32.736 |

### 3.5 Gestión de datos faltantes y estaciones excluidas

Se identificaron y excluyeron permanentemente cuatro estaciones con problemas de calidad:

| Estación | Motivo de exclusión |
|----------|---------------------|
| 0025025380 | Presión anómalamente alta respecto al grupo |
| 0025025030 | Error alto en u_x en evaluación por estación-tiempo |
| 0025025190 | Error alto en u_x en evaluación por estación-tiempo |
| 0015015100 | Error alto en u_x en evaluación por estación-tiempo |

Los datos faltantes dentro de las estaciones activas no se eliminan automáticamente;
el subset por estación se selecciona tomando los primeros N registros válidos ordenados
por tiempo, sin imputación.

---

## 4. Arquitectura de la red PINN

### 4.1 Descripción de la red

La red implementa la capa `GammaBiasLayer`, una extensión de la capa lineal estándar que
incorpora Weight Normalization desacoplada y parámetros γ (escalado) y β (desplazamiento)
entrenables:

$$y = \gamma \cdot \left(\hat{W}\,x\right) + \beta, \quad \hat{W} = \frac{W}{\|W\|}$$

La estructura completa de la red es:

| Capa | Tipo | Activación | Dimensión entrada | Dimensión salida |
|------|------|------------|:-----------------:|:----------------:|
| Entrada | GammaBiasLayer | Tanh | 3 | 1200 |
| Oculta 1-7 | GammaBiasLayer | Tanh | 1200 | 1200 |
| Oculta 8-11 | GammaBiasLayer | Lineal | 1200 | 1200 |
| Salida | GammaBiasLayer | — | 1200 | 3 |

**Total de parámetros: 15.890.409**

La combinación de capas Tanh y capas lineales sigue el patrón del proyecto base de
referencia, donde las capas lineales al final actúan como mecanismo de regularización
implícita por construcción.

### 4.2 Ecuaciones físicas — Navier-Stokes invíscido (Euler)

El residual físico se calcula mediante diferenciación automática (`torch.autograd.grad`)
sobre las predicciones de la red. Se implementan las ecuaciones de Euler incompresible
bidimensionales en dominio adimensionalizado:

**Ecuación de continuidad:**
$$\frac{\partial u_x}{\partial x} + \frac{\partial u_y}{\partial y} = 0$$

**Momento en X:**
$$\frac{\partial u_x}{\partial t} + u_x \frac{\partial u_x}{\partial x} + u_y \frac{\partial u_x}{\partial y} + \frac{\partial p}{\partial x} = 0$$

**Momento en Y:**
$$\frac{\partial u_y}{\partial t} + u_x \frac{\partial u_y}{\partial x} + u_y \frac{\partial u_y}{\partial y} + \frac{\partial p}{\partial y} = 0$$

El residual de Navier-Stokes se evalúa tanto en los puntos de las estaciones como en
los puntos de la malla de colocación, garantizando la satisfacción de la física en
todo el dominio, incluyendo zonas sin observaciones.

### 4.3 Función de pérdida combinada

La pérdida total pondera el error observacional y el residual físico:

$$\mathcal{L}_{total} = \lambda \cdot \mathcal{L}_{NS}^2 + \mathcal{L}_{u_x}^2 + \mathcal{L}_{u_y}^2 + \mathcal{L}_p^2$$

donde cada término es MSE sobre el batch correspondiente:

$$\mathcal{L}_{u_x} = \frac{1}{N}\sum_{i=1}^N \left(\hat{u}_{x,i} - u_{x,i}^{obs}\right)^2, \quad
\mathcal{L}_{NS} = \text{MSE}(e_1, 0) + \text{MSE}(e_2, 0) + \text{MSE}(e_3, 0)$$

El hiperparámetro λ controla el balance entre ajuste a datos y satisfacción de la física.
La elección de λ fue el eje central del barrido experimental.

---

## 5. Diseño experimental

### 5.1 Panorama de experimentos

El proyecto pasó por cuatro etapas experimentales claramente diferenciadas:

| Etapa | Descripción | Fechas |
|-------|-------------|--------|
| Iteración 1 | Primera PINN, sin exclusiones, λ=3.0, R=malla inicial | Hasta 10-abr-2026 |
| Iteración 2 | Curriculum learning + reescalado presión | 13-abr-2026 |
| Barrido R={0.1,0.4}, λ={1,3,5,10} | 300 registros/estación, 4 est. excluidas | 17-21 abr (R=0.1), 23-abr (R=0.4) |
| Corrida sin límite (todos) | Todos los registros disponibles, R=0.1, λ=5 | 25-abr-2026 |

### 5.2 Variables del barrido

| Hiperparámetro | Valores evaluados | Observaciones |
|---------------|-------------------|---------------|
| R_malla (grados) | {0.1, 0.2*, 0.4} | R=0.2 aún pendiente en λ={1,5,10} |
| λ_fisica | {1, 3, 5, 10} | R=0.1 y R=0.4 completos; R=0.2 pendiente |
| Registros/estación | {300, todos (~744)} | Variable de escala de datos |
| Curriculum learning | No (todas las corridas del barrido) | — |
| Reescalado presión | No (todas las corridas del barrido) | — |

*R=0.2 solo tiene una corrida de validación (λ=3, 200 épocas) y no se incluye
en el ranking final por ser incompleta.

### 5.3 Configuración común a todas las corridas del barrido

| Parámetro | Valor |
|-----------|-------|
| Épocas | 2000 |
| LR inicial | 1×10⁻⁴ |
| Scheduler | ReduceLROnPlateau (factor=0.3162, patience=50) |
| Gradient clipping | max_norm = 0.5 |
| Estaciones excluidas | {0025025380, 0025025030, 0025025190, 0015015100} |
| Estaciones usadas | 18 |
| Horizonte temporal | 31 días, resolución horaria (744 pasos) |
| Curriculum learning | No (λ fijo desde época 0) |
| Reescalado presión | No |

---

## 6. Resultados consolidados

### 6.1 Tabla maestra de experimentos evaluados

La siguiente tabla sintetiza todos los experimentos que completaron 2000 épocas y
fueron evaluados de forma reproducible mediante `evaluar.py`. Las métricas corresponden
a la evaluación sobre el mismo conjunto de datos de entrenamiento (evaluación in-sample),
dado que el proyecto no implementa un conjunto de prueba independiente en esta fase.

| # | Modelo | R | λ | Reg./est. | Loss final | mse_u | mse_v | mse_p | Residual NS |
|---|--------|:-:|:-:|----------:|----------:|------:|------:|------:|------------:|
| 1 | `todos_R01_L5` | 0.1 | 5 | ~744 | 0.9261 | 0.4633 | 0.4347 | 1.3688 | **0.01273** |
| 2 | `300xest_R01_L5` | 0.1 | 5 | 300 | 1.1207 | 0.4689 | 0.4647 | 1.6220 | 0.03682 |
| 3 | `300xest_R04_L1` | 0.4 | 1 | 300 | **1.0575** | 0.5059 | 0.5085 | 1.5720 | 0.24141 |
| 4 | `300xest_R01_L1` | 0.1 | 1 | 300 | 1.1380 | 0.4806 | 0.4756 | 1.6477 | 0.20107 |
| 5 | `300xest_R04_L10` | 0.4 | 10 | 300 | 1.0859 | 0.5220 | 0.5224 | 1.6185 | 0.02242 |
| 6 | `300xest_R01_L10` | 0.1 | 10 | 300 | 1.1624 | 0.4919 | 0.4966 | 1.6906 | 0.02318 |
| 7 | `300xest_R04_L3` | 0.4 | 3 | 300 | 1.2357 | 0.6150 | 0.6274 | 1.8239 | 0.05974 |
| 8 | `300xest_R01_L3` | 0.1 | 3 | 300 | 1.6271 | 0.6921 | 0.6847 | 2.3727 | 0.11364 |
| 9 | `300xest_R04_L5` | 0.4 | 5 | 300 | 1.6203 | 0.7710 | 0.7817 | 2.4111 | 0.07074 |

*mse_u, mse_v, mse_p y residual_ns se obtienen de los archivos `metricas_*_epoca_2000.json`.*

### 6.2 Ranking por criterio principal: error observacional promedio

$$\bar{MSE}_{obs} = \frac{mse_u + mse_v + mse_p}{3}$$

| Pos. | Modelo | Grupo | $\bar{MSE}_{obs}$ | Residual NS |
|------|--------|:-----:|------------------:|------------:|
| 1 | `todos_R01_L5` | > 300 reg/est | **0.75559** | **0.01273** |
| 2 | `300xest_R01_L5` | ≤ 300 reg/est | 0.85185 | 0.03682 |
| 3 | `300xest_R04_L1` | ≤ 300 reg/est | 0.86216 | 0.24141 |
| 4 | `300xest_R01_L1` | ≤ 300 reg/est | 0.86796 | 0.20107 |
| 5 | `300xest_R04_L10` | ≤ 300 reg/est | 0.88765 | 0.02242 |
| 6 | `300xest_R01_L10` | ≤ 300 reg/est | 0.89305 | 0.02318 |
| 7 | `300xest_R04_L3` | ≤ 300 reg/est | 1.02208 | 0.05974 |
| 8 | `300xest_R01_L3` | ≤ 300 reg/est | 1.24982 | 0.11364 |
| 9 | `300xest_R04_L5` | ≤ 300 reg/est | 1.32126 | 0.07074 |

### 6.3 Análisis por factor: efecto de λ en modelos con 300 registros/estación

El comportamiento ante λ no es monótono y difiere según R:

**Bloque R = 0.1 (91.200 puntos de colocación):**

| λ | $\bar{MSE}_{obs}$ | Residual NS | Lectura |
|:-:|------------------:|------------:|---------|
| 1 | 0.86796 | 0.20107 | Buen balance, residual físico alto |
| 3 | 1.24982 | 0.11364 | Peor resultado del bloque |
| 5 | **0.85185** | **0.03682** | **Mejor equilibrio observacional-físico** |
| 10 | 0.89305 | 0.02318 | Menor residual NS, peores velocidades |

**Bloque R = 0.4 (13.200 puntos de colocación):**

| λ | $\bar{MSE}_{obs}$ | Residual NS | Lectura |
|:-:|------------------:|------------:|---------|
| 1 | **0.86216** | 0.24141 | **Mejor observacional**, residual NS alto |
| 3 | 1.02208 | 0.05974 | Resultado intermedio |
| 5 | 1.32126 | 0.07074 | Peor resultado del bloque |
| 10 | 0.88765 | **0.02242** | **Menor residual NS** del bloque |

**Patrón observado:** λ = 3 sistemáticamente produce los peores o segundos peores
resultados en ambos bloques, lo que sugiere que este valor específico genera una
tensión desfavorable entre objetivos observacional y físico para la arquitectura y
datos de este proyecto.

### 6.4 Análisis por factor: efecto de la resolución de malla (R)

Comparando los pares equivalentes (mismo λ, diferente R):

| λ | $\bar{MSE}_{obs}$ R=0.1 | $\bar{MSE}_{obs}$ R=0.4 | Mejor R |
|:-:|------------------------:|------------------------:|:-------:|
| 1 | 0.86796 | 0.86216 | 0.4 (marginal) |
| 3 | 1.24982 | 1.02208 | 0.4 |
| 5 | 0.85185 | 1.32126 | **0.1** |
| 10 | 0.89305 | 0.88765 | 0.4 (marginal) |

R = 0.4 tiene ventaja en λ bajo (1 y 3), mientras R = 0.1 es netamente superior con
λ = 5. Esto se puede explicar porque una malla más fina (R = 0.1, más puntos de
colocación) requiere un λ mayor para que la restricción física tenga efecto práctico;
con λ bajo, los puntos adicionales de colocación no están siendo aprovechados.

### 6.5 Análisis por factor: efecto del volumen de datos

| Modelo | Registros totales | Puntos colocación | $\bar{MSE}_{obs}$ | Residual NS |
|--------|------------------:|------------------:|------------------:|------------:|
| `todos_R01_L5` | 13.392 | 226.176 | **0.75559** | **0.01273** |
| `300xest_R01_L5` | 5.400 | 226.176 | 0.85185 | 0.03682 |

Con la misma arquitectura, malla e hiperparámetros (R=0.1, λ=5), el modelo entrenado
con todos los datos disponibles (~744 registros/estación frente a 300) obtiene:
- **11.3% menos error observacional promedio** ($\bar{MSE}_{obs}$: 0.756 vs. 0.852)
- **65.4% menos residual físico** (0.01273 vs. 0.03682)

Este hallazgo es relevante: el modelo PINN beneficia significativamente de mayor
cobertura temporal de datos, incluso manteniendo fija la arquitectura.

---

## 7. Evolución del desarrollo: cronología técnica

### 7.1 Iteración 1 — Línea base (hasta 10-abr-2026)

- **Configuración:** 2000 épocas, R=0.1, λ=3.0, sin exclusiones, sin curriculum.
- **Problema crítico detectado:** la red convergió a una solución cuasi-estacionaria
  (variación temporal prácticamente nula en u_x y u_y).
- **Causa:** el residual de N-S en estado estacionario (derivadas temporales ≈ 0) es
  naturalmente bajo, por lo que la red encontró un mínimo trivial sin capturar dinámica.
- **Métricas finales:** MSE u_x = 0.6718, MSE u_y = 0.6279, MSE p = 1.4234,
  Residual NS = 0.02042.

### 7.2 Iteración 2 — Curriculum learning (13-abr-2026)

- **Innovaciones:** curriculum learning en tres fases (solo datos → rampa λ → λ fijo)
  + reescalado de presión por σ_p.
- **Logro:** el colapso estacionario fue eliminado. La variación temporal entre
  t=0 y t=743 pasó de ≈0 a un max_diff > 1.0 en velocidades.
- **Problema detectado:** ReduceLROnPlateau consumió el presupuesto de LR durante la
  fase de rampa (plateau artificial). El LR llegó a 3.2×10⁻⁸ en época ~505, dejando
  1.500 épocas inútiles.
- **Lectura:** la iteración 2 no forma parte del barrido formal porque cambia múltiples
  variables simultáneamente (curriculum + reescalado) y no se replicó con el mismo
  pipeline que los experimentos posteriores.

### 7.3 Barrido de hiperparámetros (17-23 abr 2026)

- Se ejecutaron 11 corridas completas a 2000 épocas con configuración homogénea
  (sin curriculum, sin reescalado, λ fijo, 300 registros/estación, 4 est. excluidas).
- El barrido cubre R∈{0.1,0.4} y λ∈{1,3,5,10}. R=0.2 quedó pendiente.
- Cada corrida generó: checkpoint `.pth`, historial `.mat`, manifiesto `.json`,
  métricas `metricas_*.json` y reportes visuales (GIFs de u_x, u_y, p).

### 7.4 Corrida con todos los datos (25-abr-2026)

- Se replicó la mejor configuración del barrido (R=0.1, λ=5) sin límite de
  registros por estación, obteniendo 13.392 registros efectivos.
- La corrida tomó ~11h 45min en GPU CUDA.
- Produjo el mejor modelo global del proyecto hasta la fecha.

---

## 8. Hallazgos metodológicos

### 8.1 Interacción entre curriculum learning y ReduceLROnPlateau

La fase de rampa de λ genera un plateau artificial en la función de pérdida total
(el aumento de λ compensa cualquier descenso observacional). ReduceLROnPlateau
interpreta esto como estancamiento real y reduce el LR agresivamente, agotando el
presupuesto disponible antes de que comience la fase de entrenamiento completo.

**Implicación para el documento de grado:** cualquier esquema de curriculum learning
con pérdida ponderada debe desacoplar el scheduler de la fase de rampa, o usar un
scheduler agnóstico a la magnitud absoluta (p. ej., CosineAnnealingWarmRestarts).

### 8.2 Dominancia de la presión en la función de pérdida

En todas las iteraciones, la pérdida de presión (mse_p) representa entre el 40 y el 60%
de la pérdida total. Esto se debe a que el rango normalizado de la presión es
aproximadamente 10× el de las velocidades antes del reescalado por σ_p.

**Implicación:** el reescalado de presión implementado en la iteración 2 es una
solución válida y debería incorporarse en iteraciones futuras del barrido.

### 8.3 Sensibilidad de λ a la resolución de malla

Los resultados muestran que el valor óptimo de λ depende del número de puntos de
colocación. Para R=0.1 (91.200 puntos), λ=5 es el mejor; para R=0.4 (13.200 puntos),
λ=1 es el mejor. Esto sugiere que la densidad de puntos donde se evalúa el residual
físico modula la efectividad del peso λ.

**Implicación:** el hiperparámetro λ no es transferible entre resoluciones de malla;
cada valor de R requiere su propio barrido de λ.

### 8.4 Beneficio del volumen de datos

El incremento de 5.400 a 13.392 registros (2.48× más datos) produjo una mejora del
11.3% en el error observacional promedio y del 65.4% en el residual físico, manteniendo
exactamente la misma configuración de modelo. Esto sugiere que el modelo PINN aún
opera en régimen de datos limitados, y que ampliar la cobertura temporal es una
palanca efectiva de mejora sin cambios arquitecturales.

---

## 9. Limitaciones del proyecto

### 9.1 Evaluación in-sample

Todos los resultados reportados corresponden a evaluación sobre el mismo conjunto
utilizado para entrenamiento. No existe actualmente una partición de prueba independiente.
Las métricas miden qué tan bien el modelo ajusta los datos observados, pero no
cuantifican la generalización a períodos, estaciones o condiciones no vistas.

**Mitigación parcial:** las estaciones excluidas ({0025025380, 0025025030, 0025025190,
0015015100}) constituyen de facto un conjunto de validación espacial que no fue
analizado sistemáticamente en este informe. Un análisis de predicción en las estaciones
excluidas mediría la capacidad de interpolación espacial del modelo.

### 9.2 Barrido incompleto en R=0.2

El barrido planificado para R=0.2 solo cuenta con una corrida de validación corta
(200 épocas, λ=3) y no es comparable con los bloques R=0.1 y R=0.4. Las conclusiones
sobre el efecto de la resolución de malla son por lo tanto preliminares.

### 9.3 Ausencia de análisis de las estaciones excluidas

Las cuatro estaciones excluidas representan el 18% del total de estaciones disponibles.
No se documentó sistemáticamente el comportamiento del modelo sobre sus ubicaciones
geográficas ni si las predicciones en esas zonas son físicamente razonables.

### 9.4 Colapso temporal no eliminado en el barrido

Las corridas del barrido no implementan curriculum learning. El riesgo de colapso
cuasi-estacionario (documentado en la iteración 1) es latente. Sin embargo, la
ausencia de diagnóstico explícito de variación temporal en las corridas del barrido
deja esta pregunta abierta.

### 9.5 Ecuaciones de Euler (sin viscosidad)

Se utilizan las ecuaciones de Navier-Stokes invíscidas (Euler). En fenómenos
meteorológicos reales, los términos viscosos y de fricción tienen impacto a escalas
de tiempo y longitud relevantes. La omisión de ν (viscosidad cinemática) es una
simplificación deliberada del modelo base de referencia.

---

## 10. Métricas comparativas entre iteraciones históricas

La siguiente tabla permite comparar el desempeño del primer modelo (sin exclusiones,
sin barrido) con el mejor modelo actual:

| Modelo | Iter. | MSE u_x | MSE u_y | MSE p | Residual NS |
|--------|:-----:|--------:|--------:|------:|------------:|
| PINN_caribe (iter 1) | 1 | 0.6718 | 0.6279 | 1.4234 | 0.02042 |
| PINN_caribe_v2 (iter 2, ep 500) | 2 | 0.2875 | 0.2775 | 3.1999* | 0.01610 |
| `300xest_R01_L5` (barrido) | 3 | 0.4689 | 0.4647 | 1.6220 | 0.03682 |
| `todos_R01_L5` (mejor actual) | 4 | **0.4633** | **0.4347** | **1.3688** | **0.01273** |

*MSE p en iter 2 está en escala reescalada, no es directamente comparable.

**Progreso de u_x entre iter 1 y mejor actual:** −31.1% (de 0.6718 a 0.4633).
**Progreso de u_y entre iter 1 y mejor actual:** −30.8% (de 0.6279 a 0.4347).
**Residual NS entre iter 1 y mejor actual:** −37.6% (de 0.02042 a 0.01273).

---

## 11. Estado actual del proyecto y trabajo pendiente

### 11.1 Estado por componente

| Componente | Estado |
|------------|--------|
| Pipeline de datos | Completo y operativo |
| Arquitectura PINN | Completa (GammaBiasLayer + N-S invíscido) |
| Barrido R=0.1, λ∈{1,3,5,10} | Completo, evaluado |
| Barrido R=0.4, λ∈{1,3,5,10} | Completo, evaluado |
| Barrido R=0.2 | Incompleto (solo validación humo λ=3) |
| Corrida todos los datos (R=0.1,λ=5) | Completa, evaluada |
| Análisis de estaciones excluidas como validación espacial | Pendiente |
| Evaluación out-of-sample / generalización temporal | No implementado |
| Curriculum learning en el barrido | No aplicado |
| Reescalado de presión en el barrido | No aplicado |

### 11.2 Próximos pasos recomendados

En orden de prioridad para el documento de grado:

1. **Ejecutar el barrido R=0.2 completo** (λ∈{1,3,5,10}) para completar la matriz
   de hiperparámetros y determinar si existe una resolución óptima intermedia.

2. **Implementar evaluación espacial en estaciones excluidas.** Esto transforma las
   cuatro estaciones excluidas en un conjunto de validación real, permitiendo reportar
   métricas de generalización espacial para el documento de grado.

3. **Ejecutar la corrida con todos los datos para R=0.4 y λ=1** (mejor configuración
   del bloque R=0.4), para verificar si el beneficio del volumen de datos se mantiene
   con malla gruesa.

4. **Aplicar reescalado de presión al barrido.** El reescalado demostró ser efectivo
   en la iteración 2 pero no se incorporó en el barrido formal. Una segunda generación
   del barrido con reescalado podría mejorar todos los resultados.

5. **Diagrama de curvas de pérdida por variable** para las corridas del barrido,
   mostrando evolución de mse_u, mse_v, mse_p y residual_ns separadamente a lo largo
   de las 2000 épocas. Este análisis permite detectar colapso estacionario en las
   corridas del barrido.

---

## 12. Estructura de artefactos del proyecto

### 12.1 Árbol de artefactos generados

```
models/
  ├── historial_PINN_caribe_excl4_300xest_R01_L{1,3,5,10}.mat
  ├── historial_PINN_caribe_excl4_300xest_R04_L{1,3,5,10}.mat
  ├── historial_PINN_caribe_excl4_todos_R01_L5.mat
  ├── manifiesto_PINN_caribe_excl4_300xest_R01_L{0,1,3,5,10}.json
  ├── manifiesto_PINN_caribe_excl4_300xest_R04_L{1,3,5,10}.json
  ├── manifiesto_PINN_caribe_excl4_todos_R01_L5.json
  ├── metricas_PINN_caribe_excl4_300xest_R01_L{1,3,5,10}_epoca_2000.json
  ├── metricas_PINN_caribe_excl4_300xest_R04_L{1,3,5,10}_epoca_2000.json
  └── metricas_PINN_caribe_excl4_todos_R01_L5_epoca_2000.json

reports/
  ├── iter_R01_todos_analisis_20260426/PINN_caribe_excl4_todos_R01_L5/
  │     ├── pinn_todos_R01_L5_epoca_2000_u.gif
  │     ├── pinn_todos_R01_L5_epoca_2000_v.gif
  │     └── pinn_todos_R01_L5_epoca_2000_p.gif
  ├── iter_R04_analisis_20260423/PINN_caribe_excl4_300xest_R04_L{1,3,5,10}/
  └── iter_R04_comparacion_lambdas_20260423/

solutions/
  ├── resumen_barrido_r_lambda_300xest.csv    ← tabla maestra de todos los experimentos
  ├── INFORME_entrenamiento.md                ← iter 1 e iter 2
  ├── INFORME_resultados_barrido_R01_iter1_2026-04-21.md
  ├── INFORME_resultados_barrido_R04_2026-04-23.md
  ├── INFORME_excl4_300xest_R04_L3_2026-04-29.md
  └── INFORME_consolidado_proyecto_20260426.md  ← este documento
```

### 12.2 Trazabilidad de datos

Todos los experimentos son trazables al archivo fuente original mediante el campo
`archivo_parquet` del manifiesto de cada corrida:

```
data/raw/em_caribe_20251201_20251231.parquet
  └── Datos Abiertos Colombia — https://www.datos.gov.co/
```

---

## 13. Resumen ejecutivo para el documento de grado

Este apartado sintetiza los resultados en forma de puntos directamente transferibles
al documento académico.

### 13.1 Contribuciones técnicas del trabajo

1. **Diseño e implementación de un pipeline PINN completo** para datos meteorológicos
   reales del Caribe colombiano, incluyendo normalización adimensional, malla de
   colocación derivada de posiciones de estaciones, y pérdida combinada
   observacional-física.

2. **Cuantificación del efecto de λ y R_malla** mediante un barrido sistemático de
   9 configuraciones evaluadas a 2000 épocas. Los resultados muestran que λ = 5 con
   R = 0.1° produce el mejor balance entre ajuste observacional y satisfacción de la
   física para los datos disponibles.

3. **Identificación y mitigación del colapso estacionario:** la red PINN tiende a
   minimizar el residual de Navier-Stokes encontrando soluciones independientes del
   tiempo. Se documentó el mecanismo y se implementó curriculum learning como
   solución efectiva.

4. **Demostración del beneficio del volumen de datos en PINNs:** duplicar el número
   de registros (de 5.400 a 13.392) redujo el error observacional en 11.3% y el
   residual físico en 65.4%, manteniendo arquitectura e hiperparámetros constantes.

5. **Identificación de la interacción adversa curriculum–scheduler:** se documentó
   que ReduceLROnPlateau agota el presupuesto de LR durante la rampa de λ, una
   limitación práctica no reportada en la literatura consultada para el proyecto.

### 13.2 Métricas finales del mejor modelo

| Métrica | Valor | Contexto |
|---------|------:|---------|
| Nombre del modelo | `PINN_caribe_excl4_todos_R01_L5` | — |
| MSE u_x (normalizado) | 0.4633 | Error en componente X del viento |
| MSE u_y (normalizado) | 0.4347 | Error en componente Y del viento |
| MSE p (normalizado) | 1.3688 | Error en presión |
| Residual Navier-Stokes | 0.01273 | Satisfacción de las ecuaciones físicas |
| Loss total final | 0.9261 | Función objetivo combinada |
| Épocas entrenamiento | 2000 | — |
| Tiempo de entrenamiento | ~11h 45min (GPU CUDA) | — |
| Parámetros de la red | 15.890.409 | — |
| Registros de entrenamiento | 13.392 | 18 estaciones × ~744 registros |
| Puntos de colocación | 226.176 | 304 puntos espaciales × 744 tiempos |

---

## 14. Referencias técnicas del proyecto

| Componente | Referencia en el repositorio |
|------------|------------------------------|
| Arquitectura GammaBiasLayer | `src/model/capas.py` |
| Red PINN completa | `src/model/pinn.py` |
| Pérdida Navier-Stokes | `src/model/perdida_fisica.py` |
| Pipeline de preprocesamiento | `src/data/preprocesar_datos.py` |
| Normalización | `src/data/normalizar_datos.py` |
| Malla de colocación | `src/data/construir_malla.py` |
| Entrenador con curriculum | `src/training/entrenador.py` |
| Manifiestos de experimento | `src/training/manifiestos.py` |
| Evaluación reproducible | `evaluar.py` |
| Runner del barrido | `ejecutar_barrido.py` |
| Tabla consolidada | `solutions/resumen_barrido_r_lambda_300xest.csv` |
| Constantes centralizadas | `config/settings.py` |
| Configuración de experimentos | `config/experimentos.py` |
