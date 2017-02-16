---
title: "Introducción a la administración de dispositivos de Azure IoT Hub | Microsoft Docs"
description: "Este tutorial muestra cómo comenzar con la administración de dispositivos en IoT Hub de Azure"
services: iot-hub
documentationcenter: .net
author: juanjperez
manager: timlt
editor: 
ms.assetid: e044006d-ffd6-469b-bc63-c182ad066e31
ms.service: iot-hub
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/17/2016
ms.author: juanpere
translationtype: Human Translation
ms.sourcegitcommit: 00746fa67292fa6858980e364c88921d60b29460
ms.openlocfilehash: cf9741e7bc30ccb5e6b8f79dad7c8ef725cd683a


---
# <a name="tutorial-get-started-with-device-management"></a>Tutorial: Introducción a la administración de dispositivos

[!INCLUDE [iot-hub-selector-dm-getstarted](../../includes/iot-hub-selector-dm-getstarted.md)]
## <a name="introduction"></a>Introducción
Las aplicaciones en la nube de IoT pueden usar primitivos en Azure IoT Hub, es decir, dispositivos gemelos y métodos directos, para iniciar y supervisar de forma remota las acciones de administración de dispositivos en los dispositivos.  En este artículo se proporcionan instrucciones y código sobre el funcionamiento conjunto de un dispositivo y una aplicación back-end de IoT para iniciar y supervisar un reinicio de dispositivo remoto con IoT Hub.

Para iniciar y supervisar de forma remota las acciones de administración de dispositivos en los dispositivos desde una aplicación basada en la nube y de back-end, use primitivos de IoT Hub como [dispositivos gemelos][lnk-devtwin] y [métodos directos][lnk-c2dmethod]. Este tutorial muestra cómo una aplicación de back-end y un dispositivo pueden trabajar juntos para permitirle iniciar y supervisar el reinicio del dispositivo remoto desde IoT Hub.

Use un método directo para iniciar acciones de administración de dispositivos (por ejemplo, reinicio, restablecimiento de fábrica y actualización de firmware) desde una aplicación back-end en la nube. El dispositivo es responsable de:

* Controlar la solicitud del método enviada desde IoT Hub.
* Iniciar la acción específica del dispositivo correspondiente en el dispositivo.
* Proporcionar actualizaciones de estado mediante las propiedades notificadas a IoT Hub.

Puede usar una aplicación de back-end en la nube para ejecutar consultas de dispositivos gemelos para informar sobre el progreso de las acciones de administración de los dispositivos.

En este tutorial se muestra cómo realizar las siguientes acciones:

* Usar Azure Portal para un IoT Hub y una identidad de dispositivo en este.
* Crear una aplicación de dispositivo simulado que tenga un método directo que permita un reinicio que se pueda llamar desde la nube.
* Crear una aplicación de consola que llame a un método directo de reinicio en la aplicación de dispositivo simulado mediante su centro de IoT.

Al final de este tutorial, tendrá una aplicación de dispositivo de consola de Node.js y una aplicación back-end de consola .NET (C#):

**dmpatterns_getstarted_device.js**, que se conecta al IoT Hub con la identidad del dispositivo creada anteriormente, recibe un método directo de reinicio, simula un reinicio físico y notifica la hora del último reinicio.

**TriggerReboot**, que llama a un método directo en la aplicación de dispositivo simulado, muestra la respuesta y muestra las propiedades notificadas actualizadas.

Para completar este tutorial, necesitará lo siguiente:

* Microsoft Visual Studio 2015.
* Node.js versión 0.12.x o posteriores. <br/>  En [Preparación del entorno de desarrollo][lnk-dev-setup] se describe cómo instalar Node.js para este tutorial en Windows o Linux.
* Una cuenta de Azure activa. (En caso de no tenerla, puede crear una [cuenta gratuita][lnk-free-trial] en solo unos minutos).

[!INCLUDE [iot-hub-get-started-create-hub](../../includes/iot-hub-get-started-create-hub.md)]

[!INCLUDE [iot-hub-get-started-create-device-identity](../../includes/iot-hub-get-started-create-device-identity.md)]

## <a name="trigger-a-remote-reboot-on-the-device-using-a-direct-method"></a>Desencadenar un reinicio remoto en el dispositivo con un método directo
En esta sección, creará una aplicación de consola de .NET (mediante C#) que inicia un reinicio remoto en un dispositivo con un método directo y usa consultas de dispositivo gemelo para encontrar el tiempo de reinicio más reciente para ese dispositivo.

1. En Visual Studio, agregue un proyecto de escritorio clásico de Windows de Visual C# a la solución actual mediante la plantilla de proyecto **Aplicación de consola** . Asigne al proyecto el nombre **TriggerReboot**.

    ![Nuevo proyecto de escritorio clásico de Windows de Visual C#][img-createapp]

2. En el Explorador de soluciones, haga clic con el botón derecho en el proyecto **TriggerReboot** y, luego, haga clic en **Administrar paquetes NuGet**.
3. En la ventana **Administrador de paquetes NuGet**, seleccione **Examinar**, busque **microsoft.azure.devices**, seleccione **Instalar** para instalar el paquete **Microsoft.Azure.Devices** y acepte los términos de uso. Este procedimiento permite descargar, instalar y agregar una referencia al paquete NuGet de [Microsoft Azure IoT Service SDK][lnk-nuget-service-sdk] (SDK de Servicio IoT de Microsoft Azure) y a sus dependencias.

    ![Ventana del Administrador de paquetes NuGet][img-servicenuget]
4. Agregue las siguientes instrucciones `using` al principio del archivo **Program.cs** :
   
        using Microsoft.Azure.Devices;
        
5. Agregue los campos siguientes a la clase **Program** . Sustituya el valor de marcador de posición por la cadena de conexión del centro de IoT que creó en la sección anterior y el dispositivo de destino.
   
        static RegistryManager registryManager;
        static string connString = "{iot hub connection string}";
        static ServiceClient client;
        static JobClient jobClient;
        static string targetDevice = "{deviceIdForTargetDevice}";
        
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

## <a name="create-a-simulated-device-app"></a>Creación de una aplicación de dispositivo simulado
En esta sección:

* Creará una aplicación de consola de Node.js que responda a un método directo que se llama desde la nube.
* Desencadenará un reinicio del dispositivo simulado.
* Usará las propiedades notificadas para permitir consultas de dispositivo gemelo a fin de identificar los dispositivos y cuándo se reiniciaron por última vez.

1. Cree una nueva carpeta vacía denominada "**manageddevice**".  En la carpeta **manageddevice** , cree un archivo package.json con el siguiente comando en el símbolo del sistema.  Acepte todos los valores predeterminados:
   
    ```
    npm init
    ```
2. En el símbolo del sistema, en la carpeta **manageddevice**, ejecute el siguiente comando para instalar el paquete del SDK de dispositivo **azure-iot-device** y el paquete **azure-iot-device-mqtt**:
   
    ```
    npm install azure-iot-device azure-iot-device-mqtt --save
    ```
3. Con un editor de texto, cree un nuevo archivo **dmpatterns_getstarted_device.js** en la carpeta **manageddevice**.
4. Agregue las siguientes instrucciones "require" al principio del archivo **dmpatterns_getstarted_device.js**:
   
    ```
    'use strict';
   
    var Client = require('azure-iot-device').Client;
    var Protocol = require('azure-iot-device-mqtt').Mqtt;
    ```
5. Agregue una variable **connectionString** y utilícela para crear un cliente de dispositivo.  Reemplace la cadena de conexión por la cadena de conexión del dispositivo.  
   
    ```
    var connectionString = 'HostName={youriothostname};DeviceId=myDeviceId;SharedAccessKey={yourdevicekey}';
    var client = Client.fromConnectionString(connectionString, Protocol);
    ```
6. Agregue la siguiente función para implementar el método directo en el dispositivo
   
    ```
    var onReboot = function(request, response) {
   
        // Respond the cloud app for the direct method
        response.send(200, 'Reboot started', function(err) {
            if (!err) {
                console.error('An error occured when sending a method response:\n' + err.toString());
            } else {
                console.log('Response to method \'' + request.methodName + '\' sent successfully.');
            }
        });
   
        // Report the reboot before the physical restart
        var date = new Date();
        var patch = {
            iothubDM : {
                reboot : {
                    lastReboot : date.toISOString(),
                }
            }
        };
   
        // Get device Twin
        client.getTwin(function(err, twin) {
            if (err) {
                console.error('could not get twin');
            } else {
                console.log('twin acquired');
                twin.properties.reported.update(patch, function(err) {
                    if (err) throw err;
                    console.log('Device reboot twin state reported')
                });  
            }
        });
   
        // Add your device's reboot API for physical restart.
        console.log('Rebooting!');
    };
    ```
7. Agregue el siguiente código para abrir la conexión al centro de IoT e iniciar la escucha del método directo:
   
    ```
    client.open(function(err) {
        if (err) {
            console.error('Could not open IotHub client');
        }  else {
            console.log('Client opened.  Waiting for reboot method.');
            client.onDeviceMethod('reboot', onReboot);
        }
    });
    ```
8. Guarde y cierre el archivo **dmpatterns_getstarted_device.js**.
   
   [AZURE.NOTE] Por simplificar, este tutorial no implementa ninguna directiva de reintentos. En el código de producción, deberá implementar directivas de reintentos (por ejemplo, retroceso exponencial), tal y como se sugiere en el artículo de MSDN [Transient Fault Handling][lnk-transient-faults] (Tratamiento de errores temporales).


## <a name="run-the-apps"></a>Ejecución de las aplicaciones
Ya está preparado para ejecutar las aplicaciones.

1. En el símbolo del sistema dentro de la carpeta **manageddevice**, ejecute el siguiente comando para iniciar la escucha del método directo de reinicio.
   
    ```
    node dmpatterns_getstarted_device.js
    ```
2. Ejecute la aplicación de consola de C# **TriggerReboot**: haga clic con el botón derecho en el proyecto **TriggerReboot** y seleccione **Depurar** e **Iniciar nueva instancia**.

3. Verá la respuesta del dispositivo al método directo en la consola.

## <a name="customize-and-extend-the-device-management-actions"></a>Personalizar y ampliar el dispositivo las acciones de administración del dispositivo
Las soluciones de IoT pueden expandir el conjunto definido de patrones de administración de dispositivos o permitir modelos personalizados mediante el uso de los primitivos de método de nube a dispositivo y dispositivos gemelos. Otros ejemplos de acciones de administración de dispositivos son el restablecimiento de fábrica, la actualización de firmware, la actualización de software, la administración de energía, la administración de conectividad y red, y el cifrado de datos.

## <a name="device-maintenance-windows"></a>Ventanas de mantenimiento del dispositivo
Normalmente, puede dispositivos para llevar a cabo acciones a la vez que minimiza las interrupciones y el tiempo de inactividad.  Las ventanas de mantenimiento dle dispositivo son un patrón que se utiliza habitualmente para definir la hora en la que un dispositivo debe actualizar su configuración. Las soluciones de back-end pueden utilizar las propiedades deseadas del dispositivo gemelo para definir y activar una directiva en el dispositivo que permita una ventana de mantenimiento. Cuando un dispositivo recibe la directiva de la ventana de mantenimiento, puede usar la propiedad notificada del dispositivo gemelo para informar del estado de la directiva. La aplicación de back-end puede usar luego consultas de dispositivos gemelos para dar testimonio de cumplimiento de dispositivos y cada directiva.

## <a name="next-steps"></a>Pasos siguientes
En este tutorial ha usado un método directo para desencadenar un reinicio remoto en un dispositivo, ha utilizado las propiedades notificadas para notificar la última hora de reinicio del dispositivo y ha consultado el dispositivo gemelo para detectar la última hora de reinicio del dispositivo desde la nube.

Para continuar con la introducción de IoT Hub y los patrones de administración de dispositivos como remoto a través de la actualización de firmware de aire, consulte:

[Tutorial: Realización de una actualización de firmware][lnk-fwupdate]

Para aprender a ampliar la solución de IoT y programar llamadas de método en varios dispositivos, consulte el tutorial sobre la [programación y difusión de trabajos][lnk-tutorial-jobs].

Para continuar con la introducción a IoT Hub, consulte [Introducción al SDK de puerta de enlace de IoT de Azure][lnk-gateway-SDK].

<!-- images and links -->
[img-output]: media/iot-hub-get-started-with-dm/image6.png
[img-dm-ui]: media/iot-hub-get-started-with-dm/dmui.png
[img-servicenuget]: media/iot-hub-csharp-node-device-management-get-started/servicesdknuget.png
[img-createapp]: media/iot-hub-csharp-node-device-management-get-started/createnetapp.png

[lnk-dev-setup]: https://github.com/Azure/azure-iot-sdks/blob/master/doc/get_started/node-devbox-setup.md

[lnk-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[lnk-fwupdate]: iot-hub-node-node-firmware-update.md
[Azure portal]: https://portal.azure.com/
[Using resource groups to manage your Azure resources]: ../azure-portal/resource-group-portal.md
[lnk-dm-github]: https://github.com/Azure/azure-iot-device-management
[lnk-tutorial-jobs]: iot-hub-node-node-schedule-jobs.md
[lnk-gateway-SDK]: iot-hub-linux-gateway-sdk-get-started.md

[lnk-devtwin]: iot-hub-devguide-device-twins.md
[lnk-c2dmethod]: iot-hub-devguide-direct-methods.md
[lnk-transient-faults]: https://msdn.microsoft.com/library/hh680901(v=pandp.50).aspx
[lnk-nuget-service-sdk]: https://www.nuget.org/packages/Microsoft.Azure.Devices/


<!--HONumber=Nov16_HO5-->


