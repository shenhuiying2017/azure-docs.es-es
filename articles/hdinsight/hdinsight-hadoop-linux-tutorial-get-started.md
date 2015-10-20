<properties
   	pageTitle="Tutorial de Hadoop: introducción a Hadoop y Hive | Microsoft Azure"
   	description="Siga este tutorial de Linux para empezar a usar Hadoop en HDInsight. Obtenga información sobre cómo aprovisionar clústeres de Linux y consultar datos con Hive."
   	services="hdinsight"
   	documentationCenter=""
   	authors="nitinme"
   	manager="paulettm"
   	editor="cgronlun"
	tags="azure-portal"/>

<tags
   	ms.service="hdinsight"
   	ms.devlang="na"
   	ms.topic="hero-article"
   	ms.tgt_pltfrm="na"
   	ms.workload="big-data"
   	ms.date="10/05/2015"
   	ms.author="nitinme"/>

# Tutorial de Hadoop: Introducción al uso de Hadoop con Hive en HDInsight en Linux

> [AZURE.SELECTOR]
- [Windows](hdinsight-hadoop-tutorial-get-started-windows.md)
- [Linux](hdinsight-hadoop-linux-tutorial-get-started.md)

Este documento ofrece una introducción rápida para HDInsight de Azure en Linux y le muestra cómo crear un clúster de Hadoop basado en Linux, cómo conectarse al clúster mediante un Shell seguro (SSH) y cómo ejecutar una consulta de Hive con datos de ejemplo que se incluyen con el clúster.

> [AZURE.NOTE]Si todavía no conoce Hadoop y macrodatos, puede obtener más información sobre los términos [Apache Hadoop](http://go.microsoft.com/fwlink/?LinkId=510084), [MapReduce](http://go.microsoft.com/fwlink/?LinkId=510086), [sistema de archivos distribuido de Hadoop (HDFS)](http://go.microsoft.com/fwlink/?LinkId=510087) y [Hive](http://go.microsoft.com/fwlink/?LinkId=510085). Para entender cómo HDInsight habilita Hadoop en Azure, consulte [Introducción a Hadoop en HDInsight](hdinsight-hadoop-introduction.md).

## Requisitos previos

Antes de empezar este tutorial de Linux para Hadoop, debe contar con lo siguiente:

- **Suscripción de Azure**: Consulte [ Obtener una versión de evaluación gratuita de Azure](http://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).

- **Un cliente de Shell seguro**: sistemas Linux, Unix y OS X ofrecen un cliente SSH mediante el comando `ssh`. Para sistemas Windows, se recomienda [PuTTY](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html).

    > [AZURE.NOTE]En los pasos descritos en este documento se usa SSH para conectarse al clúster de HDInsight, ya que SSH está disponible para todos los sistemas operativos de cliente. Para otros métodos de conexión al clúster de HDInsight, como el uso de las herramientas de HDInsight para Visual Studio o las API de REST, vea los vínculos de Hive, Pig y MapReduce en la sección [Pasos](#nextsteps) de este documento.
    
- **Claves de Shell seguro (SSH)** (opcional): puede proteger la cuenta de SSH usada para conectarse al clúster mediante una contraseña o una clave pública. El uso de una contraseña le permite comenzar rápidamente, y debe usar esta opción si desea aprovisionar un clúster y ejecutar algunos trabajos de prueba rápidamente. El uso de una clave es un método más seguro, sin embargo, requiere configuración adicional. Puede ser conveniente usar este enfoque al aprovisionar un clúster en producción. En este artículo, se usa el enfoque de contraseña. Para obtener instrucciones sobre cómo crear y usar claves SSH con HDInsight, consulte los siguientes artículos:

	-  Desde un equipo con Linux: [Utilización de SSH con HDInsight basado en Linux (Hadoop) desde Linux, Unix u OS X](hdinsight-hadoop-linux-use-ssh-unix.md).
    
	-  Desde un equipo con Windows: [Utilización de SSH con HDInsight basado en Linux (Hadoop) desde Windows](hdinsight-hadoop-linux-use-ssh-windows.md).

## <a name="provision"></a>Aprovisionamiento de un clúster de HDInsight en Linux

Cuando aprovisiona un clúster, crea los recursos de proceso de Azure que contienen recursos y servicios de Hadoop. En esta sección, se aprovisiona un clúster de HDInsight de la versión 3.2, que contiene la versión 2.2 de Hadoop. Para obtener información acerca de las diferentes versiones de HDInsight y sus contratos de nivel de servicio, consulte la página [Control de versiones de componentes de HDInsight](hdinsight-component-versioning.md). Para obtener información más detallada sobre la creación de un clúster de HDInsight, consulte [Aprovisionamiento de clústeres de HDInsight mediante opciones personalizadas][hdinsight-provision].

>[AZURE.NOTE]También puede crear clústeres de Hadoop que ejecutan el sistema operativo Windows Server. Para obtener instrucciones, consulte [Introducción a HDInsight en Windows](hdinsight-hadoop-tutorial-get-started-windows.md).

Utilice los pasos siguientes para crear un clúster nuevo:

1. Inicie sesión en el [Portal de vista previa de Azure](https://ms.portal.azure.com/).
2. Haga clic en **NUEVO**, en **Análisis de datos** y luego en **HDInsight**.

    ![Crear un nuevo clúster en el Portal de vista previa de Azure](./media/hdinsight-hadoop-linux-tutorial-get-started/HDI.CreateCluster.1.png "Crear un nuevo clúster en el Portal de vista previa de Azure")

3. En **Nombre de clúster**, seleccione **Hadoop** en **Tipo de clúster** y, en el menú desplegable **Sistema operativo de clústeres**, seleccione **Ubuntu**. Si está disponible, aparecerá una marca de verificación verde junto al Nombre de clúster.

	![Especifique el tipo y el nombre del clúster](./media/hdinsight-hadoop-linux-tutorial-get-started/HDI.CreateCluster.2.png "Especifique el tipo y el nombre del clúster")

4. Si tiene más de una suscripción, haga clic en la entrada **Suscripción** para seleccionar la suscripción de Azure que se usará para el clúster.

5. Haga clic en **Grupo de recursos** para ver una lista de grupos de recursos existentes y luego seleccione en el que desea crear el clúster. También puede hacer clic en **Crear nuevo** y, a continuación, escribir el nombre del nuevo grupo de recursos. Aparecerá una marca de verificación verde para indicar si el nuevo nombre de grupo está disponible.

	> [AZURE.NOTE]Esta entrada se establecerá de manera predeterminada en uno de sus grupos de recursos existentes, si hay alguno disponible.

6. Haga clic en **Credenciales** y luego escriba una contraseña para el usuario administrador. También debe especificar un **nombre de usuario de SSH**. En **Tipo de autenticación SSH**, haga clic en **CONTRASEÑA** y especifique una contraseña para el usuario de SSH. Haga clic en **Seleccionar** en la parte inferior para guardar la configuración de las credenciales.

	![Proporcione las credenciales del clúster](./media/hdinsight-hadoop-linux-tutorial-get-started/HDI.CreateCluster.3.png "Proporcione las credenciales del clúster")

    > [AZURE.NOTE]SSH sirve para tener acceso remoto a un clúster de HDInsight mediante una línea de comandos. El nombre de usuario y la contraseña que usa aquí se emplean al conectarse al clúster a través de SSH. Además, el nombre de usuario SSH tiene que ser único, ya que crea una cuenta de usuario en todos los nodos del clúster de HDInsight. A continuación se indican algunos de los nombres de cuenta reservados para su uso en los servicios que se ejecutan en el clúster y no pueden usarse como nombre de usuario SSH:
    >
    > root, hdiuser, storm, hbase, ubuntu, zookeeper, hdfs, yarn, mapred, hbase, hive, oozie, falcon, sqoop, admin, tez, hcat, hdinsight-zookeeper.

	Para obtener más información sobre el uso de SSH con HDInsight, vea uno de los siguientes documentos:

	* [Utilización de SSH con Hadoop en HDInsight basado en Linux desde Linux, Unix u OS X](hdinsight-hadoop-linux-use-ssh-unix.md)
	* [Utilización de SSH con Hadoop en HDInsight basado en Linux desde Windows](hdinsight-hadoop-linux-use-ssh-windows.md)


7. Haga clic en **Origen de datos** para elegir un origen de datos existente para el clúster o cree uno nuevo. Cuando aprovisiona un clúster de Hadoop en HDInsight, especifica una cuenta de Almacenamiento de Azure. Se designa un contenedor de almacenamiento de blobs desde esa cuenta como el sistema de archivos predeterminado, como en el sistema de archivos distribuido de Hadoop (HDFS). De forma predeterminada, el clúster de HDInsight se aprovisiona en el mismo centro de datos que la cuenta de almacenamiento que especifica. Para obtener más información, vea [Uso del almacenamiento de blobs de Azure con HDInsight](hdinsight-use-blob-storage.md)

	![Hoja Origen de datos](./media/hdinsight-hadoop-linux-tutorial-get-started/HDI.CreateCluster.4.png "Proporcione la configuración del origen de datos")

	Actualmente puede seleccionar una cuenta de almacenamiento de Azure como origen de datos para un clúster de HDInsight. Use lo siguiente para comprender las entradas de la hoja **Origen de datos**.

	- **Método de selección**: establézcalo en **De todas las suscripciones** para habilitar la exploración de cuentas de almacenamiento en todas sus suscripciones. Establezca esto en **Tecla de acceso** si desea especificar el **Nombre de almacenamiento** y la **Tecla de acceso** de una cuenta de almacenamiento existente.

	- **Seleccionar cuenta de almacenamiento / Crear nueva**: haga clic en **Seleccionar cuenta de almacenamiento** para examinar y seleccionar una cuenta de almacenamiento existente que desee asociar con el clúster. O bien, haga clic en **Crear nueva** para crear una nueva cuenta de almacenamiento. Use el campo que aparece para especificar el nombre de la cuenta de almacenamiento. Si el nombre está disponible, aparecerá una marca de verificación verde.

	- **Elegir contenedor predeterminado**: use esta opción para escribir el nombre del contenedor predeterminado que se usará para el clúster. Aunque se puede escribir cualquier nombre aquí, se recomienda usar el mismo nombre que el del clúster para que pueda reconocer fácilmente que el contenedor se usa para este clúster concreto.

	- **Ubicación**: región geográfica en la que se encuentra o donde se creará la cuenta de almacenamiento.

		> [AZURE.IMPORTANT]Seleccionar la ubicación del origen de datos predeterminado también establecerá la ubicación del clúster de HDInsight. El origen de datos del clúster y predeterminado deben encontrarse en la misma región.

	Haga clic en **Seleccionar** para guardar la configuración del origen de datos.

8. Haga clic en **Niveles de precios de nodo** para mostrar información acerca de los nodos que se crearán para este clúster. Establezca el número de nodos de trabajo que necesita para el clúster. El costo estimado del clúster se mostrará en la hoja.

	![Hoja Niveles de precios de nodo](./media/hdinsight-hadoop-linux-tutorial-get-started/HDI.CreateCluster.5.png "Especifique el número de nodos de clúster")

	Haga clic en **Seleccionar** para guardar la configuración de los precios del nodo.

9. En la hoja **Nuevo clúster de HDInsight**, asegúrese de que **Anclar a Panel de inicio** está seleccionado y, a continuación, haga clic en **Crear**. Esto creará el clúster y agregará un icono para él en el panel de inicio de su Portal de Azure. El icono indicará que el clúster está aprovisionando y cambiará para mostrar el icono de HDInsight cuando se haya completado el proceso.

Durante el aprovisionamiento|Aprovisionamiento completado
------------------|---------------------
	![Indicador de aprovisionamiento en el panel de inicio](./media/hdinsight-hadoop-linux-tutorial-get-started/provisioning.png)|![Icono de clúster aprovisionado](./media/hdinsight-hadoop-linux-tutorial-get-started/provisioned.png)

> [AZURE.NOTE]El clúster tardará algo de tiempo en crearse, normalmente unos 15 minutos. Use el icono del panel de inicio o la entrada **Notificaciones** de la izquierda de la página para comprobar el proceso de aprovisionamiento.

Una vez que termine el aprovisionamiento, haga clic en el icono del clúster desde el panel de inicio para iniciar la hoja del clúster.

## <a name="connect"></a> Para conectarse al clúster

Puede conectarse a un clúster de HDInsight en Linux desde un equipo con Linux o un equipo con Windows con SSH.

###Para conectarse desde un equipo con Linux

1. Abra un terminal y escriba el siguiente comando:

		ssh <username>@<clustername>-ssh.azurehdinsight.net

	Debido a que aprovisionó un clúster con la opción de creación rápida, el nombre de usuario de SSH predeterminado es **hdiuser**. Por lo tanto, el comando debe ser:

		ssh hdiuser@myhdinsightcluster-ssh.azurehdinsight.net

2. Cuando se le solicite, escriba la contraseña que proporcionó al aprovisionar el clúster. Una vez que se conecte correctamente, el símbolo del sistema cambiará a:

		hdiuser@headnode-0:~$


###Para conectarse desde un equipo con Windows

1. Descargue [PuTTY](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html) para clientes Windows.

2. Abra PuTTY. En **Category** (Categoría), haga clic en **Sesión**. En la pantalla **Basic options for your PuTTY session** (Opciones básicas de la sesión de PuTTY), escriba la dirección SSH del servidor de HDInsight en el campo **Host Name (or IP address)** (Nombre del host (o dirección IP)). La dirección SSH es el nombre de su clúster, seguido de **-ssh.azurehdinsight.net**. Por ejemplo, **myhdinsightcluster-ssh.azurehdinsight.net**.

	![Conexión a un clúster de HDInsight en Linux mediante PuTTY](./media/hdinsight-hadoop-linux-tutorial-get-started/HDI.linux.connect.putty.png)

3. Para guardar la información de conexión para un uso posterior, escriba un nombre para esta conexión en **Saved Sessions** (Sesiones guardadas) y, a continuación, haga clic en **Save** (Guardar). La conexión se agregará a la lista de sesiones guardadas.

4. Haga clic en **Open** (Abrir) para conectarse al clúster. Cuando se le pida el nombre de usuario, escriba **hdiuser**. En la contraseña, escriba la que especificó al aprovisionar el clúster. Una vez que se conecte correctamente, el símbolo del sistema cambiará a:

		hdiuser@headnode-0:~$

##<a name="hivequery"></a>Ejecución de una consulta de Hive

Una vez que se conecte al clúster con SSH, use los siguientes comandos para ejecutar una consulta de Hive:

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

En este documento, ha aprendido cómo crear un clúster de HDInsight basado en Linux mediante el portal de vista previa de Azure, cómo conectarse al clúster mediante SSH y cómo realizar consultas básicas de Hive.

Para obtener más información sobre el análisis de datos con HDInsight, consulte lo siguiente:

- Para obtener más información sobre el uso de Hive con HDInsight, incluida la forma de realizar consultas de Hive desde Visual Studio, vea [Uso de Hive con HDInsight][hdinsight-use-hive].

- Para obtener información sobre Pig, un lenguaje usado para transformar datos, vea [Uso de Pig con HDInsight][hdinsight-use-pig].

- Para obtener información sobre MapReduce, una manera de escribir programas que procesan datos en Hadoop, vea [Uso de MapReduce con HDInsight][hdinsight-use-mapreduce].

- Para obtener información sobre el uso de las herramientas de HDInsight para Visual Studio para analizar datos en HDInsight, consulte [Introducción al uso de herramientas de Hadoop de Visual Studio para HDInsight](hdinsight-hadoop-visual-studio-tools-get-started.md).

Si está listo para empezar a trabajar con sus propios datos y necesita saber más sobre cómo HDInsight almacena los datos o sobre cómo obtener datos en HDInsight, consulte lo siguiente:

- Para obtener información sobre cómo usa HDInsight el almacenamiento de blobs de Azure, consulte [Uso del almacenamiento de blobs de Azure con HDInsight](hdinsight-use-blob-storage.md).

- Para obtener información sobre cómo cargar datos en HDInsight, consulte [Carga de datos en HDInsight][hdinsight-upload-data].

Si desea información sobre cómo crear o administrar un clúster de HDInsight, consulte lo siguiente:

- Para obtener información sobre cómo administrar el clúster de HDInsight basado en Linux, consulte [Administrar clústeres de HDInsight mediante Ambari](hdinsight-hadoop-manage-ambari.md).

- Para obtener más información sobre las opciones que puede seleccionar al crear un clúster de HDInsight, consulte [Aprovisionamiento de HDInsight en Linux usando opciones personalizadas](hdinsight-hadoop-provision-linux-clusters.md).

- Si está familiarizado con Linux y Hadoop, pero quiere conocer información específica sobre Hadoop en HDInsight, consulte [Trabajo con HDInsight en Linux](hdinsight-hadoop-linux-information.md). Esto ofrece información como:

	* Direcciones URL para los servicios hospedados en el clúster, por ejemplo, Ambari y WebHCat
	* La ubicación de los archivos y ejemplos de Hadoop en el sistema de archivos local
	* El uso del Almacenamiento de Azure (WASB) en lugar de HDFS como el almacén de datos predeterminado


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

<!---HONumber=Oct15_HO3-->