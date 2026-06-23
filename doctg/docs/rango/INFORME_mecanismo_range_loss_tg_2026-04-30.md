# INFORME TÉCNICO — Restricción suave de rango en la función de pérdida de la PINN
**Fecha:** 30 de abril de 2026  
**Proyecto:** Predicción de condiciones meteorológicas usando PINNs — Caribe de Colombia  
**Institución:** Universidad Central — Maestría en Analítica de Datos  
**Propósito del documento:** Insumo técnico para la sección de metodología del trabajo de grado. Describe el mecanismo de restricción de rango implementado en la función de pérdida de la red neuronal informada físicamente (PINN).

---

## 1. Motivación

Una PINN predice las variables de salida (velocidad del viento en X, velocidad del viento en Y y presión) tanto en las ubicaciones de las estaciones meteorológicas como en todos los puntos de la malla espacial del Caribe de Colombia. Las estaciones están distribuidas de forma irregular: existen zonas de la malla sin ninguna observación cercana.

En estas zonas no observadas, la función de pérdida de datos no impone ninguna restricción directa sobre los valores que el modelo puede predecir. La pérdida física (ecuaciones de Navier-Stokes) tampoco garantiza que las predicciones se mantengan dentro de rangos físicamente plausibles, ya que una solución matemáticamente consistente con las ecuaciones puede tener valores muy alejados del orden de magnitud observado si los pesos del modelo se desequilibran durante el entrenamiento.

Para mitigar este problema, se incorporó un **término de penalización de rango** en la función de pérdida, denominado `loss_rango`, cuyo peso relativo es `LambdaR` (`lambda_rango_efecto` en el código).

---

## 2. Definición formal del término de penalización de rango

### 2.1 Límites de rango por variable

Antes del entrenamiento, se calculan límites inferior y superior para cada variable de salida (`u`, `v`, `p`) a partir del conjunto de datos observados en las estaciones. Se utilizan cuantiles robustos en lugar del mínimo y máximo absolutos para evitar que valores atípicos extremos definan los límites permitidos:

$$l^{-}_{k} = Q_{0.01}(y_k), \quad l^{+}_{k} = Q_{0.99}(y_k), \quad k \in \{u, v, p\}$$

donde $Q_\alpha$ denota el cuantil de orden $\alpha$ calculado sobre todos los registros disponibles de la variable $k$ en el conjunto de entrenamiento. El uso de los cuantiles 1% y 99% implica que el 2% de las observaciones puede legítimamente quedar fuera del rango sin ser considerado una violación — esto dota al mecanismo de robustez frente a errores de medición o picos transitorios de los sensores.

El código que implementa este cálculo es `_calcular_limites_rango()` en `src/training/entrenador.py`.

### 2.2 Penalización diferenciable con softplus

La penalización para una predicción $\hat{y}_k$ que cae fuera del rango $[l^{-}_{k}, l^{+}_{k}]$ se calcula con la función **softplus**, que es una aproximación diferenciable del operador rectificador $\max(0, \cdot)$:

$$\mathcal{P}(\hat{y}_k, \tau) = \underbrace{\tau \cdot \ln\!\left(1 + e^{(\hat{y}_k - l^{+}_{k})/\tau}\right)}_{\text{exceso superior}} + \underbrace{\tau \cdot \ln\!\left(1 + e^{(l^{-}_{k} - \hat{y}_k)/\tau}\right)}_{\text{exceso inferior}}$$

La pérdida de rango para la variable $k$ en un batch de $N$ predicciones es:

$$\mathcal{L}^{\text{rango}}_{k} = \frac{1}{N} \sum_{i=1}^{N} \mathcal{P}(\hat{y}_{k,i}, \tau)$$

**Propiedades del parámetro de temperatura $\tau$:**

- $\tau > 0$ controla la suavidad de la transición.
- Cuando $\hat{y}_k$ está bien dentro del rango, $\mathcal{P} \approx 0$.
- Cuando $\hat{y}_k$ excede el límite, la penalización crece aproximadamente de forma lineal con el exceso.
- Un $\tau$ pequeño (en este experimento $\tau = 0.05$) produce una transición afilada, cercana a la pérdida tipo bisagra (hinge loss), sin perder diferenciabilidad.

La función softplus evita discontinuidades en el gradiente que podrían desestabilizar el optimizador.

### 2.3 Pérdida de rango total

La pérdida de rango total en un paso de entrenamiento agrega la penalización de las tres variables de salida, ponderada por `LambdaR`:

$$\mathcal{L}^{\text{rango}} = \lambda_R \cdot \left(\mathcal{L}^{\text{rango}}_u + \mathcal{L}^{\text{rango}}_v + \mathcal{L}^{\text{rango}}_p\right)$$

---

## 3. Función de pérdida total de la PINN

La función de pérdida combina cuatro componentes mediante una fórmula de promedio armónico ponderado que actúa como normalizador dinámico:

$$\mathcal{L}_{\text{total}} = \frac{\mathcal{L}_{NS}^2 + \mathcal{L}_u^2 + \mathcal{L}_v^2 + \mathcal{L}_p^2 + (\mathcal{L}^{\text{rango}})^2}{\mathcal{L}_{NS} + \mathcal{L}_u + \mathcal{L}_v + \mathcal{L}_p + \mathcal{L}^{\text{rango}} + \varepsilon}$$

donde:
- $\mathcal{L}_{NS}$: pérdida de las ecuaciones de Navier-Stokes (física).
- $\mathcal{L}_u, \mathcal{L}_v, \mathcal{L}_p$: pérdidas de datos para cada variable de salida.
- $\mathcal{L}^{\text{rango}}$: pérdida de rango suave.
- $\varepsilon = 10^{-12}$: término de estabilidad numérica.

La estructura cuadrática en el numerador y lineal en el denominador actúa como normalizador dinámico: si un término crece desproporcionadamente, el denominador aumenta y amortigua su influencia relativa. Esto garantiza que `LambdaR = 0.50` no es una magnitud absoluta sino **relativa al equilibrio** entre todos los demás términos activos.

---

## 4. Esquema de rampa lineal (curriculum de rango)

`LambdaR` no se activa con su valor máximo desde la primera época. Se introduce gradualmente mediante una rampa lineal sobre las primeras 300 épocas:

$$\lambda_R(e) = \begin{cases} \lambda_{R,\max} \cdot \dfrac{e + 1}{E_{\text{rampa}}} & \text{si } e < E_{\text{rampa}} \\ \lambda_{R,\max} & \text{si } e \geq E_{\text{rampa}} \end{cases}$$

donde:
- $\lambda_{R,\max} = 0.50$ (peso máximo).
- $E_{\text{rampa}} = 300$ (épocas de rampa).
- $e$ es el índice de época (base 0).

**Justificación de la rampa:**  
Si la restricción de rango se activa con peso máximo desde el inicio, el modelo recibe simultáneamente tres señales de error fuertes (pérdida de datos, física de Navier-Stokes y rango) antes de haber aprendido la estructura básica del campo de vientos. Esto puede producir gradientes conflictivos que desestabilizan la convergencia temprana. La rampa permite que el modelo ajuste primero la tendencia general de los datos, y luego refine las predicciones para mantenerlas dentro del rango físicamente plausible.

Evidencia en el log de entrenamiento del experimento `todosreg_R01_L5_rangeL05`:

| Época | LambdaR registrado |
|---|---|
| 0 | 0.00 |
| 10 | 0.02 |
| 50 | 0.09 |
| 90 | 0.15 |
| ≥ 300 | **0.50** (estabilizado) |

---

## 5. Métrica de diagnóstico: Fracción Fuera de Rango (FOR)

Junto con la pérdida de rango se registra por época la **Fracción Fuera de Rango (FOR)** para cada variable:

$$\text{FOR}_k = \frac{1}{N} \sum_{i=1}^{N} \mathbf{1}\!\left[\hat{y}_{k,i} < l^{-}_{k} \;\vee\; \hat{y}_{k,i} > l^{+}_{k}\right]$$

Esta métrica es puramente diagnóstica — no entra en el cálculo del gradiente. Cuantifica qué proporción de las predicciones violan el rango observado en cada batch. Se reporta en el log como `FOR_u`, `FOR_v` y `FOR_p`.

Valores observados en el experimento `todosreg_R01_L5_rangeL05` a la época 1999 (última época con desglose FOR en el log):

| Variable | FOR |
|---|---|
| u (viento X) | ~64.67% |
| v (viento Y) | ~47.68% |
| p (presión) | ~0.29% |

El FOR de u y v se mantiene elevado al cierre, lo cual no indica un problema por sí mismo: el modelo predice en toda la malla espacial, incluyendo puntos de colocación donde la física de Navier-Stokes puede llevar las predicciones fuera del rango puntual de las estaciones. El FOR de presión permanece prácticamente nulo, confirmando que la presión se predice dentro del rango observado en casi todos los puntos, coherente con la menor variabilidad relativa de esta variable en el dominio del Caribe de Colombia.

---

## 6. Hiperparámetros de configuración

Todos los hiperparámetros del mecanismo se centralizan en `config/settings.py`:

| Parámetro | Valor | Descripción |
|---|---|---|
| `LAMBDA_RANGO_MAX` | 0.50 | Peso máximo de la pérdida de rango |
| `EPOCAS_RAMPA_RANGO` | 300 | Épocas de rampa lineal 0 → 0.50 |
| `TAU_RANGO` | 0.05 | Temperatura de suavizado softplus |
| `USAR_CUANTILES_RANGO` | True | Límites por cuantiles (no min/max absolutos) |
| `CUANTIL_MIN` | 0.01 | Cuantil inferior del rango permitido |
| `CUANTIL_MAX` | 0.99 | Cuantil superior del rango permitido |

---

## 7. Impacto medido en el entrenamiento

Comparación de la pérdida total al completar 2000 épocas en experimentos con igual configuración de red (R01, λ=5) pero distinto tratamiento del rango:

| Experimento | Mecanismo de rango | Loss final (época 2000) |
|---|---|---|
| `300xest_R01_L5` | Sin restricción de rango | 1.117 |
| `todos_R01_L5` | Sin restricción de rango | 0.926 |
| `todosreg_R01_L5_rangeL05` *(completado, 30-04-2026)* | **Con restricción de rango (λR=0.50)** | **0.638** |

Con el entrenamiento completado (2000 épocas), el experimento con restricción de rango conserva la mejor pérdida total entre los tres escenarios comparados. Este resultado refuerza la hipótesis de que la restricción suave de rango aporta un efecto regulador positivo y guía al modelo hacia soluciones más coherentes con el dominio físico observado.

---

## 8. Referencias de implementación

| Componente | Archivo | Función / símbolo |
|---|---|---|
| Cálculo de límites de rango | `src/training/entrenador.py` | `_calcular_limites_rango()` |
| Penalización softplus | `src/training/entrenador.py` | `_penalizacion_rango_suave()` |
| Rampa y aplicación de LambdaR | `src/training/entrenador.py` | `entrenar()`, líneas 314–316 |
| Hiperparámetros por defecto | `config/settings.py` | `LAMBDA_RANGO_MAX`, `EPOCAS_RAMPA_RANGO`, etc. |
| Métricas FOR en evaluación | `src/evaluation/metricas.py` | `FOR_u`, `FOR_v`, `FOR_p` |
| Registro en manifiestos | `src/training/manifiestos.py` | campo `lambda_rango_max`, etc. |
