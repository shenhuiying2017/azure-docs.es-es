---
title: Aprovisionamiento de un dispositivo X.509 simulado para Azure IoT Hub mediante Java | Microsoft Docs
description: 'Guía de inicio rápido de Azure: creación y aprovisionamiento de un dispositivo X.509 simulado mediante el SDK de dispositivos Java para el servicio Azure IoT Hub Device Provisioning'
services: iot-dps
keywords: ''
author: bryanla
ms.author: v-masebo;bryanla
ms.date: 04/09/2018
ms.topic: quickstart
ms.service: iot-dps
documentationcenter: ''
manager: timlt
ms.devlang: java
ms.custom: mvc
ms.openlocfilehash: 72460e19d202b79369844db6fea24f2914c8bbbe
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 04/16/2018
---
# <a name="create-and-provision-a-simulated-x509-device-using-java-device-sdk-for-iot-hub-device-provisioning-service"></a>Creación y aprovisionamiento de un dispositivo X.509 simulado mediante el SDK de dispositivos Java para el servicio Azure IoT Hub Device Provisioning
[!INCLUDE [iot-dps-selector-quick-create-simulated-device-x509](../../includes/iot-dps-selector-quick-create-simulated-device-x509.md)]

Estos pasos muestran cómo simular un dispositivo X.509 en la máquina de desarrollo en la que se ejecuta el sistema operativo Windows, y usar un ejemplo de código para conectar este dispositivo simulado con el servicio Device Provisioning y IoT Hub. 

Si no está familiarizado con el proceso de aprovisionamiento automático, no olvide revisar los [conceptos sobre aprovisionamiento automático](concepts-auto-provisioning.md). Asegúrese de completar los pasos descritos en [Configuración del servicio Azure IoT Hub Device Provisioning con Azure Portal](./quick-setup-auto-provision.md) antes de continuar. 

## <a name="prepare-the-environment"></a>Preparación del entorno 

1. Asegúrese de que tiene [Java SE Development Kit 8](http://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html) instalado en su máquina.

2. Descargue e instale [Maven](https://maven.apache.org/install.html).

3. Asegúrese de que Git está instalado en la máquina y se ha agregado a las variables de entorno accesibles desde la ventana de comandos. Consulte las [herramientas de cliente de Git de Software Freedom Conservancy](https://git-scm.com/download/) para instalar la versión más reciente de las herramientas `git`, lo que incluye **Git Bash**, la aplicación de línea de comandos que puede usar para interactuar con su repositorio de Git local. 

4. Abra el símbolo del sistema. Clone el repositorio GitHub para el ejemplo de código de simulación de dispositivo:
    
    ```cmd/sh
    git clone https://github.com/Azure/azure-iot-sdk-java.git --recursive
    ```
5. Vaya al directorio `azure-iot-sdk-`java raíz y compile el proyecto para descargar todos los paquetes necesarios.
   
   ```cmd/sh
   cd azure-iot-sdk-java
   mvn install -DskipTests=true
   ```
6. Navegue hasta el proyecto de generación de certificados y compílelo. 

    ```cmd/sh
    cd azure-iot-sdk-java/provisioning/provisioning-tools/provisioning-x509-cert-generator
    mvn clean install
    ```

## <a name="create-a-self-signed-x509-device-certificate-and-individual-enrollment-entry"></a>Creación de un certificado de dispositivo X.509 autofirmado y una entrada de inscripción individual

1. Mediante el símbolo del sistema de los pasos anteriores, vaya a la carpeta `target` y, a continuación, ejecute el archivo jar que creó en el paso anterior.

    ```cmd/sh
    cd target
    java -jar ./provisioning-x509-cert-generator-{version}-with-deps.jar
    ```

2. Escriba **N** en _Do you want to input common name?_ (¿Quiere escribir un nombre de entrada?). Copie en el Portapapeles la salida de `Client Cert` desde *---BEGIN CERTIFICATE---* hasta *---END CERTIFICATE---*.

   ![Generador de certificados individuales](./media/java-quick-create-simulated-device-x509/individual.png)

3. Cree un archivo denominado **_X509individual.pem_** en su máquina con Windows, ábralo en el editor que prefiera y copie el contenido del Portapapeles en este archivo. Guarde el archivo y cierre el editor.

4. En el símbolo del sistema, escriba **N** en _Do you want to input Verification Code_ (¿Desea escribir un código de verificación?) y mantenga la salida del programa abierta para usarla como referencia más adelante en la guía de inicio rápido. A continuación, copie los valores `Client Cert` y `Client Cert Private Key` para su uso en la sección siguiente.

5. Inicie sesión en [Azure Portal](https://portal.azure.com), haga clic en el botón **Todos los recursos** situado en el menú izquierdo y abra la instancia del servicio Device Provisioning.

6. En la hoja de resumen del servicio Device Provisioning, seleccione **Manage enrollments** (Administrar inscripciones). Seleccione la pestaña **Individual Enrollments** (Inscripciones individuales) y haga clic en el botón **Add** (Agregar) de la parte superior. 

7. En el panel **Agregar inscripción**, escriba la siguiente información:
    - Seleccione **X.509** como *Mecanismo* de atestación de identidad.
    - En el *Archivo .pem o .cer de certificado principal*, haga clic en *Seleccionar un archivo* para seleccionar el archivo de certificado **X509individual.pem** creado en los pasos anteriores.  
    - De forma opcional, puede proporcionar la siguiente información:
      - Seleccione un centro de IoT vinculado con el servicio de aprovisionamiento.
      - Escriba un identificador de dispositivo único. Asegúrese de evitar datos confidenciales al asignar nombre al dispositivo. 
      - Actualice el **Estado inicial del dispositivo gemelo** con la configuración inicial deseada para el dispositivo.
   - Una vez completado, haga clic en el botón **Guardar**. 

    [![Agregar inscripción individual para la atestación X.509 en el portal](./media/quick-create-simulated-device-x509-csharp/individual-enrollment.png)](./media/how-to-manage-enrollments/individual-enrollment.png#lightbox)

     Al inscribir el dispositivo correctamente, el dispositivo X.509 aparece como **microsoftriotcore** en la columna *Identificador de registro* de la pestaña *Inscripciones individuales*. 



## <a name="simulate-the-device"></a>Simulación del dispositivo

1. En la hoja de resumen del servicio Device Provisioning, seleccione **Introducción** y tome nota de los campos _Ámbito de id._ (Id. de ámbito) y _Provisioning Service Global Endpoint_ (Punto de conexión global del Servicio de aprovisionamiento).

    ![Información del servicio](./media/java-quick-create-simulated-device-x509/extract-dps-endpoints.png)

2. Abra el símbolo del sistema. Vaya a la carpeta del proyecto de ejemplo en el repositorio del SDK de Java.

    ```cmd/sh
    cd azure-iot-sdk-java/provisioning/provisioning-samples/provisioning-X509-sample
    ```

3. Escriba la información del servicio de aprovisionamiento y de la identidad X.509 en el código. Se utiliza durante el aprovisionamiento automático para la atestación del dispositivo simulado, antes del registro de dispositivo:

   - Edite el archivo `/src/main/java/samples/com/microsoft/azure/sdk/iot/ProvisioningX509Sample.java` para que incluya el _Id Scope_ (Id. de ámbito) y el _Provisioning Service Global Endpoint_ (Punto de conexión global del Servicio de aprovisionamiento) tal y como se indicó previamente. Incluya también el valor de _Client cert_ (Certificado de cliente) y _Client Cert Private Key_ (Clave privada del certificado de cliente) tal y como se indicó en la sección anterior.

      ```java
      private static final String idScope = "[Your ID scope here]";
      private static final String globalEndpoint = "[Your Provisioning Service Global Endpoint here]";
      private static final ProvisioningDeviceClientTransportProtocol PROVISIONING_DEVICE_CLIENT_TRANSPORT_PROTOCOL = ProvisioningDeviceClientTransportProtocol.HTTPS;
      private static final String leafPublicPem = "<Your Public PEM Certificate here>";
      private static final String leafPrivateKey = "<Your Private PEM Key here>";
      ```

   - Al copiar y pegar el certificado y la clave privada, utilice el siguiente formato:
        
      ```java
      private static final String leafPublicPem = "-----BEGIN CERTIFICATE-----\n" +
        "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
        "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
        "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
        "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
        "+XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
        "-----END CERTIFICATE-----\n";
      private static final String leafPrivateKey = "-----BEGIN PRIVATE KEY-----\n" +
            "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
            "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
            "XXXXXXXXXX\n" +
            "-----END PRIVATE KEY-----\n";
      ```

4. Compile el ejemplo. Vaya a la carpeta `target` y ejecute el archivo jar que se creó.

    ```cmd/sh
    mvn clean install
    cd target
    java -jar ./provisioning-x509-sample-{version}-with-deps.jar
    ```

5. En Azure Portal, vaya a la instancia de IoT Hub vinculada a su servicio de aprovisionamiento y abra la hoja **Device Explorer**. Después del aprovisionamiento correcto del dispositivo simulado X.509 en el centro, su identificador de dispositivo aparece en la hoja **Device Explorer** con el *ESTADO* **habilitado**.  Es posible que deba hacer clic en el botón **Actualizar** situado en la parte superior si ya ha abierto la hoja antes de ejecutar la aplicación de ejemplo del dispositivo. 

    ![El dispositivo se registra con el centro de IoT](./media/java-quick-create-simulated-device-x509/hub-registration.png) 

> [!NOTE]
> Si ha cambiado el valor predeterminado de *Estado inicial del dispositivo gemelo* en la entrada de inscripción para el dispositivo, el dispositivo puede extraer el estado gemelo deseado desde el centro y actuar en consecuencia. Para más información, consulte [Información y uso de dispositivos gemelos en IoT Hub](../iot-hub/iot-hub-devguide-device-twins.md).
>


## <a name="clean-up-resources"></a>Limpieza de recursos

Si piensa seguir trabajando con el ejemplo de cliente de dispositivo y explorándolo, no limpie los recursos que se crean en esta guía de inicio rápido. Si no va a continuar, use el siguiente comando para eliminar todos los recursos creados.

1. Cierre la ventana de salida de ejemplo del cliente del dispositivo en su máquina.
2. Desde el menú de la izquierda en Azure Portal, haga clic en **Todos los recursos** y seleccione el servicio Device Provisioning. Abra la hoja **Administrar inscripciones** de su servicio y, a continuación, haga clic en la pestaña **Inscripciones individuales**. Seleccione el *ID. DE REGISTRO* del dispositivo que inscribió en esta guía de inicio rápido y haga clic en el botón **Eliminar** situado en la parte superior. 
3. Desde el menú de la izquierda en Azure Portal, haga clic en **Todos los recursos** y seleccione su centro de IoT. Abra la hoja **IoT Devices** (Dispositivos IoT) de su centro, seleccione el *ID. DE DISPOSITIVO* del dispositivo que registró en esta guía de inicio rápido y, a continuación, haga clic en el botón **Eliminar** situado en la parte superior.


## <a name="next-steps"></a>Pasos siguientes

En esta guía de inicio rápido ha creado un dispositivo simulado X.509 en el equipo Windows. Ha configurado su inscripción en el servicio Azure IoT Hub Device Provisioning y se ha realizado el aprovisionamiento automático del dispositivo en su centro de IoT. Para obtener información sobre cómo inscribir el dispositivo X.509 mediante programación, siga la guía de inicio rápido para la inscripción de dispositivos X.509 mediante programación. 

> [!div class="nextstepaction"]
> [Guía de inicio rápido de Azure: Inscripción de dispositivos X.509 al servicio Azure IoT Hub Device Provisioning](quick-enroll-device-x509-java.md)
