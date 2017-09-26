---
title: "Introducción a Azure IoT Edge (Linux) | Microsoft Docs"
description: "Cómo crear una puerta de enlace en un equipo Linux y obtener información sobre los conceptos claves de Azure IoT Edge, como los módulos y los archivos de configuración de JSON."
services: iot-hub
documentationcenter: 
author: chipalost
manager: timlt
editor: 
ms.assetid: cf537bdd-2352-4bb1-96cd-a283fcd3d6cf
ms.service: iot-hub
ms.devlang: cpp
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/19/2017
ms.author: andbuc
ms.custom: H1Hack27Feb2017
ms.translationtype: HT
ms.sourcegitcommit: 4f77c7a615aaf5f87c0b260321f45a4e7129f339
ms.openlocfilehash: e2f26c1abe09feff77e1e2633d8bfcf4ca527aee
ms.contentlocale: es-es
ms.lasthandoff: 09/22/2017

---
# <a name="explore-azure-iot-edge-architecture-on-linux"></a>Explorar la arquitectura de Azure IoT Edge en Linux

[!INCLUDE [iot-hub-iot-edge-getstarted-selector](../../includes/iot-hub-iot-edge-getstarted-selector.md)]

[!INCLUDE [iot-hub-iot-edge-install-build-linux](../../includes/iot-hub-iot-edge-install-build-linux.md)]

## <a name="how-to-run-the-sample"></a>Ejecución del ejemplo

El script **build.sh** genera su salida en la carpeta **build** de la copia local del repositorio **iot-edge**. Esta salida incluye los dos módulos de IoT Edge utilizados en este ejemplo.

El script de compilación coloca **liblogger.so** en la carpeta **build/modules/logger/** y **lib\_helloworld.so** en la carpeta **build/modules/hello_world/**. Use estas rutas de acceso en los valores de **module path**, tal y como se muestra en el archivo de configuración JSON siguiente.

El proceso de hello\_world\_sample toma la ruta de acceso a un archivo de configuración JSON como argumento de línea de comandos. El siguiente archivo JSON de ejemplo se encuentra en el repositorio de SDK, en **samples/hello\_world/src/hello\_world\_lin.json**. Este archivo de configuración funciona tal y como está, a menos que se modifique el script de compilación para colocar los módulos de IoT Edge o los ejecutables de ejemplo en ubicaciones diferentes de las predeterminadas.

> [!NOTE]
> Las rutas de acceso del módulo son relativas al directorio de trabajo actual desde donde se inicia el archivo ejecutable hello\_world\_sample, no el directorio donde se encuentra el archivo ejecutable. De manera predeterminada, el archivo de configuración JSON de ejemplo escribe "log.txt" en el directorio de trabajo actual.

```json
{
    "modules" :
    [
        {
            "name" : "logger",
            "loader": {
            "name": "native",
            "entrypoint": {
                "module.path": "./modules/logger/liblogger.so"
            }
            },
            "args" : {"filename":"log.txt"}
        },
        {
            "name" : "hello_world",
            "loader": {
            "name": "native",
            "entrypoint": {
                "module.path": "./modules/hello_world/libhello_world.so"
            }
            },
            "args" : null
        }
    ],
    "links":
    [
        {
            "source": "hello_world",
            "sink": "logger"
        }
    ]
}
```

1. Vaya a la carpeta **build** en la raíz de la copia local del repositorio **iot-edge**.

1. Ejecute el siguiente comando:

    ```sh
    ./samples/hello_world/hello_world_sample ../samples/hello_world/src/hello_world_lin.json`
    ```

[!INCLUDE [iot-hub-iot-edge-getstarted-code](../../includes/iot-hub-iot-edge-getstarted-code.md)]


