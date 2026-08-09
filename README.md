
# Propuesta para el equipo Data Scientist - Modelo B (Perfil financiero)

## Comparativa de dataset clientes actual y propuesto

### Variables

#### dataset actual



| user_id | edad | sexo | estado_civil | numero_hijos | empleo_formal | ingreso_mensual | linea_credito | gasto_total | nivel_endeudamiento | rango_ahorro | perfil_financiero |
| :---: | :---: | :---: | :---: | :---: | :---: | :---: | :---: | :---: | :---: | :---: | :---: |
| 1 | 58 | m | soltero | 2 | 0 | 37256 | 30000 | 50944.338462 | 0.76 | 0.0000 | RIESGOSO |


#### dataset propuesto

| user_id | edad | sexo | estado_civil | numero_hijos | empleo_formal | ingreso_mensual | linea_credito | gasto_total | credito_utilizado | monto_promedio_ahorro | ratio_gasto_ingreso | pct_credito_ocupado | tasa_ahorro_real | perfil_financiero |
| :---: | :---: | :---: | :---: | :---: | :---: | :---: | :---: | :---: | :---: | :---: | :---: | :---: | :---: | :---: |
| 1 | 58 | m | soltero | 2 | 0 | 37256 | 30000 | 51264.62 | 24132 | 1117.68 | 1.3760 | 0.8044 | 0.03 | RIESGOSO |




#### variables actualizadas

- perfil_financiero :


  **dataset actual** : Se asignaba de forma rígida mediante condicionales estáticos de IF/ELSE (lo que provocaba que el 58.33% fuera clasificado como RIESGOSO sintéticamente).

   recategorización masiva a "RIESGOSO" debido a un "redondeo" a 0 si el ```gasto_total``` era mayor a su ```ingreso_mensual``` (omitiendo otras variables para el analisis) logrando que cayera en la condición ```IF rango_ahorro <= 0.10 THEN RIESGOSO```




  <img width="291" height="320" alt="image" src="https://github.com/user-attachments/assets/62c33277-b829-48fe-af25-42829d9d3490" />




  <img width="314" height="320" alt="image" src="https://github.com/user-attachments/assets/e08d28e7-153d-4838-b29a-e8cc301cb651" />




   distribucion de las categorias de perfil_financiero

    
  <img width="553" height="434" alt="image" src="https://github.com/user-attachments/assets/b4d5a30d-3d51-43c9-957f-c486e8bc59f7" />




   **dataset propuesto** : Se recalculó mediante la segmentación no supervisada K-Means ($k=3$) considerando simultáneamente ingresos, gastos, deudas en tarjeta y ahorros reales, generando una distribución equilibrada (SALUDABLE: 45.57%, MODERADO: 23.63%, RIESGOSO: 30.80%).



   distribucion de las categorias de perfil_financiero



  <img width="488" height="376" alt="image" src="https://github.com/user-attachments/assets/15b99e0e-cc48-401d-a096-0832c88552c4" />


<br>
<br>


**variables agregadas**
- credito_utilizado (FLOAT) : Recuperada de ```users.csv```. Representa la deuda activa real en la tarjeta o línea de crédito.
- monto_promedio_ahorro (FLOAT) : Recuperada de ```users.csv```. Representa el saldo real acumulado en las cuentas de ahorro del cliente.
- ratio_gasto_ingreso (FLOAT) : Ratio nuevo de flujo. Se calcula como ```gasto_total/ingreso_mensual```. Reemplazó al antiguo nivel_endeudamiento
- pct_credito_ocupado (FLOAT) : Ratio nuevo de apalancamiento. Se calcula como ```credito_utilizado/linea_credito```. Mide qué tan saturada está la tarjeta de crédito.
- tasa_ahorro_real (FLOAT) :Ratio nuevo de reserva. Se calcula como ```monto_promedio_ahorro/ingreso_mensual```. Reemplazó al antiguo rango_ahorro

**variables eliminadas**
- nivel_endeudamiento : Usaba una fórmula distorsionada que sumaba la línea de crédito al ingreso en el denominador ```gasto_total/(ingreso_mensual + linea_credito)```.
- rango_ahorro : Truncaba los valores negativos a cero con ```.clip(lower=0)``` cuando el gasto superaba al ingreso, colapsando al 52.1% de la población a 0.0.


### variables mas importantes

**datatset actual**

<br>
 
  <img width="847" height="454" alt="image" src="https://github.com/user-attachments/assets/0cee5a98-81f9-47f9-937d-afc9fb5c27bc" />

<br>
<br>

**dataset propuesto**

   variables mas importantes

<br>

  <img width="1019" height="556" alt="image" src="https://github.com/user-attachments/assets/9bbe6597-ccbc-4e7f-9f16-732a39193e93" />

<br>
<br>


## Comparativa entre modelo actual y propuesto (perfil financiero)

Haciendo una prueba para comprobar que el modelo actual de perfil financiero presenta Data Leakage o Overfitting MEMORÍSTICO debido a una sesgo de categorización sintética por reglas estáticas (IF/ELSE) en el dataset clientes

### Causas y evidencias
- mas información en el archivo [auditoria_entrenamiento_notebook.md](auditoria_entrenamiento_notebook.md")

#### modelo actual

- precisión del 100% perfecto

<img width="936" height="227" alt="image" src="https://github.com/user-attachments/assets/7d276f15-ccc6-4983-8e6e-1c874a619fb9" />

<br>
<br>

- matriz de confusión diagonal

<br>

<img width="664" height="556" alt="image" src="https://github.com/user-attachments/assets/2394b142-d185-4365-ae64-9a1a9126fe63" />


#### modelo propuesto

- precision 96.33%

<br>

<img width="940" height="232" alt="image" src="https://github.com/user-attachments/assets/4e9a5ba3-2352-423f-bdb9-1c128fb225a8" />

<br>
<br>

- matriz de confusión no diagonal

<br>

<img width="742" height="590" alt="image" src="https://github.com/user-attachments/assets/af6910da-baee-449a-810c-456c69ba98ba" />






### Pruebas de prediccion
#### modelo actual
- usando datos procesados

<br>

<img width="546" height="411" alt="image" src="https://github.com/user-attachments/assets/de7c9905-7da3-4af4-b73e-9e7115cfd28f" />



#### modelo propuesto
- usando datos crudos

<br>

<img width="636" height="473" alt="image" src="https://github.com/user-attachments/assets/c7240524-0008-4722-8862-44f4dfe63693" />

<br>
<br>

- usando datos procesados

<br>

<img width="409" height="417" alt="image" src="https://github.com/user-attachments/assets/a79887de-6432-4ace-8bca-465e45cc0067" />


#### Resultado
- El modelo actual dio un resultado de RIESGOSO :

```
 ¿Por qué falló el Modelo?
 Como la transformación previa aplicaba la regla estricta (25,000 - 26,000).clip(lower=0), el rango_ahorro dio 0.0.
 El modelo anterior, al haber memorizado la regla IF rango_ahorro <= 0.10 THEN RIESGOSO, calificó al cliente con 100% de riesgo con ceguera total, ignorando que el cliente tenía  S/. 80k de línea y casi nada de deuda.
```

- El modelo propuesto dio un resultado de SALUDABLE con precision 57.5% :

```
¿Por qué el Modelo acertó?
El modelo actual calculó automáticamente los ratios multidimensionales:
Detectó que la deuda en la tarjeta es bajísima (pct_credito_ocupado = 3.7%).
Rescató que el cliente posee un fondo de ahorro real (tasa_ahorro_real = 24%).
Entendió que aunque el gasto del mes fue S/. 26k, el cliente es solvente y tiene respaldo patrimonial, clasificándolo como SALUDABLE.
```

### NOTA
Estas capturas del modelo propuesto son el resultado de una combinacion aleatoria de hiperparametros, por lo que al ejecutar el notebook obtegamos diferentes score en el entrenamiento y prueba en cada sesion, para ello se debe ajustar (Fine-Tuning) que hiperparametros usar mediante varias pruebas y cual es la mejor combinacion para usar


- aqui se muestran la combinacion aleatoria entre diferentes valores para el modelo propuesto

<br>

<img width="420" height="152" alt="image" src="https://github.com/user-attachments/assets/1f2b770e-64a8-49fd-b7bc-ba294d337095"/>

<br>
<br>

- una vez se haya probado e identificado los hiperparametros para el modelo utilizamos ```RandomForestClassifier``` por ejemplo este script en el punto 4 se especifican los hiperparametros

```python
import pandas as pd
import numpy as np
import joblib
import matplotlib.pyplot as plt
import seaborn as sns
from sklearn.model_selection import train_test_split
from sklearn.ensemble import RandomForestClassifier
from sklearn.preprocessing import StandardScaler, OneHotEncoder
from sklearn.compose import ColumnTransformer
from sklearn.pipeline import Pipeline
from sklearn.metrics import (
    classification_report,
    confusion_matrix,
    accuracy_score,
    f1_score,
    precision_score,
    recall_score
)

# 0. Fijar semilla global para reproducibilidad
np.random.seed(42)

# ==========================================
# 1. CARGA DEL DATASET UNIFICADO
# ==========================================
clientes = pd.read_csv('users_final_kmeans.csv')

# ==========================================
# 2. DEFINICIÓN DE COLUMNAS PARA EL BACKEND
# ==========================================
columnas_backend = [
    'edad',
    'sexo',
    'estado_civil',
    'numero_hijos',
    'empleo_formal',
    'ingreso_mensual',
    'linea_credito',
    'gasto_total',
    'credito_utilizado',
    'monto_promedio_ahorro',
    'ratio_gasto_ingreso',
    'pct_credito_ocupado',
    'tasa_ahorro_real'
]

X = clientes[columnas_backend]
y = clientes['perfil_financiero']

# ==========================================
# 3. PREPROCESAMIENTO
# ==========================================
numeric_features = [
    'edad',
    'numero_hijos',
    'empleo_formal',
    'ingreso_mensual',
    'linea_credito',
    'gasto_total',
    'credito_utilizado',
    'monto_promedio_ahorro',
    'ratio_gasto_ingreso',
    'pct_credito_ocupado',
    'tasa_ahorro_real'
]
categorical_features = ['sexo', 'estado_civil']

preprocessor = ColumnTransformer(
    transformers=[
        ('num', StandardScaler(), numeric_features),
        ('cat', OneHotEncoder(handle_unknown='ignore'), categorical_features)
    ]
)

# ==========================================
# 4. PIPELINE CON HIPERPARÁMETROS FIJOS
# ==========================================
best_model = Pipeline(steps=[
    ('preprocessor', preprocessor),
    ('classifier', RandomForestClassifier(
        n_estimators=300,         
        max_depth=None,           # Crecimiento libre
        min_samples_split=2,      
        min_samples_leaf=1,       
        max_features='log2',      
        random_state=42
    ))
])

# ==========================================
# 5. DIVISIÓN Y ENTRENAMIENTO DIRECTO
# ==========================================
X_train, X_test, y_train, y_test = train_test_split(
    X, y, test_size=0.2, random_state=42, stratify=y
)

print("🚀 Entrenando Modelo RandomForest con Hiperparámetros Fijos (300 est, depth=None)...")
best_model.fit(X_train, y_train)

# ==========================================
# 6. EVALUACIÓN Y MÉTRICAS
# ==========================================
y_pred = best_model.predict(X_test)

print("\n--- Métricas de Evaluación ---")
print(f"Accuracy: {accuracy_score(y_test, y_pred):.4f}")
print(f"F1-Score Macro: {f1_score(y_test, y_pred, average='macro'):.4f}")
print(f"Precision Macro: {precision_score(y_test, y_pred, average='macro'):.4f}")
print(f"Recall Macro: {recall_score(y_test, y_pred, average='macro'):.4f}")

print("\nClassification Report:")
print(classification_report(y_test, y_pred))

# Guardar el modelo entrenado
joblib.dump(best_model, 'modelo_perfil_financiero_rf.pkl')
print("\n💾 Modelo exportado exitosamente como 'modelo_perfil_financiero_rf.pkl'")

```





## Probamos 3 notebooks con el modelo propuesto (dataset, entrenamiento, prueba)

**prueba** 
- resultado esperado : Saludable

```
{
    'edad': 25,
    'sexo': 'f',
    'estado_civil': 'soltero',
    'numero_hijos': 0,
    'empleo_formal': 1,
    'ingreso_mensual': 20000.0,
    'linea_credito': 10000.0,
    'gasto_total': 8000.0,             # Total transaccionado en el mes
    'credito_utilizado': 2500.0,        # Deuda actual de tarjeta
    'monto_promedio_ahorro': 5000.0,    # Ahorro promedio guardado
    'ratio_gasto_ingreso': 0.40,        # 8000 / 20000
    'pct_credito_ocupado': 0.25,        # 2500 / 10000
    'tasa_ahorro_real': 0.25            # 5000 / 20000
}
```


### notebook 1

- dataset clientes

<br>

<img width="358" height="119" alt="image" src="https://github.com/user-attachments/assets/4c7d5a39-5166-4245-a926-e6041f013491" />

<br>
<br>

- score y hiperparametros entrenamiento

<br>

 <img width="913" height="204" alt="image" src="https://github.com/user-attachments/assets/6f5c7517-1b56-475e-80f2-087a9de9acb9" />

<br>
<br>

-  matriz confusion

<br>

 <img width="743" height="584" alt="image" src="https://github.com/user-attachments/assets/95bc15e8-f551-4bed-a745-131f8945e3ae" />

<br>
<br>

- prueba

<br>

 <img width="443" height="105" alt="image" src="https://github.com/user-attachments/assets/f6c04728-7316-442a-8f90-9d8f52324f63" />



### notebook 2

- dataset clientes

<br>

<img width="385" height="125" alt="image" src="https://github.com/user-attachments/assets/ce2d94ff-9da5-465b-bdc1-02164c39835e" />

<br>
<br>

- score y hiperparametros entrenamiento

<br>

<img width="928" height="212" alt="image" src="https://github.com/user-attachments/assets/3c5b62a6-24cc-4dd9-9659-071df108480d" />

<br>
<br>


- matriz confusion

<br>

<img width="743" height="584" alt="image" src="https://github.com/user-attachments/assets/8e93ce80-aa09-42c8-9e79-f7cf6188926b" />

<br>
<br>

- prueba

<br>

<img width="394" height="96" alt="image" src="https://github.com/user-attachments/assets/0f227939-6e4c-4869-b8cb-042af6894fd5" />



### notebook 3

- dataset clientes

<br>

<img width="367" height="121" alt="image" src="https://github.com/user-attachments/assets/b986923e-a68e-4a84-a0be-d3f93413b0e3" />

<br>
<br>

- score y hiperparametros entrenamiento

<br>

<img width="920" height="208" alt="image" src="https://github.com/user-attachments/assets/ae5d4ed6-6385-4ab9-ae06-4b412f142051" />

<br>
<br>

- matriz confusion

<br>

<img width="743" height="584" alt="image" src="https://github.com/user-attachments/assets/d784ac02-fa2a-4050-b6dc-f823f3e74878" />

<br>
<br>

- prueba

<br>

<img width="388" height="97" alt="image" src="https://github.com/user-attachments/assets/136c87e5-2305-4ca8-a443-afde8a6712ca" />



### conclusiones de estos 3 notebooks
Se nota ligeras variaciones en los scores de cada entrenamiento esto es debido a la combinacion aleatoria en los hiperparametros y la distribución que realiza K-means que como se muestra en seccion dataset de cada notebook varia ligeramente
- el notebook 2 tuvo un mejor modelo donde su prueba obtuvo un score de 59%

