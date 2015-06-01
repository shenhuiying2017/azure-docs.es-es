<properties 
	pageTitle="Novedades en la actualización más reciente de Búsqueda de Azure | Microsoft Azure" 
	description="Notas de la versión de Búsqueda de Azure que describen las actualizaciones más recientes del servicio" 
	services="search" 
	documentationCenter="" 
	authors="HeidiSteen" 
	manager="mblythe" 
	editor=""/>

<tags 
	ms.service="search" 
	ms.devlang="rest-api" 
	ms.workload="search" 
	ms.topic="article" 
	ms.tgt_pltfrm="na" 
	ms.date="04/09/2015" 
	ms.author="heidist"/>

#Novedades en la actualización más reciente de Búsqueda de Azure#

Búsqueda de Azure ya está disponible con carácter general y ofrece un acuerdo de nivel de servicio (SLA) con el 99,9% de disponibilidad para las configuraciones admitidas de la [versión 2015-02-28 de la API](https://msdn.microsoft.com/library/azure/dn798935.aspx).

##Cómo se actualizan e implementan las características

Las características se publican por separado o conjuntamente mediante la [API de REST](https://msdn.microsoft.com/library/azure/dn798935.aspx), el [SDK para .NET](http://go.microsoft.com/fwlink/?LinkId=528216) o en el panel de servicio del [portal de Azure](https://portal.azure.com).

La biblioteca .NET y la API de REST tienen varias versiones. Las API más antiguas siguen estando operativas cuando se implementan características nuevas. Puede visitar [Versiones del servicio de búsqueda](https://msdn.microsoft.com/library/azure/dn864560.aspx) para obtener más información acerca de nuestra directiva de control de versiones.


##SDK para .NET 0.10.0-preview

Se trata de la segunda iteración de la biblioteca de cliente .NET, Microsoft.Azure.Search.dll. Esta versión agrega compatibilidad para crear, administrar y utilizar indizadores por medio de clases. NET. Además, para los indizadores de SQL Azure, hay nueva compatibilidad para la indización de puntos geográficos.

- [Clase de indizadores](https://msdn.microsoft.com/library/azure/microsoft.azure.search.models.indexer.aspx)
- [Clase de origen de datos](https://msdn.microsoft.com/library/azure/microsoft.azure.search.models.datasource.aspx)

##SDK para .NET 0.9.6-preview
**Publicado: 5 de marzo de 2015**

Se trata de la primera versión pública del SDK para .NET de Búsqueda de Azure. Incluye una biblioteca de cliente, Microsoft.Azure.Search.dll, con dos espacios de nombres:

- [Microsoft.Azure.Search](https://msdn.microsoft.com/library/azure/microsoft.azure.search.aspx)
- [Microsoft.Azure.Search.Models](https://msdn.microsoft.com/library/azure/microsoft.azure.search.models.aspx)

Excluye:

- [Indizadores](http://go.microsoft.com/fwlink/p/?LinkId=528173) (esta característica ya no se excluye en la versión 0.10.0-preview)
- [API de REST de administración](https://msdn.microsoft.com/library/azure/dn832684.aspx)
- Características de [2015-02-28-Preview](search-api-2015-02-28-Preview.md) (actualmente, las características de solo vista previa consisten en procesadores de lenguaje natural de Microsoft y `moreLikeThis`).

Visite [Cómo usar Búsqueda de Azure en .NET](http://go.microsoft.com/fwlink/p/?LinkId=528088) para obtener instrucciones sobre la instalación y el uso del SDK.

##Versión de API 2015-02-28-Preview
**Publicado: 22 de abril de 2015**

- Los indizadores ahora admiten construcciones de fieldMapping que proporcionan asignaciones de campo cuando los nombres de campo reales son diferentes entre la base de datos externa y el índice de Búsqueda de Azure. Consulte [Indizadores](search-api-indexers-2015-02-28-Preview.md) para la versión `2015-02-28-preview` de la documentación de indizadores.

- Además, en esta actualización de la vista previa, los indizadores admiten transformaciones de tipo de campo para que pueda asignar un campo de cadena de una tabla SQL a un campo de recopilación de cadenas en un índice de búsqueda, suponiendo que el campo de origen represente una matriz JSON.

**Publicado: 5 de marzo de 2015**

- Los [procesadores de lenguaje natural de Microsoft](search-api-2015-02-28-Preview.md) proporcionan compatibilidad para varios idiomas y lematización expansiva para todos los idiomas compatibles con Office y Bing.

- [moreLikeThis =](search-api-2015-02-28-Preview.md) es un parámetro de búsqueda, mutuamente exclusivo de `search=`, que desencadena una ruta de ejecución de consulta alternativa. En lugar de realizar una búsqueda de texto completo de `search=` basada en una entrada de término de búsqueda, `moreLikeThis=` busca documentos que son similares a un documento determinado mediante la comparación de sus campos de búsqueda.

##Versión de API 2015-02-28
**Publicado: 5 de marzo de 2015**

- Los [indizadores](http://go.microsoft.com/fwlink/p/?LinkID=528210) son una nueva característica que simplifica en gran medida la indización de orígenes de datos de la base de datos SQL de Azure, DocumentDB de Azure y SQL Server en máquinas virtuales de Azure.

- Los [proveedores de sugerencias](https://msdn.microsoft.com/library/azure/dn798936.aspx) reemplaza la compatibilidad de consulta con escritura anticipada, más limitada, de la implementación anterior (sólo coincide con los prefijos) agregando compatibilidad para la coincidencia con infijos. Esta implementación puede buscar coincidencias en cualquier parte de un término y también admite la coincidencia aproximada.

- El [aprovechamiento de etiquetas](http://go.microsoft.com/fwlink/p/?LinkId=528212) permite un nuevo escenario de perfiles de puntuación. En concreto, aprovecha los datos almacenados (por ejemplo, las preferencias de compra), por lo que puede mejorar los resultados de búsqueda para usuarios individuales en función de su información personalizada.

Visite [Búsqueda de Azure ya está disponible con carácter general](http://go.microsoft.com/fwlink/p/?LinkId=528211) para ver en el blog de Azure el anuncio de servicio que describe todas estas características.

##Versión de API 2014-10-20-Preview
**Publicado: noviembre de 2014, enero de 2015**

- Se agregaron [analizadores de lenguaje Lucene](search-api-2014-10-20-preview.md) para proporcionar compatibilidad con múltiples idiomas para los analizadores de lenguaje personalizados distribuidos con Lucene. 

- Se introdujo la compatibilidad con herramientas para la creación de índices, incluidos los perfiles de puntuación, en el [portal de administración de Azure](https://portal.azure.com).

##Versión de API 2014-07-31-Preview
**Publicado: 21 de agosto de 2014**

Esta versión era la versión preliminar pública de Búsqueda de Azure y ofrecía las siguientes características principales:

- API de REST para las operaciones de índice y de documento. La mayor parte de esta versión de API está intacta en 2015-02-28. La documentación de la versión `2014-07-31-Preview` puede encontrarse en [API de REST del Servicio Búsqueda de Azure versión 2014-07-31](search-api-2014-07-31-preview.md).

- Perfiles de puntuación para optimizar los resultados de la búsqueda. Un perfil de puntuación agrega criterios que se usan para calcular los resultados de búsqueda. La documentación de esta característica puede encontrarse en [API de REST de perfiles de puntuación del Servicio Búsqueda de Azure versión 2014-07-31](search-api-scoring-profiles-2014-07-31-preview.md).

- La compatibilidad con geoespaciales ha estado disponible desde el principio, y se proporcionaba a través del tipo de datos `Edm.GeographyPoint` que ha formado parte de Búsqueda de Azure desde sus inicios.

- Aprovisionamiento en la versión preliminar del [portal de administración de Azure](https://portal.azure.com). Búsqueda de Azure fue uno de los pocos servicios que solo estaba disponible en el nuevo portal.

##Versión de API de administración 2015-02-28
**Publicado: 5 de marzo de 2015**

La [API de REST de administración](https://msdn.microsoft.com/library/azure/dn832684.aspx) marca la primera versión de la API de administración que pertenece a la versión disponible con carácter general de Búsqueda de Azure. No hay ninguna diferencia entre la versión preliminar anterior y esta.

##Versión de API de administración 2014-07-31
**Publicado: octubre de 2014**

La versión preliminar de [API de REST de administración](search-management-api-2014-07-31-preview.md) se agregó para permitir la administración de servicios mediante programación. Se actualiza de forma independiente de la API de REST del servicio.



<!--HONumber=54-->