---
title: "Indexación de blobs JSON con el indexador de blobs de Azure Search"
description: "Indexación de blobs JSON con el indexador de blobs de Azure Search"
services: search
documentationcenter: 
author: chaosrealm
manager: pablocas
editor: 
ms.assetid: 57e32e51-9286-46da-9d59-31884650ba99
ms.service: search
ms.devlang: rest-api
ms.workload: search
ms.topic: article
ms.tgt_pltfrm: na
ms.date: 04/10/2017
ms.author: eugenesh
translationtype: Human Translation
ms.sourcegitcommit: cc9e81de9bf8a3312da834502fa6ca25e2b5834a
ms.openlocfilehash: c4a9e57cda4ba5b4db742c1a37686a802f58212f
ms.lasthandoff: 04/11/2017

---

# <a name="indexing-json-blobs-with-azure-search-blob-indexer"></a>Indexación de blobs JSON con el indexador de blobs de Azure Search
En este artículo se muestra cómo configurar el indexador de blobs de Azure Search para extraer el contenido estructurado de los blobs que contienen JSON.

## <a name="scenarios"></a>Escenarios
De forma predeterminada, el [indexador de blobs de Azure Search](search-howto-indexing-azure-blob-storage.md) analiza los blobs JSON como un único fragmento de texto. A menudo se desea conservar la estructura de los documentos de JSON. Por ejemplo, es posible que quiera analizar un documento JSON determinado

    {
        "article" : {
             "text" : "A hopefully useful article explaining how to parse JSON blobs",
            "datePublished" : "2016-04-13"
            "tags" : [ "search", "storage", "howto" ]    
        }
    }

en un documento de Azure Search con los campos text, datePublished y tags.

También, cuando los blobs contienen una **matriz de objetos JSON**, se recomienda que cada elemento de la matriz se convierta en un documento de Azure Search independiente. Por ejemplo, en un blob con este JSON:  

    [
        { "id" : "1", "text" : "example 1" },
        { "id" : "2", "text" : "example 2" },
        { "id" : "3", "text" : "example 3" }
    ]

puede rellenar el índice de Azure Search con tres documentos independientes, cada uno de ellos con los campos "id" y "text".

> [!IMPORTANT]
> Actualmente, la versión de la funcionalidad de análisis de matrices de JSON está en versión preliminar. Está disponible solo en la API de REST con la versión **2015-02-28-Preview**. Recuerde que las versiones preliminares de las API están pensadas para realizar pruebas y evaluaciones, y no deben usarse en entornos de producción.
>
>

## <a name="setting-up-json-indexing"></a>Configuración de la indexación de JSON
La indexación de blobs de JSON es similar a la extracción de documentos normal. En primer lugar, cree el origen de datos como lo haría normalmente: 

    POST https://[service name].search.windows.net/datasources?api-version=2016-09-01
    Content-Type: application/json
    api-key: [admin key]

    {
        "name" : "my-blob-datasource",
        "type" : "azureblob",
        "credentials" : { "connectionString" : "DefaultEndpointsProtocol=https;AccountName=<account name>;AccountKey=<account key>;" },
        "container" : { "name" : "my-container", "query" : "optional, my-folder" }
    }   

Si aún no tiene un índice de búsqueda de destino, créelo. 

Por último, cree un indexador y establezca el parámetro `parsingMode` en `json` (para indexar cada blob como un documento individual) o en `jsonArray` (si los blobs contienen matrices JSON y necesita que cada elemento de una matriz se trate como un documento independiente):

    POST https://[service name].search.windows.net/indexers?api-version=2016-09-01
    Content-Type: application/json
    api-key: [admin key]

    {
      "name" : "my-json-indexer",
      "dataSourceName" : "my-blob-datasource",
      "targetIndexName" : "my-target-index",
      "schedule" : { "interval" : "PT2H" },
      "parameters" : { "configuration" : { "parsingMode" : "json" } }
    }

Si es necesario, use las **asignaciones de campos** para seleccionar las propiedades del documento JSON de origen usadas para rellenar el índice de búsqueda de destino, como se muestra en la siguiente sección.

> [!IMPORTANT]
> Cuando se usa el modo de análisis de `json` o `jsonArray`, Azure Search da por hecho que todos los blobs en el origen de datos contienen JSON. Si necesita admitir una mezcla de blobs JSON y que no son JSON en el mismo origen de datos, háganoslo saber en [nuestro sitio UserVoice](https://feedback.azure.com/forums/263029-azure-search).
>
>

## <a name="using-field-mappings-to-build-search-documents"></a>Uso de asignaciones de campo para crear documentos de búsqueda
Actualmente, Azure Search no puede indexar documentos JSON arbitrarios directamente, ya que admite solo tipos de datos primitivos, matrices de cadenas y puntos de GeoJSON. Sin embargo, puede usar **asignaciones de campo** para seleccionar partes del documento JSON y los "elevan" a campos de nivel superior del documento de búsqueda. Para obtener información sobre los aspectos básicos de las asignaciones de campo, consulte [Las asignaciones de campos de indexador de Azure Search salvan las diferencias entre los orígenes de datos y los índices de búsqueda](search-indexer-field-mappings.md).

Volviendo a nuestro documento JSON de ejemplo:

    {
        "article" : {
             "text" : "A hopefully useful article explaining how to parse JSON blobs",
            "datePublished" : "2016-04-13"
            "tags" : [ "search", "storage", "howto" ]    
        }
    }

Supongamos que tiene un índice de búsqueda con los siguientes campos: `text` del tipo `Edm.String`, `date` del tipo `Edm.DateTimeOffset` y `tags` del tipo `Collection(Edm.String)`. Para asignar JSON en la forma deseada, utilice las siguientes asignaciones de campo:

    "fieldMappings" : [
        { "sourceFieldName" : "/article/text", "targetFieldName" : "text" },
        { "sourceFieldName" : "/article/datePublished", "targetFieldName" : "date" },
        { "sourceFieldName" : "/article/tags", "targetFieldName" : "tags" }
      ]

Los nombres de campo de origen en las asignaciones se especifican con la notación [puntero JSON](http://tools.ietf.org/html/rfc6901) . Comience con una barra diagonal para hacer referencia a la raíz del documento JSON y, después, seleccione en la propiedad que desee (a un nivel arbitrario de anidamiento) mediante una ruta de acceso separada por una barra diagonal.

También puede hacer referencia a elementos individuales de la matriz mediante un índice de base cero. Por ejemplo, para elegir el primer elemento de la matriz "etiquetas" del ejemplo anterior, use una asignación de campo como esta:

    { "sourceFieldName" : "/article/tags/0", "targetFieldName" : "firstTag" }

> [!NOTE]
> Si un nombre de campo de origen en una ruta de acceso de asignación de campo hace referencia a una propiedad que no existe en JSON, la asignación se omite sin errores. Esto se realiza, por lo que podemos admitir documentos con un esquema diferente (que es un caso de uso frecuente). Puesto que no hay ninguna validación, tiene que procurar evitar tipográficos en la especificación de las asignaciones de campo.
>
>

Si sus documentos JSON solo contienen propiedades simples de nivel superior, es posible que no necesite asignaciones de campo. Por ejemplo, si un documento JSON se parece al siguiente, las propiedades de nivel superior "text", "datePublished" y "tags" se asignan directamente a los campos correspondientes en el índice de búsqueda:

    {
       "text" : "A hopefully useful article explaining how to parse JSON blobs",
       "datePublished" : "2016-04-13"
       "tags" : [ "search", "storage", "howto" ]    
     }

Esta es una carga completa del indexador con asignaciones de campos:

    POST https://[service name].search.windows.net/indexers?api-version=2016-09-01
    Content-Type: application/json
    api-key: [admin key]

    {
      "name" : "my-json-indexer",
      "dataSourceName" : "my-blob-datasource",
      "targetIndexName" : "my-target-index",
      "schedule" : { "interval" : "PT2H" },
      "parameters" : { "configuration" : { "parsingMode" : "json" } },
      "fieldMappings" : [
        { "sourceFieldName" : "/article/text", "targetFieldName" : "text" },
        { "sourceFieldName" : "/article/datePublished", "targetFieldName" : "date" },
        { "sourceFieldName" : "/article/tags", "targetFieldName" : "tags" }
        ]
    }

## <a name="indexing-nested-json-arrays"></a>Indexación de matrices anidadas JSON
¿Qué sucede si quiere indexar una matriz de objetos JSON, pero está anidada en alguna parte del documento? Puede elegir la propiedad que contiene la matriz mediante la propiedad de configuración `documentRoot` . Por ejemplo, si los blobs tienen el siguiente aspecto:

    {
        "level1" : {
            "level2" : [
                { "id" : "1", "text" : "Use the documentRoot property" },
                { "id" : "2", "text" : "to pluck the array you want to index" },
                { "id" : "3", "text" : "even if it's nested inside the document" }  
            ]
        }
    }

utilice esta configuración para indexar la matriz de la propiedad `level2`:

    {
        "name" : "my-json-array-indexer",
        ... other indexer properties
        "parameters" : { "configuration" : { "parsingMode" : "jsonArray", "documentRoot" : "/level1/level2" } }
    }

## <a name="help-us-make-azure-search-better"></a>Ayúdenos a mejorar Azure Search
Si tiene solicitudes o ideas para mejorar las características, póngase en contacto con nosotros en el [sitio de UserVoice](https://feedback.azure.com/forums/263029-azure-search/).

