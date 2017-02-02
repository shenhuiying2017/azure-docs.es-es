---
title: "Preparación del equipo host y de Azure IoT Hub | Microsoft Docs"
description: Instale las herramientas y el software en el equipo host que ejecuta Ubuntu, cree un IoT Hub y registre el dispositivo en IoT Hub.
services: iot-hub
documentationcenter: 
author: shizn
manager: timtl
tags: 
keywords: "desarrollo de iot, software de iot, servicio en la nube de iot, software de internet de las cosas, cli de azure, instalar git en ubuntu, ejecución de gulp, instalar node js ubuntu"
ms.assetid: cf673154-ce67-4ed7-a9f7-2440301c6270
ms.service: iot-hub
ms.devlang: c
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/28/2016
ms.author: xshi
translationtype: Human Translation
ms.sourcegitcommit: 1c4f44787a7200a1c3634b258df32d30152daa90
ms.openlocfilehash: 6feb4f6fa72aeb3d43211ad8c3973ad2b96671ec


---
# <a name="get-the-tools-ubuntu-1604"></a>Obtención de las herramientas (Ubuntu 16.04)
> [!div class="op_single_selector"]
> * [Windows 7 o posterior](iot-hub-gateway-kit-c-sim-lesson2-get-the-tools-win32.md)
> * [Ubuntu 16.04](iot-hub-gateway-kit-c-sim-lesson2-get-the-tools-ubuntu.md)
> * [macOS 10.10](iot-hub-gateway-kit-c-sim-lesson2-get-the-tools-mac.md)

## <a name="what-you-will-do"></a>Lo que hará

- Instale Git, Node.js, Gulp y Python.
- Instalación de la interfaz de la línea de comandos de Azure (CLI de Azure) 

Si tiene problemas, busque soluciones en la [página de solución de problemas](iot-hub-gateway-kit-c-sim-troubleshooting.md).
## <a name="what-you-will-learn"></a>Lo qué aprenderá

En esta lección, aprenderá lo siguiente:

- Cómo instalar Git y Node.js
  - Git es un sistema de control de versiones distribuido de código. La aplicación de ejemplo de esta lección se almacena en Git.
  - Node.js es un entorno de tiempo de ejecución de JavaScript con un amplio ecosistema de paquetes.
- Cómo usar NPM para instalar las herramientas de desarrollo de Node.js.
  - La versión mínima necesaria de Node.js es 4.5 LTS.
  - NPM es uno de los administradores de paquetes para Node.js.
- Cómo instalar Visual Studio Code.
  - Visual Studio Code es un editor de código fuente multiplataforma ligero pero eficaz para Windows, Linux y Mac OS. Ofrece un alto nivel de compatibilidad para la depuración, control de Git insertado, resaltado de sintaxis, completar código inteligente, fragmentos de código y refactorización de código.
- Instalación de la CLI de Azure
  - La CLI de Azure proporciona una experiencia de línea de comandos multiplataforma en Azure. De este modo, puede trabajar directamente desde la línea de comandos para aprovisionar y administrar los recursos.
- Cómo usar la CLI de Azure para crear un IoT Hub.

## <a name="what-you-need"></a>Lo que necesita

- Una conexión a Internet para descargar las herramientas y el software.
- Un equipo que ejecute Ubuntu 16.04 o posterior

## <a name="install-git-and-nodejs"></a>Instalación de Git y Node.js

Para instalar Git y Node.js, siga estos pasos:

1. Presione `Ctrl + Alt + T` para abrir un terminal.
2. Ejecute los comandos siguientes:

   ```bash
   sudo apt-get update
   curl -sL https://deb.nodesource.com/setup_6.x | sudo -E bash -
   sudo apt-get install -y nodejs
   sudo apt-get install git
   ```

## <a name="install-nodejs-development-tools"></a>Instalación de herramientas de desarrollo de Node.js

Use [gulp.js](http://gulpjs.com/) para automatizar la implementación y ejecución de scripts.

Para instalar gulp, ejecute el comando siguiente en el terminal:

```bash
sudo npm install -g gulp
```

Si tiene problemas con la instalación, vea la [guía de solución de problemas](iot-hub-gateway-kit-c-sim-troubleshooting.md) para solucionar problemas comunes.

> [!Note]
> Node, NPM y Gulp son necesarios para ejecutar scripts de automatización desarrollados en Node.js.

## <a name="install-the-azure-cli"></a>Instalación de la CLI de Azure

Para instalar la CLI de Azure, siga estos pasos:

1. Ejecute los siguientes comandos en el terminal:

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

Use Visual Studio Code más adelante en este tutorial para editar archivos de configuración.

[Descargue](https://code.visualstudio.com/docs/setup/linux) e instale Visual Studio Code.

## <a name="summary"></a>Resumen

Ha instalado todas las herramientas y el software necesarios en el equipo host. La siguiente tarea consiste en utilizar la CLI de Azure para crear un IoT Hub y registrar el dispositivo en IoT Hub.

## <a name="next-steps"></a>Pasos siguientes
[Creación de un IoT Hub y registro del dispositivo](iot-hub-gateway-kit-c-sim-lesson2-register-device.md)



<!--HONumber=Dec16_HO3-->


