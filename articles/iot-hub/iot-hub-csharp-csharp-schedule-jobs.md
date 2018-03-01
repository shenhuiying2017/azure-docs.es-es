---
title: "Cómo programar trabajos con IoT Hub de Azure (.NET/.NET) | Microsoft Docs"
description: "Cómo programar un trabajo de IoT Hub de Azure para invocar un método directo en varios dispositivos. El SDK de dispositivo IoT de Azure para .NET se usa para implementar las aplicaciones de dispositivo simulado y una aplicación de servicio para ejecutar el trabajo."
services: iot-hub
documentationcenter: .net
author: msebolt
manager: timlt
editor: 
ms.assetid: 2233356e-b005-4765-ae41-3a4872bda943
ms.service: iot-hub
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 012/16/2018
ms.author: v-masebo
ms.openlocfilehash: 0cdc23683489e103b061044856d68a8b014d3535
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 02/21/2018
---
# <a name="schedule-and-broadcast-jobs-netnet"></a>Programación y difusión de trabajos (.NET/.NET)

[!INCLUDE [iot-hub-selector-schedule-jobs](../../includes/iot-hub-selector-schedule-jobs.md)]

Use Azure IoT Hub para programar y realizar el seguimiento de los trabajos que actualizan millones de dispositivos. Use los trabajos para:

* Actualizar las propiedades deseadas
* Actualizar etiquetas
* Invocar métodos directos

Los trabajos contienen una de estas acciones y realizan el seguimiento de la ejecución en un conjunto de dispositivos, que define una consulta de dispositivo gemelo. Por ejemplo, una aplicación de back-end puede utilizar un trabajo para invocar un método directo en 10 000 dispositivos que reinicie los dispositivos. Especifique el conjunto de dispositivos con una consulta de dispositivo gemelo y programe el trabajo para que se ejecute en otro momento. Este trabajo realiza el seguimiento del progreso mientras los dispositivos reciben y ejecutan el método directo de reinicio.

Para más información sobre estas funcionalidades, consulte:

* Dispositivo gemelo y propiedades: [Introducción a los dispositivos gemelos][lnk-get-started-twin] y [Tutorial: Uso de las propiedades deseadas para configurar dispositivos][lnk-twin-props]
* Métodos directos: [Guía del desarrollador de IoT Hub: métodos directos][lnk-dev-methods] y [Tutorial: Uso de métodos directos][lnk-c2d-methods]

En este tutorial se muestra cómo realizar las siguientes acciones:

* Creación de una aplicación de dispositivo que implemente un método directo denominado **LockDoor** que la aplicación de back-end puede llamar.
* Creación de una aplicación de back-end que cree un trabajo para llamar al método directo **LockDoor** en varios dispositivos. Otro trabajo envía las actualizaciones en la propiedad deseada a varios dispositivos.

Al final de este tutorial tendrá dos aplicaciones de consola de .NET (C#):

**SimulateDeviceMethods** que se conecta a IoT Hub e implementa el método directo **LockDoor**.

**ScheduleJob**, que usa trabajos para llamar al método directo **LockDoor** y actualizar las propiedades del dispositivo gemelo deseadas en varios dispositivos.

Para completar este tutorial, necesitará lo siguiente:

* Visual Studio 2015 o Visual Studio 2017.
* Una cuenta de Azure activa. Si no tiene ninguna, puede crear una [cuenta gratuita][lnk-free-trial] en tan solo unos minutos.

[!INCLUDE [iot-hub-get-started-create-hub](../../includes/iot-hub-get-started-create-hub.md)]

[!INCLUDE [iot-hub-get-started-create-device-identity-portal](../../includes/iot-hub-get-started-create-device-identity-portal.md)]


## <a name="create-a-simulated-device-app"></a>Creación de una aplicación de dispositivo simulado
En esta sección, creará una aplicación de consola de .NET que responderá a un método directo llamado por el back-end de solución.

1. En Visual Studio, agregue un proyecto de escritorio clásico de Windows de Visual C# a la solución actual mediante la plantilla de proyecto **Aplicación de consola** . Asigne al proyecto el nombre **SimulateDeviceMethods**.
   
    ![Nueva aplicación para dispositivo de Windows clásico de Visual C#][img-createdeviceapp]
    
1. En el Explorador de soluciones, haga clic con el botón derecho en el proyecto **SimulateDeviceMethods** y luego haga clic en **Administrar paquetes NuGet...**.

1. En la ventana **Administrador de paquetes NuGet**, seleccione **Examinar** y busque **microsoft.azure.devices.client**. Seleccione **Instalar** para instalar el paquete **Microsoft.Azure.Devices.Client** y acepte las condiciones de uso. Este procedimiento permite descargar, instalar y agregar una referencia al paquete NuGet del [SDK de dispositivo Azure IoT][lnk-nuget-client-sdk] y sus dependencias.
   
    ![Ventana del Administrador de paquetes NuGet: aplicación cliente][img-clientnuget]

1. Agregue las siguientes instrucciones `using` al principio del archivo **Program.cs** :
   
    ```csharp
    using Microsoft.Azure.Devices.Client;
    using Microsoft.Azure.Devices.Shared;

    using Newtonsoft.Json;
    ```

1. Agregue los campos siguientes a la clase **Program** . Sustituya el valor de marcador de posición por la cadena de conexión del dispositivo que anotó en la sección anterior:

    ```csharp
    static string DeviceConnectionString = "<yourDeviceConnectionString>";
    static DeviceClient Client = null;

1. Add the following to implement the direct method on the device:

    ```csharp
    static Task<MethodResponse> LockDoor(MethodRequest methodRequest, object userContext)
    {
        Console.WriteLine();
        Console.WriteLine("Locking Door!");
        Console.WriteLine("\nReturning response for method {0}", methodRequest.Name);
            
        string result = "'Door was locked.'";
        return Task.FromResult(new MethodResponse(Encoding.UTF8.GetBytes(result), 200));
    }

1. Add the following to implement the device twins listener on the device:

    ```csharp
    private static async Task OnDesiredPropertyChanged(TwinCollection desiredProperties, object userContext)
    {
        Console.WriteLine("Desired property change:");
        Console.WriteLine(JsonConvert.SerializeObject(desiredProperties));
    }
    ```

1. Finalmente, agregue el código siguiente al método **Main** para abrir la conexión a su IoT Hub e inicializar la escucha del método directo:
   
    ```csharp
    try
    {
        Console.WriteLine("Connecting to hub");
        Client = DeviceClient.CreateFromConnectionString(DeviceConnectionString, TransportType.Mqtt);

        Client.SetMethodHandlerAsync("LockDoor", LockDoor, null);
        Client.SetDesiredPropertyUpdateCallbackAsync(OnDesiredPropertyChanged, null);

        Console.WriteLine("Waiting for direct method call and device twin update\n Press enter to exit.");
        Console.ReadLine();

        Console.WriteLine("Exiting...");

        Client.SetMethodHandlerAsync("LockDoor", null, null);
        Client.CloseAsync().Wait();
    }
    catch (Exception ex)
    {
        Console.WriteLine();
        Console.WriteLine("Error in sample: {0}", ex.Message);
    }
    ```
        
1. Guarde el trabajo y compile la solución.         

> [!NOTE]
> Por simplificar, este tutorial no implementa ninguna directiva de reintentos. En el código de producción, debe implementar directivas de reintento (por ejemplo, reintento de conexión), tal y como se sugiere en el artículo de MSDN [Transient Fault Handling][lnk-transient-faults] (Control de errores temporales).
> 


## <a name="schedule-jobs-for-calling-a-direct-method-and-sending-device-twin-updates"></a>Programación de trabajos para llamar a un método directo y envío de actualizaciones de dispositivo gemelo

En esta sección, creará una aplicación de consola de .NET (mediante C#) que usa trabajos para llamar al método directo **LockDoor** y enviar las actualizaciones en la propiedad deseada a varios dispositivos.

1. En Visual Studio, agregue un proyecto de escritorio clásico de Windows de Visual C# a la solución actual mediante la plantilla de proyecto **Aplicación de consola** . Asigne al proyecto el nombre **ScheduleJob**.

    ![Nuevo proyecto de escritorio clásico de Windows de Visual C#][img-createapp]

1. En el Explorador de soluciones, haga clic con el botón derecho en el proyecto **ScheduleJob** y luego haga clic en **Administrar paquetes NuGet...**.

1. En la ventana **Administrador de paquetes NuGet**, seleccione **Examinar**, busque **microsoft.azure.devices**, seleccione **Instalar** para instalar el paquete **Microsoft.Azure.Devices** y acepte los términos de uso. Este paso permite descargar, instalar y agregar una referencia al [paquete NuGet del SDK de dispositivo IoT de Azure][lnk-nuget-service-sdk] y sus dependencias.

    ![Ventana del Administrador de paquetes NuGet][img-servicenuget]

1. Agregue las siguientes instrucciones `using` al principio del archivo **Program.cs** :
    
    ```csharp
    using Microsoft.Azure.Devices;
    using Microsoft.Azure.Devices.Shared;
    ```

1. Agregue la siguiente instrucción `using` si no está ya presente en las instrucciones predeterminadas.

    ```csharp
    using System.Threading;
    using System.Threading.Tasks;
    ```

1. Agregue los campos siguientes a la clase **Program** . Sustituya los marcadores de posición por la cadena de conexión de IoT Hub que creó en la sección anterior y el nombre del dispositivo.

    ```csharp
    static string connString = "<yourIotHubConnectionString>";
    static string deviceId = "<yourDeviceId>";
    ```

1. Agregue el método siguiente a la clase **Program** :

    ```csharp
    public static async Task MonitorJob(string jobId)
    {
        JobResponse result;
        do
        {
            result = await jobClient.GetJobAsync(jobId);
            Console.WriteLine("Job Status : " + result.Status.ToString());
            Thread.Sleep(2000);
        } while ((result.Status != JobStatus.Completed) && (result.Status != JobStatus.Failed));
    }
    ```

1. Agregue el método siguiente a la clase **Program** :

    ```csharp
    public static async Task StartMethodJob(string jobId)
    {
        CloudToDeviceMethod directMethod = new CloudToDeviceMethod("LockDoor", TimeSpan.FromSeconds(5), TimeSpan.FromSeconds(5));
       
        JobResponse result = await jobClient.ScheduleDeviceMethodAsync(jobId,
            $"DeviceId IN ['{deviceId}']",
            directMethod,
            DateTime.UtcNow,
            (long)TimeSpan.FromMinutes(2).TotalSeconds);

        Console.WriteLine("Started Method Job");
    }
    ```

1. Agregue otro método a la clase **Program**:

    ```csharp
    public static async Task StartTwinUpdateJob(string jobId)
    {
        Twin twin = new Twin(deviceId);
        twin.Tags = new TwinCollection();
        twin.Tags["Building"] = "43";
        twin.Tags["Floor"] = "3";
        twin.ETag = "*";

        twin.Properties.Desired["LocationUpdate"] = DateTime.UtcNow;

        JobResponse createJobResponse = jobClient.ScheduleTwinUpdateAsync(
            jobId,
            $"DeviceId IN ['{deviceId}']", 
            twin, 
            DateTime.UtcNow, 
            (long)TimeSpan.FromMinutes(2).TotalSeconds).Result;

        Console.WriteLine("Started Twin Update Job");
    }
    ```

    > [!NOTE]
    > Para más información sobre la sintaxis de consulta, consulte [Lenguaje de consulta de IoT Hub][lnk-query].
    > 

1. Por último, agregue las líneas siguientes al método **Main** :

    ```csharp
    Console.WriteLine("Press ENTER to start running jobs.");
    Console.ReadLine();

    jobClient = JobClient.CreateFromConnectionString(connString);

    string methodJobId = Guid.NewGuid().ToString();

    StartMethodJob(methodJobId);
    MonitorJob(methodJobId).Wait();
    Console.WriteLine("Press ENTER to run the next job.");
    Console.ReadLine();

    string twinUpdateJobId = Guid.NewGuid().ToString();

    StartTwinUpdateJob(twinUpdateJobId);
    MonitorJob(twinUpdateJobId).Wait();
    Console.WriteLine("Press ENTER to exit.");
    Console.ReadLine();
    ```

1. Guarde el trabajo y compile la solución. 


## <a name="run-the-apps"></a>Ejecución de las aplicaciones

Ya está preparado para ejecutar las aplicaciones.

1. En el Explorador de soluciones de Visual Studio, haga clic con el botón derecho en la solución y, después, haga clic en **Compilar**. **Proyectos de inicio múltiples**. Asegúrese de que `SimulateDeviceMethods` está en la parte superior de la lista seguido de `ScheduleJob`. Establezca ambas acciones en **Iniciar** y haga clic en **Aceptar**.

1. Ejecute los proyectos haciendo clic en **Iniciar** o vaya al menú **Depurar** y haga clic en **Iniciar depuración**.

1. Verá la salida de las aplicaciones back-end y de dispositivo.

    ![Ejecutar las aplicaciones para programar trabajos][img-schedulejobs]


## <a name="next-steps"></a>pasos siguientes

En este tutorial, ha utilizado un trabajo para programar un método directo para un dispositivo y la actualización de las propiedades del dispositivo gemelo.

Para continuar con la introducción de IoT Hub y los patrones de administración de dispositivos como remoto a través de la actualización de firmware de aire, consulte [Tutorial: Realización de una actualización de firmware][lnk-fwupdate].

Para más información sobre cómo implementar AI en dispositivos perimetrales con Azure IoT Edge, consulte [Introducción a IoT Edge][lnk-iot-edge].

<!-- images -->
[img-createdeviceapp]: ./media/iot-hub-csharp-csharp-schedule-jobs/create-device-app.png
[img-clientnuget]: ./media/iot-hub-csharp-csharp-schedule-jobs/device-app-nuget.png
[img-servicenuget]: media/iot-hub-csharp-csharp-schedule-jobs/servicesdknuget.png
[img-createapp]: media/iot-hub-csharp-csharp-schedule-jobs/createnetapp.png
[img-schedulejobs]: media/iot-hub-csharp-csharp-schedule-jobs/schedulejobs.png

[lnk-get-started-twin]: iot-hub-csharp-csharp-twin-getstarted.md
[lnk-twin-props]: iot-hub-csharp-csharp-twin-how-to-configure.md
[lnk-c2d-methods]: iot-hub-csharp-csharp-direct-methods.md
[lnk-dev-methods]: iot-hub-devguide-direct-methods.md
[lnk-fwupdate]: iot-hub-csharp-csharp-firmware-update.md
[lnk-iot-edge]: ../iot-edge/tutorial-simulate-device-linux.md
[lnk-dev-setup]: https://github.com/Azure/azure-iot-sdk-node/blob/master/doc/node-devbox-setup.md
[lnk-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[lnk-transient-faults]: https://docs.microsoft.com/azure/architecture/best-practices/transient-faults
[lnk-nuget-client-sdk]: https://www.nuget.org/packages/Microsoft.Azure.Devices.Client/
[lnk-nuget-service-sdk]: https://www.nuget.org/packages/Microsoft.Azure.Devices/
[lnk-query]: https://docs.microsoft.com/en-us/azure/iot-hub/iot-hub-devguide-query-language
