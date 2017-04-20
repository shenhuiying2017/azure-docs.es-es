---
title: "Conexión de Raspberry Pi (Node) a Azure IoT: Lección 1: Configuración del dispositivo | Microsoft Docs"
description: Configure Raspberry Pi 3 for first-time use and install the Raspbian OS, a free operating system that is optimized for the Raspberry Pi hardware.
services: iot-hub
documentationcenter: 
author: shizn
manager: timlt
tags: 
keywords: "instalación de raspbian, descarga de raspbian, cómo instalar raspbian, configuración de raspbian, instalar raspbian para raspberry pi, instalar so para raspberry pi, instalar tarjeta sd para raspberry pi, conexión de raspberry pi, conectar a raspberry pi, conectividad de raspberry pi"
ROBOTS: NOINDEX
redirect_url: /azure/iot-hub/iot-hub-raspberry-pi-kit-node-get-started
ms.assetid: 43f7c2cf-f1a5-4dd5-93f0-7e546c6dc91e
ms.service: iot-hub
ms.devlang: node
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 3/21/2017
ms.author: xshi
translationtype: Human Translation
ms.sourcegitcommit: 5b80d471c78973f8c2f6b1ad4e1240c66f0505ef
ms.openlocfilehash: 5afe1110944641daaeace5ef02b2c9fa8d2a8e05
ms.lasthandoff: 02/21/2017


---
# <a name="configure-your-device"></a>Configuración del dispositivo
## <a name="what-you-will-do"></a>Lo que hará
Configure la Pi para utilizarla por primera vez e instale el sistema operativo Raspbian. Raspbian es un sistema operativo gratuito que está optimizado para el hardware de Raspberry Pi. Si tiene problemas, puede encontrar soluciones en la [página de solución de problemas](iot-hub-raspberry-pi-kit-node-troubleshooting.md).

## <a name="what-you-will-learn"></a>Lo qué aprenderá
En este artículo, aprenderá lo siguiente:

* Cómo instalar Raspbian en la Pi
* Cómo encender la Pi mediante un cable USB
* Cómo conectar la Pi a la red mediante un cable Ethernet o una red inalámbrica
* Cómo agregar un LED a la placa de pruebas y conectarlo a la Pi

## <a name="what-you-will-need"></a>Qué necesita
Para completar esta operación, necesita las siguientes piezas del kit de inicio de Raspberry Pi 3:

* La placa Raspberry Pi 3
* La tarjeta microSD de 16 GB
* La fuente de alimentación de 5 V y 2 A con un cable microUSB de 6 pies
* La placa de pruebas
* Los cables conectores
* Una resistencia de 560 Ohm
* Un LED difuso de 10 mm
* El cable Ethernet

![Los elementos del kit de inicio](media/iot-hub-raspberry-pi-lessons/lesson1/starter_kit.jpg)

También necesita lo siguiente:

* Una conexión cableada o inalámbrica para que la Pi se conecte a los siguientes elementos.
* Un adaptador de USB a SD o una tarjeta miniSD para grabar la imagen del SO en la tarjeta microSD.
* Un equipo con Windows, Mac o Linux. El equipo se usa para instalar Raspbian en la tarjeta microSD.
* Una conexión a Internet para descargar las herramientas y el software necesarios.

## <a name="install-raspbian-on-the-microsd-card"></a>Instalación de Raspbian en la tarjeta microSD
Prepare la tarjeta microSD para instalar la imagen de Raspbian.

1. Descargue Raspbian.
   1. [Descargue](https://www.raspberrypi.org/downloads/raspbian/) el archivo .zip para Raspbian Jessie con Pixel.
   2. Extraiga la imagen de Raspbian en una carpeta del equipo.
2. Instale Raspbian en la tarjeta microSD.
   1. [Descargue](https://www.etcher.io) e instale la utilidad de grabadora de tarjetas SD Etcher.
   2. Ejecute Etcher y seleccione la imagen de Raspbian que extrajo en el paso 1.
   3. Seleccione la unidad de la tarjeta microSD.
      Tenga en cuenta que es posible que Etcher ya haya seleccionado la unidad correcta.
   4. Haga clic en **Flash** para instalar Raspbian en la tarjeta microSD.
   5. Quite la tarjeta microSD del equipo cuando se complete la instalación.
      Es seguro quitar la tarjeta microSD directamente porque Etcher expulsa o desmonta la tarjeta microSD automáticamente al acabar.
   6. Inserte la tarjeta microSD en la Pi.

![Inserción de la tarjeta SD](media/iot-hub-raspberry-pi-lessons/lesson1/insert_sdcard.jpg)

## <a name="turn-on-pi"></a>Encendido de la Pi
Encienda la Pi mediante un cable microUSB y la fuente de alimentación.

![Encendido](media/iot-hub-raspberry-pi-lessons/lesson1/micro_usb_power_on.jpg)

> [!NOTE]
> Es importante utilizar la fuente de alimentación del kit, que tiene al menos 2 A, para asegurarse de que el dispositivo Raspberry cuente con la energía suficiente como para funcionar correctamente.

## <a name="enable-ssh"></a>Habilite SSH
A partir de la versión de noviembre de 2016, Raspbian tiene el servidor SSH deshabilitado de forma predeterminada. Debe habilitarlo manualmente. Puede consultar las [instrucciones oficiales](https://www.raspberrypi.org/documentation/remote-access/ssh/) o conectar un monitor e ir a **Preferences -> Raspberry Pi Configuration** (Preferencias -> Configuración de Raspberry Pi) para habilitar SSH.

## <a name="connect-raspberry-pi-3-to-the-network"></a>Conexión de Raspberry Pi 3 a la red
Puede conectar la Pi a una red cableada o a una red inalámbrica. Asegúrese de que la Pi se conecta a la misma red que el equipo. Por ejemplo, puede conectar el dispositivo al mismo conmutador al que está conectado su equipo.

### <a name="connect-to-a-wired-network"></a>Conexión a una red cableada
Utilice el cable Ethernet para conectar la Pi a la red cableada. Los dos LED de la Pi se encienden si se establece la conexión.

![Conexión mediante un cable Ethernet](media/iot-hub-raspberry-pi-lessons/lesson1/connect_ethernet.jpg)

### <a name="connect-to-a-wireless-network"></a>Conexión a una red inalámbrica
Siga las [instrucciones](https://www.raspberrypi.org/learning/software-guide/wifi/) de Raspberry Pi Foundation para conectar la Pi a la red inalámbrica. Estas instrucciones requieren que primero conecte un monitor y un teclado a la Pi.

## <a name="connect-the-led-to-pi"></a>Conexión del LED a la Pi
Para completar esta tarea, utilice la [placa de pruebas](https://learn.sparkfun.com/tutorials/how-to-use-a-breadboard), los cables conectores, el LED y la resistencia. Conecte estas piezas a los puertos de [entrada y salida de uso general](https://www.raspberrypi.org/documentation/usage/gpio/) (GPIO) de la Pi.

![Placa de pruebas, LED y resistencia](media/iot-hub-raspberry-pi-lessons/lesson1/breadboard_led_resistor.jpg)

1. Conecte la pata más corta del LED a la **GND de los GPIO (pin 6)**.
2. Conecte la pata más larga del LED a una de la resistencia.
3. Conecte la otra pata de la resistencia a los **GPIO 4 (pin 7)**.

Tenga en cuenta que la polaridad LED es importante. Esta opción de polaridad comúnmente se conoce como "bajo activo".

![Diagrama de asignación de pines](media/iot-hub-raspberry-pi-lessons/lesson1/pinout_breadboard.png)

¡Enhorabuena! Ha configurado correctamente la Pi.

## <a name="summary"></a>Resumen
En este artículo, ha aprendido a configurar la Pi instalando Raspbian, conectándola a una red y agregando un LED a la Pi. Tenga en cuenta que todavía el LED no se encenderá. En la siguiente tarea, instalará las herramientas y el software necesarios como preparativo para ejecutar una aplicación de ejemplo en la Pi.

![El hardware está listo.](media/iot-hub-raspberry-pi-lessons/lesson1/hardware_ready.jpg)

## <a name="next-steps"></a>Pasos siguientes
[Obtener las herramientas](iot-hub-raspberry-pi-kit-node-lesson1-get-the-tools-win32.md)


