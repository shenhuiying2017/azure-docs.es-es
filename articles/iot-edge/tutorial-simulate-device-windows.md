---
title: "Simulación de Azure IoT Edge en Windows | Microsoft Docs"
description: "Instalación del runtime de Azure IoT Edge en un dispositivo simulado en Windows e implementación del primer módulo"
services: iot-edge
keywords: 
author: kgremban
manager: timlt
ms.author: kgremban
ms.reviewer: elioda
ms.date: 11/16/2017
ms.topic: article
ms.service: iot-edge
ms.openlocfilehash: 0207418cf71902ce9bc9d2911124d1d46889d893
ms.sourcegitcommit: 4ea06f52af0a8799561125497f2c2d28db7818e7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/21/2017
---
# <a name="deploy-azure-iot-edge-on-a-simulated-device-in-windows----preview"></a>Implementación de Azure IoT Edge en un dispositivo simulado en Windows (versión preliminar)

Azure IoT Edge le permite realizar análisis y procesamiento de datos en los dispositivos, en lugar de tener que insertar todos los datos en la nube. En los tutoriales IoT Edge se muestra cómo implementar diferentes tipos de módulos, que se crean a partir de los servicios de Azure o de código personalizado, pero primero se necesita un dispositivo para probarlo. 

En este tutorial, aprenderá a:

1. Creación de un IoT Hub
2. Registro de un dispositivo de IoT Edge
3. Inicio del runtime de IoT Edge
4. Implementación de un módulo

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

   ```cmd
   pip install -U azure-iot-edge-runtime-ctl
   ```

> [!NOTE]
> Azure IoT Edge puede ejecutar contenedores Windows o Linux. Si ejecuta una de las siguientes versiones de Windows, puede usar los contenedores de Windows:
>    * Windows 10 Fall Creators Update
>    * Windows Server 1709 (compilación 16299)
>    * Windows IoT Core (compilación 16299) en un dispositivo basado en x64
>
> Para Windows IoT Core, siga las instrucciones del artículo sobre cómo [instalar el entorno en tiempo de ejecución de IoT Edge en Windows IoT Core][lnk-install-iotcore]. En caso contrario, simplemente [configure Docker para que use los contenedores de Windows][lnk-docker-containers]. Utilice el siguiente comando para validar los requisitos previos:
>    ```powershell
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

El runtime de IoT Edge se implementa en todos los dispositivos de IoT Edge. Consta de dos módulos. El **agente de IoT Edge** facilita la implementación y supervisión de los módulos en el dispositivo de IoT Edge. El **centro de IoT Edge** administra las comunicaciones entre los módulos del dispositivo de IoT Edge y entre el dispositivo y la instancia de IoT Hub. Cuando configure el runtime en el nuevo dispositivo, solo se iniciará el agente de IoT Edge. El centro de IoT Edge se incluye más adelante al implementar un módulo. 


Configure el runtime con la cadena de conexión del dispositivo de IoT Edge de la sección anterior.

```cmd
iotedgectl setup --connection-string "{device connection string}" --auto-cert-gen-force-no-passwords
```

Inicie el runtime.

```cmd
iotedgectl start
```

Compruebe Docker para ver si el agente de IoT Edge se ejecuta como un módulo.

```cmd
docker ps
```

![Vea edgeAgent en Docker](./media/tutorial-simulate-device-windows/docker-ps.png)

## <a name="deploy-a-module"></a>Implementación de un módulo

Administre el dispositivo de Azure IoT Edge desde la nube para implementar un módulo que enviará datos de telemetría a IoT Hub.
![Registro de un dispositivo][6]

[!INCLUDE [iot-edge-deploy-module](../../includes/iot-edge-deploy-module.md)]


## <a name="view-generated-data"></a>Visualización de datos generados

En este tutorial de inicio rápido, ha creado un nuevo dispositivo de IoT Edge y ha instalado el runtime de IoT Edge en él. Luego, ha usado Azure Portal para insertar un módulo de IoT Edge para que se ejecute en el dispositivo sin tener que realizar cambios en el propio dispositivo. En este caso, el módulo que ha insertado crea datos del entorno que se pueden usar para los tutoriales. 

Vuelva a abrir el símbolo del sistema en el equipo que ejecuta el dispositivo simulado. Confirme que el módulo implementado desde la nube se está ejecutando en el dispositivo IoT Edge. 

```cmd
docker ps
```

![Ver tres módulos en el dispositivo](./media/tutorial-simulate-device-windows/docker-ps2.png)

Vea los mensajes que se envían desde el módulo tempSensor a la nube. 

```cmd
docker logs -f tempSensor
```

![Ver los datos desde el módulo](./media/tutorial-simulate-device-windows/docker-logs.png)

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