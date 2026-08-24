#Auto #PlatsII #Monitoreo #Pipeline #Universidad 
### ¿Qué es Google Lighthouse?

Google Lighthouse es una herramienta automatizada y de código abierto desarrollada por Google, diseñada para auditar y mejorar la calidad de las aplicaciones web. Puede ejecutarse en cualquier página (pública o que requiera autenticación) y genera un informe detallado con una calificación del 0 al 100 en diferentes categorías, proporcionando consejos accionables para corregir los problemas encontrados.

**Formas de ejecución:**

No solo funciona desde la pestaña "Lighthouse" en las herramientas de desarrollador de Chrome (DevTools), sino que también se puede ejecutar como una extensión, desde la línea de comandos (CLI) o como un módulo de Node.js, lo que la hace perfecta para la automatización.

### Los Pilares de Auditoría de Lighthouse

Lighthouse no solo mide la velocidad; audita la aplicación web basándose en varias categorías principales:

1. **Rendimiento (Performance):** Qué tan rápido carga la página y qué tan rápido los usuarios pueden interactuar con ella.
    
2. **Accesibilidad (Accessibility):** Verifica si la web es utilizable por personas con discapacidades (ej. contraste de colores adecuado, etiquetas `aria` para lectores de pantalla, navegación por teclado).
    
3. **Buenas Prácticas (Best Practices):** Comprueba que la página utilice estándares web modernos y seguros (ej. uso de HTTPS, imágenes con resoluciones correctas, ausencia de vulnerabilidades conocidas en librerías frontend).
    
4. **SEO (Search Engine Optimization):** Evalúa si la página está optimizada para ser encontrada y rankeada correctamente por los motores de búsqueda.
    
5. **PWA (Progressive Web App):** Verifica si la página cumple con los estándares para ser instalable y funcionar sin conexión como una aplicación nativa.
    

### Métricas Clave de Rendimiento (Core Web Vitals)

Google utiliza un conjunto de métricas llamadas _Core Web Vitals_ para medir la experiencia real del usuario. Estas son las más importantes que Lighthouse extrae en su reporte:

- **FCP (First Contentful Paint - Primer Despliegue de Contenido):** Mide el tiempo que transcurre desde que la página comienza a cargar hasta que cualquier parte del contenido (el primer texto, imagen o bloque de color) aparece en la pantalla. _Ideal: menos de 1.8 segundos._
    
- **LCP (Largest Contentful Paint - Despliegue del Contenido Más Grande):** Mide cuándo se vuelve completamente visible el elemento más grande de la pantalla (generalmente una imagen destacada o un gran bloque de texto). Es el indicador principal de que la página "ya cargó" visualmente. _Ideal: menos de 2.5 segundos._
    
- **TBT (Total Blocking Time - Tiempo Total de Bloqueo):** Mide la cantidad total de tiempo que la página web está "congelada" (el hilo principal del navegador está bloqueado procesando código pesado, usualmente JavaScript) y no puede responder a las interacciones del usuario, como clics o toques en la pantalla. _Ideal: menos de 200 milisegundos._
    
- **CLS (Cumulative Layout Shift - Cambio Acumulativo de Diseño):** Mide la estabilidad visual de la página. Evalúa cuánto "saltan" o se mueven los elementos de la página de forma inesperada mientras se carga el contenido (por ejemplo, cuando vas a hacer clic en un botón y una imagen de carga tardía lo empuja hacia abajo). _Ideal: una puntuación menor a 0.1._
    

### Lighthouse en el Ciclo DevOps (Lighthouse CI)

En un entorno moderno de desarrollo de software, auditar el rendimiento manualmente antes de un lanzamiento es ineficiente. Aquí entra **Lighthouse CI (LHCI)**.

LHCI permite integrar estas auditorías directamente en un pipeline de automatización (como GitHub Actions o GitLab CI).

- **Prevención de regresiones:** Puedes configurar el pipeline para que, si un _Pull Request_ reduce el puntaje de rendimiento de Lighthouse por debajo de 90, o si el LCP supera los 2.5 segundos, el despliegue a producción se bloquee automáticamente.
    
- **Trazabilidad:** LHCI guarda un historial de las auditorías a lo largo del tiempo, permitiendo al equipo ver gráficamente cómo el rendimiento del frontend mejora o empeora con cada nuevo _commit_.