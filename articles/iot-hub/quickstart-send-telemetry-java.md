---
title: Inicio rápido para enviar datos de telemetría a Azure IoT Hub (Java) | Microsoft Docs
description: En este inicio rápido, ejecutará dos aplicaciones de Java de ejemplo para enviar datos de telemetría simulados a IoT Hub y para leer los datos de telemetría procedentes de este para procesarlos en la nube.
services: iot-hub
author: dominicbetts
manager: timlt
editor: ''
ms.service: iot-hub
ms.devlang: java
ms.topic: quickstart
ms.custom: mvc
ms.tgt_pltfrm: na
ms.workload: ns
ms.date: 04/30/2018
ms.author: dobett
ms.openlocfilehash: d887c690a5f0bc8120daa74d6076083634da08f6
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/07/2018
---
# <a name="quickstart-send-telemetry-from-a-device-to-an-iot-hub-and-read-the-telemetry-from-the-hub-with-a-back-end-application-java"></a>Inicio rápido: enviar datos de telemetría desde un dispositivo a IoT Hub y leer datos de telemetría procedentes de este con una aplicación back-end (Java)

[!INCLUDE [iot-hub-quickstarts-1-selector](../../includes/iot-hub-quickstarts-1-selector.md)]

IoT Hub es un servicio de Azure que le permite ingerir grandes volúmenes de datos de telemetría desde los dispositivos IoT en la nube para su almacenamiento o procesamiento. En este inicio rápido, enviará datos de telemetría desde una aplicación de dispositivo simulado, por IoT Hub, a una aplicación back-end para su procesamiento.

El inicio rápido usa dos aplicaciones de Java escritas previamente: una para enviar datos de telemetría y otra para leer datos de telemetría desde el centro. Antes de ejecutar estas dos aplicaciones, creará un centro de IoT Hub y registrará un dispositivo con el centro.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Si no tiene una suscripción a Azure, cree una [cuenta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de empezar.

## <a name="prerequisites"></a>requisitos previos

Las dos aplicaciones de ejemplo que se ejecutan en este inicio rápido se escriben con Java. Necesita Java SE 8 o una versión posterior en el equipo de desarrollo.

Puede descargar Java para varias plataformas desde [Oracle](http://www.oracle.com/technetwork/java/javase/downloads/index.html).

Puede verificar la versión actual de Java en el equipo de desarrollo con el comando siguiente:

```cmd/sh
java --version
```

Para compilar las muestras, deberá instalar Maven 3. Puede descargar Maven para varias plataformas desde [Apache Maven](https://maven.apache.org/download.cgi).

Puede verificar la versión actual de Maven en el equipo de desarrollo con el comando siguiente:

```cmd/sh
mvn --version
```

Descargue el proyecto de Java de muestra de https://github.com/Azure-Samples/azure-iot-samples-java/archive/master.zip y extraiga el archivo ZIP.

## <a name="create-an-iot-hub"></a>Crear un centro de IoT

[!INCLUDE [iot-hub-quickstarts-create-hub](../../includes/iot-hub-quickstarts-create-hub.md)]

## <a name="register-a-device"></a>Registrar un dispositivo

Debe registrar un dispositivo con IoT Hub antes de poder conectarlo. En esta guía de inicio rápido, va a usar la CLI de Azure para registrar un dispositivo simulado.

1. Agregue la extensión de la CLI de IoT Hub y cree la identidad del dispositivo. Reemplace `{YourIoTHubName}` por el nombre que ha elegido para IoT Hub:

    ```azurecli-interactive
    az extension add --name azure-cli-iot-ext
    az iot hub device-identity create --hub-name {YourIoTHubName}--device-id MyJavaDevice
    ```

1. Ejecute el siguiente comando para obtener la _cadena de conexión del dispositivo_ que acaba de registrar:

    ```azurecli-interactive
    az iot hub device-identity show-connection-string --hub-name {YourIoTHubName} --device-id MyJavaDevice --output table
    ```

    Anote la cadena de conexión del dispositivo, que será parecida a `Hostname=...=`. Usará este valor más adelante en este inicio rápido.

1. También necesitará el _punto de conexión compatible con Event Hubs_, la _ruta de acceso compatible con Event Hubs_ y la _clave principal iothubowner_ de IoT Hub para permitir que la aplicación back-end se conecte a IoT Hub y recupere los mensajes. Los siguientes comandos recuperan estos valores para IoT Hub:

    ```azurecli-interactive
    az iot hub show --query properties.eventHubEndpoints.events.endpoint --name {YourIoTHubName}

    az iot hub show --query properties.eventHubEndpoints.events.path --name {YourIoTHubName}

    az iot hub policy show --name iothubowner --query primaryKey --hub-name {your IoT Hub name}
    ```

    Anote estos tres valores: deberá usarlos más adelante en el inicio rápido.

## <a name="send-simulated-telemetry"></a>Envío de datos de telemetría simulados

La aplicación de dispositivo simulado se conecta a un punto de conexión específico del dispositivo en IoT Hub y envía datos de telemetría simulados sobre temperatura y humedad.

1. En una ventana de terminal, vaya a la carpeta raíz del proyecto de Java de muestra. A continuación, vaya a la carpeta **Quickstarts\simulated-device**.

1. Abra el archivo **src/main/java/com/microsoft/docs/iothub/samples/SimulatedDevice.java** en el editor de texto de su elección.

    Reemplace el valor de la variable `connString` por la cadena de conexión de dispositivo que anotó anteriormente. A continuación, guarde los cambios realizados en el archivo **SimulatedDevice.java**.

1. En la ventana de terminal, ejecute los comandos siguientes para instalar las bibliotecas necesarias y compile la aplicación de dispositivo simulado:

    ```cmd/sh
    mvn clean package
    ```

1. En la ventana de terminal, ejecute los comandos siguientes para ejecutar la aplicación de dispositivo simulado:

    ```cmd/sh
    java -jar target/simulated-device-1.0.0-with-deps.jar
    ```

    La siguiente captura de pantalla muestra la salida en la que la aplicación de dispositivo simulado envía datos de telemetría a IoT Hub:

    ![Ejecutar el dispositivo simulado](media/quickstart-send-telemetry-java/SimulatedDevice.png)

## <a name="read-the-telemetry-from-your-hub"></a>Lectura de los datos de telemetría procedentes de su instancia de IoT Hub

La aplicación back-end se conecta a un punto de conexión de **Eventos** de servicio en IoT Hub. La aplicación recibe los mensajes del dispositivo a la nube enviados desde el dispositivo simulado. Normalmente, una aplicación back-end de IoT Hub se ejecuta en la nube para recibir y procesar los mensajes del dispositivo a la nube.

1. En otra ventana de terminal, vaya a la carpeta raíz del proyecto de Java de muestra. A continuación, vaya a la carpeta **Quickstarts\read-d2c-messages**.

1. Abra el archivo **src/main/java/com/microsoft/docs/iothub/samples/ReadDeviceToCloudMessages.java** en el editor de texto de su elección.

    Reemplace el valor de la variable `eventHubsCompatibleEndpoint` por el punto de conexión compatible con Event Hubs que anotó anteriormente.

    Reemplace el valor de la variable `eventHubsCompatiblePath` por la ruta de acceso compatible con Event Hubs que anotó anteriormente.

    Reemplace el valor de la variable `iotHubSasKey` por la clave principal iothubowner que anotó anteriormente. A continuación, guarde los cambios realizados en el archivo **ReadDeviceToCloudMessages.java**.

1. En la ventana de terminal, ejecute los comandos siguientes para instalar las bibliotecas necesarias y compile la aplicación back-end:

    ```cmd/sh
    mvn clean package
    ```

1. En la ventana de terminal, ejecute los comandos siguientes para ejecutar la aplicación back-end:

    ```cmd/sh
    java -jar target/read-d2c-messages-1.0.0-with-deps.jar
    ```

    La siguiente captura de pantalla muestra la salida en la que la aplicación back-end recibe los datos de telemetría que el dispositivo simulado ha enviado al centro:

    ![Ejecutar la aplicación back-end](media/quickstart-send-telemetry-java/ReadDeviceToCloud.png)

## <a name="clean-up-resources"></a>Limpieza de recursos

Si tiene pensado completar el siguiente inicio rápido, deje IoT Hub y el grupo de recursos y vuelva a usarlos más adelante.

Si ya no los va a necesitar más, elimínelos en el portal. Para ello, seleccione el grupo de recursos **qs-iot-hub-rg** que contiene la instancia de IoT Hub y haga clic en **Eliminar**.

## <a name="next-steps"></a>Pasos siguientes

En este inicio rápido, ha configurado IoT Hub, registrado un dispositivo, enviado datos de telemetría simulados al centro con una aplicación de Java y leído datos de telemetría desde el centro con una aplicación back-end básica.

Para aprender a controlar el dispositivo simulado desde una aplicación back-end, vaya al siguiente inicio rápido.

> [!div class="nextstepaction"]
> [Inicio rápido: controlar un dispositivo conectado a IoT Hub](quickstart-control-device-java.md)