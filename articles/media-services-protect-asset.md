<properties 
	pageTitle="Cifrado de activos en Servicios multimedia de Azure" 
	description="Aprenda a usar la protección de Microsoft PlayReady para cifrar un activo en Servicios multimedia. Los ejemplos de código están escritos en C# y utilizan el SDK de Servicios multimedia para .NET. Los ejemplos de código están escritos en C# y utilizan el SDK de Servicios multimedia para .NET." 
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


#Cifrado de un recurso de forma dinámica con PlayReady o AES-128

Este artículo forma parte de la serie [Flujo de trabajo de vídeo bajo demanda de Servicios multimedia](media-services-video-on-demand-workflow.md) y [Flujo de trabajo de streaming en directo de Servicios multimedia](media-services-live-streaming-workflow.md).
  
##Información general

Servicios multimedia de Microsoft Azure le permite entregar el contenido cifrado (de forma dinámica) con Estándar de cifrado avanzado (AES) (mediante claves de cifrado de 128 bits) y PlayReady DRM. Los Servicios multimedia también proporcionan un servicio para entregar claves y licencias de PlayReady a los clientes autorizados. Para entregar contenido protegido, debe configurar la directiva de autorización de claves de contenido y configurar directivas de entrega de recursos.

##Configuración

Para obtener información sobre cómo configurar la directiva de autorización de claves de contenido 
[AZURE.INCLUDE [media-services-selector-content-key-auth-policy](../includes/media-services-selector-content-key-auth-policy.md)] 

Directivas de entrega de recursos
[AZURE.INCLUDE [media-services-selector-asset-delivery-policy](../includes/media-services-selector-asset-delivery-policy.md)]
 


<!--HONumber=52-->