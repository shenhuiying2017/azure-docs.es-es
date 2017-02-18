---
title: "Conexión de Intel Edison (C) a Azure IoT: Solución de problemas | Microsoft Docs"
description: "Página de solución de problemas para la experiencia de Intel Edison (C)"
services: iot-hub
documentationcenter: 
author: shizn
manager: timtl
tags: 
keywords: "Solución de problemas de Arduino"
ms.assetid: fe20f2fe-490c-4910-82e1-578ed504ae86
ms.service: iot-hub
ms.devlang: c
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/8/2016
ms.author: xshi
translationtype: Human Translation
ms.sourcegitcommit: 475b25f02715a60493e79ecd2170854019dfc4ac
ms.openlocfilehash: c35afd54fb20c3ecd1a70796094093ad748385d0


---
# <a name="troubleshooting"></a>Solución de problemas
## <a name="hardware-issues"></a>Problemas de hardware
Para obtener más información sobre cómo solucionar problemas comunes en Intel Edison, consulte la [página oficial de solución de problemas](https://software.intel.com/en-us/node/637974).

## <a name="nodejs-package-issues"></a>Problemas de paquetes en Node.js
### <a name="no-response-during-gulp-tasks"></a>Sin respuesta durante las tareas de Gulp
Si tiene problemas para ejecutar tareas de Gulp, puede agregar la opción `--verbose` para realizar una depuración. Trate de finalizar las tareas actuales de Gulp con `Ctrl + C` y, luego, ejecute el comando siguiente en la ventana de consola para ver los mensajes de depuración. En la salida de la consola, deberían aparecer mensajes de error detallados. 

```bash
gulp --verbose
```

### <a name="npm-issues"></a>Problemas de NPM
Pruebe a ejecutar el paquete de NPM con el siguiente comando:

```bash
npm install -g npm
```

Si el problema persiste, deje sus comentarios al final de este artículo o cree un problema de GitHub en el [repositorio de ejemplos][sample-repository].

## <a name="azure-cli-issues"></a>Problemas de la CLI de Azure
La interfaz de la línea de comandos de Azure (CLI de Azure ) es una versión preliminar. Busque soluciones en la [Guía de instalación de la versión preliminar](https://github.com/Azure/azure-cli/blob/master/doc/preview_install_guide.md). Trate de actualizar la CLI de Azure a la versión más reciente cuando los comandos no funcionen según lo previsto.

Si detecta los errores con la herramienta, envíe un [problema](https://github.com/Azure/azure-cli/issues) en la sección de **problemas** del repositorio de GitHub.

Para obtener ayuda sobre la solución de problemas comunes, consulte el archivo [readme](https://github.com/Azure/azure-cli/blob/master/README.rst).

Si se encuentra un error que indica que no se ha podido encontrar una versión que satisfaga los requisitos, ejecute el siguiente comando para actualizar pip a la versión más reciente.

```bash
python -m pip install --upgrade pip
```

## <a name="python-installation-issues"></a>Problemas de instalación de Python
### <a name="legacy-installation-issues-macos"></a>Problemas de instalación de versiones antiguas (Mac OS)
Al instalar **pip**, se produce un error de permisos si hay paquetes antiguos instalados con permisos **su**. Esto se debe a que hay una instalación anterior de Python con Brew (macOS) que no se ha desinstalado por completo. Algunos paquetes **pip** de una instalación anterior se crearon de raíz, lo que provoca el error de permiso. La solución es quitar dichos paquetes instalados de raíz. Realice los pasos siguientes para completar esta tarea:

1. Vaya a: /usr/local/lib/python2.7/site-packages.
2. Muestre los paquetes creados de raíz: `ls -l | grep root`.
3. Desinstale los paquetes mostrados en el paso 2: `sudo rm -rf {package name}`.
4. Vuelva a instalar Python.

## <a name="azure-iot-hub-issues"></a>Problemas de Azure IoT Hub
Si el centro de IoT Hub de Azure se ha aprovisionado correctamente con `azure-cli` y necesita una herramienta que administre los dispositivos que se conectan a este centro, puede probar las siguientes herramientas:

### <a name="device-explorer"></a>Explorador de dispositivos
El [Explorador de dispositivos](https://github.com/Azure/azure-iot-sdk-csharp/tree/master/tools/DeviceExplorer) se ejecuta en el equipo local Windows y se conecta a IoT Hub de Azure. Además, se comunica con los siguientes [puntos de conexión de IoT Hub](iot-hub-devguide.md):

- _Administración de identidades de dispositivo_ para aprovisionar y administrar los dispositivos registrados con su instancia de IoT Hub.
- _Recepción de mensajes del dispositivo a la nube_, para poder supervisar mensajes enviados desde el dispositivo al centro de IoT Hub.
- _Envío de mensajes de la nube al dispositivo_, para poder enviar mensajes a los dispositivos desde el centro de IoT Hub.

Configurar `IoT hub connection string` dentro de esta herramienta para usar todas sus funcionalidades.

### <a name="iot-hub-explorer"></a>Explorador de IoT Hub
El [Explorador de IoT Hub](https://github.com/Azure/iothub-explorer) es una herramienta CLI multiplataforma de ejemplo para administrar clientes de dispositivos. Esta herramienta puede usarse para administrar los dispositivos en el registro de identidad, supervisar los mensajes del dispositivo a la nube y enviar comandos de la nube al dispositivo.

Para instalar la última versión (versión preliminar) de la herramienta iothub-explorer, ejecute el siguiente comando en el entorno de la línea de comandos:

```bash
npm install -g iothub-explorer@latest
```

Puede utilizar el siguiente comando para obtener más ayuda sobre todos los comandos del Explorador de IoT Hub y sus parámetros:

```bash
iothub-explorer help
```

### <a name="azure-portal"></a>Portal de Azure
Puede aprovechar todas las capacidades que brinda CLI para crear y administrar todos los recursos de Azure. También puede utilizar [Azure Portal](../azure-portal-overview.md) para ayudar a aprovisionar, administrar y depurar los recursos de Azure.

## <a name="azure-storage-issues"></a>Problemas de Azure Storage
El [Explorador de Microsoft Azure Storage (versión preliminar)](http://storageexplorer.com) es una aplicación independiente de Microsoft que permite trabajar con datos de [Azure Storage](https://azure.microsoft.com/en-us/services/storage/) en Windows, macOS y Linux. Con esta herramienta, puede conectarse a la tabla y ver los datos que contiene. Puede utilizar esta herramienta para solucionar los problemas de Azure Storage.

## <a name="next-steps"></a>Pasos siguientes
Esta página solo incluye los problemas más comunes del kit de Intel Edison. También puede dejar comentarios abajo para que podamos solucionar problemas adicionales.

Volver a [Introducción a Intel Edison (C)](iot-hub-intel-edison-kit-c-get-started.md)

<!-- Images and links -->

[sample-repository]: https://github.com/Azure-Samples/iot-hub-c-edison-getting-started


<!--HONumber=Jan17_HO4-->


