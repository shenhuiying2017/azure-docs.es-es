---
title: "Dispositivo SensorTag y puerta de enlace de Azure IoT: Lección 2: Obtención de las herramientas (Windows) | Microsoft Docs"
description: Instale las herramientas y el software en el equipo host que ejecuta Windows, cree una instancia de IoT Hub y registre el dispositivo en ella.
services: iot-hub
documentationcenter: 
author: shizn
manager: timtl
tags: 
keywords: "desarrollo de iot, software de iot, servicio en la nube de iot, software de internet de las cosas, cli de azure, instalar git en windows, ejecución de gulp, instalar node js windows, instalar npm en windows, instalar python en windows"
ms.assetid: 18ae6ee4-574a-4d5f-9838-ca2a78165628
ms.service: iot-hub
ms.devlang: c
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/07/2016
ms.author: xshi
translationtype: Human Translation
ms.sourcegitcommit: 61e9a9fc7876094c04238c61cfc38efdd97b05f7
ms.openlocfilehash: 4a854222be9aaf6733f1c256dd75195927299099


---
# <a name="get-the-tools-windows-7-and-later"></a>Obtención de las herramientas (Windows 7 y posterior)
> [!div class="op_single_selector"]
> * [Windows 7 o posterior](iot-hub-gateway-kit-c-lesson2-get-the-tools-win32.md)
> * [Ubuntu 16.04](iot-hub-gateway-kit-c-lesson2-get-the-tools-ubuntu.md)
> * [macOS 10.10](iot-hub-gateway-kit-c-lesson2-get-the-tools-mac.md)

## <a name="what-you-will-do"></a>Lo que hará

- Instale Git, Node.js, Gulp y Python.
- Instalación de la interfaz de la línea de comandos de Azure (CLI de Azure) 

Si tiene problemas, busque soluciones en la [página de solución de problemas](iot-hub-gateway-kit-c-troubleshooting.md).

## <a name="what-you-will-learn"></a>Lo qué aprenderá

En esta lección, aprenderá lo siguiente:

- Instalación de [Git](https://git-scm.com/) y [Node.js](https://nodejs.org/en/).
  - Git es un sistema de control de versiones distribuido de código abierto. La aplicación de ejemplo de esta lección se almacena en Git.
  - Node.js es un entorno en tiempo de ejecución de JavaScript con un amplio ecosistema de paquetes.
- Uso de [NPM](https://www.npmjs.com/) para instalar las herramientas de desarrollo de Node.js.
  - La versión mínima necesaria de Node.js es 4.5 LTS.
  - NPM es uno de los administradores de paquetes para Node.js.
- Instalación de Visual Studio Code.
  - Visual Studio Code es un editor de código fuente multiplataforma ligero pero eficaz para Windows, Linux y macOS. Ofrece un elevado nivel de compatibilidad para la depuración, control de Git insertado, resaltado de sintaxis, función inteligente de autocompletar código, fragmentos de código y refactorización de código.
- Cómo instalar Python.
  - Python es un lenguaje de programación general, muy usado, destinado a fines genéricos, interpretado y dinámico.
- Cómo instalar la CLI de Azure.
  - La CLI de Azure proporciona una experiencia de línea de comandos multiplataforma en Azure. De este modo, puede trabajar directamente desde la línea de comandos para aprovisionar y administrar los recursos.
- Uso de la CLI de Azure para crear una instancia de IoT Hub.

## <a name="what-you-need"></a>Lo que necesita

- Una conexión a Internet para descargar las herramientas y el software.
- Un equipo Windows.

## <a name="install-git-and-nodejs"></a>Instalación de Git y Node.js

Haga clic en los vínculos siguientes para descargar e instalar Git y Node.js LTS para Windows.

- [Obtención de Git para Windows](https://git-scm.com/download/win/)
- [Obtención de Node.js LTS para Windows](https://nodejs.org/en/)

## <a name="install-nodejs-development-tools"></a>Instalación de herramientas de desarrollo de Node.js

Use [gulp.js](http://gulpjs.com/) para automatizar la implementación y ejecución de scripts.

Presione `Windows + R`, escriba `cmd` y presione `Enter` para abrir una ventana del símbolo del sistema y, después, ejecute el siguiente comando:

```cmd
npm install -g gulp
```

Si tiene problemas con la instalación, consulte la [guía de solución de problemas](iot-hub-gateway-kit-c-troubleshooting.md) para solucionar problemas comunes.

> [!Note]
> Se precisan Node, NPM y Gulp para ejecutar scripts de automatización desarrollados en Node.js.

## <a name="install-python"></a>Instalación de Python

Puede elegir entre Python 2.7, 3.4 o 3.5. En este tutorial, se usa Python 2.7. Si ya ha instalado Python, vaya a la siguiente sección.

[Obtención de Python para Windows](https://www.python.org/downloads/)

También debe agregar la ruta de acceso de las carpetas donde se instalan Python.exe y pip.exe a la variable de entorno `PATH` del sistema. De forma predeterminada, se instala python.exe en `C:\Python27` y pip.exe en `C:\Python27\Scripts`.

## <a name="install-the-azure-cli"></a>Instalación de la CLI de Azure

Para instalar la CLI de Azure, siga estos pasos:

1. Abra una ventana de símbolo del sistema como administrador.

2. Instale la CLI de Azure ejecutando el comando siguiente:

   ```cmd
   pip install --upgrade azure-cli
   pip install --upgrade azure-cli-iot
   ```

   La instalación puede tardar 5 minutos.

3. Compruebe la instalación ejecutando el comando siguiente:

   ```cmd
   az iot -h
   ```

   Si la instalación se realiza correctamente, verá el siguiente resultado.

   ![Verificación de la instalación de la CLI de Azure](media/iot-hub-gateway-kit-lessons/lesson2/az_iot_help_win.png)

## <a name="install-visual-studio-code"></a>Instalación de Visual Studio Code

Use Visual Studio Code, como se explica más adelante en este tutorial, para editar los archivos de configuración.

[Descargue](https://code.visualstudio.com/docs/setup/windows) e instale Visual Studio Code.

## <a name="summary"></a>Resumen

Ha instalado todas las herramientas y el software necesarios en el equipo host. La siguiente tarea consiste en utilizar la CLI de Azure para crear una instancia de IoT Hub y registrar el dispositivo en ella.

## <a name="next-steps"></a>Pasos siguientes
[Creación de una instancia de IoT Hub y registro del dispositivo](iot-hub-gateway-kit-c-lesson2-register-device.md)



<!--HONumber=Jan17_HO4-->


