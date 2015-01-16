<properties title="Use custom activities in an Azure Data Factory pipeline" pageTitle="Uso de actividades personalizadas en una canalización de Factoría de datos de Azure" description="Obtenga información acerca de cómo crear actividades personalizadas y usarlas en una canalización de la factoría de datos de Azure." metaKeywords=""  services="data-factory" solutions=""  documentationCenter="" authors="spelluru" manager="jhubbard" editor="monicar" />

<tags ms.service="data-factory" ms.workload="data-services" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="spelluru" />

# Uso de actividades personalizadas en una canalización de Factoría de datos de Azure
La Factoría de datos de Azure admite el uso de actividades integradas como la **Actividad de copia** y la **Actividad de HDInsight** en las canalizaciones a fin de mover y procesar datos. También puede crear una actividad personalizada con su propia lógica de procesamiento/transformación y usar la actividad en una canalización. La actividad personalizada se ejecuta como un trabajo de solo asignación en un clúster de HDInsight, por lo que deberá vincular un clúster de HDInsight para la actividad personalizada en la canalización.
 
En este artículo se describe cómo crear una actividad personalizada y utilizarla en una canalización de la Factoría de datos. También proporciona un tutorial detallado con instrucciones paso a paso para crear y usar una actividad personalizada.

## Creación de una actividad personalizada

Para crear una actividad personalizada:
 
1.	Cree un proyecto de **biblioteca de clases** en Visual Studio 2013.
2.	Descargue el [paquete de NuGet para la Factoría de datos de Azure ][nuget-package] e instálelo. Las instrucciones de instalación están en el tutorial.
3. Agregue las siguientes instrucciones using al principio del archivo de origen en la biblioteca de clases.
	
		using Microsoft.Azure.Management.DataFactories.Models;
		using Microsoft.DataFactories.Runtime; 

4. Actualice la clase para implementar la interfaz **ICustomActivity*.
	<ol type='a'>
		<li>
			Derive la clase desde <b>ICustomActivity</b>.
			<br/>
			Ejemplo: <br/>
			public class <b>MyCustomActivity : ICustomActivity</b>
		</li>

		<li>
			Implemente el método  <b>Execute</b>  de la interfaz <b>ICustomActivity</b>
		</li>

	</ol>
5. Compile el proyecto.


## Uso la actividad personalizada en una canalización
Para usar la actividad personalizada en una canalización:

1.	**Comprima** todos los archivos binarios desde las carpetas de salida **bin\debug** o **bin\release** para el proyecto. 
2.	**Cargue el archivo comprimido** como un blob en su **almacenamiento de blob de Azure**. 
3.	Actualice el archivo **JSON de canalización** para hacer referencia al archivo comprimido, la DLL de actividad personalizada, la clase de actividad y el blob que contiene el archivo comprimido en la canalización de JSON. En el archivo JSON:
	<ol type ="a">
		<li><b>El tipo de actividad</b> debe establecerse en <b>CustomActivity</b>.</li>
		<li><b>AssemblyName</b> es el nombre de la DLL de salida del proyecto de Visual Studio.</li>
		<li><b>EntryPoint</b> especifica el <b>espacio de nombres</b> y el <b>nombre</b> de la <b>clase</b> que implementa la interfaz <b>ICustomActivity</b>.</li>
		<li><b>PackageLinkedService</b> es el servicio vinculado que hace referencia al blob que contiene el archivo comprimido. </li>
		<li><b>PackageFile</b> especifica la ubicación y el nombre del archivo comprimido que se ha cargado en el almacenamiento de blobs de Azure.</li>
		<li><b>LinkedServiceName</b> es el nombre del servicio vinculado que vincula un clúster de HDInsight (a petición o suyo propio) a una factoría de datos. La actividad personalizada se ejecuta como un trabajo de solo asignación del clúster de HDInsight especificado.</li>
	</ol>

	

	**Ejemplo JSON parcial**

		"Name": "MyCustomActivity",
    	"Type": "CustomActivity",
    	"Inputs": [{"Name": "EmpTableFromBlob"}],
    	"Outputs": [{"Name": "OutputTableForCustom"}],
		"LinkedServiceName": "myhdinsightcluster",
    	"Transformation":
    	{
	    	"AssemblyName": "MyCustomActivity.dll",
    	    "EntryPoint": "MyCustomActivityNS.MyCustomActivity",
    	    "PackageLinkedService": "MyBlobStore",
    	    "PackageFile": "customactivitycontainer/MyCustomActivity.zip",

## Para actualizar la actividad personalizada
Si actualiza el código de la actividad personalizada, lo compila y carga el archivo comprimido que contiene los nuevos binarios en el almacenamiento de blobs, deberá sobrescribir la canalización existente que usa la actividad personalizada ejecutando **New-AzureDataFactoryPipeline**. Cuando se crea una canalización que usa la actividad personalizada, el archivo comprimido se copia desde el almacenamiento de blobs especificado a un contenedor de Factoría de datos de Azure en el almacenamiento de blobs conectado al clúster de HDInsight. Esto se realiza únicamente en la creación de la canalización. Por lo tanto, debe volver a crear la canalización al actualizar la actividad personalizada. 

El siguiente tutorial proporciona instrucciones paso a paso para crear una actividad personalizada y utilizarla en una canalización de Factoría de datos de Azure. Este tutorial sirve como ampliación de la [Introducción a Factoría de datos de Azure][adfgetstarted]. Si desea ver la actividad personalizada en funcionamiento, tendrá que revisar primero el tutorial de introducción y, a continuación, realizar este otro. 

## Tutorial
**Requisitos previos:**


- Tutorial de seguimiento de [Introducción a Factoría de datos de Azure][adfgetstarted]. Debe completar el tutorial presentado en este artículo antes de continuar con este otro tutorial.
- Visual Studio 2012 o 2013
- Descargue e instale el [SDK de .NET de Microsoft Azure][azure-developer-center].
- Descargue [paquetes de NuGet para Factoría de datos de Azure][nuget-package].
- Descargue e instale el paquete de NuGet para Almacenamiento de Azure. Las instrucciones están en el tutorial, así que puede omitir este paso.

### Paso 1: creación de una actividad personalizada

1.	Cree un proyecto de biblioteca de clases .NET.
	<ol type="a">
		<li>Inicie <b>Visual Studio 2012</b> o <b>Visual Studio 2013</b>.</li>
		<li>Haga clic en <b>Archivo</b>, seleccione <b>Nuevo</b> y, a continuación, haga clic en <b>Proyecto</b>.</li> 
		<li>Expanda <b>Plantillas</b> y seleccione <b>Visual C#</b>. En este tutorial se usa C#, pero puede usar cualquier lenguaje .NET para desarrollar la actividad personalizada.</li> 
		<li>Seleccione <b>Biblioteca de clases</b> en la lista de tipos de proyecto de la derecha.</li>
		<li>Escriba <b>MyCustomActivity</b> para <b>Nombre</b>.</li> 
		<li>Seleccione <b>C:\ADFGetStarted</b> para <b>Ubicación</b>.</li>
		<li>Haga clic en <b>Aceptar</b> para crear el proyecto.</li>
	</ol>
2.  Haga clic en <b>Herramientas</b>, seleccione <b>Administrador de paquetes de NuGet</b> y haga clic en <b>Consola del Administrador de paquetes</b>.
3.	En la <b>Consola del Administrador de paquetes</b>, ejecute el siguiente comando para importar <b>Microsoft.Azure.Management.DataFactories</b> que descargó anteriormente. Reemplace la carpeta por la ubicación que contiene el paquete de NuGet de Factoría de datos descargado.

		Install-Package Microsoft.Azure.Management.DataFactories -Source d:\packages -Pre

3.	En la <b>Consola del Administrador de paquetes</b>, ejecute el siguiente comando para importar <b>Microsoft.DataFactories.Runtime</b>. Reemplace la carpeta por la ubicación que contiene el paquete de NuGet de Factoría de datos descargado.

		Install-Package Microsoft.DataFactories.Runtime -Source d:\packages -Pre

4. Importe el paquete de NuGet de Almacenamiento de Microsoft Azure en el proyecto.

		Install-Package WindowsAzure.Storage

5. Agregue las siguientes instrucciones **using** al archivo de origen en el proyecto.

		using System.IO;
		using System.Globalization;
	
		using Microsoft.Azure.Management.DataFactories.Models;
		using Microsoft.DataFactories.Runtime; 
	
		using Microsoft.WindowsAzure.Storage;
		using Microsoft.WindowsAzure.Storage.Blob;
  
6. Cambie el nombre del **espacio de nombres** a **MyCustomActivityNS**.

		namespace MyCustomActivityNS

7. Cambie el nombre de la clase a **MyCustomActivity** y derívela desde la interfaz **ICustomActivity** tal como se muestra a continuación.

		public class MyCustomActivity : ICustomActivity

8. Implemente (agregue) el método **Execute** de la interfaz **ICustomActivity** en la clase **MyCustomActivity** y copie el siguiente código de ejemplo en el método. 

	Los parámetros **inputTables** y **outputTables** representan tablas de entrada y salida para la actividad, como sugiere su nombre. Puede ver mensajes de registro mediante el objeto **registrador** en el archivo de registro que puede descargar desde el portal de Azure o utilizando los cmdlets. El diccionario **extendedProperties** contiene la lista de propiedades extendidas que se especifica en el archivo JSON de la actividad y sus valores. 

	El código de ejemplo siguiente cuenta el número de líneas en el blob de entrada y genera el siguiente contenido en el blob de salida: ruta de acceso al blob, número de líneas del blob, equipo en el que se ejecutó la actividad, fecha y hora actuales.

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
										"{0},{1},{2},{3}\n", 
										folderPath, 
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

9. Agregue los siguientes métodos auxiliares. El método **Execute** invoca estos métodos auxiliares. El método **GetConnectionString** recupera la cadena de conexión de Almacenamiento de Azure y el método **GetFolderPath** recupera la ubicación del blob. 


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
   


10. Compile el proyecto. Haga clic en **Compilar** en el menú y haga clic en **Compilar solución**.
11. Inicie el **Explorador de Windows** y desplácese a la carpeta **bin\debug** o **bin\release**, dependiendo del tipo de compilación.
12. Cree un archivo comprimido **MyCustomActivity.zip** que contenga todos los archivos binarios en la carpeta \bin\Debug de <carpeta de proyecto>.
	![zip output binaries][image-data-factory-zip-output-binaries]
13. Cargue **MyCustomActivity.zip** como un blob al contenedor de blobs: **customactvitycontainer** en el almacenamiento de blobs de Azure que utiliza el servicio vinculado **MyBlobStore** en **ADFTutorialDataFactory**.  Si todavía no existe, cree **blobcustomactivitycontainer** del contenedor de blobs. 
    ![upload zip to blob][image-data-factory-upload-zip-to-blob]

### Cree un servicio vinculado para el   clúster de HDInsight que se utilizará para ejecutar la actividad personalizada
El servicio Factoría de datos de Azure admite la creación de un clúster a petición y usarlo para procesar la entrada para generar datos de salida. También puede utilizar su propio clúster para realizar la misma tarea. Cuando se utiliza el clúster de HDInsight a petición, se crea un clúster para cada sector. Mientras que, si utiliza su propio clúster de HDInsight, el clúster está preparado para procesar el sector inmediatamente. Por lo tanto, cuando utilice el clúster a petición, es posible que no vea los datos de salida tan rápido como cuando utilice su propio clúster. Para los fines de este ejemplo, vamos a usar un clúster a petición. 

> [WACOM.NOTE] Si ya ha ampliado el tutorial [Introducción a Factoría de datos de Azure][adfgetstarted] con el tutorial [Uso de Pig y Hive con Factoría de datos de Azure][hivewalkthrough], puede omitir la creación de este servicio vinculado y usar el servicio vinculado que ya existe en ADFTutorialDataFactory. 

#### Para utilizar un clúster de HDInsight a petición

1. Cree un archivo JSON denominado **HDInsightOnDemandCluster.json** con el contenido siguiente y guárdelo en la carpeta **C:\ADFGetStarted\Custom**.


		{
    		"name": "HDInsightOnDemandCluster",
    		"properties": 
    		{
        		"type": "HDInsightOnDemandLinkedService",
				"clusterSize": 4,
        		"jobsContainer": "adftutorialjobscontainer",
        		"timeToLive": "00:05:00",
        		"linkedServiceName": "MyBlobStore"
    		}
		}

2. Inicie **Azure PowerShell** y ejecute el siguiente comando para cambiar al modo **AzureResourceManager**. Los cmdlets de Factoría de datos de Azure están disponibles en el modo **AzureResourceManager**.

         switch-azuremode AzureResourceManager
		

3. Cambie a la carpeta **C:\ADFGetstarted\Custom**.
4. Ejecute el siguiente comando para crear el servicio vinculado para el clúster de HDInsight a petición.
 
		New-AzureDataFactoryLinkedService -ResourceGroupName ADFTutorialResourceGroup -DataFactoryName ADFTutorialDataFactory -File .\HDInsightOnDemandCluster.json
  
   
#### Para utilizar su propio clúster de HDInsight: 

1. Cree un archivo JSON denominado **MyHDInsightCluster.json** con el contenido siguiente y guárdelo en la carpeta **C:\ADFGetStarted\Custom**. Reemplace el nombre de clúster, el nombre de usuario y la contraseña por valores apropiados antes de guardar el archivo JSON.  

		{
   			"Name": "MyHDInsightCluster",
    		"Properties": 
			{
        		"Type": "HDInsightBYOCLinkedService",
	        	"ClusterUri": "https://<clustername>.azurehdinsight.net/",
    	    	"UserName": "<username>",
    	    	"Password": "<password>",
    	    	"LinkedServiceName": "MyBlobStore"
    		}
		}

2. Inicie **Azure PowerShell** y ejecute el siguiente comando para cambiar al modo **AzureResourceManager**. Los cmdlets de Factoría de datos de Azure están disponibles en el modo **AzureResourceManager**.

         switch-azuremode AzureResourceManager
		

3. Cambie a la carpeta **C:\ADFGetstarted\Custom**.
4. Ejecute el siguiente comando para crear el servicio vinculado para el clúster de HDInsight a petición.
 
		New-AzureDataFactoryLinkedService -ResourceGroupName ADFTutorialResourceGroup -DataFactoryName ADFTutorialDataFactory -File .\MyHDInsightCluster.json



### Paso 2: uso la actividad personalizada en una canalización
Vamos a ampliar el tutorial [Introducción a Factoría de datos de Azure][adfgetstarted] para crear otra canalización a fin de probar esta actividad personalizada.

#### Creación de un servicio vinculado para el clúster de HDInsight que se usará para ejecutar actividades personalizadas


1.	Cree un JSON para la canalización, como se muestra en el ejemplo siguiente, y guárdelo como **ADFTutorialPipelineCustom.json** en la carpeta **C:\ADFGetStarted\Custom**. Cambie el nombre de **LinkedServiceName** por el nombre del clúster de HDInsight (**HDInsightOnDemandCluster** o **MyHDInsightCluster**)


		{
    		"name": "ADFTutorialPipelineCustom",
    		"properties":
    		{
        		"description" : "Use custom activity",
        		"activities":
        		[
					{
                		"Name": "MyCustomActivity",
                     	"Type": "CustomActivity",
                     	"Inputs": [{"Name": "EmpTableFromBlob"}],
                     	"Outputs": [{"Name": "OutputTableForCustom"}],
						"LinkedServiceName": "MyHDInsightCluster",
                     	"Transformation":
                     	{
                        	"AssemblyName": "MyCustomActivity.dll",
                            "EntryPoint": "MyCustomActivityNS.MyCustomActivity",
                            "PackageLinkedService": "MyBlobStore",
                            "PackageFile": "customactivitycontainer/MyCustomActivity.zip",
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
				]
			}
		}

	Tenga en cuenta lo siguiente: 

	- Hay una actividad en la sección de actividades y es de tipo: **CustomActivity**.
	- Utilice la misma tabla de entrada **EmpTableFromBlob** que utilizó en el tutorial de introducción.
	- Use una nueva tabla de salida **OutputTableForCustom** que creará en el paso siguiente.
	- **AssemblyName** se establece en el nombre de la DLL: **MyActivities.dll**.
	- **EntryPoint** se establece en **MyCustomActivityNS.MyCustomActivity**.
	- **PackageLinkedService** se establece en **MyBlobStore**, que se creó como parte del tutorial [Introducción a Factoría de datos de Azure][adfgetstarted]. Este almacén de blobs contiene el archivo comprimido de la actividad personalizada.
	- **PackageFile** se establece en **customactivitycontainer/MyCustomActivity.zip**.
     

4. Cree un archivo JSON para la tabla de salida (**OutputTableForCustom** al que hace referencia la canalización JSON) y guárdelo como C:\ADFGetStarted\Custom\OutputTableForCustom.json.

		

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

 	La ubicación de salida es **adftutorial/customactivityoutput/YYYYMMDDHH/**, donde YYYYMMDDHH es el año, el mes, el día y la hora del sector que se está generando. Consulte la [Referencia de desarrollador][adf-developer-reference] para obtener más información. 

5. Ejecute el siguiente comando para **crear la tabla de salida** en **ADFTutorialDataFactory**.
		
		

		New-AzureDataFactoryTable  -DataFactoryName ADFTutorialDataFactory -File C:\ADFGetStarted\Custom\OutputTableForCustom.json -ResourceGroupName ADFTutorialResourceGroup


6. Ahora, ejecute el siguiente comando para **crear la canalización**. En el paso anterior creó el archivo JSON de canalización.

		New-AzureDataFactoryPipeline  -DataFactoryName ADFTutorialDataFactory -File C:\ADFGetStarted\Custom\ADFTutorialPipelineCustom.json -ResourceGroupName ADFTutorialResourceGroup



7. Ejecute el siguiente comando de PowerShell para **establecer el período activo** en la canalización que ha creado.

		Set-AzureDataFactoryPipelineActivePeriod -ResourceGroupName ADFTutorialResourceGroup -DataFactoryName ADFTutorialDataFactory -StartDateTime 2014-09-29 -EndDateTime 2014-09-30 -Name ADFTutorialPipelineCustom

	> [WACOM.NOTE] Reemplace el valor de **StartDateTime** por el día actual y el valor de **EndDateTime** por el día siguiente. Tanto StartDateTime como EndDateTime son horas UTC y deben estar en [formato ISO](http://en.wikipedia.org/wiki/ISO_8601). Por ejemplo: 2014-10-14T16:32:41Z. **EndDateTime** es un valor opcional, pero se utilizará en este tutorial. 
	> Si no especifica **EndDateTime**, se calcula como "**StartDateTime + 48 horas**". Para ejecutar la canalización indefinidamente, especifique **9/9/9999** como valor de **EndDateTime**.



8. Compruebe que los archivos de salida se generan en el almacenamiento de blobs del contenedor **adftutorial**.

	![output from custom activity][image-data-factory-ouput-from-custom-activity]

9. Si abre el archivo de salida, debería ver un resultado similar al siguiente:
	
	adftutorial/input,2,WORKERNODE0,10/07/2014 18:34:33 

	(ubicación de blobs), (número de líneas en el blob) (nodo en el que se ejecutó la actividad), (marca de tiempo de fecha)

10.	Utilice el [Portal de Azure][azure-preview-portal] o los cmdlets de Azure PowerShell para supervisar su factoría de datos, canalizaciones y conjuntos de datos. Puede ver mensajes desde **ActivityLogger** en el código de la actividad personalizada en los registros que puede descargar desde el portal o mediante cmdlets.

	![download logs from custom activity][image-data-factory-download-logs-from-custom-activity]
   
Consulte [Introducción a Factoría de datos de Azure][adfgetstarted] para obtener pasos detallados para la supervisión de conjuntos de datos y canalizaciones.      
    
## Otras referencias

Artículo | Descripción
------ | ---------------
[Habilitación de las canalizaciones para que funcionen con datos locales][use-onpremises-datasources] | Este artículo contiene un tutorial que muestra cómo copiar datos desde una base de datos de SQL Server local a un blob de Azure.
[Uso de Pig y Hive con la Factoría de datos][use-pig-and-hive-with-data-factory] | Este artículo contiene un tutorial que muestra cómo utilizar la actividad HDInsight para ejecutar un script de hive y pig que procesa datos de entrada para generar datos de salida. 
[Tutorial: Movimiento y procesamiento de archivos de registro usando la Factoría de datos][adf-tutorial] | Este artículo ofrece un tutorial completo que muestra cómo implementar un escenario casi real usando la Factoría de datos de Azure para transformar datos de archivos de registro en perspectivas.
[Uso de actividades personalizadas en Factoría de datos][use-custom-activities] | Este artículo ofrece un tutorial con instrucciones paso a paso para crear una actividad personalizada y utilizarla en una canalización. 
[Supervisión y administración de Factoría de datos de Azure con PowerShell][monitor-manage-using-powershell] | Este artículo describe cómo supervisar Factoría de datos de Azure mediante cmdlets de Azure PowerShell. Puede probar los ejemplos del artículo en ADFTutorialDataFactory.
[Solución de problemas de Factoría de datos][troubleshoot] | Este artículo describe cómo solucionar problemas de Factoría de datos de Azure. Puede probar el tutorial de este artículo en ADFTutorialDataFactory introduciendo un error (eliminación de la tabla en la Base de datos SQL de Azure). 
[Referencia de desarrollador de Factoría de datos de Azure][developer-reference] | La Referencia de desarrollador cuenta con un exhaustivo contenido de referencia para cmdlets, script de JSON, funciones, etc. 


[monitor-manage-using-powershell]: ../data-factory-monitor-manage-using-powershell
[use-onpremises-datasources]: ../data-factory-use-onpremises-datasources
[adf-tutorial]: ../data-factory-tutorial
[use-custom-activities]: ../data-factory-use-custom-activities
[use-pig-and-hive-with-data-factory]: ../data-factory-pig-hive-activities
[troubleshoot]: ../data-factory-troubleshoot
[data-factory-introduction]: ../data-factory-introduction


[developer-reference]: http://go.microsoft.com/fwlink/?LinkId=516908
[cmdlet-reference]: http://go.microsoft.com/fwlink/?LinkId=517456




[nuget-package]: http://go.microsoft.com/fwlink/?LinkId=517478
[azure-developer-center]: http://azure.microsoft.com/es-es/develop/net/
[adf-developer-reference]: http://go.microsoft.com/fwlink/?LinkId=516908
[azure-preview-portal]: https://portal.azure.com/

[adfgetstarted]: ../data-factory-get-started
[hivewalkthrough]: ../data-factory-pig-hive-activities

[image-data-factory-zip-output-binaries]: ./media/data-factory-use-custom-activities/ZipOuputBinaries.png

[image-data-factory-upload-zip-to-blob]: ./media/data-factory-use-custom-activities/UploadZipToBlob.png

[image-data-factory-ouput-from-custom-activity]: ./media/data-factory-use-custom-activities/OutputFilesFromCustomActivity.png

[image-data-factory-download-logs-from-custom-activity]: ./media/data-factory-use-custom-activities/DownloadLogsFromCustomActivity.png
