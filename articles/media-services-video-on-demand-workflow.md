<properties 
	pageTitle="Entrega de contenido multimedia bajo demanda con Servicios multimedia de Azure" 
	description="En este tema se describen escenarios comunes de entrega de contenido multimedia con Servicios multimedia de Azure." 
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
	ms.date="03/10/2015" 
	ms.author="juliako"/>


#Entrega de contenido multimedia bajo demanda con Servicios multimedia de Azure

##Información general

En este tema se describen los pasos de un flujo de trabajo típico de vídeo bajo demanda de Servicios multimedia de Azure (AMS). Cada paso se vincula a temas relevantes. En cuanto a las tareas que se pueden lograr con distintas tecnologías, hay botones que vinculan a la tecnología que usted elija (por ejemplo, .NET o REST).   

Tenga en cuenta que puede integrar Servicios multimedia con los procesos y herramientas existentes. Por ejemplo, codifique el contenido in situ y, a continuación, cárguelo en los Servicios multimedia para realizar la transcodificación en varios formatos y entregue a través de CDN de Azure o un CDN de otro proveedor. 

En el diagrama siguiente se muestran las partes principales de la plataforma de Servicios multimedia que intervienen en el flujo de trabajo de vídeo bajo demanda.
![Flujo de trabajo de VoD][vod-overview]

##<a id="vod_scenarios"></a>Escenarios comunes: Entrega de contenido multimedia bajo demanda. 

###Protección del contenido en almacenamiento y entrega de contenido multimedia en streaming sin cifrar

1. Cargue un archivo intermedio de alta calidad en un recurso.
	
	Se recomienda aplicar la opción de cifrado de almacenamiento a su recurso con el fin de proteger su contenido durante la carga y mientras se encuentra en reposo en el almacenamiento. 
1. Codifique en conjunto de archivos MP4 de velocidad de bits adaptable. 

	Se recomienda aplicar la opción de cifrado de almacenamiento al recurso de salida con el fin de proteger su contenido mientras se encuentra en reposo.
	
1. Configure la directiva de entrega de recursos(usada por paquetes dinámicos). 
	
	Si su recurso es almacenamiento cifrado, **debe** configurar la directiva de entrega de recursos. 

1. Publique el recurso creando un localizador OnDemand.

	Asegúrese de tener al menos una unidad de streaming reservada en el extremo de streaming desde el que desea transmitir el contenido.

1. Transmita el contenido publicado.

###Protección del contenido en el almacenamiento y entrega dinámica de contenido multimedia en streaming cifrado  

Para poder usar el cifrado dinámico, primero debe obtener al menos una unidad reservada de streaming en el extremo de streaming desde el que desea transmitir contenido cifrado.

1. Cargue un archivo intermedio de alta calidad en un recurso. Aplique la opción de cifrado de almacenamiento al recurso.
1. Codifique en conjunto de archivos MP4 de velocidad de bits adaptable. Aplique la opción de cifrado de almacenamiento al recurso de salida.
1. Cree la clave de contenido cifrado para el recurso que desea cifrar dinámicamente durante la reproducción.
2. Configure la directiva de autorización de clave de contenido.
1. Configure la directiva de entrega de recursos (se usa en el empaquetado dinámico y el cifrado dinámico).
1. Publique el recurso creando un localizador OnDemand.
1. Trasmita el contenido publicado. 

###Indizar contenido

1. Cargue un archivo intermedio de alta calidad en un recurso.
1. Indice el contenido.

	El trabajo de indexación genera archivos que se pueden usar como Subtítulos (CC) en la reproducción de vídeo. También genera archivos que le permiten realizar búsquedas en vídeo y saltar a la ubicación exacta del vídeo.	

1. Consuma contenido indizado.


###Entregar la descarga progresiva 

1. Cargue un archivo intermedio de alta calidad en un recurso.
1. Codifique en un solo MP4 o en un conjunto de archivos MP4 de velocidad de bits adaptable.
1. Publique el recurso creando un localizador OnDemand o SAS.

	Si utiliza el localizador OnDemand, asegúrese de tener al menos una unidad reservada de streaming en el extremo de streaming desde el que tiene pensado descargar contenido progresivamente.

	Si utiliza un localizador SAS, el contenido se descarga desde el almacenamiento de blobs de Azure. En este caso, no necesita tener unidades reservadas de streaming.
  
1. Descargue contenido de forma progresiva.

Este artículo contiene vínculos a temas que muestran cómo configurar su entorno de desarrollo y realizar las tareas mencionadas anteriormente.


##Conceptos

Para obtener información sobre los conceptos relacionados con la entrega de su contenido bajo demanda, consulte [Conceptos sobre Servicios multimedia](media-services-concepts.md).

##Creación de una cuenta de Servicios multimedia

Use el **Portal de administración de Azure** para [crear una cuenta de Servicios multimedia de Azure](media-services-create-account.md). 

##Configuración del entorno de desarrollo  

Elija **.NET** o **API de REST** para el entorno de desarrollo.

[AZURE.INCLUDE [media-services-selector-setup](../includes/media-services-selector-setup.md)]

##Conexión mediante programación  

Elija **.NET** o **API de REST** para conectarse mediante programación a los Servicios multimedia de Azure.

[AZURE.INCLUDE [media-services-selector-connect](../includes/media-services-selector-connect.md)]


##Configuración de extremos de streaming

Para obtener información general sobre los extremos de streaming e información sobre cómo administrarlos, consulte [Administración de extremos de Streaming en una cuenta de Servicios multimedia](media-services-manage-origins.md).

##Carga de elementos multimedia 

Cargue los archivos mediante el **Portal de administración de Azure**, **.NET** o **API de REST**.

[AZURE.INCLUDE [media-services-selector-upload-files](../includes/media-services-selector-upload-files.md)]

##Creación de trabajo y tareas

Un trabajo es una entidad que contiene metadatos sobre un conjunto de tareas (por ejemplo, codificación o indización). Cada tarea realiza una operación atómica en los recursos de entrada. Para ver un ejemplo de cómo crear trabajos de codificación, consulte:

Para obtener información general, consulte [Uso de trabajos de Servicios multimedia de Azure](media-services-jobs.md).

Obtenga un procesador multimedia adecuado para su tarea con **.NET** o la **API de REST**.

[AZURE.INCLUDE [media-services-selector-get-media-processor](../includes/media-services-selector-get-media-processor.md)]

En los siguientes ejemplos se crean trabajos de codificación con el **Portal de administración de Azure**, **.NET** o la **API de REST**.

[AZURE.INCLUDE [media-services-selector-encode](../includes/media-services-selector-encode.md)]

##Indización

[AZURE.INCLUDE [media-services-selector-index-content](../includes/media-services-selector-index-content.md)]

##Codificación 

**Información general**: [Codificación de contenido bajo demanda con Servicios multimedia de Azure](media-services-encode-asset.md).

Codificación avanzada con **Flujo de trabajo del Codificador multimedia Premium** mediante **.NET**. 

[AZURE.INCLUDE [media-services-selector-advanced-encoding](../includes/media-services-selector-advanced-encoding.md)]

Codifique con el **Codificador de Servicios multimedia** mediante el **Portal de administración de Azure**, **.NET** o **API de REST**.
 
[AZURE.INCLUDE [media-services-selector-encode](../includes/media-services-selector-encode.md)]

##Supervisión del progreso del trabajo

Supervise el progreso del trabajo mediante el **Portal de administración de Azure**, **.NET** o **API de REST**.

[AZURE.INCLUDE [media-services-selector-job-progress](../includes/media-services-selector-job-progress.md)]

##Creación de una clave de contenido

Cree una clave de contenido con la que desee cifrar su recurso mediante **.NET** o la **API de REST**.

[AZURE.INCLUDE [media-services-selector-create-contentkey](../includes/media-services-selector-create-contentkey.md)]

##Configuración de la directiva de autorización de claves de contenido 

Configure la protección de contenido y las directivas de autorización de claves mediante **.NET** o **API de REST**.

[AZURE.INCLUDE [media-services-selector-content-key-auth-policy](../includes/media-services-selector-content-key-auth-policy.md)]

##Configuración de la directiva de entrega de recursos

Configure la directiva de entrega de recursos con **.NET** o **API de REST**.

[AZURE.INCLUDE [media-services-selector-asset-delivery-policy](../includes/media-services-selector-asset-delivery-policy.md)]

##Publicación y entrega de recursos

**Información general**: [Información general sobre la entrega de contenido](media-services-deliver-content-overview.md)


Publique recursos (creando localizadores) mediante el **Portal de administración de Azure** o **.NET**.

[AZURE.INCLUDE [media-services-selector-publish](../includes/media-services-selector-publish.md)]

##Habilitación de CDN de Azure

Servicios multimedia admite la integración con CDN de Azure. Para obtener información sobre cómo habilitar CDN de Azure, consulte [Administración de extremos de streaming en una cuenta de Servicios multimedia](media-services-manage-origins.md#enable_cdn).

##Escalado de una cuenta de Servicios multimedia

Puede escalar **Servicios multimedia** mediante la especificación del número de **unidades reservadas de streaming** y **unidades reservadas de codificación** con las que desea aprovisionar la cuenta. 

También puede escalar la cuenta de Servicios multimedia agregándole cuentas de almacenamiento. Cada cuenta de almacenamiento está limitada a 500 TB. Para ampliar el almacenamiento más allá del límite predeterminado, puede asociar varias cuentas de almacenamiento a una sola cuenta de Servicios multimedia.

[Este](media-services-how-to-scale.md) tema contiene vínculos a temas relevantes.

##Reproducción de contenido con reproductores existentes

Para obtener más información, consulte [Reproducción de contenido con reproductores existentes](media-services-playback-content-with-existing-players.md).


[vod-overview]: ./media/media-services-overview/media-services-video-on-demand.png

<!--HONumber=52-->