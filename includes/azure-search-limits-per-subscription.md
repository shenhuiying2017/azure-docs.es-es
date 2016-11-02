Puede crear varios servicios en una suscripción, cada uno de ellos aprovisionado en un nivel concreto, el único límite lo establece el número de servicios que se permiten en cada nivel. Por ejemplo, puede crear hasta doce servicios en el nivel básico y otros doce en el nivel de S1 dentro de la misma suscripción. 

El límite máximo de servicios se puede elevar a petición. Si necesita tener más servicios en la misma suscripción, póngase en contacto con el soporte técnico de Azure.

Recurso|Gratuito|Básica|S1|S2|S3 <br/>(Versión preliminar) <sup>1</sup>  |S3 HD <br/>(Versión preliminar) <sup>1</sup> 
---|---|---|---|----|---|----
Servicios máximos |1 |12 |12  |6 |6 |6 
Escala máxima en SU <sup>2</sup>|N/D <sup>3</sup>|3 SU <sup>4</sup> |36 unidades de búsqueda|36 unidades de búsqueda|36 unidades de búsqueda|12 SU <sup>5</sup>

<sup>1</sup> Los niveles de vista previa se facturan a una tasa de introducción del 50 % y pasan a tener el precio completo cuando el nivel pasa a ser de disponibilidad general (GA). Durante la versión preliminar no hay ningún Acuerdo de Nivel de Servicio en vigor. Para más información acerca de los niveles, consulte [Selección de SKU o plan de tarifa de Azure Search](../articles/search/search-sku-tier.md).

<sup>2</sup> Las unidades de búsqueda (SU) son unidades facturables por servicio, asignadas como *réplica* o como *partición*. Ambos recursos se necesitan para las operaciones de almacenamiento, indexación y consulta. Para más información acerca de las combinaciones válidas que permanecen dentro de los límites máximos, consulte [Escalado de niveles de recursos para cargas de trabajo de indexación y consulta en Búsqueda de Azure](../articles/search/search-capacity-planning.md). 

<sup>3</sup> Gratis se basa en los recursos compartidos que usan varios suscriptores. En este nivel, no hay ningún recurso dedicado para un suscriptor individual. Por este motivo, la escala máxima se marca como no aplicable.

<sup>4</sup> Básico tiene una partición fija. En este nivel, se utilizan SU adicionales para asignar más réplicas a más cargas de trabajo de consulta.

<sup>5</sup> S3 HD se basa en el mismo hardware que S3, pero en una configuración que está optimizada para un gran número de índices más pequeños. De forma similar al nivel básico, tiene una partición muy grande con unidades de búsqueda adicionales que pueden utilizarse para las réplicas.






<!--HONumber=Oct16_HO2-->


