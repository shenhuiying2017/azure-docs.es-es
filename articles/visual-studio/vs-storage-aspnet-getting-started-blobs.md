---
title: "Introducción a Azure Blob Storage y los servicios conectados de Visual Studio (ASP.NET) | Microsoft Docs"
description: "Cómo empezar a usar el almacenamiento de blobs de Azure en un proyecto ASP.NET en Visual Studio después de conectarse a una cuenta de almacenamiento mediante los servicios conectados de Visual Studio"
services: storage
documentationcenter: 
author: kraigb
manager: ghogen
editor: 
ms.assetid: b3497055-bef8-4c95-8567-181556b50d95
ms.service: storage
ms.workload: web
ms.tgt_pltfrm: vs-getting-started
ms.devlang: na
ms.topic: article
ms.date: 12/21/2016
ms.author: kraig
ms.openlocfilehash: e953c7978705379a28581213e8f1c665473ddd60
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="get-started-with-azure-blob-storage-and-visual-studio-connected-services-aspnet"></a>Introducción a Azure Blob Storage y los servicios conectados de Visual Studio (ASP.NET)
[!INCLUDE [storage-try-azure-tools-blobs](../../includes/storage-try-azure-tools-blobs.md)]

## <a name="overview"></a>Información general

Azure Blob Storage es un servicio que almacena datos no estructurados en la nube como objetos o blobs. El Almacenamiento de blobs puede almacenar cualquier tipo de datos binarios o texto, como un documento, un archivo multimedia o un instalador de aplicación. El Almacenamiento de blobs a veces se conoce como "almacenamiento de objetos".

Este tutorial muestra cómo escribir código ASP.NET para algunos escenarios comunes con Azure Blob Storage. Los escenarios incluyen la creación de un contenedor de blob y la carga, enumeración, descarga y eliminación de blobs.

##<a name="prerequisites"></a>Requisitos previos

* [Microsoft Visual Studio](https://www.visualstudio.com/downloads/)
* [Cuenta de Azure Storage](../storage/common/storage-create-storage-account.md#create-a-storage-account)

[!INCLUDE [storage-blob-concepts-include](../../includes/storage-blob-concepts-include.md)]

[!INCLUDE [storage-create-account-include](../../includes/vs-storage-aspnet-getting-started-create-azure-account.md)]

[!INCLUDE [storage-development-environment-include](../../includes/vs-storage-aspnet-getting-started-setup-dev-env.md)]

### <a name="create-an-mvc-controller"></a>Creación de un controlador MVC 

1. En el **Explorador de soluciones**, haga clic con el botón derecho en **Controladores** y, en el menú contextual, seleccione **Agregar > Controlador**.

    ![Incorporación de un controlador a una aplicación de ASP.NET MVC](./media/vs-storage-aspnet-getting-started-blobs/add-controller-menu.png)

1. En el cuadro de diálogo **Agregar scaffold**, seleccione **Controlador MVC 5 - Vacío** y seleccione **Agregar**.

    ![Especifique el tipo de controlador MVC](./media/vs-storage-aspnet-getting-started-blobs/add-controller.png)

1. En el cuadro de diálogo **Agregar controlador**, denomine *BlobsController* al controlador y seleccione **Agregar**.

    ![Asignación de nombre al controladorMVC](./media/vs-storage-aspnet-getting-started-blobs/add-controller-name.png)

1. Agregue las siguientes directivas *using* al archivo `BlobsController.cs`:

    ```csharp
    using Microsoft.Azure;
    using Microsoft.WindowsAzure.Storage;
    using Microsoft.WindowsAzure.Storage.Auth;
    using Microsoft.WindowsAzure.Storage.Blob;
    ```

## <a name="create-a-blob-container"></a>Creación de un contenedor de blobs

Un contenedor de blob es una jerarquía anidada de blobs y carpetas. Los siguientes pasos muestran cómo crear un contenedor de blobs:

> [!NOTE]
> 
> El código de esta sección da por supuesto que ha completado los pasos de la sección [Configuración del entorno de desarrollo](#set-up-the-development-environment). 

1. Abra el archivo `BlobsController.cs` .

1. Agregue un método llamado **CreateBlobContainer** que devuelve un **ActionResult**.

    ```csharp
    public ActionResult CreateBlobContainer()
    {
        // The code in this section goes here.

        return View();
    }
    ```
 
1. Dentro del método **CreateBlobContainer**, obtenga un objeto **CloudStorageAccount** que represente la información de su cuenta de almacenamiento. Use el código siguiente para obtener la cadena de conexión de almacenamiento y la información de la cuenta de almacenamiento de la configuración del servicio de Azure. (Cambie *&lt;storage-account-name>* por el nombre de la cuenta de Azure Storage a la que está obteniendo acceso).
   
    ```csharp
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
       CloudConfigurationManager.GetSetting("<storage-account-name>_AzureStorageConnectionString"));
    ```
   
1. Obtenga un objeto **CloudTableClient** que representa un cliente de servicio de blob.
   
    ```csharp
    CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();
    ```

1. Obtenga un objeto **CloudBlobContainer** que representa una referencia al nombre de contenedor de blobs preferido. El método **CloudBlobClient.GetContainerReference** no hace ninguna solicitud en el almacenamiento de blobs. Se devuelve la referencia tanto si el contenedor de blobs existe como si no. 
   
    ```csharp
    CloudBlobContainer container = blobClient.GetContainerReference("test-blob-container");
    ```

1. Llame al método **CloudTable.CreateIfNotExists** para crear el contenedor si aún no existe. El método **CloudBlobContainer.CreateIfNotExists** devuelve **true** si el contenedor no existía pero se creó correctamente. En caso contrario, se devuelve **false**.    

    ```csharp
    ViewBag.Success = container.CreateIfNotExists();
    ```

1. Actualización de **ViewBag** con el nombre del contenedor de blobs.

    ```csharp
    ViewBag.BlobContainerName = container.Name;
    ```

1. En el **Explorador de soluciones**, expanda la carpeta **Vistas**, haga clic con el botón derecho en **Blobs** y, en el menú contextual, seleccione **Agregar > Vista**.

1. En el cuadro de diálogo **Agregar vista**, escriba **CreateBlobContainer** para el nombre de la vista y seleccione **Agregar**.

1. Abra `CreateBlobContainer.cshtml` y modifíquelo de modo que se parezca al siguiente fragmento de código:

    ```csharp
    @{
        ViewBag.Title = "Create Blob Container";
    }
    
    <h2>Create Blob Container results</h2>

    Creation of @ViewBag.BlobContainerName @(ViewBag.Success == true ? "succeeded" : "failed")
    ```

1. En el **Explorador de soluciones**, expanda la carpeta **Vistas->Compartido** y abra `_Layout.cshtml`.

1. Después del último **Html.ActionLink**, agregue el siguiente **Html.ActionLink**:

    ```html
    <li>@Html.ActionLink("Create blob container", "CreateBlobContainer", "Blobs")</li>
    ```

1. Ejecute la aplicación y seleccione **Crear contenedor de blobs** para ver resultados similares a la siguiente captura de pantalla:
  
    ![Creación de un contenedor de blobs](./media/vs-storage-aspnet-getting-started-blobs/create-blob-container-results.png)

    Como se dijo anteriormente, el método **CloudBlobContainer.CreateIfNotExists** devuelve **true** solo si el contenedor no existía pero se creó. Por lo tanto, si se ejecuta la aplicación cuando el contenedor existe, el método devuelve **false**. Para ejecutar la aplicación varias veces, debe eliminar el contenedor antes de ejecutar la aplicación de nuevo. La eliminación del contenedor se puede hacer mediante el método **CloudBlobContainer.Delete**. También puede eliminar el contenedor mediante [Azure Portal](http://go.microsoft.com/fwlink/p/?LinkID=525040) o [Microsoft Azure Storage Explorer](../vs-azure-tools-storage-manage-with-storage-explorer.md).  

## <a name="upload-a-blob-into-a-blob-container"></a>Carga de un blob en un contenedor de blobs

Una vez que haya [creado un contenedor de blobs](#create-a-blob-container), puede cargar archivos en ese contenedor. En esta sección le explicamos cómo cargar un archivo local en un contenedor de blobs. En estos pasos se supone que ha creado un contenedor de blobs denominado *test-blob-container*. 

> [!NOTE]
> 
> El código de esta sección da por supuesto que ha completado los pasos de la sección [Configuración del entorno de desarrollo](#set-up-the-development-environment). 

1. Abra el archivo `BlobsController.cs` .

1. Agregue un método llamado **UploadBlob** que devuelve un **EmptyResult**.

    ```csharp
    public EmptyResult UploadBlob()
    {
        // The code in this section goes here.

        return new EmptyResult();
    }
    ```
 
1. Dentro del método **UploadBlob**, obtenga un objeto **CloudStorageAccount** que represente la información de su cuenta de almacenamiento. Utilice el código siguiente para obtener la información de cuenta de almacenamiento y la cadena de conexión de almacenamiento de la configuración del servicio de Azure: (cambie *&lt;storage-account-name>*  por el nombre de la cuenta de Azure Storage a la que está obteniendo acceso.)
   
    ```csharp
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
       CloudConfigurationManager.GetSetting("<storage-account-name>_AzureStorageConnectionString"));
    ```
   
1. Obtenga un objeto **CloudTableClient** que representa un cliente de servicio de blob.
   
    ```csharp
    CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();
    ```

1. Obtenga un objeto **CloudBlobContainer** que representa una referencia al nombre del contenedor de blobs. 
   
    ```csharp
    CloudBlobContainer container = blobClient.GetContainerReference("test-blob-container");
    ```

1. Como se explicó anteriormente, el almacenamiento de Azure es compatible con diferentes tipos de blob. Para recuperar una referencia a un blob en páginas, llame al método **CloudBlobContainer.GetPageBlobReference**. Para recuperar una referencia a un blob en bloques, llame al método **CloudBlobContainer.GetBlockBlobReference**. Normalmente, se recomienda usar blobs en bloques. (Cambie <blob-name>* por el nombre que desea dar al blob una vez cargado).

    ```csharp
    CloudBlockBlob blob = container.GetBlockBlobReference(<blob-name>);
    ```

1. Una vez que disponga de una referencia de blob, puede cargar cualquier secuencia de datos en ella llamando al método **UploadFromStream** del objeto de referencia de blob. El método **UploadFromStream** permite crear el blob si no existe, o sobrescribirlo si ya existe. (Cambie *&lt;archivo que se va a cargar>* a una ruta de acceso completa al archivo que desea cargar).

    ```csharp
    using (var fileStream = System.IO.File.OpenRead(<file-to-upload>))
    {
        blob.UploadFromStream(fileStream);
    }
    ```

1. En el **Explorador de soluciones**, expanda la carpeta **Vistas**, haga clic con el botón derecho en **Blobs** y, en el menú contextual, seleccione **Agregar > Vista**.

1. En el **Explorador de soluciones**, expanda la carpeta **Vistas->Compartido** y abra `_Layout.cshtml`.

1. Después del último **Html.ActionLink**, agregue el siguiente **Html.ActionLink**:

    ```html
    <li>@Html.ActionLink("Upload blob", "UploadBlob", "Blobs")</li>
    ```

1. Ejecute la aplicación y seleccione **Cargar blob**.  
  
La sección [Enumeración de blobs de un contenedor de blobs](#list-the-blobs-in-a-blob-container) muestra cómo enumerar los blobs en un contenedor de blobs.    

## <a name="list-the-blobs-in-a-blob-container"></a>Enumeración de blobs de un contenedor de blobs

Esta sección explica cómo enumerar los blobs de un contenedor de blobs. El código de ejemplo hace referencia al contenedor *test-blob-container* creado en la sección [Creación de un contenedor de blobs](#create-a-blob-container).

> [!NOTE]
> 
> El código de esta sección da por supuesto que ha completado los pasos de la sección [Configuración del entorno de desarrollo](#set-up-the-development-environment). 

1. Abra el archivo `BlobsController.cs` .

1. Agregue un método llamado **ListBlobs** que devuelve un **ActionResult**.

    ```csharp
    public ActionResult ListBlobs()
    {
        // The code in this section goes here.

        return View();
    }
    ```
 
1. Dentro del método **ListBlobs**, obtenga un objeto **CloudStorageAccount** que represente la información de su cuenta de almacenamiento. Utilice el código siguiente para obtener la información de cuenta de almacenamiento y la cadena de conexión de almacenamiento de la configuración del servicio de Azure: (cambie *&lt;storage-account-name>*  por el nombre de la cuenta de Azure Storage a la que está obteniendo acceso.)
   
    ```csharp
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
       CloudConfigurationManager.GetSetting("<storage-account-name>_AzureStorageConnectionString"));
    ```
   
1. Obtenga un objeto **CloudTableClient** que representa un cliente de servicio de blob.
   
    ```csharp
    CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();
    ```

1. Obtenga un objeto **CloudBlobContainer** que representa una referencia al nombre del contenedor de blobs. 
   
    ```csharp
    CloudBlobContainer container = blobClient.GetContainerReference("test-blob-container");
    ```

1. Para enumerar los blobs de un contenedor, use el método **CloudBlobContainer.ListBlobs**. El método **CloudBlobContainer.ListBlobs** devuelve un objeto **IListBlobItem** que se puede convertir en un objeto **CloudBlockBlob**, **CloudPageBlob** o **CloudBlobDirectory**. El fragmento de código siguiente enumera todos los blobs en un contenedor de blobs. Cada blob se convierte en el objeto adecuado según su tipo y su nombre (o URI en el caso de un **CloudBlobDirectory**) se agrega a una lista.

    ```csharp
    List<string> blobs = new List<string>();

    foreach (IListBlobItem item in container.ListBlobs(null, false))
    {
        if (item.GetType() == typeof(CloudBlockBlob))
        {
            CloudBlockBlob blob = (CloudBlockBlob)item;
            blobs.Add(blob.Name);
        }
        else if (item.GetType() == typeof(CloudPageBlob))
        {
            CloudPageBlob blob = (CloudPageBlob)item;
            blobs.Add(blob.Name);
        }
        else if (item.GetType() == typeof(CloudBlobDirectory))
        {
            CloudBlobDirectory dir = (CloudBlobDirectory)item;
            blobs.Add(dir.Uri.ToString());
        }
    }

    return View(blobs);
    ```

    Además de blobs, los contenedores de blobs pueden contener directorios. Supongamos que tiene un contenedor de blobs denominado *test-blob-container* con la siguiente jerarquía:

        foo.png
        dir1/bar.png
        dir2/baz.png

    Mediante el ejemplo de código anterior, la lista de cadenas de **blobs** contiene valores similares al siguiente:

        foo.png
        <storage-account-url>/test-blob-container/dir1
        <storage-account-url>/test-blob-container/dir2

    Como puede ver, la lista incluye solo las entidades de nivel superior, no las anidadas (*bar.png* y *baz.png*). Para enumerar todas las entidades dentro de un contenedor de blobs, debe llamar al método **CloudBlobContainer.ListBlobs** y pasar **true** para el parámetro **useFlatBlobListing**.    

    ```csharp
    ...
    foreach (IListBlobItem item in container.ListBlobs(useFlatBlobListing:true))
    ...
    ```

    Si establece el parámetro **useFlatBlobListing** en **true** se devolverá una lista plana de todas las entidades del contenedor de blobs y se producirán los siguientes resultados:

        foo.png
        dir1/bar.png
        dir2/baz.png

1. En el **Explorador de soluciones**, expanda la carpeta **Vistas**, haga clic con el botón derecho en **Blobs** y, en el menú contextual, seleccione **Agregar > Vista**.

1. En el cuadro de diálogo **Agregar vista**, escriba **ListBlobs** como nombre de vista y seleccione **Agregar**.

1. Abra `ListBlobs.cshtml` y modifíquelo de modo que se parezca al siguiente fragmento de código:

    ```html
    @model List<string>
    @{
        ViewBag.Title = "List blobs";
    }
    
    <h2>List blobs</h2>
    
    <ul>
        @foreach (var item in Model)
        {
        <li>@item</li>
        }
    </ul>
    ```

1. En el **Explorador de soluciones**, expanda la carpeta **Vistas->Compartido** y abra `_Layout.cshtml`.

1. Después del último **Html.ActionLink**, agregue el siguiente **Html.ActionLink**:

    ```html
    <li>@Html.ActionLink("List blobs", "ListBlobs", "Blobs")</li>
    ```

1. Ejecute la aplicación y seleccione **Enumerar blobs** para ver resultados similares a la siguiente captura de pantalla:
  
    ![Lista de blobs](./media/vs-storage-aspnet-getting-started-blobs/listblobs.png)

## <a name="download-blobs"></a>Descargar blobs

La sección siguiente muestra cómo descargar un blob y conservarlo en el almacenamiento local o leer el contenido en una cadena. El código de ejemplo hace referencia al contenedor *test-blob-container* creado en la sección [Creación de un contenedor de blobs](#create-a-blob-container).

1. Abra el archivo `BlobsController.cs` .

1. Agregue un método llamado **DownloadBlob** que devuelve un **ActionResult**.

    ```csharp
    public EmptyResult DownloadBlob()
    {
        // The code in this section goes here.

        return new EmptyResult();
    }
    ```
 
1. Dentro del método **DownloadBlob**, obtenga un objeto **CloudStorageAccount** que represente la información de su cuenta de almacenamiento. Utilice el código siguiente para obtener la información de cuenta de almacenamiento y la cadena de conexión de almacenamiento de la configuración del servicio de Azure: (cambie *&lt;storage-account-name>*  por el nombre de la cuenta de Azure Storage a la que está obteniendo acceso.)
   
    ```csharp
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
       CloudConfigurationManager.GetSetting("<storage-account-name>_AzureStorageConnectionString"));
    ```
   
1. Obtenga un objeto **CloudTableClient** que representa un cliente de servicio de blob.
   
    ```csharp
    CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();
    ```

1. Obtenga un objeto **CloudBlobContainer** que representa una referencia al nombre del contenedor de blobs. 
   
    ```csharp
    CloudBlobContainer container = blobClient.GetContainerReference("test-blob-container");
    ```

1. Obtenga un objeto de referencia de blob mediante una llamada al método **CloudBlobContainer.GetBlockBlobReference** o **CloudBlobContainer.GetPageBlobReference**. (Cambie *&lt;nombre del blob>* al nombre del blob va a descargar).

    ```csharp
    CloudBlockBlob blob = container.GetBlockBlobReference(<blob-name>);
    ```

1. Para descargar un blob, use el método **CloudBlockBlob.DownloadToStream** o **CloudPageBlob.DownloadToStream**, dependiendo del tipo de blob. El siguiente fragmento de código usa el método **CloudBlockBlob.DownloadToStream** para transferir el contenido del blob a un objeto de transmisión que, posteriormente, se puede guardar en un archivo local: (cambie *&lt;local-file-name>* por el nombre completo que representa la ubicación donde desea que se descargue el blob). 

    ```csharp
    using (var fileStream = System.IO.File.OpenWrite(<local-file-name>))
    {
        blob.DownloadToStream(fileStream);
    }
    ```

1. En el **Explorador de soluciones**, expanda la carpeta **Vistas->Compartido** y abra `_Layout.cshtml`.

1. Después del último **Html.ActionLink**, agregue el siguiente **Html.ActionLink**:

    ```html
    <li>@Html.ActionLink("Download blob", "DownloadBlob", "Blobs")</li>
    ```

1. Ejecute la aplicación y seleccione **Descargar blob** para descargar el blob. El blob especificado en la llamada del método **CloudBlobContainer.GetBlockBlobReference** descarga en la ubicación que especifique en la llamada del método **File.OpenWrite**. 

## <a name="delete-blobs"></a>Eliminar blobs

Los siguientes pasos muestran cómo eliminar un blob:

> [!NOTE]
> 
> El código de esta sección da por supuesto que ha completado los pasos de la sección [Configuración del entorno de desarrollo](#set-up-the-development-environment). 

1. Abra el archivo `BlobsController.cs` .

1. Agregue un método llamado **DeleteBlob** que devuelve un **ActionResult**.

    ```csharp
    public EmptyResult DeleteBlob()
    {
        // The code in this section goes here.

        return new EmptyResult();
    }
    ```

1. Obtenga un objeto **CloudStorageAccount** que represente la información de su cuenta de almacenamiento. Utilice el código siguiente para obtener la información de cuenta de almacenamiento y la cadena de conexión de almacenamiento de la configuración del servicio de Azure: (cambie *&lt;storage-account-name>*  por el nombre de la cuenta de Azure Storage a la que está obteniendo acceso.)
   
    ```csharp
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
       CloudConfigurationManager.GetSetting("<storage-account-name>_AzureStorageConnectionString"));
    ```
   
1. Obtenga un objeto **CloudTableClient** que representa un cliente de servicio de blob.
   
    ```csharp
    CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();
    ```

1. Obtenga un objeto **CloudBlobContainer** que representa una referencia al nombre del contenedor de blobs. 
   
    ```csharp
    CloudBlobContainer container = blobClient.GetContainerReference("test-blob-container");
    ```

1. Obtenga un objeto de referencia de blob mediante una llamada al método **CloudBlobContainer.GetBlockBlobReference** o **CloudBlobContainer.GetPageBlobReference**. (Cambie *&lt;nombre del blob>* al nombre del blob va a eliminar).

    ```csharp
    CloudBlockBlob blob = container.GetBlockBlobReference(<blob-name>);
        ```

1. To delete a blob, use the **Delete** method.

    ```csharp
    blob.Delete();
    ```

1. En el **Explorador de soluciones**, expanda la carpeta **Vistas->Compartido** y abra `_Layout.cshtml`.

1. Después del último **Html.ActionLink**, agregue el siguiente **Html.ActionLink**:

    ```html
    <li>@Html.ActionLink("Delete blob", "DeleteBlob", "Blobs")</li>
    ```

1. Ejecute la aplicación y seleccione **Delete blob** para eliminar el blob especificado en la llamada del método **CloudBlobContainer.GetBlockBlobReference**. 

## <a name="next-steps"></a>Pasos siguientes
Consulte más guías de características para obtener información acerca de otras opciones del almacenamiento de datos en Azure.

  * [Introducción a Azure Table Storage y a Servicios conectados de Visual Studio (ASP.NET)](vs-storage-aspnet-getting-started-tables.md)
  * [Introducción a Azure Queue Storage y a Servicios conectados de Visual Studio (ASP.NET)](vs-storage-aspnet-getting-started-queues.md)
