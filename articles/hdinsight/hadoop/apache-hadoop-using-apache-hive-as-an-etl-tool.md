---
title: 'Uso de Apache Hive como una herramienta ETL: Azure HDInsight | Microsoft Docs'
description: "Use Apache Hive para la extracción, transformación y carga de datos (ETL) de Azure HDInsight."
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
ms.openlocfilehash: 1ccbfe23e9c887a98a0dbfa8031078a15c6e41b6
ms.sourcegitcommit: 562a537ed9b96c9116c504738414e5d8c0fd53b1
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/12/2018
---
# <a name="use-apache-hive-as-an-extract-transform-and-load-etl-tool"></a>Uso de Apache Hive como una herramienta de extracción, transformación y carga de datos (ETL)

Normalmente, deberá limpiar y transformar los datos entrantes antes de cargarlos en un destino adecuado para el análisis. Las operaciones de extracción, transformación y carga de datos (ETL) se utilizan para preparar los datos y cargarlos en un destino de datos.  Hive en HDInsight puede leer los datos no estructurados, procesarlos según sea necesario y, a continuación, cargarlos en un almacén de datos relacionales para los sistema de ayuda a la toma de decisiones. En este enfoque, se extraen los datos desde el origen y se almacenan en el almacenamiento escalable, como Azure Storage Blob o Azure Data Lake Store. A continuación, los datos se transforman mediante una secuencia de consultas de Hive y, finalmente, se almacenan provisionalmente en Hive para prepararse para la carga masiva en el almacén de datos de destino.

## <a name="use-case-and-model-overview"></a>Introducción del modelo y del caso de uso

En la siguiente ilustración se muestra una introducción del caso de uso y el modelo de automatización de ETL. Los datos de entrada se transforman para generar el resultado adecuado.  Durante la transformación, los datos pueden cambiar de forma, tipo e incluso de lenguaje.  Los procesos ETL pueden convertir las unidades del sistema imperial al métrico, cambiar las zonas horarias y mejorar la precisión a fin de alinearse correctamente con los datos existentes en el destino.  Los procesos ETL también pueden combinar los nuevos datos con los datos existentes para mantener los informes actualizados, o para proporcionar más información sobre los datos existentes.  A continuación, las aplicaciones, como los servicios y las herramientas de informes, pueden consumir estos datos en el formato deseado.

![Apache Hive como ETL](./media/apache-hadoop-using-apache-hive-as-an-etl-tool/hdinsight-etl-architecture.png)

Hadoop se utiliza normalmente en los procesos ETL que importan un gran número de archivos de texto (por ejemplo, CSV), un número más pequeño pero a menudo cambiante de archivos de texto, o ambos.  Hive es una herramienta excelente que se usa para preparar los datos antes de cargarlos en su destino.  Permite crear un esquema del archivo CSV y usar un lenguaje similar a SQL para generar programas de MapReduce que interactúan con los datos. 

Los pasos típicos para usar Hive para ejecutar ETL son los siguientes:

1. Cargue los datos a Azure Data Lake Store o Azure Blob Storage.
2. Cree una base de datos de Repositorio de metadatos (con Azure SQL Database) para que la use Hive en el almacenamiento de los esquemas.
3. Cree un clúster de HDInsight y conecte el almacén de datos.
4. Defina el esquema para que se aplique en tiempo de lectura en los datos del almacén de datos:

    ```
    DROP TABLE IF EXISTS hvac;

    --create the hvac table on comma-separated sensor data stored in Azure Storage blobs
    
    CREATE EXTERNAL TABLE hvac(`date` STRING, time STRING, targettemp BIGINT,
        actualtemp BIGINT, 
        system BIGINT, 
        systemage BIGINT, 
        buildingid BIGINT)
    ROW FORMAT DELIMITED FIELDS TERMINATED BY ',' 
    STORED AS TEXTFILE LOCATION 'wasb://{container}@{storageaccount}.blob.core.windows.net/HdiSamples/SensorSampleData/hvac/';
    ```

5. Transforme los datos y cárguelos en el destino.  Hay varias maneras de usar Hive durante la transformación y la carga:

    * Consulte y prepare los datos con Hive y guárdelos como CSV en Azure Data Lake Store o Azure Blob Storage.  A continuación, use una herramienta como SQL Server Integration Services (SSIS) para adquirir esos archivos CSV y cargar los datos en una base de datos relacional de destino, como SQL Server.
    * Consulte los datos directamente desde Excel o C# utilizando el controlador ODBC de Hive.
    * Use [Apache Sqoop](apache-hadoop-use-sqoop-mac-linux.md) para leer los archivos CSV planos preparados y cárguelos en la base de datos relacional de destino.

## <a name="data-sources"></a>Orígenes de datos

Los orígenes de datos normalmente son datos externos que pueden asociarse a los datos existentes en el almacén de datos, por ejemplo:

* Datos de medios sociales, archivos de registro, sensores y aplicaciones que generan archivos de datos.
* Conjuntos de datos obtenidos de proveedores de datos, como estadísticas de tiempo o cifras de ventas de un proveedor.
* Datos de streaming capturados, filtrados y procesados a través de una herramienta o marco de trabajo adecuados.

<!-- TODO: (see Collecting and loading data into HDInsight). -->

## <a name="output-targets"></a>Destinos de salida

Puede usar Hive para enviar los datos a varios destinos como:

* Una base de datos relacional, como SQL Server o Azure SQL Database.
* Un almacenamiento de datos, como Azure SQL Data Warehouse.
* Excel.
* Almacenamiento de tabla y blob de Azure.
* Aplicaciones o servicios que requieren el procesamiento de los datos en formatos específicos o como archivos que contengan tipos específicos de la estructura de información.
* Almacén de documentos JSON como <a href="https://azure.microsoft.com/services/cosmos-db/">CosmosDB</a>.

## <a name="considerations"></a>Consideraciones

El modelo ETL se utiliza normalmente cuando quiere:

* Cargar datos de flujo o grandes volúmenes de datos estructurados o semiestructurados desde orígenes externos en una base de datos o un sistema de información existentes.
* Limpiar, transformar y validar los datos antes de cargarlos, quizás usando más de una transformación a través del clúster.
* Generar informes y visualizaciones que se actualicen periódicamente.  Por ejemplo, si el informe tarda demasiado tiempo en generarse durante el día, puede programarlo para que se ejecute durante la noche.  Puede usar Azure Scheduler y PowerShell para ejecutar automáticamente una consulta de Hive.

Si el destino de los datos no es una base de datos, puede generar un archivo con el formato adecuado dentro de la consulta, por ejemplo, un CSV. Luego, este archivo se puede importar a Excel o Power BI.

Si necesita ejecutar varias operaciones en los datos como parte del proceso ETL, tenga en cuenta cómo los administra. Si las operaciones se controlan mediante un programa externo, en lugar de como un flujo de trabajo dentro de la solución, debe decidir si algunas operaciones se pueden ejecutar en paralelo y detectar cuándo se completa cada trabajo. Usar un mecanismo de flujo de trabajo como Oozie en Hadoop puede ser más sencillo que intentar crear una secuencia de operaciones mediante scripts externos o programas personalizados. Para obtener más información acerca de Oozie, consulte [Workflow and job orchestration](https://msdn.microsoft.com/library/dn749829.aspx) (Orquestación del trabajo y el flujo de trabajo).

<!-- ## Next steps -->
<!-- * [ETL at scale](../hdinsight-etl-at-scale.md): Learn more about performing ETL at scale. -->
<!-- * [Operationalize Data Pipelines with Oozie](hdinsight-operationalize-data-pipeline.md): Learn how to build a data pipeline that uses Hive to summarize CSV flight delay data, stage the prepared data in Azure Storage blobs, and then use Sqoop to load the summarized data into Azure SQL Database. -->
<!-- * [ETL Deep Dive](../hdinsight-etl-deep-dive.md): Walk through an end-to-end ETL pipeline.  -->
