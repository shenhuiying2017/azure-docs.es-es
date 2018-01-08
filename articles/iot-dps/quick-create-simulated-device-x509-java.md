---
title: Aprovisionamiento de un dispositivo X.509 simulado para Azure IoT Hub mediante Java | Microsoft Docs
description: "Guía de inicio rápido de Azure: creación y aprovisionamiento de un dispositivo X.509 simulado mediante el SDK de dispositivos Java para el servicio Azure IoT Hub Device Provisioning"
services: iot-dps
keywords: 
author: msebolt
ms.author: v-masebo
ms.date: 12/21/2017
ms.topic: quickstart
ms.service: iot-dps
documentationcenter: 
manager: timlt
ms.devlang: java
ms.custom: mvc
ms.openlocfilehash: 508df6e7c677d87c6b893519c59b02880e16d7ec
ms.sourcegitcommit: a648f9d7a502bfbab4cd89c9e25aa03d1a0c412b
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/22/2017
---
# <a name="create-and-provision-a-simulated-x509-device-using-java-device-sdk-for-iot-hub-device-provisioning-service"></a>Creación y aprovisionamiento de un dispositivo X.509 simulado mediante el SDK de dispositivos Java para el servicio Azure IoT Hub Device Provisioning
> [!div class="op_single_selector"]
> * [C](quick-create-simulated-device-x509.md)
> * [Java](quick-create-simulated-device-x509-java.md)
> * [C#](quick-create-simulated-device-x509-csharp.md)
> * [Python](quick-create-simulated-device-x509-python.md)

Estos pasos muestran cómo simular un dispositivo X.509 en la máquina de desarrollo en la que se ejecuta el sistema operativo Windows, y usar un ejemplo de código para conectar este dispositivo simulado con el servicio Device Provisioning y IoT Hub. 

Asegúrese de completar los pasos descritos en [Configuración de un servicio Azure IoT Hub Device Provisioning con Azure Portal](./quick-setup-auto-provision.md) antes de continuar.


## <a name="prepare-the-environment"></a>Preparación del entorno 

1. Asegúrese de que tiene [Java SE Development Kit 8](http://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html) instalado en su máquina.

1. Descargue e instale [Maven](https://maven.apache.org/install.html).

1. Asegúrese de que `git` está instalado en su máquina y se agrega a las variables de entorno accesibles para la ventana de comandos. Consulte las [herramientas de cliente de Git de Software Freedom Conservancy](https://git-scm.com/download/) para instalar la versión más reciente de las herramientas `git`, lo que incluye **Git Bash**, la aplicación de línea de comandos que puede usar para interactuar con su repositorio de Git local. 

1. Abra el símbolo del sistema. Clone el repositorio GitHub para el ejemplo de código de simulación de dispositivo:
    
    ```cmd/sh
    git clone https://github.com/Azure/azure-iot-sdk-java.git --recursive
    ```

1. Navegue hasta el proyecto de generación de certificados y compílelo. 

    ```cmd/sh
    cd azure-iot-sdk-java/provisioning/provisioning-tools/provisioning-x509-cert-generator
    mvn clean install
    ```

1. Navegue hasta la carpeta de destino y ejecute el archivo .jar que se creó.

    ```cmd/sh
    cd target
    java -jar ./provisioning-x509-cert-generator-{version}-with-deps.jar
    ```

1. Cree la información de inscripción mediante cualquiera de las siguientes maneras, según la configuración:

    - **Inscripción individual**:

        1. Escriba **N** en _Do you want to input common name?_ (¿Quiere escribir un nombre de entrada?). Copie la salida en el Portapapeles de `Client Cert` desde *---BEGIN CERTIFICATE---* hasta *---END CERTIFICATE---*.

            ![Generador de certificados individuales](./media/java-quick-create-simulated-device-x509/individual.png)

        1. Cree un archivo denominado **_X509individual.pem_** en su máquina con Windows, ábralo en el editor que prefiera y copie el contenido del Portapapeles en este archivo. Guarde el archivo.

        1. Escriba **N** en _Do you want to input Verification Code_ (¿Quiere introducir un código de verificación?) y mantenga la salida del programa abierta para usarla como referencia más adelante en la guía de inicio rápido. Tenga en cuenta los valores de _Client Cert_ (Certificado de cliente) y _Client Cert Private Key_ (Clave privada del certificado de cliente).
    
    - **Grupos de inscripción**:

        1. Escriba **N** en _Do you want to input common name?_ (¿Quiere escribir un nombre de entrada?). Copie la salida en el Portapapeles de `Root Cert` desde *---BEGIN CERTIFICATE---* hasta *---END CERTIFICATE---*.

            ![Generador de certificados de grupo](./media/java-quick-create-simulated-device-x509/group.png)

        1. Cree un archivo denominado **_X509group.pem_** en su máquina con Windows, ábralo en el editor que prefiera y copie el contenido del Portapapeles en este archivo. Guarde el archivo.

        1. Escriba **Y** en _Do you want to input Verification Code_ (¿Quiere introducir un código de verificación?) y mantenga el programa abierto para usarlo como referencia más adelante en la guía de inicio rápido. Tenga en cuenta los valores de _Client Cert_ (Certificado de cliente), _Client Cert Private Key_ (Clave privada del certificado de cliente), _Signer Cert_ (Certificado de firmante) y _Root Cert_ (Certificado raíz).


## <a name="create-a-device-enrollment-entry"></a>Creación de una entrada de inscripción de dispositivo

1. Inicie sesión en Azure Portal, haga clic en el botón **Todos los recursos** situado en el menú izquierdo y abra el servicio de aprovisionamiento.

1. Escriba la información de inscripción mediante cualquiera de las siguientes maneras, según la configuración:

    - **Inscripción individual**: 

        1. En la hoja de resumen del servicio Device Provisioning, seleccione **Manage enrollments** (Administrar inscripciones). Seleccione la pestaña **Individual Enrollments** (Inscripciones individuales) y haga clic en el botón **Add** (Agregar) de la parte superior. 

        1. En **Agregar entrada de la lista de inscripción**, escriba la siguiente información:
            - Seleccione **X.509** como *Mecanismo* de atestación de identidad.
            - En el *Archivo de certificado .pem o .cer*, seleccione el archivo de certificado **_X509individual.pem_** creado en los pasos anteriores con el widget *Explorador de archivos*.
            - De forma opcional, puede proporcionar la siguiente información:
                - Seleccione un centro de IoT vinculado con el servicio de aprovisionamiento.
                - Escriba un identificador de dispositivo único. Asegúrese de evitar datos confidenciales al asignar nombre al dispositivo. 
                - Actualice el **Estado inicial del dispositivo gemelo** con la configuración inicial deseada para el dispositivo.
            - Una vez completado, haga clic en el botón **Guardar**. 

        ![Escriba la información de inscripción del dispositivo X.509 en la hoja del portal](./media/java-quick-create-simulated-device-x509/enter-device-enrollment.png)  

       Al inscribir el dispositivo correctamente, el dispositivo X.509 aparece como **microsoftriotcore** en la columna *Id. de registro* de la pestaña *Inscripciones individuales*. 

    - **Grupos de inscripción**: 

        1. En la hoja de resumen del servicio Device Provisioning, seleccione **Certificados** y haga clic en el botón **Agregar** situado en la parte superior.

        1. En **Agregar certificado**, escriba la siguiente información:
            - Escriba un nombre de certificado único.
            - Seleccione el archivo **_X509group.pem_** que creó anteriormente.
            - Una vez completado, haga clic en el botón **Guardar**.

        ![Agregar certificado](./media/java-quick-create-simulated-device-x509/add-certificate.png)

        1. Seleccione el certificado recién creado:
            - Haga clic en **Generar código de verificación**. Copie el código generado.
            - Escriba el _código de verificación_ o haga clic con el botón derecho para pegarlo en la ventana de _provisioning-x509-cert-generator_ que se está ejecutando.  Presione **Entrar**.
            - Copie la salida en el Portapapeles de `Verification Cert` desde *---BEGIN CERTIFICATE---* hasta *---END CERTIFICATE---*.
            
                ![Generador de validaciones](./media/java-quick-create-simulated-device-x509/validation-generator.png)

            - Cree un archivo denominado **_X509validation.pem_** en su máquina con Windows, ábralo en el editor que prefiera y copie el contenido del Portapapeles en este archivo. Guarde el archivo.
            - Seleccione el archivo **_X509validation.pem_** en Azure Portal. Haga clic en **Verificar**.

            ![Validación del certificado](./media/java-quick-create-simulated-device-x509/validate-certificate.png)

        1. Seleccione **Administrar inscripciones**. Seleccione la pestaña **Grupos de inscripción** y haga clic en el botón **Agregar** de la parte superior.
            - Escriba un nombre de grupo único.
            - Seleccione el nombre del certificado único que creó anteriormente.
            - De forma opcional, puede proporcionar la siguiente información:
                - Seleccione un centro de IoT vinculado con el servicio de aprovisionamiento.
                - Actualice el **Estado inicial del dispositivo gemelo** con la configuración inicial deseada para el dispositivo.

        ![Introducción de la información de inscripción del grupo X.509 en la hoja del portal](./media/java-quick-create-simulated-device-x509/enter-group-enrollment.png)

        En caso de que la inscripción se realice correctamente, el grupo de dispositivos X.509 aparecerá en la columna *Nombre del grupo* de la pestaña *Grupos de inscripción*.


## <a name="simulate-the-device"></a>Simulación del dispositivo

1. En la hoja de resumen del servicio Device Provisioning, seleccione **Introducción** y tome nota de los campos _Ámbito de id._ (Id. de ámbito) y _Provisioning Service Global Endpoint_ (Punto de conexión global del Servicio de aprovisionamiento).

    ![Información del servicio](./media/java-quick-create-simulated-device-x509/extract-dps-endpoints.png)

1. Abra el símbolo del sistema. Navegue hasta la carpeta del proyecto de ejemplo.

    ```cmd/sh
    cd azure-iot-sdk-java/provisioning/provisioning-samples/provisioning-X509-sample
    ```

1. Escriba la información de inscripción mediante cualquiera de las siguientes maneras, según la configuración:

    - **Inscripción individual**: 

        1. Edite el archivo `/src/main/java/samples/com/microsoft/azure/sdk/iot/ProvisioningX509Sample.java` para que incluya el _Id Scope_ (Id. de ámbito) y el _Provisioning Service Global Endpoint_ (Punto de conexión global del Servicio de aprovisionamiento) tal y como se indicó antes. Incluya también el valor de _Client cert_ (Certificado de cliente) y _Client Cert Private Key_ (Clave privada del certificado de cliente) tal y como se indicó anteriormente.

            ```java
            private static final String idScope = "[Your ID scope here]";
            private static final String globalEndpoint = "[Your Provisioning Service Global Endpoint here]";
            private static final ProvisioningDeviceClientTransportProtocol PROVISIONING_DEVICE_CLIENT_TRANSPORT_PROTOCOL = ProvisioningDeviceClientTransportProtocol.HTTPS;
            private static final String leafPublicPem = "<Your Public PEM Certificate here>";
            private static final String leafPrivateKey = "<Your Private PEM Key here>";
            ```

            - Para incluir el certificado y la clave, utilice el formato siguiente:
            
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

    - **Grupos de inscripción**: 

        1. Siga las instrucciones anteriores para la **inscripción individual**.

        1. Agregue las líneas de código siguientes al principio de la función `main`.
        
            ```java
            String intermediatePem = "<Your Signer Certificate here>";          
            String rootPem = "<Your Root Certificate here>";
                
            signerCertificates.add(intermediatePem);
            signerCertificates.add(root);
            ```
    
            - Para incluir los certificados, utilice el formato siguiente:
        
                ```java
                String intermediatePem = "-----BEGIN CERTIFICATE-----\n" +
                    "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
                    "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
                    "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
                    "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
                    "+XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
                    "-----END CERTIFICATE-----\n";
                String rootPem = "-----BEGIN CERTIFICATE-----\n" +
                    "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
                    "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
                    "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
                    "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
                    "+XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
                    "-----END CERTIFICATE-----\n";
                ```

1. Compile el ejemplo. Navegue hasta la carpeta de destino y ejecute el archivo .jar que se creó.

    ```cmd/sh
    mvn clean install
    cd target
    java -jar ./provisioning-x509-sample-{version}-with-deps.jar
    ```

1. En el portal, navegue hasta IoT Hub vinculado a su servicio de aprovisionamiento y abra la hoja **Device Explorer**. En el aprovisionamiento correcto del dispositivo simulado X.509 con el Hub, su identificador de dispositivo aparece en la hoja **Device Explorer** con el *ESTADO* **habilitado**. Tenga en cuenta que debe hacer clic en el botón **Actualizar** situado en la parte superior si ya ha abierto la hoja antes de ejecutar la aplicación de ejemplo del dispositivo. 

    ![El dispositivo se registra con el centro de IoT](./media/java-quick-create-simulated-device-x509/hub-registration.png) 

> [!NOTE]
> Si ha cambiado el valor predeterminado de *Estado inicial del dispositivo gemelo* en la entrada de inscripción para el dispositivo, el dispositivo puede extraer el estado gemelo deseado desde el centro y actuar en consecuencia. Para más información, consulte [Información y uso de dispositivos gemelos en IoT Hub](../iot-hub/iot-hub-devguide-device-twins.md).
>


## <a name="clean-up-resources"></a>Limpieza de recursos

Si piensa seguir trabajando con el ejemplo de cliente de dispositivo y explorándolo, no limpie los recursos que se crean en esta guía de inicio rápido. Si no va a continuar, use el siguiente comando para eliminar todos los recursos creados.

1. Cierre la ventana de salida de ejemplo del cliente del dispositivo en su máquina.
1. Desde el menú de la izquierda en Azure Portal, haga clic en **Todos los recursos** y seleccione el servicio Device Provisioning. Abra la hoja **Administrar inscripciones** de su servicio y, a continuación, haga clic en la pestaña **Inscripciones individuales**. Seleccione el *ID. DE REGISTRO* del dispositivo que inscribió en esta guía de inicio rápido y haga clic en el botón **Eliminar** situado en la parte superior. 
1. Desde el menú de la izquierda en Azure Portal, haga clic en **Todos los recursos** y seleccione su centro de IoT. Abra la hoja **IoT Devices** (Dispositivos IoT) de su centro, seleccione el *ID. DE DISPOSITIVO* del dispositivo que registró en esta guía de inicio rápido y, a continuación, haga clic en el botón **Eliminar** situado en la parte superior.


## <a name="next-steps"></a>pasos siguientes

En esta guía de inicio rápido, creó un dispositivo simulado X.509 en su máquina Windows y lo aprovisionó con IoT Hub mediante el servicio Azure IoT Hub Device Provisioning en el portal. Para obtener información sobre cómo inscribir el dispositivo X.509 mediante programación, siga la guía de inicio rápido para la inscripción de dispositivos X.509 mediante programación. 

> [!div class="nextstepaction"]
> [Inicio rápido de Azure: Inscripción de dispositivos X.509 al servicio Azure IoT Hub Device Provisioning](quick-enroll-device-x509-java.md)
