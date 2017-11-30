---
title: Escalado de Azure IoT Hub | Microsoft Docs
description: "Cómo escalar el centro de IoT Hub para respaldar el rendimiento de los mensajes previsto. Incluye un resumen del rendimiento admitido para cada nivel y opciones de particionamiento."
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
ms.date: 10/13/2017
ms.author: elioda
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 00043293eb57768f0117e912bb67f02d088934f3
ms.sourcegitcommit: 933af6219266cc685d0c9009f533ca1be03aa5e9
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/18/2017
---
# <a name="scale-your-iot-hub-solution"></a>Escalado de la solución de IoT Hub
Azure IoT Hub puede admitir hasta un millón de dispositivos conectados simultáneamente. Para obtener más información, vea [Precios de IoT Hub][lnk-pricing]. Cada unidad de IoT Hub permite un número determinado de mensajes diarios.

Para escalar correctamente su solución, debe tener en cuenta el uso particular que haga de IoT Hub. En concreto, tenga en cuenta la capacidad de procesamiento máxima requerida para las siguientes categorías de operaciones:

* Mensajes de dispositivo a nube
* Mensajes de nube a dispositivo
* Operaciones de registro de identidad

Además de esta información sobre la capacidad de procesamiento, vea [Cuotas y limitaciones de IoT Hub][IoT Hub quotas and throttles] y diseñe su solución en consecuencia.

## <a name="device-to-cloud-and-cloud-to-device-message-throughput"></a>Capacidad de procesamiento para los mensajes de dispositivo a nube y de nube a dispositivo
La mejor forma de dimensionar una solución de IoT Hub es evaluar el tráfico en cada dispositivo.

Los mensajes de dispositivo a nube siguen estas directrices de capacidad de procesamiento sostenida.

| Nivel: | Capacidad de procesamiento sostenida | Velocidad de envío sostenida |
| --- | --- | --- |
| S1 |Hasta 1111 KB/minuto por unidad<br/>(1,5 GB/día/unidad) |Promedio de 278 mensajes/minuto por unidad<br/>(400 000 mensajes/día por unidad) |
| S2 |Hasta 16 MB/minuto por unidad<br/>(22,8 GB/día/unidad) |Promedio de 4167 mensajes/minuto por unidad<br/>(6 millones de mensajes/día por unidad) |
| S3 |Hasta 814 MB/minuto por unidad<br/>(1144,4 GB/día/unidad) |Promedio de 208.333 mensajes/minuto por unidad<br/>(300 millones de mensajes/día por unidad) |

## <a name="identity-registry-operation-throughput"></a>Capacidad de procesamiento para las operaciones de registro de identidad
Las operaciones de registro de identidad de IoT Hub no deberían ser operaciones en tiempo de ejecución porque tienen que ver principalmente con el aprovisionamiento de dispositivos.

Vea las cifras de rendimiento de ráfaga específicas en [Cuotas y limitaciones de IoT Hub][IoT Hub quotas and throttles].

## <a name="sharding"></a>Clave de particionamiento
Aunque un único Centro de IoT puede escalarse a millones de dispositivos, a veces la solución requiere características de rendimiento específicas que un único Centro de IoT no puede garantizar. En ese caso, se recomienda realizar particiones de los dispositivos en varios centros de IoT. Varios centros de IoT suavizan las ráfagas de tráfico y obtienen el rendimiento necesario o las velocidades de funcionamiento necesarias.

## <a name="next-steps"></a>Pasos siguientes
Para explorar aún más las funcionalidades de IoT Hub, consulte:

* [Guía para desarrolladores de IoT Hub][lnk-devguide]
* [Implementación de AI en dispositivos perimetrales con Azure IoT Edge][lnk-iotedge]

[lnk-pricing]: https://azure.microsoft.com/pricing/details/iot-hub
[IoT Hub quotas and throttles]: iot-hub-devguide-quotas-throttling.md

[lnk-devguide]: iot-hub-devguide.md
[lnk-iotedge]: ../iot-edge/tutorial-simulate-device-linux.md
