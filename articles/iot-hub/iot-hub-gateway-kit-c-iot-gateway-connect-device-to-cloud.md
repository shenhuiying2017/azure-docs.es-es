---
title: Usar una puerta de enlace de IoT para conectar un dispositivo a Azure IoT Hub | Microsoft Docs
description: Aprenda a usar Intel NUC como una puerta de enlace de IoT para conectar un SensorTag de TI y enviar datos del sensor a Azure IoT Hub en la nube.
services: iot-hub
documentationcenter: 
author: shizn
manager: timlt
tags: 
keywords: puerta de enlace de IOT conectar dispositivo a la nube
ms.assetid: cb851648-018c-4a7e-860f-b62ed3b493a5
ms.service: iot-hub
ms.devlang: c
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/25/2017
ms.author: xshi
ms.openlocfilehash: 4fb77ed0241d15338c2574fd22828507c3e40cb3
ms.sourcegitcommit: 02e69c4a9d17645633357fe3d46677c2ff22c85a
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 08/03/2017
---
# <a name="use-iot-gateway-to-connect-things-to-the-cloud---sensortag-to-azure-iot-hub"></a>Usar una puerta de enlace de IoT para conectar dispositivos a la nube: SensorTag a Azure IoT Hub

> [!NOTE]
> Antes de empezar este tutorial, asegúrese de haber completado [Set up Intel NUC as an IoT gateway (Configurar Intel NUC como una puerta de enlace de IoT)](iot-hub-gateway-kit-c-lesson1-set-up-nuc.md). En [Set up Intel NUC as an IoT gateway (Configurar Intel NUC como una puerta de enlace de IoT)](iot-hub-gateway-kit-c-lesson1-set-up-nuc.md), se configura el dispositivo Intel NUC como una puerta de enlace de IoT.

## <a name="what-you-will-learn"></a>Lo qué aprenderá

Aprenda a usar una puerta de enlace de IoT para conectar un SensorTag de Texas Instruments (CC2650STK) a Azure IoT Hub. La puerta de enlace de IoT envía datos de temperatura y humedad recopilados del SensorTag a Azure IoT Hub.

## <a name="what-you-will-do"></a>Lo que hará

- Cree un Centro de IoT.
- Registre un dispositivo en IoT Hub para el SensorTag.
- Habilite la conexión entre la puerta de enlace de IoT y el SensorTag.
- Ejecute una aplicación de ejemplo BLE para enviar datos del SensorTag a IoT Hub.

## <a name="what-you-need"></a>Lo que necesita

- Tutorial [Set up Intel NUC as an IoT gateway (Configurar Intel NUC como una puerta de enlace de IoT)](iot-hub-gateway-kit-c-lesson1-set-up-nuc.md) completado en el que haya configurado Intel NUC como una puerta de enlace de IoT.
- * Una suscripción de Azure activa. Si no tiene ninguna cuenta de Azure, [cree una cuenta de evaluación gratuita de Azure](https://azure.microsoft.com/free/) en solo unos minutos.
- Un cliente de SSH que se ejecute en el equipo host. En Windows se recomienda PuTTY. Linux y macOS ya vienen con un cliente de SSH.
- La dirección IP y el nombre de usuario y la contraseña para acceder a la puerta de enlace desde el cliente de SSH.
- Una conexión a Internet.

[!INCLUDE [iot-hub-get-started-create-hub-and-device](../../includes/iot-hub-get-started-create-hub-and-device.md)]

> [!NOTE]
> Aquí se registra este nuevo dispositivo para el SensorTag

## <a name="enable-the-connection-between-the-iot-gateway-and-the-sensortag"></a>Habilitar la conexión entre la puerta de enlace de IoT y el SensorTag

En esta sección se realizarán las siguientes tareas:

- Obtener la dirección MAC del SensorTag para la conexión Bluetooth.
- Iniciar una conexión Bluetooth desde la puerta de enlace de IoT al SensorTag.

### <a name="get-the-mac-address-of-the-sensortag-for-bluetooth-connection"></a>Obtener la dirección MAC del SensorTag para la conexión Bluetooth

1. En el equipo host, ejecute el cliente de SSH y conéctelo a la puerta de enlace de IoT.
1. Desbloquee Bluetooth al ejecutar el siguiente comando:

   ```bash
   sudo rfkill unblock bluetooth
   ```

1. Inicie el servicio Bluetooth en la puerta de enlace de IoT y escriba un shell de Bluetooth para configurar Bluetooth al ejecutar los comandos siguientes:

   ```bash
   sudo systemctl start bluetooth
   bluetoothctl
   ```

1. Encienda el controlador Bluetooth al ejecutar el siguiente comando en el shell de Bluetooth:

   ```bash
   power on
   ```

   ![Encender el controlador Bluetooth en la puerta de enlace de IoT con bluetoothctl](./media/iot-hub-iot-gateway-connect-device-to-cloud/8_power-on-bluetooth-controller-at-bluetooth-shell-bluetoothctl.png)

1. Inicie la detección de dispositivos Bluetooth cercanos al ejecutar el comando siguiente:

   ```bash
   scan on
   ```

   ![Buscar dispositivos Bluetooth cercanos con bluetoothctl](./media/iot-hub-iot-gateway-connect-device-to-cloud/9_start-scan-nearby-bluetooth-devices-at-bluetooth-shell-bluetoothctl.png)

1. Presione el botón de emparejamiento en el SensorTag. El LED verde del SensorTag parpadea.
1. En el shell de Bluetooth debería ver que se ha encontrado el SensorTag. Anote la dirección MAC del SensorTag. En este ejemplo, la dirección MAC del SensorTag es `24:71:89:C0:7F:82`.
1. Detenga la detección al ejecutar el comando siguiente:

   ```bash
   scan off
   ```

   ![Detener la detección de dispositivos Bluetooth cercanos con bluetoothctl](./media/iot-hub-iot-gateway-connect-device-to-cloud/10_stop-scanning-nearby-bluetooth-devices-at-bluetooth-shell-bluetoothctl.png)

### <a name="initiate-a-bluetooth-connection-from-the-iot-gateway-to-the-sensortag"></a>Iniciar una conexión Bluetooth desde la puerta de enlace de IoT al SensorTag

1. Conéctese al SensorTag mediante la ejecución del comando siguiente:

   ```bash
   connect <MAC address>
   ```

   ![Conectarse al SensorTag con bluetoothctl](./media/iot-hub-iot-gateway-connect-device-to-cloud/11_connect-to-sensortag-at-bluetooth-shell-bluetoothctl.png)

1. Desconéctese del SensorTag y salga del shell de Bluetooth al ejecutar los comandos siguientes:

   ```bash
   disconnect
   exit
   ```

   ![Desconectarse del SensorTag con bluetoothctl](./media/iot-hub-iot-gateway-connect-device-to-cloud/12_disconnect-from-sensortag-at-bluetooth-shell-bluetoothctl.png)

Ha habilitado correctamente la conexión entre la puerta de enlace de IoT y el SensorTag.

## <a name="run-a-ble-sample-application-to-send-sensortag-data-to-your-iot-hub"></a>Ejecutar una aplicación de ejemplo BLE para enviar datos del SensorTag a IoT Hub

Azure IoT Edge proporciona la aplicación de ejemplo Bluetooth Low Energy (BLE). La aplicación de ejemplo recopila datos de la conexión BLE y los envía a IoT Hub. Para ejecutar la aplicación de ejemplo, tiene que:

1. Configurar la aplicación de ejemplo.
1. Ejecutar la aplicación de ejemplo en la puerta de enlace de IoT.

### <a name="configure-the-sample-application"></a>Configurar la aplicación de ejemplo

1. Vaya a la carpeta de la aplicación de ejemplo al ejecutar el comando siguiente:

   ```bash
   cd /usr/share/azureiotgatewaysdk/samples/ble_gateway
   ```

1. Abra el archivo de configuración al ejecutar el siguiente comando:

   ```bash
   vi ble_gateway.json
   ```

1. En el archivo de configuración, rellene los siguientes valores:

   **IoTHubName**: nombre del IoT Hub.

   **IoTHubSuffix**: obtenga IoTHubSuffix de la clave principal de la cadena de conexión del dispositivo que ha anotado. Asegúrese de que obtiene la clave principal de la cadena de conexión del dispositivo y no la clave principal de la cadena de conexión de IoT Hub. La clave principal de la cadena de conexión del dispositivo tiene el formato `HostName=IOTHUBNAME.IOTHUBSUFFIX;DeviceId=DEVICEID;SharedAccessKey=SHAREDACCESSKEY`.

   **Transport**: el valor predeterminada es `amqp`. Este valor muestra el protocolo durante el transporte. Podría ser `http`, `amqp` o `mqtt`.

   **macAddress**: dirección MAC del SensorTag que ha anotado.

   **deviceID**: identificador del dispositivo que ha creado en IoT Hub.

   **deviceKey**: clave principal de la cadena de conexión del dispositivo.

   ![Completar el archivo config de la aplicación de ejemplo BLE](./media/iot-hub-iot-gateway-connect-device-to-cloud/13_edit-config-file-of-ble-sample.png)

1. Presione `ESC` y escriba `:wq` para guardar el archivo.

### <a name="run-the-sample-application"></a>Ejecutar la aplicación de ejemplo

1. Asegúrese de que el SensorTag está encendido.
1. Ejecute el siguiente comando:

   ```bash
   ./ble_gateway ble_gateway.json
   ```

## <a name="next-steps"></a>Pasos siguientes

[Usar la puerta de enlace de IoT para la transformación de datos de sensor con Azure IoT Edge](iot-hub-gateway-kit-c-use-iot-gateway-for-data-conversion.md)
