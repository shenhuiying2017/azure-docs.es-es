---
title: "Introducción al SDK de puerta de enlace del IoT Hub | Microsoft Docs"
description: Tutorial del SDK de puerta de enlace de IoT de Azure mediante Windows para ilustrar los conceptos clave que debe comprender cuando utilice este SDK.
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
ms.date: 11/16/2016
ms.author: andbuc
translationtype: Human Translation
ms.sourcegitcommit: 2d8b98fbd5345edd5dc6891df12f05eccd8e7ca8
ms.openlocfilehash: 6f2fe4fd3442d97955519348416b35fe6f9075d1


---
# <a name="azure-iot-gateway-sdk---get-started-using-windows"></a>SDK de puerta de enlace de IoT de Azure: uso con Windows
[!INCLUDE [iot-hub-gateway-sdk-getstarted-selector](../../includes/iot-hub-gateway-sdk-getstarted-selector.md)]

## <a name="how-to-build-the-sample"></a>Compilación del ejemplo
Antes de comenzar, debe [configurar el entorno de desarrollo][lnk-setupdevbox] para poder trabajar con el SDK de Windows.

1. Abra un **símbolo del sistema para desarrolladores para VS2015** .
2. Vaya a la carpeta raíz en la copia local del repositorio **azure-iot-gateway-sdk** .
3. Ejecute el script **tools\\build.cmd**. Este script crea un archivo de solución de Visual Studio, genera la solución y ejecuta las pruebas. Puede encontrar la solución de Visual Studio en la carpeta **build** de su copia local del repositorio **azure-iot-gateway-sdk**.

## <a name="how-to-run-the-sample"></a>Ejecución del ejemplo
1. El script **build.cmd** crea una carpeta llamada **build** en la copia local del repositorio. Esta carpeta contiene los dos módulos utilizados en este ejemplo.
   
    El script de compilación sitúa **logger.dll** en la carpeta **build\\modules\\logger\\Debug** y **hello_world.dll** en la carpeta **build\\modules\\hello_world\\Debug**. Utilice estas rutas de acceso en el valor de **ruta de acceso del módulo**, tal y como se muestra en el archivo de configuración JSON siguiente.
2. El proceso de hello_world_sample acepta la ruta de acceso de un archivo de configuración JSON como argumento en la línea de comandos. El siguiente archivo JSON de ejemplo está incluido en el repositorio, en **azure-iot-gateway-sdk\samples\hello_world\src\hello_world_win.json**. Este archivo funcionará tal y como está, a menos que haya modificado el script de compilación para situar los módulos o los ejecutables de ejemplo en ubicaciones que no sean las predeterminadas. 

   > [!NOTE]
   > Las rutas de acceso del módulo hacen referencia al directorio en el que se encuentra hello_world_sample.exe. De manera predeterminada, el archivo de configuración JSON de ejemplo escribe "log.txt" en el directorio de trabajo actual.
   
    ```
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
3. Vaya a la carpeta raíz de la copia local del repositorio **azure-iot-gateway-sdk**.

4. Ejecute el siguiente comando:
   
   ```
   build\samples\hello_world\Debug\hello_world_sample.exe samples\hello_world\src\hello_world_win.json
   ```

[!INCLUDE [iot-hub-gateway-sdk-getstarted-code](../../includes/iot-hub-gateway-sdk-getstarted-code.md)]

<!-- Links -->
[lnk-setupdevbox]: https://github.com/Azure/azure-iot-gateway-sdk/blob/master/doc/devbox_setup.md



<!--HONumber=Nov16_HO5-->


