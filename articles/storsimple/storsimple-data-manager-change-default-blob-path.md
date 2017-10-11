---
title: Cambio del valor predeterminado de la ruta de acceso del blob | Microsoft Docs
description: "Aprenda a configurar una función de Azure para cambiar el nombre de una ruta de acceso del archivo de blob (versión preliminar privada)"
services: storsimple
documentationcenter: NA
author: vidarmsft
manager: syadav
editor: 
ms.assetid: 
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 03/16/2017
ms.author: vidarmsft
ms.openlocfilehash: 057d4d7370207859617eb63238bf425bfa6d3e16
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 07/11/2017
---
# <a name="change-a-blob-path-from-the-default-path-private-preview"></a>Cambio del valor predeterminado de la ruta de acceso de un blob (versión preliminar privada)

En este artículo se describe cómo configurar una función de Azure para cambiar el nombre de una ruta de acceso de archivo de blob predeterminada. 

## <a name="prerequisites"></a>Requisitos previos

Asegúrese de que tiene una definición del trabajo que se ha configurado correctamente en un recurso de datos híbridos de un grupo de recursos.

## <a name="create-an-azure-function"></a>Creación de una función de Azure

Para crear una función de Azure, realice estos pasos:

1. Vaya al [Portal de Azure](http://portal.azure.com/).

2. En la parte superior del panel izquierdo, haga clic en **Nuevo**. 

3. En el cuadro de **Búsqueda**, escriba **Funcion App** y presione Entrar.

    ![Escritura de "Function App" en el cuadro de búsqueda](./media/storsimple-data-manager-change-default-blob-path/goto-function-app-resource.png)

4. En la lista **Results** (Resultados), haga clic en **Function App**.

    ![Selección del recurso Function App en la lista de resultados](./media/storsimple-data-manager-change-default-blob-path/select-function-app-resource.png)

    Se abrirá la ventana **Function App**.

5. Haga clic en **Crear**.

    ![Botón "Crear" de la ventana de Function App](./media/storsimple-data-manager-change-default-blob-path/create-new-function-app.png)

6. En la hoja de configuración **Function App**, haga lo siguiente:

    a. En el cuadro **Nombre de aplicación**, escriba el nombre de la aplicación.
    
    b. En el cuadro **Suscripción**, escriba el nombre de la suscripción.

    c. En **Grupo de recursos**, haga clic en **Crear nuevo** y escriba el nombre del grupo de recursos.

    d. En el cuadro **Plan de hospedaje**, escriba **Plan de consumo**.

    e. En el cuadro **Ubicación**, escriba la ubicación.

    f. En **Cuenta de almacenamiento**, seleccione una cuenta de almacenamiento existente o cree una nueva. Una cuenta de almacenamiento se usa de forma interna para la función.

    ![Introducción de los datos de configuración de Function App](./media/storsimple-data-manager-change-default-blob-path/enter-new-funcion-app-data.png)

7. Haga clic en **Crear**.  
    La instancia de Function App se creará.

8. En el panel izquierdo, haga clic en **More services** (Más servicios), y realice lo siguiente:
    
    a. En el cuadro **Filtro**, escriba **App Services**.
    
    b. Haga clic en **App Services**. 

    ![Vínculo "More services" (Más servicios" del panel izquierdo](./media/storsimple-data-manager-change-default-blob-path/more-services.png)

9. En la lista de instancias de App Services, haga clic en el nombre de la instancia de Function App.  
    La página de la instancia de Function App se abrirá.

10. En el panel izquierdo, haga clic en **New Function** (Función nueva) y realice lo siguiente: 

    a. En la lista **Language** (Lenguaje), seleccione **C#**.
    
    b. En la matriz de iconos de plantilla, seleccione **QueueTrigger CSharp**.

    c. En el cuadro **Asigne un nombre a la función**, escriba un nombre para la función.

    d. En el cuadro **Queue name** (Nombre de la cola), escriba el nombre de la definición del trabajo de transformación de datos.

    e. En **Conexión de cuenta de almacenamiento**, haga clic en **Nueva** y seleccione la cuenta que corresponda al trabajo de transformación de datos.  
        Anote el nombre de la conexión. La función de Azure necesitará este nombre más adelante.

       ![Creación de una función de C#](./media/storsimple-data-manager-change-default-blob-path/create-new-csharp-function.png)

    f. Haga clic en **Crear**.  
    Se abrirá la ventana **Función**.

11. En la ventana **Función**, ejecute el archivo _.csx_ y haga lo siguiente:

    a. Pegue el código siguiente:

    ```
    using System;
    using System.Configuration;
    using Microsoft.WindowsAzure.Storage.Blob;
    using Microsoft.WindowsAzure.Storage.Queue;
    using Microsoft.WindowsAzure.Storage;
    using System.Collections.Generic;
    using System.Linq;

    public static void Run(QueueItem myQueueItem, TraceWriter log)
    {
        CloudStorageAccount storageAccount = CloudStorageAccount.Parse(ConfigurationManager.AppSettings["STORAGE_CONNECTIONNAME"]);

        string storageAccUriEndswith = "windows.net/";
        string uri = myQueueItem.TargetLocation.Replace("%20", " ");
        log.Info($"Blob Uri: {uri}");

        // Remove storage account uri string
        uri = uri.Substring(uri.IndexOf(storageAccUriEndswith) + storageAccUriEndswith.Length);

        string containerName = uri.Substring(0, uri.IndexOf("/")); 

        // Remove container name string
        uri = uri.Substring(containerName.Length + 1);

        // Current blob path
        string blobName = uri; 

        string volumeName = uri.Substring(containerName.Length + 1);
        volumeName = uri.Substring(0, uri.IndexOf("/"));

        // Remove volume name string
        uri = uri.Substring(volumeName.Length + 1);

        string newContainerName = uri.Substring(0, uri.IndexOf("/")).ToLower();
        string newBlobName = uri.Substring(newContainerName.Length + 1);

        log.Info($"Container name: {containerName}");
        log.Info($"Volume name: {volumeName}");
        log.Info($"New container name: {newContainerName}");

        log.Info($"Blob name: {blobName}");
        log.Info($"New blob name: {newBlobName}");

        // Create the blob client.
        CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

        // Container reference
        CloudBlobContainer container = blobClient.GetContainerReference(containerName);
        CloudBlobContainer newContainer = blobClient.GetContainerReference(newContainerName);
        newContainer.CreateIfNotExists();

        if(!container.Exists())
        {
            log.Info($"Container - {containerName} not exists");
            return;
        }

        if(!newContainer.Exists())
        {
            log.Info($"Container - {newContainerName} not exists");
            return;
        }

        CloudBlockBlob blob = container.GetBlockBlobReference(blobName);
        if (!blob.Exists())
        {
            // Skip to copy the blob to new container, if source blob doesn't exist
            log.Info($"The specified blob does not exist.");
            log.Info($"Blob Uri: {blob.Uri}");
            return;
        }

        CloudBlockBlob blobCopy = newContainer.GetBlockBlobReference(newBlobName);
        if (!blobCopy.Exists())
        {
            blobCopy.StartCopy(blob);
            // Delete old blob, after copy to new container
            blob.DeleteIfExists();
            log.Info($"Blob file path renamed completed successfully");
        }
        else
        {
            log.Info($"Blob file path renamed already done");
            // Delete old blob, if already exists.
            blob.DeleteIfExists();
        }
    }

    public class QueueItem
    {
        public string SourceLocation {get;set;}
        public long SizeInBytes {get;set;}
        public string Status {get;set;}
        public string JobID {get;set;}
        public string TargetLocation {get; set;}
    }

    ```

    b. Reemplace **STORAGE_CONNECTIONNAME** en la línea 11 por la conexión de cuenta de almacenamiento (consulte el punto 8c).

    c. En la parte superior izquierda, haga clic en **Guardar**.

    ![Función Guardar](./media/storsimple-data-manager-change-default-blob-path/save-function.png)

12. Para completar la función, haga lo siguiente para agregar un archivo más:

    a. Haga clic en **View files** (Ver archivos).

       ![Vínculo "View files" (Ver archivos)](./media/storsimple-data-manager-change-default-blob-path/view-files.png)

    b. Haga clic en **Agregar**.
    
    c. Escriba **project.json** y presione Entrar.
    
    d. En el archivo **project.json**, peque el siguiente código:

    ```
    {
    "frameworks": {
        "net46":{
        "dependencies": {
            "windowsazure.storage": "8.1.1"
        }
        }
    }
    }

    ```

    e. Haga clic en **Guardar**.

Acaba de crear una función de Azure. Esta función se desencadena cada vez que el trabajo de transformación de datos genera un blob nuevo.

## <a name="next-steps"></a>Pasos siguientes

[Uso de la IU de StorSimple Data Manager para transformar los datos](storsimple-data-manager-ui.md)
