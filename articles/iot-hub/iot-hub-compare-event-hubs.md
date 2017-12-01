---
title: "Comparación de Azure IoT Hub con Azure Event Hubs | Microsoft Docs"
description: "Comparación de los servicios de IoT Hub y los servicios de Event Hubs de Azure en la que se resaltan diferencias funcionales y casos de uso. La comparación incluye protocolos admitidos, administración de dispositivos, supervisión y cargas de archivos."
services: iot-hub
documentationcenter: 
author: fsautomata
manager: timlt
editor: 
ms.assetid: aeddea62-8302-48e2-9aad-c5a0e5f5abe9
ms.service: iot-hub
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/24/2017
ms.author: elioda
ms.openlocfilehash: b515e05d16dda83c7d865113d5d3578c44be084f
ms.sourcegitcommit: 933af6219266cc685d0c9009f533ca1be03aa5e9
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/18/2017
---
# <a name="comparison-of-azure-iot-hub-and-azure-event-hubs"></a>Comparación entre IoT Hub de Azure y Azure Event Hubs
Uno de los principales usos de IoT Hub es recopilar telemetría de dispositivos. Por este motivo, IoT Hub a menudo se compara con [Azure Event Hubs][Azure Event Hubs]. Al igual que IoT Hub, Event Hubs es un servicio de procesamiento de eventos que ofrece entrada de telemetría y eventos en la nube a escala masiva, con una latencia baja y una confiabilidad alta.

Sin embargo, los servicios tienen muchas diferencias que se detallan en la tabla siguiente:

| Ámbito | IoT Hub | Event Hubs |
| --- | --- | --- |
| Patrones de comunicación | Permite las [comunicaciones de dispositivo a nube] [lnk-d2c-guidance] (mensajería, cargas de archivos y propiedades notificadas) y [comunicaciones de nube a dispositivo] [lnk-c2d-guidance] (métodos directos, propiedades deseadas, mensajería). |Solo se habilita la entrada de eventos (normalmente se consideran escenarios dispositivo a nube). |
| Información de estado de dispositivo | Los [dispositivos gemelos][lnk-twins] pueden almacenar y consultar la información de estado del dispositivo. | Sin embargo, esta información no se puede almacenar. |
| Compatibilidad con protocolos de dispositivo |Se admite MQTT, MQTT sobre WebSockets, AMQP, AMQP sobre WebSockets y HTTP. Además, IoT Hub funciona con la [puerta de enlace de protocolos de IoT de Azure][lnk-azure-protocol-gateway], una implementación de puerta de enlace de protocolos personalizable para admitir protocolos personalizados. |Admite AMQP, AMQP sobre WebSockets y HTTP. |
| Seguridad |Ofrece identidad por dispositivo y control de acceso revocable. Vea la [sección de seguridad de la Guía del desarrollador de IoT Hub]. |Ofrece [directivas de acceso compartido][Event Hubs - security] en todo Event Hubs, con compatibilidad limitada para revocación mediante [directivas del publicador][Event Hubs publisher policies]. A menudo se requiere de las soluciones de IoT que implementen una solución personalizada para admitir las credenciales por dispositivo y las medidas contra la suplantación de identidad. |
| Supervisión de operaciones |Permite a las soluciones de IoT suscribirse a un amplio conjunto de eventos de conectividad y administración de identidad del dispositivo, como errores de autenticación de dispositivos individuales, establecimientos de limitaciones y excepciones de formato incorrecto. Estos eventos permiten identificar rápidamente problemas de conectividad en los dispositivos individuales. |Muestra solo las métricas agregadas. |
| Escala |Está optimizado para admitir millones de dispositivos conectados al mismo tiempo. |Mide las conexiones según las [cuotas de Azure Event Hubs][Azure Event Hubs quotas]. Por otro lado, Event Hubs permite especificar la partición para cada mensaje enviado. |
| SDK de dispositivo |Proporciona [SDK de dispositivo][Azure IoT SDKs] para una gran variedad de plataformas y lenguajes, además de API directas de MQTT, AMQP y HTTP. |Se admite en .NET, Java y C, además de en las interfaces de envío AMQP y HTTP. |
| Carga de archivos |Permite que las soluciones IoT carguen en la nube archivos de los dispositivos. Incluye un punto de conexión de notificación de archivos para la integración del flujo de trabajo y una categoría de supervisión de operaciones para la compatibilidad con la depuración. | No compatible. |
| Enrutamiento de mensajes a varios puntos de conexión | Se admite un máximo de 10 puntos de conexión personalizados. Las reglas determinan cómo se enrutan los mensajes a puntos de conexión personalizados. Para más información, consulte [Envío y recepción de mensajes con IoT Hub][lnk-devguide-messaging]. | Requiere escribir y hospedar código adicional para el envío de mensajes. |

En resumen, aunque el único uso sea la entrada de telemetría de dispositivo a nube, IoT Hub ofrece un servicio que está diseñado para la conectividad de dispositivos IoT. Continuará expandiendo las propuestas de valor para estos escenarios con características específicas de IoT. Event Hubs está diseñado para la entrada de eventos a gran escala, tanto en escenarios de conexión entre centros de datos como dentro del propio centro de datos.

No es raro usar IoT Hub y Event Hubs en la misma solución. IoT Hub controla la comunicación del dispositivo a la nube, y Event Hubs controla la entrada de eventos de última fase en motores de procesamiento en tiempo real.

### <a name="next-steps"></a>Pasos siguientes
Para más información sobre el planeamiento de la implementación de IoT Hub, consulte [Escalado, alta disponibilidad y recuperación ante desastres][lnk-scaling].

Para explorar aún más las funcionalidades de IoT Hub, consulte:

* [Guía para desarrolladores de IoT Hub][lnk-devguide]
* [Implementación de Azure IoT Edge en un dispositivo simulado en Linux: versión preliminar][lnk-iotedge]

[lnk-twins]: iot-hub-devguide-device-twins.md
[lnk-c2d-guidance]: iot-hub-devguide-c2d-guidance.md
[lnk-d2c-guidance]: iot-hub-devguide-d2c-guidance.md

[Azure Event Hubs]: ../event-hubs/event-hubs-what-is-event-hubs.md
[sección de seguridad de la Guía del desarrollador de IoT Hub]: iot-hub-devguide-security.md
[Event Hubs - security]: ../event-hubs/event-hubs-authentication-and-security-model-overview.md
[Event Hubs publisher policies]: ../event-hubs/event-hubs-features.md#event-publishers
[Azure Event Hubs quotas]: ../event-hubs/event-hubs-quotas.md
[Azure IoT SDKs]: https://github.com/Azure/azure-iot-sdks
[lnk-azure-protocol-gateway]: iot-hub-protocol-gateway.md

[lnk-scaling]: iot-hub-scaling.md
[lnk-devguide]: iot-hub-devguide.md
[lnk-iotedge]: ../iot-edge/tutorial-simulate-device-linux.md
[lnk-devguide-messaging]: iot-hub-devguide-messaging.md
