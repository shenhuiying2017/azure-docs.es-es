<properties
	pageTitle="Introducción a la entrega de vídeo bajo demanda (VoD) mediante el Portal de administración de Azure"
	description="Este tutorial le guiará por los pasos necesarios para implementar una aplicación de entrega de contenido de vídeo bajo demanda (VoD) con Servicios multimedia de Azure mediante el Portal de administración de Azure."
	services="media-services"
	documentationCenter=""
	authors="Juliako"
	manager="dwrede"
	editor=""/>

<tags
	ms.service="media-services"
	ms.workload="media"
	ms.tgt_pltfrm="na"
	ms.devlang="ne"
	ms.topic="get-started-article" 
	ms.date="07/16/2015"
	ms.author="juliako"/>


#Introducción a la entrega de vídeo bajo demanda (VoD) mediante el Portal de administración de Azure

[AZURE.INCLUDE [media-services-selector-get-started](../../includes/media-services-selector-get-started.md)]


>[AZURE.NOTE]Para completar este tutorial, deberá tener una cuenta de Azure. En caso de no tener ninguna, puede crear una cuenta de evaluación gratuita en tan solo unos minutos. Para obtener más información, consulte <a href="http://www.windowsazure.com/pricing/free-trial/?WT.mc_id=A8A8397B5" target="_blank">Evaluación gratuita de Azure</a>.

Este tutorial le indicará los pasos necesarios para implementar una aplicación básica de entrega de contenido de vídeo bajo demanda (VoD) mediante el Portal de administración de Azure.

En este tutorial rápido se muestran las siguientes tareas.

1.  Creación de una cuenta de Servicios multimedia
2.  Configuración de extremos de streaming
1.  Carga de un archivo de vídeo
1.  Codificación del archivo de origen en un conjunto de archivos MP4 de velocidad de bits adaptativa
1.  Publicación del recurso y obtención de direcciones URL de descarga progresiva y de streaming  
1.  Reproducción del contenido


##Creación de una cuenta de Servicios multimedia

1. En el [Portal de administración][], haga clic en **Nuevo**, después en **Servicios multimedia** y, finalmente, en **Creación rápida**.

	![Creación rápida de Servicios multimedia](./media/media-services-portal-get-started/wams-QuickCreate.png)

2. En **NOMBRE**, especifique el nombre de la cuenta nueva. El nombre de cuenta de Servicios multimedia debe estar compuesto de números o letras en minúscula, sin espacios y con una longitud de entre 3 y 24 caracteres.

3. En **REGIÓN**, seleccione la región geográfica que se usará para almacenar los registros de metadatos para la cuenta de Servicios multimedia. Solo las regiones de Servicios multimedia disponibles aparecen en el cuadro desplegable.

4. En **CUENTA DE ALMACENAMIENTO**, seleccione una cuenta de almacenamiento para proporcionar almacenamiento de blobs del contenido multimedia desde la cuenta de Servicios multimedia. Puede seleccionar una cuenta de almacenamiento existente en la misma región geográfica que la cuenta de Servicios multimedia o crear una nueva cuenta de almacenamiento. Se crea una nueva cuenta de almacenamiento en la misma región.

5. Si ha creado una nueva cuenta de almacenamiento, en **NOMBRE DE NUEVA CUENTA DE ALMACENAMIENTO**, especifique un nombre para la cuenta de almacenamiento. Las reglas para los nombres de cuenta de almacenamiento son las mismas que para las cuentas de Servicios multimedia.

6. Haga clic en **Creación rápida** en la parte inferior del formulario.

	Puede supervisar el estado del proceso en el área de mensajes situada en parte inferior de la ventana.

	Una vez que la cuenta se crea correctamente, el estado cambia a Activo.

	En la parte inferior de la página, se muestra el botón **ADMINISTRAR CLAVES**. Al hacer clic en este botón, se muestra un cuadro de diálogo con el nombre de cuenta de Servicios multimedia y las claves principales y secundarias. Necesitará el nombre de cuenta y la información de la clave principal para obtener acceso mediante programación a la cuenta de Servicios multimedia.


	![Página de Servicios multimedia](./media/media-services-portal-get-started/wams-mediaservices-page.png)

	Cuando haga doble clic en el nombre de cuenta, se mostrará la página de inicio rápido de forma predeterminada. La página le permite realizar algunas tareas de administración que también están disponibles en otras páginas del portal. Por ejemplo, puede cargar un archivo de vídeo desde esta página o hacerlo desde la página CONTENT.


##Configuración de extremos de streaming con el Portal

Cuando se trabaja con Servicios multimedia de Azure, uno de los escenarios más comunes es entregar streaming de velocidad de bits adaptable a los clientes. Con el streaming de velocidad de bits adaptable, el cliente puede cambiar a una secuencia de velocidad de bits mayor o menor que el vídeo mostrado, según el ancho de banda actual de la red, el uso de CPU y otros factores. Servicios multimedia admite las siguientes tecnologías de streaming adaptable: HTTP Live Streaming (HLS), Smooth Streaming, MPEG DASH y HDS (únicamente para licenciatarios de Adobe PrimeTime/Access).

Servicios multimedia proporciona paquetes dinámicos que permiten entregar contenido codificado MP4 de velocidad de bits adaptable o Smooth Streaming en formatos admitidos por Servicios multimedia (MPEG DASH, HLS, Smooth Streaming, HDS) sin tener que volver a empaquetar en estos formatos de streaming.

Para aprovecharse de los paquetes dinámicos, deberá hacer lo siguiente:

- codificar su archivo intermedio (origen) en un conjunto de archivos MP4 de velocidad de bits adaptable o archivos Smooth Streaming de velocidad de bits adaptable (los pasos de codificación se muestran más adelante en este tutorial),  
- obtener al menos una unidad de streaming para el **extremo de streaming** para el que planea entregar el contenido.

Con el empaquetado dinámico solo necesita almacenar y pagar por los archivos en formato de almacenamiento sencillo y Servicios multimedia creará y servirá la respuesta adecuada en función de las solicitudes del cliente.

Para cambiar el número de unidades reservadas de streaming, haga lo siguiente:

1. En el [Portal de administración](https://manage.windowsazure.com/), haga clic en **Servicios multimedia**. A continuación, haga clic en el nombre del servicio multimedia.

2. Seleccione la página EXTREMOS DE STREAMING. Luego, haga clic en el extremo de streaming que desea modificar.

3. Para especificar el número de unidades de streaming, seleccione la pestaña ESCALA y desplace el control deslizante de **capacidad reservada**.

	![Página de escala](./media/media-services-portal-get-started/media-services-origin-scale.png)

4. Presione el botón GUARDAR para guardar los cambios.

	La asignación de cualquier nueva unidad puede tardar unos 20 minutos en finalizarse.


	>[AZURE.NOTE]Actualmente, pasar de cualquier valor positivo de unidades de streaming a ninguno puede deshabilitar el streaming hasta una hora.
	>
	> Se utiliza el número más elevado de unidades especificadas durante el período de 24 horas al calcular el coste. Para obtener más información acerca del precio, consulte la página sobre [información del precio de Servicios multimedia](http://go.microsoft.com/fwlink/?LinkId=275107).

##Carga de contenido


1. En el [Portal de administración](http://go.microsoft.com/fwlink/?LinkID=256666&clcid=0x409), haga clic en **Servicios multimedia** y, a continuación, haga clic en el nombre de cuenta de Servicios multimedia.
2. Seleccione la página CONTENT.
3. Haga clic en el botón **Cargar** en la página o en la parte inferior del portal.
4. En el cuadro de diálogo **Cargar contenido**, diríjase al archivo del recurso deseado. Haga clic en el archivo y, a continuación, haga clic en **Abrir** o presione **Entrar**.

	![Cuadro de diálogo de carga de contenido][uploadcontent]

5. En el cuadro de diálogo de carga de contenido, haga clic en el botón de comprobación para aceptar el archivo y el nombre de contenido.
6. La carga comenzará y podrá realizar un seguimiento del proceso desde la parte inferior del portal.  

	![Estado del trabajo][status]

Una vez que la carga se haya completado, verá el nuevo recurso en la lista de contenido. Se establecerá una convención en la que el nombre tendrá anexo "**Source**" en la parte inferior para ayudar a realizar un seguimiento del nuevo contenido como contenido de origen para las tareas de codificación.

![ContentPage][contentpage]

Si el valor del tamaño del archivo no se actualiza después de que se detenga el proceso de carga, presione el botón **Sincronizar metadatos**. De esta forma, se sincroniza el tamaño de archivo del recurso con el tamaño del archivo real en el almacenamiento y se actualiza el valor en la página de contenido.


##Codificar contenido

###Información general
Para entregar vídeo digital a través de Internet, debe comprimir los archivos multimedia. Servicios multimedia proporciona un codificador multimedia que le permite especificar cómo desea que su contenido se codifique (por ejemplo, los códecs que se van a utilizar, el formato de archivo, la resolución y la velocidad de bits).

Cuando se trabaja con Servicios multimedia de Azure, uno de los escenarios más comunes es entregar streaming de velocidad de bits adaptable a los clientes. Con el streaming de velocidad de bits adaptable, el cliente puede cambiar a una secuencia de velocidad de bits mayor o menor que el vídeo mostrado, según el ancho de banda actual de la red, el uso de CPU y otros factores. Servicios multimedia admite las siguientes tecnologías de streaming adaptable: HTTP Live Streaming (HLS), Smooth Streaming, MPEG DASH y HDS (únicamente para licenciatarios de Adobe PrimeTime/Access).

Servicios multimedia proporciona paquetes dinámicos que permiten entregar contenido codificado MP4 de velocidad de bits adaptable o Smooth Streaming en formatos admitidos por Servicios multimedia (MPEG DASH, HLS, Smooth Streaming, HDS) sin tener que volver a empaquetar en estos formatos de streaming.

Para aprovecharse de los paquetes dinámicos, deberá hacer lo siguiente:

- Codifique su archivo intermedio (origen) en un conjunto de archivos MP4 de velocidad de bits adaptable o archivos Smooth Streaming de velocidad de bits adaptable (los pasos de codificación se muestran más adelante en este tutorial).
- Obtenga al menos la unidad de streaming a petición para el extremo de streaming desde el que planea entregar el contenido. Para obtener más información, consulte [Escalación de unidades reservadas de streaming a petición](media-services-manage-origins.md#scale_streaming_endpoints/).

Con el empaquetado dinámico solo necesita almacenar y pagar por los archivos en formato de almacenamiento sencillo y Servicios multimedia creará y servirá la respuesta adecuada en función de las solicitudes del cliente.

Tenga en cuenta que además de poder usar las capacidades de empaquetado dinámico, las unidades reservadas de streaming a petición con capacidad de salida dedicada pueden adquirirse en incrementos de 200 Mbps. De manera predeterminada, el streaming a petición está configurado en un modelo de instancias compartidas para el que se comparten recursos de servidor (por ejemplo, proceso, capacidad de salida, entre otros) con los demás usuarios. Para mejorar el resultado del streaming a petición, se recomienda adquirir unidades reservadas de streaming a petición.

###Codificación

En esta sección se describen los pasos que puede seguir para codificar el contenido con Azure Media Encoder mediante el Portal de administración.

1.  Seleccione el archivo que desea codificar. Si se admite la codificación para este tipo de archivo, se habilitará el botón PROCESAR en la parte inferior de la página de contenido.
4. En el cuadro de diálogo **Proceso**, seleccione el **procesador de Azure Media Encoder.5. Elija una de las **configuraciones de codificación**.

	![Process2][process2]


	El tema [Cadenas predefinidas de tarea para Azure Media Encoder](https://msdn.microsoft.com/library/azure/dn619392.aspx) explica lo que significan las categorías de cada valor predefinido en **Valores preestablecidos para el streaming adaptable (empaquetado dinámico)**, **Valores preestablecidos para la descarga progresiva**, **Valores preestablecidos heredados para el streaming adaptable**.


	Las **otras** configuraciones se describen a continuación:

	+ **Codificación con protección de contenido PlayReady**. Este valor preestablecido produce un activo codificado con protección de contenido PlayReady.  


		De forma predeterminada, se usa el servicio de licencias PlayReady de Servicios multimedia. Para especificar algún otro servicio del que los clientes pueden obtener una licencia para reproducir el contenido cifrado de PlayReady, use las API de REST o del SDK de Servicios multimedia para .NET. Para obtener más información, consulte [Uso de cifrado estático para proteger su contenido]() y establezca la propiedad **licenseAcquisitionUrl** en el valor preestablecido de Media Encryptor. Como alternativa, puede usar el cifrado dinámico y establecer la propiedad **PlayReadyLicenseAcquisitionUrl** tal y como se describe en [Uso del cifrado dinámico de PlayReady y del servicio de entrega de licencias](http://go.microsoft.com/fwlink/?LinkId=507720).
	+ **Reproducción en PC/Mac (a través de Flash/Silverlight)**. Este valor predefinido produce un recurso de transmisión suave con las siguientes características: 44,1 kHz 16 bits/CBR de audio estéreo de ejemplo con codificación a 96 kbps mediante AAC y CBR de vídeo de 720p con codificación con una velocidad de bits de 6 de 3400 kbps a 400 kbps mediante el perfil principal H.264 y GOP de dos segundos.
	+ **Reproducción a través de HTML5 (IE/Chrome/Safari)**. Este valor predefinido produce un archivo MP4 único con las siguientes características: 44,1 kHz 16 bits/CBR de audio estéreo de ejemplo con una codificación a 128 kbps mediante AAC y CBR de vídeo de 720p con una codificación a 4500 kbps mediante el perfil principal H.264.
	+ **Reproducción en dispositivos iOS y PC/Mac**. Este valor predefinido produce un recurso con las mismas características de recurso de transmisión suave (descrito anteriormente), pero en un formato que puede usarse para proporcionar transmisiones de HLS de Apple en dispositivos iOS.

5. A continuación, especifique el nombre descriptivo del contenido de salida o acepte el valor predeterminado. A continuación, haga clic en el botón de comprobación para iniciar la operación de codificación y podrá realizar un seguimiento del progreso desde la parte inferior del portal.
6. Presione Aceptar.

	Después de realizar la codificación, la página de contenido contendrá el archivo codificado.

	Para ver el progreso de la tarea de codificación, cambie a la página **TRABAJOS**.

	Si el valor del tamaño de archivo no se actualiza después de que la codificación se haya realizado, presione el botón **Sincronizar metadatos**. De esta forma, se sincroniza el tamaño de archivo de salida del recurso con el tamaño de archivo real en el almacenamiento y se actualiza el valor en la página de contenido.


##Publicación de contenido

###Información general

Para proporcionar al usuario una dirección URL que pueda utilizarse para transmitir o descargar su contenido, primero necesitará "publicar" su recurso mediante la creación de un localizador. Los localizadores proporcionan acceso a los archivos contenidos en el recurso. Servicios multimedia admite dos tipos de localizadores: OnDemandOrigin locators, utilizados para transmitir contenido (por ejemplo, MPEG DASH, HLS o Smooth Streaming) y localizadores de firma de acceso (SAS), que se usan para descargar archivos multimedia.

Al usar el Portal de administración de Azure para publicar sus recursos, los localizadores se crean automáticamente y se proporcionará una dirección URL basada en OnDemand (si el recurso contiene un archivo .ism) o una dirección URL de SAS.

Una dirección URL de SAS tiene el formato siguiente:

	{blob container name}/{asset name}/{file name}/{SAS signature}

Una dirección URL de streaming tiene el siguiente formato y se puede usar para reproducir los recursos de Smooth Streaming:

	{streaming endpoint name-media services account name}.streaming.mediaservices.windows.net/{locator ID}/{filename}.ism/Manifest

Para generar una dirección URL de streaming de HLS, anexe (format=m3u8-aapl) a la dirección URL.

	{streaming endpoint name-media services account name}.streaming.mediaservices.windows.net/{locator ID}/{filename}.ism/Manifest(format=m3u8-aapl)

Para generar una dirección URL de streaming de MPEG DASH, anexe (format=mpd-time-csf) a la dirección URL.

	{streaming endpoint name-media services account name}.streaming.mediaservices.windows.net/{locator ID}/{filename}.ism/Manifest(format=mpd-time-csf)


Los localizadores tienen fecha de caducidad. Al usar el Portal para publicar sus referencias, se crean los localizadores con una fecha de caducidad de 100 años.

>[AZURE.NOTE]Si utiliza el Portal para crear los localizadores antes de marzo de 2015, se crearon localizadores con una fecha de caducidad de dos años.

Para actualizar la fecha de caducidad de un localizador, utilice las [API de REST](http://msdn.microsoft.com/library/azure/hh974308.aspx#update_a_locator) o [.NET](http://go.microsoft.com/fwlink/?LinkID=533259). Tenga en cuenta que, cuando se actualiza la fecha de caducidad de un localizador de SAS, cambia la dirección URL.

###Publicar

Para utilizar el Portal para publicar un recurso, haga lo siguiente:

1. Seleccione el recurso.
2. A continuación, haga clic en el botón Publicar.

 ![Contenido publicado][publishedcontent]


##contenido desde el portal

El **Portal de administración de Azure** proporciona un reproductor de contenido que puede usar para probar el vídeo.

Haga clic en el vídeo deseado y haga clic en el botón **Reproducir** en la parte inferior del portal.

Se aplican algunas consideraciones:

- Asegúrese de que se ha publicado el vídeo.
- El **REPRODUCTOR DE CONTENIDO DE SERVICIOS MULTIMEDIA** reproduce desde el extremo de streaming predeterminado. Si desea reproducir desde un extremo de streaming que no esté predeterminado, use otro reproductor. Por ejemplo, el [Reproductor de Servicios multimedia de Azure](http://amsplayer.azurewebsites.net/azuremediaplayer.html).


![AMSPlayer][AMSPlayer]


##Paso siguiente

Obtenga más información sobre la creación de aplicaciones de vídeo bajo demanda [Creación de aplicaciones de VoD](media-services-video-on-demand-workflow.md)

###Recursos adicionales
- <a href="http://channel9.msdn.com/Shows/Azure-Friday/Azure-Media-Services-101-Get-your-video-online-now-">Servicios multimedia de Azure 101 - ¡Obtenga el vídeo en línea ahora!</a>
- <a href="http://channel9.msdn.com/Shows/Azure-Friday/Azure-Media-Services-102-Dynamic-Packaging-and-Mobile-Devices">Servicios multimedia de Azure 102 - Empaquetado dinámico y dispositivos móviles</a>


<!-- Anchors. -->


<!-- URLs. -->
[Portal de administración]: http://manage.windowsazure.com/


<!-- Images -->
[portaloverview]: ./media/media-services-portal-get-started/media-services-content-page.png
[publishedcontent]: ./media/media-services-portal-get-started/media-services-upload-content-published.png
[uploadcontent]: ./media/media-services-portal-get-started/UploadContent.png
[status]: ./media/media-services-portal-get-started/Status.png
[encoder]: ./media/media-services-manage-content/EncoderDialog2.png
[branding]: ./media/branding-reporting.png
[contentpage]: ./media/media-services-portal-get-started/media-services-content-page.png
[process]: ./media/media-services-manage-content/media-services-process-video.png
[process2]: ./media/media-services-portal-get-started/media-services-process-video2.png
[encrypt]: ./media/media-services-manage-content/media-services-encrypt-content.png
[AMSPlayer]: ./media/media-services-portal-get-started/media-services-portal-player.png
 

<!----HONumber=July15_HO4-->