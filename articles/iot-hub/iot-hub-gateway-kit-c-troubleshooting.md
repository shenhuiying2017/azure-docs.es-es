---
title: "Dispositivo SensorTag y puerta de enlace de Azure IoT: Solución de problemas | Microsoft Docs"
description: "Página de solución de problemas de la puerta de enlace de Intel NUC"
services: iot-hub
documentationcenter: 
author: shizn
manager: timlt
tags: 
keywords: problemas con iot, problemas con internet de las cosas
ROBOTS: NOINDEX
ms.assetid: 5f742c38-0e33-465a-9a0d-1e41e8d17187
ms.service: iot-hub
ms.devlang: c
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 3/21/2017
ms.author: xshi
ms.openlocfilehash: 7e80951de55ade6b5140608dcff8ebb064f942ca
ms.sourcegitcommit: 02e69c4a9d17645633357fe3d46677c2ff22c85a
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 08/03/2017
---
# <a name="troubleshooting"></a>Solución de problemas

## <a name="hardware-issues"></a>Problemas de hardware

### <a name="ti-sensortag-cannot-be-connected"></a>No se puede conectar SensorTag de TI

Para solucionar los problemas de conectividad de SensorTag, utilice la [aplicación SensorTag](http://processors.wiki.ti.com/index.php/SensorTag_User_Guide#SensorTag_App_user_guide).

### <a name="have-an-issue-with-intel-nuc"></a>Tiene un problema con Intel NUC

Para solucionar problemas de arranque, vea [Troubleshooting No Boot Issues on Intel NUC](http://www.intel.com/content/www/us/en/support/boards-and-kits/000005845.html) (Solución de problemas de no arranque en Intel NUC).

Para solucionar problemas del sistema operativo, vea [Troubleshooting Operating System Issues on Intel NUC](http://www.intel.com/content/www/us/en/support/boards-and-kits/000006018.html) (Solución de problemas del sistema operativo en Intel NUC).

Para solucionar otros problemas, vea [Blink Codes and Beep Codes for Intel NUC](http://www.intel.com/content/www/us/en/support/boards-and-kits/intel-nuc-boards/000005854.html) (Códigos intermitentes y sonoros para Intel NUC).

## <a name="nodejs-package-issues"></a>Problemas de paquetes en Node.js

### <a name="no-response-during-gulp-tasks"></a>Sin respuesta durante las tareas de Gulp

Si tiene problemas para ejecutar tareas de Gulp, puede agregar la opción `--verbose` para depurar el código. Trate de finalizar las tareas actuales de Gulp con `Ctrl + C` y ejecute el comando siguiente en la ventana de consola para ver los mensajes de depuración. En la salida de la consola, deberían aparecer mensajes de error detallados.

```bash
gulp --verbose
```

### <a name="device-discovery-issues"></a>Problemas de detección de dispositivos

Si necesita ayuda para solucionar problemas habituales con el comando `discover-sensortag`, vea la [página de la wiki](https://wiki.archlinux.org/index.php/bluetooth#Bluetoothctl).

### <a name="npm-issues"></a>problemas de npm

Intente actualizar el paquete de NPM mediante la ejecución del siguiente comando:

```bash
npm install -g npm
```

Si el problema persiste, deje sus comentarios al final de este artículo o cree un problema de GitHub en el [repositorio de ejemplos](https://github.com/azure-samples/iot-hub-c-intel-nuc-gateway-getting-started).

## <a name="remote-debugging"></a>Depuración remota
> Las siguientes instrucciones están pensadas para depurar los scripts de Node.js utilizados en este tutorial.
### <a name="run-the-sample-application-in-debug-mode"></a>Ejecución de la aplicación de ejemplo en el modo de depuración

Ejecute la aplicación de ejemplo en el modo de depuración mediante la ejecución del siguiente comando:

```bash
gulp run --debug
```

Cuando el motor de depuración esté preparado, debería ver `Debugger listening on port 5858` en la salida de la consola.

### <a name="configure-visual-studio-code-to-connect-to-the-remote-device"></a>Configuración de Visual Studio Code para establecer conexión con el dispositivo remoto

1. Abra el panel **Depurar** de la izquierda.
2. Haga clic en el botón **Iniciar depuración** de color verde (F5). Visual Studio Code abre un archivo `launch.json`.
3. Actualice el archivo `launch.json` con el siguiente contenido. Reemplace `[device hostname or IP address]` por la dirección IP o el nombre de host reales del dispositivo.

   ``` json
   {
     "version": "0.2.0",
     "configurations": [
        {
            "name": "Attach",
            "type": "node",
            "request": "attach",
            "port": 5858,
            "address": "[device hostname or IP address]",
            "restart": false,
            "sourceMaps": false,
            "outDir": null,
            "localRoot": "${workspaceRoot}",
            "remoteRoot": "~/ble_sample"
        }
     ]
   }
   ```

![Configuración de depuración remota](./media/iot-hub-gateway-kit-lessons/troubleshooting/remote_debugging_configuration.png)

### <a name="attach-to-the-remote-application"></a>Asociación a la aplicación remota

Haga clic en el color verde **Iniciar depuración** botón (F5) para iniciar la depuración.

Lea el documento de [JavaScript de VS Code](https://code.visualstudio.com/docs/languages/javascript#_debugging) para obtener más información sobre el depurador.

![BLE de depuración de ejemplo](./media/iot-hub-gateway-kit-lessons/troubleshooting/debugging_ble_sample.png)

## <a name="azure-cli-issues"></a>Problemas de la CLI de Azure

La interfaz de la línea de comandos de Azure (CLI de Azure ) es una versión preliminar. Puede consultar la [guía de instalación de la versión preliminar](https://github.com/Azure/azure-cli/blob/master/doc/preview_install_guide.md) para buscar soluciones.

Si detecta los errores con la herramienta, envíe un [problema](https://github.com/Azure/azure-cli/issues) en la sección de **problemas** del repositorio de GitHub.

Si necesita ayuda para solucionar problemas comunes, consulte el archivo [readme](https://github.com/Azure/azure-cli/blob/master/README.rst).

Si se encuentra un error que indica que no se ha podido encontrar una versión que satisfaga los requisitos, ejecute el siguiente comando para actualizar pip a la versión más reciente.

```bash
python -m pip install --upgrade pip
```

## <a name="python-installation-issues"></a>Problemas de instalación de Python

### <a name="legacy-installation-issues-macos"></a>Problemas de instalación de versiones antiguas (Mac OS)

Al instalar pip, se produce un error de permisos si hay paquetes antiguos instalados con permisos **su**. Esto se debe a que hay una instalación anterior de Python con Brew (macOS) que no se ha desinstalado por completo. Algunos paquetes de pip se crearon en la raíz en una versión anterior, lo que produce este error de permisos. La solución es quitar dichos paquetes instalados de raíz. Realice los pasos siguientes para completar esta tarea:

1. Vaya a `/usr/local/lib/python2.7/site-packages`.
2. Muestre los paquetes creados en la raíz: `ls -l | grep root`.
3. Desinstale los paquetes mostrados en el paso 2: `sudo rm -rf {package name}`.
4. Vuelva a instalar Python.

## <a name="azure-iot-hub-issues"></a>Problemas de Azure IoT Hub

Si la instancia de Azure IoT Hub se ha aprovisionado correctamente con la CLI de Azure y necesita una herramienta que administre los dispositivos que se conectan a su IoT Hub, puede probar las siguientes herramientas.

### <a name="device-explorer"></a>Explorador de dispositivos

El [Explorador de dispositivos](https://github.com/Azure/azure-iot-sdk-csharp/blob/master/tools/DeviceExplorer) se ejecuta en el equipo local Windows y se conecta a IoT Hub de Azure. Además, se comunica con los siguientes [puntos de conexión de IoT Hub](https://azure.microsoft.com/en-us/documentation/articles/iot-hub-devguide/):

- Administración de identidades de dispositivo para aprovisionar y administrar los dispositivos registrados con su instancia de IoT Hub.
- Recepción de mensajes del dispositivo a la nube, para poder supervisar mensajes enviados desde el dispositivo a la instancia de IoT Hub.
- Envío de mensajes de la nube al dispositivo, para poder enviar mensajes a los dispositivos desde la instancia de IoT Hub.

Configure la cadena de conexión de IoT Hub con esta herramienta para que pueda utilizar todas las funcionalidades.

### <a name="iothub-explorer"></a>iothub-explorer

[iothub-explorer](https://github.com/Azure/iothub-explorer) es una sencilla herramienta multiplataforma de CLI que se utiliza para administrar los clientes de los dispositivos. Esta herramienta puede usarse para administrar los dispositivos en el registro de identidad, supervisar los mensajes del dispositivo a la nube y enviar comandos de la nube al dispositivo.

Para instalar la última versión (preliminar) de la herramienta iothub-explorer, ejecute el siguiente comando:

```bash
npm install -g iothub-explorer@latest
```

Para obtener más ayuda sobre todos los comandos del explorador de IoT Hub y sus parámetros, ejecute el siguiente comando:

```bash
iothub-explorer help
```

### <a name="the-azure-portal"></a>El Portal de Azure

Puede aprovechar todas las capacidades que brinda CLI para crear y administrar todos los recursos de Azure. También puede utilizar [Azure Portal](https://azure.microsoft.com/en-us/documentation/articles/azure-portal-overview/) para ayudar a aprovisionar, administrar y depurar los recursos de Azure.

## <a name="azure-storage-issues"></a>Problemas de Azure Storage

El [Explorador de Microsoft Azure Storage (versión preliminar)](http://storageexplorer.com/) es una aplicación independiente de Microsoft que permite trabajar con datos de Azure Storage en Windows, macOS y Linux. Con esta herramienta, puede conectarse a la tabla y ver los datos que contiene. Puede utilizar esta herramienta para solucionar los problemas de Azure Storage.
