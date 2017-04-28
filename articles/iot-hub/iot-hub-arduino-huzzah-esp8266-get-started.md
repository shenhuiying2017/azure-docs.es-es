---
title: "ESP8266 en la nube: conexión de Feather HUZZAH ESP8266 a Azure IoT Hub | Microsoft Docs"
description: "Se explica cómo conectar un dispositivo Arduino, denominado Adafruit Feather HUZZAH ESP8266, a Azure IoT Hub, que es un servicio en la nube de Microsoft que ayuda a administrar los recursos de IoT."
services: iot-hub
documentationcenter: 
author: shizn
manager: timtl
tags: 
keywords: 
ms.assetid: c505aacf-89a8-40ed-a853-493b75bec524
ms.service: iot-hub
ms.devlang: arduino
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/28/2017
ms.author: xshi
translationtype: Human Translation
ms.sourcegitcommit: 785d3a8920d48e11e80048665e9866f16c514cf7
ms.openlocfilehash: 3650f628747f8a9e743711f5c7a175d2a2523565
ms.lasthandoff: 04/12/2017


---
# <a name="connect-adafruit-feather-huzzah-esp8266-to-azure-iot-hub-in-the-cloud"></a>Conexión de Adafruit Feather HUZZAH ESP8266 a Azure IoT Hub en la nube

[!INCLUDE [iot-hub-get-started-device-selector](../../includes/iot-hub-get-started-device-selector.md)]

![Conexión entre DHT22, Feather HUZZAH ESP8266 e IoT Hub](media/iot-hub-arduino-huzzah-esp8266-get-started/1_connection-hdt22-feather-huzzah-iot-hub.png)

## <a name="what-you-do"></a>Qué debe hacer


Cree una conexión entre Adafruit Feather HUZZAH ESP8266 e IoT Hub. Luego, ejecutará una aplicación de ejemplo en ESP8266 para recopilar datos de temperatura y humedad desde un sensor DHT22. Por último, envíe los datos del sensor a IoT Hub.

> [!NOTE]
> Aunque use otras placas ESP8266, puede seguir estos pasos para conectarse a IoT Hub. Dependiendo de la placa ESP8266 que use, puede que deba volver a configurar el `LED_PIN`. Por ejemplo, si usa ESP8266 de AI-Thinker, puede cambiarlo de `0` a `2`. ¿Aún no tiene un kit? Consígalo en el [sitio web de Azure](http://azure.com/iotstarterkits).




## <a name="what-you-learn"></a>Conocimientos que adquirirá

* Cómo crear una instancia de IoT Hub y registrar un dispositivo para Feather HUZZAH ESP8266
* Cómo conectar Feather HUZZAH ESP8266 con el sensor y el equipo
* Cómo recopilar datos del sensor mediante la ejecución de una aplicación de ejemplo en Feather HUZZAH ESP8266
* Cómo enviar los datos del sensor a IoT Hub

## <a name="what-you-need"></a>Lo que necesita

![Elementos necesarios para el tutorial](media/iot-hub-arduino-huzzah-esp8266-get-started/2_parts-needed-for-the-tutorial.png)

Para realizar esta operación, necesita los siguientes elementos del kit de inicio de Feather HUZZAH ESP8266:

* La placa Feather HUZZAH ESP8266
* Un cable micro USB a USB tipo A

También necesitará lo siguiente para el entorno de desarrollo:

* Un equipo PC o Mac que ejecute Windows o Ubuntu.
* Red inalámbrica a la que se conectará HUZZAH ESP8266.
* Conexión a Internet para descargar la herramienta de configuración.
* [IDE de Arduino](https://www.arduino.cc/en/main/software) versión 1.6.8 o posterior. Las versiones anteriores no funcionan con la biblioteca de AzureIoT.





Los elementos siguientes son opcionales en caso de que no tenga un sensor. También tiene la opción de usar una simulación de datos del sensor.

* Un sensor de temperatura y humedad Adafruit DHT22
* Una placa de pruebas
* Cables de puente M/M

## <a name="create-an-iot-hub-and-register-a-device-for-feather-huzzah-esp8266"></a>Creación de un centro de IoT y registro de un dispositivo para Feather HUZZAH ESP8266

### <a name="to-create-your-iot-hub-in-the-azure-portal-follow-these-steps"></a>Siga estos pasos para crear una instancia de IoT Hub en Azure Portal:

1. Inicie sesión en el [Portal de Azure](https://portal.azure.com/).
1. Haga clic en **Nuevo** > **Internet de las cosas** > **IoT Hub**.

   ![Crear IoT Hub](media/iot-hub-arduino-huzzah-esp8266-get-started/3_iot-hub-creation.png)

1. En el panel **Centro de IoT**, escriba la información necesaria para su centro de IoT:

   ![Información básica para creación de una instancia de IoT Hub](media/iot-hub-arduino-huzzah-esp8266-get-started/4_iot-hub-provide-basic-info.png)

   * **Nombre**: el nombre de su centro de IoT. Si el nombre que escribe es válido, aparece una marca de verificación verde.
   * **Nivel de precios y de escala**: seleccione el nivel F1 gratuito para esta demostración. Seleccione un [nivel de precios y de escala](https://azure.microsoft.com/pricing/details/iot-hub/).
   * **Grupo de recursos**: cree un grupo de recursos para hospedar la instancia de IoT Hub o use uno existente. Consulte [Uso de grupos de recursos para administrar los recursos de Azure](../azure-resource-manager/resource-group-portal.md).
   * **Ubicación**: seleccione la ubicación más cercana a usted donde se crea el centro de IoT.
   * **Anclar al panel**: seleccione esta opción para facilitar el acceso a IoT Hub desde el panel.

1. Haga clic en **Crear**. La creación del centro de IoT puede tardar unos minutos. Puede ver el progreso en el panel **Notificaciones**.

   ![Supervisar el progreso de creación de la instancia de IoT Hub en el panel de notificaciones](media/iot-hub-arduino-huzzah-esp8266-get-started/5_iot-hub-monitor-creation-progress-notification-pane.png)

1. Después de crear la instancia de IoT Hub, haga clic en ella desde el panel. Anote el valor del **nombre de host** que se usa más adelante en este artículo y haga clic en **Directivas de acceso compartido**.

   ![Obtener el nombre de host de la instancia de IoT Hub](media/iot-hub-arduino-huzzah-esp8266-get-started/6_iot-hub-get-hostname.png)

1. En el panel **Directivas de acceso compartido**, haga clic en la directiva **iothubowner** y, luego, copie y guarde el valor de la **cadena de conexión** de la instancia de IoT Hub. Usará este valor más adelante en este artículo. Para más información, consulte [Control del acceso a IoT Hub](iot-hub-devguide-security.md).

   ![Obtener cadena de conexión de IoT Hub](media/iot-hub-arduino-huzzah-esp8266-get-started/7_iot-hub-get-connection-string.png)

Ahora ha creado su instancia de IoT Hub. Asegúrese de guardar los valores de **nombre de host** y **cadena de conexión**. Se utilizarán más adelante en este artículo.


### <a name="register-a-device-for-feather-huzzah-esp8266-in-your-iot-hub"></a>Registro del dispositivo para Feather HUZZAH ESP8266 en su centro de IoT

Cada centro de IoT tiene un registro de identidad que almacena información acerca de los dispositivos que pueden conectarse al centro de IoT. Antes de que un dispositivo pueda conectarse a una instancia de IoT Hub, debe haber una entrada para ese dispositivo en el registro de identidad de esa instancia.


En esta sección, va a usar una herramienta de la CLI denominada *explorador iothub*. Use esta herramienta para registrar un dispositivo para Feather HUZZAH ESP8266 en el registro de identidades de su instancia de IoT Hub.



> [!NOTE]
> El explorador iothub requiere Node.js 4.x o una versión posterior para que funcione correctamente.

Para registrar un dispositivo para Feather HUZZAH ESP8266, siga estos pasos:

1. [Descargue](https://nodejs.org/en/download/) e instale la versión más reciente de LTS de Node.js, incluido NPM.
1. Instale el explorador iothub mediante NPM.

   * Windows 7 o posterior:

     Inicie un símbolo del sistema como administrador. Instale el explorador iothub mediante la ejecución del siguiente comando:

     ```bash
     npm install -g iothub-explorer
     ```

   * Ubuntu 16.04 o posterior:

     Abra un terminal mediante el método abreviado de teclado Ctrl + Alt + T y, luego, ejecute el siguiente comando:

     ```bash
     sudo npm install -g iothub-explorer
     ```

   * MacOS 10.1 o posterior:

     Abra un terminal y luego ejecute el siguiente comando:

     ```bash
     npm install -g iothub-explorer
     ```

3. Inicie sesión en su centro de IoT mediante la ejecución del siguiente comando:

   ```bash
   iothub-explorer login [your IoT hub connection string]
   ```

4. Registre un nuevo dispositivo. En el ejemplo siguiente, `deviceID` es `new-device`. Obtenga la cadena de conexión ejecutando el siguiente comando.

   ```bash
   iothub-explorer create new-device --connection-string
   ```

Anote la cadena de conexión del dispositivo registrado. Se utiliza más adelante.


> [!NOTE]
> Para ver la cadena de conexión de los dispositivos registrados, ejecute el comando `iothub-explorer list`.


## <a name="connect-feather-huzzah-esp8266-with-the-sensor-and-your-computer"></a>Conexión de Feather HUZZAH ESP8266 con el sensor y el equipo
En esta sección se conectan los sensores a la placa. Luego se conecta el dispositivo al equipo para su uso posterior.
### <a name="connect-a-dht22-temperature-and-humidity-sensor-to-feather-huzzah-esp8266"></a>Conexión de un sensor de humedad y temperatura DHT22 a Feather HUZZAH ESP8266

Use la placa de pruebas y los cables de puente para realizar la conexión del modo siguiente. Si no dispone de un sensor, omita esta sección porque en su lugar puede usar una simulación de datos del sensor.

![Referencia de conexiones](media/iot-hub-arduino-huzzah-esp8266-get-started/15_connections_on_breadboard.png)


En las patillas del sensor, use el siguiente cableado:


| Inicio (sensor)           | Fin (placa)           | Color de cable   |
| -----------------------  | ---------------------- | ------------: |
| VDD (patilla 31F)            | 3V (patilla 58H)           | Cable rojo     |
| DATOS (patilla 32F)           | GPIO 2 (patilla 46)       | Cable azul    |
| GND (patilla 34F)            | GND (patilla 56I)          | Cable negro   |

Para más información, consulte la [instalación del sensor Adafruit DHT22](https://learn.adafruit.com/dht/connecting-to-a-dhtxx-sensor) y el [patillaje de Adafruit Feather HUZZAH Esp8266](https://learn.adafruit.com/adafruit-feather-huzzah-esp8266/using-arduino-ide?view=all#pinouts).





Ahora su Feather Huzzah ESP8266 debe estar conectado con un sensor de trabajo.

![Conectar DHT22 con Feather Huzzah](media/iot-hub-arduino-huzzah-esp8266-get-started/8_connect-dht22-feather-huzzah.png)

### <a name="connect-feather-huzzah-esp8266-to-your-computer"></a>Conexión de Feather HUZZAH ESP8266 a su equipo

Como se indica a continuación, use el cable micro USB a USB tipo A para conectar Feather HUZZAH ESP8266 a su equipo de la manera siguiente.

![Conectar Feather Huzzah al equipo](media/iot-hub-arduino-huzzah-esp8266-get-started/9_connect-feather-huzzah-computer.png)

### <a name="add-serial-port-permissions-ubuntu-only"></a>Agregar permisos de puerto serie (solo Ubuntu)


Si usa Ubuntu, asegúrese de que tiene los permisos para trabajar en el puerto USB de Feather HUZZAH ESP8266. Para agregar permisos de puerto serie, siga estos pasos:


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

En esta sección, implementará y ejecutará una aplicación de ejemplo en Feather HUZZAH ESP8266. La aplicación de ejemplo hace parpadear el LED en Feather HUZZAH ESP8266 y envía los datos de humedad y temperatura recopilados del sensor DHT22 a la instancia de IoT Hub.

### <a name="get-the-sample-application-from-github"></a>Obtención de la aplicación de ejemplo de Github

La aplicación de ejemplo se hospeda en GitHub. Clone el repositorio de ejemplos que contiene la aplicación de ejemplo de GitHub. Para clonar el repositorio de ejemplos, siga estos pasos:

1. Abra un símbolo del sistema o una ventana de terminal.
1. Vaya a la carpeta donde desea almacenar la aplicación de ejemplo.
1. Ejecute el siguiente comando:

   ```bash
   git clone https://github.com/Azure-Samples/iot-hub-feather-huzzah-client-app.git
   ```

Instale el paquete de Feather HUZZAH ESP8266 en el IDE de Arduino:

1. Abra la carpeta donde se almacena la aplicación de ejemplo.
1. Abra el archivo app.ino en la carpeta de la aplicación en el IDE de Arduino.

   ![Abrir la aplicación de ejemplo en el IDE de Arduino](media/iot-hub-arduino-huzzah-esp8266-get-started/10_arduino-ide-open-sample-app.png)

1. En Arduino IDE, haga clic en **File** >  (Archivo) **Preferences** (Preferencias).
1. En el cuadro de diálogo **Preferences** (Preferencias), haga clic en el icono junto al cuadro de diálogo **Additional Boards Manager URLs** (URL adicionales del gestor de placas).
1. En la ventana emergente, escriba la dirección URL siguiente y, a continuación, haga clic en **OK** (Aceptar).

   `http://arduino.esp8266.com/stable/package_esp8266com_index.json`

   ![Apuntar a una url de paquete en el IDE de Arduino](media/iot-hub-arduino-huzzah-esp8266-get-started/11_arduino-ide-package-url.png)

1. En el cuadro de diálogo **Preference** (Preferencias), haga clic en **OK** (Aceptar).
1. Haga clic en **Tools** (Herramientas)  > **Board** (Placa)  > **Boards Manager** (Administrador de placas) y busque esp8266.

   El administrador de placas indica que está instalado ESP8266 con la versión 2.2.0 o posterior.

   ![Se instala el paquete esp8266](media/iot-hub-arduino-huzzah-esp8266-get-started/12_arduino-ide-esp8266-installed.png)

1. Haga clic en **Tools** >  (Herramientas) **Board** >  (Placa) **Adafruit HUZZAH ESP8266**.

### <a name="install-necessary-libraries"></a>Instalación de las bibliotecas necesarias

1. En Arduino IDE, haga clic en **Sketch** >  (Boceto) **Include Library** >  (Incluir biblioteca) **Manage Libraries** (Administrar bibliotecas).
1. Busque los nombres de biblioteca siguientes uno a uno. Para cada biblioteca que encuentre, haga clic en **Install** (Instalar).
   * `AzureIoTHub`
   * `AzureIoTUtility`
   * `AzureIoTProtocol_MQTT`
   * `ArduinoJson`
   * `DHT sensor library`
   * `Adafruit Unified Sensor`

### <a name="dont-have-a-real-dht22-sensor"></a>¿No tiene un sensor DHT22 real?

La aplicación de ejemplo puede simular datos de humedad y temperatura en caso de que no disponga de un sensor DHT22 real. Para configurar la aplicación de ejemplo de modo que use datos simulados, siga estos pasos:

1. Abra el archivo `config.h` de la carpeta `app`.
1. Busque la siguiente línea de código y cambie el valor de `false` a `true`:
   ```c
   define SIMULATED_DATA true
   ```
   ![Configurar la aplicación de ejemplo para usar datos simulados](media/iot-hub-arduino-huzzah-esp8266-get-started/13_arduino-ide-configure-app-use-simulated-data.png)

1. Guarde el archivo como `Control-s`.

### <a name="deploy-the-sample-application-to-feather-huzzah-esp8266"></a>Implementación de la aplicación de ejemplo en Feather HUZZAH ESP8266

1. En Arduino IDE, haga clic en **Tool** >  (Herramienta) **Port** (Puerto) y haga clic en el puerto serie de Feather HUZZAH ESP8266.
1. Haga clic en **Sketch** >  (Boceto) **Upload** (Cargar) para compilar e implementar la aplicación de ejemplo en Feather HUZZAH ESP8266.

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
> La información de credenciales se almacena en la EEPROM de Feather HUZZAH ESP8266. Si hace clic en el botón de restablecimiento de la placa Feather HUZZAH ESP8266, la aplicación de ejemplo le pregunta si desea borrar la información. Escriba `Y` para borrar la información. Se le pide que proporcione la información una segunda vez.

### <a name="verify-the-sample-application-is-running-successfully"></a>Compruebe que la aplicación de ejemplo se ejecuta correctamente.

Si ve la siguiente salida de la ventana del monitor serie y el LED parpadeando en Feather HUZZAH ESP8266, la aplicación de ejemplo se ejecuta correctamente.

![Salida final en el IDE de Arduino](media/iot-hub-arduino-huzzah-esp8266-get-started/14_arduino-ide-final-output.png)

## <a name="next-steps"></a>Pasos siguientes

Ha conectado correctamente un dispositivo Feather HUZZAH ESP8266 a su instancia de IoT Hub y ha enviado los datos de sensor capturados a esa instancia. 

[!INCLUDE [iot-hub-get-started-next-steps](../../includes/iot-hub-get-started-next-steps.md)]


