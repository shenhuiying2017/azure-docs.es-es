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
ms.date: 09/29/2017
ms.author: andbuc
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 262963250921ceb6f1a2fed0f68c36a9f052b47b
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="explore-azure-iot-edge-architecture-on-windows"></a>Explorar la arquitectura de Azure IoT Edge en Windows

[!INCLUDE [iot-hub-iot-edge-getstarted-selector](../../includes/iot-hub-iot-edge-getstarted-selector.md)]

[!INCLUDE [iot-hub-iot-edge-install-build-windows](../../includes/iot-hub-iot-edge-install-build-windows.md)]

## <a name="run-the-sample"></a>Ejecución del ejemplo

El script **build.cmd** genera su salida en la carpeta **build** de la copia local del repositorio **iot-edge**. Este resultado incluye muchos archivos, pero este ejemplo se centra en tres:
- Dos módulos de IoT Edge: **build\\modules\\logger\\Debug\\logger.dll** y **build\\modules\\hello_world\\Debug\\hello\_world.dll**. 
- Un archivo ejecutable: **build\\samples\\hello\_world\\Debug\\hello\_world\_sample.exe**. Este proceso toma un archivo de configuración JSON como argumento de línea de comandos.

El cuarto archivo que se utiliza en este ejemplo no se encuentra en la carpeta de compilación, pero se incluyó cuando se clonó el repositorio iot-edge:
- Un archivo de configuración de JSON: **samples\\hello\_world\\src\\hello\_world\_win.json**. Este archivo contiene las rutas de acceso a los dos módulos. También declara en qué logger.dll escribe su resultado. El valor predeterminado es **log.txt** en el directorio de trabajo actual. 

   >[!NOTE]
   >Si mueve los módulos de ejemplo o agregar los suyos propios para pruebas, actualice los valores de **module.path** en el archivo de configuración para que coincidan. Las rutas de acceso del módulo son relativas al directorio en el que se encuentra **hello\_world\_sample.exe**. 

Para ejecutar el ejemplo, siga estos pasos:

1. Vaya a la carpeta **build** en la raíz de la copia local del repositorio **iot-edge**.

1. Ejecute el siguiente comando:

   ```cmd
   samples\hello_world\Debug\hello_world_sample.exe ..\samples\hello_world\src\hello_world_win.json
   ```

1. El siguiente resultado indica que el ejemplo se ha ejecutado correctamente:

   ```cmd
   gateway successfully created from JSON
   gateway shall run until ENTER is pressed
   ```
  
1. Presione la tecla **ENTRAR** para detener el proceso.


[!INCLUDE [iot-hub-iot-edge-getstarted-code](../../includes/iot-hub-iot-edge-getstarted-code.md)]
