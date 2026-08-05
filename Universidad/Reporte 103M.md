**Descripción del Problema** Se reportó una falla en el salón 103M debido a que algunos equipos no presentaban conexión a internet. Para identificar la causa del problema, se encendieron los computadores afectados y se evaluó el estado y funcionamiento de la conexión de red en cada uno.

**Validación Física** Inicialmente, se llevó a cabo una inspección física de los dispositivos, ejecutando las siguientes pruebas:

- Inspección del puerto de red del computador para confirmar su correcto estado físico.
    
- Comprobación de la integridad de los cables de red.
    
- Verificación de la conexión física entre el patch cord y el puerto de red (roseta).
    
- Pruebas de conectividad intercambiando conexiones con otros puertos operativos.
    

**Hallazgos** A partir de las pruebas realizadas, se descartaron fallas en el hardware local:

- Ningún cable de red presenta daños físicos o estructurales; todos transmiten datos correctamente.
    
- Las tarjetas de red de los computadores operan sin inconvenientes. Esto se confirmó al conectar los equipos afectados a puertos de red funcionales, logrando establecer conexión exitosamente.
    

**Teoría** Apunta a que la falla radica específicamente en los puertos físicos de red (o en su correspondiente configuración en el switch) asociados a los equipos sin servicio. Dado que los computadores afectados logran navegar, responder al comando _ping_ y ejecutar funciones de red básicas al ser conectados a puertos operativos.


**Lista de Puertos y PCs Asociados**


- _[REDES01]_ - _[E04-P1-B4]_
	
- _[REDES02]_ - _[E04-P1-C01]_
	
- _[REDES04]_ - _[E04-P1-B9]_
	
- _[REDES10]_ - _[E04-P1-B6]_ 
	
- _[REDES11]_ - _[E04-P1-C5]_
	
- _[REDES12]_ - _[E04-P1-C04]_
	
- _[REDES13]_ - _[E04-P1-A17]_




