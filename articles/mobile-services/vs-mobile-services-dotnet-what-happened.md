<properties 
	pageTitle="" 
	description="Describe lo que le ha ocurrido al proyecto .NET de Servicios móviles de Azure en Visual Studio" 
	services="mobile-services" 
	documentationCenter="" 
	authors="patshea123" 
	manager="douge" 
	editor=""/>

<tags 
	ms.service="mobile-services" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="NA" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="07/02/2015" 
	ms.author="patshea123"/>

# ¿Qué le ha ocurrido a mi proyecto?

> [AZURE.SELECTOR]
> - [Getting Started](vs-mobile-services-dotnet-getting-started.md)
> - [What Happened](vs-mobile-services-dotnet-what-happened.md)

###¿Qué le ha ocurrido a mi proyecto?</span>

#####Se han agregado referencias.

El paquete de NuGet de Servicios móviles de Azure se agregó a su proyecto. Como resultado, se agregaron las siguientes referencias de .NET a su proyecto:

- `Microsoft.WindowsAzure.Mobile`
- `Microsoft.WindowsAzure.Mobile.Ext`
- `Newtonsoft.Json`
- `System.Net.Http.Extensions`
- `System.Net.Http.Primitives` 

#####Conexión de valores de cadena para Servicios móviles

En el archivo App.xaml.cs, se ha creado un objeto **MobileServiceClient** con la dirección URL y la clave de aplicación del servicio móvil seleccionado.

####Proyecto de Servicios móviles agregado

Si se crea un servicio móvil .NET en el proveedor de servicios conectado, se crea un proyecto de servicios móviles y se agrega a la solución.


[Más información acerca de Servicios móviles](http://azure.microsoft.com/documentation/services/mobile-services/)

<!---HONumber=July15_HO5-->