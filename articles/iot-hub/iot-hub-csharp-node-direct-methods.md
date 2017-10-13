---
title: "Uso de los métodos directos de IoT Hub de Azure (.NET y Node) | Microsoft Docs"
description: "Describe cómo usar los métodos directos de IoT Hub de Azure. Usará el SDK de dispositivo IoT de Azure para Node.js con el fin de implementar una aplicación de dispositivo simulado que incluye un método directo, además del SDK de servicio IoT de Azure para .NET con el objetivo de implementar una aplicación de servicio que invoque el método directo."
services: iot-hub
documentationcenter: 
author: nberdy
manager: timlt
editor: 
ms.assetid: ab035b8e-bff8-4e12-9536-f31d6b6fe425
ms.service: iot-hub
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/30/2017
ms.author: nberdy
ms.openlocfilehash: 76f1d32b4afeacae1488b4cf28be6c8cf7f4ea37
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="use-direct-methods-netnode"></a>Uso de métodos directos (.NET y Node)
[!INCLUDE [iot-hub-selector-c2d-methods](../../includes/iot-hub-selector-c2d-methods.md)]

En este tutorial, vamos a desarrollar una aplicación de consola de Node.js y de .NET:

* **CallMethodOnDevice.js**, una aplicación de back-end de .NET, que llama a un método de la aplicación de dispositivo simulado y muestra la respuesta.
* **SimulatedDevice.js**, una aplicación de Node.js que simula un dispositivo que se conecta a su centro de IoT con la identidad del dispositivo creada anteriormente y responde al método llamado por la nube.

> [!NOTE]
> En el artículo [Azure Iot SDKs][lnk-hub-sdks] (SDK de IoT de Azure) se proporciona información acerca de los diversos SDK que puede usar para crear ambas aplicaciones para que se ejecuten en dispositivos y en el back-end de la solución.
> 
> 

Para completar este tutorial, necesita:

* Visual Studio 2015 o Visual Studio 2017.
* Node.js, versión 4.0.x o posterior.
* Una cuenta de Azure activa. (En caso de no tenerla, puede crear una [cuenta gratuita][lnk-free-trial] en solo unos minutos).

[!INCLUDE [iot-hub-get-started-create-hub](../../includes/iot-hub-get-started-create-hub.md)]

[!INCLUDE [iot-hub-get-started-create-device-identity](../../includes/iot-hub-get-started-create-device-identity.md)]

## <a name="create-a-simulated-device-app"></a>Creación de una aplicación de dispositivo simulado
En esta sección, creará una aplicación de consola de Node.js que responda a un método llamado por el back-end de solución.

1. Cree una nueva carpeta vacía denominada **simulateddevice**. En la carpeta **simulateddevice**, cree un archivo package.json con el siguiente comando en el símbolo del sistema. Acepte todos los valores predeterminados:
   
    ```
    npm init
    ```
2. En el símbolo del sistema, en la carpeta **simulateddevice**, ejecute el siguiente comando para instalar los paquetes **azure-iot-device** y **azure-iot-device-mqtt**:
   
    ```
        npm install azure-iot-device azure-iot-device-mqtt --save
    ```
3. Con un editor de texto, cree un archivo en la carpeta **simulateddevice** y asígnele el nombre **SimulatedDevice.js**.
4. Agregue las siguientes instrucciones `require` al principio del archivo **SimulatedDevice.js** :
   
    ```
    'use strict';
   
    var Mqtt = require('azure-iot-device-mqtt').Mqtt;
    var DeviceClient = require('azure-iot-device').Client;
    ```
5. Agregue una variable **connectionString** y utilícela para crear una instancia de **DeviceClient**. Reemplace **{device connection string}** por la cadena conexión de dispositivo que generó en la sección sobre cómo *crear una identidad de dispositivo*:
   
    ```
    var connectionString = '{device connection string}';
    var client = DeviceClient.fromConnectionString(connectionString, Mqtt);
    ```
6. Agregue la siguiente función para implementar el método directo en el dispositivo:
   
    ```
    function onWriteLine(request, response) {
        console.log(request.payload);
   
        response.send(200, 'Input was written to log.', function(err) {
            if(err) {
                console.error('An error occurred when sending a method response:\n' + err.toString());
            } else {
                console.log('Response to method \'' + request.methodName + '\' sent successfully.' );
            }
        });
    }
    ```
7. Abra la conexión al centro de IoT y e inicialice la escucha del método:
   
    ```
    client.open(function(err) {
        if (err) {
            console.error('could not open IotHub client');
        }  else {
            console.log('client opened');
            client.onDeviceMethod('writeLine', onWriteLine);
        }
    });
    ```
8. Guarde el archivo **SimulatedDevice.js** y ciérrelo.

> [!NOTE]
> Por simplificar, este tutorial no implementa ninguna directiva de reintentos. En el código de producción, debe implementar directivas de reintento (por ejemplo, reintento de conexión), tal y como se sugiere en el artículo de MSDN [Transient Fault Handling][lnk-transient-faults] (Control de errores temporales).
> 
> 

## <a name="call-a-direct-method-on-a-device"></a>Llamada a un método directo en un dispositivo
En esta sección, creará una aplicación de consola de .NET que llame a un método en la aplicación de dispositivo simulado y muestre la respuesta.

1. En Visual Studio, agregue un proyecto de escritorio clásico de Windows de Visual C# a la solución actual mediante la plantilla de proyecto **Aplicación de consola** . Asegúrese de que la versión de .NET Framework sea 4.5.1 o una posterior. Asigne al proyecto el nombre **CallMethodOnDevice**.
   
    ![Nuevo proyecto de escritorio clásico de Windows de Visual C#][10]
2. En el Explorador de soluciones, haga clic con el botón derecho en el proyecto **CallMethodOnDevice** y luego seleccione **Administrar paquetes NuGet...**.
3. En la ventana **Administrador de paquetes NuGet**, seleccione **Examinar**, busque **microsoft.azure.devices**, seleccione **Instalar** para instalar el paquete **Microsoft.Azure.Devices** y acepte los términos de uso. Este procedimiento permite descargar, instalar y agregar una referencia al [paquete NuGet del SDK de dispositivo de IoT de Azure][lnk-nuget-service-sdk] y sus dependencias.
   
    ![Ventana del Administrador de paquetes NuGet][11]

4. Agregue las siguientes instrucciones `using` al principio del archivo **Program.cs** :
   
        using System.Threading.Tasks;
        using Microsoft.Azure.Devices;
5. Agregue los campos siguientes a la clase **Program** . Sustituya el valor de marcador de posición por la cadena de conexión de IoT Hub para el centro que creó en la sección anterior.
   
        static ServiceClient serviceClient;
        static string connectionString = "{iot hub connection string}";
6. Agregue el método siguiente a la clase **Program** :
   
        private static async Task InvokeMethod()
        {
            var methodInvocation = new CloudToDeviceMethod("writeLine") { ResponseTimeout = TimeSpan.FromSeconds(30) };
            methodInvocation.SetPayloadJson("'a line to be written'");

            var response = await serviceClient.InvokeDeviceMethodAsync("myDeviceId", methodInvocation);

            Console.WriteLine("Response status: {0}, payload:", response.Status);
            Console.WriteLine(response.GetPayloadAsJson());
        }
   
    Este método invoca un método directo con el nombre `writeLine` en el dispositivo `myDeviceId`. A continuación, escribe la respuesta proporcionada por el dispositivo en la consola. Observe cómo es posible especificar un valor de tiempo de espera de respuesta para el dispositivo.
7. Por último, agregue las líneas siguientes al método **Main** :
   
        serviceClient = ServiceClient.CreateFromConnectionString(connectionString);
        InvokeMethod().Wait();
        Console.WriteLine("Press Enter to exit.");
        Console.ReadLine();

## <a name="run-the-applications"></a>Ejecución de las aplicaciones
Ahora está preparado para ejecutar las aplicaciones.

1. En el Explorador de soluciones de Visual Studio, haga clic con el botón derecho en la solución y seleccione **Establecer proyectos de inicio...**. Seleccione **Proyecto de inicio único**y, a continuación, seleccione el proyecto **CallMethodOnDevice** en el menú desplegable.

2. En un símbolo del sistema, en la carpeta **simulateddevice** , ejecute el siguiente comando para empezar la escucha de llamadas de método desde IoT Hub:
   
    ```
    node SimulatedDevice.js
    ```
   Espere a que se abra el dispositivo simulado: ![][7]
3. Ahora que el dispositivo está conectado y esperando invocaciones del método, ejecute la aplicación **CallMethodOnDevice** de .NET para invocar el método en la aplicación de dispositivo simulado. Debería ver la respuesta del dispositivo escrita en la consola.
   
    ![][8]
4. El dispositivo, a continuación, responde al método imprimiendo este mensaje:
   
    ![][9]

## <a name="next-steps"></a>Pasos siguientes
En este tutorial, configuró un centro de IoT nuevo en Azure Portal y, después, creó una identidad de dispositivo en el registro de identidades del centro de IoT. Usó esta identidad de dispositivo para que la aplicación del dispositivo simulado reaccionara a los métodos que se invoquen desde la nube. También creó una aplicación que invoca métodos en el dispositivo y muestra la respuesta del dispositivo. 

Para continuar la introducción a IoT Hub y explorar otros escenarios de IoT, consulte:

* [Introducción a Iot Hub]
* [Programación de trabajos en varios dispositivos][lnk-devguide-jobs]

Para información sobre cómo ampliar la solución de IoT y programar llamadas de método en varios dispositivos, consulte el tutorial [Programación de trabajos en varios dispositivos][lnk-tutorial-jobs].

<!-- Images. -->
[7]: ./media/iot-hub-csharp-node-direct-methods/run-simulated-device.png
[8]: ./media/iot-hub-csharp-node-direct-methods/netserviceapp.png
[9]: ./media/iot-hub-csharp-node-direct-methods/methods-output.png

[10]: ./media/iot-hub-csharp-node-direct-methods/direct-methods-csharp1.png
[11]: ./media/iot-hub-csharp-node-direct-methods/direct-methods-csharp2.png

<!-- Links -->
[lnk-transient-faults]: https://msdn.microsoft.com/library/hh680901(v=pandp.50).aspx

[lnk-dev-setup]: https://github.com/Azure/azure-iot-sdk-node/blob/master/doc/node-devbox-setup.md

[lnk-hub-sdks]: iot-hub-devguide-sdks.md
[lnk-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[lnk-portal]: https://portal.azure.com/
[lnk-nuget-service-sdk]: https://www.nuget.org/packages/Microsoft.Azure.Devices/

[lnk-devguide-jobs]: iot-hub-devguide-jobs.md
[lnk-tutorial-jobs]: iot-hub-node-node-schedule-jobs.md
[lnk-devguide-methods]: iot-hub-devguide-direct-methods.md
[lnk-devguide-mqtt]: iot-hub-mqtt-support.md

[Send Cloud-to-Device messages with IoT Hub]: iot-hub-csharp-csharp-c2d.md
[Process Device-to-Cloud messages]: iot-hub-csharp-csharp-process-d2c.md
[Introducción a Iot Hub]: iot-hub-node-node-getstarted.md
