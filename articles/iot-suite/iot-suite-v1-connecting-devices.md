---
title: Conectar un dispositivo mediante C en Windows | Microsoft Docs
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
ms.date: 11/02/2017
ms.author: dobett
ms.openlocfilehash: 56c58a31c6b20bd8da7d1442ae75425cb3e20f3d
ms.sourcegitcommit: 295ec94e3332d3e0a8704c1b848913672f7467c8
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/06/2017
---
# <a name="connect-your-device-to-the-remote-monitoring-preconfigured-solution-windows"></a>Conectar el dispositivo a la solución preconfigurada de supervisión remota (Windows)
[!INCLUDE [iot-suite-v1-selector-connecting](../../includes/iot-suite-v1-selector-connecting.md)]

## <a name="create-a-c-sample-solution-on-windows"></a>Creación de una solución de ejemplo de C en Windows
En los siguientes pasos se explica cómo crear una aplicación cliente que se comunique con la solución preconfigurada de supervisión remota. Esta aplicación se escribe en C y se compila y ejecuta en Windows.

Cree un proyecto inicial en Visual Studio 2015 o Visual Studio 2017 y agregue los paquetes NuGet del cliente de dispositivo de IoT Hub:

1. En Visual Studio, cree una aplicación de consola de C mediante la plantilla **Aplicación de consola Win32** de Visual C++. Asigne al proyecto el nombre **RMDevice**.
2. En la página **Configuración de la aplicación** del **Asistente para aplicaciones Win32**, asegúrese de que se ha seleccionado **Aplicación de consola**, y desactive la opción **Encabezado precompilado** y **Habilitar comprobaciones adicionales del ciclo de vida de desarrollo de seguridad (SDL)**.
3. En el **Explorador de soluciones**, elimine los archivos stdafx.h, targetver.h y stdafx.cpp.
4. En el **Explorador de soluciones**, cambie el nombre del archivo RMDevice.cpp a RMDevice.c.
5. En el **Explorador de soluciones**, haga clic con el botón derecho en el proyecto **RMDevice** y, después, haga clic en **Administrar paquetes NuGet**. Haga clic en **Examinar**y busque e instale los siguientes paquetes NuGet:
   
   * Microsoft.Azure.IoTHub.Serializer
   * Microsoft.Azure.IoTHub.IoTHubClient
   * Microsoft.Azure.IoTHub.MqttTransport
6. En el **Explorador de soluciones**, haga clic con el botón derecho en el proyecto **RMDevice** y, después, haga clic en **Propiedades** para abrir el cuadro de diálogo **Páginas de propiedades** del proyecto. Para obtener información, consulte [Setting Visual C++ Project Properties][lnk-c-project-properties] (Establecimiento de propiedades de proyectos de Visual C++). 
7. Haga clic en la carpeta **Vinculador** y, luego, haga clic en la página de propiedades **Entrada**.
8. Agregue **crypt32.lib** a la propiedad **Dependencias adicionales**. Haga clic en **Aceptar** y, luego, otra vez en **Aceptar** para guardar los valores de propiedad de proyecto.

Agregue la biblioteca JSON de Parson al proyecto **RMDevice** y agregue las instrucciones `#include` necesarias:

1. En una carpeta adecuada del equipo, clone el repositorio GitHub de Parson mediante el siguiente comando:

    ```
    git clone https://github.com/kgabis/parson.git
    ```

1. Copie los archivos parson.h y parson.c de la copia local del repositorio de Parson a su carpeta de proyecto **RMDevice**.

1. En Visual Studio, haga clic con el botón derecho en el proyecto **RMDevice**, haga clic en **Agregar** y luego haga clic en **Elemento existente**.

1. En el diálogo **Agregar elemento existente**, seleccione los archivos parson.h y parson.c en la carpeta de proyecto **RMDevice**. A continuación, haga clic en **Agregar** para agregar estos dos archivos al proyecto.

1. En Visual Studio, abra el archivo RMDevice.c. Sustituya las instrucciones `#include` existentes por el siguiente código:
   
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
    > Ahora puede compilar el proyecto para comprobar que tiene configuradas las dependencias correctas.

[!INCLUDE [iot-suite-v1-connecting-code](../../includes/iot-suite-v1-connecting-code.md)]

## <a name="build-and-run-the-sample"></a>Compilación y ejecución del ejemplo

Agregue código para invocar la función **remote\_monitoring\_run** y luego compile y ejecute la aplicación del dispositivo.

1. Sustituya la función **main** por el código siguiente para invocar la función **remote\_monitoring\_run**:
   
    ```c
    int main()
    {
      remote_monitoring_run();
      return 0;
    }
    ```

1. Haga clic en **Compilar** y, después, en **Compilar solución** para compilar la aplicación del dispositivo.

1. En el **Explorador de soluciones**, haga clic con el botón derecho en el proyecto **RMDevice**, haga clic en **Depurar** y, después, en **Iniciar nueva instancia** para ejecutar el ejemplo. En la consola se muestran mensajes a medida que la aplicación envía telemetría de ejemplo a la solución preconfigurada, recibe valores de propiedad deseados establecidos en el panel de la solución y responde a los métodos invocados desde el panel de la solución.

[!INCLUDE [iot-suite-v1-visualize-connecting](../../includes/iot-suite-v1-visualize-connecting.md)]

[lnk-c-project-properties]: https://msdn.microsoft.com/library/669zx6zc.aspx
