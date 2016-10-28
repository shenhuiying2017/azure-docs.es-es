<properties
pageTitle="Indexación de blobs CSV con el indexador de blobs de Búsqueda de Azure | Microsoft Azure"
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
ms.date="07/12/2016"
ms.author="eugenesh" />

# Indexación de blobs CSV con el indexador de blobs de Búsqueda de Azure 

De forma predeterminada, el [indexador de blobs de Búsqueda de Azure](search-howto-indexing-azure-blob-storage.md) analiza los blobs de texto delimitados como un único fragmento de texto. Sin embargo, con blobs que contienen datos CSV, a menudo se desea tratar cada línea del blob como un documento independiente. Por ejemplo, dado el siguiente texto delimitado:

	id, datePublished, tags
	1, 2016-01-12, "azure-search,azure,cloud" 
	2, 2016-07-07, "cloud,mobile" 

desea analizarlo en dos documentos, que contiene cada uno de ellos los campos "id", "datePublished" y "tags".

En este artículo aprenderá cómo analizar blobs CSV con un indexador de blobs de Búsqueda de Azure.

> [AZURE.IMPORTANT] Actualmente, la versión de esta funcionalidad es una versión preliminar. Está disponible solo en la API de REST con la versión **2015-02-28-Preview**. Por favor, recuerde que las versiones preliminares de las API están pensadas para realizar pruebas y evaluar, y no deben usarse en entornos de producción.

## Configuración de la indexación de CSV

Para indexar blobs CSV, cree o actualice una definición de indexador con el modo de análisis `delimitedText`:

	{
	  "name" : "my-csv-indexer",
	  ... other indexer properties
	  "parameters" : { "configuration" : { "parsingMode" : "delimitedText", "firstLineContainsHeaders" : true } }
	}

Para más información sobre la API Create Indexer, consulte [Crear indexador](search-api-indexers-2015-02-28-preview.md#create-indexer).

`firstLineContainsHeaders` indica que la primera línea (no en blanco) de cada blob contiene encabezados. Si el blob no contienen una línea de encabezado inicial, los encabezados deben especificarse en la configuración del indexador:

	"parameters" : { "configuration" : { "parsingMode" : "delimitedText", "delimitedTextHeaders" : "id,datePublished,tags" } } 

Actualmente, solo se admite la codificación UTF-8. Además, solo se admite el carácter de coma `','` como delimitador. Si necesita compatibilidad con otras codificaciones o delimitadores, háganoslo saber en [nuestro sitio web de UserVoice](https://feedback.azure.com/forums/263029-azure-search).

> [AZURE.IMPORTANT] Cuando se utiliza el modo de análisis de texto delimitado, Búsqueda de Azure supone que todos los blobs del origen de datos serán CSV. Si necesita admitir una mezcla de blobs CSV y que no son CSV en el mismo origen de datos, háganoslo saber en [nuestro sitio UserVoice](https://feedback.azure.com/forums/263029-azure-search).

## Ejemplos de solicitud

Resumiendo, estos son los ejemplos de cargas útiles completas.

Origen de datos:

	POST https://[service name].search.windows.net/datasources?api-version=2015-02-28-Preview
	Content-Type: application/json
	api-key: [admin key]

	{
	    "name" : "my-blob-datasource",
	    "type" : "azureblob",
	    "credentials" : { "connectionString" : "<my storage connection string>" },
	    "container" : { "name" : "my-container", "query" : "<optional, my-folder>" }
	}   

Indexador:

	POST https://[service name].search.windows.net/indexers?api-version=2015-02-28-Preview
	Content-Type: application/json
	api-key: [admin key]

	{
	  "name" : "my-csv-indexer",
	  "dataSourceName" : "my-blob-datasource",
	  "targetIndexName" : "my-target-index",
      "parameters" : { "configuration" : { "parsingMode" : "delimitedText", "delimitedTextHeaders" : "id,datePublished,tags" } }
	}

## Ayúdenos a mejorar Búsqueda de Azure

Si tiene solicitudes o ideas para mejorar las características, póngase en contacto con nosotros en nuestro [sitio UserVoice](https://feedback.azure.com/forums/263029-azure-search/).

<!---HONumber=AcomDC_0713_2016-->