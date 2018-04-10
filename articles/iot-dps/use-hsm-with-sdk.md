---
title: 'Guía de Azure: uso de distintos módulos de seguridad de hardware con el SDK de cliente del servicio Device Provisioning en Azure'
description: 'Guía de Azure: uso de distintos módulos de seguridad de hardware con el SDK de cliente del servicio Device Provisioning en Azure'
services: iot-dps
keywords: ''
author: yzhong94
ms.author: yizhon
ms.date: 03/28/2018
ms.topic: hero-article
ms.service: iot-dps
documentationcenter: ''
manager: ''
ms.devlang: na
ms.custom: mvc
ms.openlocfilehash: 0d392f4a8d935cb37b6f4cfcd69826de58b33880
ms.sourcegitcommit: 34e0b4a7427f9d2a74164a18c3063c8be967b194
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 03/30/2018
---
# <a name="how-to-use-different-hardware-security-modules-with-device-provisioning-service-client-sdk-for-c"></a>Uso de diferentes módulos de seguridad de hardware con el SDK de cliente del servicio Device Provisioning para C

Este artículo muestra cómo usar diferentes [módulos de seguridad de hardware (HSM)](https://azure.microsoft.com/blog/azure-iot-supports-new-security-hardware-to-strengthen-iot-security/) con el SDK de cliente del servicio Device Provisioning para C. Puede usar un dispositivo físico o un simulador. El servicio de aprovisionamiento admite la autenticación de dos tipos de mecanismos de atestación: X**.**509 y Módulo de plataforma segura (TPM).

## <a name="prerequisites"></a>requisitos previos

Prepare el entorno de desarrollo tal como se indica en la sección "Preparación del entorno de desarrollo" de la guía [Creación y aprovisionamiento de un dispositivo simulado](./quick-create-simulated-device.md).

### <a name="choose-a-hardware-security-module"></a>Selección de un módulo de seguridad de hardware

Lo primero que necesitan hacer los fabricantes de dispositivos es elegir módulos de seguridad de hardware (o HSM) que se basen en uno de los tipos compatibles. Actualmente, el [SDK de cliente del servicio Device Provisioning para C](https://github.com/Azure/azure-iot-sdk-c/tree/master/provisioning_client) proporciona compatibilidad con los siguientes HSM: 

- [Módulo de plataforma segura (TPM)](https://en.wikipedia.org/wiki/Trusted_Platform_Module): TPM es un estándar establecido para la mayoría de las plataformas de dispositivos con Windows, así como para algunos dispositivos con Linux y Ubuntu. Los fabricantes de dispositivos pueden elegir este HSM si alguno de estos sistemas operativos se ejecutan en sus dispositivos y si buscan un estándar establecido para los HSM. Con los chips de TPM, solo puede inscribir cada dispositivo de forma individual en el servicio Device Provisioning. Con fines de desarrollo, puede usar el simulador TPM en la máquina Windows o Linux de desarrollo.

- [X.509](https://cryptography.io/en/latest/x509/): los HSM basados en X.509 son chips relativamente más recientes. Work también progresa en Microsoft, en chips RIoT o DICE, que implementan los certificados X.509. Con los chips X.509, puede realizar una inscripción masiva de dispositivos en el portal. También admite ciertos sistemas operativos distintos de Windows como embedOS. Con fines de desarrollo, el SDK del cliente del servicio Device Provisioning admite un simulador de dispositivos X.509. 

Para más información, consulte [Conceptos de seguridad del servicio Azure IoT Hub Device Provisioning](concepts-security.md). 

## <a name="enable-authentication-for-supported-hsms"></a>Habilitación de la autenticación en los HSM admitidos

El modo de autenticación (X**.**509 o TPM) debe habilitarse para el dispositivo físico o el simulador antes de que se puedan inscribir en Azure Portal. En primer lugar, vaya a la carpeta raíz para encontrar azure-iot-sdk-c. Luego, ejecute el comando especificado según el modo de autenticación que haya elegido:

### <a name="use-x509-with-simulator"></a>Uso de X**.**509 con el simulador

El servicio de aprovisionamiento se suministra con un emulador de Device Identity Composition Engine (DICE) que genera un certificado X**.**509 para autenticar el dispositivo. Para habilitar la autenticación X**.**509, ejecute el siguiente comando: 

```
cmake -Ddps_auth_type=x509 ..
```

Puede encontrar información sobre el hardware con DICE [aquí](https://azure.microsoft.com/blog/azure-iot-supports-new-security-hardware-to-strengthen-iot-security/).

### <a name="use-x509-with-hardware"></a>Uso de X**.**509 con hardware

El servicio de aprovisionamiento puede utilizarse con X**.**509 en otro hardware. Se necesita una interfaz entre el hardware y el SDK para establecer la conexión. Póngase en contacto con el fabricante del HSM para obtener información sobre la interfaz.

### <a name="use-tpm"></a>Uso de TPM

El servicio de aprovisionamiento puede conectarse con chips de hardware TPM en Windows y Linux con token de SAS. Para habilitar la autenticación TPM, ejecute el siguiente comando:

```
cmake -Ddps_auth_type=tpm ..
```

### <a name="use-tpm-with-simulator"></a>Uso de TPM con el simulador

Si no tiene un dispositivo con chips TPM, puede usar un simulador con fines de desarrollo en el sistema operativo Windows. Para habilitar la autenticación TPM y ejecutar el simulador de TPM, ejecute el siguiente comando:

```
cmake -Ddps_auth_type=tpm_simulator ..
```

## <a name="build-the-sdk"></a>Compilación del SDK
Compile el SDK antes de crear la inscripción del dispositivo.

### <a name="linux"></a>Linux
- Para compilar el SDK en Linux:
    ```
    cd azure-iot-sdk-c
    mkdir cmake
    cd cmake
    cmake ..
    cmake --build .  # append '-- -j <n>' to run <n> jobs in parallel
    ```
- Para compilar archivos binarios de depuración, agregue la opción de CMake correspondiente al comando de generación del proyecto, por ejemplo:
    ```
    cmake -DCMAKE_BUILD_TYPE=Debug ..
    ```

- Hay muchas [Opciones de configuración de CMake](https://cmake.org/cmake/help/v3.6/manual/cmake.1.html) disponibles para la compilación del SDK. Por ejemplo, puede deshabilitar una de las pilas de protocolo disponibles mediante la adición de un argumento al comando de CMake de generación del proyecto:
    ```
    cmake -Duse_amqp=OFF ..
    ```
- También puede compilar y ejecutar pruebas unitarias:
    ```
    cmake -Drun_unittests=ON ..
    cmake --build .
    ctest -C "debug" -V
    ```

### <a name="windows"></a>Windows
- Para compilar el SDK en Windows, siga estos pasos para generar los archivos de proyecto:
    - Abra un "Símbolo del sistema para desarrolladores de VS2015"
    - Ejecute los siguientes comandos de CMake desde la raíz del repositorio:
      ```
      cd azure-iot-sdk-c
      mkdir cmake
      cd cmake
      cmake -G "Visual Studio 14 2015" ..
      ```
    Este comando compila bibliotecas para x86. Para compilar para x64, modifique el argumento del generador de cmake: 
    ```
    cmake .. -G "Visual Studio 14 2015 Win64"
    ```

- Si la generación del proyecto se completa correctamente, debería ver un archivo de solución de Visual Studio (.sln) en la carpeta `cmake`. Para compilar el SDK:
    - Abra **cmake\azure_iot_sdks.sln** en Visual Studio y compílelo, **o**
    - Ejecute el siguiente comando en el símbolo del sistema que ha usado para generar los archivos de proyecto:
      ```
      cmake --build . -- /m /p:Configuration=Release
      ```
- Para compilar archivos binarios de depuración, utilice el argumento de MSBuild correspondiente: 
    ```
    cmake --build . -- /m /p:Configuration=Debug`
    ```
- Hay muchas opciones de configuración de CMake disponibles para la compilación del SDK. Por ejemplo, puede deshabilitar una de las pilas de protocolo disponibles mediante la adición de un argumento al comando de CMake de generación del proyecto:
    ```
    cmake -G "Visual Studio 14 2015" -Duse_amqp=OFF ..
    ```
- También puede compilar y ejecutar pruebas unitarias:
    ```
    cmake -G "Visual Studio 14 2015" -Drun_unittests=ON ..
    cmake --build . -- /m /p:Configuration=Debug
    ctest -C "debug" -V
    ```
  
### <a name="libraries-to-include"></a>Bibliotecas a incluir
- Estas bibliotecas deben incluirse en el SDK:
    - El servicio de aprovisionamiento: dps_http_transport, dps_client, dps_security_client
    - IoTHub Security: iothub_security_client

## <a name="create-a-device-enrollment-entry-in-device-provisioning-services"></a>Creación de una entrada de inscripción de dispositivos en los servicios Device Provisioning

### <a name="tpm"></a>TPM
Si utiliza TPM, siga las instrucciones de ["Creación y aprovisionamiento de un dispositivo de TPM simulado mediante el SDK de dispositivos C para el servicio Azure IoT Hub Device Provisioning"](./quick-create-simulated-device.md) para crear una entrada de inscripción de dispositivo en su servicio Device Provisioning y simular el primer arranque.

### <a name="x509"></a>X**.**509
1. Para inscribir un dispositivo en el servicio de aprovisionamiento, necesitará tomar nota de la clave de aprobación y del identificador de registro de cada dispositivo, que se muestran en la Herramienta de aprovisionamiento proporcionada por el SDK de cliente. Ejecute el siguiente comando para imprimir el certificado de CA raíz (para la inscripción de grupos) y el certificado del firmante (para la inscripción individual):
      ```
      ./azure-iot-sdk-c/dps_client/tools/x509_device_provision/x509_device_provision.exe
      ```
2. Inicie sesión en Azure Portal, haga clic en el botón **Todos los recursos** situado en el menú izquierdo y abra el servicio DPS.
   - Inscripción individual X**.**509: En la hoja de resumen del servicio de aprovisionamiento, seleccione **Administrar inscripciones**. Seleccione la pestaña **Individual Enrollments** (Inscripciones individuales) y haga clic en el botón **Add** (Agregar) de la parte superior. Seleccione **X**.**509** como *Mecanismo* de atestación de identidad y cargue el certificado del firmante como indica la hoja. Una vez completado, haga clic en el botón **Guardar**. 
   - Inscripción de grupo X**.**509: En la hoja de resumen del servicio de aprovisionamiento, seleccione **Administrar inscripciones**. Seleccione la pestaña **Inscripción de grupo** y haga clic en el botón **Agregar** de la parte superior. Seleccione **X**.**509** como *Mecanismo* de atestación de identidad, escriba un nombre de grupo y nombre de certificación y cargue el certificado de CA raíz como indica la hoja. Una vez completado, haga clic en el botón **Guardar**. 

## <a name="enable-authentication-for-custom-tpm-and-x509-devices-optional"></a>Habilitación de la autenticación de dispositivos TPM y X.509 personalizados (opcional)

> [!NOTE]
> Esta sección solo se puede aplicar a aquellos dispositivos que requieren compatibilidad con una plataforma personalizada o HSM, que actualmente no es compatible con el SDK de cliente del servicio Device Provisioning para C.

En primer lugar debe desarrollar su repositorio y biblioteca de HSM personalizados:

1. Desarrolle una biblioteca para tener acceso a los HSM. Este proyecto debe generar una biblioteca estática para el SDK de Device Provisioning que va a usar.

2. Implemente las funciones definidas en el siguiente archivo de encabezado de su biblioteca: 

    - Para un TPM personalizado: implemente las funciones de HSM personalizadas definidas en [API TPM de HSM](https://github.com/Azure/azure-iot-sdk-c/blob/master/provisioning_client/devdoc/using_custom_hsm.md#hsm-tpm-api).  
    - Para un X.509 personalizado: implemente las funciones de HSM personalizadas definidas en [API X509 de HSM](https://github.com/Azure/azure-iot-sdk-c/blob/master/provisioning_client/devdoc/using_custom_hsm.md#hsm-x509-api). 

Una vez que la biblioteca se ha compilado correctamente, será preciso integrarla con el SDK de cliente del servicio Device Provisioning para C; para ello, vincúlelo con la biblioteca. :

1. Especifique el repositorio de GitHub personalizado de HSM, la ruta de acceso a la biblioteca y su nombre en el siguiente comando `cmake`:
    ```cmd/sh
    cmake -Duse_prov_client:BOOL=ON -Dhsm_custom_lib=<path_and_name_of_library> <PATH_TO_AZURE_IOT_SDK>
    ```
   
2. Abra el archivo de la solución Visual Studio generado por CMake (`\azure-iot-sdk-c\cmake\azure_iot_sdks.sln`) y compílelo. 

    - El proceso compila la biblioteca de SDK.
    - SDK intentará crear un vínculo con el HSM personalizado definido en el comando `cmake`.

3. Ejecute la aplicación de ejemplo "prov_dev_client_ll_sample" de "Provision_Samples" (en `\azure-iot-sdk-c\cmake\provisioning_client\samples\prov_dev_client_ll_sample`) para comprobar que el HSM se ha implementado correctamente.

## <a name="connecting-to-iot-hub-after-provisioning"></a>Conexión a IoT Hub después del aprovisionamiento

Una vez que el dispositivo se haya aprovisionado con el servicio de aprovisionamiento, esta API usa el modo de autenticación del HSM para conectarse con IoT Hub: 
  ```
  IOTHUB_CLIENT_LL_HANDLE handle = IoTHubClient_LL_CreateFromDeviceAuth(iothub_uri, device_id, iothub_transport);
  ```

