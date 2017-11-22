---
title: Procedimiento para guardar los mensajes de IoT Hub en el almacenamiento de datos de Azure | Microsoft Docs
description: "Use el enrutamiento de mensajes de IoT Hub para guardar los mensajes de IoT Hub en Azure Blob Storage. Los mensajes del IoT Hub contienen información, como datos del sensor, que se envía desde el dispositivo de IoT."
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
ms.date: 10/04/2017
ms.author: xshi
ms.openlocfilehash: 5419f0fb86f2a7b051ffc7fda17c74cf15178a6b
ms.sourcegitcommit: c25cf136aab5f082caaf93d598df78dc23e327b9
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/15/2017
---
# <a name="save-iot-hub-messages-that-contain-sensor-data-to-your-azure-blob-storage"></a>Almacenamiento de mensajes de IoT Hub que contienen datos del sensor en Azure Blob Storage

![Diagrama integral](media/iot-hub-store-data-in-azure-table-storage/1_route-to-storage.png)

[!INCLUDE [iot-hub-get-started-note](../../includes/iot-hub-get-started-note.md)]

## <a name="what-you-learn"></a>Conocimientos que adquirirá

Aprenderá a crear una cuenta de almacenamiento de Azure y una aplicación de función de Azure para almacenar mensajes de IoT Hub en su almacenamiento de blobs.

## <a name="what-you-do"></a>Qué debe hacer

- Cree una cuenta de Azure Storage.
- Prepare su instancia de IoT Hub para enrutar mensajes al almacenamiento.

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

## <a name="prepare-your-iot-hub-to-route-messages-to-storage"></a>Preparación de una instancia de IoT Hub para enrutar mensajes al almacenamiento

IoT Hub admite de forma nativa enrutar mensajes a Azure Storage como blobs.

### <a name="add-storage-as-a-custom-endpoint"></a>Adición de almacenamiento como punto de conexión personalizado

Vaya a IoT Hub en Azure Portal. Haga clic en **Puntos de conexión** > **Agregar**. Asigne un nombre al punto de conexión y seleccione **Contenedor de almacenamiento de Azure** como el tipo de punto de conexión. Utilice el selector para seleccionar la cuenta de almacenamiento que creó en la sección anterior. Cree un contenedor de almacenamiento, selecciónelo y, después, haga clic en **Aceptar**.

  ![Creación de un punto de conexión personalizado en IoT Hub](media\iot-hub-store-data-in-azure-table-storage\2_custom-storage-endpoint.png)

### <a name="add-a-route-to-route-data-to-storage"></a>Adición de una ruta para enrutar datos al almacenamiento

Haga clic en **Rutas** > **Agregar** y escriba un nombre para la ruta. Seleccione **Mensajes del dispositivo** como origen de datos y seleccione el punto de conexión de almacenamiento recién creado como el punto de conexión de la ruta. Escriba `true` como la cadena de consulta y después haga clic en **Guardar**.

  ![Creación de una ruta en IoT Hub](media\iot-hub-store-data-in-azure-table-storage\3_create-route.png)
  
### <a name="add-a-route-for-hot-path-telemetry-optional"></a>Adición de una ruta para telemetría de ruta de acceso activa (opcional)

De forma predeterminada, IoT Hub enruta todos los mensajes que no coinciden con ninguna otra ruta al punto de conexión integrado. Puesto que todos los mensajes de telemetría ahora coinciden con la regla que enruta los mensajes al almacenamiento, debe agregar otra ruta para que los mensajes se escriban en el punto de conexión integrado. No se aplica ningún cargo adicional por enrutar mensajes a varios puntos de conexión.

> [!NOTE]
> Puede omitir este paso si no realiza un procesamiento adicional de los mensajes de telemetría.

Haga clic en **Agregar** desde el panel de Rutas y escriba un nombre para la ruta. Seleccione **Mensajes del dispositivo** como el origen de datos y, **eventos** como el punto de conexión. Escriba `true` como la cadena de consulta y después haga clic en **Guardar**.

  ![Creación de una ruta de ruta de acceso activa en IoT Hub](media\iot-hub-store-data-in-azure-table-storage\4_hot-path-route.png)

## <a name="verify-your-message-in-your-storage-container"></a>Verificación del mensaje en el contenedor de almacenamiento

1. Ejecute la aplicación de ejemplo en el dispositivo para enviar mensajes a su IoT Hub.

2. [Descargue e instale el Explorador de Azure Storage](http://storageexplorer.com/).

3. Abra el Explorador de Storage, haga clic en **Add an Azure Account** (Agregar una cuenta de Azure)  > **Iniciar sesión** e inicie sesión en su cuenta de Azure.

4. Haga clic en la suscripción de Azure > **Cuentas de almacenamiento** > su cuenta de almacenamiento > **Contenedores de blob** > su contenedor.

   Verá que los mensajes enviados desde el dispositivo a IoT Hub se han registrado en el contenedor de blobs.

## <a name="next-steps"></a>Pasos siguientes

Ha creado correctamente la cuenta de Azure Storage y ha enrutado los mensajes desde IoT Hub a un contenedor de blobs en esa cuenta de almacenamiento.

[!INCLUDE [iot-hub-get-started-next-steps](../../includes/iot-hub-get-started-next-steps.md)]
