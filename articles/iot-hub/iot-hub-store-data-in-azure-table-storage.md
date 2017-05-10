---
title: Procedimiento para guardar los mensajes de IoT Hub en el almacenamiento de datos de Azure | Microsoft Docs
description: "Use Azure Function App para guardar mensajes de IoT Hub en Azure Table Storage. Los mensajes del IoT Hub contienen información, como datos del sensor, que se envía desde el dispositivo de IoT."
services: iot-hub
documentationcenter: 
author: shizn
manager: timtl
tags: 
keywords: almacenamiento de datos de iot, almacenamiento de datos del sensor de iot
ms.assetid: 62fd14fd-aaaa-4b3d-8367-75c1111b6269
ms.service: iot-hub
ms.devlang: arduino
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/27/2017
ms.author: xshi
ms.translationtype: Human Translation
ms.sourcegitcommit: aaf97d26c982c1592230096588e0b0c3ee516a73
ms.openlocfilehash: 4918648906212ea9708b6c6f0e89d1f4bb7bdcc5
ms.contentlocale: es-es
ms.lasthandoff: 04/27/2017


---
# <a name="save-iot-hub-messages-that-contain-information-like-sensor-data-to-azure-table-storage"></a>Procedimiento para guardar mensajes de IoT Hub que contienen información, como datos del sensor, en Azure Table Storage

![Diagrama integral](media/iot-hub-get-started-e2e-diagram/3.png)

[!INCLUDE [iot-hub-get-started-note](../../includes/iot-hub-get-started-note.md)]

## <a name="what-you-will-learn"></a>Lo qué aprenderá

Aprenderá a crear una cuenta de Azure Storage y una aplicación de función de Azure para almacenar mensajes de IoT Hub en Azure Table Storage.

## <a name="what-you-will-do"></a>Lo que hará

- Cree una cuenta de Azure Storage.
- Prepare la conexión de IoT Hub para que lea mensajes.
- Cree e implemente una aplicación de función de Azure.

## <a name="what-you-will-need"></a>Qué necesita

- Tutorial [Instalación de su dispositivo](iot-hub-raspberry-pi-kit-node-get-started.md) completado donde se abordan los siguientes requisitos:
  - Una suscripción de Azure activa.
  - Un centro de Azure IoT en su suscripción.
  - Una aplicación en ejecución que envía mensajes a su centro de Azure IoT.

## <a name="create-an-azure-storage-account"></a>Creación de una cuenta de Azure Storage

1. En Azure Portal, haga clic en **Nuevo** > **Storage** > **Cuenta de Storage**.
1. Escriba la información necesaria para la cuenta de almacenamiento:

   ![Creación de una cuenta de almacenamiento en Azure Portal](media\iot-hub-store-data-in-azure-table-storage\1_azure-portal-create-storage-account.png)

   **Nombre**: el nombre de la cuenta de almacenamiento. El nombre debe ser único globalmente.

   **Grupo de recursos**: use el mismo grupo de recursos que utiliza el IoT Hub.

   **Anclar al panel**: marque esta opción para facilitar el acceso al IoT Hub desde el panel.
1. Haga clic en **Crear**.

## <a name="prepare-for-iot-hub-connection-to-read-messages"></a>Preparación de la conexión de IoT Hub para leer mensajes

IoT Hub expone un punto de conexión integrado compatible con Event Hub para permitir que las aplicaciones lean mensajes de IoT Hub. Mientras tanto, las aplicaciones usan grupos de consumidores para leer datos de IoT Hub. Antes de crear una aplicación de función de Azure para leer datos del IoT Hub, necesita:

- Obtener la cadena de conexión del punto de conexión del IoT Hub.
- Crear un grupo de consumidores para el IoT Hub.

### <a name="get-the-connection-string-of-your-iot-hub-endpoint"></a>Obtener la cadena de conexión del punto de conexión del IoT Hub

1. Abra el IoT Hub.
1. En el panel **IoT Hub**, haga clic en **Puntos de conexión** en **MENSAJERÍA**.
1. En el panel derecho, haga clic en **Eventos** en **Built-in endpoints** (Puntos de conexión integrados).
1. En el panel **Propiedades**, tome nota de los siguientes valores:
   - Punto de conexión compatible con Event Hub
   - Nombre compatible con Event Hub

   ![Obtener la cadena de conexión del punto de conexión del IoT Hub en Azure Portal](media\iot-hub-store-data-in-azure-table-storage\2_azure-portal-iot-hub-endpoint-connection-string.png)

1. En el panel **IoT Hub**, haga clic en **Directivas de acceso compartido** en **CONFIGURACIÓN**.
1. Haga clic en **iothubowner**.
1. Anote el valor de la **clave principal**.
1. Componga la cadena de conexión de su punto de conexión del IoT Hub de la manera siguiente:

   `Endpoint=<Event Hub-compatible endpoint>;SharedAccessKeyName=iothubowner;SharedAccessKey=<Primary key>`

   > [!Note]
   > Reemplace `<Event Hub-compatible endpoint>` y `<Primary key>` por los valores que anotó.

### <a name="create-a-consumer-group-for-your-iot-hub"></a>Crear un grupo de consumidores para el IoT Hub

1. Abra el IoT Hub.
1. En el panel **IoT Hub**, haga clic en **Puntos de conexión** en **MENSAJERÍA**.
1. En el panel derecho, haga clic en **Eventos** en **Built-in endpoints** (Puntos de conexión integrados).
1. En el panel **Propiedades**, escriba un nombre en **Grupos de consumidores** y anote el nombre.
1. Haga clic en **Guardar**.

## <a name="create-and-deploy-an-azure-function-app"></a>Creación e implementación de una aplicación de función de Azure

1. En [Azure Portal](https://portal.azure.com/), haga clic en **Nuevo** > **Compute** > **Function App**.
1. Escriba la información necesaria para la aplicación de función.

   ![Creación de una aplicación de función en Azure Portal](media\iot-hub-store-data-in-azure-table-storage\3_azure-portal-create-function-app.png)

   **Nombre de la aplicación**: el nombre de la aplicación de función. El nombre debe ser único globalmente.

   **Grupo de recursos**: use el mismo grupo de recursos que utiliza el IoT Hub.

   **Cuenta de Storage**: la cuenta de almacenamiento que ha creado.

   **Anclar al panel**: marque esta opción para facilitar el acceso a la aplicación de función desde el panel.
1. Haga clic en **Crear**.
1. Cuando termine de crear la aplicación de función, ábrala.
1. Cree una nueva función en la aplicación de función.
   1. Haga clic en **Nueva función**.
   1. Seleccione **JavaScript** para **Lenguaje** y **Procesamiento de datos** para **Escenario**.
   1. Haga clic en la plantilla **EventHubTrigger-JavaScript**.
   1. Escriba la información necesaria para la plantilla.

      **Nombre de la función**: el nombre de la función.

      **Nombre del Centro de eventos**: el nombre compatible con Event Hub que anotó.

      **Conexión de Centro de eventos**: haga clic en Nuevo para agregar la cadena de conexión de su punto de conexión de IoT que ha compuesto.
   1. Haga clic en **Crear**.
1. Configure una salida de la función.
   1. Haga clic en **Integrar** > **Nueva salida** > **Azure Table Storage** > **Seleccionar**.

      ![Agregar una tabla de almacenamiento a la aplicación de función en Azure Portal](media\iot-hub-store-data-in-azure-table-storage\4_azure-portal-function-app-add-output-table-storage.png)
   1. Escriba la información necesaria.

      **Nombre de parámetro de tabla**: use `outputTable` para el nombre, que se utilizará en el código de Azure Functions.
      
      **Nombre de la tabla**: use `deviceData` para el nombre.

      **Conexión de cuenta de Storage**: haga clic en **Nueva** y seleccione o escriba la cuenta de almacenamiento.
   1. Haga clic en **Guardar**.
1. En **Desencadenadores**, haga clic en **Azure Event Hub (myEventHubTrigger)**.
1. En **Grupo de consumidores del Centro de eventos**, escriba el nombre del grupo de consumidores que creó y haga clic en **Guardar**.
1. Haga clic en **Desarrollar** y luego en **Ver archivos**.
1. Reemplace el código de `index.js` por los siguientes valores y luego haga clic en **Guardar**.

   ```javascript
   'use strict';

   // This function is triggered each time a message is revieved in the IoTHub.
   // The message payload is persisted in an Azure Storage Table
 
   module.exports = function (context, iotHubMessage) {
    context.log('Message received: ' + JSON.stringify(iotHubMessage));
    var date = Date.now();
    var partitionKey = Math.floor(date / (24 * 60 * 60 * 1000)) + '';
    var rowKey = date + '';
    context.bindings.outputTable = {
     "partitionKey": partitionKey,
     "rowKey": rowKey,
     "message": JSON.stringify(iotHubMessage)
    };
    context.done();
   };
   ```

Por ahora, ha creado la aplicación de función. Dicha aplicación almacena mensajes que su IoT Hub recibe en su instancia de Azure Table Storage.

> [!Note]
> Puede usar el botón **Ejecutar** para probar la aplicación de función. Al hacer clic en **Ejecutar**, se envía el mensaje de prueba al IoT Hub. La llegada del mensaje debe desencadenar que se inicie la aplicación de función y luego guarde el mensaje en su instancia de Azure Table Storage. El panel **Registros** registra los detalles del proceso.

## <a name="verify-your-message-in-your-table-storage"></a>Comprobación del mensaje en Table Storage

1. Ejecute la aplicación de ejemplo en el dispositivo para enviar mensajes a su IoT Hub.
1. [Descargue e instale el Explorador de Microsoft Azure Storage](http://storageexplorer.com/).
1. Abra el Explorador de Microsoft Azure Storage, haga clic en **Agregar una cuenta de Azure** > **Iniciar sesión** y luego inicie sesión en su cuenta de Azure.
1. Haga clic en la suscripción de Azure > **Cuentas de almacenamiento** > su cuenta de almacenamiento > **Tablas** > **deviceData**.

   Verá que los mensajes enviados desde el dispositivo a su IoT Hub se han registrado en la tabla `deviceData`.

## <a name="next-steps"></a>Pasos siguientes

Ha creado correctamente la cuenta de Azure Storage y la aplicación de función de Azure para almacenar los mensajes que recibe su IoT Hub en su instancia de Azure Table Storage.

[!INCLUDE [iot-hub-get-started-next-steps](../../includes/iot-hub-get-started-next-steps.md)]

