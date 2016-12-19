---
title: Escalado de Azure IoT Hub | Microsoft Docs
description: "Describe cómo escalar el Centro de IoT de Azure."
services: iot-hub
documentationcenter: 
author: fsautomata
manager: timlt
editor: 
ms.assetid: e7bd4968-db46-46cf-865d-9c944f683832
ms.service: iot-hub
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/19/2016
ms.author: elioda
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 992c271fe3426f4a1e59f4e546b0f7f4e0d59e59


---
# <a name="scaling-iot-hub"></a>Escalado del Centro de IoT
El Centro de IoT de Azure puede admitir hasta un millón de dispositivos conectados simultáneamente. Para más información, vea [Precios de IoT Hub][lnk-pricing]. Cada unidad de IoT Hub permite un número determinado de mensajes diarios.

Para escalar correctamente su solución, debe tener en cuenta el uso particular que haga de IoT Hub. En concreto, tenga en cuenta la capacidad de procesamiento máxima requerida para las siguientes categorías de operaciones:

* Mensajes de dispositivo a nube
* Mensajes de nube a dispositivo
* Operaciones de registro de identidad

Además de esta información sobre la capacidad de procesamiento, consulte [Cuotas y límites de IoT Hub][Cuotas y límites de IoT Hub] y diseñe su solución en consecuencia.

## <a name="device-to-cloud-and-cloud-to-device-message-throughput"></a>Capacidad de procesamiento para los mensajes de dispositivo a nube y de nube a dispositivo
La mejor forma de dimensionar una solución de Centro de IoT es evaluar el tráfico en cada dispositivo.

Los mensajes de dispositivo a nube siguen estas directrices de capacidad de procesamiento sostenida.

| Nivel: | Capacidad de procesamiento sostenida | Velocidad de envío sostenida |
| --- | --- | --- |
| S1 |Hasta 1111 KB/minuto por unidad<br/>(1,5 GB/día/unidad) |Promedio de 278 mensajes/minuto por unidad<br/>(400 000 mensajes/día por unidad) |
| S2 |Hasta 16 MB/minuto por unidad<br/>(22,8 GB/día/unidad) |Promedio de 4167 mensajes/minuto por unidad<br/>(6 millones de mensajes/día por unidad) |
| S3 |Hasta 814 MB/minuto por unidad<br/>(1144,4 GB/día/unidad) |Promedio de 208.333 mensajes/minuto por unidad<br/>(300 millones de mensajes/día por unidad) |

## <a name="identity-registry-operation-throughput"></a>Capacidad de procesamiento para las operaciones de registro de identidad
Las operaciones de registro de identidad del Centro de IoT no deberían ser operaciones en tiempo de ejecución porque tienen que ver principalmente con el aprovisionamiento de dispositivos.

Vea las cifras de rendimiento de ráfaga específicas en [Cuotas y limitaciones del Centro de IoT][Cuotas y límites de IoT Hub].

## <a name="sharding"></a>Clave de particionamiento
Aunque un único Centro de IoT puede escalarse a millones de dispositivos, a veces la solución requiere características de rendimiento específicas que un único Centro de IoT no puede garantizar. En ese caso, se recomienda realizar particiones de los dispositivos en varios centros de IoT. Varios centros de IoT suavizan las ráfagas de tráfico y obtienen el rendimiento necesario o las velocidades de funcionamiento necesarias.

## <a name="next-steps"></a>Pasos siguientes
Para explorar aún más las funcionalidades de Centro de IoT, consulte:

* [Guía para desarrolladores de][lnk-devguide]
* [Simulación de un dispositivo con el SDK de puerta de enlace de IoT][lnk-gateway]

[lnk-pricing]: https://azure.microsoft.com/pricing/details/iot-hub
[Cuotas y límites de IoT Hub]: iot-hub-devguide-quotas-throttling.md

[lnk-devguide]: iot-hub-devguide.md
[lnk-gateway]: iot-hub-linux-gateway-sdk-simulated-device.md



<!--HONumber=Nov16_HO3-->


