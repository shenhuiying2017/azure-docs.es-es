<properties
   pageTitle="Uso de Hive de Hadoop en HDInsight | Azure"
   description="Obtenga información acerca de cómo utilizar Hive con la consola de consulta de HDInsight basada en web."
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

# Ejecución de consultas de Hive mediante la consola de consulta

[AZURE.INCLUDE [hive-selector](../includes/hdinsight-selector-use-hive.md)]

En este artículo, aprenderá a utilizar la consola de la consulta de HDInsight para ejecutar consultas de Hive en un clúster de Hadoop de HDInsight desde el explorador.

> [AZURE.NOTE] La consola de consulta solo está disponible en los clústeres de HDInsight basados en Windows

##<a id="prereq"></a>Requisitos previos

Necesitará lo siguiente para completar los pasos de este artículo.

* Un clúster de Hadoop para HDInsight basado en Windows

* Un explorador web moderno

##<a id="run"></a> Ejecución de consultas de Hive mediante la consola de consulta

1. Abra el <a href="https://manage.windowsazure.com" target="_blank">Portal de administración de Azure</a> y seleccione el clúster de HDInsight. En la parte inferior de la página, seleccione la **consola de consulta**. Cuando se le solicite, escriba el nombre de usuario y la contraseña que utilizó al crear el clúster.

    > [AZURE.NOTE] También puede tener acceso a la consola de consulta escribiendo **https://CLUSTERNAME.azurehdinsight.net** en el explorador.

2. En los vínculos de la parte superior de la página, seleccione **Editor Hive**. Se muestra un formulario que puede utilizarse para introducir instrucciones de HiveQL que desea ejecutar en el clúster de HDInsight. 
	
	![the hive editor](./media/hdinsight-hadoop-use-hive-query-console/queryconsole.png)
	
	Reemplace el texto `Select * from hivesampletable` por las siguientes instrucciones HiveQL.

        DROP TABLE log4jLogs;
        CREATE EXTERNAL TABLE log4jLogs (t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string)
        ROW FORMAT DELIMITED FIELDS TERMINATED BY ' '
        STORED AS TEXTFILE LOCATION 'wasb:///example/data/';
        SELECT t4 AS sev, COUNT(*) AS count FROM log4jLogs WHERE t4 = '[ERROR]' GROUP BY t4;

    Estas instrucciones realizan las acciones siguientes

    * **DROP TABLE**: elimina la tabla y el archivo de datos si la tabla ya existe.
    * **CREATE EXTERNAL TABLE**: crea una tabla  'externa' nueva en Hive. Las tablas externas solamente almacenan la definición de tabla en Hive (los datos se dejan en la ubicación original).

    > [AZURE.NOTE] Las tablas externas se deben usar cuando espera que un origen externo, como por ejemplo un proceso de carga de datos automático, u otra operación MapReduce, actualice los datos subyacentes, pero siempre desea que las consultas de Hive usen los datos más recientes.
    >
    > La eliminación de una tabla externa **no** elimina los datos, solamente la definición de tabla.

    * **ROW FORMAT**: indica cómo se da formato a los datos de Hive. En este caso, los campos de cada registro se separan mediante un espacio.
    * **STORED AS TEXTFILE LOCATION**: indica a Hive dónde se almacenan los datos (el directorio de ejemplos/datos) y que se almacenen como texto.
    * **SELECT**: permite seleccionar un número de todas las filas donde la columna **t4** contiene el valor **[ERROR]**. Esto debe devolver un valor de **3** ya que hay tres filas que contienen este valor.

2. Seleccione **Enviar**. La **sesión de trabajo** en la parte inferior de la página debe mostrar detalles del trabajo.

3. Cuando el campo **Estado** cambia a **Completado**, seleccione **Ver detalles** para el trabajo. En la página de detalles, el **resultado del trabajo** contendrá `[ERROR]	3`. Puede utilizar el botón **Descargar** situado debajo de este campo para descargar un archivo que contiene el resultado del trabajo.


##<a id="summary"></a>Resumen

Como puede ver, la consola de consulta proporciona una manera fácil de ejecutar consultas de Hive en un clúster de HDInsight, supervisar el estado del trabajo y recuperar el resultado. 

Para obtener más información acerca de Hive mediante la consola de la consulta, seleccione **Introducción** desde la parte superior de la consola de consulta y después use los ejemplos. Cada ejemplo se recorre el proceso de análisis de los datos de uso de Hive, incluida la explicación de las instrucciones de HiveQL utilizadas en el ejemplo.

##<a id="nextsteps"></a>Pasos siguientes

Para obtener información general acerca de Hive en HDInsight.

* [Uso de Hive con Hadoop en HDInsight](../hdinsight-use-hive/)

Para obtener información sobre otras formas en que puede trabajar con Hadoop en HDInsight:

* [Uso de Pig con Hadoop en HDInsight](../hdinsight-use-pig/)

* [Uso de MapReduce con Hadoop en HDInsight](../hdinsight-use-mapreduce/)

[1]: ../hdinsight-hadoop-visual-studio-tools-get-started/

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
