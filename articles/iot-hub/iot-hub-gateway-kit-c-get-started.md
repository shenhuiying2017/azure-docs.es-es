---
title: "Dispositivo SensorTag y puerta de enlace de Azure IoT: Introducción | Microsoft Docs"
description: Comience con el kit de inicio de puerta de enlace de IoT, cree el centro de IoT de Azure y conecte SensorTag y la puerta de enlace al centro de IoT
services: iot-hub
documentationcenter: 
author: shizn
manager: timtl
tags: 
keywords: "azure iot hub, puerta de enlace de iot, introducción con internet de las cosas, kit de herramientas de iot"
ROBOTS: NOINDEX
redirect_url: /azure/iot-hub/iot-hub-gateway-kit-c-lesson1-set-up-nuc
ms.assetid: 56d05f4e-f2c1-4b22-8701-f01e14deead6
ms.service: iot-hub
ms.devlang: c
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 3/21/2017
ms.author: xshi
ms.translationtype: Human Translation
ms.sourcegitcommit: 71fea4a41b2e3a60f2f610609a14372e678b7ec4
ms.openlocfilehash: 624bdc7877d5048da08897f868272fd8e8f3f7b6
ms.contentlocale: es-es
ms.lasthandoff: 05/10/2017


---

# <a name="get-started-with-iot-gateway-starter-kit-with-a-sensortag"></a>Introducción con el kit de inicio de puerta de enlace de IoT mediante un dispositivo SensorTag

> [!div class="op_single_selector"]
> * [SensorTag](iot-hub-gateway-kit-c-get-started.md)
> * [Dispositivo simulado](iot-hub-gateway-kit-c-sim-get-started.md)

En este tutorial, empiece por aprender los conceptos básicos sobre cómo trabajar con el [kit de inicio de puerta de enlace de IoT](https://aka.ms/gateway-kit). Va a trabajar con Intel NUC que ejecuta Wind River Linux y [SensorTag de TI](http://www.ti.com/ww/en/wireless_connectivity/sensortag2015/index.html#main). Aprenderá a conectar fácilmente los dispositivos a la nube mediante Azure IoT Hub.

***
**¿Aún no tiene el kit?:** haga clic [aquí](https://aka.ms/gateway-kit). **¿No tiene un dispositivo SensorTag?:** [Empiece con un dispositivo simulado](iot-hub-gateway-kit-c-sim-get-started.md) o [compre un dispositivo SensorTag](http://www.ti.com/ww/en/wireless_connectivity/sensortag2015/?INTC=SensorTag&HQS=sensortag)
***

## <a name="lesson-1-configure-your-nuc"></a>Lección 1: Configuración de NUC
![Diagrama integral de la lección 1](media/iot-hub-gateway-kit-lessons/e2e-lesson1.png)

En esta lección, se configura Intel NUC (Next Unit of Computing) en el kit como una puerta de enlace de IoT de Azure, se instala el paquete de Azure IoT Edge en NUC y se ejecuta una aplicación de ejemplo para comprobar la funcionalidad de la puerta de enlace.

*Tiempo estimado para completar el tutorial: 15 minutos*

Vaya a [Configuración de Intel NUC como puerta de enlace de IoT](iot-hub-gateway-kit-c-lesson1-set-up-nuc.md) (Configuración de Intel NUC como una puerta de enlace de IoT).

## <a name="lesson-2-create-your-iot-hub"></a>Lección 2: Creación de la instancia de IoT Hub
![Diagrama integral de la lección 2](media/iot-hub-gateway-kit-lessons/e2e-lesson2.png)

En esta lección, se instalan las herramientas y el software en el equipo host. Debe crear una cuenta gratuita de Azure, aprovisionar la instancia de IoT Hub de Azure y crear el primer dispositivo en IoT Hub.

Complete la lección 1 antes de iniciar esta.

### <a name="get-the-tools"></a>Obtener las herramientas
Instale las herramientas y el software en el equipo host.

*Tiempo estimado para completar el tutorial: 20 minutos*

Vaya a [Obtener las herramientas](iot-hub-gateway-kit-c-lesson2-get-the-tools-win32.md).

### <a name="create-an-iot-hub-and-register-your-device"></a>Cree una instancia de IoT Hub y registre el dispositivo
Cree el grupo de recursos, aprovisione la primera instancia de IoT Hub de Azure y agregue el primer dispositivo a dicha instancia mediante la CLI de Azure.

*Tiempo estimado para completar el tutorial: 10 minutos*

Vaya a [Creación de un IoT Hub y registro del dispositivo](iot-hub-gateway-kit-c-lesson2-register-device.md).

## <a name="lesson-3-receive-messages-from-sensortag-and-read-messages-from-your-iot-hub"></a>Lección 3: Recibir mensajes de SensorTag y leerlos en IoT Hub
En esta lección, usará scripts para automatizar la configuración y ejecución de una aplicación de ejemplo BLE en la puerta de enlace. Dichas aplicaciones utilizan una colección de módulos para agregar y transformar datos, procesar comandos o realizar una serie de tareas relacionadas. Los módulos se comunican entre sí a través de un agente de mensajes. La aplicación de ejemplo tiene un módulo de BLE y un módulo de IoT Hub. El módulo BLE recibe datos de SensorTag para BLE. El módulo de IoT Hub empaqueta los datos recibidos y los envía a su IoT Hub a través del marco de la puerta de enlace proporcionado en Azure IoT Edge.

![Diagrama integral de la lección 3](media/iot-hub-gateway-kit-lessons/e2e-lesson3.png)

### <a name="configure-and-run-the-ble-sample-app"></a>Configuración y ejecución de la aplicación de ejemplo BLE
Configure la conectividad entre SensorTag y la puerta de enlace. Complete la configuración y ejecute la aplicación de ejemplo BLE.

*Tiempo estimado para completar el tutorial: 15 minutos*

Vaya a [Configure and run the BLE sample app](iot-hub-gateway-kit-c-lesson3-configure-ble-app.md) (Configuración y ejecución de la aplicación de ejemplo BLE).

### <a name="read-messages-from-your-iot-hub"></a>Lectura de mensajes en IoT Hub
Ejecute el código de ejemplo en el equipo host para leer los mensajes en IoT Hub.

*Tiempo estimado para completar el tutorial: 15 minutos*

Vaya a [Lectura de mensajes en IoT Hub](iot-hub-gateway-kit-c-lesson3-read-messages-from-hub.md)

## <a name="lesson-4-save-messages-to-azure-table-storage"></a>Lección 4: Almacenamiento de mensajes en Azure Table Storage
Cree una instancia de Azure Function App que recopile los mensajes entrantes de la instancia de IoT Hub y los escriba en Azure Table Storage.

![Diagrama integral de la lección 4](media/iot-hub-gateway-kit-lessons/e2e-lesson4.png)

### <a name="create-an-azure-function-app-and-azure-storage-account"></a>Creación de una cuenta de Azure Storage y una aplicación de Azure Function
Use una plantilla de Azure Resource Manager para crear una aplicación de Azure Function y una cuenta de Azure Storage.

*Tiempo estimado para completar el tutorial: 10 minutos*

Vaya a [Creación de una instancia de Azure Function App y una cuenta de Azure Storage](iot-hub-gateway-kit-c-lesson4-deploy-resource-manager-template.md).

### <a name="read-messages-persisted-in-azure-table-storage"></a>Lectura de los mensajes que se conservan en Azure Table Storage
Supervise los mensajes de la puerta de enlace a la nube a medida que se escriben en Azure Table Storage.

*Tiempo estimado para completar el tutorial: 5 minutos*

Vaya a [Lectura de los mensajes que se conservan en Azure Table Storage](iot-hub-gateway-kit-c-lesson4-read-table-storage.md) (Lectura de los mensajes que se conservan en Azure Table Storage).

## <a name="troubleshooting"></a>Solución de problemas
Si tiene algún problema durante las lecciones, puede encontrar soluciones en el artículo [Troubleshooting](iot-hub-gateway-kit-c-troubleshooting.md) (Solución de problemas).

## <a name="explore-more"></a>Explorar más
Visite la [zona de desarrollador del Kit de puerta de enlace de IoT de Intel](http://software.intel.com/iot/microsoft-azure) para obtener más información.
