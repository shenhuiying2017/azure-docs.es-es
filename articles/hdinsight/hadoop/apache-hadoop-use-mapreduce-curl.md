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
ms.date: 12/04/2017
ms.author: larryfr
ms.openlocfilehash: dd3e5904ee21ee74da5adaa65abd7865a82c8b36
ms.sourcegitcommit: 7136d06474dd20bb8ef6a821c8d7e31edf3a2820
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/05/2017
---
# <a name="run-mapreduce-jobs-with-hadoop-on-hdinsight-using-rest"></a>Ejecución de trabajos de MapReduce con Hadoop en HDInsight con REST

Aprenda cómo usar la API de REST de WebHCat para ejecutar trabajos de MapReduce en un clúster de Hadoop en HDInsight. Curl se usa para demostrar cómo puede interactuar con HDInsight mediante solicitudes HTTP sin formato para ejecutar trabajos de MapReduce.

> [!NOTE]
> Si ya está familiarizado con el uso de servidores de Hadoop basado en Linux, pero no conoce HDInsight, consulte el documento [Lo que necesita saber acerca de Hadoop en HDInsight basado en Linux](../hdinsight-hadoop-linux-information.md).


## <a id="prereq"></a>Requisitos previos

* Un clúster de Hadoop en HDInsight
* [Curl](http://curl.haxx.se/)
* [jq](http://stedolan.github.io/jq/)

## <a id="curl"></a>Ejecución de trabajos de MapReduce mediante Curl

> [!NOTE]
> Al usar Curl o cualquier otra comunicación REST con WebHCat, debe autenticar las solicitudes proporcionando el nombre de usuario y la contraseña de administrador del clúster de HDInsight. Debe utilizar el nombre de clúster como parte del identificador URI utilizado para enviar las solicitudes al servidor.
>
> En el caso de los comandos de esta sección, reemplace **admin** por el usuario que se va a autenticar en el clúster. Reemplace **CLUSTERNAME** por el nombre del clúster. Cuando se le pida, escriba la contraseña de la cuenta de usuario.
>
> La API de REST se protege con la [autenticación de acceso básica](http://en.wikipedia.org/wiki/Basic_access_authentication). Siempre debe crear solicitudes usando HTTPS para así garantizar que las credenciales se envían de manera segura al servidor.


1. Desde una línea de comandos, utilice el siguiente comando para comprobar que puede conectarse al clúster de HDInsight.

    ```bash
    curl -u admin -G https://CLUSTERNAME.azurehdinsight.net/templeton/v1/status
    ```

    La respuesta que recibe es similar al código JSON siguiente:

        {"status":"ok","version":"v1"}

    Los parámetros que se utilizan en este comando son los siguientes:

   * **-u**: el nombre de usuario y la contraseña que se utilizan para autenticar la solicitud.
   * **-G**: indica que esta operación es una solicitud GET.

   El comienzo del identificador URI, **https://CLUSTERNAME.azurehdinsight.net/templeton/v1**, será el mismo para todas las solicitudes.

2. Para enviar un trabajo de MapReduce, utilice lo siguiente:

    ```bash
    curl -u admin -d user.name=admin -d jar=/example/jars/hadoop-mapreduce-examples.jar -d class=wordcount -d arg=/example/data/gutenberg/davinci.txt -d arg=/example/data/CurlOut https://CLUSTERNAME.azurehdinsight.net/templeton/v1/mapreduce/jar
    ```

    El final del identificador URI (/mapreduce/jar) indica a WebHCat que esta solicitud inicia un trabajo de MapReduce desde una clase en un archivo jar. Los parámetros que se utilizan en este comando son los siguientes:

   * **-d**: `-G` no se usa, así que la solicitud establece como valor predeterminado el método POST. `-d` especifica los valores de datos que se envían con la solicitud.
    * **user.name**: el usuario que ejecuta el comando.
    * **jar**: la ubicación del archivo jar que contiene la clase que se va a ejecutar.
    * **class**: la clase que contiene la lógica de MapReduce.
    * **arg**: los argumentos que se pasarán al trabajo de MapReduce. En este caso, el archivo de texto de entrada y el directorio que se utilizan para el resultado.

   Este comando debe devolver un identificador de trabajo que se pueda usar para comprobar el estado del trabajo:

       {"id":"job_1415651640909_0026"}

3. Para revisar el estado del trabajo, use el siguiente comando:

    ```bash
    curl -G -u admin -d user.name=admin https://CLUSTERNAME.azurehdinsight.net/templeton/v1/jobs/JOBID | jq .status.state
    ```

    Reemplace el valor de **JOBID** por el valor devuelto en el paso anterior. Por ejemplo, si el valor devuelto fue `{"id":"job_1415651640909_0026"}`, entonces el JOBID debería ser `job_1415651640909_0026`.

    Si se completa el trabajo, el estado devuelto es `SUCCEEDED`.

   > [!NOTE]
   > Esta solicitud de Curl devuelve un documento JSON con información acerca del trabajo. Jq se utiliza para recuperar solo el valor del estado.

4. Cuando el estado del trabajo haya cambiado a `SUCCEEDED`, puede recuperar los resultados del trabajo desde Azure Blob Storage. El parámetro `statusdir` transmitido con la consulta contiene la ubicación del archivo de salida. En este ejemplo, la ubicación es `/example/curl`. Esta dirección almacena el resultado del trabajo en los clústeres del almacenamiento predeterminado en `/example/curl`.

Puede enumerar y descargar estos archivos mediante la [CLI de Azure 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli). Para obtener más información sobre cómo trabajar con blobs de la CLI de Azure, consulte el documento [Uso de la CLI de Azure 2.0 con Azure Storage](../../storage/common/storage-azure-cli.md#create-and-manage-blobs).

## <a id="nextsteps"></a>Pasos siguientes

Para obtener información general sobre los trabajos de MapReduce en HDInsight:

* [Uso de MapReduce con Hadoop en HDInsight](hdinsight-use-mapreduce.md)

Para obtener información sobre otras maneras de trabajar con Hadoop en HDInsight:

* [Uso de Hive con Hadoop en HDInsight](hdinsight-use-hive.md)
* [Uso de Pig con Hadoop en HDInsight](hdinsight-use-pig.md)

Para obtener más información sobre la interfaz REST utilizada en este artículo, consulte la [referencia de WebHCat](https://cwiki.apache.org/confluence/display/Hive/WebHCat+Reference).