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
	ms.tgt_pltfrm="vs-what-happened" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="10/10/2014" 
	ms.author="kempb"/>

> [AZURE.SELECTOR]
> - [Introducción](vs-storage-aspnet5-getting-started-blobs.md)
> - [¿Qué ha ocurrido?](vs-storage-aspnet5-what-happened.md)

### <span id="whathappened">¿Qué le ha ocurrido a mi proyecto?</span>

##### Se han agregado referencias.

El paquete NuGet de Almacenamiento de Azure se agregó al proyecto de Visual Studio.  
Este paquete agrega las siguientes referencias. NET:

- `Microsoft.Data.Edm`
- `Microsoft.Data.OData`
- `Microsoft.Data.Services.Client`
- `Microsoft.WindowsAzure.Configuration`
- `Microsoft.WindowsAzure.Storage`
- `Newtonsoft.Json`
- `System.Data`
- `System.Spatial`

Además, se ha agregado el paquete de NuGet **Microsoft.Framework.ConfigurationModel.Json**.

##### Se ha agregado la cadena de conexión para Almacenamiento de Azure. 
En el archivo config.json de su proyecto, se ha creado un elemento con la cadena y la clave de conexión de la cuenta de almacenamiento seleccionada.

Para obtener más información, consulte [ASP.NET 5](http://www.asp.net/vnext).


<!--HONumber=42-->
