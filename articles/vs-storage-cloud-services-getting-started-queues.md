<properties title="Getting Started with Azure Storage" pageTitle="Getting Started with Azure Storage" metaKeywords="Azure, Getting Started, Storage" description="" services="storage" documentationCenter="" authors="ghogen, kempb" />

<tags ms.service="storage" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="10/10/2014" ms.author="ghogen, kempb"></tags>

[WACOM.INCLUDE [vs-storage-aspnet-getting-started-intro][vs-storage-aspnet-getting-started-intro]]

### Introducción a Almacenamiento de Azure

<div class="dev-center-tutorial-selector sublanding"><a href="/es-es/documentation/articles/vs-storage-cloud-services-getting-started-blobs" title="Blobs" class="current">Blobs</a><a href="/es-es/documentation/articles/vs-storage-cloud-services-getting-started-queues" title="Colas">Colas</a><a href="/es-es/documentation/articles/vs-storage-cloud-services-getting-started-tables" title="Tablas">Tablas</a></div>

El almacenamiento de cola de Azure es un servicio para almacenar grandes cantidades de mensajes a los que puede obtenerse acceso desde cualquier lugar del mundo a través de llamadas autenticadas con HTTP o HTTPS. Un único mensaje en cola puede tener un tamaño de hasta 64 KB y una cola puede contener millones de mensajes, hasta el límite de capacidad total de una cuenta de almacenamiento. Consulte [Uso del almacenamiento de colas en .NET][Uso del almacenamiento de colas en .NET] para obtener más información.

Para tener acceso mediante programación a colas en un proyecto de servicio en la nube, tendrá que realizar las tareas siguientes.

1.  Obtenga el ensamblado Microsoft.WindowsAzure.Storage.dll. Puede utilizar NuGet para ello. Haga clic con el botón secundario en el proyecto, en el Explorador de soluciones, y elija Administrar paquetes de NuGet. Busque "Azure.Storage" en línea y haga clic en Instalar para instalar el paquete y las dependencias de Almacenamiento de Azure. Agregue una referencia a este ensamblado en su proyecto.
2.  Agregue las siguientes declaraciones de espacio de nombres de código en la parte superior de todo archivo C# en el que desee obtener acceso al Almacenamiento de Azure mediante programación.

    uso de Microsoft.WindowsAzure.Storage;
    uso de Microsoft.WindowsAzure.Storage.Auth;
    uso de Microsoft.WindowsAzure.Storage.Queue;

###### Obtención de la cadena de conexión de almacenamiento

Antes de realizar cualquier acción con una cola, tendrá que obtener la cadena de conexión para la cuenta de almacenamiento en la que reside la cola. Puede utilizar el tipo **CloudStorageAccount** para representar la información de la cuenta de almacenamiento. En proyectos de servicio en la nube, puede utilizar el tipo **CloudConfigurationManager** para recuperar la información de la cuenta de almacenamiento y la cadena de conexión de almacenamiento de la configuración del servicio de Azure, como se muestra en el código siguiente.

    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
      CloudConfigurationManager.GetSetting("<storageAccountName>_AzureStorageConnectionString"));

[WACOM.INCLUDE [vs-storage-getting-started-queues-include][vs-storage-getting-started-queues-include]]

  [vs-storage-aspnet-getting-started-intro]: ../includes/vs-storage-aspnet-getting-started-intro.md
  [Blobs]: /es-es/documentation/articles/vs-storage-cloud-services-getting-started-blobs "Blobs"
  [Colas]: /es-es/documentation/articles/vs-storage-cloud-services-getting-started-queues "Colas"
  [Tablas]: /es-es/documentation/articles/vs-storage-cloud-services-getting-started-tables "Tablas"
  [Uso del almacenamiento de colas en .NET]: http://azure.microsoft.com/es-es/documentation/articles/storage-dotnet-how-to-use-queues/ "Uso del almacenamiento de colas en .NET"
  [vs-storage-getting-started-queues-include]: ../includes/vs-storage-getting-started-queues-include.md
