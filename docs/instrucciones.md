# **Copilot Instructions — Desarrollo de Diapositivas en ![][image1] Beamer**

Este archivo contiene las instrucciones técnicas, de diseño y de procedimiento que gobiernan el desarrollo de la presentación de apoyo para la sustentación del trabajo de grado. Su aplicación es obligatoria tanto para desarrolladores como para agentes autónomos.

## **1\. Identidad del Proyecto y Stack Tecnológico**

* **Propósito:** Generar una presentación de apoyo de alto nivel académico y técnico para una sustentación de maestría de exactamente **30 minutos** de duración.  
* **Insumo Exclusivo:** Documentos e información contenidos únicamente en la carpeta /doctg.  
* **Stack Principal:** ![][image1] Beamer (XeLaTeX o LuaLaTeX como motores de compilación obligatorios para admitir fuentes del sistema mediante fontspec).  
* **Relación de Aspecto:** 16:9 widescreen.  
```
  \\documentclass\[aspectratio=169, 11pt\]{beamer}
```

## **2\. Identidad Visual Coherente**

Para respetar las pautas de marca y el diseño institucional establecido en image\_c5f707.png, se definen las siguientes variables de color y tipografía en el preámbulo de ![][image1].

### **A. Paleta de Colores**

```
\\usepackage{xcolor}

% Paleta de Color Primaria (image\_c5f707.png)  
\\definecolor{ColorVerdeOliva}{HTML}{636F03} % Pantone 378c \- Énfasis principal / Íconos  
\\definecolor{ColorNegroTex}{HTML}{1A171B}   % Pantone Negro \- Texto y estructura oscura  
\\definecolor{ColorRojoVino}{HTML}{B11F16}   % Pantone 484c \- Alertas / Resaltados de alta importancia  
\\definecolor{ColorMostaza}{HTML}{ACAA00}    % Pantone 384c \- Énfasis secundario  
\\definecolor{ColorCremaBg}{HTML}{F3E7CE}    % Pantone 468c \- Fondos claros / Bloques de notas

% Paleta de Color Secundaria (image\_c5f707.png)  
\\definecolor{ColorNaranja}{HTML}{E95D0F}    % Pantone Orange 021 \- Gráficos / Llamados de atención  
\\definecolor{ColorAzulOsc}{HTML}{122253}    % Pantone 294 \- Títulos primarios / Cabeceras  
\\definecolor{ColorTurquesa}{HTML}{0B8689}   % Enlaces / Destacados intermedios  
\\definecolor{ColorAzulMed}{HTML}{1472B8}    % Ilustraciones / Elementos de soporte  
\\definecolor{ColorMorado}{HTML}{634998}     % Agrupaciones secundarias
```

* **Aplicación estructural:**  
  * ColorAzulOsc para títulos de diapositivas (frametitle) y elementos de estructura.  
  * ColorVerdeOliva para bullets, listas, íconos y divisiones metodológicas estables.  
  * ColorNegroTex como color principal de tipografía del cuerpo para asegurar máximo contraste.  
  * ColorRojoVino y ColorNaranja exclusivamente para alertas críticas y métricas excepcionales.  
  * ColorCremaBg para rellenar fondos de bloques resaltados o cajas de teoremas/notas.

### **B. Especificación Tipográfica (Requiere fontspec)**
```

\\usepackage{fontspec}  
\\usepackage{unicode-math} % Paridad tipográfica en fórmulas

% 1\. Roboto para Títulos (Sans-serif moderno y limpio)  
\\newfontfamily\\titlefont{Roboto}\[Path \= ./fonts/, Extension \= .ttf, UprightFont \= \*-Regular, BoldFont \= \*-Bold\]  
\\setbeamerfont{title}{family=\\titlefont, series=\\bfseries}  
\\setbeamerfont{frametitle}{family=\\titlefont, series=\\bfseries}

% 2\. Galliard para Texto Convencional (Serif elegante de alta legibilidad para el cuerpo)  
\\setmainfont{Galliard}\[Path \= ./fonts/, Extension \= .otf, UprightFont \= \*-Roman, BoldFont \= \*-Bold, ItalicFont \= \*-Italic\]  
\\usefonttheme{serif}

% 3\. Titillium para Notas al Pie e Indicadores de Página (Compacto y legible a menor escala)  
\\newfontfamily\\footfont{TitilliumWeb}\[Path \= ./fonts/, Extension \= .ttf, UprightFont \= \*-Regular, BoldFont \= \*-Bold\]  
\\setbeamerfont{footnote}{family=\\footfont, size=\\tiny}  
\\setbeamerfont{date}{family=\\footfont, size=\\small}
```

## **3\. Anti-Patterns de Desarrollo (Rechazar Siempre)**

| ❌ No hacer | ✅ Hacer en su lugar |
| :---- | :---- |
| **Fuentes Externas:** Introducir papers, datos complementarios o bibliografía ajena que no esté explícitamente redactada en /doctg. | **Fidelidad Estricta:** Sintetizar exclusivamente los datos, conclusiones, fórmulas y metodologías presentes en el trabajo de grado original. |
| **Notación Inconsistente:** Cambiar o simplificar la nomenclatura de variables y funciones de pérdida (ej: renombrar una función de costo ![][image2] a ![][image3]). | **Consistencia Matemática:** Transcribir exactamente la misma simbología y fórmulas matemáticas detalladas en la tesis escrita. |
| **Saturación de Texto:** Llenar diapositivas con párrafos largos copiados directamente del documento escrito. | **Estructura Visual:** Usar diagramas, columnas divididas (\\begin{columns}), tablas de métricas resumidas y listas con viñetas. |
| **Inyección de Código Directo:** Insertar fragmentos kilométricos de código sin procesar en la presentación principal. | **Abstracción Algorítmica:** Explicar el funcionamiento general mediante bloques matemáticos y derivar el código o los hiperparámetros a las diapositivas de respaldo. |
| **Ignorar Límites de Tiempo:** Diseñar más de 25 diapositivas principales para un espacio de 30 minutos, forzando una exposición apresurada. | **Control de Pacing:** Limitar la presentación principal a un rango de **17 a 24 diapositivas** (promedio de 1.2 a 1.5 minutos por slide). |
| **Falta de Respaldo:** Dejar la sesión de preguntas del jurado a la deriva sin soporte visual estructurado. | **Backup Slides:** Diseñar diapositivas de soporte técnico riguroso tras la conclusión de la charla mediante el comando \\appendix en Beamer. |

## **4\. Protocolo Obligatorio para Agentes (Sesiones de Desarrollo)**

### **Fase 0 — Planificación Estratégica (Antes de escribir código)**

Esta fase es de carácter obligatorio antes de iniciar cualquier desarrollo de diapositivas en Beamer.

1. **Preguntas de Optimización al Usuario:**  
   El agente debe formular preguntas previas para definir el plan de la sesión:  
   * **Énfasis de Contenido:** ¿En qué secciones del trabajo se desea un mayor despliegue (ej. profundidad en el diseño del modelo vs. análisis de los experimentos)?  
   * **Complejidad Matemática:** ¿Se desea transcribir la derivación formal completa o priorizar la intuición conceptual ilustrada de las ecuaciones clave?  
   * **Selección de Evidencia:** ¿Cuáles son las tablas y gráficas de la carpeta /doctg que el jurado considerará indispensables?  
2. **Revisión Holística del Proyecto:**  
   Antes de generar el código de las diapositivas, el agente debe verificar de forma panorámica:  
   * Que las ecuaciones seleccionadas mantengan una estricta paridad con la tesis.  
   * Que los flujos de la metodología (bloques lógicos) se correspondan con el documento base.  
   * Evaluar riesgos de saturación visual o rupturas tipográficas debidas al compilador de ![][image1].

## **5\. Documentación Dinámica y Control de Sesión**

Para asegurar la preservación del contexto técnico en sesiones de desarrollo largas o fragmentadas, se implementa la metodología de **Documentación Dinámica**.

### **A. Tipos de Documentos Dinámicos**

* **PLAN (plan\_\*\_2026.md):** Se elabora al inicio de la sesión para trazar la estructura lógica de las diapositivas que se van a codificar en ese bloque de trabajo.  
* **INFORME (informe\_\*\_2026.md):** Consolida progresivamente los aportes y el código Beamer estructurado con éxito al cierre de una sesión.  
* **REPORTE (reporte\_\*\_2026.md):** Diseñado especialmente para la visualización del avance global del proyecto (resumen ejecutivo de las diapositivas completadas y el guion del orador).

### **B. Gestión de Checkpoints**

* **Ubicación:** Todos los checkpoints de avance intermedio se deben registrar en la carpeta /checkpoints en la raíz del proyecto.  
* **Nomenclatura:** ``checkpoints/CP\_FASE\[X\]\_V\[Y.Z\].md`` (ej. ``checkpoints/CP\_FASE2\_v1.0.md``).  
* **Estructura base del Checkpoint:**  
```
  # Checkpoint: [Fecha] - Fase [X] - Versión [Y.Z]

  ## 1. Estado del Compilador  
  * Compila exitosamente en: [XeLaTeX / LuaLaTeX]  
  * Log de advertencias/fuentes: [Detalles]

  ## 2. Diapositivas Consolidadas en esta Versión  
  * [Listado de frames completados con su respectivo ID / Tema]

  ## 3. Estado del Código LaTeX  
  * [Estructura del preámbulo o bloques Beamer modificados]

  ## 4. Tareas Pendientes para Siguiente Sesión  
  * [ ] Tarea 1  
  * [ ] Tarea 2
```


[image1]: <data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAADQAAAAZCAYAAAB+Sg0DAAADTklEQVR4Xu1XO2gUURTdhQiKimhcl2R3522yK0HE76I2ahE/2GijVtqKYkDBwr8gaYUk6IqxEEkRUFOKESwkGCFgISnEbSxUghALbRIhaozn7NyX3DxnZtcUYYs9cJh59973OfPuuzMTi9VRR+2hubk5UygUlrj2mkQ2mz1jjJkAZxQnPM97lEwml7e2tq5CzCvwkO0Dfxf8n6shYodzudw6PacL+hE34qxhBv176cf9Bcc3nclkDrrjzAOCrsogl7Qd7d2w/wKLaMZpg7g+tPtbWlqSbCcSiRVoD4E/MNEOiVkKnoZtHHFb1JChYBagzxNZ9I2YzEdgHadgG02n0+tVl3BQSIigc7B3gSUKkMUPgBtsjBI0ifiC6t4A2wPHFgksOI8+X8Dv4DbamCW4fwqxu9z4UChBh5VtNWzXIGQzJ2HacfGwd9Nn4yIEcYyL4H5tqwQ5BjMUwbTnGLBdjqkdq4gQQUy3o0wfXJ+BRe4Srl2cyMYFCcJ9E/hJFlaJw21tbSv1eJjzhfiK4GAqlWq0/qoQJAiDXkH7PHgMvn4jaaf7EUGCLLCQNOxj7qLF1wj7LfAj2KR9mGen8dPuN8bcp31VwRXk+enWKQtqwmHfg+u4rnYWUYLY1/g7NcQ47SPkfAwGFI44xuqVNT3879dGgKByull/Pp9PcGKjqp3FQgShvR0FYI3cX+d8c73K82+E/a3nl36W6SPaXxFaEK5nwa/gO+zIVp4X3L+G7wMXDdt9MGv7LlDQHRuL3dnLeawPQpfBPwD/AcmMKbDEbLExFeHukAa+FNbCfi8oZYgqBY1ISWa7HRx1Yy1gPwn/Xdw2xPzU65G19bDthAcjShDPDXzPdWXTqFLQNDjm+Sn0LSiWoGhWOL0bco5L4BR3TMeHwsiXAnhCmeNot3MRmKRP2edBCZr9UrBQgmZTjimF8R4rQXFWQHklvAQ7aZsbpfzAj8P+B/3eRJbwrP8S43mhGEu2+RS17XZIX5ZkPn0dy/7lbzgTIIgw6gwZPwUn9RiefMeJv0P7hO/xYDbZmEWDCRGkwV2FgG7XXpOoRhB2+ibPsGuvSUR9KfBlKRWNBeKfYlRTkPMzDP5Uec//LPufpM9oYMWro446Fh9/AUt+ZMitMTg5AAAAAElFTkSuQmCC>

[image2]: <data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAAwAAAAYCAYAAADOMhxqAAAA1ElEQVR4XmNgGGZAXl7eEYhfA/F/JPwLiHdLS0sLo6uHA6CCOUD8T0FBwQNdDgPIyckJAhWfBuIHMjIy0ujyGACoUBOI3wLxGiCXBV0eAwAVRoPcDrSpHF0OKwAqngTEv4EabNDlMACS++8qKiqKo8tjAELu19LSYkMRIEcD2MNAXIQiAQGMQPEmFBF5SIRh9TAwTlSAcnPhAvgizNjYmBUoPgvkArggUIMxUOAruvuBfEmQYqD8IyCtCFLoAmQ8k0cktr9A/ASKQWyY+HJkg0YBPgAAqU5C+6AAgqYAAAAASUVORK5CYII=>

[image3]: <data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAA4AAAAYCAYAAADKx8xXAAABNUlEQVR4XmNgGGFAXl7eE4j/E4PR9YKBgoLCQqDkbzk5ORs0KUaguBEQP0QTZ2AAKhYESpwG4gcyMjLS6PIgAJSbgy7GoKioqA+U+ATEa4BcFqgwC9AVBsbGxqwgDpA9EaEDCoAaokF+ANpcDhMD2QyyRVxcnBuqJgihAwpACoD4D9DUACAtCXSBPJA9E8huRVcLB0j++wvET4D8R0D6FYgPZLugq4cDoKQxUNFXZP+pq6vzAm1cBZRTgipjBokhdDFQoFEeGjBAXAQTA/pRHMifDNTMAeID6QggPwuhCxi5QFPny2OPeDAQFRXlAcovBmJFuCBSwNwF2YKkHg6AamJAtgOZjMiCGP6DAVD8AZ1YAZR7DcSWMA0uQM4zeUQChkcFNDp+weSA/B3AxMCJbOgooCYAAFlFX/C7tBdGAAAAAElFTkSuQmCC>