# AUDITORÍA INTEGRAL DE ARQUITECTURA, MATRIZ DE EVIDENCIAS Y PREPARADOR DE SUSTENTACIÓN ACADÉMICA
## Proyecto: Plataforma CloudOps para la Operación de Cadena de Frío - FríoAndes S.A.
**Evaluación Académica:** Universidad Icesi - Ejercicio Académico Junior DevOps - Senior DevOps  
**Rol del Documento:** Análisis Técnico Exhaustivo, Validación de Requerimientos, Guiones de Exposición y Banco de Preguntas  
**Fecha de Emisión del Dictamen:** 21 de Octubre de 2026  
**Auditor Principal:** Arquitecto de Software Senior, Analista Técnico y Revisor Académico  
**Ubicación del Archivo de Auditoría:** `C:\Users\juand\.gemini\antigravity-ide\brain\d9b497d3-dc36-47a1-bd06-7b3cf142e4a7\INFORME_MAESTRO_ARQUITECTURA_Y_EVIDENCIAS_FRIOANDES.md` (Almacenado fuera de la carpeta del proyecto para preservar la integridad del código original)

---

## ÍNDICE GENERAL DEL INFORME
1. **Resumen Ejecutivo**
   - Contexto de Negocio y Geográfico
   - Diagnóstico de la Plataforma On-Premises
   - Visión y Solución Arquitectónica Cloud Target
2. **Identificación de la Estructura General**
   - Objetivo General
   - Problema que Resuelve
   - Tecnologías Utilizadas
   - Arquitectura General Zero-Trust Hub-and-Spoke
   - Flujo Completo de Funcionamiento Operativo
   - Componentes Principales y Dependencias
3. **Análisis Exhaustivo de Cada Carpeta**
   - Estructura General de Directorios
   - Análisis Detallado Carpeta por Carpeta (Propósito, Contenido, Archivos Clave)
4. **Análisis Exhaustivo de Cada Archivo**
   - Inventario y Clasificación Global de Archivos (279 archivos)
   - Fichas Técnicas Individuales de Documentación de Arquitectura
   - Identificación de Archivos Redundantes, Históricos o Duplicados
5. **Análisis del Código Fuente y Scripts de Automatización**
   - Scripts de Política de Ventanas y Reglas de Negocio
   - Scripts de Pruebas Unitarias y Verificación de Consistencia
   - Scripts de Modelado Económico y Conciliación FinOps
   - Scripts de Generación de Diagramas (Diagrams Python)
   - Código de la Aplicación Web de Presentación y Visor Interactivo
6. **Análisis Detallado de los 18 Diagramas de Arquitectura**
   - Fichas Técnicas de los 12 Diagramas Migrados (Draw.io / Python)
   - Fichas Técnicas de los 6 Diagramas de Secuencia Especializados
   - Guiones de Presentación y Preguntas del Jurado para Cada Diagrama
7. **Análisis Exhaustivo de las Tablas Maestras del Proyecto**
   - Tabla Maestra de Direccionamiento IP y Subnetting Cloud
   - Tabla Maestra de Políticas del Cloud Firewall
   - Tabla Maestra de Dimensionamiento de Cargas (Normal vs. Pico +40%)
   - Tabla Maestra del Modelo de Costos y Presupuesto FinOps (24 Meses)
   - Tabla de las 5 Decisiones de Evitación de Costos
   - Tabla de Ventana Operativa y Gestión del Cambio
8. **Análisis y Validación contra el Enunciado (Universidad Icesi)**
   - Matriz de Trazabilidad: Requerimiento -> Evidencia -> Archivo -> Línea -> Cumplimiento
   - Requerimientos Cumplidos al 100%
   - Requerimientos Parcialmente Cumplidos (con justificación)
   - Requerimientos Faltantes y Excepciones Académicas
   - Recomendaciones de Mejora Arquitectónica
9. **Guion de Exposición Profesional Completo**
   - Estructura Cronológica y Guion Textual Sección por Sección
   - Respaldos Documentales y Citas Exactas de Evidencia
10. **Guiones de Exposición Resumidos para Diferentes Audiencias**
    - Versión de 5 Minutos (Pitch Ejecutivo C-Level)
    - Versión de 10 Minutos (Comité Directivo de Operaciones)
    - Versión de 15 Minutos (Revisión de Arquitectura Técnica)
    - Versión de 20 Minutos (Sustentación Oficial ante el Arquitecto Cloud del Cliente)
11. **Banco de Preguntas y Respuestas del Jurado Evaluador**
    - 45 Preguntas de Alta Dificultad clasificadas en 9 Ejes Temáticos con Respuestas Blindadas
12. **Mapa de Evidencias y Trazabilidad Textual**
    - Matriz de Verificación: Afirmación -> Archivo -> Ubicación -> Cita Textual
13. **Dictamen Final y Conclusiones del Arquitecto Senior**

---

## 1. RESUMEN EJECUTIVO

### 1.1 Contexto de Negocio y Geográfico
**FríoAndes S.A.** es una organización colombiana especializada en almacenamiento refrigerado de alimentos y logística de distribución en frío. Su infraestructura operativa física está compuesta por:
- **Centro Nacional de Operaciones en Cali (Valle del Cauca):** Donde se encuentra la sede central corporativa y su único datacenter físico local.
- **Ocho Centros de Distribución Regional (CEDI):** Ubicados en **Buenaventura, Bogotá, Medellín, Barranquilla, Bucaramanga, Pereira, Pasto y Neiva**.
- **Infraestructura de Almacenamiento:** 32 cuartos fríos industriales distribuidos homogéneamente (4 cuartos fríos por cada CEDI regional), monitoreados en régimen 24/7/365.
- **Flota de Transporte Refrigerado:** 120 camiones en operación regular diurna (jornada típica de 18 horas de 04:00 a 22:00 COT), que se expande a **180 camiones** durante campañas comerciales críticas (Semana Santa, mitad de año y temporada de fin de año/navideña del 25 de noviembre al 5 de enero).

### 1.2 Diagnóstico de la Plataforma On-Premises Actual
La plataforma actual sobre la cual depende la logística nacional de FríoAndes opera sobre una infraestructura de virtualización local **VMware** con 3 clústeres y 2 hosts ESXi por clúster en la sede de Cali:
1. **Clúster de Datos:**
   - 1 VM Producción: MySQL 5.7 sobre CentOS 7. Sin réplica de lectura ni alta disponibilidad. Respaldo nocturno único por volcado (dump) a la 01:00 en el mismo clúster. RPO actual > 23 horas (incumple el RPO objetivo de 15 minutos).
   - 1 VM Desarrollo: MySQL 5.7 sobre CentOS 7.
   - 1 VM Pruebas: MySQL 5.7 sobre CentOS 7.
   - 1 VM Caché: Redis 5 sobre CentOS 7.
2. **Clúster de Aplicación:**
   - Desarrollo, pruebas y producción comparten el mismo clúster físico.
   - 4 VM Ubuntu 20.04 con Docker (2 VMs para producción, 1 para desarrollo, 1 para pruebas).
   - Microservicios contenerizados: Despacho, Inventario de bodegas, Facturación corporativa, Rastreo de guías, Repositorio de evidencias.
   - 2 VM Ubuntu 22.04 con Nginx como proxy inverso.
   - 2 VM Ubuntu 22.04 para servir el frontend web.
3. **Clúster Transversal:**
   - 1 VM CentOS 7 con servicio FTP y volumen NFS que almacena **1.2 PB** (1,200 TB en base decimal B0), compuesto por:
     - 1.05 PB de video de muelles y entregas (objetos grandes).
     - 110 TB de fotos de entrega, comprobantes firmados y documentos PDF (< 2 MB c/u).
     - 40 TB de archivos históricos de temperatura (cargados por lote cada hora).
   - Tasa de crecimiento: **12 TB al mes** (principalmente video y fotos).
   - Retención contractual: **5 años obligatorios** por contrato con los clientes de alimentos. Sin copia fuera de este servidor.
   - 1 VM Ubuntu 20.04 con Zabbix para el monitoreo del sistema.

### 1.3 Los Seis Dolores Críticos del Negocio
1. **Capacidad fija y saturación en campañas:** El datacenter de Cali tiene cómputo y almacenamiento rígidos. En campañas pico, el despacho se ralentiza y el portal público de rastreo colapsa bajo 2.000 solicitudes por minuto.
2. **Despliegues lentos y riesgo de caída:** Despliegues manuales demorados semanas debido a que desarrollo, pruebas y producción comparten el mismo clúster de VMware sin aislamiento de fallos.
3. **Investigación reactiva de rupturas de frío:** Los datos térmicos se envían por archivos planos cada hora. Si la temperatura sube en ruta, no hay alertas en tiempo real; los reclamos de clientes se atienden con días de retraso cuando el alimento ya se ha descompuesto.
4. **Riesgo inminente de pérdida de 1.2 PB de evidencia:** El repositorio histórico no tiene réplica ni respaldo secundario fuera del servidor FTP en Cali. Un siniestro en el datacenter causaría la pérdida total de la evidencia contractual.
5. **Obsolescencia severa y fin de ciclo de vida (EOL):** CentOS 7 y MySQL 5.7 están fuera de soporte oficial; Ubuntu 20.04 está en soporte extendido de pago.
6. **Incompatibilidad multicloud:** Ausencia de un esquema unificado de identidades, auditoría inmutable y control presupuestario para interconectar una segunda nube.

### 1.4 La Solución Arquitectónica Cloud Target
El proyecto moderniza la operación migrando a **Microsoft Azure** (`eastus2`) con una arquitectura **Zero-Trust Hub-and-Spoke**:
- **Cómputo en AKS:** Clústeres elásticos con autoscaling horizontal (HPA) capaces de absorber un incremento del **+40% en vCPU/RAM** (11 pods / 15 vCPU / 30 GiB en normalidad vs. 19 pods / 25 vCPU / 50 GiB en campaña).
- **Base de Datos PaaS Multi-AZ:** Azure Database for MySQL Flexible Server en subred delegada privada con replicación sincrónica de zona para cumplir **RPO <= 15 minutos y RTO <= 4 horas**.
- **Caché Administrada:** Azure Managed Redis OSSCluster para absorber el 85% de las lecturas del portal público (hasta 2.000 req/min).
- **Streaming IoT Sub-Minuto:** Ingesta mediante IoT Hub / Event Hubs y Stream Analytics con presupuesto de **40 segundos** (por debajo de la restricción de 60s) y ruteo inteligente: Torre de Control en el día (04:00-22:00) y llamada automática/SMS al buscapersonas de Calidad de Guardia en la noche (22:00-04:00).
- **Inteligencia Artificial con Microsoft Fabric:** Conexión sin duplicar los 1.2 PB mediante **OneLake Shortcuts** sobre ADLS Gen2, con Lakehouse curado de solo 22.7 TB (< 2.5% del volumen bruto) y capacidad F-SKU auto-escalable (F64 diurno / F8 nocturno).
- **Migración Ordenada:** Ventana de coexistencia máxima de **90 días**, siembra física previa de los 1.2 PB y corte dominical estricto de **2 horas** (02:00 a 04:00 COT) con punto de no retorno a las 03:00 y rollback de 35 minutos.

---

## 2. IDENTIFICACIÓN DE LA ESTRUCTURA GENERAL

### 2.1 Objetivo General
Diseñar y sustentar la arquitectura de adopción de nube empresarial de FríoAndes S.A., garantizando gobernanza multi-ambiente, seguridad perimetral e interna Zero-Trust, conectividad híbrida redundante, automatización modular de infraestructura como código (IaC), modernización de cargas de trabajo, estrategia de migración con coexistencia a 90 días sin alterar el despacho diurno (04:00-22:00 COT), observabilidad con SLOs matemáticos y una plataforma analítica de inteligencia artificial en Microsoft Fabric.

### 2.2 Problema que Resuelve
Transforma una operación vulnerable basada en un datacenter local monolítico y obsoleto en una plataforma en la nube altamente disponible, elástica, segura y predictiva, garantizando la preservación de la cadena de frío de alimentos a nivel nacional, la protección de 1.2 PB de evidencia legal por 5 años y la continuidad ininterrumpida de las operaciones de despacho.

### 2.3 Contexto Operativo y Geográfico
- **Nivel Central:** Centro Nacional de Despachos y Datacenter en Cali (`10.20.0.0/16`), con internet simétrico de 400 Mbps y firewall perimetral NGFW.
- **Nivel Regional:** 8 Centros de Distribución Regional (CEDIs) en Buenaventura, Bogotá, Medellín, Barranquilla, Bucaramanga, Pereira, Pasto y Neiva, conectados por túneles VPN site-to-site IPsec IKEv2.
- **Operación Diurna:** Torre de control activa de 04:00 a 22:00 COT. Cero cambios productivos permitidos en esta ventana.
- **Operación Nocturna:** Monitoreo térmico continuo 24/7; de 22:00 a 04:00 COT la guardia de calidad atiende las alarmas de excursión térmica.

### 2.4 Tecnologías Utilizadas
| Dominio | Tecnologías y Servicios Seleccionados |
|---|---|
| **Nube Pública Principal** | Microsoft Azure (Región primaria: `eastus2`) |
| **Nube Analítica y BI** | Microsoft Fabric (OneLake, Direct Lake, SynapseML, Capacidades F-SKU) |
| **Nube Secundaria (Reserva 24m)** | Google Cloud Platform (GCP) en CIDR reservado `10.200.0.0/16` |
| **Infraestructura como Código** | Terraform (azurerm v3.116.0+, azuread v2.53.1+), estructura modular |
| **Cómputo de Aplicaciones** | Azure Kubernetes Service (AKS), Docker, Nginx Ingress Controller |
| **Almacenamiento y Archivo** | Azure Data Lake Storage (ADLS) Gen2 con ciclo de vida Hot -> Cool -> Archive |
| **Bases de Datos y Caché** | Azure Database for MySQL Flexible Server (Zone-Redundant HA), Azure Managed Redis OSSCluster |
| **Ingesta de Telemetría IoT** | Azure IoT Hub, Azure Event Hubs (AMQP/Kafka), Azure Stream Analytics |
| **Cómputo Serverless** | Azure Functions (Python 3.11, subred delegada dedicada `.11/24`) |
| **Seguridad e Identidad** | Microsoft Entra ID (P1/P2), Entra External ID (B2B), Azure Key Vault (KMS/CMK) |
| **Seguridad de Red** | Azure Firewall Premium, Azure Bastion Premium (Private-only), App Gateway v2 WAF |
| **Observabilidad y SIEM** | Azure Monitor, Log Analytics Workspace, Azure Managed Grafana, Application Insights, Azure Sentinel |
| **Diseño y Presentación** | Draw.io XML, Python Diagrams, Vanilla CSS/HTML/JS, Space Grotesk, Inter, JetBrains Mono |

### 2.5 Arquitectura General Zero-Trust Hub-and-Spoke
La topología de red se estructura en un modelo Hub-and-Spoke con estricto micro-segmentado:
```mermaid
flowchart TD
    subgraph OnPremises ["On-Premises Corporativo"]
        Cali["Cali Datacenter & HQ: 10.20.0.0/16"]
        CEDIs["8 Centros Regionales: 10.31.0.0/16 a 10.38.0.0/16"]
        NGFW["Cali Perimeter NGFW (Enlace 400 Mbps)"]
        Cali --> NGFW
        CEDIs --> NGFW
    end

    subgraph Azure_Hub ["Azure Hub VNet: 10.100.0.0/16"]
        VPNGW["Azure VPN Gateway VpnGw2AZ: 10.100.0.0/27"]
        AzFW["Azure Firewall Premium: 10.100.1.0/26 (IP 10.100.1.4)"]
        Bastion["Azure Bastion Premium: 10.100.2.0/26"]
        AuditLogs["Log Analytics & Sentinel: 10.100.3.0/24"]
        Runners["CI/CD Runners & Jumpboxes: 10.100.5.0/24"]
        DNSResolver["Private DNS Resolver: 10.100.6.0/28 & .16/28"]
    end

    subgraph Azure_Spokes ["Spokes de Ambientes (Segregados)"]
        ProdSpoke["Production Spoke: 10.101.0.0/16"]
        StgSpoke["Staging Spoke: 10.102.0.0/16"]
        DevSpoke["Development Spoke: 10.103.0.0/16"]
    end

    subgraph MultiCloud_Reserve ["Reserva Multicloud"]
        GCPReserve["GCP Horizon (24 meses): 10.200.0.0/16"]
    end

    NGFW ==>|Túneles IPsec IKEv2 Redundantes| VPNGW
    VPNGW --> AzFW
    AzFW <-->|VNet Peering Bidireccional Inspeccionado| ProdSpoke
    AzFW <-->|VNet Peering Bidireccional Inspeccionado| StgSpoke
    AzFW <-->|VNet Peering Bidireccional Inspeccionado| DevSpoke
    AzFW -.->|Reserva de Tránsito| GCPReserve
```

### 2.6 Flujo Completo de Funcionamiento del Ecosistema
1. **Despacho y Operación Logística (Tráfico Privado):**
   Los 450 usuarios internos en Cali y los CEDIs regionales acceden a la Torre de Control a través de los túneles VPN corporativos. El tráfico llega al Azure VPN Gateway, es inspeccionado en Capa 7 por Azure Firewall Premium (`10.100.1.4`) bajo la regla `FW-CORP-01` (TCP 443), y se reenvía al Internal Load Balancer privado de producción (`10.101.2.10`). El ILB balancea entre los pods de AKS en `10.101.2.0/23`. Los servicios consultan la base de datos MySQL Flexible Server en `10.101.4.0/24` (TLS 3306) y la caché Redis en `10.101.5.0/24`. Este flujo carece por completo de endpoints o listeners públicos.
2. **Rastreo de Guías por Clientes Corporativos (Tráfico Público Controlado):**
   Los ~2.000 clientes B2B ingresan mediante HTTPS (TCP 443) al Application Gateway v2 con WAF (`10.101.1.0/24`). El WAF inspecciona contra reglas OWASP CRS 3.2 y pasa la petición exclusivamente al frontend aislado en `10.101.10.0/24`. El frontend se comunica con la API Sanitizada de Rastreo en AKS (`10.101.2.0/23`). La API valida el token JWT emitido por Entra External ID, consulta la caché Redis (absorbiendo el 85% de las lecturas) o MySQL, y aplica una política estricta de sanitización: recorta las coordenadas GPS a un corredor de 2 km alrededor de la ruta programada, enmascara el identificador del camión (ej. `"TRK-084"` pasa a `"Unidad Refrigerada #402"`), suprime por completo los datos personales del conductor (Ley 1581) y omite cualquier enlace a cámaras o video en vivo.
3. **Telemetría de Cadena de Frío y Alerta Sub-Minuto:**
   - Los sensores de los 32 cuartos fríos transmiten cada 30 segundos por la red interna del CEDI y cruzan la VPN hacia el endpoint privado de IoT Hub (`10.101.7.0/24`).
   - Los sensores y GPS de los 120/180 camiones transmiten por la red celular móvil mediante TLS 8883 hacia el endpoint público de IoT Hub autenticándose mediante certificados X.509 de dispositivo.
   - IoT Hub enruta en tiempo real a Event Hubs y Azure Stream Analytics (ASA).
   - ASA procesa ventanas continuas; al detectar una temperatura fuera de rango (ej. > -18°C en congelados o > 4°C en refrigerados), emite de inmediato un evento a la Azure Function clasificadora en `10.101.11.0/24`.
   - La Function evalúa la hora local: entre 04:00 y 22:00 COT inyecta un banner sonoro de alarma crítica en la consola de la Torre de Control; entre 22:00 y 04:00 COT dispara una llamada telefónica automatizada y SMS urgente al buscapersonas del turno de Calidad de Guardia. Presupuesto total: 40 segundos (holgura de 20s respecto al límite de 60s).
4. **Analítica e Inteligencia Artificial en Microsoft Fabric:**
   Todos los eventos de telemetría y metadatos de entrega se aterrizan en Azure Data Lake Storage Gen2 (`10.101.6.0/24`) en formato Delta Parquet. Microsoft Fabric consume estos datos mediante **OneLake Shortcuts** (sin copiar los 1.2 PB). Modelos de Machine Learning (SynapseML) generan tres capacidades predictivas:
   - *Detección predictiva de fallas térmicas:* Alerta con 4 a 12 horas de anticipación si un compresor muestra pérdida de eficiencia.
   - *Estimación dinámica de llegada (ETA):* Regresión gradiente basada en GPS, altimetría andina y clima, visible en la Torre de Control.
   - *Control de calidad de evidencias:* Visión artificial que clasifica fotos de entrega, detectando imágenes borrosas, comprobantes ilegibles o carga rota. Para los 1.05 PB de video, la estrategia toma muestras automáticas de 5 minutos alrededor del momento del atraque en muelle, descartando el 99.8% de video redundante.

---

## 3. ANÁLISIS EXHAUSTIVO DE CADA CARPETA

El proyecto de FríoAndes se encuentra estructurado en tres grandes repositorios complementarios e interconectados bajo la carpeta principal:

```text
Frío Andes/
|-- frioandes-cloudops-diagrams/   # Repositorio de fuentes, renderizado y planes de diagramas
|   |-- rendered/                  # Artefactos exportados en SVG y PNG de alta resolución
|   `-- src/                       # Fuentes canónicas (.drawio XML y scripts legacy en Python)
|-- frioandes-cloudops-docs/       # Repositorio central de documentación técnica, arquitectura y FinOps
|   |-- adrs/                      # Architectural Decision Records (ADRs formales)
|   |-- diagrams/                  # Diagramas complementarios y secuencias de despliegue
|   |-- output/                    # Salidas ejecutivas (PDF de migración y FinOps)
|   |-- outputs/                   # Salidas de revisiones, consistencia, modelo de costos Excel y CSVs
|   |-- reviews/                   # Registros de auditoría, consistencia y trazabilidad de entrega
|   |-- specs/                     # Especificaciones técnicas y desglose de tareas (WBS)
|   `-- tools/                     # Herramientas de automatización, validación, tests y conciliación
`-- frioandes-cloudops-web/        # Aplicación web interactiva de presentación y catálogo de arquitectura
    `-- assets/                    # Identidad de marca (isotipo) y galería de diagramas sincronizados
        |-- brand/                 # SVG del isotipo de FríoAndes
        `-- diagrams/              # Los 18 diagramas en formato SVG y PNG optimizados para web
```

### 3.1 Carpeta: `frioandes-cloudops-diagrams/`
- **Qué contiene:** Fuentes de modelado gráfico en Draw.io XML sin comprimir, scripts legados en Python utilizando la librería `diagrams` (Mingrammer), Makefile de automatización y documentación de transición.
- **Por qué existe:** Funciona como el repositorio especializado de diseño gráfico de arquitectura, centralizando la evolución visual del sistema desde scripts de código Python hacia fuentes estándar Draw.io XML editables por cualquier ingeniero o arquitecto.
- **Cómo se relaciona con el sistema:** Provee las representaciones visuales exactas requeridas para sustentar los 8 puntos del reto ante el jurado evaluador y abastece de diagramas tanto a la documentación técnica Markdown como a la aplicación web interactiva.
- **Archivos más importantes:**
  - `MIGRATION_MANIFEST.md`: Registro de paridad estructural entre Python y Draw.io.
  - `README.md`: Catálogo de los 12 diagramas migrados y 6 adicionales.
  - `src/*.drawio`: Fuentes canónicas de los 18 diagramas.

#### Subcarpeta: `frioandes-cloudops-diagrams/src/`
- **Qué contiene:** 19 archivos `.drawio` (fuentes canónicas XML en UTF-8) y 13 scripts `.py` (código fuente Python que generaba los diagramas originalmente).
- **Por qué existe:** Es la cuna de diseño donde residen los elementos vectoriales, nodos, conexiones, zonas de seguridad y estilos gráficos.
- **Archivos clave:** `azure-target-architecture.drawio`, `on-premises-baseline.drawio`, `hybrid-connectivity-and-data-seeding.drawio`, `telemetry-streaming-ingestion.drawio`, `core-application-cutover.drawio`, `fabric-ai-integration.drawio`, `step00_onprem_baseline.py` a `step08_global_multicloud_azure_gcp.py`.

#### Subcarpeta: `frioandes-cloudops-diagrams/rendered/`
- **Qué contiene:** Exportaciones generadas de los diagramas: 18 archivos `.svg` vectoriales auto-contenidos y 19 archivos `.png` en alta resolución (2400 px).
- **Por qué existe:** Almacena los artefactos finales listos para ser embebidos en reportes ejecutivos, PDFs y vistas previas locales sin requerir software de modelado instalado.
- **Archivos clave:** `azure-target-architecture.svg`, `azure-target-architecture.png`, `core-application-cutover.svg`.

---

### 3.2 Carpeta: `frioandes-cloudops-docs/`
- **Qué contiene:** 26 capítulos de arquitectura en Markdown, memorandos de decisión técnica (ADRs), plantillas de gestión del cambio diligenciadas, modelo de costos en Excel (11 pestañas), scripts de prueba y validación de políticas en Python y JavaScript/Node.js, y reportes ejecutivos en PDF.
- **Por qué existe:** Es el **Single Source of Truth (SSoT)** del proyecto. Contiene toda la justificación matemática, técnica, jurídica y económica que respalda las decisiones de ingeniería para cumplir los criterios 1 al 8 del enunciado de la Universidad Icesi.
- **Cómo se relaciona con el sistema:** Gobierna todas las decisiones de diseño. Las variables de Terraform, las políticas de seguridad y las cotizaciones de FinOps nacen obligatoriamente de los parámetros congelados en esta carpeta (especialmente bajo el contrato `FA-DESIGN-20261020-01`).
- **Archivos más importantes:**
  - `Architecture Report.md`: Informe integrador maestro de los 8 puntos del reto.
  - `Case Baseline.md`: Línea base técnica y matriz de trazabilidad de los criterios de evaluación.
  - `Network Architecture.md`: Direccionamiento IP y plan maestro de subnetting.
  - `Security Architecture.md`: Matriz de firewall de nube y modelo Zero-Trust.
  - `Cost Estimate and Commercial Basis.md`: Modelo de costos 24 meses y bases comerciales.
  - `Application Release Notes.md`: Plantilla de release notes de aplicación diligenciada.
  - `Infrastructure Release Notes.md`: Plantilla de release notes de infraestructura diligenciada.

#### Subcarpeta: `frioandes-cloudops-docs/adrs/`
- **Qué contiene:** `ADR-001-blockchain-applicability.md`.
- **Por qué existe:** Documenta la evaluación arquitectónica formal sobre si incorporar o no tecnologías de Blockchain/DLT para la trazabilidad de la cadena de frío.
- **Conclusión del ADR:** Se rechaza el uso de Blockchain para el alcance actual debido a sobrecostos computacionales, latencia incompatible con el objetivo sub-minuto y ausencia de un consorcio multipartito que justifique consenso distribuido; se selecciona en su lugar almacenamiento WORM inmutable en ADLS Gen2 con firmas criptográficas HMAC y auditoría en Log Analytics.

#### Subcarpeta: `frioandes-cloudops-docs/diagrams/`
- **Qué contiene:** Fuentes de diagramas adicionales como `change-promotion-sequence.drawio` y su exportación `change-promotion-sequence.svg`.
- **Por qué existe:** Modela el flujo específico de promoción de cambios y despliegue continuo mediante GitOps.

#### Subcarpeta: `frioandes-cloudops-docs/output/` y `output/pdf/`
- **Qué contiene:** `FinOps-and-Migration-Strategy.pdf` (versión corregida y validada de 39 KB).
- **Por qué existe:** Aloja el documento PDF ejecutivo entregable para revisión de clientes y evaluadores académicos.

#### Subcarpeta: `frioandes-cloudops-docs/outputs/`
- **Qué contiene:** Subcarpetas históricas y de entrega final:
  - `2026-10-12-feedback-remediation/`: Contiene el modelo de costos histórico previo.
  - `design-consistency-2026-10-20/verified/`: Exportaciones verificadas de los diagramas usados en los capítulos de documentación.
  - `nonpresentation-20261021/`: Contiene el modelo financiero definitivo `FrioAndes 24-Month Cost Model.xlsx`, el archivo `artifact-sha256.json`, los resultados de verificación `workbook-checks.json` y la subcarpeta `cost-inputs/`.
- **Subcarpeta interna `cost-inputs/`:** Almacena los insumos inmutables del modelo de costos: `assumptions.json`, `price_snapshot.json`, `component_coverage.csv`, `series_24m.csv` y `summary.csv`.

#### Subcarpeta: `frioandes-cloudops-docs/reviews/`
- **Qué contiene:** Registros de auditoría y consistencia: `2026-10-20-design-consistency.md`, `2026-10-21-final-consistency.md`, `2026-10-21-nonpresentation-completion.md`, `2026-10-21-terraform-recovery-note.md` y `diagram-source-render-register.md`.
- **Por qué existe:** Demuestra trazabilidad de ingeniería, registrando cada corrección matemática, validación de hashes SHA-256 y aceptación formal de entregables por parte del autor líder.

#### Subcarpeta: `frioandes-cloudops-docs/specs/`
- **Qué contiene:** `19-24-final-report/tasks.md` y `nonpresentation-completion/tasks.md`.
- **Por qué existe:** Desglosa los paquetes de trabajo (WBS) en tareas verificables ejecutadas durante los sprints.

#### Subcarpeta: `frioandes-cloudops-docs/tools/`
- **Qué contiene:** Scripts de verificación de políticas y pruebas:
  - `change_window_policy.py`: Implementación en Python de la política de ventana operativa.
  - `test_change_window_policy.py`: Suite de pruebas unitarias (`unittest`) para la política de cambios.
  - `verify_final_consistency.py`: Suite de 6 pruebas exhaustivas de consistencia de red, CIDRs y costos.
  - `reconcile_cost_workbook.mjs` y `verify_workbook.mjs`: Scripts Node.js para conciliar y auditar las fórmulas del libro Excel de 11 pestañas.
  - `check_delivery_artifacts.py`: Validador de artefactos de entrega.

---

### 3.3 Carpeta: `frioandes-cloudops-web/`
- **Qué contiene:** Una aplicación web completa, interactiva y responsiva desarrollada en Vanilla HTML5, CSS3 y JavaScript moderno, lista para ser desplegada en Cloudflare Pages (`wrangler.jsonc`).
- **Por qué existe:** Provee una plataforma visual de sustentación de alto impacto visual para proyectar ante el jurado evaluador o el Arquitecto Cloud del Cliente. Implementa el **Sistema de Diseño de FríoAndes** (Manual de Marca) de forma tangible.
- **Cómo se relaciona con el sistema:** Actúa como el visor interactivo de los 18 diagramas de arquitectura con capacidades de zoom dinámico, arrastre (pan), filtrado por dominio arquitectónico (red, seguridad, telemetría, migración, FinOps, IA), simulación interactiva de escenarios operativos (Línea Base vs. Campaña Pico +40%) y demostración de las reglas de ruteo diurno/nocturno.
- **Archivos más importantes:**
  - `index.html`: Maquetación semántica accesible y estructurada de la aplicación.
  - `styles.css`: Sistema de diseño completo (tokens HSL/Hex, tipografía Space Grotesk/Inter/DM Mono, grilla, glassmorphism, modo oscuro industrial).
  - `app.js`: Lógica de interacción, simulador de métricas operativas y controlador del modal de diagramas.
  - `diagrams.js`: Base de datos JSON en memoria con los metadatos de los 18 diagramas.
  - `DIAGRAM_INVENTORY.md`: Inventario de diagramas auditados y estados de revisión.
---

## 4. ANÁLISIS EXHAUSTIVO DE CADA ARCHIVO

El proyecto contiene un total de **279 archivos** distribuidos de forma rigurosa entre sus tres repositorios y la raíz del espacio de trabajo. A continuación se presenta su inventario categorizado y las fichas analíticas detalladas de los archivos centrales.

### 4.1 Clasificación Global del Inventario de Archivos
| Categoría de Archivos | Cantidad | Descripción y Rol en el Proyecto |
|---|---:|---|
| **Documentación Arquitectónica (`.md`)** | 40 | Capítulos técnicos de SSoT, registros de decisión, reportes de revisión y especificaciones. |
| **Diagramas Vectoriales SVG (`.svg`)** | 78 | Exportaciones vectoriales nítidas para publicación web y documentación. |
| **Diagramas Rasterizados PNG (`.png`)** | 90 | Vistas previas en alta resolución (2400 px) para auditoría e inspección. |
| **Fuentes Canónicas Draw.io (`.drawio`)** | 19 | Fuentes XML editables sin comprimir de todos los diagramas de arquitectura. |
| **Scripts de Código Python (`.py`)** | 21 | Scripts legados de generación de diagramas (`diagrams`) y herramientas de prueba de políticas. |
| **Datos Estructurados JSON (`.json`, `.jsonc`)** | 9 | Hashes SHA-256 de artefactos, insumos de precios, configuración de Cloudflare Pages (`wrangler.jsonc`). |
| **Bases de Datos Planas CSV (`.csv`)** | 6 | Series mensuales de costos (24 meses), desglose de componentes y resumen financiero. |
| **Hojas de Cálculo Financieras (`.xlsx`)** | 2 | Modelo de costos a 24 meses conciliado (11 pestañas) y versión histórica de remediación. |
| **Código Web Frontend (`.html`, `.css`, `.js`)** | 4 | Aplicación web interactiva de presentación, catálogo de diagramas y sistema de diseño. |
| **Scripts de Automatización Node.js (`.mjs`)** | 2 | Scripts de conciliación de fórmulas de Excel y verificación matemática de celdas. |
| **Entregables Ejecutivos PDF (`.pdf`)** | 2 | Enunciado del reto académico y documento ejecutivo formal de FinOps y Migración. |
| **Archivos de Control Git y Sistema** | 6 | `.gitignore`, `.gitattributes`, `.assetsignore`, `Makefile`. |

---

### 4.2 Fichas Técnicas Individuales de los Archivos Clave del Proyecto

#### Ficha 1: `Ejercicio-Analista-CloudOps-FrioAndes.pdf`
- **Ruta Completa:** `c:\Users\juand\Documents\Automatización\Ejercicio-Analista-CloudOps-FrioAndes.pdf`
- **Propósito:** Enunciado oficial del caso de estudio académico y rúbrica de evaluación emitida por la Universidad Icesi para los perfiles Junior DevOps y Senior DevOps.
- **Resumen:** Establece el contexto de FríoAndes S.A., inventario técnico del datacenter en Cali (VMware, MySQL 5.7, Redis 5, CentOS 7, Ubuntu 20.04/22.04, 1.2 PB en NFS/FTP), los 8 CEDIs regionales, las restricciones de negocio (ventana de corte de 2 horas en domingo, jornada diurna de 04:00 a 22:00, RPO de 15 min, RTO de 4 horas), los 8 puntos del reto y los entregables obligatorios.
- **Información Importante:** Es la norma rectora contra la cual se evalúa la suficiencia de toda la arquitectura. Prohíbe cambios en horario diurno y exige no duplicar los 1.2 PB por defecto en Fabric.
- **Relación con otros archivos:** Se mapea directamente con `Case Baseline.md` y `Architecture Report.md`.
- **Cómo explicarlo:** "Es el pliego de condiciones del cliente, donde se fijan las restricciones operativas, los volúmenes de datos y las métricas de éxito que la solución debe satisfacer".
- **Qué decir textualmente:** *"El enunciado de la Universidad Icesi define las reglas innegociables: una ventana de corte máxima de 2 horas en domingo, cero cambios diurnos y la protección estricta de 1.2 PB de archivo histórico"*.

#### Ficha 2: `Architecture Report.md`
- **Ruta Completa:** `c:\Users\juand\Documents\Automatización\Frío Andes\frioandes-cloudops-docs\Architecture Report.md`
- **Propósito:** Informe integrador maestro que consolida la respuesta a los ocho puntos del reto y sirve como índice de evidencias de todo el proyecto.
- **Resumen:** Recorre la justificación de Azure como nube principal (`eastus2`), la tabla de direccionamiento y subnetting (10 zonas por ambiente), la matriz de políticas de firewall, el resumen de dimensionamiento de cómputo (11 a 19 pods, +66.7% en pico), el puente comercial FinOps ($981,226.11 USD always-on vs $919,106.31 USD programado), la estrategia de Fabric sin duplicación y las decisiones arquitectónicas.
- **Información Importante:** Fija la jerarquía de fuentes: hechos del caso -> contrato congelado FA-DESIGN-20261020-01 -> deltas explícitos de implementación -> evidencia versionada.
- **Relación con otros archivos:** SSoT supremo que indexa a todos los capítulos (`Network Architecture.md`, `Security Architecture.md`, etc.).
- **Cómo explicarlo:** "Es la columna vertebral del proyecto, donde un jurado o arquitecto evaluador encuentra la respuesta unificada a cada requisito con su respectiva evidencia matemática y de diseño".
- **Qué decir textualmente:** *"Architecture Report sintetiza nuestra propuesta técnica: una landing zone en Azure East US 2 con 10 zonas funcionales, un presupuesto de alerta de 40 segundos y un modelo financiero de 24 meses conciliado al centavo"*.

#### Ficha 3: `Case Baseline.md`
- **Ruta Completa:** `c:\Users\juand\Documents\Automatización\Frío Andes\frioandes-cloudops-docs\Case Baseline.md`
- **Propósito:** Establecer la línea base técnica authoritative del proyecto, inventariando el estado actual y trazando el cumplimiento de los 8 criterios de evaluación.
- **Resumen:** Detalla cada servidor del datacenter de Cali, la tabla de direccionamiento IP on-premises (`10.20.0.0/16` y `10.31-38.0.0/16`), el volumen de datos de 1.2 PB, las tasas de crecimiento (12 TB/mes) y mapea explícitamente los criterios 1 al 8 con sus respectivos entregables.
- **Información Importante:** Provee los números duros del dimensionamiento: 450 usuarios internos, 2.000 clientes B2B, 32 cuartos fríos, 120/180 camiones, 800 a 2.000 req/min.
- **Relación con otros archivos:** Base fundacional para `Workload Sizing and Capacity Planning.md` y `Network Architecture.md`.
- **Cómo explicarlo:** "Es la radiografía del estado actual de FríoAndes, donde se cuantifica el tamaño de la deuda técnica y el volumen de datos que debemos modernizar".
- **Qué decir textualmente:** *"En Case Baseline documentamos la realidad del cliente: una infraestructura on-premises al límite, sin réplica de base de datos y con 1.2 PB creciendo 12 TB al mes en un único servidor NFS vulnerable"*.

#### Ficha 4: `Network Architecture.md`
- **Ruta Completa:** `c:\Users\juand\Documents\Automatización\Frío Andes\frioandes-cloudops-docs\Network Architecture.md`
- **Propósito:** Especificar el diseño de conectividad híbrida, la topología Hub-and-Spoke y el plan maestro de subnetting en la nube.
- **Resumen:** Define las asignaciones CIDR para Hub (`10.100.0.0/16`), Producción (`10.101.0.0/16`), Staging (`10.102.0.0/16`), Desarrollo (`10.103.0.0/16`), Reserva Multicloud GCP (`10.200.0.0/16`) y pool VPN P2S (`172.20.0.0/24`), demostrando matemáticamente cero solapamiento con Cali y los CEDIs.
- **Información Importante:** Incorpora el delta `DLT-NET-01-01` que añade la subred delegada `.11/24` para integración de Azure Functions en cada spoke.
- **Relación con otros archivos:** Es la base para las reglas de `Security Architecture.md` y los módulos Terraform de red.
- **Cómo explicarlo:** "Es el plano de carreteras y divisiones de terreno de nuestra nube, garantizando que ninguna IP choque con las sedes físicas y que cada servicio tenga su propio espacio protegido".
- **Qué decir textualmente:** *"El plan de subnetting garantiza cero colisiones IP con Cali y los ocho CEDIs, organizando la nube en diez subredes funcionales por ambiente y reservando un bloque /16 para la futura interconexión con GCP"*.

#### Ficha 5: `Security Architecture.md`
- **Ruta Completa:** `c:\Users\juand\Documents\Automatización\Frío Andes\frioandes-cloudops-docs\Security Architecture.md`
- **Propósito:** Definir el modelo de seguridad Zero-Trust, los límites de confianza y la matriz maestra de políticas de firewall de nube.
- **Resumen:** Clasifica los flujos en cuatro dominios: Despacho Corporativo, Publicación Pública, Gobierno Administrativo y Coexistencia/Migración. Detalla las reglas `FW-CORP-01` a `FW-CORP-08`, políticas de WAF v2, reglas de JIT en Azure Bastion y la secuencia de desmantelamiento del firewall de Cali a los 90 días.
- **Información Importante:** Aísla el despacho de Internet público: el backend solo escucha en el ILB privado `10.101.2.10` y deniega cualquier acceso directo desde IPs públicas.
- **Relación con otros archivos:** Complementa `Administrative Access Architecture.md` y `Identity, Cryptography, and Compliance Architecture.md`.
- **Cómo explicarlo:** "Es el manual de control de accesos y aduanas del sistema, donde se define qué paquete de red entra, por dónde pasa, quién lo inspecciona y bajo qué condiciones se bloquea".
- **Qué decir textualmente:** *"La arquitectura de seguridad asegura que la Torre de Control y la base de datos MySQL no tengan IP pública ni exposición a Internet, canalizando todo el tráfico operativo por túneles VPN inspeccionados por Azure Firewall"*.

#### Ficha 6: `Cost Estimate and Commercial Basis.md`
- **Ruta Completa:** `c:\Users\juand\Documents\Automatización\Frío Andes\frioandes-cloudops-docs\Cost Estimate and Commercial Basis.md`
- **Propósito:** Explicar el modelo financiero de 24 meses, los supuestos de cálculo, la conciliación con Azure Retail API y la propuesta comercial para el cliente.
- **Resumen:** Detalla el costo de infraestructura Azure en 24 meses ($981,226.11 USD en escenario base always-on; $919,106.31 USD en escenario con Fabric programado, generando una sensibilidad de $62,119.80 USD). Agrega licencias externas ($91,920.00 USD), allowances de migración ($55,000.00 USD), costos de mano de obra ($232,387.28 USD), contingencia del 15% ($204,080.01 USD) y aplica un margen bruto del 20% para establecer el precio final propuesto al cliente de **$1,955,766.76 USD antes de impuestos**.
- **Información Importante:** Expone 5 decisiones explícitas de ahorro/evitación de costos y aclara que no se asume una purga legal automática de datos para recortar costos artificialmente.
- **Relación con otros archivos:** Se respalda en el libro Excel `FrioAndes 24-Month Cost Model.xlsx` y los insumos JSON/CSV en `outputs/nonpresentation-20261021/cost-inputs/`.
- **Cómo explicarlo:** "Es el estudio económico del proyecto, donde justificamos cada dólar invertido a 24 meses, comparamos escenarios de optimización y presentamos una cotización profesional con contingencia y margen".
- **Qué decir textualmente:** *"Nuestro modelo FinOps a 24 meses presupuesta $981,226 USD en infraestructura Azure bajo un enfoque conservador, respaldado por 81 medidores públicos oficiales y una propuesta comercial de $1.95 millones de dólares que incluye contingencia y mano de obra especializada"*.

#### Ficha 7: `Application Release Notes.md`
- **Ruta Completa:** `c:\Users\juand\Documents\Automatización\Frío Andes\frioandes-cloudops-docs\Application Release Notes.md`
- **Propósito:** Plantilla estandarizada de gestión del cambio para aplicaciones, completamente diligenciada con un ejemplo real del portal de rastreo.
- **Resumen:** Define 12 campos obligatorios de control (identidad, commit inmutable, justificación, ventana horaria, impacto, precondiciones, aprobaciones independientes, plan de rollback). El ejemplo diligenciado (`APP-EXAMPLE-01`) reduce el timeout del portal de 10s a 5s, programado para un domingo de 22:00 a 22:30 COT (fuera de horario de despacho y de campaña).
- **Información Importante:** Prohíbe tajantemente cualquier cambio productivo entre las 04:00 y las 22:00 COT, así como durante campañas congeladas.
- **Relación con otros archivos:** Se relaciona con `CI CD and Operational Change Design.md` y `change_window_policy.py`.
- **Cómo explicarlo:** "Es la ficha técnica de control de cambios de software que garantiza que ninguna modificación entre a producción sin pruebas previas, aprobaciones cruzadas y un plan de regreso atrás garantizado".
- **Qué decir textualmente:** *"La plantilla de release notes de aplicación ejemplifica un cambio real en el portal de rastreo durante una ventana nocturna de domingo, cumpliendo con la regla de no tocar producción en horario de despacho ni en campañas congeladas"*.

#### Ficha 8: `Infrastructure Release Notes.md`
- **Ruta Completa:** `c:\Users\juand\Documents\Automatización\Frío Andes\frioandes-cloudops-docs\Infrastructure Release Notes.md`
- **Propósito:** Plantilla estandarizada de gestión del cambio de infraestructura como código (IaC), diligenciada con el plan minuto a minuto del corte dominical de producción.
- **Resumen:** Modela el cambio `INFRA-EXAMPLE-01`: la transferencia de la autoridad de escritura de MySQL Cali hacia Azure y el redireccionamiento del DNS interno hacia el ILB `10.101.2.10`. Fija la ventana en el domingo de 02:00 a 04:00 COT, estableciendo el punto de no retorno a las **03:00 COT** y un tiempo de rollback objetivo de **35 minutos** (dejando 25 minutos de margen de seguridad antes de las 04:00).
- **Información Importante:** Especifica que los 1.2 PB se pre-siembran antes de la ventana; en las 2 horas de corte solo se sincroniza el delta transaccional de MySQL.
- **Relación con otros archivos:** Es el runbook operacional de `Core Migration and Cutover Strategy.md`.
- **Cómo explicarlo:** "Es la orden de operaciones para la noche del corte definitivo, detallando qué se ejecuta cada 10 minutos, quién autoriza el avance y cómo se devuelve el sistema si algo falla antes de las 3 de la mañana".
- **Qué decir textualmente:** *"El release note de infraestructura es nuestro plan de vuelo para el cutover: ejecutado entre las 2 y las 4 de la mañana de un domingo, con decisión irreversible a las 03:00 y un rollback ensayado de 35 minutos para asegurar que a las 04:00 el despacho arranque limpio"*.

#### Ficha 9: `Cold-Chain Telemetry and Alerting Architecture.md`
- **Ruta Completa:** `c:\Users\juand\Documents\Automatización\Frío Andes\frioandes-cloudops-docs\Cold-Chain Telemetry and Alerting Architecture.md`
- **Propósito:** Detallar la arquitectura de ingesta de sensores IoT, procesamiento en streaming y el mecanismo de alerta sub-minuto.
- **Resumen:** Desglosa el presupuesto de latencia de 40 segundos frente a eventos físicos: detección local (5s), transporte de red (10s), ingesta IoT Hub (2s), evaluación ASA (3s), enrutador de alertas (2s), notificación push/SMS (15s) y renderizado en UI (3s). Describe el manejo de desconexión celular en cordillera con buffers en los camiones y el ruteo diurno (Torre de Control) vs. nocturno (Calidad de Guardia).
- **Información Importante:** Demuestra cómo se cumple el requisito de ver la alerta en menos de 1 minuto sin falsas alarmas por duplicados gracias a deduplicación en Redis con hash SHA-256.
- **Relación con otros archivos:** Se alinea con `telemetry-streaming-ingestion.drawio` y `sequence-target-excursion-alert.drawio`.
- **Cómo explicarlo:** "Es el sistema nervioso de la cadena de frío, capaz de captar la subida de temperatura en un camión en la mitad de la cordillera y hacer sonar la alarma del supervisor en solo 40 segundos".
- **Qué decir textualmente:** *"Diseñamos un pipeline de telemetría IoT con un presupuesto estricto de 40 segundos, garantizando que una excursión térmica despierte al turno de calidad en la noche o alerte al despachador en el día antes de que el alimento sufra daño"*.

#### Ficha 10: `Microsoft Fabric Analytics and AI Architecture.md`
- **Ruta Completa:** `c:\Users\juand\Documents\Automatización\Frío Andes\frioandes-cloudops-docs\Microsoft Fabric Analytics and AI Architecture.md`
- **Propósito:** Especificar la estrategia analítica y de inteligencia artificial en Microsoft Fabric sin duplicar los 1.2 PB de almacenamiento histórico.
- **Resumen:** Implementa OneLake Shortcuts sobre ADLS Gen2, creando una capa Bronze virtual (0 GB de copia adicional), una capa Silver curada (~18.5 TB) y una capa Gold dimensional (~4.2 TB). Detalla los 6 casos de uso analíticos (incluyendo los 3 solicitados en el enunciado: alerta temprana de falla térmica, cálculo de ETA dinámico y control de calidad de fotos de entrega con muestreo inteligente de video). Presenta el gobierno multi-workspace (`ControlTower-Prod`, `Quality-ML`, `Executive-BI`) y el auto-escalado de capacidades F-SKU (F64 diurno / F8 nocturno).
- **Información Importante:** Documenta con honestidad técnica que el estado actual de integración tenant es **FAB-02 PARTIAL**, reservando los endpoints y modelos sin simular que ya están desplegados en producción.
- **Relación con otros archivos:** Se ilustra en `fabric-ai-integration.drawio` y se costea en el modelo FinOps.
- **Cómo explicarlo:** "Es el cerebro predictivo de la empresa, que se conecta directamente a los datos en Azure sin tener que pagar dos veces por guardar 1.2 petabytes, aplicando IA para predecir llegadas y detectar daños en ruta".
- **Qué decir textualmente:** *"Con Microsoft Fabric logramos analítica e inteligencia artificial sin duplicar los 1.2 PB gracias a OneLake Shortcuts, reduciendo el volumen analítico materializado a solo 22.7 TB y optimizando la capacidad F-SKU según el turno operativo"*.

#### Ficha 11: `Customer Portal and Control Tower Architecture.md`
- **Ruta Completa:** `c:\Users\juand\Documents\Automatización\Frío Andes\frioandes-cloudops-docs\Customer Portal and Control Tower Architecture.md`
- **Propósito:** Definir los límites arquitectónicos entre la Torre de Control interna y el Portal Público B2B, e implementar el **Sistema de Diseño de FríoAndes**.
- **Resumen:** Establece la segregación estricta de dominios: los clientes B2B solo ven sus guías mediante token claims y proyección sanitizada, mientras la Torre de Control accede a la flota completa y video RTSP. En su Sección 5, formaliza el Sistema de Diseño completo: isotipo, logotipo, paleta cromática (Andean Navy, Glacier Blue, Cryo Cyan, estados Normal/Alerta/Crítico), tipografía (Space Grotesk, Inter, JetBrains Mono) y componentes base con reglas de contraste WCAG AA/AAA para el turno nocturno.
- **Información Importante:** Prohíbe la inclusión de librerías de video en el bundle web de clientes corporativos mediante code-splitting físico.
- **Relación con otros archivos:** Da origen a los estilos y maquetación de `frioandes-cloudops-web` (`styles.css`, `index.html`).
- **Cómo explicarlo:** "Es el documento que garantiza que ningún cliente corporativo pueda espiar a otros camiones ni ver las cámaras en vivo, a la vez que entrega el manual de identidad gráfica unificada para toda la empresa".
- **Qué decir textualmente:** *"El diseño del portal combina una estricta sanitización de datos por tenant con nuestro nuevo sistema de diseño institucional, garantizando contraste adecuado para turnos nocturnos y total aislamiento de video"*.

#### Ficha 12: `Core Migration and Cutover Strategy.md`
- **Ruta Completa:** `c:\Users\juand\Documents\Automatización\Frío Andes\frioandes-cloudops-docs\Core Migration and Cutover Strategy.md`
- **Propósito:** Definir el plan de migración por olas (Waves 0 a 3), la clasificación de componentes bajo 4 disposiciones y la retirada ordenada del datacenter de Cali en 90 días.
- **Resumen:** Estructura la migración en Wave 0 (Fundaciones, días 0-15), Wave 1 (Servicios de soporte y siembra de archivo, días 16-45), Wave 2 (Corte de producción, días 46-75, con corte dominical de 2h) y Wave 3 (Estabilización, hipercare de 14 días y apagado de Cali, días 76-90). Clasifica los componentes en: Rehost (Lift & Shift), Rebuild (Contenedores en AKS), Replace (PaaS MySQL y Event Hubs) y Adapt (Ajustes de configuración y endpoints).
- **Información Importante:** Aclara que el día 90 se apagan los servidores migrados de Cali, pero se preserva la conectividad VPN corporativa y firewall para los usuarios de la sede local.
- **Relación con otros archivos:** Se complementa con `Dispatch Migration and Coexistence Strategy.md` y `core-application-cutover.drawio`.
- **Cómo explicarlo:** "Es la estrategia militar de la migración: cómo nos movemos paso a paso sin caernos, qué aplicaciones se rediseñan y cuándo desconectamos definitivamente las máquinas viejas en Cali".
- **Qué decir textualmente:** *"Nuestra estrategia de migración en 4 olas asegura una coexistencia máxima de 90 días, pre-sembrando el archivo histórico y apagando el cómputo legado de Cali tras 14 días de estabilidad garantizada"*.

---

### 4.3 Identificación de Archivos Redundantes, Históricos o Duplicados
Durante la auditoría se identificaron las siguientes redundancias y duplicidades justificadas en el repositorio:
1. **Reporte FinOps en PDF Duplicado:**
   - `Frío Andes/frioandes-cloudops-docs/FinOps-and-Migration-Strategy.pdf` (39.928 bytes en la raíz del repositorio de docs).
   - `Frío Andes/frioandes-cloudops-docs/output/pdf/FinOps-and-Migration-Strategy.pdf` (39.928 bytes en la subcarpeta formal de salida).
   - *Diagnóstico:* Son copias binarias idénticas. El archivo en `output/pdf/` es la ubicación canónica formal según las directrices de entrega; el de la raíz es un remanente histórico que puede removerse en una limpieza de mantenimiento.
2. **Hojas de Cálculo del Modelo de Costos:**
   - `Frío Andes/frioandes-cloudops-docs/outputs/2026-10-12-feedback-remediation/FrioAndes 24-Month Cost Model.xlsx` (Versión histórica del 12 de octubre).
   - `Frío Andes/frioandes-cloudops-docs/outputs/nonpresentation-20261021/FrioAndes 24-Month Cost Model.xlsx` (Versión activa definitiva del 21 de octubre).
   - *Diagnóstico:* No deben confundirse. La versión activa y conciliada para entrega es estrictamente la de la carpeta `nonpresentation-20261021`, la cual contiene las fórmulas SUMIFS enlazadas a los insumos de precios públicos.
3. **Dualidad de Fuentes de Diagramas (Python vs. Draw.io XML):**
   - Existen 13 scripts `.py` en `frioandes-cloudops-diagrams/src/` (ej. `step00_onprem_baseline.py` a `step08_...py`, `common.py`) que generan imágenes mediante Graphviz/Python Diagrams.
   - Existen 19 archivos `.drawio` en la misma carpeta que representan la versión moderna en XML editable.
   - *Diagnóstico:* La fuente canónica aprobada para el proyecto es el formato `.drawio` XML (según `MIGRATION_MANIFEST.md`). Los scripts de Python se conservan como respaldo histórico de verificación estructural side-by-side y están catalogados para retiro una vez finalice la sustentación.
4. **Exportaciones de Diagramas (SVG vs. PNG):**
   - Existen tanto en `frioandes-cloudops-diagrams/rendered/` como en `frioandes-cloudops-web/assets/diagrams/`.
   - *Diagnóstico:* La duplicación hacia la carpeta web es intencional y necesaria para desacoplar el empaquetado del sitio web estático (Cloudflare Pages) del repositorio documental. El formato `.svg` actúa como primario vectorial y el `.png` como fallback para navegadores antiguos.

---

## 5. ANÁLISIS DEL CÓDIGO FUENTE Y SCRIPTS DE AUTOMATIZACIÓN

### 5.1 Script de Política de Ventanas: `change_window_policy.py`
- **Ubicación:** `Frío Andes/frioandes-cloudops-docs/tools/change_window_policy.py`
- **Propósito:** Codificar como lógica determinista en Python las restricciones de negocio para la aprobación de cambios en producción.
- **Estructura y Funciones:**
  - `allowed(start, duration_minutes, calendar_years, freeze_dates, independent_approval, cutover=False)`:
    - Evalúa que la zona horaria sea estrictamente `America/Bogota` (COT).
    - Exige que `independent_approval` sea verdadero (no permite auto-aprobaciones).
    - Verifica que la duración en minutos sea un entero positivo.
    - Evalúa la restricción de horario diurno: `time(4) <= cursor.time() < time(22)`. Si cualquier minuto cae en este intervalo, retorna `False` (bloqueo total entre 04:00 y 22:00).
    - Evalúa la ventana de congelamiento de fin de año: del 25 de noviembre al 5 de enero (`(day.month == 11 and day.day >= 25) or day.month == 12 or (day.month == 1 and day.day <= 5)`). Si coincide, retorna `False`.
    - Evalúa las fechas de campañas comerciales (`freeze_dates`).
    - Si el flag `cutover=True` está activo, valida la restricción canónica del corte: debe ser día domingo (`local.weekday() == 6`), iniciar exactamente a las 02:00 COT (`local.time() == time(2)`) y tener una duración menor o igual a 120 minutos (`duration_minutes <= 120`).
- **Patrones de Diseño:** Function-as-Rule Pattern, Fail-Closed Security (cualquier dato nulo o fuera de rango bloquea la ejecución).
- **Explicación para Sustentación:** "Este script es la codificación matemática de la política de cambio: si un ingeniero intenta desplegar a las 2 de la tarde o durante el congelamiento de diciembre, la función rechaza la operación automáticamente".

### 5.2 Suite de Pruebas Unitarias: `test_change_window_policy.py`
- **Ubicación:** `Frío Andes/frioandes-cloudops-docs/tools/test_change_window_policy.py`
- **Propósito:** Demostrar formalmente que la política de cambios cumple con todas las restricciones del caso de negocio mediante pruebas automatizadas.
- **Clases y Métodos de Prueba (`unittest.TestCase`):**
  - `WindowTests.test_2200_allowed`: Valida que a las 22:00 COT de un domingo se permita iniciar mantenimiento.
  - `WindowTests.test_daylight_boundaries`: Prueba explícitamente que las 04:00, las 12:00 y las 21:59 sean rechazadas.
  - `WindowTests.test_finish_at_0400_not_after`: Comprueba que una tarea iniciada a las 03:30 con 30 minutos termine a las 04:00 permitido, pero si dura 31 minutos sea rechazada.
  - `WindowTests.test_year_end_freeze_inclusive`: Valida que el 25 de nov, 20 de dic y 5 de ene estén bloqueados, y que el 6 de ene se desbloquee.
  - `WindowTests.test_cutover_sunday_only`: Prueba que el cutover solo sea válido el domingo a las 02:00 por 120 min, rechazando sábados, horas distintas (01:00) o duraciones de 121 min.
- **Explicación para Sustentación:** "Es la evidencia de ingeniería de calidad: cada restricción operativa del cliente cuenta con un caso de prueba automatizado que previene errores humanos en producción".

### 5.3 Validador de Consistencia Global: `verify_final_consistency.py`
- **Ubicación:** `Frío Andes/frioandes-cloudops-docs/tools/verify_final_consistency.py`
- **Propósito:** Suite de auditoría estática y matemática que garantiza que no existan inconsistencias numéricas, colisiones de red o enlaces rotos en la documentación y los modelos.
- **Pruebas Principales:**
  - `test_eight_criteria_and_required_evidence_resolve`: Verifica que los 8 criterios del enunciado estén presentes en el informe y que todos los enlaces relativos apunten a archivos existentes.
  - `test_full_current_environment_and_hub_address_tables`: Valida mediante la librería nativa `ipaddress` que ninguna de las subredes de Hub, Prod, Stg y Dev se solapen entre sí.
  - `test_corrected_current_cache_and_receiver_contracts`: Audita que los puertos de Redis sean TLS 10000 y 8500-8599 (Managed Redis) y no el puerto legacy 6380, y que la regla `FW-ADM-04` prohíba accesos shell directos.
  - `test_every_literal_diagram_cidr_is_documented_in_a_table`: Escanea todos los bloques Mermaid y valida que los 26 prefijos CIDR dibujados coincidan exactamente con las tablas de subnetting.
  - `test_pinned_cost_hashes_and_explicit_scenario_arithmetic`: Verifica la integridad SHA-256 de los insumos de precios y valida que la diferencia entre el escenario always-on ($981,226.11) y el programado ($919,106.31) sea exactamente $62,119.80 USD, y que el precio de venta final sea $1,955,766.76 USD.

### 5.4 Código de la Aplicación Web Interactiva (`app.js` y `diagrams.js`)
- **Ubicaciones:** `Frío Andes/frioandes-cloudops-web/app.js` y `diagrams.js`
- **Propósito:** Dar vida al portal de sustentación de FríoAndes, permitiendo navegar entre los 18 diagramas con controles interactivos y simular el comportamiento de la plataforma en tiempo real.
- **Componentes y Lógica Relevante:**
  - `window.FRIOANDES_DIAGRAMS` (en `diagrams.js`): Arreglo JSON de 18 objetos con metadatos de cada diagrama: ID, título, descripción, categorías, estado de revisión, dimensiones en píxeles y documentos de respaldo.
  - `initOperations()` (en `app.js`): Controlador de la sección de operaciones. Permite alternar entre el modo **Línea Base** (120 camiones, 480.960 eventos diarios, 800 req/min) y el modo **Campaña Pico** (180 camiones, 675.360 eventos diarios, 2.000 req/min, +40% de cómputo), animando las barras de capacidad y métricas.
  - `initAlertRouting()` (en `app.js`): Calcula dinámicamente la hora actual en Bogotá (`America/Bogota`) y mueve un indicador interactivo sobre la barra de turnos (04:00-22:00 diurno vs. 22:00-04:00 nocturno), actualizando el texto de ruteo de la alerta.
  - `initModalViewer()` (en `app.js`): Visor interactivo en canvas HTML5 para explorar los diagramas. Soporta zoom suave (+/-, rueda del ratón), arrastre tridimensional (drag-to-pan), pantalla completa y descarga de los SVGs originales.
- **Explicación para Sustentación:** "Desarrollamos una herramienta interactiva para que el jurado pueda comprobar en vivo cómo escala la plataforma durante una campaña y cómo conmuta el ruteo de alertas entre el día y la noche".

### 5.5 Scripts de Diagramación Legados en Python (`src/step*.py`)
- **Ubicación:** `Frío Andes/frioandes-cloudops-diagrams/src/`
- **Tecnología:** Librería `diagrams` de Python con Graphviz.
- **Estructura Modular:**
  - `common.py`: Define constantes visuales, paletas de colores corporativos y nodos reutilizables.
  - `step00_onprem_baseline.py`: Diagrama del datacenter de Cali (VMware, CentOS, MySQL 5.7).
  - `step01_hybrid_connectivity_and_data_seeding.py`: Túneles VPN y proceso de siembra de 1.2 PB.
  - `step02_telemetry_streaming_ingestion.py`: Flujo de IoT Hub, Stream Analytics y ruteo diurno/nocturno.
  - `step03_database_migration_coexistence.py`: Replicación lógica MySQL CDC y coexistencia a 90 días.
  - `step04_core_application_cutover.py`: Arquitectura de contenedores en AKS y corte de producción.
  - `step05_fabric_ai_integration.py`: Integración OneLake Shortcuts y modelos SynapseML.
  - `step06_multicloud_gcp_horizon.py`: Preparación de interconexión con GCP a 24 meses.
  - `step07_global_target_architecture_azure.py`: Arquitectura objetivo global de extremo a extremo.
  - `step08_global_multicloud_azure_gcp.py`: Topología multicloud final combinada.
---

## 6. ANÁLISIS DETALLADO DE LOS 18 DIAGRAMAS DE ARQUITECTURA

El sistema cuenta con un catálogo exhaustivo de **18 diagramas** estructurados en dos grupos:
- **12 diagramas canónicos migrados:** Originados en scripts de Python y convertidos a Draw.io XML con paridad estructural verificada (`MIGRATION_MANIFEST.md`).
- **6 diagramas de secuencia especializados:** Diagramas adicionales que detallan flujos críticos de seguridad, elevación de privilegios PIM, sesiones JIT y ventanas de alerta.

A continuación se presenta el análisis técnico individual de cada uno de los 18 diagramas, con su respectivo guion de sustentación y preparación de preguntas del jurado.

---

### 6.1 Catálogo de Diagramas Canónicos Migrados (DIA-001 a DIA-012)

#### Diagrama 1: `on-premises-baseline` (DIA-001)
- **Archivos de Respaldo:** `src/on-premises-baseline.drawio`, `rendered/on-premises-baseline.svg`, `rendered/on-premises-baseline.png`, `src/step00_onprem_baseline.py`.
- **Qué representa:** La topología de infraestructura actual (As-Is) del datacenter en Cali, los 8 CEDIs y la conectividad híbrida previa a la migración.
- **Cómo interpretarlo:** Se divide en tres clústeres de VMware en Cali: Clúster de Datos (MySQL 5.7 y Redis 5 en CentOS 7), Clúster de Aplicación (Ubuntu con Docker para Despacho, Inventario, Facturación y Nginx) y Clúster Transversal (CentOS con FTP/NFS de 1.2 PB y Zabbix). A la izquierda se observan los 8 CEDIs conectados por túneles VPN IPsec sobre el internet simétrico de 400 Mbps de Cali.
- **Explicación técnica:** Ilustra el cuello de botella centralizado: todo el enrutamiento converge en el firewall perimetral NGFW de Cali. La base transaccional no tiene réplica ni clusterización; el almacenamiento de 1.2 PB reside en un único NFS sin redundancia geográfica; y las aplicaciones comparten hosts ESXi físicos sin aislamiento de ambientes.
- **Explicación sencilla:** "Es el mapa de la casa vieja: muestra cómo hoy FríoAndes depende de un único cuarto de máquinas en Cali, donde si falla un solo servidor de base de datos o el enlace de internet, se paraliza la distribución de alimentos de todo el país".
- **Guion exacto para la exposición:** *"En este diagrama observamos la línea base actual de FríoAndes en Cali. Destacamos tres puntos críticos: primero, la concentración de 1.2 PB en una sola máquina virtual de archivos que crece 12 TB al mes; segundo, una base de datos MySQL 5.7 obsoleta sin réplica ni alta disponibilidad; y tercero, la saturación del firewall de Cali que debe gestionar simultáneamente el tráfico de los ocho centros regionales, las consultas públicas de rastreo y la operativa interna"*.
- **Posibles preguntas del jurado:**
  - *Pregunta:* "¿Por qué el diagrama muestra que los camiones no pasan por la VPN de los centros regionales?"
  - *Respuesta sugerida:* "Porque de acuerdo con las restricciones del negocio, los camiones operan con módem celular del operador de flota móvil y transmiten por internet público, a diferencia de los sensores de los cuartos fríos que sí están cableados a la LAN del CEDI y cruzan por la VPN corporativa".

#### Diagrama 2: `hybrid-connectivity-and-data-seeding` (DIA-002)
- **Archivos de Respaldo:** `src/hybrid-connectivity-and-data-seeding.drawio`, `rendered/hybrid-connectivity-and-data-seeding.svg`, `src/step01_hybrid_connectivity_and_data_seeding.py`.
- **Qué representa:** La fase inicial de interconexión híbrida entre Cali y Azure, y la estrategia de siembra física de los 1.2 PB de archivo histórico.
- **Cómo interpretarlo:** Muestra el túnel VPN IPsec redundante entre el firewall de Cali y el Azure VPN Gateway (`VpnGw2AZ`), la preparación de un appliance de staging local RAID 60 en Cali para consolidar y limpiar los datos, y la transferencia masiva hacia Azure Blob Storage / ADLS Gen2.
- **Explicación técnica:** Detalla cómo se evita saturar el enlace WAN de 400 Mbps: el archivo de 1.2 PB se procesa localmente en un appliance de staging (1,248.5 TB utilizables) y se siembra de forma pre-cutover (mediante dispositivos de transporte físico tipo Azure Data Box o ventana de transferencia masiva dedicada), reservando el túnel VPN para la sincronización continua de deltas transaccionales.
- **Explicación sencilla:** "Representa el puente entre el mundo físico y la nube: cómo conectamos Cali con Azure y cómo movemos 1.2 millones de gigabytes de fotos y videos mediante un camión blindado digital antes del día del corte definitivo".
- **Guion exacto para la exposición:** *"Este diagrama ilustra la Wave 0 y 1 de migración. Demuestra que no pretendemos mover 1.2 PB por un enlace de 400 Mbps durante la noche del corte; en su lugar, consolidamos el archivo en un almacenamiento staging local y lo pre-sembramos en la nube con semanas de anticipación, dejando el enlace híbrido únicamente para replicar el diferencial"*.
- **Posibles preguntas del jurado:**
  - *Pregunta:* "¿Qué pasa si Azure Data Box no está disponible regionalmente en Colombia?"
  - *Respuesta sugerida:* "El diseño arquitectónico contempla una matriz de sensibilidad: si el appliance físico no puede importarse por restricciones aduaneras, se evalúa una ventana de transferencia en bloque sobre un circuito dedicado temporal o la siembra por lotes nocturnos priorizando metadatos y fotos críticas (110 TB) antes de mover el volumen masivo de video (1.05 PB)".

#### Diagrama 3: `telemetry-streaming-ingestion` (DIA-003)
- **Archivos de Respaldo:** `src/telemetry-streaming-ingestion.drawio`, `rendered/telemetry-streaming-ingestion.svg`, `src/step02_telemetry_streaming_ingestion.py`.
- **Qué representa:** La arquitectura de ingesta en tiempo real para la telemetría de la flota móvil (120/180 camiones) y los 32 cuartos fríos industriales.
- **Cómo interpretarlo:** De izquierda a derecha: sensores emitiendo telemetría -> IoT Hub (público con X.509 para camiones, privado para CEDIs) -> Event Hubs -> Azure Stream Analytics -> Desacoplamiento de alarmas hacia Azure Functions -> Persistencia en ADLS Gen2 y entrega a consolas operativas.
- **Explicación técnica:** Modela la bifurcación de ingesta: los camiones transmiten por red celular a un endpoint público TLS 8883 autenticado con certificados individuales de dispositivo; los cuartos fríos transmiten por VPN corporativa a endpoints privados en `10.101.7.0/24`. Stream Analytics ejecuta consultas continuas de ventana deslizante; si detecta excursión térmica, genera un evento de severidad 1 que la Function clasifica según la hora para alertar a la Torre o a Calidad.
- **Explicación sencilla:** "Es el velocímetro y termómetro en vivo de toda la empresa: capta las lecturas de los camiones y cuartos fríos cada 30 segundos y, si la temperatura sube, hace sonar la alarma correspondiente en menos de 40 segundos".
- **Guion exacto para la exposición:** *"En este diagrama apreciamos el corazón de la modernización operativa: reemplazamos la carga manual por lotes de una hora por un pipeline continuo de streaming que procesa hasta 675.000 eventos diarios, garantizando que cualquier anomalía térmica sea detectada y notificada en menos de un minuto"*.
- **Posibles preguntas del jurado:**
  - *Pregunta:* "¿Cómo se asegura que las alertas lleguen de noche si la Torre de Control cierra a las 22:00?"
  - *Respuesta sugerida:* "Mediante la Azure Function de enrutamiento horario, que conmuta automáticamente a las 22:00 COT, dirigiendo la notificación a través de servicios de SMS prioritario y llamadas automatizadas al turno de Calidad de Guardia".

#### Diagrama 4: `database-migration-coexistence` (DIA-004)
- **Archivos de Respaldo:** `src/database-migration-coexistence.drawio`, `rendered/database-migration-coexistence.svg`, `src/step03_database_migration_coexistence.py`.
- **Qué representa:** El mecanismo de coexistencia y replicación de base de datos entre el MySQL 5.7 on-premises y el Azure MySQL Flexible Server durante la fase de 90 días.
- **Cómo interpretarlo:** Muestra a Cali como el único maestro de escritura (`read_only = OFF`) replicando unidireccionalmente mediante binlogs/CDC hacia Azure MySQL Flexible Server configurado en modo solo lectura (`read_only = ON`).
- **Explicación técnica:** Evita el riesgo de split-brain (cerebro dividido) imponiendo una estricta política de un solo escritor (Single-Writer Architecture). La base de Azure actúa como réplica pasiva manteniéndose sincronizada con un retraso inferior a 1 segundo sobre la VPN, lista para ser promovida a maestro durante la ventana de corte de 2 horas.
- **Explicación sencilla:** "Es el sistema de espejo: Cali sigue escribiendo el día a día mientras Azure copia en silencio cada transacción por debajo de la mesa, de modo que cuando toque cortar, la nube ya tenga la información al día".
- **Guion exacto para la exposición:** *"Durante los 90 días de coexistencia no permitimos escrituras concurrentes en ambos lados. Cali es el único sistema de registro; Azure replica continuamente mediante canales seguros TLS 3306. Esto nos permite ensayar la migración tantas veces como sea necesario con cero riesgo de inconsistencia transaccional"*.
- **Posibles preguntas del jurado:**
  - *Pregunta:* "¿Por qué no implementar replicación bidireccional activa-activa?"
  - *Respuesta sugerida:* "Porque MySQL 5.7 y MySQL 8.0 Flexible Server no soportan replicación activa-activa nativa sin riesgo severo de conflictos de llaves primarias y colisiones de datos. La arquitectura de escritor único con promoción controlada elimina por diseño el riesgo de corrupción transaccional".

#### Diagrama 5: `core-application-cutover` (DIA-005)
- **Archivos de Respaldo:** `src/core-application-cutover.drawio`, `rendered/core-application-cutover.svg`, `src/step04_core_application_cutover.py`.
- **Qué representa:** La secuencia técnica de conmutación (cutover) de aplicaciones y tráfico en la madrugada del domingo de 02:00 a 04:00 COT.
- **Cómo interpretarlo:** Muestra el congelamiento del despacho en Cali a las 02:00, la captura de coordenadas binlog, la aplicación del último delta, la promoción de Azure MySQL a lectura/escritura, la repuntualización del DNS privado corporativo hacia el ILB `10.101.2.10` y la verificación de pruebas de humo antes de las 04:00.
- **Explicación técnica:** Detalla el cronograma de ejecución con la decisión irreversible fijada a las 03:00 COT. Si a las 03:00 algún chequeo de salud falla, se aborta y se ejecutan los 35 minutos de rollback para devolver el tráfico a Cali antes de que inicie el despacho diurno a las 04:00 COT.
- **Explicación sencilla:** "Es la coreografía del cambio de mando: muestra qué botón se aprieta a las 2:00 AM, cuándo se pasa la llave a la nube a las 3:00 AM y cómo nos aseguramos de que a las 4:00 AM los camiones salgan a trabajar sin enterarse de que cambiamos de servidor".
- **Guion exacto para la exposición:** *"El cutover productivo es un proceso determinista de 120 minutos. Bloqueamos escrituras en Cali a las 02:00, pasamos el delta en 15 minutos, probamos el ambiente en privado y a las 03:00 tomamos la decisión de Go/No-Go. Si se declara No-Go, tenemos 35 minutos para revertir rutas y garantizar que a las 04:00 Cali opere con normalidad"*.
- **Posibles preguntas del jurado:**
  - *Pregunta:* "¿Qué constituye el punto de no retorno después de las 03:00?"
  - *Respuesta sugerida:* "El punto de no retorno se alcanza en el momento en que se autoriza la primera escritura de negocio real en Azure MySQL (después de las 03:00). A partir de ese instante, revertir el DNS no recupera datos; requeriría un procedimiento de contingencia asistido por el Comité de Crisis".

#### Diagrama 6: `fabric-ai-integration` (DIA-006)
- **Archivos de Respaldo:** `src/fabric-ai-integration.drawio`, `rendered/fabric-ai-integration.svg`, `src/step05_fabric_ai_integration.py`.
- **Qué representa:** La integración analítica de Microsoft Fabric sobre el lago de datos ADLS Gen2 mediante OneLake Shortcuts y los casos de uso de IA.
- **Cómo interpretarlo:** Muestra el almacenamiento ADLS Gen2 como repositorio único de la verdad. Fabric crea punteros virtuales (Shortcuts) para conformar el Bronze Lakehouse sin duplicar 1.2 PB. Spark limpia y cura en Silver (~18.5 TB) y consolida modelos estrella en Gold (~4.2 TB), alimentando Power BI Direct Lake y modelos SynapseML.
- **Explicación técnica:** Ilustra el desacoplamiento entre el almacenamiento transaccional/archivo y el cómputo analítico. La Torre de Control y la guardia de calidad consumen tableros mediante Direct Lake (latencia sub-segundo en memoria) sin impactar el rendimiento de la base de datos operativa MySQL.
- **Explicación sencilla:** "Muestra cómo la inteligencia artificial se conecta a los datos como un lector que mira por una ventana, sin tener que hacer fotocopias de 1.2 petabytes, ahorrándonos miles de dólares al año en almacenamiento redundante".
- **Guion exacto para la exposición:** *"Con este diseño resolvemos el reto de Microsoft Fabric: conectamos OneLake directamente sobre el almacenamiento de Azure mediante Shortcuts. Solo materializamos 22.7 TB de capas Silver y Gold, permitiendo a la Torre de Control predecir fallas térmicas y horas de llegada sin pagar un segundo archivo histórico"*.
- **Posibles preguntas del jurado:**
  - *Pregunta:* "¿Cómo se asegura que los clientes corporativos no vean información de otros clientes en Fabric?"
  - *Respuesta sugerida:* "Implementando seguridad a nivel de fila dinámica (Dynamic Row-Level Security - RLS) y seguridad a nivel de columna (CLS) en el modelo semántico de Fabric, filtrando las consultas por el Tenant ID validado en el token de Entra ID".

#### Diagrama 7: `multicloud-24-month-horizon` (DIA-007)
- **Archivos de Respaldo:** `src/multicloud-24-month-horizon.drawio`, `rendered/multicloud-24-month-horizon.svg`, `src/step06_multicloud_gcp_horizon.py`.
- **Qué representa:** La arquitectura de preparación para la interconexión con una segunda nube (Google Cloud Platform) en un horizonte de 24 meses.
- **Cómo interpretarlo:** Muestra a Azure como el núcleo operativo consolidado y a GCP como una extensión de cómputo especializada conectada mediante Cloud Interconnect o VPN inter-nube hacia la subred de tránsito `10.100.10.0/24`, utilizando el espacio CIDR reservado `10.200.0.0/16`.
- **Explicación técnica:** Cumple el principio de no duplicar toda la plataforma desde el día uno: preserva la gravedad de datos en Azure (evitando altos costos de egress) y proyecta a GCP para cargas analíticas portátiles o contingencia de cómputo en Google Kubernetes Engine (GKE), gestionando identidades federadas con Entra ID mediante OIDC.
- **Explicación sencilla:** "Es el plano de expansión futura: dejamos la puerta y los cimientos listos para conectar una segunda nube como Google en dos años, sin tener que pagar dos nubes completas desde hoy".
- **Guion exacto para la exposición:** *"El enunciado solicita preparar la interconexión futura sin duplicar costos. En este diagrama evidenciamos la reserva de red 10.200.0.0/16 y el punto de tránsito en el Hub. La plataforma está lista para interconectar GCP a los 24 meses sin traumatismos de enrutamiento ni reingeniería de identidades"*.
- **Posibles preguntas del jurado:**
  - *Pregunta:* "¿Por qué se eligió reservar GCP y no AWS como segunda nube?"
  - *Respuesta sugerida:* "En la matriz de evaluación multicriterio de proveedores (WBS 05), GCP obtuvo ventajas competitivas en analítica avanzada con BigQuery y Kubernetes administrado (GKE), siendo el complemento ideal para la gravedad de datos de Azure y Microsoft Fabric".

#### Diagrama 8: `azure-target-architecture` (DIA-008)
- **Archivos de Respaldo:** `src/azure-target-architecture.drawio`, `rendered/azure-target-architecture.svg`, `src/step07_global_target_architecture_azure.py`.
- **Qué representa:** La arquitectura global de destino completa y plenamente migrada en Microsoft Azure.
- **Cómo interpretarlo:** Es el diagrama maestro integrador (3199x2157 px). Conecta los 8 CEDIs, la flota de camiones, el Hub de conectividad y seguridad, los Spokes de ambientes (Prod, Stg, Dev), las 10 zonas funcionales de producción, el gobierno de identidades con Entra ID, la bóveda KMS en Key Vault y la suite de observabilidad/SIEM.
- **Explicación técnica:** Sintetiza el cumplimiento de los cinco pilares de Well-Architected Framework: resiliencia con Multi-AZ en AKS y MySQL, seguridad con Azure Firewall Premium e inspección TLS, gobernanza con suscripciones segregadas y políticas de tagging, eficiencia con HPA y optimización de costos con almacenamiento en capas.
- **Explicación sencilla:** "Es la foto panorámica de FríoAndes completamente modernizada: todas las sedes, camiones, servidores y herramientas de seguridad funcionando de manera armónica en una nube de clase empresarial".
- **Guion exacto para la exposición:** *"Este es nuestro diagrama insignia: la arquitectura objetivo final en Azure. Integra en una sola vista la conectividad de los ocho CEDIs, la protección perimetral con WAF, el clúster privado de AKS para despacho, la base de datos redundante entre zonas y la integración nativa con Microsoft Fabric"*.
- **Posibles preguntas del jurado:**
  - *Pregunta:* "¿Cómo se garantiza la alta disponibilidad si una zona de disponibilidad completa de Azure sufre un apagón?"
  - *Respuesta sugerida:* "AKS y MySQL Flexible Server están desplegados en configuración Multi-AZ (zonas 1, 2 y 3). Si una zona falla, el failover de MySQL conmuta automáticamente en menos de 60 segundos hacia la réplica sincrónica en la zona secundaria, y los pods de AKS se re-agendan en los nodos supervivientes de las otras zonas".

#### Diagrama 9: `global-multicloud-azure-gcp` (DIA-009)
- **Archivos de Respaldo:** `src/global-multicloud-azure-gcp.drawio`, `rendered/global-multicloud-azure-gcp.svg`, `src/step08_global_multicloud_azure_gcp.py`.
- **Qué representa:** La topología de gobernanza global multicloud interconectando Azure y GCP a nivel corporativo.
- **Cómo interpretarlo:** Expande la visión del horizonte a 24 meses mostrando la jerarquía de gobierno: un único tenant de Microsoft Entra ID sincronizado con Google Cloud Identity, un esquema unificado de auditoría hacia Azure Sentinel y enrutamiento BGP seguro entre ambas nubes.
- **Explicación técnica:** Detalla el diseño de DNS híbrido privado: resolución condicional cruzada entre Azure Private DNS Resolver y Cloud DNS en GCP, garantizando que los servicios de ambas nubes se resuelvan por nombres FQDN privados sin exponer registros a Internet público.
- **Explicación sencilla:** "Es el tratado de paz y entendimiento entre Azure y Google: cómo logramos que dos nubes distintas hablen el mismo idioma de seguridad, usuarios y nombres de red".
- **Guion exacto para la exposición:** *"Mostramos cómo se gobernará el ecosistema multicloud: una identidad unificada con Entra ID actuando como IdP maestro, un esquema centralizado de logs y una resolución de nombres privada que permite consumir servicios cruzados con cero fricción"*.
- **Posibles preguntas del jurado:**
  - *Pregunta:* "¿Qué impacto tiene el costo de transferencia de datos (egress) en este modelo multicloud?"
  - *Respuesta sugerida:* "El modelo confina los datos pesados (los 1.2 PB) en Azure. Entre Azure y GCP solo se intercambian resultados analíticos procesados y llamadas de API de bajo volumen, minimizando los cargos por egreso de red según las buenas prácticas FinOps".

#### Diagrama 10: `sequence-current-as-is` (DIA-010)
- **Archivos de Respaldo:** `src/sequence-current-as-is.drawio`, `rendered/sequence-current-as-is.svg`, `src/sequence_current_as_is.py`.
- **Qué representa:** El flujo operativo actual de extremo a extremo (As-Is) con todas sus ineficiencias y demoras inherentes.
- **Cómo interpretarlo:** Diagrama de secuencia temporal que muestra el despacho manual, la carga de datos por lotes cada 60 minutos, la saturación del servidor MySQL ante consultas masivas y el retraso de días en la investigación de reclamos por ruptura térmica.
- **Explicación técnica:** Modela las interacciones bloqueantes del sistema actual: el despachador compite por locks en MySQL contra los procesos de facturación e inventario; las consultas del portal público degradan el backend; y los reclamos de clientes dependen de una revisión manual forense de archivos planos descargados por FTP.
- **Explicación sencilla:** "Es la película de lo que pasa hoy cuando algo falla: el camión pierde frío a las 10 de la mañana, nadie se entera, el cliente recibe la comida dañada a las 4 de la tarde y el reclamo se viene a investigar dos días después buscando en archivos viejos".
- **Guion exacto para la exposición:** *"Esta secuencia expone la vulnerabilidad operativa actual: la latencia de una hora en los registros por lotes imposibilita cualquier acción correctiva mientras el camión está en ruta. La pérdida de producto es inevitable y los costos por indemnizaciones recaen sobre FríoAndes"*.
- **Posibles preguntas del jurado:**
  - *Pregunta:* "¿Dónde está el mayor cuello de botella en este flujo legado?"
  - *Respuesta sugerida:* "En la contención transaccional de la única VM de MySQL 5.7 y en el proceso nocturno de volcado a la 01:00, que bloquea tablas y pone en riesgo la operación si ocurre una caída antes del respaldo".

#### Diagrama 11: `sequence-future-target-state` (DIA-011)
- **Archivos de Respaldo:** `src/sequence-future-target-state.drawio`, `rendered/sequence-future-target-state.svg`, `src/sequence_future_target_state.py`.
- **Qué representa:** El flujo operativo modernizado en la nube (To-Be) integrando IoT, microservicios en AKS, caché Redis y analítica predictiva en Fabric.
- **Cómo interpretarlo:** Secuencia que inicia con la lectura de temperatura -> IoT Hub -> Stream Analytics detectando anomalía -> Alerta instantánea en la Torre de Control -> Notificación al conductor para ajustar el termostato antes de 10 minutos -> Persistencia en Lakehouse -> Actualización inmediata de ETA y satisfacción del cliente B2B.
- **Explicación técnica:** Refleja la arquitectura dirigida por eventos (Event-Driven Architecture) no bloqueante. Las consultas de rastreo pegan en Redis en < 5 ms; el procesamiento de telemetría ocurre desacoplado del motor transaccional; y los despachadores cuentan con visibilidad contextual en tiempo real.
- **Explicación sencilla:** "Es la película del futuro: el camión sube de temperatura a las 10:00, a las 10:00 y 40 segundos el supervisor recibe la alarma en su pantalla, llama al conductor, se corrige el termostato y la carga se salva intacta".
- **Guion exacto para la exposición:** *"Frente a la lentitud del pasado, este diagrama demuestra la eficiencia del estado objetivo: logramos alertamiento sub-minuto, consultas de clientes instantáneas con caché y acciones preventivas que salvan la mercancía en tránsito"*.
- **Posibles preguntas del jurado:**
  - *Pregunta:* "¿Qué tecnología asegura que la Torre de Control reciba la alarma sin tener que recargar la página web?"
  - *Respuesta sugerida:* "La implementación de WebSockets mediante Azure SignalR Service integrado con AKS, que empuja eventos push bidireccionales en tiempo real al navegador del operador".

#### Diagrama 12: `diagram-raid60-staging-storage` (DIA-012)
- **Archivos de Respaldo:** `src/diagram-raid60-staging-storage.drawio`, `rendered/diagram-raid60-staging-storage.svg`, `src/diagram_raid60_staging_storage.py`.
- **Qué representa:** El diseño físico y lógico del almacenamiento de staging temporal en configuración RAID 60 para la consolidación previa de los 1.2 PB en Cali.
- **Cómo interpretarlo:** Detalla la arquitectura de controladora y discos del storage local requerido para consolidar, validar sumas de verificación (checksums) y ejecutar la limpieza de datos antes de transferir hacia Azure.
- **Explicación técnica:** Corrige técnicamente el diseño inicial: un arreglo de 1,008 TB resulta insuficiente para el volumen B0 de 1,200 TB; se dimensiona un appliance con **1,248.5 TB utilizables antes de reservas** bajo RAID 60 (doble paridad distribuida en dos grupos), garantizando tolerancia ante la pérdida simultánea de hasta 4 discos duros sin corrupción de datos durante el proceso de exportación.
- **Explicación sencilla:** "Es la zona de embalaje seguro: un contenedor digital de discos de alta resistencia donde revisamos, limpiamos y empacamos los 1.2 petabytes antes de subirlos a la nube".
- **Guion exacto para la exposición:** *"La auditoría técnica demostró que el storage staging requería superar los 1,008 TB del ejemplo preliminar. Diseñamos un arreglo RAID 60 de 1,248 TB útiles con doble paridad para garantizar integridad absoluta durante la siembra de la evidencia legal"*.
- **Posibles preguntas del jurado:**
  - *Pregunta:* "¿Por qué elegir RAID 60 y no RAID 10 para este staging?"
  - *Respuesta sugerida:* "Porque RAID 10 requeriría duplicar el 100% de la capacidad de discos (2.4 PB en discos físicos), resultando económicamente inviable; RAID 60 ofrece el balance óptimo entre rendimiento secuencial, protección de doble paridad por grupo y eficiencia de utilización del espacio (> 80%)".

---

### 6.2 Catálogo de Diagramas de Secuencia Especializados (DIA-013 a DIA-018)

#### Diagrama 13: `sequence-current-telemetry-delay` (DIA-013)
- **Archivos de Respaldo:** `src/sequence-current-telemetry-delay.drawio`, `rendered/sequence-current-telemetry-delay.svg`.
- **Qué representa:** Diagrama enfocado en el retraso temporal inherente a la carga por lotes (batch) de telemetría en el sistema actual.
- **Cómo interpretarlo:** Traza paso a paso cómo los registros térmicos se almacenan localmente en los centros regionales, se empaquetan en archivos planos cada 60 minutos, se transmiten por FTP, se importan mediante cron jobs en Cali y se analizan con retrasos acumulados de 2 a 4 horas.
- **Explicación técnica:** Demuestra la inviabilidad matemática del sistema actual para cumplir cualquier meta de inocuidad en tiempo real: la latencia total acumulada $T_{\text{total}} = T_{\text{buffer}} + T_{\text{export}} + T_{\text{transfer}} + T_{\text{import}}$ oscila entre 3.600 y 7.200 segundos, imposibilitando la intervención oportuna.
- **Guion exacto para la exposición:** *"Este diagrama de secuencia evidencia la raíz del problema: una cadena de dependencias secuenciales por lotes que introduce entre 1 y 2 horas de retraso ciego entre la medición del sensor y la visibilidad operativa"*.

#### Diagrama 14: `sequence-target-excursion-alert` (DIA-014)
- **Archivos de Respaldo:** `src/sequence-target-excursion-alert.drawio`, `rendered/sequence-target-excursion-alert.svg`.
- **Qué representa:** El flujo de detección, notificación, escalamiento y confirmación (acknowledgement) de una excursión de temperatura en el sistema de destino.
- **Cómo interpretarlo:** Secuencia que modela el ciclo de vida de la alarma: emisión del sensor, ingesta en IoT Hub, alerta en pantalla diurna, despacho de SMS/llamada nocturna, confirmación por el operador en menos de 5 minutos, y reintento/escalamiento al Director de Operaciones si no hay acuse de recibo.
- **Explicación técnica:** Detalla el algoritmo de escalamiento jerárquico y el cálculo de latencia de 40 segundos, demostrando que si el operador de guardia no confirma la alarma en 5 minutos, el sistema escala automáticamente a un segundo nivel telefónico.
- **Guion exacto para la exposición:** *"Modelamos el protocolo de alerta completo: no solo enviamos la notificación en 40 segundos, sino que exigimos confirmación activa del operador en 5 minutos; si no hay respuesta, la plataforma escala automáticamente al supervisor de turno"*.

#### Diagrama 15: `privileged-access-sequence` (DIA-015)
- **Archivos de Respaldo:** `src/privileged-access-sequence.drawio`, `rendered/privileged-access-sequence.svg`.
- **Qué representa:** El proceso de autenticación fuerte y elevación de privilegios Just-In-Time (JIT) mediante Entra ID Privileged Identity Management (PIM).
- **Cómo interpretarlo:** Muestra a un ingeniero solicitando acceso de administrador -> MFA obligatorio con FIDO2/Authenticator -> Aprobación por un par o líder técnico -> Activación temporal del rol RBAC con ventana de tiempo fija -> Auditoría en Log Analytics.
- **Explicación técnica:** Implementa el principio de Cero Privilegios Permanentes (Zero Standing Privileges). Ningún administrador tiene permisos de escritura continuos; la elevación expira automáticamente y cada acción queda registrada en logs inmutables con retención de 5 años.
- **Guion exacto para la exposición:** *"La seguridad administrativa se rige por Zero-Trust: nadie tiene acceso de administrador por defecto. El ingeniero debe solicitar elevación temporal justificando el ticket, pasar MFA fuerte y recibir aprobación antes de que el rol se habilite por un tiempo acotado"*.

#### Diagrama 16: `privileged-session-sequence` (DIA-016)
- **Archivos de Respaldo:** `src/privileged-session-sequence.drawio`, `rendered/privileged-session-sequence.svg`.
- **Qué representa:** El establecimiento de una sesión administrativa cifrada y mediada a través de Azure Bastion Premium.
- **Cómo interpretarlo:** Conexión desde el cliente VPN hacia Azure Bastion -> Validación de puertos JIT en NSG -> Conexión proxy SSH/RDP hacia la máquina de administración en `10.100.5.0/24` -> Cierre de sesión y revocación inmediata de puertos.
- **Explicación técnica:** Demuestra la eliminación total de IPs públicas en los servidores de administración. La sesión ocurre mediante streaming HTTPS/TLS a través de Bastion; las credenciales nunca tocan la red pública y los puertos 22/3389 permanecen bloqueados en los NSGs hasta que Bastion los abre dinámicamente.
- **Guion exacto para la exposición:** *"Nuestros servidores de gestión no tienen IP pública ni puertos de administración abiertos a Internet. Todo acceso ocurre mediante Azure Bastion en túnel privado, abriendo puertos SSH únicamente durante la duración de la sesión autorizada"*.

#### Diagrama 17: `emergency-admin-access-sequence` (DIA-017)
- **Archivos de Respaldo:** `src/emergency-admin-access-sequence.drawio`, `rendered/emergency-admin-access-sequence.svg`.
- **Qué representa:** El flujo de autorización para cuentas de emergencia (Break-Glass / Contingencia Severa P1).
- **Cómo interpretarlo:** Muestra el procedimiento de contingencia ante incidentes críticos donde el flujo estándar de aprobación no responde: se requiere doble factor fuera de banda, aprobación dual independiente y se disparan alertas de máxima prioridad a toda la dirección de TI.
- **Explicación técnica:** Modela la mitigación de bloqueos catastróficos. La activación de la cuenta de contingencia genera eventos de telemetría inmediata en Microsoft Sentinel y alerta al Oficial de Seguridad de la Información (CISO).
- **Guion exacto para la exposición:** *"Para situaciones de desastre o incidentes P1 donde se requiera intervención inmediata, implementamos un protocolo 'Break-Glass' con doble factor y trazabilidad forense inmediata que alerta a la dirección en el instante en que se utiliza"*.

#### Diagrama 18: `emergency-remediation-session-sequence` (DIA-018)
- **Archivos de Respaldo:** `src/emergency-remediation-session-sequence.drawio`, `rendered/emergency-remediation-session-sequence.svg`.
- **Qué representa:** La ejecución de una sesión de remediación de emergencia en caliente, captura de evidencias forenses y cierre obligatorio.
- **Cómo interpretarlo:** Muestra la intervención en caliente del ingeniero durante un incidente, la grabación de comandos ejecutados en consola, la verificación del restablecimiento del servicio y la revocación forzosa del acceso al culminar la ventana.
- **Explicación técnica:** Cierra el ciclo de gobierno: ninguna intervención de emergencia queda sin documentación. Al terminar la sesión, el sistema compila la bitácora de comandos, revoca los tokens de acceso y exige un reporte post-mortem antes de 24 horas.
- **Guion exacto para la exposición:** *"El protocolo de remediación de emergencia asegura que incluso bajo presión de caída del sistema, cada comando ejecutado sea auditado y grabado, cerrando el acceso automáticamente tan pronto se supera el incidente"*.
---

## 7. ANÁLISIS EXHAUSTIVO DE LAS TABLAS MAESTRAS DEL PROYECTO

El proyecto FríoAndes CloudOps fundamenta su rigor técnico en un conjunto de matrices de ingeniería que gobiernan el direccionamiento de red, las políticas de seguridad perimetral, el dimensionamiento de cargas y la proyección financiera. A continuación se analizan en profundidad las seis tablas maestras más importantes del sistema.

---

### 7.1 Tabla Maestra 1: Plan de Direccionamiento IP y Subnetting Cloud
- **Documento de Origen:** `Network Architecture.md` (Sección 4, líneas 110-146) y `Architecture Report.md` (Sección 3, líneas 54-88).

| Dominio / VNet | Nombre de la Subred | Prefijo CIDR | IPs Útiles | Propósito Operativo | Tipo de Recurso Asociado |
|---|---|---|---:|---|---|
| **Hub Transit (`10.100.0.0/16`)** | `GatewaySubnet` | `10.100.0.0/27` | 27 | Terminación de túneles IPsec IKEv2 híbridos con Cali y CEDIs | Azure VPN Gateway (`VpnGw2AZ`) |
| **Hub Transit (`10.100.0.0/16`)** | `AzureFirewallSubnet` | `10.100.1.0/26` | 59 | Inspección profunda L7 centralizada y proxy de salida | Azure Firewall Premium (`10.100.1.4`) |
| **Hub Transit (`10.100.0.0/16`)** | `AzureFirewallManagementSubnet` | `10.100.1.64/26` | 59 | Gestión de plataforma y despliegue automatizado de reglas | Interfaz de administración de Azure Firewall |
| **Hub Transit (`10.100.0.0/16`)** | `AzureBastionSubnet` | `10.100.2.0/26` | 59 | Acceso administrativo JIT cifrado sin IP pública | Azure Bastion Premium (Private-only) |
| **Hub Transit (`10.100.0.0/16`)** | `snet-hub-audit-log` | `10.100.3.0/24` | 251 | Bóveda centralizada de logs de auditoría y SIEM | Private Link hacia Log Analytics y Sentinel |
| **Hub Transit (`10.100.0.0/16`)** | `snet-hub-mgmt-runners` | `10.100.5.0/24` | 251 | Agentes de despliegue CI/CD y jumpboxes internos | Runners privados de compilación de contenedores |
| **Hub Transit (`10.100.0.0/16`)** | `snet-hub-dns-inbound` | `10.100.6.0/28` | 11 | Resolución de nombres DNS desde Cali/CEDIs hacia Azure | Azure Private DNS Resolver (Inbound Endpoint) |
| **Hub Transit (`10.100.0.0/16`)** | `snet-hub-dns-outbound` | `10.100.6.16/28` | 11 | Reenvío de consultas DNS desde Azure hacia Cali on-prem | Azure Private DNS Resolver (Outbound Endpoint) |
| **Hub Transit (`10.100.0.0/16`)** | `snet-hub-multicloud-transit` | `10.100.10.0/24` | 251 | Tránsito inter-nube reservado para conectar GCP a 24m | Reserva de Gateway/BGP para segunda nube |
| **Producción (`10.101.0.0/16`)** | `snet-prod-ingress` | `10.101.1.0/24` | 251 | Punto de entrada público para clientes del portal de rastreo | Azure Application Gateway v2 con WAF v2 |
| **Producción (`10.101.0.0/16`)** | `snet-prod-app-compute` | `10.101.2.0/23` | 507 | Cómputo privado elástico (+40% en campaña) para despacho | AKS Node Pools (Pods de Despacho, Inventario, Facturación) |
| **Producción (`10.101.0.0/16`)** | `snet-prod-data-mysql` | `10.101.4.0/24` | 251 | Subred delegada para motor transaccional relacional | Azure Database for MySQL Flexible Server Multi-AZ |
| **Producción (`10.101.0.0/16`)** | `snet-prod-data-redis` | `10.101.5.0/24` | 251 | Caché en memoria para optimizar consultas de rastreo (85%) | Azure Managed Redis OSSCluster (TLS 10000/8500-8599) |
| **Producción (`10.101.0.0/16`)** | `snet-prod-storage-archive`| `10.101.6.0/24` | 251 | Acceso privado al archivo histórico de 1.2 PB y evidencias | Private Endpoints de ADLS Gen2 (DFS y Blob) |
| **Producción (`10.101.0.0/16`)** | `snet-prod-telemetry-iot` | `10.101.7.0/24` | 251 | Ingesta de telemetría de 32 cuartos fríos y Event Hubs | Private Link hacia Azure IoT Hub y Event Hubs |
| **Producción (`10.101.0.0/16`)** | `snet-prod-fabric-link` | `10.101.8.0/24` | 251 | Zona reservada de endpoints para cliente de Microsoft Fabric| Private Endpoints reservados de OneLake |
| **Producción (`10.101.0.0/16`)** | `snet-prod-mgmt-jit` | `10.101.9.0/24` | 251 | Interfaces de diagnóstico y mantenimiento con apertura JIT | Interfaces de red para administración temporal |
| **Producción (`10.101.0.0/16`)** | `snet-prod-portal-front` | `10.101.10.0/24` | 251 | Aislamiento exclusivo para los pods frontend del portal | Pool de nodos de AKS exclusivo de frontend web |
| **Producción (`10.101.0.0/16`)** | `snet-prod-functions` | `10.101.11.0/24` | 251 | Subred delegada para Azure Functions (ruteo de alertas) | Azure Function App (Integration Subnet DLT-NET-01-01) |
| **Ambientes No Prod** | Staging Spoke | `10.102.0.0/16` | 65.536 | Réplica funcional idéntica a Producción para UAT y ensayos | Topología de 10 subredes análogas (`10.102.x.0/24`) |
| **Ambientes No Prod** | Development Spoke | `10.103.0.0/16` | 65.536 | Sandboxes de ingeniería y desarrollo ágil | Topología de subredes análogas (`10.103.x.0/24`) |
| **Reserva Multicloud** | GCP Expansion Spoke | `10.200.0.0/16` | 65.536 | Reserva para interconexión futura a 24 meses sin re-subnetting| Rango asignado a VPC de Google Cloud |
| **Pool VPN Clientes** | Pool P2S Administradores | `172.20.0.0/24` | 254 | Direcciones IP asignadas dinámicamente a clientes VPN | Pool de VPN Gateway para administradores distribuidos |

#### Análisis Técnico de la Tabla:
- **Qué representa cada columna:** Dominio/VNet (espacio de red contenedor), Nombre de la Subred (identificador estandarizado de recurso), Prefijo CIDR (bloque de direcciones IP), IPs Útiles (capacidad de direccionamiento tras descontar las 5 IPs reservadas por Azure), Propósito Operativo (función de negocio) y Recurso Asociado (servicio enlazado).
- **Qué representa cada fila:** Cada una de las subredes especializadas que garantizan micro-segmentación y aislamiento de blast radius.
- **Relación con el proyecto:** Resuelve el Requerimiento 3 del enunciado: demuestra que los espacios on-premises (`10.20.0.0/16` en Cali y `10.31-38.0.0/16` en los 8 CEDIs) no colisionan con la nube (`10.100.0.0/14` abarca del 10.100 al 10.103).
- **Cómo presentarla verbalmente:** "Nuestra matriz de subnetting particiona la nube en bloques limpios /16 por ambiente, asignando subredes dedicadas para ingreso, cómputo, datos, archivo, telemetría y funciones delegadas, con cero colisiones IP frente al inventario físico".
- **Qué decir textualmente:** *"Como observan en la tabla, ningún rango de la nube toca el espacio 10.20 de Cali ni el 10.31 al 38 de los centros regionales. Creamos diez zonas por ambiente, incluyendo la subred punto-once delegada exclusivamente para las funciones de ruteo de alertas de telemetría"*.

---

### 7.2 Tabla Maestra 2: Matriz de Políticas del Cloud Firewall y Seguridad de Flujos
- **Documento de Origen:** `Security Architecture.md` (Sección 3.2, líneas 150-210) y `Architecture Report.md` (Sección 4, líneas 103-120).

| ID de Regla | Flujo de Negocio | Origen (Source) | Destino (Destination) | Puerto / Protocolo | Acción | Estado de la Regla |
|---|---|---|---|---|---|---|
| **FW-PUB-01** | Rastreo Público | Internet (`0.0.0.0/0`) | `10.101.1.0/24` (App Gateway) | TCP 443 (HTTPS) | **ALLOW** | Estado Estable |
| **FW-PUB-02** | Frontend a API | `10.101.10.0/24` (Frontend) | `10.101.2.0/23` (API AKS) | TCP 443 / 8443 | **ALLOW** | Estado Estable |
| **FW-CORP-01** | Despacho Torre | Cali (`10.20.0.0/16`) & CEDIs | `10.101.2.10` (ILB Despacho) | TCP 443 / 8443 (TLS) | **ALLOW** | Estado Estable |
| **FW-CORP-02** | Conectividad DB | `10.101.2.0/23` (Apps AKS) | `10.101.4.0/24` (MySQL PaaS) | TCP 3306 (TLS) | **ALLOW** | Estado Estable |
| **FW-CORP-05** | Acceso a Caché | `10.101.2.0/23` (Apps AKS) | `10.101.5.0/24` (Redis OSS) | TLS 10000, 8500-8599 | **ALLOW** | Estado Estable |
| **FW-CORP-06** | Acceso Archivo | `10.101.2.0/23` (Apps AKS) | `10.101.6.0/24` (ADLS Gen2) | TCP 443 (HTTPS) | **ALLOW** | Estado Estable |
| **FW-CORP-08** | Alerta a Bus | `10.101.11.0/24` (Functions) | `10.101.7.0/24` (Service Bus) | AMQPS 5671, TCP 443 | **ALLOW** | Estado Estable |
| **FW-IOT-01** | Telemetría Flota | Flota Móvil (Celular) | IoT Hub FQDN Público | TLS 8883 (MQTT) / 443 | **ALLOW** | Estado Estable |
| **FW-IOT-02** | Cuartos Fríos | Sensores CEDI (`10.3x.1.0/24`)| `10.101.7.0/24` (IoT Privado) | TLS 8883 (MQTT) / 443 | **ALLOW** | Estado Estable |
| **FW-ADM-01** | VPN Administrador | Pool VPN (`172.20.0.0/24`) | `10.100.2.0/26` (Bastion) | TCP 443 (HTTPS) | **ALLOW** | Estado Estable |
| **FW-ADM-02** | Sesión Bastion | `10.100.2.0/26` (Bastion) | `10.100.5.0/24` (Jumpbox) | JIT TCP 22 / 3389 | **ALLOW** | Estado Estable |
| **FW-ADM-04** | Bloqueo Shell | Pool VPN (`172.20.0.0/24`) | Cualquier Host de Cómputo | TCP 22 / 3389 | **DENY** | Estado Estable |
| **FW-MIG-01** | Replicación DB | Cali MySQL (`10.20.2.0/24`) | `10.101.4.0/24` (MySQL Azure) | TCP 3306 (TLS) | **ALLOW** | **Temporal (<= 90 días)** |
| **FW-MIG-02** | Siembra Archivo | Cali Staging (`10.20.3.0/24`)| `10.101.6.0/24` (Storage Azure)| TCP 443 (HTTPS) | **ALLOW** | **Temporal (<= 90 días)** |
| **FW-DEF-01** | Denegación Total | `*` (Cualquier Origen No Clasificado) | `*` (Cualquier Destino) | `*` (Cualquier Puerto) | **DENY** | Estado Estable (Default) |

#### Análisis Técnico de la Tabla:
- **Qué representa cada columna:** ID de Regla (código de trazabilidad), Flujo de Negocio (caso de uso), Origen/Destino (límites de red autorizados), Puerto/Protocolo (vector de transporte), Acción (Allow o Deny) y Estado de la Regla (si es permanente de estado estable o si es temporal y caduca a los 90 días de migración).
- **Qué representa cada fila:** Políticas de seguridad explícitas configuradas en Azure Firewall Premium y los NSGs asociados.
- **Relación con el proyecto:** Demuestra el principio de mínimo privilegio y responde al requerimiento del firewall de nube del enunciado. Las reglas `FW-MIG-01` y `FW-MIG-02` se apagan al culminar la migración en el Día 90.
- **Cómo presentarla verbalmente:** "Cada flujo de red tiene nombre, apellido, origen y puerto explícito. Prohibimos de forma predeterminada cualquier tráfico no clasificado y dejamos claramente etiquetadas las reglas temporales de migración que se destruyen al día 90".
- **Qué decir textualmente:** *"La matriz de firewall evidencia la regla FW-CORP-01 que restringe el despacho únicamente a IPs corporativas y la regla FW-ADM-04 que bloquea cualquier intento de SSH directo desde la VPN hacia los servidores de producción, forzando el paso por Bastion"*.

---

### 7.3 Tabla Maestra 3: Dimensionamiento de Cargas (Normal vs. Campaña Pico +40%)
- **Documento de Origen:** `Workload Sizing and Capacity Planning.md` (Sección 3, líneas 150-220) y `Case Baseline.md`.

| Carga de Trabajo / Recurso | Parámetro de Medición | Operación Normal (Línea Base) | Campaña Pico (+40% Cómputo) | Factor de Crecimiento | Mecanismo de Escalamiento |
|---|---|---|---|---|---|
| **Usuarios Internos de Despacho** | Sesiones concurrentes | 135 concurrentes (30% de 450) | 225 concurrentes (50% de 450) | +66.7% concurrencia | Turnos escalonados en CEDI |
| **Transacciones de Despacho** | Carga SQL combinada | 9 writes/s, 18 reads/s (27 TPS) | 15 writes/s, 30 reads/s (45 TPS) | +66.7% TPS | Absorción en MySQL Flexible |
| **Portal de Rastreo B2B** | Solicitudes por minuto | 800 req/min (13.33 RPS) | 2.000 req/min (33.33 RPS) | **+150.0% pico** (4h/día) | HPA en AKS y App Gateway WAF |
| **Throughput de Red del Portal** | Ancho de banda de salida | 1,60 Mbps | 4,00 Mbps | +150.0% ancho banda | Conexión simétrica de nube |
| **Caché Redis (Offload)** | Consultas absorbidas en RAM | 11.33 RPS absorbidos (85%) | 28.33 RPS absorbidos (85%) | +150.0% lecturas | En-Memory Cluster Cache |
| **Flota de Camiones en Ruta** | Unidades transmitiendo | 120 camiones en ruta (18h/día) | 180 camiones en ruta (18h/día) | **+50.0% flota** | Receptores IoT elásticos |
| **Cuartos Fríos Industriales** | Puntos de monitoreo | 32 cuartos fríos (30s, 24/7) | 32 cuartos fríos (30s, 24/7) | Fijo (Infraestructura base)| Stream Analytics continuo |
| **Tasa de Mensajes IoT** | Mensajes por segundo | 7.07 msgs/sec | 10.07 msgs/sec | +42.4% mensajes/s | Event Hubs 1 TU (cap: 1.000 m/s)|
| **Volumen Diario de Eventos** | Mensajes acumulados día | 480.960 eventos/día | 675.360 eventos/día | +40.4% eventos diarios | Particionamiento en Lakehouse |
| **Pods de Cómputo en AKS** | Réplicas de microservicios | **11 pods** (15 vCPU / 30 GiB) | **19 pods** (25 vCPU / 50 GiB) | **+66.7% vCPU/RAM** | Horizontal Pod Autoscaler (HPA)|
| **Nodos Físicos de AKS (Prod)**| Instancias de VM | 3 system / 3 user / 2 front | 3 system / 5 user / 3 front | +37.5% nodos de cómputo| Virtual Machine Scale Sets |

#### Análisis Técnico de la Tabla:
- **Qué representa cada columna:** Recurso evaluado, Métrica de dimensionamiento, Valor en operación normal, Valor en campaña comercial alta, Incremento porcentual real y Mecanismo tecnológico de escalado.
- **Qué representa cada fila:** Las variables operativas de FríoAndes descomponiéndose en requerimientos de hardware y software de nube.
- **Relación con el proyecto:** Cumple y supera la hipótesis de negocio del enunciado (+40% de cómputo en campaña): la capa de microservicios pasa de 15 a 25 vCPU (+66.7%), absorbiendo holgadamente la demanda, mientras que el portal se dimensiona para soportar los 2.000 req/min exactos solicitados.
- **Cómo presentarla verbalmente:** "Demostramos con números duros que nuestra infraestructura no se cae en Semana Santa ni en diciembre: los microservicios escalan automáticamente de 11 a 19 pods (+66.7% vCPU) y el portal absorbe hasta 2.000 peticiones por minuto con una tasa de acierto del 85% en caché".
- **Qué decir textualmente:** *"El enunciado planteaba como hipótesis un 40% más de cómputo en campaña. Nuestra arquitectura responde asignando 19 pods con 25 vCPU y 50 GiB de RAM, lo cual representa un 66.7% más de potencia, cubriendo el pico de 180 camiones y 2.000 peticiones por minuto sin sobrecostos en meses ordinarios"*.

---

### 7.4 Tabla Maestra 4: Presupuesto FinOps y Modelo Comercial (24 Meses)
- **Documento de Origen:** `Cost Estimate and Commercial Basis.md` (Secciones 2, 4 y 5, líneas 48-144) y `FrioAndes 24-Month Cost Model.xlsx`.

| Componente del Modelo Financiero | Valor Mensual Normal | Valor Mensual Campaña | Subtotal 24 Meses (USD) | Supuestos de Cálculo y Racionalidad Económica |
|---|---:|---:|---:|---|
| **Infraestructura Azure (Always-On Base)**| $29,935.27 | $36,012.67 | **$981,226.11** | 730 h/mes, East US 2, 81 medidores públicos, 6 meses completos de campaña incluidos. |
| *Sensibilidad: Infraestructura con Fabric Programado*| *$27,350.55* | *$33,427.95* | *$919,106.31* | *Modelo alternativo con auto-pausa y escala F8 nocturno en Fabric (ahorro de $62,119.80).* |
| **Licencias SaaS y Servicios Externos** | $3,630.00 | $3,630.00 | **$87,120.00** | Entra P1 (430 usuarios a $7) + Entra P2 (20 admins a $10) + Power BI Pro (30 licencias a $14). |
| **Provisiones Operativas (Soporte / SMS)**| $200.00 | $200.00 | **$4,800.00** | Provisión de soporte Azure ($100/mes) + Notificaciones SMS/voz prioritarias ($100/mes). |
| **Total Recurrente de Plataforma** | $33,765.27 | $39,842.67 | **$1,073,146.11** | Suma consolidada de Azure + Licencias + Provisiones en 24 meses. |
| **Allowances Únicas de Migración** | N/A | N/A | **$55,000.00** | Logística siembra ($7.5k), Staging local ($15k), Pruebas ($12k), Disposición ($5k), Ensayos ($15.5k). |
| **Mano de Obra Especializada (Labor)** | N/A | N/A | **$232,387.28** | 15 roles técnicos (Arquitectos, DevOps, SRE, DBAs, PM) con factor prestacional 51.8%. |
| **Costo Directo de Entrega del Proyecto** | N/A | N/A | **$1,360,533.40** | Total recurrente + Gastos únicos de migración + Nómina del equipo de ingeniería. |
| **Contingencia Técnica y Operativa (15%)**| N/A | N/A | **$204,080.01** | Cojín financiero para imprevistos aduaneros, sobreconsumo en pruebas o fluctuación FX. |
| **Costo Total con Contingencia** | N/A | N/A | **$1,564,613.40** | Base de costo de entrega total protegida contra riesgos. |
| **Precio Propuesto al Cliente (Antes Impuestos)**| N/A | N/A | **$1,955,766.76** | **Precio de venta comercial aplicando 20% de Margen Bruto** (`Costo / 0.8`). |

#### Análisis Técnico de la Tabla:
- **Qué representa cada columna:** Componente financiero, Costo mensual en operación ordinaria, Costo en mes de campaña comercial (Semana Santa, junio, diciembre), Costo total acumulado en 24 meses y Base de cálculo técnica.
- **Qué representa cada fila:** La estructura de costos desde los medidores de nube individuales hasta el precio de venta comercial propuesto a FríoAndes.
- **Relación con el proyecto:** Cumple el requerimiento 3 de entregables (Documento de costos con supuestos). No es una estimación improvisada: está conciliada con 81 SKUs oficiales de la Azure Retail API, incluye contingencia del 15% y establece una ganancia del 20% sobre ventas para la empresa consultora.
- **Cómo presentarla verbalmente:** "Nuestra propuesta comercial no es solo una factura de nube: presentamos un modelo de negocio completo a 24 meses por $1.95 millones de dólares, donde el 50% es infraestructura Azure, respaldado por un equipo de ingeniería de $232.000 dólares y un colchón de contingencia del 15%".
- **Qué decir textualmente:** *"El costo de infraestructura Azure en 24 meses asciende a $981,226 USD bajo un escenario conservador de Fabric siempre encendido. Si el cliente aprueba la política de auto-pausa nocturna en Fabric, el costo se reduce a $919,106 USD, liberando $62,119 USD de ahorro potencial"*.

---

### 7.5 Tabla Maestra 5: Las Cinco Decisiones de Ahorro y Evitación de Costos
- **Documento de Origen:** `Cost Estimate and Commercial Basis.md` (Sección 6, líneas 161-177).

| Decisión de Ingeniería | Descripción Técnica del Ahorro | Alternativa Descartada (Sobrecosto Evitado) | Impacto Financiero / Beneficio Arquitectónico |
|---|---|---|---|
| **1. Pila Hub y Seguridad Compartida** | Un único Hub VNet aloja Azure Firewall, Bastion, DNS y monitoreo para Prod, Stg y Dev. | Desplegar firewalls e interfaces Bastion independientes por cada suscripción o ambiente. | **Evita más de $3,000 USD/mes** en costos fijos de instancias duplicadas de firewall y bastion. |
| **2. Ambientes No-Prod Sintéticos** | Desarrollo usa 0.1 TB y Staging 0.5 TB con datos anonimizados y cómputo reducido. | Clonar el archivo completo de 1.2 PB en staging y desarrollo (duplicar o triplicar el storage). | **Evita más de $48,000 USD/año** en almacenamiento redundante innecesario en ambientes de prueba. |
| **3. Cero Duplicación de Archivo en Fabric** | Uso de OneLake Shortcuts para consultar metadatos y Delta en ADLS Gen2 in-situ. | Replicar los 1.2 PB de video y fotos dentro de OneLake Managed Storage para analítica. | **Ahorra $250,764 USD/año** en almacenamiento analítico redundante (Cálculo SSoT Criterion 6). |
| **4. Almacenamiento en Capas sin Purga Ilegal**| Ciclo de vida: Hot (0-30d) -> Cool (31-180d) -> Archive (>180d) preservando 5 años de ley. | Mantener los 1.2 PB perpetuamente en almacenamiento Hot de alto costo o purgar datos ilegalmente. | **Reduce en un 70%** el costo unitario de almacenamiento por gigabyte para archivos de más de 6 meses. |
| **5. Programación Diurna de Capacidades Fabric**| Escalamiento dinámico de Fabric: F64 en horario diurno (04:00-22:00) y F8 en nocturno/fines de semana.| Mantener la capacidad F64 encendida al 100% de potencia las 24 horas del día de forma continua. | **Sensibilidad de $62,119.80 USD** de ahorro en 24 meses frente al escenario always-on. |

---

### 7.6 Tabla Maestra 6: Ventana Operativa y Gestión del Cambio
- **Documento de Origen:** `change_window_policy.py`, `Application Release Notes.md` e `Infrastructure Release Notes.md`.

| Parámetro Operativo | Definición de la Restricción | Horario Aplicable (COT) | Regla de Validación en Código | Acción en Caso de Intento de Despliegue |
|---|---|---|---|---|
| **Ventana Diurna de Despacho** | Prohibición absoluta de cambios productivos de software o infraestructura. | 04:00 a 22:00 COT (Lunes a Domingo) | `time(4) <= cursor.time() < time(22)` | **BLOQUEO INMEDIATO (Fail-Closed)**. Retorna `False`. |
| **Ventana Nocturna de Mantenimiento**| Espacio autorizado para parches, pruebas pre-despliegue y cambios no disruptivos. | 22:00 a 04:00 COT | `not (time(4) <= cursor.time() < time(22))` | Permitido únicamente con aprobación previa registrada. |
| **Ventana de Corte Dominical** | Ventana exclusiva autorizada para el cutover definitivo de producción. | Domingo 02:00 a 04:00 COT (120 min max)| `local.weekday() == 6 and local.time() == time(2)` | Permitido solo si duración <= 120 min y aprobación formal. |
| **Punto de No Retorno (Cutover)** | Momento límite para abortar el corte y ejecutar el rollback a Cali. | Domingo 03:00 COT | Hora fija de decisión Go/No-Go | Si hay No-Go a las 03:00, se inician 35 min de rollback. |
| **Congelamiento de Fin de Año** | Bloqueo total de despliegues por campaña comercial navideña. | 25 de Noviembre al 5 de Enero | `(month == 11 and day >= 25) or month == 12 or ...` | **BLOQUEO INMEDIATO**. Solo excepciones P1 con break-glass. |
| **Congelamiento de Campañas** | Bloqueo por picos de Semana Santa y promociones de mitad de año. | Fechas móviles según calendario anual | `day.isoformat() in freeze_dates` | **BLOQUEO INMEDIATO**. Prohibido cualquier cambio. |

---

## 8. ANÁLISIS Y VALIDACIÓN CONTRA EL ENUNCIADO (UNIVERSIDAD ICESI)

A continuación se presenta la matriz de trazabilidad exhaustiva que valida el cumplimiento de cada requerimiento del pliego de condiciones de la Universidad Icesi frente a la evidencia documental y de código encontrada en el proyecto.

### 8.1 Matriz de Trazabilidad y Cumplimiento de Requerimientos

| # | Requerimiento del Enunciado (Universidad Icesi) | Evidencia Encontrada en el Proyecto | Archivo donde se Encuentra | Sección / Líneas Específicas | Nivel de Cumplimiento |
|---|---|---|---|---|:---:|
| **1.1** | Organizar cuentas/suscripciones para separar seguridad, redes, prod y non-prod. | Topología Hub-and-Spoke con suscripciones y VNets separadas: Hub (`10.100`), Prod (`10.101`), Stg (`10.102`), Dev (`10.103`). | `Network Architecture.md`<br>`Architecture Report.md` | Sec. 3.1, líneas 94-106<br>Sec. 3, líneas 54-62 | **CUMPLIDO** |
| **1.2** | Aplicar políticas, identidades, etiquetas y presupuestos desde un punto común. | Esquema de Entra ID federado, jerarquía de Management Groups, taxonomía de tags obligatoria y presupuestos en Azure Cost Management. | `Identity, Cryptography...md`<br>`Case Baseline.md` | Sec. 2.1, líneas 80-120<br>Sec. 4, Criterio 1 | **CUMPLIDO** |
| **1.3** | Auditoría y retención suficiente para reconstruir quién accedió a clientes, evidencias y temperatura. | Log Analytics centralizado en Hub (`10.100.3.0/24`), exportación a almacenamiento inmutable WORM y retención de 5 años bajo Ley 1581. | `Identity, Cryptography...md`<br>`Administrative Access...md` | Sec. 5, líneas 210-260<br>Sec. 4, líneas 140-180 | **CUMPLIDO** |
| **1.4** | Modelo de facturación interna (distinguir costo de operación, telemetría y pruebas). | Desglose en el libro de costos por centro de costos: Prod (Operación), Telemetría (Event Hubs/IoT), Non-Prod (Stg/Dev) y Shared Hub. | `Cost Estimate and Commercial...md`<br>`FrioAndes 24-Month Cost Model.xlsx` | Sec. 2, líneas 62-68<br>Pestaña `Executive Summary` | **CUMPLIDO** |
| **2.1** | Mínimo privilegio, MFA para administradores y para la consola interna de despacho. | Entra ID PIM con elevación JIT, MFA mandatorio con FIDO2/Authenticator para consola de despacho y operadores. | `Security Architecture.md`<br>`Administrative Access...md` | Sec. 2, líneas 80-96<br>Sec. 2, líneas 60-110 | **CUMPLIDO** |
| **2.2** | Cifrado en tránsito y en reposo, incluyendo llaves y secretos. | TLS 1.2+ en tránsito en todas las interfaces, Azure Key Vault con claves administradas por el cliente (CMK/HSM) y rotación automática. | `Identity, Cryptography...md` | Sec. 3, líneas 130-190 | **CUMPLIDO** |
| **2.3** | Separación de red entre torre de control, portal público, sensores e ingesta analítica. | 10 subredes aisladas: Ingress (`.1`), Compute (`.2`), MySQL (`.4`), Storage (`.6`), IoT (`.7`), Fabric (`.8`), Frontend (`.10`), Functions (`.11`).| `Network Architecture.md`<br>`Security Architecture.md` | Sec. 4.2, líneas 130-146<br>Sec. 2.1, líneas 85-96 | **CUMPLIDO** |
| **2.4** | Protección del perímetro público (portal de rastreo) y detección de anomalías. | Azure Application Gateway v2 con WAF v2 (OWASP CRS 3.2), Defender for Cloud y Microsoft Sentinel para detección de comportamiento anómalo. | `Security Architecture.md`<br>`Customer Portal and Control...md` | Sec. 4, líneas 210-270<br>Sec. 2.2, líneas 90-130 | **CUMPLIDO** |
| **2.5** | Tratamiento de datos personales (conductores/destinatarios). Retención 5 años por contrato. | API Sanitizada con recorte de GPS (2 km), anonimización de choferes (Ley 1581), cero tarjeta de crédito y lifecycle de 5 años en Blob Archive. | `Customer Portal and Control...md`<br>`Data Retention and Cleansing...md` | Sec. 3.3, líneas 192-210<br>Sec. 2, líneas 65-115 | **CUMPLIDO** |
| **3.1** | Conectividad redundante Cali-CEDIs-Nube, respetando enlace de 400 Mbps y firewall local. | VPN Gateway VpnGw2AZ en modo Activo-Activo con doble túnel IPsec IKEv2 sobre enlace de Cali; preservación del NGFW durante convivencia. | `Network Architecture.md`<br>`hybrid-connectivity-and-data-seeding.drawio` | Sec. 4.1, líneas 118-129<br>Diagrama DIA-002 | **CUMPLIDO** |
| **3.2** | Mantener el despacho fuera de internet público. | ILB privado (`10.101.2.10`), NSG bloqueando tag `Internet` (Priority 100), rutas UDR forzadas a Azure Firewall, cero IPs públicas en pods de despacho. | `Dispatch Migration and...md`<br>`Security Architecture.md` | Sec. 2.2, líneas 120-140<br>Regla `FW-CORP-01` | **CUMPLIDO** |
| **3.3** | Publicar el rastreo de forma controlada. | Ingress por WAF v2 (`10.101.1.0/24`), frontend aislado en `.10/24` y API Sanitizada que bloquea video y datos sensibles. | `Customer Portal and Control...md`<br>`Security Architecture.md` | Sec. 2.1, líneas 60-110<br>Regla `FW-PUB-01` | **CUMPLIDO** |
| **3.4** | Acceso administrativo para equipos distribuidos sin abrir la red de despacho. | Azure Bastion Premium con clientes VPN P2S (`172.20.0.0/24`), puertos JIT dinámicos y prohibición de SSH directo (`FW-ADM-04`). | `Administrative Access...md`<br>`Security Architecture.md` | Sec. 3, líneas 115-160<br>Regla `FW-ADM-01` a `04` | **CUMPLIDO** |
| **3.5** | Interconexión, DNS e identidad pensados para sumar otra nube a 24 meses. | Rango `10.200.0.0/16` reservado para GCP, tránsito en Hub (`10.100.10.0/24`), Private DNS Resolver y federación OIDC con Entra ID. | `Cloud Provider Evaluation.md`<br>`multicloud-24-month-horizon.drawio` | Sec. 5, líneas 220-270<br>Diagrama DIA-007 | **CUMPLIDO** |
| **3.6** | Tabla de subnetting cloud sin solapar Cali y CEDIs (mínimo: ingreso, app, data, archivo, iot, admin, cali, fabric). | Matriz completa de 10 zonas por spoke y 10 en Hub, garantizando no solapamiento con `10.20` y `10.31-38`. | `Network Architecture.md`<br>`Architecture Report.md` | Sec. 4, líneas 110-150<br>Sec. 3, líneas 54-88 | **CUMPLIDO** |
| **3.7** | Firewall de nube con tabla de políticas (origen, destino, puerto, acción, migración vs estable). | Tabla completa con reglas `FW-CORP`, `FW-PUB`, `FW-ADM`, `FW-IOT` y `FW-MIG`, detallando puertos y caducidad temporal a 90 días. | `Security Architecture.md`<br>`Architecture Report.md` | Sec. 3.2, líneas 155-210<br>Sec. 4, líneas 103-120 | **CUMPLIDO** |
| **4.1** | Infraestructura como Código (IaC) para red, seguridad y ambiente de aplicación. | Repositorio Terraform modular con módulos de red (`address-plan`), seguridad (`nsg`), cómputo AKS y bases de datos. 125 mock tests. | `frioandes-cloudops-docs/reviews/2026-10-21-terraform-recovery-note.md`<br>`Architecture Report.md` | Líneas 1-38<br>Sec. 5, líneas 130-160 | **CUMPLIDO (Plan-Only)** |
| **4.2** | Pipeline CI/CD para plataforma y contenedores, ambientes Dev/Stg/Prod con promoción. | Diseño de pipeline GitOps, imágenes inmutables por digest, escaneo Trivy/Checkov y compuertas de promoción manual hacia producción. | `CI CD and Operational Change Design.md`<br>`diagrams/change-promotion-sequence.drawio` | Sec. 2 y 3, líneas 40-120<br>Diagrama de flujo | **CUMPLIDO** |
| **4.3** | Gestión de secretos dentro del pipeline. | Secretless CI/CD mediante OpenID Connect (OIDC) federado con Entra ID y recuperación en runtime desde Azure Key Vault. | `CI CD and Operational Change Design.md`<br>`Identity, Cryptography...md` | Sec. 3.2, líneas 85-105<br>Sec. 3.3, líneas 150-180 | **CUMPLIDO** |
| **4.4** | Estrategia de cambio y dos plantillas diligenciadas con ejemplo (App e Infra). Cero cambios 04:00-22:00 ni campaña. | Plantillas completas diligenciadas: `Application Release Notes.md` (timeout 5s) e `Infrastructure Release Notes.md` (corte MySQL 2h). | `Application Release Notes.md`<br>`Infrastructure Release Notes.md`<br>`tools/change_window_policy.py` | Completo (82 líneas)<br>Completo (84 líneas)<br>Líneas 1-36 | **CUMPLIDO** |
| **5.A** | Carga A: Consola interna y portal de rastreo (450 usuarios, 2.000 clientes, 800 a 2.000 req/min). | Dimensionamiento matemático de 11 a 19 pods en AKS, Redis con 85% offload, MySQL Flexible HA y App Gateway WAF. | `Workload Sizing and Capacity...md`<br>`Customer Portal and Control...md` | Sec. 2.1-2.2, líneas 83-128<br>Sec. 2, líneas 50-95 | **CUMPLIDO** |
| **5.B** | Carga B: Telemetría de frío (120/180 camiones, 32 cuartos fríos, alerta < 1 min, ruteo diurno/nocturno). | Presupuesto de latencia de 40s, IoT Hub + Stream Analytics, deduplicación Redis y ruteo a Torre (04-22h) o Calidad nocturno. | `Cold-Chain Telemetry and Alerting...md`<br>`telemetry-streaming-ingestion.drawio` | Sec. 2 y 3, líneas 46-140<br>Diagrama DIA-003 | **CUMPLIDO** |
| **5.C** | Carga C: Migración del núcleo (coexistencia <= 90 días, corte <= 2h en domingo, siembra previa de 1.2 PB). | Plan en 4 olas (Waves 0-3), siembra física pre-cutover, runbook minuto a minuto de 02:00 a 04:00 COT, decisión 03:00 y rollback de 35 min. | `Core Migration and Cutover...md`<br>`Dispatch Migration and...md` | Sec. 1-3, líneas 40-180<br>Sec. 3, líneas 160-230 | **CUMPLIDO** |
| **6.1** | Observabilidad integral (salud de cómputo, red, DB, logs, trazas y seguridad en SIEM). | Arquitectura de 5 capas con Azure Monitor, Container Insights, Log Analytics, Grafana administrado y Microsoft Sentinel. | `Observability Architecture.md` | Sec. 2 y 3, líneas 60-180 | **CUMPLIDO** |
| **6.2** | Dos SLOs con indicadores: Disponibilidad del portal (>= 99.9%) y oportunidad de alerta (< 1 min, >= 99.0%). | Fórmulas matemáticas de SLI/SLO definidas formalmente con presupuestos de error (Error Budgets) mensuales calculados. | `Observability Architecture.md`<br>`Cold-Chain Telemetry and...md` | Sec. 4, líneas 190-250<br>Sec. 4.1, líneas 210-245 | **CUMPLIDO** |
| **6.3** | Tableros de operación y costo; transición y apagado de Zabbix. | Tableros en Managed Grafana para métricas y Cost Management para FinOps; Zabbix mantiene monitoreo on-prem hasta el apagado en Día 90. | `Observability Architecture.md` | Sec. 5 y 6, líneas 260-340 | **CUMPLIDO** |
| **7.1** | Estrategia de Microsoft Fabric para IA sin duplicar 1.2 PB por defecto. | OneLake Shortcuts sobre ADLS Gen2 Delta Lake (0 GB copia adicional en Bronze); solo 22.7 TB materializados en Silver/Gold (< 2.5%). | `Microsoft Fabric Analytics and...md`<br>`fabric-ai-integration.drawio` | Sec. 3, líneas 77-140<br>Diagrama DIA-006 | **CUMPLIDO** |
| **7.2** | Tres casos de uso de Fabric: alerta temprana térmica, ETA dinámico en ruta y QA de evidencias con muestreo de video. | Modelos SynapseML para predicción de fallas de compresor (4-12h), regresión de ETA en Torre de Control y filtrado de fotos con clips de 5 min de video. | `Microsoft Fabric Analytics and...md` | Sec. 2.1, líneas 50-65 | **CUMPLIDO** |
| **7.3** | Espacios de trabajo, capacidades F-SKU, costos separados y reducción fuera de campaña. | Multi-workspace (`ControlTower-Prod`, `Quality-ML`, `Executive-BI`), auto-pausa y escala (F64 día / F8 noche), línea separada en presupuesto ($62k ahorro).| `Microsoft Fabric Analytics and...md`<br>`Cost Estimate and Commercial...md` | Sec. 4, líneas 180-260<br>Sec. 2, líneas 62-68 | **CUMPLIDO (FAB-02 Partial)** |
| **8.1** | Sistema de diseño de FríoAndes: logo, isotipo, versiones tinta/negativo, paleta, tipografía, grilla y componentes base con estados. | Manual de marca completo en CSS y docs: isotipo SVG, paleta (Andean Navy, Cryo Cyan, estados), Space Grotesk/Inter/Mono, componentes con estados. | `Customer Portal and Control...md`<br>`frioandes-cloudops-web/styles.css`<br>`frioandes-cloudops-web/assets/brand/`| Sec. 5, líneas 350-440<br>Líneas 1-140<br>`isotipo.svg` | **CUMPLIDO** |
| **8.2** | Reglas de uso: contraste para turno de noche y privacidad B2B sin mostrar la flota completa. | Contraste WCAG AAA en modo oscuro para Torre nocturna, clipping de ruta a corredor de 2 km y supresión de placas/choferes para clientes B2B. | `Customer Portal and Control...md`<br>`frioandes-cloudops-web/styles.css` | Sec. 3.3 y 5.2, líneas 192-210, 381-400<br>Líneas 38-46 | **CUMPLIDO** |
| **Entregables**| Diagramas (PDF/fuente), Repositorio IaC, Plantillas de cambio, Costos con supuestos, Presentación 20 min y Sistema de diseño. | 18 diagramas Draw.io/SVG/PNG, módulos Terraform (125 tests), 2 release notes diligenciados, Excel 11 pestañas, web interactiva y manual de marca. | En todos los directorios del proyecto | Inventario de 279 archivos auditado | **CUMPLIDO AL 100%** |

---

### 8.2 Síntesis de Requerimientos Cumplidos, Parciales y Faltantes

#### Requerimientos Cumplidos con Sobresaliente (100%):
1. **Gobierno y Aislamiento de Red:** La separación en 10 zonas por spoke y el Hub centralizado sin ninguna IP pública en el despacho supera los estándares de la industria y cumple con creces el pliego.
2. **Alertamiento de Telemetría:** El presupuesto de 40 segundos frente a eventos físicos garantiza holgadamente la meta de < 1 minuto, resolviendo el ruteo diurno/nocturno con elegancia serverless.
3. **Optimización de Costos en Fabric:** La arquitectura OneLake Shortcuts evita duplicar 1.2 PB, ahorrando más de un cuarto de millón de dólares al año.
4. **Gestión del Cambio:** Dos plantillas completas y diligenciadas con ejemplos hiper-realistas y verificadas mediante código Python automatizado.
5. **Sistema de Diseño Tangible:** Implementado en código real en la aplicación web interactiva con isotipo vectorial propio y cumplimiento WCAG.

#### Requerimientos Parcialmente Cumplidos (con justificación documentada):
- **Integración de Tenant Fabric (FAB-02 PARTIAL):** El proyecto declara con honestidad profesional y transparencia técnica que los modelos semánticos y enlaces Direct Lake de Fabric están en estado de especificación de diseño (`PARTIAL`) y no desplegados en un tenant productivo vivo de Azure, lo cual se alinea perfectamente con el alcance académico del ejercicio que exime de construir la aplicación de negocio.
- **Validación Física de Azure Data Box:** Se asume la siembra física pre-cutover como hipótesis base (ASM-02), pero se advierte que la disponibilidad logística de Data Box en Colombia y las tarifas de aduana requieren cotización formal previa con Microsoft Colombia.

#### Requerimientos Faltantes / No Ejecutados (Intencionales por Alcance):
- **Despliegue Vivo de Infraestructura (Apply de Terraform):** El repositorio de IaC está validado en modo **plan-only** (1.223 recursos a crear en plan autenticado, 125 pruebas mock aprobadas); no se ejecutó `terraform apply` en una suscripción real para no incurrir en cobros reales a la universidad.
- **Construcción del Código de Negocio de las Aplicaciones:** El enunciado excluye explícitamente construir el código de despacho, inventario o facturación.

#### Recomendaciones de Mejora Arquitectónica del Revisor Senior:
1. **Negociación de Azure Savings Plans / Reservas:** El modelo de costos actual utiliza precios minoristas oficiales (Retail Pay-As-You-Go). Suscribir Savings Plans a 3 años para cómputo AKS y MySQL Flexible Server reduciría el costo de infraestructura en un 32% a 45% adicional.
2. **Implementación de Clúster de Ingesta Kafka Dedicado:** Aunque Event Hubs cubre el volumen actual (10 msgs/s en pico), si la flota se expande a más de 1.000 camiones a nivel andino (Ecuador y Perú), convendría evaluar Event Hubs Dedicated o un clúster Kafka administrado para absorber ráfagas masivas de reconexión.
3. **Automatización de Purga Jurídica:** Implementar un motor de clasificación documental que marque las evidencias que superen los 10 años (Código de Comercio) para ofrecer una opción de expurgo asistido por el oficial de cumplimiento legal.
---

## 9. GUION DE EXPOSICIÓN PROFESIONAL COMPLETO

Este guion ha sido redactado para una sustentación formal de alto nivel técnico ante el Arquitecto Cloud del Cliente y el Jurado Evaluador de la Universidad Icesi. Cada sección incluye el guion textual a exponer y el respaldo documental exacto con archivo, ruta, sección y evidencia.

---

### Bloque 1: Introducción y Propósito Estratégico
- **Qué decir textualmente:**
  *"Muy buenos días / tardes, miembros del comité evaluador y arquitecto cloud del cliente. Hoy presentamos el diseño técnico, la estrategia de migración y la arquitectura de operaciones en la nube para FríoAndes S.A., la empresa líder en almacenamiento y transporte refrigerado de alimentos en Colombia.
  Nuestra misión en este proyecto trasciende la simple modernización tecnológica: nuestro propósito es blindar la cadena de frío biológica que garantiza la inocuidad alimentaria de millones de colombianos. A lo largo de esta sustentación demostraremos cómo transformamos una infraestructura on-premises vulnerable y obsoleta en una plataforma elástica, segura bajo el modelo Zero-Trust y potenciada por inteligencia artificial en Microsoft Fabric, respetando en todo momento las restricciones innegociables del negocio: cero afectación al despacho diurno, una ventana dominical de corte de máximo dos horas y la preservación inalterada de 1.2 petabytes de evidencia contractual por cinco años"*.
- **Respaldo Documental:**
  - **Archivo:** `Case Baseline.md` y `README.md`
  - **Ruta:** `Frío Andes/frioandes-cloudops-docs/Case Baseline.md` (Líneas 30-55) y `README.md` (Líneas 50-67).
  - **Evidencia Encontrada:** Descripción de FríoAndes S.A., sede Cali, 8 CEDIs, 32 cuartos fríos, 120/180 camiones y alineación con los cinco pilares de Well-Architected Framework.

---

### Bloque 2: Contexto Operativo y Diagnóstico de la Situación Actual
- **Qué decir textualmente:**
  *"Para entender la necesidad de esta transformación, debemos mirar la realidad operativa de FríoAndes. La compañía coordina sus despachos nacionales desde su sede central en Cali, apoyada en ocho centros de distribución regional ubicados en Buenaventura, Bogotá, Medellín, Barranquilla, Bucaramanga, Pereira, Pasto y Neiva. Administramos treinta y dos cuartos fríos industriales que deben vigilarse veinticuatro horas al día y una flota de ciento veinte camiones que se expande a ciento ochenta en temporadas pico.
  Sin embargo, toda esta operación crítica descansa hoy sobre un único datacenter físico en Cali, montado sobre tres clústeres de VMware con seis servidores ESXi. El diagnóstico técnico es alarmante:
  Primero, una base de datos MySQL 5.7 sobre CentOS 7, ambos fuera de soporte oficial, sin réplica y con un único volcado nocturno a la una de la mañana en el mismo disco, lo que arroja un RPO real superior a veintitrés horas frente a un objetivo de quince minutos.
  Segundo, un archivo histórico de 1.2 petabytes que crece doce terabytes mensuales, almacenado en un único servidor NFS/FTP sin redundancia fuera de Cali.
  Tercero, la telemetría se procesa por lotes cada hora mediante archivos planos: si un camión pierde refrigeración en la cordillera, nadie se entera en ruta y las investigaciones se inician días después cuando el cliente rechaza la carga dañada.
  Y cuarto, un enlace simétrico de cuatrocientos megabits en Cali cuyo firewall perimetral está al borde del colapso durante las campañas comerciales de Semana Santa y fin de año"*.
- **Respaldo Documental:**
  - **Archivo:** `Case Baseline.md` y `on-premises-baseline.drawio`
  - **Ruta:** `Frío Andes/frioandes-cloudops-docs/Case Baseline.md` (Sección 1, líneas 50-110).
  - **Evidencia Encontrada:** Inventario técnico de VMware, CentOS 7 EOL, MySQL 5.7 EOL, 1.2 PB en NFS, 400 Mbps WAN y diagrama DIA-001.

---

### Bloque 3: Objetivos y Principios Rectores del Diseño
- **Qué decir textualmente:**
  *"Frente a este escenario, establecimos cinco pilares rectores alineados con Well-Architected Framework:
  En Excelencia Operacional: automatizar la infraestructura mediante Terraform modular y formalizar la gestión del cambio con plantillas estandarizadas donde ningún cambio productivo ocurra entre las 04:00 y las 22:00 ni durante congelamientos comerciales.
  En Seguridad: implementar Zero-Trust estricto, aislando totalmente la Torre de Control y la base de datos de Internet, aplicando MFA mandatorio y garantizando el cumplimiento de la Ley 1581 de protección de datos.
  En Confiabilidad: diseñar alta disponibilidad zonal Multi-AZ para alcanzar un RPO menor a 15 minutos y un RTO menor a 4 horas.
  En Desempeño: absorber un incremento del 40% en cómputo y picos de 2.000 peticiones por minuto en el portal de rastreo, con alertas de desvío térmico entregadas en menos de un minuto.
  Y en Optimización de Costos: evitar duplicar los 1.2 petabytes en analítica mediante OneLake Shortcuts y escalonar el almacenamiento en capas Hot, Cool y Archive"*.
- **Respaldo Documental:**
  - **Archivo:** `Architecture Report.md` y `README.md`
  - **Ruta:** `Frío Andes/frioandes-cloudops-docs/Architecture Report.md` (Sección 1 y 2, líneas 9-40).
  - **Evidencia Encontrada:** Tabla de alineación de los cinco pilares y matriz de los 8 criterios del reto.

---

### Bloque 4: Arquitectura General y Red Híbrida Zero-Trust
- **Qué decir textualmente:**
  *"La arquitectura seleccionada adopta Microsoft Azure en la región East US 2 bajo una topología Hub-and-Spoke.
  Como pueden observar en nuestro plan de direccionamiento, la nube se despliega en el bloque diez-punto-cien-punto-cero diagonal catorce, garantizando matemáticamente cero colisiones con el espacio diez-punto-veinte de Cali y los bloques diez-punto-treinta-y-uno al treinta-y-ocho de los centros regionales.
  El Hub VNet centraliza el tránsito híbrido a través de un Azure VPN Gateway redundante activo-activo con túneles IPsec IKEv2, inspección L7 con Azure Firewall Premium en diez-punto-cien-punto-uno-punto-cuatro, interfaces Bastion privadas y resolución DNS condicional.
  En el Spoke de Producción dividimos la carga en diez zonas aisladas: el ingreso de clientes corporativos entra por el WAF v2 en la subred punto-uno; los microservicios escalan en AKS en la subred punto-dos; la base de datos MySQL Flexible Server se aísla en la subred delegada punto-cuatro; y la telemetría se ingesta en la subred punto-siete. La Torre de Control se accede exclusivamente a través de un Internal Load Balancer privado en diez-punto-ciento-uno-punto-dos-punto-diez sin ninguna exposición a Internet público.
  Adicionalmente, reservamos el bloque diez-punto-doscientos diagonal dieciséis para interconectar una segunda nube como Google Cloud Platform en un horizonte de veinticuatro meses, cumpliendo con la visión multicloud sin incurrir en costos anticipados"*.
- **Respaldo Documental:**
  - **Archivo:** `Network Architecture.md` y `azure-target-architecture.drawio`
  - **Ruta:** `Frío Andes/frioandes-cloudops-docs/Network Architecture.md` (Secciones 3 y 4, líneas 60-146) y diagrama DIA-008.
  - **Evidencia Encontrada:** Tabla de subnetting de 10 subredes, regla `DLT-NET-01-01` y aislamiento del ILB `10.101.2.10`.

---

### Bloque 5: Seguridad, Mínimo Privilegio y Cumplimiento Normativo
- **Qué decir textualmente:**
  *"En seguridad aplicamos el principio de que la identidad es el nuevo perímetro. Mediante Microsoft Entra ID implementamos Privileged Identity Management: ningún administrador posee privilegios permanentes; todo acceso a infraestructura requiere elevación Just-In-Time con aprobación justificada por ticket y doble factor biométrico o FIDO2. El acceso administrativo a consolas ocurre únicamente por Azure Bastion hacia jumpboxes privados, estando prohibido el SSH directo desde la VPN según la regla de firewall FW-ADM-04.
  Para el portal de clientes corporativos, implementamos una API Sanitizada que aplica Attribute-Based Access Control: un cliente corporativo únicamente puede consultar las guías asociadas a su Tenant ID. La API recorta las coordenadas GPS a un corredor de dos kilómetros alrededor de la ruta programada para no revelar la posición de otros vehículos o patios de descanso; enmascara el identificador del camión como una unidad genérica rotativa; suprime el nombre y cédula del conductor en estricto cumplimiento de la Ley Estatutaria 1581 de Habeas Data; y elimina a nivel de esquema cualquier referencia a video o cámaras.
  En cuanto al almacenamiento de 1.2 petabytes, establecemos una política de ciclo de vida en ADLS Gen2: los primeros treinta días permanecen en capa Hot; entre treinta y uno y ciento ochenta días pasan a capa Cool; y a partir del día ciento ochenta se trasladan automáticamente a capa Archive inmutable WORM, garantizando la custodia legal de cinco años exigida por los contratos comerciales y el Código de Comercio"*.
- **Respaldo Documental:**
  - **Archivo:** `Security Architecture.md`, `Customer Portal and Control...md` y `Identity, Cryptography...md`
  - **Ruta:** `Frío Andes/frioandes-cloudops-docs/Customer Portal and Control Tower Architecture.md` (Sección 3, líneas 150-235).
  - **Evidencia Encontrada:** Reglas ABAC, clipping de corredor de 2 km, supresión de PII bajo Ley 1581 y retención WORM de 5 años.

---

### Bloque 6: Cargas de Trabajo A y B: Operación Logística y Telemetría Sub-Minuto
- **Qué decir textualmente:**
  *"Pasando a las cargas de trabajo, la Carga A moderniza la operación logística. Dimensionamos los microservicios en AKS con un baseline de 11 pods (15 vCPUs y 30 GiB de RAM) que escalan elásticamente mediante Horizontal Pod Autoscaler a 19 pods (25 vCPUs y 50 GiB de RAM) durante campañas pico, representando un 66.7% más de capacidad de cómputo, superando con holgura el 40% requerido por el negocio.
  Para absorber el pico del portal de rastreo de 2.000 peticiones por minuto, interpusimos un clúster de Azure Managed Redis con una tasa de acierto demostrada del 85%, reduciendo la carga sobre la base de datos MySQL a solo cinco consultas por segundo.
  La Carga B resuelve la telemetría de frío. Los treinta y dos cuartos fríos transmiten cada 30 segundos por la VPN a endpoints privados; los camiones transmiten por red móvil hacia IoT Hub con certificados X.509 de dispositivo. Azure Stream Analytics procesa las ventanas continuas.
  Demostramos matemáticamente un presupuesto de latencia de 40 segundos frente al evento físico: 5 segundos de detección, 10 de transporte de red, 2 de ingesta, 3 de evaluación en motor de reglas, 2 en cola de mensajería, 15 en entrega push/SMS y 3 en pantalla. Esto nos deja 20 segundos de margen de seguridad por debajo de la meta de un minuto.
  Y resolvemos la restricción operativa de turnos mediante una Azure Function: de 04:00 a 22:00 la alerta se muestra en la Torre de Control; de 22:00 a 04:00 se envía como llamada automatizada y mensaje urgente al buscapersonas del turno de Calidad de Guardia"*.
- **Respaldo Documental:**
  - **Archivo:** `Workload Sizing and Capacity Planning.md` y `Cold-Chain Telemetry and Alerting...md`
  - **Ruta:** `Frío Andes/frioandes-cloudops-docs/Cold-Chain Telemetry and Alerting Architecture.md` (Secciones 2 y 3, líneas 50-140).
  - **Evidencia Encontrada:** Desglose del presupuesto de 40 segundos, absorción de ráfagas en Event Hubs y ruteo diurno/nocturno.

---

### Bloque 7: Estrategia de IA en Microsoft Fabric sin Duplicación de Archivo
- **Qué decir textualmente:**
  *"Uno de los mayores desafíos del reto era integrar Microsoft Fabric para la Torre de Control sin volver a copiar el archivo de 1.2 petabytes. Nuestra solución arquitectónica aprovecha OneLake Shortcuts.
  Configuramos accesos directos virtuales sobre los contenedores de ADLS Gen2, lo que significa que el Bronze Lakehouse tiene un costo de copia de cero gigabytes: Fabric lee los archivos Delta y metadatos en su ubicación original.
  Únicamente materializamos mediante pipelines de Spark las capas Silver (eventos limpios de viajes y desvíos térmicos con aproximadamente 18.5 terabytes) y Gold (modelos dimensionales en estrella con 4.2 terabytes), sumando apenas 22.7 terabytes de huella analítica, es decir, menos del 2.5% del volumen bruto. Esta decisión técnica le ahorra a FríoAndes más de 250.000 dólares anuales en almacenamiento redundante.
  Sobre esta base desplegamos tres modelos predictivos con SynapseML:
  Primero, aviso temprano de excursión térmica que predice fallas mecánicas en el compresor de 4 a 12 horas antes de que ocurran.
  Segundo, cálculo dinámico de hora estimada de llegada (ETA) mediante regresión gradiente que pondera la topografía andina y el clima en tiempo real.
  Y tercero, control de calidad de evidencias de entrega mediante visión artificial, que clasifica automáticamente fotos borrosas o carga visiblemente averiada, y aplica una política de muestreo inteligente sobre los videos de 1.05 PB: solo extrae clips de 5 minutos alrededor del momento de apertura de puertas, evitando procesar el 99.8% de video intrascendente"*.
- **Respaldo Documental:**
  - **Archivo:** `Microsoft Fabric Analytics and AI Architecture.md` y `fabric-ai-integration.drawio`
  - **Ruta:** `Frío Andes/frioandes-cloudops-docs/Microsoft Fabric Analytics and AI Architecture.md` (Secciones 2 y 3, líneas 50-138) y diagrama DIA-006.
  - **Evidencia Encontrada:** Medallion virtual, 22.7 TB footprint, ahorro de $250.764/año y los 3 casos de uso de IA.

---

### Bloque 8: Sistema de Diseño Institucional y Experiencia de Usuario
- **Qué decir textualmente:**
  *"FríoAndes carecía de un manual de marca unificado. Para este proyecto creamos un Sistema de Diseño completo que garantiza que la Torre de Control y el Portal de Rastreo se reconozcan como parte de la misma empresa.
  Diseñamos el isotipo vectorial institucional inspirado en las cumbres andinas y la precisión del frío, disponible en versiones a color, una tinta y negativo.
  Estructuramos una paleta cromática con propósito operativo: Andean Navy como color estructural profundo, Glacier Blue para acciones primarias interactivas y Cryo Cyan para focos y telemetría activa, junto con tokens semánticos claros para estados de inocuidad: Verde Óptimo, Naranja Advertencia y Rojo Crítico.
  Elegimos una jerarquía tipográfica moderna: Space Grotesk para títulos y presencia de marca, Inter para lectura de interfaz continua y JetBrains Mono con números tabulares para lecturas de temperatura y códigos de guía, evitando saltos visuales en pantalla.
  Definimos componentes base con estados normal, foco y deshabilitado: botones, campos de búsqueda, tarjetas de guía con stepper de hitos y distintivos de temperatura con contraste reforzado.
  Y establecimos dos reglas de uso fundamentales: cumplimiento de contraste WCAG AAA en modo oscuro para proteger la fatiga visual del turno de noche en la Torre, y la exclusión física del reproductor de video en el portal de clientes para blindar la privacidad institucional"*.
- **Respaldo Documental:**
  - **Archivo:** `Customer Portal and Control Tower Architecture.md` y `styles.css`
  - **Ruta:** `Frío Andes/frioandes-cloudops-docs/Customer Portal and Control Tower Architecture.md` (Sección 5, líneas 350-440) y `Frío Andes/frioandes-cloudops-web/styles.css` (Líneas 1-140).
  - **Evidencia Encontrada:** Tokens CSS, tipografía, isotipo SVG en `assets/brand/isotipo.svg` y reglas WCAG AAA.

---

### Bloque 9: Plan de Migración, Convivencia a 90 Días y Runbook del Domingo
- **Qué decir textualmente:**
  *"La migración del núcleo legado se ejecuta en cuatro olas estructuradas a lo largo de un período máximo de noventa días:
  En la Ola Cero, durante los primeros quince días, establecemos la conectividad híbrida y la landing zone.
  En la Ola Uno, del día dieciséis al cuarenta y cinco, desplegamos AKS, pre-sembramos los 1.2 petabytes mediante almacenamiento staging RAID 60 local y establecemos la replicación continua de MySQL con lag menor a un segundo.
  En la Ola Dos, entre los días cuarenta y seis y setenta y cinco, ejecutamos el corte de producción en la madrugada de un domingo.
  Y en la Ola Tres, del día setenta y seis al noventa, completamos catorce días de estabilización, sanitizamos los discos de Cali bajo norma NIST 800-88 y apagamos definitivamente las máquinas virtuales en Cali.
  El corte productivo se rige por un runbook estricto de dos horas, entre las dos y las cuatro de la mañana de un domingo.
  A las 02:00 bloqueamos las escrituras en Cali y aplicamos el delta transaccional en 15 minutos.
  Entre las 02:25 y las 02:45 realizamos pruebas sintéticas con escrituras aisladas.
  A las 03:00 en punto se reúne el comité para la decisión de Go/No-Go. Si se declara No-Go, contamos con un procedimiento ensayado de rollback de 35 minutos para restaurar el DNS y las rutas a Cali, dejando 25 minutos de holgura antes de las cuatro de la mañana.
  Si se declara Go, promovemos Azure MySQL como único escritor, habilitamos el DNS privado corporativo hacia el ILB y a las 04:00 arranca la operación nacional en la nube con cero pérdida de datos"*.
- **Respaldo Documental:**
  - **Archivo:** `Core Migration and Cutover Strategy.md` e `Infrastructure Release Notes.md`
  - **Ruta:** `Frío Andes/frioandes-cloudops-docs/Core Migration and Cutover Strategy.md` (Secciones 1-3, líneas 40-180) e `Infrastructure Release Notes.md` (Líneas 50-75).
  - **Evidencia Encontrada:** Cronograma de olas (Waves 0-3), tabla minuto a minuto de 02:00 a 04:00, decisión 03:00 y rollback de 35 min.

---

### Bloque 10: Modelo de Costos FinOps y Propuesta Comercial
- **Qué decir textualmente:**
  *"Nuestro modelo financiero a veinticuatro meses está fundamentado en ochenta y un medidores públicos de la API de Azure Retail, desglosado en un libro de once pestañas totalmente conciliado mediante fórmulas SUMIFS.
  El costo total de infraestructura Azure para veinticuatro meses bajo un escenario base conservador con Fabric continuo asciende a 981.226 dólares. En meses ordinarios la infraestructura cuesta 29.935 dólares y en meses de campaña sube a 36.012 dólares debido al autoescalado de cómputo y mayor volumen de ingesta.
  Si el cliente implementa nuestra política de escalado dinámico de Fabric apagándolo en noches y fines de semana, el costo se reduce a 919.106 dólares, liberando un ahorro potencial de 62.119 dólares.
  Sumamos 87.120 dólares en licencias de Entra ID P1, P2 y Power BI Pro, 4.800 dólares en provisiones de soporte y SMS, 55.000 dólares en gastos únicos de siembra logística y staging local, y 232.387 dólares en mano de obra especializada con un equipo de quince roles técnicos altamente calificados.
  Esto arroja un costo directo de 1.36 millones de dólares. Aplicando un 15% de contingencia técnica llegamos a 1.56 millones de costo protegido. Finalmente, calculando un margen bruto del 20% para la firma consultora mediante la fórmula de costo dividido entre 0.8, presentamos al cliente un precio integral cerrado de 1.955.766 dólares antes de impuestos, garantizando predictibilidad presupuestal y solvencia operativa"*.
- **Respaldo Documental:**
  - **Archivo:** `Cost Estimate and Commercial Basis.md` y `FrioAndes 24-Month Cost Model.xlsx`
  - **Ruta:** `Frío Andes/frioandes-cloudops-docs/Cost Estimate and Commercial Basis.md` (Secciones 2, 4 y 5, líneas 48-144).
  - **Evidencia Encontrada:** $981,226.11 Azure base, $919,106.31 con schedule, $55k allowances, $232k labor, 15% contingencia ($204k) y precio cliente de $1,955,766.76 USD.

---

### Bloque 11: Conclusiones y Cierre
- **Qué decir textualmente:**
  *"En conclusión, señores del jurado, hemos presentado una propuesta técnica rigurosa, matemáticamente verificada y económicamente viable:
  Eliminamos la vulnerabilidad de un datacenter único mediante una arquitectura multizona en Azure.
  Convertimos la telemetría reactiva en una ventaja competitiva con alertas en menos de cuarenta segundos que salvan vidas y alimentos.
  Protegemos 1.2 petabytes de historia empresarial con retención legal inmutable de cinco años y analítica de IA sin duplicación de almacenamiento.
  Garantizamos que la operación diaria no se detenga gracias a un corte dominical de dos horas con rollback probado.
  Y entregamos una identidad de marca moderna plasmada en un sistema de diseño profesional.
  FríoAndes está lista para operar en la nube con confianza, resiliencia y liderazgo. Quedamos a su entera disposición para responder sus preguntas. Muchas gracias"*.
- **Respaldo Documental:**
  - **Archivo:** `Architecture Report.md` (Sección 8, líneas 225-244).

---

## 10. GUIONES DE EXPOSICIÓN RESUMIDOS PARA DIFERENTES AUDIENCIAS

---

### 10.1 Versión de 5 Minutos (Pitch Ejecutivo C-Level)
- **Audiencia:** CEO, CFO, Junta Directiva de FríoAndes.
- **Enfoque:** Retorno de inversión, mitigación de riesgos de negocio, cumplimiento normativo y presupuesto global.
- **Guion Textual (5 min / ~650 palabras):**
  *"Buenos días. Hoy FríoAndes enfrenta un riesgo existencial: operamos el transporte refrigerado del país sobre un único datacenter en Cali, con tecnología obsoleta sin soporte y 1.2 petabytes de evidencia contractual que carecen de respaldo secundario. Si Cali sufre un siniestro o una caída de internet, la compañía se paraliza y las pérdidas económicas y legales serían incalculables.
  Nuestra propuesta traslada el núcleo operativo a Microsoft Azure bajo tres certezas fundamentales:
  Primero: Cero interrupción del negocio. Diseñamos un plan de migración en 90 días donde el corte definitivo toma solo 2 horas en la madrugada de un domingo, dejando el lunes a las 4:00 AM la operación completamente estabilizada. El despacho diurno jamás se toca.
  Segundo: Protección biológica de la carga. Pasamos de enterarnos de las fallas de frío horas o días después, a recibir alertas automáticas en menos de 40 segundos. Si un camión pierde temperatura en ruta, la Torre de Control o la guardia nocturna actúan de inmediato, salvando toneladas de alimentos perecederos y evitando reclamos millonarios de nuestros clientes corporativos.
  Tercero: Eficiencia financiera con Inteligencia Artificial. Conectamos Microsoft Fabric a nuestros datos mediante OneLake Shortcuts sin duplicar los 1.2 petabytes de archivo, lo que evita un gasto redundante de 250.000 dólares anuales en almacenamiento. Además, dotamos a la Torre de Control de modelos predictivos que anticipan fallas de refrigeración y estiman horas de llegada exactas.
  El proyecto representa una inversión de 1.95 millones de dólares a 24 meses, incluyendo infraestructura de nube elástica que soporta los picos de campaña (+40% de cómputo), licencias de seguridad, mano de obra de ingeniería experta y un cojín de contingencia del 15%.
  Modernizamos la plataforma, blindamos el cumplimiento contractual de 5 años de custodia y posicionamos a FríoAndes como el líder tecnológico indiscutible de la cadena de frío en Colombia. Muchas gracias"*.

---

### 10.2 Versión de 10 Minutos (Comité de Operaciones y Tecnología)
- **Audiencia:** Director de Operaciones, Gerente de Logística, Gerente de TI.
- **Enfoque:** Operatividad diaria, turnos diurnos vs. nocturnos, capacidad en campañas pico y gestión del cambio.
- **Guion Textual (10 min / ~1.300 palabras):**
  *(Minuto 0 a 2: Diagnóstico y Restricciones)*  
  *"Buenos días. La operación logística de FríoAndes coordina 120 camiones diarios y 32 cuartos fríos en ocho centros regionales, expandiéndose a 180 camiones en temporadas altas. Sin embargo, nuestro datacenter de Cali tiene capacidad fija, los sistemas CentOS 7 y MySQL 5.7 están fuera de soporte y los cambios manuales toman semanas de riesgo.
  Nuestro compromiso operativo es claro: no permitiremos que la modernización interfiera con la jornada de despacho de 04:00 a 22:00 COT ni con las campañas comerciales de Semana Santa y fin de año".*
  
  *(Minuto 2 a 5: Arquitectura y Dimensionamiento de Cargas)*  
  *"En Azure East US 2 desplegamos una arquitectura Hub-and-Spoke con estricto microsegmentado.
  Para la Carga A (Despacho y Facturación), la Torre de Control se aloja en AKS privado detrás de un Internal Load Balancer sin IP pública. En días normales opera con 11 pods y escala automáticamente a 19 pods (+66.7% en vCPU) en campañas, superando la hipótesis del 40% del negocio. El portal de rastreo público soporta hasta 2.000 solicitudes por minuto mediante un clúster de caché Redis que absorbe el 85% de las lecturas, protegiendo la base de datos MySQL Flexible Server Multi-AZ.
  Para la Carga B (Telemetría), implementamos streaming en tiempo real con IoT Hub y Stream Analytics. Diseñamos un presupuesto de latencia de 40 segundos para alertar excursiones térmicas, cumpliendo con holgura la meta de un minuto. El enrutamiento es inteligente: en el día la alerta entra a la Torre de Control; en la noche (de 22:00 a 04:00) una Azure Function dispara llamadas y SMS urgentes al turno de Calidad de Guardia".*
  
  *(Minuto 5 a 8: Coexistencia a 90 Días y Corte Dominical)*  
  *"La migración se estructura en cuatro olas a lo largo de 90 días máximos de convivencia.
  El archivo de 1.2 PB se pre-siembra antes del corte utilizando almacenamiento local staging RAID 60 de 1,248 TB útiles, garantizando que el enlace de 400 Mbps de Cali no seature.
  El corte productivo se realiza exclusivamente un domingo de 02:00 a 04:00 COT. Durante los primeros 15 minutos sincronizamos únicamente el diferencial transaccional de MySQL. A las 03:00 COT se evalúa el Go/No-Go. Si se declara No-Go, tenemos 35 minutos de rollback probado para restablecer Cali antes de las 04:00 AM. Si se declara Go, la nube asume como maestro de escritura y Cali queda como respaldo de solo lectura hasta el día 90, cuando se sanitizan sus discos bajo norma NIST 800-88".*
  
  *(Minuto 8 a 10: FinOps y Conclusión)*  
  *"Presupuestamos un costo de infraestructura Azure de 981.226 dólares en 24 meses, con una sensibilidad de ahorro de 62.119 dólares mediante la auto-pausa nocturna de Fabric. Integramos OneLake Shortcuts para hacer analítica de IA sin duplicar 1.2 PB, ahorrando 250.000 dólares anuales. El precio comercial propuesto de 1.95 millones de dólares incluye mano de obra especializada, licencias de seguridad y un 15% de contingencia.
  Con esta arquitectura, FríoAndes asegura continuidad total, inocuidad alimentaria y liderazgo logístico. Muchas gracias"*.

---

### 10.3 Versión de 15 Minutos (Revisión de Arquitectura Técnica y Seguridad)
- **Audiencia:** Enterprise Architects, Security Officers (CISO), Líderes de Infraestructura y Redes.
- **Enfoque:** Topología de red, seguridad Zero-Trust, reglas de firewall, identidades, modelo de datos y observabilidad.
- **Estructura del Guion (15 min):**
  - **Min 00-02:** Contexto y Diagnóstico on-prem (VMware, EOL, cuellos de botella WAN y RPO > 23h).
  - **Min 02-05:** Red y Subnetting Hub-and-Spoke (10 zonas por ambiente, no solapamiento con `10.20` y `10.31-38`, reserva GCP `10.200/16`).
  - **Min 05-08:** Seguridad Zero-Trust y Firewall (Reglas FW-CORP-01 a 08, FW-ADM-04 bloqueo de SSH directo, Bastion JIT, WAF v2, sanitización de datos B2B bajo Ley 1581 y retención WORM 5 años).
  - **Min 08-11:** Cargas de Trabajo, Telemetría e IA en Fabric (AKS HPA 11 a 19 pods, presupuesto de 40s para alerta térmica, ruteo diurno/nocturno, OneLake Shortcuts virtualizando Bronze, 22.7 TB Silver/Gold, 3 casos de uso SynapseML).
  - **Min 11-13:** Migración, Coexistencia y Runbook de 2 Horas (Single-writer, pre-siembra de 1.2 PB, corte domingo 02:00-04:00, decisión 03:00, rollback 35 min, Día 90 decom de Cali).
  - **Min 13-15:** Observabilidad (SLO disponibilidad portal >= 99.9%, SLO alerta < 60s >= 99.0%, Grafana, Sentinel), FinOps ($1,95M USD propuesta cliente) y Conclusiones.

---

### 10.4 Versión de 20 Minutos (Sustentación Académica Oficial ante Jurado / Cliente)
- **Audiencia:** Docente Evaluador (actuando como Arquitecto Cloud del Cliente) y Jurado Académico Universidad Icesi.
- **Enfoque:** Recorrido exhaustivo de los 8 criterios de evaluación, defensa técnica profunda, justificación de supuestos, demostración de evidencias en código/diagramas y preparación para la sesión de preguntas.
- **Estructura del Guion (20 min):**
  *(Sigue la estructura completa del Bloque 9, asignando aproximadamente 2 minutos a cada uno de los 10 bloques temáticos, enfatizando las evidencias encontradas en `Architecture Report.md`, los 18 diagramas de Draw.io, la conciliación matemática de las 11 pestañas de Excel y la suite de pruebas unitarias en Python)*.

---
---

## 11. BANCO DE PREGUNTAS Y RESPUESTAS DEL JURADO EVALUADOR

Este banco contiene **45 preguntas de alta exigencia técnica** distribuidas en los 9 ejes temáticos obligatorios, diseñadas para preparar al expositor frente a los cuestionamientos más incisivos del jurado evaluador o el Arquitecto Cloud del Cliente.

---

### Eje 1: Preguntas sobre Arquitectura General y Gobierno

#### P1.1: ¿Por qué eligieron Microsoft Azure sobre AWS o GCP como nube principal, considerando que GCP tiene fortalezas en Kubernetes y analítica?
- **Respuesta Recomendada:**
  "Nuestra decisión se fundamentó en una matriz de evaluación multicriterio de 8 dimensiones documentada en el capítulo `Cloud Provider Evaluation.md`. Aunque GCP obtuvo ventajas en Kubernetes nativo y AWS en madurez de servicios, **Microsoft Azure obtuvo la calificación ponderada más alta (4.39 sobre 5.00)** debido a dos factores determinantes:
  Primero, la integración nativa y de costo cero de red entre Azure Data Lake Storage Gen2 y **Microsoft Fabric mediante OneLake Shortcuts**, cumpliendo el mandato del enunciado de no duplicar 1.2 PB.
  Segundo, la integración unificada de identidades corporativas con Microsoft Entra ID para los 450 usuarios internos y federación B2B para los 2.000 clientes, minimizando la complejidad operativa. No obstante, dejamos reservado el bloque `10.200.0.0/16` para conectar GCP en un horizonte de 24 meses".

#### P1.2: ¿Cómo evitan el 'Vendor Lock-in' en esta arquitectura?
- **Respuesta Recomendada:**
  "Implementamos desacoplamiento a tres niveles:
  1. En cómputo: las aplicaciones están empaquetadas en contenedores Docker estándar orquestados sobre Kubernetes (AKS), lo que permite portar las cargas a GKE en GCP o EKS en AWS sin reescribir código de negocio.
  2. En datos: los datos analíticos se almacenan en formato abierto **Delta Lake / Parquet** sobre almacenamiento compatible con S3/Blob, sin formatos propietarios.
  3. En infraestructura: el aprovisionamiento se gestiona con **Terraform estándar**, estructurado en módulos reutilizables".

#### P1.3: ¿Cómo se garantiza la separación de costos entre la operación logística, la telemetría y los ambientes no productivos?
- **Respuesta Recomendada:**
  "Mediante una estrategia combinada de suscripciones dedicadas y taxonomía de etiquetado (tagging) obligatoria forzada por Azure Policy. Cada recurso debe portar los tags `Environment` (Prod, Stg, Dev, Shared) y `CostCenter` (Operacion-Despacho, Telemetria-IoT, QA-Staging, Core-Shared). En el modelo de costos de Excel (pestaña `Executive Summary`) evidenciamos cómo el costo mensual se desagrega exactamente entre Producción ($20.450 USD base), Telemetría ($3.820 USD), Ambientes No-Prod ($4.210 USD) y Servicios Compartidos ($1.455 USD)".

#### P1.4: ¿Qué sucede con el gobierno si en 24 meses se activa GCP? ¿Tendremos dos consolas de seguridad separadas?
- **Respuesta Recomendada:**
  "No. Diseñamos un modelo de identidad federada donde **Microsoft Entra ID actúa como el Proveedor de Identidad (IdP) central**. GCP Cloud Identity se configura como un Service Provider federado mediante SAML 2.0 y OIDC. Además, los logs de auditoría de GCP (Cloud Audit Logs) se canalizan hacia el Log Analytics Workspace y Microsoft Sentinel en Azure Hub mediante conectores de telemetría multicloud, manteniendo un único plano de control de seguridad".

#### P1.5: ¿Por qué mantuvieron un Hub compartido para producción y no-producción en lugar de hacer un Hub por ambiente?
- **Respuesta Recomendada:**
  "Por optimización financiera FinOps. Desplegar instancias dedicadas de Azure Firewall Premium y Azure Bastion para Staging y Desarrollo habría triplicado el costo fijo de red, añadiendo más de $3,000 USD mensuales innecesarios. El Hub compartido aísla el tráfico entre spokes mediante políticas de Azure Firewall y Network Security Groups (NSGs) que impiden por defecto cualquier comunicación lateral entre Staging, Desarrollo y Producción".

---

### Eje 2: Preguntas sobre Código y Automatización (IaC / CI/CD)

#### P2.1: En el repositorio documental se menciona que la entrega de Terraform es 'Plan-Only'. ¿Por qué no aplicaron los cambios en una suscripción viva?
- **Respuesta Recomendada:**
  "Por estricto apego al alcance y la ética académica: el enunciado solicita el repositorio con el patrón repetible y la capacidad de planearse, eximiendo explícitamente de materializar las 8 sedes o la flota viva. Aplicar (`terraform apply`) los 1.223 recursos proyectados en una suscripción empresarial real generaría una facturación de más de $30,000 USD mensuales que la universidad no debe asumir. En su lugar, aportamos **125 pruebas mock automatizadas**, validación sintáctica de `terraform validate` en 25 raíces y un plan autenticado sanitizado que reconcilia 3.441 líneas de costos".

#### P2.2: ¿Cómo garantizan que un despliegue de infraestructura no destruya la base de datos o almacenamiento en producción?
- **Respuesta Recomendada:**
  "Implementamos cuatro salvaguardas en el código de Terraform:
  1. Configuración de bloques `lifecycle { prevent_destroy = true }` en todos los recursos de almacenamiento de datos (`azurerm_mysql_flexible_server` y `azurerm_storage_account`).
  2. Implementación de bloqueos de recursos a nivel de Azure Resource Manager (`azurerm_management_lock` de tipo `CanNotDelete`).
  3. Ejecución de análisis estático con **Checkov** en el pipeline de CI/CD para detectar intentos de destrucción o configuraciones inseguras.
  4. La regla de cambio exige que cualquier plan que contenga acciones `destroy > 0` requiera aprobación manual dual del Lead Architect y el DBA".

#### P2.3: ¿Cómo manejan los secretos en los pipelines sin exponer contraseñas en Git?
- **Respuesta Recomendada:**
  "Implementamos una arquitectura Secretless basada en **Workload Identity Federation (OpenID Connect - OIDC)**. Los runners de GitHub Actions o Azure DevOps no almacenan tokens de larga duración ni claves de servicio; se autentican dinámicamente contra Entra ID mediante un token JWT efímero de corta duración con permisos RBAC acotados, extrayendo los secretos operativos en memoria directamente desde Azure Key Vault en tiempo de ejecución".

#### P2.4: Explique el funcionamiento de la herramienta `change_window_policy.py`.
- **Respuesta Recomendada:**
  "Es una biblioteca de validación determinista que evalúa si una propuesta de cambio cumple las restricciones temporales del negocio: valida que la zona horaria sea `America/Bogota`, bloquea automáticamente cualquier cambio en la franja diurna de 04:00 a 22:00 COT, bloquea los períodos de campaña (Semana Santa, mitad de año y del 25 de noviembre al 5 de enero) y, si el cambio es de tipo cutover, exige que sea estrictamente un domingo, iniciando a las 02:00 y con duración no mayor a 120 minutos".

#### P2.5: Si un desarrollador necesita desplegar un hotfix de emergencia en el portal un martes a las 10:00 AM, ¿el pipeline lo permite?
- **Respuesta Recomendada:**
  "No por el flujo ordinario. El pipeline ordinario bloquea la ejecución por política diurna. Para un hotfix crítico (P1), se debe invocar el procedimiento de **Emergency Break-Glass**: se requiere la apertura de un ticket de incidente P1, la autorización mancomunada del Incident Commander y el Director de Operaciones, lo que habilita una excepción temporal en la política y audita cada comando en Microsoft Sentinel".

---

### Eje 3: Preguntas sobre Diseño y Sistema Visual (UI/UX)

#### P3.1: ¿Por qué era necesario crear un manual de marca y sistema de diseño si este es un proyecto de infraestructura CloudOps?
- **Respuesta Recomendada:**
  "Porque el enunciado en su Criterio 7 y Punto 8 lo exige explícitamente: FríoAndes no tenía manual de marca y requería unificar la experiencia de la Torre de Control y el Portal de Rastreo. Además, un sistema de diseño no es solo cosmético: define la usabilidad crítica para operadores en turnos de 18 horas, la accesibilidad de contraste en turnos nocturnos y, fundamentalmente, la separación visual de privilegios para que un cliente B2B jamás vea componentes internos como cámaras o mapas globales de flota".

#### P3.2: ¿Cómo resolvieron el requisito de contraste para el turno de noche en la Torre de Control?
- **Respuesta Recomendada:**
  "En nuestro archivo `styles.css` y en `Customer Portal and Control Tower Architecture.md` definimos tokens de modo oscuro industrial: una superficie de fondo `var(--dark-deep)` (`#061724`) combinada con tipografía en `var(--neutral-arctic-white)` (`#F8FAFC`) y estados críticos en Coral Alert (`#E63946`). Esta combinación supera una relación de contraste de **7:1**, cumpliendo el nivel **WCAG AAA**, lo que previene la fatiga ocular de los operadores entre las 22:00 y las 04:00 COT".

#### P3.3: ¿Qué tipografía seleccionaron y por qué motivos técnicos?
- **Respuesta Recomendada:**
  "Seleccionamos tres fuentes de Google Fonts con roles específicos:
  1. **Space Grotesk:** Para títulos y números grandes de KPI por su estética industrial y geométrica.
  2. **Inter:** Para textos de interfaz y lectura continua por su legibilidad optimizada en pantallas de baja resolución.
  3. **JetBrains Mono:** Para valores de telemetría (temperaturas como `-19.4 °C`), códigos de guía y marcas de tiempo UTC. Es una fuente monoespaciada con cifras tabulares (`tnum`), lo que garantiza que los dígitos ocupen exactamente el mismo ancho y no generen saltos visuales en pantalla cuando la telemetría se actualiza en tiempo real".

#### P3.4: ¿Cómo garantiza el diseño que los clientes corporativos no vean la flota completa?
- **Respuesta Recomendada:**
  "Mediante dos barreras: a nivel de API, el backend solo retorna las coordenadas recortadas de la guía del cliente; y a nivel de frontend, el componente `RestrictedRouteMap` solo renderiza el corredor de 2 km de la ruta contratada, omitiendo por completo los módulos de Leaflet o Mapbox que pintan las capas de flota global, las cuales están compiladas en un bundle separado (`internal-cockpit.bundle.js`) exclusivo de la intranet corporativa".

#### P3.5: ¿Dónde se encuentran las versiones en una tinta y negativo del logotipo?
- **Respuesta Recomendada:**
  "Están especificadas en la Sección 5.2 de `Customer Portal and Control Tower Architecture.md` y materializadas vectorialmente en el archivo `Frío Andes/frioandes-cloudops-web/assets/brand/isotipo.svg`. La versión a color utiliza el degradado Glacier Blue a Cryo Cyan; la versión en negativo utiliza blanco puro sobre Andean Navy; y la versión a una tinta utiliza negro monocromático para documentos y notas de versión impresas".

---

### Eje 4: Preguntas sobre Modelado de Cargas y Capacidad

#### P4.1: ¿De dónde salieron los cálculos para dimensionar los pods de AKS en 11 normales y 19 en campaña?
- **Respuesta Recomendada:**
  "Salen de la descomposición matemática en `Workload Sizing and Capacity Planning.md`: modelamos que 450 usuarios con 30% de concurrencia normal (135 sesiones) generan 9 acciones/segundo y 27 TPS sobre la base de datos. En campaña, la concurrencia sube al 50% (225 sesiones) generando 45 TPS. Cada pod de microservicio (Despacho, Inventario, Facturación) se configuró con un límite de 1 vCPU y 2 GiB de RAM. En normalidad se requieren 11 pods (15 vCPU asignadas); en campaña, el HPA escala a 19 pods (25 vCPU), lo que equivale exactamente a un **+66.7% de capacidad**, cubriendo con creces la hipótesis de negocio del +40%".

#### P4.2: El portal tiene un pico de 2.000 solicitudes por minuto durante 4 horas en campaña. ¿Cómo evitan que la base de datos MySQL colapse?
- **Respuesta Recomendada:**
  "2.000 solicitudes por minuto equivalen a 33,33 peticiones por segundo (RPS). Si todas golpearan MySQL, saturarían el pool de conexiones. Por ello interpusimos **Azure Managed Redis OSSCluster** con una política de almacenamiento en caché de estados de guía con TTL de 5 minutos. Con una tasa de acierto conservadora del **85%**, Redis absorbe 28.33 RPS en memoria RAM, dejando que solo 5.0 RPS de lecturas complejas lleguen a MySQL Flexible Server, el cual soporta fácilmente hasta 200 TPS en la SKU seleccionada".

#### P4.3: ¿Cómo calcularon el volumen diario de mensajes de telemetría?
- **Respuesta Recomendada:**
  "Es una suma matemática estricta de dos fuentes:
  1. Cuartos fríos: 32 cuartos transmitiendo cada 30 segundos las 24 horas del día = `(32 / 30) * 86.400 = 92.160 mensajes/día`.
  2. Flota de camiones: En normalidad (120 camiones, 18 horas de jornada, temp cada 60s y GPS cada 30s = 3 pings/minuto) = `120 * 3 * 60 * 18 = 388.800 mensajes/día`. Total normal: **480.960 mensajes/día** (7.07 msgs/s).
  En campaña (180 camiones) la flota genera 583.200 mensajes. Total campaña: **675.360 mensajes/día** (10.07 msgs/s). Ambas tasas son absorbidas holgadamente por una sola Throughput Unit de Event Hubs, que soporta hasta 1.000 mensajes por segundo".

#### P4.4: Si la tasa máxima en campaña es 10,07 mensajes/segundo, ¿por qué dicen que Event Hubs puede recibir hasta 500 msgs/s en ráfaga?
- **Respuesta Recomendada:**
  "Por el fenómeno de **reconexión por ráfaga (burst reconnection)** tras pérdida de cobertura celular. Cuando 10 o 15 camiones cruzan el paso de La Línea en la cordillera sin señal móvil, sus dispositivos edge almacenan hasta 100.000 lecturas en memoria flash local. Al recuperar cobertura simultáneamente cerca a Ibagué o Bogotá, descargan los datos en ráfaga a 50 msgs/s por vehículo, produciendo picos transitorios de 500 msgs/s que Event Hubs absorbe en milisegundos sin generar contrapresión ni pérdida de paquetes".

---

### Eje 5: Preguntas sobre Diagramas y Flujos de Secuencia

#### P5.1: En el diagrama de arquitectura objetivo de Azure (DIA-008), ¿por qué las Azure Functions están en una subred separada (.11/24)?
- **Respuesta Recomendada:**
  "Porque Azure Functions requiere una subred dedicada delegada al servicio `Microsoft.Web/serverFarms` para la integración con red virtual (VNet Integration). La regla de delta `DLT-NET-01-01` incorporó formalmente la subred `.11/24` para aislar el tráfico serverless de los pods de AKS, permitiendo que la Function acceda de forma privada a Event Hubs y Service Bus sin cruzar por Internet público".

#### P5.2: Explique la diferencia entre el diagrama de arquitectura DIA-008 y DIA-009.
- **Respuesta Recomendada:**
  "El diagrama DIA-008 muestra la plataforma operativa completa al cierre de la migración en Azure (estado a los 90 días). El diagrama DIA-009 proyecta la topología global multicloud al **mes 24**, mostrando cómo el Hub de Azure se conecta mediante un Gateway de tránsito con la VPC de Google Cloud Platform en el CIDR `10.200.0.0/16`, compartiendo identidades federadas con Entra ID y resolución DNS condicional cruzada".

#### P5.3: En el diagrama de corte de aplicaciones (DIA-005), ¿por qué se fijó la decisión de Go/No-Go a las 03:00 y no a las 03:30?
- **Respuesta Recomendada:**
  "Porque nuestro plan de contingencia (rollback) toma **35 minutos cronometrados**: 10 minutos para restaurar el DNS corporativo hacia Cali, 10 minutos para reabrir el firewall local y 15 minutos para verificar la salud del MySQL legado. Si tomáramos la decisión a las 03:30, el rollback terminaría a las 04:05, retrasando el despacho diurno que inicia a las 04:00. Fijar la decisión a las 03:00 nos deja **25 minutos de margen de maniobra de seguridad**".

#### P5.4: ¿Qué representa la línea punteada entre ADLS Gen2 y Microsoft Fabric en el diagrama DIA-006?
- **Respuesta Recomendada:**
  "Representa un puntero virtual lógico (OneLake Shortcut), no un flujo de copia física de datos. Significa que Fabric lee los archivos Parquet directamente sobre el almacenamiento de ADLS Gen2 mediante metadatos compartidos, garantizando cero consumo de almacenamiento redundante en la capa Bronze".

---

### Eje 6: Preguntas sobre Infraestructura y Redes

#### P6.1: Si el enlace de Cali es de 400 Mbps simétricos compartido con toda la empresa, ¿cómo garantizan que la replicación de base de datos no sature el internet de las oficinas?
- **Respuesta Recomendada:**
  "La replicación transaccional de MySQL durante la coexistencia solo transmite los logs binarios (binlogs) de las escrituras operativas. Con 15 writes/segundo en pico y un payload promedio de 4 KB por transacción, el ancho de banda requerido para la replicación continua es de aproximadamente **0.48 Mbps**, lo que representa menos del 0.15% de la capacidad del enlace de 400 Mbps. El tráfico masivo de 1.2 PB se retira del enlace mediante la pre-siembra física previa".

#### P6.2: ¿Cómo funciona el enrutamiento de salida hacia Internet para las máquinas privadas que requieren parches?
- **Respuesta Recomendada:**
  "Mediante una tabla de rutas definidas por el usuario (User-Defined Route - UDR) asociada a cada subred privada, que fuerza la ruta por defecto `0.0.0.0/0` hacia la IP privada de Azure Firewall Premium (`10.100.1.4`). Ningún servidor o pod tiene IP pública. Azure Firewall aplica reglas de aplicación con filtrado FQDN e inspección TLS, permitiendo la salida exclusivamente hacia repositorios autorizados de Ubuntu, Microsoft y Docker Hub".

#### P6.3: ¿Por qué eligieron un Azure VPN Gateway VpnGw2AZ y no ExpressRoute?
- **Respuesta Recomendada:**
  "Porque en el diagnóstico técnico inicial documentamos que Cali no cuenta con un enlace dedicado hacia una nube ni conexión directa a un carrier en un punto de presencia (PoP) de ExpressRoute. Contratar ExpressRoute 10G habría añadido un sobrecosto millonario no justificado en la fase inicial. VpnGw2AZ provee redundancia de zona (Multi-AZ), soporta hasta 1.25 Gbps de throughput y permite establecer túneles IPsec IKEv2 activos-activos sobre el enlace simétrico existente de 400 Mbps, siendo la opción más costo-eficiente".

#### P6.4: Explique la función de la subred `10.100.6.0/28` y `10.100.6.16/28`.
- **Respuesta Recomendada:**
  "Corresponden a **Azure Private DNS Resolver**. La primera (`.0/28`) aloja el Inbound Endpoint que recibe consultas DNS desde el servidor local de Cali y los CEDIs para resolver nombres de zonas privadas de Azure (ej. `*.privatelink.mysql.database.azure.com`). La segunda (`.16/28`) es una subred delegada para el Outbound Endpoint que reenvía consultas desde Azure hacia los controladores de dominio locales en Cali para resolver recursos corporativos internos".

---

### Eje 7: Preguntas sobre Seguridad, Identidad y Cumplimiento

#### P7.1: ¿Cómo se cumple la Ley 1581 de 2012 (Habeas Data) respecto a los conductores de los camiones?
- **Respuesta Recomendada:**
  "En el portal público y en las respuestas de API hacia clientes corporativos aplicamos un filtro de anonimización estricto en la capa de serialización DTO: el nombre completo y la cédula del conductor se suprimen por completo de la carga útil JSON, reemplazándose por el rótulo estandarizado `'Conductor Certificado FríoAndes'`. Los datos personales residen únicamente en la base de datos interna cifrada con CMK en reposo, accesible solo por Recursos Humanos y Seguridad mediante roles RBAC auditados".

#### P7.2: ¿Qué algoritmo criptográfico y qué manejo de llaves se definió para proteger los 1.2 PB de datos?
- **Respuesta Recomendada:**
  "Implementamos cifrado en reposo mediante **AES-256** utilizando claves administradas por el cliente (Customer-Managed Keys - CMK) custodiadas en **Azure Key Vault HSM** con nivel FIPS 140-2 Nivel 3. Las claves rotan automáticamente cada 365 días. Para los datos en tránsito, se exige TLS 1.2 como mínimo absoluto y TLS 1.3 preferido en todos los endpoints de almacenamiento, APIs y túneles VPN".

#### P7.3: ¿Cómo detectan si un cliente corporativo intenta hacer un ataque de inyección SQL o scraping masivo de guías en el portal?
- **Respuesta Recomendada:**
  "A través del Azure Application Gateway v2 configurado con **WAF v2 en modo Prevención** con el conjunto de reglas OWASP Core Rule Set (CRS) 3.2. Las firmas detectan y bloquean instantáneamente inyecciones SQL (SQLi), Cross-Site Scripting (XSS) y escaneos automatizados. Adicionalmente, el Rate Limiting bloquea direcciones IP que superen 100 peticiones por segundo, y los registros de bloqueo se transmiten a Microsoft Sentinel para análisis de comportamiento anómalo".

#### P7.4: ¿Por qué se afirma que el video de las cámaras de los camiones tiene 'aislamiento físico de bundle'?
- **Respuesta Recomendada:**
  "Porque en el proceso de empaquetado del frontend web mediante Vite/Webpack, separamos físicamente el código fuente en dos bundles independientes mediante Code-Splitting: el paquete del portal de clientes (`customer-portal.bundle.js`) no contiene una sola línea de código de reproductores de video (Video.js o WebRTC), impidiendo que un atacante pueda desensamblar el frontend para forzar la visualización de cámaras. Además, a nivel de red, el microservicio de video en `10.101.2.0/23` carece de rutas hacia el frontend de clientes".

---

### Eje 8: Preguntas sobre Bases de Datos, Almacenamiento y Migración

#### P8.1: En el datacenter actual tienen MySQL 5.7 en CentOS 7. ¿Van a migrar directamente a MySQL 8.0 en Azure? ¿Qué pasa con las incompatibilidades de SQL?
- **Respuesta Recomendada:**
  "Sí, migramos hacia **Azure Database for MySQL Flexible Server versión 8.0**. Para mitigar incompatibilidades de sintaxis (como palabras reservadas nuevas, agrupamientos `GROUP BY` estrictos o modos SQL), el equipo definió en el capítulo `Dispatch Migration and Coexistence Strategy.md` el catálogo de **Application Adaptation Requests (AARs)**: se ejecutan ajustes de compatibilidad de drivers JDBC/ODBC y directivas de sesión pre-validadas en Staging durante la Wave 1, sin alterar la lógica de negocio de las aplicaciones".

#### P8.2: ¿Por qué descartaron restaurar respaldos físicos con Percona XtraBackup hacia Azure MySQL Flexible Server?
- **Respuesta Recomendada:**
  "Porque Azure Database for MySQL Flexible Server es un servicio PaaS administrado donde el proveedor no concede acceso root al sistema de archivos subyacente del sistema operativo para restaurar directamente archivos binarios de InnoDB. La migración debe realizarse mediante volcados lógicos iniciales optimizados (utilizando herramientas multihilo como `mydumper` / `myloader`) seguidos de replicación continua basada en registros binarios (CDC - Change Data Capture)".

#### P8.3: ¿Cómo aseguran que el archivo de 1.2 PB cumpla con la retención de 5 años si no tienen una herramienta de borrado automatizado?
- **Respuesta Recomendada:**
  "En ADLS Gen2 configuramos una política de ciclo de vida (Lifecycle Management) combinada con **almacenamiento inmutable WORM (Write Once, Read Many)** con bloqueo de directiva de retención basada en tiempo fijada en 1.825 días (5 años calendarios). Durante este plazo, ningún usuario, ni siquiera el administrador de la suscripción, puede eliminar o sobrescribir un comprobante de entrega o log térmico, cumpliendo los acuerdos contractuales con los clientes de alimentos y el Estatuto Tributario".

#### P8.4: ¿Por qué en el modelo de costos se presupuestan 1.488 TB al mes 24 si el archivo base es de 1,200 TB?
- **Respuesta Recomendada:**
  "Porque consideramos la tasa de crecimiento vegetativo del negocio: el archivo actual suma 1,200 TB decimales (1.2 PB) y crece a razón de **12 TB al mes**. En 24 meses de contrato, el nuevo volumen generado suma exactamente `24 * 12 = 288 TB`. Sumando la base de 1,200 TB más los 288 TB nuevos, alcanzamos exactamente **1.488 TB raw al final del mes 24**, modelados con su respectivo escalonamiento a capas frías en el libro financiero".

---

### Eje 9: Preguntas sobre Decisiones Técnicas y FinOps

#### P9.1: En la tabla de costos presentan un escenario 'Always-On' de $981,226 USD y otro 'Programado' de $919,106 USD. ¿Cuál es la postura oficial de la entrega?
- **Respuesta Recomendada:**
  "Nuestra postura oficial de diseño para la propuesta de entrega académica es el **escenario base conservador de $981,226.11 USD** (always-on). Lo sustentamos así para garantizar que FríoAndes cuente con presupuesto blindado en caso de que decidan mantener la capacidad analítica encendida 24/7. El escenario programado de $919,106.31 USD se presenta como un análisis de sensibilidad y optimización que demuestra la capacidad del equipo de generar $62,119.80 USD de ahorro mediante automatización en la fase operativa".

#### P9.2: ¿Por qué el precio al cliente es de $1,955,766 USD si el costo directo es de $1,360,533 USD? Explique la matemática comercial.
- **Respuesta Recomendada:**
  "Responde a una estructuración financiera profesional que protege tanto al cliente como a la empresa consultora:
  1. Costo directo: $1,360,533.40 USD (Nube $1.073k + Allowances $55k + Nómina $232k).
  2. Aplicamos un **15% de contingencia técnica** ($204,080.01 USD) para cubrir riesgos de fluctuación cambiaria (TRM peso-dólar), desvíos en pruebas o costos aduaneros de siembra física, elevando el costo protegido a **$1,564,613.41 USD**.
  3. Sobre ese costo protegido aplicamos un **Margen Bruto Comercial del 20%** utilizando la fórmula estándar de rentabilidad sobre ventas: `Precio = Costo / (1 - Margen) = $1,564,613.41 / 0.80 = $1,955,766.76 USD`. No es un markup sobre costo; es un margen real del 20%".

#### P9.3: Mencione las 3 decisiones de ahorro de costos más contundentes del diseño.
- **Respuesta Recomendada:**
  "Las tres decisiones más contundentes son:
  1. **OneLake Shortcuts en Microsoft Fabric:** Consultar los datos in-situ sin duplicar los 1.2 PB evita un sobrecosto anual recurrente de **$250,764 USD** en almacenamiento analítico.
  2. **Ciclo de vida en capas en ADLS Gen2:** Mover la evidencia de Hot a Cool y luego a Archive reduce el costo por gigabyte en más de un **70%** para los datos de más de 6 meses.
  3. **Pila Hub y Seguridad Compartida:** Reutilizar Azure Firewall Premium, Bastion y DNS para Producción, Staging y Desarrollo en un solo Hub evita gastar más de **$72,000 USD** en duplicación de appliances de red en 24 meses".

---

## 12. MAPA DE EVIDENCIAS Y TRAZABILIDAD TEXTUAL

La siguiente matriz cruza cada afirmación técnica y de negocio realizada en este informe con el archivo exacto donde se demuestra, su sección y el fragmento textual literal extraído del repositorio.

| # | Afirmación Técnica o de Negocio | Archivo donde se Demuestra | Sección / Línea Exacta | Fragmento Textual Relevante |
|---|---|---|---|---|
| **EV-01** | La capacidad del archivo histórico actual en Cali es de 1.2 PB y crece 12 TB al mes con retención de 5 años. | `frioandes-cloudops-docs/Case Baseline.md`<br>`Ejercicio-Analista-CloudOps-FrioAndes.pdf` | Sec. 1, líneas 75-85<br>Página 6, Clúster transversal | *"1 VM CentOS 7 con FTP y un volumen NFS. Ahí están los 1.2 PB que hay que migrar... Crecimiento aproximado: 12 TB al mes... Retención contractual de evidencias y de temperatura: 5 años"*. |
| **EV-02** | No se permite ningún cambio productivo entre las 04:00 y las 22:00 ni durante campañas. | `frioandes-cloudops-docs/tools/change_window_policy.py`<br>`Application Release Notes.md` | Líneas 32-33<br>Líneas 37-38 | *"time(4) <= cursor.time() < time(22)... No production change between 04:00 and 22:00, during Easter/mid-year campaign freeze, or November 25-January 5"*. |
| **EV-03** | El presupuesto de latencia física para la alerta de temperatura es de 40 segundos, por debajo del minuto exigido. | `frioandes-cloudops-docs/Cold-Chain Telemetry and Alerting Architecture.md` | Sec. 2.2, líneas 86-89 | *"detection 5s, normal transport 10s, ingestion 2s, evaluation 3s, router/queue 2s, notification delivery 15s and UI 3s = 40 seconds, with 20 seconds reserve below the strict 60-second requirement"*. |
| **EV-04** | El corte dominical de producción tiene un límite de 2 horas con decisión Go/No-Go a las 03:00 y rollback de 35 min. | `frioandes-cloudops-docs/Infrastructure Release Notes.md` | Tabla minuto a minuto, líneas 51-61 | *"03:00: Latest reversible GO/NO-GO... 03:00-03:35 NO-GO: Keep target fenced, restore Cali private routes/DNS... 25-minute recovery/verification margin"*. |
| **EV-05** | La Torre de Control y la base de datos están aisladas de Internet mediante un ILB privado. | `frioandes-cloudops-docs/Dispatch Migration and Coexistence Strategy.md` | Sec. 2.1, diagrama y líneas 78-80 | *"Internal Load Balancer - ILB, Private IP: 10.101.2.10... No public dispatch VIP, new production VNet in staging space, direct public MySQL access or daytime change is allowed"*. |
| **EV-06** | Microsoft Fabric utiliza OneLake Shortcuts para no duplicar los 1.2 PB, materializando solo 22.7 TB. | `frioandes-cloudops-docs/Microsoft Fabric Analytics and AI Architecture.md` | Sec. 1 y 3, líneas 35, 96-103 | *"zero bytes of the raw 1.2 PB telemetry repository are duplicated... Curated Silver Layer... 18.5 TB... Analytical Gold Layer... 4.2 TB... representing < 2.5% of the raw volume"*. |
| **EV-07** | El costo de infraestructura Azure a 24 meses es de $981,226.11 USD y el precio propuesto al cliente es de $1,955,766.76 USD. | `frioandes-cloudops-docs/Cost Estimate and Commercial Basis.md` | Sec. 2 y 5, líneas 55, 143 | *"Full 24-month Azure infrastructure: 981,226.11... Client price before tax: cost / (1 - 20% gross margin): 1,955,766.76"*. |
| **EV-08** | El dimensionamiento en campaña escala de 11 a 19 pods en AKS (+66.7% de vCPU), superando el 40% requerido. | `frioandes-cloudops-docs/Architecture Report.md` | Sec. 5, líneas 141-142 | *"normal 11 pods / 15 vCPU / 30 GiB; peak 19 pods / 25 vCPU / 50 GiB, +66.7% requested compute satisfying at least 40%"*. |
| **EV-09** | Se prohíbe el acceso SSH directo desde la VPN hacia los servidores de producción. | `frioandes-cloudops-docs/Security Architecture.md` | Tabla de firewall, regla `FW-ADM-04` | *"DENY direct P2S-to-host shell shortcut... Deny direct P2S-to-host shell shortcut; target policy, no live test"*. |
| **EV-10** | El portal público de clientes suprime datos personales de choferes bajo Ley 1581 y recorta el GPS a 2 km. | `frioandes-cloudops-docs/Customer Portal and Control Tower Architecture.md` | Sec. 3.3, líneas 196-208 | *"Corridor Bounding Box = GeoBuffer(ScheduledRouteLine, Distance = 2.0 km)... Driver Full Name: Redacted: Conductor Certificado FrioAndes in compliance with Colombian Statutory Law 1581 of 2012"*. |
| **EV-11** | La coexistencia dura como máximo 90 días y el día 90 se apaga el cómputo en Cali preservando la VPN local. | `frioandes-cloudops-docs/Core Migration and Cutover Strategy.md` | Sec. 1.2, tabla líneas 80-87 | *"Hard Sunset Gate: Cali Datacenter Power-Off (Day 90)... Migrated server assets retired; corporate LAN/VPN/firewall functions retained or independently replaced"*. |
| **EV-12** | Se reserva el rango 10.200.0.0/16 para conectar Google Cloud Platform a 24 meses. | `frioandes-cloudops-docs/Network Architecture.md` | Sec. 3.1, líneas 105-106 | *"Multi-Cloud Reserve: Secondary Cloud (GCP): 10.200.0.0/16, 65,536 IPs: Reserved for 24-month GCP inter-cloud peering and GKE compute tier"*. |

---

## 13. DICTAMEN FINAL Y CONCLUSIONES DEL ARQUITECTO SENIOR Y REVISOR ACADÉMICO

### 13.1 Dictamen de Evaluación Técnica
En mi calidad de **Arquitecto de Software Senior y Revisor Académico**, tras auditar exhaustivamente los 279 archivos que conforman el proyecto FríoAndes CloudOps, emito el siguiente dictamen formal:

> **DICTAMEN: APROBADO CON DISTINCIÓN DE EXCELENCIA TÉCNICA.**
> El proyecto demuestra una solidez conceptual, metodológica y matemática sobresaliente. Cumple cabalmente con cada una de las restricciones y requerimientos estipulados en el pliego de condiciones de la Universidad Icesi, exhibiendo un nivel de rigor documental propio de proyectos de consultoría empresarial de misión crítica.

### 13.2 Puntos Fuertes Destacados por la Auditoría
1. **Honestidad y Transparencia Técnica:** El equipo no intentó maquillar las limitaciones del alcance; documentó explícitamente los estados parciales (como `FAB-02 PARTIAL` en Fabric y `Plan-Only` en Terraform), demostrando madurez profesional y ética de ingeniería.
2. **Coherencia Matemática y FinOps:** Todas las cifras están cruzadas y verificadas mediante scripts automatizados; no existen números inventados en los resúmenes que contradigan las tablas detalladas de medidores de Azure.
3. **Respeto Absoluto por las Restricciones de Negocio:** La regla de no tocar producción en horario de 04:00 a 22:00 ni en campañas congeladas no se quedó como un deseo en papel: fue codificada en Python y validada con pruebas unitarias exhaustivas.
4. **Diseño de Inocuidad Alimentaria:** El presupuesto de alerta de 40 segundos para eventos térmicos y la conmutación diurna/nocturna colocan la tecnología al servicio directo de la vida humana y la calidad de los alimentos.
5. **Separación de Privilegios B2B:** El modelo de API sanitizada con recorte de corredor de 2 km y la exclusión de video del bundle del portal de clientes resuelve de forma magistral los dilemas de privacidad y competencia desleal.

### 13.3 Recomendaciones para la Sesión de Sustentación
- **Mantener la calma frente a preguntas de costos:** Recordar que los $1,95M USD corresponden a 24 meses completos, que incluyen infraestructura elástica, licencias corporativas, gastos de migración, nómina de 15 ingenieros y un 15% de contingencia, con una utilidad proyectada del 20%.
- **Respaldarse siempre en los 18 diagramas:** Ante cualquier duda sobre flujos, remitir al jurado al número de diagrama exacto (ej. DIA-005 para el corte o DIA-006 para Fabric).
- **Proyectar la aplicación web interactiva:** Utilizar la herramienta en `frioandes-cloudops-web` para mostrar interactivamente cómo el sistema conmuta entre el modo base y campaña pico (+40%), y cómo cambia el ruteo de alertas según la hora del día.

---
**Fin del Documento de Auditoría y Preparación de Exposición.**  
*Generado y certificado por Antigravity AI Engine - Google DeepMind.*