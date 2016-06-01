<properties
pageTitle="Indexación de blobs JSON con el indexador de blobs de Búsqueda de Azure"
description="Aprenda a indexar blobs JSON con Búsqueda de Azure"
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
ms.date="04/20/2016"
ms.author="eugenesh" />

# Indexación de blobs JSON con el indexador de blobs de Búsqueda de Azure 

De forma predeterminada, el [indexador de blobs de Búsqueda de Azure](search-howto-indexing-azure-blob-storage.md) analiza los blobs JSON como un único fragmento de texto. Normalmente, desea conservar la estructura de los documentos JSON. Por ejemplo, es posible que quiera analizar un documento JSON determinado

	{ 
		"article" : {
	 		"text" : "A hopefully useful article explaining how to parse JSON blobs",
	        "datePublished" : "2016-04-13" 
			"tags" : [ "search", "storage", "howto" ]    
	    }
	}

con los campos "text", "datePublished" y "tags" en el índice de búsqueda.

Este artículo muestra cómo configurar el indexador de blobs de Búsqueda de Azure para el análisis de JSON. Esperamos que la indexación se realice correctamente.

> [AZURE.IMPORTANT] Actualmente, la versión de esta funcionalidad es una versión preliminar. Está disponible solo en la API de REST con la versión **2015-02-28-Preview**. Por favor, recuerde que las versiones preliminares de las API están pensadas para realizar pruebas y evaluar, y no deben usarse en entornos de producción.

## Configuración de la indexación de JSON

Para indexar los blobs JSON, use el indexador de blobs en modo "Análisis de JSON". Habilite los ajustes de configuración de `useJsonParser` en la propiedad `parameters` de la definición del indexador:

	{
	  "name" : "my-json-indexer",
	  ... other indexer properties
	  "parameters" : { "configuration" : { "useJsonParser" : true } }
	}

Si es necesario, utilice **asignaciones de campo** para elegir las propiedades del documento JSON de origen usado para completar el índice de búsqueda de destino. Esto se describe más detalladamente a continuación.

> [AZURE.IMPORTANT] Cuando se utiliza el modo de análisis de JSON, Búsqueda de Azure supone que todos los blobs en el origen de datos serán JSON. Si necesita admitir una mezcla de blobs JSON y que no son JSON en el mismo origen de datos, háganoslo saber en [nuestro sitio UserVoice](https://feedback.azure.com/forums/263029-azure-search).

## Uso de asignaciones de campo para crear documentos de búsqueda 

Actualmente, Búsqueda de Azure no puede indexar documentos JSON arbitrarios directamente, ya que admite solo tipos de datos primitivos, matrices de cadenas y puntos de GeoJSON. Sin embargo, puede usar **asignaciones de campo** para seleccionar partes del documento JSON y los "elevan" a campos de nivel superior del documento de búsqueda. Para obtener información sobre los aspectos básicos de las asignaciones de campo, consulte [Las asignaciones de campos de indexador de Búsqueda de Azure salvan las diferencias entre los orígenes de datos y los índices de búsqueda](search-indexer-field-mappings.md).

Volviendo a nuestro documento JSON de ejemplo:

    { 
		"article" : {
	 		"text" : "A hopefully useful article explaining how to parse JSON blobs",
	        "datePublished" : "2016-04-13" 
			"tags" : [ "search", "storage", "howto" ]    
	    }
	}

Supongamos que tiene un índice de búsqueda con los campos siguientes: `text` de tipo Edm.String, `date` de tipo Edm.DateTimeOffset y `tags` de tipo Collection (EDM.String). Para asignar JSON en la forma deseada, utilice las siguientes asignaciones de campo:

	"fieldMappings" : [ 
        { "sourceFieldName" : "/article/text", "targetFieldName" : "text" },
        { "sourceFieldName" : "/article/datePublished", "targetFieldName" : "date" },
        { "sourceFieldName" : "/article/tags", "targetFieldName" : "tags" }
  	]

Los nombres de campo de origen en las asignaciones se especifican con la notación [puntero JSON](http://tools.ietf.org/html/rfc6901). Comience con una barra diagonal para hacer referencia a la raíz del documento JSON y, a continuación, profundice en la propiedad deseada (nivel arbitrario de anidamiento) con una ruta de acceso separada mediante una barra diagonal.

También puede hacer referencia a elementos individuales de la matriz mediante un índice de base cero. Por ejemplo, para elegir el primer elemento de la matriz "etiquetas" del ejemplo anterior, use una asignación de campo como esta:

	{ "sourceFieldName" : "/article/tags/0", "targetFieldName" : "firstTag" }

> [AZURE.NOTE] Si un nombre de campo de origen en una ruta de acceso de asignación de campo hace referencia a una propiedad que no existe en JSON, la asignación se omite sin errores. Esto se realiza, por lo que podemos admitir documentos con un esquema diferente (que es un caso de uso frecuente). Puesto que no hay ninguna validación, tiene que procurar evitar tipográficos en la especificación de las asignaciones de campo.

Si sus documentos JSON solo contienen propiedades simples de nivel superior, es posible que no necesite asignaciones de campo. Por ejemplo, si su JSON se parece a esto, las propiedades de nivel superior "text", "datePublished" y "tags" se asignarán directamente a los campos correspondientes en el índice de búsqueda:
 
	{ 
	   "text" : "A hopefully useful article explaining how to parse JSON blobs",
	   "datePublished" : "2016-04-13" 
       "tags" : [ "search", "storage", "howto" ]    
 	}

> [AZURE.NOTE] Búsqueda de Azure solo admite actualmente el análisis de un blob JSON en un documento de búsqueda. Si los blobs contienen matrices JSON que le gustaría analizar en varios documentos de búsqueda, vote por [esta sugerencia de UserVoice](https://feedback.azure.com/forums/263029-azure-search/suggestions/13431384-parse-blob-containing-a-json-array-into-multiple-d) para ayudarnos a dar prioridad a este trabajo.

## Ejemplos de solicitud

Resumiendo, estos son los ejemplos de cargas completas.

Origen de datos:

	POST https://[service name].search.windows.net/datasources?api-version=2015-02-28-Preview
	Content-Type: application/json
	api-key: [admin key]

	{
	    "name" : "my-blob-datasource",
	    "type" : "azureblob",
	    "credentials" : { "connectionString" : "<my storage connection string>" },
	    "container" : { "name" : "my-container", "query" : "optional, my-folder" }
	}   

Indexador:

	POST https://[service name].search.windows.net/indexers?api-version=2015-02-28-Preview
	Content-Type: application/json
	api-key: [admin key]

	{
	  "name" : "my-json-indexer",
	  "dataSourceName" : "my-blob-datasource",
	  "targetIndexName" : "my-target-index",
	  "schedule" : { "interval" : "PT2H" },
      "parameters" : { "configuration" : { "useJsonParser" : true } }, 
      "fieldMappings" : [ 
        { "sourceFieldName" : "/article/text", "targetFieldName" : "text" },
        { "sourceFieldName" : "/article/datePublished", "targetFieldName" : "date" },
        { "sourceFieldName" : "/article/tags", "targetFieldName" : "tags" }
  	  ]
	}

## Ayúdenos a mejorar Búsqueda de Azure

Si tiene solicitudes o ideas para mejorar las características, póngase en contacto con nosotros en nuestro [sitio UserVoice](https://feedback.azure.com/forums/263029-azure-search/).

<!---HONumber=AcomDC_0518_2016-->