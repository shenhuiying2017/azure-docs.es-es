---
title: Aprovisionamiento de un dispositivo de TPM simulado para Azure IoT Hub mediante Node.js | Microsoft Docs
description: "Guía de inicio rápido de Azure: Creación y aprovisionamiento de un dispositivo de TPM simulado mediante el SDK de dispositivos Node.js para el servicio Azure IoT Hub Device Provisioning"
services: iot-dps
keywords: 
author: msebolt
ms.author: v-masebo
ms.date: 03/01/2018
ms.topic: hero-article
ms.service: iot-dps
documentationcenter: 
manager: timlt
ms.devlang: na
ms.custom: mvc
ms.openlocfilehash: 3cc667286e9c643f31d9c704a8a2dfac18312bb3
ms.sourcegitcommit: 0b02e180f02ca3acbfb2f91ca3e36989df0f2d9c
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 03/05/2018
---
# <a name="create-and-provision-a-simulated-tpm-device-using-nodejs-device-sdk-for-iot-hub-device-provisioning-service"></a>Creación y aprovisionamiento de un dispositivo de TPM simulado mediante el SDK de dispositivos Python para el servicio IoT Hub Device Provisioning

[!INCLUDE [iot-dps-selector-quick-create-simulated-device-tpm](../../includes/iot-dps-selector-quick-create-simulated-device-tpm.md)]

Estos pasos muestran cómo crear un dispositivo simulado en la máquina de desarrollo en la que se ejecuta el sistema operativo Windows, ejecutar el simulador de Windows TPM como el [módulo de seguridad de Hardware (HSM)](https://azure.microsoft.com/blog/azure-iot-supports-new-security-hardware-to-strengthen-iot-security/) del dispositivo y usar el ejemplo de código para conectar este dispositivo simulado con el servicio Device Provisioning y un centro de IoT. 

Asegúrese de completar los pasos descritos en [Configuración de un servicio Azure IoT Hub Device Provisioning con Azure Portal](./quick-setup-auto-provision.md) antes de continuar.


## <a name="prepare-the-environment"></a>Preparación del entorno 

1. Asegúrese de que tiene [Node.js v4.0 o posterior](https://nodejs.org) instalado en su máquina.

1. Asegúrese de que `git` está instalado en su máquina y se agrega a las variables de entorno accesibles para la ventana de comandos. Consulte las [herramientas de cliente de Git de Software Freedom Conservancy](https://git-scm.com/download/) para instalar la versión más reciente de las herramientas `git`, lo que incluye **Git Bash**, la aplicación de línea de comandos que puede usar para interactuar con su repositorio de Git local. 


## <a name="simulate-a-tpm-device"></a>Simulación del dispositivo de TPM

1. Abra un símbolo del sistema o Git Bash. Clone el `azure-utpm-c` repositorio de GitHub:
    
    ```cmd/sh
    git clone https://github.com/Azure/azure-utpm-c.git
    ```

1. Vaya a la carpeta raíz de GitHub y ejecute el simulador de [TPM](https://docs.microsoft.com/windows/device-security/tpm/trusted-platform-module-overview). Atiende en un socket en los puertos 2321 y 2322. No cierre esta ventana de comandos; este simulador debe seguir en funcionamiento hasta el final de esta guía: 

    ```cmd/sh
    .\azure-utpm-c\tools\tpm_simulator\Simulator.exe
    ```

1. Cree una nueva carpeta vacía denominada **registerdevice**. En la carpeta **registerdevice**, cree un archivo package.json escribiendo el siguiente comando en el símbolo del sistema. Asegúrese de que responde a todas las preguntas que realiza `npm` o acepte los valores predeterminados si le convienen:
   
    ```cmd/sh
    npm init
    ```

1. Instale los siguientes paquetes precursores:

    ```cmd/sh
    npm install node-gyp -g
    npm install ffi -g
    ```

    > [!NOTE]
    > Hay algunos problemas conocidos al instalar los paquetes anteriores. Para resolverlos, ejecute `npm install --global --production windows-build-tools` desde un símbolo del sistema en modo **Ejecutar como administrador**, ejecute `SET VCTargetsPath=C:\Program Files (x86)\MSBuild\Microsoft.Cpp\v4.0\V140` después de reemplazar la ruta de acceso por la versión instalada, y, luego, vuelva a ejecutar los comandos de la instalación anterior.
    >

1. Instale los siguientes paquetes que contienen los componentes utilizados durante el registro:

    - un cliente de seguridad que funciona con TPM: `azure-iot-security-tpm`
    - un transporte para que el dispositivo se conecte al servicio Device Provisioning: `azure-iot-provisioning-device-http` o `azure-iot-provisioning-device-amqp`
    - un cliente que use el transporte y el cliente de seguridad: `azure-iot-provisioning-device`

    Una vez que el dispositivo esté registrado, puede utilizar los paquetes habituales de cliente de dispositivos de IoT Hub para conectar el dispositivo con las credenciales que proporcionan durante el registro. Necesitará:

    - el cliente de dispositivo: `azure-iot-device`
    - un transporte: `azure-iot-device-amqp`, `azure-iot-device-mqtt` o `azure-iot-device-http`
    - el cliente de seguridad que ya ha instalado: `azure-iot-security-tpm`

    > [!NOTE]
    > En los ejemplos siguientes se usan los transportes `azure-iot-provisioning-device-http` y `azure-iot-device-mqtt`.
    > 

    Para instalar todos ellos a la vez, ejecute el comando siguiente en el símbolo del sistema en la carpeta **registereddevice**:

        ```cmd/sh
        npm install --save azure-iot-device azure-iot-device-mqtt azure-iot-security-tpm azure-iot-provisioning-device-http azure-iot-provisioning-device
        ```

1. Con un editor de texto, cree un nuevo archivo, **ExtractDevice.js**, en la carpeta **registerdevice**.

1. Agregue las siguientes instrucciones `require` al principio del archivo **ExtractDevice.js** :
   
    ```
    'use strict';

    var tpmSecurity = require('azure-iot-security-tpm');
    var tssJs = require("tss.js");

    var myTpm = new tpmSecurity.TpmSecurityClient(undefined, new tssJs.Tpm(true));
    ```

1. Agregue la siguiente función para implementar el método:
   
    ```
    myTpm.getEndorsementKey(function(err, endorsementKey) {
      if (err) {
        console.log('The error returned from get key is: ' + err);
      } else {
        console.log('the endorsement key is: ' + endorsementKey.toString('base64'));
        myTpm.getRegistrationId((getRegistrationIdError, registrationId) => {
          if (getRegistrationIdError) {
            console.log('The error returned from get registration id is: ' + getRegistrationIdError);
          } else {
            console.log('The Registration Id is: ' + registrationId);
            process.exit();
          }
        });
      }
    });
    ```

1. Guarde y cierre el archivo **ExtractDevice.js**. Ejecución del ejemplo:

    ```cmd/sh
    node ExtractDevice.js
    ```

1. La ventana de salida muestra la **_clave de aprobación_** y el **_id. de registro_** necesarios para la inscripción del dispositivo. Anote estos valores. 


## <a name="create-a-device-entry"></a>Creación de una entrada del dispositivo

1. Inicie sesión en Azure Portal, haga clic en el botón **Todos los recursos** situado en el menú izquierdo y abra el servicio Device Provisioning.

1. En la hoja de resumen del servicio Device Provisioning, seleccione **Manage enrollments** (Administrar inscripciones). Seleccione la pestaña **Individual Enrollments** (Inscripciones individuales) y haga clic en el botón **Add** (Agregar) de la parte superior. 

1. En **Agregar entrada de la lista de inscripción**, escriba la siguiente información:
    - Seleccione **TPM** como *Mecanismo* de atestación de identidad.
    - Escriba el *Identificador de registro* y la *Clave de aprobación* del dispositivo TPM.
    - De forma opcional, puede proporcionar la siguiente información:
        - Seleccione un centro de IoT vinculado con el servicio de aprovisionamiento.
        - Escriba un identificador de dispositivo único. Asegúrese de evitar datos confidenciales al asignar nombre al dispositivo.
        - Actualice el **Estado inicial del dispositivo gemelo** con la configuración inicial deseada para el dispositivo.
    - Una vez completado, haga clic en el botón **Guardar**. 

    ![Especificación de la información de inscripción del dispositivo en la hoja de portal](./media/quick-create-simulated-device/enter-device-enrollment.png)  

   Al inscribir el dispositivo correctamente, el valor de *Id. de registro* del dispositivo aparece en la lista de la pestaña *Inscripciones individuales*. 


## <a name="register-the-device"></a>Registro del dispositivo

1. En Azure Portal, seleccione la hoja **Información general** del servicio Device Provisioning y anote los valores de **_Punto de conexión global del dispositivo_** y **_Ámbito de id_**.

    ![Extracción de información del punto de conexión DPS de la hoja del portal](./media/quick-create-simulated-device/extract-dps-endpoints.png) 

1. Con un editor de texto, cree un nuevo archivo, **RegisterDevice.js**, en la carpeta **registerdevice**.

1. Agregue las siguientes instrucciones `require` al principio del archivo **RegisterDevice.js** :
   
    ```
    'use strict';

    var ProvisioningTransport = require('azure-iot-provisioning-device-http').Http;
    var iotHubTransport = require('azure-iot-device-mqtt').Mqtt;
    var Client = require('azure-iot-device').Client;
    var Message = require('azure-iot-device').Message;
    var tpmSecurity = require('azure-iot-security-tpm');
    var ProvisioningDeviceClient = require('azure-iot-provisioning-device').ProvisioningDeviceClient;
    ```

    > [!NOTE]
    > El **SDK de IoT de Azure para Node.js** admite protocolos adicionales, como _AMQP_, _AMQP WS_ y _MQTT WS_.  Para ver más ejemplos, consulte [Samples for the Azure IoT Device Provisioning Service SDK for Node.js](https://github.com/Azure/azure-iot-sdk-node/tree/master/provisioning/device/samples) (Ejemplos del SDK de servicio de aprovisionamiento de dispositivos de Azure IoT para Node.js).
    > 

1. Agregue las variables **globalDeviceEndpoint** e **idScope** y úselas para crear una instancia de **ProvisioningDeviceClient**. Reemplace **{globalDeviceEndpoint}** e **{idScope}** por los valores de **_Punto de conexión global del dispositivo_**  y **_Ámbito de id_**  del **paso 1**:
   
    ```
    var provisioningHost = '{globalDeviceEndpoint}';
    var idScope = '{idScope}';

    var tssJs = require("tss.js");
    var securityClient = new tpmSecurity.TpmSecurityClient('', new tssJs.Tpm(true));
    // if using non-simulated device, replace the above line with following:
    //var securityClient = new tpmSecurity.TpmSecurityClient();

    var provisioningClient = ProvisioningDeviceClient.create(provisioningHost, idScope, new ProvisioningTransport(), securityClient);
    ```

1. Agregue la siguiente función para implementar el método en el dispositivo:
   
    ```
    provisioningClient.register(function(err, result) {
      if (err) {
        console.log("error registering device: " + err);
      } else {
        console.log('registration succeeded');
        console.log('assigned hub=' + result.registrationState.assignedHub);
        console.log('deviceId=' + result.registrationState.deviceId);
        var tpmAuthenticationProvider = tpmSecurity.TpmAuthenticationProvider.fromTpmSecurityClient(result.registrationState.deviceId, result.registrationState.assignedHub, securityClient);
        var hubClient = Client.fromAuthenticationProvider(tpmAuthenticationProvider, iotHubTransport);

        var connectCallback = function (err) {
          if (err) {
            console.error('Could not connect: ' + err.message);
          } else {
            console.log('Client connected');
            var message = new Message('Hello world');
            hubClient.sendEvent(message, printResultFor('send'));
          }
        };

        hubClient.open(connectCallback);

        function printResultFor(op) {
          return function printResult(err, res) {
            if (err) console.log(op + ' error: ' + err.toString());
            if (res) console.log(op + ' status: ' + res.constructor.name);
            process.exit(1);
          };
        }
      }
    });
    ```

1. Guarde el archivo **RegisterDevice.js** y ciérrelo. Ejecución del ejemplo:

    ```cmd/sh
    node RegisterDevice.js
    ```

1. Tenga en cuenta los mensajes que simulan el arranque del dispositivo y la conexión al servicio Device Provisioning para obtener la información del centro de IoT. Si se aprovisiona correctamente el dispositivo simulado para el centro IoT Hub vinculado con el servicio de aprovisionamiento, el identificador de dispositivo se muestra en la hoja **IoT Devices** (IoT Devices) del centro. 

    ![El dispositivo se registra con el centro de IoT](./media/quick-create-simulated-device/hub-registration.png) 

    Si ha cambiado el valor predeterminado de *Estado inicial del dispositivo gemelo* en la entrada de inscripción para el dispositivo, el dispositivo puede extraer el estado gemelo deseado desde el centro y actuar en consecuencia. Para más información, consulte [Información y uso de dispositivos gemelos en IoT Hub](../iot-hub/iot-hub-devguide-device-twins.md)


## <a name="clean-up-resources"></a>Limpieza de recursos

Si piensa seguir trabajando con el ejemplo de cliente de dispositivo y explorándolo, no limpie los recursos que se crean en esta guía de inicio rápido. Si no va a continuar, use el siguiente comando para eliminar todos los recursos creados.

1. Cierre la ventana de salida de ejemplo del cliente del dispositivo en su máquina.
1. Cierre la ventana del simulador de TPM en su máquina.
1. Desde el menú de la izquierda en Azure Portal, haga clic en **Todos los recursos** y seleccione el servicio Device Provisioning. Abra la hoja **Administrar inscripciones** de su servicio y, a continuación, haga clic en la pestaña **Inscripciones individuales**. Seleccione el *ID. DE REGISTRO* del dispositivo que inscribió en esta guía de inicio rápido y haga clic en el botón **Eliminar** situado en la parte superior. 
1. Desde el menú de la izquierda en Azure Portal, haga clic en **Todos los recursos** y seleccione su centro de IoT. Abra la hoja **IoT Devices** (Dispositivos IoT) de su centro, seleccione el *ID. DE DISPOSITIVO* del dispositivo que registró en esta guía de inicio rápido y, a continuación, haga clic en el botón **Eliminar** situado en la parte superior.


## <a name="next-steps"></a>Pasos siguientes

En este tutorial, ha creado un dispositivo de TPM simulado en su máquina y lo ha aprovisionado con IoT Hub mediante el servicio Azure IoT Hub Device Provisioning. Para obtener información sobre cómo inscribir el dispositivo de TPM mediante programación, siga la guía de inicio rápido para la inscripción de un dispositivo de TPM mediante programación. 

> [!div class="nextstepaction"]
> [Guía de inicio rápido de Azure: Inscripción de dispositivos de TPM al Servicio Azure IoT Hub Device Provisioning](quick-enroll-device-tpm-node.md)
