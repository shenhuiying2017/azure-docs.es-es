<properties 
	pageTitle="Flujo de trabajo de streaming en vivo de Servicios multimedia" 
	description="En este tema se describen los pasos de un flujo de trabajo típico de streaming en vivo de Servicios multimedia." 
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


#Flujo de trabajo de streaming en vivo de Servicios multimedia

##Información general

En este tema se describen los pasos de un flujo de trabajo típico de streaming en vivo de Servicios multimedia de Azure (AMS). Cada paso se vincula a temas relevantes. En cuanto a las tareas que se pueden lograr con distintas tecnologías, hay botones que vinculan a la tecnología que usted elija (por ejemplo, .NET o REST).   

Tenga en cuenta que puede integrar Servicios multimedia con los procesos y herramientas existentes. Por ejemplo, codifique el contenido in situ y, a continuación, cárguelo en los Servicios multimedia para realizar la transcodificación en varios formatos y entregue a través de CDN de Azure o un CDN de otro proveedor. 

En el diagrama siguiente se muestran las partes principales de la plataforma de Servicios multimedia que intervienen en el flujo de trabajo de vídeo bajo demanda.
![Live workflow][live-overview]

##Creación de una cuenta de Servicios multimedia

Use el **Portal de administración de Azure** para [crear una cuenta de Servicios multimedia de Azure](../media-services-create-account/).

##Configuración del entorno de desarrollo  

Elija **.NET** o **API de REST** para el entorno de desarrollo.

[AZURE.INCLUDE [media-services-selector-setup](../includes/media-services-selector-setup.md)]

##Conexión mediante programación  

Elija **.NET** o **API de REST** para conectarse mediante programación a los Servicios multimedia de Azure.

[AZURE.INCLUDE [media-services-selector-connect](../includes/media-services-selector-connect.md)]

##Trabajar con transcodificadores en vivo

Para más información, vea [Uso de codificadores dinámicos de terceros con los Servicios multimedia de Azure](https://msdn.microsoft.com/es-es/library/azure/dn783464.aspx).

##Administración de canales, programas y recursos

Para obtener más información, consulte [Streaming en vivo](https://msdn.microsoft.com/es-es/library/azure/dn783466.aspx).

##Configuración de la directiva de autorización de claves de contenido 

Configure la directiva de autorización de claves mediante **.NET** o **API de REST**.

[AZURE.INCLUDE [media-services-selector-content-key-auth-policy](../includes/media-services-selector-content-key-auth-policy.md)]

##Configuración de la directiva de entrega de recursos

Configure la directiva de entrega de recursos con **.NET** o **API de REST**.

[AZURE.INCLUDE [media-services-selector-asset-delivery-policy](../includes/media-services-selector-asset-delivery-policy.md)]

##Publicación de recursos

Publique recursos (creando localizadores) mediante el **Portal de administración de Azure** o **.NET**.

[AZURE.INCLUDE [media-services-selector-publish](../includes/media-services-selector-publish.md)]


##Escalado de una cuenta de Servicios multimedia

Puede escalar **Servicios multimedia** especificando el número de **unidades reservadas** que desea aprovisionar con su cuenta. 

Para obtener información sobre el escalado de unidades de streaming, vea: [Cómo escalar unidades de streaming](../media-services-manage-origins#scale_streaming_endpoints).



[live-overview]: ./media/media-services-overview/media-services-live-streaming-current.png
<!--HONumber=45--> 
