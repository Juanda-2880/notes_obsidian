#Ciberseguridad #Universidad 
#### Vídeo #1
Fuente: https://www.youtube.com/watch?v=AQDCe585Lnc

**¿Qué es Encriptar?** Encriptar es el proceso de tomar un mensaje original (texto plano) y "desordenar" o cifrar su contenido mediante un algoritmo matemático, de forma que se convierta en información ilegible (texto cifrado). El objetivo es que solo las personas que posean la autorización (y la llave o contraseña correcta) puedan devolver el texto a su forma original y leerlo.

Existen dos tipos principales de encriptado:

1. **Encriptado Simétrico:** Utiliza **la misma clave (contraseña)** tanto para encriptar el mensaje como para desencriptarlo. Su gran problema es logístico: ¿Cómo comparten el emisor y el receptor esa clave de forma segura sin que un hacker la intercepte en el camino?
    
2. **Encriptado Asimétrico (o de clave pública):** Soluciona el problema de tener que compartir claves. Utiliza un par de claves que están vinculadas matemáticamente: una **Clave Pública** (que todo el mundo puede conocer) y una **Clave Privada** (que debes guardar en absoluto secreto y no compartir con nadie). Si alguien quiere enviarte un archivo, lo cifra utilizando tu _clave pública_. Una vez cifrado, es matemáticamente imposible revertirlo, a menos que el destinatario utilice su _clave privada_ correspondiente.
    
    - _Analogía:_ Es como un buzón de correos público en la calle. Cualquiera puede introducir un mensaje por la ranura (cifrar con clave pública), pero solo el dueño del buzón tiene la llave (clave privada) para abrirlo y leer el contenido.
        

**Usos de Algoritmos Asimétricos (Ej. RSA):**

- **HTTPS y SSL/TLS:** Se utiliza cada vez que visitas una página web con el candado de seguridad. La criptografía asimétrica se usa al inicio de la conexión para intercambiar de manera segura una clave temporal simétrica, protegiendo tus datos bancarios y contraseñas en el navegador.
    
- **SSH (Secure Shell):** Es un protocolo usado por administradores de sistemas para conectarse y controlar servidores remotos a través de una red insegura (como Internet) sin que nadie pueda espiar los comandos.
    
- **PGP (Pretty Good Privacy) o GPG:** Herramientas populares para encriptar y firmar digitalmente correos electrónicos. Garantizan que solo el destinatario pueda leer el correo y verifican que quien lo envió es verdaderamente quien dice ser.
    
- **Bitcoin (Criptomonedas):** Utiliza criptografía asimétrica para generar la dirección de tu billetera (clave pública) y tus firmas digitales. La clave privada es lo que te da la propiedad sobre tus bitcoins; solo con ella puedes autorizar transferencias.

#### Vídeo #2
Fuente: https://www.youtube.com/watch?v=2BldESGZKB8


**¿Qué es el Hashing?** El Hashing es el proceso de convertir una entrada de datos de cualquier longitud (ya sea una palabra corta o un libro entero) en una cadena de texto de un tamaño fijo (letras y números) utilizando una función matemática. La entrada se conoce como _Input_, el algoritmo es la _Hash Function_, y el resultado es el _Hash Value_. Existen múltiples algoritmos como MD5, SHA-1 o SHA-256 (el que usa Bitcoin).

**Cualidades fundamentales de un Hash Seguro:**

1. **Valor único y determinista:** Si introduces exactamente el mismo texto, siempre obtendrás el mismo Hash. Sin embargo, es estadísticamente imposible que dos textos diferentes produzcan el mismo Hash (a esto se le llama evitar "colisiones").
    
2. **Unidireccional e Irreversible (Seguridad):** Es facilísimo calcular el Hash a partir de un archivo, pero es imposible tomar el valor del Hash y hacer el proceso inverso para adivinar el archivo original.
    
3. **Efecto Avalancha:** El más mínimo cambio en el archivo original (por ejemplo, cambiar una "a" por una "e" o añadir una simple coma) generará un valor Hash radicalmente diferente al anterior.
    
4. **Velocidad:** Los algoritmos están diseñados para ejecutarse y devolver un valor de manera rápida y eficiente.
    

**El Hashing en la Blockchain:** En redes como Blockchain, los Hashes son la columna vertebral. Cada transacción (que contiene el remitente, monto y hora) se hashea para producir un **TXID (Transaction ID)**. Además, los bloques de transacciones se enlazan unos con otros creando la cadena de bloques. Cada nuevo bloque incluye el Hash matemático del bloque anterior. De esta manera, si un hacker intenta modificar una sola transacción del pasado, el hash de ese bloque cambiaría por el _efecto avalancha_, lo que invalidaría inmediatamente todos los bloques siguientes en la red. Esto garantiza que la información sea completamente **inmutable**.

![[Preparación Clase-1785861190793.webp]]


----

### Respuestas a las Preguntas de Preparación
#Preguntas 

**1. ¿Cuál es la diferencia entre criptografía y criptoanálisis?**

La criptografía es el arte y la ciencia de la escritura secreta; se enfoca en crear sistemas y algoritmos para ocultar o cifrar información, protegiéndola de accesos no autorizados. Por otro lado, el criptoanálisis es la disciplina opuesta: intenta romper y descifrar esos códigos o algoritmos criptográficos sin tener acceso inicial a la clave, buscando vulnerabilidades en el diseño del sistema.

**2. Explique brevemente cómo es el proceso de cifrado.**

El proceso comienza con un mensaje original en formato legible, conocido como "texto claro" (plaintext). Este mensaje se procesa mediante un algoritmo matemático de encriptación junto con una clave (key) para alterar su estructura y producir información totalmente ininteligible, conocida como "texto cifrado" (ciphertext). Para que el receptor pueda leerlo, el texto cifrado debe pasar por un proceso inverso de desencriptación utilizando la clave adecuada.

**3. Investigue cómo funciona el Cifrado Cesar (Caesar's cypher) y moviendo 5 posiciones hacia adelante cifre lo siguiente: `CRYPTOGRAPHY IS FUN`.**

El cifrado César es un tipo de cifrado de sustitución en el que cada letra del texto original es reemplazada por una letra que se encuentra un número fijo de posiciones más adelante en el alfabeto. Si se llega a la 'Z', el ciclo continúa empezando por la 'A'.

- _Desplazamiento:_ +5 posiciones.
    
- _Texto:_ CRYPTOGRAPHY IS FUN
    
- _Cifrado paso a paso:_ C(+5)=H, R(+5)=W, Y(+5)=D...
    
- **Resultado:** `HWDYUTLWFUMD NX KZS`
    

**4. Investigue cómo funciona el cifrado Vigenère y cifre la frase: `CRYPTOGRAPHY IS FUN` usando `BOAT` como clave.**

El cifrado Vigenère es una sustitución polialfabética que utiliza una palabra clave que se repite para cifrar las letras, haciéndolo más seguro que el César porque la misma letra original puede cifrarse de maneras distintas. Se suman los valores numéricos de la letra del mensaje (A=0, B=1...) con la de la letra de la clave.

- Mensaje: C R Y P T O G R A P H Y | I S | F U N
    
- Clave: B O A T B O A T B O A T | B O | A T B _(La clave se repite saltando espacios)_
    
- Ejemplo matemático: C (valor 2) + B (valor 1) = D (valor 3). R (valor 17) + O (valor 14) = 31. Como pasa de 25, restamos 26 = F (valor 5).
    
- **Resultado:** `DFYIUCGKBDHR JG FNO`
    

**5. Investigue qué fue la máquina Enigma y en qué año aproximadamente fue usada y bajo qué contexto.**

La máquina Enigma fue un avanzado dispositivo electromecánico de rotores utilizado para la encriptación y desencriptación de mensajes secretos. Aunque fue inventada a finales de la Primera Guerra Mundial con fines comerciales, es mundialmente famosa por su uso militar durante la **Segunda Guerra Mundial (aprox. 1939 - 1945)**. Fue el estándar de las comunicaciones secretas de la **Alemania nazi**. Su fuerza radicaba en que los rotores cambiaban el cifrado eléctrico con cada tecla que se presionaba. El éxito de los Aliados (destacando a Alan Turing) al descifrar el código Enigma fue vital para acortar la guerra.

**6. Explique cómo funciona el cifrado simétrico y ¿cuál es su principal desventaja?**

El cifrado simétrico funciona utilizando una **única clave compartida** tanto para el proceso de encriptación como para la desencriptación del mensaje.

- **Desventaja Principal:** El problema de la "Distribución de la clave". Si Alice y Bob se quieren comunicar, ambos necesitan tener la clave secreta. Si están lejos, enviar esa clave a través de un canal inseguro puede provocar que un atacante la intercepte. Además, a gran escala, si un usuario necesita comunicarse de forma segura con miles de personas, tendría que recordar y gestionar miles de claves simétricas distintas.
    

**7. Explique cómo funciona el cifrado asimétrico y cuáles son sus principales ventajas y desventajas.**

Utiliza un sistema de dos claves separadas pero relacionadas matemáticamente: una clave pública que se puede distribuir a todo el mundo sin riesgo, y una privada que el dueño nunca debe revelar. Si la persona A cifra un dato con tu clave pública, solo tú podrás abrirlo con tu clave privada.

- **Ventajas:** Elimina la enorme vulnerabilidad de tener que distribuir y enviar una clave compartida a través de Internet. Permite la generación de Firmas Digitales y mecanismos sólidos de Autenticación, asegurando la identidad de la persona sin lugar a dudas y evitando el "repudio".
    
- **Desventajas:** Los algoritmos de clave pública (como RSA) demandan muchos más recursos del procesador y son muchísimo más lentos que los métodos simétricos. (Por eso a menudo se combinan ambos métodos en la vida real).
    

**8. ¿Qué es un hash y para qué puede ser útil?**

Un hash, también conocido como "transformación unidireccional" o "message digest", es una función matemática que procesa un mensaje o archivo de cualquier longitud y lo convierte en un número o código de un tamaño corto y predeterminado.

Es extremadamente útil en ciberseguridad para:

- **Hashing de contraseñas:** Los sistemas no guardan contraseñas en texto claro (por si un hacker roba la base de datos). Solo guardan el hash de la contraseña y validan que el hash coincida cada vez que intentas iniciar sesión.
    
- **Verificación de Identidad e Integridad (Fingerprint/MIC):** Si descargas un programa grande, puedes calcular su hash y compararlo con el original. Si coincide, tienes la certeza absoluta de que el archivo no fue modificado por un atacante inyectando un virus y garantizas su integridad.
    
- **Eficiencia en Firmas Digitales:** Dado que encriptar asimétricamente un archivo pesado consume mucho tiempo de procesador, es más eficiente calcular primero el hash corto del mensaje y aplicar la firma digital únicamente sobre ese hash.