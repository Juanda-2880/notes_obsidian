#Universidad #PlatsII #Auto 

### ¿Qué son las Naming Conventions?

Las convenciones de nomenclatura son un conjunto de reglas y estándares que los equipos de desarrollo acuerdan seguir para nombrar sus variables, funciones, archivos, ramas de Git, servidores y recursos en la nube.

**¿Por qué son tan importantes?**

- **Reducen la carga cognitiva:** Al leer el código o la infraestructura, un desarrollador no tiene que descifrar qué significa cada cosa; el formato del nombre ya le da contexto.
    
- **Facilitan la búsqueda y refactorización:** Es más fácil buscar un patrón estandarizado en un proyecto masivo.
    
- **Permiten la automatización:** Los _pipelines_ de CI/CD (Integración y Despliegue Continuos) suelen depender de que las ramas, etiquetas (_tags_) o variables de entorno tengan un formato específico para ejecutar ciertas tareas.
    

### Los Estándares Principales (Los "Casos")

Dependiendo del lenguaje de programación o la tecnología, se utiliza un formato de capitalización diferente. Estos son los más universales:

|**Formato**|**¿Cómo se escribe?**|**Uso común en la industria**|
|---|---|---|
|**camelCase**|`calcularTotalVentas`|Variables, funciones y métodos (JavaScript, Java, C#). La primera palabra va en minúscula y las siguientes inician con mayúscula.|
|**PascalCase**|`UsuarioControlador`|Nombres de Clases, Interfaces y Componentes visuales (React, Angular). Todas las palabras inician con mayúscula.|
|**snake_case**|`calcular_total_ventas`|Variables y funciones (Python, Ruby), nombres de bases de datos y columnas en SQL. Las palabras se separan con guiones bajos.|
|**kebab-case**|`calcular-total-ventas`|URLs, nombres de archivos, clases CSS, ramas de Git y nombres de repositorios. Las palabras se separan con guiones medios.|
|**UPPER_SNAKE_CASE**|`MAX_CONEXIONES`|Constantes globales y **Variables de Entorno**. Todo en mayúsculas separado por guiones bajos.|

### Naming Conventions aplicadas a DevOps y Cloud

En tu materia y en roles de infraestructura, las convenciones de nombres van más allá del código fuente. Se aplican a los recursos que despliegas y administras:

#### 1. Nomenclatura en la Nube (Infraestructura)

Cuando gestionas múltiples entornos (Desarrollo, QA, Producción) en plataformas cloud, los recursos (como servidores EC2, redes VPC o roles IAM) deben seguir una estructura predecible. Un patrón muy recomendado es:

- **Patrón:** `<proyecto>-<entorno>-<recurso>-<región>`
    
- _Ejemplo para una red:_ `ecommerce-prod-vpc-useast1`
    
- _Ejemplo para un servidor:_ `backend-dev-ec2-appserver`
    
- **Ventaja:** Con solo leer el nombre del servidor, sabes a qué proyecto pertenece, en qué entorno está operando y qué función cumple, lo cual evita desastres como apagar el servidor de producción por accidente.
    

#### 2. Nomenclatura en Git (Branching)

Como vimos en los temas de versionado, los equipos deben estructurar los nombres de sus ramas usando _kebab-case_ y prefijos estandarizados, usualmente combinados con el número del ticket de Jira o Trello:

- `feature/102-agregar-login` (Nueva funcionalidad)
    
- `bugfix/304-corregir-cierre-sesion` (Arreglo de un error no crítico)
    
- `hotfix/501-caida-base-datos` (Arreglo urgente en producción)
    

#### 3. Variables de Entorno y Secretos

Las variables que guardan configuraciones sensibles (credenciales, URLs de bases de datos) en contenedores Docker o pipelines de automatización siempre deben ir en `UPPER_SNAKE_CASE`.

- _Correcto:_ `DB_HOST`, `AWS_ACCESS_KEY_ID`, `DOCKER_REGISTRY_URL`.
    
- _Incorrecto:_ `dbHost`, `ApiKey`.
    

### Buenas Prácticas Generales

1. **Nombres Descriptivos, no Genéricos:** Evita nombres como `data`, `temp`, `info` o `var1`. En su lugar, usa `userData`, `temporaryFileDirectory` o `paymentPayload`.
    
2. **Usa verbos para las funciones:** Las funciones o scripts que ejecutan una acción deben empezar con un verbo. Ej: `getUser()`, `build_docker_image.sh`.
    
3. **Usa booleanos con prefijos:** Las variables que devuelven Verdadero o Falso suelen llevar prefijos como `is`, `has` o `can`. Ej: `isActive`, `hasPermission`.
    
4. **Consistencia antes que perfección:** No importa si a nivel personal prefieres _snake_case_; si el equipo o el framework (como React o Terraform) dicta usar otro formato, debes adaptarte para mantener la consistencia del proyecto.

