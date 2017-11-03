---
title: "Actualización del firmware de los dispositivos con Azure IoT Hub | Microsoft Docs"
description: "Describe cómo usar la administración de dispositivos en IoT Hub de Azure para iniciar una actualización de firmware del dispositivo. El SDK de dispositivo IoT de Azure para .NET se usa para implementar una aplicación de dispositivo simulada, mientras que el SDK de servicio IoT de Azure para .NET se usa para implementar una aplicación de servicio que desencadena la actualización del firmware."
services: iot-hub
documentationcenter: .net
author: JimacoMS2
manager: timlt
editor: 
ms.assetid: 
ms.service: iot-hub
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/19/2017
ms.author: v-jamebr
ms.openlocfilehash: bd0a227861d75dc66af8fb4865a17a3b6d0f70ba
ms.sourcegitcommit: 2d1153d625a7318d7b12a6493f5a2122a16052e0
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/20/2017
---
# <a name="use-device-management-to-initiate-a-device-firmware-update-netnet"></a>Uso de la administración de dispositivos para iniciar una actualización de firmware de los dispositivos (. NET/.NET)
[!INCLUDE [iot-hub-selector-firmware-update](../../includes/iot-hub-selector-firmware-update.md)]

## <a name="introduction"></a>Introducción
En el tutorial de [Introducción a la administración de dispositivos][lnk-dm-getstarted], vimos cómo usar los primitivos de [dispositivo gemelo][lnk-devtwin] y [métodos directos][lnk-c2dmethod] para reiniciar de forma remota un dispositivo. Este tutorial emplea los mismos tipos primitivos de IoT Hub. Además, proporciona orientación y muestra cómo realizar una actualización de firmware simulada de extremo a extremo.  Este patrón se usa en la implementación de actualizaciones de firmware del [ejemplo de implementación de dispositivos Raspberry Pi][lnk-rpi-implementation].

En este tutorial se muestra cómo realizar las siguientes acciones:

* Crear una aplicación de consola de .NET que llame al método directo **firmwareUpdate** en la aplicación de dispositivo simulado a través de su IoT Hub.
* Cree una aplicación de dispositivo simulado que implemente un método directo **firmwareUpdate**. Este método inicia un proceso de varias fases que espera para descargar la imagen de firmware, la descarga y, por último, la aplica. Durante cada fase de la actualización, el dispositivo usa las propiedades notificadas para informar sobre el progreso.

Al final de este tutorial, tendrá una aplicación de dispositivo de consola de .NET (C#) y una aplicación back-end de consola .NET (C#):

* **SimulatedDeviceFwUpdate**, que se conecta a su instancia de IoT Hub con la identidad del dispositivo creada anteriormente, recibe el método directo **firmwareUpdate** y se ejecuta a través de un proceso de varios estados para simular una actualización del firmware, lo que incluye: la espera para la descarga de la imagen, la descarga de la nueva imagen y, finalmente, la aplicación de esta imagen.

* **TriggerFWUpdate**, que usa el SDK del servicio para invocar de forma remota al método directo **firmwareUpdate** en el dispositivo simulado, muestra la respuesta y muestra periódicamente (cada 500 ms) las propiedades notificadas actualizadas.

Para completar este tutorial, necesitará lo siguiente:

* Visual Studio 2015 o Visual Studio 2017.
* Una cuenta de Azure activa. (En caso de no tenerla, puede crear una [cuenta gratuita][lnk-free-trial] en solo unos minutos).

Siga los pasos del artículo de [introducción a la administración de dispositivos](iot-hub-csharp-csharp-device-management-get-started.md) para crear su instancia de IoT Hub y obtener la cadena de conexión de IoT Hub.

[!INCLUDE [iot-hub-get-started-create-hub](../../includes/iot-hub-get-started-create-hub.md)]

[!INCLUDE [iot-hub-get-started-create-device-identity-portal](../../includes/iot-hub-get-started-create-device-identity-portal.md)]

## <a name="trigger-a-remote-firmware-update-on-the-device-using-a-direct-method"></a>Desencadenamiento de una actualización de firmware remota en el dispositivo con un método directo
En esta sección, creará una aplicación de consola de .NET (mediante C#) que inicia una actualización de firmware remota en un dispositivo. La aplicación usa un método directo para iniciar la actualización y utiliza consultas de dispositivos gemelos para obtener periódicamente el estado de la actualización del firmware activa.

1. En Visual Studio, agregue un proyecto de escritorio clásico de Windows de Visual C# a la solución actual mediante la plantilla de proyecto **Aplicación de consola**. Asigne al proyecto el nombre **TriggerFWUpdate**.

    ![Nuevo proyecto de escritorio clásico de Windows de Visual C#][img-createserviceapp]

2. En el Explorador de soluciones, haga clic con el botón derecho en el proyecto **TriggerFWUpdate** y luego seleccione **Administrar paquetes NuGet**.
3. En la ventana **Administrador de paquetes NuGet**, seleccione **Examinar**, busque **microsoft.azure.devices**, seleccione **Instalar** para instalar el paquete **Microsoft.Azure.Devices** y acepte los términos de uso. Este procedimiento permite descargar, instalar y agregar una referencia al [paquete NuGet del SDK de dispositivo de IoT de Azure][lnk-nuget-service-sdk] y sus dependencias.

    ![Ventana del Administrador de paquetes NuGet][img-servicenuget]
4. Agregue las siguientes instrucciones `using` al principio del archivo **Program.cs**:

    ```csharp   
    using Microsoft.Azure.Devices;
    using Microsoft.Azure.Devices.Shared;
    ```

5. Agregue los campos siguientes a la clase **Program**. Reemplace los valores de varios marcadores de posición por la cadena de conexión de IoT Hub que creó en la sección anterior y por el identificador del dispositivo.
   
    ```csharp   
    static RegistryManager registryManager;
    static string connString = "{iot hub connection string}";
    static ServiceClient client;
    static string targetDevice = "{deviceIdForTargetDevice}";
    ```
        
6. Agregue el método siguiente a la clase **Program**. Este método sondea el dispositivo gemelo para ver si el estado se ha actualizado cada 500 milisegundos. Solo escribe en la consola cuando el estado ha cambiado. En este ejemplo, para evitar el consumo de mensajes de IoT Hub adicionales en su suscripción, el sondeo se detiene cuando el dispositivo indica que el estado es **applyComplete** o un error.  
   
    ```csharp   
    public static async Task QueryTwinFWUpdateReported(DateTime startTime)
    {
        DateTime lastUpdated = startTime;

        while (true)
        {
            Twin twin = await registryManager.GetTwinAsync(targetDevice);

            if (twin.Properties.Reported.GetLastUpdated().ToUniversalTime() > lastUpdated.ToUniversalTime())
            {
                lastUpdated = twin.Properties.Reported.GetLastUpdated().ToUniversalTime();
                Console.WriteLine("\n" + twin.Properties.Reported["iothubDM"].ToJson());

                var status = twin.Properties.Reported["iothubDM"]["firmwareUpdate"]["status"].Value;
                if ((status == "downloadFailed") || (status == "applyFailed") || (status == "applyComplete"))
                {
                    Console.WriteLine("\nStop polling.");
                    return;
                }
            }
            await Task.Delay(500);
        }
    }
    ```
        
7. Agregue el método siguiente a la clase **Program**:

    ```csharp   
    public static async Task StartFirmwareUpdate()
    {
        client = ServiceClient.CreateFromConnectionString(connString);
        CloudToDeviceMethod method = new CloudToDeviceMethod("firmwareUpdate");
        method.ResponseTimeout = TimeSpan.FromSeconds(30);
        method.SetPayloadJson(
            @"{
               fwPackageUri : 'https://someurl'
            }");

        CloudToDeviceMethodResult result = await client.InvokeDeviceMethodAsync(targetDevice, method);

        Console.WriteLine("Invoked firmware update on device.");
    }
    ```

8. Finalmente, agregue las líneas siguientes al método **Main**. Esto crea un administrador del registro con el que se lee el dispositivo gemelo, que inicia la tarea de sondeo en un subproceso de trabajo y que, después, desencadena la actualización del firmware.
   
    ```csharp   
    registryManager = RegistryManager.CreateFromConnectionString(connString);

    Task queryTask = Task.Run(() => (QueryTwinFWUpdateReported(DateTime.Now)));

    StartFirmwareUpdate().Wait();
    Console.WriteLine("Press ENTER to exit.");
    Console.ReadLine();
    ```
        
9. Compile la solución.

## <a name="create-a-simulated-device-app"></a>Creación de una aplicación de dispositivo simulado
En esta sección:

* Creará una aplicación de consola de .NET que responda a un método directo que se llama desde la nube.
* Simulará una actualización de firmware desencadenada por un servicio back-end a través de un método directo
* Usará las propiedades notificadas para habilitar consultas de dispositivos gemelos a fin de identificar los dispositivos y cuándo se actualizó su firmware por última vez.

1. En Visual Studio, agregue un proyecto de escritorio clásico de Windows de Visual C# a la solución actual mediante la plantilla de proyecto **Aplicación de consola**. Asigne al proyecto el nombre **SimulateDeviceFWUpdate**.
   
    ![Nueva aplicación para dispositivo de Windows clásico de Visual C#][img-createdeviceapp]
    
2. En el Explorador de soluciones, haga clic con el botón derecho en el proyecto **SimulateDeviceFWUpdate** y luego seleccione **Administrar paquetes NuGet**.
3. En la ventana **Administrador de paquetes NuGet**, seleccione **Examinar** y busque **microsoft.azure.devices.client**. Seleccione **Instalar** para instalar el paquete **Microsoft.Azure.Devices.Client** y acepte las condiciones de uso. Este procedimiento permite descargar, instalar y agregar una referencia al paquete NuGet del [SDK de dispositivo Azure IoT][lnk-nuget-client-sdk] y sus dependencias.
   
    ![Ventana del Administrador de paquetes NuGet: aplicación cliente][img-clientnuget]
4. Agregue las siguientes instrucciones `using` al principio del archivo **Program.cs**:
   
    ```csharp   
    using Newtonsoft.Json.Linq;
    using Microsoft.Azure.Devices.Client;
    using Microsoft.Azure.Devices.Shared;
    ```

5. Agregue los campos siguientes a la clase **Program**. Sustituya el valor del marcador de posición por la cadena de conexión del dispositivo que anotó en la sección **Creación de una identidad de dispositivo**.
   
    ```csharp   
    static string DeviceConnectionString = "HostName=<yourIotHubName>.azure-devices.net;DeviceId=<yourIotDeviceName>;SharedAccessKey=<yourIotDeviceAccessKey>";
    static DeviceClient Client = null;
    ```

6. Agregue el siguiente método para informar del estado a la nube a través del dispositivo gemelo: 

    ```csharp   
    static async Task reportFwUpdateThroughTwin(Twin twin, TwinCollection fwUpdateValue)
    {
        try
        {
            TwinCollection patch = new TwinCollection();
            TwinCollection iothubDM = new TwinCollection();

            iothubDM["firmwareUpdate"] = fwUpdateValue;
            patch["iothubDM"] = iothubDM;

            await Client.UpdateReportedPropertiesAsync(patch);
            Console.WriteLine("Twin state reported: {0}", fwUpdateValue["status"]);
        }
        catch 
        {
            Console.WriteLine("Error updating device twin");
            throw;
        }
    }
    ```

7. Agregue el siguiente método para simular la descarga de la imagen de firmware:
        
    ```csharp   
    static async Task<byte[]> simulateDownloadImage(string imageUrl)
    {
        var image = "[fake image data]";

        Console.WriteLine("Downloading image from " + imageUrl);

        await Task.Delay(4000);

        return Encoding.ASCII.GetBytes(image);
            
    }
    ```

8. Agregue el siguiente método para simular la aplicación de la imagen de firmware en el dispositivo:
        
    ```csharp   
    static async Task simulateApplyImage(byte[] imageData)
    {
        if (imageData == null)
        {
            throw new ArgumentNullException();
        }

        await Task.Delay(4000);

    }
    ```
 
9.  Agregue el siguiente método para simular la espera para descargar la imagen de firmware. Actualice el estado a **en espera** y borre las restantes propiedades de la actualización del firmware en el dispositivo gemelo. Estas propiedades se borran para eliminar todos los valores existentes de las actualizaciones de firmware anteriores. Esto es necesario porque las propiedades notificadas se envían como una operación PATCH (delta), no como operación PUT (un conjunto completo de propiedades que se reemplaza todos los valores anteriores). Normalmente, los dispositivos reciben una notificación sobre una actualización disponible y una directiva definida por un administrador provoca que el dispositivo empiece a descargar y aplicar la actualización. Esta función es donde se debe ejecutar la lógica para habilitar esa directiva. 
        
    ```csharp   
    static async Task waitToDownload(Twin twin, string fwUpdateUri)
    {
        var now = DateTime.Now;
        TwinCollection status = new TwinCollection();
        status["fwPackageUri"] = fwUpdateUri;
        status["status"] = "waiting";
        status["error"] = null;
        status["startedWaitingTime"] = DateTime.Now;
        status["downloadCompleteTime"] = null;
        status["startedApplyingImage"] = null;
        status["lastFirmwareUpdate"] = null;

        await reportFwUpdateThroughTwin(twin, status);

        await Task.Delay(2000);
    }
    ```

10. Agregue el siguiente método para realizar la descarga. Actualiza el estado a **en descarga** a través del dispositivo gemelo, llama al método de descarga simulado y notifica los estados **downloadComplete** o **downloadFailed** a través del dispositivo gemelo en función de los resultados de la operación de descarga. 
        
    ```csharp   
    static async Task<byte[]> downloadImage(Twin twin, string fwUpdateUri)
    {
        try
        {
            TwinCollection statusUpdate = new TwinCollection();
            statusUpdate["status"] = "downloading";
            await reportFwUpdateThroughTwin(twin, statusUpdate);

            byte[] imageData = await simulateDownloadImage(fwUpdateUri);

            statusUpdate = new TwinCollection();
            statusUpdate["status"] = "downloadComplete";
            statusUpdate["downloadCompleteTime"] = DateTime.Now;
            await reportFwUpdateThroughTwin(twin, statusUpdate);
            return imageData;
        }
        catch (Exception ex)
        {
            TwinCollection statusUpdate = new TwinCollection();
            statusUpdate["status"] = "downloadFailed";
            statusUpdate["error"] = new TwinCollection();
            statusUpdate["error"]["code"] = ex.GetType().ToString();
            statusUpdate["error"]["message"] = ex.Message;
            await reportFwUpdateThroughTwin(twin, statusUpdate);
            throw;
        }
    }
    ```

11. Agregue el siguiente método para aplicar la imagen. Actualiza el estado a **aplicando** a través del dispositivo gemelo, llama al método de imagen de aplicación simulado y actualiza el estado a **applyComplete** o **applyComplete** a través del dispositivo gemelo en función de los resultados de la operación de aplicación. 
        
    ```csharp   
    static async Task applyImage(Twin twin, byte[] imageData)
    {
        try
        {
            TwinCollection statusUpdate = new TwinCollection();
            statusUpdate["status"] = "applying";
            statusUpdate["startedApplyingImage"] = DateTime.Now;
            await reportFwUpdateThroughTwin(twin, statusUpdate);

            await simulateApplyImage(imageData);

            statusUpdate = new TwinCollection();
            statusUpdate["status"] = "applyComplete";
            statusUpdate["lastFirmwareUpdate"] = DateTime.Now;
            await reportFwUpdateThroughTwin(twin, statusUpdate);
        }
        catch (Exception ex)
        {
            TwinCollection statusUpdate = new TwinCollection();
            statusUpdate["status"] = "applyFailed";
            statusUpdate["error"] = new TwinCollection();
            statusUpdate["error"]["code"] = ex.GetType().ToString();
            statusUpdate["error"]["message"] = ex.Message;
            await reportFwUpdateThroughTwin(twin, statusUpdate);
            throw;
        }
    }
    ```

12. Agregue el siguiente método para secuenciar la operación de actualización de firmware desde en espera para descargar la imagen a través de aplicar la imagen en el dispositivo:
        
    ```csharp   
    static async Task doUpdate(string fwUpdateUrl)
    {
        try
        {
            Twin twin = await Client.GetTwinAsync();
            await waitToDownload(twin, fwUpdateUrl);
            byte[] imageData = await downloadImage(twin, fwUpdateUrl);
            await applyImage(twin, imageData);
        }
        catch (Exception ex)
        {
            Console.WriteLine();
            Console.WriteLine("Error during update: {0}", ex.Message);
        }
    }
    ```

13. Agregue el siguiente método para controlar el método directo **updateFirmware** de la nube. Extrae la dirección URL para la actualización del firmware de la carga del mensaje y la pasa a la tarea **doUpdate**, que inicia en otro subproceso del grupo de subprocesos. A continuación, devuelve inmediatamente la respuesta del método a la nube.
        
    ```csharp   
    static Task<MethodResponse> onFirmwareUpdate(MethodRequest methodRequest, object userContext)
    {
        string fwUpdateUrl = (string)JObject.Parse(methodRequest.DataAsJson)["fwPackageUri"];
        Console.WriteLine("\nMethod: {0} triggered by service, URI is: {1}", methodRequest.Name, fwUpdateUrl);

        Task updateTask = Task.Run(() => (doUpdate(fwUpdateUrl)));

        string result = "'FirmwareUpdate started.'";
        return Task.FromResult(new MethodResponse(Encoding.UTF8.GetBytes(result), 200));
    }
    ```
> [!NOTE]
> Este método desencadena la actualización simulada para que se ejecute como una **Tarea** y, después, responde inmediatamente a la llamada del método e informa al servicio de que se ha iniciado la actualización del firmware. Tanto el estado de la actualización como su finalización se enviarán al servicio mediante las propiedades notificadas del dispositivo gemelo. La respuesta a la llamada del método se realiza al iniciar la actualización, en lugar de después de su finalización, porque:
> * Es muy probable que un proceso de actualización real tarde más que el tiempo de expiración de la llamada al método.
> * Es muy probable que un proceso de actualización requiera un reinicio, que volvería a iniciar esta aplicación, lo que haría que el objeto **MetodRequest** no estuviera disponible. (sin embargo, las propiedades notificadas se pueden actualizar, incluso tras un reinicio). 

14. Finalmente, agregue el código siguiente al método **Main** para abrir la conexión a su IoT Hub e inicializar la escucha del método directo:
   
    ```csharp   
    try
    {
        Console.WriteLine("Connecting to hub");
        Client = DeviceClient.CreateFromConnectionString(DeviceConnectionString, TransportType.Mqtt);
        
        // setup callback for "firmware update" method
        Client.SetMethodHandlerAsync("firmwareUpdate", onFirmwareUpdate, null).Wait();
        Console.WriteLine("Waiting for firmware update direct method call\n Press enter to exit.");
        Console.ReadLine();

        Console.WriteLine("Exiting...");

        // as a good practice, remove the firmware update handler
        Client.SetMethodHandlerAsync("firmwareUpdate", null, null).Wait();
        Client.CloseAsync().Wait();
    }
    catch (Exception ex)
    {
        Console.WriteLine();
        Console.WriteLine("Error in sample: {0}", ex.Message);
    }
    ```
        
15. Compile la solución.       

> [!NOTE]
> Por simplificar, este tutorial no implementa ninguna directiva de reintentos. En el código de producción, deberá implementar directivas de reintentos (por ejemplo, retroceso exponencial), tal y como se sugiere en el artículo de MSDN [Transient Fault Handling][lnk-transient-faults] (Tratamiento de errores temporales).


## <a name="run-the-apps"></a>Ejecución de las aplicaciones
Ya está preparado para ejecutar las aplicaciones.
1. Ejecute la aplicación de dispositivos .NET **SimulatedDeviceFWUpdate**.  Haga clic con el botón derecho en el proyecto **SimulatedDeviceFWUpdate**, seleccione **Depurar** y luego seleccione **Iniciar nueva instancia**. Debería empezar a escuchar llamadas de método desde IoT Hub: 

2. Una vez que el dispositivo está conectado y esperando invocaciones del método, ejecute la aplicación **TriggerFWUpdate** de .NET para invocar el método de actualización del firmware en la aplicación de dispositivo simulado. Haga clic con el botón derecho en el proyecto **TriggerReboot**, seleccione **Depurar** y luego seleccione **Iniciar nueva instancia**. Debería ver la secuencia actualizada escrita en la consola **SimulatedDeviceFWUpdate**, así como la secuencia notificada a través de las propiedades del dispositivo en la consola **TriggerFWUpdate**. Tenga en cuenta que el proceso tarda varios segundos en completarse. 
   
    ![Ejecución de servicio y de aplicación de dispositivo][img-combinedrun]


## <a name="next-steps"></a>Pasos siguientes
En este tutorial, ha usado un método directo para desencadenar una actualización de firmware remota en un dispositivo y ha utilizado las propiedades notificadas para entender el progreso del proceso de actualización del firmware.

Para información sobre cómo ampliar la solución de IoT y programar llamadas de método en varios dispositivos, consulte el tutorial [Programación de trabajos en varios dispositivos][lnk-tutorial-jobs].

<!-- images -->
[img-servicenuget]: media/iot-hub-csharp-csharp-firmware-update/servicesdknuget.png
[img-clientnuget]: media/iot-hub-csharp-csharp-firmware-update/clientsdknuget.png
[img-createserviceapp]: media/iot-hub-csharp-csharp-firmware-update/createserviceapp.png
[img-createdeviceapp]: media/iot-hub-csharp-csharp-firmware-update/createdeviceapp.png
[img-combinedrun]: media/iot-hub-csharp-csharp-firmware-update/combinedrun.png

[lnk-devtwin]: iot-hub-devguide-device-twins.md
[lnk-c2dmethod]: iot-hub-devguide-direct-methods.md
[lnk-dm-getstarted]: iot-hub-csharp-csharp-device-management-get-started.md
[lnk-tutorial-jobs]: iot-hub-csharp-node-schedule-jobs.md

[lnk-dev-setup]: https://github.com/Azure/azure-iot-sdk-node/blob/master/doc/node-devbox-setup.md
[lnk-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[lnk-transient-faults]: https://msdn.microsoft.com/library/hh680901(v=pandp.50).aspx
[lnk-rpi-implementation]: https://github.com/Azure/azure-iot-sdk-c/tree/master/iothub_client/samples/iothub_client_sample_mqtt_dm/pi_device
[lnk-nuget-client-sdk]: https://www.nuget.org/packages/Microsoft.Azure.Devices.Client/
[lnk-nuget-service-sdk]: https://www.nuget.org/packages/Microsoft.Azure.Devices/