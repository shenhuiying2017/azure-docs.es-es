---
title: 'M0 en la nube: Conectar Feather M0 WiFi a Azure IoT Hub | Microsoft Docs'
description: "Se explica cómo conectar un dispositivo Arduino, denominado Adafruit Feather M0 WiFi, a Azure IoT Hub, que es un servicio en la nube de Microsoft que ayuda a administrar los recursos de IoT."
services: iot-hub
documentationcenter: 
author: shizn
manager: timtl
tags: 
keywords: 
ms.assetid: 51befcdb-332b-416f-a6a1-8aabdb67f283
ms.service: iot-hub
ms.devlang: arduino
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 6/17/2017
ms.author: xshi
ms.translationtype: Human Translation
ms.sourcegitcommit: 857267f46f6a2d545fc402ebf3a12f21c62ecd21
ms.openlocfilehash: 2f770a8f088754e63aec40d3f670f6ae0543d6e0
ms.contentlocale: es-es
ms.lasthandoff: 06/28/2017


---

# <a name="connect-adafruit-feather-m0-wifi-to-azure-iot-hub-in-the-cloud"></a>Conectar Adafruit Feather M0 WiFi a Azure IoT Hub en la nube
[!INCLUDE [iot-hub-get-started-device-selector](../../includes/iot-hub-get-started-device-selector.md)]

![Conexión entre BME280, Feather M0 WiFi e IoT Hub](media/iot-hub-adafruit-feather-m0-wifi-get-started/1_connection-m0-feather-m0-iot-hub.png)

En este tutorial, empiece por aprender los conceptos básicos sobre cómo trabajar con la placa Arduino. A continuación, aprenderá a conectar sin problemas los dispositivos en la nube con [Azure IoT Hub](iot-hub-what-is-iot-hub.md).

## <a name="what-you-do"></a>Qué debe hacer

Conecte Adafruit Feather M0 WiFi a un IoT Hub que cree. Luego ejecute una aplicación de ejemplo en M0 WiFi para recopilar datos de temperatura y humedad de un BME280. Por último, envíe los datos del sensor a IoT Hub.



## <a name="what-you-learn"></a>Conocimientos que adquirirá

* Cómo crear un IoT Hub y registrar un dispositivo en Feather M0 WiFi
* Cómo conectar Feather M0 WiFi con el sensor y el equipo
* Cómo recopilar datos del sensor al ejecutar una aplicación de ejemplo en Feather M0 WiFi
* Cómo enviar los datos del sensor a IoT Hub

## <a name="what-you-need"></a>Lo que necesita

![Elementos necesarios para el tutorial](media/iot-hub-adafruit-feather-m0-wifi-get-started/2_parts-needed-for-the-tutorial.png)

Para realizar esta operación, necesita los siguientes elementos del kit de inicio de Feather M0 WiFi:

* La placa Feather M0 WiFi
* Un cable micro USB a USB tipo A

También necesitará lo siguiente para el entorno de desarrollo:

* Una suscripción de Azure activa. Si no tiene ninguna cuenta de Azure, [cree una cuenta de evaluación gratuita de Azure](https://azure.microsoft.com/free/) en solo unos minutos.
* Un equipo PC o Mac que ejecute Windows o Ubuntu.
* Red inalámbrica a la que se conectará Feather M0 WiFi.
* Conexión a Internet para descargar la herramienta de configuración.
* [IDE de Arduino](https://www.arduino.cc/en/main/software) versión 1.6.8 o posterior. Las versiones anteriores no funcionan con la biblioteca de AzureIoT.


Los elementos siguientes son opcionales en caso de que no tenga un sensor. También tiene la opción de usar una simulación de datos del sensor.

* Un sensor de temperatura y humedad BME280
* Una placa de pruebas
* Cables de puente M/M

[!INCLUDE [iot-hub-get-started-create-hub-and-device](../../includes/iot-hub-get-started-create-hub-and-device.md)]

## <a name="connect-feather-m0-wifi-with-the-sensor-and-your-computer"></a>Conectar Feather M0 WiFi con el sensor y el equipo
En esta sección se conectan los sensores a la placa. Luego se conecta el dispositivo al equipo para su uso posterior.
### <a name="connect-a-dht22-temperature-and-humidity-sensor-to-feather-m0-wifi"></a>Conectar un sensor de humedad y temperatura DHT22 a Feather M0 WiFi

Use la placa de pruebas y los cables de puente para realizar la conexión del modo siguiente. Si no dispone de un sensor, omita esta sección porque en su lugar puede usar una simulación de datos del sensor.

![Referencia de conexiones](media/iot-hub-adafruit-feather-m0-wifi-get-started/3_connections_on_breadboard.png)


En las patillas del sensor, use el siguiente cableado:


| Inicio (sensor)           | Fin (placa)            | Color de cable   |
| -----------------------  | ---------------------- | ------------: |
| VDD (patilla 27A)            | 3V (patilla 3A)            | Cable rojo     |
| GND (patilla 29A)            | GND (patilla 6A)           | Cable negro   |
| SCK (patilla 30A)            | SCK (patilla 12A)          | Cable amarillo  |
| SDO (patilla 31A)            | MI (patilla 14A)           | Cable blanco   |
| SDI (patilla 32A)            | M0 (patilla 13A)           | Cable azul    |
| CS (patilla 33A)             | GPIO 5 (patilla 15J)       | Cable naranja  |

Para más información, vea [Adafruit BME280 Humidity + Barometric Pressure + Temperature Sensor Breakout (Desglose del sensor de humedad, presión y temperatura Adafruit BME280)](https://learn.adafruit.com/adafruit-bme280-humidity-barometric-pressure-temperature-sensor-breakout/wiring-and-test?view=all) y [Adafruit Feather M0 WiFi Pinouts (Conexiones de Adafruit Feather M0 WiFi)](https://learn.adafruit.com/adafruit-feather-m0-wifi-atwinc1500/pinouts).



Ahora Feather M0 WiFi debe estar conectado con un sensor en funcionamiento.

![Conectar DHT22 con Feather Huzzah](media/iot-hub-adafruit-feather-m0-wifi-get-started/4_connect-bme280-feather-m0-wifi.png)

### <a name="connect-feather-m0-wifi-to-your-computer"></a>Conectar Feather M0 WiFi al equipo

Como se muestra a continuación, use el cable micro USB a USB tipo A para conectar Feather M0 WiFi al equipo.

![Conectar Feather Huzzah al equipo](media/iot-hub-adafruit-feather-m0-wifi-get-started/5_connect-feather-m0-wifi-computer.png)

### <a name="add-serial-port-permissions-ubuntu-only"></a>Agregar permisos de puerto serie (solo Ubuntu)

Si usa Ubuntu, asegúrese de que tiene los permisos necesarios para trabajar en el puerto USB de Feather M0 WiFi. Para agregar permisos de puerto serie, siga estos pasos:


1. Ejecute los siguientes comandos en un terminal:

   ```bash
   ls -l /dev/ttyUSB*
   ls -l /dev/ttyACM*
   ```

   Obtendrá una de las siguientes salidas:

   * crw-rw---- 1 root uucp xxxxxxxx
   * crw-rw---- 1 root dialout xxxxxxxx

   En la salida, observe que `uucp` o `dialout` es el nombre del propietario del grupo del puerto USB.

1. Agregue el usuario al grupo mediante la ejecución del comando siguiente:

   ```bash
   sudo usermod -a -G <group-owner-name> <username>
   ```

   `<group-owner-name>` es el nombre del propietario del grupo que obtuvo en el paso anterior. `<username>` es el nombre de usuario de Ubuntu.

1. Cierre la sesión de Ubuntu y luego vuelva a iniciar sesión para que aparezca el cambio.

## <a name="collect-sensor-data-and-send-it-to-your-iot-hub"></a>Recopilación de datos del sensor y envío al centro de IoT

En esta sección, implementará y ejecutará una aplicación de ejemplo en Feather M0 WiFi. La aplicación de ejemplo hace parpadear el LED en Feather M0 WiFi y envía los datos de humedad y temperatura recopilados del sensor BME280 a IoT Hub.

### <a name="get-the-sample-application-from-github-and-prepare-arduino-ide"></a>Obtener la aplicación de ejemplo de Github y preparar el IDE de Arduino

La aplicación de ejemplo se hospeda en GitHub. Clone el repositorio de ejemplos que contiene la aplicación de ejemplo de GitHub. Para clonar el repositorio de ejemplos, siga estos pasos:

1. Abra un símbolo del sistema o una ventana de terminal.
1. Vaya a la carpeta donde desea almacenar la aplicación de ejemplo.
1. Ejecute el siguiente comando:

   ```bash
   git clone https://github.com/Azure-Samples/iot-hub-Feather-M0-WiFi-client-app.git
   ```

Instale el paquete de Feather M0 WiFi en el IDE de Arduino:

1. Abra la carpeta donde se almacena la aplicación de ejemplo.
1. Abra el archivo app.ino en la carpeta de la aplicación en el IDE de Arduino.

   ![Abrir la aplicación de ejemplo en el IDE de Arduino](media/iot-hub-adafruit-feather-m0-wifi-get-started/6_arduino-ide-open-sample-app.png)

1. Haga clic en **Tools** >  (Herramientas) **Board** >  (Placa) **Boards Manager** (Administrador de placas) y luego instale `Arduino SAMD Boards` versión `1.6.2` o posterior. Luego, instale el paquete `Adafruit SAMD` para agregar las definiciones de archivo de la placa.

   Boards Manager indica que está instalado `Arduino SAMD Boards` con la versión `1.6.2` o posterior. 

   ![Se instala el paquete esp8266](media/iot-hub-adafruit-feather-m0-wifi-get-started/7_arduino-ide-package-url.png)

1. Haga clic en **Tools** >  (Herramientas) **Board** >  (Placa) **Adafruit M0 WiFi**.

1. Instalar los controladores (solo Windows): Cuando conecte Feather, posiblemente tenga que instalar un controlador. Haga clic [aquí](https://github.com/adafruit/Adafruit_Windows_Drivers/releases/download/1.1/adafruit_drivers.exe) para descargar el instalador del controlador.
   Siga los pasos para instalar los controladores que quiera instalar.

### <a name="install-necessary-libraries"></a>Instalación de las bibliotecas necesarias

1. En Arduino IDE, haga clic en **Sketch** >  (Boceto) **Include Library** >  (Incluir biblioteca) **Manage Libraries** (Administrar bibliotecas).
1. Busque los nombres de biblioteca siguientes uno a uno. Para cada biblioteca que encuentre, haga clic en **Install** (Instalar).
   * `RTCZero`
   * `NTPClient`
   * `AzureIoTHub`
   * `AzureIoTUtility`
   * `AzureIoTProtocol_HTTP`
   * `ArduinoJson`
   * `Adafruit BME280 Library`
   * `Adafruit Unified Sensor`
1. Instale manualmente `Adafruit_WINC1500`. Visite [este vínculo](https://github.com/adafruit/Adafruit_WINC1500) y haga clic en el botón **Clone or download** (Clonar o descargar) y, luego **descargue el archivo ZIP**. Luego, en el IDE de Arduino, vaya a **Sketch (Boceto)** -> **Include Library (Incluir biblioteca)** -> **Add .zip Library (Incluir biblioteca de archivos ZIP)** y agregue el archivo ZIP que acaba de descargar.

### <a name="dont-have-a-real-bme280-sensor"></a>¿No tiene un sensor BME280 real?

La aplicación de ejemplo puede simular datos de humedad y temperatura en caso de que no disponga de un sensor BME280 real. Para configurar la aplicación de ejemplo de modo que use datos simulados, siga estos pasos:

1. Abra el archivo `config.h` de la carpeta `app`.
1. Busque la siguiente línea de código y cambie el valor de `false` a `true`:
   ```c
   define SIMULATED_DATA true
   ```
   ![Configurar la aplicación de ejemplo para usar datos simulados](media/iot-hub-adafruit-feather-m0-wifi-get-started/8_arduino-ide-configure-app-use-simulated-data.png)

1. Guarde el archivo como `Control-s`.

### <a name="deploy-the-sample-application-to-feather-m0-wifi"></a>Implementar la aplicación de ejemplo en Feather M0 WiFi

1. En el IDE de Arduino, haga clic en **Tool** (Herramienta)  > **Port** (Puerto) y luego en el puerto serie de Feather M0 WiFi.
1. Haga clic en **Sketch** (Boceto)  > **Upload** (Cargar) para compilar e implementar la aplicación de ejemplo en Feather M0 WiFi.

### <a name="enter-your-credentials"></a>Escriba sus credenciales.

Cuando la carga finalice correctamente, siga estos pasos para escribir las credenciales:

1. En Arduino IDE, haga clic en **Tools** >  (Herramientas) **Serial Monitor** (Monitor serie).
1. En la ventana del monitor serie, observe las dos listas desplegables de la esquina inferior derecha.
1. Seleccione **No line ending** (Sin final de línea) para la lista desplegable de la izquierda.
1. Seleccione **115200 baud** (115200 baudios) para la lista desplegable derecha.
1. En el cuadro de entrada ubicado en la parte superior de la ventana del monitor serie, escriba la siguiente información si se le solicita y luego haga clic en **Send** (Enviar).
   * SSID de Wi-Fi
   * Contraseña de Wi-Fi
   * Cadena de conexión de dispositivo

> [!Note]
> La información de credenciales se almacena en la EEPROM de Feather M0 WiFi. Si hace clic en el botón de restablecimiento de la placa Feather M0 WiFi, la aplicación de ejemplo le pregunta si quiere borrar la información. Escriba `Y` para borrar la información. Se le pide que proporcione la información una segunda vez.

### <a name="verify-the-sample-application-is-running-successfully"></a>Compruebe que la aplicación de ejemplo se ejecuta correctamente.

Si ve la siguiente salida de la ventana del monitor serie y el LED parpadeando en Feather M0 WiFi, la aplicación de ejemplo se está ejecutando correctamente.

![Salida final en el IDE de Arduino](media/iot-hub-adafruit-feather-m0-wifi-get-started/9_arduino-ide-final-output.png)

## <a name="next-steps"></a>Pasos siguientes

Ha conectado correctamente un dispositivo Feather M0 WiFi a IoT Hub y ha enviado los datos del sensor capturados a IoT Hub. 

[!INCLUDE [iot-hub-get-started-next-steps](../../includes/iot-hub-get-started-next-steps.md)]


