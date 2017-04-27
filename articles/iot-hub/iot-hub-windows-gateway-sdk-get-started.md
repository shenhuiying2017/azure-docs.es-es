---
title: "Introducción al SDK de puerta de enlace de IoT de Azure (Windows) | Microsoft Docs"
description: "Cómo crear una puerta de enlace en un equipo Windows y obtener información sobre los conceptos claves en el SDK de puerta de enlace de IoT de Azure como módulos y archivos de configuración de JSON."
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
ms.date: 03/28/2017
ms.author: andbuc
ms.custom: H1Hack27Feb2017
translationtype: Human Translation
ms.sourcegitcommit: 5cce99eff6ed75636399153a846654f56fb64a68
ms.openlocfilehash: f3cc32daac5059e816c885c88f4a7d36b6fc897e
ms.lasthandoff: 03/31/2017


---
# <a name="explore-the-iot-gateway-sdk-architecture-on-windows"></a>Exploración de la arquitectura del SDK de puerta de enlace de IoT en Windows

[!INCLUDE [iot-hub-gateway-sdk-getstarted-selector](../../includes/iot-hub-gateway-sdk-getstarted-selector.md)]

## <a name="how-to-build-the-sample"></a>Compilación del ejemplo

Antes de comenzar, debe [configurar el entorno de desarrollo][lnk-setupdevbox] para poder trabajar con el SDK de Windows.

1. Abra un **símbolo del sistema para desarrolladores de VS2015** o un **símbolo del sistema para desarrolladores de VS2017**.
1. Vaya a la carpeta raíz en la copia local del repositorio **azure-iot-gateway-sdk** .
1. Ejecute el script **tools\\build.cmd**. Este script crea un archivo de solución de Visual Studio y compila la solución. Puede encontrar la solución de Visual Studio en la carpeta **build** de su copia local del repositorio **azure-iot-gateway-sdk**. Se pueden pasar parámetros adicionales al script para compilar y ejecutar pruebas unitarias de un extremo a otro. Estos parámetros son **--run-unittests** y **--run-e2e-tests**, respectivamente.

## <a name="how-to-run-the-sample"></a>Ejecución del ejemplo

1. El script **build.cmd** crea una carpeta llamada **build** en la copia local del repositorio. Esta carpeta contiene los dos módulos utilizados en este ejemplo.

    El script de compilación coloca **logger.dll** en la carpeta **build\\modules\\logger\\Debug** y **hello\_world.dll** en la carpeta **build\\modules\\hello_world\\Debug**. Utilice estas rutas de acceso para los valores de **ruta de acceso del módulo**, tal y como se muestra en el archivo de configuración JSON siguiente.
1. El proceso de hello\_world\_sample toma la ruta de acceso a un archivo de configuración JSON como argumento de línea de comandos. El siguiente archivo JSON de ejemplo se encuentra en el repositorio de SDK, en **samples\\hello\_world\\src\\hello\_world\_win.json**. Este archivo de configuración funcionará tal y como está, a menos que se modifique el script de compilación para colocar los módulos o los ejecutables de ejemplo en ubicaciones que no sean las predeterminadas.

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

1. Vaya a la carpeta raíz de la copia local del repositorio **azure-iot-gateway-sdk**.

1. Ejecute el siguiente comando:

   `build\samples\hello_world\Debug\hello_world_sample.exe samples\hello_world\src\hello_world_win.json`

[!INCLUDE [iot-hub-gateway-sdk-getstarted-code](../../includes/iot-hub-gateway-sdk-getstarted-code.md)]

<!-- Links -->
[lnk-setupdevbox]: https://github.com/Azure/azure-iot-gateway-sdk/blob/master/doc/devbox_setup.md

