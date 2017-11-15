---
title: "Solución de problemas de Spark mediante Azure HDInsight | Microsoft Docs"
description: "Obtenga respuestas a las preguntas comunes sobre cómo trabajar con Apache Spark y Azure HDInsight."
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
ms.date: 11/2/2017
ms.author: arijitt
ms.openlocfilehash: b75aca7d9f78cb425099cc33034b44a80d7b6b81
ms.sourcegitcommit: 3df3fcec9ac9e56a3f5282f6c65e5a9bc1b5ba22
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/04/2017
---
# <a name="troubleshoot-spark-by-using-azure-hdinsight"></a>Solución de problemas de Spark mediante Azure HDInsight

Obtenga información sobre los principales problemas y sus soluciones al trabajar con cargas útiles de Apache Spark en Apache Ambari.

## <a name="how-do-i-configure-a-spark-application-by-using-ambari-on-clusters"></a>¿Cómo se configura una aplicación de Spark a través de Ambari en clústeres?

### <a name="resolution-steps"></a>Pasos de la solución

Los valores de configuración de este procedimiento se establecieron anteriormente en HDInsight. Para determinar qué configuraciones de Spark deben establecerse y en qué valores, consulte [Qué produce una excepción OutofMemoryError en una aplicación Spark](#what-causes-a-spark-application-outofmemoryerror-exception). 

1. En la lista de clústeres, seleccione **Spark2**.

    ![Selección del clúster de la lista](./media/apache-troubleshoot-spark/update-config-1.png)

2. Seleccione la pestaña **Configs** (Configuraciones).

    ![Selección de la pestaña Configs (Configuraciones)](./media/apache-troubleshoot-spark/update-config-2.png)

3. En la lista de configuraciones, seleccione **Custom-spark2-defaults**.

    ![Selección de custom-spark-defaults](./media/apache-troubleshoot-spark/update-config-3.png)

4. Busque el valor de configuración que tiene que ajustar, por ejemplo **spark.executor.memory**. En este caso, el valor de **4608m** es demasiado alto.

    ![Seleccionar el campo spark.executor.memory](./media/apache-troubleshoot-spark/update-config-4.png)

5. Establezca el valor en la configuración recomendada. Se recomienda el valor de **2048m** para esta configuración.

    ![Cambiar el valor a 2048m](./media/apache-troubleshoot-spark/update-config-5.png)

6. Guarde el valor y, después, guarde la configuración. En la barra de herramientas, seleccione **Guardar**.

    ![Guardar el valor y la configuración](./media/apache-troubleshoot-spark/update-config-6a.png)

    Se le notificará si hay configuraciones que necesiten atención. Tenga en cuenta los elementos y, a continuación, seleccione **Proceed Anyway** (Continuar de todos modos). 

    ![Selección de Proceed Anyway (Continuar de todos modos)](./media/apache-troubleshoot-spark/update-config-6b.png)

    Escriba una nota sobre los cambios de configuración y, después, seleccione **Guardar**.

    ![Escribir una nota sobre los cambios realizados](./media/apache-troubleshoot-spark/update-config-6c.png)

7. Cada vez que se guarda una configuración, se le solicitará que reinicie el servicio. Seleccione **Reiniciar**.

    ![Selección de Reiniciar](./media/apache-troubleshoot-spark/update-config-7a.png)

    Confirme el reinicio.

    ![Selección de Confirm Restart All (Confirmar reinicio de todo)](./media/apache-troubleshoot-spark/update-config-7b.png)

    Puede revisar los procesos en ejecución.

    ![Revisar los procesos en ejecución](./media/apache-troubleshoot-spark/update-config-7c.png)

8. Puede agregar configuraciones. En la lista de configuraciones, seleccione **Custom-spark2-defaults** y, a continuación, seleccione **Agregar propiedad**.

    ![Selección de Agregar propiedad](./media/apache-troubleshoot-spark/update-config-8.png)

9. Defina una nueva propiedad. Puede definir una propiedad única mediante un cuadro de diálogo para valores específicos como el tipo de datos. O bien, puede definir varias propiedades mediante una definición por línea. 

    En este ejemplo, se define la propiedad **spark.driver.memory** con un valor de **4g**.

    ![Definir la nueva propiedad](./media/apache-troubleshoot-spark/update-config-9.png)

10. Guarde la configuración y, a continuación, reinicie el servicio, como se describe en los pasos 6 y 7.

Estos cambios son para todo el clúster, pero se pueden invalidar al enviar el trabajo de Spark.

### <a name="additional-reading"></a>Lecturas adicionales

[Envío de trabajos de Spark en clústeres de HDInsight](https://blogs.msdn.microsoft.com/azuredatalake/2017/01/06/spark-job-submission-on-hdinsight-101/)


## <a name="how-do-i-configure-a-spark-application-by-using-a-jupyter-notebook-on-clusters"></a>¿Cómo se configura una aplicación de Spark a través de un cuaderno de Jupyter Notebook en clústeres?

### <a name="resolution-steps"></a>Pasos de la solución

1. Para determinar qué configuraciones de Spark deben establecerse y en qué valores, consulte [Qué produce una excepción OutofMemoryError en una aplicación Spark](#what-causes-a-spark-application-outofmemoryerror-exception).

2. En la primera celda del cuaderno de Jupyter, después de la directiva **%%configure**, especifique las configuraciones de Spark en formato JSON válido. Cambie los valores reales según sea necesario:

    ![Agregar una configuración](./media/apache-troubleshoot-spark/add-configuration-cell.png)

### <a name="additional-reading"></a>Lecturas adicionales

[Envío de trabajos de Spark en clústeres de HDInsight](https://blogs.msdn.microsoft.com/azuredatalake/2017/01/06/spark-job-submission-on-hdinsight-101/)


## <a name="how-do-i-configure-a-spark-application-by-using-livy-on-clusters"></a>¿Cómo se configura una aplicación de Spark a través de LIVY en clústeres?

### <a name="resolution-steps"></a>Pasos de la solución

1. Para determinar qué configuraciones de Spark deben establecerse y en qué valores, consulte [Qué produce una excepción OutofMemoryError en una aplicación Spark](#what-causes-a-spark-application-outofmemoryerror-exception). 

2. Envíe la aplicación Spark a Livy mediante un cliente REST como cURL. Use un comando similar al siguiente. Cambie los valores reales según sea necesario:

    ```apache
    curl -k --user 'username:password' -v -H 'Content-Type: application/json' -X POST -d '{ "file":"wasb://container@storageaccountname.blob.core.windows.net/example/jars/sparkapplication.jar", "className":"com.microsoft.spark.application", "numExecutors":4, "executorMemory":"4g", "executorCores":2, "driverMemory":"8g", "driverCores":4}'  
    ```

### <a name="additional-reading"></a>Lecturas adicionales

[Envío de trabajos de Spark en clústeres de HDInsight](https://blogs.msdn.microsoft.com/azuredatalake/2017/01/06/spark-job-submission-on-hdinsight-101/)


## <a name="how-do-i-configure-a-spark-application-by-using-spark-submit-on-clusters"></a>¿Cómo se configura una aplicación de Spark a través de spark-submit en clústeres?

### <a name="resolution-steps"></a>Pasos de la solución

1. Para determinar qué configuraciones de Spark deben establecerse y en qué valores, consulte [Qué produce una excepción OutofMemoryError en una aplicación Spark](#what-causes-a-spark-application-outofmemoryerror-exception).

2. Inicie spark-shell mediante un comando similar al siguiente. Cambie el valor real de las configuraciones según sea necesario: 

    ```apache
    spark-submit --master yarn-cluster --class com.microsoft.spark.application --num-executors 4 --executor-memory 4g --executor-cores 2 --driver-memory 8g --driver-cores 4 /home/user/spark/sparkapplication.jar
    ```

### <a name="additional-reading"></a>Lecturas adicionales

[Envío de trabajos de Spark en clústeres de HDInsight](https://blogs.msdn.microsoft.com/azuredatalake/2017/01/06/spark-job-submission-on-hdinsight-101/)


## <a name="what-causes-a-spark-application-outofmemoryerror-exception"></a>¿Qué produce una excepción OutOfMemoryError en una aplicación Spark?

### <a name="detailed-description"></a>Descripción detallada

Se produce un error en la aplicación Spark, con los siguientes tipos de excepciones no detectadas:

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

### <a name="probable-cause"></a>Causa probable

La causa más probable de esta excepción es que no se asigna suficiente memoria del montón a las máquinas virtuales Java (JVM). Estas JVM se inician como ejecutores o controladores como parte de la aplicación Spark. 

### <a name="resolution-steps"></a>Pasos de la solución

1. Determine el tamaño máximo de los datos que la aplicación Spark controla. Puede realizar una estimación en función del tamaño máximo de los datos de entrada, los datos intermedios que se generan al transformar los datos de entrada y los datos de salida generados cuando la aplicación transforma aún más los datos intermedios. Este proceso puede ser iterativo si no puede realizar una estimación formal inicial. 

2. Asegúrese de que el clúster de HDInsight que va a usar tiene suficientes recursos, en términos de memoria, así como núcleos para acomodar la aplicación Spark. Puede determinarlo viendo en la sección Cluster Metrics (Métricas de clústeres) de la interfaz de usuario de YARN los valores de **Memory Used** (Memoria usada) frente a **Memory Total** (Total de memoria ) y **VCores Used** (VCores usados) frente a **VCores Total** (Total de VCores).

3. Establezca las siguientes configuraciones de Spark en los valores adecuados, que no deben superar el 90 % de la memoria y los núcleos disponibles. Los valores deben cumplir los requisitos de memoria de la aplicación Spark: 

    ```apache
    spark.executor.instances (Example: 8 for 8 executor count) 
    spark.executor.memory (Example: 4g for 4 GB) 
    spark.yarn.executor.memoryOverhead (Example: 384m for 384 MB) 
    spark.executor.cores (Example: 2 for 2 cores per executor) 
    spark.driver.memory (Example: 8g for 8GB) 
    spark.driver.cores (Example: 4 for 4 cores)   
    spark.yarn.driver.memoryOverhead (Example: 384m for 384MB) 
    ```

    Para calcular la memoria total utilizada por todos los ejecutores: 
    
    ```apache
    spark.executor.instances * (spark.executor.memory + spark.yarn.executor.memoryOverhead) 
    ```
   Para calcular la memoria total utilizada por el controlador:
    
    ```apache
    spark.driver.memory + spark.yarn.driver.memoryOverhead
    ```

### <a name="additional-reading"></a>Lecturas adicionales

- [Información general de la administración de memoria en Spark](http://spark.apache.org/docs/latest/tuning.html#memory-management-overview)
- [Debug a Spark application on an HDInsight cluster](https://blogs.msdn.microsoft.com/azuredatalake/2016/12/19/spark-debugging-101/) (Depuración de una aplicación Spark en un clúster de HDInsight)


### <a name="see-also"></a>Otras referencias
[Solución de problemas mediante Azure HDInsight](../../hdinsight/hdinsight-troubleshoot-guide.md)

