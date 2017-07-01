---
title: "Notas de la versión para los componentes de Hadoop en Azure HDInsight | Microsoft Docs"
description: "Últimas notas de la versión y versiones de los componentes de Hadoop para HDInsight de Azure. Obtenga sugerencias de desarrollo y detalles sobre Spark, R Server, Hive, etc."
services: hdinsight
documentationcenter: 
editor: cgronlun
manager: jhubbard
author: nitinme
tags: azure-portal
ms.assetid: a363e5f6-dd75-476a-87fa-46beb480c1fe
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/29/2017
ms.author: nitinme
ms.translationtype: Human Translation
ms.sourcegitcommit: afa23b1395b8275e72048bd47fffcf38f9dcd334
ms.openlocfilehash: 782cab231e1b152c720abebff1fc76ae0559d12a
ms.contentlocale: es-es
ms.lasthandoff: 05/12/2017


---
# <a name="release-notes-for-hadoop-components-on-azure-hdinsight"></a>Notas de la versión de los componentes de Hadoop en HDInsight de Azure

Este artículo proporciona información sobre las **últimas** actualizaciones de versión de Azure HDInsight. Para más información sobre versiones anteriores, vea [Notas de la versión (en archivo) de los componentes de Hadoop en Azure HDInsight](hdinsight-release-notes-archive.md).

> [!IMPORTANT]
> Linux es el único sistema operativo que se usa en la versión 3.4 de HDInsight, o en las superiores. Para obtener más información, consulte el [artículo de control de versiones de HDInsight](hdinsight-component-versioning.md).

## <a name="04062017---general-availability-of-hdinsight-36"></a>06/04/2017: Disponibilidad general de HDInsight 3.6

* En este lanzamiento, Azure HDInsight presenta la versión 3.6, que se basa en HDP 2.6. Las notas de la versión HDP 2.6 están disponibles [aquí](http://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.6.0/bk_release-notes/content/ch_relnotes.html) y se puede encontrar más información sobre las versiones de HDInsight [aquí](hdinsight-component-versioning.md). HDInsight 3.6 está disponible para las siguientes cargas de trabajo:

    * Hadoop v2.7.3
    * HBase v1.1.2
    * Storm v1.1.0
    * Spark v2.1.0
    * Interactive Hive v2.1.0

* **Compatibilidad con Hive View 2.0**. Esta permite mejorar la experiencia del usuario de Interactive Hive. Para más información, vea la [documentación de Hortonworks](http://docs.hortonworks.com/HDPDocuments/Ambari-2.5.0.3/bk_ambari-views/content/ch_using_hive_view.html).

* **Mejoras de rendimiento con Hive LLAP**. Consulte la [documentación de Hortonworks](https://hortonworks.com/blog/top-5-performance-boosters-with-apache-hive-llap/) para obtener más detalles.

* **Nuevas características en Hive**. Consulte la [documentación de Hortonworks](https://hortonworks.com/apache/hive/#section_4) para obtener más detalles.

* **Desuso de la CLI de Hive**: la CLI de Hive está en desuso y se recomienda a los clientes usar Beeline en su lugar. Para más información, consulte la [documentación de Apache](https://cwiki.apache.org/confluence/display/Hive/Replacing+the+Implementation+of+Hive+CLI+Using+Beeline). Para obtener instrucciones sobre cómo usar Beeline con HDInsight, consulte [Uso de Beeline con clústeres de HDInsight Hadoop](hdinsight-hadoop-use-hive-beeline.md).

* **Nuevas características de Apache Phoenix y HBase**.
    * Compatibilidad de cuota de almacenamiento: se usa habitualmente en entornos multiinquilino, lo que permite un espacio de almacenamiento limitado por tabla y por espacio de nombres.
    * Mejoras de indización de Phoenix: creación de índices incremental y recompilación o reanudación de la indización a partir de errores anteriores.
    * Herramienta de integridad de datos de Phoenix: admite la validación de esquemas, índices y otros metadatos.


* **Problema con HBase**: mientras se ejecuta un trabajo de MapReduce de carga masiva de archivos CSV, la sintaxis siguiente podría producir un error.

        HADOOP_CLASSPATH=$(hbase mapredcp):/path/to/hbase/conf hadoop jar phoenix-<version>-client.jar org.apache.phoenix.mapreduce.CsvBulkLoadTool --table EXAMPLE --input /data/example.csv

    Use esta sintaxis en su lugar:

        HADOOP_CLASSPATH=/path/to/hbase-protocol.jar:/path/to/hbase/conf hadoop jar phoenix-<version>-client.jar org.apache.phoenix.mapreduce.CsvBulkLoadTool --table EXAMPLE --input /data/example.csv


## <a name="02282017---release-of-spark-21-on-hdinsight-36-preview"></a>28/02/2017: lanzamiento de Spark 2.1 en HDInsight 3.6 (versión preliminar)
* [2.1 Spark](http://spark.apache.org/releases/spark-release-2-1-0.html) mejora muchos de los problemas de estabilidad y uso que había en las versiones anteriores. También ofrece características nuevas en todas las cargas de trabajo de Spark, como Spark Core, SQL, ML y Streaming.
* Se ha mejorado la escalabilidad del streaming estructurado y se ha agregado compatibilidad con las marcas de agua de horas de evento y el conector Kafka 0.10.
* Ahora se puede controlar la creación de particiones de Spark SQL mediante el nuevo mecanismo de control de particiones escalable. Consulte más información sobre cómo actualizar [aquí](http://spark.apache.org/releases/spark-release-2-1-0.html).
* En estos momentos, Spark 2.1 en HDInsight 3.6 (versión preliminar) de Azure no admite la conectividad de herramientas de BI con el controlador ODBC.
* En esta versión preliminar tampoco se puede acceder al almacén de Azure Data Lake Store desde los clústeres de Spark 2.1.


## <a name="11182016---release-of-spark-201-on-hdinsight-35"></a>18/11/2016: lanzamiento de Spark 2.0.1 en HDInsight 3.5
Spark 2.0.1 ahora está disponible en los clústeres Spark (HDInsight versión 3.5).

## <a name="11162016---release-of-r-server-90-on-hdinsight-35-spark-20"></a>16/11/2016 : lanzamiento de R Server 9.0 en HDInsight 3.5 (Spark 2.0)
*   Los clústeres de R Server incluyen ahora la opción de dos versiones: R Server 9.0 en HDI 3.5 (Spark 2.0) y R Server 8.0 en HDI 3.4 (Spark 1.6).
*   R Server 9.0 en HDI 3.5 (Spark 2.0) se basa en R 3.3.2 e incluye nuevas funciones de orígenes de datos ScaleR llamadas RxHiveData y RxParquetData para la carga de datos de Hive y Parquet directamente en DataFrames de Spark para el análisis por ScaleR. Para más información, consulte la Ayuda en pantalla sobre estas funciones en R por medio de los comandos **?RxHiveData** y **?RxParquetData**.
*   La edición de la comunidad de RStudio Server se instala ahora de forma predeterminada (con una opción de cancelación) en la hoja de configuración del clúster como parte del flujo de aprovisionamiento.

## <a name="11092016---release-of-spark-20-on-hdinsight"></a>09/11/2016: lanzamiento de Spark 2.0 en HDInsight
* Los clústeres de Spark 2.0 en HDInsight 3.5 ahora admiten servicios Livy y Jupyter.

## <a name="10262016---release-of-r-server-on-hdinsight"></a>26/10/2016: lanzamiento de R Server en HDInsight
* El URI para el acceso al nodo perimetral ha cambiado a **clustername**-ed-ssh.azurehdinsight.net
* Se ha optimizado R Server en el aprovisionamiento de clústeres de HDInsight.
* R Server en HDInsight ahora está disponible como un tipo de clúster R Server regular de HDInsight y ya no se instala como una aplicación independiente de HDInsight. El nodo perimetral y los archivos binarios de R Server ahora se suministran como parte de la implementación del clúster de R Server. Esto mejora la velocidad y la confiabilidad del aprovisionamiento. El modelo de precios para R Server se actualiza en consecuencia.
* El precio del tipo de clúster de R Server ahora se basa en el precio del nivel estándar más el suplemento de R Server. Ahora el nivel Premium se reserva para las características Premium disponibles en diferentes tipos de clúster y no se usa para el tipo de clúster de R Server. Este cambio no afecta al precio efectivo de R Server, únicamente cambia el modo en que los cargos aparecen reflejados en la factura. Todos los clústeres de R Server existentes seguirán funcionando y las plantillas de Resource Manager siguen siendo válidas hasta que se notifique el abandono de su uso. **En cambio, se recomienda actualizar las implementaciones con scripts de forma que usen la nueva plantilla de Resource Manager.**

## <a name="08302016---release-of-r-server-on-hdinsight"></a>30/08/2016: lanzamiento de R Server en HDInsight
Los números de versión completos de los clústeres de HDInsight basados en Linux implementados con esta versión son:

| HDI | Versión del clúster de HDI | HDP | Compilación de HDP | Compilación de Ambari |
| --- | --- | --- | --- | --- |
| 3.2 |3.2.1000.0.8268980 |2.2 |2.2.9.1-19 |2.2.1.12-4 |
| 3.3 |3.3.1000.0.8268980 |2.3 |2.3.3.1-25 |2.2.1.12-4 |
| 3.4 |3.4.1000.0.8269383 |2.4 |2.4.2.4-5 |2.2.1.12-4 |

Los números de versión completos de los clústeres de HDInsight basados en Windows implementados con esta versión son:

| HDI | Versión del clúster de HDI | HDP | Compilación de HDP |
| --- | --- | --- | --- |
| 2.1 |2.1.10.1033.2559206 |1.3 |1.3.12.0-01795 |
| 3.0 |3.0.6.1033.2559206 |2.0 |2.0.13.0-2117 |
| 3.1 |3.1.4.1033.2559206 |2.1 |2.1.16.0-2374 |
| 3.2 |3.2.7.1033.2559206 |2.2 |2.2.9.1-11 |
| 3.3 |3.3.0.1033.2559206 |2.3 |2.3.3.1-25 |






