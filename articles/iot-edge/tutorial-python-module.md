---
title: Módulo de Python de Azure IoT Edge | Microsoft Docs
description: Creación de un módulo IoT Edge con código Python e implementación en un dispositivo perimetral
services: iot-edge
keywords: ''
author: shizn
manager: timlt
ms.author: xshi
ms.date: 03/18/2018
ms.topic: article
ms.service: iot-edge
ms.openlocfilehash: 3c46df85f95377f5740526542ac1baf5a8fd77c0
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 04/28/2018
ms.locfileid: "32177842"
---
# <a name="develop-and-deploy-a-python-iot-edge-module-to-your-simulated-device---preview"></a>Desarrollo e implementación de un módulo de Python de IoT Edge en su dispositivo simulado: versión preliminar

Los módulos de IoT Edge se pueden usar para implementar código que, a su vez, implementa una lógica de negocios directamente en los dispositivos de IoT Edge. Este tutorial le guía a través de la creación e implementación de un módulo de IoT Edge que filtra los datos de sensor. Usará el dispositivo de IoT Edge simulado que ha creado en los tutoriales sobre cómo implementar Azure IoT Edge en un dispositivo simulado de [Windows] [ lnk-tutorial1-win] o [Linux] [ lnk-tutorial1-lin]. En este tutorial, aprenderá a:    

> [!div class="checklist"]
> * Uso de Visual Studio Code para crear un módulo de Python de IoT Edge
> * Usar Visual Studio Code y Docker para crear una imagen de Docker y publicarla en el Registro 
> * Implementar el módulo en el dispositivo IoT Edge
> * Visualización de datos generados


El módulo IoT Edge que se crea en este tutorial filtra los datos de temperatura generados por el dispositivo. Solo envía mensajes al siguiente nivel si la temperatura es superior al umbral especificado. Este tipo de análisis perimetral resulta útil para reducir la cantidad de datos que se comunican a la nube y se almacenan en ella. 

> [!IMPORTANT]
> Actualmente el módulo de Python solo puede ejecutarse en contenedores Linux amd64; no puede ejecutarse en contenedores Windows ni en contenedores basados en ARM. 

## <a name="prerequisites"></a>requisitos previos

* El dispositivo de Azure IoT Edge que creó en la guía de inicio rápido o en el primer tutorial.
* La cadena de conexión de clave principal para el dispositivo de IoT Edge.  
* [Visual Studio Code](https://code.visualstudio.com/). 
* [Extensión de Azure IoT Edge para Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-edge). 
* [Extensión de Python para Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=ms-python.python). 
* [Docker](https://docs.docker.com/engine/installation/) en el mismo equipo que tiene Visual Studio Code. La Community Edition (CE) es suficiente para este tutorial. 
* [Python](https://www.python.org/downloads/).
* [Pip](https://pip.pypa.io/en/stable/installing/#installation) para instalar paquetes de Python (normalmente se incluye con la instalación de Python).

## <a name="create-a-container-registry"></a>Creación de un registro de contenedor
En este tutorial, puede usar la extensión de Azure IoT Edge para VS Code a fin de generar un módulo y crear una **imagen de contenedor** a partir de los archivos. Inserte luego esta imagen en un **Registro** que almacena y administra las imágenes. Por último, implemente la imagen en el Registro para que se ejecute en el dispositivo de IoT Edge.  

Puede usar cualquier registro compatible con Docker para este tutorial. Dos servicios populares del Registro de Docker disponibles en la nube son [Azure Container Registry](https://docs.microsoft.com/azure/container-registry/) y [Docker Hub](https://docs.docker.com/docker-hub/repos/#viewing-repository-tags). En este tutorial se usa Azure Container Registry. 

1. En [Azure Portal](https://portal.azure.com), seleccione **Crear un recurso** > **Contenedores** > **Azure Container Registry**.
2. Asigne un nombre al Registro, elija una suscripción, elija un grupo de recursos y establezca la SKU en el nivel **Básico**. 
3. Seleccione **Crear**.
4. Una vez creado el registro de contenedor, desplácese hasta él y seleccione **Claves de acceso**. 
5. Cambie **Usuario administrador** a **Habilitar**.
6. Copie los valores de **Servidor de inicio de sesión**, **Nombre de usuario** y **Contraseña**. Los usará más adelante en el tutorial. 

## <a name="create-an-iot-edge-module-project"></a>Crear un proyecto de módulo IoT Edge
En los siguientes pasos, puede ver cómo crear un módulo de Python de IoT Edge mediante Visual Studio Code y la extensión de Azure IoT Edge.
1. En Visual Studio Code, seleccione **Ver** > **Terminal integrado** para abrir el terminal integrado de VS Code.
2. En el terminal integrado, escriba el siguiente comando para instalar (o actualizar) **cookiecutter** (se recomienda hacer esto en un entorno virtual o como una instalación de usuario tal y como se muestra a continuación):

    ```cmd/sh
    pip install --upgrade --user cookiecutter
    ```

3. Cree un proyecto para el nuevo módulo. El comando siguiente crea la carpeta del proyecto, **FilterModule**, con el repositorio de contenedores. Si usa Azure Container Registry, el parámetro de `image_repository` debe tener el formato de `<your container registry name>.azurecr.io/filtermodule`. Escriba el siguiente comando en la carpeta de trabajo actual:

    ```cmd/sh
    cookiecutter --no-input https://github.com/Azure/cookiecutter-azure-iot-edge-module module_name=FilterModule image_repository=<your container registry address>/filtermodule
    ```
 
4. Seleccione **Archivo** > **Abrir carpeta**.
5. Vaya a la carpeta **FilterModule** y haga clic en **Seleccionar carpeta** para abrir el proyecto en VS Code.
6. En el explorador de VS Code, haga clic en **main.py** para abrirlo.
7. En la parte superior del espacio de nombres **FilterModule**, importe la biblioteca `json`:

    ```python
    import json
    ```

8. Agregue `TEMPERATURE_THRESHOLD`, `RECEIVE_CALLBACKS` y `TWIN_CALLBACKS` en los contadores globales. El umbral de temperatura establece el valor que debe superar la temperatura medida para que los datos se envíen a IoT Hub.

    ```python
    TEMPERATURE_THRESHOLD = 25
    TWIN_CALLBACKS = RECEIVE_CALLBACKS = 0
    ```

9. Actualice la función `receive_message_callback` con el siguiente contenido.

    ```python
    # receive_message_callback is invoked when an incoming message arrives on the specified 
    # input queue (in the case of this sample, "input1").  Because this is a filter module, 
    # we will forward this message onto the "output1" queue.
    def receive_message_callback(message, hubManager):
        global RECEIVE_CALLBACKS
        global TEMPERATURE_THRESHOLD
        message_buffer = message.get_bytearray()
        size = len(message_buffer)
        message_text = message_buffer[:size].decode('utf-8')
        print("    Data: <<<{}>>> & Size={:d}".format(message_text, size))
        map_properties = message.properties()
        key_value_pair = map_properties.get_internals()
        print("    Properties: {}".format(key_value_pair))
        RECEIVE_CALLBACKS += 1
        print("    Total calls received: {:d}".format(RECEIVE_CALLBACKS))
        data = json.loads(message_text)
        if "machine" in data and "temperature" in data["machine"] and data["machine"]["temperature"] > TEMPERATURE_THRESHOLD:
            map_properties.add("MessageType", "Alert")
            print("Machine temperature {} exceeds threshold {}".format(data["machine"]["temperature"], TEMPERATURE_THRESHOLD))
        hubManager.forward_event_to_output("output1", message, 0)
        return IoTHubMessageDispositionResult.ACCEPTED
    ```

10. Agregue una nueva función `device_twin_callback`. Esta función se invocará cuando se actualicen las propiedades deseadas.

    ```python
    # device_twin_callback is invoked when twin's desired properties are updated.
    def device_twin_callback(update_state, payload, user_context):
        global TWIN_CALLBACKS
        global TEMPERATURE_THRESHOLD
        print("\nTwin callback called with:\nupdateStatus = {}\npayload = {}\ncontext = {}".format(update_state, payload, user_context))
        data = json.loads(payload)
        if "desired" in data and "TemperatureThreshold" in data["desired"]:
            TEMPERATURE_THRESHOLD = data["desired"]["TemperatureThreshold"]
        if "TemperatureThreshold" in data:
            TEMPERATURE_THRESHOLD = data["TemperatureThreshold"]
        TWIN_CALLBACKS += 1
        print("Total calls confirmed: {:d}\n".format(TWIN_CALLBACKS))
    ```

11. En la clase `HubManager`, agregue una línea nueva al método `__init__` para inicializar la función `device_twin_callback` recién agregada.

    ```python
    # sets the callback when a twin's desired properties are updated.
    self.client.set_device_twin_callback(device_twin_callback, self)
    ```


12. Guarde este archivo.

## <a name="create-a-docker-image-and-publish-it-to-your-registry"></a>Crear una imagen de Docker y publicarla en el Registro

1. Inicie sesión en Docker escribiendo el comando siguiente en el terminal integrado de VS Code: 
     
   ```csh/sh
   docker login -u <username> -p <password> <Login server>
   ```
        
   Utilice el nombre de usuario, la contraseña y el servidor de inicio de sesión que ha copiado del Azure Container Registry al crearlo.

2. En el explorador de VS Code, haga clic con el botón derecho en el archivo **module.json** y haga clic en **Build and Push IoT Edge module Docker image** (Compilar e insertar la imagen de Docker del módulo de IoT Edge). En el cuadro de la lista desplegable emergente situado en la parte superior de la ventana de VS Code, seleccione la plataforma de contenedor, por ejemplo, **amd64** para el contenedor de Linux. Ponga en contenedores de VS Code `main.py` y las dependencias necesarias y, a continuación, insértelo en el registro de contenedor especificado. La imagen puede tardar varios minutos en compilarse por primera vez.

3. Puede obtener la dirección de la imagen de contenedor completa con la etiqueta en la terminal integrada de VS Code. Para obtener más información acerca de la definición de compilación y de inserción, puede consultar el archivo `module.json`.

## <a name="add-registry-credentials-to-edge-runtime"></a>Agregar las credenciales del Registro al runtime de Edge
Agregue las credenciales del Registro al runtime de Edge en el equipo en que ejecuta el dispositivo de Edge. Estas credenciales proporcionan acceso al runtime para extraer el contenedor. 

- En Windows, ejecute el siguiente comando:
    
    ```cmd/sh
    iotedgectl login --address <your container registry address> --username <username> --password <password> 
    ```

- En Linux, ejecute el siguiente comando:
    
    ```cmd/sh
    sudo iotedgectl login --address <your container registry address> --username <username> --password <password> 
    ```

## <a name="run-the-solution"></a>Ejecución de la solución

1. En [Azure Portal](https://portal.azure.com), navegue hasta su centro de IoT.
2. Vaya a **IoT Edge (versión preliminar)** y seleccione el dispositivo de IoT Edge.
3. Seleccione **Set modules** (Establecer módulos). 
2. Compruebe que el módulo **tempSensor** se rellena automáticamente. Si no es así, realice los pasos siguientes para agregarlo:
    1. Seleccione **Add IoT Edge module** (Agregar módulo de IoT Edge).
    2. En el campo **Nombre**, escriba `tempSensor`.
    3. En el campo **URI de la imagen**, escriba `microsoft/azureiotedge-simulated-temperature-sensor:1.0-preview`.
    4. Deje los restantes valores intactos y haga clic en **Guardar**.
9. Agregue el módulo **filterModule** que creó en secciones anteriores. 
    1. Seleccione **Add IoT Edge module** (Agregar módulo de IoT Edge).
    2. En el campo **Nombre**, escriba `filterModule`.
    3. En el campo **URI de la imagen**, escriba la dirección de la imagen; por ejemplo, `<your container registry address>/filtermodule:0.0.1-amd64`. La dirección de la imagen completa puede encontrarse en la sección anterior.
    4. Active la casilla **Habilitar** para que pueda modificar el módulo gemelo. 
    5. Reemplace el archivo JSON del cuadro de texto del módulo gemelo por el siguiente: 

        ```json
        {
           "properties.desired":{
              "TemperatureThreshold":25
           }
        }
        ```
 
    6. Haga clic en **Save**(Guardar).
10. Haga clic en **Next**.
11. En el paso **Specify Routes** (Especificar rutas), copie el archivo JSON siguiente en el cuadro de texto. Los módulos publican todos los mensajes en el runtime de Edge. Las reglas declarativas del runtime definen por dónde fluyen esos mensajes. En este tutorial, necesita dos rutas. La primera transporta mensajes desde el sensor de temperatura hasta el módulo de filtro a través del punto de conexión "input1", que es el que configuró con el controlador **FilterMessages**. La segunda transporta mensajes desde el módulo de filtro hasta IoT Hub. En esta ruta, `upstream` es un destino especial que indica a Edge Hub que envíe mensajes a IoT Hub. 

    ```json
    {
       "routes":{
          "sensorToFilter":"FROM /messages/modules/tempSensor/outputs/temperatureOutput INTO BrokeredEndpoint(\"/modules/filterModule/inputs/input1\")",
          "filterToIoTHub":"FROM /messages/modules/filterModule/outputs/output1 INTO $upstream"
       }
    }
    ```

4. Haga clic en **Next**.
5. En el paso **Review Template** (Revisar plantilla), haga clic en **Enviar**. 
6. Vuelva a la página de detalles del dispositivo IoT Edge y haga clic en **Actualizar**. Debería ver el nuevo **filtermodule** en ejecución junto con el módulo **tempSensor** y el **runtime de IoT Edge**. 

## <a name="view-generated-data"></a>Ver datos generados

Para supervisar los mensajes del dispositivo a la nube enviados al centro de IoT desde el dispositivo de IoT Edge:
1. Configure la extensión de Azure IoT Toolkit con la cadena de conexión del centro de IoT: 
    1. Abra el explorador de VS Code seleccionando **Ver** > **Explorador**. 
    3. En el explorador, haga clic en **IOT HUB DEVICES** y, luego, en **...**. Haga clic en **Set IoT Hub Connection String** (Establecer cadena de conexión de IoT Hub) y escriba la cadena de conexión del centro de IoT al que se conecta el dispositivo de IoT Edge en la ventana emergente. 

        Para buscar la cadena de conexión, haga clic en el icono del centro de IoT en Azure Portal y, después, en **Directivas de acceso compartido**. En **Directivas de acceso compartido**, haga clic en la directiva **iothubowner** y copie la cadena de conexión de IoT Hub en la ventana **iothubowner**.   

1. Para supervisar los datos que llegan a IoT Hub, seleccione **Ver** > **Paleta de comandos** y busque el comando de menú **IoT: Start monitoring D2C message** (IoT: iniciar la supervisión del mensaje de D2C). 
2. Para dejar de supervisar datos, use el comando de menú **IoT: Start monitoring D2C message** (IoT: detener la supervisión del mensaje de D2C). 

## <a name="next-steps"></a>Pasos siguientes

En este tutorial, creó un módulo IoT Edge que contiene código para filtrar datos sin procesar generados por el dispositivo IoT Edge. Puede continuar con cualquiera de los siguientes tutoriales para obtener información sobre otras formas en que Azure IoT Edge puede ayudarle a transformar datos en perspectivas empresariales en el límite.

> [!div class="nextstepaction"]
> [Deploy Azure Function as a module (Implementar Azure Functions como módulo)](tutorial-deploy-function.md)
> [Deploy Azure Stream Analytics as a module (Implementar Azure Stream Analytics como módulo)](tutorial-deploy-stream-analytics.md)


<!-- Links -->
[lnk-tutorial1-win]: tutorial-simulate-device-windows.md
[lnk-tutorial1-lin]: tutorial-simulate-device-linux.md

<!-- Images -->
[1]: ./media/tutorial-csharp-module/programcs.png
[2]: ./media/tutorial-csharp-module/build-module.png
[3]: ./media/tutorial-csharp-module/docker-os.png
