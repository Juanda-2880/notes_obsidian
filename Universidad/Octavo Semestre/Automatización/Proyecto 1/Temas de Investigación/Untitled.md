# Gestión de cambios y trazabilidad

**Proyecto:** SINTRATEL Docket  
**HU:** #72 — Definir proceso de gestión de cambios y trazabilidad entre commits, HUs y despliegues

## Objetivo

Definir un proceso sencillo para que cada cambio realizado en el proyecto pueda ser identificado, revisado y relacionado con una Historia de Usuario o incidencia.

La idea es que cualquier cambio pueda seguir este flujo:

```text
Issue / HU
   ↓
Rama
   ↓
Commits
   ↓
Pull Request
   ↓
Checks + Revisión
   ↓
Merge a main
   ↓
Despliegue
   ↓
Release
```

---

## 1. Solicitud del cambio

Todo cambio debe comenzar con un **GitHub Issue**.

El Issue debe indicar:

- Qué se necesita hacer.
- Por qué se necesita.
- Criterios de aceptación.
- Responsable.

Ejemplo:

```text
Issue #72
Definir proceso de gestión de cambios y trazabilidad.
```

---

## 2. Creación de la rama

Para trabajar el cambio se crea una rama relacionada con el número del Issue.

Ejemplo:

```text
docs/72-gestion-cambios
```

No se deben hacer cambios directamente sobre `main`.

---

## 3. Commits

Los commits deben ser claros y, cuando sea posible, incluir el número del Issue.

Ejemplo:

```text
docs: agregar proceso de gestión de cambios (#72)
```

Esto permite saber rápidamente a qué Historia de Usuario pertenece un cambio.

---

## 4. Pull Request

Cuando el cambio esté terminado se crea un **Pull Request hacia `main`**.

El Pull Request debe indicar qué Issue resuelve.

Ejemplo:

```text
Closes #72
```

Antes de aprobar el PR se debe verificar:

- Que el cambio cumple los criterios de aceptación.
- Que los checks automáticos pasen.
- Que otro integrante del equipo revise el cambio.

---

## 5. Aprobación

Un cambio puede pasar a `main` cuando:

- Los checks de GitHub Actions sean exitosos.
- Las pruebas pasen.
- El análisis de calidad, si aplica, sea exitoso.
- Al menos otro integrante apruebe el Pull Request.

Flujo:

```text
PR
 ↓
Checks
 ↓
Revisión
 ↓
Aprobación
 ↓
Merge a main
```

---

## 6. Despliegue

Después del merge, la pipeline de GitHub Actions se encarga del despliegue.

El proyecto maneja los ambientes:

```text
DEV → STAGING → PROD
```

El paso a producción debe tener aprobación manual.

Cada despliegue debe guardar como mínimo:

- Versión.
- Commit.
- Fecha.
- Ambiente.

---

## 7. Trazabilidad

Para cada cambio debe ser posible seguir la relación completa:

```text
HU #72
 ↓
Rama
 ↓
Commit
 ↓
Pull Request
 ↓
main
 ↓
Pipeline
 ↓
Versión
 ↓
Despliegue
```

Ejemplo:

```text
HU #72
 ↓
docs/72-gestion-cambios
 ↓
Commit abc123
 ↓
PR #85
 ↓
Release v1.2.0
 ↓
Producción
```

De esta forma, si se revisa una versión desplegada, es posible saber qué Historias de Usuario incluye.

---

## 8. Release Notes y rollback

Cada versión liberada debe tener un registro sencillo de los cambios incluidos.

Ejemplo:

```text
Release v1.2.0

Cambios:
- #72 Gestión de cambios y trazabilidad.
- #65 Configuración de pruebas.
- #61 Monitoreo con Prometheus.
```

También debe mantenerse identificada la versión anterior para poder hacer rollback si aparece un problema.

Ejemplo:

```text
Versión actual: v1.2.0
Rollback: v1.1.0
```

---

# Cumplimiento de la HU #72

### Criterio 1
**El proceso define cómo se solicita, revisa y aprueba un cambio.**

```text
Issue → PR → Checks → Revisión → Aprobación
```

### Criterio 2
**Cada PR referencia la historia de usuario o incidencia.**

Ejemplo:

```text
Closes #72
```

### Criterio 3
**Es posible saber qué HUs incluye cada despliegue.**

Esto se logra relacionando:

```text
Issue → PR → Commit → Release → Despliegue
```

Así, cada versión desplegada puede relacionarse con las Historias de Usuario que contiene.
