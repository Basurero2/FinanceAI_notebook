# Auditoría Técnica al Milímetro: EDA y Entrenamiento (FinanceAI)

**Notebook auditado:** [`hackathon_dataset_avanzado_31.07.2026.ipynb`](file:///c:/Users/ESTUDIO/Desktop/antigravity/FinanceAI/notebooks/hackathon_dataset_avanzado_31.07.2026.ipynb)  
**Fecha de evaluación:** Agosto 2026  
**Objetivo:** Proporcionar un desglose exhaustivo de los resultados de entrenamiento, sus causas técnicas a nivel de código, datos, estructura, hiperparámetros y arquitectura de modelos, para la posterior documentación formal e interpretación de gráficos.

---

## 1. Resumen Ejecutivo de la Auditoría

El notebook implementa un flujo completo desde la ingesta, ingeniería de variables, exploración (EDA) hasta el desarrollo de **dos modelos independientes de Machine Learning** y un módulo híbrido de búsqueda diferida (Fuzzy Matching + NLP Baseline).

### Resumen de Modelos Evaluados

| Modelo | Tarea / Objetivo | Algoritmos Probados | Muestras (Train / Test) | Métrica Macro F1 | Resultado / Diagnóstico Principal |
| :--- | :--- | :--- | :--- | :--- | :--- |
| **Módulo Híbrido** | Fuzzy Match Comercio | RapidFuzz + TF-IDF + Regresión Logística | Catalog: 800 comercios | N/A (Threshold ≥90) | Búsqueda por catálogo + Fallback a modelo predictivo si confianza <60. |
| **Modelo A (NLP)** | Clasificación de Categoría de Gasto (`categoria_gasto`) | Regresión Logística, Linear SVC, Random Forest | Train: 105,172 <br> Test: 26,293 | **1.0000** (100%) | **Score Perfecto (1.00)**. Causa: Nombres sintéticos deterministas no solapados en vocabulario TF-IDF. |
| **Modelo B (Perfil)** | Clasificación de Perfil Financiero (`perfil_financiero`) | Random Forest + RandomizedSearchCV | Train: 2,400 <br> Test: 600 | **1.0000** (100%) | **Score Perfecto (1.00)**. Causa: **Data Leakage Target Directo**. Las variables de entrada determinan por fórmula exacta la etiqueta. |

---

## 2. Auditoría del Preprocesamiento y EDA (Contexto de Datos)

### 2.1 Enriquecimiento y Balanceo Sintético de Transacciones
* **Limpieza inicial:** Ingesta de `transactions.csv` (125,465 filas iniciales).
* **Inyección sintética (Celda 8):** Se generaron sintéticamente **6,000 transacciones adicionales** para cubrir categorías omitidas en la muestra base (`Educación` y `Vivienda`).
* **Transformación numérica (Celda 14):** `monto_transaccion = monto_transaccion / 1.3`.
* **Merge final EDA (Celda 49):** `df_final` resultante de 131,465 filas x 18 columnas.

### 2.2 Creación de Variables Clave en Clientes
* `gasto_total`: Suma de `monto_transaccion` agrupada por `user_id`.
* `nivel_endeudamiento`: `gasto_total / (ingreso_mensual + linea_credito)` redondeado a 2 decimales.
* `rango_ahorro`: `clip(ingreso_mensual - gasto_total, lower=0) / ingreso_mensual` redondeado a 2 o 4 decimales.
* `perfil_financiero` (Definición de Etiqueta / Target en Celda 46):
  * `deuda <= 0.50` AND `ahorro >= 0.20` $\rightarrow$ **`SALUDABLE`**
  * `deuda <= 0.80` AND `ahorro >= 0.10` $\rightarrow$ **`MODERADO`**
  * Caso contrario $\rightarrow$ **`RIESGOSO`**

---

## 3. Auditoría Exhaustiva de Entrenamiento: Modelo A (Categorización NLP)

### 3.1 Especificación Técnica por Código y Datos
* **Objetivo de Negocio:** Clasificar el tipo de gasto (`alimentacion`, `compras`, `educacion`, `entretenimiento`, `salud`, `servicios`, `transporte`, `vivienda`) dado el comercio y monto.
* **Estructura de Entradas:**
  * Categorical/Text: `nombre_comercio` $\rightarrow$ `TfidfVectorizer(token_pattern=r"(?u)\b\w+\b", max_features=5000, min_df=2)`
  * Numerical: `monto_transaccion` $\rightarrow$ `StandardScaler()`
* **Estrategia de Split:**
  * `train_test_split` del 80% train / 20% test con `random_state=42` y estratificación (`stratify=y`).
  * Volumen: Train = 105,172 muestras | Test = 26,293 muestras.

### 3.2 Resultados y Comparativa de Algoritmos (Celda 79)

```text
               Modelo  Acc Train  Acc Test  Precision Macro  Recall Macro  F1-Score Macro
0 Logistic Regression        1.0       1.0              1.0           1.0             1.0
1          Linear SVC        1.0       1.0              1.0           1.0             1.0
2       Random Forest        1.0       1.0              1.0           1.0             1.0
```

* **Modelo Ganador Seleccionado:** `Logistic Regression` (C=1.0, max_iter=1000).
* **Desglose por clase en Test (Support = 26,293):**
  * `alimentacion` (6,042 muestras): Precision 1.00, Recall 1.00, F1 1.00
  * `compras` (1,986 muestras): Precision 1.00, Recall 1.00, F1 1.00
  * `educacion` (1,200 muestras): Precision 1.00, Recall 1.00, F1 1.00
  * `entretenimiento` (3,953 muestras): Precision 1.00, Recall 1.00, F1 1.00
  * `salud` (1,975 muestras): Precision 1.00, Recall 1.00, F1 1.00
  * `servicios` (3,982 muestras): Precision 1.00, Recall 1.00, F1 1.00
  * `transporte` (5,955 muestras): Precision 1.00, Recall 1.00, F1 1.00
  * `vivienda` (1,200 muestras): Precision 1.00, Recall 1.00, F1 1.00

### 3.3 Análsis Causativo de Exactitud (¿Por qué 1.0000?)
1. **Separabilidad Vocabular Exagerada (Causa en Datos Sintéticos):** Los textos de los comercios en el dataset sintético (ej. "soriana", "uber", "gandhi", "farmacias del ahorro") no comparten tokens ni entropía léxica entre categorías distintas.
2. **Cero Ruido de Etiquetado:** Cada token mapea biyectivamente a una única categoría de gasto. TF-IDF genera vectores totalmente ortogonales en el espacio de características.

---

## 4. Auditoría Exhaustiva de Entrenamiento: Modelo B (Perfil Financiero)

### 4.1 Especificación Técnica por Código y Datos
* **Objetivo de Negocio:** Asignar el nivel de riesgo/perfil del usuario (`SALUDABLE`, `MODERADO`, `RIESGOSO`).
* **Variables Utilizadas (9 características de Back-End):**
  * Numéricas (7): `edad`, `numero_hijos`, `empleo_formal`, `ingreso_mensual`, `linea_credito`, `nivel_endeudamiento`, `rango_ahorro`.
  * Categóricas (2): `sexo`, `estado_civil` (One-Hot Encoded con `handle_unknown='ignore'`).
* **Preprocesamiento (`ColumnTransformer`):**
  * `StandardScaler` sobre numéricas.
  * `OneHotEncoder` sobre categóricas.
* **Estrategia de Split:**
  * Train: 2,400 usuarios (80%) | Test: 600 usuarios (20%) con `stratify=y` y `random_state=42`.

### 4.2 Optimización de Hiperparámetros (Celda 82)
* **Búsqueda:** `RandomizedSearchCV` con 15 iteraciones y 5-Fold Cross Validation (`cv=5`), optimizando `scoring='f1_macro'`.
* **Hiperparámetros Óptimos Seleccionados:**
  * `classifier__n_estimators`: **300**
  * `classifier__max_depth`: **20**
  * `classifier__min_samples_split`: **5**
  * `classifier__min_samples_leaf`: **4**
  * `classifier__max_features`: **'log2'**

### 4.3 Resultados del Modelo B (Celda 82)

```text
Accuracy: 1.0000 | F1-Score Macro: 1.0000 | Precision Macro: 1.0000 | Recall Macro: 1.0000

Classification Report:
              precision    recall  f1-score   support
    MODERADO       1.00      1.00      1.00        80
    RIESGOSO       1.00      1.00      1.00       350
   SALUDABLE       1.00      1.00      1.00       170
    accuracy                           1.00       600
```

### 4.4 Interpretabilidad SHAP e Importancia de Variables (Celdas 83 y 86)
* **Importancia de Variables (Random Forest Gini Impurity):** `nivel_endeudamiento` y `rango_ahorro` absorben prácticamente el 100% del peso en la toma de decisión del bosque.
* **Impacto SHAP (`TreeExplainer`):** Muestra que los cortes de decisión en los árboles de decisión replicaron de forma perfecta las fronteras lineales:
  * `nivel_endeudamiento <= 0.50` y `rango_ahorro >= 0.20` $\rightarrow$ SALUDABLE.
  * `nivel_endeudamiento <= 0.80` y `rango_ahorro >= 0.10` $\rightarrow$ MODERADO.

### 4.5 Análisis Causativo de Exactitud (¿Por qué 1.0000?) -> **Target Leakage Matemático**
1. **Fórmulas Deterministas Directas:** En la Celda 46, la columna objetivo `perfil_financiero` fue creada usando una función regla estricta sobre `nivel_endeudamiento` y `rango_ahorro`.
2. **Inclusión de Variables Generadoras en el Pipeline de Entrenamiento:** Al incluir `nivel_endeudamiento` y `rango_ahorro` dentro de la matriz $X$ de características para entrenar el clasificador, el modelo de Machine Learning simplemente aprendió una regla condicional determinista. No existe varianza ni ruido residual.

---

## 5. Resumen de Puntos Clave para la Documentación Final y Gráficos

1. **Para los Gráficos de matrices de confusión (Celdas 79 y 82):**
   * Ambas matrices son completamente diagonales (cero falsos positivos y cero falsos negativos).
2. **Para la Documentación del Modelo A (NLP):**
   * Documentar que la Regresión Logística con TF-IDF funciona de forma óptima para nombres de comercios estandarizados, pero recomendar incorporar data augmentation con variaciones ortográficas en producción.
3. **Para la Documentación del Modelo B (Perfil):**
   * Explicar el fenómeno de **Data Leakage / Target Leakage** derivado de reglas de negocio deterministas. Explicar que el modelo replica exactamente la lógica actuarial programada.
