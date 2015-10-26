<properties 
	pageTitle="Uso de partners para entregar licencias de Widevine a Servicios multimedia de Azure" 
	description="En este artículo se describe cómo puede usar Servicios multimedia de Azure (AMS) para entregar una secuencia que se cifra dinámicamente por AMS con DRM tanto de PlayReady como Widevine. La licencia de PlayReady procede del servidor de licencias PlayReady de Servicios multimedia y la licencia de Widevine se entrega al servidor de licencias de castLabs." 
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
	ms.date="10/07/2015"  
	ms.author="juliako"/>

#Uso de partners para entregar licencias de Widevine a Servicios multimedia de Azure

##Información general

Servicios multimedia de Microsoft Azure le permite entregar MPEG-DASH protegido con DRM de Widevine, que se cifra según la especificación de cifrado común (CENC).

>[AZURE.NOTE]Actualmente, Servicios multimedia no proporciona un servidor de licencias de Widevine. Puede usar los siguientes partners de AMS para ayudarle a entregar licencias de Widevine: [Axinom](http://www.axinom.com/press/ibc-axinom-drm-6/), [EZDRM](http://ezdrm.com/), [castLabs](http://castlabs.com/company/partners/azure/).

##castLabs

Puede usar [castLabs](http://castlabs.com/company/partners/azure/) para entregar licencias Widevine. Para obtener más información, consulte [Uso de castLabs para entregar licencias de DRM a Servicios multimedia de Azure](media-services-castlabs-integration.md).

##Axinom

Puede usar [castLabs](http://www.axinom.com/press/ibc-axinom-drm-6/) para entregar licencias Widevine. Para obtener más información, consulte [Uso de Axinom para entregar licencias de DRM a Servicios multimedia de Azure](media-services-axinom-integration.md).


##Rutas de aprendizaje de Servicios multimedia

Puede ver las rutas de aprendizaje de Servicios multimedia de Azure aquí:

- [Flujo de trabajo de streaming en vivo de Servicios multimedia de Azure](http://azure.microsoft.com/documentation/learning-paths/media-services-streaming-live/)
- [Flujo de trabajo de streaming a petición de Servicios multimedia de Azure](http://azure.microsoft.com/documentation/learning-paths/media-services-streaming-on-demand/)

##Consulte también

[Uso de cifrado dinámico común de PlayReady o Widevine](media-services-protect-with-drm.md).

[Blog de Mingfei](https://azure.microsoft.com/es-ES/blog/azure-media-services-adds-google-widevine-packaging-for-delivering-multi-drm-stream/)

<!---HONumber=Oct15_HO3-->