---
title: "Qué es Azure Search | Microsoft Docs"
description: "Búsqueda de Azure es un servicio de búsqueda completamente administrado hospedado en la nube. Conozca más en la información general de esta característica."
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
ms.date: 01/12/2017
ms.author: ashmaka
experiment_id: heidist-experiment1-20170221
translationtype: Human Translation
ms.sourcegitcommit: 292c9150822363aba3336b1efce579dc5362cb14
ms.openlocfilehash: 99b3babee9b252b2d741515391295d8b1dc5c747

---
# <a name="what-is-azure-search"></a>¿Qué es Búsqueda de Azure?
Azure Search es una solución de búsqueda como servicio en la nube que delega la administración de los servidores y la infraestructura a Microsoft, dejando así un servicio listo para usar que puede completar con sus propios datos y usar para buscar en la aplicación web o móvil. Azure Search permite agregar fácilmente una sólida experiencia de búsqueda a las aplicaciones con una sencilla [API de REST](https://msdn.microsoft.com/library/azure/dn798935.aspx) o [SDK de .NET](search-howto-dotnet-sdk.md) sin necesidad de administrar la infraestructura de búsqueda o convertirse en un experto en esta materia.

## <a name="give-your-users-a-powerful-search-experience"></a>Ofrezca a los usuarios una eficaz experiencia de búsqueda
**consultas eficaces** con la [sintaxis de consulta simple](https://msdn.microsoft.com/library/azure/dn798920.aspx), que ofrece operadores lógicos, operadores de búsqueda de frase, operadores de sufijo y operadores de precedencia. Además, la [sintaxis de consulta Lucene](https://msdn.microsoft.com/library/azure/mt589323.aspx) puede habilitar la búsqueda aproximada, la búsqueda de proximidad, la priorización de términos y las expresiones regulares. Búsqueda de Azure también admite analizadores léxicos personalizados para permitir que su aplicación administre consultas de búsqueda complejas mediante la coincidencia fonética y las expresiones regulares.

**compatibilidad** con [56 idiomas diferentes](https://msdn.microsoft.com/library/azure/dn879793.aspx). Al usar analizadores de Lucene y analizadores de Microsoft (refinados gracias a varios años de procesamiento de lenguaje natural en Office y Bing), Azure Search puede analizar el texto en el cuadro de búsqueda de la aplicación para controlar de manera inteligente la lingüística específica del idioma, como por ejemplo, tiempos verbales, género, nombres plurales irregulares (por ejemplo, "mouse" frente a "ratones"), separación de palabras compuestas, separación de palabras (para idiomas sin espacios) y mucho más.

**sugerencias de búsqueda** pueden habilitarse las barras de búsqueda de autocompletado y las consultas de escritura automática. [Se sugieren los documentos correspondientes en el índice](https://msdn.microsoft.com/library/azure/dn798936.aspx) a medida que los usuarios escriben entradas de búsqueda parciales.

**El resaltado de referencias** [permite](https://msdn.microsoft.com/library/azure/dn798927.aspx) a los usuarios ver el fragmento de texto de cada resultado que contenga las coincidencias para su consulta. Se puede elegir qué campos devuelven fragmentos resaltados.

**La navegación por facetas** se agrega fácilmente a la página de resultados de búsqueda con Búsqueda de Azure. Con [simplemente un parámetro de consulta](https://msdn.microsoft.com/library/azure/dn798927.aspx), Azure Search devolverá toda la información necesaria para construir una experiencia de búsqueda por facetas en la interfaz de usuario de la aplicación para permitir que los usuarios profundicen en los resultados de la búsqueda y los filtren (por ejemplo, filtrar elementos de un catálogo por rango de precios o marca).

**geoespacial** permite procesar, filtrar y mostrar de forma inteligente las ubicaciones geográficas. Búsqueda de Azure permite a los usuarios explorar datos basados en la proximidad de un resultado de búsqueda a una ubicación determinada o en una región geográfica específica. Este vídeo explica cómo funciona: [Channel 9: Azure Search and Geospatial data](https://channel9.msdn.com/Shows/Data-Exposed/Azure-Search-and-Geospatial-Data)(Búsqueda de Azure y datos geoespaciales).

**filtros** se pueden usar para incorporar fácilmente una navegación por facetas en la interfaz de usuario de la aplicación, mejorar la formulación de consultas y filtrar según los criterios especificados por el usuario o el desarrollador. Cree filtros eficaces mediante la [sintaxis de OData](https://msdn.microsoft.com/library/azure/dn798921.aspx).

## <a name="empower-your-developers-with-an-easy-to-use-service"></a>Faculte a los desarrolladores con un servicio de fácil de usar
**alta disponibilidad** garantiza una experiencia de servicio de búsqueda muy confiable. Cuando se escala correctamente, [Búsqueda de Azure ofrece un contrato de nivel de servicio del 99,9%](https://azure.microsoft.com/support/legal/sla/search/v1_0/).

**Completamente administrada** como una solución de un extremo a otro, Búsqueda de Azure no requiere absolutamente ninguna administración de la infraestructura. El servicio se puede adaptar fácilmente a sus necesidades mediante el escalado en dos dimensiones para controlar más almacenamiento de documentos, mayores cargas de consultas o ambos.

La **integración de datos** con [indexadores](https://msdn.microsoft.com/library/azure/dn946891.aspx) permite a Azure Search rastrear automáticamente Azure SQL Database, Azure DocumentDB o [Azure Blob Storage](search-howto-indexing-azure-blob-storage.md) para sincronizar el contenido del índice de búsqueda con el almacén de datos principal.

**averiguación de documentos** está disponible (actualmente en versión preliminar) para [leer e indexar los principales formatos de archivo](search-howto-indexing-azure-blob-storage.md) , por ejemplo, documentos de Microsoft Office, PDF y HTML.

El **análisis de tráfico de búsqueda** se [recopila y analiza fácilmente](search-traffic-analytics.md) para conocer qué usuarios están escribiendo en el cuadro de búsqueda.

**puntuación simple** es una ventaja clave de Búsqueda de Azure. [perfiles de puntuación](https://msdn.microsoft.com/library/azure/dn798928.aspx) se utilizan para permitir que las organizaciones modelen la relevancia en función de los valores de los propios documentos. Por ejemplo, tal vez se desea que los productos más recientes o con descuento aparezcan arriba en los resultados de búsqueda. También se pueden crear perfiles de puntuación mediante etiquetas para puntuaciones personalizadas, según las preferencias de búsqueda de los clientes de las que se ha hecho seguimiento y se han almacenado por separado.

**clasificación** se ofrece para varios campos mediante el esquema de índice y luego se activa o se desactiva en el momento de consulta con un único parámetro de búsqueda.

**paginación** y limitación de los resultados de búsqueda se [aplican fácilmente con el control adaptado](search-pagination-page-layout.md) que Búsqueda de Azure ofrece a través de los resultados de búsqueda.  

**explorador de Búsqueda** permite emitir consultas sobre todos los índices directamente desde el Portal de Azure de su cuenta para que pueda probar consultas y perfeccionar los perfiles de puntuación fácilmente.

## <a name="how-it-works"></a>Cómo funciona
### <a name="1-provision-service"></a>1. Servicio de aprovisionamiento
Puede poner en marcha un servicio de Azure Search mediante [Azure Portal](https://portal.azure.com/) o la [API de administración de recursos de Azure](https://msdn.microsoft.com/library/azure/dn832684.aspx).

Según cómo se configure el servicio de búsqueda, usará el nivel Gratis que se comparte con otros suscriptores de Búsqueda de Azure, o el [nivel de pago](https://azure.microsoft.com/pricing/details/search/) que ofrece recursos dedicados en exclusividad para su servicio. Al aprovisionar el servicio, también se elige la región del centro de datos que hospeda el servicio.

Según el nivel de servicio que elija, puede escalar el servicio en dos dimensiones: 1) agregar réplicas para aumentar su capacidad a fin de manejar grandes cargas de consultas y 2) agregar particiones para agregar almacenamiento para más documentos. Al controlar el almacenamiento de documentos y el rendimiento de consultas por separado, puede personalizar el servicio de búsqueda para sus necesidades específicas.

### <a name="2-create-index"></a>2. Creación de índice
Para cargar el contenido en el servicio Búsqueda de Azure, primero debe definir un índice de Búsqueda de Azure. Un índice es como una tabla de base de datos que contiene los datos y puede aceptar consultas de búsqueda. El esquema de índice se define para su asignación a la estructura de los documentos que desea buscar, igual que los campos de una base de datos.

Es posible crear el esquema de estos índices en Azure Portal, o mediante programación [con el SDK de .NET](search-howto-dotnet-sdk.md) o la [API de REST](https://msdn.microsoft.com/library/azure/dn798941.aspx). Una vez que se ha definido el índice, se pueden cargar los datos en el servicio Búsqueda de Azure, donde se indexan seguidamente.

### <a name="3-index-data"></a>3. Datos de índice
Una vez que haya definido los campos y los atributos del índice, estará listo para cargar el contenido en el índice. Se pueden emplear modelos de inserción o de extracción para la carga de los datos en el índice.

El modelo de extracción se proporciona a través de indexadores que se pueden configurar para actualizaciones bajo demanda o programadas (consulte [Operaciones de indexador (API de REST del servicio de Búsqueda de Azure)](https://msdn.microsoft.com/library/azure/dn946891.aspx)), lo que permite introducir fácilmente datos y cambios de datos de Azure DocumentDB, Base de datos SQL de Azure, Almacenamiento de blobs de Azure o SQL Server hospedado en una máquina virtual de Azure.

El modelo de inserción se proporciona a través del SDK o las API de REST usadas para enviar documentos actualizados a un índice. Se pueden insertar datos desde prácticamente cualquier conjunto de datos usando el formato JSON. Consulte [Agregar, actualizar o eliminar documentos](https://msdn.microsoft.com/library/azure/dn798930.aspx) o [Cómo usar Azure Search desde una aplicación .NET](search-howto-dotnet-sdk.md) para obtener instrucciones sobre la carga de datos.

### <a name="4-search"></a>4. Search
Una vez que se ha completado el índice de Azure Search, podrá [emitir consultas de búsqueda](https://msdn.microsoft.com/library/azure/dn798927.aspx) al punto de conexión de servicio mediante solicitudes HTTP sencillas con la API de REST o el SDK de .NET.

## <a name="try-it-now-for-free"></a>Pruébelo ahora (¡gratis!)
Los suscriptores de Azure pueden [aprovisionar un servicio en el nivel Gratis](search-create-service-portal.md).

Si no es suscriptor, puede [abrir una cuenta de Azure de manera gratuita](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A261C142F): obtiene crédito que puede usar para probar los servicios de Azure de pago, e incluso una vez agotado este, podrá mantener la cuenta y usar servicios gratuitos de Azure, como Websites. Nunca se la hará ningún cargo en la tarjeta de crédito, a menos que cambie explícitamente la configuración y pida que se le realice algún cargo.

Además, puede [activar las ventajas de suscriptor de MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F): su suscripción a MSDN le proporciona crédito todos los meses que puede usar con servicios de Azure de pago. 





<!--HONumber=Jan17_HO2-->


