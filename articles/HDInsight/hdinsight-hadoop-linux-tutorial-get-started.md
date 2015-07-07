<properties
   pageTitle="Tutorial de Hadoop: introducción a Hadoop y Hive | Microsoft Azure"
   description="Siga este tutorial de Linux para empezar a usar Hadoop en HDInsight. Obtenga información sobre cómo aprovisionar clústeres de Linux y consultar datos con Hive."
   services="hdinsight"
   documentationCenter=""
   authors="nitinme"
   manager="paulettm"
   editor="cgronlun"/>

<tags
   ms.service="hdinsight"
   ms.devlang="na"
   ms.topic="hero-article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="03/13/2015"
   ms.author="nitinme"/>

# Tutorial de Hadoop: Introducción al uso de Hadoop con Hive en HDInsight en Linux (vista previa)

> [AZURE.SELECTOR]
- [Windows](hdinsight-hadoop-tutorial-get-started-windows.md)
- [Linux](hdinsight-hadoop-linux-tutorial-get-started.md)

Este tutorial de Hadoop le permite comenzar rápidamente con HDInsight de Azure en Linux al mostrarle cómo aprovisionar un clúster de Hadoop en Linux y ejecutar una consulta Hive para extraer información importante desde datos no estructurados.


> [AZURE.NOTE]Si todavía no conoce Hadoop y Big Data, puede obtener más información sobre los términos <a href="http://go.microsoft.com/fwlink/?LinkId=510084" target="_blank">Apache Hadoop</a>, <a href="http://go.microsoft.com/fwlink/?LinkId=510086" target="_blank">MapReduce</a>, <a href="http://go.microsoft.com/fwlink/?LinkId=510087" target="_blank">sistema de archivos distribuido de Hadoop (HDFS)</a> y <a href="http://go.microsoft.com/fwlink/?LinkId=510085" target="_blank">Hive</a>. Para entender cómo HDInsight habilita Hadoop en Azure, consulte [Introducción a Hadoop en HDInsight](hdinsight-hadoop-introduction.md).


## ¿Qué logra este tutorial? ##

Supongamos que tiene un gran conjunto de datos no estructurados y desea ejecutar consultas en él para extraer alguna información significativa. Siga los pasos siguientes para lograrlo:

   ![Pasos del tutorial de Hadoop: crear una cuenta de almacenamiento; aprovisionar un clúster de Hadoop; consultar datos con Hive.](./media/hdinsight-hadoop-linux-tutorial-get-started/HDI.Linux.GetStartedFlow.png)


## Requisitos previos

Antes de empezar este tutorial de Linux para Hadoop, debe contar con lo siguiente:

- **Una suscripción de Azure**. Vea [Obtener evaluación gratuita de Azure](http://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).

**Tiempo estimado para completar el tutorial:** 30 minutos

## Apartados de este tutorial

* [Creación de una cuenta de almacenamiento de Azure](#storage)
* [Aprovisionamiento de un clúster de HDInsight Linux](#provision)
* [Envío de un trabajo de Hive en el clúster](#hivequery)
* [Pasos siguientes](#nextsteps)

## <a name="storage"></a>Creación de una cuenta de almacenamiento de Azure

HDInsight usa el almacenamiento de blobs de Azure para almacenar datos. Para obtener más información, consulte [Uso de almacenamiento de blobs de Azure con HDInsight](../hdinsight-use-blob-storage.md).

Cuando aprovisiona un clúster de HDInsight, especifica una cuenta de Almacenamiento de Azure Storage. Un contenedor de almacenamiento de blobs de esa cuenta se designa como el sistema de archivos predeterminado, justo como HDFS. El clúster de HDInsight se aprovisiona de manera predeterminada en el mismo centro de datos que la cuenta de almacenamiento que especifique.

Además de esta cuenta de almacenamiento, puede agregar otras cuentas de almacenamiento cuando configura de manera personalizada un clúster de HDInsight. Estas cuentas de almacenamiento adicionales pueden ser de la misma suscripción de Azure o de suscripciones de Azure distintas. Para obtener instrucciones, consulte [Aprovisionamiento de clústeres de HDInsight Linux usando opciones personalizadas](hdinsight-hadoop-provision-linux-clusters.md).

Para simplificar este tutorial, solamente se usan el contenedor de blobs y la cuenta de almacenamiento predeterminados. En la práctica, los archivos de datos normalmente se almacenan en una cuenta de almacenamiento designada.

**Para crear una cuenta de almacenamiento de Azure**

1. Inicie sesión en el <a href="https://manage.windowsazure.com/" target="_blank">Portal de Azure</a>.
2. Haga clic en **NUEVO** en la esquina inferior izquierda, seleccione **SERVICIOS DE DATOS**, **ALMACENAMIENTO** y, a continuación, haga clic en **CREACIÓN RÁPIDA**.

	![El Portal de Azure, donde puede usar la función de creación rápida para configurar una nueva cuenta de almacenamiento.](./media/hdinsight-hadoop-linux-tutorial-get-started/HDI.StorageAccount.QuickCreate.png)

3. Escriba información sobre la **DIRECCIÓN URL**, **UBICACIÓN** y **REPLICACIÓN** y, a continuación, haga clic en **CREAR CUENTA DE ALMACENAMIENTO**. No se admiten grupos de afinidad. La nueva cuenta de almacenamiento aparecerá en la lista de almacenamiento.

	>[AZURE.NOTE]La opción de creación rápida para aprovisionar un clúster de HDInsight Linux, como el que se usa en este tutorial, no solicita una ubicación cuando se aprovisiona el clúster. De manera predeterminada, coloca el clúster en el mismo centro de datos que la cuenta de almacenamiento. Por lo tanto, asegúrese de crear la cuenta de almacenamiento en las ubicaciones que se admiten para el clúster, que son: **este de Asia**, **sudeste de Asia**, **norte de Europa**, **oeste de Europa**, **este de EE. UU.**, **oeste de EE. UU.**, **centro y norte de EE. UU.**, **centro y sur de EE. UU**.

4. Espere hasta que la característica **ESTADO** de la nueva cuenta de almacenamiento cambie a **En línea**.
5. Seleccione la nueva cuenta de almacenamiento en la lista y haga clic en **ADMINISTRAR CLAVES DE ACCESO** en la parte inferior de la página.
7. Toma nota de la información de los valores de los campos **NOMBRE DE CUENTA DE ALMACENAMIENTO** y **CLAVE DE ACCESO PRIMARIO** (o **CLAVE DE ACCESO SECUNDARIA**). Cualquiera de estas claves funciona. Los necesitará más adelante en el tutorial.


Para obtener más información, consulte [Creación de una cuenta de almacenamiento](../storage-create-storage-account.md) y [Uso del almacenamiento con blobs de Azure con HDInsight](../hdinsight-use-blob-storage.md).

## <a name="provision"></a>Aprovisionamiento de un clúster de HDInsight en Linux

Cuando aprovisiona un clúster de Azure, aprovisiona cursos de proceso de Azure que contienen aplicaciones de Hadoop y aplicaciones relacionadas. En esta sección, aprovisionará un clúster de HDInsight en Linux con la opción de creación rápida. Esta opción usa nombres de usuario predeterminados y contenedores de almacenamiento de Azure; además, configura un clúster con HDInsight versión 3.2 (Hadoop versión 2.6, Hortonworks Data Platform versión 2.2) en ejecución en Ubuntu 12.04 LTS. Para obtener información acerca de las diferentes versiones de HDInsight y sus contratos de nivel de servicio, consulte la página [Control de versiones de componentes de HDInsight](hdinsight-component-versioning.md).

>[AZURE.NOTE]También puede crear clústeres de Hadoop que ejecutan el sistema operativo Windows Server. Para obtener instrucciones, consulte [Introducción a HDInsight](../hdinsight-get-started.md).


**Para aprovisionar un clúster de HDInsight**

1. Inicie sesión en el <a href="https://manage.windowsazure.com/" target="_blank">Portal de Azure</a>.

2. Haga clic en **NUEVO** en la esquina inferior izquierda, haga clic en **SERVICIOS DE DATOS**, **HDINSIGHT** y, a continuación, en **HADOOP EN LINUX**.

	![Creación de un clúster de Hadoop en HDInsight.](./media/hdinsight-hadoop-linux-tutorial-get-started/HDI.QuickCreateCluster.png)

4. Escriba o seleccione los siguientes valores:

	<table border="1">
<tr><th>Nombre</th><th>Valor</th></tr>
<tr><td>Cluster Name</td><td>Nombre del clúster.</td></tr>
<tr><td>Cluster Size</td><td>Número de nodos de datos que desea implementar. El valor predeterminado es 4. Pero también están disponibles las opciones para usar 1 o 2 nodos de datos en la lista desplegable. Con la opción <strong>Creación personalizada</strong> puede especificarse un número cualquiera de nodos de clúster. Hay disponible información sobre las tarifas de facturación para varios tamaños de clúster. Haga clic en el símbolo <strong>?</strong> justo encima del cuadro desplegable y siga el vínculo del elemento emergente.</td></tr>
<tr><td>Password</td><td>La contraseña de la cuenta <i>HTTP</i> (nombre de usuario predeterminado: admin) y la cuenta <i>SSH</i> (nombre de usuario predeterminado: hdiuser). Tenga en cuenta que estas NO son las cuentas de administrador para las máquinas virtuales en las que se aprovisionan los clústeres. </td></tr>

<tr><td>Cuenta de almacenamiento</td><td>Seleccione la cuenta de almacenamiento que ha creado en el cuadro desplegable. <br/>

Una vez elegida una cuenta de almacenamiento, no se puede cambiar. En caso de quitarla, el clúster dejará de estar disponible para su uso. El clúster de HDInsight se coloca en el mismo centro de datos que la cuenta de almacenamiento.
</td></tr>
</table>Guarde una copia del nombre del clúster. La necesitará más adelante en el tutorial.


5. Haga clic en **CREAR CLÚSTER DE HDINSIGHT**. Cuando finalice el aprovisionamiento, la columna de estado muestra **En ejecución**.

	>[AZURE.NOTE]El procedimiento anterior crea un clúster de Linux con la opción de creación rápida que usa los contenedores de almacenamiento de Azure y un nombre de usuario de SSH predeterminado. Para crear un clúster con opciones personalizadas, como usar la clave SSH para autenticación o usar cuentas de almacenamiento adicionales, consulte [Aprovisionamiento de clústeres de HDInsight Linux usando opciones personalizadas](hdinsight-hadoop-provision-linux-clusters.md).


## <a name="hivequery"></a>Envío de un trabajo de Hive en el clúster
Ahora que ha aprovisionado un clúster de HDInsight Linux, el siguiente paso es ejecutar un trabajo de Hive de ejemplo a datos de ejemplo de consulta (sample.log) que se incluye con los clústeres de HDInsight. Los datos de ejemplo contienen la información de registro, que incluye los errores, información, advertencias y seguimiento. Consultamos estos datos para recuperar todos los registros de error que tengan una gravedad específica. Debe realizar los siguientes pasos para ejecutar una consulta de Hive en un clúster de HDInsight Linux:

- Conexión a un clúster de Linux
- Ejecución de un trabajo de Hive



### Para conectarse a un clúster

Puede conectarse a un clúster de HDInsight en Linux desde un equipo con Linux o un equipo con Windows con SSH.

**Para conectarse desde un equipo con Linux**

1. Abra un terminal y escriba el siguiente comando:

		ssh <username>@<clustername>-ssh.azurehdinsight.net

	Debido a que aprovisionó un clúster con la opción de creación rápida, el nombre de usuario de SSH predeterminado es **hdiuser**. Por lo tanto, el comando debe ser:

		ssh hdiuser@myhdinsightcluster-ssh.azurehdinsight.net

2. Cuando se le solicite, escriba la contraseña que proporcionó al aprovisionar el clúster. Una vez que se conecte correctamente, el símbolo del sistema cambiará a:

		hdiuser@headnode-0:~$


**Para conectarse desde un equipo con Windows**

1. Descargue <a href="http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html" target="_blank">PuTTY</a> para clientes Windows.

2. Abra PuTTY. En **Category** (Categoría), haga clic en **Sesión**. En la pantalla **Basic options for your PuTTY session** (Opciones básicas de la sesión de PuTTY), escriba la dirección SSH del servidor de HDInsight en el campo **Host Name (or IP address)** (Nombre del host (o dirección IP)). La dirección SSH es el nombre de su clúster, seguido de **-ssh.azurehdinsight.net**. Por ejemplo, **myhdinsightcluster-ssh.azurehdinsight.net**.

	![Conexión a un clúster de HDInsight en Linux mediante PuTTY](./media/hdinsight-hadoop-linux-tutorial-get-started/HDI.linux.connect.putty.png)

3. Para guardar la información de conexión para un uso posterior, escriba un nombre para esta conexión en **Saved Sessions** (Sesiones guardadas) y, a continuación, haga clic en **Save** (Guardar). La conexión se agregará a la lista de sesiones guardadas.

4. Haga clic en **Open** (Abrir) para conectarse al clúster. Cuando se le pida el nombre de usuario, escriba **hdiuser**. En la contraseña, escriba la que especificó al aprovisionar el clúster. Una vez que se conecte correctamente, el símbolo del sistema cambiará a:

		hdiuser@headnode-0:~$

### Para ejecutar un trabajo de Hive

Una vez que se conecte al clúster con SSH, use los siguientes comandos para ejecutar una consulta de Hive.

1. Inicie la interfaz de línea de comando (CLI) de Hive con el siguiente comando en el símbolo del sistema:

		hive

2. Con la CLI, escriba las siguientes instrucciones para crear una tabla nueva llamada **log4jLogs** usando los datos de ejemplo ya disponibles en el clúster:

		DROP TABLE log4jLogs;
		CREATE EXTERNAL TABLE log4jLogs(t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string)
		ROW FORMAT DELIMITED FIELDS TERMINATED BY ' '
		STORED AS TEXTFILE LOCATION 'wasb:///example/data/';
		SELECT t4 AS sev, COUNT(*) AS cnt FROM log4jLogs WHERE t4 = '[ERROR]' GROUP BY t4;

	Estas instrucciones realizan las acciones siguientes:

	- **DROP TABLE**: elimina la tabla y el archivo de datos si la tabla ya existe.
	- **CREATE EXTERNAL TABLE**: crea una tabla "externa" nueva en Hive. Las tablas externas solo almacenan la definición de tabla en Hive; los datos quedan en la ubicación original.
	- **ROW FORMAT**: indica cómo se da formato a los datos de Hive. En este caso, los campos de cada registro se separan mediante un espacio.
	- **STORED AS TEXTFILE LOCATION**: indica a Hive dónde se almacenan los datos (el directorio example/data) y que se almacenan como texto.
	- **SELECT**: selecciona un número de todas las filas donde la columna t4 contiene el valor [ERROR].

	>[AZURE.NOTE]Las tablas externas se deben usar cuando espera que un origen externo, como por ejemplo un proceso de carga de datos automático, u otra operación MapReduce, actualice los datos subyacentes, pero siempre desea que las consultas de Hive usen los datos más recientes. La eliminación de una tabla externa *no* elimina los datos, solamente la definición de tabla.

	Esta acción devuelve la siguiente salida:

		Query ID = hdiuser_20150116000202_cceb9c6b-4356-4931-b9a7-2c373ebba493
		Total jobs = 1
		Launching Job 1 out of 1
		Number of reduce tasks not specified. Estimated from input data size: 1
		In order to change the average load for a reducer (in bytes):
		  set hive.exec.reducers.bytes.per.reducer=<number>
		In order to limit the maximum number of reducers:
		  set hive.exec.reducers.max=<number>
		In order to set a constant number of reducers:
		  set mapreduce.job.reduces=<number>
		Starting Job = job_1421200049012_0006, Tracking URL = <URL>:8088/proxy/application_1421200049012_0006/
		Kill Command = /usr/hdp/2.2.1.0-2165/hadoop/bin/hadoop job  -kill job_1421200049012_0006
		Hadoop job information for Stage-1: number of mappers: 1; number of reducers: 1
		2015-01-16 00:02:40,823 Stage-1 map = 0%,  reduce = 0%
		2015-01-16 00:02:55,488 Stage-1 map = 100%,  reduce = 0%, Cumulative CPU 3.32 sec
		2015-01-16 00:03:05,298 Stage-1 map = 100%,  reduce = 100%, Cumulative CPU 5.62 sec
		MapReduce Total cumulative CPU time: 5 seconds 620 msec
		Ended Job = job_1421200049012_0006
		MapReduce Jobs Launched:
		Stage-Stage-1: Map: 1  Reduce: 1   Cumulative CPU: 5.62 sec   HDFS Read: 0 HDFS Write: 0 SUCCESS
		Total MapReduce CPU Time Spent: 5 seconds 620 msec
		OK
		[ERROR]    3
		Time taken: 60.991 seconds, Fetched: 1 row(s)

	Observe que la salida contiene **[ERROR] 3**, debido a que son tres filas las que contienen este valor.

3. Use las siguientes instrucciones para crear una nueva tabla "interna" llamada **errorLogs**:

		CREATE TABLE IF NOT EXISTS errorLogs (t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string) STORED AS ORC;
		INSERT OVERWRITE TABLE errorLogs SELECT t1, t2, t3, t4, t5, t6, t7 FROM log4jLogs WHERE t4 = '[ERROR]';


	Estas instrucciones realizan las acciones siguientes:

	- **CREATE TABLE IF NOT EXISTS**: crea una tabla, si todavía no existe. Dado que la palabra clave **EXTERNAL** no es usa, se trata de una tabla interna, la que se almacena en el almacenamiento de datos de Hive y es administrada completamente por Hive. A diferencia de las tablas externas, la eliminación de una tabla interna también eliminará los datos subyacentes.
	- **STORED AS ORC**: almacena los datos en el formato Optimized Row Columnar (ORC). Se trata de un formato altamente optimizado y eficiente para almacenar datos de Hive.
	- **INSERT OVERWRITE ... SELECT**: selecciona filas de la tabla **log4jLogs** que contienen [ERROR] y, a continuación, inserta los datos en la tabla **errorLogs**.

4. Para comprobar que solamente las filas que contienen [ERROR] en la columna t4 se almacenaron en la tabla **errorLogs**, use la siguiente instrucción para devolver todas las filas de **errorLogs**.

		SELECT * from errorLogs;

	La consola debe mostrar la siguiente salida:

		2012-02-03	18:35:34	SampleClass0	[ERROR]	 incorrect		id
		2012-02-03	18:55:54	SampleClass1	[ERROR]	 incorrect		id
		2012-02-03	19:25:27	SampleClass4	[ERROR]	 incorrect		id
		Time taken: 0.987 seconds, Fetched: 3 row(s)

	Todos los datos devueltos deben corresponder con registros de [ERROR].


## <a name="nextsteps"></a>Pasos siguientes
En este tutorial de Linux, aprendió a aprovisionar un clúster de Hadoop en Linux con HDInsight y a ejecutar una consulta de Hive en él mediante SSH. Para obtener más información, consulte los artículos siguientes:

- [Aprovisionamiento de HDInsight en Linux usando opciones personalizadas](hdinsight-hadoop-provision-linux-clusters.md)
- [Trabajo con HDInsight en Linux](hdinsight-hadoop-linux-information.md)
- [Administración de clústeres de HDInsight con Ambari](hdinsight-hadoop-manage-ambari.md)
- [Uso de MapReduce con HDInsight][hdinsight-use-mapreduce]
- [Uso de Hive con HDInsight][hdinsight-use-hive]
- [Uso de Pig con HDInsight][hdinsight-use-pig]
- [Uso del almacenamiento de blobs de Azure con HDInsight](../hdinsight-use-blob-storage.md)
- [Carga de datos en HDInsight][hdinsight-upload-data]


[1]: ../HDInsight/hdinsight-hadoop-visual-studio-tools-get-started.md

[hdinsight-provision]: hdinsight-provision-clusters.md
[hdinsight-admin-powershell]: hdinsight-administer-use-powershell.md
[hdinsight-upload-data]: hdinsight-upload-data.md
[hdinsight-use-mapreduce]: hdinsight-use-mapreduce.md
[hdinsight-use-hive]: hdinsight-use-hive.md
[hdinsight-use-pig]: hdinsight-use-pig.md

[powershell-download]: http://go.microsoft.com/fwlink/p/?linkid=320376&clcid=0x409
[powershell-install-configure]: ../install-configure-powershell.md
[powershell-open]: ../install-configure-powershell.md#Install

[img-hdi-dashboard]: ./media/hdinsight-hadoop-tutorial-get-started-windows/HDI.dashboard.png
[img-hdi-dashboard-query-select]: ./media/hdinsight-hadoop-tutorial-get-started-windows/HDI.dashboard.query.select.png
[img-hdi-dashboard-query-select-result]: ./media/hdinsight-hadoop-tutorial-get-started-windows/HDI.dashboard.query.select.result.png
[img-hdi-dashboard-query-select-result-output]: ./media/hdinsight-hadoop-tutorial-get-started-windows/HDI.dashboard.query.select.result.output.png
[img-hdi-dashboard-query-browse-output]: ./media/hdinsight-hadoop-tutorial-get-started-windows/HDI.dashboard.query.browse.output.png
[image-hdi-clusterstatus]: ./media/hdinsight-hadoop-tutorial-get-started-windows/HDI.ClusterStatus.png
[image-hdi-gettingstarted-powerquery-importdata]: ./media/hdinsight-hadoop-tutorial-get-started-windows/HDI.GettingStarted.PowerQuery.ImportData.png
[image-hdi-gettingstarted-powerquery-importdata2]: ./media/hdinsight-hadoop-tutorial-get-started-windows/HDI.GettingStarted.PowerQuery.ImportData2.png
 

<!---HONumber=62-->