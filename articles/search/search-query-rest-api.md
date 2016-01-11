<properties
	pageTitle="Creación de consultas en Búsqueda de Azure con llamadas de REST | Microsoft Azure | Servicio de búsqueda hospedado en la nube"
	description="Cree una consulta de búsqueda en Búsqueda de Azure y use los parámetros de búsqueda para filtrar, ordenar y crear facetas en el resultado de búsqueda mediante la biblioteca de .NET o el SDK."
	services="search"
	documentationCenter=""
	authors="HeidiSteen"
	manager="mblythe"
	editor=""
    tags="azure-portal"/>

<tags
	ms.service="search"
	ms.devlang="rest-api"
	ms.workload="search"
	ms.topic="get-started-article"
	ms.tgt_pltfrm="na"
	ms.date="11/17/2015"
	ms.author="heidist"/>

# Creación de consultas en Búsqueda de Azure mediante llamadas REST
> [AZURE.SELECTOR]
- [Overview](search-query-overview.md)
- [Fiddler](search-fiddler.md)
- [.NET](search-query-dotnet.md)
- [REST](search-query-rest-api.md)

En este artículo se muestra cómo crear una consulta en un índice con la [API de REST de Búsqueda de Azure](https://msdn.microsoft.com/library/azure/dn798935.aspx). Parte del contenido siguiente procede de [Búsqueda de documentos (API de REST de Búsqueda de Azure)](https://msdn.microsoft.com/library/azure/dn798927.aspx). Consulte el artículo primario para ver más contexto.

Los requisitos previos para la importación incluyen tener un índice existente preparado, cargado con documentos que proporcionen los datos que se pueden buscar.

Para buscar el índice mediante la API de REST, emitirá una solicitud HTTP GET. Los parámetros de consulta se definirán dentro de la dirección URL de la solicitud HTTP.

**Solicitudes y encabezados de solicitud**:

En la dirección URL tendrá que proporcionar el nombre del servicio, el nombre del índice y la versión correcta de la API. En la cadena de consulta al final de la dirección URL debe proporcionar los parámetros de consulta. Uno de los parámetros en la cadena de consulta debe ser la versión correcta de la API (la versión actual de la API es "2015-02-28" en el momento de la publicación de este documento).

Como encabezados de solicitud, tendrá que definir Content-Type y proporcionar la clave de administración principal o secundaria del servicio.

	GET https://[service name].search.windows.net/indexes/[index name]/docs?[query string]&api-version=2015-02-28
	Content-Type: application/JSON
	api-key:[primary admin key or secondary admin key]

Búsqueda de Azure ofrece muchas opciones para crear consultas extremadamente eficaces. Para obtener más información acerca de todos los distintos parámetros de una cadena de consulta, visite [esta página](https://msdn.microsoft.com/library/azure/dn798927.aspx).

**Ejemplos:**

A continuación se muestran algunos ejemplos con varias cadenas de consulta. Estos ejemplos usan un índice ficticio denominado "hotels":

Busque el término "quality" en todo el índice:

	GET https://[service name].search.windows.net/indexes/hotels/docs?search=quality&$orderby=lastRenovationDate desc&api-version=2015-02-28
	Content-Type: application/JSON
	api-key:[primary admin key or secondary admin key]

Busque en todo el índice:

	GET https://[service name].search.windows.net/indexes/hotels/docs?search=*&api-version=2015-02-28
	Content-Type: application/JSON
	api-key:[primary admin key or secondary admin key]

Busque en todo el índice y ordene por un campo específico (lastRenovationDate):

	GET https://[service name].search.windows.net/indexes/hotels/docs?search=*&$orderby=lastRenovationDate desc&api-version=2015-02-28
	Content-Type: application/JSON
	api-key:[primary admin key or secondary admin key]

Una solicitud de consulta correcta dará como resultado un código de estado de "200 OK" y los resultados de la búsqueda se encuentran en formato JSON en el cuerpo de la respuesta. Para obtener más información, visite la sección "Respuesta" de [esta página](https://msdn.microsoft.com/library/azure/dn798927.aspx).

<!---HONumber=AcomDC_1223_2015-->