---
title: "Introducción a Azure IoT Hub (.NET) | Microsoft Docs"
description: "Obtenga información sobre cómo enviar mensajes del dispositivo a la nube a una instancia de Azure IoT Hub mediante los SDK de IoT para .NET. Cree aplicaciones de servicio y de dispositivo simuladas para registrar el dispositivo, enviar mensajes y leerlos en IoT Hub."
services: iot-hub
documentationcenter: .net
author: dominicbetts
manager: timlt
editor: 
ms.assetid: f40604ff-8fd6-4969-9e99-8574fbcf036c
ms.service: iot-hub
ms.devlang: dotnet
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/08/2017
ms.author: dobett
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: ec9ff4a866ef8736ed260b4d17aa997433c1ef8d
ms.sourcegitcommit: 4ac89872f4c86c612a71eb7ec30b755e7df89722
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/07/2017
---
# <a name="connect-your-device-to-your-iot-hub-using-net"></a>Conexión del dispositivo en IoT Hub con .NET

[!INCLUDE [iot-hub-selector-get-started](../../includes/iot-hub-selector-get-started.md)]

Al final de este tutorial tendrá tres aplicaciones de consola de .NET:

* **CreateDeviceIdentity**, que crea una identidad de dispositivo y una clave de seguridad asociada para conectar la aplicación de dispositivo.
* **ReadDeviceToCloudMessages**, que muestra los datos de telemetría enviados por la aplicación de dispositivo.
* **SimulatedDevice**, que se conecta con su IoT Hub con la identidad de dispositivo creada anteriormente, y envía un mensaje de telemetría cada segundo mediante el protocolo MQTT.

Puede descargar o clonar la solución de Visual Studio, que contiene las tres aplicaciones de Github.

```bash
git clone https://github.com/Azure-Samples/iot-hub-dotnet-simulated-device-client-app.git
```

> [!NOTE]
> Para más información acerca de los diversos SDK que puede usar para crear ambas aplicaciones para que se ejecuten en dispositivos y en el back-end de la solución, consulte [Azure IoT SDKs][lnk-hub-sdks] (SDK de IoT de Azure).

Para completar este tutorial, necesitará lo siguiente:

* Visual Studio 2015 o Visual Studio 2017.
* Una cuenta de Azure activa. (En caso de no tenerla, puede crear una [cuenta gratuita][lnk-free-trial] en solo unos minutos).

[!INCLUDE [iot-hub-get-started-create-hub](../../includes/iot-hub-get-started-create-hub.md)]

Ya se creó IoT Hub y ya tiene el nombre de host y la cadena de conexión de IoT Hub que necesita para completar el resto del tutorial.

<a id="DeviceIdentity_csharp"></a>
[!INCLUDE [iot-hub-get-started-create-device-identity-csharp](../../includes/iot-hub-get-started-create-device-identity-csharp.md)]

<a id="D2C_csharp"></a>
## <a name="receive-device-to-cloud-messages"></a>Recepción de mensajes de dispositivo a nube
En esta sección, creará una aplicación de consola de .NET que lee los mensajes de dispositivo a nube desde un IoT Hub. El centro de IoT expone un punto de conexión compatible con [Azure Event Hubs][lnk-event-hubs-overview] para permitirle leer los mensajes del dispositivo a la nube. Para simplificar las cosas, este tutorial crea un lector básico que no es apto para una implementación de alta capacidad de procesamiento. El tutorial sobre [procesamiento de mensajes del dispositivo a la nube][lnk-process-d2c-tutorial] muestra cómo procesar mensajes del dispositivo a la nube a escala. Para más información sobre cómo procesar los mensajes de los Centros de eventos, consulte el tutorial [Introducción a Event Hubs][lnk-eventhubs-tutorial]. (Este tutorial es aplicable a los puntos de conexión compatibles con los centros de eventos de IoT Hub).

> [!NOTE]
> El punto de conexión compatible con Event Hubs para leer mensajes de dispositivo a la nube siempre usa el protocolo AMQP.

1. En Visual Studio, agregue un proyecto de escritorio clásico de Windows de Visual C# a la solución actual mediante la plantilla de proyecto **Aplicación de consola (.NET Framework)**. Asegúrese de que la versión de .NET Framework sea 4.5.1 o una posterior. Asigne al proyecto el nombre **ReadDeviceToCloudMessages**.

    ![Nuevo proyecto de escritorio clásico de Windows de Visual C#][10a]

2. En el Explorador de soluciones, haga clic con el botón derecho en el proyecto **ReadDeviceToCloudMessages** y luego haga clic en **Administrar paquetes NuGet**.

3. En la ventana **Administrador de paquetes NuGet**, busque **WindowsAzure.ServiceBus**, seleccione **Instalar** y acepte los términos de uso. Este procedimiento permite descargar, instalar y agregar una referencia a [Azure Service Bus][lnk-servicebus-nuget], con todas sus dependencias. Este paquete permite que la aplicación para se conecte al punto de conexión compatible con centros de eventos en su centro de IoT.

4. Agregue las siguientes instrucciones `using` al principio del archivo **Program.cs** :

    ```csharp
    using Microsoft.ServiceBus.Messaging;
    using System.Threading;
    ```

5. Agregue los campos siguientes a la clase **Program** . Sustituya el valor de marcador de posición por la cadena de conexión de IoT Hub que creó en la sección "Creación de un IoT Hub".

    ```csharp
    static string connectionString = "{iothub connection string}";
    static string iotHubD2cEndpoint = "messages/events";
    static EventHubClient eventHubClient;
    ```

6. Agregue el método siguiente a la clase **Program** :

    ```csharp
    private static async Task ReceiveMessagesFromDeviceAsync(string partition, CancellationToken ct)
    {
        var eventHubReceiver = eventHubClient.GetDefaultConsumerGroup().CreateReceiver(partition, DateTime.UtcNow);
        while (true)
        {
            if (ct.IsCancellationRequested) break;
            EventData eventData = await eventHubReceiver.ReceiveAsync();
            if (eventData == null) continue;

            string data = Encoding.UTF8.GetString(eventData.GetBytes());
            Console.WriteLine("Message received. Partition: {0} Data: '{1}'", partition, data);
        }
    }
    ```

    Este método usa una instancia de **EventHubReceiver** para recibir mensajes de todas las particiones de recepción de dispositivo a nube del centro de IoT. Observe cómo pasar un parámetro `DateTime.Now` al crear el objeto **EventHubReceiver** para que solo reciba los mensajes enviados después de iniciarse. Este filtro es útil en un entorno de prueba, porque puede ver el conjunto actual de mensajes. En un entorno de producción, el código debe asegurarse de que este procesa todos los mensajes. Para más información, consulte el tutorial [Procesamiento de mensajes de dispositivo a la nube de IoT Hub][lnk-process-d2c-tutorial].

7. Por último, agregue las líneas siguientes al método **Main** :

    ```csharp
    Console.WriteLine("Receive messages. Ctrl-C to exit.\n");
    eventHubClient = EventHubClient.CreateFromConnectionString(connectionString, iotHubD2cEndpoint);

    var d2cPartitions = eventHubClient.GetRuntimeInformation().PartitionIds;

    CancellationTokenSource cts = new CancellationTokenSource();

    System.Console.CancelKeyPress += (s, e) =>
    {
        e.Cancel = true;
        cts.Cancel();
        Console.WriteLine("Exiting...");
    };

    var tasks = new List<Task>();
    foreach (string partition in d2cPartitions)
    {
        tasks.Add(ReceiveMessagesFromDeviceAsync(partition, cts.Token));
    }  
    Task.WaitAll(tasks.ToArray());
    ```

## <a name="create-a-device-app"></a>Creación de una aplicación de dispositivo

En esta sección, creará una aplicación de consola de .NET que simula un dispositivo que envía mensajes de dispositivo a nube a un IoT Hub.

1. En Visual Studio, agregue un proyecto de escritorio clásico de Windows de Visual C# a la solución actual mediante la plantilla de proyecto **Aplicación de consola (.NET Framework)**. Asegúrese de que la versión de .NET Framework sea 4.5.1 o una posterior. Asigne al proyecto el nombre **SimulatedDevice**.

    ![Nuevo proyecto de escritorio clásico de Windows de Visual C#][10b]

2. En el Explorador de soluciones, haga clic con el botón derecho en el proyecto **SimulatedDevice** y, a continuación, seleccione **Administrar paquetes NuGet**.

3. En la ventana **Administrador de paquetes NuGet**, seleccione **Examinar**, busque **Microsoft.Azure.Devices.Client**, haga clic en **Instalar** para instalar el paquete **Microsoft.Azure.Devices** y acepte los términos de uso. Este procedimiento permite descargar, instalar y agregar una referencia al [paquete NuGet del SDK de dispositivo de IoT de Azure][lnk-device-nuget] y sus dependencias.

4. Agregue la siguiente instrucción `using` en la parte superior del archivo **Program.cs** :

    ```csharp
    using Microsoft.Azure.Devices.Client;
    using Newtonsoft.Json;
    ```

5. Agregue los campos siguientes a la clase **Program** . Sustituya `{iot hub hostname}` por el nombre de host del centro de IoT que obtuvo en la sección "Creación de un centro de IoT". Sustituya `{device key}` por la clave de dispositivo que obtuvo en la sección "Creación de una identidad de dispositivo".

    ```csharp
    static DeviceClient deviceClient;
    static string iotHubUri = "{iot hub hostname}";
    static string deviceKey = "{device key}";
    ```

6. Agregue el método siguiente a la clase **Program** :

    ```csharp
    private static async void SendDeviceToCloudMessagesAsync()
    {
        double minTemperature = 20;
        double minHumidity = 60;
        int messageId = 1;
        Random rand = new Random();

        while (true)
        {
            double currentTemperature = minTemperature + rand.NextDouble() * 15;
            double currentHumidity = minHumidity + rand.NextDouble() * 20;

            var telemetryDataPoint = new
            {
                messageId = messageId++,
                deviceId = "myFirstDevice",
                temperature = currentTemperature,
                humidity = currentHumidity
            };
            var messageString = JsonConvert.SerializeObject(telemetryDataPoint);
            var message = new Message(Encoding.ASCII.GetBytes(messageString));
            message.Properties.Add("temperatureAlert", (currentTemperature > 30) ? "true" : "false");

            await deviceClient.SendEventAsync(message);
            Console.WriteLine("{0} > Sending message: {1}", DateTime.Now, messageString);

            await Task.Delay(1000);
        }
    }
    ```

    Este método envía un nuevo mensaje de dispositivo a nube cada segundo. El mensaje contiene un objeto JSON serializado con el identificador de dispositivo y un número generado aleatoriamente para simular un sensor de temperatura y otro de humedad.

7. Por último, agregue las líneas siguientes al método **Main** :

    ```csharp
    Console.WriteLine("Simulated device\n");
    deviceClient = DeviceClient.Create(iotHubUri, new DeviceAuthenticationWithRegistrySymmetricKey ("myFirstDevice", deviceKey), TransportType.Mqtt);
    deviceClient.ProductInfo = "HappyPath_Simulated-CSharp";
    SendDeviceToCloudMessagesAsync();
    Console.ReadLine();
    ```

    De forma predeterminada, el método **Create** en una aplicación de .NET Framework crea una instancia de **DeviceClient** que usa el protocolo AMQP para comunicarse con IoT Hub. Para usar los protocolos MQTT o HTTPS, utilice la invalidación del método **Create** que permite especificar el protocolo. Los clientes de UWP y PCL utilizan el protocolo HTTPS de forma predeterminada. Si usa el protocolo HTTPS, debe agregar también el paquete NuGet **Microsoft.AspNet.WebApi.Client** al proyecto para incluir el espacio de nombres **System.Net.Http.Formatting**.

Este tutorial le guiará por los pasos para crear una aplicación de dispositivo de IoT Hub. También puede utilizar la extensión de Visual Studio [Connected Service for Azure IoT Hub][lnk-connected-service] (Servicio conectado para Azure IoT Hub) para agregar el código necesario a la aplicación del dispositivo.

> [!NOTE]
> Por simplificar, este tutorial no implementa ninguna directiva de reintentos. En el código de producción, deberá implementar directivas de reintentos (por ejemplo, retroceso exponencial), tal y como se sugiere en el artículo de MSDN [Transient Fault Handling][lnk-transient-faults] (Tratamiento de errores temporales).

## <a name="run-the-apps"></a>Ejecución de las aplicaciones

Ya está preparado para ejecutar las aplicaciones.

1. En el Explorador de soluciones de Visual Studio, haga clic con el botón derecho en la solución y seleccione **Establecer proyectos de inicio**. Seleccione **Proyectos de inicio múltiples** y seleccione **Iniciar** como acción para los proyectos **ReadDeviceToCloudMessages** y **SimulatedDevice**.

    ![Propiedades del proyecto de inicio][41]

2. Presione **F5** para iniciar la ejecución de ambas aplicaciones. La salida de la consola de la aplicación **SimulatedDevice** muestra los mensajes que envía la aplicación de dispositivo a IoT Hub. La salida de la consola de la aplicación **ReadDeviceToCloudMessages** muestra los mensajes que recibe el Centro de IoT.

    ![Salida de la consola de aplicaciones][42]

3. El icono **Uso** de [Azure Portal][lnk-portal] muestra el número de mensajes enviados al centro de IoT:

    ![Icono Uso de Azure Portal][43]

## <a name="next-steps"></a>Pasos siguientes

En este tutorial, configuró un centro de IoT en Azure Portal y, después, creó una identidad de dispositivo en el registro de identidades del centro de IoT. Usó esta identidad de dispositivo para habilitar la aplicación del dispositivo para enviar a IoT Hub los mensajes del dispositivo a la nube. También creó otra aplicación que muestra los mensajes recibidos por el centro de IoT.

Para continuar la introducción a IoT Hub y explorar otros escenarios de IoT, consulte:

* [Conexión del dispositivo][lnk-connect-device]
* [Introducción a la administración de dispositivos][lnk-device-management]
* [Introducción a IoT Edge][lnk-iot-edge]

Para aprender a ampliar su solución IoT y cómo procesar mensajes de dispositivo a la nube a escala, consulte [Tutorial: procesamiento de mensajes de dispositivo a la nube][lnk-process-d2c-tutorial].

[!INCLUDE [iot-hub-get-started-next-steps](../../includes/iot-hub-get-started-next-steps.md)]

<!-- Images. -->
[41]: ./media/iot-hub-csharp-csharp-getstarted/run-apps1.png
[42]: ./media/iot-hub-csharp-csharp-getstarted/run-apps2.png
[43]: ./media/iot-hub-csharp-csharp-getstarted/usage.png
[10a]: ./media/iot-hub-csharp-csharp-getstarted/create-receive-csharp1.png
[10b]: ./media/iot-hub-csharp-csharp-getstarted/create-device-csharp1.png


<!-- Links -->
[lnk-process-d2c-tutorial]: iot-hub-csharp-csharp-process-d2c.md

[lnk-hub-sdks]: iot-hub-devguide-sdks.md
[lnk-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[lnk-portal]: https://portal.azure.com/

[lnk-eventhubs-tutorial]: ../event-hubs/event-hubs-csharp-ephcs-getstarted.md
[lnk-servicebus-nuget]: https://www.nuget.org/packages/WindowsAzure.ServiceBus
[lnk-event-hubs-overview]: ../event-hubs/event-hubs-overview.md

[lnk-device-nuget]: https://www.nuget.org/packages/Microsoft.Azure.Devices.Client/
[lnk-transient-faults]: https://msdn.microsoft.com/library/hh680901(v=pandp.50).aspx
[lnk-connected-service]: https://visualstudiogallery.msdn.microsoft.com/e254a3a5-d72e-488e-9bd3-8fee8e0cd1d6
[lnk-device-management]: iot-hub-node-node-device-management-get-started.md
[lnk-iot-edge]: ../iot-edge/tutorial-simulate-device-linux.md
[lnk-connect-device]: https://azure.microsoft.com/develop/iot/
