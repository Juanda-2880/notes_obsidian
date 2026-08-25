#Guión

Buenos días, mi nombre es Juan David Pacheco, soy ingeniero CloudOps, y hoy les voy a hablar sobre las estrategias de branching en el control de versiones, específicamente vamos a profundizar en Trunk-Based Development.

---


Antes de entrar en detalle, es importante entender qué es una estrategia de branching. En pocas palabras, es el conjunto de reglas que define cómo un equipo crea, organiza y fusiona ramas en un repositorio. Nos dice cuándo debe surgir una rama nueva, cómo se integra de vuelta al código principal, y quién es responsable de ese proceso.

¿Por qué es importante que un equipo elija una estrategia? Porque sin ella, el trabajo colaborativo se vuelve caótico: aparecen conflictos de código, el historial de cambios se vuelve confuso, y es fácil cometer errores al integrar el trabajo de varias personas. Al definir una estrategia clara, logramos coordinar mejor el desarrollo, mantener estable la rama principal, y facilitar procesos de entrega continua.

---


La primera estrategia que vamos a revisar es Trunk-Based Development. Esta estrategia consiste en que todos los desarrolladores integran sus cambios directamente sobre la rama principal, conocida como main o trunk, de forma frecuente, idealmente al menos una vez al día. A diferencia de otras estrategias, aquí se evitan las ramas de larga duración; si llega a usarse alguna rama, es de muy corta vida, apenas unas horas, y se apoya en el uso de feature flags para poder subir código incompleto a producción sin que afecte a los usuarios.

Como pueden ver en el diagrama, el flujo es simple: los commits se hacen directamente sobre main, y cuando se necesita ocultar una funcionalidad que aún no está lista, se usa un feature flag en lugar de crear una rama separada.

Hablemos de las ventajas y desventajas. Entre las ventajas, esta estrategia permite una integración continua real, lo que reduce significativamente los conflictos de merge, y da un feedback mucho más rápido al equipo, permitiendo despliegues frecuentes. Sin embargo, también tiene desventajas: requiere una alta disciplina por parte del equipo y una cobertura sólida de pruebas automatizadas, porque si no se cuenta con un pipeline de CI/CD robusto, se corre el riesgo de romper la rama principal.

Esta estrategia se recomienda usar en equipos que ya trabajan con integración y despliegue continuo, y en proyectos que buscan hacer releases rápidos y frecuentes. Un ejemplo de esto a gran escala lo vemos en empresas como Google, Facebook y Netflix, que utilizan herramientas como LaunchDarkly o Split.io para el manejo de feature flags.

---


Para que quede más claro cómo funciona esto en la práctica, preparé un ejemplo real. Creé un repositorio de prueba y simulé el flujo de trabajo de Trunk-Based Development, haciendo varios commits pequeños y frecuentes directamente sobre main: primero la estructura inicial, luego una funcionalidad de login, una corrección de bug, y finalmente una nueva funcionalidad de pago protegida detrás de un feature flag.

Como pueden ver en el log de commits, todos están alineados en una sola línea, sin bifurcaciones ni ramas paralelas, lo cual evidencia justamente lo que caracteriza a esta estrategia: integración directa y continua sobre la rama principal. Y en la captura de GitHub podemos confirmar exactamente lo mismo desde la vista del repositorio.

