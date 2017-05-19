---
title: "Crear una función en Azure desencadenada por Blob Storage | Microsoft Docs"
description: "Use Azure Functions para crear una función sin servidor que se invoca mediante elementos agregados a Azure Blob Storage."
services: azure-functions
documentationcenter: na
author: ggailey777
manager: erikre
editor: 
tags: 
ms.assetid: d6bff41c-a624-40c1-bbc7-80590df29ded
ms.service: functions
ms.devlang: multiple
ms.topic: get-started-article
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 05/02/2017
ms.author: glenga
ms.translationtype: Human Translation
ms.sourcegitcommit: 71fea4a41b2e3a60f2f610609a14372e678b7ec4
ms.openlocfilehash: b00f9e7491048a5dd60e0decab1727a1ff01448e
ms.contentlocale: es-es
ms.lasthandoff: 05/10/2017


---
# <a name="create-a-function-triggered-by-azure-blob-storage"></a>Crear una función desencadenada por Azure Blob Storage

Obtenga información sobre cómo crear una función que se desencadena cuando se cargan o se actualizan archivos en Azure Blob Storage.  

![Vea el mensaje en los registros.](./media/functions-create-storage-blob-triggered-function/function-app-in-portal-editor.png)

Debería tardar menos de cinco minutos en completar todos los pasos de este tema.

## <a name="prerequisites"></a>Requisitos previos

[!INCLUDE [Previous quickstart note](../../includes/functions-quickstart-previous-topics.md)]

También debe descargar e instalar el [Explorador de Microsoft Azure Storage](http://storageexplorer.com/). 

[!INCLUDE [functions-portal-favorite-function-apps](../../includes/functions-portal-favorite-function-apps.md)] 

## <a name="create-function"></a>Crear una función desencadenada por Blob Storage

Expanda la Function App, haga clic en el botón **+** situado junto a **Funciones** y haga clic en la plantilla **BlobTrigger** para el idioma que quiera. Después, use la configuración especificada en la tabla y haga clic en **Crear**.

![Cree la función desencadenada por Blob Storage.](./media/functions-create-storage-blob-triggered-function/functions-create-blob-storage-trigger-portal.png)
    
| Configuración      |  Valor sugerido   | Descripción                                        |
| ------------ |  ----------------- | -------------------------------------------------- |
| **Ruta de acceso**   | mycontainer/{name}    | Ubicación de Blob Storage que se está supervisando. El nombre de archivo del blob se pasa en el enlace como parámetro _name_.  |
| **Conexión de cuenta de Storage** | AzureWebJobStorage | Puede usar la conexión de cuenta de almacenamiento que ya usa la Function App o crear una.  |
| **Asigne un nombre a la función** | Único en la Function App | Nombre de la función desencadenada por la cola. | 

Después, conéctese a su cuenta de Azure Storage y cree el contenedor **mycontainer**.

## <a name="create-the-container"></a>Cree el contenedor.

1. En la función, haga clic en **Integrar**, expanda **Documentación** y copie los dos valores de **Nombre de cuenta** y **Clave de cuenta**. Use estas credenciales para conectarse a la cuenta de almacenamiento. Si ya se ha conectado a la cuenta de almacenamiento, vaya al paso 4.
 
    ![Obtenga las credenciales de conexión de la cuenta de almacenamiento.](./media/functions-create-storage-blob-triggered-function/functions-storage-account-connection.png)

2. Ejecute la herramienta [Explorador de Microsoft Azure Storage](http://storageexplorer.com/), haga clic en el icono de conexión situado a la izquierda, seleccione **Use a storage account name and key** (Usar el nombre y la clave de una cuenta de almacenamiento) y haga clic en **Siguiente**.

    ![Ejecute la herramienta Explorador de la cuenta de almacenamiento.](./media/functions-create-storage-blob-triggered-function/functions-storage-manager-connect-1.png)
    
3. Escriba los valores de **Nombre de cuenta** y **Clave de cuenta** del paso 1, haga clic en **Siguiente** y, después, en **Conectar**. 
  
    ![Escriba las credenciales de almacenamiento y conéctese.](./media/functions-create-storage-blob-triggered-function/functions-storage-manager-connect-2.png)

4. Expanda la cuenta de almacenamiento asociada, haga clic con el botón derecho en **Contenedores de blobs**, haga clic en **Crear contenedor de blobs**, escriba `mycontainer` y, después, presione ENTRAR.
 
    ![Cree una cola de almacenamiento.](./media/functions-create-storage-blob-triggered-function/functions-storage-manager-create-blob-container.png)

Ahora que tiene un contenedor de blobs, puede probar la función. Para ello, cargue un archivo en el contenedor.  

## <a name="test-the-function"></a>Prueba de la función

1. De nuevo en Azure Portal, vaya a la función. Expanda **Registros** en la parte inferior de la página y asegúrese de que el streaming de registros no está en pausa.

2. En el Explorador de almacenamiento, expanda la cuenta de almacenamiento, **Contenedores de blobs** y **mycontainer**. Haga clic en **Cargar** y en **Cargar archivos…**

    ![Cargue un archivo en el contenedor de blobs.](./media/functions-create-storage-blob-triggered-function/functions-storage-manager-upload-file-blob.png)

2. En el cuadro de diálogo **Cargar archivos**, haga clic en el campo **Archivos**. Busque un archivo en el equipo local, como un archivo de imagen, selecciónelo y haga clic en **Abrir** y, después, en **Cargar**.   
 
3. Vuelva a sus registros de función y compruebe que se ha leído el blob. 

   ![Vea el mensaje en los registros.](./media/functions-create-storage-blob-triggered-function/functions-blob-storage-trigger-view-logs.png)

    >[!NOTE]
    > Cuando la Function App se ejecuta en el plan de consumo predeterminado, puede haber un retraso de hasta varios minutos entre el momento en que se agrega o se actualiza el blob y el momento en que se desencadena la función. Si necesita una latencia baja en las funciones desencadenadas por el blob, considere la posibilidad de ejecutar la Function App en un plan de App Service. 

## <a name="clean-up-resources"></a>Limpieza de recursos

[!INCLUDE [Next steps note](../../includes/functions-quickstart-cleanup.md)]

## <a name="next-steps"></a>Pasos siguientes

Ha creado una función que se ejecuta cuando se agrega o se actualiza un blob en Blob Storage. 

[!INCLUDE [Next steps note](../../includes/functions-quickstart-next-steps.md)]

Para obtener más información sobre los desencadenadores de Blob Storage, vea [Enlaces de Blob Storage en Azure Functions](functions-bindings-storage-blob.md). 




