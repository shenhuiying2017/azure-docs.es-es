---
title: Información de los puntos de conexión personalizados de Azure IoT Hub | Microsoft Docs
description: 'Guía del desarrollador: mediante las reglas de enrutamiento para enrutar los mensajes de dispositivo a nube a puntos de conexión personalizados.'
services: iot-hub
documentationcenter: .net
author: dominicbetts
manager: timlt
editor: ''
ms.service: iot-hub
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/09/2018
ms.author: dobett
ms.openlocfilehash: 3d54da43141dc2bdf34c9f71adc41dc7cf24ff10
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 04/16/2018
---
# <a name="use-message-routes-and-custom-endpoints-for-device-to-cloud-messages"></a>Uso de rutas de mensajes y de puntos de conexión personalizados para mensajes de dispositivo a nube

IoT Hub le permite enrutar [mensajes de dispositivo a nube][lnk-device-to-cloud] a puntos de conexión orientados al servicio de IoT Hub según las propiedades de mensaje. Las reglas de enrutamiento proporcionan la flexibilidad para enviar los mensajes donde deben ir sin la necesidad de servicios adicionales o de código personalizado. Cada regla de enrutamiento que configure tiene las siguientes propiedades:

| Propiedad      | DESCRIPCIÓN |
| ------------- | ----------- |
| **Name**      | Nombre único que identifica la regla. |
| **Origen**    | El origen del flujo de datos sobre el que se debe actuar. Por ejemplo, telemetría de dispositivo. |
| **Condition** | La expresión de consulta para la regla de enrutamiento que se ejecuta en el cuerpo y los encabezados del mensaje y determina si se trata de una coincidencia para el punto de conexión. Para más información sobre cómo crear una condición de ruta, consulte [Referencia: Lenguaje de consulta de IoT Hub para dispositivos gemelos y trabajos][lnk-devguide-query-language]. |
| **Punto de conexión**  | El nombre del punto de conexión donde IoT Hub envía mensajes que cumplen la condición. Los puntos de conexión deben estar en la misma región que IoT Hub, ya que, de lo caso contrario, se puede cobrar por escrituras entre regiones. |

Un solo mensaje puede cumplir la condición en varias reglas de enrutamiento, en cuyo caso IoT Hub entrega el mensaje al punto de conexión asociado a cada regla coincidente. IoT Hub también desduplica automáticamente la entrega de mensajes, por lo que si un mensaje cumple varias reglas que tienen el mismo destino, solo se escribe en ese destino una vez.

## <a name="endpoints-and-routing"></a>Puntos de conexión y enrutamiento

Un centro de IoT tiene un [punto de conexión integrado][lnk-built-in] predeterminado. Puede crear puntos de conexión personalizados a los que enrutar mensajes vinculando otros servicios de su suscripción al centro. IoT Hub admite actualmente los contenedores de Azure Storage, Event Hubs, las colas de Service Bus y los temas de Service Bus como puntos de conexión personalizados.

Cuando utilice el enrutamiento y los puntos de conexión personalizados, los mensajes se entregarán solo al punto de conexión integrado si no coinciden con ninguna regla. Para entregar mensajes al punto de conexión integrado, además de a un punto de conexión personalizado, agregue una ruta que envíe mensajes al punto de conexión **events**.

> [!NOTE]
> IoT Hub solo admite la escritura de datos en contenedores de Azure Storage como blobs.

> [!WARNING]
> Las colas y los temas de Service Bus con **Sesiones** o **Detección de duplicados** habilitadas no son compatibles como puntos de conexión personalizados.

Para más información sobre cómo crear puntos de conexión personalizados en IoT Hub, consulte [Referencia: Puntos de conexión de IoT Hub][lnk-devguide-endpoints].

Para obtener más información sobre la lectura de puntos de conexión personalizados, consulte:

* Leer de [contenedores de Azure Storage][lnk-getstarted-storage].
* Leer de [Event Hubs][lnk-getstarted-eh].
* Leer de [colas de Service Bus][lnk-getstarted-queue].
* Leer de [temas de Service Bus][lnk-getstarted-topic].

## <a name="latency"></a>Latency

Al enrutar mensajes de telemetría del dispositivo a la nube mediante puntos de conexión integrados, aumenta ligeramente la latencia de un extremo a otro tras las creación de la primera ruta.

En la mayoría de los casos, el aumento medio de la latencia es inferior a un segundo. Se puede supervisar la latencia con la [métrica de IoT Hub](https://docs.microsoft.com/azure/iot-hub/iot-hub-metrics) **d2c.endpoints.latency.builtIn.events**. La creación o eliminación de una ruta tras la primera no afecta a la latencia de un extremo a otro.

### <a name="next-steps"></a>Pasos siguientes

Para obtener más información sobre los puntos de conexión de IoT Hub, vea [Puntos de conexión de IoT Hub][lnk-devguide-endpoints].

Para obtener más información sobre el lenguaje de consulta que se usa para definir las reglas de enrutamiento, vea [Referencia: Lenguaje de consulta de IoT Hub para dispositivos gemelos, trabajos y enrutamiento de mensajes][lnk-devguide-query-language].

El tutorial [Procesamiento de mensajes de dispositivo a nube de IoT Hub mediante rutas][lnk-d2c-tutorial] muestra cómo usar las reglas de enrutamiento y los puntos de conexión personalizados.

[lnk-built-in]: iot-hub-devguide-messages-read-builtin.md
[lnk-device-to-cloud]: iot-hub-devguide-messages-d2c.md
[lnk-devguide-query-language]: iot-hub-devguide-query-language.md
[lnk-devguide-endpoints]: iot-hub-devguide-endpoints.md
[lnk-d2c-tutorial]: iot-hub-csharp-csharp-process-d2c.md
[lnk-getstarted-eh]: ../event-hubs/event-hubs-csharp-ephcs-getstarted.md
[lnk-getstarted-queue]: ../service-bus-messaging/service-bus-dotnet-get-started-with-queues.md
[lnk-getstarted-topic]: ../service-bus-messaging/service-bus-dotnet-how-to-use-topics-subscriptions.md
[lnk-getstarted-storage]: ../storage/blobs/storage-blobs-introduction.md
