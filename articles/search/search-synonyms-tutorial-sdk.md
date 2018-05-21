---
title: Tutorial de sinónimos en Azure Search | Microsoft Docs
description: Incorporación de la característica de sinónimos a un índice de Azure Search.
manager: cgronlun
author: HeidiSteen
services: search
ms.service: search
ms.topic: tutorial
ms.date: 04/20/2018
ms.author: heidist
ms.openlocfilehash: 5482185a4a4cc8b76c1094ce12a7ac52985ec57c
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 04/28/2018
---
# <a name="synonym-c-tutorial-for-azure-search"></a>Tutorial de sinónimos de C# para Azure Search

Los sinónimos amplían una consulta realizando coincidencias con términos que se consideran equivalentes semánticamente al término de entrada. Por ejemplo, podría interesarle que "coche" obtenga coincidencias con documentos que contengan los términos "automóvil" o "vehículo".

En Azure Search, los sinónimos se definen en un *mapa de sinónimos* a través de *reglas de asignación* que permiten asociar términos equivalentes. Puede crear varios mapas de sinónimos, publicarlos como un recurso de todo el servicio disponible para todos los índices y, a continuación, hacer referencia a cuál desea usar en el nivel de campo. En el momento de la consulta, además de buscar un índice, Azure Search realiza una búsqueda en un mapa de sinónimos, si se especifica uno en los campos usados en la consulta.

> [!NOTE]
> La característica de sinónimos se admite en la versión más reciente de la API y del SDK (api-version=2017-11-11, versión 5.0.0). En este momento no es compatible con Azure Portal. Si la compatibilidad de Azure Portal con los sinónimos no le resulta útil, proporcione sus comentarios en [UserVoice](https://feedback.azure.com/forums/263029-azure-search)

## <a name="prerequisites"></a>requisitos previos

Los requisitos del tutorial incluyen los siguientes:

* [Visual Studio](https://www.visualstudio.com/downloads/)
* [Un servicio de Azure Search](search-create-service-portal.md)
* [Biblioteca de Microsoft.Azure.Search .NET](https://aka.ms/search-sdk)
* [Cómo usar Azure Search desde una aplicación .NET](https://docs.microsoft.com/azure/search/search-howto-dotnet-sdk)

## <a name="overview"></a>Información general

Las consultas de antes y después del uso de sinónimos muestran el valor de los mismos. En este tutorial, usamos una aplicación de ejemplo que ejecuta consultas y devuelve los resultados en un índice de ejemplo. La aplicación de ejemplo crea un índice pequeño denominado "hotels" rellenado con dos documentos. La aplicación ejecuta consultas de búsqueda usando términos y frases que no aparecen en el índice, habilita la característica de sinónimos y vuelve a realizar las mismas búsquedas. El código siguiente muestra el flujo general.

```csharp
  static void Main(string[] args)
  {
      SearchServiceClient serviceClient = CreateSearchServiceClient();

      Console.WriteLine("{0}", "Cleaning up resources...\n");
      CleanupResources(serviceClient);

      Console.WriteLine("{0}", "Creating index...\n");
      CreateHotelsIndex(serviceClient);

      ISearchIndexClient indexClient = serviceClient.Indexes.GetClient("hotels");

      Console.WriteLine("{0}", "Uploading documents...\n");
      UploadDocuments(indexClient);

      ISearchIndexClient indexClientForQueries = CreateSearchIndexClient();

      RunQueriesWithNonExistentTermsInIndex(indexClientForQueries);

      Console.WriteLine("{0}", "Adding synonyms...\n");
      UploadSynonyms(serviceClient);
      EnableSynonymsInHotelsIndex(serviceClient);
      Thread.Sleep(10000); // Wait for the changes to propagate

      RunQueriesWithNonExistentTermsInIndex(indexClientForQueries);

      Console.WriteLine("{0}", "Complete.  Press any key to end application...\n");

      Console.ReadKey();
  }
```
Se explican los pasos necesarios para crear y rellenar el índice de ejemplo en [Cómo usar Azure Search desde una aplicación .NET](https://docs.microsoft.com/azure/search/search-howto-dotnet-sdk).

## <a name="before-queries"></a>Consultas "antes de"

En `RunQueriesWithNonExistentTermsInIndex`, se emiten consultas de búsqueda con "five star", "internet" y "economy AND hotel".
```csharp
Console.WriteLine("Search the entire index for the phrase \"five star\":\n");
results = indexClient.Documents.Search<Hotel>("\"five star\"", parameters);
WriteDocuments(results);

Console.WriteLine("Search the entire index for the term 'internet':\n");
results = indexClient.Documents.Search<Hotel>("internet", parameters);
WriteDocuments(results);

Console.WriteLine("Search the entire index for the terms 'economy' AND 'hotel':\n");
results = indexClient.Documents.Search<Hotel>("economy AND hotel", parameters);
WriteDocuments(results);
```
Ninguno de los dos documentos indexados contienen los términos, por lo que obtenemos el siguiente resultado del primer `RunQueriesWithNonExistentTermsInIndex`.
~~~
Search the entire index for the phrase "five star":

no document matched

Search the entire index for the term 'internet':

no document matched

Search the entire index for the terms 'economy' AND 'hotel':

no document matched
~~~

## <a name="enable-synonyms"></a>Habilitación de sinónimos

La habilitación de sinónimos es un proceso de dos pasos. Primero se definen y cargan reglas de sinónimos y, a continuación, se configuran los campos para utilizarlos. El proceso se describe en `UploadSynonyms` y `EnableSynonymsInHotelsIndex`.

1. Agregue un mapa de sinónimos al servicio de búsqueda. En `UploadSynonyms`, definimos cuatro reglas en nuestro mapa de sinónimos 'desc synonymmap' y lo cargamos en el servicio.
```csharp
    var synonymMap = new SynonymMap()
    {
        Name = "desc-synonymmap",
        Format = "solr",
        Synonyms = "hotel, motel\n
                    internet,wifi\n
                    five star=>luxury\n
                    economy,inexpensive=>budget"
    };

    serviceClient.SynonymMaps.CreateOrUpdate(synonymMap);
```
Un mapa de sinónimos tiene que cumplir el formato `solr` de estándar de código abierto. El formato se explica en [Synonyms in Azure Search](search-synonyms.md) (Sinónimos en Azure Search) en la sección `Apache Solr synonym format`.

2. Configure los campos de búsqueda para utilizar el mapa de sinónimos en la definición del índice. En `EnableSynonymsInHotelsIndex`, habilitaremos los sinónimos en dos campos `category` y `tags` estableciendo la propiedad `synonymMaps` en el nombre del mapa de sinónimos recién cargado.
```csharp
  Index index = serviceClient.Indexes.Get("hotels");
  index.Fields.First(f => f.Name == "category").SynonymMaps = new[] { "desc-synonymmap" };
  index.Fields.First(f => f.Name == "tags").SynonymMaps = new[] { "desc-synonymmap" };

  serviceClient.Indexes.CreateOrUpdate(index);
```
Al agregar un mapa de sinónimos, no son necesarias las recompilaciones de índices. Puede agregar un mapa de sinónimos a su servicio y luego modificar las definiciones de campo existentes en los índices para usar el nuevo mapa de sinónimos. La adición de nuevos atributos no influye en la disponibilidad de los índices. Lo mismo se aplica a la deshabilitación de sinónimos para un campo. Simplemente establezca la propiedad `synonymMaps` en una lista vacía.
```csharp
  index.Fields.First(f => f.Name == "category").SynonymMaps = new List<string>();
```

## <a name="after-queries"></a>Consultas "después de"

Después cargar el mapa de sinónimos y de actualizar el índice para que use el mapa de sinónimos, la segunda llamada a `RunQueriesWithNonExistentTermsInIndex` genera lo siguiente:

~~~
Search the entire index for the phrase "five star":

Name: Fancy Stay        Category: Luxury        Tags: [pool, view, wifi, concierge]

Search the entire index for the term 'internet':

Name: Fancy Stay        Category: Luxury        Tags: [pool, view, wifi, concierge]

Search the entire index for the terms 'economy' AND 'hotel':

Name: Roach Motel       Category: Budget        Tags: [motel, budget]
~~~
La primera consulta busca el documento a partir de la regla `five star=>luxury`. La segunda consulta amplía la búsqueda utilizando `internet,wifi` y la tercera usando `hotel, motel` y `economy,inexpensive=>budget` en la búsqueda de coincidencia en los documentos.

Agregar sinónimos cambia totalmente la experiencia de búsqueda. En este tutorial, las consultas originales devolvieron resultados significativos, aunque los documentos en nuestro índice eran relevantes. Al habilitar sinónimos, podemos ampliar un índice para incluir los términos de uso común, sin realizar cambios en los datos subyacentes en el índice.

## <a name="sample-application-source-code"></a>Código fuente de aplicación de ejemplo
Puede encontrar el código fuente completo de la aplicación de ejemplo usada en este tutorial en [GitHub](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetHowToSynonyms).

## <a name="next-steps"></a>Pasos siguientes

* Revise [cómo utilizar sinónimos en Azure Search](search-synonyms.md)
* Revise la [documentación sobre el uso de sinónimos con la API de REST](https://aka.ms/rgm6rq)
* Examine las referencias del [SDK para .NET](https://docs.microsoft.com/dotnet/api/microsoft.azure.search) y la [API de REST](https://docs.microsoft.com/rest/api/searchservice/).
