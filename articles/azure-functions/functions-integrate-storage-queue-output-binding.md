---
title: "Crear una función en Azure desencadenada por mensajes en cola | Microsoft Docs"
description: "Use Azure Functions para crear una función sin servidor que se invoca mediante mensajes enviados a una cola de Azure Storage."
services: azure-functions
documentationcenter: na
author: ggailey777
manager: erikre
editor: 
tags: 
ms.assetid: 0b609bc0-c264-4092-8e3e-0784dcc23b5d
ms.service: functions
ms.devlang: multiple
ms.topic: get-started-article
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 05/02/2017
ms.author: glenga
ms.translationtype: Human Translation
ms.sourcegitcommit: 71fea4a41b2e3a60f2f610609a14372e678b7ec4
ms.openlocfilehash: 0e2501b0eb218d3c8a62dd4959b08ff85ec565eb
ms.contentlocale: es-es
ms.lasthandoff: 05/10/2017


---
# <a name="add-messages-to-an-azure-storage-queue-using-functions"></a>Agregar mensajes a una cola de Azure Storage con Functions

En Azure Functions, los enlaces de entrada y salida proporcionan una manera declarativa de conectarse a los datos de servicio externos desde su función. En este tema, obtendrá información sobre cómo actualizar una función existente agregando un enlace de salida que envía mensajes a Azure Queue Storage.  

![Vea el mensaje en los registros.](./media/functions-integrate-storage-queue-output-binding/functions-integrate-storage-binding-in-portal.png)

Debería tardar menos de cinco minutos en completar todos los pasos de este tema.

## <a name="prerequisites"></a>Requisitos previos 

[!INCLUDE [Previous topics](../../includes/functions-quickstart-previous-topics.md)]

También necesita descargar e instalar el [Explorador de Microsoft Azure Storage](http://storageexplorer.com/). 

[!INCLUDE [functions-portal-favorite-function-apps](../../includes/functions-portal-favorite-function-apps.md)] 

## <a name="add-binding"></a>Agregar un enlace de salida
 
1. Expanda su Function App y su función.

2. Haga clic en **Integrar** y **+ Nueva salida**, después haga clic en **Azure Queue Storage** y haga clic en **Seleccionar**.
    
    ![Agregue un enlace de salida de Queue Storage a una función en Azure Portal.](./media/functions-integrate-storage-queue-output-binding/function-add-queue-storage-output-binding.png)

3. Use la configuración especificada en la tabla y, después, haga clic en **Guardar**: 

    ![Agregue un enlace de salida de Queue Storage a una función en Azure Portal.](./media/functions-integrate-storage-queue-output-binding/function-add-queue-storage-output-binding-2.png)

    | Configuración      |  Valor sugerido   | Descripción                              |
    | ------------ |  ------- | -------------------------------------------------- |
    | **Nombre de la cola**   | myqueue-items    | El nombre de la cola a la que se va a conectar en la cuenta de almacenamiento. |
    | **Conexión de la cuenta de almacenamiento** | AzureWebJobStorage | Puede usar la conexión de cuenta de almacenamiento que ya usa la Function App o crear una nueva.  |
    | **Nombre del parámetro de mensaje** | outQueueItem | El nombre del parámetro del enlace de salida. | 

Ahora que tiene definido un enlace de salida, necesita actualizar el código que va a usar el enlace para agregar mensajes a una cola.  

## <a name="update-the-function-code"></a>Actualizar el código de la función

1. Haga clic en su función para mostrar el código de función en el editor. 

2. Para una función de C#, actualice la definición de función de la manera siguiente para agregar el parámetro de enlace de almacenamiento **outQueueItem**. Omita este paso para una función de JavaScript.

    ```cs   
    public static async Task<HttpResponseMessage> Run(HttpRequestMessage req, 
        ICollector<string> outQueueItem, TraceWriter log)
    {
        ....
    }
    ```

3. Agregue el siguiente código a la función justo antes de que se devuelva el método. Use el fragmento de código adecuado para el lenguaje de su función.

    ```javascript
    context.bindings.outQueueItem = "Name passed to the function: " + 
                (req.query.name || req.body.name);
    ```

    ```cs
    outQueueItem.Add("Name passed to the function: " + name);     
    ```

4. Haga clic en **Guardar** para guardar los cambios.

El valor que se ha pasado al desencadenador HTTP se incluye en un mensaje agregado a la cola.
 
## <a name="test-the-function"></a>Prueba de la función 

1. Después de que se guarden los cambios del código, haga clic en **Ejecutar**. 

    ![Agregue un enlace de salida de Queue Storage a una función en Azure Portal.](./media/functions-integrate-storage-queue-output-binding/functions-test-run-function.png)

2. Compruebe los registros para asegurarse de que la función se ha realizado correctamente. Se crea una nueva cola denominada **outqueue** en su cuenta de almacenamiento mediante el entorno de ejecución de las funciones cuando el enlace de salida se usa por primera vez.

Después, puede conectarse a su cuenta de almacenamiento para comprobar la cola nueva y el mensaje que ha agregado en esta. 

## <a name="connect-to-the-queue"></a>Conectarse a la cola

Omita los tres primeros pasos si ya ha instalado el Explorador de almacenamiento y lo ha conectado a su cuenta de almacenamiento.    

1. En su función, haga clic en **Integrar** y en el nuevo enlace de salida de **Azure Queue Storage**. Después, expanda **Documentación**. Copie el **Nombre de cuenta** y la **Clave de cuenta**. Use estas credenciales para conectarse a la cuenta de almacenamiento.
 
    ![Obtenga las credenciales de conexión de la cuenta de almacenamiento.](./media/functions-integrate-storage-queue-output-binding/function-get-storage-account-credentials.png)

2. Ejecute la herramienta [Explorador de Microsoft Azure Storage](http://storageexplorer.com/), haga clic en el icono de conexión situado a la izquierda, seleccione **Use a storage account name and key** (Usar el nombre y la clave de una cuenta de almacenamiento) y haga clic en **Siguiente**.

    ![Ejecute la herramienta Explorador de la cuenta de almacenamiento.](./media/functions-integrate-storage-queue-output-binding/functions-storage-manager-connect-1.png)
    
3. Escriba los valores de **Nombre de cuenta** y **Clave de cuenta** del paso 1, haga clic en **Siguiente** y, después, en **Conectar**. 
  
    ![Escriba las credenciales de almacenamiento y conéctese.](./media/functions-integrate-storage-queue-output-binding/functions-storage-manager-connect-2.png)

4. Expanda la cuenta de almacenamiento adjunta, haga clic con el botón derecho en **Colas** y compruebe que existe una cola denominada **myqueue-items**. También debe ver ya un mensaje en la cola.  
 
    ![Cree una cola de almacenamiento.](./media/functions-integrate-storage-queue-output-binding/function-queue-storage-output-view-queue.png)
 

## <a name="clean-up-resources"></a>Limpieza de recursos

[!INCLUDE [Next steps note](../../includes/functions-quickstart-cleanup.md)]

## <a name="next-steps"></a>Pasos siguientes

Ha agregado un enlace de salida a una función existente. 

[!INCLUDE [Next steps note](../../includes/functions-quickstart-next-steps.md)]

Para obtener más información sobre los enlaces a Queue Storage, vea [Enlaces de cola de Storage en Azure Functions](functions-bindings-storage-queue.md). 




