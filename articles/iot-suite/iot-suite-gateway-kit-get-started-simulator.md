---
title: Conectar una puerta de enlace al Conjunto de aplicaciones de IoT de Azure con Intel NUC | Documentos de Microsoft
description: "Use el Kit de puerta de enlace comercial de Microsoft IoT y la solución preconfigurada de supervisión remota. Use la puerta de enlace de Azure IoT Edge para conectarse a la solución de supervisión remota, enviar telemetría simulada a la nube y responder a los métodos invocados desde el panel de soluciones."
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
ms.date: 07/25/2017
ms.author: dobett
ms.openlocfilehash: 9ed57d3c23e2adbd42c054f33c8ed46e3d6c9792
ms.sourcegitcommit: 02e69c4a9d17645633357fe3d46677c2ff22c85a
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 08/03/2017
---
# <a name="connect-your-azure-iot-edge-gateway-to-the-remote-monitoring-preconfigured-solution-and-send-simulated-telemetry"></a>Conectar la puerta de enlace de Azure IoT Edge a la solución preconfigurada de supervisión remota y enviar telemetría simulada

[!INCLUDE [iot-suite-gateway-kit-selector](../../includes/iot-suite-gateway-kit-selector.md)]

En este tutorial se muestra cómo usar Azure IoT Edge para simular datos de temperatura y humedad y enviarlos a la solución preconfigurada de supervisión remota. El tutorial usa:

- Azure IoT Edge para implementar una puerta de enlace de muestra.
- La solución preconfigurada de supervisión remota del Conjunto de aplicaciones de IoT como el back-end basado en la nube.

## <a name="overview"></a>Información general

En este tutorial, va a completar los siguientes pasos:

- Implemente una instancia de la solución preconfigurada de supervisión remota en su suscripción de Azure. Este paso implementa y configura varios servicios de Azure automáticamente.
- Configure el dispositivo de puerta de enlace Intel NUC para que se comunique con el equipo y la solución de supervisión remota.
- Configure la puerta de enlace de IoT Edge para enviar telemetría simulada que se puede ver en el panel de soluciones.

[!INCLUDE [iot-suite-gateway-kit-prerequisites](../../includes/iot-suite-gateway-kit-prerequisites.md)]

[!INCLUDE [iot-suite-provision-remote-monitoring](../../includes/iot-suite-provision-remote-monitoring.md)]

> [!WARNING]
> La solución de supervisión remota proporciona un conjunto de servicios de Azure de la suscripción de Azure. La implementación refleja una arquitectura empresarial real. Para evitar cobros de consumo innecesarios de Azure, elimine la instancia de la solución preconfigurada en azureiotsuite.com cuando haya terminado con ella. Si necesita la solución preconfigurada de nuevo, puede crearla fácilmente. Para más información sobre cómo reducir el consumo mientras se ejecuta la solución de supervisión remota, consulte [Configuring Azure IoT Suite preconfigured solutions for demo purposes][lnk-demo-config] (Configuración de soluciones preconfiguradas del Conjunto de aplicaciones de IoT de Azure para fines de demostración).

[!INCLUDE [iot-suite-gateway-kit-view-solution](../../includes/iot-suite-gateway-kit-view-solution.md)]

Repita los pasos anteriores para agregar un segundo dispositivo con un id. de dispositivo como **device02**. La muestra envía datos procedentes de dos dispositivos simulados en la puerta de enlace a la solución de supervisión remota.

[!INCLUDE [iot-suite-gateway-kit-prepare-nuc-connectivity](../../includes/iot-suite-gateway-kit-prepare-nuc-connectivity.md)]

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
cd ~/iot-remote-monitoring-c-intel-nuc-gateway-getting-started/simulator
chmod u+x build.sh
sed -i -e 's/\r$//' build.sh
./build.sh
```

El script de compilación coloca el módulo personalizado de IoT Edge libsimulator.so en la carpeta de compilación.

## <a name="configure-and-run-the-iot-edge-gateway"></a>Configurar y ejecutar la puerta de enlace de IoT Edge

Ahora puede configurar la puerta de enlace de IoT Edge para enviar telemetría simulada al panel de supervisión remota. Para obtener más información sobre cómo configurar una puerta de enlace y los módulos de IoT Edge, consulte [Conceptos de Azure IoT Edge][lnk-gateway-concepts].

> [!TIP]
> En este tutorial, use el editor de texto `vi` estándar en Intel NUC. Si no ha usado `vi` antes, complete un tutorial introductorio, como [Unix - The vi Editor Tutorial][lnk-vi-tutorial] (Unix: el tutorial de editor vi) para familiarizarse con este editor. Como alternativa, puede instalar el editor [nano](https://www.nano-editor.org/) fácil de usar con el comando `smart install nano -y`.

Abra el archivo de configuración de ejemplo en el editor **vi** con el comando siguiente:

```bash
vi ~/iot-remote-monitoring-c-intel-nuc-gateway-getting-started/simulator/remote_monitoring.json
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
    "macAddress": "AA:BB:CC:DD:EE:FF",
    "deviceId": "<<Azure IoT Hub Device ID>>",
    "deviceKey": "<<Azure IoT Hub Device Key>>"
  },
  {
    "macAddress": "AA:BB:CC:DD:EE:FF",
    "deviceId": "<<Azure IoT Hub Device ID>>",
    "deviceKey": "<<Azure IoT Hub Device Key>>"
  }
]
```

Reemplace los marcadores de posición **deviceID** y **deviceKey** con los identificadores y las claves para los dos dispositivos que creó anteriormente en la solución de supervisión remota.

Guarde los cambios.

Ahora puede usar estos comandos para ejecutar la puerta de enlace de IoT Edge:

```bash
cd ~/iot-remote-monitoring-c-intel-nuc-gateway-getting-started/simulator
/usr/share/azureiotgatewaysdk/samples/simulated_device_cloud_upload/simulated_device_cloud_upload remote_monitoring.json
```

La puerta de enlace se inicia en Intel NUC y envía telemetría simulada a la solución de supervisión remota:

![La puerta de enlace de IoT Edge genera telemetría simulada.][img-simulated telemetry]

Presione **Ctrl-C** para salir del programa en cualquier momento.

## <a name="view-the-telemetry"></a>Visualización de la telemetría

La puerta de enlace de IoT Edge ahora está enviando telemetría simulada a la solución de supervisión remota. Puede verla en el panel de soluciones.

- Vaya al panel de soluciones.
- Seleccione uno de los dos dispositivos que configuró en la puerta de enlace en la lista desplegable **Dispositivo que se visualizará**.
- La telemetría de los dispositivos de puerta de enlace se muestra en el panel.

![Mostrar la telemetría de los dispositivos de puerta de enlace simulada][img-telemetry-display]

> [!WARNING]
> Si deja la solución de supervisión remota ejecutándose en su cuenta de Azure, se le cobra por el tiempo que se ejecute. Para más información sobre cómo reducir el consumo mientras se ejecuta la solución de supervisión remota, consulte [Configuring Azure IoT Suite preconfigured solutions for demo purposes][lnk-demo-config] (Configuración de soluciones preconfiguradas del Conjunto de aplicaciones de IoT de Azure para fines de demostración). Elimine la solución preconfigurada de su cuenta de Azure cuando haya terminado de usarla.

## <a name="next-steps"></a>Pasos siguientes

Visite el [Centro para desarrolladores de IoT de Azure](https://azure.microsoft.com/develop/iot/) para obtener más ejemplos y la documentación de IoT de Azure.

[img-simulated telemetry]: ./media/iot-suite-gateway-kit-get-started-simulator/appoutput.png

[img-telemetry-display]: ./media/iot-suite-gateway-kit-get-started-simulator/telemetry.png

[lnk-demo-config]: https://github.com/Azure/azure-iot-remote-monitoring/blob/master/Docs/configure-preconfigured-demo.md

[lnk-vi-tutorial]: http://www.tutorialspoint.com/unix/unix-vi-editor.htm
[lnk-gateway-concepts]: https://docs.microsoft.com/azure/iot-hub/iot-hub-linux-iot-edge-get-started