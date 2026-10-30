
Ya se tiene un punto de inicio que fue como un piloto de trazabilidad del café, que va desde la finca hasta el comprador, teniendo las siguientes features:
- Telemetría de Cultivo
- Registro de Beneficiario
- Transporte Terrestre
- Centro de Acopio
- Entrega en el puerto de Buenaventura

El alcance geográfico era Cauca, Huila y Tolima (Esto no se desplegó)

Se retoma actualmente con cotizaciones actuales, dispositivos de Hardware Actuales etc etc

Hay que dejar un piloto en el mismo territorio con las siguientes capacidades: 

- Monitoreo del Cultivo y del Secado (Con alerta al Caficultor)
	- Posibles Tecnologias (Prometheus, Grafana, Loki, Hibana etc etc)
- Registro de la Cosecha y del Empaque en sacos de 60Kg
	- Posibles Tecnologias (S3, RDS, Aurora o sus equivalentes en otras nubes)
- Seguimiento del viaje hasta el centro de acopio y hasta Buenaventura
	- Posibles Tecnologias (MQTT, Cloud9, servicios de rastreo con dispositivos IoT)
- Consulta de un saco, por código, para el caficultor, el centro de acopio y el comprador
	- Posibes tecnologias (Pues a mi me parece que es organizar los datos dichos en el apartado anterior y es hacer un GET )
- Servicio de drones para delimitar el lote, confirmar la alerta, fumigar, verificar y estimar la cosecha. El diseño elige agricultura de precisión, agricultura de áreas, o una combinación, y lo sostiene.
	- Posibles tecnoologias I dont know

### Aplicaciones a Construir 

- Caficultor
- Transportador
- Consulta Web Reportes y Lectura del Código del saco

(Aqui se pueden evidenciar algunos accesos por roles que terminan siendo necesarios en algun ppunto del desarrollo)

### COSAS POR ACTUALIZAR

- No asumir cobertura de Datos en las fincas
- El centro de Acopio y el puerto quedaban sin LAN ni WAN

ESTA PARTE TE LA ENCARGO A TI QUE ESTA UN POCO MAS DENSA ESTA INFORMACION DE DIGERIR 

---


![[Primer Análisis-1793330180860.webp]]

---

![[Primer Análisis-1793330383172.webp]]

---

![[Primer Análisis-1793330434901.webp]]

---

### Cifras del Piloto

- 200 Fincas: Cauca, Huila y Tolima
- Tres centro de Acopio, uno por Departamento
- Cada fincna tiene dos zonas, una de cultivo y zona de secado
	- Cultivo: nitrógeno, fósforo, potasio, humedad y temperatura de aire y de suelo, cada 4 horas, las 24 horas
	- Secado: temperatura y humedad del café, cada hora, mientras el lote está en secado
 Y lo que sigue etc etc
