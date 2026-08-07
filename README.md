
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

*cambios*

variables actualizadas
- perfil_financiero

variables agregadas
- credito_utilizado : Recuperada de users.csv. Representa la deuda activa real en la tarjeta o línea de crédito.
- monto_promedio_ahorro : Recuperada de users.csv. Representa el saldo real acumulado en las cuentas de ahorro del cliente.
- ratio_gasto_ingreso : Ratio nuevo de flujo. Se calcula como '''gasto_total/ingreso_mensual'''. Reemplazó al antiguo nivel_endeudamiento
- pct_credito_ocupado : Ratio nuevo de apalancamiento. Se calcula como '''credito_utilizado/textlinea_credito'''. Mide qué tan saturada está la tarjeta de crédito.
- tasa_ahorro_real :Ratio nuevo de reserva. Se calcula como '''monto_promedio_ahorro/ingreso\_mensual'''. Reemplazó al antiguo rango_ahorro

bariables eliminadas :
- nivel_endeudamiento
- rango_ahorro



## Comparativa entre modelo actual y propuesto (perfil financiero)

Haciendo una prueba para comprobar que el modelo actual de perfil financiero presenta data leake debido a una incorrecta categorizacion en el proceso de depuracion de dataset clientes

### Causas y evidencias
- mas informacion en archivo auditoria_modelos.md

#### modelo actual

- precision del 100% perfecto

<img width="936" height="227" alt="image" src="https://github.com/user-attachments/assets/7d276f15-ccc6-4983-8e6e-1c874a619fb9" />


- tabla de confusion diagonal

<img width="664" height="556" alt="image" src="https://github.com/user-attachments/assets/2394b142-d185-4365-ae64-9a1a9126fe63" />

#### modelo propuesto

- precision 96.33%
<img width="940" height="232" alt="image" src="https://github.com/user-attachments/assets/4e9a5ba3-2352-423f-bdb9-1c128fb225a8" />

- tabla de confusion no diagonal

<img width="742" height="590" alt="image" src="https://github.com/user-attachments/assets/af6910da-baee-449a-810c-456c69ba98ba" />








### Pruebas de prediccion
#### modelo actual
- usando datos procesados

<img width="546" height="411" alt="image" src="https://github.com/user-attachments/assets/de7c9905-7da3-4af4-b73e-9e7115cfd28f" />



#### modelo propuesto
- usando datos crudos

<img width="636" height="473" alt="image" src="https://github.com/user-attachments/assets/c7240524-0008-4722-8862-44f4dfe63693" />

- usando datos procesados

<img width="409" height="417" alt="image" src="https://github.com/user-attachments/assets/a79887de-6432-4ace-8bca-465e45cc0067" />


### resultado
El modelo actual dio un resultado de RIESGOSO
El modelo propuesto dio un resultado de SALUDABLE con precision 57.5%
