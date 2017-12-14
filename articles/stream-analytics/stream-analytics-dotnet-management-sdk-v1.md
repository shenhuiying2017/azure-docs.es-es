---
title: "SDK v1.x de .NET de administración para Azure Stream Analytics | Microsoft Docs"
description: "Introducción al uso del SDK de .NET de administración de Stream Analytics Aprenda a configurar y ejecutar trabajos de análisis. Cree un proyecto, entradas, salidas y transformaciones."
keywords: "SDK de .NET, API de análisis"
services: stream-analytics
documentationcenter: 
author: samacha
manager: jhubbard
editor: cgronlun
ms.assetid: 5e93de87-0c6f-4f4b-be98-08d63f832897
ms.service: stream-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-services
ms.date: 03/06/2017
ms.author: samacha
ms.openlocfilehash: 7f434f1fe600877d1a12174ae84aaa47e878a055
ms.sourcegitcommit: b07d06ea51a20e32fdc61980667e801cb5db7333
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/08/2017
---
# <a name="management-net-sdk-v1x-set-up-and-run-analytics-jobs-using-the-azure-stream-analytics-api-for-net"></a>SDK v1.x de .NET de administración: configuración y ejecución de trabajos de análisis con la API de Azure Stream Analytics para .NET
Aprenda a configurar y ejecutar trabajos de análisis con la API de Stream Analytics para .NET mediante el SDK de .NET de administración. Configure un proyecto, cree orígenes de entrada y salida, transformaciones, e inicie y detenga trabajos. En los trabajos de análisis puede transmitir datos desde el almacenamiento de blobs o desde un centro de eventos.

Consulte la [documentación de referencia de administración de la API de Stream Analytics para .NET](https://msdn.microsoft.com/library/azure/dn889315.aspx).

Azure Stream Analytics es un servicio totalmente administrado que proporciona un procesamiento completo de eventos de baja latencia, alta disponibilidad y escalable a través de la transmisión de datos en la nube. Stream Analytics permite a los clientes configurar trabajos de streaming para analizar flujos de datos y realizar análisis casi en tiempo real.  

> [!NOTE]
> El código de ejemplo de este artículo todavía usa una versión heredada (1.x) del SDK de .NET de administración de Azure Stream Analytics. Para código de ejemplo con la versión actualizada del SDK, vea [Uso del SDK de .NET de administración para Stream Analytics](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-dotnet-management-sdk).

## <a name="prerequisites"></a>Requisitos previos
Antes de empezar este artículo, debe tener lo siguiente:

* Instale Visual Studio 2017 o 2015.
* Descargue e instale el [SDK de .NET de Azure](https://azure.microsoft.com/downloads/).
* Cree un grupo de recursos de Azure en su suscripción. A continuación se muestra un ejemplo de script de Azure PowerShell. Para obtener información sobre Azure PowerShell, consulte [Instalación y configuración de Azure PowerShell](/powershell/azure/overview).  

        # Log in to your Azure account
        Add-AzureAccount

        # Select the Azure subscription you want to use to create the resource group
        Select-AzureSubscription -SubscriptionName <subscription name>

            # If Stream Analytics has not been registered to the subscription, remove the remark symbol (#) to run the Register-AzureRMProvider cmdlet to register the provider namespace
            #Register-AzureRMProvider -Force -ProviderNamespace 'Microsoft.StreamAnalytics'

        # Create an Azure resource group
        New-AzureResourceGroup -Name <YOUR RESOURCE GROUP NAME> -Location <LOCATION>


* Configure un origen de entrada y un destino de salida para usar. Para más instrucciones, vea [Agregar entradas](stream-analytics-add-inputs.md) para configurar una entrada de muestra y [Agregar salidas](stream-analytics-add-outputs.md) para configurar una salida de muestra.

## <a name="set-up-a-project"></a>Configuración de un proyecto
Para crear un trabajo de análisis que use la API de Stream Analytics para. NET, configure primero el proyecto.

1. Cree una aplicación de consola .NET de Visual Studio C#.
2. En la consola del administrador de paquetes, ejecute los siguientes comandos para instalar los paquetes NuGet. El primero es el SDK de .NET de administración de Azure Stream Analytics. El segundo es el cliente de Azure Active Directory que se usará para autenticación.
   
        Install-Package Microsoft.Azure.Management.StreamAnalytics -Version 1.8.3
        Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory -Version 2.28.4
3. Agregue la siguiente sección **appSettings** al archivo App.config:
   
        <appSettings>
          <!--CSM Prod related values-->
          <add key="ActiveDirectoryEndpoint" value="https://login.microsoftonline.com/" />
          <add key="ResourceManagerEndpoint" value="https://management.azure.com/" />
          <add key="WindowsManagementUri" value="https://management.core.windows.net/" />
          <add key="AsaClientId" value="1950a258-227b-4e31-a9cf-717495945fc2" />
          <add key="RedirectUri" value="urn:ietf:wg:oauth:2.0:oob" />
          <add key="SubscriptionId" value="YOUR AZURE SUBSCRIPTION" />
          <add key="ActiveDirectoryTenantId" value="YOU TENANT ID" />
        </appSettings>

    Reemplace los valores para **SubscriptionId** y **ActiveDirectoryTenantId** por sus identificadores de inquilino y de suscripción de Azure. Para obtener estos valores, ejecute el siguiente cmdlet de Azure PowerShell:

        Get-AzureAccount

4. Agregue la siguiente referencia al archivo .csproj:

        <Reference Include="System.Configuration" />

1. Agregue las siguientes instrucciones **using** al archivo de origen (Program.cs) en el proyecto:
   
        using System;
        using System.Configuration;
        using System.Threading.Tasks;
        
        using Microsoft.Azure;
        using Microsoft.Azure.Management.StreamAnalytics;
        using Microsoft.Azure.Management.StreamAnalytics.Models;
        using Microsoft.IdentityModel.Clients.ActiveDirectory;
2. Agregue un método de autenticación auxiliar:

   ```   
   private static async Task<string> GetAuthorizationHeader()
   {
       var context = new AuthenticationContext(
           ConfigurationManager.AppSettings["ActiveDirectoryEndpoint"] +
           ConfigurationManager.AppSettings["ActiveDirectoryTenantId"]);

        AuthenticationResult result = await context.AcquireTokenASync(
           resource: ConfigurationManager.AppSettings["WindowsManagementUri"],
           clientId: ConfigurationManager.AppSettings["AsaClientId"],
           redirectUri: new Uri(ConfigurationManager.AppSettings["RedirectUri"]),
           promptBehavior: PromptBehavior.Always);

        if (result != null)
            return result.AccessToken;

       throw new InvalidOperationException("Failed to acquire token");
   }
   ```  

## <a name="create-a-stream-analytics-management-client"></a>Cree un cliente de administración de Stream Analytics
Un objeto **StreamAnalyticsManagementClient** le permite administrar el trabajo y los componentes del trabajo, como la entrada, la salida y la transformación.

Agregue el siguiente código al comienzo del método **Main** :

    string resourceGroupName = "<YOUR AZURE RESOURCE GROUP NAME>";
    string streamAnalyticsJobName = "<YOUR STREAM ANALYTICS JOB NAME>";
    string streamAnalyticsInputName = "<YOUR JOB INPUT NAME>";
    string streamAnalyticsOutputName = "<YOUR JOB OUTPUT NAME>";
    string streamAnalyticsTransformationName = "<YOUR JOB TRANSFORMATION NAME>";

    // Get authentication token
    TokenCloudCredentials aadTokenCredentials = new TokenCloudCredentials(
        ConfigurationManager.AppSettings["SubscriptionId"],
        GetAuthorizationHeader().Result);

    // Create Stream Analytics management client
    StreamAnalyticsManagementClient client = new StreamAnalyticsManagementClient(aadTokenCredentials);

El valor de la variable **resourceGroupName** debe ser el mismo que el nombre del grupo de recursos que creó o eligió en los pasos de requisitos previos.

Para automatizar el aspecto de la presentación de credenciales de creación del trabajo, consulte [Autenticación de una entidad de servicio con el Administrador de recursos de Azure](../azure-resource-manager/resource-group-authenticate-service-principal.md).

Las secciones restantes de este artículo suponen que este código se encuentra al comienzo del método **Main** .

## <a name="create-a-stream-analytics-job"></a>Creación de un trabajo de Stream Analytics
El siguiente código crea un trabajo de Stream Analytics bajo el grupo de recursos que ha definido. Agregará una entrada, salida y transformación al trabajo más adelante.

    // Create a Stream Analytics job
    JobCreateOrUpdateParameters jobCreateParameters = new JobCreateOrUpdateParameters()
    {
        Job = new Job()
        {
            Name = streamAnalyticsJobName,
            Location = "<LOCATION>",
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


## <a name="create-a-stream-analytics-input-source"></a>Creación de un origen de entrada de Stream Analytics
El código siguiente crea un origen de entrada de Stream Analytics con el tipo de origen de entrada de blob y la serialización de CSV. Para crear un origen de entrada de centro de eventos, use **EventHubStreamInputDataSource** en lugar de **BlobStreamInputDataSource**. De manera similar, puede personalizar el tipo de serialización del origen de entrada.

    // Create a Stream Analytics input source
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
                                AccountName = "<YOUR STORAGE ACCOUNT NAME>",
                                AccountKey = "<YOUR STORAGE ACCOUNT KEY>"
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

Los orígenes de entrada, ya sean desde el almacenamiento de blobs o un centro de eventos, están vinculados a un trabajo específico. Para usar el mismo origen de entrada para distintos trabajos, debe llamar nuevamente al método y especificar un nombre de trabajo distinto.

## <a name="test-a-stream-analytics-input-source"></a>Prueba del origen de entrada de Stream Analytics
El método **TestConnection** prueba si el trabajo de Stream Analytics puede conectarse al origen de entrada así como otros aspectos específicos para el tipo de origen de entrada. Por ejemplo, en el origen de entrada de blob que creó en un paso anterior, el método comprobará que el par de claves y el nombre de cuenta de almacenamiento se pueden usar para conectarse a la cuenta de almacenamiento, así como para comprobar que existe el contenedor especificado.

    // Test input source connection
    DataSourceTestConnectionResponse inputTestResponse =
        client.Inputs.TestConnection(resourceGroupName, streamAnalyticsJobName, streamAnalyticsInputName);

## <a name="create-a-stream-analytics-output-target"></a>Creación de un destino de salida de Stream Analytics
La creación de un destino de salida es muy similar a crear un origen de entrada de Stream Analytics. Al igual que los orígenes de entrada, los destinos de salida están vinculados a un trabajo específico. Para usar el mismo destino de salida para distintos trabajos, debe llamar nuevamente al método y especificar un nombre de trabajo distinto.

El siguiente código crea un destino de salida (Base de datos SQL de Azure). Puede personalizar el tipo de datos y/o el tipo de serialización del destino de salida.

    // Create a Stream Analytics output target
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
                        Server = "<YOUR DATABASE SERVER NAME>",
                        Database = "<YOUR DATABASE NAME>",
                        User = "<YOUR DATABASE LOGIN>",
                        Password = "<YOUR DATABASE LOGIN PASSWORD>",
                        Table = "<YOUR DATABASE TABLE NAME>"
                    }
                }
            }
        }
    };

    OutputCreateOrUpdateResponse outputCreateResponse =
        client.Outputs.CreateOrUpdate(resourceGroupName, streamAnalyticsJobName, jobOutputCreateParameters);

## <a name="test-a-stream-analytics-output-target"></a>Prueba de un destino de salida de Stream Analytics
Un destino de salida de Stream Analytics también tiene el método **TestConnection** para probar conexiones.

    // Test output target connection
    DataSourceTestConnectionResponse outputTestResponse =
        client.Outputs.TestConnection(resourceGroupName, streamAnalyticsJobName, streamAnalyticsOutputName);

## <a name="create-a-stream-analytics-transformation"></a>Creación de una transformación de Stream Analytics
El siguiente código crea una transformación de Stream Analytics con la consulta "select * from Input" y especifica para asignar una unidad de streaming para el trabajo de Stream Analytics. Para obtener más información sobre el ajuste de las unidades de streaming, consulte [Escalación de trabajos de Stream Analytics](stream-analytics-scale-jobs.md).

    // Create a Stream Analytics transformation
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

Al igual que la entrada y la salida, una transformación también está vinculada al trabajo de Stream Analytics específico en el que se creó.

## <a name="start-a-stream-analytics-job"></a>Inicio de un trabajo de Stream Analytics
Después de crear un trabajo de Stream Analytics y sus entradas, salidas y transformaciones, puede iniciar el trabajo si llama al método **Start** .

El siguiente código de ejemplo inicia un trabajo de Stream Analytics con una hora de inicio de salida personalizada definida para el 12 de diciembre de 2012, 12:12:12 UTC:

    // Start a Stream Analytics job
    JobStartParameters jobStartParameters = new JobStartParameters
    {
        OutputStartMode = OutputStartMode.CustomTime,
        OutputStartTime = new DateTime(2012, 12, 12, 0, 0, 0, DateTimeKind.Utc)
    };

    LongRunningOperationResponse jobStartResponse = client.StreamingJobs.Start(resourceGroupName, streamAnalyticsJobName, jobStartParameters);

## <a name="stop-a-stream-analytics-job"></a>Detención de un trabajo de Stream Analytics
Puede detener un trabajo de Stream Analytics en ejecución si llama al método **Stop** .

    // Stop a Stream Analytics job
    LongRunningOperationResponse jobStopResponse = client.StreamingJobs.Stop(resourceGroupName, streamAnalyticsJobName);

## <a name="delete-a-stream-analytics-job"></a>Eliminación de un trabajo de Stream Analytics
El método **Delete** eliminará el trabajo, además de los subrecursos subyacentes, incluidas las entradas, salidas y transformaciones del trabajo.

    // Delete a Stream Analytics job
    LongRunningOperationResponse jobDeleteResponse = client.StreamingJobs.Delete(resourceGroupName, streamAnalyticsJobName);

## <a name="get-support"></a>Obtención de soporte técnico
Para obtener más ayuda, pruebe nuestro [foro de Azure Stream Analytics](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureStreamAnalytics).

## <a name="next-steps"></a>Pasos siguientes
Ha aprendido los conceptos básicos del uso de un SDK de .NET para crear y ejecutar trabajos de análisis. Para obtener más información, consulte:

* [Introducción a Azure Stream Analytics](stream-analytics-introduction.md)
* [Introducción al uso de Azure Stream Analytics](stream-analytics-real-time-fraud-detection.md)
* [Escalación de trabajos de Azure Stream Analytics](stream-analytics-scale-jobs.md)
* [SDK de .NET de administración de Azure Stream Analytics](https://msdn.microsoft.com/library/azure/dn889315.aspx)
* [Referencia del lenguaje de consulta de Azure Stream Analytics](https://msdn.microsoft.com/library/azure/dn834998.aspx)
* [Referencia de API de REST de administración de Azure Stream Analytics](https://msdn.microsoft.com/library/azure/dn835031.aspx)

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
[stream.analytics.get.started]: stream-analytics-real-time-fraud-detection.md
[stream.analytics.developer.guide]: stream-analytics-developer-guide.md
[stream.analytics.scale.jobs]: stream-analytics-scale-jobs.md
[stream.analytics.query.language.reference]: http://go.microsoft.com/fwlink/?LinkID=513299
[stream.analytics.rest.api.reference]: http://go.microsoft.com/fwlink/?LinkId=517301
