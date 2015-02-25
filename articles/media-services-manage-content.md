<properties pageTitle="Administración de contenido multimedia: Servicios multimedia de Azure" description="Aprenda a administrar su contenido multimedia en Servicios multimedia de Azure." services="media-services" documentationCenter="" authors="juliako" manager="dwrede" editor=""/>

<tags ms.service="media-services" ms.workload="media" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="10/30/2014" ms.author="juliako"/>





# Administración de contenido en Servicios multimedia #

En este tema se muestra cómo usar el Portal de administración de Azure para administrar contenido multimedia en su cuenta de Servicios multimedia.

Actualmente, puede realizar las siguientes operaciones de contenido directamente desde el portal:

- Ver información de contenido como el estado publicado, la dirección URL publicada, el tamaño, la fecha y hora de la última actualización y si el recurso está cifrado o no.
- Cargar nuevo contenido
- Indizar contenido
- Codificar contenido
- Reproducir contenido
- Publicar/cancelar la publicación de contenido


##   Carga de contenido 


1. En el [Portal de administración](http://go.microsoft.com/fwlink/?LinkID=256666&clcid=0x409), haga clic en **Servicios multimedia** y, a continuación, haga clic en el nombre de cuenta de Servicios multimedia.
2. Seleccione la página CONTENT. 
3. Haga clic en el botón **Cargar** en la página o en la parte inferior del portal. 
4. En el cuadro de diálogo **Cargar contenido**, diríjase al archivo del recurso deseado. Haga clic en el archivo y, a continuación, haga clic en **Abrir** o presione **Entrar**.

	![UploadContentDialog][uploadcontent]

5. En el cuadro de diálogo de carga de contenido, haga clic en el botón de comprobación para aceptar el archivo y el nombre de contenido.
6. La carga comenzará y podrá realizar un seguimiento del proceso desde la parte inferior del portal.  

	![JobStatus][status]

Una vez que la carga se haya completado, verá el nuevo recurso en la lista de contenido. Se establecerá una convención en la que el nombre tendrá anexo "**-Source**" en la parte inferior para ayudar a realizar un seguimiento del nuevo contenido como contenido de origen para las tareas de codificación.

![ContentPage][contentpage]

Si el valor del tamaño del archivo no se actualiza después de que se detenga el proceso de carga, presione el botón **Sincronizar metadatos**. De esta forma, se sincroniza el tamaño de archivo del recurso con el tamaño del archivo real en el almacenamiento y se actualiza el valor en la página de contenido.	

##  Indizar contenido

El Indizador multimedia de Azure permite que el contenido de los archivos multimedia se puedan buscar y genera una transcripción de texto completo para las palabras clave y subtítulos. Puede indizar el contenido mediante el Portal de administración, para ello siga los pasos que se muestran a continuación. Sin embargo, si desea más control sobre qué archivos y cómo se va a realizar el trabajo de indización, puede utilizar el SDK de Servicios multimedia para las API .NET o REST. Para obtener más información, consulte [Indización de archivos multimedia con el Indizador multimedia de Azure](https://msdn.microsoft.com/es-es/library/azure/dn783455.aspx).

Los pasos siguientes muestran cómo usar el Portal de administración para indizar el contenido.

1. Seleccione el archivo que desea indizar.
	Si se admite la indización para este tipo de archivo, se habilitará el botón PROCESAR en la parte inferior de la página de contenido.
1. Presione el botón PROCESAR.
2. En el cuadro de diálogo **Proceso**, elija el procesador **Indizador multimedia de Azure**.
3. A continuación, rellene el cuadro de diálogo Proceso con la información detallada del **título** y **descripción** del archivo multimedia de entrada.
	
	![Process][process]

##  Codificar contenido

Para entregar vídeo digital a través de Internet, debe comprimir los archivos multimedia. Servicios multimedia proporciona un codificador multimedia que le permite especificar cómo desea que su contenido se codifique (por ejemplo, los códecs que se van a utilizar, el formato de archivo, la resolución y la velocidad de bits). 

Cuando se trabaja con los Servicios multimedia de Azure, uno de los escenarios más comunes es ofrecer streaming de velocidad de bits adaptable a los clientes. Con el streaming de velocidad de bits adaptable, el cliente puede cambiar a una secuencia de velocidad de bits mayor o menor que el vídeo mostrado, según el ancho de banda actual de la red, el uso de CPU y otros factores. Servicios multimedia admite el streaming de velocidad de bits adaptable siguiente: HTTP Live Streaming (HLS), Smooth Streaming, MPEG DASH y HDS (únicamente para licenciatarios de Adobe PrimeTime/Access). 

Servicios multimedia proporciona paquetes dinámicos que permiten entregar contenido codificado MP4 de velocidad de bits adaptable o Smooth Streaming en formatos admitidos por Servicios multimedia (MPEG DASH, HLS, Smooth Streaming, HDS) sin tener que volver a empaquetar en estos formatos de streaming. 

Para aprovecharse de los paquetes dinámicos, deberá hacer lo siguiente:

- codificar o transcodificar el archivo intermedio (origen) en un conjunto de archivos MP4 de velocidad de bits adaptable o de Smooth Streaming de velocidad de bits adaptable (más adelante en este tutorial se muestran los pasos de codificación),  
- obtener al menos una unidad de streaming a petición para el extremo de streaming para el que piensa entregar el contenido. Para obtener más información, consulte [Escalación de unidades reservadas de streaming a petición](http://azure.microsoft.com/es-es/documentation/articles/media-services-how-to-scale/).

Con el empaquetado dinámico solo necesita almacenar y pagar por los archivos en formato de almacenamiento sencillo y Servicios multimedia creará y servirá la respuesta adecuada en función de las solicitudes del cliente. 

Tenga en cuenta que además de poder usar las capacidades de empaquetado dinámico, las unidades reservadas de streaming a petición con capacidad de salida dedicada pueden adquirirse en incrementos de 200 Mbps. De manera predeterminada, el streaming a petición está configurado en un modelo de instancias compartidas para el que se comparten recursos de servidor (por ejemplo, proceso, capacidad de salida, entre otros) con los demás usuarios. Para mejorar el resultado del streaming a petición, se recomienda adquirir unidades reservadas de streaming a petición.


En esta sección se describen los pasos que puede seguir para codificar el contenido con el codificador multimedia de Azure mediante el Portal de administración.

1.  Seleccione el archivo que desea codificar.
	Si se admite la codificación para este tipo de archivo, se habilitará el botón PROCESAR en la parte inferior de la página de contenido.
4. En el cuadro de diálogo **Proceso**, seleccione el procesador **Indizador multimedia de Azure**.
5. Elija una de las **configuraciones de codificación**.

	![Process2][process2]

		
	El tema [Cadenas predefinidas de tarea para el Codificador multimedia de Azure](https://msdn.microsoft.com/es-es/library/azure/dn619392.aspx) explica lo que significan las categorías de cada valor predefinido en **Valores preestablecidos para el streaming adaptable (empaquetado dinámico)**, **Valores preestablecidos para la descarga progresiva**, **Valores preestablecidos heredados para el streaming adaptable**  .  


	Las **otras** configuraciones se describen a continuación:

	+ **Codificación con protección de contenido PlayReady**. Este valor preestablecido produce un activo codificado con protección de contenido PlayReady.  
	
	
		De forma predeterminada, se usa el servicio de licencias PlayReady de Servicios multimedia. Para especificar algún otro servicio del que los clientes pueden obtener una licencia para reproducir el contenido cifrado de PlayReady, use las API de REST o del SDK de .NET de Servicios multimedia. Para obtener más información, consulte [Uso de cifrado estático para proteger su contenido]() y establezca la propiedad **licenseAcquisitionUrl** en el valor preestablecido de Media Encryptor. Como alternativa, puede usar el cifrado dinámico y establecer la propiedad **PlayReadyLicenseAcquisitionUrl** tal y como se describe en [Uso del cifrado dinámico de PlayReady y del servicio de entrega de licencias](http://go.microsoft.com/fwlink/?LinkId=507720 ). 
	+ **Reproducción en PC/Mac (a través de Flash/Silverlight)**. Este valor predefinido produce un recurso de transmisión suave con las siguientes características: 44,1 kHz 16 bits/CBR de audio estéreo de ejemplo con codificación a 96 kbps mediante AAC y CBR de vídeo de 720p con codificación con una velocidad de bits de 6 de 3400 kbps a 400 kbps mediante el perfil principal H.264 y GOP de dos segundos.
	+ **Reproducción a través de HTML5 (IE/Chrome/Safari)**. Este valor predefinido produce un archivo MP4 único con las siguientes características: 44,1 kHz 16 bits/CBR de audio estéreo de ejemplo con una codificación a 128 kbps mediante AAC y CBR de vídeo de 720p con una codificación a 4500 kbps mediante el perfil principal H.264.
	+ **Reproducción en dispositivos iOS y PC/Mac**. Este valor predefinido produce un recurso con las mismas características de recurso de transmisión suave (descrito anteriormente), pero en un formato que puede usarse para proporcionar transmisiones de HLS de Apple en dispositivos iOS. 

5. A continuación, especifique el nombre descriptivo del contenido de salida o acepte el valor predeterminado. A continuación, haga clic en el botón de comprobación para iniciar la operación de codificación y podrá realizar un seguimiento del progreso desde la parte inferior del portal.
6. Presione Aceptar.

	Después de realizar la codificación, la página de contenido contendrá el archivo codificado. 

	Para ver el progreso de la tarea de codificación, cambie a la página **TRABAJOS**.  


	Si el valor del tamaño de archivo no se actualiza después de que la codificación se haya realizado, presione el botón **Sincronizar metadatos**. De esta forma, se sincroniza el tamaño de archivo de salida del recurso con el tamaño de archivo real en el almacenamiento y se actualiza el valor en la página de contenido.	

##  Publicación de contenido

Al publicar el contenido, se le proporcionará una dirección URL de descarga progresiva o de streaming. El cliente sería ser capaz de reproducir los vídeos usando esta dirección URL.

1. Haga clic en el recurso que desea publicar. 
2. A continuación, haga clic en el botón Publicar. 
	
	Una vez que se haya publicado el contenido en una dirección URL, un reproductor de clientes con capacidad para mostrar el contenido codificado puede abrir la dirección URL.

 ![PublishedContent][publishedcontent]

## Reproducción de contenido desde el portal

El Portal de administración proporciona un reproductor de contenido de Servicios multimedia que puede utilizar para probar el vídeo.

Haga clic en el contenido de vídeo deseado y haga clic en el botón **Reproducir** en la parte inferior del portal. 
 
Solo el contenido publicado puede reproducirse desde el portal. Además, la codificación debe ser compatible con el explorador.


<!-- Images -->
[portaloverview]: ./media/media-services-manage-content/media-services-content-page.png
[publishedcontent]: ./media/media-services-manage-content/media-services-upload-content-published.png
[uploadcontent]: ./media/media-services-manage-content/UploadContent.png
[status]: ./media/media-services-manage-content/Status.png
[encoder]: ./media/media-services-manage-content/EncoderDialog2.png
[branding]: ./media/branding-reporting.png
[contentpage]: ./media/media-services-manage-content/media-services-content-page.png
[process]: ./media/media-services-manage-content/media-services-process-video.png
[process2]: ./media/media-services-manage-content/media-services-process-video2.png

<!--HONumber=42-->
