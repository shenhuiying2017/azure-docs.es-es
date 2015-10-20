<properties
	pageTitle="Tutorial: Introducción a la biblioteca de .NET de Lote de Azure| Microsoft Azure"
	description="Aprenda los conceptos básicos sobre el Lote de Azure y cómo desarrollar el servicio Lote con un escenario simple."
	services="batch"
	documentationCenter=".net"
	authors="yidingzhou"
	manager="timlt"
	editor=""/>

<tags
	ms.service="batch"
	ms.devlang="dotnet"
	ms.topic="hero-article"
	ms.tgt_pltfrm="na"
	ms.workload="big-compute"
	ms.date="09/23/2015"
	ms.author="yidingz"/>

# Introducción a la biblioteca de proceso por lotes de Azure para .NET  

Comience a trabajar con la Biblioteca de .NET del Lote de Azure mediante la creación de una aplicación de consola que establezca los archivos de soporte y un programa que se ejecute en varios nodos de ejecución en un grupo de Lote de Azure. Las tareas que se crean en este tutorial evalúan el texto de los archivos cargados en Almacenamiento de Azure y devuelven las palabras que aparecen con más frecuencia en esos archivos. Los ejemplos están escritos en C# y usan la [Biblioteca de .NET de Lote de Azure](https://msdn.microsoft.com/library/azure/mt348682.aspx).

## Requisitos previos

- Las cuentas:

	- **Cuenta de Azure**: puede crear una cuenta de evaluación gratuita en pocos minutos. Para obtener más información, consulte [Evaluación gratuita de Azure](http://azure.microsoft.com/pricing/free-trial/).

	- **Cuenta de Lote**: consulte la sección **Cuenta de Lote** de [Información técnica de Lote de Azure](batch-technical-overview.md).

	- **Cuenta de almacenamiento**: consulte la sección **Crear una cuenta de almacenamiento** del apartado [Acerca de cuentas de Almacenamiento de Azure](../storage-create-storage-account.md). En este tutorial creará un contenedor en esta cuenta denominado **testcon1**.

- Un proyecto de aplicación de consola de Visual Studio:

	1.  Abra Visual Studio en el menú **Archivo**, haga clic en **Nuevo** y, a continuación, haga clic en **Proyecto**.

	2.	En **Windows**, en **Visual C#**, haga clic en **Aplicación de consola**, denomine el proyecto **GettingStarted**, denomine la solución **AzureBatch** y, a continuación, haga clic en **Aceptar**.

- Los ensamblados de NuGet:

	1. Después de crear el proyecto en Visual Studio, haga clic con el botón derecho en el proyecto en el **Explorador de soluciones** y elija **Administrar paquetes de NuGet**. Busque **Azure.Batch** en línea y, a continuación, haga clic en **Instalar** para instalar el paquete y las dependencias de Lote de Microsoft Azure.

	2. Busque **WindowsAzure.Storage** en línea y, a continuación, haga clic en **Instalar** para instalar el paquete y las dependencias de Almacenamiento de Azure.

> [AZURE.TIP]Este tutorial usará algunos de los principales conceptos de Lote descritos en [Aspectos básicos de la API de Lote de Azure](batch-api-basics.md); si es nuevo en Lote, le recomendamos que los lea.

## Paso 1: Creación y carga de los archivos de compatibilidad

Para admitir la aplicación, se crea un contenedor en Almacenamiento de Azure, se crean los archivos de texto y, a continuación, se cargan los archivos de texto y archivos de compatibilidad en el contenedor.

### Configuración de la cadena de conexión de almacenamiento

1. Abra el archivo App.config del proyecto GettingStarted y, a continuación, agregue el elemento *&lt;appSettings&gt;* en *&lt;configuration&gt;*.

		<?xml version="1.0" encoding="utf-8" ?>
		<configuration>
			<appSettings>
				<add key="StorageConnectionString" value="DefaultEndpointsProtocol=https;AccountName=[account-name];AccountKey=[account-key]"/>
			</appSettings>
		</configuration>

	Reemplace estos valores:

	- **[account-name]**: el nombre de la cuenta de almacenamiento que creó anteriormente.

	- **[account-key]**: la clave principal de la cuenta de almacenamiento. Puede encontrar la clave principal en la página de Almacenamiento que se encuentra en el Portal de Azure.

2. Guarde el archivo App.config.

Para obtener más información acerca de las cadenas de conexión de Almacenamiento de Azure, consulte [Configurar cadenas de conexión de Almacenamiento de Azure](../storage/storage-configure-connection-string.md).

### Creación de un contenedor de almacenamiento

1. Agregue las directivas “using” en la parte superior de Program.cs en el proyecto GettingStarted:

		using System.Configuration;
		using System.IO;
		using Microsoft.WindowsAzure.Storage;
		using Microsoft.WindowsAzure.Storage.Blob;

2. Agregue *System.Configuration* a **Referencias** en el **Explorador de soluciones** para el proyecto GettingStarted

3. Agregue este método a la clase Program que obtiene la cadena de conexión de almacenamiento, crea el contenedor y configura los permisos:

		static void CreateStorage()
		{
			// Get the storage connection string
			CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
				ConfigurationManager.AppSettings["StorageConnectionString"]);

			// Create the container
			CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();
			CloudBlobContainer container = blobClient.GetContainerReference("testcon1");
			container.CreateIfNotExists();

			// Set permissions on the container
			BlobContainerPermissions containerPermissions = new BlobContainerPermissions();
			containerPermissions.PublicAccess = BlobContainerPublicAccessType.Blob;
			container.SetPermissions(containerPermissions);
			Console.WriteLine("Created the container. Press Enter to continue.");
			Console.ReadLine();
		}

4. Agregue este código a Main, que llama al método que acaba de agregar:

		CreateStorage();

5. Guarde el archivo Program.cs.

	> [AZURE.NOTE]En un entorno de producción, se recomienda usar una [firma de acceso compartido](https://msdn.microsoft.com/library/azure/ee395415.aspx).

Para obtener más información acerca del almacenamiento de blobs, consulte [Cómo usar el almacenamiento de blobs en .NET](../storage/storage-dotnet-how-to-use-blobs.md).

### Creación del programa de procesamiento

1. En el **Explorador de soluciones**, cree un nuevo proyecto de aplicación de consola denominado **ProcessTaskData**.

2. Después de crear el proyecto en Visual Studio, haga clic con el botón derecho en el proyecto en el **Explorador de soluciones** y elija **Administrar paquetes de NuGet**. Busque **WindowsAzure.Storage** en línea y, a continuación, haga clic en **Instalar** para instalar el paquete y las dependencias de Almacenamiento de Azure.

3. Agregue la siguiente directiva “using” en la parte superior de Program.cs:

		using Microsoft.WindowsAzure.Storage.Blob;

4. Agregue este código a Main, que procesa el texto desde los archivos:

		string blobName = args[0];
		Uri blobUri = new Uri(blobName);
		int numTopN = int.Parse(args[1]);

		CloudBlockBlob blob = new CloudBlockBlob(blobUri);
		string content = blob.DownloadText();
		string[] words = content.Split(' ');
		var topNWords =
		  words.
			Where(word => word.Length > 0).
			GroupBy(word => word, (key, group) => new KeyValuePair<String, long>(key, group.LongCount())).
			OrderByDescending(x => x.Value).
			Take(numTopN).
			ToList();

		foreach (var pair in topNWords)
		{
			Console.WriteLine("{0} {1}", pair.Key, pair.Value);
		}

5. Guarde y compile el proyecto ProcessTaskData.

### Creación de los archivos de datos

1. En el proyecto GettingStarted, cree un nuevo archivo de texto denominado **taskdata1.txt**, copie el siguiente texto en él y, a continuación, guarde el archivo.

	Puede usar máquinas virtuales de Azure para proporcionar infraestructura de proceso a petición y escalable cuando necesite recursos flexibles para sus necesidades empresariales. En la galería, puede crear máquinas virtuales que ejecuten Windows, Linux y aplicaciones empresariales como SQL Server y SharePoint. O bien, puede capturar y utilizar sus propias imágenes para crear máquinas virtuales personalizadas.

2. Cree un nuevo archivo de texto denominado **taskdata2.txt**, copie el siguiente texto en él y, a continuación, guarde el archivo.

	Implemente y administre rápidamente aplicaciones y servicios potentes con los servicios en la nube de Azure. Cargue la aplicación y Azure controlará los detalles de la implementación: desde el aprovisionamiento y el equilibrio de carga hasta la supervisión de estado para una disponibilidad continua. La aplicación está respaldada por un contrato de nivel de servicio mensual líder en el sector con un 99,95 %. Simplemente se centrará en la aplicación y no la infraestructura.

3. Cree un nuevo archivo de texto denominado **taskdata3.txt**, copie el siguiente texto en él y, a continuación, guarde el archivo.

	Los sitios web Azure proporcionan un entorno escalable, confiable y fácil de usar para hospedar aplicaciones web. Seleccione entre una variedad de marcos y plantillas para crear un sitio web en segundos. Utilice cualquier herramienta o sistema operativo para desarrollar su sitio con. NET, PHP, Node.js o Python. Elija entre una variedad de opciones de control de código fuente como TFS, GitHub y BitBucket para configurar la integración continua y desarrollarse como equipo. Expanda la funcionalidad del sitio con el tiempo aprovechando los servicios administrados adicionales de Azure, como la base de datos SQL, la red CDN y el almacenamiento.

### Carga de los archivos en el contenedor de almacenamiento

1. Abra el archivo Program.cs del proyecto **GettingStarted** y, a continuación, agregue este método que carga los archivos:

		static void CreateFiles()
		{
			CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
				ConfigurationManager.AppSettings["StorageConnectionString"]);
			CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();
			CloudBlobContainer container = blobClient.GetContainerReference("testcon1");

			CloudBlockBlob taskData1 = container.GetBlockBlobReference("taskdata1");
			CloudBlockBlob taskData2 = container.GetBlockBlobReference("taskdata2");
			CloudBlockBlob taskData3 = container.GetBlockBlobReference("taskdata3");
			taskData1.UploadFromFile("..\\..\\taskdata1.txt", FileMode.Open);
			taskData2.UploadFromFile("..\\..\\taskdata2.txt", FileMode.Open);
			taskData3.UploadFromFile("..\\..\\taskdata3.txt", FileMode.Open);

			CloudBlockBlob storageassembly = container.GetBlockBlobReference("Microsoft.WindowsAzure.Storage.dll");
			storageassembly.UploadFromFile("Microsoft.WindowsAzure.Storage.dll", FileMode.Open);

			CloudBlockBlob dataprocessor = container.GetBlockBlobReference("ProcessTaskData.exe");
			dataprocessor.UploadFromFile("..\\..\\..\\ProcessTaskData\\bin\\debug\\ProcessTaskData.exe", FileMode.Open);

			Console.WriteLine("Uploaded the files. Press Enter to continue.");
			Console.ReadLine();
		}

2. Agregue este código a Main, que llama al método que acaba de agregar:

		CreateFiles();

3. Guarde el archivo Program.cs.

## Paso 2: Adición de un grupo a su cuenta de Lote

Un grupo de nodos de ejecución es el primer conjunto de recursos que debe crear cuando desee ejecutar tareas.

1.	Agregue las directivas “using” en la parte superior de Program.cs en el proyecto GettingStarted:

			using Microsoft.Azure.Batch;
			using Microsoft.Azure.Batch.Auth;
			using Microsoft.Azure.Batch.Common;

2. Agregue este código a Main, que configura las credenciales para realizar llamadas al servicio de Lote de Azure:

			BatchSharedKeyCredentials cred = new BatchSharedKeyCredentials("[account-url]", "[account-name]", "[account-key]");
			BatchClient client = BatchClient.Open(cred);

	Reemplace los valores entre corchetes por aquellos asociados con la cuenta de Lote, cada uno de los cuales puede encontrarse en el [portal de vista previa de Azure](https://portal.azure.com). Para encontrar estos valores, inicie sesión en el [Portal de vista previa de Azure](https://portal.azure.com) y:

	- **[account-name]**: haga clic en **Cuentas de Lote**, seleccione la cuenta de Lote que creó anteriormente
	- **[account-url]**: en la hoja de cuenta de Lote, haga clic en **Propiedades** > **URL**
	- **[account-key]**: en la hoja de cuenta de Lote, haga clic en **Propiedades** > **Claves** > **Clave de acceso primaria**

3.	Agregue este método a la clase Program que crea el grupo:

			static void CreatePool(BatchClient client)
			{
			  CloudPool newPool = client.PoolOperations.CreatePool(
			    "testpool1",
			    "3",
			    "small",
			    3);
			  newPool.Commit();
			  Console.WriteLine("Created the pool. Press Enter to continue.");
			  Console.ReadLine();
		  	}

4. Agregue este código a Main, que llama al método que acaba de agregar:

		CreatePool(client);

5. Agregue este método a la clase Program que enumera las Esto le ayudará a comprobar que se creó el grupo:

			static void ListPools(BatchClient client)
			{
				IPagedEnumerable<CloudPool> pools = client.PoolOperations.ListPools();
				foreach (CloudPool pool in pools)
				{
					Console.WriteLine("Pool name: " + pool.Id);
					Console.WriteLine("   Pool status: " + pool.State);
				}
				Console.WriteLine("Press enter to continue.");
				Console.ReadLine();
			}

6. Agregue este código a Main, que llama al método que acaba de agregar:

		ListPools(client);

7. Guarde el archivo Program.cs.

## Paso 3: Adición de un trabajo a una cuenta

Cree un trabajo que se use para administrar las tareas que se ejecutan en el grupo. Todas las tareas deben asociarse a un trabajo.

1. Agregue este método a la clase Program que crea el trabajo:

		static CloudJob CreateJob (BatchClient client)
		{
			CloudJob newJob = client.JobOperations.CreateJob();
			newJob.Id = "testjob1";
			newJob.PoolInformation = new PoolInformation() { PoolId = "testpool1" };
			newJob.Commit();
			Console.WriteLine("Created the job. Press Enter to continue.");
			Console.ReadLine();

			return newJob;
		}

2. Agregue este código a Main, que llama al método que acaba de agregar:

		CreateJob(client);

3. Agregue este método a la clase Program que enumera los trabajos en la cuenta. Esto le ayudará a comprobar que se creó el trabajo:

		static void ListJobs (BatchClient client)
		{
			IPagedEnumerable<CloudJob> jobs = client.JobOperations.ListJobs();
			foreach (CloudJob job in jobs)
			{
				Console.WriteLine("Job id: " + job.Id);
				Console.WriteLine("   Job status: " + job.State);
			}
			Console.WriteLine("Press Enter to continue.");
			Console.ReadLine();
		}

4. Agregue este código a Main, que llama al método que acaba de agregar:

		ListJobs(client);

5. Guarde el archivo Program.cs.

## Paso 4: Adición de tareas al trabajo

Después de crear el trabajo, pueden agregarse las tareas a él. Cada tarea se ejecuta en un nodo de ejecución y procesa un archivo de texto. Para este tutorial, agregue tres tareas al trabajo.

1. Agregue este método a la clase Program que agrega las tres tareas al trabajo:

		static void AddTasks(BatchClient client)
		{
			CloudJob job = client.JobOperations.GetJob("testjob1");
			ResourceFile programFile = new ResourceFile(
				"https://[account-name].blob.core.windows.net/testcon1/ProcessTaskData.exe",
				"ProcessTaskData.exe");
      	  ResourceFile assemblyFile = new ResourceFile(
				"https://[account-name].blob.core.windows.net/testcon1/Microsoft.WindowsAzure.Storage.dll",
				"Microsoft.WindowsAzure.Storage.dll");
			for (int i = 1; i < 4; ++i)
			{
				string blobName = "taskdata" + i;
				string taskName = "mytask" + i;
				ResourceFile taskData = new ResourceFile("https://[account-name].blob.core.windows.net/testcon1/" +
				  blobName, blobName);
				CloudTask task = new CloudTask(taskName, "ProcessTaskData.exe https://[account-name].blob.core.windows.net/testcon1/" +
				  blobName + " 3");
				List<ResourceFile> taskFiles = new List<ResourceFile>();
				taskFiles.Add(taskData);
				taskFiles.Add(programFile);
				taskFiles.Add(assemblyFile);
				task.ResourceFiles = taskFiles;
				job.AddTask(task);
				job.Commit();
				job.Refresh();
			}

			client.Utilities.CreateTaskStateMonitor().WaitAll(job.ListTasks(),
        TaskState.Completed, new TimeSpan(0, 30, 0));
			Console.WriteLine("The tasks completed successfully.");
			foreach (CloudTask task in job.ListTasks())
			{
				Console.WriteLine("Task " + task.Id + " says:\n" + task.GetNodeFile(Constants.StandardOutFileName).ReadAsString());
			}
			Console.WriteLine("Press Enter to continue.");
			Console.ReadLine();
		}


	**[account-name]** tiene que reemplazarse por el nombre de cuenta de almacenamiento que creó previamente. En el ejemplo anterior, actualice las cuatro instancias de **[account-name]**.


2. Agregue este código a Main, que llama al método que acaba de agregar:

			AddTasks(client);

3. Agregue este método a la clase Program que enumera las tareas asociadas al trabajo:

		static void ListTasks(BatchClient client)
		{
			IPagedEnumerable<CloudTask> tasks = client.JobOperations.ListTasks("testjob1");
			foreach (CloudTask task in tasks)
			{
				Console.WriteLine("Task id: " + task.Id);
				Console.WriteLine("   Task status: " + task.State);
			  Console.WriteLine("   Task start: " + task.ExecutionInformation.StartTime);
			}
			Console.ReadLine();
		}

4. Agregue este código a Main para llamar al método que acaba de agregar:

		ListTasks(client);

5. Guarde el archivo Program.cs.

## Paso 5: Eliminación de los recursos

Dado que se le cobrará por los recursos en Azure, siempre es conveniente eliminar los recursos si ya no son necesarios.

### Eliminación de las tareas

1.	Agregue este método a la clase Program que elimina las tareas:

			static void DeleteTasks(BatchClient client)
			{
				CloudJob job = client.JobOperations.GetJob("testjob1");
				foreach (CloudTask task in job.ListTasks())
				{
					task.Delete();
				}
				Console.WriteLine("All tasks deleted.");
				Console.ReadLine();
			}

2. Agregue este código a Main, que llama al método que acaba de agregar:

		DeleteTasks(client);

3. Guarde el archivo Program.cs.

### Eliminación del trabajo

1.	Agregue este método a la clase Program que elimina el trabajo:

			static void DeleteJob(BatchClient client)
			{
				client.JobOperations.DeleteJob("testjob1");
				Console.WriteLine("Job was deleted.");
				Console.ReadLine();
			}

2. Agregue este código a Main, que llama al método que acaba de agregar:

		DeleteJob(client);

3. Guarde el archivo Program.cs.

### Eliminación del grupo

1. Agregue este método a la clase Program que elimina el grupo:

		static void DeletePool (BatchClient client)
		{
			client.PoolOperations.DeletePool("testpool1");
			Console.WriteLine("Pool was deleted.");
			Console.ReadLine();
		}

2. Agregue este código a Main, que llama al método que acaba de agregar:

		DeletePool(client);

3. Guarde el archivo Program.cs.

## Paso 6: Ejecución de la aplicación

1. Inicie el proyecto GettingStarted y debería ver esto en la ventana de la consola después de que se cree el contenedor:

		Created the container. Press Enter to continue.

2. Presione Entrar y los archivos se crearán y se cargarán; ahora debería ver una nueva línea en la ventana:

		Uploaded the files. Press Enter to continue.

3. Presione Entrar y se creará el grupo:

		Created the pool. Press Enter to continue.

4. Presione Entrar y debería ver esta lista del grupo nuevo:

		Pool name: testpool1
			Pool status: Active
		Press Enter to continue.

5. Presione Entrar y se creará el trabajo:

		Created the job. Press Enter to continue.

6. Presione Entrar y debería ver esta lista de nuevo trabajo:

		Job id: testjob1
			Job status: Active
		Press Enter to continue.

7. Presione Entrar y las tareas se agregarán al trabajo. Cuando se agregan las tareas, se ejecutan automáticamente:

		The tasks completed successfully.
		Task mytask1 says:
		can 3
		you 3
		and 3

		Task mytask2 says:
		and 5
		application 3
		the 3

		Task mytask3 says:
		a 5
		and 5
		to 3

		Press Enter to continue.

7. Presione Entrar y debería ver la lista de tareas y su estado:

		Task id: mytask1
			Task status: Completed
			Task start: 7/17/2015 8:31:58 PM
		Task id: mytask2
			Task status: Completed
			Task start: 7/17/2015 8:31:57 PM
		Task id: mytask3
			Task status: Completed
			Task start: 7/17/2015 8:31:57 PM

8. En este momento, puede ir al Portal de Azure para ver los recursos que se han creado. Para eliminar los recursos, pulse Entrar hasta que el programa finalice.

## Pasos siguientes

1. Ahora que conoce los aspectos básicos de la ejecución de tareas, puede aprender a realizar el escalado automático de nodos de ejecución cuando cambie la demanda de la aplicación. Para ello, consulte [Escalado automático de nodos de ejecución en un grupo de Lote de Azure](batch-automatic-scaling.md).

2. Algunas aplicaciones generan grandes cantidades de datos que pueden ser difíciles de procesar. Una manera de resolver esto es a través de una [consulta de lista eficiente](batch-efficient-list-queries.md).

<!---HONumber=Oct15_HO3-->