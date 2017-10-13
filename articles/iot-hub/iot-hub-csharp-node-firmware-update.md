---
title: "Actualización de firmware de dispositivos con IoT Hub de Azure | Microsoft Docs"
description: "Describe cómo usar la administración de dispositivos en IoT Hub de Azure para iniciar una actualización de firmware del dispositivo. Usará el SDK de dispositivo IoT de Azure para Node.js con el fin de implementar una aplicación de dispositivo simulado, además del SDK de servicio IoT de Azure para .NET con el objetivo de implementar una aplicación de servicio que desencadena la actualización el firmware."
services: iot-hub
documentationcenter: .net
author: juanjperez
manager: timlt
editor: 
ms.assetid: 70b84258-bc9f-43b1-b7cf-de1bb715f2cf
ms.service: iot-hub
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/30/2017
ms.author: juanpere
ms.openlocfilehash: 157f112869f0042e330e6b281367632ca015e890
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="use-device-management-to-initiate-a-device-firmware-update-netnode"></a>Use la administración de dispositivos para iniciar una actualización de firmware del dispositivo (. NET y Node)
[!INCLUDE [iot-hub-selector-firmware-update](../../includes/iot-hub-selector-firmware-update.md)]

En el tutorial de [Introducción a la administración de dispositivos][lnk-dm-getstarted], vimos cómo usar los primitivos de [dispositivo gemelo][lnk-devtwin] y [métodos directos][lnk-c2dmethod] para reiniciar de forma remota un dispositivo. Este tutorial emplea los mismos tipos primitivos de IoT Hub. Además, proporciona orientación y muestra cómo realizar una actualización de firmware simulada de extremo a extremo.  Este patrón se usa en la implementación de actualizaciones de firmware del [ejemplo de implementación de dispositivos Raspberry Pi][lnk-rpi-implementation].

En este tutorial se muestra cómo realizar las siguientes acciones:

* Crear una aplicación de consola de .NET que llame al método directo firmwareUpdate en la aplicación de dispositivo simulado a través de su IoT Hub.
* Cree una aplicación de dispositivo simulado que implemente un método directo **firmwareUpdate**. Este método inicia un proceso de varias fases que espera para descargar la imagen de firmware, la descarga y, por último, la aplica. Durante cada fase de la actualización, el dispositivo usa las propiedades notificadas para informar sobre el progreso.

Al final de este tutorial, tendrá una aplicación de dispositivo de consola de Node.js y una aplicación back-end de consola .NET (C#):

**dmpatterns_fwupdate_service.js**, que llama a un método directo en la aplicación de dispositivo simulado, muestra la respuesta y muestra periódicamente (cada 500 ms) las propiedades notificadas actualizadas.

**TriggerFWUpdate**, que se conecta a IoT Hub con la identidad del dispositivo creada anteriormente, recibe un método directo firmwareUpdate y se ejecuta mediante un proceso de varios estados para simular una actualización de firmware; entre ellas, la espera para la descarga de imágenes, la descarga de la nueva imagen y, finalmente, la aplicación de dicha imagen.

Para completar este tutorial, necesitará lo siguiente:

* Visual Studio 2015 o Visual Studio 2017.
* Node.js versión 4.0.x o posterior. <br/>  En [Preparación del entorno de desarrollo][lnk-dev-setup] se describe cómo instalar Node.js para este tutorial en Windows o Linux.
* Una cuenta de Azure activa. (En caso de no tenerla, puede crear una [cuenta gratuita][lnk-free-trial] en solo unos minutos).

Siga los pasos del artículo de [introducción a la administración de dispositivos](iot-hub-csharp-node-device-management-get-started.md) para crear su instancia de IoT Hub y obtener la cadena de conexión de IoT Hub.

[!INCLUDE [iot-hub-get-started-create-hub](../../includes/iot-hub-get-started-create-hub.md)]

[!INCLUDE [iot-hub-get-started-create-device-identity](../../includes/iot-hub-get-started-create-device-identity.md)]

## <a name="trigger-a-remote-firmware-update-on-the-device-using-a-direct-method"></a>Desencadenamiento de una actualización de firmware remota en el dispositivo con un método directo
En esta sección, creará una aplicación de consola de .NET (mediante C#) que inicia una actualización de firmware remota en un dispositivo. La aplicación usa un método directo para iniciar la actualización y utiliza consultas de dispositivos gemelos para obtener periódicamente el estado de la actualización del firmware activa.

1. En Visual Studio, agregue un proyecto de escritorio clásico de Windows de Visual C# a la solución actual mediante la plantilla de proyecto **Aplicación de consola** . Asigne al proyecto el nombre **TriggerFWUpdate**.

    ![Nuevo proyecto de escritorio clásico de Windows de Visual C#][img-createapp]

1. En el Explorador de soluciones, haga clic con el botón derecho en el proyecto **TriggerFWUpdate** y luego seleccione **Administrar paquetes NuGet**.
1. En la ventana **Administrador de paquetes NuGet**, seleccione **Examinar**, busque **microsoft.azure.devices**, seleccione **Instalar** para instalar el paquete **Microsoft.Azure.Devices** y acepte los términos de uso. Este procedimiento permite descargar, instalar y agregar una referencia al [paquete NuGet del SDK de dispositivo de IoT de Azure][lnk-nuget-service-sdk] y sus dependencias.

    ![Ventana del Administrador de paquetes NuGet][img-servicenuget]
1. Agregue las siguientes instrucciones `using` al principio del archivo **Program.cs** :
   
        using Microsoft.Azure.Devices;
        using Microsoft.Azure.Devices.Shared;
        
1. Agregue los campos siguientes a la clase **Program** . Sustituya los valores de varios marcadores de posición por la cadena de conexión de IoT Hub que creó en la sección anterior y por el identificador del dispositivo.
   
        static RegistryManager registryManager;
        static string connString = "{iot hub connection string}";
        static ServiceClient client;
        static JobClient jobClient;
        static string targetDevice = "{deviceIdForTargetDevice}";
        
1. Agregue el método siguiente a la clase **Program** :
   
        public static async Task QueryTwinFWUpdateReported()
        {
            Twin twin = await registryManager.GetTwinAsync(targetDevice);
            Console.WriteLine(twin.Properties.Reported.ToJson());
        }
        
1. Agregue el método siguiente a la clase **Program** :

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

1. Por último, agregue las líneas siguientes al método **Main** :
   
        registryManager = RegistryManager.CreateFromConnectionString(connString);
        StartFirmwareUpdate().Wait();
        QueryTwinFWUpdateReported().Wait();
        Console.WriteLine("Press ENTER to exit.");
        Console.ReadLine();
        
1. En el Explorador de soluciones, abra **Establecer proyectos de inicio...** y asegúrese de que la **acción** de **TriggerFWUpdate** sea **Iniciar**.

1. Compile la solución.

[!INCLUDE [iot-hub-device-firmware-update](../../includes/iot-hub-device-firmware-update.md)]

## <a name="run-the-apps"></a>Ejecución de las aplicaciones
Ya está preparado para ejecutar las aplicaciones.

1. En el símbolo del sistema dentro de la carpeta **manageddevice**, ejecute el siguiente comando para iniciar la escucha del método directo de reinicio.
   
    ```
    node dmpatterns_fwupdate_device.js
    ```
2. En Visual Studio, haga clic con el botón derecho en el proyecto **TriggerFWUpdate** y seleccione **Depurar** e **Iniciar nueva instancia**.

3. Verá la respuesta del dispositivo al método directo en la consola.

    ![Firmware actualizado correctamente][img-fwupdate]

## <a name="next-steps"></a>Pasos siguientes
En este tutorial, ha usado un método directo para desencadenar una actualización de firmware remota en un dispositivo y ha utilizado las propiedades notificadas para entender el progreso del proceso de actualización del firmware.

Para información sobre cómo ampliar la solución de IoT y programar llamadas de método en varios dispositivos, consulte el tutorial [Programación de trabajos en varios dispositivos][lnk-tutorial-jobs].

<!-- images -->
[img-servicenuget]: media/iot-hub-csharp-node-firmware-update/servicesdknuget.png
[img-createapp]: media/iot-hub-csharp-node-firmware-update/createnetapp.png
[img-fwupdate]: media/iot-hub-csharp-node-firmware-update/fwupdated.png

[lnk-devtwin]: iot-hub-devguide-device-twins.md
[lnk-c2dmethod]: iot-hub-devguide-direct-methods.md
[lnk-dm-getstarted]: iot-hub-node-node-device-management-get-started.md
[lnk-tutorial-jobs]: iot-hub-node-node-schedule-jobs.md

[lnk-dev-setup]: https://github.com/Azure/azure-iot-sdk-node/blob/master/doc/node-devbox-setup.md
[lnk-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[lnk-transient-faults]: https://msdn.microsoft.com/library/hh680901(v=pandp.50).aspx
[lnk-rpi-implementation]: https://github.com/Azure/azure-iot-sdk-c/tree/master/iothub_client/samples/iothub_client_sample_mqtt_dm/pi_device
[lnk-nuget-service-sdk]: https://www.nuget.org/packages/Microsoft.Azure.Devices/