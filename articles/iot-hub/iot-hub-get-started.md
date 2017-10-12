---
title: 'Azure IoT Hub: empezar a conectar dispositivos de IoT con la nube | Microsoft Docs'
description: "Aprenda a conectar paneles y kits de inicio de IoT con Azure IoT Hub. Los dispositivos pueden enviar datos de telemetría a IoT Hub y este servicio supervisa y administra los dispositivos."
services: iot-hub
documentationcenter: 
author: dominicbetts
manager: timlt
editor: 
keywords: tutorial de azure iot hub
ms.assetid: 24376318-5344-4a81-a1e6-0003ed587d53
ms.service: iot-hub
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/22/2017
ms.author: dobett
ms.openlocfilehash: 45016e6383761ffe78f13ccef1112ab3d9753498
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="azure-iot-hub-get-started-tutorials"></a>Tutoriales de introducción de Azure IoT Hub

Puede usar Azure IoT Hub y los SDK de dispositivo IoT de Azure para crear soluciones de Internet de las cosas (IoT):

* Azure IoT Hub es un servicio completamente administrado en la nube que conecta, supervisa y administra los dispositivos IoT de forma segura. Use los SDK de dispositivo IoT de Azure para implementar los dispositivos de IoT.
* Use una puerta de enlace de IoT en escenarios IoT más complejos. Por ejemplo, donde sea necesario tener en cuenta factores como dispositivos antiguos, costos de ancho de banda, directivas de seguridad y privacidad o procesamiento de datos perimetrales. En estos escenarios, se usa Azure IoT Edge para implementar una puerta de enlace que conecta dispositivos con IoT Hub.

## <a name="what-the-tutorials-cover"></a>¿Qué cubren los tutoriales?

Estos tutoriales le presentan Azure IoT Hub y los SDK de dispositivo. Los tutoriales cubren escenarios comunes de IoT para demostrar las funcionalidades de IoT Hub. Los tutoriales también muestran cómo combinar IoT Hub con otras herramientas y servicios de Azure para crear soluciones de IoT más eficaces. En los tutoriales, puede usar dispositivos IoT simulados o reales. Además, puede obtener información sobre cómo usar una puerta de enlace para habilitar dispositivos para conectarse a su IoT Hub.

## <a name="set-up-your-device"></a>Configuración del dispositivo

Conecte un dispositivo IoT o una puerta de enlace a Azure IoT Hub. Puede elegir un dispositivo físico o simulado para empezar a trabajar:

| Dispositivo IoT                       | Lenguaje de programación |
|----------------------------------|----------------------|
| Raspberry Pi                     | [Python][Pi_Py], [Node.js][Pi_Nd], [C][Pi_C]  |
| IoT DevKit                       | [Arduino en VSCode][DevKit]     |
| Intel Edison                     | [Node.js][Ed_Nd], [C][Ed_C]    |
| Adafruit Feather HUZZAH ESP8266  | [Arduino][Hu_Ard]              |
| Sparkfun ESP8266 Thing Dev       | [Arduino][Th_Ard]              |
| Adafruit Feather M0              | [Arduino][M0_Ard]              |
| Dispositivo simulado en PC           | [.NET][Sim_NET], [Java][Sim_Jav], [Node.js][Sim_Nd] y [Python][Sim_Pyth] |
| Simulador de dispositivos en línea         | [Raspberry Pi (Node.js)][Ol_Sim] |

Además, puede usar una puerta de enlace de IoT Edge para habilitar dispositivos para conectarse a su instancia de IoT Hub:

| Dispositivo de puerta de enlace               | Lenguaje de programación | Plataforma         |
|------------------------------|----------------------|------------------|
| Intel NUC (modelo DE3815TYKE) | C                    | [Wind River Linux][NUC_Lnx] |
| Puerta de enlace simulada            | C                    | [Linux][Sim_Lnx], [Windows][Sim_Win] |

[!INCLUDE [iot-hub-get-started-extended](../../includes/iot-hub-get-started-extended.md)]

[Pi_Nd]: iot-hub-raspberry-pi-kit-node-get-started.md
[Pi_C]: iot-hub-raspberry-pi-kit-c-get-started.md
[Pi_Py]: iot-hub-raspberry-pi-kit-python-get-started.md
[DevKit]: iot-hub-arduino-iot-devkit-az3166-get-started.md
[Ed_Nd]: iot-hub-intel-edison-kit-node-get-started.md
[Ed_C]: iot-hub-intel-edison-kit-c-get-started.md
[Hu_Ard]: iot-hub-arduino-huzzah-esp8266-get-started.md
[Th_Ard]: iot-hub-sparkfun-esp8266-thing-dev-get-started.md
[M0_Ard]: iot-hub-adafruit-feather-m0-wifi-kit-arduino-get-started.md
[Sim_NET]: iot-hub-csharp-csharp-getstarted.md
[Sim_Jav]: iot-hub-java-java-getstarted.md
[Sim_Nd]: iot-hub-node-node-getstarted.md
[Sim_Pyth]: iot-hub-python-getstarted.md
[NUC_Lnx]: iot-hub-gateway-kit-c-lesson1-set-up-nuc.md
[Sim_Lnx]: iot-hub-linux-iot-edge-get-started.md
[Sim_Win]: iot-hub-windows-iot-edge-get-started.md
[Ol_Sim]: iot-hub-raspberry-pi-web-simulator-get-started.md
