---
title: "Conexión de Raspberry Pi al Conjunto de aplicaciones de IoT de Azure mediante Node.js con telemetría simulada | Microsoft Docs"
description: "Use el Starter Kit de IoT de Microsoft Azure para Raspberry Pi 3 y el Conjunto de aplicaciones de IoT de Azure. Use Node.js para conectar su Raspberry Pi a la solución de supervisión remota, enviar telemetría simulada a la nube y responder a los métodos invocados desde el panel de la solución."
services: 
suite: iot-suite
documentationcenter: 
author: dominicbetts
manager: timlt
editor: 
ms.service: iot-suite
ms.devlang: node.js
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/02/2017
ms.author: dobett
ms.openlocfilehash: 53297049fd36eae3839c6a8146afc336b8f5cd02
ms.sourcegitcommit: 295ec94e3332d3e0a8704c1b848913672f7467c8
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/06/2017
---
# <a name="connect-your-raspberry-pi-3-to-the-remote-monitoring-solution-and-send-simulated-telemetry-using-nodejs"></a>Conexión de Raspberry Pi 3 a la solución de supervisión remota y envío de telemetría simulada mediante Node.js

[!INCLUDE [iot-suite-v1-raspberry-pi-kit-selector](../../includes/iot-suite-v1-raspberry-pi-kit-selector.md)]

Este tutorial muestra cómo utilizar Raspberry Pi 3 para simular los datos de temperatura y humedad para enviar a la nube. El tutorial usa:

- El SO Raspbian, el lenguaje de programación Node.js y el SDK de IoT de Microsoft Azure para Node.js para implementar un dispositivo de ejemplo.
- La solución preconfigurada de supervisión remota del Conjunto de aplicaciones de IoT como el back-end basado en la nube.

[!INCLUDE [iot-suite-v1-raspberry-pi-kit-overview-simulator](../../includes/iot-suite-v1-raspberry-pi-kit-overview-simulator.md)]

[!INCLUDE [iot-suite-v1-provision-remote-monitoring](../../includes/iot-suite-v1-provision-remote-monitoring.md)]

> [!WARNING]
> La solución de supervisión remota proporciona un conjunto de servicios de Azure de la suscripción de Azure. La implementación refleja una arquitectura empresarial real. Para evitar cobros de consumo innecesarios de Azure, elimine la instancia de la solución preconfigurada en azureiotsuite.com cuando haya terminado con ella. Si necesita la solución preconfigurada de nuevo, puede crearla fácilmente. Para más información sobre cómo reducir el consumo mientras se ejecuta la solución de supervisión remota, consulte [Configuring Azure IoT Suite preconfigured solutions for demo purposes][lnk-demo-config] (Configuración de soluciones preconfiguradas del Conjunto de aplicaciones de IoT de Azure para fines de demostración).

[!INCLUDE [iot-suite-v1-raspberry-pi-kit-view-solution](../../includes/iot-suite-v1-raspberry-pi-kit-view-solution.md)]

[!INCLUDE [iot-suite-v1-raspberry-pi-kit-prepare-pi-simulator](../../includes/iot-suite-v1-raspberry-pi-kit-prepare-pi-simulator.md)]

## <a name="download-and-configure-the-sample"></a>Descarga y configuración del ejemplo

Ahora puede descargar y configurar la aplicación de cliente de supervisión remota en su Raspberry Pi.

### <a name="install-nodejs"></a>Instalación de Node.js

Si aún no lo ha hecho, instale Node.js en su Raspberry Pi. El SDK de IoT para Node.js requiere la versión 0.11.5 de Node.js o una versión posterior. Los pasos siguientes muestran cómo instalar Node.js v6.10.2 en Raspberry Pi:

1. Use el siguiente comando para actualizar Raspberry Pi:

    ```sh
    sudo apt-get update
    ```

1. Use el siguiente comando para descargar los archivos binarios de Node.js en Raspberry Pi:

    ```sh
    wget https://nodejs.org/dist/v6.10.2/node-v6.10.2-linux-armv7l.tar.gz
    ```

1. Use el siguiente comando para los binarios:

    ```sh
    sudo tar -C /usr/local --strip-components 1 -xzf node-v6.10.2-linux-armv7l.tar.gz
    ```

1. Use el siguiente comando para comprobar que ha instalado Node.js v6.10.2 correctamente:

    ```sh
    node --version
    ```

### <a name="clone-the-repositories"></a>Clonación de repositorios

Si aún no lo ha hecho, clone los repositorios necesarios mediante la ejecución de los siguientes comandos en un terminal en su Pi:

```sh
cd ~
git clone --recursive https://github.com/Azure-Samples/iot-remote-monitoring-node-raspberrypi-getstartedkit.git
```

### <a name="update-the-device-connection-string"></a>Actualización de la cadena de conexión de dispositivo

Abra el archivo de código fuente de ejemplo en el editor **nano** con el comando siguiente:

```sh
nano ~/iot-remote-monitoring-node-raspberrypi-getstartedkit/simulator/remote_monitoring.js
```

Busque la línea:

```javascript
var connectionString = 'HostName=[Your IoT hub name].azure-devices.net;DeviceId=[Your device id];SharedAccessKey=[Your device key]';
```

Reemplace los valores de marcador de posición por el dispositivo y la información de IoT Hub que creó y guardó al principio de este tutorial. Guarde los cambios (**Ctrl-O**, **ENTRAR**) y salga del editor (**Ctrl-X**).

## <a name="run-the-sample"></a>Ejecución del ejemplo

Ejecute los comandos siguientes para instalar los paquetes de requisitos previos para el ejemplo:

```sh
cd ~/iot-remote-monitoring-node-raspberrypi-getstartedkit/simulator
npm install
```

Ahora puede ejecutar el programa de ejemplo en Raspberry Pi. Escriba el comando:

```sh
sudo node ~/iot-remote-monitoring-node-raspberrypi-getstartedkit/simulator/remote_monitoring.js
```

La salida de ejemplo siguiente es un ejemplo de la salida que se ve en el símbolo del sistema en Raspberry Pi:

![Salida de la aplicación Raspberry Pi][img-raspberry-output]

Presione **Ctrl-C** para salir del programa en cualquier momento.

[!INCLUDE [iot-suite-v1-raspberry-pi-kit-view-telemetry-simulator](../../includes/iot-suite-v1-raspberry-pi-kit-view-telemetry-simulator.md)]

## <a name="next-steps"></a>Pasos siguientes

Visite el [Centro para desarrolladores de IoT de Azure](https://azure.microsoft.com/develop/iot/) para obtener más ejemplos y la documentación de IoT de Azure.

[img-raspberry-output]: ./media/iot-suite-v1-raspberry-pi-kit-node-get-started-simulator/app-output.png

[lnk-demo-config]: https://github.com/Azure/azure-iot-remote-monitoring/blob/master/Docs/configure-preconfigured-demo.md
