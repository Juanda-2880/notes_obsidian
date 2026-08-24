
### Los Roles Principales en un Repositorio

Aunque los nombres pueden variar ligeramente entre GitHub y GitLab, la estructura jerárquica es siempre la misma:

|**Rol**|**Nivel de Acceso**|**Responsabilidad Principal**|
|---|---|---|
|**Owner / Administrador**|Total|Configuración del proyecto, facturación y borrado del repositorio.|
|**Maintainer (Mantenedor)**|Muy Alto|Proteger ramas, aprobar _Pull Requests_ y gestionar CI/CD.|
|**Developer (Desarrollador)**|Medio|Escribir código, crear ramas de _features_ y proponer cambios.|
|**Reporter / Reader (Lector)**|Bajo|Clonar el código, leer _logs_ y auditar. No puede modificar el código.|
|**Guest (Invitado)**|Mínimo|Solo puede ver _issues_ (incidencias) o tableros Kanban.|

### Reglas y Buenas Prácticas según el Rol

Para que un equipo funcione ágilmente sin romper el código, se deben establecer reglas claras de interacción.

#### 1. Reglas para el Developer (Desarrollador)

Este es el rol en el que pasa la mayor parte del equipo. Su objetivo es aportar valor sin poner en riesgo la estabilidad del sistema.

- **Nunca hacer push a `main` o `master`:** Los desarrolladores tienen prohibido empujar código directamente a la rama principal.
    
- **Uso estricto de ramas (Branching):** Todo nuevo trabajo debe hacerse en una rama aislada. Se recomienda usar convenciones de nombres claras, por ejemplo:
    
    - `feature/login-usuario` (Para nuevas funcionalidades)
        
    - `bugfix/error-pago` (Para corrección de errores)
        
    - `hotfix/caida-bd` (Para emergencias en producción)
        
- **Crear Pull Requests (PR) o Merge Requests (MR):** Cuando el desarrollador termina su trabajo, debe abrir una solicitud para que su código sea revisado antes de fusionarse con la rama principal.
    
- **Commits Semánticos:** Escribir mensajes de _commit_ descriptivos (ej. `feat: agregar autenticación con JWT`).
    

#### 2. Reglas para el Maintainer (Líder Técnico / DevOps)

El _Maintainer_ actúa como el guardián de la calidad del código y la infraestructura.

- **Configurar Protección de Ramas (Branch Protection):** El Maintainer debe configurar el repositorio para que nadie (ni siquiera él mismo) pueda hacer _push_ directo a `main`.
    
- **Revisiones de Código (Code Review):** Es el encargado de leer el código del _Developer_ en el _Pull Request_. Debe buscar errores lógicos, vulnerabilidades de seguridad o malas prácticas antes de aprobarlo.
    
- **Gestión de Secretos:** Solo el Maintainer debe tener acceso a configurar las variables de entorno sensibles (contraseñas de bases de datos, tokens de AWS) que usarán los pipelines de CI/CD.
    
- **Ejecutar el _Merge_:** Una vez que el código es aprobado y los pipelines automáticos pasan exitosamente, el Maintainer es quien hace el _merge_ (fusión) hacia la rama principal.
    

#### 3. Reglas para el Reporter / Reader (Lector o QA)

Este rol suele asignarse a perfiles como Product Managers, Auditores de Seguridad o ingenieros de QA (Quality Assurance).

- **Clonar y probar:** Pueden descargar (_pull_) el código a sus máquinas para probarlo localmente.
    
- **Trazabilidad:** Pueden revisar el historial de _commits_ para entender qué cambios se hicieron y cuándo.
    
- **Gestión de incidencias:** Pueden abrir _issues_ (tickets) detallando errores que encontraron en la aplicación, pero no pueden subir código para arreglarlos.
    

### ¿Cómo se automatizan estas reglas? (El Pipeline de CI)

En la cultura DevOps, no confiamos ciegamente en que las personas seguirán las reglas; **hacemos que el sistema las fuerce**.

Para asegurar que los Desarrolladores y Mantenedores cumplan con los estándares, se configuran validaciones automáticas antes de permitir un _Merge_:

1. **Pase de pruebas:** El _Pull Request_ se bloquea automáticamente si las pruebas unitarias (o de integración) fallan.
    
2. **Auditoría de calidad:** Herramientas como SonarQube analizan el código subido por el _Developer_. Si la calidad es baja, el botón de _Merge_ se deshabilita para el _Maintainer_.
    
3. **Mínimo de aprobaciones:** Se puede configurar el repositorio para que un código necesite obligatoriamente la aprobación de al menos dos _Maintainers_ diferentes antes de ir a producción.