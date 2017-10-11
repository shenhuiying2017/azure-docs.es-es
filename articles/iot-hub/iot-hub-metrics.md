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
ms.date: 08/25/2017
ms.author: nberdy
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: e850370faf2d271b4adad1af48c1ead7b316fa67
ms.sourcegitcommit: 18ad9bc049589c8e44ed277f8f43dcaa483f3339
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 08/29/2017
---
# <a name="understand-iot-hub-metrics"></a>Comprender las métricas de IoT Hub
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

|Métrica|Nombre de métrica para mostrar|Unidad|Tipo de agregación|Description|
|---|---|---|---|---|
|d2c.telemetry.ingress.allProtocol|Intentos de envío de mensajes de telemetría|Recuento|Total|Número de mensajes de telemetría de dispositivo a la nube para enviar a IoT Hub|
|d2c.telemetry.ingress.success|Mensajes de telemetría enviados|Recuento|Total|Número de mensajes de telemetría de dispositivo a la nube enviados correctamente a IoT Hub|
|c2d.commands.egress.complete.success|Comandos completados|Recuento|Total|Número de comandos de la nube al dispositivo que el dispositivo ha completado correctamente|
|c2d.commands.egress.abandon.success|Comandos abandonados|Recuento|Total|Número de comandos de la nube al dispositivo que el dispositivo ha abandonado|
|c2d.commands.egress.reject.success|Comandos rechazados|Recuento|Total|Número de comandos de la nube al dispositivo que el dispositivo ha rechazado|
|devices.totalDevices|Número total de dispositivos|Recuento|Total|Número de dispositivos registrados en IoT Hub|
|devices.connectedDevices.allProtocol|Dispositivos conectados|Recuento|Total|Número de dispositivos conectados a IoT Hub|
|d2c.telemetry.egress.success|Mensajes de telemetría entregados|Recuento|Total|Número de veces que los mensajes se han escrito correctamente mensajes en los puntos de conexión (total)|
|d2c.telemetry.egress.dropped|Mensajes descartados|Recuento|Total|Número de mensajes descartados porque no coincidían con ninguna ruta y porque la ruta de reserva se deshabilitó|
|d2c.telemetry.egress.orphaned|Mensajes huérfanos|Recuento|Total|El recuento de mensajes no coincide con ninguna ruta, incluida la ruta de reserva|
|d2c.telemetry.egress.invalid|Mensajes no válidos|Recuento|Total|El recuento de mensajes no entregados debido a incompatibilidad con el punto de conexión|
|d2c.telemetry.egress.fallback|Mensajes que coinciden con la condición de reserva|Recuento|Total|Número de mensajes escritos en el punto de conexión de reserva|
|d2c.endpoints.egress.eventHubs|Mensajes entregados a los puntos de conexión de Event Hub|Recuento|Total|Número de veces que los mensajes se han escrito correctamente en los puntos de conexión de Event Hub|
|d2c.endpoints.latency.eventHubs|Latencia de mensajes para los puntos de conexión del Centro de eventos|Milisegundos|Media|La latencia media entre la entrada de mensajes en el centro de IoT y la entrada de mensajes en un punto de conexión de Event Hubs, en milisegundos|
|d2c.endpoints.egress.serviceBusQueues|Mensajes entregados a los puntos de conexión de la cola de Service Bus|Recuento|Total|Número de veces que los mensajes se han escrito correctamente en los puntos de conexión de la cola de Service Bus|
|d2c.endpoints.latency.serviceBusQueues|Latencia de mensajes para los puntos de conexión de la cola de Service Bus|Milisegundos|Media|La latencia media entre la entrada de mensajes en el centro de IoT y la entrada de mensajes en un punto de conexión de la cola de Service Bus, en milisegundos|
|d2c.endpoints.egress.serviceBusTopics|Mensajes entregados a los puntos de conexión del tema de Service Bus|Recuento|Total|Número de veces que los mensajes se han escrito correctamente en los puntos de conexión del tema de Service Bus|
|d2c.endpoints.latency.serviceBusTopics|Latencia de mensajes para los puntos de conexión del tema de Service Bus|Milisegundos|Media|La latencia media entre la entrada de mensajes en el centro de IoT y la entrada de mensajes en un punto de conexión del tema de Service Bus, en milisegundos|
|d2c.endpoints.egress.builtIn.events|Mensajes entregados al punto de conexión integrado (mensajes y eventos)|Recuento|Total|Número de veces que los mensajes se han escrito correctamente en el punto de conexión integrado (mensajes y eventos)|
|d2c.endpoints.latency.builtIn.events|Latencia de mensajes para el punto de conexión integrado (mensajes y eventos)|Milisegundos|Media|La latencia media entre la entrada de mensajes en el centro de IoT y la entrada de mensajes en el punto de conexión integrado (mensajes/eventos), en milisegundos |
|d2c.twin.read.success|Lecturas gemelas correctas de los dispositivos|Recuento|Total|El recuento de todas las lecturas gemelas correctas iniciadas por el dispositivo.|
|d2c.twin.read.failure|Lecturas gemelas con error de los dispositivos|Recuento|Total|El recuento de todas las lecturas gemelas con error iniciadas por el dispositivo.|
|d2c.twin.read.size|Tamaño de la respuesta de las lecturas gemelas de dispositivos|Bytes|Media|El valor medio, mínimo y máximo de todas las lecturas gemelas correctas iniciadas por el dispositivo.|
|d2c.twin.update.success|Actualizaciones gemelas correctas de los dispositivos|Recuento|Total|El recuento de todas las actualizaciones gemelas correctas iniciadas por el dispositivo.|
|d2c.twin.update.failure|Actualizaciones gemelas con error de los dispositivos|Recuento|Total|El recuento de todas las actualizaciones gemelas con error iniciadas por el dispositivo.|
|d2c.twin.update.size|Tamaño de las actualizaciones gemelas de los dispositivos|Bytes|Media|El tamaño medio, mínimo y máximo de todas las actualizaciones gemelas correctas iniciadas por el dispositivo.|
|c2d.methods.success|Invocaciones correctas al método directo|Recuento|Total|El número de todas las llamadas correctas al método directo.|
|c2d.methods.failure|Invocaciones al método directo con error|Recuento|Total|El número de todas las llamadas al método directo con error.|
|c2d.methods.requestSize|Tamaño de la solicitud de las invocaciones a métodos directos|Bytes|Media|El valor medio, mínimo y máximo de todas las solicitudes correctas de método directo.|
|c2d.methods.responseSize|Tamaño de la respuesta de las invocaciones a métodos directos|Bytes|Media|El valor medio, mínimo y máximo de todas las respuestas correctas de método directo.|
|c2d.twin.read.success|Lecturas gemelas correctas del back-end|Recuento|Total|El recuento de todas las lecturas gemelas correctas iniciadas por el back-end.|
|c2d.twin.read.failure|Lecturas gemelas con error del back-end|Recuento|Total|El recuento de todas las lecturas gemelas con error iniciadas por el back-end.|
|c2d.twin.read.size|Tamaño de la respuesta de las lecturas gemelas del back-end|Bytes|Media|El valor medio, mínimo y máximo de todas las lecturas gemelas correctas iniciadas por el back-end.|
|c2d.twin.update.success|Actualizaciones gemelas correctas del back-end|Recuento|Total|El recuento de todas las actualizaciones gemelas correctas iniciadas por el back-end.|
|c2d.twin.update.failure|Actualizaciones gemelas con error del back-end|Recuento|Total|El recuento de todas las actualizaciones gemelas con error iniciadas por el back-end.|
|c2d.twin.update.size|Tamaño de las actualizaciones gemelas del back-end|Bytes|Media|El tamaño medio, mínimo y máximo de todas las actualizaciones gemelas correctas iniciadas por el back-end.|
|twinQueries.success|Consultas gemelas correctas|Recuento|Total|El recuento de todas las consultas gemelas correctas.|
|twinQueries.failure|Consultas gemelas con error|Recuento|Total|El recuento de todas las consultas gemelas con error.|
|twinQueries.resultSize|Tamaño de resultado de consultas gemelas|Bytes|Media|El valor medio, mínimo y máximo del tamaño del resultado de todas las consultas gemelas correctas.|
|jobs.createTwinUpdateJob.success|Creaciones correctas de trabajos de actualización gemelos|Recuento|Total|El recuento de todas las creaciones correctas de trabajos de actualización gemelos.|
|jobs.createTwinUpdateJob.failure|Creaciones con error de trabajos de actualización gemelos|Recuento|Total|El recuento de todas las creaciones con error de trabajos de actualización gemelos.|
|jobs.createDirectMethodJob.success|Creaciones correctas de trabajos de invocación de método|Recuento|Total|El recuento de todas las creaciones correctas de los trabajos de invocación de método directos.|
|jobs.createDirectMethodJob.failure|Creaciones con error de trabajos de invocación de método|Recuento|Total|El recuento de todas las creaciones con error de los trabajos de invocación de método directos.|
|jobs.listJobs.success|Llamadas correctas para enumerar trabajos|Recuento|Total|El recuento de todas las llamadas correctas para enumerar trabajos.|
|jobs.listJobs.failure|Llamadas con error para enumerar trabajos|Recuento|Total|El recuento de todas las llamadas con error para enumerar trabajos.|
|jobs.cancelJob.success|Cancelaciones de trabajos correctas|Recuento|Total|El recuento de todas las llamadas correctas para cancelar un trabajo.|
|jobs.cancelJob.failure|Cancelaciones de trabajos con error|Recuento|Total|El recuento de todas las llamadas con error para cancelar un trabajo.|
|jobs.queryJobs.success|Consultas de trabajo correctas|Recuento|Total|El recuento de todas las llamadas correctas para consultar trabajos.|
|jobs.queryJobs.failure|Consultas de trabajo con error|Recuento|Total|El recuento de todas las llamadas con error para consultar trabajos.|
|jobs.completed|Trabajos completados|Recuento|Total|El recuento de todos los trabajos completados.|
|jobs.failed|Trabajos con error|Recuento|Total|El recuento de todos los trabajos con error.|

## <a name="next-steps"></a>Pasos siguientes
Ahora que ha visto la información general sobre las métricas de IoT Hub, siga este vínculo para más información sobre la administración de IoT Hub de Azure:

* [Supervisión de operaciones][lnk-monitor]

Para explorar aún más las funcionalidades de IoT Hub, consulte:

* [Guía para desarrolladores de IoT Hub][lnk-devguide]
* [Simular un dispositivo con Azure IoT Edge][lnk-iotedge]

<!-- Links and images -->
[1]: media/iot-hub-metrics/enable-metrics-1.png
[2]: media/iot-hub-metrics/enable-metrics-2.png

[lnk-get-started]: iot-hub-csharp-csharp-getstarted.md
[lnk-operations-monitoring]: iot-hub-operations-monitoring.md
[lnk-scaling]: iot-hub-scaling.md
[lnk-dr]: iot-hub-ha-dr.md

[lnk-monitor]: iot-hub-operations-monitoring.md

[lnk-devguide]: iot-hub-devguide.md
[lnk-iotedge]: iot-hub-linux-iot-edge-simulated-device.md
