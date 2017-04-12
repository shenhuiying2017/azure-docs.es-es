---
title: "Creación de un procesador de multimedia | Microsoft Docs"
description: "Aprenda a crear un componente de procesador de multimedia para codificar, cifrar, descifrar o convertir el formato de contenido multimedia para Servicios multimedia de Azure. Los ejemplos de código están escritos en C# y utilizan el SDK de Servicios multimedia para .NET."
services: media-services
documentationcenter: 
author: juliako
manager: erikre
editor: 
ms.assetid: dbf9496f-c6f0-42a7-aa36-70f89dcb8ea2
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/26/2016
ms.author: juliako
translationtype: Human Translation
ms.sourcegitcommit: 785d3a8920d48e11e80048665e9866f16c514cf7
ms.openlocfilehash: 88f6e1da090eb6088e54c6f81d0f83b1737d3c2c
ms.lasthandoff: 04/12/2017


---
# <a name="how-to-get-a-media-processor-instance"></a>Obtención de una instancia de procesador multimedia
> [!div class="op_single_selector"]
> * [.NET](media-services-get-media-processor.md)
> * [REST](media-services-rest-get-media-processor.md)
> 
> 

## <a name="overview"></a>Información general
En los Servicios multimedia, un procesador multimedia es un componente que controla una tarea de procesamiento específica, como codificación, conversión de formato, cifrado o descifrado de contenido multimedia. Normalmente crea un procesador multimedia cuando crea una tarea para codificar, cifrar o convertir el formato de contenido multimedia.

La siguiente tabla proporciona el nombre y la descripción de cada procesador multimedia disponible.

| Nombre de procesador multimedia | Description | Más información |
| --- | --- | --- |
| Media Encoder Estándar |Proporciona funciones estándar de codificación a petición. |[Información general y comparación de codificadores multimedia a petición de Azure](media-services-encode-asset.md) |
| Flujo de trabajo del Codificador multimedia |Le permite ejecutar tareas de codificación con el flujo de trabajo Premium del Codificador multimedia. |[Información general y comparación de codificadores multimedia a petición de Azure](media-services-encode-asset.md) |
| Azure Media Indexer |Le permite crear archivos multimedia y contenido que se puede buscar, así como generar pistas y palabras clave de subtítulos (CC). |[Azure Media Indexer](media-services-index-content.md) |
| Azure Media Hyperlapse (versión preliminar) |Permite suavizar los “saltos” en el vídeo con estabilización de vídeo. También permite acelerar su contenido en un clip consumible. |[Azure Media Hyperlapse](media-services-hyperlapse-content.md) |
| Codificador multimedia de Azure |En desuso | |
| Storage Decryption |En desuso | |
| Azure Media Packager |En desuso | |
| Azure Media Encryptor |En desuso | |

## <a name="get-media-processor"></a>Obtención de un procesador multimedia
El siguiente método muestra cómo obtener una instancia del procesador multimedia. El ejemplo de código supone el uso de una variable de nivel de módulo llamada **_context** para hacer referencia al contexto de servidor tal como se describe en la sección [Procedimientos: conexión con los Media Services mediante programación](media-services-dotnet-connect-programmatically.md).

    private static IMediaProcessor GetLatestMediaProcessorByName(string mediaProcessorName)
    {
        var processor = _context.MediaProcessors.Where(p => p.Name == mediaProcessorName).
        ToList().OrderBy(p => new Version(p.Version)).LastOrDefault();

        if (processor == null)
        throw new ArgumentException(string.Format("Unknown media processor", mediaProcessorName));

        return processor;
    }


## <a name="media-services-learning-paths"></a>Rutas de aprendizaje de Servicios multimedia
[!INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Envío de comentarios
[!INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

## <a name="next-steps"></a>Pasos siguientes
Ahora que sabe cómo obtener una instancia de procesador multimedia, consulte el tema sobre la [codificación de un recurso](media-services-dotnet-encode-with-media-encoder-standard.md) , que le mostrará cómo utilizar el servicio Media Encoder estándar para codificar un recurso.


