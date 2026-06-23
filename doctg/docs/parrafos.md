# Insumo de redacción — Correcciones metodológicas TG
**Fecha de elaboración:** 4 de mayo de 2026
**Proyecto:** Predicción de condiciones meteorológicas usando PINNs — Caribe de Colombia
**Institución:** Universidad Central — Maestría en Analítica de Datos
**Propósito:** Proporcionar los párrafos listos para insertar en el documento del trabajo de grado,
en respuesta a las observaciones del revisor. Cada sección indica el lugar sugerido de inserción
dentro del documento y reproduce los valores derivados de los cálculos realizados en el proyecto.

---

## INSTRUCCIONES DE USO

Cada bloque de este documento corresponde a uno de los pendientes identificados por el revisor.
Se presenta:
- **Dónde insertar:** sección y ubicación sugerida dentro del TG.
- **Texto listo para copiar:** párrafo redactado con los valores reales del experimento.
- **Notas para el autor:** contexto técnico relevante que el autor debe conocer para adaptar o validar el párrafo.

Los valores numéricos provienen de:
- Script `scripts/calcular_dominio.py` (cálculo de dominio y número de Rossby).
- Evaluación del checkpoint `models/PINN_caribe_excl4_todosreg_R01_L5_rangeL05_epoca_2000.pth`.
- Evaluación del checkpoint `models/PINN_caribe_excl4_todos_R01_L5_epoca_2000.pth`.
- Metadatos del modelo en `models/manifiesto_PINN_caribe_excl4_todos_R01_L5.json`.

---

---

## BLOQUE 1 — Extensión real del dominio de estudio
**Pendiente:** Paso B (análisis completado)
**Dónde insertar:** Capítulo 4 — Metodología, subsección sobre el dominio geográfico y los datos.

### Párrafo sugerido

> El dominio de estudio abarca la totalidad del litoral Caribe colombiano cubierto
> por las estaciones activas consideradas en el análisis. A partir de las coordenadas
> geográficas de las 18 estaciones que integran el conjunto de entrenamiento, se calculó la
> extensión real del dominio: la latitud varía entre 7.4750° N y 11.1150° N, y la longitud
> entre -76.2240° O y -73.9260° O. Aplicando la fórmula de Haversine para distancias
> esféricas, la extensión en la dirección norte–sur es de aproximadamente **404.7 km** y en
> la dirección este–oeste de **252.2 km**, con una diagonal total del dominio de **476.9 km**.
> La malla de predicción se deriva de estas posiciones observadas, con una resolución
> espacial R = 0.1° (≈ 11.1 km), lo que produce una cobertura de puntos de colocación
> proporcional a la densidad de observaciones disponibles en cada subregión del dominio.

### Tabla sugerida (puede incluirse al lado del párrafo)

| Dimensión | Valor |
|-----------|-------|
| Latitud mínima | 7.4750° N |
| Latitud máxima | 11.1150° N |
| Longitud mínima | -76.2240° O |
| Longitud máxima | -73.9260° O |
| Extensión N–S | 404.7 km |
| Extensión E–O | 252.2 km |
| Diagonal del dominio | 476.9 km |
| Resolución de la malla | R = 0.1° (≈ 11.1 km) |
| Latitud media | 9.295° N |

### Notas para el autor

- Este cálculo proviene de `scripts/calcular_dominio.py`, ejecutado sobre
  `data/raw/em_caribe_20251201_20251231.parquet` con las 4 estaciones excluidas ya removidas.
- La extensión E–O es notablemente menor que la N–S porque las estaciones
  se concentran en la franja costera. Esto puede mencionarse como característica
  del dataset: **cobertura costera, no de interior continental**.
- La tabla puede referenciarse como **Tabla 4.X — Extensión del dominio de estudio**.

---

---

## BLOQUE 2 — Justificación y limitación del término de Coriolis
**Pendiente:** Paso B (análisis completado; redacción pendiente)
**Dónde insertar:** Capítulo 4 — Metodología, subsección sobre las ecuaciones de Navier-Stokes
implementadas; y adicionalmente en el Capítulo 6 o sección de limitaciones.

### Párrafo sugerido (para la metodología, subsección de física)

> Las ecuaciones de Navier-Stokes implementadas en este trabajo corresponden a la
> formulación invíscida (ecuaciones de Euler), sin incluir el término de Coriolis
> (−f × **u**), donde f = 2Ω sin(φ) es el parámetro de Coriolis, Ω = 7.2921 × 10⁻⁵ rad/s
> es la velocidad angular de rotación terrestre y φ es la latitud. Para la latitud media
> del dominio (φ_medio = 9.295° N), este parámetro toma el valor
> f = 2.3556 × 10⁻⁵ s⁻¹.
>
> La relevancia del término de Coriolis frente a las fuerzas de inercia se cuantifica
> mediante el **número de Rossby**, definido como Ro = W / (f · L), donde W es la escala
> de velocidad característica del flujo y L es la escala de longitud del dominio. A partir
> de la velocidad de referencia de normalización del modelo (W = 10.80 m/s) y la diagonal
> del dominio (L = 476.9 km), se obtiene Ro = **0.96**. Un número de Rossby cercano a la
> unidad indica que los efectos de inercia y los efectos de Coriolis son comparables en
> magnitud, lo que corresponde a un **régimen geostrófico parcial**. Esto implica que la
> omisión del término de Coriolis en las ecuaciones implementadas constituye una
> **simplificación metodológica significativa**: en el Caribe colombiano, el balance
> geostrófico entre el gradiente de presión y la fuerza de Coriolis es un mecanismo
> dinámico relevante a la escala de cientos de kilómetros. La omisión de este término
> tiende a subestimar la curvatura de las trayectorias del viento y el acoplamiento
> entre el campo de presiones y el campo de velocidades a escala sinóptica.
>
> Esta limitación se declara explícitamente y se considera dentro del alcance del
> trabajo como una simplificación de primer orden que deberá incorporarse en
> versiones futuras del modelo para mejorar su representatividad física.

### Párrafo sugerido (para la sección de limitaciones / conclusiones)

> Una limitación física relevante del modelo desarrollado es la omisión del término de
> Coriolis en las ecuaciones de Navier-Stokes. El número de Rossby calculado para el
> dominio del Caribe colombiano es Ro = 0.96, lo que indica que la rotación terrestre
> ejerce una influencia comparable a las fuerzas de inercia a la escala espacial del estudio
> (≈ 477 km de diagonal). En consecuencia, los resultados obtenidos capturan la dinámica
> local del flujo (gradientes observados en las estaciones) con restricciones físicas de
> consistencia cinemática, pero no incorporan el balance geostrófico que caracteriza la
> circulación atmosférica de mesoescala y escala sinóptica en la región. Esta omisión debe
> tenerse en cuenta al interpretar las predicciones sobre los puntos de la malla que no
> cuentan con observaciones directas.

### Notas para el autor

- **Coriolis**: el término de Coriolis aparece en las ecuaciones de movimiento atmosférico
  como `−f × u` en la ecuación de momento en X y `+f × u` en la de momento en Y
  (convenio meteorológico estándar). Su ausencia en la pérdida de física no genera error
  de cómputo, sino una subespecificación dinámica del campo de vientos.
- **Comparación con literatura:** Raissi et al. (2019) aplican PINNs a flujos en escala de
  laboratorio (dominio < 1 m), donde Coriolis es completamente irrelevante. A escala
  meteorológica, la omisión no es estándar y debe justificarse o reconocerse como limitación.
- **Régimen correcto:** Ro ≈ 1 corresponde técnicamente a transición geostrófica,
  no convección libre (Ro >> 1) ni balance geostrófico puro (Ro << 0.1). Puede usarse
  la expresión "régimen de transición geostrófica" o simplemente indicar que
  Coriolis e inercia son comparables.
- Los valores numéricos de este bloque provienen íntegramente de `scripts/calcular_dominio.py`.

---

---

## BLOQUE 3 — Efecto de la restricción suave de rango sobre el ajuste observacional
**Pendiente:** Paso E (evaluación completada; redacción pendiente)
**Dónde insertar:** Capítulo 5 — Resultados, subsección sobre el experimento con restricción de rango
(actualmente la subsección reporta solo la pérdida total; debe ampliarse con las métricas desagregadas).

### Párrafo sugerido

> Para verificar si la mejora observada en la pérdida total del experimento con restricción
> de rango corresponde a un beneficio genuino sobre el ajuste a los datos y a la física —o
> únicamente a la satisfacción del término de penalización de rango—, se evaluaron las
> métricas desagregadas sobre el conjunto completo de registros del período de estudio.
>
> La Tabla 5.X compara las métricas del modelo de referencia (`todos_R01_L5`, sin
> restricción de rango) frente al modelo con restricción de rango (`todosreg_R01_L5_rangeL05`,
> λ_rango_max = 0.50, τ = 0.05, rampa de 300 épocas).

### Tabla sugerida

| Métrica | `todos_R01_L5` (sin rango) | `todosreg_R01_L5_rangeL05` (con rango) | Variación |
|---------|---------------------------|----------------------------------------|-----------|
| MSE_ux (vel. viento X) | 0.4633 | 0.3799 | **−18.0 %** |
| MSE_uy (vel. viento Y) | 0.4347 | 0.3775 | **−13.2 %** |
| MSE_p (presión) | 1.3688 | 0.9875 | **−27.9 %** |
| RNS (residual Navier-Stokes) | 0.01273 | 0.01534 | +20.5 % |
| FOR_u (% fuera de rango, u) | — | 64.35 % | — |
| FOR_v (% fuera de rango, v) | — | 48.09 % | — |
| FOR_p (% fuera de rango, p) | — | 0.28 % | — |
| Pérdida total | 0.926 | 0.638 | −31.1 % |

*Tabla 5.X — Métricas comparativas entre el modelo base y el modelo con restricción de rango.
Las variables están en unidades normalizadas (adimensionalizadas por las escalas de referencia
del modelo). MSE = error cuadrático medio sobre los puntos de las estaciones; RNS = residual
cuadrático medio de las ecuaciones de Navier-Stokes sobre los puntos de colocación;
FOR = fracción de predicciones de la malla fuera del rango físicamente plausible
(cuantiles 1 % – 99 % de las observaciones).*

### Párrafo de interpretación sugerido

> Los resultados indican que la incorporación de la restricción de rango produce una mejora
> real en el ajuste observacional: el MSE disminuye entre 13 % y 28 % para las tres
> variables de salida con respecto al modelo base. Esta mejora no es un artefacto de la
> función de pérdida: el término de penalización de rango actúa sobre los puntos de la
> malla (sin observación directa), no sobre los puntos de las estaciones donde se calcula
> el MSE. Por tanto, la reducción del MSE observacional refleja una regularización implícita
> del campo de predicción: al forzar que las predicciones en zonas no observadas se mantengan
> dentro de rangos plausibles, el modelo evita soluciones patológicas que propaguen errores
> hacia las regiones con estaciones.
>
> No obstante, la restricción de rango presenta dos efectos secundarios que deben
> considerarse. Primero, el residual de Navier-Stokes aumenta levemente (RNS: 0.01273 →
> 0.01534), lo que indica que la penalización de rango compite parcialmente con la
> restricción física durante el entrenamiento. Segundo, el porcentaje de predicciones fuera
> del rango en la malla completa permanece elevado para las componentes de velocidad
> (FOR_u = 64 %, FOR_v = 48 %), lo que sugiere que la red no alcanza una cobertura completa
> del rango durante el entrenamiento con los 2000 épocas evaluadas. La presión, en cambio,
> logra una compliance casi total (FOR_p = 0.28 %). Este comportamiento asimétrico puede
> deberse a la mayor variabilidad espacial de las componentes de velocidad en comparación
> con la presión, o a que el valor de λ_rango = 0.50 no es suficiente para reducir FOR en
> velocidades de forma significativa dentro del número de épocas disponible.
>
> En conjunto, la restricción de rango mejora el ajuste observacional y reduce la pérdida
> total, pero la elevada FOR en velocidades indica que el experimento no ha convergido
> completamente hacia el cumplimiento de la restricción. La optimización de λ_rango y
> de la duración del entrenamiento queda como línea de trabajo futuro.

### Notas para el autor

- El experimento `todosreg_R01_L5_rangeL05` usa la misma arquitectura, R y λ_física
  que el modelo de referencia (`todos_R01_L5`), con la única adición del término de
  penalización de rango (λ_rango_max = 0.50, τ = 0.05, rampa 300 épocas).
- El INFORME técnico detallado del mecanismo de rango se encuentra en
  `solutions/INFORME_mecanismo_range_loss_tg_2026-04-30.md`.
- La descripción matemática del término de penalización (softplus, cuantiles 1%-99%)
  puede incluirse en la metodología haciendo referencia a ese informe o trasladando
  las ecuaciones directamente al documento del TG.
- El FOR se calcula **sobre los puntos de la malla**, no sobre los puntos de las estaciones.
  Esta distinción es importante para la interpretación correcta de la tabla.

---

---

## BLOQUE 4 — Justificación de la arquitectura de la red neuronal
**Pendiente:** observación del revisor sobre sobredimensionamiento (sin bloque explícito en el plan,
pero forma parte del Paso C)
**Dónde insertar:** Capítulo 4 — Metodología, subsección sobre la arquitectura de la red neuronal.

### Párrafo sugerido (a incluir en la subsección de arquitectura existente)

> La arquitectura seleccionada para la PINN consta de 12 capas ocultas con 1200 neuronas
> cada una, con normalización de pesos desacoplada (GammaBiasLayer con Weight Normalization),
> totalizando aproximadamente **15.89 millones de parámetros entrenables**. El conjunto de
> entrenamiento comprende 13.392 registros, lo que implica un ratio parámetros/datos de
> aproximadamente 1186:1.
>
> Esta elección se sustenta en dos consideraciones. En primer lugar, las PINNs de alta
> capacidad expresiva han mostrado ventajas en problemas de predicción de campos continuos
> con restricciones diferenciales (Raissi et al., 2019; Cuomo et al., 2022), donde la
> red debe aproximar funciones suficientemente suaves sobre todo el dominio, no solo en
> los puntos de observación. En segundo lugar, la pérdida de física (ecuaciones de
> Navier-Stokes sobre los puntos de colocación) actúa como regularizador implícito,
> reduciendo el riesgo de sobreajuste que normalmente se asocia con redes sobredimensionadas
> en el contexto del aprendizaje supervisado puro.
>
> No obstante, se reconoce que la ausencia de una comparativa sistemática con arquitecturas
> más reducidas constituye una limitación metodológica. Para abordarla, se propone como
> trabajo futuro (o experimento adicional antes de la defensa) la evaluación de
> configuraciones con N = 200 (∼480 k parámetros), N = 300 (∼1.08 M) y N = 650 (∼5.07 M)
> neuronas por capa, lo que permitiría determinar si la arquitectura actual está
> sobredimensionada o si la capacidad expresiva adicional se traduce en un mejor ajuste
> físico y observacional.

### Tabla sugerida (experimentos de arquitectura)

| Configuración | Neuronas por capa | Parámetros aprox. | Ratio datos/parámetros |
|--------------|-------------------|-------------------|----------------------|
| N200 | 200 | 480 k | 27.9 : 1 |
| N300 | 300 | 1.08 M | 12.4 : 1 |
| N650 | 650 | 5.07 M | 2.6 : 1 |
| **N1200 (actual)** | **1200** | **15.89 M** | **0.84 : 1** |

*Tabla 4.X — Configuraciones de arquitectura propuestas para la comparativa.
Parámetros estimados con 12 capas ocultas y GammaBiasLayer con Weight Normalization.
El modelo actual (N1200) es el único evaluado en este trabajo.*

### Notas para el autor

- Los experimentos con arquitecturas reducidas (Paso C del plan) están **pendientes de
  ejecución** y forman parte de los trabajos complementarios antes de la defensa.
  Si no se ejecutan antes de la entrega, el párrafo sugerido ya declara la limitación
  y propone los experimentos como trabajo futuro — esto es aceptable académicamente.
- La referencia a Raissi et al. (2019) es pertinente: el trabajo original de PINNs
  usa redes con 4–8 capas y 20–100 neuronas, significativamente más pequeñas.
  La escala de este problema (dominio de cientos de km, 13 k puntos de datos)
  justifica una red más grande, pero la argumentación debe quedar explícita en el documento.
- Cuomo et al. (2022) — "Scientific Machine Learning through Physics-Informed Neural
  Networks" — es una revisión completa de PINNs que incluye discusión sobre arquitecturas.

---

---

## BLOQUE 5 — Validación fuera de muestra (Paso A)
**Pendiente:** Paso A — entrenamiento en curso (días 1–20); evaluación en días 21–31 pendiente
**Dónde insertar:** Capítulo 5 — Resultados, nueva subsección sobre generalización temporal.

Este bloque tiene **dos versiones**:
- **Versión A** (si el entrenamiento termina y los resultados están disponibles antes de la entrega).
- **Versión B** (si el entrenamiento no termina o los resultados son demasiado tardíos para incluirlos).

---

### VERSIÓN A — Con resultados de prueba disponibles

#### Párrafo introductorio sugerido

> Para evaluar la capacidad de generalización del modelo a condiciones temporales no vistas
> durante el entrenamiento, se realizó una partición temporal del conjunto de datos:
> los días 1 a 20 de diciembre de 2025 (8.574 registros) se utilizaron para el entrenamiento,
> y los días 21 a 31 (4.818 registros) constituyeron el conjunto de prueba independiente.
> Se entrenó el modelo con la configuración de referencia (R = 0.1°, λ = 5.0, 2000 épocas,
> todas las estaciones activas) y se evaluaron las métricas MSE_obs, RNS y FOR
> sobre el conjunto de prueba una vez completado el entrenamiento.

#### Tabla sugerida (completar con valores reales una vez disponibles)

| Métrica | Entrenamiento (días 1–20) | Prueba (días 21–31) | Diferencia |
|---------|--------------------------|---------------------|------------|
| MSE_ux | 0.4633 | *(pendiente)* | — |
| MSE_uy | 0.4347 | *(pendiente)* | — |
| MSE_p | 1.3688 | *(pendiente)* | — |
| RNS | 0.01273 | *(pendiente)* | — |
| FOR_u | — | *(pendiente)* | — |
| FOR_v | — | *(pendiente)* | — |

*Tabla 5.Y — Comparación de métricas entre el conjunto de entrenamiento (días 1–20)
y el conjunto de prueba (días 21–31) para el modelo `PINN_caribe_excl4_todos_R01_L5_train20d`.*

#### Párrafo de interpretación — opción favorable (MSE_prueba ≈ MSE_train, diferencia < 20 %)

> Los resultados indican que el modelo generaliza satisfactoriamente al período no visto:
> la diferencia en MSE entre los conjuntos de entrenamiento y prueba es inferior al 20 %
> para las tres variables, lo que sugiere que las restricciones físicas de Navier-Stokes
> actúan efectivamente como regularizador, limitando el sobreajuste incluso con un ratio
> parámetros/datos de 1186:1. Este resultado apoya la premisa fundamental de las PINNs:
> la inclusión de restricciones diferenciales permite obtener soluciones generalizables
> con conjuntos de datos de tamaño reducido.

#### Párrafo de interpretación — opción adversa (MSE_prueba >> MSE_train, diferencia > 20 %)

> Los resultados muestran una diferencia significativa entre las métricas de entrenamiento
> y prueba, lo que indica que el modelo presenta sobreajuste a los datos temporales del
> período de entrenamiento. Esta discrepancia puede atribuirse a la elevada capacidad
> expresiva de la red (15.89 M parámetros) en relación con el número limitado de puntos
> de observación disponibles, y a la variabilidad natural de las condiciones atmosféricas
> del Caribe colombiano entre los primeros 20 y los últimos 11 días de diciembre de 2025.
> Como acciones correctivas se proponen: (1) reducir la capacidad de la red a una
> configuración más conservadora (N = 650 o menor), (2) aumentar el peso de la pérdida
> física (λ > 5.0) para reforzar la regularización, o (3) ampliar el período de datos
> de entrenamiento a meses adicionales disponibles en la fuente Datos Abiertos Colombia.

---

### VERSIÓN B — Sin resultados de prueba disponibles (cláusula de viabilidad teórica)

**Usar si el entrenamiento no termina antes de la entrega del documento o si los
resultados no alcanzan a analizarse.**

#### Párrafo sugerido (para las conclusiones o limitaciones)

> Todos los resultados cuantitativos reportados en este trabajo (Tablas 5.1 a 5.6,
> Figuras 5.5 a 5.8) corresponden al ajuste del modelo sobre el mismo conjunto de datos
> utilizado durante el entrenamiento. No se ha evaluado la capacidad de generalización
> del modelo a períodos temporales no vistos. En consecuencia, no es posible afirmar, a
> partir de los resultados presentados, que el modelo tiene capacidad predictiva sobre
> condiciones futuras o sobre intervalos de tiempo distintos al mes de diciembre de 2025.
>
> Este trabajo debe interpretarse como un **estudio de viabilidad metodológica**: demuestra
> que la formulación PINN con restricciones de Navier-Stokes permite ajustar
> simultáneamente el campo de viento y presión a las observaciones de las estaciones,
> produciendo un campo espacialmente coherente. La validación de la capacidad predictiva
> fuera de muestra constituye una extensión necesaria para que el modelo pueda
> considerarse una herramienta de pronóstico, y se propone como trabajo futuro inmediato.

### Notas para el autor

- El comando para ejecutar la evaluación en el conjunto de prueba una vez terminado
  el entrenamiento es:
  ```bash
  source .venv/bin/activate
  python evaluar_split.py models/PINN_caribe_excl4_todos_R01_L5_train20d_epoca_2000.pth --dias-entrenamiento 20
  ```
- El entrenamiento inició el 4 de mayo de 2026 a las 15:17 (época 0). Con una
  velocidad aproximada de 10 épocas cada 2.5 minutos, se estima que finaliza
  alrededor de las 23:30 del mismo día.
- Los resultados de prueba se guardarán en
  `models/metricas_PINN_caribe_excl4_todos_R01_L5_train20d_prueba_d20_*.json`.
- Los valores de referencia del entrenamiento (días 1–20) son:
  MSE_ux = 0.4633, MSE_uy = 0.4347, MSE_p = 1.3688, RNS = 0.01273.
  Estos provienen del modelo `todos_R01_L5` (entrenado con los 31 días completos),
  y serán reemplazados por los del modelo `train20d` una vez esté disponible.

---

---

## BLOQUE 6 — Barrido de hiperparámetros R = 0.2° (Paso D)
**Pendiente:** Paso D — experimentos no iniciados
**Dónde insertar:** Capítulo 5 — Resultados, subsección del barrido de hiperparámetros;
o Capítulo 6 — Conclusiones, como limitación del análisis de optimalidad.

### Párrafo sugerido (si el barrido se completa antes de la defensa)

> El análisis de la superficie de respuesta para R = 0.2° se completó con las configuraciones
> λ ∈ {1, 3, 5, 7, 10}, complementando el barrido reportado para R = 0.1°. Los resultados
> permiten comparar la optimalidad entre ambas resoluciones de malla y confirmar si la
> configuración R = 0.1°, λ = 5.0 corresponde efectivamente al mínimo global explorado.

> *(Tabla de resultados del barrido R = 0.2° a completar con los valores obtenidos)*

### Párrafo sugerido (como limitación, si el barrido no se completa antes de la entrega)

> El barrido de hiperparámetros realizado en este trabajo cubre la totalidad de los valores
> de λ evaluados para R = 0.1° (λ ∈ {1, 3, 5, 10}), pero no incluye la cobertura
> completa del espacio de búsqueda para R = 0.2°. La configuración disponible para esta
> resolución de malla corresponde únicamente a λ = 3.0 bajo condiciones de entrenamiento
> no estrictamente comparables. En consecuencia, no es posible afirmar con certeza que
> R = 0.1° es la resolución de malla óptima: la superficie de respuesta podría presentar
> un mínimo en R = 0.2° con un valor de λ no evaluado. La completitud del barrido para
> R = 0.2° con λ ∈ {1, 3, 5, 7, 10} se propone como experimento complementario para
> la validación final del modelo antes de la defensa.

### Notas para el autor

- El comando para lanzar el barrido de R = 0.2° es:
  ```bash
  source .venv/bin/activate
  python ejecutar_barrido.py --r 0.2 --lambdas 1 3 5 7 10
  ```
- Este barrido requiere aproximadamente 5 × 8 h ≈ 40 horas de GPU en la configuración
  de entrenamiento actual. No es viable antes de la entrega del documento en 2 días,
  pero sí antes de la defensa.
- El único experimento existente con R = 0.2° es `R02_L3_humo200`, que usa una
  configuración no estándar (max_norm_grad diferente) y no es comparable directamente.

---

---

## RESUMEN DE BLOQUES Y ESTADO

| Bloque | Contenido | Datos disponibles | Acción en el documento |
|--------|-----------|-------------------|----------------------|
| 1 | Extensión del dominio | ✅ Completos | Insertar párrafo + tabla en sección 4 |
| 2 | Justificación Coriolis | ✅ Completos | Insertar párrafo en sección 4 + cláusula en sección 6 |
| 3 | Métricas restricción de rango | ✅ Completos | Ampliar subsección existente en sección 5 |
| 4 | Justificación arquitectura | ✅ Completos | Insertar en subsección de arquitectura, sección 4 |
| 5-A | Validación fuera de muestra | ⏳ Pendiente (entrenamiento ~23:30 h) | Insertar tabla + párrafo después de la evaluación |
| 5-B | Cláusula viabilidad teórica | ✅ Lista (fallback) | Usar si los resultados no están a tiempo |
| 6 | Cláusula barrido R=0.2° | ✅ Lista (cláusula de limitación) | Insertar en sección 5 o 6 |
