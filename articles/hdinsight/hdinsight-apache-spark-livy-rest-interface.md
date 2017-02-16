---
title: "Envío remoto de trabajos de Spark mediante Livy | Microsoft Docs"
description: "Obtenga información sobre cómo usar Livy con clústeres de HDInsight para enviar trabajos de Spark de manera remota."
services: hdinsight
documentationcenter: 
author: nitinme
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: 2817b779-1594-486b-8759-489379ca907d
ms.service: hdinsight
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/28/2016
ms.author: nitinme
translationtype: Human Translation
ms.sourcegitcommit: 0866581d6feebc71ed82aa48f6e41a58068af900
ms.openlocfilehash: 0b8c9f9395f034355c8f10574e750fcf40e870de


---
# <a name="submit-spark-jobs-remotely-to-an-apache-spark-cluster-on-hdinsight-linux-using-livy"></a>Envío de trabajos de Spark remotamente a un clúster de Apache Spark en HDInsight Linux mediante Livy
Un clúster Apache Spark en HDInsight de Azure incluye Livy, una interfaz REST para enviar trabajos de forma remota a un clúster Spark. Para obtener documentación detallada, vea [Livy](https://github.com/cloudera/hue/tree/master/apps/spark/java#welcome-to-livy-the-rest-spark-server).

Puede usar Livy para ejecutar shells de Spark interactivos o enviar trabajos por lotes que se ejecutarán en Spark. Este artículo trata acerca de cómo utilizar Livy para enviar trabajos por lotes. La sintaxis siguiente utiliza Curl para realizar llamadas REST al punto de conexión de Livy.

**Requisitos previos:**

Debe tener lo siguiente:

* Una suscripción de Azure. Consulte [How to get Azure Free trial for testing Hadoop in HDInsight (Obtención de una versión de prueba gratuita de Azure para probar Hadoop en HDInsight)](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).
* Un clúster Apache Spark en HDInsight Linux. Para obtener instrucciones, vea [Creación de clústeres Apache Spark en HDInsight de Azure](hdinsight-apache-spark-jupyter-spark-sql.md).

## <a name="submit-a-batch-job"></a>Envío de un trabajo por lotes
Antes de enviar un trabajo por lotes, debe cargar el archivo jar de aplicación en el almacenamiento del clúster asociado al clúster. Puede usar [**AzCopy**](../storage/storage-use-azcopy.md), una utilidad de línea de comandos, para hacerlo. Hay muchos otros clientes que se pueden utilizar para cargar datos. Puede encontrar más información al respecto en [Carga de datos para trabajos de Hadoop en HDInsight](hdinsight-upload-data.md).

    curl -k --user "<hdinsight user>:<user password>" -v -H <content-type> -X POST -d '{ "file":"<path to application jar>", "className":"<classname in jar>" }' 'https://<spark_cluster_name>.azurehdinsight.net/livy/batches'

**Ejemplos:**

* Si el archivo jar se encuentra en el almacenamiento de clúster (WASB)
  
        curl -k --user "admin:mypassword1!" -v -H 'Content-Type: application/json' -X POST -d '{ "file":"wasbs://mycontainer@mystorageaccount.blob.core.windows.net/data/SparkSimpleTest.jar", "className":"com.microsoft.spark.test.SimpleFile" }' "https://mysparkcluster.azurehdinsight.net/livy/batches"
* Si desea pasar el nombre del archivo jar y el nombre de clase como parte de un archivo de entrada (en este ejemplo, input.txt)
  
        curl -k  --user "admin:mypassword1!" -v -H "Content-Type: application/json" -X POST --data @C:\Temp\input.txt "https://mysparkcluster.azurehdinsight.net/livy/batches"

## <a name="get-information-on-batches-running-on-the-cluster"></a>Obtener información sobre los lotes que se ejecutan en el clúster
    curl -k --user "<hdinsight user>:<user password>" -v -X GET "https://<spark_cluster_name>.azurehdinsight.net/livy/batches"

**Ejemplos:**

* Si desea recuperar todos los lotes que se ejecutan en el clúster:
  
        curl -k --user "admin:mypassword1!" -v -X GET "https://mysparkcluster.azurehdinsight.net/livy/batches"
* Si desea recuperar un lote específico con un determinado identificador de lote
  
        curl -k --user "admin:mypassword1!" -v -X GET "https://mysparkcluster.azurehdinsight.net/livy/batches/{batchId}"

## <a name="delete-a-batch-job"></a>Eliminar un trabajo por lotes
    curl -k --user "<hdinsight user>:<user password>" -v -X DELETE "https://<spark_cluster_name>.azurehdinsight.net/livy/batches/{batchId}"

**Ejemplo**:

    curl -k --user "admin:mypassword1!" -v -X DELETE "https://mysparkcluster.azurehdinsight.net/livy/batches/{batchId}"

## <a name="livy-and-high-availability"></a>Livy y la alta disponibilidad
Livy proporciona alta disponibilidad para los trabajos de Spark que se ejecuten en el clúster. He aquí un par de ejemplos:

* Si el servicio Livy deja de funcionar después de haber enviado un trabajo de forma remota a un clúster Spark, dicho trabajo continúa ejecutándose en segundo plano. Cuando se restablece el funcionamiento de Livy, restaura el estado del trabajo e informa de ello.
* Los cuadernos de Jupyter Notebook para HDInsight cuentan con la tecnología de Livy en el backend. Si uno de tales cuadernos se ejecuta en un trabajo de Spark y se reinicia el servicio Livy, el cuaderno seguirá ejecutando las celdas de código. 

## <a name="show-me-an-example"></a>Mostrar un ejemplo
En esta sección, veremos ejemplos sobre cómo usar Livy para enviar una aplicación Spark, supervisar el progreso de la aplicación y después eliminar el trabajo. La aplicación que se usa en este ejemplo es la que se desarrolla en el artículo [Creación de una aplicación de Scala independiente para ejecutarla en el clúster HDInsight Spark](hdinsight-apache-spark-create-standalone-application.md). En los pasos siguientes se supone lo siguiente:

* Ya ha copiado el archivo JAR de la aplicación en la cuenta de almacenamiento asociada con el clúster.
* Tiene CuRL instalado en el equipo donde intenta seguir estos pasos.

Lleve a cabo los siguiente pasos.

1. Nos gustaría comprobar primero que Livy se está ejecutando en el clúster. Podemos hacerlo mediante la obtención de una lista de lotes en ejecución. Si es la primera vez que ejecuta un trabajo con Livy, debe devolver cero.
   
        curl -k --user "admin:mypassword1!" -v -X GET "https://mysparkcluster.azurehdinsight.net/livy/batches"
   
    Debería obtener una salida similar a la siguiente:
   
        < HTTP/1.1 200 OK
        < Content-Type: application/json; charset=UTF-8
        < Server: Microsoft-IIS/8.5
        < X-Powered-By: ARR/2.5
        < X-Powered-By: ASP.NET
        < Date: Fri, 20 Nov 2015 23:47:53 GMT
        < Content-Length: 34
        <
        {"from":0,"total":0,"sessions":[]}* Connection #0 to host mysparkcluster.azurehdinsight.net left intact
   
    Observe que la última línea de la salida indica **total: 0**, lo que sugiere que no hay lotes en ejecución.
2. Ahora vamos a enviar un trabajo por lotes. El fragmento de código siguiente utiliza un archivo de entrada (input.txt) para pasar el nombre de archivo jar y el nombre de clase como parámetros. Este es el enfoque recomendado si ejecuta estos pasos desde un equipo de Windows.
   
        curl -k --user "admin:mypassword1!" -v -H "Content-Type: application/json" -X POST --data @C:\Temp\input.txt "https://mysparkcluster.azurehdinsight.net/livy/batches"
   
    A continuación se definen los parámetros del archivo **input.txt** :
   
        { "file":"wasbs:///example/jars/SparkSimpleApp.jar", "className":"com.microsoft.spark.example.WasbIOTest" }
   
    Debería ver una salida similar a la siguiente:
   
        < HTTP/1.1 201 Created
        < Content-Type: application/json; charset=UTF-8
        < Location: /0
        < Server: Microsoft-IIS/8.5
        < X-Powered-By: ARR/2.5
        < X-Powered-By: ASP.NET
        < Date: Fri, 20 Nov 2015 23:51:30 GMT
        < Content-Length: 36
        <
        {"id":0,"state":"starting","log":[]}* Connection #0 to host mysparkcluster.azurehdinsight.net left intact
   
    Observe que la última línea del resultado indica **state:starting**. También indica **id:0**. Este es el identificador de lote.
3. Ahora puede recuperar el estado de este lote específico con el identificador de lote.
   
        curl -k --user "admin:mypassword1!" -v -X GET "https://mysparkcluster.azurehdinsight.net/livy/batches/0"
   
    Debería ver una salida similar a la siguiente:
   
        < HTTP/1.1 200 OK
        < Content-Type: application/json; charset=UTF-8
        < Server: Microsoft-IIS/8.5
        < X-Powered-By: ARR/2.5
        < X-Powered-By: ASP.NET
        < Date: Fri, 20 Nov 2015 23:54:42 GMT
        < Content-Length: 509
        <
        {"id":0,"state":"success","log":["\t diagnostics: N/A","\t ApplicationMaster host: 10.0.0.4","\t ApplicationMaster RPC port: 0","\t queue: default","\t start time: 1448063505350","\t final status: SUCCEEDED","\t tracking URL: http://hn0-myspar.lpel1gnnvxne3gwzqkfq5u5uzh.jx.internal.cloudapp.net:8088/proxy/application_1447984474852_0002/","\t user: root","15/11/20 23:52:47 INFO Utils: Shutdown hook called","15/11/20 23:52:47 INFO Utils: Deleting directory /tmp/spark-b72cd2bf-280b-4c57-8ceb-9e3e69ac7d0c"]}* Connection #0 to host mysparkcluster.azurehdinsight.net left intact
   
    La salida muestra ahora **state:success**, lo que sugiere que el trabajo se ha completado correctamente.
4. Si lo desea, ahora puede eliminar el lote.
   
        curl -k --user "admin:mypassword1!" -v -X DELETE "https://mysparkcluster.azurehdinsight.net/livy/batches/0"
   
    Debería ver una salida similar a la siguiente:
   
        < HTTP/1.1 200 OK
        < Content-Type: application/json; charset=UTF-8
        < Server: Microsoft-IIS/8.5
        < X-Powered-By: ARR/2.5
        < X-Powered-By: ASP.NET
        < Date: Sat, 21 Nov 2015 18:51:54 GMT
        < Content-Length: 17
        <
        {"msg":"deleted"}* Connection #0 to host mysparkcluster.azurehdinsight.net left intact
   
    La última línea de la salida indica que el lote se ha eliminado correctamente. Si elimina un trabajo mientras está en ejecución, esencialmente terminará el trabajo. Si elimina un trabajo que se ha completado correctamente o incorrectamente, elimina la información del trabajo por completo.

## <a name="using-livy-on-hdinsight-35-spark-clusters"></a>Uso de Livy en clústeres de HDInsight 3.5 Spark

Un clúster de HDInsight 3.5 deshabilita de forma predeterminada el uso de rutas de acceso a archivos locales para el acceso a archivos de datos de ejemplo o archivos jar. Le recomendamos que use la ruta de acceso `wasb://` en su lugar para tener acceso a archivos jar o archivos de datos de ejemplo desde el clúster. Si desea usar la ruta de acceso local, debe actualizar la configuración de Ambari en consecuencia. Para ello:

1. Vaya al portal de Ambari para el clúster. La interfaz de usuario web de Ambari está disponible en el clúster de HDInsight en https://**CLUSTERNAME**.azurehdidnsight.net, donde CLUSTERNAME es el nombre del clúster.

2. En el panel de navegación izquierdo, haga clic en **Livy**y, a continuación, haga clic en **Configs** (Configuraciones).

3. En **livy-default**, agregue el nombre de propiedad `livy.file.local-dir-whitelist` y establezca su valor en **"/"** si desea permitir el acceso total al sistema de archivos. Si desea permitir el acceso únicamente a un directorio específico, proporcione la ruta de acceso a ese directorio como valor.


## <a name="a-nameseealsoasee-also"></a><a name="seealso"></a>Otras referencias
* [Introducción a Apache Spark en HDInsight de Azure](hdinsight-apache-spark-overview.md)

### <a name="scenarios"></a>Escenarios
* [Spark with BI: Realizar el análisis de datos interactivos con Spark en HDInsight con las herramientas de BI](hdinsight-apache-spark-use-bi-tools.md)
* [Creación de aplicaciones de Aprendizaje automático con Apache Spark en HDInsight de Azure](hdinsight-apache-spark-ipython-notebook-machine-learning.md)
* [Spark con aprendizaje automático: uso de Spark en HDInsight para predecir los resultados de la inspección de alimentos](hdinsight-apache-spark-machine-learning-mllib-ipython.md)
* [Streaming con Spark: uso de Spark en HDInsight para compilar aplicaciones de streaming en tiempo real](hdinsight-apache-spark-eventhub-streaming.md)
* [Análisis del registro del sitio web con Spark en HDInsight](hdinsight-apache-spark-custom-library-website-log-analysis.md)

### <a name="create-and-run-applications"></a>Creación y ejecución de aplicaciones
* [Crear una aplicación independiente con Scala](hdinsight-apache-spark-create-standalone-application.md)

### <a name="tools-and-extensions"></a>Herramientas y extensiones
* [Use HDInsight Tools Plugin for IntelliJ IDEA to create and submit Spark Scala applications (Uso del complemento de herramientas de HDInsight para IntelliJ IDEA para crear y enviar aplicaciones Scala Spark)](hdinsight-apache-spark-intellij-tool-plugin.md)
* [Use HDInsight Tools Plugin for IntelliJ IDEA to debug Spark applications remotely (Uso del complemento de herramientas de HDInsight para IntelliJ IDEA para depurar aplicaciones de Spark de forma remota)](hdinsight-apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)
* [Uso de cuadernos de Zeppelin con un clúster Spark en HDInsight](hdinsight-apache-spark-use-zeppelin-notebook.md)
* [Kernels disponibles para el cuaderno de Jupyter en el clúster Spark para HDInsight](hdinsight-apache-spark-jupyter-notebook-kernels.md)
* [Uso de paquetes externos con cuadernos de Jupyter Notebook](hdinsight-apache-spark-jupyter-notebook-use-external-packages.md)
* [Instalación de un cuaderno de Jupyter Notebook en el equipo y conexión al clúster de Apache Spark en HDInsight de Azure](hdinsight-apache-spark-jupyter-notebook-install-locally.md)

### <a name="manage-resources"></a>Administración de recursos
* [Administración de recursos para el clúster Apache Spark en HDInsight de Azure](hdinsight-apache-spark-resource-manager.md)
* [Track and debug jobs running on an Apache Spark cluster in HDInsight (Seguimiento y depuración de trabajos que se ejecutan en un clúster de Apache Spark en HDInsight)](hdinsight-apache-spark-job-debugging.md)




<!--HONumber=Nov16_HO5-->


