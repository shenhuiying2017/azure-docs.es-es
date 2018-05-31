---
title: Conexión de un dispositivo Raspberry Pi a una aplicación de Azure IoT Central (Python) | Microsoft Docs
description: Como desarrollador de dispositivos, aprenderá a conectar Raspberry Pi a su aplicación de Azure IoT Central mediante Python.
services: iot-central
author: dominicbetts
ms.author: dobett
ms.date: 01/23/2018
ms.topic: article
ms.prod: microsoft-iot-central
manager: timlt
ms.openlocfilehash: 23ab31d6d2357bfcb184e5b3022155bef5ace658
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/16/2018
ms.locfileid: "34199884"
---
# <a name="connect-a-raspberry-pi-to-your-azure-iot-central-application-python"></a>Conexión de un dispositivo Raspberry Pi a una aplicación de Azure IoT Central (Python)

[!INCLUDE [howto-raspberrypi-selector](../../includes/iot-central-howto-raspberrypi-selector.md)]

En este artículo se describe el modo de conectar, como desarrollador de dispositivos, un dispositivo Raspberry Pi a una aplicación de Microsoft Azure IoT Central con el lenguaje de programación Python.

## <a name="before-you-begin"></a>Antes de empezar

Necesitará lo siguiente para completar los pasos de este artículo:

* Una aplicación de Azure IoT Central creada a partir de la plantilla de aplicación **Ejemplo Devkits**. Para más información, consulte [Create your Azure IoT Central Application](howto-create-application.md) (Creación de una aplicación de Azure IoT Central).
* Un dispositivo Raspberry Pi que ejecuta el sistema operativo Raspbian. Necesitará conectar un monitor, un teclado y un mouse (ratón) a su Raspberry Pi para acceder al entorno de interfaz gráfica de usuario. Raspberry Pi debe poder [conectarse a Internet](https://www.raspberrypi.org/learning/software-guide/wifi/).
* Opcionalmente, una placa complementaria [Sense Hat](https://www.raspberrypi.org/products/sense-hat/) para el dispositivo Raspberry Pi. Esta placa recopila datos de telemetría de diversos sensores y los envía a la aplicación de Azure IoT Central. Si no tiene una placa **Sense Hat**, puede usar un emulador en su lugar.

Una aplicación creada a partir de la plantilla de aplicación **Ejemplo Devkits** incluye una plantilla de dispositivo **Raspberry Pi** con las siguientes características:

### <a name="telemetry-measurements"></a>Medidas de telemetría

| Nombre del campo     | Unidades  | Mínima | Máxima | Decimal places |
| -------------- | ------ | ------- | ------- | -------------- |
| humedad       | %      | 0       | 100     | 0              |
| temp           | °C     | -40     | 120     | 0              |
| pressure       | hPa    | 260     | 1260    | 0              |
| magnetometerX  | mgauss | -1000   | 1000    | 0              |
| magnetometerY  | mgauss | -1000   | 1000    | 0              |
| magnetometerZ  | mgauss | -1000   | 1000    | 0              |
| accelerometerX | mg     | -2000   | 2000    | 0              |
| accelerometerY | mg     | -2000   | 2000    | 0              |
| accelerometerZ | mg     | -2000   | 2000    | 0              |
| gyroscopeX     | mdps   | -2000   | 2000    | 0              |
| gyroscopeY     | mdps   | -2000   | 2000    | 0              |
| gyroscopeZ     | mdps   | -2000   | 2000    | 0              |

### <a name="settings"></a>Settings

Valores numéricos

| Nombre para mostrar | Nombre del campo | Unidades | Decimal places | Mínima | Máxima | Inicial |
| ------------ | ---------- | ----- | -------------- | ------- | ------- | ------- |
| Voltage      | setVoltage | Volts | 0              | 0       | 240     | 0       |
| Current      | setCurrent | Amps  | 0              | 0       | 100     | 0       |
| Fan Speed    | fanSpeed   | RPM   | 0              | 0       | 1000    | 0       |

Configuración de alternancia

| Nombre para mostrar | Nombre del campo | On text | Off text | Inicial |
| ------------ | ---------- | ------- | -------- | ------- |
| IR           | activateIR | ACTIVAR      | Apagado      | Off     |

### <a name="properties"></a>Properties (Propiedades)

| Escriba            | Nombre para mostrar | Nombre del campo | Tipo de datos |
| --------------- | ------------ | ---------- | --------- |
| Device property | Die number   | dieNumber  | número    |
| Texto            | Ubicación     | location   | N/D       |

### <a name="add-a-real-device"></a>Adición de un dispositivo real

En su aplicación de Azure IoT Central, agregue un dispositivo real desde la plantilla de dispositivo **Raspberry Pi** y anote la cadena de conexión del dispositivo. Para más información, consulte [Add a real device to your Azure IoT Central application](tutorial-add-device.md) (Adición de un dispositivo real a la aplicación de Azure IoT Central).

## <a name="configure-the-raspberry-pi"></a>Configuración del dispositivo Raspberry Pi

En los pasos siguientes se describe cómo descargar y configurar la aplicación de Python de ejemplo desde GitHub. Esta aplicación de ejemplo:

* Envía telemetría y valores de propiedad a Azure IoT Central.
* Responde a los cambios de configuración realizados en Azure IoT Central.

> [!NOTE]
> Para más información sobre el ejemplo de Python de Raspberry Pi, consulte el archivo [Léame](https://github.com/Microsoft/microsoft-iot-central-firmware/blob/master/RaspberryPi/README.md) en GitHub.

1. Use el explorador web de Raspberry Pi para ir a la página de [versiones de firmware de Azure IoT Central](https://github.com/Microsoft/microsoft-iot-central-firmware/releases).

1. Descargue el archivo ZIP que contiene el firmware más reciente a la carpeta principal de Raspberry Pi. El nombre de archivo se parece a este: `RaspberryPi-IoTCentral-X.X.X.zip`.

1. Para descomprimir el archivo de firmware, use **File Manager** (Administrador de archivos) en el escritorio de Raspberry Pi. Haga clic con el botón derecho en el archivo ZIP y elija **Extraer aquí**. Esta operación crea una carpeta llamada `RaspberryPi-IoTCentral-X.X.X` en su carpeta principal.

1. Si no tiene una placa **Sense Hat** conectada a su dispositivo Raspberry Pi, necesitará habilitar un emulador:
    1. En **File Manager** (Administrador de archivos), en la carpeta `RaspberryPi-IoTCentral-X.X.X`, haga clic con el botón derecho en el archivo **config.iot** y elija **Text Editor** (Editor de texto).
    1. Cambie la línea `"simulateSenseHat": false,` por `"simulateSenseHat": true,`.
    1. Guarde los cambios y cierre el **Text Editor** (Editor de texto).

1. Inicie una sesión de **Terminal** y use el comando `cd` para desplazarse hasta la carpeta que creó en el paso anterior.

1. Para iniciar la ejecución de la aplicación de ejemplo, escriba `./start.sh` en la ventana de **Terminal**. Si está usando el **emulador de Sense HAT**, se muestra su interfaz gráfica de usuario. Puede usar la interfaz gráfica de usuario para cambiar los valores de telemetría enviados a la aplicación de Azure IoT Central.

1. La ventana de **Terminal** muestra un mensaje parecido a este: `Device information being served at http://192.168.0.60:8080`. La dirección URL puede ser diferente en su entorno. Copie la dirección URL y use el explorador web para navegar hasta la página de configuración:

    ![Configuración del dispositivo](media/howto-connect-raspberry-pi-python/configure.png)

1. Escriba la cadena de conexión del dispositivo que anotó cuando agregó un dispositivo real a la aplicación de Azure IoT Central. A continuación, elija **Configure Device** (Configurar dispositivo). Verá un mensaje que dice que **el dispositivo está configurado, y deberá comenzar a enviar datos a Azure IoT Central en un momento**.

1. En la aplicación de Azure IoT Central, puede ver cómo el código que se ejecuta en Raspberry Pi interactúa con la aplicación:

    * En la página **Measurements** (Medidas) del dispositivo real, puede ver la telemetría enviada desde Raspberry Pi. Si usa el **emulador de Sense HAT**, puede modificar los valores de telemetría en la interfaz gráfica de usuario de Raspberry Pi.
    * En la página **Properties** (Propiedades), puede ver el valor de la propiedad **Die Number** (Número de chip) notificado.
    * En la página **Settings** (Configuración), puede cambiar distintos parámetros en Raspberry Pi como el voltaje y la velocidad del ventilador. Cuando Raspberry Pi confirma el cambio, el valor se muestra como **synced** (sincronizado) en Azure IoT Central.

## <a name="next-steps"></a>Pasos siguientes

Ahora que ha aprendido cómo conectar un dispositivo Raspberry Pi a la aplicación de Azure IoT Central, estos son los siguientes pasos sugeridos:

* [Conexión de una aplicación cliente de Node.js genérica a Azure IoT Central](howto-connect-nodejs.md)