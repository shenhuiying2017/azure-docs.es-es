---
title: "Dispositivo SensorTag y puerta de enlace de Azure IoT: Lección 2: Obtención de las herramientas (Ubuntu) | Microsoft Docs"
description: Instale las herramientas y el software en el equipo host que ejecuta Ubuntu, cree una instancia de IoT Hub y registre el dispositivo en ella.
services: iot-hub
documentationcenter: 
author: shizn
manager: timtl
tags: 
keywords: "desarrollo de iot, software de iot, servicio en la nube de iot, software de internet de las cosas, cli de azure, instalar git en ubuntu, ejecución de gulp, instalar node js ubuntu"
ms.assetid: 0bac1412-385b-4255-a33f-9d44c35feb3e
ms.service: iot-hub
ms.devlang: c
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 3/21/2017
ms.author: xshi
translationtype: Human Translation
ms.sourcegitcommit: 61e9a9fc7876094c04238c61cfc38efdd97b05f7
ms.openlocfilehash: 6414a4f37367d8acc0bab9356b8a09aadf13bfc9
ms.lasthandoff: 01/25/2017


---
# <a name="get-the-tools-ubuntu-1604"></a>Obtención de las herramientas (Ubuntu 16.04)
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

- Cómo instalar Git y Node.js
  - Git es un sistema de control de versiones distribuido de código abierto. La aplicación de ejemplo de esta lección se almacena en Git.
  - Node.js es un entorno de tiempo de ejecución de JavaScript con un amplio ecosistema de paquetes.
- Uso de NPM para instalar las herramientas de desarrollo de Node.js.
  - La versión mínima necesaria de Node.js es 4.5 LTS.
  - NPM es uno de los administradores de paquetes para Node.js.
- Instalación de Visual Studio Code.
  - Visual Studio Code es un editor de código fuente multiplataforma ligero pero eficaz para Windows, Linux y macOS. Ofrece un elevado nivel de compatibilidad para la depuración, control de Git insertado, resaltado de sintaxis, función inteligente de autocompletar código, fragmentos de código y refactorización de código.
- Instalación de la CLI de Azure
  - La CLI de Azure proporciona una experiencia de línea de comandos multiplataforma en Azure. De este modo, puede trabajar directamente desde la línea de comandos para aprovisionar y administrar los recursos.
- Uso de la CLI de Azure para crear una instancia de IoT Hub.

## <a name="what-you-need"></a>Lo que necesita

- Una conexión a Internet para descargar las herramientas y el software.
- Un equipo que ejecute Ubuntu 16.04 o posterior

## <a name="install-git-and-nodejs"></a>Instalación de Git y Node.js

Para instalar Git y Node.js, siga estos pasos:

1. Presione `Ctrl + Alt + T` para abrir una ventana de terminal.
2. Ejecute los comandos siguientes:

   ```bash
   sudo apt-get update
   curl -sL https://deb.nodesource.com/setup_6.x | sudo -E bash -
   sudo apt-get install -y nodejs
   sudo apt-get install git
   ```

## <a name="install-nodejs-development-tools"></a>Instalación de herramientas de desarrollo de Node.js

Use [gulp.js](http://gulpjs.com/) para automatizar la implementación y ejecución de scripts.

Para instalar Gulp, ejecute el comando siguiente en una ventana del terminal:

```bash
sudo npm install -g gulp
```

Si tiene problemas con la instalación, consulte la [guía de solución de problemas](iot-hub-gateway-kit-c-troubleshooting.md) para solucionar problemas comunes.

> [!Note]
> Se precisan Node, NPM y Gulp para ejecutar scripts de automatización desarrollados en Node.js.

## <a name="install-the-azure-cli"></a>Instalación de la CLI de Azure

Para instalar la CLI de Azure, siga estos pasos:

1. Ejecute los siguientes comandos en una ventana de terminal:

   ```bash
   sudo apt-get update
   sudo apt-get install -y libssl-dev libffi-dev
   sudo apt-get install -y python-dev
   sudo apt-get install -y build-essential
   sudo apt-get install -y python-pip
   sudo pip install --upgrade azure-cli
   sudo pip install --upgrade azure-cli-iot
   ```

   La instalación puede tardar 5 minutos.

2. Compruebe la instalación ejecutando el comando siguiente:

   ```bash
   az iot -h
   ```
Si la instalación se realiza correctamente, verá el siguiente resultado.
![Verificación de la instalación de la CLI de Azure](media/iot-hub-gateway-kit-lessons/lesson2/az_iot_help_ubuntu.png)

### <a name="install-visual-studio-code"></a>Instalación de Visual Studio Code

Use Visual Studio Code, como se explica más adelante en este tutorial, para editar los archivos de configuración.

[Descargue](https://code.visualstudio.com/docs/setup/linux) e instale Visual Studio Code.

## <a name="summary"></a>Resumen

Ha instalado todas las herramientas y el software necesarios en el equipo host. La siguiente tarea consiste en utilizar la CLI de Azure para crear una instancia de IoT Hub y registrar el dispositivo en ella.

## <a name="next-steps"></a>Pasos siguientes
[Creación de una instancia de IoT Hub y registro del dispositivo](iot-hub-gateway-kit-c-lesson2-register-device.md)

