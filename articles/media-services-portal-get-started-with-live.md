<properties 
	pageTitle="Uso del Portal de administración para crear canales que reciben secuencias en vivo de varias velocidades de bits de codificadores locales" 
	description="Este tutorial le guiará por los pasos necesarios para implementar una aplicación básica de streaming en vivo de Servicios multimedia donde un canal recibe una secuencia en vivo de velocidades de bits múltiples desde un codificador en directo local." 
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
	ms.topic="article" 
	ms.date="04/16/2015" 
	ms.author="juliako"/>


#Uso del Portal de administración para crear canales que reciben secuencias en vivo de varias velocidades de bits de codificadores locales

[AZURE.INCLUDE [media-services-selector-manage-channels](../includes/media-services-selector-manage-channels.md)]


Este tutorial le guiará por los pasos necesarios para implementar una aplicación básica de streaming en vivo de Servicios multimedia donde un **canal** recibe una secuencia en vivo de velocidades de bits múltiples desde un codificador en dircto local. Para obtener información detallada acerca de cómo trabajar con canales y componentes relacionados, consulte [Trabajo con canales que reciben secuencias en vivo de varias velocidades de bits de codificadores locales](media-services-channels-overview.md).

En este tutorial, se utiliza el Portal de administración de Azure para realizar las tareas siguientes:

2.  Configurar extremos de streaming
3.  Crear un canal
1.  Configurar un codificador en vivo e introducir la secuencia en vivo en el canal (se utiliza Wirecast en este paso)
1.  Crear un programa (y un recurso)
1.  Publicar el recurso y obtener las direcciones URL de streaming  
1.  Reproducir el contenido 
2.  Limpiar

##Requisitos previos
Los siguientes requisitos son necesarios para completar el tutorial.

- Para completar este tutorial, deberá tener una cuenta de Azure. En caso de no tener ninguna, puede crear una cuenta de evaluación gratuita en tan solo unos minutos. Para obtener más información, consulte [Evaluación gratuita de Azure](azure.microsoft.com).
- Una cuenta de Servicios multimedia. Para crear una cuenta de Servicios multimedia, consulte el tema de [creación de cuenta](media-services-create-account.md).
- Una cámara web y un codificador que puede enviar una secuencia en vivo con velocidad de bits múltiple.

	 
##Configuración de extremos de streaming con el Portal

Cuando se trabaja con los Servicios multimedia de Azure, uno de los escenarios más comunes es entregar streaming de velocidad de bits adaptable a los clientes. Con el streaming de velocidad de bits adaptable, el cliente puede cambiar a una secuencia de velocidad de bits mayor o menor que el vídeo mostrado, según el ancho de banda actual de la red, el uso de CPU y otros factores. Servicios multimedia admite las siguientes tecnologías de streaming adaptable: HTTP Live Streaming (HLS), Smooth Streaming, MPEG DASH y HDS (únicamente para licenciatarios de Adobe PrimeTime/Access).

Cuando se trabaja con streaming en vivo, un codificador en directo local (en nuestro caso Wirecast) introduce una secuencia en vivo de velocidad de bits múltiple en el canal. Cuando un usuario solicita el stream, Servicios multimedia usa paquetes dinámicos para volver a empaquetar la secuencia de origen en la secuencia de velocidad de bits adaptativa solicitado (HLS, DASH o Smooth).

Para aprovechar al máximo el empaquetado dinámico, debe obtener al menos una unidad de streaming para el **extremo de streaming** desde el que va a entregar el contenido.

Para cambiar el número de unidades reservadas de streaming, haga lo siguiente:

1. En el [Portal de administración](https://manage.windowsazure.com/), haga clic en **Servicios multimedia**. A continuación, haga clic en el nombre del servicio multimedia.

2. Seleccione la página EXTREMOS DE STREAMING. Luego, haga clic en el extremo de streaming que desea modificar.

3. Para especificar el número de unidades de streaming, seleccione la pestaña ESCALA y desplace el control deslizante de **capacidad reservada**.

	![Página de escala](./media/media-services-how-to-scale/media-services-origin-scale.png)

4. Presione el botón GUARDAR para guardar los cambios.

	La asignación de cualquier nueva unidad puede tardar unos 20 minutos en finalizarse.

	 
	>[AZURE.NOTE]Actualmente, pasar de cualquier valor positivo de unidades de streaming a ninguno puede deshabilitar el streaming hasta una hora.
	>
	> Se utiliza el número más elevado de unidades especificadas durante el período de 24 horas al calcular el coste. Para obtener más información acerca del precio, consulte la página sobre [información del precio de Servicios multimedia](http://go.microsoft.com/fwlink/?LinkId=275107).


##Crear un canal

En el portal de administración de Azure, seleccione la página **CANAL**. A continuación, haga clic en **NUEVO**. En el cuadro de diálogo **Crear un nuevo canal en vivo**, escriba un nombre para el canal.

![createchannel](./media/media-services-managing-channels/media-services-create-channel.png)

Presione **Aceptar**.

Tras unos minutos, el canal se crea y se inicia.

##Obtención de direcciones URL de introducción

Una vez creado el canal, obtendrá direcciones URL de introducción que se proporcionarán al codificador en directo. El codificador usa estas direcciones URL para introducir una secuencia en vivo.

![readychannel](./media/media-services-managing-channels/media-services-ready-channel.png)


![ingesturls](./media/media-services-managing-channels/media-services-ingest-urls.png)

Para obtener más información sobre las URL de introducción, consulte [Uso de codificadores locales para enviar secuencias en vivo de velocidad de bits múltiple a un canal](media-services-channels-overview.md)

##Configuración de un codificador en directo y de una secuencia en vivo de introducción 

>[AZURE.NOTE]Este paso requiere la URL de introducción del canal que se mencionó en el paso anterior.

Para obtener información detallada acerca de cómo configurar Wirecast y comenzar la introducción de la secuencia, consulte [Configuración de Wirecast](http://azure.microsoft.com/blog/2014/09/18/azure-media-services-rtmp-support-and-live-encoders/).

>[AZURE.NOTE]Si por cualquier motivo detiene el codificador y después tiene que reiniciarlo, deberá restablecer el canal haciendo clic en el comando **RESTABLECER** en el Portal de administración de Azure.


##Creación y administración de un programa

###Información general

Un canal está asociado a programas que le permiten controlar la publicación y el almacenamiento de segmentos en una secuencia en vivo. Los canales administran los programas. La relación entre canales y programas es muy similar a los medios tradicionales, donde un canal tiene un flujo constante de contenido y un programa se enfoca algún evento programado en dicho canal.

Puede especificar la cantidad de horas que desea conservar el contenido grabado del programa en la configuración de la duración de **Ventana de archivo**. Este valor se puede establecer desde un mínimo de cinco minutos a un máximo de 25 horas. La duración de la ventana de archivo también indica el tiempo máximo que los clientes pueden buscar hacia atrás a partir de la posición en vivo actual. Los programas pueden transmitirse durante la cantidad de tiempo especificada, pero el contenido que escape de esa longitud de ventana se descartará continuamente. El valor de esta propiedad también determina durante cuánto tiempo los manifiestos de cliente pueden crecer.

Cada programa está asociado a un recurso. Para publicar el programa, debe crear un localizador a petición para el recurso asociado. Contar con este localizador le permitirá crear una dirección URL de streaming que puede proporcionar a sus clientes.

Un canal es compatible con hasta tres programas en ejecución simultánea, por lo que puede crear varios archivos del mismo flujo entrante. Esto le permite publicar y archivar distintas partes de un evento, según sea necesario. Por ejemplo, el requisito de su empresa es solo archivar seis horas de un programa, pero difundir solo los últimos diez minutos. Para lograrlo, necesita crear dos programas en ejecución simultánea. Un programa está definido para archivar seis horas del evento, pero no está publicado. El otro programa está definido para archivar durante diez minutos y este programa sí se publica.

No debe volver a usar programas existentes para eventos nuevos. En su lugar, cree e inicie un programa nuevo para cada evento como se describe en la sección Programación de aplicaciones de streaming en vivo.

Inicie el programa cuando esté listo para iniciar el streaming y el archivo. Detenga el programa cuando quiera detener el streaming y el archivo del evento.

Para eliminar contenido archivado, detenga y elimine el programa y, a continuación, elimine el recurso asociado. No se puede eliminar un recurso si se usa un programa; primero se debe eliminar el programa.

Incluso después de detener y eliminar el programa, los usuarios podrán transmitir el contenido archivado como un vídeo a petición siempre que no elimine el recurso.

Si desea conservar el contenido archivado, pero no hacerlo disponible para la transmisión, elimine el localizador de streaming.

###Creación/inicio/detención de programas

Cuando la secuencia fluye en el canal, puede comenzar el evento de streaming mediante la creación de un recurso, un programa y el localizador de streaming. Se archivará la secuencia y estará disponible a los usuarios a través del extremo de streaming.

Existen dos formas de iniciar un evento:

1. En la página **CANAL**, presione **AGREGAR** para agregar un nuevo programa.

	Especifique lo siguiente: nombre del programa, nombre de recurso, ventana de archivo y opción de cifrado.
	
	![createprogram](./media/media-services-managing-channels/media-services-create-program.png)
	
	Si ha dejado **Publicar este programa ahora** activado, el programa creará las direcciones URL de publicación.
	
	Puede presionar **INICIO**, cuando esté preparado para transmitir por streaming el programa.

	Una vez iniciado el programa, puede presionar REPRODUCIR para empezar a reproducir el contenido.


	![createdprogram](./media/media-services-managing-channels/media-services-created-program.png)

2. También, puede utilizar un acceso directo y presionar el botón **INICIAR STREAMING** situado en la página **CANAL**. Se creará un recurso, un programa y el localizador de streaming.

	El programa se denomina DefaultProgram y la ventana de archivo se establece en una hora.

	Puede reproducir el programa publicado desde la página CANAL.

	![channelpublish](./media/media-services-managing-channels/media-services-channel-play.png)


Si hace clic en **DETENER STREAMING** en la página **CANAL**, el programa predeterminado se detiene y se elimina. El recurso seguirá estando allí y se puede publicar o cancelar su publicación desde la página **CONTENIDO**.

Si cambia a la página **CONTENIDO**, verá los recursos que se crearon para los programas.

![contentasset](./media/media-services-managing-channels/media-services-content-assets.png)


##Reproducción de contenido

Para proporcionar al usuario una dirección URL que pueda utilizar para transmitir por streaming el contenido, primero tendrá que "publicar" el recurso (tal como se describió en la sección anterior) mediante la creación de un localizador (cuando se publica un recurso mediante el Portal, los localizadores se crean automáticamente). Los localizadores proporcionan acceso a los archivos contenidos en el recurso.

Dependiendo del protocolo de streaming que desee utilizar para reproducir el contenido, tendrá que modificar la dirección URL que obtiene del vínculo **URL DE PUBLICACIÓN** del canal\\programa.

El empaquetado dinámico se encargará de empaquetar la secuencia en vivo en el protocolo especificado.

De manera predeterminada, una dirección URL de streaming tiene el siguiente formato y se puede usar para reproducir los recursos de Smooth Streaming:

	{streaming endpoint name-media services account name}.streaming.mediaservices.windows.net/{locator ID}/{filename}.ism/Manifest

Para generar una dirección URL de streaming de HLS, anexe (format=m3u8-aapl) a la dirección URL.

	{streaming endpoint name-media services account name}.streaming.mediaservices.windows.net/{locator ID}/{filename}.ism/Manifest(format=m3u8-aapl)

Para generar una dirección URL de streaming de MPEG DASH, anexe (format=mpd-time-csf) a la dirección URL.

	{streaming endpoint name-media services account name}.streaming.mediaservices.windows.net/{locator ID}/{filename}.ism/Manifest(format=mpd-time-csf)

Para obtener más información acerca de la entrega de contenido, consulte [Entregar de contenido](media-services-deliver-content-overview.md).

Puede reproducir secuencias de Smooth Streaming mediante el [reproductor AMS](http://amsplayer.azurewebsites.net/azuremediaplayer.html) o usar dispositivos iOS y Android para reproducir HLS versión 3.

##Limpieza

Si se realizan eventos de streaming y desea limpiar los recursos aprovisionados anteriormente, siga el procedimiento siguiente.

- Detenga la inserción de la secuencia en el codificador.
- Detenga el canal. Cuando se detiene el canal, no se incurrirá en ningún cargo. Cuando necesite iniciarlo de nuevo, tendrá la misma URL de introducción, por lo que no necesitará volver a configurar su codificador.
- Puede detener el extremo de streaming, a menos que desee seguir proporcionando el archivo de su evento en vivo como una secuencia a petición. Cuando el canal está en estado detenido, no se incurrirá en ningún cargo.



##Paso siguiente

Obtenga más información acerca de la creación de aplicaciones de streaming en vivo [Creación de aplicaciones de streaming en vivo](media-services-live-streaming-workflow.md)

###Recursos adicionales
- <a href="http://channel9.msdn.com/Shows/Azure-Friday/Azure-Media-Services-101-Get-your-video-online-now-">Servicios multimedia de Azure 101 - ¡Obtenga el vídeo en línea ahora!</a>
- <a href="http://channel9.msdn.com/Shows/Azure-Friday/Azure-Media-Services-102-Dynamic-Packaging-and-Mobile-Devices">Servicios multimedia de Azure 102 - Empaquetado dinámico y dispositivos móviles</a>


<!-- Anchors. -->


<!-- URLs. -->
[Management Portal]: http://manage.windowsazure.com/

<!-- Images -->

<!--HONumber=52-->
