_**Juan David Pacheco Vargas A00401844**_

# Informe Unidad 3 — Auditoría de sesgo por género y ampliación de features del modelo de churn

---

## 1. Acceso y configuración del entorno (GCP Workbench)

Confirmación del proyecto correcto en la consola de GCP:

![[Informe Unidad 3-1789077260402.webp]]

Construcción y organización del comando de creación de la instancia:

![[Informe Unidad 3-1789077278574.webp]]

Ejecución del comando en Cloud Shell:

![[Informe Unidad 3-1789077334379.webp]]

Instancia ya en estado `RUNNING`:

![[Informe Unidad 3-1789077530685.webp]]

Notebook `.ipynb` subido a la instancia de JupyterLab:

![[Informe Unidad 3-1789077785157.webp]]

Reinicio de kernel y limpieza de variables antes de correr desde cero (evita el clásico problema de "stale state" — variables viejas en memoria que no reflejan los cambios hechos en el código):

![[Informe Unidad 3-1789077921103.webp]]

Verificación y corrección del `PROJECT_ID` correcto para el grupo (no el genérico del ejemplo del notebook):

![[Informe Unidad 3-1789078232078.webp]]

---

## 2. Tarea 1 — Selección de features (20%)

**Restricción respetada:** no se agregó el resto del dataset crudo "porque sí" — cada columna nueva se justifica con el EDA de abajo o se descarta explícitamente.

Selección y EDA de las 10 columnas del dataset crudo que no se habían usado:

![[Informe Unidad 3-1789078649719.webp]]

### Resultados del EDA — tasa de churn por columna candidata

|Columna|Categoría con mayor churn|Categoría con menor churn|Brecha (pp)|
|---|---|---|---|
|InternetService|Fiber optic — 41.9%|No — 7.4%|**34.5**|
|TechSupport|No — 41.6%|Yes — 15.2%|**26.5**|
|OnlineSecurity|No — 41.8%|Yes — 14.6%|**27.2**|
|OnlineBackup|No — 39.9%|Yes — 21.5%|18.4|
|DeviceProtection|No — 39.1%|Yes — 22.5%|16.6|
|PaperlessBilling|Sí — 33.6%|No — 16.3%|17.2|
|StreamingTV|No — 33.5%|Yes — 30.1%|3.4|
|StreamingMovies|No — 33.7%|Yes — 29.9%|3.7|
|MultipleLines|Yes — 28.6%|No phone service — 24.9%|3.6|
|PhoneService|Sí — 26.7%|No — 24.9%|1.8|

### Decisión final: `InternetService`, `TechSupport`, `OnlineSecurity`

**Justificación de las elegidas:**

- **`InternetService`**: la brecha más grande de todas las candidatas (34.5 puntos porcentuales entre Fiber optic y No). Fibra es el segmento premium con más fricción de precio/soporte — señal fuerte y con sentido de negocio.
- **`TechSupport`**: pedida explícitamente por el PM. Brecha de 26.5 pp — clientes sin soporte técnico contratado casi triplican el churn de los que sí lo tienen.
- **`OnlineSecurity`**: también pedida por el PM. Brecha de 27.2 pp, la segunda más alta de todas las candidatas.

**Justificación de las descartadas (no es que se "olvidaron", se evaluaron y no pasaron el criterio):**

- **`OnlineBackup` y `DeviceProtection`**: brechas relevantes (16–18 pp) pero **redundantes** — su categoría "No internet service" tiene exactamente la misma tasa (7.40%) que `OnlineSecurity` y `TechSupport`, porque las cuatro columnas son proxies del mismo hecho (tener o no internet) más el mismo patrón de bundling de servicios. Agregarlas junto a `TechSupport`/`OnlineSecurity` metería multicolinealidad sin aportar señal independiente — justo el problema de calidad que el PM pidió evitar.
- **`PaperlessBilling`**: brecha real (17.2 pp) pero es un comportamiento administrativo, no un servicio contratado; se deja fuera de esta ronda para no diluir la justificación de negocio del set elegido, aunque es candidata razonable para una siguiente iteración.
- **`StreamingTV`, `StreamingMovies`, `MultipleLines`, `PhoneService`**: brechas de 1.8 a 3.7 pp — prácticamente sin poder separador. Se descartan por bajo valor de señal.

---

## 3. Corrección de error de encoding (troubleshooting documentado)

Al pasar las columnas categóricas nuevas a XGBoost, salió `ValueError: DataFrame.dtypes for data must be int, float, bool or category`, porque `InternetService`, `TechSupport` y `OnlineSecurity` seguían como texto. Se resolvió forzando el one-hot encoding y casteando explícitamente a entero:

```python
# One-hot encoding de las columnas categóricas (VERSIÓN CORREGIDA)
cols_a_codificar = [
    'gender', 'Partner', 'Dependents', 'Contract', 'PaymentMethod',
    'InternetService', 'TechSupport', 'OnlineSecurity'  # <-- se agregan las nuevas aquí
]

X_encoded = pd.get_dummies(X, columns=cols_a_codificar, drop_first=True).astype(int)
```

![[Informe Unidad 3-1789097272031.webp]]

---

## 4. Tarea 2 — Modelo base ampliado (10%)

Mismo `train_test_split(random_state=42, stratify=y)` que el modelo original — resultados comparables entre el modelo de 9 columnas y este de features ampliadas.

![[Informe Unidad 3-1789098116785.webp]]

### Reporte de clasificación del modelo ampliado

![[Informe Unidad 3-1789097378694.webp]]

### Interpretabilidad del modelo ampliado (SHAP)

Importancia de features vía SHAP, para verificar que las columnas nuevas sí aportan señal real y no solo ruido:

![[Informe Unidad 3-1789097436399.webp]]

![[Informe Unidad 3-1789097472823.webp]]

![[Informe Unidad 3-1789097495293.webp]]

### PDP vs. ALE

Comparación de Partial Dependence Plot contra Accumulated Local Effects, para revisar si la relación entre variables como `tenure` y la predicción se mantiene estable una vez se controla por correlación entre features:

![[Informe Unidad 3-1789097558867.webp]]

![[Informe Unidad 3-1789097599146.webp]]

Gráficas adicionales de interpretabilidad del modelo ampliado:

![[Informe Unidad 3-1789097791778.webp]]

![[Informe Unidad 3-1789097806423.webp]]

![[Informe Unidad 3-1789097866688.webp]]

![[Informe Unidad 3-1789097943252.webp]]

![[Informe Unidad 3-1789097987851.webp]]

---

## 5. Tarea 3 — Técnicas de mitigación por separado, para género (20%)

**Restricción respetada:** género se agrega como variable protegida adicional; `SeniorCitizen` sigue en el modelo, no se reemplazó.

![[Informe Unidad 3-1789098143664.webp]]

### 3a. Reweighting y 3b. Adversarial training

`compute_sample_weight` calculado sobre la intersección `gender × Churn` (no sobre `SeniorCitizen`, que es el punto de cuidado del troubleshooting de la guía). Adversarial training con la misma arquitectura ya usada para `SeniorCitizen`, cambiando la variable protegida que el adversario intenta adivinar.

![[Informe Unidad 3-1789098502319.webp]]

### 3c. Threshold adjustment (post-procesamiento)

Técnica que no estaba en el notebook original — se construyó con `fairlearn.postprocessing.ThresholdOptimizer` sobre las probabilidades del modelo ya entrenado.

**Troubleshooting adicional documentado:** `XGBClassifier.predict_proba()` devuelve `float32`, y fairlearn intenta escribir predicciones interpoladas en `float64` sobre ese arreglo — las versiones recientes de pandas ya no permiten esa pérdida de precisión silenciosa (`LossySetitemError`). Se resolvió con un wrapper que fuerza `predict_proba` a `float64` antes de pasarlo a `ThresholdOptimizer`, sin alterar ninguna predicción del modelo.

![[Informe Unidad 3-1789099200390.webp]]

---

## 6. Tarea 4 — Combinar técnicas (20%)

**Decisión de orden:** reweighting (pre-procesamiento) + threshold adjustment (post-procesamiento), en ese orden. El `ThresholdOptimizer` se ajustó sobre `modelo_reweighted_gender` (el modelo ya reponderado), no sobre el modelo base — es decir, el ajuste de umbral es el último paso y queda calibrado sobre las probabilidades finales que de verdad se usan en producción, no sobre probabilidades que después iban a cambiar.

![[Informe Unidad 3-1789099130414.webp]]

---

## 7. Tarea 5 — Tabla comparativa (20%)

|Configuración|Recall (Churn)|Precision (Churn)|AUC-ROC|DPD (género)|EOD (género)|
|---|---|---|---|---|---|
|**Base**|0.532|0.646|0.835|0.014|0.051|
|Solo Reweighting|0.786|0.517|0.834|0.004|0.078|
|Solo Adversarial|0.596|0.582|0.797|0.002|0.024|
|Solo Threshold|0.524|0.632|0.835|0.000|0.098|
|**Combinado**|0.612|0.589|0.834|0.001|0.101|

![[Informe Unidad 3-1789099470318.webp]]

### Lectura de la tabla

- **El modelo base ya estaba razonablemente parejo por género**: DPD=0.014 y EOD=0.051 son valores bajos de entrada — muy por debajo del umbral de 0.1 que suele usarse como referencia de alerta en Fairlearn. Esto confirma lo que insinuaba el diccionario de datos (`gender` como "control negativo" con poco poder predictivo sobre Churn).
- **DPD mejora con las tres técnicas**, de forma consistente (0.014 → 0.004 → 0.002 → 0.000).
- **EOD NO mejora con reweighting ni con threshold** — de hecho empeora (0.051 → 0.078 y → 0.098 respectivamente). Solo adversarial reduce ambas métricas de disparidad a la vez (EOD 0.051 → 0.024). Esto es un ejemplo real del _trade-off_ clásico entre demographic parity y equalized odds: optimizar una no garantiza mejorar la otra.
- **Reweighting** es la técnica más costosa en precisión: gana 25 puntos de recall pero pierde 13 de precisión — un cambio de umbral de decisión implícito, no gratis.
- **Adversarial** es la única con costo real en AUC (0.835 → 0.797, -3.8 puntos) — el precio de forzar la red a "olvidar" género.
- **Combinar reweighting + threshold no mejoró el resultado**: el EOD del combinado (0.101) terminó siendo el peor de las cinco configuraciones, incluso peor que el modelo base sin mitigar. El DPD sí mejora (0.001), pero a costa de empeorar la métrica de disparidad más estricta (EOD).

---

## 8. Cierre del laboratorio

![[Informe Unidad 3-1789099760109.webp]]