<properties urlDisplayName="How to manage media content" pageTitle="Administración de contenido multimedia: Servicios multimedia de Azure" metaKeywords="" description="Learn how to manage your media content in Azure Media Services." metaCanonical="" services="media-services" documentationCenter="" title="How to Manage Content in Media Services" authors="juliako" solutions="" manager="dwrede" editor="" />

<tags ms.service="media-services" ms.workload="media" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="10/30/2014" ms.author="juliako" />





# Administración de contenido en Servicios multimedia #
El contenido de Servicios multimedia de Azure le permite administrar contenido multimedia para la cuenta de Servicios multimedia.

Actualmente, puede realizar las siguientes operaciones de contenido directamente desde el portal:

- Consulta de información del contenido, como el estado publicado, la dirección URL publicada, el tamaño y la fecha y hora de la última actualización
- Carga de nuevo contenido
- contenido
- Reproducción de vídeo de contenido
- Publicación/cancelación de publicación de contenido
- Eliminación de contenido


## Carga de contenido ##


1. En el [Portal de administración](http://go.microsoft.com/fwlink/?LinkID=256666&clcid=0x409)haga clic en **Servicios multimedia** y, a continuación, haga clic en el nombre de cuenta de Servicios multimedia.
2. Seleccione la página CONTENT. 
3. Haga clic en el botón **Cargar** en la página o en la parte inferior del portal. 
4. En el cuadro de diálogo **Cargar contenido**, diríjase al archivo del recurso deseado. Haga clic en el archivo y, a continuación, haga clic en **Abrir**o presione **Entrar**.

	![UploadContentDialog][uploadcontent]

5. En el cuadro de diálogo de carga de contenido, haga clic en el botón de comprobación para aceptar el archivo y el nombre de contenido.
6. La carga comenzará y podrá realizar un seguimiento del proceso desde la parte inferior del portal.  

	![JobStatus][status]

Una vez que la carga se haya completado, verá el nuevo recurso en la lista de contenido. Se establecerá una convención en la que el nombre tendrá anexo "**-Source**" en la parte inferior para ayudar a realizar un seguimiento del nuevo contenido como contenido de origen para las tareas de codificación.

Si el valor del tamaño del archivo no se actualiza después de que se detenga el proceso de carga, presione el botón **Sincronizar metadatos**. De esta forma, se sincroniza el tamaño de archivo del recurso con el tamaño del archivo real en el almacenamiento y se actualiza el valor en la página de contenido.	


## Cifrado de contenido

1. En el [Portal de administración](http://go.microsoft.com/fwlink/?LinkID=256666&clcid=0x409)haga clic en **Servicios multimedia** y, a continuación, haga clic en el nombre de cuenta de Servicios multimedia. 
2. Haga clic en la página CONTENT en la parte superior de la página.
3. Haga clic en el vídeo de origen deseado para el trabajo de codificación y, a continuación, haga clic en **Codificar** en la parte inferior de la página.
4. En el cuadro de diálogo del codificador multimedia de Azure, seleccione uno de los valores predefinidos de codificación avanzados o comunes.

	**Valores predefinidos comunes**

	+ **Reproducción en PC/Mac (a través de Flash/Silverlight)**.. Este valor predefinido produce un recurso de transmisión suave con las siguientes características: 44,1 kHz 16 bits/CBR de audio estéreo de ejemplo con codificación a 96 kbps mediante AAC y CBR de vídeo de 720p con codificación con una velocidad de bits de 6 de 3400 kbps a 400 kbps mediante el perfil principal H.264 y GOP de dos segundos.
	+ **Reproducción a través de HTML5 (IE/Chrome/Safari)**.. Este valor predefinido produce un archivo MP4 único con las siguientes características: 44,1 kHz 16 bits/CBR de audio estéreo de ejemplo con una codificación a 128 kbps mediante AAC y CBR de vídeo de 720p con una codificación a 4500 kbps mediante el perfil principal H.264.
	+ **Reproducción en dispositivos iOS y PC/Mac**. Este valor predefinido produce un recurso con las mismas características de recurso de transmisión suave (descrito anteriormente), pero en un formato que puede usarse para proporcionar transmisiones de HLS de Apple en dispositivos iOS. 
	+ **Codificación con protección de contenido PlayReady**. Este valor preestablecido produce un activo codificado con protección de contenido PlayReady.  Para que este valor preestablecido funcione, debe habilitar el servicio de entrega de licencias PlayReady. Para ello, vaya a la pestaña **PROTECCIÓN DE CONTENIDO** y agregue una fila a la tabla Branding Reporting. El servicio de licencias PlayReady de Servicios multimedia se habilitará a los pocos minutos de presionar GUARDAR.   
	
	
		De forma predeterminada, se usa el servicio de licencias PlayReady de Servicios multimedia. Para especificar algún otro servicio del que los clientes pueden obtener una licencia para reproducir el contenido cifrado de PlayReady, use las API de REST o del SDK de .NET de Servicios multimedia. Para obtener más información, consulte [Uso de cifrado estático para proteger su contenido]() y establezca la propiedad **licenseAcquisitionUrl** en el valor preestablecido de Media Encryptor. Como alternativa, puede usar el cifrado dinámico y establecer la propiedad **PlayReadyLicenseAcquisitionUrl** tal y como se describe en [Uso del cifrado dinámico de PlayReady y del servicio de entrega de licencias](http://go.microsoft.com/fwlink/?LinkId=507720 ). 
		
		Tenga en cuenta que esta opción solo se mostrará si se ha registrado para la característica de vista previa de protección de contenido de PlayReady. Para registrarse para las características de vista previa, debe pasar por el proceso que se describe en la siguiente página: [Características de la versión de vista previa de Microsoft Azure](http://azure.microsoft.com/en-us/services/preview/).  
	
		
	**Valores preestablecidos avanzados**
	
	+ El tema [Cadenas de valores preestablecidos de tareas para el Codificador multimedia de Azure](http://go.microsoft.com/fwlink/?LinkId=270865) explica lo que significa cada valor preestablecido en la lista Valores preestablecidos avanzados. 


	![EncoderDialog][encoder]

	Actualmente, el portal no es compatible con todos los formatos de codificación compatibles que admite el codificador multimedia. Tampoco es compatible con el cifrado/descifrado de recursos multimedia. Puede realizar estas tareas mediante programación. Para obtener más información, consulte [Creación de aplicaciones con el SDK de Servicios multimedia para .NET](http://go.microsoft.com/fwlink/?LinkId=270866) y [Cadenas de valores preestablecidos de tareas para el Codificador multimedia de Azure](http://go.microsoft.com/fwlink/?LinkId=270865).


5. En el cuadro de diálogo del codificador multimedia de Azure, especifique el nombre descriptivo del contenido de salida o acepte el valor predeterminado. A continuación, haga clic en el botón de comprobación para iniciar la operación de codificación y podrá realizar un seguimiento del progreso desde la parte inferior del portal.

	Una vez que se haya realizado la codificación, la vista debe tener un aspecto similar a la captura de pantalla siguiente. 

	![PortalViewUploadCompleted][portaloverview]


	Si el valor del tamaño de archivo no se actualiza después de que la codificación se haya realizado, presione el botón **Sincronizar metadatos**. De esta forma, se sincroniza el tamaño de archivo de salida del recurso con el tamaño de archivo real en el almacenamiento y se actualiza el valor en la página de contenido.	

## Publicación de de contenido

1. En el [Portal de administración](http://go.microsoft.com/fwlink/?LinkID=256666&clcid=0x409)haga clic en **Servicios multimedia** y, a continuación, haga clic en el nombre de cuenta de Servicios multimedia.
2. Haga clic en la página CONTENT en la parte superior de la página.
3. Haga clic en un recurso que no se haya publicado. Haga clic en el botón de publicación para publicar en una dirección URL pública. Una vez que se haya publicado el contenido en una dirección URL, un reproductor de clientes con capacidad para mostrar el contenido codificado puede abrir la dirección URL.

 ![PublishedContent][publishedcontent]

## Reproducción de contenido desde el portal


1. En el [Portal de administración](http://go.microsoft.com/fwlink/?LinkID=256666&clcid=0x409)haga clic en **Servicios multimedia** y, a continuación, haga clic en el nombre de cuenta de Servicios multimedia.
2. Haga clic en la página CONTENT en la parte superior de la página.
3. Haga clic en el contenido de vídeo deseado y haga clic en el botón **Reproducir** en la parte inferior del portal. Solo el contenido publicado puede reproducirse desde el portal. Además, la codificación debe ser compatible con el explorador.


<!-- Images -->
[portaloverview]: ./media/media-services-manage-content/media-services-content-page.png
[publishedcontent]: ./media/media-services-manage-content/media-services-upload-content-published.png
[uploadcontent]: ./media/media-services-manage-content/UploadContent.png
[status]: ./media/media-services-manage-content/Status.png
[encoder]: ./media/media-services-manage-content/EncoderDialog2.png
[branding]: ./media/branding-reporting.png

<!--HONumber=35_1-->
