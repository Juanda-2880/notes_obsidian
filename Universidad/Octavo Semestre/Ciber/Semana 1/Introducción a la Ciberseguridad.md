#Universidad #Ciberseguridad 

---

### NOTAS POST-LECTURA:
#Notas

### 1. La Base de Todo: El Triángulo CID y el No Repudio

- **Confidencialidad:** Proteger la información de accesos no autorizados. (Ej. Controles de acceso, permisos, encriptación ).
    
- **Integridad:** Proteger la información de modificaciones no autorizadas. (Ej. Hashes, firmas digitales ).
    
- **Disponibilidad:** Garantizar el acceso confiable y oportuno a los datos y sistemas. (Ej. Backups, redundancia ).
    
- **No Repudio:** Garantizar que ni el emisor ni el receptor puedan negar haber enviado o recibido un mensaje. Se logra mediante firmas digitales y registros (_logs_) transaccionales.
    

### 2. Diferencia Clave: Información vs. Ciber

- **Seguridad de la Información:** Es el concepto general. Protege la información en **cualquier formato** (papel, digital, comunicaciones verbales, propiedad intelectual en la mente).
    
- **Ciberseguridad:** Es una parte de la seguridad de la información. Se enfoca exclusivamente en **activos digitales** y sistemas interconectados. Además, lidia con amenazas propias del ciberespacio, como las APTs o los ataques patrocinados por estados.
    

### 3. Las 5 Funciones del Marco NIST

Para proteger activos digitales frente a incidentes, la gestión se divide en cinco pasos vitales:

1. **Identificar:** Conocer el entorno para minimizar riesgos en sistemas y datos.
    
2. **Proteger:** Diseñar e implementar salvaguardas (controles preventivos).
    
3. **Detectar:** Monitorear para identificar eventos de ciberseguridad a tiempo.
    
4. **Responder:** Tomar acciones inmediatas al descubrir un incidente.
    
5. **Recuperar:** Restaurar los servicios comprometidos y asegurar la resiliencia.
    

### 4. Amenazas, Riesgos y Controles

- **APTs (Amenazas Persistentes Avanzadas):** Atacantes con mucho tiempo, dinero, paciencia y experiencia que usan múltiples vías para infiltrarse sin ser detectados.
    
- **El Riesgo Residual:** El riesgo nunca se elimina al 100%. Los _stakeholders_ aplican controles para reducir vulnerabilidades y bajar el riesgo a un nivel que el negocio pueda aceptar.
    
- **Impulsores (Drivers):** La postura de seguridad depende tanto de la tecnología (nube, complejidad de TI) como del negocio (apetito de riesgo, regulaciones, adquisiciones).
    

### 5. El Factor Humano

- **Conciencia Situacional:** Los profesionales deben entender su entorno interno (cultura de la empresa) y estar al tanto de las tendencias externas (nuevas tecnologías, nuevos ataques) para ser efectivos.
    
- **Brecha de Competencias (Skills Gap):** Hay un déficit grave de profesionales capacitados. Las amenazas crecen en volumen y sofisticación, pero el mercado no tiene suficientes expertos para cubrir la demanda.

----
### Evolución de la Ciberseguridad
#Contexto 

A lo largo de la historia, la protección de la privacidad y la integridad de la información ha sido una necesidad humana constante. Desde métodos clásicos como el cifrado César —diseñado originalmente para garantizar la confidencialidad— hasta los entornos modernos, las exigencias de seguridad han evolucionado drásticamente a la par de la tecnología.

Hoy en día, la seguridad de la información persigue un triple objetivo fundamental conocido como la **Tríada CIA** (por sus siglas en inglés):

- **Confidencialidad:** Garantiza la protección de los datos contra accesos o divulgaciones no autorizadas.
    
- **Integridad:** Asegura la protección de la información frente a modificaciones o alteraciones no autorizadas, manteniendo su exactitud.
    
- **Disponibilidad:** Salvaguarda el acceso continuo, confiable y oportuno a los sistemas y recursos cuando el negocio lo requiera.


![[Introducción a la Ciberseguridad-1785291908236.webp|276]]


### Delimitación: Seguridad de la Información vs. Ciberseguridad

Aunque suelen usarse como sinónimos, la ciberseguridad es una rama especializada dentro de la seguridad de la información. Específicamente, se define como la protección de los activos de información mediante el tratamiento de amenazas que afectan a los datos procesados, almacenados y transportados a través de sistemas interconectados en red.

A diferencia de la seguridad de la información general, la ciberseguridad se enfoca de manera exclusiva en mitigar las amenazas provenientes del ciberespacio global y de actores adversarios. Por lo tanto, aspectos como los desastres naturales, los fallos de seguridad física o los errores humanos no intencionados entran en el dominio de la seguridad de la información, pero quedan fuera del espectro estricto de la ciberseguridad


### Relaciones de Dominio y la Norma ISO/IEC 27032

El estándar **ISO/IEC 27032** describe la compleja interacción entre la ciberseguridad y otros dominios de seguridad (como la seguridad de redes, de aplicaciones e Internet). Un claro ejemplo de esta interdependencia ocurre en las infraestructuras críticas (agua, transporte, energía) : si bien un fallo interno en su operación no siempre altera la ciberseguridad corporativa, la ausencia de controles digitales adecuados en estas redes interconectadas puede tumbar la disponibilidad y confiabilidad de servicios vitales, incluyendo las telecomunicaciones.




![[Introducción a la Ciberseguridad-1785292134299.webp]]


Debido a que muchas vulnerabilidades tocan temas de seguridad nacional, las debilidades de infraestructura no se discuten abiertamente en el mercado. Esto hace indispensable un marco formal de intercambio de información y coordinación de incidentes que alinee a entidades públicas y privadas, locales y globales, para afrontar los riesgos de forma conjunta sin exponer datos sensibles.

### Ciberseguridad y Conciencia Situacional

#### El Panorama de Amenazas Avanzadas

El dinamismo tecnológico y la hiperconectividad abren vectores de ataque inéditos. Esto obliga a los profesionales del área a desarrollar una alta **conciencia situacional**, la cual permite comprender a fondo tanto el entorno operativo interno como las tendencias de amenazas globales externas.

Dentro de este ecosistema destacan las **APTs (Amenazas Persistentes Avanzadas)**. Estas no son ataques aleatorios; son campañas ejecutadas por adversarios con sofisticados niveles de experiencia, financiamiento y paciencia, capaces de explotar múltiples vectores simultáneamente durante largos periodos para alcanzar sus objetivos.


![[Introducción a la Ciberseguridad-1785293768787.webp]]

#### Gestión de Riesgos y el Rol de los Stakeholders

Las amenazas se categorizan según la probabilidad de impactar los activos con valor real para la organización. La responsabilidad última recae sobre los _stakeholders_, quienes deben imponer controles técnicos y organizacionales con el fin de reducir las vulnerabilidades y mitigar los impactos.

> **Nota conceptual de valor:** El riesgo nunca puede eliminarse por completo. Las organizaciones deben convivir con el **riesgo residual** e implementar un monitoreo continuo para asegurar que los controles siguen siendo efectivos frente a nuevas variantes de ataque.

El nivel de aceptación del riesgo varía según la madurez y naturaleza del negocio: una _startup_ en crecimiento suele ser más tolerante al riesgo digital que una corporación multinacional consolidada.

#### Impulsores de Seguridad (Drivers) Tecnológicos y de Negocio

Para estructurar una estrategia de ciberseguridad efectiva, se deben evaluar los factores internos y externos que condicionan la postura de seguridad de la empresa:

- **Factores Tecnológicos:** Incluyen el nivel de complejidad de la infraestructura de TI, los tipos de conectividad (interna, pública o con terceros) y el uso de sistemas en la nube, locales o híbridos. También entran en juego las herramientas de seguridad disponibles, las capacidades del personal de soporte operativo y las competencias de la propia comunidad de usuarios.
    
- **Factores de Negocio:** Contemplan la naturaleza del sector comercial, la alineación con las tendencias de seguridad del mercado y los niveles de apetito o tolerancia al riesgo corporativo. Asimismo, se deben integrar la misión estratégica de la empresa, las regulaciones de cumplimiento locales e internacionales, y los procesos de fusiones, adquisiciones o subcontratación (_outsourcing_) de proveedores.
    

La incapacidad para anticipar e integrar estos impulsores tecnológicos y comerciales se traduce directamente en una postura de seguridad deficiente, incrementando drásticamente la exposición a brechas y limitando la capacidad de respuesta ante incidentes imprevistos.

### Brecha de Competencias en Ciberseguridad

La ciberseguridad es un campo que exige profesionales altamente capacitados. Ya no basta con conocimientos técnicos básicos; se requiere educación sólida y liderazgo para hacer frente a un panorama tecnológico en constante cambio. La combinación de vectores de amenaza avanzados, tecnologías emergentes y un sinfín de regulaciones hace indispensable que los profesionales dominen tanto la tecnología como las comunicaciones y el negocio.

El enfoque de la ciberseguridad aborda tanto las amenazas internas como las externas que ponen en riesgo los activos digitales de una organización, centrándose en el procesamiento de datos críticos, las transacciones, el análisis de riesgos y la ingeniería de seguridad de los sistemas de información.

----

### Diferencia entre Seguridad de la Información y Ciberseguridad

Es vital entender dónde termina una disciplina y empieza la otra:

- **Seguridad de la Información:** Se ocupa de proteger la información sin importar su formato. Esto incluye documentos en papel, datos digitales, la propiedad intelectual (incluso la que está en la mente de las personas) y las comunicaciones visuales o verbales.
    
- **Ciberseguridad:** Es una subdisciplina enfocada exclusivamente en proteger los **activos digitales**. Abarca todo lo relacionado con el hardware de red, el software y la información que es procesada, almacenada en sistemas aislados o transportada a través de entornos de información interconectados.
    

> **Explicación Adicional: Ataques de Estado-Nación y APTs** Conceptos como los ataques patrocinados por estados y las APTs pertenecen casi de forma exclusiva al dominio de la ciberseguridad. * **APTs (Amenazas Persistentes Avanzadas):** Son ciberataques orquestados por adversarios que poseen un nivel altísimo de experiencia, tiempo, paciencia y recursos significativos. No buscan un impacto rápido; utilizan múltiples vectores de ataque para infiltrarse en una red y crear oportunidades para lograr sus objetivos de forma prolongada y silenciosa.
> 
> - **Ataques Patrocinados por Estados (Nation-State Attacks):** Son operaciones cibernéticas financiadas y ejecutadas por gobiernos (o grupos apoyados por ellos). Su objetivo suele ser el espionaje, el sabotaje de infraestructuras críticas extranjeras o el robo de propiedad intelectual a gran escala.


#### Protección de Activos Digitales

En el núcleo del marco de ciberseguridad del Instituto Nacional de Estándares y Tecnología (NIST), se identifican cinco funciones clave para proteger los activos digitales. Estas funciones coinciden con las metodologías modernas de gestión de incidentes:

| **Función**                | **Descripción de la Actividad**                                                                                                        |
| -------------------------- | -------------------------------------------------------------------------------------------------------------------------------------- |
| **Identificar (Identify)** | Utilizar la comprensión del contexto organizacional para minimizar los riesgos asociados a los sistemas, activos, datos y capacidades. |
| **Proteger (Protect)**     | Diseñar e implementar salvaguardas para limitar el impacto de eventos potenciales sobre los servicios e infraestructuras críticas.     |
| **Detectar (Detect)**      | Implementar actividades que permitan identificar rápidamente la ocurrencia de un evento de ciberseguridad.                             |
| **Responder (Respond)**    | Tomar las medidas y acciones apropiadas una vez que se tiene conocimiento de un evento de seguridad.                                   |
| **Recuperar (Recover)**    | Planificar la resiliencia y la reparación oportuna de los servicios y capacidades que hayan sido comprometidos.                        |

### Objetivos de la Ciberseguridad

Mantener el ciberespacio útil y confiable requiere que todas las partes interesadas asuman un rol activo en la seguridad, yendo más allá de la simple protección de sus propios activos. Deben estar preparados para identificar y gestionar riesgos emergentes de forma proactiva. La ciberseguridad trabaja de la mano con la seguridad de la información y trasciende la mera seguridad de Internet, redes o aplicaciones; requiere la orquestación de todos estos componentes.

Los objetivos principales de la ciberseguridad en la protección de los activos digitales se resumen en garantizar tres pilares fundamentales:


![[Introducción a la Ciberseguridad-1785296297607.webp]]


**1. Confidencialidad** Es la protección de la información contra accesos o divulgaciones no autorizadas. El nivel de confidencialidad requerido depende del tipo de información y puede cambiar con el tiempo. Por ejemplo, los datos médicos, financieros o personales exigen un grado de protección mucho más alto. De manera similar, una empresa debe proteger agresivamente los detalles de un nuevo producto antes de su lanzamiento, pero esa misma información se vuelve pública después. Esto se controla mediante permisos de archivos, cifrado y controles de acceso.

**2. Integridad** Garantiza que la información esté protegida contra modificaciones no autorizadas. Por ejemplo, si un banco transfiere $10.000 a otra institución, es crítico que el monto no se altere a $100.000 en el proceso. Este concepto también aplica a configuraciones, software y mensajes electrónicos. Cualquier violación a la integridad es una alerta grave, ya que suele ser el primer paso de un ataque mayor contra la confidencialidad o disponibilidad del sistema. Se verifica y controla mediante firmas digitales, encriptación, _hashes_ y registros (_logs_).

**3. Disponibilidad** Asegura el acceso y uso confiable y oportuno de la información y los sistemas. Incluye salvaguardas para evitar que los datos se eliminen de manera accidental o maliciosa. En sistemas de misión crítica, esto es fundamental, ya que cualquier interrupción se traduce directamente en pérdida de productividad e ingresos. Además, la pérdida de datos afecta la capacidad de la gerencia para tomar decisiones. La disponibilidad se protege mediante redundancia de sistemas, copias de seguridad (_backups_) y planes de continuidad del negocio.


![[Introducción a la Ciberseguridad-1785297336814.webp]]

### No Repudio (Nonrepudiation)

El no repudio es un concepto clave que garantiza la autenticidad de un mensaje o fragmento de información. Cuando se envía información, es crucial verificar que realmente proviene de la fuente declarada. Este mecanismo proporciona los medios técnicos para que ni el remitente pueda negar haber enviado la información, ni el receptor pueda negar haberla recibido. En la práctica, se implementa mediante registros transaccionales y **firmas digitales**.