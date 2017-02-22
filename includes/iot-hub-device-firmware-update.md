## <a name="create-a-simulated-device-app"></a>Creación de una aplicación de dispositivo simulado
En esta sección:

* Creará una aplicación de consola de Node.js que responda a un método directo que se llama desde la nube.
* Desencadenará una actualización de firmware simulada.
* Usará las propiedades notificadas para habilitar consultas de dispositivos gemelos a fin de identificar los dispositivos y cuándo se actualizó su firmware por última vez.

1. Cree una nueva carpeta vacía denominada **manageddevice**.  En la carpeta **manageddevice** , cree un archivo package.json con el siguiente comando en el símbolo del sistema. Acepte todos los valores predeterminados:
   
    ```
    npm init
    ```
2. En el símbolo del sistema, en la carpeta **manageddevice**, ejecute el siguiente comando para instalar los paquetes del SDK de dispositivo **azure-iot-device** y **azure-iot-device-mqtt**:
   
    ```
    npm install azure-iot-device azure-iot-device-mqtt --save
    ```
3. Con un editor de texto, cree un archivo **dmpatterns_fwupdate_device.js** en la carpeta **manageddevice**.

4. Agregue las siguientes instrucciones "require" al principio del archivo **dmpatterns_fwupdate_device.js**:
   
    ```
    'use strict';
   
    var Client = require('azure-iot-device').Client;
    var Protocol = require('azure-iot-device-mqtt').Mqtt;
    ```
5. Agregue una variable **connectionString** y utilícela para crear una instancia de **cliente**. Reemplace el marcador de posición `{yourdeviceconnectionstring}` por la cadena de conexión que anteriormente anotó en la sección sobre la creación de una identidad de dispositivo:
   
    ```
    var connectionString = '{yourdeviceconnectionstring}';
    var client = Client.fromConnectionString(connectionString, Protocol);
    ```
6. Agregue la siguiente función que se usa para actualizar las propiedades notificadas:
   
    ```
    var reportFWUpdateThroughTwin = function(twin, firmwareUpdateValue) {
      var patch = {
          iothubDM : {
            firmwareUpdate : firmwareUpdateValue
          }
      };
   
      twin.properties.reported.update(patch, function(err) {
        if (err) throw err;
        console.log('twin state reported: ' + firmwareUpdateValue.status);
      });
    };
    ```
7. Agregue las siguientes funciones que simulan descargar y aplicar la imagen de firmware:
   
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
8. Agregue la siguiente función que actualiza el estado de actualización del firmware mediante las propiedades notificadas a **waiting**. Normalmente, los dispositivos reciben una notificación sobre una actualización disponible y una directiva definida por un administrador provoca que el dispositivo empiece a descargar y aplicar la actualización. Esta función es donde se debe ejecutar la lógica para habilitar esa directiva. Para simplificar, el ejemplo se muestra cuatro segundos antes de proceder a la descarga de la imagen de firmware:
   
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
9. Agregue la siguiente función que actualiza el estado de actualización de firmware mediante las propiedades notificadas a **downloading**. La función simula entonces una descarga de firmware y actualiza finalmente el estado de actualización del firmware a **downloadFailed** o **downloadComplete**:
   
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
10. Agregue la siguiente función que actualiza el estado de actualización del firmware mediante las propiedades notificadas a **applying**. La función simula entonces la aplicación de la imagen de firmware y actualiza finalmente el estado de actualización del firmware a **applyFailed** o **applyComplete**:
    
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
11. Agregue la siguiente función que controla el método directo **firmwareUpdate** e inicia el proceso de actualización del firmware de varias fases:
    
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
      var fwPackageUri = request.payload.fwPackageUri;
    
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
12. Por último, agregue el código siguiente que conecta con el centro de IoT:
    
    ```
    client.open(function(err) {
      if (err) {
        console.error('Could not connect to IotHub client');
      }  else {
        console.log('Client connected to IoT Hub.  Waiting for firmwareUpdate direct method.');
      }
    
      client.onDeviceMethod('firmwareUpdate', onFirmwareUpdate);
    });
    ```

> [!NOTE]
> Por simplificar, este tutorial no implementa ninguna directiva de reintentos. En el código de producción, debe implementar directivas de reintentos (por ejemplo, retroceso exponencial), como se sugiere en el artículo de MSDN [Transient Fault Handling][lnk-transient-faults](Control de errores transitorios).
> 
> 

<!--HONumber=Feb17_HO1-->


