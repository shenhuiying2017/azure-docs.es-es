<properties 
	pageTitle="Uso del backend .NET de Servicios móviles - Servicios móviles de Azure" 
	description="Obtener información acerca de los detalles del modelo de programación de back-end de .NET de Servicios móviles de Azure, incluida la manera de trabajar con datos de tabla, las API, la autenticación y los trabajos programados" 
	services="" 
	documentationCenter="windows" 
	authors="mattchenderson" 
	manager="dwrede" 
	editor="mollybos"/>

<tags 
	ms.service="mobile-services" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-multiple" 
	ms.devlang="multiple" 
	ms.topic="article" 
	ms.date="11/11/2014" 
	ms.author="mahender"/>
# Uso del backend .NET de Servicios móviles

<div class="dev-center-tutorial-subselector"><a href="/es-es/documentation/articles/mobile-services-dotnet-backend-how-to-use/" title=".NET backend" class="current">Back-end de .NET</a> | <a href="/es-es/documentation/articles/mobile-services-how-to-use-server-scripts/"  title="JavaScript backend">Back-end de JavaScript</a></div>

Este artículo proporciona información detallada y ejemplos sobre cómo usar el backend .NET de Servicios móviles de Azure. Este tema está dividido en las siguientes secciones:

+ [Introducción](#intro)
+ [Operaciones de tabla](#table-scripts)

##<a name="intro"></a>Introducción

El back-end de .NET de Servicios móviles le permite escribir lógica empresarial de back-end completa mediante [ASP.NET Web API](http://www.asp.net/web-api) y su lenguaje .NET favorito. Los Servicios móviles exponen una pequeña superficie de modelo de programación como [paquetes NuGet](http://www.nuget.org/packages?q=%22mobile+services+.net+backend%22), lo que permite el acceso sin problemas al servicio desde los SDK de cliente de Servicios móviles multiplataforma para Windows, Android, iOS, etc. Estas API garantizan también que la incorporación de autenticación y notificaciones de inserción sea lo más sencilla posible. Sin embargo, en su mayor parte, el modelo de programación se basa en Web API y los desarrolladores que ya la conocen se sentirán como en casa. 

##<a name="table-scripts"></a>Operaciones de tabla

El backend .NET de Servicios móviles proporciona una abstracción de "tabla" universal, que representa una API HTTP basada en CRUD para el almacenamiento en bases de datos. Esta abstracción aleja la preocupación del almacenamiento de datos de su lógica empresarial y permite que el servicio móvil exponga diferentes almacenes de datos con un formato de envío coherente basado en JSON que comprenden fácilmente los SDK de cliente de Servicios móviles multiplataforma. 

En la raíz de este modelo de programación se encuentra la clase [**TableController<T>**](http://msdn.microsoft.com/library/dn643359.aspx), que es simplemente un [**ApiController**](http://msdn.microsoft.com/library/system.web.http.apicontroller.aspx) de API web que está personalizado para un modelo de acceso a datos basado en CRUD. El **TableController** puede usar una amplia variedad de almacenes de datos, como SQL (a través de [Entity Framework](http://msdn.microsoft.com/data/ef.aspx)), [almacenamiento de tablas de Azure](http://azure.microsoft.com/documentation/services/storage/), [MongoDB](http://www.mongodb.org) o su propio almacén personalizado.

<!--HONumber=42-->
