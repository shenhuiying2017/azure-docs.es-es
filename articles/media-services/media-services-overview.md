<properties 
	pageTitle="Información general de Servicios multimedia de Azure" 
	description="En este tema se proporciona información general de Servicios multimedia de Azure." 
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
	ms.date="08/11/2015"
	ms.author="juliako"/>

#Información general de Servicios multimedia de Azure

Servicios multimedia de Microsoft Azure es una plataforma extensible basada en la nube que permite a los desarrolladores crear aplicaciones de administración y entrega de contenido multimedia escalables. Servicios multimedia se basa en las API de REST, que permiten cargar, almacenar, codificar y empaquetar de forma segura contenido de audio o vídeo para la entrega bajo demanda y de streaming en vivo a varios clientes (por ejemplo, televisión, PC y dispositivos móviles).

Puede crear flujos de trabajo de un extremo a otro usando solamente Servicios multimedia. También puede usar componentes de terceros para algunas partes del flujo de trabajo. Por ejemplo, codifique mediante un codificador de terceros. A continuación, cargue, proteja, empaquete y entregue con Servicios multimedia.

Para compilar soluciones de Servicios multimedia, puede usar:

- [API de REST de Servicios multimedia](https://msdn.microsoft.com/library/azure/hh973617.aspx)
- Uno de los SDK de cliente disponibles: [SDK de Servicios multimedia de Azure para .NET](https://github.com/Azure/azure-sdk-for-media-services), [SDK de Azure para Java](https://github.com/Azure/azure-sdk-for-java), [Servicios multimedia de Azure para Node.js](https://github.com/fritzy/node-azure-media), [SDK de PHP de Azure](https://github.com/Azure/azure-sdk-for-php)
- Herramientas existentes: [Portal de administración de Azure](http://manage.windowsazure.com/) o [Azure-Media-Services-Explorer](https://github.com/Azure/Azure-Media-Services-Explorer).


La siguiente publicación describe flujos de trabajo de Servicios multimedia de Azure, desde la creación de contenido multimedia hasta el consumo. Puede descargar el póster aquí: [Póster de Servicios multimedia de Azure](http://www.microsoft.com/download/details.aspx?id=38195).

![Información general][overview]

**Contrato de nivel de servicio (SLA)**

- Garantizamos la disponibilidad del 99,9% de las transacciones de API de REST para la codificación de Servicios multimedia.
- Para el streaming, atenderemos correctamente las solicitudes de servicio con una garantía de disponibilidad del 99,9% para el contenido multimedia existente cuando se adquiera al menos una unidad de streaming.
- Para los canales en directo, garantizamos que los canales en ejecución tendrán una conectividad externa como mínimo el 99,9 % del tiempo.
- Para la protección de contenido, garantizamos que procesaremos correctamente las solicitudes clave como mínimo el 99,9% del tiempo.
- Para el indizador, atenderemos correctamente las solicitudes de tarea de indizador procesadas con una unidad reservada de codificación el 99,9% del tiempo.

	Para obtener más información, consulte [SLA de Microsoft Azure](http://azure.microsoft.com/support/legal/sla/).

##Conceptos

Para obtener más información, consulte [Conceptos](media-services-concepts.md).


##Entrega de multimedia a petición con Servicios multimedia de Azure

En el tema siguiente se describen los pasos de los flujos de trabajo comunes de vídeo bajo demanda de Servicios multimedia. El tema dirige a otros temas que muestran cómo realizar estos pasos con tecnologías compatibles con Servicios multimedia.

[Entrega de multimedia a petición con Servicios multimedia de Azure](media-services-video-on-demand-workflow.md).

##Entrega de transmisión en directo con Servicios multimedia de Azure

En el tema siguiente se describen los pasos de los flujos de trabajo comunes de streaming en vivo de Servicios multimedia. El tema dirige a otros temas que muestran cómo realizar estos pasos con tecnologías compatibles con Servicios multimedia.

[Entrega de streaming en vivo con Servicios multimedia de Azure](media-services-live-streaming-workflow.md).

##Consumo de contenido

Servicios multimedia de Azure proporciona las herramientas que necesita para crear aplicaciones cliente de reproductor enriquecidas y dinámicas para la mayoría de las plataformas, como dispositivos iOS, dispositivos Android, Windows, Windows Phone, Xbox y decodificadores (set-top boxes). El tema siguiente proporciona vínculos a los SDK y Player Framework que puede usar para desarrollar sus propias aplicaciones cliente que pueden consumir contenido multimedia en streaming desde Servicios multimedia.

[Desarrollo de aplicaciones de reproductor de vídeo](media-services-develop-video-players.md)

##Guía de patrones y prácticas

[Guía de patrones y prácticas](https://wamsg.codeplex.com/) [Documentación en línea](https://msdn.microsoft.com/library/dn735912.aspx) [Libro electrónico descargable](https://www.microsoft.com/download/details.aspx?id=42629)

##Soporte técnico

El [Soporte técnico de Azure](http://azure.microsoft.com/support/options/) proporciona opciones de soporte técnico para Azure, incluido Servicios multimedia.

##Pasos siguientes

[Entrega de streaming en vivo con Servicios multimedia de Azure](media-services-live-streaming-workflow.md)

[Desarrollo de aplicaciones de reproductor de vídeo](media-services-develop-video-players.md)
 
[Desarrollo de aplicaciones de reproductor de vídeo](media-services-develop-video-players.md)


<!-- Images -->
[overview]: ./media/media-services-overview/media-services-overview.png
 

<!---HONumber=August15_HO7-->