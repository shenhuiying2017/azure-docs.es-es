---
title: "Inscripción de dispositivos X.509 al servicio Azure Device Provisioning con Python | Microsoft Docs"
description: "Guía de inicio rápido de Azure: Inscripción de dispositivos X.509 al servicio Azure IoT Hub Device Provisioning con el SDK del servicio de aprovisionamiento de Python"
services: iot-dps
keywords: 
author: msebolt
ms.author: v-masebo
ms.date: 01/25/2018
ms.topic: hero-article
ms.service: iot-dps
documentationcenter: 
manager: timlt
ms.devlang: na
ms.custom: mvc
ms.openlocfilehash: 9cd16ddcd5dfbcd68be37e2988e57604082a338f
ms.sourcegitcommit: 059dae3d8a0e716adc95ad2296843a45745a415d
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 02/09/2018
---
# <a name="enroll-x509-devices-to-iot-hub-device-provisioning-service-using-python-provisioning-service-sdk"></a>Inscripción de dispositivos X.509 al servicio IoT Hub Device Provisioning con el SDK del servicio de aprovisionamiento de Python
[!INCLUDE [iot-dps-selector-quick-enroll-device-x509](../../includes/iot-dps-selector-quick-enroll-device-x509.md)]

En estos pasos se muestra cómo inscribir un grupo de dispositivos X.509 simulados mediante programación en el servicio Azure IoT Hub Device Provisioning con el [SDK del servicio de aprovisionamiento de Python](https://github.com/Azure/azure-iot-sdk-python/tree/master/provisioning_service_client) y una aplicación de Python de ejemplo. Aunque el SDK del servicio de Java funciona en máquinas Windows y Linux, este artículo usa una máquina de desarrollo de Windows para guiarle por el proceso de inscripción.

Asegúrese de [configurar un servicio Azure IoT Hub Device Provisioning con Azure Portal](./quick-setup-auto-provision.md) antes de continuar.

> [!NOTE]
> Esta guía de inicio rápido solo admite un **grupo de inscripción**. La **inscripción individual** con el _SDK del servicio de aprovisionamiento de Python_ es un trabajo en curso.
> 

<a id="prepareenvironment"></a>

## <a name="prepare-the-environment"></a>Preparación del entorno 

1. Descargue e instale [Python 2.x o 3.x](https://www.python.org/downloads/). Asegúrese de usar la instalación de 32 bits o 64 bits en función del programa de instalación. Cuando se le solicite durante la instalación, asegúrese de agregar Python a las variables de entorno específicas de la plataforma. 

1. Elija una de las siguientes opciones:

    - Cree y compile el **SDK de Python de Azure IoT**. Siga [estas instrucciones](https://github.com/Azure/azure-iot-sdk-python/blob/master/doc/python-devbox-setup.md) para compilar los paquetes de Python. Si usa el sistema operativo Windows, instale igualmente el [paquete redistribuible de Visual C++](http://www.microsoft.com/download/confirmation.aspx?id=48145) para permitir el uso de archivos DLL nativos de Python.

    - [Instale o actualice *pip*, el sistema de administración de paquetes de Python](https://pip.pypa.io/en/stable/installing/) e instale el paquete mediante el comando siguiente:

        ```cmd/sh
        pip install azure-iothub-provisioningserviceclient
        ```

1. Necesita un archivo .pem que contenga un certificado de entidad emisora X.509 intermedio o raíz cargado y comprobado con su servicio de aprovisionamiento. El **SDK de Azure IoT para C** contiene herramientas que pueden ayudarle a crear una cadena de certificados X.509, cargar un certificado raíz o intermedio a partir de esa cadena y realizar la prueba de posesión con el servicio para comprobar el certificado. Para usar estas herramientas, clone el [SDK de Azure IoT para C](https://github.com/Azure/azure-iot-sdk-c) y siga los pasos descritos en [azure-iot-sdk-c\tools\CACertificates\CACertificateOverview.md](https://github.com/Azure/azure-iot-sdk-c/blob/master/tools/CACertificates/CACertificateOverview.md) en su máquina.


## <a name="modify-the-python-sample-code"></a>Modificación del código de ejemplo de Python

En esta sección se muestra cómo agregar los detalles de aprovisionamiento del dispositivo X.509 al código de ejemplo. 

1. Con un editor de texto, cree un nuevo archivo **EnrollmentGroup.py**.

1. Agregue las siguientes instrucciones y variables `import` al principio del archivo **EnrollmentGroup.py**. Después, reemplace `dpsConnectionString` por la cadena de conexión que se encuentra en **Directivas de acceso compartido** en **Servicio Device Provisioning** de **Azure Portal**. Reemplace el marcador de posición de certificado por el certificado creado anteriormente en [Preparación del entorno](quick-enroll-device-x509-python.md#prepareenvironment). Por último, cree un único `registrationid`, y asegúrese de que solo consta de caracteres alfanuméricos en minúsculas y guiones.  
   
    ```python
    from provisioningserviceclient import ProvisioningServiceClient
    from provisioningserviceclient.models import EnrollmentGroup, AttestationMechanism

    CONNECTION_STRING = "{dpsConnectionString}"

    SIGNING_CERT = """-----BEGIN CERTIFICATE-----
    XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX
    XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX
    XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX
    XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX
    XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX
    XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX
    XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX
    XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX
    XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX
    XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX
    XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX
    XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX
    XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX
    XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX
    XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX
    -----END CERTIFICATE-----"""

    GROUP_ID = "{registrationid}"
    ```

1. Agregue la función y llamada de función siguientes para implementar la creación de inscripción de grupo:
   
    ```python
    def main():
        print ( "Initiating enrollment group creation..." )

        psc = ProvisioningServiceClient.create_from_connection_string(CONNECTION_STRING)
        att = AttestationMechanism.create_with_x509_signing_certs(SIGNING_CERT)
        eg = EnrollmentGroup.create(GROUP_ID, att)

        eg = psc.create_or_update(eg)
    
        print ( "Enrollment group created." )

    if __name__ == '__main__':
        main()
    ```

1. Guarde y cierre el archivo **EnrollmentGroup.py**.
 

## <a name="run-the-sample-group-enrollment"></a>Ejecución de la inscripción de grupo de ejemplo

1. Abra el símbolo del sistema y ejecute el script.

    ```cmd/sh
    python EnrollmentGroup.py
    ```

1. Observe la salida para comprobar si la inscripción se realizó correctamente.

1. Vaya al servicio de aprovisionamiento en Azure Portal. Haga clic en **Administrar inscripciones**. Tenga en cuenta que el grupo de dispositivos X.509 aparecerá en la pestaña **Grupos de inscripción**, con el nombre `registrationid` creado anteriormente. 

    ![Comprobación de si la inscripción X.509 se realizó correctamente en el portal](./media/quick-enroll-device-x509-python/1.png)  


## <a name="clean-up-resources"></a>Limpieza de recursos
Si tiene previsto explorar el ejemplo del servicio de Java, no elimine los recursos que se crearon con esta guía de inicio rápido. Si no va a continuar, use el siguiente comando para eliminar todos los recursos creados.

1. Cierre la ventana de salida de ejemplo de Java en su máquina.
1. Cierre la ventana de la herramienta para _generar certificados X.509_ en su máquina.
1. Navegue a su servicio Device Provisioning en Azure Portal, haga clic en **Administrar inscripciones** y, a continuación, seleccione la pestaña **Grupos de inscripción**. Seleccione el *NOMBRE DE GRUPO* para los dispositivos X.509 que inscribió mediante esta guía de inicio rápido y haga clic en el botón **Eliminar** situado en la parte superior de la hoja.  


## <a name="next-steps"></a>pasos siguientes
En esta guía de inicio rápido, inscribió un grupo de dispositivos X.509 simulados al servicio de Device Provisioning. Para más información acerca del aprovisionamiento de dispositivos, continúe con el tutorial para instalar el servicio Device Provisioning en Azure Portal. 

> [!div class="nextstepaction"]
> [Tutoriales del servicio Azure IoT Hub Device Provisioning](./tutorial-set-up-cloud.md)
