---
title: Comparación de Azure IoT Hub con Azure Event Hubs | Microsoft Docs
description: Comparación de los servicios de IoT Hub y los servicios de Event Hubs de Azure en la que se resaltan diferencias funcionales y casos de uso. La comparación incluye protocolos admitidos, administración de dispositivos, supervisión y cargas de archivos.
services: iot-hub
documentationcenter: ''
author: kgremban
manager: timlt
editor: ''
ms.assetid: aeddea62-8302-48e2-9aad-c5a0e5f5abe9
ms.service: iot-hub
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/01/2018
ms.author: kgremban
ms.openlocfilehash: 303a2bde0a1e0b25ca6eb145e7b0cd6c91fff351
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 04/03/2018
---
# <a name="comparison-of-azure-iot-hub-and-azure-event-hubs"></a>Comparación entre IoT Hub de Azure y Azure Event Hubs

Tanto Azure IoT Hub como Azure Event Hubs son servicios en la nube que pueden ingerir grandes cantidades de datos y procesar o almacenar esos datos para obtener perspectivas empresariales. Los dos servicios se parecen en que ambos admiten el procesamiento de datos de telemetría y eventos con baja latencia y alta fiabilidad. Sin embargo, solo IoT Hub se desarrolló con las funcionalidades específicas que se necesitan para admitir escenarios a escala de Internet de las cosas. 

Azure IoT Hub es la puerta de enlace en la nube que conecta dispositivos y recopila datos para la información empresarial y la automatización. Facilita la transmisión de datos a la nube y la administración de los dispositivos a escala. Una diferencia importante entre IoT Hub y otros servicios de ingesta de datos es que IoT Hub incluye características que enriquecen la relación entre los dispositivos y los sistemas de back-end. Las capacidades de comunicación bidireccional implican que al tiempo que se reciben datos de los dispositivos, también es posible devolver mensajes a los dispositivos para actualizar las propiedades o invocar una acción. La identidad a nivel de dispositivo ayuda a proteger el sistema. La computación distribuida transfiere la lógica del servicio en la nube a los dispositivos perimetrales.

[Azure Event Hubs][Azure Event Hubs] es un servicio de ingesta de eventos que puede procesar y almacenar grandes cantidades de datos y telemetría. Event Hubs está diseñado para la ingesta de eventos a escala masiva, tanto en el contexto de escenarios entre centros de datos como en el seno de estos, pero no ofrece las eficaces capacidades específicas de IoT que están disponibles con IoT Hub. Por esta razón, no se recomienda Event Hubs para las soluciones de IoT. 

En la tabla siguiente se proporcionan detalles de la comparación entre dos niveles de IoT Hub y Event Hubs al valorarlas en función de su capacidad de IoT. Para obtener más información acerca de los niveles básico y estándar de IoT Hub, consulte [Escalado de la solución de IoT Hub][lnk-scaling].

| Funcionalidad de IoT | Nivel estándar de IoT Hub | Nivel básico de IoT Hub | Event Hubs |
| --- | --- | --- | --- |
| Mensajería de un dispositivo a la nube | ![Comprobar][1] | ![Comprobar][1] | ![Comprobar][1] |
| Protocolos: HTTPS, AMQP, AMQP sobre WebSockets | ![Comprobar][1] | ![Comprobar][1] | ![Comprobar][1] |
| Protocolos: MQTT, MQTT sobre WebSockets | ![Comprobar][1] | ![Comprobar][1] |  |
| Identidad por dispositivo | ![Comprobar][1] | ![Comprobar][1] |  |
| Carga de archivos desde dispositivos | ![Comprobar][1] | ![Comprobar][1] |  |
| Servicio Device Provisioning | ![Comprobar][1] | ![Comprobar][1] |  |
| Mensajería de la nube a un dispositivo | ![Comprobar][1] |  |  |
| Administración de dispositivos y dispositivos gemelos | ![Comprobar][1] |  |  |
| IoT Edge | ![Comprobar][1] |  |  |

Aunque el único uso sea la ingesta de datos de dispositivo a nube, es muy recomendable utilizar IoT Hub porque ofrece un servicio que está diseñado para la conectividad de dispositivos IoT. 

### <a name="next-steps"></a>Pasos siguientes

Para explorar aún más las funcionalidades de IoT Hub, consulte la [Guía del desarrollador de Azure IoT Hub][lnk-devguide].


[Azure Event Hubs]: ../event-hubs/event-hubs-what-is-event-hubs.md
[lnk-scaling]: iot-hub-scaling.md
[lnk-devguide]: iot-hub-devguide.md

<!--Image references-->
[1]: ./media/iot-hub-compare-event-hubs/ic195031.png