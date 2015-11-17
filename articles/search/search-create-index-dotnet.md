<properties
	pageTitle="Creación de un índice de Búsqueda de Azure con .NET | Microsoft Azure | Servicio de búsqueda hospedado en la nube"
	description="Creación de un índice en código mediante al biblioteca o el SDK de .NET de Búsqueda de Azure"
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
	ms.date="11/09/2015"
	ms.author="heidist"/>

# Creación de un índice de Búsqueda de Azure con .NET
> [AZURE.SELECTOR]
- [Overview](search-what-is-an-index.md)
- [Portal](search-create-index-portal.md)
- [.NET](search-create-index-dotnet.md)
- [REST](search-create-index-rest-api.md)

Este artículo muestra cómo crear un índice con el [SDK de .NET de Búsqueda de Azure](https://msdn.microsoft.com/library/azure/dn951165.aspx). El contenido siguiente es un subconjunto de [Cómo usar Búsqueda de Azure desde una aplicación .NET](search-howto-dotnet-sdk.md). Consulte en el artículo primario los pasos al completo.

Los requisitos previos para crear un índice incluyen tener establecida una conexión con el servicio de búsqueda, normalmente mediante un `SearchServiceClient`. Si ya existe, un procedimiento recomendado para garantizar una implementación sin dificultades es eliminar un índice existente con el mismo nombre.

Si suponemos un índice llamado 'hoteles', puede crear un método para hacerlo como sigue:

    private static void DeleteHotelsIndexIfExists(SearchServiceClient serviceClient)
    {
        if (serviceClient.Indexes.Exists("hotels"))
        {
            serviceClient.Indexes.Delete("hotels");
        }
    }

Este método utiliza el `SearchServiceClient` proporcionado para comprobar si existe el índice y, en ese caso, eliminarlo.

Para crear un nuevo índice "hoteles", construya un método similar al siguiente:

    private static void CreateHotelsIndex(SearchServiceClient serviceClient)
    {
        var definition = new Index()
        {
            Name = "hotels",
            Fields = new[]
            {
                new Field("hotelId", DataType.String)                       { IsKey = true },
                new Field("hotelName", DataType.String)                     { IsSearchable = true, IsFilterable = true },
                new Field("baseRate", DataType.Double)                      { IsFilterable = true, IsSortable = true },
                new Field("category", DataType.String)                      { IsSearchable = true, IsFilterable = true, IsSortable = true, IsFacetable = true },
                new Field("tags", DataType.Collection(DataType.String))     { IsSearchable = true, IsFilterable = true, IsFacetable = true },
                new Field("parkingIncluded", DataType.Boolean)              { IsFilterable = true, IsFacetable = true },
                new Field("lastRenovationDate", DataType.DateTimeOffset)    { IsFilterable = true, IsSortable = true, IsFacetable = true },
                new Field("rating", DataType.Int32)                         { IsFilterable = true, IsSortable = true, IsFacetable = true },
                new Field("location", DataType.GeographyPoint)              { IsFilterable = true, IsSortable = true }
            }
        };

        serviceClient.Indexes.Create(definition);
    }

Este método crea un nuevo objeto `Index` con una lista de objetos `Field` que define el esquema del nuevo índice. Cada campo tiene un nombre, un tipo de datos y varios atributos que definen su comportamiento de búsqueda. Además de campos, puede agregar al índice perfiles de puntuación, proveedores de sugerencias u opciones de CORS (se omiten en el ejemplo para mayor brevedad). Puede encontrar más información sobre el objeto Index y sus partes constituyentes en la referencia del SDK en [MSDN](https://msdn.microsoft.com/library/azure/microsoft.azure.search.models.index_members.aspx), así como en la [referencia de la API de REST de Búsqueda de Azure](https://msdn.microsoft.com/library/azure/dn798935.aspx).

<!---HONumber=Nov15_HO3-->