---
title: Ejemplos de Azure Event Hubs | Microsoft Docs
description: Ejemplos de Azure Event Hubs
services: event-hubs
documentationcenter: na
author: sethmanheim
manager: timlt
editor: 
ms.assetid: 
ms.service: event-hubs
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/05/2018
ms.author: sethm
ms.openlocfilehash: a581b7039a3631b7f1dc35816175242f892bd7dd
ms.sourcegitcommit: 168426c3545eae6287febecc8804b1035171c048
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 03/08/2018
---
# <a name="event-hubs-samples"></a>Ejemplos de Event Hubs 

El conjunto de ejemplos de Event Hubs muestra las características clave de [Azure Event Hubs](/azure/event-hubs/). En este artículo se categorizan y describen los ejemplos disponibles, con vínculos a cada uno.

En el momento de redactar este artículo, los ejemplos de Event Hubs se encuentran en distintos lugares:

- [Ejemplos de código para desarrolladores de MSDN](https://code.msdn.microsoft.com/site/search?query=event%20hubs&f%5B0%5D.Value=event%20hubs&f%5B0%5D.Type=SearchText&ac=5)
- [GitHub](https://github.com/Azure/azure-event-hubs/tree/master/samples)

Para más información sobre las diferentes versiones de .NET Framework, consulte [Marcos y destinos](/dotnet/articles/standard/frameworks).

Se agregarán más ejemplos con el tiempo, así que revise este sitio con frecuencia para comprobar las actualizaciones.

## <a name="net-standard"></a>.NET Standard

Los ejemplos siguientes muestran cómo enviar y recibir eventos mediante el [cliente de Event Hubs](https://github.com/Azure/azure-event-hubs-dotnet/blob/master/readme.md) para la [biblioteca de .NET Standard](/dotnet/articles/standard/library).

### <a name="send-events"></a>Envío de eventos 

El ejemplo de [Get started sending](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/Microsoft.Azure.EventHubs/SampleSender) (Introducción al envío) muestra cómo escribir una aplicación de consola de .NET Core que envía eventos a un centro de eventos.

### <a name="receive-events"></a>Recepción de eventos 

El ejemplo de [introducción a la recepción con el host del procesador de eventos](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/Microsoft.Azure.EventHubs/SampleEphReceiver) es una aplicación de consola de .NET Core que recibe mensajes desde un centro de eventos mediante el host del procesador de eventos.

## <a name="net-framework"></a>.NET Framework   

Estos ejemplos muestran otras características de Azure Event Hubs que tienen como destino la [biblioteca de .NET Framework](/dotnet/framework/index).
 
### <a name="notify-users-of-events-received"></a>Notificación a los usuarios sobre los eventos recibidos

El ejemplo de [AppToNotifyUsers](https://github.com/Azure-Samples/event-hubs-dotnet-user-notifications) muestra cómo notificar a los usuarios sobre los datos recibidos de los sensores o de otros sistemas.

### <a name="get-started-with-event-hubs"></a>Introducción a Event Hubs 

El ejemplo de [Event Hubs Getting Started](https://code.msdn.microsoft.com/Service-Bus-Event-Hub-286fd097) (Introducción a Event Hubs) muestra las funcionalidades básicas de Event Hubs, como la creación de un centro de eventos, el envío de eventos a un centro de eventos y el consumo de eventos mediante el [host del procesador de eventos](https://www.nuget.org/packages/Microsoft.Azure.ServiceBus.EventProcessorHost/).

### <a name="scale-out-event-processing"></a>Escalado horizontal del procesamiento de eventos 

El ejemplo de [escalado horizontal del procesamiento de eventos](https://code.msdn.microsoft.com/Service-Bus-Event-Hub-45f43fc3) muestra cómo utilizar el [host del procesador de eventos](https://www.nuget.org/packages/Microsoft.Azure.ServiceBus.EventProcessorHost/) para distribuir la carga de trabajo del consumo de flujo de Event Hubs. Muestra cómo implementar los objetos **EventProcessor** y **EventProcessorFactory** para administrar el flujo de eventos. 

## <a name="next-steps"></a>Pasos siguientes

Si desea conocer más información sobre las versiones de .NET Framework, visite los vínculos siguientes:

- [Marcos y destinos](/dotnet/articles/standard/frameworks)
- [.NET Framework 4.6 y 4.5](/dotnet/framework/index)

Si desea conocer más información acerca de Event Hubs, consulte los siguientes artículos:

- [Información general de Event Hubs](event-hubs-what-is-event-hubs.md)
- [Características de Event Hubs](event-hubs-features.md)
- [Preguntas más frecuentes sobre Event Hubs](event-hubs-faq.md)