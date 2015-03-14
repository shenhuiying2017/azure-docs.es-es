<properties
   pageTitle="Lo que necesita saber acerca de Hadoop en HDInsight basado en Linux | Azure"
   description="Los clústeres de HDInsight basado en Linux proporcionan Hadoop en un entorno conocido de Linux, que se ejecuta en la nube de Azure."
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

# Trabajo con HDInsight en Linux (vista previa)

Los clústeres de HDInsight basado en Linux proporcionan Hadoop en un entorno conocido de Linux, que se ejecuta en la nube de Azure. En la mayoría de los casos, debiera funcionar exactamente como cualquier otra instalación de Hadoop en Linux. Este documento detalla las diferencias específicas que debe tener en cuenta.

## Nombres de dominio

El nombre de dominio completo que se utiliza al conectarse con el clúster es **&lt;nombre del clúster>.azurehdinsight.net** o (solo para SSH) **&lt;nombre del clúster>.aurehdinsight.net**.

## Servicios accesibles de manera remota

* **Ambari (web)** - https://&lt;nombre del clúster>.azurehdinsight.net

	> [AZURE.NOTE] Realice la autenticación con el usuario y la contraseña del administrador de clúster y, a continuación, inicie sesión en Ambari. Aquí también se usa el usuario y la contraseña del administrador de clúster.
	> 
	> La autenticación es texto no cifrado: use siempre HTTPS para asegurarse de que la conexión sea segura.

	A pesar de que es posible tener acceso directamente a través de Internet a Ambari para su clúster, cierta funcionalidad se basa en tener acceso a nodos a través del nombre de dominio interno que usa el clúster. Debido a que se trata de un nombre de dominio interno y no público, recibirá errores de servidor no encontrado al intentar tener acceso a algunas características a través de Internet.

	Para solucionar este problema, use un túnel SSH para autorizar el tráfico web al nodo principal del clúster. Use los siguientes artículos para crear un túnel SSH desde un puerto en la máquina local al clúster.

	* <a href="../hdinsight-hadoop-linux-use-ssh-unix/#tunnel" target="_blank">Utilización de SSH con Hadoop en HDInsight basado en Linux desde Linux, Unix u OS X</a> : pasos para crear un túnel SSH con el comando `ssh`.

	* <a href="../hdinsight-hadoop-linux-use-ssh-windows/#tunnel" target="_blank">Utilización de SSH con Hadoop en HDInsight basado en Linux desde Windows</a> : pasos para usar PuTTY para crear un túnel SSH.

* **Ambari (REST)**: https://&lt;nombre del clúster>.azurehdinsight.net/ambari

	> [AZURE.NOTE] Realice la autenticación con el usuario y la contraseña del administrador de clúster.
	> 
	> La autenticación es texto no cifrado: use siempre HTTPS para asegurarse de que la conexión sea segura.

* **WebHCat (Templeton)**: https://&lt;nombre del clúster>.azurehdinsight.net/templeton

	> [AZURE.NOTE] Realice la autenticación con el usuario y la contraseña del administrador de clúster.
	> 
	> La autenticación es texto no cifrado: use siempre HTTPS para asegurarse de que la conexión sea segura.

* **SSH**: &lt;nombre del clúster>-ssh.azurehdinsight.net en el puerto 22

	> [AZURE.NOTE] Solo puede tener acceso al nodo principal del clúster a través de SSH desde un equipo cliente. Una vez conectado, puede tener acceso a los nodos de trabajo mediante el uso de SSH desde el nodo principal.

## Ubicaciones de archivo

Puede encontrar los archivos relacionados con Hadoop en los nodos de clúster en `/usr/hdp/current`.

Puede encontrar datos de ejemplo y archivos jar en HDFS (WASB) en "/example" o 'wasb:///example'.

## Procedimientos recomendados de HDFS, WASB y almacenamiento

En la mayoría de las distribuciones de Hadoop, el almacenamiento local crea una copia de seguridad del sistema de archivos distribuido de Hadoop (HDFS) en las máquinas del clúster. A pesar de que esto es eficaz, puede ser costoso para una solución basada en la nube en la que se le cobra por hora por los recursos de proceso.

HDInsight usa el almacenamiento de blobs de Azure como el almacén predeterminado, lo que brinda los siguientes beneficios:

* Almacenamiento económico a largo plazo

* Accesible desde servicios externos, como sitios web, utilidades para carga/descarga de archivos, SDK en varios idiomas y exploradores web.

Debido a que es el almacén predeterminado para HDInsight, normalmente no tiene que hacer nada para utilizarlo. Por ejemplo, el siguiente comando enumerará los archivos existentes en la carpeta **/example/data**, que está almacenada en el almacenamiento de blobs de Azure.

	hadoop fs -ls /example/data

Es posible que algunos comandos requieran que especifique que usa almacenamiento de BLOBS. En estos casos, puede usar el prefijo **WASB://** en el comando.

HDInsight también le permite asociar varias cuentas de almacenamiento de BLOBS a un clúster. Para tener acceso a los datos ubicados en una cuenta de almacenamiento de blobs no predeterminada, puede usar el formato **WASB://&lt;nombre del contenedor>@&lt;nombre de la cuenta>.blob.core.windows.net/**. Por ejemplo, lo siguiente enumerará los contenidos del directorio **/example/data** para la cuenta de almacenamiento y el contenedor especificados.

	hadoop fs -ls wasb://mycontainer@mystorage.blob.core.windows.net/example/data

### ¿Qué almacenamiento de blobs utiliza el clúster?

Durante la creación del clúster, selecciona usar un contenedor y una cuenta de almacenamiento existentes o bien, crear una nueva. Por lo tanto, probablemente se le olvidó. Puede encontrar el contenedor y la cuenta de almacenamiento con los siguientes métodos

**Portal de Azure**

1. En el <a href="https://manage.windowsazure.com/" target="_blank">Portal de administración de Azure</a>, seleccione su clúster de HDInsight.

2. Seleccione **Panel** en la parte superior de la página.

3. Las cuentas de almacenamiento y los contenedores aparecen en la sección de **recursos vinculados** de la página.

	![linked resources](./media/hdinsight-hadoop-linux-information/storageportal.png)

**Interfaz de la línea de comandos multiplataforma de Azure**

*Próximamente*

### ¿Cómo obtengo acceso al almacenamiento de blobs?

Además de mediante el comando Hadoop desde el clúster, existe una variedad de formas para tener acceso a los blobs:

* <a href="http://azure.microsoft.com/ documentation/articles/xplat-cli/" target="_blank">Interfaz de la línea de comandos multiplataforma de Azure</a> : después de la instalación, consulte `azure storage` para obtener ayuda sobre el uso del almacenamiento o `azure blob` para comandos específicos para los blobs.

* Una variedad de SDK:

	* <a href="https://github.com/Azure/azure-sdk-for-java" target="_blank">Java</a>

	* <a href="https://github.com/Azure/azure-sdk-for-node" target="_blank">Node.js</a>

	* <a href="https://github.com/Azure/azure-sdk-for-php" target="_blank">PHP</a>

	* <a href="https://github.com/Azure/azure-sdk-for-python" target="_blank">Python</a>

	* <a href="https://github.com/Azure/azure-sdk-for-ruby" target="_blank">Ruby</a>

	* <a href="https://github.com/Azure/azure-sdk-for-net" target="_blank">.NET</a>

* <a href="https://msdn.microsoft.com/es-es/library/azure/dd135733.aspx" target="_blank">API de REST de almacenamiento</a>


## Pasos siguientes

* [Uso de Hive con HDInsight](../hdinsight-use-hive/)
* [Uso de Pig con HDInsight](../hdinsight-use-pig/)
* [Uso de trabajos de MapReduce con HDInsight](../hdinsight-use-mapreduce)


<!--HONumber=45--> 
