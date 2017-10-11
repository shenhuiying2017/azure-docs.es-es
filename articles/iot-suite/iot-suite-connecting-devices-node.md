---
title: "Conexión de un dispositivo mediante Node.js | Microsoft Docs"
description: "Se describe cómo conectar un dispositivo a la solución de supervisión remota preconfigurada del Conjunto de IoT de Azure mediante una aplicación creada en Node.js."
services: 
suite: iot-suite
documentationcenter: na
author: dominicbetts
manager: timlt
editor: 
ms.assetid: fc50a33f-9fb9-42d7-b1b8-eb5cff19335e
ms.service: iot-suite
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/24/2017
ms.author: dobett
ms.openlocfilehash: 6459b6196eb7f4a083b67e5a421bcc0d51d39e5c
ms.sourcegitcommit: 18ad9bc049589c8e44ed277f8f43dcaa483f3339
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 08/29/2017
---
# <a name="connect-your-device-to-the-remote-monitoring-preconfigured-solution-nodejs"></a>Conectar el dispositivo a la solución preconfigurada de supervisión remota (Node.js)
[!INCLUDE [iot-suite-selector-connecting](../../includes/iot-suite-selector-connecting.md)]

## <a name="create-a-nodejs-sample-solution"></a>Creación de una solución de ejemplo de node.js

Asegúrese de que tiene instalada la versión 0.11.5 o posterior de Node.js en la máquina de desarrollo. Para comprobar la versión, puede ejecutar `node --version` en la línea de comandos.

1. Cree una carpeta denominada **RemoteMonitoring** en la máquina de desarrollo. Vaya a esta carpeta en el entorno de línea de comandos.

1. Ejecute los comandos siguientes para descargar e instalar los paquetes que necesita para realizar la aplicación de ejemplo:

    ```
    npm init
    npm install azure-iot-device azure-iot-device-mqtt --save
    ```

1. En la carpeta **RemoteMonitoring**, cree un archivo llamado **remote_monitoring.js**. Abra este archivo en un editor de texto.

1. En el archivo **remote_monitoring.js**, agregue las siguientes instrucciones `require`:

    ```nodejs
    'use strict';

    var Protocol = require('azure-iot-device-mqtt').Mqtt;
    var Client = require('azure-iot-device').Client;
    var ConnectionString = require('azure-iot-device').ConnectionString;
    var Message = require('azure-iot-device').Message;
    ```

1. Agregue las siguientes declaraciones de variable después de las instrucciones `require` . Sustituya los valores de marcador de posición [Device Id] y [Device Key] por los valores que anotó para el dispositivo en el panel de la solución de supervisión remota. Use el nombre de host de IoT Hub en el panel de la solución para sustituir [IoTHub Name]. Por ejemplo, si el nombre de host de IoT Hub es **contoso.azure-devices.net**, sustituya [IoTHub Name] por **contoso**:

    ```nodejs
    var connectionString = 'HostName=[IoTHub Name].azure-devices.net;DeviceId=[Device Id];SharedAccessKey=[Device Key]';
    var deviceId = ConnectionString.parse(connectionString).DeviceId;
    ```

1. Agregue las siguientes variables para definir algunos datos de telemetría de base:

    ```nodejs
    var temperature = 50;
    var humidity = 50;
    var externalTemperature = 55;
    ```

1. Agregue la función auxiliar siguiente para imprimir los resultados de la operación:

    ```nodejs
    function printErrorFor(op) {
        return function printError(err) {
            if (err) console.log(op + ' error: ' + err.toString());
        };
    }
    ```

1. Agregue la siguiente función auxiliar para aleatorizar los valores de telemetría:

    ```nodejs
    function generateRandomIncrement() {
        return ((Math.random() * 2) - 1);
    }
    ```

1. Agregue la siguiente definición para el objeto **DeviceInfo** que envía el dispositivo al inicio:

    ```nodejs
    var deviceMetaData = {
        'ObjectType': 'DeviceInfo',
        'IsSimulatedDevice': 0,
        'Version': '1.0',
        'DeviceProperties': {
            'DeviceID': deviceId,
            'HubEnabledState': 1
        }
    };
    ```

1. Agregue la siguiente definición para los valores notificados por el dispositivo gemelo. Esta definición incluye descripciones de los métodos directos que admite el dispositivo:

    ```nodejs
    var reportedProperties = {
        "Device": {
            "DeviceState": "normal",
            "Location": {
                "Latitude": 47.642877,
                "Longitude": -122.125497
            }
        },
        "Config": {
            "TemperatureMeanValue": 56.7,
            "TelemetryInterval": 45
        },
        "System": {
            "Manufacturer": "Contoso Inc.",
            "FirmwareVersion": "2.22",
            "InstalledRAM": "8 MB",
            "ModelNumber": "DB-14",
            "Platform": "Plat 9.75",
            "Processor": "i3-9",
            "SerialNumber": "SER99"
        },
        "Location": {
            "Latitude": 47.642877,
            "Longitude": -122.125497
        },
        "SupportedMethods": {
            "Reboot": "Reboot the device",
            "InitiateFirmwareUpdate--FwPackageURI-string": "Updates device Firmware. Use parameter FwPackageURI to specifiy the URI of the firmware file"
        },
    }
    ```

1. Agregue la siguiente función para controlar la llamada al método directo **Reboot**:

    ```nodejs
    function onReboot(request, response) {
        // Implement actual logic here.
        console.log('Simulated reboot...');

        // Complete the response
        response.send(200, "Rebooting device", function(err) {
            if(!!err) {
                console.error('An error occurred when sending a method response:\n' + err.toString());
            } else {
                console.log('Response to method \'' + request.methodName + '\' sent successfully.' );
            }
        });
    }
    ```

1. Agregue la siguiente función para controlar la llamada al método directo **InitiateFirmwareUpdate**. Este método directo usa un parámetro para especificar la ubicación de la imagen de firmware que se va a descargar e inicia la actualización de firmware en el dispositivo de forma asincrónica:

    ```nodejs
    function onInitiateFirmwareUpdate(request, response) {
        console.log('Simulated firmware update initiated, using: ' + request.payload.FwPackageURI);

        // Complete the response
        response.send(200, "Firmware update initiated", function(err) {
            if(!!err) {
                console.error('An error occurred when sending a method response:\n' + err.toString());
            } else {
                console.log('Response to method \'' + request.methodName + '\' sent successfully.' );
            }
        });

        // Add logic here to perform the firmware update asynchronously
    }
    ```

1. Agregue el código siguiente para crear una instancia de cliente:

    ```nodejs
    var client = Client.fromConnectionString(connectionString, Protocol);
    ```

1. Agregue el siguiente código para:

    * Abrir la conexión.
    * Enviar el objeto **DeviceInfo**.
    * Configurar un controlador para propiedades deseadas.
    * Enviar propiedades notificadas.
    * Registrar controladores para métodos directos.
    * Empezar a enviar telemetría.

    ```nodejs
    client.open(function (err) {
        if (err) {
            printErrorFor('open')(err);
        } else {
            console.log('Sending device metadata:\n' + JSON.stringify(deviceMetaData));
            client.sendEvent(new Message(JSON.stringify(deviceMetaData)), printErrorFor('send metadata'));

            // Create device twin
            client.getTwin(function(err, twin) {
                if (err) {
                    console.error('Could not get device twin');
                } else {
                    console.log('Device twin created');

                    twin.on('properties.desired', function(delta) {
                        console.log('Received new desired properties:');
                        console.log(JSON.stringify(delta));
                    });

                    // Send reported properties
                    twin.properties.reported.update(reportedProperties, function(err) {
                        if (err) throw err;
                        console.log('twin state reported');
                    });

                    // Register handlers for direct methods
                    client.onDeviceMethod('Reboot', onReboot);
                    client.onDeviceMethod('InitiateFirmwareUpdate', onInitiateFirmwareUpdate);
                }
            });

            // Start sending telemetry
            var sendInterval = setInterval(function () {
                temperature += generateRandomIncrement();
                externalTemperature += generateRandomIncrement();
                humidity += generateRandomIncrement();

                var data = JSON.stringify({
                    'DeviceID': deviceId,
                    'Temperature': temperature,
                    'Humidity': humidity,
                    'ExternalTemperature': externalTemperature
                });

                console.log('Sending device event data:\n' + data);
                client.sendEvent(new Message(data), printErrorFor('send event'));
            }, 5000);

            client.on('error', function (err) {
                printErrorFor('client')(err);
                if (sendInterval) clearInterval(sendInterval);
                client.close(printErrorFor('client.close'));
            });
        }
    });
    ```

1. Guarde los cambios al archivo **remote_monitoring.js**.

1. Ejecute el siguiente comando en un símbolo del sistema para iniciar la aplicación de ejemplo:
   
    ```
    node remote_monitoring.js
    ```

[!INCLUDE [iot-suite-visualize-connecting](../../includes/iot-suite-visualize-connecting.md)]

[lnk-github-repo]: https://github.com/azure/azure-iot-sdk-node
[lnk-github-prepare]: https://github.com/Azure/azure-iot-sdk-node/blob/master/doc/node-devbox-setup.md
