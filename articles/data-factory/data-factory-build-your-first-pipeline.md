<properties
	pageTitle="Compilación de la primera canalización mediante la Factoría de datos de Azure"
	description="En este tutorial se muestra cómo crear una canalización de datos de ejemplo que transforma los datos con HDInsight de Azure y el Editor de la Factoría de datos."
	services="data-factory"
	documentationCenter=""
	authors="spelluru"
	manager="jhubbard"
	editor="monicar"/>

<tags
	ms.service="data-factory"
	ms.workload="data-services"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="get-started-article" 
	ms.date="07/27/2015"
	ms.author="spelluru"/>

# Compilación de la primera canalización mediante la Factoría de datos de Azure
> [AZURE.SELECTOR]
- [Tutorial Overview](data-factory-build-your-first-pipeline.md)
- [Using Data Factory Editor](data-factory-build-your-first-pipeline-using-editor.md)
- [Using PowerShell](data-factory-build-your-first-pipeline-using-powershell.md)
- [Using Visual Studio](data-factory-build-your-first-pipeline-using-vs.md)

Este artículo le ayuda a comenzar a crear su primera canalización e implementarla en la Factoría de datos de Azure.

> [AZURE.NOTE]Este artículo no ofrece información general conceptual sobre el servicio Factoría de datos de Azure. Para obtener información general detallada del servicio, vea el artículo [Introducción a la Factoría de datos de Azure](data-factory-introduction.md).

## Información general del tutorial
Este tutorial le guiará por los pasos necesarios para poner en funcionamiento la primera canalización. Se pueden crear canalizaciones y especificar todos los recursos necesarios desde el principio.

Si desea explorar rápidamente las distintas capacidades de la factoría de datos, sin crear una desde el principio, puede usar los ejemplos que se ofrecen en el Portal de vista previa de Azure. Vea [Actualización de la Factoría de datos de Azure: implementación de ejemplo simplificada](http://azure.microsoft.com/blog/2015/04/24/azure-data-factory-update-simplified-sample-deployment/) sobre cómo implementar y usar un ejemplo basado en caso de uso mediante el Portal de vista previa de Azure.

## Requisitos previos
Antes de comenzar este tutorial, debe cumplir los siguientes requisitos previos:

1.	**Suscripción de Azure**: si no tiene ninguna, puede crear una cuenta de evaluación gratuita en un par de minutos. Vea el artículo [Evaluación gratuita](http://azure.microsoft.com/pricing/free-trial/) sobre cómo puede obtener una cuenta de evaluación gratuita.

2.	**Almacenamiento de Azure**: usará una cuenta de almacenamiento de Azure para almacenar los datos en este tutorial. Si no dispone de una cuenta de almacenamiento de Azure, vea el artículo [Creación de una cuenta de almacenamiento](../storage-create-storage-account/#create-a-storage-account). Tras crear la cuenta de almacenamiento, tendrá que obtener la clave de cuenta que se usa para tener acceso al almacenamiento. Vea [Vista, copia y regeneración de las claves de acceso de almacenamiento](../storage-create-storage-account/#view-copy-and-regenerate-storage-access-keys).

## ¿Qué se va a tratar en este tutorial?	
La Factoría de datos de Azure permite componer tareas de procesamiento de datos y movimiento de datos como datos controlados por flujo de trabajo. Aprenderá a crear una primera canalización que use HDInsight para transformar y analizar registros web mensualmente.

En este tutorial, realizará los siguientes pasos:

1.	Creación de una factoría de datos.
2.	Creación de los siguientes servicios vinculados:
	1.	**Cuenta de almacenamiento de Azure**: se usará la cuenta de almacenamiento de Azure para almacenar archivos que utiliza el clúster de HDInsight a petición.
	2.	**Clúster HDInsight a petición**: se iniciará un clúster de HDInsight a petición para transformar y analizar los datos.
3.	Creación del conjunto de datos de salida 
4.	Creación de la canalización que ejecuta un script de Hive y almacena el resultado en el conjunto de datos de salida. El script de Hive crea primero una tabla externa, que hace referencia a los datos de registro web sin procesar que se almacenan en el almacenamiento de blobs de Azure. El siguiente paso del script de Hive es realizar particiones de los datos sin procesar por año y mes.

La primera canalización, denominada **MyFirstPipeline** usa una actividad de Hive para transformar y analizar registros web que se implementan como parte del clúster de HDInsight y se almacenan en **/HdiSamples/WebsiteLogSampleData/SampleLog/**.

![Vista de diagrama](./media/data-factory-build-your-first-pipeline/diagram-view.png)

Después de ejecutar el script de Hive, los resultados se almacenarán en un contenedor de almacenamiento de blobs de Azure: **data/partitioneddata**.

La disponibilidad definida en el conjunto de datos **AzureBlobOutput** determina la frecuencia con la que se ejecuta la actividad de Hive. En este tutorial, se establece en mensualmente.

## Preparar el almacenamiento de Azure para el tutorial
Antes de comenzar el tutorial, tendrá que preparar el almacenamiento de Azure con los archivos necesarios para el tutorial.

1. Inicie el Bloc de notas, pegue el texto siguiente y guárdelo como **partitionweblogs.hql** en la carpeta C:\\adfgettingstarted del disco duro. Estos scripts de Hive crean dos tablas externas: **WebLogsRaw** y **WebLogsPartitioned**.

	> [AZURE.IMPORTANT]Reemplace **storageaccountname** en la última línea por el nombre de la cuenta de almacenamiento.

		set hive.exec.dynamic.partition.mode=nonstrict;

		DROP TABLE IF EXISTS WebLogsRaw; 
		CREATE EXTERNAL TABLE WebLogsRaw (
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
		LOCATION '/HdiSamples/WebsiteLogSampleData/SampleLog/'
		tblproperties ("skip.header.line.count"="2");
		
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

	 
 
2. Para preparar el almacenamiento de Azure para el tutorial:
	1. Descargue la [versión más reciente de [AzCopy](http://aka.ms/downloadazcopypr)](http://aka.ms/downloadazcopy) o la **versión más reciente de vista previa**. Consulte el artículo [Cómo usar AzCopy](../storage/storage-use-azcopy.md) para obtener instrucciones sobre el uso de la utilidad.
	2. Después de instalar AzCopy, puede agregarlo a la ruta de acceso del sistema ejecutando el comando siguiente en un símbolo del sistema. 
	
			set path=%path%;C:\Program Files (x86)\Microsoft SDKs\Azure\AzCopy
	

	3. Navegue hasta la carpeta c:\\adfgettingstarted y ejecute el comando siguiente para cargar el archivo .HQL de Hive a la cuenta de almacenamiento. Reemplace **<StorageAccountName>** por el nombre de la cuenta de almacenamiento y **<Storage Key>** por la clave de la cuenta de almacenamiento.

			AzCopy /Source:. /Dest:https://<StorageAccountName>.blob.core.windows.net/script /DestKey:<Storage Key>
	4. Una vez cargado correctamente el archivo, verá el siguiente resultado de AzCopy.
	
			Finished 1 of total 1 file(s).
			[2015/06/15 15:47:13] Transfer summary:
			-----------------
			Total files transferred: 1
			Transfer successfully:   1
			Transfer skipped:        0
			Transfer failed:         0
			Elapsed time:            00.00:00:01

Haga lo siguiente:

- Haga clic en el vínculo [Uso del editor de la Factoría de datos](data-factory-build-your-first-pipeline-using-editor.md) en la parte superior para realizar el tutorial con el Editor de Factoría de datos, que forma parte del Portal de Azure.
- Haga clic en el vínculo [Uso de PowerShell](data-factory-build-your-first-pipeline-using-powershell.md) en la parte superior para realizar el tutorial con PowerShell de Azure.
- Haga clic en el vínculo [Uso de Visual Studio](data-factory-build-your-first-pipeline-using-vs.md) de la parte superior para realizar el tutorial con Visual Studio. 

<!---HONumber=August15_HO7-->