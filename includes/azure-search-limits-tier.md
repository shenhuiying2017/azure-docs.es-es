Recurso|Gratuito|Básico (vista preliminar)|S1|S2
---|---|---|---|----
Servicios máximos en cada nivel por suscripción<sup>1</sup>|1 |12 |12 |1
Escala máxima por nivel<sup>2</sup>|N/D|3 unidades de búsqueda (hasta 3 réplicas y 1 partición)|36 unidades de búsqueda|36 unidades de búsqueda  

<sup>1</sup> Cada servicio se aprovisiona con un determinado plan de tarifa y con límites en el número de servicios que pueden aprovisionarse en cada nivel de una sola suscripción de Azure. Durante el periodo de vista previa, los niveles están disponibles con una tarifa inicial del 50 % del precio total.

<sup>2</sup> Los límites de escalabilidad horizontal se definen en términos de unidades de búsqueda (SU) por nivel. Las unidades de búsqueda son la unidad facturable de una **réplica** o una **partición**. Es necesario para las operaciones de almacenamiento, indexación y consulta. Visite [Planeación de la capacidad en Búsqueda de Azure](../articles/search/search-capacity-planning.md) para consultar las combinaciones válidas de réplicas y particiones que lo mantienen dentro del límite máximo de 3 o 36 unidades, para **Básico** y **Estándar**, respectivamente. Dado que **Gratis** se basa en los recursos compartidos que utilizan varios suscriptores, en este nivel no se proporciona escalabilidad horizontal.

<!---HONumber=AcomDC_0601_2016-->