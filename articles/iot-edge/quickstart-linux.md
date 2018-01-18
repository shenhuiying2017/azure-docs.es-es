---
title: "Guía de inicio rápido de Azure IoT Edge + Linux | Microsoft Docs"
description: "Pruebe Azure IoT Edge mediante la realización de análisis en un dispositivo perimetral simulado"
services: iot-edge
keywords: 
author: kgremban
manager: timlt
ms.author: kgremban
ms.date: 01/11/2018
ms.topic: article
ms.service: iot-edge
ms.openlocfilehash: 440b70f4d04728973d77e54e7f6303e1ad7fcd89
ms.sourcegitcommit: 48fce90a4ec357d2fb89183141610789003993d2
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/12/2018
---
# <a name="quickstart-deploy-your-first-iot-edge-module-to-a-linux-or-mac-device---preview"></a>Guía de inicio rápido: implementación del primer módulo de IoT Edge en un dispositivo Linux o Mac (versión preliminar)

Azure IoT Edge traslada la eficacia de la nube a los dispositivos de Internet de las cosas. En este tema, obtenga información sobre cómo usar la interfaz de nube para implementar código creado previamente de manera remota en un dispositivo IoT Edge.

Si no tiene una suscripción activa a Azure, cree una [cuenta gratuita][lnk-account] antes de comenzar.

## <a name="prerequisites"></a>Requisitos previos

En esta guía de inicio rápido se usa el equipo o la máquina virtual como un dispositivo de Internet de las cosas. Para convertir una máquina en un dispositivo IoT Edge, se requieren los siguientes servicios:

* PIP de Python, para instalar el runtime de IoT Edge.
   * Linux: `sudo apt-get install python-pip`.
   * MacOS: `sudo easy_install pip`.
* Docker, para ejecutar los módulos de IoT Edge
   * [Instale Docker para Linux][lnk-docker-ubuntu] y asegúrese de que se ejecuta. 
   * [Instale Docker para Mac][lnk-docker-mac] y asegúrese de que se ejecuta. 

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

Cree una identidad para el dispositivo simulado, con el fin de que pueda comunicarse con su centro de IoT. Dado que los dispositivos de IoT Edge se comportan y se pueden administrar de manera diferente a los dispositivos de IoT típicos, declare que este es un dispositivo de IoT Edge desde el principio. 

1. En Azure Portal, navegue hasta el centro de IoT.
1. Seleccione **IoT Edge (versión preliminar)**.
1. Seleccione **Add IoT Edge device** (Agregar dispositivo de IoT Edge).
1. Asigne el dispositivo simulado un identificador de dispositivo único.
1. Seleccione **Guardar** para agregar su dispositivo.
1. Seleccione el dispositivo nuevo en la lista de dispositivos. 
1. Copie el valor de **Cadena de conexión: clave principal** y guárdelo. Este valor se usará para configurar el runtime de IoT Edge en la sección siguiente. 

## <a name="install-and-start-the-iot-edge-runtime"></a>Instale e inicie el runtime de IoT Edge

El runtime de IoT Edge se implementa en todos los dispositivos con IoT Edge. Consta de dos módulos. En primer lugar, el agente de IoT Edge facilita la implementación y supervisión de los módulos en el dispositivo de IoT Edge. En segundo lugar, el centro de IoT Edge administra las comunicaciones entre los módulos del dispositivo de IoT Edge y entre el dispositivo e IoT Hub. 

En el equipo en el que va a ejecutar el dispositivo con IoT Edge, descargue el script de control de IoT Edge:
```cmd
sudo pip install -U azure-iot-edge-runtime-ctl
```

Configure el runtime con la cadena de conexión del dispositivo de IoT Edge de la sección anterior:
```cmd
sudo iotedgectl setup --connection-string "{device connection string}" --auto-cert-gen-force-no-passwords
```

Inicie el runtime:
```cmd
sudo iotedgectl start
```

Compruebe Docker para ver si el agente de IoT Edge se ejecuta como un módulo:
```cmd
sudo docker ps
```

![Ver edgeAgent en Docker](./media/tutorial-simulate-device-linux/docker-ps.png)

## <a name="deploy-a-module"></a>Implementación de un módulo

[!INCLUDE [iot-edge-deploy-module](../../includes/iot-edge-deploy-module.md)]

## <a name="view-generated-data"></a>Visualización de datos generados

En esta guía de inicio rápido, ha creado un nuevo dispositivo de IoT Edge y ha instalado el runtime de IoT Edge en él. Luego, ha usado Azure Portal para insertar un módulo de IoT Edge para que se ejecute en el dispositivo sin tener que realizar cambios en el propio dispositivo. En este caso, el módulo que ha insertado crea datos del entorno que se pueden usar para los tutoriales. 

Vuelva a abrir el símbolo del sistema en el equipo que ejecuta el dispositivo simulado. Confirme que el módulo implementado desde la nube se está ejecutando en el dispositivo IoT Edge:

```cmd
sudo docker ps
```

![Ver tres módulos en el dispositivo](./media/tutorial-simulate-device-linux/docker-ps2.png)

Vea los mensajes que se envían desde el módulo tempSensor a la nube:

```cmd
sudo docker logs -f tempSensor
```

![Ver los datos desde el módulo](./media/tutorial-simulate-device-linux/docker-logs.png)

También puede ver la telemetría que envía el dispositivo mediante la [herramienta del explorador IoT Hub][lnk-iothub-explorer]. 

## <a name="clean-up-resources"></a>Limpieza de recursos

Cuando ya no necesite la instancia de IoT Hub que ha creado, puede usar el comando [az iot hub delete][lnk-delete] para quitar el recurso y todos los dispositivos que tenga asociados:

```azurecli
az iot hub delete --name {your iot hub name} --resource-group {your resource group name}
```

## <a name="next-steps"></a>pasos siguientes

Ha aprendido a implementar un módulo de IoT Edge en un dispositivo de IoT Edge. Ahora intente implementar distintos tipos de servicios de Azure como módulos, para poder analizar los datos en el perímetro. 

* [Implementación de su propio código como un módulo](tutorial-csharp-module.md)
* [Implementación de Azure Function como un módulo](tutorial-deploy-function.md)
* [Implementación de Azure Stream Analytics como un módulo](tutorial-deploy-stream-analytics.md)


<!-- Images -->
[1]: ./media/quickstart/cloud-shell.png

<!-- Links -->
[lnk-docker-ubuntu]: https://docs.docker.com/engine/installation/linux/docker-ce/ubuntu/ 
[lnk-docker-mac]: https://docs.docker.com/docker-for-mac/install/
[lnk-iothub-explorer]: https://github.com/azure/iothub-explorer
[lnk-account]: https://azure.microsoft.com/free
[lnk-portal]: https://portal.azure.com
[lnk-delete]: https://docs.microsoft.com/cli/azure/iot/hub?view=azure-cli-latest#az_iot_hub_delete

<!-- Anchor links -->
[anchor-register]: #register-an-iot-edge-device
