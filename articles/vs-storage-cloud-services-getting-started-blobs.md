<properties 
	pageTitle="Introducción a Almacenamiento de Azure" 
	description="Cómo empezar a usar el almacenamiento de blobs de Azure en un proyecto de servicio en la nube en Visual Studio" 
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
> - [Introducción](/documentation/articles/vs-storage-cloud-services-getting-started-blobs/)
> - [¿Qué ha ocurrido?](/documentation/articles/vs-storage-cloud-services-what-happened/)

## Introducción a Almacenamiento de Azure (proyectos de servicio en la nube)

> [AZURE.SELECTOR]
> - [Blobs](/documentation/articles/vs-storage-cloud-services-getting-started-blobs/)
> - [Colas](/documentation/articles/vs-storage-cloud-services-getting-started-queues/)
> - [Tablas](/documentation/articles/vs-storage-cloud-services-getting-started-tables/)

El almacenamiento de blobs de Azure es un servicio para almacenar grandes cantidades de datos no estructurados a los que puede obtenerse acceso desde cualquier lugar del mundo a través de HTTP o HTTPS. Un solo blob puede tener cualquier tamaño. Los blobs pueden tener forma de imágenes, archivos de audio y vídeo, archivos sin procesar y archivos de documentos.

Para iniciar, tiene que crear una cuenta de Almacenamiento de Azure y, a continuación, crear uno o varios contenedores en el almacenamiento. Por ejemplo, puede crear un almacenamiento llamado "Scrapbook", a continuación crear contenedores en el almacenamiento denominados "images" para almacenar imágenes y "audio" para almacenar archivos de audio. Una vez creados los contenedores, puede cargar archivos de blob individuales a ellos. Consulte [Uso del almacenamiento de blobs en .NET](http://azure.microsoft.com/documentation/articles/storage-dotnet-how-to-use-blobs/ "How to use Blob Storage from .NET") para obtener más información sobre la manipulación de blobs mediante programación.

Agregue las siguientes declaraciones de espacio de nombres de código en la parte superior de todo archivo C# en el que desee obtener acceso al Almacenamiento de Azure mediante programación.

	using Microsoft.WindowsAzure.Storage;
	using Microsoft.WindowsAzure.Storage.Auth;
	using Microsoft.WindowsAzure.Storage.Blob;

#####Obtención de la cadena de conexión de almacenamiento
Antes de realizar cualquier acción con un blob, tendrá que obtener la cadena de conexión para la cuenta de almacenamiento en la que residen los blobs. Puede utilizar el tipo **CloudStorageAccount** para representar la información de la cuenta de almacenamiento. En proyectos de servicio en la nube, puede utilizar el tipo **CloudConfigurationManager** para recuperar la información de la cuenta de almacenamiento y la cadena de conexión de almacenamiento de la configuración del servicio de Azure, como se muestra en el código siguiente.

	CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
      CloudConfigurationManager.GetSetting("<storageAccountName>_AzureStorageConnectionString"));

[WACOM.INCLUDE [vs-storage-getting-started-blobs-include](../includes/vs-storage-getting-started-blobs-include.md)]

<!--HONumber=46--> 
