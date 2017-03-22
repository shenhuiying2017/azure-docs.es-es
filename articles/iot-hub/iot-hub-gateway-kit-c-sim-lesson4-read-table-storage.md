---
title: "Dispositivo simulado y puerta de enlace de Azure IoT: Lección 4: Table Storage | Microsoft Docs"
description: "Guarde mensajes de Intel NUC en su IoT Hub, escríbalos en una tabla de Azure Table Storage y, después, léalos desde la nube."
services: iot-hub
documentationcenter: 
author: shizn
manager: timtl
tags: 
keywords: recuperar datos de la nube, servicio en la nube de iot
ms.assetid: 78e4b6ea-968d-401e-a7dc-8f9acdb3ec1a
ms.service: iot-hub
ms.devlang: c
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 3/21/2017
ms.author: xshi
translationtype: Human Translation
ms.sourcegitcommit: 61e9a9fc7876094c04238c61cfc38efdd97b05f7
ms.openlocfilehash: b12e16a5a532448cf2e939cfcad322225b9ee811
ms.lasthandoff: 01/25/2017


---

# <a name="read-messages-persisted-in-azure-table-storage"></a>Lectura de mensajes guardados en Azure Table Storage

## <a name="what-you-will-do"></a>Lo que hará

- Ejecute la aplicación de ejemplo de puerta de enlace en la puerta de enlace que envía mensajes a IoT Hub.
- Ejecute un ejemplo de código en el equipo host para leer los mensajes en Azure Table Storage.

Si tiene problemas, busque soluciones en la [página de solución de problemas](iot-hub-gateway-kit-c-sim-troubleshooting.md).

## <a name="what-you-will-learn"></a>Lo qué aprenderá

Cómo utilizar la herramienta Gulp para ejecutar el ejemplo de código con la finalidad de leer mensajes en Azure Table Storage.

## <a name="what-you-need"></a>Lo que necesita

Ha realizado correctamente las tareas siguientes:

- [Creación de una cuenta de almacenamiento de Azure y una Azure Function App](iot-hub-gateway-kit-c-sim-lesson4-deploy-resource-manager-template.md).
- [Ejecución de la aplicación de ejemplo de puerta de enlace](iot-hub-gateway-kit-c-sim-lesson3-configure-simulated-device-app.md).
- [Lectura de mensajes desde el IoT Hub](iot-hub-gateway-kit-c-sim-lesson3-read-messages-from-hub.md)

## <a name="get-your-azure-storage-connection-strings"></a>Obtener cadenas de conexión de Azure Storage

Al principio de esta lección, creó correctamente una cuenta de almacenamiento de Azure. Para obtener la cadena de conexión de la cuenta de almacenamiento de Azure, ejecute los siguientes comandos:

* Enumere todas las cuentas de almacenamiento.

```bash
az storage account list -g iot-gateway --query [].name
```

* Obtenga la cadena de conexión de Azure Storage.

```bash
az storage account show-connection-string -g iot-gateway -n {storage name}
```

Use `iot-gateway` como el valor de `{resource group name}` si no lo cambió en la lección 2.

## <a name="configure-the-device-connection"></a>Configuración de la conexión de dispositivos

Actualice el archivo `config-azure.json` para que el ejemplo de código que se ejecuta en el equipo host pueda leer el mensaje de su instancia de Azure Table Storage. Para configurar la conexión de dispositivos, siga estos pasos:

1. Genere el archivo de configuración del dispositivo `config-azure.json` mediante la ejecución de los comandos siguientes:

   ```bash
   # For Windows command prompt
   code %USERPROFILE%\.iot-hub-getting-started\config-azure.json
   # For MacOS or Ubuntu
   code ~/.iot-hub-getting-started/config-azure.json
   ```

   ![configuración](media/iot-hub-gateway-kit-lessons/lesson4/config_azure.png)

2. Sustituya `[Azure storage connection string]` por la cadena de conexión de Azure Storage obtenida.

   `[IoT hub connection string]` ya se debe haber sustituido en la sección [Lectura de mensajes en Azure IoT Hub](iot-hub-gateway-kit-c-sim-lesson3-read-messages-from-hub.md) de la lección&3;.

## <a name="read-messages-in-your-azure-table-storage"></a>Lectura de mensajes en Azure Table Storage

Ejecute la aplicación de ejemplo de puerta de enlace y lea mensajes en Azure Table Storage mediante la ejecución del siguiente comando:

```bash
gulp run --table-storage
```

IoT Hub desencadena la instancia de Azure Function App para guardar el mensaje en Azure Table Storage cuando llega un mensaje nuevo.
El comando `gulp run` ejecuta la aplicación de ejemplo de puerta de enlace que envía mensajes a IoT Hub. Con el parámetro `table-storage`, también genera un proceso secundario para recibir el mensaje guardado en Azure Table Storage.

Todos los mensajes que se envían y reciben se muestran al instante en la misma ventana de consola del equipo host. La instancia de la aplicación de ejemplo se cerrará automáticamente en 40 segundos.

   ![Lectura en Gulp](media/iot-hub-gateway-kit-lessons/lesson4/gulp_run_read_table_simudev.png)


## <a name="summary"></a>Resumen

Ha ejecutado el ejemplo de código para leer los mensajes en Azure Table Storage guardados por la aplicación de Azure Functions.

