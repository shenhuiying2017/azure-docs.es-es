Puede crear varios servicios en una suscripción, cada uno de ellos aprovisionado en un nivel concreto y limitado solo por el número de servicios permitidos en cada nivel en una sola suscripción de Azure. Los servicios máximos por nivel se especifican a continuación. Como se indica, puede crear hasta 12 servicios en el nivel básico y otros 12 servicios en el nivel de S1 dentro de la misma suscripción.

En el resto de niveles, corresponde uno por suscripción. Puede ponerse en contacto con soporte técnico de Azure si necesita más de un S2, S3 o S3 HD por suscripción.

Recurso|Gratuito|Básica|S1|S2|S3 <sup>1</sup> <br/>(Versión preliminar) |S3 HD <sup>1</sup> <br/>(Versión preliminar) 
---|---|---|---|----|---|----
Servicios máximos |1 |12 |12 |1 |1 |1 
Escala máxima en unidad de búsqueda <sup>2</sup>|N/D <sup>3</sup>|3 unidades de búsqueda <sup>4</sup> |36 unidades de búsqueda|36 unidades de búsqueda|36 unidades de búsqueda|12 unidades de búsqueda <sup>5</sup>

<sup>1</sup> Los niveles en **versión preliminar** se facturan a una tarifa inicial del 50 % del precio completo. Antes de la disponibilidad general, los niveles se presentan como una característica en versión preliminar. Durante la versión preliminar no hay ningún Acuerdo de Nivel de Servicio en vigor. Consulte [Choose a SKU or tier for Azure Search](../articles/search/search-sku-tier.md) (Elegir SKU o nivel de Búsqueda de Azure) para más información acerca de los niveles.

<sup>2</sup> Las **unidades de búsqueda** son unidades facturables por servicio, asignadas como **réplica** o como **partición**. Necesita ambos tipos de recursos para las operaciones de para las operaciones de almacenamiento, indexación y consulta. Consulte [Planeación de la capacidad en Búsqueda de Azure](../articles/search/search-capacity-planning.md) para ver las combinaciones válidas que permanecen dentro de los límites máximos.

<sup>3</sup> **Gratis** se basa en los recursos compartidos utilizados por varios suscriptores. En este nivel, no hay ningún recurso dedicado para un suscriptor individual. Por este motivo, no se admite la escalabilidad.

<sup>4</sup> **Básico** tiene una partición fija. Las unidades de búsqueda se utilizan para asignar réplicas para el escalado de cargas de trabajo de consulta.

<sup>5</sup> **S3 HD** se basa en el mismo hardware como S3, pero en una configuración que está optimizada para un número mayor de índices más pequeños. Tiene 1 partición muy grande en lugar de 12 particiones más pequeñas y tiene un máximo de 12 réplicas, similares a S3.

<!---HONumber=AcomDC_0608_2016-->