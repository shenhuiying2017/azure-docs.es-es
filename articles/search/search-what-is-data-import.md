---
title: "Importación de datos en Azure Search | Microsoft Docs"
description: "Aprenda a cargar datos en un índice en Azure Search."
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
ms.date: 01/05/2018
ms.author: ashmaka
ms.openlocfilehash: 8394475572502df9d57256dc5123fcdfa72765a6
ms.sourcegitcommit: e19f6a1709b0fe0f898386118fbef858d430e19d
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/13/2018
---
# <a name="indexing-in-azure-search"></a>Indexación en Azure Search
> [!div class="op_single_selector"]
> * [Información general](search-what-is-data-import.md)
> * [.NET](search-import-data-dotnet.md)
> * [REST](search-import-data-rest-api.md)
> 
> 

En Azure Search, las consultas se ejecutan sobre el contenido cargado en un [índice de búsqueda](search-what-is-an-index.md). Este artículo examina los dos enfoques básicos para cargar contenido en un índice: *insertar* los datos en el índice mediante programación o apuntar un [indexador de Azure Search](search-indexer-overview.md) en un origen de datos admitido para *extraer* en los datos.

## <a name="pushing-data-to-an-index"></a>Inserción de datos en un índice
El modelo de inserción, que se usa para enviar a Azure Search los datos mediante programación, es el enfoque más flexible. En primer lugar, no tiene ninguna restricción en el tipo de origen de datos. Cualquier conjunto de datos que se compone de documentos JSON se puede insertar en un índice de Azure Search si cada documento en el conjunto de datos tiene campos asignados a los campos definidos en el esquema de índice. En segundo lugar, no tiene ninguna restricción en la frecuencia de ejecución. Puede insertar los cambios a un índice tantas veces como desee. En el caso de las aplicaciones con requisitos de latencia muy baja (por ejemplo, si se necesita que las operaciones de búsqueda estén sincronizadas con las bases de datos dinámicas del inventario), la única opción es un modelo de inserción.

Este enfoque es más flexible que el modelo de extracción, ya que los documentos se pueden cargar individualmente o en lotes (hasta 1000 por lote o 16 MB, lo que ocurra primero). El modelo de inserción también le permite cargar documentos en Azure Search independientemente de dónde están los datos.

### <a name="how-to-push-data-to-an-azure-search-index"></a>Cómo insertar los datos en un índice de Azure Search

Puede usar las siguientes API para cargar uno o varios documentos en un índice:

+ [Agregar, Actualizar o Eliminar documentos (API de REST)](https://docs.microsoft.com/rest/api/searchservice/AddUpdate-or-Delete-Documents)
+ [Clase indexAction](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.indexaction?view=azure-dotnet) o [clase indexBatch](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.indexbatch?view=azure-dotnet) 

Actualmente no se admite ninguna herramienta para insertar datos mediante el portal.

Para una introducción a cada metodología, consulte [Importar datos con REST](search-import-data-rest-api.md) o [Importar datos mediante .NET](search-import-data-dotnet.md).


## <a name="pulling-data-into-an-index"></a>Extracción de datos en un índice
El modelo de extracción rastrea un origen de datos compatible y carga automáticamente los datos en el índice. En Azure Search, esta funcionalidad se implementa a través de *indexadores*, que en este momento se encuentran disponibles en las siguientes plataformas:

+ [Blob storage](search-howto-indexing-azure-blob-storage.md)
+ [Almacenamiento de tablas](search-howto-indexing-azure-tables.md)
+ [Azure Cosmos DB](http://aka.ms/documentdb-search-indexer)
+ [Azure SQL Database y SQL Server en máquinas virtuales de Azure](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md)

Los indexadores conectan un índice a un origen de datos (normalmente una tabla, vista o estructura equivalente) y asignan campos de origen a los campos equivalentes del índice. Durante la ejecución, el conjunto de filas se transforma automáticamente en JSON y se carga en el índice especificado. Todos los indexadores admiten la programación, de modo que se puede especificar con qué frecuencia se deben actualizar los datos. La mayoría de los indexadores proporcionan seguimiento de cambios, siempre el origen de datos lo admita. Mediante el seguimiento de cambios y eliminaciones en documentos existentes, además de reconocer nuevos documentos, los indexadores eliminan la necesidad de administrar activamente los datos del índice. 


### <a name="how-to-pull-data-into-an-azure-search-index"></a>Cómo extraer los datos en un índice de Azure Search

La funcionalidad del indexador se expone en [Azure Portal](search-import-data-portal.md) así como en la [API de REST](/rest/api/searchservice/Indexer-operations) y el [SDK de .NET](/dotnet/api/microsoft.azure.search.indexersoperations). 

Una ventaja del uso el portal es que Azure Search normalmente puede generar automáticamente un esquema de índice predeterminado mediante la lectura de los metadatos del conjunto de datos de origen. El índice generado se puede modificar hasta que se procese, tras lo cual las únicas modificaciones de esquema que se permiten son las que no requieren volver a indexar. Si los cambios que desea realizar afectan directamente el esquema, necesitaría volver a generar el índice. 

## <a name="verify-data-import-with-search-explorer"></a>Comprobar la importación de datos con el explorador de Search

Una forma rápida de realizar una comprobación preliminar en la carga de documentos es usar el **Explorador de búsqueda** en el portal. El explorador permite consultar un índice sin tener que escribir código. La búsqueda se basa en los valores predeterminados, como la [sintaxis simple](/rest/api/searchservice/simple-query-syntax-in-azure-search) y el [parámetro de consulta searchMode](/rest/api/searchservice/search-documents) predeterminado. Los resultados se devuelven en formato JSON, con el fin de que pueda revisar todo el documento.

> [!TIP]
> Muchos [ejemplos de código de Azure Search](https://github.com/Azure-Samples/?utf8=%E2%9C%93&query=search) incluyen conjuntos de datos incrustados o rápidamente disponibles, lo que supone una forma sencilla de empezar a trabajar. El portal también proporciona un indexador de ejemplo y un origen de datos que consta del conjunto de datos de una pequeña inmobiliaria (denominado "realestate-us-sample"). Al ejecutar el indexador preconfigurado en el origen de datos de ejemplo, se crea un índice que se carga con documentos que, luego, se pueden consultar en el Explorador de búsqueda o mediante un código creado por usted.

## <a name="see-also"></a>Otras referencias

+ [Información general del indexador](search-indexer-overview.md)
+ [Tutorial del portal: crear, cargar, consultar un índice](search-get-started-portal.md)