#IA #Agentes #Harness 

La Palabra "Harness" muchas veces se refiere como a "un archivo de Prompt" y eso no es un Harness. Es como cuando se abre un restaurante con solo ingredientes, sin estufa, sin cuchillos, sin recetas. Eso no es un restauranete, es un refrigerador

El Harness consiste en cinco Subsitemas, cada uno con resposabilidades claras y criterios de evaluación

### Conceptos Clave

- El repositorio es la única fuente de verdad: Cualquier cosa que el agente no pueda ver, para todos los efectos prácticos, no existe. Todo el contexto necesario debe de vivir en el repositorio,  a través de archivos estructurados y una organización clara de directorios
- Da un mapa, no un manual: El Agents.md debería ser una página de directorio, no una enciclopedia. Alrededor de 100 líneas es suficiente. Si no cabe, se debe dividir en el directorio `docs/` y que el agente lo lea bajo demanda
- Restringe, no microgestiones: Un buen Harness usa reglas ejecutables para restringir al agente, en lugar de enumerar instrucciones una por una
- Elimina componentes uno a la vez: Para cuantificar la contribución marginal de cada componente del harness, eliminalos uno a la vez y observa cual eliminacion causa la mayor caida de rendimiento.

### El modelo de Harness de Cinco Subsistemas 

![[Lección 02-1793917104091.webp]]

**Subsistema de Instrucciones:** Crea un `AGENTS.md` que contenga un resumen y propósito del proyecto (una frase), el Stack Tecnologico y Versiones, comandos de primera ejecución (make setup, make test), restricciones fija innegociables ("Todas las APIs deben usar OAuth 2.0") y enlaces a documentación más detallada 

**Subsistema de Herramientas:** Asegurate de que el agente tenga acceso suficiente a herramientas. No descatives el shell por "seguridad"

**Subsistema de Entorno:** Haz que el estado del entorno se describa a sí mismo.  Usa `pyproject.toml` o `package.json` para bloquear dependencias, `.nvmrc` o `.python-version` para versiones de runtime, Docker o devcontainers para reproducibilidad.


**Subsistema de Estado:** Las tareas largas necesitan seguimiento del progreso. Usa un archivo simple `PROGRESS.md` que registre: qué está hecho, qué está en progreso, qué está bloqueado. Se actualiza antes de que termine cada sesión y se lee cuando comience la siguiente 

**Subsistema de Retroalimentación:** Lista explicitamente los comandos de verificación en `AGENTS.md`

Que falte cualquier subsistema es como que falte un área funcional en una cocina, todavía puedes cocinar, pero siempre será incómodo.

