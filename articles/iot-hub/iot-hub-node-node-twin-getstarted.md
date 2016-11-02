<properties
    pageTitle="Introducción a los gemelos | Microsoft Azure"
    description="En este tutorial se muestra cómo usar los gemelos"
    services="iot-hub"
    documentationCenter="node"
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


# <a name="tutorial:-get-started-with-device-twins-(preview)"></a>Tutorial: Introducción a los dispositivos gemelos (versión preliminar)

[AZURE.INCLUDE [iot-hub-selector-twin-get-started](../../includes/iot-hub-selector-twin-get-started.md)]

Al final de este tutorial tendrá dos aplicaciones de consola de Node.js:

* **AddTagsAndQuery.js**, una aplicación Node.js diseñada para ejecutarse desde el back-end, que agrega etiquetas y consulta dispositivos gemelos.
* **TwinSimulatedDevice.js**, una aplicación de Node.js que simula un dispositivo que se conecta a su centro de IoT con la identidad del dispositivo creada anteriormente e informa de su estado de conectividad.

> [AZURE.NOTE] El artículo ///[IoT Hub SDKs][lnk-hub-sdks] (Los SDK de IoT Hub) proporciona información acerca de los SDK que puede usar para crear el dispositivo y las aplicaciones back-end.

Para completar este tutorial, necesitará lo siguiente:

+ Node.js versión 0.10.x o posteriores.

+ Una cuenta de Azure activa. En caso de no tener ninguna, puede crear una cuenta de evaluación gratuita en tan solo unos minutos. Para más información, consulte la [prueba gratuita de Azure][lnk-free-trial]).

[AZURE.INCLUDE [iot-hub-get-started-create-hub-pp](../../includes/iot-hub-get-started-create-hub-pp.md)]

## <a name="create-the-service-app"></a>Creación de la aplicación de servicio

En esta sección, creará una aplicación de consola de Node.js que agrega metadatos de ubicación al gemelo asociado con **myDeviceId**. A continuación, consulta los gemelos almacenados en el centro seleccionando los dispositivos ubicados en Estados Unidos y, a continuación los que informan de una conexión de red de telefonía móvil.

1. Cree una nueva carpeta vacía denominada **addtagsandqueryapp**. En la carpeta **addtagsandqueryapp** , cree un nuevo archivo package.json con el siguiente comando en el símbolo del sistema. Acepte todos los valores predeterminados:

    ```
    npm init
    ```

2. En el símbolo del sistema, en la carpeta **addtagsandqueryapp**, ejecute el siguiente comando para instalar el paquete **azure-iothub**:

    ```
    npm install azure-iothub@dtpreview --save
    ```

3. Con un editor de texto, cree un nuevo archivo **AddTagsAndQuery.js** en la carpeta **addtagsandqueryapp**.

4. Agregue el código siguiente al archivo **AddTagsAndQuery.js** y sustituya el marcador de posición **{service connection string}** con la cadena de conexión que copió al crear su centro:

        'use strict';
        var iothub = require('azure-iothub');
        var connectionString = '{service hub connection string}';
        var registry = iothub.Registry.fromConnectionString(connectionString);

        registry.getTwin('myDeviceId', function(err, twin){
            if (err) {
                console.error(err.constructor.name + ': ' + err.message);
            } else {
                var patch = {
                    tags: {
                        location: {
                            region: 'US',
                            plant: 'Redmond43'
                      }
                    }
                };
             
                twin.update(patch, function(err) {
                  if (err) {
                    console.error('Could not update twin: ' + err.constructor.name + ': ' + err.message);
                  } else {
                    console.log(twin.deviceId + ' twin updated successfully');
                    queryTwins();
                  }
                });
            }
        });

    El objeto **Registro** expone todos los métodos necesarios para interactuar con dispositivos gemelos del servicio. El código anterior inicializa primero el objeto **Registro**, a continuación, recupera el gemelo de **myDeviceId**, y por último actualiza sus etiquetas con la información de la ubicación deseada.

    Después de actualizar las etiquetas, llama a la función **queryTwins**.

7. Agregue el código siguiente al final de  **AddTagsAndQuery.js** para implementar la función **queryTwins**:

        var queryTwins = function() {
            var query = registry.createQuery("SELECT * FROM devices WHERE tags.location.plant = 'Redmond43'", 100);
            query.nextAsTwin(function(err, results) {
                if (err) {
                    console.error('Failed to fetch the results: ' + err.message);
                } else {
                    console.log("Devices in Redmond43: " + results.map(function(twin) {return twin.deviceId}).join(','));
                }
            });
            
            query = registry.createQuery("SELECT * FROM devices WHERE tags.location.plant = 'Redmond43' AND properties.reported.connectivity.type = 'cellular'", 100);
            query.nextAsTwin(function(err, results) {
                if (err) {
                    console.error('Failed to fetch the results: ' + err.message);
                } else {
                    console.log("Devices in Redmond43 using cellular network: " + results.map(function(twin) {return twin.deviceId}).join(','));
                }
            });
        };

    El código anterior ejecuta dos consultas: la primera selecciona solo los dispositivos gemelos que se encuentran en la planta **Redmond43**, y la segunda mejora la consulta para seleccionar solo los dispositivos que están también conectados a través de la red de telefonía móvil.

    Tenga en cuenta que el código anterior, cuando crea el objeto de **consulta**, especifica un número máximo de documentos devueltos. El objeto **consulta** contiene una propiedad booleana **hasMoreResults** que puede utilizar para invocar a los métodos **nextAsTwin** varias veces para recuperar todos los resultados. Un método llamado **siguiente** está disponible para los resultados que no son dispositivos gemelos, por ejemplo, los resultados de consultas de agregación.

8. Ejecute la aplicación con:

        node AddTagsAndQuery.js

    Debería ver un dispositivo en los resultados de la consulta que pregunta por todos los dispositivos que se encuentran en **Redmond43** y ninguno para la consulta que restringe los resultados a los dispositivos que utilizan una red de telefonía móvil.

    ![][1]

En la siguiente sección, creará una aplicación de dispositivo que notifica la información de conectividad y cambia el resultado de la consulta en la sección anterior.

## <a name="create-the-device-app"></a>Creación de la aplicación del dispositivo

En esta sección, creará una aplicación de consola de Node.js que se conecta al centro como **myDeviceId**, y luego actualiza las propiedades notificadas de su gemelo para contener la información que está conectada mediante una red de telefonía móvil.

> [AZURE.NOTE] En la actualidad, solo se puede acceder a los dispositivos gemelos desde dispositivos conectados a IoT Hub mediante el protocolo MQTT. Para instrucciones acerca de cómo convertir la aplicación de dispositivo existente para usar MQTT, consulte el artículo sobre [compatibilidad con MQTT][lnk-devguide-mqtt].

1. Cree una nueva carpeta vacía denominada **reportconnectivity**. En la carpeta **reportconnectivity** , cree un nuevo archivo package.json con el siguiente comando en el símbolo del sistema. Acepte todos los valores predeterminados:

    ```
    npm init
    ```

2. En el símbolo del sistema, en la carpeta **reportconnectivity**, ejecute el siguiente comando para instalar **azure-iot-device** y el paquete **azure-iot-device-mqtt**:

    ```
    npm install azure-iot-device@dtpreview azure-iot-device-mqtt@dtpreview --save
    ```

3. Con un editor de texto, cree un nuevo archivo **ReportConnectivity.js** en la carpeta **reportconnectivity**.

4. Agregue el código siguiente al archivo **ReportConnectivity.js** y sustituya el marcador de posición **{device connection string}** con la cadena de conexión que copió al crear la identidad de dispositivo **myDeviceId**:

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

    El objeto **Cliente** expone todos los métodos necesarios para interactuar con dispositivos gemelos del dispositivo. El código anterior, una vez que inicialice el objeto **Cliente**, recupera el gemelo de **myDeviceId**, y actualiza su propiedad notificada con la información de conectividad.

5. Ejecute la aplicación del dispositivo

        node ReportConnectivity.js

    Verá el mensaje `twin state reported`.

6. Ahora que el dispositivo ha informado sobre su información de conectividad, debe aparecer en ambas consultas. Vuelva a la carpeta **addtagsandqueryapp** y vuelva a ejecutar las consultas:

        node AddTagsAndQuery.js

    Esta vez **myDeviceId** debe aparecer en los resultados de ambas consulta.

    ![][3]

## <a name="next-steps"></a>Pasos siguientes
En este tutorial, configuró un nuevo Centro de IoT en el portal y después creó una identidad de dispositivo en el registro de identidades del centro. Ha agregado metadatos de dispositivo como etiquetas desde una aplicación de back-end y ha escrito una aplicación de dispositivo simulado para notificar la información de conectividad de dispositivos en el dispositivo gemelo. También ha aprendido cómo consultar esta información usando el lenguaje de consulta de tipo SQL de IoT Hub.

Use los siguientes recursos para obtener información sobre cómo:

- enviar telemetría desde dispositivos con el tutorial [Introducción a Azure IoT Hub para Node.js][lnk-iothub-getstarted],
- configurar dispositivos mediante las propiedades deseadas del gemelo con el tutorial [Uso de las propiedades deseadas para configurar dispositivos (versión preliminar)][lnk-twin-how-to-configure],
- controlar los dispositivos de forma interactiva (por ejemplo, encender un ventilador desde una aplicación controlada por el usuario), con el tutorial ///[Use direct methods][lnk-methods-tutorial] (Uso de métodos directos).

<!-- images -->
[1]: media/iot-hub-node-node-twin-getstarted/service1.png
[3]: media/iot-hub-node-node-twin-getstarted/service2.png

<!-- links -->
[lnk-hub-sdks]: iot-hub-devguide-sdks.md
[lnk-free-trial]: http://azure.microsoft.com/pricing/free-trial/

[lnk-d2c]: iot-hub-devguide-messaging.md#device-to-cloud-messages
[lnk-methods]: iot-hub-devguide-direct-methods.md
[lnk-twins]: iot-hub-devguide-device-twins.md
[lnk-query]: iot-hub-devguide-query-language.md
[lnk-identity]: iot-hub-devguide-identity-registry.md

[lnk-iothub-getstarted]: iot-hub-node-node-getstarted.md
[lnk-device-management]: iot-hub-device-management-get-started.md
[lnk-gateway-SDK]: iot-hub-linux-gateway-sdk-get-started.md
[lnk-connect-device]: https://azure.microsoft.com/develop/iot/

[lnk-twin-how-to-configure]: iot-hub-node-node-twin-how-to-configure.md
[lnk-dev-setup]: https://github.com/Azure/azure-iot-sdks/blob/master/doc/get_started/node-devbox-setup.md

[lnk-methods-tutorial]: iot-hub-c2d-methods.md
[lnk-devguide-mqtt]: iot-hub-mqtt-support.md


<!--HONumber=Oct16_HO2-->


