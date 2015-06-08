<properties
	pageTitle="Tutorial: Introducción a la biblioteca de Lote de Azure para .NET"
	description="Aprenda los conceptos básicos sobre el lote de Azure y cómo desarrollar para el servicio de proceso por lotes con un escenario simple"
	services="batch"
	documentationCenter=".net"
	authors="yidingzhou"
	manager="timlt"
	editor=""/>

<tags
	ms.service="batch"
	ms.devlang="dotnet"
	ms.topic="article"
	ms.tgt_pltfrm="na"
	ms.workload="big-compute"
	ms.date="05/04/2015"
	ms.author="yidingz"/>

#Introducción a la biblioteca de Lote de Azure para .NET  

Este artículo contiene los siguientes dos tutoriales que le introducirán en el desarrollo con la biblioteca de .NET para el servicio Lote de Azure.

-	[Tutorial 1: Biblioteca de Azure por lotes para .NET](#tutorial1)
-	[Tutorial 2: Biblioteca de aplicaciones de Azure por lotes para .NET](#tutorial2)  


Para obtener información general y escenarios para el lote de Azure, consulte [Introducción técnica a Azure lote](batch-technical-overview.md).

##<a name="tutorial1"></a>Tutorial 1: Biblioteca de Azure por lotes para .NET

Este tutorial le enseñará a crear una aplicación de consola que configura el cálculo distribuido entre un grupo de máquinas virtuales mediante el servicio Lote de Azure. Las tareas que se crean en este tutorial evalúan el texto de los archivos de almacenamiento de Azure y devuelven las palabras que se utilizan normalmente. Los ejemplos están escritos en código C# y utilizan la biblioteca de Lote de Azure para .NET.


>[AZURE.NOTE]Para completar este tutorial, deberá tener una cuenta de Azure. Puede crear una cuenta de evaluación gratuita en pocos minutos. Para obtener más información, consulte [Evaluación gratuita de Azure](http://azure.microsoft.com/pricing/free-trial/).
>
>Deberá usar NuGet para obtener la **Microsoft.Azure.Batch.dll** ensamblado. Después de crear el proyecto en Visual Studio, haga clic en el proyecto en **el Explorador de soluciones** y elija **Administrar paquetes de NuGet**. Buscar en línea **Azure.Batch** y, a continuación, haga clic en instalar para instalar el paquete de proceso de Azure y las dependencias.
>
>Asegúrese de que la versión del Administrador de paquetes de NuGet es 2.8 o posterior. Puede encontrar el número de versión en Visual Studio -> "Ayuda" -> cuadro de diálogo "Acerca de Microsoft Visual Studio". Si tiene una versión anterior del Administrador de paquetes de NuGet, deberá actualizar Visual Studio o puede que tenga problemas al descargar la versión correcta de las dependencias de NuGet.
>
>Además, puede hacer referencia a la [ejemplo Hello World de Azure por lotes](https://code.msdn.microsoft.com/Azure-Batch-Sample-Hello-6573967c) en MSDN para obtener un ejemplo similar al código que se tratan aquí.



###Conceptos
El servicio Lote se utiliza para programar un cálculo escalable y distribuido. Le permite ejecutar cargas de trabajo de gran escala que se distribuyen entre varias máquinas virtuales. Estas cargas de trabajo proporcionan soporte eficiente para el cálculo intensivo. Cuando usa el servicio Lote, se beneficia de los siguientes recursos:

-	**Cuenta** - A identifica de forma única entidad dentro del servicio. Todo el procesamiento se realiza a través de una cuenta de Lote.
-	**Grupo** : una colección de máquinas virtuales de tareas en las aplicaciones de tarea que ejecutar.
-	**Máquina Virtual de tareas** -una máquina que se asigna a un grupo y se utiliza por las tareas que se asignan a los trabajos que se ejecutan en el grupo.
-	**Usuario de la máquina Virtual de tarea** : una cuenta de usuario en una máquina virtual de tarea.
-	**Workitem** -especifica cómo se realiza el cálculo en máquinas virtuales de tareas en un grupo.
-	**Trabajo** - A ejecutando la instancia de un elemento de trabajo y consta de una colección de tareas.
-	**Tareas** : una aplicación que está asociada con un proyecto y se ejecuta en una máquina virtual de tareas.
-	**Archivo** – contiene la información que es procesada por una tarea.  

Comencemos con el uso más básico.

###Creación de una cuenta de Lote de Azure
Puede utilizar el Portal de administración para crear la cuenta de Lote. Una vez creada la cuenta, se le proporciona una clave. Para obtener más información, consulte [Introducción técnica a Azure lote](batch-technical-overview.md).

###Cómo: agregar un grupo a una cuenta
Un grupo de máquinas virtuales de tareas es el primer conjunto de recursos que debe crear cuando desee ejecutar tareas.

1.	Abra Microsoft Visual Studio 2013, en la **archivo** menú, haga clic en **nuevo**, y, a continuación, haga clic en **proyecto**.

2.	De **Windows**, en **Visual C#**, haga clic en **aplicación de consola**, denomine el proyecto **GettingStarted**, llame a la solución **AzureBatch**, y, a continuación, haga clic en **Aceptar**.

3.	Agregue las siguientes declaraciones de espacio de nombres en la parte superior de Program.cs:

		using Microsoft.Azure.Batch;
		using Microsoft.Azure.Batch.Auth;
		using Microsoft.Azure.Batch.Common;

	Asegúrese de hacer referencia al ensamblado Microsoft.Azure.Batch.dll.

4.	Agregue las siguientes variables a la clase Program:

		private const string PoolName = "[name-of-pool]";
		private const int NumOfMachines = 3;
		private const string AccountName = "[name-of-batch-account]";
		private const string AccountKey = "[key-of-batch-account]";
		private const string Uri = "https://batch.core.windows.net";
	Reemplace los valores siguientes:- **[nombre de grupo]** - el nombre que desea utilizar para el grupo. - **[cuenta de nombre de lote]** - el nombre de la cuenta de proceso por lotes. - **[clave de cuenta de proceso por lotes]** -la clave que proporcionó para la cuenta de proceso por lotes.
5.	Agregue el código siguiente a Main que define las credenciales que se van a utilizar:

		BatchCredentials cred = new BatchCredentials(AccountName, AccountKey);
6.	Agregue el código siguiente a Main para crear el cliente que se utiliza para realizar operaciones:

		IBatchClient client = BatchClient.Connect(Uri, cred);
7.	Agregue el código siguiente a Main para crear el grupo si no existe:

		using (IPoolManager pm = client.OpenPoolManager())
		{
		   IEnumerable<ICloudPool> pools = pm.ListPools();
		   if (!pools.Select(pool => pool.Name).Contains(PoolName))
		   {
		      Console.WriteLine("The pool does not exist, creating now...");
		      ICloudPool newPool = pm.CreatePool(
		         PoolName,
		         osFamily: "3",
		         vmSize: "small",
		         targetDedicated: NumOfMachines);
		       newPool.Commit();
		    }
		}
		Console.WriteLine("Created pool {0}", PoolName);
		Console.ReadLine();
8.	Guarde y ejecute el programa. El estado es **Active** para un grupo que se agregó correctamente.  

###Cómo: enumerar los grupos de una cuenta
Si no conoce el nombre de un grupo existente, puede obtener una lista de ellos en una cuenta.

1.	Agregue el código siguiente a Main que define las credenciales que se van a utilizar:  

		BatchCredentials cred = new BatchCredentials(AccountName, AccountKey);
2.	Agregue el código siguiente a Main para crear el cliente que se utiliza para realizar operaciones:

		IBatchClient client = BatchClient.Connect(Uri, cred);

3.	Actualice el procedimiento Main en el siguiente código que escribe los nombres y los estados de todos los grupos de la cuenta y cree el grupo si no existe:

		BatchCredentials cred = new BatchCredentials(AccountName, AccountKey);
		IBatchClient client = BatchClient.Connect(Uri, cred);

		using (IPoolManager pm = client.OpenPoolManager())
		{
		    IEnumerable<ICloudPool> pools = pm.ListPools();

		    Console.WriteLine("Listing Pools\n=================");
		    foreach (var p in pools)
		    {
		        Console.WriteLine("Pool: " + p.Name + " State:" + p.State);
		    }  

		    if (!pools.Select(pool => pool.Name).Contains(PoolName))
		    {
		        Console.WriteLine("The pool does not exist, creating now...");
		        ICloudPool newPool = pm.CreatePool(
		           PoolName,
		           osFamily: "3",
		           vmSize: "small",
		           targetDedicated: NumOfMachines);
		        newPool.Commit();
		    }
		}
		Console.WriteLine("Created pool {0}. Press <Enter> to continue.", PoolName);
		Console.ReadLine();

4.	Guarde y ejecute el programa. Verá la siguiente salida.

		Listing Pools
		=================
		Pool: gettingstarted State:Active
		Created pool gettingstarted. Press <Enter> to continue.

###Cómo: enumerar los elementos de trabajo en una cuenta
Si no conoce el nombre de un elemento de trabajo existente, puede obtener una lista de ellos en una cuenta.

1.	Agregue el código siguiente al final de Main que escribe los nombres y los estados de todos los elementos de trabajo de la cuenta:

		using (IWorkItemManager wm = client.OpenWorkItemManager())
		{
		   Console.WriteLine("Listing Workitems\n=================");
		   IEnumerable<ICloudWorkItem> workitems = wm.ListWorkItems();
		   foreach (var w in workitems)
		   {
		      Console.WriteLine("Workitem: " + w.Name + " State:" + w.State);
		   }
		}
		Console.WriteLine("Press <Enter> to continue.");
		Console.ReadLine();
7.	Guarde y ejecute el programa. Probablemente no verá nada, ya que no hemos enviado ningún elemento de trabajo. Hablaremos acerca de cómo agregar elementos de trabajo en la sección siguiente.  

##Cómo: agregar un elemento de trabajo a una cuenta
Debe crear un elemento de trabajo para definir el modo en que se ejecutarán las tareas en el grupo.

1.	Agregue las siguientes variables a la clase Program:

		private static readonly string WorkItemName = Environment.GetEnvironmentVariable("USERNAME") + DateTime.Now.ToString("yyyyMMdd-HHmmss");

2.	Cuando se crea un elemento de trabajo, también se crea un trabajo. Puede asignar un nombre al elemento de trabajo, pero el trabajo siempre se asigna el nombre de **0000000001 de trabajo**. Agregue el código siguiente a Main (antes del código del elemento de trabajo de la lista) que agrega el elemento de trabajo:

		using (IWorkItemManager wm = client.OpenWorkItemManager())
		{
		   ICloudWorkItem cloudWorkItem = wm.CreateWorkItem(WorkItemName);
           cloudWorkItem.JobExecutionEnvironment = new JobExecutionEnvironment() {PoolName = PoolName};
		   cloudWorkItem.Commit();
		}
		Console.WriteLine("Workitem successfully added. Press <Enter> to continue.");
		Console.ReadLine();
3.	Guarde y ejecute el programa. El estado es **Active** para un elemento de trabajo que se agregó correctamente. Debería ver la siguiente salida.

		Listing Pools
		=================
		Pool: gettingstarted State:Active
		Created pool gettingstarted. Press <Enter> to continue.

		Workitem successfully added. Press <Enter> to continue.

		Listing Workitems
		=================
		Workitem: yidingz20141106-111211 State:Active
		Press <Enter> to continue.

###Cómo: agregar tareas a un trabajo
Un elemento de trabajo sin tarea no hará nada. Una vez creados el elemento de trabajo y el trabajo, puede agregar tareas a este último. Vamos a agregar una tarea sencilla al trabajo.

1.	Agregue las siguientes variables a la clase Program:

		private const int Count = 4; // number of tasks that will run
		private const string JobName = "job-0000000001";

2.	Agregue el código siguiente a Main que agrega tareas a un trabajo, espera a que se ejecuten e informa de los resultados:

		using (IWorkItemManager wm = client.OpenWorkItemManager())
		{
		    string taskName;
		    ICloudJob job = wm.GetJob(WorkItemName, JobName);
		    for (int i = 1; i <= Count; ++i)
		    {
		        taskName = "taskdata" + i;
		        string commandLine = String.Format("cmd /c echo I am {0}", taskName);
		        ICloudTask taskToAdd = new CloudTask(taskName, commandLine);
		        job.AddTask(taskToAdd);
		        job.Commit();
		        job.Refresh();
		    }

		    ICloudJob listjob = wm.GetJob(WorkItemName, JobName);
		    client.OpenToolbox().CreateTaskStateMonitor().WaitAll(listjob.ListTasks(),
		       TaskState.Completed, new TimeSpan(0, 30, 0));
		    Console.WriteLine("The tasks completed successfully. Terminating the workitem...");
		    wm.GetWorkItem(WorkItemName).Terminate();
		    foreach (ICloudTask task in listjob.ListTasks())
		    {
		        Console.WriteLine("Task " + task.Name + " says:\n" + task.GetTaskFile(Constants.StandardOutFileName).ReadAsString());
		    }
		    Console.ReadLine();
		}

3.	Guarde y ejecute el programa. El resultado debería tener este aspecto:

		The tasks completed successfully. Terminating the workitem...
		Task taskdata1 says:
		I am taskdata1

		Task taskdata2 says:
		I am taskdata2

		Task taskdata3 says:
		I am taskdata3

###Creación de un programa de procesamiento de tareas
Ahora que podemos ejecutar hello world en la máquina virtual, hagamos algo real. Vamos a crear un programa de procesamiento de tareas en esta sección y cargarlo en la máquina virtual de tareas que ejecuta tareas.

1.	En el Explorador de soluciones, cree un nuevo proyecto de aplicación de consola denominado **ProcessTaskData** en la **AzureBatch** solución.

2.	Agregue las siguientes declaraciones de espacio de nombres en la parte superior de Program.cs:

		using System.Net;

3.	Agregue el código siguiente a Main que se utilizará para procesar los datos:

		string blobName = args[0];
		int numTopN = int.Parse(args[1]);

		WebClient myWebClient = new WebClient();
		string content = myWebClient.DownloadString(blobName);

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

4.	Guarde y compile el proyecto.

###Preparación de los recursos para la ejecución de la tarea

Utilizará el siguiente flujo de trabajo básico al crear un escenario de cálculo distribuido con el servicio Lote:

1.	Cargue los archivos que desea utilizar en el escenario de cálculo distribuido a una cuenta de almacenamiento de Azure. Estos archivos deben estar en la cuenta de almacenamiento para que el servicio de Lote pueda tener acceso a ellos. Los archivos se cargan en una máquina virtual de tareas cuando se ejecuta la tarea.
2.	Cargue los archivos binarios dependientes en la cuenta de almacenamiento. Los archivos binarios incluyen el programa que ejecutará la tarea y los ensamblados dependientes. También es necesario tener acceso a estos archivos desde el almacenamiento y se cargan en la máquina virtual de tareas.
3.	Cree un grupo de máquinas virtuales de tareas. Puede asignar el tamaño de la máquina virtual de tareas que se utilizará cuando se crea el grupo. Cuando se ejecuta una tarea, se le asigna una máquina virtual de este grupo.
4.	Cree un elemento de trabajo. Un elemento de trabajo le permite administrar un trabajo de tareas. Cuando se crea un elemento de trabajo, se crea automáticamente un trabajo.
5.	Agregue tareas al trabajo. Cada tarea usa el programa que ha cargado para procesar la información de un archivo cargado.
6.	Supervise los resultados de la salida.  

Hemos mostrado los pasos de 3 a 6. Veamos cómo preparar el Almacenamiento de Azure para ejecutar la tarea.

####Obtención de cuenta de almacenamiento
Necesitará una cuenta de almacenamiento para completar el resto de este tutorial. Si no sabe cómo hacerlo, consulte [crear una cuenta de almacenamiento de Azure](#tutorial1_storage).

####Carga de datos

1. Cree un contenedor llamado "gettingstarted" en la cuenta de Almacenamiento de Azure. Puede utilizar para ello el Portal de Azure. Asegúrese de que establece el campo de "ACCESS" en "Public Container".

2. Cargue "ProcessTaskData.exe" en el contenedor.

3. Cree tres archivos de texto (taskdata1.txt, taskdata2.txt, taskdata3.txt) de manera que cada uno contenga los siguientes párrafos y, a continuación, cárguelos en el contenedor:

		You can use Azure Virtual Machines to provision on-demand, scalable compute infrastructure when you need flexible resources for your business needs. From the gallery, you can create virtual machines that run Windows, Linux, and enterprise applications such as SharePoint and SQL Server. Or, you can capture and use your own images to create customized virtual machines.

		Quickly deploy and manage powerful applications and services with Azure Cloud Services. Simply upload your application and Azure handles the deployment details - from provisioning and load balancing to health monitoring for continuous availability. Your application is backed by an industry leading 99.95% monthly SLA. You just focus on the application and not the infrastructure.

		Azure Web Sites provide a scalable, reliable, and easy-to-use environment for hosting web applications. Select from a range of frameworks and templates to create a web site in seconds. Use any tool or OS to develop your site with .NET, PHP, Node.js or Python. Choose from a variety of source control options including TFS, GitHub, and BitBucket to set up continuous integration and develop as a team. Expand your site functionality over time by leveraging additional Azure managed services like storage, CDN, and SQL Database.


>[AZURE.NOTE]En un entorno de producción, se recomienda que utilice una firma de acceso compartido.


>[AZURE.NOTE]Equipo de almacenamiento de Azure tiene un [entrada de blog](http://blogs.msdn.com/b/windowsazurestorage/archive/2014/03/11/windows-azure-storage-explorers-2014.aspx) lista de exploradores de almacenamiento de Azure que pueden ayudar a la carga de archivos.



###Actualización del código de envío de tarea

1.	Agregue las siguientes variables a la clase Program:

		private const string BlobPath = "[storage-path]";
	Reemplace los valores siguientes:- **[ruta de acceso de almacenamiento]** -la ruta de acceso del BLOB en almacenamiento. Por ejemplo: http://yiding.blob.core.windows.net/gettingstarted/

2. Actualice el código de envío de la tarea del modo siguiente.

		string taskName;
		ICloudJob job = wm.GetJob(WorkItemName, JobName);
		for (int i = 1; i <= Count; ++i)
		{
		    taskName = "taskdata" + i;
		    IResourceFile programFile = new ResourceFile(BlobPath + "ProcessTaskData.exe", "ProcessTaskData.exe");
		    IResourceFile supportFile = new ResourceFile(BlobPath + taskName + ".txt", taskName);
		    string commandLine = String.Format("ProcessTaskData.exe {0} {1}", BlobPath + taskName + ".txt", 3);
		    ICloudTask taskToAdd = new CloudTask(taskName, commandLine);
		    taskToAdd.ResourceFiles = new List<IResourceFile>();
		    taskToAdd.ResourceFiles.Add(programFile);
		    taskToAdd.ResourceFiles.Add(supportFile);
		    job.AddTask(taskToAdd);
		}
		job.Commit();

3. Guarde y ejecute el programa. Debería ver lo siguiente:

		Listing Pools
		=================
		Pool: gettingstarted State:Active
		Created pool gettingstarted. Press <Enter> to continue.

		Workitem successfully added. Press <Enter> to continue.

		Listing Workitems
		=================
		Workitem: yidingz20141106-132140 State:Active
		Press <Enter> to continue.

		The tasks completed successfully. Terminating the workitem...
		Task taskdata1 says:
		can 3
		you 3
		and 3

		Task taskdata2 says:
		and 5
		application 3
		the 3

		Task taskdata3 says:
		a 5
		and 5
		to 3

###Cómo: eliminar el grupo y el elemento de trabajo
Una vez procesado el elemento de trabajo, puede liberar recursos eliminando el grupo y el elemento de trabajo.

####Eliminación del grupo
1.	Agregue el código siguiente al final de Main que elimina el grupo:

		using (IPoolManager pm = client.OpenPoolManager())
		{
		   pm.DeletePool(PoolName);
		}
		Console.WriteLine("Pool successfully deleted.");
		Console.ReadLine();
2.	Guarde y ejecute el programa.


####Eliminación del elemento de trabajo
1.	Agregue el código siguiente a Main que elimina el elemento de trabajo:

		using (IWorkItemManager wm = client.OpenWorkItemManager())
		{
		   wm.DeleteWorkItem(WorkItemName);
		}
		Console.WriteLine("Workitem successfully deleted.");
		Console.ReadLine();
2.	Guarde y ejecute el programa.

###<a name="tutorial1_storage"></a>Apéndice: Crear una cuenta de almacenamiento de Azure
Para poder ejecutar el código en este tutorial, debe tener acceso a una cuenta de almacenamiento de Azure.

1.	Inicie sesión en el [Portal de administración de Azure](http://manage.windowsazure.com/).
2.	En la parte inferior del panel de navegación, haga clic en **nuevo**. ![][1]
3.	Haga clic en **DATA SERVICES**, a continuación, **almacenamiento**, y, a continuación, haga clic en **creación rápida**. ![][2]

4.	En **URL**, escriba un nombre de subdominio para usar en el URI para la cuenta de almacenamiento. Esta entrada puede contener de 3 a 24 letras minúsculas y números. Este valor se convierte en el nombre del host dentro del URI que se ha usado para direccionar los recursos Blob, Cola o Tabla de la suscripción.
5.	Elija un **ubicación/grupo de AFINIDAD** en el que se ubicará el almacenamiento.
6.	También puede habilitar la replicación geográfica.
7.	Haga clic en **CREAR CUENTA DE ALMACENAMIENTO**.  

Para obtener más información acerca del almacenamiento de Azure, consulte [cómo utilizar el servicio de almacenamiento de blobs de Azure en .NET](http://azure.microsoft.com/develop/net/how-to-guides/blob-storage/).


##<a name="tutorial2"></a>Tutorial 2: Biblioteca de aplicaciones de Azure por lotes para .NET
Este tutorial muestra cómo ejecutar cargas de trabajo de proceso paralelas en Lote de Azure mediante el servicio de Aplicaciones de Lote.

Aplicaciones de Lote es una característica de Lote de Azure que proporciona una manera de administrar y ejecutar las cargas de trabajo de Lote centrada en las aplicaciones. Con el SDK de Aplicaciones de Lote, puede crear paquetes para habilitar una aplicación para el uso de Lote e implementarlos en su propia cuenta o bien ponerlos a disposición de otros usuarios de Lote. Lote también proporciona administración de datos, supervisión de trabajos, diagnóstico y registro integrado y soporte para dependencias entre tareas. Además, incluye un portal de administración donde puede administrar trabajos, ver registros y descargar resultados sin tener que escribir su propio cliente.

En el caso de Aplicaciones de Lote, se escribe código utilizando el SDK de la nube de Aplicaciones de Lote para dividir trabajos en tareas paralelas, se describen las dependencias entre estas tareas y se especifica cómo ejecutar cada tarea. Este código se implementa en la cuenta de Lote. Los clientes, a continuación, pueden ejecutar trabajos de manera sencilla especificando el tipo de trabajo y los archivos de entrada en una API de REST.

>[AZURE.NOTE]Para completar este tutorial, deberá tener una cuenta de Azure. Puede crear una cuenta de evaluación gratuita en pocos minutos. Para obtener más información, consulte [Evaluación gratuita de Azure](http://azure.microsoft.com/pricing/free-trial/). Puede usar NuGet para obtener tanto los <a href="http://www.nuget.org/packages/Microsoft.Azure.Batch.Apps.Cloud/">lote aplicaciones de nube</a> ensamblado y el <a href="http://www.nuget.org/packages/Microsoft.Azure.Batch.Apps/">lote aplicaciones cliente</a> ensamblado. Después de crear el proyecto en Visual Studio, haga clic en el proyecto en **el Explorador de soluciones** y elija **Administrar paquetes de NuGet**. También puede descargar la extensión de Visual Studio para las aplicaciones de proceso por lotes que incluye una plantilla de proyecto para la nube permiten a las aplicaciones y la capacidad de implementar una aplicación <a href="https://visualstudiogallery.msdn.microsoft.com/8b294850-a0a5-43b0-acde-57a07f17826a">aquí</a> o a través de la búsqueda de **aplicaciones de proceso por lotes** en Visual Studio mediante el elemento de menú de extensiones y actualizaciones. También puede encontrar <a href="https://go.microsoft.com/fwLink/?LinkID=512183&clcid=0x409">-to-end muestras en MSDN.</a>
>

###Aspectos básicos de Aplicaciones de Lote de Azure
Lote está diseñado para trabajar con las aplicaciones existentes de proceso intensivo. Aprovecha el código de la aplicación existente y se ejecuta en un entorno dinámico, virtualizado y de uso general. Para habilitar una aplicación para que funcione con Aplicaciones de Lote hay un par de cosas que necesita hacer:

1.	Prepare un archivo comprimido de los ejecutables de aplicación existentes, los mismos ejecutables que se ejecutarían en una granja o un clúster de servidores tradicional, y los archivos de soporte técnico que necesita. Este archivo comprimido, a continuación, se carga en la cuenta de Lote mediante el portal de administración o la API de REST.
2.	Cree un archivo comprimido con los "ensamblados de nube" que distribuyen las cargas de trabajo en la aplicación, y cárguelo a través del portal de administración o la API de REST. Un ensamblado de nube contiene dos componentes que se generan con el SDK de nube:
	1.	Divisor de trabajo: desglosa el trabajo en tareas que se pueden procesar de forma independiente. Por ejemplo, en un escenario de animación, el divisor de trabajo podría tomar un trabajo de película y desglosarlo en fotogramas individuales.
	2.	Procesador de tareas: invoca el ejecutable de la aplicación para una tarea determinada. Por ejemplo, en un escenario de animación, el procesador de tareas invocaría un programa de representación para representar el fotograma único especificado por la tarea en cuestión.
3.	Proporcione una manera de enviar trabajos a la aplicación habilitada en Azure. Podría tratarse de un complemento en la interfaz de usuario o un portal web de la aplicación, o incluso un servicio no atendido como parte de la canalización de ejecución. Consulte la <a href="https://go.microsoft.com/fwLink/?LinkID=512183&clcid=0x409">ejemplos</a> en MSDN para obtener ejemplos.



###Conceptos clave de Aplicaciones de Lote
El modelo de programación y uso de Aplicaciones de Lote gira en torno a los conceptos siguientes:

####Trabajos
Un **trabajo** es un elemento de trabajo enviada por el usuario. Cuando se envía un trabajo, el usuario especifica el tipo de trabajo, cualquier configuración de ese trabajo y los datos requeridos para el trabajo. La implementación habilitada puede ocuparse de estos detalles en nombre del usuario o, en algunos casos, el usuario puede proporcionar esta información explícitamente a través del cliente. Un trabajo tiene resultados que se devuelven. Cada trabajo tiene una salida principal y, opcionalmente, un resultado en vista previa. Los trabajos también pueden devolver resultados adicionales si lo desea.

####Tareas
Un **tarea** es una parte del trabajo para llevar a cabo como parte de un trabajo. Cuando un usuario envía un trabajo, este se desglosa en tareas más pequeñas. El servicio procesa estas tareas individuales y, a continuación, ensambla los resultados de la tarea en una salida de trabajo global. La naturaleza de las tareas depende del tipo de trabajo. El divisor de trabajo define cómo se desglosa un trabajo en tareas, guiado por el conocimiento de aquellos fragmentos de trabajo que la aplicación va a procesar según su diseño. Los resultados de tarea también se pueden descargar individualmente y pueden resultar útiles en algunos casos; por ejemplo, cuando un usuario desea descargar tareas individuales de un trabajo de animación.

####Tareas de combinación
Un **tarea de mezcla** es un tipo especial de tarea que ensambla los resultados de las tareas individuales en los resultados del trabajo final. En el caso de un trabajo de representación de película, la tarea de combinación podría ensamblar los fotogramas representados en una película o comprimir todos los fotogramas representados en un único archivo. Todos los trabajos tienen una tarea de combinación, incluso si no se requiere una "combinación" real.

####Archivos
Un **archivo** es un fragmento de datos que se utilizan como entrada a un trabajo. Un trabajo puede no tener asociado ningún archivo de entrada o bien tener uno o varios. El mismo archivo puede utilizarse en varios trabajos, por ejemplo, para una película de trabajo de procesamiento, los archivos que podrían estar texturas, modelos, etc.. Para un trabajo de análisis de datos, los archivos pueden ser un conjunto de observaciones o mediciones.

###Habilitación de la aplicación en la nube
La aplicación debe contener una propiedad o un campo estático que contenga todos los detalles de la aplicación. Este especifica el nombre de la aplicación y los tipos de trabajo controlados por la aplicación. Se proporciona cuando se utiliza la plantilla en el SDK que se puede descargar a través de la Galería de Visual Studio.

Este es un ejemplo de una declaración de aplicación de nube para una carga de trabajo paralela:

	public static class ApplicationDefinition
	{
	    public static readonly CloudApplication App = new ParallelCloudApplication
	    {
	        ApplicationName = "ApplicationName",
	        JobType = "ApplicationJobType",
	        JobSplitterType = typeof(MyJobSplitter),
	        TaskProcessorType = typeof(MyTaskProcessor),
	    };
	}

####Implementación del divisor de trabajo y el procesador de tareas
En el caso de las cargas de trabajo embarazosamente paralelas, debe implementar un divisor de trabajo y un procesador de tareas.

####Implementación de JobSplitter.SplitIntoTasks
La implementación de SplitIntoTasks debe devolver una secuencia de tareas. Cada tarea representa un elemento de trabajo independiente que se pondrá en cola para su procesamiento por un nodo de cálculo. Cada tarea debe ser independiente y debe configurarse con toda la información que necesitará el procesador de tareas.

Las tareas especificadas por el divisor de trabajo se representan como objetos TaskSpecifier. TaskSpecifier tiene varias propiedades que puede configurar antes de devolver la tarea.


-	TaskIndex se omite, pero está disponible para los procesadores de tareas. Puede utilizarlo para pasar un índice al procesador de tareas. Si no necesita pasar un índice, no es necesario configurar esta propiedad.
-	Parámetros es una colección vacía de forma predeterminada. Puede agregarle elementos o reemplazarla por una nueva colección. Puede copiar entradas de la colección de parámetros de trabajo utilizando el método WithJobParameters o WithAllJobParameters.  


RequiredFiles es una colección vacía de forma predeterminada. Puede agregarle elementos o reemplazarla por una nueva colección. Puede copiar entradas de la colección de archivos de trabajo utilizando el método RequiringJobFiles o RequiringAllJobFiles.

Puede especificar una tarea que depende de otra tarea determinada. Para ello, configure la propiedad TaskSpecifier.DependsOn, pasando el identificador de la tarea de la que depende:

	new TaskSpecifier {
	    DependsOn = TaskDependency.OnId(5)
	}

La tarea no se ejecutará hasta que esté disponible la salida de la tarea de la que depende.

También puede especificar que todo un grupo de tareas no debe comenzar a procesarse hasta que otro grupo haya finalizado completamente. En este caso puede configurar la propiedad TaskSpecifier.Stage. Las tareas con un valor determinado de Stage no comenzarán a procesarse hasta que hayan terminado todas las tareas con valores más bajos de Stage; Por ejemplo, las tareas con un valor de Stage de 3 no empezarán a procesarse hasta que hayan terminado todas las tareas con un valor de Stage de 0, 1 o 2. Las etapas deben comenzar en 0, la secuencia de etapas no debe tener espacios y SplitIntoTasks debe devolver las tareas por orden de Stage: por ejemplo, es un error devolver una tarea de Stage 0 después de una tarea de Stage 1.

Cada trabajo paralelo finaliza con una tarea especial denominada tarea de combinación. El trabajo de la tarea de combinación es ensamblar los resultados de las tareas de procesamiento en paralelo en un resultado final. Aplicaciones de Lote crea automáticamente la tarea de combinación.

En raras ocasiones, puede que desee controlar explícitamente la tarea de combinación; por ejemplo, para personalizar sus parámetros. En este caso, puede especificar la tarea de combinación devolviendo un TaskSpecifier con la propiedad IsMerge establecida en true. Esto anulará la tarea de combinación automática. Si crea una tarea de combinación explícita:

-	Puede crear solo una tarea de combinación explícita
-	Debe ser la última tarea de la secuencia
-	Debe tener IsMerge establecido en true, y el resto de tareas deben tener IsMerge establecido en false  


Sin embargo, recuerde que normalmente no necesitará crear explícitamente la tarea de combinación.

El código siguiente muestra una implementación sencilla de SplitIntoTasks.

	protected override IEnumerable<TaskSpecifier> SplitIntoTasks(
	    IJob job,
	    JobSplitSettings settings)
	{
	    int start = Int32.Parse(job.Parameters["startIndex"]);
	    int end = Int32.Parse(job.Parameters["endIndex"]);
	    int count = (end - start) + 1;

	    // Processing tasks
	    for (int i = 0; i < count; ++i)
	    {
	        yield return new TaskSpecifier
	        {
	            TaskIndex = start + i,
	        }.RequiringAllJobFiles();
	    }
	}
####Implementación ParallelTaskProcessor.RunExternalTaskProcess

Se llama a RunExternalTaskProcess para cada tarea de combinación devuelta desde el divisor de trabajo. Debería invocar la aplicación con los argumentos adecuados y devolver un conjunto de resultados que deben conservarse para su uso posterior.

El siguiente fragmento muestra cómo llamar a un programa denominado application.exe. Tenga en cuenta que puede utilizar el método auxiliar ExecutablePath para crear rutas de archivo absolutas.

La clase ExternalProcess en el SDK de Nube proporciona una útil lógica auxiliar para los archivos ejecutables de la aplicación. ExternalProcess puede ocuparse de la cancelación, la conversión de códigos de salida en excepciones, la captura de la salida estándar y el error estándar y la configuración de las variables de entorno. Además, también puede utilizar la clase .NET Process directamente para ejecutar programas si lo prefiere.

El método RunExternalTaskProcess devuelve un TaskProcessResult, que incluye una lista de archivos de salida. Esto debe incluir como mínimo todos los archivos necesarios para la combinación; opcionalmente, también puede devolver archivos de registro, archivos de vista previa y archivos intermedios (por ejemplo, para fines de diagnóstico si se produjo un error en la tarea). Tenga en cuenta que el método devuelve las rutas de acceso de los archivos, no el contenido de los archivos.

Cada archivo debe identificarse con el tipo de salida que contiene: salida (es decir, la parte de la salida del trabajo eventual), vista previa, registro o intermedios. Estos valores proceden de la enumeración TaskOutputFileKind. El siguiente fragmento devuelve una salida de tarea única, y no vistas previas o registros. El método TaskProcessResult.FromExternalProcessResult simplifica el escenario común de captura del código de salida, el resultado del procesador y los archivos de salida desde un programa de línea de comandos:

El código siguiente muestra una implementación sencilla de ParallelTaskProcessor.RunExternalTaskProcess.

	protected override TaskProcessResult RunExternalTaskProcess(
	    ITask task,
	    TaskExecutionSettings settings)
	{
	    var inputFile = LocalPath(task.RequiredFiles[0].Name);
	    var outputFile = LocalPath(task.TaskId.ToString() + ".jpg");

	    var exePath = ExecutablePath(@"application\application.exe");
	    var arguments = String.Format("-in:{0} -out:{1}", inputFile, outputFile);

	    var result = new ExternalProcess {
	        CommandPath = exePath,
	        Arguments = arguments,
	        WorkingDirectory = LocalStoragePath,
	        CancellationToken = settings.CancellationToken
	    }.Run();

	    return TaskProcessResult.FromExternalProcessResult(result, outputFile);
	}
####Implementación ParallelTaskProcessor.RunExternalMergeProcess

Se le llama para la tarea de combinación. Debe invocar la aplicación para combinar los resultados de las tareas anteriores, de la forma adecuada para la aplicación, y devolver el resultado combinado.

La implementación de RunExternalMergeProcess es muy similar a RunExternalTaskProcess, salvo que:

-	RunExternalMergeProcess consume las salidas de las tareas anteriores, en lugar de archivos de entrada del usuario. Por lo tanto, debería resolver los nombres de los archivos que desea procesar basándose en el identificador de tarea, y no en la colección Task.RequiredFiles.
-	RunExternalMergeProcess devuelve un archivo JobOutput y, opcionalmente, un archivo JobPreview.


El código siguiente muestra una implementación sencilla de ParallelTaskProcessor.RunExternalMergeProcess.

	protected override JobResult RunExternalMergeProcess(
	    ITask mergeTask,
	    TaskExecutionSettings settings)
	{
	    var outputFile =  "output.zip";

	    var exePath =  ExecutablePath(@"application\application.exe");
	    var arguments = String.Format("a -application {0} *.jpg", outputFile);

	    new ExternalProcess {
	        CommandPath = exePath,
	        Arguments = arguments,
	        WorkingDirectory = LocalStoragePath
	    }.Run();

	    return new JobResult {
	        OutputFile = outputFile
	    };
	}

###Envío de trabajos a Aplicaciones de Lote
Un trabajo describe una carga de trabajo que se ejecutará y debe incluir toda la información sobre la carga de trabajo excepto el contenido del archivo. Por ejemplo, el trabajo contiene opciones de configuración que fluyen desde el cliente a través del divisor de trabajo y las tareas. Los ejemplos proporcionados en MSDN muestran cómo enviar trabajos y proporcionan varios clientes, incluyendo un portal web y un cliente de línea de comandos.


<!--Anchors-->


<!--Image references-->
[1]: ./media/batch-dotnet-get-started/batch-dotnet-get-started-01.jpg
[2]: ./media/batch-dotnet-get-started/batch-dotnet-get-started-02.jpg
[3]: ./media/batch-dotnet-get-started/batch-dotnet-get-started-03.jpg
[4]: ./media/batch-dotnet-get-started/batch-dotnet-get-started-04.jpg

<!---HONumber=GIT-SubDir-->