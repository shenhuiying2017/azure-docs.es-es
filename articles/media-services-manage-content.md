<properties 
	pageTitle="Administración de contenido multimedia con Servicios multimedia de Azure" 
	description="Aprenda a administrar su contenido multimedia en Servicios multimedia de Azure." 
	services="media-services" 
	documentationCenter="" 
	authors="Juliako" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="media-services" 
	ms.workload="media" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="04/08/2015" 
	ms.author="juliako"/>


# Administración de contenido con Servicios multimedia de Azure 

Este artículo forma parte de la serie [Vídeo de Servicios multimedia sobre el flujo de trabajo a petición](media-services-video-on-demand-workflow.md). 

En este tema se muestra cómo usar el Portal de administración de Azure para administrar contenido multimedia en su cuenta de Servicios multimedia.

En este tema se muestra cómo realizar las siguientes operaciones de contenido directamente desde el portal:

- Ver información de contenido como el estado publicado, la dirección URL publicada, el tamaño, la fecha y hora de la última actualización y si el recurso está cifrado o no.
- Cargar nuevo contenido
- Indizar contenido
- Codificar contenido
- Cifrar
- Publicar/cancelar la publicación de contenido
- Reproducir contenido


##<a id="upload"></a>Carga de contenido 


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

##<a id="index"></a>Indización de contenido

El Indizador multimedia de Azure permite que el contenido de los archivos multimedia se puedan buscar y genera una transcripción de texto completo para las palabras clave y subtítulos. Puede indizar el contenido mediante el Portal de administración, para ello siga los pasos que se muestran a continuación. Sin embargo, si desea más control sobre qué archivos y cómo se va a realizar el trabajo de indización, puede utilizar el SDK de Servicios multimedia para las API .NET o REST. Para obtener más información, consulte [Indización de archivos multimedia con el Indizador multimedia de Azure](media-services-index-content.md).

Los pasos siguientes muestran cómo usar el Portal de administración para indizar el contenido.

1. Seleccione el archivo que desea indizar.
	Si se admite la indización para este tipo de archivo, se habilitará el botón PROCESAR en la parte inferior de la página de contenido.
1. Presione el botón PROCESAR.
2. En el cuadro de diálogo **Proceso**, elija el procesador **Indizador multimedia de Azure**.
3. A continuación, rellene el cuadro de diálogo Proceso con la información detallada del **título** y **descripción** del archivo multimedia de entrada.
	
	![Process][process]

##<a id="encode"></a>Indización Codificar contenido

Para entregar vídeo digital a través de Internet, debe comprimir los archivos multimedia. Servicios multimedia proporciona un codificador multimedia que le permite especificar cómo desea que su contenido se codifique (por ejemplo, los códecs que se van a utilizar, el formato de archivo, la resolución y la velocidad de bits). 

Cuando se trabaja con los Servicios multimedia de Azure, uno de los escenarios más comunes es entregar streaming de velocidad de bits adaptable a los clientes. Con el streaming de velocidad de bits adaptable, el cliente puede cambiar a una secuencia de velocidad de bits mayor o menor que el vídeo mostrado, según el ancho de banda actual de la red, el uso de CPU y otros factores. Servicios multimedia admite el streaming de velocidad de bits adaptable siguiente: HTTP Live Streaming (HLS), Smooth Streaming, MPEG DASH y HDS (únicamente para licenciatarios de Adobe PrimeTime/Access). 

Servicios multimedia proporciona paquetes dinámicos que permiten entregar contenido codificado MP4 de velocidad de bits adaptable o Smooth Streaming en formatos admitidos por Servicios multimedia (MPEG DASH, HLS, Smooth Streaming, HDS) sin tener que volver a empaquetar en estos formatos de streaming. 

Para aprovecharse de los paquetes dinámicos, deberá hacer lo siguiente:

- Codifique su archivo intermedio (origen) en un conjunto de archivos MP4 de velocidad de bits adaptable o archivos Smooth Streaming de velocidad de bits adaptable (los pasos de codificación se muestran más adelante en este tutorial).
- Obtenga al menos la unidad de streaming a petición para el extremo de streaming desde el que planea entregar el contenido. Para obtener más información, vea [Escalación de unidades reservadas de streaming a petición](media-services-manage-origins.md#scale_streaming_endpoints/).

Con el empaquetado dinámico solo necesita almacenar y pagar por los archivos en formato de almacenamiento sencillo y Servicios multimedia creará y servirá la respuesta adecuada en función de las solicitudes del cliente. 

Tenga en cuenta que además de poder usar las capacidades de empaquetado dinámico, las unidades reservadas de streaming a petición con capacidad de salida dedicada pueden adquirirse en incrementos de 200 Mbps. De manera predeterminada, el streaming a petición está configurado en un modelo de instancias compartidas para el que se comparten recursos de servidor (por ejemplo, proceso, capacidad de salida, entre otros) con los demás usuarios. Para mejorar el resultado del streaming a petición, se recomienda adquirir unidades reservadas de streaming a petición.

En esta sección se describen los pasos que puede seguir para codificar el contenido con el codificador multimedia de Azure mediante el Portal de administración.

1.  Seleccione el archivo que desea codificar.
	Si se admite la codificación para este tipo de archivo, se habilitará el botón PROCESAR en la parte inferior de la página de contenido.
4. En el cuadro de diálogo **Proceso**, seleccione el procesador **Indizador multimedia de Azure**.
5. Elija una de las **configuraciones de codificación**.

	![Process2][process2]

		
	El tema [Cadenas predefinidas de tarea para el Codificador multimedia de Azurer](https://msdn.microsoft.com/library/azure/dn619392.aspx) explica lo que significan las categorías de cada valor predefinido en **Valores preestablecidos para el streaming adaptable (empaquetado dinámico)**, **Valores preestablecidos para la descarga progresiva**, **Valores preestablecidos heredados para el streaming adaptable**.  


	Las **otras** configuraciones se describen a continuación:

	+ **Codificación con protección de contenido PlayReady**. Este valor preestablecido produce un activo codificado con protección de contenido PlayReady.  
	
	
		De forma predeterminada, se usa el servicio de licencias PlayReady de Servicios multimedia. Para especificar algún otro servicio del que los clientes pueden obtener una licencia para reproducir el contenido cifrado de PlayReady, use las API de REST o del SDK de Servicios multimedia para .NET. Para obtener más información, consulte [Uso de cifrado estático para proteger su contenido]() y establezca la propiedad **licenseAcquisitionUrl** en el valor preestablecido de Media Encryptor. Como alternativa, puede usar el cifrado dinámico y establecer la propiedad **PlayReadyLicenseAcquisitionUrl** tal y como se describe en [Uso del cifrado dinámico de PlayReady y del servicio de entrega de licencias](http://go.microsoft.com/fwlink/?LinkId=507720 ). 
	+ **Reproducción en PC/Mac (a través de Flash/Silverlight)**.. Este valor predefinido produce un recurso de transmisión suave con las siguientes características: 44,1 kHz 16 bits/CBR de audio estéreo de ejemplo con codificación a 96 kbps mediante AAC y CBR de vídeo de 720p con codificación con una velocidad de bits de 6 de 3400 kbps a 400 kbps mediante el perfil principal H.264 y GOP de dos segundos.
	+ **Reproducción a través de HTML5 (IE/Chrome/Safari)**. Este valor predefinido produce un archivo MP4 único con las siguientes características: 44,1 kHz 16 bits/CBR de audio estéreo de ejemplo con una codificación a 128 kbps mediante AAC y CBR de vídeo de 720p con una codificación a 4500 kbps mediante el perfil principal H.264.
	+ **Reproducción en dispositivos iOS y PC/Mac**. Este valor predefinido produce un recurso con las mismas características de recurso de transmisión suave (descrito anteriormente), pero en un formato que puede usarse para proporcionar transmisiones de HLS de Apple en dispositivos iOS. 

5. A continuación, especifique el nombre descriptivo del contenido de salida o acepte el valor predeterminado. A continuación, haga clic en el botón de comprobación para iniciar la operación de codificación y podrá realizar un seguimiento del progreso desde la parte inferior del portal.
6. Presione Aceptar.

	Después de realizar la codificación, la página de contenido contendrá el archivo codificado. 

	Para ver el progreso de la tarea de codificación, cambie a la página **TRABAJOS**.  

	Si el valor del tamaño de archivo no se actualiza después de que la codificación se haya realizado, presione el botón **Sincronizar metadatos**. De esta forma, se sincroniza el tamaño de archivo de salida del recurso con el tamaño de archivo real en el almacenamiento y se actualiza el valor en la página de contenido.	

##<a id="encrypt"></a>Indización Cifrar contenido

Si desea que los Servicios multimedia cifren el recurso de forma dinámica con una clave AES o PlayReady DRM, haga lo siguiente:

- Codifique su archivo intermedio (origen) en un conjunto de archivos MP4 de velocidad de bits adaptable o archivos  Smooth Streaming de velocidad de bits adaptable (los pasos de codificación se muestran en la sección [Codificación](#encode) ).
- Obtenga al menos la unidad de streaming a petición para el extremo de streaming desde el que planea entregar el contenido. Para obtener más información, vea [Escalación de unidades reservadas de streaming a petición](media-services-manage-origins.md#scale_streaming_endpoints/).
- Configure "Directiva del servicio de claves sin cifrado de aes predeterminada" o "Directiva del servicio de licencias de Playready predeterminada". Para más información, vea [Configuración de la directiva de autorización de claves de contenido](media-services-portal-configure-content-key-auth-policy.md).  


	Cuando esté listo para habilitar el cifrado, presione el botón **CIFRADO** situado en la parte inferior de la página **CONTENIDO**.

	![Encrypt][encrypt] 

	Una vez habilitado el cifrado, siempre que un reproductor solicita una secuencia, los Servicios multimedia usan la clave especificada para cifrar de forma dinámica el contenido con cifrado AES o PlayReady. Para descifrar la secuencia, el reproductor solicitará la clave del servicio de entrega de claves. Para decidir si el usuario está o no autorizado para obtener la clave, el servicio evalúa las directivas de autorización que especificó para la clave.

##<a id="publish"></a>Publicación de contenido

###Información general

Para proporcionar al usuario una dirección URL que pueda utilizarse para transmitir o descargar su contenido, primero necesitará "publicar" su recurso mediante la creación de un localizador. Los localizadores proporcionan acceso a los archivos contenidos en el recurso. Servicios multimedia admite dos tipos de localizadores: Localizadores OnDemandOrigin, utilizados para transmitir contenido multimedia (por ejemplo, MPEG DASH, HLS o Smooth Streaming) y localizadores de firma de acceso (SAS), que se usan para descargar archivos multimedia.

Al usar el Portal de administración de Azure para publicar sus recursos, los localizadores se crean automáticamente y se le proporcionará OnDemantOrigin en función de la dirección URL (si el recurso contiene un archivo .ism) o una dirección URL de SAS. 

Una dirección URL de SAS tiene el formato siguiente:

	{blob container name}/{asset name}/{file name}/{SAS signature}

Una dirección URL de streaming tiene el siguiente formato y se puede usar para reproducir los recursos de Smooth Streaming:

	{streaming endpoint name-media services account name}.streaming.mediaservices.windows.net/{locator ID}/{filename}.ism/Manifest

Para generar una dirección URL de streaming de HLS, anexe (format=m3u8-aapl) a la dirección URL.

	{streaming endpoint name-media services account name}.streaming.mediaservices.windows.net/{locator ID}/{filename}.ism/Manifest(format=m3u8-aapl)

Para generar una dirección URL de streaming de MPEG DASH, anexe (format=mpd-time-csf) a la dirección URL.

	{streaming endpoint name-media services account name}.streaming.mediaservices.windows.net/{locator ID}/{filename}.ism/Manifest(format=mpd-time-csf)


Los localizadores tienen fecha de caducidad. Al usar el Portal para publicar sus referencias, se crean los localizadores con una fecha de caducidad de 100 años. 

>[AZURE.NOTE] Si utiliza el Portal para crear los localizadores antes de marzo de 2015, se crearon localizadores con una fecha de caducidad de dos años.  

Para actualizar la fecha de caducidad de un localizador, utilice las API de [REST](http://msdn.microsoft.com/library/azure/hh974308.aspx#update_a_locator ) o [.NET](http://go.microsoft.com/fwlink/?LinkID=533259). Tenga en cuenta que, cuando se actualiza la fecha de caducidad de un localizador de SAS, cambia la dirección URL. 

###Publicar

Para utilizar el Portal para publicar un recurso, haga lo siguiente: 

1. Seleccione el recurso. 
2. A continuación, haga clic en el botón Publicar. 
	
 ![PublishedContent][publishedcontent]


## Reproducción de contenido desde el portal

El **Portal de administración de Azure** proporciona un reproductor de contenido que puede usar para probar el vídeo.

Haga clic en el vídeo deseado y haga clic en el botón **Reproducir** en la parte inferior del portal. 
 
Se aplican algunas consideraciones:

- Asegúrese de que se ha publicado el vídeo.
- El **REPRODUCTOR DE CONTENIDO DE SERVICIOS MULTIMEDIA** reproduce desde el extremo de streaming predeterminado. Si desea reproducir desde un extremo de streaming que no esté predeterminado, use otro reproductor. Por ejemplo, el [Reproductor de Servicios multimedia de Azure](http://amsplayer.azurewebsites.net/azuremediaplayer.html).
 

![AMSPlayer][AMSPlayer]

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
[encrypt]: ./media/media-services-manage-content/media-services-encrypt-content.png
[AMSPlayer]: ./media/media-services-players/media-services-portal-player.png

<!--HONumber=52-->