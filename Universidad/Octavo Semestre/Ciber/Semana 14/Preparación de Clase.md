#Universidad #Ciberseguridad 

¿Qué es un firewall y cuál es su función dentro de la seguridad de una red? Explique brevemente la diferencia entre un firewall de filtrado de paquetes, uno con inspección de estado y un firewall de nueva generación (NGFW).

----

Un Firewall es un sistema de seguridad de red de las computadoras que restringe el tráfico de internet entrante, saliente o dentro de una red privada. 

Este funciona bloqueando o permitiendo los paquetes de datos de forma selectiva. Normalmente, su finalidad es ayuda a prevenir la actividad maliciosa y evitar que cualquier persona (Dentro o fuera de la red Privada) Pueda realizar actividades no autorizadas en la web


![[Preparación de Clase-1793805495434.webp]]

[Comparativa de Firewalls: NGFW vs WAF vs Stateful Inspection](https://scansearch.net/es/articles/firewall-types-compared/)

----

### 1. VPN y Mecanismos de Protección

Una **VPN (Virtual Private Network)** es una tecnología de red que crea una extensión segura de una red privada sobre una infraestructura pública o no confiable (como Internet). Su objetivo es permitir que dos o más extremos intercambien datos como si estuvieran en un canal físico dedicado y privado.

  

#### Mecanismos de seguridad

- **Cifrado (Confidencialidad):** Emplea algoritmos simétricos robustos (como AES-GCM o ChaCha20-Poly1305) para codificar los datos en tránsito, de modo que cualquier interceptor solo capture texto cifrado ininteligible.
    
      
    
- **Autenticación e Integridad:** Utiliza funciones hash seguras (HMAC con SHA-256/SHA-512) y firmas digitales basadas en infraestructura de clave pública (PKI con certificados X.509) o claves precompartidas (PSK). Esto confirma la identidad de los extremos y asegura que los paquetes no hayan sido alterados en tránsito.
    
      
    
- **Protección contra repetición (Anti-Replay):** Protocolos como IPsec incorporan números de secuencia obligatorios en las cabeceras para que el receptor rechace paquetes duplicados o reinyectados fuera de la ventana permitida.
    
      
    
- **Gestión de claves:** Emplea mecanismos como Diffie-Hellman (DH) o su variante sobre curvas elípticas (ECDH) para establecer claves simétricas de sesión de forma segura sin transmitirlas en texto claro.
    
      
    

#### Modo Transporte vs. Modo Túnel (IPsec)

|**Característica**|**Modo Transporte**|**Modo Túnel**|
|---|---|---|
|**Qué se cifra**|Solo la **carga útil (payload)** de nivel superior (L4: TCP/UDP + datos).|El **paquete IP original completo** (cabecera IP original + payload).|
|**Cabecera IP**|Mantiene la cabecera IP original visible y sin modificar para el enrutamiento.|Se genera una **nueva cabecera IP externa**; la cabecera original queda cifrada dentro.|
|**Ocultación de topología**|**No**. Los nodos intermedios ven las direcciones IP reales de origen y destino.|**Sí**. Oculta las IPs y la topología de la red interna detrás de las IPs de los gateways.|
|**Caso de uso principal**|Comunicaciones **extremo a extremo** (Host-to-Host) entre dos servidores directos.|Enlaces **Sitio a Sitio** (LAN-to-LAN) o **Acceso Remoto** (Host-to-Gateway).|

### 2. IDS vs. IPS

La diferencia fundamental radica en su **posición arquitectónica en la red** y en su **capacidad de intervenir en tiempo real sobre el tráfico**.

  

- **IDS (Intrusion Detection System):** Opera de manera **pasiva y fuera de línea (Out-of-band)** mediante puertos espejo (SPAN/mirror ports) o TAPs de red. Su rol es exclusivamente de monitorización, inspección y visibilidad.
    
      
    
- **IPS (Intrusion Prevention System):** Opera de manera **activa y en línea (Inline)**, ubicándose directamente en la ruta por la que fluyen los paquetes (como un puente o firewall de capa de aplicación).
    
      
    

#### Comportamiento ante una intrusión

- **El IDS:** Al detectar una anomalía o firma maliciosa, genera una alerta (registro en Syslog, notificación al SIEM/SOC o trap SNMP). **No detiene el ataque por sí mismo**; el paquete malicioso llega a su destino y puede comprometer el objetivo mientras el equipo de seguridad recibe la alerta.
    
      
    
- **El IPS:** Al detectar la firma o anomalía, **bloquea la amenaza en tiempo real antes de que alcance el destino**. Puede descartar el paquete (_drop_), forzar el cierre de la conexión TCP enviando paquetes `RST` a ambos extremos, o inyectar dinámicamente una regla de bloqueo temporal en el firewall perimetral para aislar la IP origen.
    
      
    

#### Por qué se utilizan de manera conjunta

1. **Control de falsos positivos:** Un IPS agresivo con falsos positivos bloquea tráfico legítimo de producción, generando una denegación de servicio (DoS) involuntaria. El IDS permite auditar y afinar firmas en segmentos complejos sin riesgo de corte.
    
      
    
2. **Defensa en profundidad y rendimiento:** El IPS se ubica en el perímetro o entre zonas críticas para detener ataques masivos conocidos (exploits comunes, fuerza bruta). El IDS se distribuye internamente (sensores en switches de agregación o core) para analizar tráfico este-oeste, detectar movimientos laterales y recolectar telemetría sin degradar la latencia de los enlaces principales.
    
      
    

### 3. Relación entre SIEM, SOC y SOAR en la Gestión de Incidentes

Estos tres elementos integran el ciclo operativo moderno de defensa:

  

- **SOC (Security Operations Center):** Es el **equipo humano, organizativo y de procesos** responsable de la monitorización, análisis y respuesta continua.
    
      
    
- **SIEM (Security Information and Event Management):** Es la **plataforma de centralización analítica**. Recolecta logs de múltiples orígenes (firewalls, EDR, servidores, proxies), normaliza los datos y ejecuta reglas de correlación para detectar anomalías.
    
      
    
- **SOAR (Security Orchestration, Automation, and Response):** Es el **motor de ejecución y automatización**. Se conecta vía APIs con múltiples tecnologías para orquestar flujos de trabajo (_playbooks_), enriquecer datos y ejecutar respuestas automáticas sin intervención manual inicial.
    
      
    

```
Telemetría / Logs ──> [ SIEM: Normaliza & Correlaciona ] ──> Genera Alerta
                                                                 │
                                                                 ▼
                                                    [ SOAR: Ejecuta Playbook ]
                                                    (Enriquecimiento inicial)
                                                                 │
                                                                 ▼
                                                    [ SOC: Análisis y Triage ]
                                                    (Validación N1 / N2 / N3)
                                                                 │
                                                                 ▼
[ Remediación (SOAR / Firewall / EDR) ] <── [ Aprobación / Orden de Respuesta ]
```

#### Flujo paso a paso desde la alerta hasta la respuesta:

1. **Generación y Detección (SIEM):** Un endpoint infectado genera múltiples conexiones a una IP sospechosa. El SIEM correlaciona los logs de DNS, firewall y EDR, identifica el patrón de Comando y Control (C2) y dispara una **alerta unificada**.
    
      
    
2. **Orquestación y Enriquecimiento (SOAR):** El SOAR recibe la alerta del SIEM e inicia de forma autónoma un _playbook_ de triaje: consulta la reputación de la IP externa en bases de Threat Intelligence, analiza el hash del binario involucrado y extrae metadatos del usuario logueado.
    
      
    
3. **Análisis y Validación (SOC):** El analista del SOC recibe el incidente ya contextualizado en su consola, descartando falsos positivos en minutos. Confirma que se trata de un incidente real (True Positive) y determina el nivel de contención requerido.
    
      
    
4. **Respuesta y Contención (SOAR + SOC):** Con la aprobación del analista (o automáticamente según la severidad), el SOAR ejecuta las acciones defensivas: ordena al EDR aislar el equipo de la red, actualiza la lista de bloqueo del firewall perimetral mediante API para mitigar la IP atacante y desactiva la cuenta del usuario en el Identity Provider (IDP).
    
      
    
5. **Cierre y Retroalimentación:** El SOC documenta las lecciones aprendidas y ajusta las reglas del SIEM o las acciones del SOAR para mitigar variantes futuras.
    
      
    

### 4. Honeypots, Honeynets y Padded Cells

Un **honeypot** es un recurso informático intencionalmente señuelo, vulnerable y monitoreado, diseñado para ser explorado, atacado o comprometido. Su principio fundamental es que **no tiene tráfico legítimo ni valor de producción autorizado**: cualquier interacción dirigida a un honeypot es, por definición, no autorizada o maliciosa.

  

Su propósito dentro de la estrategia de defensa es:

  

- **Detección temprana:** Alerta sobre intrusiones en etapas preliminares (escaneos, reconocimientos).
    
      
    
- **Desviación:** Aleja al atacante de los sistemas de producción críticos mientras gasta recursos en un señuelo.
    
      
    
- **Inteligencia de amenazas:** Permite estudiar las tácticas, técnicas y herramientas del adversario en un entorno controlado.
    
      
    

#### Diferencias entre Honeypot, Honeynet y Padded Cell

- **Honeypot:** Es una **unidad o servicio individual señuelo**. Puede ser de baja interacción (simula solo puertos o respuestas básicas como un falso banner SSH) o de alta interacción (una máquina virtual real vulnerable).
    
      
    
- **Honeynet:** Es una **red completa de honeypots** que emula una subred corporativa real (con servidores web, bases de datos, routers y controladores de dominio). Su objetivo es observar ataques multietapa sofisticados, ingeniería social y movimientos laterales dentro de una topología extensa.
    
      
    
- **Padded Cell (Celda Acolchada):** Es un entorno señuelo especializado al cual un **intruso activo es transferido dinámicamente** desde la red de producción. Cuando un IDS/IPS detecta que un atacante ha vulnerado o intenta vulnerar un servidor real, redirige de forma transparente la sesión hacia la _padded cell_. Allí el atacante cree seguir dentro del objetivo original, pero está contenido, aislado de datos sensibles y monitorizado detalladamente.
    
      
    

#### Información que obtiene una organización con su uso

- **TTPs (Tácticas, Técnicas y Procedimientos):** Comportamiento real del adversario según el marco MITRE ATT&CK (cómo intentan escalar privilegios, qué comandos ejecutan primero).
    
      
    
- **Muestras de malware y exploits de día cero (0-day):** Captura de binarios, scripts, payloads y secuencias de explotación no documentadas previamente por proveedores de seguridad.
    
      
    
- **Indicadores de Compromiso (IoCs):** Direcciones IP de origen, servidores de C2, dominios, firmas de herramientas de ataque y hashes de archivos maliciosos para incorporarlos de inmediato a las reglas defensivas perimetrales.
    
      
    
- **Nivel de sofisticación e intención:** Determina si el atacante es un bot automatizado que busca vulnerabilidades genéricas o un actor dirigido con objetivos específicos contra la organización.