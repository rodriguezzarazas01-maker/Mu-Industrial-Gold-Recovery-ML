# Mu | Predicción de Recuperación de Oro con Machine Learning

## Descripción general

La industria minera requiere procesos eficientes para maximizar la recuperación de metales valiosos y reducir pérdidas operativas. Las variaciones en la composición del mineral, las condiciones de flotación y los parámetros de purificación pueden afectar considerablemente la cantidad de oro recuperado.

Este proyecto desarrolla un modelo de Machine Learning capaz de predecir la recuperación de oro en dos etapas fundamentales del proceso industrial:

- Recuperación del concentrado rougher.
- Recuperación final después de las etapas de purificación.

El análisis utiliza información histórica relacionada con la alimentación del mineral, los reactivos empleados, las condiciones de las plantas de flotación y el estado de las diferentes etapas del proceso.

La solución busca proporcionar una herramienta de apoyo para anticipar el rendimiento de la producción, identificar condiciones operativas poco eficientes y contribuir a la optimización del proceso de extracción de oro.

---

## Problema de negocio

Zyfra desarrolla soluciones tecnológicas orientadas a mejorar la eficiencia de procesos industriales en sectores como la minería y la metalurgia.

La empresa necesita predecir la cantidad de oro recuperada durante el procesamiento del mineral. Estas estimaciones pueden ayudar a:

- Evaluar la eficiencia de las condiciones operativas.
- Identificar parámetros de producción poco rentables.
- Reducir pérdidas de metales valiosos.
- Optimizar el uso de recursos y reactivos.
- Apoyar la toma de decisiones basada en datos.

El problema se aborda como una tarea de regresión con dos variables objetivo:

- `rougher.output.recovery`
- `final.output.recovery`

---

## Objetivo general

Desarrollar y evaluar un modelo de Machine Learning capaz de predecir la recuperación de oro durante las etapas rougher y final del proceso de extracción y purificación.

---

## Objetivos específicos

- Examinar la estructura y calidad de los datos disponibles.
- Verificar la consistencia del cálculo de recuperación rougher.
- Identificar diferencias entre los conjuntos de entrenamiento y prueba.
- Analizar las características que no están disponibles al momento de realizar predicciones.
- Tratar valores ausentes respetando la estructura temporal de los datos.
- Analizar la evolución de las concentraciones de oro, plata y plomo.
- Comparar las distribuciones del tamaño de las partículas de alimentación.
- Identificar y eliminar concentraciones totales anómalas.
- Implementar las métricas sMAPE y sMAPE final ponderado.
- Entrenar y comparar diferentes algoritmos de regresión.
- Seleccionar el modelo con mejor desempeño mediante validación cruzada.
- Evaluar el modelo seleccionado sobre un conjunto de prueba.
- Comparar el resultado final con un modelo de referencia.

---

## Datos utilizados

El proyecto utiliza tres conjuntos de datos:

- `gold_recovery_train.csv`: datos de entrenamiento.
- `gold_recovery_test.csv`: datos de prueba.
- `gold_recovery_full.csv`: dataset fuente con toda la información disponible.

La columna `date` registra la fecha y hora de adquisición de cada observación.

Los nombres de las variables siguen esta estructura:

```text
[stage].[parameter_type].[parameter_name]
```

### Etapas del proceso

- `rougher`: flotación.
- `primary_cleaner`: purificación primaria.
- `secondary_cleaner`: purificación secundaria.
- `final`: resultado final del proceso.

### Tipos de parámetros

- `input`: variables de entrada.
- `output`: resultados obtenidos después de una etapa.
- `state`: estado actual de los equipos o del proceso.
- `calculation`: variables obtenidas mediante cálculos posteriores.

---

## Proceso industrial analizado

### 1. Alimentación inicial

El mineral extraído es acondicionado antes de ingresar al proceso de flotación. En esta etapa se registran las concentraciones iniciales de los diferentes metales y las propiedades físicas de la alimentación.

### 2. Flotación rougher

La alimentación ingresa a las instalaciones de flotación, donde se utilizan diferentes reactivos para separar las partículas valiosas del material residual.

Como resultado se obtienen:

- Concentrado rougher.
- Colas rougher.

### 3. Purificación primaria

El concentrado rougher se somete a una primera etapa de limpieza para incrementar la concentración de oro y disminuir la presencia de impurezas.

### 4. Purificación secundaria

El material pasa por una segunda etapa de limpieza antes de convertirse en el concentrado final.

### 5. Concentrado final

Representa el producto obtenido después de completar las etapas de flotación y purificación.

---

## Metodología

El proyecto se desarrolló en cuatro fases principales.

### 1. Preparación de datos

- Carga e inspección de los datasets.
- Revisión de dimensiones y tipos de datos.
- Análisis de valores ausentes.
- Verificación de duplicados.
- Conversión de `date` a formato temporal.
- Validación de la fórmula de recuperación.
- Identificación de características ausentes en prueba.
- Separación de características y variables objetivo.
- Recuperación de los objetivos reales del conjunto de prueba.
- Tratamiento temporal de valores ausentes.
- Validación de compatibilidad entre entrenamiento y prueba.

### 2. Análisis exploratorio

- Evolución de las concentraciones de `Au`, `Ag` y `Pb`.
- Comparación del tamaño de las partículas de alimentación.
- Análisis de concentraciones totales.
- Identificación de concentraciones iguales o cercanas a cero.
- Eliminación coordinada de observaciones anómalas.

### 3. Modelado

- Implementación de la métrica sMAPE.
- Implementación del sMAPE final ponderado.
- Validación de las funciones mediante casos controlados.
- Evaluación mediante validación cruzada.
- Entrenamiento de regresión lineal.
- Entrenamiento y ajuste de bosque aleatorio.
- Selección del modelo con menor error promedio.

### 4. Evaluación final

- Entrenamiento del modelo seleccionado con todos los datos disponibles.
- Predicción sobre el conjunto de prueba.
- Evaluación individual de ambos objetivos.
- Cálculo del sMAPE ponderado.
- Comparación con un modelo de referencia.

---

## Validación de la fórmula de recuperación

La recuperación rougher se calculó utilizando:

\[
Recovery =
\frac{C(F-T)}
{F(C-T)}
\times 100
\]

Donde:

- **C** representa la concentración de oro en el concentrado.
- **F** representa la concentración de oro en la alimentación.
- **T** representa la concentración de oro en las colas.

El Error Absoluto Medio entre la recuperación calculada y los valores registrados en `rougher.output.recovery` fue:

```text
9.30 × 10⁻¹⁵
```

Este resultado es prácticamente igual a cero y confirma que los valores de recuperación rougher fueron calculados correctamente.

---

## Características ausentes en prueba

Se identificaron 34 variables presentes en entrenamiento, pero ausentes en prueba:

- 30 parámetros de tipo `output`.
- 4 parámetros de tipo `calculation`.
- 0 parámetros de tipo `input`.
- 0 parámetros de tipo `state`.

Estas características representan resultados o cálculos obtenidos durante o después del proceso. Por esta razón, no se utilizaron como variables predictoras.

Esta decisión evita entrenar los modelos con información que no estaría disponible al momento de realizar nuevas predicciones.

---

## Tratamiento de valores ausentes

Las observaciones sin alguno de los dos objetivos reales fueron eliminadas, debido a que no podían utilizarse correctamente para el aprendizaje supervisado.

Los valores ausentes en las características predictoras fueron tratados mediante relleno hacia adelante:

```python
ffill()
```

Esta estrategia fue seleccionada porque:

- Los registros se encuentran ordenados cronológicamente.
- Los parámetros cercanos en el tiempo suelen ser similares.
- Permite conservar observaciones sin introducir una media global.
- Utiliza únicamente el último valor válido conocido.

El relleno se aplicó de manera independiente sobre los conjuntos de entrenamiento y prueba.

---

## Evolución de las concentraciones de metales

Se analizaron las concentraciones medias de plata, plomo y oro en cuatro etapas:

1. Alimentación inicial.
2. Concentrado rougher.
3. Purificación primaria.
4. Concentrado final.

### Plata

| Etapa | Concentración media |
|---|---:|
| Alimentación inicial | 8.04 |
| Concentrado rougher | 10.85 |
| Purificación primaria | 7.67 |
| Concentrado final | 4.77 |

La concentración de plata aumenta después de la flotación, pero disminuye durante las etapas posteriores de purificación.

### Plomo

| Etapa | Concentración media |
|---|---:|
| Alimentación inicial | 3.28 |
| Concentrado rougher | 6.89 |
| Purificación primaria | 8.81 |
| Concentrado final | 9.07 |

La concentración de plomo aumenta progresivamente durante el proceso.

### Oro

| Etapa | Concentración media |
|---|---:|
| Alimentación inicial | 7.55 |
| Concentrado rougher | 17.83 |
| Purificación primaria | 29.12 |
| Concentrado final | 39.89 |

La concentración de oro presenta un crecimiento continuo y pronunciado. Este comportamiento evidencia que las etapas de flotación y purificación enriquecen correctamente el producto en oro.

> Las unidades específicas de concentración no se encuentran documentadas en los datos suministrados. Por esta razón, los resultados se presentan como unidades de concentración del dataset.

---

## Distribución del tamaño de las partículas

Se comparó la distribución de `rougher.input.feed_size` entre entrenamiento y prueba.

### Entrenamiento

- Media: 60.24
- Mediana: 55.27
- Desviación estándar: 23.01

### Prueba

- Media: 55.90
- Mediana: 49.96
- Desviación estándar: 22.71

La distribución de prueba está ligeramente desplazada hacia tamaños menores. Sin embargo, ambos conjuntos presentan formas generales, rangos y niveles de dispersión semejantes.

No se identificaron diferencias suficientemente pronunciadas como para considerar que la evaluación posterior del modelo dejaría de ser representativa.

---

## Análisis de concentraciones totales

Las concentraciones totales se calcularon mediante la suma de:

- Plata.
- Plomo.
- Oro.
- Otros sólidos.

El análisis se realizó en:

- Alimentación inicial.
- Concentrado rougher.
- Concentrado final.

Las distribuciones mostraron acumulaciones de observaciones iguales o cercanas a cero.

### Anomalías identificadas

| Etapa | Observaciones ≤ 1 | Porcentaje |
|---|---:|---:|
| Alimentación inicial | 1.966 | 8.65% |
| Concentrado rougher | 2.464 | 10.85% |
| Concentrado final | 2.082 | 9.17% |

Se identificaron **2.814 observaciones únicas** con una concentración total menor o igual a una unidad en al menos una etapa. Estas observaciones representaron el **12.39% del dataset fuente**.

Los registros fueron eliminados de forma coordinada de las características y los objetivos, ya que podían representar:

- Mediciones anómalas.
- Períodos de inactividad.
- Condiciones operativas poco representativas.
- Registros que podían distorsionar el entrenamiento.

Después del tratamiento de anomalías, los datos destinados al modelado quedaron conformados por:

- 13.789 observaciones de entrenamiento.
- 5.154 observaciones de prueba.
- 52 características predictoras.

---

## Métrica de evaluación

El proyecto utiliza el error porcentual absoluto medio simétrico:

\[
sMAPE =
\frac{1}{N}
\sum_{i=1}^{N}
\frac{|y_i-\hat{y}_i|}
{(|y_i|+|\hat{y}_i|)/2}
\times 100
\]

La métrica final combina el error de ambos objetivos:

\[
sMAPE_{final}
=
0.25 \times sMAPE_{rougher}
+
0.75 \times sMAPE_{final}
\]

La recuperación rougher aporta el 25% de la métrica, mientras que la recuperación final representa el 75%.

---

## Modelos evaluados

Se entrenaron y compararon:

- Regresión lineal.
- Bosque aleatorio.

Los modelos fueron evaluados mediante validación cruzada de cinco bloques, manteniendo el orden de las observaciones.

### Resultados de validación

| Modelo | sMAPE promedio |
|---|---:|
| Bosque aleatorio | 8.77% |
| Regresión lineal | 10.01% |

El bosque aleatorio obtuvo el menor error promedio.

### Mejores hiperparámetros

- Número de árboles: `150`
- Profundidad máxima: `3`
- Semilla aleatoria: `12345`

Las configuraciones con árboles más profundos produjeron errores superiores. Esto indica que aumentar la complejidad del modelo no mejoró su capacidad de generalización.

---

## Resultados finales

El bosque aleatorio seleccionado obtuvo los siguientes resultados sobre el conjunto de prueba:

| Métrica | Resultado |
|---|---:|
| sMAPE rougher | 4.99% |
| sMAPE recuperación final | 7.74% |
| sMAPE ponderado final | 7.06% |
| sMAPE del modelo de referencia | 7.77% |

El modelo presentó una mayor precisión al predecir la recuperación rougher.

La recuperación final obtuvo un error superior y, debido a su ponderación del 75%, tuvo una mayor influencia sobre el resultado global.

El bosque aleatorio superó al modelo de referencia por aproximadamente **0.71 puntos porcentuales**, lo que representa una reducción relativa del error cercana al **9.2%**.

---

## Conclusión ejecutiva

El bosque aleatorio fue el modelo con mejor desempeño entre los algoritmos evaluados, alcanzando un sMAPE ponderado final de **7.06%** sobre el conjunto de prueba.

El análisis también confirmó que el proceso industrial concentra progresivamente el oro, pasando de una concentración media de **7.55** en la alimentación inicial a **39.89** en el concentrado final.

La solución desarrollada puede utilizarse como herramienta de apoyo para:

- Anticipar el rendimiento del proceso.
- Identificar condiciones operativas menos eficientes.
- Detectar configuraciones con menor recuperación.
- Apoyar la optimización de la producción.
- Reducir parámetros operativos poco rentables.

Aunque el modelo supera la estrategia de referencia, la mejora es moderada. Por esta razón, existe margen para continuar optimizando su desempeño, especialmente en la predicción de la recuperación final.

---

## Tecnologías utilizadas

- Python
- Pandas
- NumPy
- Matplotlib
- Seaborn
- Scikit-Learn
- Google Colab
- Git
- GitHub

---

## Estructura sugerida del repositorio

```text
Mu-Gold-Recovery-Prediction/
│
├── README.md
├── notebooks/
│   └── mu_gold_recovery_prediction.ipynb
│
├── data/
│   └── README.md
│
├── images/
│   ├── metal_concentration_evolution.png
│   ├── particle_size_distribution.png
│   └── total_concentration_distributions.png
│
├── requirements.txt
└── .gitignore
```

> Los datasets originales no deben incluirse en el repositorio si existen restricciones de distribución, privacidad o tamaño. La carpeta `data/` puede contener únicamente un archivo explicativo sobre el origen y la estructura esperada de los datos.

---

## 
