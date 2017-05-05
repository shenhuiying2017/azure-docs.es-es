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
ms.date: 04/24/2017
ms.author: ashmaka
translationtype: Human Translation
ms.sourcegitcommit: 1cc1ee946d8eb2214fd05701b495bbce6d471a49
ms.openlocfilehash: 3720aed95706f6e8dd9a28085a6de0bc1f756a9b
ms.lasthandoff: 04/26/2017

---
# <a name="what-is-azure-search"></a>¿Qué es Azure Search?
Azure Search es una solución de búsqueda como servicio en la nube que delega la administración de los servidores y la infraestructura a Microsoft, dejando así un servicio listo para usar que puede completar con sus propios datos y usar para buscar en la aplicación web o móvil. Azure Search permite agregar fácilmente una sólida experiencia de búsqueda a las aplicaciones con una sencilla [API de REST](https://msdn.microsoft.com/library/azure/dn798935.aspx) o [SDK de .NET](search-howto-dotnet-sdk.md) sin necesidad de administrar la infraestructura de búsqueda o convertirse en un experto en esta materia.

## <a name="embed-a-powerful-search-experience-in-your-app-or-site"></a>Insertar una experiencia de búsqueda eficaz en una aplicación o un sitio


### <a name="full-text-search-and-text-analysis"></a>Búsqueda de texto completo y análisis de texto

Las consultas se pueden formular mediante la [sintaxis de consulta simple](https://docs.microsoft.com/rest/api/searchservice/simple-query-syntax-in-azure-search), que ofrece operadores lógicos, operadores de búsqueda de frase, operadores de sufijo y operadores de precedencia. Además, la [sintaxis de consulta Lucene](https://docs.microsoft.com/rest/api/searchservice/lucene-query-syntax-in-azure-search) puede habilitar la búsqueda aproximada, la búsqueda de proximidad, la priorización de términos y las expresiones regulares. Azure Search también admite [analizadores léxicos personalizados](https://docs.microsoft.com/rest/api/searchservice/custom-analyzers-in-azure-search) para permitir que su aplicación administre consultas de búsqueda complejas mediante coincidencia fonética y expresiones regulares.

### <a name="language-support"></a>Compatibilidad con idiomas

Azure Search admite analizadores léxicos para más de [56 idiomas distintos](https://docs.microsoft.com/rest/api/searchservice/language-support). Al usar analizadores de Lucene y analizadores de Microsoft (refinados gracias a varios años de procesamiento de lenguaje natural en Office y Bing), Azure Search puede analizar el texto en el cuadro de búsqueda de la aplicación para controlar de manera inteligente la lingüística específica del idioma, como por ejemplo, tiempos verbales, género, nombres plurales irregulares (por ejemplo, "mouse" frente a "ratones"), separación de palabras compuestas, separación de palabras (para idiomas sin espacios) y mucho más.

### <a name="data-integration"></a>Integración de datos

Puede insertar estructuras de datos JSON para rellenar un índice de Azure Search. Además, en el caso de los orígenes de datos compatibles, puede utilizar [indexadores](search-indexer-overview.md) para rastrear automáticamente Azure SQL Database, Azure DocumentDB o [Azure Blob Storage](search-howto-indexing-azure-blob-storage.md) para sincronizar el contenido del índice de búsqueda con el almacén de datos principal.

La **averiguación de documentos** permite indexar los [principales formatos de archivo](search-howto-indexing-azure-blob-storage.md), incluidos los documentos de Microsoft Office, PDF y HTML.

### <a name="search-experience"></a>Experiencia de búsqueda

+ Las **sugerencias de búsqueda** se pueden habilitar en las barras de búsqueda de texto autocompletado y las consultas de escritura automática. [Se sugieren los documentos correspondientes en el índice](https://docs.microsoft.com/rest/api/searchservice/suggesters) a medida que los usuarios escriben entradas de búsqueda parciales.

+ La **navegación por facetas** se habilita con [solo un parámetro de consulta](https://docs.microsoft.com/azure/search/search-faceted-navigation). Azure Search devuelve una estructura de navegación por facetas que puede utilizar como código subyacente a una lista de categorías para el filtrado autodirigido (por ejemplo, para filtrar los elementos de catálogo por intervalo de precios o marca).

+ Los **filtros** se pueden usar para incorporar fácilmente una navegación por facetas en la interfaz de usuario de la aplicación, mejorar la formulación de consultas y filtrar según los criterios especificados por el usuario o el desarrollador. Cree filtros mediante la [sintaxis de OData](https://docs.microsoft.com/rest/api/searchservice/odata-expression-syntax-for-azure-search).

+ El **resaltado de referencias** [aplica un formato visual a una palabra clave coincidente en los resultados de búsqueda](https://docs.microsoft.com/rest/api/searchservice/Search-Documents). Se puede elegir qué campos devuelven los fragmentos resaltados.

+ **puntuación simple** es una ventaja clave de Azure Search. Los [perfiles de puntuación](https://docs.microsoft.com/rest/api/searchservice/add-scoring-profiles-to-a-search-index) se utilizan para modelar la relevancia en función de los valores de los propios documentos. Por ejemplo, tal vez se desea que los productos más recientes o con descuento aparezcan arriba en los resultados de búsqueda. También se pueden crear perfiles de puntuación mediante etiquetas para puntuaciones personalizadas, según las preferencias de búsqueda de los clientes de las que se ha hecho seguimiento y se han almacenado por separado.

+ **clasificación** se ofrece para varios campos mediante el esquema de índice y luego se activa o se desactiva en el momento de consulta con un único parámetro de búsqueda.

+ **paginación** y limitación de los resultados de búsqueda se [aplican fácilmente con el control adaptado](search-pagination-page-layout.md) que Azure Search ofrece a través de los resultados de búsqueda.  

### <a name="geosearch"></a>Búsqueda geográfica

Azure Search procesa, filtra y muestra las ubicaciones geográficas de forma inteligente. Permite a los usuarios explorar datos basados en la proximidad de un resultado de búsqueda a una ubicación determinada o en una región geográfica específica. Este vídeo explica cómo funciona: [Channel 9: Azure Search and Geospatial data](https://channel9.msdn.com/Shows/Data-Exposed/Azure-Search-and-Geospatial-Data)(Azure Search y datos geoespaciales).

### <a name="cloud-service-advantages"></a>Ventajas del servicio en la nube

+ **alta disponibilidad** garantiza una experiencia de servicio de búsqueda muy confiable. Cuando se escala correctamente, [Azure Search ofrece un contrato de nivel de servicio del 99,9%](https://azure.microsoft.com/support/legal/sla/search/v1_0/).

+ **Completamente administrada** como una solución de un extremo a otro, Azure Search no requiere absolutamente ninguna administración de la infraestructura. El servicio se puede adaptar a sus necesidades mediante el escalado en dos dimensiones para controlar un mayor almacenamiento de documentos, mayores cargas de consultas o ambos.

### <a name="monitoring-and-reporting"></a>Supervisión e informes

+ El **análisis de tráfico de búsqueda** se [recopila y analiza](search-traffic-analytics.md) para conocer datos acerca de lo que los usuarios están escribiendo en el cuadro de búsqueda.

+ Las métricas sobre consultas por segundo, latencia y limitación se capturan y notifican en páginas del portal sin que sea necesaria ninguna configuración adicional. También puede supervisar fácilmente el índice y los recuentos de documentos para ajustar la capacidad según sea necesario. 

### <a name="tools-for-prototyping-and-inspection"></a>Herramientas para la creación de prototipos y la inspección

En el portal, puede usar el Asistente para la **importación de datos** para configurar los indexadores, el diseñador de índices para crear un índice y el **Explorador de búsquedas** para emitir consultas en todos los índices directamente desde la instancia de Azure Portal de su cuenta para probar consultas y refinar los perfiles de puntuación. También puede abrir cualquier índice para ver su esquema.

## <a name="how-it-works"></a>Cómo funciona
### <a name="step-1-provision-service"></a>Paso 1: Aprovisionar el servicio
Puede poner en marcha un servicio de Azure Search mediante [Azure Portal](https://portal.azure.com/) o la [API de administración de recursos de Azure](https://msdn.microsoft.com/library/azure/dn832684.aspx).

Según cómo se configure el servicio de búsqueda, usará el nivel Gratis que se comparte con otros suscriptores de Azure Search, o el [nivel de pago](https://azure.microsoft.com/pricing/details/search/) que ofrece recursos dedicados en exclusividad para su servicio. Al aprovisionar el servicio, también se elige la región del centro de datos que hospeda el servicio.

Según el nivel de servicio que elija, puede escalar el servicio en dos dimensiones: 1) agregar réplicas para aumentar su capacidad a fin de manejar grandes cargas de consultas y 2) agregar particiones para agregar almacenamiento para más documentos. Al controlar el almacenamiento de documentos y el rendimiento de consultas por separado, puede personalizar el servicio de búsqueda para sus necesidades específicas.

### <a name="step-2-create-index"></a>Paso 2: Crear un índice
Para cargar el contenido en el servicio Azure Search, primero debe definir un índice de Azure Search. Un índice es como una tabla de base de datos que contiene los datos y puede aceptar consultas de búsqueda. El esquema de índice se define para su asignación a la estructura de los documentos que desea buscar, igual que los campos de una base de datos.

Es posible crear el esquema de estos índices en Azure Portal, o mediante programación [con el SDK de .NET](search-howto-dotnet-sdk.md) o la [API de REST](https://msdn.microsoft.com/library/azure/dn798941.aspx). Una vez que se ha definido el índice, se pueden cargar los datos en el servicio Azure Search, donde se indexan seguidamente.

### <a name="step-3-index-data"></a>Paso 3: Indizar los datos
Una vez que haya definido los campos y los atributos del índice, estará listo para cargar el contenido en el índice. Se pueden emplear modelos de inserción o de extracción para la carga de los datos en el índice.

El modelo de extracción se proporciona a través de indexadores que se pueden configurar para actualizaciones bajo demanda o programadas (consulte [Operaciones de indexador (API de REST del servicio de Azure Search)](https://msdn.microsoft.com/library/azure/dn946891.aspx)), lo que permite introducir fácilmente datos y cambios de datos de Azure DocumentDB, Azure SQL Database, Azure Blob Storage o SQL Server hospedado en una máquina virtual de Azure.

El modelo de inserción se proporciona a través del SDK o las API de REST usadas para enviar documentos actualizados a un índice. Se pueden insertar datos desde prácticamente cualquier conjunto de datos usando el formato JSON. Consulte [Agregar, actualizar o eliminar documentos](https://msdn.microsoft.com/library/azure/dn798930.aspx) o [Cómo usar Azure Search desde una aplicación .NET](search-howto-dotnet-sdk.md) para obtener instrucciones sobre la carga de datos.

### <a name="step-4-search"></a>Paso 4: Buscar
Una vez que se ha completado el índice de Azure Search, podrá [emitir consultas de búsqueda](https://msdn.microsoft.com/library/azure/dn798927.aspx) al punto de conexión de servicio mediante solicitudes HTTP sencillas con la API de REST o el SDK de .NET.

## <a name="rest-api--net-sdk"></a>API de REST | .Net SDK

Aunque algunas tareas se pueden realizar en el portal, Azure Search está diseñada para desarrolladores que desean integrar la funcionalidad de búsqueda en aplicaciones ya existentes. Las siguientes interfaces de programación están disponibles.

|Plataforma |Descripción |
|-----|------------|
|[REST](https://docs.microsoft.com/rest/api/searchservice/) | Comandos HTTP admitidos por cualquier plataforma de programación y lenguaje, como Xamarin, Java y JavaScript|
|[.NET SDK](search-howto-dotnet-sdk.md) | El contenedor de .NET para la API de REST proporciona codificación eficaz en C# y otros lenguajes de código administrado que tienen como destino .NET Framework |

## <a name="free-trial"></a>Evaluación gratuita
Los suscriptores de Azure pueden [aprovisionar un servicio en el nivel Gratis](search-create-service-portal.md).

Si no es suscriptor, puede [abrir una cuenta de Azure de manera gratuita](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A261C142F): obtiene crédito que puede usar para probar los servicios de Azure de pago, e incluso una vez agotado este, podrá mantener la cuenta y usar servicios gratuitos de Azure, como Websites. Nunca se la hará ningún cargo en la tarjeta de crédito, a menos que cambie explícitamente la configuración y pida que se le realice algún cargo.

Además, puede [activar las ventajas de suscriptor de MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F): su suscripción a MSDN le proporciona crédito todos los meses que puede usar con servicios de Azure de pago. 

## <a name="watch-a-short-video"></a>Vea un vídeo breve

Los motores de búsqueda son los controladores habituales de recuperación de información en las aplicaciones móviles, en la web y en almacenes de datos corporativos. Azure Search le proporciona herramientas para crear una experiencia de búsqueda similar a la de los grandes sitios web comerciales. Este vídeo de 9 minutos del administrador de programas Liam Cavanagh explica cómo puede beneficiar la integración de un motor de búsqueda a la aplicación, las características principales de Azure Search y el aspecto de un flujo de trabajo típico. 

>[!VIDEO https://channel9.msdn.com/Events/Connect/2016/138/player]
 
+ Los minutos 0 a 3 describen las características y los casos de uso.
+ Los minutos 3 y 4 explican el aprovisionamiento del servicio. 
+ Los minutos 4 a 6 describen el Asistente para la importación de datos utilizado para crear un índice con el conjunto de datos integrados de propiedades inmobiliarias.
+ Los minutos 6 a 9 muestran el Explorador de búsquedas y varias consultas.



