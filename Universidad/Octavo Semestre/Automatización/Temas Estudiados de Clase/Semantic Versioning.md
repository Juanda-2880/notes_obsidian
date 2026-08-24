#SemVer #Universidad 

### ¿Qué es el Versionado Semántico (SemVer)?

Es la convención de nomenclatura estándar más popular en la industria para el control de versiones de software. Proporciona un sistema de etiquetas consistente y con significado (semántico) para rastrear la evolución de un proyecto.

Su objetivo principal es resolver el "infierno de las dependencias" (_dependency hell_), permitiendo a los desarrolladores y a los sistemas automatizados saber exactamente qué tan seguro es actualizar una aplicación o librería con solo mirar los números de la versión.

### La Estructura: X.Y.Z (Major.Minor.Patch)

SemVer establece que una versión se compone de tres números separados por puntos (ejemplo: `1.0.1`, `2.1.0`). Cada número tiene un significado estricto:

- **MAJOR (Versión Mayor - X.y.z): Cambios que rompen la compatibilidad.**
    
    Este número cambia (se incrementa) cuando se introducen modificaciones incompatibles con las versiones anteriores (_breaking changes_). Esto implica que, si otro sistema dependía de tu código, la actualización podría causarle daños o fallos, por lo que requerirá adaptación. Al subir el MAJOR, los números MINOR y PATCH se reinician a cero (ej. de `1.4.2` pasa a `2.0.0`).
    
- **MINOR (Versión Menor - x.Y.z): Nuevas características.**
    
    Este número representa la adición de una funcionalidad o comportamiento nuevo, pero de manera **compatible hacia atrás** (_backwards-compatible_). Es decir, la actualización es segura y no romperá lo que ya funcionaba. Al incrementar el MINOR, el número PATCH se reinicia a cero (ej. de `2.1.4` pasa a `2.2.0`).
    
- **PATCH (Parche - x.y.Z): Corrección de errores.**
    
    El incremento de este número representa exclusivamente una reparación en el código (_bugfix_). Se realiza para solucionar errores y asegurar la estabilidad o seguridad del software, sin añadir nuevas funcionalidades. (ej. de `2.2.0` pasa a `2.2.1`).
    

> **Regla de oro inicial:** La versión `0.y.z` se usa para la fase de desarrollo inicial. Indica que el software aún es inestable, la API pública puede cambiar en cualquier momento y no debe usarse en producción. La versión `1.0.0` define la primera versión pública y estable.

### Estados de Estabilidad (Etiquetas de Pre-lanzamiento)

Además de los tres números, SemVer permite añadir un guion seguido de etiquetas para indicar el estado de madurez o estabilidad de una versión antes de su lanzamiento oficial (ejemplo: `2.0.0-beta.1`).

El flujo de maduración suele seguir este orden:

1. **Dev (Development):** Hace referencia a un código que aún se encuentra en estado crudo de desarrollo diario.
    
2. **Alpha (`-alpha`):** Es la primera etapa de prueba. El código se prepara para ser un _release_, pero puede ser muy inestable, tener errores conocidos o funcionalidades incompletas. Suele probarse solo internamente.
    
3. **Beta (`-beta`):** Es una versión mucho más estable y con las características completas (_feature freeze_), pero aún no cumple con la calidad suficiente para producción. Se suele liberar a un grupo de usuarios para que reporten fallos.
    
4. **RC (Release Candidate - `-rc.1`, `-rc.2`):** Es una "Candidata a Lanzamiento". Cuenta con una gran estabilidad y, si no se encuentran errores críticos durante esta fase, esta misma versión exacta se convertirá en la versión final. Pueden existir varias (RC1, RC2) si se descubren fallos de última hora.
    
5. **Stable / Release:** Tiene toda la estabilidad requerida para producción. Simplemente se elimina la etiqueta (ej. `2.0.0`), ya que escribir "estable" sería redundante; si no tiene etiqueta de pre-lanzamiento, se asume que es la versión final y estable.
    

### ¿Por qué es crucial en DevOps y Automatización?

SemVer no es solo para que los humanos lo lean; es el lenguaje que entienden las máquinas:

- **Gestión de Dependencias (NPM, Pip, Maven):** Cuando instalas paquetes (como en el _package.json_ de Node.js), herramientas como NPM usan símbolos basados en SemVer para actualizar de forma segura. Por ejemplo, `^1.2.0` le dice a la máquina: _"Puedes instalar automáticamente cualquier versión nueva, siempre y cuando no cambie el MAJOR (porque se rompería mi código)"_.
    
- **Pipelines CI/CD:** En plataformas como GitHub Actions o GitLab CI, puedes automatizar despliegues basándote en las versiones. Por ejemplo, puedes configurar un _pipeline_ que diga: _"Si se crea un tag en Git que empiece por `v` y no tenga sufijo (ej. `v1.2.0`), despliega automáticamente a Producción. Si tiene el sufijo `-rc` (ej. `v1.2.0-rc.1`), despliega al entorno de QA (Pruebas)"_.