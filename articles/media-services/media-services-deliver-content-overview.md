<properties 
	pageTitle="Información general de entrega de contenido a los clientes" 
	description="Este tema proporciona información general de lo que implica la entrega de contenido con Servicios multimedia de Azure." 
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


#Información general de entrega de contenido a los clientes

##Información general

Cuando se trabaja con Servicios multimedia, uno de los escenarios más comunes es:

1. Cargar un archivo de entrada (llamado archivo intermedio) en un recurso. Por ejemplo, H.264, MP4 o WMV.
1. Codificar el recurso en un conjunto de MP4 de velocidades de bits adaptativas.
1. Publicar el recurso. 
2. Utilice [paquetes dinámicos](http://msdn.microsoft.com/library/azure/jj889436.aspx) para enviar el contenido a sus clientes en cualquiera de los siguientes formatos: MPEG DASH, Apple HLS o Smooth Streaming. 

Este tema proporciona información general de los conceptos [principales](../media-services-deliver-content.md#concepts) y vínculos a temas que muestran cómo realizar la entrega de contenido relacionado con [tareas](../media-services-deliver-content.md#tasks).

##<a id="concepts"></a>Conceptos

La lista siguiente describe los conceptos y terminología útiles cuando se entrega contenido multimedia.

###Localizadores

Para proporcionar al usuario una dirección URL que pueda utilizarse para transmitir o descargar su contenido, primero necesitará "publicar" su recurso mediante la creación de un localizador.  Los localizadores proporcionan un punto de entrada para tener acceso a los archivos que se encuentran en un recurso. Servicios multimedia admite dos tipos de localizadores: 

- Los localizadores **OnDemandOrigin**, que se usan para transmitir archivos multimedia (por ejemplo, MPEG DASH, HLS o Smooth Streaming) o archivos de descarga progresiva.
-  Los localizadores de direcciones URL (firma de acceso) de **SAS** que se utilizan para descargar archivos multimedia en el equipo local.

Se usa una **directiva de acceso** para definir los permisos (como lectura, escritura y lista) y la duración del acceso del cliente a un recurso determinado. Observe que el permiso de lista (AccessPermissions.List) no se debe usar al crear un localizador OnDemandOrigin.

Los localizadores tienen fecha de caducidad. Al usar el Portal para publicar sus referencias, se crean los localizadores con una fecha de caducidad de 100 años. 

>[AZURE.NOTE] Si utiliza el Portal para crear los localizadores antes de marzo de 2015, se crearon localizadores con una fecha de caducidad de dos años.  

Para actualizar la fecha de caducidad de un localizador, utilice las API de [REST](http://msdn.microsoft.com/library/azure/hh974308.aspx#update_a_locator ) o [.NET](http://go.microsoft.com/fwlink/?LinkID=533259). Tenga en cuenta que, cuando se actualiza la fecha de caducidad de un localizador de SAS, cambia la dirección URL. 
 
Los localizadores no están diseñados para administrar el control de acceso por usuario. Para conceder derechos de acceso diferentes a usuarios individuales, use las soluciones de administración de derechos digitales (DRM). Para obtener más información, consulte [Protección de archivos multimedia](http://msdn.microsoft.com/library/azure/dn282272.aspx).

Tenga en cuenta que al crear un localizador, puede haber un retraso de 30 segundos debido a los procesos de almacenamiento y propagación requeridos en Almacenamiento de Azure.


###Streaming adaptativo 

Las tecnologías de velocidad de bits adaptable permiten a las aplicaciones para reproductor de vídeo determinar las condiciones de red y seleccionar entre varias velocidades de bits. Cuando se degrada la comunicación de red, el cliente puede seleccionar una velocidad de bits más baja que permita al reproductor seguir reproduciendo el vídeo con una calidad inferior. A medida que mejoren las condiciones de red, el cliente puede cambiar a una velocidad de bits superior con calidad de vídeo mejorada. Servicios multimedia de Azure admite tecnologías de velocidad de bits adaptable siguiente: HTTP Live Streaming (HLS), Smooth Streaming, MPEG DASH y HDS.

Para proporcionar direcciones URL de streaming a los usuarios, primero debe crear un localizador OnDemandOrigin. Crear el localizador le brinda la ruta de acceso de base al recurso que contiene el contenido que desea transmitir. Sin embargo, para poder transmitir este contenido, es necesario modificar aún más esta ruta de acceso. Para construir una dirección URL completa al archivo de manifiesto del streaming, debe concatenar el valor de la ruta de acceso del localizador y el nombre de archivo del manifiesto (filename.ism). Luego, anexe /Manifiesto y un formato adecuado (si corresponde) a la ruta de acceso del localizador. 

También puede transmitir el contenido por una conexión SSL. Para ello, asegúrese de que las URL de streaming comienzan por HTTPS. 

Tenga en cuenta que solo puede transmitir por SSL si se creó el extremo de streaming desde el que se entrega el contenido a partir del 10 de septiembre de 2014. Si las direcciones URL de streaming se basan en los extremos de streaming creados después del 10 de septiembre, la dirección URL contendrá "streaming.mediaservices.windows.net" (el formato nuevo). Las direcciones URL de streaming que contengan "origin.mediaservices.windows.net" (el formato anterior) no son compatibles con SSL. Si la dirección URL tiene un formato antiguo y desea poder transmitir a través de SSL, cree un extremo de streaming nuevo. Utilice direcciones URL creadas en función del nuevo extremo de streaming para transmitir el contenido a través de SSL. 


####Formatos de la dirección URL de streaming:

**Formato Smooth Streaming**

{streaming endpoint name-media services account name}.streaming.mediaservices.windows.net/{locator ID}/{filename}.ism/Manifest

Ejemplo:

	http://testendpoint-testaccount.streaming.mediaservices.windows.net/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ism/Manifest


**Formato MPEG DASH**

{streaming endpoint name-media services account name}.streaming.mediaservices.windows.net/{locator ID}/{filename}.ism/Manifest(format=mpd-time-csf) 

Ejemplo

	http://testendpoint-testaccount.streaming.mediaservices.windows.net/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ism/Manifest(format=mpd-time-csf)

**Formato Apple HTTP Live Streaming (HLS) V4**

{streaming endpoint name-media services account name}.streaming.mediaservices.windows.net/{locator ID}/{filename}.ism/Manifest(format=m3u8-aapl)

	http://testendpoint-testaccount.streaming.mediaservices.windows.net/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ism/Manifest(format=m3u8-aapl)

**Formato Apple HTTP Live Streaming (HLS) V3**

{streaming endpoint name-media services account name}.streaming.mediaservices.windows.net/{locator ID}/{filename}.ism/Manifest(format=m3u8-aapl-v3)
	
	http://testendpoint-testaccount.streaming.mediaservices.windows.net/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ism/Manifest(format=m3u8-aapl-v3)

**HDS (solo para licencias de Adobe PrimeTime/Access)**

{streaming endpoint name-media services account name}.streaming.mediaservices.windows.net/{locator ID}/{filename}.ism/Manifest(format=f4m-f4f)

	http://testendpoint-testaccount.streaming.mediaservices.windows.net/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ism/Manifest(format=f4m-f4f)


###Empaquetado dinámico

Servicios multimedia proporciona paquetes dinámicos que permiten entregar contenido codificado MP4 de velocidad de bits adaptable o Smooth Streaming en formatos admitidos por Servicios multimedia (MPEG DASH, HLS, Smooth Streaming, HDS) sin tener que volver a empaquetar en estos formatos de streaming. 

Para aprovecharse de los paquetes dinámicos, deberá hacer lo siguiente:

- Codifique el archivo intermedio (origen) en un conjunto de archivos MP4 de velocidad de bits adaptable o de Smooth Streaming de velocidad de bits adaptable,
- Obtenga al menos la unidad de streaming a petición para el extremo de streaming desde el que planea entregar el contenido. Para obtener más información, consulte [Escalación de unidades reservadas de streaming a petición](media-services-manage-origins.md#scale_streaming_endpoints/).

Con el empaquetado dinámico solo necesita almacenar y pagar por los archivos en formato de almacenamiento sencillo y Servicios multimedia creará y servirá la respuesta adecuada en función de las solicitudes del cliente. 

Tenga en cuenta que además de poder usar las capacidades de empaquetado dinámico, las unidades reservadas de streaming a petición con capacidad de salida dedicada pueden adquirirse en incrementos de 200 Mbps. De manera predeterminada, el streaming a petición está configurado en un modelo de instancias compartidas para el que se comparten recursos de servidor (por ejemplo, proceso, capacidad de salida, entre otros) con los demás usuarios. Para mejorar el resultado del streaming a petición, se recomienda adquirir unidades reservadas de streaming a petición.

###Descarga progresiva 

La descarga progresiva le permite comenzar a reproducir archivos multimedia antes de haber descargado todo el archivo. No se puede descargar progresivamente archivos .ism * (ismv, isma, ismt e ismc). 

Para la descarga progresiva de contenido, utilice el tipo OnDemandOrigin de localizador. En el ejemplo siguiente se muestra la dirección URL que se basa en el tipo OnDemandOrigin de localizador:

	http://amstest1.streaming.mediaservices.windows.net/3c5fe676-199c-4620-9b03-ba014900f214/BigBuckBunny_H264_650kbps_AAC_und_ch2_96kbps.mp4

Se aplican las siguientes consideraciones:

- Debe descifrar los recursos cifrados en almacenamiento que desee transmitir desde el servicio de origen para la descarga progresiva.


###Descargar

Para descargar el contenido en un dispositivo cliente, debe crear un localizador de SAS. El localizador de SAS da acceso al contenedor de Almacenamiento Azure donde se encuentra el archivo. Para generar la dirección URL de descarga, tiene que insertar el nombre de archivo entre el host y la firma de SAS. 

En el ejemplo siguiente se muestra la dirección URL que se basa en el localizador de SAS:

	https://test001.blob.core.windows.net/asset-ca7a4c3f-9eb5-4fd8-a898-459cb17761bd/BigBuckBunny.mp4?sv=2012-02-12&se=2014-05-03T01%3A23%3A50Z&sr=c&si=7c093e7c-7dab-45b4-beb4-2bfdff764bb5&sig=msEHP90c6JHXEOtTyIWqD7xio91GtVg0UIzjdpFscHk%3D

Se aplican las siguientes consideraciones:

- Debe descifrar los recursos cifrados en almacenamiento que desee transmitir desde el servicio de origen para la descarga progresiva.
- Se producirá un error en una descarga que no se haya completado en 12 horas.



###Extremos de streaming

Un **extremo de streaming** representa un servicio de streaming que puede entregar contenido directamente a una aplicación de reproducción de cliente o a una red de entrega de contenido (CDN) para la distribución posterior. La secuencia de salida del servicio de extremo de streaming puede ser streaming en vivo o un recurso de vídeo a petición en la cuenta de Servicios multimedia. Además, puede controlar la capacidad del servicio de extremo de streaming para administrar las necesidades crecientes de ancho de banda mediante el ajuste de unidades reservadas de streaming. Debe asignar al menos una unidad reservada para las aplicaciones en un entorno de producción. Para obtener más información, consulte [Escalación de un servicio multimedia](media-services-manage-origins.md#scale_streaming_endpoints).

##<a id="tasks"></a>Tareas relacionadas con la entrega de recursos

###Configuración de la directiva de entrega de recursos

Configure la directiva de entrega de recursos con **.NET** o **API de REST**.

[AZURE.INCLUDE [media-services-selector-asset-delivery-policy](../../includes/media-services-selector-asset-delivery-policy.md)]

###Publicación de recursos

Publique recursos (creando localizadores) mediante el **Portal de administración de Azure** o **.NET**.

[AZURE.INCLUDE [media-services-selector-publish](../../includes/media-services-selector-publish.md)]


##Temas relacionados

[Actualización de los localizadores de Servicios multimedia después de revertir las claves de almacenamiento](media-services-roll-storage-access-keys.md)


<!--HONumber=52--> 