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
   	ms.date="08/07/2015"
   	ms.author="nitinme"/>

# Tutorial de Hadoop: Introducción al uso de Hadoop con Hive en HDInsight en Linux (vista previa)

> [AZURE.SELECTOR]
- [Windows](hdinsight-hadoop-tutorial-get-started-windows.md)
- [Linux](hdinsight-hadoop-linux-tutorial-get-started.md)

Este tutorial de Hadoop le permite comenzar rápidamente con HDInsight de Azure en Linux, ya que le muestra cómo aprovisionar un clúster de Hadoop en Linux y ejecutar una consulta de Hive.


> [AZURE.NOTE]Si todavía no conoce Hadoop y Big Data, puede obtener más información sobre los términos <a href="http://go.microsoft.com/fwlink/?LinkId=510084" target="_blank">Apache Hadoop</a>, <a href="http://go.microsoft.com/fwlink/?LinkId=510086" target="_blank">MapReduce</a>, <a href="http://go.microsoft.com/fwlink/?LinkId=510087" target="_blank">sistema de archivos distribuido de Hadoop (HDFS)</a> y <a href="http://go.microsoft.com/fwlink/?LinkId=510085" target="_blank">Hive</a>. Para entender cómo HDInsight habilita Hadoop en Azure, consulte [Introducción a Hadoop en HDInsight](hdinsight-hadoop-introduction.md).


## ¿Qué logra este tutorial?

Supongamos que tiene un gran conjunto de datos no estructurados y desea ejecutar consultas en él para extraer alguna información significativa. Siga los pasos siguientes para lograrlo:

   ![Pasos del tutorial de Hadoop: crear una cuenta de almacenamiento; aprovisionar un clúster de Hadoop; consultar datos con Hive.](./media/hdinsight-hadoop-linux-tutorial-get-started/HDI.Linux.GetStartedFlow.png)


## Requisitos previos

Antes de empezar este tutorial de Linux para Hadoop, debe contar con lo siguiente:

- **Una suscripción de Azure**. Consulte [Obtención de una versión de evaluación gratuita](http://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).
- **Claves de Shell seguro (SSH)**. Si desea tener acceso remoto a un clúster de Linux mediante SSH con una clave en lugar de una contraseña. Usar una clave es el método recomendado, puesto que es más seguro. Para obtener instrucciones sobre cómo generar claves SSH, consulte los siguientes artículos:
	-  Desde un equipo con Linux: [Utilización de SSH con HDInsight basado en Linux (Hadoop) desde Linux, Unix u OS X](hdinsight-hadoop-linux-use-ssh-unix.md).
	-  Desde un equipo con Windows: [Utilización de SSH con HDInsight basado en Linux (Hadoop) desde Windows](hdinsight-hadoop-linux-use-ssh-windows.md).

**Tiempo estimado para completar el tutorial:** 30 minutos

## <a name="provision"></a>Aprovisionamiento de un clúster de HDInsight en Linux

Cuando aprovisiona un clúster, aprovisiona recursos de proceso de Azure que contienen aplicaciones de Hadoop y aplicaciones relacionadas. En esta sección, se aprovisiona un clúster de HDInsight versión 3.2. También puede crear clústeres de Hadoop para otras versiones. Para obtener instrucciones, consulte [Aprovisionamiento de clústeres de HDInsight usando opciones personalizadas][hdinsight-provision]. Para obtener información acerca de las diferentes versiones de HDInsight y sus contratos de nivel de servicio, consulte la página [Control de versiones de componentes de HDInsight](hdinsight-component-versioning.md).

>[AZURE.NOTE]También puede crear clústeres de Hadoop que ejecutan el sistema operativo Windows Server. Para obtener instrucciones, consulte [Introducción a HDInsight en Windows](hdinsight-hadoop-tutorial-get-started-windows.md).


**Para aprovisionar un clúster de HDInsight**

1. Inicie sesión en el [Portal de vista previa de Azure](https://ms.portal.azure.com/).
2. Haga clic en **NUEVO**, haga clic en **Análisis de datos** y, a continuación, haga clic en **HDInsight**.

    ![Crear un nuevo clúster en el Portal de vista previa de Azure](./media/hdinsight-hadoop-linux-tutorial-get-started/HDI.CreateCluster.1.png "Crear un nuevo clúster en el Portal de vista previa de Azure")

3. Escriba un **Nombre de clúster**, seleccione **Hadoop** para el **Tipo de clúster** y, en el menú desplegable **Sistema operativo de clúster**, seleccione **Ubuntu**. Si está disponible, aparecerá una marca de verificación verde junto al Nombre de clúster.

	![Especifique el tipo y el nombre del clúster](./media/hdinsight-hadoop-linux-tutorial-get-started/HDI.CreateCluster.2.png "Especifique el tipo y el nombre del clúster")

4. Si tiene más de una suscripción, haga clic en la entrada **Suscripción** para seleccionar la suscripción de Azure que se usará para el clúster.

5. Haga clic en **Grupo de recursos** para ver una lista de grupos de recursos existentes y, a continuación, seleccionar en el que desea crear el clúster. También puede hacer clic en **Crear nuevo** y, a continuación, escribir el nombre del nuevo grupo de recursos. Aparecerá una marca de verificación verde para indicar si el nuevo nombre de grupo está disponible.

	> [AZURE.NOTE]Esta entrada se establecerá de manera predeterminada en uno de sus grupos de recursos existentes, si hay alguno disponible.

6. Haga clic en **Credenciales** y, a continuación, escriba una contraseña para el usuario administrador. También debe especificar un **Nombre de usuario de SSH** y una **CONTRASEÑA** o una **CLAVE PÚBLICA**, que se usará para autenticar al usuario de SSH. Es recomendable usar una clave pública. Haga clic en **Seleccionar** en la parte inferior para guardar la configuración de las credenciales.

	![Proporcione las credenciales del clúster](./media/hdinsight-hadoop-linux-tutorial-get-started/HDI.CreateCluster.3.png "Proporcione las credenciales del clúster")

	Para obtener más información sobre el uso de SSH con HDInsight, vea uno de los siguientes artículos:

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

9. En la hoja **Nuevo clúster de HDInsight**, asegúrese de que **Anclar a Panel de inicio** está seleccionado y, a continuación, haga clic en **Crear**. Esto creará el clúster y agregará un icono para él en el panel de inicio de su Portal de Azure. El icono indicará que el clúster está aprovisionando y cambiará para mostrar el icono de HDInsight cuando se haya completado el aprovisionamiento.

Durante el aprovisionamiento|Aprovisionamiento completado
------------------|---------------------
	![Indicador de aprovisionamiento en el panel de inicio](./media/hdinsight-hadoop-linux-tutorial-get-started/provisioning.png)|![Icono de clúster aprovisionado](./media/hdinsight-hadoop-linux-tutorial-get-started/provisioned.png)

> [AZURE.NOTE] El clúster tardará algún tiempo en crearse, normalmente unos 15 minutos. Use el icono del panel de inicio o la entrada **Notificaciones** de la izquierda de la página para comprobar el proceso de aprovisionamiento.

Una vez que termine el aprovisionamiento, haga clic en el icono del clúster desde el panel de inicio para iniciar la hoja del clúster.

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

- [Administrar clústeres HDInsight mediante Ambari](hdinsight-hadoop-manage-ambari.md): los clústeres de HDInsight basados en Linux usan Ambari para la administración y supervisión de servicios de Hadoop. La interfaz de usuario web Ambari está disponible en cada clúster en https://CLUSTERNAME.azurehdinsight.net.

	> [AZURE.IMPORTANT] Si bien es posible acceder a muchas secciones de la web de Ambari directamente a través de Internet, la interfaz de usuario web de los servicios de Hadoop como el Administrador de recursos o el Historial de trabajos requieren el uso de un túnel SSH. Para obtener más información sobre el uso de túneles SSH con HDInsight, consulte los siguientes artículos:
	>
	> * [Utilización de SSH con Hadoop en HDInsight basado en Linux desde Linux, Unix u OS X](hdinsight-hadoop-linux-use-ssh-unix.md#tunnel)
	> * [Utilización de SSH con Hadoop en HDInsight basado en Linux desde Windows](hdinsight-hadoop-linux-use-ssh-windows.md#tunnel)

- [Aprovisionamiento de HDInsight en Linux mediante opciones personalizadas](hdinsight-hadoop-provision-linux-clusters.md): obtenga más información detallada sobre cómo aprovisionar clústeres de HDInsight.

- [Trabajar con HDInsight en Linux](hdinsight-hadoop-linux-information.md): si ya está familiarizado con Hadoop en las plataformas Linux, este documento ofrece orientación sobre información específica de Azure, como:

	* Direcciones URL para los servicios hospedados en el clúster, por ejemplo, Ambari y WebHCat
	* La ubicación de los archivos y ejemplos de Hadoop en el sistema de archivos local
	* El uso del Almacenamiento de Azure (WASB) en lugar de HDFS como el almacén de datos predeterminado

- Para obtener más información acerca de Hive, o para obtener información acerca de Pig y MapReduce, consulte lo siguiente:

	- [Uso de MapReduce con HDInsight][hdinsight-use-mapreduce]
	- [Uso de Hive con HDInsight][hdinsight-use-hive]
	- [Uso de Pig con HDInsight][hdinsight-use-pig]

- Para obtener más información sobre cómo trabajar con el Almacenamiento de Azure usado por el clúster de HDInsight, consulte lo siguiente:

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

<!---HONumber=August15_HO8-->