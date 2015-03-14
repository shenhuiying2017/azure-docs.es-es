<properties
   pageTitle="Uso de Hive de Hadoop en HDInsight | Azure"
   description= "Aprender a utilizar Hive con HDInsight a través de SSH."
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

[AZURE.INCLUDE [hive-selector](../includes/hdinsight-selector-use-hive.md)]

En este artículo, aprenderá a usar SSH para conectarse a un clúster Hadoop en HDInsight y, a continuación, enviar interactivamente consultas de Hive usando la interfaz de línea de comandos (CLI) de Hive.

> [AZURE.NOTE] Si ya está familiarizado con el uso de servidores Hadoop basados en Linux, pero no tiene experiencia con HDInsight, consulte <a href="../hdinsight-hadoop-linux-information/" target="_blank">Lo que necesita saber acerca de Hadoop en HDInsight basado en Linux</a>.

## <a id="prereq"></a>Requisitos previos

Necesitará lo siguiente para completar los pasos de este artículo.

* Un clúster de Hadoop basado en Linux en HDInsight

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

## <a id="hive"></a>Uso del comando de Hive

2. Una vez conectado, inicie la interfaz de línea de comandos (CLI) de Hive mediante el comando siguiente.

        hive

3. Con la CLI, introduzca las siguientes instrucciones para crear una nueva tabla denominada **log4jLogs** con los datos de ejemplo.

        DROP TABLE log4jLogs;
        CREATE EXTERNAL TABLE log4jLogs (t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string)
        ROW FORMAT DELIMITED FIELDS TERMINATED BY ' '
        STORED AS TEXTFILE LOCATION 'wasb:///example/data/';
        SELECT t4 AS sev, COUNT(*) AS count FROM log4jLogs WHERE t4 = '[ERROR]' GROUP BY t4;

    Estas instrucciones realizan las acciones siguientes

    * **DROP TABLE**: elimina la tabla y el archivo de datos si la tabla ya existe.
    * **CREATE EXTERNAL TABLE**: crea una tabla  'external' en Hive. Las tablas externas solamente almacenan la definición de tabla en Hive (los datos se dejan en la ubicación original).
    * **ROW FORMAT**: indica cómo se da formato a los datos de Hive. En este caso, los campos de cada registro se separan mediante un espacio.
    * **STORED AS TEXTFILE LOCATION**: indica a Hive dónde se almacenan los datos (el directorio de ejemplos/datos) y que se almacenen como texto.
    * **SELECT**: permite seleccionar un número de todas las filas donde la columna **t4** contiene el valor **[ERROR]** Esto debe devolver un valor de **3** ya que hay tres filas que contienen este valor.

    > [AZURE.NOTE] Las tablas externas se deben usar cuando espera que un origen externo, como por ejemplo un proceso de carga de datos automático, u otra operación MapReduce, actualice los datos subyacentes, pero siempre desea que las consultas de Hive usen los datos más recientes.
    >
    > La eliminación de una tabla externa **no** elimina los datos, solamente la definición de tabla.

4. Utilice las instrucciones siguientes para crear una nueva tabla 'internal' denominada **errorLogs**.

        CREATE TABLE IF NOT EXISTS errorLogs (t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string) STORED AS ORC;
        INSERT OVERWRITE TABLE errorLogs SELECT t1, t2, t3, t4, t5, t6, t7 FROM log4jLogs WHERE t4 = '[ERROR]';

    Estas instrucciones realizan las acciones siguientes.

    * **CREATE TABLE IF NOT EXISTS**: crea una tabla, si todavía no existe. Dado que la palabra clave **EXTERNAL** no se usa, se trata de una tabla interna, que se almacena en el almacén de datos de Hive y es administrada completamente por Hive
    * **STORED AS ORC**: almacena los datos en el formato Optimized Row Columnar (ORC). Se trata de un formato altamente optimizado y eficiente para almacenar datos de Hive
    * **INSERT OVERWRITE ... SELECT**: selecciona filas de la tabla **log4jLogs** que contienen **[ERROR]** y después inserta los datos en la tabla **errorLogs**.

    Para comprobar que solamente las filas que contienen **[ERROR]** en la columna t4 se almacenaron en la tabla **errorLogs**, use la siguiente instrucción para devolver todas las filas de **errorLogs**.

        SELECT * from errorLogs;

    Se deben devolver tres filas de datos y todas ellas deben contener **[ERROR]** en la columna t4.

    > [AZURE.NOTE] A diferencia de las tablas **EXTERNAL**, la eliminación de una tabla interna también eliminará los datos subyacentes.

## <a id="summary"></a>Resumen

Como puede ver, el comando de Hive proporciona una manera fácil de ejecutar consultas de Hive interactivamente en un clúster de HDInsight, supervisar el estado del trabajo y recuperar el resultado.

## <a id="nextsteps"></a>Pasos siguientes

Para obtener información general acerca de Hive en HDInsight.

* [Uso de Hive con Hadoop en HDInsight](../hdinsight-use-hive/)

Para obtener información sobre otras maneras en que puede trabajar con Hadoop en HDInsight.

* [Uso de Pig con Hadoop en HDInsight](../hdinsight-use-pig/)

* [Uso de MapReduce con Hadoop en HDInsight](../hdinsight-use-mapreduce/)

[hdinsight-sdk-documentation]: http://msdnstage.redmond.corp.microsoft.com/es-es/library/dn479185.aspx

[azure-purchase-options]: http://azure.microsoft.com/ pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/ pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/ pricing/free-trial/

[apache-tez]: http://tez.apache.org
[apache-hive]: http://hive.apache.org/
[apache-log4j]: http://en.wikipedia.org/wiki/Log4j
[hive-on-tez-wiki]: https://cwiki.apache.org/confluence/display/Hive/Hive+on+Tez
[import-to-excel]: http://azure.microsoft.com/ documentation/articles/hdinsight-connect-excel-power-query/


[hdinsight-use-oozie]: ../hdinsight-use-oozie/
[hdinsight-analyze-flight-data]: ../hdinsight-analyze-flight-delay-data/

[putty]: http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html

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

<!--HONumber=45--> 
