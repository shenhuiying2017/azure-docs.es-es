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
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/28/2017
ms.author: andbuc
ms.custom: H1Hack27Feb2017
ms.translationtype: Human Translation
ms.sourcegitcommit: e7da3c6d4cfad588e8cc6850143112989ff3e481
ms.openlocfilehash: c401052d65cb9f06398fcf6597dc1de5e7451734
ms.contentlocale: es-es
ms.lasthandoff: 05/16/2017


---
# <a name="explore-azure-iot-edge-architecture-on-linux"></a>Explorar la arquitectura de Azure IoT Edge en Linux

[!INCLUDE [iot-hub-gateway-sdk-getstarted-selector](../../includes/iot-hub-gateway-sdk-getstarted-selector.md)]

## <a name="how-to-build-the-sample"></a>Compilación del ejemplo

Antes de comenzar, debe [configurar el entorno de desarrollo][lnk-setupdevbox] para trabajar con el SDK en Linux.

1. Abra un shell.
1. Vaya a la carpeta raíz en la copia local del repositorio **iot-edge**.
1. Ejecute el script **tools/build.sh** . Este script emplea la utilidad **cmake** para crear una carpeta llamada **build** en la carpeta raíz de la copia local del repositorio **iot-edge** y generar un archivo Make. A continuación, el script compila la solución, omitiendo las pruebas unitarias y las pruebas completas. Agregue el parámetro **--run-unittests** si desea crear y ejecutar las pruebas unitarias. Agregue el parámetro **--run-e2e-tests** si desea crear y ejecutar las pruebas completas.

> [!NOTE]
> Cada vez que ejecute el script **build.sh**, elimina y luego vuelve a crear la carpeta **build** en la carpeta raíz de la copia local del repositorio **iot-edge**.

## <a name="how-to-run-the-sample"></a>Ejecución del ejemplo

1. El script **build.sh** genera su salida en la carpeta **build** de la copia local del repositorio **iot-edge**. Esta salida incluye los dos módulos de IoT Edge utilizados en este ejemplo.

    El script de compilación coloca **liblogger.so** en la carpeta **build/modules/logger/** y **lib\_helloworld.so** en la carpeta **build/modules/hello_world/**. Use estas rutas de acceso en el valor de **module path**, tal y como se muestra en el archivo de configuración JSON siguiente.
1. El proceso de hello\_world\_sample acepta la ruta de acceso a un archivo de configuración JSON como argumento de línea de comandos. El siguiente archivo JSON de ejemplo se encuentra en el repositorio de SDK, en **samples/hello\_world/src/hello\_world\_lin.json**. Este archivo de configuración funcionará tal y como está, a menos que se modifique el script de compilación para colocar los módulos de IoT Edge o los ejecutables de ejemplo en ubicaciones que no sean las predeterminadas.

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
1. Vaya a la carpeta **build**.
1. Ejecute el siguiente comando:

   `./samples/hello_world/hello_world_sample ./../samples/hello_world/src/hello_world_lin.json`

[!INCLUDE [iot-hub-gateway-sdk-getstarted-code](../../includes/iot-hub-gateway-sdk-getstarted-code.md)]

<!-- Links -->
[lnk-setupdevbox]: https://github.com/Azure/iot-edge/blob/master/doc/devbox_setup.md

