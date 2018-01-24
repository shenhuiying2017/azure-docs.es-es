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
ms.openlocfilehash: 835a54f147b9ea543df21e7dfeb226ac42aceda3
ms.sourcegitcommit: 357afe80eae48e14dffdd51224c863c898303449
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/15/2017
---
# <a name="set-up-a-device-to-provision-using-the-azure-iot-hub-device-provisioning-service"></a>Configuración del dispositivo para el aprovisionamiento mediante el servicio Azure IoT Hub Device Provisioning

En el tutorial anterior, aprendió a configurar el servicio Azure IoT Hub Device Provisioning para aprovisionar automáticamente los dispositivos en su instancia de IoT Hub. Este tutorial proporciona instrucciones sobre cómo configurar el dispositivo durante el proceso de fabricación, para que pueda configurar el servicio Device Provisioning para el dispositivo en función del [módulo de seguridad de hardware (HSM)](https://azure.microsoft.com/blog/azure-iot-supports-new-security-hardware-to-strengthen-iot-security) y que el dispositivo se pueda conectar al servicio Device Provisioning cuando se inicie por primera vez. Este tutorial describe los procesos para:

> [!div class="checklist"]
> * Seleccionar un módulo de seguridad de hardware
> * Compilar el SDK del cliente de aprovisionamiento de dispositivos para el HSM seleccionado
> * Extracción de los artefactos de seguridad
> * Establecer la configuración del servicio Device Provisioning en el dispositivo

## <a name="prerequisites"></a>requisitos previos

Antes de continuar, cree la instancia del servicio Device Provisioning y una instancia de IoT Hub mediante las instrucciones mencionadas en el tutorial [Configuración de la nube para el aprovisionamiento de dispositivos](./tutorial-set-up-cloud.md).


## <a name="select-a-hardware-security-module"></a>Seleccionar un módulo de seguridad de hardware

El [SDK del cliente del servicio Device Provisioning](https://github.com/Azure/azure-iot-sdk-c/tree/master/provisioning_client) proporciona compatibilidad para los dos tipos de módulos de seguridad de hardware (o HSM): 

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
        cmake -Duse_prov_client:BOOL=ON ..
        ```

    - Para el simulador TPM:
        ```cmd/sh
        cmake -Duse_prov_client:BOOL=ON -Duse_tpm_simulator:BOOL=ON ..
        ```

    - Para dispositivos X.509 y el simulador:
        ```cmd/sh
        cmake -Duse_prov_client:BOOL=ON ..
        ```

1. El SDK proporciona compatibilidad predeterminada para dispositivos que ejecutan implementaciones de Windows o Ubuntu para HSM de los tipos TPM y X.509. Para más información sobre estos HSM compatibles, continúe en la sección titulada [Extracción de los artefactos de seguridad](#extractsecurity) que aparece a continuación. 
 
## <a name="support-custom-tpm-and-x509-devices"></a>Compatibilidad con dispositivos TPM y X.509 personalizados

El SDK de cliente del servicio Device Provisioning no proporciona compatibilidad predeterminada para los dispositivos TPM y X.509 que no ejecutan Windows o Ubuntu. Para estos dispositivos, debe escribir el código personalizado para el chip HSM concreto, como se muestra en los pasos siguientes:

### <a name="develop-your-custom-repository"></a>Desarrollo de su repositorio personalizado

1. Desarrolle una biblioteca para tener acceso a los HSM. Este proyecto debe generar una biblioteca estática para el SDK de Device Provisioning que va a usar.
1. La biblioteca debe implementar las funciones definidas en el archivo de encabezado siguiente: a. Para TPM personalizado, implemente las funciones definidas en [Documentación de HSM personalizado](https://github.com/Azure/azure-iot-sdk-c/blob/master/provisioning_client/devdoc/using_custom_hsm.md#hsm-tpm-api).
    b. Para X.509 personalizado, implemente las funciones definidas en [Documentación de HSM personalizado](https://github.com/Azure/azure-iot-sdk-c/blob/master/provisioning_client/devdoc/using_custom_hsm.md#hsm-x509-api). 

### <a name="integrate-with-the-device-provisioning-service-client"></a>Integración con el cliente del servicio Device Provisioning

Una vez que la biblioteca se compila correctamente, puede moverla al SDK-C de IoThub y realizar un vinculo a la biblioteca:

1. Especifique el repositorio de GitHub para el HSM personalizado, la ruta de acceso a la biblioteca y su nombre en el siguiente comando cmake:
    ```cmd/sh
    cmake -Duse_prov_client:BOOL=ON -Dhsm_custom_lib=<path_and_name_of_library> <PATH_TO_AZURE_IOT_SDK>
    ```
   
1. Abra el SDK de Visual Studio y compílelo. 

    - El proceso de compilación compilará la biblioteca del SDK.
    - El SDK se intentará vincular en el HSM personalizado definido en el comando cmake.

1. Ejecute el ejemplo `\azure-iot-sdk-c\provisioning_client\samples\prov_dev_client_ll_sample\prov_dev_client_ll_sample.c` para comprobar si el HSM se implementa correctamente.

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
// Creates a Provisioning Client for communications with the Device Provisioning Client Service
PROV_DEVICE_LL_HANDLE Prov_Device_LL_Create(const char* uri, const char* scope_id, PROV_DEVICE_TRANSPORT_PROVIDER_FUNCTION protocol)

// Disposes of resources allocated by the provisioning Client.
void Prov_Device_LL_Destroy(PROV_DEVICE_LL_HANDLE handle)

// Asynchronous call initiates the registration of a device.
PROV_DEVICE_RESULT Prov_Device_LL_Register_Device(PROV_DEVICE_LL_HANDLE handle, PROV_DEVICE_CLIENT_REGISTER_DEVICE_CALLBACK register_callback, void* user_context, PROV_DEVICE_CLIENT_REGISTER_STATUS_CALLBACK reg_status_cb, void* status_user_ctext)

// Api to be called by user when work (registering device) can be done
void Prov_Device_LL_DoWork(PROV_DEVICE_LL_HANDLE handle)

// API sets a runtime option identified by parameter optionName to a value pointed to by value
PROV_DEVICE_RESULT Prov_Device_LL_SetOption(PROV_DEVICE_LL_HANDLE handle, const char* optionName, const void* value)
```

Recuerde que debe inicializar las variables `uri` y `id_scope` como se mencionó en la [sección Simulación de la primera secuencia de arranque para el dispositivo de este inicio rápido](./quick-create-simulated-device.md#firstbootsequence), antes de utilizarlas. La API del registro de cliente de Device Provisioning `Prov_Device_LL_Create` se conecta con el servicio global de Device Provisioning. El servicio genera el *ámbito del identificador* y este garantiza la exclusividad. Es inmutable y se utiliza para identificar de forma exclusiva los identificadores de registro. El `iothub_uri` permite a la API de registro del cliente de IoT Hub `IoTHubClient_LL_CreateFromDeviceAuth` conectarse a la instancia de IoT Hub correcta. 


Estas API ayudan a su dispositivo a conectarse y registrarse con el servicio Device Provisioning cuando este arranca, y a obtener la información sobre la instancia de IoT Hub y conectarse a esta. El archivo `provisioning_client/samples/prov_client_ll_sample/prov_client_ll_sample.c` muestra cómo utilizar estas API. En general, debe crear el siguiente marco para el registro de cliente:

```C
static const char* global_uri = "global.azure-devices-provisioning.net";
static const char* id_scope = "[ID scope for your provisioning service]";
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
    SECURE_DEVICE_TYPE hsm_type;
    hsm_type = SECURE_DEVICE_TYPE_TPM;
    //hsm_type = SECURE_DEVICE_TYPE_X509;
    prov_dev_security_init(hsm_type); // initialize your HSM 

    prov_transport = Prov_Device_HTTP_Protocol;
    
    PROV_CLIENT_LL_HANDLE handle = Prov_Device_LL_Create(global_uri, id_scope, prov_transport); // Create your provisioning client

    if (Prov_Client_LL_Register_Device(handle, register_callback, &user_info, register_status, &user_info) == IOTHUB_DPS_OK) {
        do {
        // The register_callback is called when registration is complete or fails
            Prov_Client_LL_DoWork(handle);
        } while (user_info.reg_complete == 0);
    }
    Prov_Client_LL_Destroy(handle); // Clean up the Provisioning client
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


## <a name="next-steps"></a>pasos siguientes
En este tutorial aprendió lo siguiente:

> [!div class="checklist"]
> * Seleccionar un módulo de seguridad de hardware
> * Compilar el SDK del cliente de aprovisionamiento de dispositivos para el HSM seleccionado
> * Extracción de los artefactos de seguridad
> * Establecer la configuración del servicio Device Provisioning en el dispositivo

Avance hasta el siguiente tutorial para aprender a aprovisionar el dispositivo en su instancia de IoT Hub inscribiéndolo en el servicio Azure IoT Hub Device Provisioning para su aprovisionamiento automático.

> [!div class="nextstepaction"]
> [Aprovisionar el dispositivo en la instancia de IoT Hub](tutorial-provision-device-to-hub.md)

