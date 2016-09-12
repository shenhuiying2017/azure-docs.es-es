<properties
   pageTitle="Creación de clústeres de HDInsight con el Almacén de Azure Data Lake mediante el Portal | Azure"
   description="Uso del Portal de Azure para crear y uso de clústeres de HDInsight con el Almacén de Azure Data Lake"
   services="data-lake-store,hdinsight" 
   documentationCenter=""
   authors="nitinme"
   manager="paulettm"
   editor="cgronlun"/>

<tags
   ms.service="data-lake-store"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="08/29/2016"
   ms.author="nitinme"/>

# Creación de un clúster de HDInsight con el Almacén de Data Lake mediante el Portal de Azure

> [AZURE.SELECTOR]
- [Uso del Portal](data-lake-store-hdinsight-hadoop-use-portal.md)
- [Uso de PowerShell](data-lake-store-hdinsight-hadoop-use-powershell.md)


Aprenda a usar el Portal de Azure para crear un clúster de HDInsight (Hadoop, HBase, Spark o Storm) con acceso al Almacén de Azure Data Lake. Algunas consideraciones importantes sobre esta versión:

* **En clústeres de Spark (Linux) y Hadoop (Windows y Linux)**, el Almacén de Data Lake solo puede usarse como cuenta de almacenamiento adicional. La cuenta de almacenamiento predeterminada para los clústeres de este tipo seguirán Blobs de almacenamiento de Azure (WASB).

* **En clústeres de Storm (Windows y Linux)**, el Almacén de Data Lake se puede usar para escribir datos de una topología de Storm. Almacén de Data Lake también puede utilizarse para almacenar datos de referencia que luego puede leer una topología de Storm. Para obtener más información, consulte [Usar el Almacén de Data Lake en una topología de Storm](#use-data-lake-store-in-a-storm-topology).

* **En clústeres HBase (Windows y Linux)**, Azure Data Lake Store puede usarse como almacenamiento predeterminado o almacenamiento adicional. Para obtener más información, consulte [Usar el Almacén de Data Lake con clústeres de HBase](#use-data-lake-store-with-hbase-clusters).

> [AZURE.NOTE] Algunos puntos importantes que tener en cuenta:
> 
> * La opción para crear clústeres de HDInsight con acceso al Almacén de Data Lake solo está disponible para HDInsight versión 3.2 y 3.4 (para clústeres de Hadoop, HBase y Storm en Windows y Linux). Para los clústeres Spark en Linux, esta opción solo está disponible en los clústeres de HDInsight 3.4.
>
> * Tal y como se mencionó anteriormente, el Almacén de Data Lake está disponible como almacenamiento predeterminado para algunos tipos de clúster (HBase) y como almacenamiento adicional para otros tipos de clúster (Hadoop, Spark y Storm). Utilizar el Almacén de Data Lake como una cuenta de almacenamiento adicional no afecta al rendimiento o la capacidad de lectura y escritura en el almacenamiento del clúster. En un escenario donde se utiliza el Almacén de Data Lake como almacenamiento adicional, los archivos relacionados con el clúster (por ejemplo, registros, etc.) se crean en el almacenamiento predeterminado (Blobs de Azure), mientras que los datos que quiere procesar pueden almacenarse en una cuenta de Almacén de Data Lake.


## Requisitos previos

Antes de empezar este tutorial, debe contar con lo siguiente:

- **Una suscripción de Azure**. Vea [Obtener evaluación gratuita de Azure](https://azure.microsoft.com/pricing/free-trial/).
- **Habilite su suscripción de Azure** para la versión preliminar pública de Azure Data Lake Store. Consulte las [instrucciones](data-lake-store-get-started-portal.md#signup).
- **Cuenta del Almacén de Azure Data Lake**. Siga las instrucciones que se describen en [Introducción al Almacén de Azure Data Lake mediante el Portal de Azure](data-lake-store-get-started-portal.md). Una vez que haya creado la cuenta, realice las siguientes tareas para cargar datos de ejemplo. Necesitará estos datos más adelante en el tutorial para ejecutar trabajos desde un clúster de HDInsight que accedan a datos en el Almacén de Data Lake.

	* [Cree una carpeta en el Almacén de Data Lake](data-lake-store-get-started-portal.md#createfolder).
	* [Cargue un archivo en su Almacén de Data Lake](data-lake-store-get-started-portal.md#uploaddata). Si busca datos de ejemplo para cargar, puede obtener la carpeta **Ambulance Data** en el [repositorio Git de Azure Data Lake](https://github.com/Azure/usql/tree/master/Examples/Samples/Data/AmbulanceData).

## ¿Aprende más rápido con vídeos?

Vea los vídeos siguientes para entender cómo aprovisionar clústeres de HDInsight con acceso al Almacén de Data Lake.

* [Creación de un clúster de HDInsight con el Almacén de Data Lake mediante el Portal de Azure](https://mix.office.com/watch/l93xri2yhtp2)
* Una vez configurado el clúster, [acceda a los datos del Almacén de Data Lake mediante scripts de Hive y Pig](https://mix.office.com/watch/1n9g5w0fiqv1q) (vídeo en inglés).

## Creación de un clúster de HDInsight con acceso al Almacén de Azure Data Lake

En esta sección, se crea un clúster de Hadoop en HDInsight que usa el Almacén de Data Lake como almacenamiento adicional. En esta versión, para un clúster de Hadoop, el Almacén de Data Lake solo sirve como almacenamiento adicional para el clúster. El almacenamiento predeterminado seguirá siendo los blobs de almacenamiento de Azure (WASB). En primer lugar, crearemos la cuenta de almacenamiento y los contenedores de almacenamiento necesarios para el clúster.

1. Inicie sesión en el nuevo [Portal de Azure](https://portal.azure.com).

2. Siga los pasos descritos en [Creación de clústeres de Hadoop en HDInsight](../hdinsight/hdinsight-provision-clusters.md#create-using-the-preview-portal) para iniciar el aprovisionamiento de un clúster de HDInsight.

3. En la hoja **Configuración opcional**, haga clic en **Origen de datos**. En la hoja **Origen de datos**, especifique los detalles de la cuenta de almacenamiento y el contenedor de almacenamiento, especifique **Ubicación** como **Este de EE. UU. 2** y después haga clic en **Identidad de AAD de clúster**.

	![Agregar entidad de servicio al clúster de HDInsight](./media/data-lake-store-hdinsight-hadoop-use-portal/hdi.adl.1.png "Agregar entidad de servicio al clúster de HDInsight")

4. En la hoja **Identidad de AAD de clúster**, puede seleccionar una entidad de servicio existente o crear una nueva.

	* **Cree una nueva entidad de servicio**

		* En la hoja **Identidad de AAD del clúster**, haga clic en **Crear nuevo** y en **Entidad de servicio** y, después, en la hoja **Crear entidad de servicio**, proporcione valores para crear una nueva entidad de servicio. En este paso, también se crean un certificado y una aplicación de Azure Active Directory. Haga clic en **Crear**.

			![Agregar entidad de servicio al clúster de HDInsight](./media/data-lake-store-hdinsight-hadoop-use-portal/hdi.adl.2.png "Agregar entidad de servicio al clúster de HDInsight")

		* En la hoja **Identidad de AAD de clúster**, haga clic en **Administrar acceso a ADLS**. En el panel, aparecen las cuentas de Data Lake Store asociadas a la suscripción. Sin embargo, puede establecer los permisos solo para la cuenta que creó. Seleccione los permisos de lectura, escritura y ejecución para la cuenta que desea asociar con el clúster de HDInsight y, después, haga clic en **Guardar permisos**.

			![Agregar entidad de servicio al clúster de HDInsight](./media/data-lake-store-hdinsight-hadoop-use-portal/hdi.adl.3.png "Agregar entidad de servicio al clúster de HDInsight")

		* En la hoja **Identidad de AAD de clúster**, haga clic en **Descargar certificado** para descargar el certificado asociado a la entidad de servicio creada. Esto es útil si desea usar la misma entidad de servicio en el futuro, cuando cree más clústeres de HDInsight. Haga clic en **Seleccionar**.

			![Agregar entidad de servicio al clúster de HDInsight](./media/data-lake-store-hdinsight-hadoop-use-portal/hdi.adl.4.png "Agregar entidad de servicio al clúster de HDInsight")


	* **Elija una entidad de servicio existente**.

		* En la hoja **Identidad de AAD de clúster**, haga clic en **Usar existente** y en **Entidad de servicio** y, después, en la hoja **Seleccionar entidad de servicio**, busque una entidad de servicio existente. Haga clic en un nombre de entidad de servicio y después en **Seleccionar**.

			![Agregar entidad de servicio al clúster de HDInsight](./media/data-lake-store-hdinsight-hadoop-use-portal/hdi.adl.5.png "Agregar entidad de servicio al clúster de HDInsight")

		* En la hoja **Identidad de AAD de clúster**, cargue el certificado (.pfx) asociado a la entidad de servicio que seleccionó y luego proporcione la contraseña del certificado.

5. Haga clic en **Administrar acceso a ADLS** y, a después, haga clic en **Seleccionar permisos de archivo**.

	![Agregar entidad de servicio al clúster de HDInsight](./media/data-lake-store-hdinsight-hadoop-use-portal/hdi.adl.5.existing.save.png "Agregar entidad de servicio al clúster de HDInsight")

6. En la hoja **Seleccionar permisos de archivo**, elija la cuenta de Data Lake Store que quiera asociar con el clúster de HDInsight desde el menú desplegable **Cuenta**. La hoja enumera los archivos y las carpetas disponibles en la cuenta de Data Lake Store seleccionada.
 
	![Proporcionar acceso a Data Lake Store](./media/data-lake-store-hdinsight-hadoop-use-portal/hdi-adl-permission-1.png "Proporcionar acceso a Data Lake Store")

	Después, determine los permisos que se proporcionarán para los archivos y las carpetas seleccionados. Para las carpetas, especifique si los permisos se aplican solo a la carpeta o a la carpeta y todos los elementos secundarios de ella. Puede realizar esta selección eligiendo el valor adecuado del menú desplegable **Aplicar a**. Para quitar un permiso, haga clic en el icono de **Eliminar**.

	![Proporcionar acceso a Data Lake Store](./media/data-lake-store-hdinsight-hadoop-use-portal/hdi-adl-permission-2.png "Proporcionar acceso a Data Lake Store")

	Repita estos pasos en las carpetas y los archivos asociados desde otras cuentas de Data Lake Store. Cuando haya completado la asignación de los permisos, haga clic en **Seleccionar** en la parte inferior de la hoja.

7. En la hoja **Asignar permisos seleccionados**, revise los permisos proporcionados y, después, haga clic en **Ejecutar** para conceder dichos permisos.

	![Proporcionar acceso a Data Lake Store](./media/data-lake-store-hdinsight-hadoop-use-portal/hdi-adl-permission-3.png "Proporcionar acceso a Data Lake Store")

	La columna de estado muestra el progreso. Una vez que se hayan asignado correctamente todos los permisos, haga clic en **Listo**.

6. Haga clic en **Seleccionar** en las hojas **Identidad de AAD del clúster** y [Origen de datos](../hdinsight/hdinsight-hadoop-create-linux-clusters-portal.md) y, luego, continúe con la creación de los clústeres tal y como se describe en **Creación de clústeres de Hadoop en HDInsight**.

7. Una vez aprovisionado el clúster, puede comprobar que la entidad de servicio esté asociada con el clúster de HDInsight. Para hacerlo, en la hoja del clúster, haga clic en **Identidad de AAD de clúster** para ver la entidad de servicio asociada.

	![Agregar entidad de servicio al clúster de HDInsight](./media/data-lake-store-hdinsight-hadoop-use-portal/hdi.adl.6.png "Agregar entidad de servicio al clúster de HDInsight")

## Ejecución de trabajos de prueba en el clúster de HDInsight para usar el Almacén de Azure Data Lake

Después de configurar un clúster de HDInsight, puede ejecutar trabajos de prueba en el clúster para probar que el clúster de HDInsight puede acceder a datos en el Almacén de Azure Data Lake. Para hacerlo, vamos a ejecutar algunas consultas de Hive que tienen como destino el Almacén de Data Lake.

### En un clúster de Linux

1. Abra la hoja del clúster que acaba de aprovisionar y después haga clic en **Panel**. Se abre Ambari para el clúster de Linux. Al acceder a Ambari, se le pedirá autenticarse en el sitio. Escriba el nombre de cuenta y la contraseña del administrador (el administrador predeterminado) que usó al crear el clúster.

	![Inicie el panel del clúster](./media/data-lake-store-hdinsight-hadoop-use-portal/hdiadlcluster1.png "Inicie el panel del clúster")

	También puede ir directamente a Ambari accediendo a https://CLUSTERNAME.azurehdinsight.net en un explorador web (donde **CLUSTERNAME** es el nombre del clúster de HDInsight).

2. Abra la vista de Hive. Seleccione el grupo de cuadrados en el menú de la página (junto al vínculo **Administrador** y el botón de la derecha de la página) para mostrar las vistas disponibles. Seleccione la **Vista de Hive**.

	![Selección de vistas de Ambari](./media/data-lake-store-hdinsight-hadoop-use-portal/selecthiveview.png)

3. Debería ver una página similar a la siguiente:

	![Imagen de la página de vista de Hive, que contiene una sección del Editor de consultas](./media/data-lake-store-hdinsight-hadoop-use-portal/hiveview.png)

4. En la sección **Editor de consultas** de la página, pegue la siguiente instrucción de HiveQL en la hoja de cálculo:

		CREATE EXTERNAL TABLE vehicles (str string) LOCATION 'adl://mydatalakestore.azuredatalakestore.net:443/mynewfolder'

5. Haga clic en el botón **Ejecutar** de la parte inferior del **Editor de consultas** para iniciar la consulta. Debe aparecer la sección **Resultados del proceso de consulta** bajo el **Editor de consultas** y mostrar información sobre el trabajo.

6. Cuando haya finalizado la consulta, la sección **Resultados del proceso de consulta** mostrará los resultados de la operación. La pestaña **Resultados** debe contener la información siguiente:

7. Ejecute la siguiente consulta para comprobar que se creó la tabla.

		SHOW TABLES;

	La pestaña **Resultados** debe mostrar lo siguiente:

		hivesampletable
		vehicles

	**vehicles** es la tabla que creó antes. La tabla de ejemplo **hivesampletable** está disponible en todos los clústeres de HDInsight de forma predeterminada.

8. También puede ejecutar una consulta para recuperar datos de la tabla **vehicles**.

		SELECT * FROM vehicles LIMIT 5;

### En un clúster de Windows

1. Abra la hoja del clúster que acaba de aprovisionar y después haga clic en **Panel**.

	![Inicie el panel del clúster](./media/data-lake-store-hdinsight-hadoop-use-portal/hdiadlcluster1.png "Inicie el panel del clúster")

	Cuando se le solicite, escriba las credenciales de administrador para el clúster.

2. Se abre la consola de consulta de HDInsight de Microsoft Azure. Haga clic en **Editor de Hive**.

	![Abrir el Editor de Hive](./media/data-lake-store-hdinsight-hadoop-use-portal/hdiadlcluster2.png "Abrir el Editor de Hive")

3. En el Editor de Hive, escriba la siguiente consulta y después haga clic en **Enviar**.

		CREATE EXTERNAL TABLE vehicles (str string) LOCATION 'adl://mydatalakestore.azuredatalakestore.net:443/mynewfolder'

	En esta consulta de Hive, creamos una tabla con los datos almacenados en el almacén de Data Lake en `adl://mydatalakestore.azuredatalakestore.net:443/mynewfolder`. Esta ubicación contiene un archivo de datos de ejemplo que debería haber cargado antes.

	La tabla **Sesión de trabajo** en la parte inferior muestra el estado del trabajo, que cambia de **Inicializando** a **Ejecutando** y después a **Completado**. También puede hacer clic en **Ver detalles** para obtener más información sobre el trabajo completado.

	![Crear tabla](./media/data-lake-store-hdinsight-hadoop-use-portal/hdiadlcluster3.png "Crear tabla")

4. Ejecute la siguiente consulta para comprobar que se creó la tabla.

		SHOW TABLES;

	Haga clic en la opción **Ver detalles** de esta consulta, la salida debe mostrar lo siguiente:

		hivesampletable
		vehicles

	**vehicles** es la tabla que creó antes. La tabla de ejemplo **hivesampletable** está disponible en todos los clústeres de HDInsight de forma predeterminada.

5. También puede ejecutar una consulta para recuperar datos de la tabla **vehicles**.

		SELECT * FROM vehicles LIMIT 5;


## Acceso al Almacén de Data Lake mediante comandos de HDFS

Una vez que configure el clúster de HDInsight para que use el Almacén de Data Lake, puede usar los comandos de shell de HDFS para acceder al almacén.

### En un clúster de Linux

En esta sección, se usará SSH en el clúster y se ejecutarán los comandos de HDFS. Windows no proporciona ningún cliente SSH integrado. Se recomienda usar **PuTTY**, que se puede descargar en [http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html).

Para obtener más información sobre el uso de PuTTY, consulte [Utilización de SSH con Hadoop en HDInsight basado en Linux desde Linux, Unix u OS X (versión preliminar)](../hdinsight/hdinsight-hadoop-linux-use-ssh-windows.md).

Una vez conectado, utilice el siguiente comando del sistema de archivos HDFS para enumerar los archivos del Almacén de Data Lake.

	hdfs dfs -ls adl://<Data Lake Store account name>.azuredatalakestore.net:443/

Se debería incluir el archivo que cargó antes en el Almacén de Data Lake.

	15/09/17 21:41:15 INFO web.CaboWebHdfsFileSystem: Replacing original urlConnectionFactory with org.apache.hadoop.hdfs.web.URLConnectionFactory@21a728d6
	Found 1 items
	-rwxrwxrwx   0 NotSupportYet NotSupportYet     671388 2015-09-16 22:16 adl://mydatalakestore.azuredatalakestore.net:443/mynewfolder

También puede usar el comando `hdfs dfs -put` para cargar algunos archivos en el almacén de Data Lake y después usar `hdfs dfs -ls` para comprobar si los archivos se cargaron correctamente.


### En un clúster de Windows

1. Inicie sesión en el nuevo [Portal de Azure](https://portal.azure.com).

2. Haga clic en **Examinar**, en **Clústeres de HDInsight** y después en el clúster de HDInsight que creó.

3. En la hoja del clúster, haga clic en **Escritorio remoto** y después, en la hoja **Escritorio remoto**, haga clic en **Conectar**.

	![Conexión remota con un clúster de HDI](./media/data-lake-store-hdinsight-hadoop-use-portal/ADL.HDI.PS.Remote.Desktop.png "Crear un grupo de recursos de Azure")

	Cuando se le solicite, escriba las credenciales que proporcionó para el usuario de Escritorio remoto.

4. En la sesión remota, inicie Windows PowerShell y use los comandos del sistema de archivos de HDFS para enumerar los archivos en el Almacén de Azure Data Lake.

	 	hdfs dfs -ls adl://<Data Lake Store account name>.azuredatalakestore.net:443/

	Se debería incluir el archivo que cargó antes en el Almacén de Data Lake.

		15/09/17 21:41:15 INFO web.CaboWebHdfsFileSystem: Replacing original urlConnectionFactory with org.apache.hadoop.hdfs.web.URLConnectionFactory@21a728d6
		Found 1 items
		-rwxrwxrwx   0 NotSupportYet NotSupportYet     671388 2015-09-16 22:16 adl://mydatalakestore.azuredatalakestore.net:443/mynewfolder

	También puede usar el comando `hdfs dfs -put` para cargar algunos archivos en el almacén de Data Lake y después usar `hdfs dfs -ls` para comprobar si los archivos se cargaron correctamente.

## Uso del Almacén de Data Lake con un clúster de Spark

En esta sección, se usa el cuaderno de Jupyter Notebook disponible con los clústeres de HDInsight Spark para ejecutar un trabajo que lee datos de una cuenta del Almacén de Data Lake que asoció con un clúster de HDInsight Spark, en lugar de la cuenta de blob de Almacenamiento de Azure predeterminada.

1. Copie datos de ejemplo de la cuenta de almacenamiento predeterminada (WASB) asociada con el clúster de Spark a la cuenta del Almacén de Azure Data Lake asociada con el clúster. Puede usar la [herramienta ADLCopy](http://aka.ms/downloadadlcopy) para hacerlo. Descargue e instale la herramienta desde el vínculo.

2. Abra un símbolo del sistema y vaya al directorio donde está instalada la herramienta AdlCopy, normalmente `%HOMEPATH%\Documents\adlcopy`.

3. Ejecute el siguiente comando para copiar un blob específico desde el contenedor de origen a un Almacén de Data Lake:

		AdlCopy /source https://<source_account>.blob.core.windows.net/<source_container>/<blob name> /dest swebhdfs://<dest_adls_account>.azuredatalakestore.net/<dest_folder>/ /sourcekey <storage_account_key_for_storage_container>

	Para este tutorial, copie el archivo de datos de ejemplo **HVAC.csv** en **/HdiSamples/HdiSamples/SensorSampleData/hvac/** a la cuenta de Azure Data Lake Store. El fragmento de código debería tener este aspecto:

		AdlCopy /Source https://mydatastore.blob.core.windows.net/mysparkcluster/HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv /dest swebhdfs://mydatalakestore.azuredatalakestore.net/hvac/ /sourcekey uJUfvD6cEvhfLoBae2yyQf8t9/BpbWZ4XoYj4kAS5Jf40pZaMNf0q6a8yqTxktwVgRED4vPHeh/50iS9atS5LQ==

	>[AZURE.WARNING] Asegúrese de que coincida el uso de mayúsculas y minúsculas en los nombres de archivo y ruta de acceso.

4. Se le pedirá que escriba las credenciales de la suscripción a Azure en la que tiene la cuenta de Azure Data Lake Store. Verá un resultado similar al siguiente:

		Initializing Copy.
		Copy Started.
		100% data copied.
		Copy Completed. 1 file copied.

	El archivo de datos (**HVAC.csv**) se copiará en una carpeta **/hvac** en la cuenta de Azure Data Lake Store.

4. Desde el [Portal de Azure](https://portal.azure.com/), en el panel de inicio, haga clic en el icono del clúster Spark (si lo ancló al panel de inicio). También puede navegar hasta el clúster en **Examinar todo** > **Clústeres de HDInsight**.

2. En la hoja del clúster Spark, haga clic en **Vínculos rápidos** y, luego, en la hoja **Panel de clúster**, haga clic en **Jupyter Notebook**. Cuando se le pida, escriba las credenciales del clúster.

	> [AZURE.NOTE] También puede comunicarse con el equipo Jupyter Notebook en el clúster si abre la siguiente dirección URL en el explorador. Reemplace __CLUSTERNAME__ por el nombre del clúster:
	>
	> `https://CLUSTERNAME.azurehdinsight.net/jupyter`

2. Cree un nuevo notebook. Haga clic en **Nuevo** y, luego, en **PySpark**.

	![Crear un nuevo cuaderno de Jupyter](./media/data-lake-store-hdinsight-hadoop-use-portal/hdispark.note.jupyter.createnotebook.png "Crear un nuevo cuaderno de Jupyter")

3. Se crea y se abre un nuevo cuaderno con el nombre **Untitled.pynb**.

4. Dado que creó un cuaderno con el kernel PySpark, no necesitará crear ningún contexto explícitamente. Los contextos Spark y Hive se crearán automáticamente al ejecutar la primera celda de código. Puede empezar por importar los tipos necesarios para este escenario. Para ello, pegue el siguiente fragmento de código en una celda y presione **MAYÚS + ENTRAR**.

		from pyspark.sql.types import *
		
	Cada vez que se ejecuta un trabajo en Jupyter, el título de la ventana del explorador web mostrará el estado **(Busy)** (Ocupado) junto con el título del cuaderno. También verá un círculo sólido junto al texto **PySpark** en la esquina superior derecha. Una vez completado el trabajo, cambiará a un círculo hueco.

	 ![Estado de un trabajo de cuaderno de Jupyter](./media/data-lake-store-hdinsight-hadoop-use-portal/hdispark.jupyter.job.status.png "Estado de un trabajo de cuaderno de Jupyter")

4. Cargue datos de ejemplo en una tabla temporal mediante el archivo **HVAC.csv** que copió a la cuenta de Azure Data Lake Store. Ahora puede acceder a los datos de la cuenta del Almacén de Data Lake con el siguiente patrón de dirección URL.

		adl://<data_lake_store_name>.azuredatalakestore.net/<path_to_file>

	En una celda vacía, pegue el siguiente ejemplo de código, reemplace **MYDATALAKESTORE** por el nombre de su cuenta de Azure Data Lake Store y presione **MAYÚS+ENTRAR**. Este ejemplo de código registra los datos en una tabla temporal llamada **hvac**.

		# Load the data
		hvacText = sc.textFile("adl://MYDATALAKESTORE.azuredatalakestore.net/hvac/HVAC.csv")
		
		# Create the schema
		hvacSchema = StructType([StructField("date", StringType(), False),StructField("time", StringType(), False),StructField("targettemp", IntegerType(), False),StructField("actualtemp", IntegerType(), False),StructField("buildingID", StringType(), False)])
		
		# Parse the data in hvacText
		hvac = hvacText.map(lambda s: s.split(",")).filter(lambda s: s[0] != "Date").map(lambda s:(str(s[0]), str(s[1]), int(s[2]), int(s[3]), str(s[6]) ))
		
		# Create a data frame
		hvacdf = sqlContext.createDataFrame(hvac,hvacSchema)
		
		# Register the data fram as a table to run queries against
		hvacdf.registerTempTable("hvac")

5. Al usar un kernel de PySpark, puede ejecutar directamente una consulta SQL en la tabla temporal **hvac** que acaba de crear con la instrucción mágica `%%sql`. Para más información sobre la instrucción mágica `%%sql`, así como otras que hay disponible con el kernel de PySpark, consulte [Kernels disponibles para cuadernos de Jupyter con clústeres Spark en HDInsight basados en Linux en HDInsight (versión preliminar)](hdinsight-apache-spark-jupyter-notebook-kernels.md#why-should-i-use-the-new-kernels).
		
		%%sql
		SELECT buildingID, (targettemp - actualtemp) AS temp_diff, date FROM hvac WHERE date = "6/1/13"

5. Una vez que el trabajo se completa correctamente, se muestra de forma predeterminada el resultado tabular siguiente.

 	![Salida de tabla del resultado de la consulta](./media/data-lake-store-hdinsight-hadoop-use-portal/tabular.output.png "Salida de tabla del resultado de la consulta")

	También puede ver la salida en otras visualizaciones. Por ejemplo, un gráfico de área con la misma salida tendría el siguiente aspecto.

	![Gráfico de área del resultado de la consulta](./media/data-lake-store-hdinsight-hadoop-use-portal/area.output.png "Gráfico de área del resultado de la consulta")


6. Cuando haya terminado de ejecutar la aplicación, debe apagar el cuaderno para liberar los recursos. Para ello, en el menú **Archivo** del cuaderno, haga clic en **Cerrar y detener**. De esta manera se apagará y se cerrará el cuaderno.

## Uso del Almacén de Data Lake en una topología de Storm

El Almacén de Data Lake se puede usar para escribir datos de una topología de Storm. Para obtener instrucciones sobre cómo posibilitar este escenario, consulte [Uso del Almacén de Azure Data Lake con Apache Storm con HDInsight](../hdinsight/hdinsight-storm-write-data-lake-store.md).

## Uso del Almacén de Data Lake con clústeres de HBase

Con los clústeres de HBase, puede usar el Almacén de Data Lake como almacenamiento predeterminado así como almacenamiento adicional. Para ello:

1.  En la hoja **Origen de datos**, para **Ubicación de datos de HBase**, seleccione **Data Lake Store**.
2.  Seleccione el nombre del Almacén de Data Lake que desea usar o cree uno nuevo.
3.  Por último, especifique el valor de **Carpeta raíz de HBase** dentro de Azure Data Lake Store. Si la cuenta del Almacén de Data Lake no tiene una carpeta raíz, cree una nueva.

	![HBase con Almacén de Data Lake](./media/data-lake-store-hdinsight-hadoop-use-portal/hbase-data-lake-store.png "Crear un grupo de recursos de Azure")

### Consideraciones al usar el Almacén de Data Lake como almacenamiento predeterminado para clústeres de HBase

* Puede usar la misma cuenta de Almacén de Data Lake para más de un clúster de HBase. Sin embargo, el valor de **Carpeta raíz de HBase** que proporcione para el clúster (paso 4 en la captura de pantalla anterior) debe ser único. **No debe** usar la misma carpeta raíz en dos clústeres de HBase diferentes.
* Aunque use la cuenta de Almacén de Data Lake como almacenamiento predeterminado, los archivos de registro del clúster de HBase se siguen almacenando en los blobs de almacenamiento de Azure (WASB) asociados al clúster. Esto se resalta en el cuadro azul de la captura de pantalla anterior.



## Otras referencias

* [Aprovisionamiento de un clúster de HDInsight con el Almacén de Data Lake mediante Azure PowerShell](data-lake-store-hdinsight-hadoop-use-powershell.md)

[makecert]: https://msdn.microsoft.com/library/windows/desktop/ff548309(v=vs.85).aspx
[pvk2pfx]: https://msdn.microsoft.com/library/windows/desktop/ff550672(v=vs.85).aspx

<!---HONumber=AcomDC_0831_2016-->