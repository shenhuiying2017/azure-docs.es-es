<properties
	pageTitle="indexadores en Búsqueda de Azure | Microsoft Azure | Servicio de búsqueda hospedado en la nube"
	description="Rastree Base de datos SQL de Azure, DocumentDB o Almacenamiento de Azure para extraer los datos utilizables en búsquedas y rellenar un índice de Búsqueda de Azure."
	services="search"
	documentationCenter=""
	authors="HeidiSteen"
	manager="mblythe"
	editor=""
    tags="azure-portal"/>

<tags
	ms.service="search"
	ms.devlang="na"
	ms.workload="search"
	ms.topic="get-started-article"
	ms.tgt_pltfrm="na"
	ms.date="04/14/2016"
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

Los indexadores se pueden ejecutar a petición o en una programación de actualización periódica de datos que se ejecuta cada quince minutos. Actualizaciones más frecuentes requieren un modelo de inserción que actualiza simultáneamente los datos en Búsqueda de Azure y su origen de datos externo.

Un indexador extrae datos de un **origen de datos** que contiene información, por ejemplo, una cadena de conexión. Actualmente se admiten los siguientes orígenes de datos:

- [Conexión de Base de datos SQL de Azure a Búsqueda de Azure con indexadores](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers-2015-02-28.md) (o SQL Server en máquinas virtuales de Azure)
- [DocumentDB](../documentdb/documentdb-search-indexer.md)
- [Indexación de documentos en Almacenamiento de blobs de Azure con Búsqueda de Azure](search-howto-indexing-azure-blob-storage.md) (actualmente en versión preliminar). Extrae texto de PDF, documentos de Office, HTML y XML).
- [Indexing Azure Table Storage with Azure Search](search-howto-indexing-azure-tables.md) (Indexación de Almacenamiento de tablas de Azure con Búsqueda de Azure) (actualmente en versión preliminar).

Los orígenes de datos se configuran y administran independientemente de los indexadores que los usan, lo que significa que varios indexadores pueden usar un origen de datos para cargar más de un índice a la vez.

Tanto el [SDK de .NET](https://msdn.microsoft.com/library/azure/microsoft.azure.search.iindexersoperations.aspx) como la [API de REST del servicio](https://msdn.microsoft.com/library/azure/dn946891.aspx) admiten la administración de indexadores y orígenes de datos.

Como alternativa, también puede configurar un indexador en el Portal cuando use el Asistente para la **importación de datos**. Diríjase a [Introducción a Búsqueda de Azure en el Portal](search-get-started-portal) para consultar un tutorial rápido que utiliza datos de ejemplo y el indexador de DocumentDB para crear y cargar un índice mediante el asistente.

<!---HONumber=AcomDC_0420_2016-->