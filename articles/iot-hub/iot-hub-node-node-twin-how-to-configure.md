<properties
    pageTitle="Uso de las propiedades gemelas | Microsoft Azure"
    description="En este tutorial se muestra cómo usar las propiedades gemelas"
    services="iot-hub"
    documentationCenter=".net"
    authors="fsautomata"
    manager="timlt"
    editor=""/>

<tags
     ms.service="iot-hub"
     ms.devlang="node"
     ms.topic="article"
     ms.tgt_pltfrm="na"
     ms.workload="na"
     ms.date="09/13/2016"
     ms.author="elioda"/>


# <a name="tutorial:-use-desired-properties-to-configure-devices-(preview)"></a>Tutorial: Uso de las propiedades deseadas para configurar dispositivos (versión preliminar)

[AZURE.INCLUDE [iot-hub-selector-twin-how-to-configure](../../includes/iot-hub-selector-twin-how-to-configure.md)]

Al final de este tutorial tendrá dos aplicaciones de consola de Node.js:

* **SimulateDeviceConfiguration.js**, una aplicación de dispositivo simulado que espera por una actualización de la configuración deseada y notifica el estado de un proceso de actualización de configuración simulada.
* **SetDesiredConfigurationAndQuery.js**, una aplicación Node.js diseñada para ejecutarse desde el back-end, que establece la configuración deseada en un dispositivo y consulta el proceso de actualización de la configuración.

> [AZURE.NOTE] El artículo ///[IoT Hub SDKs][lnk-hub-sdks] (Los SDK de IoT Hub) proporciona información acerca de los SDK que puede usar para crear el dispositivo y las aplicaciones back-end.

Para completar este tutorial, necesitará lo siguiente:

+ Node.js versión 0.10.x o posteriores.

+ Una cuenta de Azure activa. En caso de no tener ninguna, puede crear una cuenta de evaluación gratuita en tan solo unos minutos. Para más información, consulte la [prueba gratuita de Azure][lnk-free-trial]).

Si ha seguido el tutorial [Introducción a los dispositivos gemelos (vista preliminar)][Ink-twin-tutorial], ya tiene un centro habilitado de administración de dispositivos y una identidad de dispositivo denominada **myDeviceId**; y puede ir directamente a la sección [Creación de la aplicación de dispositivo simulado][Ink-how-to-configure-createapp] .

[AZURE.INCLUDE [iot-hub-get-started-create-hub-pp](../../includes/iot-hub-get-started-create-hub-pp.md)]

## <a name="create-the-simulated-device-app"></a>Creación de la aplicación de dispositivo simulado

En esta sección, creará una aplicación de consola de Node.js que se conecta a su centro como **myDeviceId**, espera por una actualización de la configuración deseada y, a continuación, informa de las actualizaciones en el proceso de actualización de configuración simulada.

1. Cree una nueva carpeta vacía denominada **simulatedeviceconfiguration**. En la carpeta **simulatedeviceconfiguration** , cree un nuevo archivo package.json con el siguiente comando en el símbolo del sistema. Acepte todos los valores predeterminados:

    ```
    npm init
    ```

2. En el símbolo del sistema, en la carpeta **simulatedeviceconfiguration**, ejecute el siguiente comando para instalar **azure-iot-device** y el paquete **azure-iot-device-mqtt**:

    ```
    npm install azure-iot-device@dtpreview azure-iot-device-mqtt@dtpreview --save
    ```

3. Con un editor de texto, cree un nuevo archivo **SimulateDeviceConfiguration.js** en la carpeta **simulatedeviceconfiguration**.

4. Agregue el código siguiente al archivo **SimulateDeviceConfiguration.js** y sustituya el marcador de posición **{device connection string}** con la cadena de conexión que copió al crear la identidad de dispositivo **myDeviceId**:

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

    El objeto **Cliente** expone todos los métodos necesarios para interactuar con dispositivos gemelos del dispositivo. El código anterior, una vez que inicialice el objeto **Cliente**, recupera el gemelo de **myDeviceId**, y adjunta un controlador para la actualización en las propiedades deseadas. El controlador comprueba que hay una solicitud real de cambio de la configuración comparando los configId, y a continuación, invoca un método que inicia el cambio de configuración.

    Tenga en cuenta que, por simplicidad, el código anterior usa un valor predeterminado codificado de forma rígida para la configuración inicial. Una aplicación real probablemente cargaría esa configuración desde un almacenamiento local.
    
    > [AZURE.IMPORTANT] Los eventos de cambio de propiedad deseados siempre se emiten una vez en la conexión del dispositivo, no olvide comprobar que hay un cambio real en las propiedades deseadas antes de realizar cualquier acción.

5. Agregue los métodos siguientes antes de la invocación `client.open()`:

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

    El método **initConfigChange** actualiza propiedades notificadas en el objeto gemelo local con la solicitud de actualización de la configuración y establece el estado en **Pendiente**, a continuación, actualiza el dispositivo gemelo en el servicio. Después de actualizar correctamente el gemelo, simula un proceso de ejecución prolongada que finaliza en la ejecución de **completeConfigChange**. Este método actualiza las propiedades notificadas del gemelo local estableciendo el estado en **Correcto** y eliminado el objeto **pendingConfig**. A continuación, actualiza al gemelo en el servicio.

    Tenga en cuenta que, para ahorrar ancho de banda, las propiedades notificadas se actualizan especificando solo las propiedades que se van a modificar (denominadas **revisión** en el código anterior), en lugar de reemplazar el documento completo.

    > [AZURE.NOTE] Este tutorial no simula ningún comportamiento de las actualizaciones de configuración simultáneas. Algunos procesos de actualización de configuración pueden dar cabida a cambios de configuración de destino mientras se está ejecutando la actualización, otros tienen que ponerlos en cola, y otros podrían rechazarlos con una condición de error. Asegúrese de que considera el comportamiento deseado para el proceso de configuración específica y agregue la lógica adecuada antes de iniciar el cambio de configuración.

6. Ejecute la aplicación del dispositivo:

        node SimulateDeviceConfiguration.js

    Verá el mensaje `retrieved device twin`. Mantenga la aplicación en ejecución.

## <a name="create-the-service-app"></a>Creación de la aplicación de servicio

En esta sección, creará una aplicación de consola de Node.js que actualiza las *propiedades deseadas* en el gemelo asociado con **myDeviceId** con un nuevo objeto de configuración de telemetría. A continuación, consulta a los dispositivos gemelos almacenados en el centro y muestra la diferencia entre las configuraciones deseada y notificada del dispositivo.

1. Cree una nueva carpeta vacía denominada **setdesiredandqueryapp**. En la carpeta **setdesiredandqueryapp** , cree un nuevo archivo package.json con el siguiente comando en el símbolo del sistema. Acepte todos los valores predeterminados:

    ```
    npm init
    ```

2. En el símbolo del sistema, en la carpeta **setdesiredandqueryapp**, ejecute el siguiente comando para instalar el paquete **azure-iothub**:

    ```
    npm install azure-iothub@dtpreview node-uuid --save
    ```

3. Con un editor de texto, cree un nuevo archivo **SetDesiredAndQuery.js** en la carpeta **addtagsandqueryapp**.

4. Agregue el código siguiente al archivo **SetDesiredAndQuery.js** y sustituya el marcador de posición **{service connection string}** con la cadena de conexión que copió al crear su centro:

        'use strict';
        var iothub = require('azure-iothub');
        var uuid = require('node-uuid');
        var connectionString = '{service connection string}';
        var registry = iothub.Registry.fromConnectionString(connectionString);
         
        registry.getTwin('myDeviceId', function(err, twin){
            if (err) {
                console.error(err.constructor.name + ': ' + err.message);
            } else {
                var newConfigId = uuid.v4();
                var newFrequency = process.argv[2] || "5m";
                var patch = {
                    properties: {
                        desired: {
                            telemetryConfig: {
                                configId: newConfigId,
                                sendFrequency: newFrequency
                            }
                        }
                    }
                }
                twin.update(patch, function(err) {
                    if (err) {
                        console.error('Could not update twin: ' + err.constructor.name + ': ' + err.message);
                    } else {
                        console.log(twin.deviceId + ' twin updated successfully');
                    }
                });
                setInterval(queryTwins, 10000);
            }
        });
            

    El objeto **Registro** expone todos los métodos necesarios para interactuar con dispositivos gemelos del servicio. El código anterior, una vez que inicialice el objeto **Registro**, recupera el gemelo de **myDeviceId**, y actualiza sus propiedades deseadas con un nuevo objeto de configuración de telemetría. Después de esto, llama a la función **queryTwins** cada 10 segundos.

    > [AZURE.IMPORTANT] Esta aplicación consulta IoT Hub cada 10 segundos con fines ilustrativos. Use consultas para generar informes de cara al usuario en muchos dispositivos y no para detectar cambios. Si la solución requiere notificaciones en tiempo real de eventos de dispositivo use [device-to-cloud messages][lnk d2c].

5. Agregue el siguiente código justo antes de la invocación `registry.getDeviceTwin()` para implementar la función **queryTwins**:

        var queryTwins = function() {
            var query = registry.createQuery("SELECT * FROM devices WHERE deviceId = 'myDeviceId'", 100);
            query.nextAsTwin(function(err, results) {
                if (err) {
                    console.error('Failed to fetch the results: ' + err.message);
                } else {
                    console.log();
                    results.forEach(function(twin) {
                        var desiredConfig = twin.properties.desired.telemetryConfig;
                        var reportedConfig = twin.properties.reported.telemetryConfig;
                        console.log("Config report for: " + twin.deviceId);
                        console.log("Desired: ");
                        console.log(JSON.stringify(desiredConfig, null, 2));
                        console.log("Reported: ");
                        console.log(JSON.stringify(reportedConfig, null, 2));
                    });
                }
            });
        };

    El código anterior consulta los gemelos almacenados en el centro e imprime las configuraciones de telemetría deseadas y notificadas. Consulte el [lenguaje de consulta de IoT Hub][consulta lnk] para obtener información sobre cómo generar informes completos en todos los dispositivos.


8. Con **SimulateDeviceConfiguration.js** en ejecución, ejecute la aplicación con:

        node SetDesiredAndQuery.js 5m

    Debería ver como la configuración notificada cambia de **Correcto** a **Pendiente** a **Correcto** de nuevo con la nueva frecuencia de envío activa de cinco minutos en lugar de 24 horas.

    > [AZURE.IMPORTANT] Hay un retraso de hasta un minuto entre la operación de informe de dispositivo y el resultado de la consulta. Esto es para habilitar la infraestructura de consulta para que funcione a gran escala. Para recuperar vistas coherentes de un único gemelo use el método **getDeviceTwin** en la clase **Registro**.

## <a name="next-steps"></a>Pasos siguientes

En este tutorial, ha establecido una configuración deseada como *propiedades deseadas* desde una aplicación back-end, y ha escrito una aplicación de dispositivo simulado para detectar ese cambio y simular un proceso de actualización de varios pasos que informe de su estado como *propiedades notificadas* para el gemelo.

Use los siguientes recursos para obtener información sobre cómo:

- enviar telemetría desde dispositivos con el tutorial [Introducción a Azure IoT Hub para Node.js][lnk-iothub-getstarted],
- programar o realizar operaciones en grandes conjuntos de dispositivos, consulte el tutorial ///[Use jobs to schedule and broadcast device operations][lnk-schedule-jobs] (Uso de trabajos para programar y difundir operaciones de dispositivo).
- controlar los dispositivos de forma interactiva (por ejemplo, encender un ventilador desde una aplicación controlada por el usuario), con el tutorial ///[Use direct methods][lnk-methods-tutorial] (Uso de métodos directos).


<!-- links -->
[lnk-hub-sdks]: iot-hub-devguide-sdks.md
[lnk-free-trial]: http://azure.microsoft.com/pricing/free-trial/

[lnk-devguide-jobs]: iot-hub-devguide-jobs.md
[lnk-query]: iot-hub-devguide-query-language.md
[lnk-d2c]: iot-hub-devguide-messaging.md#device-to-cloud-messages
[lnk-methods]: iot-hub-devguide-direct-methods.md
[lnk-dm-overview]: iot-hub-device-management-overview.md
[lnk-twin-tutorial]: iot-hub-node-node-twin-getstarted.md
[lnk-schedule-jobs]: iot-hub-schedule-jobs.md
[lnk-dev-setup]: https://github.com/Azure/azure-iot-sdks/blob/master/doc/get_started/node-devbox-setup.md
[lnk-connect-device]: https://azure.microsoft.com/develop/iot/
[lnk-device-management]: iot-hub-device-management-get-started.md
[lnk-gateway-SDK]: iot-hub-linux-gateway-sdk-get-started.md
[lnk-iothub-getstarted]: iot-hub-node-node-getstarted.md
[lnk-methods-tutorial]: iot-hub-c2d-methods.md

[lnk-guid]: https://en.wikipedia.org/wiki/Globally_unique_identifier

[lnk-how-to-configure-createapp]: iot-hub-node-node-twin-how-to-configure.md#create-the-simulated-device-app



<!--HONumber=Oct16_HO2-->


