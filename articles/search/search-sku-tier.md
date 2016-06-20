<properties
	pageTitle="Selección de SKU o niveles de Búsqueda de Azure | Microsoft Azure"
	description="El servicio Búsqueda de Azure puede aprovisionarse en estas SKU: Gratis, Básico o Estándar; este último está disponible en varias configuraciones de recursos y niveles de capacidad."
	services="search"
	documentationCenter=""
	authors="HeidiSteen"
	manager="paulettm"
	editor=""
    tags="azure-portal"/>

<tags
	ms.service="search"
	ms.devlang="NA"
	ms.workload="search"
	ms.topic="article"
	ms.tgt_pltfrm="na"
	ms.date="06/05/2016"
	ms.author="heidist"/>

# Selección de SKU o niveles de Búsqueda de Azure

Antes de poder [crear un servicio de búsqueda](search-create-service-portal.md) en Búsqueda de Azure, necesitará saber en qué nivel o SKU aprovisionar el servicio. Las SKU incluyen los niveles Gratis, Básico o Estándar; este último está disponible en varias configuraciones de recursos y capacidades.

La capacidad y los costes de ejecutar el servicio están relacionados. La información de este artículo puede ayudarlo a decidir qué SKU ofrece el equilibrio correcto, pero para que puedan ser útiles, como mínimo, tendrá que realizar estimaciones aproximadas del número y tamaño de los índices que planea crear, así como tener una idea del volumen de consultas. Cuando tenga las estimaciones, los pasos siguientes deberían simplificar el proceso:

- **Paso 1**: revisar las siguientes descripciones de las SKU para saber cuáles son sus opciones
- **Paso 2**: responder a una serie de preguntas para reducir las opciones
- **Paso 3**: validar la decisión revisando los límites estrictos de almacenamiento y precios 

## Descripciones de las SKU

En la tabla siguiente se muestran una descripción y una comparación sencilla de cada SKU en lo que respecta a particiones y réplicas, así como a las consultas por segundo (QPS), según corresponda. Para ver una introducción a las réplicas y particiones, consulte [Límites de servicio en la Búsqueda de Azure](search-limits-quotas-capacity.md).

Nivel:|Capacity|Diseñado para
----|-----------|-----------
Gratuito|50 MB o 3 índices y 10 000 documentos|Un servicio compartido gratuito utilizado para cargas de trabajo de evaluación o investigación reducidas. Dado que se comparte con otros suscriptores, la indexación y el rendimiento de las consultas variará en función de quién más esté utilizando el servicio.
Básica|3 réplicas y 1 partición|Cargas de trabajo de producción reducidas en hardware específico. Se trata de un nivel de alta disponibilidad.
Estándar 1 (S1)|Las particiones tienen un tamaño de 25 GB cada una. <br/><br/>Se pueden realizar aproximadamente 15 consultas por segundo en las réplicas.|Admite combinaciones flexibles de particiones (12) y réplicas (12). Se utiliza para cargas de trabajo de producción normales en hardware específico. Puede asignar particiones y réplicas en combinaciones que admitan un máximo de 36 unidades de búsqueda facturables.
Estándar 2 (S2)|Las particiones tienen un tamaño de 100 GB cada una. <br/><br/>Se pueden realizar aproximadamente 60 consultas por segundo en las réplicas.|Ejecuta cargas de trabajo de producción mayores con las mismas configuraciones que S1, pero con réplicas y particiones de más tamaño.
Estándar 3 (S3) (versión preliminar)|Las particiones tienen un tamaño de 200 GB cada una. <br/><br/>Se pueden realizar más de 60 consultas por segundo en las réplicas.|Ejecuta cargas de trabajo de producción proporcionalmente mayores en los sistemas finales superiores con configuraciones de hasta 12 particiones o 12 réplicas. 
Estándar 3 de alta densidad (S3 HD) (versión preliminar)|Solo tiene una partición de 200 GB. <br/><br/>Se pueden realizar más de 60 consultas por segundo en las réplicas.|Está destinado a los clientes que tienen un gran número de índices más pequeños.

## Guía de decisión para elegir una SKU

El gráfico siguiente es un subconjunto de los límites que figuran en [Límites de servicio en la Búsqueda de Azure](search-limits-quotas-capacity.md). Muestra los factores que tienen más probabilidades de afectar a una decisión de SKU (incluidos los límites de capacidad de SKU de la tabla anterior) con límites adicionales relacionados con los tipos de contenido (es decir, índices y documentos). Puede consultar este gráfico:

Recurso|Gratuito|Básica|S1|S2|S3 <br/>(versión preliminar) |S3 HD <br/>(versión preliminar) 
---|---|---|---|----|---|----
Contrato de nivel de servicio (SLA)|No <sup>1</sup> |Sí |Sí |Sí |No <sup>1</sup> |No <sup>1</sup> 
Número máximo de particiones|N/D |1 |12 |12 |12|1
Índices permitidos por SKU|3|5|50|200|200|1000
Límites de documentos|10 000 en total|1 millón por servicio|15 millones por partición |60 millones por partición|120 millones por partición |1 millón por índice
Tamaño de la partición|50 MB en total|2 GB por servicio|25 GB por partición |100 GB por partición (hasta 1,2 TB por servicio)|200 GB por partición (hasta 2,4 TB por servicio)|200 GB (para la partición 1)
Número máximo de réplicas|N/D |3 |12 |12 |12|12
Consultas por segundo|N/D|~ 3 por réplica|~ 15 por réplica|~ 60 por réplica|> 60 por réplica|> 60 por réplica

<sup>1</sup> Las SKU de Gratis y Vista previa no se incluyen en los SLA. Los SLA se aplican cuando una SKU se encuentra disponible de manera generalizada.

> [AZURE.NOTE] Los valores máximos de réplicas y particiones están sujetos a una configuración de facturación máxima combinada de 36 unidades, que impone un límite real inferior al valor nominal del máximo. Por ejemplo, para usar el máximo de 12 réplicas, podría tener, como máximo, 3 particiones (12 * 3 = 36 unidades). De forma similar, para utilizar la cantidad máxima de particiones, reduzca el número de réplicas a 3. Consulte [Planeación de la capacidad en Búsqueda de Azure](search-capacity-planning.md) para ver un gráfico de las combinaciones que pueden realizarse.

### Preguntas más comunes al elegir una SKU

Las siguientes preguntas pueden ayudarlo a decidir qué SKU elegir para su carga de trabajo.

1. ¿Tiene requisitos de **Acuerdo de Nivel de Servicio (SLA)**? Reduzca las opciones a los niveles Básico o Estándar (que no sea Vista previa).
2. **¿Cuántos índices** necesita? Una de las variables más importantes que afecta a la decisión de qué SKU elegir es el número de índices que admite cada una, ya que los planes de tarifas más económicos tienen límites muy diferentes. Los requisitos de número de índices podrían ser un factor determinante para decidir qué SKU elegir.
3. **¿Cuántos documentos** se cargarán en cada índice? El número y tamaño de los documentos determinarán el tamaño final del índice. Si damos por hecho que puede estimar el tamaño previsto del índice, puede comparar ese número con el tamaño de la partición por SKU y sumarle la cantidad de particiones necesarias para almacenar un índice de ese tamaño. 
4. **¿Qué carga de consultas se espera**? Una vez que entiende los requisitos de almacenamiento, tenga en cuenta las cargas de trabajo de consultas. Las SKU de los niveles S2 y S3 ofrecen prácticamente el mismo rendimiento, pero los requisitos de SLA excluirán todas las SKU de vista previa. 

La mayoría de los clientes pueden incluir o excluir SKU específicas en función de lo que respondan a estas cuatro preguntas. Si todavía no está seguro de qué SKU elegir, póngase en contacto con el servicio de soporte técnico de Azure para obtener más información.

## Validación de la decisión: ¿ofrece la SKU suficiente espacio de almacenamiento y QPS?

El último paso consiste en volver a visitar la [página de precios](https://azure.microsoft.com/pricing/details/search/) y el artículo [Límites de servicio en la Búsqueda de Azure](search-limits-quotas-capacity.md) para comprobar de nuevo las estimaciones con respecto a los límites de servicio y suscripción.

Si los requisitos de almacenamiento o precio se encuentran fuera de los límites, se recomienda, por ejemplo, refactorizar las cargas de trabajo entre varios servicios de menor envergadura. Si desea adoptar una solución más avanzada, podría volver a diseñar los índices para sean más pequeños, o bien usar filtros para realizar consultas de manera más eficaz.

> [AZURE.NOTE] Los requisitos de almacenamiento pueden ser demasiado excesivos si los documentos contienen datos extraños. Lo ideal es que los documentos tengan solamente datos utilizables en búsquedas o metadatos. Los datos binarios que pueden sobredimensionar un documento deben almacenarse por separado (por ejemplo, en un almacenamiento de tablas o blobs de Azure) con un campo en el índice para mantener una referencia de URL a los datos externos. El tamaño máximo de un documento individual es 16 MB (o menos si carga en masa varios documentos en una sola solicitud). Consulte [Límites de servicio en la Búsqueda de Azure](search-limits-quotas-capacity.md) para obtener más información.

## Paso siguiente

Una vez que sepa qué SKU se ajusta a sus requisitos, consulte cuáles son los siguientes pasos en estos artículos:
- [Creación de un servicio Búsqueda de Azure mediante el Portal de Azure](search-create-service-portal.md)
- [Planeación de la capacidad en Búsqueda de Azure](search-capacity-planning.md)

<!---HONumber=AcomDC_0608_2016-->