---
title: "Simulación de Azure IoT Edge en Windows | Microsoft Docs"
description: "Instalación del runtime de Azure IoT Edge en un dispositivo simulado en Windows e implementación del primer módulo"
services: iot-edge
keywords: 
author: kgremban
manager: timlt
ms.author: kgremban
ms.reviewer: elioda
ms.date: 11/15/2017
ms.topic: article
ms.service: iot-edge
ms.openlocfilehash: 08c501b9132bb21f47f099725d1fad5556befb4c
ms.sourcegitcommit: 3ee36b8a4115fce8b79dd912486adb7610866a7c
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/15/2017
---
# <a name="deploy-azure-iot-edge-on-a-simulated-device-in-windows----preview"></a>Implementación de Azure IoT Edge en un dispositivo simulado en Windows (versión preliminar)

Azure IoT Edge traslada la eficacia de la nube a los dispositivos de Internet de las cosas (IoT). Este tutorial le guía por la creación de un dispositivo de IoT Edge simulado que genera los datos del sensor. Aprenderá a:

![Arquitectura del tutorial][2]

El dispositivo simulado que se crea en este tutorial es un monitor de un aerogenerador que genera datos de temperatura, humedad y presión. Le interesan estos datos porque las turbinas funcionan a diferentes niveles de eficiencia dependiendo de las condiciones meteorológicas. Los otros tutoriales de Azure IoT Edge se basan en el trabajo que se realiza aquí mediante la implementación de módulos que analizan los datos para obtener información empresarial. 

## <a name="prerequisites"></a>Requisitos previos

En este tutorial se asume que utiliza un equipo o máquina virtual con Windows para simular un dispositivo de Internet de las cosas. 

>[!TIP]
>Si ejecuta Windows en una máquina virtual, habilite la [virtualización anidada][lnk-nested] y asigne al menos 2 GB de memoria. 

1. Asegúrese de que usa una versión de Windows compatible:
   * Windows 10 
   * Windows Server
2. Instale [Docker para Windows][lnk-docker] y asegúrese de que se ejecuta.
3. Instale [Python 2.7 en Windows][lnk-python] y asegúrese de que puede usar el comando pip.
4. Ejecute el comando siguiente para descargar el script de control de IoT Edge.

   ```
   pip install -U azure-iot-edge-runtime-ctl
   ```

> [!NOTE]
> Azure IoT Edge puede ejecutar contenedores Windows o Linux. Para utilizar contenedores Windows, es preciso ejecutar:
>    * Windows 10 Fall Creators Update
>    * Windows Server 1709 (compilación 16299)
>    * Windows IoT Core (compilación 16299) en un dispositivo basado en x64
>
> Para Windows IoT Core, siga las instrucciones del artículo sobre cómo [instalar el entorno en tiempo de ejecución de IoT Edge en Windows IoT Core][lnk-install-iotcore]. En caso contrario, simplemente [configure Docker para que use contenedores Windows][lnk-docker-containers] y, opcionalmente, valide los requisitos previos con el siguiente comando de PowerShell:
>    ```
>    Invoke-Expression (Invoke-WebRequest -useb https://aka.ms/iotedgewin)
>    ```


## <a name="create-an-iot-hub"></a>Crear un centro de IoT

Para iniciar el tutorial, cree una instancia de IoT Hub.
![Creación de una instancia de IoT Hub][3]

[!INCLUDE [iot-hub-create-hub](../../includes/iot-hub-create-hub.md)]

## <a name="register-an-iot-edge-device"></a>Registro de un dispositivo de IoT Edge

Registre un dispositivo de IoT Edge con la instancia de IoT Hub recién creada.
![Registro de un dispositivo][4]

[!INCLUDE [iot-edge-register-device](../../includes/iot-edge-register-device.md)]

## <a name="configure-the-iot-edge-runtime"></a>Configuración del runtime de IoT Edge

Instale e inicie el runtime de Azure IoT Edge en el dispositivo. 
![Registro de un dispositivo][5]

El runtime de IoT Edge se implementa en todos los dispositivos de IoT Edge. Consta de dos módulos. En primer lugar, el agente de IoT Edge facilita la implementación y supervisión de los módulos en el dispositivo de IoT Edge. En segundo lugar, el centro de IoT Edge administra las comunicaciones entre los módulos del dispositivo de IoT Edge y entre el dispositivo y la instancia de IoT Hub. 


Use los pasos siguientes para instalar e iniciar el runtime de IoT Edge:

1. Configure el runtime con la cadena de conexión del dispositivo de IoT Edge de la sección anterior.

   ```
   iotedgectl setup --connection-string "{device connection string}" --auto-cert-gen-force-no-passwords
   ```

1. Inicie el runtime.

   ```
   iotedgectl start
   ```

1. Compruebe Docker para ver si el agente de IoT Edge se ejecuta como un módulo.

   ```
   docker ps
   ```

## <a name="deploy-a-module"></a>Implementación de un módulo

Administre el dispositivo de Azure IoT Edge desde la nube para implementar un módulo que enviará datos de telemetría a IoT Hub.
![Registro de un dispositivo][6]

[!INCLUDE [iot-edge-deploy-module](../../includes/iot-edge-deploy-module.md)]


## <a name="view-generated-data"></a>Visualización de datos generados

En esta guía de inicio rápido, ha creado un nuevo dispositivo de IoT Edge y ha instalado el runtime de IoT Edge en él. Luego, ha usado Azure Portal para insertar un módulo de IoT Edge para que se ejecute en el dispositivo sin tener que realizar cambios en el propio dispositivo. En este caso, el módulo que ha insertado crea datos del entorno que se pueden usar para los tutoriales. 

Vea los mensajes que se envían desde el módulo tempSensor:

```cmd/sh
sudo docker logs -f tempSensor
```

También puede ver la telemetría que envía el dispositivo mediante la [herramienta del explorador IoT Hub][lnk-iothub-explorer]. 

## <a name="next-steps"></a>Pasos siguientes

En este tutorial, ha creado un nuevo dispositivo de IoT Edge y ha usado la interfaz en la nube de Azure IoT Edge para implementar el código en el dispositivo. Ahora tiene un dispositivo simulado que genera datos sin procesar acerca de su entorno. 

Este tutorial es el requisito previo para todos los demás tutoriales de IoT Edge. Puede continuar con cualquiera de los demás tutoriales para obtener información acerca de la forma en que Azure IoT Edge puede ayudarle a transformar estos datos en información empresarial en el perímetro.

> [!div class="nextstepaction"]
> [Desarrollo e implementación de código de C# como módulo](tutorial-csharp-module.md)

<!-- Images -->
[2]: ./media/tutorial-install-iot-edge/install-edge-full.png
[3]: ./media/tutorial-install-iot-edge/create-iot-hub.png
[4]: ./media/tutorial-install-iot-edge/register-device.png
[5]: ./media/tutorial-install-iot-edge/start-runtime.png
[6]: ./media/tutorial-install-iot-edge/deploy-module.png

<!-- Links -->
[lnk-nested]: https://docs.microsoft.com/virtualization/hyper-v-on-windows/user-guide/nested-virtualization
[lnk-docker]: https://docs.docker.com/docker-for-windows/install/ 
[lnk-python]: https://www.python.org/downloads/
[lnk-docker-containers]: https://docs.microsoft.com/virtualization/windowscontainers/quick-start/quick-start-windows-10#2-switch-to-windows-containers
[lnk-iothub-explorer]: https://github.com/azure/iothub-explorer
[lnk-install-iotcore]: how-to-install-iot-core.md