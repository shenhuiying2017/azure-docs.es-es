<properties 
	pageTitle="Flujo de trabajo de vídeo bajo demanda de Servicios multimedia" 
	description="En este tema se describen los pasos de un flujo de trabajo de vídeo bajo demanda de Servicios multimedia." 
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
	ms.date="02/15/2015" 
	ms.author="juliako"/>


# Flujo de trabajo de vídeo bajo demanda de Servicios multimedia

## Información general

En este tema se describen los pasos de un flujo de trabajo típico de vídeo bajo demanda de Servicios multimedia de Azure (AMS). Cada paso se vincula a temas relevantes. En cuanto a las tareas que se pueden lograr con distintas tecnologías, hay botones que vinculan a la tecnología que usted elija (por ejemplo, .NET o REST).   

Tenga en cuenta que puede integrar Servicios multimedia con los procesos y herramientas existentes. Por ejemplo, codifique el contenido in situ y, a continuación, cárguelo en los Servicios multimedia para realizar la transcodificación en varios formatos y entregue a través de CDN de Azure o un CDN de otro proveedor. 

En el diagrama siguiente se muestran las partes principales de la plataforma de Servicios multimedia que intervienen en el flujo de trabajo de vídeo bajo demanda.
![VoD workflow][vod-overview]

Para obtener más información, consulte [Información general de Servicios multimedia](media-services-overview.md).

## Creación de una cuenta de Servicios multimedia

Use el **Portal de administración de Azure** para [crear una cuenta de Servicios multimedia de Azure](media-services-create-account.md). 

## Configuración de extremos de streaming

[trabajo en curso]

## Configuración del entorno de desarrollo  

Elija **.NET** o **API de REST** para el entorno de desarrollo.

[AZURE.INCLUDE [media-services-selector-setup](../includes/media-services-selector-setup.md)]

## Conexión mediante programación  

Elija **.NET** o **API de REST** para conectarse mediante programación a los Servicios multimedia de Azure.

[AZURE.INCLUDE [media-services-selector-connect](../includes/media-services-selector-connect.md)]

## Carga de elementos multimedia 

Cargue los archivos mediante el **Portal de administración de Azure**, **.NET** o **API de REST**.

[AZURE.INCLUDE [media-services-selector-upload-files](../includes/media-services-selector-upload-files.md)]

## Procesamiento de elementos multimedia: codificación, indización, supervisión de trabajos

### Obtención de procesador multimedia

Obtenga el procesador multimedia con **.NET** o **API de REST**.

[AZURE.INCLUDE [media-services-selector-get-media-processor](../includes/media-services-selector-get-media-processor.md)]

### Creación de trabajos 

Un trabajo es una entidad que contiene metadatos sobre un conjunto de tareas (por ejemplo, codificación o indización). Cada tarea realiza una operación atómica en los recursos de entrada. Para ver un ejemplo de cómo crear trabajos de codificación, consulte:

[AZURE.INCLUDE [media-services-selector-encode](../includes/media-services-selector-encode.md)]

### Supervisión del progreso del trabajo

Supervise el progreso del trabajo mediante el **Portal de administración de Azure**, **.NET** o **API de REST**.

[AZURE.INCLUDE [media-services-selector-job-progress](../includes/media-services-selector-job-progress.md)]

### Indización

[AZURE.INCLUDE [media-services-selector-index-content](../includes/media-services-selector-index-content.md)]

### Codificación 

Vea [Codificación con Servicios multimedia de Azure](media-services-encode-asset.md).

## Configuración de la directiva de autorización de claves de contenido 

Configure la protección de contenido y las directivas de autorización de claves mediante **.NET** o **API de REST**.

[AZURE.INCLUDE [media-services-selector-content-key-auth-policy](../includes/media-services-selector-content-key-auth-policy.md)]

## Configuración de la directiva de entrega de recursos

Configure la directiva de entrega de recursos con **.NET** o **API de REST**.

[AZURE.INCLUDE [media-services-selector-asset-delivery-policy](../includes/media-services-selector-asset-delivery-policy.md)]

## Publicación de recursos

Publique recursos (creando localizadores) mediante el **Portal de administración de Azure** o **.NET**.

[AZURE.INCLUDE [media-services-selector-publish](../includes/media-services-selector-publish.md)]

## Escalado de una cuenta de Servicios multimedia

Puede escalar **Servicios multimedia** mediante la especificación del número de **unidades reservadas de streaming** y **unidades reservadas de codificación** con las que desea aprovisionar la cuenta. 

También puede escalar la cuenta de Servicios multimedia agregándole cuentas de almacenamiento. Cada cuenta de almacenamiento está limitada a 500 TB. Para ampliar el almacenamiento más allá del límite predeterminado, puede asociar varias cuentas de almacenamiento a una sola cuenta de Servicios multimedia.

[Este](media-services-how-to-scale.md) tema contiene vínculos a temas relevantes.


## Reproducción del contenido

Para obtener más información, consulte [Reproducción de contenido con reproductores existentes](media-services-playback-content.md).

[vod-overview]: ./media/media-services-overview/media-services-video-on-demand.png
<!--HONumber=47-->
