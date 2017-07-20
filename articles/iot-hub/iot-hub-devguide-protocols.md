---
title: "Puertos y protocolos de comunicación de Azure IoT Hub | Microsoft Docs"
description: "Guía del desarrollador: describe los protocolos de comunicación compatibles para las comunicaciones de dispositivo a nube y de nube a dispositivo, además de los números de puerto que deben estar abiertos."
services: iot-hub
documentationcenter: .net
author: dominicbetts
manager: timlt
editor: 
ms.assetid: 3fc5f1a3-3711-4611-9897-d4db079b4250
ms.service: iot-hub
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/25/2017
ms.author: dobett
ms.translationtype: Human Translation
ms.sourcegitcommit: 5edc47e03ca9319ba2e3285600703d759963e1f3
ms.openlocfilehash: 98004a48734e33f85eebf8f6213d9f0751dea843
ms.contentlocale: es-es
ms.lasthandoff: 05/31/2017


---
# <a name="reference---choose-a-communication-protocol"></a>Referencia: elección de un protocolo de comunicación

IoT Hub permite a los dispositivos usar protocolos [MQTT][lnk-mqtt], MQTT sobre WebSockets, [AMQP][lnk-amqp], AMQP sobre WebSockets y HTTP para comunicaciones del lado del dispositivo. Para información sobre cómo estos protocolos admiten características específicas de IoT Hub, consulte [Guía de comunicación de dispositivo a nube][lnk-d2c-guidance] y [Guía de comunicación de nube a dispositivo][lnk-c2d-guidance].

La tabla siguiente proporciona recomendaciones generales para la elección del protocolo:

| Protocol | Cuándo elegir este protocolo |
| --- | --- |
| MQTT <br> MQTT sobre WebSocket |Utilice todos los dispositivos que no requieren conexión a varios dispositivos (cada uno con sus propias credenciales por servicio) sobre la misma conexión TLS. |
| AMQP <br> AMQP sobre WebSocket |Usar en puertas de enlace de campo y en la nube para aprovechar las ventajas de la multiplexación de la conexión entre dispositivos. |
| HTTP |Usar con dispositivos que no admiten otros protocolos. |

Considere los siguientes aspectos a la hora de elegir el protocolo para las comunicaciones del dispositivo:

* **Patrón de nube a dispositivo**. HTTP no cuenta con una forma eficaz de implementar la inserción de servidor. Por lo tanto, cuando se usa HTTP, los dispositivos sondean los mensajes de nube a dispositivo en IoT Hub. Este enfoque es ineficaz tanto para el dispositivo como para IoT Hub. Según las directrices actuales de HTTP, cada dispositivo sondeará si hay mensajes cada 25 minutos o más. Por otro lado, AMQP y MQTT admiten la inserción de servidor cuando se reciben mensajes de nube a dispositivo. Permiten inserciones inmediatas de mensajes desde Centro de IoT en el dispositivo. Si le preocupa la latencia de entrega, es mucho mejor usar los protocolos MQTT o AMQP. Para dispositivos conectados en raras ocasiones, HTTP funciona bien.
* **Puertas de enlace de campo**. Cuando se utiliza MQTT y HTTP, no puede conectar varios dispositivos (cada uno con sus propias credenciales por dispositivo) con la misma conexión TLS. Por lo tanto, en los [escenarios de puerta de enlace de campo][lnk-azure-gateway-guidance], estos protocolos no son óptimos, ya que requieren una conexión TLS entre la puerta de enlace e IoT Hub para cada dispositivo conectado a ella.
* **Dispositivos con bajos recursos**. Las bibliotecas de MQTT y HTTP tienen una huella menor que las bibliotecas de AMQP. Por ello, si el dispositivo tiene recursos limitados (por ejemplo, menos de 1 MB de RAM), estos protocolos podrían ser la única opción disponible.
* **Cruce seguro de red**. El protocolo AMQP estándar usa el puerto 5671, mientras que el MQTT realiza la escucha en el puerto 8883, lo que podría producir problemas en las redes cerradas para los protocolos que no sean HTTP. MQTT sobre WebSockets, AMQP sobre WebSockets y HTTP están disponibles para usarse en este escenario.
* **Tamaño de carga**. MQTT y AMQP son protocolos binarios que producen cargas más compactas que HTTP.

> [!WARNING]
> Cuando se usa HTTP, cada dispositivo sondeará si hay mensajes de la nube a dispositivo cada 25 minutos o más. Sin embargo, durante el desarrollo, es aceptable sondear con una frecuencia mayor de 25 minutos.

## <a name="port-numbers"></a>Números de puerto

Los dispositivos pueden comunicarse con el Centro de IoT en Azure mediante diversos protocolos. Normalmente, la elección del protocolo se basa en los requisitos específicos de la solución. En la tabla siguiente se indican los puertos de salida que deben estar abiertos para que un dispositivo pueda usar un protocolo concreto:

| Protocol | Port |
| --- | --- |
| MQTT |8883 |
| MQTT sobre WebSockets |443 |
| AMQP |5671 |
| AMQP sobre WebSockets |443 |
| HTTP |443 |

Una vez creado un centro de IoT en una región de Azure, el centro de IoT mantiene la misma dirección IP durante toda su existencia. Sin embargo, para mantener la calidad de servicio, si Microsoft mueve el Centro de IoT a una unidad de escalado diferente, se le asigna una nueva dirección IP.


## <a name="next-steps"></a>Pasos siguientes

Para más información sobre cómo IoT Hub implementa el protocolo MQTT, consulte [Comunicación con la instancia de IoT Hub mediante el protocolo MQTT][lnk-mqtt-support].

[lnk-d2c-guidance]: iot-hub-devguide-d2c-guidance.md
[lnk-c2d-guidance]: iot-hub-devguide-c2d-guidance.md
[lnk-mqtt-support]: iot-hub-mqtt-support.md
[lnk-amqp]: http://docs.oasis-open.org/amqp/core/v1.0/os/amqp-core-complete-v1.0-os.pdf
[lnk-mqtt]: http://docs.oasis-open.org/mqtt/mqtt/v3.1.1/mqtt-v3.1.1.pdf
[lnk-azure-gateway-guidance]: iot-hub-devguide-endpoints.md#field-gateways

