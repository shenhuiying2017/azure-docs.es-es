---
title: Opciones de dispositivo a la nube de IoT Hub de Azure | Microsoft Docs
description: "Guía del desarrollador: una guía sobre cuándo usar mensajes de dispositivo a nube, propiedades notificadas o carga de archivos para comunicaciones de nube a dispositivo."
services: iot-hub
documentationcenter: .net
author: fsautomata
manager: timlt
editor: 
ms.assetid: 979136db-c92d-4288-870c-f305e8777bdd
ms.service: iot-hub
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/09/2017
ms.author: elioda
ms.openlocfilehash: 335928776e1e62caf2855cd5a5684ccaf37f73cd
ms.sourcegitcommit: 51ea178c8205726e8772f8c6f53637b0d43259c6
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="device-to-cloud-communications-guidance"></a>Guía de comunicación de dispositivo a nube
Al enviar información desde la aplicación del dispositivo al back-end de solución, IoT Hub presenta tres opciones:

* [Mensajes de dispositivo a nube][lnk-d2c], para telemetría y alertas de series temporales.
* [Propiedades notificadas del dispositivo gemelo][lnk-twins], para notificar información de estado del dispositivo, como funcionalidades disponibles, condiciones o estado de los flujos de trabajo de ejecución prolongada. Por ejemplo, configuración y actualizaciones de software.
* [Cargas de archivos][lnk-fileupload], para archivos multimedia y grandes lotes de telemetría cargados por dispositivos conectados de manera intermitente o comprimidos para ahorrar ancho de banda.

Esta es una comparación detallada de las distintas opciones de comunicación de dispositivo a nube.

|  | Mensajes de dispositivo a nube | Propiedades notificadas del dispositivo gemelo | Cargas de archivos |
| ---- | ------- | ---------- | ---- |
| Escenario | Serie temporal de telemetría y alertas. Por ejemplo, lotes de datos del sensor de 256 KB enviados cada cinco minutos. | Funcionalidades disponibles y condiciones. Por ejemplo, el modo actual de conectividad del dispositivo, como móvil o WiFi. Sincronización de flujos de trabajo de ejecución prolongada, como configuración y actualizaciones de software. | Archivos multimedia. Lotes de telemetría (generalmente comprimidos) de gran tamaño. |
| Almacenamiento y recuperación | Almacenados temporalmente por IoT Hub, hasta 7 días. Solo lectura secuencial. | Almacenados por IoT Hub en el dispositivo gemelo. Recuperables mediante el [lenguaje de consulta de IoT Hub][lnk-query]. | Almacenadas en la cuenta de Azure Storage proporcionada por el usuario. |
| Tamaño | Mensajes de hasta 256 KB. | El tamaño máximo de las propiedades notificadas es 8 KB. | Tamaño máximo de archivo admitido por Azure Blob Storage. |
| Frecuencia | Alta. Para más información, consulte los [Límites de IoT Hub][lnk-quotas]. | Mediana. Para más información, consulte los [Límites de IoT Hub][lnk-quotas]. | Baja. Para más información, consulte los [Límites de IoT Hub][lnk-quotas]. |
| Protocol | Disponible en todos los protocolos. | Disponible con MQTT o AMQP. | Disponible cuando se usa cualquier protocolo, pero hace falta HTTPS en el dispositivo. |

Es posible que una aplicación deba enviar información como alertas o series temporales de telemetría y también permitir que esté disponible en el dispositivo gemelo. En este escenario, puede elegir una de las siguientes opciones:

* La aplicación para dispositivo envía un mensaje de dispositivo a nube y notifica un cambio de propiedad.
* El back-end de la solución puede almacenar la información en las etiquetas del dispositivo gemelo cuando recibe el mensaje.

Dado que los mensajes de dispositivo a nube permiten una capacidad de proceso mucho mayor que las actualizaciones de dispositivos gemelos, a veces es aconsejable evitar actualizar el dispositivo gemelo con cada mensaje de dispositivo a nube.


[lnk-twins]: iot-hub-devguide-device-twins.md
[lnk-fileupload]: iot-hub-devguide-file-upload.md
[lnk-quotas]: iot-hub-devguide-quotas-throttling.md
[lnk-query]: iot-hub-devguide-query-language.md
[lnk-d2c]: iot-hub-devguide-messages-d2c.md
