#Universidad #Minikube #Calico #Kubernetes #PlatsII 
## 0. Objetivo y escenario

Se creará un clúster local con **Minikube** usando **Calico** como plugin CNI (necesario para que las NetworkPolicies realmente se apliquen; sin un CNI compatible el recurso se crea, pero no filtra nada). Luego se desplegarán tres Pods de **nginx** y se comprobará cómo una NetworkPolicy aísla a `pod2` y cómo se le va abriendo el acceso solo para `pod1`.

|Pod|Etiqueta|Rol en la prueba|
|---|---|---|
|`pod1`|`app=pod1`|Cliente autorizado (origen del `curl`)|
|`pod2`|`app=pod2`|Pod destino, el que será protegido por la política|
|`pod3`|_(sin etiqueta)_|Cliente **no** autorizado (debe ser bloqueado)|

**Resultado esperado:**

1. Sin política → `pod1` llega a `pod2`.
2. Política que aísla a `pod2` sin reglas → `pod1` **ya no** llega.
3. Política con reglas que permiten a `pod1` → `pod1` llega de nuevo.
4. `pod3` (sin permiso) → **no** llega a `pod2`.

---

## 1. Iniciar Minikube con Calico

```bash
minikube start --cni calico
```

**Explicación:**

- `minikube start`: crea e inicia un clúster de Kubernetes local de un solo nodo (en una VM o contenedor Docker, según el driver).
- `--cni calico`: indica a Minikube que instale **Calico** como plugin de red (CNI) en lugar del CNI por defecto. Calico es el que se encarga de interpretar y aplicar las NetworkPolicies mediante reglas de filtrado en el nodo (`iptables`/eBPF).

> **Nota:** Si ya existía un clúster de Minikube sin Calico, hay que eliminarlo primero con `minikube delete`, porque el CNI se define al crear el clúster.

![[Crear Network Policies con Calico-1793039255364.webp]]

---

## 2. Crear los Pods de prueba

```bash
kubectl run pod# --image nginx:latest -l app=pod#
```

> Reemplazar `#` por el número del Pod. En este laboratorio se ejecuta para `pod1` y `pod2`:

```bash
kubectl run pod1 --image nginx:latest -l app=pod1
kubectl run pod2 --image nginx:latest -l app=pod2
```

**Explicación:**

- `kubectl run`: crea un Pod (un solo contenedor) de forma imperativa, sin necesidad de un archivo YAML.
- `pod1` / `pod2`: nombre del Pod.
- `--image nginx:latest`: imagen del contenedor. Se usa **nginx** porque levanta un servidor web en el puerto 80 y además la imagen incluye `curl`, útil para las pruebas de conectividad.
- `-l app=pod1` (`--labels`): asigna la etiqueta `app=pod1` al Pod. Estas etiquetas son las que luego usarán los `podSelector` de la NetworkPolicy para seleccionar a qué Pods aplica y desde cuáles se permite el tráfico.

![[Crear Network Policies con Calico-1793039332755.webp]]

---

## 3. Verificar el estado de los Pods

```bash
kubectl get pods
```

**Explicación:** lista los Pods del namespace actual (`default`) con su estado (`STATUS`), cuántos contenedores están listos (`READY`), reinicios y antigüedad. Se espera ver ambos Pods en estado `Running`.

```bash
kubectl get pods -o wide
```

**Explicación:** igual que el anterior, pero con columnas adicionales: **IP del Pod**, **nodo** donde corre, etc. Es necesario para obtener la **IP de `pod2`** (en este caso `10.244.120.68`), que se usará como destino en los `curl`.

> La IP de un Pod es **dinámica**: cambia si el Pod se recrea. En tu entorno la IP puede ser distinta; usa la que aparezca en la columna `IP`.

![[Crear Network Policies con Calico-1793039469759.webp]]

---

## 4. Probar la conectividad inicial (sin políticas)

```bash
kubectl exec -it pod1 -- curl 10.244.120.68 --max-time 1
```

**Explicación:**

- `kubectl exec`: ejecuta un comando dentro de un contenedor de un Pod en ejecución.
- `-it`: combinación de `-i` (mantiene la entrada estándar abierta) y `-t` (asigna una terminal), para interactuar con el comando.
- `pod1`: Pod dentro del cual se ejecuta el comando (será el **origen** de la petición).
- `--`: separa las opciones de `kubectl` del comando que se ejecutará dentro del Pod.
- `curl 10.244.120.68`: realiza una petición HTTP a la IP de `pod2` (puerto 80 por defecto).
- `--max-time 1`: limita la operación a **1 segundo**. Si no hay respuesta, `curl` termina con error de _timeout_. Esto es clave para distinguir rápidamente un tráfico bloqueado (se queda esperando) de uno permitido.

**Resultado esperado:** se imprime la página de bienvenida de nginx (`Welcome to nginx!`). Esto confirma que, **sin ninguna NetworkPolicy**, la red es plana (_any-to-any_) y `pod1` puede hablar con `pod2`.

![[Crear Network Policies con Calico-1793039585226.webp]]

---

## 5. Crear la primera NetworkPolicy (aislamiento total de `pod2`)

Crear un nuevo archivo llamado `np.yaml` y pegarle el siguiente contenido:

![[Crear Network Policies con Calico-1793039910565.webp]]

**Contenido del archivo:**

```yaml
apiVersion: networking.k8s.io/v1
kind: NetworkPolicy
metadata:
  name: network-policy
  namespace: default
spec:
  podSelector:
    matchLabels:
      app: pod2
  policyTypes:
    - Ingress
    - Egress
```

**Explicación de los campos:**

- `apiVersion: networking.k8s.io/v1` / `kind: NetworkPolicy`: tipo de recurso de Kubernetes que se está creando.
- `metadata.name`: nombre de la política (`network-policy`).
- `metadata.namespace`: namespace donde se aplica (`default`). Las NetworkPolicies son recursos con alcance de _namespace_.
- `spec.podSelector.matchLabels: app: pod2`: la política **selecciona** a los Pods con la etiqueta `app=pod2`, es decir, solo afecta a `pod2`.
- `spec.policyTypes: [Ingress, Egress]`: activa el aislamiento en **ambas direcciones** (entrada y salida) para los Pods seleccionados.
- **No hay secciones `ingress` ni `egress`**: al no definir ninguna regla de permiso, no se autoriza ningún tráfico. Por el modelo de _allow-list_, `pod2` queda **completamente aislado** (deniega todo el tráfico de entrada y de salida).

### Aplicar la política

```bash
kubectl apply -f .\np.yaml
```

**Explicación:**

- `kubectl apply`: crea (o actualiza) el recurso a partir de un manifiesto de forma **declarativa**.
- `-f`: indica el archivo que contiene el manifiesto.
- `.\np.yaml`: ruta relativa del archivo (sintaxis de Windows/PowerShell; en Linux/macOS sería `./np.yaml`).

**Salida esperada:** `networkpolicy.networking.k8s.io/network-policy created`

![[Crear Network Policies con Calico-1793040097112.webp]]

### Verificar el bloqueo

```bash
kubectl exec -it pod1 -- curl 10.244.120.68 --max-time 1
```

**Resultado esperado:** la petición **falla por timeout** (por ejemplo `curl: (28) Connection timed out after 1001 milliseconds`). `pod2` está aislado para _Ingress_ y ninguna regla autoriza la entrada desde `pod1`, así que Calico descarta los paquetes.

![[Crear Network Policies con Calico-1793040207746.webp]]

---

## 6. Editar la política para permitir tráfico solo desde/hacia `pod1`

Se edita `np.yaml` con el siguiente contenido:

![[Crear Network Policies con Calico-1793040459205.webp]]

**Contenido:**

```yaml
apiVersion: networking.k8s.io/v1
kind: NetworkPolicy
metadata:
  name: network-policy
  namespace: default
spec:
  podSelector:
    matchLabels:
      app: pod2
  policyTypes:
    - Ingress
    - Egress
  ingress:
    - from:
        - podSelector:
            matchLabels:
              app: pod1
  egress:
    - to:
        - podSelector:
            matchLabels:
              app: pod1
```

**Explicación de lo nuevo:**

- `ingress.from.podSelector (app: pod1)`: permite que **entre** tráfico hacia `pod2` únicamente si **proviene de Pods con la etiqueta `app=pod1`** (dentro del mismo namespace, ya que no se especifica `namespaceSelector`).
- `egress.to.podSelector (app: pod1)`: permite que `pod2` **inicie** conexiones de salida únicamente hacia Pods con la etiqueta `app=pod1`.
- No se especifica `ports`, por lo que se permiten **todos los puertos** para esos orígenes/destinos.

> **Nota sobre el Egress:** para que `pod1` reciba la respuesta HTTP de `pod2` no haría falta la regla de `egress`, porque el filtrado es _stateful_ (_connection tracking_) y el tráfico de respuesta de una conexión permitida se autoriza automáticamente. La regla de `egress` solo es necesaria si `pod2` debe **iniciar** conexiones hacia `pod1`.

### Aplicar los cambios

```bash
kubectl apply -f .\np.yaml
```

**Salida esperada:** `networkpolicy.networking.k8s.io/network-policy configured` (indica que se **actualizó** la política existente en vez de crear una nueva).

### Volver a verificar

```bash
kubectl exec -it pod1 -- curl 10.244.120.68 --max-time 1
```

**Resultado esperado:** vuelve a mostrarse la página `Welcome to nginx!`. La política ahora autoriza explícitamente a `pod1`.

![[Crear Network Policies con Calico-1793040538345.webp]]

---

## 7. Comprobar que otros Pods siguen bloqueados (`pod3`)

Se crea un tercer Pod, **sin etiqueta `app`**, para comprobar que la política solo deja pasar a `pod1`:

```bash
kubectl run pod3 --image nginx:latest
```

**Explicación:** crea `pod3` con la misma imagen, pero **sin `-l`**. `kubectl run` le asigna por defecto la etiqueta `run=pod3`, que **no coincide** con `app=pod1`, por lo que no cumple la regla de _ingress_ de `pod2`.

```bash
kubectl exec -it pod3 -- curl 10.244.120.68 --max-time 1
```

**Resultado esperado:** la petición **falla por timeout**. Aunque `pod3` está en el mismo namespace, `pod2` solo acepta tráfico de entrada desde Pods con `app=pod1`, así que el tráfico desde `pod3` se descarta.

> **Antes de probar**, confirmar con `kubectl get pods` que `pod3` esté en estado `Running`; de lo contrario el error podría deberse a que el Pod aún no está listo y no a la política.

![[Crear Network Policies con Calico-1793041418639.webp]]

---

## 8. Resumen del flujo

|Paso|Estado de la política|Origen → Destino|Resultado|
|---|---|---|---|
|4|Ninguna|`pod1` → `pod2`|✅ Permitido (red plana por defecto)|
|5|`pod2` aislado, sin reglas|`pod1` → `pod2`|❌ Bloqueado (timeout)|
|6|`pod2` permite solo a `pod1`|`pod1` → `pod2`|✅ Permitido|
|7|`pod2` permite solo a `pod1`|`pod3` → `pod2`|❌ Bloqueado (timeout)|

### Conceptos clave que demuestra el laboratorio

- **Aislamiento por selección:** un Pod pasa de _no aislado_ a _aislado_ en cuanto una NetworkPolicy lo selecciona con `podSelector` y declara el `policyTypes` correspondiente.
- **Modelo allow-list:** una vez aislado, todo lo que no esté explícitamente permitido se deniega.
- **Selección por etiquetas:** las reglas se basan en etiquetas (`app=pod1`), no en IPs ni en nombres de Pod, lo que las hace resistentes a que las IPs cambien.
- **Necesidad de un CNI compatible:** sin Calico (u otro CNI con soporte), la política se crearía pero **no tendría efecto**.

---

## 9. Limpieza (opcional)

```bash
kubectl delete networkpolicy network-policy
kubectl delete pod pod1 pod2 pod3
minikube stop
```

**Explicación:**

- `kubectl delete networkpolicy network-policy`: elimina la política (los Pods vuelven a quedar no aislados).
- `kubectl delete pod pod1 pod2 pod3`: elimina los tres Pods de prueba.
- `minikube stop`: detiene el clúster sin borrarlo (con `minikube delete` se elimina por completo).