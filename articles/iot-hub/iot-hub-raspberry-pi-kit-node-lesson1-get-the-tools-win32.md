---
title: "Obtención de las herramientas (Windows 7 o posterior) | Microsoft Docs"
description: "Descargue e instale las herramientas y el software necesarios para la primera aplicación de ejemplo de Pi en Windows 7 y versiones posteriores."
services: iot-hub
documentationcenter: 
author: shizn
manager: timlt
tags: 
keywords: "desarrollo de iot, software de iot, software de internet de las cosas, instalar git en windows, ejecución de gulp, instalar node js windows, instalar npm en windows, instalar python en windows"
ms.assetid: b3d88e17-97cc-4f23-85fd-a688fc228eb8
ms.service: iot-hub
ms.devlang: node
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/28/2016
ms.author: xshi
translationtype: Human Translation
ms.sourcegitcommit: ffcb9214b8fa645a8a2378c5e7054b9f984addbb
ms.openlocfilehash: 63d4a4a5cbc0d7313c5b527e5ec417d1b968483a


---
# <a name="get-the-tools-windows-7-or-later"></a>Obtención de las herramientas (Windows 7 o posterior)

> [!div class="op_single_selector"]
> * [Windows 7 o posterior](iot-hub-raspberry-pi-kit-node-lesson1-get-the-tools-win32.md)
> * [Ubuntu 16.04](iot-hub-raspberry-pi-kit-node-lesson1-get-the-tools-ubuntu.md)
> * [macOS 10.10](iot-hub-raspberry-pi-kit-node-lesson1-get-the-tools-mac.md)


## <a name="what-you-will-do"></a>Lo que hará
Descargue las herramientas de desarrollo y el software para la primera aplicación de ejemplo de Raspberry Pi 3. Si tiene problemas, busque soluciones en la [página de solución de problemas](iot-hub-raspberry-pi-kit-node-troubleshooting.md).

## <a name="what-you-will-learn"></a>Lo qué aprenderá
En este artículo, aprenderá lo siguiente:

* Cómo instalar Git y Node.js
  * [Git](https://git-scm.com) es un sistema de control de versiones distribuido de código. La aplicación de ejemplo de este artículo se almacena en Git.
  * [Node.js](https://nodejs.org/en/) es un entorno de tiempo de ejecución de JavaScript con un amplio ecosistema de paquetes.
* Cómo usar NPM para instalar las herramientas de desarrollo de Node.js adicionales.
  * La versión mínima necesaria de Node.js es 4.5 LTS.
  * [NPM](https://www.npmjs.com) es uno de los administradores de paquetes para Node.js.

## <a name="what-you-need"></a>Lo que necesita
Para completar esta operación, necesitará:

* Una conexión a Internet para descargar el software y las herramientas de desarrollo necesarias
* Un equipo que ejecute Windows

## <a name="install-git-and-nodejs"></a>Instalación de Git y Node.js
Haga clic en los vínculos siguientes para descargar e instalar Git y Node.js LTS para Windows.

* [Obtención de Git para Windows](https://git-scm.com/download/win/)
* [Obtención de Node.js LTS para Windows](https://nodejs.org/en/)

## <a name="install-additional-nodejs-development-tools"></a>Instalación de herramientas de desarrollo de Node.js adicionales
Utilice [gulp.js](http://gulpjs.com) para automatizar la implementación de la aplicación de ejemplo en Pi. Utiliza también [device-discovery-cli](https://github.com/Azure/device-discovery-cli) para recuperar información de la red de los dispositivos de IoT.

Inicie un símbolo del sistema como administrador. Instale `gulp` y `device-discovery-cli` ejecutando el comando siguiente:

```bash
npm install -g device-discovery-cli gulp
```

Si experimenta problemas al instalar Node.js y estas herramientas de desarrollo adicionales de Node.js en el equipo, consulte la [Guía de solución de problemas](iot-hub-raspberry-pi-kit-node-troubleshooting.md) para ver soluciones a problemas comunes.

## <a name="install-visual-studio-code"></a>Instalación de Visual Studio Code
[Descargue](https://code.visualstudio.com/docs/setup/windows) e instale Visual Studio Code. Visual Studio Code es un editor de código fuente ligero pero eficaz para Windows, Linux y Mac OS. Use este editor más adelante en el tutorial para editar el código de ejemplo.

## <a name="summary"></a>Resumen
Ha instalado las herramientas de desarrollo y el software necesarios para la primera aplicación de ejemplo. En la siguiente tarea, creará, implementará y ejecutará la aplicación de ejemplo en Pi.

## <a name="next-steps"></a>Pasos siguientes
[Creación e implementación de la aplicación de intermitencia de ejemplo](iot-hub-raspberry-pi-kit-node-lesson1-deploy-blink-app.md)




<!--HONumber=Nov16_HO5-->


