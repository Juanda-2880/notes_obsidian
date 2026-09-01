**Curso:** Ciberseguridad 
**Integrantes:**

- Juan David Pacheco Vargas — A00401844
- Esteban Guarin Valencia — A00402028

----
## 1. Objetivo

Verificar la **integridad** y la **autenticidad** de la imagen ISO de Lubuntu descargada (`lubuntu-16.04.6-desktop-amd64.iso`), mediante el cálculo de su hash SHA-256 y la comprobación de la firma digital GPG del archivo de sumas de verificación oficial publicado por Canonical.

## 2. Archivos utilizados

![[Tarea 3-1788235389890.webp]]


Del directorio oficial de descargas de Lubuntu 16.04.6 (`cdimage.ubuntu.com/lubuntu/releases/16.04/release/`) se descargaron los siguientes archivos, junto con la imagen ISO:

![[Tarea 3-1788235451473.webp]]

| Archivo                             | Descripción                                                                  |
| ----------------------------------- | ---------------------------------------------------------------------------- |
| `lubuntu-16.04.6-desktop-amd64.iso` | Imagen de disco a verificar                                                  |
| `SHA256SUMS`                        | Archivo con los hashes SHA-256 oficiales de todas las imágenes de la release |
| `SHA256SUMS.gpg`                    | Firma digital GPG del archivo `SHA256SUMS`, generada por Canonical           |

----

## 3. Procedimiento y resultados

### 3.1. Cálculo del hash SHA-256 de la ISO

Se calculó el hash de la imagen descargada con el comando:

![[Tarea 3-1788235526473.webp]]

### 3.2. Comparación manual contra el archivo oficial

Se buscó la línea correspondiente a la ISO dentro del archivo `SHA256SUMS` oficial, para comparar manualmente el hash publicado por Canonical contra el hash calculado localmente:

![[Tarea 3-1788235618996.webp]]

**Comparación:** el hash calculado en el punto 3.1 y el hash publicado oficialmente en `SHA256SUMS` son **idénticos**, lo cual confirma que el archivo descargado no sufrió alteración ni corrupción durante la descarga.

### 3.3. Verificación automática del hash

Como alternativa a la comparación manual, se utilizó la opción de verificación automática de `sha256sum`, que compara directamente el archivo local contra todas las entradas del archivo `SHA256SUMS`:

![[Tarea 3-1788235686527.webp]]

El estado `OK` confirma automáticamente que el hash de la ISO coincide con el valor esperado, sin necesidad de comparación visual.

----

Para poder verificar la autenticidad de la firma GPG, fue necesario importar las claves públicas oficiales con las que Canonical firma el archivo `SHA256SUMS`:

![[Tarea 3-1788235854921.webp]]

### 3.5. Verificación de la firma digital

Finalmente, se verificó que el archivo `SHA256SUMS` efectivamente fue firmado por Canonical y no ha sido modificado, usando:

![[Tarea 3-1788236361485.webp]]

**Análisis del resultado:**

- **`Good signature from "Ubuntu CD Image Automatic Signing Key (2012)"`**: confirma que el archivo `SHA256SUMS` fue firmado con la clave privada correspondiente a la clave pública oficial de Canonical, y que su contenido no ha sido alterado desde que fue firmado.
- **`WARNING: This key is not certified with a trusted signature`**: esta advertencia es **esperada y no invalida la verificación**. Solo indica que ninguno de los dos integrantes ha firmado personalmente la clave pública de Ubuntu (no existe una cadena de confianza local, "web of trust"), pero no significa que la firma sea inválida o que el archivo esté comprometido.

----

## 4. Conclusiones

1. El hash SHA-256 calculado localmente (`d069c1595b91673648b72664bcaffa8f0dad908e0010332bf847cdaab4f87229`) coincide exactamente con el valor publicado oficialmente por Canonical, lo que garantiza la **integridad** de la imagen descargada: el archivo no fue modificado ni corrompido durante la transferencia.

2. La verificación con `gpg --verify` arrojó una **firma válida (`Good signature`)**, generada con la clave oficial de Canonical para imágenes de Ubuntu/Lubuntu, lo que garantiza la **autenticidad** del archivo `SHA256SUMS` y, por extensión, de los hashes que contiene.

3. En conjunto, ambas verificaciones confirman que la imagen `lubuntu-16.04.6-desktop-amd64.iso` descargada es **legítima, íntegra y proviene de una fuente confiable (Canonical)**, sin evidencia de manipulación por parte de terceros (por ejemplo, un mirror comprometido o un ataque de tipo man-in-the-middle durante la descarga).

4. Este procedimiento ilustra la diferencia entre **integridad** (verificada mediante el hash) y **autenticidad** (verificada mediante la firma digital): un hash correcto solo demuestra que el archivo no cambió respecto a un valor de referencia, mientras que la firma digital demuestra además que ese valor de referencia proviene realmente de quien dice haberlo publicado.