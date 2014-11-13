<properties urlDisplayName="Create a Media Processor" pageTitle="Creaci&oacute;n de un procesador de multimedia en Azure" metaKeywords="" description="Aprenda a crear un componente de procesador de multimedia para codificar, cifrar, descifrar o convertir el formato de contenido multimedia para Servicios multimedia de Azure. Los ejemplos de c&oacute;digo est&aacute;n escritos en C# y utilizan el SDK de Servicios multimedia para .NET." metaCanonical="" services="media-services" documentationCenter="" title="Reproducci&oacute;n de instancia de procesador multimedia" authors="juliako" solutions="" manager="dwrede" editor="" />

<tags ms.service="media-services" ms.workload="media" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="09/26/2014" ms.author="juliako" />

# Reproducción de instancia de procesador multimedia

Este artículo forma parte de una serie en la que se presenta la programación de los Servicios multimedia de Azure. El tema anterior trataba de [Comprobación del de un recurso codificado y carga en el almacenamiento][Comprobación del de un recurso codificado y carga en el almacenamiento].

En los Servicios multimedia, un procesador multimedia es un componente que controla una tarea de procesamiento específica, como codificación, conversión de formato, cifrado o descifrado de contenido multimedia. Normalmente crea un procesador multimedia cuando crea una tarea para codificar, cifrar o convertir el formato de contenido multimedia.

La siguiente tabla proporciona el nombre y la descripción de cada procesador multimedia disponible.

| Nombre de procesador multimedia | Descripción                                                                                                                                                                                         | Más información                                             |
|---------------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|-------------------------------------------------------------|
| Codificador multimedia de Azure | Le permite ejecutar tareas de codificación con el Codificador multimedia.                                                                                                                           | [Valores predefinidos del sistema Media Services Encoder][Valores predefinidos del sistema Media Services Encoder] |
| Windows Azure Media Packager    | Le permite convertir recursos multimedia de .mp4 a un formato de Smooth Streaming. Además, le permite convertir recursos multimedia de Smooth Streaming al formato Apple HTTP Live Streaming (HLS). | [Valores predefinidos del sistema Azure Media Packager][Valores predefinidos del sistema Azure Media Packager]   |
| Windows Azure Media Encryptor   | Le permite cifrar recursos multimedia con la protección PlayReady.                                                                                                                                  | [Valores predefinidos del sistema Azure Media Packager][1]  |
| Azure Media Indexer             | Le permite crear archivos multimedia y contenido que se puede buscar, así como generar pistas y palabras clave de subtítulos (CC).                                                                  | N/D                                                         |
| Storage Decryption              | Le permite descifrar recursos multimedia que se cifraron con el cifrado de almacenamiento.                                                                                                          | N/D                                                         |

El siguiente método muestra cómo obtener una instancia del procesador multimedia. El ejemplo de código supone el uso de una variable de nivel de módulo llamado \*\*\_context\*\* para hacer referencia al contexto de servidor tal como se describe en la sección [Conexión con los Servicios multimedia mediante programación][Conexión con los Servicios multimedia mediante programación].

    private static IMediaProcessor GetLatestMediaProcessorByName(string mediaProcessorName)
    {
         var processor = _context.MediaProcessors.Where(p => p.Name == mediaProcessorName).
            ToList().OrderBy(p => new Version(p.Version)).LastOrDefault();

        if (processor == null)
            throw new ArgumentException(string.Format("Unknown media processor", mediaProcessorName));

        return processor;
    }

## Pasos siguientes

Ahora que sabe cómo obtener una instancia de procesador multimedia, consulte el tema [Codificar un recurso][Codificar un recurso], que le mostrará cómo utilizar el Codificador multimedia de Azure para codificar un recurso.

  [Comprobación del de un recurso codificado y carga en el almacenamiento]: ../media-services-create-encrypted-asset-upload-storage/
  [Valores predefinidos del sistema Media Services Encoder]: http://msdn.microsoft.com/es-es/library/jj129582.aspx
  [Valores predefinidos del sistema Azure Media Packager]: http://msdn.microsoft.com/es-es/library/hh973635.aspx
  [1]: http://msdn.microsoft.com/es-es/library/hh973610.aspx
  [Conexión con los Servicios multimedia mediante programación]: ../media-services-set-up-computer/
  [Codificar un recurso]: ../media-services-encode-asset/
