#Universidad #Kubernetes #Policies #PlatsII 

## 1. Fundamentos y Conceptos Clave

Las **NetworkPolicies** son recursos estándar de Kubernetes (`networking.k8s.io/v1`) que permiten controlar el flujo de tráfico de red a nivel de **Capa 3 (IP)** y **Capa 4 (TCP, UDP, SCTP)** del modelo OSI. Permiten especificar reglas declarativas de tráfico tanto dentro del clúster (comunicación inter-pod) como con redes externas.

Por defecto, la red de Kubernetes sigue un modelo de **red plana (flat network, any-to-any)**: cualquier Pod puede comunicarse libremente con cualquier otro Pod o destino sin restricciones. Las Network Policies permiten cambiar este comportamiento hacia un modelo de **confianza cero (Zero Trust)** mediante listas de permisos (_allow-lists_).

### Entidades de Comunicación

Para evitar sobrecargar términos de capas superiores (como _Endpoints_ o _Services_), Kubernetes define tres identificadores de entidades con las que un Pod puede interactuar:

1. **Otros Pods permitidos:** En el mismo o en diferentes _namespaces_ (mediante selectores de etiquetas).
    - _Excepción:_ Un Pod nunca puede bloquear el tráfico hacia sí mismo ni a su propia interfaz de _loopback_ (`127.0.0.1`).
2. **Namespaces:** Conjunto completo de Pods que residen dentro de uno o varios espacios de nombres seleccionados.
3. **Bloques CIDR (`ipBlock`):** Rangos de direcciones IP (típicamente externas al clúster).
    - _Excepción:_ El tráfico entre el Pod y el nodo de Kubernetes donde se ejecuta siempre está permitido por la infraestructura subyacente para asegurar chequeos de salud (_probes_) y gestión de kubelet.

---

## 2. Prerrequisitos de Red y Plugins CNI

La API de Kubernetes define el recurso `NetworkPolicy`, pero **el plano de control de Kubernetes no implementa el filtrado de paquetes por sí mismo**.

Para que las políticas sean efectivas, el clúster debe contar con un plugin de red compatible con la especificación **CNI (Container Network Interface)** que soporte el filtrado de Network Policies (mediante `iptables`, `ipset`, `NFTables` o `eBPF`):

- **Plugins Open Source populares:** Calico, Cilium, Antrea, Kube-router.
- **Plugins sin soporte nativo:** Flannel (por sí solo no aplica políticas; requiere integrarse con Calico a través de Canal).
- **Soluciones gestionadas/cerradas:** Plugins CNI nativos de proveedores de nube (AWS VPC CNI con Network Policy controller activado, Azure CNI powered by Cilium, GKE Datapath V2).

### Componentes de Soporte de Red

#### A. Loopback CNI (`lo`)

Además del plugin CNI principal instalado en los nodos, el _Container Runtime_ (containerd, CRI-O) requiere el plugin CNI `loopback`.

- **Función:** Se encarga de aprovisionar y levantar la interfaz de red local `lo` (`127.0.0.1` / `::1`) dentro del _network namespace_ (sandbox) de cada Pod.
- **¿Por qué es indispensable?**
    1. **Comunicación inter-contenedor:** Permite que contenedores dentro del mismo Pod (por ejemplo, el patrón _Sidecar_ o _Proxy local_ con la aplicación principal) se comuniquen vía `localhost` a máxima velocidad en memoria sin salir a la pila de red del host.
    2. **Probes del Kubelet:** Las sondas `livenessProbe` y `readinessProbe` configuradas contra `localhost` dependen de esta interfaz.
    3. **Seguridad interna:** El tráfico sobre `localhost` está estrictamente aislado dentro del Pod y **nunca es bloqueado** por una NetworkPolicy.

#### B. Soporte para `hostPort`

- Permite vincular un puerto específico del nodo host directamente a un puerto del contenedor dentro del Pod.
- Requiere soporte explícito en el CNI (habitualmente mediante el plugin encadenado `portmap`).
- **Interacción con NetworkPolicies:** El tráfico entrante a través de un `hostPort` proviene directamente de la pila de red del nodo. Dependiendo de la implementación del CNI (y de si realiza DNAT antes o después del filtrado), este tráfico puede ser interpretado como tráfico proveniente del nodo o eludir ciertas reglas de `ingress`.

#### C. Soporte para Traffic Shaping (Modelado de Tráfico)

- Permite limitar el ancho de banda (_rate limiting_) de entrada y salida asignado a un Pod mediante anotaciones en el manifiesto (`kubernetes.io/ingress-bandwidth` y `kubernetes.io/egress-bandwidth`).
- Requiere que el CNI incluya y habilite el plugin `bandwidth` (el cual utiliza las colas `tc` — _Traffic Control_ de Linux en las interfaces virtuales `veth`).
- Controla la congestión y previene el problema del vecino ruidoso (_noisy neighbor_) a nivel de consumo de red, complementando la seguridad de las NetworkPolicies.

---

## 3. Modelo de Aislamiento de Pods (Ingress y Egress)

Un Pod puede ser aislado de forma independiente en dos direcciones:

- **Ingress (Entrada):** Conexiones dirigidas hacia el Pod.
- **Egress (Salida):** Conexiones iniciadas desde el Pod hacia otros destinos.

```text
[Origen]  ========= Tráfico / Petición ========>  [Destino]
   │                                                  │
(Egress Policy)                                (Ingress Policy)
   │                                                  │
   ▼                                                  ▼
¿Permite salir a Destino?                ¿Permite entrar desde Origen?
```

### Reglas Fundamentales del Aislamiento

1. **Estado por Defecto (No Aislado):**
    
    - Si ninguna NetworkPolicy selecciona a un Pod, dicho Pod está **no aislado** (_non-isolated_): acepta todo el tráfico entrante y puede iniciar cualquier conexión saliente.
2. **Activación del Aislamiento:**
    
    - Un Pod pasa a estar **aislado para Ingress** si al menos una NetworkPolicy lo selecciona en su `podSelector` y contiene `"Ingress"` en su lista `policyTypes`.
    - Un Pod pasa a estar **aislado para Egress** si al menos una NetworkPolicy lo selecciona en su `podSelector` y contiene `"Egress"` en su lista `policyTypes`.
3. **Lista Blanca (Allow-List) Aditiva:**
    
    - En cuanto un Pod queda aislado, **se deniega todo el tráfico no explícito**. Solo se permiten las conexiones que coincidan con alguna de las reglas especificadas.
    - **Las políticas son aditivas (unión lógica / OR):** Si múltiples NetworkPolicies aplican sobre el mismo Pod, el tráfico permitido es la suma de lo que autoriza cada una de ellas. Las reglas **no se contradicen ni entran en conflicto**; no existe precedencia de orden de evaluación.
4. **Principio de Doble Validación:**
    
    - Para que una conexión entre el Pod A (origen) y el Pod B (destino) sea exitosa:
        - La política de **Egress** del Pod A debe autorizar el envío hacia B.
        - La política de **Ingress** del Pod B debe autorizar la recepción desde A.
    - Si cualquiera de los dos extremos bloquea la conexión, esta se descarta.
5. **Tráfico de Respuesta (Conexiones con Estado / Stateful):**
    
    - El filtrado opera con seguimiento de conexiones (_connection tracking_). Si una conexión saliente está permitida por Egress, el tráfico de respuesta entrante correspondiente a esa sesión se permite automáticamente. Lo mismo aplica en sentido inverso para Ingress.

---

## 4. Estructura de un Manifiesto NetworkPolicy

Campos obligatorios de la API de Kubernetes:

- `apiVersion`: `networking.k8s.io/v1`
- `kind`: `NetworkPolicy`
- `metadata`: Nombre y `namespace` donde opera la política.
- `spec`: Reglas y selectores de la política.

```yaml
apiVersion: networking.k8s.io/v1
kind: NetworkPolicy
metadata:
  name: ejemplo-red
  namespace: default
spec:
  # 1. Selector de Pods objetivo (a quiénes aplica esta política)
  podSelector:
    matchLabels:
      role: db

  # 2. Tipos de aislamiento a activar
  policyTypes:
    - Ingress
    - Egress

  # 3. Reglas de entrada permitidas
  ingress:
    - from:
        - podSelector:
            matchLabels:
              role: frontend
      ports:
        - protocol: TCP
          port: 6379

  # 4. Reglas de salida permitidas
  egress:
    - to:
        - ipBlock:
            cidr: 10.0.0.0/24
      ports:
        - protocol: TCP
          port: 5978
```

### Desglose de Campos

- `podSelector`: Agrupación de Pods en el namespace actual sobre los cuales recae el aislamiento. Un `podSelector: {}` vacío selecciona **todos** los Pods del namespace.
- `policyTypes`: Indica qué direcciones se aíslan. Si no se especifica explícitamente, Kubernetes asume `Ingress` por defecto y añade `Egress` si se incluye alguna regla en la sección `egress`. Se recomienda definirlo siempre de forma explícita.
- `ingress`: Lista de reglas permitidas. Cada regla define orígenes (`from`) y puertos (`ports`).
- `egress`: Lista de reglas permitidas. Cada regla define destinos (`to`) y puertos (`ports`).

---

## 5. Sintaxis y Comportamiento de Selectores (`from` / `to`)

Dentro de un bloque `from` (Ingress) o `to` (Egress), se pueden definir combinaciones de selectores. La sintaxis en YAML determina si la evaluación es una conjunción (**AND**) o una disyunción (**OR**).

### A. Operador AND (Mismo elemento de lista)

Cuando `namespaceSelector` y `podSelector` están bajo el **mismo elemento de lista** (un solo guion `-`):

```yaml
ingress:
  - from:
      - namespaceSelector:
          matchLabels:
            user: alice
        podSelector:
          matchLabels:
            role: client
```

- **Comportamiento:** Permite tráfico únicamente desde Pods con la etiqueta `role: client` que además residan en Namespaces con la etiqueta `user: alice`.

### B. Operador OR (Elementos de lista independientes)

Cuando los selectores se definen en **guiones independientes** dentro del array:

```yaml
ingress:
  - from:
      - namespaceSelector:
          matchLabels:
            user: alice
      - podSelector:
          matchLabels:
            role: client
```

- **Comportamiento:** Permite tráfico desde cualquier Pod en Namespaces con `user: alice` **O** desde Pods en el mismo namespace local que tengan `role: client`.

### C. Selector de IP (`ipBlock`)

Permite definir rangos en notación CIDR y excepciones mediante `except`:

```yaml
ingress:
  - from:
      - ipBlock:
          cidr: 172.17.0.0/16
          except:
            - 172.17.1.0/24
```

- **Uso:** Exclusivo para IPs externas al clúster. **No debe usarse para IPs de Pods**, ya que estas son dinámicas, efímeras y reciclables.
- **Impacto con Services y LoadBalancers:** Si el tráfico atraviesa un LoadBalancer, Ingress Controller o NodePort con Source NAT (SNAT), la IP de origen que ve el Pod puede ser la del nodo y no la IP del cliente real, afectando la evaluación del `ipBlock`.

### D. Selección de Namespaces por Nombre

Dado que `NetworkPolicy` no cuenta con un campo nativo para apuntar directamente al nombre del namespace mediante texto plano, se debe emplear la etiqueta estandarizada e inmutable que el plano de control asigna a todo namespace:

```yaml
namespaceSelector:
  matchLabels:
    kubernetes.io/metadata.name: frontend
```

---

## 6. Políticas Predeterminadas Comunes (Patrones Base)

### 1. Denegación Total por Defecto de Entrada (Default Deny Ingress)

Aísla todos los pods del namespace para entrada sin definir reglas de permiso.

```yaml
apiVersion: networking.k8s.io/v1
kind: NetworkPolicy
metadata:
  name: default-deny-ingress
  namespace: produccion
spec:
  podSelector: {}
  policyTypes:
    - Ingress
```

### 2. Permitir Todo el Tráfico de Entrada (Allow All Ingress)

Permite cualquier conexión entrante hacia cualquier pod del namespace.

```yaml
apiVersion: networking.k8s.io/v1
kind: NetworkPolicy
metadata:
  name: allow-all-ingress
  namespace: produccion
spec:
  podSelector: {}
  ingress:
    - {}
  policyTypes:
    - Ingress
```

### 3. Denegación Total por Defecto de Salida (Default Deny Egress)

Aísla todos los pods del namespace para salida sin autorizar ningún destino.

```yaml
apiVersion: networking.k8s.io/v1
kind: NetworkPolicy
metadata:
  name: default-deny-egress
  namespace: produccion
spec:
  podSelector: {}
  policyTypes:
    - Egress
```

> **Advertencia crítica sobre DNS:** Bloquear todo el tráfico de salida impide que los Pods se comuniquen con el servicio de CoreDNS (`kube-dns` en `kube-system`). Para resolver nombres de dominio, se debe añadir una política que autorice la salida hacia los puertos 53 UDP y TCP:

```yaml
apiVersion: networking.k8s.io/v1
kind: NetworkPolicy
metadata:
  name: allow-dns-egress
  namespace: produccion
spec:
  podSelector: {}
  policyTypes:
    - Egress
  egress:
    - to:
        - namespaceSelector:
            matchLabels:
              kubernetes.io/metadata.name: kube-system
          podSelector:
            matchLabels:
              k8s-app: kube-dns
      ports:
        - protocol: UDP
          port: 53
        - protocol: TCP
          port: 53
```

### 4. Permitir Todo el Tráfico de Salida (Allow All Egress)

```yaml
apiVersion: networking.k8s.io/v1
kind: NetworkPolicy
metadata:
  name: allow-all-egress
  namespace: produccion
spec:
  podSelector: {}
  egress:
    - {}
  policyTypes:
    - Egress
```

### 5. Denegación Total de Entrada y Salida (Default Deny All)

Aislamiento completo en ambas direcciones.

```yaml
apiVersion: networking.k8s.io/v1
kind: NetworkPolicy
metadata:
  name: default-deny-all
  namespace: produccion
spec:
  podSelector: {}
  policyTypes:
    - Ingress
    - Egress
```

---

## 7. Funcionalidades Avanzadas de Filtrado

### Rangos de Puertos con `endPort` (Estable desde v1.25)

Permite especificar un rango continuo de puertos en lugar de definir una regla por cada puerto individual.

```yaml
apiVersion: networking.k8s.io/v1
kind: NetworkPolicy
metadata:
  name: multiport-egress
  namespace: default
spec:
  podSelector:
    matchLabels:
      role: db
  policyTypes:
    - Egress
  egress:
    - to:
        - ipBlock:
            cidr: 10.0.0.0/24
      ports:
        - protocol: TCP
          port: 32000
          endPort: 32768
```

**Restricciones:**

- `endPort` debe ser mayor o igual que `port`.
- `endPort` solo puede definirse si `port` está especificado numéricamente (no con nombres de puertos).
- El CNI subyacente debe soportar el campo `endPort`; de lo contrario, solo se filtrará el puerto base (`port`).

### Protocolos Capa 4

- Las reglas garantizan el control sobre **TCP**, **UDP** y opcionalmente **SCTP** (si el CNI lo soporta).
- Protocolos como **ICMP (ping)** o **ARP** no están estandarizados en la API de NetworkPolicies. Su comportamiento (si son bloqueados o transmitidos) queda a criterio de la implementación del CNI.

---

## 8. Ciclo de Vida del Pod y Sincronización de Políticas

1. **Naturaleza Distribuida:** Cuando se crea o actualiza un objeto `NetworkPolicy`, los agentes locales del CNI en cada nodo (por ejemplo, `calico-node` o `cilium-agent`) procesan la política de forma asíncrona.
2. **Arranque de Nuevos Pods:**
    - Un CNI conforme debe asegurar que las reglas de aislamiento se apliquen **antes o en el mismo instante** en que se inicia cualquier contenedor del Pod (incluyendo `initContainers` y `sidecars`).
    - Si las reglas de aislamiento se aplican antes de que se sincronicen las reglas de permiso (_allow_), el Pod podría experimentar una breve pérdida de conectividad durante los primeros segundos de vida.
3. **Resiliencia en Aplicaciones:** Debido a la convergencia eventual de los agentes de red, los contenedores deben implementar reintentos de conexión (_backoff/retries_) o apoyarse en _init containers_ para esperar a que los servicios dependientes respondan.

---

## 9. Interacción con `hostNetwork: true`

Cuando un Pod se configura con `spec.hostNetwork: true`, comparte la pila de red y la dirección IP del nodo que lo hospeda.

- **Comportamiento general:** No está estrictamente definido en la especificación de Kubernetes.
- **Comportamiento estándar en la mayoría de CNIs:** El tráfico de un Pod con `hostNetwork` es indistinguible del tráfico del propio nodo. Por ello, estos Pods son ignorados por `podSelector` y su tráfico solo puede gestionarse mediante reglas de `ipBlock` orientadas a la IP del host.
- **CNIs avanzados (basados en eBPF):** Algunos plugins avanzados son capaces de inspeccionar sockets locales y vincular la actividad de red al Pod individual aun en modo `hostNetwork`.

---

## 10. Limitaciones del API Estándar de NetworkPolicies

Actualmente, las siguientes capacidades **no** están soportadas de forma nativa por el recurso estándar de Kubernetes:

- **Reglas de Denegación Explícita (`Deny`):** El modelo nativo funciona exclusivamente como lista de permisos (_allow-list_) sobre un aislamiento implícito.
- **Inspección de Capa 7:** No filtra por encabezados HTTP, rutas URL, verbos REST o nombres de dominio/FQDN.
- **Terminación y Políticas TLS:** No inspecciona ni condiciona flujos cifrados en Capa 7.
- **Políticas a nivel de Clúster (Cluster-wide):** Las NetworkPolicies son recursos con alcance de _Namespace_ (no globales).
- **Selección de Servicios por Nombre:** No es posible apuntar directamente a un Service (por ejemplo, `my-service.default.svc.cluster.local`); se deben seleccionar las etiquetas de los Pods detrás del servicio.
- **Bloqueo de Loopback / Host Local:** Los Pods no pueden bloquear el acceso a su propio `127.0.0.1` ni la comunicación básica con el Kubelet del nodo.
- **Registro de Auditoría (Logging):** No existe un mecanismo nativo en el manifiesto para registrar conexiones aceptadas o bloqueadas (para esto se requiere herramientas del CNI como _Hubble_ en Cilium o _Felix log_ en Calico).

_(Nota: Gran parte de estas limitaciones se resuelven mediante el nuevo estándar **AdminNetworkPolicy** de Kubernetes SIG-Network o usando CRDs propios del CNI como `CiliumNetworkPolicy` o `GlobalNetworkPolicy` de Calico)._

---

## 11. Impacto en Conexiones de Red Existentes

Cuando se modifica una NetworkPolicy, se eliminan etiquetas o se crea una nueva política que restringe un flujo previamente autorizado:

1. **Definido por la Implementación del CNI:** El estándar de Kubernetes no impone si una nueva política debe cortar de inmediato una conexión TCP activa o si solo debe aplicar a las conexiones nuevas.
2. **Gestión de la Tabla de Conexiones (`conntrack`):**
    - Muchos plugins de red dejan abiertas las conexiones TCP existentes hasta que expiran por inactividad o terminan por señal de los extremos, ya que el estado se encuentra activo en la tabla `conntrack`.
    - Otros plugins (especialmente implementaciones modernas en eBPF) purgan activamente las entradas de seguimiento de conexiones y envían paquetes `TCP RST` para terminar los sockets inmediatamente.
3. **Recomendación Operativa:** Se recomienda planificar los cambios en políticas de red, etiquetas de Pods y selectores de Namespaces durante ventanas de mantenimiento o validarlos previamente en entornos de prueba (_Staging_), monitoreando posibles caídas abruptas de sesiones activas.