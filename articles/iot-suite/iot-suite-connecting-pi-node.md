---
title: "Aprovisionamiento de Raspberry Pi para la supervisión remota en Node.js con Azure | Microsoft Docs"
description: "Se describe cómo conectar un dispositivo Raspberry Pi a la solución de supervisión remota preconfigurada del Conjunto de aplicaciones de IoT de Azure mediante una aplicación creada en Node.js."
services: iot-suite
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
ms.date: 12/12/2017
ms.author: dobett
ms.openlocfilehash: d2cc72f26568a362049f24323ffa598b6012d77f
ms.sourcegitcommit: 922687d91838b77c038c68b415ab87d94729555e
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/13/2017
---
# <a name="connect-your-raspberry-pi-device-to-the-remote-monitoring-preconfigured-solution-nodejs"></a>Conexión del dispositivo Raspberry Pi a la solución preconfigurada de supervisión remota (Node.js)

[!INCLUDE [iot-suite-selector-connecting](../../includes/iot-suite-selector-connecting.md)]

Este tutorial muestra cómo conectar un dispositivo físico a la solución preconfigurada de supervisión remota. En este tutorial, se usa Node.js, que es una buena opción para entornos con las restricciones de recursos mínimos.

### <a name="required-hardware"></a>Requisitos de hardware

Un equipo de escritorio que permita conectarse remotamente a la línea de comandos en Raspberry Pi.

[Kit de inicio de Microsoft IoT para Raspberry Pi 3](https://azure.microsoft.com/develop/iot/starter-kits/) o componentes equivalentes. En este tutorial se usan los siguientes elementos del kit:

- Raspberry Pi 3
- Tarjeta MicroSD (con NOOBS)
- Un cable USB mini
- Un cable Ethernet

### <a name="required-desktop-software"></a>Requiere el software de escritorio

Necesita el cliente SSH en su máquina de escritorio para poder acceder de forma remota a la línea de comandos en su Raspberry Pi.

- Windows no incluye ningún cliente SSH. Se recomienda usar [PuTTY](http://www.putty.org/).
- La mayoría de las distribuciones de Linux y Mac OS incluyen la utilidad de línea de comandos de SSH. Para más información, consulte [SSH Using Linux or Mac OS](https://www.raspberrypi.org/documentation/remote-access/ssh/unix.md) (SSH cuando se usa Linux o Mac OS).

### <a name="required-raspberry-pi-software"></a>Software necesario de Raspberry Pi

Si aún no lo ha hecho, instale Node.js versión 4.0.0 o posterior en su dispositivo Raspberry Pi. Los pasos siguientes muestran cómo instalar Node.js v6.11.4 en Raspberry Pi:

1. Conecte con su dispositivo Raspberry Pi mediante `ssh`. Para obtener más información, consulte [SSH (Secure Shell)](https://www.raspberrypi.org/documentation/remote-access/ssh/README.md) en el [sitio web de Raspberry Pi](https://www.raspberrypi.org/).

1. Use el siguiente comando para actualizar Raspberry Pi:

    ```sh
    sudo apt-get update
    ```

1. Use el siguiente comando para descargar los archivos binarios de Node.js en Raspberry Pi:

    ```sh
    wget https://nodejs.org/dist/v6.11.4/node-v6.11.4-linux-armv7l.tar.gz
    ```

1. Use el siguiente comando para los binarios:

    ```sh
    sudo tar -C /usr/local --strip-components 1 -xzf node-v6.11.4-linux-armv7l.tar.gz
    ```

1. Use el siguiente comando para comprobar que ha instalado Node.js v6.11.4 correctamente:

    ```sh
    node --version
    ```

## <a name="create-a-nodejs-solution"></a>Creación de una solución Node.js

Realice los pasos siguientes con la conexión `ssh` al dispositivo Raspberry Pi:

1. Cree una carpeta denominada `RemoteMonitoring` en la carpeta particular en el dispositivo Raspberry Pi. Vaya a esta carpeta en la línea de comandos:

    ```sh
    cd ~
    mkdir RemoteMonitoring
    cd RemoteMonitoring
    ```

1. Para descargar e instalar los paquetes que necesita para realizar la aplicación de ejemplo, ejecute los comandos siguientes:

    ```sh
    npm init
    npm install azure-iot-device azure-iot-device-mqtt --save
    ```

1. En la carpeta `RemoteMonitoring`, cree un archivo llamado **remote_monitoring.js**. Abra este archivo en un editor de texto. En el dispositivo Raspberry Pi, puede usar los editores de texto `nano` o `vi`.

1. En el archivo **remote_monitoring.js**, agregue las siguientes instrucciones `require`:

    ```nodejs
    'use strict';

    var Protocol = require('azure-iot-device-mqtt').Mqtt;
    var Client = require('azure-iot-device').Client;
    var ConnectionString = require('azure-iot-device').ConnectionString;
    var Message = require('azure-iot-device').Message;
    ```

1. Agregue las siguientes declaraciones de variable después de las instrucciones `require` . Sustituya los valores de marcador de posición `{Device Id}` y `{Device Key}` por los valores que anotó para el dispositivo que aprovisionó en la solución de supervisión remota. Use el nombre de host de IoT Hub de la solución para sustituir a `{IoTHub Name}`. Por ejemplo, si el nombre de host de IoT Hub es `contoso.azure-devices.net`, reemplace `{IoTHub Name}` por `contoso`:

    ```nodejs
    var connectionString = 'HostName={IoTHub Name}.azure-devices.net;DeviceId={Device Id};SharedAccessKey={Device Key}';
    var deviceId = ConnectionString.parse(connectionString).DeviceId;
    ```

1. Para definir algunos datos de telemetría básicos, agregue las siguientes variables:

    ```nodejs
    var temperature = 50;
    var temperatureUnit = 'F';
    var humidity = 50;
    var humidityUnit = '%';
    var pressure = 55;
    var pressureUnit = 'psig';
    ```

1. Para definir algunos valores de propiedades, agregue las siguientes variables:

    ```nodejs
    var temperatureSchema = 'chiller-temperature;v1';
    var humiditySchema = 'chiller-humidity;v1';
    var pressureSchema = 'chiller-pressure;v1';
    var interval = "00:00:05";
    var deviceType = "Chiller";
    var deviceFirmware = "1.0.0";
    var deviceFirmwareUpdateStatus = "";
    var deviceLocation = "Building 44";
    var deviceLatitude = 47.638928;
    var deviceLongitude = -122.13476;
    ```

1. Agregue la siguiente variable para definir las propiedades notificadas para enviar a la solución. Estas propiedades incluyen los metadatos para describir los métodos y la telemetría que el dispositivo usa:

    ```nodejs
    var reportedProperties = {
      "Protocol": "MQTT",
      "SupportedMethods": "Reboot,FirmwareUpdate,EmergencyValveRelease,IncreasePressure",
      "Telemetry": {
        "TemperatureSchema": {
          "Interval": interval,
          "MessageTemplate": "{\"temperature\":${temperature},\"temperature_unit\":\"${temperature_unit}\"}",
          "MessageSchema": {
            "Name": temperatureSchema,
            "Format": "JSON",
            "Fields": {
              "temperature": "Double",
              "temperature_unit": "Text"
            }
          }
        },
        "HumiditySchema": {
          "Interval": interval,
          "MessageTemplate": "{\"humidity\":${humidity},\"humidity_unit\":\"${humidity_unit}\"}",
          "MessageSchema": {
            "Name": humiditySchema,
            "Format": "JSON",
            "Fields": {
              "humidity": "Double",
              "humidity_unit": "Text"
            }
          }
        },
        "PressureSchema": {
          "Interval": interval,
          "MessageTemplate": "{\"pressure\":${pressure},\"pressure_unit\":\"${pressure_unit}\"}",
          "MessageSchema": {
            "Name": pressureSchema,
            "Format": "JSON",
            "Fields": {
              "pressure": "Double",
              "pressure_unit": "Text"
            }
          }
        }
      },
      "Type": deviceType,
      "Firmware": deviceFirmware,
      "FirmwareUpdateStatus": deviceFirmwareUpdateStatus,
      "Location": deviceLocation,
      "Latitude": deviceLatitude,
      "Longitude": deviceLongitude
    }
    ```

1. Para imprimir los resultados de la operación, agregue la función auxiliar siguiente:

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

1. Agregue la siguiente función para controlar las llamadas al método directo desde la solución. La solución utiliza métodos directos para actuar en los dispositivos:

    ```nodejs
    function onDirectMethod(request, response) {
      // Implement logic asynchronously here.
      console.log('Simulated ' + request.methodName);

      // Complete the response
      response.send(200, request.methodName + ' was called on the device', function (err) {
        if (!!err) {
          console.error('An error ocurred when sending a method response:\n' +
            err.toString());
        } else {
          console.log('Response to method \'' + request.methodName +
            '\' sent successfully.');
        }
      });
    }
    ```

1. Agregue el código siguiente para enviar los datos de telemetría a la solución. La aplicación cliente agrega propiedades al mensaje para identificar el esquema del mensaje:

    ```node.js
    function sendTelemetry(data, schema) {
      var d = new Date();
      var payload = JSON.stringify(data);
      var message = new Message(payload);
      message.properties.add('$$CreationTimeUtc', d.toISOString());
      message.properties.add('$$MessageSchema', schema);
      message.properties.add('$$ContentType', 'JSON');

      console.log('Sending device message data:\n' + payload);
      client.sendEvent(message, printErrorFor('send event'));
    }
    ```

1. Agregue el código siguiente para crear una instancia de cliente:

    ```nodejs
    var client = Client.fromConnectionString(connectionString, Protocol);
    ```

1. Agregue el siguiente código para:

    - Abrir la conexión.
    - Configurar un controlador para propiedades deseadas.
    - Enviar propiedades notificadas.
    - Registrar controladores para métodos directos.
    - Empezar a enviar telemetría.

    ```nodejs
    client.open(function (err) {
      if (err) {
        printErrorFor('open')(err);
      } else {
        // Create device Twin
        client.getTwin(function (err, twin) {
          if (err) {
            console.error('Could not get device twin');
          } else {
            console.log('Device twin created');

            twin.on('properties.desired', function (delta) {
              // Handle desired properties set by solution
              console.log('Received new desired properties:');
              console.log(JSON.stringify(delta));
            });

            // Send reported properties
            twin.properties.reported.update(reportedProperties, function (err) {
              if (err) throw err;
              console.log('twin state reported');
            });

            // Register handlers for all the method names we are interested in.
            // Consider separate handlers for each method.
            client.onDeviceMethod('Reboot', onDirectMethod);
            client.onDeviceMethod('FirmwareUpdate', onDirectMethod);
            client.onDeviceMethod('EmergencyValveRelease', onDirectMethod);
            client.onDeviceMethod('IncreasePressure', onDirectMethod);
          }
        });

        // Start sending telemetry
        var sendTemperatureInterval = setInterval(function () {
          temperature += generateRandomIncrement();
          var data = {
            'temperature': temperature,
            'temperature_unit': temperatureUnit
          };
          sendTelemetry(data, temperatureSchema)
        }, 5000);

        var sendHumidityInterval = setInterval(function () {
          humidity += generateRandomIncrement();
          var data = {
            'humidity': humidity,
            'humidity_unit': humidityUnit
          };
          sendTelemetry(data, humiditySchema)
        }, 5000);

        var sendPressureInterval = setInterval(function () {
          pressure += generateRandomIncrement();
          var data = {
            'pressure': pressure,
            'pressure_unit': pressureUnit
          };
          sendTelemetry(data, pressureSchema)
        }, 5000);

        client.on('error', function (err) {
          printErrorFor('client')(err);
          if (sendTemperatureInterval) clearInterval(sendTemperatureInterval);
          if (sendHumidityInterval) clearInterval(sendHumidityInterval);
          if (sendPressureInterval) clearInterval(sendPressureInterval);
          client.close(printErrorFor('client.close'));
        });
      }
    });
    ```

1. Guarde los cambios al archivo **remote_monitoring.js**.

1. Para iniciar la aplicación de ejemplo, ejecute el siguiente comando en un símbolo del sistema en el dispositivo Raspberry Pi:

    ```sh
    node remote_monitoring.js
    ```

[!INCLUDE [iot-suite-visualize-connecting](../../includes/iot-suite-visualize-connecting.md)]
