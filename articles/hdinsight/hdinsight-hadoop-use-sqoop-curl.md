<properties
   pageTitle="Uso de Sqoop con Hadoop en HDInsight con Curl| Microsoft Azure"
   description="Obtenga información sobre cómo enviar remotamente trabajos a HDInsight usando Curl."
   services="hdinsight"
   documentationCenter=""
   authors="mumian"
   manager="jhubbard"
   editor="cgronlun"
	tags="azure-portal"/>

<tags
   ms.service="hdinsight"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="07/25/2016"
   ms.author="jgao"/>

#Ejecución de trabajos de Sqoop con Hadoop en HDInsight con Curl

[AZURE.INCLUDE [sqoop-selector](../../includes/hdinsight-selector-use-sqoop.md)]

En este documento, aprenderá a usar Curl para ejecutar trabajos de Sqoop en un clúster de Hadoop en HDInsight de Azure.

Curl sirve para demostrar cómo se puede interactuar con HDInsight usando solicitudes HTTP sin procesar para ejecutar, supervisar y recuperar los resultados de los trabajos de Sqoop. Esto funciona mediante la API de REST de WebHCat (conocida anteriormente como Templeton) proporcionada por el clúster de HDInsight.

> [AZURE.NOTE] Si ya está familiarizado con el uso de servidores de Hadoop basado en Linux, pero no conoce HDInsight, consulte [Lo que necesita saber acerca de Hadoop en HDInsight basado en Linux](hdinsight-hadoop-linux-information.md).

##Requisitos previos

Para completar los pasos de este artículo, necesitará lo siguiente:

* Un clúster de Hadoop en HDInsight (basado en Linux o Windows)

* [Curl](http://curl.haxx.se/)

* [jq](http://stedolan.github.io/jq/)

##Envío de trabajos de Sqoop mediante Curl

> [AZURE.NOTE] Al usar Curl o cualquier otra comunicación REST con WebHCat, debe proporcionar el nombre de usuario y la contraseña del administrador del clúster de HDInsight para autenticar las solicitudes. También debe usar el nombre del clúster como parte del identificador uniforme de recursos (URI) que se usa para enviar las solicitudes al servidor.
>
> En el caso de los comandos que aparecen en esta sección, reemplace **USERNAME** por el usuario para autenticación en el clúster y **PASSWORD** por la contraseña de la cuenta de usuario. Reemplace **CLUSTERNAME** por el nombre del clúster.
>
> La API de REST se protege con la [autenticación básica](http://en.wikipedia.org/wiki/Basic_access_authentication). Siempre debe crear solicitudes usando HTTP segura (HTTPS) para así garantizar que las credenciales se envían de manera segura al servidor.

1. Desde una línea de comandos, utilice el siguiente comando para comprobar que puede conectarse al clúster de HDInsight.

        curl -u USERNAME:PASSWORD -G https://CLUSTERNAME.azurehdinsight.net/templeton/v1/status

    Debería recibir una respuesta similar a la siguiente:

        {"status":"ok","version":"v1"}

    Los parámetros que se utilizan en este comando son los siguientes:

    * **-u**: el nombre de usuario y la contraseña que se utilizan para autenticar la solicitud.
    * **-G**: indica que esta es una solicitud GET.

    El comienzo de la dirección URL, **https://CLUSTERNAME.azurehdinsight.net/templeton/v1**, será el mismo para todas las solicitudes. La ruta de acceso, **/status**, indica que la solicitud debe devolver un estado de WebHCat (también conocido como Templeton) al servidor.

2. Para enviar el trabajo de sqoop, use lo siguiente:


        curl -u USERNAME:PASSWORD -d user.name=USERNAME -d command="export --connect jdbc:sqlserver://SQLDATABASESERVERNAME.database.windows.net;user=USERNAME@SQLDATABASESERVERNAME;password=PASSWORD;database=SQLDATABASENAME --table log4jlogs --export-dir /tutorials/usesqoop/data --input-fields-terminated-by \0x20 -m 1" -d statusdir="wasbs:///example/curl" https://CLUSTERNAME.azurehdinsight.net/templeton/v1/sqoop

    Los parámetros que se utilizan en este comando son los siguientes:

    * **-d** : desde dado que `-G` no se usa, la solicitud tiene como valor predeterminado el método POST. `-d` especifica los valores de datos que se envían con la solicitud.

        * **user.name**: el usuario que ejecuta el comando.

        * **command**: el comando de Sqoop para ejecutar.

        * **statusdir**: el directorio donde se escribirá el estado de este trabajo.

    Este comando debe devolver un identificador de trabajo que se pueda usar para comprobar el estado del trabajo.

        {"id":"job_1415651640909_0026"}

3. Para revisar el estado del trabajo, use el siguiente comando. Reemplace **JOBID** por el valor devuelto en el paso anterior. Por ejemplo, si el valor devuelto fue `{"id":"job_1415651640909_0026"}`, entonces **JOBID** sería `job_1415651640909_0026`.

        curl -G -u USERNAME:PASSWORD -d user.name=USERNAME https://CLUSTERNAME.azurehdinsight.net/templeton/v1/jobs/JOBID | jq .status.state

	Si se completó el trabajo, el estado será **SUCCEEDED**.

    > [AZURE.NOTE] Esta solicitud de Curl devuelve un documento de notación de objetos JavaScript (JSON) con información acerca del trabajo; jq se usa para recuperar solo el valor de estado.

4. Una vez que el estado del trabajo cambió a **SUCCEEDED**, puede recuperar los resultados del trabajo desde el almacenamiento de blobs de Azure. El parámetro `statusdir` transmitido con la consulta contiene la ubicación del archivo de salida; en este caso, **wasbs:///example/curl**. Esta dirección almacena la salida del trabajo en el directorio **example/curl** en el contenedor de almacenamiento predeterminado que su clúster de HDInsight usa.

    Puede enumerar y descargar estos archivos mediante la [CLI de Azure](../xplat-cli-install.md). Por ejemplo, para enumerar los archivos existentes en **example/curl**, use el siguiente comando.

		azure storage blob list <container-name> example/curl

	Para descargar un archivo, use lo siguiente:

		azure storage blob download <container-name> <blob-name> <destination-file>

	> [AZURE.NOTE] Debe especificar el nombre de la cuenta de almacenamiento que contiene el blob usando los parámetros `-a` y `-k` o bien, definir las variables de entorno **AZURE\_STORAGE\_ACCOUNT** y **AZURE\_STORAGE\_ACCESS\_KEY**. Vea <a href="hdinsight-upload-data.md" target="\_blank" para obtener más información.

##Limitaciones

* Exportación masiva: con HDInsight basado en Linux, el conector Sqoop que se utiliza para exportar datos a Microsoft SQL Server o Base de datos SQL Azure no es compatible actualmente con las inserciones masivas.

* Procesamiento por lotes: con HDInsight basado en Linux, cuando se usa `-batch` al realizar inserciones, Sqoop realizará varias inserciones en lugar de procesar por lotes las operaciones de inserción.

##Resumen

Tal como se ha mostrado en este documento, puede usar una solicitud HTTP sin procesar para ejecutar, supervisar y ver los resultados de los trabajos de Sqoop en su clúster de HDInsight.

Para obtener más información sobre la interfaz REST utilizada en este artículo, consulte la referencia <a href="https://sqoop.apache.org/docs/1.99.3/RESTAPI.html" target="_blank">Sqoop REST API Guide</a>.

##Pasos siguientes

Si desea obtener información general sobre Hive con HDInsight:

* [Uso de Sqoop con Hadoop en HDInsight (Windows)](hdinsight-use-sqoop.md)

Para obtener información sobre otras formas en que puede trabajar con Hadoop en HDInsight:

* [Uso de Hive con Hadoop en HDInsight](hdinsight-use-hive.md)

* [Uso de Pig con Hadoop en HDInsight](hdinsight-use-pig.md)

* [Uso de MapReduce con Hadoop en HDInsight](hdinsight-use-mapreduce.md)

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




[hdinsight-provision]: hdinsight-provision-clusters.md
[hdinsight-submit-jobs]: hdinsight-submit-hadoop-jobs-programmatically.md
[hdinsight-upload-data]: hdinsight-upload-data.md

[powershell-here-strings]: http://technet.microsoft.com/library/ee692792.aspx

<!---HONumber=AcomDC_0914_2016-->