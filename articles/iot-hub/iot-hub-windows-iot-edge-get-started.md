---
title: "Introducción a Azure IoT Edge (Windows) | Documentos de Microsoft"
description: "Cómo crear una puerta de enlace de Azure IoT Edge en un equipo Windows y obtener información sobre los conceptos claves de Azure IoT Edge, como los módulos y los archivos de configuración de JSON."
services: iot-hub
documentationcenter: 
author: chipalost
manager: timlt
editor: 
ms.assetid: 9aff3724-5e4e-40ec-b95a-d00df4f590c5
ms.service: iot-hub
ms.devlang: cpp
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/07/2017
ms.author: andbuc
ms.custom: H1Hack27Feb2017
ms.translationtype: Human Translation
ms.sourcegitcommit: 245ce9261332a3d36a36968f7c9dbc4611a019b2
ms.openlocfilehash: 5db39bab8e31a8e7026b34e72b4614b0f6f57772
ms.contentlocale: es-es
ms.lasthandoff: 06/09/2017


---
# <a name="explore-azure-iot-edge-architecture-on-windows"></a>Explorar la arquitectura de Azure IoT Edge en Windows

[!INCLUDE [iot-hub-iot-edge-getstarted-selector](../../includes/iot-hub-iot-edge-getstarted-selector.md)]

[!INCLUDE [iot-hub-iot-edge-install-build-windows](../../includes/iot-hub-iot-edge-install-build-windows.md)]

## <a name="how-to-run-the-sample"></a>Ejecución del ejemplo

El script **build.cmd** genera su salida en la carpeta **build** de la copia local del repositorio **iot-edge**. Esta salida incluye los dos módulos de IoT Edge utilizados en este ejemplo.

El script de compilación coloca **logger.dll** en la carpeta **build\\modules\\logger\\Debug** y **hello\_world.dll** en la carpeta **build\\modules\\hello_world\\Debug**. Utilice estas rutas de acceso para los valores de **ruta de acceso del módulo**, tal y como se muestra en el archivo de configuración JSON siguiente.

El proceso de hello\_world\_sample toma la ruta de acceso a un archivo de configuración JSON como argumento de línea de comandos. El siguiente archivo JSON de ejemplo se encuentra en el repositorio de SDK, en **samples\\hello\_world\\src\\hello\_world\_win.json**. Este archivo de configuración funcionará tal y como está, a menos que se modifique el script de compilación para colocar los módulos de IoT Edge o los ejecutables de ejemplo en ubicaciones que no sean las predeterminadas.

> [!NOTE]
> Las rutas de acceso del módulo son relativas al directorio en el que se encuentra hello\_world\_sample.exe. De manera predeterminada, el archivo de configuración JSON de ejemplo escribe "log.txt" en el directorio de trabajo actual.

```json
{
  "modules": [
    {
      "name": "logger",
      "loader": {
        "name": "native",
        "entrypoint": {
          "module.path": "..\\..\\..\\modules\\logger\\Debug\\logger.dll"
        }
      },
      "args": { "filename": "log.txt" }
    },
    {
      "name": "hello_world",
      "loader": {
        "name": "native",
        "entrypoint": {
          "module.path": "..\\..\\..\\modules\\hello_world\\Debug\\hello_world.dll"
        }
      },
      "args": null
      }
  ],
  "links": [
    {
      "source": "hello_world",
      "sink": "logger"
    }
  ]
}
```

1. Vaya a la carpeta **build** en la raíz de la copia local del repositorio **iot-edge**.

1. Ejecute el siguiente comando:

    ```cmd
    samples\hello_world\Debug\hello_world_sample.exe ..\samples\hello_world\src\hello_world_win.json
    ```

[!INCLUDE [iot-hub-iot-edge-getstarted-code](../../includes/iot-hub-iot-edge-getstarted-code.md)]

