---
title: "Creación de un índice (API de .NET - Azure Search) | Microsoft Docs"
description: "Creación de un índice en código mediante SDK de .NET para Búsqueda de Azure"
services: search
documentationcenter: 
author: brjohnstmsft
manager: jhubbard
editor: 
tags: azure-portal
ms.assetid: 3a851647-fc7b-4fb6-8506-6aaa519e77cd
ms.service: search
ms.devlang: dotnet
ms.workload: search
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.date: 05/22/2017
ms.author: brjohnst
ms.openlocfilehash: fac41903c3e5731d17f832ff58145fe74dfa29f1
ms.sourcegitcommit: 02e69c4a9d17645633357fe3d46677c2ff22c85a
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 08/03/2017
---
# <a name="create-an-azure-search-index-using-the-net-sdk"></a>Creación de un índice de Búsqueda de Azure mediante el SDK para .NET
> [!div class="op_single_selector"]
> * [Información general](search-what-is-an-index.md)
> * [Portal](search-create-index-portal.md)
> * [.NET](search-create-index-dotnet.md)
> * [REST](search-create-index-rest-api.md)
> 
> 

Este artículo le guiará a través del proceso de creación de un [índice](https://docs.microsoft.com/rest/api/searchservice/Create-Index) de Azure Search usando el [SDK de .NET para Azure Search](https://aka.ms/search-sdk).

Antes de seguir con esta guía y crear un índice, debe haber [creado ya un servicio de Búsqueda de Azure](search-create-service-portal.md).

> [!NOTE]
> Todo el código de ejemplo de este artículo está escrito en C#. El código fuente completo se puede encontrar [en GitHub](http://aka.ms/search-dotnet-howto). Consulte el [SDK de Azure Search para .NET](search-howto-dotnet-sdk.md) para ver un tutorial más detallado sobre el código de ejemplo.


## <a name="identify-your-azure-search-services-admin-api-key"></a>Identificación de la clave de API de administración del servicio de Búsqueda de Azure
Ahora que ha aprovisionado un servicio de Búsqueda de Azure, está casi preparado para emitir solicitudes en el punto de conexión de servicio mediante el SDK para .NET. En primer lugar, tiene que obtener una de las claves de API de administrador que se generaron para aprovisionar el servicio de búsqueda. El SDK para .NET enviará esta clave de API en cada solicitud al servicio. Tener una clave válida genera la confianza, solicitud a solicitud, entre la aplicación que envía la solicitud y el servicio que se encarga de ella.

1. Para buscar las claves de API del servicio, inicie sesión en [Azure Portal](https://portal.azure.com/)
2. Vaya a la hoja de servicio de Búsqueda de Azure
3. Haga clic en el icono "Claves"

El servicio tendrá *claves de administración* y *claves de consulta*.

* Sus *claves de administración* principal y secundaria permiten conceder derechos completos para todas las operaciones, incluida la capacidad para administrar el servicio, crear y eliminar índices, indexadores y orígenes de datos. Existen dos claves, de forma que puede usar la clave secundaria si decide volver a generar la clave principal y viceversa.
* Las *claves de consulta* conceden acceso de solo lectura a índices y documentos y normalmente se distribuyen entre las aplicaciones cliente que emiten solicitudes de búsqueda.

Para crear un índice, puede usar su clave de administración principal o la secundaria.

<a name="CreateSearchServiceClient"></a>

## <a name="create-an-instance-of-the-searchserviceclient-class"></a>Creación de una instancia de la clase SearchServiceClient
Para empezar a usar el SDK de .NET para Búsqueda de Azure, tendrá que crear una instancia de la clase `SearchServiceClient` . Esta clase tiene varios constructores. El que desea tiene el nombre del servicio de búsqueda y un objeto `SearchCredentials` como parámetros. `SearchCredentials` incluye su clave de API.

El código siguiente crea una nueva instancia de `SearchServiceClient` con los valores de nombre de servicio de búsqueda y la clave de API que se almacenan en el archivo de configuración de la aplicación (`appsettings.json` en el caso de la [aplicación de ejemplo](http://aka.ms/search-dotnet-howto)):

```csharp
private static SearchServiceClient CreateSearchServiceClient(IConfigurationRoot configuration)
{
    string searchServiceName = configuration["SearchServiceName"];
    string adminApiKey = configuration["SearchServiceAdminApiKey"];

    SearchServiceClient serviceClient = new SearchServiceClient(searchServiceName, new SearchCredentials(adminApiKey));
    return serviceClient;
}
```

`SearchServiceClient` tiene una propiedad `Indexes`. Esta propiedad proporciona todos los métodos que necesita para crear, enumerar, actualizar o eliminar los índices de Búsqueda de Azure.

> [!NOTE]
> La clase `SearchServiceClient` administra las conexiones con el servicio de búsqueda. Para evitar la apertura de un número excesivo de conexiones, debe intentar, si es posible, compartir una única instancia de `SearchServiceClient` en la aplicación. Sus métodos son seguros para subprocesos lo que permite habilitar este tipo de uso compartido.
> 
> 

<a name="DefineIndex"></a>

## <a name="define-your-azure-search-index"></a>Definición del índice de Azure Search
Una única llamada al método `Indexes.Create` creará el índice. Este método toma como parámetro un objeto `Index` que define el índice de Búsqueda de Azure. Tiene que crear un objeto `Index` e inicializarlo de la forma siguiente:

1. Establezca la propiedad `Name` del objeto `Index` en el nombre del índice.
2. Establezca la propiedad `Fields` del objeto `Index` en una matriz de objetos `Field`. La manera más fácil de crear los objetos `Field` es mediante la llamada al método `FieldBuilder.BuildForType`, pasando una clase de modelo para el parámetro de tipo. Una clase de modelo tiene propiedades que se asignan a los campos del índice. Esto le permite enlazar documentos desde el índice de búsqueda con las instancias de la clase de modelo.

> [!NOTE]
> Si no tiene pensado usar una clase de modelo, todavía puede definir el índice mediante la creación de objetos `Field` directamente. Puede proporcionar el nombre del campo en el constructor, junto con el tipo de datos (o el analizador de campos de cadena). También puede establecer otras propiedades, como `IsSearchable`, `IsFilterable`, etc.
>
>

Es importante que tenga en cuenta sus necesidades de negocio y experiencia de usuario al diseñar el índice, ya que a cada campo se le tienen que asignar las [propiedades adecuadas](https://docs.microsoft.com/rest/api/searchservice/Create-Index). Estas propiedades controlan qué características de búsqueda (filtrado, facetas, ordenación, búsqueda de texto completo, etc.) se aplican a qué campos. Para cualquier propiedad que no establezca de forma explícita, el valor predeterminado de la clase `Field` es deshabilitar la característica de búsqueda correspondiente, excepto si la habilita específicamente.

En nuestro ejemplo, hemos llamado a nuestro índice "hoteles" y definido los campos mediante una clase de modelo: Cada propiedad de la clase de modelo tiene atributos que determinan los comportamientos relacionados con la búsqueda del campo de índice correspondiente. La clase del modelo se define de la manera siguiente:

```csharp
using System;
using Microsoft.Azure.Search;
using Microsoft.Azure.Search.Models;
using Microsoft.Spatial;
using Newtonsoft.Json;

// The SerializePropertyNamesAsCamelCase attribute is defined in the Azure Search .NET SDK.
// It ensures that Pascal-case property names in the model class are mapped to camel-case
// field names in the index.
[SerializePropertyNamesAsCamelCase]
public partial class Hotel
{
    [System.ComponentModel.DataAnnotations.Key]
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
}
```

Hemos elegido cuidadosamente los atributos de cada propiedad en función de cómo creemos que se usarán en una aplicación. Por ejemplo, es probable que las personas que buscan hoteles estén interesadas en coincidencias de palabras clave en el campo `description`, por eso hemos habilitado la búsqueda de texto completo para ese campo agregando el atributo `IsSearchable` a la propiedad `Description`.

Tenga en cuenta que exactamente un campo del índice de tipo `string` se debe designar como el campo *clave* agregando el atributo `Key` (consulte `HotelId` en el ejemplo anterior).

La definición del índice anterior usa un analizador de lenguaje para el campo `description_fr` porque está diseñado para almacenar texto en francés. Consulte [el tema de compatibilidad de idiomas](https://docs.microsoft.com/rest/api/searchservice/Language-support) así como la correspondiente [entrada de blog](https://azure.microsoft.com/blog/language-support-in-azure-search/) para más información sobre los analizadores de idiomas.

> [!NOTE]
> De forma predeterminada, el nombre de cada propiedad de la clase de modelo se usa como nombre del campo correspondiente en el índice. Si quiere asignar todos los nombres de propiedad a nombres de campo con mayúsculas y minúsculas Camel, marque la clase con el atributo `SerializePropertyNamesAsCamelCase`. Si quiere asignarlos a un nombre diferente, puede usar el atributo `JsonProperty` como la propiedad `DescriptionFr` anterior. El atributo `JsonProperty` tiene prioridad sobre el atributo `SerializePropertyNamesAsCamelCase`.
> 
> 

Ahora que hemos definido una clase de modelo, podemos crear fácilmente una definición de índice:

```csharp
var definition = new Index()
{
    Name = "hotels",
    Fields = FieldBuilder.BuildForType<Hotel>()
};
```

## <a name="create-the-index"></a>creación del índice
Ahora que tiene un objeto `Index` inicializado, puede crear el índice simplemente realizando una llamada a `Indexes.Create` en el objeto `SearchServiceClient`:

```csharp
serviceClient.Indexes.Create(definition);
```

Para una solicitud correcta, el método realizará la devolución normalmente. Si hay un problema con la solicitud, como un parámetro no válido, el método producirá una `CloudException`.

Cuando haya terminado con un índice y desee eliminarlo, simplemente llame al método `Indexes.Delete` en su `SearchServiceClient`. Por ejemplo, así es cómo se eliminaría el índice "hoteles":

```csharp
serviceClient.Indexes.Delete("hotels");
```

> [!NOTE]
> El código de ejemplo de este artículo usa los métodos sincrónicos del SDK de Búsqueda de Azure para .NET por motivos de simplicidad. En sus propias aplicaciones, recomendamos que use métodos asincrónicos para mantener su escalabilidad y capacidad de respuesta. Por ejemplo, en los ejemplos anteriores podría utilizar `CreateAsync` y `DeleteAsync` en lugar de `Create` y `Delete`.
> 
> 

## <a name="next-steps"></a>Pasos siguientes
Después de crear un índice de Búsqueda de Azure, ya podrá [cargar el contenido en el índice](search-what-is-data-import.md) y empezar la búsqueda de los datos.

