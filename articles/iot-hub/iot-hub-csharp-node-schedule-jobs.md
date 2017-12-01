---
title: "Cómo programar trabajos con IoT Hub de Azure (.NET y Node) | Microsoft Docs"
description: "Cómo programar un trabajo de IoT Hub de Azure para invocar un método directo en varios dispositivos. Usará el SDK de dispositivo IoT de Azure para Node.js con el fin de implementar una aplicación de dispositivo simulado, además del SDK de servicio IoT de Azure para .NET con el objetivo de implementar una aplicación de servicio para ejecutar el trabajo."
services: iot-hub
documentationcenter: .net
author: juanjperez
manager: timlt
editor: 
ms.assetid: 2233356e-b005-4765-ae41-3a4872bda943
ms.service: iot-hub
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/10/2017
ms.author: juanpere
ms.openlocfilehash: e6795f09e275f9fcd38000d48710560244abc11d
ms.sourcegitcommit: 933af6219266cc685d0c9009f533ca1be03aa5e9
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/18/2017
---
# <a name="schedule-and-broadcast-jobs-netnodejs"></a>Programación y difusión de trabajos (.NET/Node.js)

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

* Creación de una aplicación de dispositivo que implemente un método directo denominado **lockDoor** que la aplicación de back-end puede llamar. La aplicación de dispositivo también recibe cambios en la propiedad deseada de la aplicación de back-end.
* Creación de una aplicación de back-end que cree un trabajo para llamar al método directo **lockDoor** en varios dispositivos. Otro trabajo envía las actualizaciones en la propiedad deseada a varios dispositivos.

Al final de este tutorial, tendrá una aplicación de dispositivo de consola de Node.js y una aplicación back-end de consola .NET (C#):

**simDevice.js**, que se conecta a la instancia de IoT Hub, implementa el método directo **lockDoor** y controla los cambios en la propiedad deseada.

**ScheduleJob**, que usa trabajos para llamar al método directo **lockDoor** y actualizar las propiedades del dispositivo gemelo deseadas en varios dispositivos.

Para completar este tutorial, necesitará lo siguiente:

* Visual Studio 2015 o Visual Studio 2017.
* Node.js, versión 4.0.x o posterior. El artículo [Preparación del entorno de desarrollo][lnk-dev-setup] describe cómo instalar Node.js para este tutorial en Windows o Linux.
* Una cuenta de Azure activa. En caso de no tener ninguna, puede crear una [cuenta gratuita][lnk-free-trial] en tan solo unos minutos.

[!INCLUDE [iot-hub-get-started-create-hub](../../includes/iot-hub-get-started-create-hub.md)]

[!INCLUDE [iot-hub-get-started-create-device-identity](../../includes/iot-hub-get-started-create-device-identity.md)]

## <a name="schedule-jobs-for-calling-a-direct-method-and-sending-device-twin-updates"></a>Programación de trabajos para llamar a un método directo y envío de actualizaciones de dispositivo gemelo

En esta sección, creará una aplicación de consola de .NET (mediante C#) que usa trabajos para llamar al método directo **lockDoor** y enviar las actualizaciones en la propiedad deseada a varios dispositivos.

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
    using System.Threading.Tasks;
    ```

1. Agregue los campos siguientes a la clase **Program** . Sustituya el marcador de posición por la cadena de conexión de IoT Hub para el centro que creó en la sección anterior.

    ```csharp
    static string connString = "{iot hub connection string}";
    static ServiceClient client;
    static JobClient jobClient;
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
        CloudToDeviceMethod directMethod = new CloudToDeviceMethod("lockDoor", TimeSpan.FromSeconds(5), TimeSpan.FromSeconds(5));

        JobResponse result = await jobClient.ScheduleDeviceMethodAsync(jobId,
            "deviceId='myDeviceId'",
            directMethod,
            DateTime.Now,
            10);

        Console.WriteLine("Started Method Job");
    }
    ```

1. Agregue el método siguiente a la clase **Program** :

    ```csharp
    public static async Task StartTwinUpdateJob(string jobId)
    {
        var twin = new Twin();
        twin.Properties.Desired["Building"] = "43";
        twin.Properties.Desired["Floor"] = "3";
        twin.ETag = "*";

        JobResponse result = await jobClient.ScheduleTwinUpdateAsync(jobId,
            "deviceId='myDeviceId'",
            twin,
            DateTime.Now,
            10);

        Console.WriteLine("Started Twin Update Job");
    }
    ```

1. Por último, agregue las líneas siguientes al método **Main** :

    ```csharp
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

1. En el Explorador de soluciones, abra **Establecer proyectos de inicio...** y asegúrese de que la **acción** de **ScheduleJob** sea **Iniciar**. Compile la solución.

## <a name="create-a-simulated-device-app"></a>Creación de una aplicación de dispositivo simulado

En esta sección, creará una aplicación de consola de Node.js que responde a un método directo que se invoca desde la nube, que desencadena un reinicio del dispositivo simulado y utiliza las propiedades notificadas para habilitar consultas en el dispositivo gemelo con el fin de identificar los dispositivos y cuándo se reiniciaron por última vez.

1. Cree una nueva carpeta vacía denominada **simDevice**.  En la carpeta **simDevice**, cree un archivo package.json con el siguiente comando en el símbolo del sistema.  Acepte todos los valores predeterminados:

    ```cmd/sh
    npm init
    ```

1. En el símbolo del sistema, en la carpeta **simDevice**, ejecute el siguiente comando para instalar los paquetes **azure-iot-device** y **azure-iot-device-mqtt**:

    ```cmd/sh
    npm install azure-iot-device azure-iot-device-mqtt --save
    ```

1. Con un editor de texto, cree un nuevo archivo **simDevice.js** en la carpeta **simDevice**.

1. Agregue las siguientes instrucciones "require" al principio del archivo **simDevice.js**:

    ```nodejs
    'use strict';
   
    var Client = require('azure-iot-device').Client;
    var Protocol = require('azure-iot-device-mqtt').Mqtt;
    ```

1. Agregue una variable **connectionString** y utilícela para crear una instancia de **cliente**. Asegúrese de reemplazar los marcadores de posición por los valores adecuados para la configuración.

    ```nodejs
    var connectionString = 'HostName={youriothostname};DeviceId={yourdeviceid};SharedAccessKey={yourdevicekey}';
    var client = Client.fromConnectionString(connectionString, Protocol);
    ```

1. Agregue la siguiente función para controlar el método **lockDoor**.

    ```nodejs
    var onLockDoor = function(request, response) {
   
        // Respond the cloud app for the direct method
        response.send(200, function(err) {
            if (!err) {
                console.error('An error occured when sending a method response:\n' + err.toString());
            } else {
                console.log('Response to method \'' + request.methodName + '\' sent successfully.');
            }
        });
   
        console.log('Locking Door!');
    };
    ```

1. Agregue el código siguiente para registrar el controlador del método **lockDoor**.

    ```nodejs
    client.open(function(err) {
        if (err) {
            console.error('Could not connect to IotHub client.');
        }  else {
            console.log('Client connected to IoT Hub.  Waiting for lockDoor direct method.');
            client.onDeviceMethod('lockDoor', onLockDoor);
        }
    });
    ```

1. Guarde y cierre el archivo **simDevice.js**.

> [!NOTE]
> Por simplificar, este tutorial no implementa ninguna directiva de reintentos. En el código de producción, deberá implementar directivas de reintentos (por ejemplo, retroceso exponencial), tal y como se sugiere en el artículo de MSDN [Transient Fault Handling][lnk-transient-faults] (Tratamiento de errores temporales).

## <a name="run-the-apps"></a>Ejecución de las aplicaciones

Ya está preparado para ejecutar las aplicaciones.

1. En el símbolo del sistema dentro de la carpeta **simDevice**, ejecute el siguiente comando para iniciar la escucha del método directo de reinicio.

    ```cmd/sh
    node simDevice.js
    ```

1. Ejecute la aplicación de consola de C# **ScheduleJob** haciendo clic con el botón derecho en el proyecto **ScheduleJob** y seleccione **Depurar** e **Iniciar nueva instancia**.

1. Verá la salida de las aplicaciones back-end y de dispositivo.

    ![Ejecutar las aplicaciones para programar trabajos][img-schedulejobs]

## <a name="next-steps"></a>Pasos siguientes

En este tutorial, ha utilizado un trabajo para programar un método directo para un dispositivo y la actualización de las propiedades del dispositivo gemelo.

Para continuar con la introducción de IoT Hub y los patrones de administración de dispositivos como remoto a través de la actualización de firmware de aire, consulte [Tutorial: Realización de una actualización de firmware][lnk-fwupdate].

Para más información sobre cómo implementar AI en dispositivos perimetrales con Azure IoT Edge, consulte [Introducción a IoT Edge][lnk-iot-edge].

<!-- images -->
[img-servicenuget]: media/iot-hub-csharp-node-schedule-jobs/servicesdknuget.png
[img-createapp]: media/iot-hub-csharp-node-schedule-jobs/createnetapp.png
[img-schedulejobs]: media/iot-hub-csharp-node-schedule-jobs/schedulejobs.png

[lnk-get-started-twin]: iot-hub-node-node-twin-getstarted.md
[lnk-twin-props]: iot-hub-node-node-twin-how-to-configure.md
[lnk-c2d-methods]: iot-hub-node-node-direct-methods.md
[lnk-dev-methods]: iot-hub-devguide-direct-methods.md
[lnk-fwupdate]: iot-hub-node-node-firmware-update.md
[lnk-iot-edge]: ../iot-edge/tutorial-simulate-device-linux.md
[lnk-dev-setup]: https://github.com/Azure/azure-iot-sdk-node/blob/master/doc/node-devbox-setup.md
[lnk-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[lnk-transient-faults]: https://msdn.microsoft.com/library/hh680901(v=pandp.50).aspx
[lnk-nuget-service-sdk]: https://www.nuget.org/packages/Microsoft.Azure.Devices/
