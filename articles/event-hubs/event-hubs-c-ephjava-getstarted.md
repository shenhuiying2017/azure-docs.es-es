---
title: "Introducción a Event Hubs en C | Microsoft Docs"
description: Siga este tutorial para empezar a usar Azure Event Hubs, a enviar eventos con C y a recibirlos en Java mediante Host del procesador de eventos.
services: event-hubs
documentationcenter: 
author: jtaubensee
manager: timlt
editor: 
ms.assetid: aa6553f9-e12e-4568-9bf3-667f1c47a6cf
ms.service: event-hubs
ms.workload: na
ms.tgt_pltfrm: c
ms.devlang: csharp
ms.topic: article
ms.date: 09/27/2016
ms.author: jotaub;sethm
translationtype: Human Translation
ms.sourcegitcommit: 63cf1a5476a205da2f804fb2f408f4d35860835f
ms.openlocfilehash: e8e3708989f85a7a0d895140baad5d6370ba3146


---
# <a name="get-started-with-event-hubs"></a>Introducción a los Centros de eventos
[!INCLUDE [service-bus-selector-get-started](../../includes/service-bus-selector-get-started.md)]

## <a name="introduction"></a>Introducción
Centros de eventos es un sistema de recopilación de alta escalabilidad que puede recibir millones de eventos por segundo, habilitando una aplicación para procesar y analizar las grandes cantidades de datos generados por las aplicaciones y los dispositivos conectados. Una vez recopilados en los Centros de eventos, puede transformar y almacenar los datos usando cualquier proveedor de análisis en tiempo real o clúster de almacenamiento.

Para más información, consulte [Información general de Event Hubs][Información general de Event Hubs].

En este tutorial, aprenderá a recopilar mensajes en un Centro de eventos mediante una aplicación de consola en C y a recuperarlos en paralelo con la biblioteca [Host del procesador de eventos][Host del procesador de eventos] de C#.

Para completar este tutorial, necesitará lo siguiente:

* Un entorno de desarrollo de C. Para este tutorial, consideraremos la pila de gcc en una [VM Linux de Azure](../virtual-machines/virtual-machines-linux-quick-create-cli.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) con Ubuntu 14.04. En los vínculos externos se proporcionarán instrucciones para otros entornos.
* Microsoft Visual Studio Express para Windows
* Una cuenta de Azure activa. <br/>En caso de no tener ninguna, puede crear una cuenta gratuita en tan solo unos minutos. Para obtener más información, consulte <a href="http://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fen-us%2Fdevelop%2Fmobile%2Ftutorials%2Fget-started%2F" target="_blank">Evaluación gratuita de Azure</a>.

[!INCLUDE [event-hubs-create-event-hub](../../includes/event-hubs-create-event-hub.md)]

[!INCLUDE [service-bus-event-hubs-get-started-send-c](../../includes/service-bus-event-hubs-get-started-send-c.md)]

[!INCLUDE [service-bus-event-hubs-get-started-receive-ephjava](../../includes/service-bus-event-hubs-get-started-receive-ephjava.md)]

## <a name="run-the-applications"></a>Ejecución de las aplicaciones
Ahora está preparado para ejecutar las aplicaciones.

1. Ejecute el proyecto **Receiver** .
   
   ![][21]
2. Ejecute el programa **Sender** y verá que los eventos aparecen en la ventana del receptor.
   
   ![][24]

## <a name="next-steps"></a>Pasos siguientes
Ahora que ha creado una aplicación de trabajo que crea un centro de eventos y envía y recibe datos, puede pasar a los siguientes escenarios:

* Una completa [aplicación de ejemplo que usa Event Hubs][aplicación de ejemplo que usa Event Hubs].
* El ejemplo de [Escala horizontal del procesamiento de eventos con Centros de eventos][Escala horizontal del procesamiento de eventos con Centros de eventos].
* [Información general de Event Hubs][Información general de Event Hubs]

<!-- Images. -->
[21]: ./media/event-hubs-c-ephjava-getstarted/ephjava.png
[24]: ./media/event-hubs-c-ephjava-getstarted/receive-eph-c.png

<!-- Links -->
[Portal de Azure clásico]: https://manage.windowsazure.com/
[Host del procesador de eventos]: https://www.nuget.org/packages/Microsoft.Azure.ServiceBus.EventProcessorHost
[Información general de Event Hubs]: event-hubs-overview.md
[aplicación de ejemplo que usa Event Hubs]: https://code.msdn.microsoft.com/Service-Bus-Event-Hub-286fd097
[Escala horizontal del procesamiento de eventos con Centros de eventos]: https://code.msdn.microsoft.com/Service-Bus-Event-Hub-45f43fc3



<!--HONumber=Nov16_HO3-->


