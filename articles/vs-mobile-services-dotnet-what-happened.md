<properties 
	pageTitle="" 
	description="" 
	services="mobile-services" 
	documentationCenter="" 
	authors="kempb" 
	manager="douge" 
	editor=""/>

<tags 
	ms.service="mobile-services" 
	ms.workload="web" 
	ms.tgt_pltfrm="vs-what-happened" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="10/8/2014" 
	ms.author="kempb"/>

> [AZURE.SELECTOR]
> - [Introducción](vs-mobile-services-dotnet-getting-started.md)
> - [¿Qué ha ocurrido?](vs-mobile-services-dotnet-what-happened.md)

###<span id="whathappened">¿Qué le ha ocurrido a mi proyecto?</span>

#####Se han agregado referencias.

El paquete de NuGet de Servicios móviles de Azure se agregó a su proyecto. Como resultado, se agregaron las siguientes referencias de .NET a su proyecto:

- `Microsoft.WindowsAzure.Mobile`
- `Microsoft.WindowsAzure.Mobile.Ext`
- `Newtonsoft.Json`
- `System.Net.Http.Extensions`
- `System.Net.Http.Primitives` 

#####Conexión de valores de cadena para Servicios móviles

En el archivo App.xaml.cs, se ha creado un objeto **MobileServiceClient** con la dirección URL y la clave de aplicación del servicio móvil seleccionado. 

[Más información acerca de Servicios móviles](http://azure.microsoft.com/documentation/services/mobile-services/)




<!--HONumber=42-->
