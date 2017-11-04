---
title: "Guía del desarrollador de IoT Hub de Azure | Microsoft Docs"
description: "La Guía del desarrollador de IoT Hub de Azure incluye explicaciones sobre los puntos de conexión, la seguridad, el registro de identidad, la administración de dispositivos, los métodos directos, los dispositivos gemelos, las cargas de archivos, los trabajos, el lenguaje de consultas de IoT Hub y la mensajería."
services: iot-hub
documentationcenter: .net
author: dominicbetts
manager: timlt
editor: 
ms.assetid: d534ff9d-2de5-4995-bb2d-84a02693cb2e
ms.service: iot-hub
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/13/2017
ms.author: dobett
ms.openlocfilehash: 27b296092335ec5b95e8f259756aaf9572da1c16
ms.sourcegitcommit: e6029b2994fa5ba82d0ac72b264879c3484e3dd0
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/24/2017
---
# <a name="azure-iot-hub-developer-guide"></a>Guía del desarrollador del Centro de IoT de Azure

IoT Hub de Azure es un servicio totalmente administrado que permite la comunicación bidireccional confiable y segura entre millones de dispositivos y una solución de back-end.

IoT Hub de Azure le proporciona:

* Comunicaciones seguras a través del uso de credenciales de seguridad para cada dispositivo y el control de acceso.
* Varias opciones de comunicación a gran escala de dispositivo a la nube y de la nube a dispositivo.
* Almacenamiento consultable de la información de estado y los metadatos de cada dispositivo.
* Conectividad fácil del dispositivo con las bibliotecas de dispositivos para las plataformas y lenguajes más populares.

Esta guía del desarrollador de IoT Hub incluye los siguientes artículos:

* [Device-to-cloud communication guidance][lnk-d2c-guidance] (Guía de comunicación de dispositivo a nube) le permite elegir entre los mensajes del dispositivo a la nube, las propiedades notificadas del dispositivo gemelo y la carga de archivos.
* [Guía de comunicación de nube a dispositivo][lnk-c2d-guidance] le ayuda a elegir entre métodos directos, las propiedades preferidas del dispositivo gemelo y los mensajes de la nube al dispositivo.
* [Mensajería de dispositivo a nube y de nube a dispositivo con IoT Hub][devguide-messaging] describe las características de mensajería (de dispositivo a nube y de nube a dispositivo) que expone IoT Hub.
  * [Envío de mensajes de dispositivo a nube a IoT Hub][devguide-messages-d2c].
  * [Lectura de mensajes de dispositivo a nube desde el punto de conexión integrado][devguide-builtin].
  * [Uso de puntos de conexión y reglas de enrutamiento personalizados para mensajes de dispositivos a la nube][devguide-custom].
  * [Envío de mensajes de nube a dispositivo desde IoT Hub][devguide-messages-c2d].
  * [Creación y lectura de mensajes de IoT Hub][devguide-format].
* En [Carga de archivos desde un dispositivo][devguide-upload] se describe cómo se cargan archivos desde un dispositivo. El artículo también incluye información acerca de temas como las notificaciones que el proceso de carga puede enviar.
* En [Administrar identidades del dispositivo en IoT Hub][devguide-identities] se describe qué información almacena el registro de identidad de cada centro de IoT y cómo se accede a él y se modifica.
* En [Control de acceso a IoT Hub][devguide-security] se describe el modelo de seguridad que se usa para conceder acceso a la funcionalidad de IoT Hub tanto para los dispositivos como para los componentes de la nube. El artículo incluye información acerca del uso de tokens y certificados X.509, y los detalles de los permisos que puede conceder.
* En [Uso de dispositivos gemelos para sincronizar el estado y las configuraciones][devguide-device-twins] se describe el concepto de *dispositivo gemelo* y la funcionalidad que expone, como la sincronización de un dispositivo con su gemelo. El artículo incluye información acerca de los datos almacenados en un dispositivo gemelo.
* En [Invocación de un método directo en un dispositivo][devguide-directmethods] se describe el ciclo de vida de un método directo y proporciona información sobre cómo invocar métodos en un dispositivo desde la aplicación back-end y controlar el método directo en el dispositivo.
* En [Programación de trabajos en varios dispositivos][devguide-jobs] se describe cómo se programan trabajos en varios dispositivos. En el artículo se describe cómo enviar trabajos que realizan tareas como la ejecución de un método directo y la actualización de un dispositivo mediante un dispositivo gemelo. También describe cómo consultar el estado de un trabajo.
* [Referencia: elegir un protocolo de comunicación][devguide-protocol] describe los protocolos de comunicación que IoT Hub admite para la comunicación de dispositivos y muestra los puertos que deben estar abiertos.
* En la [reference de puntos de conexión de IoT Hub][devguide-endpoints] se describen los diferentes puntos de conexión que expone cada centro de IoT para las operaciones en tiempo de ejecución y de administración. En el artículo también se describe cómo puede crear puntos de conexión adicionales en su IoT Hub y cómo usar una puerta de enlace de campo para habilitar la conectividad con los puntos de conexión de su IoT Hub en escenarios no estándar.
* [Referencia: lenguaje de consulta para dispositivos gemelos, trabajos y enrutamiento de mensajes][devguide-query] describe el lenguaje de consulta de IoT Hub que le permite recuperar información desde su instancia de IoT Hub sobre los dispositivos gemelos y los trabajos.
* En [Referencia: cuotas y limitaciones][devguide-quotas] se resumen las cuotas establecidas en el servicio de IoT Hub y la limitación que se produce cuando supera una cuota.
* [Referencia: precios][devguide-pricing] proporciona información general sobre diferentes SKU y precios para IoT Hub y detalles sobre cómo las diversas funcionalidades se miden como mensajes en IoT Hub.
* En [Referencia: SDK de servicio y de dispositivo][devguide-sdks] se muestran los diversos SDK de Azure IoT para desarrollar aplicaciones de dispositivo y de servicio que interactúen con su IoT Hub. El artículo incluye vínculos a documentación de la API en línea.
* En [Referencia: compatibilidad con MQTT de IoT Hub][devguide-mqtt] se proporciona más información acerca de la compatibilidad de IoT Hub con el protocolo MQTT. En el artículo se describe la compatibilidad con el protocolo MQTT integrado en los SDK de IoT de Azure y se proporciona información acerca de cómo utilizar el protocolo MQTT directamente.
* [Glosario][devguide-glossary], una lista de términos habituales relacionados con IoT Hub.

[devguide-messaging]: iot-hub-devguide-messaging.md
[devguide-upload]: iot-hub-devguide-file-upload.md
[devguide-identities]: iot-hub-devguide-identity-registry.md
[devguide-security]: iot-hub-devguide-security.md
[devguide-device-twins]: iot-hub-devguide-device-twins.md
[devguide-directmethods]: iot-hub-devguide-direct-methods.md
[devguide-jobs]: iot-hub-devguide-jobs.md
[devguide-endpoints]: iot-hub-devguide-endpoints.md
[devguide-quotas]: iot-hub-devguide-quotas-throttling.md
[devguide-query]: iot-hub-devguide-query-language.md
[devguide-sdks]: iot-hub-devguide-sdks.md
[devguide-mqtt]: iot-hub-mqtt-support.md
[devguide-glossary]: iot-hub-devguide-glossary.md
[devguide-pricing]: iot-hub-devguide-pricing.md
[lnk-c2d-guidance]: iot-hub-devguide-c2d-guidance.md
[lnk-d2c-guidance]: iot-hub-devguide-d2c-guidance.md
[devguide-messages-d2c]: iot-hub-devguide-messages-d2c.md
[devguide-builtin]: iot-hub-devguide-messages-read-builtin.md
[devguide-custom]: iot-hub-devguide-messages-read-custom.md
[devguide-messages-c2d]: iot-hub-devguide-messages-c2d.md
[devguide-format]: iot-hub-devguide-messages-construct.md
[devguide-protocol]: iot-hub-devguide-protocols.md
