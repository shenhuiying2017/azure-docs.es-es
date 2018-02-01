---
title: "Implementación de módulos en dispositivos de IoT Edge con la extensión de IoT de la CLI de Azure 2.0 | Microsoft Docs"
description: "Implementación de módulos en un dispositivo de IoT Edge con la extensión de IoT de la CLI de Azure 2.0"
services: iot-edge
keywords: 
author: chrissie926
manager: timlt
ms.author: menchi
ms.date: 01/11/2018
ms.topic: tutorial
ms.service: iot-edge
ms.custom: mvc
ms.openlocfilehash: 26067187864f9a2a4c85c953ae8aca888458d245
ms.sourcegitcommit: f1c1789f2f2502d683afaf5a2f46cc548c0dea50
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/18/2018
---
# <a name="deploy-modules-to-an-iot-edge-device-using-iot-extension-for-azure-cli-20"></a>Implementación de módulos en un dispositivo de IoT Edge con la extensión de IoT de la CLI de Azure 2.0

La [CLI de Azure 2.0](https://docs.microsoft.com/en-us/cli/azure/overview?view=azure-cli-latest) es una herramienta de la línea de comandos multiplataforma y de código abierto para la administración de recursos de Azure, como IoT Edge. Está disponible en Windows, Linux y MacOS.

La CLI de Azure 2.0 permite administrar recursos de Azure IoT Hub, instancias del servicio de aprovisionamiento de dispositivos y centros vinculados listos para usar. La nueva extensión de IoT enriquece la CLI de Azure 2.0 con características como la administración de dispositivos y funcionalidad completa de IoT Edge.

En este tutorial, primero realizará los pasos para configurar la CLI de Azure 2.0 y la extensión de IoT. A continuación, aprenderá a implementar módulos en un dispositivo de IoT Edge con los comandos de la CLI disponibles.

## <a name="installation"></a>Instalación 

### <a name="step-1---install-python"></a>Paso 1: Instalación de Python

Se requiere [Python 2.7x o Python 3.x](https://www.python.org/downloads/).

### <a name="step-2---install-azure-cli-20"></a>Paso 2: Instalación de la CLI de Azure 2.0

Siga las [instrucciones de instalación](https://docs.microsoft.com/en-us/cli/azure/install-azure-cli?view=azure-cli-latest) para instalar la CLI de Azure 2.0 en su entorno. La versión mínima de la CLI de Azure 2.0 debe ser la 2.0.24. Use `az –version` para realizar la validacion. Esta versión admite comandos az extension e introduce la plataforma de comandos de Knack. Una manera sencilla de instalarla en Windows es descargar e instalar [MSI](https://aka.ms/InstallAzureCliWindows).

### <a name="step-3---install-iot-extension"></a>Paso 3: Instalación de la extensión de IoT

En el [archivo Léame de la extensión de IoT](https://github.com/Azure/azure-iot-cli-extension) se describen varias maneras de instalarla. La manera más sencilla es ejecutar `az extension add --name azure-cli-iot-ext`. Después de la instalación, puede usar `az extension list` para validar las extensiones instaladas actualmente o `az extension show --name azure-cli-iot-ext` para ver los detalles acerca de la extensión de IoT. Para quitar la extensión, puede usar `az extension remove --name azure-cli-iot-ext`.


## <a name="deploy-modules-to-an-iot-edge-device"></a>Implementación de módulos en un dispositivo de IoT Edge
En este tutorial aprenderá a crear una implementación de IoT Edge. En el ejemplo se muestra cómo iniciar sesión en la cuenta de Azure, crear un grupo de recursos de Azure (es decir, un contenedor que contiene recursos relacionados con una solución de Azure), crear un centro de IoT, crear tres identidades de dispositivo de IoT Edge, establecer etiquetas y crear una implementación de IoT Edge dirigida a esos dispositivos. Antes de comenzar, realice los pasos de instalación descritos anteriormente. Si no tiene una cuenta de Azure aún, también puede [crear una cuenta gratuita](https://azure.microsoft.com/free/?v=17.39a). 


### <a name="1-login-to-the-azure-account"></a>1. Inicie sesión en la cuenta de Azure
  
    az login

![login][1]

### <a name="2-create-a-resource-group-iothubblogdemo-in-eastus"></a>2. Cree un grupo de recursos IoTHubBlogDemo en eastus

    az group create -l eastus -n IoTHubBlogDemo

![Crear un grupo de recursos][2]


### <a name="3-create-an-iot-hub-blogdemohub-under-the-newly-created-resource-group"></a>3. Cree una instancia de IoT Hub blogDemoHub en el grupo de recursos recién creado

    az iot hub create --name blogDemoHub --resource-group IoTHubBlogDemo

![Crear IoT Hub][3]


### <a name="4-create-an-iot-edge-device"></a>4. Cree un dispositivo de IoT Edge

    az iot hub device-identity create -d edge001 -n blogDemoHub --edge-enabled

![Crear dispositivo de IoT Edge][4]

### <a name="5-apply-configuration-to-the-iot-edge-device"></a>5. Aplique la configuración del dispositivo de IoT Edge

Guarde la plantilla de implementación de JSON localmente como archivo txt. Necesitará la ruta de acceso al archivo al ejecutar el comando apply-configuration.

A continuación se muestra una plantilla de implementación de JSON de ejemplo que contiene un módulo de tempSensor:

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
              "image": "edgepreview.azurecr.io/azureiotedge/edge-agent:1.0-preview",
              "createOptions": "{}"
            }
          },
          "edgeHub": {
            "type": "docker",
            "status": "running",
            "restartPolicy": "always",
            "settings": {
              "image": "edgepreview.azurecr.io/azureiotedge/edge-hub:1.0-preview",
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
              "image": "edgepreview.azurecr.io/azureiotedge/simulated-temperature-sensor:1.0-preview",
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

    az iot hub apply-configuration --device-id edge001 --hub-name blogDemoHub --content C:\<yourLocation>\edgeconfig.txt

![Aplicación de la configuración][5]

### <a name="6-list-modules"></a>6. Enumere los módulos
    
    az iot hub module-identity list --device-id edge001 --hub-name blogDemoHub

![Enumere los módulos][6]

## <a name="next-steps"></a>pasos siguientes

En este tutorial, ha creado una instancia de Azure Functions que contiene código para filtrar datos sin procesar generados por el dispositivo IoT Edge. Para continuar con la exploración de Azure IoT Edge, aprenda a usar un dispositivo de IoT Edge como puerta de enlace. 

> [!div class="nextstepaction"]
> [Creación de un dispositivo de puerta de enlace con IoT Edge](how-to-create-transparent-gateway.md)

<!--Links-->
[lnk-tutorial1-win]: tutorial-simulate-device-windows.md
[lnk-tutorial1-lin]: tutorial-simulate-device-linux.md

<!-- Images -->
[1]: ./media/tutorial-create-deployment-with-cli-iot-extension/login.jpg
[2]: ./media/tutorial-create-deployment-with-cli-iot-extension/create-resource-group.jpg
[3]: ./media/tutorial-create-deployment-with-cli-iot-extension/create-hub.jpg
[4]: ./media/tutorial-create-deployment-with-cli-iot-extension/Create-edge-device.png
[5]: ./media/tutorial-create-deployment-with-cli-iot-extension/apply-configuration.PNG
[6]: ./media/tutorial-create-deployment-with-cli-iot-extension/list-modules.PNG

