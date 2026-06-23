# Checkpoint: Fase 1 - Plan estructural inicial

## Análisis de Impacto

### Mapeo de capítulos a diapositivas

| Capítulo en `doctg` | Función en la presentación | Bloque de diapositivas |
| --- | --- | --- |
| `13_introduccion.tex` | Contexto, motivación y transición al problema | 1-2 |
| `14_planteamiento_problema.tex` | Problema, pregunta de investigación y justificación | 3-4 |
| `15_objetivos.tex` | Objetivo general y objetivos específicos | 5 |
| `16_antecedentes.tex` | Línea base científica y antecedentes de PINNs / NWP / ML | 6-7 |
| `16_02_marco_teorico.tex` | Fundamento físico y encaje del caso de estudio | 8-10 |
| `17_metodologia.tex` | Núcleo técnico: datos, preprocesamiento, modelo, física y pérdida | 11-17 |
| `18_resultados.tex` | Evidencia experimental y análisis comparativo | 18-21 |
| `19_discusion.tex` | Interpretación, trade-offs y lectura crítica | 22 |
| `20_conclusiones.tex` | Cierre sintético y proyección | 23 |

### Lectura de impacto
- La **metodología** concentra el mayor peso porque ahí vive el aporte técnico de la tesis.
- Los **resultados** deben ir inmediatamente después para sostener la narrativa experimental.
- La **discusión** y las **conclusiones** cierran el argumento sin competir con el bloque técnico central.
- El conjunto completo debe mantenerse dentro del rango objetivo de la defensa oral, sin saturar texto.

## Traza de Progreso

- [ ] Construir diapositivas 1-2: apertura, contexto y transición al problema.
  - Ecuaciones/expresiones a extraer: ninguna todavía.
- [ ] Construir diapositivas 3-5: planteamiento del problema, pregunta de investigación y objetivos.
  - Ecuaciones/expresiones a extraer: formulación textual de la pregunta y variables $(x, y, t)$, $(vel_x, vel_y)$, $p$.
- [ ] Construir diapositivas 6-7: antecedentes y marco teórico mínimo.
  - Ecuaciones a extraer: Navier-Stokes incompresible en su forma vectorial y la ecuación de continuidad.
- [ ] Construir diapositivas 8-10: datos, dominio físico y preprocesamiento.
  - Ecuaciones a extraer: proyección cartesiana $(x_i, y_i)$, corrección ISA de presión y adimensionalización.
- [ ] Construir diapositivas 11-13: arquitectura PINN, residual físico y función de pérdida.
  - Ecuaciones a extraer: transformación de `GammaBiasLayer`, continuidad, momentos en $x$ e $y$, número de Rossby, pérdida total y pérdida de rango.
- [ ] Dejar definidos los puentes visuales hacia resultados y discusión para la siguiente sesión.
  - Ecuaciones a extraer: promedio de MSE observacional y comparación entre configuraciones $(R, \lambda)$.

## Ecuaciones clave identificadas para transcripción literal
- $ \rho \left( \frac{\partial \mathbf{u}}{\partial t} + \mathbf{u} \cdot \nabla \mathbf{u} \right) = -\nabla p + \mu \nabla^2 \mathbf{u} + \mathbf{f} $
- $ \nabla \cdot \mathbf{u} = 0 $
- $ x_i = R_T \cdot \sin\left(\frac{\pi}{180}\,\Delta\lambda_i\right), \qquad y_i = R_T \cdot \sin\left(\frac{\pi}{180}\,\Delta\varphi_i\right) $
- $ P_{\text{corr}} = P \cdot \left(1 - \frac{0{,}0065 \cdot Z}{T + 273{,}15 + 0{,}0065 \cdot Z}\right)^{-5{,}257} $
- $ x_{\text{norm}} = \frac{x}{L}, \ y_{\text{norm}} = \frac{y}{L}, \ t_{\text{norm}} = \frac{t}{T} $
- $ u_{\text{norm}} = \frac{u_x}{W}, \ v_{\text{norm}} = \frac{u_y}{W}, \ p_{\text{norm}} = \frac{p}{\rho W^2} $
- $ y = \gamma \cdot (\hat{W}x) + \beta, \qquad \hat{W} = \frac{W}{\|W\|} $
- $ \frac{\partial u_x}{\partial x} + \frac{\partial u_y}{\partial y} = 0 $
- $ \frac{\partial u_x}{\partial t} + u_x \frac{\partial u_x}{\partial x} + u_y \frac{\partial u_x}{\partial y} + \frac{\partial p}{\partial x} = 0 $
- $ \frac{\partial u_y}{\partial t} + u_x \frac{\partial u_y}{\partial x} + u_y \frac{\partial u_y}{\partial y} + \frac{\partial p}{\partial y} = 0 $
- $ Ro = \frac{W}{f \cdot L} $
- $ \mathcal{L}_{\text{total}} = \lambda \cdot \mathcal{L}_{\text{NS}}^2 + \mathcal{L}_{u_x}^2 + \mathcal{L}_{u_y}^2 + \mathcal{L}_{p}^2 $
- $ \mathcal{L}_{u_x} = \frac{1}{N}\sum_{i=1}^N \left(\hat{u}_{x,i} - u_{x,i}^{\text{obs}}\right)^2 $
- $ \mathcal{L}_{\text{NS}} = \text{MSE}(e_{\text{cont}}, 0) + \text{MSE}(e_{\text{mom}_x}, 0) + \text{MSE}(e_{\text{mom}_y}, 0) $
- $ \bar{MSE}_{obs}=\frac{\text{MSE}_{u_x}+\text{MSE}_{u_y}+\text{MSE}_{p}}{3} $
- $ \mathcal{P}(\hat{y}_k, \tau) = \tau \ln\!\left(1 + e^{(\hat{y}_k-l_k^+)/\tau}\right) + \tau \ln\!\left(1 + e^{(l_k^- - \hat{y}_k)/\tau}\right) $
- $ \mathcal{L}^{\text{rango}} = \lambda_R \left(\mathcal{L}^{\text{rango}}_{u_x} + \mathcal{L}^{\text{rango}}_{u_y} + \mathcal{L}^{\text{rango}}_{p}\right) $
- $ \mathcal{L}_{\text{total}}^{\text{ext}} =
  \frac{\mathcal{L}_{NS}^2 + \mathcal{L}_{u_x}^2 + \mathcal{L}_{u_y}^2 + \mathcal{L}_{p}^2 + \left(\mathcal{L}^{\text{rango}}\right)^2}
  {\mathcal{L}_{NS} + \mathcal{L}_{u_x} + \mathcal{L}_{u_y} + \mathcal{L}_{p} + \mathcal{L}^{\text{rango}} + \varepsilon} $

## Observaciones
- El bloque de resultados debe reservar la evidencia comparativa de $R \in \{0{,}1^{\circ}, 0{,}4^{\circ}\}$ y $\lambda \in \{1, 3, 5, 10\}$.
- La versión extendida con restricción suave de rango debe quedar señalada como extensión metodológica, no como eje principal de la defensa.
