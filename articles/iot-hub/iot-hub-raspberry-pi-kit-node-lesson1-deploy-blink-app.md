---
featureFlags:
- usabilla
title: "Conexión de Raspberry Pi (Node) a Azure IoT: Lección 1: Implementación de la aplicación | Microsoft Docs"
description: "Clone la aplicación Node.js de ejemplo de GitHub y use Gulp para implementar esta aplicación en la placa de Raspberry Pi 3. Esta aplicación de ejemplo hace parpadear el LED conectado a la placa cada dos segundos."
services: iot-hub
documentationcenter: 
author: shizn
manager: timlt
tags: 
keywords: intermitencia de led de raspberry pi, led intermitente con raspberry pi
ROBOTS: NOINDEX
redirect_url: /azure/iot-hub/iot-hub-raspberry-pi-kit-node-get-started
ms.assetid: a5a03a57-fe86-416f-90ff-6eca17775842
ms.service: iot-hub
ms.devlang: node
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 3/21/2017
ms.author: xshi
translationtype: Human Translation
ms.sourcegitcommit: 64e69df256404e98f6175f77357500b562d74318
ms.openlocfilehash: 90ceb432bd9506dc40e340af21a3cae4e21a20b0
ms.lasthandoff: 01/24/2017


---
# <a name="create-and-deploy-the-blink-application"></a>Creación e implementación de la aplicación de intermitencia
## <a name="what-you-will-do"></a>Lo que hará
Clone la aplicación de Node.js de ejemplo de GitHub y use la herramienta Gulp para implementar la aplicación de ejemplo en Raspberry Pi 3. La aplicación de ejemplo hace parpadear el LED conectado a la placa cada dos segundos. Si tiene problemas, busque soluciones en la [página de solución de problemas](iot-hub-raspberry-pi-kit-node-troubleshooting.md).

## <a name="what-you-will-learn"></a>Lo qué aprenderá
En este artículo, aprenderá lo siguiente:

* Cómo utilizar la herramienta `device-discover-cli` para recuperar la información de red de Pi
* Cómo implementar y ejecutar la aplicación de ejemplo en Pi
* Cómo implementar y depurar aplicaciones que se ejecutan de forma remota en Pi

## <a name="what-you-need"></a>Lo que necesita
Debe haber completado correctamente las siguientes operaciones:

* [Configuración del dispositivo](iot-hub-raspberry-pi-kit-node-lesson1-configure-your-device.md)
* [Obtener las herramientas](iot-hub-raspberry-pi-kit-node-lesson1-get-the-tools-win32.md)

## <a name="obtain-the-ip-address-and-host-name-of-pi"></a>Obtención de la dirección IP y el nombre de host de Pi
Abra un símbolo del sistema en Windows o una ventana de terminal de Mac OS o Ubuntu y, después, ejecute el siguiente comando:

```bash
devdisco list --eth
```

Debería ver una salida similar a la siguiente:

![Detección de dispositivos](media/iot-hub-raspberry-pi-lessons/lesson1/device_discovery.png)

Tome nota de los valores de `IP address` y `hostname` de Pi. Necesitará esta información posteriormente en este artículo.

> [!NOTE]
> Asegúrese de que la Pi se conecta a la misma red que el equipo. Por ejemplo, si el equipo está conectado a una red inalámbrica mientras Pi está conectada a una red cableada, es posible que no vea la dirección IP en la salida de devdisco.

## <a name="clone-the-sample-application"></a>Clonación de la aplicación de ejemplo
Para ejecutar el código de ejemplo, siga estos pasos:

1. Clone el repositorio de ejemplo de GitHub ejecutando el comando siguiente:
   
   ```bash
   git clone https://github.com/Azure-Samples/iot-hub-node-raspberrypi-getting-started.git
   ```
2. Abra la aplicación de ejemplo en Visual Studio Code ejecutando los comandos siguientes:
   
   ```bash
   cd iot-hub-node-raspberrypi-getting-started
   cd Lesson1
   code .
   ```

![Estructura del repositorio](media/iot-hub-raspberry-pi-lessons/lesson1/vscode-blink-mac.png)

El archivo `app.js` de la subcarpeta `app` es el archivo de origen de la clave que contiene el código para controlar el LED.

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
   
   El archivo de configuración `config-raspberrypi.json` contiene las credenciales de usuario que use para iniciar sesión en Pi. Para evitar la pérdida de las credenciales de usuario, se genera el archivo de configuración en la subcarpeta `.iot-hub-getting-started` de la carpeta principal del equipo.

2. Abra el archivo de configuración de dispositivos en Visual Studio Code ejecutando el comando siguiente:
   
   ```bash
   # For Windows command prompt
   code %USERPROFILE%\.iot-hub-getting-started\config-raspberrypi.json
   
   # For macOS or Ubuntu
   code ~/.iot-hub-getting-started/config-raspberrypi.json
   ```
   
3. Reemplace el marcador de posición `[device hostname or IP address]` con la dirección IP o el nombre de host que obtuvo anteriormente en "Obtención de la dirección IP y el nombre de host de Pi".
   
   ![Config.json](media/iot-hub-raspberry-pi-lessons/lesson1/vscode-config-mac.png)

> [!NOTE]
> Puede usar la clave SSH en lugar del nombre de usuario y la contraseña al conectarse a Raspberry Pi. Para ello, se debe generar la clave mediante **ssh-keygen** y **ssh-copy-id pi@\<dirección del dispositivo\>**.
>
> En Windows, estos comandos están disponibles en **Git Bash**.
>
> En Mac OS, debe ejecutar **brew install ssh-copy-id**.
>
> Después de cargar correctamente la clave en Raspberry Pi, reemplace **device_password** por la propiedad **device_key_path** en **config-raspberrypi.json**.
>
> Las líneas actualizadas deben presentar el siguiente aspecto:
> ```javascript
> "device_user_name": "pi",
> "device_key_path": "id_rsa",
> ```

¡Enhorabuena! Ha creado correctamente la primera aplicación de ejemplo para Pi.

## <a name="deploy-and-run-the-sample-application"></a>Implementación y ejecución de la aplicación de ejemplo
### <a name="install-nodejs-and-npm-on-pi"></a>Instalación de Node.js y NPM en Pi
Instale Node.js y NPM en Pi ejecutando el comando siguiente:

```bash
gulp install-tools
```

Esta tarea podría tardar 10 minutos en completarse la primera vez que la ejecute.

### <a name="deploy-and-run-the-sample-app"></a>Implementación y ejecución de la aplicación de ejemplo
Implemente y ejecute la aplicación de ejemplo usando el comando siguiente:

```bash
gulp deploy && gulp run
```

### <a name="verify-the-app-works"></a>Comprobación del funcionamiento de la aplicación
Ahora debería ver que el LED de Pi parpadea cada dos segundos.  En caso contrario, consulte la [guía de solución de problemas](iot-hub-raspberry-pi-kit-node-troubleshooting.md) para ver soluciones a problemas comunes.
![Intermitencia del LED](media/iot-hub-raspberry-pi-lessons/lesson1/led_blinking.jpg)

## <a name="summary"></a>Resumen
Ha instalado las herramientas necesarias para usar Pi e implementado una aplicación de ejemplo para que Pi haga parpadear el LED. Ahora puede crear, implementar y ejecutar otra aplicación de ejemplo que conecte Pi a Azure IoT Hub para enviar y recibir mensajes.

## <a name="next-steps"></a>Pasos siguientes
[Obtención de las herramientas de Azure](iot-hub-raspberry-pi-kit-node-lesson2-get-azure-tools-win32.md)


