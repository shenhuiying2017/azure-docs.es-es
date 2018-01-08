---
title: Aprovisionamiento de un dispositivo X.509 simulado para Azure IoT Hub mediante C# | Microsoft Docs
description: "Inicio rápido de Azure: Creación y aprovisionamiento de un dispositivo X.509 simulado mediante el SDK de dispositivos C# para el servicio Azure IoT Hub Device Provisioning"
services: iot-dps
keywords: 
author: JimacoMS2
ms.author: v-jamebr
ms.date: 12/21/2017
ms.topic: hero-article
ms.service: iot-dps
documentationcenter: 
manager: timlt
ms.devlang: na
ms.custom: mvc
ms.openlocfilehash: 631b3dbd644b9fb9c14cb6e39a67560b070cca89
ms.sourcegitcommit: 85012dbead7879f1f6c2965daa61302eb78bd366
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/02/2018
---
# <a name="create-and-provision-a-simulated-x509-device-using-c-device-sdk-for-iot-hub-device-provisioning-service"></a>Creación y aprovisionamiento de un dispositivo X.509 simulado mediante el SDK de dispositivos C# para el servicio IoT Hub Device Provisioning
> [!div class="op_single_selector"]
> * [C](quick-create-simulated-device-x509.md)
> * [Java](quick-create-simulated-device-x509-java.md)
> * [C#](quick-create-simulated-device-x509-csharp.md)
> * [Python](quick-create-simulated-device-x509-python.md)

Estos pasos muestran cómo compilar el ejemplo de dispositivo X.509 simulado del [SDK de C# de Azure IoT Hub](https://github.com/Azure/azure-iot-sdk-csharp) en una máquina de desarrollo en la que se ejecuta el sistema operativo Windows y cómo conectar el dispositivo simulado con el servicio Device Provisioning e IoT Hub.

Asegúrese de completar los pasos descritos en [Configuración de un servicio Azure IoT Hub Device Provisioning con Azure Portal](./quick-setup-auto-provision.md) antes de continuar.

<a id="setupdevbox"></a>
## <a name="prepare-the-development-environment"></a>Preparación del entorno de desarrollo 

1. Asegúrese de que tiene el [SDK de .NET Core](https://www.microsoft.com/net/download/windows) instalado en la máquina. 

1. Asegúrese de que `git` está instalado en su máquina y se agrega a las variables de entorno accesibles para la ventana de comandos. Consulte las [herramientas de cliente de Git de Software Freedom Conservancy](https://git-scm.com/download/) para instalar la versión más reciente de las herramientas `git`, lo que incluye **Git Bash**, la aplicación de línea de comandos que puede usar para interactuar con su repositorio de Git local. 

4. Abra un símbolo del sistema o Git Bash. Clone el repositorio de GitHub del [SDK de Azure IoT para C#](https://github.com/Azure/azure-iot-sdk-csharp):
    
    ```cmd
    git clone --recursive https://github.com/Azure/azure-iot-sdk-csharp.git
    ```

## <a name="create-a-self-signed-x509-device-certificate-and-individual-enrollment-entry"></a>Creación de un certificado de dispositivo X.509 autofirmado y una entrada de inscripción individual

1. En un símbolo del sistema, cambie los directorios al directorio del proyecto del ejemplo de Device Provisioning X.509.

    ```cmd
    cd .\azure-iot-sdk-csharp\provisioning\device\samples\ProvisioningDeviceClientX509
    ```

1. El código de ejemplo está configurado para usar los certificados X.509 almacenados en un archivo con formato PKCS12 protegido con contraseña (certificate.pfx). Además, necesita un archivo de certificado de clave pública (certificate.cer) para crear una inscripción individual más adelante en esta guía de inicio rápido. Para generar un certificado autofirmado y los archivos .cer y .pfx asociados, ejecute el siguiente comando:

    ```cmd
    powershell .\GenerateTestCertificate.ps1
    ```

2. El script le pide una contraseña PFX. Recuerde esta contraseña, que deberá usar al ejecutar el ejemplo.

    ![ Escriba la contraseña PFX](./media/quick-create-simulated-device-x509-csharp/generate-certificate.png)  


4. Inicie sesión en Azure Portal, haga clic en el botón **Todos los recursos** situado en el menú izquierdo y abra el servicio de aprovisionamiento.

4. En la hoja de resumen del servicio Device Provisioning, seleccione **Manage enrollments** (Administrar inscripciones). Seleccione la pestaña **Individual Enrollments** (Inscripciones individuales) y haga clic en el botón **Add** (Agregar) de la parte superior. 

5. En **Agregar entrada de la lista de inscripción**, escriba la siguiente información:
    - Seleccione **X.509** como *Mecanismo* de atestación de identidad.
    - En *Archivo .pem o .cer de certificado*, seleccione el archivo de certificado **certificate.cer** creado en los pasos anteriores con el widget *Explorador de archivos*.
    - Deje el valor de **Id. de dispositivo** en blanco. El dispositivo se aprovisionará con su id. de dispositivo establecido en el nombre común en el certificado X.509, **iothubx509device1**. También será el nombre usado para el id. de registro de la entrada de inscripción individual. 
    - De forma opcional, puede proporcionar la siguiente información:
        - Seleccione un centro de IoT vinculado con el servicio de aprovisionamiento.
        - Actualice el **Estado inicial del dispositivo gemelo** con la configuración inicial deseada para el dispositivo.
    - Una vez completado, haga clic en el botón **Guardar**. 

    ![Escriba la información de inscripción del dispositivo X.509 en la hoja del portal](./media/quick-create-simulated-device-x509-csharp/enter-device-enrollment.png)  

   Al realizar la inscripción correctamente, la entrada de la inscripción X.509 aparece como **iothubx509device1** en la columna *Id. de registro* de la pestaña *Inscripciones individuales*. 

## <a name="provision-the-simulated-device"></a>Aprovisionamiento del dispositivo simulado

1. Desde la hoja **Información general** de su servicio de aprovisionamiento, anote el valor de **_Ámbito de id._**

    ![Extracción de información del punto de conexión DPS de la hoja del portal](./media/quick-create-simulated-device-x509-csharp/copy-scope.png) 


2. Escriba el siguiente comando para compilar y ejecutar el ejemplo de aprovisionamiento de dispositivo X.509. Reemplace el valor de `<IDScope>` por el ámbito de id. de su servicio de aprovisionamiento. 

    ```cmd
    dotnet run <IDScope>
    ```

6. Cuando se le solicite, escriba la contraseña del archivo PFX que creó anteriormente. Tenga en cuenta los mensajes que simulan el arranque del dispositivo y la conexión al servicio Device Provisioning para obtener la información del centro de IoT. 

    ![Salida del dispositivo de ejemplo](./media/quick-create-simulated-device-x509-csharp/sample-output.png) 

1. Compruebe que el dispositivo se haya aprovisionado. Si se aprovisiona correctamente el dispositivo simulado para la instancia de IoT Hub vinculada con el servicio de aprovisionamiento, el id. de dispositivo se muestra en la hoja **IoT Devices** (Dispositivos IoT) del centro. 

    ![El dispositivo se registra con el centro de IoT](./media/quick-create-simulated-device-x509-csharp/hub-registration.png) 

    Si ha cambiado el valor predeterminado de *Estado inicial del dispositivo gemelo* en la entrada de inscripción para el dispositivo, el dispositivo puede extraer el estado gemelo deseado desde el centro y actuar en consecuencia. Para más información, consulte [Información y uso de dispositivos gemelos en IoT Hub](../iot-hub/iot-hub-devguide-device-twins.md)


## <a name="clean-up-resources"></a>Limpieza de recursos

Si piensa seguir trabajando con el ejemplo de cliente de dispositivo y explorándolo, no limpie los recursos que se crean en esta guía de inicio rápido. Si no va a continuar, use el siguiente comando para eliminar todos los recursos creados.

1. Cierre la ventana de salida de ejemplo del cliente del dispositivo en su máquina.
1. Cierre la ventana del simulador de TPM en su máquina.
1. Desde el menú de la izquierda en Azure Portal, haga clic en **Todos los recursos** y seleccione el servicio Device Provisioning. En la parte superior de la hoja **Todos los recursos**, haga clic en **Eliminar**.  
1. Desde el menú de la izquierda en Azure Portal, haga clic en **Todos los recursos** y seleccione su centro de IoT. En la parte superior de la hoja **Todos los recursos**, haga clic en **Eliminar**.  

## <a name="next-steps"></a>pasos siguientes

En esta guía de inicio rápido, creó un dispositivo simulado X.509 en su máquina Windows y lo aprovisionó con IoT Hub mediante el servicio Azure IoT Hub Device Provisioning en el portal. Para obtener información sobre cómo inscribir el dispositivo X.509 mediante programación, siga la guía de inicio rápido para la inscripción de dispositivos X.509 mediante programación. 

> [!div class="nextstepaction"]
> [Guía de inicio rápido de Azure: Inscripción de dispositivos X.509 al servicio Azure IoT Hub Device Provisioning](quick-enroll-device-x509-node.md)
