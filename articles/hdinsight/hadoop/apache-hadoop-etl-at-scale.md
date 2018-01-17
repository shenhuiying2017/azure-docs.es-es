---
title: "Extracción, transformación y carga de datos (ETL) a escala: Azure HDInsight | Microsoft Docs"
description: "Obtenga información acerca de cómo se usa ETL en HDInsight con Hadoop."
services: hdinsight
documentationcenter: 
author: ashishthaps
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: 
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 11/14/2017
ms.author: ashishth
ms.openlocfilehash: 47c2d129cb296f6387142e03b14356bcd83ad698
ms.sourcegitcommit: 562a537ed9b96c9116c504738414e5d8c0fd53b1
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/12/2018
---
# <a name="extract-transform-and-load-etl-at-scale"></a>Extracción, transformación y carga de datos (ETL) a escala

Extracción, transformación y carga de datos (ETL) es el proceso por el que se adquieren los datos desde varios orígenes, se recopilan en una ubicación estándar, se limpian y se procesan y, en última instancia, se cargan en un almacén de datos desde el que se pueden consultar. Los procesos ETL heredados importan los datos, los limpian localmente y, a continuación, los almacenan en un motor de datos relacionales. Con HDInsight, una gran variedad de componentes del ecosistema de Hadoop admiten la ejecución de ETL a escala. 

Esta canalización puede resumir el uso de HDInsight en el proceso ETL:

![Información general de ETL de HDInsight](./media/apache-hadoop-etl-at-scale/hdinsight-etl-at-scale-overview.png)

En las siguientes secciones se explica cada una de las fases de ETL y sus componentes asociados.

## <a name="orchestration"></a>Orquestación

La orquestación se expande a lo largo de todas las fases de la canalización de ETL. Los trabajos de ETL en HDInsight a menudo implican varios productos diferentes que funcionan con dependencia el uno del otro.  Puede usar Hive para limpiar alguna parte de los datos, mientras Pig limpia otra.  Puede usar Azure Data Factory para cargar datos en Azure SQL Database desde Azure Data Lake Store.

La orquestación es necesaria para ejecutar el trabajo pertinente en el momento adecuado.

### <a name="oozie"></a>Oozie

Apache Oozie es un sistema de coordinación de flujos de trabajo que administra trabajos de Hadoop. Oozie se ejecuta dentro de un clúster de HDInsight y se integra con la pila de Hadoop. Oozie es compatible con los trabajos de Hadoop para Apache MapReduce, Apache Pig, Apache Hive y Apache Sqoop. También puede usarse para programar trabajos específicos de un sistema, como scripts de shell o programas Java.

Para obtener más información, vea [Uso de Oozie con Hadoop para definir y ejecutar un flujo de trabajo en HDInsight](../hdinsight-use-oozie-linux-mac.md).

<!-- For a deep dive showing how to use Oozie to drive an end-to-end pipeline, see [Operationalize the Data Pipeline](hdinsight-operationalize-data-pipeline.md). -->

### <a name="azure-data-factory"></a>Azure Data Factory

Azure Data Factory proporciona funcionalidades de orquestación como plataforma como servicio. Se trata de un servicio de integración de datos basado en la nube que le permite crear flujos de trabajo controlados por datos en la nube a fin de coordinar y automatizar el movimiento y la transformación de los datos. 

Con Azure Data Factory, puede:

1. Crear y programar flujos de trabajo controlados por datos (denominados canalizaciones) que ingieren datos de distintos almacenes de datos.
2. Procesar y transformar los datos mediante servicios de proceso, como Azure HDInsight Hadoop, Spark, Azure Data Lake Analytics, Azure Batch y Azure Machine Learning.
3. Publicación de datos de salida en almacenes de datos como Azure SQL Data Warehouse para que las aplicaciones de inteligencia empresarial los consuman.

Para obtener más información sobre Azure Data Factory, consulte la [documentación](../../data-factory/introduction.md).

## <a name="ingest-file-storage-and-result-storage"></a>Ingesta de almacenamiento de archivos y de resultados

Normalmente, los archivos de datos de origen se cargan en una ubicación de Azure Storage o Azure Data Lake Store. Los archivos pueden estar en cualquier formato, pero normalmente son archivos planos como CSV. 

### <a name="azure-storage"></a>Azure Storage 

[Azure Storage](https://azure.microsoft.com/services/storage/blobs/) tiene [objetivos de escalabilidad específicos](../../storage/common/storage-scalability-targets.md).  Para los nodos más analíticos, Azure Storage se escala mejor cuando trabaja con muchos archivos pequeños.  Azure Storage garantiza el mismo rendimiento, independientemente de la cantidad o del tamaño de los archivos (siempre y cuando se encuentren dentro de sus límites).  Esto significa que puede almacenar terabytes de datos y seguir obteniendo un rendimiento coherente, tanto si usa un subconjunto de los datos como su totalidad.

Azure Storage tiene varios tipos diferentes de blobs.  Un *blob en anexos* es una opción ideal para almacenar los registros web o los datos del sensor.  

Se pueden distribuir varios blobs en muchos servidores para escalar horizontalmente el acceso a ellos, pero los blobs únicos solo pueden proporcionarse mediante servidores únicos. Aunque los blobs pueden agruparse lógicamente en contenedores de blob, esta agrupación no afecta a las particiones.

Azure Storage también tiene una capa de API de WebHDFS para el almacenamiento de blobs.  Todos los servicios de HDInsight pueden tener acceso a los archivos de Azure Blob Storage para la limpieza o el procesamiento de datos, de forma similar a cómo dichos servicios usan el Sistema de archivos distribuido de Hadoop (HDFS).

Normalmente, los datos se ingieren en Azure Storage mediante PowerShell, el SDK de Azure Storage o AZCopy.

### <a name="azure-data-lake-store"></a>Almacén de Azure Data Lake

Azure Data Lake Store (ADLS) es un repositorio de gran escala administrado para los datos de análisis que son compatibles con HDFS.  ADLS usa un paradigma de diseño que es similar a HDFS y ofrece una escalabilidad ilimitada en cuanto a la capacidad total y el tamaño de los archivos individuales. ADLS es excelente al trabajar con archivos grandes, ya que un archivo de gran tamaño se puede almacenar en varios nodos.  La creación de particiones de datos de ADLS se realiza en segundo plano.  Obtiene un rendimiento masivo para ejecutar trabajos de análisis con miles de ejecutores simultáneos que leen y escriben cientos de terabytes de datos con gran eficacia.

Los datos normalmente se ingieren en ADLS con Azure Data Factory, SDK de ADLS, AdlCopy Service, Apache DistCp o Apache Sqoop.  El uso de un servicio u otro depende en gran medida de dónde se encuentran dichos datos.  Si los datos están actualmente en un clúster de Hadoop existente, podría usar Apache DistCp, AdlCopy Service o Azure Data Factory.  Si se encuentra en Azure Blob Storage, puede usar el SDK de .NET de Azure Data Lake Store, Azure PowerShell o Azure Data Factory.

ADLS también está optimizado para la ingesta de eventos con Azure Event Hubs o Apache Storm.

#### <a name="considerations-for-both-storage-options"></a>Consideraciones para ambas opciones de almacenamiento

Para cargar los conjuntos de datos en el intervalo de terabytes, la latencia de red puede ser un problema serio, especialmente si los datos provienen de una ubicación local.  En tales casos, puede utilizar las opciones siguientes:

* Azure ExpressRoute, que permite crear conexiones privadas entre los centros de datos de Azure y su infraestructura local. Estas conexiones ofrecen una opción confiable para transferir grandes cantidades de datos. Para obtener más información, consulte la [documentación de Azure ExpressRoute](../../expressroute/expressroute-introduction.md).

* Carga "sin conexión" de los datos. Puede usar el [servicio Azure Import/Export](../../storage/common/storage-import-export-service.md) para enviar unidades de disco duro con sus datos a un centro de datos de Azure. Los datos se cargan primero a Blobs de Azure Storage. Después, puede usar [Azure Data Factory](../../data-factory/v1/data-factory-azure-datalake-connector.md) o la herramienta [AdlCopy](../../data-lake-store/data-lake-store-copy-data-azure-storage-blob.md) para copiar datos desde Azure Storage Blob a Data Lake Store.

### <a name="azure-sql-data-warehouse"></a>Azure SQL Data Warehouse

Azure SQL DW es una excelente opción para almacenar resultados limpios y preparados para futuros análisis.  Azure HDInsight puede utilizarse para realizar esos servicios para Azure SQL DW.

Azure SQL Data Warehouse (SQL DW) es un almacén de bases de datos relacionales optimizado para cargas de trabajo de análisis.  Azure SQL DW se escala según las tablas con particiones.  La tablas se pueden dividir en particiones entre varios nodos.  Los nodos de Azure SQL DW se seleccionan en el momento de la creación.  Se pueden escalar a posteriori, pero es un proceso activo que puede requerir el movimiento de datos. Para obtener más información, consulte [SQL Data Warehouse: administración del proceso](../../sql-data-warehouse/sql-data-warehouse-manage-compute-overview.md).

### <a name="hbase"></a>hbase

Apache HBase es un almacén de par clave-valor disponible en Azure HDInsight.  Apache HBase es una base de datos NoSQL de código abierto que se compila en Hadoop y se modela después de Google BigTable. HBase proporciona un acceso aleatorio y eficaz y una enorme coherencia para grandes cantidades de datos no estructurados y semiestructurados en una base de datos sin esquemas organizada por familias de columnas.

Los datos se almacenan en las filas de una tabla, mientras que los datos de una fila se agrupan por familia de columnas. HBase es una base de datos sin esquemas en el sentido de que no es preciso que ni las columnas ni el tipo de datos almacenados en ellas se definan antes de usarlos. El código abierto se escala linealmente para controlar petabytes de datos en miles de nodos. HBase puede basarse en la redundancia de datos, el procesamiento por lotes y otras características proporcionadas por aplicaciones distribuidas en el ecosistema Hadoop.   

Es un destino excelente para los datos del sensor y de registro para su posterior análisis.

La escalabilidad de HBase depende del número de nodos del clúster de HDInsight.

### <a name="azure-sql-database-and-azure-database"></a>Azure SQL Database y Azure Database

Azure ofrece tres bases de datos relacionales diferentes como plataforma como servicio (PaaS).

* [Azure SQL Database](../../sql-database/sql-database-technical-overview.md) es una implementación de Microsoft SQL Server. Para obtener más información sobre el rendimiento, consulte [Ajuste del rendimiento en Azure SQL Database](../../sql-database/sql-database-performance-guidance.md).
* [Azure Database for MySQL](../../mysql/overview.md) es una implementación de MySQL de Oracle.
* [Azure Database for PostgreSQL](../../postgresql/quickstart-create-server-database-portal.md) es una implementación de PostgreSQL.

Estos productos se escalan verticalmente, lo que significa que se escalan mediante la adición de más CPU y memoria.  También puede optar por usar los discos premium con los productos para mejorar el rendimiento de E/S.

## <a name="azure-analysis-services"></a>Azure Analysis Services 

Azure Analysis Services (AAS) es un motor de datos analíticos que se usa en la ayuda para la toma de decisiones y los análisis empresariales. Proporciona los datos analíticos para los informes empresariales y las aplicaciones cliente, como Power BI, Excel, informes de Reporting Services y otras herramientas de visualización de datos.

Para que los cubos de análisis se escalen, se deben cambiar los niveles de cada cubo individual.  Para obtener más información, consulte [Precios de Azure Analysis Services](https://azure.microsoft.com/pricing/details/analysis-services/).

## <a name="extract-and-load"></a>Extracción y carga

Una vez que los datos existan en Azure, puede usar muchos servicios para extraerlos y cargarlos en otros productos.  HDInsight admite Sqoop y Flume. 

### <a name="sqoop"></a>Sqoop

Apache Sqoop es una herramienta que se ha diseñado para transferir datos eficazmente entre orígenes de datos estructurados, semiestructurados y no estructurados. 

Sqoop usa MapReduce para importar y exportar los datos y para proporcionar tolerancia a errores y una operación paralela.

### <a name="flume"></a>Flume

Apache Flume es un servicio distribuido, confiable y disponible para recopilar, agregar y mover grandes cantidades de datos de registro de forma eficaz. Flume tiene una arquitectura simple y flexible basada en los flujos de datos de streaming. Flume es sólido y tolerante a errores con mecanismos de confiabilidad ajustables y numerosos mecanismos de conmutación por error y recuperación. Flume utiliza un modelo de datos extensible simple que permite la aplicación de análisis en línea.

No se puede usar Flume Apache con Azure HDInsight.  Una instalación de Hadoop local puede usar Flume para enviar datos a Azure Storage Blob o Azure Data Lake Store.  Para obtener más información, consulte [Using Apache Flume with HDInsight](https://blogs.msdn.microsoft.com/bigdatasupport/2014/03/18/using-apache-flume-with-hdinsight/) (Uso de Apache Flume con HDInsight).

## <a name="transform"></a>Transformar

Una vez que existan los datos en la ubicación elegida, deberá limpiarlos, combinarlos o prepararlos para un patrón de uso específico.  Hive, Pig y Spark SQL son buenas opciones para ese tipo de trabajo.  Todos se admiten en HDInsight. 

## <a name="next-steps"></a>pasos siguientes

* [Uso de Pig con Hadoop en HDInsight](hdinsight-use-pig.md)
<!-- * [Using Apache Hive as an ETL Tool](hdinsight-using-apache-hive-as-an-etl-tool.md) -->
