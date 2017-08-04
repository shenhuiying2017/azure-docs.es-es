---
title: 'Raspberry PI a la nube simulado (Node.js): Conectar el simulador web de Raspberry PI a Azure IoT Hub | Microsoft Docs'
description: "Aprenda a configurar y conectar el simulador de Raspberry Pi a Azure IoT Hub para que envíe datos a la plataforma en la nube de Azure. No se necesita una placa física para este tutorial."
services: iot-hub
documentationcenter: 
author: shizn
manager: timlt
tags: 
keywords: "simulador de raspberry pi, azure iot raspberry pi, raspberry pi iot hub, raspberry pi envía datos a la nube, raspberry pi a la nube"
ms.service: iot-hub
ms.devlang: node
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 7/7/2017
ms.author: xshi
ms.translationtype: HT
ms.sourcegitcommit: bde1bc7e140f9eb7bb864c1c0a1387b9da5d4d22
ms.openlocfilehash: b76c64b677a1a57a032cb71d2922778e7cf700c8
ms.contentlocale: es-es
ms.lasthandoff: 07/21/2017

---

# <a name="connect-raspberry-pi-online-simulator-to-azure-iot-hub-nodejs"></a>Conectar el simulador en línea de Raspberry Pi a Azure IoT Hub (Node.js)

[!INCLUDE [iot-hub-get-started-device-selector](../../includes/iot-hub-get-started-device-selector.md)]

En este tutorial, empezará por aprender los principios básicos del uso del simulador en línea de Raspberry Pi. A continuación, aprenderá a conectar sin problemas el simulador Pi a la nube con [Azure IoT Hub](iot-hub-what-is-iot-hub.md). 

Si tiene dispositivos físicos, visite [Conectar Raspberry Pi a Azure IoT Hub](iot-hub-raspberry-pi-kit-node-get-started.md) para comenzar. 

## <a name="what-you-do"></a>Qué debe hacer

* Conozca los aspectos fundamentales del simulador en línea de Raspberry Pi.
* Cree un IoT Hub.
* Registre un dispositivo para Pi en IoT Hub.
* Ejecuta una aplicación de ejemplo en PI para enviar datos de sensor simulados a IoT Hub.

Conecte Raspberry Pi simulado al IoT Hub que ha creado. A continuación, ejecute una aplicación de ejemplo con el simulador para generar datos de sensor. Por último, envíe los datos del sensor a IoT Hub.

## <a name="what-you-learn"></a>Conocimientos que adquirirá

* Cómo crear Azure IoT Hub y obtener la cadena de conexión del nuevo dispositivo. Si no tiene ninguna cuenta de Azure, [cree una cuenta de evaluación gratuita de Azure](https://azure.microsoft.com/free/) en solo unos minutos.
* Cómo trabajar con el simulador en línea de Raspberry Pi.
* Cómo enviar los datos del sensor a IoT Hub.

## <a name="overview-of-raspberry-pi-web-simulator"></a>Introducción al simulador web de Raspberry Pi

Haga clic en el botón para iniciar el simulador en línea de Raspberry Pi.

> [!div class="button"]
[Iniciar simulador de Raspberry Pi](https://azure-samples.github.io/raspberry-pi-web-simulator/)

Hay tres áreas en el simulador web.
* Área de ensamblado: el circuito predeterminado es que Pi se conecta con un sensor BME280 y un LED. Esta área está bloqueada en la versión preliminar por lo que actualmente no puede hacer ninguna personalización.
* Área de codificación: un editor de código en línea para codificar con Raspberry Pi. La aplicación de ejemplo predeterminada ayuda a recopilar datos de sensor desde el sensor BME280 y los envía a Azure IoT Hub. La aplicación es totalmente compatible con dispositivos Pi reales. 
* Ventana de consola integrada: muestra la salida del código. En la parte superior de esta ventana aparecen tres botones.
   * **Run** (Ejecutar): ejecute la aplicación en el área de codificación.
   * **Reset** (Restablecer): restablezca el área de codificación a la aplicación de ejemplo predeterminada.
   * **Fold/Expand** (Plegar/expandir): al lado derecho hay un botón para plegar o expandir la ventana de consola.

> [!NOTE] 
El simulador web de Raspberry Pi ahora está disponible en versión preliminar. Nos gustaría oír su voz en la [sala de chat de Gitter](https://gitter.im/Microsoft/raspberry-pi-web-simulator). El código fuente es público en [Github](https://github.com/Azure-Samples/raspberry-pi-web-simulator).

![Introducción al simulador en línea de Pi](media/iot-hub-raspberry-pi-web-simulator/0_overview.png)

[!INCLUDE [iot-hub-get-started-create-hub-and-device](../../includes/iot-hub-get-started-create-hub-and-device.md)]


## <a name="run-a-sample-application-on-pi-web-simulator"></a>Ejecución de una aplicación de ejemplo en el simulador web de Pi

1. En el área de codificación, asegúrese de trabajar en la aplicación de ejemplo predeterminada. Reemplace el marcador de posición de la línea 15 con la cadena de conexión del dispositivo de Azure IoT Hub.
   ![Reemplazo de la cadena de conexión de dispositivo](media/iot-hub-raspberry-pi-web-simulator/1_connectionstring.png)

2. Haga clic en **Ejecutar** o escriba `npm start` para ejecutar la aplicación.


Debería ver el resultado siguiente que muestra los datos de sensor y los mensajes que se envían a IoT Hub ![Resultado: datos de sensor enviados desde Raspberry Pi a IoT Hub](media/iot-hub-raspberry-pi-web-simulator/2_run_application.png)


## <a name="next-steps"></a>Pasos siguientes

Ha ejecutado una aplicación de ejemplo para recopilar datos de sensor y enviarlos a IoT Hub.

[!INCLUDE [iot-hub-get-started-next-steps](../../includes/iot-hub-get-started-next-steps.md)]

