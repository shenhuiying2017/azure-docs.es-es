---
title: "Conexión de Arduino (C) a Azure IoT - Lección 1: Configuración del dispositivo | Microsoft Docs"
description: Configure Adafruit Feather M0 WiFi por primera vez.
services: iot-hub
documentationcenter: 
author: shizn
manager: timtl
tags: 
keywords: "configuración de Arduino, conexión de Arduino al equipo, configuración de Arduino, placa Arduino"
ms.assetid: f5b334f0-a148-41aa-b374-ce7b9f5b305a
ms.service: iot-hub
ms.devlang: arduino
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/13/2016
ms.author: xshi
translationtype: Human Translation
ms.sourcegitcommit: 64e69df256404e98f6175f77357500b562d74318
ms.openlocfilehash: 3789c07cd6142ba5b6a58a67813c0ead6b87bd76


---
# <a name="configure-your-device"></a>Configuración del dispositivo
## <a name="what-you-will-do"></a>Lo que hará
Ensamble la placa y enciéndala para configurar la placa Arduino de Adafruit Feather M0 WiFi por primera vez. Si tiene problemas, busque soluciones en la [página de solución de problemas](iot-hub-adafruit-feather-m0-wifi-kit-arduino-troubleshooting.md).

## <a name="what-you-need"></a>Lo que necesita
Para completar esta operación, necesita las siguientes piezas del kit de inicio de Adafruit Feather M0 WiFi:

* La placa Adafruit Feather M0 WiFi
* Un cable USB Micro B a Tipo A.

![USB Micro B a Tipo A][kit]

También necesita lo siguiente:

* Un equipo con Windows, Mac o Linux.
* Una conexión inalámbrica para que la placa Arduino se conecte a los siguientes elementos.
* Una conexión a Internet para descargar la herramienta de configuración.

## <a name="what-you-will-learn"></a>Lo qué aprenderá
En este artículo, aprenderá lo siguiente:

* Cómo ensamblar la placa Arduino y encenderla en las lecciones siguientes.
* Cómo agregar permisos de puerto serie en Ubuntu.

## <a name="connect-your-arduino-board-to-your-computer"></a>Conexión de la placa Arduino a su equipo

1. Conecte el cable microUSB al puerto microUSB superior.

   ![Puerto microUSB superior][top-micro-usb-port]

2. Conecte el otro extremo del cable USB al equipo.

   ![Cable USB conectado a un equipo][computer-usb]

## <a name="add-serial-port-permissions-on-ubuntu"></a>Adición de permisos de puerto serie en Ubuntu

Puede omitir esta sección si utiliza Windows o Mac OS. Para Ubuntu, necesita realizar los siguientes pasos para asegurarse de que el usuario de Linux normal tiene los permisos para que funcione en el puerto USB de su placa Arduino.

1. Ahora como usuario normal del terminal:

   ```bash
   ls -l /dev/ttyUSB*
   # Or
   ls -l /dev/ttyACM*
   ```

   Verá algo parecido a lo siguiente:

   ```bash
   crw-rw---- 1 root uucp 188, 0 5 apr 23.01 ttyUSB0
   # Or
   crw-rw---- 1 root dialout 188, 0 5 apr 23.01 ttyACM0
   ```

   "0" puede ser un número diferente o se podrían devolver varias entradas. En el primer caso, los datos que necesitamos son `uucp`, en el segundo son `dialout`, que es el propietario del grupo del archivo.

2. Adición del usuario al grupo:

   ```bash
   sudo usermod -a -G group-name username
   ```

   Donde `group-name` son los datos encontrados en el primer paso, y `username` es el nombre de usuario de Linux.

3. Tiene que cerrar sesión y volver a iniciarla para que este cambio surta efecto y se complete la instalación.

## <a name="summary"></a>Resumen
En este artículo, ha aprendido cómo configurar la placa Arduino. En la siguiente tarea, instalará las herramientas y el software necesarios como preparativo para ejecutar una aplicación de ejemplo en la placa Arduino.

![El hardware está listo.][hardware-is-ready]

## <a name="next-steps"></a>Pasos siguientes
[Obtención de las herramientas][get-the-tools]
<!-- Images and links -->

[kit]: media/iot-hub-adafruit-feather-m0-wifi-lessons/lesson1/kit.png
[top-micro-usb-port]: media/iot-hub-adafruit-feather-m0-wifi-lessons/lesson1/top_usbport.jpg
[computer-usb]: media/iot-hub-adafruit-feather-m0-wifi-lessons/lesson1/computer_usb.jpg
[hardware-is-ready]: media/iot-hub-adafruit-feather-m0-wifi-lessons/lesson1/hardware_ready.jpg
[get-the-tools]: iot-hub-adafruit-feather-m0-wifi-kit-arduino-lesson1-get-the-tools-win32.md


<!--HONumber=Jan17_HO4-->


