# INFORME — Resultados finales del barrido R=0.1 — Iteracion 1

**Fecha:** 21 de abril de 2026

## Alcance

Este informe consolida los resultados finales de las funciones de perdida observadas en la iteracion completa del barrido con `R = 0.1` y `lambda in {1, 3, 5, 10}`.

Las corridas comparadas son:

- `PINN_caribe_excl4_300xest_R01_L1`
- `PINN_caribe_excl4_300xest_R01_L3`
- `PINN_caribe_excl4_300xest_R01_L5`
- `PINN_caribe_excl4_300xest_R01_L10`

## Base de comparacion

Las cuatro corridas son comparables entre si porque comparten la misma configuracion estructural del experimento:

- `R = 0.1`
- `18` estaciones utilizadas
- `300` registros por estacion en el subset declarado por manifiesto
- `5400` registros totales declarados por manifiesto
- `304 x 300 = 91200` puntos de colocacion
- mismas estaciones excluidas
- mismo horizonte temporal y misma malla fisica

## Fuente de los valores finales

- La **loss total final** se toma de la linea `Epoca 2000 con loss ...` de cada log.
- Las componentes **NS**, **U**, **V** y **P** se toman de la ultima descomposicion detallada registrada en la epoca `1999`.

## Tabla 1. Perdidas finales por experimento

| Experimento | Lambda fisica | Loss total final | Residual NS final | Loss U final | Loss V final | Loss P final |
|---|---:|---:|---:|---:|---:|---:|
| PINN_caribe_excl4_300xest_R01_L1 | 1.0 | 1.14 | 0.5275 | 0.4805 | 0.4754 | 1.6480 |
| PINN_caribe_excl4_300xest_R01_L3 | 3.0 | 1.63 | 0.7588 | 0.6921 | 0.6846 | 2.3720 |
| PINN_caribe_excl4_300xest_R01_L5 | 5.0 | 1.12 | 0.5043 | 0.4688 | 0.4646 | 1.6220 |
| PINN_caribe_excl4_300xest_R01_L10 | 10.0 | 1.16 | 0.5206 | 0.4920 | 0.4966 | 1.6910 |

## Tabla 2. Ranking por componente de perdida

| Componente | Mejor corrida | Valor | Peor corrida | Valor |
|---|---|---:|---|---:|
| Loss total | PINN_caribe_excl4_300xest_R01_L5 | 1.12 | PINN_caribe_excl4_300xest_R01_L3 | 1.63 |
| Residual NS | PINN_caribe_excl4_300xest_R01_L5 | 0.5043 | PINN_caribe_excl4_300xest_R01_L3 | 0.7588 |
| Loss U | PINN_caribe_excl4_300xest_R01_L5 | 0.4688 | PINN_caribe_excl4_300xest_R01_L3 | 0.6921 |
| Loss V | PINN_caribe_excl4_300xest_R01_L5 | 0.4646 | PINN_caribe_excl4_300xest_R01_L3 | 0.6846 |
| Loss P | PINN_caribe_excl4_300xest_R01_L5 | 1.6220 | PINN_caribe_excl4_300xest_R01_L3 | 2.3720 |

## Tabla 3. Lectura resumida por lambda

| Lambda fisica | Lectura tecnica resumida |
|---:|---|
| 1.0 | Buen resultado general. Queda cerca del mejor caso, con perdidas observacionales y residual fisico bajos. |
| 3.0 | Resultado claramente inferior. La corrida entra en una meseta alta y termina con los peores valores en todas las componentes reportadas. |
| 5.0 | Mejor resultado del bloque R=0.1. Presenta la menor loss total y tambien los menores valores en NS, U, V y P. |
| 10.0 | Resultado competitivo, pero no supera a `lambda = 5`. El residual fisico y las perdidas observacionales quedan ligeramente por encima del mejor caso. |

## Hallazgos principales

1. Dentro de la iteracion `R = 0.1`, la mejor combinacion observada en entrenamiento fue `lambda = 5`.
2. La corrida `lambda = 3` quedo sustancialmente por debajo del resto del bloque y no muestra un compromiso favorable entre ajuste observacional y residual fisico.
3. `lambda = 1` y `lambda = 10` quedan en una zona intermedia, con desempeno cercano pero inferior a `lambda = 5`.
4. Con la evidencia disponible, el mejor equilibrio entre las variables de salida `u_x`, `u_y`, `p` y la restriccion fisica de Navier-Stokes aparece en `lambda = 5`.

## Limitaciones del informe

1. Este informe resume **perdidas finales de entrenamiento**, no metricas de evaluacion reproducible sobre artefactos de `evaluar.py`.
2. Para estas cuatro corridas no se encontraron archivos de metricas finales exportadas en `models/metricas_*.json`.
3. Los manifiestos declaran `5400` registros del subset, pero el entrenamiento reporta `5347` registros de estaciones usados efectivamente. Esa diferencia debe revisarse antes de cerrar conclusiones definitivas del barrido.

## Recomendacion operativa

1. Priorizar la evaluacion reproducible de `PINN_caribe_excl4_300xest_R01_L5`.
2. Evaluar despues `PINN_caribe_excl4_300xest_R01_L1` y `PINN_caribe_excl4_300xest_R01_L10` para confirmar si el orden observado en entrenamiento se mantiene fuera del entrenamiento.
3. Auditar la diferencia entre `5400` registros declarados en el manifiesto y `5347` registros usados en entrenamiento.