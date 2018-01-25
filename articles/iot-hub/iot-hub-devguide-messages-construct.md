---
title: "Información sobre el formato de mensaje de Azure IoT Hub | Microsoft Docs"
description: "Guía del desarrollador: describe el formato y el contenido esperado de los mensajes de IoT Hub."
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
ms.openlocfilehash: c57ceb83951341d4a7bf368e209ba0f88825672c
ms.sourcegitcommit: 28178ca0364e498318e2630f51ba6158e4a09a89
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/24/2018
---
# <a name="create-and-read-iot-hub-messages"></a>Creación y lectura de mensajes de IoT Hub

Para admitir la interoperabilidad sin problemas entre protocolos, IoT Hub define un formato de mensaje común para todos los protocolos accesibles desde el dispositivo. Este formato de mensaje se usa tanto para mensajes [dispositivo a nube][lnk-d2c] como para [nube a dispositivo][lnk-c2d]. Un [mensaje IoT Hub][lnk-messaging] consta de:

* Un conjunto de *propiedades del sistema*. Propiedades que IoT Hub interpreta o establece. Este conjunto es el predeterminado.
* Un conjunto de *propiedades de la aplicación*. Diccionario de propiedades de cadena que la aplicación puede definir y a las que puede acceder sin necesidad de deserializar el cuerpo del mensaje. IoT Hub nunca modifica estas propiedades.
* Un cuerpo binario opaco.

Los nombres de propiedad solo pueden contener caracteres alfanuméricos ASCII y ```{'!', '#', '$', '%, '&', "'", '*', '+', '-', '.', '^', '_', '`', '|', '~'}``` cuando realiza las siguientes acciones:  

* Envío de mensajes de dispositivo a nube mediante el protocolo HTTPS.
* Envío de mensajes de nube a dispositivo.

Para obtener más información sobre cómo codificar y descodificar el mensaje usando distintos protocolos, consulte [SDK de Azure IoT][lnk-sdks].

En la siguiente tabla, aparece el conjunto de propiedades del sistema en los mensajes de IoT Hub.

| Propiedad | DESCRIPCIÓN |
| --- | --- |
| MessageId |Un identificador configurable por el usuario para el mensaje utilizado para patrones de solicitud y respuesta. Formato: una cadena que distingue mayúsculas y minúsculas (de hasta 128 caracteres) de caracteres alfanuméricos ASCII de 7 bits + `{'-', ':',’.', '+', '%', '_', '#', '*', '?', '!', '(', ')', ',', '=', '@', ';', '$', '''}`. |
| Número de secuencia |Un número (exclusivo para cada cola de dispositivo) asignado por IoT Hub a cada mensaje de nube a dispositivo. |
| Para |Un destino especificado en los mensajes [de la nube al dispositivo][lnk-c2d]. |
| ExpiryTimeUtc |Fecha y hora de la expiración del mensaje. |
| EnqueuedTime |Fecha y hora en la que IoT Hub recibió el mensaje de [nube a dispositivo][lnk-c2d]. |
| CorrelationId |Cadena de propiedad en un mensaje de respuesta que normalmente contiene el identificador del mensaje de la solicitud en los patrones de solicitud y respuesta. |
| UserId |Un identificador que se utiliza para especificar el origen de los mensajes. Cuando IoT Hub genera mensajes, se establece en `{iot hub name}`. |
| Ack |Un generador de mensajes de comentarios. Esta propiedad se usa en los mensajes de nube a dispositivo para solicitar a IoT Hub que genere mensajes de comentarios debido al consumo del mensaje por el dispositivo. Valores posibles: **none** (valor predeterminado): no se genera ningún mensaje de comentarios, **positive**: recibe un mensaje de comentarios si el mensaje se completó, **negative**: recibe un mensaje de comentarios si el mensaje expiró (o si se alcanzó el número máximo de entregas) sin que se complete en el dispositivo, y **full**: comentarios positivos y negativos. Para más información, consulte [Comentarios de mensajes][lnk-feedback]. |
| ConnectionDeviceId |Un identificador establecido por IoT Hub en los mensajes de dispositivo a nube. Contiene el **deviceId** del dispositivo que envió el mensaje. |
| ConnectionDeviceGenerationId |Un identificador establecido por IoT Hub en los mensajes de dispositivo a nube. Contiene el valor **generationId** (como se indica en [Propiedades de identidad del dispositivo][lnk-device-properties]) del dispositivo que envió el mensaje. |
| ConnectionAuthMethod |Un método de autenticación establecido por IoT Hub en los mensajes de dispositivo a nube. Esta propiedad contiene información sobre el método de autenticación usado para autenticar el dispositivo que envía el mensaje. Para más información, consulte la sección [Propiedades contra la suplantación][lnk-antispoofing]. |

## <a name="message-size"></a>Tamaño del mensaje

IoT Hub mide el tamaño de los mensajes de una manera independiente del protocolo, teniendo en cuenta solo la carga real. El tamaño en bytes se calcula como la suma de los siguientes elementos:

* El tamaño del cuerpo en bytes.
* El tamaño en bytes de todos los valores de las propiedades del sistema de mensajes.
* El tamaño en bytes de todos los valores y nombres de propiedades del usuario.

Solamente se pueden usar caracteres ASCII para los valores y los nombres de propiedades, por lo que la longitud de las cadenas es igual al tamaño en bytes.

## <a name="next-steps"></a>pasos siguientes

Para obtener información acerca de los límites de tamaño de mensaje IoT Hub, consulte [Cuotas y limitación de IoT Hub][lnk-quotas].

Para obtener información sobre cómo crear y leer mensajes de IoT Hub en varios lenguajes de programación, consulte los tutoriales de [Introducción][lnk-get-started].

[lnk-messaging]: iot-hub-devguide-messaging.md
[lnk-quotas]: iot-hub-devguide-quotas-throttling.md
[lnk-get-started]: iot-hub-get-started.md
[lnk-sdks]: iot-hub-devguide-sdks.md
[lnk-c2d]: iot-hub-devguide-messages-c2d.md
[lnk-d2c]: iot-hub-devguide-messages-d2c.md
[lnk-feedback]: iot-hub-devguide-messages-c2d.md#message-feedback
[lnk-device-properties]: iot-hub-devguide-identity-registry.md#device-identity-properties
[lnk-antispoofing]: iot-hub-devguide-messages-d2c.md#anti-spoofing-properties
