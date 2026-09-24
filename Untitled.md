### Slide 1: Portada e Introducción – La Tesis de KORA

_(Tiempo estimado: 0:00 – 0:45 | ~110 palabras)_

«Muy buenos días, miembros de la Junta Directiva. Hoy les presentamos los avances y validaciones de **KORA: Pasaporte Cultural**.

En la industria musical actual existe una paradoja: tenemos acceso ilimitado a plataformas de streaming, pero conectar con la oferta cultural física y descubrir el talento en vivo de nuestras ciudades sigue siendo un proceso fragmentado y frustrante. KORA nace como un ecosistema _Online-to-Offline_ que convierte la asistencia a eventos en una experiencia gamificada a través de un pasaporte interactivo.

Nuestra prioridad en esta etapa ha sido clara: **fracasar rápido, barato y aprender de métricas reales** antes de construir a ciegas. A continuación, les mostraremos cómo validamos primero nuestra propuesta de valor en el mercado digital, el quiebre de hipótesis que redefinió nuestro segmento, cómo iteró el prototipo funcional y la evidencia real que demuestra disposición a pagar.»

### Slide 2: Validación de la Propuesta de Valor en Meta Ads – El Quiebre de Hipótesis

_(Tiempo estimado: 0:45 – 2:15 | ~210 palabras)_

«Comenzamos validando la deseabilidad y la tracción de nuestra propuesta de valor antes de invertir tiempo y recursos en desarrollo masivo. Nuestra hipótesis de partida era que nuestro nicho prioritario estaba compuesto exclusivamente por jóvenes universitarios de 18 a 24 años.

Para comprobarlo, lanzamos una campaña real en Meta Ads compuesta por un post visual y un video promocional de diez segundos comunicando la experiencia de descubrir eventos locales y coleccionar sellos. Los resultados cuantitativos fueron contundentes: logramos un **alcance de 5.736 personas**, **7.144 reproducciones de video**, una **tasa de captura inicial del 21%** y un **costo por resultado altamente eficiente de entre $100 y $104 pesos colombianos**.

Sin embargo, este experimento **nos quebró por completo la hipótesis del segmento de mercado**: los jóvenes de 18 a 24 años representaron apenas el 3% de las interacciones. Quienes realmente reaccionaron, retuvieron el video y mostraron un alto interés orgánico fueron las personas entre los **35 y los 64 años**, destacando los hombres de 55 a 64 años con un 35% de los resultados y las mujeres de 35 a 44 años con un 26%. Este aprendizaje nos demostró que la necesidad de encontrar planes culturales centralizados y de calidad está fuertemente latente en un segmento adulto con mayor poder adquisitivo que no habíamos considerado inicialmente.»

### Slide 3: Reenfoque del Segmento y Diferenciación Competitiva

_(Tiempo estimado: 2:15 – 3:30 | ~190 palabras)_

«Este hallazgo no significó descartar la solución, sino enriquecer y afinar nuestra propuesta de valor. Al analizar a la competencia, identificamos que las alternativas existentes son simples canales de notificación de conciertos: le avisan al usuario de una fecha y lo dejan solo, obligándolo a buscar en Google, rastrear ticketeras y lidiar con la dispersión de información. La gente se queja de esa desconexión.

KORA se diferencia al ofrecer una experiencia integral de extremo a extremo que atiende a dos audiencias complementarias: por un lado, para el público adulto descubierto en Meta Ads, resolvemos la fricción de búsqueda centralizando planes y curaduría cultural confiable; por el otro, para el público joven, transformamos el evento en un juego de estatus y comunidad.

Nuestra ventaja competitiva y difícil de replicar radica en dos pilares: la **geolocalización cultural interactiva** y el **storytelling detrás del artista**. No somos un directorio estático de eventos; somos el puente que conecta la identidad del asistente con el ecosistema musical de su ciudad.»

### Slide 4: Madurez del Prototipo – Validación de Deseabilidad con Usuarios

_(Tiempo estimado: 3:30 – 5:00 | ~220 palabras)_

«Habiendo validado el interés general en canales digitales, pasamos a comprobar la usabilidad y deseabilidad de la solución mediante prototipos funcionales e interactivos puestos a prueba directamente con usuarios.

De estas sesiones de prueba presenciales extrajimos tres aprendizajes determinantes:

Primero, sobre el **Pasaporte Cultural y los Sellos Digitales**: observamos que el 85% de los participantes mostró un entusiasmo inmediato, pero con una condición clave: el sello no tiene valor si se queda atrapado dentro de la aplicación; su verdadero valor surge al poder compartirlo en historias de redes sociales como Instagram y al introducir dinámicas de escasez con sellos de edición limitada.

Segundo, sobre el **Storytelling frente al Algoritmo**: los usuarios afirmaron que lo que más valoran de KORA no es solo el mapa, sino conocer el trasfondo y la historia íntima del artista local, algo que plataformas genéricas como Spotify no ofrecen. Por ello, enriquecimos las fichas con notas de voz exclusivas y la historia de origen de cada banda.

Y tercero, sobre la **Gamificación**: descubrimos que ubicar un rango final como 'Leyenda KORA' se percibía demasiado lejano, desmotivando a los usuarios en las etapas iniciales. En consecuencia, rediseñamos la curva de experiencia para otorgar victorias rápidas y ascensos durante las primeras dos semanas de uso.»

### Slide 5: Factibilidad Técnica – Resolviendo la Operación en el Mundo Real

_(Tiempo estimado: 5:00 – 6:00 | ~180 palabras)_

«En el componente técnico, sometimos el sistema a condiciones reales de operación para no construir sobre supuestos inviables. Validamos tres frentes críticos:

En primer lugar, el **consumo energético**: el muestreo continuo de GPS consumía un 12% de batería por hora en dispositivos Android de gama media. Para evitar la desinstalación de la app, pivotamos hacia un esquema de _geofencing adaptativo_ que solo activa los sensores cuando el acelerómetro detecta desplazamiento del usuario.

En segundo lugar, la **carga y actualización de eventos**: comprobamos que extraer datos de redes sociales mediante APIs masivas era inestable y ofrecía apenas un 35% de precisión. Por lo tanto, orientamos la integración hacia alianzas con plataformas locales de boletería, cuya precisión alcanza el 90%, complementado con un portal de autogestión directa para los artistas.

Y en tercer lugar, la **conectividad en eventos en vivo**: en recintos cerrados con alta aglomeración, las redes móviles colapsaban provocando hasta un 40% de fallas en el check-in. Diseñamos entonces una arquitectura _Offline-First_, donde el código QR se valida de manera instantánea y local mediante firma criptográfica, sincronizando los datos en la nube en segundo plano en cuanto se restablece la conexión.»

### Slide 6: Evidencia de Captura de Valor – Disposición a Pagar Validada

_(Tiempo estimado: 6:00 – 7:30 | ~220 palabras)_

«Un modelo de negocio solo es viable si existe evidencia concreta de que el mercado está dispuesto a pagar. Para medir esto sin depender de respuestas de complacencia, implementamos pruebas de validación con la técnica de puerta falsa (_Fake Door_) dentro del prototipo:

Primero, evaluamos la **monetización B2C mediante un esquema Freemium**: colocamos una opción para adquirir personalizaciones estéticas y beneficios VIP para el pasaporte por un valor de $3.000 pesos colombianos. Nuestro criterio de validación era alcanzar un 15% de interacción, y **obtuvimos un 28% de usuarios que intentaron realizar la transacción de compra de forma espontánea**.

Segundo, testeamos el **micro-mecenazgo voluntario**: incluimos un botón para 'Invitarle una cerveza a la banda' por $4.000 pesos colombianos directamente desde el evento. **Uno de cada tres usuarios intentó presionar el botón de aporte**, preguntando si dicha acción les otorgaba un sello exclusivo de patrocinador o más puntos de experiencia dentro de la comunidad.

Y en el frente de las **organizaciones y bandas emergentes**, entrevistamos a agrupaciones y gestores culturales: el **100% de los líderes manifestó un interés inmediato en utilizar el check-in de KORA en sus presentaciones**. Su dolor principal es que actualmente no tienen ninguna herramienta para saber quiénes asisten a sus conciertos físicos ni cómo fidelizarlos más allá de una red social.»

### Slide 7: Innovación en el Modelo de Negocio – Usuario vs. Cliente Pagador

_(Tiempo estimado: 7:30 – 8:30 | ~190 palabras)_

«A partir de estos aprendizajes, definimos con total claridad la diferencia entre quién utiliza la solución y quién paga por ella. KORA opera como una plataforma multilateral de doble cara:

El **usuario final** accede de manera gratuita para descubrir eventos, registrar su asistencia física y construir su pasaporte cultural. Su monetización se da por transacciones voluntarias de personalización estética y apoyo directo a artistas.

Sin embargo, el **cliente que paga sumas significativas por la solución son las organizaciones, los productores de eventos, las salas de música y las marcas aliadas**. ¿Por qué pagan? Porque necesitan con urgencia la data y las métricas de asistencia que hoy nadie les provee. A través de nuestro panel administrativo, les entregamos analítica de audiencia en vivo: datos demográficos consolidados, niveles de recurrencia, horas de mayor afluencia y preferencias musicales.

Nuestras fuentes de ingreso se estructuran en tres vías: primero, suscripciones al panel de analítica para promotores y salas; segundo, comisiones por integración de boletería aliada y micro-donaciones; y tercero, el modelo freemium de diferenciación digital para los usuarios.»

### Slide 8: Roadmap de Crecimiento & Networking Estratégico

_(Tiempo estimado: 8:30 – 9:15 | ~140 palabras)_

«Para materializar esta oportunidad de forma ordenada, seguimos el principio de aprender a gatear antes de pretender correr:

En la **fase actual de gateo**, nos concentramos en el circuito local e independiente de Cali, validando el loop de check-in y la adopción de sellos con diez eventos presenciales controlados.

En la **segunda fase**, formalizaremos el despliegue del dashboard de analítica para organizadores, integraremos pasarelas de pago ágiles como Mercado Pago para habilitar las compras en un clic, y consolidaremos la alianza con ticketeras regionales.

En la **fase de expansión**, escalaremos el modelo hacia otras capitales del país. Paralelamente, estamos ejecutando una estrategia activa de networking contactando por LinkedIn a directores de festivales, gestores de salas y líderes de la industria del entretenimiento para co-diseñar el panel administrativo con base en las métricas exactas que ellos requieren para contratar el servicio.»

### Slide 9: Preguntas Estratégicas para la Junta Directiva

_(Tiempo estimado: 9:15 – 10:00 | ~130 palabras)_

«Para concluir nuestra intervención y abrir el espacio de conversación, queremos aprovechar la experiencia estratégica y la visión de negocios de esta mesa directiva planteándoles tres preguntas fundamentales:

1. Tras comprobar en Meta Ads que el público de 35 a 64 años generó la mayor tasa de interacción con un costo sumamente bajo, ¿nos recomiendan reorientar la adquisición principal hacia este segmento con mayor liquidez financiera, o mantener el foco en jóvenes profundizando en la gamificación?
    
2. Para el segmento de organizadores y salas de eventos, ¿consideran más conveniente arrancar con un modelo transaccional por asistencia registrada o una suscripción mensual fija por el uso del panel de datos?
    
3. ¿Cuál consideran que es la métrica de tracción indispensable que debemos alcanzar en esta etapa piloto para sentarnos a negociar alianzas con las grandes plataformas de boletería?
    

Muchas gracias por su atención; quedamos atentos a sus preguntas y sugerencias.»