---
title: "Solución de problemas de Spark: Azure HDInsight | Microsoft Docs"
description: "Use las preguntas más frecuentes de Spark para obtener respuestas a preguntas comunes sobre Spark en la plataforma Azure HDInsight."
keywords: "Azure HDInsight, preguntas más frecuentes de Spark, guía de solución de problemas, problemas comunes, configuración de la aplicación, Ambari"
services: Azure HDInsight
documentationcenter: na
author: arijitt
manager: 
editor: 
ms.assetid: 25D89586-DE5B-4268-B5D5-CC2CE12207ED
ms.service: multiple
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 7/7/2017
ms.author: arijitt
ms.translationtype: HT
ms.sourcegitcommit: bde1bc7e140f9eb7bb864c1c0a1387b9da5d4d22
ms.openlocfilehash: 7655acd689c3f5b869eb0ddb5f186cff032ca8b6
ms.contentlocale: es-es
ms.lasthandoff: 07/21/2017

---

# <a name="spark-troubleshooting"></a>Solución de problemas de Spark

En este artículo se describen los principales problemas y sus soluciones para trabajar con cargas de Spark en Apache Ambari.

## <a name="how-do-i-configure-a-spark-application-through-ambari-on-clusters"></a>Cómo se configura una aplicación de Spark a través de Ambari en clústeres

### <a name="issue"></a>Problema:

Tiene que configurar en Ambari la cantidad de memoria y el número de núcleos que puede usar una aplicación de Spark.  

### <a name="resolution-steps"></a>Pasos de la solución: 

Los valores de configuración de este procedimiento se establecieron anteriormente en clústeres de HDInsight Spark. Consulte [¿Por qué se produjo un error OutOfMemoryError en mi aplicación de Spark?](#why-did-my-spark-application-fail-with-an-outofmemoryerror) para determinar qué configuraciones de Spark deben establecerse y en qué valores.

1. Elija **Spark2** en la lista de clústeres.

    ![Selección del clúster de la lista](media/hdinsight-troubleshoot-spark/update-config-1.png)

1. Seleccione la pestaña **Configs** (Configuraciones).

    ![Haga clic en la pestaña Configs (Configuraciones).](media/hdinsight-troubleshoot-spark/update-config-2.png)

1. En la lista de configuraciones, seleccione **Custom-spark2-defaults**.

    ![Selección de custom-spark-defaults](media/hdinsight-troubleshoot-spark/update-config-3.png)

1. Busque el valor de configuración que tiene que ajustar, por ejemplo **spark.executor.memory**. En este caso, el valor de 4608m es demasiado alto.

    ![Seleccionar el campo spark.executor.memory](media/hdinsight-troubleshoot-spark/update-config-4.png)

1. Establezca el valor como se recomienda. En este caso, se recomienda 2048 para esta configuración.

    ![Cambiar el valor a 2048m](media/hdinsight-troubleshoot-spark/update-config-5.png)

1. Guarde el valor de configuración y, después, guarde la configuración. 

    Haga clic en **Guardar** en la barra de herramientas.

    ![Guardar el valor y la configuración](media/hdinsight-troubleshoot-spark/update-config-6a.png)

    Se le notificará si hay configuraciones que necesiten atención. Tome nota de ellas y haga clic en **Proceed Anyway** (Continuar de todos modos). 

    ![Haga clic en Proceed Anyway](media/hdinsight-troubleshoot-spark/update-config-6b.png)

    Escriba una nota sobre los cambios de configuración y haga clic en **Guardar**.

    ![Escribir una nota sobre los cambios realizados](media/hdinsight-troubleshoot-spark/update-config-6c.png)

1. Cada vez que se guarda una configuración, se le solicitará que reinicie el servicio. Haga clic en **Restart**(Reiniciar).

    ![Hacer clic en Restart](media/hdinsight-troubleshoot-spark/update-config-7a.png)

    Confirme el reinicio.

    ![Hacer clic en Confirm Restart All](media/hdinsight-troubleshoot-spark/update-config-7b.png)

    Puede revisar los procesos en ejecución.

    ![Revisar los procesos en ejecución](media/hdinsight-troubleshoot-spark/update-config-7c.png)

1. También puede agregar configuraciones. En la lista de configuraciones, seleccione **Custom-spark2-defaults** como hizo en el paso 3 y, después, haga clic en **Add Property** (Agregar propiedad).

    ![Hacer clic en Add Property](media/hdinsight-troubleshoot-spark/update-config-8.png)

1. Defina una nueva propiedad. Puede definir una sola propiedad, con un cuadro de diálogo para un valor específico como el tipo de datos, o bien puede definir varias propiedades con una definición por línea. 

    En este ejemplo, se define la propiedad **spark.driver.memory** con un valor de 4g.

    ![Definir la nueva propiedad](media/hdinsight-troubleshoot-spark/update-config-9.png)

1. Guarde la configuración y reinicie el servicio, como se describe en los pasos 6 y 7.

Estos cambios son para todo el clúster, pero se pueden invalidar en el tiempo de envío de trabajos de Spark.

### <a name="further-reading"></a>Lecturas adicionales:

[Envío de trabajos de Spark en clústeres de HDInsight](https://blogs.msdn.microsoft.com/azuredatalake/2017/01/06/spark-job-submission-on-hdinsight-101/)


## <a name="how-do-i-configure-a-spark-application-through-a-jupyter-notebook-on-clusters"></a>Cómo se configura una aplicación de Spark a través de un cuaderno de Jupyter Notebook en clústeres

### <a name="issue"></a>Problema:

Tiene que configurar la cantidad de memoria y el número de núcleos que puede usar una aplicación Spark cuando se usa un cuaderno de Jupyter Notebook en clústeres de HDInsight. 

1. Consulte el tema [¿Por qué se produjo un error OutOfMemoryError en mi aplicación de Spark?](#spark-application-failure-outofmemory) para determinar qué configuraciones de Spark deben establecerse y en qué valores.
1.  Especifique las configuraciones de Spark en formato JSON válido en la primera celda del cuaderno de Jupyter Notebook después de la directiva %%configure (cambie los valores reales según corresponda): 

>![Agregar una configuración](media/hdinsight-troubleshoot-spark/add-configuration-cell.png)

### <a name="further-reading"></a>Lecturas adicionales:

[Envío de trabajos de Spark en clústeres de HDInsight](https://blogs.msdn.microsoft.com/azuredatalake/2017/01/06/spark-job-submission-on-hdinsight-101/)


## <a name="how-do-i-configure-a-spark-application-through-livy-on-clusters"></a>Cómo se configura una aplicación de Spark a través de LIVY en clústeres

### <a name="issue"></a>Problema:

Tiene que configurar en tiempo de envío a través de LIVY la cantidad de memoria y el número de núcleos que puede usar una aplicación Spark en clústeres de HDInsight. 

1. Consulte el tema [¿Por qué se produjo un error OutOfMemoryError en mi aplicación de Spark?](#spark-application-failure-outofmemory) para determinar qué configuraciones de Spark deben establecerse y en qué valores.
1. Envíe la aplicación Spark a LIVY mediante un cliente de REST como CURL con un comando similar al siguiente (cambie los valores reales según corresponda):

```apache
curl -k --user 'username:password' -v -H 'Content-Type: application/json' -X POST -d '{ "file":"wasb://container@storageaccountname.blob.core.windows.net/example/jars/sparkapplication.jar", "className":"com.microsoft.spark.application", "numExecutors":4, "executorMemory":"4g", "executorCores":2, "driverMemory":"8g", "driverCores":4}'  
```

### <a name="further-reading"></a>Lecturas adicionales:

[Envío de trabajos de Spark en clústeres de HDInsight](https://blogs.msdn.microsoft.com/azuredatalake/2017/01/06/spark-job-submission-on-hdinsight-101/)


## <a name="how-do-i-configure-a-spark-application-through-spark-submit-on-clusters"></a>Cómo se configura una aplicación de Spark a través de spark-submit en clústeres

### <a name="issue"></a>Problema: 

Tiene que configurar en tiempo de envío a través de spark-submit la cantidad de memoria y el número de núcleos que puede usar una aplicación Spark en clústeres de HDInsight.

1. Consulte el tema [¿Por qué se produjo un error OutOfMemoryError en mi aplicación de Spark?](#spark-application-failure-outofmemory) para determinar qué configuraciones de Spark deben establecerse y en qué valores.
1. Inicie el shell de Spark con un comando similar al siguiente (cambie el valor real de las configuraciones como corresponda): 

```apache
spark-submit --master yarn-cluster --class com.microsoft.spark.application --num-executors 4 --executor-memory 4g --executor-cores 2 --driver-memory 8g --driver-cores 4 /home/user/spark/sparkapplication.jar
```

### <a name="further-reading"></a>Lecturas adicionales:

[Envío de trabajos de Spark en clústeres de HDInsight](https://blogs.msdn.microsoft.com/azuredatalake/2017/01/06/spark-job-submission-on-hdinsight-101/)



## <a name="what-causes-a-spark-application-outofmemoryerror-exception"></a>Qué produce una excepción OutOfMemoryError en una aplicación Spark

### <a name="error"></a>Error:

Error de la aplicación Spark con la excepción OutOfMemoryError.

### <a name="detailed-description"></a>Descripción detallada:

Se produce un error en la aplicación Spark con los siguientes tipos de excepciones no detectadas.  
```apache
ERROR Executor: Exception in task 7.0 in stage 6.0 (TID 439) 

java.lang.OutOfMemoryError 
    at java.io.ByteArrayOutputStream.hugeCapacity(Unknown Source) 
    at java.io.ByteArrayOutputStream.grow(Unknown Source) 
    at java.io.ByteArrayOutputStream.ensureCapacity(Unknown Source) 
    at java.io.ByteArrayOutputStream.write(Unknown Source) 
    at java.io.ObjectOutputStream$BlockDataOutputStream.drain(Unknown Source) 
    at java.io.ObjectOutputStream$BlockDataOutputStream.setBlockDataMode(Unknown Source) 
    at java.io.ObjectOutputStream.writeObject0(Unknown Source) 
    at java.io.ObjectOutputStream.writeObject(Unknown Source) 
    at org.apache.spark.serializer.JavaSerializationStream.writeObject(JavaSerializer.scala:44) 
    at org.apache.spark.serializer.JavaSerializerInstance.serialize(JavaSerializer.scala:101) 
    at org.apache.spark.executor.Executor$TaskRunner.run(Executor.scala:239) 
    at java.util.concurrent.ThreadPoolExecutor.runWorker(Unknown Source) 
    at java.util.concurrent.ThreadPoolExecutor$Worker.run(Unknown Source) 
    at java.lang.Thread.run(Unknown Source) 
```

```apache
ERROR SparkUncaughtExceptionHandler: Uncaught exception in thread Thread[Executor task launch worker-0,5,main] 

java.lang.OutOfMemoryError 
    at java.io.ByteArrayOutputStream.hugeCapacity(Unknown Source) 
    at java.io.ByteArrayOutputStream.grow(Unknown Source) 
    at java.io.ByteArrayOutputStream.ensureCapacity(Unknown Source) 
    at java.io.ByteArrayOutputStream.write(Unknown Source) 
    at java.io.ObjectOutputStream$BlockDataOutputStream.drain(Unknown Source) 
    at java.io.ObjectOutputStream$BlockDataOutputStream.setBlockDataMode(Unknown Source) 
    at java.io.ObjectOutputStream.writeObject0(Unknown Source) 
    at java.io.ObjectOutputStream.writeObject(Unknown Source) 
    at org.apache.spark.serializer.JavaSerializationStream.writeObject(JavaSerializer.scala:44) 
    at org.apache.spark.serializer.JavaSerializerInstance.serialize(JavaSerializer.scala:101) 
    at org.apache.spark.executor.Executor$TaskRunner.run(Executor.scala:239) 
    at java.util.concurrent.ThreadPoolExecutor.runWorker(Unknown Source) 
    at java.util.concurrent.ThreadPoolExecutor$Worker.run(Unknown Source) 
    at java.lang.Thread.run(Unknown Source) 
```

### <a name="probable-cause"></a>Causa probable:

La causa más probable de esta excepción es que no hay suficiente memoria del montón asignada a la máquina virtual Java (JVM) que se inicie como ejecutor o controlador como parte de la aplicación Spark. 

### <a name="resolution-steps"></a>Pasos de la solución:

1. Determine el tamaño máximo de los datos que la aplicación Spark va a controlar. Se puede realizar una estimación en función del tamaño máximo de los datos de entrada, los datos intermedios que se generan al transformar los datos de entrada y los datos de salida generados al transformar aún más los datos intermedios. Este también puede ser un proceso iterativo si no es posible una estimación inicial formal. 
1. Asegúrese de que el clúster de HDInsight que se va a usar tiene suficientes recursos, en términos de memoria, así como núcleos para acomodar la aplicación Spark. Para determinarlo, hay que ver en la sección Cluster Metrics (Métricas de clústeres) de la interfaz de usuario de YARN del clúster los valores de Memory Used (Memoria usada) frente a Memory Total (Total de memoria ) y VCores Used (VCores usados) frente a VCores Total (Total de VCores).

1. Establezca las configuraciones de Spark siguientes en los valores adecuados que no superen el 90 % de la memoria disponible y los núcleos que ve YARN que cumplan los requisitos de memoria de la aplicación Spark: 

```apache
spark.executor.instances (Example: 8 for 8 executor count) 
spark.executor.memory (Example: 4g for 4 GB) 
spark.yarn.executor.memoryOverhead (Example: 384m for 384 MB) 
spark.executor.cores (Example: 2 for 2 cores per executor) 
spark.driver.memory (Example: 8g for 8GB) 
spark.driver.cores (Example: 4 for 4 cores)   
spark.yarn.driver.memoryOverhead (Example: 384m for 384MB) 
```

Memoria total usada por todos los ejecutores = 
```apache
spark.executor.instances * (spark.executor.memory + spark.yarn.executor.memoryOverhead) 
```
Memoria total usada por el controlador = 
```apache
spark.driver.memory + spark.yarn.driver.memoryOverhead
```

### <a name="further-reading"></a>Lecturas adicionales:

- [Información general de la administración de memoria en Spark](http://spark.apache.org/docs/latest/tuning.html#memory-management-overview)
- [Depuración de aplicaciones Spark en clústeres de HDInsight](https://blogs.msdn.microsoft.com/azuredatalake/2016/12/19/spark-debugging-101/)










