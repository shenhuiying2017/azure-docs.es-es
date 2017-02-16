---
title: "Cómo realizar una actualización de firmware con IoT Hub de Azure | Microsoft Docs"
description: "En este tutorial se muestra cómo realizar una actualización de firmware"
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
ms.date: 11/17/2016
ms.author: juanpere
translationtype: Human Translation
ms.sourcegitcommit: 00746fa67292fa6858980e364c88921d60b29460
ms.openlocfilehash: 7cbb823f2d8b9a337bd987ae3fc41a85ddb6ae80


---
# <a name="tutorial-how-to-do-a-firmware-update"></a>Tutorial: Cómo realizar una actualización de firmware
[!INCLUDE [iot-hub-selector-firmware-update](../../includes/iot-hub-selector-firmware-update.md)]

## <a name="introduction"></a>Introducción
En el tutorial de [Introducción a la administración de dispositivos][lnk-dm-getstarted], vimos cómo usar los primitivos de [dispositivo gemelo][lnk-devtwin] y [métodos directos][lnk-c2dmethod] para reiniciar de forma remota un dispositivo. Este tutorial emplea los mismos tipos primitivos de IoT Hub. Además, proporciona orientación y muestra cómo realizar una actualización de firmware simulada de extremo a extremo.  Este patrón se usa en la implementación de actualizaciones de firmware del [ejemplo de implementación de dispositivos Raspberry Pi][lnk-rpi-implementation].

En este tutorial se muestra cómo realizar las siguientes acciones:

* Crear una aplicación de consola que llame al método directo firmwareUpdate en la aplicación de dispositivo simulado a través de su IoT Hub.
* Crear una aplicación de dispositivo simulado que implemente un método directo firmwareUpdate que pase por un proceso de varias fases que espera para descargar la imagen de firmware, la descarga y, por último, la aplica.  Durante toda la ejecución, en cada fase el dispositivo usa las propiedades notificadas para actualizar el progreso.

Al final de este tutorial, tendrá una aplicación de dispositivo de consola de Node.js y una aplicación back-end de consola .NET (C#):

**dmpatterns_fwupdate_service.js**, que llama a un método directo en la aplicación de dispositivo simulado, muestra la respuesta y muestra periódicamente (cada 500 ms) las propiedades notificadas actualizadas.

**TriggerFWUpdate**, que se conecta a IoT Hub con la identidad del dispositivo creada anteriormente, recibe un método directo firmwareUpdate y se ejecuta mediante un proceso de varios estados para simular una actualización de firmware; entre ellas, la espera para la descarga de imágenes, la descarga de la nueva imagen y, finalmente, la aplicación de dicha imagen.

Para completar este tutorial, necesitará lo siguiente:

* Microsoft Visual Studio 2015.
* Node.js versión 0.12.x o posteriores. <br/>  En [Preparación del entorno de desarrollo][lnk-dev-setup] se describe cómo instalar Node.js para este tutorial en Windows o Linux.
* Una cuenta de Azure activa. (En caso de no tenerla, puede crear una [cuenta gratuita][lnk-free-trial] en solo unos minutos).

Siga los pasos del artículo de [introducción a la administración de dispositivos](iot-hub-csharp-node-device-management-get-started.md) para crear su instancia de IoT Hub y obtener la cadena de conexión.

[!INCLUDE [iot-hub-get-started-create-hub](../../includes/iot-hub-get-started-create-hub.md)]

[!INCLUDE [iot-hub-get-started-create-device-identity](../../includes/iot-hub-get-started-create-device-identity.md)]

## <a name="trigger-a-remote-firmware-update-on-the-device-using-a-direct-method"></a>Desencadenamiento de una actualización de firmware remota en el dispositivo con un método directo
En esta sección, creará una aplicación de consola de .NET (mediante C#) que inicia una actualización de firmware remota en un dispositivo con un método directo y utiliza las consultas del dispositivo gemelo para obtener periódicamente el estado de la actualización de firmware activa en ese dispositivo.

1. En Visual Studio, agregue un proyecto de escritorio clásico de Windows de Visual C# a la solución actual mediante la plantilla de proyecto **Aplicación de consola** . Asigne al proyecto el nombre **TriggerFWUpdate**.

    ![Nuevo proyecto de escritorio clásico de Windows de Visual C#][img-createapp]

2. En el Explorador de soluciones, haga clic con el botón derecho en el proyecto **TriggerFWUpdate** y luego seleccione **Administrar paquetes NuGet**.
3. En la ventana **Administrador de paquetes NuGet**, seleccione **Examinar**, busque **microsoft.azure.devices**, seleccione **Instalar** para instalar el paquete **Microsoft.Azure.Devices** y acepte los términos de uso. Este procedimiento permite descargar, instalar y agregar una referencia al paquete NuGet de [Microsoft Azure IoT Service SDK][lnk-nuget-service-sdk] (SDK de Servicio IoT de Microsoft Azure) y a sus dependencias.

    ![Ventana del Administrador de paquetes NuGet][img-servicenuget]
4. Agregue las siguientes instrucciones `using` al principio del archivo **Program.cs** :
   
        using Microsoft.Azure.Devices;
        
5. Agregue los campos siguientes a la clase **Program** . Sustituya el valor de marcadores de posición múltiples por la cadena de conexión de IoT Hub que creó en la sección anterior.
   
        static RegistryManager registryManager;
        static string connString = "{iot hub connection string}";
        static ServiceClient client;
        static JobClient jobClient;
        static string targetDevice = "{deviceIdForTargetDevice}";
        
6. Agregue el método siguiente a la clase **Program** :
   
        public static async Task QueryTwinFWUpdateReported()
        {
            Twin twin = await registryManager.GetTwinAsync(targetDevice);
            Console.WriteLine(twin.Properties.Reported.ToJson());
        }
        
7. Agregue el método siguiente a la clase **Program** :

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

7. Por último, agregue las líneas siguientes al método **Main** :
   
        registryManager = RegistryManager.CreateFromConnectionString(connString);
        StartFirmwareUpdate().Wait();
        QueryTwinFWUpdateReported().Wait();
        Console.WriteLine("Press ENTER to exit.");
        Console.ReadLine();
        
8. Compile la solución.

## <a name="create-a-simulated-device-app"></a>Creación de una aplicación de dispositivo simulado
En esta sección:

* Creará una aplicación de consola de Node.js que responda a un método directo que se llama desde la nube.
* Desencadenará una actualización de firmware simulada.
* Usará las propiedades notificadas para habilitar consultas de dispositivos gemelos a fin de identificar los dispositivos y cuándo se actualizó su firmware por última vez.

1. Cree una nueva carpeta vacía denominada "**manageddevice**".  En la carpeta **manageddevice** , cree un archivo package.json con el siguiente comando en el símbolo del sistema.  Acepte todos los valores predeterminados:
   
    ```
    npm init
    ```
2. En el símbolo del sistema, en la carpeta **manageddevice**, ejecute el siguiente comando para instalar el paquete del SDK de dispositivo **azure-iot-device** y el paquete **azure-iot-device-mqtt**:
   
    ```
    npm install azure-iot-device azure-iot-device-mqtt --save
    ```
3. Con un editor de texto, cree un nuevo archivo **dmpatterns_fwupdate_device.js** en la carpeta **manageddevice**.
4. Agregue las siguientes instrucciones "require" al principio del archivo **dmpatterns_fwupdate_device.js**:
   
    ```
    'use strict';
   
    var Client = require('azure-iot-device').Client;
    var Protocol = require('azure-iot-device-mqtt').Mqtt;
    ```
5. Agregue una variable **connectionString** y utilícela para crear un cliente de dispositivo.  
   
    ```
    var connectionString = 'HostName={youriothostname};DeviceId=myDeviceId;SharedAccessKey={yourdevicekey}';
    var client = Client.fromConnectionString(connectionString, Protocol);
    ```
6. Agregue la siguiente función que se usa para actualizar las propiedades notificadas.
   
    ```
    var reportFWUpdateThroughTwin = function(twin, firmwareUpdateValue) {
      var patch = {
          iothubDM : {
            firmwareUpdate : firmwareUpdateValue
          }
      };
   
      twin.properties.reported.update(patch, function(err) {
        if (err) throw err;
        console.log('twin state reported')
      });
    };
    ```
7. Agregue las siguientes funciones que simulan la descarga y aplicación de la imagen de firmware.
   
    ```
    var simulateDownloadImage = function(imageUrl, callback) {
      var error = null;
      var image = "[fake image data]";
   
      console.log("Downloading image from " + imageUrl);
   
      callback(error, image);
    }
   
    var simulateApplyImage = function(imageData, callback) {
      var error = null;
   
      if (!imageData) {
        error = {message: 'Apply image failed because of missing image data.'};
      }
   
      callback(error);
    }
    ```
8. Agregue la siguiente función que actualiza el estado de actualización de firmware a través de las propiedades notificadas a Esperando descarga.  Normalmente, los dispositivos se notifican a partir de una actualización disponible y una directiva definida por un administrador provoca que el dispositivo empiece a descargar y aplicar la actualización.  Aquí es donde se ejecutaría la lógica para habilitar esa directiva.  Por motivos de simplicidad, estableceremos un retraso de 4 segundos y continuaremos con la descarga de la imagen de firmware. 
   
    ```
    var waitToDownload = function(twin, fwPackageUriVal, callback) {
      var now = new Date();
   
      reportFWUpdateThroughTwin(twin, {
        fwPackageUri: fwPackageUriVal,
        status: 'waiting',
        error : null,
        startedWaitingTime : now.toISOString()
      });
      setTimeout(callback, 4000);
    };
    ```
9. Agregue la siguiente función que actualiza el estado de actualización de firmware a través de las propiedades notificadas a Descargando la imagen de firmware.  A continuación, se simula una descarga de firmware y se actualiza el estado de actualización de firmware para notificar si la descarga se ha realizado correctamente o no.
   
    ```
    var downloadImage = function(twin, fwPackageUriVal, callback) {
      var now = new Date();   
   
      reportFWUpdateThroughTwin(twin, {
        status: 'downloading',
      });
   
      setTimeout(function() {
        // Simulate download
        simulateDownloadImage(fwPackageUriVal, function(err, image) {
   
          if (err)
          {
            reportFWUpdateThroughTwin(twin, {
              status: 'downloadfailed',
              error: {
                code: error_code,
                message: error_message,
              }
            });
          }
          else {        
            reportFWUpdateThroughTwin(twin, {
              status: 'downloadComplete',
              downloadCompleteTime: now.toISOString(),
            });
   
            setTimeout(function() { callback(image); }, 4000);   
          }
        });
   
      }, 4000);
    }
    ```
10. Agregue la siguiente función que actualiza el estado de actualización de firmware a través de las propiedades notificadas a Aplicando la imagen de firmware.  A continuación, se simula una aplicación de la imagen de firmware y se actualiza el estado de actualización de firmware para notificar si la aplicación se ha realizado correctamente o no.
    
    ```
    var applyImage = function(twin, imageData, callback) {
      var now = new Date();   
    
      reportFWUpdateThroughTwin(twin, {
        status: 'applying',
        startedApplyingImage : now.toISOString()
      });
    
      setTimeout(function() {
    
        // Simulate apply firmware image
        simulateApplyImage(imageData, function(err) {
          if (err) {
            reportFWUpdateThroughTwin(twin, {
              status: 'applyFailed',
              error: {
                code: err.error_code,
                message: err.error_message,
              }
            });
          } else { 
            reportFWUpdateThroughTwin(twin, {
              status: 'applyComplete',
              lastFirmwareUpdate: now.toISOString()
            });    
    
          }
        });
    
        setTimeout(callback, 4000);
    
      }, 4000);
    }
    ```
11. Agregue la siguiente función que controla el método **firmwareUpdate** e inicie el proceso de actualización de firmware en varias fases.
    
    ```
    var onFirmwareUpdate = function(request, response) {
    
      // Respond the cloud app for the direct method
      response.send(200, 'FirmwareUpdate started', function(err) {
        if (!err) {
          console.error('An error occured when sending a method response:\n' + err.toString());
        } else {
          console.log('Response to method \'' + request.methodName + '\' sent successfully.');
        }
      });
    
      // Get the parameter from the body of the method request
      var fwPackageUri = JSON.parse(request.payload).fwPackageUri;
    
      // Obtain the device twin
      client.getTwin(function(err, twin) {
        if (err) {
          console.error('Could not get device twin.');
        } else {
          console.log('Device twin acquired.');
    
          // Start the multi-stage firmware update
          waitToDownload(twin, fwPackageUri, function() {
            downloadImage(twin, fwPackageUri, function(imageData) {
              applyImage(twin, imageData, function() {});    
            });  
          });
    
        }
      });
    }
    ```
12. Finalmente, agregue el siguiente código que conecta con su IoT Hub como un dispositivo. 
    
    ```
    client.open(function(err) {
      if (err) {
        console.error('Could not connect to IotHub client');
      }  else {
        console.log('Client connected to IoT Hub.  Waiting for firmwareUpdate direct method.');
      }
    
      client.onDeviceMethod('firmwareUpdate', onFirmwareUpdate(request, response));
    });
    ```

> [!NOTE]
> Por simplificar, este tutorial no implementa ninguna directiva de reintentos. En el código de producción, deberá implementar directivas de reintentos (por ejemplo, retroceso exponencial), tal y como se sugiere en el artículo de MSDN [Transient Fault Handling][lnk-transient-faults] (Tratamiento de errores temporales).
> 
> 

## <a name="run-the-apps"></a>Ejecución de las aplicaciones
Ya está preparado para ejecutar las aplicaciones.

1. En el símbolo del sistema dentro de la carpeta **manageddevice**, ejecute el siguiente comando para iniciar la escucha del método directo de reinicio.
   
    ```
    node dmpatterns_fwupdate_device.js
    ```
2. Ejecute la aplicación de consola de C# **TriggerFWUpdate**: haga clic con el botón derecho en el proyecto **TriggerFWUpdate** y seleccione **Depurar** e **Iniciar nueva instancia**.

3. Verá la respuesta del dispositivo al método directo en la consola.

## <a name="next-steps"></a>Pasos siguientes
En este tutorial, ha usado un método directo para desencadenar una actualización de firmware remota en un dispositivo y ha utilizado periódicamente las propiedades notificadas para entender el progreso del proceso de actualización del firmware.  

Para aprender a ampliar la solución de IoT y programar llamadas de método en varios dispositivos, consulte el tutorial sobre la [programación y difusión de trabajos][lnk-tutorial-jobs].

<!-- images -->
[img-servicenuget]: media/iot-hub-csharp-node-firmware-update/servicesdknuget.png
[img-createapp]: media/iot-hub-csharp-node-firmware-update/createnetapp.png

[lnk-devtwin]: iot-hub-devguide-device-twins.md
[lnk-c2dmethod]: iot-hub-devguide-direct-methods.md
[lnk-dm-getstarted]: iot-hub-node-node-device-management-get-started.md
[lnk-tutorial-jobs]: iot-hub-node-node-schedule-jobs.md

[lnk-dev-setup]: https://github.com/Azure/azure-iot-sdks/blob/master/doc/get_started/node-devbox-setup.md
[lnk-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[lnk-transient-faults]: https://msdn.microsoft.com/library/hh680901(v=pandp.50).aspx
[lnk-rpi-implementation]: https://github.com/Azure/azure-iot-sdks/tree/master/c/iothub_client/samples/iothub_client_sample_mqtt_dm/pi_device
[lnk-nuget-service-sdk]: https://www.nuget.org/packages/Microsoft.Azure.Devices/


<!--HONumber=Nov16_HO5-->


