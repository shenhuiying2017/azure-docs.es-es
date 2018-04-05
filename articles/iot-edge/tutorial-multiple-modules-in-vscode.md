---
title: Trabajo con varios módulos de IoT Edge en Visual Studio Code | Microsoft Docs
description: Implementación de Azure Machine Learning como módulo en un dispositivo perimetral
services: iot-edge
keywords: ''
author: shizn
manager: timlt
ms.author: xshi
ms.date: 03/18/2018
ms.topic: article
ms.service: iot-edge
ms.openlocfilehash: 0ea2dc723c674e7119b6ef38771a73ff4c11e98d
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 03/28/2018
---
# <a name="develop-an-iot-edge-solution-with-multiple-modules-in-visual-studio-code---preview"></a>Desarrollo de una solución de IoT Edge con varios módulos en Visual Studio Code: versión preliminar
Puede utilizar Visual Studio Code para desarrollar su solución de IoT Edge con varios módulos. Este tutorial le guía a través de la creación, la actualización y la implementación de una solución de IoT Edge que simplemente canaliza los datos de los sensores en un dispositivo IoT Edge simulado en Visual Studio Code. En este tutorial, aprenderá a:

> [!div class="checklist"]
> * Usar Visual Studio Code para crear una solución de IoT Edge
> * Usar Visual Studio Code para agregar un nuevo módulo a su solución de IoT Edge de trabajo 
> * Implementar la solución de IoT Edge (varios módulos) al dispositivo de IoT Edge
> * Visualización de datos generados

## <a name="prerequisites"></a>requisitos previos
* Complete los tutoriales siguientes:
  * [Desarrollar e implementar un módulo IoT Edge C# en su dispositivo simulado: versión preliminar](tutorial-csharp-module.md)
  * [Implementación de Azure Functions como módulo de IoT Edge (versión preliminar)](tutorial-deploy-function.md)
  * [Develop and deploy a Python IoT Edge module to your simulated device - preview](tutorial-python-module.md) (Desarrollo e implementación de un módulo IoT Edge de Python en su dispositivo simulado: versión preliminar)
* [Docker para VS Code](https://marketplace.visualstudio.com/items?itemName=PeterJausovec.vscode-docker) con integración con el explorador para administrar imágenes y contenedores.


## <a name="prepare-your-first-iot-edge-solution"></a>Preparación de su primera solución de IoT Edge
1. En la paleta de comandos de VS Code, escriba y ejecute el comando **Edge: New IoT Edge solution**. A continuación, seleccione la carpeta del área de trabajo, proporcione el nombre de la solución (el nombre predeterminado es **EdgeSolution**) y cree un módulo de C# (**SampleModule**) como primer módulo del usuario en esta solución. También debe especificar el repositorio de imágenes de Docker para el primer módulo. El repositorio de imágenes predeterminado se basa en un registro de Docker local (`localhost:5000/<first module name>`). También puede cambiarlo al registro de contenedor de Azure o Docker Hub.

> [!NOTE]
> Si usa un registro de Docker local, asegúrese de que el registro se está ejecutando; para ello, escriba el comando `docker run -d -p 5000:5000 --restart=always --name registry registry:2` en la ventana de consola.

2. La ventana de Visual Studio Code cargará el área de trabajo de la solución de IoT Edge. Hay una carpeta `modules`, una carpeta `.vscode` y un archivo de plantilla de manifiesto de implementación en la carpeta raíz. Puede ver configuraciones de depuración en la carpeta `.vscode`. Todos los códigos del módulo de usuario serán subcarpetas en la carpeta `modules`. `deployment.template.json` es la plantilla del manifiesto de implementación. Algunos de los parámetros de este archivo se analizarán desde `module.json`, que existe en cada carpeta del módulo.

3. Agregue el segundo módulo en este proyecto de la solución. Esta vez, escriba y ejecute **Edge: Add IoT Edge module** y seleccione el archivo de plantilla de implementación que se va a actualizar. A continuación, seleccione una instancia de**Azure Function - C#** con el nombre **SampleFunction** y su repositorio de imágenes de Docker para agregar.

4. Ya está lista su primera solución de IoT Edge con dos módulos básicos. El módulo predeterminado de C# actúa como un módulo de mensajes de canalización, mientras que C# Functions actúa como una función de mensajes de canalización. En `deployment.template.json`, verá que esta solución contiene tres módulos. El mensaje se generará a partir del módulo `tempSensor`, y se canalizará directamente mediante `SampleModule` y `SampleFunction`; luego, se enviará a su centro de IoT. Actualice las rutas de estos módulos con el contenido siguiente. 
   ```json
        "routes": {
          "SensorToPipeModule": "FROM /messages/modules/tempSensor/outputs/temperatureOutput INTO BrokeredEndpoint(\"/modules/SampleModule/inputs/input1\")",
          "PipeModuleToPipeFunction": "FROM /messages/modules/SampleModule/outputs/output1 INTO BrokeredEndpoint(\"/modules/SampleFunction/inputs/input1\")",
          "PipeFunctionToIoTHub": "FROM /messages/modules/SampleFunction/outputs/output1 INTO $upstream"
        },
   ```

5. Guarde este archivo.

## <a name="build-and-deploy-your-iot-edge-solution"></a>Compilación e implementación de la solución de IoT Edge
1. En la paleta de comandos de VS Code, escriba y ejecute el comando **Edge: Build IoT Edge solution**. Tomando como base el archivo `module.json` en cada carpeta del módulo, este comando comprobará y empezará a compilar, incluir en contenedores e insertar cada imagen de Docker del módulo. A continuación, analizará el valor necesario en `deployment.template.json` y generará `deployment.json` con el valor real en la carpeta `config`. Puede ver el progreso de compilación en el terminal integrado de Visual Studio Code.

2. En el Explorador de dispositivos de Azure IoT Hub, haga clic con el botón derecho en un identificador de dispositivo de IoT Edge y, a continuación, seleccione **Create deployment for Edge device** (Crear implementación para dispositivo Edge). Seleccione el archivo `deployment.json` en la carpeta `config`. A continuación, puede ver que la implementación se ha creado correctamente con un identificador de implementación en el terminal integrado de VS Code.

3. Si está [simulando un dispositivo de IoT Edge](tutorial-simulate-device-linux.md) en el equipo de desarrollo. Verá que todos los contenedores de imagen del módulo se iniciarán en unos minutos.

## <a name="view-generated-data"></a>Visualización de datos generados

1. Para supervisar los datos que llegan a IoT Hub, seleccione **Ver** > **Paleta de comandos** y busque **IoT: Start monitoring D2C message** (IoT: iniciar la supervisión del mensaje de D2C). 
2. Para dejar de supervisar datos, use el comando **IoT: Stop monitoring D2C message** (IoT: detener la supervisión del mensaje de D2C) en la paleta de comandos. 

## <a name="next-steps"></a>Pasos siguientes

En este tutorial, ha creado una solución de IoT Edge con un módulo C#, más adelante ha agregado un módulo de Functions, ha actualizado las rutas de la solución y ha compilado e implementado en el dispositivo de IoT Edge simulado. Puede continuar con cualquiera de los siguientes tutoriales para obtener información sobre otros escenarios al desarrollar Azure IoT Edge en VS Code.

> [!div class="nextstepaction"]
> [Uso de Visual Studio Code para depurar un módulo de C# con Azure IoT Edge](how-to-vscode-debug-csharp-module.md)
> [Uso de Visual Studio Code para depurar Azure Functions con Azure IoT Edge](how-to-vscode-debug-azure-function.md)