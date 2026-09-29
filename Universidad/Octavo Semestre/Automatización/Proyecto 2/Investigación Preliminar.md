
### Entendiendo el Contexto:

Principalmente se dice que tiene un único datacenter en Cali y ocho centros de distribución regionales y actualmente corre sobre una virtualización local.

¿Cambiarla a Nube es optimo?

En mi opinión se puede hacer una especie de infraestructura híbrida entre algunas cosas en local y otras en nube, esto permite de que los costos no sean tan elevados y dependientes de la nube a convenir, por otro lado puede que esta infraestructura local permita funcionar como parte de almacenamiento, para no se realicen cobros de almacenamiento, pero esto habría que evaluarlo más adelante.

----

Las cargas del Datacenter que se tienen actualmente son:

- Torre de Control de Despachos (**RESTRICCIÓN IMPORTANTE:** Solo es usada para personal interno)
- Inventario de Bodegas Refrigeradas
- Facturación a Clientes Corporativos
- Portal Publico de rastreo de guías 
- Repositorio de Evidencias de Entrega: Fotos, Comprobantes firmados y documentos 
- Históricos de temperatura que hoy se cargan por lote, una vez por hora, desde archivos que envían los centros regionales 

![[Investigación Preliminar-1790697211989.webp]]

Realizando el primer análisis de cómo se comunica y funciona todo actualmente, me percaté de que el eje central se ve en el storage, en este se almacenan muchas cosas por la que la disponibilidad de este puede llegar a ser clave.

----

### Problemas

- La capacidad de cómputo y almacenamiento es fijo, en eventos especiales o cosas por ese estilo el despacho se ralentiza y el portal de rastreo se degrada 
- Un cambio toma semanas porque el despliegue es manual y los ambientes comparten el mismo cluster 
- No hay alertas mientras el camión esta en ruta por cadena de frío 
- El archivo suma aproximadamente cerca de 1,2 PB y sigue creciendo 12 TB al mes. El servicio actual no tiene holgura ni un respaldo separado
- CentOS 7 y MySQL 5.7 ya están fuera de soporte. Solo está Ubuntu con soporte extendido de pago 
- No existe un esquema único de identidades, presupuestos ni auditoría para pensar en mas de un proveedor de nube 

---

### Visión

FríoAndes quiere dejar de ampliar el datacenter y pasar a una plataforma en la nube operada con gobierno, seguridad, conectividad híbrida y entrega automatizada. El plan combina tres movimientos:

1. Construir una plataforma nueva de operación logística, que reemplaza al núcleo actual.
2. Incorporar telemetría de temperatura y ubicación en tiempo cercano al real.
3. Migrar datos y módulos hacia esa plataforma sin detener el despacho diurno.
4. Sumar, encima de ese diseño, una estrategia de Microsoft Fabric para que la torre de control use inteligencia artificial sobre la operación y la telemetría.

La estrategia debe alinearse con los pilares de un marco Well-Architected: excelencia operacional, seguridad, confiabilidad, eficiencia de desempeño y optimización de costos.


