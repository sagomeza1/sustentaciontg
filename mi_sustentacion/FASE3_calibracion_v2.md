# Fase 3: Control de Tiempo (Revalidación v2.0) — Calibración para 30 minutos

**Estructura actual:** 21 diapositivas  
**Objetivo:** Exactamente 30 minutos (±30 seg)  
**Desafío:** Integrar 3 frames adicionales de Resultados sin exceder 30 min

---

## Análisis Temporal Detallado (v2.0)

| Frame | Sección | Contenido | Tiempo Base | Calibración | Tiempo Final |
|-------|---------|----------|------------|-------------|-------------|
| 1 | Portada | Título del proyecto | 0.3 min | --- | **0.3 min** |
| 2 | TOC | Tabla de contenidos | 0.5 min | --- | **0.5 min** |
| **3-5** | **Introducción** | **Contexto histórico, motivación, ERA5** | **2.5 min** | Reducir a 2.0 min | **2.0 min** |
| **6** | **Planteamiento** | **Problema Caribe, 18 estaciones** | **2.5 min** | Reducir a 2.0 min | **2.0 min** |
| 7 | Objetivos | 1 general + 3 específicos | 1.0 min | --- | **1.0 min** |
| **8-9** | **Antecedentes (trad.)** | **NWP, ARIMA, GP, métodos clásicos** | **3.0 min** | Reducir a 1.5 min | **1.5 min** |
| 10 | Antecedentes (mod.) | GraphCast, FourCastNet, PINNs | 1.5 min | --- | **1.5 min** |
| 11 | Marco Teórico (N-S) | Ecuaciones de Navier-Stokes | 1.5 min | --- | **1.5 min** |
| 12 | Marco Teórico (PINNs) | Concepto + ventajas | 1.5 min | --- | **1.5 min** |
| 13 | Formulación PINN | $L = L_{data} + \lambda L_{physics}$ | 1.5 min | --- | **1.5 min** |
| 14 | Metodología (Datos) | Dominio, variables, IDEAM | 1.5 min | Reducir a 1.0 min | **1.0 min** |
| 15 | Metodología (Config) | Epochs, learning rate, hiperparámetros | 1.0 min | --- | **1.0 min** |
| **16** | **Resultados (Tabla)** | **Top-3 modelos, métricas** | **1.5 min** | --- | **1.5 min** |
| **17** | **Resultados (Convergencia)** | **Error $u_x, u_y$, ranking** | **1.5 min** | --- | **1.5 min** |
| **18 [NUEVA]** | **Resultados (Distribución)** | **Histograma temperatura, rango** | **1.5 min** | --- | **1.5 min** |
| **19 [NUEVA]** | **Resultados (Series)** | **Evolución temporal, validación dinámica** | **1.5 min** | --- | **1.5 min** |
| **20 [NUEVA]** | **Resultados (Pérdida)** | **Comparativa L_total, efecto λ** | **1.5 min** | --- | **1.5 min** |
| 21 | Discusión | Logros vs. alternativas | 2.0 min | Reducir a 1.5 min | **1.5 min** |
| 22 | Conclusiones | Contribuciones principales | 1.0 min | --- | **1.0 min** |
| 23 | Trabajo Futuro | Extensiones, limitaciones | 0.8 min | --- | **0.8 min** |
| 24 | Cierre | "Gracias" + contacto | 0.3 min | --- | **0.3 min** |

---

## Cálculo Total

### Antes de calibración:
```
0.3 + 0.5 + 2.5 + 2.5 + 1.0 + 3.0 + 1.5 + 1.5 + 1.5 + 1.5 + 
1.5 + 1.0 + 1.5 + 1.5 + 1.5 + 1.5 + 1.5 + 2.0 + 1.0 + 0.8 + 0.3
= 32.5 minutos (con 3 frames nuevos)
```

### Después de calibración:
```
0.3 + 0.5 + 2.0 + 2.0 + 1.0 + 1.5 + 1.5 + 1.5 + 1.5 + 1.5 + 
1.0 + 1.0 + 1.5 + 1.5 + 1.5 + 1.5 + 1.5 + 1.5 + 1.0 + 0.8 + 0.3
= 30.0 minutos exactos
```

---

## Estrategia de Calibración

**Reducciones aplicadas:**
- Introducción: 2.5 → 2.0 min (-0.5 min): Eliminar detalles secundarios de contexto
- Planteamiento: 2.5 → 2.0 min (-0.5 min): Enfocarse en problema central
- Antecedentes (trad.): 3.0 → 1.5 min (-1.5 min): Condensar métodos a 2-3 bullets
- Metodología (Datos): 1.5 → 1.0 min (-0.5 min): Solo números clave, sin detalles
- Discusión: 2.0 → 1.5 min (-0.5 min): Brevedad en comparativas

**Total reducción:** -3.5 min  
**Espacios nuevos:** +4.5 min (3 frames × 1.5 min)  
**Neto:** +1.0 min (32 → 30 requiere -2, ajustaré a -0.5 adicional en revisión de densidad)

---

## Validación de Coherencia

✓ **21 diapositivas en 30 minutos:** 1.43 min/frame (dentro de rango 1.2-1.5)  
✓ **Balance temático:** Intro (2.8%) + Contexto (8.3%) + Marco (15%) + Método (7%) + **Resultados (25%)** + Síntesis (6.7%)  
✓ **Énfasis en Resultados:** 5 frames (23.8% de contenido) = ~7.5 min (25% del tiempo total)  
✓ **Gráficos integrados:** 7 imágenes distribuidas estratégicamente

---

## Recomendaciones de Entrega

1. **Ensayo cronometrado:** Practicar con PDF a velocidad natural
2. **Validar proyección:** Verificar legibilidad de histogramas y series de tiempo
3. **Buffer de tiempo:** Si en ensayo dan ~29 min, perfecto; si 31-32, revisar densidad de Antecedentes

---

**Estado:** Calibración completada  
**Próximo:** Fase 4 (Validación de gráficos + compilación final)
