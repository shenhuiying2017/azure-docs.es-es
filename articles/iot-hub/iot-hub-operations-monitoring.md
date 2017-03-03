---
title: "Supervisión de operaciones de IoT Hub de Azure | Microsoft Docs"
description: "Describe cómo usar la supervisión de operaciones de IoT Hub de Azure para supervisar el estado de las operaciones de su centro de IoT Hub en tiempo real."
services: iot-hub
documentationcenter: 
author: nberdy
manager: timlt
editor: 
ms.assetid: a299f3a5-b14d-4586-9c3b-44aea14ed013
ms.service: iot-hub
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/13/2016
ms.author: nberdy
translationtype: Human Translation
ms.sourcegitcommit: 8f72f2ca66a5d1394e87c7c0f8d8dff9da73732f
ms.openlocfilehash: 612ef94efb9776ae0ce768de1b59fb208824da93
ms.lasthandoff: 02/08/2017


---
# <a name="iot-hub-operations-monitoring"></a>IoT Hub operations monitoring (Supervisión de operaciones de Centro de IoT)
La supervisión de operaciones del Centro de IoT permite supervisar el estado de las operaciones de su Centro de IoT en tiempo real. IoT Hub realiza el seguimiento de eventos a través de varias categorías de operaciones. Se puede optar por que los eventos de una o varias categorías se envíen a un punto de conexión de su centro de IoT para su procesamiento. Los usuarios pueden supervisar los datos en busca de errores o configurar un procesamiento más complejo basado en patrones de datos.

IoT Hub supervisa seis categorías de eventos:

* Operaciones de identidad de dispositivos
* Telemetría de dispositivo
* Mensajes de nube a dispositivo
* Conexiones
* Cargas de archivos
* Enrutamiento de mensajes

## <a name="how-to-enable-operations-monitoring"></a>Habilitación de la supervisión de operaciones
1. Cree un Centro de IoT. Puede encontrar instrucciones sobre cómo crear un centro de IoT en la guía [Introducción][lnk-get-started].
2. Abra la hoja de su Centro de IoT. Desde allí, haga clic en **Supervisión de operaciones**.
   
    ![][1]
3. Seleccione las categorías que desea supervisar y luego haga clic en **Guardar**. Los eventos están disponibles para su lectura desde el punto de conexión compatible con el Centro de eventos que aparece en **Configuración de supervisión**. El punto de conexión del Centro de IoT se denomina `messages/operationsmonitoringevents`.
   
    ![][2]

> [!NOTE]
> Al seleccionar el tipo de supervisión **Detallado** en la categoría **Conexiones**, IoT Hub generará más mensajes de diagnóstico. Para el resto de las categorías, la configuración **Detallado** cambia la cantidad de información que IoT Hub incluye en cada mensaje de error.

## <a name="event-categories-and-how-to-use-them"></a>Categorías de eventos y su uso
Cada categoría de supervisión de operaciones realiza el seguimiento de un tipo diferente de interacción con el Centro de IoT, y cada categoría de supervisión tiene un esquema que define cómo se estructuran los eventos de esa categoría.

### <a name="device-identity-operations"></a>Operaciones de identidad de dispositivos
La categoría de operaciones de identidad de dispositivo supervisa los errores que se producen cuando se intenta crear, actualizar o eliminar una entrada en el registro de la identidad del Centro de IoT. El seguimiento de esta categoría resulta útil para los escenarios de aprovisionamiento.

    {
        "time": "UTC timestamp",
         "operationName": "create",
         "category": "DeviceIdentityOperations",
         "level": "Error",
         "statusCode": 4XX,
         "statusDescription": "MessageDescription",
         "deviceId": "device-ID",
         "durationMs": 1234,
         "userAgent": "userAgent",
         "sharedAccessPolicy": "accessPolicy"
    }

### <a name="device-telemetry"></a>Telemetría de dispositivo
La categoría de telemetría del dispositivo realiza el seguimiento de los errores que se producen en el Centro de IoT y se relacionan con la canalización de telemetría. Esta categoría incluye los errores que se producen al enviar eventos de telemetría (por ejemplo, la limitación) y recibir eventos de telemetría (por ejemplo, un lector no autorizado). Tenga en cuenta que esta categoría no puede detectar los errores causados por el código que se ejecuta en el propio dispositivo.

    {
         "messageSizeInBytes": 1234,
         "batching": 0,
         "protocol": "Amqp",
         "authType": "{\"scope\":\"device\",\"type\":\"sas\",\"issuer\":\"iothub\"}",
         "time": "UTC timestamp",
         "operationName": "ingress",
         "category": "DeviceTelemetry",
         "level": "Error",
         "statusCode": 4XX,
         "statusType": 4XX001,
         "statusDescription": "MessageDescription",
         "deviceId": "device-ID",
         "EventProcessedUtcTime": "UTC timestamp",
         "PartitionId": 1,
         "EventEnqueuedUtcTime": "UTC timestamp"
    }

### <a name="cloud-to-device-commands"></a>Comandos de nube a dispositivo
La categoría de comandos de nube a dispositivo realiza el seguimiento de los errores que se producen en el centro de IoT Hub y se relacionan con la canalización de mensajes de nube a dispositivo. Esta categoría incluye errores que se producen al enviar mensajes de nube a dispositivo (por ejemplo, un remitente no autorizado), recibir mensajes de nube a dispositivo (por ejemplo, el número de entregas superado) y recibir mensajes de nube a dispositivo (por ejemplo, comentarios expirados). Esta categoría no detecta errores de un dispositivo que trata incorrectamente un mensaje de nube a dispositivo si este se ha entregado correctamente.

    {
         "messageSizeInBytes": 1234,
         "authType": "{\"scope\":\"hub\",\"type\":\"sas\",\"issuer\":\"iothub\"}",
         "deliveryAcknowledgement": 0,
         "protocol": "Amqp",
         "time": " UTC timestamp",
         "operationName": "ingress",
         "category": "C2DCommands",
         "level": "Error",
         "statusCode": 4XX,
         "statusType": 4XX001,
         "statusDescription": "MessageDescription",
         "deviceId": "device-ID",
         "EventProcessedUtcTime": "UTC timestamp",
         "PartitionId": 1,
         "EventEnqueuedUtcTime": “UTC timestamp"
    }

### <a name="connections"></a>Conexiones
La categoría Conexiones supervisa los errores que se producen cuando los dispositivos se conectan o desconectan de un Centro de IoT. El seguimiento de esta categoría resulta útil para identificar intentos de conexión no autorizados y para realizar el seguimiento de las situaciones en que una conexión se pierde para los dispositivos en las áreas de conectividad deficiente.

    {
         "durationMs": 1234,
         "authType": "{\"scope\":\"hub\",\"type\":\"sas\",\"issuer\":\"iothub\"}",
         "protocol": "Amqp",
         "time": " UTC timestamp",
         "operationName": "deviceConnect",
         "category": "Connections",
         "level": "Error",
         "statusCode": 4XX,
         "statusType": 4XX001,
         "statusDescription": "MessageDescription",
         "deviceId": "device-ID"
    }

### <a name="file-uploads"></a>Cargas de archivos

La categoría de carga de archivos supervisa los errores que se producen en el Centro de IoT y está relacionada con la funcionalidad de carga de archivos. Esta categoría incluye lo siguiente:

- Errores que se producen con el URI de SAS, como cuando caduca antes de que un dispositivo notifique al centro una carga completada.
- Cargas con errores notificadas por el dispositivo.
- Errores que se producen cuando no se encuentra un archivo en el almacenamiento durante la creación del mensaje de notificación de IoT Hub.

Tenga en cuenta que esta categoría no puede detectar los errores que se producen directamente mientras el dispositivo está cargando un archivo en el almacenamiento.


    {
         "authType": "{\"scope\":\"hub\",\"type\":\"sas\",\"issuer\":\"iothub\"}",
         "protocol": "HTTP",
         "time": " UTC timestamp",
         "operationName": "ingress",
         "category": "fileUpload",
         "level": "Error",
         "statusCode": 4XX,
         "statusType": 4XX001,
         "statusDescription": "MessageDescription",
         "deviceId": "device-ID",
         "blobUri": "http//bloburi.com",
         "durationMs": 1234
    }

### <a name="message-routing"></a>Enrutamiento de mensajes
La categoría de enrutamiento de mensajes realiza un seguimiento de los errores que se producen durante la evaluación de este proceso y el estado del punto de conexión según lo que observa IoT Hub. Esta categoría incluye eventos, como cuando una regla se evalúa como "sin definir", cuando IoT Hub marca un punto de conexión como inactivo y todos los errores recibidos de un punto de conexión. Tenga en cuenta que esta categoría no incluye errores específicos de los mensajes (por ejemplo, de limitación del dispositivo), que se notifican en la categoría de telemetría de dispositivo.
        
    {
        "messageSizeInBytes": 1234,
        "time": "UTC timestamp",
        "operationName": "ingress",
        "category": "routes",
        "level": "Error",
        "deviceId": "device-ID",
        "messageId": "ID of message",
        "routeName": "myroute",
        "endpointName": "myendpoint",
        "details": "ExternalEndpointDisabled"
    }

## <a name="next-steps"></a>Pasos siguientes
Para explorar aún más las funcionalidades de Centro de IoT, consulte:

* [Guía para desarrolladores de IoT Hub][lnk-devguide]
* [Simulación de un dispositivo con el SDK de puerta de enlace de IoT][lnk-gateway]

<!-- Links and images -->
[1]: media/iot-hub-operations-monitoring/enable-OM-1.png
[2]: media/iot-hub-operations-monitoring/enable-OM-2.png

[lnk-get-started]: iot-hub-csharp-csharp-getstarted.md
[lnk-diagnostic-metrics]: iot-hub-metrics.md
[lnk-scaling]: iot-hub-scaling.md
[lnk-dr]: iot-hub-ha-dr.md

[lnk-devguide]: iot-hub-devguide.md
[lnk-gateway]: iot-hub-linux-gateway-sdk-simulated-device.md

