---
title: "Introducción a Azure Blob Storage y Servicios conectados de Visual Studio (ASP.NET Core) | Microsoft Docs"
description: "Cómo empezar a usar Azure Blob Storage en un proyecto de ASP.NET Core en Visual Studio después de conectarse a una cuenta de almacenamiento mediante Servicios conectados de Visual Studio"
services: storage
documentationcenter: 
author: camsoper
manager: wpickett
editor: 
ms.service: storage
ms.workload: web
ms.tgt_pltfrm: vs-getting-started
ms.devlang: na
ms.topic: article
ms.date: 12/07/2017
ms.author: casoper
ms.openlocfilehash: 889afa471eeb556662cddf8eb383a905f08b1f01
ms.sourcegitcommit: a5f16c1e2e0573204581c072cf7d237745ff98dc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/11/2017
---
# <a name="get-started-with-azure-blob-storage-and-visual-studio-connected-services-aspnet-core"></a>Introducción a Azure Blob Storage y Servicios conectados de Visual Studio (ASP.NET Core)

> [!div class="op_single_selector"]
> - [ASP.NET](./vs-storage-aspnet-getting-started-blobs.md)
> - [ASP.NET Core](./vs-storage-aspnet-core-getting-started-blobs.md)

Azure Blob Storage es un servicio que almacena datos no estructurados en la nube como objetos o blobs. El Almacenamiento de blobs puede almacenar cualquier tipo de datos binarios o texto, como un documento, un archivo multimedia o un instalador de aplicación. El Almacenamiento de blobs a veces se conoce como "almacenamiento de objetos".

En este tutorial se muestra cómo escribir código de ASP.NET Core para algunos escenarios comunes mediante Azure Blob Storage. Los escenarios incluyen la creación de un contenedor de blob y la carga, enumeración, descarga y eliminación de blobs.

[!INCLUDE [storage-try-azure-tools-blobs](../../includes/storage-try-azure-tools-blobs.md)]

## <a name="prerequisites"></a>Requisitos previos

* [Microsoft Visual Studio](https://www.visualstudio.com/downloads/)

[!INCLUDE [storage-blob-concepts-include](../../includes/storage-blob-concepts-include.md)]

## <a name="set-up-the-development-environment"></a>Configuración del entorno de desarrollo

Esta sección le lleva por la configuración del entorno de desarrollo, incluida la creación de una aplicación de ASP.NET MVC, la adición de una conexión de Servicios conectados, la adición de un controlador y la especificación de las directivas de espacio de nombres necesarias.

### <a name="create-an-aspnet-mvc-app-project"></a>Creación de un proyecto ASP.NET MVC

1. Abra Visual Studio.

1. Seleccione **Archivo-> Nuevo-> Proyecto** en el menú principal

1. En el cuadro de diálogo **Nuevo proyecto**, especifique las opciones tal y como se resalta en la ilustración siguiente:

    ![Creación de un proyecto de ASP.NET Core](./media/vs-storage-aspnet-core-getting-started-blobs/new-project.png)

1. Seleccione **Aceptar**.

1. En el cuadro de diálogo **Nuevo proyecto ASP.NET**, especifique las opciones tal y como se resalta en la ilustración siguiente:

    ![Especificación de MVC](./media/vs-storage-aspnet-core-getting-started-blobs/new-mvc.png)

1. Seleccione **Aceptar**.

### <a name="use-connected-services-to-connect-to-an-azure-storage-account"></a>Uso de Servicios conectados para conectarse a una cuenta de almacenamiento de Azure

1. En el **Explorador de soluciones**, haga clic con el botón derecho en el proyecto y, en el menú contextual, seleccione **Agregar > Servicio conectado**.

1. En el cuadro de diálogo **Agregar servicio conectado**, seleccione **Almacenamiento en la nube con Azure Storage** y luego seleccione **Configurar**.

    ![Cuadro de diálogo Servicio conectado](./media/vs-storage-aspnet-core-getting-started-blobs/connected-services.png)

1. En el cuadro de diálogo **Azure Storage**, seleccione la cuenta de Azure Storage que se usará en este tutorial.  Para crear una nueva cuenta de Azure Storage, haga clic en **Crear una nueva cuenta de almacenamiento** y rellene el formulario.  Después de seleccionar una cuenta de almacenamiento existente o de crear una nueva, haga clic en **Agregar**.  Visual Studio instalará el paquete de NuGet de Azure Storage y una cadena de conexión de almacenamiento a **appsettings.json**.

> [!TIP]
> Para aprender a crear una cuenta de almacenamiento con [Azure Portal](https://portal.azure.com), consulte [Crear una cuenta de almacenamiento](../storage/common/storage-create-storage-account.md#create-a-storage-account).
>
> También se puede crear una cuenta de almacenamiento de Azure mediante [Azure PowerShell](../storage/common/storage-powershell-guide-full.md), la [CLI de Azure](../storage/common/storage-azure-cli.md) o [Azure Cloud Shell](../cloud-shell/overview.md).


### <a name="create-an-mvc-controller"></a>Creación de un controlador MVC 

1. En el **Explorador de soluciones**, haga clic con el botón derecho en **Controladores** y, en el menú contextual, seleccione **Agregar > Controlador**.

    ![Adición de un controlador a una aplicación de ASP.NET MVC](./media/vs-storage-aspnet-core-getting-started-blobs/add-controller-menu.png)

1. En el cuadro de diálogo **Agregar scaffold**, seleccione **Controlador de MVC: en blanco** y **Agregar**.

    ![Especifique el tipo de controlador MVC](./media/vs-storage-aspnet-core-getting-started-blobs/add-controller.png)

1. En el cuadro de diálogo **Agregar controlador**, denomine *BlobsController* al controlador y seleccione **Agregar**.

    ![Asignación de nombre al controladorMVC](./media/vs-storage-aspnet-core-getting-started-blobs/add-controller-name.png)

1. Agregue las siguientes directivas *using* al archivo `BlobsController.cs`:

    ```csharp
    using System.IO;
    using Microsoft.Extensions.Configuration;
    using Microsoft.WindowsAzure.Storage;
    using Microsoft.WindowsAzure.Storage.Blob;
    ```

## <a name="connect-to-a-storage-account-and-get-a-container-reference"></a>Conexión a una cuenta de almacenamiento y obtención de una referencia de contenedor

Un contenedor de blob es una jerarquía anidada de blobs y carpetas.  El resto de los pasos que se describen en este documento requieren una referencia a un contenedor de blobs, por lo que el código debe colocarse en su propio método para poder reutilizarlo.

Con los pasos siguientes se crea un método para conectar con la cuenta de almacenamiento mediante la cadena de conexión de **appsettings.json** y se crea una referencia a un contenedor.  El valor de configuración de la cadena de conexión de **appSettings.json** se nombrará con el formato `<storageaccountname>_AzureStorageConnectionString`. 

1. Abra el archivo `BlobsController.cs` .

1. Agregue un método llamado **GetCloudBlobContainer** que devuelve un elemento **CloudBlobContainer**.  No olvide reemplazar `<storageaccountname>_AzureStorageConnectionString` por el nombre real de la clave en **Web.config**.
    
    ```csharp
    private CloudBlobContainer GetCloudBlobContainer()
    {
        var builder = new ConfigurationBuilder()
            .SetBasePath(Directory.GetCurrentDirectory())
            .AddJsonFile("appsettings.json");
        IConfigurationRoot Configuration = builder.Build();
        CloudStorageAccount storageAccount = 
            CloudStorageAccount.Parse(Configuration["ConnectionStrings:aspnettutorial_AzureStorageConnectionString"]);
        CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();
        CloudBlobContainer container = blobClient.GetContainerReference("test-blob-container");
        return container;
    }
    ```

> [!NOTE]
> Aunque *test-blob-container* no existe aún, este código crea una referencia a él, por lo que el contenedor se puede crear con el método `CreateIfNotExists` que se muestra en el siguiente paso.

## <a name="create-a-blob-container"></a>Creación de un contenedor de blobs

Los siguientes pasos muestran cómo crear un contenedor de blobs:

1. Agregue un método llamado `CreateBlobContainer` que devuelve un elemento `ActionResult`.

    ```csharp
    public ActionResult CreateBlobContainer()
    {
        // The code in this section goes here.

        return View();
    }
    ```
 
1. Obtenga un objeto `CloudBlobContainer` que representa una referencia al nombre del contenedor de blobs preferido. 
   
    ```csharp
    CloudBlobContainer container = GetCloudBlobContainer();
    ```

1. Llame al método `CloudBlobContainer.CreateIfNotExists` para crear el contenedor si aún no existe. El método `CloudBlobContainer.CreateIfNotExists` devuelve **true** si el contenedor no existe y se crea correctamente. En caso contrario, se devuelve **false**.    

    ```csharp
    ViewBag.Success = container.CreateIfNotExistsAsync().Result;
    ```

1. Actualice `ViewBag` con el nombre del contenedor de blobs.

    ```csharp
    ViewBag.BlobContainerName = container.Name;
    ```
    
    A continuación, se muestra el método `CreateBlobContainer` completado:

    ```csharp
    public ActionResult CreateBlobContainer()
    {
        CloudBlobContainer container = GetCloudBlobContainer();
        ViewBag.Success = container.CreateIfNotExistsAsync().Result;
        ViewBag.BlobContainerName = container.Name;

        return View();
    }
    ```

1. En el **Explorador de soluciones**, haga clic con el botón derecho en la carpeta **Vistas** y, en el menú contextual, seleccione **Agregar->Nueva carpeta**. Asigne a la nueva carpeta el nombre *Blobs*. 

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

1. Busque la lista sin ordenar que se parece a esta: `<ul class="nav navbar-nav">`.  Después del último elemento `<li>` de la lista, agregue el siguiente código HTML para agregar otro elemento de menú de navegación:

    ```html
    <li><a asp-area="" asp-controller="Blobs" asp-action="CreateBlobContainer">Create blob container</a></li>
    ```

1. Ejecute la aplicación y seleccione **Crear contenedor de blobs** para ver resultados similares a la siguiente captura de pantalla:
  
    ![Creación de un contenedor de blobs](./media/vs-storage-aspnet-core-getting-started-blobs/create-blob-container-results.png)

    Como se mencionó anteriormente, el método `CloudBlobContainer.CreateIfNotExists` devuelve **true** solo cuando el contenedor no existe y se crea. Por lo tanto, si la aplicación se ejecuta cuando el contenedor existe, el método devuelve **false**.

## <a name="upload-a-blob-into-a-blob-container"></a>Carga de un blob en un contenedor de blobs

Después de [crear el contenedor de blobs](#create-a-blob-container), cargue los archivos en ese contenedor. En esta sección se explica cómo cargar un archivo local en un contenedor de blobs. En estos pasos se supone que hay un contenedor de blobs denominado *test-blob-container*. 

1. Abra el archivo `BlobsController.cs` .

1. Agregue un método llamado `UploadBlob` que devuelve una cadena.

    ```csharp
    public string UploadBlob()
    {
        // The code in this section goes here.

        return "success!";
    }
    ```
 
1. Dentro del método `UploadBlob`, obtenga un objeto `CloudBlobContainer` que representa una referencia al nombre del contenedor de blobs deseado. 
   
    ```csharp
    CloudBlobContainer container = GetCloudBlobContainer();
    ```

1. Como se explicó anteriormente, el almacenamiento de Azure es compatible con diferentes tipos de blob. En este tutorial se usan blobs en bloques.  Para recuperar una referencia a un blob en bloques, llame al método `CloudBlobContainer.GetBlockBlobReference`.

    ```csharp
    CloudBlockBlob blob = container.GetBlockBlobReference("myBlob");
    ```
    
    > [!NOTE]
    > El nombre del blob es parte de la dirección URL utilizada para recuperar un blob y puede ser cualquier cadena, incluido el nombre del archivo.

1. Una vez que haya una referencia de blob, cargue en ella cualquier flujo de datos mediante una llamada al método `UploadFromStream` del objeto de referencia. El método `UploadFromStream` crea el blob si no existe o lo sobrescribe si existe. (Cambie *&lt;file-to-upload>* por una ruta de acceso completa al archivo que se va a cargar).

    ```csharp
    using (var fileStream = System.IO.File.OpenRead(@"<file-to-upload>"))
    {
        blob.UploadFromStreamAsync(fileStream).Wait();
    }
    ```
    
    A continuación, se muestra el método `UploadBlob` completado (con una ruta de acceso completa para el archivo que se va a cargar):

    ```csharp
    public string UploadBlob()
    {
        CloudBlobContainer container = GetCloudBlobContainer();
        CloudBlockBlob blob = container.GetBlockBlobReference("myBlob");
        using (var fileStream = System.IO.File.OpenRead(@"c:\src\sample.txt"))
        {
            blob.UploadFromStreamAsync(fileStream).Wait();
        }
        return "success!";
    }
    ```

1. En el **Explorador de soluciones**, expanda la carpeta **Vistas->Compartido** y abra `_Layout.cshtml`.

1. Después del último elemento `<li>` de la lista, agregue el siguiente código HTML para agregar otro elemento de menú de navegación:

    ```html
    <li><a asp-area="" asp-controller="Blobs" asp-action="UploadBlob">Upload blob</a></li>
    ```

1. Ejecute la aplicación y seleccione **Cargar blob**.  Se debería mostrar la palabra "success!" .
    
    ![Comprobación correcta](./media/vs-storage-aspnet-core-getting-started-blobs/upload-blob.png)
  
La sección [Enumeración de blobs de un contenedor de blobs](#list-the-blobs-in-a-blob-container) muestra cómo enumerar los blobs en un contenedor de blobs.    

## <a name="list-the-blobs-in-a-blob-container"></a>Enumeración de blobs de un contenedor de blobs

Esta sección explica cómo enumerar los blobs de un contenedor de blobs. El código de ejemplo hace referencia al contenedor *test-blob-container* creado en la sección [Creación de un contenedor de blobs](#create-a-blob-container).

1. Abra el archivo `BlobsController.cs` .

1. Agregue un método llamado `ListBlobs` que devuelve un elemento `ActionResult`.

    ```csharp
    public ActionResult ListBlobs()
    {
        // The code in this section goes here.

    }
    ```
 
1. Dentro del método `ListBlobs`, obtenga un objeto `CloudBlobContainer` que representa una referencia al contenedor de blobs. 
   
    ```csharp
    CloudBlobContainer container = GetCloudBlobContainer();
    ```
   
1. Para enumerar los blobs de un contenedor de blobs, use el método `CloudBlobContainer.ListBlobsSegmentedAsync`. El método `CloudBlobContainer.ListBlobsSegmentedAsync` devuelve un elemento `BlobResultSegment` que contiene objetos `IListBlobItem` que se pueden transmitir a objetos `CloudBlockBlob`, `CloudPageBlob` o `CloudBlobDirectory`. El fragmento de código siguiente enumera todos los blobs en un contenedor de blobs. Cada blob se transmite al objeto adecuado en función de su tipo, y su nombre (o el URI en caso de un elemento `CloudBlobDirectory`) se agrega a una lista.

    ```csharp
    List<string> blobs = new List<string>();
    BlobResultSegment resultSegment = container.ListBlobsSegmentedAsync(null).Result;
    foreach (IListBlobItem item in resultSegment.Results)
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
    A continuación, se muestra el método `ListBlobs` completado:

    ```csharp
    public ActionResult ListBlobs()
    {
        CloudBlobContainer container = GetCloudBlobContainer();
        List<string> blobs = new List<string>();
        BlobResultSegment resultSegment = container.ListBlobsSegmentedAsync(null).Result;
        foreach (IListBlobItem item in resultSegment.Results)
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
    }
    ```

1. En el **Explorador de soluciones**, expanda la carpeta **Vistas**, haga clic con el botón derecho en **Blobs** y, en el menú contextual, seleccione **Agregar > Vista**.

1. En el cuadro de diálogo **Agregar vista**, escriba `ListBlobs` como nombre de vista y seleccione **Agregar**.

1. Abra `ListBlobs.cshtml` y reemplace el contenido por el código siguiente:

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

1. Después del último elemento `<li>` de la lista, agregue el siguiente código HTML para agregar otro elemento de menú de navegación:

    ```html
    <li><a asp-area="" asp-controller="Blobs" asp-action="ListBlobs">List blobs</a></li>
    ```

1. Ejecute la aplicación y seleccione **Enumerar blobs** para ver resultados similares a la siguiente captura de pantalla:
  
    ![Lista de blobs](./media/vs-storage-aspnet-core-getting-started-blobs/listblobs.png)

## <a name="download-blobs"></a>Descargar blobs

La sección siguiente muestra cómo descargar un blob y conservarlo en el almacenamiento local o leer el contenido en una cadena. El código de ejemplo hace referencia al contenedor *test-blob-container* creado en la sección [Creación de un contenedor de blobs](#create-a-blob-container).

1. Abra el archivo `BlobsController.cs` .

1. Agregue un método llamado `DownloadBlob` que devuelve una cadena.

    ```csharp
    public string DownloadBlob()
    {
        // The code in this section goes here.

        return "success!";
    }
    ```
 
1. Dentro del método `DownloadBlob`, obtenga un objeto `CloudBlobContainer` que representa una referencia al contenedor de blobs.
   
    ```csharp
    CloudBlobContainer container = GetCloudBlobContainer();
    ```

1. Obtenga un objeto de referencia de blob mediante la llamada al método `CloudBlobContainer.GetBlockBlobReference`. 

    ```csharp
    CloudBlockBlob blob = container.GetBlockBlobReference("myBlob");
    ```

1. Para descargar un blob, use el método `CloudBlockBlob.DownloadToStream`. El código siguiente transfiere el contenido de un blob a un objeto de flujo que luego se guarda en un archivo local (cambie *&lt;local-file-name>* por el nombre de archivo completo que representa dónde se descarga el blob): 

    ```csharp
    using (var fileStream = System.IO.File.OpenWrite(<local-file-name>))
    {
        blob.DownloadToStreamAsync(fileStream).Wait();
    }
    ```
    
    A continuación, se muestra el método `ListBlobs` completado (con una ruta de acceso completa al archivo local que se crea):
    
    ```csharp
    public string DownloadBlob()
    {
        CloudBlobContainer container = GetCloudBlobContainer();
        CloudBlockBlob blob = container.GetBlockBlobReference("myBlob");
        using (var fileStream = System.IO.File.OpenWrite(@"c:\src\downloadedBlob.txt"))
        {
            blob.DownloadToStreamAsync(fileStream).Wait();
        }
        return "success!";
    }
    ```

1. En el **Explorador de soluciones**, expanda la carpeta **Vistas->Compartido** y abra `_Layout.cshtml`.

1. Después del último elemento `<li>` de la lista, agregue el siguiente código HTML para agregar otro elemento de menú de navegación:

    ```html
    <li><a asp-area="" asp-controller="Blobs" asp-action="DownloadBlob">Download blob</a></li>
    ```

1. Ejecute la aplicación y seleccione **Descargar blob** para descargar el blob. El blob especificado en la llamada al método `CloudBlobContainer.GetBlockBlobReference` se descarga en la ubicación especificada en la llamada al método `File.OpenWrite`.  El texto "success!" debería mostrarse en el explorador. 

## <a name="delete-blobs"></a>Eliminar blobs

Los siguientes pasos muestran cómo eliminar un blob:

1. Abra el archivo `BlobsController.cs` .

1. Agregue un método llamado `DeleteBlob` que devuelve una cadena.

    ```csharp
    public string DeleteBlob()
    {
        // The code in this section goes here.

        return "success!";
    }
    ```

1. Dentro del método `DeleteBlob`, obtenga un objeto `CloudBlobContainer` que representa una referencia al contenedor de blobs.
   
    ```csharp
    CloudBlobContainer container = GetCloudBlobContainer();
    ```

1. Obtenga un objeto de referencia de blob mediante la llamada al método `CloudBlobContainer.GetBlockBlobReference`. 

    ```csharp
    CloudBlockBlob blob = container.GetBlockBlobReference("myBlob");
    ```

1. Para eliminar un blob, use el método `Delete`.

    ```csharp
    blob.DeleteAsync().Wait();
    ```
    
    El método `DeleteBlob` completado debe aparecer de la manera siguiente:
    
    ```csharp
    public string DeleteBlob()
    {
        CloudBlobContainer container = GetCloudBlobContainer();
        CloudBlockBlob blob = container.GetBlockBlobReference("myBlob");
        blob.DeleteAsync().Wait();
        return "success!";
    }
    ```

1. En el **Explorador de soluciones**, expanda la carpeta **Vistas->Compartido** y abra `_Layout.cshtml`.

1. Después del último elemento `<li>` de la lista, agregue el siguiente código HTML para agregar otro elemento de menú de navegación:

    ```html
    <li><a asp-area="" asp-controller="Blobs" asp-action="DeleteBlob">Delete blob</a></li>
    ```

1. Ejecute la aplicación y seleccione **Eliminar blob** para eliminar el blob especificado en la llamada al método `CloudBlobContainer.GetBlockBlobReference`.  El texto "success!" debería aparecer en el explorador.  Haga clic en el botón **Atrás** del explorador y seleccione **Mostrar blobs** para comprobar que el blob ya no está en el contenedor.

## <a name="next-steps"></a>Pasos siguientes

En este tutorial, aprendió a almacenar, enumerar y recuperar blobs en Azure Storage mediante ASP.NET Core.  Consulte más guías de características para obtener información acerca de otras opciones del almacenamiento de datos en Azure.

  * [Introducción a Azure Table Storage y a Servicios conectados de Visual Studio (ASP.NET)](vs-storage-aspnet-getting-started-tables.md)
  * [Introducción a Azure Queue Storage y a Servicios conectados de Visual Studio (ASP.NET)](vs-storage-aspnet-getting-started-queues.md)
