---
title: "Uso de un dispositivo físico con Azure IoT Edge | Documentos de Microsoft"
description: "Describe cómo usar un dispositivo SensorTag de Texas Instruments para enviar datos a IoT Hub a través de una puerta de enlace de IoT Edge que se ejecuta en un dispositivo Raspberry Pi 3. La puerta de enlace se creó con Azure IoT Edge."
services: iot-hub
documentationcenter: 
author: chipalost
manager: timlt
editor: 
ms.assetid: 212dacbf-e5e9-48b2-9c8a-1c14d9e7b913
ms.service: iot-hub
ms.devlang: cpp
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/12/2017
ms.author: andbuc
ms.translationtype: Human Translation
ms.sourcegitcommit: cb4d075d283059d613e3e9d8f0a6f9448310d96b
ms.openlocfilehash: 02962a91c739a53dfcf947bcc736e5c293b9384f
ms.contentlocale: es-es
ms.lasthandoff: 06/26/2017

---
# <a name="use-azure-iot-edge-on-a-raspberry-pi-to-forward-device-to-cloud-messages-to-iot-hub"></a>Uso de Azure IoT Edge en un Raspberry Pi para reenviar mensajes del dispositivo a la nube a IoT Hub

En este tutorial de [ejemplo de baja energía de Bluetooth][lnk-ble-samplecode] se explica cómo usar [Azure IoT Edge][lnk-sdk] para:

* Reenviar telemetría de dispositivo a nube, a IoT Hub desde un dispositivo físico.
* Comandos de ruta desde IoT Hub a un dispositivo físico.

En este tutorial, se describen los siguientes procedimientos:

* **Arquitectura**: información de arquitectura importante sobre el ejemplo de baja energía de Bluetooth.
* **Compilación y ejecución**: los pasos necesarios para compilar y ejecutar el ejemplo.

## <a name="architecture"></a>Arquitectura

En el tutorial se muestra cómo compilar y ejecutar una puerta de enlace de IoT Edge en un Raspberry Pi 3 que ejecuta Raspbian Linux. La puerta de enlace se creó con IoT Edge. El ejemplo usa un dispositivo de baja energía de Bluetooth (BLE) SensorTag de Texas Instruments para recopilar datos de temperatura.

Al ejecutar la puerta de enlace de IoT Edge:

* Se conecta a un dispositivo SensorTag mediante el protocolo de baja energía de Bluetooth (BLE).
* Se conecta a IoT Hub mediante el protocolo HTTP.
* Reenvía los datos de telemetría del dispositivo SensorTag a IoT Hub.
* Enruta los comandos desde IoT Hub al dispositivo SensorTag.

La puerta de enlace contiene los siguientes módulos de IoT Edge:

* Un *módulo BLE* que interactúa con un dispositivo BLE para recibir sus datos de temperatura y enviarle comandos.
* Un módulo *BLE de nube a dispositivo* que convierte mensajes JSON enviados de IoT Hub en instrucciones BLE para el *módulo BLE*.
* Un *módulo registrador* que recoge todos los mensajes de la puerta de enlace en un archivo local.
* Un *módulo de asignación de identidad* que traduce entre direcciones MAC de los dispositivos BLE y las identidades de los dispositivos de Azure IoT Hub.
* Un *módulo de IoT Hub* que carga los datos de telemetría a un centro de IoT y recibe comandos de dispositivos provenientes de un centro de IoT.
* Un *módulo de impresora BLE* que interpreta la telemetría del dispositivo BLE e imprime los datos con formato en la consola para habilitar la solución de problemas y la depuración.

### <a name="how-data-flows-through-the-gateway"></a>Cómo fluyen los datos a través de la puerta de enlace

El siguiente diagrama de bloques muestra la canalización del flujo de datos de carga para telemetría:

![Canalización de puerta de enlace de carga de telemetría](media/iot-hub-iot-edge-physical-device/gateway_ble_upload_data_flow.png)

Etapas de la trayectoria de un elemento de telemetría desde un dispositivo BLE hasta IoT Hub:

1. El dispositivo BLE genera un ejemplo de temperatura y lo envía a través de Bluetooth al módulo BLE de la puerta de enlace.
1. El módulo BLE recibe el ejemplo y lo publica en el agente junto con la dirección MAC del dispositivo.
1. El módulo de asignación de identidad recoge este mensaje y usa una tabla interna para convertir la dirección MAC del dispositivo en una identidad de dispositivo de IoT Hub. Una identidad de dispositivo de IoT Hub consta de un identificador de dispositivo y una clave de dispositivo.
1. El módulo de asignación de identidad publica un mensaje nuevo que contiene los datos de ejemplo de la temperatura, la dirección MAC del dispositivo, el identificador de dispositivo y la clave del dispositivo.
1. El módulo de IoT Hub recibe este mensaje nuevo (generado por el módulo de asignación de identidad) y lo publica en IoT Hub.
1. El módulo registrador recoge todos los mensajes del agente en un archivo local.

El siguiente diagrama de bloques muestra la canalización del flujo de datos de comandos para el dispositivo:

![Canalización de puerta de enlace de comandos de dispositivo](media/iot-hub-iot-edge-physical-device/gateway_ble_command_data_flow.png)

1. El módulo de IoT Hub sondea periódicamente el centro de IoT en busca de nuevos mensajes de comando.
1. Cuando el módulo de IoT Hub recibe un nuevo mensaje de comando, lo publica en el agente.
1. El módulo de asignación de identidad recoge este mensaje y usa una tabla interna para convertir el identificador del dispositivo de IoT Hub en una dirección MAC del dispositivo. A continuación, publica un mensaje nuevo que incluye la dirección MAC del dispositivo de destino en el mapa de propiedades del mensaje.
1. El módulo BLE de nube a dispositivo recoge este mensaje y lo convierte en la instrucción BLE adecuada para el módulo BLE. Luego publica un mensaje nuevo.
1. El módulo BLE recoge este mensaje y ejecuta la instrucción de E/S al comunicarse con el dispositivo BLE.
1. El módulo registrador recoge todos los mensajes del agente en un archivo de disco.

## <a name="prerequisites"></a>Requisitos previos

Para completar este tutorial, deberá tener una suscripción activa de Azure.

> [!NOTE]
> En caso de no tener ninguna, puede crear una cuenta de evaluación gratuita en tan solo unos minutos. Para más información, consulte la [evaluación gratuita de Azure][lnk-free-trial].

Necesita el cliente SSH en su máquina de escritorio para poder acceder de forma remota a la línea de comandos en su Raspberry Pi.

- Windows no incluye ningún cliente SSH. Se recomienda usar [PuTTY](http://www.putty.org/).
- La mayoría de las distribuciones de Linux y Mac OS incluyen la utilidad de línea de comandos de SSH. Para más información, consulte [SSH Using Linux or Mac OS](https://www.raspberrypi.org/documentation/remote-access/ssh/unix.md) (SSH cuando se usa Linux o Mac OS).

## <a name="prepare-your-hardware"></a>Preparar el hardware

En este tutorial se supone que usa un dispositivo [SensorTag de Texas Instruments](http://www.ti.com/ww/en/wireless_connectivity/sensortag2015/index.html) conectado a un Raspberry pi 3 que ejecuta Raspbian.

### <a name="install-raspbian"></a>Instalación de Raspbian

Puede utilizar cualquiera de las siguientes opciones para instalar Raspbian en el dispositivo Raspberry Pi 3.

* Use [NOOBS][lnk-noobs], una interfaz gráfica de usuario, para instalar la versión más reciente de Raspbian.
* [Descargue][lnk-raspbian] manualmente y escriba la imagen más reciente del sistema operativo Raspbian en una tarjeta SD.

### <a name="sign-in-and-access-the-terminal"></a>Inicio de sesión y acceso al terminal

Dispone de dos opciones para acceder a un entorno de terminal en su Raspberry Pi:

* Si tiene un teclado y un monitor conectados a su Raspberry Pi, puede usar la GUI de Raspbian para acceder a una ventana de terminal.

* Acceda a la línea de comandos en su Raspberry Pi mediante SSH desde la máquina de escritorio.

#### <a name="use-a-terminal-window-in-the-gui"></a>Uso de una ventana de terminal en la GUI

Las credenciales predeterminadas para Raspbian son el nombre de usuario **pi** y la contraseña **raspberry**. En la barra de tareas en la GUI, puede iniciar la utilidad **Terminal** mediante el icono que parece un monitor.

#### <a name="sign-in-with-ssh"></a>Inicio de sesión con SSH

Puede usar SSH para el acceso de línea de comandos a su Raspberry Pi. En el artículo [SSH (Secure Shell)][lnk-pi-ssh], se describe cómo configurar SSH en Raspberry Pi y cómo conectarse desde [Windows][lnk-ssh-windows] o [Linux y Mac OS][lnk-ssh-linux].

Inicie sesión con el nombre de usuario **pi** y la contraseña **raspberry**.

### <a name="install-bluez-537"></a>Instalación de BlueZ 5.37

Los módulos de BLE se comunican con el hardware de Bluetooth a través de la pila BlueZ. Se necesita la versión 5.37 de BlueZ para que los módulos funcionen correctamente. Con estas instrucciones se garantiza que está instalada la versión correcta de BlueZ.

1. Detenga el demonio de bluetooth actual:

    ```sh
    sudo systemctl stop bluetooth
    ```

1. Instale las dependencias de BlueZ:

    ```sh
    sudo apt-get update
    sudo apt-get install bluetooth bluez-tools build-essential autoconf glib2.0 libglib2.0-dev libdbus-1-dev libudev-dev libical-dev libreadline-dev
    ```

1. Descargue el código fuente de BlueZ de bluez.org:

    ```sh
    wget http://www.kernel.org/pub/linux/bluetooth/bluez-5.37.tar.xz
    ```

1. Descomprima el código fuente:

    ```sh
    tar -xvf bluez-5.37.tar.xz
    ```

1. Cambie los directorios a la carpeta recién creada:

    ```sh
    cd bluez-5.37
    ```

1. Configure el código de BlueZ que se va a crear:

    ```sh
    ./configure --disable-udev --disable-systemd --enable-experimental
    ```

1. Cree BlueZ:

    ```sh
    make
    ```

1. Una vez que ha terminado la creación, instale BlueZ:

    ```sh
    sudo make install
    ```

1. Cambie la configuración de servicio systemd para el bluetooth de forma que apunte al nuevo demonio de bluetooth en el archivo `/lib/systemd/system/bluetooth.service`. Sustituya la línea "ExecStart" por el siguiente texto:

    ```conf
    ExecStart=/usr/local/libexec/bluetooth/bluetoothd -E
    ```

### <a name="enable-connectivity-to-the-sensortag-device-from-your-raspberry-pi-3-device"></a>Habilitación de la conectividad al dispositivo SensorTag desde el dispositivo Raspberry Pi 3

Antes de ejecutar el ejemplo, debe comprobar que la Raspberry Pi 3 puede conectarse al dispositivo SensorTag.

1. Asegúrese de que la utilidad `rfkill` está instalada:

    ```sh
    sudo apt-get install rfkill
    ```

1. Desbloquee el bluetooth en el Raspberry Pi 3 y compruebe que el número de versión es **5.37**:

    ```sh
    sudo rfkill unblock bluetooth
    bluetoothctl --version
    ```

1. Para entrar en el shell de BlueTooth interactivo, inicie el servicio BlueTooth y ejecute el comando **bluetoothctl**:

    ```sh
    sudo systemctl start bluetooth
    bluetoothctl
    ```

1. Escriba el comando **power on** para encender el controlador Bluetooth. El comando devuelve una salida similar a la siguiente:

    ```sh
    [NEW] Controller 98:4F:EE:04:1F:DF C3 raspberrypi [default]
    ```

1. En el shell de Bluetooth interactivo, escriba el comando **scan on** para buscar dispositivos Bluetooth. El comando devuelve una salida similar a la siguiente:

    ```sh
    Discovery started
    [CHG] Controller 98:4F:EE:04:1F:DF Discovering: yes
    ```

1. Presione el botón pequeño (el LED verde debe parpadear) para que el dispositivo SensorTag sea visible. El Raspberry Pi 3 debe detectar el dispositivo SensorTag:

    ```sh
    [NEW] Device A0:E6:F8:B5:F6:00 CC2650 SensorTag
    [CHG] Device A0:E6:F8:B5:F6:00 TxPower: 0
    [CHG] Device A0:E6:F8:B5:F6:00 RSSI: -43
    ```

    En este ejemplo, puede ver que la dirección MAC del dispositivo SensorTag es **A0:E6:F8:B5:F6:00**.

1. Escriba el comando **scan off** para desactivar la búsqueda:

    ```sh
    [CHG] Controller 98:4F:EE:04:1F:DF Discovering: no
    Discovery stopped
    ```

1. Escriba **connect \<dirección MAC\>** para conectarse al dispositivo SensorTag con la dirección MAC. La salida del ejemplo siguiente se abrevia para mayor claridad:

    ```sh
    Attempting to connect to A0:E6:F8:B5:F6:00
    [CHG] Device A0:E6:F8:B5:F6:00 Connected: yes
    Connection successful
    [CHG] Device A0:E6:F8:B5:F6:00 UUIDs: 00001800-0000-1000-8000-00805f9b34fb
    ...
    [NEW] Primary Service
            /org/bluez/hci0/dev_A0_E6_F8_B5_F6_00/service000c
            Device Information
    ...
    [CHG] Device A0:E6:F8:B5:F6:00 GattServices: /org/bluez/hci0/dev_A0_E6_F8_B5_F6_00/service000c
    ...
    [CHG] Device A0:E6:F8:B5:F6:00 Name: SensorTag 2.0
    [CHG] Device A0:E6:F8:B5:F6:00 Alias: SensorTag 2.0
    [CHG] Device A0:E6:F8:B5:F6:00 Modalias: bluetooth:v000Dp0000d0110
    ```

    > Con el comando **list-attributes** puede volver a enumerar las características GATT del dispositivo.

1. Ahora puede desconectarse del dispositivo con el comando **disconnect** y salir del shell de Bluetooth con el comando **quit**:

    ```sh
    Attempting to disconnect from A0:E6:F8:B5:F6:00
    Successful disconnected
    [CHG] Device A0:E6:F8:B5:F6:00 Connected: no
    ```

Ya está listo para ejecutar el ejemplo de IoT Edge de BLE en Raspberry Pi 3.

## <a name="run-the-iot-edge-ble-sample"></a>Ejecutar el ejemplo de IoT Edge de BLE

Para ejecutar el ejemplo de IoT Edge de BLE, debe realizar tres tareas:

* Configurar dos dispositivos de ejemplo en IoT Hub.
* Compile IoT Edge en el dispositivo Raspberry Pi 3.
* Configurar y ejecutar el ejemplo de BLE en el dispositivo Raspberry Pi 3.

En el momento de redactar este artículo, IoT Edge solo es compatible con módulos BLE en puertas de enlace que se ejecuten en Linux.

### <a name="configure-two-sample-devices-in-your-iot-hub"></a>Configuración de dos dispositivos de ejemplo en IoT Hub

* [Cree una instancia de IoT Hub][lnk-create-hub] en su suscripción de Azure. Necesitará el nombre de su instancia para realizar este tutorial. En caso de no tener ninguna, puede crear una [cuenta gratuita][lnk-free-trial] en tan solo unos minutos.
* Agregue un dispositivo denominado **SensorTag_01** a su centro de IoT y tome nota de la clave y el identificador del dispositivo. Puede usar las herramientas [Explorador de dispositivos o iothub-explorer][lnk-explorer-tools] para agregar este dispositivo al centro de IoT que creó en el paso anterior y recuperar la clave. Tendrá que asignar este dispositivo al dispositivo SensorTag cuando configure la puerta de enlace.

### <a name="build-azure-iot-edge-on-your-raspberry-pi-3"></a>Compilar Azure IoT Edge en Raspberry Pi 3

Instale las dependencias para Azure IoT Edge:

```sh
sudo apt-get install cmake uuid-dev curl libcurl4-openssl-dev libssl-dev
```

Use los siguientes comandos para clonar IoT Edge y todos los submódulos en el directorio principal:

```sh
cd ~
git clone https://github.com/Azure/iot-edge.git
```

Cuando haya una copia de todo el repositorio de IoT Edge en Raspberry Pi 3, puede crearlo desde la carpeta que contiene el SDK con el comando siguiente:

```sh
cd ~/iot-edge
./tools/build.sh  --disable-native-remote-modules
```

### <a name="configure-and-run-the-ble-sample-on-your-raspberry-pi-3"></a>Configuración y ejecución del ejemplo de BLE en el Raspberry Pi 3

Para arrancar y ejecutar el ejemplo, debe configurar todos los módulos de IoT Edge que participan en la puerta de enlace. Esta configuración se proporciona en un archivo JSON y debe configurar los cinco módulos de IoT Edge participantes. Se proporciona un archivo JSON de ejemplo en el repositorio denominado **gateway\_sample.json** como punto de partida para crear su propio archivo de configuración. Este archivo se encuentra en la carpeta **samples/ble_gateway/src** de la copia local del repositorio de IoT Edge.

En las secciones siguientes se describe cómo editar este archivo de configuración para el ejemplo de BLE y se da por hecho que el repositorio de IoT Edge se encuentra en la carpeta **/home/pi/iot-edge/** del Raspberry Pi 3. Si el repositorio se encuentra en otro lugar, ajuste las rutas de acceso según corresponda.

#### <a name="logger-configuration"></a>Configuración del registrador

Suponiendo que el repositorio de la puerta de enlace se encuentra en la carpeta **/home/pi/iot-edge/**, configure el módulo del registrador de la siguiente forma:

```json
{
  "name": "Logger",
  "loader": {
    "name" : "native",
    "entrypoint" : {
      "module.path" : "build/modules/logger/liblogger.so"
    }
  },
  "args":
  {
    "filename": "<</path/to/log-file.log>>"
  }
}
```

#### <a name="ble-module-configuration"></a>Configuración del módulo BLE

La configuración de ejemplo para el dispositivo BLE supone un dispositivo SensorTag de Texas Instruments. Cualquier dispositivo BLE estándar que funcione como periférico GATT debería valer, pero es posible que necesite actualizar los identificadores de las características GATT y los datos. Agregue la dirección MAC del dispositivo SensorTag:

```json
{
  "name": "SensorTag",
  "loader": {
    "name" : "native",
    "entrypoint" : {
      "module.path": "build/modules/ble/libble.so"
    }
  },
  "args": {
    "controller_index": 0,
    "device_mac_address": "<<AA:BB:CC:DD:EE:FF>>",
    "instructions": [
      {
        "type": "read_once",
        "characteristic_uuid": "00002A24-0000-1000-8000-00805F9B34FB"
      },
      {
        "type": "read_once",
        "characteristic_uuid": "00002A25-0000-1000-8000-00805F9B34FB"
      },
      {
        "type": "read_once",
        "characteristic_uuid": "00002A26-0000-1000-8000-00805F9B34FB"
      },
      {
        "type": "read_once",
        "characteristic_uuid": "00002A27-0000-1000-8000-00805F9B34FB"
      },
      {
        "type": "read_once",
        "characteristic_uuid": "00002A28-0000-1000-8000-00805F9B34FB"
      },
      {
        "type": "read_once",
        "characteristic_uuid": "00002A29-0000-1000-8000-00805F9B34FB"
      },
      {
        "type": "write_at_init",
        "characteristic_uuid": "F000AA02-0451-4000-B000-000000000000",
        "data": "AQ=="
      },
      {
        "type": "read_periodic",
        "characteristic_uuid": "F000AA01-0451-4000-B000-000000000000",
        "interval_in_ms": 1000
      },
      {
        "type": "write_at_exit",
        "characteristic_uuid": "F000AA02-0451-4000-B000-000000000000",
        "data": "AA=="
      }
    ]
  }
}
```

Si no usa un dispositivo SensorTag, revise la documentación del dispositivo BLE para determinar si necesita actualizar los identificadores de las características GATT y los valores de datos.

#### <a name="iot-hub-module"></a>módulo de IoT Hub

Agregue el nombre de su centro de IoT. El sufijo suele ser **azure-devices.net**:

```json
{
  "name": "IoTHub",
  "loader": {
    "name" : "native",
    "entrypoint" : {
      "module.path": "build/modules/iothub/libiothub.so"
    }
  },
  "args": {
    "IoTHubName": "<<Azure IoT Hub Name>>",
    "IoTHubSuffix": "<<Azure IoT Hub Suffix>>",
    "Transport" : "amqp"
  }
}
```

#### <a name="identity-mapping-module-configuration"></a>Configuración del módulo de asignación de identidad

Agregue la dirección MAC del dispositivo SensorTag, y el identificador del dispositivo y la clave del dispositivo **SensorTag_01** agregado al centro de IoT:

```json
{
  "name": "mapping",
  "loader": {
    "name" : "native",
    "entrypoint" : {
      "module.path": "build/modules/identitymap/libidentity_map.so"
    }
  },
  "args": [
    {
      "macAddress": "<<AA:BB:CC:DD:EE:FF>>",
      "deviceId": "<<Azure IoT Hub Device ID>>",
      "deviceKey": "<<Azure IoT Hub Device Key>>"
    }
  ]
}
```

#### <a name="ble-printer-module-configuration"></a>Configuración de la impresora BLE

```json
{
  "name": "BLE Printer",
  "loader": {
    "name" : "native",
    "entrypoint" : {
      "module.path": "build/samples/ble_gateway/ble_printer/libble_printer.so"
    }
  },
  "args": null
}
```

#### <a name="blec2d-module-configuration"></a>Configuración del módulo BLEC2D

```json
{
  "name": "BLEC2D",
  "loader": {
    "name" : "native",
    "entrypoint" : {
      "module.path": "build/modules/ble/libble_c2d.so"
    }
  },
  "args": null
}
```

#### <a name="routing-configuration"></a>Configuración de enrutamiento

La configuración siguiente asegura el enrutamiento entre módulos de IoT Edge que aparece a continuación:

* El módulo **Registrador** recibe y registra todos los mensajes.
* El módulo **SensorTag** envía mensajes a los módulos de **asignación** e **impresora BLE**.
* El módulo de **asignación** envía mensajes al módulo **IoTHub** para que los envíe a su centro de IoT.
* El módulo **IoTHub** envía mensajes de vuelta al módulo de **asignación**.
* El módulo de **asignación** envía mensajes al módulo **BLEC2D**.
* El módulo **BLEC2D** envía mensajes de vuelta al módulo **Sensor Tag**.

```json
"links" : [
    {"source" : "*", "sink" : "Logger" },
    {"source" : "SensorTag", "sink" : "mapping" },
    {"source" : "SensorTag", "sink" : "BLE Printer" },
    {"source" : "mapping", "sink" : "IoTHub" },
    {"source" : "IoTHub", "sink" : "mapping" },
    {"source" : "mapping", "sink" : "BLEC2D" },
    {"source" : "BLEC2D", "sink" : "SensorTag"}
 ]
```

Para ejecutar el ejemplo, pase la ruta de acceso al archivo de configuración de JSON como un parámetro al archivo binario **ble\_gateway**. El siguiente comando presupone que va a usar el archivo de configuración **gateway_sample.json**. Ejecute este comando desde la carpeta **iot-edge** en Raspberry Pi:

```sh
./build/samples/ble_gateway/ble_gateway ./samples/ble_gateway/src/gateway_sample.json
```

Presione el botoncito del dispositivo SensorTag para que se pueda detectar antes de ejecutar el ejemplo.

Cuando ejecute el ejemplo, puede usar las herramientas [Explorador de dispositivos](https://github.com/Azure/azure-iot-sdk-csharp/blob/master/tools/DeviceExplorer) o [iothub-explorer](https://github.com/Azure/iothub-explorer) para supervisar los mensajes que la puerta de enlace de IoT Edge remite desde el dispositivo SensorTag. Por ejemplo, mediante iothub-explorer, puede supervisar los mensajes del dispositivo a la nube con el comando siguiente:

```sh
iothub-explorer monitor-events --login "HostName={Your iot hub name}.azure-devices.net;SharedAccessKeyName=iothubowner;SharedAccessKey={Your IoT Hub key}"
```

## <a name="send-cloud-to-device-messages"></a>Envío de mensajes de nube a dispositivo

El módulo BLE también admite el envío de comandos de IoT Hub al dispositivo. Puede usar el [Explorador de dispositivos ](https://github.com/Azure/azure-iot-sdk-csharp/blob/master/tools/DeviceExplorer) o [iothub-explorer](https://github.com/Azure/iothub-explorer) para enviar mensajes JSON que el módulo de puerta de enlace BLE reenvía al dispositivo BLE.

Si está utilizando el dispositivo SensorTag de Texas Instruments, a continuación, puede activar el LED rojo o verde, o el timbre mediante el envío de comandos desde IoT Hub. Antes de enviar comandos desde IoT Hub, envíe primero los siguientes dos mensajes JSON en orden. A continuación, puede enviar cualquiera de los comandos para activar las luces o timbre.

1. Restablezca todos los LED y el timbre (desactívelos):

    ```json
    {
      "type": "write_once",
      "characteristic_uuid": "F000AA65-0451-4000-B000-000000000000",
      "data": "AA=="
    }
    ```

1. Configure E/S como "remoto":

    ```json
    {
      "type": "write_once",
      "characteristic_uuid": "F000AA66-0451-4000-B000-000000000000",
      "data": "AQ=="
    }
    ```

Ahora, puede enviar cualquiera de los comandos siguientes para activar las luces o timbre en el dispositivo SensorTag:

* Active el LED rojo:

    ```json
    {
      "type": "write_once",
      "characteristic_uuid": "F000AA65-0451-4000-B000-000000000000",
      "data": "AQ=="
    }
    ```

* Active el LED verde:

    ```json
    {
      "type": "write_once",
      "characteristic_uuid": "F000AA65-0451-4000-B000-000000000000",
      "data": "Ag=="
    }
    ```

* Active el timbre:

    ```json
    {
      "type": "write_once",
      "characteristic_uuid": "F000AA65-0451-4000-B000-000000000000",
      "data": "BA=="
    }
    ```

## <a name="next-steps"></a>Pasos siguientes

Si desea una descripción más avanzada de IoT Edge y experimentar con algunos ejemplos de código, consulte los siguientes tutoriales y recursos para desarrolladores:

* [Azure IoT Edge][lnk-sdk]

Para explorar aún más las funcionalidades de IoT Hub, consulte:

* [Guía para desarrolladores de IoT Hub][lnk-devguide]

<!-- Links -->
[lnk-ble-samplecode]: https://github.com/Azure/iot-edge/tree/master/samples/ble_gateway
[lnk-free-trial]: https://azure.microsoft.com/pricing/free-trial/
[lnk-explorer-tools]: https://github.com/Azure/azure-iot-sdks/blob/master/doc/manage_iot_hub.md
[lnk-sdk]: https://github.com/Azure/iot-edge/
[lnk-noobs]: https://www.raspberrypi.org/documentation/installation/noobs.md
[lnk-raspbian]: https://www.raspberrypi.org/downloads/raspbian/
[lnk-devguide]: iot-hub-devguide.md
[lnk-create-hub]: iot-hub-create-through-portal.md 
[lnk-pi-ssh]: https://www.raspberrypi.org/documentation/remote-access/ssh/README.md
[lnk-ssh-windows]: https://www.raspberrypi.org/documentation/remote-access/ssh/windows.md
[lnk-ssh-linux]: https://www.raspberrypi.org/documentation/remote-access/ssh/unix.md

