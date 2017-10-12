---
title: Uso de las propiedades de dispositivos gemelos de IoT Hub de Azure (.NET y Node) | Microsoft Docs
description: "En este artículo se describe cómo usar los dispositivos gemelos de IoT Hub de Azure para configurar dispositivos. Usará el SDK de dispositivo IoT de Azure para Node.js con el fin de implementar una aplicación de dispositivo simulado, además del SDK de servicio IoT de Azure para .NET con el objetivo de implementar una aplicación de servicio que modifica una configuración de dispositivo que emplea un dispositivo gemelo."
services: iot-hub
documentationcenter: .net
author: fsautomata
manager: timlt
editor: 
ms.assetid: 3c627476-f982-43c9-bd17-e0698c5d236d
ms.service: iot-hub
ms.devlang: node
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/30/2017
ms.author: elioda
ms.openlocfilehash: 8f9626fc47e7d32cb104b960bea9b7de9efa6f3e
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="use-desired-properties-to-configure-devices"></a>Uso de las propiedades deseadas para configurar dispositivos
[!INCLUDE [iot-hub-selector-twin-how-to-configure](../../includes/iot-hub-selector-twin-how-to-configure.md)]

Al final de este tutorial tendrá dos aplicaciones de consola:

* **SimulateDeviceConfiguration.js**, una aplicación de dispositivo simulado que espera por una actualización de la configuración deseada y notifica el estado de un proceso de actualización de configuración simulada.
* **SetDesiredConfigurationAndQuery**, una aplicación .NET diseñada para ejecutarse desde el back-end, que establece la configuración deseada en un dispositivo y consulta el proceso de actualización de la configuración.

> [!NOTE]
> En el artículo [SDK de IoT de Azure][lnk-hub-sdks] se proporciona información sobre los SDK que puede usar para crear aplicaciones de dispositivo y de back-end.
> 
> 

Para completar este tutorial, necesitará lo siguiente:

* Visual Studio 2015 o Visual Studio 2017.
* Node.js, versión 4.0.x o posterior.
* Una cuenta de Azure activa. En caso de no tener ninguna, puede crear una [cuenta gratuita][lnk-free-trial] en tan solo unos minutos.

Si ha seguido el tutorial [Introducción a los dispositivos gemelos][lnk-twin-tutorial], ya tiene una instancia de IoT Hub y una identidad de dispositivo denominada **myDeviceId**. En ese caso, puede ir directamente a la sección [Creación de la aplicación de dispositivo simulado][lnk-how-to-configure-createapp].

[!INCLUDE [iot-hub-get-started-create-hub](../../includes/iot-hub-get-started-create-hub.md)]

[!INCLUDE [iot-hub-get-started-create-device-identity](../../includes/iot-hub-get-started-create-device-identity.md)]

<a id="#create-the-simulated-device-app"></a>
## <a name="create-the-simulated-device-app"></a>Creación de la aplicación de dispositivo simulado
En esta sección, creará una aplicación de consola de Node.js que se conecta a su centro como **myDeviceId**, espera por una actualización de la configuración deseada y, a continuación, informa de las actualizaciones en el proceso de actualización de configuración simulada.

1. Cree una nueva carpeta vacía denominada **simulatedeviceconfiguration**. En la carpeta **simulatedeviceconfiguration** , cree un nuevo archivo package.json con el siguiente comando en el símbolo del sistema. Acepte todos los valores predeterminados.
   
    ```
    npm init
    ```
1. En el símbolo del sistema, en la carpeta **simulatedeviceconfiguration**, ejecute el siguiente comando para instalar **azure-iot-device** y los paquetes **azure-iot-device-mqtt**:
   
    ```
    npm install azure-iot-device azure-iot-device-mqtt --save
    ```
1. Con un editor de texto, cree un nuevo archivo **SimulateDeviceConfiguration.js** en la carpeta **simulatedeviceconfiguration**.
1. Agregue el código siguiente al archivo **SimulateDeviceConfiguration.js** y sustituya el marcador de posición **{device connection string}** con la cadena de conexión del dispositivo que copió al crear la identidad de dispositivo **myDeviceId**:
   
        'use strict';
        var Client = require('azure-iot-device').Client;
        var Protocol = require('azure-iot-device-mqtt').Mqtt;
   
        var connectionString = '{device connection string}';
        var client = Client.fromConnectionString(connectionString, Protocol);
   
        client.open(function(err) {
            if (err) {
                console.error('could not open IotHub client');
            } else {
                client.getTwin(function(err, twin) {
                    if (err) {
                        console.error('could not get twin');
                    } else {
                        console.log('retrieved device twin');
                        twin.properties.reported.telemetryConfig = {
                            configId: "0",
                            sendFrequency: "24h"
                        }
                        twin.on('properties.desired', function(desiredChange) {
                            console.log("received change: "+JSON.stringify(desiredChange));
                            var currentTelemetryConfig = twin.properties.reported.telemetryConfig;
                            if (desiredChange.telemetryConfig &&desiredChange.telemetryConfig.configId !== currentTelemetryConfig.configId) {
                                initConfigChange(twin);
                            }
                        });
                    }
                });
            }
        });
   
    El objeto **Cliente** expone todos los métodos necesarios para interactuar con dispositivos gemelos del dispositivo. Este código, inicializa el objeto **Cliente**, recupera el dispositivo gemelo de **myDeviceId** y, posteriormente, adjunta un controlador para la actualización en las *propiedades deseadas*. El controlador comprueba que hay una solicitud real de cambio de la configuración comparando los configId, y a continuación, invoca un método que inicia el cambio de configuración.
   
    Tenga en cuenta que, por simplicidad, este código usa un valor predeterminado codificado de forma rígida para la configuración inicial. Una aplicación real probablemente cargaría esa configuración desde un almacenamiento local.
   
   > [!IMPORTANT]
   > Los eventos de cambio de propiedad deseados siempre se emiten una vez en la conexión de dispositivo. Asegúrese de comprobar que hay un cambio real en las propiedades deseadas antes de realizar cualquier acción.
   > 
   > 
1. Agregue los métodos siguientes antes de la invocación `client.open()`:
   
        var initConfigChange = function(twin) {
            var currentTelemetryConfig = twin.properties.reported.telemetryConfig;
            currentTelemetryConfig.pendingConfig = twin.properties.desired.telemetryConfig;
            currentTelemetryConfig.status = "Pending";
   
            var patch = {
            telemetryConfig: currentTelemetryConfig
            };
            twin.properties.reported.update(patch, function(err) {
                if (err) {
                    console.log('Could not report properties');
                } else {
                    console.log('Reported pending config change: ' + JSON.stringify(patch));
                    setTimeout(function() {completeConfigChange(twin);}, 60000);
                }
            });
        }
   
        var completeConfigChange =  function(twin) {
            var currentTelemetryConfig = twin.properties.reported.telemetryConfig;
            currentTelemetryConfig.configId = currentTelemetryConfig.pendingConfig.configId;
            currentTelemetryConfig.sendFrequency = currentTelemetryConfig.pendingConfig.sendFrequency;
            currentTelemetryConfig.status = "Success";
            delete currentTelemetryConfig.pendingConfig;
   
            var patch = {
                telemetryConfig: currentTelemetryConfig
            };
            patch.telemetryConfig.pendingConfig = null;
   
            twin.properties.reported.update(patch, function(err) {
                if (err) {
                    console.error('Error reporting properties: ' + err);
                } else {
                    console.log('Reported completed config change: ' + JSON.stringify(patch));
                }
            });
        };
   
    El método **initConfigChange** actualiza propiedades notificadas en el objeto del dispositivo gemelo local con la solicitud de actualización de la configuración y establece el estado en **Pendiente**, a continuación, actualiza el dispositivo gemelo en el servicio. Después de actualizar correctamente el dispositivo gemelo, simula un proceso de ejecución prolongada que finaliza en la ejecución de **completeConfigChange**. Este método actualiza las propiedades notificadas locales estableciendo el estado en **Correcto** y eliminado el objeto **pendingConfig**. A continuación, actualiza el dispositivo gemelo en el servicio.
   
    Tenga en cuenta que, para ahorrar ancho de banda, las propiedades notificadas se actualizan especificando solo las propiedades que se van a modificar (denominadas **revisión** en el código anterior), en lugar de reemplazar el documento completo.
   
   > [!NOTE]
   > Este tutorial no simula ningún comportamiento de las actualizaciones de configuración simultáneas. Algunos procesos de actualización de configuración pueden dar cabida a cambios de configuración de destino mientras se está ejecutando la actualización, otros tiene que ponerlos en cola, y otros podría rechazarlos con una condición de error. Asegúrese de que considera el comportamiento deseado para el proceso de configuración específica y agregue la lógica adecuada antes de iniciar el cambio de configuración.
   > 
   > 
1. Ejecute la aplicación del dispositivo:
   
        node SimulateDeviceConfiguration.js
   
    Verá el mensaje `retrieved device twin`. Mantenga la aplicación en ejecución.

## <a name="create-the-service-app"></a>Creación de la aplicación de servicio
En esta sección, creará una aplicación de consola .NET que actualiza las *propiedades deseadas* en el dispositivo gemelo asociado con **myDeviceId** con un nuevo objeto de configuración de telemetría. A continuación, consulta los dispositivos gemelos almacenados en IoT Hub y muestra la diferencia entre las configuraciones deseada y notificada del dispositivo.

1. En Visual Studio, agregue un proyecto de escritorio clásico de Windows de Visual C# a la solución actual mediante la plantilla de proyecto **Aplicación de consola** . Asigne el nombre **SetDesiredConfigurationAndQuery** al proyecto.
   
    ![Nuevo proyecto de escritorio clásico de Windows de Visual C#][img-createapp]
1. En el Explorador de soluciones, haga clic con el botón derecho en el proyecto **SetDesiredConfigurationAndQuery** y luego seleccione **Administrar paquetes NuGet...**.
1. En la ventana **Administrador de paquetes NuGet**, seleccione **Examinar**, busque **microsoft.azure.devices**, seleccione **Instalar** para instalar el paquete **Microsoft.Azure.Devices** y acepte los términos de uso. Este procedimiento permite descargar, instalar y agregar una referencia al [paquete NuGet del SDK de dispositivo de IoT de Azure][lnk-nuget-service-sdk] y sus dependencias.
   
    ![Ventana del Administrador de paquetes NuGet][img-servicenuget]
1. Agregue las siguientes instrucciones `using` al principio del archivo **Program.cs** :
   
        using Microsoft.Azure.Devices;
        using System.Threading;
        using Newtonsoft.Json;
1. Agregue los campos siguientes a la clase **Program** . Sustituya el valor de marcador de posición por la cadena de conexión de IoT Hub para el centro que creó en la sección anterior.
   
        static RegistryManager registryManager;
        static string connectionString = "{iot hub connection string}";
1. Agregue el método siguiente a la clase **Program** :
   
        static private async Task SetDesiredConfigurationAndQuery()
        {
            var twin = await registryManager.GetTwinAsync("myDeviceId");
            var patch = new {
                    properties = new {
                        desired = new {
                            telemetryConfig = new {
                                configId = Guid.NewGuid().ToString(),
                                sendFrequency = "5m"
                            }
                        }
                    }
                };
   
            await registryManager.UpdateTwinAsync(twin.DeviceId, JsonConvert.SerializeObject(patch), twin.ETag);
            Console.WriteLine("Updated desired configuration");
   
            try
            {
                while (true)
                {
                    var query = registryManager.CreateQuery("SELECT * FROM devices WHERE deviceId = 'myDeviceId'");
                    var results = await query.GetNextAsTwinAsync();
                    foreach (var result in results)
                    {
                        Console.WriteLine("Config report for: {0}", result.DeviceId);
                        Console.WriteLine("Desired telemetryConfig: {0}", JsonConvert.SerializeObject(result.Properties.Desired["telemetryConfig"], Formatting.Indented));
                        Console.WriteLine("Reported telemetryConfig: {0}", JsonConvert.SerializeObject(result.Properties.Reported["telemetryConfig"], Formatting.Indented));
                        Console.WriteLine();
                    }
                    Thread.Sleep(10000);
                }
            }
            catch (Exception ex)
            {
                Console.WriteLine($"Exception: {ex.Message}");
            }
        }
   
    El objeto **Registro** expone todos los métodos necesarios para interactuar con dispositivos gemelos del servicio. Este código inicializa el objeto **Registro**, recupera el dispositivo gemelo de **myDeviceId** y actualiza sus propiedades deseadas con un nuevo objeto de configuración de telemetría.
    Después, cada 10 segundos, consulta los dispositivos gemelos almacenados en IoT Hub e imprime las configuraciones de telemetría deseadas y notificadas. Consulte el [lenguaje de consulta de IoT Hub][lnk-query] para obtener información sobre cómo generar informes completos en todos los dispositivos.
   
   > [!IMPORTANT]
   > Esta aplicación consulta IoT Hub cada 10 segundos con fines ilustrativos. Use consultas para generar informes de cara al usuario en muchos dispositivos y no para detectar cambios. Si la solución requiere notificaciones en tiempo real de eventos de dispositivo, use [notificaciones gemelas][lnk-twin-notifications].
   > 
   > 
1. Por último, agregue las líneas siguientes al método **Main** :
   
        registryManager = RegistryManager.CreateFromConnectionString(connectionString);
        SetDesiredConfigurationAndQuery().Wait();
        Console.WriteLine("Press any key to quit.");
        Console.ReadLine();
1. En el Explorador de soluciones, abra **Establecer proyectos de inicio...** y asegúrese de que la **acción** de **SetDesiredConfigurationAndQuery** sea **Iniciar**. Compile la solución.
1. Con **SimulateDeviceConfiguration.js** ejecutándose, ejecute la aplicación .NET desde Visual Studio mediante **F5** y debería ver cómo la configuración notificada cambia de **Correcto** a **Pendiente** a **Correcto** de nuevo con la nueva frecuencia de envío activa de cinco minutos en lugar de 24 horas.

 ![Dispositivo configurado correctamente][img-deviceconfigured]
   
   > [!IMPORTANT]
   > Hay un retraso de hasta un minuto entre la operación de informe de dispositivo y el resultado de la consulta. Esto es para habilitar la infraestructura de consulta para que funcione a gran escala. Para recuperar vistas coherentes de un único dispositivo gemelo, use el método **getDeviceTwin** en la clase **Registro**.
   > 
   > 

## <a name="next-steps"></a>Pasos siguientes
En este tutorial, ha establecido una configuración deseada como *propiedades deseadas* desde el back-end de solución, y ha escrito una aplicación de dispositivo para detectar ese cambio y simular un proceso de actualización de varios pasos que informe de su estado mediante las propiedades notificadas.

Use los siguientes recursos para obtener información sobre cómo:

* enviar telemetría desde dispositivos con el tutorial [Introducción a IoT Hub][lnk-iothub-getstarted];
* programar o realizar operaciones en grandes conjuntos de dispositivos (consulte el tutorial [Schedule and broadcast jobs][lnk-schedule-jobs] [Programación y difusión de trabajos]);
* controlar los dispositivos de forma interactiva (por ejemplo, encender un ventilador desde una aplicación controlada por el usuario), con el tutorial [Uso de métodos directos][lnk-methods-tutorial].

<!-- images -->
[img-servicenuget]: media/iot-hub-csharp-node-twin-how-to-configure/servicesdknuget.png
[img-createapp]: media/iot-hub-csharp-node-twin-how-to-configure/createnetapp.png
[img-deviceconfigured]: media/iot-hub-csharp-node-twin-how-to-configure/deviceconfigured.png

<!-- links -->
[lnk-hub-sdks]: iot-hub-devguide-sdks.md
[lnk-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[lnk-nuget-service-sdk]: https://www.nuget.org/packages/Microsoft.Azure.Devices/1.1.0/

[lnk-devguide-jobs]: iot-hub-devguide-jobs.md
[lnk-query]: iot-hub-devguide-query-language.md
[lnk-twin-notifications]: iot-hub-devguide-device-twins.md#back-end-operations
[lnk-methods]: iot-hub-devguide-direct-methods.md
[lnk-dm-overview]: iot-hub-device-management-overview.md
[lnk-twin-tutorial]: iot-hub-node-node-twin-getstarted.md
[lnk-schedule-jobs]: iot-hub-node-node-schedule-jobs.md
[lnk-dev-setup]: https://github.com/Azure/azure-iot-sdk-node/blob/master/doc/node-devbox-setup.md
[lnk-connect-device]: https://azure.microsoft.com/develop/iot/
[lnk-device-management]: iot-hub-node-node-device-management-get-started.md
[lnk-iothub-getstarted]: iot-hub-node-node-getstarted.md
[lnk-methods-tutorial]: iot-hub-node-node-direct-methods.md

[lnk-guid]: https://en.wikipedia.org/wiki/Globally_unique_identifier

[lnk-how-to-configure-createapp]: iot-hub-csharp-node-twin-how-to-configure.md#create-the-simulated-device-app
