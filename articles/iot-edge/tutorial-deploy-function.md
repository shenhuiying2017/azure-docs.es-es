---
title: "Implementación de Azure Functions con Azure IoT Edge | Microsoft Docs"
description: "Implementación de Azure Functions como módulo en un dispositivo perimetral"
services: iot-edge
keywords: 
author: kgremban
manager: timlt
ms.author: v-jamebr
ms.date: 11/15/2017
ms.topic: tutorial
ms.service: iot-edge
ms.custom: mvc
ms.openlocfilehash: fb295b37819788ed14f54e4123ae0fe1b52d0210
ms.sourcegitcommit: 7136d06474dd20bb8ef6a821c8d7e31edf3a2820
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/05/2017
---
# <a name="deploy-azure-function-as-an-iot-edge-module---preview"></a>Implementación de Azure Functions como módulo de IoT Edge (versión preliminar)
Azure Functions se puede usar para implementar código que, a su vez, implementa una lógica de negocios directamente en los dispositivos de IoT Edge. Este tutorial le guía a través de la creación y la implementación de una instancia de Azure Functions que filtra datos del sensor en el dispositivo de IoT Edge simulado que creó en el paso de implementación de Azure IoT Edge en un dispositivo simulado en los tutoriales de [Windows][lnk-tutorial1-win] o [Linux][lnk-tutorial1-lin]. En este tutorial, aprenderá a:     

> [!div class="checklist"]
> * Usar Visual Studio Code para crear una instancia de Azure Functions
> * Usar VS Code y Docker para crear una imagen de Docker y publicarla en el Registro 
> * Implementación del módulo en un dispositivo de IoT Edge
> * Visualización de datos generados


La instancia de Azure Functions que se crea en este tutorial filtra los datos de temperatura generados por el dispositivo y solo envía mensajes en dirección ascendente a Azure IoT Hub si la temperatura se encuentra por encima de un umbral especificado. 

## <a name="prerequisites"></a>Requisitos previos

* El dispositivo de Azure IoT Edge que creó en el tutorial anterior o en la guía de inicio rápido.
* [Visual Studio Code](https://code.visualstudio.com/). 
* [Extensión de C# para Visual Studio Code (con tecnología de OmniSharp)](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp) (la extensión se puede instalar desde el panel de extensiones de Visual Studio Code).
* [Extensión de Azure IoT Edge para Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-edge). (la extensión se puede instalar desde el panel de extensiones de Visual Studio Code).
* [Docker](https://docs.docker.com/engine/installation/). La Community Edition (CE) de la plataforma es suficiente para este tutorial. 
* [SDK de .NET Core 2.0](https://www.microsoft.com/net/core#windowscmd). 

## <a name="set-up-a-docker-registry"></a>Configuración de un registro de Docker
En este tutorial, puede usar la extensión de Azure IoT Edge para VS Code para crear una instancia de Azure Functions y compilar una [imagen del Docker](https://docs.docker.com/glossary/?term=image). A continuación, inserte esta imagen de Docker en un [repositorio de Docker](https://docs.docker.com/glossary/?term=repository) hospedado por [Docker Registry](https://docs.docker.com/glossary/?term=registry). Por último, implemente la imagen de Docker empaquetada como [contenedor de Docker](https://docs.docker.com/glossary/?term=container) desde el registro en el dispositivo IoT Edge.  

Puede usar cualquier registro compatible con Docker para este tutorial. Dos servicios populares de Docker Registry disponibles en la nube son **Azure Container Registry** y **Docker Hub**:

- [Azure Container Registry](https://docs.microsoft.com/en-us/azure/container-registry/) está disponible con las [suscripciones de pago](https://azure.microsoft.com/en-us/pricing/details/container-registry/). Para este tutorial, la suscripción **Basic** es suficiente. 

- [Docker Hub](https://docs.docker.com/docker-hub/repos/#viewing-repository-tags) ofrece un repositorio privado gratuito si se suscribe a un identificador de Docker (gratuito). 
    1. Para suscribirse a un identificador de Docker, siga las instrucciones que aparecen en [Register for a Docker ID](https://docs.docker.com/docker-id/#register-for-a-docker-id) (Suscribirse a un identificador de Docker) en el sitio de Docker. 

    2. Para crear un repositorio de Docker privado, siga las instrucciones de [Creating a new repository on Docker Hub](https://docs.docker.com/docker-hub/repos/#creating-a-new-repository-on-docker-hub) (Creación de un nuevo repositorio en Docker Hub) en el sitio de Docker.

En todo este tutorial, donde corresponda, se proporcionarán comandos tanto de Azure Container Registry como de Docker Hub.

## <a name="create-a-function-project"></a>Creación de un proyecto de aplicación de una función
En los siguientes pasos puede ver cómo crear una función de IoT Edge mediante Visual Studio Code y la extensión de Azure IoT Edge.
1. Abra VS Code.
2. Use el comando de menú **Ver | Terminal integrado** para abrir el terminal integrado de VS Code.
3. En dicho terminal, escriba el siguiente comando para instalar (o actualizar) la plantilla **AzureIoTEdgeFunction** en dotnet:

    ```cmd/sh
    dotnet new -i Microsoft.Azure.IoT.Edge.Function
    ```
2. En el terminal integrado, escriba el siguiente comando para crear un proyecto para el nuevo módulo:

    ```cmd/sh
    dotnet new aziotedgefunction -n FilterFunction
    ```

    >[!NOTE]
    > Este comando crea la carpeta del proyecto, **FilterFunction**, en la carpeta de trabajo actual. Si desea crearla en otra ubicación, cambie los directorios antes de ejecutar el comando.

3. Use el comando de menú **Archivo | Abrir carpeta**, vaya a la carpeta **FilterFunction** y haga clic en **Seleccionar carpeta** para abrir el proyecto en VS Code.
4. En el explorador de VS Code, haga clic en la carpeta **EdgeHubTrigger-Csharp** y, después, haga clic en el archivo **run.csx** para abrirlo.
5. Agregue la siguiente instrucción después de la instrucción `#r "Microsoft.Azure.Devices.Client"`:

    ```csharp
    #r "Newtonsoft.Json"
    ```

5. Agregue la siguiente instrucción después de la instrucciones `using` existentes:

    ```csharp
    using Newtonsoft.Json;
    ```

1. Agregue las siguientes clases. Estas clases definen el esquema esperado para el cuerpo de los mensajes entrantes.

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

1. Reemplace el cuerpo del método **Run** por el código siguiente. Filtra los mensajes en función del valor de temperatura en el cuerpo del mensaje y del valor del umbral de temperatura.

    ```csharp
    const int temperatureThreshold = 25;

    byte[] messageBytes = messageReceived.GetBytes();
    var messageString = System.Text.Encoding.UTF8.GetString(messageBytes);

    if (!string.IsNullOrEmpty(messageString))
    {
        // Get the body of the message and deserialize it
        var messageBody = JsonConvert.DeserializeObject<MessageBody>(messageString);
        
        if (messageBody != null && messageBody.machine.temperature > temperatureThreshold)
        {
            // We will send the message to the output as the temperature value is greater than the threashold
            var filteredMessage = new Message(messageBytes);
            // We need to copy the properties of the original message into the new Message object
            foreach (KeyValuePair<string, string> prop in messageReceived.Properties)
            {
                filteredMessage.Properties.Add(prop.Key, prop.Value);
            }
            // We are adding a new property to the message to indicate it is an alert
            filteredMessage.Properties.Add("MessageType", "Alert");
            // Send the message        
            await output.AddAsync(filteredMessage);
            log.Info("Received and transferred a message with temperature above the threshold");
        }
    }
    ```

11. Guarde el archivo .

## <a name="publish-a-docker-image"></a>Publicación de una imagen de Docker

1. Compile la imagen de Docker.
    1. En el explorador de VS Code, haga clic en la carpeta **Docker** para abrirla. Luego, seleccione la carpeta de la plataforma de contenedores, **linux-x64** o **windows-nano**. 
    2. Haga clic con el botón derecho en el archivo **Dockerfile** y haga clic en **Build IoT Edge module Docker image** (Generar imagen de Docker del módulo de IoT Edge). 
    3. En el cuadro **Seleccionar carpeta**, navegue hasta la carpeta del proyecto **FilterFunction** y haga clic en **Select Folder as EXE_DIR** (Seleccionar carpeta como EXE_DIR). 
    4. En el cuadro de texto emergente de la parte superior de la ventana de VS Code, escriba el nombre de la imagen. Por ejemplo, `<docker registry address>/filterfunction:latest`; donde la *dirección de Docker Registry* es su identificador de Docker si usa Docker Hub o es similar a `<your registry name>.azurecr.io`, si usa Azure Container Registry.
 
4. Inicie sesión en Docker. En el terminal integrado, escriba el siguiente comando: 

    - Docker Hub (escriba sus credenciales cuando se le solicite):
        
        ```csh/sh
        docker login
        ```

    - Azure Container Registry:
        
        ```csh/sh
        docker login -u <username> -p <password> <Login server>
        ```
        
        Para buscar el nombre de usuario, la contraseña y el servidor de inicio de sesión que se van a usar en este comando, vaya a [Azure Portal] (https://portal.azure.com). En **Todos los recursos**, haga clic en el icono para que Azure Container Registry abra sus propiedades y, después, haga clic en **Claves de acceso**. Copie los valores en los campos **Nombre de usuario**, **Contraseña** y **Servidor de inicio de sesión**. El servidor de inicio de sesión debe ser del tipo: `<your registry name>.azurecr.io`.

3. Inserte la imagen en el repositorio de Docker. Use el comando de menú **Ver | Paleta de comandos ... | Edge: insertar la imagen de Docker del módulo de IoT Edge** y escriba el nombre de la imagen en el cuadro de texto emergente de la parte superior de la ventana de VS Code. Use el mismo nombre de imagen que utilizó en el paso 1.c.

## <a name="add-registry-credentials-to-your-edge-device"></a>Adición de las credenciales del Registro a un dispositivo de Edge
Agregue las credenciales del Registro al runtime de Edge en el equipo en que ejecuta el dispositivo de Edge. Esto proporciona acceso al runtime para extraer el contenedor. 

- En Windows, ejecute el siguiente comando:
    
    ```cmd/sh
    iotedgectl login --address <docker-registry-address> --username <docker-username> --password <docker-password> 
    ```

- En Linux, ejecute el siguiente comando:
    
    ```cmd/sh
    sudo iotedgectl login --address <docker-registry-address> --username <docker-username> --password <docker-password> 
    ```

## <a name="run-the-solution"></a>Ejecución de la solución

1. En **Azure Portal**, navegue hasta su centro de IoT.
2. Vaya a **IoT Edge (versión preliminar)** y seleccione el dispositivo de IoT Edge.
1. Seleccione **Set modules** (Establecer módulos). 
2. Agregue el módulo **tempSensor**. Este paso solo es obligatorio si anteriormente no ha implementado el módulo **tempSensor** en el dispositivo de IoT Edge.
    1. Seleccione **Add IoT Edge module** (Agregar módulo de IoT Edge).
    2. En el campo **Nombre**, escriba `tempSensor`.
    3. En el campo **URI de la imagen**, escriba `microsoft/azureiotedge-simulated-temperature-sensor:1.0-preview`.
    4. Deje los restantes valores intactos y haga clic en **Guardar**.
1. Agregue el módulo **filterfunction**.
    1. Vuelva a seleccionar **Add IoT Edge Module** (Agregar módulo de IoT Edge).
    2. En el campo **Nombre**, escriba `filterfunction`.
    3. En el campo **Imagen**, escriba la dirección de la imagen; por ejemplo, `<docker registry address>/filterfunction:latest`.
    74. Haga clic en **Guardar**.
2. Haga clic en **Siguiente**.
3. En el paso **Specify Routes** (Especificar rutas), copie el archivo JSON siguiente en el cuadro de texto. Los módulos publican todos los mensajes en el runtime de Edge. Las reglas declarativas del runtime definen dónde fluyen esos mensajes. En este tutorial, necesita dos rutas. La primera transporta mensajes desde el sensor de temperatura hasta el módulo de filtro a través del punto de conexión "input1", que es el que configuró con el controlador **FilterMessages**. La segunda transporta mensajes desde el módulo de filtro hasta IoT Hub. En esta ruta, `upstream` es un destino especial que indica a Edge Hub que envíe mensajes a IoT Hub. 

    ```json
    {
       "routes":{
          "sensorToFilter":"FROM /messages/modules/tempSensor/outputs/temperatureOutput INTO BrokeredEndpoint(\"/modules/filterfunction/inputs/input1\")",
          "filterToIoTHub":"FROM /messages/modules/filterfunction/outputs/* INTO $upstream"
       }
    }
    ```

4. Haga clic en **Siguiente**.
5. En el paso **Review Template** (Revisar plantilla), haga clic en **Enviar**. 
6. Vuelva a la página de detalles del dispositivo de IoT Edge y haga clic en **Actualizar**. Debería ver que se están ejecutando el nuevo módulo **filtermodule**, junto con el módulo **tempSensor** y el **runtime de IoT Edge**. 

## <a name="view-generated-data"></a>Visualización de datos generados

Para supervisar los mensajes del dispositivo a la nube enviados al centro de IoT desde el dispositivo de IoT Edge:
1. Configure la extensión de Azure IoT Toolkit con la cadena de conexión del centro de IoT: 
    1. Use el comando de menú **Ver | Explorador** para abrir el explorador de VS Code. 
    3. En el explorador, haga clic en **IOT HUB DEVICES** y, luego, en **...**. Haga clic en **Set IoT Hub Connection String** (Establecer cadena de conexión de IoT Hub) y escriba la cadena de conexión del centro de IoT al que se conecta el dispositivo de IoT Edge en la ventana emergente. 

        Para buscar la cadena de conexión, haga clic en el icono del centro de IoT en Azure Portal y, después, en **Directivas de acceso compartido**. En **Directivas de acceso compartido**, haga clic en la directiva **iothubowner** y copie la cadena de conexión de IoT Hub en la ventana **iothubowner**.   

1. Para supervisar los datos que llegan al centro de IoT, use el comando de menú **Ver | Paleta de comandos... | IoT: inicio de la supervisión del mensaje de D2C**. 
2. Para dejar de supervisar datos, use el comando de menú **Ver | Paleta de comandos... | IoT: detención de la supervisión del mensaje de D2C**. 

## <a name="next-steps"></a>Pasos siguientes

En este tutorial, ha creado una instancia de Azure Functions que contiene código para filtrar datos sin procesar generados por el dispositivo IoT Edge. Para continuar con la exploración de Azure IoT Edge, aprenda a usar un dispositivo de IoT Edge como puerta de enlace. 

> [!div class="nextstepaction"]
> [Creación de un dispositivo de puerta de enlace con IoT Edge](how-to-create-transparent-gateway.md)

<!--Links-->
[lnk-tutorial1-win]: tutorial-simulate-device-windows.md
[lnk-tutorial1-lin]: tutorial-simulate-device-linux.md
