---
title: 'Azure IoT Hub: empezar a conectar dispositivos de IoT con la nube | Microsoft Docs'
description: "Obtenga información sobre cómo conectar dispositivos de IoT con Azure IoT Hub. Los dispositivos pueden enviar telemetría a IoT Hub y este servicio puede supervisar y administrar los dispositivos."
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
ms.date: 04/28/2017
ms.author: dobett
ms.translationtype: Human Translation
ms.sourcegitcommit: 44eac1ae8676912bc0eb461e7e38569432ad3393
ms.openlocfilehash: 64b09448149b7e1efd8b76663a27b6c7ec1eb3c7
ms.contentlocale: es-es
ms.lasthandoff: 05/17/2017


---
# <a name="azure-iot-hub-get-started-tutorials"></a>Tutoriales de introducción de Azure IoT Hub

Puede usar Azure IoT Hub y los SDK de dispositivo IoT de Azure para crear soluciones de Internet de las cosas (IoT).

* Azure IoT Hub es un servicio completamente administrado en la nube que conecta, supervisa y administra los dispositivos IoT de forma segura. Use los SDK de dispositivo IoT de Azure para implementar los dispositivos de IoT.
* Use una puerta de enlace de IoT en escenarios de IoT más complejos donde sea necesario tener en cuenta factores como dispositivos heredados, costos de ancho de banda, directivas de seguridad y privacidad o procesamiento de datos perimetrales. En estos escenarios, se usa Azure IoT Edge para implementar una puerta de enlace que conecta dispositivos con IoT Hub.

## <a name="what-the-tutorials-cover"></a>¿Qué cubren los tutoriales?

Estos tutoriales le presentan Azure IoT Hub y los SDK de dispositivo. Los tutoriales cubren escenarios comunes de IoT para demostrar las funcionalidades de IoT Hub. Los tutoriales también muestran cómo combinar IoT Hub con otras herramientas y servicios de Azure para crear soluciones de IoT más eficaces. En los tutoriales, puede usar dispositivos IoT simulados o reales. Además, puede obtener información sobre cómo usar una puerta de enlace para habilitar dispositivos para conectarse a su IoT Hub.

## <a name="device-setup-scenario-connect-iot-device-or-gateway-to-azure-iot-hub"></a>Escenario de instalación de dispositivo: conectar un dispositivo puerta de enlace IoT a Azure IoT Hub

Puede elegir un dispositivo real o simulado para empezar a trabajar.

| Dispositivo IoT                       | Lenguaje de programación |
|---------------------------------|----------------------|
| Raspberry Pi                    | [Node.js][Pi_Nd], [C][Pi_C]           |
| Intel Edison                    | [Node.js][Ed_Nd], [C][Ed_C]           |
| Adafruit Feather HUZZAH ESP8266 | [Arduino][Hu_Ard]              |
| Sparkfun ESP8266 Thing Dev      | [Arduino][Th_Ard]              |
| Adafruit Feather M0             | [Arduino][M0_Ard]              |
| Dispositivo simulado                | [.NET][Sim_NET], [Java][Sim_Jav], [Node.js][Sim_Nd] y [Python][Sim_Pyth]              |

Además, puede usar una puerta de enlace de IoT Edge para habilitar dispositivos para conectarse a su IoT Hub.

| Dispositivo de puerta de enlace               | Lenguaje de programación | Plataforma         |
|------------------------------|----------------------|------------------|
| Intel NUC (modelo DE3815TYKE) | C                    | [Wind River Linux][NUC_Lnx] |
| Puerta de enlace simulada            | C                    | [Linux][Sim_Lnx], [Windows][Sim_Win] |

## <a name="extended-iot-scenarios-use-other-azure-services-and-tools"></a>Escenarios de IoT extendidos: usar otras herramientas y servicios de Azure

Cuando el dispositivo se ha conectado a IoT Hub, puede explorar escenarios adicionales que utilizan otras herramientas y servicios de Azure:

| Escenario                                    | Herramienta o servicio de Azure              |
|---------------------------------------------|------------------------------------|
| [Administración de mensajes de IoT Hub][Mg_IoT_Hub_Msg]                    | herramienta iothub-explorer               |
| [Administración de su dispositivo IoT][Mg_IoT_Dv]               | herramienta iothub-explorer               |
| [Guardado de los mensajes de IoT Hub en Azure Storage][Sv_IoT_Msg_Stor]                      | Almacenamiento de tablas de Azure               |
| [Visualización de datos de sensor][Vis_Data]             | Microsoft Power BI, Azure Web Apps |
| [Previsión meteorológica con datos del sensor][Weather_Forecast] | Aprendizaje automático de Azure             |
| [Detección de anomalías automática y reacción][Anomaly_Detect]    | Azure Logic Apps                   |

## <a name="next-steps"></a>Pasos siguientes

Cuando se hayan completado estos tutoriales, puede explorar aún más las funcionalidades de IoT Hub mediante la [guía del desarrollador][lnk-dev-guide]. Puede encontrar más tutoriales en la sección [Cómo][lnk-how-to].


[Pi_Nd]: iot-hub-raspberry-pi-kit-node-get-started.md
[Pi_C]: iot-hub-raspberry-pi-kit-c-get-started.md
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
[Mg_IoT_Hub_Msg]: iot-hub-explorer-cloud-device-messaging.md
[Mg_IoT_Dv]: iot-hub-device-management-iothub-explorer.md
[Sv_IoT_Msg_Stor]: iot-hub-store-data-in-azure-table-storage.md
[Vis_Data]: iot-hub-live-data-visualization-in-power-bi.md
[Weather_Forecast]: iot-hub-weather-forecast-machine-learning.md
[Anomaly_Detect]: iot-hub-monitoring-notifications-with-azure-logic-apps.md
[lnk-dev-guide]: iot-hub-devguide.md
[lnk-how-to]: iot-hub-how-to.md
