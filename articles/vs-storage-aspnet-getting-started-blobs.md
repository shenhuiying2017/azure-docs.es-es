<properties title="Getting Started with Azure Storage" pageTitle="Getting Started with Azure Storage" metaKeywords="Azure, Getting Started, Storage" description="" services="storage" documentationCenter="" authors="ghogen, kempb" />

<tags ms.service="storage" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="10/10/2014" ms.author="ghogen, kempb"></tags>

[WACOM.INCLUDE [vs-storage-aspnet-getting-started-intro](../includes/vs-storage-aspnet-getting-started-intro.md)]

### Introducción a Almacenamiento de Azure

<div class="dev-center-tutorial-selector sublanding"><a href="/es-es/documentation/articles/vs-storage-aspnet-getting-started-blobs" title="Blobs" class="current">Blobs</a><a href="/es-es/documentation/articles/vs-storage-aspnet-getting-started-queues" title="Colas">Colas</a><a href="/es-es/documentation/articles/vs-storage-aspnet-getting-started-tables" title="Tablas">Tablas</a></div>

El almacenamiento de blobs de Azure es un servicio para almacenar grandes cantidades de datos no estructurados a los que puede obtenerse acceso desde cualquier lugar del mundo a través de HTTP o HTTPS. Un solo blob puede tener cualquier tamaño. Los blobs pueden tener forma de imágenes, archivos de audio y vídeo, archivos sin procesar y archivos de documentos.

Para iniciar, tiene que crear una cuenta de almacenamiento de Azure y, a continuación, crear uno o varios contenedores en el almacenamiento. Por ejemplo, puede crear un almacenamiento llamado "Scrapbook", a continuación crear contenedores en el almacenamiento llamados "images" para almacenar imágenes y otro llamado "audio" para almacenar los archivos de audio. Una vez creados los contenedores, puede cargar archivos de blob individuales a ellos. Consulte [Uso del almacenamiento de blobs en .NET][Uso del almacenamiento de blobs en .NET] para obtener más información acerca de la manipulación de blobs mediante programación.

Para tener acceso mediante programación a blobs en proyectos ASP.NET, tendrá que realizar las tareas siguientes.

1.  Obtenga el ensamblado Microsoft.WindowsAzure.Storage.dll. Puede utilizar NuGet para ello. Haga clic con el botón secundario en el proyecto, en el Explorador de soluciones, y elija Administrar paquetes de NuGet. Busque "Azure.Storage" en línea y haga clic en Instalar para instalar el paquete y las dependencias de Almacenamiento de Azure. Agregue una referencia a este ensamblado en su proyecto.
2.  Agregue las siguientes declaraciones de espacio de nombres de código en la parte superior de todo archivo C# en el que desee obtener acceso al Almacenamiento de Azure mediante programación.

    uso de Microsoft.WindowsAzure.Storage;
    uso de Microsoft.WindowsAzure.Storage.Auth;
    uso de Microsoft.WindowsAzure.Storage.Queue;

###### Obtención de la cadena de conexión de almacenamiento

Antes de realizar cualquier acción con un blob, tendrá que obtener la cadena de conexión para la cuenta de almacenamiento en la que residen los blobs. Puede utilizar el tipo **CloudStorageAccount** para representar la información de la cuenta de almacenamiento. En proyectos ASP.NET, puede utilizar el tipo **ConfigurationManager** para recuperar la información de la cuenta de almacenamiento y la cadena de conexión de almacenamiento de la configuración del servicio de Azure, como se muestra en el código siguiente.

    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
      ConfigurationManager.GetSetting("<storageAccountName>_AzureStorageConnectionString"));

[WACOM.INCLUDE [vs-storage-getting-started-blobs-include](../includes/vs-storage-getting-started-blobs-include.md)]

  [vs-storage-aspnet-getting-started-intro]: ../includes/vs-storage-aspnet-getting-started-intro.md
  [Blobs]: /es-es/documentation/articles/vs-storage-aspnet-getting-started-blobs "Blobs"
  [Colas]: /es-es/documentation/articles/vs-storage-aspnet-getting-started-queues "Colas"
  [Tablas]: /es-es/documentation/articles/vs-storage-aspnet-getting-started-tables "Tablas"
  [Uso del almacenamiento de blobs en .NET]: http://azure.microsoft.com/es-es/documentation/articles/storage-dotnet-how-to-use-blobs/ "Uso del almacenamiento de blobs en .NET"
  [vs-storage-getting-started-blobs-include]: ../includes/vs-storage-getting-started-blobs-include.md
