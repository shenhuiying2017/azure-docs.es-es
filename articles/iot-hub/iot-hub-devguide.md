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
ms.date: 05/02/2017
ms.author: dobett
translationtype: Human Translation
ms.sourcegitcommit: 1915044f252984f6d68498837e13c817242542cf
ms.openlocfilehash: 4f12a4b3ba9c1d0b7ed10cf5d766bcea60205d24
ms.lasthandoff: 01/31/2017


---
# <a name="azure-iot-hub-developer-guide"></a>Guía del desarrollador del Centro de IoT de Azure
IoT Hub de Azure es un servicio totalmente administrado que permite la comunicación bidireccional confiable y segura entre millones de dispositivos y una solución de back-end.

IoT Hub de Azure le proporciona:

* Comunicaciones seguras a través del uso de credenciales de seguridad para cada dispositivo y el control de acceso.
* Varias opciones de comunicación a gran escala de dispositivo a la nube y de la nube a dispositivo.
* Almacenamiento consultable de la información de estado y los metadatos de cada dispositivo.
* Conectividad fácil del dispositivo con las bibliotecas de dispositivos para las plataformas y lenguajes más populares.

Esta guía del desarrollador de IoT Hub incluye los siguientes artículos:

* En [Envío y recepción de mensajes con IoT Hub][devguide-messaging] se describen las características de mensajería (dispositivo a la nube y nube al dispositivo) que muestra IoT Hub. El artículo también incluye información acerca de temas como enrutamiento de mensajes, formatos de mensaje y protocolos de comunicaciones compatibles y los números de puerto que usan.
* [Device-to-cloud communication guidance][lnk-d2c-guidance] (Guía de comunicación de dispositivo a nube) le permite elegir entre los mensajes del dispositivo a la nube, las propiedades notificadas del dispositivo gemelo y la carga de archivos.
* [Guía de comunicación de nube a dispositivo][lnk-c2d-guidance] le ayuda a elegir entre métodos directos, las propiedades preferidas del dispositivo gemelo y los mensajes de la nube al dispositivo.
* En [Carga de archivos desde un dispositivo][devguide-upload] se describe cómo se cargan archivos desde un dispositivo. El artículo también incluye información acerca de temas como las notificaciones que el proceso de carga puede enviar.
* En [Administrar identidades del dispositivo en IoT Hub][devguide-identities] se describe qué información almacena el registro de identidad de cada centro de IoT y cómo se accede a él y se modifica.
* En [Control de acceso a IoT Hub][devguide-security] se describe el modelo de seguridad que se usa para conceder acceso a la funcionalidad de IoT Hub tanto para los dispositivos como para los componentes de la nube. El artículo incluye información acerca del uso de tokens y certificados X.509, y los detalles de los permisos que puede conceder.
* En [Uso de dispositivos gemelos para sincronizar el estado y las configuraciones][devguide-device-twins] se describe el concepto de *dispositivo gemelo* y la funcionalidad que expone, como la sincronización de un dispositivo con su gemelo. El artículo incluye información acerca de los datos almacenados en un dispositivo gemelo.
* En [Invocación de un método directo en un dispositivo][devguide-directmethods] se describe el ciclo de vida de un método directo y proporciona información sobre cómo invocar métodos en un dispositivo desde la aplicación back-end y controlar el método directo en el dispositivo.
* En [Programación de trabajos en varios dispositivos][devguide-jobs] se describe cómo se programan trabajos en varios dispositivos. En el artículo se describe cómo enviar trabajos que realizan tareas como la ejecución de un método directo y la actualización de un dispositivo mediante un dispositivo gemelo. También describe cómo consultar el estado de un trabajo.
* En la [reference de puntos de conexión de IoT Hub][devguide-endpoints] se describen los diferentes puntos de conexión que expone cada centro de IoT para las operaciones en tiempo de ejecución y de administración. El artículo también describe cómo puede crear puntos de conexión adicionales en su IoT Hub y cómo usar una puerta de enlace de campo para habilitar la conectividad de dispositivos con los puntos de conexión de su IoT Hub en escenarios no estándar.
* En [Referencia: lenguaje de consulta para gemelos y trabajos][devguide-query] se describe el lenguaje de consulta de IoT Hub que permite recuperar información del centro sobre los dispositivos gemelos y los trabajos.
* En [Referencia: cuotas y limitaciones][devguide-quotas] se resumen las cuotas establecidas en el servicio de IoT Hub y el comportamiento de limitación que puede esperar ver cuando supere la cuota.
* [Referencia: precios][devguide-pricing] proporciona información general sobre diferentes SKU y precios para IoT Hub y detalles sobre cómo las diversas funcionalidades se miden como mensajes en IoT Hub.
* En [Referencia: SDK de servicio y de dispositivo][devguide-sdks] se muestran los diversos SDK que puede usar para desarrollar aplicaciones de dispositivo y de servicio que interactúen con su IoT Hub. El artículo incluye vínculos a documentación de la API en línea.
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


