<properties 
	pageTitle="Creación de un procesador de multimedia | Microsoft Azure" 
	description="Aprenda a crear un componente de procesador de multimedia para codificar, cifrar, descifrar o convertir el formato de contenido multimedia para Servicios multimedia de Azure. Los ejemplos de código están escritos en C# y utilizan el SDK de Servicios multimedia para .NET." 
	services="media-services" 
	documentationCenter="" 
	authors="juliako" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="media-services" 
	ms.workload="media" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="12/05/2015" 
	ms.author="juliako"/>


#Obtención de una instancia de procesador multimedia

> [AZURE.SELECTOR]
- [.NET](media-services-get-media-processor.md)
- [REST](media-services-rest-get-media-processor.md)
 

##Información general

En los Servicios multimedia, un procesador multimedia es un componente que controla una tarea de procesamiento específica, como codificación, conversión de formato, cifrado o descifrado de contenido multimedia. Normalmente crea un procesador multimedia cuando crea una tarea para codificar, cifrar o convertir el formato de contenido multimedia.

La siguiente tabla proporciona el nombre y la descripción de cada procesador multimedia disponible.

Nombre de procesador multimedia|Descripción|Más información
---|---|---
Codificador multimedia de Azure|Le permite ejecutar tareas de codificación con el Codificador multimedia de Azure.|[Codificador multimedia de Azure](media-services-encode-asset.md#azure_media_encoder)
Media Encoder Estándar|Le permite ejecutar tareas de codificación con el Codificador multimedia estándar.|[Codificador multimedia de Azure](media-services-encode-asset.md#media_encoder_standard)
Flujo de trabajo del Codificador multimedia|Le permite ejecutar tareas de codificación con el flujo de trabajo Premium del Codificador multimedia.|[Flujo de trabajo del Codificador multimedia](media-services-encode-asset.md#media_encoder_premium_wokrflow)
Azure Media Indexer| Le permite crear archivos multimedia y contenido que se puede buscar, así como generar pistas y palabras clave de subtítulos (CC).|[Indexación de archivos multimedia con Azure Media Indexer](media-services-index-content.md)
Azure Media Hyperlapse (versión preliminar)|Permite suavizar los “saltos” en el vídeo con estabilización de vídeo. También permite acelerar su contenido en un clip consumible.|		[Azure Media Hyperlapse](http://azure.microsoft.com/blog/?p=286281&preview=1&_ppp=61e1a0b3db)</a>
Storage Decryption| Le permite descifrar recursos multimedia que se cifraron con el cifrado de almacenamiento.|N/D
Microsoft Azure Media Packager|Le permite convertir recursos multimedia de .mp4 a un formato de Smooth Streaming. Además, le permite convertir recursos multimedia de Smooth Streaming al formato Apple HTTP Live Streaming (HLS).|[Cadenas preestablecidas de tarea para el Empaquetador multimedia de Azure](http://msdn.microsoft.com/library/hh973635.aspx)
Microsoft Azure Media Encryptor|Le permite cifrar recursos multimedia con la protección PlayReady.|[Cadenas preestablecidas de tarea para el Empaquetador multimedia de Azure](http://msdn.microsoft.com/library/hh973610.aspx)

##Obtener MediaProcessor

El siguiente método muestra cómo obtener una instancia del procesador multimedia. El ejemplo de código supone el uso de una variable de nivel de módulo llamada **\_context** para hacer referencia al contexto de servidor tal como se describe en la sección [Procedimientos: conexión con los Servicios multimedia mediante programación].

	private static IMediaProcessor GetLatestMediaProcessorByName(string mediaProcessorName)
	{
	     var processor = _context.MediaProcessors.Where(p => p.Name == mediaProcessorName).
	        ToList().OrderBy(p => new Version(p.Version)).LastOrDefault();
	
	    if (processor == null)
	        throw new ArgumentException(string.Format("Unknown media processor", mediaProcessorName));
	
	    return processor;
	}


##Rutas de aprendizaje de Servicios multimedia

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##Envío de comentarios

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

##Pasos siguientes
Ahora que sabe cómo obtener una instancia de procesador multimedia, consulte el tema [Codificar un recurso][], que le mostrará cómo utilizar el Codificador multimedia de Azure para codificar un recurso.

[Codificar un recurso]: media-services-encode-asset.md
[Task Preset Strings for the Azure Media Encoder]: http://msdn.microsoft.com/library/jj129582.aspx
[Procedimientos: conexión con los Servicios multimedia mediante programación]: ../media-services-set-up-computer/

<!---HONumber=AcomDC_1210_2015-->