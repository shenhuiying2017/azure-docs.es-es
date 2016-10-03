<properties
 pageTitle="Escalado del Centro de IoT de Azure | Microsoft Azure"
 description="Describe cómo escalar el Centro de IoT de Azure."
 services="iot-hub"
 documentationCenter=""
 authors="fsautomata"
 manager="timlt"
 editor=""/>

<tags
 ms.service="iot-hub"
 ms.devlang="na"
 ms.topic="article"
 ms.tgt_pltfrm="na"
 ms.workload="na"
 ms.date="09/19/2016"
 ms.author="elioda"/>

# Escalado del Centro de IoT

El Centro de IoT de Azure puede admitir hasta un millón de dispositivos conectados simultáneamente. Para más información, vea [Precios del Centro de IoT][lnk-pricing]. Cada unidad de IoT Hub permite un número determinado de mensajes diarios.

Para escalar correctamente su solución, debe tener en cuenta el uso particular que haga de IoT Hub. En concreto, tenga en cuenta la capacidad de procesamiento máxima requerida para las siguientes categorías de operaciones:

* Mensajes de dispositivo a nube
* Mensajes de nube a dispositivo
* Operaciones de registro de identidad

Además de esta información sobre la capacidad de procesamiento, vea [Cuotas y limitaciones del Centro de IoT][] y diseñe su solución en consecuencia.

## Capacidad de procesamiento para los mensajes de dispositivo a nube y de nube a dispositivo

La mejor forma de dimensionar una solución de Centro de IoT es evaluar el tráfico en cada dispositivo.

Los mensajes de dispositivo a nube siguen estas directrices de capacidad de procesamiento sostenida.

| Nivel: | Capacidad de procesamiento sostenida | Velocidad de envío sostenida |
| ---- | -------------------- | ------------------- |
| S1 | Hasta 1111 KB/minuto por unidad<br/>(1,5 GB/día/unidad) | Promedio de 278 mensajes/minuto por unidad<br/>(400 000 mensajes/día por unidad) |
| S2 | Hasta 16 MB/minuto por unidad<br/>(22,8 GB/día/unidad) | Promedio de 4167 mensajes/minuto por unidad<br/>(6 millones de mensajes/día por unidad) |
| S3 | Hasta 814 MB/minuto por unidad<br/>(1144,4 GB/día/unidad) | Promedio de 208.333 mensajes/minuto por unidad<br/>(300 millones de mensajes/día por unidad) |

## Capacidad de procesamiento para las operaciones de registro de identidad

Las operaciones de registro de identidad del Centro de IoT no deberían ser operaciones en tiempo de ejecución porque tienen que ver principalmente con el aprovisionamiento de dispositivos.

Vea las cifras de rendimiento de ráfaga específicas en [Cuotas y limitaciones del Centro de IoT][].

## Clave de particionamiento

Aunque un único Centro de IoT puede escalarse a millones de dispositivos, a veces la solución requiere características de rendimiento específicas que un único Centro de IoT no puede garantizar. En ese caso, se recomienda realizar particiones de los dispositivos en varios centros de IoT. Varios centros de IoT suavizan las ráfagas de tráfico y obtienen el rendimiento necesario o las velocidades de funcionamiento necesarias.

## Pasos siguientes

Para explorar aún más las funcionalidades de Centro de IoT, consulte:

- [Guía del desarrollador][lnk-devguide]
- [Exploración de la administración de dispositivos desde Centro de IoT de Azure con la IU de ejemplo][lnk-dmui]
- [SDK de puerta de enlace de IoT (beta): envío de mensajes del dispositivo a la nube con un dispositivo simulado usando Linux][lnk-gateway]
- [Administración de Centros de IoT a través del portal de Azure][lnk-portal]

[lnk-pricing]: https://azure.microsoft.com/pricing/details/iot-hub
[Cuotas y limitaciones del Centro de IoT]: iot-hub-devguide.md#throttling

[lnk-design]: iot-hub-guidance.md
[lnk-mqtt]: iot-hub-mqtt-support.md
[lnk-devices]: iot-hub-tested-configurations.md
[lnk-protocols]: iot-hub-protocol-gateway.md
[lnk-compare]: iot-hub-compare-event-hubs.md
[lnk-scaling]: iot-hub-scaling.md
[lnk-devguide]: iot-hub-devguide.md
[lnk-dmui]: iot-hub-device-management-ui-sample.md
[lnk-gateway]: iot-hub-linux-gateway-sdk-simulated-device.md
[lnk-portal]: iot-hub-manage-through-portal.md

<!---HONumber=AcomDC_0921_2016-->