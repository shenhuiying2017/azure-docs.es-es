---
title: "Indexación de blobs CSV con el indexador de blobs de Azure Search | Microsoft Docs"
description: Aprenda a indexar blobs JSON con Azure Search
services: search
documentationcenter: 
author: chaosrealm
manager: pablocas
editor: 
ms.assetid: ed3c9cff-1946-4af2-a05a-5e0b3d61eb25
ms.service: search
ms.devlang: rest-api
ms.workload: search
ms.topic: article
ms.tgt_pltfrm: na
ms.date: 12/15/2016
ms.author: eugenesh
ms.openlocfilehash: 60ca696a6fa8f277a13875c39b44577c4b38c92a
ms.sourcegitcommit: f8437edf5de144b40aed00af5c52a20e35d10ba1
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/03/2017
---
# <a name="indexing-csv-blobs-with-azure-search-blob-indexer"></a>Indexación de blobs CSV con el indexador de blobs de Azure Search
De forma predeterminada, el [indexador de blobs de Azure Search](search-howto-indexing-azure-blob-storage.md) analiza los blobs de texto delimitados como un único fragmento de texto. Sin embargo, con blobs que contienen datos CSV, a menudo se desea tratar cada línea del blob como un documento independiente. Por ejemplo, dado el siguiente texto delimitado: 

    id, datePublished, tags
    1, 2016-01-12, "azure-search,azure,cloud" 
    2, 2016-07-07, "cloud,mobile" 

desea analizarlo en dos documentos, que contiene cada uno de ellos los campos "id", "datePublished" y "tags".

En este artículo aprenderá cómo analizar blobs CSV con un indexador de blobs de Azure Search. 

> [!IMPORTANT]
> Actualmente, la versión de esta funcionalidad es una versión preliminar. Está disponible solo en la API de REST con la versión **2015-02-28-Preview**. Por favor, recuerde que las versiones preliminares de las API están pensadas para realizar pruebas y evaluar, y no deben usarse en entornos de producción. 
> 
> 

## <a name="setting-up-csv-indexing"></a>Configuración de la indexación de CSV
Para indexar blobs CSV, cree o actualice una definición de indexador con el modo de análisis `delimitedText` :  

    {
      "name" : "my-csv-indexer",
      ... other indexer properties
      "parameters" : { "configuration" : { "parsingMode" : "delimitedText", "firstLineContainsHeaders" : true } }
    }

Para más información sobre la API Create Indexer, consulte [Crear indexador](https://docs.microsoft.com/rest/api/searchservice/create-indexer).

`firstLineContainsHeaders` indica que la primera línea (no en blanco) de cada blob contiene encabezados.
Si el blob no contienen una línea de encabezado inicial, los encabezados deben especificarse en la configuración del indexador: 

    "parameters" : { "configuration" : { "parsingMode" : "delimitedText", "delimitedTextHeaders" : "id,datePublished,tags" } } 

Actualmente, solo se admite la codificación UTF-8. Además, solo se admite el carácter de coma `','` como delimitador. Si necesita compatibilidad con otras codificaciones o delimitadores, háganoslo saber en [nuestro sitio web de UserVoice](https://feedback.azure.com/forums/263029-azure-search).

> [!IMPORTANT]
> Cuando se utiliza el modo de análisis de texto delimitado, Azure Search supone que todos los blobs del origen de datos serán CSV. Si necesita admitir una mezcla de blobs CSV y que no son CSV en el mismo origen de datos, háganoslo saber en [nuestro sitio UserVoice](https://feedback.azure.com/forums/263029-azure-search).
> 
> 

## <a name="request-examples"></a>Ejemplos de solicitud
Resumiendo, estos son los ejemplos de cargas útiles completas. 

Origen de datos: 

    POST https://[service name].search.windows.net/datasources?api-version=2015-02-28-Preview
    Content-Type: application/json
    api-key: [admin key]

    {
        "name" : "my-blob-datasource",
        "type" : "azureblob",
        "credentials" : { "connectionString" : "DefaultEndpointsProtocol=https;AccountName=<account name>;AccountKey=<account key>;" },
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

## <a name="help-us-make-azure-search-better"></a>Ayúdenos a mejorar Azure Search
Si tiene solicitudes o ideas para mejorar las características, póngase en contacto con nosotros en nuestro [sitio UserVoice](https://feedback.azure.com/forums/263029-azure-search/).

