---
title: "Creación del primer módulo de puerta de enlace de IoT de Azure | Microsoft Docs"
description: "Cree un módulo y agréguelo a una aplicación de ejemplo para personalizar los comportamientos del módulo."
services: iot-hub
documentationcenter: 
author: shizn
manager: timtl
tags: 
keywords: 
ms.assetid: cd7660f4-7b8b-4091-8d71-bb8723165b0b
ms.service: iot-hub
ms.devlang: 
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 2/17/2017
ms.author: xshi
translationtype: Human Translation
ms.sourcegitcommit: 6b12d39ce8c6062df5fb78de654bf22183b6ecd9
ms.openlocfilehash: 42c22192bc3cccdea97f7d8dac575fa7cbc9a6c6
ms.lasthandoff: 02/21/2017


---
# <a name="lesson-5-create-your-first-azure-iot-gateway-module"></a>Lección 5: Creación del primer módulo de puerta de enlace de IoT de Azure

## <a name="what-you-will-do"></a>Lo que hará

- Compile y ejecute la aplicación de ejemplo hello_world en Intel NUC.
- Cree un módulo y compílelo en Intel NUC.
- Agregue el nuevo módulo a la aplicación de ejemplo hello_world y, después, ejecute el ejemplo en Intel NUC. El nuevo módulo imprime mensajes de "hello_world" con una marca de tiempo.

## <a name="what-you-will-learn"></a>Lo qué aprenderá

- Cómo compilar y ejecutar la aplicación de ejemplo hello_world en Intel NUC.
- Cómo crear un módulo.
- Cómo agregar un módulo a una aplicación de ejemplo.

## <a name="what-you-need"></a>Lo que necesita

El SDK de puerta de enlace de IoT de Azure que se ha instalado en el equipo host.

## <a name="folder-structure"></a>Estructura de carpetas

En la subcarpeta de la lección 5 del código de ejemplo que se clonó en la lección 1, hay una carpeta `module` y una carpeta `sample`.

![my_module](media/iot-hub-gateway-kit-lessons/lesson5/my_module.png)

- La carpeta `module/my_module` contiene el código fuente y el script para crear el módulo.
- La carpeta `sample` contiene el código fuente y el script para crear la aplicación de ejemplo.

## <a name="compile-and-run-the-helloworld-sample-app-on-intel-nuc"></a>Compilación y ejecución de la aplicación de ejemplo hello_world en Intel NUC

El ejemplo `hello_world` crea una puerta de enlace basada en el archivo `hello_world.json` que especifica los dos módulos predefinidos asociados a la aplicación. La puerta de enlace registra un mensaje "hello world" en un archivo cada 5 segundos. En esta sección, se compila y ejecuta la aplicación `hello_world` con su módulo predeterminado.

Para compilar y ejecutar la aplicación `hello_world`, siga estos pasos en el equipo host:

1. Inicialice el archivo de configuración mediante la ejecución de los siguientes comandos:

   ```bash
   cd iot-hub-c-intel-nuc-gateway-getting-started
   cd Lesson5
   npm install
   gulp init
   ```

1. Actualice el archivo de configuración de puerta de enlace con la dirección MAC de Intel NUC. Omita este paso si ha leído la lección [Configuración y ejecución de la aplicación de ejemplo BLE][config_ble].

   1. Genere el archivo de configuración de puerta de enlace mediante la ejecución del comando siguiente:

      ```bash
      # For Windows command prompt
      code %USERPROFILE%\.iot-hub-getting-started\config-gateway.json

      # For MacOS or Ubuntu
      code ~/.iot-hub-getting-started/config-gateway.json
      ```

   1. Actualice las direcciones MAC de la puerta de enlace cuando [configure Intel NUC como una puerta de enlace de IoT][setup_nuc] y, después, guarde el archivo.

1. Compile el código fuente de ejemplo mediante la ejecución del comando siguiente:

   ```bash
   gulp compile
   ```

   El comando transfiere el código fuente de ejemplo a Intel NUC y ejecuta `build.sh` para compilarlo.

1. Ejecute la aplicación `hello_world` en Intel NUC mediante el comando siguiente:

   ```bash
   gulp run
   ```

   El comando ejecuta `../Tools/run-hello-world.js`, que en `config.json` está especificado para iniciar la aplicación de ejemplo en Intel NUC.

   ![run_sample](media/iot-hub-gateway-kit-lessons/lesson5/run_sample.png)

## <a name="create-a-new-module-and-compile-it-on-intel-nuc"></a>Creación de un módulo y compilarlo en Intel NUC

Los pasos siguientes lo guían para crear un módulo y compilarlo en Intel NUC. El módulo imprime los mensajes con una marca de tiempo tras su recepción. En esta sección va a crear el primer módulo de puerta de enlace personalizado.

Cualquier módulo del SDK de puerta de enlace de IoT de Azure debe implementar las siguientes interfaces:

   ```C
   pfModule_ParseConfigurationFromJson Module_ParseConfigurationFromJson
   pfModule_FreeConfiguration Module_FreeConfiguration
   pfModule_Create Module_Create
   pfModule_Destroy Module_Destroy
   pfModule_Receive Module_Receive
   ```

También puede implementar la interfaz siguiente:

   ```C
   pfModule_Start Module_Start
   ```

El diagrama siguiente muestra las rutas de acceso de estado importantes de un módulo. Los rectángulos cuadrados representan métodos que se implementan para llevar a cabo operaciones cuando el módulo se mueve entre estados. Las formas ovaladas representan los estados más importantes que un módulo puede tener.

![state_path](media/iot-hub-gateway-kit-lessons/lesson5/state_path.png)

Ahora se va a crear un módulo basado en la plantilla:

1. Abra la carpeta de la plantilla mediante la ejecución del comando siguiente:

   ```bash
   code module/my_module
   ```

   ![code_module](media/iot-hub-gateway-kit-lessons/lesson5/code_module.png)

   - `src/my_module.c` actúa como una plantilla que facilita la creación de un módulo. La plantilla declara las interfaces. Lo único que necesita es agregar lógica a la función `MyModule_Receive`.
   - `build.sh` es el script para compilar el módulo en Intel NUC.
1. Abra el archivo `src/my_module.c` e incluya dos archivos de encabezado:

   ```C
   #include <stdio.h>
   #include "azure_c_shared_utility/xlogging.h"
   ```

1. Agregue el siguiente código a la función `MyModule_Receive`:

   ```C
   if (message == NULL)
   {
      LogError("invalid arg message");
   }
   else
   {
      // get the message content
      const CONSTBUFFER * content = Message_GetContent(message);
      // get the local time and format it
      time_t temp = time(NULL);
      if (temp == (time_t)-1)
      {
          LogError("time function failed");
      }
      else
      {
          struct tm* t = localtime(&temp);
          if (t == NULL)
          {
              LogError("localtime failed");
          }
          else
          {
              char timetemp[80] = { 0 };
              if (strftime(timetemp, sizeof(timetemp) / sizeof(timetemp[0]), "%c", t) == 0)
              {
                  LogError("unable to strftime");
              }
              else
              {
                  printf("[%s] %.*s\r\n", timetemp, (int)content->size, content->buffer);
              }
          }
      }
   }
   ```

1. Actualice el archivo `config.json` para especificar la carpeta `workspace` en el equipo host y la ruta de acceso de la implementación en Intel NUC. Durante la compilación, los archivos de la carpeta `workspace` se transferirán a la ruta de acceso de la implementación.

   1. Abra el archivo `config.json` mediante la ejecución del comando siguiente:

      ```bash
      code config.json
      ```

   1. Actualice `config.json` con la configuración siguiente:

      ```json
      "workspace": "./module/my_module",
      "deploy_path": "module/my_module"
      ```

      ![config_json](media/iot-hub-gateway-kit-lessons/lesson5/config_json.png)

1. Compile el módulo mediante la ejecución del comando siguiente:

   ```bash
   gulp compile
   ```

   El comando transfiere el código fuente a Intel NUC y ejecuta `build.sh` para compilar el módulo.

## <a name="add-the-module-to-the-helloworld-sample-app-and-run-the-app-on-intel-nuc"></a>Agregar el módulo a la aplicación de ejemplo hello_world y ejecutar la aplicación en Intel NUC

Para realizar esta tarea, siga estos pasos:

1. Enumere todos los archivos binarios disponibles en el módulo (archivos .so) en Intel NUC mediante la ejecución del comando siguiente:

   ```bash
   gulp modules --list
   ```

   La ruta de acceso binaria de `my_module` que ha compilado debe aparecer como sigue:

   ```path
   /root/gateway_sample/module/my_module/build/libmy_module.so
   ```

   Si cambia el nombre de usuario de inicio de sesión predeterminado en `config-gateway.json`, la ruta de acceso binaria se iniciará con `home/<your username>` en lugar de con `root`.

1. Agregue `my_module` a la aplicación de ejemplo `hello_world`:

   1. Abra el archivo `hello_world.json` mediante la ejecución del comando siguiente:

      ```bash
      code sample/hello_world/src/hello_world.json
      ```

   1. Agregue el siguiente código a la sección `modules`:

      ```json
      {
       "name": "my_module",
       "loader": {
       "name": "native",
       "entrypoint": {
       "module.path": "/root/gateway_sample/module/my_module/build/libmy_module.so"
         }
        },
       "args": null
      }
      ```

      El valor de `module.path` debe ser `/root/gateway_sample/module/my_module/build/libmy_module.so`. El código declara que `my_module` se asocie con la puerta de enlace y la ubicación del archivo binario del módulo especificado en `module.path`.
   1. Agregue el siguiente código a la sección `links`:

      ```json
      {
        "source": "hello_world",
        "sink": "my_module"
      }
      ```

      Este código especifica que los mensajes se transfieran del módulo `hello_world` a `my_module`.

      ![hello_world_json](media/iot-hub-gateway-kit-lessons/lesson5/hello_world_json.png)

1. Ejecute la aplicación de ejemplo `hello_world` mediante la ejecución del comando siguiente:

   ```bash
   gulp run --config sample/hello_world/src/hello_world.json
   ```

   El parámetro `--config` fuerza la ejecución del script `run-hello-world.js` con la utilización del archivo `hello_world.json`.

   ![hello_world_new](media/iot-hub-gateway-kit-lessons/lesson5/hello_world_new.png)

¡Enhorabuena! Ahora puede ver el comportamiento de este nuevo módulo; simplemente imprime mensajes de "hello world" con una marca de tiempo, un resultado diferente del módulo original de "hello_world".

## <a name="next-steps"></a>Pasos siguientes

Ha creado un módulo, lo ha agregado al ejemplo hello_world y ha hecho que la aplicación de ejemplo se ejecute con el nuevo módulo en la puerta de enlace. Si desea obtener más información sobre los módulos de puerta de enlace de IoT de Azure, puede encontrar más ejemplos de módulo aquí: [https://github.com/Azure/azure-iot-gateway-sdk/tree/master/modules](https://github.com/Azure/azure-iot-gateway-sdk/tree/master/modules).

<!-- Images and links -->

[config_ble]: iot-hub-gateway-kit-c-lesson3-configure-ble-app.md
[setup_nuc]: iot-hub-gateway-kit-c-lesson1-set-up-nuc.md

