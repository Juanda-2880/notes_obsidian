#Auto #PlatsII #Universidad #QA

### ¿Qué es la Pirámide de Pruebas?

Es un _framework_ visual (conceptualizado originalmente por Mike Cohn) que sirve como guía para estructurar estrategias de _testing_ de software automatizado. Propone cómo agrupar las pruebas en capas y determina la cantidad ideal de pruebas que se deben escribir en cada una de ellas para mantener una base de código saludable, rápida y mantenible.

Originalmente consta de tres capas principales, pero se rige por dos reglas de oro:

1. **Diferente granularidad:** Escribe pruebas con diferentes niveles de profundidad (desde una función matemática hasta el flujo completo de un usuario).
    
2. **Proporción inversa:** Mientras más alta sea la capa en la pirámide, **menor** debe ser la cantidad de pruebas automatizadas.



![[Pirámide de Pruebas-1787531144778.webp]]



### 1. Capa Base: Pruebas Unitarias (Unit Tests)

Es la base de la pirámide y **debe contener la mayor cantidad de pruebas**.

- **¿Qué son?** Verifican si una unidad de código (una función, un método o una clase) funciona como se espera en condiciones de aislamiento total. No se conectan a bases de datos ni a redes.
    
- **Cobertura:** Deben estar muy ligadas al código base y cubrir todos los escenarios no triviales. Esto incluye el _"Happy Path"_ (el flujo ideal donde todo sale bien) y los _"Edge Cases"_ (casos límite o extremos).
    
- **El mito del 100%:** Aunque mencionas que "nunca se debe publicar si hay menos del 100%", en la industria real buscar el 100% de _cobertura de código_ suele ser un error (genera pruebas inútiles). Lo correcto es decir que **el 100% de las pruebas unitarias que evalúan el funcionamiento crítico e imprescindible del negocio deben pasar exitosamente** antes de lanzar una versión.


### 2. Capa Media: Pruebas de Servicios (Integración)

Una vez que sabes que las unidades de código funcionan por separado, debes probar si funcionan al unirlas.

- **¿Qué evalúan?** Verifican la integración de los distintos componentes que arman el sistema: APIs, Microservicios, Bases de Datos, Colas de Mensajes o Sistemas de Archivos.
    
- **¿Dónde aplicarlas?** Un punto clave para escribir estas pruebas es donde la aplicación necesita _serializar_ o _deserializar_ datos, por ejemplo:
    
    - Llamadas a una API REST (verificar que el _endpoint_ devuelva el JSON correcto).
        
    - Lectura y escritura correcta en una Base de Datos.
        
    - Consumo de eventos en colas (como Kafka o RabbitMQ).
        
- En esta capa también se suelen incluir pruebas enfocadas en el negocio, como las Pruebas de Historias de Usuario (_User Story Tests_) o Pruebas de Aceptación.

### 3. Capa Superior: Pruebas de UI (Interfaz de Usuario) y E2E

Esta capa busca verificar el producto desde la perspectiva más cercana al cliente final, comprobando si el sistema en su totalidad está listo para producción.

**Es vital diferenciar UI de E2E (_End-to-End_):**

- **Pruebas de UI:** Verifican que los elementos visuales funcionen. Se pueden hacer aisladas del backend (usando librerías como _Jest_ o _Testing Library_ en React). Recuerda que una UI no es solo una web; una línea de comandos (CLI) también es una UI.
    
- **Pruebas E2E:** Prueban el flujo completo (ej. desde que el usuario hace clic en "Comprar" en el frontend, hasta que el registro se guarda en la base de datos). Las E2E no siempre necesitan una UI visual, pueden ser llamadas de API a API si el consumidor es otro microservicio.
    

**¿Por qué debe haber muy pocas pruebas en esta capa? (El problema del ROI)**

- **Fragilidad:** El frontend cambia constantemente (un botón cambia de ID o de color), lo que rompe las pruebas fácilmente. Requieren demasiado mantenimiento para no volverse obsoletas.
    
- **Costo Elevado:** Para ejecutar una prueba E2E automatizada necesitas levantar toda la infraestructura (Base de datos, Backend, Frontend, Redes).
    



### La Capa Extra: Pruebas de Exploración (Manuales)

A esta pirámide le falta un elemento fundamental: **el factor humano**. Por más automatizado que esté el proyecto, nunca se puede ni se debe automatizar el 100% de las pruebas del desarrollo.

- **¿Qué son?** Son pruebas manuales donde el _tester_ usa su creatividad, intuición y experiencia para ejecutar escenarios no planificados.
    
- **Objetivo:** No buscan simplemente "romper" la aplicación con un enfoque destructivo, sino conocerla en profundidad, encontrar errores de diseño de experiencia (_UX_), tiempos de respuesta extraños o fallos que una máquina no detectaría.
    
- **El ciclo ideal:** Cualquier escenario relevante, error o _bug_ nuevo que se descubra de forma manual durante esta exploración, **debe ser documentado y posteriormente automatizado** en alguna de las capas inferiores de la pirámide para que no vuelva a ocurrir.


![[Pirámide de Pruebas-1787531376258.webp]]

