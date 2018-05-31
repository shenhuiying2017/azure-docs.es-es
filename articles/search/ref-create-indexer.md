---
title: Crear un indizador (versión de la API de REST del Servicio de Azure Search = 2017-11-11-Versión preliminar)
description: En la versión preliminar de la API se han ampliado los indizadores para incluir parámetros outputFieldMapping que se usan para asignar el resultado de enriquecimiento a un campo de un índice de Azure Search.
services: search
manager: pablocas
author: luiscabrer
ms.author: luisca
ms.date: 05/01/2018
ms.prod: azure
ms.service: search
ms.devlang: rest-api
ms.workload: search
ms.topic: language-reference
ms.openlocfilehash: 595502ecf0a78491c73800e8077de65707c7a486
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/20/2018
ms.locfileid: "34365192"
---
# <a name="create-indexer-azure-search-service-rest-api-version2017-11-11-preview"></a>Crear un indizador (versión de la API de REST del Servicio de Azure Search = 2017-11-11-Versión preliminar)

Esta referencia de API es una versión específica de la versión preliminar de la documentación, la cual agrega elementos de [Cognitive Search](cognitive-search-concept-intro.md) a Create Indexer API. Al igual que con la versión [disponible con carácter general](https://docs.microsoft.com/rest/api/searchservice/create-indexer), puede crear un indizador dentro de un servicio Azure Search usando una solicitud HTTP POST. 

```http
POST https://[service name].search.windows.net/indexers?api-version=2017-11-11-Preview
    Content-Type: application/json  
    api-key: [admin key]  
```  
**api-key** debe ser una clave de administración (en lugar de una clave de consulta). Consulte la sección de autenticación de [Security in Azure Search](search-security-overview.md) (Seguridad en Azure Search) para obtener más información sobre las claves. En [Create an Azure Search service in the portal](search-create-service-portal.md) (Crear un servicio de Azure Search el portal) se explica cómo obtener la dirección URL del servicio y las propiedades de clave usadas en la solicitud.

Como alternativa, puede usar PUT y especificar el nombre del origen de datos en el URI. Si el origen de datos no existe, se creará.  

```http
PUT https://[service name].search.windows.net/indexers/[indexer name]?api-version=[api-version]  
```  
La **api-version** es obligatoria. La versión actual disponible es `api-version=2017-11-11`, pero necesita la versión preliminar para Cognitive Search: `api-version=2017-11-11-Preview`.  Vea [API versions in Azure Search](search-api-versions.md) (Versiones de API en Azure Search) para obtener información detallada.

Para obtener una guía específica de la plataforma de datos sobre cómo crear indizadores, comience con la [introducción a los indizadores](search-indexer-overview.md), que incluye la lista completa de [artículos relacionados](search-indexer-overview.md#next-steps).

> [!NOTE]  
>  El número máximo de indizadores permitido varía según el plan de tarifa. El servicio gratuito permite hasta tres indexadores. El servicio estándar permite 50 indexadores. Los servicios estándar de alta definición no son de ninguna manera compatibles con los indexadores. Consulte [Límites de servicio](search-limits-quotas-capacity.md) para obtener más información.    

## <a name="request"></a>Solicitud  

Un [origen de datos](https://docs.microsoft.com/rest/api/searchservice/create-data-source), un [índice](https://docs.microsoft.com/rest/api/searchservice/create-index) y un [conjunto de aptitudes](ref-create-skillset.md) forman parte de la definición de un [indexador](search-indexer-overview.md), pero cada uno de ellos es un componente independiente que se puede usar en diferentes combinaciones. Por ejemplo, puede usar el mismo origen de datos con varios indexadores, o el mismo índice con varios indexadores, o escribir varios indexadores en un índice único.

 El cuerpo de la solicitud contiene una definición de indexador, con las siguientes partes.

+ [dataSourceName](#dataSourceName)
+ [targetIndexName](#targetIndexName)
+ [skillsetName](#skillset)
+ [schedule](#indexer-schedule)
+ [parameters](#indexer-parameters)
+ [fieldMappings](#field-mappings)
+ [outputFieldMappings](#output-fieldmappings)

## <a name="request-syntax"></a>Sintaxis de la solicitud

La sintaxis para estructurar la carga de la solicitud es la siguiente. En este artículo se proporciona una solicitud de ejemplo más adelante.  

```json
{   
    "name" : "Required for POST, optional for PUT. The name of the indexer",  
    "description" : "Optional. Anything you want, or null",  
    "dataSourceName" : "Required. The name of an existing data source",  
    "targetIndexName" : "Required. The name of an existing index",  
    "skillsetName" : "Required for cognitive search enrichment",
    "schedule" : { Optional, but immediately runs once if unspecified. See Indexing Schedule below. },  
    "parameters" : { Optional. See Indexing Parameters below. },  
    "fieldMappings" : { Optional. See fieldMappings below. },
    "outputFieldMappings" : { Required for enrichment pipelines. See outputFieldMappings below. },
    "disabled" : Optional boolean value indicating whether the indexer is disabled. False by default.
}  
```
<a name="dataSourceName"></a>

### <a name="datasourcename"></a>"dataSourceName"

Una [definición del origen de datos](https://docs.microsoft.com/rest/api/searchservice/create-data-source) a menudo incluye propiedades que un indexador puede usar para aprovechar las características de la plataforma de origen. Por lo tanto, el origen de datos que se pasará al indexador determina la disponibilidad de ciertas propiedades y parámetros, como el filtrado del tipo de contenido en Azure Blobs, o el tiempo de expiración de consultas de Azure SQL Database. 

<a name="targetIndexName"></a>

### <a name="targetindexname"></a>"targetIndexName"

Un [esquema de índice](https://docs.microsoft.com/rest/api/searchservice/create-index) define la colección de campos que contiene atributos que se pueden buscar, filtrar, recuperar o determinar cómo se usa un campo, entre otras cosas. Durante la indexación, el indexador rastrea el origen de datos, accede a los documentos y extrae información de forma opcional, serializa los resultados a JSON e indexa la carga según el esquema definido para el índice.

<a name="skillset"></a>

### <a name="skillsetname"></a>"skillsetName"

[Cognitive Search (versión preliminar)](cognitive-search-concept-intro.md) hace referencia al lenguaje natural y a las capacidades de procesamiento de imágenes en Azure Search; se aplica durante la ingesta de datos para extraer entidades, frases clave, idioma, información de las imágenes, etc. Las transformaciones se aplican al contenido a través de *aptitudes*, que se pueden combinar en un solo [*conjunto de aptitudes*](ref-create-skillset.md), uno por indexador. Igual que sucede con las fuentes de datos y los índices, un conjunto de aptitudes es un componente independiente que se adjunta a un indexador. Puede reutilizar un conjunto de aptitudes con otros indexadores, pero cada indexador solo puede usar un conjunto de aptitudes a la vez.
 
<a name="indexer-schedule"></a>

### <a name="schedule"></a>"schedule"  
Un indizador puede especificar opcionalmente una programación. Sin una programación, el indexador se ejecuta inmediatamente cuando se envía una de las siguientes solicitudes: conectar con, rastreo e indexación el origen de datos. En algunos escenarios, incluidos los trabajos de indexación de larga duración, se usan programaciones para [ampliar la ventana de procesamiento](search-howto-reindex.md#scale-out-indexing) más allá del máximo de 24 horas. Si existe una programación, el indizador se ejecuta de forma periódica de acuerdo con esta. El programador está integrado, por lo que no se puede usar uno externo. Una **programación** tiene los siguientes atributos: 

-   **intervalo**: obligatorio. Valor de duración que especifica un intervalo o período durante el que se ejecuta el indizador. El intervalo mínimo permitido es de cinco minutos y el máximo de un día. Debe tener el formato de un valor "dayTimeDuration" XSD (subconjunto restringido de un valor de [duración ISO 8601](http://www.w3.org/TR/xmlschema11-2/#dayTimeDuration) ). El patrón correspondiente es: `"P[nD][T[nH][nM]]".` Ejemplos: `PT15M` para cada 15 minutos, `PT2H` para cada 2 horas.  

-   **startTime**: opcional. Valor de fecha y hora UTC que especifica cuándo debería empezar a ejecutarse el indexador.  

<a name="indexer-parameters"></a>

### <a name="parameters"></a>"parameters"

De forma opcional, un indexador puede usar parámetros de configuración que modifiquen el comportamiento del tiempo de ejecución. Los parámetros de configuración están delimitados por comas en la solicitud del indexador. 

```json
    {
      "name" : "my-blob-indexer-for-cognitive-search",
      ... other indexer properties
      "parameters" : { "maxFailedItems" : "15", "batchSize" : "100", "configuration" : { "parsingMode" : "json", "indexedFileNameExtensions" : ".json, .jpg, .png", "imageAction" : "generateNormalizedImages", "dataToExtract" : "contentAndMetadata" } }
    }
```

#### <a name="general-parameters-for-all-indexers"></a>Parámetros generales para todos los indexadores

| . | Tipos y valores permitidos   | Uso  |
|-----------|------------|--------------------------|--------|
| `"batchSize"` | Entero<br/>El valor predeterminado es específico del origen (1000 para Azure SQL Database y Azure Cosmos DB; 10 para Azure Blob Storage) | Especifica el número de elementos que se leen desde el origen de datos y se indexan como un único lote para mejorar el rendimiento. |
| `"maxFailedItems"` | Entero<br/>El valor predeterminado es 0. | Número de errores que se tolera antes de que la ejecución del indexador se considere un error. Se establece en -1 si no quiere que los errores detengan el proceso de indización. Puede recuperar información sobre elementos erróneos mediante [Get Indexer Status](https://docs.microsoft.com/rest/api/searchservice/get-indexer-status) (Obtener estado del indexador).  |
| `"maxFailedItemsPerBatch"` | Entero<br/>El valor predeterminado es 0. | Número de errores que se tolera en cada lote antes de que la ejecución del indexador se considere un error. Se establece en -1 si no quiere que los errores detengan el proceso de indización. |

#### <a name="blob-configuration-parameters"></a>Parámetros de configuración de blobs

Varios parámetros son exclusivos de un indexador determinado, como la [indexación de blobs de Azure](search-howto-indexing-azure-blob-storage.md).

| . | Tipos y valores permitidos   | Uso  |
|-----------|---------------------------|--------|
| `"parsingMode"` | string<br/>`"text"`<br/>`"delimitedText"`<br/>`"json"`<br/>`"jsonArray"`  | En los [blobs de Azure](search-howto-indexing-azure-blob-storage.md), se establece en `text` para mejorar el rendimiento de la indexación de los archivos de texto sin formato en Blob Storage. <br/>En los [blobs de CSV](search-howto-index-csv-blobs.md), se establece en `delimitedText` cuando los blobs son archivos sin formato de CSV. <br/>En los [blobs de JSON](search-howto-index-json-blobs.md), se establece en `json` para extraer contenido estructurado, o en `jsonArray` (versión preliminar) para extraer los elementos individuales de una matriz como documentos independientes en Azure Search. |
| `"excludedFileNameExtensions"` | string<br/>Lista delimitada por comas | En los [blobs de Azure](search-howto-indexing-azure-blob-storage.md), ignore los tipos de archivo de la lista. Por ejemplo, puede excluir ".png, .png,. mp4" para omitir esos archivos durante la indexación. | 
| `"indexedFileNameExtensions"` | string<br/>Lista delimitada por comas | En los [blobs de Azure](search-howto-indexing-azure-blob-storage.md), selecciona los blobs si la extensión de archivo está en la lista. Por ejemplo, puede enfocar la indexación en archivos de aplicaciones específicos como ".docx, .pptx y .msg", para incluir específicamente esos tipos de archivo. | 
| `"failOnUnsupportedContentType"` | true <br/>False (valor predeterminado) | En los [blobs de Azure](search-howto-indexing-azure-blob-storage.md), establezca `false` si quiere continuar con la indexación cuando se encuentre un tipo de contenido no admitido y no conoce todos los tipos de contenido (extensiones de archivos) de antemano. |
| `"failOnUnprocessableDocument"` | true <br/>False (valor predeterminado)| En los [blobs de Azure](search-howto-indexing-azure-blob-storage.md), establezca `false` si quiere seguir con la indexación si un documento no puede realizar este proceso de indexación. |
| `"indexStorageMetadataOnlyForOversizedDocuments"` | true <br/>False (valor predeterminado)| En los [blobs de Azure](search-howto-indexing-azure-blob-storage.md), establezca esta propiedad en `true` para seguir indexando los metadatos de almacenamiento del contenido de blob que sea demasiado grande para procesar.  Los blobs demasiado grandes se tratan como errores de forma predeterminada. Si quiere obtener información acerca de los límites de tamaño de los blobs, consulte los [límites de servicio](search-limits-quotas-capacity.md). |
| `"delimitedTextHeaders"` | string<br/>Lista delimitada por comas| En los [blobs de CSV (versión preliminar) ](search-howto-index-csv-blobs.md), se especifica una lista delimitada por comas de encabezados de columna, que resulta útil para asignar campos de origen a los campos de destino de un índice. |
| `"delimitedTextDelimiter"` | string<br/>definidas por el usuario | En los [blobs de CSV (versión preliminar)](search-howto-index-csv-blobs.md), se especifica el delimitador del final de línea para archivos CSV donde cada línea comienza un nuevo documento (por ejemplo, `"|"`).  |
| `"firstLineContainsHeaders"` | true (predeterminado) <br/>false | En los [blobs de CSV (versión preliminar)](search-howto-index-csv-blobs.md) se indica que la primera línea (que no esté en blanco) de cada blob contiene encabezados.|
| `"documentRoot"`  | string<br/>definidas por el usuario | En las [matrices JSON (versión preliminar)](search-howto-index-json-blobs.md#nested-json-arrays), dado un documento estructurado o semiestructurado, puede especificar una ruta de acceso a la matriz mediante esta propiedad. |
| `"dataToExtract"` | string<br/>`"storageMetadata"`<br/>`"allMetadata"`<br/>`"contentAndMetadata"` (valor predeterminado) | En los [blobs de Azure](search-howto-indexing-azure-blob-storage.md):<br/>Se establece en `"storageMetadata"` para indexar solamente las [propiedades de blob estándar y los metadatos que especificó el usuario](../storage/blobs/storage-properties-metadata.md). <br/>Se establece en `"allMetadata"` para extraer los metadatos que proporciona el subsistema de almacenamiento de blobs de Azure y los [metadatos específicos del tipo de contenido](search-howto-indexing-azure-blob-storage.md#ContentSpecificMetadata) (por ejemplo, metadatos únicos de archivos .png) que se indexa. <br/>Se establece en `"contentAndMetadata"` para extraer todos los metadatos y el contenido textual de cada blob. <br/><br/>En el [análisis de imágenes de Cognitive Search (versión preliminar)](cognitive-search-concept-image-scenarios.md), cuando `"imageAction"` está configurado en `"generateNormalizedImages"`, la configuración `"dataToExtract"` le dice al indexador qué datos debe extraer del contenido de la imagen. Se aplica al contenido de imágenes insertadas en un .PDF u otra aplicación, o en archivos de imagen como .jpg y .png, de los blobs de Azure.  |
| `"imageAction"` |  string<br/>`"none"`<br/>`"generateNormalizedImages"` | En los [blobs de Azure](search-howto-indexing-azure-blob-storage.md), se establece en `"none"` para ignorar las imágenes insertadas o los archivos de imagen del conjunto de datos. Este es el valor predeterminado. <br/><br/>En el [análisis de imágenes de Cognitive Search](cognitive-search-concept-image-scenarios.md) se establece en `"generateNormalizedImages"` para extraer texto de las imágenes (por ejemplo, la palabra "stop" de una señal de Stop) y lo inserte como parte del campo de contenido. Durante el análisis de imágenes, el indexador crea una matriz de imágenes normalizadas como parte del descifrado de documentos e inserta la información generada en el campo de contenido. Esta acción requiere que `"dataToExtract"` se configure en `"contentAndMetadata"`. Una imagen normalizada hace referencia a un procesamiento adicional que crea una imagen de salida uniforme, dimensionada y rotada para facilitar una representación consistente cuando se incluyen imágenes en resultados de búsqueda visuales (por ejemplo, fotografías del mismo tamaño en un control de gráficos, tal como se ve en la [demostración JFK](https://github.com/Microsoft/AzureSearch_JFK_Files)). Esta información se crea para cada imagen cuando usa |


#### <a name="other-configuration-parameters"></a>Otros parámetros de configuración

Los siguientes parámetros son específicos de Azure SQL Database.

| . | Tipos y valores permitidos   | Uso  |
|-----------|---------------------------|--------|
| `"queryTimeout"` | string<br/>"hh:mm:ss"<br/>"00:05:00"| En [Azure SQL Database](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md), establezca este parámetro para aumentar el tiempo de espera predeterminado de 5 minutos.|

<a name="field-mappings"></a>

### <a name="fieldmappings"></a>"fieldMappings"

Las definiciones de indizador contienen asociaciones de campo para asignar un campo de origen a un campo de destino en un índice de Azure Search. Hay dos tipos de asociaciones en función de si la transferencia de contenido va seguida de una ruta directa o enriquecida:

+ **fieldMappings** son asociaciones opcionales que se aplican cuando los nombres de campo de origen y destino no coinciden o si se quiere especificar una función.
+ **outputFieldMappings** son asociaciones obligatorias si se va a compilar [una canalización de enriquecimiento](cognitive-search-concept-intro.md). En una canalización de enriquecimiento, el campo de salida es una construcción definida durante el proceso de enriquecimiento. Por ejemplo, el campo de salida podría ser una estructura compuesta compilada durante el enriquecimiento a partir de dos campos independientes del documento de origen. 

En el siguiente ejemplo, imagínese que hay una tabla de origen con un campo `_id`. Azure Search no permite un nombre de campo que comience por un carácter de subrayado, por lo que debe cambiarse el nombre del campo. Esto puede hacerse mediante la propiedad `fieldMappings` del indexador del modo indicado a continuación:

```json
"fieldMappings" : [ { "sourceFieldName" : "_id", "targetFieldName" : "id" } ]
```

Puede especificar varias asignaciones de campo.

```json
"fieldMappings" : [
    { "sourceFieldName" : "_id", "targetFieldName" : "id" },
    { "sourceFieldName" : "_timestamp", "targetFieldName" : "timestamp" }
]
```

Los nombres de campo de origen y destino no distinguen entre mayúsculas y minúsculas.

Para obtener información sobre los escenarios en los que son útiles las asignaciones de campos, vea [Search Indexer Field Mappings](https://docs.microsoft.com/azure/search/search-indexer-field-mappings) (Buscar asignaciones de campos de indizador).

<a name="output-fieldmappings"></a>

### <a name="outputfieldmappings"></a>"outputFieldMappings"

En los escenarios de [Cognitive Search](cognitive-search-concept-intro.md) en los que un conjunto de aptitudes está enlazado a un indexador, deberá agregar `outputFieldMappings` para asociar todos los resultados de un paso de enriquecimiento que proporcionen contenido a un campo de búsqueda del índice.

```json
  "outputFieldMappings" : [
        {
          "sourceFieldName" : "/document/organizations", 
          "targetFieldName" : "organizations"
        },
        {
          "sourceFieldName" : "/document/pages/*/keyPhrases/*", 
          "targetFieldName" : "keyphrases"
        },
        {
            "sourceFieldName": "/document/languageCode",
            "targetFieldName": "language",
            "mappingFunction": null
        }      
   ],
```

<a name="FieldMappingFunctions"></a>

### <a name="field-mapping-functions"></a>Funciones de asignación de campos

Las asignaciones de campos también pueden usarse para transformar valores del campo de origen mediante *funciones de asignación de campos*. Por ejemplo, un valor de cadena arbitraria puede tener una codificación base64 para que se pueda usar para rellenar un campo de clave de documento.

Para obtener más información sobre cuándo y cómo se deben usar las funciones de asignación de campos, vea [Field Mapping Functions](https://docs.microsoft.com/azure/search/search-indexer-field-mappings#field-mapping-functions) (Funciones de asignación de campos).

## <a name="request-examples"></a>Ejemplos de solicitud  
 En el primer ejemplo se crea un indexador que copia datos de la tabla a la que hace referencia el origen de datos `ordersds` en el índice `orders`, de acuerdo con una programación que empieza el 1 de enero de 2015 UTC y que se ejecuta cada hora. Cada invocación de indexador se realizará correctamente si no se logra indexar más de cinco elementos en cada lote y no más de 10 elementos en total.  

```json
{
    "name" : "myindexer",  
    "description" : "a cool indexer",  
    "dataSourceName" : "ordersds",  
    "targetIndexName" : "orders",  
    "schedule" : { "interval" : "PT1H", "startTime" : "2018-01-01T00:00:00Z" },  
    "parameters" : { "maxFailedItems" : 10, "maxFailedItemsPerBatch" : 5 }  
}
```

En el segundo ejemplo se demuestra una operación de Cognitive Search, que indica la referencia a un conjunto de aptitudes y [outputFieldMappings](#output-fieldmappings). Los [conjuntos de aptitudes](ref-create-skillset.md) son recursos de alto nivel y se definen por separado. Este ejemplo es un resumen de la definición del indexador que encontrará en el [ tutorial de Cognitive Search](cognitive-search-tutorial-blob.md).

```json
{
  "name":"demoindexer", 
  "dataSourceName" : "demodata",
  "targetIndexName" : "demoindex",
  "skillsetName" : "demoskillset",
  "fieldMappings" : [
    {
        "sourceFieldName" : "content",
        "targetFieldName" : "content"
    }
   ],
  "outputFieldMappings" : 
  [
    {
        "sourceFieldName" : "/document/organizations", 
        "targetFieldName" : "organizations"
    },
  ],
  "parameters":
  {
    "maxFailedItems":-1,
    "configuration": 
    {
    "dataToExtract": "contentAndMetadata",
    "imageAction": "generateNormalizedImages"
    }
  }
}
```

## <a name="response"></a>Response  
 Para obtener una solicitud correcta: "201 Creado".  

## <a name="see-also"></a>Otras referencias

+ [Información general del indexador](search-indexer-overview.md)
+ [Introducción a la búsqueda cognitiva](cognitive-search-concept-intro.md)
+ [Inicio rápido: probar la búsqueda cognitiva](cognitive-search-quickstart-blob.md)
+ [Cómo asignar campos](cognitive-search-output-field-mapping.md)
