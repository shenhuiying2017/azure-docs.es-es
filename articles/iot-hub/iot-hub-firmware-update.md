---
title: Cómo realizar una actualización de firmware | Microsoft Docs
description: En este tutorial se muestra cómo realizar una actualización de firmware
services: iot-hub
documentationcenter: .net
author: juanjperez
manager: timlt
editor: ''

ms.service: iot-hub
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/30/2016
ms.author: juanpere

---
# <a name="tutorial-how-to-do-a-firmware-update-preview"></a>Tutorial: Cómo realizar una actualización de firmware (versión preliminar)
## <a name="introduction"></a>Introducción
En el tutorial de [introducción a la administración de dispositivos][lnk-dm-getstarted], vimos cómo usar el [dispositivo gemelo][lnk devtwin] y los [métodos de la nube al dispositivo (C2D)][lnk-c2dmethod] primitivos para reiniciar de forma remota un dispositivo. Este tutorial utiliza a los mismos tipos primitivos de IoT Hub. Además, proporciona orientación y muestra cómo realizar una actualización de firmware simulada de extremo a extremo.  Este patrón se utiliza en la implementación de actualización de firmware para el ejemplo de dispositivo Intel Edison.

En este tutorial se muestra cómo realizar las siguientes acciones:

* Crear una aplicación de consola que llame a un método directo firmwareUpdate del dispositivo simulado a través de IoT Hub.
* Cred un dispositivo simulado que implementa un método directo firmwareUpdate que pase por un proceso de varias fases que espera para descargar la imagen de firmware, la descarga y, por último, se aplica.  Durante toda la ejecución, en cada fase el dispositivo usa las propiedades notificadas del dispositivo gemelo para actualizar el progreso.

Al final de este tutorial tendrá dos aplicaciones de consola de Node.js:

**dmpatterns_fwupdate_service.js**, que llama a un método directo en el dispositivo simulado, muestra la respuesta y muestra periódicamente (cada 500 ms) las propiedades notificadas del dispositivo gemelo actualizadas.

**dmpatterns_fwupdate_device.js**, que se conecta a IoT Hub con la identidad del dispositivo creada anteriormente, recibe un método directo firmwareUpdate y se ejecuta mediante un proceso de varios estado para simular una actualización de firmware; entre ellas, la espera para la descarga de imágenes, la descarga de la nueva imagen y, finalmente, la aplicación de dicha imagen.

Para completar este tutorial, necesitará lo siguiente:

Node.js versión 0.12.x o posteriores. <br/>  [Prepare your development environment][lnk-dev-setup] (Preparación de un entorno de desarrollo) describe cómo instalar Node.js para este tutorial en Windows o Linux.

Una cuenta de Azure activa. En caso de no tener ninguna, puede crear una cuenta de evaluación gratuita en tan solo unos minutos. Para obtener más información, consulte la [prueba gratuita de Azure][lnk-free-trial]).

Siga los pasos del artículo de [introducción a la administración de dispositivos](iot-hub-device-management-get-started.md) para crear su instancia de IoT Hub y obtener la cadena de conexión.

[!INCLUDE [iot-hub-get-started-create-hub-pp](../../includes/iot-hub-get-started-create-hub-pp.md)]

## <a name="create-a-simulated-device-app"></a>Creación de una aplicación de dispositivo simulado
En esta sección, creará una aplicación de consola de Node.js que responde a un método directo que se invoca desde la nube, que desencadena una actualización de firmware del dispositivo simulado y utiliza las propiedades notificadas del dispositivo gemelo para habilitar consultas en el dispositivo gemelo con el fin de identificar los dispositivos y cuándo se reiniciaron por última vez.

1. Cree una nueva carpeta vacía denominada "**manageddevice**".  En la carpeta **manageddevice** , cree un archivo package.json con el siguiente comando en el símbolo del sistema.  Acepte todos los valores predeterminados:
   
    ```
    npm init
    ```
2. En el símbolo del sistema en la carpeta **manageddevice**, ejecute el siguiente comando para instalar el **azure-iot-device@dtpreview**paquete del SDK de dispositivo**azure-iot-device-mqtt@dtpreview**:
   
    ```
    npm install azure-iot-device@dtpreview azure-iot-device-mqtt@dtpreview --save
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
6. Agregue la siguiente función que se utilizará para actualizar las propiedades notificadas del dispositivo gemelo actualizadas.
   
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
7. Agregue las siguientes funciones que simularán la descarga y se aplicará a partir de la imagen de firmware.
   
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
8. Agregue la siguiente función que actualizará el estado de actualización de firmware a través de las propiedades notificadas del dispositivo gemelo para esperar la descarga.  Normalmente, los dispositivos se notifican a partir de una actualización disponible y una directiva definida por un administrador provoca que el dispositivo empiece a descargar y aplicar la actualización.  Aquí es donde se ejecutaría la lógica para habilitar esa directiva.  Por motivos de simplicidad, estableceremos un retraso de 4 segundos y continuaremos con la descarga de la imagen de firmware. 
   
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
9. Agregue la siguiente función que actualizará el estado de actualización de firmware a través de las propiedades notificadas del dispositivo gemelo para descargar la imagen de firmware.  A continuación, se simula una descarga de firmware y se actualiza el estado de actualización de firmware para notificar si la descarga se ha realizado correctamente o no.
   
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
10. Agregue la siguiente función que actualizará el estado de actualización de firmware a través de las propiedades notificadas del dispositivo gemelo para aplicar la imagen de firmware.  A continuación, se simula una aplicación de la imagen de firmware y se actualiza el estado de actualización de firmware para notificar si la aplicación se ha realizado correctamente o no.
    
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
11. Agregue la siguiente función que controlar el método firmwareUpdate e inicie el proceso de actualización de firmware en varias fases.
    
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
12. Finalmente, agregue el siguiente código que se conecta a IoT Hub como un dispositivo. 
    
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
> Por simplificar, este tutorial no implementa ninguna directiva de reintentos. En el código de producción, debe implementar directivas de reintentos (por ejemplo, retroceso exponencial), como se sugiere en el artículo de MSDN [Transient Fault Handling][lnk-transient-faults](Control de errores transitorios).
> 
> 

## <a name="trigger-a-remote-firmware-update-on-the-device-using-a-direct-method"></a>Desencadenamiento de una actualización de firmware remota en el dispositivo con un método directo
En esta sección, creará una aplicación de consola de Node.js que inicia una actualización de firmware remota en un dispositivo con un método directo y utiliza las consultas del dispositivo gemelo para obtener periódicamente el estado de la actualización de firmware activa en ese dispositivo.

1. Cree una nueva carpeta vacía denominada **triggerfwupdateondevice**.  En la carpeta **triggerfwupdateondevice** , cree un archivo package.json con el siguiente comando en el símbolo del sistema.  Acepte todos los valores predeterminados:
   
    ```
    npm init
    ```
2. En el símbolo del sistema, en la carpeta **triggerfwupdateondevice**, ejecute el siguiente comando para instalar el paquete del SDK de dispositivo **azure-iothub** y el paquete **azure-iot-device-mqtt**:
   
    ```
    npm install azure-iot-hub@dtpreview --save
    ```
3. Con un editor de texto, cree un nuevo archivo **dmpatterns_getstarted_service.js** en la carpeta **triggerfwupdateondevice**.
4. Agregue las siguientes instrucciones "require" al principio del archivo **dmpatterns_getstarted_service.js**:
   
    ```
    'use strict';
   
    var Registry = require('azure-iothub').Registry;
    var Client = require('azure-iothub').Client;
    ```
5. Agregue las siguientes declaraciones de variable y reemplace los valores de marcador de posición:
   
    ```
    var connectionString = '{device_connectionstring}';
    var registry = Registry.fromConnectionString(connectionString);
    var client = Client.fromConnectionString(connectionString);
    var deviceToUpdate = 'myDeviceId';
    ```
6. Agregue la siguiente función para buscar y mostrar el valor de la propiedad notificada firmwareUpdate.
   
    ```
    var queryTwinFWUpdateReported = function() {
        registry.getTwin(deviceToUpdate, function(err, twin){
            if (err) {
              console.error('Could not query twins: ' + err.constructor.name + ': ' + err.message);
            } else {
              console.log((JSON.stringify(twin.properties.reported.iothubDM.firmwareUpdate)) + "\n");
            }
        });
    };
    ```
7. Agregue la siguiente función para invocar el método firmwareUpdate con el fin de reiniciar el dispositivo de destino:
   
    ```
    var startFirmwareUpdateDevice = function() {
      var params = {
          fwPackageUri: 'https://secureurl'
      };
   
      var methodName = "firmwareUpdate";
      var payloadData =  JSON.stringify(params);
   
      var methodParams = {
        methodName: methodName,
        payload: payloadData,
        timeoutInSeconds: 30
      };
   
      client.invokeDeviceMethod(deviceToUpdate, methodParams, function(err, result) {
        if (err) {
          console.error('Could not start the firmware update on the device: ' + err.message)
        } 
      });
    };
    ```
8. Finalmente, agregue la siguiente función al código para iniciar la secuencia de actualización de firmware e iníciela periódicamente mostrando las propiedades notificadas del dispositivo gemelo:
   
    ```
    startFirmwareUpdateDevice();
    setInterval(queryTwinFWUpdateReported, 500);
    ```
9. Guarde y cierre el archivo **dmpatterns_fwupdate_service.js**.

## <a name="run-the-applications"></a>Ejecución de las aplicaciones
Ahora está preparado para ejecutar las aplicaciones.

1. En el símbolo del sistema dentro de la carpeta **manageddevice**, ejecute el siguiente comando para iniciar la escucha del método directo de reinicio.
   
    ```
    node dmpatterns_fwupdate_device.js
    ```
2. En el símbolo del sistema dentro de la carpeta **triggerfwupdateondevice**, ejecute el siguiente comando para desencadenar el reinicio remoto y la consulta en el dispositivo gemelo para buscar la hora del último reinicio.
   
    ```
    node dmpatterns_fwupdate_service.js
    ```
3. Verá cómo reacciona al método directo si imprime el mensaje.

## <a name="next-steps"></a>Pasos siguientes
En este tutorial, ha utilizado un método directo para desencadenar una actualización de firmware remota en un dispositivo y ha utilizado periódicamente las propiedades notificadas del dispositivo gemelo para entender el progreso del proceso de actualización del firmware.  

Para información sobre cómo ampliar la solución de IoT y programar llamadas de método en varios dispositivos, consulte el tutorial [Schedule and broadcast jobs][lnk-tutorial-jobs] (Programación y difusión de trabajos).

[lnk devtwin]: iot-hub-devguide-device-twins.md
[lnk-c2dmethod]: iot-hub-devguide-direct-methods.md
[lnk-dm-getstarted]: iot-hub-device-management-get-started.md
[lnk-tutorial-jobs]: iot-hub-schedule-jobs.md

[lnk-dev-setup]: https://github.com/Azure/azure-iot-sdks/blob/master/doc/get_started/node-devbox-setup.md
[lnk-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[lnk-transient-faults]: https://msdn.microsoft.com/library/hh680901(v=pandp.50).aspx



<!--HONumber=Oct16_HO2-->


