---
title: Uso de MapReduce y Curl con Hadoop en HDInsight (Azure) | Microsoft Docs
description: "Obtenga información acerca de cómo ejecutar de manera remota trabajos de MapReduce con Hadoop en HDInsight con Curl."
services: hdinsight
documentationcenter: 
author: Blackmist
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: bc6daf37-fcdc-467a-a8a8-6fb2f0f773d1
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 02/27/2018
ms.author: larryfr
ms.openlocfilehash: e48e9f833db86f01d944133c8a32d2c6b27b7b48
ms.sourcegitcommit: c765cbd9c379ed00f1e2394374efa8e1915321b9
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 02/28/2018
---
# <a name="run-mapreduce-jobs-with-hadoop-on-hdinsight-using-rest"></a>Ejecución de trabajos de MapReduce con Hadoop en HDInsight con REST

Aprenda cómo usar la API de REST de WebHCat para ejecutar trabajos de MapReduce en un clúster de Hadoop en HDInsight. Curl se usa para demostrar cómo puede interactuar con HDInsight mediante solicitudes HTTP sin formato para ejecutar trabajos de MapReduce.

> [!NOTE]
> Si ya está familiarizado con el uso de servidores de Hadoop basado en Linux, pero no conoce HDInsight, consulte el documento [Lo que necesita saber acerca de Hadoop en HDInsight basado en Linux](../hdinsight-hadoop-linux-information.md).


## <a id="prereq"></a>Requisitos previos

* Un clúster de Hadoop en HDInsight
* Windows PowerShell o [Curl](http://curl.haxx.se/) y [jq](http://stedolan.github.io/jq/)

## <a id="curl"></a>Ejecute un trabajo MapReduce

> [!NOTE]
> Al usar Curl o cualquier otra comunicación REST con WebHCat, debe autenticar las solicitudes proporcionando el nombre de usuario y la contraseña de administrador del clúster de HDInsight. Debe utilizar el nombre de clúster como parte del identificador URI utilizado para enviar las solicitudes al servidor.
>
> La API de REST se protege con la [autenticación de acceso básica](http://en.wikipedia.org/wiki/Basic_access_authentication). Siempre debe crear solicitudes usando HTTPS para así garantizar que las credenciales se envían de manera segura al servidor.

1. Para establecer el inicio de sesión del clúster para los scripts de este documento, use uno de los siguientes comandos:

    ```bash
    read -p "Enter your cluster login account name: " LOGIN
    ```

    ```powershell
    $creds = Get-Credential -UserName admin -Message "Enter the cluster login name and password"
    ```

2. Para establecer el nombre del clúster, use uno de los siguientes comandos:

    ```bash
    read -p "Enter the HDInsight cluster name: " CLUSTERNAME
    ```

    ```powershell
    $clusterName = Read-Host -Prompt "Enter the HDInsight cluster name"
    ```

3. Desde una línea de comandos, utilice el siguiente comando para comprobar que puede conectarse al clúster de HDInsight.

    ```bash
    curl -u $LOGIN -G https://$CLUSTERNAME.azurehdinsight.net/templeton/v1/status
    ```

    ```powershell
    $resp = Invoke-WebRequest -Uri "https://$clustername.azurehdinsight.net/templeton/v1/status" `
        -Credential $creds `
        -UseBasicParsing
    $resp.Content
    ```

    La respuesta que recibe es similar al código JSON siguiente:

        {"status":"ok","version":"v1"}

    Los parámetros que se utilizan en este comando son los siguientes:

   * **-u**: el nombre de usuario y la contraseña que se utilizan para autenticar la solicitud.
   * **-G**: indica que esta operación es una solicitud GET.

   El comienzo del identificador URI, **https://CLUSTERNAME.azurehdinsight.net/templeton/v1**, será el mismo para todas las solicitudes.

4. Para enviar un trabajo de MapReduce, utilice lo siguiente:

    ```bash
    JOBID=`curl -u $LOGIN -d user.name=$LOGIN -d jar=/example/jars/hadoop-mapreduce-examples.jar -d class=wordcount -d arg=/example/data/gutenberg/davinci.txt -d arg=/example/data/output https://$CLUSTERNAME.azurehdinsight.net/templeton/v1/mapreduce/jar | jq .id`
    echo $JOBID
    ```

    ```powershell
    $reqParams = @{}
    $reqParams."user.name" = "admin"
    $reqParams.jar = "/example/jars/hadoop-mapreduce-examples.jar"
    $reqParams.class = "wordcount"
    $reqParams.arg = @()
    $reqParams.arg += "/example/data/gutenberg/davinci.txt"
    $reqparams.arg += "/example/data/output"
    $resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/templeton/v1/mapreduce/jar" `
       -Credential $creds `
       -Body $reqParams `
       -Method POST `
       -UseBasicParsing
    $jobID = (ConvertFrom-Json $resp.Content).id
    $jobID
    ```

    El final del identificador URI (/mapreduce/jar) indica a WebHCat que esta solicitud inicia un trabajo de MapReduce desde una clase en un archivo jar. Los parámetros que se utilizan en este comando son los siguientes:

   * **-d**: `-G` no se usa, así que la solicitud establece como valor predeterminado el método POST. `-d` especifica los valores de datos que se envían con la solicitud.
    * **user.name**: el usuario que ejecuta el comando.
    * **jar**: la ubicación del archivo jar que contiene la clase que se va a ejecutar.
    * **class**: la clase que contiene la lógica de MapReduce.
    * **arg**: los argumentos que se pasarán al trabajo de MapReduce. En este caso, el archivo de texto de entrada y el directorio que se utilizan para el resultado.

   Este comando debe devolver un identificador de trabajo que se pueda usar para comprobar el estado del trabajo:

       job_1415651640909_0026

5. Para revisar el estado del trabajo, use el siguiente comando:

    ```bash
    curl -G -u $LOGIN -d user.name=$LOGIN https://$CLUSTERNAME.azurehdinsight.net/templeton/v1/jobs/$JOBID | jq .status.state
    ```

    ```powershell
    $reqParams=@{"user.name"="admin"}
    $resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/templeton/v1/jobs/$jobID" `
       -Credential $creds `
       -Body $reqParams `
       -UseBasicParsing
    # ConvertFrom-JSON can't handle duplicate names with different case
    # So change one to prevent the error
    $fixDup=$resp.Content.Replace("jobID","job_ID")
    (ConvertFrom-Json $fixDup).status.state
    ```

    Si se completa el trabajo, el estado devuelto es `SUCCEEDED`.

   > [!NOTE]
   > Esta solicitud de Curl devuelve un documento JSON con información acerca del trabajo. Jq se utiliza para recuperar solo el valor del estado.

6. Cuando el estado del trabajo haya cambiado a `SUCCEEDED`, puede recuperar los resultados del trabajo desde Azure Blob Storage. El parámetro `statusdir` transmitido con la consulta contiene la ubicación del archivo de salida. En este ejemplo, la ubicación es `/example/curl`. Esta dirección almacena el resultado del trabajo en los clústeres del almacenamiento predeterminado en `/example/curl`.

Puede enumerar y descargar estos archivos mediante la [CLI de Azure 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli). Para obtener más información sobre cómo trabajar con blobs de la CLI de Azure, consulte el documento [Uso de la CLI de Azure 2.0 con Azure Storage](../../storage/common/storage-azure-cli.md#create-and-manage-blobs).

## <a id="nextsteps"></a>Pasos siguientes

Para obtener información general sobre los trabajos de MapReduce en HDInsight:

* [Uso de MapReduce con Hadoop en HDInsight](hdinsight-use-mapreduce.md)

Para obtener información sobre otras maneras de trabajar con Hadoop en HDInsight:

* [Uso de Hive con Hadoop en HDInsight](hdinsight-use-hive.md)
* [Uso de Pig con Hadoop en HDInsight](hdinsight-use-pig.md)

Para obtener más información sobre la interfaz REST utilizada en este artículo, consulte la [referencia de WebHCat](https://cwiki.apache.org/confluence/display/Hive/WebHCat+Reference).