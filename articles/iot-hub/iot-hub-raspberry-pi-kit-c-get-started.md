---
title: 'Raspberry Pi en la nube (C): Conectar Raspberry Pi a Azure IoT Hub | Microsoft Docs'
description: "Con este tutorial aprenderá a configurar y conectar Raspberry Pi a Azure IoT Hub para que envíe datos a la plataforma en la nube de Azure."
services: iot-hub
documentationcenter: 
author: shizn
manager: timlt
tags: 
keywords: "azure iot raspberry pi, raspberry pi iot hub, raspberry pi envía datos a la nube, raspberry pi a la nube"
ms.assetid: 68c0e730-1dc8-4e26-ac6b-573b217b302d
ms.service: iot-hub
ms.devlang: c
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 7/12/2017
ms.author: xshi
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 8b8fda17a8d1d1796d5299e3aba4b0fd5e719a4c
ms.sourcegitcommit: 02e69c4a9d17645633357fe3d46677c2ff22c85a
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 08/03/2017
---
# <a name="connect-raspberry-pi-to-azure-iot-hub-c"></a>Conectar Raspberry Pi a Azure IoT Hub (C)

[!INCLUDE [iot-hub-get-started-device-selector](../../includes/iot-hub-get-started-device-selector.md)]

En este tutorial, empezará por aprender los principios básicos del uso de Raspberry Pi que ejecuta Raspbian. A continuación, aprenderá a conectar sin problemas los dispositivos en la nube con [Azure IoT Hub](iot-hub-what-is-iot-hub.md). Para obtener ejemplos de Windows 10 IoT Core, vaya al [Centro de desarrollo de Windows](http://www.windowsondevices.com/).

¿Aún no tiene un kit? Pruebe el [simulador en línea de Rapsberry Pi](iot-hub-raspberry-pi-web-simulator-get-started.md). También puede comprar un nuevo kit [aquí](https://azure.microsoft.com/develop/iot/starter-kits).

## <a name="what-you-do"></a>Qué debe hacer

* Cree un Centro de IoT.
* Registre un dispositivo para Pi en IoT Hub.
* Configure Raspberry Pi.
* Ejecute una aplicación de ejemplo en Pi para enviar datos de sensor a IoT Hub.

Conecte Raspberry Pi al IoT Hub que ha creado. Luego ejecute una aplicación de ejemplo en Pi para recopilar datos de temperatura y humedad de un sensor BME280. Por último, envíe los datos del sensor a IoT Hub.

## <a name="what-you-learn"></a>Conocimientos que adquirirá

* Cómo crear Azure IoT Hub y obtener la cadena de conexión del nuevo dispositivo.
* Cómo conectar Pi con un sensor BME280.
* Cómo recopilar datos del sensor al ejecutar una aplicación de ejemplo en Pi.
* Cómo enviar los datos del sensor a IoT Hub.

## <a name="what-you-need"></a>Lo que necesita

![Lo que necesita](media/iot-hub-raspberry-pi-kit-c-get-started/0_starter_kit.jpg)

* La placa de Raspberry Pi 2 o Raspberry Pi 3.
* Una suscripción de Azure activa. Si no tiene ninguna cuenta de Azure, [cree una cuenta de evaluación gratuita de Azure](https://azure.microsoft.com/free/) en solo unos minutos.
* Un monitor, un teclado USB y un mouse que se conecten a Pi.
* Un equipo PC o Mac con Windows o Linux.
* Una conexión a Internet.
* Una tarjeta microSD de 16 GB o más.
* Un adaptador de USB a SD o una tarjeta microSD para grabar la imagen del sistema operativo en la tarjeta microSD.
* Una fuente de alimentación de 5 V y 2 A con un cable microUSB de 6 pies.

Los elementos siguientes son opcionales:

* Un sensor de temperatura, presión y humedad Adafruit BME280 ensamblado.
* La placa de pruebas.
* Cables de puente M/6 F.
* Un LED difuso de 10 mm.


> [!NOTE] 
Estos elementos son opcionales porque el ejemplo de código simula los datos del sensor.


[!INCLUDE [iot-hub-get-started-create-hub-and-device](../../includes/iot-hub-get-started-create-hub-and-device.md)]

## <a name="setup-raspberry-pi"></a>Configurar Raspberry Pi

### <a name="install-the-raspbian-operating-system-for-pi"></a>Instalar el sistema operativo Raspbian para Pi

Prepare la tarjeta microSD para instalar la imagen de Raspbian.

1. Descargue Raspbian.
   1. [Descargue Raspbian Jessie con el escritorio](https://www.raspberrypi.org/downloads/raspbian/) (el archivo .zip).
   1. Extraiga la imagen de Raspbian en una carpeta del equipo.
1. Instale Raspbian en la tarjeta microSD.
   1. [Descargue e instale la utilidad de grabadora de tarjetas SD Etcher](https://etcher.io/).
   1. Ejecute Etcher y seleccione la imagen de Raspbian que extrajo en el paso 1.
   1. Seleccione la unidad de la tarjeta microSD. Tenga en cuenta que es posible que Etcher ya haya seleccionado la unidad correcta.
   1. Haga clic en Flash para instalar Raspbian en la tarjeta microSD.
   1. Quite la tarjeta microSD del equipo cuando se complete la instalación. Es seguro quitar la tarjeta microSD directamente porque Etcher expulsa o desmonta la tarjeta microSD automáticamente al acabar.
   1. Inserte la tarjeta microSD en la Pi.

### <a name="enable-ssh-and-spi"></a>Habilitar SSH y SPI

1. Conecte Pi al monitor, el teclado y el mouse, inicie Pi y luego inicie sesión en Raspbian con `pi` como nombre de usuario y `raspberry` como contraseña.
1. Haga clic en el icono de Raspberry > **Preferencias** > **Configuración de Raspberry Pi**.

   ![Menú Preferencias de Raspbian](media/iot-hub-raspberry-pi-kit-c-get-started/1_raspbian-preferences-menu.png)

1. En la pestaña **Interfaces**, establezca **SPI** y **SSH** en **Habilitar** y luego haga clic en **Aceptar**. Si no tiene sensores físicos y desea usar datos de detección simulados, este paso es opcional.

   ![Habilitar SPI y SSH en Raspberry Pi](media/iot-hub-raspberry-pi-kit-c-get-started/2_enable-spi-ssh-on-raspberry-pi.png)

> [!NOTE] 
Para habilitar SSH y SPI, puede buscar más documentos de referencia en [raspberrypi.org](https://www.raspberrypi.org/documentation/remote-access/ssh/) y [RASPI-CONFIG](https://www.raspberrypi.org/documentation/configuration/raspi-config.md).

### <a name="connect-the-sensor-to-pi"></a>Conectar el sensor a Pi

Use la placa de pruebas y los cables de puente para conectar un LED y un BME280 a Pi como se indica a continuación. Si no tiene el sensor, [omita esta sección](#connect-pi-to-the-network).

![Conexión de Raspberry Pi y el sensor](media/iot-hub-raspberry-pi-kit-c-get-started/3_raspberry-pi-sensor-connection.png)

El sensor BME280 recopila datos sobre la temperatura y la humedad, y el LED parpadea si se produce comunicación entre el dispositivo y la nube. 

En las patillas del sensor, use el siguiente cableado:

| Inicio (sensor y LED)     | Fin (placa)            | Color de cable   |
| -----------------------  | ---------------------- | ------------: |
| LED VDD (patilla 5G)         | GPIO 4 (patilla 7)         | Cable blanco   |
| LED GND (patilla 6G)         | GND (patilla 6)            | Cable negro   |
| VDD (patilla 18F)            | Potencia 3,3 V (patilla 17)      | Cable blanco   |
| GND (patilla 20F)            | GND (patilla 20)           | Cable negro   |
| SCK (patilla 21F)            | SPI0 SCLK (patilla 23)     | Cable naranja  |
| SDO (patilla 22F)            | SPI0 MISO (patilla 21)     | Cable amarillo  |
| SDI (patilla 23F)            | SPI0 MOSI (patilla 19)     | Cable verde   |
| CS (patilla 24F)             | SPI0 CS (patilla 24)       | Cable azul    |

Haga clic para ver [Raspberry Pi 2 & 3 Pin mappings (Asignaciones de patillas de Raspberry Pi 2 y 3)](https://developer.microsoft.com/windows/iot/docs/pinmappingsrpi) para su referencia.

Una vez que haya conectado correctamente BME280 a Raspberry Pi, su aspecto debería ser el de la imagen siguiente.

![Pi y BME280 conectados](media/iot-hub-raspberry-pi-kit-c-get-started/4_connected-pi.jpg)

### <a name="connect-pi-to-the-network"></a>Conexión de Pi a la red

Encienda la Pi mediante un cable microUSB y la fuente de alimentación. Use el cable Ethernet para conectar Pi a la red cableada o siga las [instrucciones de Raspberry Pi Foundation](https://www.raspberrypi.org/learning/software-guide/wifi/) para conectar Pi a la red inalámbrica. Después de que Pi se ha conectado correctamente a la red, debe anotar la [dirección IP de su Pi](https://learn.adafruit.com/adafruits-raspberry-pi-lesson-3-network-setup/finding-your-pis-ip-address).

![Conectado a la red cableada](media/iot-hub-raspberry-pi-kit-c-get-started/5_power-on-pi.jpg)


## <a name="run-a-sample-application-on-pi"></a>Ejecutar una aplicación de ejemplo en Pi

### <a name="install-the-prerequisite-packages"></a>Instalar los paquetes de requisitos previos

1. Use uno de los siguientes clientes SSH del equipo host para conectar con Raspberry Pi.
   
   **Usuarios de Windows**
   1. Descargue e instale [PuTTY](http://www.putty.org/) para Windows. 
   1. Copie la dirección IP de Pi en la sección de nombre de host (o dirección IP) y seleccione SSH como el tipo de conexión.
   
   ![PuTTy](media/iot-hub-raspberry-pi-kit-node-get-started/7_putty-windows.png)
   
   **Usuarios de Mac y Ubuntu**
   
   Use el cliente de SSH integrado en Ubuntu o macOS. Quizás deba ejecutar `ssh pi@<ip address of pi>` para conectar Pi mediante SSH.
   > [!NOTE] 
   El nombre de usuario predeterminado es `pi` y la contraseña es `raspberry`.

1. Instale los paquetes de requisitos previos para el SDK de dispositivo IoT de Microsoft Azure para C y Cmake al ejecutar los comandos siguientes:

   ```bash
   grep -q -F 'deb http://ppa.launchpad.net/aziotsdklinux/ppa-azureiot/ubuntu vivid main' /etc/apt/sources.list || sudo sh -c "echo 'deb http://ppa.launchpad.net/aziotsdklinux/ppa-azureiot/ubuntu vivid main' >> /etc/apt/sources.list"
   grep -q -F 'deb-src http://ppa.launchpad.net/aziotsdklinux/ppa-azureiot/ubuntu vivid main' /etc/apt/sources.list || sudo sh -c "echo 'deb-src http://ppa.launchpad.net/aziotsdklinux/ppa-azureiot/ubuntu vivid main' >> /etc/apt/sources.list"
   sudo apt-key adv --keyserver keyserver.ubuntu.com --recv-keys FDA6A393E4C2257F
   sudo apt-get update
   sudo apt-get install -y azure-iot-sdk-c-dev cmake libcurl4-openssl-dev git-core
   git clone git://git.drogon.net/wiringPi
   cd ./wiringPi
   ./build
   ```


### <a name="configure-the-sample-application"></a>Configurar la aplicación de ejemplo

1. Clone la aplicación de ejemplo mediante el comando siguiente:

   ```bash
   git clone https://github.com/Azure-Samples/iot-hub-c-raspberrypi-client-app
   ```
1. Abra el archivo config mediante la ejecución de los comandos siguientes:

   ```bash
   cd iot-hub-c-raspberrypi-client-app
   nano config.h
   ```

   ![Archivo config](media/iot-hub-raspberry-pi-kit-c-get-started/6_config-file.png)

   Hay dos macros en este archivo que se pueden configurar. La primera es `INTERVAL`, que define el intervalo de tiempo (en milisegundos) entre dos mensajes que se envían a la nube. La segunda es `SIMULATED_DATA`, un valor booleano que indica si se usan los datos de sensor simulados o no.

   Si **no tiene el sensor**, establezca el valor `SIMULATED_DATA` en `1` para que la aplicación de ejemplo cree y use datos de sensor simulados.

1. Guarde y salga al presionar Control-O > Entrar > Control-X.

### <a name="build-and-run-the-sample-application"></a>Compilar y ejecutar la aplicación de ejemplo

1. Compile la aplicación de ejemplo al ejecutar el comando siguiente:

   ```bash
   cmake . && make
   ```
   ![Resultado de la compilación](media/iot-hub-raspberry-pi-kit-c-get-started/7_build-output.png)

1. Ejecute la aplicación de ejemplo mediante el comando siguiente:

   ```bash
   sudo ./app '<DEVICE CONNECTION STRING>'
   ```

   > [!NOTE] 
   Asegúrese de que copia y pega la cadena de conexión del dispositivo entre las comillas simples.


Debería ver el resultado siguiente, que muestra los datos del sensor y los mensajes que se envían a IoT Hub.

![Resultado: datos de sensor enviados desde Raspberry Pi a IoT Hub](media/iot-hub-raspberry-pi-kit-c-get-started/8_run-output.png)

## <a name="next-steps"></a>Pasos siguientes

Ha ejecutado una aplicación de ejemplo para recopilar datos de sensor y enviarlos a IoT Hub. Para ver los mensajes que ha enviado Raspberry Pi a IoT Hub o para enviar mensajes a Raspberry Pi en una interfaz de la línea de comandos, consulte el tutorial [Administración de los mensajes de dispositivo con iothub-explorer](https://docs.microsoft.com/en-gb/azure/iot-hub/iot-hub-explorer-cloud-device-messaging).

[!INCLUDE [iot-hub-get-started-next-steps](../../includes/iot-hub-get-started-next-steps.md)]
