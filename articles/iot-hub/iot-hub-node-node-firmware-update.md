---
title: "Actualización de firmware de dispositivos con IoT Hub de Azure (Node) | Microsoft Docs"
description: "Describe cómo usar la administración de dispositivos en IoT Hub de Azure para iniciar una actualización de firmware del dispositivo. Usará el SDK de IoT de Azure para Node.js para implementar la aplicación de dispositivo simulado y una aplicación de servicio que desencadena la actualización del firmware."
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
ms.date: 09/07/2017
ms.author: juanpere
ms.openlocfilehash: e169367592b25ea45c3d1017937316a3b3b538b8
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="use-device-management-to-initiate-a-device-firmware-update-nodenode"></a>Use la administración de dispositivos para iniciar una actualización de firmware del dispositivo (Node/Node)
[!INCLUDE [iot-hub-selector-firmware-update](../../includes/iot-hub-selector-firmware-update.md)]

En el tutorial de [Introducción a la administración de dispositivos][lnk-dm-getstarted], vimos cómo usar los primitivos de [dispositivo gemelo][lnk-devtwin] y [métodos directos][lnk-c2dmethod] para reiniciar de forma remota un dispositivo. Este tutorial utiliza a los mismos tipos primitivos de IoT Hub. Además, proporciona orientación y muestra cómo realizar una actualización de firmware simulada de extremo a extremo.  Este patrón se utiliza en la implementación de actualización de firmware para el ejemplo de dispositivo Intel Edison.

En este tutorial se muestra cómo realizar las siguientes acciones:

* Crear una aplicación de consola de Node.js que llame al método directo firmwareUpdate en la aplicación de dispositivo simulado a través de su IoT Hub.
* Cree una aplicación de dispositivo simulado que implemente un método directo **firmwareUpdate**. Este método inicia un proceso de varias fases que espera para descargar la imagen de firmware, la descarga y, por último, la aplica. Durante cada fase de la actualización, el dispositivo usa las propiedades notificadas para informar sobre el progreso.

Al final de este tutorial tendrá dos aplicaciones de consola de Node.js:

**dmpatterns_fwupdate_service.js**, que llama a un método directo en la aplicación de dispositivo simulado, muestra la respuesta y muestra periódicamente (cada 500 ms) las propiedades notificadas actualizadas.

**dmpatterns_fwupdate_device.js**, que se conecta a IoT Hub con la identidad del dispositivo creada anteriormente, recibe un método directo firmwareUpdate y se ejecuta mediante un proceso de varios estado para simular una actualización de firmware; entre ellas, la espera para la descarga de imágenes, la descarga de la nueva imagen y, finalmente, la aplicación de dicha imagen.

Para completar este tutorial, necesitará lo siguiente:

* Node.js versión 4.0.x o posterior. <br/>  En [Preparación del entorno de desarrollo][lnk-dev-setup] se describe cómo instalar Node.js para este tutorial en Windows o Linux.
* Una cuenta de Azure activa. (En caso de no tenerla, puede crear una [cuenta gratuita][lnk-free-trial] en solo unos minutos).

Siga los pasos del artículo de [introducción a la administración de dispositivos](iot-hub-node-node-device-management-get-started.md) para crear su instancia de IoT Hub y obtener la cadena de conexión de IoT Hub.

[!INCLUDE [iot-hub-get-started-create-hub](../../includes/iot-hub-get-started-create-hub.md)]

[!INCLUDE [iot-hub-get-started-create-device-identity](../../includes/iot-hub-get-started-create-device-identity.md)]

## <a name="trigger-a-remote-firmware-update-on-the-device-using-a-direct-method"></a>Desencadenamiento de una actualización de firmware remota en el dispositivo con un método directo
En esta sección, creará una aplicación de consola de Node.js que inicia una actualización de firmware remota en un dispositivo. La aplicación usa un método directo para iniciar la actualización y utiliza consultas de dispositivos gemelos para obtener periódicamente el estado de la actualización del firmware activa.

1. Cree una carpeta vacía denominada **triggerfwupdateondevice**.  En la carpeta **triggerfwupdateondevice** , cree un archivo package.json con el siguiente comando en el símbolo del sistema.  Acepte todos los valores predeterminados:
   
    ```
    npm init
    ```
2. En el símbolo del sistema, en la carpeta **triggerfwupdateondevice**, ejecute el siguiente comando para instalar el paquete **azure-iot-hub**:
   
    ```
    npm install azure-iothub --save
    ```
3. Con un editor de texto, cree un archivo **dmpatterns_getstarted_service.js** en la carpeta **triggerfwupdateondevice**.
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
8. Finalmente, agregue la siguiente función al código para iniciar la secuencia de actualización de firmware e iníciela periódicamente mostrando las propiedades notificadas:
   
    ```
    startFirmwareUpdateDevice();
    setInterval(queryTwinFWUpdateReported, 500);
    ```
9. Guarde y cierre el archivo **dmpatterns_fwupdate_service.js**.

[!INCLUDE [iot-hub-device-firmware-update](../../includes/iot-hub-device-firmware-update.md)]

## <a name="run-the-apps"></a>Ejecución de las aplicaciones
Ya está preparado para ejecutar las aplicaciones.

1. En el símbolo del sistema dentro de la carpeta **manageddevice**, ejecute el siguiente comando para iniciar la escucha del método directo de reinicio.
   
    ```
    node dmpatterns_fwupdate_device.js
    ```
2. En el símbolo del sistema dentro de la carpeta **triggerfwupdateondevice**, ejecute el siguiente comando para desencadenar el reinicio remoto y la consulta en el dispositivo gemelo para buscar la hora del último reinicio.
   
    ```
    node dmpatterns_fwupdate_service.js
    ```
3. Verá la respuesta del dispositivo al método directo en la consola.

## <a name="next-steps"></a>Pasos siguientes
En este tutorial, ha usado un método directo para desencadenar una actualización de firmware remota en un dispositivo y ha utilizado las propiedades notificadas para entender el progreso del proceso de actualización del firmware.

Para información sobre cómo ampliar la solución de IoT y programar llamadas de método en varios dispositivos, consulte el tutorial [Programación de trabajos en varios dispositivos][lnk-tutorial-jobs].

[lnk-devtwin]: iot-hub-devguide-device-twins.md
[lnk-c2dmethod]: iot-hub-devguide-direct-methods.md
[lnk-dm-getstarted]: iot-hub-node-node-device-management-get-started.md
[lnk-tutorial-jobs]: iot-hub-node-node-schedule-jobs.md

[lnk-dev-setup]: https://github.com/Azure/azure-iot-sdk-node/tree/master/doc/node-devbox-setup.md
[lnk-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[lnk-transient-faults]: https://msdn.microsoft.com/library/hh680901(v=pandp.50).aspx
