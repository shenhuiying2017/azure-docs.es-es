---
title: Uso de Visual Studio Code para depurar Azure Functions con Azure IoT Edge | Microsoft Docs
description: Depuración de Azure Functions en C# con Azure IoT Edge en Visual Studio Code
services: iot-edge
keywords: ''
author: shizn
manager: timlt
ms.author: xshi
ms.date: 3/20/2018
ms.topic: article
ms.service: iot-edge
ms.openlocfilehash: 8da16ffe72ad265f0201c2fe7e00e585dfa255e8
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 03/28/2018
---
# <a name="use-visual-studio-code-to-debug-azure-functions-with-azure-iot-edge"></a>Uso de Visual Studio Code para depurar Azure Functions con Azure IoT Edge

En este artículo se proporcionan instrucciones detalladas para usar [Visual Studio Code](https://code.visualstudio.com/) como la herramienta de desarrollo principal para depurar Azure Functions en IoT Edge.

## <a name="prerequisites"></a>requisitos previos
En este tutorial se presupone que utiliza un equipo o una máquina virtual con Windows o Linux como máquina de desarrollo. El dispositivo de IoT Edge podría ser otro dispositivo físico o una simulación en la máquina de desarrollo.

Asegúrese de haber completado los siguientes tutoriales antes de empezar con esta guía.
- [Desarrollo de una solución de IoT Edge con varios módulos en Visual Studio Code](tutorial-multiple-modules-in-vscode.md)

Una vez completado el tutorial anterior, debe tener listos los siguientes elementos:
- Un registro de Docker local que se ejecuta en el equipo de desarrollo. Se recomienda usar un registro de Docker local para fines de prototipo y pruebas. Puede actualizar el registro de contenedores en el archivo `module.json` en cada carpeta del módulo.
- Un área de trabajo del proyecto de solución de IoT Edge con una subcarpeta del módulo Azure Function en ella.
- El archivo `run.csx` con el código de la función.
- Una versión en tiempo de ejecución de Edge que se ejecute en el equipo de desarrollo.

## <a name="build-your-iot-edge-function-module-for-debugging-purpose"></a>Compilación de un módulo de función de IoT Edge para fines de depuración
1. Para iniciar la depuración, debe usar el archivo **Dockerfile.amd64.debug** para recompilar la imagen de Docker e implementar de nuevo la solución de Edge. En el explorador de VS Code, navegue al archivo `deployment.template.json`. Para actualizar la dirección URL de la imagen de la función, agregue un `.debug` al final.

    ![Compilar una imagen de depuración](./media/how-to-debug-csharp-function/build-debug-image.png)

2. Recompile la solución. En la paleta de comandos de VS Code, escriba y ejecute el comando **Edge: Build IoT Edge solution**.

3. En el Explorador de dispositivos de Azure IoT Hub, haga clic con el botón derecho en un identificador de dispositivo de IoT Edge y, a continuación, seleccione **Create deployment for Edge device** (Crear implementación para dispositivo Edge). Seleccione el archivo `deployment.json` en la carpeta `config`. A continuación, puede ver que la implementación se ha creado correctamente con un identificador de implementación en el terminal integrado de VS Code.

> [!NOTE]
> Puede comprobar el estado del contenedor en el Explorador de Docker de VS Code o ejecutando el comando `docker images` en el terminal.

## <a name="start-debugging-c-function-in-vs-code"></a>Inicio de la depuración de la función de C# en VS Code
1. VS Code sigue depurando la información de configuración en un archivo `launch.json` ubicado en una carpeta `.vscode` en el área de trabajo. Este archivo `launch.json` se ha generado al crear una nueva solución de IoT Edge. Este se actualizará cada vez que agregue un nuevo módulo que admita la depuración. Vaya a la vista de depuración y seleccione el archivo de configuración de depuración correspondiente.
    ![Selección de la configuración de depuración](./media/how-to-debug-csharp-function/select-debug-configuration.jpg)

2. Vaya a `run.csx`. Agregue un punto de interrupción en la función.

3. Haga clic en el botón Iniciar depuración o presione **F5** y seleccione el proceso que se adjuntará.

4. En la vista de depuración de VS Code, puede ver las variables en el panel izquierdo. 


> [!NOTE]
> En el ejemplo anterior se muestra cómo depurar la función de IoT Edge en .Net Core en contenedores. Se basa en la versión de depuración de `Dockerfile.amd64.debug`, que incluye VSDBG (el depurador de línea de comandos de .NET Core) en la imagen de contenedor al realizar la compilación. Se recomienda utilizar directamente o personalizar `Dockerfile` sin VSDBG para funciones de IoT Edge listas para entornos de producción cuando termine de depurar las funciones de C#.

## <a name="next-steps"></a>Pasos siguientes

En este tutorial, ha creado una Azure Function y la ha implementado en IoT Edge para fines de depuración, y ha empezado a depurarla en VS Code. Puede continuar con cualquiera de los siguientes tutoriales para obtener información sobre otros escenarios al desarrollar Azure IoT Edge en VS Code. 

> [!div class="nextstepaction"]
> [Desarrollo de una solución de IoT Edge con varios módulos en Visual Studio Code](tutorial-multiple-modules-in-vscode.md)

