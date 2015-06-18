<properties 
	pageTitle="Introducción a Almacenamiento de Azure" 
	description="Describe lo que ha ocurrido al crear un almacenamiento de Azure en un proyecto de servicio en la nube de Visual Studio" 
	services="storage" 
	documentationCenter="" 
	authors="kempb" 
	manager="douge" 
	editor="tglee"/>

<tags 
	ms.service="storage" 
	ms.workload="web" 
	ms.tgt_pltfrm="vs-what-happened" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="02/02/2015" 
	ms.author="kempb"/>

> [AZURE.SELECTOR]
> - [Introducción](vs-storage-cloud-services-getting-started-blobs.md)
> - [¿Qué ha ocurrido?](vs-storage-cloud-services-what-happened.md)

###<span id="whathappened">¿Qué le ha ocurrido a mi proyecto?</span>

###### Se han agregado referencias.

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

######Se ha agregado la cadena de conexión para Almacenamiento de Azure. 
Se han creado elementos con la cadena y la clave de conexión de la cuenta de almacenamiento seleccionada. Se han hecho modificaciones en los archivos siguientes:

- `ServiceDefinition.csdef`
- `ServiceConfiguration.Cloud.cscfg`
- `ServiceConfiguration.Local.cscfg`


<!--HONumber=46--> 
 