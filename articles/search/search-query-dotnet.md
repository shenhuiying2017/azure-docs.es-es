<properties
	pageTitle="Creación de consultas en Búsqueda de Azure en .NET | Microsoft Azure | Servicio de búsqueda hospedado en la nube"
	description="Cree una consulta de búsqueda en Búsqueda de Azure y use los parámetros de búsqueda para filtrar, ordenar y crear facetas en los resultados de búsqueda mediante la biblioteca de .NET o el SDK."
	services="search"
	documentationCenter=""
	authors="HeidiSteen"
	manager="mblythe"
	editor=""
    tags="azure-portal"/>

<tags
	ms.service="search"
	ms.devlang="dotnet"
	ms.workload="search"
	ms.topic="get-started-article"
	ms.tgt_pltfrm="na"
	ms.date="11/10/2015"
	ms.author="heidist"/>

#Creación de consultas en Búsqueda de Azure en .NET
> [AZURE.SELECTOR]
- [Overview](search-query-overview.md)
- [Fiddler](search-fiddler.md)
- [.NET](search-query-dotnet.md)
- [REST](search-query-rest-api.md)

En este artículo se muestra cómo crear una consulta con el [SDK de .NET de Búsqueda de Azure](https://msdn.microsoft.com/library/azure/dn951165.aspx). El contenido siguiente es un subconjunto de [Cómo usar Búsqueda de Azure desde una aplicación .NET](search-howto-dotnet-sdk.md). Consulte en el artículo primario los pasos al completo.

Los requisitos previos para crear una consulta incluyen tener establecida una conexión con el servicio de búsqueda, normalmente mediante un `SearchServiceClient`.

El fragmento de código siguiente crea un método que pasa una entrada de cadena de búsqueda a un método SearchDocuments.

	private static void SearchDocuments(SearchIndexClient indexClient, string searchText, string filter = null)
	{
		// Execute search based on search text and optional filter
		var sp = new SearchParameters();
	
		if (!String.IsNullOrEmpty(filter))
		{
			sp.Filter = filter;
		}
	
		DocumentSearchResponse<Hotel> response = indexClient.Documents.Search<Hotel>(searchText, sp);
		foreach (SearchResult<Hotel> result in response)
		{
			Console.WriteLine(result.Document);
		}
	}
	
En primer lugar, este método crea el objeto SearchParameters. Esto se utiliza para especificar opciones adicionales para la consulta, como el orden, los filtros, la paginación y las facetas. En este ejemplo, solo vamos a establecer la propiedad Filter.

El siguiente paso consiste en ejecutar la consulta de búsqueda. Esto se realiza mediante el método Documents.Search. En este caso, pasamos como una cadena el texto de búsqueda que se usará, además de los parámetros de búsqueda creados anteriormente. También especificamos Hotel como el parámetro de tipo para Documents.Search, lo que indica al SDK que deserialice los documentos de los resultados de búsqueda en objetos de tipo Hotel.

Por último, este método recorre en iteración todas las coincidencias de los resultados de búsqueda e imprime cada documento en la consola.

Veamos más de cerca cómo se llama a este método:

	SearchDocuments(indexClient, searchText: "fancy wifi");
	SearchDocuments(indexClient, searchText: "*", filter: "category eq 'Luxury'");

En la primera llamada estamos buscando todos los documentos que contienen los términos de consulta "fancy" o "wifi". En la segunda llamada, el texto de búsqueda se define como "*", lo que significa "buscar todos los elementos". Puede encontrar más información acerca de la sintaxis de expresiones de consulta de búsqueda en [Sintaxis de consulta simple en Búsqueda de Azure](https://msdn.microsoft.com/library/azure/dn798920.aspx).

La segunda llamada usa una expresión OData $filter, categoría igual a 'Lujo'. Esto restringe la búsqueda de forma que devuelva solo aquellos documentos en los que el campo de categoría coincida exactamente con la cadena "Lujo". Puede encontrar ejemplos adicionales sobre la sintaxis de OData en [Sintaxis de expresiones de OData para Búsqueda de Azure](https://msdn.microsoft.com/library/azure/dn798921.aspx).

<!---HONumber=AcomDC_1223_2015-->