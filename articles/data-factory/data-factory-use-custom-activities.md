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
	ms.date="04/18/2016"
	ms.author="spelluru"/>

# Uso de actividades personalizadas en una canalización de Factoría de datos de Azure
Hay dos tipos de actividades que puede usar en una canalización de Data Factory de Azure.
 
- [Actividades de movimiento de datos](data-factory-data-movement-activities.md) para mover datos entre [almacenes de datos compatibles](data-factory-data-movement-activities.md#supported-data-stores).
- [Actividades de transformación de datos](data-factory-data-transformation-activities.md) para transformar o procesar datos mediante procesos como HDInsight de Azure, Lote de Azure y Aprendizaje automático de Microsoft Azure. Por ejemplo, Hive de HDInsight y la ejecución de lotes de aprendizaje automático.  

Si precisa mover datos a un almacén de datos (o desde él) que no sea compatible con Data Factory de Azure, puede crear una actividad de .NET personalizada con su propia lógica de traslado de datos y utilizarla en la canalización.

De forma similar, si necesita transformar y procesar datos de algún modo no compatible con Data Factory, puede crear una actividad personalizada con su propia lógica de procesamiento de datos y usarla en la canalización.
 
Puede configurar una actividad de .NET personalizada para ejecutarse mediante un servicio **Lote de Azure** o un clúster de **HDInsight de Azure**.

En el siguiente tutorial se proporcionan instrucciones paso a paso para crear una actividad de .NET personalizada y utilizarla en una canalización. En el tutorial se utiliza el servicio vinculado **Lote de Azure**. Para usar el servicio vinculado de HDInsight de Azure en su lugar, cree un servicio vinculado del tipo **HDInsight** (si usa su propio clúster de HDInsight) o **HDInsightOnDemand** (si quiere que Data Factory cree un clúster de HDInsight cluster a petición) y utilícelo en la sección de actividades de la canalización de JSON (**linkedServiceName**). Consulte la sección [Uso de los servicios vinculados de HDInsight de Azure](#use-azure-hdinsight-linked-services) para obtener más información sobre cómo utilizar HDInsight de Azure para ejecutar la actividad personalizada.


## Tutorial 

### Requisitos previos

- Visual Studio 2012/2013/2015
- Descargue e instale el [SDK de .NET de Azure][azure-developer-center].


### Requisitos previos de Lote de Azure
En el tutorial, ejecutará sus actividades de .NET personalizadas mediante el Lote de Azure como recurso de proceso. Consulte [Datos básicos de Lote de Azure][batch-technical-overview] para obtener información general del servicio Lote de Azure y consulte [Introducción a la biblioteca de .NET de Lote de Azure][batch-get-started] para empezar a trabajar rápidamente con el servicio Lote de Azure.

Para seguir el tutorial, tendrá que crear una cuenta de Lote de Azure con un grupo de VM. Estos son los pasos que se deben seguir:

1. Cree una **cuenta de Lote de Azure** en el [Portal de Azure](http://manage.windowsazure.com). Para obtener instrucciones, consulte el artículo [Creación y administración de una cuenta de Lote de Azure en el Portal de Azure][batch-create-account]. Anote el nombre y la clave de la cuenta de Lote de Azure.

	También puede usar el cmdlet [New-AzureBatchAccount][new-azure-batch-account] para crear una cuenta de Lote de Azure. Consulte [Uso de Azure PowerShell para administrar la cuenta de Lote de Azure][azure-batch-blog] para obtener instrucciones detalladas sobre cómo utilizar este cmdlet.
2. Cree un **grupo de Lote de Azure**.
	1. En el [Portal de Azure](https://portal.azure.com), haga clic en **Examinar** en el menú de la izquierda y después en **Cuentas de Lote**. 
	2. Seleccione la cuenta de Lote de Azure para abrir la hoja **Cuenta de Lote**. 
	3. Haga clic en el icono **Grupos**.
	4. En la hoja **Grupos**, haga clic en el botón Agregar en la barra de herramientas para agregar un grupo.
		1. Especifique un identificador para el grupo (**Identificador del grupo**). Anote el **identificador del grupo**; lo necesitará al crear la solución de Factoría de datos. 
		2. Especifique **Windows Server 2012 R2** en Familia del sistema operativo.
		3. Seleccione un **plan de tarifa de nodos**. 
		3. Escriba **2** como valor en la configuración **Dedicada a destino**.
		4. Escriba **2** como valor en la configuración **Máximo de tareas por nodo**.
	5. Haga clic en **Aceptar** para crear el grupo. 
 
	También puede usar el cmdlet [New-AzureBatchPool](https://msdn.microsoft.com/library/mt628690.aspx) para crear una cuenta de Lote de Azure.

### Pasos de alto nivel 
1.	**Cree una actividad personalizada** para utilizar una canalización de Data Factory. La actividad personalizada de este ejemplo contendrá la lógica de procesamiento o transformación de datos. 
	1.	En Visual Studio, cree un proyecto de la biblioteca de clases .NET, agregue el código para procesar los datos de entrada y compile el proyecto.	
	2.	Comprima todos los archivos binarios y el archivo PDB (opcional) en la carpeta de salida.	
	3.	Cargue el archivo ZIP en Almacenamiento de blobs de Azure. En la sección Creación de la actividad personalizada encontrará más información al respecto. 
2. **Crear una Factoría de datos de Azure que use la actividad personalizada**:
	1. Crear una factoría de datos de Azure.
	2. Cree servicios vinculados.
		1. AzureStorageLinkedService: proporciona credenciales de almacenamiento para tener acceso a los blobs.
		2. AzureBatchLinkedService: especifica Lote de Azure como proceso.
	3. Crear conjuntos de datos.
		1. InputDataset: especifica el contenedor de almacenamiento y la carpeta para los blobs de entrada.
		1. OuputDataset: especifica el contenedor de almacenamiento y la carpeta para los blobs de salida.
	2. Crea una canalización que usa la actividad personalizada.
	3. Ejecutar y probar la canalización.
	4. Depurar la canalización.

## Creación de la actividad personalizada
Para crear una actividad personalizada de .NET, cree un proyecto de la **biblioteca de clases .NET** con una clase que implemente la interfaz **IDotNetActivity**. Esta interfaz solo tiene un método, [Execute](https://msdn.microsoft.com/library/azure/mt603945.aspx), y su firma es:

	public IDictionary<string, string> Execute(
            IEnumerable<LinkedService> linkedServices, 
            IEnumerable<Dataset> datasets, 
            Activity activity, 
            IActivityLogger logger)
        

El método toma cuatro parámetros:

- **linkedServices**. Se trata de una lista enumerable de servicios vinculados que vincula los orígenes de datos de entrada y salida (por ejemplo: Almacenamiento de blobs de Azure) a la factoría de datos. En este ejemplo, hay solo un servicio vinculado del tipo Almacenamiento de Azure que se utilice como entrada y salida. 
- **datasets**. Se trata de una lista enumerable de conjuntos de datos. Este parámetro se puede usar para obtener las ubicaciones y esquemas que definen los conjuntos de datos de entrada y salida.
- **activity**. Este parámetro representa la entidad de proceso actual, en este caso, un Lote de Azure.
- **logger**. El parámetro logger le permite escribir comentarios de depuración que se mostrarán como el registro de "User" en la canalización. 

El método devuelve un diccionario que se puede usar para encadenar actividades personalizadas en el futuro. Esta característica todavía no está implementada, así que solo devuelva un diccionario vacío en el método.

### Procedimiento 
1.	Cree un proyecto de **biblioteca de clases .NET**.
	<ol type="a">
		<li>Inicie <b>Visual Studio 2015</b>, <b>Visual Studio 2013</b> o <b>Visual Studio 2012</b>.</li>
		<li>Haga clic en <b>Archivo</b>, seleccione <b>Nuevo</b> y, luego, haga clic en <b>Proyecto</b>.</li>
		<li>Expanda <b>Plantillas</b> y seleccione <b>Visual C#</b>. En este tutorial se usa C#, pero puede usar cualquier lenguaje .NET para desarrollar la actividad personalizada.</li>
		<li>Seleccione <b>Biblioteca de clases</b> en la lista de tipos de proyecto de la derecha.</li>
		<li>Escriba <b>MyDotNetActivity</b> para <b>Nombre</b>.</li>
		<li>Seleccione <b>C:\ADFGetStarted</b> para <b>Ubicación</b>.</li>
		<li>Haga clic en <b>Aceptar</b> para crear el proyecto.</li>
	</ol>
2.  Haga clic en **Herramientas**, seleccione **Administrador de paquetes NuGet** y haga clic en **Consola del Administrador de paquetes**.
3.	En la Consola del Administrador de paquetes, ejecute el siguiente comando para importar **Microsoft.Azure.Management.DataFactories**.

		Install-Package Microsoft.Azure.Management.DataFactories

4. Importe el paquete NuGet de **Almacenamiento de Azure** en el proyecto.

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

	El ejemplo siguiente cuenta el número de apariciones del término de búsqueda (“Microsoft”) en cada blob asociado con un segmento de datos.

		/// <summary>
        /// Execute method is the only method of IDotNetActivity interface you must implement. 
        /// In this sample, the method invokes the Calculate method to perform the core logic.  
		/// </summary>

        public IDictionary<string, string> Execute(
            IEnumerable<LinkedService> linkedServices,
            IEnumerable<Dataset> datasets,
            Activity activity,
            IActivityLogger logger)
        {
			// to get extended properties (for example: SliceStart)
			DotNetActivity dotNetActivity = (DotNetActivity)activity.TypeProperties;
            string sliceStartString = dotNetActivity.ExtendedProperties["SliceStart"];

			// to log all extended properties			
			IDictionary<string, string> extendedProperties = dotNetActivity.ExtendedProperties;
			logger.Write("Logging extended properties if any...");
			foreach (KeyValuePair<string, string> entry in extendedProperties)
			{
				logger.Write("<key:{0}> <value:{1}>", entry.Key, entry.Value);
			}
		
            // declare types for input and output data stores
            AzureStorageLinkedService inputLinkedService;

            // declare dataset types
            CustomDataset inputLocation;
            AzureBlobDataset outputLocation;

            Dataset inputDataset = datasets.Single(dataset => dataset.Name == activity.Inputs.Single().Name);
            inputLocation = inputDataset.Properties.TypeProperties as CustomDataset;

            foreach (LinkedService ls in linkedServices)
                logger.Write("linkedService.Name {0}", ls.Name);

            // using First method instead of Single since we are using the same 
            // Azure Storage linked service for input and output. 
            inputLinkedService = linkedServices.First(
                linkedService =>
                linkedService.Name ==
                inputDataset.Properties.LinkedServiceName).Properties.TypeProperties
                as AzureStorageLinkedService;

            string connectionString = inputLinkedService.ConnectionString; 

			// To create an input storage client.
            string folderPath = GetFolderPath(inputDataset);
            string output = string.Empty; // for use later.

            // create storage client for input. Pass the connection string.
            CloudStorageAccount inputStorageAccount = CloudStorageAccount.Parse(connectionString);
            CloudBlobClient inputClient = inputStorageAccount.CreateCloudBlobClient();

            // initialize the continuation token before using it in the do-while loop.
            BlobContinuationToken continuationToken = null;
            do
            {   // get the list of input blobs from the input storage client object.
                BlobResultSegment blobList = inputClient.ListBlobsSegmented(folderPath,
                                         true,
                                         BlobListingDetails.Metadata,
                                         null,
                                         continuationToken,
                                         null,
                                         null);
                
                // Calculate method returns the number of occurrences of 
                // the search term (“Microsoft”) in each blob associated
       			// with the data slice. definition of the method is shown in the next step.
 
                output = Calculate(blobList, logger, folderPath, ref continuationToken, "Microsoft");

            } while (continuationToken != null);

            // get the output dataset using the name of the dataset matched to a name in the Activity output collection.
            Dataset outputDataset = datasets.Single(dataset => dataset.Name == activity.Outputs.Single().Name);
            // convert to blob location object.
            outputLocation = outputDataset.Properties.TypeProperties as AzureBlobDataset;

            folderPath = GetFolderPath(outputDataset);

            logger.Write("Writing blob to the folder: {0}", folderPath);

            // create a storage object for the output blob.
            CloudStorageAccount outputStorageAccount = CloudStorageAccount.Parse(connectionString);
            // write the name of the file. 
            Uri outputBlobUri = new Uri(outputStorageAccount.BlobEndpoint, folderPath + "/" + GetFileName(outputDataset));

            logger.Write("output blob URI: {0}", outputBlobUri.ToString());
            // create a new blob and upload the output text.
            CloudBlockBlob outputBlob = new CloudBlockBlob(outputBlobUri, outputStorageAccount.Credentials);
            logger.Write("Writing {0} to the output blob", output);
            outputBlob.UploadText(output);

			// The dictionary can be used to chain custom activities together in the future.
			// This feature is not implemented yet, so just return an empty dictionary.  

            return new Dictionary<string, string>();
        }

9. Agregue los siguientes métodos auxiliares. El método **Execute** invoca estos métodos auxiliares. El método **GetConnectionString** recupera la cadena de conexión de Almacenamiento de Azure y el método **GetFolderPath** recupera la ubicación del blob. Y lo más importante, el método **Calculate** aísla el código que procesa una iteración de cada blob.

        /// <summary>
        /// Gets the folderPath value from the input/output dataset.
		/// </summary>

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

        /// <summary>
        /// Gets the fileName value from the input/output dataset.   
        /// </summary>

        private static string GetFileName(Dataset dataArtifact)
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

            return blobDataset.FileName;
        }

        /// <summary>
        /// Iterates through each blob (file) in the folder, counts the number of instances of search term in the file, 
        /// and prepares the output text that will be written to the output blob. 
        /// </summary>

        public static string Calculate(BlobResultSegment Bresult, IActivityLogger logger, string folderPath, ref BlobContinuationToken token, string searchTerm)
        {
            string output = string.Empty;
            logger.Write("number of blobs found: {0}", Bresult.Results.Count<IListBlobItem>());
            foreach (IListBlobItem listBlobItem in Bresult.Results)
            {
                CloudBlockBlob inputBlob = listBlobItem as CloudBlockBlob;
                if ((inputBlob != null) && (inputBlob.Name.IndexOf("$$$.$$$") == -1))
                {
                    string blobText = inputBlob.DownloadText(Encoding.ASCII, null, null, null);
                    logger.Write("input blob text: {0}", blobText);
                    string[] source = blobText.Split(new char[] { '.', '?', '!', ' ', ';', ':', ',' }, StringSplitOptions.RemoveEmptyEntries);
                    var matchQuery = from word in source
                                     where word.ToLowerInvariant() == searchTerm.ToLowerInvariant()
                                     select word;
                    int wordCount = matchQuery.Count();
                    output += string.Format("{0} occurrences(s) of the search term "{1}" were found in the file {2}.\r\n", wordCount, searchTerm, inputBlob.Name);
                }
            }
            return output;
        }

	El método GetFolderPath devuelve la ruta de acceso a la carpeta a la que apunta el conjunto de datos y el método GetFileName devuelve el nombre del blob o archivo a la que apunta el conjunto de datos. Tenga presente que si tiene definiciones de folderPath mediante variables como {Year}, {Month}, {Day}, etc., el método devuelve la cadena tal cual, sin sustituir dichas variables por los valores de tiempo de ejecución. Consulte la sección [Acceso a las propiedades extendidas](#access-extended-properties) para obtener más información sobre cómo acceder a SliceStart, SliceEnd, etc.
	
		    "name": "InputDataset",
		    "properties": {
		        "type": "AzureBlob",
		        "linkedServiceName": "AzureStorageLinkedService",
		        "typeProperties": {
		            "fileName": "file.txt",
		            "folderPath": "mycontainer/inputfolder/",
	
	El método Calculate calcula el número de instancias de la palabra clave Microsoft en los archivos de entrada (los blobs de la carpeta). El término de búsqueda ("Microsoft") está codificado de forma rígida en el código.

10. Compile el proyecto. Haga clic en **Compilar** en el menú y haga clic en **Compilar solución**.
11. Inicie el **Explorador de Windows** y vaya a la carpeta **bin\\debug** o **bin\\release** según el tipo de compilación.
12. Cree un archivo comprimido **MyDotNetActivity.zip** que contenga todos los archivos binarios en la carpeta <project folder>\\bin\\Debug. Puede incluir el archivo **MyDotNetActivity.pdb** para obtener detalles adicionales, como el número de línea en el código fuente que causó el problema en caso de error. Todos los archivos incluidos en el archivo ZIP de la actividad personalizada deben estar en el **nivel superior**; no debe haber subcarpetas.

	![Archivos de salida binarios](./media/data-factory-use-custom-activities/Binaries.png)
13. Cargue **MyDotNetActivity.zip** como un blob en el contenedor de blobs: **customactvitycontainer** en el Almacenamiento de blobs de Azure que usa el servicio vinculado **AzureStorageLinkedService** en **ADFTutorialDataFactory**. Cree el contenedor de blobs **customactivitycontainer** si no existe.

> [AZURE.NOTE] Si agrega este proyecto de actividad de .NET a una solución en Visual Studio que contenga un proyecto de Data Factory, y agrega una referencia al proyecto de actividad .NET del proyecto de la aplicación de Data Factory, no tendrá que realizar los últimos dos pasos de crear manualmente el archivo ZIP y cargarlo al Almacenamiento de blobs de Azure. Al publicar las entidades de la factoría de datos con Visual Studio el proceso de publicación realizar automáticamente estos pasos. Consulte los artículos [Compilación de la primera canalización mediante Visual Studio](data-factory-build-your-first-pipeline-using-vs.md) y [Tutorial: Creación y supervisión de una factoría de datos mediante Visual Studio](data-factory-get-started-using-vs.md) para aprender a crear y publicar entidades de una factoría de datos mediante Visual Studio.

### Método Execute

En esta sección se proporcionan más detalles y notas sobre el código del método **Execute**.
 
1. Los miembros para procesar una iteración en la colección de entrada se encuentran en el espacio de nombres [Microsoft.WindowsAzure.Storage.Blob](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.storage.blob.aspx). El procesamiento de una iteración en la colección de blobs requiere el uso de la clase **BlobContinuationToken**. En esencia, es preciso usar un bucle do-while con el token como mecanismo para salir del bucle. Para más información, consulte [Uso del almacenamiento de blobs de .NET](../storage/storage-dotnet-how-to-use-blobs.md). Aquí se muestra un bucle básico:

		// Initialize the continuation token.
		BlobContinuationToken continuationToken = null;
		do
		{   
			// Get the list of input blobs from the input storage client object.
			BlobResultSegment blobList = inputClient.ListBlobsSegmented(folderPath,
    					              true,
    	                              BlobListingDetails.Metadata,
    	                              null,
    	                              continuationToken,
    	                              null,
    	                              null);
			// Return a string derived from parsing each blob.
    		output = Calculate(blobList, logger, folderPath, ref continuationToken, "Microsoft");
		} while (continuationToken != null);

	Para más información, consulte la documentación del método [ListBlobsSegmented](https://msdn.microsoft.com/library/jj717596.aspx).

2.	Lógicamente, el código para trabajar en el conjunto de blobs lógicamente está dentro del bucle do-while. En el método **Execute**, el bucle do-while pasa la lista de blobs a un método denominado **Calculate**. El método devuelve una variable de cadena denominada **output** que es el resultado de haber procesado una iteración en todos los blobs del segmento.

	Devuelve el número de apariciones del término de búsqueda (**Microsoft**) en el blob que se pasa al método **Calculate**.

			output += string.Format("{0} occurrences of the search term "{1}" were found in the file {2}.\r\n", wordCount, searchTerm, inputBlob.Name);

3.	Una vez que el método **Calculate** ha terminado el trabajo, debe escribirse en un blob nuevo. Por consiguiente, por cada conjunto de blobs que se procesa, se puede escribir un blob nuevo con los resultados. Para escribir en un blob nuevo, primero es preciso buscar el conjunto de datos de salida.

			// Get the output dataset using the name of the dataset matched to a name in the Activity output collection.
			Dataset outputDataset = datasets.Single(dataset => dataset.Name == activity.Outputs.Single().Name);

			// Convert to blob location object.
			outputLocation = outputDataset.Properties.TypeProperties as AzureBlobDataset;

4.	El código también llama a un método auxiliar, **GetFolderPath**, para recuperar la ruta de acceso de la carpeta (el nombre del contenedor de almacenamiento).
 
			folderPath = GetFolderPath(outputDataset);

	El método **GetFolderPath** convierte el objeto DataSet en una clase AzureBlobDataSet, que tiene una propiedad denominada FolderPath.
			
			AzureBlobDataset blobDataset = dataArtifact.Properties.TypeProperties as AzureBlobDataset;
			
			return blobDataset.FolderPath;

5.	El código llama al método **GetFileName** para recuperar el nombre de archivo (nombre del blob). El código es similar al código anterior para obtener la ruta de acceso de la carpeta.

			AzureBlobDataset blobDataset = dataArtifact.Properties.TypeProperties as AzureBlobDataset;

			return blobDataset.FileName;

6.	El nombre del archivo se escribe mediante la creación de un nuevo objeto URI. El constructor URI usa la propiedad **BlobEndpoint** para devolver el nombre del contenedor. Tanto la ruta de la carpeta como el nombre de archivo se agregan al construir el identificador URI del blob de salida.

			// Write the name of the file. 
			Uri outputBlobUri = new Uri(outputStorageAccount.BlobEndpoint, folderPath + "/" + GetFileName(outputDataset));

7.	Se ha escrito el nombre del archivo y ahora puede escribir la cadena de salida desde el método Calculate en un blob nuevo:

			// Create a new blob and upload the output text.
			CloudBlockBlob outputBlob = new CloudBlockBlob(outputBlobUri, outputStorageAccount.Credentials);
			logger.Write("Writing {0} to the output blob", output);
			outputBlob.UploadText(output);

## Creación de Data Factory mediante el Portal de Azure

En la sección **Creación de la actividad personalizada**, se creó una actividad personalizada y se cargó el archivo ZIP con archivos binarios y el archivo PDB en un contenedor de blobs de Azure. En esta sección, creará una **factoría de datos** de Azure con una **canalización** que usa la **actividad personalizada**.
 
El conjunto de datos de entrada de la actividad personalizada representa los blobs (archivos) de la carpeta de entrada (mycontainer\\inputfolder) del almacenamiento de blobs. El conjunto de datos de salida de la actividad representa los blobs de salida de la carpeta de salida (mycontainer\\outputfolder) del almacenamiento de blobs.

Cree un archivo llamado **file.txt** con el siguiente contenido y cárguelo a **miContenedor\\carpetaDeEntrada** (miContenedor es el nombre del contenedor de blobs de Azure y carpetaDeEntrada es el de la carpeta ubicada en ese contenedor).

	test custom activity Microsoft test custom activity Microsoft

La carpeta de entrada corresponde a un segmento de Factoría de datos de Azure, aunque la carpeta contenga dos, o más, archivos. Cuando la canalización procesa cada segmento, la actividad personalizada procesa una iteración en todos los blobs de la carpeta de entrada de dicho segmento.

Verá un archivo de salida en la carpeta mycontainer\\output con una o varias líneas (tantas como blobs haya en la carpeta de entrada):
 
	2 occurrences(s) of the search term "Microsoft" were found in the file inputfolder/2015-11-16-00/file.txt.


Estos son los pasos que realizará en esta sección:

1. Crear una **factoría de datos**.
2. Los **servicios vinculados** del grupo de máquinas virtuales de Lote de Azure en el que se ejecutará la actividad personalizada y el Almacenamiento de Azure que contiene los blobs de entrada o salida. 
2. Los **conjuntos de datos** de entrada y salida que representan las entradas y salidas de la actividad personalizada. 
3. La **canalización** que utiliza la actividad personalizada.
4. **Data Factory**. Creará una al publicar estas entidades en Azure. 

> [AZURE.NOTE] Cree el archivo **file.txt** y cárguelo a un contenedor de blobs, si no lo ha hecho aún. Consulte las instrucciones anteriores.

### Paso 1: Creación de la factoría de datos

1.	Tras iniciar sesión en el Portal de Azure, siga estos pasos:
	1.	Haga clic en **NUEVO** en el menú de la izquierda.
	2.	Haga clic en **Datos y análisis** en la hoja **Nuevo**.
	3.	Haga clic en **Factoría de datos** en la hoja **Análisis de datos**.
2.	En la hoja **Nueva factoría de datos**, escriba **CustomActivityFactory** en el campo Nombre. El nombre del generador de datos de Azure debe ser único global. Si recibe el error **El nombre "CustomActivityFactory" de factoría de datos no está disponible**, cambie el nombre de la factoría de datos (por ejemplo, **suNombreCustomActivityFactory**) e intente crearla de nuevo.
3.	Haga clic en **NOMBRE DEL GRUPO DE RECURSOS** y seleccione un grupo de recursos existente, o buen cree uno nuevo. 
4.	Compruebe que usa la **suscripción** y **región** correctas en las que desea que se cree la factoría de datos. 
5.	Haga clic en **Crear** en la hoja **Nueva fábrica de datos**.
6.	Verá que la factoría de datos se crea en el **Panel** del Portal de Azure.
7.	Una vez que la factoría de datos se haya creado correctamente, verá la hoja Factoría de datos, que muestra el contenido de la misma.

### Paso 2: Creación de servicios vinculados

Los servicios vinculados vinculan almacenes de datos o servicios de proceso con una factoría de datos de Azure. En este paso, vinculará su cuenta de Almacenamiento de Azure y la cuenta de Lote de Azure con su Data Factory.

#### Creación de un servicio vinculado de Almacenamiento de Azure

1.	Haga clic en el icono **Crear e implementar** de la hoja **FACTORÍA DE DATOS** de **CustomActivityFactory**. Esto inicia el Editor de la Factoría de datos.
2.	Haga clic en **Nuevo almacén de datos** en la barra de comandos y elija **Almacenamiento de Azure**. Debería ver el script JSON para crear un servicio vinculado de Almacenamiento de Azure en el editor.
3.	Reemplace **account name** por el nombre de la cuenta de Almacenamiento de Azure y **account key** por la clave de acceso de la cuenta de Almacenamiento de Azure. Para aprender a obtener una clave de acceso de almacenamiento, consulte [Acerca de las cuentas de almacenamiento de Azure](../storage/storage-create-storage-account.md#view-copy-and-regenerate-storage-access-keys).
4.	Haga clic en **Implementar** en la barra de comandos para implementar el servicio vinculado.

#### Creación del servicio vinculado de Lote de Azure

2. En el Editor de Data Factory, haga clic en **Nuevo proceso** en la barra de comandos y seleccione **Lote de Azure** en el menú.
3. En el script JSON, haga lo siguiente:
	1. Especifique el nombre de cuenta de Lote de Azure en la propiedad **accountName**. La **URL** de la **hoja de la cuenta de Lote de Azure** presenta el siguiente formato: http://**accountname**.region.batch.azure.com. En la propiedad **batchUri** del script JSON, tendrá que **quitar "accountname."** de la dirección URL y usar **accountname** en la propiedad de JSON **accountName**.
	2. Especifique la clave de cuenta de Lote de Azure en la propiedad **accessKey**. 
	3. Especifique el nombre del grupo que creó como parte de los requisitos previos en la propiedad **poolName**. También puede especificar el id. del grupo, en lugar del nombre.
	4. Especifique el URI de Lote de Azure en la propiedad **batchUri**. La **URL** de la **hoja de la cuenta de Lote de Azure** presenta el siguiente formato: http://accountname.region.batch.azure.com. En la propiedad **batchUri** del script JSON, tendrá que **quitar "accountname."** de la dirección URL y usar **accountname** en la propiedad de JSON **accountName**.
	5. Especifique el servicio **AzureStorageLinkedService** en la propiedad **linkedServiceName**.
		
			{
			  "name": "AzureBatchLinkedService",
			  "properties": {
			    "type": "AzureBatch",
			    "typeProperties": {
			      "accountName": "myazurebatchaccount",
				  "batchUri": "https://westus.batch.azure.com",
			      "accessKey": "batchaccountkey>",
			      "poolName": "myazurebatchpool",
			      "linkedServiceName": "AzureStorageLinkedService"
			    }
			  }
			}

	> [AZURE.IMPORTANT] La **dirección URL** de la **hoja de la cuenta de Lote de Azure** tiene el formato siguiente: accountname.region.batch.azure.com. En la propiedad **batchUri** del script JSON, tendrá que **quitar "accountname."** de la dirección URL y usar **accountname** en la propiedad de JSON **accountName**.

	En la propiedad **poolName**, también puede especificar el identificador del grupo, en lugar del nombre del grupo.

	> [AZURE.NOTE] El servicio de Factoría de datos no admite una opción a petición para el Lote de Azure como lo hace para HDInsight. Solo puede usar su propio grupo de Lote de Azure en una factoría de datos de Azure.
	
### Paso 3: Creación de conjuntos de datos
En este paso, creará conjuntos de datos que representen los datos de entrada y salida.

#### Creación de un conjunto de datos de entrada
1.	En el **Editor** de Factoría de datos, haga clic en el botón **Nuevo conjunto de datos** en la barra de herramientas y haga clic en **Almacenamiento de blobs de Azure** en el menú desplegable.
2.	Reemplace el script JSON del panel derecho por el siguiente fragmento de código JSON:

			{
			    "name": "InputDataset",
			    "properties": {
			        "type": "AzureBlob",
			        "linkedServiceName": "AzureStorageLinkedService",
			        "typeProperties": {
			            "folderPath": "adftutorial/customactivityinput/",
			            "format": {
			                "type": "TextFormat"
			            }
			        },
			        "availability": {
			            "frequency": "Hour",
			            "interval": 1
			        },
			        "external": true,
			        "policy": {}
			    }
			}

	En este tutorial, creará posteriormente una canalización cuya hora de inicio es: 2015-11-hora 16T00:00:00Z y cuya hora de finalización es: 2015-11-16T05:00:00Z. Está programada para generar datos cada hora, por lo que habrá cinco segmentos de entrada o salida (entre **00**: 00:00 -> **05**: 00:00).

	El **frecuencia** e **intervalo** del conjunto de datos de entrada se establece en **Hora** y **1**, lo que significa que el segmento de entrada está disponible cada hora. En este ejemplo, es el mismo archivo (file.txt) de la carpeta de entrada.

	Estas son las horas de inicio de cada segmento, que se representa mediante la variable de sistema SliceStart en el fragmento de código JSON anterior.

	
3.	Haga clic en **Implementar** en la barra de herramientas para crear e implementar **InputDataset**. Confirme que aparece el mensaje **TABLA CREADA CORRECTAMENTE** en la barra de título del Editor.


#### Crear un conjunto de datos de salida

1. En el **Editor de Factoría de datos**, haga clic en **Nuevo conjunto de datos** y en **Almacenamiento de blobs de Azure** desde la barra de comandos.
2. Reemplace el script JSON en el panel derecho con el siguiente script JSON:

		{
		    "name": "OutputDataset",
		    "properties": {
		        "type": "AzureBlob",
		        "linkedServiceName": "AzureStorageLinkedService",
		        "typeProperties": {
		            "fileName": "{slice}.txt",
		            "folderPath": "adftutorial/customactivityoutput",
		            "partitionedBy": [
		                {
		                    "name": "slice",
		                    "value": {
		                        "type": "DateTime",
		                        "date": "SliceStart",
		                        "format": "yyyy-MM-dd-HH"
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

 	La ubicación de salida es **adftutorial/customactivityoutput/** y el nombre del archivo de salida es yyyy-MM-dd-HH.txt, donde yyyy-MM-dd-HH es el año, el mes, el día y la hora del segmento que se está generando. Consulte la [Referencia para desarrolladores][adf-developer-reference] para obtener más información.

	Se genera un blob o archivo de salida para cada segmento de entrada. Así es cómo se asigna el nombre al archivo de salida de cada segmento. Todos los archivos de salida se generan en una carpeta de salida: **adftutorial\\customactivityoutput**.

	| Segmento | Hora de inicio | Archivo de salida |
	| :---- | :--------- | :---------- | 
	| 1 | 2015-11-16T00:00:00 | 2015-11-16-00.txt |
	| 2 | 2015-11-16T01:00:00 | 2015-11-16-01.txt |
	| 3 | 2015-11-16T02:00:00 | 2015-11-16-02.txt |
	| 4 | 2015-11-16T03:00:00 | 2015-11-16-03.txt |
	| 5 | 2015-11-16T04:00:00 | 2015-11-16-04.txt |

	Recuerde que todos los archivos de una carpeta de entrada forman parte de un segmento con las horas de inicio mencionadas anteriormente. Cuando se procesa este segmento, la actividad personalizada examinan todos los archivos y produce una línea en el archivo de salida con el número de apariciones del término de búsqueda ("Microsoft"). Si hay tres archivos en inputfolder, habrá tres líneas en el archivo de salida para cada segmento horario: 2015-11-16-00.txt 2015-11-16:01:00:00.txt, etc.


2. Haga clic en **Implementar** en la barra de comandos para implementar **OutputDataset**.


### Creación y ejecución de una canalización que usa la actividad personalizada

1. En el Editor de Factoría de datos, haga clic en **Nueva canalización** en la barra de comandos. Si no ve el comando, haga clic en **... (puntos suspensivos)** para verlo.
2. Reemplace el script JSON del panel derecho con el siguiente script JSON. 

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
		            "Name": "InputDataset"
		          }
		        ],
		        "Outputs": [
		          {
		            "Name": "OutputDataset"
		          }
		        ],
		        "LinkedServiceName": "AzureBatchLinkedService",
		        "typeProperties": {
		          "AssemblyName": "MyDotNetActivity.dll",
		          "EntryPoint": "MyDotNetActivityNS.MyDotNetActivity",
		          "PackageLinkedService": "AzureStorageLinkedService",
		          "PackageFile": "customactivitycontainer/MyDotNetActivity.zip",
		          "extendedProperties": {
		            "SliceStart": "$$Text.Format('{0:yyyyMMddHH-mm}', Time.AddMinutes(SliceStart, 0))"
		          }
		        },
		        "Policy": {
		          "Concurrency": 2,
		          "ExecutionPriorityOrder": "OldestFirst",
		          "Retry": 3,
		          "Timeout": "00:30:00",
		          "Delay": "00:00:00"
		        }
		      }
		    ],
    		"start": "2015-11-16T00:00:00Z",
			"end": "2015-11-16T05:00:00Z",
		    "isPaused": false
		  }
		}

	Tenga en cuenta lo siguiente:

	- **Concurrency** está establecida en **2** para que dos máquinas virtuales del grupo de Lote de Azure procesen dos segmentos en paralelo.
	- Hay una actividad en la sección de actividades y es de tipo:**DotNetActivity**.
	- **AssemblyName** se establece en el nombre del DLL: **MyActivities.dll**.
	- **EntryPoint** se establece en establecido en **MyDotNetActivityNS.MyDotNetActivity**.
	- **PackageLinkedService** se establece en **AzureStorageLinkedService**, que apunta al Almacenamiento de blobs que contiene el archivo zip de la actividad personalizada. Si usa diferentes cuentas de Almacenamiento de Azure para los archivos de entrada y salida y el archivo ZIP de actividad personalizada, tendrá que crear otro servicio vinculado de Almacenamiento de Azure. Este artículo se asume que usa la misma cuenta de Almacenamiento de Azure.
	- **PackageFile** se establece en **customactivitycontainer/MyDotNetActivity.zip**. Está en el formato <contenedorDelZIP>/<nombreDelZIP.zip>.
	- La actividad personalizada toma **InputDataset** como entrada y **OutputDataset** como salida.
	- La propiedad linkedServiceName de la actividad personalizada apunta a **HDInsightLinkedService**, que indica a Factoría de datos de Azure que la actividad personalizada debe ejecutarse en un clúster de HDInsight de Azure.
	- La propiedad **isPaused** propiedad se establece en **false** de forma predeterminada. La canalización se ejecuta inmediatamente en este ejemplo, ya que los segmentos se inician en el pasado. Esta propiedad se puede establecer en true para pausar la canalización y se puede volver a establecer en false para reiniciarla. 
	- Hay una diferencia de **cinco** horas entre la hora de **inicio** y las horas de **finalización** y los segmentos se producen cada hora, por lo que la canalización produce cinco segmentos. 

4. Haga clic en **Implementar** en la barra de comandos para implementar la canalización.

### Supervisar la canalización
 
8. En la hoja Factoría de datos del Portal de Azure, haga clic en **Diagrama**.
	
	![Icono Diagrama](./media/data-factory-use-custom-activities/DataFactoryBlade.png)
 
9. En la vista de diagrama, haga clic en OutputDataset.
 
	![Vista de diagrama](./media/data-factory-use-custom-activities/diagram.png)

10. Si ya se han producido, debería ver que los cinco segmentos están en estado Listo.

	![Segmentos de salida](./media/data-factory-use-custom-activities/OutputSlices.png)
	
12. Compruebe que los archivos de salida se generan en el almacenamiento de blobs del contenedor **adftutorial**.

	![salida de la actividad personalizada][image-data-factory-ouput-from-custom-activity]

9. Si abre el archivo de salida, debería ver un resultado similar al siguiente:

	2 occurrences(s) of the search term "Microsoft" were found in the file inputfolder/2015-11-16-00/file.txt.

10.	Use el [Portal de Azure][azure-preview-portal] o los cmdlets de Azure PowerShell para supervisar su factoría de datos, canalizaciones y conjuntos de datos. Puede ver mensajes desde **ActivityLogger** en el código de la actividad personalizada en los registros (de forma específica user-0.log) que puede descargar desde el portal o con cmdlets.

	![registros de descarga de la actividad personalizada][image-data-factory-download-logs-from-custom-activity]


Consulte [Supervisión y administración de canalizaciones de la Factoría de datos de Azure](data-factory-monitor-manage-pipelines.md) para obtener información detallada sobre los pasos que hay que seguir para supervisar los conjuntos de datos y las canalizaciones.

El servicio Data Factory crea un trabajo en Lote de Azure con el nombre: **adf-<pool name>:job-xxx**. Se crea una tarea para cada ejecución de actividad de un segmento. Si hay diez segmentos listos para ser procesados, se crean diez tareas en este trabajo. Si el grupo tiene varios nodos de procesos, puede haber más de un segmento en ejecución en paralelo. También puede haber más de un segmento en ejecución en el mismo proceso si el número máximo de tareas se establece un valor mayor que 1.

	
![Tareas del Explorador de Lote](./media/data-factory-use-custom-activities/BatchExplorerTasks.png)

> [AZURE.NOTE] Descargue el código fuente de la [herramienta Explorador de lote de Azure][batch-explorer], compílelo y úselo para crear y supervisar los grupos de Lote. Consulte el [tutorial de ejemplo del Explorador de Lote de Azure][batch-explorer-walkthrough] para obtener instrucciones paso a paso para usar el Explorador de Lote de Azure.

![Factoría de datos y Lote](./media/data-factory-use-custom-activities/DataFactoryAndBatch.png)

Puede ver las tareas de Lote de Azure asociadas al procesamiento de los segmentos en el Explorador de Lote de Azure como se muestra en el diagrama siguiente.

![Tareas de Lote de Azure][image-data-factory-azure-batch-tasks]


### Depuración de la canalización
La depuración se compone de varias técnicas básicas:

1.	Si el segmento de entrada no está establecido en **Listo**, confirme que la estructura de la carpeta de entrada es correcta y que **file.txt** se encuentra en las carpetas de entrada. 
2.	En el método **Execute** de la actividad personalizada, use el objeto **IActivityLogger** para registrar información que lo ayudará a solucionar problemas. Los mensajes registrados se mostrarán en los archivos de registro del usuario (uno o varios archivos llamados user-0.log, user-1.log, user-2.log, etc.). 

	En la hoja **OutputDataset**, haga clic en el segmento para ver la hoja **SEGMENTO DE DATOS** de dicho segmento. Verá **ejecuciones de actividad** para ese segmento. Debería ver una ejecución de actividad del segmento. Si hace clic en Ejecutar en la barra de comandos, podrá iniciar otra ejecución de actividad en el mismo segmento.

	Al hacer clic en la ejecución de actividad, verá la hoja **DETALLES DE LA EJECUCIÓN DE ACTIVIDAD** con una lista de archivos de registro. Los mensajes registrados se verán en el archivo user\_0.log. Si se produce un error, verá tres ejecuciones de actividad, ya que el número de reintentos está establecido en 3 en la canalización o actividad JSON. Al hacer clic en la ejecución de actividad, verá los archivos de registro que puede revisar para solucionar el error.

	En la lista de archivos de registro, haga clic en **user-0.log**. En el panel derecho, se encuentran los resultados del uso del método **IActivityLogger.Write**. Si no ve todos los mensajes, compruebe si tiene más archivos de registro llamados user\_1.log, user\_2.log, etc. De lo contrario, es posible que el código haya generado algún error tras el último mensaje registrado.

	En **0.log system** también se encuentran todas las excepciones y mensajes de error del sistema.

3.	Incluya el archivo **PDB** en el archivo ZIP para que se ofrezca información como la **pila de llamadas** cuando se proporcionen detalles sobre un error que se haya producido.
4.	Todos los archivos incluidos en el archivo zip de la actividad personalizada deben estar en el **nivel superior**; no debe haber subcarpetas.
5.	Asegúrese de que en **assemblyName** (MyDotNetActivity.dll), **entryPoint**(MyDotNetActivityNS.MyDotNetActivity), **packageFile** (customactivitycontainer/MyDotNetActivity.zip) y **packageLinkedService** (debe apuntar al Almacenamiento de blobs de Azure que contiene el archivo ZIP) se han seleccionado los valores correctos. 
6.	Si corrigió algún error y desea volver a procesar el segmento, haga clic con el botón derecho en el segmento, en la hoja **OutputDataset**, y haga clic en **Run** (Ejecutar). 


## Actualización de la actividad personalizada
Si actualiza el código de la actividad personalizada, compílelo y cargue el archivo comprimido que contiene los nuevos binarios en el almacenamiento de blobs.

## Copia o traslado de datos 
La actividad de copia copia los datos de un almacén de datos de **origen** a un almacén de datos **receptor**. Consulte [Almacenes de datos que se admiten](data-factory-data-movement-activities.md#supported-data-stores) para ver la lista de almacenes de datos admitidos como orígenes y receptores de la actividad de copia.

Si tiene que mover datos a un almacén de datos (o desde él) que no sea compatible con la **actividad de copia**, puede utilizar la **actividad personalizada** en Data Factory con su propia lógica para copiar o mover los datos. Consulte [HTTP Data Downloader Sample](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/HttpDataDownloaderSample) (Ejemplo de descargador de datos HTTP) en GitHub.

## Aislamiento de AppDomain 
Consulte el [ejemplo de AppDomain cruzado](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/CrossAppDomainDotNetActivitySample), que muestra cómo crear una actividad de .NET personalizada para Data Factory de Azure que no esté restringida a las versiones de ensamblado utilizadas por el iniciador de Data Factory de Azure (p. ej., WindowsAzure.Storage v4.3.0, Newtonsoft.Json v6.0.x, etc.).

## Acceso a las propiedades extendidas
Puede declarar propiedades extendidas en la actividad de JSON como se muestra a continuación:

	"typeProperties": {
	  "AssemblyName": "MyDotNetActivity.dll",
	  "EntryPoint": "MyDotNetActivityNS.MyDotNetActivity",
	  "PackageLinkedService": "AzureStorageLinkedService",
	  "PackageFile": "customactivitycontainer/MyDotNetActivity.zip",
	  "extendedProperties": {
	    "SliceStart": "$$Text.Format('{0:yyyyMMddHH-mm}', Time.AddMinutes(SliceStart, 0))",
		"DataFactoryName": "CustomActivityFactory"
	  }
	},

En el ejemplo anterior, hay dos propiedades extendidas: **SliceStart** y **DataFactoryName**. El valor de SliceStart se basa en la variable del sistema SliceStart. Consulte el artículo sobre las [variables del sistema](data-factory-scheduling-and-execution.md#data-factory-system-variables) para ver una lista de las variables del sistema admitidas. El valor de DataFactoryName está codificado de forma rígida para "CustomActivityFactory".

Para acceder a estas propiedades extendidas en el método **Execute**, use código similar al siguiente:

	// to get extended properties (for example: SliceStart)
	DotNetActivity dotNetActivity = (DotNetActivity)activity.TypeProperties;
	string sliceStartString = dotNetActivity.ExtendedProperties["SliceStart"];

	// to log all extended properties                               
    IDictionary<string, string> extendedProperties = dotNetActivity.ExtendedProperties;
    logger.Write("Logging extended properties if any...");
    foreach (KeyValuePair<string, string> entry in extendedProperties)
    {
    	logger.Write("<key:{0}> <value:{1}>", entry.Key, entry.Value);
	}

## Característica de escalación automática de Lote de Azure
También puede crear un grupo de Lote de Azure con la característica **Autoescala**. Por ejemplo, podría crear un grupo de Lote de Azure con 0 máquinas virtuales dedicadas y una fórmula de escalado automático basada en el número de tareas pendientes:
 
	pendingTaskSampleVector=$PendingTasks.GetSample(600 * TimeInterval_Second);$TargetDedicated = max(pendingTaskSampleVector);

Para más detalles, consulte [Escalación automática de los nodos de ejecución en un grupo de Lote de Azure](../batch/batch-automatic-scaling.md).

Si el grupo usa el valor predeterminado de la propiedad [autoScaleEvaluationInterval](https://msdn.microsoft.com/library/azure/dn820173.aspx), el servicio Lote puede tardar de 15 a 30 minutos en preparar la máquina virtual antes de ejecutar la actividad personalizada. Si el grupo usa otro valor de autoScaleEvaluationInterval diferente, el servicio Lote podría tardar el valor de autoScaleEvaluationInterval más 10 minutos.

## Uso de los servicios vinculados de HDInsight de Azure
En el tutorial, ha utilizado el proceso Lotes de Azure para ejecutar la actividad personalizada. También puede utilizar su propio clúster de HDInsight o hacer que Data Factory cree un clúster de HDInsight a petición y configurar la actividad personalizada para que se ejecute en el clúster de HDInsight. Estos son los pasos generales para usar un clúster de HDInsight.

1. Cree un servicio vinculado de HDInsight de Azure.   
2. Utilice el servicio vinculado de HDInsight en lugar de **AzureBatchLinkedService** en la canalización de JSON. 

Se recomienda cambiar las horas de **inicio** y **finalización** de la canalización para poder probar el escenario con el servicio de HDInsight de Azure.

#### Creación de un servicio vinculado de HDInsight de Azure 
El servicio Factoría de datos de Azure admite la creación de un clúster a petición y usarlo para procesar la entrada para generar datos de salida. También puede utilizar su propio clúster para realizar la misma tarea. Cuando se utiliza el clúster de HDInsight a petición, se crea un clúster para cada sector. Mientras que, si utiliza su propio clúster de HDInsight, el clúster está preparado para procesar el sector inmediatamente. Por lo tanto, cuando utilice el clúster a petición, es posible que no vea los datos de salida tan rápido como cuando utilice su propio clúster.

> [AZURE.NOTE] En tiempo de ejecución, una instancia de una actividad de .NET solo se ejecuta en un nodo de trabajo en el clúster de HDInsight; no se puede escalar para que se ejecute en varios nodos. Se pueden ejecutar en paralelo varias instancias de actividad de .NET en distintos nodos del clúster de HDInsight.

##### Para utilizar un clúster de HDInsight a petición

1. En el **Portal de Azure**, haga clic en **Crear e implementar** en la página principal de Factoría de datos.
2. En el Editor de Factoría de datos, haga clic en **Nuevo proceso** en la barra de comandos y seleccione **Clúster de HDInsight a petición** en el menú.
2. En el script JSON, haga lo siguiente:
	1. En la propiedad **clusterSize**, especifique el tamaño del clúster de HDInsight.
	3. En la propiedad **timeToLive**, especifique cuánto tiempo el clúster puede estar inactivo antes de que se elimine.
	4. En la propiedad **version**, especifique la versión de HDInsight que quiere usar. Si excluye esta propiedad, se usa la versión más reciente.  
	5. En **linkedServiceName**, especifique el elemento **AzureStorageLinkedService** que creó en el tutorial de introducción.

			{
			    "name": "HDInsightOnDemandLinkedService",
			    "properties": {
			        "type": "HDInsightOnDemand",
			        "typeProperties": {
			            "clusterSize": 4,
			            "timeToLive": "00:05:00",
			            "osType": "Windows",
			            "linkedServiceName": "AzureStorageLinkedService",
			        }
			    }
			}

2. Haga clic en **Implementar** en la barra de comandos para implementar el servicio vinculado.

##### Para utilizar su propio clúster de HDInsight:

1. En el **Portal de Azure**, haga clic en **Crear e implementar** en la página principal de Factoría de datos.
2. En el **Editor de Factoría de datos**, haga clic en **Nuevo proceso** en la barra de comandos y seleccione **Clúster de HDInsight a petición** en el menú.
2. En el script JSON, haga lo siguiente:
	1. En la propiedad **clusterUri**, especifique la dirección URL para su HDInsight. Por ejemplo, https://<clustername>.azurehdinsight.net/.     
	2. En la propiedad **UserName**, escriba el nombre del usuario que tiene acceso al clúster de HDInsight.
	3. En la propiedad **Password**, especifique la contraseña del usuario.
	4. En la propiedad **LinkedServiceName**, escriba **AzureStorageLinkedService**. Este es el servicio vinculado que creó en el tutorial Introducción.

2. Haga clic en **Implementar** en la barra de comandos para implementar el servicio vinculado.

Consulte [Servicios vinculados de procesos](data-factory-compute-linked-services.md) para más información.

En el **JSON de la canalización**, utilice el servicio vinculado de HDInsight (a petición o suyo propio):

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
	            "Name": "InputDataset"
	          }
	        ],
	        "Outputs": [
	          {
	            "Name": "OutputDataset"
	          }
	        ],
	        "LinkedServiceName": "HDInsightOnDemandLinkedService",
	        "typeProperties": {
	          "AssemblyName": "MyDotNetActivity.dll",
	          "EntryPoint": "MyDotNetActivityNS.MyDotNetActivity",
	          "PackageLinkedService": "AzureStorageLinkedService",
	          "PackageFile": "customactivitycontainer/MyDotNetActivity.zip",
	          "extendedProperties": {
	            "SliceStart": "$$Text.Format('{0:yyyyMMddHH-mm}', Time.AddMinutes(SliceStart, 0))"
	          }
	        },
	        "Policy": {
	          "Concurrency": 2,
	          "ExecutionPriorityOrder": "OldestFirst",
	          "Retry": 3,
	          "Timeout": "00:30:00",
	          "Delay": "00:00:00"
	        }
	      }
	    ],
		"start": "2015-11-16T00:00:00Z",
		"end": "2015-11-16T05:00:00Z",
	    "isPaused": false
	  }
	}

## Ejemplos

Muestra | Qué hace la actividad personalizada 
------ | ----------- 
[Descargador de datos HTTP](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/HttpDataDownloaderSample). | Descarga datos de un punto de conexión HTTP a Almacenamiento de blobs de Azure a través de una actividad personalizada de C# en Data Factory.
[Ejemplo de Análisis de opiniones de Twitter.](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/TwitterAnalysisSample-CustomC%23Activity) | Invoca un modelo de aprendizaje automático de Azure y realiza un análisis de opiniones, puntuación, predicción, etc.
[Ejecutar script de R](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/RunRScriptUsingADFSample). | Invoca el script de R; para ello, ejecuta RScript.exe en el clúster de HDInsight que ya tiene instalado R. 
[Actividad .NET entre AppDomain](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/CrossAppDomainDotNetActivitySample) | Utiliza otras versiones de ensamblado que las utilizadas por el iniciador de Data Factory.  
 

## Otras referencias

[Actualizaciones de Factoría de datos de Azure: ejecución de actividades de .NET personalizadas de ADF mediante Lote de Azure](https://azure.microsoft.com/blog/2015/05/01/azure-data-factory-updates-execute-adf-custom-net-activities-using-azure-batch/).

[batch-net-library]: ../batch/batch-dotnet-get-started.md
[batch-explorer]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp/BatchExplorer
[batch-explorer-walkthrough]: http://blogs.technet.com/b/windowshpc/archive/2015/01/20/azure-batch-explorer-sample-walkthrough.aspx
[batch-create-account]: ../batch/batch-account-create-portal.md
[batch-technical-overview]: ../batch/batch-technical-overview.md
[batch-get-started]: ../batch/batch-dotnet-get-started.md
[monitor-manage-using-powershell]: data-factory-monitor-manage-using-powershell.md
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

<!---HONumber=AcomDC_0518_2016-->