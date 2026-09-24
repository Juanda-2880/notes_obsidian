Juan David Pacheco Vargas A00401844

---

**Destinatario:** Equipo de Negocio

**Fecha:** 24 de septiembre de 2026

**Asunto:** Diagnóstico del pipeline

### 1. Resumen

El análisis sobre 703 registros de las últimas 10 semanas evidencia un alza crítica en la tasa de rechazo, pasando de 2.0% a 14.3% y finalmente a 31.8%. Este comportamiento obedece a tres causas técnicas:

- **Métodos de pago no reconocidos:** Ingesta de PSE, PayPal, Digital Wallet, Corporate Billing y tarjeta manual, ausentes en el catálogo original.
    
- **Calidad de datos:** Inconsistencias recurrentes como `MonthlyCharges` vacío, espacios en `Partner`/`Dependents`, valores nulos en `tenure` y duplicidad en `customerID`.
    
- _**Data Drift**_**:** Desplazamiento en registros aceptados, observándose mayor antigüedad, cargos mensuales más altos y predominio de fibra óptica respecto a las semanas 1 y 2.
    

### 2. Impacto y Recomendaciones

Aunque el modelo genera predicciones, evalúa una población desalineada frente al entrenamiento base. Se recomienda:

1. Actualizar el contrato de datos para homologar o admitir los nuevos métodos de pago.
    
2. Reentrenar el algoritmo predictivo con muestras etiquetadas recientes.
    
3. Aplicar reglas de contingencia de negocio en lugar de automatizar scores.
    

_Nota:_ BancoPago se monitorea de forma desacoplada. Se solicita confirmar si las campañas modificaron su segmento objetivo a partir del 24 de agosto.