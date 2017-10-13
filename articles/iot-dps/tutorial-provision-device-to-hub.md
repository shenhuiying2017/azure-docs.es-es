---
title: Aprovisionamiento de un dispositivo mediante el servicio Azure IoT Hub Device Provisioning | Microsoft Docs
description: "Aprovisionar el dispositivo en una única instancia de IoT Hub mediante el servicio Azure IoT Hub Device Provisioning"
services: iot-dps
keywords: 
author: dsk-2015
ms.author: dkshir
ms.date: 09/05/2017
ms.topic: tutorial
ms.service: iot-dps
documentationcenter: 
manager: timlt
ms.devlang: na
ms.custom: mvc
ms.openlocfilehash: bf50699d2dc67294d554ba15713254a8b88d8ade
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="provision-the-device-to-an-iot-hub-using-the-azure-iot-hub-device-provisioning-service"></a>Aprovisionamiento del dispositivo en una instancia de IoT Hub mediante el servicio Azure IoT Hub Device Provisioning

En el tutorial anterior, aprendió a configurar un dispositivo para conectarse al servicio Device Provisioning. En este tutorial, aprenderá a usar este servicio para aprovisionar el dispositivo en una única instancia de IoT Hub mediante **_listas de inscripción_**. En este tutorial se muestra cómo realizar las siguientes acciones:

> [!div class="checklist"]
> * Inscribir el dispositivo
> * Iniciar el dispositivo
> * Comprobar que el dispositivo está registrado

## <a name="prerequisites"></a>Requisitos previos

Antes de continuar, asegúrese de configurar el dispositivo y su *módulo de seguridad de hardware* como se describe en el tutorial [Configuración del dispositivo para el aprovisionamiento mediante el servicio Azure IoT Hub Device Provisioning](./tutorial-set-up-device.md).


<a id="enrolldevice"></a>
## <a name="enroll-the-device"></a>Inscribir el dispositivo

Este paso incluye la incorporación de artefactos de seguridad únicos del dispositivo al servicio Device Provisioning. Estos artefactos de seguridad son los siguientes:

- Para dispositivos basados en TPM:
    - La *clave de aprobación* que es única para cada chip de TPM o simulación. Consulte [Descripción de la clave de aprobación de TPM](https://technet.microsoft.com/library/cc770443.aspx) para más información.
    - El *identificador de registro* que se usa para identificar de forma única un dispositivo en el espacio de nombres o el ámbito. Este puede ser igual que el identificador de dispositivo o no. El identificador es obligatorio para todos los dispositivos. En el caso de los dispositivos basados en TPM, el identificador de registro puede derivarse del propio TPM, por ejemplo, un hash SHA-256 de la clave de aprobación del TPM.

    ![Información de inscripción del TPM en el portal](./media/tutorial-provision-device-to-hub/tpm-device-enrollment.png)

- Para dispositivos basados en X.509:
    - El [certificado emitido para el chip o simulación X.509](https://msdn.microsoft.com/library/windows/desktop/bb540819.aspx), en un formato de archivo *.pem* o *.cer*. Para la inscripción individual, debe usar el *certificado del firmante* para su sistema X.509, mientras que para la inscripción de grupos, debe usar el *certificado raíz*.

    ![Información de inscripción del dispositivo X.509 en el portal](./media/tutorial-provision-device-to-hub/x509-device-enrollment.png)


Hay dos maneras de inscribir el dispositivo en el servicio Device Provisioning:

- **Grupos de inscripción**: esta representa un grupo de dispositivos que comparten un mecanismo de atestación específico. Se recomienda usar un grupo de inscripción para un gran número de dispositivos que compartan la configuración inicial deseada o para dispositivos que vayan todos al mismo inquilino.

    ![Grupos de inscripción para X.509 en el portal](./media/tutorial-provision-device-to-hub/x509-enrollment-groups.png)

- **Inscripciones individuales**: esta representa una entrada de un único dispositivo que se puede registrar con el servicio Device Provisioning. Las inscripciones individuales pueden usar certificados X.509 o tokens de SAS (en un módulo de plataforma segura real o virtual) como mecanismos de atestación. Se recomiendan las inscripciones individuales para los dispositivos que requieran configuraciones iniciales únicas o para los dispositivos que solo se puedan utilizar tokens de SAS en módulos de plataforma segura reales o virtuales como mecanismo de atestación. En las inscripciones individuales se puede especificar el identificador de dispositivo del centro de IoT.

Éstos son los pasos necesarios para inscribir el dispositivo en el portal:

1. Observe los artefactos de seguridad del HSM en el dispositivo. Puede que tenga que usar las API mencionadas en la sección [Extracción de artefactos de seguridad](./tutorial-set-up-device.md#extractsecurity) del tutorial anterior, en un entorno de desarrollo.

1. Inicie sesión en Azure Portal, haga clic en el botón **Todos los recursos** situado en el menú izquierdo y abra el servicio Device Provisioning.

1. En la hoja de resumen del servicio Device Provisioning, seleccione **Manage enrollments** (Administrar inscripciones). Seleccione la pestaña **Inscripciones individuales** o **Grupos de inscripción** según la configuración del dispositivo. Haga clic en el botón **Agregar** de la parte superior. Seleccione **TPM** o **X.509** como *mecanismo* de atestación de identidad y escriba los artefactos de seguridad adecuados, según lo descrito anteriormente. Puede escribir un nuevo **identificador de dispositivo de IoT Hub**. Una vez completado, haga clic en el botón **Guardar**. 

1. Cuando el dispositivo se haya inscrito satisfactoriamente, verá que aparece en el portal como sigue:

    ![Inscripción de TPM satisfactoria en el portal](./media/tutorial-provision-device-to-hub/tpm-enrollment-success.png)


## <a name="start-the-device"></a>Iniciar el dispositivo

En este momento, la siguiente configuración está lista para el registro del dispositivo:

1. El dispositivo o grupo de dispositivos ya están inscritos en el servicio Device Provisioning, y 
2. El dispositivo está preparado con el chip HSM configurado y accesible a través de la aplicación mediante el SDK de cliente del servicio Device Provisioning.

Inicie el dispositivo para permitir que la aplicación cliente inicie el registro con el servicio Device Provisioning.  


## <a name="verify-the-device-is-registered"></a>Comprobar que el dispositivo está registrado

Una vez que arranque el dispositivo, se deben realizar las siguientes acciones. Consulte la aplicación de ejemplo del simulador TPM [dps_client_sample](https://github.com/Azure/azure-iot-device-auth/blob/master/dps_client/samples/dps_client_sample/dps_client_sample.c) para más información. 

1. El dispositivo envía una solicitud de registro al servicio Device Provisioning.
2. En el caso de los dispositivos TPM, el servicio Device Provisioning devuelve un desafío de registro al que responde el dispositivo. 
3. Una vez que el registro se ha realizado correctamente, el servicio Device Provisioning envía el identificador URI del centro de IoT, el del dispositivo y la clave cifrada al dispositivo. 
4. Luego, la aplicación cliente de IoT Hub del dispositivo se conecta al centro. 
5. Después de una conexión correcta, el dispositivo aparecerá en la página **Device Explorer** (Explorador de dispositivos) del centro de IoT. 

    ![Conexión correcta al centro en el portal](./media/tutorial-provision-device-to-hub/hub-connect-success.png)

## <a name="next-steps"></a>Pasos siguientes
En este tutorial, ha aprendido cómo:

> [!div class="checklist"]
> * Inscribir el dispositivo
> * Iniciar el dispositivo
> * Comprobar que el dispositivo está registrado

Pase al siguiente tutorial para aprender a aprovisionar varios dispositivos en centros con equilibrio de carga. 

> [!div class="nextstepaction"]
> [Aprovisionamiento de dispositivos en instancias de IoT Hub con equilibrio de carga](./tutorial-provision-multiple-hubs.md)
