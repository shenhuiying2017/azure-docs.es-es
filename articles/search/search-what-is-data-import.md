---
title: Carga de datos en Azure Search | Microsoft Docs
description: "Aprenda a cargar datos en un índice en Búsqueda de Azure."
services: search
documentationcenter: 
author: ashmaka
manager: jhubbard
editor: 
tags: 
ms.assetid: aa8d47c1-4ae6-4209-a8ce-48d5a9474707
ms.service: search
ms.devlang: NA
ms.workload: search
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.date: 05/01/2017
ms.author: ashmaka
ms.openlocfilehash: 5a601b75ec67824e72d8736bc3c45f8e1231ca86
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="upload-data-to-azure-search"></a>Carga de datos en Búsqueda de Azure
> [!div class="op_single_selector"]
> * [Información general](search-what-is-data-import.md)
> * [.NET](search-import-data-dotnet.md)
> * [REST](search-import-data-rest-api.md)
> 
> 

Hay dos maneras de rellenar un índice con sus datos. La primera opción es insertar manualmente los datos en el índice mediante la [API de REST](search-import-data-rest-api.md) o el [SDK de .NET](search-import-data-dotnet.md) de Azure Search. La segunda opción es [señalar un origen de datos admitido](search-indexer-overview.md) hacia el índice y dejar que Azure Search y extraiga automáticamente los datos.

## <a name="push-data-to-an-index"></a>Inserción de datos en un índice
Este enfoque se basa en cómo enviar mediante programación los datos a Búsqueda de Azure para que estén disponibles para las búsquedas. En el caso de las aplicaciones con requisitos de latencia muy baja (por ejemplo, si se necesita que las operaciones de búsqueda estén sincronizadas con las bases de datos dinámicas del inventario), la única opción es un modelo de inserción.

Para insertar datos en un índice, se pueden usar la [API de REST](https://docs.microsoft.com/rest/api/searchservice/AddUpdate-or-Delete-Documents) o el [SDK de .NET](search-import-data-dotnet.md). Actualmente no se admite ninguna herramienta para insertar datos mediante el portal.

Este enfoque es más flexible que el modelo de extracción, ya que los documentos se pueden cargar individualmente o en lotes (hasta 1000 por lote o 16 MB, lo que ocurra primero). El modelo de inserción también le permite cargar documentos en Búsqueda de Azure independientemente de dónde están los datos.

El formato de datos que entiende Azure Search es JSON, y todos los documentos del conjunto de datos deben tener campos que se asignen a los campos definidos en el esquema del índice. 

## <a name="pull-data-into-an-index"></a>Extracción de datos e introducción en un índice
El modelo de extracción rastrea un origen de datos compatible y carga automáticamente los datos en el índice. En Azure Search, esta funcionalidad se implementa mediante *indexadores*, que actualmente están disponibles para [Blob Storage](search-howto-indexing-azure-blob-storage.md), [Table Storage](search-howto-indexing-azure-tables.md), [Azure Cosmos DB](http://aka.ms/documentdb-search-indexer), [Azure SQL Database y SQL Server en máquinas virtuales de Azure](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md). 

Los indexadores conectan un índice a un origen de datos (normalmente una tabla, vista o estructura equivalente) y asignan campos de origen a los campos equivalentes del índice. Durante la ejecución, el conjunto de filas se transforma automáticamente en JSON y se carga en el índice especificado. Todos los indexadores admiten la programación, de modo que se puede especificar con qué frecuencia se deben actualizar los datos. La mayoría de los indexadores proporcionan seguimiento de cambios, siempre el origen de datos lo admita. Mediante el seguimiento de cambios y eliminaciones en documentos existentes, además de reconocer nuevos documentos, los indexadores eliminan la necesidad de administrar activamente los datos del índice. 

La funcionalidad del indexador se expone en [Azure Portal](search-import-data-portal.md) así como en la [API de REST](/rest/api/searchservice/Indexer-operations) y el [SDK de .NET](/dotnet/api/microsoft.azure.search.indexersoperations). 

Una ventaja del uso el portal es que Azure Search normalmente puede generar automáticamente un esquema de índice predeterminado mediante la lectura de los metadatos del conjunto de datos de origen. El índice generado se puede modificar hasta que se procese, tras lo cual las únicas modificaciones de esquema que se permiten son las que no requieren volver a indexar. Si los cambios que desea realizar afectan directamente el esquema, necesitaría volver a generar el índice. 

Una vez que se rellena el índice, puede usar el **Explorador de búsqueda** de la barra de comandos del portal como paso de verificación.

## <a name="query-an-index-using-search-explorer"></a>Realización de consultas en un índice mediante el Explorador de búsqueda

Una forma rápida de realizar una comprobación preliminar en la carga de documentos es usar el **Explorador de búsqueda** en el portal. El explorador permite consultar un índice sin tener que escribir código. La búsqueda se basa en los valores predeterminados, como la [sintaxis simple](/rest/api/searchservice/simple-query-syntax-in-azure-search) y el [parámetro de consulta searchMode](/rest/api/searchservice/search-documents) predeterminado. Los resultados se devuelven en formato JSON, con el fin de que pueda revisar todo el documento.

> [!TIP]
> Muchos [ejemplos de código de Azure Search](https://github.com/Azure-Samples/?utf8=%E2%9C%93&query=search) incluyen conjuntos de datos incrustados o rápidamente disponibles, lo que supone una forma sencilla de empezar a trabajar. El portal también proporciona un indexador de ejemplo y un origen de datos que consta del conjunto de datos de una pequeña inmobiliaria (denominado "realestate-us-sample"). Al ejecutar el indexador preconfigurado en el origen de datos de ejemplo, se crea un índice que se carga con documentos que, luego, se pueden consultar en el Explorador de búsqueda o mediante un código creado por usted.