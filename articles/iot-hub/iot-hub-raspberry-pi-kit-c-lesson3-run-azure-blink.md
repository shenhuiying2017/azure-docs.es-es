---
title: "Conexión de Raspberry Pi (C) a Azure IoT: Lección 3: Ejecución del ejemplo | Microsoft Docs"
description: "Implemente y ejecute una aplicación de ejemplo para Raspberry Pi 3 que envíe mensajes a IoT Hub y haga parpadear el LED."
services: iot-hub
documentationcenter: 
author: shizn
manager: timtl
tags: 
keywords: pi en la nube con parpadeo del led, parpadeo del led desde la nube
ROBOTS: NOINDEX
redirect_url: /azure/iot-hub/iot-hub-raspberry-pi-kit-c-get-started
ms.assetid: e38df29f-f77f-435f-9add-46814297564f
ms.service: iot-hub
ms.devlang: c
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 3/21/2017
ms.author: xshi
translationtype: Human Translation
ms.sourcegitcommit: 64e69df256404e98f6175f77357500b562d74318
ms.openlocfilehash: 985ba895bfcad1611bebe80a6c2fc8ffed780da5
ms.lasthandoff: 01/24/2017


---
# <a name="run-a-sample-application-to-send-device-to-cloud-messages"></a>Ejecución de una aplicación de ejemplo para enviar mensajes de dispositivo a nube
## <a name="what-you-will-do"></a>Lo que hará
En este artículo, se explica cómo implementar y ejecutar una aplicación de ejemplo en Raspberry Pi 3 que envía mensajes a IoT Hub. Si tiene problemas, busque soluciones en la [página de solución de problemas](iot-hub-raspberry-pi-kit-c-troubleshooting.md).

## <a name="what-you-will-learn"></a>Lo qué aprenderá
Aprenderá a utilizar la herramienta de Gulp para implementar y ejecutar la aplicación de ejemplo Node.js en Pi.

## <a name="what-you-need"></a>Lo que necesita
* Para comenzar esta tarea, debe haber completado correctamente el tutorial [Creación de una instancia de Azure Function App y una cuenta de Azure Storage para procesar y guardar mensajes de IoT Hub](iot-hub-raspberry-pi-kit-c-lesson3-deploy-resource-manager-template.md).

## <a name="get-your-iot-hub-and-device-connection-strings"></a>Obtención de las cadenas de conexión de IoT Hub y del dispositivo
La cadena de conexión de dispositivos se utiliza para conectar Pi con IoT Hub. La cadena de conexión de IoT Hub se utiliza para conectar con el registro de identidad en IoT Hub para administrar los dispositivos que pueden conectarse con IoT Hub. 

* Enumere todos los centros de IoT Hub del grupo de recursos ejecutando el siguiente comando de la CLI de Azure:

```bash
az iot hub list -g iot-sample --query [].name
```

Use `iot-sample` como valor de `{resource group name}`, si no lo modificó previamente.

* Obtenga la cadena de conexión de IoT Hub ejecutando el siguiente comando de la CLI de Azure:

```bash
az iot hub show-connection-string --name {my hub name} -g iot-sample
```

`{my hub name}` es el nombre que especificó cuando creó el centro de IoT Hub y registró Pi.

* Obtenga la cadena de conexión de dispositivos ejecutando el siguiente comando:

```bash
az iot device show-connection-string --hub-name {my hub name} --device-id myraspberrypi -g iot-sample
```

Use `myraspberrypi` como valor de `{device id}`, si no lo modificó previamente.

## <a name="configure-the-device-connection"></a>Configuración de la conexión de dispositivos
1. Inicialice el archivo de configuración con los siguientes comandos:
   
   ```bash
   npm install
   gulp init
   ```

> [!NOTE]
> Ejecute también **gulp install-tools** si no lo hizo en la lección 1.

2. Abra el archivo de configuración de dispositivos `config-raspberrypi.json` en Visual Studio Code ejecutando el comando siguiente:
   
   ```bash
   # For Windows command prompt
   code %USERPROFILE%\.iot-hub-getting-started\config-raspberrypi.json
   
   # For MacOS or Ubuntu
   code ~/.iot-hub-getting-started/config-raspberrypi.json
   ```
   
   ![config.json](media/iot-hub-raspberry-pi-lessons/lesson3/config.png)
3. Realice las sustituciones siguientes en el archivo `config-raspberrypi.json`:
   
   * Reemplace **[device hostname or IP address]** por la dirección IP o el nombre de host del dispositivo que obtuvo de `device-discovery-cli` o por el valor heredado de la configuración del dispositivo.
   * Reemplace **[IoT device connection string]** con el valor de `device connection string` que ha obtenido.
   * Reemplace **[IoT hub connection string]** con el valor de `iot hub connection string` que ha obtenido.

> [!NOTE]
> `azure_storage_connection_string` no es necesario en este artículo. Déjelo como está.

Actualice el archivo `config-raspberrypi.json` para que pueda implementar la aplicación de ejemplo desde el equipo.

## <a name="deploy-and-run-the-sample-application"></a>Implementación y ejecución de la aplicación de ejemplo
Implemente y ejecute la aplicación de ejemplo en PI mediante el comando siguiente:

```bash
gulp deploy && gulp run
```

## <a name="verify-that-the-sample-application-works"></a>Comprobación del funcionamiento de la aplicación de ejemplo
El LED conectado a Pi debería parpadear cada dos segundos. Cada vez que el LED parpadea, la aplicación de ejemplo envía un mensaje a IoT Hub y comprueba que el mensaje se envió correctamente a IoT Hub. Además, todos los mensajes que reciba IoT Hub se imprimirán en la ventana de consola. La aplicación de ejemplo se finaliza automáticamente después de enviar 20 mensajes.

![Aplicación de ejemplo con mensajes enviados y recibidos](media/iot-hub-raspberry-pi-lessons/lesson3/gulp_run_c.png)

## <a name="summary"></a>Resumen
Ha implementado y ejecutado la nueva aplicación de ejemplo de activación del parpadeo en Pi para enviar al centro de IoT Hub mensajes de dispositivo a nube. Ahora, puede supervisar los mensajes a medida que se escriben en la cuenta de almacenamiento.

## <a name="next-steps"></a>Pasos siguientes
[Read messages persisted in Azure Storage](iot-hub-raspberry-pi-kit-c-lesson3-read-table-storage.md) (Lectura de los mensajes guardados en Azure Storage)


