---
title: "ESP8266 en la nube: conexión de Sparkfun ESP8266 Thing Dev a Azure IoT Hub | Microsoft Docs"
description: "Con este tutorial aprenderá a configurar y conectar Sparkfun ESP8266 Thing Dev a Azure IoT Hub para que envíe datos a la plataforma en la nube de Azure."
services: iot-hub
documentationcenter: 
author: shizn
manager: timlt
tags: 
keywords: 
ms.assetid: 587fe292-9602-45b4-95ee-f39bba10e716
ms.service: iot-hub
ms.devlang: arduino
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/16/2017
ms.author: xshi
ms.openlocfilehash: 557f0cdf375b345e0dbe0526f5a5bd3c050dec38
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="connect-sparkfun-esp8266-thing-dev-to-azure-iot-hub-in-the-cloud"></a>Conexión de Sparkfun ESP8266 Thing Dev a Azure IoT Hub en la nube

[!INCLUDE [iot-hub-get-started-device-selector](../../includes/iot-hub-get-started-device-selector.md)]

![conexión entre DHT22, Thing Dev, y IoT Hub](media/iot-hub-sparkfun-thing-dev-get-started/1_connection-hdt22-thing-dev-iot-hub.png)

## <a name="what-you-will-do"></a>Lo que hará

Conectará Sparkfun ESP8266 Thing Dev a un centro de IoT que creará. Luego, ejecutará una aplicación de ejemplo en ESP8266 para recopilar datos de temperatura y humedad desde un sensor DHT22. Por último, enviará los datos del sensor al centro de IoT.

> [!NOTE]
> Aunque use otras placas ESP8266, puede seguir estos pasos para conectarse a su centro de IoT. Dependiendo de la placa ESP8266 que use, puede que deba volver a configurar el `LED_PIN`. Por ejemplo, si usa ESP8266 de AI-Thinker, puede cambiarlo de `0` a `2`. ¿Aún no tiene el kit?, haga clic [aquí](http://azure.com/iotstarterkits)

## <a name="what-you-will-learn"></a>Lo qué aprenderá

* Cómo crear un centro de IoT y registrar un dispositivo para Thing Dev.
* Cómo conectar Thing Dev con el sensor y el equipo.
* Cómo recopilar datos del sensor mediante la ejecución de una aplicación de ejemplo en Thing Dev.
* Cómo enviar los datos del sensor al centro de IoT.

## <a name="what-you-will-need"></a>Qué necesita

![Elementos necesarios para el tutorial](media/iot-hub-sparkfun-thing-dev-get-started/2_parts-needed-for-the-tutorial.png)

Para completar esta operación, necesita las siguientes piezas del kit de inicio de Thing Dev:

* La placa Sparkfun ESP8266 Thing Dev.
* Un cable micro USB a USB tipo A.

También necesitará lo siguiente para el entorno de desarrollo:

* Una suscripción de Azure activa. Si no tiene ninguna cuenta de Azure, [cree una cuenta de evaluación gratuita de Azure](https://azure.microsoft.com/free/) en solo unos minutos.
* Un equipo PC o Mac que ejecute Windows o Ubuntu.
* Red inalámbrica a la que se pueda conectar Sparkfun ESP8266 Thing Dev.
* Conexión a Internet para descargar la herramienta de configuración.
* [Arduino IDE](https://www.arduino.cc/en/main/software) versión 1.6.8 (o posterior), ya que las versiones anteriores no funcionarán con la biblioteca de AzureIoT.

Los elementos siguientes son opcionales en caso de que no tenga un sensor. También tiene la opción de usar una simulación de datos del sensor.

* Un sensor de temperatura y humedad Adafruit DHT22.
* La placa de pruebas.
* Cables de puente M/M.

[!INCLUDE [iot-hub-get-started-create-hub-and-device](../../includes/iot-hub-get-started-create-hub-and-device.md)]

## <a name="connect-esp8266-thing-dev-with-the-sensor-and-your-computer"></a>Conexión de ESP8266 Thing Dev con el sensor y el equipo

### <a name="connect-a-dht22-temperature-and-humidity-sensor-to-esp8266-thing-dev"></a>Conexión de un sensor de humedad y temperatura DHT22 a ESP8266 Thing Dev

Use la placa de pruebas y los cables de puente para realizar la conexión del modo siguiente. Si no dispone de un sensor, omita esta sección porque en su lugar puede usar una simulación de datos del sensor.

![referencia de conexiones](media/iot-hub-sparkfun-thing-dev-get-started/15_connections_on_breadboard.png)

Para las patillas del sensor, usaremos el siguiente cableado:

| Inicio (sensor)           | Fin (placa)           | Color de cable   |
| -----------------------  | ---------------------- | ------------: |
| VDD (patilla 27F)            | 3V (patilla 8A)           | Cable rojo     |
| DATOS (patilla 28F)           | GPIO 2 (patilla 9A)       | Cable blanco    |
| GND (patilla 30F)            | GND (patilla 7J)          | Cable negro   |


- Para obtener más información, consulte [el programa de instalación del sensor DHT22](http://cdn.sparkfun.com/datasheets/Sensors/Weather/RHT03.pdf) y [las especificaciones de Sparkfun ESP8266 Thing Dev](https://www.sparkfun.com/products/13711).

Ahora su Sparkfun ESP8266 Thing Dev debe estar conectado con un sensor de trabajo.

![conectar dht22 con ESP8266 Thing Dev](media/iot-hub-sparkfun-thing-dev-get-started/8_connect-dht22-thing-dev.png)

### <a name="connect-sparkfun-esp8266-thing-dev-to-your-computer"></a>Conexión de Sparkfun ESP8266 Thing Dev a su equipo

Use el cable micro USB a USB tipo A para conectar Sparkfun ESP8266 Thing Dev a su equipo de la manera siguiente.

![conectar feather huzzah a su equipo](media/iot-hub-sparkfun-thing-dev-get-started/9_connect-thing-dev-computer.png)

### <a name="add-serial-port-permissions--ubuntu-only"></a>Adición de permisos de puerto (solo Ubuntu)

Si usa Ubuntu, asegúrese de que un usuario normal tenga los permisos para trabajar en el puerto USB de Sparkfun ESP8266 Thing Dev. Para agregar permisos de puerto serie para un usuario normal, siga estos pasos:

1. Ejecute los siguientes comandos en un terminal:

   ```bash
   ls -l /dev/ttyUSB*
   ls -l /dev/ttyACM*
   ```

   Obtendrá una de las siguientes salidas:

   * crw-rw---- 1 root uucp xxxxxxxx
   * crw-rw---- 1 root dialout xxxxxxxx

   En la salida, observe `uucp` o `dialout`, que es el nombre del propietario del grupo del puerto USB.

1. Agregue el usuario al grupo mediante la ejecución del comando siguiente:

   ```bash
   sudo usermod -a -G <group-owner-name> <username>
   ```

   `<group-owner-name>` es el nombre del propietario del grupo que obtuvo en el paso anterior. `<username>` es el nombre de usuario de Ubuntu.

1. Cierre la sesión en Ubuntu e iníciela de nuevo para que el cambio surta efecto.

## <a name="collect-sensor-data-and-send-it-to-your-iot-hub"></a>Recopilación de datos del sensor y envío al centro de IoT

En esta sección, implementará y ejecutará una aplicación de ejemplo en Sparkfun ESP8266 Thing Dev. La aplicación de ejemplo hace parpadear el LED en Sparkfun ESP8266 Thing Dev y envía los datos de humedad y temperatura recopilados del sensor DHT22 al centro de IoT.

### <a name="get-the-sample-application-from-github"></a>Obtención de la aplicación de ejemplo de Github

La aplicación de ejemplo se hospeda en GitHub. Clone el repositorio de ejemplos que contiene la aplicación de ejemplo de GitHub. Para clonar el repositorio de ejemplos, siga estos pasos:

1. Abra un símbolo del sistema o una ventana de terminal.
1. Vaya a la carpeta donde desea almacenar la aplicación de ejemplo.
1. Ejecute el siguiente comando:

   ```bash
   git clone https://github.com/Azure-Samples/iot-hub-SparkFun-ThingDev-client-app.git
   ```

Instale el paquete para Sparkfun ESP8266 Thing Dev en Arduino IDE:

1. Abra la carpeta donde se almacena la aplicación de ejemplo.
1. Abra el archivo app.ino en la carpeta de la aplicación en Arduino IDE.

   ![abrir la aplicación de ejemplo en arduino ide](media/iot-hub-sparkfun-thing-dev-get-started/10_arduino-ide-open-sample-app.png)

1. En Arduino IDE, haga clic en **File** >  (Archivo) **Preferences** (Preferencias).
1. En el cuadro de diálogo **Preferences** (Preferencias), haga clic en el icono junto al cuadro de texto **Additional Boards Manager URLs** (URL adicionales del gestor de placas).
1. En la ventana emergente, escriba la dirección URL siguiente y, a continuación, haga clic en **OK** (Aceptar).

   `http://arduino.esp8266.com/stable/package_esp8266com_index.json`

   ![apuntar a una url de paquete en arduino ide](media/iot-hub-sparkfun-thing-dev-get-started/11_arduino-ide-package-url.png)

1. En el cuadro de diálogo **Preference** (Preferencias), haga clic en **OK** (Aceptar).
1. Haga clic en **Tools** (Herramientas)  > **Board** (Placa)  > **Boards Manager** (Administrador de placas) y busque esp8266.
   Se instalará ESP8266 con una versión de 2.2.0 o posterior.

   ![se instala el paquete esp8266](media/iot-hub-sparkfun-thing-dev-get-started/12_arduino-ide-esp8266-installed.png)

1. Haga clic en **Tools** (Herramientas)  > **Board** (Placa)  > **Sparkfun ESP8266 Thing Dev**.

### <a name="install-necessary-libraries"></a>Instalación de las bibliotecas necesarias

1. En Arduino IDE, haga clic en **Sketch** >  (Boceto) **Include Library** >  (Incluir biblioteca) **Manage Libraries** (Administrar bibliotecas).
1. Busque los nombres de biblioteca siguientes uno a uno. Para cada una de las bibliotecas que encuentre, haga clic en **Install** (Instalar).
   * `AzureIoTHub`
   * `AzureIoTUtility`
   * `AzureIoTProtocol_MQTT`
   * `ArduinoJson`
   * `DHT sensor library`
   * `Adafruit Unified Sensor`

### <a name="dont-have-a-real-dht22-sensor"></a>¿No tiene un sensor DHT22 real?

La aplicación de ejemplo puede simular datos de humedad y temperatura en caso de que no disponga de un sensor DHT22 real. Para permitir que la aplicación de ejemplo use datos simulados, siga estos pasos:

1. Abra el archivo `config.h` de la carpeta `app`.
1. Busque la siguiente línea de código y cambie el valor de `false` a `true`:
   ```c
   define SIMULATED_DATA true
   ```
   ![configurar la aplicación de ejemplo para usar datos simulados](media/iot-hub-sparkfun-thing-dev-get-started/13_arduino-ide-configure-app-use-simulated-data.png)
   
1. Guarde con `Control-s`.

### <a name="deploy-the-sample-application-to-sparkfun-esp8266-thing-dev"></a>Implementación de la aplicación de ejemplo en Sparkfun ESP8266 Thing Dev

1. En Arduino IDE, haga clic en **Tool** >  (Herramienta) **Port** (Puerto) y haga clic en el puerto serie de Sparkfun ESP8266 Thing Dev.
1. Haga clic en **Sketch** >  (Boceto) **Upload** (Cargar) para compilar e implementar la aplicación de ejemplo en Sparkfun ESP8266 Thing Dev.

> [!Note]
> Si usa macOS, quizá vea los siguientes mensajes durante la carga. `warning: espcomm_sync failed`,`error: espcomm_open failed`. Abra la ventana de terminal y realice las siguientes acciones para resolver este problema.
> ```bash
> cd /System/Library/Extensions/IOUSBFamily.kext/Contents/PlugIns
> sudo mv AppleUSBFTDI.kext AppleUSBFTDI.disabled
> sudo touch /System/Library/Extensions
> ```

### <a name="enter-your-credentials"></a>Escriba sus credenciales.

Cuando la carga finalice correctamente, siga los pasos para escribir sus credenciales:

1. En Arduino IDE, haga clic en **Tools** >  (Herramientas) **Serial Monitor** (Monitor serie).
1. En la ventana de monitor serie, observe las dos listas desplegables de la esquina inferior derecha.
1. Seleccione **No line ending** (Sin final de línea) para la lista desplegable de la izquierda.
1. Seleccione **115200 baud** (115200 baudios) para la lista desplegable derecha.
1. En el cuadro de entrada ubicado en la parte superior de la ventana del monitor serie, escriba la siguiente información si se le solicita y luego haga clic en **Send** (Enviar).
   * SSID de Wi-Fi
   * Contraseña de Wi-Fi
   * Cadena de conexión de dispositivo

> [!Note]
> La información de credenciales se almacena en la EEPROM de Sparkfun ESP8266 Thing Dev. Si hace clic en el botón de restablecimiento de la placa Sparkfun ESP8266 Thing Dev, la aplicación de ejemplo le pregunta si desea borrar la información. Escriba `Y` para que se borre la información y se le pida de nuevo.

### <a name="verify-the-sample-application-is-running-successfully"></a>Compruebe que la aplicación de ejemplo se ejecuta correctamente.

Si ve la siguiente salida de la ventana del monitor serie y el LED parpadeando en Sparkfun ESP8266 Thing Dev, la aplicación de ejemplo se ejecuta correctamente.

![salida final en arduino ide](media/iot-hub-sparkfun-thing-dev-get-started/14_arduino-ide-final-output.png)

## <a name="next-steps"></a>Pasos siguientes

Ha conectado correctamente un dispositivo Sparkfun ESP8266 Thing Dev a su centro de IoT y ha enviado los datos de sensor capturados a su centro de IoT. 

[!INCLUDE [iot-hub-get-started-next-steps](../../includes/iot-hub-get-started-next-steps.md)]
