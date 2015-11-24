<properties
	pageTitle="Creación de un índice de Búsqueda de Azure con una API de REST | Microsoft Azure | Servicio de búsqueda hospedado en la nube"
	description="Creación de un índice en código con Búsqueda de Azure y una API de REST de HTTP"
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

# Creación de un índice de Búsqueda de Azure con una API de REST
> [AZURE.SELECTOR]
- [Overview](search-what-is-an-index.md)
- [Portal](search-create-index-portal.md)
- [.NET](search-create-index-dotnet.md)
- [REST](search-create-index-rest-api.md)

Este artículo muestra cómo crear un índice con la [API de REST de Búsqueda de Azure](https://msdn.microsoft.com/library/azure/dn798935.aspx). Parte del contenido a continuación procede de [Creación de indices (API de REST de Búsqueda de Azure)](https://msdn.microsoft.com/library/azure/dn798941.aspx). Consulte el artículo primario para ver más contexto.

Los requisitos previos para crear un índice incluyen tener establecida una conexión con el servicio de búsqueda, normalmente mediante un 'httpClient'.

## Definición del esquema de índice

Para crear un índice mediante la API de REST, emitirá una solicitud POST al punto de conexión de la dirección URL de Búsqueda de Azure. Definirá la estructura del índice con JSON en el cuerpo de solicitud.

**Solicitudes y encabezados de solicitud**:

En el siguiente ejemplo, tendrá que usar el punto de conexión de la dirección URL del servicio, específicamente el nombre del servicio y la versión correcta de la API (la versión actual de la API es "2015-02-28" en el momento de la publicación de este documento). En los encabezados de solicitud, también tendrá que proporcionar la clave del administrador principal del servicio que recibe al [crear un servicio](https://msdn.microsoft.com/library/azure/dn798941.aspx/).

	POST https://[servicename].search.windows.net/indexes?api-version=2015-02-28
	Content-Type: application/JSON
	api-key:[primary admin key]


**Cuerpo de la solicitud**:

Aquí se identifica el nombre del índice (en este caso, "hotels"), así como los [nombres, tipos y atributos de los campos](https://msdn.microsoft.com/library/azure/dn798941.aspx).

	{
		"name": "hotels",  
		"fields": [
			{"name": "hotelId", "type": "Edm.String", "key": true, "searchable": false},
			{"name": "baseRate", "type": "Edm.Double"},
			{"name": "description", "type": "Edm.String", "filterable": false, "sortable": false, "facetable": false},
			{"name": "description_fr", "type": "Edm.String", "filterable": false, "sortable": false, "facetable": false, analyzer:"fr.lucene"},
			{"name": "hotelName", "type": "Edm.String"},
			{"name": "category", "type": "Edm.String"},
			{"name": "tags", "type": "Collection(Edm.String)"},
			{"name": "parkingIncluded", "type": "Edm.Boolean"},
			{"name": "smokingAllowed", "type": "Edm.Boolean"},
			{"name": "lastRenovationDate", "type": "Edm.DateTimeOffset"},
			{"name": "rating", "type": "Edm.Int32"},
			{"name": "location", "type": "Edm.GeographyPoint"}
		]
	}

Para una solicitud correcta, debería ver el código de estado "201 Created". Para obtener más información sobre la creación de un índice mediante la API de REST, visite [esta página](https://msdn.microsoft.com/library/azure/dn798941.aspx).

<!---HONumber=Nov15_HO4-->