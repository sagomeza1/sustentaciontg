# QUICK REFERENCE: .instructions.md

**Ubicación:** `.instructions.md` en raíz del workspace  
**Aplicación:** Agentes desarrollando presentaciones Beamer para trabajos de grado

---

## 🚀 FASE 0 (OBLIGATORIA)

Preguntas antes de cualquier desarrollo:
```
1. ¿En qué secciones deseas mayor despliegue?
2. ¿Derivación matemática completa o intuición conceptual?
3. ¿Cuáles gráficos de /doctg son indispensables?
4. ¿Duración: exactamente 30 min o rango flexible?
5. ¿Visuales específicas prioritarias?
```

**Resultado:** `plan.md` con confirmación del usuario

---

## 📊 ESTRUCTURA ESTÁNDAR

```
17-24 diapositivas | ~30 minutos | 1.2-1.5 min/slide

Diapositivas obligatorias:
1. Portada
2. Tabla de Contenidos
3-N. Contenido (metodología + resultados priorizado)
N+1. Conclusiones
N+2. Cierre
```

---

## 🎨 IDENTIDAD VISUAL (Trabajos de Grado)

| Elemento | Color | Uso |
|----------|-------|-----|
| Títulos de frames | ColorAzulOsc (#122253) | `\frametitle`, cabeceras |
| Bullets/Listas | ColorVerdeOliva (#636F03) | `\begin{itemize}`, divisiones |
| Texto cuerpo | ColorNegroTex (#1A171B) | Máximo contraste |
| Alertas críticas | ColorRojoVino (#B11F16) | Excepciones, métricas destacadas |
| Fondos de bloques | ColorCremaBg (#F3E7CE) | `\begin{block}` resaltados |

**Nota:** Flexible. Si el usuario especifica otra paleta, respetar preferencia.

---

## ⚙️ LAS 4 FASES

| Fase | Objetivo | Checkpoint |
|------|----------|-----------|
| **1** | Crear carpeta, main.tex, templates de sections/ | PDF compilable con placeholders |
| **2** | Llenar contenido de /doctg, sintetizar | Contenido completo (falta gráficos) |
| **3** | Balancear timing, expandir/condensar, validar rango | Estructura lista, timing validado |
| **4** | Incorporar gráficos, imágenes, recompilación final | PDF final con visualización |

---

## ❌ ANTI-PATTERNS (Evaluar caso a caso)

```
❌ Párrafos largos copiados      → ✅ Viñetas, columnas, tablas
❌ Cambiar nomenclatura de vars  → ✅ Exacta como en tesis
❌ Código extenso sin procesar   → ✅ Abstraer con bloques matemáticos
❌ >25 slides para 30 min        → ✅ 17-24 slides (respetar si usuario pide otro rango)
❌ Sin timing en frames          → ✅ \note[item]{Tiempo: X min}
❌ Sin slides de respaldo        → ✅ \appendix con soporte técnico
```

**Política:** Si el usuario pide excepción, preguntar antes de rechazar.

---

## 📁 DOCUMENTACIÓN DINÁMICA

```
plan.md                          # Se actualiza cada fase con [x] / [ ]
checkpoints/
├── plan_fase1_completada.md
├── plan_fase2_completada.md
├── plan_fase3_completada.md
└── plan_fase4_completada.md
```

**Crear checkpoint cuando:**
- Se completa una fase mayor (1, 2, 3, etc.)
- Se alcanza milestone (PDF compilable, gráficos incorporados, etc.)

---

## 🔧 COMPILACIÓN

```bash
cd mi_sustentacion/
rm -f *.aux *.log *.out *.toc *.nav *.snm *.vrb
pdflatex -interaction=nonstopmode main.tex
pdfinfo main.pdf | grep -E "Pages|Title"
```

**Criterios de éxito:**
- ✓ Compila sin errores
- ✓ Número de páginas esperado (17-24)
- ✓ Gráficos embebidos (si aplica)

---

## 💬 PROMPTS EFECTIVOS

### Iniciar proyecto
```
Voy a crear una presentación Beamer para [TEMA].
Por favor: Fase 0 (preguntas, revisión holística, propuesta de plan.md)
```

### Después de completar contenido
```
Fase 2 completada. Ahora Fase 3: analiza timing, propón ajustes para ~30 min.
¿Divido algún frame?
```

### Para incorporar gráficos
```
Fase 3 validado. Ahora Fase 4: incorpora estos gráficos de /doctg:
[lista de imágenes]
```

### Crear checkpoint
```
[Fase X] completada. Crea checkpoint en checkpoints/plan_faseX_completada.md
```

---

## 📌 CHECKLIST MÍNIMO

- [ ] Fase 0 completada (plan.md aprobado por usuario)
- [ ] Fase 1: Esqueleto compilable
- [ ] Fase 2: Contenido de /doctg integrado
- [ ] Fase 3: Timing balanceado (17-24 slides, ~30 min)
- [ ] Fase 4: Gráficos incorporados, PDF final
- [ ] Checkpoints creados tras cada fase
- [ ] plan.md actualizado con [x] en tareas completadas

---

**Para más detalles:** Ver `.instructions.md` completo  
**Última revisión:** 2026-06-23  
**Aplicable a:** Trabajos de grado, presentaciones Beamer en este workspace
