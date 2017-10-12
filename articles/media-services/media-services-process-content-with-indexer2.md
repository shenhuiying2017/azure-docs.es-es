---
title: "Indexación de archivos multimedia con Azure Media Indexer 2 Preview | Microsoft Docs"
description: "El Indizador multimedia de Azure permite que el contenido de los archivos multimedia se puedan buscar y genera una transcripción de texto completo para las palabras clave y subtítulos. En este tema se muestra cómo usar Media Indexer 2 Preview."
services: media-services
documentationcenter: 
author: Juliako
manager: cfowler
editor: 
ms.assetid: 85d25525-a498-44eb-ae3a-2ca5ceb8e53d
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 07/31/2017
ms.author: adsolank;juliako;
ms.openlocfilehash: 0afdd1c04e50215a55fb92c70b1210d1f80d8e3f
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="indexing-media-files-with-azure-media-indexer-2-preview"></a>Indización de archivos multimedia con Azure Media Indexer 2 Preview
## <a name="overview"></a>Información general
El procesador de multimedia (MP) **Azure Media Indexer 2 Preview** permite crear archivos multimedia y contenido que se puede buscar, así como generar pistas de subtítulos (CC). En comparación con la versión anterior de [Azure Media Indexer](media-services-index-content.md), **Azure Media Indexer 2 Preview** realiza la indexación de forma más rápida y ofrece compatibilidad con más idiomas. Los idiomas admitidos son inglés, español, francés, alemán, italiano, chino (simplificado y mandarín), portugués, árabe y japonés.

El MP **Azure Media Indexer 2 Preview** se encuentra actualmente en la fase de versión preliminar.

En este tema se muestra cómo crear trabajos de indexación con **Azure Media Indexer 2 Preview**.

> [!NOTE]
> Se aplican las siguientes consideraciones:
> 
> Indexer 2 no es compatible con Azure China y Azure Government.
> 
> Al indizar contenido, asegúrese de usar archivos multimedia que tengan una voz muy clara (sin música de fondo, ruido, efectos ni silbido de micrófono). Algunos ejemplos de contenido adecuado son: reuniones, conferencias o presentaciones grabadas. Es posible que el siguiente contenido no sea adecuado para la indización: películas, programas de TV, cualquier elemento con audio y efectos de sonido mezclados o contenido mal gragado con ruido de fondo (silbido).
> 
> 

En este tema se proporciona información sobre **Azure Media Indexer 2 Preview** y se muestra cómo se usa con el SDK de Media Services para .NET.

## <a name="input-and-output-files"></a>Archivos de entrada y salida
### <a name="input-files"></a>Archivos de entrada
Archivos de audio o vídeo

### <a name="output-files"></a>Archivos de salida
Un trabajo de indexación puede generar archivos de subtítulos en los siguientes formatos:  

* **SAMI**
* **TTML**
* **WebVTT**

Los archivos de subtítulos (CC) en estos formatos se pueden usar para crear archivos de audio y vídeo accesibles para personas con discapacidades auditivas.

## <a name="task-configuration-preset"></a>Configuración de tareas (valor preestablecido)
Al crear una tarea de indexación con **Azure Media Indexer 2 Preview**, debe especificar un valor predeterminado de configuración.

El siguiente JSON establece los parámetros disponibles.

    {
      "version":"1.0",
      "Features":
        [
           {
           "Options": {
                "Formats":["WebVtt","ttml"],
                "Language":"enUs",
                "Type":"RecoOptions"
           },
           "Type":"SpReco"
        }]
    }

## <a name="supported-languages"></a>Idiomas admitidos
Azure Media Indexer 2 Preview admite características de texto a voz en los siguientes idiomas (al especificar el nombre del idioma en la configuración de la tarea, use el código de 4 caracteres entre corchetes tal y como se muestra a continuación):

* Inglés [EnUs]
* Español [EsEs]
* Chino (mandarín, simplificado) [ZhCn]
* Francés [FrFr]
* Alemán [DeDe]
* Italiano [ItIt]
* Portugués [PtBr]
* Árabe (egipcio) [ArEg]
* Japonés [JaJp]
* Ruso [RuRu]
* Inglés británico [EnGb]
* Español de México [EsMx] 

## <a name="supported-file-types"></a>Tipos de archivo admitidos

Para más información sobre los tipos de archivos admitidos, vea la sección de [códecs y formatos admitidos](media-services-media-encoder-standard-formats.md#input-containerfile-formats).

## <a name="net-sample-code"></a>Código de ejemplo de .NET

El programa siguiente muestra cómo:

1. Crear un recurso y cargar un archivo multimedia en dicho recurso.
2. Crear un trabajo con una tarea de indexación basada en un archivo de configuración que contiene el siguiente valor predeterminado de JSON.
   
        {
          "version":"1.0",
          "Features":
            [
               {
               "Options": {
                    "Formats":["WebVtt","ttml"],
                    "Language":"enUs",
                    "Type":"RecoOptions"
               },
               "Type":"SpReco"
            }]
        }
3. Descargue los archivos de salida. 
   
#### <a name="create-and-configure-a-visual-studio-project"></a>Creación y configuración de un proyecto de Visual Studio

Configure el entorno de desarrollo y rellene el archivo app.config con la información de la conexión, como se describe en [Desarrollo de Media Services con .NET](media-services-dotnet-how-to-use.md). 

#### <a name="example"></a>Ejemplo

    using System;
    using System.Configuration;
    using System.IO;
    using System.Linq;
    using Microsoft.WindowsAzure.MediaServices.Client;
    using System.Threading;
    using System.Threading.Tasks;

    namespace IndexContent
    {
        class Program
        {
            // Read values from the App.config file.
            private static readonly string _AADTenantDomain =
                ConfigurationManager.AppSettings["AADTenantDomain"];
            private static readonly string _RESTAPIEndpoint =
                ConfigurationManager.AppSettings["MediaServiceRESTAPIEndpoint"];

            // Field for service context.
            private static CloudMediaContext _context = null;

            static void Main(string[] args)
            {
                var tokenCredentials = new AzureAdTokenCredentials(_AADTenantDomain, AzureEnvironments.AzureCloudEnvironment);
                var tokenProvider = new AzureAdTokenProvider(tokenCredentials);

                _context = new CloudMediaContext(new Uri(_RESTAPIEndpoint), tokenProvider);

                // Run indexing job.
                var asset = RunIndexingJob(@"C:\supportFiles\Indexer\BigBuckBunny.mp4",
                                            @"C:\supportFiles\Indexer\config.json");

                // Download the job output asset.
                DownloadAsset(asset, @"C:\supportFiles\Indexer\Output");
            }

            static IAsset RunIndexingJob(string inputMediaFilePath, string configurationFile)
            {
                // Create an asset and upload the input media file to storage.
                IAsset asset = CreateAssetAndUploadSingleFile(inputMediaFilePath,
                    "My Indexing Input Asset",
                    AssetCreationOptions.None);

                // Declare a new job.
                IJob job = _context.Jobs.Create("My Indexing Job");

                // Get a reference to Azure Media Indexer 2 Preview.
                string MediaProcessorName = "Azure Media Indexer 2 Preview";

                var processor = GetLatestMediaProcessorByName(MediaProcessorName);

                // Read configuration from the specified file.
                string configuration = File.ReadAllText(configurationFile);

                // Create a task with the encoding details, using a string preset.
                ITask task = job.Tasks.AddNew("My Indexing Task",
                    processor,
                    configuration,
                    TaskOptions.None);

                // Specify the input asset to be indexed.
                task.InputAssets.Add(asset);

                // Add an output asset to contain the results of the job.
                task.OutputAssets.AddNew("My Indexing Output Asset", AssetCreationOptions.None);

                // Use the following event handler to check job progress.  
                job.StateChanged += new EventHandler<JobStateChangedEventArgs>(StateChanged);

                // Launch the job.
                job.Submit();

                // Check job execution and wait for job to finish.
                Task progressJobTask = job.GetExecutionProgressTask(CancellationToken.None);

                progressJobTask.Wait();

                // If job state is Error, the event handling
                // method for job progress should log errors.  Here we check
                // for error state and exit if needed.
                if (job.State == JobState.Error)
                {
                    ErrorDetail error = job.Tasks.First().ErrorDetails.First();
                    Console.WriteLine(string.Format("Error: {0}. {1}",
                                                    error.Code,
                                                    error.Message));
                    return null;
                }

                return job.OutputMediaAssets[0];
            }

            static IAsset CreateAssetAndUploadSingleFile(string filePath, string assetName, AssetCreationOptions options)
            {
                IAsset asset = _context.Assets.Create(assetName, options);

                var assetFile = asset.AssetFiles.Create(Path.GetFileName(filePath));
                assetFile.Upload(filePath);

                return asset;
            }

            static void DownloadAsset(IAsset asset, string outputDirectory)
            {
                foreach (IAssetFile file in asset.AssetFiles)
                {
                    file.Download(Path.Combine(outputDirectory, file.Name));
                }
            }

            static IMediaProcessor GetLatestMediaProcessorByName(string mediaProcessorName)
            {
                var processor = _context.MediaProcessors
                    .Where(p => p.Name == mediaProcessorName)
                    .ToList()
                    .OrderBy(p => new Version(p.Version))
                    .LastOrDefault();

                if (processor == null)
                    throw new ArgumentException(string.Format("Unknown media processor",
                                                               mediaProcessorName));

                return processor;
            }

            static private void StateChanged(object sender, JobStateChangedEventArgs e)
            {
                Console.WriteLine("Job state changed event:");
                Console.WriteLine("  Previous state: " + e.PreviousState);
                Console.WriteLine("  Current state: " + e.CurrentState);

                switch (e.CurrentState)
                {
                    case JobState.Finished:
                        Console.WriteLine();
                        Console.WriteLine("Job is finished.");
                        Console.WriteLine();
                        break;
                    case JobState.Canceling:
                    case JobState.Queued:
                    case JobState.Scheduled:
                    case JobState.Processing:
                        Console.WriteLine("Please wait...\n");
                        break;
                    case JobState.Canceled:
                    case JobState.Error:
                        // Cast sender as a job.
                        IJob job = (IJob)sender;
                        // Display or log error details as needed.
                        // LogJobStop(job.Id);
                        break;
                    default:
                        break;
                }
            }
        }
    }

## <a name="media-services-learning-paths"></a>Rutas de aprendizaje de Servicios multimedia
[!INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Envío de comentarios
[!INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

## <a name="related-links"></a>Vínculos relacionados
[Azure Media Services Analytics Overview (Información general sobre análisis de Servicios multimedia de Azure)](media-services-analytics-overview.md)

[Demostraciones de Azure Media Analytics](http://azuremedialabs.azurewebsites.net/demos/Analytics.html)

