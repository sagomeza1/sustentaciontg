# Propuesta de acciones correctivas — Revisión Diego Gerardo (04/05/2026)

> Estado actual del documento: borrador de planificación. Cada punto mapea directamente
> a una observación del revisor, indica la acción concreta, su tipo (experimento / edición
> de texto / ambos) y la prioridad.

---

## Observación 1 — Validación fuera de muestra (CRÍTICO)

**Problema identificado:** todas las métricas reportadas en Tablas 5.1–5.6 y Figuras 5.5–5.8
corresponden a evaluación *in-sample*. No existe partición entrenamiento/prueba independiente.

### Acción A1-a — Experimento de validación temporal (preferida si hay tiempo)

| Parámetro | Valor |
|-----------|-------|
| Datos de entrenamiento | Días 1–20 de diciembre de 2025 (≈ 480 registros/estación × 18 estaciones = 8.640 registros) |
| Datos de prueba | Días 21–31 de diciembre de 2025 (≈ 264 registros/estación × 18 estaciones = 4.752 registros) |
| Configuración | Igual a `todos_R01_L5`: R = 0,1°, λ = 5, 2.000 épocas |
| Métricas a reportar | MSE_obs (MSE_ux, MSE_uy, MSE_p), R_NS, FOR en conjunto de **prueba** |

**Archivos a modificar tras el experimento:**
- `18_resultados.tex`: añadir subsección "Validación fuera de muestra" con tabla de métricas en prueba y comparación entrenamiento vs prueba.
- `20_conclusiones.tex`: actualizar §Hallazgos principales e incluir interpretación sobre capacidad de generalización.
- Si el desempeño en prueba es significativamente peor: añadir subsección de análisis de sobreajuste con propuestas (aumentar λ, reducir arquitectura, más datos temporales).

### Acción A1-b — Declaración explícita de limitación (si no hay tiempo de ejecutar A1-a)

En `20_conclusiones.tex`, sección "Limitaciones del alcance actual", reemplazar el ítem
"Evaluación in-sample" actual por el siguiente texto (pendiente validación del director):

> *"Los resultados reportados (Tablas 5.1–5.6 y Figuras 5.5–5.8) corresponden exclusivamente
> a evaluación sobre los datos de entrenamiento; no se ha realizado evaluación sobre un
> conjunto de prueba independiente. En consecuencia, este trabajo constituye un estudio
> de viabilidad teórica sobre la aplicabilidad de PINNs en el dominio Caribe colombiano,
> y no una validación de capacidad predictiva fuera de muestra."*

Adicionalmente, agregar en `18_resultados.tex`, al inicio de la sección de métricas, una
nota explícita:

> *"Nota metodológica: todas las métricas reportadas en este capítulo son medidas de ajuste
> sobre datos de entrenamiento (evaluación in-sample). No se incluye partición de prueba
> independiente en esta versión del estudio."*

**Prioridad:** CRÍTICA — debe resolverse antes de la defensa, con A1-a o A1-b.

---

## Observación 2 — Omisión del término de Coriolis (IMPORTANTE)

**Problema identificado:** las ecuaciones de Euler incompresible implementadas no incluyen el
término de Coriolis (f × u). A la escala del Caribe colombiano (cientos de km), este término
es fundamental para describir el equilibrio geostrófico.

### Acción A2-a — Calcular la extensión real del dominio

A partir de los datos (latitud y longitud mínima y máxima de las 18 estaciones validadas),
calcular:

- Latitud mínima y máxima → extensión Norte–Sur en km
- Longitud mínima y máxima → extensión Este–Oeste en km
- Latitud media del dominio → parámetro de Coriolis f = 2Ω sin(φ_media),
  con Ω = 7,2921 × 10⁻⁵ rad/s

Incorporar estos valores en `17_metodologia.tex`, subsección "Datos de entrada", justo después
de la descripción de estaciones. Ejemplo de redacción (sustituir valores con los reales):

> *"El dominio estudiado se extiende aproximadamente entre las latitudes [φ_min]° N y
> [φ_max]° N, y entre las longitudes [λ_min]° O y [λ_max]° O, cubriendo una extensión
> Norte–Sur de ≈ XX km y Este–Oeste de ≈ XX km, con centroide en [φ_c]° N. La escala
> horizontal característica del dominio es L ≈ XX km."*

### Acción A2-b — Justificar explícitamente la omisión de Coriolis en metodología

Calcular el número de Rossby del dominio:

```
Ro = U / (f · L)
```

donde U es la velocidad característica del viento (escala observada), f es el parámetro de
Coriolis en la latitud media y L es la escala horizontal del dominio.

- Si Ro >> 1 (efectos locales dominan): Coriolis puede despreciarse y se justifica la
  simplificación a Euler incompresible.
- Si Ro ~ 1 o Ro < 1: el término de Coriolis es relevante y su omisión es una
  **limitación explícita** del modelo que debe declararse.

En `17_metodologia.tex`, al final de la sección "Ecuaciones físicas: Navier-Stokes invíscido",
añadir un párrafo titulado "Justificación de la omisión del término de Coriolis" con:
1. El valor calculado de f para la latitud media del dominio.
2. El valor calculado de Ro.
3. La conclusión (si Ro >> 1: justificación de la simplificación; si no: declaración
   de limitación y trabajo futuro).

En `20_conclusiones.tex`, sección "Limitaciones del alcance actual", añadir ítem:

> *"Las ecuaciones implementadas corresponden a Euler incompresible sin término de Coriolis.
> A la escala del dominio estudiado (L ≈ XX km), el número de Rossby calculado es
> Ro = [valor]. [Si Ro << 1: "Esto implica que el balance geostrófico no está representado
> y constituye una limitación física fundamental del modelo."] [Si Ro >> 1: "Esto indica
> que los efectos locales de inercia dominan sobre la rotación terrestre en la escala
> estudiada, lo que justifica a priori la simplificación utilizada."]"*

**Prioridad:** ALTA — requiere cálculo numérico y edición de texto (no experimento de
entrenamiento). Puede completarse en pocas horas.

---

## Observación 3 — Arquitectura sobredimensionada (IMPORTANTE)

**Problema identificado:** la red tiene 15.890.409 parámetros entrenables para 13.392 puntos
de entrenamiento (ratio ≈ 1.186:1). Sin justificación ni comparación con arquitecturas
menores.

### Acción A3-a — Experimentos con arquitecturas reducidas

Ejecutar tres corridas adicionales con la configuración R = 0,1°, λ = 5, datos completos
(13.392 registros), variando solo la arquitectura:

| Experimento | Capas ocultas × neuronas | Parámetros aprox. | Denominación sugerida |
|-------------|--------------------------|-------------------|-----------------------|
| Small (~500 k) | 5 × 300 | ≈ 455 k | `todos_R01_L5_small` |
| Medium (~1 M) | 7 × 400 | ≈ 1,1 M | `todos_R01_L5_medium` |
| Large (~5 M) | 9 × 750 | ≈ 5,1 M | `todos_R01_L5_large` |

> **Nota de implementación:** los parámetros exactos dependen de la capa GammaBiasLayer.
> Calcular el conteo real antes de ejecutar para confirmar los rangos objetivo.

Reportar para cada arquitectura a época 2.000: MSE_obs (MSE_ux, MSE_uy, MSE_p), R_NS y
Loss total.

**Archivos a modificar tras los experimentos:**
- `17_metodologia.tex`: añadir párrafo de justificación de la arquitectura base (ver A3-b).
- `18_resultados.tex`: añadir tabla y subsección "Ablación de arquitectura".
- `20_conclusiones.tex`: añadir hallazgo sobre si la arquitectura base es o no necesaria.

### Acción A3-b — Justificación en metodología (independiente de A3-a)

En `17_metodologia.tex`, sección "Arquitectura de la red", añadir párrafo de justificación
antes de la tabla de estructura. Puntos a cubrir:
1. Referencia a Raissi et al. (2019) que usa arquitecturas más pequeñas (9 capas × 20
   neuronas) en problemas con pocos datos.
2. Indicar que la arquitectura de 11 capas × 1.200 neuronas se eligió considerando la
   complejidad espacio-temporal del campo meteorológico y la escala del dominio estudiado.
3. Señalar que la evaluación de arquitecturas reducidas se propone como trabajo futuro
   (o referir a los resultados de A3-a si se ejecutan).
4. Citar al menos un trabajo de PINNs en meteorología con arquitecturas de tamaño comparable.

**Prioridad:** ALTA — la edición de texto (A3-b) puede hacerse de inmediato; los experimentos
(A3-a) son deseables antes de la defensa pero requieren tiempo computacional.

---

## Observación 4 — Barrido incompleto en R = 0,2° (MODERADO)

**Problema identificado:** el bloque R = 0,2° permanece sin evaluar para varios valores de λ.
No se puede afirmar que R = 0,1° es óptimo sin datos de R = 0,2°.

### Acción A4 — Completar bloque R = 0,2°

Ejecutar las corridas faltantes con λ ∈ {1, 3, 5, 7, 10} y R = 0,2° con la configuración
estándar del barrido (300 registros/estación, 2.000 épocas). Las corridas ya completadas
de R = 0,2° deben identificarse primero en `docs/metricas/` para evitar repetición.

> **Verificar antes de ejecutar:** listar los manifiestos existentes en `docs/metricas/`
> para confirmar qué valores de λ con R = 0,2° ya están completados.

**Archivos a modificar tras el experimento:**
- `18_resultados.tex`: incorporar el bloque R = 0,2° en la Tabla maestra (Tabla 5.1) y
  añadir subsección de análisis del bloque R = 0,2°.
- `20_conclusiones.tex`: actualizar §Efecto de la resolución de malla con los nuevos
  resultados y revisar si el ranking de R óptima cambia.

**Prioridad:** MODERADA — importante para la solidez del argumento de optimalidad de
R = 0,1°, pero no invalida los resultados actuales si se declara como limitación.

---

## Observación 5 — Métricas faltantes para restricción de rango (MODERADO)

**Problema identificado:** para `todosreg_R01_L5_rangeL05` solo se reporta la pérdida total
(0,638 vs 0,926). No se informa cómo cambiaron MSE_obs (MSE_ux, MSE_uy, MSE_p), R_NS ni
las métricas FOR.

### Acción A5 — Extraer y reportar métricas completas del experimento de rango

Verificar si el experimento `todosreg_R01_L5_rangeL05` generó un manifiesto en
`docs/metricas/` o en `docs/rango/`. Si los datos existen, extraer:
- MSE_ux, MSE_uy, MSE_p (componentes observacionales)
- R_NS (residual físico sobre puntos de colocación)
- FOR final por variable (si fue registrado)
- Pérdida total (ya reportada: 0,638)

Si los datos no están disponibles en los archivos de métricas, deberá ejecutarse una
evaluación adicional del modelo guardado (checkpoint de época 2.000).

**Archivos a modificar:**
- `18_resultados.tex`: ampliar la Tabla de comparación de rango (`tab:range_loss_comp`)
  con las columnas MSE_ux, MSE_uy, MSE_p y R_NS, y actualizar el análisis cualitativo
  para concluir si la mejora en pérdida total se traduce en mejora observacional y física.
- `20_conclusiones.tex`: actualizar §Aporte de la restricción suave de rango con el
  veredicto basado en las métricas completas.

**Nota importante:** si MSE_obs es similar o peor que `todos_R01_L5` a pesar de la
reducción en pérdida total, declararlo explícitamente como hallazgo: la restricción de
rango mejora la pérdida compuesta pero no necesariamente el ajuste observacional.

**Prioridad:** MODERADA — los datos probablemente ya existen; es principalmente una extracción
y edición de texto.

---

## Observación 6 — Justificación del caso Caribe colombiano (MODERADO)

**Problema identificado:** el documento no menciona problemas climáticos específicos del Caribe
colombiano, no cita literatura local o informes del IDEAM, y no justifica explícitamente por
qué la cobertura de estaciones es insuficiente.

### Acción A6-a — Añadir contexto climático en Planteamiento o Antecedentes

En `14_planteamiento_problema.tex`, ampliar la sección "Justificación" con 1–2 párrafos
que cubran:
1. Problemas climáticos relevantes del Caribe colombiano: temporadas de lluvia, sequías,
   influencia del ENSO, vientos Alisios, fenómeno La Niña / El Niño.
2. Citar al menos: IDEAM (Boletines Climáticos regionales o Atlas Climatológico Colombia)
   y al menos un artículo académico sobre climatología del Caribe colombiano.
3. Señalar que la red de estaciones del IDEAM en el Caribe tiene cobertura insuficiente:
   el número de estaciones activas en los cinco departamentos es limitado en comparación
   con la extensión del dominio, lo que deja zonas costeras y marítimas sin cobertura.

### Acción A6-b — Referenciar el mapa de estaciones existente

La Figura `fig:mapa_estaciones` ya existe en `17_metodologia.tex` y muestra la distribución
geográfica. Verificar que en el caption o en la sección "Justificación" se haga referencia
explícita a la distribución espacial irregular como motivación del estudio.

En `14_planteamiento_problema.tex`, añadir una referencia cruzada a la Figura del mapa
de estaciones (si el flujo del documento lo permite) o describir textualmente la
distribución: "las estaciones disponibles se concentran principalmente en áreas urbanas
costeras, dejando sin cobertura amplias zonas del interior departamental y la franja marítima."

**Prioridad:** MODERADA — es una edición de texto y búsqueda bibliográfica, sin experimentos.

---

## Resumen de prioridades y tipo de acción

| Obs. | Descripción | Tipo | Prioridad | Estado |
|------|-------------|------|-----------|--------|
| 1 | Validación fuera de muestra | Experimento + texto | CRÍTICA | Pendiente |
| 2 | Justificar omisión de Coriolis + extensión del dominio | Cálculo + texto | ALTA | Pendiente |
| 3-b | Justificar arquitectura en metodología | Texto | ALTA | Pendiente |
| 3-a | Ablación de arquitecturas reducidas | Experimento | ALTA | Pendiente |
| 5 | Métricas completas para restricción de rango | Extracción datos + texto | MODERADA | Pendiente |
| 4 | Completar barrido R = 0,2° | Experimento | MODERADA | Pendiente |
| 6 | Justificación del Caribe colombiano | Texto + bibliografía | MODERADA | Pendiente |

---

## Orden de ejecución recomendado

1. **Inmediato (sin cómputo):**
   - A6-a y A6-b: añadir contexto climático y mapa en planteamiento.
   - A3-b: justificación de arquitectura en metodología.
   - A2-a: calcular extensión del dominio y número de Rossby con los datos de estaciones.
   - A2-b: añadir el párrafo de justificación de Coriolis basado en Ro.
   - A5: verificar si los datos del experimento de rango ya existen y actualizar tabla.
   - A1-b (contingencia): añadir declaración explícita de limitación in-sample si no hay
     tiempo para el experimento.

2. **Con disponibilidad computacional:**
   - A1-a: experimento de validación temporal (días 1–20 / 21–31).
   - A3-a: tres corridas de ablación arquitectural.
   - A4: completar bloque R = 0,2° con λ ∈ {1, 3, 5, 7, 10}.

---

*Documento creado el 04/05/2026. Actualizar estado de cada ítem conforme se completen
las acciones.*
