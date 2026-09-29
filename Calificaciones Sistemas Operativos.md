
Aquí tienes el guion completo. Está pensado para que **cualquier integrante pueda responder** (porque la profesora elige al azar). Cada diapositiva tiene: qué decir, cuánto durar, y una frase clave que debe quedar clara.

**Duración total estimada:** 18–22 minutos + preguntas.

---

## GUION DE SUSTENTACIÓN — GRUPO g08

---

### Diapositiva 1 — Portada (20 seg)

> "Buenos días, profesora. Somos el grupo g08: Juan David Pacheco Vargas, [Nombre 2] y [Nombre 3]. Hoy presentamos el proyecto integrador de las unidades 3 a 6: un pipeline de retención de clientes que va desde la auditoría de sesgo hasta el monitoreo en producción. El énfasis está en la Unidad 6, que es donde todo el trabajo anterior se pone a prueba con datos reales."

**Frase clave:** "No construimos un modelo: construimos un sistema que se da cuenta cuando el modelo deja de ser válido."

---

### Diapositiva 2 — Agenda (30 seg)

> "El proyecto tiene 4 fases. U3: auditar sesgo de género y justificar nuevas features. U4: competir arquitecturas y registrar la ganadora. U5: envolver el modelo en una API y desplegarla. U6: monitorear el pipeline con datos reales de 10 semanas. La U6 es la más importante porque es donde el cliente nos dice: 'las campañas funcionan peor'. Y ahí empieza el trabajo real."

---

### Diapositiva 3 — Contexto de Negocio (30 seg)

> "El PM nos pidió tres cosas: auditar si el modelo trata igual a hombres y mujeres, justificar cada feature nueva con EDA, y mitigar sesgo si existía. La restricción era clara: nada de agregar columnas porque sí. Y el objetivo: reducir disparidad sin destruir el desempeño predictivo."

---

### Diapositiva 4 — Selección de Features (45 seg)

> "Evaluamos 10 columnas candidatas. Encontramos tres con brechas de churn mayores a 25 puntos porcentuales: InternetService — fibra tiene 41.9% de churn vs 7.4% sin internet. TechSupport — 41.6% sin soporte vs 15.2% con soporte. OnlineSecurity — 41.8% sin seguridad vs 14.6% con seguridad. Estas tres entraron. Las demás — StreamingTV, StreamingMovies, MultipleLines, PhoneService — tenían brechas menores a 4 puntos: no discriminan, no entran."

---

### Diapositiva 5 — Mitigación de Sesgo (60 seg)

> "Aplicamos las tres técnicas de mitigación para género: reweighting, adversarial y threshold. La tabla muestra el trade-off. Reweighting bajó DPD de 0.014 a 0.004 pero subió EOD de 0.051 a 0.078. Adversarial fue la única que mejoró ambas — DPD 0.002 y EOD 0.024 — pero costó 4 puntos de AUC. Threshold bajó DPD a 0 pero empeoró EOD a 0.098. Y combinar reweighting + threshold fue el peor resultado: EOD 0.101, peor que el modelo base."

> "El hallazgo: **el dataset ya estaba razonablemente parejo por género**. Forzar mitigación empeoró el modelo sin beneficio real. Eso también es un resultado: no forzamos una solución a un problema que no existía."

---

### Diapositiva 6 — Leaderboard y Model Registry (40 seg)

> "En U4 competimos dos arquitecturas con búsqueda de hiperparámetros y un scorecard 360°: AUC, Recall, DPD, EOD y costo de negocio en dólares. Ganó la Arquitectura B, mitigada con reweighting sobre género: mejor balance entre estabilidad financiera y equidad. La registramos en Vertex AI Model Registry como versión aprobada."

---

### Diapositiva 7 — Contrato y Despliegue (40 seg)

> "En U5 convertimos el modelo en servicio. `schemas.py` define el contrato con Pydantic: entrada y salida tipadas. `main.py` es FastAPI: carga el modelo desde GCS una sola vez al arrancar y tiene logging estructurado. Lo dockerizamos y lo desplegamos a Cloud Run con `--no-allow-unauthenticated`, service account dedicada y `min-instances=0` para no pagar cuando nadie lo usa."

---

### Diapositiva 8 — Evidencia del Servicio (30 seg)

> "Probamos tres casos: `/health` devuelve 200. `/predict` con un payload válido devuelve el score. Y un payload inválido dispara 422 — Pydantic protege el contrato. El servicio quedó arriba con 100% del tráfico asignado."

---

### Diapositiva 9 — El Problema Real (45 seg)

> "Y aquí empieza la Unidad 6. El cliente nos escribe: 'las campañas de retención funcionan peor'. Pero el modelo no se queja: sigue devolviendo 200 OK y un número. No hay excepciones, no hay caídas. La pregunta real no es si el modelo funciona, sino **si sigue siendo representativo de la realidad**. Recibimos 703 registros de 10 semanas, con volumen variable por semana."

**Frase clave:** "Un modelo en producción sin monitoreo es un modelo que miente con seguridad."

---

### Diapositiva 10 — Arquitectura de Monitoreo (40 seg)

> "Montamos tres capas. Airflow orquesta un DAG de 4 tareas secuenciales. BigQuery almacena 8 tablas: raw, aceptados, cuarentena, métricas semanales, motivos de rechazo, drift numérico, drift categórico y alertas de umbral. Streamlit en Cloud Run es la capa de visualización: consulta BigQuery directo y muestra todo en un dashboard."

---

### Diapositiva 11 — Contrato y Cuarentena (40 seg)

> "El contrato valida campos obligatorios, categorías permitidas y consistencia entre internet y servicios adicionales. Todo lo que no cumple va a cuarentena: nuevos métodos de pago como PSE, PayPal, Digital wallet, Corporate billing y Credit card (manual), más duplicados de customerID dentro del mismo lote. Resultado: 659 aceptados, 44 rechazados."

---

### Diapositiva 12 — La Métrica del Pico (60 seg) — **Diapositiva estrella**

> "Aquí está el hallazgo principal. La tasa de rechazo se mantuvo estable entre 0% y 2.8% durante 8 semanas. Y en las últimas dos semanas explotó: 14.3% el 24 de agosto y 31.8% el 31 de agosto. Calibramos el umbral con las primeras dos semanas: μ=1.0%, σ=1.41%, +3σ = **5.24%**. Las dos últimas semanas superan el umbral con creces. Ese es el 'algo pasó' que el pipeline nos tenía que decir."

---

### Diapositiva 13 — Cuarentena Abierta (50 seg)

> "Pero el pipeline solo dice que algo pasó, no qué. Así que abrimos la cuarentena. Encontramos **dos fenómenos cronológicamente separados**. Semanas 1 a 6: problemas de captura — MonthlyCharges con 'sin dato', Partner con espacios, tenure nulo, duplicados. Semanas 7 a 10: aparecen métodos de pago nuevos que el modelo no conoce. No son 'datos malos' genéricos: son dos causas distintas en dos momentos distintos."

---

### Diapositiva 14 — Drift en los Aceptados (50 seg)

> "Y los registros que sí pasaron el contrato también tienen algo que contar. Comparando contra dos referencias — las primeras dos semanas del archivo y el set de entrenamiento — detectamos drift. Numérico: 30 filas con alertas en tenure, MonthlyCharges y TotalCharges. Categórico: 180 filas con alertas en InternetService, Contract y PaymentMethod. **Aunque los registros pasan el contrato, la distribución cambió.** El modelo aprendió con otra población."

---

### Diapositiva 15 — Metodología de Drift (40 seg)

> "La metodología no es arbitraria. Z-score estandarizado con umbral |z| > 3 para numéricas. PSI con cortes 0.1 y 0.25 para cambio estructural. Z-test de proporciones para categóricas. Y el punto clave: **los límites se calcularon con las primeras semanas**, no se escogieron a dedo. Eso evita los dos extremos: alertar por ruido o no alertar nunca."

---

### Diapositiva 16 — BancoPago (40 seg)

> "Sobre el campo BancoPago: aparece desde el 27 de julio, con captura imperfecta — alias, mayúsculas inconsistentes, vacíos. Decisión documentada: **no entra al modelo** porque no fue entrenado con él. Se normaliza a un catálogo cerrado de 5 bancos y se monitorea aparte. Es una decisión defendible, no improvisada."

---

### Diapositiva 17 — Streamlit (40 seg)

> "El dashboard tiene 5 secciones: tasa de rechazo con línea de tiempo vs umbral 5.24%, motivos de rechazo desglosados, drift numérico, drift categórico y mix de bancos. Está desplegado en Cloud Run como `u6-g08-cr-20260924` en us-central1. El Dockerfile tiene config.toml con WebSocket habilitado para que funcione detrás del proxy."

**[Aquí reproduces el video del Streamlit — 30 seg]**

---

### Diapositiva 18 — Respuesta al Cliente (45 seg)

> "Con toda esta evidencia respondimos al cliente. Tres causas: nuevos métodos de pago, errores de captura upstream, y drift demográfico. Tres recomendaciones: actualizar el contrato formal, reentrenar el modelo con las últimas semanas etiquetadas, y aplicar reglas de negocio temporales mientras tanto. Y le hicimos una pregunta clave: **¿cambiaron el segmento objetivo a partir del 24 de agosto?** Porque eso explicaría el resto."

---

### Diapositiva 19 — Conclusiones (40 seg)

> "Tres conclusiones. Primera: un modelo sin monitoreo responde números con seguridad aunque la realidad cambie. Segunda: construimos un pipeline que audita contratos, aísla cuarentena y calibra umbrales con base estadística, no con corazonadas. Tercera: el problema no estaba en el algoritmo. Estaba en el cambio de población atendida. Si el cliente hubiera esperado un mes más sin este monitoreo, habría seguido tomando decisiones con un modelo ciego."

---

### Diapositiva 20 — Entregables (20 seg)

> "Todo está disponible: código y DAGs en GitHub, 8 tablas en BigQuery, imagen Docker v3 en Artifact Registry y el dashboard en Cloud Run. Muchas gracias. Quedamos abiertos a preguntas."

---

## PREPARACIÓN PARA PREGUNTAS (por si acaso)

La profesora elige al azar quién responde y qué pregunta. Prepárense para estas:

**"¿Por qué el umbral es 5.24% y no otro?"**
> "Porque lo calculamos con las primeras dos semanas, donde no pasó nada extraordinario. Media 1%, desviación 1.41%. Media + 3σ = 5.24%. Eso significa que si una semana supera ese valor, hay 99.7% de probabilidad de que no sea variación normal."

**"¿Por qué no agregaron BancoPago al modelo?"**
> "Porque el modelo se entrenó sin esa columna. Si la agregamos, el modelo no sabe qué hacer con ella — tendríamos que reentrenar. Mientras tanto, la monitoreamos aparte para detectar si el mix de bancos cambia, que también es señal de negocio."

**"¿Qué pasaría si el umbral fuera el doble o la mitad?"**
> "Al doble — 10% — no habría alertado hasta la semana 10. Falsa tranquilidad. A la mitad — 2.6% — habría alertado desde la semana 7 por ruido. Todos habrían aprendido a ignorar la alerta. Por eso se calcula, no se escoge."

**"¿Combinar técnicas de mitigación valió la pena?"**
> "No. El EOD del combinado fue 0.101, peor que el modelo base. El DPD sí bajó a 0.001, pero a costa de la métrica más estricta. Y género ya estaba parejo desde el inicio. Fue un ejemplo real del trade-off entre demographic parity y equalized odds."

**"¿Cómo midieron drift y por qué?"**
> "Z-score estandarizado para numéricas, z-test de proporciones para categóricas, PSI para cambio estructural. Usamos dos referencias: las primeras dos semanas del archivo y el set de entrenamiento. No coinciden — y eso ya es un hallazgo: la población cambió más contra el set original que contra las semanas recientes."

**"¿Por qué el DAG está en Airflow local y no en Cloud Composer?"**
> "Por restricción de costos del curso. Airflow local corre sin costo en GCP. En producción real usaríamos Cloud Composer, pero la lógica del DAG es idéntica."

**"¿Qué harían con las semanas sin alerta del drift?"**
> "Nada. La ausencia de alerta también es información: significa que la población se mantuvo estable. Lo que sí haríamos es acumular historial para recalibrar el umbral cada trimestre."

---

## CONSEJOS FINALES

1. **Reparte las diapositivas** así: Juan David las de U6 (porque las trabajó más), compañero 2 las de U3-U4, compañero 3 las de U5 + conclusiones. Pero **todos deben poder responder cualquier pregunta** — estudien todo el guion.

2. **La diapositiva 12 es la más importante.** Si solo pueden preparar una a fondo, es esa. Es la que responde directamente al cliente.

3. **Mencionen el trade-off de mitigación** aunque no pregunten. Demuestra que entienden el problema, no solo que corrieron código.

4. **No lean las diapositivas.** El guion es para saber qué decir, no para memorizar palabra por palabra.

5. **Si no saben algo, digan "no lo medimos, pero lo que sí sabemos es..."** y redirijan a lo que sí dominan. Eso es mejor que inventar.

¿Quieres que te prepare también un **resumen de 1 página** con los números clave para tener a mano durante la sustentación?