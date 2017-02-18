---
title: "Uso de métricas para supervisar Azure IoT Hub | Microsoft Docs"
description: "Describe cómo usar las métricas de IoT Hub de Azure para evaluar y supervisar el estado general de los centros de IoT Hub."
services: iot-hub
documentationcenter: 
author: nberdy
manager: timlt
editor: 
ms.assetid: a47108fd-f994-4105-b21d-5b8f697b699c
ms.service: iot-hub
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/16/2016
ms.author: nberdy
translationtype: Human Translation
ms.sourcegitcommit: 64e69df256404e98f6175f77357500b562d74318
ms.openlocfilehash: e8cac4af4b971320429cc4c76b8d806e314e1143


---
# <a name="iot-hub-metrics"></a>Métricas de IoT Hub
Las métricas de IoT Hub ofrecen mejores datos sobre el estado de los recursos de Azure en la suscripción de Azure. Las métricas de IoT Hub permiten evaluar el estado general del servicio IoT Hub y de los dispositivos conectados a él. Las estadísticas orientadas al usuario son importantes porque ayudan a ver lo que está ocurriendo con su Centro de IoT y ayudan a determinar la causa raíz de los problemas sin necesidad de ponerse en contacto con el soporte técnico de Azure.

Las métricas están habilitadas de forma predeterminada. Puede ver las métricas de IoT Hub desde Azure Portal.

## <a name="how-to-view-iot-hub-metrics"></a>Cómo ver las métricas de IoT Hub
1. Cree un Centro de IoT. Puede encontrar instrucciones sobre cómo crear un centro de IoT en la guía [Introducción][lnk-get-started].
2. Abra la hoja de su Centro de IoT. Desde aquí, haga clic en **Métricas**.
   
    ![][1]
3. En la hoja de métricas, puede ver las métricas para su centro de IoT y crear vistas personalizadas de ellas. Puede elegir entre enviar los datos de las métricas a un punto de conexión de Event Hubs o a una cuenta de Azure Storage haciendo clic en **Configuración de diagnóstico**.
   
    ![][2]

## <a name="iot-hub-metrics-and-how-to-use-them"></a>Las métricas de IoT Hub y su uso
IoT Hub proporciona varias métricas para ofrecerle una visión general del estado de su centro y el número total de dispositivos conectados. Puede combinar información de varias métricas para conseguir una imagen más amplia del estado del Centro de IoT. La tabla siguiente describe las métricas de las que cada Centro de IoT realiza un seguimiento y cómo se relaciona cada métrica con el estado general del Centro de IoT.

| Métrica | Descripción de la métrica | Para qué se usa la métrica |
| --- | --- | --- |
| d2c.telemetry.ingress.allProtocol | Número de mensajes enviados en todos los dispositivos | Datos de información general sobre el envío de mensajes |
| d2c.telemetry.ingress.success | Número de mensajes completados correctamente en el Centro | Información general de las recepciones de mensajes completadas correctamente en el Centro |
| d2c.telemetry.egress.success | El recuento de todas las escrituras correctas en un punto de conexión | Información general de distribución ramificada de mensajes en función de las rutas de un usuario |
| d2c.telemetry.egress.invalid | El recuento de mensajes no entregados debido a incompatibilidad con el punto de conexión | Información general del número de errores al escribir en el conjunto de puntos de conexión del usuario. Los valores altos pueden indicar puntos de conexión configurados incorrectamente. |
| d2c.telemetry.egress.dropped | El recuento de mensajes descartados debido a un punto de conexión incorrecto | Información general sobre el número de mensajes descartados dada la configuración actual del centro de IoT |
| d2c.telemetry.egress.fallback | El recuento de mensajes que coinciden con la ruta de reserva | Para los usuarios que canalizan todos los mensajes a puntos de conexión diferentes del integrado, esta métrica muestra carencias en la configuración de enrutamiento |
| d2c.telemetry.egress.orphaned | El recuento de mensajes no coincide con ninguna ruta, incluida la ruta de reserva | Información general sobre el número de mensajes huérfanos dada la configuración actual del centro de IoT |
| d2c.endpoints.latency.eventHubs | La latencia media entre la entrada de mensajes en el centro de IoT y la entrada de mensajes en un punto de conexión de Event Hubs, en milisegundos | La propagación ayuda a los usuarios a identificar una configuración de punto de conexión deficiente |
| d2c.endpoints.latency.serviceBusQueues | La latencia media entre la entrada de mensajes en el centro de IoT y la entrada de mensajes en un punto de conexión de la cola de Service Bus, en milisegundos | La propagación ayuda a los usuarios a identificar una configuración de punto de conexión deficiente |
| d2c.endpoints.latency.serviceBusTopic | La latencia media entre la entrada de mensajes en el centro de IoT y la entrada de mensajes en un punto de conexión del tema de Service Bus, en milisegundos | La propagación ayuda a los usuarios a identificar una configuración de punto de conexión deficiente |
| d2c.endpoints.latency.builtIn.events | La latencia media entre la entrada de mensajes en el centro de IoT y la entrada de mensajes en el punto de conexión integrado (mensajes/eventos), en milisegundos | La propagación ayuda a los usuarios a identificar una configuración de punto de conexión deficiente |
| c2d.commands.egress.complete.success | Número de todos los mensajes de comando completados por el dispositivo receptor en todos los dispositivos |Junto con las métricas de abandono y rechazo, ofrece una visión general de la tasa de éxito global de comandos de nube a dispositivo |
| c2d.commands.egress.abandon.success | Número de todos los mensajes abandonados correctamente por el dispositivo receptor en todos los dispositivos |Destaca los posibles problemas si se están abandonando mensajes con una frecuencia superior a la esperada |
| c2d.commands.egress.reject.success | Número de todos los mensajes rechazados correctamente por el dispositivo receptor en todos los dispositivos |Destaca los posibles problemas si se están rechazando mensajes con una frecuencia superior a la esperada |
| devices.totalDevices | Número de dispositivos registrados en el centro de IoT |Número de dispositivos registrados en el Centro |
| devices.connectedDevices.allProtocol | Número de dispositivos conectados simultáneamente |Información general sobre el número de dispositivos conectados en el Centro |

## <a name="next-steps"></a>Pasos siguientes
Ahora que ha visto la información general sobre las métricas de IoT Hub, siga este vínculo para más información sobre la administración de IoT Hub de Azure:

* [Supervisión de operaciones][lnk-monitor]

Para explorar aún más las funcionalidades de Centro de IoT, consulte:

* [Guía para desarrolladores de IoT Hub][lnk-devguide]
* [Simulación de un dispositivo con el SDK de puerta de enlace de IoT][lnk-gateway]

<!-- Links and images -->
[1]: media/iot-hub-metrics/enable-metrics-1.png
[2]: media/iot-hub-metrics/enable-metrics-2.png

[lnk-get-started]: iot-hub-csharp-csharp-getstarted.md
[lnk-operations-monitoring]: iot-hub-operations-monitoring.md
[lnk-scaling]: iot-hub-scaling.md
[lnk-dr]: iot-hub-ha-dr.md

[lnk-monitor]: iot-hub-operations-monitoring.md

[lnk-devguide]: iot-hub-devguide.md
[lnk-gateway]: iot-hub-linux-gateway-sdk-simulated-device.md



<!--HONumber=Jan17_HO4-->


