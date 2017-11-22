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
ms.date: 09/07/2017
ms.author: eugenesh
ms.openlocfilehash: 2dac2c5980970946a6b9c26ee6ee8ac0f0344144
ms.sourcegitcommit: 93902ffcb7c8550dcb65a2a5e711919bd1d09df9
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/09/2017
---
# <a name="indexing-json-blobs-with-azure-search-blob-indexer"></a>Indexación de blobs JSON con el indexador de blobs de Azure Search
En este artículo se muestra cómo configurar un indexador de blobs de Azure Search para extraer contenido estructurado de los blobs JSON en Azure Blob Storage.

Los blobs JSON de Azure Blobs Storage suelen ser un documento JSON único o una matriz JSON. El indexador de blobs de Azure Search puede analizar cualquier construcción, según cómo configure el parámetro **parsingMode** en la solicitud.

| Documento JSON | parsingMode | Descripción | Disponibilidad |
|--------------|-------------|--------------|--------------|
| Uno por blob | `json` | Analiza los blobs JSON como un único fragmento de texto. Cada blob JSON se convierte en un único documento de Azure Search. | Por lo general, está disponible en API de [REST](https://docs.microsoft.com/rest/api/searchservice/indexer-operations) y [.NET](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.indexer). |
| Varios por blob | `jsonArray` | Analiza una matriz JSON en el blob, donde cada elemento de la matriz se convierte en un documento de Azure Search independiente.  | En versión preliminar, en [REST api-version=`2016-09-01-Preview`](search-api-2016-09-01-preview.md) y [versión preliminar de SDK de .NET](https://aka.ms/search-sdk-preview). |

> [!Note]
> Las versiones preliminares de las API están pensadas para realizar pruebas y evaluaciones, y no deben usarse en entornos de producción.
>

## <a name="setting-up-json-indexing"></a>Configuración de la indexación de JSON
La indexación de blobs JSON es similar a la extracción normal de documentos en un flujo de trabajo de tres partes común a todos los indexadores en Azure Search.

### <a name="step-1-create-a-data-source"></a>Paso 1: Creación de un origen de datos

El primer paso es proporcionar la información de conexión de origen de datos utilizada por el indexador. El tipo de origen de datos, especificado aquí como `azureblob`, determina qué comportamientos de extracción de datos se invocan mediante el indexador. Para la indexación de blobs JSON, la definición del origen de datos es la misma para documentos y matrices JSON. 

    POST https://[service name].search.windows.net/datasources?api-version=2016-09-01
    Content-Type: application/json
    api-key: [admin key]

    {
        "name" : "my-blob-datasource",
        "type" : "azureblob",
        "credentials" : { "connectionString" : "DefaultEndpointsProtocol=https;AccountName=<account name>;AccountKey=<account key>;" },
        "container" : { "name" : "my-container", "query" : "optional, my-folder" }
    }   

### <a name="step-2-create-a-target-search-index"></a>Paso 2: Creación de un índice de búsqueda de destino 

Los indexadores se emparejan con un esquema de índice. Si utiliza la API (en lugar del portal), prepare un índice de antemano para poder especificarlo en la operación del indexador. 

> [!Note]
> Los indexadores se exponen en el portal a través de la acción **Importar** para un número limitado de indexadores disponibles con carácter general. A menudo, el flujo de trabajo de importación puede construir un índice preliminar basándose en los metadatos en el origen. Para obtener más información, consulte [Importación de datos en Azure Search en el portal](search-import-data-portal.md).

### <a name="step-3-configure-and-run-the-indexer"></a>Paso 3: Configuración y ejecución del indexador

Hasta ahora, las definiciones para el origen de datos y el índice han sido independientes de parsingMode. Sin embargo, en el paso 3 de la configuración del indexador, la ruta de acceso difiere según cómo desee que se analice y estructure el contenido de los blobs JSON en un índice de Azure Search.

Al llamar al indexador, haga lo siguiente:

+ Establezca el parámetro **parsingMode** en `json` (para indexar cada blob como un documento individual) o en `jsonArray` (si los blobs contienen matrices JSON y necesita que cada elemento de una matriz se trate como un documento independiente).

+ Como alternativa, utilice **asignaciones de campo** para elegir las propiedades del documento JSON de origen que se utilizarán para completar el índice de búsqueda de destino. En el caso de las matrices JSON, si la matriz existe como propiedad de nivel inferior, puede establecer una raíz del documento que indique dónde se coloca la matriz en el blob.

> [!IMPORTANT]
> Cuando se usa el modo de análisis de `json` o `jsonArray`, Azure Search da por hecho que todos los blobs en el origen de datos contienen JSON. Si necesita admitir una mezcla de blobs JSON y que no son JSON en el mismo origen de datos, háganoslo saber en [nuestro sitio UserVoice](https://feedback.azure.com/forums/263029-azure-search).


## <a name="how-to-parse-single-json-blobs"></a>Cómo analizar blobs JSON únicos

De forma predeterminada, el [indexador de blobs de Azure Search](search-howto-indexing-azure-blob-storage.md) analiza los blobs JSON como un único fragmento de texto. A menudo se desea conservar la estructura de los documentos de JSON. Por ejemplo, suponga que tiene el siguiente documento JSON en Azure Blob Storage:

    {
        "article" : {
            "text" : "A hopefully useful article explaining how to parse JSON blobs",
            "datePublished" : "2016-04-13"
            "tags" : [ "search", "storage", "howto" ]    
        }
    }

### <a name="indexer-definition-for-single-json-blobs"></a>Definición de indexador para blobs JSON únicos

Mediante el indexador de blobs de Azure Search, un documento JSON similar al del ejemplo anterior, se analiza en un único documento de Azure Search. El indexador carga un índice asociando "text", "datePublished" y "tags" del origen con los campos de destino con nombre y tipo idénticos.

La configuración se proporciona en el cuerpo de una operación del indexador. Recuerde que el objeto de origen de datos, definido previamente especifica la información de conexión y de tipo de origen de datos. Además, el índice de destino también debe existir como contenedor vacío en el servicio. La programación y los parámetros son opcionales, pero, si los omite, el indexador se ejecuta de inmediato, con `json` como modo de análisis.

Una solicitud totalmente especificada se vería similar a la siguiente:

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

Como se indicó, las asignaciones de campos no son necesarias. Dado un índice con los campos "text", "datePublished" y "tags", el indexador de blobs puede inferir la asignación correcta sin que haya presente una asignación de campos en la solicitud.

## <a name="how-to-parse-json-arrays-preview"></a>Cómo analizar matrices JSON (versión preliminar)

Como alternativa, puede optar por la característica de versión preliminar de las matrices JSON. Esta funcionalidad es útil cuando los blobs contienen una *matriz de objetos JSON* y quiere que cada elemento se convierta en un documento de Azure Search independiente. Por ejemplo, dado el blob JSON siguiente, puede rellenar el índice de Azure Search con tres documentos independientes, cada uno de ellos con los campos "id" y "text".  

    [
        { "id" : "1", "text" : "example 1" },
        { "id" : "2", "text" : "example 2" },
        { "id" : "3", "text" : "example 3" }
    ]

### <a name="indexer-definition-for-a-json-array"></a>Definición del indexador para una matriz JSON

Para una matriz JSON, la solicitud del indexador utiliza la API de versión preliminar y el analizador `jsonArray`. Se trata de los únicos dos requisitos específicos de la matriz para la indexación de blobs JSON.

    POST https://[service name].search.windows.net/indexers?api-version=2016-09-01-Preview
    Content-Type: application/json
    api-key: [admin key]

    {
      "name" : "my-json-indexer",
      "dataSourceName" : "my-blob-datasource",
      "targetIndexName" : "my-target-index",
      "schedule" : { "interval" : "PT2H" },
      "parameters" : { "configuration" : { "parsingMode" : "jsonArray" } }
    }

Nuevamente, tenga en cuenta que las asignaciones de campos no son necesarias. Dado un índice con los campos "id" y "text", el indexador de blobs puede inferir la asignación correcta sin una lista de asignación de campos.

### <a name="nested-json-arrays"></a>Matrices JSON anidadas
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

Utilice esta configuración para indexar la matriz de la propiedad `level2`:

    {
        "name" : "my-json-array-indexer",
        ... other indexer properties
        "parameters" : { "configuration" : { "parsingMode" : "jsonArray", "documentRoot" : "/level1/level2" } }
    }

## <a name="using-field-mappings-to-build-search-documents"></a>Uso de asignaciones de campo para crear documentos de búsqueda

Cuando los campos de origen y de destino no estén perfectamente alineados, puede definir una sección de asignación de campos en el cuerpo de la solicitud para crear asociaciones explícitas de campo a campo.

Actualmente, Azure Search no puede indexar documentos JSON arbitrarios directamente, ya que admite solo tipos de datos primitivos, matrices de cadenas y puntos de GeoJSON. Sin embargo, puede usar **asignaciones de campo** para seleccionar partes del documento JSON y los "elevan" a campos de nivel superior del documento de búsqueda. Para obtener información acerca de los conceptos básicos de las asignaciones de campos, consulte [Asignaciones de campos en los indexadores de Azure Search](search-indexer-field-mappings.md).

Volviendo al documento JSON de ejemplo:

    {
        "article" : {
            "text" : "A hopefully useful article explaining how to parse JSON blobs",
            "datePublished" : "2016-04-13"
            "tags" : [ "search", "storage", "howto" ]    
        }
    }

Supongamos que tiene un índice de búsqueda con los siguientes campos: `text` del tipo `Edm.String`, `date` del tipo `Edm.DateTimeOffset` y `tags` del tipo `Collection(Edm.String)`. Tenga en cuenta la diferencia entre "datePublished" en el origen y el campo `date` en el índice. Para asignar JSON en la forma deseada, utilice las siguientes asignaciones de campo:

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

## <a name="example-indexer-request-with-field-mappings"></a>Ejemplo: solicitud del indexador con asignaciones de campos

El ejemplo siguiente es una carga de indexador completamente especificada, incluidas las asignaciones de campos:

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


## <a name="help-us-make-azure-search-better"></a>Ayúdenos a mejorar Azure Search
Si tiene solicitudes o ideas para mejorar las características, póngase en contacto con nosotros en el [sitio de UserVoice](https://feedback.azure.com/forums/263029-azure-search/).

## <a name="see-also"></a>Otras referencias

+ [Indexadores de Azure Search](search-indexer-overview.md)
+ [Indexación de Azure Blob Storage con Azure Search](search-howto-index-json-blobs.md)
+ [Indexación de blobs CSV con el indexador de blobs de Azure Search](search-howto-index-csv-blobs.md)
+ [Tutorial: Búsqueda de datos semiestructurados en el almacenamiento en la nube](search-semi-structured-data.md)