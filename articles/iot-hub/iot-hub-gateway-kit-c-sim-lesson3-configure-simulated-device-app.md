---
title: "Ejecución de una aplicación de ejemplo de dispositivo simulado para enviar datos a Azure IoT Hub | Microsoft Docs"
description: "Ejecución de una aplicación de ejemplo de dispositivo simulado para enviar datos sobre temperatura a IoT Hub"
services: iot-hub
documentationcenter: 
author: shizn
manager: timtl
tags: 
keywords: datos a la nube
ms.assetid: 5d051d99-9749-4150-b3c8-573b0bda9c52
ms.service: iot-hub
ms.devlang: c
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/28/2016
ms.author: xshi
translationtype: Human Translation
ms.sourcegitcommit: 1c4f44787a7200a1c3634b258df32d30152daa90
ms.openlocfilehash: 94bffa363f7979b7dc37363328a358f0b139307d


---
# <a name="configure-and-run-a-simulated-device-sample-app"></a>Configuración y ejecución de una aplicación de ejemplo de dispositivo simulado

## <a name="what-you-will-do"></a>Lo que hará

- Clone el repositorio de ejemplo.
- Use la CLI de Azure para obtener la información sobre dispositivos lógicos y sobre IoT Hub para la aplicación de ejemplo de dispositivo simulado. Configure y ejecute la aplicación de ejemplo de dispositivo simulado.

Si tiene problemas, busque soluciones en la [página de solución de problemas](iot-hub-gateway-kit-c-sim-troubleshooting.md).

## <a name="what-you-will-learn"></a>Lo qué aprenderá

En este artículo, aprenderá lo siguiente:

- Cómo configurar y ejecutar la aplicación de muestra de dispositivo simulado.

## <a name="what-you-need"></a>Lo que necesita

Debe haber completado correctamente los siguientes tutoriales:

- [Creación de un IoT Hub y registro del dispositivo](iot-hub-gateway-kit-c-sim-lesson2-register-device.md)

## <a name="clone-the-sample-repository-to-the-host-computer"></a>Clonar el repositorio de ejemplo en el equipo host

Para clonar el repositorio de ejemplo, siga estos pasos en el equipo host:

1. Abra una ventana del símbolo del sistema en Windows o abra un terminal de macOS o Ubuntu.
2. Ejecute los comandos siguientes:

   ```bash
   git clone https://github.com/Azure-samples/iot-hub-c-intel-nuc-gateway-getting-started
   cd iot-hub-c-intel-nuc-gateway-getting-started
   ```

## <a name="configure-the-simulated-device-and-your-nuc"></a>Configurar el dispositivo simulado y su NUC

1. Abra el archivo de configuración `config.json` en Visual Studio Code con este comando:

   ```bash
   code config.json
   ```

2. Reemplazar `"has_sensortag": true` con `"has_sensortag": false`

   ![Configurar un dispositivo de SensorTag de TI si no lo tiene](media/iot-hub-gateway-kit-lessons/lesson3/config_no_sensortag.png)

3. Inicialice el archivo de configuración con los siguientes comandos:

   ```bash
   cd Lesson3
   npm install
   gulp init
   ```

4. Abra `config-gateway.json` en Visual Studio Code mediante la ejecución del siguiente comando:

   ```bash
   # For Windows command prompt
   code %USERPROFILE%\.iot-hub-getting-started\config-gateway.json
   # For macOS or Ubuntu
   code ~/.iot-hub-getting-started/config-gateway.json
   ```

5. Busque la siguiente línea de código y reemplace `[device hostname or IP address]` con el nombre de host o la dirección IP de Intel NUC.
   ![captura de pantalla de puerta de enlace de configuración](media/iot-hub-gateway-kit-lessons/lesson3/config_gateway.png)

## <a name="get-the-connection-string-of-your-iot-hub-logical-device"></a>Obtener la cadena de conexión del dispositivo lógico de IoT Hub

Para obtener la cadena de conexión de Azure IoT Hub del dispositivo lógico, ejecute el siguiente comando en el equipo host:

```bash
az iot device show-connection-string --hub-name {IoT hub name} --device-id mydevice --resource-group iot-gateway
```

`{IoT hub name}` es el nombre del IoT Hub utilizado. Use iot-gateway como el valor de `{resource group name}` y utilice mydevice como el valor de `{device id}` si no lo ha cambiado en la lección 2.

## <a name="configure-the-simulated-device-cloud-upload-sample-application"></a>Configurar la aplicación de ejemplo de carga en la nube del dispositivo simulado

Para configurar y ejecutar la aplicación de ejemplo de carga en la nube del dispositivo simulado, siga estos pasos en el equipo host:

1. Abra `config-sensortag.json` en Visual Studio Code mediante la ejecución del siguiente comando:

   ```bash
   # For Windows command prompt
   code %USERPROFILE%\.iot-hub-getting-started\config-sensortag.json
   # For macOS or Ubuntu
   code ~/.iot-hub-getting-started/config-sensortag.json
   ```

   ![captura de sensortag configurado](media/iot-hub-gateway-kit-lessons/lesson3/config_simulated_device.png)

2. Realice las sustituciones siguientes en el código:
   - Reemplace `[IoT hub name]` con el nombre de IoT Hub.
   - Reemplace `[IoT device connection string]` con la cadena de conexión del dispositivo lógico de IoT Hub.

3. Ejecute la aplicación.

   Implemente y ejecute la aplicación mediante la ejecución del comando siguiente:

   ```bash
   gulp run
   ```

## <a name="verify-the-sample-application-works"></a>Comprobación del funcionamiento de la aplicación de ejemplo

Ahora debe aparecer un resultado parecido al siguiente:

![resultado de la aplicación de ejemplo del dispositivo simulado](media/iot-hub-gateway-kit-lessons/lesson3/gulp_run_simudev.png)

La aplicación envía datos sobre temperatura a IoT Hub, una tarea que dura 40 segundos.

## <a name="summary"></a>Resumen

Ha configurado y ejecutado correctamente la aplicación de ejemplo de carga en la nube del dispositivo simulado que envía datos a IoT Hub con el dispositivo simulado.

## <a name="next-steps"></a>Pasos siguientes
[Lectura de mensajes en IoT Hub](iot-hub-gateway-kit-c-sim-lesson3-read-messages-from-hub.md)


<!--HONumber=Dec16_HO3-->


