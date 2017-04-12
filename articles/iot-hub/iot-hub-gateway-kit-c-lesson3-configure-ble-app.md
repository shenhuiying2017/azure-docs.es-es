---
title: "Dispositivo SensorTag y puerta de enlace de Azure IoT: Lección 3: Ejecución de la aplicación de ejemplo | Microsoft Docs"
description: "Ejecute una aplicación de ejemplo BLE para recibir datos de SensorTag para BLE y del IoT Hub."
services: iot-hub
documentationcenter: 
author: shizn
manager: timtl
tags: 
keywords: "aplicación ble, aplicación para monitor de sensores, colección de datos del sensor, datos del sensor, datos del sensor a la nube"
ROBOTS: NOINDEX
redirect_url: /azure/iot-hub/iot-hub-gateway-kit-c-lesson1-set-up-nuc
ms.assetid: b33e53a1-1df7-4412-ade1-45185aec5bef
ms.service: iot-hub
ms.devlang: c
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 3/21/2017
ms.author: xshi
translationtype: Human Translation
ms.sourcegitcommit: 61e9a9fc7876094c04238c61cfc38efdd97b05f7
ms.openlocfilehash: a9f689c0b231af3cdf9257e2179bf86ff7bc9a31
ms.lasthandoff: 01/25/2017


---
# <a name="configure-and-run-a-ble-sample-application"></a>Configuración y ejecución de la aplicación de ejemplo BLE

## <a name="what-you-will-do"></a>Lo que hará

- Clone el repositorio de ejemplo. 
- Configure la conectividad entre SensorTag e Intel NUC. 
- Use la CLI de Azure para obtener la información de SensorTag para una aplicación de ejemplo BLE (Bluetooth de bajo consumo). Y configure y ejecute la aplicación de ejemplo BLE. 

Si tiene problemas, busque soluciones en la [página de solución de problemas](iot-hub-gateway-kit-c-troubleshooting.md).

## <a name="what-you-will-learn"></a>Lo qué aprenderá

En este artículo, aprenderá lo siguiente:

- Cómo configurar y ejecutar la aplicación de ejemplo BLE.

## <a name="what-you-need"></a>Lo que necesita

Debe haber completado correctamente los siguientes tutoriales:

- [Cree un IoT Hub y registre SensorTag](iot-hub-gateway-kit-c-lesson2-register-device.md)

## <a name="clone-the-sample-repository-to-the-host-computer"></a>Clonar el repositorio de ejemplo en el equipo host

Para clonar el repositorio de ejemplo, siga estos pasos en el equipo host:

1. Abra una ventana del símbolo del sistema en Windows o abra un terminal de macOS o Ubuntu.
2. Ejecute los comandos siguientes:

   ```bash
   git clone https://github.com/Azure-samples/iot-hub-c-intel-nuc-gateway-getting-started
   cd iot-hub-c-intel-nuc-gateway-getting-started
   ```

## <a name="set-up-the-connectivity-between-sensortag-and-intel-nuc"></a>Configurar la conectividad entre SensorTag e Intel NUC

Para configurar la conectividad, siga estos pasos en el equipo host:

1. Inicialice el archivo de configuración con los siguientes comandos:

   ```bash
   cd Lesson3
   npm install
   gulp init
   ```

2. Abra `config-gateway.json` en Visual Studio Code mediante la ejecución del siguiente comando:

   ```bash
   # For Windows command prompt
   code %USERPROFILE%\.iot-hub-getting-started\config-gateway.json
   # For macOS or Ubuntu
   code ~/.iot-hub-getting-started/config-gateway.json
   ```

3. Busque la siguiente línea de código y reemplace `[device hostname or IP address]` con el nombre de host o la dirección IP de Intel NUC.
   ![captura de pantalla de puerta de enlace de configuración](media/iot-hub-gateway-kit-lessons/lesson3/config_gateway.png)

4. Instale herramientas auxiliares en Intel NUC mediante la ejecución del siguiente comando:

   ```bash
   gulp install-tools
   ```

5. Active SensorTag; para ello, presione el botón de encendido como en la imagen siguiente y, a continuación, el LED verde debe parpadear.

   ![activar SensorTag](media/iot-hub-gateway-kit-lessons/lesson3/turn on_off sensortag.jpg)

6. Examine los dispositivos SensorTag mediante la ejecución de los comandos siguientes:

   ```bash
   gulp discover-sensortag
   ```

7. Pruebe la conectividad entre SensorTag e Intel NUC mediante la ejecución del siguiente comando:

   ```bash
   gulp test-connectivity --mac {mac address}
   ```

   Reemplace `{mac address}` por la dirección MAC obtenida en el paso anterior.

## <a name="get-the-connection-string-of-sensortag"></a>Obtener la cadena de conexión de SensorTag

Para obtener la cadena de conexión de Azure IoT Hub para SensorTag, ejecute el siguiente comando en el equipo host:

```bash
az iot device show-connection-string --hub-name {IoT hub name} --device-id mydevice --resource-group iot-gateway
```

`{IoT hub name}` es el nombre del IoT Hub utilizado. Use iot-gateway como el valor de `{resource group name}` y utilice mydevice como el valor de `{device id}` si no lo ha cambiado en la lección 2.

## <a name="configure-the-ble-sample-application"></a>Configurar la aplicación de ejemplo BLE

Para configurar y ejecutar la aplicación de ejemplo BLE, siga estos pasos en el equipo host:

1. Abra `config-sensortag.json` en Visual Studio Code mediante la ejecución del siguiente comando:

   ```bash
   # For Windows command prompt
   code %USERPROFILE%\.iot-hub-getting-started\config-sensortag.json
   # For macOS or Ubuntu
   code ~/.iot-hub-getting-started/config-sensortag.json
   ```

   ![captura de sensortag configurado](media/iot-hub-gateway-kit-lessons/lesson3/config_sensortag.png)

2. Realice las sustituciones siguientes en el código:
   - Reemplace `[IoT hub name]` con el nombre de IoT Hub utilizado.
   - Reemplace `[IoT device connection string]` con la cadena de conexión de SensorTag obtenida.
   - Reemplace `[device_mac_address]` con la dirección MAC de SensorTag obtenida.

3. Ejecute la aplicación de ejemplo BLE.

   Para ejecutar la aplicación de ejemplo BLE, siga estos pasos en el equipo host:

   1. Active SensorTag.

   2. Implemente y ejecute la aplicación de ejemplo BLE en Inter NUC mediante la ejecución del comando siguiente:
   
      ```bash
      gulp run
      ```

## <a name="verify-that-the-ble-sample-application-works"></a>Verificación del funcionamiento de la aplicación de ejemplo BLE

Ahora debe aparecer algo parecido a lo siguiente:

![Resultado de la aplicación de ejemplo BLE](media/iot-hub-gateway-kit-lessons/lesson3/BLE_running.png)

La aplicación de ejemplo continúa recopilando datos de temperatura y los envía a IoT Hub. La aplicación de ejemplo finaliza automáticamente después de realizar envíos durante 40 segundos.

## <a name="summary"></a>Resumen

Ha configurado correctamente la conectividad entre SensorTag e Intel NUC y ha ejecutado una aplicación de ejemplo BLE que recopila y envía datos desde SensorTag al IoT Hub. Está listo para aprender a verificar si IoT Hube ha recibido los datos.

## <a name="next-steps"></a>Pasos siguientes
[Lectura de mensajes en IoT Hub](iot-hub-gateway-kit-c-lesson3-read-messages-from-hub.md)
