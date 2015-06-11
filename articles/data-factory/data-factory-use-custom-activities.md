<properties 
	pageTitle="Uso de actividades personalizadas en una canalización de Factoría de datos de Azure" 
	description="Obtenga información acerca de cómo crear actividades personalizadas y usarlas en una canalización de la factoría de datos de Azure." 
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
	ms.topic="article" 
	ms.date="06/02/2015" 
	ms.author="spelluru"/>

# Uso de actividades personalizadas en una canalización de Factoría de datos de Azure
Factoría de datos de Azure es compatible con las actividades integradas como **copia actividad** y **HDInsight actividad** para usarse en canalizaciones para mover y procesar los datos. También puede crear una actividad personalizada de .NET con su propia lógica de procesamiento/transformación y utilizar la actividad en una canalización. Puede configurar la actividad para que se ejecute con un **HDInsight de Azure** clúster o un **Azure lote** servicio.

En este artículo se describe cómo crear una actividad personalizada y utilizarla en una canalización de la Factoría de datos. También proporciona un tutorial detallado con instrucciones paso a paso para crear y usar una actividad personalizada. El tutorial utiliza el servicio HDInsight vinculado. Para usar el proceso de Azure vinculados service en su lugar, se crea un servicio de tipo vinculado **AzureBatchLinkedService** y se utiliza en la sección de la actividad de la canalización de JSON (** linkedServiceName **). Consulte la [Servicios de Azure lote vinculados](#AzureBatch) sección para obtener más información acerca del uso de lote de Azure con la actividad personalizada.

## Requisitos previos
Descargue la última versión [paquete NuGet para el generador de datos de Azure][nuget-package] e instalarlo. Las instrucciones están en el [tutorial](#SupportedSourcesAndSinks) en este artículo.

## Creación de una actividad personalizada

Para crear una actividad personalizada:
 
1.	Crear un **biblioteca de clases** proyecto en Visual Studio 2013.
3. Agregue las siguientes instrucciones using al principio del archivo de origen en la biblioteca de clases.
	
		using Microsoft.Azure.Management.DataFactories.Models;
		using Microsoft.DataFactories.Runtime; 

4. Actualizar la clase para implementar la **IDotNetActivity** interfaz.
	<ol type='a'>
	<li>
		Derive la clase de <b>IDotNetActivity</b>.
		<br/>
		Ejemplo: <br/>
		clase pública <b>MyDotNetActivity: IDotNetActivity</b>
	</li>

	<li>
		Implemente el <b>Execute</b> método <b>IDotNetActivity</b> (interfaz)
	</li>

</ol>
5. Compile el proyecto.


## Usar la actividad personalizada en una canalización
Para usar la actividad personalizada en una canalización:

1.	**Comprimir** todos los archivos binarios de la **bin\debug** o **bin\release** carpetas para el proyecto de salida. 
2.	**Cargar el código postal** archivo como un blob para su **almacenamiento Azure blob**. 
3.	Actualización de la **JSON de canalización** que haga referencia a un archivo zip, DLL de actividad personalizada, la clase de actividad y el blob que contiene el archivo zip en la canalización de JSON. En el archivo JSON:
	<ol type ="a">
	<li><b>Tipo de actividad</b> debe establecerse en <b>DotNetActivity</b>.</li>
	<li><b>AssemblyName</b> es el nombre de la DLL de salida del proyecto de Visual Studio.</li>
	<li><b>EntryPoint</b> especifica la <b>espacio de nombres</b> y <b>nombre</b> de la <b>clase</b> que implementa el <b>IDotNetActivity</b> interfaz.</li>
	<li><b>PackageLinkedService</b> es el servicio vinculado que hace referencia al blob que contiene el archivo zip. </li>
	<li><b>PackageFile</b> especifica la ubicación y el nombre del archivo zip que se cargó en el almacenamiento de blobs de Azure.</li>
	<li><b>LinkedServiceName</b> es el nombre del servicio vinculado que se vincula a un clúster de HDInsight (a petición o su propio) a un generador de datos. La actividad personalizada se ejecuta como un trabajo de solo asignación del clúster de HDInsight especificado.</li>
</ol>**Ejemplo JSON parcial**

		"Name": "MyDotNetActivity",
    	"Type": "DotNetActivity",
    	"Inputs": [{"Name": "EmpTableFromBlob"}],
    	"Outputs": [{"Name": "OutputTableForCustom"}],
		"LinkedServiceName": "myhdinsightcluster",
    	"Transformation":
    	{
	    	"AssemblyName": "MyDotNetActivity.dll",
    	    "EntryPoint": "MyDotNetActivityNS.MyDotNetActivity",
    	    "PackageLinkedService": "MyBlobStore",
    	    "PackageFile": "customactivitycontainer/MyDotNetActivity.zip",

## Actualización de una actividad personalizada
Si actualiza el código de la actividad personalizada, compilarlo y cargue el archivo zip que contiene los nuevos binarios para el almacenamiento de blobs.

## <a name="walkthrough" /> Tutorial
Este tutorial proporciona instrucciones paso a paso para crear una actividad personalizada y utilizar la actividad en una canalización del generador de datos de Azure. En este tutorial amplía el tutorial desde el [empezar a trabajar con el generador de datos de Azure][adfgetstarted]. Si desea ver la actividad personalizada en funcionamiento, tendrá que revisar primero el tutorial de introducción y, a continuación, realizar este otro.

**Requisitos previos:**


- Tutorial de [empezar a trabajar con el generador de datos de Azure][adfgetstarted]. Debe completar el tutorial de este artículo antes de realizar este tutorial.
- Visual Studio 2012 o 2013
- Descargue e instale [Azure .NET SDK][azure-developer-center]
- Descargue la última versión [paquete NuGet para el generador de datos de Azure][nuget-package] e instalarlo. Las instrucciones están en el tutorial.
- Descargue e instale el paquete de NuGet para Almacenamiento de Azure. Las instrucciones están en el tutorial, así que puede omitir este paso.

## Paso 1: Crear una actividad personalizada

1.	Cree un proyecto de biblioteca de clases .NET.
	<ol type="a">
	<li>Iniciar <b>Visual Studio 2012</b> o <b>Visual Studio 2013</b>.</li>
	<li>Haga clic en <b>archivo</b>, seleccione <b>nuevo</b>, y haga clic en <b>proyecto</b>.</li> 
	<li>Expanda <b>plantillas</b>, y seleccione <b>Visual C#</b>. En este tutorial se usa C#, pero puede usar cualquier lenguaje .NET para desarrollar la actividad personalizada.</li> 
	<li>Seleccione <b>biblioteca de clases de</b> de la lista de tipos de proyecto de la derecha.</li>
	<li>Escriba <b>MyDotNetActivity</b> para el <b>nombre</b>.</li> 
	<li>Seleccione <b>C:\ADFGetStarted</b> para el <b>ubicación</b>.</li>
	<li>Haga clic en <b>Aceptar</b> para crear el proyecto.</li>
</ol>
2.  Haga clic en <b>herramientas</b>, elija <b>el Administrador de paquetes de NuGet</b>, y haga clic en <b>Package Manager Console</b>.
3.	En el <b>Package Manager Console</b>, ejecute el siguiente comando para importar <b>Microsoft.Azure.Management.DataFactories</b>. 

		Install-Package Microsoft.Azure.Management.DataFactories –Pre

3.	En el <b>Package Manager Console</b>, ejecute el siguiente comando para importar <b>Microsoft.DataFactories.Runtime</b>. Reemplace la carpeta por la ubicación que contiene el paquete de NuGet de Factoría de datos descargado.

		Install-Package Microsoft.DataFactories.Runtime –Pre

4. Importe el paquete NuGet de almacenamiento de Azure en el proyecto.

		Install-Package Azure.Storage

5. Agregue el siguiente **con** las instrucciones en el archivo de origen en el proyecto.

		using System.IO;
		using System.Globalization;
		using System.Diagnostics;
	
		using Microsoft.Azure.Management.DataFactories.Models;
		using Microsoft.DataFactories.Runtime; 
	
		using Microsoft.WindowsAzure.Storage;
		using Microsoft.WindowsAzure.Storage.Blob;
  
6. Cambiar el nombre de la **espacio de nombres** a **MyDotNetActivityNS**.

		namespace MyDotNetActivityNS

7. Cambiar el nombre de la clase **MyDotNetActivity** y derivar desde la **IDotNetActivity** interfaz tal y como se muestra a continuación.

		public class MyDotNetActivity : IDotNetActivity

8. Implemente (Agregar) el **Execute** método de la **IDotNetActivity** interfaz a la **MyDotNetActivity** de clase y copie el siguiente código de ejemplo para el método.

	El **inputTables** y **outputTables** parámetros representan tablas de entrada y salidas de la actividad, tal como lo sugiere el nombre. Puede ver mensajes de registro mediante el **registrador** objeto en el archivo de registro que puede descargar desde el portal de Azure o mediante los cmdlets. El **extendedProperties** diccionario contiene la lista de propiedades extendidas que especifique en el archivo JSON para la actividad y sus valores.

	El código de ejemplo siguiente cuenta el número de líneas en el blob de entrada y genera el siguiente contenido en el blob de salida: ruta de acceso del BLOB, el número de líneas en el blob, el equipo en el que se ejecutó la actividad, fecha y hora actual.

        public IDictionary<string, string> Execute(
                    IEnumerable<ResolvedTable> inputTables, 
                    IEnumerable<ResolvedTable> outputTables, 
                    IDictionary<string, string> extendedProperties, 
                    IActivityLogger logger)
        {
            string output = string.Empty;

            logger.Write(TraceEventType.Information, "Before anything...");

            logger.Write(TraceEventType.Information, "Printing dictionary entities if any...");
            foreach (KeyValuePair<string, string> entry in extendedProperties)
            {
                logger.Write(TraceEventType.Information, "<key:{0}> <value:{1}>", entry.Key, entry.Value);
            }

            foreach (ResolvedTable inputTable in inputTables)
            {
                string connectionString = GetConnectionString(inputTable.LinkedService);
                string folderPath = GetFolderPath(inputTable.Table);

                if (String.IsNullOrEmpty(connectionString) ||
                    String.IsNullOrEmpty(folderPath))
                {
                    continue;
                }

                logger.Write(TraceEventType.Information, "Reading blob from: {0}", folderPath);

                CloudStorageAccount inputStorageAccount = CloudStorageAccount.Parse(connectionString);
                CloudBlobClient inputClient = inputStorageAccount.CreateCloudBlobClient();

                BlobContinuationToken continuationToken = null;

                do
                {
                    BlobResultSegment result = inputClient.ListBlobsSegmented(folderPath, 
												true, 
												BlobListingDetails.Metadata, 
												null, 
												continuationToken, 
												null, 
												null);
                    foreach (IListBlobItem listBlobItem in result.Results)
                    {
                        CloudBlockBlob inputBlob = listBlobItem as CloudBlockBlob;
                        int count = 0;
                        if (inputBlob != null)
                        {
                            using (StreamReader sr = new StreamReader(inputBlob.OpenRead()))
                            {
                                while (!sr.EndOfStream)
                                {
                                    string line = sr.ReadLine();
                                    if (count == 0)
                                    {
                                        logger.Write(TraceEventType.Information, "First line: [{0}]", line);
                                    }
                                    count++;
                                }

                            }

                        }
                        output += string.Format(CultureInfo.InvariantCulture,
                                        "{0},{1},{2},{3},{4}\n",
                                        folderPath,
                                        inputBlob.Name,
                                        count,
                                        Environment.MachineName,
                                        DateTime.UtcNow);

                    }
                    continuationToken = result.ContinuationToken;

                } while (continuationToken != null);
            }

            foreach (ResolvedTable outputTable in outputTables)
            {
                string connectionString = GetConnectionString(outputTable.LinkedService);
                string folderPath = GetFolderPath(outputTable.Table);

                if (String.IsNullOrEmpty(connectionString) ||
                    String.IsNullOrEmpty(folderPath))
                {
                    continue;
                }

                logger.Write(TraceEventType.Information, "Writing blob to: {0}", folderPath);

                CloudStorageAccount outputStorageAccount = CloudStorageAccount.Parse(connectionString);
                Uri outputBlobUri = new Uri(outputStorageAccount.BlobEndpoint, folderPath + "/" + Guid.NewGuid() + ".csv");

                CloudBlockBlob outputBlob = new CloudBlockBlob(outputBlobUri, outputStorageAccount.Credentials);
                outputBlob.UploadText(output);

            }
            return new Dictionary<string, string>();

        }

9. Agregue los siguientes métodos auxiliares. El **Execute** método invoca estos métodos auxiliares. El **GetConnectionString** método recupera la cadena de conexión de almacenamiento de Azure y **GetFolderPath** método recupera la ubicación del blob.


        private static string GetConnectionString(LinkedService asset)
        {
            AzureStorageLinkedService storageAsset;
            if (asset == null)
            {
                return null;
            }

            storageAsset = asset.Properties as AzureStorageLinkedService;
            if (storageAsset == null)
            {
                return null;
            }

            return storageAsset.ConnectionString;
        }

        private static string GetFolderPath(Table dataArtifact)
        {
            AzureBlobLocation blobLocation;
            if (dataArtifact == null || dataArtifact.Properties == null)
            {
                return null;
            }

            blobLocation = dataArtifact.Properties.Location as AzureBlobLocation;
            if (blobLocation == null)
            {
                return null;
            }

            return blobLocation.FolderPath;
        }
   


10. Compile el proyecto. Haga clic en **crear** en el menú y haga clic en **Generar solución**.
11. Iniciar **Windows Explorer**, y vaya a **bin\debug** o **bin\release** según tipo de carpeta de compilación.
12. Crear un archivo zip **MyDotNetActivity.zip** que contienen todos los archivos binarios en el <project folder>carpeta \bin\Debug.
13. Cargar **MyDotNetActivity.zip** como un blob al contenedor de blobs: **customactvitycontainer** almacenamiento de blobs de Azure que el **MyBlobStore** vinculados de servicio en la **ADFTutorialDataFactory** utiliza. Crear el contenedor de blob **blobcustomactivitycontainer** si todavía no existe. 


## Paso 2: Utilizar la actividad personalizada en una canalización
Estos son los pasos que se va a realizar en este paso:

1. Crear un servicio para el clúster de HDInsight en el que se ejecutará la actividad personalizada como un solo mapa de trabajo vinculado. 
2. Crear una tabla de resultados que genere la canalización en este ejemplo.
3. Crear y ejecutar una canalización que usa la actividad personalizada que creó en el paso 1. 
 
### Crear un servicio de clúster de HDInsight que se utilizará para ejecutar la actividad personalizada vinculado
El servicio Factoría de datos de Azure admite la creación de un clúster a petición y usarlo para procesar la entrada para generar datos de salida. También puede utilizar su propio clúster para realizar la misma tarea. Cuando se utiliza el clúster de HDInsight a petición, se crea un clúster para cada sector. Mientras que, si utiliza su propio clúster de HDInsight, el clúster está preparado para procesar el sector inmediatamente. Por lo tanto, cuando utilice el clúster a petición, es posible que no vea los datos de salida tan rápido como cuando utilice su propio clúster.

> [AZURE.NOTE]En tiempo de ejecución, una instancia de una actividad de .NET sólo se ejecuta en un nodo de trabajo en el clúster de HDInsight no se puede escalar para que se ejecute en varios nodos. Pueden ejecutar varias instancias de actividad de .NET en paralelo en distintos nodos del clúster de HDInsight.

Si se ha ampliado la [empezar a trabajar con el generador de datos de Azure][adfgetstarted] tutorial con el tutorial de [usar Pig y Hive con el generador de datos de Azure][hivewalkthrough], puede omitir la creación de este servicio vinculado y utilizar el servicio vinculado ya existe en el ADFTutorialDataFactory.


#### Para utilizar un clúster de HDInsight a petición

1. En el **Portal Azure**, haga clic en **autor e implementar** en la página de inicio del generador de datos.
2. En el Editor del generador de datos, haga clic en **nuevo cálculo** en la barra de comandos y seleccione **clúster de HDInsight a petición** en el menú.
2. Haga lo siguiente en la secuencia de comandos JSON: 
	1. Para el **clusterSize** propiedad, especifique el tamaño del clúster de HDInsight.
	2. Para el **jobsContainer** propiedad, especifique el nombre del contenedor predeterminado donde se almacenarán los registros de clúster. Para este tutorial, especifique **adfjobscontainer**.
	3. Para el **timeToLive** propiedad, especifique cuánto tiempo el cliente puede estar inactivo antes de eliminarla. 
	4. Para el **versión** propiedad, especificar la versión de HDInsight que desea utilizar. Si excluye esta propiedad, se utiliza la versión más reciente.  
	5. Para el **linkedServiceName**, especifique **StorageLinkedService** tutorial de introducción que se han creado en Get. 

			{
		    	"name": "HDInsightOnDemandLinkedService",
				    "properties": {
		    	    "type": "HDInsightOnDemandLinkedService",
		    	    "clusterSize": "4",
		    	    "jobsContainer": "adfjobscontainer",
		    	    "timeToLive": "00:05:00",
		    	    "version": "3.1",
		    	    "linkedServiceName": "StorageLinkedService"
		    	}
			}

2. Haga clic en **implementar** en la barra de comandos para implementar el servicio vinculado.
   
#### Para utilizar su propio clúster de HDInsight: 

1. En el **Portal Azure**, haga clic en **autor e implementar** en la página de inicio del generador de datos.
2. En el **Editor del generador de datos**, haga clic en **nuevo cálculo** en la barra de comandos y seleccione **clúster de HDInsight** en el menú.
2. Haga lo siguiente en la secuencia de comandos JSON: 
	1. Para el **clusterUri** propiedad, escriba la dirección URL para su HDInsight. Por ejemplo: https://<clustername>.azurehdinsight.net/     
	2. Para el **nombre de usuario** propiedad, escriba el nombre de usuario que tenga acceso al clúster de HDInsight.
	3. Para el **contraseña** propiedad, escriba la contraseña del usuario. 
	4. Para el **LinkedServiceName** propiedad, escriba **StorageLinkedService**. Éste es el servicio vinculado que creó en el tutorial de introducción. 

2. Haga clic en **implementar** en la barra de comandos para implementar el servicio vinculado.

### Crear una tabla de resultados

1. En el **editor factoría de datos**, haga clic en **nuevo conjunto de datos**, y, a continuación, haga clic en **almacenamiento Azure Blob** desde la barra de comandos.
2. Reemplace el script JSON en el panel derecho con el siguiente script JSON:

		{
    		"name": "OutputTableForCustom",
    		"properties":
    		{
        		"location": 
        		{
					"type": "AzureBlobLocation",
					"folderPath": "adftutorial/customactivityoutput/{Slice}",
					"partitionedBy": [ { "name": "Slice", "value": { "type": "DateTime", "date": "SliceStart", "format": "yyyyMMddHH" } }],

					"linkedServiceName": "MyBlobStore"
        		},
        		"availability": 
        		{
            		"frequency": "hour",
            		"interval": 1
        		}   
    		}
		}


 	Ubicación de salida es **customactivityoutput/adftutorial/AAAAMMDDHH/** donde AAAAMMDDHH es el año, mes, fecha y hora del sector que se produce. Consulte [material de referencia][adf-developer-reference] para obtener más información.

2. Haga clic en **implementar** en la barra de comandos para implementar la tabla.


### Crear y ejecutar una canalización que usa la actividad personalizada
   
1. En el Editor del generador de datos, haga clic en **nueva canalización** en la barra de comandos. Si no ve el comando, haga clic en **... (Puntos suspensivos)** para verlo. 
2. Reemplace el JSON en el panel derecho con el siguiente script JSON. Si desea utilizar su propio clúster y seguir los pasos para crear el **HDInsightLinkedService** vinculado servicio, reemplace **HDInsightOnDemandLinkedService** con **HDInsightLinkedService** en el siguiente JSON. 

		{
    		"name": "ADFTutorialPipelineCustom",
    		"properties":
    		{
        		"description" : "Use custom activity",
        		"activities":
        		[
					{
                		"Name": "MyDotNetActivity",
                     	"Type": "DotNetActivity",
                     	"Inputs": [{"Name": "EmpTableFromBlob"}],
                     	"Outputs": [{"Name": "OutputTableForCustom"}],
						"LinkedServiceName": "HDInsightLinkedService",
                     	"Transformation":
                     	{
                        	"AssemblyName": "MyDotNetActivity.dll",
                            "EntryPoint": "MyDotNetActivityNS.MyDotNetActivity",
                            "PackageLinkedService": "MyBlobStore",
                            "PackageFile": "customactivitycontainer/MyDotNetActivity.zip",
                            "ExtendedProperties":
							{
								"SliceStart": "$$Text.Format('{0:yyyyMMddHH-mm}', Time.AddMinutes(SliceStart, 0))"
							}
                      	},
                        "Policy":
                        {
                        	"Concurrency": 1,
                            "ExecutionPriorityOrder": "OldestFirst",
                            "Retry": 3,
                            "Timeout": "00:30:00",
                            "Delay": "00:00:00"		
						}
					}
        		],
				"start": "2015-02-13T00:00:00Z",
        		"end": "2015-02-14T00:00:00Z",
        		"isPaused": false
			}
		}

	> [AZURE.NOTE]Reemplace **StartDateTime** valor con los tres días antes de la fecha actual y **finalización** valor con la fecha actual. StartDateTime y finalización deben estar en [formato ISO](http://en.wikipedia.org/wiki/ISO_8601). Por ejemplo: 2014-10-14T16:32:41Z. La tabla de salida está programada para producirse cada día, por lo que habrá tres segmentos producidos.

	Tenga en cuenta lo siguiente:

	- Hay una actividad en la sección actividades y es de tipo: **DotNetActivity**.
	- Utilizar la misma tabla de entrada **EmpTableFromBlob** tutorial de inicio que utilizó en Get.
	- Usar una nueva tabla de salida **OutputTableForCustom** que creará en el paso siguiente.
	- **AssemblyName** se establece en el nombre del archivo DLL: **MyActivities.dll**.
	- **EntryPoint** está establecido en **MyDotNetActivityNS.MyDotNetActivity**.
	- **PackageLinkedService** está establecido en **MyBlobStore** que se creó como parte del tutorial de [empezar a trabajar con el generador de datos de Azure][adfgetstarted]. Este almacén de blobs contiene el archivo comprimido de la actividad personalizada.
	- **PackageFile** está establecido en **customactivitycontainer/MyDotNetActivity.zip**.
     
4. Haga clic en **implementar** en la barra de comandos para implementar la canalización.
8. Compruebe que los archivos de salida se generan en el almacenamiento de blobs en el **adftutorial** contenedor.

	![resultado de la actividad personalizada][image-data-factory-ouput-from-custom-activity]

9. Si abre el archivo de salida, debería ver un resultado similar al siguiente:
	
	adftutorial/,emp.txt,2,WORKERNODE0,03/27/2015 19:23:28

	(ubicación de blobs), (nombre del blob) (número de líneas en el blob) (nodo en el que se ejecutó la actividad), (marca de tiempo de fecha)

10.	Utilice la [Portal Azure][azure-preview-portal] o los cmdlets de PowerShell de Azure para supervisar su generador de datos, canalizaciones y conjuntos de datos. Puede ver mensajes desde el **ActivityLogger** en el código de la actividad personalizada en los registros se puede descargar desde el portal o mediante cmdlets.

	![descargar los registros de actividad personalizado][image-data-factory-download-logs-from-custom-activity]
   
Consulte [empezar a trabajar con el generador de datos de Azure][adfgetstarted] para obtener pasos detallados para la supervisión de conjuntos de datos y las canalizaciones.
    
## <a name="AzureBatch"></a> Uso de Azure lote vinculado servicio 
> [AZURE.NOTE]Consulte [Introducción técnica de Azure por lotes][batch-technical-overview] para obtener información general del lote de Azure de servicio y ver [Introducción a la biblioteca de lote de Azure para .NET][batch-get-started] para empezar a trabajar rápidamente con el servicio de proceso de Azure.

Estos son los pasos de alto nivel para usar el servicio vinculado del lote de Azure en el tutorial que se describe en la sección anterior:

1. Cree una cuenta de proceso de Azure mediante el Portal de administración de Azure. Consulte [Introducción técnica de Azure por lotes][batch-create-account] artículo para obtener instrucciones. Anote la clave de nombre y la cuenta de la cuenta de proceso de Azure. 

	También puede usar [nueva AzureBatchAccount][new-azure-batch-account] cmdlet para crear una cuenta de proceso de Azure. Consulte [con Azure PowerShell para administrar la cuenta de proceso de Azure][azure-batch-blog] para obtener instrucciones detalladas sobre cómo utilizar este cmdlet. 
2. Cree un grupo de proceso de Azure. Puede descargar y utilizar la [herramienta Explorador de lote de Azure][batch-explorer] (o) Utilice [biblioteca de lote de Azure para .NET][batch-net-library] para crear un grupo de proceso de Azure. Consulte [Tutorial de ejemplo en el Explorador de Azure por lotes][batch-explorer-walkthrough] para obtener instrucciones paso a paso para usar el Explorador de lote de Azure.
	
	También puede usar [nueva AzureBatchPool][new-azure-batch-pool] cmdlet para crear un grupo de proceso de Azure.

2. Crear un servicio vinculado de lote de Azure mediante la siguiente plantilla JSON. El Editor de generador de datos muestra una plantilla similar por usted para empezar. Especifique el nombre de la cuenta de proceso de Azure, el nombre de clave y el grupo de cuenta en el fragmento JSON.

		{
		    "name": "AzureBatchLinkedService",
		    "properties": {
		        "type": "AzureBatchLinkedService",
		        "accountName": "<Azure Batch account name>",
		        "accessKey": "<Azure Batch account key>",
		        "poolName": "<Azure Batch pool name>",
		        "linkedServiceName": "<Specify associated storage linked service reference here>"
		  }
		}

	Consulte [tema de MSDN de servicio de Azure lote vinculado](https://msdn.microsoft.com/library/mt163609.aspx) para obtener una descripción de estas propiedades.

2.  En el Editor del generador de datos, abra la definición de JSON de la canalización que creó en el tutorial y reemplace **HDInsightLinkedService** con **AzureBatchLinkedService**.
3.  Puede que desee cambiar los tiempos de inicio y finalización para la canalización para que pueda probar el escenario con el servicio de proceso de Azure. 
4.  Puede ver las tareas de proceso de Azure asociadas con el procesamiento de los sectores en el Explorador de lote de Azure como se muestra en el diagrama siguiente.

	![Tareas de proceso de Azure][image-data-factory-azure-batch-tasks]

## Otras referencias

[Las actualizaciones de generador de datos de azure: actividades de ejecutar ADF personalizada .NET mediante el proceso de Azure](http://azure.microsoft.com/blog/2015/05/01/azure-data-factory-updates-execute-adf-custom-net-activities-using-azure-batch/).

[batch-net-library]: ../batch/batch-dotnet-get-started.md
[batch-explorer]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp/BatchExplorer
[batch-explorer-walkthrough]: http://blogs.technet.com/b/windowshpc/archive/2015/01/20/azure-batch-explorer-sample-walkthrough.aspx
[batch-create-account]: ../batch/batch-technical-overview.md/#batch-concepts
[batch-technical-overview]: ../batch/batch-technical-overview.md
[batch-get-started]: ../batch/batch-dotnet-get-started.md
[monitor-manage-using-powershell]: data-factory-monitor-manage-using-powershell.md
[use-onpremises-datasources]: data-factory-use-onpremises-datasources.md
[adf-tutorial]: data-factory-tutorial.md
[use-custom-activities]: data-factory-use-custom-activities.md
[use-pig-and-hive-with-data-factory]: data-factory-pig-hive-activities.md
[troubleshoot]: data-factory-troubleshoot.md
[data-factory-introduction]: data-factory-introduction.md
[azure-powershell-install]: https://github.com/Azure/azure-sdk-tools/releases


[developer-reference]: http://go.microsoft.com/fwlink/?LinkId=516908
[cmdlet-reference]: http://go.microsoft.com/fwlink/?LinkId=517456

[new-azure-batch-account]: https://msdn.microsoft.com/library/mt125880.aspx
[new-azure-batch-pool]: https://msdn.microsoft.com/library/mt125936.aspx
[azure-batch-blog]: http://blogs.technet.com/b/windowshpc/archive/2014/10/28/using-azure-powershell-to-manage-azure-batch-account.aspx

[nuget-package]: http://go.microsoft.com/fwlink/?LinkId=517478
[azure-developer-center]: http://azure.microsoft.com/develop/net/
[adf-developer-reference]: http://go.microsoft.com/fwlink/?LinkId=516908
[azure-preview-portal]: https://portal.azure.com/

[adfgetstarted]: data-factory-get-started.md
[hivewalkthrough]: data-factory-pig-hive-activities.md

[image-data-factory-ouput-from-custom-activity]: ./media/data-factory-use-custom-activities/OutputFilesFromCustomActivity.png

[image-data-factory-download-logs-from-custom-activity]: ./media/data-factory-use-custom-activities/DownloadLogsFromCustomActivity.png

[image-data-factory-azure-batch-tasks]: ./media/data-factory-use-custom-activities/AzureBatchTasks.png

<!---HONumber=GIT-SubDir--> 