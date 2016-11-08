---
title: Introducción al SDK de puerta de enlace del Centro de IoT | Microsoft Docs
description: Este tutorial del SDK de puerta de enlace de Azure IoT Hub usa Linux para ilustrar los conceptos clave que debe comprender cuando utilice este SDK.
services: iot-hub
documentationcenter: ''
author: chipalost
manager: timlt
editor: ''

ms.service: iot-hub
ms.devlang: cpp
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/25/2016
ms.author: andbuc

---
# SDK de puerta de enlace de IoT (beta): introducción usando Linux
[!INCLUDE [iot-hub-gateway-sdk-getstarted-selector](../../includes/iot-hub-gateway-sdk-getstarted-selector.md)]

## Compilación del ejemplo
Antes de comenzar, debe [configurar el entorno de desarrollo][lnk-setupdevbox] para trabajar con el SDK en Linux.

1. Abra un shell.
2. Vaya a la carpeta raíz en la copia local del repositorio **azure-iot-gateway-sdk**.
3. Ejecute el script **tools/build.sh**. Este script emplea la utilidad **cmake** para crear una carpeta llamada **build** en la carpeta raíz de la copia local del repositorio **azure-iot-gateway-sdk** y generar un archivo Make. A continuación, el script compila la solución y ejecuta las pruebas.

> [!NOTE]
> Cada vez que ejecute el script **build.sh**, elimina y luego vuelve a crear la carpeta **build** en la carpeta raíz de la copia local del repositorio **azure-iot-gateway-sdk**.
> 
> 

## Ejecución del ejemplo
1. El script **build.sh** genera su salida en la carpeta **build** de la copia local del repositorio **azure-iot-gateway-sdk**. Esto incluye los dos módulos utilizados en este ejemplo.
   
    El script de compilación coloca **liblogger\_hl.so** en la carpeta **build/modules/logger/** y **libhello\_world\_hl.so** en la carpeta **build/modules/hello\_world/**. Utilice estas rutas de acceso para el valor de **ruta de acceso del módulo**, tal como se muestra en el archivo de configuración JSON siguiente.
2. El archivo **hello\_world\_lin.json** de la carpeta **samples\\hello\_world\\src** es un ejemplo de archivo de configuración JSON para Linux que puede usar para ejecutar el ejemplo. En el ejemplo de configuración JSON que se muestra a continuación se da por hecho que ejecutará el ejemplo desde la raíz de la copia local del repositorio **azure-iot-gateway-sdk**.
3. Para el módulo **logger\_hl**, de la sección **args**, establezca el valor de **nombre de archivo** en el nombre y la ruta de acceso del archivo que contendrá los datos de registro.
   
    Este es un ejemplo de un archivo de configuración JSON para Linux que se escribirá en **log.txt** en la carpeta donde se ejecuta el ejemplo.
   
    ```
    {
      "modules" :
      [ 
        {
          "module name" : "logger_hl",
          "module path" : "./build/modules/logger/liblogger_hl.so",
          "args" : 
          {
            "filename":"./log.txt"
          }
        },
        {
          "module name" : "hello_world",
          "module path" : "./build/modules/hello_world/libhello_world_hl.so",
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
4. En el shell, vaya a la carpeta **azure-iot-gateway-sdk**.
5. Ejecute el siguiente comando:
   
   ```
   ./build/samples/hello_world/hello_world_sample ./samples/hello_world/src/hello_world_lin.json
   ``` 

[!INCLUDE [iot-hub-gateway-sdk-getstarted-code](../../includes/iot-hub-gateway-sdk-getstarted-code.md)]

<!-- Links -->
[lnk-setupdevbox]: https://github.com/Azure/azure-iot-gateway-sdk/blob/master/doc/devbox_setup.md

<!---HONumber=AcomDC_1005_2016-->