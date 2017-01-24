---
title: "Introducción a Azure Queue Storage y a Servicios conectados de Visual Studio (ASP.NET) | Microsoft Docs"
description: "Cómo empezar a usar Azure Queue Storage en un proyecto ASP.NET en Visual Studio después de conectarse a una cuenta de almacenamiento mediante Servicios conectados de Visual Studio"
services: storage
documentationcenter: 
author: TomArcher
manager: douge
editor: 
ms.assetid: 94ca3413-5497-433f-abbe-836f83a9de72
ms.service: storage
ms.workload: web
ms.tgt_pltfrm: vs-getting-started
ms.devlang: na
ms.topic: article
ms.date: 12/02/2016
ms.author: tarcher
translationtype: Human Translation
ms.sourcegitcommit: f58c2b522f81dc2dc86f0d2c6bc4872504cf7377
ms.openlocfilehash: 70875287e79aaf49e1b8802cf2953cd5381b97f4


---
# <a name="get-started-with-azure-queue-storage-and-visual-studio-connected-services-aspnet"></a>Introducción a Azure Queue Storage y a Servicios conectados de Visual Studio (ASP.NET)
[!INCLUDE [storage-try-azure-tools-queues](../../includes/storage-try-azure-tools-queues.md)]

## <a name="overview"></a>Información general

Azure Queue Storage es un servicio para almacenar grandes cantidades de datos no estructurados a los que puede acceder a través de HTTP o HTTPS. Un único mensaje en cola puede tener un tamaño de hasta 64 KB y una cola puede contener un número ilimitado de mensajes, hasta el límite de la capacidad total de una cuenta de almacenamiento.

En este artículo se describe cómo administrar entidades de Azure Queue Storage mediante programación, realizar tareas comunes como crear una cola de Azure, y agregar, modificar, leer y eliminar mensajes de la cola.

> [!NOTE]
> 
> En las secciones de código de este artículo se supone que ya se ha conectado a una cuenta de almacenamiento de Azure mediante Servicios conectados. Para configurar Servicios conectados, abra el Explorador de soluciones de Visual Studio, haga clic con el botón derecho en el proyecto y, en el menú contextual, seleccione la opción **Agregar-> Servicio conectado**. Desde allí, siga las instrucciones del cuadro de diálogo para conectarse a la cuenta de almacenamiento de Azure que desee.      

## <a name="create-a-queue"></a>Creación de una cola

Los siguientes pasos muestran cómo crear una cola mediante programación. En una aplicación ASP.NET MVC, el código iría en un controlador.

1. Agregue las siguientes directivas *using*:
   
        using Microsoft.Azure;
        using Microsoft.WindowsAzure.Storage;
        using Microsoft.WindowsAzure.Storage.Queue;

2. Obtenga un objeto **CloudStorageAccount** que represente la información de su cuenta de almacenamiento. Use el código siguiente para obtener la cadena de conexión de almacenamiento y la información de la cuenta de almacenamiento de la configuración del servicio de Azure. (Cambie *<storage-account-name>* por el nombre de la cuenta de almacenamiento de Azure a la que está accediendo).

         CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
           CloudConfigurationManager.GetSetting("<storage-account-name>_AzureStorageConnectionString"));

3. Obtenga un objeto **CloudQueueClient** que representa un cliente de servicio de cola.

        CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();

4. Obtenga un objeto **CloudQueue** que representa una referencia al nombre de cola que desea. (Cambie * <queue-name> * por el nombre de la cola que desea crear).

        CloudQueue queue = queueClient.GetQueueReference(<queue-name>);

5. Llame a la **CloudQueue.CreateIfNotExists** método para crear la cola si aún no existe. 

        queue.CreateIfNotExists();


## <a name="add-a-message-to-a-queue"></a>un mensaje a una cola

Los siguientes pasos muestran cómo agregar un mensaje a una cola mediante programación. En una aplicación ASP.NET MVC, el código iría en un controlador.

1. Agregue las siguientes directivas *using*:
   
        using Microsoft.Azure;
        using Microsoft.WindowsAzure.Storage;
        using Microsoft.WindowsAzure.Storage.Queue;

2. Obtenga un objeto **CloudStorageAccount** que represente la información de su cuenta de almacenamiento. Use el código siguiente para obtener la cadena de conexión de almacenamiento y la información de la cuenta de almacenamiento de la configuración del servicio de Azure. (Cambie *<storage-account-name>* por el nombre de la cuenta de almacenamiento de Azure a la que está accediendo).

         CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
           CloudConfigurationManager.GetSetting("<storage-account-name>_AzureStorageConnectionString"));

3. Obtenga un objeto **CloudQueueClient** que representa un cliente de servicio de cola.

        CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();

4. Obtenga un objeto **CloudQueue** que representa una referencia al nombre de cola que desea. (Cambie *<queue-name> * por el nombre de la cola a la que desea agregar el mensaje).

        CloudQueue queue = queueClient.GetQueueReference(<queue-name>);

5. Cree el objeto **CloudQueueMessage** que representa el mensaje que desea agregar a la cola. Se puede crear un objeto **CloudQueueMessage** a partir de una cadena (en formato UTF-8) o de una matriz de bytes. (Cambie * <queue-message> * por el mensaje que desea agregar.

        CloudQueueMessage message = new CloudQueueMessage(<queue-message>);

6. Llame al método **CloudQueue.AddMessage** para agregar el mensaje a la cola.

        queue.AddMessage(message);

## <a name="read-a-message-from-a-queue-without-removing-it"></a>Lectura de un mensaje de una cola sin eliminarlo

En los pasos siguientes se muestra cómo inspeccionar mediante programación un mensaje en cola (leer el primer mensaje sin eliminarlo). En una aplicación ASP.NET MVC, el código iría en un controlador. 

1. Agregue las siguientes directivas *using*:
   
        using Microsoft.Azure;
        using Microsoft.WindowsAzure.Storage;
        using Microsoft.WindowsAzure.Storage.Queue;

2. Obtenga un objeto **CloudStorageAccount** que represente la información de su cuenta de almacenamiento. Use el código siguiente para obtener la cadena de conexión de almacenamiento y la información de la cuenta de almacenamiento de la configuración del servicio de Azure. (Cambie *<storage-account-name>* por el nombre de la cuenta de almacenamiento de Azure a la que está accediendo).

         CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
           CloudConfigurationManager.GetSetting("<storage-account-name>_AzureStorageConnectionString"));

3. Obtenga un objeto **CloudQueueClient** que representa un cliente de servicio de cola.

        CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();

4. Obtenga un objeto **CloudQueue** que representa una referencia a la cola. (Cambie *<queue-name> * por el nombre de la cola de la que desea leer un mensaje).

        CloudQueue queue = queueClient.GetQueueReference(<queue-name>);

5. Llame al método **CloudQueue.PeekMessage** para leer el mensaje situado en la parte delantera de una cola sin quitarlo de ella.

        CloudQueueMessage message = queue.PeekMessage();

6. Acceda al valor del objeto **CloudQueueMessage** utilizando la propiedad **CloudQueueMessage.AsBytes** o **CloudQueueMessage.AsString**.

        string messageAsString = message.AsString;
        byte[] messageAsBytes = message.AsBytes;

## <a name="read-and-remove-a-message-from-a-queue"></a>Lectura y eliminación de un mensaje de una cola

Los siguientes pasos muestran cómo leer un mensaje en cola mediante programación y después eliminarlo. En una aplicación ASP.NET MVC, el código iría en un controlador. 

1. Agregue las siguientes directivas *using*:
   
        using Microsoft.Azure;
        using Microsoft.WindowsAzure.Storage;
        using Microsoft.WindowsAzure.Storage.Queue;

2. Obtenga un objeto **CloudStorageAccount** que represente la información de su cuenta de almacenamiento. Use el código siguiente para obtener la cadena de conexión de almacenamiento y la información de la cuenta de almacenamiento de la configuración del servicio de Azure. (Cambie *<storage-account-name>* por el nombre de la cuenta de almacenamiento de Azure a la que está accediendo).

         CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
           CloudConfigurationManager.GetSetting("<storage-account-name>_AzureStorageConnectionString"));

3. Obtenga un objeto **CloudQueueClient** que representa un cliente de servicio de cola.

        CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();

4. Obtenga un objeto **CloudQueue** que representa una referencia a la cola. (Cambie *<queue-name> * por el nombre de la cola de la que desea leer un mensaje).

        CloudQueue queue = queueClient.GetQueueReference(<queue-name>);

5. Llame al método **CloudQueue.GetMessage** para leer el primer mensaje de la cola. El método **CloudQueue.GetMessage** hace que el mensaje sea invisible durante 30 segundos (de forma predeterminada) para cualquier otro código que lea mensajes de forma que ningún otro código pueda modificar o eliminar el mensaje mientras lo procesa. Para cambiar la cantidad de tiempo que el mensaje es invisible, modifique el parámetro **visibilityTimeout** que se pasa al método **CloudQueue.GetMessage**.

        // This message will be invisible to other code for 30 seconds.
        CloudQueueMessage message = queue.GetMessage();     

6. Llame al método **CloudQueueMessage.Delete** para eliminar el mensaje de la cola.

        queue.DeleteMessage(message);

## <a name="get-the-queue-length"></a>la longitud de la cola

En los siguientes pasos se muestra cómo obtener la longitud de la cola mediante programación (número de mensajes). En una aplicación ASP.NET MVC, el código iría en un controlador. 

1. Agregue las siguientes directivas *using*:
   
        using Microsoft.Azure;
        using Microsoft.WindowsAzure.Storage;
        using Microsoft.WindowsAzure.Storage.Queue;

2. Obtenga un objeto **CloudStorageAccount** que represente la información de su cuenta de almacenamiento. Use el código siguiente para obtener la cadena de conexión de almacenamiento y la información de la cuenta de almacenamiento de la configuración del servicio de Azure. (Cambie *<storage-account-name>* por el nombre de la cuenta de almacenamiento de Azure a la que está accediendo).

         CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
           CloudConfigurationManager.GetSetting("<storage-account-name>_AzureStorageConnectionString"));

3. Obtenga un objeto **CloudQueueClient** que representa un cliente de servicio de cola.

        CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();

4. Obtenga un objeto **CloudQueue** que representa una referencia a la cola. (Cambie * <queue-name> * por el nombre de la cola cuya longitud está consultando).

        CloudQueue queue = queueClient.GetQueueReference(<queue-name>);

5. Llame al método **CloudQueue.FetchAttributes** para recuperar los atributos de la cola (incluida su longitud). 

        queue.FetchAttributes();

6. Acceda a la propiedad **CloudQueue.ApproximateMessageCount** para obtener la longitud de la cola.
 
        int? nMessages = queue.ApproximateMessageCount;

## <a name="delete-a-queue"></a>Eliminación de una cola
En los siguientes pasos se muestra cómo eliminar una cola mediante programación. 

1. Agregue las siguientes directivas *using*:
   
        using Microsoft.Azure;
        using Microsoft.WindowsAzure.Storage;
        using Microsoft.WindowsAzure.Storage.Queue;

2. Obtenga un objeto **CloudStorageAccount** que represente la información de su cuenta de almacenamiento. Use el código siguiente para obtener la cadena de conexión de almacenamiento y la información de la cuenta de almacenamiento de la configuración del servicio de Azure. (Cambie *<storage-account-name>* por el nombre de la cuenta de almacenamiento de Azure a la que está accediendo).

         CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
           CloudConfigurationManager.GetSetting("<storage-account-name>_AzureStorageConnectionString"));

3. Obtenga un objeto **CloudQueueClient** que representa un cliente de servicio de cola.

        CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();

4. Obtenga un objeto **CloudQueue** que representa una referencia a la cola. (Cambie * <queue-name> * por el nombre de la cola cuya longitud está consultando).

        CloudQueue queue = queueClient.GetQueueReference(<queue-name>);

5. Llame al método **CloudQueue.Delete** para eliminar la cola representada por el objeto **CloudQueue**.

        messageQueue.Delete();

## <a name="next-steps"></a>Pasos siguientes
[!INCLUDE [vs-storage-dotnet-queues-next-steps](../../includes/vs-storage-dotnet-queues-next-steps.md)]




<!--HONumber=Dec16_HO2-->


