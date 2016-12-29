---
title: "Guía de comunicación de dispositivo a nube de Azure IoT Hub | Microsoft Docs"
description: "Guía del desarrollador de Azure IoT Hub: una guía sobre cuándo usar mensajes de dispositivo a nube, propiedades notificadas o carga de archivos."
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
ms.date: 09/30/2016
ms.author: elioda
translationtype: Human Translation
ms.sourcegitcommit: 53f14e6fe115ed5f96d25b9ec5ab04abe23712d5
ms.openlocfilehash: 6d5608cd86e3f042a7d63bd4e43e75a06141dfab


---
# <a name="device-to-cloud-communications-guidance"></a>Guía de comunicación de dispositivo a nube
Al enviar información desde la aplicación del dispositivo al back-end, IoT Hub presenta tres opciones:

* [Mensajes de dispositivo a nube (D2C)][lnk-d2c], para telemetría y alertas de series temporales;
* [Propiedades notificadas][lnk-twins], para notificar información de estado del dispositivo, como funcionalidades disponibles, condiciones y estado de los flujos de trabajo de ejecución prolongada (por ejemplo, configuración y actualizaciones de software);
* [Cargas de archivos][lnk-fileupload], para archivos multimedia y grandes lotes de telemetría cargados por dispositivos conectados de manera intermitente o comprimidos para ahorrar ancho de banda.

Esta es una comparación detallada de las distintas opciones de comunicación de dispositivo a nube.

|  | Mensajes de D2C | Propiedades notificadas | Cargas de archivos |
| ---- | ------- | ---------- | ---- |
| Escenario | Alertas y series temporales de telemetría, por ejemplo, lotes de datos de sensor de 256 KB enviados cada 5 minutos. | Funcionalidades y condiciones disponibles, por ejemplo, modo de conectividad actual del dispositivo (telefonía móvil o wi-fi). Sincronización de flujos de trabajo de ejecución prolongada, como configuración y actualizaciones de software. | Archivos multimedia. Lotes de telemetría (normalmente comprimidos) de gran tamaño. |
| Almacenamiento y recuperación | Almacenados temporalmente por IoT Hub, hasta 7 días. Solo lectura secuencial. | Almacenados por IoT Hub en el dispositivo gemelo. Recuperables mediante el [lenguaje de consulta de IoT Hub][lnk-query]. | Almacenadas en la cuenta de Azure Storage proporcionada por el usuario. |
| Tamaño | Hasta 256 KB de mensajes. | El tamaño máximo de las propiedades notificadas es 8 KB. | Tamaño máximo de archivo admitido por Azure Blob Storage. |
| Frecuencia | Alta. Para más información, consulte [Límites de IoT Hub][lnk-quotas]. | Mediana. Para más información, consulte [Límites de IoT Hub][lnk-quotas]. | Baja. Para más información, consulte [Límites de IoT Hub][lnk-quotas]. |
| Protocol | Disponible en todos los protocolos. | Actualmente solo está disponible cuando se usa MQTT. | Disponible cuando se usa cualquier protocolo, pero hace falta HTTP en el dispositivo. |

> [!NOTE]
> Es posible que una aplicación deba enviar información como alertas o series temporales de telemetría y también permitir que esté disponible en el dispositivo gemelo. En esos casos, la aplicación de dispositivo puede enviar un mensaje D2C y notificar un cambio de propiedad, o el back-end puede almacenar la información en las etiquetas del dispositivo gemelo cuando recibe un mensaje. Dado que los mensajes D2C permiten una capacidad de proceso mucho mayor que las actualizaciones de dispositivos gemelos, en ocasiones es aconsejable evitar actualizar el dispositivo gemelo con cada mensaje D2C.
> 
> 

[lnk-twins]: iot-hub-devguide-device-twins.md
[lnk-fileupload]: iot-hub-devguide-file-upload.md
[lnk-quotas]: iot-hub-devguide-quotas-throttling.md
[lnk-query]: iot-hub-devguide-query-language.md
[lnk-d2c]: iot-hub-devguide-messaging.md#device-to-cloud-messages



<!--HONumber=Nov16_HO5-->


