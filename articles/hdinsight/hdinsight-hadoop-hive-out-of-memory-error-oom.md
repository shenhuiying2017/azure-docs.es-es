---
title: "Corrección de un error de memoria insuficiente de Hive en Azure HDInsight | Microsoft Docs"
description: "Corrija un error de memoria insuficiente de Hive en HDInsight. El escenario de cliente es una consulta entre numerosas tablas de gran tamaño."
keywords: "error de memoria insuficiente, OOM, configuración de Hive"
services: hdinsight
documentationcenter: 
author: mumian
manager: jhubbard
editor: cgronlun
ms.assetid: 7bce3dff-9825-4fa0-a568-c52a9f7d1dad
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 11/06/2017
ms.author: jgao
ms.openlocfilehash: 3149147a95ac6e12b8866fbd03dd159de97c43cb
ms.sourcegitcommit: 295ec94e3332d3e0a8704c1b848913672f7467c8
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/06/2017
---
# <a name="fix-a-hive-out-of-memory-error-in-azure-hdinsight"></a>Corrección de un error de memoria insuficiente de Hive en Azure HDInsight

Obtenga información acerca de cómo corregir un error de memoria insuficiente cuando procese tablas de gran tamaño configurando ajustes de memoria de Hive.

## <a name="run-hive-query-against-large-tables"></a>Ejecución de una consulta de Hive en tablas de gran tamaño

Un cliente ejecutó una consulta de Hive:

    SELECT
        COUNT (T1.COLUMN1) as DisplayColumn1,
        …
        …
        ….
    FROM
        TABLE1 T1,
        TABLE2 T2,
        TABLE3 T3,
        TABLE5 T4,
        TABLE6 T5,
        TABLE7 T6
    where (T1.KEY1 = T2.KEY1….
        …
        …

Algunos matices de esta consulta:

* T1 es un alias para una tabla de gran tamaño, TABLE1, que cuenta con numerosos tipos de columna de CADENA.
* Otras tablas no son tan grandes, pero tienen un gran número de columnas.
* Todas las tablas se unen entre sí, en algunos casos con varias columnas en TABLE1, etc.

La consulta de Hive tardó 26 minutos en completarse en un clúster de HDInsigh A3 de 24 nodos. El cliente ha observado los siguientes mensajes de advertencia:

    Warning: Map Join MAPJOIN[428][bigTable=?] in task 'Stage-21:MAPRED' is a cross product
    Warning: Shuffle Join JOIN[8][tables = [t1933775, t1932766]] in Stage 'Stage-4:MAPRED' is a cross product

Mediante el motor de ejecución de Tez. La misma consulta se ejecutó durante 15 minutos y se produjo el error siguiente:

    Status: Failed
    Vertex failed, vertexName=Map 5, vertexId=vertex_1443634917922_0008_1_05, diagnostics=[Task failed, taskId=task_1443634917922_0008_1_05_000006, diagnostics=[TaskAttempt 0 failed, info=[Error: Failure while running task:java.lang.RuntimeException: java.lang.OutOfMemoryError: Java heap space
        at
    org.apache.hadoop.hive.ql.exec.tez.TezProcessor.initializeAndRunProcessor(TezProcessor.java:172)
        at org.apache.hadoop.hive.ql.exec.tez.TezProcessor.run(TezProcessor.java:138)
        at
    org.apache.tez.runtime.LogicalIOProcessorRuntimeTask.run(LogicalIOProcessorRuntimeTask.java:324)
        at
    org.apache.tez.runtime.task.TezTaskRunner$TaskRunnerCallable$1.run(TezTaskRunner.java:176)
        at
    org.apache.tez.runtime.task.TezTaskRunner$TaskRunnerCallable$1.run(TezTaskRunner.java:168)
        at java.security.AccessController.doPrivileged(Native Method)
        at javax.security.auth.Subject.doAs(Subject.java:415)
        at org.apache.hadoop.security.UserGroupInformation.doAs(UserGroupInformation.java:1628)
        at
    org.apache.tez.runtime.task.TezTaskRunner$TaskRunnerCallable.call(TezTaskRunner.java:168)
        at
    org.apache.tez.runtime.task.TezTaskRunner$TaskRunnerCallable.call(TezTaskRunner.java:163)
        at java.util.concurrent.FutureTask.run(FutureTask.java:262)
        at java.util.concurrent.ThreadPoolExecutor.runWorker(ThreadPoolExecutor.java:1145)
        at java.util.concurrent.ThreadPoolExecutor$Worker.run(ThreadPoolExecutor.java:615)
        at java.lang.Thread.run(Thread.java:745)
    Caused by: java.lang.OutOfMemoryError: Java heap space

El error se sigue produciendo cuando se usa una máquina virtual más grande (por ejemplo, D12).


## <a name="debug-the-out-of-memory-error"></a>Depuración del error de memoria insuficiente

Nuestros equipos de soporte técnico e ingeniería localizaron uno de los problemas que hicieron que el error de memoria insuficiente fuera un [problema conocido descrito en Apache JIRA](https://issues.apache.org/jira/browse/HIVE-8306):

    When hive.auto.convert.join.noconditionaltask = true we check noconditionaltask.size and if the sum  of tables sizes in the map join is less than noconditionaltask.size the plan would generate a Map join, the issue with this is that the calculation doesnt take into account the overhead introduced by different HashTable implementation as results if the sum of input sizes is smaller than the noconditionaltask size by a small margin queries will hit OOM.

La propiedad **hive.auto.convert.join.noconditionaltask** del archivo hive-site.xml se estableció en **true**:

    <property>
        <name>hive.auto.convert.join.noconditionaltask</name>
        <value>true</value>
        <description>
              Whether Hive enables the optimization about converting common join into mapjoin based on the input file size.
              If this parameter is on, and the sum of size for n-1 of the tables/partitions for a n-way join is smaller than the
              specified size, the join is directly converted to a mapjoin (there is no conditional task).
        </description>
      </property>

Es probable que la combinación de la asignación provocara el error de memoria insuficiente de montón de Java. Como se explica en la entrada de blog [Configuración de memoria de Hadoop Yarn en HDInsight](http://blogs.msdn.com/b/shanyu/archive/2014/07/31/hadoop-yarn-memory-settings-in-hdinsigh.aspx), cuando se usa el motor de ejecución de Tez, el espacio de montón utilizado pertenece en realidad al contenedor de Tez. Consulte la siguiente imagen, que describe la memoria del contenedor de Tez.

![Diagrama de memoria del contenedor de Tez: error de memoria insuficiente de Hive](./media/hdinsight-hadoop-hive-out-of-memory-error-oom/hive-out-of-memory-error-oom-tez-container-memory.png)

Como sugiere la entrada de blog, los dos valores de configuración de memoria siguientes definen la memoria del contenedor del montón: **hive.tez.container.size** y **hive.tez.java.opts**. En nuestra experiencia, la excepción de memoria insuficiente no significa que el tamaño del contenedor sea demasiado pequeño. Significa que el tamaño del montón de Java (hive.tez.java.opts) es demasiado pequeño. Por tanto, siempre que vea memoria insuficiente, puede intentar aumentar **hive.tez.java.opts**. Si es necesario, es posible que deba aumentar **hive.tez.container.size**. El valor de **java.opts** debe ser aproximadamente el 80 % de **container.size**.

> [!NOTE]
> El valor de **hive.tez.java.opts** siempre debe ser menor que **hive.tez.container.size**.
> 
> 

Como una máquina D12 tiene 28 GB de memoria, se optó por usar un tamaño de contenedor de 10 GB (10 240 MB) y asignar el 80 % a java.opts:

    SET hive.tez.container.size=10240
    SET hive.tez.java.opts=-Xmx8192m

Con estos nuevos valores, la consulta se ejecutó correctamente en menos de 10 minutos.

## <a name="next-steps"></a>Pasos siguientes

Recibir un error de memoria insuficiente no significa necesariamente que el tamaño del contenedor sea demasiado pequeño. En su lugar, debe configurar las opciones de memoria para que aumente el tamaño del montón y sea de al menos el 80% del tamaño de memoria del contenedor. Para optimizar las consultas de Hive, vea [Optimizar consultas de Hive para Hadoop en HDInsight](hdinsight-hadoop-optimize-hive-query.md).