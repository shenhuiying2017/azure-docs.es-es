---
title: Lectura de mensajes guardados en Azure Table Storage | Microsoft Docs
description: "Guarde los mensajes de Intel NUC en IoT Hub, escríbalos en Azure Table Storage y léalos en la nube."
services: iot-hub
documentationcenter: 
author: shizn
manager: timtl
tags: 
keywords: recuperar datos de la nube, servicio en la nube de iot
ms.assetid: 8ca78045-ad92-4a6a-90f1-05f9668e6f0e
ms.service: iot-hub
ms.devlang: c
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/28/2016
ms.author: xshi
translationtype: Human Translation
ms.sourcegitcommit: 65a25dd7a2f6a8d518217512f9e10fc9008ee728
ms.openlocfilehash: a1007ac2db53c2c378cf4312b73765ef490e0ad6


---

# <a name="read-messages-persisted-in-azure-table-storage"></a>Lectura de los mensajes que se conservan en Azure Table Storage

## <a name="what-you-will-do"></a>Lo que hará

- Ejecute la aplicación de ejemplo de puerta de enlace en la puerta de enlace que envía mensajes a IoT Hub.
- Después ejecute un código de ejemplo en el equipo host para leer los mensajes en Azure Table Storage. 

Si tiene problemas, busque soluciones en la [página de solución de problemas](iot-hub-gateway-kit-c-troubleshooting.md).

## <a name="what-you-will-learn"></a>Lo qué aprenderá

Cómo utilizar la herramienta de Gulp para ejecutar el código de ejemplo para leer mensajes en Azure Table Storage.

## <a name="what-you-need"></a>Lo que necesita

Ha realizado correctamente las tareas siguientes:

- [Creación de una instancia de Azure Function App y una cuenta de Azure Storage](iot-hub-gateway-kit-c-lesson4-deploy-resource-manager-template.md).
- [Ejecución de la aplicación de ejemplo de puerta de enlace](iot-hub-gateway-kit-c-lesson3-configure-ble-app.md).
- [Lectura de mensajes en IoT Hub](iot-hub-gateway-kit-c-lesson3-read-messages-from-hub.md).

## <a name="get-your-azure-storage-connection-strings"></a>Obtener cadenas de conexión de Azure Storage

Al principio de esta lección, creó correctamente una cuenta de Azure Storage. Para obtener la cadena de conexión de la cuenta de Azure Storage, ejecute los siguientes comandos:

* Enumere todas las cuentas de almacenamiento.

```bash
az storage account list -g iot-gateway --query [].name
```

* Obtenga la cadena de conexión de Azure Storage.

```bash
az storage account show-connection-string -g iot-gateway -n {storage name}
```

Use iot-gateway como el valor de `{resource group name}` si no ha cambiado el valor en la lección 2.

## <a name="configure-the-device-connection"></a>Configuración de la conexión de dispositivos

Actualice el archivo `config-azure.json` para que el código de ejemplo que se ejecuta en el equipo host pueda leer el mensaje en Azure Table Storage. Para configurar la conexión de dispositivos, siga estos pasos:

1. Genere el archivo de configuración del dispositivo `config-azure.json` mediante la ejecución de los comandos siguientes:

   ```bash
   # For Windows command prompt
   code %USERPROFILE%\.iot-hub-getting-started\config-azure.json
   # For MacOS or Ubuntu
   code ~/.iot-hub-getting-started/config-azure.json
   ```

   ![configuración](media/iot-hub-gateway-kit-lessons/lesson4/config_azure.png)

2. Reemplace `[Azure storage connection string]` con la cadena de conexión de Azure Storage obtenida.

   `[IoT hub connection string]` ya se debe haber reemplazado en la sección [Lectura de mensajes en Azure IoT Hub](iot-hub-gateway-kit-c-lesson3-read-messages-from-hub.md) de la lección 3.

## <a name="read-messages-in-your-azure-table-storage"></a>Lectura de mensajes en Azure Table Storage

Ejecute la aplicación de ejemplo de puerta de enlace y lea mensajes en Azure Table Storage mediante la ejecución del siguiente comando:

```bash
gulp run --table-storage
```

IoT Hub desencadena la instancia de Azure Function App para guardar el mensaje en Azure Table Storage cuando llega un mensaje nuevo.
El comando `gulp run` ejecuta la aplicación de ejemplo de puerta de enlace que envía mensajes a IoT Hub. Con el parámetro `table-storage`, también genera un proceso secundario para recibir el mensaje guardado en Azure Table Storage.

Todos los mensajes que se envían y reciben se muestran al instante en la misma ventana de consola del equipo host. La instancia de la aplicación de ejemplo se cerrará automáticamente en 40 segundos.

   ![Lectura en Gulp](media/iot-hub-gateway-kit-lessons/lesson4/gulp_run_read_table.png)


## <a name="summary"></a>Resumen

Ha ejecutado el código de ejemplo para leer los mensajes en Azure Table Storage guardados por la instancia de Azure Function App.


<!--HONumber=Dec16_HO3-->


