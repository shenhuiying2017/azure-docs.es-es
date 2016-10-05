<properties
	pageTitle="Tutorial de Data Factory: primera canalización de datos | Microsoft Azure"
	description="En este tutorial de Data Factory de Azure se muestra cómo crear y programar una factoría de datos que procese los datos mediante el script de Hive en un clúster de Hadoop."
	services="data-factory"
	keywords="Tutorial de Data Factory de Azure, clúster de Hadoop, Hive de Hadoop"
	documentationCenter=""
	authors="spelluru"
	manager=""
	editor=""/>

<tags
	ms.service="data-factory"
	ms.workload="data-services"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article" 
	ms.date="09/06/2016"
	ms.author="spelluru"/>

# Tutorial: Compilación de la primera canalización para procesar datos mediante el clúster de Hadoop 
> [AZURE.SELECTOR]
- [Introducción y requisitos previos](data-factory-build-your-first-pipeline.md)
- [Portal de Azure](data-factory-build-your-first-pipeline-using-editor.md)
- [Visual Studio](data-factory-build-your-first-pipeline-using-vs.md)
- [PowerShell](data-factory-build-your-first-pipeline-using-powershell.md)
- [Plantilla de Resource Manager](data-factory-build-your-first-pipeline-using-arm.md)
- [API DE REST](data-factory-build-your-first-pipeline-using-rest-api.md)

En este tutorial, va a crear su primera factoría de datos de Azure con una canalización de datos que procesa los datos ejecutando el script de Hive en un clúster de HDInsight de Azure (Hadoop).

> [AZURE.NOTE] Este artículo no ofrece información general sobre conceptos del servicio Azure Data Factory. Si desea ver este tipo de información, consulte [Introducción al servicio Factoría de datos de Azure](data-factory-introduction.md). Consulte [Ruta de aprendizaje para Data Factory de Azure](https://azure.microsoft.com/documentation/learning-paths/data-factory/) para ver un método recomendado de navegar por el contenido y obtener información acerca de Data Factory.

## ¿Qué se va a tratar en este tutorial?	
**Data Factory de Azure** permite crear tareas de **movimiento** y **procesamiento** de datos como un flujo de trabajo controlado por datos (también se denominan "canalizaciones de datos"). Aprenda a crear su primera tarea de canalización con una actividad de procesamiento de datos (o transformación de datos). Esta actividad utiliza un clúster de Hadoop de HDInsight para transformar y analizar registros web de ejemplo.

En este tutorial, realizará los siguientes pasos:

1.	Crear una **factoría de datos**. Una factoría de datos puede contener una o más canalizaciones de datos que pueden mover y procesar datos.
2.	Cree **servicios vinculados**. Un servicio vinculado se crea para vincular un almacén de datos o servicio de proceso a la factoría de datos. Un almacén de datos como Almacenamiento de Azure contiene los datos de entrada y salida de las actividades de la canalización. Un servicio de proceso como un clúster de Hadoop de HDInsight procesa y transforma los datos.
3.	Crear **conjuntos de datos** de entrada y salida. Un conjunto de datos de entrada representa la entrada para una actividad de la canalización y un conjunto de datos de salida representa la salida de la actividad.
3.	Crear la **canalización**. Una canalización puede tener una o varias actividades (ejemplos: actividad de copia, actividad de Hive en HDInsight). Este ejemplo usa la actividad de Hive de HDInsight que ejecuta un script de Hive en un clúster de Hadoop de HDInsight. El script crea primero una tabla que hace referencia a los datos de blog sin procesar almacenados en Azure Blob Storage y, después, divide los datos sin procesar por año y mes.

	Hay dos tipos de actividades que admite Azure Data Factory. Son: las [actividades de movimiento de datos](data-factory-data-movement-activities.md) y las [actividades de transformación de datos](data-factory-data-transformation-activities.md). Solo hay una actividad de movimiento de datos, que es la actividad de copia. En este tutorial, no usa la actividad de copia. Para obtener un tutorial sobre el uso de la actividad de copia, consulte [Tutorial: Copia de datos de un blob de Azure a SQL Azure](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md). La actividad de Hive de HDInsight que se usa en este tutorial es una de las actividades de transformación de datos admitidas por Data Factory.
 
Este es la **vista de diagrama** de la factoría de datos de ejemplo creada en este tutorial.

![Vista de diagrama en el tutorial de Data Factory](./media/data-factory-build-your-first-pipeline/data-factory-tutorial-diagram-view.png)

En este tutorial, la carpeta **inputdata** del contenedor de blobs de Azure **adfgetstarted** contiene un archivo llamado input.log. Este archivo de registro tiene entradas de tres meses: enero, febrero y marzo de 2016. Aquí están las filas de ejemplo para cada mes en el archivo de entrada.

	2016-01-01,02:01:09,SAMPLEWEBSITE,GET,/blogposts/mvc4/step2.png,X-ARR-LOG-ID=2ec4b8ad-3cf0-4442-93ab-837317ece6a1,80,-,1.54.23.196,Mozilla/5.0+(Windows+NT+6.3;+WOW64)+AppleWebKit/537.36+(KHTML,+like+Gecko)+Chrome/31.0.1650.63+Safari/537.36,-,http://weblogs.asp.net/sample/archive/2007/12/09/asp-net-mvc-framework-part-4-handling-form-edit-and-post-scenarios.aspx,\N,200,0,0,53175,871 
	2016-02-01,02:01:10,SAMPLEWEBSITE,GET,/blogposts/mvc4/step7.png,X-ARR-LOG-ID=d7472a26-431a-4a4d-99eb-c7b4fda2cf4c,80,-,1.54.23.196,Mozilla/5.0+(Windows+NT+6.3;+WOW64)+AppleWebKit/537.36+(KHTML,+like+Gecko)+Chrome/31.0.1650.63+Safari/537.36,-,http://weblogs.asp.net/sample/archive/2007/12/09/asp-net-mvc-framework-part-4-handling-form-edit-and-post-scenarios.aspx,\N,200,0,0,30184,871
	2016-03-01,02:01:10,SAMPLEWEBSITE,GET,/blogposts/mvc4/step7.png,X-ARR-LOG-ID=d7472a26-431a-4a4d-99eb-c7b4fda2cf4c,80,-,1.54.23.196,Mozilla/5.0+(Windows+NT+6.3;+WOW64)+AppleWebKit/537.36+(KHTML,+like+Gecko)+Chrome/31.0.1650.63+Safari/537.36,-,http://weblogs.asp.net/sample/archive/2007/12/09/asp-net-mvc-framework-part-4-handling-form-edit-and-post-scenarios.aspx,\N,200,0,0,30184,871

Cuando la canalización procesa el archivo mediante la actividad de Hive de HDInsight, esta actividad ejecuta un script de Hive en el clúster de HDInsight que divide los datos de entrada por año y mes. El script crea tres carpetas de salida que contienen un archivo con entradas de cada mes.

	adfgetstarted/partitioneddata/year=2016/month=1/000000_0
	adfgetstarted/partitioneddata/year=2016/month=2/000000_0
	adfgetstarted/partitioneddata/year=2016/month=3/000000_0

De las líneas de ejemplo mostradas anteriormente, la primera de ellas (con 2016-01-01) se escribirá en el archivo 000000\_0 en la carpeta month=1. De igual manera, la segunda se escribirá en el archivo de la carpeta month=2 y la tercera en el archivo de la carpeta month=3.


## Requisitos previos
Antes de comenzar este tutorial, debe cumplir los siguientes requisitos previos:

1.	**Suscripción de Azure**: si no tiene ninguna, puede crear una cuenta de evaluación gratuita en un par de minutos. Consulte el artículo [Evaluación gratuita](https://azure.microsoft.com/pricing/free-trial/) sobre cómo puede obtener una cuenta de evaluación gratuita.

2.	**Almacenamiento de Azure**: para almacenar los datos de este tutorial usará una cuenta de Almacenamiento de Azure. Si no dispone de una cuenta de Almacenamiento de Azure, consulte el artículo [Creación de una cuenta de almacenamiento](../storage/storage-create-storage-account.md#create-a-storage-account). Después de haber creado la cuenta de almacenamiento, anote el **nombre de la cuenta** y la **clave de acceso**. Consulte [Visualización y copia de las claves de acceso de almacenamiento](../storage/storage-create-storage-account.md#view-and-copy-storage-access-keys).

### Carga de archivos en Almacenamiento de Azure para el tutorial
Antes de comenzar el tutorial, tendrá que preparar la cuenta de Azure Storage con los archivos de ejemplo para el tutorial.

1. Cargue el archivo de consulta de Hive (HQL) en la carpeta **script** del contenedor de blobs **adfgetstarted**.
2. Cargue el archivo de entrada en la carpeta **inputdata** del contenedor de blobs **adfgetstarted**.

#### Creación del archivo de script HQL 

1. Inicie el **Bloc de notas** y pegue el siguiente script HQL. Estos scripts de Hive crean dos tablas: **WebLogsRaw** y **WebLogsPartitioned**. Haga clic en **Archivo** en el menú y seleccione **Guardar como**. Cambie a la carpeta **C:\\adfgetstarted** en el disco duro. Seleccione **Todos los archivos (*.*)** en el campo **Guardar como tipo**. Escriba **partitionweblogs.hql** en **Nombre de archivo**. Confirme que el campo **Codificación** en la parte inferior del cuadro de diálogo está establecido en **ANSI**. Si no es así, establézcalo en **ANSI**.

		set hive.exec.dynamic.partition.mode=nonstrict;
		
		DROP TABLE IF EXISTS WebLogsRaw; 
		CREATE TABLE WebLogsRaw (
		  date  date,
		  time  string,
		  ssitename string,
		  csmethod  string,
		  csuristem  string,
		  csuriquery string,
		  sport int,
		  susername string,
		  cipcsUserAgent string,
		  csCookie string,
		  csReferer string,
		  cshost  string,
		  scstatus  int,
		  scsubstatus  int,
		  scwin32status  int,
		  scbytes int,
		  csbytes int,
		  timetaken int
		)
		ROW FORMAT DELIMITED FIELDS TERMINATED BY ' '
		LINES TERMINATED BY '\n' 
		tblproperties ("skip.header.line.count"="2");
		
		LOAD DATA INPATH '${hiveconf:inputtable}' OVERWRITE INTO TABLE WebLogsRaw;
		
		DROP TABLE IF EXISTS WebLogsPartitioned ; 
		create external table WebLogsPartitioned (  
		  date  date,
		  time  string,
		  ssitename string,
		  csmethod  string,
		  csuristem  string,
		  csuriquery string,
		  sport int,
		  susername string,
		  cipcsUserAgent string,
		  csCookie string,
		  csReferer string,
		  cshost  string,
		  scstatus  int,
		  scsubstatus  int,
		  scwin32status  int,
		  scbytes int,
		  csbytes int,
		  timetaken int
		)
		partitioned by ( year int, month int)
		ROW FORMAT DELIMITED FIELDS TERMINATED BY ',' 
		STORED AS TEXTFILE 
		LOCATION '${hiveconf:partitionedtable}';
		
		INSERT INTO TABLE WebLogsPartitioned  PARTITION( year , month) 
		SELECT
		  date,
		  time,
		  ssitename,
		  csmethod,
		  csuristem,
		  csuriquery,
		  sport,
		  susername,
		  cipcsUserAgent,
		  csCookie,
		  csReferer,
		  cshost,
		  scstatus,
		  scsubstatus,
		  scwin32status,
		  scbytes,
		  csbytes,
		  timetaken,
		  year(date),
		  month(date)
		FROM WebLogsRaw

En el entorno de tiempo de ejecución, la actividad de Hive en la canalización de Data Factory pasa los valores de los parámetros **inputtable** y **partitionedtable** tal y como se muestra en el fragmento siguiente:

		"inputtable": "wasb://adfgetstarted@<storageaccountname>.blob.core.windows.net/inputdata",
		"partitionedtable": "wasb://adfgetstarted@<storageaccountname>.blob.core.windows.net/partitioneddata"

El parámetro **storageaccountname** es el nombre de la cuenta de Azure Storage.
 
#### Creación de un archivo de entrada de ejemplo
Mediante el Bloc de notas, cree un archivo denominado **input.log** en **c:\\adfgetstarted** con el siguiente contenido:

	#Software: Microsoft Internet Information Services 8.0
	#Fields: date time s-sitename cs-method cs-uri-stem cs-uri-query s-port cs-username c-ip cs(User-Agent) cs(Cookie) cs(Referer) cs-host sc-status sc-substatus sc-win32-status sc-bytes cs-bytes time-taken
	2016-01-01 02:01:09 SAMPLEWEBSITE GET /blogposts/mvc4/step2.png X-ARR-LOG-ID=2ec4b8ad-3cf0-4442-93ab-837317ece6a1 80 - 1.54.23.196 Mozilla/5.0+(Windows+NT+6.3;+WOW64)+AppleWebKit/537.36+(KHTML,+like+Gecko)+Chrome/31.0.1650.63+Safari/537.36 - http://weblogs.asp.net/sample/archive/2007/12/09/asp-net-mvc-framework-part-4-handling-form-edit-and-post-scenarios.aspx www.sample.com 200 0 0 53175 871 46
	2016-01-01 02:01:09 SAMPLEWEBSITE GET /blogposts/mvc4/step3.png X-ARR-LOG-ID=9eace870-2f49-4efd-b204-0d170da46b4a 80 - 1.54.23.196 Mozilla/5.0+(Windows+NT+6.3;+WOW64)+AppleWebKit/537.36+(KHTML,+like+Gecko)+Chrome/31.0.1650.63+Safari/537.36 - http://weblogs.asp.net/sample/archive/2007/12/09/asp-net-mvc-framework-part-4-handling-form-edit-and-post-scenarios.aspx www.sample.com 200 0 0 51237 871 32
	2016-01-01 02:01:09 SAMPLEWEBSITE GET /blogposts/mvc4/step4.png X-ARR-LOG-ID=4bea5b3d-8ac9-46c9-9b8c-ec3e9500cbea 80 - 1.54.23.196 Mozilla/5.0+(Windows+NT+6.3;+WOW64)+AppleWebKit/537.36+(KHTML,+like+Gecko)+Chrome/31.0.1650.63+Safari/537.36 - http://weblogs.asp.net/sample/archive/2007/12/09/asp-net-mvc-framework-part-4-handling-form-edit-and-post-scenarios.aspx www.sample.com 200 0 0 72177 871 47
	2016-01-01 02:01:09 SAMPLEWEBSITE GET /blogposts/mvc4/step5.png X-ARR-LOG-ID=9b0c14b1-434d-495a-9b0d-46775194257b 80 - 1.54.23.196 Mozilla/5.0+(Windows+NT+6.3;+WOW64)+AppleWebKit/537.36+(KHTML,+like+Gecko)+Chrome/31.0.1650.63+Safari/537.36 - http://weblogs.asp.net/sample/archive/2007/12/09/asp-net-mvc-framework-part-4-handling-form-edit-and-post-scenarios.aspx www.sample.com 200 0 0 37931 871 32
	2016-01-01 02:01:09 SAMPLEWEBSITE GET /blogposts/mvc4/step6.png X-ARR-LOG-ID=f99cff81-ec38-4a13-b2fe-21b10adca996 80 - 1.54.23.196 Mozilla/5.0+(Windows+NT+6.3;+WOW64)+AppleWebKit/537.36+(KHTML,+like+Gecko)+Chrome/31.0.1650.63+Safari/537.36 - http://weblogs.asp.net/sample/archive/2007/12/09/asp-net-mvc-framework-part-4-handling-form-edit-and-post-scenarios.aspx www.sample.com 200 0 0 27146 871 47
	2016-01-01 02:01:09 SAMPLEWEBSITE GET /blogposts/mvc4/step1.png X-ARR-LOG-ID=af94d3b4-8e05-4871-82c4-638f866d4e83 80 - 1.54.23.196 Mozilla/5.0+(Windows+NT+6.3;+WOW64)+AppleWebKit/537.36+(KHTML,+like+Gecko)+Chrome/31.0.1650.63+Safari/537.36 - http://weblogs.asp.net/sample/archive/2007/12/09/asp-net-mvc-framework-part-4-handling-form-edit-and-post-scenarios.aspx www.sample.com 200 0 0 66259 871 140
	2016-02-01 02:01:10 SAMPLEWEBSITE GET /blogposts/mvc4/step7.png X-ARR-LOG-ID=d7472a26-431a-4a4d-99eb-c7b4fda2cf4c 80 - 1.54.23.196 Mozilla/5.0+(Windows+NT+6.3;+WOW64)+AppleWebKit/537.36+(KHTML,+like+Gecko)+Chrome/31.0.1650.63+Safari/537.36 - http://weblogs.asp.net/sample/archive/2007/12/09/asp-net-mvc-framework-part-4-handling-form-edit-and-post-scenarios.aspx www.sample.com 200 0 0 30184 871 47
	2016-02-01 02:01:10 SAMPLEWEBSITE GET /blogposts/mvc4/step7.png X-ARR-LOG-ID=d7472a26-431a-4a4d-99eb-c7b4fda2cf4c 80 - 1.54.23.196 Mozilla/5.0+(Windows+NT+6.3;+WOW64)+AppleWebKit/537.36+(KHTML,+like+Gecko)+Chrome/31.0.1650.63+Safari/537.36 - http://weblogs.asp.net/sample/archive/2007/12/09/asp-net-mvc-framework-part-4-handling-form-edit-and-post-scenarios.aspx www.sample.com 200 0 0 30184 871 47
	2016-02-01 02:01:10 SAMPLEWEBSITE GET /blogposts/mvc4/step7.png X-ARR-LOG-ID=d7472a26-431a-4a4d-99eb-c7b4fda2cf4c 80 - 1.54.23.196 Mozilla/5.0+(Windows+NT+6.3;+WOW64)+AppleWebKit/537.36+(KHTML,+like+Gecko)+Chrome/31.0.1650.63+Safari/537.36 - http://weblogs.asp.net/sample/archive/2007/12/09/asp-net-mvc-framework-part-4-handling-form-edit-and-post-scenarios.aspx www.sample.com 200 0 0 30184 871 47
	2016-02-01 02:01:10 SAMPLEWEBSITE GET /blogposts/mvc4/step7.png X-ARR-LOG-ID=d7472a26-431a-4a4d-99eb-c7b4fda2cf4c 80 - 1.54.23.196 Mozilla/5.0+(Windows+NT+6.3;+WOW64)+AppleWebKit/537.36+(KHTML,+like+Gecko)+Chrome/31.0.1650.63+Safari/537.36 - http://weblogs.asp.net/sample/archive/2007/12/09/asp-net-mvc-framework-part-4-handling-form-edit-and-post-scenarios.aspx www.sample.com 200 0 0 30184 871 47
	2016-02-01 02:01:10 SAMPLEWEBSITE GET /blogposts/mvc4/step7.png X-ARR-LOG-ID=d7472a26-431a-4a4d-99eb-c7b4fda2cf4c 80 - 1.54.23.196 Mozilla/5.0+(Windows+NT+6.3;+WOW64)+AppleWebKit/537.36+(KHTML,+like+Gecko)+Chrome/31.0.1650.63+Safari/537.36 - http://weblogs.asp.net/sample/archive/2007/12/09/asp-net-mvc-framework-part-4-handling-form-edit-and-post-scenarios.aspx www.sample.com 200 0 0 30184 871 47
	2016-02-01 02:01:10 SAMPLEWEBSITE GET /blogposts/mvc4/step7.png X-ARR-LOG-ID=d7472a26-431a-4a4d-99eb-c7b4fda2cf4c 80 - 1.54.23.196 Mozilla/5.0+(Windows+NT+6.3;+WOW64)+AppleWebKit/537.36+(KHTML,+like+Gecko)+Chrome/31.0.1650.63+Safari/537.36 - http://weblogs.asp.net/sample/archive/2007/12/09/asp-net-mvc-framework-part-4-handling-form-edit-and-post-scenarios.aspx www.sample.com 200 0 0 30184 871 47
	2016-02-01 02:01:10 SAMPLEWEBSITE GET /blogposts/mvc4/step7.png X-ARR-LOG-ID=d7472a26-431a-4a4d-99eb-c7b4fda2cf4c 80 - 1.54.23.196 Mozilla/5.0+(Windows+NT+6.3;+WOW64)+AppleWebKit/537.36+(KHTML,+like+Gecko)+Chrome/31.0.1650.63+Safari/537.36 - http://weblogs.asp.net/sample/archive/2007/12/09/asp-net-mvc-framework-part-4-handling-form-edit-and-post-scenarios.aspx www.sample.com 200 0 0 30184 871 47
	2016-02-01 02:01:10 SAMPLEWEBSITE GET /blogposts/mvc4/step7.png X-ARR-LOG-ID=d7472a26-431a-4a4d-99eb-c7b4fda2cf4c 80 - 1.54.23.196 Mozilla/5.0+(Windows+NT+6.3;+WOW64)+AppleWebKit/537.36+(KHTML,+like+Gecko)+Chrome/31.0.1650.63+Safari/537.36 - http://weblogs.asp.net/sample/archive/2007/12/09/asp-net-mvc-framework-part-4-handling-form-edit-and-post-scenarios.aspx www.sample.com 200 0 0 30184 871 47
	2016-03-01 02:01:10 SAMPLEWEBSITE GET /blogposts/mvc4/step7.png X-ARR-LOG-ID=d7472a26-431a-4a4d-99eb-c7b4fda2cf4c 80 - 1.54.23.196 Mozilla/5.0+(Windows+NT+6.3;+WOW64)+AppleWebKit/537.36+(KHTML,+like+Gecko)+Chrome/31.0.1650.63+Safari/537.36 - http://weblogs.asp.net/sample/archive/2007/12/09/asp-net-mvc-framework-part-4-handling-form-edit-and-post-scenarios.aspx www.sample.com 200 0 0 30184 871 47
	2016-03-01 02:01:10 SAMPLEWEBSITE GET /blogposts/mvc4/step7.png X-ARR-LOG-ID=d7472a26-431a-4a4d-99eb-c7b4fda2cf4c 80 - 1.54.23.196 Mozilla/5.0+(Windows+NT+6.3;+WOW64)+AppleWebKit/537.36+(KHTML,+like+Gecko)+Chrome/31.0.1650.63+Safari/537.36 - http://weblogs.asp.net/sample/archive/2007/12/09/asp-net-mvc-framework-part-4-handling-form-edit-and-post-scenarios.aspx www.sample.com 200 0 0 30184 871 47
	2016-03-01 02:01:10 SAMPLEWEBSITE GET /blogposts/mvc4/step7.png X-ARR-LOG-ID=d7472a26-431a-4a4d-99eb-c7b4fda2cf4c 80 - 1.54.23.196 Mozilla/5.0+(Windows+NT+6.3;+WOW64)+AppleWebKit/537.36+(KHTML,+like+Gecko)+Chrome/31.0.1650.63+Safari/537.36 - http://weblogs.asp.net/sample/archive/2007/12/09/asp-net-mvc-framework-part-4-handling-form-edit-and-post-scenarios.aspx www.sample.com 200 0 0 30184 871 47
	2016-03-01 02:01:10 SAMPLEWEBSITE GET /blogposts/mvc4/step7.png X-ARR-LOG-ID=d7472a26-431a-4a4d-99eb-c7b4fda2cf4c 80 - 1.54.23.196 Mozilla/5.0+(Windows+NT+6.3;+WOW64)+AppleWebKit/537.36+(KHTML,+like+Gecko)+Chrome/31.0.1650.63+Safari/537.36 - http://weblogs.asp.net/sample/archive/2007/12/09/asp-net-mvc-framework-part-4-handling-form-edit-and-post-scenarios.aspx www.sample.com 200 0 0 30184 871 47
	2016-03-01 02:01:10 SAMPLEWEBSITE GET /blogposts/mvc4/step7.png X-ARR-LOG-ID=d7472a26-431a-4a4d-99eb-c7b4fda2cf4c 80 - 1.54.23.196 Mozilla/5.0+(Windows+NT+6.3;+WOW64)+AppleWebKit/537.36+(KHTML,+like+Gecko)+Chrome/31.0.1650.63+Safari/537.36 - http://weblogs.asp.net/sample/archive/2007/12/09/asp-net-mvc-framework-part-4-handling-form-edit-and-post-scenarios.aspx www.sample.com 200 0 0 30184 871 47

#### Carga del archivo de entrada y del archivo HQL en Almacenamiento de blobs de Azure

Esta sección proporciona instrucciones sobre cómo usar la herramienta **AzCopy** para copiar archivos input.log y partitionweblogs.hql en Azure Blob Storage. Puede usar la herramienta que prefiera (por ejemplo: el [Explorador de Almacenamiento de Microsoft Azure](http://storageexplorer.com/) o [CloudXPlorer de ClumsyLeaf Software](http://clumsyleaf.com/products/cloudxplorer)) para realizar esta tarea.
	 
1. Descargue la [versión más reciente de \[AzCopy\](http://aka.ms/downloadazcopypr)](http://aka.ms/downloadazcopy) o la **versión más reciente de vista previa**. Consulte el artículo [Uso de AzCopy](../storage/storage-use-azcopy.md) para obtener instrucciones sobre cómo usar la utilidad.
2. Navegue hasta la carpeta c:\\adfgetstarted y ejecute el siguiente comando:

		"C:\Program Files (x86)\Microsoft SDKs\Azure\AzCopy\AzCopy" /Source:. /Dest:https://<storageaccountname>.blob.core.windows.net/adfgetstarted/inputdata /DestKey:<storageaccesskey>  /Pattern:input.log

	Este comando carga el archivo **input.log** a la cuenta de almacenamiento (contenedor **adfgetstarted** y carpeta **inputdata**). Reemplace **storageaccountname** por el nombre de la cuenta de almacenamiento y **storageaccesskey** por la clave de acceso del almacenamiento.

	> [AZURE.NOTE] Este comando crea un contenedor llamado **adfgetstarted** en Blob Storage de Azure y copia el archivo **input.log** desde la unidad local a la carpeta **inputdata** del contenedor.
	
3. Una vez que se ha cargado correctamente el archivo, verá un resultado similar al siguiente de AzCopy.
	
		Finished 1 of total 1 file(s).
		[2015/12/16 23:07:33] Transfer summary:
		-----------------
		Total files transferred: 1
		Transfer successfully:   1
		Transfer skipped:        0
		Transfer failed:         0
		Elapsed time:            00.00:00:01
5. Ejecute el comando siguiente para cargar el archivo **partitionweblogs.hql** en la carpeta **script** del contenedor **adfgetstarted**. Este es el comando:
	
		AzCopy /Source:. /Dest:https://<storageaccountname>.blob.core.windows.net/adfgetstarted/script /DestKey:<storageaccesskey>  /Pattern:partitionweblogs.hql

<!---HONumber=AcomDC_0921_2016-->