# Plan: Justificación climática del Caribe colombiano

**Objetivo:** Reforzar el planteamiento del problema con (a) motivación climática regional específica, (b) evidencia de cobertura insuficiente de estaciones y (c) referencias a fuentes locales verificables.

---

## Fase 1 — Recopilación de fuentes

**1.1 Identificar referencias del IDEAM disponibles en línea**
Buscar y registrar los siguientes documentos institucionales con URL verificable:
- *Atlas Climatológico de Colombia* (IDEAM, 2005 o edición más reciente)
- *Estudio Nacional del Agua* (IDEAM, edición disponible: 2014, 2019)
- *Boletines hidrometeorológicos regionales Caribe* (IDEAM, publicación periódica)
- *Informe del Estado del Medio Ambiente* (IDEAM, versión más reciente)

**1.2 Identificar fenómenos climáticos documentados del Caribe colombiano**
Los siguientes fenómenos están reconocidos en la literatura y pueden ser la base de la justificación — **requieren confirmación con fuente antes de incluirse**:
- Influencia del Jet de Bajo Nivel del Caribe (CLLJ) sobre el régimen de vientos
- Variabilidad asociada a ENSO (El Niño / La Niña) en la precipitación y viento de la región
- Déficit hídrico estructural en los departamentos de La Guajira, Atlántico y Bolívar
- Alta frecuencia de eventos de viento fuerte en la zona costera norte

**1.3 Explorar si existe literatura académica local citable**
Buscar artículos en revistas colombianas (Meteorología Colombiana, Revista de la Academia Colombiana de Ciencias) o tesis de universidades regionales (Universidad del Norte, Universidad de Cartagena) sobre caracterización del viento o presión en el Caribe colombiano.

---

## Fase 2 — Redacción de contenido nuevo

**2.1 Párrafo de contexto climático regional** — a insertar en `14_planteamiento_problema.tex`, dentro de la sección **Justificación**, antes del párrafo metodológico actual.

Estructura propuesta del párrafo (contenido provisional, pendiente de validación con fuentes):

> *"La región Caribe colombiana es una de las zonas de mayor variabilidad climática del país. [FENÓMENO 1 con cita]. [FENÓMENO 2 con cita]. A pesar de esta relevancia, la red de monitoreo del IDEAM en los cinco departamentos considerados cubre [N] estaciones activas sobre un territorio de [área km²], lo que implica una densidad de aproximadamente [D] estaciones por 10.000 km², insuficiente para caracterizar la variabilidad espacial del campo de viento y presión."*

**2.2 Nota sobre cobertura de estaciones con apoyo del mapa** — vincular el texto anterior con la Figura `fig:mapa_estaciones` ya existente en metodología, o referenciarla también desde el planteamiento. Esto conecta la motivación climática con la evidencia visual ya disponible.

**2.3 Entrada(s) nueva(s) en `21_bibliografia.bib`**
Añadir al menos una referencia institucional del IDEAM y una publicación académica con cobertura del Caribe colombiano. Formato `@techreport` o `@article` según corresponda.

---

## Fase 3 — Integración y verificación

**3.1 Ubicación exacta del nuevo contenido**
El párrafo de contexto climático va en `14_planteamiento_problema.tex`, sección `\textbf{Justificación}`, como primer párrafo antes de *"La relevancia del problema radica en que..."*. No requiere crear nueva sección.

**3.2 Verificación de consistencia interna**
- Confirmar que las cifras de densidad de estaciones son coherentes con los datos ya reportados en `17_metodologia.tex` (18 estaciones válidas, dominio de 476,9 km de diagonal).
- Confirmar que las referencias añadidas al `.bib` compilan sin error con `bibtex`.

**3.3 Compilar el documento** con `latexmk -pdf` y verificar que no haya referencias rotas ni advertencias de `bibtex`.

---

## Decisiones que requieren confirmación antes de implementar

1. **¿Qué fuentes del IDEAM tienes acceso o puedes citar?** Si no tienes los documentos en mano, confirmar cuáles están disponibles para no inventar referencias.
2. **¿Deseas mencionar fenómenos climáticos específicos** (p. ej. ENSO, CLLJ, déficit hídrico) o prefieres mantener la justificación a nivel de cobertura de estaciones solamente?
3. **¿El mapa de estaciones debe aparecer también en el planteamiento**, o solo en metodología como está actualmente?

---

## Archivos a modificar

- `14_planteamiento_problema.tex` — insertar párrafo de justificación climática regional
- `21_bibliografia.bib` — agregar entradas de IDEAM y literatura local

**Archivos sin cambio necesario:** el resto del documento ya está coherente con este añadido.
