---
title: Uso de Azure IoT Hub | Microsoft Docs
description: "Como desarrollador, ¿cómo uso las distintas características de IoT Hub?"
services: iot-hub
documentationcenter: 
author: dominicbetts
manager: timlt
editor: 
ms.assetid: 24376318-5344-4a81-a1e6-0003ed587d53
ms.service: iot-hub
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/31/2017
ms.author: dobett
translationtype: Human Translation
ms.sourcegitcommit: 6fbdebd316cd00e7dd762487848e616fdd2317e8
ms.openlocfilehash: 358fd0888049f97e5fde6e2a6303ea6c4cf65da9
ms.lasthandoff: 03/01/2017


---
# <a name="how-to-use-azure-iot-hub"></a>Uso de Azure IoT Hub

Tiene varias opciones para aprender a desarrollar para el servicio IoT Hub. Puede leer los artículos conceptuales que describen las características de IoT Hub detalladamente o seguir alguno de los tutoriales que explican las distintas características de IoT Hub.

## <a name="the-developer-guide"></a>Guía del desarrollador

Como desarrollador, puede leer información conceptual detallada acerca de IoT Hub en la [Guía del desarrollador][lnk-devguide]. Esta guía incluye descripciones detalladas de todas las características de IoT Hub que le ayudarán a aprender a utilizarlas y a elegir entre ellas cuando existan varias opciones


## <a name="the-tutorials"></a>Los tutoriales

Si prefiere obtener información sobre características específicas de IoT Hub trabajando con ejercicios prácticos, hay varios tutoriales entre los que puede elegir. Muchos de estos tutoriales están disponibles en varios lenguajes de programación. Entre estos tutoriales se incluyen:

- [Procesamiento de mensajes de dispositivo a nube de IoT Hub mediante rutas][lnk-routes-tutorial]. Este tutorial le muestra cómo usar reglas de enrutamiento de IoT Hub para enviar mensajes del dispositivo a la nube de manera sencilla y basada en la configuración.

- [Envío de mensajes de nube a dispositivo con IoT Hub][lnk-c2d-tutorial]. Este tutorial muestra cómo enviar mensajes de la nube al dispositivo a través de IoT Hub y cómo recibir estos mensajes en un dispositivo.

- [Cargar archivos desde dispositivos a la nube con IoT Hub][lnk-upload-tutorial]. Este tutorial le muestra cómo usar las funcionalidades de carga de archivos de IoT Hub.

- [Introducción a los dispositivos gemelos][lnk-twin-tutorial]. Este tutorial hace una introducción sobre los dispositivos gemelos, las propiedades notificadas, las propiedades deseadas y las etiquetas. Los dispositivos gemelos se usan para sincronizar los valores con los dispositivos.

- [Uso de métodos directos][lnk-methods-tutorial]. En este tutorial se muestra cómo usar métodos directos. Podrá agregar un controlador para un método directo en el dispositivo simulado e invocar al método directo desde IoT Hub.

- [Introducción a la administración de dispositivos][lnk-dm-tutorial]. Este tutorial le muestra cómo utilizar características clave de administración de dispositivos como, por ejemplo, los dispositivos gemelos y métodos directos para reiniciar de forma remota su dispositivo simulado.

- [Uso de las propiedades deseadas para configurar dispositivos][lnk-properties-tutorial]. En este tutorial se explica cómo usar las propiedades deseadas del dispositivo gemelo, junto con las propiedades notificadas, para configurar el dispositivo de forma remota.

- [Uso de trabajos de dispositivo para iniciar una actualización de firmware de un dispositivo][lnk-jobs-tutorial]. Este tutorial le muestra cómo utilizar características clave de administración de dispositivos como, por ejemplo, los dispositivos gemelos y métodos directos para actualizar de forma remota el firmware del dispositivo.

- [Programación y difusión de trabajos][lnk-schedule-tutorial]. Este tutorial le muestra cómo utilizar las propiedades deseadas y los métodos directos para interactuar con varios dispositivos a una hora programada.

## <a name="next-steps"></a>Pasos siguientes

Para más información sobre el servicio IoT Hub, consulte la [Guía del desarrollador][lnk-devguide].

[lnk-devguide]: ./iot-hub-devguide.md
[lnk-routes-tutorial]: ./iot-hub-csharp-csharp-process-d2c.md
[lnk-c2d-tutorial]: ./iot-hub-csharp-csharp-c2d.md
[lnk-upload-tutorial]: ./iot-hub-csharp-csharp-file-upload.md
[lnk-twin-tutorial]: ./iot-hub-node-node-twin-getstarted.md
[lnk-methods-tutorial]: ./iot-hub-node-node-direct-methods.md
[lnk-dm-tutorial]: ./iot-hub-node-node-device-management-get-started.md
[lnk-properties-tutorial]: ./iot-hub-node-node-twin-how-to-configure.md
[lnk-jobs-tutorial]: ./iot-hub-node-node-firmware-update.md
[lnk-schedule-tutorial]: ./iot-hub-node-node-schedule-jobs.md
