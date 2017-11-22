---
title: "Módulo C# de Azure IoT Edge | Microsoft Docs"
description: "Crear un módulo IoT Edge con código de C# e implementarlo en un dispositivo perimetral"
services: iot-edge
keywords: 
author: JimacoMS2
manager: timlt
ms.author: v-jamebr
ms.date: 11/15/2017
ms.topic: article
ms.service: iot-edge
ms.openlocfilehash: fb7674d8c292e7d571a94ac4625b0858a90704b3
ms.sourcegitcommit: 3ee36b8a4115fce8b79dd912486adb7610866a7c
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/15/2017
---
# <a name="develop-and-deploy-a-c-iot-edge-module-to-your-simulated-device---preview"></a>Desarrollar e implementar un módulo IoT Edge C# en su dispositivo simulado: versión preliminar

Puede usar los módulos IoT Edge para implementar código que a su vez implementa la lógica empresarial directamente en los dispositivos IoT Edge. Este tutorial le guía a través de la creación y la implementación de un módulo IoT Edge que filtra datos del sensor en el dispositivo IoT Edge simulado que creó en el paso de implementación de Azure IoT Edge en un dispositivo simulado en tutoriales de [Windows][lnk-tutorial1-win] o [Linux][lnk-tutorial1-lin]. En este tutorial, aprenderá a:    

> [!div class="checklist"]
> * Usar Visual Studio Code para crear un módulo IoT Edge basado en .NET Core 2.0
> * Usar Visual Studio Code y Docker para crear una imagen de Docker y publicarla en el Registro 
> * Implementar el módulo en el dispositivo IoT Edge
> * Ver datos generados


En el módulo IoT Edge creado en este tutorial se filtran los datos de temperatura generados por el dispositivo y solo se envían mensajes en dirección ascendente si la temperatura se encuentra por encima de un umbral especificado. 

## <a name="prerequisites"></a>Requisitos previos

* El dispositivo de Azure IoT Edge que creó en el tutorial anterior o de inicio rápido.
* La cadena de conexión de IoT Hub del centro de IoT al que se conecta el dispositivo IoT Edge.  
* [Visual Studio Code](https://code.visualstudio.com/). 
* [Extensión de Azure IoT Edge para Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-edge) (puede instalar la extensión a partir del panel de extensiones de Visual Studio Code).
* [Extensión de C# para Visual Studio Code (con tecnología de OmniSharp)](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp) (puede instalar la extensión a partir del panel de extensiones de Visual Studio Code).
* Extensión de Azure IoT Edge para Visual Studio Code
* [Docker](https://docs.docker.com/engine/installation/). La Community Edition (CE) de la plataforma es suficiente para este tutorial. Asegúrese de instalarla en el equipo en el que ejecuta VS Code.
* [SDK de .NET Core 2.0](https://www.microsoft.com/net/core#windowscmd). 

## <a name="choose-or-sign-up-for-a-docker-registry"></a>Elija o suscríbase a Docker Registry
En este tutorial, puede usar la extensión de Azure IoT Edge para VS Code a fin de crear un módulo y una [imagen de Docker](https://docs.docker.com/glossary/?term=image). A continuación, puede insertar esta imagen de Docker en un [repositorio de Docker](https://docs.docker.com/glossary/?term=repository) hospedado en [Docker Registry](https://docs.docker.com/glossary/?term=registry). Por último, puede implementar la imagen de Docker empaquetada como [contenedor de Docker](https://docs.docker.com/glossary/?term=container) desde el Registro en el dispositivo IoT Edge.  

Puede usar cualquier Registro compatible con Docker para este tutorial. Dos servicios populares de Docker Registry disponibles en la nube son **Azure Container Registry** y **Docker Hub**:

- [Azure Container Registry](https://docs.microsoft.com/en-us/azure/container-registry/) está disponible con una [suscripción de pago](https://azure.microsoft.com/en-us/pricing/details/container-registry/). Para este tutorial, la suscripción **Basic** es suficiente. 

- [Docker Hub](https://docs.docker.com/docker-hub/repos/#viewing-repository-tags) ofrece un repositorio privado gratuito si se suscribe a un identificador de Docker (gratuito). 
    1. Para suscribirse a un identificador de Docker, siga las instrucciones en [Register for a Docker ID](https://docs.docker.com/docker-id/#register-for-a-docker-id) (Suscribirse a un identificador de Docker) en el sitio de Docker. 

    2. Para crear un repositorio de Docker privado, siga las instrucciones en [Creating a new repository on Docker Hub](https://docs.docker.com/docker-hub/repos/#creating-a-new-repository-on-docker-hub) (Creación de un nuevo repositorio en Docker Hub) en el sitio de Docker.

En todo este tutorial, donde corresponda, se proporcionarán comandos tanto para Azure Container Registry como para Docker Hub.

## <a name="create-an-iot-edge-module-project"></a>Crear un proyecto de módulo IoT Edge
En los siguientes pasos puede ver cómo crear un módulo IoT Edge basado en .NET Core 2.0 mediante Visual Studio Code y la extensión de Azure IoT Edge.
1. Abra VS Code.
2. Use el comando de menú **Ver | Terminal integrado** para abrir el terminal integrado de VS Code.
3. En el terminal integrado, escriba el siguiente comando para instalar (o actualizar) la plantilla **AzureIoTEdgeModule** en dotnet:

    ```cmd/sh
    dotnet new -i Microsoft.Azure.IoT.Edge.Module
    ```

2. En el terminal integrado, escriba el siguiente comando para crear un proyecto para el nuevo módulo:

    ```cmd/sh
    dotnet new aziotedgemodule -n FilterModule
    ```

    >[!NOTE]
    > Este comando crea la carpeta del proyecto, **FilterModule**, en la carpeta de trabajo actual. Si desea crearla en otra ubicación, cambie los directorios antes de ejecutar el comando.
 
3. Use el comando de menú **Archivo | Abrir carpeta**, vaya a la carpeta **FilterModule** y haga clic en **Seleccionar carpeta** para abrir el proyecto en VS Code.
4. En el explorador de VS Code, haga clic en **Program.cs** para abrirlo.
5. Agregue el campo siguiente a la clase **Program**.

    ```csharp
    static int temperatureThreshold { get; set; } = 25;
    ```

1. Agregue las clases siguientes a la clase **Program**. Estas clases definen el esquema esperado para el cuerpo de los mensajes entrantes.

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

1. En el método **Init**, el código crea y configura un objeto **DeviceClient**. Este objeto permite al módulo conectarse al runtime de Azure IoT Edge local para enviar y recibir mensajes. El runtime de IoT Edge proporciona el parámetro de cadena de conexión usado en el método **Init** al módulo en la variable de entorno **EdgeHubConnectionString**. Tras la creación de **DeviceClient**, el código registra una devolución de llamada para recibir mensajes del centro de IoT Edge a través del punto de conexión **input1**. Reemplace el método usado como devolución de llamada para administrar mensajes por uno nuevo y asocie una devolución de llamada para las actualizaciones de propiedades deseadas en el módulo gemelo. Para realizar este cambio, reemplace la última línea del método **Init** con el siguiente código:

    ```csharp
    // Register callback to be called when a message is received by the module
    // await ioTHubModuleClient.SetImputMessageHandlerAsync("input1", PipeMessage, iotHubModuleClient);

    // Attach callback for Twin desired properties updates
    await ioTHubModuleClient.SetDesiredPropertyUpdateCallbackAsync(onDesiredPropertiesUpdate, null);

    // Register callback to be called when a message is received by the module
    await ioTHubModuleClient.SetInputMessageHandlerAsync("input1", FilterMessages, ioTHubModuleClient);
    ```

1. Agregue el siguiente método a la clase **Program** para actualizar el campo **temperatureThreshold** basado en las propiedades deseadas enviadas por el servicio back-end a través del módulo gemelo. Todos los módulos tienen su propio módulo gemelo. Un módulo gemelo permite que un servicio back-end configure el código que se ejecuta dentro de un módulo.

    ```csharp
    static Task onDesiredPropertiesUpdate(TwinCollection desiredProperties, object userContext)
    {
        try
        {
            Console.WriteLine("Desired property change:");
            Console.WriteLine(JsonConvert.SerializeObject(desiredProperties));

            if (desiredProperties["TemperatureThreshold"].exists())
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

1. Reemplace el método **PipeMessage** por el método siguiente. Se llama a este método cada vez que el módulo recibe un mensaje de Edge Hub. Filtra los mensajes en función del valor de temperatura en el cuerpo del mensaje y el umbral de temperatura establecido a través del módulo gemelo.

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

11. Compile el proyecto. Use el comando de menú **Ver | Explorador** para abrir el explorador de VS Code. En el explorador, haga clic con el botón derecho en el archivo **FilterModule.csproj** y haga clic en **Generar el módulo de IoT Edge** para compilar el módulo y exportar el binario y sus dependencias en una carpeta a partir de la cual se crea la imagen de Docker en el paso siguiente.

## <a name="create-a-docker-image-and-publish-it-to-your-registry"></a>Crear una imagen de Docker y publicarla en el Registro

1. Compile la imagen de Docker.
    1. En el explorador de VS Code, haga clic en la carpeta **Docker** para abrirla. A continuación, seleccione la carpeta para su plataforma de contenedores, **linux-x64** o **windows-nano**. 
    2. Haga clic con el botón derecho en el archivo **Dockerfile** y en **Generar la imagen de Docker del módulo de IoT Edge**. 
    3. En el cuadro **Seleccionar carpeta**, vaya a `./bin/Debug/netcoreapp2.0/publish` o especifíquelo. Haga clic en **Seleccionar carpeta como EXE_DIR**.
    4. En el cuadro de texto emergente de la parte superior de la ventana de VS Code, escriba el nombre de la imagen. Por ejemplo:`<docker registry address>/filtermodule:latest`; donde la *dirección de Docker Registry* es su identificador de Docker si usa Docker Hub o similar a `<your registry name>.azurecr.io`, si usa Azure Container Registry.
 
4. Inicie sesión en Docker. En el terminal integrado, escriba el siguiente comando: 

    - Docker Hub (escriba sus credenciales cuando se le solicite):
        
        ```csh/sh
        docker login
        ```

    - Para Azure Container Registry:
        
        ```csh/sh
        docker login -u <username> -p <password> <Login server>
        ```
        
        Para buscar el nombre de usuario, la contraseña y el servidor de inicio de sesión a fin de usar este comando, vaya a [Azure Portal] (https://portal.azure.com). En **Todos los recursos**, haga clic en el icono para que Azure Container Registry abra sus propiedades y, a continuación, haga clic en **Claves de acceso**. Copie los valores en los campos **Nombre de usuario**, **Contraseña** y **Servidor de inicio de sesión**. El servidor de inicio de sesión debe ser del tipo: `<your registry name>.azurecr.io`.

3. Inserte la imagen en el repositorio de Docker. Use el comando de menú **Ver | Paleta de comandos ... | Edge: insertar la imagen de Docker del módulo de IoT Edge** y escriba el nombre de la imagen en el cuadro de texto emergente en la parte superior de la ventana de VS Code. Use el mismo nombre de imagen que utilizó en el paso 1.c.

## <a name="add-registry-credentials-to-edge-runtime-on-your-edge-device"></a>Agregar las credenciales del Registro al runtime de Edge en el dispositivo Edge
Agregue las credenciales del Registro al runtime de Edge en el equipo donde ejecuta el dispositivo Edge. Esto proporciona acceso al runtime para extraer el contenedor. 

- Para Windows, ejecute el siguiente comando:
    
    ```cmd/sh
    iotedgectl login --address <docker-registry-address> --username <docker-username> --password <docker-password> 
    ```

- Para Linux, ejecute el siguiente comando:
    
    ```cmd/sh
    sudo iotedgectl login --address <docker-registry-address> --username <docker-username> --password <docker-password> 
    ```

## <a name="run-the-solution"></a>Ejecutar la solución

1. En [Azure Portal](https://portal.azure.com), vaya al centro de IoT.
2. Vaya a **IoT Edge (versión preliminar)** y seleccione el dispositivo IoT Edge.
3. Seleccione **Establecer módulos**. 
2. Agregue el módulo **tempSensor**. Este paso solo es obligatorio si anteriormente no ha implementado el módulo **tempSensor** en el dispositivo IoT Edge.
    1. Seleccione **Agregar módulo de IoT Edge**.
    2. En el campo **Nombre**, escriba `tempSensor`.
    3. En el campo **URI de imagen**, escriba `microsoft/azureiotedge-simulated-temperature-sensor:1.0-preview`.
    4. No cambie las otras configuraciones y haga clic en **Guardar**.
9. Agregue **filtermodule**.
    1. Seleccione **Agregar módulo de IoT Edge** de nuevo.
    2. En el campo **Nombre**, escriba `filtermodule`.
    3. En el campo **Imagen**, escriba la dirección de la imagen; por ejemplo, `<docker registry address>/filtermodule:latest`.
    4. Compruebe el cuadro **Editar módulo gemelo**.
    5. Reemplace el archivo JSON del cuadro de texto del módulo gemelo por el siguiente: 

        ```json
        {
           "properties.desired":{
              "TemperatureThreshold":25
           }
        }
        ```
 
    6. Haga clic en **Guardar**.
12. Haga clic en **Siguiente**.
13. En el paso **Especificar rutas**, copie el archivo JSON siguiente en el cuadro de texto. Los módulos publican todos los mensajes en el runtime de Edge. Las reglas declarativas del runtime definen dónde fluyen esos mensajes. En este tutorial, necesita dos rutas. La primera ruta transporta mensajes del sensor de temperatura al módulo de filtro a través del punto de conexión "input1", que es el punto de conexión que configuró con el controlador **FilterMessages**. La segunda ruta transporta mensajes del módulo de filtro a IoT Hub. En esta ruta, `upstream` es un destino especial que indica a Edge Hub que envíe mensajes a IoT Hub. 

    ```json
    {
       "routes":{
          "sensorToFilter":"FROM /messages/modules/tempSensor/outputs/temperatureOutput INTO BrokeredEndpoint(\"/modules/filtermodule/inputs/input1\")",
          "filterToIoTHub":"FROM /messages/modules/filtermodule/outputs/output1 INTO $upstream"
       }
    }
    ```

4. Haga clic en **Siguiente**.
5. En el paso **Revisar plantilla**, haga clic en **Enviar**. 
6. Vuelva a la página de detalles del dispositivo IoT Edge y haga clic en **Actualizar**. Debería ver el nuevo **filtermodule** en ejecución junto con el módulo **tempSensor** y el **runtime de IoT Edge**. 

## <a name="view-generated-data"></a>Ver datos generados

Para supervisar mensajes del dispositivo a la nube enviados al centro de IoT desde el dispositivo IoT Edge:
1. Configure la extensión de Azure IoT Toolkit con la cadena de conexión del centro de IoT: 
    1. Use el comando de menú **Ver | Explorador** para abrir el explorador de VS Code. 
    3. En el explorador, haga clic en **IOT HUB DEVICES** y, a continuación, en **...**. Haga clic en **Establecer cadena de conexión de IoT Hub** y escriba la cadena de conexión del centro de IoT al que se conecta el dispositivo IoT Edge en la ventana emergente. 

        Para buscar la cadena de conexión, haga clic en el icono del centro de IoT en Azure Portal y, a continuación, haga clic en **Directivas de acceso compartido**. En **Directivas de acceso compartido**, haga clic en la directiva **iothubowner** y copie la cadena de conexión de IoT Hub en la ventana **iothubowner**.   

1. Para supervisar los datos que llegan al centro de IoT, use el comando de menú **Ver | Paleta de comandos... | IoT: inicio de la supervisión del mensaje de D2C**. 
2. Para dejar de supervisar datos, use el comando de menú **Ver | Paleta de comandos... | IoT: detención de la supervisión del mensaje de D2C**. 

## <a name="next-steps"></a>Pasos siguientes

En este tutorial, creó un módulo IoT Edge que contiene código para filtrar datos sin procesar generados por el dispositivo IoT Edge. Puede continuar con cualquiera de los siguientes tutoriales para obtener información sobre otras formas en que Azure IoT Edge puede ayudarle a transformar datos en perspectivas empresariales en el límite.

> [!div class="nextstepaction"]
> [Deploy Azure Function as a module (Implementar Azure Functions como módulo)](tutorial-deploy-function.md)
> [Deploy Azure Stream Analytics as a module (Implementar Azure Stream Analytics como módulo)](tutorial-deploy-stream-analytics.md)


<!-- Links -->
[lnk-tutorial1-win]: tutorial-simulate-device-windows.md
[lnk-tutorial1-lin]: tutorial-simulate-device-linux.md
