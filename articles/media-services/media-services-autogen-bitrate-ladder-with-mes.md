---
title: "Uso de Azure Media Encoder Standard para generar automáticamente una escalera de velocidad de bits | Microsoft Docs"
description: "En este tema se muestra cómo usar Media Encoder Standard (MES) para generar automáticamente una escalera de velocidad de bits en función de la resolución de entrada y la velocidad de bits. La resolución de entrada y la velocidad de bits nunca se superarán. Por ejemplo, si la entrada es 720p a 3 Mbps, la salida permanecerá en 720p en el mejor de los casos y se iniciará a velocidades inferiores a 3 Mbps."
services: media-services
documentationcenter: 
author: juliako
manager: erikre
editor: 
ms.assetid: 63ed95da-1b82-44b0-b8ff-eebd535bc5c7
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/06/2017
ms.author: juliako
ms.translationtype: Human Translation
ms.sourcegitcommit: 09f24fa2b55d298cfbbf3de71334de579fbf2ecd
ms.openlocfilehash: 3bad3219b087523125047f24d643ffdc5e24caa0
ms.contentlocale: es-es
ms.lasthandoff: 06/07/2017


---
#  <a name="use-azure-media-encoder-standard-to-auto-generate-a-bitrate-ladder"></a>Uso de Azure Media Encoder Standard para generar automáticamente una escalera de velocidad de bits

## <a name="overview"></a>Información general

En este tema se muestra cómo usar Media Encoder Standard (MES) para generar automáticamente una escalera de velocidad de bits (pares de velocidad de bits-resolución) en función de la velocidad de bits y la resolución de entrada. El valor preestablecido generado automáticamente nunca superará la velocidad de bits y la resolución de entrada. Por ejemplo, si la entrada es 720p a 3 Mbps, la salida permanecerá en 720p en el mejor de los casos y se iniciará a velocidades inferiores a 3 Mbps.

Para usar esta característica, debe especificar el valor preestablecido **Adaptive Streaming** al crear una tarea de codificación. Cuando se usa el valor preestablecido **Adaptive Streaming**, el codificador MES limitará inteligentemente una escalera de velocidad de bits. Sin embargo, no será capaz de controlar los costos de codificación, ya que el servicio determina cuántos niveles usar y con qué resolución. Puede ver ejemplos de los niveles de salida generados por MES como resultado de la codificación con el valor preestablecido **Adaptive Streaming** al [final](#output) de este tema.

>[!NOTE]
> Este valor predeterminado debe usarse solo cuando el propósito sea producir un recurso de salida transmisible por streaming. En concreto, el recurso de salida contendrá archivos MP4 en los que el audio y el vídeo no están intercalados. Si necesita que la salida contenga archivos MP4 con el vídeo y el audio intercalados (por ejemplo, para usarlos como archivo de descarga progresiva), use uno de los valores predeterminados que aparecen [en esta sección](media-services-mes-presets-overview.md).

## <a id="encoding_with_dotnet"></a>Codificación con el SDK de .NET de Servicios multimedia

En el ejemplo de código siguiente se usa el último SDK para .NET de Servicios multimedia para realizar las siguientes tareas:

- Crear un trabajo de codificación.
- Obtener una referencia al codificador Codificador multimedia estándar.
- Agregue una tarea de codificación al trabajo y especifique que se utilice el valor preestablecido **Adaptive Streaming**. 
- Crear un recurso de salida que contendrá el recurso codificado.
- Agregar un controlador de eventos para comprobar el progreso del trabajo.
- Envíe el trabajo.

        using System;
        using System.Configuration;
        using System.IO;
        using System.Linq;
        using Microsoft.WindowsAzure.MediaServices.Client;
        using System.Threading;

        namespace AdaptiveStreamingMESPresest
        {
            class Program
            {
            // Read values from the App.config file.
            private static readonly string _mediaServicesAccountName =
                ConfigurationManager.AppSettings["MediaServicesAccountName"];
            private static readonly string _mediaServicesAccountKey =
                ConfigurationManager.AppSettings["MediaServicesAccountKey"];

            // Field for service context.
            private static CloudMediaContext _context = null;
            private static MediaServicesCredentials _cachedCredentials = null;
            static void Main(string[] args)
            {
                // Create and cache the Media Services credentials in a static class variable.
                _cachedCredentials = new MediaServicesCredentials(
                        _mediaServicesAccountName,
                        _mediaServicesAccountKey);
                // Used the chached credentials to create CloudMediaContext.
                _context = new CloudMediaContext(_cachedCredentials);

                // Get an uploaded asset.
                var asset = _context.Assets.FirstOrDefault();

                // Encode and generate the output using the "Adaptive Streaming" preset.
                EncodeToAdaptiveBitrateMP4Set(asset);

                Console.ReadLine();
            }

            static public IAsset EncodeToAdaptiveBitrateMP4Set(IAsset asset)
            {
                // Declare a new job.
                IJob job = _context.Jobs.Create("Media Encoder Standard Job");

                // Get a media processor reference, and pass to it the name of the 
                // processor to use for the specific task.
                IMediaProcessor processor = GetLatestMediaProcessorByName("Media Encoder Standard");

                // Create a task
                ITask task = job.Tasks.AddNew("Media Encoder Standard encoding task",
                processor,
                "Adaptive Streaming",
                TaskOptions.None);

                // Specify the input asset to be encoded.
                task.InputAssets.Add(asset);
                // Add an output asset to contain the results of the job. 
                // This output is specified as AssetCreationOptions.None, which 
                // means the output asset is not encrypted. 
                task.OutputAssets.AddNew("Output asset",
                AssetCreationOptions.None);

                job.StateChanged += new EventHandler<JobStateChangedEventArgs>(JobStateChanged);
                job.Submit();
                job.GetExecutionProgressTask(CancellationToken.None).Wait();

                return job.OutputMediaAssets[0];
            }
            private static void JobStateChanged(object sender, JobStateChangedEventArgs e)
            {
                Console.WriteLine("Job state changed event:");
                Console.WriteLine("  Previous state: " + e.PreviousState);
                Console.WriteLine("  Current state: " + e.CurrentState);
                switch (e.CurrentState)
                {
                case JobState.Finished:
                    Console.WriteLine();
                    Console.WriteLine("Job is finished. Please wait while local tasks or downloads complete...");
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
                    break;
                default:
                    break;
                }
            }
            private static IMediaProcessor GetLatestMediaProcessorByName(string mediaProcessorName)
            {
                var processor = _context.MediaProcessors.Where(p => p.Name == mediaProcessorName).
                ToList().OrderBy(p => new Version(p.Version)).LastOrDefault();

                if (processor == null)
                throw new ArgumentException(string.Format("Unknown media processor", mediaProcessorName));

                return processor;
            }

            }

        }

## <a id="output"></a>Salida

Esta sección muestra tres niveles de salida generados por MES como resultado de la codificación con el valor preestablecido **Adaptive Streaming**. 

### <a name="example-1"></a>Ejemplo 1
Un origen con un alto de "1080" y una tasa de fotogramas de "29 970" genera 6 niveles de vídeo:

|Nivel|Alto|Ancho|Velocidad de bits (kbps)|
|---|---|---|---|
|1|1080|1920|6780|
|2|720|1280|3520|
|3|540|960|2210|
|4|360|640|1150|
|5|270|480|720|
|6|180|320|380|

### <a name="example-2"></a>Ejemplo 2
Un origen con un alto de "720" y una tasa de fotogramas de "23 970" genera 5 niveles de vídeo:

|Nivel|Alto|Ancho|Velocidad de bits (kbps)|
|---|---|---|---|
|1|720|1280|2940|
|2|540|960|1850|
|3|360|640|960|
|4|270|480|600|
|5|180|320|320|

### <a name="example-3"></a>Ejemplo 3
Un origen con un alto de "360" y una tasa de fotogramas de "29 970" genera 3 niveles de vídeo:

|Nivel|Alto|Ancho|Velocidad de bits (kbps)|
|---|---|---|---|
|1|360|640|700|
|2|270|480|440|
|3|180|320|230|
## <a name="media-services-learning-paths"></a>Rutas de aprendizaje de Servicios multimedia
[!INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Envío de comentarios
[!INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

## <a name="see-also"></a>Otras referencias
[Información general sobre la codificación de Servicios multimedia](media-services-encode-asset.md)


