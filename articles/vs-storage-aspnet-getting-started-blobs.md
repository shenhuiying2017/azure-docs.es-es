<properties title="Introducci&oacute;n a Almacenamiento de Azure" pageTitle="Introducci&oacute;n a Almacenamiento de Azure" metaKeywords="Azure, Getting Started, Storage" description="" services="storage" documentationCenter="" authors="ghogen, kempb" />

<tags ms.service="storage" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="10/10/2014" ms.author="ghogen, kempb" />

> [AZURE.SELECTOR]
>
> -   [Tareas iniciales][Tareas iniciales]
> -   [¿Qué ha ocurrido?][¿Qué ha ocurrido?]

## Introducción a Almacenamiento de Azure (proyectos ASP.NET)

> [AZURE.SELECTOR]
>
> -   [Blobs][Tareas iniciales]
> -   [Colas][Colas]
> -   [Tablas][Tablas]

El almacenamiento de blobs de Azure es un servicio para almacenar grandes cantidades de datos no estructurados a los que puede obtenerse acceso desde cualquier lugar del mundo a través de HTTP o HTTPS. Un solo blob puede tener cualquier tamaño. Los blobs pueden tener forma de imágenes, archivos de audio y vídeo, archivos sin procesar y archivos de documentos.

Para iniciar, tiene que crear una cuenta de almacenamiento de Azure y, a continuación, crear uno o varios contenedores en el almacenamiento. Por ejemplo, puede crear un almacenamiento llamado "Scrapbook", a continuación crear contenedores en el almacenamiento denominados "images" para almacenar imágenes y "audio" para almacenar archivos de audio. Una vez creados los contenedores, puede cargar archivos de blob individuales a ellos. Consulte [Uso del almacenamiento de blobs en .NET][Uso del almacenamiento de blobs en .NET] para obtener más información acerca de la manipulación de blobs mediante programación.

Agregue las siguientes declaraciones de espacio de nombres de código en la parte superior de todo archivo C# en el que desee obtener acceso al Almacenamiento de Azure mediante programación.

    using Microsoft.WindowsAzure.Storage;
    using Microsoft.WindowsAzure.Storage.Auth;
    using Microsoft.WindowsAzure.Storage.Blob;

##### Obtención de la cadena de conexión de almacenamiento

Antes de realizar cualquier acción con un blob, tendrá que obtener la cadena de conexión para la cuenta de almacenamiento en la que residen los blobs. Puede utilizar el tipo **CloudStorageAccount** para representar la información de la cuenta de almacenamiento. En proyectos ASP.NET, puede utilizar el tipo **ConfigurationManager** para recuperar la información de la cuenta de almacenamiento y la cadena de conexión de almacenamiento de la configuración del servicio de Azure, como se muestra en el código siguiente.

    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
      ConfigurationManager.AppSettings["<storageAccountName>_AzureStorageConnectionString"]);

[WACOM.INCLUDE [vs-storage-getting-started-blobs-include](../includes/vs-storage-getting-started-blobs-include.md)]

Para obtener más información, consulte [ASP.NET][ASP.NET].

  [Tareas iniciales]: /documentation/articles/vs-storage-aspnet-getting-started-blobs/
  [¿Qué ha ocurrido?]: /documentation/articles/vs-storage-aspnet-what-happened/
  [Colas]: /documentation/articles/vs-storage-aspnet-getting-started-queues/
  [Tablas]: /documentation/articles/vs-storage-aspnet-getting-started-tables/
  [Uso del almacenamiento de blobs en .NET]: http://azure.microsoft.com/es-es/documentation/articles/storage-dotnet-how-to-use-blobs/ "Uso del almacenamiento de blobs en .NET"
  [ASP.NET]: http://www.asp.net
