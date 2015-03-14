<properties
   pageTitle="Uso de MapReduce con Hadoop en HDinsight | Azure"
   description="Obtenga información acerca de cómo ejecutar de manera remota trabajos de MapReduce con Hadoop en HDInsight con Curl."
   services="hdinsight"
   documentationCenter=""
   authors="Blackmist"
   manager="paulettm"
   editor="cgronlun"/>

<tags
   ms.service="hdinsight"
   ms.devlang=""
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="02/18/2015"
   ms.author="larryfr"/>

# Ejecución de trabajos de MapReduce con Hadoop en HDInsight con Curl

[AZURE.INCLUDE [mapreduce-selector](../includes/hdinsight-selector-use-mapreduce.md)]

En este documento, obtendrá información sobre cómo utilizar Curl para ejecutar trabajos de MapReduce en un clúster de Hadoop en HDInsight. 

Curl se utiliza para demostrar cómo se puede interactuar con HDInsight mediante solicitudes HTTP sin formato para ejecutar trabajos de MapReduce. Esto funciona mediante la API de REST de WebHCat (anteriormente conocida como Templeton), proporcionada por el clúster de HDInsight.

> [AZURE.NOTE] Si ya está familiarizado con el uso de servidores Hadoop basados en Linux, pero no tiene experiencia con HDInsight, consulte <a href="../hdinsight-hadoop-linux-information/" target="_blank">Lo que necesita saber acerca de Hadoop en HDInsight basado en Linux</a>.

## <a id="prereq"></a>Requisitos previos

Necesitará lo siguiente para completar los pasos de este artículo.

* Un clúster de Hadoop en HDInsight (Linux o basado en Windows)

* <a href="http://curl.haxx.se/" target="_blank">Curl</a>

* <a href="http://stedolan.github.io/jq/" target="_blank">jq</a>

## <a id="curl"></a>Ejecución de trabajos de MapReduce mediante Curl

> [AZURE.NOTE] Al usar Curl o cualquier otra comunicación REST con WebHCat, debe autenticar las solicitudes proporcionando el nombre de usuario o la contraseña de administrador del clúster de HDInsight. También debe utilizar el nombre de clúster como parte del URI utilizado para enviar las solicitudes al servidor.
> 
> Para los comandos de esta sección, reemplace **USERNAME** por el usuario que se autenticará en el clúster y **PASSWORD** por la contraseña de la cuenta de usuario. Reemplace **CLUSTERNAME** por el nombre del clúster.
> 
> La API de REST se protege utilizando <a href="http://en.wikipedia.org/wiki/Basic_access_authentication" target="_blank">la autenticación básica</a>. Siempre debe realizar las solicitudes mediante HTTPS para asegurarse de que sus credenciales se envían de forma segura al servidor.

1. Desde una línea de comandos, utilice el siguiente comando para comprobar que puede conectarse al clúster de HDInsight. 

        curl -u USERNAME:PASSWORD -G https://CLUSTERNAME.azurehdinsight.net/templeton/v1/status

    Debería recibir una respuesta similar a la siguiente.

        {"status":"ok","version":"v1"}

    Los parámetros utilizados en este comando son los siguientes.

    * **-u**: el nombre de usuario y la contraseña utilizados para autenticar la solicitud
    * **-G**: indica que se trata de una solicitud GET

    El comienzo de la dirección URI, **https://CLUSTERNAME.azurehdinsight.net/templeton/v1**, será el mismo para todas las solicitudes. 

2. Para enviar un trabajo de MapReduce, utilice lo siguiente.

		curl -u USERNAME:PASSWORD -d user.name=USERNAME -d jar=wasb:///example/jars/hadoop-mapreduce-examples.jar -d class=wordcount -d arg=wasb:///example/data/gutenberg/davinci.txt -d arg=wasb:///example/data/CurlOut https://CLUSTERNAME.azurehdinsight.net/templeton/v1/mapreduce/jar

    El final del URI (/mapreduce/jar) indica a WebHCat que esta solicitud iniciará un trabajo de MapReduce desde una clase en un archivo jar. Los parámetros utilizados en este comando son los siguientes.

	* **-d**: como `-G` no se utiliza, la solicitud tiene como valor predeterminado el método POST. `-d`: especifica los valores de datos que se envían con la solicitud

        * **user.name**: el usuario que ejecuta el comando
        * **jar**: la ubicación del archivo jar que contiene la clase que se va a ejecutar
        * **class**: la clase que contiene la lógica de MapReduce
        * **arg**: los argumentos que se pasarán al trabajo de MapReduce. En este caso, el archivo de texto de entrada y el directorio utilizado para la salida

    Este comando debe devolver un identificador de trabajo que pueda utilizarse para comprobar el estado del trabajo.

        {"id":"job_1415651640909_0026"}

3. Para comprobar el estado del trabajo, utilice el siguiente comando. Reemplace el valor de **JOBID** por el valor devuelto en el paso anterior. Por ejemplo, si el valor devuelto es `{"id":"job_1415651640909_0026"}`, JOBID sería `job_1415651640909_0026`.

        curl -G -u USERNAME:PASSWORD -d user.name=USERNAME https://CLUSTERNAME.azurehdinsight.net/templeton/v1/jobs/JOBID | jq .status.state

	Si el trabajo se ha completado, el estado será "SUCCEEDED".

    > [AZURE.NOTE] Esta solicitud curl devuelve un documento JSON con información sobre el trabajo; jq se utiliza para recuperar solo el valor de estado. 

4. Una vez que se ha cambiado el estado del trabajo a **SUCCEEDED**, puede recuperar los resultados del trabajo de almacenamiento Blob de Azure. El parámetro `statusdir` pasado con la consulta contiene la ubicación del archivo de salida; en este caso, **wasb:///example/curl**. Esta dirección almacena el resultado del trabajo en el directorio **example/curl** en el contenedor de almacenamiento predeterminado utilizado por el clúster de HDInsight.

Puede enumerar y descargar estos archivos mediante la <a href="../xplat-cli/" target="_blank">Interfaz de línea de comandos entre plataformas de Azure (xplat-cli)</a>. Por ejemplo, para enumerar los archivos de **example/curl**, utilice el siguiente comando.

	azure storage blob list <container-name> example/curl

Para descargar un archivo, utilice lo siguiente.

	azure storage blob download <container-name> <blob-name> <destination-file>

> [AZURE.NOTE] Debe especificar el nombre de la cuenta de almacenamiento que contiene el blob mediante los parámetros `-a` y `-k`, o establecer las variable de entorno **AZURE\_STORAGE\_ACCOUNT** y **AZURE\_STORAGE\_ACCESS\_KEY**. Consulte <a href="../hdinsight-upload-data/" target="_blank" for more information.

## <a id="summary"></a>Resumen

Como se muestra en este documento, puede utilizar la solicitud HTTP sin formato para ejecutar, supervisar y ver los resultados de los trabajos de Hive en el clúster de HDInsight.

Para obtener más información sobre la interfaz REST utilizada en este artículo, consulte la [referencia de WebHCat](https://cwiki.apache.org/confluence/display/Hive/WebHCat+Reference).

## <a id="nextsteps"></a>Pasos siguientes

Para obtener información general sobre los trabajos de MapReduce en HDInsight.

* [Uso de MapReduce con Hadoop en HDInsight](../hdinsight-use-mapreduce/)

Para obtener información sobre otras maneras en que puede trabajar con Hadoop en HDInsight.

* [Uso de Hive con Hadoop en HDInsight](../hdinsight-use-hive/)

* [Uso de Pig con Hadoop en HDInsight](../hdinsight-use-pig/)
<!--HONumber=45--> 
