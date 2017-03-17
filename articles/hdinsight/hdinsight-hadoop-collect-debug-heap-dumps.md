---
title: Depurar y analizar servicios de Hadoop con volcados de memoria | Microsoft Docs
description: "Recopile automáticamente volcados de memoria para servicios de Hadoop y coloque dentro de la cuenta de almacenamiento de blobs de Azure para depuración y análisis."
services: hdinsight
documentationcenter: 
tags: azure-portal
author: mumian
manager: jhubbard
editor: cgronlun
ms.assetid: e4ec4ebb-fd32-4668-8382-f956581485c4
ms.service: hdinsight
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/06/2017
ms.author: jgao
ROBOTS: NOINDEX
translationtype: Human Translation
ms.sourcegitcommit: 6407c371bc51461a05429fabaf38d3f9bc80d32c
ms.openlocfilehash: b6830f422b914722b8381a69c73cf8b02e107115
ms.lasthandoff: 02/07/2017


---
# <a name="collect-heap-dumps-in-blob-storage-to-debug-and-analyze-hadoop-services"></a>Recopilar volcados de memoria en el almacenamiento de blobs para depurar y analizar servicios de Hadoop
[!INCLUDE [heapdump-selector](../../includes/hdinsight-selector-heap-dump.md)]

Los volcados de montón contienen una instantánea de la memoria de la aplicación, incluidos los valores de variables en el momento en el que se creó el volcado de memoria. Por ello, estos volcados son realmente útiles a la hora de diagnosticar cualquier problema que hubiera ocurrido durante el tiempo de ejecución. Los volcados de memoria pueden recopilarse automáticamente para servicios Hadoop y se colocan en la cuenta de almacenamiento de blobs de Azure de un usuario en HDInsightHeapDumps/. 

La recopilación de volcados de memoria para los distintos servicios debe habilitarse para los servicios en clústeres individuales. De forma predeterminada, esta característica está desactivada para un clúster. Los volcados de memoria pueden ser de gran tamaño, por lo que se recomienda supervisar la cuenta de almacenamiento de blobs en la que se van a guardar tras habilitar la recopilación.

> [!IMPORTANT]
> Linux es el único sistema operativo que se usa en la versión 3.4 de HDInsight, o en las superiores. Para más información, consulte [El contrato de nivel de servicio para las versiones de clúster de HDInsight](hdinsight-component-versioning.md#hdi-version-32-and-33-nearing-deprecation-date). La información de este artículo solo se aplica al HDInsight basado en Windows. Para obtener más información sobre el HDInsight basado en Linux, consulte [Habilitar volcados de memoria para servicios de Hadoop en el HDInsight basado en Linux](hdinsight-hadoop-collect-debug-heap-dump-linux.md)


## <a name="eligible-services-for-heap-dumps"></a>Servicios de volcados de memoria aptos
Puede habilitar los volcados de montón en los siguientes servicios:

* **hcatalog** - tempelton
* **hive** - hiveserver2, metastore, derbyserver
* **mapreduce** - jobhistoryserver
* **yarn** - resourcemanager, nodemanager, timelineserver
* **hdfs** - datanode, secondarynamenode, namenode

## <a name="configuration-elements-that-enable-heap-dumps"></a>Elementos de configuración que habilitan los volcados de memoria
Para activar el volcado de memoria para un servicio, el usuario deberá definir los elementos de configuración adecuados en la sección de dicho servicio, que se especifica mediante **nombre_servicio**.

    "javaargs.<service_name>.XX:+HeapDumpOnOutOfMemoryError" = "-XX:+HeapDumpOnOutOfMemoryError",
    "javaargs.<service_name>.XX:HeapDumpPath" = "-XX:HeapDumpPath=c:\Dumps\<service_name>_%date:~4,2%%date:~7,2%%date:~10,2%%time:~0,2%%time:~3,2%%time:~6,2%.hprof"

El valor de **nombre_servicio** puede ser cualquiera de los servicios enumerados anteriormente: tempelton, hiveserver2, metastore, derbyserver, jobhistoryserver, resourcemanager, nodemanager, timelineserver, datanode, secondarynamenode o namenode.

## <a name="enable-using-azure-powershell"></a>Habilitar con Azure PowerShell
Por ejemplo, para activar los volcados de memoria mediante Azure PowerShell para jobhistoryserver, el usuario debe hacer lo siguiente:

[!INCLUDE [upgrade-powershell](../../includes/hdinsight-use-latest-powershell.md)]

    $MapRedConfigValues = new-object 'Microsoft.WindowsAzure.Management.HDInsight.Cmdlet.DataObjects.AzureHDInsightMapReduceConfiguration'

    $MapRedConfigValues.Configuration = @{ "javaargs.jobhistoryserver.XX:+HeapDumpOnOutOfMemoryError"="-XX:+HeapDumpOnOutOfMemoryError" ; "javaargs.jobhistoryserver.XX:HeapDumpPath" = "-XX:HeapDumpPath=c:\\Dumps\\jobhistoryserver_%date:~4,2%_%date:~7,2%_%date:~10,2%_%time:~0,2%_%time:~3,2%_%time:~6,2%.hprof" }

## <a name="enable-using-net-sdk"></a>Habilitar con SDK para .NET
Por ejemplo, para activar los volcados de memoria mediante el SDK de .NET de HDInsight de Azure para jobhistoryserver, el usuario debe hacer lo siguiente:

    clusterInfo.MapReduceConfiguration.ConfigurationCollection.Add(new KeyValuePair<string, string>("javaargs.jobhistoryserver.XX:+HeapDumpOnOutOfMemoryError", "-XX:+HeapDumpOnOutOfMemoryError"));

    clusterInfo.MapReduceConfiguration.ConfigurationCollection.Add(new KeyValuePair<string, string>("javaargs.jobhistoryserver.XX:HeapDumpPath", "-XX:HeapDumpPath=c:\\Dumps\\jobhistoryserver_%date:~4,2%_%date:~7,2%_%date:~10,2%_%time:~0,2%_%time:~3,2%_%time:~6,2%.hprof"));

