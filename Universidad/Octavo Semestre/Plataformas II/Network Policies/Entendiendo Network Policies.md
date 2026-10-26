#Universidad #Kubernetes #Policies #PlatsII 

Las reglas de **Ingress** y **Egress** de una Network Policy pueden usar distintos tipos de selectores para identificar los Pods que tienen permitido comunicarse con el objetivo de la política. Los veremos en esta sección.

## Tipos de selectores

### `podSelector`

Como se vio en los ejemplos anteriores, `podSelector` selecciona los Pods que coinciden con un conjunto definido de etiquetas.

```yaml
podSelector:
  matchLabels:
    app: demo
```

### `namespaceSelector`

`namespaceSelector` es similar a `podSelector`, pero selecciona un _namespace_ completo mediante etiquetas. Se incluyen todos los Pods del namespace.

```yaml
namespaceSelector:
  matchLabels:
    app: demo
```

Puedes seleccionar un namespace específico por su nombre haciendo referencia a la etiqueta `kubernetes.io/metadata.name`, que Kubernetes asigna automáticamente:

```yaml
namespaceSelector:
  matchLabels:
    kubernetes.io/metadata.name: demo-namespace
```

### `ipBlock`

Los selectores `ipBlock` se usan para permitir tráfico hacia o desde rangos CIDR de direcciones IP específicos. Están pensados para filtrar tráfico de direcciones IP **externas al clúster**. No son adecuados para controlar el tráfico entre Pods, porque las IP de los Pods son efímeras: cambian cuando un Pod es reemplazado.

```yaml
ipBlock:
  cidr: 10.0.0.0/24
```

---

## Combinación de selectores

Puedes usar varios selectores para crear condiciones complejas en tus políticas. La siguiente política selecciona todos los Pods que están etiquetados como `demo-api` **o** que pertenecen a un namespace etiquetado con `app: demo`:

```yaml
ingress:
  - from:
      - namespaceSelector:
          matchLabels:
            app: demo
      - podSelector:
          matchLabels:
            app: demo-api
```

Este ejemplo representa un **"OR" lógico** (cada selector está en su propio elemento de lista, con su propio guion `-`).

También puedes crear condiciones **"AND"** combinando los selectores dentro del **mismo elemento de lista**:

```yaml
ingress:
  - from:
      - namespaceSelector:
          matchLabels:
            app: demo
        podSelector:
          matchLabels:
            app: demo-api
```

Esta política solo aplica a los Pods que están etiquetados con `app: demo-api` **y** además se encuentran en un namespace etiquetado con `app: demo`.

|Sintaxis|Operador|Significado|
|---|---|---|
|Selectores en elementos de lista separados (`-` por cada uno)|**OR**|Cumple uno **u** otro|
|Selectores en el mismo elemento de lista (un solo `-`)|**AND**|Cumple **ambos** a la vez|

---

## Definir rangos de puertos permitidos

Los ejemplos anteriores permiten que los Pods autorizados se comuniquen usando todo el rango de puertos disponible. Sin embargo, al agregar el campo `ports` a tus reglas de Ingress y Egress, puedes restringir la comunicación únicamente a los puertos que tu aplicación realmente necesita:

```yaml
ingress:
  - from:
      - podSelector:
          matchLabels:
            app: demo
    ports:
      - protocol: TCP
        port: 32000
        endPort: 32100
```

Ahora la comunicación solo se permite en los puertos TCP del rango 32000 al 32100. Puedes omitir el campo `endPort` si solo usas un único puerto.

---

## Ejemplos de políticas por defecto en Kubernetes

A continuación, algunos ejemplos de Network Policies útiles que podrías necesitar en tu clúster.

### Denegar todo el tráfico hacia un Pod

```yaml
apiVersion: networking.k8s.io/v1
kind: NetworkPolicy
metadata:
  name: network-policy
spec:
  podSelector:
    matchLabels:
      app: demo
  policyTypes:
    - Ingress
    - Egress
```

### Denegar todo el tráfico hacia todos los Pods

```yaml
apiVersion: networking.k8s.io/v1
kind: NetworkPolicy
metadata:
  name: network-policy
spec:
  podSelector: {}
  policyTypes:
    - Ingress
    - Egress
```

### Denegar todo el tráfico de entrada (Ingress)

```yaml
apiVersion: networking.k8s.io/v1
kind: NetworkPolicy
metadata:
  name: network-policy
spec:
  podSelector: {}
  policyTypes:
    - Ingress
```

### Denegar todo el tráfico de salida (Egress)

```yaml
apiVersion: networking.k8s.io/v1
kind: NetworkPolicy
metadata:
  name: network-policy
spec:
  podSelector: {}
  policyTypes:
    - Egress
```

### Permitir todo el tráfico hacia un Pod

```yaml
apiVersion: networking.k8s.io/v1
kind: NetworkPolicy
metadata:
  name: network-policy
spec:
  podSelector:
    matchLabels:
      app: demo
  policyTypes:
    - Ingress
    - Egress
  ingress:
    - {}
  egress:
    - {}
```

---

## Buenas prácticas para las Network Policies de Kubernetes

Ahora que has visto cómo usar las Network Policies, estas son algunas buenas prácticas que te darán las mayores garantías de seguridad.

### 1. Asegúrate de que todos los Pods estén cubiertos por una Network Policy

Todos los Pods de un clúster de Kubernetes deberían estar sujetos a Network Policies que limiten sus interacciones de red al conjunto mínimo de destinos de Ingress/Egress que necesitan. No definir Network Policies permite que todos los Pods se comuniquen entre sí, lo cual es un riesgo potencial de seguridad.

### 2. Usa selectores de Ingress/Egress precisos

Mantén tus selectores de Pods, selectores de namespaces y rangos `ipBlock` lo más precisos posible para evitar que seleccionen accidentalmente Pods nuevos en el futuro. Por ejemplo, un selector de namespace no es apropiado si es probable que despliegues Pods adicionales en ese namespace y esos Pods no deberían comunicarse automáticamente con el objetivo de tu Network Policy.

### 3. Define una política de denegación por defecto y luego agrega tus políticas de permiso

Puedes asegurar que tu clúster tenga cobertura completa de Network Policies creando una política por defecto de "denegar todo" (como se muestra en los ejemplos anteriores) y luego agregando políticas de "permitir" específicas que autoricen los flujos de tráfico necesarios. Con este método, los Pods nuevos quedan protegidos frente a una exposición accidental de red, incluso si olvidas crear una Network Policy específica para ellos.

### 4. Revisa tus políticas con regularidad y mantenlas actualizadas

Es probable que los requisitos de tus Network Policies cambien a medida que tu clúster evoluciona con nuevos Pods y namespaces. Debes revisar tus políticas con regularidad y hacer los ajustes necesarios para que sigan siendo apropiadas para tu entorno.

### 5. Prueba tus políticas para comprobar que funcionan como se espera

Una de las dificultades asociadas a las Network Policies es la falta de visibilidad clara sobre si realmente están funcionando. Vale la pena probar las políticas nuevas para asegurarte de que estén bien configuradas. Como en los ejemplos anteriores, puedes crear un Pod nuevo con etiquetas que coincidan con los selectores de tu Network Policy y luego usar comandos como `curl` y `ping` para probar la conectividad disponible dentro del contenedor.

---

## Consideración final

Las Network Policies son solo una parte de tu estrategia de aislamiento en Kubernetes. Deben complementarse con otras protecciones, como [contextos de seguridad de contenedor](https://kubernetes.io/docs/tasks/configure-pod-container/security-context) correctos y [reglas RBAC](https://spacelift.io/blog/kubernetes-rbac) apropiadas que bloqueen el acceso no autorizado de usuarios a tus Pods.