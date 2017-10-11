---
title: "Uso de Livy Spark para enviar trabajos a un clúster Spark de Azure HDInsight | Microsoft Docs"
description: "Obtenga información sobre cómo usar la API de REST de Apache Spark para enviar trabajos de Spark de forma remota a un clúster de Azure HDInsight."
keywords: API de REST de Apache Spark, Livy Spark
services: hdinsight
documentationcenter: 
author: nitinme
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: 2817b779-1594-486b-8759-489379ca907d
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/25/2017
ms.author: nitinme
ms.openlocfilehash: e1a28d69bbf40ea3134a7899a0d2fe70e5fc9e71
ms.sourcegitcommit: 18ad9bc049589c8e44ed277f8f43dcaa483f3339
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 08/29/2017
---
# <a name="use-apache-spark-rest-api-to-submit-remote-jobs-to-an-hdinsight-spark-cluster"></a>Uso de la API de REST de Apache Spark para enviar trabajos remotos a un clúster Spark de HDInsight

Obtenga información sobre cómo usar Livy, la API de REST de Apache Spark, que se usa para enviar trabajos remotos a un clúster Spark de Azure HDInsight. Para obtener documentación detallada, vea [Livy](https://github.com/cloudera/hue/tree/master/apps/spark/java#welcome-to-livy-the-rest-spark-server).

Puede usar Livy para ejecutar shells de Spark interactivos o enviar trabajos por lotes que se ejecutarán en Spark. Este artículo trata acerca de cómo utilizar Livy para enviar trabajos por lotes. Los fragmentos de código de este artículo usan cURL para realizar llamadas de la API de REST al punto de conexión de Livy Spark.

**Requisitos previos:**

* Un clúster de Apache Spark en HDInsight. Para obtener instrucciones, vea [Creación de clústeres Apache Spark en HDInsight de Azure](hdinsight-apache-spark-jupyter-spark-sql.md).

* [cURL](http://curl.haxx.se/). En este artículo se usa cURL para mostrar cómo realizar llamadas de la API de REST a un clúster de HDInsight Spark.

## <a name="submit-a-livy-spark-batch-job"></a>Envío de un trabajo por lotes de Livy Spark
Antes de enviar un trabajo por lotes, debe cargar el archivo jar de aplicación en el almacenamiento del clúster asociado al clúster. Puede usar [**AzCopy**](../storage/common/storage-use-azcopy.md), una utilidad de línea de comandos, para hacerlo. Hay muchos otros clientes que se pueden usar para cargar datos. Puede encontrar más información al respecto en [Carga de datos para trabajos de Hadoop en HDInsight](hdinsight-upload-data.md).

    curl -k --user "<hdinsight user>:<user password>" -v -H <content-type> -X POST -d '{ "file":"<path to application jar>", "className":"<classname in jar>" }' 'https://<spark_cluster_name>.azurehdinsight.net/livy/batches'

**Ejemplos:**

* Si el archivo jar se encuentra en el almacenamiento de clúster (WASB)
  
        curl -k --user "admin:mypassword1!" -v -H 'Content-Type: application/json' -X POST -d '{ "file":"wasb://mycontainer@mystorageaccount.blob.core.windows.net/data/SparkSimpleTest.jar", "className":"com.microsoft.spark.test.SimpleFile" }' "https://mysparkcluster.azurehdinsight.net/livy/batches"
* Si quiere pasar el nombre del archivo jar y el nombre de clase como parte de un archivo de entrada (en este ejemplo, input.txt)
  
        curl -k  --user "admin:mypassword1!" -v -H "Content-Type: application/json" -X POST --data @C:\Temp\input.txt "https://mysparkcluster.azurehdinsight.net/livy/batches"

## <a name="get-information-on-livy-spark-batches-running-on-the-cluster"></a>Obtención de información sobre los lotes de Livy Spark que se ejecutan en el clúster
    curl -k --user "<hdinsight user>:<user password>" -v -X GET "https://<spark_cluster_name>.azurehdinsight.net/livy/batches"

**Ejemplos:**

* Si desea recuperar todos los lotes de Livy Spark que se ejecutan en el clúster:
  
        curl -k --user "admin:mypassword1!" -v -X GET "https://mysparkcluster.azurehdinsight.net/livy/batches"
* Si desea recuperar un lote específico con un determinado identificador de lote
  
        curl -k --user "admin:mypassword1!" -v -X GET "https://mysparkcluster.azurehdinsight.net/livy/batches/{batchId}"

## <a name="delete-a-livy-spark-batch-job"></a>Eliminación de un trabajo por lotes de Livy Spark
    curl -k --user "<hdinsight user>:<user password>" -v -X DELETE "https://<spark_cluster_name>.azurehdinsight.net/livy/batches/{batchId}"

**Ejemplo**:

    curl -k --user "admin:mypassword1!" -v -X DELETE "https://mysparkcluster.azurehdinsight.net/livy/batches/{batchId}"

## <a name="livy-spark-and-high-availability"></a>Livy Spark y alta disponibilidad
Livy proporciona alta disponibilidad para los trabajos de Spark que se ejecuten en el clúster. Estos son algunos ejemplos.

* Si el servicio Livy deja de funcionar después de haber enviado un trabajo de forma remota a un clúster Spark, dicho trabajo continúa ejecutándose en segundo plano. Cuando se restablece el funcionamiento de Livy, restaura el estado del trabajo e informa de ello.
* Los cuadernos de Jupyter Notebook para HDInsight cuentan con la tecnología de Livy en el backend. Si un cuaderno ejecuta un trabajo de Spark y se reinicia el servicio Livy, el cuaderno sigue ejecutando las celdas de código. 

## <a name="show-me-an-example"></a>Mostrar un ejemplo
En esta sección se verán ejemplos de uso de Livy Spark para enviar un trabajo por lotes, supervisar el progreso del trabajo y después eliminarlo. La aplicación que se usa en este ejemplo es la que se desarrolla en el artículo [Creación de una aplicación de Scala independiente para ejecutarla en el clúster HDInsight Spark](hdinsight-apache-spark-create-standalone-application.md). En los pasos siguientes se da por hecho que:

* Ya ha copiado el archivo JAR de la aplicación en la cuenta de almacenamiento asociada con el clúster.
* Tiene CuRL instalado en el equipo donde intenta seguir estos pasos.

Lleve a cabo los siguiente pasos:

1. Nos gustaría comprobar primero que Livy Spark se está ejecutando en el clúster. Podemos hacerlo mediante la obtención de una lista de lotes en ejecución. Si es la primera vez que ejecuta un trabajo con Livy, la salida debe devolver cero.
   
        curl -k --user "admin:mypassword1!" -v -X GET "https://mysparkcluster.azurehdinsight.net/livy/batches"
   
    Debería obtener una salida similar al siguiente fragmento de código:
   
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

2. Ahora vamos a enviar un trabajo por lotes. El fragmento de código siguiente usa un archivo de entrada (input.txt) para pasar el nombre del archivo jar y el nombre de clase como parámetros. Si ejecuta estos pasos desde un equipo Windows, el enfoque recomendado es usar un archivo de entrada.
   
        curl -k --user "admin:mypassword1!" -v -H "Content-Type: application/json" -X POST --data @C:\Temp\input.txt "https://mysparkcluster.azurehdinsight.net/livy/batches"
   
    A continuación se definen los parámetros del archivo **input.txt** :
   
        { "file":"wasb:///example/jars/SparkSimpleApp.jar", "className":"com.microsoft.spark.example.WasbIOTest" }
   
    Debería ver una salida similar al siguiente fragmento de código:
   
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
   
    Observe que la última línea del resultado indica **state:starting**. También indica **id:0**. Aquí, **0** es el identificador de lote.

3. Ahora puede recuperar el estado de este lote concreto con el identificador de lote.
   
        curl -k --user "admin:mypassword1!" -v -X GET "https://mysparkcluster.azurehdinsight.net/livy/batches/0"
   
    Debería ver una salida similar al siguiente fragmento de código:
   
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
   
    Debería ver una salida similar al siguiente fragmento de código:
   
        < HTTP/1.1 200 OK
        < Content-Type: application/json; charset=UTF-8
        < Server: Microsoft-IIS/8.5
        < X-Powered-By: ARR/2.5
        < X-Powered-By: ASP.NET
        < Date: Sat, 21 Nov 2015 18:51:54 GMT
        < Content-Length: 17
        <
        {"msg":"deleted"}* Connection #0 to host mysparkcluster.azurehdinsight.net left intact
   
    La última línea de la salida indica que el lote se ha eliminado correctamente. Al eliminar un trabajo mientras se está ejecutando también se termina. Si elimina un trabajo que se ha completado correctamente o incorrectamente, elimina la información del trabajo por completo.

## <a name="using-livy-spark-on-hdinsight-35-clusters"></a>Uso de Livy Spark en clústeres de HDInsight 3.5

Un clúster de HDInsight 3.5 deshabilita de forma predeterminada el uso de rutas de acceso a archivos locales para el acceso a archivos de datos de ejemplo o archivos jar. Le recomendamos que use la ruta de acceso `wasb://` en su lugar para tener acceso a archivos jar o archivos de datos de ejemplo desde el clúster. Si desea usar la ruta de acceso local, debe actualizar la configuración de Ambari en consecuencia. Para ello:

1. Vaya al portal de Ambari para el clúster. La interfaz de usuario web de Ambari está disponible en el clúster de HDInsight en https://**CLUSTERNAME**.azurehdidnsight.net, donde CLUSTERNAME es el nombre del clúster.

2. En el panel de navegación izquierdo, haga clic en **Livy**y, a continuación, haga clic en **Configs** (Configuraciones).

3. En **livy-default**, agregue el nombre de propiedad `livy.file.local-dir-whitelist` y establezca su valor en **"/"** si desea permitir el acceso total al sistema de archivos. Si desea permitir el acceso únicamente a un directorio específico, proporcione la ruta de acceso a ese directorio como valor.

## <a name="submitting-livy-jobs-for-a-cluster-within-an-azure-virtual-network"></a>Envío de trabajos de Livy para un clúster en una red virtual de Azure

Si se conecta a un clúster de HDInsight Spark desde una red virtual de Azure, puede conectarse directamente a Livy en el clúster. En tal caso, la dirección URL del punto de conexión de Livy es `http://<IP address of the headnode>:8998/batches`. Aquí, **8998** es el puerto en el que se ejecuta Livy en el nodo principal del clúster. Para más información sobre el acceso a los servicios en puertos no públicos, vea [Puertos utilizados por los servicios Hadoop en HDInsight](hdinsight-hadoop-port-settings-for-services.md).

## <a name="troubleshooting"></a>Solución de problemas

Estos son algunos de los problemas que pueden surgir a la hora de utilizar Livy para el envío de trabajo remoto a clústeres de Spark.

### <a name="using-an-external-jar-from-the-additional-storage-is-not-supported"></a>No se admite el uso de un archivo jar externo desde el almacenamiento adicional

**Problema**: si el trabajo de Livy Spark hace referencia a un archivo .jar externo de la cuenta de almacenamiento adicional asociada al clúster, se produce un error en el trabajo.

**Solución:** asegúrese de que el archivo jar que desea usar se encuentra disponible en el almacenamiento predeterminado asociado al clúster de HDInsight.





## <a name="next-step"></a>Paso siguiente

* [Administración de recursos para el clúster Apache Spark en HDInsight de Azure](hdinsight-apache-spark-resource-manager.md)
* [Track and debug jobs running on an Apache Spark cluster in HDInsight (Seguimiento y depuración de trabajos que se ejecutan en un clúster de Apache Spark en HDInsight)](hdinsight-apache-spark-job-debugging.md)

