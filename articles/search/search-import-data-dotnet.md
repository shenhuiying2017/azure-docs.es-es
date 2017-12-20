---
title: Carga de datos (-.NET - Azure Search) | Microsoft Docs
description: "Aprenda cómo cargar de datos en un índice de Azure Search mediante el SDK para .NET."
services: search
documentationcenter: 
author: brjohnstmsft
manager: jhubbard
editor: 
tags: 
ms.assetid: 0e0e7e7b-7178-4c26-95c6-2fd1e8015aca
ms.service: search
ms.devlang: dotnet
ms.workload: search
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.date: 01/13/2017
ms.author: brjohnst
ms.openlocfilehash: bdd952869143c6ca6374bb9264db5bcba1f32b50
ms.sourcegitcommit: 68aec76e471d677fd9a6333dc60ed098d1072cfc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/18/2017
---
# <a name="upload-data-to-azure-search-using-the-net-sdk"></a>Carga de datos en Azure Search mediante el SDK para .NET
> [!div class="op_single_selector"]
> * [Información general](search-what-is-data-import.md)
> * [.NET](search-import-data-dotnet.md)
> * [REST](search-import-data-rest-api.md)
> 
> 

En este artículo se mostrará cómo usar el [SDK de .NET para Azure Search](https://aka.ms/search-sdk) para importar datos en un índice de Azure Search.

Antes de comenzar este tutorial, debe haber [creado ya un índice de Azure Search](search-what-is-an-index.md). En este artículo también se asume que ha creado un objeto `SearchServiceClient` , como se muestra en [Creación de un índice de Azure Search mediante el SDK para .NET](search-create-index-dotnet.md#CreateSearchServiceClient).

> [!NOTE]
> Todo el código de ejemplo de este artículo está escrito en C#. El código fuente completo se puede encontrar [en GitHub](http://aka.ms/search-dotnet-howto). Consulte el [SDK de Azure Search para .NET](search-howto-dotnet-sdk.md) para ver un tutorial más detallado sobre el código de ejemplo.

Para insertar documentos en un índice mediante el SDK. para NET, necesitará:

1. Crear un objeto `SearchIndexClient` para conectarse al índice de búsqueda.
2. Crear un `IndexBatch` que contenga los documentos que se van a agregar, modificar o eliminar.
3. Llamar al método `Documents.Index` del `SearchIndexClient` para enviar el `IndexBatch` al índice de búsqueda.

## <a name="create-an-instance-of-the-searchindexclient-class"></a>Creación de una instancia de la clase SearchIndexClient
Para importar datos en el SDK de .NET para Azure Search, será preciso crear una instancia de la clase `SearchIndexClient` . Dicha instancia se puede construir, pero es más fácil si ya tiene una instancia de `SearchServiceClient` para llamar a su método `Indexes.GetClient`. Por ejemplo, aquí se muestra cómo obtener un `SearchIndexClient` para el índice denominado "hoteles" de un `SearchServiceClient` denominado `serviceClient`:

```csharp
ISearchIndexClient indexClient = serviceClient.Indexes.GetClient("hotels");
```

> [!NOTE]
> En una aplicación de búsqueda típica, el llenado y la administración de índices se controlan mediante un componente independiente de las consultas de búsqueda. `Indexes.GetClient` resulta cómodo para rellenar un índice porque evita la molestia de dar otro `SearchCredentials`. Con este fin, pasa la clave de administrador que se usó para crear el `SearchServiceClient` al nuevo `SearchIndexClient`. Sin embargo, en la parte de la aplicación que ejecuta consultas, es mejor crear directamente el `SearchIndexClient` para poder pasar una clave de consulta en lugar de una clave de administración. Esto está en consonancia con el [principio de privilegios mínimos](https://en.wikipedia.org/wiki/Principle_of_least_privilege) y le ayudará a proteger su aplicación. Puede encontrar más información sobre las claves de administración y de consulta en la [referencia de la API de REST de Azure Search](https://docs.microsoft.com/rest/api/searchservice/).
> 
> 

`SearchIndexClient` tiene una propiedad `Documents`. Esta propiedad proporciona todos los métodos necesarios para agregar, modificar, eliminar o consultar los documentos del índice.

## <a name="decide-which-indexing-action-to-use"></a>Elección de la acción de indexación que va a usar
Para importar datos mediante el SDK. de NET, es preciso empaquetar los datos en un objeto `IndexBatch`. Un `IndexBatch` encapsula una colección de objetos `IndexAction`, cada uno de los cuales contiene un documento y una propiedad que indica a Azure Search qué acción debe realizar en el documento (cargar, combinar, eliminar, etc.). Dependiendo de cuál de las acciones siguientes elija, se deberán incluir solo ciertos campos para cada documento:

| Acción | Description | Campos necesarios para cada documento | Notas |
| --- | --- | --- | --- |
| `Upload` |Una acción `Upload` es similar a un "upsert" donde se insertará el documento si es nuevo y se actualizará/reemplazará si ya existe. |la clave, además de cualquier otro campo que desee definir |Al actualizar o reemplazar un documento existente, cualquier campo que no esté especificado en la solicitud tendrá su campo establecido en `null`. Esto ocurre incluso cuando el campo se ha establecido previamente en un valor que no sea nulo. |
| `Merge` |Permite actualizar un documento existente con los campos especificados. Si el documento no existe en el índice, se producirá un error en la combinación. |la clave, además de cualquier otro campo que desee definir |Cualquier campo que se especifica en una combinación reemplazará al campo existente en el documento. Aquí se incluyen los campos de tipo `DataType.Collection(DataType.String)`. Por ejemplo, si el documento contiene un campo `tags` con el valor `["budget"]` y ejecuta una combinación con el valor `["economy", "pool"]` para `tags`, el valor final del campo `tags` será `["economy", "pool"]`. No será `["budget", "economy", "pool"]`. |
| `MergeOrUpload` |Esta acción se comporta como `Merge` si ya existe un documento con la clave especificada en el índice. Si el documento no existe, se comporta como `Upload` con un nuevo documento. |la clave, además de cualquier otro campo que desee definir |- |
| `Delete` |Quita el documento especificado del índice. |solo la clave |Todos los campos que especifique que no sean el campo de clave, se omitirán. Si desea quitar un campo individual de un documento, use `Merge` en su lugar y establezca el campo explícitamente con el valor NULL. |

Puede especificar la acción que desea utilizar con los distintos métodos estáticos de las clases `IndexBatch` y `IndexAction`, como se muestra en la siguiente sección.

## <a name="construct-your-indexbatch"></a>Construcción de IndexBatch
Ahora que conoce las acciones que se van a realizar en los documentos, está listo para construir `IndexBatch`. El ejemplo siguiente muestra cómo crear un lote con otras acciones. Tenga en cuenta que en nuestro ejemplo se usa una clase personalizada denominada `Hotel` que se asigna a un documento del índice "hoteles".

```csharp
var actions =
    new IndexAction<Hotel>[]
    {
        IndexAction.Upload(
            new Hotel()
            {
                HotelId = "1",
                BaseRate = 199.0,
                Description = "Best hotel in town",
                DescriptionFr = "Meilleur hôtel en ville",
                HotelName = "Fancy Stay",
                Category = "Luxury",
                Tags = new[] { "pool", "view", "wifi", "concierge" },
                ParkingIncluded = false,
                SmokingAllowed = false,
                LastRenovationDate = new DateTimeOffset(2010, 6, 27, 0, 0, 0, TimeSpan.Zero),
                Rating = 5,
                Location = GeographyPoint.Create(47.678581, -122.131577)
            }),
        IndexAction.Upload(
            new Hotel()
            {
                HotelId = "2",
                BaseRate = 79.99,
                Description = "Cheapest hotel in town",
                DescriptionFr = "Hôtel le moins cher en ville",
                HotelName = "Roach Motel",
                Category = "Budget",
                Tags = new[] { "motel", "budget" },
                ParkingIncluded = true,
                SmokingAllowed = true,
                LastRenovationDate = new DateTimeOffset(1982, 4, 28, 0, 0, 0, TimeSpan.Zero),
                Rating = 1,
                Location = GeographyPoint.Create(49.678581, -122.131577)
            }),
        IndexAction.MergeOrUpload(
            new Hotel()
            {
                HotelId = "3",
                BaseRate = 129.99,
                Description = "Close to town hall and the river"
            }),
        IndexAction.Delete(new Hotel() { HotelId = "6" })
    };

var batch = IndexBatch.New(actions);
```

En este caso, se van a usar `Upload`, `MergeOrUpload` y `Delete` como acciones de búsqueda, tal como especifican los métodos a los que se llama en la clase `IndexAction`.

Se supone que este índice "hoteles" de ejemplo ya está relleno con varios documentos. Observe que no tuvimos que especificar todos los campos posibles del documento al utilizar `MergeOrUpload` y que solo especificamos la clave del documento (`HotelId`) al usar `Delete`.

Además, tenga en cuenta que en las solicitudes de indexación individuales solo se puede incluir un máximo de 1000 documentos.

> [!NOTE]
> En este ejemplo, aplicamos acciones diferentes a documentos diferentes. Si deseara realizar las mismas acciones en todos los documentos del lote, en lugar de llamar a `IndexBatch.New`, podría utilizar los restantes métodos estáticos de `IndexBatch`. Por ejemplo, podría crear lotes mediante una llamada a `IndexBatch.Merge`, `IndexBatch.MergeOrUpload` o `IndexBatch.Delete`. Estos métodos toman una colección de documentos (objetos del tipo `Hotel` en este ejemplo), en lugar de objetos `IndexAction`.
> 
> 

## <a name="import-data-to-the-index"></a>Importación de datos en el índice
Ahora que tiene un objeto `IndexBatch` inicializado, puede enviarlo al índice, para lo que debe realizar una llamada a `Documents.Index` en el objeto `SearchIndexClient`. En el ejemplo siguiente se muestra cómo llamar a `Index`, así como algunos pasos adicionales que es preciso llevar a cabo:

```csharp
try
{
    indexClient.Documents.Index(batch);
}
catch (IndexBatchException e)
{
    // Sometimes when your Search service is under load, indexing will fail for some of the documents in
    // the batch. Depending on your application, you can take compensating actions like delaying and
    // retrying. For this simple demo, we just log the failed document keys and continue.
    Console.WriteLine(
        "Failed to index some of the documents: {0}",
        String.Join(", ", e.IndexingResults.Where(r => !r.Succeeded).Select(r => r.Key)));
}

Console.WriteLine("Waiting for documents to be indexed...\n");
Thread.Sleep(2000);
```

Tenga en cuenta el `try`/`catch` que rodea la llamada al método `Index`. El bloque catch controla un caso de error importante de indexación. Si su servicio Azure Search no logra indizar algunos de los documentos del lote, aparece una `IndexBatchException` producida por `Documents.Index`. Esto puede suceder si indiza documentos mientras el servicio está sobrecargado. **Recomendamos encarecidamente controlar este caso de forma explícita en el código.** Puede retrasar la indización de los documentos que dieron error y volver a intentarlo; puede crear un registro y continuar, como hace el ejemplo, o puede adoptar otro enfoque según los requisitos de coherencia de datos de la aplicación.

Por último, el código del ejemplo anterior se retrasa dos segundos. La indización ocurre de manera asincrónica en el servicio Azure Search, por lo que la aplicación de ejemplo debe esperar unos momentos para asegurarse de que los documentos estén disponibles para la búsqueda. Retrasos así solo suelen ser necesarios en las pruebas, demostraciones y aplicaciones de ejemplo.

<a name="HotelClass"></a>

### <a name="how-the-net-sdk-handles-documents"></a>Gestión de documentos del SDK de .NET
Quizás se pregunte cómo consigue el SDK de Azure para .NET cargar en el índice las instancias de una clase definida por el usuario como `Hotel` . Para ayudar a responder esa pregunta, examinemos la clase `Hotel` , que se asigna al esquema de índice definido en [Creación de un índice de Azure Search mediante el SDK para .NET](search-create-index-dotnet.md#DefineIndex):

```csharp
[SerializePropertyNamesAsCamelCase]
public partial class Hotel
{
    [Key]
    [IsFilterable]
    public string HotelId { get; set; }

    [IsFilterable, IsSortable, IsFacetable]
    public double? BaseRate { get; set; }

    [IsSearchable]
    public string Description { get; set; }

    [IsSearchable]
    [Analyzer(AnalyzerName.AsString.FrLucene)]
    [JsonProperty("description_fr")]
    public string DescriptionFr { get; set; }

    [IsSearchable, IsFilterable, IsSortable]
    public string HotelName { get; set; }

    [IsSearchable, IsFilterable, IsSortable, IsFacetable]
    public string Category { get; set; }

    [IsSearchable, IsFilterable, IsFacetable]
    public string[] Tags { get; set; }

    [IsFilterable, IsFacetable]
    public bool? ParkingIncluded { get; set; }

    [IsFilterable, IsFacetable]
    public bool? SmokingAllowed { get; set; }

    [IsFilterable, IsSortable, IsFacetable]
    public DateTimeOffset? LastRenovationDate { get; set; }

    [IsFilterable, IsSortable, IsFacetable]
    public int? Rating { get; set; }

    [IsFilterable, IsSortable]
    public GeographyPoint Location { get; set; }

    // ToString() method omitted for brevity...
}
```

Lo primero que debe tener en cuenta es que cada propiedad pública de `Hotel` corresponde a un campo de la definición del índice, pero con una diferencia fundamental: el nombre de cada campo comienza con una letra minúscula ("mayúsculas y minúsculas Camel"), mientras que el nombre de cada propiedad pública de `Hotel` comienza con una letra mayúscula ("mayúsculas y minúsculas Pascal"). Se trata de un escenario común en las aplicaciones .NET que realizan enlaces de datos cuando el esquema de destino está fuera del control del desarrollador de la aplicación. En lugar de tener que infringir las directrices de nomenclatura de .NET utilizando mayúsculas y minúsculas Camel para los nombres de las propiedades, puede usar el atributo `[SerializePropertyNamesAsCamelCase]` para indicar al SDK que asigne los nombres de las propiedades automáticamente a mayúsculas y minúsculas Camel.

> [!NOTE]
> El SDK de .NET para Azure Search usa la biblioteca [NewtonSoft JSON.NET](http://www.newtonsoft.com/json/help/html/Introduction.htm) para serializar y deserializar los objetos de modelo personalizados en JSON y de este. Puede personalizar esta serialización si es necesario. Puede encontrar más información en [Serialización personalizada con JSON.NET](search-howto-dotnet-sdk.md#JsonDotNet). Un ejemplo de ello es el uso del atributo `[JsonProperty]` en la propiedad `DescriptionFr` del código de ejemplo anterior.
> 
> 

La segunda cosa importante acerca de la clase `Hotel` son los tipos de datos de las propiedades públicas. Los tipos .NET de esas propiedades se asignan a los tipos de campo equivalentes de la definición del índice. Por ejemplo, la propiedad de cadena `Category` se asigna al campo `category`, que es de tipo `DataType.String`. Se dan asignaciones de tipos similares entre `bool?` y `DataType.Boolean`, `DateTimeOffset?` y `DataType.DateTimeOffset`, y así sucesivamente. Las reglas específicas para la asignación de tipos se documentan con el método `Documents.Get` en la [referencia del SDK de Azure Search para .NET](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.idocumentsoperations#Microsoft_Azure_Search_IDocumentsOperations_GetWithHttpMessagesAsync__1_System_String_System_Collections_Generic_IEnumerable_System_String__Microsoft_Azure_Search_Models_SearchRequestOptions_System_Collections_Generic_Dictionary_System_String_System_Collections_Generic_List_System_String___System_Threading_CancellationToken_).

La capacidad de usar sus propias clases como documentos funciona en ambas direcciones; también puede recuperar los resultados de la búsqueda y hacer que el SDK los deserialice automáticamente en el tipo que prefiera, como se muestra en el [siguiente artículo](search-query-dotnet.md).

> [!NOTE]
> El SDK de Azure Search para .NET también admite documentos de tipo dinámico mediante la clase `Document`, que es una asignación clave/valor de nombres de campo a valores de campo. Esto es útil en escenarios en los que no se conoce el esquema del índice en el momento del diseño o en los que resulte inconveniente enlazar a clases de modelo específicas. Todos los métodos del SDK que se ocupan de los documentos tienen sobrecargas que funcionan con la clase `Document` , así como sobrecargas de asignación rigurosa que aceptan un parámetro de tipo genérico. En el código de ejemplo de este artículo solo se utilizan las últimas.
> 
> 

**¿Por qué debería usar tipos de datos que aceptan valores null?**

Al diseñar sus propias clases de modelo para asignar a un índice de Azure Search, es recomendable declarar las propiedades de tipos de valor como `bool` y `int` que aceptan valores NULL (por ejemplo: `bool?` en lugar de `bool`). Si usa un tipo de modelo con una propiedad que no acepta valores NULL, tendrá que **garantizar** que ningún documento del índice contiene un valor NULL para el campo correspondiente. Ni el SDK ni el servicio Azure Search le permitirá aplicar esto.

Esto no es solo una inquietud hipotética: imagine un escenario donde se agrega un nuevo campo a un índice existente que es de tipo `DataType.Int32`. Después de actualizar la definición del índice, todos los documentos tendrán un valor null para ese campo nuevo (ya que todos los tipos aceptan valores NULL en Azure Search). Si después usa una clase de modelo con una propiedad `int` que no acepta valores NULL para ese campo, obtendrá `JsonSerializationException` así al intentar recuperar documentos:

    Error converting value {null} to type 'System.Int32'. Path 'IntValue'.

Por este motivo, recomendamos utilizar tipos que aceptan valores NULL en las clases de modelo como procedimiento recomendado.

## <a name="next-steps"></a>Pasos siguientes
Después de rellenar el índice de Azure Search, estará listo para iniciar la emisión de consultas para buscar documentos. Para más información, vea [Consultas en Azure Search](search-query-overview.md) .

