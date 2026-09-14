#Universidad #Auto #PlatsII 

## ¿Qué es el Forecasting?

El **Forecasting** (también conocido como pronóstico o previsión) es una técnica de análisis de datos que se utiliza para predecir el comportamiento futuro de un evento o una serie de eventos basándose en el estudio de datos históricos y la identificación de patrones.

## Características del Forecasting

- **Análisis de datos históricos:** Se fundamenta en datos del pasado. Requiere el acceso y procesamiento de grandes volúmenes de datos históricos para identificar tendencias, estacionalidad y ciclos.
    
- **Uso de técnicas estadísticas y matemáticas:** Emplea algoritmos y modelos matemáticos para proyectar eventos futuros. Las técnicas más comunes incluyen el análisis de series de tiempo, modelos de regresión y algoritmos de Machine Learning.
    
- **Aplicaciones transversales:** Se utiliza en diversas industrias para casos de uso como la planificación de la cadena de suministro, la predicción de demanda y ventas, la gestión de recursos humanos y la estimación de riesgos.
    
- **Impacto en la toma de decisiones:** Es crucial para la estrategia empresarial. Proporciona información con respaldo cuantitativo que permite a las organizaciones anticiparse, planificar capacidad y mitigar riesgos de forma informada.
    
- **Actualización y revisión constante (Monitoreo):** Dado que las condiciones del entorno cambian, los pronósticos pierden precisión con el tiempo (fenómeno conocido como _data drift_). Es vital actualizar los datos y reentrenar los modelos constantemente para garantizar su relevancia.
    

## ➕ Datos Adicionales (Enfoque en Plataformas y Automatización)

Para darle más peso a tus apuntes de cara a la materia, te sugiero agregar estos conceptos clave:

**1. Tipos de Modelos de Forecasting:**

- **Estadísticos Clásicos:** ARIMA (Autoregressive Integrated Moving Average), Suavizado Exponencial (Exponential Smoothing). Son ideales para datos con tendencias claras y poca complejidad.
    
- **Machine Learning / Deep Learning:** Redes Neuronales Recurrentes (RNN), LSTMs (Long Short-Term Memory) o librerías modernas como Prophet de Meta. Son excelentes para manejar múltiples variables complejas, picos irregulares o datos no lineales.
    

**2. El Forecasting en la Automatización de Infraestructura (AIOps):** En el diseño de plataformas, el forecasting no solo predice ventas, sino que automatiza la infraestructura tecnológica. Por ejemplo:

- **Capacity Planning y Auto-escalado:** Predecir cuándo un clúster (como Kubernetes) va a llegar a su límite de CPU/Memoria basándose en el tráfico histórico, permitiendo aprovisionar nuevos nodos o contenedores de manera predictiva antes de que ocurra una caída, en lugar de hacerlo de manera reactiva.
    

**3. Métricas de Evaluación:** Todo sistema automatizado de forecasting debe medir su margen de error para saber si es confiable. Las métricas más comunes son:

- **MAE (Mean Absolute Error):** El promedio absoluto de los errores en las predicciones.
    
- **RMSE (Root Mean Square Error):** Penaliza más fuerte los errores grandes, muy útil para evitar fallos críticos en predicciones automatizadas.