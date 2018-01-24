---
title: "Aprovisionamiento de Raspberry Pi para la supervisión remota con C en Azure | Microsoft Docs"
description: "Se describe cómo conectar un dispositivo Raspberry Pi a la solución de supervisión remota preconfigurada del Conjunto de aplicaciones de IoT de Azure mediante una aplicación creada en C."
services: iot-suite
suite: iot-suite
documentationcenter: na
author: dominicbetts
manager: timlt
editor: 
ms.assetid: fc50a33f-9fb9-42d7-b1b8-eb5cff19335e
ms.service: iot-suite
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/03/2018
ms.author: dobett
ms.openlocfilehash: 7cfa6dd93c6db7477e03ff966b2ac8af15de3614
ms.sourcegitcommit: 2e540e6acb953b1294d364f70aee73deaf047441
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/03/2018
---
# <a name="connect-your-raspberry-pi-device-to-the-remote-monitoring-preconfigured-solution-c"></a>Conexión del dispositivo Raspberry Pi a la solución preconfigurada de supervisión remota (C)

[!INCLUDE [iot-suite-selector-connecting](../../includes/iot-suite-selector-connecting.md)]

Este tutorial muestra cómo conectar un dispositivo físico a la solución preconfigurada de supervisión remota. Como sucede con la mayoría de aplicaciones insertadas que se ejecutan en dispositivos restringidos, el código cliente para la aplicación del dispositivo Raspberry Pi se escribe en C. En este tutorial, compilará la aplicación en un dispositivo Raspberry Pi que ejecuta el sistema operativo Raspbian.

### <a name="required-hardware"></a>Requisitos de hardware

Un equipo de escritorio que permita conectarse remotamente a la línea de comandos en Raspberry Pi.

[Kit de inicio de Microsoft IoT para Raspberry Pi 3](https://azure.microsoft.com/develop/iot/starter-kits/) o componentes equivalentes. En este tutorial se usan los siguientes elementos del kit:

- Raspberry Pi 3
- Tarjeta MicroSD (con NOOBS)
- Un cable USB mini
- Un cable Ethernet

### <a name="required-desktop-software"></a>Requiere el software de escritorio

Necesita el cliente SSH en su máquina de escritorio para poder acceder de forma remota a la línea de comandos en su Raspberry Pi.

- Windows no incluye ningún cliente SSH. Se recomienda usar [PuTTY](http://www.putty.org/).
- La mayoría de las distribuciones de Linux y Mac OS incluyen la utilidad de línea de comandos de SSH. Para más información, consulte [SSH Using Linux or Mac OS](https://www.raspberrypi.org/documentation/remote-access/ssh/unix.md) (SSH cuando se usa Linux o Mac OS).

### <a name="required-raspberry-pi-software"></a>Software necesario de Raspberry Pi

En este artículo se supone que instaló la versión más reciente del [sistema operativo Raspbian OS en un dispositivo Raspberry Pi](https://www.raspberrypi.org/learning/software-guide/quickstart/).

Los pasos siguientes muestran cómo preparar el dispositivo Raspberry Pi para compilar una aplicación C que se conecta a la solución preconfigurada:

1. Conecte con su dispositivo Raspberry Pi mediante **ssh**. Para obtener más información, consulte [SSH (Secure Shell)](https://www.raspberrypi.org/documentation/remote-access/ssh/README.md) en el [sitio web de Raspberry Pi](https://www.raspberrypi.org/).

1. Use el siguiente comando para actualizar Raspberry Pi:

    ```sh
    sudo apt-get update
    ```

1. Use el siguiente comando para agregar las herramientas de desarrollo y bibliotecas necesarias a su dispositivo Raspberry Pi:

    ```sh
    sudo apt-get purge libssl-dev
    sudo apt-get install g++ make cmake gcc git libssl1.0-dev build-essential curl libcurl4-openssl-dev uuid-dev
    ```

1. Use los comandos siguientes para descargar, compilar e instalar las bibliotecas de cliente de IoT Hub en el dispositivo Raspberry Pi:

    ```sh
    cd ~
    git clone --recursive https://github.com/azure/azure-iot-sdk-c.git
    cd azure-iot-sdk-c/build_all/linux
    ./build.sh --no-make
    cd ../../cmake/iotsdk_linux
    make
    sudo make install
    ```

## <a name="create-a-project"></a>Creación de un proyecto

Complete los pasos siguientes con la conexión **ssh** al dispositivo Raspberry Pi:

1. Cree una carpeta denominada `remote_monitoring` en la carpeta particular en el dispositivo Raspberry Pi. Vaya a esta carpeta en el shell:

    ```sh
    cd ~
    mkdir remote_monitoring
    cd remote_monitoring
    ```

1. Cree los cuatro archivos **main.c**, **remote_monitoring.c**, **remote_monitoring.h** y **CMakeLists.txt** en la carpeta `remote_monitoring`.

1. En un editor de texto, abra el archivo **remote_monitoring.c**. En el dispositivo Raspberry Pi, puede usar el editor de texto **nano** o **vi**. Agregue las instrucciones siguientes `#include` :

    ```c
    #include "iothubtransportmqtt.h"
    #include "schemalib.h"
    #include "iothub_client.h"
    #include "serializer_devicetwin.h"
    #include "schemaserializer.h"
    #include "azure_c_shared_utility/threadapi.h"
    #include "azure_c_shared_utility/platform.h"
    #include "parson.h"
    ```

[!INCLUDE [iot-suite-connecting-code](../../includes/iot-suite-connecting-code.md)]

Guarde el archivo **remote_monitoring.c** y salga del editor.

## <a name="add-code-to-run-the-app"></a>Incorporación de código para ejecutar la aplicación

En un editor de texto, abra el archivo **remote_monitoring.h**. Agregue el siguiente código:

```c
void remote_monitoring_run(void);
```

Guarde el archivo **remote_monitoring.h** y salga del editor.

En un editor de texto, abra el archivo **main.c** . Agregue el siguiente código:

```c
#include "remote_monitoring.h"

int main(void)
{
  remote_monitoring_run();

  return 0;
}
```

Guarde el archivo **main.c** y salga del editor.

## <a name="build-and-run-the-application"></a>Compilación y ejecución de la aplicación

En los pasos siguientes se describe cómo puede utilizar *CMake* para compilar la aplicación cliente.

1. En un editor de texto, abra el archivo **CMakeLists.txt** de la carpeta `remote_monitoring`.

1. Agregue las siguientes instrucciones para definir cómo compilar la aplicación cliente:

    ```cmake
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

    set(AZUREIOT_INC_FOLDER "${CMAKE_SOURCE_DIR}" "/usr/local/include/azureiot")

    include_directories(${AZUREIOT_INC_FOLDER})

    set(sample_application_c_files
        ./remote_monitoring.c
        ./main.c
    )

    set(sample_application_h_files
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

1. Guarde el archivo **CMakeLists.txt** y salga del editor.

1. En la carpeta `remote_monitoring`, cree otra carpeta para almacenar los archivos *make* que CMake genera. A continuación, ejecute los comandos **cmake** y **make**, como se indica a continuación:

    ```sh
    mkdir cmake
    cd cmake
    cmake ../
    make
    ```

1. Ejecute la aplicación cliente y envíe los datos telemetría a IoT Hub:

    ```sh
    ./sample_app
    ```

[!INCLUDE [iot-suite-visualize-connecting](../../includes/iot-suite-visualize-connecting.md)]
