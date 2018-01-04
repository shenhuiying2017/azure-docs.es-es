---
title: Uso de Sqoop en Hadoop con Curl en HDInsight (Azure)| Microsoft Docs
description: "Obtenga información sobre cómo enviar remotamente trabajos a HDInsight usando Curl."
services: hdinsight
documentationcenter: 
author: mumian
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: 39798321-78ca-428c-bcfe-322e49af4059
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 01/03/2018
ms.author: jgao
ms.openlocfilehash: 08e7288d4d9d06f3a74464817892c2b03ae8a86c
ms.sourcegitcommit: 3f33787645e890ff3b73c4b3a28d90d5f814e46c
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/03/2018
---
# <a name="run-sqoop-jobs-with-hadoop-in-hdinsight-with-curl"></a>Ejecución de trabajos de Sqoop con Hadoop en HDInsight con Curl
[!INCLUDE [sqoop-selector](../../../includes/hdinsight-selector-use-sqoop.md)]

En este documento aprenderá a utilizar Curl para ejecutar trabajos de Sqoop en un clúster de Hadoop en HDInsight.

Curl sirve para demostrar cómo se puede interactuar con HDInsight usando solicitudes HTTP sin procesar para ejecutar, supervisar y recuperar los resultados de los trabajos de Sqoop. Esto funciona mediante la API de REST de WebHCat (conocida anteriormente como Templeton) proporcionada por el clúster de HDInsight.

## <a name="prerequisites"></a>requisitos previos
Para completar los pasos de este artículo, necesitará lo siguiente:

* Complete la opción [Usar Sqoop con Hadoop en HDInsight](hdinsight-use-sqoop.md#create-cluster-and-sql-database) para configurar un entorno con un clúster de HDInsight y una base de datos de Azure SQL.
* [Curl](http://curl.haxx.se/). Curl es una herramienta que se usa para transferir datos desde o hacia un clúster de HDInsight.
* [jq](http://stedolan.github.io/jq/). La utilidad jq se usa para procesar los datos JSON que devuelven las solicitudes de REST.

## <a name="submit-sqoop-jobs-by-using-curl"></a>Envío de trabajos de Sqoop mediante Curl
> [!NOTE]
> Al usar Curl o cualquier otra comunicación REST con WebHCat, debe proporcionar el nombre de usuario y la contraseña del administrador del clúster de HDInsight para autenticar las solicitudes. También debe usar el nombre del clúster como parte del identificador uniforme de recursos (URI) que se usa para enviar las solicitudes al servidor.
> 
> En el caso de los comandos que aparecen en esta sección, reemplace **USERNAME** por el usuario para autenticación en el clúster y **PASSWORD** por la contraseña de la cuenta de usuario. Reemplace **CLUSTERNAME** por el nombre del clúster.
> 
> La API de REST se protege con la [autenticación básica](http://en.wikipedia.org/wiki/Basic_access_authentication). Siempre debe crear solicitudes usando HTTP segura (HTTPS) para así garantizar que las credenciales se envían de manera segura al servidor.
> 
> 

1. Desde una línea de comandos, utilice el siguiente comando para comprobar que puede conectarse al clúster de HDInsight.

    ```bash   
    curl -u USERNAME:PASSWORD -G https://CLUSTERNAME.azurehdinsight.net/templeton/v1/status
    ```

    Debería recibir una respuesta similar a la siguiente:

    ```json   
    {"status":"ok","version":"v1"}
    ```
   
    Los parámetros que se utilizan en este comando son los siguientes:
   
   * **-u** : el nombre de usuario y la contraseña que se utilizan para autenticar la solicitud.
   * **-G** : indica que esta es una solicitud GET.
     
     El comienzo del identificador URL, **https://CLUSTERNAME.azurehdinsight.net/templeton/v1**, será el mismo para todas las solicitudes. La ruta de acceso, **/status**, indica que la solicitud debe devolver un estado de WebHCat (también conocido como Templeton) al servidor. 
2. Para enviar el trabajo de sqoop, use lo siguiente:

    ```bash
    curl -u USERNAME:PASSWORD -d user.name=USERNAME -d command="export --connect jdbc:sqlserver://SQLDATABASESERVERNAME.database.windows.net;user=USERNAME@SQLDATABASESERVERNAME;password=PASSWORD;database=SQLDATABASENAME --table log4jlogs --export-dir /example/data/sample.log --input-fields-terminated-by \0x20 -m 1" -d statusdir="wasb:///example/data/sqoop/curl" https://CLUSTERNAME.azurehdinsight.net/templeton/v1/sqoop
    ```

    Los parámetros que se utilizan en este comando son los siguientes:

    * **-d**: como `-G` no se usa, la solicitud establece como valor predeterminado el método POST. `-d` especifica los valores de datos que se envían con la solicitud.

        * **user.name** : el usuario que ejecuta el comando.

        * **command** : el comando de Sqoop para ejecutar.

        * **statusdir** : el directorio donde se escribirá el estado de este trabajo.

    Este comando debe devolver un id. de trabajo que se pueda usar para comprobar el estado del trabajo.

        ```json
        {"id":"job_1415651640909_0026"}
        ```

3. Para revisar el estado del trabajo, use el siguiente comando. Reemplace **JOBID** por el valor devuelto en el paso anterior. Por ejemplo, si el valor devuelto fuese `{"id":"job_1415651640909_0026"}`, entonces **JOBID** sería `job_1415651640909_0026`.

    ```bash
    curl -G -u USERNAME:PASSWORD -d user.name=USERNAME https://CLUSTERNAME.azurehdinsight.net/templeton/v1/jobs/JOBID | jq .status.state
    ```

    Si se completó el trabajo, el estado será **SUCCEEDED**.
   
   > [!NOTE]
   > Esta solicitud de Curl devuelve un documento de notación de objetos JavaScript (JSON) con información acerca del trabajo; jq se usa para recuperar solo el valor de estado.
   > 
   > 
4. Una vez que el estado del trabajo cambió a **SUCCEEDED**, puede recuperar los resultados del trabajo desde Azure Blob Storage. El parámetro `statusdir` transmitido con la consulta contiene la ubicación del archivo de salida; en este caso, **wasb:///example/data/sqoop/curl**. Esta dirección almacena la salida del trabajo en el directorio **example/data/sqoop/curl** que se encuentra en el contenedor de almacenamiento predeterminado que su clúster de HDInsight usa.
   
    Puede usar Azure Portal para tener acceso a los blobs "stderr" y "stdout".  Asimismo, también puede usar Microsoft SQL Server Management Studio para comprobar los datos que se cargan en la tabla denominada "log4jlogs".

## <a name="limitations"></a>Limitaciones
* Exportación masiva: con HDInsight basado en Linux, el conector Sqoop que se utiliza para exportar datos a Microsoft SQL Server o SQL Database Azure no es compatible actualmente con las inserciones masivas.
* Procesamiento por lotes: con HDInsight basado en Linux, cuando se usa `-batch` al realizar inserciones, Sqoop realizará varias inserciones en lugar de procesar por lotes las operaciones de inserción.

## <a name="summary"></a>Resumen
Tal como se ha mostrado en este documento, puede usar una solicitud HTTP sin procesar para ejecutar, supervisar y ver los resultados de los trabajos de Sqoop en su clúster de HDInsight.

Para obtener más información sobre la interfaz REST utilizada en este artículo, consulte la referencia <a href="https://sqoop.apache.org/docs/1.99.3/RESTAPI.html" target="_blank">Sqoop REST API Guide</a>.

## <a name="next-steps"></a>pasos siguientes
Si desea obtener información general sobre Hive con HDInsight:

* [Uso de Sqoop con Hadoop en HDInsight (Windows)](hdinsight-use-sqoop.md)

Para obtener información sobre otras formas en que puede trabajar con Hadoop en HDInsight:

* [Uso de Hive con Hadoop en HDInsight](hdinsight-use-hive.md)
* [Uso de Pig con Hadoop en HDInsight](hdinsight-use-pig.md)
* [Uso de MapReduce con Hadoop en HDInsight](hdinsight-use-mapreduce.md)

Si quiere leer más artículos de HDInsight en los que se incluya Curl, consulte:
 
* [Creación de clústeres de Hadoop mediante la API de REST de Azure](../hdinsight-hadoop-create-linux-clusters-curl-rest.md)
* [Ejecución de consultas de Hive con Hadoop en HDInsight con REST](apache-hadoop-use-hive-curl.md)
* [Ejecución de trabajos de MapReduce con Hadoop en HDInsight con REST](apache-hadoop-use-mapreduce-curl.md)
* [Ejecución de trabajos de Pig con Hadoop en HDInsight con Curl](apache-hadoop-use-pig-curl.md)



