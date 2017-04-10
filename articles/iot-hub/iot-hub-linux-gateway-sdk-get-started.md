---
title: "Introducción al SDK de puerta de enlace de IoT de Azure (Linux) | Microsoft Docs"
description: "Cómo crear una puerta de enlace en un equipo Linux y obtener información sobre los conceptos claves en el SDK de puerta de enlace de IoT de Azure como módulos y archivos de configuración de JSON."
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
translationtype: Human Translation
ms.sourcegitcommit: 5cce99eff6ed75636399153a846654f56fb64a68
ms.openlocfilehash: 856ffeeeb8f9d8296ba972a9e070686171f7fde8
ms.lasthandoff: 03/31/2017


---
# <a name="explore-the-iot-gateway-sdk-architecture-on-linux"></a>Exploración de la arquitectura del SDK de puerta de enlace de IoT en Linux

[!INCLUDE [iot-hub-gateway-sdk-getstarted-selector](../../includes/iot-hub-gateway-sdk-getstarted-selector.md)]

## <a name="how-to-build-the-sample"></a>Compilación del ejemplo

Antes de comenzar, debe [configurar el entorno de desarrollo][lnk-setupdevbox] para trabajar con el SDK en Linux.

1. Abra un shell.
1. Vaya a la carpeta raíz en la copia local del repositorio **azure-iot-gateway-sdk** .
1. Ejecute el script **tools/build.sh** . Este script emplea la utilidad **cmake** para crear una carpeta llamada **build** en la carpeta raíz de la copia local del repositorio **azure-iot-gateway-sdk** y generar un archivo Make. A continuación, el script compila la solución, omitiendo las pruebas unitarias y las pruebas completas. Agregue el parámetro **--run-unittests** si desea crear y ejecutar las pruebas unitarias. Agregue el parámetro **--run-e2e-tests** si desea crear y ejecutar las pruebas completas.

> [!NOTE]
> Cada vez que ejecute el script **build.sh**, elimina y luego vuelve a crear la carpeta **build** en la carpeta raíz de la copia local del repositorio **azure-iot-gateway-sdk**.

## <a name="how-to-run-the-sample"></a>Ejecución del ejemplo

1. El script **build.sh** genera su salida en la carpeta **build** de la copia local del repositorio **azure-iot-gateway-sdk**. Esta salida incluye los dos módulos utilizados en este ejemplo.

    El script de compilación coloca **liblogger.so** en la carpeta **build/modules/logger/** y **lib\_helloworld.so** en la carpeta **build/modules/hello_world/**. Use estas rutas de acceso en el valor de **module path**, tal y como se muestra en el archivo de configuración JSON siguiente.
1. El proceso de hello\_world\_sample acepta la ruta de acceso a un archivo de configuración JSON como argumento de línea de comandos. El siguiente archivo JSON de ejemplo se encuentra en el repositorio de SDK, en **samples/hello\_world/src/hello\_world\_lin.json**. Este archivo de configuración funcionará tal y como está, a menos que se modifique el script de compilación para colocar los módulos o los ejecutables de ejemplo en ubicaciones que no sean las predeterminadas.

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
[lnk-setupdevbox]: https://github.com/Azure/azure-iot-gateway-sdk/blob/master/doc/devbox_setup.md

