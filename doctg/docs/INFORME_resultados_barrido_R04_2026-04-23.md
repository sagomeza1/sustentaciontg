# INFORME — Resultados del ultimo barrido R=0.4

**Fecha:** 23 de abril de 2026

## Alcance

Este informe consolida el barrido del bloque `R = 0.4` con las corridas evaluadas a epoca 2000.

Las corridas comparadas son:

- `PINN_caribe_excl4_300xest_R04_L1`
- `PINN_caribe_excl4_300xest_R04_L3`
- `PINN_caribe_excl4_300xest_R04_L5`
- `PINN_caribe_excl4_300xest_R04_L10`

## Base de comparacion

Las cuatro corridas son comparables entre si porque comparten la misma configuracion estructural del experimento:

- `R = 0.4`
- `18` estaciones utilizadas
- `300` registros por estacion declarados en manifiesto
- `5400` registros totales declarados en manifiesto
- `44 x 300 = 13200` puntos de colocacion
- mismas estaciones excluidas
- mismo horizonte temporal y misma malla fisica del bloque `R = 0.4`

## Fuente de los valores

- La **loss total final** y sus componentes **NS**, **U**, **V** y **P** se toman del ultimo valor almacenado en cada historial `historial_*.mat`.
- Las metricas **MSE u**, **MSE v**, **MSE p** y **residual NS** se toman de los JSON exportados por `evaluar.py` tras la evaluacion reproducible de cada modelo.

## Tabla 1. Perdidas finales de entrenamiento por experimento

| Experimento | Lambda fisica | Loss total final | Residual NS final | Loss U final | Loss V final | Loss P final |
|---|---:|---:|---:|---:|---:|---:|
| PINN_caribe_excl4_300xest_R04_L1 | 1.0 | 1.0575 | 0.5651 | 0.5060 | 0.5084 | 1.5716 |
| PINN_caribe_excl4_300xest_R04_L3 | 3.0 | 1.2357 | 0.6346 | 0.6139 | 0.6278 | 1.8290 |
| PINN_caribe_excl4_300xest_R04_L5 | 5.0 | 1.6203 | 0.8059 | 0.7712 | 0.7828 | 2.4090 |
| PINN_caribe_excl4_300xest_R04_L10 | 10.0 | 1.0859 | 0.5386 | 0.5222 | 0.5222 | 1.6115 |

## Tabla 2. Metricas finales de evaluacion reproducible

| Experimento | Lambda fisica | MSE u_x | MSE u_y | MSE presion | Residual NS |
|---|---:|---:|---:|---:|---:|
| PINN_caribe_excl4_300xest_R04_L1 | 1.0 | 0.5059 | 0.5085 | 1.5720 | 0.2414 |
| PINN_caribe_excl4_300xest_R04_L3 | 3.0 | 0.6150 | 0.6274 | 1.8239 | 0.0597 |
| PINN_caribe_excl4_300xest_R04_L5 | 5.0 | 0.7710 | 0.7817 | 2.4111 | 0.0707 |
| PINN_caribe_excl4_300xest_R04_L10 | 10.0 | 0.5220 | 0.5224 | 1.6185 | 0.0224 |

## Tabla 3. Ranking resumido del bloque R=0.4

| Criterio | Mejor corrida | Valor | Peor corrida | Valor |
|---|---|---:|---|---:|
| Loss total de entrenamiento | PINN_caribe_excl4_300xest_R04_L1 | 1.0575 | PINN_caribe_excl4_300xest_R04_L5 | 1.6203 |
| Residual NS de entrenamiento | PINN_caribe_excl4_300xest_R04_L10 | 0.5386 | PINN_caribe_excl4_300xest_R04_L5 | 0.8059 |
| MSE u_x en evaluacion | PINN_caribe_excl4_300xest_R04_L1 | 0.5059 | PINN_caribe_excl4_300xest_R04_L5 | 0.7710 |
| MSE u_y en evaluacion | PINN_caribe_excl4_300xest_R04_L1 | 0.5085 | PINN_caribe_excl4_300xest_R04_L5 | 0.7817 |
| MSE presion en evaluacion | PINN_caribe_excl4_300xest_R04_L1 | 1.5720 | PINN_caribe_excl4_300xest_R04_L5 | 2.4111 |
| Residual NS en evaluacion | PINN_caribe_excl4_300xest_R04_L10 | 0.0224 | PINN_caribe_excl4_300xest_R04_L1 | 0.2414 |

## Tabla 4. Artefactos generados para el analisis

| Tipo de artefacto | Carpeta |
|---|---|
| Comparacion de perdidas por `lambda` | `reports/iter_R04_comparacion_lambdas_20260423/` |
| Evaluacion reproducible de `R04_L1` | `reports/iter_R04_analisis_20260423/PINN_caribe_excl4_300xest_R04_L1/` |
| Evaluacion reproducible de `R04_L3` | `reports/iter_R04_analisis_20260423/PINN_caribe_excl4_300xest_R04_L3/` |
| Evaluacion reproducible de `R04_L5` | `reports/iter_R04_analisis_20260423/PINN_caribe_excl4_300xest_R04_L5/` |
| Evaluacion reproducible de `R04_L10` | `reports/iter_R04_analisis_20260423/PINN_caribe_excl4_300xest_R04_L10/` |

Cada carpeta de evaluacion contiene las graficas temporales de error, las graficas MSE por estacion y los GIFs de `u`, `v` y `p` del modelo correspondiente.

## Hallazgos principales

1. En terminos de **loss total de entrenamiento**, la mejor corrida del bloque `R = 0.4` fue `lambda = 1`, con `1.0575`, seguida muy de cerca por `lambda = 10`, con `1.0859`.
2. La corrida `lambda = 3` queda en posicion intermedia: mejora ampliamente el residual NS frente a `lambda = 1` (`0.2414 -> 0.0597`), pero con degradacion observacional en `u_x`, `u_y` y `p`.
3. La corrida `lambda = 5` quedo claramente por debajo del resto tanto en entrenamiento como en evaluacion: presenta la mayor loss total y los peores MSE en `u_x`, `u_y` y `p`.
4. En la **evaluacion reproducible**, `lambda = 1` mantiene el mejor ajuste observacional global, porque obtiene el menor `MSE u_x`, `MSE u_y` y `MSE presion`.
5. Si se prioriza el **cumplimiento fisico fuera del entrenamiento**, `lambda = 10` ofrece la mejor evidencia, con el menor `residual_ns` de evaluacion (`0.0224`).
6. El bloque `R = 0.4` no deja un ganador unico absoluto: `lambda = 1` domina las metricas observacionales, mientras `lambda = 10` domina el residual de Navier-Stokes en evaluacion; `lambda = 3` se mantiene como referencia intermedia.

## Limitaciones y observaciones

1. Este informe resume el bloque `R = 0.4` con el conjunto completo de `lambda in {1, 3, 5, 10}` evaluado a epoca 2000.
2. Aunque los manifiestos declaran `5400` registros del subset, la evaluacion reproducible sigue reportando `5347` puntos efectivos de estaciones; esa diferencia continua abierta y debe auditarse antes de cerrar una conclusion definitiva del barrido completo.
3. Las metricas aqui consolidadas corresponden a la epoca final disponible (`2000`) para cada corrida evaluada.

## Lectura operativa

1. Si el criterio principal es minimizar el error sobre las variables observadas `u_x`, `u_y` y `p`, la corrida a priorizar del bloque `R = 0.4` es `PINN_caribe_excl4_300xest_R04_L1`.
2. Si el criterio principal es minimizar el residual fisico de Navier-Stokes en evaluacion, la corrida a priorizar es `PINN_caribe_excl4_300xest_R04_L10`.
3. La corrida `PINN_caribe_excl4_300xest_R04_L3` puede conservarse como referencia intermedia para sensibilidad de `lambda` en este bloque.
4. La corrida `PINN_caribe_excl4_300xest_R04_L5` no muestra una ventaja competitiva frente a las demas y puede considerarse descartable dentro de este bloque.