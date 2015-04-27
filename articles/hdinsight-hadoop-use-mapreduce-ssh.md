<properties
   pageTitle="MapReduce con Hadoop en HDInsight | Azure"
   description="Obtenga más información sobre cómo usar SSH para ejecutar trabajos de MapReduce mediante Hadoop en HDInsight."
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

# Uso de Hive con Hadoop en HDInsight con SSH

[AZURE.INCLUDE [mapreduce-selector](../includes/hdinsight-selector-use-mapreduce.md)]

En este artículo, aprenderá a usar SSH para conectarse a un clúster de Hadoop de HDInsight y después enviar trabajos de MapReduce mediante el comando de Hadoop.

> [AZURE.NOTE] Si ya está familiarizado con el uso de servidores Hadoop basados en Linux, pero no tiene experiencia con HDInsight, consulte <a href="../hdinsight-hadoop-linux-information/" target="_blank">Lo que necesita saber acerca de Hadoop en HDInsight basado en Linux</a>.

## <a id="prereq"></a>Requisitos previos

Necesitará lo siguiente para completar los pasos de este artículo.

* Un clúster de HDInsight basado en Linux (Hadoop en HDInsight)

* Un cliente SSH. Linux, Unix y Mac OS deben incluir un cliente ssh. Los usuarios de Windows deben descargar un cliente, como <a href="http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html" target="_blank">Putty</a>.

## <a id="ssh"></a>Conexión con SSH

Conéctese con el nombre de dominio completo (FQDN) de su clúster de HDInsight mediante el comando SSH. El FQDN será el nombre que asignó al clúster, **.azurehdinsight.net**. Por ejemplo, lo siguiente se conectaría a un clúster denominado **myhdinsight**.

	ssh admin@myhdinsight-ssh.azurehdinsight.net

**Si proporciona una clave de certificado para la autenticación de SSH**, al crear el clúster de HDInsight, es posible que tenga que especificar la ubicación de la clave privada en el sistema cliente.

	ssh admin@myhdinsight-ssh.azurehdinsight.net -i ~/mykey.key

**Si proporciona una contraseña para la autenticación de SSH** al crear el clúster de HDInsight, tendrá que proporcionar la contraseña cuando se le solicite.

### Putty (clientes Windows)

Windows no proporciona ningún cliente SSH integrado. Se recomienda usar **Putty**, que se puede descargar en <a href="http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html" target="_blank">http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html</a>.

Para obtener más información acerca del uso de Putty, consulte la sección **Uso de Putty para conectarse a un equipo Linux** de <a href="http://azure.microsoft.com/ documentation/articles/virtual-machines-linux-use-ssh-key/" target="_blank">Utilización de SSH con Linux en Azure</a>.

> [AZURE.NOTE] Si utiliza un certificado para la autenticación de SSH para el clúster de HDInsight, también necesitará consultar la sección **Creación de un PPK para Putty** de <a href="http://azure.microsoft.com/ documentation/articles/virtual-machines-linux-use-ssh-key/" target="_blank">Utilización de SSH con Linux en Azure</a>

## <a id="hadoop"></a>Uso del comando de Hadoop

1. Una vez conectado al clúster de HDInsight, utilice lo siguiente para usar el comando **Hadoop** para iniciar un trabajo de MapReduce.

		hadoop jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar wordcount wasb:///example/data/gutenberg/davinci.txt wasb:///example/data/WordCountOutput

	Se inicia la clase **wordcount**, incluido en el archivo **hadoop-mapreduce-examples.jar**. Como entrada, utiliza el documento **wasb://example/data/gutenberg/davinci.txt** y la salida se almacena en **wasb:///ejemplo/data/WordCountOutput**.

	> [AZURE.NOTE] Para obtener más información acerca de este trabajo de MapReduce y los datos de ejemplo, consulte <a href="../hdinsight-use-mapreduce/" target="_blank">Uso de MapReduce en Hadoop en HDInsight</a>.

2. El trabajo emitirá detalles cuando se procesa, devolviendo finalmente información similar a la siguiente cuando finaliza el trabajo.

		File Input Format Counters
        Bytes Read=1395666
		File Output Format Counters
        Bytes Written=337623

3. Una vez completado, utilice el siguiente comando para enumerar los archivos de salida almacenados en **wasb://example/data/WordCountOutput**.

		hadoop fs -ls wasb:///example/data/WordCountOutput

	Se deben mostrar dos archivos, **_SUCCESS** y **part-r-00000**. El archivo **part-r-00000** contiene la salida de este trabajo.

	> [AZURE.NOTE] Algunos trabajos de MapReduce pueden dividir los resultados entre varios archivos **part-r-####**. Si es así, utilice el sufijo #### para indicar el orden de los archivos.

4. Para ver la salida, use el comando siguiente.

		hadoop fs -cat wasb:///example/data/WordCountOutput/part-r-00000

	Se mostrará una lista de las palabras contenidas en el archivo **wasb://example/data/gutenberg/davinci.txt**, junto con el número de veces que aparecía cada palabra.  El siguiente es un ejemplo de los datos que se incluirán en el archivo.

		wreathed        3
		wreathing       1
		wreaths 		1
		wrecked 		3
		wrenching       1
		wretched        6
		wriggling       1

## <a id="summary"></a>Resumen

Como se puede ver, el comando de Hadoop proporciona una manera fácil de ejecutar trabajos de MapReduce en un clúster de HDInsight y, a continuación, ver la salida del trabajo.

## <a id="nextsteps"></a>Pasos siguientes

Para obtener información general sobre los trabajos de MapReduce en HDInsight.

* [Uso de MapReduce en Hadoop de HDInsight](hdinsight-use-mapreduce.md)

Para obtener información sobre otras maneras en que puede trabajar con Hadoop en HDInsight.

* [Uso de Hive con Hadoop en HDInsight](hdinsight-use-hive.md)

* [Uso de Pig con Hadoop en HDInsight](hdinsight-use-pig.md)

<!--HONumber=47-->
