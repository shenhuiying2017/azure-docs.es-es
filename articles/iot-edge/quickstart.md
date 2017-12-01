---
title: "Guía de inicio rápido de Azure IoT Edge + Windows | Microsoft Docs"
description: "Pruebe Azure IoT Edge mediante la realización de análisis en un dispositivo perimetral simulado"
services: iot-edge
keywords: 
author: kgremban
manager: timlt
ms.author: kgremban
ms.date: 11/15/2017
ms.topic: article
ms.service: iot-edge
ms.openlocfilehash: 17675f870a015e86f98bf286a9b1c2bbc05c16cd
ms.sourcegitcommit: 933af6219266cc685d0c9009f533ca1be03aa5e9
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/18/2017
---
# <a name="quickstart-deploy-your-first-iot-edge-module-from-the-azure-portal-to-a-windows-device---preview"></a>Guía de inicio rápido: implementación del primer módulo de IoT Edge desde Azure Portal a un dispositivo Windows (versión preliminar)

En esta guía de inicio rápido, use la interfaz en la nube de Azure IoT Edge para implementar código creado previamente de manera remota en un dispositivo de IoT Edge. Para realizar esta tarea, en primer lugar, use un dispositivo de Windows para simular un dispositivo de IoT Edge y, posteriormente, puede implementar un módulo en él.

Si no tiene una suscripción activa a Azure, cree una [cuenta gratuita][lnk-account] antes de comenzar.

## <a name="prerequisites"></a>Requisitos previos

En este tutorial se asume que utiliza un equipo o máquina virtual con Windows para simular un dispositivo de Internet de las cosas. Si ejecuta Windows en una máquina virtual, habilite la [virtualización anidada][lnk-nested] y asigne al menos 2 GB de memoria. 

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

## <a name="create-an-iot-hub-with-azure-cli"></a>Creación de un centro de IoT con la CLI de Azure

Cree un centro de IoT en una suscripción de Azure. El nivel gratuito de IoT Hub funciona para esta guía de inicio rápido. Si ya ha usado IoT Hub y ha creado una centro gratuito, puede omitir esta sección e ir a [Registro de un dispositivo de IoT Edge][anchor-register]. Cada suscripción no puede tener más de un centro de IoT gratuito. 

1. Inicie sesión en [Azure Portal][lnk-portal]. 
1. Haga clic en el botón **Cloud Shell**. 

   ![Botón Cloud Shell][1]

1. Cree un grupo de recursos. El código siguiente crea un grupo de recursos llamado **IoTEdge** en la región **Oeste de EE. UU.**:

   ```azurecli
   az group create --name IoTEdge --location westus
   ```

1. Cree un centro de IoT en el grupo de recursos nuevo. El código siguiente crea un centro **F1** gratis llamado **MyIotHub** en el grupo de recursos **IoTEdge**:

   ```azurecli
   az iot hub create --resource-group IoTEdge --name MyIotHub --sku F1 
   ```

## <a name="register-an-iot-edge-device"></a>Registro de un dispositivo de IoT Edge

[!INCLUDE [iot-edge-register-device](../../includes/iot-edge-register-device.md)]

## <a name="configure-the-iot-edge-runtime"></a>Configuración del runtime de IoT Edge

El runtime de IoT Edge se implementa en todos los dispositivos de IoT Edge. Consta de dos módulos. En primer lugar, el agente de IoT Edge facilita la implementación y supervisión de los módulos en el dispositivo de IoT Edge. En segundo lugar, el centro de IoT Edge administra las comunicaciones entre los módulos del dispositivo de IoT Edge y entre el dispositivo y la instancia de IoT Hub. 

Configure el runtime con la cadena de conexión del dispositivo de IoT Edge de la sección anterior.

```
iotedgectl setup --connection-string "{device connection string}" --auto-cert-gen-force-no-passwords
```

Inicie el runtime.

```
iotedgectl start
```

Compruebe Docker para ver si el agente de IoT Edge se ejecuta como un módulo.

```
docker ps
```

![Ver edgeAgent en Docker](./media/tutorial-simulate-device-windows/docker-ps.png)

## <a name="deploy-a-module"></a>Implementación de un módulo

[!INCLUDE [iot-edge-deploy-module](../../includes/iot-edge-deploy-module.md)]

## <a name="view-generated-data"></a>Visualización de datos generados

En esta guía de inicio rápido, ha creado un nuevo dispositivo de IoT Edge y ha instalado el runtime de IoT Edge en él. Luego, ha usado Azure Portal para insertar un módulo de IoT Edge para que se ejecute en el dispositivo sin tener que realizar cambios en el propio dispositivo. En este caso, el módulo que ha insertado crea datos del entorno que se pueden usar para los tutoriales. 

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
## <a name="clean-up-resources"></a>Limpieza de recursos

Cuando ya no necesite la instancia de IoT Hub que ha creado, puede usar el comando [az iot hub delete][lnk-delete] para quitar el recurso y todos los dispositivos que tenga asociados:

```azurecli
az iot hub delete --name {your iot hub name} --resource-group {your resource group name}
```

## <a name="next-steps"></a>Pasos siguientes

Ha aprendido a implementar un módulo de IoT Edge en un dispositivo de IoT Edge. Ahora intente implementar distintos tipos de servicios de Azure como módulos, para poder analizar los datos en el perímetro. 

* [Implementación de Azure Functions como módulo](tutorial-deploy-function.md)
* [Implementación de Azure Stream Analytics como módulo](tutorial-deploy-stream-analytics.md)
* [Implementación de su propio código como módulo](tutorial-csharp-module.md)


<!-- Images -->
[1]: ./media/quickstart/cloud-shell.png

<!-- Links -->
[lnk-docker]: https://docs.docker.com/docker-for-windows/install/ 
[lnk-docker-containers]: https://docs.microsoft.com/virtualization/windowscontainers/quick-start/quick-start-windows-10#2-switch-to-windows-containers
[lnk-python]: https://www.python.org/downloads/
[lnk-iothub-explorer]: https://github.com/azure/iothub-explorer
[lnk-account]: https://azure.microsoft.com/free
[lnk-portal]: https://portal.azure.com
[lnk-nested]: https://docs.microsoft.com/virtualization/hyper-v-on-windows/user-guide/nested-virtualization
[lnk-delete]: https://docs.microsoft.com/cli/azure/iot/hub?view=azure-cli-latest#az_iot_hub_delete
[lnk-install-iotcore]: how-to-install-iot-core.md

<!-- Anchor links -->
[anchor-register]: #register-an-iot-edge-device
