<properties
   pageTitle="MapReduce con Hadoop en HDInsight | Azure"
   description="Obtenga información acerca de cómo usar Escritorio remoto para conectarse a Hadoop en HDInsight y ejecutar trabajos de MapReduce."
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

# Uso de MapReduce de Hadoop en HDInsight con Escritorio remoto

[AZURE.INCLUDE [mapreduce-selector](../includes/hdinsight-selector-use-mapreduce.md)]

En este artículo, obtendrá información sobre cómo conectarse a un clúster de Hadoop en HDInsight mediante Escritorio remoto y, a continuación, ejecutar trabajos de MapReduce mediante el comando de Hadoop.

## <a id="prereq"></a>Requisitos previos

Necesitará lo siguiente para completar los pasos de este artículo.

* Un clúster HDInsight basado en Windows (Hadoop en HDInsight)

* Un cliente de Windows 7, 8 o 10

## <a id="connect"></a>Conexión con el Escritorio remoto

Habilite el Escritorio remoto para el clúster de HDInsight y, a continuación, conéctese a él siguiendo las instrucciones de <a href="http://azure.microsoft.com/ documentation/articles/hdinsight-administer-use-management-portal/#rdp" target="_blank">Conexión a los clústeres de HDInsight con RDP</a>.

## <a id="hadoop"></a>Uso del comando de Hadoop

Una vez conectado al escritorio para el clúster de HDInsight, siga estos pasos para ejecutar un trabajo de MapReduce mediante el comando de Hadoop.

1. Desde el escritorio de HDInsight, inicie la **línea de comandos de Hadoop**. Se abrirá un nuevo símbolo del sistema en el directorio **c:\apps\dist\hadoop-&lt;número de versión>**.

	> [AZURE.NOTE] El número de versión cambia cuando se actualiza Hadoop. La variable de entorno **HADOOP_HOME** puede usarse para encontrar la ruta de acceso. Por ejemplo, `cd %HADOOP_HOME%` cambiará los directorios al directorio de Hadoop, sin que sea necesario conocer el número de versión.

2. Para usar el comando **Hadoop** para ejecutar un trabajo de MapReduce de ejemplo, utilice lo siguiente.

		hadoop jar hadoop-mapreduce-examples.jar wordcount wasb:///example/data/gutenberg/davinci.txt wasb:///example/data/WordCountOutput

	Se inicia la clase **wordcount**, incluida en el archivo **hadoop-mapreduce-examples.jar** del directorio actual. Como entrada, utiliza el documento **wasb://example/data/gutenberg/davinci.txt** y la salida se almacena en **wasb:///ejemplo/data/WordCountOutput**.

	> [AZURE.NOTE] Para obtener más información acerca de este trabajo de MapReduce y los datos de ejemplo, consulte <a href="../hdinsight-use-mapreduce/">Uso de MapReduce en Hadoop de HDInsight</a>.

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

* [Uso de MapReduce en Hadoop de HDInsight](../hdinsight-use-mapreduce/)

Para obtener información sobre otras maneras en que puede trabajar con Hadoop en HDInsight.

* [Uso de Hive con Hadoop en HDInsight](../hdinsight-use-hive/)

* [Uso de Pig con Hadoop en HDInsight](../hdinsight-use-pig/)
<!--HONumber=45--> 
