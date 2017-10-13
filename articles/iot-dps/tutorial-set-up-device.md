---
title: "Configuración del dispositivo del servicio Azure IoT Hub Device Provisioning | Microsoft Docs"
description: "Configurar el dispositivo para el aprovisionamiento mediante el servicio IoT Hub Device Provisioning durante el proceso de fabricación del dispositivo"
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
ms.openlocfilehash: c99279413b50e7bf1e6058a4151890e3a8f83892
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="set-up-a-device-to-provision-using-the-azure-iot-hub-device-provisioning-service"></a>Configuración del dispositivo para el aprovisionamiento mediante el servicio Azure IoT Hub Device Provisioning

En el tutorial anterior, aprendió a configurar el servicio Azure IoT Hub Device Provisioning para aprovisionar automáticamente los dispositivos en su instancia de IoT Hub. Este tutorial proporciona instrucciones sobre cómo configurar el dispositivo durante el proceso de fabricación, para que pueda configurar el servicio Device Provisioning para el dispositivo en función del [módulo de seguridad de hardware (HSM)](https://azure.microsoft.com/blog/azure-iot-supports-new-security-hardware-to-strengthen-iot-security) y que el dispositivo se pueda conectar al servicio Device Provisioning cuando se inicie por primera vez. Este tutorial describe los procesos para:

> [!div class="checklist"]
> * Seleccionar un módulo de seguridad de hardware
> * Compilar el SDK del cliente de aprovisionamiento de dispositivos para el HSM seleccionado
> * Extracción de los artefactos de seguridad
> * Establecer la configuración del servicio Device Provisioning en el dispositivo

## <a name="prerequisites"></a>Requisitos previos

Antes de continuar, cree la instancia del servicio Device Provisioning y una instancia de IoT Hub mediante las instrucciones mencionadas en el tutorial [Configuración de la nube para el aprovisionamiento de dispositivos](./tutorial-set-up-cloud.md).


## <a name="select-a-hardware-security-module"></a>Seleccionar un módulo de seguridad de hardware

El [SDK del cliente del servicio Device Provisioning](https://github.com/Azure/azure-iot-sdk-c/tree/master/dps_client) proporciona compatibilidad para los dos tipos de módulos de seguridad de hardware (o HSM): 

- [Módulo de plataforma segura (TPM)](https://en.wikipedia.org/wiki/Trusted_Platform_Module).
    - TPM es una norma establecida para la mayoría de las plataformas de dispositivos basados en Windows, así como para algunos dispositivos basados en Linux y Ubuntu. Como fabricante de un dispositivo, puede elegir este HSM si tiene alguno de estos sistemas operativos ejecutándose en los dispositivos y si está buscando una norma establecida para HSM. Con los chips de TPM, solo puede inscribir cada dispositivo de forma individual en el servicio Device Provisioning. Con fines de desarrollo, puede usar el simulador TPM en la máquina Windows o Linux de desarrollo.

- Módulos de seguridad de hardware basados en [X.509](https://cryptography.io/en/latest/x509/). 
    - Los HSM basados en X.509 son chips relativamente más recientes y, actualmente, se están realizando trabajos en chips RIoT o DICE de Microsoft que implementan los certificados X.509. Con los chips X.509, puede realizar una inscripción masiva en el portal. También admite ciertos sistemas operativos distintos de Windows como embedOS. Con fines de desarrollo, el SDK del cliente del servicio Device Provisioning admite un simulador de dispositivos X.509. 

Como fabricante de un dispositivo, debe seleccionar módulos o chips de seguridad de hardware que se basen en cualquiera de los tipos anteriores. Actualmente no se admiten otros tipos de HSM en el SDK de cliente del servicio Device Provisioning.   


## <a name="build-device-provisioning-client-sdk-for-the-selected-hsm"></a>Compilar el SDK del cliente de aprovisionamiento de dispositivos para el HSM seleccionado

El SDK de cliente del servicio Device Provisioning ayuda a implementar el mecanismo de seguridad seleccionado en el software. Los pasos siguientes muestran cómo usar el SDK para el chip HSM seleccionado:

1. Si ha seguido la [Guía de inicio rápido para crear el dispositivo simulado](./quick-create-simulated-device.md), tendrá la configuración lista para compilar el SDK. Si no es así, siga los cuatro primeros pasos de la sección titulada [Preparación del entorno de desarrollo](./quick-create-simulated-device.md#setupdevbox). Estos pasos permiten clonar el repositorio de GitHub para el SDK de cliente del servicio Device Provisioning, así como instalar la herramienta de compilación `cmake`. 

1. Compile el SDK para el tipo de HSM que ha seleccionado para el dispositivo, mediante uno de los siguientes comandos en el símbolo del sistema:
    - Para dispositivos TPM:
        ```cmd/sh
        cmake -Ddps_auth_type=tpm ..
        ```

    - Para el simulador TPM:
        ```cmd/sh
        cmake -Ddps_auth_type=tpm_simulator ..
        ```

    - Para dispositivos X.509 y el simulador:
        ```cmd/sh
        cmake -Ddps_auth_type=x509 ..
        ```

1. El SDK proporciona compatibilidad predeterminada para dispositivos que ejecutan implementaciones de Windows o Ubuntu para HSM de los tipos TPM y X.509. Para más información sobre estos HSM compatibles, continúe en la sección titulada [Extracción de los artefactos de seguridad](#extractsecurity) que aparece a continuación. 
 
## <a name="support-custom-tpm-and-x509-devices"></a>Compatibilidad con dispositivos TPM y X.509 personalizados

El SDK de cliente del servicio Device Provisioning no proporciona compatibilidad predeterminada para los dispositivos TPM y X.509 que no ejecutan Windows o Ubuntu. Para estos dispositivos, debe escribir el código personalizado para el chip HSM concreto, como se muestra en los pasos siguientes:

### <a name="develop-your-custom-repository"></a>Desarrollo de su repositorio personalizado

1. Desarrolle un repositorio de GitHub para acceder al HSM. Este proyecto debe generar una biblioteca estática para el SDK de Device Provisioning que va a usar.
1. La biblioteca debe implementar las funciones definidas en el archivo de encabezado siguiente: a. Para TPM personalizados, implemente las funciones definidas en `\azure-iot-sdk-c\dps_client\adapters\custom_hsm_tpm_impl.h`.
    b. Para los dispositivos X.509 personalizados, implemente las funciones definidas en `\azure-iot-sdk-c\dps_client\adapters\custom_hsm_x509_impl.h`. 
1. El repositorio HSM también debe contener un archivo `CMakeLists.txt` en la raíz para el repositorio que se debe compilar.

### <a name="integrate-with-the-device-provisioning-service-client"></a>Integración con el cliente del servicio Device Provisioning

Una vez que la biblioteca se compila correctamente, puede moverla al SDK-C de IoThub y extraerla en el repositorio:

1. Especifique el repositorio de GitHub para el HSM personalizado, la ruta de acceso a la biblioteca y su nombre en el siguiente comando cmake:
    ```cmd/sh
    cmake -Ddps_auth_type=<custom_hsm> -Ddps_hsm_custom_repo=<github_repo_name> -Ddps_hsm_custom_lib=<path_and_name_of library> <PATH_TO_AZURE_IOT_SDK>
    ```
   Reemplace `<custom_hsm>` en este comando por `tpm` o `x509`. Este comando crea un marcador para el repositorio del HSM personalizado dentro del directorio `cmake`. Tenga en cuenta que el HSM personalizado debe estar basado en los mecanismos de seguridad TPM o X.509.

1. Abra el SDK de Visual Studio y compílelo. 

    - El proceso de compilación clona el repositorio personalizado y permite compilar la biblioteca.
    - El SDK se intentará vincular en el HSM personalizado definido en el comando cmake.

1. Ejecute el ejemplo `\azure-iot-sdk-c\dps_client\samples\dps_client_sample\dps_client_sample.c` para comprobar si el HSM se implementa correctamente.

<a id="extractsecurity"></a>
## <a name="extract-the-security-artifacts"></a>Extracción de los artefactos de seguridad

El siguiente paso es extraer los artefactos de seguridad del HSM en el dispositivo.

1. Para un dispositivo TPM, deberá averiguar la **clave de aprobación** asociada a este con la ayuda del fabricante del chip TPM. Puede derivar un único **identificador del registro** para el dispositivo TPM aplicando un algoritmo hash a la clave de aprobación. 
2. Para un dispositivo X.509, debe obtener los certificados emitidos para los dispositivos: certificados de entidad final para la inscripción en dispositivos individuales, y certificados raíz para las inscripciones de grupos de dispositivos.

Estos artefactos de seguridad son necesarios para inscribir sus dispositivos en el servicio Device Provisioning. El servicio de aprovisionamiento espera a que cualquiera de estos dispositivos arranque y se conecte con él en cualquier momento posterior en el tiempo. Consulte [Administración de las inscripciones de dispositivos](how-to-manage-enrollments.md) para obtener información sobre cómo usar estos artefactos de seguridad para crear las inscripciones. 

Cuando el dispositivo arranca por primera vez, el cliente SDK interactúa con el chip para extraer los artefactos de seguridad del dispositivo y comprueba el registro con el servicio Device Provisioning. 


## <a name="set-up-the-device-provisioning-service-configuration-on-the-device"></a>Establecer la configuración del servicio Device Provisioning en el dispositivo

El último paso en el proceso de fabricación del dispositivo es escribir una aplicación que usa el SDK de cliente del servicio Device Provisioning para registrar el dispositivo con el servicio. Este SDK proporciona las siguientes API para que las usen sus aplicaciones:

```C
typedef void(*DPS_REGISTER_DEVICE_CALLBACK)(DPS_RESULT register_result, const char* iothub_uri, const char* device_id, void* user_context); // Callback to notify user of device registration results.
DPS_CLIENT_LL_HANDLE DPS_Client_LL_Create (const char* dps_uri, const char* scope_id, DPS_TRANSPORT_PROVIDER_FUNCTION protocol, DPS_CLIENT_ON_ERROR_CALLBACK on_error_callback, void* user_ctx); // Creates the IOTHUB_DPS_LL_HANDLE to be used in subsequent calls.
void DPS_Client_LL_Destroy(DPS_CLIENT_LL_HANDLE handle); // Frees any resources created by the IoTHub Device Provisioning Service module.
DPS_RESULT DPS_LL_Register_Device(DPS_LL_HANDLE handle, DPS_REGISTER_DEVICE_CALLBACK register_callback, void* user_context, DPS_CLIENT_REGISTER_STATUS_CALLBACK status_cb, void* status_ctx); // Registers a device that has been previously registered with Device Provisioning Service
void DPS_Client_LL_DoWork(DPS_LL_HANDLE handle); // Processes the communications with the Device Provisioning Service and calls any user callbacks that are required.
```

Recuerde que debe inicializar las variables `dps_uri` y `dps_scope_id` como se mencionó en la [sección Simulación de la primera secuencia de arranque para el dispositivo de este inicio rápido](./quick-create-simulated-device.md#firstbootsequence), antes de utilizarlas. La API del registro de cliente de Device Provisioning `DPS_Client_LL_Create` se conecta con el servicio global de Device Provisioning. El servicio genera el *ámbito del identificador* y este garantiza la exclusividad. Es inmutable y se utiliza para identificar de forma exclusiva los identificadores de registro. El `iothub_uri` permite a la API de registro del cliente de IoT Hub `IoTHubClient_LL_CreateFromDeviceAuth` conectarse a la instancia de IoT Hub correcta. 


Estas API ayudan a su dispositivo a conectarse y registrarse con el servicio Device Provisioning cuando este arranca, y a obtener la información sobre la instancia de IoT Hub y conectarse a esta. El archivo `dps_client/samples/dps_client_sample/dps_client_sample.c` muestra cómo utilizar estas API. En general, debe crear el siguiente marco para el registro de cliente:

```C
static const char* dps_uri = "global.azure-devices-provisioning.net";
static const char* dps_scope_id = "[ID scope for your provisioning service]";
...
static void register_callback(DPS_RESULT register_result, const char* iothub_uri, const char* device_id, void* context)
{
    USER_DEFINED_INFO* user_info = (USER_DEFINED_INFO *)user_context;
    ...
    user_info. reg_complete = 1;
}
static void registation_status(DPS_REGISTRATION_STATUS reg_status, void* user_context)
{
}
int main()
{
    ...    
    security_device_init(); // initialize your HSM 

    DPS_CLIENT_LL_HANDLE handle = DPS_Client_LL_Create(dps_uri, dps_scope_id, dps_transport, on_dps_error_callback, &user_info); // Create your DPS client

    if (DPS_Client_LL_Register_Device(handle, register_callback, &user_info, register_status, &user_info) == IOTHUB_DPS_OK) {
        do {
            // The dps_register_callback is called when registration is complete or fails
            DPS_Client_LL_DoWork(handle);
        } while (user_info.reg_complete == 0);
    }
    DPS_Client_LL_Destroy(handle); // Clean up the DPS client
    ...
    iothub_client = IoTHubClient_LL_CreateFromDeviceAuth(user_info.iothub_uri, user_info.device_id, transport); // Create your IoT hub client and connect to your hub
    ...
}
```

Puede refinar la aplicación de registro de cliente del servicio Device Provisioning mediante un dispositivo simulado al principio y mediante una configuración de servicio de prueba. Una vez que la aplicación está funcionando en el entorno de prueba, puede compilarla para el dispositivo concreto y copiar el archivo ejecutable en la imagen del dispositivo. No inicie el dispositivo aún ya que necesita [inscribir el dispositivo con el servicio Device Provisioning](./tutorial-provision-device-to-hub.md#enrolldevice) antes de iniciarlo. Consulte los pasos siguientes para más información sobre este proceso. 

## <a name="clean-up-resources"></a>Limpieza de recursos

En este momento, puede que haya terminado de configurar los servicios Device Provisioning y de IoT Hub en el portal. Si desea abandonar la configuración de Device Provisioning o retrasarla mediante cualquiera de estos servicios, es recomendable apagarlos para evitar incurrir en costos innecesarios.

1. Desde el menú de la izquierda en Azure Portal, haga clic en **Todos los recursos** y seleccione el servicio Device Provisioning. En la parte superior de la hoja **Todos los recursos**, haga clic en **Eliminar**.  
1. Desde el menú de la izquierda en Azure Portal, haga clic en **Todos los recursos** y seleccione su centro de IoT. En la parte superior de la hoja **Todos los recursos**, haga clic en **Eliminar**.  


## <a name="next-steps"></a>Pasos siguientes
En este tutorial, ha aprendido cómo:

> [!div class="checklist"]
> * Seleccionar un módulo de seguridad de hardware
> * Compilar el SDK del cliente de aprovisionamiento de dispositivos para el HSM seleccionado
> * Extracción de los artefactos de seguridad
> * Establecer la configuración del servicio Device Provisioning en el dispositivo

Avance hasta el siguiente tutorial para aprender a aprovisionar el dispositivo en su instancia de IoT Hub inscribiéndolo en el servicio Azure IoT Hub Device Provisioning para su aprovisionamiento automático.

> [!div class="nextstepaction"]
> [Aprovisionar el dispositivo en la instancia de IoT Hub](tutorial-provision-device-to-hub.md)

