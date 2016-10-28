<properties
    pageTitle="Carga de datos en Búsqueda de Azure | Microsoft Azure | Servicio de búsqueda hospedado en la nube"
    description="Aprenda a cargar datos en un índice en Búsqueda de Azure."
    services="search"
    documentationCenter=""
    authors="ashmaka"
    manager=""
    editor=""
    tags=""/>

<tags
    ms.service="search"
    ms.devlang="NA"
    ms.workload="search"
    ms.topic="get-started-article"
    ms.tgt_pltfrm="na"
    ms.date="08/29/2016"
    ms.author="ashmaka"/>

# Carga de datos en Búsqueda de Azure
> [AZURE.SELECTOR]
- [Información general](search-what-is-data-import.md)
- [.NET](search-import-data-dotnet.md)
- [REST](search-import-data-rest-api.md)


## Modelos de carga de datos en Búsqueda de Azure
Hay dos maneras de rellenar el índice de Búsqueda de Azure con los datos. La primera opción es insertar manualmente los datos en el índice mediante la [API de REST](search-import-data-rest-api.md) o el [SDK de .NET](search-import-data-dotnet.md) de Búsqueda de Azure. La segunda opción es [seleccionar un origen de datos admitido](search-indexer-overview.md) para el índice de Búsqueda de Azure y permitir que este extraiga automáticamente los datos y los sitúe en el servicio de búsqueda.

Esta guía solo tratará instrucciones sobre cómo utilizar el modelo de inserción de carga de datos (que solo se admite en la [API de REST](search-import-data-rest-api.md) y el [SDK de .NET](search-import-data-dotnet.md)), pero también puede obtener más información sobre el modelo de extracción siguiente.

### Inserción de datos en un índice

Este enfoque se basa en cómo enviar mediante programación los datos a Búsqueda de Azure para que estén disponibles para las búsquedas. En el caso de las aplicaciones con requisitos de latencia muy baja (por ejemplo, si se necesita que las operaciones de búsqueda estén sincronizadas con las bases de datos dinámicas del inventario), la única opción es un modelo de inserción.

Para insertar datos en un índice, se pueden usar la [API de REST](https://msdn.microsoft.com/library/azure/dn798930.aspx) o el [SDK de .NET](search-import-data-dotnet.md). Actualmente no se admite ninguna herramienta para insertar datos mediante el portal.

Este enfoque es más flexible que el modelo de extracción, ya que los documentos se pueden cargar individualmente o en lotes (hasta 1000 por lote o 16 MB, lo que ocurra primero). El modelo de inserción también le permite cargar documentos en Búsqueda de Azure independientemente de dónde están los datos.

### Extracción de datos e introducción en un índice

El modelo de extracción rastrea un origen de datos admitido y carga automáticamente para usted los datos en el índice de Búsqueda de Azure. Mediante el seguimiento de cambios y eliminaciones en documentos existentes, además de reconocer nuevos documentos, los indexadores eliminan la necesidad de administrar activamente los datos del índice.

En Búsqueda de Azure, esta funcionalidad se implementa mediante *indexadores*, que actualmente están disponibles en [Almacenamiento de blobs (vista previa)](search-howto-indexing-azure-blob-storage.md), [DocumentDB](http://aka.ms/documentdb-search-indexer), [Base de datos SQL de Azure y SQL Server en máquinas virtuales de Azure](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers-2015-02-28.md).

La funcionalidad de indexador se expone en el [Portal de Azure](search-import-data-portal.md) así como en la [API de REST](https://msdn.microsoft.com/library/azure/dn946891.aspx).

<!---HONumber=AcomDC_0831_2016-->