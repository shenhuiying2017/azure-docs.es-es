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
ms.date: 11/23/2016
ms.author: andbuc
ms.custom: H1Hack27Feb2017
translationtype: Human Translation
ms.sourcegitcommit: 37b2a82d7f6043224e68219fde753eef73078ffd
ms.openlocfilehash: b3cc8e53b0c8bb7ea40b6ebcebe1f97d4a3e1180
ms.lasthandoff: 03/02/2017


---
# <a name="explore-the-iot-gateway-sdk-architecture-on-linux"></a>Exploración de la arquitectura del SDK de puerta de enlace de IoT en Linux
[!INCLUDE [iot-hub-gateway-sdk-getstarted-selector](../../includes/iot-hub-gateway-sdk-getstarted-selector.md)]

## <a name="how-to-build-the-sample"></a>Compilación del ejemplo
Antes de comenzar, debe [configurar el entorno de desarrollo][lnk-setupdevbox] para trabajar con el SDK en Linux.

1. Abra un shell.
2. Vaya a la carpeta raíz en la copia local del repositorio **azure-iot-gateway-sdk** .
3. Ejecute el script **tools/build.sh** . Este script emplea la utilidad **cmake** para crear una carpeta llamada **build** en la carpeta raíz de la copia local del repositorio **azure-iot-gateway-sdk** y generar un archivo Make. A continuación, el script compila la solución, omitiendo las pruebas unitarias y las pruebas completas. Agregue el parámetro **--run-unittests** si desea crear y ejecutar las pruebas unitarias. Agregue el parámetro **--run-e2e-tests** si desea crear y ejecutar las pruebas completas.

> [!NOTE]
> Cada vez que ejecute el script **build.sh**, elimina y luego vuelve a crear la carpeta **build** en la carpeta raíz de la copia local del repositorio **azure-iot-gateway-sdk**.
> 
> 

## <a name="how-to-run-the-sample"></a>Ejecución del ejemplo
1. El script **build.sh** genera su salida en la carpeta **build** de la copia local del repositorio **azure-iot-gateway-sdk**. Esto incluye los dos módulos utilizados en este ejemplo.
   
    El script de compilación coloca **liblogger.so** en la carpeta **build/modules/logger/** y **libhello_world.so** en la carpeta **build/modules/hello_world/**. Utilice estas rutas de acceso para el valor de **ruta de acceso del módulo** , tal como se muestra en el archivo de configuración JSON siguiente.
2. El proceso de hello_world_sample acepta la ruta de acceso de un archivo de configuración JSON como argumento en la línea de comandos. Se ha proporcionado un archivo JSON de ejemplo como parte del repositorio en **azure-iot-gateway-sdk/samples/hello_world/src/hello_world_win.json** y está copiado a continuación. Funcionará tal cual, a menos que haya modificado el script de compilación para colocar los módulos o los archivos ejecutables de ejemplo en ubicaciones diferentes de las predeterminadas.

   > [!NOTE]
   > Las rutas de acceso del módulo son relativas al directorio de trabajo actual desde donde se inicia el archivo ejecutable hello_world_sample, no al directorio donde se encuentra el archivo ejecutable. De manera predeterminada, el archivo de configuración JSON de ejemplo escribe "log.txt" en el directorio de trabajo actual.
   
    ```
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
3. Vaya a la carpeta **azure-iot-gateway-sdk/build**.
4. Ejecute el siguiente comando:
   
   ```
   ./samples/hello_world/hello_world_sample ./../samples/hello_world/src/hello_world_lin.json
   ``` 

[!INCLUDE [iot-hub-gateway-sdk-getstarted-code](../../includes/iot-hub-gateway-sdk-getstarted-code.md)]

<!-- Links -->
[lnk-setupdevbox]: https://github.com/Azure/azure-iot-gateway-sdk/blob/master/doc/devbox_setup.md

