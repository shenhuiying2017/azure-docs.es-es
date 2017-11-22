---
title: "Selección de SKU o plan de tarifa de Azure Search | Microsoft Docs"
description: "El servicio Azure Search puede aprovisionarse en estas SKU: Gratis, Básico o Estándar; este último está disponible en varias configuraciones de recursos y niveles de capacidad."
services: search
documentationcenter: 
author: HeidiSteen
manager: jhubbard
editor: 
tags: azure-portal
ms.assetid: 8d4b7bca-02a5-43ee-b3f8-03551dfb32fd
ms.service: search
ms.devlang: NA
ms.workload: search
ms.topic: article
ms.tgt_pltfrm: na
ms.date: 10/24/2016
ms.author: heidist
ms.openlocfilehash: 781683f27c943e25d5629dd846da357f51c9d4f9
ms.sourcegitcommit: bc8d39fa83b3c4a66457fba007d215bccd8be985
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/10/2017
---
# <a name="choose-a-sku-or-pricing-tier-for-azure-search"></a>Selección de SKU o plan de tarifa de Azure Search
En Azure Search, un [servicio se aprovisiona](search-create-service-portal.md) en un plan de tarifa específico o SKU. Las opciones incluyen los niveles **Gratis**, **Básico** o **Estándar**; **este último** está disponible en varias configuraciones y capacidades.

La finalidad de este artículo es ayudarle a elegir un nivel. Si la capacidad de un nivel resulta demasiado baja, debe aprovisionar un nuevo servicio en el nivel superior y, después, volver a cargar los índices. No hay ninguna actualización en marcha del mismo servicio desde una SKU a otra.

> [!NOTE]
> Después de elegir un nivel y [aprovisionar un servicio de búsqueda](search-create-service-portal.md), puede aumentar los recuentos de réplica y partición dentro del servicio. Para obtener instrucciones, consulte [Escalado de niveles de recursos para cargas de trabajo de indexación y consulta](search-capacity-planning.md).
>
>

## <a name="how-to-approach-a-pricing-tier-decision"></a>Cómo enfocar una decisión de plan de tarifas
Azure Search, el nivel determina la capacidad, no la disponibilidad de características. Generalmente, todas las características están disponibles en cada nivel, incluidas las de vista previa. La única excepción es que no se admiten indexadores en S3 HD.

> [!TIP]
> Se recomienda que aprovisione siempre un servicio **gratis** (uno por cada suscripción sin expiración) para que esté fácilmente disponible para los proyectos ligeros. Use el servicio **gratis** para prueba y evaluación y, después, cree un segundo servicio facturable en el nivel **Básico** o **Estándar** para cargas de trabajo de prueba o producción mayores.
>
>

La capacidad y los costes de ejecutar el servicio están relacionados. La información de este artículo puede ayudarle a decidir qué SKU ofrece el equilibrio correcto, pero para que puedan ser útiles, como mínimo, tiene que realizar estimaciones aproximadas sobre lo siguiente:

* Número y tamaño de los índices que se van a crear
* Número y tamaño de los documentos que se van a cargar
* Una idea del volumen de consultas, en términos de consultas por segundo (QPS). Para tener una guía, consulte [Consideraciones sobre el rendimiento y la optimización de Azure Search](search-performance-optimization.md).

El número y el tamaño son importantes porque se alcanzan los límites máximos a través de un límite en el número de índices por servicio, o en recursos (almacenamiento o réplicas) utilizados por el servicio. El límite real del servicio es el que se ha usado primero: recursos u objetos.

Cuando tenga las estimaciones, los pasos siguientes deberían simplificar el proceso:

* **Paso 1** Revisar las descripciones de SKU siguientes para obtener más información sobre las opciones disponibles.
* **Paso 2** Responder las preguntas siguientes para llegar a una decisión preliminar.
* **Paso 3** Finalizar la decisión revisando los límites estrictos de almacenamiento y precios.

## <a name="sku-descriptions"></a>Descripciones de las SKU
En la tabla siguiente se proporcionan descripciones de cada nivel.

| Nivel: | Escenarios principales |
| --- | --- |
| **Gratis** |Un servicio compartido gratuito utilizado para cargas de trabajo de evaluación o investigación reducidas. Dado que se comparte con otros suscriptores, la indexación y el rendimiento de las consultas varía en función de quién más esté utilizando el servicio. La capacidad es pequeña (50 MB o tres índices con hasta 10.000 documentos cada uno). |
| **Básica** |Cargas de trabajo de producción reducidas en hardware específico. Se trata de un nivel de alta disponibilidad. La capacidad es hasta tres réplicas y una partición (2 GB). |
| **S1** |Estándar 1 admite combinaciones flexibles de particiones (12) y réplicas (12). Se utiliza para cargas de trabajo de producción normales en hardware específico. Puede asignar particiones y réplicas en combinaciones que admitan un máximo de 36 unidades de búsqueda facturables. En este nivel, las particiones son 25 GB cada una. |
| **S2** |Estándar 2 ejecuta cargas de trabajo de producción mayores con las mismas 36 unidades de búsqueda que S1, pero con réplicas y particiones de más tamaño. En este nivel, las particiones son 100 GB cada una. |
| **S3** |Estándar 3 ejecuta cargas de trabajo de producción proporcionalmente mayores en los sistemas finales superiores con configuraciones de hasta 12 particiones o 12 réplicas en 36 unidades de búsqueda. En este nivel, las particiones son 200 GB cada una. |
| **S3 HD** |Estándar 3 de alta densidad está diseñado para un gran número de índices más pequeños. Puede tener hasta 3 particiones, a 200 GB cada una.|

> [!NOTE]
> Los valores máximos de réplicas y particiones se facturan como unidades de búsqueda (un máximo de 36 unidades por servicio), que impone un límite real inferior al valor nominal del máximo. Por ejemplo, para usar el máximo de 12 réplicas, podría tener, como máximo, 3 particiones (12 * 3 = 36 unidades). De forma similar, para utilizar la cantidad máxima de particiones, reduzca el número de réplicas a 3. Consulte [Planeación de la capacidad en Azure Search](search-capacity-planning.md) para ver un gráfico de las combinaciones que pueden realizarse.
>
>

## <a name="review-limits-per-tier"></a>Revisión de los límites por nivel
El gráfico siguiente es un subconjunto de los límites que figuran en [Límites de servicio en Azure Search](search-limits-quotas-capacity.md). Muestra los factores que con más probabilidad pueden afectar a una decisión de SKU. Puede hacer referencia a este gráfico al revisar las preguntas siguientes.

| Recurso | Gratuito | Básica | S1 | S2 | S3 | S3 HD |
| --- | --- | --- | --- | --- | --- | --- |
| Contrato de nivel de servicio (SLA) |No <sup>1</sup> |Sí |Sí |Sí |Sí |yes |
| Límites de índice |3 |5 |50 |200 |200 |1000 <sup>2</sup> |
| Límites de documento |10 000 en total |1 millón por servicio |15 millones por partición |60 millones por partición |120 millones por partición |1 millón por índice |
| Número máximo de particiones |N/D |1 |12 |12 |12 |3 <sup>2</sup> |
| Tamaño de la partición |50 MB en total |2 GB por servicio |25 GB por partición |100 GB por partición (hasta 1,2 TB por servicio) |200 GB por partición (hasta 2,4 TB por servicio) |200 GB (hasta un máximo de 600 GB por servicio) |
| Número máximo de réplicas |N/D |3 |12 |12 |12 |12 |

<sup>1</sup> Las versiones gratuitas y preliminares no incluyen contratos de nivel de servicio (SLA). Para todos los niveles facturables, los SLA tomarán efecto cuando se aprovisione suficiente redundancia para el servicio. Son necesarias dos o más réplicas para el SLA de consulta (lectura). Son necesarias tres o más réplicas para el SLA de consulta e indexación (lectura y escritura). El número de particiones no es una consideración del SLA. 

<sup>2</sup> S3 y S3 HD están respaldados por la misma infraestructura de alta capacidad, pero cada uno de ellos alcanza su límite máximo de maneras diferentes. S3 se dirige a un menor número de índices muy grandes. Como tal, su límite máximo es el límite de recursos (2,4 TB para cada servicio). S3 HD se dirige a un gran número de índices muy pequeños. A los 1000 índices, S3 HD alcanza sus límites en forma de restricciones de índice. Si es un cliente de S3 HD que requiere más de 1000 índices, póngase en contacto con el soporte técnico de Microsoft para obtener información sobre cómo proceder.

## <a name="eliminate-skus-that-dont-meet-requirements"></a>Eliminación de las SKU que no cumplen los requisitos
Las siguientes preguntas pueden ayudarlo a decidir qué SKU elegir para su carga de trabajo.

1. ¿Tiene requisitos de **SLA** ? Puede usar cualquier nivel facturable (Básico y superiores), pero debe configurar el servicio en pos de la redundancia. Son necesarias dos o más réplicas para el SLA de consulta (lectura). Son necesarias tres o más réplicas para el SLA de consulta e indexación (lectura y escritura). El número de particiones no es una consideración del SLA.
2. ¿**Cuántos índices** necesita? Una de las variables más importantes que afecta a la decisión de qué SKU elegir es el número de índices que admite cada una, ya que los planes de tarifas más económicos tienen límites muy diferentes. Los requisitos de número de índices podrían ser un factor determinante para decidir qué SKU elegir.
3. **¿Cuántos documentos** se cargarán en cada índice? El número y tamaño de los documentos determinarán el tamaño final del índice. Si damos por hecho que puede estimar el tamaño previsto del índice, puede comparar ese número con el tamaño de la partición por SKU y sumarle la cantidad de particiones necesarias para almacenar un índice de ese tamaño.
4. **¿Qué carga de consultas se espera**? Una vez que entiende los requisitos de almacenamiento, tenga en cuenta las cargas de trabajo de consultas. Las SKU de los niveles S2 y S3 ofrecen prácticamente el mismo rendimiento, pero los requisitos de SLA excluirán todas las SKU de vista previa.
5. Si está pensando en el nivel S2 o S3, determine si necesita [indexadores](search-indexer-overview.md). Los indexadores todavía no están disponibles para el nivel de S3 HD. Un enfoque alternativo es usar un modelo de inserción para las actualizaciones de índice, donde se escribe código de aplicación para insertar un conjunto de datos en un índice.

La mayoría de los clientes pueden incluir o excluir SKU específicas en función de lo que respondan a las preguntas anteriores. Si todavía no está seguro de qué SKU usar, puede publicar preguntas en los foros de MSDN o StackOverflow o ponerse en contacto con el soporte técnico de Azure para obtener más información.

## <a name="decision-validation-does-the-sku-offer-sufficient-storage-and-qps"></a>Validación de la decisión: ¿ofrece la SKU suficiente espacio de almacenamiento y QPS?
El último paso consiste en volver a visitar la [página de precios](https://azure.microsoft.com/pricing/details/search/) y el artículo sobre [límites de servicio en Azure Search](search-limits-quotas-capacity.md) para comprobar de nuevo las estimaciones con respecto a los límites de servicio y suscripción.

Si los requisitos de almacenamiento o precio se encuentran fuera de los límites, se recomienda, por ejemplo, refactorizar las cargas de trabajo entre varios servicios de menor envergadura. Si desea adoptar una solución más avanzada, podría volver a diseñar los índices para sean más pequeños, o bien usar filtros para realizar consultas de manera más eficaz.

> [!NOTE]
> Los requisitos de almacenamiento pueden ser demasiado excesivos si los documentos contienen datos extraños. Lo ideal es que los documentos contengan solamente datos utilizables en búsquedas o metadatos. Los datos binarios no son utilizables en búsquedas y deben almacenarse por separado (por ejemplo, en un almacenamiento de tablas o blobs de Azure) con un campo en el índice para mantener una referencia de URL a los datos externos. El tamaño máximo de un documento individual es 16 MB (o menos si carga en masa varios documentos en una sola solicitud). Consulte [Límites de servicio en Azure Search](search-limits-quotas-capacity.md) para obtener más información.
>
>

## <a name="next-step"></a>Paso siguiente
Una vez que sepa qué SKU se ajusta a sus requisitos, consulte cuáles son los siguientes pasos en estos artículos:

* [Creación de un servicio Búsqueda de Azure mediante el Portal de Azure](search-create-service-portal.md)
* [Planeación de la capacidad en Búsqueda de Azure](search-capacity-planning.md)
