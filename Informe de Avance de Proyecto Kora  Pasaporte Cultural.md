
## 1. Resumen Ejecutivo y Definición del Proyecto

**Kora (Pasaporte Cultural)** es una plataforma multilateral _Online-to-Offline_ (O2O) diseñada para conectar la agenda digital cultural con la escena musical y de eventos en vivo presenciales, teniendo como foco inicial el circuito independiente de Cali. La propuesta se articula bajo la arquitectura de marca: _"Tu mapa, tu música, tu pasaporte"_.

### 1.1. Equipo Emprendedor

- **Angela Camila Quitiaquez Diaz** (Código: A00395494).
    
- **Josué Manuel Ñañez Castro** (Código: A00395928).
    
- **David Vergara Laverde** (Código: A00402237).
    
- **Juan David Pacheco Vargas** (Código: A00401844).
    

### 1.2. Arquitectura de Plataforma Multilateral (Two-Sided Platform)

- **Lado A – Audiencia / Usuarios (B2C):** Descubrimiento georreferenciado de toques y salas en agenda y mapa interactivo, pasaporte cultural gamificado mediante colección de sellos verificados al asistir físicamente, canal de micro-mecenazgo voluntario hacia las agrupaciones y personalización estética con privilegios VIP.
    
- **Lado B – Organizadores, Salas y Agrupaciones (B2B):** Mayor visibilidad de eventos locales, control de acceso y check-in presencial, y suscripción a un _Admin Dashboard_ con analíticas demográficas y de retención física de audiencia en tiempo real (_Live Audience Analytics_).
    

## 2. Definición Operativa de Uso Real (O2O) y Resiliencia Técnica

El proyecto establece formalmente que la simple navegación web, visitas al prototipo o respuestas a encuestas declarativas no constituyen entrega ni validación de valor.

### 2.1. Criterio de Uso Real

- **En el usuario (B2C):** Descubrimiento del evento dentro de la plataforma, asistencia física al recinto y ejecución exitosa del check-in presencial para estampar el sello digital verificado en el pasaporte.
    
- **En el organizador / banda (B2B):** Ingreso efectivo al _Admin Dashboard_ de Kora y consulta de reportes analíticos de asistencia física y desglose por edad y género de su audiencia.
    

### 2.2. Resiliencia de Ingeniería y Pivotes Técnicos Operativos

Para garantizar viabilidad y continuidad operativa en campo, la arquitectura del prototipo desplegado en Netlify resolvió tres fricciones críticas:

- **Pivote 01 (Consumo de Batería):** Reemplazo del rastreo continuo por GPS (que drenaba un 12%/hora) por un modelo de _geofencing_ reactivo activado por el acelerómetro del dispositivo.
    
- **Pivote 02 (Ingesta de Eventos):** Abandono del _scraping_ en Instagram (que exhibía una efectividad de solo 35%) para adoptar integraciones directas vía API con ticketeras locales y módulos de autogestión para promotores (alcanzando 90% de confiabilidad).
    
- **Pivote 03 (Conectividad en Recintos):** Ante la pérdida recurrente de red celular en sótanos y salas cerradas (40% de fallas en arquitecturas síncronas), se implementó una arquitectura _Offline-First_ con validación de sellos mediante códigos QR locales cifrados.
    

## 3. Validación Experimental Temprana (Tracción y Fake Doors)

### 3.1. Métricas de Pauta Digital (Meta Ads)

- **Alcance:** 5.736 personas únicas impactadas mediante video promocional de 10 segundos.
    
- **Reproducciones Totales:** 7.144 visualizaciones con 38,4% de retención promedio.
    
- **Eficiencia de Adquisición:** Costo por resultado (CPR) promedio de $102 COP ($104 en hombres, $100 en mujeres).
    
- **Quiebre Demográfico:** Descarte de la hipótesis inicial de consumo puramente universitario (el rango de 18 a 24 años solo representó el 3% de las interacciones). El mayor volumen de _engagement_ se concentró en adultos de 35 a 64 años (hombres 55–64 años: 35%; mujeres 35–44 años: 26%).
    

### 3.2. Experimentos de Disposición a Pagar (Fake Doors en Prototipo)

- **Fake Door 01 – Pasaporte VIP ($3.000 COP):** 28% de tasa de conversión ante el botón de personalización estética y sellos exclusivos, superando la meta base proyectada del 15%.
    
- **Fake Door 02 – Micro-mecenazgo a Bandas ($4.000 COP):** 33% de tasa de conversión (1 de cada 3 usuarios intentó aportar bajo el llamado a donar a cambio de una insignia digital de mecenas).
    
- **Validación B2B Preliminar:** 100% de receptividad en promotores e integrantes de bandas locales entrevistadas respecto al valor de la data demográfica, consolidando acuerdos verbales para 10 eventos en fase piloto.
    

## 4. Validación Comercial Presencial (Superación del First Money Challenge)

Para cerrar la brecha entre la intención declarada de las _Fake Doors_ y el intercambio monetario efectivo, se ejecutó una campaña de validación presencial cara a cara en entorno universitario sin intermediarios técnicos.

### 4.1. Diseño del Experimento de Campo

- **Propuesta Evaluada:** Desbloqueo y mejora del pasaporte cultural Kora.
    
- **Punto de Precio Testeado:** **$4.000 COP** en efectivo/transacción inmediata (precio seleccionado para eliminar barreras de entrada sin sacrificar compromiso económico real).
    
- **Muestra Abordada:** 7 perfiles (2 estudiantes y 5 profesores, segmento seleccionado deliberadamente para evaluar la receptividad en audiencias adultas observada en Meta Ads).
    

### 4.2. Resultados Transaccionales

- **Tasa de Conversión a Pago Inmediato:** **71,4%** (5 de 7 personas entregaron dinero en el acto).
    
    - Conversión en estudiantes: 100% (2 ventas pagadas sobre 2 abordados).
        
    - Conversión en profesores: 60% (3 ventas pagadas sobre 5 abordados).
        
- **Leads Cualificados Restantes:** 28,6% (los 2 profesores restantes manifestaron interés explícito y quedaron registrados para adopción).
    
- **Comportamiento de Valor:** Determinados participantes pagaron voluntariamente cifras por encima de los $4.000 COP base, demostrando un excedente del consumidor positivo y ratificando que el público adulto comprende y valora la propuesta con mínima fricción.
    
- **Recaudo Inmediato:** $\ge$$20.000 COP generados en validación directa.
    

## 5. Análisis Financiero y Modelo de Negocio (3 Años)

El modelo financiero proyecta el comportamiento del negocio en un horizonte de 36 meses (mes a mes durante el Año 1; consolidado anual en Años 2 y 3) en pesos colombianos (COP sin IVA).

### 5.1. Economía Unitaria (_Unit Economics_) y Alerta de Pasarela

El modelo revela una discrepancia estructural en la captura de valor transaccional:

|**Producto / Servicio**|**Segmento**|**Precio / Ticket**|**Ingreso Propio Kora**|**Costo Variable (Pasarela + Nube)**|**Margen Unitario Neto**|**Estado de Validación**|
|---|---|---|---|---|---|---|
|**Pase VIP Digital**|B2C|$3.000|$3.000|$927,61|**+$2.072,39** (+69,1%)|Validado con Fake Door 01 (28%). Conversión asumida a pago: 25%.|
|**Comisión Micro-mecenazgo (15%)**|B2C|$4.000|$600|$959,14 _(base: 2,65% + $700 + IVA)_|**-$359,14** _(Margen Negativo)_|Validado con Fake Door 02 (33%). La tarifa fija de pasarela destruye la comisión.|
|**Suscripción B2B Dashboard**|B2B|$99.900/mes|$99.900|$3.983,35|**+$95.916,65** (+96,0%)|Hipótesis de precio SaaS. Gratuito meses 1–6 (piloto); se factura desde mes 7.|
|**Comisión Entrada Afiliado**|B2B/B2C|$35.000|$1.750 _(5%)_|$0,00|**+$1.750,00** (+100%)|Hipótesis por enlace a ticketeras; sin costo de pasarela para Kora.|
|**Costo Nube por Usuario Activo**|Nube|$0,00|$0,00|$100,00/mes|**-$100,00**|Costo de infraestructura y soporte por usuario registrado activo.|

> **Hallazgo Estratégico Clave:** El micro-mecenazgo unitario genera pérdida neta si se procesa transacción a transacción ($4.000 recaudados cuestan $959 en pasarela frente a $600 retenidos). Requiere implementar monederos digitales prepagados (paquetes de créditos/tokens) o transferir el costo del procesamiento al aportante.

### 5.2. Estructura de Gastos Fijos y Desembolsos Iniciales

- **Nómina Fundadora con Rigor Legal:** $11.068.427 COP mensuales ($132,8M al año). Remunera a los 4 fundadores con un salario básico de $2.000.000 COP mensuales cada uno, agregando un 38,35% de cargas prestacionales y patronales (Pensión 12%, ARL Clase I 0,522%, Caja de Compensación 4%, Cesantías, Prima y Vacaciones, bajo la exoneración del artículo 114-1 del E.T.).
    
- **Gastos Operativos Mensuales:** $1.800.000 COP (herramientas cloud $500k, transporte y visitas a salas en Cali $400k, administración y contabilidad $400k, internet y comunicaciones $400k, contingencias $100k).
    
- **Gastos Fijos Consolidados:** **$13.113.227 COP/mes** en el mes 1, ajustándose gradualmente a $14.460.827 COP/mes al cierre del Año 1 por esfuerzos de captación.
    
- **Inversión Inicial de Puesta en Marcha (CAPEX):** **$30.500.000 COP**, desglosados en:
    
    - Desarrollo del MVP de producción (arquitectura _Offline-First_ y pasarela integrada): $20.000.000 COP.
        
    - Equipos físicos para terminales de check-in: $4.000.000 COP.
        
    - Presupuesto operativo para el piloto de 10 eventos en Cali: $3.000.000 COP.
        
    - Trámites de constitución y gastos legales: $2.000.000 COP.
        
    - Señalética física y material POP QR para salas: $1.500.000 COP.
        

### 5.3. Proyección Financiera Consolidada a Tres Años

|**Indicador Financiero**|**Año 1**|**Año 2**|**Año 3**|
|---|---|---|---|
|**Usuarios Activos al Cierre**|2.606|6.488|11.509|
|**Organizadores B2B Pagadores al Cierre**|11|44|96|
|**Ingresos Propios Totales**|**$4.953.800**|**$39.503.290**|**$103.754.390**|
|– _Pases VIP (B2C)_|$843.000 (17,0%)|$2.009.700 (5,1%)|$3.102.000 (3,0%)|
|– _Comisión Micro-mecenazgo_|$144.000 (2,9%)|$587.790 (1,5%)|$1.204.500 (1,2%)|
|– _Suscripciones B2B Dashboard_|**$3.196.800 (64,5%)**|**$33.777.800 (85,5%)**|**$93.034.500 (89,7%)**|
|– _Comisión Entradas Ticketeras_|$770.000 (15,5%)|$3.128.000 (7,9%)|$6.413.390 (6,2%)|
|**Costos Variables Totales**|$2.107.718|$8.798.146|$18.502.351|
|**Margen de Contribución**|$2.846.082 (57,5%)|$30.705.144 (77,7%)|$85.252.039 (82,2%)|
|**Gastos Fijos Anuales**|$164.394.320|$197.356.432|$233.739.555|
|**Resultado Operativo Anual (EBITDA)**|**-$161.548.238**|**-$166.651.288**|**-$148.487.516**|
|**Caja Final Acumulada**|**-$162.097.776**|**-$329.144.097**|**-$478.669.156**|

### 5.4. Realidad del Negocio y Punto de Equilibrio (_Break-Even_)

- **Dependencia B2B del Modelo:** Aunque la experiencia de usuario es B2C cultural, el modelo económico es predominantemente un **SaaS B2B**. Entre el 85% y 90% del flujo proyectado depende del pago mensual que hacen las salas y festivales por el _Dashboard_ analítico.
    
- **Ventas Requeridas para Punto de Equilibrio:** Kora requiere ingresar **$24.267.348 COP mensuales** para cubrir sus gastos operativos y nómina completa.
    
- **Capacidad Comercial Necesaria:** Equivale a mantener una base activa y recurrente de **145 organizadores B2B pagando $99.900 COP/mes** en el Año 1 (y 190 en el Año 3). La proyección estima 11 salas activas al término del primer año, por lo que el negocio opera en déficit durante el trienio si se mantiene la estructura salarial formalizada.
    
- **Costo de Adquisición de Clientes (CAC):**
    
    - _Usuario B2C:_ $2.040 COP (costo puramente publicitario derivado de Meta Ads); escalando a **$8.551 COP** si se le imputa el 20% del costo salarial del equipo enfocado en captación.
        
    - _Organizador B2B:_ **$4.979.859 COP** en el Año 1 (incluyendo el 40% del esfuerzo comercial del equipo y visitas directas), diluyéndose a $1.025.861 COP para el Año 3.
        

### 5.5. Necesidades de Capital

- **Aportes Iniciales de los Socios:** $30.000.000 COP.
    
- **Capital Requerido Año 1:** **$192.097.776 COP** ($30,5M CAPEX inicial + $161,6M de reserva de cobertura para meses deficitarios).
    
- **Financiación Adicional Faltante (Año 1):** **$162.097.776 COP**.
    
- **Financiación Total a 3 Años:** **$478.669.156 COP** de capital adicional acumulado.
    
- **TIR:** No determinable formalmente al no registrar flujos de caja netos positivos dentro del horizonte evaluado.
    

## 6. Matriz de Estado Actual, Brechas Críticas y Próximos Pasos

### 6.1. Estado de Validación Actual

1. **Deseabilidad B2C Validada:** Eficiencia de tráfico comprobada ($102 COP por resultado), nicho receptivo en adultos de 35 a 64 años e intención de compra en _Fake Doors_ (28% y 33%).
    
2. **Tracción Transaccional Inicial Lograda:** 71,4% de conversión en venta directa en campus ($4.000 COP) demostrando disposición a pagar presencial.
    
3. **Resiliencia Técnica Validada:** Prototipo en Netlify funcional, validación _offline-first_ para sótanos diseñada y esquema QR local cifrado estructurado.
    

### 6.2. Brechas Críticas Identificadas

- **Riesgo Unitario en Micropagos:** Pérdida financiera por cada aporte voluntario individual debido al cargo fijo de pasarela ($700 + % + IVA).
    
- **Falta de Validación del WTP B2B:** La sostenibilidad total del negocio recae en el cobro de $99.900 COP/mes a recintos; indicador que sigue catalogado como hipótesis sin contrato cerrado.
    
- **Presión de Nómina sobre Runway:** Los salarios de los fundadores representan el 85% del egreso fijo mensual, requiriendo esquemas de compensación por hitos para extender la vida útil del capital semilla.
    

### 6.3. Hoja de Ruta Inmediata

1. **Integración Transaccional en Vivo (Piloto Cali):** Implementar el botón transaccional vinculado a pasarela (Mercado Pago / Wompi) en el próximo toque en vivo para cobrar $4.000 COP por el sello exclusivo de Mecenas, evaluando si se sostiene la meta de conversión $\ge 10\%$ sobre los asistentes en recinto.
    
2. **Reestructuración de Mecenazgo:** Transicionar el micro-mecenazgo individual hacia un sistema de saldo prepagado o carga mínima para blindar el margen contra la tarifa de adquirencia.
    
3. **Validación Comercial B2B:** Presentar reportes analíticos de audiencia a 5 promotores en Cali para testear la firma de contratos piloto y validar el precio de reserva mensual del SaaS.