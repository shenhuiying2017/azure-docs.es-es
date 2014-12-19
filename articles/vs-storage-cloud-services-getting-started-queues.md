<properties title="Getting Started with Azure Storage" pageTitle="Introducción a Almacenamiento de Azure" metaKeywords="Azure, Getting Started, Storage" description="" services="storage" documentationCenter="" authors="ghogen, kempb" />

<tags ms.service="storage" ms.workload="web" ms.tgt_pltfrm="vs-getting-started" ms.devlang="na" ms.topic="article" ms.date="10/10/2014" ms.author="ghogen, kempb" />

> [AZURE.SELECTOR]
> - [Introducción](/documentation/articles/vs-storage-cloud-services-getting-started-queues/)
> - [¿Qué ha ocurrido?](/documentation/articles/vs-storage-cloud-services-what-happened/)

## Introducción a Almacenamiento de Azure (proyectos de servicio en la nube)

> [AZURE.SELECTOR]
> - [Blobs](/documentation/articles/vs-storage-cloud-services-getting-started-blobs)
> - [Queues](/documentation/articles/vs-storage-cloud-services-getting-started-queues/)
> - [Tablas](/documentation/articles/vs-storage-cloud-services-getting-started-tables/)

El almacenamiento de cola de Azure es un servicio para almacenar grandes cantidades de mensajes a los que puede obtenerse acceso desde cualquier lugar del mundo a través de llamadas autenticadas con HTTP o HTTPS. Un único mensaje en cola puede tener un tamaño de hasta 64 KB y una cola puede contener millones de mensajes, hasta el límite de capacidad total de una cuenta de almacenamiento. Consulte [Uso del almacenamiento de cola en .NET](http://azure.microsoft.com/es-es/documentation/articles/storage-dotnet-how-to-use-queues/ "How to use Queue Storage from .NET") para obtener más información.

Agregue las siguientes declaraciones de espacio de nombres de código en la parte superior de todo archivo C# en el que desee obtener acceso al Almacenamiento de Azure mediante programación.

	using Microsoft.WindowsAzure.Storage;
	using Microsoft.WindowsAzure.Storage.Auth;
	using Microsoft.WindowsAzure.Storage.Queue;

#####Obtención de la cadena de conexión de almacenamiento
Antes de realizar cualquier acción con una cola, tendrá que obtener la cadena de conexión para la cuenta de almacenamiento en la que reside la cola. Puede utilizar el tipo **CloudStorageAccount** para representar la información de la cuenta de almacenamiento. En proyectos de servicio en la nube, puede utilizar el tipo **CloudConfigurationManager** para recuperar la información de la cuenta de almacenamiento y la cadena de conexión de almacenamiento de la configuración del servicio de Azure, como se muestra en el código siguiente.

	CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
      CloudConfigurationManager.GetSetting("<storageAccountName>_AzureStorageConnectionString"));

[WACOM.INCLUDE [vs-storage-getting-started-queues-include](../includes/vs-storage-getting-started-queues-include.md)]

