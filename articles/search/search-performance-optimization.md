---
title: "Consideraciones sobre el rendimiento y la optimización de Azure Search | Microsoft Docs"
description: "Optimización del rendimiento de Búsqueda de Azure y configuración de la escala óptima"
services: search
documentationcenter: 
author: LiamCavanagh
manager: pablocas
editor: 
ms.assetid: 4d3cd864-29d2-4921-be0d-a3f1a819de46
ms.service: search
ms.devlang: rest-api
ms.workload: search
ms.topic: article
ms.tgt_pltfrm: na
ms.date: 05/01/2017
ms.author: liamca
ms.openlocfilehash: f4e371fc16bc57e6963f1ec51c0ea864fa568f0c
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="azure-search-performance-and-optimization-considerations"></a>Consideraciones sobre el rendimiento y la optimización de Búsqueda de Azure
Una buena experiencia de búsqueda es clave para el éxito de muchas aplicaciones web y móviles. Desde el mercado inmobiliario al de vehículos usados o catálogos en línea, una búsqueda rápida y unos resultados pertinentes influirán en la experiencia del cliente. Este documento ofrece información sobre los procedimientos recomendados para sacar el máximo provecho de Búsqueda de Azure, especialmente en escenarios avanzados con requisitos sofisticados de escalabilidad, compatibilidad multilingüe o clasificación personalizada.  Además, este documento describe los aspectos internos y los enfoques que funcionan de forma eficaz en las aplicaciones de clientes reales.

## <a name="performance-and-scale-tuning-for-search-services"></a>Rendimiento y ajuste de la escala de los servicios de Búsqueda
Todos estamos acostumbrados a los motores de búsqueda como Bing y Google, y al alto rendimiento que ofrecen.  Como resultado, cuando los clientes usan su aplicación móvil o web habilitada para búsqueda, esperan características de rendimiento similares.  Para optimizar el rendimiento de la búsqueda, uno de los mejores enfoques es centrarse en la latencia, que es el tiempo que una consulta tarda en completarse y devolver resultados.  Para optimizar la latencia de la búsqueda es importante:

1. Seleccionar una latencia objetivo (o cantidad máxima de tiempo) que una solicitud de búsqueda típica debería tardar en completarse.
2. Crear y probar una carga de trabajo real en su servicio de búsqueda con un conjunto de datos realista para medir estas tasas de latencia.
3. Comenzar con un número reducido de consultas por segundo y continuar aumentar el número que se ejecuta en la prueba hasta que la latencia caiga por debajo de la latencia objetivo definida.  Esta es una prueba comparativa importante que le ayudará a planear el escalado a medida que el uso de la aplicación crece.
4. Siempre que sea posible, reutilice las conexiones HTTP.  Si usa el SDK de .NET para Búsqueda de Azure, deberá reutilizar una instancia o una instancia de [SearchIndexClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.searchindexclient) , y si usa la API de REST, deberá reutilizar un única instancia de HttpClient.

Al crear estas cargas de trabajo de prueba, hay algunas características de Búsqueda de Azure que debe tener en cuenta:

1. Se pueden insertar tantas consultas de búsqueda al mismo tiempo que los recursos disponibles en el servicio Búsqueda de Azure podrían verse desbordados.  Cuando esto sucede, verá códigos de respuesta HTTP 503.  Por este motivo, es mejor empezar con varios intervalos de solicitudes de búsqueda para ver las diferencias en las tasas de latencia cuando se agreguen más solicitudes de búsqueda.
2. Cargar contenido a Búsqueda de Azure afectará el rendimiento general y a la latencia del servicio Búsqueda de Azure.  Si espera enviar datos mientras los usuarios están realizando búsquedas, es importante tener en cuenta esta carga de trabajo en las pruebas.
3. No todas las consultas de búsqueda tendrán los mismos niveles de rendimiento.  Por ejemplo, una sugerencia de búsqueda o la búsqueda de documentos normalmente se realiza más rápidamente que una consulta, con un número significativo de facetas y filtros.  Es mejor tener en cuenta las distintas consultas que espera ver a la hora de crear las pruebas.  
4. La variación de las solicitudes de búsqueda es importante porque si ejecuta continuamente las mismas solicitudes de búsqueda, el almacenamiento en caché de los datos empezará a ofrecer un rendimiento mejor que el que se obtendría con un conjunto de consultas más dispar.

> [!NOTE]
> Las [pruebas de carga de Visual Studio](https://www.visualstudio.com/docs/test/performance-testing/run-performance-tests-app-before-release) son una buena manera de realizar las pruebas comparativas porque permite ejecutar solicitudes HTTP tal y como necesite para ejecutar consultas en Azure Search, y habilita la paralelización de solicitudes.
> 
> 

## <a name="scaling-azure-search-for-high-query-rates-and-throttled-requests"></a>Escalado de Búsqueda de Azure para grandes volúmenes de consultas y solicitudes limitadas
Si recibe demasiadas solicitudes limitadas o se superan las tasas de latencia objetivo debido a una carga de consultas mayor, puede intentar reducir las tasas de latencia de dos maneras:

1. **Aumentar las réplicas:** Una réplica es como una copia de los datos que permite a Azure Search equilibrar la carga de solicitudes entre las diversas copias.  Búsqueda de Azure administra el equilibrado de la carga y la replicación de los datos entre las réplicas, y puede modificar el número de réplicas asignado a su servicio en cualquier momento.  Puede asignar hasta 12 réplicas en un servicio de búsqueda estándar y 3 réplicas en un servicio de búsqueda básico. Las réplicas pueden ajustarse desde [Azure Portal](search-create-service-portal.md) o [PowerShell](search-manage-powershell.md).
2. **Aumentar el nivel de búsqueda:** Azure Search se ofrece con [diferentes planes](https://azure.microsoft.com/pricing/details/search/), cada uno de los cuales ofrece diferentes niveles de rendimiento.  En algunos casos, el número de consultas es tan alto que su nivel actual no puede ofrecer tasas de latencia suficientemente bajas aunque se use el máximo de réplicas.  En este caso, puede considerar la posibilidad de usar uno de los niveles de búsqueda más altos, como el nivel S3 de Búsqueda de Azure, ideal para escenarios con un gran número de documentos y cargas de trabajo de consulta muy altas.

## <a name="scaling-azure-search-for-slow-individual-queries"></a>Escalado de Búsqueda de Azure para consultas individuales lentas
Otro motivo por el que las tasas de latencia pueden ser lentas es una única consulta que tarda demasiado tiempo en completarse.  En este caso, agregar réplicas no mejorará las tasas de latencia.  En cambio, hay dos opciones disponibles:

1. **Aumentar las particiones**: Una partición es un mecanismo para dividir los datos entre recursos adicionales.  Por este motivo, cuando se agrega una segunda partición, los datos se dividen en dos.  Una tercera partición divide el índice en tres, etc.  En algunos casos, el efecto que produce es que las consultas lentas se realizarán con más rapidez debido a la paralelización de los procesos.  Hemos observado algunos ejemplos en los que esta paralelización funciona muy bien con consultas que tienen consultas de baja selectividad.  Se componen de consultas que coinciden con muchos documentos o cuando el uso de facetas necesita proporcionar recuentos en una gran cantidad de documentos.  Como se necesita una gran cantidad de proceso para puntuar la pertinencia de los documentos o para contar el número de documentos, agregar particiones adicionales puede ayudar a proporcionar capacidad de proceso adicional.  
   
   Puede haber un máximo de 12 particiones en el servicio de búsqueda estándar y 1 partición en el servicio de búsqueda básico.  Las particiones pueden ajustarse desde [Azure Portal](search-create-service-portal.md) o [PowerShell](search-manage-powershell.md).
2. **Límitar los campos de alta cardinalidad:** un campo de alta cardinalidad consta de un campo filtrable o de navegación por facetas que tiene un número significativo de valores únicos y, como resultado, necesita muchos recursos para procesar sus resultados.   Por ejemplo, establecer un campo de identificador de producto o de descripción como filtrable o de navegación por facetas haría que fuera de alta cardinalidad ya que la mayoría de los valores de un documento a otro son únicos. Siempre que sea posible, limite el número de campos de alta cardinalidad.
3. **Aumentar el nivel de búsqueda:** Aumentar a nivel superior de Azure Search puede ser otra manera de mejorar el rendimiento de las consultas lentas.  Cada nivel superior también proporciona CPU más rápidas y más memoria, lo que puede tener un impacto positivo en el rendimiento de las consultas.

## <a name="scaling-for-availability"></a>Escalado para disponibilidad
Las réplicas no solo ayudan a reducir la latencia de las consultas, sino que también permiten una alta disponibilidad.  Con una sola réplica, puede esperar que se produzcan tiempos de inactividad periódicamente debido al reinicio del servidor después de las actualizaciones de software o por otros posibles eventos de mantenimiento.  Como resultado, es importante tener en cuenta si la aplicación requiere alta disponibilidad de búsquedas (consultas), así como de escrituras (eventos de indexación).  Búsqueda de Azure ofrece opciones de SLA en todas las ofertas de búsqueda de pago con los siguientes atributos:

* 2 réplicas para alta disponibilidad de cargas de trabajo de solo lectura (consultas)
* 3 o más réplicas para alta disponibilidad de cargas de trabajo de lectura-escritura (consultas e indización)

Para más información al respecto, visite el [Acuerdo de Nivel de Servicio de Búsqueda de Azure](https://azure.microsoft.com/support/legal/sla/search/v1_0/).

Como las réplicas son copias de los datos, tener varias réplicas permite que Búsqueda de Azure realice tareas de reinicio y mantenimiento del equipo en una réplica al mismo tiempo mientras las consultas se siguen ejecutando en las otras réplicas.  Por ese motivo, también deberá tener en cuenta cómo puede afectar este tiempo de inactividad a las consultas que ahora tienen que ejecutarse con una copia menos de los datos.

## <a name="scaling-geo-distributed-workloads-and-provide-geo-redundancy"></a>Escalado de cargas de trabajo distribuidas geográficamente y provisión de redundancia geográfica
Para las cargas de trabajo distribuidas geográficamente, encontrará que los usuarios que se encuentran lejos del centro de datos donde se hospeda el servicio Búsqueda de Azure tendrán tasas de latencia más altas.  Por este motivo, es importante tener varios servicios de búsqueda en las regiones más próximas a estos usuarios.  Búsqueda de Azure no proporciona actualmente un método automatizado de índices de Búsqueda de Azure con replicación geográfica entre regiones, pero existen algunas técnicas que permiten simplificar la implementación y administración de este proceso. Se describen en las siguientes secciones.

El objetivo de un conjunto de servicios de búsqueda distribuido geográficamente es tener dos o más índices disponibles en dos o más regiones en las que un usuario se enrutará al servicio Búsqueda de Azure que proporciona la latencia más baja, tal como se muestra en este ejemplo:

   ![Tablas de referencias cruzadas de servicios por región][1]

### <a name="keeping-data-in-sync-across-multiple-azure-search-services"></a>Sincronización de los datos entre varios servicios Búsqueda de Azure
Hay dos opciones para mantener los servicios de búsqueda distribuidos sincronizados: usar el [indexador de Azure Search](search-indexer-overview.md) o la API de inserción (también denominada [API de REST de Azure Search](https://docs.microsoft.com/rest/api/searchservice/)).  

### <a name="azure-search-indexers"></a>Indexadores de Búsqueda de Azure
Si usa el indexador de Azure Search, ya está importando los cambios de los datos desde un almacén de datos central, como Azure SQL DB o Azure Cosmos DB. Cuando se crea un nuevo servicio de búsqueda, puede crear también un indexador de Búsqueda de Azure para ese servicio que apunte a este mismo almacén de datos. De este modo, siempre que lleguen cambios nuevos al almacén de datos, los distintos indexadores los indexarán.  

Este es un ejemplo del aspecto que podría tener esa arquitectura.

   ![Origen de datos único con indexador distribuido y combinaciones de servicios][2]

### <a name="push-api"></a>API de inserción
Si usa la API de inserción de Búsqueda de Azure para [actualizar el contenido de su índice de Búsqueda de Azure](https://docs.microsoft.com/rest/api/searchservice/update-index)y desea mantener sincronizados los distintos servicios de búsqueda, inserte los cambios en todos los servicios de búsqueda cada vez que se necesite una actualización.  Al hacerlo es importante asegurarse de controlar los casos en los que se produce un error de actualización de un servicio de búsqueda y se realizan correctamente una o varias actualizaciones.

## <a name="leveraging-azure-traffic-manager"></a>Uso del Administrador de tráfico de Azure
[Administrador de tráfico de Azure](../traffic-manager/traffic-manager-overview.md) permite enrutar solicitudes a diversos sitios web con ubicación geográfica atendidos por distintos servicios Búsqueda de Azure.  Una ventaja del Administrador de tráfico es que puede sondear Búsqueda de Azure para asegurarse de que está disponible y enrutar los usuarios a servicios de búsqueda alternativos en caso de tiempo de inactividad.  Además, si va a enrutar solicitudes de búsqueda a través de Sitios web de Azure, el Administrador de tráfico de Azure permite equilibrar la carga de aquellos casos en los que el sitio web está activo pero Búsqueda de Azure no.  Este es un ejemplo de una arquitectura que utiliza el Administrador de tráfico.

   ![Tablas de referencias cruzadas de servicios por región con Administrador de tráfico central][3]

## <a name="monitoring-performance"></a>Supervisión del rendimiento
Búsqueda de Azure permite analizar y supervisar el rendimiento del servicio mediante [Search Traffic Analytics (STA)](search-traffic-analytics.md). Con STA también puede registrar las operaciones de búsqueda individuales, así como las métricas agregadas en una cuenta de Almacenamiento de Azure que después puede procesarse para su análisis o verse en Power BI.  Con las métricas de STA, puede revisar estadísticas de rendimiento tales como el número promedio de consultas o los tiempos de respuesta de las consultas.  Además, el registro de operaciones permite profundizar en los detalles de operaciones de búsqueda específicas.

STA es una herramienta valiosa para comprender las tasas de latencia desde el punto de vista de Búsqueda de Azure.  Como las métricas de rendimiento de las consultas registradas se basan en el tiempo que una consulta tarda en procesarse por completo en Búsqueda de Azure (desde el momento en que se solicita hasta que se envía), puede usarlas para determinar si los problemas de latencia se producen en el lado del servicio Búsqueda de Azure o fuera de servicio, por ejemplo, la latencia de la red.  

## <a name="next-steps"></a>Pasos siguientes
Para obtener más información acerca de los planes de tarifa y los límites de los servicios de cada uno de ellos, consulte [Límites de servicio en Búsqueda de Azure](search-limits-quotas-capacity.md).

Consulte [Escalado de niveles de recursos para cargas de trabajo de indexación y consulta en Búsqueda de Azure](search-capacity-planning.md) para obtener ayuda para elegir las combinaciones de particiones y réplicas.

Para más información sobre el rendimiento y para ver algunas demostraciones sobre cómo implementar las optimizaciones que se tratan en este artículo, vea el vídeo siguiente:

> [!VIDEO https://channel9.msdn.com/Events/Microsoft-Azure/AzureCon-2015/ACON319/player]
> 
> 

<!--Image references-->
[1]: ./media/search-performance-optimization/geo-redundancy.png
[2]: ./media/search-performance-optimization/scale-indexers.png
[3]: ./media/search-performance-optimization/geo-search-traffic-mgr.png
