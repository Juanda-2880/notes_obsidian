#Universidad #MachineLearning  

----
### Series de Tiempo

¿Cómo se identifica una serie de Tiempo?

Es una variable que se mide a través del tiempo, la idea de este es hacer pronósticos, es empezar a hacer una especie de ideas a futuro. Esta información es super importante para las empresas, dado de que este les permite hacer una planeación y poder mirar a futuro, para hacer este necesitan hacer predicciones.

_La series de tiempo es un conjunto de observaciones que se recopilan después de intervalos regulares de tiempo_

Por ejemplo, estas series de tiempo si se toman diarios no se pueden cambiar a semanales, porque este cambia el tipo de datos o secuencia de estos

El Target siempre va a ser Cuantitativo, me interesa saber que va a suceder en los siguientes pasos, estos pasos en si terminan siendo una cantidad de tiempo que es importante para este y siendo de forma continua. Pero es importante comprender el negocio, saber cual es el dato objetivo al que se quiere llevar, que dato es al que le interesa.

Uno de los fundamentos de las series de tiempo es que se puede analizar el pasado para pronosticar el futuro, con esto también se sabe de que el pronostico incluye el tiempo como variable

Existen dos tipos de Pronósticos, en el cual se van a indagar en los método estádisticos:

- Los que usan métodos estadísticos tienen la opción de los Univariados que usan técnicas de series de tiempo cuyo objetivo es emplear la información contenida en el comportamiento histórico de la variable para pronosticar su comportamiento futuro.
- Los Multivariables emplean información de una o más variables diferentes a la variables de interés para pronosticar el comportamiento futuro (Un ejemplo es la regresión Múltiple)

![[Apuntes en Clase-1793410593516.webp]]

### Ejemplo

En una tienda se tienen las ventas mensuales de cada mes, la idea es conocer el valor de mercado que pide el cliente, ya sean las ventas de los próximos 12 meses o similar

![[Apuntes en Clase-1793407192549.webp]]

Esta serie de tiempo tiene una especie de continuidad y se pueden analizar muchas cosas de este mismo. 
### Corte Transversal

Es cuando se toma un conjunto de datos y estos se modelan, estos son los que se usan para después usarlos en Xgboost, Arboles de Decisión etc etc 

Para hacer este se necesitan:
- Variable Target/Respuesta (Y)
- Variables Predictoras (X)

### Ejemplo 

Entonces para los datos lo mejor es realizar el análisis de los datos, saber cuales son los datos a los que nos estamos enfrentando y saber cuál es el objetivo al que deseamos llegar

| Variables Predictoras                                                 | Variables Target o Respuesta |
| --------------------------------------------------------------------- | ---------------------------- |
| Ingresos / Deuda / Estrato / Edad / Nivel de Estudio / Genero / Hijos | Pagó / No pagó               |

¿Para qué se le pasa al Modelo Datos Históricos?

Con estos datos se entrena el modelo, en el caso del ejemplo se puede poner otro caso que tenga esos datos, pero el modelo puede hacer las probabilidades de que este funcione completamente con este mismo

En el Modelo el Input es el X y el Output es la Y

### Cuando usar Corte Transversal o Series de Tiempo

Se usa series de Tiempo cuando las variables son Numéricas/Continuas, si es categórica se usa el corte transversal. 

Con las series de tiempo es muy probable que te pasen varios datos. Muchas veces es bueno validar en ambos de estos modelos, dado de que muchas veces no se sabe cual es el valor que va a terminar llegando a producción, entonces estas cosas se deben de tener en cuenta para las series de Tiempo.

Una de las diferencias es que las Series de tiempo no depende de variables predictoras, es decir, otros datos que pueden ser las variables predictoras del corte transversal no se incluyen en esta.

### Qué es un Intervalo de Confianza?

La idea es que el modelo puede cometer errores, entonces es como una especie de rango en la cual podemos valer de que el modelo esta en un rango del arrojar un valor valido

Si se tiene menor confianza el intervalo de confianza es más estrecho y lo mismo en viceversa, si el rango de confianza es mayor se amplia más, estos tienen su formula pero esto es para decirle a alguien que se tiene un 95% de confianza, es posible que el pronostico se salga por este 5%.

---

### Pronósticos Discrecionales Indivduales

El "Ojímetro" es basado en la intuición

Los empresarios y emprendedores exitosos se caracterizan por tener la habilidad de hacer pronósticos acertado de la demanda y del comportamiento de ventas

### Pronósticos Discrecionales Grupales

La intuición de varias personas se supone que es superior al de una sola persona, en esta premisa se basa el llamado Jurado de Opinión Ejecutiva, Busca el término medio de opinión de diferentes integrantes de la empresa

Para que este sea eficiente, debe de incluir:
- A los gerentes tanto de mercado como de producción y la admin, supervisores y personal de venta
- También suele utilizarse métodos que incluyen la combinación de opiniones de expertos y no expertos

Otro apartado importante son los Métodos de Investigación del Mercado, Dado que se le solicita a un grupo de compradores o posibles compradores que expresen sus planes futuros de compra, ya sean con encuestas a clientes o población general.

Uno de los  más antiguos y los más usados son el Método de Delphi, este es un método que permite obtener y refinar "juicios" (Opiniones) de Grupos y sirve cuando el tema es uno en el que no existe conocimiento especifico.

Este proceso tiene 3 características
- Tiene respuestas anónimas
- Iteración y Retroalimentación Contralada
- La opinión del grupo corresponde a la media de las respuestas individuales

---

### Consideraciones para un Pronóstico Exitoso


Los pronósticos son extrapolaciones, para esto se usan diferentes protocolos de evaluación, todo esto se hace para saber cual es el mejor modelo a utilizar, el objetivo de cada uno de estos es escoger el mejor.

El Futuro es similar al pasado

#### 1. Hold Out (División Temporal 70/30 u 80/20)

Consiste en partir la serie temporal en un punto de corte cronológico fijo: los primeros periodos corresponden al entrenamiento (_in-sample_) y los últimos al conjunto de prueba (_out-of-sample_).

- **Para qué sirve:** Medir el error de extrapolación en los últimos $H$ periodos observados manteniendo el orden temporal estricto.
    
- **Cuándo aplicarlo:** Cuando se cuenta con series largas o como validación inicial de referencia (_baseline_).
    
- **Ventajas:** Computacionalmente económico y muy simple de interpretar.
    
- **Desventajas:** El desempeño evaluado depende enteramente del comportamiento puntual del segmento final (si hubo una anomalía o estacionalidad atípica al final, sesga la evaluación).
    

#### 2. K-Fold Cross Validation Tradicional

Consiste en dividir aleatoriamente los datos en $K$ particiones, entrenando en $K-1$ y evaluando en la restante.

- **Para qué sirve y cuándo aplicarlo:** Exclusivo de **datos de corte transversal** (clasificación o regresión sin dependencia secuencial, como predecir impago de créditos o fuga de clientes).
    
- **Incompatibilidad con Series de Tiempo:** **No debe usarse en series temporales puras**, porque toma datos de $t+1$ para entrenar un modelo que predice en $t$, rompiendo la estructura autoregresiva.
    
- **Ventajas:** Aprovecha todos los datos para entrenar y evaluar en corte transversal.
    
- **Desventajas:** Fuga masiva de información si se utiliza erróneamente sobre series cronológicas.
    

#### 3. Time Series Cross-Validation (Ventana Móvil / Ventana Recursiva)

El equivalente válido de validación cruzada para series temporales: evalúa el modelo iterativamente a lo largo del tiempo.

- **Variantes principales:**
    
    - **Ventana Recursiva / Expansiva (_Expanding Window_):** Se inicia con una muestra base y en cada iteración se suma una observación al entrenamiento para proyectar $h$ pasos adelante.
        
    - **Ventana Móvil (_Rolling Window_):** El tamaño de la muestra de entrenamiento se mantiene constante, descartando la observación más antigua al incorporar la más reciente.
        
- **Cuándo aplicarlo:** Para seleccionar hiperparámetros y comparar modelos (ARIMA, suavizamiento exponencial, modelos de Machine Learning) de forma robusta.
    
- **Ventajas:** Evalúa la estabilidad del modelo a través de múltiples horizontes sin violar la causalidad temporal.
    
- **Desventajas:** Mayor costo computacional al requerir reentrenar o actualizar el modelo en múltiples rondas

----

