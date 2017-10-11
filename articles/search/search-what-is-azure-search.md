---
title: "Qué es Azure Search | Microsoft Docs"
description: "Azure Search es un servicio de búsqueda completamente administrado hospedado en la nube. Conozca más en la información general de esta característica."
services: search
manager: jhubbard
author: ashmaka
documentationcenter: 
ms.assetid: 50bed849-b716-4cc9-bbbc-b5b34e2c6153
ms.service: search
ms.devlang: NA
ms.workload: search
ms.topic: article
ms.tgt_pltfrm: na
ms.date: 06/26/2017
ms.author: ashmaka
ms.openlocfilehash: baf73639eb6506b14d0d3a4de1bf55b66e973b95
ms.sourcegitcommit: 02e69c4a9d17645633357fe3d46677c2ff22c85a
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 08/03/2017
---
# <a name="what-is-azure-search"></a>¿Qué es Azure Search?
Azure Search es una solución de búsqueda como servicio en la nube que ofrece a los desarrolladores las API y las herramientas necesarias para agregar una experiencia de búsqueda de datos enriquecida en las aplicaciones web, para dispositivos móviles y empresariales.

La funcionalidad se expone a través de [API de REST](/rest/api/searchservice/) o [SDK de .NET](search-howto-dotnet-sdk.md) sencillos que enmascaran la complejidad inherente de la tecnología de búsqueda. Además de las API, Azure Portal proporciona ofrece administración y compatibilidad con la creación de prototipos. La infraestructura y la disponibilidad las administra Microsoft.

<a name="feature-drilldown"></a>

## <a name="feature-summary"></a>Resumen de características

| Categoría | Características |
|----------|----------|
|Búsqueda de texto completo y análisis de texto | [**Búsqueda de texto completo**](search-lucene-query-architecture.md) es el principal caso de uso para la mayoría de las aplicaciones basadas en búsquedas. Las consultas se formulan con una sintaxis compatible: <br/><br/>[**Sintaxis de consulta simple**](https://docs.microsoft.com/rest/api/searchservice/simple-query-syntax-in-azure-search), que ofrece operadores lógicos, de búsqueda de frase, de sufijo y de precedencia.<br/><br/>[**Sintaxis de consulta Lucene**](https://docs.microsoft.com/rest/api/searchservice/lucene-query-syntax-in-azure-search) es compatible con cualquier búsqueda aproximada, la búsqueda por proximidad, la priorización de términos y las expresiones regulares.| 
| Integración de datos | Los índices de Azure Search aceptan datos de cualquier origen, siempre que se envíe como estructura de datos JSON. <br/><br/> Opcionalmente, en el caso de los orígenes de datos compatibles en Azure, puede utilizar [**indexadores**](search-indexer-overview.md) para rastrear automáticamente [Azure SQL Database](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md), [Azure Cosmos DB](search-howto-index-documentdb.md) o [Azure Blob Storage](search-howto-indexing-azure-blob-storage.md) y sincronizar el contenido del índice de búsqueda con el almacén de datos principal. Los indexadores de Azure Blog pueden realizar la *averiguación de documentos* para [indexar los principales formatos de archivo](search-howto-indexing-azure-blob-storage.md), incluidos los documentos de Microsoft Office, PDF y HTML. |
| Análisis de búsqueda | Los [**analizadores léxicos personalizados**](https://docs.microsoft.com/rest/api/searchservice/custom-analyzers-in-azure-search) están disponibles para las consultas de búsqueda complejas, mediante la coincidencia de fonética y expresiones regulares. |
| Compatibilidad con idiomas | Los [**analizadores de idioma**](https://docs.microsoft.com/rest/api/searchservice/language-support) de Lucene, así como los procesadores de idioma natural de Microsoft disponibles en 56 idiomas para analizar el texto del cuadro de búsqueda de la aplicación permiten controlar de manera inteligente la lingüística específica del idioma, como por ejemplo, tiempos verbales, género, nombres plurales irregulares (por ejemplo, "régimen" frente a "regímenes"), separación de palabras compuestas, separación de palabras (para idiomas sin espacios) y mucho más. |
| Búsqueda georreferenciada | Azure Search procesa, filtra y muestra las ubicaciones geográficas de forma inteligente. Permite a los usuarios explorar datos basados en la proximidad de un resultado de búsqueda a una ubicación física. [Vea este vídeo](https://channel9.msdn.com/Shows/Data-Exposed/Azure-Search-and-Geospatial-Data) o [revise este ejemplo](https://github.com/Azure-Samples/search-dotnet-asp-net-mvc-jobs) para más información. |
| Características de la experiencia del usuario | En una barra de búsqueda se pueden habilitar las [**sugerencias de búsqueda**](https://docs.microsoft.com/rest/api/searchservice/suggesters) para las consultas de escritura automática. Se sugieren los documentos correspondientes en el índice a medida que los usuarios escriben entradas de búsqueda parciales. <br/><br/>La [**navegación por facetas**](https://docs.microsoft.com/azure/search/search-faceted-navigation) se habilita con un único parámetro de consulta. Azure Search devuelve una estructura de navegación por facetas que puede utilizar como código subyacente a una lista de categorías para el filtrado autodirigido (por ejemplo, para filtrar los elementos de catálogo por intervalo de precios o marca). <br/><br/> Se pueden usar [**filtros**](https://docs.microsoft.com/rest/api/searchservice/odata-expression-syntax-for-azure-search) para incorporar fácilmente la navegación por facetas en la interfaz de usuario de la aplicación, mejorar la formulación de consulta y filtrar según los criterios especificados por el usuario o el desarrollador. Los filtros se crean con la sintaxis de OData.<br/><br/> El [**resaltado de referencias**](https://docs.microsoft.com/rest/api/searchservice/Search-Documents) aplica un formato visual a una palabra clave coincidente en los resultados de búsqueda. Se puede elegir qué campos devuelven los fragmentos resaltados.<br/><br/>La [**ordenación**](https://docs.microsoft.com/rest/api/searchservice/Search-Documents) se ofrece para varios campos mediante el esquema de índice y se activa o se desactiva en el momento de la consulta con un único parámetro de búsqueda.<br/><br/> La [**paginación**](search-pagination-page-layout.md) y la limitación de los resultados de búsqueda se aplican fácilmente con el control de precisión que Azure Search ofrece para los resultados de búsqueda.  
| Relevancia | La [**puntuación simple**](/rest/api/searchservice/add-scoring-profiles-to-a-search-index) es una ventaja clave de Azure Search. Los perfiles de puntuación sirven para modelar la relevancia en función de los valores de los propios documentos. Por ejemplo, tal vez se desea que los productos más recientes o con descuento aparezcan arriba en los resultados de búsqueda. También se pueden crear perfiles de puntuación mediante etiquetas para puntuaciones personalizadas, según las preferencias de búsqueda de los clientes de las que se ha hecho seguimiento y se han almacenado por separado. |
| Supervisión e informes | Los [**datos de análisis del tráfico de búsqueda**](search-traffic-analytics.md) se recopilan y se analizan para conocer información acerca de lo que los usuarios escriben en el cuadro de búsqueda. <br/><br/>Las métricas sobre consultas por segundo, latencia y limitación se capturan y notifican en páginas del portal sin que sea necesaria ninguna configuración adicional. También puede supervisar fácilmente el índice y los recuentos de documentos para ajustar la capacidad según sea necesario. Para más información, consulte el artículo sobre la [administración de servicios](search-manage.md). |
| Herramientas para la creación de prototipos y la inspección | En el portal, puede usar el [**Asistente para importación de datos**](search-import-data-portal.md) para configurar los indexadores, el diseñador de índices para crear un índice y el [**Explorador de búsqueda**](search-explorer.md) para probar consultas y perfeccionar los perfiles de puntuación. También puede abrir cualquier índice para ver su esquema. |
| Infraestructura | La **plataforma de alta disponibilidad** garantiza una experiencia de servicio de búsqueda muy confiable. Cuando se escala correctamente, [Azure Search ofrece un contrato de nivel de servicio del 99,9%](https://azure.microsoft.com/support/legal/sla/search/v1_0/).<br/><br/> **Totalmente administrada y escalable** como cualquier solución completa, Azure Search no requiere ninguna administración de la infraestructura en absoluto. El servicio se puede adaptar a sus necesidades mediante el escalado en dos dimensiones para controlar un mayor almacenamiento de documentos, mayores cargas de consultas o ambos.

## <a name="how-it-works"></a>Cómo funciona
### <a name="step-1-provision-service"></a>Paso 1: Aprovisionar el servicio
Puede poner en marcha un servicio de Azure Search en [Azure Portal](https://portal.azure.com/) o mediante la [API de administración de recursos de Azure](/rest/api/searchmanagement/). Puede elegir cualquier servicio gratuito compartido con otros suscriptores o un [nivel de pago](https://azure.microsoft.com/pricing/details/search/) que dedica recursos utilizados solo por su servicio. Para los niveles de pago, es posible escalar un servicio en dos dimensiones: 

- Agregar réplicas para aumentar su capacidad de administrar cargas de consulta elevadas.   
- Agregar particiones para aumentar el almacenamiento de más documentos. 

Controlar el almacenamiento de documentos y el procesamiento de consultas por separado, con lo que podrá calibrar los recursos en función de los requisitos de producción.

### <a name="step-2-create-index"></a>Paso 2: Crear un índice
Para cargar el contenido en el que quiere realizar búsquedas, primero debe definir un índice de Azure Search. Un índice es como una tabla de base de datos que contiene los datos y puede aceptar consultas de búsqueda. El esquema de índice se define para su asignación y reflejo en la estructura de los documentos que desea buscar, igual que los campos de una base de datos.

Es posible crear un esquema en Azure Portal, o mediante programación con el [SDK de .NET](search-howto-dotnet-sdk.md) o la [API de REST](/rest/api/searchservice/).

### <a name="step-3-index-data"></a>Paso 3: Indizar los datos
Una vez definido un índice, estará listo para cargar contenido. Puede usar un modelo de inserción o de extracción.

El modelo de extracción recupera datos de orígenes de datos externos. Se admite mediante *indexadores* que agilizan y automatizan diversos aspectos de la ingestión de datos, como conectarse, leer y serializar datos. Los [indexadores](/rest/api/searchservice/Indexer-operations) están disponibles para Azure Cosmos DB, Azure SQL Database, Azure Blob Storage y SQL Server hospedado en Azure Virtual Machines. Puede configurar un indexador para una actualización de datos programada o a petición.

El modelo de inserción se proporciona a través del SDK o las API de REST usadas para enviar documentos actualizados a un índice. Se pueden insertar datos desde prácticamente cualquier conjunto de datos usando el formato JSON. Consulte [Agregar, actualizar o eliminar documentos](/rest/api/searchservice/addupdate-or-delete-documents) o [Cómo usar Azure Search desde una aplicación .NET](search-howto-dotnet-sdk.md) para obtener instrucciones sobre la carga de datos.

### <a name="step-4-search"></a>Paso 4: Buscar
Una vez completado un índice, podrá [emitir consultas de búsqueda](/rest/api/searchservice/Search-Documents) al punto de conexión de servicio mediante solicitudes HTTP sencillas con la API de REST o el SDK de .NET.

## <a name="how-it-compares"></a>Comparación

Los clientes a menudo se preguntan cómo se puede comparar la [búsqueda de texto completo en Azure Search](search-lucene-query-architecture.md) con la [búsqueda de texto completo](https://en.wikipedia.org/wiki/Full_text_search) en su producto de bases de datos. Nuestra respuesta es que las funcionalidades de lenguaje de Azure Search son más complejas y flexibles, gracias a la compatibilidad para consultas de Lucene, analizadores de lenguaje de Lucene y Microsoft, analizadores personalizados para entradas fonéticas o entradas especializadas de otro tipo y capacidad de combinar datos de varios orígenes en el índice de búsqueda. 

Otro punto de inflexión es que una solución de búsqueda se ocupe de toda la experiencia de búsqueda. Por ejemplo, podría desear tener una puntuación personalizada de los resultados, navegación por facetas para un filtrado autodirigido, resaltado de referencias y sugerencias de consulta de escritura anticipada. 

Las herramientas de supervisión y comprensión de la actividad de consulta también se pueden tener en cuenta en una decisión sobre la solución de búsqueda. Por ejemplo, Azure Search admite [búsqueda de análisis de tráfico](search-traffic-analytics.md) para métricas de porcentaje de clics, búsquedas más populares búsquedas sin clics, etc. En productos en los que la búsqueda es un complemento, no tendrá muchas probabilidades de que encontrar estas características.

El uso de recursos es otro punto que se debe tener en cuenta. La búsqueda en lenguaje natural requiere a menudo un proceso intensivo. Algunos de nuestros clientes descargaban las operaciones de búsqueda en Azure Search como modo de conservar los recursos del equipo para el procesamiento de transacciones. Al externalizar la búsqueda, puede ajustar fácilmente la escala para que se adapte al volumen de consultas.

Una vez que haya decidido optar por la búsqueda dedicada, su decisión siguiente será elegir entre un servicio en la nube y un servidor local. Un servicio en la nube es la opción adecuada si desea una [solución llave en mano con costes generales y un mantenimiento mínimos, y una escala ajustable](#cloud-service-advantage).

Dentro del paradigma de la nube, varios proveedores ofrecen características de línea base similares, con búsqueda de texto completo, búsqueda geográfica y capacidad de controlar cierto nivel de ambigüedad en entradas de búsqueda. Normalmente, es una [característica especializada](#feature-drilldown) o la facilidad y sencillez general de las API, las herramientas y la administración, lo que determina la solución que mejor se ajusta a nuestras necesidades.

Entre los proveedores de servicios en la nube, Azure Search es el más potente para cargas de trabajo de búsqueda de texto completo en almacenes de contenido y bases de datos de Azure, y para aplicaciones que se basan principalmente en la búsqueda para navegar por el contenido y recuperar información. Las principales ventajas incluyen:

+ Integración de datos de Azure (rastreadores o crawlers) en el nivel de indexación
+ Azure Portal para administración central
+ Escalado, confiabilidad y disponibilidad internacional de Azure
+ Análisis lingüístico y personalizado, con analizadores para la búsqueda de texto completo en 56 idiomas
+ [Características principales comunes a las aplicaciones centradas en la búsqueda](#feature-drilldown): puntuación, uso de facetas, sugerencias, sinónimos, búsqueda geográfica y muchas más.

> [!Note]
> Como aclaración, los orígenes de datos que no sean de Azure son totalmente compatibles pero se basan en una metodología de inserción de código más intensiva en lugar de indexadores. Con nuestras API, puede canalizar cualquier colección de documentos JSON hacia un índice de Azure Search.

Entre nuestros clientes, los que son capaces de utilizar la gama más amplia de características de Azure Search pueden usar catálogos en línea, programas de línea de negocio y aplicaciones de detección de documentos.

## <a name="rest-api--net-sdk"></a>API de REST | .Net SDK

Aunque muchas tareas se pueden realizar en el portal, Azure Search está diseñado para desarrolladores que desean integrar la funcionalidad de búsqueda en aplicaciones ya existentes. Las siguientes interfaces de programación están disponibles.

|Plataforma |Descripción |
|-----|------------|
|[REST](/rest/api/searchservice/) | Comandos HTTP admitidos por cualquier plataforma de programación y lenguaje, como Xamarin, Java y JavaScript|
|[.NET SDK](search-howto-dotnet-sdk.md) | El contenedor de .NET para la API de REST proporciona codificación eficaz en C# y otros lenguajes de código administrado que tienen como destino .NET Framework |

## <a name="free-trial"></a>Evaluación gratuita
Los suscriptores de Azure pueden [aprovisionar un servicio en el nivel Gratis](search-create-service-portal.md).

Si no es un suscriptor, puede [abrir una cuenta de Azure de forma gratuita](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A261C142F). Obtenga créditos que puede usar para probar los servicios de Azure de pago. Después de que se agoten los créditos, puede mantener la cuenta y usar los [servicios gratuitos de Azure](https://azure.microsoft.com/free/). No se le realizará ningún cargo en su tarjeta de crédito a menos que cambie explícitamente la configuración y lo solicite.

Además, puede [activar las ventajas de suscriptor de MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F): su suscripción a MSDN le proporciona crédito todos los meses que puede usar con servicios de Azure de pago. 

## <a name="how-to-get-started"></a>Primeros pasos

1. Cree un servicio en el [nivel Gratis](search-create-service-portal.md).

2. Consulte los pasos de uno o varios de los siguientes tutoriales. 

  + El artículo sobre el [uso del SDK de .NET](search-howto-dotnet-sdk.md) muestra los pasos principales en código administrado.  
  + El artículo de [introducción a la API de REST](https://github.com/Azure-Samples/search-rest-api-getting-started) muestra los mismos pasos con la API de REST.  
  + El tutorial para [crear el primer índice en el portal](search-get-started-portal.md) muestra las características integradas de indexación y prototipo.   

Los motores de búsqueda son los controladores habituales de recuperación de información en las aplicaciones móviles, en la web y en almacenes de datos corporativos. Azure Search le proporciona herramientas para crear una experiencia de búsqueda similar a la de los grandes sitios web comerciales.

En este vídeo de 9 minutos del administrador de programas Liam Cavanagh, aprenda cómo puede beneficiar a su aplicación la integración de un motor de búsqueda. Unas breves demostraciones presentan las características clave de Azure Search y el aspecto típico que tiene un flujo de trabajo. 

>[!VIDEO https://channel9.msdn.com/Events/Connect/2016/138/player]
 
+ Los minutos 0 a 3 describen las características y los casos de uso.
+ Los minutos 3 y 4 explican el aprovisionamiento del servicio. 
+ Los minutos 4 a 6 describen el Asistente para la importación de datos utilizado para crear un índice con el conjunto de datos integrados de propiedades inmobiliarias.
+ Los minutos 6 a 9 muestran el Explorador de búsquedas y varias consultas.


