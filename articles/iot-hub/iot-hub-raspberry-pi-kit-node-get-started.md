---
title: "Conexión de Raspberry Pi (Node) a Azure IoT: Introducción | Microsoft Docs"
description: "Introducción al dispositivo Raspberry Pi 3, creación de una instancia de IoT Hub de Azure y conexión de Pi a dicha instancia"
services: iot-hub
documentationcenter: 
author: shizn
manager: timlt
tags: 
keywords: "azure iot hub, introducción a Internet de las cosas, kit de herramientas de iot"
experimental: true
experiment_id: xshi-happypathemu-20161202
ms.assetid: b0e14bfa-8e64-440a-a6ec-e507ca0f76ba
ms.service: iot-hub
ms.devlang: node
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 3/21/2017
ms.author: xshi
ms.custom: H1Hack27Feb2017
translationtype: Human Translation
ms.sourcegitcommit: 7adde91586f5fbbffd0aeaf0efb0810cc891ac0b
ms.openlocfilehash: 8d283531fc7905e9691feccca25fffd27499e2bb
ms.lasthandoff: 03/02/2017


---
# <a name="connect-your-raspberry-pi-3-device-to-your-iot-hub-using-nodejs"></a>Conexión de un dispositivo Raspberry Pi 3 a su IoT Hub mediante Node.js
> [!div class="op_single_selector"]
> * [Node.JS](iot-hub-raspberry-pi-kit-node-get-started.md)
> * [C](iot-hub-raspberry-pi-kit-c-get-started.md)

En este tutorial, empezará por aprender los principios básicos del uso de Raspberry Pi 3 que ejecuta Raspbian. A continuación, aprenderá a conectar sin problemas los dispositivos en la nube con [Azure IoT Hub](iot-hub-what-is-iot-hub.md). Para obtener ejemplos de Windows 10 IoT Core, vaya al [Centro de desarrollo de Windows](http://www.windowsondevices.com/).

¿Aún no tiene un kit? Comience [aquí](https://azure.microsoft.com/develop/iot/starter-kits).

## <a name="lesson-1-configure-your-device"></a>Lección 1: Configuración del dispositivo
![Diagrama integral de la lección 1](media/iot-hub-raspberry-pi-lessons/e2e-lesson1.png)

En esta lección, configurará el dispositivo Raspberry Pi 3 con un sistema operativo, configurará el entorno de desarrollo e implementará una aplicación en la Pi.

### <a name="configure-your-device"></a>Configuración del dispositivo
Configure el dispositivo Raspberry Pi 3 para usarlo por primera vez e instale Raspbian. Raspbian es un sistema operativo gratuito que está optimizado para el hardware de Raspberry Pi.

*Tiempo estimado para completar el tutorial: 30 minutos*

Vaya a [Configuración del dispositivo](iot-hub-raspberry-pi-kit-node-lesson1-configure-your-device.md).

### <a name="get-the-tools"></a>Obtener las herramientas
Descargue las herramientas y el software para compilar e implementar su primera aplicación para Raspberry Pi 3.

*Tiempo estimado para completar el tutorial: 20 minutos*

Vaya a [Obtener las herramientas](iot-hub-raspberry-pi-kit-node-lesson1-get-the-tools-win32.md).

### <a name="create-and-deploy-the-blink-application"></a>Creación e implementación de la aplicación de intermitencia
Clone la aplicación de intermitencia Node.js de ejemplo de GitHub y use Gulp para implementar esta aplicación en la placa Raspberry Pi 3. Esta aplicación de ejemplo hace parpadear el LED conectado a la placa cada dos segundos.

*Tiempo estimado para completar el tutorial: 5 minutos*

Vaya a [Creación e implementación de la aplicación de intermitencia](iot-hub-raspberry-pi-kit-node-lesson1-deploy-blink-app.md).

## <a name="lesson-2-create-your-iot-hub"></a>Lección 2: Creación de la instancia de IoT Hub
![Diagrama integral de la lección 2](media/iot-hub-raspberry-pi-lessons/e2e-lesson2.png)

En esta lección, creará una cuenta gratuita de Azure, aprovisionará la instancia de IoT Hub de Azure y creará su primer dispositivo en el centro de IoT.

Complete la lección 1 antes de iniciar esta.

### <a name="get-the-azure-tools"></a>Obtención de las herramientas de Azure
Instalación de la interfaz de la línea de comandos de Azure (CLI de Azure)

*Tiempo estimado para completar el tutorial: 10 minutos*

Vaya a [Obtención de las herramientas de Azure](iot-hub-raspberry-pi-kit-node-lesson2-get-azure-tools-win32.md).

### <a name="create-your-iot-hub-and-register-raspberry-pi-3"></a>Creación de un centro de IoT y registro de Raspberry Pi 3
Cree el grupo de recursos, aprovisione la primera instancia de IoT Hub de Azure y agregue el primer dispositivo a dicha instancia mediante la CLI de Azure.

*Tiempo estimado para completar el tutorial: 10 minutos*

Vaya a [Creación de un centro de IoT y registro de Raspberry Pi 3](iot-hub-raspberry-pi-kit-node-lesson2-prepare-azure-iot-hub.md).

## <a name="lesson-3-send-device-to-cloud-messages"></a>Lección 3: Envío de mensajes de dispositivo a nube
![Diagrama integral de la lección 3](media/iot-hub-raspberry-pi-lessons/e2e-lesson3.png)

En esta lección, enviará mensajes desde la Pi al centro de IoT. También creará una aplicación de Azure Function que recoge los mensajes entrantes del centro de IoT y los escribe en Azure Table Storage.

Complete las lecciones 1 y 2 antes de iniciar esta.

### <a name="create-an-azure-function-app-and-azure-storage-account"></a>Creación de una cuenta de Azure Storage y una aplicación de Azure Function
Use una plantilla de Azure Resource Manager para crear una aplicación de Azure Function y una cuenta de Azure Storage.

*Tiempo estimado para completar el tutorial: 10 minutos*

Vaya a [Creación de una cuenta de Azure Storage y una aplicación de Azure Function](iot-hub-raspberry-pi-kit-node-lesson3-deploy-resource-manager-template.md).

### <a name="run-a-sample-application-to-send-device-to-cloud-messages"></a>Ejecución de una aplicación de ejemplo para enviar mensajes de dispositivo a nube
Implemente y ejecute una aplicación de ejemplo para el dispositivo Raspberry Pi 3 que envía mensajes al centro de IoT.

*Tiempo estimado para completar el tutorial: 10 minutos*

Vaya a [Ejecución de una aplicación de ejemplo para enviar mensajes de dispositivo a nube](iot-hub-raspberry-pi-kit-node-lesson3-run-azure-blink.md).

### <a name="read-messages-persisted-in-azure-storage"></a>Lectura de los mensajes que se conservan en Azure Storage
Supervise los mensajes de dispositivo a nube a medida que se escriben en Azure Storage.

*Tiempo estimado para completar el tutorial: 5 minutos*

Vaya a [Lectura de los mensajes que se conservan en Azure Storage](iot-hub-raspberry-pi-kit-node-lesson3-read-table-storage.md).

## <a name="lesson-4-send-cloud-to-device-messages"></a>Lección 4. Envío de mensajes de nube a dispositivo
![Diagrama integral de la lección 4](media/iot-hub-raspberry-pi-lessons/e2e-lesson4.png)

En esta lección se explica cómo enviar mensajes desde la instancia de Azure IoT Hub a Raspberry Pi 3. Los mensajes controlan el comportamiento de encendido y apagado del LED que está conectado a la Pi. Una aplicación de ejemplo está preparada para que pueda llevar a cabo esta tarea.

Complete las lecciones 1, 2 y 3 antes de iniciar esta.

### <a name="run-the-sample-application-to-receive-cloud-to-device-messages"></a>Ejecución de la aplicación de ejemplo para recibir mensajes de nube a dispositivo
La aplicación de ejemplo de la lección 4 se ejecuta en la Pi y supervisa los mensajes entrantes del centro de IoT. Una nueva tarea de Gulp envía mensajes a Pi desde su centro de IoT para que parpadee el LED.

*Tiempo estimado para completar el tutorial: 10 minutos*

Vaya a [Ejecución de la aplicación de ejemplo para recibir mensajes de nube a dispositivo](iot-hub-raspberry-pi-kit-node-lesson4-send-cloud-to-device-messages.md).

### <a name="optional-section-change-the-on-and-off-behavior-of-the-led"></a>Sección opcional: Cambio del comportamiento de encendido y apagado del LED
Personalice los mensajes para cambiar el comportamiento de encendido y apagado del LED.

*Tiempo estimado para completar el tutorial: 10 minutos*

Vaya a [Sección opcional: Cambio del comportamiento de encendido y apagado del LED](iot-hub-raspberry-pi-kit-node-lesson4-change-led-behavior.md).

## <a name="troubleshooting"></a>Solución de problemas
Si tiene algún problema durante las lecciones, puede encontrar soluciones en el artículo [Troubleshooting](iot-hub-raspberry-pi-kit-node-troubleshooting.md) (Solución de problemas).


