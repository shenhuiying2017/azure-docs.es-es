---
title: "Ejecución de una aplicación de ejemplo para enviar mensajes de dispositivo a nube a IoT Hub de Azure | Microsoft Docs"
description: "Implemente y ejecute una aplicación de ejemplo para Intel Edison que envíe mensajes a IoT Hub y haga parpadear el LED."
services: iot-hub
documentationcenter: 
author: shizn
manager: timtl
tags: 
keywords: "servicio en la nube de IoT, envío de datos de Arduino a la nube"
ms.assetid: 12672b64-795a-4dfc-86fd-df53ed3eeef7
ms.service: iot-hub
ms.devlang: c
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/8/2016
ms.author: xshi
translationtype: Human Translation
ms.sourcegitcommit: cb18052f74528e245264bb2f400b625fe550ed85
ms.openlocfilehash: 59db4ca0afcfd2757a725f5d56c0810f6547c924


---
# <a name="run-a-sample-application-to-send-device-to-cloud-messages"></a>Ejecución de una aplicación de ejemplo para enviar mensajes de dispositivo a nube
## <a name="what-you-will-do"></a>Lo que hará
En este artículo, se explica cómo implementar y ejecutar una aplicación de ejemplo en Intel Edison que envía mensajes a IoT Hub. Si tiene problemas, busque soluciones en [esta página][troubleshooting].

## <a name="what-you-will-learn"></a>Lo qué aprenderá
Aprenderá a utilizar la herramienta de Gulp para implementar y ejecutar la aplicación de ejemplo en C en Intel Edison.

## <a name="what-you-need"></a>Lo que necesita
* Para comenzar esta tarea, debe haber completado correctamente el tutorial [Creación de una instancia de Azure Function App y una cuenta de Azure Storage para procesar y guardar mensajes de IoT Hub][process-and-store-iot-hub-messages].

## <a name="get-your-iot-hub-and-device-connection-strings"></a>Obtención de las cadenas de conexión de IoT Hub y del dispositivo
La cadena de conexión del dispositivo se utiliza para conectar Intel Edison con IoT Hub. La cadena de conexión de IoT Hub se utiliza para conectar el centro de IoT Hub con la identidad de dispositivo que representa a Intel Edison en IoT Hub.

* Enumere todos los centros de IoT Hub del grupo de recursos ejecutando el siguiente comando de la CLI de Azure:

```bash
az iot hub list -g iot-sample --query [].name
```

Use `iot-sample` como valor de `{resource group name}`, si no lo modificó previamente.

* Obtenga la cadena de conexión de IoT Hub ejecutando el siguiente comando de la CLI de Azure:

```bash
az iot hub show-connection-string --name {my hub name}
```

`{my hub name}` es el nombre que especificó cuando creó el centro de IoT Hub y registró Intel Edison.

* Obtenga la cadena de conexión de dispositivos ejecutando el siguiente comando:

```bash
az iot device show-connection-string --hub-name {my hub name} --device-id myinteledison
```

Use `myinteledison` como valor de `{device id}`, si no lo modificó previamente.

## <a name="configure-the-device-connection"></a>Configuración de la conexión de dispositivos
1. Inicialice el archivo de configuración con los siguientes comandos:

   ```bash
   npm install
   gulp init
   ```
   > [!NOTE]
   > Ejecute también **gulp install-tools** si no lo hizo en la lección 1.

2. Abra el archivo de configuración de dispositivos `config-edison.json` en Visual Studio Code ejecutando el comando siguiente:

   ```bash
   # For Windows command prompt
   code %USERPROFILE%\.iot-hub-getting-started\config-edison.json

   # For MacOS or Ubuntu
   code ~/.iot-hub-getting-started/config-edison.json
   ```

   ![config.json](media/iot-hub-intel-edison-lessons/lesson3/config.png)

3. Realice las sustituciones siguientes en el archivo `config-edison.json`:

   * Reemplace **[nombre de host del dispositivo o dirección IP]** por la dirección IP del dispositivo que anotó cuando configuró el dispositivo.
   * Reemplace **[IoT device connection string]** con el valor de `device connection string` que ha obtenido.
   * Reemplace **[IoT hub connection string]** con el valor de `iot hub connection string` que ha obtenido.

   > [!NOTE]
   > `azure_storage_connection_string` no es necesario en este artículo. Déjelo como está.

## <a name="deploy-and-run-the-sample-application"></a>Implementación y ejecución de la aplicación de ejemplo
Implemente y ejecute la aplicación de ejemplo en Intel Edison usando el comando siguiente:

```bash
gulp deploy && gulp run
```

## <a name="verify-that-the-sample-application-works"></a>Comprobación del funcionamiento de la aplicación de ejemplo
El LED conectado a Intel Edison debería parpadear cada dos segundos. Cada vez que el LED parpadea, la aplicación de ejemplo envía un mensaje a IoT Hub y comprueba que el mensaje se envió correctamente a IoT Hub. Además, todos los mensajes que reciba IoT Hub se imprimirán en la ventana de consola. La aplicación de ejemplo se finaliza automáticamente después de enviar 20 mensajes.

![Aplicación de ejemplo con mensajes enviados y recibidos][sample-application-with-sent-and-received-messages]

## <a name="summary"></a>Resumen
Ha implementado y ejecutado la nueva aplicación de ejemplo de activación del parpadeo en Intel Edison para enviar al centro de IoT Hub mensajes de dispositivo a nube. Ahora, puede supervisar los mensajes a medida que se escriben en la cuenta de almacenamiento.

## <a name="next-steps"></a>Pasos siguientes
[Lectura de los mensajes que se conservan en Azure Storage][read-messages-persisted-in-azure-storage]
<!-- Images and links -->

[troubleshooting]: iot-hub-intel-edison-kit-c-troubleshooting.md
[process-and-store-iot-hub-messages]: iot-hub-intel-edison-kit-c-lesson3-deploy-resource-manager-template.md
[sample-application-with-sent-and-received-messages]: media/iot-hub-intel-edison-lessons/lesson3/gulp_run_c.png
[read-messages-persisted-in-azure-storage]: iot-hub-intel-edison-kit-c-lesson3-read-table-storage.md


<!--HONumber=Dec16_HO2-->


