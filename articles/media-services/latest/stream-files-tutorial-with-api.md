---
title: Carga, codificación y transmisión con Azure Media Services | Microsoft Docs
description: Siga los pasos de este tutorial para cargar un archivo, codificar el vídeo y transmitir su contenido con Azure Media Services.
services: media-services
documentationcenter: ''
author: Juliako
manager: cfowler
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: tutorial
ms.custom: mvc
ms.date: 04/09/2018
ms.author: juliako
ms.openlocfilehash: 1f0ce5599cce7fc830075e57af1bcba80d0e69e7
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/07/2018
---
# <a name="tutorial-upload-encode-and-stream-videos-using-apis"></a>Tutorial: Carga, codificación y transmisión de vídeos con las API

En este tutorial se muestra cómo cargar, codificar y transmitir archivos de vídeo con Azure Media Services. Quiere transmitir el contenido en los formatos HLS, MPEG DASH o CMAF de Apple para que pueda reproducirse en una amplia variedad de exploradores y dispositivos. El vídeo debe codificarse y empaquetarse adecuadamente antes de poder transmitirse.

Mientras el tutorial le guía por los pasos para cargar un vídeo, también puede codificar el contenido que hace accesible a su cuenta de Media Services a través de una dirección URL HTTPS.

![Reproducción del vídeo](./media/stream-files-tutorial-with-api/final-video.png)

En este tutorial se muestra cómo realizar las siguientes acciones:    

> [!div class="checklist"]
> * Inicio de Azure Cloud Shell
> * Creación de una cuenta de Media Services
> * Acceso a la API de Media Services
> * Configuración de la aplicación de ejemplo
> * Examen el código con detalle
> * Ejecución de la aplicación
> * Prueba de la URL de streaming
> * Limpieza de recursos

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>requisitos previos

Si no tiene Visual Studio instalado, puede obtener [Visual Studio Community 2017](https://www.visualstudio.com/thank-you-downloading-visual-studio/?sku=Community&rel=15).

## <a name="download-the-sample"></a>Descarga del ejemplo

Clone un repositorio GitHub que contenga el ejemplo de .NET de streaming en la máquina mediante el siguiente comando:  

 ```bash
 git clone https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials.git
 ```

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

[!INCLUDE [media-services-cli-create-v3-account-include](../../../includes/media-services-cli-create-v3-account-include.md)]

[!INCLUDE [media-services-v3-cli-access-api-include](../../../includes/media-services-v3-cli-access-api-include.md)]

## <a name="examine-the-code"></a>Examen del código

En esta sección se examinan las funciones definidas en el archivo [Program.cs](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/UploadEncodeAndStreamFiles/Program.cs) del proyecto *UploadEncodeAndStreamFiles*.

### <a name="start-using-media-services-apis-with-net-sdk"></a>Uso de las API de Media Services con SDK de .NET

Para empezar a usar las API de Media Services con. NET, debe crear un objeto **AzureMediaServicesClient**. Para crear el objeto, debe proporcionar las credenciales necesarias para que el cliente se conecte a Azure mediante Azure AD. Primero debe obtener un token y, después, crear un objeto **ClientCredential** desde el token devuelto. En el código que se clonó al comienzo del artículo, se ha usado el objeto **ArmClientCredential** para obtener el token.  

```csharp
private static IAzureMediaServicesClient CreateMediaServicesClient(ConfigWrapper config)
{
    ArmClientCredentials credentials = new ArmClientCredentials(config);

    return new AzureMediaServicesClient(config.ArmEndpoint, credentials)
    {
        SubscriptionId = config.SubscriptionId,
    };
}
```

### <a name="create-an-input-asset-and-upload-a-local-file-into-it"></a>Creación de un recurso de entrada y carga de un archivo local en él 

La función **CreateInputAsset** crea un nuevo recurso de entrada y carga en él el archivo de vídeo local especificado. Este recurso se utiliza como entrada para el trabajo de codificación. En Media Services v3, la entrada a un trabajo puede ser un recurso, o puede ser contenido que se pone a disposición de la cuenta de Media Services a través de direcciones URL HTTPS. Para más información sobre cómo codificar desde una dirección URL HTTPS, consulte [este](job-input-from-http-how-to.md) artículo.  

En Media Services v3, se utilizan las API de Azure Storage para cargar archivos. En el siguiente fragmento de código de .NET se muestra cómo hacerlo.

La función siguiente realiza estas acciones:

* Crea un recurso. 
* Obtiene una [dirección URL de SAS](https://docs.microsoft.com/azure/storage/common/storage-dotnet-shared-access-signature-part-1) que se puede escribir en el [contenedor de almacenamiento](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-dotnet?tabs=windows#upload-blobs-to-the-container) del recurso.
* Carga el archivo en el contenedor de almacenamiento mediante la dirección URL de SAS.

```csharp
private static Asset CreateInputAsset(IAzureMediaServicesClient client, string resourceGroupName, string accountName, string assetName, string fileToUpload)
{
    // Check if an Asset already exists.
    Asset asset = client.Assets.Get(resourceGroupName, accountName, assetName);

    if (asset == null)
    {
        asset = client.Assets.CreateOrUpdate(resourceGroupName, accountName, assetName, new Asset());

        var response = client.Assets.ListContainerSas(
                resourceGroupName,
                accountName,
                assetName,
                permissions: AssetContainerPermission.ReadWrite,
                expiryTime: DateTime.UtcNow.AddHours(4).ToUniversalTime()
            );

        var sasUri = new Uri(response.AssetContainerSasUrls.First());
        CloudBlobContainer container = new CloudBlobContainer(sasUri);
        var blob = container.GetBlockBlobReference(Path.GetFileName(fileToUpload));
        blob.UploadFromFile(fileToUpload);
    }

    // In this sample method, we are going to assume that if an Asset already exists with the desired name, 
    // then we can go ahead an use it for encoding or analyzing.

    return asset;
}
```

### <a name="create-an-output-asset-to-store-the-result-of-a-job"></a>Creación de un recurso de salida para almacenar el resultado de un trabajo 

El recurso de salida almacena el resultado del trabajo de codificación. El proyecto define la función **DownloadResults** que descarga los resultados de este recurso de salida en la carpeta "output", para que se pueda ver lo que tiene.

```csharp
private static Asset CreateOutputAsset(IAzureMediaServicesClient client, string resourceGroupName, string accountName, string assetName)
{
    // Check if an Asset already exists
    Asset outputAsset = client.Assets.Get(resourceGroupName, accountName, assetName);
    Asset asset = new Asset();
    string outputAssetName = assetName;

    if (outputAsset != null)
    {
        // Name collision! In order to get the sample to work, let's just go ahead and create a unique asset name
        // Note that the returned Asset can have a different name than the one specified as an input parameter.
        // You may want to update this part to throw an Exception instead, and handle name collisions differently.
        string uniqueness = @"-" + Guid.NewGuid().ToString();
        outputAssetName += uniqueness;
    }

    return client.Assets.CreateOrUpdate(resourceGroupName, accountName, outputAssetName, asset);
}
```

### <a name="create-a-transform-and-a-job-that-encodes-the-uploaded-file"></a>Creación de una transformación y de un trabajo que codifica el archivo cargado
Cuando se codifica o procesa contenido en Media Services, es un patrón común configurar los ajustes de codificación como una receta. Después, podría enviar un **trabajo** para aplicar esa receta a un vídeo. Al enviar nuevos trabajos a cada nuevo vídeo, está aplicando esa receta a todos los vídeos de la biblioteca. Una receta en Media Services se llama **transformación**. Para más información, consulte [Transformaciones y trabajos](transform-concept.md). El ejemplo descrito en este tutorial define una receta que codifica el vídeo para transmitirlo a varios dispositivos iOS y Android. 

#### <a name="transform"></a>Transformación

Al crear una nueva instancia de la **transformación**, debe especificar qué desea originar como salida. El parámetro requerido es un objeto **TransformOutput**, como se muestra en el código siguiente. Cada objeto **TransformOutput** contiene un **valor preestablecido**. El **valor preestablecido** describe las instrucciones paso a paso de las operaciones de procesamiento de vídeo o audio que se utilizarán para generar el objeto **TransformOutput** deseado. El ejemplo descrito en este artículo utiliza un valor preestablecido integrado denominado **AdaptiveStreaming**. El valor preestablecido codifica el vídeo de entrada en una escala de velocidad de bits generada automáticamente (pares resolución-velocidad de bits) basada en la resolución y la velocidad de bits, y produce archivos ISO MP4 con vídeo H.264 y audio AAC correspondiente a cada par resolución-velocidad de bits. Para más información sobre este valor preestablecido, consulte el artículo sobre la [generación automática de la escala de velocidad de bits](autogen-bitrate-ladder.md).

Puede usar otro valor de EncoderNamedPreset integrado o utilizar valores preestablecidos personalizados. 

Al crear una **transformación**, debe comprobar primero si ya existe una con el método **Get**, tal como se muestra en el código siguiente.  En Media Services v3, los métodos **Get** en las entidades devuelven **null** si la entidad no existe (una comprobación sin distinción entre mayúsculas y minúsculas en el nombre).

```csharp
private static Transform EnsureTransformExists(IAzureMediaServicesClient client,
    string resourceGroupName,
    string accountName,
    string transformName)
{
    // Does a Transform already exist with the desired name? Assume that an existing Transform with the desired name
    // also uses the same recipe or Preset for processing content.
    Transform transform = client.Transforms.Get(resourceGroupName, accountName, transformName);

    if (transform == null)
    {
        // Start by defining the desired outputs.
        TransformOutput[] outputs = new TransformOutput[]
        {
            new TransformOutput
            {
                Preset = new BuiltInStandardEncoderPreset()
                {
                    PresetName = EncoderNamedPreset.AdaptiveStreaming
                }
            }
        };

        transform = client.Transforms.CreateOrUpdate(resourceGroupName, accountName, transformName, outputs);
    }

    return transform;
}
```

#### <a name="job"></a>Trabajo

Como se mencionó anteriormente, el objeto **Transform** es la receta y un **trabajo** es la solicitud real a Media Services para aplicar que dicho objeto **Transform** a un determinado contenido de audio o vídeo de entrada. El **trabajo** especifica información como la ubicación del vídeo de entrada y la ubicación de la salida.

En este ejemplo, el vídeo de entrada se ha cargado desde la máquina local. Para más información sobre cómo codificar desde una dirección URL HTTPS, consulte [este](job-input-from-http-how-to.md) artículo.

```csharp
private static Job SubmitJob(IAzureMediaServicesClient client, 
    string resourceGroupName, 
    string accountName, 
    string transformName, 
    string jobName, 
    JobInput jobInput, 
    string outputAssetName)
{
    string uniqueJobName = jobName;
    Job job = client.Jobs.Get(resourceGroupName, accountName, transformName, jobName);

    if (job != null)
    {
        // Job already exists with the same name, so let's append a GUID
        string uniqueness = @"-" + Guid.NewGuid().ToString();
        uniqueJobName += uniqueness;
    }

    JobOutput[] jobOutputs =
    {
        new JobOutputAsset(outputAssetName),
    };

    job = client.Jobs.Create(
        resourceGroupName,
        accountName,
        transformName,
        jobName,
        new Job
        {
            Input = jobInput,
            Outputs = jobOutputs,
        });

    return job;
}
```

### <a name="wait-for-the-job-to-complete"></a>Espere a que el trabajo se complete.

El ejemplo de código siguiente muestra cómo sondear el servicio para conocer el estado del trabajo. El sondeo no es un procedimiento recomendado para aplicaciones de producción debido a la posible latencia. El sondeo se puede limitar si se sobreutiliza en una cuenta. Los desarrolladores deben utilizar en su lugar Event Grid.

Event Grid está diseñado para una alta disponibilidad, un rendimiento consistente y una escala dinámica. Con Event Grid, sus aplicaciones pueden escuchar y reaccionar a eventos de casi todos los servicios de Azure y de orígenes personalizados. El control de eventos sencillo y reactivo basado en HTTP le ayuda a crear soluciones eficaces con filtrado y enrutamiento de eventos inteligente.  Consulte [Enrutamiento de eventos a un punto de conexión web personalizado](job-state-events-cli-how-to.md).

El **trabajo** normalmente pasa por los siguientes estados: **Programado**, **En cola**, **Procesando**, **Finalizado** (el estado final). Si el trabajo ha encontrado un error, obtendrá el estado **Error**. Si el trabajo está en proceso de cancelación, obtendrá **Cancelando** y **Cancelado** cuando haya terminado.

```csharp
private static Job WaitForJobToFinish(IAzureMediaServicesClient client,
    string resourceGroupName,
    string accountName,
    string transformName,
    string jobName)
{
    int SleepInterval = 60 * 1000;

    Job job = null;

    while (true)
    {
        job = client.Jobs.Get(resourceGroupName, accountName, transformName, jobName);

        if (job.State == JobState.Finished || job.State == JobState.Error || job.State == JobState.Canceled)
        {
            break;
        }

        Console.WriteLine($"Job is {job.State}.");
        for (int i = 0; i < job.Outputs.Count; i++)
        {
            JobOutput output = job.Outputs[i];
            Console.Write($"\tJobOutput[{i}] is {output.State}.");
            if (output.State == JobState.Processing)
            {
                Console.Write($"  Progress: {output.Progress}");
            }
            Console.WriteLine();
        }
        System.Threading.Thread.Sleep(SleepInterval);
    }

    return job;
}
```

### <a name="get-a-streaminglocator"></a>Obtención de un objeto StreamingLocator

Una vez finalizada la codificación, el siguiente paso es poner el vídeo del recurso de salida a disposición de los clientes para su reproducción. Puede hacerlo en dos pasos: primero, cree un objeto **StreamingLocator** y, en segundo lugar, cree las direcciones URL de streaming que pueden usar los clientes. 

El proceso de creación de un objeto **StreamingLocator** se denomina publicación. De forma predeterminada, el objeto **StreamingLocator** es válido inmediatamente después de realizar las llamadas a la API y dura hasta que se elimina, a menos que configure las horas de inicio y de finalización opcionales. 

Al crear un objeto **StreamingLocator**, debe especificar el objeto **StreamingPolicyName** deseado. En este ejemplo, estará transmitiendo contenido no cifrado, de modo que se puede utilizar la directiva de streaming sin cifrar predefinida, **PredefinedStreamingPolicy.ClearStreamingOnly**.

> [!IMPORTANT]
> Al utilizar el objeto StreamingPolicy personalizado, debe diseñar un conjunto limitado de dichas directivas para su cuenta de Media Service y reutilizarlas para sus objetos StreamingLocator siempre que se necesiten las mismas opciones y protocolos de cifrado. La cuenta de Media Service tiene una cuota para el número de entradas de StreamingPolicy. No debe crear un nuevo objeto StreamingPolicy para cada objeto StreamingLocator.

El código siguiente da por supuesto que está llamando a la función con un único objeto locatorName.

```csharp
private static StreamingLocator CreateStreamingLocator(IAzureMediaServicesClient client,
                                                        string resourceGroup,
                                                        string accountName,
                                                        string assetName,
                                                        string locatorName)
{
    StreamingLocator locator =
        client.StreamingLocators.Create(resourceGroup,
        accountName,
        locatorName,
        new StreamingLocator()
        {
            AssetName = assetName,
            StreamingPolicyName = PredefinedStreamingPolicy.ClearStreamingOnly,
        });

    return locator;
}
```

Aunque el ejemplo de este tema trata del streaming, puede utilizar la misma llamada para crear un objeto StreamingLocator para la entrega de vídeo mediante descarga progresiva.

### <a name="get-streaming-urls"></a>Obtención de direcciones URL de streaming

Ahora que se ha creado un objeto StreamingLocator, puede obtener las direcciones URL de streaming, tal y como se muestra en **GetStreamingURLs**. Para crear una dirección URL, debe concatenar el nombre de host de **StreamingEndpoint** y la ruta de acceso de **StreamingLocator**. En este ejemplo, se utiliza el objeto **StreamingEndpoint** *predeterminado*. Cuando cree una cuenta de Media Services por primera vez, este **StreamingEndpoint** *predeterminado* estará en un estado detenido, por lo que deberá llamar a **Start**.

> [!NOTE]
> En este método, se necesita el objeto locatorName que se utilizó al crear el objeto **StreamingLocator** para el recurso de salida.

```csharp
static IList<string> GetStreamingURLs(
    IAzureMediaServicesClient client,
    string resourceGroupName,
    string accountName,
    String locatorName)
{
    IList<string> streamingURLs = new List<string>();

    string streamingUrlPrefx = "";

    StreamingEndpoint streamingEndpoint = client.StreamingEndpoints.Get(resourceGroupName, accountName, "default");

    if (streamingEndpoint != null)
    {
        streamingUrlPrefx = streamingEndpoint.HostName;

        if (streamingEndpoint.ResourceState != StreamingEndpointResourceState.Running)
            client.StreamingEndpoints.Start(resourceGroupName, accountName, "default");
    }

    foreach (var path in client.StreamingLocators.ListPaths(resourceGroupName, accountName, locatorName).StreamingPaths)
    {
        streamingURLs.Add("http://" + streamingUrlPrefx + path.Paths[0].ToString());
    }

    return streamingURLs;
}
```

### <a name="clean-up-resources-in-your-media-services-account"></a>Limpieza de los recursos en su cuenta de Media Services

Por lo general, debe limpiar todo excepto los objetos que piensa reutilizar (típicamente, reutilizará transformaciones y conservará objetos StreamingLocators, etc.). Si desea que la cuenta esté limpia después de la experimentación, debe eliminar los recursos que no piensa volver a usar.  Por ejemplo, el código siguiente elimina los trabajos.

```csharp
static void CleanUp(IAzureMediaServicesClient client,
        string resourceGroupName,
        string accountName,
        string transformName)
{
    foreach (var job in client.Jobs.List(resourceGroupName, accountName, transformName))
    {
        client.Jobs.Delete(resourceGroupName, accountName, transformName, job.Name);
    }

    foreach (var asset in client.Assets.List(resourceGroupName, accountName))
    {
        client.Assets.Delete(resourceGroupName, accountName, asset.Name);
    }
}
```

## <a name="run-the-sample-app"></a>Ejecutar la aplicación de ejemplo

1. Presione Ctrl + F5 para ejecutar la aplicación *EncodeAndStreamFiles*.
2. Copie una de las direcciones URL de streaming desde la consola.

En este ejemplo se muestran las direcciones URL que pueden usarse para reproducir el vídeo con diferentes protocolos:

![Salida](./media/stream-files-tutorial-with-api/output.png)

## <a name="test-the-streaming-url"></a>Prueba de la URL de streaming

Para probar la transmisión, este artículo usa Azure Media Player. 

> [!NOTE]
> Si el reproductor está hospedado en un sitio https, asegúrese de actualizar la dirección URL a "https".

1. Abra un explorador web y vaya a [https://aka.ms/azuremediaplayer/](https://aka.ms/azuremediaplayer/).
2. En el cuadro **Dirección URL:**, pegue uno de los valores de la dirección URL de streaming que se obtuvo al ejecutar la aplicación. 
3. Presione **Actualizar Player**.

Azure Media Player puede utilizarse para realizar pruebas, pero no debe usarse en un entorno de producción. 

## <a name="clean-up-resources"></a>Limpieza de recursos

Si ya no necesita ninguno de los recursos del grupo de recursos, incluida las cuentas de almacenamiento y de Media Services que ha creado en este tutorial, elimine el grupo de recursos que ha creado antes. Puede usar la herramienta **CloudShell**.

En **CloudShell**, ejecute el siguiente comando:

```azurecli-interactive
az group delete --name amsResourceGroup
```

## <a name="multithreading"></a>Multithreading

Los SDK de Azure Media Services v3 no son seguros para subprocesos. Al desarrollar una aplicación multiproceso, debe generar y utilizar un nuevo objeto AzureMediaServicesClient por subproceso.

## <a name="next-steps"></a>Pasos siguientes

Ahora que sabe cómo cargar, codificar y transmitir el vídeo, consulte el siguiente artículo: 

> [!div class="nextstepaction"]
> [Análisis de vídeos](analyze-videos-tutorial-with-api.md)
