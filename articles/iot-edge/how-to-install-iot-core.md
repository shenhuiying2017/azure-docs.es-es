---
title: "Instalación de Azure IoT Edge en IoT Core | Microsoft Docs"
description: "Instalación de la instancia de Azure IoT Edge en tiempo de ejecución en un dispositivo Windows IoT Core"
services: iot-edge
keywords: 
author: kgremban
manager: timlt
ms.author: kgremban
ms.reviewer: veyalla
ms.date: 11/17/2017
ms.topic: article
ms.service: iot-edge
ms.openlocfilehash: d3ff260b4ac238ce7aaa2a63538dede7bd21a19c
ms.sourcegitcommit: cfd1ea99922329b3d5fab26b71ca2882df33f6c2
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/30/2017
---
# <a name="install-the-iot-edge-runtime-on-windows-iot-core---preview"></a>Instalación de la instancia de IoT Edge en tiempo de ejecución en un dispositivo Windows IoT Core (versión preliminar)

La instancia de Azure IoT Edge en tiempo de ejecución puede ejecutarse incluso en dispositivos de una sola placa (SBC) de tamaño reducido que son muy frecuentes en el sector de IoT. Este artículo lo guía a través de aprovisionamiento de la instancia en tiempo de ejecución en una placa de desarrollo [MinnowBoard Turbot][lnk-minnow] que ejecuta Windows IoT Core.

## <a name="install-the-runtime"></a>Instalación de la instancia en tiempo de ejecución

1. Instale el [panel de Windows 10 IoT Core] [ lnk-core] en un sistema host.
1. Siga los pasos de [Configurar el dispositivo][lnk-board] para configurar la placa con la imagen MinnowBoard Turbot/MAX Build 16299. 
1. Encienda el dispositivo y, luego, [inicie sesión de forma remota con PowerShell][lnk-powershell].
1. En la consola de PowerShell, instale el entorno de tiempo de ejecución del contenedor: 

   ```powershell
   Invoke-WebRequest https://master.dockerproject.org/windows/x86_64/docker-17.06.0-dev.zip -o temp.zip
   Expand-Archive .\temp.zip $env:ProgramFiles -f
   Remove-Item .\temp.zip
   $env:Path += ";$env:programfiles\docker"
   SETX /M PATH "$env:Path"
   dockerd --register-service
   start-service docker
   ```

   >[!NOTE]
   >Este entorno de tiempo de ejecución del contenedor es del servidor de compilación del proyecto Moby y es solo para fines de evaluación. No se ha probado, aprobado ni admitido en Docker.

1. Instale el entorno de tiempo de ejecución de IoT Edge y compruebe su configuración:

   ```powershell
   Invoke-Expression (Invoke-WebRequest -useb https://aka.ms/iotedgewin)
   ```

   El script proporciona lo siguiente: 
   * Python 3.6
   * El script de control de IoT Edge (iotedgectl.exe)

Puede ver resultados informativos de la herramienta iotedgectl.exe en verde en la ventana de PowerShell remota. Esto no indica que haya necesariamente errores. 

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
