---
title: Administración de varios módulos de Azure IoT Edge en VS Code | Microsoft Docs
description: Use Visual Studio Code para desarrollar soluciones de IoT Edge que usan varios módulos.
services: iot-edge
keywords: ''
author: shizn
manager: timlt
ms.author: xshi
ms.date: 03/18/2018
ms.topic: article
ms.service: iot-edge
ms.openlocfilehash: 4d9caa7aa95c99fa30e8ec76c5b6362615725622
ms.sourcegitcommit: d78bcecd983ca2a7473fff23371c8cfed0d89627
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/14/2018
ms.locfileid: "34166359"
---
# <a name="develop-an-iot-edge-solution-with-multiple-modules-in-visual-studio-code---preview"></a>Desarrollo de una solución de IoT Edge con varios módulos en Visual Studio Code: versión preliminar
Puede utilizar Visual Studio Code para desarrollar su solución de IoT Edge con varios módulos. Este artículo le guía en la creación, la actualización y la implementación de una solución de IoT Edge que canaliza los datos de los sensores en un dispositivo IoT Edge simulado en Visual Studio Code. 

## <a name="prerequisites"></a>requisitos previos

Para completar todos los pasos de este artículo, debe satisfacer los siguientes requisitos previos:

- [Visual Studio Code](https://code.visualstudio.com/) 
- [Extensión de Azure IoT Edge para Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-edge) 
- [Extensión de C# para Visual Studio Code (con tecnología de OmniSharp)](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp) 
- [Docker](https://docs.docker.com/engine/installation/)
- [SDK de .NET Core 2.0](https://www.microsoft.com/net/core#windowscmd) 
- Plantilla AzureIoTEdgeModule (`dotnet new -i Microsoft.Azure.IoT.Edge.Module`)
- Una instancia de IoT Hub activa con al menos un dispositivo de IoT Edge


* [Docker para VS Code](https://marketplace.visualstudio.com/items?itemName=PeterJausovec.vscode-docker) con integración con el explorador para administrar imágenes y contenedores.


## <a name="prepare-your-first-iot-edge-solution"></a>Preparación de su primera solución de IoT Edge
1. En la paleta de comandos de VS Code, escriba y ejecute el comando **Edge: New IoT Edge solution**. A continuación, seleccione la carpeta del área de trabajo, proporcione el nombre de la solución (el nombre predeterminado es **EdgeSolution**) y cree un módulo de C# (**SampleModule**) como primer módulo del usuario en esta solución. También debe especificar el repositorio de imágenes de Docker para el primer módulo. El repositorio de imágenes predeterminado se basa en un registro de Docker local (`localhost:5000/<first module name>`). También puede cambiarlo al registro de contenedor de Azure o Docker Hub.

   > [!NOTE]
   > Si usa un registro de Docker local, asegúrese de que se está ejecutando; para ello, escriba el comando `docker run -d -p 5000:5000 --restart=always --name registry registry:2` en la ventana de consola.

2. La ventana de Visual Studio Code carga el área de trabajo de la solución de IoT Edge. La carpeta raíz contiene una carpeta `modules`, una carpeta `.vscode` y un archivo de plantilla de manifiesto de implementación. Puede ver configuraciones de depuración en la carpeta `.vscode`. Todos los códigos del módulo de usuario serán subcarpetas en la carpeta `modules`. `deployment.template.json` es la plantilla del manifiesto de implementación. Algunos de los parámetros de este archivo se analizarán desde `module.json`, que existe en cada carpeta del módulo.

3. Agregue el segundo módulo en este proyecto de la solución. Esta vez, escriba y ejecute **Edge: Add IoT Edge module** y seleccione el archivo de plantilla de implementación que se va a actualizar. A continuación, seleccione una instancia de **Azure Function - C#** con el nombre **SampleFunction** y su repositorio de imágenes de Docker.

4. Abra el archivo `deployment.template.json` y compruebe que se declaran tres módulos además del tiempo de ejecución. El mensaje se generará a partir del módulo `tempSensor`, y se canalizará directamente mediante `SampleModule` y `SampleFunction`; luego, se enviará a su centro de IoT. 
5. Actualice las rutas a estos módulos con el contenido siguiente:
   ```json
   "routes": {
      "SensorToPipeModule": "FROM /messages/modules/tempSensor/outputs/temperatureOutput INTO BrokeredEndpoint(\"/modules/SampleModule/inputs/input1\")",
      "PipeModuleToPipeFunction": "FROM /messages/modules/SampleModule/outputs/output1 INTO BrokeredEndpoint(\"/modules/SampleFunction/inputs/input1\")",
      "PipeFunctionToIoTHub": "FROM /messages/modules/SampleFunction/outputs/output1 INTO $upstream"
   },
   ```

6. Guarde este archivo.

## <a name="build-and-deploy-your-iot-edge-solution"></a>Compilación e implementación de la solución de IoT Edge
1. En la paleta de comandos de VS Code, escriba y ejecute el comando **Edge: Build IoT Edge solution**. Este comando toma como base el archivo `module.json` de cada carpeta del módulo, y empieza a compilar, incluir en contenedores e insertar cada imagen de Docker del módulo. A continuación, pasa el valor necesario a `deployment.template.json` y genera el archivo `deployment.json` con la información de la carpeta `config`. Puede ver el progreso de compilación en el terminal integrado de Visual Studio Code.

2. En el Explorador de dispositivos de Azure IoT Hub, haga clic con el botón derecho en un identificador de dispositivo de IoT Edge y, a continuación, seleccione **Create deployment for Edge device** (Crear implementación para dispositivo Edge). Seleccione el archivo `deployment.json` en la carpeta `config`. A continuación, puede ver que la implementación se ha creado correctamente con un identificador de implementación en el terminal integrado de VS Code.

3. Si va a simular un dispositivo de IoT Edge en la máquina de desarrollo, verá que todos los contenedores de imágenes de módulo se inician en unos minutos.

## <a name="view-generated-data"></a>Visualización de datos generados

1. Para supervisar los datos que llegan a IoT Hub, seleccione **Ver** > **Paleta de comandos** y busque **IoT: Start monitoring D2C message** (IoT: iniciar la supervisión del mensaje de D2C). 
2. Para dejar de supervisar datos, use el comando **IoT: Stop monitoring D2C message** (IoT: detener la supervisión del mensaje de D2C) en la paleta de comandos. 

## <a name="next-steps"></a>Pasos siguientes

Conozca otros escenarios de desarrollo de Azure IoT Edge en Visual Studio Code:

* [Depuración de un módulo de C# en VS Code](how-to-vscode-debug-csharp-module.md)
* [Depuración de una función de C# en VS Code](how-to-vscode-debug-azure-function.md)