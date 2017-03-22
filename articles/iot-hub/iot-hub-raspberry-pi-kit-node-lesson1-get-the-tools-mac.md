---
title: "Conexión de Raspberry Pi (Node) a Azure IoT: Lección 1: Obtención de las herramientas (macOS) | Microsoft Docs"
description: "Descargue e instale las herramientas y el software necesarios para la primera aplicación de ejemplo de Pi en macOS."
services: iot-hub
documentationcenter: 
author: shizn
manager: timlt
tags: 
keywords: "desarrollo de iot, software de iot, software de internet de las cosas, instalar python mac, instalar git en mac, ejecución de gulp, instalar node js mac"
ms.assetid: 2ea6d211-c0e8-4ade-ac69-d1c2261d78c4
ms.service: iot-hub
ms.devlang: node
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 3/21/2017
ms.author: xshi
translationtype: Human Translation
ms.sourcegitcommit: 64e69df256404e98f6175f77357500b562d74318
ms.openlocfilehash: f157a68f502f018bab83a377367f92ce20f89adf
ms.lasthandoff: 01/24/2017


---
# <a name="get-the-tools-macos-1010"></a>Obtención de las herramientas (Mac OS 10.10)
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
* Un equipo Mac que ejecute macOS Yosemite (10.10) o posterior

## <a name="install-git-and-nodejs"></a>Instalación de Git y Node.js
Para instalar Git y Node.js, use la utilidad de administración de paquetes [Homebrew](http://brew.sh) siguiendo estos pasos:

1. Instale Homebrew. Si ya ha instalado Homebrew, vaya al paso 2.
   
   1. Presione `Cmd + Space` y escriba `Terminal` para abrir una ventana de terminal.
   2. Ejecute el siguiente comando:
      
      ```bash
      /usr/bin/ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"
      ```
2. Instale Git y Node.js ejecutando el comando siguiente:
   
   ```bash
   brew install node git
   ```

## <a name="install-additional-nodejs-development-tools"></a>Instalación de herramientas de desarrollo de Node.js adicionales
Utilice [gulp.js](http://gulpjs.com) para automatizar la implementación de la aplicación de ejemplo en Pi. Utilice [device-discovery-cli](https://github.com/Azure/device-discovery-cli) para recuperar información de la red de los dispositivos de IoT.

Instale `gulp` y `device-discovery-cli` ejecutando el comando siguiente en la ventana de terminal:

```bash
npm install -g device-discovery-cli gulp
```

Si experimenta problemas al instalar Node.js y estas herramientas de desarrollo adicionales en Mac OS, consulte la [Guía de solución de problemas](iot-hub-raspberry-pi-kit-node-troubleshooting.md) para ver soluciones a problemas comunes.

## <a name="install-visual-studio-code"></a>Instalación de Visual Studio Code
[Descargue](https://code.visualstudio.com/docs/setup/osx) e instale Visual Studio Code. Visual Studio Code es un editor de código fuente ligero pero eficaz para Windows, Linux y Mac OS. Use este editor más adelante en el tutorial para editar el código de ejemplo.

## <a name="summary"></a>Resumen
Ha instalado las herramientas de desarrollo y el software necesarios para la primera aplicación de ejemplo. En la siguiente tarea, creará, implementará y ejecutará la aplicación de ejemplo en Pi.

## <a name="next-steps"></a>Pasos siguientes
[Creación e implementación de la aplicación de intermitencia de ejemplo](iot-hub-raspberry-pi-kit-node-lesson1-deploy-blink-app.md)


