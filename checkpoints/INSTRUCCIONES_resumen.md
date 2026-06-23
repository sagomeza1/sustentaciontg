# Resumen: Instrucciones Formalizadas

**Archivo creado:** `.instructions.md` en raíz del workspace  
**Fecha:** 2026-06-23  
**Basado en:** `docs/instrucciones.md` + proyecto PINN Caribe completado

## ✓ Lo que se formalizó

### 1. Principios Fundamentales
- Enfoque académico riguroso (síntesis de `/doctg`, no fuentes externas)
- Stack: Beamer 16:9 (flexible en motor LaTeX)
- Anti-patterns evaluados caso a caso (no rigidez absoluta)

### 2. Identidad Visual
- **8 colores definidos** (Verde Oliva, Negro, Rojo Vino, etc.) - aplicables a trabajos de grado
- **3 familias tipográficas** (Roboto, Galliard, Titillium) - con fallback a defaults si no disponibles
- **Paleta secundaria** para soporte (Naranja, Azul, Turquesa, Morado)

### 3. Estructura y Diseño
- Rango recomendado: **17-24 diapositivas** (~30 min)
  - **Flexible:** respeta preferencias del usuario
- 5 diapositivas obligatorias (Portada, TOC, Contenido, Conclusiones, Cierre)
- Anti-patterns con evaluación contextual

### 4. Protocolo para Agentes: Fase 0 Obligatoria
**Antes de desarrollar, siempre:**
1. Hacer 5 preguntas de optimización al usuario
2. Revisar holísticamente la estructura propuesta
3. Crear `plan.md` con confirmación del usuario
4. **No proceder sin aprobación del plan**

### 5. Gestión de Documentación
- **plan.md** (dinámico, actualizado por fase)
- **checkpoints/** (uno por fase completa)
- Nomenclatura clara (`sections/`, `figures/`, `checkpoints/`)

### 6. Flujo Estándar: 4 Fases
1. **Fase 1:** Esqueleto (carpeta + main.tex + templates)
2. **Fase 2:** Contenido (síntesis de doctg)
3. **Fase 3:** Timing (balance de duración)
4. **Fase 4:** Gráficos (incorporación de evidencia visual)

---

## 📋 Ejemplos de Prompts Efectivos

### Fase 0: Iniciar proyecto
> "Crea una presentación Beamer para [tema]. Primero, Fase 0: preguntas de optimización, evaluación holística, y propuesta de plan.md"

### Incorporar gráficos
> "Fase 2 completada. Ahora Fase 3: analiza timing actual y propone ajustes para mantener ~30 minutos. ¿Necesito dividir algún frame?"

### Validar estructura
> "¿La estructura actual respeta las instrucciones de .instructions.md? Especialmente: nomenclatura de archivos, rango de diapositivas, anti-patterns aplicables"

### Crear checkpoint
> "Fase 3 completada. Por favor: crea checkpoint en `checkpoints/plan_fase3_completada.md` con estadísticas actuales"

---

## 🔄 Cómo Invocar el Archivo en Futuras Sesiones

### Opción 1: Referencia Explícita
```
Desarrolla una nueva presentación Beamer siguiendo las instrucciones en `.instructions.md`.
```

### Opción 2: Activación Automática
El archivo `.instructions.md` será detectado por el agente automáticamente en sesiones futuras en este workspace.

### Opción 3: Preguntas Específicas sobre Instrucciones
```
Según `.instructions.md`, ¿debo aplicar la paleta de colores obligatoriamente?
```

---

## 📊 Comparación: Antes vs. Después

| Aspecto | Antes | Después |
|---------|-------|---------|
| Referencia de normas | Archivo `docs/instrucciones.md` (largo, contextual) | `.instructions.md` (formalizado, ejecutable por agentes) |
| Ambigüedad | Alta (muchas secciones descriptivas) | Baja (reglas claras con excepciones documentadas) |
| Fase 0 | Sugería en docs | **Obligatoria** en instrucciones |
| Flexibilidad | Implícita | **Explícita** (casos a caso, con notificaciones) |
| Aplicabilidad | Solo PINN Caribe | **General a cualquier trabajo de grado** |

---

## 🎯 Partes Potencialmente Ambiguas (Para Refinamiento Futuro)

1. **Validación de Fase 0:** ¿Cuándo se considera que Fase 0 fue "suficiente"?
   - Sugerencia: cuando el usuario confirma el plan.md propuesto

2. **Umbral de gráficos:** ¿Cuántos gráficos son demasiados por frame?
   - Sugerencia: máx 2-3 imágenes por slide (evitar saturación visual)

3. **Decisiones sobre fallback:** ¿Fontspec no disponible = usar defaults, o preguntar?
   - **Decisión actual:** Preguntar si desea instalar; si no, usar defaults

4. **Checkpoint timing:** ¿Crear checkpoint incluso si Fase N es pequeña?
   - Sugerencia: solo para fases con avance tangible (PDF compilable, x10 slides, etc.)

---

## 📝 Próximos Pasos Sugeridos

### Para refinar instrucciones:
1. Ejecutar un nuevo proyecto pequeño siguiendo `.instructions.md` 
2. Documentar ambigüedades encontradas
3. Iterar sobre secciones débiles

### Para otros usuarios/proyectos:
1. Compartir `.instructions.md` como referencia
2. Adaptar paleta de colores si es necesario (sección 2.1 es editable)
3. Ajustar rango de diapositivas según contexto

---

## ✅ Validación

- ✓ Archivo `.instructions.md` creado y formalizado
- ✓ Flexibilidad explícita en anti-patterns, tipografía y rango
- ✓ Fase 0 obligatoria documentada
- ✓ Protocolo de checkpoints establecido
- ✓ Ejemplos de prompts provistos
- ✓ Adaptable a múltiples proyectos

**Estado:** Listo para uso en futuras sesiones de desarrollo Beamer

---

**Responsable de creación:** AI Agent  
**Base normativa:** `docs/instrucciones.md` + Proyecto PINN Caribe (Fases 1-4)  
**Ubicación:** `/home/alejandro/PresentacionTG/.instructions.md`
