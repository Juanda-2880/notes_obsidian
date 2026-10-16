**Universidad Icesi — Facultad Barbiere de Ingeniería, Diseño y Ciencias Aplicadas** **Asignatura:** Ciberseguridad
**Integrantes:**

- Juan David Pacheco Vargas — A00401844

---

### Verificación de conectividad hacia el enrutador

**Paso a paso**

1. Identificar la IP de cada equipo con `ip a` o `ifconfig -a` (Linux) / `ipconfig /all` (Windows).
2. Verificar que Kali y Metasploitable se ven entre sí con `ping`.
3. Hacer ping al enrutador del laboratorio (dirección terminada en `.1`) desde Kali y desde el host.

```bash
ping -c 4 <IP_enrutador>
```

**PING ENRUTADOR DEL LABORATORIO**

![[Hacking Etico-1791350888786.webp]]

Como se puede ver en la captura, el enrutador responde a los paquetes ICMP, lo que confirma que la red está bien configurada.

---

# PRIMERA PARTE

## 2. Técnicas de rastreo

### 2.1 Rango de direcciones de la red

La red de la sala es **192.168.105.0** con máscara **255.255.255.192** (/26).

- Bits de host: 32 − 26 = 6, es decir 2⁶ = **64 direcciones** en total.
- Dirección de red: 192.168.105.0
- **Rango de dispositivos permitidos (hosts): 192.168.105.1 a 192.168.105.62** (62 equipos)
- Dirección de broadcast: 192.168.105.63

### 2.2 Descubrimiento con fping

**Paso a paso**

1. En Kali, abrir una consola y ajustar el teclado: `setxkbmap latam` (o `setxkbmap es`).
2. Crear un archivo con las primeras 15 direcciones de la red. Se hizo con un ciclo `for`:

**FOR para el archivo de las direcciones**

![[Hacking Etico-1791351152083.webp]]

3. Ejecutar fping usando el archivo como entrada:

```bash
fping -f nomarchivo.txt
```

**Comando fping**

![[Hacking Etico-1791351205262.webp]]

#### Pregunta 1. ¿Cuáles de las primeras 15 direcciones están asociadas a equipos activos?

fping marca cada dirección como `is alive` (responde al ICMP echo) o `is unreachable`. Los equipos activos son los que aparecen como `is alive` en la captura anterior. El resto no respondió, lo cual no implica que estén apagados, ya que un firewall puede estar bloqueando el ICMP.

---

### 2.3 Descubrimiento con Nmap (sin escaneo de puertos)

El ping está bloqueado con frecuencia a nivel de firewall, por lo que se combinan técnicas. Se ejecutó:

```bash
sudo nmap -sn -PS80,443 -PE 192.168.105.0/26
```

**NMAP a todas las direcciones**

![[Hacking Etico-1791351236883.webp]]

#### Pregunta 2. ¿Qué resultados arroja nmap?

Nmap recorre las 64 direcciones de la subred y, para cada una que responde, imprime `Nmap scan report for <IP>` con `Host is up` y la latencia (más la MAC y el fabricante si está en la misma red local). Al final resume cuántas IPs se escanearon y cuántos hosts están activos, como se puede ver en la captura.

Nmap suele detectar más equipos que fping, porque además de ICMP prueba conexiones TCP a los puertos 80 y 443.

#### Pregunta 3. ¿Cuál es la utilidad de `-sn`, `-PS80,443` y `-PE`?

|Opción|Función|
|---|---|
|`-sn`|_Ping scan_ o "no port scan": solo descubre qué hosts están activos y **no** escanea puertos.|
|`-PS80,443`|Descubrimiento con **TCP SYN ping**: envía un paquete SYN a los puertos 80 y 443. Un SYN/ACK (puerto abierto) o un RST (puerto cerrado) demuestran que el host está vivo. Sirve cuando se filtra ICMP.|
|`-PE`|Descubrimiento con **ICMP Echo Request** (el ping tradicional, tipo 8).|

#### Pregunta 4. ¿Qué paquetes envía nmap a los equipos destino? (Wireshark)

**Wireshark – NMAP (red del laboratorio)**

![[Hacking Etico-1791351385798.webp]]

En la captura se observa que por cada host objetivo Nmap envía:

1. un **ICMP Echo Request** (por `-PE`),
2. un **TCP SYN al puerto 80** y
3. un **TCP SYN al puerto 443** (por `-PS80,443`).

Los hosts que responden devuelven ICMP Echo Reply, SYN/ACK o RST. En una red local, Nmap además usa **ARP** para descubrir hosts, y esa respuesta ARP basta para marcarlos como activos.

Ahora el mismo comando contra **Metasploitable 2**:

**NMAP a Metasploitable**

![[Hacking Etico-1791351402556.webp]]

**Wireshark a Metasploitable (del nmap)**

![[Hacking Etico-1791351419096.webp]]

Comparación: los paquetes enviados son del mismo tipo (ICMP echo, SYN a 80 y 443). La diferencia está en la **respuesta**: Metasploitable no tiene firewall y tiene el puerto 80 abierto, por lo que responde **SYN/ACK** al puerto 80 y **RST/ACK** al 443 (cerrado), tal como se ve en la captura de Wireshark.

---

### 2.4 Cambio del puerto destino del ping scan

```bash
sudo nmap -sn -PS5 192.168.105.0/26
```

**NMAP a un puerto**

![[Hacking Etico-1791351435374.webp]]

#### Pregunta 5. ¿Qué hace la opción `-PS` en este caso? (Wireshark)

`-PS<puertos>` indica a Nmap que haga el descubrimiento de hosts con **paquetes TCP SYN** hacia los puertos indicados. Con `-PS5` el SYN se envía al **puerto 5** en vez del 80/443. No es necesario que el puerto esté abierto: un RST (cerrado) o un SYN/ACK (abierto) confirman que el host está vivo; si no hay respuesta, se considera inactivo o filtrado.

Evidencia en Wireshark (filtro `tcp.dstport == 5`):

**Wireshark – NMAP al puerto, parte 1**

![[Hacking Etico-1791351454886.webp]]

**Wireshark – NMAP al puerto, parte 2**

![[Hacking Etico-1791351467939.webp]]

**Captura Wireshark – NMAP a puerto, parte 3**

![[Hacking Etico-1791351481443.webp]]

#### Pregunta 6. ¿Hay diferencia entre el listado de `-PS80,443 -PE` y el de `-PS5`?

Al comparar las capturas de los dos escaneos, el listado de hosts activos es prácticamente el mismo. Esto se debe a que los equipos que están en la misma subred se siguen detectando por **ARP**, sin importar el puerto TCP o el tipo de paquete que se use.

Cuando sí hay diferencias, se deben a que algunos equipos tienen firewall: filtran el ICMP o el puerto 5, pero responden en 80/443 (o al revés). Por eso ninguna técnica sola es suficiente y se combinan.

---

## 3. Técnicas de enumeración

Una vez conocidos los equipos activos, se determinan los puertos abiertos, los servicios, el sistema operativo y las versiones.

### 3.1 Escaneo TCP SYN (`-sS`)

#### Pregunta 7. ¿En qué consiste el escaneo TCP SYN?

Es el escaneo "half-open" o sigiloso. Nmap **no completa** el _three-way handshake_:

1. Nmap envía un **SYN** al puerto objetivo.
2. Según la respuesta:
    - **SYN/ACK**: puerto **abierto**. Nmap responde con **RST** para abortar la conexión sin completarla.
    - **RST (/ACK)**: puerto **cerrado**.
    - **Sin respuesta** o **ICMP unreachable**: puerto **filtrado** (firewall).

Al no completarse la conexión, normalmente no queda registrada en el log de la aplicación, aunque un IDS o un firewall con estado aún puede detectarlo.

```bash
sudo nmap -sS <dir_host>
```

**Nmap -sS a Metasploitable**

![[Hacking Etico-1791351501763.webp]]

**Captura de Wireshark de lo anterior** (se observa la secuencia SYN, SYN/ACK y RST)

![[Hacking Etico-1791351518314.webp]]

**Nmap -sS a un host del laboratorio**

![[Hacking Etico-1791351549848.webp]]

**Wireshark del -sS a hosts**

![[Hacking Etico-1791351572776.webp]]

**Puertos abiertos por equipo:** los puertos abiertos de cada equipo son los que aparecen con estado `open` en las capturas anteriores. En el caso de Metasploitable 2 se encuentran abiertos, entre otros, los puertos 21 (ftp), 22 (ssh), 23 (telnet), 25 (smtp), 53 (domain), 80 (http), 111 (rpcbind), 139 y 445 (smb), 512, 513 y 514 (servicios r), 1099, 1524, 2049 (nfs), 2121, 3306 (mysql), 5432 (postgresql), 5900 (vnc), 6000 (X11), 6667 (irc), 8009 y 8180 (tomcat).

---

### 3.2 Otros esquemas de enumeración

#### Pregunta 8. ¿En qué consiste cada uno y cómo se invocan?

Estos escaneos aprovechan el comportamiento de la **RFC 793**: un puerto cerrado responde con RST a paquetes que no pertenecen a una conexión establecida, mientras que un puerto abierto los ignora. Funcionan bien contra pilas TCP que siguen el estándar (Linux, por ejemplo) y no contra Windows, que responde RST en todos los casos.

|Técnica|Flags TCP enviados|Respuesta si el puerto está **cerrado**|Respuesta si está **abierto**|Comando|
|---|---|---|---|---|
|**TCP FIN**|FIN|RST|Ninguna (`open\|filtered`)|`sudo nmap -sF <host>`|
|**TCP Xmas Tree**|FIN + PSH + URG (varios bits encendidos, como un árbol de navidad)|RST|Ninguna (`open\|filtered`)|`sudo nmap -sX <host>`|
|**TCP NULL**|Ningún flag (todos en 0)|RST|Ninguna (`open\|filtered`)|`sudo nmap -sN <host>`|
|**TCP ACK**|ACK|RST (tanto abierto como cerrado, resultado `unfiltered`)|RST (igual)|`sudo nmap -sA <host>`|

Detalles importantes:

- FIN, Xmas y NULL son escaneos más **sigilosos** porque pueden pasar filtros que solo bloquean paquetes SYN. El resultado `open|filtered` indica que no hubo respuesta, así que Nmap no puede distinguir entre abierto y filtrado.
- **ACK no determina puertos abiertos.** Sirve para **mapear las reglas de un firewall**: si un puerto responde RST es `unfiltered` (el firewall deja pasar el paquete); si no responde, es `filtered`.

**Nmap TCP FIN**

![[Hacking Etico-1791351590588.webp]]

**Wireshark – Nmap TCP FIN**

![[Hacking Etico-1791351615017.webp]]

**Nmap TCP Xmas**

![[Hacking Etico-1791351631934.webp]]

**Wireshark – Nmap Xmas TCP** (se ven paquetes con flags FIN, PSH y URG)

![[Hacking Etico-1791351669435.webp]]

**Wireshark – TCP NULL** (paquetes TCP sin ningún flag activo)

![[Hacking Etico-1791351690924.webp]]

**Nmap TCP ACK**

![[Hacking Etico-1791351711100.webp]]

**Wireshark – Nmap TCP ACK**

![[Hacking Etico-1791351730980.webp]]

---

### 3.3 Fingerprinting del sistema operativo

#### Pregunta 9. ¿En qué consiste el fingerprinting de la pila TCP/IP?

Cada sistema operativo implementa la pila TCP/IP con pequeñas diferencias (valores iniciales de TTL, tamaño de ventana TCP, opciones TCP como MSS, SACK, timestamps y window scale, números de secuencia iniciales, manejo del bit DF, respuestas a paquetes malformados, etc.). Nmap envía una serie de sondas especiales (TCP, UDP e ICMP) a un puerto abierto y a uno cerrado, mide las respuestas y las compara con su base de datos de firmas para deducir el sistema operativo y su versión aproximada.

```bash
sudo nmap -O <dir_host>
```

**Nmap a Metasploitable (-O)**

![[Hacking Etico-1791351772373.webp]]

**Nmap a la dirección de un host (-O)**

![[Hacking Etico-1791351786054.webp]]

Como se puede ver en las capturas, Nmap identifica el sistema operativo de cada máquina en el campo `OS details`. En el caso de Metasploitable 2 lo reconoce como un sistema Linux con kernel 2.6.x.

---

### 3.4 Captura de banners

#### Pregunta 10. ¿Qué banner se obtuvo en cada puerto?

Se usa `telnet <IP> <puerto>` y se observa la respuesta del servicio. Los banners obtenidos en cada puerto son los que se muestran en las capturas (por ejemplo, el servicio FTP de Metasploitable anuncia `vsFTPd 2.3.4`).

Para HTTP hay que iniciar el diálogo: tras conectar por el puerto 80, se escribe `GET / HTTP/1.0` y se presiona ENTER dos veces. La respuesta trae primero las cabeceras HTTP (donde está el banner con el servidor y su versión) y luego la página.

**HTTP puerto 80 con mensaje GET y respuesta**

![[Hacking Etico-1791351805779.webp]]

Para servicios con TLS se usaría: `openssl s_client -connect x.x.x.x:443`.

#### Pregunta 11. Versiones del software en Metasploitable y en un equipo del laboratorio

Se usó el escaneo integrado de Nmap:

```bash
sudo nmap -T4 -A -v -sV <dir_host>
```

|Opción|Función|
|---|---|
|`-T4`|Plantilla de tiempos "agresiva" (más rápido).|
|`-A`|Modo agresivo: detección de SO (`-O`), versiones (`-sV`), scripts NSE por defecto (`-sC`) y traceroute.|
|`-v`|Salida detallada (_verbose_).|
|`-sV`|Detección de versión de los servicios.|

**Nmap -T4 ... a Metasploitable**

![[Hacking Etico-1791351813393.webp]]

**Nmap -T4 ... a un equipo del laboratorio**

![[Hacking Etico-1791351834877.webp]]

**Interpretación:** en las capturas se ve la versión de cada servicio detectado. En Metasploitable 2 las versiones típicas son:

|Puerto|Servicio y versión|
|---|---|
|21|vsftpd 2.3.4|
|22|OpenSSH 4.7p1 Debian|
|23|Linux telnetd|
|25|Postfix smtpd|
|80|Apache httpd 2.2.8 (Ubuntu) DAV/2, PHP/5.2.4|
|139/445|Samba smbd 3.X|
|3306|MySQL 5.0.51a|
|5432|PostgreSQL 8.3.x|
|6667|UnrealIRCd|
|8180|Apache Tomcat/Coyote JSP engine 1.1|

Todas son versiones muy antiguas con vulnerabilidades públicas conocidas, y esa es la utilidad del _fingerprinting_: con la versión exacta se puede buscar el CVE y el exploit correspondiente.

---

### 3.5 Maltego

#### Pregunta 12. ¿Para qué sirve Maltego?

Maltego es una herramienta de **inteligencia de fuentes abiertas (OSINT)** y análisis de enlaces. A partir de una entidad inicial (dominio, correo, persona, empresa, IP, etc.), ejecuta **transformaciones** que consultan fuentes públicas (DNS, WHOIS, motores de búsqueda, redes sociales) y representa las relaciones como un **grafo visual**. Se usa en la fase de _reconocimiento/footprinting_ de un pentest, en investigaciones forenses y en inteligencia de amenazas.

**Descarga de Maltego**

![[Hacking Etico-1791351846057.webp]]

#### Pregunta 13. Cuatro transformaciones y su utilidad

Procedimiento: crear un mapa en blanco, arrastrar una entidad _Domain_, renombrarla con el dominio a explorar, dar clic derecho y aplicar transformaciones. El dominio explorado y los resultados de cada transformación se documentan en el PDF de Maltego adjunto en el ZIP de la entrega.

|Transformación|Utilidad|
|---|---|
|**To DNS Name – NS (name server)**|Obtiene los servidores de nombres del dominio.|
|**To DNS Name – MX (mail server)**|Identifica los servidores de correo; útil para ver proveedor y superficie de ataque por email.|
|**To IP Address [DNS]**|Resuelve nombres a direcciones IP, para saber dónde está alojada la infraestructura.|
|**To Website [Quick lookup] / To Email addresses**|Descubre sitios web y correos asociados al dominio, útiles para ingeniería social.|

---

# SEGUNDA PARTE

## 4. Detección de vulnerabilidades con OpenVAS (GVM)

**Paso a paso**

1. Con la Kali configurada con 4 GB de RAM, iniciar el servicio: menú _Kali, System Services, gvm start_ (o `sudo gvm-start`).
2. Esperar a que abra la interfaz web en `https://127.0.0.1:9392` y entrar con las credenciales del administrador.

**OpenVAS – Dashboard**

![[Hacking Etico-1791351859351.webp]]

3. Crear el objetivo en _Configuration, Targets_ (nombre descriptivo y la IP de Metasploitable 2).

**Creación del Target**

![[Hacking Etico-1791351868653.webp]]

4. Crear una tarea en _Scan Management, New Task_, con _Scan Config = Full and Fast_ y el target anterior. Iniciar con _Start Task_ (puede tardar más de 30 minutos).

**Escaneo de Metasploitable en OpenVAS**

![[Hacking Etico-1791351885088.webp]]

5. Al terminar, abrir el reporte, analizarlo y descargarlo en PDF (adjunto en el ZIP de la entrega junto con el de Maltego).

#### Pregunta 14. Interpretación de resultados: al menos 4 vulnerabilidades de Metasploitable 2

Según el reporte de OpenVAS, cada hallazgo muestra su severidad (CVSS), el puerto afectado y el CVE asociado. Las vulnerabilidades más relevantes encontradas en Metasploitable 2 son:

|#|Vulnerabilidad|Puerto/Servicio|Descripción e impacto|
|---|---|---|---|
|1|**Backdoor en vsftpd 2.3.4** (CVE-2011-2523)|21/FTP|La versión distribuida contiene una puerta trasera: un usuario que termina en `:)` abre una shell root en el puerto 6200.|
|2|**Samba `usermap_script`** (CVE-2007-2447)|139, 445/SMB|Permite ejecución remota de comandos sin autenticación mediante metacaracteres de shell en el nombre de usuario.|
|3|**Backdoor en UnrealIRCd 3.2.8.1** (CVE-2010-2075)|6667/IRC|Un troyano en el código fuente permite ejecutar comandos remotamente.|
|4|**distccd, ejecución remota de comandos** (CVE-2004-2687)|3632/distcc|Cualquiera puede ejecutar comandos con los privilegios del servicio.|
|5|**Bindshell / shell root abierta**|1524|Un puerto que entrega directamente una shell de root sin autenticación.|
|6|**Servicios sin cifrar y credenciales débiles o por defecto**|23 (Telnet), 512-514 (rexec/rlogin/rsh), 5900 (VNC), Tomcat 8180|Envían credenciales en texto plano o usan contraseñas triviales (`msfadmin`, `tomcat`, `password`).|
|7|**Apache/PHP obsoletos (PHP-CGI)** (CVE-2012-1823)|80/HTTP|Inyección de argumentos que permite ejecutar código remoto y leer el código fuente.|

**¿Qué bondades tiene una herramienta de este tipo?**

- **Automatiza** la revisión de cientos de miles de pruebas (NVT) contra muchos equipos, algo inviable manualmente.
- **Prioriza** el riesgo mediante la puntuación CVSS y asocia cada hallazgo a su CVE.
- **Recomienda soluciones** (parches, configuraciones) en el reporte.
- Es **gratuita y de código abierto**, y su base de datos de vulnerabilidades se actualiza continuamente.
- Permite **escaneos programados** para detectar a tiempo cambios en la seguridad y comprobar que los parches funcionan.
- Genera **reportes** exportables (PDF, XML, CSV) para auditorías y comunicación con la gerencia.
- Limitaciones: puede generar falsos positivos, consume muchos recursos y es ruidosa en la red; debe usarse solo con autorización.

---

## 5. Conclusiones

1. Ninguna técnica de descubrimiento es suficiente por sí sola: el ICMP puede estar bloqueado, por lo que combinar ARP, ICMP y TCP SYN (fping y Nmap) da un mapa más completo de la red.
2. Wireshark permitió **verificar en la práctica** lo que cada opción de Nmap realmente envía (ICMP, SYN, FIN, Xmas, NULL, ACK) y cómo responde cada equipo, lo que confirma la teoría de la RFC 793.
3. El escaneo SYN es más discreto que una conexión completa, pero **no es invisible**: los IDS y los firewalls con estado lo detectan.
4. Los escaneos FIN, Xmas y NULL dependen de la implementación de la pila TCP; ACK sirve para analizar reglas de firewall, no para encontrar puertos abiertos.
5. El fingerprinting y la captura de banners revelan sistema operativo y versiones, información crítica para un atacante, lo que justifica **ocultar o minimizar los banners** en entornos productivos.
6. Metasploitable 2 evidenció que el uso de software obsoleto, servicios innecesarios y credenciales por defecto genera una superficie de ataque enorme; OpenVAS permite detectarla de forma sistemática.
7. Toda esta actividad solo es legítima con **autorización expresa** y en un entorno controlado, como en este laboratorio.

## 6. Qué aprendí en este laboratorio

Aprendí a descubrir equipos activos en una red y enumerar sus servicios con fping y Nmap, a leer el tráfico en Wireshark para entender qué hace cada técnica de escaneo a nivel de paquetes y flags TCP, a identificar el sistema operativo y las versiones de software por fingerprinting y banners, a hacer reconocimiento OSINT con Maltego, y a usar OpenVAS para interpretar un reporte de vulnerabilidades y priorizar su corrección. También entendí por qué es tan importante mantener los sistemas actualizados y minimizar los servicios expuestos.

