---
title: "Introducción a Azure Queue Storage y a Servicios conectados de Visual Studio (ASP.NET) | Microsoft Docs"
description: "Cómo empezar a usar Azure Queue Storage en un proyecto ASP.NET en Visual Studio después de conectarse a una cuenta de almacenamiento mediante Servicios conectados de Visual Studio"
services: storage
documentationcenter: 
author: kraigb
manager: ghogen
editor: 
ms.assetid: 94ca3413-5497-433f-abbe-836f83a9de72
ms.service: storage
ms.workload: web
ms.tgt_pltfrm: vs-getting-started
ms.devlang: na
ms.topic: article
ms.date: 12/23/2016
ms.author: kraigb
ms.openlocfilehash: 4687e5dfce72583728068c176d86d100313badf6
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="get-started-with-azure-queue-storage-and-visual-studio-connected-services-aspnet"></a>Introducción a Azure Queue Storage y a Servicios conectados de Visual Studio (ASP.NET)
[!INCLUDE [storage-try-azure-tools-queues](../../includes/storage-try-azure-tools-queues.md)]

## <a name="overview"></a>Información general

Azure Queue Storage proporciona mensajería en la nube entre componentes de aplicaciones. A la hora de diseñar aplicaciones para escala, los componentes de las mismas suelen desacoplarse para poder escalarlos de forma independiente. El almacenamiento en cola ofrece mensajería asincrónica para la comunicación entre los componentes de las aplicaciones, independientemente de si se ejecutan en la nube, en el escritorio, en un servidor local o en un dispositivo móvil. Además, este tipo de almacenamiento admite la administración de tareas asincrónicas y la creación de flujos de trabajo de procesos.

Este tutorial muestra cómo escribir código ASP.NET para algunos escenarios comunes mediante entidades de Azure Queue Storage. Estos escenarios incluyen tareas comunes como crear una cola de Azure y agregar, modificar, leer y eliminar mensajes de la cola.

##<a name="prerequisites"></a>Requisitos previos

* [Microsoft Visual Studio](https://www.visualstudio.com/downloads/)
* [Cuenta de Azure Storage](../storage/common/storage-create-storage-account.md#create-a-storage-account)

[!INCLUDE [storage-queue-concepts-include](../../includes/storage-queue-concepts-include.md)]

[!INCLUDE [storage-create-account-include](../../includes/vs-storage-aspnet-getting-started-create-azure-account.md)]

[!INCLUDE [storage-development-environment-include](../../includes/vs-storage-aspnet-getting-started-setup-dev-env.md)]

### <a name="create-an-mvc-controller"></a>Creación de un controlador MVC 

1. En el **Explorador de soluciones**, haga clic con el botón derecho en **Controladores** y, en el menú contextual, seleccione **Agregar > Controlador**.

    ![Incorporación de un controlador a una aplicación de ASP.NET MVC](./media/vs-storage-aspnet-getting-started-queues/add-controller-menu.png)

1. En el cuadro de diálogo **Agregar scaffold**, seleccione **Controlador MVC 5 - Vacío** y seleccione **Agregar**.

    ![Especifique el tipo de controlador MVC](./media/vs-storage-aspnet-getting-started-queues/add-controller.png)

1. En el cuadro de diálogo **Agregar controlador**, denomine *QueuesController* al controlador y seleccione **Agregar**.

    ![Asignación de nombre al controladorMVC](./media/vs-storage-aspnet-getting-started-queues/add-controller-name.png)

1. Agregue las siguientes directivas *using* al archivo `QueuesController.cs`:

    ```csharp
    using Microsoft.Azure;
    using Microsoft.WindowsAzure.Storage;
    using Microsoft.WindowsAzure.Storage.Auth;
    using Microsoft.WindowsAzure.Storage.Queue;
    ```
## <a name="create-a-queue"></a>Creación de una cola

Los siguientes pasos muestran cómo crear una cola:

> [!NOTE]
> 
> En esta sección se da por supuesto que ha completado los pasos de [Configuración del entorno de desarrollo](#set-up-the-development-environment). 

1. Abra el archivo `QueuesController.cs` . 

1. Agregue un método llamado **CreateQueue** que devuelve un **ActionResult**.

    ```csharp
    public ActionResult CreateQueue()
    {
        // The code in this section goes here.

        return View();
    }
    ```

1. Dentro del método **CreateQueue**, obtenga un objeto **CloudStorageAccount** que represente la información de su cuenta de almacenamiento. Utilice el código siguiente para obtener la información de cuenta de almacenamiento y la cadena de conexión de almacenamiento de la configuración del servicio de Azure: (cambie *&lt;storage-account-name>*  por el nombre de la cuenta de Azure Storage a la que está obteniendo acceso.)
   
    ```csharp
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
       CloudConfigurationManager.GetSetting("<storage-account-name>_AzureStorageConnectionString"));
    ```

1. Obtenga un objeto **CloudQueueClient** que representa un cliente de servicio de cola.
   
    ```csharp
    CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();
    ```
1. Obtenga un objeto **CloudQueue** que representa una referencia al nombre de cola que desea. El método **CloudQueueClient.GetQueueReference** no hace ninguna solicitud en el almacenamiento de la cola. Se devuelve la referencia tanto si la cola existe como si no. 
   
    ```csharp
    CloudQueue queue = queueClient.GetQueueReference("test-queue");
    ```

1. Llame a la **CloudQueue.CreateIfNotExists** método para crear la cola si aún no existe. El método **CloudQueue.CreateIfNotExists** devuelve **true** si la cola no existe y se ha creado correctamente. En caso contrario, se devuelve **false**.    

    ```csharp
    ViewBag.Success = queue.CreateIfNotExists();
    ```

1. Actualice **ViewBag** con el nombre de la cola.

    ```csharp
    ViewBag.QueueName = queue.Name;
    ```

1. En el **Explorador de soluciones**, expanda la carpeta **Vistas**, haga clic con el botón derecho en **Colas** y, en el menú contextual, seleccione **Agregar > Vista**.

1. En el cuadro de diálogo **Agregar vista**, escriba **CreateQueue** como nombre de vista y seleccione **Agregar**.

1. Abra `CreateQueue.cshtml` y modifíquelo de modo que se parezca al siguiente fragmento de código:

    ```csharp
    @{
        ViewBag.Title = "Create Queue";
    }
    
    <h2>Create Queue results</h2>

    Creation of @ViewBag.QueueName @(ViewBag.Success == true ? "succeeded" : "failed")
    ```

1. En el **Explorador de soluciones**, expanda la carpeta **Vistas->Compartido** y abra `_Layout.cshtml`.

1. Después del último **Html.ActionLink**, agregue el siguiente **Html.ActionLink**:

    ```html
    <li>@Html.ActionLink("Create queue", "CreateQueue", "Queues")</li>
    ```

1. Ejecute la aplicación y seleccione **Crear cola** para ver resultados similares a la siguiente captura de pantalla:
  
    ![Crear cola](./media/vs-storage-aspnet-getting-started-queues/create-queue-results.png)

    Como se dijo anteriormente, el método **CloudQueue.CreateIfNotExists** devuelve **true** solo si la cola no existía pero se creó. Por lo tanto, si se ejecuta la aplicación cuando la cola existe, el método devuelve **false**. Para ejecutar la aplicación varias veces, debe eliminar la cola antes de ejecutar la aplicación de nuevo. La eliminación de la cola puede realizarse a través del método **CloudQueue.Delete**. También puede eliminar la cola mediante [Azure Portal](http://go.microsoft.com/fwlink/p/?LinkID=525040) o [el Explorador de Microsoft Azure Storage](../vs-azure-tools-storage-manage-with-storage-explorer.md).  

## <a name="add-a-message-to-a-queue"></a>un mensaje a una cola

Una vez que se haya [creado una cola](#create-a-queue), puede agregar mensajes a dicha cola. En esta sección se explica cómo agregar un mensaje a una cola *test-queue*. 

> [!NOTE]
> 
> Se da por supuesto que ha completado los pasos de [Configuración del entorno de desarrollo](#set-up-the-development-environment). 

1. Abra el archivo `QueuesController.cs` .

1. Agregue un método llamado **AddMessage** que devuelve un **ActionResult**.

    ```csharp
    public ActionResult AddMessage()
    {
        // The code in this section goes here.

        return View();
    }
    ```
 
1. Dentro del método **AddMessage**, obtenga un objeto **CloudStorageAccount** que represente la información de su cuenta de almacenamiento. Utilice el código siguiente para obtener la información de cuenta de almacenamiento y la cadena de conexión de almacenamiento de la configuración del servicio de Azure: (cambie *&lt;storage-account-name>*  por el nombre de la cuenta de Azure Storage a la que está obteniendo acceso.)
   
    ```csharp
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
       CloudConfigurationManager.GetSetting("<storage-account-name>_AzureStorageConnectionString"));
    ```
   
1. Obtenga un objeto **CloudQueueClient** que representa un cliente de servicio de cola.
   
    ```csharp
    CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();
    ```

1. Obtenga un objeto **CloudQueueContainer** que representa una referencia a la cola. 
   
    ```csharp
    CloudQueue queue = queueClient.GetQueueReference("test-queue");
    ```

1. Cree el objeto **CloudQueueMessage** que representa el mensaje que desea agregar a la cola. Se puede crear un objeto **CloudQueueMessage** a partir de una cadena (en formato UTF-8) o de una matriz de bytes.

    ```csharp
    CloudQueueMessage message = new CloudQueueMessage("Hello, Azure Queue Storage");
    ```

1. Llame al método **CloudQueue.AddMessage** para agregar el mensaje a la cola.

    ```csharp
    queue.AddMessage(message);
    ```

1. Cree y configure un par de propiedades **ViewBag** para su presentación en la vista.

    ```csharp
    ViewBag.QueueName = queue.Name;
    ViewBag.Message = message.AsString;
    ```

1. En el **Explorador de soluciones**, expanda la carpeta **Vistas**, haga clic con el botón derecho en **Colas** y, en el menú contextual, seleccione **Agregar > Vista**.

1. En el cuadro de diálogo **Agregar vista**, escriba **AddMessage** como nombre de vista y seleccione **Agregar**.

1. Abra `AddMessage.cshtml` y modifíquelo de modo que se parezca al siguiente fragmento de código:

    ```csharp
    @{
        ViewBag.Title = "Add Message";
    }
    
    <h2>Add Message results</h2>
    
    The message '@ViewBag.Message' was added to the queue '@ViewBag.QueueName'.
    ```

1. En el **Explorador de soluciones**, expanda la carpeta **Vistas->Compartido** y abra `_Layout.cshtml`.

1. Después del último **Html.ActionLink**, agregue el siguiente **Html.ActionLink**:

    ```html
    <li>@Html.ActionLink("Add message", "AddMessage", "Queues")</li>
    ```

1. Ejecute la aplicación y seleccione **Agregar mensaje** para ver resultados similares a la siguiente captura de pantalla:
  
    ![Agregar mensaje](./media/vs-storage-aspnet-getting-started-queues/add-message-results.png)

Las dos secciones —[Lectura de un mensaje de una cola sin eliminarlo](#read-a-message-from-a-queue-without-removing-it) y [Lectura y eliminación de un mensaje de una cola](#read-and-remove-a-message-from-a-queue)— muestran cómo leer los mensajes de una cola.    

## <a name="read-a-message-from-a-queue-without-removing-it"></a>Lectura de un mensaje de una cola sin eliminarlo

En esta sección se muestra cómo inspeccionar un mensaje en cola (leer el primer mensaje sin eliminarlo).  

> [!NOTE]
> 
> Se da por supuesto que ha completado los pasos de [Configuración del entorno de desarrollo](#set-up-the-development-environment). 

1. Abra el archivo `QueuesController.cs` .

1. Agregue un método llamado **PeekMessage** que devuelve un **ActionResult**.

    ```csharp
    public ActionResult PeekMessage()
    {
        // The code in this section goes here.

        return View();
    }
    ```
 
1. Dentro del método **PeekMessage**, obtenga un objeto **CloudStorageAccount** que represente la información de su cuenta de almacenamiento. Utilice el código siguiente para obtener la información de cuenta de almacenamiento y la cadena de conexión de almacenamiento de la configuración del servicio de Azure: (cambie *&lt;storage-account-name>*  por el nombre de la cuenta de Azure Storage a la que está obteniendo acceso.)
   
    ```csharp
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
       CloudConfigurationManager.GetSetting("<storage-account-name>_AzureStorageConnectionString"));
    ```
   
1. Obtenga un objeto **CloudQueueClient** que representa un cliente de servicio de cola.
   
    ```csharp
    CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();
    ```

1. Obtenga un objeto **CloudQueueContainer** que representa una referencia a la cola. 
   
    ```csharp
    CloudQueue queue = queueClient.GetQueueReference("test-queue");
    ```

1. Llame al método **CloudQueue.PeekMessage** para leer el primer mensaje de la cola sin quitarlo de ella. 

    ```csharp
    CloudQueueMessage message = queue.PeekMessage();
    ```

1. Actualice **ViewBag** con dos valores: el nombre de la cola y el mensaje que se ha leído. El objeto **CloudQueueMessage** expone dos propiedades para obtener el valor del objeto: **CloudQueueMessage.AsBytes** y **CloudQueueMessage.AsString**. **AsString** (que se usa en este ejemplo) devuelve una cadena, mientras que **AsBytes** devuelve una matriz de bytes.

    ```csharp
    ViewBag.QueueName = queue.Name; 
    ViewBag.Message = (message != null ? message.AsString : "");
    ```

1. En el **Explorador de soluciones**, expanda la carpeta **Vistas**, haga clic con el botón derecho en **Colas** y, en el menú contextual, seleccione **Agregar > Vista**.

1. En el cuadro de diálogo **Agregar vista**, escriba **PeekMessage** como nombre de vista y seleccione **Agregar**.

1. Abra `PeekMessage.cshtml` y modifíquelo de modo que se parezca al siguiente fragmento de código:

    ```csharp
    @{
        ViewBag.Title = "PeekMessage";
    }
    
    <h2>Peek Message results</h2>
    
    <table border="1">
        <tr><th>Queue</th><th>Peeked Message</th></tr>
        <tr><td>@ViewBag.QueueName</td><td>@ViewBag.Message</td></tr>
    </table>    
    ```

1. En el **Explorador de soluciones**, expanda la carpeta **Vistas->Compartido** y abra `_Layout.cshtml`.

1. Después del último **Html.ActionLink**, agregue el siguiente **Html.ActionLink**:

    ```html
    <li>@Html.ActionLink("Peek message", "PeekMessage", "Queues")</li>
    ```

1. Ejecute la aplicación y seleccione **Inspeccionar mensaje** para ver resultados similares a la siguiente captura de pantalla:
  
    ![Inspeccionar mensaje](./media/vs-storage-aspnet-getting-started-queues/peek-message-results.png)

## <a name="read-and-remove-a-message-from-a-queue"></a>Lectura y eliminación de un mensaje de una cola

En esta sección, aprenderá a leer y eliminar un mensaje de una cola.   

> [!NOTE]
> 
> Se da por supuesto que ha completado los pasos de [Configuración del entorno de desarrollo](#set-up-the-development-environment). 

1. Abra el archivo `QueuesController.cs` .

1. Agregue un método llamado **ReadMessage** que devuelve un **ActionResult**.

    ```csharp
    public ActionResult ReadMessage()
    {
        // The code in this section goes here.

        return View();
    }
    ```
 
1. Dentro del método **ReadMessage**, obtenga un objeto **CloudStorageAccount** que represente la información de su cuenta de almacenamiento. Utilice el código siguiente para obtener la información de cuenta de almacenamiento y la cadena de conexión de almacenamiento de la configuración del servicio de Azure: (cambie *&lt;storage-account-name>*  por el nombre de la cuenta de Azure Storage a la que está obteniendo acceso.)
   
    ```csharp
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
       CloudConfigurationManager.GetSetting("<storage-account-name>_AzureStorageConnectionString"));
    ```
   
1. Obtenga un objeto **CloudQueueClient** que representa un cliente de servicio de cola.
   
    ```csharp
    CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();
    ```

1. Obtenga un objeto **CloudQueueContainer** que representa una referencia a la cola. 
   
    ```csharp
    CloudQueue queue = queueClient.GetQueueReference("test-queue");
    ```

1. Llame al método **CloudQueue.GetMessage** para leer el primer mensaje de la cola. El método **CloudQueue.GetMessage** hace que el mensaje sea invisible durante 30 segundos (de forma predeterminada) para cualquier otro código que lea mensajes de forma que ningún otro código pueda modificar o eliminar el mensaje mientras lo procesa. Para cambiar la cantidad de tiempo que el mensaje es invisible, modifique el parámetro **visibilityTimeout** que se pasa al método **CloudQueue.GetMessage**.

    ```csharp
    // This message will be invisible to other code for 30 seconds.
    CloudQueueMessage message = queue.GetMessage();     
    ```

1. Llame al método **CloudQueueMessage.Delete** para eliminar el mensaje de la cola.

    ```csharp
    queue.DeleteMessage(message);
    ```

1. Actualice **ViewBag** con el mensaje eliminado y el nombre de la cola.

    ```csharp
    ViewBag.QueueName = queue.Name;
    ViewBag.Message = message.AsString;
    ```
 
1. En el **Explorador de soluciones**, expanda la carpeta **Vistas**, haga clic con el botón derecho en **Colas** y, en el menú contextual, seleccione **Agregar > Vista**.

1. En el cuadro de diálogo **Agregar vista**, escriba **ReadMessage** como nombre de vista y seleccione **Agregar**.

1. Abra `ReadMessage.cshtml` y modifíquelo de modo que se parezca al siguiente fragmento de código:

    ```csharp
    @{
        ViewBag.Title = "ReadMessage";
    }
    
    <h2>Read Message results</h2>
    
    <table border="1">
        <tr><th>Queue</th><th>Read (and Deleted) Message</th></tr>
        <tr><td>@ViewBag.QueueName</td><td>@ViewBag.Message</td></tr>
    </table>
    ```

1. En el **Explorador de soluciones**, expanda la carpeta **Vistas->Compartido** y abra `_Layout.cshtml`.

1. Después del último **Html.ActionLink**, agregue el siguiente **Html.ActionLink**:

    ```html
    <li>@Html.ActionLink("Read/Delete message", "ReadMessage", "Queues")</li>
    ```

1. Ejecute la aplicación y seleccione **Read/Delete message** (Leer/eliminar mensaje) para ver resultados similares a la siguiente captura de pantalla:
  
    ![Leer y eliminar mensaje](./media/vs-storage-aspnet-getting-started-queues/read-message-results.png)

## <a name="get-the-queue-length"></a>la longitud de la cola

En esta sección se muestra cómo obtener la longitud de cola (número de mensajes). 

> [!NOTE]
> 
> Se da por supuesto que ha completado los pasos de [Configuración del entorno de desarrollo](#set-up-the-development-environment). 

1. Abra el archivo `QueuesController.cs` .

1. Agregue un método llamado **GetQueueLength** que devuelve un **ActionResult**.

    ```csharp
    public ActionResult GetQueueLength()
    {
        // The code in this section goes here.

        return View();
    }
    ```
 
1. Dentro del método **ReadMessage**, obtenga un objeto **CloudStorageAccount** que represente la información de su cuenta de almacenamiento. Utilice el código siguiente para obtener la información de cuenta de almacenamiento y la cadena de conexión de almacenamiento de la configuración del servicio de Azure: (cambie *&lt;storage-account-name>*  por el nombre de la cuenta de Azure Storage a la que está obteniendo acceso.)
   
    ```csharp
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
       CloudConfigurationManager.GetSetting("<storage-account-name>_AzureStorageConnectionString"));
    ```
   
1. Obtenga un objeto **CloudQueueClient** que representa un cliente de servicio de cola.
   
    ```csharp
    CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();
    ```

1. Obtenga un objeto **CloudQueueContainer** que representa una referencia a la cola. 
   
    ```csharp
    CloudQueue queue = queueClient.GetQueueReference("test-queue");
    ```

1. Llame al método **CloudQueue.FetchAttributes** para recuperar los atributos de la cola (incluida su longitud). 

    ```csharp
    queue.FetchAttributes();
    ```

6. Acceda a la propiedad **CloudQueue.ApproximateMessageCount** para obtener la longitud de la cola.
 
    ```csharp
    int? nMessages = queue.ApproximateMessageCount;
    ```

1. Actualice **ViewBag** con el nombre de la cola y su longitud.

    ```csharp
    ViewBag.QueueName = queue.Name;
    ViewBag.Length = nMessages;
    ```
 
1. En el **Explorador de soluciones**, expanda la carpeta **Vistas**, haga clic con el botón derecho en **Colas** y, en el menú contextual, seleccione **Agregar > Vista**.

1. En el cuadro de diálogo **Agregar vista**, escriba **GetQueueLength** como nombre de vista y seleccione **Agregar**.

1. Abra `GetQueueLengthMessage.cshtml` y modifíquelo de modo que se parezca al siguiente fragmento de código:

    ```csharp
    @{
        ViewBag.Title = "GetQueueLength";
    }
    
    <h2>Get Queue Length results</h2>
    
    The queue '@ViewBag.QueueName' has a length of (number of messages): @ViewBag.Length
    ```

1. En el **Explorador de soluciones**, expanda la carpeta **Vistas->Compartido** y abra `_Layout.cshtml`.

1. Después del último **Html.ActionLink**, agregue el siguiente **Html.ActionLink**:

    ```html
    <li>@Html.ActionLink("Get queue length", "GetQueueLength", "Queues")</li>
    ```

1. Ejecute la aplicación y seleccione **Get queue length** (Obtener la longitud de cola) para ver resultados similares a la siguiente captura de pantalla:
  
    ![longitud de la cola](./media/vs-storage-aspnet-getting-started-queues/get-queue-length-results.png)


## <a name="delete-a-queue"></a>Eliminación de una cola
En esta sección se muestra cómo eliminar una cola. 

> [!NOTE]
> 
> Se da por supuesto que ha completado los pasos de [Configuración del entorno de desarrollo](#set-up-the-development-environment). 

1. Abra el archivo `QueuesController.cs` .

1. Agregue un método llamado **DeleteQueue** que devuelve un **ActionResult**.

    ```csharp
    public ActionResult DeleteQueue()
    {
        // The code in this section goes here.

        return View();
    }
    ```
 
1. Dentro del método **DeleteQueue**, obtenga un objeto **CloudStorageAccount** que represente la información de su cuenta de almacenamiento. Utilice el código siguiente para obtener la información de cuenta de almacenamiento y la cadena de conexión de almacenamiento de la configuración del servicio de Azure: (cambie *&lt;storage-account-name>*  por el nombre de la cuenta de Azure Storage a la que está obteniendo acceso.)
   
    ```csharp
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
       CloudConfigurationManager.GetSetting("<storage-account-name>_AzureStorageConnectionString"));
    ```
   
1. Obtenga un objeto **CloudQueueClient** que representa un cliente de servicio de cola.
   
    ```csharp
    CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();
    ```

1. Obtenga un objeto **CloudQueueContainer** que representa una referencia a la cola. 
   
    ```csharp
    CloudQueue queue = queueClient.GetQueueReference("test-queue");
    ```

1. Llame al método **CloudQueue.Delete** para eliminar la cola representada por el objeto **CloudQueue**.

    ```csharp
    queue.Delete();
    ```

1. Actualice **ViewBag** con el nombre de la cola y su longitud.

    ```csharp
    ViewBag.QueueName = queue.Name;
    ```
 
1. En el **Explorador de soluciones**, expanda la carpeta **Vistas**, haga clic con el botón derecho en **Colas** y, en el menú contextual, seleccione **Agregar > Vista**.

1. En el cuadro de diálogo **Agregar vista**, escriba **DeleteQueue** como nombre de vista y seleccione **Agregar**.

1. Abra `DeleteQueue.cshtml` y modifíquelo de modo que se parezca al siguiente fragmento de código:

    ```csharp
    @{
        ViewBag.Title = "DeleteQueue";
    }
    
    <h2>Delete Queue results</h2>
    
    @ViewBag.QueueName deleted.
    ```

1. En el **Explorador de soluciones**, expanda la carpeta **Vistas->Compartido** y abra `_Layout.cshtml`.

1. Después del último **Html.ActionLink**, agregue el siguiente **Html.ActionLink**:

    ```html
    <li>@Html.ActionLink("Delete queue", "DeleteQueue", "Queues")</li>
    ```

1. Ejecute la aplicación y seleccione **Get queue length** (Obtener la longitud de cola) para ver resultados similares a la siguiente captura de pantalla:
  
    ![Eliminar cola](./media/vs-storage-aspnet-getting-started-queues/delete-queue-results.png)

## <a name="next-steps"></a>Pasos siguientes
Consulte más guías de características para obtener información acerca de otras opciones del almacenamiento de datos en Azure.

  * [Introducción a Azure Blob Storage y los servicios conectados de Visual Studio (ASP.NET)](../storage/vs-storage-aspnet-getting-started-blobs.md)
  * [Introducción a Azure Table Storage y a Servicios conectados de Visual Studio (ASP.NET)](vs-storage-aspnet-getting-started-tables.md)
