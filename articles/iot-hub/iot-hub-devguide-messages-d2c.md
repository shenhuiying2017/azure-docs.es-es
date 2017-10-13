---
title: "Información sobre la mensajería de dispositivo a nube de Azure IoT Hub | Microsoft Docs"
description: "Guía del desarrollador: cómo utilizar la mensajería de dispositivo a nube con IoT Hub. Incluye información acerca del envío de datos de telemetría y datos sin telemetría, y del uso del enrutamiento para entregar los mensajes."
services: iot-hub
documentationcenter: .net
author: dominicbetts
manager: timlt
editor: 
ms.service: iot-hub
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/19/2017
ms.author: dobett
ms.openlocfilehash: 4e346306ecb8f4897a249454c537ce9a1a4c4011
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="send-device-to-cloud-messages-to-iot-hub"></a>Envío de mensajes de dispositivo a nube a IoT Hub

Para enviar alertas y telemetría de series temporales desde los dispositivos a su back-end de soluciones, envíe mensajes de dispositivo a nube desde el dispositivo a su IoT Hub. Para obtener una explicación de otras opciones de dispositivo a nube compatibles con IoT Hub, consulte [Guía de comunicación de dispositivo a nube][lnk-d2c-guidance].

Los mensajes del dispositivo a la nube se envían a través de un punto de conexión orientado al dispositivo (**/devices/{IdDeDispositivo}/messages/events**). Después, las reglas de enrutamiento enrutan los mensajes a uno de los puntos de conexión orientado al servicio en su IoT Hub. Las reglas de enrutamiento utilizan los encabezados y el cuerpo de los mensajes de dispositivo a nube que fluyen a través de su centro para determinar dónde enrutarlos. De forma predeterminada, los mensajes se enrutan al punto de conexión orientado al servicio integrado (**/messages/events**), que es compatible con [Event Hubs][lnk-event-hubs]. Por lo tanto, puede usar [los SDK e integración de Event Hubs][lnk-compatible-endpoint] estándar para recibir mensajes de dispositivo a nube.

IoT Hub implementa mensajería de dispositivo a nube mediante un patrón de mensajería de streaming. Los mensajes de dispositivo a nube de IoT Hub son más parecidos a *eventos* de [Event Hubs][lnk-event-hubs] que a *mensajes* de [Service Bus][lnk-servicebus] en que hay un gran volumen de eventos que se pasan a través del servicio que pueden leer varios lectores.

La mensajería de dispositivo a nube con IoT Hub tiene las siguientes características:

* Los mensajes de dispositivo a nube son duraderos y se conservan en el punto de conexión **messages/events** predeterminado de una instancia de IoT Hub hasta siete días.
* Los mensajes de dispositivo a nube pueden tener como máximo 256 KB y se pueden agrupar en lotes para optimizar los envíos. Los lotes pueden tener un tamaño máximo de 256 KB.
* Como se explica en la sección [Control del acceso a IoT Hub][lnk-devguide-security], IoT Hub habilita la autenticación y el control de acceso por dispositivo.
* IoT Hub le permite crear hasta 10 puntos de conexión personalizados. Los mensajes se entregan a los puntos de conexión según las rutas configuradas en su IoT Hub. Para obtener más información, consulte [Reglas de enrutamiento](#routing-rules).
* IoT Hub habilita millones de dispositivos conectados al mismo tiempo (consulte [Cuotas y limitación][lnk-quotas]).
* IoT Hub no permite el particionamiento arbitrario. Los mensajes de dispositivo a nube se dividen en particiones en función de su valor de **deviceId**de origen.

Para obtener más información acerca de las diferencias entre Azure IoT Hub y los servicios de Event Hubs, consulte [Comparación entre IoT Hub de Azure y Azure Event Hubs][lnk-comparison].

## <a name="send-non-telemetry-traffic"></a>Envío de tráfico sin telemetría

A menudo, además de los puntos de datos de telemetría, los dispositivos envían mensajes y solicitudes que requieren la ejecución y el control en el back-end de la solución. Por ejemplo, las alertas críticas que deben desencadenar una acción específica en el back-end. Puede escribir fácilmente una [regla de enrutamiento][lnk-devguide-custom] para enviar estos tipos de mensajes a un punto de conexión dedicado para su procesamiento según un encabezado en el mensaje o un valor en el cuerpo del mensaje.

Para más información sobre la mejor manera de procesar este tipo de mensaje, consulte [Tutorial: procesamiento de mensajes del dispositivo a la nube de IoT Hub][lnk-d2c-tutorial].

## <a name="route-device-to-cloud-messages"></a>Enrutamiento de mensajes de dispositivo a nube

Tiene dos opciones para enrutar mensajes de dispositivo a nube a las aplicaciones del back-end:

* Use el [punto de conexión compatible con Event Hub][lnk-compatible-endpoint] integrado para permitir que las aplicaciones de back-end lean los mensajes de dispositivo a nube recibidos por el centro. Para obtener información sobre el punto de conexión compatible con Event Hub integrado, consulte [Lectura de mensajes de dispositivo a nube desde el punto de conexión integrado][lnk-devguide-builtin].
* Utilice reglas de enrutamiento para enviar mensajes a puntos de conexión personalizados en su centro de IoT. Los puntos de conexión personalizados permiten que las aplicaciones back-end lean mensajes de dispositivo a nube con Event Hubs, colas de Service Bus o temas de Service Bus. Para obtener información acerca de los puntos de conexión personalizados y de enrutamientos, consulte [Uso de puntos de conexión y reglas de enrutamiento personalizados para mensajes de dispositivos a la nube][lnk-devguide-custom].

## <a name="anti-spoofing-properties"></a>Propiedades contra la suplantación

Para evitar la suplantación de dispositivos en los mensajes de dispositivo a nube, el Centro de IoT marca todos los mensajes con las siguientes propiedades:

* **ConnectionDeviceId**
* **ConnectionDeviceGenerationId**
* **ConnectionAuthMethod**

Las dos primeras contienen los valores **deviceId** y **generationId** del dispositivo de origen, tal como se indicó en [Propiedades de identidad del dispositivo][lnk-device-properties].

La propiedad **ConnectionAuthMethod** contiene un objeto JSON serializado con las siguientes propiedades:

```json
{
  "scope": "{ hub | device}",
  "type": "{ symkey | sas}",
  "issuer": "iothub"
}
```

## <a name="next-steps"></a>Pasos siguientes

Para obtener información sobre los SDK que puede utilizar para enviar mensajes de dispositivo a nube, consulte [SDK de Azure IoT][lnk-sdks].

El tutorial de [Introducción][lnk-get-started] muestra cómo enviar mensajes de dispositivo a nube desde dispositivos físicos y simulados. Para obtener más información, vea el tutorial [Procesamiento de mensajes de dispositivo a nube de IoT Hub mediante rutas][lnk-d2c-tutorial].

[lnk-devguide-builtin]: iot-hub-devguide-messages-read-builtin.md
[lnk-devguide-custom]: iot-hub-devguide-messages-read-custom.md
[lnk-comparison]: iot-hub-compare-event-hubs.md
[lnk-d2c-guidance]: iot-hub-devguide-d2c-guidance.md
[lnk-get-started]: iot-hub-get-started.md

[lnk-event-hubs]: http://azure.microsoft.com/documentation/services/event-hubs/
[lnk-servicebus]: http://azure.microsoft.com/documentation/services/service-bus/
[lnk-quotas]: iot-hub-devguide-quotas-throttling.md
[lnk-sdks]: iot-hub-devguide-sdks.md
[lnk-compatible-endpoint]: iot-hub-devguide-messages-read-builtin.md
[lnk-device-properties]: iot-hub-devguide-identity-registry.md#device-identity-properties
[lnk-devguide-security]: iot-hub-devguide-security.md
[lnk-d2c-tutorial]: iot-hub-csharp-csharp-process-d2c.md
