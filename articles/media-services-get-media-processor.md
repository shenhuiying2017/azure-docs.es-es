<properties linkid="develop-media-services-how-to-guides-create-media-processor" urlDisplayName="Create a Media Processor" pageTitle="How to Create a Media Processor - Azure" metaKeywords="" description="Learn how to create a media processor component to encode, convert format, encrypt, or decrypt media content for Azure Media Services. Code samples are written in C# and use the Media Services SDK for .NET." metaCanonical="" services="media-services" documentationCenter="" title="How to: Get a Media Processor Instance" authors="migree" solutions="" manager="" editor="" />

<tags ms.service="media-services" ms.workload="media" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="migree"></tags>

# Obtención de una instancia de procesador multimedia

Este artículo forma parte de una serie en la que se presenta la programación de los Servicios multimedia de Azure. El tema anterior trataba de [Creación de un recurso codificado y carga en el almacenamiento][].

En los Servicios multimedia, un procesador multimedia es un componente que controla una tarea de procesamiento específica, como codificación, conversión de formato, cifrado o descifrado de contenido multimedia. Normalmente crea un procesador multimedia cuando crea una tarea para codificar, cifrar o convertir el formato de contenido multimedia.

La siguiente tabla proporciona el nombre y la descripción de cada procesador multimedia disponible.

| Nombre de procesador multimedia | Descripción                                                                                                                                                                                         | Más información                                             |
|---------------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|-------------------------------------------------------------|
| Codificador multimedia de Azure | Le permite ejecutar tareas de codificación con el Codificador multimedia.                                                                                                                           | [Valores predefinidos del sistema Media Services Encoder][] |
| Azure Media Packager            | Le permite convertir recursos multimedia de .mp4 a un formato de Smooth Streaming. Además, le permite convertir recursos multimedia de Smooth Streaming al formato Apple HTTP Live Streaming (HLS). | [Valores predefinidos del sistema Azure Media Packager][]   |
| Azure Media Encryptor           | Le permite cifrar recursos multimedia con la protección PlayReady.                                                                                                                                  | [Valores predefinidos del sistema Azure Media Packager][1]  |
| Storage Decryption              | Le permite descifrar recursos multimedia que se cifraron con el cifrado de almacenamiento.                                                                                                          | N/D                                                         |

El siguiente método muestra cómo obtener una instancia del procesador multimedia. El ejemplo de código supone el uso de una variable de nivel de módulo llamado \*\*\_context\*\* para hacer referencia al contexto de servidor tal como se describe en la sección [Conexión con los Servicios multimedia mediante programación][].

    private static IMediaProcessor GetLatestMediaProcessorByName(string mediaProcessorName)
    {
         var processor = _context.MediaProcessors.Where(p => p.Name == mediaProcessorName).
            ToList().OrderBy(p => new Version(p.Version)).LastOrDefault();

        if (processor == null)
            throw new ArgumentException(string.Format("Unknown media processor", mediaProcessorName));

        return processor;
    }

## Pasos siguientes

Ahora que sabe cómo obtener una instancia de procesador multimedia, consulte el tema [Codificar un recurso][], que le mostrará cómo utilizar el Codificador multimedia de Azure para codificar un recurso.

  [Creación de un recurso codificado y carga en el almacenamiento]: http://go.microsoft.com/fwlink/?LinkID=301733&clcid=0x409
  [Valores predefinidos del sistema Media Services Encoder]: http://msdn.microsoft.com/en-us/library/jj129582.aspx
  [Valores predefinidos del sistema Azure Media Packager]: http://msdn.microsoft.com/en-us/library/hh973635.aspx
  [1]: http://msdn.microsoft.com/en-us/library/hh973610.aspx
  [Conexión con los Servicios multimedia mediante programación]: http://www.windowsazure.com/en-us/develop/media-services/how-to-guides/set-up-computer-for-media-services
  [Codificar un recurso]: http://go.microsoft.com/fwlink/?LinkId=301753
