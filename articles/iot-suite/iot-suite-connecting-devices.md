---
title: "Aprovisionamiento de dispositivos Windows para la supervisión remota con C - Azure | Microsoft Docs"
description: "Se describe cómo conectar un dispositivo a la solución de supervisión remota preconfigurada del Conjunto de IoT de Azure mediante una aplicación creada en C que se ejecuta en Windows."
services: 
suite: iot-suite
documentationcenter: na
author: dominicbetts
manager: timlt
editor: 
ms.assetid: 34e39a58-2434-482c-b3fa-29438a0c05e8
ms.service: iot-suite
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/12/2017
ms.author: dobett
ms.openlocfilehash: a6453c8d84b7025f1f510db5e675cde8685c23ed
ms.sourcegitcommit: 922687d91838b77c038c68b415ab87d94729555e
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/13/2017
---
# <a name="connect-your-device-to-the-remote-monitoring-preconfigured-solution-windows"></a>Conectar el dispositivo a la solución preconfigurada de supervisión remota (Windows)

[!INCLUDE [iot-suite-selector-connecting](../../includes/iot-suite-selector-connecting.md)]

Este tutorial muestra cómo conectar un dispositivo físico a la solución preconfigurada de supervisión remota.

## <a name="create-a-c-client-solution-on-windows"></a>Creación de una solución cliente de C en Windows

Como sucede con la mayoría de aplicaciones insertadas que se ejecutan en dispositivos restringidos, el código cliente para la aplicación del dispositivo está escrito en C. En este tutorial, se crea la aplicación en un equipo Windows.

### <a name="create-the-starter-project"></a>Creación del proyecto inicial

Cree un proyecto inicial en Visual Studio 2017 y agregue los paquetes NuGet del cliente de dispositivo de IoT Hub:

1. En Visual Studio, cree una aplicación de consola de C mediante la plantilla **Aplicación de consola Windows** de Visual C++. Asigne al proyecto el nombre **RMDevice**.

    ![Creación de una aplicación de consola de Windows en Visual C++](media/iot-suite-connecting-devices/visualstudio01.png)

1. En el **Explorador de soluciones**, elimine los archivos `stdafx.h`, `targetver.h` y `stdafx.cpp`.

1. En el **Explorador de soluciones**, cambie el nombre del archivo `RMDevice.cpp` a `RMDevice.c`.

    ![El Explorador de soluciones muestra el archivo RMDevice.c después del cambio de nombre](media/iot-suite-connecting-devices/visualstudio02.png)

1. En el **Explorador de soluciones**, haga clic con el botón derecho en el proyecto **RMDevice** y, después, haga clic en **Administrar paquetes NuGet**. Seleccione **Examinar** y busque e instale los siguientes paquetes NuGet:

    * Microsoft.Azure.IoTHub.Serializer
    * Microsoft.Azure.IoTHub.IoTHubClient
    * Microsoft.Azure.IoTHub.MqttTransport

    ![El administrador de paquetes de NuGet muestra los paquetes Microsoft.Azure.IoTHub instalados](media/iot-suite-connecting-devices/visualstudio03.png)

1. En el **Explorador de soluciones**, haga clic con el botón derecho en el proyecto **RMDevice** y, después, seleccione **Propiedades** para abrir el cuadro de diálogo **Páginas de propiedades** del proyecto. Para obtener información, consulte [Setting Visual C++ Project Properties](https://docs.microsoft.com/cpp/ide/working-with-project-properties) (Establecimiento de propiedades de proyectos de Visual C++).

1. Elija la carpeta **C/C++** y, a continuación, elija la página de propiedades **Encabezados precompilados**.

1. Establezca **Encabezado precompilado** en **No utilizar encabezados precompilados**. Luego elija **Aplicar**.

    ![Las propiedades del proyecto muestran que el proyecto no usa encabezados precompilados](media/iot-suite-connecting-devices/visualstudio04.png)

1. Elija la carpeta **Enlazador** y, luego, elija la página de propiedades **Entrada**.

1. Agregue `crypt32.lib` a la propiedad **Dependencias adicionales**. Elija **Aceptar** y, luego, otra vez **Aceptar** para guardar los valores de propiedades del proyecto.

    ![Las propiedades de proyecto muestran que el enlazador incluye crypt32.lib](media/iot-suite-connecting-devices/visualstudio05.png)

### <a name="add-the-parson-json-library"></a>Adición de la biblioteca JSON de Parson

Agregue la biblioteca JSON de Parson al proyecto **RMDevice** y agregue las instrucciones `#include` necesarias:

1. En una carpeta adecuada del equipo, clone el repositorio GitHub de Parson mediante el siguiente comando:

    ```cmd
    git clone https://github.com/kgabis/parson.git
    ```

1. Copie los archivos `parson.h` y `parson.c` desde la copia local del repositorio de Parson a la carpeta del proyecto **RMDevice**.

1. En Visual Studio, haga clic con el botón derecho en el proyecto **RMDevice**, elija **Agregar** y luego elija **Elemento existente**.

1. En el cuadro de diálogo **Agregar elemento existente**, seleccione los archivos `parson.h` y `parson.c` en la carpeta del proyecto **RMDevice**. Para agregar estos dos archivos al proyecto, elija **Agregar**.

    ![El Explorador de soluciones muestra los archivos parson.h y parson.c](media/iot-suite-connecting-devices/visualstudio06.png)

1. En Visual Studio, abra el archivo `RMDevice.c`. Sustituya las instrucciones `#include` existentes por el siguiente código:

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

    > [!NOTE]
    > Ahora puede compilar la solución para comprobar que el proyecto tiene configuradas las dependencias correctas.

[!INCLUDE [iot-suite-connecting-code](../../includes/iot-suite-connecting-code.md)]

## <a name="build-and-run-the-sample"></a>Compilación y ejecución del ejemplo

Agregue código para invocar la función **remote\_monitoring\_run** y luego compile y ejecute la aplicación del dispositivo:

1. Para invocar la función **remote\_monitoring\_run**, reemplace la función **main** por el código siguiente:

    ```c
    int main()
    {
      remote_monitoring_run();
      return 0;
    }
    ```

1. Elija **Compilar** y, después, **Compilar solución** para compilar la aplicación del dispositivo. Pase por alto la advertencia sobre la función **gmtime**.

1. En el **Explorador de soluciones**, haga clic con el botón derecho en el proyecto **RMDevice**, elija **Depurar** y, después, elija **Iniciar nueva instancia** para ejecutar el ejemplo. La consola muestra mensajes como:

    * La aplicación envía datos de telemetría de ejemplo a la solución preconfigurada.
    * Recibe los valores de propiedad establecidos en el panel de la solución.
    * Responde a los métodos invocados desde el panel de la solución.

[!INCLUDE [iot-suite-visualize-connecting](../../includes/iot-suite-visualize-connecting.md)]
