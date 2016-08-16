<properties
	pageTitle="indexadores en Búsqueda de Azure | Microsoft Azure | Servicio de búsqueda hospedado en la nube"
	description="Rastree Base de datos SQL de Azure, DocumentDB o Almacenamiento de Azure para extraer los datos utilizables en búsquedas y rellenar un índice de Búsqueda de Azure."
	services="search"
	documentationCenter=""
	authors="HeidiSteen"
	manager="paulettm"
	editor=""
    tags="azure-portal"/>

<tags
	ms.service="search"
	ms.devlang="na"
	ms.workload="search"
	ms.topic="get-started-article"
	ms.tgt_pltfrm="na"
	ms.date="08/08/2016"
	ms.author="heidist"/>

# Indexadores de Búsqueda de Azure
> [AZURE.SELECTOR]
- [Información general](search-indexer-overview.md)
- [Portal](search-import-data-portal.md)
- [SQL de Azure](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers-2015-02-28.md)
- [DocumentDB](../documentdb/documentdb-search-indexer.md)
- [Almacenamiento de blobs (vista previa)](search-howto-indexing-azure-blob-storage.md)
- [Indexing Azure Table Storage with Azure Search](search-howto-indexing-azure-tables.md) (Indexación de Almacenamiento de tablas de Azure con Búsqueda de Azure)

Un **indexador** de Búsqueda de Azure es un rastreador (crawler) que extrae datos y metadatos utilizables en búsquedas de un origen de datos externo y rellena un índice basado en las asignaciones de un campo a otro entre el índice y su origen de datos. Este enfoque se denomina a veces "modelo de extracción", porque el servicio extrae datos sin que sea preciso escribir código que inserte datos en un índice.

Puede utilizar un indexador como único medio para la ingesta de datos. o bien utilizar una combinación de técnicas que incluyan el uso de un indexador para cargar solo algunos de los campos en el índice.

Puede ejecutar los indexadores a petición o con una programación de actualización periódica de datos que se ejecuta con una frecuencia de incluso cada quince minutos. Actualizaciones más frecuentes requieren un modelo de inserción que actualiza simultáneamente los datos en Búsqueda de Azure y su origen de datos externo.

## Enfoques para crear y administrar indexadores

Para los indexadores disponibles generalmente, como SQL Azure o DocumentDB, puede crearlos y administrarlos mediante estos enfoques:

- [Portal > Asistente para la importación de datos ](search-get-started-portal.md)
- [API de REST de servicio](https://msdn.microsoft.com/library/azure/dn946891.aspx)
- [.NET SDK](https://msdn.microsoft.com/library/azure/microsoft.azure.search.iindexersoperations.aspx)

Los indexadores en versión preliminar, como Almacenamiento de blobs o de tablas de Azure, requieren código y API en versión preliminar como la [API de REST de Búsqueda de Azure en versión preliminar para indexadores](search-api-indexers-2015-02-28-preview.md). Por lo general, las herramientas de portal no están disponibles para las características en versión preliminar.

## Pasos básicos de configuración

Los indexadores pueden ofrecer características que son exclusivas del origen de datos. En este sentido, algunos aspectos de la configuración de orígenes de datos o indexadores varían según el tipo de indexador. No obstante, todos los indexadores comparten composición básica y requisitos. Más adelante, se explican los pasos que son comunes a todos los indexadores.

### Paso 1: Creación de un índice

Un indexador automatizará algunas tareas relacionadas con la ingesta de datos, pero la creación de un índice no es una de ellas. Como requisito previo, debe tener un índice predefinido con campos que coincidan con los del origen de datos externo. Para más información sobre la estructura de un índice, consulte [Crear índice (API de REST de servicios de búsqueda de Azure)](https://msdn.microsoft.com/library/azure/dn798941.aspx).

### Paso 2: Creación de un origen de datos

Un indexador extrae datos de un **origen de datos** que contiene información, por ejemplo, una cadena de conexión. Actualmente se admiten los siguientes orígenes de datos:

- [Base de datos SQL de Azure o SQL Server en una máquina virtual de Azure](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers-2015-02-28.md)
- [DocumentDB](../documentdb/documentdb-search-indexer.md)
- [Almacenamiento de blobs de Azure (versión preliminar)](search-howto-indexing-azure-blob-storage.md), que se usa para extraer texto de PDF, documentos de Office, HTML o XML
- [Almacenamiento de tablas de Azure (versión preliminar)](search-howto-indexing-azure-tables.md)

Los orígenes de datos se configuran y administran independientemente de los indexadores que los usan, lo que significa que varios indexadores pueden usar un origen de datos para cargar más de un índice a la vez.

### Paso 3: Creación y programación del indexador

La definición del indexador es una construcción que especifica el índice, el origen de datos y una programación. Un indexador puede hacer referencia a un origen de datos desde otro servicio, siempre que ese origen de datos pertenezca a la misma suscripción. Para más información sobre la estructura de un indexador, consulte [Crear el indizador (API de REST de servicios de búsqueda de Azure)](https://msdn.microsoft.com/library/azure/dn946899.aspx).

## Pasos siguientes

Ahora que tiene el concepto básico, el paso siguiente consiste en revisar los requisitos y las tareas específicos de cada tipo de origen de datos.

- [Base de datos SQL de Azure o SQL Server en una máquina virtual de Azure](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers-2015-02-28.md)
- [DocumentDB](../documentdb/documentdb-search-indexer.md)
- [Almacenamiento de blobs de Azure (versión preliminar)](search-howto-indexing-azure-blob-storage.md), que se usa para extraer texto de PDF, documentos de Office, HTML o XML
- [Almacenamiento de tablas de Azure (versión preliminar)](search-howto-indexing-azure-tables.md)
- [Indexación de blobs CSV con el indexador de blobs de Búsqueda de Azure](search-howto-index-csv-blobs.md) (versión preliminar)
- [Indexación de blobs JSON con el indexador de blobs de Búsqueda de Azure](search-howto-index-json-blobs.md) (versión preliminar)

<!---HONumber=AcomDC_0810_2016-->