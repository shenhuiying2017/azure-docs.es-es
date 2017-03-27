---
title: Uso de Pig en Hadoop con Curl en HDInsight | Microsoft Docs
description: "En este documento aprenderá a utilizar Curl para ejecutar trabajos de Pig Latin en un clúster de Hadoop en HDInsight de Azure."
services: hdinsight
documentationcenter: 
author: Blackmist
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: ed5e10d1-4f47-459c-a0d6-7ff967b468c4
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 02/09/2017
ms.author: larryfr
translationtype: Human Translation
ms.sourcegitcommit: bb1ca3189e6c39b46eaa5151bf0c74dbf4a35228
ms.openlocfilehash: e7489b0485fe4779a72daeef0467a244bb55ff65
ms.lasthandoff: 03/18/2017


---
# <a name="run-pig-jobs-with-hadoop-on-hdinsight-by-using-curl"></a>Ejecución de trabajos de Pig con Hadoop en HDInsight con Curl

[!INCLUDE [pig-selector](../../includes/hdinsight-selector-use-pig.md)]

En este documento, aprenderá a utilizar Curl para ejecutar trabajos de Pig Latin en un clúster de HDInsight de Azure. El lenguaje de programación de Pig Latin le permite describir las transformaciones que se aplican a los datos de entrada para generar el resultado deseado.

CURL sirve para demostrar cómo se puede interactuar con HDInsight mediante solicitudes HTTP sin procesar para ejecutar, supervisar y recuperar los resultados de los trabajos de Pig. Esto funciona mediante la API de REST de WebHCat (antes conocida como Templeton) que proporciona el clúster de HDInsight.

> [!NOTE]
> Si ya está familiarizado con el uso de servidores de Hadoop basados en Linux, pero no conoce HDInsight, consulte [Información sobre el uso de HDInsight en Linux](hdinsight-hadoop-linux-information.md).

## <a id="prereq"></a>Requisitos previos

Necesitará lo siguiente para completar los pasos de este artículo:

* Un clúster de HDInsight de Azure (Hadoop en HDInsight) (basado en Windows o en Linux)

  > [!IMPORTANT]
  > Linux es el único sistema operativo que se usa en la versión 3.4 de HDInsight, o en las superiores. Para más información, consulte [El contrato de nivel de servicio para las versiones de clúster de HDInsight](hdinsight-component-versioning.md#hdi-version-32-and-33-nearing-deprecation-date).

* [Curl](http://curl.haxx.se/)
* [jq](http://stedolan.github.io/jq/)

## <a id="curl"></a>Ejecución de trabajos de Pig con Curl

> [!NOTE]
> Al usar Curl o cualquier otra comunicación REST con WebHCat, debe proporcionar el nombre de usuario y la contraseña del administrador del clúster de HDInsight para autenticar las solicitudes. También debe usar el nombre del clúster como parte del identificador uniforme de recursos (URI) que se usa para enviar las solicitudes al servidor.
>
> En el caso de los comandos que aparecen en esta sección, reemplace **USERNAME** por el usuario para autenticación en el clúster y **PASSWORD** por la contraseña de la cuenta de usuario. Reemplace **CLUSTERNAME** por el nombre del clúster.
>
> La API de REST se protege con la [autenticación de acceso básico](http://en.wikipedia.org/wiki/Basic_access_authentication). Siempre debe crear solicitudes usando HTTP segura (HTTPS) para así garantizar que las credenciales se envían de manera segura al servidor.

1. Desde una línea de comandos, utilice el siguiente comando para comprobar que puede conectarse al clúster de HDInsight.

        curl -u USERNAME:PASSWORD -G https://CLUSTERNAME.azurehdinsight.net/templeton/v1/status

    Debería recibir una respuesta similar a la siguiente:

        {"status":"ok","version":"v1"}

    Los parámetros que se utilizan en este comando son los siguientes:

    * **-u**: el nombre de usuario y la contraseña que se utilizan para autenticar la solicitud.
    * **-G**: indica que esta es una solicitud GET.

     El principio de la dirección URL, **https://CLUSTERNAME.azurehdinsight.net/templeton/v1**, será el mismo para todas las solicitudes. La ruta de acceso, **/status**, indica que la solicitud debe devolver el estado de WebHCat (también conocido como Templeton) al servidor.

2. Utilice el siguiente código para enviar un trabajo de Pig Latin al clúster:

        curl -u USERNAME:PASSWORD -d user.name=USERNAME -d execute="LOGS=LOAD+'/example/data/sample.log';LEVELS=foreach+LOGS+generate+REGEX_EXTRACT($0,'(TRACE|DEBUG|INFO|WARN|ERROR|FATAL)',1)+as+LOGLEVEL;FILTEREDLEVELS=FILTER+LEVELS+by+LOGLEVEL+is+not+null;GROUPEDLEVELS=GROUP+FILTEREDLEVELS+by+LOGLEVEL;FREQUENCIES=foreach+GROUPEDLEVELS+generate+group+as+LOGLEVEL,COUNT(FILTEREDLEVELS.LOGLEVEL)+as+count;RESULT=order+FREQUENCIES+by+COUNT+desc;DUMP+RESULT;" -d statusdir="/example/pigcurl" https://CLUSTERNAME.azurehdinsight.net/templeton/v1/pig

    Los parámetros que se utilizan en este comando son los siguientes:

    * **-d**: como `-G` no se usa, la solicitud establece como valor predeterminado el método POST. `-d` especifica los valores de datos que se envían con la solicitud.

    * **user.name**: el usuario que ejecuta el comando.
    * **execute**: las instrucciones de Pig Latin que se ejecutarán.
    * **statusdir**: el directorio donde se escribirá el estado de este trabajo.

    > [!NOTE]
    > Observe que los espacios en las instrucciones de Pig Latin se reemplazan por el carácter `+` cuando se utilizan con Curl.

    Este comando debe devolver un identificador de trabajo que se pueda usar para comprobar el estado del trabajo, por ejemplo:

        {"id":"job_1415651640909_0026"}

3. Para revisar el estado del trabajo, use el siguiente comando. Reemplace **JOBID** por el valor devuelto en el paso anterior. Por ejemplo, si el valor devuelto fuese `{"id":"job_1415651640909_0026"}`, entonces **JOBID** sería `job_1415651640909_0026`.

        curl -G -u USERNAME:PASSWORD -d user.name=USERNAME https://CLUSTERNAME.azurehdinsight.net/templeton/v1/jobs/JOBID | jq .status.state

    Si se completó el trabajo, el estado será **SUCCEEDED**.

    > [!NOTE]
    > Esta solicitud de Curl devuelve un documento de notación de objetos JavaScript (JSON) con información acerca del trabajo; jq se usa para recuperar solo el valor de estado.

## <a id="results"></a>Visualización de resultados

Cuando el estado del trabajo haya cambiado a **SUCCEEDED**, puede recuperar los resultados del trabajo desde el almacenamiento que usa el clúster. El parámetro `statusdir` pasado con la consulta contiene la ubicación del archivo de salida; en este caso, **/example/pigcurl**.

La memoria auxiliar para HDInsight puede ser Azure Storage o Azure Data Lake Store, y hay distintas formas de obtener los datos según cuál use. Para obtener más información sobre cómo trabajar con Azure Storage y Azure Data Lake Store, consulte la sección [HDFS, Blob Storage y Data Lake Store](hdinsight-hadoop-linux-information.md#hdfs-azure-storage-and-data-lake-store) del documento de HDInsight en Linux.

## <a id="summary"></a>Resumen

Como se muestra en este documento, puede utilizar la solicitud HTTP sin procesar para ejecutar, supervisar y ver los resultados de los trabajos de Hive en el clúster de HDInsight.

Para obtener más información sobre la interfaz REST utilizada en este artículo, consulte la [referencia de WebHCat](https://cwiki.apache.org/confluence/display/Hive/WebHCat+Reference).

## <a id="nextsteps"></a>Pasos siguientes

Para obtener información general sobre Pig en HDInsight:

* [Uso de Pig con Hadoop en HDInsight](hdinsight-use-pig.md)

Para obtener información sobre otras maneras de trabajar con Hadoop en HDInsight:

* [Uso de Hive con Hadoop en HDInsight](hdinsight-use-hive.md)
* [Uso de MapReduce con Hadoop en HDInsight](hdinsight-use-mapreduce.md)

