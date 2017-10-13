---
title: "Introducción a los dispositivos gemelos de IoT Hub de Azure (.NET y Node) | Microsoft Docs"
description: "Describe cómo usar dispositivos gemelos de IoT Hub de Azure para agregar etiquetas y, luego, usar una consulta de IoT Hub. Usará el SDK de dispositivo IoT de Azure para Node.js con el fin de implementar una aplicación de dispositivo simulado, además del SDK de servicio IoT de Azure para .NET con el objetivo de implementar una aplicación de servicio que agrega las etiquetas y ejecuta la consulta de IoT Hub."
services: iot-hub
documentationcenter: node
author: fsautomata
manager: timlt
editor: 
ms.assetid: f7e23b6e-bfde-4fba-a6ec-dbb0f0e005f4
ms.service: iot-hub
ms.devlang: node
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/07/2017
ms.author: elioda
ms.openlocfilehash: 4cf607e8e0ccd3aab06be54d715c2bf3777caeb0
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="get-started-with-device-twins-netnode"></a>Introducción a los dispositivos gemelos (.NET y Node)
[!INCLUDE [iot-hub-selector-twin-get-started](../../includes/iot-hub-selector-twin-get-started.md)]

Al final de este tutorial tendrá una aplicación de consola Node.js y .NET:

* **AddTagsAndQuery.js**, una aplicación de .NET de back-end diseñada para ejecutarse desde el back-end, que agrega etiquetas y consulta dispositivos gemelos.
* **TwinSimulatedDevice.js**, una aplicación de Node.js que simula un dispositivo que se conecta a su centro de IoT con la identidad del dispositivo creada anteriormente e informa de su estado de conectividad.

> [!NOTE]
> En el artículo [SDK de IoT de Azure][lnk-hub-sdks] se proporciona información sobre los SDK que puede usar para crear aplicaciones de dispositivo y de back-end.
> 
> 

Para completar este tutorial, necesitará lo siguiente:

* Visual Studio 2015 o Visual Studio 2017.
* Node.js, versión 4.0.x o posterior.
* Una cuenta de Azure activa. (En caso de no tenerla, puede crear una [cuenta gratuita][lnk-free-trial] en solo unos minutos).

[!INCLUDE [iot-hub-get-started-create-hub](../../includes/iot-hub-get-started-create-hub.md)]

[!INCLUDE [iot-hub-get-started-create-device-identity](../../includes/iot-hub-get-started-create-device-identity.md)]

## <a name="create-the-service-app"></a>Creación de la aplicación de servicio
En esta sección, creará una aplicación de consola de .NET (que usa C#) que agrega metadatos de ubicación al dispositivo gemelo asociado con **myDeviceId**. A continuación, consulta los dispositivos gemelos almacenados en IoT Hub seleccionando los dispositivos ubicados en Estados Unidos y, después, los que informan de una conexión de red de telefonía móvil.

1. En Visual Studio, agregue un proyecto de escritorio clásico de Windows de Visual C# a la solución actual mediante la plantilla de proyecto **Aplicación de consola** . Asigne el nombre **AddTagsAndQuery** al proyecto.
   
    ![Nuevo proyecto de escritorio clásico de Windows de Visual C#][img-createapp]
1. En el Explorador de soluciones, haga clic con el botón derecho en el proyecto **AddTagsAndQuery** y luego seleccione **Administrar paquetes NuGet...**.
1. En la ventana **Administrador de paquetes NuGet**, seleccione **Examinar** y busque **microsoft.azure.devices**. Seleccione **Instalar** para instalar el paquete **Microsoft.Azure.Devices** y acepte las condiciones de uso. Este procedimiento permite descargar, instalar y agregar una referencia al [paquete NuGet del SDK de dispositivo de IoT de Azure][lnk-nuget-service-sdk] y sus dependencias.
   
    ![Ventana del Administrador de paquetes NuGet][img-servicenuget]
1. Agregue las siguientes instrucciones `using` al principio del archivo **Program.cs** :
   
        using Microsoft.Azure.Devices;
1. Agregue los campos siguientes a la clase **Program** . Sustituya el valor de marcador de posición por la cadena de conexión de IoT Hub para el centro que creó en la sección anterior.
   
        static RegistryManager registryManager;
        static string connectionString = "{iot hub connection string}";
1. Agregue el método siguiente a la clase **Program** :
   
        public static async Task AddTagsAndQuery()
        {
            var twin = await registryManager.GetTwinAsync("myDeviceId");
            var patch =
                @"{
                    tags: {
                        location: {
                            region: 'US',
                            plant: 'Redmond43'
                        }
                    }
                }";
            await registryManager.UpdateTwinAsync(twin.DeviceId, patch, twin.ETag);
   
            var query = registryManager.CreateQuery("SELECT * FROM devices WHERE tags.location.plant = 'Redmond43'", 100);
            var twinsInRedmond43 = await query.GetNextAsTwinAsync();
            Console.WriteLine("Devices in Redmond43: {0}", string.Join(", ", twinsInRedmond43.Select(t => t.DeviceId)));
   
            query = registryManager.CreateQuery("SELECT * FROM devices WHERE tags.location.plant = 'Redmond43' AND properties.reported.connectivity.type = 'cellular'", 100);
            var twinsInRedmond43UsingCellular = await query.GetNextAsTwinAsync();
            Console.WriteLine("Devices in Redmond43 using cellular network: {0}", string.Join(", ", twinsInRedmond43UsingCellular.Select(t => t.DeviceId)));
        }
   
    La clase **RegistryManager** expone todos los métodos necesarios para interactuar con dispositivos gemelos del servicio. El código anterior inicializa primero el objeto **registryManager**, luego recupera el dispositivo gemelo de **myDeviceId** y, por último, actualiza sus etiquetas con la información de la ubicación deseada.
   
    Después de la actualización, ejecuta dos consultas: la primera selecciona solo los dispositivos gemelos que se encuentran en la planta **Redmond43** y la segunda mejora la consulta para seleccionar solo los dispositivos que están también conectados a través de la red de telefonía móvil.
   
    Tenga en cuenta que el código anterior, cuando crea el objeto de **consulta**, especifica un número máximo de documentos devueltos. El objeto **consulta** contiene una propiedad booleana **HasMoreResults** que puede utilizar para invocar a los métodos **GetNextAsTwinAsync** varias veces para recuperar todos los resultados. Un método llamado **GetNextAsJson** está disponible para los resultados que no son dispositivos gemelos, por ejemplo, los resultados de consultas de agregación.
1. Por último, agregue las líneas siguientes al método **Main** :
   
        registryManager = RegistryManager.CreateFromConnectionString(connectionString);
        AddTagsAndQuery().Wait();
        Console.WriteLine("Press Enter to exit.");
        Console.ReadLine();

1. En el Explorador de soluciones, abra **Establecer proyectos de inicio...** y asegúrese de que la **acción** de **AddTagsAndQuery** sea **Iniciar**. Compile la solución.
1. Ejecute esta aplicación haciendo clic con el botón derecho en el proyecto **AddTagsAndQuery** y seleccionando **Depurar**, seguido de **Iniciar nueva instancia**. Debería ver un dispositivo en los resultados de la consulta que pregunta por todos los dispositivos que se encuentran en **Redmond43** y ninguno para la consulta que restringe los resultados a los dispositivos que utilizan una red de telefonía móvil.
   
    ![Resultados de consulta en ventana][img-addtagapp]

En la siguiente sección, creará una aplicación de dispositivo que notifica la información de conectividad y cambia el resultado de la consulta en la sección anterior.

## <a name="create-the-device-app"></a>Creación de la aplicación del dispositivo
En esta sección, creará una aplicación de consola de Node.js que se conecta al centro como **myDeviceId**, y luego actualiza las propiedades notificadas para contener la información que está conectada mediante una red de telefonía móvil.

1. Cree una nueva carpeta vacía denominada **reportconnectivity**. En la carpeta **reportconnectivity** , cree un nuevo archivo package.json con el siguiente comando en el símbolo del sistema. Acepte todos los valores predeterminados.
   
    ```
    npm init
    ```
1. En el símbolo del sistema, en la carpeta **reportconnectivity**, ejecute el siguiente comando para instalar **azure-iot-device** y el paquete **azure-iot-device-mqtt**:
   
    ```
    npm install azure-iot-device azure-iot-device-mqtt --save
    ```
1. Con un editor de texto, cree un nuevo archivo **ReportConnectivity.js** en la carpeta **reportconnectivity**.
1. Agregue el código siguiente al archivo **ReportConnectivity.js** y sustituya el marcador de posición de la cadena de conexión del dispositivo por la cadena que copió al crear la identidad de dispositivo **myDeviceId**:
   
        'use strict';
        var Client = require('azure-iot-device').Client;
        var Protocol = require('azure-iot-device-mqtt').Mqtt;
   
        var connectionString = '{device connection string}';
        var client = Client.fromConnectionString(connectionString, Protocol);
   
        client.open(function(err) {
        if (err) {
            console.error('could not open IotHub client');
        }  else {
            console.log('client opened');
   
            client.getTwin(function(err, twin) {
            if (err) {
                console.error('could not get twin');
            } else {
                var patch = {
                    connectivity: {
                        type: 'cellular'
                    }
                };
   
                twin.properties.reported.update(patch, function(err) {
                    if (err) {
                        console.error('could not update twin');
                    } else {
                        console.log('twin state reported');
                        process.exit();
                    }
                });
            }
            });
        }
        });
   
    El objeto **Cliente** expone todos los métodos necesarios para interactuar con dispositivos gemelos del dispositivo. El código anterior, una vez que inicialice el objeto **Cliente**, recupera el dispositivo gemelo de **myDeviceId**, y actualiza su propiedad notificada con la información de conectividad.
1. Ejecute la aplicación del dispositivo
   
        node ReportConnectivity.js
   
    Verá el mensaje `twin state reported`.
1. Ahora que el dispositivo ha informado sobre su información de conectividad, debe aparecer en ambas consultas. Ejecute la aplicación **AddTagsAndQuery** .NET para volver a ejecutar las consultas. Esta vez **myDeviceId** debe aparecer en los resultados de ambas consulta.
   
    ![][img-addtagapp2]

## <a name="next-steps"></a>Pasos siguientes
En este tutorial, configuró un centro de IoT nuevo en Azure Portal y, después, creó una identidad de dispositivo en el registro de identidades del centro de IoT. Ha agregado metadatos de dispositivo como etiquetas desde una aplicación back-end y ha escrito una aplicación de dispositivo simulado para notificar la información de conectividad de dispositivos en el dispositivo gemelo. También ha aprendido cómo consultar esta información mediante el lenguaje de consulta de IoT Hub de tipo SQL.

Use los siguientes recursos para obtener información sobre cómo:

* enviar telemetría desde dispositivos con el tutorial [Introducción a IoT Hub][lnk-iothub-getstarted];
* configurar dispositivos mediante las propiedades deseadas del dispositivo gemelo con el tutorial [Uso de las propiedades deseadas para configurar dispositivos][lnk-twin-how-to-configure];
* controlar los dispositivos de forma interactiva (por ejemplo, encender un ventilador desde una aplicación controlada por el usuario), con el tutorial [Use direct methods][lnk-methods-tutorial] (Uso de métodos directos).

<!-- images -->
[img-servicenuget]: media/iot-hub-csharp-node-twin-getstarted/servicesdknuget.png
[img-createapp]: media/iot-hub-csharp-node-twin-getstarted/createnetapp.png
[img-addtagapp]: media/iot-hub-csharp-node-twin-getstarted/addtagapp.png
[img-addtagapp2]: media/iot-hub-csharp-node-twin-getstarted/addtagapp2.png

<!-- links -->
[lnk-hub-sdks]: iot-hub-devguide-sdks.md
[lnk-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[lnk-nuget-service-sdk]: https://www.nuget.org/packages/Microsoft.Azure.Devices/

[lnk-d2c]: iot-hub-devguide-messaging.md#device-to-cloud-messages
[lnk-methods]: iot-hub-devguide-direct-methods.md
[lnk-twins]: iot-hub-devguide-device-twins.md
[lnk-query]: iot-hub-devguide-query-language.md
[lnk-identity]: iot-hub-devguide-identity-registry.md

[lnk-iothub-getstarted]: iot-hub-node-node-getstarted.md
[lnk-methods-tutorial]: iot-hub-node-node-direct-methods.md
[lnk-twin-how-to-configure]: iot-hub-csharp-node-twin-how-to-configure.md

[lnk-dev-setup]: https://github.com/Azure/azure-iot-sdk-node/blob/master/doc/node-devbox-setup.md

