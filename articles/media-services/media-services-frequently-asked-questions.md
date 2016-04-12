<properties 
	pageTitle="Preguntas más frecuentes" 
	description="Preguntas más frecuentes (P+F)" 
	services="media-services" 
	documentationCenter="" 
	authors="Juliako" 
	manager="erikre" 
	editor=""/>

<tags 
	ms.service="media-services" 
	ms.workload="media" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
 	ms.date="03/02/2016"  
	ms.author="juliako"/>


#Preguntas más frecuentes  

##Preguntas más frecuentes generales sobre AMS 

P: ¿Cómo se escala la indización?

R: las unidades reservadas son las mismas para las tareas de codificación y de indización. Siga las instrucciones de [Escalación de unidades reservadas de codificación](media-services-how-to-scale.md) **Tenga en cuenta** que el rendimiento del indizador no se ve afectado por tipo de unidad reservada.

P: He cargado, codificado y publicado un vídeo. ¿Cuál es el motivo por el que el vídeo no se reproduce cuando intento transmitirlo?

R: uno de los motivos más habituales es que no dispone de al menos una unidad de streaming reservada asignada en el extremo de streaming desde el que está intentando reproducir. Siga las instrucciones de [Escalación de unidades reservadas de streaming](media-services-how-to-scale.md)

P: ¿Puedo realizar una composición en una secuencia en directo?

R: no se ofrece actualmente la composición en secuencias en vivo en Servicios multimedia de Azure, por lo que tendrá que realizar una composición previa en el equipo.

P: ¿Puedo usar CDN de Azure con secuencia en directo?

R: servicios multimedia admite la integración con CDN de Azure (para obtener más información, consulte [Administración de extremos de streaming en una cuenta de Servicios multimedia](media-services-manage-origins.md#enable_cdn)). Puede usar el streaming en vivo con CDN. Servicios multimedia de Azure proporciona salidas de Smooth Streaming, HLS y MPEG-DASH. Todos estos formatos usan HTTP para transferir datos y obtener beneficios del almacenamiento en caché de HTTP. En la transmisión en vivo, los datos de audio/vídeo reales se dividen en fragmentos y los fragmentos individuales se almacenan en caché en CDN. Solo tienen que actualizarse los datos de manifiesto. CDN actualiza periódicamente los datos de manifiesto.

P: ¿Los servicios multimedia de Azure admiten el almacenamiento de imágenes?

R: si solo busca almacenar las imágenes JPEG o PNG, debe mantenerlas en Almacenamiento de blobs de Azure. No supone una ventaja si se colocan en la cuenta de Servicios multimedia a menos que desee mantenerlas asociadas a los recursos de vídeo o audio. O bien, si puede tener la necesidad de usar las imágenes como superposiciones en el codificador de vídeo, el Estándar de codificador multimedia admite la superposición de las imágenes encima de los vídeos, por eso muestra JPEG y PNG como formatos de entrada admitidos. Para obtener más información, consulte [Creación de superposiciones](media-services-custom-mes-presets-with-dotnet.md#overlay).

P: ¿Cómo puedo copiar recursos de una cuenta de Servicios multimedia a otra?

R: Para copiar recursos de una cuenta de Servicios multimedia a otro, use el método de extensión [IAsset.Copy](https://github.com/Azure/azure-sdk-for-media-services-extensions/blob/dev/MediaServices.Client.Extensions/IAssetExtensions.cs#L354) disponible en el repositorio [Extensiones del SDK de .NET para Servicios multimedia de Azure](https://github.com/Azure/azure-sdk-for-media-services-extensions/). Para obtener más información, consulte [esta](https://social.msdn.microsoft.com/Forums/azure/28912d5d-6733-41c1-b27d-5d5dff2695ca/migrate-media-services-across-subscription?forum=MediaServices) conversación del foro.

P: ¿Cuáles son los caracteres admitidos para nombrar los archivos cuando se trabaja con AMS?

R: Los Servicios multimedia usan el valor de la propiedad IAssetFile.Name al generar direcciones URL para el contenido de streaming (por ejemplo, http://{AMSAccount}.origin.mediaservices.windows.net/{GUID}/{IAssetFile.Name}/streamingParameters.). Por este motivo, no se permite la codificación porcentual. El valor de la propiedad **Name** no puede tener ninguno de los siguientes [caracteres reservados para la codificación porcentual](http://en.wikipedia.org/wiki/Percent-encoding#Percent-encoding_reserved_characters): !*'();:@&=+$,/?%#". Además, solo puede haber un '.' para la extensión del nombre de archivo.


P: ¿Cómo se realiza la conexión con REST?

R: Después de conectarse correctamente a https://media.windows.net, recibirá un redireccionamiento 301 especificando otro URI de Servicios multimedia. Debe realizar las llamadas subsiguientes al nuevo URI como se describe en [Conexión a Servicios multimedia con la API de REST](media-services-rest-connect_programmatically.md).


P: ¿Cómo puedo girar un vídeo durante el proceso de codificación?

R: [Media Encoder Estándar](media-services-dotnet-encode-with-media-encoder-standard.md) admite ángulos de rotación de 90, 180 o 270. El comportamiento predeterminado es "Auto", en el que se intentan detectar los metadatos de rotación del archivo MP4 o MOV entrante y la compensación. Incluya el siguiente elemento **Sources** en uno de los valores preestablecidos JSON definidos [aquí](http://msdn.microsoft.com/library/azure/mt269960.aspx):
	
	"Version": 1.0,
	"Sources": [
	{
	  "Streams": [],
	  "Filters": {
	    "Rotation": "90"
	  }
	}
	],
	"Codecs": [
	
	...




##Rutas de aprendizaje de Servicios multimedia

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##Envío de comentarios

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

<!---HONumber=AcomDC_0309_2016-->