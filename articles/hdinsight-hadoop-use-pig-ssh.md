<properties
   pageTitle="Uso de Pig con Hadoop en HDInsight | Azure"
   description="Aprenda a usar Pig con Hadoop en HDInsight a través de SSH."
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
 
# Ejecución de trabajos de Pig con el comando Pig (SSH)

[AZURE.INCLUDE [pig-selector](../includes/hdinsight-selector-use-pig.md)]

En este documento que le guiará a través del proceso de conexión a un clúster de HDInsight de Azure basado en Linux mediante Secure Shell(SSH) y utilizando después el comando Pig para ejecutar instrucciones de Pig Latin de manera interactiva, o como un trabajo por lotes.

El lenguaje de programación de Pig Latin le permite describir las transformaciones que se aplican a los datos de entrada para generar el resultado deseado.

> [AZURE.NOTE] Si ya está familiarizado con el uso de servidores de Hadoop basado en Linux, pero no conoce HDInsight, consulte <a href="../hdinsight-hadoop-linux-information/" target="_blank">Lo que necesita saber acerca de Hadoop en HDInsight basado en Linux</a>.

## <a id="prereq"></a>Requisitos previos

Necesitará lo siguiente para completar los pasos de este artículo.

* Un clúster de HDInsight basado en Linux (Hadoop en HDInsight).

* Un cliente SSH. Linux, Unix y Mac OS deben incluir un cliente SSH. Los usuarios de Windows deben descargar un cliente, como <a href="http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html" target="_blank">Putty</a>.

## <a id="ssh"></a>Conexión con SSH

Conéctese con el nombre de dominio completo (FQDN) de su clúster de HDInsight mediante el comando SSH. El FQDN será el nombre que ha asignado al clúster, es decir, **.azurehdinsight.net**. Por ejemplo, lo siguiente debería conectarse a un clúster denominado **myhdinsight**.

	ssh admin@myhdinsight-ssh.azurehdinsight.net

**Si proporciona una clave de certificado para la autenticación de SSH**, al crear el clúster de HDInsight, es posible que tenga que especificar la ubicación de la clave privada en el sistema cliente.

	ssh admin@myhdinsight-ssh.azurehdinsight.net -i ~/mykey.key

**Si proporcionó una contraseña para la autenticación de SSH**, al crear el clúster de HDInsight, tendrá que proporcionar la contraseña cuando se le solicite.

### Putty (clientes basados en Windows)

Windows no proporciona ningún cliente SSH integrado. Se recomienda usar **Putty**, que se puede descargar en <a href="http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html" target="_blank">http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html</a>.

Para obtener más información sobre cómo usar Putty, consulte la sección **Uso de Putty para conectarse a un equipo Linux** de <a href="http://azure.microsoft.com/documentation/articles/virtual-machines-linux-use-ssh-key/" target="_blank">Cómo utilizar SSH con Linux en Azure</a>.

> [AZURE.NOTE] Si utilizó un certificado para la autenticación de SSH del clúster de HDInsight, también necesitará consultar la sección **Creación de un PPK para Putty** de <a href="http://azure.microsoft.com/documentation/articles/virtual-machines-linux-use-ssh-key/" target="_blank">Cómo utilizar SSH con Linux en Azure</a>.

## <a id="pig"></a>Uso del comando Pig

2. Una vez conectado, inicie la interfaz de línea de comandos (CLI) de Pig mediante el comando siguiente.

        pig

	Después de un momento, debiera ver un símbolo de sistema de  `grunt>`.

3. Introduzca la siguiente instrucción.

		LOGS = LOAD 'wasb:///example/data/sample.log';

	Este comando carga el contenido del archivo sample.log en LOGS. Puede ver el contenido del archivo mediante el siguiente código.

		DUMP LOGS;

4. A continuación, transforme los datos aplicando una expresión regular para extraer solo el nivel de registro en cada registro mediante lo siguiente.

		LEVELS = foreach LOGS generate REGEX_EXTRACT($0, '(TRACE|DEBUG|INFO|WARN|ERROR|FATAL)', 1)  as LOGLEVEL;

	Puede usar **DUMP** para ver los datos después de la transformación. En este caso, utilice `DUMP LEVELS;`.

5. Continúe aplicando transformaciones mediante las instrucciones siguientes. Utilice `DUMP` para ver el resultado de la transformación después de cada paso.

	<table>
	<tr>
	<th>Instrucción</th><th>Qué hace</th>
	</tr>
	<tr>
	<td>FILTEREDLEVELS = FILTER LEVELS by LOGLEVEL is not null;</td><td>Quita las filas que contienen un valor nulo para el nivel de registro y almacena los resultados en FILTEREDLEVELS.</td>
	</tr>
	<tr>
	<td>GROUPEDLEVELS = GROUP FILTEREDLEVELS by LOGLEVEL;</td><td>Agrupa las filas por nivel de registro y almacena los resultados en GROUPEDLEVELS.</td>
	</tr>
	<tr>
	<td>FREQUENCIES = foreach GROUPEDLEVELS generate group as LOGLEVEL, COUNT(FILTEREDLEVELS.LOGLEVEL) as COUNT;</td><td>Crea un nuevo conjunto de datos que contiene cada valor de registro único y cuántas veces se produce. Esto se almacena en FREQUENCIES.</td>
	</tr>
	<tr>
	<td>RESULT = order FREQUENCIES by COUNT desc;</td><td>Ordena los niveles de registro por número (descendente) y los almacena en RESULT.</td>
	</tr>
	</table>

6. También puede guardar los resultados de una transformación mediante la instrucción `STORE`. Por ejemplo, lo siguiente guarda el valor `RESULT` en el directorio **/example/data/pigout** en el contenedor de almacenamiento predeterminado para el clúster.

		STORE RESULT into 'wasb:///example/data/pigout'

	> [AZURE.NOTE] Los datos se almacenan en el directorio especificado en los archivos denominados **part-nnnnn**. Si el directorio ya existe, recibirá un error.

7. Para salir del aviso de grunt, introduzca la siguiente instrucción.

		QUIT;

### Archivos por lotes de Pig Latin

También puede usar el comando de Pig para ejecutar Pig Latin contenido en un archivo.

3. Después de salir del símbolo de sistema de Grunt, use el comando siguiente para canalizar STDIN en un archivo denominado **pigbatch.pig**. Este archivo se creará en el directorio principal de la cuenta en la que se inició la sesión para la sesión de SSH.

		cat > ~/pigbatch.pig

4. Escriba o pegue las siguientes líneas y, a continuación, utilice Ctrl+D cuando finalice.

		LOGS = LOAD 'wasb:///example/data/sample.log';
		LEVELS = foreach LOGS generate REGEX_EXTRACT($0, '(TRACE|DEBUG|INFO|WARN|ERROR|FATAL)', 1)  as LOGLEVEL;
		FILTEREDLEVELS = FILTER LEVELS by LOGLEVEL is not null;
		GROUPEDLEVELS = GROUP FILTEREDLEVELS by LOGLEVEL;
		FREQUENCIES = foreach GROUPEDLEVELS generate group as LOGLEVEL, COUNT(FILTEREDLEVELS.LOGLEVEL) as COUNT;
		RESULT = order FREQUENCIES by COUNT desc;
		DUMP RESULT;

5. Utilice lo siguiente para ejecutar el archivo **pigbatch.pig** mediante el comando Pig.

		pig ~/pigbatch.pig

	Una vez finalizado el trabajo por lotes, debiera ver el siguiente resultado, que debe ser el mismo que cuando ha utilizado `DUMP RESULT;` en los pasos anteriores.

		(TRACE,816)
		(DEBUG,434)
		(INFO,96)
		(WARN,11)
		(ERROR,6)
		(FATAL,2)

## <a id="summary"></a>Resumen

Como puede ver, el comando Pig le permite ejecutar interactivamente operaciones de MapReduce mediante Pig Latin, así como ejecutar instrucciones almacenadas en un archivo por lotes.

## <a id="nextsteps"></a>Pasos siguientes

Para obtener información general sobre Pig en HDInsight.

* [Uso de Pig con Hadoop en HDInsight](../hdinsight-use-pig/)

Para obtener información sobre otras maneras en que puede trabajar con Hadoop en HDInsight.

* [Uso de Hive con Hadoop en HDInsight](../hdinsight-use-hive/)

* [Uso de MapReduce con Hadoop en HDInsight](../hdinsight-use-mapreduce/)

<!--HONumber=47-->
