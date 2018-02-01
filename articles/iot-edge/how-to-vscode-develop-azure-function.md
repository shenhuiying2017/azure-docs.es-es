---
title: Uso de Visual Studio Code para desarrollar e implementar Azure Functions en Azure IoT Edge | Microsoft Docs
description: "Desarrollo e implementación de Azure Functions en C# con Azure IoT Edge en VS Code sin cambiar de contexto"
services: iot-edge
keywords: 
author: shizn
manager: timlt
ms.author: xshi
ms.date: 12/20/2017
ms.topic: article
ms.service: iot-edge
ms.openlocfilehash: 219474a4577a76f5ceb9a9efaa3c349d633de047
ms.sourcegitcommit: 28178ca0364e498318e2630f51ba6158e4a09a89
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/24/2018
---
# <a name="use-visual-studio-code-to-develop-and-deploy-azure-functions-to-azure-iot-edge"></a>Uso de Visual Studio Code para desarrollar e implementar Azure Functions en Azure IoT Edge

En este artículo se proporcionan instrucciones detalladas para usar [Visual Studio Code](https://code.visualstudio.com/) como la herramienta de desarrollo principal para desarrollar e implementar Azure Functions en IoT Edge. 

## <a name="prerequisites"></a>requisitos previos
En este tutorial se presupone que utiliza un equipo o una máquina virtual con Windows o Linux como máquina de desarrollo. El dispositivo de IoT Edge podría ser otro dispositivo físico o una simulación en la máquina de desarrollo.

Asegúrese de haber completado los siguientes tutoriales antes de empezar con esta guía.
- Implementación de Azure IoT Edge en un dispositivo simulado en [Windows](https://docs.microsoft.com/azure/iot-edge/tutorial-simulate-device-windows) o [Linux](https://docs.microsoft.com/azure/iot-edge/tutorial-simulate-device-linux)
- [Implementación de Azure Functions](https://docs.microsoft.com/azure/iot-edge/tutorial-deploy-function)

Esta es una lista de comprobación que muestra los elementos que deben constar una vez terminados los tutoriales anteriores.

- [Visual Studio Code](https://code.visualstudio.com/). 
- [Extensión de Azure IoT Edge para Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-edge). 
- [Extensión de C# para Visual Studio Code (con tecnología de OmniSharp)](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp) 
- [Docker](https://docs.docker.com/engine/installation/)
- [SDK de .NET Core 2.0](https://www.microsoft.com/net/core#windowscmd). 
- [Python 2.7](https://www.python.org/downloads/)
- [Script de control de IoT Edge](https://pypi.python.org/pypi/azure-iot-edge-runtime-ctl)
- Plantilla de AzureIoTEdgeFunction (`dotnet new -i Microsoft.Azure.IoT.Edge.Function`)
- Un centro de IoT activo con al menos un dispositivo de IoT Edge

También se recomienda instalar la [compatibilidad Docker para VS Code](https://marketplace.visualstudio.com/items?itemName=PeterJausovec.vscode-docker) para administrar mejor las imágenes del módulo y los contenedores.

> [!NOTE]
> Actualmente, Azure Functions en IoT Edge solo admite C#.

## <a name="deploy-azure-iot-functions-in-vs-code"></a>Implementación de Azure Functions de IoT en VS Code
En el tutorial [Implementación de Azure Functions](https://docs.microsoft.com/azure/iot-edge/tutorial-deploy-function), actualice, compile y publique las imágenes del módulo de la función en Visual Studio Code y después visite Azure Portal para implementar Azure Functions. En esta sección se explica cómo utilizar VS Code para implementar y supervisar Azure Functions.

### <a name="start-a-local-docker-registry"></a>Inicio de un registro de Docker local
Puede usar cualquier registro compatible con Docker para este tutorial. Dos servicios populares del Registro de Docker disponibles en la nube son [Azure Container Registry](https://docs.microsoft.com/azure/container-registry/) y [Docker Hub](https://docs.docker.com/docker-hub/repos/#viewing-repository-tags). En esta sección se usa un [registro de Docker local](https://docs.docker.com/registry/deploying/), que es más fácil para los fines de prueba durante el desarrollo temprano.
En el **terminal integrado** (Ctrl + `) de VS Code, ejecute los siguientes comandos para iniciar un registro local.  

```cmd/sh
docker run -d -p 5000:5000 --name registry registry:2 
```

> [!NOTE]
> En el ejemplo anterior se muestran configuraciones de registro que solo son adecuadas para las pruebas. Un registro para entornos de producción debe estar protegido con TLS y lo ideal es usar un mecanismo de control de acceso. Se recomienda usar [Azure Container Registry](https://docs.microsoft.com/azure/container-registry/) o [Docker Hub](https://docs.docker.com/docker-hub/repos/#viewing-repository-tags) para implementar módulos con IoT Edge para entornos de producción.

### <a name="create-a-function-project"></a>Creación de un proyecto de aplicación de una función
En los siguientes pasos puede ver cómo crear un módulo IoT Edge basado en .NET Core 2.0 mediante Visual Studio Code y la extensión de Azure IoT Edge. Si ha completado esta sección en el tutorial anterior, puede omitir esta sección sin problema.

1. En Visual Studio Code, seleccione **Ver** > **Terminal integrado** para abrir el terminal integrado de VS Code.
2. Para instalar o actualizar la plantilla **AzureIoTEdgeFunction** en DotNet, ejecute el comando siguiente en el terminal integrado:

   ```cmd/sh
   dotnet new -i Microsoft.Azure.IoT.Edge.Function
   ```
3. Cree un proyecto para el nuevo módulo. El comando siguiente crea la carpeta del proyecto, **FilterFunction**, en la carpeta de trabajo actual:

   ```cmd/sh
   dotnet new aziotedgefunction -n FilterFunction
   ```
 
4. Seleccione **Archivo > Abrir carpeta** y, a continuación, busque la carpeta **FilterFunction** y abra el proyecto en VS Code.
5. Vaya a la carpeta **FilterFunction** y haga clic en **Seleccionar carpeta** para abrir el proyecto en VS Code.
6. En el explorador de VS Code, expanda la carpeta **EdgeHubTrigger-Csharp** y, después, abra el archivo **run.csx**.
7. Reemplace el contenido del archivo por el código siguiente:

   ```csharp
   #r "Microsoft.Azure.Devices.Client"
   #r "Newtonsoft.Json"

   using System.IO;
   using Microsoft.Azure.Devices.Client;
   using Newtonsoft.Json;

   // Filter messages based on the temperature value in the body of the message and the temperature threshold value.
   public static async Task Run(Message messageReceived, IAsyncCollector<Message> output, TraceWriter log)
   {
        const int temperatureThreshold = 25;
        byte[] messageBytes = messageReceived.GetBytes();
        var messageString = System.Text.Encoding.UTF8.GetString(messageBytes);

        if (!string.IsNullOrEmpty(messageString))
        {
            // Get the body of the message and deserialize it
            var messageBody = JsonConvert.DeserializeObject<MessageBody>(messageString);

            if (messageBody != null && messageBody.machine.temperature > temperatureThreshold)
            {
                // Send the message to the output as the temperature value is greater than the threashold
                var filteredMessage = new Message(messageBytes);
                // Copy the properties of the original message into the new Message object
                foreach (KeyValuePair<string, string> prop in messageReceived.Properties)
                {
                    filteredMessage.Properties.Add(prop.Key, prop.Value);
                }
                // Add a new property to the message to indicate it is an alert
                filteredMessage.Properties.Add("MessageType", "Alert");
                // Send the message        
                await output.AddAsync(filteredMessage);
                log.Info("Received and transferred a message with temperature above the threshold");
            }
        }
    }

    //Define the expected schema for the body of incoming messages
    class MessageBody
    {
        public Machine machine {get;set;}
        public Ambient ambient {get; set;}
        public string timeCreated {get; set;}
    }
    class Machine
    {
       public double temperature {get; set;}
       public double pressure {get; set;}         
    }
    class Ambient
    {
       public double temperature {get; set;}
       public int humidity {get; set;}         
    }
   ```

8. Guarde el archivo.

### <a name="create-a-docker-image-and-publish-it-to-your-registry"></a>Crear una imagen de Docker y publicarla en el Registro

1. En el explorador de VS Code, expanda la carpeta **Docker**. Luego, seleccione la carpeta de la plataforma de contenedores, **linux-x64** o **windows-nano**.
2. Haga clic con el botón derecho en el archivo **Dockerfile** y haga clic en **Build IoT Edge module Docker image** (Generar imagen de Docker del módulo de IoT Edge). 

    ![Compilar una imagen de Docker](./media/how-to-vscode-develop-csharp-function/build-docker-image.png)

3. Desplácese hasta la carpeta de proyecto **FilterFunction** y haga clic en **Select Folder as EXE_DIR** (Seleccionar carpeta como EXE_DIR). 
4. En el cuadro de texto emergente de la parte superior de la ventana de VS Code, escriba el nombre de la imagen. Por ejemplo: `<your container registry address>/filterfunction:latest`. Si va a realizar la implementación en el registro local, debe ser `localhost:5000/filterfunction:latest`.
5. Inserte la imagen en el repositorio de Docker. Use el comando **Edge: Push IoT Edge module Docker image** (Edge: insertar la imagen de Docker del módulo con IoT Edge) y escriba la dirección URL de la imagen en el cuadro de texto emergente de la parte superior de la ventana de VS Code. Use la misma dirección URL de la imagen que utilizó en el paso anterior.
    ![Insertar una imagen de Docker](./media/how-to-vscode-develop-csharp-function/push-image.png)

### <a name="deploy-your-function-to-iot-edge"></a>Implementación de la función en IoT Edge

1. Abra el archivo `deployment.json` y reemplace la sección **modules** con el contenido siguiente:
   ```json
   "tempSensor": {
      "version": "1.0",
      "type": "docker",
      "status": "running",
      "restartPolicy": "always",
      "settings": {
         "image": "microsoft/azureiotedge-simulated-temperature-sensor:1.0-preview",
         "createOptions": ""
      }
   },
   "filterfunction": {
      "version": "1.0",
      "type": "docker",
      "status": "running",
      "restartPolicy": "always",
      "settings": {
         "image": "localhost:5000/filterfunction:latest",
         "createOptions": ""
      }
   }
   ```

2. Reemplace la sección **routes** con el contenido siguiente:
   ```json
       "routes":{
           "sensorToFilter":"FROM /messages/modules/tempSensor/outputs/temperatureOutput INTO BrokeredEndpoint(\"/modules/filterfunction/inputs/input1\")",
           "filterToIoTHub":"FROM /messages/modules/filterfunction/outputs/* INTO $upstream"
       }
   ```
   > [!NOTE]
   > Las reglas declarativas del runtime definen dónde fluyen esos mensajes. En este tutorial, necesita dos rutas. La primera transporta mensajes desde el sensor de temperatura hasta la función de filtro a través del punto de conexión "input1", que es el que configuró con el controlador FilterMessages. La segunda transporta mensajes desde la función de filtro hasta IoT Hub. En esta ruta, upstream es un destino especial que indica a Edge Hub que envíe mensajes a IoT Hub.

3. Guarde este archivo.
4. En la paleta de comandos, seleccione **Edge: Create deployment for Edge device** (Edge: crear implementación para dispositivo de Edge). A continuación, seleccione el identificador del dispositivo de IoT Edge para crear una implementación. También puede hacer clic con el botón derecho en el identificador del dispositivo en la lista de dispositivos y seleccionar **Create deployment for Edge device** (Crear implementación para dispositivo de Edge).

    ![Creación de una implementación](./media/how-to-vscode-develop-csharp-function/create-deployment.png)

5. Seleccione el archivo `deployment.json` actualizado. En la ventana de salida, verá las salidas correspondientes a la implementación.
6. Inicie el entorno de tiempo de ejecución de Edge en la paleta de comandos. **Edge: Start Edge** (Edge: iniciar Edge)
7. Verá el inicio de la ejecución del entorno de tiempo de ejecución de IoT Edge en el explorador de Docker con el sensor simulado y la función de filtro.

    ![Solución que se ejecuta](./media/how-to-vscode-develop-csharp-function/solution-running.png)

8. Haga clic con el botón derecho en el identificador de dispositivo de Edge; también puede supervisar los mensajes de D2C en VS Code.

    ![Controlar mensajes](./media/how-to-vscode-develop-csharp-function/monitor-d2c-messages.png)


## <a name="next-steps"></a>pasos siguientes

En este tutorial, creó una instancia de Azure Functions en IoT Edge y la implementó en un dispositivo de IoT Edge en VS Code. Puede continuar con cualquiera de los siguientes tutoriales para obtener información sobre otros escenarios al desarrollar Azure IoT Edge en VS Code.

> [!div class="nextstepaction"]
> [Depuración de Azure Functions en VS Code](how-to-vscode-debug-azure-function.md)
