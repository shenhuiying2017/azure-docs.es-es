<properties 
	pageTitle="Entrega de transmisión en directo con Servicios multimedia de Azure" 
	description="En este tema se describen los pasos de un flujo de trabajo típico de streaming en vivo de Servicios multimedia." 
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


#Entrega de transmisión en directo con Servicios multimedia de Azure

##Información general

En este tema se describen los pasos de un flujo de trabajo típico de streaming en vivo de Servicios multimedia de Azure (AMS). Cada paso se vincula a temas relevantes. En cuanto a las tareas que se pueden lograr con distintas tecnologías, hay botones que vinculan a la tecnología que usted elija (por ejemplo, .NET o REST).   

Tenga en cuenta que puede integrar Servicios multimedia con los procesos y herramientas existentes. Por ejemplo, codifique el contenido in situ y, a continuación, cárguelo en los Servicios multimedia para realizar la transcodificación en varios formatos y entregue a través de CDN de Azure o un CDN de otro proveedor. 

En el diagrama siguiente se muestran las partes principales de la plataforma de Servicios multimedia que intervienen en el flujo de trabajo de streaming en directo.

![Flujo de trabajo en directo][live-overview]

Este tema describe los conceptos relacionados con la transmisión en directo y vínculos a temas que muestran cómo realizar tareas de streaming en directo.

##Conceptos

Para los conceptos relacionados con el streaming en directo, vea [Conceptos de Servicios multimedia](media-services-concepts.md).

##Creación de una cuenta de Servicios multimedia

Use el **Portal de administración de Azure** para [Creación de una cuenta de Servicios multimedia de Azure](media-services-create-account.md).

##Configuración de extremos de streaming

Para obtener información general acerca de los extremos de streaming e información acerca de cómo administrarlos, vea [Administración de extremos de streaming en una cuenta de Servicios multimedia](media-services-manage-origins.md)

##Configuración del entorno de desarrollo  

Elija **.NET** o **API de REST** para el entorno de desarrollo.

[AZURE.INCLUDE [media-services-selector-setup](../../includes/media-services-selector-setup.md)]

##Conexión mediante programación  

Elija **.NET** o **API de REST** para conectarse mediante programación a los Servicios multimedia de Azure.

[AZURE.INCLUDE [media-services-selector-connect](../../includes/media-services-selector-connect.md)]


##Uso de codificadores en directo locales en secuencias de varias velocidades de bits de salida en un canal

##Trabajo con transcodificadores en directo de terceros

Para más información, vea [Uso de codificadores dinámicos de terceros con los Servicios multimedia de Azure](https://msdn.microsoft.com/library/azure/dn783464.aspx).

##Administración de canales, programas y recursos

**Información general**: [Información general de la administración de canales y programas](media-services-manage-channels-overview.md).

Elija **Portal**, **.NET** y **API de REST** para ver ejemplos.

[AZURE.INCLUDE [media-services-selector-manage-channels](../../includes/media-services-selector-manage-channels.md)]

##Configuración de la directiva de entrega de recursos

Configure la directiva de entrega de recursos con **.NET** o **API de REST**.

[AZURE.INCLUDE [media-services-selector-asset-delivery-policy](../../includes/media-services-selector-asset-delivery-policy.md)]

##Creación de una clave de contenido

Cree una clave de contenido con la que desee cifrar el recurso con **.NET** o **API de REST**.

[AZURE.INCLUDE [media-services-selector-create-contentkey](../../includes/media-services-selector-create-contentkey.md)]

##Configuración de la directiva de autorización de claves de contenido 

Configure la protección de contenido y las directivas de autorización de claves mediante **.NET** o **API de REST**.

[AZURE.INCLUDE [media-services-selector-content-key-auth-policy](../../includes/media-services-selector-content-key-auth-policy.md)]


##Publicación y entrega de recursos

**Información general**: [Información general de entrega de recursos](media-services-deliver-content-overview.md)

Publique recursos (creando localizadores) mediante el **Portal de administración de Azure** o **.NET**.

[AZURE.INCLUDE [media-services-selector-publish](../../includes/media-services-selector-publish.md)]


##Habilitación de CDN de Azure

Servicios multimedia admite la integración con CDN de Azure. Para obtener información acerca de cómo habilitar CDN de Azure, consulte [Administración de extremos de streaming en una cuenta de Servicios multimedia](media-services-manage-origins.md#enable_cdn).

##Escalado de una cuenta de Servicios multimedia

Puede escalar **Servicios multimedia** especificando el número de **unidades reservadas** que desea aprovisionar con su cuenta. 

Para obtener información sobre la escalación de unidades de streaming, vea: [Escalación de unidades de streaming](media-services-manage-origins.md#scale_streaming_endpoints.md).




[live-overview]: ./media/media-services-live-streaming-workflow/media-services-live-streaming-current.png


<!--HONumber=52--> 