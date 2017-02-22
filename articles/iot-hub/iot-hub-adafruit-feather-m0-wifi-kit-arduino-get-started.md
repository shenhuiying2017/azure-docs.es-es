---
title: "Conexión de Arduino (C) a Azure IoT: Introducción | Microsoft Docs"
description: "Con la introducción a Adafruit Feather M0 WiFi i puede crear un Azure IoT Hub y conectar Adafruit Feather M0 WiFi al centro de IoT"
services: iot-hub
documentationcenter: 
author: shizn
manager: timtl
tags: 
keywords: "Azure IoT Hub, introducción a Internet de las cosas, tutorial de Internet de las cosas, Adafruit e Internet de las cosas, introducción a Arduino"
ms.assetid: 51befcdb-332b-416f-a6a1-8aabdb67f283
ms.service: iot-hub
ms.devlang: arduino
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/13/2016
ms.author: xshi
translationtype: Human Translation
ms.sourcegitcommit: 64e69df256404e98f6175f77357500b562d74318
ms.openlocfilehash: 6789e6f1c094f8809163e29349c8ea54e1e97683


---
# <a name="get-started-with-your-arduino-board-adafruit-feather-m0-wifi"></a>Introducción a la placa Arduino: Adafruit Feather M0 WiFi

En este tutorial, empiece por aprender los conceptos básicos sobre cómo trabajar con la placa Arduino. A continuación, aprenderá a conectar sin problemas los dispositivos en la nube con [Azure IoT Hub](iot-hub-what-is-iot-hub.md).

## <a name="lesson-1-configure-your-device"></a>Lección 1: Configuración del dispositivo
![Diagrama integral de la lección 1][Lesson-1-end-to-end-diagram]

En esta lección, configurará la placa Arduino con un sistema operativo, configurará el entorno de desarrollo e implementará una aplicación en la placa Arduino.

### <a name="configure-your-device"></a>Configuración del dispositivo
Ensamble la placa y enciéndala para configurar la placa Arduino por primera vez.

*Tiempo estimado para completar el tutorial: 5 minutos*

Vaya a [Configuración del dispositivo][configure-your-device].

### <a name="get-the-tools"></a>Obtener las herramientas
Descargue las herramientas y el software para compilar e implementar su primera aplicación para la placa Arduino.

*Tiempo estimado para completar el tutorial: 20 minutos*

Vaya a [Obtener las herramientas][get-the-tools]

### <a name="create-and-deploy-the-blink-application"></a>Creación e implementación de la aplicación de intermitencia
Clone la aplicación de intermitencia de ejemplo de Arduino desde GitHub y use Gulp para implementar esta aplicación en la placa Arduino. La aplicación de ejemplo hace parpadear el LED 13 de la GPIO cada dos segundos.

*Tiempo estimado para completar el tutorial: 5 minutos*

Vaya a [Creación e implementación de la aplicación de intermitencia][create-and-deploy-the-blink-application].

## <a name="lesson-2-create-your-iot-hub"></a>Lección 2: Creación de la instancia de IoT Hub
![Diagrama integral de la lección 2][lesson-2-end-to-end-diagram]

En esta lección, creará una cuenta gratuita de Azure, aprovisionará la instancia de IoT Hub de Azure y creará su primer dispositivo en el centro de IoT Hub.

Complete la lección 1 antes de iniciar esta.

### <a name="get-the-azure-tools"></a>Obtención de las herramientas de Azure
Instalación de la interfaz de la línea de comandos de Azure (CLI de Azure)

*Tiempo estimado para completar el tutorial: 10 minutos*

Vaya a [Obtención de las herramientas de Azure][get-azure-tools].

### <a name="create-your-iot-hub-and-register-your-arduino-board"></a>Creación de IoT Hub y registro de la placa Arduino
Cree el grupo de recursos, aprovisione la primera instancia de IoT Hub de Azure y agregue el primer dispositivo a dicha instancia mediante la CLI de Azure.

*Tiempo estimado para completar el tutorial: 10 minutos*

Vaya a [Creación de IoT Hub y registro de la placa Arduino][create-your-iot-hub-and-register-your-arduino-board].

## <a name="lesson-3-send-device-to-cloud-messages"></a>Lección 3: Envío de mensajes de dispositivo a nube
![Diagrama integral de la lección 3][lesson-3-end-to-end-diagram]

En esta lección, enviará mensajes desde la placa Arduino a IoT Hub. También creará una aplicación de Azure Function que recoge los mensajes entrantes del centro de IoT y los escribe en Azure Table Storage.

Complete las lecciones 1 y 2 antes de iniciar esta.

### <a name="create-an-azure-function-app-and-azure-storage-account"></a>Creación de una cuenta de Azure Storage y una aplicación de Azure Function
Use una plantilla de Azure Resource Manager para crear una aplicación de Azure Function y una cuenta de Azure Storage.

*Tiempo estimado para completar el tutorial: 10 minutos*

Vaya a [Creación de una cuenta de Azure Storage y una aplicación de Azure Function][create-an-azure-function-app-and-azure-storage-account].

### <a name="run-a-sample-application-to-send-device-to-cloud-messages"></a>Ejecución de una aplicación de ejemplo para enviar mensajes de dispositivo a nube
Implemente y ejecute una aplicación de ejemplo para la placa Arduino que envía mensajes al centro de IoT.

*Tiempo estimado para completar el tutorial: 10 minutos*

Vaya a [Ejecución de una aplicación de ejemplo para enviar mensajes de dispositivo a nube][send-device-to-cloud-messages].

### <a name="read-messages-persisted-in-azure-storage"></a>Lectura de los mensajes que se conservan en Azure Storage
Supervise los mensajes de dispositivo a nube a medida que se escriben en Azure Storage.

*Tiempo estimado para completar el tutorial: 5 minutos*

Vaya a [Lectura de los mensajes que se conservan en Azure Storage][read-messages-persisted-in-azure-storage].

## <a name="lesson-4-send-cloud-to-device-messages"></a>Lección 4. Envío de mensajes de nube a dispositivo
![Diagrama integral de la lección 4][lesson-4-end-to-end-diagram]

En esta lección se explica cómo enviar mensajes desde la instancia de Azure IoT Hub a la placa Arduino. Los mensajes controlan el comportamiento de encendido y apagado del LED integrado en GPIO #13. Una aplicación de ejemplo está preparada para que pueda llevar a cabo esta tarea.

Complete las lecciones 1, 2 y 3 antes de iniciar esta.

### <a name="run-the-sample-application-to-receive-cloud-to-device-messages"></a>Ejecución de la aplicación de ejemplo para recibir mensajes de nube a dispositivo
La aplicación de ejemplo de la lección 4 se ejecuta en la placa Arduino y supervisa los mensajes entrantes de IoT Hub. Una nueva tarea de Gulp envía mensajes a la placa Arduino desde IoT Hub para que parpadee el LED.

*Tiempo estimado para completar el tutorial: 10 minutos*

Vaya a [Ejecución de la aplicación de ejemplo para recibir mensajes de nube a dispositivo][receive-cloud-to-device-messages].

### <a name="optional-section-change-the-on-and-off-behavior-of-the-led"></a>Sección opcional: Cambio del comportamiento de encendido y apagado del LED
Personalice los mensajes para cambiar el comportamiento de encendido y apagado del LED.

*Tiempo estimado para completar el tutorial: 10 minutos*

Vaya a [Sección opcional: Cambio del comportamiento de encendido y apagado del LED][change-the-on-and-off-behavior-of-the-led].

## <a name="troubleshooting"></a>Solución de problemas
Si tiene algún problema durante las lecciones, puede encontrar soluciones en el artículo [Solución de problemas][troubleshooting].

<!-- Images and links -->

[Lesson-1-end-to-end-diagram]: media/iot-hub-adafruit-feather-m0-wifi-lessons/e2e-lesson1.png
[configure-your-device]: iot-hub-adafruit-feather-m0-wifi-kit-arduino-lesson1-configure-your-device.md
[get-the-tools]: iot-hub-adafruit-feather-m0-wifi-kit-arduino-lesson1-get-the-tools-win32.md
[create-and-deploy-the-blink-application]: iot-hub-adafruit-feather-m0-wifi-kit-arduino-lesson1-deploy-blink-app.md
[lesson-2-end-to-end-diagram]: media/iot-hub-adafruit-feather-m0-wifi-lessons/e2e-lesson2.png
[get-azure-tools]: iot-hub-adafruit-feather-m0-wifi-kit-arduino-lesson2-get-azure-tools-win32.md
[create-your-iot-hub-and-register-your-arduino-board]: iot-hub-adafruit-feather-m0-wifi-kit-arduino-lesson2-prepare-azure-iot-hub.md
[lesson-3-end-to-end-diagram]: media/iot-hub-adafruit-feather-m0-wifi-lessons/e2e-lesson3.png
[create-an-azure-function-app-and-azure-storage-account]: iot-hub-adafruit-feather-m0-wifi-kit-arduino-lesson3-deploy-resource-manager-template.md
[send-device-to-cloud-messages]: iot-hub-adafruit-feather-m0-wifi-kit-arduino-lesson3-run-azure-blink.md
[read-messages-persisted-in-azure-storage]: iot-hub-adafruit-feather-m0-wifi-kit-arduino-lesson3-read-table-storage.md
[lesson-4-end-to-end-diagram]: media/iot-hub-adafruit-feather-m0-wifi-lessons/e2e-lesson4.png
[receive-cloud-to-device-messages]: iot-hub-adafruit-feather-m0-wifi-kit-arduino-lesson4-send-cloud-to-device-messages.md
[change-the-on-and-off-behavior-of-the-led]: iot-hub-adafruit-feather-m0-wifi-kit-arduino-lesson4-change-led-behavior.md
[troubleshooting]: iot-hub-adafruit-feather-m0-wifi-kit-arduino-troubleshooting.md


<!--HONumber=Jan17_HO4-->


