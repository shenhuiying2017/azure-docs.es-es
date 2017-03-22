---
title: "Conexión de Arduino a Azure IoT: Lección 1: Implementación de la aplicación | Microsoft Docs"
description: "Clone la aplicación de Arduino de ejemplo de GitHub y ejecute gulp para implementar esta aplicación en Adafruit Feather M0 WiFi. Esta aplicación de ejemplo hace parpadear la GPIO."
services: iot-hub
documentationcenter: 
author: shizn
manager: timtl
tags: 
keywords: "proyectos de LED de Arduino, intermitencia del LED de Arduino, código de intermitencia del LED de Arduino, programa de intermitencia del LED de Arduino, ejemplo de intermitencia en Arduino"
ms.assetid: b0a7d076-d580-4686-9f7d-c0712750b615
ms.service: iot-hub
ms.devlang: arduino
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 3/21/2017
ms.author: xshi
translationtype: Human Translation
ms.sourcegitcommit: 64e69df256404e98f6175f77357500b562d74318
ms.openlocfilehash: a2058611cecb33eab04de9249ad73b1c4ec6c2a5
ms.lasthandoff: 01/24/2017


---
# <a name="create-and-deploy-the-blink-application"></a>Creación e implementación de la aplicación de intermitencia
## <a name="what-you-will-do"></a>Lo que hará
Clone la aplicación de Arduino de ejemplo de GitHub y use la herramienta gulp para implementar la aplicación de ejemplo en la placa Adafruit Feather M0 WiFi Arduino. La aplicación de ejemplo hace parpadear el LED 13 de la GPIO cada 2 segundos.

Si tiene problemas, busque soluciones en [esta página][troubleshooting-page].

## <a name="what-you-will-learn"></a>Lo qué aprenderá
* Cómo implementar y ejecutar la aplicación de ejemplo en la placa de Arduino

## <a name="what-you-need"></a>Lo que necesita
Debe haber completado correctamente las siguientes operaciones:

* [Configuración del dispositivo][configure-your-device]
* [Obtención de las herramientas][get-the-tools]

## <a name="open-the-sample-application"></a>Apertura de la aplicación de ejemplo
Para abrir la aplicación de ejemplo, siga estos pasos:

1. Clone el repositorio de ejemplo de GitHub ejecutando el comando siguiente:

   ```bash
   git clone https://github.com/Azure-Samples/iot-hub-c-feather-m0-getting-started.git
   ```
2. Abra la aplicación de ejemplo en Visual Studio Code ejecutando los comandos siguientes:

   ```bash
   cd iot-hub-c-feather-m0-getting-started
   cd Lesson1
   code .
   ```

   ![Estructura del repositorio][repo-structure]

El archivo `app.ino` de la subcarpeta `app` es el archivo de origen de la clave que contiene el código para controlar el LED.

### <a name="install-application-dependencies"></a>Instalación de las dependencias de aplicaciones
Instale las bibliotecas y otros módulos que necesite para la aplicación de ejemplo ejecutando el comando siguiente:

```bash
npm install
```

## <a name="configure-the-device-connection"></a>Configuración de la conexión de dispositivos
Para configurar la conexión de dispositivos, siga estos pasos:

1. Obtenga el puerto serie del dispositivo con la CLI de detección de dispositivos:

   ```bash
   devdisco list --usb
   ```

   Debería ver un resultado similar al siguiente y localizar el puerto COM USB de la placa de Arduino: ![Detección de dispositivos][device-discovery]

2. Abra el archivo `config.json` de la carpeta lesson y agregue el valor del número de puerto COM encontrado:

   ```json
   {
       "device_port" : "COM1"
   }
   ```
   ![config.json][config-json]
   > [!NOTE]
   > Para el puerto COM, en la plataforma Windows tiene el formato de `COM1, COM2, ...`. En macOS o Ubuntu, empieza por `/dev/`.

## <a name="deploy-and-run-the-sample-application"></a>Implementación y ejecución de la aplicación de ejemplo
### <a name="install-the-required-tools-for-your-arduino-board"></a>Instalación de las herramientas necesarias para la placa de Arduino

Instale el SDK de IoT Hub de Azure de su placa de Arduino ejecutando el comando siguiente:

```bash
gulp install-tools
```

Esta tarea podría tardar mucho tiempo en completarse; depende de la conexión de red.

> [!NOTE]
> Salga de la instancia en ejecución del IDE de Arduino cuando se ejecuten las tareas de gulp: `install-tools` y `run`.

### <a name="deploy-and-run-the-sample-app"></a>Implementación y ejecución de la aplicación de ejemplo
Implemente y ejecute la aplicación de ejemplo usando el comando siguiente:

```bash
gulp run

# You can monitor the serial port by running listen task:
gulp listen

# Or you can combine above two gulp tasks into one:
gulp run --listen
```

### <a name="verify-the-app-works"></a>Comprobación del funcionamiento de la aplicación
En caso contrario, consulte [esta guía][troubleshooting-page] para ver soluciones a problemas comunes.

![Intermitencia del LED][led-blinking]

## <a name="summary"></a>Resumen
Ha instalado las herramientas necesarias para usar la placa de Arduino e implementado una aplicación de ejemplo para que dicha placa haga parpadear el LED. Ahora puede crear, implementar y ejecutar otra aplicación de ejemplo que conecte la placa de Arduino a IoT Hub de Azure para enviar y recibir mensajes.

## <a name="next-steps"></a>Pasos siguientes
[Obtención de las herramientas de Azure][get-the-azure-tools]

<!-- Images and links -->

[troubleshooting-page]: iot-hub-adafruit-feather-m0-wifi-kit-arduino-troubleshooting.md
[configure-your-device]: iot-hub-adafruit-feather-m0-wifi-kit-arduino-lesson1-configure-your-device.md
[get-the-tools]: iot-hub-adafruit-feather-m0-wifi-kit-arduino-lesson1-get-the-tools-win32.md
[repo-structure]: media/iot-hub-adafruit-feather-m0-wifi-lessons/lesson1/vscode-blink-arduino-mac.png
[device-discovery]: media/iot-hub-adafruit-feather-m0-wifi-lessons/lesson1/device_discovery.png
[config-json]: media/iot-hub-adafruit-feather-m0-wifi-lessons/lesson1/vscode-config-mac.png
[led-blinking]: media/iot-hub-adafruit-feather-m0-wifi-lessons/lesson1/led_blinking.png
[get-the-azure-tools]: iot-hub-adafruit-feather-m0-wifi-kit-arduino-lesson2-get-azure-tools-win32.md
