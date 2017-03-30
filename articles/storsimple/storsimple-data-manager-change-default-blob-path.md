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
translationtype: Human Translation
ms.sourcegitcommit: 0d8472cb3b0d891d2b184621d62830d1ccd5e2e7
ms.openlocfilehash: 8a06e4b3f482943f55e998c3c68857d3530ff98a
ms.lasthandoff: 03/21/2017

---

# <a name="change-blob-path-from-the-default-private-preview"></a>Cambio del valor predeterminado de la ruta de acceso del blob (versión preliminar privada)

En este artículo se describe cómo configurar una función de Azure para cambiar el nombre de una ruta de acceso del archivo de blob predeterminada. 

## <a name="prerequisites"></a>Requisitos previos

Antes de comenzar, asegúrese de que dispone de:
* Una definición del trabajo que se ha configurado correctamente en un recurso de datos híbridos de un grupo de recursos.

## <a name="create-an-azure-function"></a>Creación de una función de Azure

Lleve a cabo estos pasos para crear una función de Azure.

#### <a name="to-create-an-azure-function"></a>Para crear una función de Azure

1. Vaya al [Portal de Azure](http://portal.azure.com/).

2. Haga clic en **+ Nuevo** en la esquina superior izquierda. Escriba "Function App" en el cuadro de texto de **búsqueda** y presione **Entrar**.

    ![Ir al recurso de Function App](./media/storsimple-data-manager-change-default-blob-path/goto-function-app-resource.png)

3. Haga clic en **Function App** en los resultados.

    ![Seleccionar el recurso de Function App](./media/storsimple-data-manager-change-default-blob-path/select-function-app-resource.png)

4. Abra la ventana **Function App** y haga clic en **Crear**.

    ![Crear una nueva instancia de Function App](./media/storsimple-data-manager-change-default-blob-path/create-new-function-app.png)

5. En la hoja **Configuración**, escriba todas las entradas y haga clic en **Crear**.

    1. Nombre de la aplicación
    2. Subscription
    3. Grupos de recursos
    4. Plan de hospedaje: **plan de consumo**
    5. Ubicación
    6. Cuenta de almacenamiento: use una cuenta de almacenamiento existente o cree una nueva. Una cuenta de almacenamiento se usa de forma interna para la función.

        ![Escribir los datos de configuración de la nueva instancia de Function App](./media/storsimple-data-manager-change-default-blob-path/enter-new-funcion-app-data.png)

6. Una vez que se cree la instancia de Function App, vaya a **Más servicios >** en la parte inferior izquierda. Escriba "App Services" en el cuadro de texto **Filtrar** y haga clic en **App Services**.

    ![Más servicios >](./media/storsimple-data-manager-change-default-blob-path/more-services.png)

7. Haga clic en **Nombre de Function App** en la lista de App Services.

8. Haga clic en **+ Nueva función**. Seleccione **C#** en la lista desplegable de **lenguajes**. Seleccione la opción **QueueTrigger-CSharp** en la lista de plantillas. Escribir todas las entradas.

   1. Nombre: proporcione un nombre para la función.
   2. Nombre de la cola: escriba el **nombre de la definición del trabajo de Transformación de datos**.
   3. Conexión de cuenta de almacenamiento: haga clic en la opción **Nuevo**. Seleccione la cuenta que corresponde al trabajo de Transformación de datos.
      
      Anote el `Connection name`. La función de Azure requerirá este nombre más adelante.

   4. Haga clic en el botón **Crear**.

       ![Crear una nueva función C Sharp >](./media/storsimple-data-manager-change-default-blob-path/create-new-csharp-function.png)

9. En la ventana **Función**, ejecute el archivo _.csx_. Copie y pegue el código siguiente:

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
            log.Info($"Blob Uri: {myQueueItem.TargetLocation}");
            CloudStorageAccount storageAccount = CloudStorageAccount.Parse(ConfigurationManager.AppSettings["STORAGE_CONNECTIONNAME"]);

            // Create the blob client.
            CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

            string uriString = "windows.net/";
            string containerName = myQueueItem.TargetLocation;
            containerName = containerName.Substring(containerName.IndexOf(uriString) + uriString.Length);
            containerName = containerName.Substring(0, containerName.IndexOf("/"));
            //log.Info($"Container name: {containerName}");

            CloudBlobContainer container = blobClient.GetContainerReference(containerName);

            if(!container.Exists())
            {
                log.Info($"Container - {containerName} not exists");
                return;
            }

            string containerUri = string.Format("{0}/", container.Uri.ToString());  // Reading container Uri
            string blobUri =  myQueueItem.TargetLocation;
            string blobName = blobUri.Replace(containerUri, string.Empty).Replace("%20", " "); // Reading existing file path after container name
            string newBlobName = blobName.Substring(blobName.IndexOf(string.Format("{0}/", container.Name)) + container.Name.Length + 1); // Reading actual File path after container name

            log.Info($"Blob name: {blobName}");
            log.Info($"New blob name: {newBlobName}");

            CloudBlockBlob blobCopy = container.GetBlockBlobReference(newBlobName);
            CloudBlockBlob blob = container.GetBlockBlobReference(blobName);
            if (!blobCopy.Exists())
            {
                blobCopy.StartCopy(blob);
                // Delete old blob, after copy the blob
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

   1. Reemplace **STORAGE_CONNECTIONNAME** exactamente en la línea 12 por la conexión de cuenta de almacenamiento (consulte el punto 8c).
   2. Haga clic en el botón **Guardar** en la esquina superior izquierda.

       ![Guardar función >](./media/storsimple-data-manager-change-default-blob-path/save-function.png)

10.  Haga clic en **Ver archivos** en la esquina superior derecha.

    ![Ver archivos](./media/storsimple-data-manager-change-default-blob-path/view-files.png)

   1. Haga clic en **+ Agregar**. Escriba **project.json** y presione **Entrar**.
   2. Copie y pegue el código siguiente en el archivo **project.json**.

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

   2. Haga clic en **Guardar**.

Acaba de crear una función de Azure. Esta función se desencadena cada vez que el trabajo de Transformación de datos genera un blob nuevo.

## <a name="next-steps"></a>Pasos siguientes

[Manage using the StorSimple Data Manager service UI](storsimple-data-manager-ui.md) (Administración del uso de la interfaz de usuario del servicio StorSimple Data Manager).

