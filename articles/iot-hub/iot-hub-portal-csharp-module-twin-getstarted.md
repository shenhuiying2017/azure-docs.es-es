---
title: Introducción a la identidad de módulo y a los módulos gemelos de Azure IoT Hub (portal y .NET) | Microsoft Docs
description: Aprenda a crear la identidad de módulo y a actualizar módulos gemelos mediante el portal y .NET.
services: iot-hub
documentationcenter: .net
author: chrissie926
manager: timlt
editor: ''
ms.assetid: f40604ff-8fd6-4969-9e99-8574fbcf036c
ms.service: iot-hub
ms.devlang: dotnet
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/26/2018
ms.author: dobett
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 99b0acbf6461750c2606cc3d4338c10b03a4b430
ms.sourcegitcommit: ca05dd10784c0651da12c4d58fb9ad40fdcd9b10
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/03/2018
---
# <a name="get-started-with-iot-hub-module-identity-and-module-twin-using-the-portal-and-net-device"></a>Introducción a la identidad de módulo y los módulos gemelos de IoT Hub con el portal y un dispositivo .NET

> [!NOTE]
> [Las identidades de módulo y los módulos gemelos](iot-hub-devguide-module-twins.md) son similares a la identidad de dispositivo y el dispositivo gemelo de Azure IoT Hub, pero ofrecen granularidad más fina. Aunque la identidad de módulo y los dispositivos gemelos de Azure IoT Hub permiten que la aplicación back-end configure un dispositivo y proporcionan visibilidad para las condiciones del dispositivo, la identidad de módulo y los módulos gemelos proporcionan estas funcionalidades para los componentes individuales del dispositivo. En los dispositivos compatibles con varios componentes, como dispositivos con sistema operativo o firmware, permiten la configuración individual y condiciones específicas por componente.

En este tutorial, aprenderá a: 
1. Crear una identidad de módulo en el portal. 
2. Usar el SDK del dispositivo .NET para actualizar el módulo gemelo desde el dispositivo.

> [!NOTE]
> Para más información acerca de los diversos SDK que puede usar para compilar las aplicaciones para que se ejecuten en los dispositivos y en el back-end de la solución, consulte [SDK de Azure IoT Hub][lnk-hub-sdks].

Para completar este tutorial, necesitará lo siguiente:

* Visual Studio 2015 o Visual Studio 2017.
* Una cuenta de Azure activa. (En caso de no tener ninguna, puede crear una [cuenta gratuita][lnk-free-trial] en tan solo unos minutos).

[!INCLUDE [iot-hub-get-started-create-hub](../../includes/iot-hub-get-started-create-hub.md)]

## <a name="create-a-device-identity-in-the-portal"></a>Creación de la identidad del dispositivo en el portal

Ahora dispone de una instancia de IoT Hub. Inicie sesión en el [portal](https://portal.azure.com) y vaya a IoT Hub. Haga clic Dispositivos IoT y en Agregar para crear la identidad del dispositivo. Asígnele el nombre **MyFirstDevice**. 

![Creación de la identidad del dispositivo][8]

Después de guardar, en la lista de identidades de dispositivo, verá que la identidad MyFirstDevice creó correctamente.

![Identificador de dispositivo creado][11]

Haga clic ahora en la fila. Verá los detalles del dispositivo.

![Detalles del dispositivo][10]

## <a name="create-a-module-identity-in-the-portal"></a>Creación de una identidad de módulo en el portal

En una identidad del dispositivo se pueden crear hasta 20 identidades de módulo. Haga clic en el botón **Add Module Identity** (Agregar identidad de módulo) de la parte superior para crear la primera identidad de módulo, denominada **myFirstModule**. 

![Detalles del dispositivo][9]

Guárdela y haga clic en la identidad de módulo que acaba de crear. Verá los detalles de la identidad de módulo. Copie la cadena de conexión: clave principal. Se utilizará en la siguiente sección para configurar el módulo en el dispositivo.

![Detalles del dispositivo][12]

<a id="D2C_csharp"></a>
## <a name="update-the-module-twin-using-net-device-sdk"></a>Actualización del módulo gemelo con el SDK de dispositivo .NET

Ha creado correctamente la identidad del módulo en la instancia de IoT Hub. Probemos la comunicación con la nube desde el dispositivo simulado. Al crearse una identidad de módulo, se crea también un módulo gemelo en IoT Hub de manera implícita. En esta sección creará una aplicación de consola .NET en el dispositivo simulado que actualiza las propiedades notificadas del módulo gemelo.

1. **Cree un proyecto de Visual Studio**: en Visual Studio, agregue un proyecto de escritorio clásico de Windows de Visual C# a la solución existente mediante la plantilla de proyecto **Aplicación de consola (.NET Framework)**. Asegúrese de que la versión de .NET Framework sea 4.6.1 o una posterior. Denomine el proyecto **UpdateModuleTwinReportedProperties**.

    ![Creación de un proyecto de Visual Studio][13]

2. **Instale el SDK de dispositivo .NET de Azure IoT Hub V1.16.0-preview-005**: la identidad de módulo y el módulo gemelo se encuentran en versión preliminar pública. Solo está disponible en los SDK de dispositivo de la versión preliminar de IoT Hub. En Visual Studio, abra Herramientas > Administrador de paquetes NuGet > Administrar paquetes NuGet para la solución. Busque Microsoft.Azure.Devices.Client. Asegúrese de que ha marcado la casilla de inclusión de la versión preliminar. Seleccione la versión V1.16.0-preview-005 e instálela. Ahora tendrá acceso a todas las características del módulo. 

    ![Instalación del SDK de servicio .NET de Azure IoT Hub V1.16.0-preview-005][14]

3. **Obtenga la cadena de conexión del módulo**: para ello, inicie sesión en [Azure Portal][lnk-portal]. Vaya a la instancia de IoT Hub y haga clic en Dispositivos IoT. Busque myFirstDevice, ábralo y verá que myFirstModule se creó correctamente. Copie la cadena de conexión del módulo, la necesitará en el paso siguiente.

    ![Detalles del módulo en Azure Portal][15]

4. **Cree la aplicación de consola UpdateModuleTwinReportedProperties**: agregue las siguientes instrucciones `using` a la parte superior del archivo **Program.cs**:

    ```csharp
    using Microsoft.Azure.Devices.Client;
    using Microsoft.Azure.Devices.Shared;
    ```

    Agregue los campos siguientes a la clase **Program** . Reemplace el valor del marcador de posición por la cadena de conexión del módulo.

    ```csharp
    private const string ModuleConnectionString = "<Your module connection string>“;
    private static DeviceClient Client = null;
    ```

    Agregue el método **OnDesiredPropertyChanged** siguiente a la clase **Program**:

    ```csharp
    private static async Task OnDesiredPropertyChanged(TwinCollection desiredProperties, object userContext)
        {
            Console.WriteLine("desired property change:");
            Console.WriteLine(JsonConvert.SerializeObject(desiredProperties));
            Console.WriteLine("Sending current time as reported property");
            TwinCollection reportedProperties = new TwinCollection
            {
                ["DateTimeLastDesiredPropertyChangeReceived"] = DateTime.Now
            };

            await Client.UpdateReportedPropertiesAsync(reportedProperties).ConfigureAwait(false);
        }
    ```

    Por último, agregue las líneas siguientes al método **Main** :

    ```csharp
    static void Main(string[] args)
    {
        Microsoft.Azure.Devices.Client.TransportType transport = Microsoft.Azure.Devices.Client.TransportType.Amqp;

        try
        {
            Client = DeviceClient.CreateFromConnectionString(ModuleConnectionString, transport);
            Client.SetConnectionStatusChangesHandler(ConnectionStatusChangeHandler);
            Client.SetDesiredPropertyUpdateCallbackAsync(OnDesiredPropertyChanged, null).Wait();

            Console.WriteLine("Retrieving twin");
            var twinTask = Client.GetTwinAsync();
            twinTask.Wait();
            var twin = twinTask.Result;
            Console.WriteLine(JsonConvert.SerializeObject(twin));

            Console.WriteLine("Sending app start time as reported property");
            TwinCollection reportedProperties = new TwinCollection();
            reportedProperties["DateTimeLastAppLaunch"] = DateTime.Now;

            Client.UpdateReportedPropertiesAsync(reportedProperties);
        }
        catch (AggregateException ex)
        {
            Console.WriteLine("Error in sample: {0}", ex);
        }

        Console.WriteLine("Waiting for Events.  Press enter to exit...");
        Client.CloseAsync().Wait();
    }
    ```

    Este ejemplo de código muestra cómo recuperar el módulo gemelo y actualizar las propiedades notificadas con el protocolo AMQP. En la versión preliminar pública solo se admite AMQP para las operaciones de módulos gemelos.
    ```

## Run the apps

You are now ready to run the apps. In Visual Studio, in Solution Explorer, right-click your solution, and then click **Set StartUp projects**. Select **Multiple startup projects**, and then select **Start** as the action for the console app. And then press F5 to start both apps running. 

## Next steps

To continue getting started with IoT Hub and to explore other IoT scenarios, see:

* [Get started with IoT Hub module identity and module twin using .NET backup and .NET device][lnk-csharp-csharp-getstarted]
* [Getting started with IoT Edge][lnk-iot-edge]


<!-- Images. -->
[8]:./media\iot-hub-portal-csharp-module-twin-getstarted/create-device-id.JPG
[9]:./media\iot-hub-portal-csharp-module-twin-getstarted/create-module-id.JPG
[10]:./media\iot-hub-portal-csharp-module-twin-getstarted/device-details.JPG
[11]:./media\iot-hub-portal-csharp-module-twin-getstarted/device-id-created.JPG
[12]:./media\iot-hub-portal-csharp-module-twin-getstarted/module-details.JPG
[13]: ./media\iot-hub-csharp-csharp-module-twin-getstarted/update-twins-csharp1.JPG
[14]: ./media\iot-hub-csharp-csharp-module-twin-getstarted/install-sdk.png
[15]: ./media\iot-hub-csharp-csharp-module-twin-getstarted/module-detail.JPG
<!-- Links -->
[lnk-hub-sdks]: iot-hub-devguide-sdks.md
[lnk-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[lnk-portal]: https://portal.azure.com/

[lnk-csharp-csharp-getstarted]: iot-hub-csharp-csharp-module-twin-getstarted.md
[lnk-iot-edge]: ../iot-edge/tutorial-simulate-device-linux.md
