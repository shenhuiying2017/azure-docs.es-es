---
title: Aprovisionamiento de un dispositivo de TPM simulado para Azure IoT Hub mediante C# | Microsoft Docs
description: "Inicio rápido de Azure: Creación y aprovisionamiento de un dispositivo de TPM simulado mediante el SDK de dispositivos C# para el servicio Azure IoT Hub Device Provisioning"
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
ms.openlocfilehash: 73c0411d93dae242a735caeb8d9b990720903044
ms.sourcegitcommit: 85012dbead7879f1f6c2965daa61302eb78bd366
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/02/2018
---
# <a name="create-and-provision-a-simulated-tpm-device-using-c-device-sdk-for-iot-hub-device-provisioning-service"></a>Creación y aprovisionamiento de un dispositivo de TPM simulado mediante el SDK de dispositivos C# para el servicio Azure IoT Hub Device Provisioning
> [!div class="op_single_selector"]
> * [C](quick-create-simulated-device.md)
> * [Java](quick-create-simulated-device-tpm-java.md)
> * [C#](quick-create-simulated-device-tpm-csharp.md)
> * [Python](quick-create-simulated-device-tpm-python.md)

En estos pasos se muestra cómo compilar el ejemplo de dispositivo de TPM simulado del SDK de C# de Azure IoT Hub en una máquina de desarrollo en la que se ejecuta el sistema operativo Windows y cómo conectar el dispositivo simulado con el servicio Device Provisioning e IoT Hub. El código de ejemplo utiliza el simulador de TPM de Windows como [módulo de seguridad de hardware (HSM)](https://azure.microsoft.com/blog/azure-iot-supports-new-security-hardware-to-strengthen-iot-security/) del dispositivo. 

Asegúrese de completar los pasos descritos en [Configuración de un servicio Azure IoT Hub Device Provisioning con Azure Portal](./quick-setup-auto-provision.md) antes de continuar.

<a id="setupdevbox"></a>
## <a name="prepare-the-development-environment"></a>Preparación del entorno de desarrollo 

1. Asegúrese de que tiene el [SDK de .NET Core](https://www.microsoft.com/net/download/windows) instalado en la máquina. 

1. Asegúrese de que `git` está instalado en su máquina y se agrega a las variables de entorno accesibles para la ventana de comandos. Consulte las [herramientas de cliente de Git de Software Freedom Conservancy](https://git-scm.com/download/) para instalar la versión más reciente de las herramientas `git`, lo que incluye **Git Bash**, la aplicación de línea de comandos que puede usar para interactuar con su repositorio de Git local. 

4. Abra un símbolo del sistema o Git Bash. Clone el repositorio de GitHub del SDK de Azure IoT para C#:
    
    ```cmd
    git clone --recursive https://github.com/Azure/azure-iot-sdk-csharp.git
    ```

## <a name="provision-the-simulated-device"></a>Aprovisionamiento del dispositivo simulado


1. Inicie sesión en el Portal de Azure. Haga clic en el botón **Todos los recursos** situado en el menú izquierdo y abra el servicio Device Provisioning. En la hoja **Información general**, anote el valor de **_Ámbito de id._**

    ![Copie el id. de ámbito del servicio de aprovisionamiento de la hoja del portal](./media/quick-create-simulated-device-tpm-csharp/copy-scope.png) 


2. En un símbolo del sistema, cambie los directorios al directorio del proyecto del ejemplo de aprovisionamiento de dispositivo de TPM.

    ```cmd
    cd .\azure-iot-sdk-csharp\provisioning\device\samples\ProvisioningDeviceClientTpm
    ```

2. Escriba el siguiente comando para compilar y ejecutar el ejemplo de aprovisionamiento de dispositivo de TPM. Reemplace el valor de `<IDScope>` por el ámbito de id. de su servicio de aprovisionamiento. 

    ```cmd
    dotnet run <IDScope>
    ```

1. La ventana de comandos muestra la **_clave de aprobación_**, el **_id. de registro_** y un **_id. de dispositivo_** sugerido necesarios para la inscripción del dispositivo. Anote estos valores. 
   > [!NOTE]
   > No confunda la ventana que contiene la salida del comando con la ventana que contiene la salida desde el simulador de TPM. Es posible que tenga que hacer clic en la ventana de comandos para pasarla al primer plano.

    ![Salida de la ventana de comandos](./media/quick-create-simulated-device-tpm-csharp/output1.png) 


4. En Azure Portal, en la hoja de resumen del servicio Device Provisioning, seleccione **Administrar inscripciones**. Seleccione la pestaña **Inscripciones individuales** y haga clic en el botón **Agregar** de la parte superior. 

5. En **Agregar entrada de la lista de inscripción**, escriba la siguiente información:
    - Seleccione **TPM** como *Mecanismo* de atestación de identidad.
    - Escriba el *Identificador de registro* y la *Clave de aprobación* del dispositivo TPM. 
    - De manera opcional, seleccione una instancia de IoT Hub vinculada con el servicio de aprovisionamiento.
    - Escriba un identificador de dispositivo único. Puede especificar el id. de dispositivo que se sugiere en la salida de ejemplo o escribir uno propio. Si usa uno propio, asegúrese de evitar datos confidenciales al asignar un nombre al dispositivo. 
    - Actualice el **Estado inicial del dispositivo gemelo** con la configuración inicial deseada para el dispositivo.
    - Una vez completado, haga clic en el botón **Guardar**. 

    ![Especificación de la información de inscripción del dispositivo en la hoja de portal](./media/quick-create-simulated-device-tpm-csharp/enter-device-enrollment.png)  

   Al inscribir el dispositivo correctamente, el *id. del registro* del dispositivo aparecerá en la lista de la pestaña *Individual Enrollments* (Inscripciones individuales). 

6. Presione Entrar para inscribir el dispositivo simulado. Tenga en cuenta los mensajes que simulan el arranque del dispositivo y la conexión al servicio Device Provisioning para obtener la información del centro de IoT. 

1. Compruebe que el dispositivo se haya aprovisionado. Si se aprovisiona correctamente el dispositivo simulado para la instancia de IoT Hub vinculada con el servicio de aprovisionamiento, el id. de dispositivo se muestra en la hoja **IoT Devices** (Dispositivos IoT) del centro. 

    ![El dispositivo se registra con el centro de IoT](./media/quick-create-simulated-device-tpm-csharp/hub-registration.png) 

    Si ha cambiado el valor predeterminado de *Estado inicial del dispositivo gemelo* en la entrada de inscripción para el dispositivo, el dispositivo puede extraer el estado gemelo deseado desde el centro y actuar en consecuencia. Para más información, consulte [Información y uso de dispositivos gemelos en IoT Hub](../iot-hub/iot-hub-devguide-device-twins.md)


## <a name="clean-up-resources"></a>Limpieza de recursos

Si piensa seguir trabajando con el ejemplo de cliente de dispositivo y explorándolo, no limpie los recursos que se crean en esta guía de inicio rápido. Si no va a continuar, use el siguiente comando para eliminar todos los recursos creados.

1. Cierre la ventana de salida de ejemplo del cliente del dispositivo en su máquina.
1. Cierre la ventana del simulador de TPM en su máquina.
1. Desde el menú de la izquierda en Azure Portal, haga clic en **Todos los recursos** y seleccione el servicio Device Provisioning. En la parte superior de la hoja **Todos los recursos**, haga clic en **Eliminar**.  
1. Desde el menú de la izquierda en Azure Portal, haga clic en **Todos los recursos** y seleccione su centro de IoT. En la parte superior de la hoja **Todos los recursos**, haga clic en **Eliminar**.  

## <a name="next-steps"></a>pasos siguientes

En este tutorial, ha creado un dispositivo de TPM simulado en su máquina y lo ha aprovisionado con IoT Hub mediante el servicio Azure IoT Hub Device Provisioning. Para obtener información sobre cómo inscribir el dispositivo de TPM mediante programación, siga la guía de inicio rápido para la inscripción de un dispositivo de TPM mediante programación. 

> [!div class="nextstepaction"]
> [Inicio rápido de Azure: Inscripción de dispositivos de TPM al servicio Azure IoT Hub Device Provisioning](quick-enroll-device-tpm-node.md)
