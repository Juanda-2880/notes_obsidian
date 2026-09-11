
**Asunto: Auditoría de sesgo por género — resultados del modelo de churn**

Hola [PM],

Con números: el modelo base **ya estaba razonablemente parejo por género** — DPD=0.014 y EOD=0.051, ambos por debajo del umbral de 0.1 que se usa como referencia de alerta [comparar aquí contra la disparidad real que tenían con SeniorCitizen antes de mitigar, de la Sección 8 del notebook original]. No hay un problema urgente que resolver en esta variable, pero ya queda documentado con evidencia.

Aun así, probamos las tres técnicas: **reweighting** bajó el DPD casi a cero (0.004) pero el EOD subió a 0.078, y sacrificó 13 puntos de precisión a cambio de 25 de recall. **Adversarial** fue la única que mejoró ambas métricas de disparidad a la vez (DPD=0.002, EOD=0.024), con un costo real de 3.8 puntos de AUC. **Threshold adjustment** logró DPD≈0 pero empeoró el EOD a 0.098.

**Combinar reweighting + threshold no valió la pena**: el EOD del combinado (0.101) terminó peor que cualquier técnica individual, incluso peor que el modelo sin mitigar.

Agregamos `InternetService`, `TechSupport` y `OnlineSecurity` — el EDA mostró brechas de churn de 26 a 34 puntos porcentuales, no intuición.

Quedamos atentos a feedback.

Saludos.