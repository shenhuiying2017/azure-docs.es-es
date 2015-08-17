<properties 
	pageTitle="Creación de un procesador de multimedia en Azure" 
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
	ms.date="05/13/2015" 
	ms.author="juliako"/>


#Obtención de una instancia de procesador multimedia

Este artículo forma parte de la serie [Vídeo de Servicios multimedia sobre el flujo de trabajo a petición](media-services-video-on-demand-workflow.md).

##Información general

En los Servicios multimedia, un procesador multimedia es un componente que controla una tarea de procesamiento específica, como codificación, conversión de formato, cifrado o descifrado de contenido multimedia. Normalmente crea un procesador multimedia cuando crea una tarea para codificar, cifrar o convertir el formato de contenido multimedia.

La siguiente tabla proporciona el nombre y la descripción de cada procesador multimedia disponible.

Nombre de procesador multimedia|Descripción|Más información
---|---|---
Codificador multimedia de Azure|Le permite ejecutar tareas de codificación con el Codificador multimedia de Azure.| [Valores predefinidos del sistema Media Services Encoder](http://msdn.microsoft.com/library/jj129582.aspx)
Flujo de trabajo del Codificador multimedia|Le permite ejecutar tareas de codificación con el flujo de trabajo Premium del Codificador multimedia.|[Codificación con Flujo de trabajo premium de codificación de medios](media-services-encode-with-premium-workflow.md).
Azure Media Indexer|Le permite crear archivos multimedia y contenido que se puede buscar, así como generar pistas y palabras clave de subtítulos (CC).|[Indexación de archivos multimedia con Azure Media Indexer](media-services-index-content.md)
Windows Azure Media Packager|Le permite convertir recursos multimedia de .mp4 a un formato de Smooth Streaming. Además, le permite convertir recursos multimedia de Smooth Streaming al formato Apple HTTP Live Streaming (HLS).|[Cadenas preestablecidas de tarea para el Empaquetador multimedia de Azure](http://msdn.microsoft.com/library/hh973635.aspx)
Windows Azure Media Encryptor|Le permite cifrar recursos multimedia con la protección PlayReady.|[Cadenas preestablecidas de tarea para el Empaquetador multimedia de Azure](http://msdn.microsoft.com/library/hh973610.aspx)
Azure Media Hyperlapse (versión preliminar)|Permite suavizar los “saltos” en el vídeo con estabilización de vídeo. También permite acelerar su contenido en un clip consumible.|[Azure Media Hyperlapse](http://go.microsoft.com/fwlink/?LinkId=613274)
Storage Decryption|Le permite descifrar recursos multimedia que se cifraron con el cifrado de almacenamiento.|N/D

##Obtener MediaProcessor

El siguiente método muestra cómo obtener una instancia del procesador multimedia. El ejemplo de código supone el uso de una variable de nivel de módulo llamada **\_context** para hacer referencia al contexto de servidor tal como se describe en la sección [Procedimientos: Conexión con los Servicios multimedia mediante programación].

	private static IMediaProcessor GetLatestMediaProcessorByName(string mediaProcessorName)
	{
	     var processor = _context.MediaProcessors.Where(p => p.Name == mediaProcessorName).
	        ToList().OrderBy(p => new Version(p.Version)).LastOrDefault();
	
	    if (processor == null)
	        throw new ArgumentException(string.Format("Unknown media processor", mediaProcessorName));
	
	    return processor;
	}

##Pasos siguientes
Ahora que sabe cómo obtener una instancia de procesador multimedia, consulte el tema [Codificar un recurso][], que le mostrará cómo utilizar el Codificador multimedia de Azure para codificar un recurso.

[Codificar un recurso]: media-services-encode-asset.md
[Task Preset Strings for the Azure Media Encoder]: http://msdn.microsoft.com/library/jj129582.aspx
[Procedimientos: Conexión con los Servicios multimedia mediante programación]: ../media-services-set-up-computer/

<!---HONumber=August15_HO6-->