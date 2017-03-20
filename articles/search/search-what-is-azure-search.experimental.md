---
title: "Qué es Azure Search | Microsoft Docs"
description: "Motor de búsqueda con características avanzadas para la barra de búsqueda de sitios web personalizados, aplicaciones y almacenes de datos y archivos corporativos."
services: search
manager: jhubbard
author: HeidiSteen
documentationcenter: 
ms.assetid: 
ms.service: search
ms.devlang: NA
ms.workload: search
ms.topic: article
ms.tgt_pltfrm: na
ms.date: 02/15/2017
ms.author: heidist
experiment_id: heidist-20170221
translationtype: Human Translation
ms.sourcegitcommit: 72b2d9142479f9ba0380c5bd2dd82734e370dee7
ms.openlocfilehash: 8f4947ba2641bc8a39a71945ff2a6818d1ff7830
ms.lasthandoff: 03/08/2017

---
# <a name="what-is-azure-search"></a>¿Qué es Búsqueda de Azure?

Azure Search ofrece un motor de búsqueda dedicado y programable con comportamientos de búsqueda inteligente que permite a la barra de búsquedas realizar búsquedas en el contenido del sitio web, en las aplicaciones y en los almacenes de datos o de archivos corporativos. 

![Barra de búsqueda con tecnología de Azure Search](./media/search-what-is-azure-search/search-powered-by-azsearch3.png)

Tener una experiencia de búsqueda sólida es algo necesario para la mayoría de aplicaciones móviles y web. La búsqueda de texto completo con términos de consulta que se completan automáticamente, correcciones ortográficas y coincidencias basadas en entradas semánticamente idénticas pero superficialmente diferentes (como "coche" y "auto") conforman las características mínimas de una barra de búsqueda, a pesar de la complejidad técnica que requiere poder ofrecer una experiencia así. Igualmente importantes son los requisitos operativos de escalabilidad, confiabilidad y sincronización entre la búsqueda y los almacenes de datos de back-end. 

Azure Search proporciona una funcionalidad completa que permite satisfacer sus requisitos operativos y de búsqueda.

![Barra y página de búsqueda personalizada con las características de búsqueda típicas](./media/search-what-is-azure-search/search-page-callouts3.png)

## <a name="how-it-works"></a>Cómo funciona

Para usar Azure Search, aprovisione un servicio de pago o uno gratis en su suscripción de Azure, cree y cargue un índice que contenga el contenido utilizable en búsquedas y, a continuación, llame a las API para emitir solicitudes de búsqueda y controlar los resultados. Se requiere un servicio de pago si desea recursos dedicados a escala.

|Nivel: |Descripción |
|-----|------------|
|[Gratis](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A261C142F) | Un servicio compartido que se puede aprovisionar rápidamente y se usa para cargas de trabajo pequeñas, las típicas en escenarios de aprendizaje y evaluación. |
|[Facturable](search-sku-tier.md) | Un servicio dedicado en niveles graduados de capacidad, desde alta densidad básica a estándar. Permite alojar una amplia variedad de configuraciones de implementación en diferentes planes de tarifa.|

Azure Search se ejecuta en la nube como un servicio administrado de Microsoft. Se puede integrar mediante código personalizado en cualquier plataforma de aplicaciones. Su servicio totalmente administrado y el contenido privado están globalmente disponibles, son programables, escalables y recuperables. 

Los servicios dedicados se ejecutan ininterrumpidamente a escala en el [99,9% de los Acuerdos de Nivel de Servicio](https://azure.microsoft.com/support/legal/sla/search/v1_0/) respaldados por Microsoft Azure.

## <a name="how-it-compares"></a>Comparación

Otros proveedores de servicios en la nube ofrecen motores de búsqueda personalizados con características que proporcionan una barra de búsqueda en aplicaciones personalizadas. Algunos ofrecen características de línea base similares, con búsqueda de texto completo, búsqueda geográfica y la capacidad para controlar un cierto nivel de ambigüedad en entradas de búsqueda. Normalmente, es una [característica especializada](#feature-drilldown) o la facilidad y sencillez general de las API, las herramientas y la administración, lo que determina la solución que mejor se ajusta a nuestras necesidades.

En comparación con otras soluciones de búsqueda, Azure Search es la más potente con cargas de trabajo de búsqueda de texto completo en almacenes de contenido y bases de datos de Azure, y ofrece un nivel adicional de funcionalidad que es necesario para las aplicaciones que se basan principalmente en la búsqueda para navegar por el contenido y recuperar información. Las principales ventajas incluyen:

+ Integración de datos de Azure (rastreadores o crawlers) en el nivel de indexación
+ Azure Portal para administración central
+ Escalado, confiabilidad y disponibilidad internacional de Azure
+ Análisis lingüístico y personalizado, con analizadores para la búsqueda de texto completo en 56 idiomas
+ Características principales comunes a las aplicaciones centradas en la búsqueda, incluida la relevancia, el uso de facetas, sugerencias, sinónimos, búsqueda geográfica y muchas más (que se muestran a continuación).

> [!Note]
> Totalmente compatibles con los orígenes de datos que no son de Azure. Puede canalizar cualquier colección de documentos JSON hacia un índice de Azure Search.

Los casos de uso capaces de utilizar la gama más amplia de características de Azure Search comprenden catálogos en línea, programas de línea de negocio y aplicaciones de detección de documentos.

### <a name="feature-drilldown"></a>Exploración en profundidad de las características

#### <a name="full-text-search-and-text-analysis"></a>Búsqueda de texto completo y análisis de texto

Las consultas se pueden formular mediante la [sintaxis de consulta simple](https://docs.microsoft.com/rest/api/searchservice/simple-query-syntax-in-azure-search), que ofrece operadores lógicos, operadores de búsqueda de frase, operadores de sufijo y operadores de precedencia. Además, la [sintaxis de consulta Lucene](https://docs.microsoft.com/rest/api/searchservice/lucene-query-syntax-in-azure-search) puede habilitar la búsqueda aproximada, la búsqueda de proximidad, la priorización de términos y las expresiones regulares. Azure Search también admite [analizadores léxicos personalizados](https://docs.microsoft.com/rest/api/searchservice/custom-analyzers-in-azure-search) para permitir que su aplicación administre consultas de búsqueda complejas mediante coincidencia fonética y expresiones regulares.

#### <a name="language-support"></a>Compatibilidad con idiomas

Azure Search admite analizadores léxicos para más de [56 idiomas distintos](https://docs.microsoft.com/rest/api/searchservice/language-support). Al usar analizadores de Lucene y analizadores de Microsoft (refinados gracias a varios años de procesamiento de lenguaje natural en Office y Bing), Azure Search puede analizar el texto en el cuadro de búsqueda de la aplicación para controlar de manera inteligente la lingüística específica del idioma, como por ejemplo, tiempos verbales, género, nombres plurales irregulares (por ejemplo, "mouse" frente a "ratones"), separación de palabras compuestas, separación de palabras (para idiomas sin espacios) y mucho más.

#### <a name="data-integration"></a>Integración de datos

Puede insertar estructuras de datos JSON para rellenar un índice de Azure Search. Además, en el caso de los orígenes de datos compatibles, puede utilizar [indexadores](search-indexer-overview.md) para rastrear automáticamente Azure SQL Database, Azure DocumentDB o [Azure Blob Storage](search-howto-indexing-azure-blob-storage.md) para sincronizar el contenido del índice de búsqueda con el almacén de datos principal.

La **averiguación de documentos** permite indexar los [principales formatos de archivo](search-howto-indexing-azure-blob-storage.md), incluidos los documentos de Microsoft Office, PDF y HTML.

#### <a name="search-experience"></a>Experiencia de búsqueda

+ Las **sugerencias de búsqueda** se pueden habilitar en las barras de búsqueda de texto autocompletado y las consultas de escritura automática. [Se sugieren los documentos correspondientes en el índice](https://docs.microsoft.com/rest/api/searchservice/suggesters) a medida que los usuarios escriben entradas de búsqueda parciales.

+ La **navegación por facetas** se habilita con [solo un parámetro de consulta](https://docs.microsoft.com/azure/search/search-faceted-navigation). Azure Search devuelve una estructura de navegación por facetas que puede utilizar como código subyacente a una lista de categorías para el filtrado autodirigido (por ejemplo, para filtrar los elementos de catálogo por intervalo de precios o marca).

+ Los **filtros** se pueden usar para incorporar fácilmente una navegación por facetas en la interfaz de usuario de la aplicación, mejorar la formulación de consultas y filtrar según los criterios especificados por el usuario o el desarrollador. Cree filtros mediante la [sintaxis de OData](https://docs.microsoft.com/rest/api/searchservice/odata-expression-syntax-for-azure-search).

+ El **resaltado de referencias** [aplica un formato visual a una palabra clave coincidente en los resultados de búsqueda](https://docs.microsoft.com/rest/api/searchservice/Search-Documents). Se puede elegir qué campos devuelven los fragmentos resaltados.

+ **puntuación simple** es una ventaja clave de Búsqueda de Azure. Los [perfiles de puntuación](https://docs.microsoft.com/rest/api/searchservice/add-scoring-profiles-to-a-search-index) se utilizan para modelar la relevancia en función de los valores de los propios documentos. Por ejemplo, tal vez se desea que los productos más recientes o con descuento aparezcan arriba en los resultados de búsqueda. También se pueden crear perfiles de puntuación mediante etiquetas para puntuaciones personalizadas, según las preferencias de búsqueda de los clientes de las que se ha hecho seguimiento y se han almacenado por separado.

+ **clasificación** se ofrece para varios campos mediante el esquema de índice y luego se activa o se desactiva en el momento de consulta con un único parámetro de búsqueda.

+ **paginación** y limitación de los resultados de búsqueda se [aplican fácilmente con el control adaptado](search-pagination-page-layout.md) que Búsqueda de Azure ofrece a través de los resultados de búsqueda.  

#### <a name="geosearch"></a>Búsqueda geográfica

Azure Search procesa, filtra y muestra las ubicaciones geográficas de forma inteligente. Permite a los usuarios explorar datos basados en la proximidad de un resultado de búsqueda a una ubicación determinada o en una región geográfica específica. Este vídeo explica cómo funciona: [Channel 9: Azure Search and Geospatial data](https://channel9.msdn.com/Shows/Data-Exposed/Azure-Search-and-Geospatial-Data)(Búsqueda de Azure y datos geoespaciales).

#### <a name="cloud-service-advantages"></a>Ventajas del servicio en la nube

+ **alta disponibilidad** garantiza una experiencia de servicio de búsqueda muy confiable. Cuando se escala correctamente, [Búsqueda de Azure ofrece un contrato de nivel de servicio del 99,9%](https://azure.microsoft.com/support/legal/sla/search/v1_0/).

+ **Completamente administrada** como una solución de un extremo a otro, Búsqueda de Azure no requiere absolutamente ninguna administración de la infraestructura. El servicio se puede adaptar a sus necesidades mediante el escalado en dos dimensiones para controlar un mayor almacenamiento de documentos, mayores cargas de consultas o ambos.

#### <a name="monitoring-and-reporting"></a>Supervisión e informes

+ El **análisis de tráfico de búsqueda** se [recopila y analiza](search-traffic-analytics.md) para conocer datos acerca de lo que los usuarios están escribiendo en el cuadro de búsqueda.

+ Las métricas sobre consultas por segundo, latencia y limitación se capturan y notifican en páginas del portal sin que sea necesaria ninguna configuración adicional. También puede supervisar fácilmente el índice y los recuentos de documentos para ajustar la capacidad según sea necesario. 

#### <a name="tools-for-prototyping-and-inspection"></a>Herramientas para la creación de prototipos y la inspección

En el portal, puede usar el Asistente para la **importación de datos** para configurar los indexadores, el diseñador de índices para crear un índice y el **Explorador de búsquedas** para emitir consultas en todos los índices directamente desde la instancia de Azure Portal de su cuenta para probar consultas y refinar los perfiles de puntuación. También puede abrir cualquier índice para ver su esquema.

## <a name="how-to-get-started"></a>Primeros pasos

Comience con un servicio gratuito y, a continuación, [cree y consulte un índice mediante datos integrados de ejemplo](search-get-started-portal.md). Puede usar el portal para todas las tareas, lo cual es una manera rápida de probar Azure Search antes de tener que escribir ningún código.

1. Los suscriptores de Azure pueden [aprovisionar un servicio en el nivel Gratis](search-create-service-portal.md).

  Los no suscriptores pueden [abrir una cuenta de Azure de forma gratuita](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A261C142F) y usar los créditos gratuitos para probar los servicios de Azure de pago. Después de que se agoten los créditos, puede mantener la cuenta y usar los servicios y características gratuitos de Azure como, por ejemplo, Websites. Nunca se la hará ningún cargo en la tarjeta de crédito, a menos que cambie explícitamente la configuración y pida que se le realice algún cargo.

  Opcionalmente, [puede activar los beneficios de suscriptores de MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F). Su suscripción a MSDN le proporciona créditos todos los meses que puede usar para servicios de Azure de pago. 

2. Ejecute el Asistente para la **importación de datos** sobre el [conjunto de datos de ejemplo de propiedades inmobiliarias](search-get-started-portal.md#create-index).

  Este asistente puede generar y cargar un índice desde la mayoría de orígenes de datos de Azure. Se requiere código para los orígenes de datos que el asistente no admite específicamente.

3. Use el [Explorador de búsquedas](search-get-started-portal.md#query-index) para consultar el índice.

## <a name="rest-api--net-sdk"></a>API de REST | .Net SDK

Aunque algunas tareas se pueden realizar en el portal, Azure Search está diseñada para desarrolladores que desean integrar la funcionalidad de búsqueda en aplicaciones ya existentes. Las siguientes interfaces de programación están disponibles.

|Plataforma |Descripción |
|-----|------------|
|[REST](https://docs.microsoft.com/rest/api/searchservice/) | Comandos HTTP admitidos por cualquier plataforma de programación y lenguaje, como Xamarin, Java y JavaScript|
|[.NET SDK](search-howto-dotnet-sdk.md) | El contenedor de .NET para la API de REST proporciona codificación eficaz en C# y otros lenguajes de código administrado que tienen como destino .NET Framework |

## <a name="watch-a-short-video"></a>Vea un vídeo breve

Los motores de búsqueda son los controladores habituales de recuperación de información en las aplicaciones móviles, en la web y en almacenes de datos corporativos. Azure Search le proporciona herramientas para crear una experiencia de búsqueda similar a la de los grandes sitios web comerciales. Este vídeo de 9 minutos del administrador de programas Liam Cavanagh explica cómo puede beneficiar la integración de un motor de búsqueda a la aplicación, las características principales de Azure Search y el aspecto de un flujo de trabajo típico. 

>[!VIDEO https://channel9.msdn.com/Events/Connect/2016/138/player]
 
+ Los minutos&0; a&3; describen las características y los casos de uso.
+ Los minutos&3; y&4; explican el aprovisionamiento del servicio. 
+ Los minutos&4; a&6; describen el Asistente para la importación de datos utilizado para crear un índice con el conjunto de datos integrados de propiedades inmobiliarias.
+ Los minutos&6; a&9; muestran el Explorador de búsquedas y varias consultas.


