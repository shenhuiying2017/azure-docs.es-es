---
title: Uso de Hive en Hadoop con Curl en HDInsight (Azure) | Microsoft Docs
description: "Sepa cómo enviar remotamente trabajos de Pig a HDInsight mediante el uso de Curl."
services: hdinsight
documentationcenter: 
author: Blackmist
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: 6ce18163-63b5-4df6-9bb6-8fcbd4db05d8
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 12/04/2017
ms.author: larryfr
ms.openlocfilehash: b451a80934a19f8a38ab9e8ace358674827aefa0
ms.sourcegitcommit: 828cd4b47fbd7d7d620fbb93a592559256f9d234
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/18/2018
---
# <a name="run-hive-queries-with-hadoop-in-hdinsight-using-rest"></a>Ejecución de consultas de Hive con Hadoop en HDInsight con REST

[!INCLUDE [hive-selector](../../../includes/hdinsight-selector-use-hive.md)]

Obtenga información acerca de cómo usar la API de REST de WebHCat para ejecutar consultas de Hive con Hadoop en un clúster de Azure HDInsight.

[Curl](http://curl.haxx.se/) se usa para demostrar cómo puede interactuar con HDInsight mediante solicitudes HTTP sin formato. La utilidad [jq](http://stedolan.github.io/jq/) se usa para procesar datos JSON devueltos de solicitudes de REST.

> [!NOTE]
> Si ya está familiarizado con el uso de servidores de Hadoop basado en Linux, pero no conoce HDInsight, consulte el documento [Lo que necesita saber acerca de Hadoop en HDInsight basado en Linux](../hdinsight-hadoop-linux-information.md).

## <a id="curl"></a>Ejecución de consultas de Hive

> [!NOTE]
> Al usar Curl o cualquier otra comunicación REST con WebHCat, debe proporcionar el nombre de usuario y la contraseña del administrador del clúster de HDInsight para autenticar las solicitudes.
>
> En el caso de los comandos de esta sección, reemplace **admin** por el usuario que se va a autenticar en el clúster. Reemplace **CLUSTERNAME** por el nombre del clúster. Cuando se le solicite, escriba la contraseña de la cuenta de usuario.
>
> La API de REST se protege con la [autenticación básica](http://en.wikipedia.org/wiki/Basic_access_authentication). Para garantizar que las credenciales se envían de manera segura al servidor, siempre debe crear solicitudes usando HTTP segura (HTTPS).

1. Desde una línea de comandos, utilice el siguiente comando para comprobar que puede conectarse al clúster de HDInsight.

    ```bash
    curl -u admin -G https://CLUSTERNAME.azurehdinsight.net/templeton/v1/status
    ```

    Debe recibir una respuesta similar al texto siguiente:

    ```json
    {"status":"ok","version":"v1"}
    ```

    Los parámetros que se utilizan en este comando son los siguientes:

    * **-u** : el nombre de usuario y la contraseña que se utilizan para autenticar la solicitud.
    * **-G**: indica que esta es una solicitud de operación GET.

   El comienzo del identificador URL, **https://CLUSTERNAME.azurehdinsight.net/templeton/v1**, será el mismo para todas las solicitudes. La ruta de acceso, **/status**, indica que la solicitud debe devolver un estado de WebHCat (también conocido como Templeton) al servidor. También puede solicitar la versión de Hive con el siguiente comando:

    ```bash
    curl -u admin -G https://CLUSTERNAME.azurehdinsight.net/templeton/v1/version/hive
    ```

    Esta solicitud devuelve una respuesta similar al texto siguiente:

    ```json
        {"module":"hive","version":"0.13.0.2.1.6.0-2103"}
    ```

2. Use lo siguiente para crear una tabla llamada **log4jLogs**:

    ```bash
    curl -u admin -d user.name=admin -d execute="set+hive.execution.engine=tez;DROP+TABLE+log4jLogs;CREATE+EXTERNAL+TABLE+log4jLogs(t1+string,t2+string,t3+string,t4+string,t5+string,t6+string,t7+string)+ROW+FORMAT+DELIMITED+FIELDS+TERMINATED+BY+' '+STORED+AS+TEXTFILE+LOCATION+'/example/data/';SELECT+t4+AS+sev,COUNT(*)+AS+count+FROM+log4jLogs+WHERE+t4+=+'[ERROR]'+AND+INPUT__FILE__NAME+LIKE+'%25.log'+GROUP+BY+t4;" -d statusdir="/example/curl" https://CLUSTERNAME.azurehdinsight.net/templeton/v1/hive
    ```

    Los parámetros siguientes se utilizan con esta solicitud:

   * **-d**: como `-G` no se usa, la solicitud establece como valor predeterminado el método POST. `-d` especifica los valores de datos que se envían con la solicitud.

     * **user.name** : el usuario que ejecuta el comando.
     * **execute** : las instrucciones HiveQL para ejecutar.
     * **statusdir**: el directorio donde se escribe el estado de este trabajo.

   Estas instrucciones realizan las acciones siguientes:
   
   * **DROP TABLE**: si la tabla ya existe, se elimina.
   * **CREATE EXTERNAL TABLE** : crea una tabla "externa" nueva en Hive. Las tablas externas solo almacenan la definición de tabla en Hive. Los datos permanecen en la ubicación original.

     > [!NOTE]
     > Las tablas externas se deben utilizar cuando se espera que un origen externo actualice los datos subyacentes. Por ejemplo, un proceso de carga de datos automatizado u otra operación de MapReduce.
     >
     > La eliminación de una tabla externa **no** elimina los datos, solamente la definición de tabla.

   * **ROW FORMAT**: indica cómo se da formato a los datos. Los campos de cada registro se separan mediante un espacio.
   * **STORED AS TEXTFILE LOCATION** : indica dónde se almacenan los datos (el directorio example/data) y que se almacenan como texto.
   * **SELECT**: selecciona un número de todas las filas donde la columna **t4** contiene el valor **[ERROR]**. Esta instrucción devuelve un valor de **3** porque hay tres filas que contienen este valor.

     > [!NOTE]
     > Observe que los espacios entre las instrucciones HiveQL se reemplazan por el carácter `+` cuando se usa con Curl. Los valores entre comillas que contengan un espacio, como el delimitador, no se reemplazarán por `+`.

   * **INPUT__FILE__NAME LIKE '%25.log'**: esta instrucción limita la búsqueda a solo aquellos archivos que terminan en .log.

     > [!NOTE]
     > `%25` es el formato codificado de URL de %, por lo que la condición real es `like '%.log'`. El % tiene que ser una dirección URL codificada, ya que se trata como carácter especial en las direcciones URL.

   Este comando devuelve un identificador de trabajo que se pueda usar para comprobar el estado del trabajo.

    ```json
       {"id":"job_1415651640909_0026"}
    ```

3. Para revisar el estado del trabajo, use el siguiente comando:

    ```bash
    curl -G -u admin -d user.name=admin https://CLUSTERNAME.azurehdinsight.net/templeton/v1/jobs/JOBID | jq .status.state
    ```

    Reemplace **JOBID** por el valor devuelto en el paso anterior. Por ejemplo, si el valor devuelto fuese `{"id":"job_1415651640909_0026"}`, entonces **JOBID** sería `job_1415651640909_0026`.

    Si se ha completado el trabajo, el estado es **SUCCEEDED**.

   > [!NOTE]
   > Esta solicitud de Curl devuelve un documento de notación de objetos JavaScript (JSON) con información acerca del trabajo. Jq se utiliza para recuperar solo el valor del estado.

4. Una vez que el estado del trabajo cambió a **SUCCEEDED**, puede recuperar los resultados del trabajo desde Azure Blob Storage. El parámetro `statusdir` pasado con la consulta contiene la ubicación del archivo de salida; en este caso, **/example/curl**. Esta dirección almacena el resultado en el directorio **example/curl** de los clústeres del almacenamiento predeterminado.

    Puede enumerar y descargar estos archivos mediante la [CLI de Azure](https://docs.microsoft.com/cli/azure/install-azure-cli). Para obtener más información sobre el uso de la CLI de Azure con Azure Storage, consulte el documento [Uso de la CLI de Azure 2.0 con Azure Storage](https://docs.microsoft.com/azure/storage/storage-azure-cli#create-and-manage-blobs).

5. Use las siguientes instrucciones para crear una nueva tabla "interna" llamada **errorLogs**.

    ```bash
    curl -u admin -d user.name=admin -d execute="set+hive.execution.engine=tez;CREATE+TABLE+IF+NOT+EXISTS+errorLogs(t1+string,t2+string,t3+string,t4+string,t5+string,t6+string,t7+string)+STORED+AS+ORC;INSERT+OVERWRITE+TABLE+errorLogs+SELECT+t1,t2,t3,t4,t5,t6,t7+FROM+log4jLogs+WHERE+t4+=+'[ERROR]'+AND+INPUT__FILE__NAME+LIKE+'%25.log';SELECT+*+from+errorLogs;" -d statusdir="/example/curl" https://CLUSTERNAME.azurehdinsight.net/templeton/v1/hive
    ```

    Estas instrucciones realizan las acciones siguientes:

   * **CREATE TABLE IF NOT EXISTS** : crea una tabla, si todavía no existe. Esta instrucción crea una tabla interna que se almacena en el almacenamiento de datos de Hive. Hive administra esta tabla.

     > [!NOTE]
     > A diferencia de las tablas externas , la eliminación de una tabla interna también elimina los datos subyacentes.

   * **STORED AS ORC** : almacena los datos en el formato Optimized Row Columnar (ORC). ORC es un formato altamente optimizado y eficiente para almacenar datos de Hive.
   * **INSERT OVERWRITE ... SELECT**: selecciona filas de la tabla **log4jLogs** que contienen **[ERROR]** y, a continuación, inserta los datos en la tabla **errorLogs**.
   * **SELECT**: selecciona todas las filas de la nueva tabla **errorLogs**.

6. Use el identificador de trabajo devuelto para revisar el estado del trabajo. Una vez que lo haya realizado correctamente, use la CLI de Azure tal como se describió anteriormente para descargar y ver los resultados. La salida debe contener tres líneas, todas las cuales contienen **[ERROR]**.

## <a id="nextsteps"></a>Pasos siguientes

Si desea obtener información general sobre Hive con HDInsight:

* [Uso de Hive con Hadoop en HDInsight](hdinsight-use-hive.md)

Para obtener información sobre otras formas en que puede trabajar con Hadoop en HDInsight:

* [Uso de Pig con Hadoop en HDInsight](hdinsight-use-pig.md)
* [Uso de MapReduce con Hadoop en HDInsight](hdinsight-use-mapreduce.md)

Si usa Tez con Hive, consulte los siguientes documentos para la información de depuración:

* [Use the Ambari Tez view on Linux-based HDInsight (Uso de la vista Tez de Ambari en HDInsight basado en Linux)](../hdinsight-debug-ambari-tez-view.md)

Para obtener más información sobre la API de REST usada en este documento, consulte el documento [WebHCat reference](https://cwiki.apache.org/confluence/display/Hive/WebHCat+Reference) (Referencia de WebHCat).

[hdinsight-sdk-documentation]: http://msdnstage.redmond.corp.microsoft.com/library/dn479185.aspx

[azure-purchase-options]: http://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/

[apache-tez]: http://tez.apache.org
[apache-hive]: http://hive.apache.org/
[apache-log4j]: http://en.wikipedia.org/wiki/Log4j
[hive-on-tez-wiki]: https://cwiki.apache.org/confluence/display/Hive/Hive+on+Tez
[import-to-excel]: http://azure.microsoft.com/documentation/articles/hdinsight-connect-excel-power-query/


[hdinsight-use-oozie]: hdinsight-use-oozie.md
[hdinsight-analyze-flight-data]: hdinsight-analyze-flight-delay-data.md




[hdinsight-provision]: hdinsight-hadoop-provision-linux-clusters.md
[hdinsight-submit-jobs]:submit-apache-hadoop-jobs-programmatically.md
[hdinsight-upload-data]: hdinsight-upload-data.md

[powershell-here-strings]: http://technet.microsoft.com/library/ee692792.aspx


