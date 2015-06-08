<properties 
	pageTitle="Crear, supervisar y administrar factorías de datos de Azure mediante el SDK de la factoría de datos" 
	description="Aprenda a crear, supervisar y administrar los generadores de datos de Azure mediante el SDK del generador de datos mediante programación." 
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
	ms.date="04/14/2015" 
	ms.author="spelluru"/>

# Crear, supervisar y administrar factorías de datos de Azure mediante el SDK de .NET de la factoría de datos
## Información general
Puede crear, supervisar y administrar factorías de datos de Azure mediante programación con el SDK de .NET de la factoría de datos. Este artículo contiene un tutorial que puede seguir para crear una aplicación de consola .NET de ejemplo que crea y controla una factoría de datos. Consulte [referencia de biblioteca de clases de generador de datos][adf-class-library-reference] para obtener detalles sobre el SDK de .NET del generador de datos.



## Requisitos previos

- Visual Studio 2012 o 2013
- Descargue e instale [Azure .NET SDK][azure-developer-center]
- Descargue e instale paquetes de NuGet para la factoría de datos de Azure. Las instrucciones están en el tutorial.

## Tutorial
1. Con Visual Studio 2012 o 2013, cree una aplicación de consola .NET de C#.
	<ol type="a">
	<li>Iniciar <b>Visual Studio 2012</b> o <b>Visual Studio 2013</b>.</li>
	<li>Haga clic en <b>archivo</b>, seleccione <b>nuevo</b>, y haga clic en <b>proyecto</b>.</li> 
	<li>Expanda <b>plantillas</b>, y seleccione <b>Visual C#</b>. En este tutorial, se usa C# pero puede usar cualquier lenguaje .NET.</li> 
	<li>Seleccione <b>aplicación de consola</b> en la lista de tipos de proyecto de la derecha.</li>
	<li>Escriba <b>DataFactoryAPITestApp</b> para el <b>nombre</b>.</li> 
	<li>Seleccione <b>C:\ADFGetStarted</b> para el <b>ubicación</b>.</li>
	<li>Haga clic en <b>Aceptar</b> para crear el proyecto.</li>
</ol>
2. Haga clic en <b>herramientas</b>, elija <b>el Administrador de paquetes de NuGet</b>, y haga clic en <b>Package Manager Console</b>.
3.	En el <b>Package Manager Console</b>, ejecute los siguientes comandos uno por uno.</b>. 

		Install-Package Microsoft.Azure.Management.DataFactories –Pre
		Install-Package Microsoft.DataFactories.Runtime –Pre
		Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory
6. Agregue el siguiente **appSetttings** sección a la **App.config** archivo. Los utiliza el método auxiliar: **GetAuthorizationHeader**. 

	Reemplace los valores para **SubscriptionId** y **ActiveDirectoryTenantId** por sus identificadores de inquilino y de suscripción de Azure. Puede obtener estos valores ejecutando **Get-AzureAccount** de Azure PowerShell (puede que necesite iniciar sesión en primer lugar usando Add-AzureAccount).
 
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
6. Agregue el siguiente **con** las instrucciones en el archivo de origen (Program.cs) en el proyecto.

		using System.Threading;
		using System.Configuration;
		using System.Collections.ObjectModel;
				
		using Microsoft.Azure.Management.DataFactories;
		using Microsoft.Azure.Management.DataFactories.Models;
		using Microsoft.IdentityModel.Clients.ActiveDirectory;
		using Microsoft.Azure; 
6. Agregue el código siguiente que crea una instancia de **DataPipelineManagementClient** clase a la **principal** método. Usará este objeto para crear una factoría de datos, un servicio vinculado, tablas de entrada y salida, y una canalización. También lo utilizará para supervisar los segmentos de una tabla en tiempo de ejecución.    

        // create data pipeline management client
        string resourceGroupName = "ADF";
        string dataFactoryName = "APITutorialFactory";

        TokenCloudCredentials aadTokenCredentials =
            new TokenCloudCredentials(
                ConfigurationManager.AppSettings["SubscriptionId"],
                GetAuthorizationHeader());

        Uri resourceManagerUri = new Uri(ConfigurationManager.AppSettings["ResourceManagerEndpoint"]);

        DataPipelineManagementClient client = new DataPipelineManagementClient(aadTokenCredentials, resourceManagerUri);
7. Agregue el siguiente código crea un **factoría de datos** a la **principal** método.

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
8. Agregue el siguiente código crea un **vinculado servicio** a la **principal** método.
	> [AZURE.NOTE]**nombre de cuenta****clave de cuenta****ConnectionString** 

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
9. Agregue el siguiente código crea **tablas de entrada y salidas** a la **principal** método. 

	Tenga en cuenta que el **FolderPath** para el blob de entrada está establecido en **adftutorial /** donde **adftutorial** es el nombre del contenedor en el almacenamiento de blobs. Si este contenedor no existe en el almacenamiento de blobs de Azure, cree un contenedor con este nombre: **adftutorial** y cargar un archivo de texto en el contenedor.
	
	Tenga en cuenta que el elemento FolderPath para el blob de salida se establece en: **adftutorial/apifactoryoutput / {segmentar}** donde **sector** se calcula dinámicamente según el valor de **SliceStart** (fecha y hora de cada sector de inicio).

 
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
10. Agregue el siguiente código **crea y activa una canalización** a la **principal** método. Esta canalización tiene una **CopyActivity** que toma **BlobSource** como origen y **BlobSink** como un receptor. 

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

11. Agregue el siguiente método auxiliar utilizado por el **principal** método para el **programa** clase. Este método abre un cuadro de diálogo que le permite proporcionar **nombre de usuario** y **contraseña** que usar para iniciar sesión en el portal de Azure.
 
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
 
13. Agregue el código siguiente a la **principal** método para obtener el estado de un segmento de datos de la tabla de salida. Solo se espera un segmento en este ejemplo.
 
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

14. Agregue el código siguiente para obtener ejecutar detalles de un segmento del segmento de datos a la **principal** método.

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

15. Compile la aplicación de la consola. Haga clic en **crear** en el menú y haga clic en **Generar solución**.
16. Confirme que hay al menos un archivo en el contenedor de adftutorial del almacenamiento de blobs de Azure. En caso contrario, cree el archivo Emp.txt en el Bloc de notas con el siguiente contenido y cárguelo en el contenedor de adftutorial.

        John, Doe
		Jane, Doe
	 
17. Ejecutar el ejemplo, haga clic en **Depurar** -> **Iniciar depuración** en el menú.
18. Usar el Portal de vista previa de Azure para comprobar que el generador de datos: **APITutorialFactory** se crea con los siguientes artefactos: 
	- Vincular servicio: **LinkedService_AzureStorage** 
	- Tablas: **TableBlobSource** y **TableBlobDestination**.
	- Canalización: **PipelineBlobSample** 
18. Compruebe que se crea un archivo de salida en el **apifactoryoutput** carpeta en el **adftutorial** contenedor.


## Otras referencias

Artículo | Descripción
------ | ---------------
[Referencia del programador de generador de datos de Azure][developer-reference] | La referencia del programador tiene el contenido de referencia completa de la biblioteca de clases. NET, los cmdlets, script JSON, funciones, etc.... 


[data-factory-introduction]: data-factory-introduction.md
[adf-getstarted]: data-factory-get-started.md
[use-onpremises-datasources]: data-factory-use-onpremises-datasources.md
[adf-tutorial]: data-factory-tutorial.md
[use-custom-activities]: data-factory-use-custom-activities.md
[developer-reference]: http://go.microsoft.com/fwlink/?LinkId=516908
 
[adf-class-library-reference]: http://go.microsoft.com/fwlink/?LinkID=521877
[azure-developer-center]: http://azure.microsoft.com/downloads/

<!---HONumber=GIT-SubDir-->