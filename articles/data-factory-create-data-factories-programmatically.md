<properties title="Create, monitor, and manage Azure data factories using Data Factory SDK" pageTitle="Crear, supervisar y administrar factorías de datos de Azure mediante el SDK de la factoría de datos" description="Aprenda a crear, supervisar y administrar factorías de datos de Azure mediante el SDK de Factoría de datos." metaKeywords=""  services="data-factory" solutions=""  documentationCenter="" authors="spelluru" manager="jhubbard" editor="monicar" />

<tags ms.service="data-factory" ms.workload="data-services" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="12/08/2014" ms.author="spelluru" />

# Crear, supervisar y administrar factorías de datos de Azure mediante el SDK de .NET de la factoría de datos
Puede crear, supervisar y administrar factorías de datos de Azure mediante programación con el SDK de .NET de la factoría de datos. Este artículo contiene un tutorial que puede seguir para crear una aplicación de consola .NET de ejemplo que crea y controla una factoría de datos. Vea la [referencia de la biblioteca de clases de la factoría de datos][adf-class-library-reference] para obtener más información sobre el SDK de .NET de la factoría de datos. 

## Tutorial: Crear una factoría de datos de Azure con el SDK de .NET de la factoría de datos

**Requisitos previos:**

- Visual Studio 2012 o 2013
- Descargar e instalar el [SDK de .NET de Windows Azure][azure-developer-center]
- Descargue e instale paquetes de NuGet para la factoría de datos de Azure. Las instrucciones están en el tutorial.

### Paso 1: Creación de una factoría de datos de Azure con el SDK de .NET de la factoría de datos
1. Con Visual Studio 2012 o 2013, cree una aplicación de consola .NET de C#.
	<ol type="a">
		<li>Inicie <b>Visual Studio 2012</b> o <b>Visual Studio 2013</b>.</li>
		<li>Haga clic en <b>Archivo</b>, seleccione <b>Nuevo</b>y haga clic en <b>Project</b>.</li> 
		<li>Expanda <b>Plantillas</b>y seleccione <b>Visual C#</b>. En este tutorial, se usa C# pero puede usar cualquier lenguaje .NET.</li> 
		<li>Seleccione <b>Aplicación de consola</b> en la lista de tipos de proyecto de la derecha.</li>
		<li>Escriba <b>DataFactoryAPITestApp</b> para el <b>Nombre</b>.</li> 
		<li>Seleccione <b>C:\ADFGetStarted</b> para la <b>Ubicación</b>.</li>
		<li>Haga clic en <b>OK</b> para crear el proyecto.</li>
	</ol>
2. Haga clic en <b>Herramientas</b>, seleccione el <b>Administrador de paquetes NuGet</b>y haga clic en la <b>Consola del Administrador de paquetes</b>.
3.	En la clase <b>Consola del Administrador de paquetes</b>, ejecute los siguientes comandos uno por uno.</b>. 

		Install-Package Microsoft.Azure.Management.DataFactories -Pre
		Install-Package Microsoft.DataFactories.Runtime -Pre
		Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory
6. Agregue la siguiente sección **appSetttings** al archivo **App.config**. Estos se usan por el método auxiliar: **GetAuthorizationHeader**. 

	Reemplace los valores para **SubscriptionId** y **ActiveDirectoryTenantId** por sus id. de inquilino y suscripción de Azure. Puede obtener estos valores ejecutando **Get-AzureAccount** desde el PowerShell de Azure (puede que necesite iniciar sesión primero usando Add-AzureAccount).
 
		<appSettings>
		    <!--CSM Prod related values-->
		    <add key="ActiveDirectoryEndpoint" value="https://login.windows.net/" />
		    <add key="ResourceManagerEndpoint" value="https://management.azure.com/" />
		    <add key="WindowsManagementUri" value="https://management.core.windows.net/" />
		    <add key="AdfClientId" value="1950a258-227b-4e31-a9cf-717495945fc2" />
		    <add key="RedirectUri" value="urn:ietf:wg:oauth:2.0:oob" />
		    <!--Make sure to write your own tenenat id and subscription ID here-->
		    <add key="SubscriptionId" value="49fb6e5f-3098-4fb2-ba2f-6d6eed843a65" />
    		<add key="ActiveDirectoryTenantId" value="37330244-7828-4a28-99b7-c8c3a437c7ac" />
		</appSettings>
6. Agregue las siguientes instrucciones **using** al archivo de origen (Program.cs) en el proyecto.

		using System.Threading;
		using System.Configuration;
		using System.Collections.ObjectModel;
				
		using Microsoft.Azure.Management.DataFactories;
		using Microsoft.Azure.Management.DataFactories.Models;
		using Microsoft.IdentityModel.Clients.ActiveDirectory;
		using Microsoft.WindowsAzure; 
6. Agregue el código siguiente que crea una instancia de la clase **DataPipelineManagementClient** al método **Main**. Usará este objeto para crear una factoría de datos, un servicio vinculado, tablas de entrada y salida, y una canalización. También lo utilizará para supervisar los segmentos de una tabla en tiempo de ejecución.    

        // create data pipeline management client
        string resourceGroupName = "ADF";
        string dataFactoryName = "APITutorialFactory";

        TokenCloudCredentials aadTokenCredentials =
            new TokenCloudCredentials(
                ConfigurationManager.AppSettings["SubscriptionId"],
                GetAuthorizationHeader());

        Uri resourceManagerUri = new Uri(ConfigurationManager.AppSettings["ResourceManagerEndpoint"]);

        DataPipelineManagementClient client = new DataPipelineManagementClient(aadTokenCredentials, resourceManagerUri);
7. Add the following code that creates a **data factory** to the **Main** method.

        // create a data factory
        Console.WriteLine("Creating a data factory");
        client.DataFactories.CreateOrUpdate(resourceGroupName,
            new DataFactoryCreateOrUpdateParameters()
            {
                DataFactory = new DataFactory()
                {
                    Name = dataFactoryName,
                    Location = "westus",
                    Properties = new DataFactoryProperties() { }
                }
            }
        );
8. Agregue el siguiente código que crea un **servicio vinculado** al método **Main**. 
	> [WACOM.NOTE] Utilice el **nombre de la cuenta** y la **clave de cuenta** de su cuenta de almacenamiento de Azure para la **ConnectionString**. 

		// create a linked service
        Console.WriteLine("Creating a linked service");
        client.LinkedServices.CreateOrUpdate(resourceGroupName, dataFactoryName,
            new LinkedServiceCreateOrUpdateParameters()
            {
                LinkedService = new LinkedService()
                {
                    Name = "LinkedService-AzureStorage",
                    Properties = new AzureStorageLinkedService()
                    {
                        ConnectionString = "DefaultEndpointsProtocol=https;AccountName=<account name>;AccountKey=account key",
                    }
                }
            }
        );
9. Agregue el siguiente código que crea **tablas de entrada y salida** al método **Main**. 

	Tenga en cuenta que la **FolderPath** para el blob de entrada se establece en **adftutorial /** donde **adftutorial** es el nombre del contenedor en el almacenamiento de blobs. Si este contenedor no existe en el almacenamiento de blobs de Azure, cree un contenedor con este nombre: **adftutorial** y cargue un archivo de texto en el contenedor.
	
	Tenga en cuenta que la FolderPath para el blob de salida se establece en: **adftutorial/apifactoryoutput/{Segmento}** donde **Segmento** se calcula dinámicamente en función del valor de **SliceStart** (fecha y hora de inicio de cada segmento.)  

 
        // create input and output tables
        Console.WriteLine("Creating input and output tables");
        string Table_Source = "TableBlobSource";
        string Table_Destination = "TableBlobDestination";

        client.Tables.CreateOrUpdate(resourceGroupName, dataFactoryName,
            new TableCreateOrUpdateParameters()
            {
                Table = new Table()
                {
                    Name = Table_Source,
                    Properties = new TableProperties()
                    {
                        Location = new AzureBlobLocation()
                        {
                            FolderPath = "adftutorial/",
                            LinkedServiceName = "LinkedService-AzureStorage",
                        },

                        Availability = new Availability()
                        {
                            Frequency = SchedulePeriod.Hour,
                            Interval = 1,
                            WaitOnExternal = new WaitOnExternal()
                        },

                        Policy = new Policy()
                        {
                            Validation = new ValidationPolicy()
                            {
                                MinimumRows = 1
                            }
                        }
                    }
                }
            });

        client.Tables.CreateOrUpdate(resourceGroupName, dataFactoryName,
            new TableCreateOrUpdateParameters()
            {
                Table = new Table()
                {
                    Name = Table_Destination,
                    Properties = new TableProperties()
                    {
                        Location = new AzureBlobLocation()
                        {
                            FolderPath = "adftutorial/apifactoryoutput/{Slice}",
                            PartitionedBy = new Collection<Partition>()
                            {
                                new Partition()
                                {
                                    Name = "Slice",
                                    Value = new DateTimePartitionValue()
                                    {
                                        Date = "SliceStart",
                                        Format = "yyyyMMdd-HH"
                                    }
                                }
                            },
                            LinkedServiceName = "LinkedService-AzureStorage",
                        },

                        Availability = new Availability()
                        {
                            Frequency = SchedulePeriod.Hour,
                            Interval = 1,
                        },
                    }
                }
            });
10. Agregue el siguiente código que **crea y activa una canalización** al método **Main**. Esta canalización tiene una **CopyActivity** que toma **BlobSource** como origen y **BlobSink** como receptor. 

        // create a pipeline
        Console.WriteLine("Creating a pipeline");
        DateTime PipelineActivePeriodStartTime = new DateTime(2014, 8, 9, 0, 0, 0, 0, DateTimeKind.Utc);
        DateTime PipelineActivePeriodEndTime = PipelineActivePeriodStartTime.AddMinutes(60);
        string PipelineName = "PipelineBlobSample";

        client.Pipelines.CreateOrUpdate(resourceGroupName, dataFactoryName,
            new PipelineCreateOrUpdateParameters()
            {
                Pipeline = new Pipeline()
                {
                    Name = PipelineName,
                    Properties = new PipelineProperties()
                    {
                        Description = "Demo Pipeline for data transfer between blobs",

                        // Initial value for pipeline's active period. With this, you won't need to set slice status
                        Start = PipelineActivePeriodStartTime,
                        End = PipelineActivePeriodEndTime,

                        Activities = new List<BaseActivity>()
                        {
                            new CopyActivity()
                            {
                                Name = "BlobToBlob",
                                Inputs = new List<ActivityInput>()
                                {
                                    new ActivityInput()
                                    {
                                        Name = Table_Source,
                                    }
                                },
                        
                                Outputs = new List<ActivityOutput>()
                                {
                                    new ActivityOutput()
                                    {
                                        Name = Table_Destination, 
                                    }
                                },
                     
                                Transformation = new CopyActivityProperties()
                                {
                                    Source = new BlobSource()
                                    {
                                        BlobColumnSeparators = ",",
                                    },
                            
                                    Sink = new BlobSink()
                                    {
                                        WriteBatchSize = 10000,
                                        WriteBatchTimeout = TimeSpan.FromMinutes(10)
                                    },

                                },

                                Policy = new ActivityPolicy()
                                {
                                    ExecutionPriorityOrder = ExecutionPriorityOrder.NewestFirst,
                                    Concurrency = 1,
                                    Retry = 2,
                                    Timeout = TimeSpan.FromMinutes(10),
                                }
                            }

                        },
                    }
                }
            });

11. Agregue el siguiente método auxiliar usado por el método **Main** a la clase **Program**. Este método abre un cuadro de diálogo emergente que le permite proporcionar un **nombre de usuario** y una **contraseña** que se usa para iniciar sesión en el portal de Azure. 
 
		public static string GetAuthorizationHeader()
        {
            AuthenticationResult result = null;
            var thread = new Thread(() =>
            {
                try
                {
                    var context = new AuthenticationContext(ConfigurationManager.AppSettings["ActiveDirectoryEndpoint"] + ConfigurationManager.AppSettings["ActiveDirectoryTenantId"]);

                    result = context.AcquireToken(
                        resource: ConfigurationManager.AppSettings["WindowsManagementUri"],
                        clientId: ConfigurationManager.AppSettings["AdfClientId"],
                        redirectUri: new Uri(ConfigurationManager.AppSettings["RedirectUri"]),
                        promptBehavior: PromptBehavior.Always);
                }
                catch (Exception threadEx)
                {
                    Console.WriteLine(threadEx.Message);
                }
            });

            thread.SetApartmentState(ApartmentState.STA);
            thread.Name = "AcquireTokenThread";
            thread.Start();
            thread.Join();

            if (result != null)
            {
                return result.AccessToken;
            }

            throw new InvalidOperationException("Failed to acquire token");
        }  
 
13. Agregue el código siguiente al método **Main** para obtener el estado de un segmento de datos de la tabla de salida. Solo se espera un segmento en este ejemplo.   
 
        // Pulling status within a timeout threshold
        DateTime start = DateTime.Now;
        bool done = false;

        while (DateTime.Now - start < TimeSpan.FromMinutes(5) && !done)
        {
            Console.WriteLine("Pulling the slice status");
            // wait before the next status check
            Thread.Sleep(1000 * 12);

            var datalistResponse = client.DataSlices.List(resourceGroupName, dataFactoryName, Table_Destination,
                PipelineActivePeriodStartTime.ConvertToISO8601DateTimeString(),
                PipelineActivePeriodEndTime.ConvertToISO8601DateTimeString());

            foreach (DataSlice slice in datalistResponse.DataSlices)
            {
                if (slice.Status == DataSliceStatus.Failed || slice.Status == DataSliceStatus.Ready)
                {
                    Console.WriteLine("Slice execution is done with status: {0}", slice.Status);
                    done = true;
                    break;
                }
                else
                {
                    Console.WriteLine("Slice status is: {0}", slice.Status);
                }
            }
        }

14. Agregue el código siguiente para obtener detalles de ejecución para un segmento de datos en el método **Main**.

        Console.WriteLine("Getting run details of a data slice");

        var datasliceRunListResponse = client.DataSliceRuns.List(resourceGroupName, dataFactoryName, Table_Destination,
                PipelineActivePeriodStartTime.ConvertToISO8601DateTimeString());

        foreach (DataSliceRun run in datasliceRunListResponse.DataSliceRuns)
        {
            Console.WriteLine("Status: \t\t{0}", run.Status);
            Console.WriteLine("DataSliceStart: \t{0}", run.DataSliceStart);
            Console.WriteLine("DataSliceEnd: \t\t{0}", run.DataSliceEnd);
            Console.WriteLine("ActivityId: \t\t{0}", run.ActivityName);
            Console.WriteLine("ProcessingStartTime: \t{0}", run.ProcessingStartTime);
            Console.WriteLine("ProcessingEndTime: \t{0}", run.ProcessingEndTime);
            Console.WriteLine("ErrorMessage: \t{0}", run.ErrorMessage);
        }

        Console.WriteLine("\nPress any key to exit.");
        Console.ReadKey();
    }

15. Compile la aplicación de la consola. Haga clic en **Compilar** en el menú y en **Compilar solución**.
16. Confirme que hay al menos un archivo en el contenedor de adftutorial del almacenamiento de blobs de Azure. En caso contrario, cree el archivo Emp.txt en el Bloc de notas con el siguiente contenido y cárguelo en el contenedor de adftutorial.

        John, Doe
		Jane, Doe
	 
17. Para ejecutar el ejemplo haga clic en **Depurar** -> **Iniciar depuración** en el menú.
18. Utilice el Portal de vista previa de Azure para comprobar que se crea la factoría de datos: **APITutorialFactory** con los siguientes artefactos: 
	- Servicio vinculado: **LinkedService_AzureStorage** 
	- Tablas: **TableBlobSource** y **TableBlobDestination**.
	- Canalización: **PipelineBlobSample** 
18. Compruebe que se crea un archivo de salida en la carpeta **apifactoryoutput** en el contenedor de **adftutorial**.


## Otras referencias

Artículo | Descripción
------ | ---------------
[Introducción a la Factoría de datos de Azure][data-factory-introduction] | Este artículo presenta el servicio Factoría de datos de Azure, los conceptos, el valor que proporciona y los escenarios que admite.
[Introducción a la Factoría de datos de Azure][adf-getstarted] | Este artículo proporciona un tutorial completo que le muestra cómo crear una factoría de datos de Azure de ejemplo que copia datos desde un blob de Azure hasta una base de datos SQL de Azure.
[Habilitar las canalizaciones para trabajar con datos locales][use-onpremises-datasources] | Este artículo tiene un tutorial en el que se muestra cómo copiar datos de una base de datos de SQL Server local a un blob de Azure.
[Tutorial: Mover y procesar los archivos de registro mediante la factoría de datos][adf-tutorial] | Este artículo proporciona un tutorial completo en el que se muestra cómo implementar un escenario en tiempo real mediante el uso de la factoría de datos de Azure para transformar los datos de los archivos de registro en información.
[Usar actividades personalizadas en una factoría de datos][use-custom-activities] | Este artículo ofrece un tutorial con instrucciones detalladas para crear una actividad personalizada y usarla en una canalización. 
[Referencia del desarrollador de la factoría de datos de Azure][developer-reference] | La Referencia del desarrollador tiene el contenido de referencia completo para cmdlets, scripts JSON, funciones, etc. 


[data-factory-introduction]: ../data-factory-introduction
[adf-getstarted]: ../data-factory-get-started
[use-onpremises-datasources]: ../data-factory-use-onpremises-datasources
[adf-tutorial]: ../data-factory-tutorial
[use-custom-activities]: ../data-factory-use-custom-activities
[developer-reference]: http://go.microsoft.com/fwlink/?LinkId=516908
 
[adf-class-library-reference]: http://go.microsoft.com/fwlink/?LinkID=521877
[azure-developer-center]: http://azure.microsoft.com/es-es/downloads/

<!--HONumber=35.2-->
