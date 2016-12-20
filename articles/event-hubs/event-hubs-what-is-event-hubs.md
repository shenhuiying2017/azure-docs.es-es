---
title: "¿Qué es Centros de eventos de Azure? | Microsoft Docs"
description: "Información general y descripción de Centros de eventos de Azure"
services: event-hubs
documentationcenter: .net
author: sethmanheim
manager: timlt
editor: 
ms.assetid: 4391d750-5bbe-456d-9091-b416127bc754
ms.service: event-hubs
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 11/30/2016
ms.author: sethm
translationtype: Human Translation
ms.sourcegitcommit: 05ca343cfdfc602759eb3ea30a7186a0bb47bd74
ms.openlocfilehash: eddc3e7c4914936a8a83a042dc0f7d528b91f059


---
# <a name="what-is-azure-event-hubs"></a>¿Qué es Centros de eventos de Azure?
Centros de eventos de Azure es un servicio de introducción de datos altamente escalable que permite la introducción de millones de eventos por segundo para que pueda procesar y analizar grandes cantidades de datos generados por los dispositivos y aplicaciones conectados. Centros de eventos actúa como la "puerta principal" de una canalización de eventos y, una vez que los datos se recopilan en un Centro de eventos, se pueden transformar y almacenar con cualquier proveedor de análisis en tiempo real o adaptadores de procesamiento por lotes/almacenamiento. Centros de eventos desacopla la producción de un flujo de eventos desde el consumo de los eventos, para que los consumidores de eventos pueden tener acceso a los eventos según su propia programación. Para más información y detalles técnicos, consulte [Información general de los centros de eventos](event-hubs-overview.md).

## <a name="event-hubs-capabilities"></a>Capacidades de los Centros de eventos
Centros de eventos es un servicio de procesamiento de eventos que ofrece procesamiento de telemetría y eventos a escala masiva, con una latencia baja y una alta confiabilidad. Este servicio es especialmente útil para:

* Instrumentación de aplicaciones
* La experiencia del usuario o el procesamiento de flujos de trabajo
* Escenarios de Internet de las cosas (IoT)

Otras funcionalidades de Centros de eventos incluyen el seguimiento del comportamiento en aplicaciones móviles, la información sobre el tráfico de granjas de servidores web, la captura de eventos en el juego de juegos de consola o telemetría recopilados de máquinas industriales o vehículos conectados.

## <a name="next-steps"></a>Pasos siguientes
Para obtener información detallada acerca de los Centros de eventos, consulte los temas siguientes.

* [Información general de los Centros de eventos](event-hubs-overview.md)
* [Guía de programación de Centros de eventos](event-hubs-programming-guide.md)
* [Preguntas más frecuentes sobre la disponibilidad y el soporte técnico de los Centros de eventos](event-hubs-availability-and-support-faq.md)
* Empezar a trabajar con un [tutorial de Event Hubs][Event Hubs tutorial]
* Una [aplicación de ejemplo completa que usa Event Hubs][sample application that uses Event Hubs]

[Event Hubs tutorial]: event-hubs-csharp-ephcs-getstarted.md
[sample application that uses Event Hubs]: https://code.msdn.microsoft.com/Service-Bus-Event-Hub-286fd097



<!--HONumber=Dec16_HO1-->


