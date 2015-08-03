<properties 
	pageTitle="Introducción a Almacenamiento de Azure" 
	description="Describe lo que ha ocurrido al crear un almacenamiento de Azure en un proyecto de Visual Studio ASP.NET 5" 
	services="storage" 
	documentationCenter="" 
	authors="patshea123" 
	manager="douge" 
	editor="tglee"/>

<tags 
	ms.service="storage" 
	ms.workload="web" 
	ms.tgt_pltfrm="vs-what-happened" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="04/20/2015" 
	ms.author="patshea123"/>

# ¿Qué le ha ocurrido a mi proyecto?

> [AZURE.SELECTOR]
> - [Getting Started](vs-storage-aspnet5-getting-started-blobs.md)
> - [What Happened](vs-storage-aspnet5-what-happened.md)

> [AZURE.SELECTOR]
> - [Blobs](vs-storage-aspnet5-getting-started-blobs.md)
> - [Queues](vs-storage-aspnet5-getting-started-queues.md)
> - [Tables](vs-storage-aspnet5-getting-started-tables.md)

###<span id="whathappened">¿Qué le ha ocurrido a mi proyecto?</span>

##### Se han agregado referencias.

El paquete NuGet de Almacenamiento de Azure se agregó al proyecto de Visual Studio. Este paquete agrega las siguientes referencias. NET:

- `Microsoft.Data.Edm`
- `Microsoft.Data.OData`
- `Microsoft.Data.Services.Client`
- `Microsoft.WindowsAzure.Configuration`
- `Microsoft.WindowsAzure.Storage`
- `Newtonsoft.Json`
- `System.Data`
- `System.Spatial`

Asimismo, se agregó el paquete de NuGet **Microsoft.Framework.Configuration.Json**.

#####Se ha agregado la cadena de conexión para Almacenamiento de Azure. 
En el archivo config.json de su proyecto, se ha creado un elemento con la cadena y la clave de conexión de la cuenta de almacenamiento seleccionada.

Para obtener más información, consulte [ASP.NET 5](http://www.asp.net/vnext).
 

<!---HONumber=July15_HO4-->