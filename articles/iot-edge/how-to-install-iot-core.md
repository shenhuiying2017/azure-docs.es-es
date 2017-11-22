---
title: "Instalación de Azure IoT Edge en IoT Core | Microsoft Docs"
description: "Instalación de la instancia de Azure IoT Edge en tiempo de ejecución en un dispositivo Windows IoT Core"
services: iot-edge
keywords: 
author: kgremban
manager: timlt
ms.author: kgremban
ms.reviewer: veyalla
ms.date: 11/15/2017
ms.topic: article
ms.service: iot-edge
ms.openlocfilehash: be2a80645d23e709d6c5cfb3978498bbe85eca34
ms.sourcegitcommit: 9a61faf3463003375a53279e3adce241b5700879
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/15/2017
---
# <a name="install-the-iot-edge-runtime-on-windows-iot-core---preview"></a>Instalación de la instancia de IoT Edge en tiempo de ejecución en un dispositivo Windows IoT Core (versión preliminar)

La instancia de Azure IoT Edge en tiempo de ejecución puede ejecutarse incluso en dispositivos de una sola placa (SBC) de tamaño reducido que son muy frecuentes en el sector de IoT. Este artículo lo guía a través de aprovisionamiento de la instancia en tiempo de ejecución en una placa de desarrollo [MinnowBoard Turbot][lnk-minnow] que ejecuta Windows IoT Core.

## <a name="install-the-runtime"></a>Instalación de la instancia en tiempo de ejecución

1. Instale el [panel de Windows 10 IoT Core] [ lnk-core] en un sistema host.
1. Siga los pasos de [Configurar el dispositivo][lnk-board] para configurar la placa con la imagen MinnowBoard Turbot/MAX Build 16299. 
1. Encienda el dispositivo y, luego, [inicie sesión de forma remota con PowerShell][lnk-powershell].
1. En la consola de PowerShell, [instale los archivos binarios de Docker][lnk-docker-install].
1. Ejecute el siguiente comando en la consola de PowerShell para instalar la instancia de IoT Edge en tiempo de ejecución y compruebe la configuración:

   ```powershell
   Invoke-Expression (Invoke-WebRequest -useb https://aka.ms/iotedgewin)
   ```

   El script proporciona lo siguiente: 
   * Python 3.6
   * El script de control de IoT Edge (iotedgectl.exe)

Puede ver resultados informativos de la herramienta iotedgectl.exe en rojo en la ventana de PowerShell remota. Esto no indica que haya necesariamente errores. 

## <a name="next-steps"></a>Pasos siguientes

Ahora que tiene un dispositivo que ejecuta la instancia de IoT Edge en tiempo de ejecución, obtenga información sobre cómo [implementar y supervisar los módulos de IoT Edge a escala][lnk-deploy].

<!--Links-->
[lnk-minnow]: https://minnowboard.org/ 
[lnk-core]: https://docs.microsoft.com/windows/iot-core/connect-your-device/iotdashboard
[lnk-board]: https://developer.microsoft.com/windows/iot/Docs/GetStarted/mbm/sdcard/stable/getstartedstep2
[lnk-powershell]: https://docs.microsoft.com/windows/iot-core/connect-your-device/powershell
[lnk-deploy]: how-to-deploy-monitor.md
[lnk-docker-install]: https://docs.docker.com/engine/installation/linux/docker-ce/binaries#install-server-and-client-binaries-on-windows
[lnk-docker-containers]: https://docs.microsoft.com/virtualization/windowscontainers/quick-start/quick-start-windows-10#2-switch-to-windows-containers