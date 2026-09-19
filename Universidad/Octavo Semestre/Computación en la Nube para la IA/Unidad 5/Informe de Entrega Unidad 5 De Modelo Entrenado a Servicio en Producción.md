
_**Juan David Pacheco Vargas A00401844

## 1. Contexto y Objetivo

El negocio validó el enfoque de auditoría de género realizado en la Unidad 4 y requirió que el modelo corregido pasara a formar parte de un piloto real. Esto implicó dos entregables fundamentales:

1. **Un proceso de selección real** donde compitieran múltiples configuraciones de modelos con evidencia cuantitativa (leaderboard 360°).
    
2. **La conversión del modelo ganador en un servicio real**, con estándar de seguridad, contrato definido y trazabilidad completa.
    

> **Restricciones del proyecto:** Las features son las ya justificadas en la Tarea 1 (sin agregados), la variable protegida es género, y el servicio no necesita quedar corriendo — se despliega, se toma evidencia y se elimina.

---

## 2. Unidad 4 — Selección del Modelo (Leaderboard 360°)

### 2.1 Entorno de Trabajo

Se creó una instancia de Vertex AI Workbench para el desarrollo de la unidad:

bash

```bash
gcloud workbench instances create u4-g08-wb-20260910 \
  --project=computenube20264-507322 \
  --location=us-central1-a \
  --machine-type=e2-standard-4 \
  --metadata=idle-timeout-seconds=3600
```


![[Untitled 2-1789793608025.webp]]

---

### 2.2 Ajustes al Notebook de Entrenamiento

Se realizaron modificaciones al notebook para incorporar las técnicas de mitigación de sesgo de género y habilitar la comparación justa entre arquitecturas:

![[Untitled 2-1789795770834.webp]]

![[Untitled 2-1789795871280.webp]]

![[Untitled 2-1789795995712.webp]]

![[Untitled 2-1789796097614.webp]]

![[Untitled 2-1789796269727.webp]]

![[Untitled 2-1789796516740.webp]]

---

### 2.3 Resultado del Leaderboard 360°

El leaderboard comparó las arquitecturas candidatas con un scorecard completo: **AUC, Recall, DPD, EOD y costo de negocio en dólares**, permitiendo seleccionar la mejor configuración con evidencia:

![[Untitled 2-1789796733314.webp]]

---

### 2.4 Registro en Model Registry

El modelo ganador fue registrado en **Model Registry**, quedando disponible para su despliegue como servicio:

![[Untitled 2-1789796809105.webp]]

---

## 3. Unidad 5 — Del Modelo al Servicio en Producción

### 3.1 Organización de Archivos en Cloud Shell

Se creó la estructura de carpetas del proyecto y se subieron los 4 archivos necesarios (`schemas.py`, `main.py`, `requirements.txt`, `Dockerfile`):

![[Untitled 2-1789788297570.webp]]

![[Untitled 2-1789788470438.webp]]

---

### 3.2 Creación del Repositorio de Artefactos (Artifact Registry)

Se creó el repositorio Docker y se configuró la autenticación:

bash

```bash
gcloud artifacts repositories create churn-api-repo \
  --repository-format=docker --location=us-central1

gcloud auth configure-docker us-central1-docker.pkg.dev
```

![[Untitled 2-1789788634403.webp]]

---

### 3.3 Construcción de la Imagen Docker

bash

```bash
docker build -t us-central1-docker.pkg.dev/computenube20264-507322/churn-api-repo/churn-api:v1 .
```

![[Untitled 2-1789788921440.webp]]

---

### 3.4 Subida de la Imagen al Registro

bash

```bash
docker push us-central1-docker.pkg.dev/computenube20264-507322/churn-api-repo/churn-api:v1
```

![[Untitled 2-1789789666358.webp]]

---

### 3.5 Despliegue a Cloud Run

El servicio se desplegó con las siguientes configuraciones de seguridad y costo:

bash

```bash
gcloud run deploy churn-api \
  --image=us-central1-docker.pkg.dev/computenube20264-507322/u5-g08-img-20260918/churn-api:v1 \
  --region=us-central1 \
  --no-allow-unauthenticated \
  --min-instances=0 --max-instances=1 \
  --memory=512Mi --port=8080
```

Table

|Configuración|Justificación|
|:--|:--|
|`--no-allow-unauthenticated`|Servicio cerrado, sin acceso público|
|`--min-instances=0`|No genera costo si nadie lo usa|
|`--max-instances=1`|Techo de costo definido|

![[Untitled 2-1789797651664.webp]]

---

### 3.6 Prueba del Endpoint `/health`

Se verificó que el servicio respondiera correctamente con status **200**:

bash

```bash
curl -H "Authorization: Bearer $(gcloud auth print-identity-token)" \
  https://TU_SERVICE_URL/health
```

![[Untitled 2-1789797943640.webp]]

---

### 3.7 Prueba del Endpoint `/predict`

Se realizó una predicción de prueba con un caso válido, confirmando que el modelo responde correctamente:

bash

```bash
curl -X POST \
  -H "Authorization: Bearer $(gcloud auth print-identity-token)" \
  -H "Content-Type: application/json" \
  -d '{
    "customer_id": "TEST-001",
    "senior_citizen": false,
    "partner": true,
    "dependents": false,
    "tenure": 12,
    "monthly_charges": 70.5,
    "contract": "Month-to-month",
    "payment_method": "Electronic check"
  }' \
  https://u5-g08-cr-20260918-1052716375237.us-central1.run.app/predict
```

![[Untitled 2-1789798211760.webp]]

---

### 3.8 Verificación del Servicio Desplegado

Se confirmó el estado del servicio activo en Cloud Run:

![[Untitled 2-1789798630283.webp]]

---

## 4. Consideraciones Finales

- **Logging estructurado:** El archivo `main.py` ya incorporaba desde el inicio el logging estructurado con `logger.info("predict_ok", ...)`, registrando input y output completos en formato JSON para trazabilidad en Cloud Logging. Por esta razón no fue necesario repetir el proceso de reconstrucción con tag `:v2`.
    
- **Service Account dedicada:** Se creó la identidad `churn-api-sa` con permisos mínimos (`roles/storage.objectViewer`) sobre el bucket del modelo, siguiendo el principio de menor privilegio.
    
- **Limpieza:** Como el servicio no requiere quedar corriendo para la entrega, se procedió a eliminarlo junto con el repositorio de artefactos al finalizar la toma de evidencias.
    

---

## 5. Conclusión

Se cumplió el objetivo completo: el modelo corregido con mitigación de sesgo de género pasó de ser un notebook a un **servicio real en producción**, con contrato definido, seguridad por autenticación, service account dedicada con permisos mínimos, logging estructurado para trazabilidad, y evidencia de funcionamiento mediante pruebas de los endpoints `/health` y `/predict`.