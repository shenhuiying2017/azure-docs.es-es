---
title: Las asignaciones de campos de indexador de Búsqueda de Azure salvan las diferencias entre los orígenes de datos y los índices de búsqueda
description: Configurar asignaciones de campos de indexador de Búsqueda de Azure para tener en cuenta las diferencias en los nombres de campo y las representaciones de datos
services: search
documentationcenter: ''
author: chaosrealm
manager: pablocas
editor: ''

ms.service: search
ms.devlang: rest-api
ms.workload: search
ms.topic: article
ms.tgt_pltfrm: na
ms.date: 04/30/2016
ms.author: eugenesh

---
# Las asignaciones de campos de indexador de Búsqueda de Azure salvan las diferencias entre los orígenes de datos y los índices de búsqueda
Al usar los indexadores de Búsqueda de Azure, habrá ocasiones en que pueda encontrarse en situaciones donde sus datos de entrada no coincidan demasiado con el esquema de su índice de destino. En esos casos, puede usar **asignaciones de campos** para transformar sus datos en la forma deseada.

Algunas situaciones donde las asignaciones de campos son útiles:

* Su origen de datos tiene un campo `_id`, pero Búsqueda de Azure no permite los nombres de campo que empiezan por un carácter de subrayado. Una asignación de campos permite "cambiar el nombre" a un campo. 
* Desea rellenar varios campos en el índice con los mismos datos de origen de datos, por ejemplo, porque desea aplicar diferentes analizadores a esos campos. Las asignaciones de campos permiten "bifurcar" un campo de origen de datos.
* Necesita codificar o descodificar sus datos con Base64. Las asignaciones de campos admiten varias **funciones de asignación**, incluidas las funciones de codificación y descodificación Base64.   

> [!IMPORTANT]
> Actualmente, la funcionalidad de las asignaciones de campos se encuentra en versión preliminar. Está disponible solo en la API de REST con la versión **2015-02-28-Preview**. Por favor, recuerde que las versiones preliminares de las API están pensadas para realizar pruebas y evaluar, y no deben usarse en entornos de producción.
> 
> 

## Configuración de asignaciones de campos
Puede agregar asignaciones de campos al crear un nuevo indexador con la API de [creación de indexador](search-api-indexers-2015-02-28-preview.md#create-indexer). Puede administrar asignaciones de campos en un indexador de indización con la API de [actualización de indexador](search-api-indexers-2015-02-28-preview.md#update-indexer).

Una asignación de campos consta de 3 partes:

1. `sourceFieldName`, que representa un campo de su origen de datos. Esta propiedad es obligatoria. 
2. `targetFieldName` opcional, que representa un campo de su índice de búsqueda. Si se omite, se usa el mismo nombre que en el origen de datos.
3. `mappingFunction` opcional, que puede transformar sus datos con una de las diversas funciones predefinidas. La lista completa de funciones se encuentra [a continuación](#mappingFunctions).

Las asignaciones de campos se agregan a la matriz `fieldMappings` de la definición del indexador.

Por ejemplo, así es como puede adaptarse a las diferencias existentes en los nombres de campo:

```JSON

PUT https://[service name].search.windows.net/indexers/myindexer?api-version=[api-version]
Content-Type: application/json
api-key: [admin key]
{
    "dataSourceName" : "mydatasource",
    "targetIndexName" : "myindex",
    "fieldMappings" : [ { "sourceFieldName" : "_id", "targetFieldName" : "id" } ] 
} 
```

Un indexador puede tener varias asignaciones de campos. Por ejemplo, así es como puede "bifurcar" un campo:

```JSON

"fieldMappings" : [ 
    { "sourceFieldName" : "text", "targetFieldName" : "textStandardEnglishAnalyzer" },
    { "sourceFieldName" : "text", "targetFieldName" : "textSoundexAnalyzer" }, 
] 
```

> [!NOTE]
> Búsqueda de Azure usa una comparación que no distingue mayúsculas de minúsculas para resolver los nombres de campo y función de las asignaciones de campos. Esto es práctico (no es necesario que el uso de mayúsculas y minúsculas sea correcto en todo momento), pero se traduce en que su índice u origen de datos no puede tener campos que difieran únicamente en mayúsculas y minúsculas.
> 
> 

<a name="mappingFunctions"></a>

## Funciones de asignación de campos
Actualmente se admiten estas funciones:

* [base64Encode](#base64EncodeFunction)
* [base64Decode](#base64DecodeFunction)
* [extractTokenAtPosition](#extractTokenAtPositionFunction)
* [jsonArrayToStringCollection](#jsonArrayToStringCollectionFunction)

<a name="base64EncodeFunction"></a>

### base64Encode
Realiza una codificación Base64 *segura para direcciones URL* de la cadena de entrada. Asume que la entrada presenta una codificación UTF-8.

#### Caso de uso de ejemplo
Solo pueden aparecer caracteres seguros para direcciones URL en una clave de documento de Búsqueda de Azure (porque los clientes deben poder enviar el documento con la API de búsqueda, por ejemplo). Si sus datos contienen caracteres no seguros para direcciones URL y desea usarlos para rellenar un campo clave de su índice de búsqueda, utilice esta función.

#### Ejemplo
```JSON

"fieldMappings" : [ 
  { 
    "sourceFieldName" : "Path", 
    "targetFieldName" : "UrlSafePath",
    "mappingFunction" : { "name" : "base64Encode" } 
  }] 
```

<a name="base64DecodeFunction"></a>

### base64Decode
Realiza una descodificación Base64 de la cadena de entrada. La entrada se asume para una cadena con codificación Base64 *segura para direcciones URL*.

#### Caso de uso de ejemplo
Los valores de metadatos personalizados del blob deben tener codificación ASCII. Puede usar la codificación Base64 para representar cadenas Unicode arbitrarias en metadatos personalizados del blob. Sin embargo, para que la búsqueda sea significativa, puede usar esta función para volver a convertir los datos codificados en cadenas "normales" al rellenar su índice de búsqueda.

#### Ejemplo
```JSON

"fieldMappings" : [ 
  { 
    "sourceFieldName" : "Base64EncodedMetadata", 
    "targetFieldName" : "SearchableMetadata",
    "mappingFunction" : { "name" : "base64Decode" } 
  }] 
```

<a name="extractTokenAtPositionFunction"></a>

### extractTokenAtPosition
Divide un campo de cadena con el delimitador especificado y elige el token en la posición especificada de la división resultante.

Por ejemplo, si la entrada es `Jane Doe`, `delimiter` es `" "`(espacio) y `position` es 0, el resultado es `Jane`; si `position` es 1, el resultado es `Doe`. Si la posición hace referencia a un token que no existe, se devolverá un error.

#### Caso de uso de ejemplo
Su origen de datos contiene un campo `PersonName` y desea indizarlo como dos campos `FirstName` y `LastName` independientes. Puede usar esta función para dividir la entrada con el carácter de espacio como delimitador.

#### Parámetros
* `delimiter`: una cadena para su uso como separador al dividir la cadena de entrada.
* `position`: una posición de base cero entera del token que se va a elegir tras dividirse la cadena de entrada.    

#### Ejemplo
```JSON 

"fieldMappings" : [ 
  { 
    "sourceFieldName" : "PersonName", 
    "targetFieldName" : "FirstName",
    "mappingFunction" : { "name" : "extractTokenAtPosition", "parameters" : { "delimiter" : " ", "position" : 0 } } 
  }, 
  { 
    "sourceFieldName" : "PersonName", 
    "targetFieldName" : "LastName",
    "mappingFunction" : { "name" : "extractTokenAtPosition", "parameters" : { "delimiter" : " ", "position" : 1 } } 
  }] 
```

<a name="jsonArrayToStringCollectionFunction"></a>

### jsonArrayToStringCollection
Transforma una cadena con formato de una matriz JSON de cadenas en una matriz de cadenas que puede usarse para rellenar un campo `Collection(Edm.String)` del índice.

Por ejemplo, si la cadena de entrada es `["red", "white", "blue"]`, el campo de destino de tipo `Collection(Edm.String)` se rellenará con los tres valores `red`, `white` y `blue`. En el caso de los valores de entrada que no pueden analizarse como matrices de cadenas JSON, se devolverá un error.

#### Caso de uso de ejemplo
La base de datos SQL de Azure no tiene un tipo de datos integrado que se asigne de forma natural a los campos `Collection(Edm.String)` de Búsqueda de Azure. Para rellenar campos de colección de cadenas, aplique a sus datos de origen formato de una matriz de cadenas JSON y use esta función.

#### Ejemplo
```JSON

"fieldMappings" : [ 
  { "sourceFieldName" : "tags", "mappingFunction" : { "name" : "jsonArrayToStringCollection" } }
] 
```

## Ayúdenos a mejorar Búsqueda de Azure
Si tiene solicitudes o ideas para mejorar las características, póngase en contacto con nosotros en nuestro [sitio UserVoice](https://feedback.azure.com/forums/263029-azure-search/).

<!---HONumber=AcomDC_0504_2016-->