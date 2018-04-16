---
title: Implementación de Azure Functions con Azure IoT Edge | Microsoft Docs
description: Implementación de Azure Functions como módulo en un dispositivo perimetral
services: iot-edge
keywords: ''
author: kgremban
manager: timlt
ms.author: kgremban
ms.date: 04/02/2018
ms.topic: tutorial
ms.service: iot-edge
ms.custom: mvc
ms.openlocfilehash: f1c6b5cd07752c6b29234a365b3298d76b639b3a
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 04/06/2018
---
# <a name="deploy-azure-function-as-an-iot-edge-module---preview"></a>Implementación de Azure Functions como módulo de IoT Edge (versión preliminar)
Azure Functions se puede usar para implementar código que, a su vez, implementa una lógica de negocios directamente en los dispositivos de IoT Edge. Este tutorial le guía a través de la creación y la implementación de una instancia de Azure Functions que filtra datos del sensor en el dispositivo de IoT Edge simulado que creó en el paso de implementación de Azure IoT Edge en un dispositivo simulado en los tutoriales de [Windows][lnk-tutorial1-win] o [Linux][lnk-tutorial1-lin]. En este tutorial, aprenderá a:     

> [!div class="checklist"]
> * Usar Visual Studio Code para crear una instancia de Azure Functions
> * Usar VS Code y Docker para crear una imagen de Docker y publicarla en el Registro 
> * Implementación del módulo en un dispositivo de IoT Edge
> * Visualización de datos generados


La instancia de Azure Functions que se crea en este tutorial filtra los datos de temperatura generados por el dispositivo y solo envía mensajes en dirección ascendente a Azure IoT Hub si la temperatura se encuentra por encima de un umbral especificado. 

## <a name="prerequisites"></a>requisitos previos

* El dispositivo de Azure IoT Edge que creó en el tutorial anterior o en la guía de inicio rápido.
* [Visual Studio Code](https://code.visualstudio.com/). 
* [Extensión de C# para Visual Studio Code (con tecnología de OmniSharp)](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp) 
* [Extensión de Azure IoT Edge para Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-edge). 
* [Docker](https://docs.docker.com/engine/installation/). La Community Edition (CE) de la plataforma es suficiente para este tutorial. 
* [SDK de .NET Core 2.0](https://www.microsoft.com/net/core#windowscmd). 

## <a name="create-a-container-registry"></a>Creación de un registro de contenedor
En este tutorial, puede usar la extensión de Azure IoT Edge para VS Code a fin de generar un módulo y crear una **imagen de contenedor** a partir de los archivos. Inserte luego esta imagen en un **Registro** que almacena y administra las imágenes. Por último, implemente la imagen en el Registro para que se ejecute en el dispositivo de IoT Edge.  

Puede usar cualquier registro compatible con Docker para este tutorial. Dos servicios populares del Registro de Docker disponibles en la nube son [Azure Container Registry](https://docs.microsoft.com/azure/container-registry/) y [Docker Hub](https://docs.docker.com/docker-hub/repos/#viewing-repository-tags). En este tutorial se usa Azure Container Registry. 

1. En [Azure Portal](https://portal.azure.com), seleccione **Crear un recurso** > **Contenedores** > **Azure Container Registry**.
2. Asigne un nombre al Registro, elija una suscripción, elija un grupo de recursos y establezca la SKU en el nivel **Básico**. 
3. Seleccione **Crear**.
4. Una vez creado el registro de contenedor, desplácese hasta él y seleccione **Claves de acceso**. 
5. Cambie **Usuario administrador** a **Habilitar**.
6. Copie los valores de **Servidor de inicio de sesión**, **Nombre de usuario** y **Contraseña**. Los usará más adelante en el tutorial. 

## <a name="create-a-function-project"></a>Creación de un proyecto de aplicación de una función
En los siguientes pasos puede ver cómo crear una función de IoT Edge mediante Visual Studio Code y la extensión de Azure IoT Edge.
1. Abra Visual Studio Code.
2. Para abrir el terminal integrado de VSCode, seleccione **Ver** > **Terminal integrado**.
3. Para instalar o actualizar la plantilla **AzureIoTEdgeFunction** en DotNet, ejecute el comando siguiente en el terminal integrado:

    ```cmd/sh
    dotnet new -i Microsoft.Azure.IoT.Edge.Function
    ```
2. Cree un proyecto para el nuevo módulo. El comando siguiente crea la carpeta del proyecto, **FilterFunction**, con el repositorio de contenedores. Si usa Azure Container Registry, el segundo parámetro debe tener el formato de `<your container registry name>.azurecr.io`. Escriba el siguiente comando en la carpeta de trabajo actual:

    ```cmd/sh
    dotnet new aziotedgefunction -n FilterFunction -r <your container registry address>/filterfunction
    ```

3. Seleccione **Archivo** > **Abrir carpeta** y, a continuación, busque la carpeta **FilterFunction** y abra el proyecto en VSCode.
4. En el explorador de VSCode, expanda la carpeta **EdgeHubTrigger-Csharp** y, después, abra el archivo **run.csx**.
5. Reemplace el contenido del archivo por el código siguiente:

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
                    filteredMessage.Properties.Add(prop.Key, prop.Value);                }
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

11. Guarde el archivo.

## <a name="create-a-docker-image-and-publish-it-to-your-registry"></a>Crear una imagen de Docker y publicarla en el Registro

1. Inicie sesión en Docker escribiendo el comando siguiente en el terminal integrado de VS Code: 
     
   ```csh/sh
   docker login -u <ACR username> -p <ACR password> <ACR login server>
   ```
   Para buscar el nombre de usuario, la contraseña y el servidor de inicio de sesión que se van a usar en este comando, vaya a [Azure Portal] (https://portal.azure.com). En **Todos los recursos**, haga clic en el icono para que Azure Container Registry abra sus propiedades y, después, haga clic en **Claves de acceso**. Copie los valores en los campos **Nombre de usuario**, **Contraseña** y **Servidor de inicio de sesión**. 

2. Abra **module.json**. Si lo desea, puede actualizar `"version"` a, por ejemplo, **"1.0"**. También se muestra el nombre del repositorio que especificó en el parámetro `-r` de `dotnet new aziotedgefunction`.

3. Guarde el archivo **module.json**.

4. En el explorador de VS Code, haga clic con el botón derecho en el archivo **module.json** y haga clic en **Build and Push IoT Edge module Docker image** (Compilar e insertar la imagen de Docker del módulo de IoT Edge). En el cuadro de lista desplegable emergente situado en la parte superior de la ventana de VS Code, seleccione la plataforma de contenedor, **amd64** para el contenedor de Linux o **windows-amd64** para el contenedor de Windows. A continuación, VS Code pone en contenedores los códigos de función y los inserta en el registro de contenedor especificado.

5. Puede obtener la dirección de la imagen de contenedor completa con la etiqueta en la terminal integrada de VS Code. Para obtener más información acerca de la definición de compilación y de inserción, puede consultar el archivo `module.json`.

## <a name="add-registry-credentials-to-your-edge-device"></a>Adición de las credenciales del Registro a un dispositivo de Edge
Agregue las credenciales del Registro al runtime de Edge en el equipo en que ejecuta el dispositivo de Edge. Esto proporciona acceso al runtime para extraer el contenedor. 

- En Windows, ejecute el siguiente comando:
    
    ```cmd/sh
    iotedgectl login --address <your container registry address> --username <username> --password <password> 
    ```

- En Linux, ejecute el siguiente comando:
    
    ```cmd/sh
    sudo iotedgectl login --address <your container registry address> --username <username> --password <password> 
    ```

## <a name="run-the-solution"></a>Ejecución de la solución

1. En **Azure Portal**, navegue hasta su centro de IoT.
2. Vaya a **IoT Edge (versión preliminar)** y seleccione el dispositivo de IoT Edge.
1. Seleccione **Set modules** (Establecer módulos). 
2. Si ya ha implementado el módulo **tempSensor** en este dispositivo, se puede completar automáticamente. En caso contrario, siga estos pasos para agregarlo:
    1. Seleccione **Add IoT Edge module** (Agregar módulo de IoT Edge).
    2. En el campo **Nombre**, escriba `tempSensor`.
    3. En el campo **URI de la imagen**, escriba `microsoft/azureiotedge-simulated-temperature-sensor:1.0-preview`.
    4. Deje los restantes valores intactos y haga clic en **Guardar**.
1. Agregue el módulo **filterFunction**.
    1. Vuelva a seleccionar **Add IoT Edge Module** (Agregar módulo de IoT Edge).
    2. En el campo **Nombre**, escriba `filterFunction`.
    3. En el campo **URI de la imagen**, escriba la dirección de la imagen; por ejemplo, `<your container registry address>/filterfunction:0.0.1-amd64`. La dirección de la imagen completa puede encontrarse en la sección anterior.
    74. Haga clic en **Save**(Guardar).
2. Haga clic en **Next**.
3. En el paso **Specify Routes** (Especificar rutas), copie el archivo JSON siguiente en el cuadro de texto. La primera ruta transporta los mensajes del sensor de temperatura al módulo de filtro a través del punto de conexión "input1". La segunda transporta mensajes desde el módulo de filtro hasta IoT Hub. En esta ruta, `$upstream` es un destino especial que indica a Edge Hub que envíe mensajes a IoT Hub. 

    ```json
    {
       "routes":{
          "sensorToFilter":"FROM /messages/modules/tempSensor/outputs/temperatureOutput INTO BrokeredEndpoint(\"/modules/filterFunction/inputs/input1\")",
          "filterToIoTHub":"FROM /messages/modules/filterFunction/outputs/* INTO $upstream"
       }
    }
    ```

4. Haga clic en **Next**.
5. En el paso **Review Template** (Revisar plantilla), haga clic en **Enviar**. 
6. Vuelva a la página de detalles del dispositivo de IoT Edge y haga clic en **Actualizar**. Debería ver que se están ejecutando el nuevo módulo **filtermodule**, junto con el módulo **tempSensor** y el **runtime de IoT Edge**. 

## <a name="view-generated-data"></a>Visualización de datos generados

Para supervisar los mensajes del dispositivo a la nube enviados al centro de IoT desde el dispositivo de IoT Edge:
1. Configure la extensión de Azure IoT Toolkit con la cadena de conexión del centro de IoT: 
    1. En Azure Portal, vaya al centro de IoT y seleccione **Directivas de acceso compartido**. 
    2. Seleccione **iothubowner** y, a continuación, copie el valor de **Cadena de conexión: clave principal**.
    3. En el explorador de VSCode, haga clic en **IOT HUB DEVICES** y, luego, en **...**. 
    4. Seleccione **Set IoT Hub Connection String** (Establecer cadena de conexión de IoT Hub) y especifique la cadena de conexión de IoT Hub en la ventana emergente. 

2. Para supervisar los datos que llegan a IoT Hub, seleccione **Ver** > **Paleta de comandos** y busque **IoT: Start monitoring D2C message** (IoT: iniciar la supervisión del mensaje de D2C). 
3. Para dejar de supervisar datos, use el comando **IoT: Stop monitoring D2C message** (IoT: detener la supervisión del mensaje de D2C) en la paleta de comandos. 

## <a name="next-steps"></a>Pasos siguientes

En este tutorial, ha creado una instancia de Azure Functions que contiene código para filtrar datos sin procesar generados por el dispositivo IoT Edge. Para continuar con la exploración de Azure IoT Edge, aprenda a usar un dispositivo de IoT Edge como puerta de enlace. 

> [!div class="nextstepaction"]
> [Creación de un dispositivo de puerta de enlace con IoT Edge](how-to-create-transparent-gateway.md)

<!--Links-->
[lnk-tutorial1-win]: tutorial-simulate-device-windows.md
[lnk-tutorial1-lin]: tutorial-simulate-device-linux.md
