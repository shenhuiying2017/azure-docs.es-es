<properties
   pageTitle="Uso de Hadoop Pig en HDInsight | Azure"
   description="Sepa cómo enviar remotamente trabajos de Pig a HDInsight mediante el uso de Curl."
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

#Ejecución de consultas de Hive con Hadoop en HDInsight con Curl

[AZURE.INCLUDE [hive-selector](../includes/hdinsight-selector-use-hive.md)]

En este documento, aprenderá a usar Curl para ejecutar consultas de Hive en un clúster de Hadoop en HDInsight. 

Curl se usa para mostrar cómo se puede interactuar con HDInsight usando solicitudes HTTP sin procesar para ejecutar, supervisar y recuperar los resultados de las consultas de Hive. Esto funciona mediante la API de REST de WebHCat (conocida anteriormente como Templeton) proporcionada por el clúster de HDInsight.

> [AZURE.NOTE] Si ya está familiarizado con el uso de servidores de Hadoop basado en Linux, pero no conoce HDInsight, consulte <a href="../hdinsight-hadoop-linux-information/" target="_blank">Lo que necesita saber acerca de Hadoop en HDInsight basado en Linux</a>.

##<a id="prereq"></a>Requisitos previos

Necesitará lo siguiente para completar los pasos de este artículo.

* Un clúster de Hadoop en HDInsight (basado en Linux o Windows)

* <a href="http://curl.haxx.se/" target="_blank">Curl</a>

* <a href="http://stedolan.github.io/jq/" target="_blank">jq</a>

##<a id="curl"></a>Ejecución de consultas de Hive con Curl

> [AZURE.NOTE] Cuando use Curl o cualquier otra comunicación de REST con WebHCat, debe proporcionar el nombre de usuario y la contraseña del administrador del clúster de HDInsight para autenticar las solicitudes. También debe usar el nombre del clúster como parte del URI que se usa para enviar las solicitudes al servidor.
> 
> En el caso de los comandos que aparecen en esta sección, reemplace **NOMBREDEUSUARIO** por el usuario para autenticación en el clúster y **CONTRASEÑA** por la contraseña de la cuenta de usuario. Reemplace **NOMBREDELCLÚSTER** por el nombre de su clúster.
> 
> La API de REST se protege con la <a href="http://en.wikipedia.org/wiki/Basic_access_authentication" target="_blank">autenticación básica</a>. Siempre debe crear solicitudes usando HTTPS para así garantizar que las credenciales se envían de manera segura al servidor.

1. Desde una línea de comandos, use el siguiente comando para comprobar que puede conectarse al clúster de HDInsight.

        curl -u USERNAME:PASSWORD -G https://CLUSTERNAME.azurehdinsight.net/templeton/v1/status

    Debiera recibir una respuesta similar a la siguiente.

        {"status":"ok","version":"v1"}

    Los parámetros que se utilizan en este comando son los siguientes.

    * **-u**: el nombre de usuario y la contraseña que se utilizan para autenticar la solicitud.
    * **-G**: indica que esta es una solicitud GET.

    El comienzo de la dirección URL, **https://CLUSTERNAME.azurehdinsight.net/templeton/v1**, será el mismo para todas las solicitudes. La ruta de acceso, **/status**, indica que la solicitud debe devolver el estado de WebHCat (también conocido como Templeton) al servidor. También puede solicitar la versión de Hive con el siguiente comando.

        curl -u USERNAME:PASSWORD -G https://CLUSTERNAME.azurehdinsight.net/templeton/v1/version/hive

    Debiera devolver una respuesta similar a la siguiente.

        {"module":"hive","version":"0.13.0.2.1.6.0-2103"}

2. Use lo siguiente para crear una tabla nueva llamada **log4jLogs**.

        curl -u USERNAME:PASSWORD -d user.name=USERNAME -d execute="DROP+TABLE+log4jLogs;CREATE+EXTERNAL+TABLE+log4jLogs(t1+string,t2+string,t3+string,t4+string,t5+string,t6+string,t7+string)+ROW+FORMAT+DELIMITED+FIELDS+TERMINATED+BY+' '+STORED+AS+TEXTFILE+LOCATION+'wasb:///example/data/';SELECT+t4+AS+sev,COUNT(*)+AS+count+FROM+log4jLogs+WHERE+t4+=+'[ERROR]'+GROUP+BY+t4;" -d statusdir="wasb:///example/curl" https://CLUSTERNAME.azurehdinsight.net/templeton/v1/hive

    Los parámetros que se utilizan en este comando son los siguientes.

    * **-d**: como `-G` no se usa, la solicitud establece como valor predeterminado el método POST. `-d` especifica los valores de datos que se envían con la solicitud.

        * **user.name**: el usuario que ejecuta el comando.
        
        * **execute**: las instrucciones HiveQL para ejecutar
        
        * **statusdir**: el directorio en el que se escribirá el estado de este trabajo

    Estas instrucciones realizan las acciones siguientes.

    * **DROP TABLE**: elimina la tabla y el archivo de datos si la tabla ya existe.
    
    * **CREATE EXTERNAL TABLE**: crea una tabla "externa" nueva en Hive. Las tablas externas solamente almacenan la definición de tabla en Hive (los datos se dejan en la ubicación original).

		> [AZURE.NOTE] Las tablas externas se deben usar cuando espera que un origen externo, como por ejemplo un proceso de carga de datos automático, u otra operación MapReduce, actualice los datos subyacentes, pero siempre desea que las consultas de Hive usen los datos más recientes.
		>
		> La eliminación de una tabla externa **no** elimina los datos, solamente la definición de tabla.

    * **ROW FORMAT**: indica cómo se da formato a los datos de Hive. En este caso, los campos de cada registro se separan mediante un espacio.
    
    * **STORED AS TEXTFILE LOCATION**: indica a Hive dónde se almacenan los datos (el directorio de ejemplos/datos) y que se almacenen como texto.
    
    * **SELECT**: permite seleccionar un número de todas las filas donde la columna **t4** contiene el valor **[ERROR]**. Esto debe devolver un valor de **3** ya que hay tres filas que contienen este valor.

    > [AZURE.NOTE] Observe que los espacios entre las instrucciones HiveQL se reemplazan por el carácter `+` cuando se usa con Curl. Los valores entre comillas que contengan un espacio, como el delimitador, no se reemplazarán por `+`.

    Este comando debe devolver un identificador de trabajo que se pueda usar para comprobar el estado del trabajo.

        {"id":"job_1415651640909_0026"}

3. Para revisar el estado del trabajo, use el siguiente comando. Reemplace **JOBID** con el valor devuelto en el paso anterior. Por ejemplo, si el valor devuelto fue `{"id":"job_1415651640909_0026"}`, JOBID debiera ser `job_1415651640909_0026`.

        curl -G -u USERNAME:PASSWORD -d user.name=USERNAME https://CLUSTERNAME.azurehdinsight.net/templeton/v1/jobs/JOBID | jq .status.state

	Si se completó el trabajo, el estado será "SUCCEEDED".

    > [AZURE.NOTE] Esta solicitud de Curl devuelve un documento JSON con información acerca del trabajo; jq se usa para recuperar solo el valor de estado. 

4. Una vez que el estado del trabajo cambió a **SUCCEEDED**, puede recuperar los resultados del trabajo desde el almacenamiento de blobs de Azure. El parámetro `statusdir` transmitido con la consulta contiene la ubicación del archivo de salida; en este caso, **wasb:///example/curl**. Esta dirección almacena la salida del trabajo en el directorio **example/curl** en el contenedor de almacenamiento predeterminado que su clúster de HDInsight usa.

    Puede enumerar y descargar estos archivos mediante la <a href="../xplat-cli/" target="_blank">interfaz de línea de comandos multiplataforma de Azure (xplat-cli)</a>. Por ejemplo, para enumerar los archivos existentes en **example/curl**, use el siguiente comando.

		azure storage blob list <container-name> example/curl

	Para descargar un archivo, use lo siguiente.

		azure storage blob download <container-name> <blob-name> <destination-file>

	> [AZURE.NOTE] Debe especificar el nombre de la cuenta de almacenamiento que contiene el blob mediante los parámetros `-a` y `-k`, o bien, definir las variables de entorno **AZURE\_STORAGE\_ACCOUNT** y **AZURE\_STORAGE\_ACCESS\_KEY**. Consulte <a href="../hdinsight-upload-data/" target="_blank" for more information.

6. Use las siguientes instrucciones para crear una nueva tabla "interna" llamada **errorLogs**.

        curl -u USERNAME:PASSWORD -d user.name=USERNAME -d execute="CREATE+TABLE+IF+NOT+EXISTS+errorLogs(t1+string,t2+string,t3+string,t4+string,t5+string,t6+string,t7+string)+STORED+AS+ORC;INSERT+OVERWRITE+TABLE+errorLogs+SELECT+t1,t2,t3,t4,t5,t6,t7+FROM+log4jLogs+WHERE+t4+=+'[ERROR]';SELECT+*+from+errorLogs;" -d statusdir="wasb:///example/curl" https://CLUSTERNAME.azurehdinsight.net/templeton/v1/hive

    Estas instrucciones realizan las acciones siguientes.

    * **CREATE TABLE IF NOT EXISTS** : crea una tabla, si todavía no existe. Dado que la palabra clave**EXTERNAL** no se usa, se trata de una tabla "interna", que se almacena en el almacén de datos de Hive y es administrada por Hive.

		> [AZURE.NOTE] A diferencia de las tablas **EXTERNAL**, la eliminación de una tabla interna eliminará también los datos subyacentes.

    * **STORED AS ORC**: almacena los datos en formato Optimized Row Columnar (ORC). Se trata de un formato altamente optimizado y eficiente para almacenar datos de Hive
    * **INSERT OVERWRITE ... SELECT**: selecciona filas desde la tabla **log4jLogs** que contiene **[ERROR]** y, a continuación, inserta los datos en la tabla **errorLogs**.
    * **SELECT**: selecciona todas las filas de la nueva tabla **errorLogs**.

7. Use el identificador de trabajo devuelto para revisar el estado del trabajo. Una vez que lo haya hecho con éxito, use la xplat-cli tal como se describió anteriormente para descargar y ver los resultados. La salida debe contener tres líneas, todas las cuales contienen **[ERROR]**.


##<a id="summary"></a>Resumen

Tal como se demostró en este documento, puede usar una solicitud HTTP sin procesar para ejecutar, supervisar y ver los resultados de los trabajos de Hive en su clúster de HDInsight.

Para obtener más información sobre la interfaz REST utilizada en este artículo, consulte la <a href="https://cwiki.apache.org/confluence/display/Hive/WebHCat+Reference" target="_blank">referencia de WebHCat</a>.

##<a id="nextsteps"></a>Pasos siguientes

Si desea obtener información general sobre Hive con HDInsight.

* [Uso de Hive con Hadoop en HDInsight](../hdinsight-use-hive/)

Para obtener información sobre otras formas en que puede trabajar con Hadoop en HDInsight:

* [Uso de Pig con Hadoop en HDInsight](../hdinsight-use-pig/)

* [Uso de MapReduce con Hadoop en HDInsight](../hdinsight-use-mapreduce/)


[hdinsight-sdk-documentation]: http://msdnstage.redmond.corp.microsoft.com/library/dn479185.aspx

[azure-purchase-options]: http://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/

[apache-tez]: http://tez.apache.org
[apache-hive]: http://hive.apache.org/
[apache-log4j]: http://en.wikipedia.org/wiki/Log4j
[hive-on-tez-wiki]: https://cwiki.apache.org/confluence/display/Hive/Hive+on+Tez
[import-to-excel]: http://azure.microsoft.com/documentation/articles/hdinsight-connect-excel-power-query/


[hdinsight-use-oozie]: ../hdinsight-use-oozie/
[hdinsight-analyze-flight-data]: ../hdinsight-analyze-flight-delay-data/



[hdinsight-storage]: ../hdinsight-use-blob-storage

[hdinsight-provision]: ../hdinsight-provision-clusters/
[hdinsight-submit-jobs]: ../hdinsight-submit-hadoop-jobs-programmatically/
[hdinsight-upload-data]: ../hdinsight-upload-data/
[hdinsight-get-started]: ../hdinsight-get-started/

[Powershell-install-configure]: ../install-configure-powershell/
[powershell-here-strings]: http://technet.microsoft.com/library/ee692792.aspx

[image-hdi-hive-powershell]: ./media/hdinsight-use-hive/HDI.HIVE.PowerShell.png
[img-hdi-hive-powershell-output]: ./media/hdinsight-use-hive/HDI.Hive.PowerShell.Output.png
[image-hdi-hive-architecture]: ./media/hdinsight-use-hive/HDI.Hive.Architecture.png

<!--HONumber=47-->
