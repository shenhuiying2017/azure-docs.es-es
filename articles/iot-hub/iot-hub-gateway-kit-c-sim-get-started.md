---
title: "Dispositivo simulado y puerta de enlace de Azure IoT: Introducción | Microsoft Docs"
description: Comience con el kit de inicio de puerta de enlace de IoT, cree la instancia de IoT Hub de Azure y conecte la puerta de enlace a la instancia de IoT Hub
services: iot-hub
documentationcenter: 
author: shizn
manager: timtl
tags: 
keywords: "iot hub de azure, puerta de enlace de iot, introducción con internet de las cosas, kit de herramientas de iot"
ROBOTS: NOINDEX
redirect_url: /azure/iot-hub/iot-hub-gateway-kit-c-lesson1-set-up-nuc
ms.assetid: 0c110b8b-bee4-4aec-a18a-dfc292aa17a3
ms.service: iot-hub
ms.devlang: c
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 3/21/2017
ms.author: xshi
translationtype: Human Translation
ms.sourcegitcommit: 61e9a9fc7876094c04238c61cfc38efdd97b05f7
ms.openlocfilehash: 06161c2950fbe99d2e4f9c1b416695a5f9f18ca4
ms.lasthandoff: 01/25/2017


---

# <a name="get-started-with-iot-gateway-starter-kit-with-a-simulated-device"></a>Introducción con el kit de inicio de puerta de enlace de IoT con un dispositivo simulado

> [!div class="op_single_selector"]
> * [SensorTag](iot-hub-gateway-kit-c-get-started.md)
> * [Dispositivo simulado](iot-hub-gateway-kit-c-sim-get-started.md)

En este tutorial, empezará por aprender los conceptos básicos sobre cómo trabajar con el [kit de inicio de puerta de enlace de IoT](https://aka.ms/gateway-kit). Va a trabajar con Intel NUC que ejecuta Wind River Linux. Aprenderá a conectar fácilmente los dispositivos a la nube mediante IoT Hub de Azure.

***
**¿Aún no tiene el kit?:** haga clic [aquí](https://aka.ms/gateway-kit).
***

## <a name="lesson-1-configure-your-nuc"></a>Lección 1: Configuración de NUC
![Diagrama integral de la lección&1;](media/iot-hub-gateway-kit-lessons/e2e-sim-Lesson1.png)

En esta lección, se configura Intel NUC (Next Unit of Computing) en el kit como una puerta de enlace de IoT de Azure, se instala el paquete del SDK de puerta de enlace de IoT de Azure en NUC y se ejecuta una aplicación de ejemplo para comprobar la funcionalidad de la puerta de enlace.

*Tiempo estimado para completar el tutorial: 15 minutos*

Vaya a [Configuración de Intel NUC como puerta de enlace de IoT](iot-hub-gateway-kit-c-sim-lesson1-set-up-nuc.md) (Configuración de Intel NUC como una puerta de enlace de IoT).

## <a name="lesson-2-create-your-iot-hub"></a>Lección 2: Creación de la instancia de IoT Hub
![Diagrama integral de la lección&2;](media/iot-hub-gateway-kit-lessons/e2e-sim-Lesson2.png)

En esta lección, se instalan las herramientas y el software en el equipo host. Debe crear una cuenta gratuita de Azure, aprovisionar la instancia de IoT Hub de Azure y crear el primer dispositivo en IoT Hub.

Complete la lección 1 antes de iniciar esta.

### <a name="get-the-tools"></a>Obtener las herramientas
Instale las herramientas y el software en el equipo host.

*Tiempo estimado para completar el tutorial: 20 minutos*

Vaya a [Obtener las herramientas](iot-hub-gateway-kit-c-sim-lesson2-get-the-tools-win32.md).

### <a name="create-an-iot-hub-and-register-your-device"></a>Cree una instancia de IoT Hub y registre el dispositivo
Cree el grupo de recursos, aprovisione la primera instancia de IoT Hub de Azure y agregue el primer dispositivo a dicha instancia mediante la CLI de Azure.

*Tiempo estimado para completar el tutorial: 10 minutos*

Vaya a [Creación de una instancia de IoT Hub y registro del dispositivo](iot-hub-gateway-kit-c-sim-lesson2-register-device.md).

## <a name="lesson-3-receive-messages-from-the-simulated-device-and-read-messages-from-your-iot-hub"></a>Lección 3: Recepción de mensajes del dispositivo simulado y lectura de estos en IoT Hub
En esta lección, usará scripts para automatizar la configuración y la ejecución de una aplicación de dispositivo simulado en la puerta de enlace. La aplicación de dispositivo simulado genera datos de temperatura de ejemplo y los envía a un módulo de IoT Hub. El módulo de IoT Hub empaqueta los datos recibidos y los envía a la instancia de IoT Hub a través del marco de la puerta de enlace proporcionado en el SDK de puerta de enlace de IoT de Azure.

![Diagrama integral de la lección 3](media/iot-hub-gateway-kit-lessons/e2e-sim-Lesson3.png)

### <a name="configure-and-run-a-simulated-device"></a>Configuración y ejecución de un dispositivo simulado
Prepare los códigos de ejemplo. Luego, configure y ejecute la aplicación de ejemplo del dispositivo simulado.

*Tiempo estimado para completar el tutorial: 15 minutos*

Vaya a [Configuración y ejecución de un dispositivo simulado](iot-hub-gateway-kit-c-sim-lesson3-configure-simulated-device-app.md)

### <a name="read-messages-from-your-iot-hub"></a>Lectura de mensajes en IoT Hub
Ejecute el código de ejemplo en el equipo host para leer los mensajes en IoT Hub.

*Tiempo estimado para completar el tutorial: 15 minutos*

Vaya a [Lectura de mensajes en IoT Hub](iot-hub-gateway-kit-c-sim-lesson3-read-messages-from-hub.md)

## <a name="lesson-4-save-messages-to-azure-table-storage"></a>Lección 4: Almacenamiento de mensajes en Azure Table Storage
Cree una instancia de Azure Function App que recopile los mensajes entrantes de la instancia de IoT Hub y los escriba en Azure Table Storage.

![Diagrama integral de la lección 4](media/iot-hub-gateway-kit-lessons/e2e-sim-Lesson4.png)

### <a name="create-an-azure-function-app-and-azure-storage-account"></a>Creación de una cuenta de Azure Storage y una aplicación de Azure Function
Use una plantilla de Azure Resource Manager para crear una aplicación de Azure Function y una cuenta de Azure Storage.

*Tiempo estimado para completar el tutorial: 10 minutos*

Vaya a [Creación de una instancia de Azure Function App y una cuenta de Azure Storage](iot-hub-gateway-kit-c-sim-lesson4-deploy-resource-manager-template.md).

### <a name="read-messages-persisted-in-azure-table-storage"></a>Lectura de los mensajes que se conservan en Azure Table Storage
Supervise los mensajes de la puerta de enlace a la nube a medida que se escriben en Azure Table Storage.

*Tiempo estimado para completar el tutorial: 5 minutos*

Vaya a [Lectura de los mensajes que se conservan en Azure Table Storage](iot-hub-gateway-kit-c-sim-lesson4-read-table-storage.md) (Lectura de los mensajes que se conservan en Azure Table Storage).

## <a name="troubleshooting"></a>Solución de problemas
Si tiene algún problema durante las lecciones, puede encontrar soluciones en el artículo [Troubleshooting](iot-hub-gateway-kit-c-sim-troubleshooting.md) (Solución de problemas).

## <a name="explore-more"></a>Explorar más
Visite la [zona de desarrollador del Kit de puerta de enlace de IoT de Intel](https://software.intel.com/en-us/iot/hardware/gateways/dev-kit) para obtener más información.
