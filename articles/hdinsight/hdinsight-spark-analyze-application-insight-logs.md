---
title: Análisis de registros de telemetría de Application Insights con Spark en HDInsight | Microsoft Docs
description: Obtenga información sobre cómo exportar registros de Application Insight para almacenamiento de blobs y analizar los registros con Spark en HDInsight.
services: hdinsight
documentationcenter: ''
author: Blackmist
manager: jhubbard
editor: cgronlun

ms.service: hdinsight
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 08/15/2016
ms.author: larryfr

---
# Análisis de registros de telemetría de Application Insights con Spark en HDInsight
[Application Insights de Visual Studio](../application-insights/app-insights-overview.md) es un servicio de análisis que supervisa sus aplicaciones web. Los datos de telemetría que genera Application Insights pueden exportarse al Almacenamiento de Azure para que HDInsight los analice.

En este documento, aprenderá a usar HDInsight para analizar los datos de telemetría de Application Insight con Apache Spark.

## Requisitos previos
* Una suscripción de Azure.
* Una aplicación configurada para usar Application Insights.
* Experiencia en la creación de un clúster de HDInsight basado en Linux. Si no está familiarizado con la creación de un clúster, consulte [Creación de Spark en HDInsight](hdinsight-apache-spark-jupyter-spark-sql.md) para obtener más información.
  
  > [!NOTE]
  > Este documento no es un tutorial para crear un nuevo clúster. En su lugar, hace referencia a otros documentos que proporcionan información sobre cómo crear un clúster que tenga acceso a los datos de telemetría.
  > 
  > 
* Un navegador web. Se utiliza para ejecutar análisis interactivamente mediante un cuaderno de Jupyter Notebook.

Se ha utilizado lo siguiente para el desarrollo y pruebas de este documento:

* Los datos de telemetría de Application Insights se han generado mediante una [aplicación web Node.js configurada para usar Application Insights](../application-insights/app-insights-nodejs.md).
* Se usó un clúster de Spark en HDInsight versión 3.4 basado en Linux para analizar los datos.

## Arquitectura y planeación
El siguiente diagrama muestra la arquitectura de servicio de este ejemplo:

![diagrama que muestra los datos que fluyen desde Application Insights al almacenamiento de blobs y que a continuación son procesados por Spark en HDInsight](./media/hdinsight-spark-analyze-application-insight-logs/appinsightshdinsight.png)

### Almacenamiento de Azure
Un clúster de HDInsight puede acceder directamente a blobs en bloques de una cuenta de Almacenamiento de Azure, y Application Insights puede configurarse para exportar continuamente información de telemetría a blobs de Almacenamiento de Azure. Sin embargo, hay algunos requisitos que se deben seguir:

* **Ubicación**: la cuenta de almacenamiento debe encontrarse en la misma región que HDInsight. Esto reduce la latencia al acceder a los datos y evita los cargos de salida que se producen cuando se mueven datos entre regiones.
* **Tipo de blob**: HDInsight solo es compatible con blobs en bloques. Application Insights usa blobs en bloques de forma predeterminada, por lo que debería funcionar con HDInsight sin ninguna configuración adicional.
* **Permisos de acceso**: si utiliza la misma cuenta de almacenamiento para la exportación continua de Application Insights y el almacenamiento predeterminado de HDInsight, HDInsight tendrá acceso completo a los datos de telemetría de Application Insights. Esto significa que se pueden eliminar los datos de telemetría desde el clúster de HDInsight.
  
    En su lugar, se recomienda utilizar cuentas de almacenamiento independientes para HDInsight y los datos de telemetría de Application Insights, y [usar firmas de acceso compartido (SAS) para restringir el acceso a los datos desde HDInsight](hdinsight-storage-sharedaccesssignature-permissions.md). Usar SAS permite conceder a HDInsight acceso de solo lectura a los datos de telemetría.

### Esquema de datos
Application Insights proporciona información del [modelo de datos de exportación](../application-insights/app-insights-export-data-model.md) para el formato de datos de telemetría exportados a blobs. Los pasos descritos en este documento usan Spark SQL para interactuar con los datos. Spark SQL puede generar automáticamente un esquema para la estructura de datos JSON registrado por Application Insights, por lo que no es necesario definir manualmente el esquema al realizar el análisis.

## Exportación de datos de telemetría
Siga los pasos de [Configuración de exportación continua](../application-insights/app-insights-export-telemetry.md) para configurar Application Insights de modo que la información de telemetría se exporte a un blob de Almacenamiento de Azure.

## Configuración de HDInsight para tener acceso a los datos
Utilice la información de [Uso de firmas de acceso compartido de Almacenamiento de Azure para restringir el acceso a datos con HDInsight](hdinsight-storage-sharedaccesssignature-permissions.md) para crear una SAS para el contenedor de blobs que contenga los datos de telemetría exportados. Las SAS deben proporcionar acceso de solo lectura a los datos.

El documento de firma de acceso compartido proporciona información sobre cómo puede agregarse almacenamiento SAS a un clúster existente de HDInsight basado en Linux. También se proporciona información sobre cómo agregarlo al crear un nuevo clúster de HDInsight.

## Análisis de datos mediante Python (PySpark)
1. Desde el [Portal de Azure](https://portal.azure.com), seleccione el clúster de Spark en HDInsight. En la sección **Vínculos rápidos**, seleccione **Paneles de clúster** y, a continuación, seleccione **Jupyter Notebook** en la hoja de Paneles de clúster\_\_.
   
    ![Los paneles de clúster](./media/hdinsight-spark-analyze-application-insight-logs/clusterdashboards.png)
2. En la esquina superior derecha de la página de Jupyter, seleccione **Nuevo** y, a continuación, **PySpark**. Se abrirá una nueva pestaña en el explorador con un cuaderno de Jupyter Notebook basado en Python.
3. En el primer campo (llamado una **celda**) de la página, escriba lo siguiente:
   
        sc._jsc.hadoopConfiguration().set('mapreduce.input.fileinputformat.input.dir.recursive', 'true')
   
    Esto permite a Spark acceder de forma recursiva a la estructura de directorios de los datos de entrada. La telemetría de Application Insights se registra en una estructura de directorios similar a la siguiente:
   
        /{telemetry type}/YYYY-MM-DD/{##}/
4. Utilice **MAYÚS + INTRO** para ejecutar el código. En el lado izquierdo de la celda, aparece un asterisco entre corchetes para indicar que se está ejecutando el código en esta celda. Una vez finalizado, el asterisco cambiará a un número y se mostrará un resultado similar al siguiente debajo de la celda:
   
        Creating SparkContext as 'sc'
   
        ID    YARN Application ID    Kind    State    Spark UI    Driver log    Current session?
        3    application_1468969497124_0001    pyspark    idle    Link    Link    ✔
   
        Creating HiveContext as 'sqlContext'
        SparkContext and HiveContext created. Executing user code ...
5. Se habrá creado una nueva celda debajo de la primera. Escriba lo siguiente en la nueva celda. Reemplace **CONTAINER** y **STORAGEACCOUNT** con el nombre de la cuenta de Almacenamiento de Azure y el nombre del contenedor de blobs utilizado al configurar la exportación continua de Application Insights.
   
        %%bash
        hdfs dfs -ls wasb://CONTAINER@STORAGEACCOUNT.blob.core.windows.net/
   
    Utilice **MAYÚS + INTRO** para ejecutar esta celda. Verá un resultado similar al siguiente:
   
        Found 1 items
        drwxrwxrwx   -          0 1970-01-01 00:00 wasb://appinsights@contosostore.blob.core.windows.net/contosoappinsights_2bededa61bc741fbdee6b556571a4831
   
    La ruta de acceso de WASB devuelta es la ubicación de los datos de telemetría de Application Insights. Cambie la línea `hdfs dfs -ls` en la celda para usar la ruta wasb devuelta y, a continuación, use **MAYÚS + INTRO** para volver a ejecutar la celda. Esta vez, los resultados deberían mostrar los directorios que contienen datos de telemetría.
   
   > [!NOTE]
   > Para el resto de los pasos descritos en esta sección, se ha usado el directorio `wasb://appinsights@contosostore.blob.core.windows.net/contosoappinsights_{ID}/Requests`. Este directorio podría no existir, a menos que los datos de telemetría sean de una aplicación web. Si se usan datos de telemetría que no incluyan un directorio de solicitudes, seleccione otro directorio y ajuste el resto de los pasos para utilizar ese directorio y el esquema para los datos almacenados en él.
   > 
   > 
6. Escriba lo siguiente en la nueva celda. Reemplace **WASB\_PATH** con la ruta de acceso del paso anterior.
   
        jsonFiles = sc.textFile('WASB_PATH')
        jsonData = sqlContext.read.json(jsonFiles)
   
    Esto crea una nueva trama de datos de los archivos JSON exportados por el proceso de exportación continua. Utilice **MAYÚS + INTRO** para ejecutar esta celda.
7. En la celda siguiente, escriba y ejecute lo siguiente para ver el esquema creado por Spark para los archivos JSON:
   
        jsonData.printSchema()
   
    El esquema de cada tipo de telemetría será diferente. El siguiente es el esquema que se genera para las solicitudes web (datos almacenados en el subdirectorio `Requests`):
   
        root
        |-- context: struct (nullable = true)
        |    |-- application: struct (nullable = true)
        |    |    |-- version: string (nullable = true)
        |    |-- custom: struct (nullable = true)
        |    |    |-- dimensions: array (nullable = true)
        |    |    |    |-- element: string (containsNull = true)
        |    |    |-- metrics: array (nullable = true)
        |    |    |    |-- element: string (containsNull = true)
        |    |-- data: struct (nullable = true)
        |    |    |-- eventTime: string (nullable = true)
        |    |    |-- isSynthetic: boolean (nullable = true)
        |    |    |-- samplingRate: double (nullable = true)
        |    |    |-- syntheticSource: string (nullable = true)
        |    |-- device: struct (nullable = true)
        |    |    |-- browser: string (nullable = true)
        |    |    |-- browserVersion: string (nullable = true)
        |    |    |-- deviceModel: string (nullable = true)
        |    |    |-- deviceName: string (nullable = true)
        |    |    |-- id: string (nullable = true)
        |    |    |-- osVersion: string (nullable = true)
        |    |    |-- type: string (nullable = true)
        |    |-- location: struct (nullable = true)
        |    |    |-- city: string (nullable = true)
        |    |    |-- clientip: string (nullable = true)
        |    |    |-- continent: string (nullable = true)
        |    |    |-- country: string (nullable = true)
        |    |    |-- province: string (nullable = true)
        |    |-- operation: struct (nullable = true)
        |    |    |-- name: string (nullable = true)
        |    |-- session: struct (nullable = true)
        |    |    |-- id: string (nullable = true)
        |    |    |-- isFirst: boolean (nullable = true)
        |    |-- user: struct (nullable = true)
        |    |    |-- anonId: string (nullable = true)
        |    |    |-- isAuthenticated: boolean (nullable = true)
        |-- internal: struct (nullable = true)
        |    |-- data: struct (nullable = true)
        |    |    |-- documentVersion: string (nullable = true)
        |    |    |-- id: string (nullable = true)
        |-- request: array (nullable = true)
        |    |-- element: struct (containsNull = true)
        |    |    |-- count: long (nullable = true)
        |    |    |-- durationMetric: struct (nullable = true)
        |    |    |    |-- count: double (nullable = true)
        |    |    |    |-- max: double (nullable = true)
        |    |    |    |-- min: double (nullable = true)
        |    |    |    |-- sampledValue: double (nullable = true)
        |    |    |    |-- stdDev: double (nullable = true)
        |    |    |    |-- value: double (nullable = true)
        |    |    |-- id: string (nullable = true)
        |    |    |-- name: string (nullable = true)
        |    |    |-- responseCode: long (nullable = true)
        |    |    |-- success: boolean (nullable = true)
        |    |    |-- url: string (nullable = true)
        |    |    |-- urlData: struct (nullable = true)
        |    |    |    |-- base: string (nullable = true)
        |    |    |    |-- hashTag: string (nullable = true)
        |    |    |    |-- host: string (nullable = true)
        |    |    |    |-- protocol: string (nullable = true)
8. Para registrar la trama de datos como una tabla temporal y ejecutar una consulta de los datos, utilice lo siguiente:
   
        jsonData.registerTempTable("requests")
        sqlContext.sql("select context.location.city from requests where context.location.city is not null")
   
    Esta consulta devolverá la información de la ciudad de los 20 registros principales donde context.location.city no sea nulo.
   
   > [!NOTE]
   > La estructura de contexto está presente en toda la telemetría registrada por Application Insights. Sin embargo, el elemento de la ciudad no se rellena en los registros. Utilice el esquema para identificar otros elementos que se puedan consultar y que puedan contener datos de los registros.
   > 
   > 
   
    Esta consulta devolverá información similar a la siguiente:
   
        +---------+
        |     city|
        +---------+
        | Bellevue|
        |  Redmond|
        |  Seattle|
        |Charlotte|
        ...
        +---------+

## Análisis de datos mediante Scala
1. Desde el [Portal de Azure](https://portal.azure.com), seleccione el clúster de Spark en HDInsight. En la sección **Vínculos rápidos**, seleccione **Paneles de clúster** y, a continuación, seleccione **Jupyter Notebook** en la hoja de Paneles de clúster\_\_.
   
    ![Los paneles de clúster](./media/hdinsight-spark-analyze-application-insight-logs/clusterdashboards.png)
2. En la esquina superior derecha de la página de Jupyter, seleccione **Nuevo** y, después, **Scala**. Se abrirá una nueva pestaña en el explorador con un cuaderno de Jupyter Notebook basado en Scala.
3. En el primer campo (llamado una **celda**) de la página, escriba lo siguiente:
   
        sc.hadoopConfiguration.set("mapreduce.input.fileinputformat.input.dir.recursive", "true")
   
    Esto permite a Spark acceder de forma recursiva a la estructura de directorios de los datos de entrada. La telemetría de Application Insights se registra en una estructura de directorios similar a la siguiente:
   
        /{telemetry type}/YYYY-MM-DD/{##}/
4. Utilice **MAYÚS + INTRO** para ejecutar el código. En el lado izquierdo de la celda, aparece un asterisco entre corchetes para indicar que se está ejecutando el código en esta celda. Una vez finalizado, el asterisco cambiará a un número y se mostrará un resultado similar al siguiente debajo de la celda:
   
        Creating SparkContext as 'sc'
   
        ID    YARN Application ID    Kind    State    Spark UI    Driver log    Current session?
        3    application_1468969497124_0001    spark    idle    Link    Link    ✔
   
        Creating HiveContext as 'sqlContext'
        SparkContext and HiveContext created. Executing user code ...
5. Se habrá creado una nueva celda debajo de la primera. Escriba lo siguiente en la nueva celda. Reemplace **CONTAINER** y **STORAGEACCOUNT** con el nombre de la cuenta de Almacenamiento de Azure y el nombre del contenedor de blobs utilizado al configurar la exportación continua de Application Insights.
   
        %%bash
        hdfs dfs -ls wasb://CONTAINER@STORAGEACCOUNT.blob.core.windows.net/
   
    Utilice **MAYÚS + INTRO** para ejecutar esta celda. Verá un resultado similar al siguiente:
   
        Found 1 items
        drwxrwxrwx   -          0 1970-01-01 00:00 wasb://appinsights@contosostore.blob.core.windows.net/contosoappinsights_2bededa61bc741fbdee6b556571a4831
   
    La ruta de acceso de WASB devuelta es la ubicación de los datos de telemetría de Application Insights. Cambie la línea `hdfs dfs -ls` en la celda para usar la ruta wasb devuelta y, a continuación, use **MAYÚS + INTRO** para volver a ejecutar la celda. Esta vez, los resultados deberían mostrar los directorios que contienen datos de telemetría.
   
   > [!NOTE]
   > Para el resto de los pasos descritos en esta sección, se ha usado el directorio `wasb://appinsights@contosostore.blob.core.windows.net/contosoappinsights_{ID}/Requests`. Este directorio podría no existir, a menos que los datos de telemetría sean de una aplicación web. Si se usan datos de telemetría que no incluyan un directorio de solicitudes, seleccione otro directorio y ajuste el resto de los pasos para utilizar ese directorio y el esquema para los datos almacenados en él.
   > 
   > 
6. Escriba lo siguiente en la nueva celda. Reemplace **WASB\_PATH** con la ruta de acceso del paso anterior.
   
        jsonFiles = sc.textFile('WASB_PATH')
        jsonData = sqlContext.read.json(jsonFiles)
   
    Esto crea una nueva trama de datos de los archivos JSON exportados por el proceso de exportación continua. Utilice **MAYÚS + INTRO** para ejecutar esta celda.
7. En la celda siguiente, escriba y ejecute lo siguiente para ver el esquema creado por Spark para los archivos JSON:
   
        jsonData.printSchema
   
    El esquema de cada tipo de telemetría será diferente. El siguiente es el esquema que se genera para las solicitudes web (datos almacenados en el subdirectorio `Requests`):
   
        root
        |-- context: struct (nullable = true)
        |    |-- application: struct (nullable = true)
        |    |    |-- version: string (nullable = true)
        |    |-- custom: struct (nullable = true)
        |    |    |-- dimensions: array (nullable = true)
        |    |    |    |-- element: string (containsNull = true)
        |    |    |-- metrics: array (nullable = true)
        |    |    |    |-- element: string (containsNull = true)
        |    |-- data: struct (nullable = true)
        |    |    |-- eventTime: string (nullable = true)
        |    |    |-- isSynthetic: boolean (nullable = true)
        |    |    |-- samplingRate: double (nullable = true)
        |    |    |-- syntheticSource: string (nullable = true)
        |    |-- device: struct (nullable = true)
        |    |    |-- browser: string (nullable = true)
        |    |    |-- browserVersion: string (nullable = true)
        |    |    |-- deviceModel: string (nullable = true)
        |    |    |-- deviceName: string (nullable = true)
        |    |    |-- id: string (nullable = true)
        |    |    |-- osVersion: string (nullable = true)
        |    |    |-- type: string (nullable = true)
        |    |-- location: struct (nullable = true)
        |    |    |-- city: string (nullable = true)
        |    |    |-- clientip: string (nullable = true)
        |    |    |-- continent: string (nullable = true)
        |    |    |-- country: string (nullable = true)
        |    |    |-- province: string (nullable = true)
        |    |-- operation: struct (nullable = true)
        |    |    |-- name: string (nullable = true)
        |    |-- session: struct (nullable = true)
        |    |    |-- id: string (nullable = true)
        |    |    |-- isFirst: boolean (nullable = true)
        |    |-- user: struct (nullable = true)
        |    |    |-- anonId: string (nullable = true)
        |    |    |-- isAuthenticated: boolean (nullable = true)
        |-- internal: struct (nullable = true)
        |    |-- data: struct (nullable = true)
        |    |    |-- documentVersion: string (nullable = true)
        |    |    |-- id: string (nullable = true)
        |-- request: array (nullable = true)
        |    |-- element: struct (containsNull = true)
        |    |    |-- count: long (nullable = true)
        |    |    |-- durationMetric: struct (nullable = true)
        |    |    |    |-- count: double (nullable = true)
        |    |    |    |-- max: double (nullable = true)
        |    |    |    |-- min: double (nullable = true)
        |    |    |    |-- sampledValue: double (nullable = true)
        |    |    |    |-- stdDev: double (nullable = true)
        |    |    |    |-- value: double (nullable = true)
        |    |    |-- id: string (nullable = true)
        |    |    |-- name: string (nullable = true)
        |    |    |-- responseCode: long (nullable = true)
        |    |    |-- success: boolean (nullable = true)
        |    |    |-- url: string (nullable = true)
        |    |    |-- urlData: struct (nullable = true)
        |    |    |    |-- base: string (nullable = true)
        |    |    |    |-- hashTag: string (nullable = true)
        |    |    |    |-- host: string (nullable = true)
        |    |    |    |-- protocol: string (nullable = true)
8. Para registrar la trama de datos como una tabla temporal y ejecutar una consulta de los datos, utilice lo siguiente:
   
        jsonData.registerTempTable("requests")
        var city = sqlContext.sql("select context.location.city from requests where context.location.city is not null limit 10").show()
   
    Esta consulta devolverá la información de la ciudad de los 20 registros principales donde context.location.city no sea nulo.
   
   > [!NOTE]
   > La estructura de contexto está presente en toda la telemetría registrada por Application Insights. Sin embargo, el elemento de la ciudad no se rellena en los registros. Utilice el esquema para identificar otros elementos que se puedan consultar y que puedan contener datos de los registros.
   > 
   > 
   
    Esta consulta devolverá información similar a la siguiente:
   
        +---------+
        |     city|
        +---------+
        | Bellevue|
        |  Redmond|
        |  Seattle|
        |Charlotte|
        ...
        +---------+

## Pasos siguientes
Para obtener más ejemplos de uso de Spark para trabajar con datos y servicios de Azure, consulte los siguientes documentos:

* [Spark with BI: Realizar el análisis de datos interactivos con Spark en HDInsight con las herramientas de BI](hdinsight-apache-spark-use-bi-tools.md)
* [Creación de aplicaciones de Aprendizaje automático con Apache Spark en HDInsight de Azure](hdinsight-apache-spark-ipython-notebook-machine-learning.md)
* [Spark con aprendizaje automático: uso de Spark en HDInsight para predecir los resultados de la inspección de alimentos](hdinsight-apache-spark-machine-learning-mllib-ipython.md)
* [Streaming con Spark: uso de Spark en HDInsight para compilar aplicaciones de streaming en tiempo real](hdinsight-apache-spark-eventhub-streaming.md)
* [Análisis del registro del sitio web con Spark en HDInsight](hdinsight-apache-spark-custom-library-website-log-analysis.md)

Para obtener información sobre cómo crear y ejecutar aplicaciones de Spark, consulte los siguientes documentos:

* [Crear una aplicación independiente con Scala](hdinsight-apache-spark-create-standalone-application.md)
* [Ejecutar trabajos de forma remota en un clúster de Spark mediante Livy](hdinsight-apache-spark-livy-rest-interface.md)

<!---HONumber=AcomDC_0914_2016-->