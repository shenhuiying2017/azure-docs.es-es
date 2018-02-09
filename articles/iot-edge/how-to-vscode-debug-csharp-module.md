---
title: "Uso de Visual Studio Code para depurar un módulo de C# con Azure IoT Edge | Microsoft Docs"
description: "Depure un módulo de C# con Azure IoT Edge en Visual Studio Code."
services: iot-edge
keywords: 
author: shizn
manager: timlt
ms.author: xshi
ms.date: 12/06/2017
ms.topic: article
ms.service: iot-edge
ms.openlocfilehash: 46d9ca0bf6c9ddf95c147fc2eb62d275c973845e
ms.sourcegitcommit: 9d317dabf4a5cca13308c50a10349af0e72e1b7e
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 02/01/2018
---
# <a name="use-visual-studio-code-to-debug-a-c-module-with-azure-iot-edge"></a>Uso de Visual Studio Code para depurar un módulo de C# con Azure IoT Edge
En este artículo se proporcionan instrucciones detalladas para usar [Visual Studio Code](https://code.visualstudio.com/) como la herramienta de desarrollo principal para depurar los módulos de Azure IoT Edge.

## <a name="prerequisites"></a>requisitos previos
En este tutorial se presupone que utiliza un equipo o una máquina virtual con Windows o Linux como máquina de desarrollo. El dispositivo de IoT Edge puede ser otro dispositivo físico o una simulación de dicho dispositivo en el equipo de desarrollo.

Antes de empezar esta guía, complete el siguiente tutorial:
- [Uso de Visual Studio Code para desarrollar un módulo de C# con Azure IoT Edge](how-to-vscode-develop-csharp-module.md)

Una vez completado el tutorial anterior, debe tener listos los siguientes elementos:
- Un registro de Docker local que se ejecuta en el equipo de desarrollo. Esto es para fines de creación de prototipos y pruebas.
- El archivo `Program.cs` con el código más reciente del módulo de filtro.
- Un archivo `deployment.json` actualizado para los módulos de sensor y filtro.
- Una versión en tiempo de ejecución de IoT Edge que se ejecute en el equipo de desarrollo.

## <a name="build-your-iot-edge-module-for-debugging"></a>Creación de un módulo de IoT Edge para depuración
1. Para iniciar la depuración, use **dockerfile.debug** para recompilar la imagen de Docker e implementar de nuevo la solución IoT Edge. En el Explorador de Visual Studio Code, seleccione la carpeta de Docker para abrirla. Después, seleccione la carpeta **linux-x64**, haga clic con el botón derecho en **Dockerfile.debug** y luego seleccione **Build IoT Edge module Docker image** (Generar imagen de Docker del módulo de IoT Edge).

    ![Captura de pantalla del Explorador de VS Code](./media/how-to-debug-csharp-module/build-debug-image.png)

3. En la ventana **Seleccionar carpeta**, vaya a **./bin/Debug/netcoreapp2.0/publish** o especifíquelo. Después, seleccione **Select Folder as EXE_DIR** (Seleccionar carpeta como EXE_DIR).
4. En el cuadro de texto emergente de la parte superior de la ventana de VS Code, escriba el nombre de la imagen. Por ejemplo: `<your container registry address>/filtermodule:latest`. Si va a realizar la implementación en el registro local, debe ser: `localhost:5000/filtermodule:latest`.
5. Inserte la imagen en el repositorio de Docker. Use el comando **Edge: Push IoT Edge module Docker image** (Edge: insertar la imagen de Docker del módulo con IoT Edge) y escriba la dirección URL de la imagen en el cuadro de texto emergente de la parte superior de la ventana de VS Code. Use la misma dirección URL de la imagen que utilizó en el paso anterior.
6. Puede reutilizar `deployment.json` para la reimplementación. En la paleta de comandos, escriba y seleccione **Edge: Restart Edge** (Edge: reiniciar Edge) para obtener el módulo de filtro que se ejecuta con la versión de depuración.

## <a name="start-debugging-in-vs-code"></a>Inicio de la depuración en VS Code
1. Vaya a la ventana de depuración de VS Code. Presione **F5** y seleccione **IoT Edge (.NET Core)**.

    ![Captura de pantalla de la ventana de depuración de VS Code](./media/how-to-debug-csharp-module/f5-debug-option.png)

2. En `launch.json`, vaya a la sección **Debug IoT Edge Custom Module (.NET Core)** [Depurar el módulo personalizado de IoT Edge (.NET Core)]. En **pipeArgs**, rellene `<container_name>`. Debe ser `filtermodule` en este tutorial.

    ![Captura de pantalla de la ventana de depuración de VS Code](./media/how-to-debug-csharp-module/f5-debug-option.png)

3. Vaya a **Program.cs**. Agregue un punto de interrupción en `method static async Task<MessageResponse> FilterModule(Message message, object userContext)`.
4. Vuelta a presionar **F5** y seleccione el proceso al que desea agregarlo. En este tutorial, el nombre del proceso debe ser `FilterModule.dll`.

    ![Captura de pantalla de la ventana de depuración de VS Code](./media/how-to-debug-csharp-module/attach-process.png)

5. En la ventana de depuración de VS Code, puede ver las variables en el panel izquierdo. 

> [!NOTE]
> En el ejemplo anterior se muestra cómo depurar módulos de IoT Edge en .NET Core en contenedores. Se basa en la versión de depuración de `Dockerfile.debug`, que incluye VSDBG (el depurador de línea de comandos de .NET Core) en la imagen de contenedor al realizar la compilación. Una vez finalizada la depuración de los módulos de C#, se recomienda utilizar directamente o personalizar `Dockerfile` sin VSDBG para módulos de IoT Edge listos para entornos de producción.

## <a name="next-steps"></a>pasos siguientes

En este tutorial, creó un módulo con IoT Edge y lo implementó para la depuración. Empezó a depurarlo en VS Code. Para obtener información sobre otros escenarios al desarrollar Azure IoT Edge en VS Code, vea: 

> [!div class="nextstepaction"]
> [Desarrollo e implementación de un módulo de C# en VS Code](how-to-vscode-develop-csharp-module.md)
