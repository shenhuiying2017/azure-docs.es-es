---
title: "Crear una función en Azure desencadenada por mensajes en cola | Microsoft Docs"
description: "Use Azure Functions para crear una función sin servidor que se invoca mediante mensajes enviados a una cola de Azure Storage."
services: azure-functions
documentationcenter: na
author: ggailey777
manager: cfowler
editor: 
tags: 
ms.assetid: 361da2a4-15d1-4903-bdc4-cc4b27fc3ff4
ms.service: functions
ms.devlang: multiple
ms.topic: quickstart
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 05/31/2017
ms.author: glenga
ms.custom: mvc
ms.openlocfilehash: 3fd5a5b9d2e2eec485fd9ecc5380ad6adb9851d0
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="create-a-function-triggered-by-azure-queue-storage"></a>Crear una función desencadenada por Azure Queue Storage

Obtenga información sobre cómo crear una función que se desencadena cuando se envían mensajes a una cola de Azure Storage.

![Vea el mensaje en los registros.](./media/functions-create-storage-queue-triggered-function/function-app-in-portal-editor.png)

## <a name="prerequisites"></a>Requisitos previos

- Descargue e instale el [Explorador de Microsoft Azure Storage](http://storageexplorer.com/).

- Una suscripción de Azure. Si no tiene una, cree una [cuenta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de empezar.

[!INCLUDE [functions-portal-favorite-function-apps](../../includes/functions-portal-favorite-function-apps.md)]

## <a name="create-an-azure-function-app"></a>Creación de una Function App de Azure

[!INCLUDE [Create function app Azure portal](../../includes/functions-create-function-app-portal.md)]

![Function App creada correctamente.](./media/functions-create-first-azure-function/function-app-create-success.png)

Después, cree una función en la nueva Function App.

<a name="create-function"></a>

## <a name="create-a-queue-triggered-function"></a>Creación de una función desencadenada por el servicio Queue

1. Expanda su instancia de Function App y haga clic en el botón **+**, que se encuentra junto a **Functions**. Si se trata de la primera función de Function App, seleccione **Función personalizada**. Se muestra el conjunto completo de plantillas de funciones.

    ![Página de inicio rápido de Functions en Azure Portal](./media/functions-create-storage-queue-triggered-function/add-first-function.png)

2. Seleccione la plantilla **QueueTrigger** de idioma que desee y use la configuración que se especifica en la tabla.

    ![Cree la función desencadenada por la cola de almacenamiento.](./media/functions-create-storage-queue-triggered-function/functions-create-queue-storage-trigger-portal.png)
    
    | Configuración | Valor sugerido | Descripción |
    |---|---|---|
    | **Asigne un nombre a la función** | Único en la Function App | Nombre de la función desencadenada por la cola. |
    | **Nombre de la cola**   | myqueue-items    | Nombre de la cola a la que se va a conectar en la cuenta de almacenamiento. |
    | **Conexión de cuenta de Storage** | AzureWebJobStorage | Puede usar la conexión de cuenta de almacenamiento que ya usa la Function App o crear una nueva.  |    

3. Haga clic en **Crear** para crear la función.

Después, conéctese a su cuenta de Azure Storage y cree la cola de almacenamiento **myqueue-items**.

## <a name="create-the-queue"></a>Creación de la cola

1. En la función, haga clic en **Integrar**, expanda **Documentación** y copie los dos valores de **Nombre de cuenta** y **Clave de cuenta**. Use estas credenciales para conectarse a la cuenta de almacenamiento en el Explorador de Azure Storage. Si ya se ha conectado a la cuenta de almacenamiento, vaya al paso 4.

    ![Obtenga las credenciales de conexión de la cuenta de almacenamiento.](./media/functions-create-storage-queue-triggered-function/functions-storage-account-connection.png)

1. Ejecute la herramienta [Explorador de Microsoft Azure Storage](http://storageexplorer.com/), haga clic en el icono de conexión situado a la izquierda, seleccione **Use a storage account name and key** (Usar el nombre y la clave de una cuenta de almacenamiento) y haga clic en **Siguiente**.

    ![Ejecute la herramienta Explorador de la cuenta de almacenamiento.](./media/functions-create-storage-queue-triggered-function/functions-storage-manager-connect-1.png)

1. Escriba los valores de **Nombre de cuenta** y **Clave de cuenta** del paso 1, haga clic en **Siguiente** y, después, en **Conectar**.

    ![Escriba las credenciales de almacenamiento y conéctese.](./media/functions-create-storage-queue-triggered-function/functions-storage-manager-connect-2.png)

1. Expanda la cuenta de almacenamiento asociada, haga clic con el botón derecho en **Colas**, haga clic en **Crear cola**, escriba `myqueue-items` y, después, presione ENTRAR.

    ![Cree una cola de almacenamiento.](./media/functions-create-storage-queue-triggered-function/functions-storage-manager-create-queue.png)

Ahora que tiene una cola de almacenamiento, puede probar la función. Para ello, agregue un mensaje a la cola.

## <a name="test-the-function"></a>Prueba de la función

1. De nuevo en Azure Portal, vaya a la función. Expanda **Registros** en la parte inferior de la página y asegúrese de que el streaming de registros no está en pausa.

1. En el Explorador de almacenamiento, expanda la cuenta de almacenamiento, **Colas** y **myqueue-items** y, después, haga clic en **Agregar mensaje**.

    ![Agregue un mensaje a la cola.](./media/functions-create-storage-queue-triggered-function/functions-storage-manager-add-message.png)

1. Escriba su mensaje "Hola mundo" en **Texto del mensaje** y haga clic en **Aceptar**.

1. Espere unos segundos y, después, vuelva a los registros de función para comprobar que se ha leído el mensaje nuevo de la cola.

    ![Vea el mensaje en los registros.](./media/functions-create-storage-queue-triggered-function/functions-queue-storage-trigger-view-logs.png)

1. En el Explorador de almacenamiento, haga clic en **Actualizar** y compruebe que el mensaje se ha procesado y ya no está en la cola.

## <a name="clean-up-resources"></a>Limpieza de recursos

[!INCLUDE [Next steps note](../../includes/functions-quickstart-cleanup.md)]

## <a name="next-steps"></a>Pasos siguientes

Ha creado una función que se ejecuta cuando se agrega un mensaje a una cola de almacenamiento.

[!INCLUDE [Next steps note](../../includes/functions-quickstart-next-steps.md)]

Para obtener más información sobre los desencadenadores de Queue Storage, vea [Enlaces de colas de Storage en Azure Functions](functions-bindings-storage-queue.md).
