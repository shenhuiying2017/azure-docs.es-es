<properties 
	pageTitle="Uso de SDK de .NET de administración de Análisis de transmisiones de Azure | Azure" 
	description="Obtenga información sobre cómo usar el SDK de .NET de administración de Análisis de transmisiones" 
	services="stream-analytics" 
	documentationCenter="" 
	authors="mumian" 
	manager="paulettm" 
	editor="cgronlun"/>

<tags 
	ms.service="stream-analytics" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.tgt_pltfrm="na" 
	ms.workload="data-services" 
	ms.date="03/05/2015" 
	ms.author="jgao"/>


# Uso del SDK de .NET de administración de Análisis de transmisiones de Azure

[Este tema es la documentación preliminar y está sujeto a cambios en las futuras versiones.] 

Análisis de transmisiones de Azure es un servicio totalmente administrado que proporciona un procesamiento completo de eventos de baja latencia, alta disponibilidad y escalable a través de la transmisión de datos en la nube. En la versión preliminar, Análisis de transmisiones permite a los clientes configurar trabajos de streaming para analizar las secuencias de datos y realizar análisis casi en tiempo real.  

Este artículo muestra cómo usar el SDK de .NET de administración de Análisis de transmisiones de Azure.


## Requisitos previos
Antes de empezar este artículo, debe tener lo siguiente:

- Instale Visual Studio 2012 o 2013.
- Descargue e instale el [SDK de .NET de Azure](http://azure.microsoft.com/downloads/). 
- Cree un grupo de recursos de Azure en su suscripción. A continuación se muestra un ejemplo de script de Azure PowerShell. Para obtener información sobre Azure PowerShell, consulte [Instalación y configuración de Azure PowerShell](../install-configure-powershell.md);  


		# Configure la sesión de Azure PowerShell para tener acceso al administrador de recursos de Azure
		Switch-AzureMode AzureResourceManager

		# Inicie sesión en su cuenta de Azure
		Add-AzureAccount

		# Seleccione la suscripción de Azure que desea usar para crear el grupo de recursos
		Select-AzureSubscription -SubscriptionName <nombre de suscripción>

		# Cree un grupo de recursos de Azure	
		New-AzureResourceGroup -Name <EL NOMBRE DEL GRUPO DE RECURSOS> -Location <UBICACIÓN>


-	Configure un origen de entrada y un destino de salida para usar. Consulte [Introducción al uso de Análisis de transmisiones de Azure](stream-analytics-get-started.md) para configurar una entrada y/o salida de ejemplo que se utilizarán en este artículo.


## Configuración de un proyecto

1. Cree una aplicación de consola .NET de Visual Studio C#.
2. En la consola del administrador de paquetes, ejecute los siguientes comandos para instalar los paquetes NuGet. El primero es el SDK de .NET de administración de Análisis de transmisiones de Azure. El segundo es el cliente de Azure Active Directory que se usará para autenticación.

		Install-Package Microsoft.Azure.Management.StreamAnalytics -Pre
		Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory

4. Agregue la siguiente sección **appSettings** al archivo App.config:

		<appSettings>
		  <!--CSM Prod related values-->
		  <add key="ActiveDirectoryEndpoint" value="https://login.windows.net/" />
		  <add key="ResourceManagerEndpoint" value="https://management.azure.com/" />
		  <add key="WindowsManagementUri" value="https://management.core.windows.net/" />
		  <add key="AsaClientId" value="1950a258-227b-4e31-a9cf-717495945fc2" />
		  <add key="RedirectUri" value="urn:ietf:wg:oauth:2.0:oob" />
		  <add key="SubscriptionId" value="<SU SUSCRIPCIÓN DE AZURE>" />
		  <add key="ActiveDirectoryTenantId" value="<SU ID DE INQUILINO>" />
		</appSettings>


	Reemplace los valores para **SubscriptionId** y **ActiveDirectoryTenantId** por sus id. de inquilino y suscripción de Azure. Para obtener estos valores, ejecute el siguiente cmdlet de Azure PowerShell:

		Get-AzureAccount

5. Agregue las siguientes instrucciones **using** al archivo de origen (Program.cs) del proyecto:

		using System;
		using System.Configuration;
		using System.Threading;
		using Microsoft.Azure;
		using Microsoft.Azure.Management.StreamAnalytics;
		using Microsoft.Azure.Management.StreamAnalytics.Models;
		using Microsoft.IdentityModel.Clients.ActiveDirectory;

6.	Agregue un método de autenticación auxiliar:

		public static string GetAuthorizationHeader()
		{
		    AuthenticationResult result = null;
		    var thread = new Thread(() =>
		    {
		        try
		        {
		            var context = new AuthenticationContext(
						ConfigurationManager.AppSettings["ActiveDirectoryEndpoint"] +
						ConfigurationManager.AppSettings["ActiveDirectoryTenantId"]);
		
		            result = context.AcquireToken(
		                resource: ConfigurationManager.AppSettings["WindowsManagementUri"],
		                clientId: ConfigurationManager.AppSettings["AsaClientId"],
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
		
		    throw new InvalidOperationException("Error al adquirir el token");
		}  


## Cree un cliente de administración de Análisis de transmisiones

Un objeto **StreamAnalyticsManagementClient** le permite administrar el trabajo y los componentes del trabajo, como la entrada, la salida y la transformación. 

Agregue el siguiente código al comienzo del método **Main**: 

	string resourceGroupName = "<EL NOMBRE DEL GRUPO DE RECURSOS DE AZURE>";
	string streamAnalyticsJobName = "<EL NOMBRE DE TRABAJO DE ANÁLISIS DE TRANSMISIONES>";
	string streamAnalyticsInputName = "<EL NOMBRE DE ENTRADA DEL TRABAJO>";
	string streamAnalyticsOutputName = "<EL NOMBRE DE SALIDA DEL TRABAJO>";
	string streamAnalyticsTransformationName = "<EL NOMBRE DE TRANSFORMACIÓN DEL TRABAJO>";
	
	// Obtenga el token de autenticación
	TokenCloudCredentials aadTokenCredentials =
	    new TokenCloudCredentials(
	        ConfigurationManager.AppSettings["SubscriptionId"],
	        GetAuthorizationHeader());
	
	// Cree el cliente de administración de Análisis de transmisiones
	StreamAnalyticsManagementClient client = new StreamAnalyticsManagementClient(aadTokenCredentials);

El valor de la variable **resourceGroupName** debe ser el mismo nombre del grupo de recursos que creó o eligió en los pasos de requisitos previos.

Las secciones restantes de este artículo supone que este código se encuentra al comienzo del método **Main**.

## Creación de un trabajo de Análisis de transmisiones

El siguiente código crea un trabajo de Análisis de transmisiones bajo el grupo de recursos que ha definido. Agregará una entrada, salida y transformación al trabajo más adelante.

	// Cree un trabajo de Análisis de transmisiones
	JobCreateOrUpdateParameters jobCreateParameters = new JobCreateOrUpdateParameters()
	{
	    Job = new Job()
	    {
	        Name = streamAnalyticsJobName,
	        Location = "<UBICACIÓN>",
	        Properties = new JobProperties()
	        {
	            EventsOutOfOrderPolicy = EventsOutOfOrderPolicy.Adjust,
	            Sku = new Sku()
	            {
	                Name = "Standard"
	            }
	        }
	    }
	};
	
	JobCreateOrUpdateResponse jobCreateResponse = client.StreamingJobs.CreateOrUpdate(resourceGroupName, jobCreateParameters);


## Creación de un origen de entrada de Análisis de transmisiones

El código siguiente crea un origen de entrada de Análisis de transmisiones con el tipo de origen de entrada de blob y la serialización de CSV. Para crear un origen de entrada de centro de eventos, use **EventHubStreamInputDataSource** en lugar de **BlobStreamInputDataSource**. De manera similar, puede personalizar el tipo de serialización del origen de entrada.

	// Cree un origen de entrada de Análisis de transmisiones
	InputCreateOrUpdateParameters jobInputCreateParameters = new InputCreateOrUpdateParameters()
	{
	    Input = new Input()
	    {
	        Name = streamAnalyticsInputName,
	        Properties = new StreamInputProperties()
	        {
	            Serialization = new CsvSerialization
	            {
	                Properties = new CsvSerializationProperties
	                {
	                    Encoding = "UTF8",
	                    FieldDelimiter = ","
	                }
	            },
	            DataSource = new BlobStreamInputDataSource
	            {
	                Properties = new BlobStreamInputDataSourceProperties
	                {
	                    StorageAccounts = new StorageAccount[]
	                    {
	                        new StorageAccount()
	                        {
	                            AccountName = "<EL NOMBRE DE LA CUENTA DE ALMACENAMIENTO>",
	                            AccountKey = "<LA CLAVE DE LA CUENTA DE ALMACENAMIENTO>"
	                        }
	                    },
	                    Container = "samples",
	                    PathPattern = ""
	                }
	            }
	        }
	    }
	};
	
	InputCreateOrUpdateResponse inputCreateResponse = 
		client.Inputs.CreateOrUpdate(resourceGroupName, streamAnalyticsJobName, jobInputCreateParameters);

Los orígenes de entrada están vinculados a un trabajo específico. Para usar el mismo origen de entrada para distintos trabajos, debe llamar nuevamente al método y especificar un nombre de trabajo distinto.


## Prueba del origen de entrada de Análisis de transmisiones

El método **TestConnection** prueba si el trabajo de Análisis de transmisiones puede conectarse al origen de entrada así como otros aspectos específicos para el tipo de origen de entrada. Por ejemplo, en el origen de entrada de blob que creó en un paso anterior, el método comprobará que el par de claves y el nombre de cuenta de almacenamiento se pueden usar para conectarse a la cuenta de almacenamiento, así como para comprobar que existe el contenedor especificado.

	// Pruebe la conexión de origen de entrada
	DataSourceTestConnectionResponse inputTestResponse = 
		client.Inputs.TestConnection(resourceGroupName, streamAnalyticsJobName, streamAnalyticsInputName);

## Creación de un destino de salida de Análisis de transmisiones

La creación de un destino de salida es muy similar a crear un origen de entrada de Análisis de transmisiones. Al igual que los orígenes de entrada, los destinos de salida están vinculados a un trabajo específico. Para usar el mismo destino de salida para distintos trabajos, debe llamar nuevamente al método y especificar un nombre de trabajo distinto.

El siguiente código crea un destino de salida (Base de datos SQL de Azure). Puede personalizar el tipo de datos y/o el tipo de serialización del destino de salida.

	// Cree un destino de salida de Análisis de transmisiones
	OutputCreateOrUpdateParameters jobOutputCreateParameters = new OutputCreateOrUpdateParameters()
	{
	    Output = new Output()
	    {
	        Name = streamAnalyticsOutputName,
	        Properties = new OutputProperties()
	        {
	            DataSource = new SqlAzureOutputDataSource()
	            {
	                Properties = new SqlAzureOutputDataSourceProperties()
	                {
	                    Server = "<EL NOMBRE DEL SERVIDOR DE BASE DE DATOS>",
	                    Database = "<EL NOMBRE DE LA BASE DE DATOS>",
	                    User = "<EL INICIO DE SESIÓN EN LA BASE DE DATOS>",
	                    Password = "<LA CONTRASEÑA DE INICIO DE SESIÓN EN LA BASE DE DATOS>",
	                    Table = "<EL NOMBRE DE TABLA DE LA BASE DE DATOS>"
	                }
	            }
	        }
	    }
	};
	
	OutputCreateOrUpdateResponse outputCreateResponse = 
		client.Outputs.CreateOrUpdate(resourceGroupName, streamAnalyticsJobName, jobOutputCreateParameters);

## Prueba de un destino de salida de Análisis de transmisiones

Un destino de salida de Análisis de transmisiones también tiene el método **TestConnection** para probar conexiones.

	// Pruebe la conexión de destino de salida
	DataSourceTestConnectionResponse outputTestResponse = 
		client.Outputs.TestConnection(resourceGroupName, streamAnalyticsJobName, streamAnalyticsOutputName);

## Creación de una transformación de Análisis de transmisiones

El siguiente código crea una transformación de Análisis de transmisiones con la consulta "select * from Input" y especifica para asignar una unidad de streaming para el trabajo de Análisis de transmisiones. Para obtener más información sobre cómo ajustar las unidades de streaming, consulte [Escalar trabajos de Análisis de transmisiones](stream-analytics-scale-jobs.md).


	// Cree una transformación de Análisis de transmisiones
	TransformationCreateOrUpdateParameters transformationCreateParameters = new TransformationCreateOrUpdateParameters()
	{
	    Transformation = new Transformation()
	    {
	        Name = streamAnalyticsTransformationName,
	        Properties = new TransformationProperties()
	        {
	            StreamingUnits = 1,
	            Query = "select * from Input"
	        }
	    }
	};
	
	var transformationCreateResp = 
		client.Transformations.CreateOrUpdate(resourceGroupName, streamAnalyticsJobName, transformationCreateParameters);

Al igual que la entrada y la salida, una transformación también está vinculada al trabajo de Análisis de transmisiones específico bajo el que se creó.

## Inicio de un trabajo de Análisis de transmisiones
Después de crear un trabajo de Análisis de transmisiones y sus entradas, salidas y transformaciones, puede iniciar el trabajo si llama al método **Start**. 

El siguiente código de ejemplo inicia un trabajo de Análisis de transmisiones con una hora de inicio de salida personalizada definida para el 12 de diciembre de 2012, 12:12:12 UTC:

	// Inicie un trabajo de Análisis de transmisiones
	JobStartParameters jobStartParameters = new JobStartParameters
	{
	    OutputStartMode = OutputStartMode.CustomTime,
	    OutputStartTime = new DateTime(2012, 12, 12, 0, 0, 0, DateTimeKind.Utc)
	};
	
	LongRunningOperationResponse jobStartResponse = client.StreamingJobs.Start(resourceGroupName, streamAnalyticsJobName, jobStartParameters);



## Detención de un trabajo de Análisis de transmisiones
Puede detener un trabajo de Análisis de transmisiones en ejecución si llama al método **Stop**.

	// Detenga un trabajo de Análisis de transmisiones
	LongRunningOperationResponse jobStopResponse = client.StreamingJobs.Stop(resourceGroupName, streamAnalyticsJobName);

## Eliminación de un trabajo de Análisis de transmisiones
El método **Delete** eliminará el trabajo, además de los subrecursos subyacentes, incluidas las entradas, salidas y transformaciones del trabajo.

	// Elimine un trabajo de Análisis de transmisiones
	LongRunningOperationResponse jobDeleteResponse = client.StreamingJobs.Delete(resourceGroupName, streamAnalyticsJobName);


## Pasos siguientes

- [Introducción a Análisis de transmisiones de Azure][stream.analytics.introduction]
- [Introducción al uso de Análisis de transmisiones de Azure][stream.analytics.get.started]
- [Escalar trabajos de Análisis de transmisiones de Azure][stream.analytics.scale.jobs]
- [Limitaciones y problemas conocidos de Análisis de transmisiones de Azure][stream.analytics.limitations]
- [Referencia de lenguaje de consulta de Análisis de transmisiones de Azure][stream.analytics.query.language.reference]
- [Referencia de API de REST de administración de Análisis de transmisiones de Azure][stream.analytics.rest.api.reference] 



<!--Image references-->
[5]: ./media/markdown-template-for-new-articles/octocats.png
[6]: ./media/markdown-template-for-new-articles/pretty49.png
[7]: ./media/markdown-template-for-new-articles/channel-9.png


<!--Link references-->
[azure.blob.storage]: http://azure.microsoft.com/documentation/services/storage/
[azure.blob.storage.use]: http://azure.microsoft.com/documentation/articles/storage-dotnet-how-to-use-blobs/

[azure.event.hubs]: http://azure.microsoft.com/services/event-hubs/
[azure.event.hubs.developer.guide]: http://msdn.microsoft.com/library/azure/dn789972.aspx

[stream.analytics.query.language.reference]: http://go.microsoft.com/fwlink/?LinkID=513299
[stream.analytics.forum]: http://go.microsoft.com/fwlink/?LinkId=512151

[stream.analytics.introduction]: stream-analytics-introduction.md
[stream.analytics.get.started]: stream-analytics-get-started.md
[stream.analytics.developer.guide]: ../stream-analytics-developer-guide.md
[stream.analytics.scale.jobs]: stream-analytics-scale-jobs.md
[stream.analytics.limitations]: ../stream-analytics-limitations.md
[stream.analytics.query.language.reference]: http://go.microsoft.com/fwlink/?LinkID=513299
[stream.analytics.rest.api.reference]: http://go.microsoft.com/fwlink/?LinkId=517301


<!--HONumber=52--> 