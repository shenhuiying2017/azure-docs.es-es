---
title: "Conexión de Intel Edison (Node) a Azure IoT: Lección 1: Implementación de la aplicación | Microsoft Docs"
description: "Clone la aplicación de C de ejemplo de GitHub y ejecute Gulp para implementar esta aplicación en la placa Intel Edison. Esta aplicación de ejemplo hace parpadear el LED conectado a la placa cada dos segundos."
services: iot-hub
documentationcenter: 
author: shizn
manager: timtl
tags: 
keywords: "proyectos de LED de Arduino, intermitencia del LED de Arduino, código de intermitencia del LED de Arduino, programa de intermitencia del LED de Arduino, ejemplo de intermitencia en Arduino"
ROBOTS: NOINDEX
redirect_url: /azure/iot-hub/iot-hub-intel-edison-kit-node-get-started
ms.assetid: ed2c21d0-c72c-4ac2-9e70-347e9a0711c0
ms.service: iot-hub
ms.devlang: nodejs
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 3/21/2017
ms.author: xshi
translationtype: Human Translation
ms.sourcegitcommit: adf5b10721a28432e6b37ef73c6a7e7ec9f93cdd
ms.openlocfilehash: 182e63d40986b61a12885799b1b33bb570437a3c
ms.lasthandoff: 01/25/2017


---
# <a name="create-and-deploy-the-blink-application"></a>Creación e implementación de la aplicación de intermitencia
## <a name="what-you-will-do"></a>Lo que hará
Clone la aplicación de C de ejemplo de GitHub y use la herramienta Gulp para implementar la aplicación de ejemplo en Intel Edison. La aplicación de ejemplo hace parpadear el LED conectado a la placa cada dos segundos. Si tiene problemas, busque soluciones en [esta página][troubleshooting].

## <a name="what-you-will-learn"></a>Lo qué aprenderá
* Cómo implementar y ejecutar la aplicación de ejemplo en Edison

## <a name="what-you-need"></a>Lo que necesita
Debe haber completado correctamente las siguientes operaciones:

* [Configuración del dispositivo][configure-your-device]
* [Obtención de las herramientas][get-the-tools]

## <a name="open-the-sample-application"></a>Apertura de la aplicación de ejemplo
Para abrir la aplicación de ejemplo, siga estos pasos:

1. Clone el repositorio de ejemplo de GitHub ejecutando el comando siguiente:

   ```bash
   git clone https://github.com/Azure-Samples/iot-hub-node-edison-getting-started.git
   ```
2. Abra la aplicación de ejemplo en Visual Studio Code ejecutando los comandos siguientes:

   ```bash
   cd iot-hub-node-edison-getting-started
   cd Lesson1
   code .
   ```

   ![Estructura del repositorio][repo-structure]

El archivo de la subcarpeta `app` es el archivo de origen de la clave que contiene el código para controlar el LED.

### <a name="install-application-dependencies"></a>Instalación de las dependencias de aplicaciones
Instale las bibliotecas y otros módulos que necesite para la aplicación de ejemplo ejecutando el comando siguiente:

```bash
npm install
```

## <a name="configure-the-device-connection"></a>Configuración de la conexión de dispositivos
Para configurar la conexión de dispositivos, siga estos pasos:

1. Genere el archivo de configuración de dispositivos mediante la ejecución del comando siguiente:

   ```bash
   gulp init
   ```

   El archivo de configuración `config-edison.json` contiene las credenciales de usuario que use para iniciar sesión en Edison. Para evitar la pérdida de las credenciales de usuario, se genera el archivo de configuración en la subcarpeta `.iot-hub-getting-started` de la carpeta principal del equipo.

2. Abra el archivo de configuración de dispositivos en Visual Studio Code ejecutando el comando siguiente:

   ```bash
   # For Windows command prompt
   code %USERPROFILE%\.iot-hub-getting-started\config-edison.json

   # For MacOS or Ubuntu
   code ~/.iot-hub-getting-started/config-edison.json
   ```

3. Reemplace el marcador de posición `[device hostname or IP address]` y `[device password]` por la dirección IP y contraseña que ha anotado en la lección anterior.

   ![Config.json](media/iot-hub-intel-edison-lessons/lesson1/vscode-config-mac.png)

¡Enhorabuena! Ha creado correctamente la primera aplicación de ejemplo para Edison.

## <a name="deploy-and-run-the-sample-application"></a>Implementación y ejecución de la aplicación de ejemplo

### <a name="deploy-and-run-the-sample-app"></a>Implementación y ejecución de la aplicación de ejemplo
Implemente y ejecute la aplicación de ejemplo usando el comando siguiente:

```bash
gulp deploy && gulp run
```

### <a name="verify-the-app-works"></a>Comprobación del funcionamiento de la aplicación
La aplicación de ejemplo se finaliza automáticamente después de que el LED parpadee 20 veces. En caso contrario, consulte [esta guía][troubleshooting] para ver soluciones a problemas comunes.

![Intermitencia del LED][led-blinking]

## <a name="summary"></a>Resumen
Ha instalado las herramientas necesarias para usar Edison e implementado una aplicación de ejemplo para que Edison haga parpadear el LED. Ahora puede crear, implementar y ejecutar otra aplicación de ejemplo que conecte Edison a IoT Hub de Azure para enviar y recibir mensajes.

## <a name="next-steps"></a>Pasos siguientes
[Obtención de las herramientas de Azure][get-the-azure-tools]

<!-- Images and links -->

[troubleshooting]: iot-hub-intel-edison-kit-node-troubleshooting.md
[Configure-your-device]: iot-hub-intel-edison-kit-node-lesson1-configure-your-device.md
[get-the-tools]: iot-hub-intel-edison-kit-node-lesson1-get-the-tools-win32.md
[repo-structure]: media/iot-hub-intel-edison-lessons/lesson1/repo_structure.png
[led-blinking]: media/iot-hub-intel-edison-lessons/lesson1/led_blinking.png
[get-the-azure-tools]: iot-hub-intel-edison-kit-node-lesson2-get-azure-tools-win32.md

