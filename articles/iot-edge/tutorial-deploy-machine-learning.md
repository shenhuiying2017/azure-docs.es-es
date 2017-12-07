---
title: "Implementación de Azure Machine Learning con Azure IoT Edge | Microsoft Docs"
description: "Implementación de Azure Machine Learning como módulo en un dispositivo perimetral"
services: iot-edge
keywords: 
author: kgremban
manager: timlt
ms.author: kgremban
ms.date: 11/15/2017
ms.topic: article
ms.service: iot-edge
ms.openlocfilehash: e061e599f365bf3d343cb59b8dc6a61e06627517
ms.sourcegitcommit: cfd1ea99922329b3d5fab26b71ca2882df33f6c2
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/30/2017
---
# <a name="deploy-azure-machine-learning-as-an-iot-edge-module---preview"></a>Implementación de Azure Machine Learning como un módulo de IoT Edge (versión preliminar)

Los módulos de IoT Edge se pueden usar para implementar código que, a su vez, implementa una lógica de negocios directamente en los dispositivos de IoT Edge. Este tutorial le guía a través de la implementación de un módulo de Azure Machine Learning que predice cuándo se produce un error en un dispositivo basándose en los datos de sensor del dispositivo de IoT Edge simulado que creó en el paso de implementación de Azure IoT Edge en un dispositivo simulado en tutoriales de [Windows][lnk-tutorial1-win] o [Linux][lnk-tutorial1-lin]. Aprenderá a: 

> [!div class="checklist"]
> * Implementar un modelo de Azure Machine Learning en un dispositivo de IoT Edge
> * Visualización de datos generados

Si desea utilizar su propio modelo de [Azure Machine Learning](https://docs.microsoft.com/azure/machine-learning/preview/) en la solución, tendrá que [implementar un modelo](https://aka.ms/aml-iot-edge-doc) para IoT Edge y albergarlo en un registro de contenedor como [Azure Container Registry](../container-registry/index.yml) o Docker.

## <a name="prerequisites"></a>Requisitos previos

* El dispositivo de Azure IoT Edge que creó en la guía de inicio rápido o en el primer tutorial.
* La cadena de conexión de IoT Hub del centro de IoT al que se conecta el dispositivo de IoT Edge.
* El contenedor de Azure Machine Learning

## <a name="create-the-azure-ml-container"></a>Creación del contenedor de Azure Machine Learning
Para crear el contenedor de Azure Machine Learning, siga las instrucciones que aparecen en el [kit de herramientas de IA de Azure IoT Edge](https://aka.ms/aml-iot-edge-anomaly-detection).

## <a name="run-the-solution"></a>Ejecución de la solución

1. En [Azure Portal](https://portal.azure.com), navegue hasta su centro de IoT.
1. Vaya a **IoT Edge (versión preliminar)** y seleccione el dispositivo de IoT Edge.
1. Seleccione **Set modules** (Establecer módulos).
1. Seleccione **Add IoT Edge module** (Agregar módulo de IoT Edge).
1. En el campo **Nombre**, escriba `tempSensor`.
1. En el campo **URI de la imagen**, escriba `microsoft/azureiotedge-simulated-temperature-sensor:1.0-preview`.
1. Deje los restantes valores tal cual y seleccione **Guardar**.
1. En el paso **Add Modules** (Agregar módulos), vuelva a seleccionar **Add IoT Edge module** (Agregar módulo de IoT Edge).
1. En el campo **Nombre**, escriba el nombre del contenedor que ha realizado en la sección anterior. Consulte el [Kit de herramientas de IA para Azure IoT Edge](https://aka.ms/aml-iot-edge-anomaly-detection) para encontrar el nombre.
1. En el campo **Imagen**, introduzca el URI de la imagen del contenedor que ha realizado en la sección anterior. Consulte el [Kit de herramientas de IA para Azure IoT Edge](https://aka.ms/aml-iot-edge-anomaly-detection) para encontrar la imagen.
1. Haga clic en **Guardar**.
1. Vuelva al paso **Add Modules** (Agregar módulos) y seleccione **Siguiente**.
1. Actualice las rutas para el módulo:
1. En el paso **Specify Routes** (Especificar rutas), copie el archivo JSON siguiente en el cuadro de texto. Los módulos publican todos los mensajes en el runtime de Edge. Las reglas declarativas del runtime definen dónde fluyen esos mensajes. En este tutorial, necesita dos rutas. La primera ruta transporta los mensajes del sensor de temperatura al módulo de Machine Learning a través del punto de conexión "amlInput", que es el punto de conexión que utilizan todos los módulos de Azure Machine Learning. La segunda ruta transporta mensajes del módulo de Machine Learning a IoT Hub. En esta ruta, ''amlOutput'' es el punto de conexión que usan todos los módulos de Azure Machine Learning para los datos de salida y "$upstream" es un destino especial que indica a Edge Hub que envíe mensajes a IoT Hub. 

    ```json
    {
        "routes": {
            "sensorToMachineLearning":"FROM /messages/modules/tempSensor/outputs/temperatureOutput INTO BrokeredEndpoint(\"/modules/machinelearningmodule/inputs/amlInput\")",
            "machineLearningToIoTHub": "FROM /messages/modules/machinelearningmodule/outputs/amlOutput INTO $upstream"
        }
    }
    ``` 

1. Haga clic en **Siguiente**. 
1. En el paso "Review Template" (Revisar plantilla), haga clic en "Enviar". 
1. Vuelva a la página de detalles del dispositivo de IoT Edge y haga clic en "Actualizar".  Debería ver el nuevo módulo "machinelearningmodule" en ejecución junto con el "módulo tempSensor" y el "runtime de IoT Edge".

## <a name="view-generated-data"></a>Visualización de datos generados

 En VS Code, utilice el comando de menú **Ver | Paleta de comandos... | IoT: inicio de la supervisión del mensaje de D2C** para supervisar los datos que llegan a IoT Hub. 

## <a name="next-steps"></a>Pasos siguientes

En este tutorial, ha implementado un módulo de IoT Edge con tecnología de Azure Machine Learning. Puede continuar con cualquiera de los otros tutoriales para aprender otras formas en las que Azure IoT Edge puede ayudarle a convertir los datos en perspectivas empresariales en el perímetro.

> [!div class="nextstepaction"]
> [Implementación de una función de Azure como un módulo](tutorial-deploy-function.md)

<!--Links-->
[lnk-tutorial1-win]: tutorial-simulate-device-windows.md
[lnk-tutorial1-lin]: tutorial-simulate-device-linux.md
