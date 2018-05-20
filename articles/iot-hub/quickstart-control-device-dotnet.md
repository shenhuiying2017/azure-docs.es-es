---
title: Inicio rápido para controlar un dispositivo desde Azure IoT Hub (.NET) | Microsoft Docs
description: En este inicio rápido, ejecuta dos aplicaciones C# de muestra. Una aplicación es una aplicación back-end que puede controlar dispositivos conectados al centro de manera remota. La otra aplicación simula un dispositivo conectado al centro que se puede controlar de manera remota.
services: iot-hub
author: dominicbetts
manager: timlt
editor: ''
ms.service: iot-hub
ms.devlang: dotnet
ms.topic: quickstart
ms.custom: mvc
ms.tgt_pltfrm: na
ms.workload: ns
ms.date: 04/30/2018
ms.author: dobett
ms.openlocfilehash: d0233d2bd68397491e22cc7eb6993d0909f92218
ms.sourcegitcommit: e14229bb94d61172046335972cfb1a708c8a97a5
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/14/2018
---
# <a name="quickstart-control-a-device-connected-to-an-iot-hub-net"></a>Inicio rápido: controlar un dispositivo conectado a IoT Hub (.NET)

[!INCLUDE [iot-hub-quickstarts-2-selector](../../includes/iot-hub-quickstarts-2-selector.md)]

IoT Hub es un servicio de Azure que le permite ingerir grandes volúmenes de datos de telemetría desde los dispositivos IoT en la nube y administrar dispositivos desde la nube. En este inicio rápido, usa un *método directo* para controlar un dispositivo simulado conectado a IoT Hub. Puede usar métodos directos para cambiar el comportamiento de un dispositivo conectado a IoT Hub de manera remota.

El inicio rápido usa dos aplicaciones .NET escritas anteriormente:

* Una aplicación de dispositivo simulado que responde a métodos directos que se llaman desde una aplicación back-end. Para recibir las llamadas de método directo, esta aplicación se conecta a un punto de conexión específico del dispositivo en IoT Hub.
* Una aplicación back-end que llama a los métodos directos en el dispositivo simulado. Para llamar a un método directo en un dispositivo, esta aplicación se conecta a un punto de conexión de servicio en IoT Hub.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Si no tiene una suscripción a Azure, cree una [cuenta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de empezar.

## <a name="prerequisites"></a>requisitos previos

Las dos aplicaciones de ejemplo que se ejecutan en este inicio rápido se escriben con C#. Necesita el SDK de .NET Core 2.1.0 o una versión posterior en el equipo de desarrollo.

Puede descargar el SDK de .NET Core para varias plataformas desde [.NET](https://www.microsoft.com/net/download/all).

Puede verificar la versión actual de C# en el equipo de desarrollo con el comando siguiente:

```cmd/sh
dotnet --version
```

Si aún no lo ha hecho, descargue el proyecto de C# de muestra desde https://github.com/Azure-Samples/azure-iot-samples-csharp/archive/master.zip y extraiga el archivo ZIP.

## <a name="create-an-iot-hub"></a>Crear un centro de IoT

Si ha completado el anterior [Quickstart: Send telemetry from a device to an IoT hub](quickstart-send-telemetry-dotnet.md) (Inicio rápido: enviar datos de telemetría desde un dispositivo a IoT Hub), puede omitir este paso.

[!INCLUDE [iot-hub-quickstarts-create-hub](../../includes/iot-hub-quickstarts-create-hub.md)]

## <a name="register-a-device"></a>Registrar un dispositivo

Si ha completado el anterior [Quickstart: Send telemetry from a device to an IoT hub](quickstart-send-telemetry-dotnet.md) (Inicio rápido: enviar datos de telemetría desde un dispositivo a IoT Hub), puede omitir este paso.

Debe registrar un dispositivo con IoT Hub antes de poder conectarlo. En esta guía de inicio rápido, va a usar la CLI de Azure para registrar un dispositivo simulado.

1. Agregue la extensión de la CLI de IoT Hub y cree la identidad del dispositivo. Reemplace `{YourIoTHubName}` por el nombre que ha elegido para IoT Hub:

    ```azurecli-interactive
    az extension add --name azure-cli-iot-ext
    az iot hub device-identity create --hub-name {YourIoTHubName} --device-id MyDotnetDevice
    ```

    Si elige otro nombre para el dispositivo, actualícelo en las aplicaciones de ejemplo antes de ejecutarlas.

1. Ejecute el siguiente comando para obtener la _cadena de conexión del dispositivo_ que acaba de registrar:

    ```azurecli-interactive
    az iot hub device-identity show-connection-string --hub-name {YourIoTHubName} --device-id MyDotnetDevice --output table
    ```

    Anote la cadena de conexión del dispositivo, que será parecida a `Hostname=...=`. Usará este valor más adelante en este inicio rápido.

## <a name="retrieve-the-service-connection-string"></a>Recuperación de la cadena de conexión de servicio

También necesita una _cadena de conexión de servicio_ de IoT Hub para permitir que la aplicación back-end se conecte al centro y recupere los mensajes. El comando siguiente recupera la cadena de conexión del servicio de su instancia de IoT Hub:

```azurecli-interactive
az iot hub show-connection-string --hub-name {YourIoTHubName} --output table
```

Anote la cadena de conexión del servicio, que será parecida a `Hostname=...=`. Usará este valor más adelante en este inicio rápido.

## <a name="listen-for-direct-method-calls"></a>Escuchas para llamadas de método directo

La aplicación del dispositivo simulado se conecta a un punto de conexión específico del dispositivo en IoT Hub, envía los datos de telemetría simulados y escucha llamadas de método directo desde el centro. En este inicio rápido, la llamada de método directo desde el centro indica al dispositivo que debe cambiar el intervalo en el que envía los datos de telemetría. El dispositivo simulado envía una confirmación al centro después de que ejecute el método directo.

1. En una ventana de terminal, vaya a la carpeta raíz del proyecto de C# de muestra. A continuación, vaya a la carpeta **Quickstarts\simulated-device-2**.

1. Abra el archivo **SimulatedDevice.cs** en el editor de texto de su elección.

    Reemplace el valor de la variable `connectionString` por la cadena de conexión de dispositivo que anotó anteriormente. A continuación, guarde los cambios realizados en el archivo **SimulatedDevice.cs**.

1. En la ventana de terminal, ejecute los comandos siguientes para instalar los paquetes necesarios para la aplicación de dispositivo simulado:

    ```cmd/sh
    dotnet restore
    ```

1. En la ventana de terminal, ejecute el comando siguiente para compilar la aplicación de dispositivo simulado y ejecutarla:

    ```cmd/sh
    dotnet run
    ```

    La siguiente captura de pantalla muestra la salida en la que la aplicación de dispositivo simulado envía datos de telemetría a IoT Hub:

    ![Ejecutar el dispositivo simulado](media/quickstart-control-device-dotnet/SimulatedDevice-1.png)

## <a name="call-the-direct-method"></a>Llamar al método directo

La aplicación back-end se conecta a un punto de conexión de servicio en IoT Hub. La aplicación realiza llamadas de método directo a un dispositivo con IoT Hub y realiza escuchas para confirmaciones. Normalmente, una aplicación back-end de IoT Hub se ejecuta en la nube.

1. En otra ventana de terminal, vaya a la carpeta raíz del proyecto de C# de muestra. A continuación, vaya a la carpeta **Quickstarts\back-end-application**.

1. Abra el archivo **BackEndApplication.cs** en el editor de texto de su elección.

    Reemplace el valor de la variable `connectionString` por la cadena de conexión de servicio que anotó anteriormente. A continuación, guarde los cambios realizados en el archivo **BackEndApplication.cs**.

1. En la ventana de terminal, ejecute los comandos siguientes para instalar las bibliotecas necesarias para la aplicación back-end:

    ```cmd/sh
    dotnet restore
    ```

1. En la ventana de terminal, ejecute los comandos siguientes para compilar la aplicación back-end y ejecutarla:

    ```cmd/sh
    dotnet run
    ```

    La siguiente captura de pantalla muestra la salida en la que la aplicación realiza una llamada de método directo en el dispositivo y recibe una confirmación:

    ![Ejecutar la aplicación back-end](media/quickstart-control-device-dotnet/BackEndApplication.png)

    Después de ejecutar la aplicación back-end, verá un mensaje en la ventana de consola que ejecuta el dispositivo simulado y cambiará la velocidad a la que envía mensajes:

    ![Cambio en el cliente simulado](media/quickstart-control-device-dotnet/SimulatedDevice-2.png)

## <a name="clean-up-resources"></a>Limpieza de recursos

Si tiene pensado pasar a los tutoriales, deje IoT Hub y el grupo de recursos y vuelva a usarlos más adelante.

Si ya no los va a necesitar más, elimínelos en el portal. Para ello, seleccione el grupo de recursos que contiene la instancia de IoT Hub y haga clic en **Eliminar**.

## <a name="next-steps"></a>Pasos siguientes

En este inicio rápido, ha llamado a un método directo en un dispositivo desde una aplicación back-end y ha respondido a la llamada de método directo en una aplicación de dispositivo simulado.

Para obtener información sobre cómo redirigir mensajes del dispositivo a la nube a diferentes destinos en la nube, continúe con el siguiente tutorial.

> [!div class="nextstepaction"]
> [Tutorial: Enrutar datos de telemetría a distintos puntos de conexión para procesamiento](iot-hub-csharp-csharp-process-d2c.md)