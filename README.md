# Proyecto de Minería de Datos: Análisis de Datos de Cáncer con Metodología CRISP-DM

## 📋 Descripción General

Este proyecto aplica la metodología **CRISP-DM** (Cross-Industry Standard Process for Data Mining) para el análisis de un dataset de cáncer de mama. El objetivo principal es desarrollar modelos predictivos que clasifiquen tumores como **benignos (B)** o **malignos (M)** basándose en características morfológicas de núcleos celulares extraídas mediante imágenes digitalizadas.

**Autores:** Héctor Guevara - Guillermo Hernández  
**Institución:** Universidad Central de Venezuela (UCV)  
**Año:** 2026  
**Dataset:** [Cancer Data (Breast Cancer Wisconsin Diagnostic)](https://www.kaggle.com/datasets/erdemtaha/cancer-data)

---

## 🎯 Objetivos del Proyecto

1. Aplicar la metodología CRISP-DM de principio a fin para garantizar un desarrollo sistemático y orientado al negocio.
2. Realizar un análisis exploratorio exhaustivo para comprender la estructura y calidad de los datos.
3. Implementar técnicas de preprocesamiento para preparar los datos para el modelado.
4. Evaluar diferentes algoritmos de clasificación (KNN, Árboles de Decisión, Redes Neuronales).
5. Seleccionar el modelo óptimo considerando métricas clínicas (minimización de falsos negativos).
6. Discutir consideraciones para un hipotético despliegue en producción.

---

## 📊 Dataset: Cancer Data

### Características Generales
- **Instancias:** 569 muestras de pacientes
- **Atributos iniciales:** 32 columnas
- **Variable objetivo:** `diagnosis` (M = Maligno, B = Benigno)
- **Proporción:** 63% benignos, 37% malignos (ligero desbalance)

### Estructura de los Datos
El dataset contiene 10 características base para cada núcleo celular:
- **Radio (radius):** Distancia media del centro al perímetro
- **Textura (texture):** Desviación estándar de escala de grises
- **Perímetro (perimeter):** Longitud del contorno
- **Área (area):** Superficie total
- **Suavidad (smoothness):** Variación local de radios
- **Compacidad (compactness):** `perímetro²/área - 1.0`
- **Concavidad (concavity):** Gravedad de porciones cóncavas
- **Puntos cóncavos (concave points):** Número de porciones cóncavas
- **Simetría (symmetry):** Balance de la forma
- **Dimensión fractal (fractal dimension):** Complejidad del borde

Para cada característica, se registran **tres estadísticos**:
- **Media (Mean):** Valor promedio
- **Error Estándar (SE):** Variabilidad en la medición
- **Peor Caso (Worst):** Media de los tres valores más grandes

---

## 🧪 Metodología CRISP-DM

### Fase 1: Entendimiento del Negocio y los Datos

**Objetivo de negocio:** Clasificación binaria para predecir malignidad tumoral.

**Criterios de éxito:**
- Minimizar **falsos negativos** (casos de cáncer no detectados)
- Utilizar métricas robustas: **F1-Score** y **Recall** para clase maligna
- Considerar el desbalance de clases en la evaluación

**Auditoría inicial de calidad:**
- Verificación de valores nulos
- Consistencia de formatos
- Identificación de outliers potenciales

### Fase 2: Preparación de Datos y Análisis Exploratorio (EDA)

#### Preprocesamiento Estructural
- ✅ Eliminación de columna `Unnamed: 32` (100% valores nulos)
- ✅ Verificación de 0 registros duplicados
- ✅ Validación de dominios en variable objetivo (solo 'M' y 'B')
- ✅ Reducción de dimensionalidad por selección:
  - Eliminación de variables de radio y área por alta correlación con perímetro
  - Conservación de `perimeter_mean`, `perimeter_se`, `perimeter_worst`

#### Análisis Exploratorio (EDA)

**Hallazgos clave:**
1. **Distribuciones variadas:** Variables con tendencia central (`texture_mean`) y sesgo positivo (`area_worst`, `concavity_worst`)
2. **Outliers detectados:** Valores extremos en variables de "peor caso" que pueden ser clínicamente significativos
3. **Multicolinealidad geométrica:** Alta correlación entre radio, perímetro y área (justificada por morfología circular)
4. **Predictores fuertes:** `concave points_worst`, `perimeter_worst`, `concavity_mean` con clara separación entre clases

**Visualizaciones implementadas:**
- Histogramas con KDE
- Boxplots + Stripplots combinados
- Mapas de calor de correlación
- Pair plots multivariados

#### Preprocesamiento Funcional
- **Label Encoding:** `diagnosis` → `diagnosis_num` (M=1, B=0)
- **Estandarización:** `StandardScaler` para media 0, desviación 1
- **Muestreo estratificado:** Preservación de proporciones 80/20 train/test

#### Experimento: Evaluación de Redundancia en KNN
| Modelo | Accuracy | F1-Score (Maligno) | Falsos Negativos |
|--------|----------|---------------------|------------------|
| Initial (24 var.) | 0.9386 | 0.91 | 6 |
| Reduced (14 var.) | 0.9211 | 0.89 | 7 |

**Conclusión:** La información adicional en variables correlacionadas mejora la sensibilidad en KNN. Se selecciona el conjunto completo para fases siguientes.

### Fase 3: Modelado

#### Algoritmos Evaluados

| Modelo | Configuración Óptima | F1-Score (Test) | AUC (Test) |
|--------|---------------------|------------------|------------|
| **KNN** | 11 vecinos, distancia euclídea, pesos uniformes | 0.9136 | 0.981 |
| **Árbol de Decisión** | Profundidad 5, criterio entropía | 0.9512 | 0.957 |
| **Red Neuronal (MLP)** | Capas (100,50), activación tanh, α=0.0001 | **0.9630** | **0.991** |

**Justificación de selección:**
- **KNN:** Adecuado para espacios con atributos escalados, pero sensible a dimensionalidad
- **Árboles de Decisión:** Robustos a distribuciones extremas, interpretables, manejan multicolinealidad
- **MLP:** Capaz de aprender fronteras no lineales complejas, mejor rendimiento global

### Fase 4: Evaluación

#### Matrices de Confusión

| Modelo | Falsos Positivos | Falsos Negativos | Observación |
|--------|------------------|------------------|-------------|
| KNN | 2 | 5 | Sensibilidad limitada |
| Árbol de Decisión | 1 | 3 | Mejora sustancial |
| **Red Neuronal (MLP)** | **0** | **3** | **Óptimo: 0 falsos positivos** |

**Conclusión de evaluación:**
1. Árbol de Decisión y MLP reducen falsos negativos a 3 casos
2. MLP demuestra superioridad con 0 falsos positivos
3. Modelo seleccionado para despliegue: **Perceptrón Multicapa (MLP)**

### Fase 5: Despliegue (Teórico)

#### Consideraciones para Producción
- **Infraestructura:** APIs (FastAPI), interfaces (Gradio/Streamlit)
- **Modularización:** Migración de notebooks (.ipynb) a scripts (.py)
- **Pipelines:** Automatización del flujo de datos
- **Monitoreo:** Detección de *concept drift* y *prediction bias*

#### Stack Tecnológico Recomendado
| Función | Herramientas |
|--------|--------------|
| Interfaz de Usuario | Gradio, Streamlit, Power BI |
| Exposición (API) | FastAPI, AWS Lambda |
| Inferencia | Hugging Face Inference Endpoints |
| Mantenimiento | Evidently AI, MLflow, Neptune.ai |
| Contenedores | Docker |

#### Criterios de Éxito
- Reproducibilidad (`requirements.txt`, versionado de datos)
- Documentación exhaustiva (asunciones, riesgos, interpretación)
- Auditoría ética (sesgos, equidad en diagnósticos)

---

## 📈 Principales Hallazgos

1. **Geometría celular como predictor:** El perímetro y puntos cóncavos son las variables más discriminantes.
2. **Multicolinealidad informativa:** Variables correlacionadas pueden aportar información sutil beneficiosa (especialmente en KNN).
3. **Importancia del preprocesamiento:** La estandarización es crítica para modelos basados en distancia.
4. **Superioridad de redes neuronales:** MLP logra el mejor equilibrio sensibilidad-especificidad.
5. **Contexto clínico guía la evaluación:** Los falsos negativos son la métrica prioritaria.

---

## 🛠️ Tecnologías Utilizadas

- **Lenguaje:** Python
- **Librerías principales:** pandas, numpy, matplotlib, seaborn, scikit-learn
- **Técnicas:**
  - EDA: histogramas, boxplots, pair plots, mapas de calor
  - Preprocesamiento: StandardScaler, Label Encoding
  - Modelado: GridSearchCV, KNN, Decision Trees, MLP
  - Evaluación: matrices de confusión, curvas ROC, F1-Score

---

## 📚 Bibliografía Seleccionada

- Han, J., Kamber, M., & Pei, J. (2012). *Data Mining: Concepts and Techniques*.
- Sculley, D., et al. (2015). *Hidden Technical Debt in Machine Learning Systems*.
- Wickham, H. (2014). *Tidy Data*. Journal of Statistical Software.
- Wirth, R., & Hipp, J. (2000). *CRISP-DM: Towards a Standard Process Model for Data Mining*.
- Tukey, J. W. (1977). *Exploratory Data Analysis*.

# Preprocesamiento de Datos de Cáncer de Mama

## Descripción
Este proyecto tiene como objetivo realizar un preprocesamiento completo de un conjunto de datos de cáncer de mama (`Cancer_Data.csv`). El preprocesamiento es un paso fundamental antes de aplicar cualquier modelo de machine learning, y en este notebook se llevan a cabo varias etapas clave para limpiar y preparar los datos.

## Archivos Incluidos
- `Presentación_MD_codificación.ipynb`: **Notebook principal que contiene todo el código de preprocesamiento.**


## Pasos del Preprocesamiento
El notebook incluye los siguientes pasos:
1.  **Carga de Datos:** Importación de las bibliotecas necesarias y carga del archivo CSV en un DataFrame de pandas.
2.  **Verificación y Manejo de Valores Nulos:** Se inspecciona el DataFrame en busca de valores faltantes y se elimina la columna `Unnamed: 32`, que contenía todos los valores nulos.
3.  **Verificación y Manejo de Registros Duplicados:** Se comprueba la existencia de filas duplicadas, confirmando que no hay ninguna en el conjunto de datos.
4.  **Verificación de Tipos de Datos e Inconsistencias:** Se analizan los tipos de datos de las columnas, confirmando que las variables categóricas (`diagnosis`) y numéricas son correctas.
5.  **Visualización de Distribuciones:** Se generan histogramas para cada columna numérica. Esto permite observar la distribución de los datos e identificar posibles valores atípicos de manera visual. Además, se muestran los datos de los histogramas en formato tabular para un análisis más detallado.

## Resultados Clave
- Se eliminó correctamente la columna `Unnamed: 32` que contenía solo valores nulos.
- Se confirmó que no existen filas duplicadas en el conjunto de datos.
- La columna `diagnosis` es de tipo objeto con dos valores únicos ('M' para maligno y 'B' para benigno), lo que la hace apta para codificación en el futuro.
- Se generaron histogramas y tablas de frecuencias para todas las variables numéricas, lo que permite una comprensión inicial de su distribución.

## Requisitos
- Python 3.x
- pandas
- numpy
- matplotlib
- seaborn

Puedes instalar las dependencias con el siguiente comando:
```bash
pip install pandas numpy matplotlib seaborn