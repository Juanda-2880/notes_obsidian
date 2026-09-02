**Curso:** Ciberseguridad 
**Integrantes:**

- Juan David Pacheco Vargas — A00401844
- Esteban Guarin Valencia — A00402028

----

## 1. Resumen 
El presente informe detalla el análisis del proceso de criptoanálisis y descifrado realizado sobre un conjunto de tres archivos en cadena. A través de pistas sucesivas, se aplicaron técnicas de descifrado simétrico utilizando la herramienta `openssl`, específicamente con el algoritmo AES en distintas modalidades y tamaños de clave.

## 2. Metodología y Herramientas
Se empleó la herramienta `openssl` desde la línea de comandos para efectuar los procesos de descifrado, analizando la información obtenida de archivos de texto externos (`Pista.txt`) y de los propios mensajes resultantes de cada iteración de descifrado.

## 3. Análisis Paso a Paso del Proceso

### 3.1. Fase 1: Descifrado del Archivo 1 (`Archivo1.cif.txt`)
**Contexto y Pista:** El documento inicial `Pista.txt` indicaba que el primer archivo fue cifrado con el algoritmo AES en modo CTR. Además, señalaba que la contraseña correspondía al año de fundación de la Universidad Icesi, pero transformado: cada dígito debía ser convertido a su representación binaria de 4 bits y concatenado.

**Resolución de la Pista:**
1. Año de fundación de la Universidad Icesi: **1979**.
2. Conversión a binario (4 bits por dígito):
   - `1` $\rightarrow$ `0001`
   - `9` $\rightarrow$ `1001`
   - `7` $\rightarrow$ `0111`
   - `9` $\rightarrow$ `1001`
3. Concatenación: La contraseña resultante es `0001100101111001`.

**Ejecución:**
Con la contraseña derivada y sabiendo que se empleaba AES en modo CTR (se determinó el uso específico de la variante de 192 bits), se ejecutó el siguiente comando, incorporando PBKDF2 para la derivación segura de la clave:
```bash
openssl enc -d -aes-192-ctr -in Archivo1.cif.txt -out Archivo1Des.txt -pass pass:0001100101111001 -pbkdf2
```
**Resultado:** El archivo se descifró con éxito, revelando un mensaje de felicitación y la pista necesaria para continuar con el siguiente archivo.

### 3.2. Fase 2: Descifrado del Archivo 2 (`Archivo2.cif.txt`)
**Contexto y Pista:** El texto recuperado del primer archivo indicó que este segundo documento utilizaba el algoritmo AES en modalidad CBC. La pista especificó que la clave estaba formada por las letras de una dirección MAC de *broadcast* (con opciones de formato en mayúsculas/minúsculas) y proporcionó directamente el Vector de Inicialización (IV): `0123456789abcdeffedcba9876543210`.

**Resolución de la Pista:**
1. Una dirección MAC de broadcast es `FF:FF:FF:FF:FF:FF`.
2. Tomando únicamente las letras y ajustando a mayúsculas (como sugirió una de las opciones de la pista) para rellenar los 128 bits de una clave AES-128, se requieren 32 caracteres hexadecimales.
3. La clave hexadecimal construida es: `FFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFF`.

**Ejecución:**
Al tener la clave en hexadecimal (`-K`) y el vector de inicialización (`-iv`), se pasaron directamente al comando, descartando el uso de una contraseña humana (como `-pass`):
```bash
openssl enc -d -aes-128-cbc -in Archivo2.cif.txt -out Archivo2Des.txt -K FFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFF -iv 0123456789abcdeffedcba9876543210
```
**Resultado:** El archivo fue vulnerado correctamente, exponiendo el mensaje oculto y los parámetros completos para resolver la última fase.

### 3.3. Fase 3: Descifrado del Archivo 3 (`Archivo3.cif.txt`)
**Contexto y Pista:** El mensaje descifrado en la fase anterior reveló que el último documento estaba protegido mediante AES en modalidad CTR. Esta vez, las credenciales criptográficas fueron entregadas explícitamente en texto claro:
- **Clave:** `0123456789abcdef0123456789abcdeffedcba9876543210fedcba9876543210` (Al tener 64 caracteres hex, equivale a 32 bytes o 256 bits, indicando AES-256).
- **IV:** `11112222333344445555666677778888`.

**Ejecución:**
```bash
openssl enc -d -aes-256-ctr -in Archivo3.cif.txt -out Archivo3Des.txt -K 0123456789abcdef0123456789abcdeffedcba9876543210fedcba9876543210 -iv 11112222333344445555666677778888
```
**Resultado:** El descifrado exitoso presenta un acertijo final de lógica (*"Si en una mesa de un restaurante hay 10 moscas y matas dos moscas, cuantas moscas quedan?"*), marcando la conclusión exitosa del reto criptográfico.

