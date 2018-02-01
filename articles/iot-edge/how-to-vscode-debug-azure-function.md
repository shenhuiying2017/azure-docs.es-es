---
title: Uso de Visual Studio Code para depurar Azure Functions con Azure IoT Edge | Microsoft Docs
description: "Depuración de Azure Functions en C# con Azure IoT Edge en Visual Studio Code"
services: iot-edge
keywords: 
author: shizn
manager: timlt
ms.author: xshi
ms.date: 12/20/2017
ms.topic: article
ms.service: iot-edge
ms.openlocfilehash: db86a08a19e97f8f415849aa060fe87d77cccf68
ms.sourcegitcommit: 28178ca0364e498318e2630f51ba6158e4a09a89
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/24/2018
---
# <a name="use-visual-studio-code-to-debug-azure-functions-with-azure-iot-edge"></a>Uso de Visual Studio Code para depurar Azure Functions con Azure IoT Edge

En este artículo se proporcionan instrucciones detalladas para usar [Visual Studio Code](https://code.visualstudio.com/) como la herramienta de desarrollo principal para depurar Azure Functions en IoT Edge.

## <a name="prerequisites"></a>requisitos previos
En este tutorial se presupone que utiliza un equipo o una máquina virtual con Windows o Linux como máquina de desarrollo. El dispositivo de IoT Edge podría ser otro dispositivo físico o una simulación en la máquina de desarrollo.

Asegúrese de haber completado los siguientes tutoriales antes de empezar con esta guía.
- [Uso de Visual Studio Code para desarrollar y depurar Azure Functions en Azure IoT Edge](how-to-vscode-develop-azure-function.md)

Una vez completado el tutorial anterior, debe tener listos los siguientes elementos:
- Un registro de Docker local que se ejecuta en el equipo de desarrollo. Se recomienda usar un registro de Docker local para fines de prototipo y pruebas.
- El archivo `run.csx` con el código más reciente de función de filtro.
- Un archivo `deployment.json` actualizado para el módulo de sensor y el módulo de función de filtro.
- Una versión en tiempo de ejecución de Edge que se ejecute en el equipo de desarrollo.

## <a name="build-your-iot-edge-module-for-debugging-purpose"></a>Creación de un módulo de IoT Edge para fines de depuración
1. Para iniciar la depuración, debe usar el archivo **dockerfile.debug** para recompilar la imagen de Docker e implementar de nuevo la solución Edge. En el explorador de VS Code, haga clic en la carpeta Docker para abrirla. Después, haga clic en la carpeta `linux-x64`, haga clic con el botón derecho en el archivo **Dockerfile.debug** y luego haga clic en **Build IoT Edge module Docker image** (Generar imagen de Docker del módulo de IoT Edge).

    ![Compilar una imagen de depuración](./media/how-to-debug-csharp-function/build-debug-image.png)

2. En la ventana **Seleccionar carpeta**, vaya al proyecto **FilterFunction** y haga clic en **Select Folder as EXE_DIR** (Seleccionar carpeta como EXE_DIR).
3. En el cuadro de texto emergente de la parte superior de la ventana de VS Code, escriba el nombre de la imagen. Por ejemplo: `<your container registry address>/filterfunction:latest`. Si va a realizar la implementación en el registro local, debe ser `localhost:5000/filterfunction:latest`.

    ![Imagen de inserción](./media/how-to-debug-csharp-function/push-image.png)

4. Inserte la imagen en el repositorio de Docker. Use el comando **Edge: Push IoT Edge module Docker image** (Edge: insertar la imagen de Docker del módulo con IoT Edge) y escriba la dirección URL de la imagen en el cuadro de texto emergente de la parte superior de la ventana de VS Code. Use la misma dirección URL de la imagen que utilizó en el paso anterior.
5. Puede reutilizar `deployment.json` para la reimplementación. En la paleta de comandos, escriba y seleccione **Edge: Restart Edge** (Edge: reiniciar Edge) para obtener la función de filtro que se ejecuta con la versión de depuración.

## <a name="start-debugging-in-vs-code"></a>Inicio de la depuración en VS Code
1. Vaya a la ventana de depuración de VS Code. Presione **F5** y seleccione **IoT Edge (.Net Core)**.

    ![Presione F5.](./media/how-to-debug-csharp-function/f5-debug-option.png)

2. En `launch.json`, vaya a la sección **Debug IoT Edge Function (.NET Core)** (Depuración de la función de IoT Edge (.NET Core)) y rellene `<container_name>` en `pipeArgs`. Debe ser `filterfunction` en este tutorial.

    ![Actualice launch.json.](./media/how-to-debug-csharp-function/update-launch-json.png)

3. Vaya a run.csx. Agregue un punto de interrupción en la función.
4. Navegue a la ventana de depuración (Ctrl + Mayús + D) y elija **Debug IoT Edge Function (.NET Core)** (Depurar función de IoT Edge [.NET Core]) en la lista desplegable. 

    ![Seleccionar el modo de depuración](./media/how-to-debug-csharp-function/choose-debug-mode.png)

5. Haga clic en el botón Iniciar depuración o presione **F5** y seleccione el proceso que se adjuntará.

    ![Adjuntar el proceso de función](./media/how-to-debug-csharp-function/attach-function-process.png)

6. En la ventana de depuración de VS Code, puede ver las variables en el panel izquierdo. 

> [!NOTE]
> En el ejemplo anterior se muestra cómo depurar la función de IoT Edge en .Net Core en contenedores. Se basa en la versión de depuración de `Dockerfile.debug`, que incluye VSDBG (el depurador de línea de comandos de .NET Core) en la imagen de contenedor al realizar la compilación. Se recomienda utilizar directamente o personalizar `Dockerfile` sin VSDBG para funciones de IoT Edge listas para entornos de producción cuando termine de depurar las funciones de C#.

## <a name="next-steps"></a>pasos siguientes

En este tutorial, ha creado una Azure Function y la ha implementado en IoT Edge para fines de depuración, y ha empezado a depurarla en VS Code. Puede continuar con cualquiera de los siguientes tutoriales para obtener información sobre otros escenarios al desarrollar Azure IoT Edge en VS Code. 

> [!div class="nextstepaction"]
> [Desarrollo e implementación de un módulo de C# en VS Code](how-to-vscode-develop-csharp-module.md)

