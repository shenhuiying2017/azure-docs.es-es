---
title: Conectar un dispositivo mediante C en Linux | Microsoft Docs
description: "Se describe cómo conectar un dispositivo a la solución de supervisión remota preconfigurada del Conjunto de IoT de Azure mediante una aplicación creada en C que se ejecuta en Linux."
services: 
suite: iot-suite
documentationcenter: na
author: dominicbetts
manager: timlt
editor: 
ms.assetid: 0c7c8039-0bbf-4bb5-9e79-ed8cff433629
ms.service: iot-suite
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/02/2017
ms.author: dobett
ms.openlocfilehash: a5768041a13d5ddc355c054dc85ba651b0752aba
ms.sourcegitcommit: 295ec94e3332d3e0a8704c1b848913672f7467c8
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/06/2017
---
# <a name="connect-your-device-to-the-remote-monitoring-preconfigured-solution-linux"></a>Conectar el dispositivo a la solución preconfigurada de supervisión remota (Linux)
[!INCLUDE [iot-suite-v1-selector-connecting](../../includes/iot-suite-v1-selector-connecting.md)]

## <a name="build-and-run-a-sample-c-client-linux"></a>Compilación y ejecución de una aplicación cliente de C de ejemplo (Linux)
En los siguientes pasos se explica cómo crear una aplicación cliente que se comunique con la solución preconfigurada de supervisión remota. Esta aplicación se escribe en C y se compila y ejecuta en Ubuntu Linux.

Para completar estos pasos, necesita un dispositivo que ejecute la versión 15.04 o 15.10 de Ubuntu. Antes de continuar, instale los paquetes de requisitos previos en el dispositivo Ubuntu ejecutando el siguiente comando:

```
sudo apt-get install cmake gcc g++
```

## <a name="install-the-client-libraries-on-your-device"></a>Instalación de las bibliotecas de cliente en el dispositivo
Las bibliotecas de cliente de Azure IoT Hub están disponibles como un paquete que puede instalar en su dispositivo Ubuntu ejecutando el comando **apt-get** . Complete los pasos siguientes para instalar el paquete que contiene la biblioteca de cliente de IoT Hub y los archivos de encabezado de su equipo Ubuntu:

1. En un shell, agregue el repositorio de AzureIoT al equipo:
   
    ```
    sudo add-apt-repository ppa:aziotsdklinux/ppa-azureiot
    sudo apt-get update
    ```
2. Instale el paquete azure-iot-sdk-c-dev:
   
    ```
    sudo apt-get install -y azure-iot-sdk-c-dev
    ```

## <a name="install-the-parson-json-parser"></a>Instalación del analizador JSON de Parson
Las bibliotecas de cliente de IoT Hub emplean el analizador JSON de Parson para analizar cargas de mensajes. En una carpeta adecuada del equipo, clone el repositorio GitHub de Parson mediante el siguiente comando:

```
git clone https://github.com/kgabis/parson.git
```

## <a name="prepare-your-project"></a>Preparación del proyecto
En la máquina Ubuntu, cree una carpeta denominada **remote\_monitoring**. En la carpeta **remote\_monitoring**:

- Cree los cuatro archivos **main.c**, **remote\_monitoring.c**, **remote\_monitoring.h** y **CMakeLists.txt**.
- Cree la carpeta denominada **parson**.

Copie los archivos **parson.c** y **parson.h** de la copia local del repositorio Parson en la carpeta **remote\_monitoring/parson**.

En un editor de texto, abra el archivo **remote\_monitoring.c**. Agregue las instrucciones siguientes `#include` :
   
```
#include "iothubtransportmqtt.h"
#include "schemalib.h"
#include "iothub_client.h"
#include "serializer_devicetwin.h"
#include "schemaserializer.h"
#include "azure_c_shared_utility/threadapi.h"
#include "azure_c_shared_utility/platform.h"
#include "parson.h"
```

[!INCLUDE [iot-suite-v1-connecting-code](../../includes/iot-suite-v1-connecting-code.md)]

## <a name="call-the-remotemonitoringrun-function"></a>Llame a la función remote\_monitoring\_run.
En un editor de texto, abra el archivo **remote_monitoring.h**. Agregue el siguiente código:

```
void remote_monitoring_run(void);
```

En un editor de texto, abra el archivo **main.c** . Agregue el siguiente código:

```
#include "remote_monitoring.h"

int main(void)
{
    remote_monitoring_run();

    return 0;
}
```

## <a name="build-and-run-the-application"></a>Compilación y ejecución de la aplicación
En los pasos siguientes se describe cómo puede utilizar *CMake* para compilar la aplicación cliente.

1. En un editor de texto, abra el archivo **CMakeLists.txt** de la carpeta **remote_monitoring**.

1. Agregue las siguientes instrucciones para definir cómo compilar la aplicación cliente:
   
    ```
    macro(compileAsC99)
      if (CMAKE_VERSION VERSION_LESS "3.1")
        if (CMAKE_C_COMPILER_ID STREQUAL "GNU")
          set (CMAKE_C_FLAGS "--std=c99 ${CMAKE_C_FLAGS}")
          set (CMAKE_CXX_FLAGS "--std=c++11 ${CMAKE_CXX_FLAGS}")
        endif()
      else()
        set (CMAKE_C_STANDARD 99)
        set (CMAKE_CXX_STANDARD 11)
      endif()
    endmacro(compileAsC99)

    cmake_minimum_required(VERSION 2.8.11)
    compileAsC99()

    set(AZUREIOT_INC_FOLDER "${CMAKE_SOURCE_DIR}" "${CMAKE_SOURCE_DIR}/parson" "/usr/include/azureiot" "/usr/include/azureiot/inc")

    include_directories(${AZUREIOT_INC_FOLDER})

    set(sample_application_c_files
        ./parson/parson.c
        ./remote_monitoring.c
        ./main.c
    )

    set(sample_application_h_files
        ./parson/parson.h
        ./remote_monitoring.h
    )

    add_executable(sample_app ${sample_application_c_files} ${sample_application_h_files})

    target_link_libraries(sample_app
        serializer
        iothub_client
        iothub_client_mqtt_transport
        aziotsharedutil
        umqtt
        pthread
        curl
        ssl
        crypto
        m
    )
    ```
1. En la carpeta **remote_monitoring**, cree otra para almacenar los archivos *make* que genera CMake y, después, ejecute los comandos **cmake** y **make**, tal y como se muestra a continuación:
   
    ```
    mkdir cmake
    cd cmake
    cmake ../
    make
    ```

1. Ejecute la aplicación cliente y envíe los datos telemetría a IoT Hub:
   
    ```
    ./sample_app
    ```

[!INCLUDE [iot-suite-v1-visualize-connecting](../../includes/iot-suite-v1-visualize-connecting.md)]

