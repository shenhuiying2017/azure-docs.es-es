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
   	ms.date="01/13/2016"
   	ms.author="nitinme"/>

# Tutorial de Hadoop: Introducción al uso de Hadoop con Hive en HDInsight en Linux

> [AZURE.SELECTOR]
- [Windows](hdinsight-hadoop-tutorial-get-started-windows.md)
- [Linux](hdinsight-hadoop-linux-tutorial-get-started.md)

Este documento le permite comenzar rápidamente con HDInsight de Azure en Linux al mostrarle cómo crear un clúster de Hadoop basado en Linux, cómo abrir la interfaz de usuario de la web de Ambari y, después, cómo ejecutar una consulta de Hive mediante la vista de Hive de Ambari.

> [AZURE.NOTE]Si todavía no conoce Hadoop y macrodatos, puede obtener más información sobre los términos [Apache Hadoop](http://go.microsoft.com/fwlink/?LinkId=510084), [MapReduce](http://go.microsoft.com/fwlink/?LinkId=510086), [sistema de archivos distribuido de Hadoop (HDFS)](http://go.microsoft.com/fwlink/?LinkId=510087) y [Hive](http://go.microsoft.com/fwlink/?LinkId=510085). Para entender cómo HDInsight habilita Hadoop en Azure, consulte [Introducción a Hadoop en HDInsight](hdinsight-hadoop-introduction.md).

## Requisitos previos

Antes de empezar este tutorial de Linux para Hadoop, debe contar con lo siguiente:

- **Suscripción de Azure**: Consulte [ Obtener una versión de evaluación gratuita de Azure](http://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).

## <a name="provision"></a>Aprovisionamiento de un clúster de HDInsight en Linux

Cuando aprovisiona un clúster, crea los recursos de proceso de Azure que contienen recursos y servicios de Hadoop. En esta sección, se aprovisiona un clúster de HDInsight de la versión 3.2, que contiene la versión 2.2 de Hadoop. Para obtener información acerca de las diferentes versiones de HDInsight y sus contratos de nivel de servicio, consulte la página [Control de versiones de componentes de HDInsight](hdinsight-component-versioning.md). Para más información sobre la creación de un clúster de HDInsight, consulte [Aprovisionamiento de clústeres Apache Spark en HDInsight mediante opciones personalizadas][hdinsight-provision].

>[AZURE.NOTE]También puede crear clústeres de Hadoop que ejecutan el sistema operativo Windows Server. Para obtener instrucciones, consulte [Tutorial de Hadoop: Introducción al uso de Hadoop en HDInsight en Windows](hdinsight-hadoop-tutorial-get-started-windows.md).

Utilice los pasos siguientes para crear un clúster nuevo:

1. Inicie sesión en el [Portal de Azure](https://ms.portal.azure.com/).
2. Haga clic en **NUEVO**, en **Análisis de datos** y luego en **HDInsight**.

    ![Creación de un clúster en el Portal de Azure](./media/hdinsight-hadoop-linux-tutorial-get-started/HDI.CreateCluster.1.png "Creación de un clúster en el Portal de Azure")

3. Escriba un **Nombre de clúster**, seleccione **Hadoop** en **Tipo de clúster** y, en la lista desplegable **Sistema operativo de clústeres**, seleccione **Ubuntu**. Si está disponible, aparecerá una marca de verificación verde junto al Nombre de clúster.

	![Especifique el tipo y el nombre del clúster](./media/hdinsight-hadoop-linux-tutorial-get-started/HDI.CreateCluster.2.png "Especifique el tipo y el nombre del clúster")

4. Si tiene más de una suscripción, haga clic en la entrada **Suscripción** para seleccionar la suscripción de Azure que se usará para el clúster.

5. Haga clic en **Grupo de recursos** para ver una lista de grupos de recursos existentes y seleccione el grupo en el que quiere crear el clúster. También puede hacer clic en **Crear nuevo** y luego escribir el nombre del nuevo grupo de recursos. Aparecerá una marca de verificación verde para indicar si el nuevo nombre de grupo está disponible.

	> [AZURE.NOTE]Esta entrada se establecerá de manera predeterminada en uno de sus grupos de recursos existentes, si hay alguno disponible.

6. Haga clic en **Credenciales** y escriba una contraseña para el usuario administrador. También debe especificar un **nombre de usuario de SSH**. En **Tipo de autenticación SSH**, haga clic en **CONTRASEÑA** y especifique una contraseña para el usuario de SSH. Haga clic en **Seleccionar** en la parte inferior para guardar la configuración de las credenciales.

	![Proporcione las credenciales del clúster](./media/hdinsight-hadoop-linux-tutorial-get-started/HDI.CreateCluster.3.png "Proporcione las credenciales del clúster")

    > [AZURE.NOTE]SSH sirve para tener acceso remoto a un clúster de HDInsight mediante una línea de comandos. El nombre de usuario y la contraseña que usa aquí se emplean al conectarse al clúster a través de SSH.

	Para obtener más información sobre el uso de SSH con HDInsight, consulta uno de los siguientes documentos:

	* [Utilización de SSH con Hadoop en HDInsight basado en Linux desde Linux, Unix u OS X](hdinsight-hadoop-linux-use-ssh-unix.md)
	* [Utilización de SSH con Hadoop en HDInsight basado en Linux desde Windows](hdinsight-hadoop-linux-use-ssh-windows.md)


7. Haga clic en **Origen de datos** para elegir un origen de datos existente para el clúster o crear uno nuevo. Cuando aprovisiona un clúster de Hadoop en HDInsight, especifica una cuenta de Almacenamiento de Azure. Se designa un contenedor de almacenamiento de blobs desde esa cuenta como el sistema de archivos predeterminado, como en el sistema de archivos distribuido de Hadoop (HDFS). De forma predeterminada, el clúster de HDInsight se aprovisiona en el mismo centro de datos que la cuenta de almacenamiento que especifica. Para obtener más información, vea [Uso del almacenamiento de blobs de Azure con HDInsight](hdinsight-use-blob-storage.md)

	![Hoja Origen de datos](./media/hdinsight-hadoop-linux-tutorial-get-started/HDI.CreateCluster.4.png "Proporcione la configuración del origen de datos")

	Actualmente puede seleccionar una cuenta de almacenamiento de Azure como origen de datos para un clúster de HDInsight. Use lo siguiente para comprender las entradas de la hoja **Origen de datos**.

	- **Método de selección**: establézcalo en **De todas las suscripciones** para habilitar la exploración de cuentas de almacenamiento en todas sus suscripciones. Establézcalo en **Clave de acceso** si desea especificar el **Nombre de almacenamiento** y la **Clave de acceso** de una cuenta de almacenamiento existente.

	- **Seleccionar cuenta de almacenamiento o Crear nueva**: haga clic en **Seleccionar cuenta de almacenamiento** para buscar y seleccionar una cuenta de almacenamiento existente que desee asociar con el clúster. O bien, haga clic en **Crear nueva** para crear una nueva cuenta de almacenamiento. Use el campo que aparece para especificar el nombre de la cuenta de almacenamiento. Si el nombre está disponible, aparecerá una marca de verificación verde.

	- **Elegir contenedor predeterminado**: use esta opción para escribir el nombre del contenedor predeterminado que se usará para el clúster. Aunque se puede escribir cualquier nombre aquí, se recomienda usar el mismo nombre que el del clúster para que pueda reconocer fácilmente que el contenedor se usa para este clúster concreto.

	- **Ubicación**: región geográfica en la que se encuentra o donde se creará la cuenta de almacenamiento.

		> [AZURE.IMPORTANT]Seleccionar la ubicación del origen de datos predeterminado también establecerá la ubicación del clúster de HDInsight. El origen de datos del clúster y predeterminado deben encontrarse en la misma región.

	Haga clic en **Seleccionar** para guardar la configuración del origen de datos.

8. Haga clic en **Planes de tarifa de nodo** para mostrar información sobre los nodos que se creen para este clúster. Establezca el número de nodos de trabajo que necesita para el clúster. El costo estimado del clúster se mostrará en la hoja.

	![Hoja Niveles de precios de nodo](./media/hdinsight-hadoop-linux-tutorial-get-started/HDI.CreateCluster.5.png "Especifique el número de nodos de clúster")
    
    > [AZURE.IMPORTANT]Si planea crear más de 32 nodos de trabajo, en la creación de clústeres o al cambiar el tamaño del clúster después de la creación, debe seleccionar un tamaño de nodo principal con al menos 8 núcleos y 14 GB de RAM.
    >
    > Para obtener más información acerca de los tamaños de nodo y los costos asociados, consulte [Precios de HDInsight](https://azure.microsoft.com/pricing/details/hdinsight/).

	Haga clic en **Seleccionar** para guardar la configuración de precios de nodos.

9. En la hoja **Nuevo clúster de HDInsight**, asegúrese de que **Anclar a Panel de inicio** está seleccionado y haga clic en **Crear**. Esto creará el clúster y agregará un icono para él en el panel de inicio de su Portal de Azure. El icono indicará que el clúster está aprovisionando y cambiará para mostrar el icono de HDInsight cuando se haya completado el proceso.

Durante el aprovisionamiento|Aprovisionamiento completado
------------------|---------------------
	![Indicador de aprovisionamiento en el panel de inicio](./media/hdinsight-hadoop-linux-tutorial-get-started/provisioning.png)|![Icono de clúster aprovisionado](./media/hdinsight-hadoop-linux-tutorial-get-started/provisioned.png)

> [AZURE.NOTE]El clúster tardará algo de tiempo en crearse, normalmente unos 15 minutos. Use el icono del panel de inicio o la entrada **Notificaciones** de la izquierda de la página para comprobar el proceso de aprovisionamiento.

Una vez que termine el aprovisionamiento, haga clic en el icono del clúster desde el panel de inicio para iniciar la hoja del clúster.

##Conexión a la vista de Hive

Las vistas de Ambari proporcionan varias utilidades a través de una página web. En las secciones siguientes, usará la vista de Hive para ejecutar consultas de Hive en el clúster de HDInsight.

> [AZURE.NOTE]Ambari es una utilidad de administración y supervisión proporcionada con los clústeres de HDInsight basados en Linux. Ambari cuenta con muchas funcionalidades que no se tratan en este documento. Para más información, consulte [Administración de clústeres de HDInsight con la interfaz de usuario web de Ambari](hdinsight-hadoop-manage-ambari.md).

Para mostrar las vistas de Ambari en el Portal de Azure, seleccione el clúster de HDInsight y, después, seleccione __Vistas de Ambari__ en la sección __Vínculos rápidos__.

![Sección Vínculos rápidos](./media/hdinsight-hadoop-linux-tutorial-get-started/quicklinks.png)

También puede navegar directamente a Ambari yendo a https://CLUSTERNAME.azurehdinsight.net con un explorador web (donde __CLUSTERNAME__ es el nombre del clúster de HDInsight) y después seleccionando el conjunto de cuadrados en el menú de página (junto al vínculo y botón __Admin__ de la izquierda de la página) para mostrar las vistas disponibles. Seleccione la __Vista de Hive__.

![Selección de vistas de Ambari](./media/hdinsight-hadoop-linux-tutorial-get-started/selecthiveview.png).

> [AZURE.NOTE]Al acceder a Ambari, se le pedirá autenticarse en el sitio. Escriba el nombre de la cuenta del administrador (de manera predeterminada `admin`) y la contraseña que usó al crear el clúster.

Debería ver una página similar a la siguiente:

![Imagen de la página de vista de Hive, que contiene una sección del Editor de consultas](./media/hdinsight-hadoop-linux-tutorial-get-started/hiveview.png)

##<a name="hivequery"></a>Ejecución de una consulta de Hive

Use los pasos siguientes desde la vista de Hive para ejecutar una consulta de Hive en los datos que se incluyen con el clúster.

1. En la sección __Editor de consultas__ de la página, pegue las siguientes instrucciones de HiveQL en la hoja de cálculo:

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

2. Use el botón __Ejecutar__, situado en la parte inferior del Editor de consultas, para iniciar la consulta. Debe volverse de color naranja y el texto cambiará a __Detener ejecución__. Debe aparecer la sección __Resultados del proceso de consulta__ bajo el Editor de consultas y mostrar información sobre el trabajo.

    > [AZURE.IMPORTANT]Es posible que algunos exploradores no puedan actualizar correctamente la información de registro o los resultados. Si se ejecuta un trabajo y parece ejecutarse eternamente sin actualizar el registro o sin devolver resultados, pruebe a usar en su lugar Mozilla FireFox o Google Chrome.
    
3. Cuando haya finalizado la consulta, la sección __Resultados del proceso de consulta__ mostrará los resultados de la operación. El botón __Detener ejecución__ volverá a cambiar al botón __Ejecutar__ de color verde. La pestaña __Resultados__ debe contener la información siguiente:

        sev       cnt
        [ERROR]   3

    La pestaña __Registros__ puede usarse para ver la información de registro creada por el trabajo. Puede usarlo para solucionar problemas si se produjera alguno con una consulta.
    
    > [AZURE.TIP]Tenga en cuenta el elemento desplegable __Guardar resultados__ en la esquina superior izquierda de la sección __Resultados del proceso de consulta__; úsela para descargar los resultados o guardarlos en el almacenamiento de HDInsight como un archivo CSV.

3. Seleccione las primeras cuatro líneas de esta consulta y después elija __Ejecutar__. Observe que no hay ningún resultado cuando finaliza el trabajo. Esto es debido a que al usar el botón __Ejecutar__ cuando se selecciona parte de la consulta, solo se ejecutarán las instrucciones seleccionadas. En este caso, la selección no incluyó la instrucción final que recupera filas de la tabla. Si selecciona solo esa línea y usa __Ejecutar__, debería ver los resultados esperados.

3. Use el botón __Nueva hoja de cálculo__ situado en la parte inferior del __Editor de consultas__ para crear una nueva hoja de cálculo. En la hoja de cálculo nueva, escriba las siguientes instrucciones de HiveQL:

		CREATE TABLE IF NOT EXISTS errorLogs (t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string) STORED AS ORC;
		INSERT OVERWRITE TABLE errorLogs SELECT t1, t2, t3, t4, t5, t6, t7 FROM log4jLogs WHERE t4 = '[ERROR]';

	Estas instrucciones realizan las acciones siguientes:

	- **CREATE TABLE IF NOT EXISTS**: crea una tabla, si todavía no existe. Dado que la palabra clave **EXTERNAL** no es usa, se trata de una tabla interna, la que se almacena en el almacenamiento de datos de Hive y es administrada completamente por Hive. A diferencia de las tablas externas, la eliminación de una tabla interna también eliminará los datos subyacentes.
	- **STORED AS ORC**: almacena los datos en el formato Optimized Row Columnar (ORC). Se trata de un formato altamente optimizado y eficiente para almacenar datos de Hive.
	- **INSERT OVERWRITE ... SELECT**: selecciona filas de la tabla **log4jLogs** que contienen [ERROR] y, a continuación, inserta los datos en la tabla **errorLogs**.
    
    Use el botón __Ejecutar__ para ejecutar esta consulta. La pestaña __Resultados__ no contendrá ninguna información ya que no se devuelven filas por esta consulta, pero el estado debe aparecer como __CORRECTO__.
    
4. A la derecha del Editor de consultas se muestra una fila de iconos. Seleccione el que se parece a una cadena.

    ![iconos](./media/hdinsight-hadoop-linux-tutorial-get-started/icons.png)
    
    Se trata de la vista __Explicación visual__ de la consulta, que puede ser útil para comprender el flujo de las consultas complejas. Puede ver un equivalente textual de esta vista con el botón __Explicar__ en el Editor de consultas.
    
    ![Imagen de Explicación visual](./media/hdinsight-hadoop-linux-tutorial-get-started/visualexplain.png)
    
    Los demás iconos son los siguientes:
    
	* Configuración: el icono de engranaje le permite cambiar la configuración de Hive, como la configuración `hive.execution.engine`o los parámetros de Tez.
	* Tez: muestra el grafo acíclico dirigido (DAG) que usó Tez para ejecutar la consulta. Si desea ver el DAG para las consultas que se ejecutaron en el pasado, use la __Vista de Tez__ en su lugar.
	* Notificaciones: muestra notificaciones, como “La consulta se ha enviado”, o si se produce un error al ejecutar una consulta.

5. Seleccione el icono __SQL__ para cambiar de nuevo al Editor de consultas, cree una nueva hoja de cálculo y escriba la siguiente consulta:

        SELECT * from errorLogs;
    
    Use el botón __Guardar como__ de la parte inferior del editor. Asigne a esta consulta el nombre de __Errorlogs__ y seleccione __Aceptar__. Tenga en cuenta que el nombre de la hoja de cálculo cambia a __Errorlogs__.
    
    Las consultas guardadas aparecen también en la pestaña __Consultas guardadas__ en la parte superior de la página. Seleccione esta opción y debería ver el nombre __Errorlogs__ mostrado. Si selecciona el nombre, se abrirá la consulta en el Editor de consultas.

4. Ejecute la consulta __Errorlogs__. El resultado será como sigue:

        errorlogs.t1 	errorlogs.t2 	errorlogs.t3 	errorlogs.t4 	errorlogs.t5 	errorlogs.t6 	errorlogs.t7
        2012-02-03 	18:35:34 	SampleClass0 	[ERROR] 	incorrect 	id 	
        2012-02-03 	18:55:54 	SampleClass1 	[ERROR] 	incorrect 	id 	
        2012-02-03 	19:25:27 	SampleClass4 	[ERROR] 	incorrect 	id

## <a name="nextsteps"></a>Pasos siguientes

En este documento, ha aprendido cómo crear un clúster de HDInsight basado en Linux mediante el Portal de Azure, cómo conectarse al clúster mediante SSH y cómo realizar consultas básicas de Hive.

Para obtener más información sobre el análisis de datos con HDInsight, consulte lo siguiente:

- Para más información sobre el uso de Hive con HDInsight, incluida la forma de realizar consultas de Hive desde Visual Studio, consulte [Usar Hive y HiveQL con Hadoop en HDInsight para analizar un archivo log4j de Apache de muestra][hdinsight-use-hive].

- Para más información sobre Pig, un lenguaje usado para transformar datos, consulte [Uso de Pig con Hadoop en HDInsight][hdinsight-use-pig].

- Para más información sobre MapReduce, una manera de escribir programas que procesan datos en Hadoop, consulte [Uso de MapReduce en Hadoop en HDInsight][hdinsight-use-mapreduce].

- Para más información sobre el uso de las herramientas de HDInsight para Visual Studio para analizar datos en HDInsight, consulte [Introducción al uso de herramientas de Hadoop en Visual Studio para HDInsight para ejecutar una consulta de Hive](hdinsight-hadoop-visual-studio-tools-get-started.md).

Si está listo para empezar a trabajar con sus propios datos y necesita saber más sobre cómo HDInsight almacena los datos o sobre cómo obtener datos en HDInsight, consulte lo siguiente:

- Para más información sobre cómo HDInsight usa el almacenamiento de blobs de Azure, consulte [Uso del almacenamiento de blobs de Azure compatibles con HDFS con Hadoop en HDInsight](hdinsight-use-blob-storage.md).

- Para más información sobre cómo cargar datos en HDInsight, consulte [Carga de datos para trabajos de Hadoop en HDInsight][hdinsight-upload-data].

Si desea información sobre cómo crear o administrar un clúster de HDInsight, consulte lo siguiente:

- Para más información sobre cómo administrar el clúster de HDInsight basado en Linux, consulte [Administración de clústeres de HDInsight con la interfaz de usuario web de Ambari](hdinsight-hadoop-manage-ambari.md).

- Para obtener más información sobre las opciones que puede seleccionar al crear un clúster de HDInsight, consulte [Aprovisionamiento de HDInsight en Linux usando opciones personalizadas](hdinsight-hadoop-provision-linux-clusters.md).

- Si está familiarizado con Linux y Hadoop, pero quiere conocer información específica sobre Hadoop en HDInsight, consulte [Información sobre el uso de HDInsight en Linux](hdinsight-hadoop-linux-information.md). Esto ofrece información como:

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

<!---HONumber=AcomDC_0114_2016-->