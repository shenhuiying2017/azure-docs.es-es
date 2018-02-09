---
title: "Desarrollo de un módulo de C# con Azure IoT Edge en Visual Studio Code | Microsoft Docs"
description: "Desarrollo e implementación de un módulo de C# con Azure IoT Edge en Visual Studio Code sin cambio de contexto"
services: iot-edge
keywords: 
author: shizn
manager: timlt
ms.author: xshi
ms.date: 01/11/2018
ms.topic: article
ms.service: iot-edge
ms.openlocfilehash: 4cf07d5c4a21fa989e7de6e996cc62424099e3e5
ms.sourcegitcommit: 9d317dabf4a5cca13308c50a10349af0e72e1b7e
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 02/01/2018
---
# <a name="use-visual-studio-code-to-develop-a-c-module-with-azure-iot-edge"></a>Uso de Visual Studio Code para desarrollar un módulo de C# con Azure IoT Edge
En este artículo se proporcionan instrucciones detalladas para usar [Visual Studio Code](https://code.visualstudio.com/) como la herramienta de desarrollo principal para desarrollar e implementar los módulos de Azure IoT Edge. 

## <a name="prerequisites"></a>requisitos previos
En este tutorial se presupone que utiliza un equipo o una máquina virtual con Windows o Linux como máquina de desarrollo. El dispositivo de IoT Edge puede ser otro dispositivo físico o una simulación de dicho dispositivo en el equipo de desarrollo.

Antes de empezar esta guía, complete los siguientes tutoriales:
- Implementación de Azure IoT Edge en un dispositivo simulado en [Windows](https://docs.microsoft.com/azure/iot-edge/tutorial-simulate-device-windows) o [Linux](https://docs.microsoft.com/azure/iot-edge/tutorial-simulate-device-linux)
- [Desarrollo e implementación de un módulo de C# con IoT Edge en el dispositivo simulado](https://docs.microsoft.com/azure/iot-edge/tutorial-csharp-module)

Esta es una lista de comprobación que muestra los elementos que deben constar una vez terminados los tutoriales anteriores:

- [Visual Studio Code](https://code.visualstudio.com/) 
- [Extensión de Azure IoT Edge para Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-edge) 
- [Extensión de C# para Visual Studio Code (con tecnología de OmniSharp)](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp) 
- [Docker](https://docs.docker.com/engine/installation/)
- [SDK de .NET Core 2.0](https://www.microsoft.com/net/core#windowscmd) 
- [Python 2.7](https://www.python.org/downloads/)
- [Script de control de IoT Edge](https://pypi.python.org/pypi/azure-iot-edge-runtime-ctl)
- Plantilla AzureIoTEdgeModule (`dotnet new -i Microsoft.Azure.IoT.Edge.Module`)
- Una instancia de IoT Hub con al menos un dispositivo de IoT Edge

También es útil instalar la [compatibilidad de Docker para VS Code](https://marketplace.visualstudio.com/items?itemName=PeterJausovec.vscode-docker) para administrar mejor los contenedores y las imágenes del módulo.

## <a name="deploy-an-azure-iot-edge-module-in-vs-code"></a>Implementación de un módulo con Azure IoT Edge en VS Code

### <a name="list-your-iot-hub-devices"></a>Lista de los dispositivos del centro de IoT
Hay dos maneras de enumerar los dispositivos de la instancia de IoT Hub en VS Code. Puede elegir cualquiera de ellas para continuar.

#### <a name="sign-in-to-your-azure-account-in-vs-code-and-choose-your-iot-hub"></a>Inicio de sesión en la cuenta de Azure en VS Code y elección de la instancia de IoT Hub
1. En la paleta de comandos (F1 o Ctrl + Mayús + P), escriba y seleccione **Azure: Sign in** (Azure: iniciar sesión). Después, seleccione **Copiar y Abrir**. Pegue (CTRL + V) el código en el explorador y seleccione **Continuar**. Después, inicie sesión con su cuenta de Azure. En la barra de estado de VS Code verá la información de la cuenta.
2. En la paleta de comandos, escriba y seleccione **IoT: Select IoT Hub** (IoT: seleccionar IoT Hub). Primero seleccione la suscripción donde creó la instancia de IoT Hub en el tutorial anterior. Después, elija la instancia de IoT Hub que contenga el dispositivo de IoT Edge.

    ![Captura de pantalla de la lista de dispositivos](./media/how-to-vscode-develop-csharp-module/device-list.png)

#### <a name="set-the-iot-hub-connection-string"></a>Definición de la cadena de conexión de IoT Hub
En la paleta de comandos, escriba y seleccione **IoT: Set IoT Hub Connection String** (IoT: establecer cadena de conexión de IoT Hub). Asegúrese de pegar la cadena de conexión en la directiva **iothubowner**. (Puede encontrarla en las directivas de acceso compartido de la instancia de IoT Hub en Azure Portal).
 
Puede ver la lista de dispositivos en la barra lateral izquierda del explorador de dispositivos de IoT Hub.

### <a name="start-your-iot-edge-runtime-and-deploy-a-module"></a>Inicio del entorno de tiempo de ejecución de IoT Edge e implementación de un módulo
Instale e inicie el runtime de Azure IoT Edge en el dispositivo. Implemente un módulo de sensor simulado que envíe los datos de telemetría a Azure IoT Hub.
1. En la paleta de comandos, seleccione **Edge: Setup Edge** (Edge: configuración de Edge) y elija el identificador del dispositivo de IoT Edge. Alternativamente, haga clic con el botón derecho en el identificador de dispositivo de IoT Edge en la **lista de dispositivos** y seleccione **Setup Edge** (Configurar Edge).

    ![Captura de pantalla de configuración de Edge en tiempo de ejecución](./media/how-to-vscode-develop-csharp-module/setup-edge.png)

2. En la paleta de comandos, seleccione **Edge: Start Edge** (Edge: iniciar Edge) para iniciar el entorno de tiempo de ejecución de IoT Edge. Puede ver las salidas correspondientes en el terminal integrado.

    ![Captura de pantalla de inicio de Edge en tiempo de ejecución](./media/how-to-vscode-develop-csharp-module/start-edge.png)

3. Compruebe el estado del entorno de tiempo de ejecución de IoT Edge en el explorador de Docker. El color verde significa que está en ejecución y que el entorno de tiempo de ejecución de IoT Edge se inició correctamente. El equipo ahora simula un dispositivo de IoT Edge.

    ![Captura de pantalla del estado del entorno de tiempo de ejecución de Edge](./media/how-to-vscode-develop-csharp-module/edge-runtime.png)

4. Simule una especie de sensor que siga enviando mensajes al dispositivo de IoT Edge. En la paleta de comandos, escriba y seleccione **Edge: Generate Edge configuration file** (Edge: generar archivo de configuración de Edge). Seleccione una carpeta para crear este archivo. En el archivo deployment.json generado, reemplace el contenido `<registry>/<image>:<tag>` por `microsoft/azureiotedge-simulated-temperature-sensor:1.0-preview` y guarde el archivo.

    ![Captura de pantalla del módulo del sensor](./media/how-to-vscode-develop-csharp-module/sensor-module.png)

5. Seleccione **Edge: Create deployment for Edge device** (Edge: crear implementación para dispositivo de Edge) y elija el identificador del dispositivo de IoT Edge para crear una implementación. Como alternativa, puede hacer clic con el botón derecho en el identificador del dispositivo de IoT Edge en la lista de dispositivos y seleccionar **Create deployment for Edge device** (Crear implementación para dispositivo de Edge). 

6. Debería ver el inicio de la ejecución de su instancia de IoT Edge en el explorador de Docker con el sensor simulado. Haga clic con el botón derecho en el contenedor del explorador de Docker. También puede ver registros de Docker para cada módulo. Además, puede ver la lista de módulos en la lista de dispositivos.

    ![Captura de pantalla de la lista de módulos](./media/how-to-vscode-develop-csharp-module/module-list.png)

7. Haga clic con el botón derecho en el identificador de dispositivo de IoT Edge; también puede supervisar los mensajes de D2C en VS Code.
8. Para detener el entorno de tiempo de ejecución de IoT Edge y el módulo del sensor, escriba y seleccione **Edge: Stop Edge** (Edge: detener Edge) en la paleta de comandos.

## <a name="develop-and-deploy-a-c-module-in-vs-code"></a>Desarrollo e implementación de un módulo de C# en VS Code
En el tutorial de [desarrollo de un módulo de C#](https://docs.microsoft.com/azure/iot-edge/tutorial-csharp-module), se actualiza, se compila y se publica la imagen del módulo en VS Code. Después, se visita Azure Portal para implementar el módulo de C#. En esta sección se explica cómo utilizar VS Code para implementar y supervisar el módulo de C#.

### <a name="start-a-local-docker-registry"></a>Inicio de un registro de Docker local
Puede usar cualquier registro compatible con Docker para este tutorial. Dos servicios populares del Registro de Docker disponibles en la nube son [Azure Container Registry](https://docs.microsoft.com/azure/container-registry/) y [Docker Hub](https://docs.docker.com/docker-hub/repos/#viewing-repository-tags). En esta sección se usa un [registro de Docker local](https://docs.docker.com/registry/deploying/), que es más fácil para los fines de prueba durante el desarrollo temprano.
En el **terminal integrado** (Ctrl + `) de VS Code, ejecute el siguiente comando para iniciar un registro local:  

```cmd/sh
docker run -d -p 5000:5000 --name registry registry:2 
```

> [!NOTE]
> En este ejemplo se muestran configuraciones de registro que solo son adecuadas para las pruebas. Un registro para entornos de producción debe estar protegido con TLS y lo ideal es usar un mecanismo de control de acceso. Se recomienda usar [Azure Container Registry](https://docs.microsoft.com/azure/container-registry/) o [Docker Hub](https://docs.docker.com/docker-hub/repos/#viewing-repository-tags) para implementar módulos con IoT Edge para entornos de producción.

### <a name="create-an-iot-edge-module-project"></a>Crear un proyecto de módulo IoT Edge
En los siguientes pasos puede ver cómo crear un módulo IoT Edge basado en .NET Core 2.0 mediante Visual Studio Code y la extensión de Azure IoT Edge. Si ha completado esta sección en el tutorial anterior, puede omitirla sin problema.
1. En Visual Studio Code, seleccione **Ver** > **Terminal integrado** para abrir el terminal integrado de VS Code.
3. En el terminal integrado, escriba el siguiente comando para instalar (o actualizar) la plantilla **AzureIoTEdgeModule** en dotnet:

    ```cmd/sh
    dotnet new -i Microsoft.Azure.IoT.Edge.Module
    ```

2. Cree un proyecto para el nuevo módulo. El comando siguiente crea la carpeta del proyecto, **FilterModule**, en la carpeta de trabajo actual:

    ```cmd/sh
    dotnet new aziotedgemodule -n FilterModule
    ```
 
3. Seleccione **Archivo** > **Abrir carpeta**.
4. Vaya a la carpeta **FilterModule** y haga clic en **Seleccionar carpeta** para abrir el proyecto en VS Code.
5. En el explorador de VS Code, seleccione **Program.cs** para abrirlo. En la parte superior de **program.cs**, incluya los siguientes espacios de nombres:
   ```csharp
   using Microsoft.Azure.Devices.Shared;
   using System.Collections.Generic;  
   using Newtonsoft.Json;
   ```

6. Agregue la variable `temperatureThreshold` a la clase **Program**. Esta variable establece el valor que debe superar la temperatura medida para que los datos se envíen a IoT Hub. 

   ```csharp
   static int temperatureThreshold { get; set; } = 25;
   ```

7. Agregue las clases `MessageBody`, `Machine` y `Ambient` a la clase **Program**. Estas clases definen el esquema esperado para el cuerpo de los mensajes entrantes.

    ```csharp
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

8. En el método **Init**, el código crea y configura un objeto **DeviceClient**. Este objeto permite al módulo conectarse al entorno de tiempo de ejecución de Azure IoT Edge local para enviar y recibir mensajes. El entorno de tiempo de ejecución de IoT Edge proporciona al módulo la cadena de conexión usada en el método **Init**. Tras la creación del objeto **DeviceClient**, el código registra una devolución de llamada para recibir mensajes de la instancia de IoT Edge Hub a través del punto de conexión **input1**. Reemplace el método `SetInputMessageHandlerAsync` por otro y agregue un método `SetDesiredPropertyUpdateCallbackAsync` para las actualizaciones de propiedades que quiera. Para realizar este cambio, reemplace la última línea del método **Init** con el siguiente código:

    ```csharp
    // Register callback to be called when a message is received by the module
    // await ioTHubModuleClient.SetImputMessageHandlerAsync("input1", PipeMessage, iotHubModuleClient);

    // Attach callback for Twin desired properties updates
    await ioTHubModuleClient.SetDesiredPropertyUpdateCallbackAsync(onDesiredPropertiesUpdate, null);

    // Register callback to be called when a message is received by the module
    await ioTHubModuleClient.SetInputMessageHandlerAsync("input1", FilterMessages, ioTHubModuleClient);
    ```

9. Agregue el método `onDesiredPropertiesUpdate` a la clase **Program**. Este método recibe las actualizaciones sobre las propiedades que se quieren del módulo gemelo y actualiza la variable **temperatureThreshold** para que coincida. Todos los módulos tienen su propio módulo gemelo, que le permite configurar el código que se ejecuta dentro de un módulo directamente desde la nube.

    ```csharp
    static Task onDesiredPropertiesUpdate(TwinCollection desiredProperties, object userContext)
    {
        try
        {
            Console.WriteLine("Desired property change:");
            Console.WriteLine(JsonConvert.SerializeObject(desiredProperties));

            if (desiredProperties["TemperatureThreshold"]!=null)
                temperatureThreshold = desiredProperties["TemperatureThreshold"];

        }
        catch (AggregateException ex)
        {
            foreach (Exception exception in ex.InnerExceptions)
            {
                Console.WriteLine();
                Console.WriteLine("Error when receiving desired property: {0}", exception);
            }
        }
        catch (Exception ex)
        {
            Console.WriteLine();
            Console.WriteLine("Error when receiving desired property: {0}", ex.Message);
        }
        return Task.CompletedTask;
    }
    ```

10. Reemplace el método `PipeMessage` por el método `FilterMessages`. Se llama a este método cada vez que el módulo recibe un mensaje de IoT Edge Hub. Filtra los mensajes que informan de temperaturas por debajo del umbral de temperatura que se establecen mediante el módulo gemelo. También agrega la propiedad **MessageType** al mensaje con el valor establecido en **Alerta**. 

    ```csharp
    static async Task<MessageResponse> FilterMessages(Message message, object userContext)
    {
        int counterValue = Interlocked.Increment(ref counter);

        try {
            DeviceClient deviceClient = (DeviceClient)userContext;

            byte[] messageBytes = message.GetBytes();
            string messageString = Encoding.UTF8.GetString(messageBytes);
            Console.WriteLine($"Received message {counterValue}: [{messageString}]");

            // Get message body
            var messageBody = JsonConvert.DeserializeObject<MessageBody>(messageString);

            if (messageBody != null && messageBody.machine.temperature > temperatureThreshold)
            {
                Console.WriteLine($"Machine temperature {messageBody.machine.temperature} " +
                    $"exceeds threshold {temperatureThreshold}");
                var filteredMessage = new Message(messageBytes);
                foreach (KeyValuePair<string, string> prop in message.Properties)
                {
                    filteredMessage.Properties.Add(prop.Key, prop.Value);
                }

                filteredMessage.Properties.Add("MessageType", "Alert");
                await deviceClient.SendEventAsync("output1", filteredMessage);
            }

            // Indicate that the message treatment is completed
            return MessageResponse.Completed;
        }
        catch (AggregateException ex)
        {
            foreach (Exception exception in ex.InnerExceptions)
            {
                Console.WriteLine();
                Console.WriteLine("Error in sample: {0}", exception);
            }
            // Indicate that the message treatment is not completed
            DeviceClient deviceClient = (DeviceClient)userContext;
            return MessageResponse.Abandoned;
        }
        catch (Exception ex)
        {
            Console.WriteLine();
            Console.WriteLine("Error in sample: {0}", ex.Message);
            // Indicate that the message treatment is not completed
            DeviceClient deviceClient = (DeviceClient)userContext;
            return MessageResponse.Abandoned;
        }
    }
    ```

11. Para compilar el proyecto, haga clic con el botón derecho en el archivo **FilterModule.csproj** en el explorador y luego seleccione **Build IoT Edge module** (Generar módulo IoT Edge). Este proceso compila el módulo y exporta el archivo binario y sus dependencias a una carpeta que se usa para crear una imagen de Docker. 

    ![Captura de pantalla del Explorador de VS Code](./media/how-to-vscode-develop-csharp-module/build-module.png)

### <a name="create-a-docker-image-and-publish-it-to-your-registry"></a>Crear una imagen de Docker y publicarla en el Registro

1. En el explorador de VS Code, expanda la carpeta **Docker**. Luego, seleccione la carpeta de la plataforma de contenedores, **linux-x64** o **windows-nano**.
2. Haga clic con el botón derecho en el archivo **Dockerfile** y seleccione **Build IoT Edge module Docker image** (Generar imagen de Docker del módulo de IoT Edge). 

    ![Captura de pantalla del Explorador de VS Code](./media/how-to-vscode-develop-csharp-module/build-docker-image.png)

3. En la ventana **Seleccionar carpeta**, vaya a `./bin/Debug/netcoreapp2.0/publish` o especifíquelo. Seleccione **Select Folder as EXE_DIR** (Seleccionar carpeta como EXE_DIR).
4. En el cuadro de texto emergente de la parte superior de la ventana de VS Code, escriba el nombre de la imagen. Por ejemplo: `<your container registry address>/filtermodule:latest`. Si va a realizar la implementación en el registro local, debe ser `localhost:5000/filtermodule:latest`.
5. Inserte la imagen en el repositorio de Docker. Use el comando **Edge: Push IoT Edge module Docker image** (Edge: insertar la imagen de Docker del módulo con IoT Edge) y escriba la dirección URL de la imagen en el cuadro de texto emergente de la parte superior de la ventana de VS Code. Use la misma dirección URL de la imagen que utilizó en el paso anterior. Compruebe el registro de la consola para asegurarse de que la imagen se ha insertado correctamente.

    ![Captura de pantalla de inserción de la imagen de Docker](./media/how-to-vscode-develop-csharp-module/push-image.png) ![Captura de pantalla del registro de la consola](./media/how-to-vscode-develop-csharp-module/pushed-image.png)

### <a name="deploy-your-iot-edge-modules"></a>Implementación de los módulos con IoT Edge

1. Abra el archivo `deployment.json` y reemplace la sección **modules** con lo siguiente:
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
    "filtermodule": {
        "version": "1.0",
        "type": "docker",
        "status": "running",
        "restartPolicy": "always",
        "settings": {
            "image": "localhost:5000/filtermodule:latest",
            "createOptions": ""
        }
    }
    ```

2. Reemplace la sección **routes** con lo siguiente:
    ```json
    "sensorToFilter": "FROM /messages/modules/tempSensor/outputs/temperatureOutput INTO BrokeredEndpoint(\"/modules/filtermodule/inputs/input1\")",
    "filterToIoTHub": "FROM /messages/modules/filtermodule/outputs/output1 INTO $upstream"
    ```
   > [!NOTE]
   > Las reglas declarativas del runtime definen dónde fluyen esos mensajes. En este tutorial, necesita dos rutas. La primera ruta transporta los mensajes del sensor de temperatura al módulo de filtro a través del punto de conexión "input1". Este es el punto de conexión que configuró con el controlador FilterMessages. La segunda transporta mensajes desde el módulo de filtro hasta IoT Hub. En esta ruta, upstream es un destino especial que indica a IoT Edge Hub que envíe mensajes a IoT Hub.

3. Guarde este archivo.
4. En la paleta de comandos, seleccione **Edge: Create deployment for Edge device** (Edge: crear implementación para dispositivo de Edge). A continuación, seleccione el identificador del dispositivo de IoT Edge para crear una implementación. También puede hacer clic con el botón derecho en el identificador del dispositivo en la lista de dispositivos y seleccionar **Create deployment for Edge device** (Crear implementación para dispositivo de Edge).

    ![Captura de pantalla de la opción para crear la implementación](./media/how-to-vscode-develop-csharp-module/create-deployment.png)

5. Seleccione el archivo `deployment.json` actualizado. En la ventana de salida, verá las salidas correspondientes a la implementación.

    ![Captura de pantalla de la ventana de salida](./media/how-to-vscode-develop-csharp-module/deployment-succeeded.png)

6. En la paleta de comandos, seleccione **Edge: Start Edge** (Edge: iniciar Edge) para iniciar el entorno de tiempo de ejecución de IoT Edge.
7. Verá el inicio de la ejecución del entorno de tiempo de ejecución de IoT Edge en el explorador de Docker con el sensor simulado y el módulo de filtro.

    ![Captura de pantalla del explorador de Docker](./media/how-to-vscode-develop-csharp-module/solution-running.png)

8. Haga clic con el botón derecho en el identificador de dispositivo de IoT Edge; también puede supervisar los mensajes de D2C en VS Code.


## <a name="next-steps"></a>pasos siguientes

En este tutorial, creó un módulo con IoT Edge y lo implementó en un dispositivo de IoT Edge en VS Code. Para obtener información sobre otros escenarios al desarrollar Azure IoT Edge en VS Code, vea el siguiente tutorial:

> [!div class="nextstepaction"]
> [Depuración de un módulo de C# en VS Code](how-to-vscode-debug-csharp-module.md)
