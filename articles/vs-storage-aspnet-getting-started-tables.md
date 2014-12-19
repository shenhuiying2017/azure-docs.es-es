<properties title="Getting Started with Azure Storage" pageTitle="Introducción a Almacenamiento de Azure" metaKeywords="Azure, Getting Started, Storage" description="" services="storage" documentationCenter="" authors="ghogen, kempb" />

<tags ms.service="storage" ms.workload="web" ms.tgt_pltfrm="vs-getting-started" ms.devlang="na" ms.topic="article" ms.date="09/17/2014" ms.author="ghogen, kempb" />

> [AZURE.SELECTOR]
> - [Introducción](/documentation/articles/vs-storage-aspnet-getting-started-tables/)
> - [¿Qué ha ocurrido?](/documentation/articles/vs-storage-aspnet-what-happened/)

## Introducción a Almacenamiento de Azure (proyectos ASP.NET)

> [AZURE.SELECTOR]
> - [Blobs](/documentation/articles/vs-storage-aspnet-getting-started-blobs/)
> - [Colas](/documentation/articles/vs-storage-aspnet-getting-started-queues/)
> - [Tablas](/documentation/articles/vs-storage-aspnet-getting-started-tables/)

El servicio de almacenamiento de tabla de Azure permite almacenar una gran cantidad de datos estructurados. El servicio es un almacén de datos NoSQL que acepta llamadas autenticadas desde dentro y fuera de la nube de Azure. Las tablas de Azure son ideales para el almacenamiento de datos estructurados no relacionales.  Consulte [Uso de almacenamiento de tabla de .NET](http://azure.microsoft.com/es-es/documentation/articles/storage-dotnet-how-to-use-tables/#create-table "How to use Table Storage from .NET") para obtener más información.

Agregue las siguientes declaraciones de espacio de nombres de código en la parte superior de todo archivo C# en el que desee obtener acceso al Almacenamiento de Azure mediante programación.

	using Microsoft.WindowsAzure.Storage;
	using Microsoft.WindowsAzure.Storage.Auth;
	using Microsoft.WindowsAzure.Storage.Table;

#####Obtención de la cadena de conexión de almacenamiento
Antes de realizar cualquier acción con una tabla, tendrá que obtener la cadena de conexión para la cuenta de almacenamiento en la que residen las tablas. Puede utilizar el tipo **CloudStorageAccount** para representar la información de la cuenta de almacenamiento. En proyectos ASP.NET, puede utilizar el tipo **ConfigurationManager** para recuperar la información de la cuenta de almacenamiento y la cadena de conexión de almacenamiento de la configuración del servicio de Azure, como se muestra en el código siguiente.

	CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
      ConfigurationManager.GetSetting("<storageAccountName>_AzureStorageConnectionString"));

[WACOM.INCLUDE [vs-storage-getting-started-tables-include](../includes/vs-storage-getting-started-tables-include.md)]

Para obtener más información, consulte [ASP.NET](http://www.asp.net).
