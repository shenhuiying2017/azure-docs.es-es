---
title: Conexión de un dispositivo DevKit a una aplicación de Azure IoT Central | Microsoft Docs
description: Como desarrollador de dispositivos, aprenda a conectar un dispositivo MXChip IoT DevKit a una aplicación de Azure IoT Central.
services: iot-central
author: tanmaybhagwat
ms.author: tanmayb
ms.date: 04/16/2018
ms.topic: article
ms.prod: microsoft-iot-central
manager: timlt
ms.openlocfilehash: 4c7074e5e7d3858919f3fc17005fea4f8dce1560
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/16/2018
ms.locfileid: "34200748"
---
# <a name="connect-an-mxchip-iot-devkit-device-to-your-azure-iot-central-application"></a>Conexión de un dispositivo MXChip IoT DevKit a una aplicación de Microsoft IoT Central

En este artículo se describe cómo conectar, en tanto que desarrollador de dispositivos, un dispositivo MXChip IoT DevKit (DevKit) a una aplicación de Microsoft Azure IoT Central.

## <a name="before-you-begin"></a>Antes de empezar

Necesitará lo siguiente para completar los pasos de este artículo:

1. Una aplicación de Azure IoT Central creada a partir de la plantilla de aplicación **Ejemplo Devkits**. Para más información, consulte [Create your Azure IoT Central Application](howto-create-application.md) (Creación de una aplicación de Azure IoT Central).
1. Un dispositivo DevKit. Para adquirir un dispositivo DevKit, visite [MXChip IoT DevKit](http://mxchip.com/az3166).

Una aplicación creada a partir de la plantilla de aplicación **Ejemplo Devkits** incluye una plantilla de dispositivo **MXChip** con las siguientes características:

### <a name="telemetry-measurements"></a>Medidas de telemetría

| Nombre del campo     | Unidades  | Mínima | Máxima | Posiciones decimales |
| -------------- | ------ | ------- | ------- | -------------- |
| humedad       | %      | 0       | 100     | 0              |
| temp           | °C     | -40     | 120     | 0              |
| pressure       | hPa    | 260     | 1260    | 0              |
| magnetometerX (magnetómetro X)  | mgauss | -1000   | 1000    | 0              |
| magnetometerY (magnetómetro Y)  | mgauss | -1000   | 1000    | 0              |
| magnetometerZ (magnetómetro Z)  | mgauss | -1000   | 1000    | 0              |
| accelerometerX (acelerómetro X) | mg     | -2000   | 2000    | 0              |
| accelerometerY (acelerómetro Y) | mg     | -2000   | 2000    | 0              |
| accelerometerZ (acelerómetro Z) | mg     | -2000   | 2000    | 0              |
| gyroscopeX (giróscopo X)     | mdps   | -2000   | 2000    | 0              |
| gyroscopeY (giróscopo Y)     | mdps   | -2000   | 2000    | 0              |
| gyroscopeZ (giróscopo Z)     | mdps   | -2000   | 2000    | 0              |

### <a name="settings"></a>Settings

Valores numéricos

| Nombre para mostrar | Nombre del campo | Unidades | Posiciones decimales | Mínima | Máxima | Inicial |
| ------------ | ---------- | ----- | -------------- | ------- | ------- | ------- |
| Voltage (Voltaje)      | setVoltage | Voltios | 0              | 0       | 240     | 0       |
| Current      | setCurrent | Amp  | 0              | 0       | 100     | 0       |
| Fan Speed (Velocidad del ventilador)    | fanSpeed   | RPM   | 0              | 0       | 1000    | 0       |

Cambiar configuración

| Nombre para mostrar | Nombre del campo | Texto activado | Texto desactivado | Inicial |
| ------------ | ---------- | ------- | -------- | ------- |
| IR           | activateIR | ACTIVAR      | Apagado      | Off     |

### <a name="properties"></a>Properties (Propiedades)

| Escriba            | Nombre para mostrar | Nombre del campo | Tipo de datos |
| --------------- | ------------ | ---------- | --------- |
| Propiedad de dispositivo | Die number (Número de chip)   | dieNumber  | número    |
| Texto            | Ubicación     | location   | N/D       |

### <a name="states"></a>States 

| NOMBRE          | Nombre para mostrar   | NORMAL | PRECAUCIÓN | PELIGRO | 
| ------------- | -------------- | ------ | ------- | ------ | 
| DeviceState   | Device State (Estado del dispositivo)   | Verde  | Naranja  | Rojo    | 

### <a name="events"></a>Eventos 

| NOMBRE             | Nombre para mostrar      | 
| ---------------- | ----------------- | 
| ButtonBPressed   | Button B Pressed (Botón B presionado)  | 

### <a name="add-a-real-device"></a>Adición de un dispositivo real

En su aplicación de Azure IoT Central, agregue un dispositivo real de la plantilla de dispositivo **MXChip** y tome nota de la cadena de conexión del dispositivo. Para obtener más información, consulte [Add a real device to your Azure IoT Central application](tutorial-add-device.md) (Adición de un dispositivo real a su aplicación de Azure IoT Central).

## <a name="prepare-the-devkit-device"></a>Preparación del dispositivo DevKit

> [!TIP]
> Para obtener instrucciones sobre la solución de problemas de los dispositivos, consulte [IoT DevKit get started](https://microsoft.github.io/azure-iot-developer-kit/docs/get-started/) (Introducción a IoT DevKit).

Para preparar el dispositivo DevKit:

1. Descargue el firmware de Azure IoT Central precompilado más reciente para MXChip desde la página [releases](https://github.com/Azure/iot-central-firmware/releases) (versiones) en GitHub. El nombre de archivo de descarga de la página de versiones es similar a `AZ3166-IoT-Central-X.X.X.bin`.

1. Conecte el dispositivo DevKit en el equipo de desarrollo con un cable USB. En Windows, se abre una ventana del explorador de archivos en una unidad asignada al almacenamiento en el dispositivo DevKit. Por ejemplo, la unidad podría denominarse **AZ3166 (D:)**.

1. Arrastre el archivo **iotCentral.bin** a la ventana de la unidad. Cuando se complete la operación de copia, el dispositivo se reinicia con el nuevo firmware.

1. Cuando se reinicie el dispositivo DevKit, se muestra la siguiente pantalla:

    ```
    Connect HotSpot:
    AZ3166_??????
    go-> 192.168.0.1 
    PIN CODE xxxxx
    ```

    > [!NOTE]
    > Si la pantalla muestra otra cosa, presione el botón **Reset** (Restablecer) en el dispositivo. 

1. El dispositivo ya está en modo de punto de acceso. Puede conectarse a este punto de acceso Wi-Fi desde su equipo o dispositivo móvil.

1. En el equipo, teléfono o tableta, conéctese al nombre de red Wi-Fi que se muestra en la pantalla del dispositivo. Cuando se conecta a la red, no tiene acceso a Internet. Este estado es el esperado, y solo se conecta a esta red durante un período corto mientras se configura el dispositivo.

1. Abra el explorador web y vaya a [http://192.168.0.1/start](http://192.168.0.1/start). Aparece la siguiente página web:

    ![Página de configuración del dispositivo](media/howto-connect-devkit/configpage.png)

    En la página web: 
    - agregue el nombre de su red Wi-Fi 
    - la contraseña de la red Wi-Fi 
    - el PIN CODE (CÓDIGO PIN) que se muestra en el dispositivo LCD 
    - la cadena de conexión del dispositivo. 
      Puede encontrar la cadena de conexión @ `https://apps.iotcentral.com` -> `Device Explorer` -> `Device` -> `Select or Create a new Real Device` -> `Connect this device` (en la esquina superior derecha) 
    - Seleccione todas las mediciones de telemetría disponibles. 

1. Después de elegir **Configure Device** (Configurar dispositivo), verá esta página:

    ![Dispositivo configurado](media/howto-connect-devkit/deviceconfigured.png)

1. Presione el botón **Reset**  (Restablecer) en el dispositivo.

> [!NOTE]
> Para volver a configurar el dispositivo para que utilice una red Wi-Fi, cadena de conexión o medida de telemetría diferente, presione los botones **A** y **B** del panel de manera simultánea. Si no funciona, presione el botón **reset** (restablecer) e inténtelo de nuevo. 

## <a name="view-the-telemetry"></a>Visualización de la telemetría

Cuando se reinicie el dispositivo DevKit, la pantalla del dispositivo muestra:

* El número de mensajes de telemetría enviados.
* El número de errores.
* El número de propiedades deseadas recibidas y el número de propiedades notificadas enviadas.

Al agitar el dispositivo se incrementa el número de propiedades notificadas enviadas. El dispositivo envía un número aleatorio como propiedad del dispositivo **Die number** (Número de chip).

Puede ver las mediciones de telemetría y los valores de propiedad notificados y configurar los parámetros en Azure IoT Central:

1. Use **Device Explorer** (Explorador de dispositivos) para navegar hasta la página **Measurements** (Medidas) del dispositivo MXChip real que se ha agregado:

    ![Navegación al dispositivo real](media/howto-connect-devkit/realdevice.png)

1. En la página **Measurements** (Medidas), puede ver la telemetría proveniente del dispositivo de MXChip:

    ![Visualización de telemetría desde el dispositivo real](media/howto-connect-devkit/realtelemetry.png)

1. En la página **Properties** (Propiedades), puede ver el último número de chip notificado por el dispositivo:

    ![Visualización de propiedades del dispositivo](media/howto-connect-devkit/deviceproperties.png)

1. En la página **Settings** (Configuración), puede actualizar la configuración en el dispositivo MXChip:

    ![Visualización de la configuración del dispositivo](media/howto-connect-devkit/settings.png)

## <a name="download-the-source-code"></a>Descarga del código fuente

Si desea explorar y modificar el código del dispositivo, puede descargarlo desde GitHub. Si tiene previsto modificar el código, debe seguir estas instrucciones para [preparar el entorno de desarrollo](https://microsoft.github.io/azure-iot-developer-kit/docs/get-started/#step-5-prepare-the-development-environment) para su sistema operativo de escritorio.

Para descargar el código fuente, ejecute el siguiente comando en el equipo de escritorio:

```cmd/sh
git clone https://github.com/Azure/iot-central-firmware
```

El ejemplo anterior descarga el código fuente en una carpeta denominada `iot-central-firmware`. 

> [!NOTE]
> Si **git** no está instalado en el entorno de desarrollo, puede descargarlo desde [https://git-scm.com/download](https://git-scm.com/download).

## <a name="review-the-code"></a>Revisión del código

Use Visual Studio Code, que se instaló al preparar el entorno de desarrollo, para abrir la carpeta `AZ3166` en la carpeta `iot-central-firmware`: 

![Visual Studio Code](media/howto-connect-devkit/vscodeview.png)

Para ver cómo se envía la telemetría a la aplicación de Azure IoT Central, abra el archivo **main_telemetry.cpp** en la carpeta de origen.

La función `buildTelemetryPayload` crea la carga de telemetría de JSON utilizando los datos de los sensores en el dispositivo.

La función `sendTelemetryPayload` llama a `sendTelemetry` en **iotHubClient.cpp** para enviar la carga de JSON a la instancia de IoT Hub que usa su aplicación de Azure IoT Central.

Para ver cómo se notifican los valores de propiedad a la aplicación de Azure IoT Central, abra el archivo **main_telemetry.cpp** en la carpeta de origen.

La función `telemetryLoop` envía la propiedad notificada **doubleTap** cuando el acelerómetro detecta un doble punteo. Usa la función `sendReportedProperty` en el archivo de origen **iotHubClient.cpp**.

El código del archivo de origen **iotHubClient.cpp** utiliza funciones de los [SDK y las bibliotecas de Microsoft Azure IoT para C](https://github.com/Azure/azure-iot-sdk-c) para interactuar con IoT Hub.

Para obtener información acerca de cómo modificar, compilar y cargar el código de ejemplo en el dispositivo, consulte el archivo **readme.md** en la carpeta `AZ3166`.

## <a name="next-steps"></a>Pasos siguientes

Ahora que ha aprendido cómo conectar un dispositivo DevKit a la aplicación de Azure IoT Central, estos son los siguientes pasos sugeridos:

* [Preparación y conexión de una instancia de Raspberry Pi](howto-connect-raspberry-pi-python.md)