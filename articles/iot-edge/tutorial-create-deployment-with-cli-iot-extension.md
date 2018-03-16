---
title: "Implementación de módulos en dispositivos de IoT Edge con la extensión de IoT de la CLI de Azure 2.0 | Microsoft Docs"
description: "Implementación de módulos en un dispositivo de IoT Edge con la extensión de IoT de la CLI de Azure 2.0"
services: iot-edge
keywords: 
author: chrissie926
manager: timlt
ms.author: menchi
ms.date: 03/02/2018
ms.topic: article
ms.service: iot-edge
ms.custom: mvc
ms.reviewer: kgremban
ms.openlocfilehash: 25f1becff11138e59595c211802acbf791094e53
ms.sourcegitcommit: 8c3267c34fc46c681ea476fee87f5fb0bf858f9e
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 03/09/2018
---
# <a name="deploy-modules-to-an-iot-edge-device-using-iot-extension-for-azure-cli-20"></a>Implementación de módulos en un dispositivo de IoT Edge con la extensión de IoT de la CLI de Azure 2.0

La [CLI de Azure 2.0](https://docs.microsoft.com/en-us/cli/azure?view=azure-cli-latest) es una herramienta de línea de comandos multiplataforma y de código abierto para la administración de recursos de Azure, como IoT Edge. Está disponible en Windows, Linux y MacOS.

La CLI de Azure 2.0 permite administrar recursos de Azure IoT Hub, instancias de servicio de aprovisionamiento de dispositivos y centros vinculados listos para usar. La nueva extensión de IoT enriquece la CLI de Azure 2.0 con características como la administración de dispositivos y funcionalidad completa de IoT Edge.

En este tutorial, primero realizará los pasos para configurar la CLI de Azure 2.0 y la extensión de IoT. A continuación, aprenderá a implementar módulos en un dispositivo de IoT Edge con los comandos de la CLI disponibles.

## <a name="prerequisites"></a>requisitos previos

* Una cuenta de Azure. En caso de no tener ninguna, puede [crear una gratis](https://azure.microsoft.com/free/?v=17.39a) ahora mismo. 

* [Python 2.7x o Python 3.x](https://www.python.org/downloads/).

* [CLI de Azure 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli) en su entorno. La versión mínima de la CLI de Azure 2.0 debe ser la 2.0.24. Use `az –-version` para realizar la validacion. Esta versión admite comandos az extension e introduce la plataforma de comandos de Knack. Una manera sencilla de instalarla en Windows es descargar e instalar [MSI](https://aka.ms/InstallAzureCliWindows).

* [La extensión de IoT para la CLI de Azure 2.0](https://github.com/Azure/azure-iot-cli-extension):
   1. Ejecute `az extension add --name azure-cli-iot-ext`. 
   2. Después de la instalación, use `az extension list` para validar las extensiones instaladas actualmente o `az extension show --name azure-cli-iot-ext` para ver detalles acerca de la extensión de IoT.
   3. Para eliminar la extensión, use `az extension remove --name azure-cli-iot-ext`.


## <a name="create-an-iot-edge-device"></a>Cree un dispositivo de IoT Edge
En este artículo se proporcionan instrucciones para crear una implementación de IoT Edge. En el ejemplo se muestra cómo iniciar sesión en la cuenta de Azure, crear un grupo de recursos de Azure (es decir, un contenedor que albergue recursos relacionados con una solución de Azure), crear una instancia de IoT Hub, crear tres identidades de dispositivo de IoT Edge, establecer etiquetas y crear una implementación de IoT Edge dirigida a esos dispositivos. 

Inicio de sesión en la cuenta de Azure Después de escribir el siguiente comando de inicio de sesión, se le pedirá que use un explorador web para iniciar sesión con un código de un solo uso: 

   ```cli
   az login
   ```

Cree un nuevo grupo de recursos llamado **IoTHubCLI** en la región Este de EE. UU.: 

   ```cli
   az group create -l eastus -n IoTHubCLI
   ```

   ![Creación de un grupo de recursos][2]

Cree un centro de IoT llamado **CLIDemoHub** en el grupo de recursos recién creado:

   ```cli
   az iot hub create --name CLIDemoHub --resource-group IoTHubCLI --sku S1
   ```

   >[!TIP]
   >A cada suscripción se le asigna un centro de IoT disponible. Para crear un centro disponible con el comando de la CLI, reemplace el valor SKU por `--sku F1`. Si ya tiene un centro disponible en su suscripción, recibirá un mensaje de error al intentar crear un segundo. 

Cree un dispositivo de IoT Edge:

   ```cli
   az iot hub device-identity create --device-id edge001 -hub-name CLIDemoHub --edge-enabled
   ```

   ![Crear dispositivo de IoT Edge][4]

## <a name="configure-the-iot-edge-device"></a>Configuración del dispositivo de IoT Edge

Cree una plantilla JSON de implementación y guárdela localmente como un archivo txt. Necesitará la ruta de acceso al archivo al ejecutar el comando apply-configuration.

Las plantillas JSON de implementación siempre deben incluir dos módulos del sistema: edgeAgent y edgeHub. Además de estos dos, puede usar este archivo para implementar módulos adicionales en el dispositivo de IoT Edge. Use el ejemplo siguiente para configurar el dispositivo de IoT Edge con un módulo tempSensor:

   ```json
   {
     "moduleContent": {
       "$edgeAgent": {
         "properties.desired": {
           "schemaVersion": "1.0",
           "runtime": {
             "type": "docker",
             "settings": {
               "minDockerVersion": "v1.25",
               "loggingOptions": ""
             }
           },
           "systemModules": {
             "edgeAgent": {
               "type": "docker",
               "settings": {
                 "image": "microsoft/azureiotedge-agent:1.0-preview",
                 "createOptions": "{}"
               }
             },
             "edgeHub": {
               "type": "docker",
               "status": "running",
               "restartPolicy": "always",
               "settings": {
                 "image": "microsoft/azureiotedge-hub:1.0-preview",
                 "createOptions": "{}"
               }
             }
           },
           "modules": {
             "tempSensor": {
               "version": "1.0",
               "type": "docker",
               "status": "running",
               "restartPolicy": "always",
               "settings": {
                 "image": "microsoft/azureiotedge-simulated-temperature-sensor:1.0-preview",
                 "createOptions": "{}"
               }
             }
           }
         }
       },
       "$edgeHub": {
         "properties.desired": {
           "schemaVersion": "1.0",
           "routes": {},
           "storeAndForwardConfiguration": {
             "timeToLiveSecs": 7200
           }
         }
       },
       "tempSensor": {
         "properties.desired": {}
       }
     }
   }
   ```

Aplique la configuración al dispositivo de IoT Edge:

   ```cli
   az iot hub apply-configuration --device-id edge001 --hub-name CLIDemoHub --content C:\<configuration.txt file path>
   ```

Vea los módulos en el dispositivo de IoT Edge:
    
   ```cli
   az iot hub module-identity list --device-id edge001 --hub-name CLIDemoHub
   ```

   ![Enumere los módulos][6]

## <a name="next-steps"></a>Pasos siguientes

* Aprenda a usar [un dispositivo de IoT Edge como puerta de enlace](how-to-create-transparent-gateway.md)

<!--Links-->
[lnk-tutorial1-win]: tutorial-simulate-device-windows.md
[lnk-tutorial1-lin]: tutorial-simulate-device-linux.md

<!-- Images -->
[2]: ./media/tutorial-create-deployment-with-cli-iot-extension/create-resource-group.png
[4]: ./media/tutorial-create-deployment-with-cli-iot-extension/Create-edge-device.png
[6]: ./media/tutorial-create-deployment-with-cli-iot-extension/list-modules.png

