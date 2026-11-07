# Informe de Práctica: Resolución del Reto Cicada-3301 Vol:1 (TryHackMe)

**Universidad Icesi — Facultad de Ingeniería, Diseño y Ciencias Aplicadas**  
**Departamento de Tecnologías de Información y Comunicaciones**  
**Asignatura:** Ciberseguridad  

**Integrantes:**
* **Juan David Pacheco Vargas** — Código: `A00401844`
* **Esteban Guarín Valencia** — Código: `A00402028`

**Laboratorio de TryHackMe:** [Cicada-3301 Vol:1](https://tryhackme.com/room/cicada3301vol1)

![[Informe-1793552385893.webp]]

---

## 1. Introducción y Contexto Histórico: El Enigma de Cicada 3301

El reto práctico desarrollado en esta sesión está inspirado en uno de los enigmas más fascinantes y sofisticados en la historia moderna de la seguridad informática e Internet: **Cicada 3301**. 

El 4 de enero de 2012 apareció una publicación anónima en los foros de 4chan con una imagen sencilla en blanco y negro que contenía un texto desafiante:

> *"Hola. Estamos buscando individuos altamente inteligentes. Para encontrarlos, hemos diseñado una prueba. Hay un mensaje oculto en esta imagen. Encuéntralo y te llevará por el camino para encontrarnos. Esperamos con ansias conocer a los pocos que logren llegar hasta el final. Buena suerte. 3301."*

Lo que comenzó como una aparente curiosidad digital se convirtió en una compleja travesía internacional de reclutamiento por entregas (con ediciones en 2012, 2013 y 2014) atribuida a una organización enigmática que buscaba expertos en seguridad, criptografía, ciberseguridad defensiva y libertades civiles en la red. 

Las pruebas de Cicada 3301 se caracterizaron por una interdisciplinariedad sin precedentes, integrando:
1. **Esteganografía digital avanzada:** Ocultamiento de información en imágenes (usando herramientas como OutGuess y Steghide) y en archivos de sonido mediante espectrogramas de audio.
2. **Criptografía clásica y asimétrica:** Desde cifrados históricos (César, Atbash, Vigenère, cifrados de libro) hasta algoritmos modernos de clave pública como **PGP (Pretty Good Privacy)** y firmas digitales RSA para validar la autenticidad de cada pista y evitar suplantadores.
3. **Matemáticas y Filosofía:** Un uso intensivo de números primos, la periodicidad biológica de la cigarra (cuyos ciclos de vida emergen en números primos de 13 o 17 años para evitar depredadores), literatura clásica (*The Book of the Law* de Aleister Crowley, poemas de William Blake) y textos anglosajones rúnicos (*Liber Primus*).

La sala **Cicada-3301 Vol:1** de la plataforma **TryHackMe** reproduce de forma fiel las etapas fundacionales del misterio original, exigiendo al estudiante aplicar conceptos de esteganálisis, criptoanálisis de sustitución, identificación y rotura de funciones resumen (*hashes*), y correlación de pistas mediante análisis forense digital y pensamiento lateral.

---

## 2. Configuración del Entorno de Red: Conexión mediante OpenVPN

Para acceder a las máquinas virtuales, servicios privados y descargas de recursos dentro de la infraestructura aislada de TryHackMe, es fundamental configurar una conexión segura de capa de red (OSI 3) a través de **OpenVPN**.

![[Informe-1794000602197.webp]]

### Justificación Técnica de la Conexión VPN
OpenVPN establece un túnel cifrado (TLS/SSL) entre el sistema operativo de auditoría (Kali Linux) y la red de laboratorios de TryHackMe. Esta práctica es un estándar en la industria de la ciberseguridad por las siguientes razones:
* **Enrutamiento privado (Intranet del laboratorio):** Asigna una interfaz virtual de red (`tun0`) con una dirección IP dentro del segmento privado `10.x.x.x`, permitiendo el tráfico directo hacia los destinos del laboratorio sin exponer el host a Internet abierta.
* **Seguridad y confidencialidad:** El tráfico que contiene cargas útiles, paquetes de análisis y credenciales viaja encapsulado y cifrado mediante algoritmos robustos de cifrado simétrico (AES-CBC / AES-GCM), evitando interceptaciones en la red física del operador local.
* **Aislamiento forense:** Garantiza que las interacciones del laboratorio se mantengan confinadas en la red de pruebas sin generar interferencias con servicios corporativos o universitarios.

Comando ejecutado en la terminal de Kali Linux:
```bash
sudo openvpn us-east-1-Juanda2880-regular.ovpn
```

---

## 3. Desarrollo y Resolución Técnica de las Tareas (Walkthrough)

### Task 1: Download! (Descarga y Reconocimiento Inicial)

El punto de partida del laboratorio consiste en obtener los recursos iniciales suministrados por el creador del reto.

![[Informe-1794000505607.webp]]

Procedimos a descargar el paquete comprimido denominado `Cicada_3301_1586991734089.zip` y lo descomprimimos utilizando la utilidad `unzip` en la terminal:

```bash
cd ~/Downloads
unzip Cicada_3301_1586991734089.zip
```

![[Informe-1794000730884.webp]]

Al listar el contenido del directorio, identificamos dos artefactos clave:
1. `welcome.jpg`: Imagen estática con el mensaje de bienvenida de Cicada 3301.
2. `3301.wav`: Archivo de audio sin compresión de 7 segundos de duración.

Al visualizar el archivo `welcome.jpg`, observamos el mensaje arquetípico de inicio:

![[Informe-1794000766294.webp]]

Por su parte, el archivo de audio `3301.wav` constituyó el objetivo primario para la siguiente tarea:

![[Informe-1794000906525.webp]]

Validamos la descarga y extracción satisfactoria en la plataforma:

![[Informe-1794000835595.webp]]

---

### Task 2: Analyze The Audio (Esteganografía de Audio y Análisis Espectral)

En la segunda tarea, TryHackMe nos presenta una premisa reveladora: los navegadores convencionales y los reproductores de música habituales son totalmente inútiles en este escenario, sugiriendo la necesidad de emplear software especializado en análisis de señales de audio.

![[Informe-1794000945248.webp]]

Al reproducir auditivamente el archivo `3301.wav`, el audio se percibe únicamente como una ráfaga breve de chirridos estridentes y ruido blanco, sin mensajes hablados ni patrones rítmicos audibles. Esto constituye un vector clásico de **esteganografía acústica**, donde los datos no se codifican para el oído humano, sino que se dibujan matemáticamente en el dominio de las frecuencias.

Para investigarlo, instalamos y ejecutamos la herramienta de análisis espectral **Sonic Visualiser** en Kali Linux:

![[Informe-1794001110731.webp]]

Al cargar la pista en su vista estándar de forma de onda (*Waveform*), únicamente visualizamos la envolvente de amplitud frente al tiempo, la cual no arroja pistas concluyentes:

![[Informe-1794001680135.webp]]

Para revelar el contenido oculto, aplicamos la transformada de Fourier a través de la funcionalidad de **Espectrograma** del software (`Layer` $\to$ `Add Spectrogram` o tecla rápida `Shift + G`):

![[Informe-1794001830016.webp]]

Fue necesario calibrar los parámetros de renderizado del espectrograma:
* **Escala:** Logarítmica / dB para maximizar el contraste de las frecuencias altas.
* **Tamaño de ventana (Window):** 1024 / 4096 muestras para refinar la resolución visual.
* **Paleta de color:** *Bright Green* para destacar los picos de intensidad energética.

El resultado fue inmediato y sorprendente: en el rango de frecuencias altas (de $500\text{ Hz}$ a $21\text{ kHz}$) apareció dibujado un **código QR bidimensional** que integraba en su zona central el logotipo emblemático de la cigarra de Cicada 3301:

![[Informe-1794002136244.webp]]

#### Extracción del Enlace mediante Escaneo Óptico
Procedimos a escanear el código QR directamente con un dispositivo móvil desde la pantalla de la máquina virtual:

![[Informe-1794002280976.webp]]

> [!NOTE] Dificultad técnica encontrada
> En un primer momento, el escaneo falló debido a la curvatura de la lente, los reflejos del monitor y la relación de aspecto no cuadrada del espectrograma. Se requirió ajustar manualmente el zoom horizontal y vertical en Sonic Visualiser hasta lograr una cuadrícula geométrica proporcional y legible por el sensor de la cámara.

El código QR decodificado apuntó hacia la siguiente dirección web:
```text
https://pastebin.com/wphPq0Aa
```

Al abrir esta página en el navegador, encontramos dos cadenas de texto aparentemente ofuscadas:

![[Informe-1794002506964.webp]]

Ingresamos el enlace en TryHackMe para registrar la finalización exitosa de la Task 2:

![[Informe-1794002564236.webp]]

---

### Task 3: Decode the Passphrase (Criptoanálisis: Base64 y Cifrado Vigenère)

Al comenzar la tercera tarea, debemos descifrar los datos obtenidos en la publicación de Pastebin:
* **Passphrase:** `SG01Ul80X1A0NTVtaHA0NTMh`
* **Key:** `Q2ljYWRh`

![[Informe-1794002584250.webp]]

Ambas cadenas presentan las características inequívocas de la codificación **Base64** (alfabeto alfanumérico ASCII de 64 caracteres, longitud múltiplo de 4 y caracteres de relleno `=`). Para procesarlas de manera controlada y profesional, empleamos **CyberChef**.

#### ¿Qué es CyberChef y para qué se utiliza?
**CyberChef** (conocido como *The Cyber Swiss Army Knife*) es una aplicación web de código abierto creada por el **GCHQ (Government Communications Headquarters)**, la agencia de inteligencia y ciberseguridad del Reino Unido. Es un estándar en análisis forense digital, análisis de malware y competencias CTF. 

Permite componer flujos de trabajo (*recipes*) modulares para ejecutar cientos de operaciones criptográficas y forenses complejas de forma intuitiva, incluyendo:
* Conversión de esquemas de codificación (Base64, Hexadecimal, Binario, URL Encoding, Morse).
* Descompresión y desempaquetado de formatos de archivo.
* Análisis de entropía y cálculo de hashes criptográficos (MD5, SHA-1, SHA-256, SHA-512).
* Aplicación de cifrados simétricos clásicos y modernos (César, Vigenère, XOR, AES, DES, Blowfish).

#### Decodificación en CyberChef

1. **Decodificación de la Passphrase:**  
   Aplicando la receta `From Base64` a la entrada `SG01Ul80X1A0NTVtaHA0NTMh`, obtuvimos:
   $$\text{Output: } \texttt{Hm5R\_4\_P455mhp453!}$$

   ![[Informe-1794002831350.webp]]

2. **Decodificación de la Clave (Key):**  
   Aplicando la receta `From Base64` a la entrada `Q2ljYWRh`, obtuvimos:
   $$\text{Output: } \texttt{Cicada}$$

   ![[Informe-1794002866158.webp]]

Ingresamos ambas respuestas intermedias en la plataforma y fueron validadas como correctas:

![[Informe-1794003020238.webp]]

#### El Segundo Nivel Criptográfico: Cifrado Polialfabético de Vigenère
A pesar de haber decodificado el Base64, TryHackMe formula una pregunta adicional con la siguiente pista:

> *"Still looks funny? Find and use a cipher along with the key to decipher the passphrase."*

![[Informe-1794003742431.webp]]

![[Informe-1794003106388.webp]]

La cadena `Hm5R_4_P455mhp453!` claramente aún no posee significado legible completo, pero contamos con una clave: `Cicada`. Esto corresponde al clásico **Cifrado de Vigenère**, un cifrado polialfabético de sustitución propuesto originalmente por Blaise de Vigenère en 1586. 

En este sistema, cada carácter del texto plano $P_i$ se desplaza en el alfabeto un número de posiciones dictado por el carácter correspondiente de la clave repetida $K_i$:
$$C_i = (P_i + K_i) \pmod{26}$$

Para resolverlo de forma programática y reproducible, desarrollamos un script interactivo en **Python 3**.

#### Script en Python: Implementación del Algoritmo Vigenère

A continuación se presenta el código completo en Python listo para copiar, guardar y ejecutar (`vigenere.py`):

```python
def vigenere(text, key, decrypt=False):
    result = ""
    key = key.lower()
    key_index = 0

    for char in text:
        if char.isalpha():
            # Calcula el desplazamiento (0 a 25) según la letra de la clave
            shift = ord(key[key_index % len(key)]) - ord('a')

            # Si es descifrado, invertimos la dirección del desplazamiento
            if decrypt:
                shift = -shift

            # Determina la base ASCII según si la letra es mayúscula o minúscula
            base = ord('A') if char.isupper() else ord('a')

            # Aplica la fórmula modular preservando el rango alfabético
            encrypted = chr(
                (ord(char) - base + shift) % 26 + base
            )

            result += encrypted
            key_index += 1
        else:
            # Los números, guiones y signos especiales no se alteran
            result += char

    return result


print("=== CIFRADO VIGENERE ===")
print("1. Encrypt")
print("2. Decrypt")

option = input("Selecciona una opcion: ")
text = input("Introduce el texto: ")
key = input("Introduce la clave: ")

if not key.isalpha():
    print("Error: la clave debe contener solo letras.")
elif option == "1":
    print("Ciphertext:", vigenere(text, key))
elif option == "2":
    print("Plaintext:", vigenere(text, key, True))
else:
    print("Opcion no valida.")
```

#### Explicación Detallada Línea por Línea del Código

1. `def vigenere(text, key, decrypt=False):`  
   Define la función principal que recibe el texto a procesar, la palabra clave (`key`) y una bandera booleana `decrypt` que por defecto es `False` (modo cifrado).
2. `result = ""`  
   Inicializa una cadena vacía donde se irán concatenando los caracteres transformados.
3. `key = key.lower()`  
   Convierte toda la clave a letras minúsculas para unificar los desplazamientos numéricos sin distinción de mayúsculas.
4. `key_index = 0`  
   Lleva el control de la posición del carácter de la clave que se debe aplicar.
5. `for char in text:`  
   Itera carácter por carácter sobre el texto de entrada.
6. `if char.isalpha():`  
   Verifica si el carácter actual es una letra del abecedario. Si no lo es (por ejemplo, dígitos como `4`, `5`, guiones bajos `_` o signos `!`), se salta la operación criptográfica para no corromper la sintaxis leetspeak.
7. `shift = ord(key[key_index % len(key)]) - ord('a')`  
   Obtiene el carácter de la clave mediante aritmética modular (`% len(key)`) para repetirla cíclicamente tantas veces como sea necesario. Convierte la letra en un entero entre $0$ y $25$ restando el valor ASCII de `'a'`.
8. `if decrypt: shift = -shift`  
   Si la función se invocó en modo descifrado, invierte el signo del desplazamiento para retroceder en el abecedario.
9. `base = ord('A') if char.isupper() else ord('a')`  
   Determina la base de referencia numérica ASCII (`65` para mayúsculas o `97` para minúsculas), permitiendo preservar la caja original del carácter.
10. `encrypted = chr((ord(char) - base + shift) % 26 + base)`  
    Aplica la fórmula matemática modular: normaliza el carácter restándole la base, suma el desplazamiento, aplica el módulo 26 para que dé una vuelta completa al abecedario en caso de desbordamiento, vuelve a sumar la base y lo convierte de nuevo a carácter con `chr()`.
11. `result += encrypted` y `key_index += 1`  
    Añade el carácter resultante y avanza el cursor de la clave únicamente cuando se procesó una letra válida.
12. `else: result += char`  
    Mantiene intactos los caracteres no alfabéticos sin incrementar `key_index`.
13. `return result`  
    Retorna la cadena final procesada.
14. Bloque interactivo (`input`, validación `key.isalpha()` y condicionales `if/elif/else`):  
    Interactúa con el usuario solicitando la opción, el texto y la clave, validando que la clave esté compuesta únicamente de letras y mostrando el resultado correspondiente.

#### Ejecución del Script y Corrección del Fallo Conceptual (Descifrado vs. Cifrado)

Editamos y guardamos el script con el editor `vim` en la terminal de Kali Linux (`vim vigenere.py`) y procedimos a ejecutarlo:

```bash
python3 vigenere.py
```

##### Primer Intento (Opción 2: Decrypt):
Ingresamos los datos:
* Opción: `2`
* Texto: `Hm5R_4_P455mhp453!`
* Clave: `Cicada`

![[Informe-1794004310183.webp]]

El resultado obtenido fue: `Fe5P_4_P455jhn453!`. Esta cadena carecía de coherencia semántica en inglés y no representaba ninguna palabra o frase lógica.

##### Segundo Intento (Opción 1: Encrypt):
Probamos entonces con la opción de cifrado directo:
* Opción: `1`
* Texto: `Hm5R_4_P455mhp453!`
* Clave: `Cicada`

![[Informe-1794004488317.webp]]

El resultado obtenido fue:
$$\mathbf{Ju5T\_4\_P455phr453!}$$

> [!IMPORTANT] ¿Por qué funcionó la opción de Cifrado (Encrypt) y no la de Descifrado (Decrypt)?
> La razón técnica radica en la dirección del desplazamiento algebraico aplicado por el creador del laboratorio:
> * La frase original en lenguaje claro (*plaintext*) con convención *leetspeak* era: `Ju5T_4_P455phr453!` (*"Just_a_Passphrase!"*).
> * Para preparar el reto, el autor generó la cadena intermedia aplicando la operación de **descifrado** sobre la frase original (desplazamiento negativo con la clave `Cicada`), produciendo `Hm5R_4_P455mhp453!`.
> * Por consiguiente, para regresar de `Hm5R...` a la frase inteligible `Ju5T...`, la transformación matemática requerida dentro de la lógica del algoritmo correspondía exactamente a un **desplazamiento hacia adelante (+shift)**, es decir, la operación de **cifrado (Encrypt)**.

Validamos esta contraseña final en TryHackMe, completando con éxito todas las preguntas de la Task 3:

![[Informe-1794004609053.webp]]

---

### Task 4: Gather Metadata (Esteganografía con Steghide en Imágenes)

La cuarta tarea nos instruye a regresar a la imagen inicial descargada en la primera tarea (`welcome.jpg`) y extraer la información oculta utilizando herramientas esteganográficas y la frase de paso (*passphrase*) descubierta en la etapa anterior.

![[Informe-1794004653973.webp]]

#### ¿Qué es Steghide y cuál es su principio de operación?
**Steghide** es una herramienta especializada de esteganografía para sistemas Linux diseñada para ocultar datos dentro de diversos archivos contenedores, especialmente imágenes JPEG y mapas de bits (BMP), así como archivos de audio WAV y AU. 

A diferencia de técnicas rudimentarias de esteganografía que se limitan a concatenar datos al final del archivo (después del marcador de fin de imagen `FF D9` en JPEG), Steghide utiliza un algoritmo basado en **teoría de grafos**:
1. Empareja coeficientes de la transformada discreta del coseno (DCT) para minimizar las alteraciones estadísticas perceptibles en la imagen.
2. Cifra el mensaje embebido mediante el algoritmo criptográfico simétrico **Rijndael-128 (AES)** empleando una clave derivada de la frase de paso (*passphrase*) provista.
3. Si la frase de paso es incorrecta o no coincide, no es posible recuperar el payload ni determinar analíticamente si el contenedor posee datos ocultos.

Consultamos la documentación oficial y la página de ayuda de Steghide en Kali Linux:

![[Informe-1794004843681.webp]]

Para extraer información de un archivo contenedor esteganográfico (*stego-file*), se utiliza el modificador `extract -sf`:

```bash
steghide extract -sf welcome.jpg
```

![[Informe-1794005000417.webp]]

#### Validación de la Passphrase
* Al ejecutar el comando e ingresar la contraseña previa sin transformar (`Hm5R_4_P455mhp453!`), Steghide falló rotundamente con el mensaje:
  ```text
  steghide: could not extract any data with that passphrase!
  ```
* Al reintentar la extracción utilizando la frase final obtenida en el paso de Vigenère (`Ju5T_4_P455phr453!`), el programa extrajo exitosamente el archivo oculto:
  ```text
  wrote extracted data to "invitation.txt".
  ```

![[Informe-1794005128505.webp]]

Inspeccionamos el contenido del archivo de texto extraído con el comando `cat invitation.txt`:

```bash
cat invitation.txt
https://imgur.com/a/c0ZSZga
```

![[Informe-1794005189600.webp]]

El archivo contenía una URL que apunta a la plataforma Imgur (`https://imgur.com/a/c0ZSZga`). Ingresamos esta respuesta en TryHackMe y superamos el Task 4:

![[Informe-1794005238580.webp]]

---

### Task 5: Find Hidden Files (Identificación de Herramientas Históricas: OutGuess)

En la quinta tarea, se nos desafía a inspeccionar la nueva imagen obtenida a través del enlace de Imgur e identificar la herramienta específica que fue utilizada para incrustar datos ocultos en ella.

![[Informe-1794005476521.webp]]

Navegamos a la URL `https://imgur.com/a/c0ZSZga` y visualizamos la famosa imagen de bienvenida original de Cicada 3301:

![[Informe-1794005458509.webp]]

El texto del reto nos insta a investigar qué software se utilizó en el desafío histórico de Cicada 3301. Realizamos una búsqueda en Google de fuentes abiertas (OSINT):
`"Use the same tool used to extract data in the original Cicada challenges"`

![[Informe-1794005780142.webp]]

La visión general de inteligencia de búsqueda confirmó inmediatamente el hallazgo:
> *"El software principal utilizado para extraer los datos ocultos en las imágenes originales de los desafíos de Cicada 3301 es **OutGuess**."*

#### ¿Qué es OutGuess?
Desarrollado por el investigador en seguridad **Niels Provos** en 1999, **OutGuess** es una herramienta universal de esteganografía diseñada específicamente para imágenes en formato JPEG. 

Su principal fortaleza frente a herramientas convencionales como Steghide radica en que **preserva la distribución estadística de primer y segundo orden** de los coeficientes DCT. Esto significa que corrige activamente los histogramas de la imagen durante el proceso de inserción, volviendo el archivo invisible frente a técnicas avanzadas de detección esteganográfica como el análisis $\chi^2$ (Chi-cuadrado).

Ingresamos `outguess` como respuesta en la plataforma y completamos el Task 5:

![[Informe-1794006268005.webp]]

---

### Task 6: Book Cipher & Crack the Hash (Extracción con OutGuess, Identificación SHA-512 y Cifrado de Libro)

La sexta tarea constituye la sección más densa y completa del laboratorio: exige extraer la carga útil con OutGuess, identificar el algoritmo de resumen del hash encontrado, quebrar dicho hash para descubrir el texto base y, finalmente, decodificar un cifrado de libro (*Book Cipher*).

![[Informe-1794006311866.webp]]

#### 1. Descarga del JPEG Original y Uso de Boxentriq OutGuess Extractor
Descargamos la imagen original directamente desde el enlace de Imgur, guardándola con su nombre original `8S8OaQw.jpeg` (26.8 KB):

![[Informe-1794005964765.webp]]

> [!NOTE] Cuestión técnica sobre la disponibilidad de OutGuess
> El repositorio y binarios originales de OutGuess en Linux datan de hace más de dos décadas y presentan severos conflictos de compatibilidad al compilarse en kernels y toolchains modernos de Kali Linux (debido a versiones obsoletas de `libjpeg` y cabeceras de arquitectura de 32 bits). Por tal motivo, empleamos la plataforma web especializada **Boxentriq OutGuess Extractor**, la cual ejecuta una compilación verificada de OutGuess mediante **WebAssembly (WASM)** directamente en el navegador de forma local y sin enviar datos al servidor.

Cargamos el archivo `8S8OaQw.jpeg` en Boxentriq sin necesidad de passphrase y ejecutamos la extracción:

![[Informe-1794006115216.webp]]

El resultado de la extracción reveló un mensaje completo firmado digitalmente con PGP:

![[Informe-1794006142423.webp]]

Guardamos y abrimos el texto extraído en el editor de notas `Mousepad` de Kali Linux:

![[Informe-1794007351455.webp]]

![[Informe-1794007076043.webp]]

El mensaje contiene la cabecera:
```text
-----BEGIN PGP SIGNED MESSAGE-----
Hash: SHA1

Welcome again.

Here is a book code. To find the book, break this hash:
b6a233fb9b2d8772b636ab581169b58c98bd4b8df25e452911ef75561df649edc8852846e81837136840f3aa453e83d86323082d5b6002a16bc20c1560828348
```

Seguido por una lista extensa de números con formato de coordenadas y la firma digital PGP genuina de Cicada 3301.

#### 2. Identificación del Hash con `hashid`
Para identificar con certeza la familia algorítmica del hash extraído, utilizamos la utilidad CLI **HashID** en Kali Linux sobre el archivo de texto:

```bash
hashid hash.txt
```

![[Informe-1794007545070.webp]]

##### ¿Qué es HashID?
`hashid` es una herramienta escrita en Python integrada de fábrica en Kali Linux que permite inspeccionar firmas, longitudes en bits y conjuntos de caracteres de un resumen criptográfico desconocido. 

Al evaluar la cadena de 128 caracteres hexadecimales ($128 \times 4 \text{ bits} = 512 \text{ bits}$), `hashid` determina con máxima prioridad que corresponde al estándar **SHA-512** (Secure Hash Algorithm de 512 bits, perteneciente a la familia SHA-2).

#### 3. Rotura del Hash (Reverse Hash Lookup)
Copiamos el hash SHA-512 e ingresamos a una herramienta en línea de búsqueda inversa de hashes (*Reverse Hash Decoder / MD5Hashing*):

![[Informe-1794007697271.webp]]

> [!NOTE] ¿Por qué toma tiempo el proceso de desencriptación inversa de un hash?
> A diferencia de los algoritmos de cifrado simétrico o asimétrico, las funciones hash criptográficas como **SHA-512 son funciones unidireccionales (*One-Way Functions*)**, diseñadas intencionalmente para ser matemáticamente irreversibles (propiedad de **resistencia a la preimagen**). No existe una fórmula matemática inversa que permita "despejar" el texto plano a partir del hash de salida.
> 
> En consecuencia, los servicios de descifrado en línea operan mediante **búsquedas en macro-bases de datos precomputadas (Tablas Rainbow y diccionarios distribuidos)** que indexan billones de combinaciones calculadas previamente. La demora en la respuesta se debe a la consulta de índices distribuidos de terabytes de tamaño y a la verificación criptográfica de coincidencias exactas en servidores de alta concurrencia.

El resultado de la búsqueda inversa quebró el hash exitosamente, arrojando la siguiente URL:
```text
https://pastebin.com/6FNiVLh5
```

Al acceder a este Pastebin, nos encontramos con un texto en inglés correspondiente al **Capítulo I de *The Book of the Law* (*Liber AL vel Legis*)**, obra mística escrita por Aleister Crowley en 1904:

![[Informe-1794009309900.webp]]

#### 4. Explicación Didáctica y Resolución del Cifrado de Libro (Book Cipher)

El mensaje extraído de OutGuess incluía una serie de instrucciones y pares numéricos bajo el título de *Book Code*:

```text
I:1:6
I:2:15
I:3:26
...
```

##### ¿Cómo funciona un Cifrado de Libro (*Book Cipher*)?
Un **cifrado de libro** es una técnica criptográfica clásica en la cual ambas partes (emisor y receptor) acuerdan utilizar una misma obra literaria o texto público como fuente de coordenadas para cifrar mensajes. Ninguna clave criptográfica tradicional viaja en el mensaje; únicamente viajan punteros numéricos que indexan caracteres o palabras dentro del texto de referencia.

En el formato particular utilizado por Cicada 3301, cada coordenada sigue la estructura:
$$\mathbf{Cap\acute{\imath}tulo} : \mathbf{L\acute{\imath}nea / P\acute{a}rrafo} : \mathbf{Car\acute{a}cter}$$

##### Reglas de Decodificación:
1. **Primer parámetro (`I`):** Hace referencia al número del capítulo del libro (Capítulo I).
2. **Segundo parámetro:** Indica el número del versículo o línea dentro de dicho capítulo.
3. **Tercer parámetro (Índice del carácter):** 
   * **Entero positivo:** Se cuentan los caracteres de izquierda a derecha desde el inicio de la línea.
   * **Entero negativo:** Se cuentan los caracteres de derecha a izquierda partiendo desde el final de la línea hacia atrás.
4. **Separadores (`/`):** El carácter de barra diagonal indica la separación explícita de componentes o palabras del mensaje final.

##### Desglose Pedagógico de los Primeros Caracteres:
* Coordenada `I:1:6`: Nos ubicamos en el Capítulo I, versículo 1:
  > *"1. Had! The manifestation of Nuit."*  
  Contando el 6.° carácter alfabético de la oración: `H` (1), `a` (2), `d` (3), `T` (4), `h` (5), **`h`** $\to$ produce el carácter **`h`**.
* Coordenada `I:2:15`: Nos ubicamos en el Capítulo I, versículo 2:
  > *"2. The unveiling of the company of heaven."*  
  Contando hasta el carácter número 15: obtenemos la letra **`t`**.
* Coordenada `I:3:26`: Nos ubicamos en el Capítulo I, versículo 3:
  > *"3. Every man and every woman is a star."*  
  Contando hasta la posición 26: obtenemos la letra **`t`**.

Al ejecutar este proceso de sustitución de coordenadas de manera consecutiva para cada una de las líneas suministradas en el archivo, se ensambla ordenadamente la siguiente secuencia de caracteres:
$$\texttt{h - t - t - p - s - : - / - / - b - i - t - . - l - y - / - 3 - 9 - p - w - 2 - N - H}$$

La cual conforma un enlace acortado funcional:
$$\mathbf{https://bit.ly/39pw2NH}$$

Registramos y validamos las respuestas en TryHackMe para completar la Task 6:

![[Informe-1794009774173.webp]]

---

### Task 7: The Final Song (Descubrimiento del Destino Final)

Ingresamos en el navegador web la dirección URL obtenida del cifrado de libro (`https://bit.ly/39pw2NH`). El servicio acortador nos redirige inmediatamente a una pista de audio alojada en la plataforma **SoundCloud**:

![[Informe-1794009835184.webp]]

* **URL de destino:** `https://soundcloud.com/user-984804993/the-instar-emergence`
* **Perfil de usuario / Artista:** `Cicada 3301`
* **Nombre de la canción:** **The Instar Emergence**

#### Contexto de la Pista Musical
En la edición real del año 2013 de Cicada 3301, la organización liberó un archivo musical titulado *The Instar Emergence* (originalmente denominado `761.mp3`). El término biológico **instar** alude a las fases ninfales de desarrollo por las que atraviesa un artrópodo (como la cigarra) entre mudas de exoesqueleto hasta alcanzar la madurez alada. La pieza musical contenía a su vez micro-variaciones tonales que escondían nuevos enigmas criptográficos para los finalistas mundiales.

Ingresamos el nombre de la canción (`The Instar Emergence`) en TryHackMe, concluyendo de manera definitiva y con puntaje perfecto la totalidad del reto.

---

## 4. Evidencias de Conclusión y Resultados en TryHackMe

La sala fue completada al 100%, superando con éxito las 7 tareas asignadas y acumulando los 80 puntos del laboratorio.

![[Informe-1794009911542.webp]]

Constancia de completación de la sala y felicitaciones de la plataforma:

![[Informe-1794009941002.webp]]

Enlace oficial de verificación de finalización de la sala en TryHackMe:
[TryHackMe Room Completion — Cicada 3301 Vol:1](https://tryhackme.com/room/cicada3301vol1?utm_campaign=social_share&utm_medium=social&utm_content=room&utm_source=copy&share_date=2026-11-06&sharerId=6ad66ad13107b1b0c9e94e81)

Registro en el perfil de salas completadas en TryHackMe:

![[Informe-1794010016770.webp]]

---

## 5. Conclusiones y Aprendizajes del Curso de Ciberseguridad

El desarrollo de este laboratorio aportó lecciones fundamentales que consolidan la formación técnica y metodológica en ciberseguridad:

1. **La Esteganografía como Complemento Vital de la Criptografía:**  
   Comprendimos en la práctica la diferencia operativa entre *criptografía* (hacer ininteligible el contenido de un mensaje) y *esteganografía* (ocultar la existencia misma de la comunicación). La combinación secuencial de ambas disciplinas (cifrar una contraseña mediante Vigenère para luego incrustarla en los coeficientes DCT de un JPEG o en las frecuencias inaudibles de un WAV) ilustra el principio de **Defensa en Profundidad (*Defense-in-Depth*)**.

2. **Relevancia del Análisis Forense Digital (DFIR) y la Integridad de Artefactos:**  
   En la fase de OutGuess y Steghide evidenciamos que cualquier compresión automática, reescalado de imagen o manipulación inadecuada destruye irremediablemente los bits menos significativos (LSB) y los coeficientes de frecuencia donde residen las cargas útiles. En el ejercicio profesional de la informática forense, la adquisición estricta bit a bit y el cálculo de hashes de verificación resultan innegociables para garantizar la validez probatoria de las evidencias.

3. **Criptoanálisis de Funciones Hash y Resistencia a la Preimagen:**  
   Analizamos cómo el estándar SHA-512 garantiza matemáticamente la integridad al ser irreversible por diseño computacional. La técnica de rotura mediante *Reverse Lookup* nos demostró que la seguridad de un hash depende directamente de la entropía del mensaje original: contraseñas cortas o predecibles sucumben ante tablas Rainbow, reforzando la imperiosa necesidad de utilizar técnicas de *salting* y funciones de derivación de claves robustas (como Argon2 o PBKDF2) en sistemas de autenticación reales.

4. **Automatización y Desarrollo de Scripts Personalizados:**  
   La implementación del algoritmo de Vigenère en Python demostró que un analista de seguridad no puede depender exclusivamente de herramientas gráficas automáticas. La capacidad de programar scripts a la medida permite depurar inconsistencias lógicas (como la necesidad de alternar entre desplazamiento de cifrado y descifrado), entender la aritmética modular subyacente y acelerar la resolución de incidentes en entornos de pentesting y respuesta a incidentes.

5. **Pensamiento Lateral e Inteligencia de Amenazas (OSINT):**  
   Retos de alta complejidad como Cicada 3301 enseñan que la ciberseguridad trasciende la ejecución mecánica de comandos: exige correlacionar pistas históricas, analizar metadatos contextuales y aplicar metodologías de investigación abierta para conectar vectores heterogéneos (audio, imagen, texto, código y audio en streaming).
