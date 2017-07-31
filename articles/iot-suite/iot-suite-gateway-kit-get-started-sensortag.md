---
title: Conectar una puerta de enlace al Conjunto de aplicaciones de IoT de Azure con Intel NUC | Documentos de Microsoft
description: "Use el Kit de puerta de enlace comercial de Microsoft IoT y la solución preconfigurada de supervisión remota. Use la puerta de enlace de Azure IoT Edge para habilitar un dispositivo SensorTag para conectarse a la solución de supervisión remota, enviar telemetría a la nube y responder a los métodos invocados desde el panel de soluciones."
services: 
suite: iot-suite
documentationcenter: 
author: dominicbetts
manager: timlt
editor: 
ms.service: iot-suite
ms.devlang: c
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/24/2017
ms.author: dobett
ms.translationtype: Human Translation
ms.sourcegitcommit: 9edcaee4d051c3dc05bfe23eecc9c22818cf967c
ms.openlocfilehash: e97f22340f869ef31d7b4c7b9857bf0d1d5400c5
ms.contentlocale: es-es
ms.lasthandoff: 06/08/2017

---
# <a name="connect-your-azure-iot-edge-gateway-to-the-remote-monitoring-preconfigured-solution-and-send-telemetry-from-a-sensortag"></a>Conectar la puerta de enlace de Azure IoT Edge a la solución preconfigurada de supervisión remota y enviar telemetría desde un dispositivo SensorTag

[!INCLUDE [iot-suite-gateway-kit-selector](../../includes/iot-suite-gateway-kit-selector.md)]

En este tutorial se muestra cómo usar Azure IoT Edge para enviar datos de temperatura y humedad desde un dispositivo SensorTag a la solución preconfigurada de supervisión remota. El dispositivo SensorTag se conecta a la puerta de enlace de Intel NUC con Bluetooth. El tutorial usa:

- Azure IoT Edge para implementar una puerta de enlace de muestra.
- La solución preconfigurada de supervisión remota del Conjunto de aplicaciones de IoT como el back-end basado en la nube.

## <a name="overview"></a>Información general

En este tutorial, va a completar los siguientes pasos:

- Implemente una instancia de la solución preconfigurada de supervisión remota en su suscripción de Azure. Este paso implementa y configura varios servicios de Azure automáticamente.
- Configure el dispositivo de puerta de enlace Intel NUC para que se comunique con el equipo y la solución de supervisión remota.
- Configure la puerta de enlace Intel NUC para recibir datos de telemetría desde un dispositivo SensorTag y enviarla al panel de supervisión remota.

[!INCLUDE [iot-suite-gateway-kit-prerequisites](../../includes/iot-suite-gateway-kit-prerequisites.md)]

[SensorTag de Texas Instruments BLE][lnk-sensortag]. En este tutorial se recuperan los datos de telemetría por Bluetooth desde el dispositivo SensorTag.

[!INCLUDE [iot-suite-provision-remote-monitoring](../../includes/iot-suite-provision-remote-monitoring.md)]

> [!WARNING]
> La solución de supervisión remota proporciona un conjunto de servicios de Azure de la suscripción de Azure. La implementación refleja una arquitectura empresarial real. Para evitar cobros de consumo innecesarios de Azure, elimine la instancia de la solución preconfigurada en azureiotsuite.com cuando haya terminado con ella. Si necesita la solución preconfigurada de nuevo, puede crearla fácilmente. Para más información sobre cómo reducir el consumo mientras se ejecuta la solución de supervisión remota, consulte [Configuring Azure IoT Suite preconfigured solutions for demo purposes][lnk-demo-config] (Configuración de soluciones preconfiguradas del Conjunto de aplicaciones de IoT de Azure para fines de demostración).

[!INCLUDE [iot-suite-gateway-kit-view-solution](../../includes/iot-suite-gateway-kit-view-solution.md)]

[!INCLUDE [iot-suite-gateway-kit-prepare-nuc-connectivity](../../includes/iot-suite-gateway-kit-prepare-nuc-connectivity.md)]

## <a name="configure-bluetooth-connectivity"></a>Configurar la conectividad de Bluetooth

Configure Bluetooth en Intel NUC para habilitar el dispositivo SensorTag para conectarse y enviar telemetría.

### <a name="find-the-mac-address-of-the-sensortag"></a>Buscar la dirección MAC del SensorTag

1. En el shell de Intel NUC, ejecute el siguiente comando para desbloquear el servicio de Bluetooth:

    ```bash
    sudo rfkill unblock bluetooth
    ```

1. Ejecute los comandos siguientes para iniciar el servicio de Bluetooth en Intel NUC y escriba el shell de Bluetooth:

    ```bash
    sudo systemctl start bluetooth
    bluetoothctl
    ```

1. Ejecute el siguiente comando para activar el controlador de Bluetooth:

    ```bash
    power on
    ```

    Cuando el controlador esté activado, verá el mensaje **Changing power on succeeded** (El cambio de estado de activación se realizó correctamente).

1. Ejecute el comando siguiente para detectar dispositivos Bluetooth cercanos:

    ```bash
    scan on
    ```

1. Presione el botón de encendido en el SensorTag para que sea reconocible. El LED verde parpadea.

1. Cuando vea un mensaje en el shell que indique que el controlador ha detectado el SensorTag, anote la dirección MAC del dispositivo. La dirección MAC es similar a **A0:E6:F8:B5:F6:00**. Necesitará la dirección MAC más adelante en el tutorial cuando configure la puerta de enlace.

1. Ejecute el siguiente comando para desactivar la detección de Bluetooth:

    ```bash
    scan off
    ```

1. Ejecute el siguiente comando para comprobar si puede conectarse al dispositivo SensorTag:

    ```bash
    connect <SensorTag MAC address>
    ```

    Si se conecta correctamente, el shell muestra un mensaje **Conexión correcta** e imprime información acerca del dispositivo SensorTag. Si no puede conectarse, compruebe que SensorTag todavía esté encendido.

1. Ahora puede desconectarse del SensorTag y salir del shell de Bluetooth. Para ello, ejecute los comandos siguientes:

    ```bash
    disconnect
    exit
    ```

[!INCLUDE [iot-suite-gateway-kit-prepare-nuc-software](../../includes/iot-suite-gateway-kit-prepare-nuc-software.md)]

## <a name="build-the-custom-iot-edge-module"></a>Generar el módulo de IoT Edge personalizado

Ahora puede generar el módulo personalizado de IoT Edge que permite que la puerta de enlace envíe mensajes a la solución de supervisión remota. Para obtener más información sobre cómo configurar una puerta de enlace y los módulos de IoT Edge, consulte [Conceptos de Azure IoT Edge][lnk-gateway-concepts].

Descargue el código fuente para los módulos personalizados de IoT Edge desde GitHub con los comandos siguientes:

```bash
cd ~
git clone https://github.com/Azure-Samples/iot-remote-monitoring-c-intel-nuc-gateway-getting-started.git
```

Cree el módulo personalizado de IoT Edge con los comandos siguientes:

```bash
cd ~/iot-remote-monitoring-c-intel-nuc-gateway-getting-started/basic
chmod u+x build.sh
sed -i -e 's/\r$//' build.sh
./build.sh
```

El script de compilación coloca el módulo personalizado de IoT Edge libsensor2remotemonitoring.so en la carpeta de compilación.

## <a name="configure-and-run-the-iot-edge-gateway"></a>Configurar y ejecutar la puerta de enlace de IoT Edge

Ahora puede configurar la puerta de enlace de IoT Edge para enviar telemetría desde su dispositivo SensorTag al panel de supervisión remota. Para obtener más información sobre cómo configurar una puerta de enlace y los módulos de IoT Edge, consulte [Conceptos de Azure IoT Edge][lnk-gateway-concepts].

> [!TIP]
> En este tutorial, use el editor de texto `vi` estándar en Intel NUC. Si no ha usado `vi` antes, complete un tutorial introductorio, como [Unix - The vi Editor Tutorial][lnk-vi-tutorial] (Unix: el tutorial de editor vi) para familiarizarse con este editor. Como alternativa, puede instalar el editor [nano](https://www.nano-editor.org/) fácil de usar con el comando `smart install nano -y`.

Abra el archivo de configuración de ejemplo en el editor **vi** con el comando siguiente:

```bash
vi ~/iot-remote-monitoring-c-intel-nuc-gateway-getting-started/basic/remote_monitoring.json
```

Busque las líneas siguientes en la configuración del módulo de IoTHub:

```json
"args": {
  "IoTHubName": "<<Azure IoT Hub Name>>",
  "IoTHubSuffix": "<<Azure IoT Hub Suffix>>",
  "Transport": "http"
}
```

Reemplace los valores de marcador de posición con la información de IoT Hub que creó y guardó al principio de este tutorial. El valor de IoTHubName es similar a **yourrmsolution37e08** y el valor de IoTSuffix suele ser **azure-devices.net**.

Busque las líneas siguientes en la configuración del módulo de asignación:

```json
args": [
  {
    "macAddress": "<<AA:BB:CC:DD:EE:FF>>",
    "deviceId": "<<Azure IoT Hub Device ID>>",
    "deviceKey": "<<Azure IoT Hub Device Key>>"
  }
]
```

Reemplace el marcador de posición **macAddress** con la dirección MAC del dispositivo SensorTag que ha anotado anteriormente. Reemplace los marcadores de posición **deviceID** y **deviceKey** con los identificadores y las claves para los dos dispositivos que creó anteriormente en la solución de supervisión remota.

Busque las líneas siguientes en la configuración del módulo de SensorTag:

```json
"args": {
  "controller_index": 0,
  "device_mac_address": "<<AA:BB:CC:DD:EE:FF>>",
  ...
}
```

Reemplace el marcador de posición **device\_mac\_address** con la dirección MAC del dispositivo SensorTag que ha anotado anteriormente.

Guarde los cambios.

Ahora puede usar estos comandos para ejecutar la puerta de enlace:

```bash
cd ~/iot-remote-monitoring-c-intel-nuc-gateway-getting-started/basic
/usr/share/azureiotgatewaysdk/samples/ble_gateway/ble_gateway remote_monitoring.json
```

La puerta de enlace de IoT Edge se inicia en Intel NUC y envía telemetría desde el dispositivo SensorTag a la solución de supervisión remota:

![La puerta de enlace de IoT Edge envía telemetría desde SensorTag.][img-telemetry]

Presione **Ctrl-C** para salir del programa en cualquier momento.

## <a name="view-the-telemetry"></a>Visualización de la telemetría

Ahora, la puerta de enlace envía telemetría desde el dispositivo SensorTag a la solución de supervisión remota. Puede verla en el panel de soluciones. También puede enviar comandos al dispositivo SensorTag por la puerta de enlace desde el panel de soluciones.

- Vaya al panel de soluciones.
- Seleccione el dispositivo que configuró en la puerta de enlace que representa al dispositivo SensorTag en la lista desplegable **Dispositivo que se visualizará**.
- La telemetría del dispositivo SensorTag se muestra en el panel.

![Mostrar la telemetría del dispositivo SensorTag][img-telemetry-display]

> [!WARNING]
> Si deja la solución de supervisión remota ejecutándose en su cuenta de Azure, se le cobra por el tiempo que se ejecute. Para más información sobre cómo reducir el consumo mientras se ejecuta la solución de supervisión remota, consulte [Configuring Azure IoT Suite preconfigured solutions for demo purposes][lnk-demo-config] (Configuración de soluciones preconfiguradas del Conjunto de aplicaciones de IoT de Azure para fines de demostración). Elimine la solución preconfigurada de su cuenta de Azure cuando haya terminado de usarla.


## <a name="next-steps"></a>Pasos siguientes

Visite el [Centro para desarrolladores de IoT de Azure](https://azure.microsoft.com/develop/iot/) para obtener más ejemplos y la documentación de IoT de Azure.

[img-telemetry]: ./media/iot-suite-gateway-kit-get-started-sensortag/appoutput.png


[img-telemetry-display]: ./media/iot-suite-gateway-kit-get-started-sensortag/telemetry.png

[lnk-demo-config]: https://github.com/Azure/azure-iot-remote-monitoring/blob/master/Docs/configure-preconfigured-demo.md
[lnk-vi-tutorial]: http://www.tutorialspoint.com/unix/unix-vi-editor.htm
[lnk-sensortag]: http://www.ti.com/ww/en/wireless_connectivity/sensortag/
[lnk-gateway-concepts]: https://docs.microsoft.com/azure/iot-hub/iot-hub-linux-iot-edge-get-started
