---
title: "Introducción a la administración de dispositivos de Azure IoT Hub (.NET/.NET) | Microsoft Docs"
description: "Describe cómo usar la administración de dispositivos de IoT Hub de Azure para iniciar un reinicio del dispositivo remoto. Usará el SDK de dispositivo IoT de Azure para .NET con el fin de implementar una aplicación de dispositivo simulado que incluye un método directo, además del SDK de servicio IoT de Azure para .NET con el objetivo de implementar una aplicación de servicio que invoque el método directo."
services: iot-hub
documentationcenter: .net
author: JimacoMS2
manager: timlt
editor: 
ms.assetid: 
ms.service: iot-hub
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/15/2017
ms.author: v-jamebr
ms.openlocfilehash: 3af7fbfb9740e00d9ff9c2b077cb444a8057b8c3
ms.sourcegitcommit: 0930aabc3ede63240f60c2c61baa88ac6576c508
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/07/2017
---
# <a name="get-started-with-device-management-netnet"></a>Introducción a la administración de dispositivos (.NET/.NET)

[!INCLUDE [iot-hub-selector-dm-getstarted](../../includes/iot-hub-selector-dm-getstarted.md)]

En este tutorial se muestra cómo realizar las siguientes acciones:

* Usar Azure Portal para un IoT Hub y una identidad de dispositivo en este.
* Crear una aplicación de dispositivo simulado que contiene un método directo que reinicia ese dispositivo. Los métodos directos se invocan desde la nube.
* Crear una aplicación de consola de .NET que llame a un método directo de reinicio en la aplicación de dispositivo simulado mediante su centro de IoT Hub.

Al final de este tutorial tendrá dos aplicaciones de consola de .NET:

* **SimulateManagedDevice**, que se conecta a IoT Hub con la identidad del dispositivo creada anteriormente, recibe un método directo de reinicio, simula un reinicio físico y notifica la hora del último reinicio.
* **TriggerReboot**, que llama a un método directo en la aplicación de dispositivo simulado, muestra la respuesta y muestra las propiedades notificadas actualizadas.

Para completar este tutorial, necesitará lo siguiente:

* Visual Studio 2015 o Visual Studio 2017.
* Una cuenta de Azure activa. (En caso de no tenerla, puede crear una [cuenta gratuita][lnk-free-trial] en solo unos minutos).

[!INCLUDE [iot-hub-get-started-create-hub](../../includes/iot-hub-get-started-create-hub.md)]

<a id="DeviceIdentity_csharp"></a>
[!INCLUDE [iot-hub-get-started-create-device-identity-portal](../../includes/iot-hub-get-started-create-device-identity-portal.md)]

## <a name="trigger-a-remote-reboot-on-the-device-using-a-direct-method"></a>Desencadenar un reinicio remoto en el dispositivo con un método directo
En esta sección, creará una aplicación de consola de .NET (mediante C#) que inicia una actualización remota en un dispositivo mediante un método directo. La aplicación usa las consultas gemelas de dispositivo para detectar la hora en que se reinició por última vez el dispositivo.

1. En Visual Studio, agregue un proyecto de escritorio clásico de Windows de Visual C# a una nueva solución mediante la plantilla de proyecto **Aplicación de consola (.NET Framework)**. Asegúrese de que la versión de .NET Framework sea 4.5.1 o una posterior. Asigne al proyecto el nombre **TriggerReboot**.

    ![Nuevo proyecto de escritorio clásico de Windows de Visual C#][img-createserviceapp]

2. En el Explorador de soluciones, haga clic con el botón derecho en el proyecto **TriggerReboot** y, luego, haga clic en **Administrar paquetes NuGet**.
3. En la ventana **Administrador de paquetes NuGet**, seleccione **Examinar**, busque **microsoft.azure.devices**, seleccione **Instalar** para instalar el paquete **Microsoft.Azure.Devices** y acepte los términos de uso. Este procedimiento permite descargar, instalar y agregar una referencia al [paquete NuGet del SDK de dispositivo de IoT de Azure][lnk-nuget-service-sdk] y sus dependencias.

    ![Ventana del Administrador de paquetes NuGet][img-servicenuget]
4. Agregue las siguientes instrucciones `using` al principio del archivo **Program.cs** :
   
        using Microsoft.Azure.Devices;
        using Microsoft.Azure.Devices.Shared;
        
5. Agregue los campos siguientes a la clase **Program** . Reemplace el valor de marcador de posición por la cadena de conexión de IoT Hub para la central que creó en la sección "Creación de un IoT Hub". 
   
        static RegistryManager registryManager;
        static string connString = "{iot hub connection string}";
        static ServiceClient client;
        static string targetDevice = "myDeviceId";
        
6. Agregue el método siguiente a la clase **Program**.  Este código obtiene el dispositivo gemelo para el dispositivo de reinicio y genera las propiedades notificadas.
   
        public static async Task QueryTwinRebootReported()
        {
            Twin twin = await registryManager.GetTwinAsync(targetDevice);
            Console.WriteLine(twin.Properties.Reported.ToJson());
        }
        
7. Agregue el método siguiente a la clase **Program**.  Este código inicia el reinicio en el dispositivo mediante un método directo.

        public static async Task StartReboot()
        {
            client = ServiceClient.CreateFromConnectionString(connString);
            CloudToDeviceMethod method = new CloudToDeviceMethod("reboot");
            method.ResponseTimeout = TimeSpan.FromSeconds(30);

            CloudToDeviceMethodResult result = await client.InvokeDeviceMethodAsync(targetDevice, method);

            Console.WriteLine("Invoked firmware update on device.");
        }

7. Por último, agregue las líneas siguientes al método **Main** :
   
        registryManager = RegistryManager.CreateFromConnectionString(connString);
        StartReboot().Wait();
        QueryTwinRebootReported().Wait();
        Console.WriteLine("Press ENTER to exit.");
        Console.ReadLine();
        
8. Compile la solución.

> [!NOTE]
> Este tutorial realiza solo una consulta individual de las propiedades notificadas del dispositivo. En el código de producción, se recomienda realizar un sondeo detectar cambios en las propiedades notificadas.

## <a name="create-a-simulated-device-app"></a>Creación de una aplicación de dispositivo simulado
En esta sección:

* Creará una aplicación de consola de .NET que responda a un método directo que se llama desde la nube.
* Desencadenará un reinicio del dispositivo simulado.
* Usará las propiedades notificadas para permitir consultas de dispositivo gemelo a fin de identificar los dispositivos y cuándo se reiniciaron por última vez.

1. En Visual Studio, agregue un proyecto de escritorio clásico de Windows de Visual C# a la solución actual mediante la plantilla de proyecto **Aplicación de consola** . Asigne al proyecto el nombre **SimulateManagedDevice**.
   
    ![Nueva aplicación para dispositivo de Windows clásico de Visual C#][img-createdeviceapp]
    
2. En el Explorador de soluciones, haga clic con el botón derecho en el proyecto **SimulateManagedDevice** y haga clic en **Administrar paquetes NuGet**.
3. En la ventana **Administrador de paquetes NuGet**, seleccione **Examinar** y busque **microsoft.azure.devices.client**. Seleccione **Instalar** para instalar el paquete **Microsoft.Azure.Devices.Client** y acepte las condiciones de uso. Este procedimiento permite descargar, instalar y agregar una referencia al paquete NuGet del [SDK de dispositivo Azure IoT][lnk-nuget-client-sdk] y sus dependencias.
   
    ![Ventana del Administrador de paquetes NuGet: aplicación cliente][img-clientnuget]
4. Agregue las siguientes instrucciones `using` al principio del archivo **Program.cs** :
   
        using Microsoft.Azure.Devices.Client;
        using Microsoft.Azure.Devices.Shared;

5. Agregue los campos siguientes a la clase **Program** . Sustituya el valor de marcador de posición por la cadena de conexión del dispositivo del anotó en la sección anterior.
   
        static string DeviceConnectionString = "HostName=<yourIotHubName>.azure-devices.net;DeviceId=<yourIotDeviceName>;SharedAccessKey=<yourIotDeviceAccessKey>";
        static DeviceClient Client = null;

6. Agregue lo siguiente para implementar el método directo en el dispositivo:

        static Task<MethodResponse> onReboot(MethodRequest methodRequest, object userContext)
        {
            // In a production device, you would trigger a reboot scheduled to start after this method returns
            // For this sample, we simulate the reboot by writing to the console and updating the reported properties 
            try
            {
                Console.WriteLine("Rebooting!");

                // Update device twin with reboot time. 
                TwinCollection reportedProperties, reboot, lastReboot;
                lastReboot = new TwinCollection();
                reboot = new TwinCollection();
                reportedProperties = new TwinCollection();
                lastReboot["lastReboot"] = DateTime.Now;
                reboot["reboot"] = lastReboot;
                reportedProperties["iothubDM"] = reboot;
                Client.UpdateReportedPropertiesAsync(reportedProperties).Wait();
            }
            catch (Exception ex)
            {
                Console.WriteLine();
                Console.WriteLine("Error in sample: {0}", ex.Message);
            }

            string result = "'Reboot started.'";
            return Task.FromResult(new MethodResponse(Encoding.UTF8.GetBytes(result), 200));
        }

7. Finalmente, agregue el código siguiente al método **Main** para abrir la conexión a su IoT Hub e inicializar la escucha del método directo:
   
        try
        {
            Console.WriteLine("Connecting to hub");
            Client = DeviceClient.CreateFromConnectionString(DeviceConnectionString, TransportType.Mqtt);

            // setup callback for "reboot" method
            Client.SetMethodHandlerAsync("reboot", onReboot, null).Wait();
            Console.WriteLine("Waiting for reboot method\n Press enter to exit.");
            Console.ReadLine();

            Console.WriteLine("Exiting...");

            // as a good practice, remove the "reboot" handler
            Client.SetMethodHandlerAsync("reboot", null, null).Wait();
            Client.CloseAsync().Wait();
        }
        catch (Exception ex)
        {
            Console.WriteLine();
            Console.WriteLine("Error in sample: {0}", ex.Message);
        }
        
8. En el Explorador de soluciones de Visual Studio, haga clic con el botón derecho en la solución y seleccione **Establecer proyectos de inicio...**. Seleccione **Proyecto de inicio único** y luego seleccione el proyecto **SimulateManagedDevice** en el menú desplegable. Compile la solución.       

> [!NOTE]
> Por simplificar, este tutorial no implementa ninguna directiva de reintentos. En el código de producción, deberá implementar directivas de reintentos (por ejemplo, retroceso exponencial), tal y como se sugiere en el artículo de MSDN [Transient Fault Handling][lnk-transient-faults] (Tratamiento de errores temporales).


## <a name="run-the-apps"></a>Ejecución de las aplicaciones
Ya está preparado para ejecutar las aplicaciones.
1. Ejecute la aplicación de dispositivos .NET **SimulateManagedDevice**.  Haga clic con el botón derecho en el proyecto **SimulateManagedDevice**, seleccione **Depurar** y luego seleccione **Iniciar nueva instancia**. Debería empezar a escuchar llamadas de método desde IoT Hub: 

2. Una vez que el dispositivo está conectado y esperando invocaciones del método, ejecute la aplicación **TriggerReboot** de .NET para invocar el método de reinicio en la aplicación de dispositivo simulado. Haga clic en el proyecto **TriggerReboot**, seleccione **Depurar** y luego seleccione **Iniciar nueva instancia**. Debería ver el mensaje "Reiniciando" escrito en la consola de **SimulatedManagedDevice** y las propiedades notificadas del dispositivo, que incluyen la hora en que se reinició por última vez, escritas en la consola de **TriggerReboot**.
   
    ![Ejecución de servicio y de aplicación de dispositivo][img-combinedrun]

[!INCLUDE [iot-hub-dm-followup](../../includes/iot-hub-dm-followup.md)]

<!-- images and links -->
[img-output]: media/iot-hub-get-started-with-dm/image6.png
[img-dm-ui]: media/iot-hub-get-started-with-dm/dmui.png
[img-servicenuget]: media/iot-hub-csharp-csharp-device-management-get-started/servicesdknuget.png
[img-createserviceapp]: media/iot-hub-csharp-csharp-device-management-get-started/createserviceapp.png
[img-clientnuget]: media/iot-hub-csharp-csharp-device-management-get-started/clientsdknuget.png
[img-createdeviceapp]: media/iot-hub-csharp-csharp-device-management-get-started/createdeviceapp.png
[img-combinedrun]: media/iot-hub-csharp-csharp-device-management-get-started/combinedrun.png

[lnk-dev-setup]: https://github.com/Azure/azure-iot-sdk-node/blob/master/doc/node-devbox-setup.md

[lnk-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[Azure portal]: https://portal.azure.com/
[Using resource groups to manage your Azure resources]: ../azure-portal/resource-group-portal.md
[lnk-dm-github]: https://github.com/Azure/azure-iot-device-management

[lnk-devtwin]: iot-hub-devguide-device-twins.md
[lnk-c2dmethod]: iot-hub-devguide-direct-methods.md
[lnk-transient-faults]: https://msdn.microsoft.com/library/hh680901(v=pandp.50).aspx
[lnk-nuget-client-sdk]: https://www.nuget.org/packages/Microsoft.Azure.Devices.Client/
[lnk-nuget-service-sdk]: https://www.nuget.org/packages/Microsoft.Azure.Devices/
