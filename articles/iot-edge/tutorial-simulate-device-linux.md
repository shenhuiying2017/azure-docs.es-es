---
title: "Simulación de Azure IoT Edge en Linux | Microsoft Docs"
description: "Instale el runtime de Azure IoT Edge en un dispositivo simulado en Linux e implemente el primer módulo"
services: iot-edge
keywords: 
author: kgremban
manager: timlt
ms.author: kgremban
ms.reviewer: elioda
ms.date: 10/05/2017
ms.topic: article
ms.service: iot-edge
ms.openlocfilehash: 041919fd729880d429e08d8942f8d1ee087ccf61
ms.sourcegitcommit: 3ee36b8a4115fce8b79dd912486adb7610866a7c
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/15/2017
---
# <a name="deploy-azure-iot-edge-on-a-simulated-device-in-linux---preview"></a>Implementación de Azure IoT Edge en un dispositivo simulado en Linux: versión preliminar

Azure IoT Edge le permite realizar análisis y procesamiento de datos en los dispositivos, en lugar de tener que insertar todos los datos en la nube. En los tutoriales IoT Edge se muestra cómo implementar diferentes tipos de módulos, que se crean a partir de los servicios de Azure o de código personalizado, pero primero se necesita un dispositivo para probarlo. 

Este tutorial le guía por la creación de un dispositivo de IoT Edge simulado y la posterior implementación de un módulo que genera los datos del sensor. Aprenderá a:

![Arquitectura del tutorial][2]

El dispositivo simulado que se crea en este tutorial es un monitor que genera datos de temperatura, humedad y presión. Los otros tutoriales de Azure IoT Edge se basan en el trabajo que se realiza aquí mediante la implementación de módulos que analizan los datos para obtener información empresarial. 

## <a name="prerequisites"></a>Requisitos previos

En este tutorial se asume que usa un equipo o máquina virtual con Linux para simular un dispositivo de Internet de las cosas. Para implementar correctamente un dispositivo de IoT Edge se requieren los siguientes servicios:

- [Instale Docker para Linux] [ lnk-docker-ubuntu] y asegúrese de que se ejecuta. 
- La mayoría de las distribuciones de Linux, incluida Ubuntu, ya tienen Python 2.7 instalado. Use el siguiente comando para asegurarse de que PIP está instalado: `sudo apt-get install python-pip`.

## <a name="create-an-iot-hub"></a>Crear un centro de IoT

Para iniciar el tutorial, cree una instancia de IoT Hub.
![Creación de una instancia de IoT Hub][3]

[!INCLUDE [iot-hub-create-hub](../../includes/iot-hub-create-hub.md)]

## <a name="register-an-iot-edge-device"></a>Registro de un dispositivo de IoT Edge

Registre un dispositivo de IoT Edge con la instancia de IoT Hub recién creada.
![Registro de un dispositivo][4]

[!INCLUDE [iot-edge-register-device](../../includes/iot-edge-register-device.md)]

## <a name="install-and-start-the-iot-edge-runtime"></a>Instale e inicie el runtime de IoT Edge

Instale e inicie el runtime de Azure IoT Edge en el dispositivo. 
![Registro de un dispositivo][5]

El runtime de IoT Edge se implementa en todos los dispositivos de IoT Edge. Consta de dos módulos. En primer lugar, el agente de IoT Edge facilita la implementación y supervisión de los módulos en el dispositivo de IoT Edge. En segundo lugar, el centro de IoT Edge administra las comunicaciones entre los módulos del dispositivo de IoT Edge y entre el dispositivo y la instancia de IoT Hub. 

Use los pasos siguientes para instalar e iniciar el runtime de IoT Edge:

1. En el equipo en el que va a ejecutar el dispositivo de IoT Edge, descargue el script de control de IoT Edge.

   ```
   sudo pip install -U azure-iot-edge-runtime-ctl
   ```

1. Configure el runtime con la cadena de conexión del dispositivo de IoT Edge de la sección anterior.

   ```
   sudo iotedgectl setup --connection-string "{device connection string}" --auto-cert-gen-force-no-passwords
   ```

1. Inicie el runtime.

   ```
   sudo iotedgectl start
   ```

1. Compruebe Docker para ver si el agente de IoT Edge se ejecuta como un módulo.

   ```
   sudo docker ps
   ```

## <a name="deploy-a-module"></a>Implementación de un módulo

Administre el dispositivo de Azure IoT Edge desde la nube para implementar un módulo que enviará datos de telemetría a IoT Hub.
![Registro de un dispositivo][6]

[!INCLUDE [iot-edge-deploy-module](../../includes/iot-edge-deploy-module.md)]

## <a name="view-generated-data"></a>Visualización de datos generados

En esta guía de inicio rápido, ha creado un nuevo dispositivo de IoT Edge y ha instalado el runtime de IoT Edge en él. Luego, ha usado Azure Portal para insertar un módulo de IoT Edge para que se ejecute en el dispositivo sin tener que realizar cambios en el propio dispositivo. En este caso, el módulo que ha insertado crea datos del entorno que se pueden usar para los tutoriales. 

Vea los mensajes que se envían desde el módulo tempSensor:

```cmd/sh
docker logs -f tempSensor
```

También puede ver la telemetría que envía el dispositivo mediante la [herramienta del explorador IoT Hub][lnk-iothub-explorer]. 

## <a name="next-steps"></a>Pasos siguientes

En este tutorial, ha creado un nuevo dispositivo de IoT Edge y ha usado la interfaz en la nube de Azure IoT Edge para implementar el código en el dispositivo. Ahora tiene un dispositivo simulado que genera datos sin procesar acerca de su entorno. 

Este tutorial es el requisito previo para todos los demás tutoriales de IoT Edge. Puede continuar con cualquiera de los demás tutoriales para obtener información acerca de la forma en que Azure IoT Edge puede ayudarle a transformar estos datos en información empresarial en el perímetro.

> [!div class="nextstepaction"]
> [Desarrollo e implementación de código de C# como módulo](tutorial-csharp-module.md)


<!-- Images -->
[1]: ./media/tutorial-install-iot-edge/view-module.png
[2]: ./media/tutorial-install-iot-edge/install-edge-full.png
[3]: ./media/tutorial-install-iot-edge/create-iot-hub.png
[4]: ./media/tutorial-install-iot-edge/register-device.png
[5]: ./media/tutorial-install-iot-edge/start-runtime.png
[6]: ./media/tutorial-install-iot-edge/deploy-module.png

<!-- Links -->
[lnk-docker-ubuntu]: https://docs.docker.com/engine/installation/linux/docker-ce/ubuntu/ 
[lnk-iothub-explorer]: https://github.com/azure/iothub-explorer
