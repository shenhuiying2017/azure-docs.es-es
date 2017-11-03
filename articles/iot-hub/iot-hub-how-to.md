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
ms.date: 10/13/2017
ms.author: dobett
ms.openlocfilehash: b451ea2d42f0ff2f64746bcb296fb9128472fea8
ms.sourcegitcommit: e6029b2994fa5ba82d0ac72b264879c3484e3dd0
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/24/2017
---
# <a name="how-to-use-azure-iot-hub"></a>Uso de Azure IoT Hub

Tiene varias opciones para aprender a desarrollar para el servicio IoT Hub:

* Lea los artículos conceptuales en los que se describen los detalles de las características de IoT Hub.
* Siga uno de los tutoriales que trata las distintas características de IoT Hub.

## <a name="developer-guide"></a>Guía del desarrollador

Como desarrollador, puede leer información conceptual detallada acerca de IoT Hub en la [Guía del desarrollador][lnk-devguide]. Esta guía incluye:

* Descripciones detalladas de todas las características de IoT Hub para enseñarle a usarlas.
* Instrucciones sobre qué elección hacer cuando hay varias opciones disponibles.

## <a name="tutorials"></a>Tutoriales

Si prefiere obtener información sobre características específicas de IoT Hub trabajando con ejercicios prácticos, hay varios tutoriales entre los que puede elegir. Muchos de estos tutoriales están disponibles en varios lenguajes de programación. Entre estos tutoriales se incluyen:

- [Procesamiento de mensajes de dispositivo a nube de IoT Hub mediante rutas][lnk-routes-tutorial]. Este tutorial le muestra cómo usar reglas de enrutamiento de IoT Hub para enviar mensajes del dispositivo a la nube de manera sencilla y basada en la configuración.

- [Envío de mensajes de nube a dispositivo con IoT Hub][lnk-c2d-tutorial]. Este tutorial muestra cómo enviar mensajes de la nube al dispositivo a través de IoT Hub y cómo recibir estos mensajes en un dispositivo.

- [Cargar archivos desde dispositivos a la nube con IoT Hub][lnk-upload-tutorial]. Este tutorial le muestra cómo usar las funcionalidades de carga de archivos de IoT Hub.

- [Introducción a los dispositivos gemelos][lnk-twin-tutorial]. Este tutorial hace una introducción sobre los dispositivos gemelos, las propiedades notificadas, las propiedades deseadas y las etiquetas. Los dispositivos gemelos se usan para sincronizar los valores con los dispositivos.

- [Uso de métodos directos][lnk-methods-tutorial]. En este tutorial se muestra cómo usar métodos directos. Podrá agregar un controlador para un método directo en el dispositivo simulado e invocar al método directo desde IoT Hub.

- [Introducción a la administración de dispositivos][lnk-dm-tutorial]. En este tutorial se explica cómo usar las características clave de administración de dispositivos, como los dispositivos gemelos y los métodos directos. Utilice estas características para reiniciar de forma remota el dispositivo simulado.

- [Uso de las propiedades deseadas para configurar dispositivos][lnk-properties-tutorial]. En este tutorial se explica cómo usar las propiedades deseadas y notificadas del dispositivo gemelo, para configurarlo de forma remota.

- [Uso de trabajos de dispositivo para iniciar una actualización de firmware de un dispositivo][lnk-jobs-tutorial]. En este tutorial se explica cómo usar las características clave de administración de dispositivos, como los dispositivos gemelos y los métodos directos. Se ofrece información sobre cómo usar estas características para actualizar el firmware del dispositivo de forma remota.

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