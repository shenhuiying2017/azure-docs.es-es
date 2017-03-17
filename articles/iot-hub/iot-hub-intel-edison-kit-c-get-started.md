---
title: "Conexión de Intel Edison (C) a Azure IoT: Introducción | Microsoft Docs"
description: "En este artículo se explica cómo empezar a trabajar con Intel Edison, crear la instancia de IoT Hub de Azure y conectar Edison a IoT Hub."
services: iot-hub
documentationcenter: 
author: shizn
manager: timtl
tags: 
keywords: "desarrollo de Intel Edison, Azure IoT Hub, introducción a Internet de las cosas, tutorial de Internet de las cosas, Adafruit e Internet de las cosas, Intel Edison Arduino, introducción a Arduino"
ms.assetid: 4885fa2c-c2ee-4253-b37f-ccd55f92b006
ms.service: iot-hub
ms.devlang: c
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/7/2016
ms.author: xshi
ms.custom: H1Hack27Feb2017
translationtype: Human Translation
ms.sourcegitcommit: 7adde91586f5fbbffd0aeaf0efb0810cc891ac0b
ms.openlocfilehash: 248bc4c309ed61ae00ef144ebdb4c820f285783d
ms.lasthandoff: 03/02/2017


---
# <a name="connect-your-intel-edison-device-to-your-iot-hub-using-c"></a>Conexión de un dispositivo Intel Edison a su IoT Hub mediante C
> [!div class="op_single_selector"]
> * [Node.JS](iot-hub-intel-edison-kit-node-get-started.md)
> * [C](iot-hub-intel-edison-kit-c-get-started.md)

En este tutorial, empiece por aprender los conceptos básicos sobre cómo trabajar con Intel Edison. A continuación, aprenderá a conectar sin problemas los dispositivos en la nube con [Azure IoT Hub](iot-hub-what-is-iot-hub.md).

¿Aún no tiene un kit? Comience [aquí](https://azure.microsoft.com/develop/iot/starter-kits)

## <a name="lesson-1-configure-your-device"></a>Lección 1: Configuración del dispositivo
![Diagrama integral de la lección 1](media/iot-hub-intel-edison-lessons/e2e-lesson1.png)

En esta lección, configurará su Intel Edison con un sistema operativo, configurará el entorno de desarrollo e implementará una aplicación en Edison.

### <a name="configure-your-device"></a>Configuración del dispositivo
Configure Intel Edison para usarlo por primera vez ensamblando la placa, encendiéndola e instalando la herramienta de configuración en el sistema operativo de escritorio. El objetivo es actualizar el firmware de Edison, establecer su contraseña y conectarlo a una red Wi-Fi.  

*Tiempo estimado para completar el tutorial: 30 minutos*

Vaya a [Configuración del dispositivo][configure-your-device].

### <a name="get-the-tools"></a>Obtener las herramientas
Descargue las herramientas y el software para compilar e implementar su primera aplicación para Intel Edison.

*Tiempo estimado para completar el tutorial: 20 minutos*

Vaya a [Obtener las herramientas][get-the-tools].

### <a name="create-and-deploy-the-blink-application"></a>Creación e implementación de la aplicación de intermitencia
Clone la aplicación de intermitencia de ejemplo de GitHub y use Gulp para implementar esta aplicación en la placa Intel Edison. Esta aplicación de ejemplo hace parpadear el LED conectado a la placa cada dos segundos.

*Tiempo estimado para completar el tutorial: 5 minutos*

Vaya a [Creación e implementación de la aplicación de intermitencia][create-and-deploy-the-blink-application].

## <a name="lesson-2-create-your-iot-hub"></a>Lección 2: Creación de la instancia de IoT Hub
![Diagrama integral de la lección 2](media/iot-hub-intel-edison-lessons/e2e-lesson2.png)

En esta lección, creará una cuenta gratuita de Azure, aprovisionará la instancia de IoT Hub de Azure y creará su primer dispositivo en el centro de IoT Hub.

Complete la lección 1 antes de iniciar esta.

### <a name="get-the-azure-tools"></a>Obtención de las herramientas de Azure
Instalación de la interfaz de la línea de comandos de Azure (CLI de Azure)

*Tiempo estimado para completar el tutorial: 10 minutos*

Vaya a [Obtención de las herramientas de Azure][get-azure-tools].

### <a name="create-your-iot-hub-and-register-intel-edison"></a>Creación de un centro de IoT Hub y registro de Intel Edison
Cree el grupo de recursos, aprovisione la primera instancia de IoT Hub de Azure y agregue el primer dispositivo a dicha instancia mediante la CLI de Azure.

*Tiempo estimado para completar el tutorial: 10 minutos*

Vaya a [Creación de un centro de IoT Hub y registro de Intel Edison](iot-hub-intel-edison-kit-c-lesson2-prepare-azure-iot-hub.md).

## <a name="lesson-3-send-device-to-cloud-messages"></a>Lección 3: Envío de mensajes de dispositivo a nube
![Diagrama integral de la lección 3](media/iot-hub-intel-edison-lessons/e2e-lesson3.png)

En esta lección, enviará mensajes desde Intel Edison al centro de IoT Hub. También creará una aplicación de Azure Function que recoge los mensajes entrantes del centro de IoT y los escribe en Azure Table Storage.

Complete las lecciones 1 y 2 antes de iniciar esta.

### <a name="create-an-azure-function-app-and-azure-storage-account"></a>Creación de una cuenta de Azure Storage y una aplicación de Azure Function
Use una plantilla de Azure Resource Manager para crear una aplicación de Azure Function y una cuenta de Azure Storage.

*Tiempo estimado para completar el tutorial: 10 minutos*

Vaya a [Creación de una cuenta de Azure Storage y una aplicación de Azure Function][create-an-azure-function-app-and-azure-storage-account].

### <a name="run-a-sample-application-to-send-device-to-cloud-messages"></a>Ejecución de una aplicación de ejemplo para enviar mensajes de dispositivo a nube
Implemente y ejecute una aplicación de ejemplo para la placa Intel Edison que envía mensajes al centro de IoT Hub.

*Tiempo estimado para completar el tutorial: 10 minutos*

Vaya a [Ejecución de una aplicación de ejemplo para enviar mensajes de dispositivo a nube][send-device-to-cloud-messages].

### <a name="read-messages-persisted-in-azure-storage"></a>Lectura de los mensajes que se conservan en Azure Storage
Supervise los mensajes de dispositivo a nube a medida que se escriben en Azure Storage.

*Tiempo estimado para completar el tutorial: 5 minutos*

Vaya a [Lectura de los mensajes que se conservan en Azure Storage][read-messages-persisted-in-azure-storage].

## <a name="lesson-4-send-cloud-to-device-messages"></a>Lección 4. Envío de mensajes de nube a dispositivo
![Diagrama integral de la lección 4](media/iot-hub-intel-edison-lessons/e2e-lesson4.png)

En esta lección se explica cómo enviar mensajes desde la instancia de IoT Hub de Azure a Intel Edison. Los mensajes controlan el comportamiento de encendido y apagado del LED que está conectado a Edison. Una aplicación de ejemplo está preparada para que pueda llevar a cabo esta tarea.

Complete las lecciones 1, 2 y 3 antes de iniciar esta.

### <a name="run-the-sample-application-to-receive-cloud-to-device-messages"></a>Ejecución de la aplicación de ejemplo para recibir mensajes de nube a dispositivo
La aplicación de ejemplo de la lección 4 se ejecuta en Intel Edison y supervisa los mensajes entrantes del centro de IoT Hub. Una nueva tarea de Gulp envía mensajes a Intel Edison desde su centro de IoT Hub para que parpadee el LED.

*Tiempo estimado para completar el tutorial: 10 minutos*

Vaya a [Ejecución de la aplicación de ejemplo para recibir mensajes de nube a dispositivo][receive-cloud-to-device-messages].

### <a name="optional-section-change-the-on-and-off-behavior-of-the-led"></a>Sección opcional: Cambio del comportamiento de encendido y apagado del LED
Personalice los mensajes para cambiar el comportamiento de encendido y apagado del LED.

*Tiempo estimado para completar el tutorial: 10 minutos*

Vaya a [Sección opcional: Cambio del comportamiento de encendido y apagado del LED][change-the-on-and-off-behavior-of-the-led].

## <a name="troubleshooting"></a>Solución de problemas
Si tiene algún problema durante las lecciones, puede encontrar soluciones en el artículo [Solución de problemas][troubleshooting].
<!-- Images and links -->

[configure-your-device]: iot-hub-intel-edison-kit-c-lesson1-configure-your-device.md
[get-the-tools]: iot-hub-intel-edison-kit-c-lesson1-get-the-tools-win32.md
[create-and-deploy-the-blink-application]: iot-hub-intel-edison-kit-c-lesson1-deploy-blink-app.md
[get-azure-tools]: iot-hub-intel-edison-kit-c-lesson2-get-azure-tools-win32.md
[create-an-azure-function-app-and-azure-storage-account]: iot-hub-intel-edison-kit-c-lesson3-deploy-resource-manager-template.md
[send-device-to-cloud-messages]: iot-hub-intel-edison-kit-c-lesson3-run-azure-blink.md
[read-messages-persisted-in-azure-storage]: iot-hub-intel-edison-kit-c-lesson3-read-table-storage.md
[receive-cloud-to-device-messages]:iot-hub-intel-edison-kit-c-lesson4-send-cloud-to-device-messages.md
[change-the-on-and-off-behavior-of-the-led]: iot-hub-intel-edison-kit-c-lesson4-change-led-behavior.md
[troubleshooting]: iot-hub-intel-edison-kit-c-troubleshooting.md
