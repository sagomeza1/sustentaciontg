# Fase 0: Propuesta de Revalidación v2.0

**Fecha:** 2026-06-23  
**Base:** `.instructions.md` Protocolo Obligatorio Fase 0  
**Estado:** Pendiente confirmación del usuario

---

## 📋 Preguntas Fase 0 — Respuestas Confirmadas

| Pregunta | Respuesta | Implicación |
|----------|-----------|------------|
| **Énfasis de contenido** | Expandir Resultados (+2-3 frames) | Análisis profundo de convergencia, distribuciones, comparativa |
| **Complejidad matemática** | Híbrido (intuición + ecuaciones) | Explicar N-S, PINNs con formulación rigurosa pero accesible |
| **Gráficos indispensables** | Agregar de `/doctg/Imagenes/` | Histogramas, series de tiempo, análisis de pérdida |
| **Duración objetivo** | Exactamente 30 minutos | Ajustar densidad, no sobrepasar 30 min ±30 seg |
| **Validación** | Completa (Fases 1-4 + PDF) | Reconstruir desde cero validando cada fase |

---

## 🎯 Cambios Propuestos vs. Versión Actual (v1.0)

### Cambios en Contenido

| Elemento | v1.0 (18 slides) | v2.0 Propuesta (20-22 slides) | Cambio |
|----------|-----------------|-------------------------------|--------|
| **Resultados** | 2 frames | 5 frames | +3 nuevos frames |
| **Énfasis** | Balance general | Profundidad en análisis | Rebalanceo |
| **Gráficos** | 3 imágenes | 6-7 imágenes | +3-4 adicionales |
| **Duración** | ~32 min | ~30 min | -2 min (ajuste de densidad) |

### 3 Nuevos Frames en Sección Resultados

**Frame 15: Distribución de Temperatura** (1.5 min)
- Gráfico: `descripcion_datos/em_caribe_temp_hist.png`
- Contenido: Histograma de distribución, media/desviación, rango de validación
- Propósito: Caracterizar datos de entrada del modelo

**Frame 16: Series Temporales del Modelo** (1.5 min)
- Gráfico: `descripcion_datos/em_caribe_time_series.png`
- Contenido: Evolución temporal de predicciones vs. observadas
- Propósito: Visualizar calidad de reconstrucción temporal

**Frame 17: Análisis Comparativo de Pérdida Total** (1.5 min)
- Gráfico: `rango/01_loss_total_comparativa.png`
- Contenido: Comparación de pérdida L_total entre experimentos R01 vs. R04
- Propósito: Justificar selección de mejores hiperparámetros (λ)

---

## 📊 Estructura Propuesta: 20-22 Diapositivas

```
Sección 1-2:    Portada + TOC                        (~1 min)
Sección 3-5:    Intro + Planteamiento + Objetivos    (~3.8 min)
Sección 6-10:   Antecedentes + Marco Teórico         (~7.5 min)
Sección 11-12:  Metodología                          (~2 min)
Sección 13-17:  Resultados EXPANDIDO                 (~7.5 min)
Sección 18-21:  Discusión + Conclusiones + Cierre    (~3.2 min)
────────────────────────────────────────────────
TOTAL:          20-22 frames | 30 minutos (±30 seg)
```

---

## 🔄 Gráficos a Integrar

```
Actuales (mantener):
  ✓ doctg/Imagenes/mapa_estaciones_caribe.png         (Frame 4)
  ✓ mi_sustentacion/figures/error_u.png              (Frame 14)
  ✓ mi_sustentacion/figures/error_v.png              (Frame 14)

Nuevos (agregar):
  + doctg/Imagenes/descripcion_datos/em_caribe_temp_hist.png        (Frame 15)
  + doctg/Imagenes/descripcion_datos/em_caribe_time_series.png      (Frame 16)
  + doctg/Imagenes/rango/01_loss_total_comparativa.png              (Frame 17)

Total esperado: 6-7 imágenes en PDF final (~2.3-2.5 MB)
```

---

## ✅ Checklist de Implementación

### Fase 1: Esqueleto (Revalidación)
- [ ] Verificar carpeta `mi_sustentacion/` intacta
- [ ] Validar `main.tex` compilable (test rápido)
- [ ] Confirmar 9 archivos `sections/`
- [ ] Expandir `figures/` para gráficos adicionales

### Fase 2: Contenido (Expansión)
- [ ] Mantener secciones 1-6 sin cambios
- [ ] Actualizar `07_resultados.tex` con 5 frames (en lugar de 2)
  - [ ] Frame 13: Tabla de modelos (actual)
  - [ ] Frame 14: Convergencia (actual)
  - [ ] Frame 15: Histograma (nuevo)
  - [ ] Frame 16: Series temporales (nuevo)
  - [ ] Frame 17: Pérdida comparativa (nuevo)
- [ ] Mantener frames 8-9 (Discusión + Conclusiones)

### Fase 3: Timing (Calibración)
- [ ] Calcular duración por frame incluyendo nuevos
- [ ] Reducir densidad en Antecedentes si tiempo > 30 min
- [ ] Validar: exactamente 30 min (±30 seg)

### Fase 4: Gráficos (Integración)
- [ ] Copiar 3 gráficos nuevos a `figures/`
- [ ] Actualizar referencias en `07_resultados.tex`
- [ ] Recompilar PDF
- [ ] Validar legibilidad de todas las imágenes

---

## 🚀 Propuesta de Ejecución

**Si confirmas esta propuesta, procederé:**

1. **Fase 1:** Verificación rápida de estructura (5 min)
2. **Fase 2:** Expansión de sección Resultados + síntesis (15 min)
3. **Fase 3:** Análisis de timing + ajustes de densidad (10 min)
4. **Fase 4:** Copia de gráficos + integración + compilación PDF final (10 min)

**Tiempo total estimado:** ~40 minutos  
**Artefacto final:** PDF compilado con 20-22 diapositivas, exactamente 30 min, 2.3-2.5 MB

---

## ❓ ¿Próximo Paso?

**Opción A (Confirmación):**
```
"Sí, procede con la revalidación v2.0 según propuesta"
→ Ejecuto Fases 1-4 inmediatamente
```

**Opción B (Ajustes):**
```
"Cambiar [X] en la propuesta" 
→ Actualizo plan y re-propongo
```

**Opción C (Rechazo):**
```
"Mantener versión v1.0 (18 slides, 32 min)"
→ Cierra revalidación, conserva PDF actual
```

---

**Responsable:** AI Agent  
**Base normativa:** `.instructions.md` Fase 0 Obligatoria  
**Estado:** Pendiente confirmación del usuario para proceder a Fases 1-4
