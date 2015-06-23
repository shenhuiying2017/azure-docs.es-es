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
	ms.date="02/10/2015" 
	ms.author="juliako"/>


#Obtención de una instancia de procesador multimedia

Este artículo forma parte de la serie [Flujo de trabajo de vídeo bajo demanda de Servicios multimedia](media-services-video-on-demand-workflow.md) . 

##Información general

En los Servicios multimedia, un procesador multimedia es un componente que controla una tarea de procesamiento específica, como codificación, conversión de formato, cifrado o descifrado de contenido multimedia. Normalmente crea un procesador multimedia cuando crea una tarea para codificar, cifrar o convertir el formato de contenido multimedia.

La siguiente tabla proporciona el nombre y la descripción de cada procesador multimedia disponible.

<table border="2" cellspacing="0" cellpadding="5" style="border: 2px solid #000000;">
  <thead>
    <tr>
       <th>Nombre de procesador multimedia</th>
       <th>Descripción</th>
	<th>Más información</th>
    </tr>
  </thead>
  <tbody>
    <tr>
       <td>Codificador multimedia de Azure</td>
       <td>Le permite ejecutar tareas de codificación con el Codificador multimedia de Azure.</td>
       <td><a href="http://msdn.microsoft.com/library/jj129582.aspx"> Cadenas preestablecidas de tarea para el Codificador multimedia de Azure</a></td>
    </tr>
    <tr>
       <td>Flujo de trabajo del Codificador multimedia</td>
       <td>Le permite ejecutar tareas de codificación con el flujo de trabajo Premium del Codificador multimedia.</td>
       <td><a href="http://azure.microsoft.com/documentation/articles/media-services-encode-with-premium-workflow/">Codificación con el flujo de trabajo Premium del Codificador multimedia.</a></td>
    </tr>    
	<tr>
        <td>Azure Media Indexer</td>
        <td>Le permite crear archivos multimedia y contenido que se puede buscar, así como generar pistas y palabras clave de subtítulos (CC).</td>
		<td><a href="http://azure.microsoft.com/documentation/articles/media-services-index-content/">Indización de archivos multimedia con el Indizador multimedia de Azure</a>.</td>
    </tr>
    <tr>
        <td>Windows Azure Media Packager</td>
        <td>Le permite convertir recursos multimedia de .mp4 a un formato de Smooth Streaming. Además, le permite convertir recursos multimedia de Smooth Streaming al formato Apple HTTP Live Streaming (HLS).</td>
		<td><a href="http://msdn.microsoft.com/library/hh973635.aspx">Cadenas preestablecidas de tarea para el Empaquetador multimedia de Azure</a></td>
    </tr>
    <tr>
        <td>Windows Azure Media Encryptor</td>
        <td>Le permite cifrar recursos multimedia con la protección PlayReady.</td>
        <td><a href="http://msdn.microsoft.com/library/hh973610.aspx">Cadenas preestablecidas de tarea para el Empaquetador multimedia de Azure</a></td>
    </tr>
    <tr>
        <td>Storage Decryption</td>
        <td>Le permite descifrar recursos multimedia que se cifraron con el cifrado de almacenamiento.</td>
		<td>N/D</td>
    </tr>  </tbody>
</table>

<br />

##Obtener MediaProcessor

El siguiente método muestra cómo obtener una instancia del procesador multimedia. El ejemplo de código supone el uso de una variable de nivel de módulo llamado **_context** para hacer referencia al contexto de servidor tal como se describe en la sección [Conexión con los Servicios multimedia mediante programación].

	private static IMediaProcessor GetLatestMediaProcessorByName(string mediaProcessorName)
	{
	     var processor = _context.MediaProcessors.Where(p => p.Name == mediaProcessorName).
	        ToList().OrderBy(p => new Version(p.Version)).LastOrDefault();
	
	    if (processor == null)
	        throw new ArgumentException(string.Format("Unknown media processor", mediaProcessorName));
	
	    return processor;
	}

##Pasos siguientes
Ahora que sabe cómo obtener una instancia de procesador multimedia, consulte el tema [Codificación de un recurso][], que le mostrará cómo utilizar el Codificador multimedia de Azure para codificar un recurso.

[Codificación de un recurso]: media-services-encode-asset.md
[Cadenas preestablecidas de tarea para el Codificador multimedia de Azure]: http://msdn.microsoft.com/library/jj129582.aspx
[Conexión con los Servicios multimedia mediante programación]: ../media-services-set-up-computer/


<!--HONumber=52--> 