<properties
   pageTitle="Sugerencias para usar Hadoop en HDInsight basado en Linux | Microsoft Azure"
   description="Obtenga sugerencias de implementación para usar clústeres de HDInsight basado en Linux (Hadoop) en un entorno de Linux conocido que se ejecuta en la nube de Azure."
   services="hdinsight"
   documentationCenter=""
   authors="Blackmist"
   manager="paulettm"
   editor="cgronlun"/>

<tags
   ms.service="hdinsight"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="05/27/2015"
   ms.author="larryfr"/>

# Información sobre el uso de HDInsight en Linux (vista previa)

Los clústeres de HDInsight de Azure basado en Linux proporcionan Hadoop en un entorno conocido de Linux, que se ejecuta en la nube de Azure. En la mayoría de los casos, debiera funcionar exactamente como cualquier otra instalación de Hadoop en Linux. Este documento detalla las diferencias específicas que debe tener en cuenta.

## Nombres de dominio

El nombre de dominio completo (FQDN) que se usa al conectarse con el clúster es **&lt;nombre del clúster>.azurehdinsight.net** o **&lt;nombre del clúster-ssh>.azurehdinsight.net** (solo para SSH).

## Acceso remoto a los servicios

* **Ambari (web)** - https://&lt;clustername>.azurehdinsight.net

	> [AZURE.NOTE]Realice la autenticación con el usuario y la contraseña del administrador de clúster y, a continuación, inicie sesión en Ambari. Aquí también se usa el usuario y la contraseña del administrador de clúster.
	>
	> La autenticación es texto no cifrado: use siempre HTTPS para asegurarse de que la conexión sea segura.

	A pesar de que es posible tener acceso directamente a través de Internet a Ambari para su clúster, cierta funcionalidad se basa en tener acceso a nodos a través del nombre de dominio interno que usa el clúster. Debido a que se trata de un nombre de dominio interno y no público, recibirá errores de "servidor no encontrado" al intentar tener acceso a algunas características a través de Internet.

	Para solucionar este problema, use un túnel SSH para autorizar el tráfico web al nodo principal del clúster. Use la sección **Tunelización de SSH** de los siguientes artículos para crear un túnel SSH desde un puerto en la máquina local al clúster:

	* [Utilización de SSH con Hadoop en HDInsight basado en Linux desde Linux, Unix u OS X](hdinsight-hadoop-linux-use-ssh-unix.md): pasos para crear un túnel SSH con el comando `ssh`.

	* [Utilización de SSH con Hadoop en HDInsight basado en Linux desde Windows](hdinsight-hadoop-linux-use-ssh-windows): pasos para usar PuTTY con el fin de crear un túnel SSH.

* **Ambari (REST)** - https://&lt;clustername>.azurehdinsight.net/ambari

	> [AZURE.NOTE]Realice la autenticación con el usuario y la contraseña del administrador de clúster.
	>
	> La autenticación es texto no cifrado: use siempre HTTPS para asegurarse de que la conexión sea segura.

* **WebHCat (Templeton)** - https://&lt;clustername>.azurehdinsight.net/templeton

	> [AZURE.NOTE]Realice la autenticación con el usuario y la contraseña del administrador de clúster.
	>
	> La autenticación es texto no cifrado: use siempre HTTPS para asegurarse de que la conexión sea segura.

* **SSH** - &lt;nombre del clúster>-ssh.azurehdinsight.net en el puerto 22

	> [AZURE.NOTE]Solo puede tener acceso al nodo principal del clúster a través de SSH desde un equipo cliente. Una vez conectado, puede tener acceso a los nodos de trabajo mediante el uso de SSH desde el nodo principal.

## Ubicaciones de archivo

Puede encontrar los archivos relacionados con Hadoop en los nodos de clúster en `/usr/hdp/current`.

Es posible encontrar los archivos JAR y datos de ejemplo en el Sistema de archivos distribuido de Hadoop (HDFS) o en el almacenamiento de blobs de Azure en "/example" o "wasb:///example".

## Procedimientos recomendados de almacenamiento, almacenamiento de blobs de Azure y HDFS

En la mayoría de las distribuciones de Hadoop, se crean copias de seguridad de HDFS en el almacenamiento local de las máquinas del clúster. A pesar de que esto es eficaz, puede ser costoso para una solución basada en la nube en la que se le cobra por hora por los recursos de proceso.

HDInsight usa el almacenamiento de blobs de Azure como el almacén predeterminado, lo que brinda los siguientes beneficios:

* Almacenamiento económico a largo plazo

* Accesible desde servicios externos, como sitios web, utilidades para carga/descarga de archivos, SDK en varios idiomas y exploradores web.

Debido a que es el almacén predeterminado para HDInsight, normalmente no tiene que hacer nada para utilizarlo. Por ejemplo, el comando siguiente enumerará los archivos existentes en la carpeta **/example/data**, que está almacenada en el almacenamiento de blobs de Azure:

	hadoop fs -ls /example/data

Es posible que algunos comandos requieran que especifique que usa almacenamiento de blobs. En estos casos, puede usar el prefijo **WASB://** en el comando.

HDInsight también le permite asociar varias cuentas de almacenamiento de blobs a un clúster. Para tener acceso a los datos ubicados en una cuenta de almacenamiento de blobs no predeterminada, puede usar el formato **WASB://&lt;container-name>@&lt;nombre de la cuenta>.blob.core.windows.net/**. Por ejemplo, lo siguiente enumerará los contenidos del directorio **/example/data** para la cuenta de almacenamiento de blobs y el contenedor especificados:

	hadoop fs -ls wasb://mycontainer@mystorage.blob.core.windows.net/example/data

### ¿Qué almacenamiento de blobs utiliza el clúster?

Durante la creación del clúster, seleccionó usar un contenedor y una cuenta de almacenamiento de Azure existentes, o bien, crear una nueva. Por lo tanto, probablemente se le olvidó. Puede encontrar el contenedor y la cuenta de almacenamiento con los siguientes métodos.

**API de Ambari**

1. Use el comando siguiente para recuperar información de configuración de HDFS:

        curl -u admin:PASSWORD -G "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/configurations/service_config_versions?service_name=HDFS&service_config_version=1"

2. En los datos de JSON devueltos, busque la entrada `fs.defaultFS`. Esta contendrá el nombre de la cuenta de almacenamiento y el contenedor predeterminado con un formato similar al siguiente:

        wasb://CONTAINTERNAME@STORAGEACCOUNTNAME.blob.core.windows.net

	> [AZURE.TIP]Si instaló [jq](http://stedolan.github.io/jq/), puede utilizar lo siguiente para devolver solo la entrada `fs.defaultFS`:
	>
	> `curl -u admin:PASSWORD -G "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/configurations/service_config_versions?service_name=HDFS&service_config_version=1" | jq '.items[].configurations[].properties["fs.defaultFS"] | select(. != null)'`
	
3. Para buscar la clave usada para autenticar a la cuenta de almacenamiento, o para encontrar las cuentas de almacenamiento secundarias asociadas al clúster, use lo siguiente:

		curl -u admin:PASSWORD -G "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/configurations/service_config_versions?service_name=HDFS&service_config_version=1"
		
4. En los datos de JSON devueltos, encuentre la entrada que empieza por `fs.azure.account.key`. El resto del nombre de la entrada es el nombre de la cuenta de almacenamiento. Por ejemplo: `fs.azure.account.key.mystorage.blob.core.windows.net`. El valor almacenado en esta entrada es la clave utilizada para autenticar a la cuenta de almacenamiento.

	> [AZURE.TIP]Si instaló [jq](http://stedolan.github.io/jq/), puede usar lo siguiente para devolver una lista de las claves y los valores:
	>
	> `curl -u admin:PASSWORD -G "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/configurations/service_config_versions?service_name=HDFS&service_config_version=1" | jq '.items[].configurations[].properties as $in | $in | keys[] | select(. | contains("fs.azure.account.key.")) as $item | $item | ltrimstr("fs.azure.account.key.") | { storage_account: ., storage_account_key: $in[$item] }'`


**Portal de Azure**

1. En el [Portal de Azure](https://manage.windowsazure.com/), seleccione el clúster de HDInsight.

2. Seleccione **Panel** en la parte superior de la página.

3. Las cuentas de almacenamiento y los contenedores aparecen en la sección de **recursos vinculados** de la página.

	![recursos vinculados](./media/hdinsight-hadoop-linux-information/storageportal.png)

### ¿Cómo obtengo acceso al almacenamiento de blobs?

Además de mediante el comando Hadoop desde el clúster, existe una variedad de formas para tener acceso a los blobs:

* [CLI de Azure para Mac, Linux y Windows](../xplat-cli.md): comandos de interfaz de línea de comandos para trabajar con Azure. Después de la instalación, use el comando `azure storage` para obtener ayuda sobre el uso del almacenamiento o `azure blob` para comandos específicos para los blobs.

* [blobxfer.py](https://github.com/Azure/azure-batch-samples/tree/master/Python/Storage): un script de Python para trabajar con blobs en almacenamiento de Azure.

* Una variedad de SDK:

	* [Java](https://github.com/Azure/azure-sdk-for-java)

	* [Node.js](https://github.com/Azure/azure-sdk-for-node)

	* [PHP](https://github.com/Azure/azure-sdk-for-php)

	* [Python](https://github.com/Azure/azure-sdk-for-python)

	* [Ruby](https://github.com/Azure/azure-sdk-for-ruby)

	* [.NET](https://github.com/Azure/azure-sdk-for-net)

* [API de REST de almacenamiento](https://msdn.microsoft.com/library/azure/dd135733.aspx)

## Pasos siguientes

* [Uso de Hive con HDInsight](hdinsight-use-hive.md)
* [Uso de Pig con HDInsight](hdinsight-use-pig.md)
* [Uso de trabajos de MapReduce con HDInsight](hdinsight-use-mapreduce.md)
 

<!---HONumber=July15_HO2-->