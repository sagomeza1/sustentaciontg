# Fase 3: Control de Tiempo y Consistencia

## Análisis temporal por sección

| # | Sección | Frames | Tiempo (min) | Notas |
|---|---------|--------|--------------|-------|
| 1 | Portada + TOC | 2 | 1,5 | Introductorio |
| 2 | Introducción | 1 | 2,5 | Contexto histórico y motivación |
| 3 | Planteamiento | 1 | 2,5 | Visualizar problema del Caribe |
| 4 | Objetivos | 1 | 2 | Lectura rápida, 1 general + 3 específicos |
| 5 | Antecedentes | 1 | 3 | Comparación de 4 métodos (pausa) |
| 6 | Marco Teórico (N-S) | 1 | 2,5 | Presentar ecuaciones + contexto |
| 7 | Marco Teórico (PINNs) | 1 | 2 | Concepto y ventajas clave |
| 8 | Metodología (Datos) | 1 | 2,5 | Describir dominio y variables |
| 9 | Metodología (Config) | 1 | 2 | Parámetros de entrenamiento |
| 10 | Resultados | 1 | 3,5 | Tabla + interpretación |
| 11 | Discusión | 1 | 3 | Logros vs. alternativas |
| 12 | Conclusiones | 1 | 2 | Contribuciones principales |
| 13 | Cierre | 1 | 0,5 | Diapositiva "Gracias" |
| | **TOTAL** | **14** | **~30** | ✓ Dentro del límite |

## Conteo de diapositivas

- **Actuales:** 14 diapositivas
- **Rango objetivo:** 18-24 diapositivas  
- **Estado:** BAJO (necesita 4-10 frames adicionales)

### Opciones de ampliación

1. **Dividir Antecedentes en dos frames** (2 min c/u):
   - Frame 5a: Métodos tradicionales (NWP + estadísticos)
   - Frame 5b: Métodos modernos (ML convencional + PINNs)
   - **Impacto:** +1 frame, +2 min

2. **Agregar frame de Formulación Matemática de PINN** (entre Marco Teórico y Metodología):
   - Mostrar función de pérdida híbrida: $\mathcal{L} = \mathcal{L}_{data} + \lambda \mathcal{L}_{physics}$
   - **Impacto:** +1 frame, +2 min

3. **Dividir Resultados en dos frames** (1.5-2 min c/u):
   - Frame 10a: Tabla de desempeño
   - Frame 10b: Análisis de convergencia / ranking
   - **Impacto:** +1 frame, +2 min

4. **Agregar frame de Limitaciones y Consideraciones**:
   - Antes de Conclusiones
   - **Impacto:** +1 frame, +2 min

5. **Expandir Conclusiones**:
   - Frame separada para "Trabajo Futuro"
   - **Impacto:** +1 frame, +1.5 min

## Propuesta de ampliación balanceada

Implementaré opciones 1, 2, 3 y 5 para llegar a **18 diapositivas** (dentro del rango):

### Nueva estructura (18 frames)

1. Portada + TOC: 2
2. Introducción: 1
3. Planteamiento: 1
4. Objetivos: 1
5. Antecedentes (dividido): 2 ← **NEW**
6. Marco Teórico (N-S): 1
7. Marco Teórico (PINNs): 1
8. **Formulación matemática de PINN: 1** ← **NEW**
9. Metodología (Datos): 1
10. Metodología (Config): 1
11. Resultados (Tabla): 1
12. **Resultados (Análisis): 1** ← **NEW**
13. Discusión: 1
14. Conclusiones: 1
15. **Trabajo Futuro: 1** ← **NEW**
16. Cierre: 1

**Subtotal contenido:** 16  
**Más Portada/TOC:** 2  
**TOTAL:** 18 frames

## Tiempo estimado (ajustado)

- Portada/TOC: 1,5 min
- Introducción: 2,5 min
- Planteamiento: 2,5 min
- Objetivos: 2 min
- Antecedentes (2 frames): 4 min
- Marco Teórico (3 frames): 6 min
- Formulación PINN: 2 min
- Metodología (2 frames): 4,5 min
- Resultados (2 frames): 4 min
- Discusión: 3 min
- Conclusiones: 2 min
- Trabajo Futuro: 1,5 min
- Cierre: 0,5 min

**TIEMPO TOTAL ESTIMADO: 36 min** (6 min por encima del objetivo)

### Ajuste necesario

Reducir ciertos bloques:
- Antecedentes: 3 min (reducir de 4)
- Marco Teórico: 5 min (reducir de 6)
- Resultados: 3,5 min (reducir de 4)
- Discusión: 2,5 min (reducir de 3)

**NUEVO TOTAL: ~31 min** (aceptable, margen de 1 min)

## Decisiones

- ✓ Expandir a 18 diapositivas (centro del rango)
- ✓ Agregar frame de formulación PINN
- ✓ Dividir Antecedentes y Resultados  
- ✓ Mover "Trabajo Futuro" a frame separada
- ✓ Mantener tiempo dentro de ~30-31 minutos

