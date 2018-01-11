---
title: "Crear una función en Azure desencadenada por Blob Storage | Microsoft Docs"
description: "Use Azure Functions para crear una función sin servidor que se invoca mediante elementos agregados a Azure Blob Storage."
services: azure-functions
documentationcenter: na
author: ggailey777
manager: cfowler
editor: 
tags: 
ms.assetid: d6bff41c-a624-40c1-bbc7-80590df29ded
ms.service: functions
ms.devlang: multiple
ms.topic: quickstart
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 12/07/2017
ms.author: glenga
ms.custom: mvc
ms.openlocfilehash: e34d3634b592efe4581135f9dee52bf77d7506cd
ms.sourcegitcommit: fa28ca091317eba4e55cef17766e72475bdd4c96
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/14/2017
---
# <a name="create-a-function-triggered-by-azure-blob-storage"></a>Crear una función desencadenada por Azure Blob Storage

Obtenga información sobre cómo crear una función que se desencadena cuando se cargan o se actualizan archivos en Azure Blob Storage.

![Vea el mensaje en los registros.](./media/functions-create-storage-blob-triggered-function/function-app-in-portal-editor.png)

## <a name="prerequisites"></a>Requisitos previos

+ Descargue e instale el [Explorador de Microsoft Azure Storage](http://storageexplorer.com/).
+ Una suscripción de Azure. Si no tiene una, cree una [cuenta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de empezar.

[!INCLUDE [functions-portal-favorite-function-apps](../../includes/functions-portal-favorite-function-apps.md)]

## <a name="create-an-azure-function-app"></a>Creación de una Function App de Azure

[!INCLUDE [Create function app Azure portal](../../includes/functions-create-function-app-portal.md)]

![Function App creada correctamente.](./media/functions-create-first-azure-function/function-app-create-success.png)

Después, cree una función en la nueva Function App.

<a name="create-function"></a>

## <a name="create-a-blob-storage-triggered-function"></a>Creación de una función desencadenada por Blob Storage

1. Expanda su instancia de Function App y haga clic en el botón **+**, que se encuentra junto a **Functions**. Si se trata de la primera función de Function App, seleccione **Función personalizada**. Se muestra el conjunto completo de plantillas de funciones.

    ![Página de inicio rápido de Functions en Azure Portal](./media/functions-create-storage-blob-triggered-function/add-first-function.png)

2. En el campo de búsqueda, escriba `blob` y seleccione el idioma que desee para la plantilla del desencadenador de Blob Storage.

    ![Elija la plantilla de desencadenador de Blob Storage.](./media/functions-create-storage-blob-triggered-function/functions-create-blob-storage-trigger-portal.png)
 
3. Utilice la configuración que se especifica en la tabla debajo de la imagen.

    ![Cree la función desencadenada por Blob Storage.](./media/functions-create-storage-blob-triggered-function/functions-create-blob-storage-trigger-portal-2.png)

    | Configuración | Valor sugerido | Descripción |
    |---|---|---|
    | **Name** | Único en la Function App | Nombre de la función desencadenada por este blob. |
    | **Ruta de acceso**   | samples-workitems/{name}    | Ubicación de Blob Storage que se está supervisando. El nombre de archivo del blob se pasa en el enlace como parámetro _name_.  |
    | **Conexión de la cuenta de almacenamiento** | AzureWebJobsStorage | Puede usar la conexión de cuenta de almacenamiento que ya usa la Function App o crear una nueva.  |

3. Haga clic en **Crear** para crear la función.

Después, conéctese a su cuenta de Azure Storage y cree el contenedor **samples-workitems**.

## <a name="create-the-container"></a>Cree el contenedor.

1. En la función, haga clic en **Integrar**, expanda **Documentación** y copie los dos valores de **Nombre de cuenta** y **Clave de cuenta**. Use estas credenciales para conectarse a la cuenta de almacenamiento. Si ya se ha conectado a la cuenta de almacenamiento, vaya al paso 4.

    ![Obtenga las credenciales de conexión de la cuenta de almacenamiento.](./media/functions-create-storage-blob-triggered-function/functions-storage-account-connection.png)

1. Ejecute la herramienta [Explorador de Microsoft Azure Storage](http://storageexplorer.com/), haga clic en el icono de conexión situado a la izquierda, seleccione **Use a storage account name and key** (Usar el nombre y la clave de una cuenta de almacenamiento) y haga clic en **Siguiente**.

    ![Ejecute la herramienta Explorador de la cuenta de almacenamiento.](./media/functions-create-storage-blob-triggered-function/functions-storage-manager-connect-1.png)

1. Escriba los valores de **Nombre de cuenta** y **Clave de cuenta** del paso 1, haga clic en **Siguiente** y, después, en **Conectar**. 

    ![Escriba las credenciales de almacenamiento y conéctese.](./media/functions-create-storage-blob-triggered-function/functions-storage-manager-connect-2.png)

1. Expanda la cuenta de almacenamiento asociada, haga clic con el botón derecho en **Contenedores de blobs**, haga clic en **Crear contenedor de blobs**, escriba `samples-workitems` y, después, presione ENTRAR.

    ![Cree una cola de almacenamiento.](./media/functions-create-storage-blob-triggered-function/functions-storage-manager-create-blob-container.png)

Ahora que tiene un contenedor de blobs, puede probar la función. Para ello, cargue un archivo en el contenedor.

## <a name="test-the-function"></a>Prueba de la función

1. De nuevo en Azure Portal, vaya a la función. Expanda **Registros** en la parte inferior de la página y asegúrese de que el streaming de registros no está en pausa.

1. En el Explorador de Storage, expanda la cuenta de almacenamiento, **Contenedores de blobs** y **samples-workitems**. Haga clic en **Cargar** y en **Cargar archivos…**

    ![Cargue un archivo en el contenedor de blobs.](./media/functions-create-storage-blob-triggered-function/functions-storage-manager-upload-file-blob.png)

1. En el cuadro de diálogo **Cargar archivos**, haga clic en el campo **Archivos**. Busque un archivo en el equipo local, como un archivo de imagen, selecciónelo y haga clic en **Abrir** y, después, en **Cargar**.

1. Vuelva a sus registros de función y compruebe que se ha leído el blob.

   ![Vea el mensaje en los registros.](./media/functions-create-storage-blob-triggered-function/functions-blob-storage-trigger-view-logs.png)

    >[!NOTE]
    > Cuando la Function App se ejecuta en el plan de consumo predeterminado, puede haber un retraso de hasta varios minutos entre el momento en que se agrega o se actualiza el blob y el momento en que se desencadena la función. Si necesita una latencia baja en las funciones desencadenadas por el blob, considere la posibilidad de ejecutar la Function App en un plan de App Service.

## <a name="clean-up-resources"></a>Limpieza de recursos

[!INCLUDE [Next steps note](../../includes/functions-quickstart-cleanup.md)]

## <a name="next-steps"></a>Pasos siguientes

Ha creado una función que se ejecuta cuando se agrega o se actualiza un blob en Blob Storage. 

[!INCLUDE [Next steps note](../../includes/functions-quickstart-next-steps.md)]

Para obtener más información sobre los desencadenadores de Blob Storage, vea [Enlaces de Blob Storage en Azure Functions](functions-bindings-storage-blob.md).
