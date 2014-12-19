<properties title="Getting Started with Azure Storage" pageTitle="Introducción a Almacenamiento de Azure" metaKeywords="Azure, Getting Started, Storage" description="" services="storage" documentationCenter="" authors="ghogen, kempb" />

<tags ms.service="storage" ms.workload="web" ms.tgt_pltfrm="vs-what-happened" ms.devlang="na" ms.topic="article" ms.date="10/10/2014" ms.author="ghogen, kempb" />

> [AZURE.SELECTOR]
> - [Introducción](/documentation/articles/vs-storage-aspnet-getting-started-blobs/)
> - [¿Qué ha ocurrido?](/documentation/articles/vs-storage-aspnet-what-happened/)

###<span id="whathappened">¿Qué le ha ocurrido a mi proyecto?</span>

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

#####Se ha agregado la cadena de conexión para Almacenamiento de Azure. 
En el archivo web.config de su proyecto, se ha creado un elemento con la cadena y la clave de conexión de la cuenta de almacenamiento seleccionada.

Para obtener más información, consulte [ASP.NET](http://www.asp.net).
