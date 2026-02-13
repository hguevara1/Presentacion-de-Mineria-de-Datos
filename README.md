# Aplicación de la Metodología CRISP-DM para el Análisis de Datos de Cáncer

Este repositorio contiene una presentación en **LaTeX (Beamer)** que explica la aplicación de la metodología **CRISP-DM** al análisis de un conjunto de datos médicos relacionados con el diagnóstico de cáncer. El objetivo es mostrar el proceso completo de minería de datos, desde el entendimiento del negocio hasta el despliegue del modelo.

---

## 📌 Contenido del Proyecto

- **Presentación Beamer (`.tex`)** con:
  - Introducción a la minería de datos.
  - Justificación del uso de CRISP-DM.
  - Explicación detallada de las seis fases:
    1. Entendimiento del Negocio  
    2. Entendimiento de los Datos  
    3. Preparación de los Datos  
    4. Modelado  
    5. Evaluación  
    6. Despliegue  
  - Ejemplos de EDA, preprocesamiento y selección de algoritmos.
  - Bibliografía académica.

---

## 🎯 Objetivo del Proyecto

Desarrollar un flujo de trabajo estructurado para predecir si una observación celular es **benigna o maligna**, siguiendo las mejores prácticas de minería de datos y aprendizaje automático.

---

## 🧠 Metodología: CRISP-DM

La presentación cubre:

- **Entendimiento del negocio:** definición de objetivos y criterios de éxito.  
- **Entendimiento de los datos:** exploración inicial, calidad y características relevantes.  
- **Preparación:** escalamiento, codificación, reducción de dimensionalidad.  
- **Modelado:** algoritmos candidatos como k-NN, Regresión Logística y Árboles de Decisión.  
- **Evaluación:** métricas como Accuracy, F1-Score y matriz de confusión.  
- **Despliegue:** consideraciones para integración y mantenimiento.

---

## 🛠 Tecnologías Utilizadas

- **LaTeX (Beamer)**
- **MiKTeX / TeX Live**
- **Paquetes:** `beamer`, `babel`, `inputenc`, `cite`

---

## 📄 Compilación

Para compilar la presentación:

```bash
pdflatex presentacion.tex
