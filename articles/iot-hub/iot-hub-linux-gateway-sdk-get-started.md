---
title: "Introducción al SDK de puerta de enlace del IoT Hub | Microsoft Docs"
description: Este tutorial del SDK de puerta de enlace de Azure IoT usa Linux para ilustrar los conceptos clave que debe comprender cuando utilice este SDK.
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
ms.date: 08/25/2016
ms.author: andbuc
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 23176a9251a90a985a5d2fbce23ceeb9d0925234


---
# <a name="azure-iot-gateway-sdk-beta-get-started-using-linux"></a>SDK de puerta de enlace de Azure IoT (beta): introducción usando Linux
[!INCLUDE [iot-hub-gateway-sdk-getstarted-selector](../../includes/iot-hub-gateway-sdk-getstarted-selector.md)]

## <a name="how-to-build-the-sample"></a>Compilación del ejemplo
Antes de comenzar, tiene que [configurar el entorno de desarrollo][lnk-setupdevbox] para trabajar con el SDK en Linux.

1. Abra un shell.
2. Vaya a la carpeta raíz en la copia local del repositorio **azure-iot-gateway-sdk** .
3. Ejecute el script **tools/build.sh** . Este script emplea la utilidad **cmake** para crear una carpeta llamada **build** en la carpeta raíz de la copia local del repositorio **azure-iot-gateway-sdk** y generar un archivo Make. A continuación, el script compila la solución y ejecuta las pruebas.

> [!NOTE]
> Cada vez que ejecute el script **build.sh**, elimina y luego vuelve a crear la carpeta **build** en la carpeta raíz de la copia local del repositorio **azure-iot-gateway-sdk**.
> 
> 

## <a name="how-to-run-the-sample"></a>Ejecución del ejemplo
1. El script **build.sh** genera su salida en la carpeta **build** de la copia local del repositorio **azure-iot-gateway-sdk**. Esto incluye los dos módulos utilizados en este ejemplo.
   
    El script de compilación coloca **liblogger_hl.so** en la carpeta **build/modules/logger/** y **libhello_world_hl.so** en la carpeta **build/modules/hello_world/**. Utilice estas rutas de acceso para el valor de **ruta de acceso del módulo** , tal como se muestra en el archivo de configuración JSON siguiente.
2. El archivo **hello_world_lin.json** de la carpeta **samples\hello_world\src** es un archivo de configuración JSON de ejemplo para Linux que puede usar para ejecutar el ejemplo. En el ejemplo de configuración JSON que se muestra a continuación se da por hecho que ejecutará el ejemplo desde la raíz de la copia local del repositorio **azure-iot-gateway-sdk** .
3. Para el módulo **logger_hl** de la sección **args**, establezca el valor de **nombre de archivo** en el nombre y la ruta de acceso del archivo que contendrá los datos de registro.
   
    Este es un ejemplo de un archivo de configuración JSON para Linux que se escribirá en **log.txt** en la carpeta donde se ejecuta el ejemplo.
   
    ```
    {
      "modules" :
      [ 
        {
          "module name" : "logger_hl",
          "loading args": {
            "module path" : "./build/modules/logger/liblogger_hl.so"
          },
          "args" : 
          {
            "filename":"./log.txt"
          }
        },
        {
          "module name" : "hello_world",
          "loading args": {
            "module path" : "./build/modules/hello_world/libhello_world_hl.so"
          },
          "args" : null
        }
      ],
      "links" :
      [
        {
          "source": "hello_world",
          "sink": "logger_hl"
        }
      ]
    }
    ```
4. En el shell, vaya a la carpeta **azure-iot-gateway-sdk** .
5. Ejecute el siguiente comando:
   
   ```
   ./build/samples/hello_world/hello_world_sample ./samples/hello_world/src/hello_world_lin.json
   ``` 

[!INCLUDE [iot-hub-gateway-sdk-getstarted-code](../../includes/iot-hub-gateway-sdk-getstarted-code.md)]

<!-- Links -->
[lnk setupdevbox]: https://github.com/Azure/azure-iot-gateway-sdk/blob/master/doc/devbox_setup.md



<!--HONumber=Nov16_HO2-->


