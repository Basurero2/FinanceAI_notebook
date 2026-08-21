# Cambios que se hicieron



## data transactions.csv

<img width="636" height="149" alt="image" src="https://github.com/user-attachments/assets/b944c92c-5dd7-470f-bd24-b2c068a679ac" />

### Que se cambio 

Se cambio el dataset transactions.csv con la original que no contiene los valores `Vivienda` y `Educación`


<br>
<br>



## Advertencias

<img width="938" height="415" alt="image" src="https://github.com/user-attachments/assets/c6aa1bf4-89ec-471c-b7ee-5983057f92ce" />

### Que se cambio

Se agrego la librería `warnings` en sección "Importación de librerías"

<br>
<br>


## segunda matriz de correlación (revisar detalladamente)

### Matriz de correlación de variables numéricas

Se calcula una segunda matriz de correlación para las variables numéricas y se aplica una máscara triangular para mostrar únicamente una mitad de la matriz. Los valores de correlación se presentan en un mapa de calor.

```python
plt.figure(figsize=(12, 10))

columnas_numericas = df_final.select_dtypes(include='number').columns

# 1. Calcular matriz de correlación
corr = df_final[columnas_numericas].corr()

# 2. Crear la máscara booleana respetando la estructura de corr
mask = np.triu(np.ones_like(corr, dtype=bool))

# 3. Dibujar el mapa de calor
sns.heatmap(corr, mask=mask, annot=True, cmap='coolwarm', fmt='.2f', vmin=-1, vmax=1)
plt.title('Matriz de Correlación - Variables Numéricas', fontsize=12, fontweight='bold')
plt.tight_layout()
plt.show()

```

<img width="1133" height="984" alt="image" src="https://github.com/user-attachments/assets/f51edf19-6197-45fa-aaa2-31f0e3877867" />


### Que se cambio

Esta es una matriz de correlación de Pearson y no de Spearman, que fue la que se utilizó en la primera matriz. Al presentarla de forma triangular, podría dar la alusión de que se trata de la misma matriz con el mismo método; por ello, se especificó explícitamente el parámetro correspondiente.

```python
corr = df_final[columnas_numericas].corr(method="spearman")
```

<br>
<br>



## variable porcentaje_ahorro

<img width="650" height="424" alt="image" src="https://github.com/user-attachments/assets/bbaf9ee3-d7e5-4e37-b78e-231c5756ecf7" />

### Que se cambio

Dicha variable no existe, por lo que se reemplazó por `rango_ahorro`, la cual fue calculada previamente en la sección «Cálculo de variables financieras» y se encuentra en df_final

```python
sns.scatterplot(data=df_final,x='ingreso_mensual',y='rango_ahorro')
```


## Shap comentarios

<img width="784" height="653" alt="image" src="https://github.com/user-attachments/assets/24bda2e3-3f0b-4917-8326-f2ae1bcf5437" />


#### Análisis de Impacto SHAP

El gráfico anterior nos permite entender:
1. **Magnitud**: Qué tan fuerte es el impacto de cada variable en la decisión final.
2. **Distribución por Clase**: Los colores representan cómo cada variable contribuye a las diferentes etiquetas del perfil financiero.

Generalmente, verás que `credito_utilizado` y `monto_promedio_ahorro` dominan la explicación, confirmando que el comportamiento de gasto y reserva es lo que más separa a un usuario saludable de uno en riesgo.

### Que se cambio

Se ve que las variables `credito_utilizado` y `monto_promedio_ahorro` no existen, lo cual se puso los correctos


<br>
<br>


## Comentarios de los modelos

Se agregó `9.1 Análisis visual de fronteras de decisión y justificación del rendimiento`

<img width="1484" height="584" alt="image" src="https://github.com/user-attachments/assets/1dc1bfeb-3388-4dcd-b5f9-f32a7b8b7131" />

Como tambien se añadio algunos conclusiones de los resultados y causas de dichos scores en las pruebas respecto al puntaje en el entrenamiento y test del modelo perfil_financiero y transacciones




