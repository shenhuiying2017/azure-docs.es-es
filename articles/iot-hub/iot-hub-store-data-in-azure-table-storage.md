---
title: Procedimiento para guardar los mensajes de IoT Hub en el almacenamiento de datos de Azure | Microsoft Docs
description: "Use la aplicación de función de Azure para guardar mensajes de IoT Hub en Azure Table Storage. Los mensajes del IoT Hub contienen información, como datos del sensor, que se envía desde el dispositivo de IoT."
services: iot-hub
documentationcenter: 
author: shizn
manager: timlt
tags: 
keywords: almacenamiento de datos de iot, almacenamiento de datos del sensor de iot
ms.assetid: 62fd14fd-aaaa-4b3d-8367-75c1111b6269
ms.service: iot-hub
ms.devlang: arduino
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/16/2017
ms.author: xshi
ms.translationtype: HT
ms.sourcegitcommit: 540180e7d6cd02dfa1f3cac8ccd343e965ded91b
ms.openlocfilehash: 06503f9564e00ef62587d02f2da4778974e246c5
ms.contentlocale: es-es
ms.lasthandoff: 08/16/2017

---
# <a name="save-iot-hub-messages-that-contain-sensor-data-to-your-azure-table-storage"></a>Procedimiento para guardar mensajes de IoT Hub que contienen datos del sensor en Azure Table Storage

![Diagrama integral](media/iot-hub-get-started-e2e-diagram/3.png)

[!INCLUDE [iot-hub-get-started-note](../../includes/iot-hub-get-started-note.md)]

## <a name="what-you-learn"></a>Conocimientos que adquirirá

Aprenderá a crear una cuenta de Azure Storage y una aplicación de función de Azure para almacenar mensajes de IoT Hub en Table Storage.

## <a name="what-you-do"></a>Qué debe hacer

- Cree una cuenta de Azure Storage.
- Prepare la conexión de IoT Hub para que lea mensajes.
- Cree e implemente una aplicación de función de Azure.

## <a name="what-you-need"></a>Lo que necesita

- [Instalar el dispositivo](iot-hub-raspberry-pi-kit-node-get-started.md) para que cumpla los siguientes requisitos:
  - Una suscripción de Azure activa.
  - Una instancia de IoT Hub en su suscripción. 
  - Una aplicación en ejecución que envíe mensajes a su instancia de IoT Hub.

## <a name="create-an-azure-storage-account"></a>Creación de una cuenta de Azure Storage

1. En [Azure Portal](https://portal.azure.com/), haga clic en **Nuevo** > **Storage** > **Cuenta de Storage** > **Crear**.

2. Escriba la información necesaria para la cuenta de almacenamiento:

   ![Creación de una cuenta de almacenamiento en Azure Portal](media\iot-hub-store-data-in-azure-table-storage\1_azure-portal-create-storage-account.png)

   * **Nombre**: el nombre de la cuenta de almacenamiento. El nombre debe ser único globalmente.

   * **Grupo de recursos**: use el mismo grupo de recursos que usa el IoT Hub.

   * **Anclar al panel**: seleccione esta opción para facilitar el acceso a IoT Hub desde el panel.

3. Haga clic en **Crear**.

## <a name="prepare-your-iot-hub-connection-to-read-messages"></a>Preparación de la conexión de IoT Hub para que lea mensajes

IoT Hub expone un punto de conexión integrado compatible con el centro de eventos para permitir que las aplicaciones lean mensajes de IoT Hub. Mientras tanto, las aplicaciones usan grupos de consumidores para leer datos de IoT Hub. Antes de crear una aplicación de función de Azure para leer los datos del IoT Hub, necesita:

- Obtener la cadena de conexión del punto de conexión del IoT Hub.
- Crear un grupo de consumidores para el IoT Hub.

### <a name="get-the-connection-string-of-your-iot-hub-endpoint"></a>Obtener la cadena de conexión del punto de conexión del IoT Hub

1. Abra el IoT Hub.

2. En **Mensajería** del panel **IoT Hub**, haga clic en **Puntos de conexión**.

3. En el panel derecho, haga clic en **Eventos** en **Puntos de conexión integrados**.

4. Anote los siguientes valores del panel **Propiedades**:
   - Extremo compatible con Centro de eventos
   - Nombre compatible con Centro de eventos

   ![Obtener la cadena de conexión del punto de conexión del IoT Hub en Azure Portal](media\iot-hub-store-data-in-azure-table-storage\2_azure-portal-iot-hub-endpoint-connection-string.png)

5. En **Configuración** del panel **IoT Hub**, haga clic en **Directivas de acceso compartido**.

6. Haga clic en **iothubowner**.

7. Anote el valor de **Clave principal**.

8. Cree la cadena de conexión de su punto de conexión de IoT Hub de la manera siguiente:

   `Endpoint=<Event Hub-compatible endpoint>;SharedAccessKeyName=iothubowner;SharedAccessKey=<Primary key>`

   > [!NOTE]
   > Reemplace `<Event Hub-compatible endpoint>` y `<Primary key>` por los valores que anotó anteriormente.

### <a name="create-a-consumer-group-for-your-iot-hub"></a>Crear un grupo de consumidores para el IoT Hub

1. Abra el IoT Hub.

2. En **Mensajería** del panel **IoT Hub**, haga clic en **Puntos de conexión**.

3. En el panel derecho, haga clic en **Eventos** en **Puntos de conexión integrados**.

4. En el panel **Propiedades**, escriba un nombre en **Grupos de consumidores** y anótelo.

5. Haga clic en **Guardar**.

## <a name="create-and-deploy-an-azure-function-app"></a>Creación e implementación de una aplicación de función de Azure

1. En [Azure Portal](https://portal.azure.com/), haga clic en **Nuevo** > **Compute** > **Function App** > **Crear**.

2. Escriba la información necesaria para la aplicación de función.

   ![Creación de una aplicación de función en Azure Portal](media\iot-hub-store-data-in-azure-table-storage\3_azure-portal-create-function-app.png)

   * **Nombre de la aplicación**: el nombre de la aplicación de función. El nombre debe ser único globalmente.

   * **Grupo de recursos**: use el mismo grupo de recursos que usa el IoT Hub.

   * **Cuenta de Storage**: la cuenta de almacenamiento que ha creado.

   * **Anclar al panel**: marque esta opción para facilitar el acceso a la aplicación de función desde el panel.

3. Haga clic en **Crear**.

4. Una vez creada la aplicación de función, ábrala.

5. En la aplicación de función, haga lo siguiente para crear una función:

   a. Haga clic en **Nueva función**.

   b. Seleccione **JavaScript** para **Lenguaje** y **Procesamiento de datos** para **Escenario**.

   c. Haga clic en **Crear esta función** y en **Nueva función**.

   d. Seleccione **JavaScript** para el lenguaje y **Procesamiento de datos** para el escenario.

   e. Haga clic en la plantilla **EventHubTrigger-JavaScript**.

   f. Escriba la información necesaria para la plantilla.

      * **Nombre de la función**: el nombre de la función.

      * **Nombre del Centro de eventos**: el nombre compatible con el centro de eventos que anotó.

      * **Conexión de Centro de eventos**: para agregar la cadena de conexión del punto de conexión de IoT Hub creado, haga clic en **Nuevo**.

   g. Haga clic en **Crear**.

6. Configure una salida de la función de la siguiente manera:

   a. Haga clic en **Integrar** > **Nueva salida** > **Azure Table Storage** > **Seleccionar**.

      ![Incorporación de una tabla de almacenamiento a la aplicación de función en Azure Portal](media\iot-hub-store-data-in-azure-table-storage\4_azure-portal-function-app-add-output-table-storage.png)

   b. Escriba la información necesaria.

      * **Nombre de parámetro de tabla**: use `outputTable`, que se utilizará en el código de la función de Azure.
      
      * **Nombre de la tabla**: use `deviceData`.

      * **Conexión de cuenta de Storage**: haga clic en **Nuevo** y seleccione o escriba la cuenta de Storage. Si la cuenta de almacenamiento no aparece, consulte [Requisitos de la cuenta de almacenamiento](https://docs.microsoft.com/azure/azure-functions/functions-create-function-app-portal#storage-account-requirements).
      
   c. Haga clic en **Guardar**.

7. En **Desencadenadores**, haga clic en **Azure Event Hubs (eventHubMessages)**.

8. En **Grupo de consumidores del Centro de eventos**, escriba el nombre del grupo de consumidores que creó y haga clic en **Guardar**.

9. Haga clic en la función que ha creado en la parte izquierda y en **Ver archivos** de la derecha.

10. Reemplace el código de `index.js` por lo siguiente:

   ```javascript
   'use strict';

   // This function is triggered each time a message is received in the IoT hub.
   // The message payload is persisted in an Azure storage table
 
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

11. Haga clic en **Guardar**.

Acaba de crear la aplicación de función, que almacena mensajes que la instancia de IoT Hub recibe en Table Storage.

> [!NOTE]
> Puede usar el botón **Ejecutar** para probar la aplicación de función. Al hacer clic en **Ejecutar**, se envía el mensaje de prueba al IoT Hub. La llegada del mensaje debe desencadenar que se inicie la aplicación de función; después, guarde el mensaje en Table Storage. El panel **Registros** registra los detalles del proceso.

## <a name="verify-your-message-in-your-table-storage"></a>Comprobación del mensaje en Table Storage

1. Ejecute la aplicación de ejemplo en el dispositivo para enviar mensajes a su IoT Hub.

2. [Descargue e instale el Explorador de Azure Storage](http://storageexplorer.com/).

3. Abra el Explorador de Storage, haga clic en **Add an Azure Account** (Agregar una cuenta de Azure)  > **Iniciar sesión** e inicie sesión en su cuenta de Azure.

4. Haga clic en la suscripción de Azure > **Cuentas de almacenamiento** > su cuenta de almacenamiento > **Tablas** > **deviceData**.

   Verá que los mensajes enviados desde el dispositivo a su IoT Hub se han registrado en la tabla `deviceData`.

## <a name="next-steps"></a>Pasos siguientes

Ha creado correctamente la cuenta de Azure Storage y la aplicación de función de Azure, donde se almacenan los mensajes que recibe la instancia de IoT Hub de Table Storage.

[!INCLUDE [iot-hub-get-started-next-steps](../../includes/iot-hub-get-started-next-steps.md)]

