---
title: Aprovisionamiento de un dispositivo X.509 simulado para Azure IoT Hub mediante Node.js | Microsoft Docs
description: Creación y aprovisionamiento de un dispositivo X.509 simulado mediante el SDK de dispositivos Node.js para el servicio Azure IoT Hub Device Provisioning
services: iot-dps
keywords: ''
author: bryanla
ms.author: v-masebo;bryanla
ms.date: 04/09/2018
ms.topic: hero-article
ms.service: iot-dps
documentationcenter: ''
manager: timlt
ms.devlang: na
ms.custom: mvc
ms.openlocfilehash: 957a0e1561b384468ab22ccdb0a71aea7cbcf1c1
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 04/28/2018
---
# <a name="create-and-provision-an-x509-simulated-device-using-nodejs-device-sdk-for-iot-hub-device-provisioning-service"></a>Creación y aprovisionamiento de un dispositivo X.509 simulado mediante el SDK de dispositivos Node.js para el servicio IoT Hub Device Provisioning
[!INCLUDE [iot-dps-selector-quick-create-simulated-device-x509](../../includes/iot-dps-selector-quick-create-simulated-device-x509.md)]

Estos pasos muestran cómo crear una entrada de inscripción en el servicio Device Provisioning, simular un dispositivo X.509 en la máquina de desarrollo, conectar el dispositivo simulado con el servicio Device Provisioning y registrar el dispositivo en el centro de IoT mediante el [SDK del dispositivo de Node-js de Azure IoT Hub](https://github.com/Azure/azure-iot-sdk-node).

Si no está familiarizado con el proceso de aprovisionamiento automático, no olvide revisar los [conceptos sobre aprovisionamiento automático](concepts-auto-provisioning.md). Asegúrese de completar los pasos descritos en [Configuración del servicio Azure IoT Hub Device Provisioning con Azure Portal](./quick-setup-auto-provision.md) antes de continuar. 

[!INCLUDE [IoT DPS basic](../../includes/iot-dps-basic.md)]

## <a name="prepare-the-environment"></a>Preparación del entorno 

1. Complete los pasos descritos en [Setup IoT Hub Device Provisioning Service with the Azure portal](./quick-setup-auto-provision.md) (Configuración del servicio Azure IoT Hub Device Provisioning con Azure Portal) antes de continuar.

2. Asegúrese de que tiene [Node.js v4.0 o posterior](https://nodejs.org) instalado en su máquina.

3. Asegúrese de que [Git](https://git-scm.com/download/) está instalado en su máquina y se agrega a las variables de entorno accesibles para la ventana de comandos. 

4. Asegúrese de que [OpenSSL](https://www.openssl.org/) está instalado en su máquina y se agrega a las variables de entorno accesibles para la ventana de comandos. Esta biblioteca se puede compilar e instalar desde el origen o descargarse e instalarse desde el sitio web de un [tercero](https://wiki.openssl.org/index.php/Binaries) como [este](https://sourceforge.net/projects/openssl/). 

    > [!NOTE]
    > Si ya ha creado los certificados X.509 _raíz_, _intermedio_ u _hoja_, puede omitir este paso y todos los siguientes relativos a la generación de certificados.
    >

## <a name="create-a-self-signed-x509-device-certificate-and-individual-enrollment-entry"></a>Creación de un certificado de dispositivo X.509 autofirmado y una entrada de inscripción individual

1. Abra el símbolo del sistema. Clone el repositorio de GitHub para el código de ejemplo:
    
    ```cmd/sh
    git clone https://github.com/Azure/azure-iot-sdk-node.git --recursive
    ```

2. Vaya hasta el script de generación de certificados y compílelo. 

    ```cmd/sh
    cd azure-iot-sdk-node/provisioning/tools
    npm install
    ```

3. Cree el certificado X.509 _de hoja_ mediante la ejecución del script con su propio _nombre-de-certificado_. Tenga en cuenta que el nombre común del certificado hoja se convierte en el [identificador del registro](https://docs.microsoft.com/azure/iot-dps/concepts-device#registration-id), así que asegúrese de usar solo minúsculas, números y guiones.

    ```cmd/sh
    node create_test_cert.js device {certificate-name}
    ```

4. Inicie sesión en [Azure Portal](https://portal.azure.com), haga clic en el botón **Todos los recursos** situado en el menú izquierdo y abra la instancia del servicio Device Provisioning.

5. En la hoja de resumen del servicio Device Provisioning, seleccione **Manage enrollments** (Administrar inscripciones). Seleccione la pestaña **Individual Enrollments** (Inscripciones individuales) y haga clic en el botón **Add** (Agregar) de la parte superior. 

6. En el panel **Agregar inscripción**, escriba la siguiente información:
    - Seleccione **X.509** como *Mecanismo* de atestación de identidad.
    - En el *Archivo .pem o .cer de certificado principal*, haga clic en *Seleccionar un archivo* para seleccionar el archivo de certificado **{nombre-de-certificado}_cert.pem** creado en los pasos anteriores.  
    - De forma opcional, puede proporcionar la siguiente información:
      - Seleccione un centro de IoT vinculado con el servicio de aprovisionamiento.
      - Escriba un identificador de dispositivo único. Asegúrese de evitar datos confidenciales al asignar nombre al dispositivo. 
      - Actualice el **Estado inicial del dispositivo gemelo** con la configuración inicial deseada para el dispositivo.
   - Una vez completado, haga clic en el botón **Guardar**. 

    [![Agregar inscripción individual para la atestación X.509 en el portal](./media/quick-create-simulated-device-x509-node/individual-enrollment.png)](./media/quick-create-simulated-device-x509-node/individual-enrollment.png#lightbox)

    Al inscribir el dispositivo correctamente, el dispositivo X.509 aparece como **{nombredelcertificado}** en la columna *Id. de registro* de la pestaña *Inscripciones individuales*. Anote este valor para más adelante.

## <a name="simulate-the-device"></a>Simulación del dispositivo

El [SDK de dispositivo de Node.js de Azure IoT Hub](https://github.com/Azure/azure-iot-sdk-node) proporciona una manera sencilla de simular un dispositivo. Para más información, consulte los [conceptos de dispositivos](https://docs.microsoft.com/azure/iot-dps/concepts-device).

1. En Azure Portal, seleccione la hoja **Información general** para su servicio Device Provisioning y anote los valores del **_punto de conexión de dispositivo global_** y del **_ámbito de identificador_**.

    ![Extracción de información del punto de conexión DPS de la hoja del portal](./media/quick-create-simulated-device-x509-node/extract-dps-endpoints.png) 

2. Copie el _certificado_ y la _clave_ en la carpeta de ejemplo.

    ```cmd/sh
    copy .\{certificate-name}_cert.pem ..\device\samples\{certificate-name}_cert.pem
    copy .\{certificate-name}_key.pem ..\device\samples\{certificate-name}_key.pem
    ```

3. Vaya al script de prueba de dispositivo y compile el proyecto. 

    ```cmd/sh
    cd ..\device\samples
    npm install
    ```

4. Edite el archivo **register\_x509.js**. Guarde el archivo después de realizar los cambios siguientes.
    - Reemplace `provisioning host` con el **_punto de conexión de dispositivo global_** que anotó en el **paso 1** anterior.
    - Reemplace `id scope` con el **_Id. de ámbito_** que anotó en el **paso 1** anterior. 
    - Reemplace `registration id` por el **_Identificador de registro_** que anotó en la sección anterior.
    - Reemplace `cert filename` y `key filename` con los archivos que copió en el **paso 2** anterior. 

5. Ejecute el script y verifique que el dispositivo se aprovisione correctamente.

    ```cmd/sh
    node register_x509.js
    ```   

6. En el portal, navegue hasta el centro de IoT Hub vinculado a su servicio de aprovisionamiento y abra la hoja **IoT Devices** (Dispositivos IoT). Si el dispositivo X.509 simulado se aprovisiona correctamente con el centro, su identificador de dispositivo aparecerá en la hoja **IoT Devices** (Dispositivos IoT) con el *ESTADO* **habilitado**. Es posible que deba hacer clic en el botón **Actualizar** situado en la parte superior si ya ha abierto la hoja antes de ejecutar la aplicación de ejemplo del dispositivo. 

    ![El dispositivo se registra con el centro de IoT](./media/quick-create-simulated-device-x509-node/hub-registration.png) 

    Si ha cambiado el valor predeterminado de *Estado inicial del dispositivo gemelo* en la entrada de inscripción para el dispositivo, el dispositivo puede extraer el estado gemelo deseado desde el centro y actuar en consecuencia. Para más información, consulte [Información y uso de dispositivos gemelos en IoT Hub](../iot-hub/iot-hub-devguide-device-twins.md).


## <a name="clean-up-resources"></a>Limpieza de recursos

Si piensa seguir trabajando con el ejemplo de cliente de dispositivo y explorándolo, no limpie los recursos que se crean en esta guía de inicio rápido. Si no va a continuar, use el siguiente comando para eliminar todos los recursos creados.

1. Cierre la ventana de salida de ejemplo del cliente del dispositivo en su máquina.
2. Desde el menú de la izquierda en Azure Portal, haga clic en **Todos los recursos** y seleccione el servicio Device Provisioning. Abra la hoja **Administrar inscripciones** de su servicio y, a continuación, haga clic en la pestaña **Inscripciones individuales**. Seleccione el *ID. DE REGISTRO* del dispositivo que inscribió en esta guía de inicio rápido y haga clic en el botón **Eliminar** situado en la parte superior. 
3. Desde el menú de la izquierda en Azure Portal, haga clic en **Todos los recursos** y seleccione su centro de IoT. Abra la hoja **IoT Devices** (Dispositivos IoT) de su centro, seleccione el *ID. DE DISPOSITIVO* del dispositivo que registró en esta guía de inicio rápido y, a continuación, haga clic en el botón **Eliminar** situado en la parte superior.


## <a name="next-steps"></a>Pasos siguientes

En esta guía de inicio rápido ha creado un dispositivo simulado X.509 y lo ha aprovisionado con IoT Hub mediante el servicio Azure IoT Hub Device Provisioning en el portal. Para obtener información sobre cómo inscribir el dispositivo X.509 mediante programación, siga la guía de inicio rápido para la inscripción de dispositivos X.509 mediante programación. 

> [!div class="nextstepaction"]
> [Guía de inicio rápido de Azure: Inscripción de dispositivos X.509 al servicio Azure IoT Hub Device Provisioning](quick-enroll-device-x509-node.md)
