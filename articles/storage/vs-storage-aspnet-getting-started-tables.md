<properties 
	pageTitle="Introducción a Almacenamiento de Azure" 
	description="Cómo empezar a usar el almacenamiento de tablas de Azure en un proyecto de ASP.NET en Visual Studio" 
	services="storage" 
	documentationCenter="" 
	authors="kempb" 
	manager="douge" 
	editor="tglee"/>

<tags 
	ms.service="storage" 
	ms.workload="web" 
	ms.tgt_pltfrm="vs-getting-started" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="02/02/2015" 
	ms.author="kempb"/>

> [AZURE.SELECTOR]
> - [Introducción](vs-storage-aspnet-getting-started-tables.md)
> - [¿Qué ha ocurrido?](vs-storage-aspnet-what-happened.md)

## Introducción a Almacenamiento de Azure (proyectos ASP.NET)

> [AZURE.SELECTOR]
> - [Blobs](vs-storage-aspnet-getting-started-blobs.md)
> - [Colas](vs-storage-aspnet-getting-started-queues.md)
> - [Tablas](vs-storage-aspnet-getting-started-tables.md)

El servicio de almacenamiento de tabla de Azure permite almacenar una gran cantidad de datos estructurados. El servicio es un almacén de datos NoSQL que acepta llamadas autenticadas desde dentro y fuera de la nube de Azure. Las tablas de Azure son ideales para el almacenamiento de datos estructurados no relacionales.  Consulte [Uso de almacenamiento de tabla de .NET](http://azure.microsoft.com/documentation/articles/storage-dotnet-how-to-use-tables/#create-table "How to use Table Storage from .NET") para obtener más información.

Agregue las siguientes declaraciones de espacio de nombres de código en la parte superior de todo archivo C# en el que desee obtener acceso al Almacenamiento de Azure mediante programación.

	using Microsoft.WindowsAzure.Storage;
	using Microsoft.WindowsAzure.Storage.Auth;
	using Microsoft.WindowsAzure.Storage.Table;

#####Obtención de la cadena de conexión de almacenamiento
Antes de realizar cualquier acción con una tabla, tendrá que obtener la cadena de conexión para la cuenta de almacenamiento en la que residen las tablas. Puede utilizar el tipo **CloudStorageAccount** para representar la información de la cuenta de almacenamiento. En proyectos ASP.NET, puede utilizar el tipo **ConfigurationManager** para recuperar la información de la cuenta de almacenamiento y la cadena de conexión de almacenamiento de la configuración del servicio de Azure, como se muestra en el código siguiente.

	CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
      ConfigurationManager.GetSetting("<storageAccountName>_AzureStorageConnectionString"));

[WACOM.INCLUDE [vs-storage-getting-started-tables-include](../../includes/vs-storage-getting-started-tables-include.md)]

Para obtener más información, consulte [ASP.NET](http://www.asp.net).

<!--HONumber=46--> 
 