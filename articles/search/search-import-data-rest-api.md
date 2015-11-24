<properties
	pageTitle="Importación de datos en Búsqueda de Azure con la API de REST | Microsoft Azure | Servicio de búsqueda hospedado en la nube"
	description="Carga de datos en un índice de Búsqueda de Azure con la API de REST."
	services="search"
	documentationCenter=""
	authors="HeidiSteen"
	manager="mblythe"
	editor=""
    tags=""/>

<tags
	ms.service="search"
	ms.devlang="rest-api"
	ms.workload="search"
	ms.topic="get-started-article"
	ms.tgt_pltfrm="na"
	ms.date="11/17/2015"
	ms.author="heidist"/>

# Importación de datos en Búsqueda de Azure con la API de REST
> [AZURE.SELECTOR]
- [Overview](search-what-is-data-import.md)
- [Portal](search-import-data-portal.md)
- [.NET](search-import-data-dotnet.md)
- [REST](search-import-data-rest-api.md)
- [Indexers](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers-2015-02-28.md)

En este artículo se muestra cómo rellenar un índice con la [API de REST de Búsqueda de Azure](https://msdn.microsoft.com/library/azure/dn798935.aspx). Parte del contenido siguiente procede de [Agregar, actualizar o eliminar documentos (API de REST de Búsqueda de Azure)](https://msdn.microsoft.com/library/azure/dn798930.aspx). Consulte el artículo primario para ver más contexto.

Para poder insertar documentos en el índice mediante la API de REST, se van a emitir las solicitudes HTTP POST al punto de conexión de la dirección URL de su servicio.

**Solicitudes y encabezados de solicitud**:

En la dirección URL, tendrá que proporcionar el nombre del servicio, así como la versión correcta de la API (la versión actual de la API es "2015-02-28" en el momento de la publicación de este documento).

En los encabezados de solicitud, tendrá que definir Content-Type y proporcionar la clave de administración principal del servicio.

	POST https://[servicename].search.windows.net/indexes/[indexname]/docs/index?api-version=2015-02-28
	Content-Type: application/JSON
	api-key:[primary admin key]


**Cuerpo de la solicitud**:


	{
		"value": [
			{
				"@search.action": "upload",
				"hotelId": "1",
				"baseRate": 199.0,
				"description": "Best hotel in town",
				"description_fr": "Meilleur hôtel en ville",
				"hotelName": "Fancy Stay",
				"category": "Luxury",
				"tags": ["pool", "view", "wifi", "concierge"],
				"parkingIncluded": false,
				"smokingAllowed": false,
				"lastRenovationDate": "2010-06-27T00:00:00Z",
				"rating": 5,
				"location": { "type": "Point", "coordinates": [-122.131577, 47.678581] }
			},
			{
				"@search.action": "upload",
				"hotelId": "2",
				"baseRate": 79.99,
				"description": "Cheapest hotel in town",
				"description_fr": "Hôtel le moins cher en ville",
				"hotelName": "Roach Motel",
				"category": "Budget",
				"tags": ["motel", "budget"],
				"parkingIncluded": true,
				"smokingAllowed": true,
				"lastRenovationDate": "1982-04-28T00:00:00Z",
				"rating": 1,
				"location": { "type": "Point", "coordinates": [-122.131577, 49.678581] }
			}
		]
	}

En este caso, estamos usando "upload" como la acción de búsqueda. Al actualizar y eliminar documentos existentes puede usar "merge", "mergeOrUpload" y "delete".

Al actualizar el índice, recibirá un código de estado de "200 OK" si s correcto. Si al menos un elemento en la solicitud no se indexan correctamente, recibirá un código de estado "207".

Para obtener más información sobre las acciones de documentos y las respuestas de éxito o error, consulte [esta página](https://msdn.microsoft.com/library/azure/dn798930.aspx).

<!---HONumber=Nov15_HO4-->