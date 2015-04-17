<properties 
	pageTitle="Reproducción del contenido" 
	description="En este tema se describe cómo reproducir el contenido." 
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
	ms.date="02/16/2015" 
	ms.author="juliako"/>


# Reproducción del contenido

Servicios multimedia de Azure admite muchos formatos de streaming populares como Smooth Streaming, HTTP Live Streaming y MPEG-Dash. Este tema remite a reproductores existentes que puede usar para probar sus transmisiones.  

## Reproducción de contenido con existentes

En este tema se muestran los reproductores existentes con los que puede reproducir contenido.

>[AZURE.NOTE]Para reproducir contenido empaquetado o cifrado dinámicamente, asegúrese de obtener al menos una unidad de streaming para el extremo de streaming desde el que planea entregar el contenido. Para obtener información sobre la escalación de unidades de streaming, vea: [Cómo escalar unidades de streaming](../media-services-manage-origins#scale_streaming_endpoints).


### Reproductor de contenido de Servicios de multimedia del Portal de administración de Azure

El **Portal de administración de Azure** proporciona un reproductor de contenido que puede usar para probar el vídeo.

Haga clic en el vídeo deseado (asegúrese de que se [publicó](../media-services-manage-content#publish)) y haga clic en el botón **Reproducir** situado en la parte inferior del portal. 
 
Se aplican algunas consideraciones:

- El **REPRODUCTOR DE CONTENIDO DE SERVICIOS MULTIMEDIA** reproduce desde el extremo de streaming predeterminado. Si desea reproducir desde un extremo de streaming que no esté predeterminado, use otro reproductor. Por ejemplo, el [Reproductor de Servicios multimedia de Azure](http://amsplayer.azurewebsites.net/azuremediaplayer.html).
 

![AMSPlayer][AMSPlayer]

### Reproductor de Servicios multimedia de Azure

Use el [Reproductor de Servicios multimedia de Azure](http://amsplayer.azurewebsites.net/azuremediaplayer.html) para reproducir el contenido.


## Desarrollo de reproductores de vídeo

Para obtener información sobre cómo desarrollar sus propios reproductores, consulte [Desarrollo de reproductores de vídeo](../media-services-develop-video-players)
 
[AMSPlayer]: ./media/media-services-players/media-services-portal-player.png
<!--HONumber=47-->
