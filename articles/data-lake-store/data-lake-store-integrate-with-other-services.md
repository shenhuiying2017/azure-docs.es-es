---
title: "Integración de Data Lake Store con otros servicios de Azure | Microsoft Docs"
description: "Descripción de cómo se integra el Almacén de Data Lake con otros servicios de Azure"
documentationcenter: 
services: data-lake-store
author: nitinme
manager: jhubbard
editor: cgronlun
ms.assetid: 48a5d1f4-3850-4c22-bbc4-6d1d394fba8a
ms.service: data-lake-store
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 08/28/2017
ms.author: nitinme
ms.translationtype: HT
ms.sourcegitcommit: c3a2462b4ce4e1410a670624bcbcec26fd51b811
ms.openlocfilehash: de7aff6b31d937576da65498c5fcce2ae9abdbf1
ms.contentlocale: es-es
ms.lasthandoff: 09/25/2017

---
# <a name="integrating-data-lake-store-with-other-azure-services"></a>Integración del Almacén de Data Lake con otros servicios de Azure
El Almacén de Azure Data Lake puede usarse junto con otros servicios de Azure para habilitar una gama más amplia de escenarios. En el siguiente artículo se enumeran los servicios con los que el Almacén de Data Lake puede integrarse.

## <a name="use-data-lake-store-with-azure-hdinsight"></a>Uso del Almacén de Data Lake con Azure HDInsight
Puede aprovisionar un clúster [HDInsight de Azure](https://azure.microsoft.com/documentation/learning-paths/hdinsight-self-guided-hadoop-training/) que use el Almacén de Data Lake como almacenamiento compatible con HDFS. Para esta versión, para los clústeres Hadoop y Storm en Windows y Linux, puede usar el Almacén de Data Lake solo como almacenamiento adicional. Dichos clústeres todavía usan el Almacenamiento de Azure (WASB) como almacenamiento predeterminado. Sin embargo, para los clústeres de HBase en Windows y Linux, puede usar el Almacén de Data Lake como almacenamiento predeterminado, como almacenamiento adicional o ambos.

Para obtener instrucciones sobre cómo aprovisionar un clúster de HDInsight con el Almacén de Data Lake, consulte:

* [Aprovisionamiento de un clúster de HDInsight con el Almacén de Data Lake mediante el Portal de Azure](data-lake-store-hdinsight-hadoop-use-portal.md)
* [Aprovisionamiento de un clúster de HDInsight con Data Lake Store como almacenamiento predeterminado mediante Azure PowerShell](data-lake-store-hdinsight-hadoop-use-powershell-for-default-storage.md)
* [Aprovisionamiento de un clúster de HDInsight con Data Lake Store como almacenamiento adicional mediante Azure PowerShell](data-lake-store-hdinsight-hadoop-use-powershell.md)

## <a name="use-data-lake-store-with-azure-data-lake-analytics"></a>Uso del Almacén de Data Lake con Análisis de Data Lake de Azure
[Análisis de Azure Data Lake](../data-lake-analytics/data-lake-analytics-overview.md) le permite trabajar con macrodatos a una escala de nube. Dinámicamente aprovisiona recursos y le permite realizar análisis en terabytes o incluso exabytes de datos que pueden almacenarse en un número de orígenes de datos admitidos, siendo uno de ellos el Almacén de Data Lake. Análisis de Data Lake está especialmente optimizado para trabajar con el Almacén de Azure Data Lake: proporciona el nivel máximo de rendimiento, el procesamiento y la ejecución en paralelo para las cargas de trabajo de macrodatos.

Para obtener instrucciones sobre cómo usar el Análisis de Data Lake con el Almacén de Data Lake, consulte [Introducción a Análisis de Data Lake con el Almacén de Data Lake](../data-lake-analytics/data-lake-analytics-get-started-portal.md).

## <a name="use-data-lake-store-with-azure-data-factory"></a>Uso del Almacén de Data Lake con Factoría de datos de Azure
Puede usar [Factoría de datos de Azure](https://azure.microsoft.com/services/data-factory/) para recopilar datos de tablas de Azure, Base de datos SQL de Azure, Almacenamiento de datos SQL de Azure, blobs de Almacenamiento de Azure y bases de datos locales. Al ser considerado un elemento de primera clase en el ecosistema de Azure, Factoría de datos de Azure puede usarse para orquestar la recopilación de datos desde esos orígenes al Almacén de Azure Data Lake.

Para obtener instrucciones sobre cómo usar Factoría de datos de Azure con el Almacén de Data Lake, consulte [Desplazamiento de datos hacia y desde el Almacén de Data Lake mediante Factoría de datos](../data-factory/connector-azure-data-lake-store.md).

## <a name="copy-data-from-azure-storage-blobs-into-data-lake-store"></a>Copia de datos de los blobs de Almacenamiento de Azure en el Almacén de Data Lake
El Almacén de Azure Data Lake proporciona una herramienta de línea de comandos, AdlCopy, que le permite copiar datos desde el Almacenamiento de blobs de Azure en una cuenta de Almacén de Data Lake. Para más información, consulte [Copia de datos de los blobs de Almacenamiento de Azure en el Almacén de Data Lake](data-lake-store-copy-data-azure-storage-blob.md).

## <a name="copy-data-between-azure-sql-database-and-data-lake-store"></a>Copia de datos entre Base de datos SQL de Azure y Almacén de Data Lake
Puede usar Apache Sqoop para importar y exportar datos entre Base de datos SQL y Almacén de Data Lake. Para más información, consulte [Copia de datos entre Almacén de Data Lake y Base de datos SQL de Azure mediante Sqoop](data-lake-store-data-transfer-sql-sqoop.md).

## <a name="use-data-lake-store-with-stream-analytics"></a>Uso del Almacén de Data Lake con Análisis de transmisiones
Puede utilizar Almacén de Data Lake como una de las salidas para almacenar los datos que se transmiten mediante Análisis de transmisiones de Azure. Para más información, consulte [Transmisión de datos del blob de Almacenamiento de Azure al Almacén de Data Lake mediante el Análisis de transmisiones](data-lake-store-stream-analytics.md).

## <a name="use-data-lake-store-with-power-bi"></a>Uso del Almacén de Data Lake con Power BI
Puede usar Power BI para importar datos desde una cuenta de Almacén de Data Lake para analizar y visualizar los datos. Para más información, consulte [Análisis de datos en Almacén de Data Lake mediante Power BI](data-lake-store-power-bi.md).

## <a name="use-data-lake-store-with-data-catalog"></a>Uso de Almacén de Data Lake con Catálogo de datos
Puede registrar los datos desde Almacén de Data Lake al Catálogo de datos de Azure para que los datos se puedan reconocer en toda la organización. Para más información, consulte [Registro de datos del Almacén de Data Lake en el Catálogo de datos de Azure](data-lake-store-with-data-catalog.md).

## <a name="use-data-lake-store-with-sql-server-integration-services-ssis"></a>Uso de Data Lake Store con SQL Server Integration Services (SSIS)
Puede usar el Administrador de conexiones de Azure Data Lake Store en SSIS para conectar un paquete de SSIS a Azure Data Lake Store. Para más información, consulte [Uso de Data Lake Store con SSIS](https://docs.microsoft.com/sql/integration-services/connection-manager/azure-data-lake-store-connection-manager).

## <a name="use-data-lake-store-with-sql-data-warehouse"></a>Uso de Data Lake Store con SQL Data Warehouse
Puede usar PolyBase para cargar datos de Azure Data Lake Store en SQL Data Warehouse. Para más información, consulte [Uso de Data Lake Store con SQL Data Warehouse](../sql-data-warehouse/sql-data-warehouse-load-from-azure-data-lake-store.md).

## <a name="use-data-lake-store-with-azure-event-hubs"></a>Uso de Data Lake Store con Azure Event Hubs
Puede usar Azure Data Lake Store para archivar y capturar datos recibidos por Azure Event Hubs. Para más información, vea [Uso de Data Lake Store con Azure Event Hubs](data-lake-store-archive-eventhub-capture.md).

## <a name="see-also"></a>Otras referencias
* [Información general del Almacén de Azure Data Lake](data-lake-store-overview.md)
* [Introducción al Almacén de Data Lake mediante el Portal](data-lake-store-get-started-portal.md)
* [Introducción al Almacén de Data Lake mediante PowerShell](data-lake-store-get-started-powershell.md)  


