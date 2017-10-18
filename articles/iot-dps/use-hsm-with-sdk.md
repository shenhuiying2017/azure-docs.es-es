---
title: "Guía de Azure: uso de distintos modelos de seguridad de hardware con el SDK de cliente del servicio Device Provisioning en Azure | Microsoft Docs"
description: "Guía de Azure: uso de distintos modelos de seguridad de hardware con el SDK de cliente del servicio Device Provisioning en Azure"
services: iot-dps
keywords: 
author: yzhong94
ms.author: yizhon
ms.date: 08/28/2017
ms.topic: hero-article
ms.service: iot-dps
documentationcenter: 
manager: 
ms.devlang: na
ms.custom: mvc
ms.openlocfilehash: 620d86b62cf43c3e1a5f7f5c724fcf00174f30e8
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="how-to-use-different-hardware-security-modules-with-device-provisioning-service-client-sdk"></a>Cómo usar diferentes módulos de seguridad de hardware con el SDK de cliente del servicio de aprovisionamiento de dispositivos
Estos pasos muestran cómo usar diferentes [Módulos de seguridad de hardware (HSM)](https://azure.microsoft.com/blog/azure-iot-supports-new-security-hardware-to-strengthen-iot-security/) con el SDK de cliente del servicio de aprovisionamiento de dispositivos en C con dispositivo físico y simulador.  El servicio de aprovisionamiento admite dos modos de autenticación: X**.**509 y Módulo de plataforma segura (TPM).

## <a name="prerequisites"></a>Requisitos previos

Prepare el entorno de desarrollo según la sección "Preparación del entorno de desarrollo" de la guía [Creación y aprovisionamiento de un dispositivo simulado] (./quick-create-simulated-device.md).

## <a name="enable-authentication-with-different-hsms"></a>Habilitación de la autenticación con diferentes HSM

El modo de autenticación (X**.**509 o TPM) debe habilitarse para el dispositivo físico o el simulador antes de que se puedan inscribir en Azure Portal.  Navegue hasta la carpeta raíz para azure-iot-sdk-c.  Ejecute el comando especificado según el modo de autenticación que elija.

### <a name="use-x509-with-simulator"></a>Uso de X**.**509 con el simulador

El servicio de aprovisionamiento se suministra con un emulador de Motor de composición de identidad de dispositivo (DICE) que genera un certificado X**.**509 para autenticar el dispositivo.  Ejecute el siguiente comando para habilitar la autenticación X**.**509:

```
cmake -Ddps_auth_type=x509 ..
```

Puede encontrar información sobre el hardware con DICE [aquí](https://azure.microsoft.com/blog/azure-iot-supports-new-security-hardware-to-strengthen-iot-security/).

### <a name="use-x509-with-hardware"></a>Uso de X**.**509 con hardware

El servicio de aprovisionamiento puede utilizarse con X**.**509 en otro hardware.  Se necesita una interfaz entre el hardware y el SDK para establecer la conexión.  Póngase en contacto con el fabricante del HSM para obtener información sobre la interfaz.

### <a name="use-tpm"></a>Uso de TPM

El servicio de aprovisionamiento puede conectarse con chips de hardware TPM en Windows y Linux con token de SAS.  Ejecute el siguiente comando para habilitar la autenticación TPM:

```
cmake -Ddps_auth_type=tpm ..
```

### <a name="use-tpm-with-simulator"></a>Uso de TPM con el simulador

Si no tiene un dispositivo con chips TPM, puede usar un simulador con fines de desarrollo en el sistema operativo Windows.  Ejecute el siguiente comando para habilitar la autenticación TPM y ejecutar el simulador TPM:

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

## <a name="create-a-device-enrollment-entry-in-dps"></a>Creación de una entrada de inscripción de dispositivo en DPS

### <a name="tpm"></a>TPM
Si está utilizando TPM, siga las instrucciones de ["Creación y aprovisionamiento de un dispositivo simulado con el servicio IoT Hub Device Provisioning"](./quick-create-simulated-device.md) para crear una entrada de inscripción de dispositivo en DPS y simular el primer inicio.

### <a name="x509"></a>X**.**509
1. Para inscribir un dispositivo en el servicio de aprovisionamiento, necesitará tomar nota de la clave de aprobación y del identificador de registro de cada dispositivo, que se muestran en la Herramienta de aprovisionamiento proporcionada por el SDK de cliente. Ejecute el siguiente comando para imprimir el certificado de CA raíz (para la inscripción de grupos) y el certificado del firmante (para la inscripción individual):
      ```
      ./azure-iot-sdk-c/dps_client/tools/x509_device_provision/x509_device_provision.exe
      ```
2. Inicie sesión en Azure Portal, haga clic en el botón **Todos los recursos** situado en el menú izquierdo y abra el servicio DPS.
   - Inscripción individual X**.**509: En la hoja de resumen del servicio de aprovisionamiento, seleccione **Administrar inscripciones**. Seleccione la pestaña **Individual Enrollments** (Inscripciones individuales) y haga clic en el botón **Add** (Agregar) de la parte superior. Seleccione **X**.**509** como *Mecanismo* de atestación de identidad y cargue el certificado del firmante como indica la hoja. Una vez completado, haga clic en el botón **Guardar**. 
   - Inscripción de grupo X**.**509: En la hoja de resumen del servicio de aprovisionamiento, seleccione **Administrar inscripciones**. Seleccione la pestaña **Inscripción de grupo** y haga clic en el botón **Agregar** de la parte superior. Seleccione **X**.**509** como *Mecanismo* de atestación de identidad, escriba un nombre de grupo y nombre de certificación y cargue el certificado de CA raíz como indica la hoja. Una vez completado, haga clic en el botón **Guardar**. 

## <a name="connecting-to-iot-hub-after-provisioning"></a>Conexión a IoT Hub después del aprovisionamiento

Una vez que el dispositivo se haya aprovisionado con el servicio de aprovisionamiento, esta API usa el modo de autenticación del HSM para conectarse con IoT Hub: 
  ```
  IOTHUB_CLIENT_LL_HANDLE handle = IoTHubClient_LL_CreateFromDeviceAuth(iothub_uri, device_id, iothub_transport);
  ```
