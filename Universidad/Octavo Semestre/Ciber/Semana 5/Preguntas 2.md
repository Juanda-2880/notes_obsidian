#Ciberseguridad #Universidad #Preguntas 

### 1. Principio de funcionamiento básico del algoritmo de clave pública

La criptografía de clave pública, también conocida como **criptografía asimétrica**, es un método para cifrar o firmar datos utilizando un par de claves matemáticamente vinculadas:

- **Clave pública:** Está disponible para que cualquier persona o sistema la utilice para cifrar un mensaje.
    
- **Clave privada:** Se mantiene en estricto secreto por el propietario. Los datos encriptados con la clave pública solo pueden desencriptarse con esta clave privada.
    

Este sistema es de uso generalizado y resulta extremadamente útil para establecer comunicaciones seguras en internet. Es el pilar de protocolos como TLS/SSL, lo que hace posible la navegación segura mediante HTTPS. En este contexto, la clave pública se comparte abiertamente, mientras que la clave privada permanece instalada y protegida en el servidor de origen.

Durante el _handshake_ (protocolo de enlace) de TLS, la criptografía de clave pública se utiliza para autenticar la identidad del servidor y para proteger el intercambio de los datos que generarán las **claves de sesión**. Un algoritmo de intercambio de claves, como RSA o Diffie-Hellman, utiliza este par de claves para acordar una clave secreta compartida. Una vez finalizado el _handshake_, la comunicación cambia a **encriptación simétrica** (usando la clave de sesión acordada), ya que es computacionalmente más rápida.

Los clientes y servidores acuerdan nuevas claves de sesión para cada comunicación. De esta manera, si un agente malicioso logra interceptar o robar una clave de sesión antigua, no podrá desencriptar las comunicaciones de sesiones previas o futuras.

### 2. ¿Cómo considera que es el proceso matemático con el cual trabaja RSA?

El algoritmo RSA (Rivest-Shamir-Adleman) basa su seguridad en la **dificultad computacional de factorizar números enteros gigantes** (el producto de dos números primos muy grandes). El proceso matemático se divide en tres fases:

**A. Generación de claves:**

1. Se eligen dos números primos muy grandes elegidos al azar, $p$ y $q$.
    
2. Se calcula el módulo $n = p \times q$. (Este valor $n$ formará parte de ambas claves).
    
3. Se calcula la función indicatriz de Euler: $\phi(n) = (p-1) \times (q-1)$.
    
4. Se elige un exponente público $e$ que sea coprimo con $\phi(n)$ (es decir, que el máximo común divisor entre $e$ y $\phi(n)$ sea 1) y donde $1 < e < \phi(n)$.
    
5. Se calcula el exponente privado $d$, el cual es el inverso multiplicativo modular de $e$. Esto significa que $d$ debe satisfacer la congruencia:
    
    $$d \times e \equiv 1 \pmod{\phi(n)}$$
    
6. La **Clave Pública** es el par $(e, n)$ y la **Clave Privada** es el par $(d, n)$.
    

**B. Proceso de Cifrado (Encriptación):**

Para cifrar un mensaje $M$ (convertido previamente en un número menor que $n$), el emisor utiliza la clave pública del receptor y calcula el texto cifrado $C$ mediante:

$$C = M^e \pmod{n}$$

**C. Proceso de Descifrado:**

Para descifrar el mensaje $C$, el receptor utiliza su clave privada $d$ y calcula:

$$M = C^d \pmod{n}$$

Las propiedades matemáticas garantizan que al elevar el texto cifrado a la potencia de $d$, se recupera el mensaje original $M$.

### 3. Ventajas y desventajas de RSA

**Ventajas:**

- **Alta seguridad:** Respaldado por décadas de escrutinio criptoanalítico; mientras las claves sean suficientemente largas (ej. 2048 o 4096 bits), es computacionalmente inviable romperlo con tecnología actual.
    
- **Dualidad de funciones:** Permite tanto el cifrado de datos (confidencialidad) como la generación de firmas digitales (autenticación y no repudio).
    
- **Distribución de claves:** Resuelve el problema de la criptografía simétrica, ya que la clave pública puede viajar por canales inseguros sin comprometer el sistema.
    
- **Estandarización:** Es uno de los algoritmos más soportados y ampliamente integrados en protocolos de red y software (PKI, certificados X.509, SSH, PGP).
    

**Desventajas:**

- **Lentitud computacional:** Es matemáticamente pesado (exponenciación modular). Es unas 100 veces más lento que algoritmos simétricos como AES, por lo que no se usa para cifrar grandes volúmenes de datos, sino solo para cifrar claves de sesión o _hashes_.
    
- **Tamaño de clave:** Requiere claves muy largas (mínimo 2048 bits hoy en día) para mantener un margen de seguridad aceptable, lo que consume más recursos de almacenamiento y ancho de banda en los _handshakes_.
    
- **Vulnerabilidad cuántica:** Es teóricamente vulnerable al Algoritmo de Shor; un ordenador cuántico con suficientes qubits podría factorizar el módulo $n$ en tiempo polinomial, rompiendo el cifrado.
    

### 4. ¿Cómo funciona el algoritmo de curvas elípticas (ECC)?

La Criptografía de Curva Elíptica (ECC) es una alternativa moderna a RSA. En lugar de usar la factorización de números primos, basa su seguridad matemática en la estructura algebraica de las curvas elípticas sobre campos finitos. Una curva típica tiene la forma matemática $y^2 = x^3 + ax + b$.

**Funcionamiento:**

1. Se define un punto base generador $G$ sobre la curva elíptica.
    
2. Para crear un par de claves, un usuario elige un número entero aleatorio muy grande como su **clave privada**, al cual llamaremos $k$.
    
3. La **clave pública** $P$ se calcula multiplicando el punto base por la clave privada: $P = k \times G$.
    
    - _Nota:_ Esta "multiplicación" en curvas elípticas no es aritmética tradicional, sino operaciones geométricas de sumar un punto consigo mismo repetidas veces sobre la curva.
        

**¿Por qué es seguro? (El problema del logaritmo discreto en curvas elípticas):**

Es extremadamente fácil calcular $P$ si conoces $k$ y $G$. Sin embargo, si un atacante solo conoce la clave pública $P$ y el punto base $G$, es computacionalmente inviable hacer la operación inversa para descubrir la clave privada $k$.

**Gran ventaja:** ECC ofrece el mismo nivel de seguridad que RSA pero con claves drásticamente más pequeñas (una clave ECC de 256 bits ofrece una seguridad equivalente a una clave RSA de 3072 bits). Esto se traduce en menor consumo de CPU, memoria y batería, haciéndolo ideal para dispositivos móviles e IoT.

### 5. ¿Cómo funciona el algoritmo Diffie-Hellman? Ventajas y desventajas

Diffie-Hellman (DH) no es un algoritmo de cifrado en sí, sino un **protocolo de intercambio de claves**. Permite que dos partes (ej. Alice y Bob) que no se conocen previamente acuerden un secreto compartido a través de un canal público e inseguro.

**Proceso matemático:**

1. Alice y Bob acuerdan públicamente dos números: un número primo grande $p$ y una base $g$ (generador). Estos viajan en texto claro.
    
2. Alice elige un número secreto aleatorio $a$ (su clave privada) y calcula su valor público $A = g^a \pmod{p}$.
    
3. Bob elige un número secreto aleatorio $b$ (su clave privada) y calcula su valor público $B = g^b \pmod{p}$.
    
4. Intercambian sus valores públicos $A$ y $B$ a través del canal inseguro.
    
5. Alice toma el valor de Bob ($B$) y calcula: $S = B^a \pmod{p}$.
    
6. Bob toma el valor de Alice ($A$) y calcula: $S = A^b \pmod{p}$.
    

El secreto compartido $S$ es exactamente el mismo para ambos, ya que matemáticamente: $(g^b)^a \pmod{p} = (g^a)^b \pmod{p} = g^{ab} \pmod{p}$. Un atacante que observe $p, g, A$ y $B$ no puede deducir $S$ gracias al problema del logaritmo discreto.

**Ventajas:**

- **Intercambio seguro sin conocimiento previo:** Permite establecer una clave simétrica sin necesidad de enviarla por la red.
    
- **Perfect Forward Secrecy (PFS):** Si se utiliza su variante efímera (DHE o ECDHE), se generan nuevos parámetros para cada sesión. Si un atacante roba la clave privada de un servidor a futuro, no podrá descifrar las sesiones pasadas grabadas.
    

**Desventajas:**

- **Falta de autenticación:** Por sí solo, Diffie-Hellman no verifica la identidad de las partes. Esto lo hace vulnerable a ataques de intermediario (_Man-in-the-Middle_ o MitM), donde el atacante intercepta las comunicaciones y negocia secretos por separado con Alice y Bob. Por esto, siempre se implementa junto con algoritmos de firmas digitales (como RSA o ECDSA).
    
- **Costo computacional:** Las operaciones de exponenciación modular consumen bastantes recursos del procesador (aunque esto se mitiga usando su variante basada en curvas elípticas, ECDHE).