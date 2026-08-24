#Universidad #Ciberseguridad #Preguntas 

### 1. Algoritmo DES (Data Encryption Standard)

- **Tamaño de bloque:** 64 bits (tanto para entrada como para salida).
    
- **Tamaño de clave:** 56 bits efectivos (la clave original es de 64 bits, pero 8 bits se reservan para paridad).
    
- **Razón de desuso:** Su longitud de clave (56 bits) es demasiado corta para los estándares actuales. Hoy en día es fácilmente vulnerable a **ataques de fuerza bruta** (el espacio de claves puede ser recorrido exhaustivamente en cuestión de horas con hardware moderno).
    

### 2. Uso de la función XOR en criptografía

La operación lógica XOR ($\oplus$) es la base de gran parte de la criptografía moderna por dos razones fundamentales:

- **Reversibilidad perfecta:** Si aplicas XOR a un mensaje con una clave, y luego aplicas XOR al resultado con la misma clave, recuperas el mensaje original ($A \oplus B = C$ y $C \oplus B = A$).
    
- **Eficiencia computacional:** Es una operación binaria nativa en los procesadores, lo que la hace extremadamente rápida y de bajo costo en consumo de recursos.
    

### 3. Algoritmo AES (Advanced Encryption Standard)

- **Tamaño de bloque:** 128 bits (entrada y salida fijas).
    
- **Tamaños de clave admitidos:** 128, 192 o 256 bits. (A mayor tamaño, mayor seguridad y rondas de cifrado).
    

### 4. Modalidad de cifrado ECB (Electronic Codebook)

- **¿En qué consiste?:** Divide el mensaje en bloques del mismo tamaño y cifra cada bloque de manera completamente **independiente** utilizando la misma clave.
    
- **¿Por qué no es aconsejable?:** No oculta los patrones de los datos subyacentes. Si dos bloques de texto plano son idénticos, producirán exactamente el mismo bloque de texto cifrado. Esto hace que formatos estructurados (como imágenes o bases de datos) dejen ver su estructura original tras ser cifrados.
    

### 5. El rol del IV (Vector de Inicialización) en la modalidad CBC

El modo CBC (Cipher Block Chaining) encadena los bloques. El IV actúa introduciendo **aleatoriedad inicial** al proceso. Se aplica mediante una operación XOR al _primer bloque_ de texto plano antes de que pase por el algoritmo de cifrado.

- **Propósito principal:** Garantiza que si se cifra el mismo mensaje exacto dos veces con la misma clave, el texto cifrado resultante sea completamente diferente en ambas ocasiones, previniendo análisis de patrones.
    

### 6. Modalidad OFB (Output Feedback) para flujos de audio y video

OFB convierte un cifrador de bloques en un cifrador de flujo. Es ideal para streaming (audio/video) debido a que **no propaga errores de bits**.

- Si durante la transmisión de la red un bit del texto cifrado se altera o se pierde, al descifrar solo se corromperá ese bit específico. En los formatos de audio y video, un bit erróneo apenas representa un micro-ruido o un píxel muerto temporal, mientras que en otros modos de cifrado (como CBC), un solo bit erróneo corrompe un bloque entero de datos, lo que arruinaría la decodificación del flujo multimedia.
    

### 7. Modalidad de cifrado CTR (Counter Mode)

- **¿Cómo funciona?:** Se toma un valor inicial (Nonce) combinado con un contador secuencial (Ej: Nonce+1, Nonce+2...). Este contador se cifra para generar un "flujo de claves" (keystream) seudoaleatorio. Finalmente, este keystream se combina con el texto plano usando la operación XOR.
    
- **¿Por qué emplea siempre la función de cifrado?:** Porque el algoritmo de cifrado (ej. AES) **no se aplica a los datos**, sino exclusivamente al contador para generar el keystream.
    
- **¿Cómo efectúa el descifrado?:** Exactamente igual. El receptor vuelve a cifrar el mismo contador para regenerar el mismo keystream. Al aplicar XOR de este keystream contra el texto cifrado, los datos regresan a su estado original (gracias a la reversibilidad del XOR explicada en la pregunta 2).