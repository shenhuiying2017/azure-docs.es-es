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
ms.date: 08/04/2017
ms.author: nitinme
ms.openlocfilehash: 96b2b4976729da5b7d8b75909dbe099090240c08
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="release-notes-for-hadoop-components-on-azure-hdinsight"></a>Notas de la versión de los componentes de Hadoop en HDInsight de Azure

Este artículo proporciona información sobre las **últimas** actualizaciones de versión de Azure HDInsight. Para más información sobre versiones anteriores, vea [Notas de la versión (en archivo) de los componentes de Hadoop en Azure HDInsight](hdinsight-release-notes-archive.md).

> [!IMPORTANT]
> Linux es el único sistema operativo que se usa en la versión 3.4 de HDInsight, o en las superiores. Para obtener más información, consulte el [artículo de control de versiones de HDInsight](hdinsight-component-versioning.md).


## <a name="notes-for-08012017-release-of-hdinsight"></a>Notas de la versión del 01/08/2017 de HDInsight

| Título | Descripción | Área afectada  | Tipo de clúster  | 
| --- | --- | --- | --- | --- |
| Lanzamiento de Microsoft R Server 9.1 en HDInsight |HDInsight ahora admite el aprovisionamiento de clústeres de R Server 9.1 en HDInsight. Para más información sobre la versión Microsoft R Server 9.1, vea [este blog](https://blogs.technet.microsoft.com/dataplatforminsider/2017/04/19/introducing-microsoft-r-server-9-1-release/). |Servicio |R Server |
| HDInsight 3.6 ahora incluye las últimas versiones de la pila de Hadoop|<ul><li>Para obtener una lista detallada de las versiones actualizadas, vea [Componentes de Hadoop disponibles con las distintas versiones de HDInsight](hdinsight-component-versioning.md#hadoop-components-available-with-different-hdinsight-versions).</li><li>Para obtener una lista de errores corregidos en las últimas versiones de la pila de Hadoop, vea [Apache Patch Information](https://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.6.1/bk_release-notes/content/patch_parent.html) (Información sobre la revisión de Apache).</li><li>Para obtener una lista de los principales cambios entre HDP 2.6.1 (que ahora está disponible en HDInsight 3.6), vea [https://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.6.1/bk_release-notes/content/behavior_changes.html](https://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.6.1/bk_release-notes/content/behavior_changes.html).</li><li>Para obtener una lista de los problemas conocidos de HDP 2.6.1, vea [Known issues](https://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.6.1/bk_release-notes/content/known_issues.html) (Problemas conocidos).</li></ul> |Servicio |Todo |N/D |
| Actualizaciones de clústeres de Hive interactivo (versión preliminar) |<ul><li><b>Mejora de características.</b> Implementación de Metastore en caché que reduce la carga en el back-end de SQL mediante el almacenamiento en caché de los metadatos y mejora el rendimiento de todas las operaciones de metadatos.  Esta mejora ahora es una opción predeterminada en todas los clústeres de Hive interactivo. Para más información, vea [https://issues.apache.org/jira/browse/HIVE-16520](https://issues.apache.org/jira/browse/HIVE-16520).</li><li><b>Mejora de características.</b> Se optimiza la carga dinámica de partición. Para más información, vea [https://issues.apache.org/jira/browse/HIVE-14204] (https://issues.apache.org/jira/browse/HIVE-14204).</li><li><b>Mejora de características.</b> Optimizaciones de configuración de HDInsight en Linux.</li><li><b>Corrección de errores.</b> `CredentialProviderFactory$getProviders` no es seguro para subprocesos. Este problema está corregido. Para más información, vea [https://issues.apache.org/jira/browse/HADOOP-14195](https://issues.apache.org/jira/browse/HADOOP-14195).</li><li><b>Corrección de errores.</b> Uso elevado de la CPU con la API `liststatus` del controlador de WASB, que resulta en un rendimiento incorrecto de ATS. Este problema está corregido. Para más información, vea [https://github.com/Azure/azure-storage-java/pull/154](https://github.com/Azure/azure-storage-java/pull/154).</li></ul> |Servicio |Interactive Hive (versión preliminar) |
| Actualizaciones de los clústeres de Hadoop |Se mejora la confiabilidad de la operación de trabajo de Templeton. Para más información, vea [https://issues.apache.org/jira/browse/HIVE-15947](https://issues.apache.org/jira/browse/HIVE-15947). |Servicio |Hadoop |
| Actualizaciones de YARN | HDInsight ahora crea una base de datos de Ambari de 250 GB (sin aumentar el costo), que mejora la experiencia de los clientes. Este cambio debe impedir que se llene ATS, para ofrecer la posibilidad de mejorar el rendimiento. |Servicio |Todo |
| Actualizaciones de Spark | Versión de Spark 2.1.1. Para más información, vea [Notas de la versión de Spark 2.1.1](https://spark.apache.org/releases/spark-release-2-1-1.html). | Servicio | Spark |

  



## <a name="04062017---general-availability-of-hdinsight-36"></a>06/04/2017: Disponibilidad general de HDInsight 3.6

* En este lanzamiento, Azure HDInsight presenta la versión 3.6, que se basa en HDP 2.6. Las notas de la versión HDP 2.6 están disponibles [aquí](http://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.6.0/bk_release-notes/content/ch_relnotes.html) y se puede encontrar más información sobre las versiones de HDInsight [aquí](hdinsight-component-versioning.md). HDInsight 3.6 está disponible para las siguientes cargas de trabajo:

    * Hadoop v2.7.3
    * HBase v1.1.2
    * Storm v1.1.0
    * Spark v2.1.0
    * Interactive Hive v2.1.0

* **Compatibilidad con Hive View 2.0**. Esta permite mejorar la experiencia del usuario de Interactive Hive. Para más información, vea la [documentación de Hortonworks](http://docs.hortonworks.com/HDPDocuments/Ambari-2.5.0.3/bk_ambari-views/content/ch_using_hive_view.html).

* **Mejoras de rendimiento con Hive LLAP**. Para más información, vea la [documentación de Hortonworks](https://hortonworks.com/blog/top-5-performance-boosters-with-apache-hive-llap/).

* **Nuevas características en Hive**. Vea la [documentación de Hortonworks](https://hortonworks.com/apache/hive/#section_4).

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






