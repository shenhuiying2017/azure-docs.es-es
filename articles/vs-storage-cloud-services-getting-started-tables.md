<properties 
	pageTitle="Introducción a Almacenamiento de Azure" 
	description="" 
	services="storage" 
	documentationCenter="" 
	authors="kempb" 
	manager="douge" 
	editor=""/>

<tags 
	ms.service="storage" 
	ms.workload="web" 
	ms.tgt_pltfrm="vs-getting-started" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="09/17/2014" 
	ms.author="kempb"/>

> [AZURE.SELECTOR]
> - [Introducción](vs-storage-cloud-services-getting-started-tables.md)
> - [¿Qué ha ocurrido?](vs-storage-cloud-services-what-happened.md)

##Introducción a Almacenamiento de Azure (proyectos de servicio en la nube)

> [AZURE.SELECTOR]
> - [Blobs](vs-storage-cloud-services-getting-started-blobs.md)
> - [Colas](vs-storage-cloud-services-getting-started-queues.md)
> - [Tablas](vs-storage-cloud-services-getting-started-tables.md)

El servicio de almacenamiento de tabla de Azure permite almacenar una gran cantidad de datos estructurados. El servicio es un almacén de datos NoSQL que acepta llamadas autenticadas desde dentro y fuera de la nube de Azure. Las tablas de Azure son ideales para el almacenamiento de datos estructurados no relacionales.  Para obtener más información, consulte [Uso del almacenamiento de tablas en .NET](http://azure.microsoft.com/documentation/articles/storage-dotnet-how-to-use-tables/#create-table "How to use Table Storage from .NET").

Agregue las siguientes declaraciones de espacio de nombres de código en la parte superior de todo archivo C# en el que desee obtener acceso al Almacenamiento de Azure mediante programación.

	using Microsoft.WindowsAzure.Storage;
	using Microsoft.WindowsAzure.Storage.Auth;
	using Microsoft.WindowsAzure.Storage.Table;

##### Obtención de la cadena de conexión de almacenamiento
Antes de realizar cualquier acción con una tabla, debe obtener la cadena de conexión para la cuenta de almacenamiento en la que residirán las tablas. Puede usar el tipo **CloudStorageAccount** para representar la información de la cuenta de almacenamiento. En proyectos de servicio en la nube, puede usar el tipo **CloudConfigurationManager** para recuperar la información de la cuenta de almacenamiento y la cadena de conexión de almacenamiento de la configuración del servicio de Azure, como se muestra en el código siguiente.

	CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
      CloudConfigurationManager.GetSetting("<storageAccountName>_AzureStorageConnectionString"));

[AZURE.INCLUDE [vs-storage-getting-started-tables-include](../includes/vs-storage-getting-started-tables-include.md)]

<!--HONumber=42-->
