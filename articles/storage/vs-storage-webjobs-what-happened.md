<properties
	pageTitle="¿Qué le ha ocurrido a mi proyecto WebJob (servicio conectado a Almacenamiento de Azure de Visual Studio)? | Microsoft Azure"
	description="Describe lo que ha ocurrido en un proyecto WebJob de Azure después de conectarse a una cuenta de almacenamiento de Azure mediante los servicios conectados de Visual Studio" 
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
	ms.date="09/03/2015"
	ms.author="patshea"/>

# ¿Qué le ha ocurrido a mi proyecto WebJob (servicio conectado a Almacenamiento de Azure de Visual Studio)?

> [AZURE.SELECTOR]
> - [Getting Started](vs-storage-webjobs-getting-started-blobs.md)
> - [What Happened](vs-storage-webjobs-what-happened.md)


## Se han agregado referencias

El paquete de NuGet de Almacenamiento de Azure se agregó al proyecto de Visual Studio o se actualizó en él. Este paquete agrega las siguientes referencias. NET:

- **Microsoft.Data.Edm**
- **Microsoft.Data.OData**
- **Microsoft.Data.Services.Client**
- **Microsoft.WindowsAzure.ConfigurationManager**
- **Microsoft.WindowsAzure.Storage**
- **Newtonsoft.Json**
- **System.Data**
- **System.Spatial**

## Se ha agregado la cadena de conexión para Almacenamiento de Azure.
En el archivo App.config del proyecto, se actualizaron las entradas **AzureWebJobsStorage** y **AzureWebJobsDashboard** con la cadena y la clave de conexión de la cuenta de almacenamiento seleccionada.

Para obtener más información, consulte [Recursos recomendados de WebJobs de Azure](http://go.microsoft.com/fwlink/?linkid=390226).

<!---HONumber=Sept15_HO4-->