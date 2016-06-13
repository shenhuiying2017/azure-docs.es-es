<properties
pageTitle="Indexación de Almacenamiento de tablas de Azure con Búsqueda de Azure"
description="Obtenga información sobre cómo indexar los datos almacenados en tablas de Azure con Búsqueda de Azure"
services="search"
documentationCenter=""
authors="chaosrealm"
manager="pablocas"
editor="" />

<tags
ms.service="search"
ms.devlang="rest-api"
ms.workload="search" ms.topic="article"  
ms.tgt_pltfrm="na"
ms.date="05/28/2016"
ms.author="eugenesh" />

# Indexación de Almacenamiento de tablas de Azure con Búsqueda de Azure

Este artículo muestra cómo usar Búsqueda de Azure para indexar los datos almacenados en Almacenamiento de tablas de Azure. El nuevo indexador de tablas de Búsqueda de Azure agiliza y facilita este proceso.

> [AZURE.IMPORTANT] Actualmente, la versión de esta funcionalidad es una versión preliminar. Está disponible solo en la API de REST con la versión **2015-02-28-Preview**. Por favor, recuerde que las versiones preliminares de las API están pensadas para realizar pruebas y evaluar, y no deben usarse en entornos de producción.

## Configuración de la indexación de tablas de Azure

Para instalar y configurar un indexador tablas de Azure, puede usar la API de REST de Búsqueda de Azure para crear y administrar **indexadores** y **orígenes de datos**, tal como se describe en[Operaciones de indexador](https://msdn.microsoft.com/library/azure/dn946891.aspx). En el futuro, se agregará la compatibilidad con la indexación de tablas para el SDK de .NET de Búsqueda de Azure y el Portal de Azure.

Un origen de datos especifica los datos que se deben indexar, las credenciales necesarias para obtener acceso a los mismos y las directivas que posibilitan que Búsqueda de Azure identifique cambios en los datos de forma eficiente (filas nuevas, modificadas o eliminadas).

Un indexador lee datos de un origen de datos y los carga en un índice de búsqueda de destino.

Para configurar la indexación de tablas:

1. Creación de un origen de datos
	- Establezca el parámetro `type` en `azuretable`.
	- Transfiera la cadena de conexión de la cuenta de almacenamiento como parámetro `credentials.connectionString`
	- Especifique el nombre de la tabla con el parámetro `container.name`.
	- De manera opcional, especifique una consulta con el parámetro `container.query`. Siempre que sea posible, use un filtro en PartitionKey para obtener el mejor rendimiento posible; cualquier otra consulta dará como resultando un recorrido de tabla completo, el que puede generar un rendimiento deficiente en el caso de tablas de gran tamaño.
2. Cree un índice de búsqueda con el esquema que corresponde a las columnas de la tabla que desee indexar. 
3. Cree el indexador mediante la conexión del origen de datos con el índice de búsqueda.

### Creación de un origen de datos

	POST https://[service name].search.windows.net/datasources?api-version=2015-02-28-Preview
	Content-Type: application/json
	api-key: [admin key]

	{
	    "name" : "table-datasource",
	    "type" : "azuretable",
	    "credentials" : { "connectionString" : "<my storage connection string>" },
	    "container" : { "name" : "my-table", "query" : "PartitionKey eq '123'" }
	}   

Para más información sobre la API Create Datasource, consulte [Crear origen de datos](search-api-indexers-2015-02-28-preview.md#create-data-source).

### Creación de índice 

	POST https://[service name].search.windows.net/indexes?api-version=2015-02-28
	Content-Type: application/json
	api-key: [admin key]

	{
  		"name" : "my-target-index",
  		"fields": [
    		{ "name": "key", "type": "Edm.String", "key": true, "searchable": false },
    		{ "name": "SomeColumnInMyTable", "type": "Edm.String", "searchable": true }
  		]
	}

Para más información sobre la API Create Index, consulte [Create Index (Azure Search Service REST API)](https://msdn.microsoft.com/library/dn798941.aspx) (Create Index [API de REST del servicio Búsqueda de Azure]).

### Creación de un indexador 

Por último, cree un indexador que haga referencia al origen de datos y un índice de destino. Por ejemplo:

	POST https://[service name].search.windows.net/indexers?api-version=2015-02-28-Preview
	Content-Type: application/json
	api-key: [admin key]

	{
	  "name" : "table-indexer",
	  "dataSourceName" : "table-datasource",
	  "targetIndexName" : "my-target-index",
	  "schedule" : { "interval" : "PT2H" }
	}

Para más detalles sobre la API Create Indexer, consulte [Crear indexador](search-api-indexers-2015-02-28-preview.md#create-indexer).

Eso es todo. ¡Feliz indexación!

## Manejo de distintos nombres de campos

Con frecuencia, los nombres de campos del índice existente serán distintos de los nombres de las propiedades de la tabla. Puede usar **asignaciones de campos** para asignar los nombres de propiedad provenientes de la tabla a los nombres de campo del índice de búsqueda. Para más información sobre las asignaciones de campos, consulte [Las asignaciones de campos de indexador de Búsqueda de Azure salvan las diferencias entre los orígenes de datos y los índices de búsqueda](search-indexer-field-mappings.md).

## Control de claves de documentos

En Búsqueda de Azure, la clave del documento identifica de forma exclusiva a un documento. Cada índice de búsqueda debe tener exactamente un campo clave de tipo `Edm.String`. El campo clave es necesario para cada documento que se agrega al índice (de hecho, es el único campo obligatorio).

Como las filas de tablas tienen una clave compuesta, Búsqueda de Azure genera un campo sintético llamado `Key`, que es una concatenación de valores clave de partición y clave de fila. Por ejemplo, si el valor PartitionKey de una fila es `PK1` y el valor RowKey es `RK1`, el valor del campo `Key` será `PK1RK1`.

> [AZURE.NOTE] El valor `Key` puede contener caracteres no válidos en claves de documentos, como guiones. Para tratar con caracteres no válidos, use la [función de asignación de campo](search-indexer-field-mappings.md#base64EncodeFunction) `base64Encode`. Si lo hace, recuerde utilizar también la codificación Base64 de seguridad de direcciones URL al pasar las claves de documento en las llamadas de la API como búsqueda.

## Indexación incremental y detección de eliminación
 
Cuando configura un indexador de tablas para que se ejecute según una programación, vuelve a indexar solo las filas nuevas o actualizadas, según lo determine el valor `Timestamp` de una fila. No tiene que especificar una directiva de detección de cambios, la indexación incremental ya está habilitada automáticamente.

Para indicar que determinados documentos se deben quitar del índice, puede usar una estrategia de eliminación temporal: en lugar de eliminar una fila, agregue una propiedad para indicar que está eliminada y configure una directiva de detección de eliminación temporal en el origen de datos. Por ejemplo, la directiva que se muestra a continuación considerará que una fila está eliminada si tiene una propiedad `IsDeleted` con el valor `"true"`:

	PUT https://[service name].search.windows.net/datasources?api-version=2015-02-28-Preview
	Content-Type: application/json
	api-key: [admin key]
	
	{
	    "name" : "my-table-datasource",
	    "type" : "azuretable",
	    "credentials" : { "connectionString" : "<your storage connection string>" },
	    "container" : { "name" : "table name", "query" : "query" },
	    "dataDeletionDetectionPolicy" : { "@odata.type" : "#Microsoft.Azure.Search.SoftDeleteColumnDeletionDetectionPolicy", "softDeleteColumnName" : "IsDeleted", "softDeleteMarkerValue" : "true" }
	}   


## Ayúdenos a mejorar Búsqueda de Azure

Si tiene solicitudes o ideas para mejorar las características, póngase en contacto con nosotros en nuestro [sitio UserVoice](https://feedback.azure.com/forums/263029-azure-search/).

<!---HONumber=AcomDC_0601_2016-->