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
	ms.date="10/06/2015"
	ms.author="spelluru"/>

# Uso de actividades personalizadas en una canalización de Factoría de datos de Azure
La Factoría de datos de Azure admite el uso de actividades integradas como la **actividad de copia** y la **actividad de HDInsight** en las canalizaciones a fin de mover y procesar datos. También puede crear una actividad personalizada de .NET con su propia lógica de procesamiento/transformación y usar la actividad en una canalización. Puede configurar la actividad para que se ejecute con un clúster de **HDInsight de Azure** o un servicio **Lote de Azure**.

En este artículo se describe cómo crear una actividad personalizada y utilizarla en una canalización de la Factoría de datos. También proporciona un tutorial detallado con instrucciones paso a paso para crear y usar una actividad personalizada. El tutorial usa el servicio vinculado de HDInsight. Para usar el servicio vinculado de Lote de Azure, se crea un servicio vinculado de tipo **AzureBatch** y se usa en la sección de actividad del archivo JSON de la canalización (**linkedServiceName**). Vea la sección [Servicio vinculado de Lote de Azure](#AzureBatch) para obtener información detallada sobre el uso de Lote de Azure con la actividad personalizada.


## <a name="walkthrough" /> Tutorial
El siguiente tutorial incluye instrucciones paso a paso para crear una actividad personalizada y usarla en una canalización de Factoría de datos de Azure. Este tutorial amplía el tutorial [Introducción a Factoría de datos de Azure][adfgetstarted]. Si desea ver la actividad personalizada en funcionamiento, tendrá que revisar primero el tutorial de introducción y, a continuación, realizar este otro.

**Requisitos previos:**


- Tutorial de [Introducción a Factoría de datos de Azure][adfgetstarted]. Debe completar el tutorial presentado en este artículo antes de realizar este otro tutorial.
- Visual Studio 2012 o 2013
- Descargue e instale el [SDK de .NET de Azure][azure-developer-center].
- Descargue el [paquete de NuGet para Factoría de datos de Azure](https://www.nuget.org/packages/Microsoft.Azure.Management.DataFactories/) más reciente e instálelo. Las instrucciones están en el tutorial.
- Descargue e instale el paquete de NuGet para Almacenamiento de Azure. Las instrucciones están en el tutorial, así que puede omitir este paso.

## Paso 1: Creación de una actividad personalizada

1.	Cree un proyecto de biblioteca de clases .NET.
	<ol type="a">
	<li>Inicie <b>Visual Studio 2012</b> o <b>Visual Studio 2013</b>.</li>
	<li>Haga clic en <b>Archivo</b>, seleccione <b>Nuevo</b> y, luego, haga clic en <b>Proyecto</b>.</li>
	<li>Expanda <b>Plantillas</b> y seleccione <b>Visual C#</b>. En este tutorial se usa C#, pero puede usar cualquier lenguaje .NET para desarrollar la actividad personalizada.</li>
	<li>Seleccione <b>Biblioteca de clases</b> en la lista de tipos de proyecto de la derecha.</li>
	<li>Escriba <b>MyDotNetActivity</b> para <b>Nombre</b>.</li>
	<li>Seleccione <b>C:\ADFGetStarted</b> para <b>Ubicación</b>.</li>
	<li>Haga clic en <b>Aceptar</b> para crear el proyecto.</li>
</ol>
2.  Haga clic en <b>Herramientas</b>, seleccione <b>Administrador de paquetes de NuGet</b> y haga clic en <b>Consola del Administrador de paquetes</b>.
3.	En la <b>Consola del Administrador de paquetes</b>, ejecute el siguiente comando para importar <b>Microsoft.Azure.Management.DataFactories</b>.

		Install-Package Microsoft.Azure.Management.DataFactories

4. Importe el paquete de NuGet de Almacenamiento de Azure en el proyecto.

		Install-Package Azure.Storage

5. Agregue las siguientes instrucciones **using** al archivo de origen en el proyecto.

		using System.IO;
		using System.Globalization;
		using System.Diagnostics;
		using System.Linq;

		using Microsoft.Azure.Management.DataFactories.Models;
		using Microsoft.Azure.Management.DataFactories.Runtime;

		using Microsoft.WindowsAzure.Storage;
		using Microsoft.WindowsAzure.Storage.Blob;

6. Cambie el nombre del **espacio de nombres** a **MyDotNetActivityNS**.

		namespace MyDotNetActivityNS

7. Cambie el nombre de la clase a **MyDotNetActivity** y derívela desde la interfaz **IDotNetActivity** tal como se muestra a continuación.

		public class MyDotNetActivity : IDotNetActivity

8. Implemente (agregue) el método **Execute** de la interfaz **IDotNetActivity** en la clase **MyDotNetActivity** y copie el siguiente código de ejemplo en el método.

	El código de ejemplo siguiente cuenta el número de líneas del blob de entrada y genera el siguiente contenido en el blob de salida: ruta de acceso al blob, número de líneas del blob, equipo donde se ejecutó la actividad, fecha y hora actual.

		public IDictionary<string, string> Execute(IEnumerable<LinkedService> linkedServices, IEnumerable<Dataset> datasets, Activity activity, IActivityLogger logger)
        {
            IDictionary<string, string> extendedProperties = ((DotNetActivity)activity.TypeProperties).ExtendedProperties;

            AzureStorageLinkedService inputLinkedService, outputLinkedService;
            CustomDataset inputLocation;
            AzureBlobDataset outputLocation;

            Dataset inputDataset = datasets.Single(dataset => dataset.Name == activity.Inputs.Single().Name);
            inputLocation = inputDataset.Properties.TypeProperties as CustomDataset;

            // using First method instead of Single since we are using the same
            // Azure Storage linked service for input and output.
            inputLinkedService = linkedServices.First(linkedService => linkedService.Name == inputDataset.Properties.LinkedServiceName).Properties.TypeProperties as AzureStorageLinkedService;

            string output = string.Empty;

            logger.Write("Before anything...");

            logger.Write("Printing dictionary entities if any...");
            foreach (KeyValuePair<string, string> entry in extendedProperties)
            {
                logger.Write("<key:{0}> <value:{1}>", entry.Key, entry.Value);
            }

            string connectionString = GetConnectionString(inputLinkedService);
            string folderPath = GetFolderPath(inputDataset);

            logger.Write("Reading blob from: {0}", folderPath);

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
                                    logger.Write("First line: [{0}]", line);
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

            Dataset outputDataset = datasets.Single(dataset => dataset.Name == activity.Outputs.Single().Name);
            outputLocation = outputDataset.Properties.TypeProperties as AzureBlobDataset;
            outputLinkedService = linkedServices.First(linkedService => linkedService.Name == outputDataset.Properties.LinkedServiceName).Properties.TypeProperties as AzureStorageLinkedService;

            connectionString = GetConnectionString(outputLinkedService);
            folderPath = GetFolderPath(outputDataset);

            logger.Write("Writing blob to: {0}", folderPath);

            CloudStorageAccount outputStorageAccount = CloudStorageAccount.Parse(connectionString);
            Uri outputBlobUri = new Uri(outputStorageAccount.BlobEndpoint, folderPath + "/" + Guid.NewGuid() + ".csv");

            CloudBlockBlob outputBlob = new CloudBlockBlob(outputBlobUri, outputStorageAccount.Credentials);
            outputBlob.UploadText(output);

            return new Dictionary<string, string>();

        }

9. Agregue los siguientes métodos auxiliares. El método **Execute** invoca estos métodos auxiliares. El método **GetConnectionString** recupera la cadena de conexión de Almacenamiento de Azure y el método **GetFolderPath** recupera la ubicación del blob.


        private static string GetConnectionString(AzureStorageLinkedService asset)
        {

            if (asset == null)
            {
                return null;
            }

            return asset.ConnectionString;
        }

        private static string GetFolderPath(Dataset dataArtifact)
        {
            if (dataArtifact == null || dataArtifact.Properties == null)
            {
                return null;
            }

            AzureBlobDataset blobDataset = dataArtifact.Properties.TypeProperties as AzureBlobDataset;
            if (blobDataset == null)
            {
                return null;
            }

            return blobDataset.FolderPath;
        }


10. Compile el proyecto. Haga clic en **Compilar** en el menú y haga clic en **Compilar solución**.
11. Inicie el **Explorador de Windows** y vaya a la carpeta **bin\\debug** o **bin\\release** según el tipo de compilación.
12. Cree un archivo comprimido **MyDotNetActivity.zip** que contenga todos los archivos binarios en la carpeta <project folder>\\bin\\Debug. Puede que desee incluir el archivo MyDotNetActivity.pdb para obtener detalles adicionales, como el número de línea en el código fuente que produjo el problema en caso de error.
13. Cargue **MyDotNetActivity.zip** como un blob en el contenedor de blobs: **customactvitycontainer** en el almacenamiento de blobs de Azure que usa el servicio vinculado **StorageLinkedService** en **ADFTutorialDataFactory** Cree el contenedor de blobs **customactivitycontainer** si aún no existe.

> [AZURE.NOTE]Si agrega este proyecto de actividad de .NET a una solución en Visual Studio que contenga un proyecto de la factoría de datos, no tendrá que realizar los dos últimos pasos de creación del archivo zip y que cargarlos manualmente en el almacenamiento de blobs de Azure. Al publicar las entidades de la factoría de datos con Visual Studio el proceso de publicación realizar automáticamente estos pasos. Consulte los artículos [Crear su primera canalización mediante Visual Studio](data-factory-build-your-first-pipeline-using-vs.md) y [Copiar datos del Blob de Azure en SQL de Azure](data-factory-get-started-using-vs.md) para obtener información sobre la creación y publicación de las entidades de la factoría de datos con Visual Studio.


## Paso 2: Uso de la actividad personalizada en una canalización
Estas son las acciones que realizará en este paso:

1. Crear un servicio vinculado para el clúster de HDInsight en el que se ejecutará la actividad personalizada como un trabajo de solo asignación.
2. Crear una tabla de salida que generará la canalización de este ejemplo.
3. Crear y ejecutar una canalización que use la actividad personalizada que creó en el paso 1.

### Crear un servicio vinculado para el clúster de HDInsight que se usará para ejecutar la actividad personalizada
El servicio Factoría de datos de Azure admite la creación de un clúster a petición y usarlo para procesar la entrada para generar datos de salida. También puede utilizar su propio clúster para realizar la misma tarea. Cuando se utiliza el clúster de HDInsight a petición, se crea un clúster para cada sector. Mientras que, si utiliza su propio clúster de HDInsight, el clúster está preparado para procesar el sector inmediatamente. Por lo tanto, cuando utilice el clúster a petición, es posible que no vea los datos de salida tan rápido como cuando utilice su propio clúster.

> [AZURE.NOTE]En tiempo de ejecución, una instancia de una actividad de .NET solo se ejecuta en un nodo de trabajo en el clúster de HDInsight; no se puede escalar para que se ejecute en varios nodos. Se pueden ejecutar en paralelo varias instancias de actividad de .NET en distintos nodos del clúster de HDInsight.

Si ya ha ampliado el tutorial [Introducción a Factoría de datos de Azure][adfgetstarted] con el tutorial [Uso de Pig y Hive con Factoría de datos de Azure][hivewalkthrough], puede omitir la creación de este servicio vinculado y usar el servicio vinculado que ya existe en ADFTutorialDataFactory.


#### Para utilizar un clúster de HDInsight a petición

1. En el **Portal de Azure clásico**, haga clic en **Crear e implementar** en la página principal de Factoría de datos.
2. En el Editor de Factoría de datos, haga clic en **Nuevo proceso** en la barra de comandos y seleccione **Clúster de HDInsight a petición** en el menú.
2. En el script JSON, haga lo siguiente:
	1. En la propiedad **clusterSize**, especifique el tamaño del clúster de HDInsight.
	3. En la propiedad **timeToLive**, especifique cuánto tiempo el clúster puede estar inactivo antes de que se elimine.
	4. En la propiedad **version**, especifique la versión de HDInsight que quiere usar. Si excluye esta propiedad, se usa la versión más reciente.  
	5. En **linkedServiceName**, especifique el elemento **StorageLinkedService** que creó en el tutorial Introducción.

			{
			  "name": "HDInsightOnDemandLinkedService",
			  "properties": {
			    "type": "HDInsightOnDemand",
			    "typeProperties": {
			      "clusterSize": "1",
			      "timeToLive": "00:05:00",
			      "version": "3.2",
			      "linkedServiceName": "StorageLinkedService"
			    }
			  }
			}

2. Haga clic en **Implementar** en la barra de comandos para implementar el servicio vinculado.

#### Para utilizar su propio clúster de HDInsight:

1. En el **Portal de Azure**, haga clic en **Crear e implementar** en la página principal de Factoría de datos.
2. En el **Editor de Factoría de datos**, haga clic en **Nuevo proceso** en la barra de comandos y seleccione **Clúster de HDInsight a petición** en el menú.
2. En el script JSON, haga lo siguiente:
	1. En la propiedad **clusterUri**, especifique la dirección URL para su HDInsight. Por ejemplo, https://<clustername>.azurehdinsight.net/.     
	2. En la propiedad **UserName**, escriba el nombre del usuario que tiene acceso al clúster de HDInsight.
	3. En la propiedad **Password**, especifique la contraseña del usuario.
	4. En la propiedad **LinkedServiceName**, escriba **StorageLinkedService**. Este es el servicio vinculado que creó en el tutorial Introducción.

2. Haga clic en **Implementar** en la barra de comandos para implementar el servicio vinculado.

### Creación de tablas de salida

1. En el **Editor de Factoría de datos**, haga clic en **Nuevo conjunto de datos** y en **Almacenamiento de blobs de Azure** desde la barra de comandos.
2. Reemplace el script JSON en el panel derecho con el siguiente script JSON:

		{
		  "name": "OutputTableForCustom",
		  "properties": {
		    "type": "AzureBlob",
		    "linkedServiceName": "StorageLinkedService",
		    "typeProperties": {
		      "folderPath": "adftutorial/customactivityoutput/{Slice}",
		      "partitionedBy": [
		        {
		          "name": "Slice",
		          "value": {
		            "type": "DateTime",
		            "date": "SliceStart",
		            "format": "yyyyMMddHH"
		          }
		        }
		      ]
		    },
		    "availability": {
		      "frequency": "Hour",
		      "interval": 1
		    }
		  }
		}

 	La ubicación de salida es **adftutorial/customactivityoutput/YYYYMMDDHH/**, donde YYYYMMDDHH es el año, el mes, el día y la hora del segmento que se está generando. Consulte la [Referencia para desarrolladores][adf-developer-reference] para obtener más información.

2. Haga clic en **Implementar** en la barra de comandos para implementar la tabla.


### Creación y ejecución de una canalización que usa la actividad personalizada

1. En el Editor de Factoría de datos, haga clic en **Nueva canalización** en la barra de comandos. Si no ve el comando, haga clic en **... (puntos suspensivos)** para verlo.
2. Reemplace el script JSON del panel derecho con el siguiente script JSON. Si quiere usar su propio clúster y ha seguido los pasos para crear el servicio vinculado **HDInsightLinkedService**, reemplace **HDInsightOnDemandLinkedService** por **HDInsightLinkedService** en el siguiente JSON.

		{
		  "name": "ADFTutorialPipelineCustom",
		  "properties": {
		    "description": "Use custom activity",
		    "activities": [
		      {
		        "Name": "MyDotNetActivity",
		        "Type": "DotNetActivity",
		        "Inputs": [
		          {
		            "Name": "EmpTableFromBlob"
		          }
		        ],
		        "Outputs": [
		          {
		            "Name": "OutputTableForCustom"
		          }
		        ],
		        "LinkedServiceName": "HDInsightOnDemandLinkedService",
		        "typeProperties": {
		          "AssemblyName": "MyDotNetActivity.dll",
		          "EntryPoint": "MyDotNetActivityNS.MyDotNetActivity",
		          "PackageLinkedService": "StorageLinkedService",
		          "PackageFile": "customactivitycontainer/MyDotNetActivity.zip",
		          "extendedProperties": {
		            "SliceStart": "$$Text.Format('{0:yyyyMMddHH-mm}', Time.AddMinutes(SliceStart, 0))"
		          }
		        },
		        "Policy": {
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

	Reemplace el valor **StartDateTime** por los tres días anteriores al día actual y el valor **EndDateTime** por el día actual. Tanto StartDateTime como EndDateTime deben estar en [formato ISO](http://en.wikipedia.org/wiki/ISO_8601). Por ejemplo: 2014-10-14T16:32:41Z. La tabla de salida está programada para producirse cada día, por lo que habrá tres segmentos producidos.

	Tenga en cuenta lo siguiente:

	- Hay una actividad en la sección de actividades y es de tipo:**DotNetActivity**.
	- Use la misma tabla de entrada **EmpTableFromBlob** que usó en el tutorial Introducción.
	- Use una nueva tabla de salida **OutputTableForCustom** que creará en el paso siguiente.
	- **AssemblyName** se establece en el nombre del DLL: **MyActivities.dll**.
	- **EntryPoint** se establece en establecido en **MyDotNetActivityNS.MyDotNetActivity**.
	- **PackageLinkedService** se establece en **MyBlobStore**, que se creó como parte del tutorial [Introducción a Factoría de datos de Azure][adfgetstarted]. Este almacén de blobs contiene el archivo comprimido de la actividad personalizada.
	- **PackageFile** se establece en **customactivitycontainer/MyDotNetActivity.zip**.

4. Haga clic en **Implementar** en la barra de comandos para implementar la canalización.
8. Compruebe que los archivos de salida se generan en el almacenamiento de blobs del contenedor **adftutorial**.

	![salida de la actividad personalizada][image-data-factory-ouput-from-custom-activity]

9. Si abre el archivo de salida, debería ver un resultado similar al siguiente:

	adftutorial/,emp.txt,2,WORKERNODE0,03/27/2015 19:23:28

	(ubicación del blob), (nombre el blob), (número de líneas en el blob), (nodo en el que se ejecutó la actividad), (marca de fecha y hora)

10.	Use el [Portal de Azure clásico][azure-preview-portal] o los cmdlets de Azure PowerShell para supervisar la factoría de datos, las canalizaciones y los conjuntos de datos. Puede ver mensajes desde **ActivityLogger** en el código de la actividad personalizada en los registros (de forma específica user-0.log) que puede descargar desde el portal o con cmdlets.

	![registros de descarga de la actividad personalizada][image-data-factory-download-logs-from-custom-activity]


Vea [Supervisión y administración de canalizaciones](data-factory-monitor-manage-pipelines.md) para obtener pasos detallados para supervisar los conjuntos de datos y las canalizaciones.

## Actualización de una actividad personalizada
Si actualiza el código de la actividad personalizada, compílelo y cargue el archivo comprimido que contiene los nuevos binarios en el almacenamiento de blobs.

## <a name="AzureBatch"></a> Uso del servicio vinculado de Lote de Azure
> [AZURE.NOTE]Vea [Datos básicos de Lote de Azure][batch-technical-overview] para obtener información general del servicio Lote de Azure. Vea [Introducción a la biblioteca de lote de Azure para .NET][batch-get-started] para empezar a trabajar rápidamente con el servicio Lote de Azure.

Puede ejecutar sus actividades de .NET personalizadas mediante el Lote de Azure como recurso de proceso. Debe crear sus propios grupos de Lote de Azure y especificar el número de máquinas virtuales junto con otras configuraciones. Los grupos de Lote de Azure ofrecen las siguientes características a los clientes:

1. Crear grupos que contienen un núcleo único a miles de núcleos.
2. Escalar automáticamente el número de máquinas virtuales según una fórmula
3. Admitir máquinas virtuales de cualquier tamaño
4. Número configurable de tareas por máquina virtual
5. Poner en cola un número ilimitado de tareas.


Estos son los pasos de alto nivel para usar el servicio vinculado de Lote de Azure en el tutorial que se describe en la sección anterior:

1. Cree una cuenta de Lote de Azure mediante el [Portal de Azure clásico](http://manage.windowsazure.com). Consulte el artículo [Crear y administrar una cuenta de Lote de Azure][batch-create-account] para obtener instrucciones. Anote el nombre y la clave de la cuenta de Lote de Azure.

	También puede usar el cmdlet [New-AzureBatchAccount][new-azure-batch-account] para crear una cuenta de Lote de Azure. Consulte [Uso de Azure PowerShell para administrar la cuenta de Lote de Azure][azure-batch-blog] para obtener instrucciones detalladas sobre cómo utilizar este cmdlet.
2. Cree un grupo de Lote de Azure Puede descargar el código fuente de la [herramienta Explorador de Lote de Azure][batch-explorer], compilarla y usarla (o) usar la [Biblioteca de Lote de Azure para .NET][batch-net-library] para crear un grupo de Lote de Azure. Consulte el [tutorial de ejemplo del Explorador de Lote de Azure][batch-explorer-walkthrough] para obtener instrucciones paso a paso para usar el Explorador de Lote de Azure.

	También puede usar el cmdlet [New-AzureBatchPool][new-azure-batch-pool] para crear una cuenta de Lote de Azure.

2. Cree un servicio vinculado de Lote de Azure con la siguiente plantilla JSON. El Editor de Factoría de datos muestra una plantilla similar para empezar. Especifique el nombre de la cuenta de Lote de Azure, la clave de la cuenta y el nombre del grupo en el fragmento JSON.

		{
		  "name": "AzureBatchLinkedService",
		  "properties": {
		    "type": "AzureBatch",
		    "typeProperties": {
		      "accountName": "<Azure Batch account name>",
			  "batchUri": "https://<region>.batch.azure.com",
		      "accessKey": "<Azure Batch account key>",
		      "poolName": "<Azure Batch pool name>",
		      "linkedServiceName": "<Specify associated storage linked service reference here>"
		    }
		  }
		}

	> [AZURE.IMPORTANT]La **URL** desde la **hoja de cuenta de Lote de Azure** tiene el formato siguiente: accountname.region.batch.azure.com. En la propiedad **batchUri** en JSON, necesitará **quitar "accountname."** de la dirección URL y usar **accountname** en la propiedad JSON **accountName**.

	En la propiedad **poolName**, también puede especificar el identificador del grupo, en lugar del nombre del grupo.

	Consulte el [tema de MSDN del servicio vinculado de Lote de Azure](https://msdn.microsoft.com/library/mt163609.aspx) para obtener una descripción de estas propiedades.

2.  En el Editor de Factoría de datos, abra la definición de JSON de la canalización que creó en el tutorial y reemplace **HDInsightLinkedService** por **AzureBatchLinkedService**.
3.  Puede que desee cambiar las horas de inicio y finalización de la canalización para que pueda probar el escenario con el servicio Lote de Azure.
4.  Puede ver las tareas de Lote de Azure asociadas al procesamiento de los segmentos en el Explorador de Lote de Azure como se muestra en el diagrama siguiente.

	![Tareas de Lote de Azure][image-data-factory-azure-batch-tasks]

> [AZURE.NOTE]El servicio de Factoría de datos no admite una opción a petición para el Lote de Azure como lo hace para HDInsight. Solo puede usar su propio grupo de Lote de Azure en una factoría de datos de Azure.

## Otras referencias

[Actualizaciones de Factoría de datos de Azure: ejecución de actividades de .NET personalizadas de ADF mediante Lote de Azure](http://azure.microsoft.com/blog/2015/05/01/azure-data-factory-updates-execute-adf-custom-net-activities-using-azure-batch/).

[batch-net-library]: ../batch/batch-dotnet-get-started.md
[batch-explorer]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp/BatchExplorer
[batch-explorer-walkthrough]: http://blogs.technet.com/b/windowshpc/archive/2015/01/20/azure-batch-explorer-sample-walkthrough.aspx
[batch-create-account]: ../batch/batch-account-create-portal.md
[batch-technical-overview]: ../batch/batch-technical-overview.md
[batch-get-started]: ../batch/batch-dotnet-get-started.md
[monitor-manage-using-powershell]: data-factory-monitor-manage-using-powershell.md
[adf-tutorial]: data-factory-tutorial.md
[use-custom-activities]: data-factory-use-custom-activities.md
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
[hivewalkthrough]: data-factory-data-transformation-activities.md

[image-data-factory-ouput-from-custom-activity]: ./media/data-factory-use-custom-activities/OutputFilesFromCustomActivity.png

[image-data-factory-download-logs-from-custom-activity]: ./media/data-factory-use-custom-activities/DownloadLogsFromCustomActivity.png

[image-data-factory-azure-batch-tasks]: ./media/data-factory-use-custom-activities/AzureBatchTasks.png

<!---HONumber=AcomDC_1203_2015-->