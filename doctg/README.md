# Prediccion de variables meteorologicas usando una PINN basada en las ecuaciones de Navier-Stokes

Repositorio del documento de tesis de maestria desarrollado en LaTeX sobre el uso de Redes Neuronales Fisicamente Informadas (PINN) para la prediccion de variables meteorologicas en la region Caribe de Colombia.

## Descripcion

Este proyecto contiene la memoria escrita de la tesis desarrollada en LaTeX. El trabajo plantea una aproximacion basada en PINN para integrar aprendizaje profundo con restricciones fisicas derivadas de las ecuaciones de Navier-Stokes, con el objetivo de mejorar la prediccion meteorologica y reducir el costo computacional frente a enfoques numericos tradicionales.

El documento esta orientado al contexto de estaciones meteorologicas ubicadas en los departamentos de Atlantico, Bolivar, Magdalena, Sucre y Cordoba, y aborda aspectos de formulacion del problema, objetivos, antecedentes, marco teorico, metodologia, resultados, discusion y conclusiones.

## Contenido

El repositorio incluye:

- El archivo principal del documento: main.tex
- Los capitulos y secciones de la tesis en archivos .tex independientes
- La bibliografia en 21_bibliografia.bib
- Recursos graficos en la carpeta Imagenes

## Direccion

Proyecto dirigido por el Dr. Diego Roldan.

## Compilacion

Para generar el documento PDF se recomienda ejecutar:

```bash
latexmk -pdf main.tex
```

Como alternativa, puede compilarse manualmente con:

```bash
pdflatex main.tex
bibtex main
pdflatex main.tex
pdflatex main.tex
```

## Autor

S. Alejandro Gomez Ardila