<properties 
	pageTitle="Crear, supervisar y administrar factorías de datos de Azure mediante el SDK de la factoría de datos | Microsoft Azure" 
	description="Conozca cómo puede crear, supervisar y administrar factorías de datos de Azure mediante programación con el SDK de Factoría de datos." 
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
	ms.date="09/14/2016" 
	ms.author="spelluru"/>

# Crear, supervisar y administrar factorías de datos de Azure mediante el SDK de .NET de la factoría de datos
## Información general
Puede crear, supervisar y administrar factorías de datos de Azure mediante programación con el SDK de .NET de la factoría de datos. Este artículo contiene un tutorial que puede seguir para crear una aplicación de consola .NET de ejemplo que crea y controla una factoría de datos. Vea la [Referencia de la biblioteca de clases de Factoría de datos](https://msdn.microsoft.com/library/mt415893.aspx) para obtener más información sobre el SDK de .NET de la factoría de datos.

## Requisitos previos

- Visual Studio 2012, 2013 o 2015
- Descargue e instale el [SDK de .NET de Azure](http://azure.microsoft.com/downloads/).
- Agregue una aplicación cliente nativa a Azure Active Directory. Para conocer los pasos para agregar la aplicación, consulte [Integración de aplicaciones con Azure Active Directory](../active-directory/active-directory-integrating-applications.md). Anote los valores de **ID DE CLIENTE** y **URI DE REDIRECCIÓN** en la página **CONFIGURAR**.
- Obtenga su **id. de suscripción** e **id. de inquilino** de Azure. Consulte [Obtención de los id. de suscripción y de inquilino de Azure](#get-azure-subscription-and-tenant-ids) para recibir instrucciones.
- Descargue e instale paquetes de NuGet para la factoría de datos de Azure. Las instrucciones están en el tutorial.

## Tutorial
1. Con Visual Studio 2012 o 2013, cree una aplicación de consola .NET de C#.
	1. Inicie **Visual Studio 2012/2013/2015**.
	2. Haga clic en **Archivo**, seleccione **Nuevo** y, luego, haga clic en **Proyecto**.
	3. Expanda **Plantillas** y seleccione **Visual C#**. En este tutorial, se usa C# pero puede usar cualquier lenguaje .NET.
	4. Seleccione **Aplicación de consola** en la lista de tipos de proyecto de la derecha.
	5. Escriba **DataFactoryAPITestApp** para el **nombre**.
	6. Seleccione **C:\\ADFGetStarted** para la **ubicación**.
	7. Haga clic en **Aceptar** para crear el proyecto.
2. Haga clic en **Herramientas**, seleccione **Administrador de paquetes NuGet** y haga clic en **Consola del Administrador de paquetes**.
3.	En **Consola del Administrador de paquetes**, ejecute los siguientes comandos uno a uno.

			Install-Package Microsoft.Azure.Management.DataFactories
			Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory -Version 2.19.208020213
4. Agregue la siguiente sección **appSetttings** al archivo **App.config**. Estos valores de configuración se usan en el método **GetAuthorizationHeader**.

	> [AZURE.IMPORTANT] Reemplace los valores de **AdfClientId**, **RedirectUri**, **SubscriptionId** y **ActiveDirectoryTenantId** por los suyos propios.
 
		<appSettings>
		    <add key="ActiveDirectoryEndpoint" value="https://login.windows.net/" />
		    <add key="ResourceManagerEndpoint" value="https://management.azure.com/" />
		    <add key="WindowsManagementUri" value="https://management.core.windows.net/" />

		    <!-- Replace the following values with your own -->
		    <add key="AdfClientId" value="Your AAD application ID" />
		    <add key="RedirectUri" value="Your AAD application's redirect URI" />
		    <add key="SubscriptionId" value="your subscription ID" />
    		<add key="ActiveDirectoryTenantId" value="your tenant ID" />
		</appSettings>
5. Agregue las siguientes instrucciones **using** al archivo de origen (Program.cs) en el proyecto.

		using System.Threading;
		using System.Configuration;
		using System.Collections.ObjectModel;
		
		using Microsoft.Azure.Management.DataFactories;
		using Microsoft.Azure.Management.DataFactories.Models;
		using Microsoft.Azure.Management.DataFactories.Common.Models;
		
		using Microsoft.IdentityModel.Clients.ActiveDirectory;
		using Microsoft.Azure;
6. Agregue el código siguiente que crea una instancia de la clase **DataPipelineManagementClient** al método **Main**. Este objeto se usa para crear una factoría de datos, un servicio vinculado, conjuntos de datos de entrada y salida, y una canalización. También se usa para supervisar los segmentos de un conjunto de datos en tiempo de ejecución.

        // create data factory management client
        string resourceGroupName = "resourcegroupname";
        string dataFactoryName = "APITutorialFactorySP";

        TokenCloudCredentials aadTokenCredentials =
            new TokenCloudCredentials(
                ConfigurationManager.AppSettings["SubscriptionId"],
                GetAuthorizationHeader());

        Uri resourceManagerUri = new Uri(ConfigurationManager.AppSettings["ResourceManagerEndpoint"]);

        DataFactoryManagementClient client = new DataFactoryManagementClient(aadTokenCredentials, resourceManagerUri);

	> [AZURE.NOTE] Reemplace el **resourcegroupname** por el nombre de su grupo de recursos de Azure. Puede crear un grupo de recursos con el cmdlet [New-AzureResourceGroup](https://msdn.microsoft.com/library/Dn654594.aspx).

7. Agregue el siguiente código que crea una **factoría de datos** en el método **Main**.

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

	> [AZURE.NOTE] Use el **nombre de cuenta** y la **clave de cuenta** de su cuenta de almacenamiento de Azure para la **ConnectionString**.

        // create a linked service
        Console.WriteLine("Creating a linked service");
        client.LinkedServices.CreateOrUpdate(resourceGroupName, dataFactoryName,
            new LinkedServiceCreateOrUpdateParameters()
            {
                LinkedService = new LinkedService()
                {
                    Name = "LinkedService-AzureStorage",
                    Properties = new LinkedServiceProperties
                    (
                        new AzureStorageLinkedService("DefaultEndpointsProtocol=https;AccountName=<account name>;AccountKey=<account key>")
                    )
                }
            }
        );
9. Agregue el siguiente código que crea **conjuntos de datos de entrada y salida** en el método **Main**.

	Tenga en cuenta que el valor de **FolderPath** para el blob de entrada está establecido en **adftutorial/**, donde **adftutorial** es el nombre del contenedor en el almacenamiento de blobs. Si este contenedor no existe en el almacenamiento de blobs de Azure, cree un contenedor con este nombre: **adftutorial** y cargue un archivo de texto al contenedor.
	
	Tenga en cuenta que el valor de FolderPath para el blob de salida está establecido en: **adftutorial/apifactoryoutput/{Segmento}**, donde **Segmento** se calcula dinámicamente en función del valor de **SliceStart** (fecha y hora de inicio de cada segmento).
 
        // create input and output datasets
        Console.WriteLine("Creating input and output datasets");
        string Dataset_Source = "DatasetBlobSource";
        string Dataset_Destination = "DatasetBlobDestination";

        client.Datasets.CreateOrUpdate(resourceGroupName, dataFactoryName,
            new DatasetCreateOrUpdateParameters()
            {
                Dataset = new Dataset()
                {
                    Name = Dataset_Source,
                    Properties = new DatasetProperties()
                    {
                        LinkedServiceName = "LinkedService-AzureStorage",
                        TypeProperties = new AzureBlobDataset()
                        {
                            FolderPath = "adftutorial/",
                            FileName = "emp.txt"
                        },
                        External = true,
                        Availability = new Availability()
                        {
                            Frequency = SchedulePeriod.Hour,
                            Interval = 1,
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

        client.Datasets.CreateOrUpdate(resourceGroupName, dataFactoryName,
            new DatasetCreateOrUpdateParameters()
            {
                Dataset = new Dataset()
                {
                    Name = Dataset_Destination,
                    Properties = new DatasetProperties()
                    {

                        LinkedServiceName = "LinkedService-AzureStorage",
                        TypeProperties = new AzureBlobDataset()
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
                            }
                        },

                        Availability = new Availability()
                        {
                            Frequency = SchedulePeriod.Hour,
                            Interval = 1,
                        },
                    }
                }
            });

10. Agregue el siguiente código que **crea y activa una canalización** al método **Principal**. Esta canalización tiene una **CopyActivity** que toma **BlobSource** como origen y **BlobSink** como receptor.

	La actividad de copia realiza el movimiento de datos en Data Factory de Azure. La actividad funciona con un servicio disponible de forma global que puede copiar datos entre varios almacenes de datos de forma segura, confiable y escalable. Consulte el artículo [Actividades de movimiento de datos](data-factory-data-movement-activities.md) para obtener más información sobre la actividad de copia.

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

                        Activities = new List<Activity>()
                        {                                
                            new Activity()
                            {   
                                Name = "BlobToBlob",
                                Inputs = new List<ActivityInput>()
                                {
                                    new ActivityInput() {
                                        Name = Dataset_Source
                                    }
                                },
                                Outputs = new List<ActivityOutput>()
                                {
                                    new ActivityOutput()
                                    {
                                        Name = Dataset_Destination
                                    }
                                },
                                TypeProperties = new CopyActivity()
                                {
                                    Source = new BlobSource(),
                                    Sink = new BlobSink()
                                    {
                                        WriteBatchSize = 10000,
                                        WriteBatchTimeout = TimeSpan.FromMinutes(10)
                                    }
                                }
                            }

                        },
                    }
                }
            });

11. Agregue el siguiente método auxiliar usado por el método **Main** a la clase **Program**. Este método abre un cuadro de diálogo que le permite proporcionar un **nombre de usuario** y una **contraseña** para iniciar sesión en Azure Portal.
 
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
 
13. Agregue el código siguiente al método **Main** para obtener el estado de un segmento de datos del conjunto de datos de salida. Solo se espera un segmento en este ejemplo.
 
        // Pulling status within a timeout threshold
        DateTime start = DateTime.Now;
        bool done = false;

        while (DateTime.Now - start < TimeSpan.FromMinutes(5) && !done)
        {
            Console.WriteLine("Pulling the slice status");
            // wait before the next status check
            Thread.Sleep(1000 * 12);

            var datalistResponse = client.DataSlices.List(resourceGroupName, dataFactoryName, Dataset_Destination,
                new DataSliceListParameters()
                {
                    DataSliceRangeStartTime = PipelineActivePeriodStartTime.ConvertToISO8601DateTimeString(),
                    DataSliceRangeEndTime = PipelineActivePeriodEndTime.ConvertToISO8601DateTimeString()
                });

            foreach (DataSlice slice in datalistResponse.DataSlices)
            {
                if (slice.State == DataSliceState.Failed || slice.State == DataSliceState.Ready)
                {
                    Console.WriteLine("Slice execution is done with status: {0}", slice.State);
                    done = true;
                    break;
                }
                else
                {
                    Console.WriteLine("Slice status is: {0}", slice.State);
                }
            }
        }

13. **(Opcional)** Agregue el código siguiente para obtener detalles de ejecución de un segmento de datos en el método **Main**.

        Console.WriteLine("Getting run details of a data slice");

		// give it a few minutes for the output slice to be ready
        Console.WriteLine("\nGive it a few minutes for the output slice to be ready and press any key.");
        Console.ReadKey();

        var datasliceRunListResponse = client.DataSliceRuns.List(
                resourceGroupName,
                dataFactoryName,
                Dataset_Destination,
                new DataSliceRunListParameters()
                {
                    DataSliceStartTime = PipelineActivePeriodStartTime.ConvertToISO8601DateTimeString()
                }
            );
        
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

14. En el Explorador de soluciones, expanda el proyecto (**DataFactoryAPITestApp**), haga clic con el botón derecho en **Referencias**, y haga clic en **Agregar referencia**. Seleccione la casilla del ensamblado de `System.Configuration` y haga clic en **Aceptar**.
15. Compile la aplicación de la consola. Haga clic en **Compilar** en el menú y en **Compilar solución**.
16. Confirme que hay al menos un archivo en el contenedor de adftutorial del almacenamiento de blobs de Azure. En caso contrario, cree el archivo Emp.txt en el Bloc de notas con el siguiente contenido y cárguelo en el contenedor de adftutorial.

        John, Doe
		Jane, Doe 
17. Para ejecutar el ejemplo haga clic en **Depurar** -> **Iniciar depuración en el menú**. Cuando vea **Obteniendo los detalles de ejecución de un segmento de datos**, espere unos minutos y presione **ENTRAR**.
18. Use el Portal de Azure para comprobar que la factoría de datos **APITutorialFactory** se crea con los siguientes artefactos:
	- Servicio vinculado: **LinkedService\_AzureStorage**
	- Conjunto de datos: **DatasetBlobSource** y **DatasetBlobDestination**.
	- Canalización: **PipelineBlobSample**
18. Compruebe que se crea un archivo de salida en la carpeta **apifactoryoutput** en el contenedor de **adftutorial**.

## Inicio de sesión sin cuadro de diálogo emergente 
El código de ejemplo anterior de este tutorial inicia un cuadro de diálogo para escribir las credenciales de Azure. Si necesita iniciar sesión mediante programación sin usar un cuadro de diálogo, consulte [Autenticación de una entidad de servicio con Azure Resource Manager](resource-group-authenticate-service-principal.md#authenticate-service-principal-with-certificate---powershell).

> [AZURE.IMPORTANT] Agregue una aplicación web a Azure Active Directory y anote el id. de cliente y el secreto de cliente de la aplicación.

### Ejemplo

Cree el método GetAuthorizationHeaderNoPopup.

    public static string GetAuthorizationHeaderNoPopup()
    {
        var authority = new Uri(new Uri("https://login.windows.net"), ConfigurationManager.AppSettings["ActiveDirectoryTenantId"]);
        var context = new AuthenticationContext(authority.AbsoluteUri);
        var credential = new ClientCredential(ConfigurationManager.AppSettings["AdfClientId"], ConfigurationManager.AppSettings["AdfClientSecret"]);
        AuthenticationResult result = context.AcquireTokenAsync(ConfigurationManager.AppSettings["WindowsManagementUri"], credential).Result;
        if (result != null)
            return result.AccessToken;

        throw new InvalidOperationException("Failed to acquire token");
    }

Reemplace la llamada a **GetAuthorizationHeader** por una llamada a **GetAuthorizationHeaderNoPopup** en la función **Main**:

        TokenCloudCredentials aadTokenCredentials =
            new TokenCloudCredentials(
            ConfigurationManager.AppSettings["SubscriptionId"],
            GetAuthorizationHeaderNoPopup());

Así es como puede crear la aplicación de Active Directory, entidad de servicio, y asignarla al rol de Colaborador de Data Factory:

1. Cree la aplicación de AD.

		$azureAdApplication = New-AzureRmADApplication -DisplayName "MyADAppForADF" -HomePage "https://www.contoso.org" -IdentifierUris "https://www.myadappforadf.org/example" -Password "Pass@word1"

2. Cree la entidad de servicio de AD.

		New-AzureRmADServicePrincipal -ApplicationId $azureAdApplication.ApplicationId

3. Agregue la entidad de servicio al rol de Colaborador de Data Factory.

		New-AzureRmRoleAssignment -RoleDefinitionName "Data Factory Contributor" -ServicePrincipalName $azureAdApplication.ApplicationId.Guid

4. Obtenga el identificador de aplicación.

		$azureAdApplication


Anote el id. de aplicación y la contraseña (secreto de cliente) y úselos en el tutorial.

## Obtención de los id. de suscripción y de inquilino de Azure
Si no tiene instalada la última versión de PowerShell en su máquina, siga las instrucciones del artículo [Cómo instalar y configurar Azure PowerShell](../powershell-install-configure.md) para hacerlo.

1. Inicie Azure PowerShell y ejecute el comando siguiente.
2. Ejecute el siguiente comando y escriba el nombre de usuario y la contraseña que utiliza para iniciar sesión en el Portal de Azure.

		Login-AzureRmAccount

	Si solo tiene una suscripción de Azure asociada con esta cuenta, no es necesario que realice los dos pasos siguientes.
3. Ejecute el siguiente comando para ver todas las suscripciones para esta cuenta.

		Get-AzureRmSubscription
4. Ejecute el comando siguiente para seleccionar la suscripción con la que desea trabajar. Reemplace **NameOfAzureSubscription** por el nombre de su suscripción de Azure.

		Get-AzureRmSubscription -SubscriptionName NameOfAzureSubscription | Set-AzureRmContext 

Anote los valores de **SubscriptionId** y **TenantId**.

<!---HONumber=AcomDC_0914_2016-->