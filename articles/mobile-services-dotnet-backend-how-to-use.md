<properties linkid="mobile-services-dotnet-backend-how-to-use" urlDisplayName="Use the Mobile Services .NET Backend" pageTitle="Use the Mobile Services .NET Backend - Azure Mobile Services" metaKeywords="" description="Learn the details of the .NET Backend programming model for Azure Mobile Services, including how to work with table data, APIs, authentication, and scheduled jobs" metaCanonical="" services="" documentationCenter="Mobile" title="Use the Mobile Services .NET Backend" authors="yavorg" solutions="" manager="" editor="mollybos" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-multiple" ms.devlang="multiple" ms.topic="article" ms.date="01/01/1900" ms.author="yavorg"></tags>

# Uso del backend .NET de Servicios móviles

<div class="dev-center-tutorial-subselector"><a href="/es-es/documentation/articles/mobile-services-dotnet-backend-how-to-use/" title="Back-end de .NET" class="current">Back-end de .NET</a> | <a href="/es-es/documentation/articles/mobile-services-how-to-use-server-scripts/"  title="Back-end de JavaScript">Back-end de JavaScript</a></div>

Este artículo proporciona información detallada y ejemplos sobre cómo usar el backend .NET de Servicios móviles de Azure. Este tema está dividido en las siguientes secciones:

-   [Introducción][]
-   [Operaciones de tabla][]

## <a name="intro"></a>Introducción

El backend .NET de Servicios móviles permite escribir lógica empresarial de backend completa usando [ASP.NET Web API][] y su lenguaje .NET favorito. Servicios móviles expone una pequeña superficie de modelo de programación como [paquetes NuGet][], que permite el acceso sin problemas al servicio desde los SDK de cliente de Servicios móviles multiplataforma para Windows, Android, iOS, etc. Estas API garantizan también que la incorporación de autenticación y notificaciones de inserción sea lo más sencilla posible. Sin embargo, en su mayor parte, el modelo de programación se basa en Web API y los desarrolladores que ya la conocen se sentirán como en casa.

## <a name="table-scripts"></a>Operaciones de tabla

El backend .NET de Servicios móviles proporciona una abstracción de "tabla" universal, que representa una API HTTP basada en CRUD para el almacenamiento en bases de datos. Esta abstracción aleja la preocupación del almacenamiento de datos de su lógica empresarial y permite que el servicio móvil exponga diferentes almacenes de datos con un formato de envío coherente basado en JSON que comprenden fácilmente los SDK de cliente de Servicios móviles multiplataforma.

En la raíz de este modelo de programación se encuentra la clase [**TableController<t>**][], que es simplemente un [**ApiController**][] normal de Web API que está personalizado para un modelo de acceso a datos basado en CRUD. El **TableController** puede usar una gran variedad de almacenes de datos, incluidos SQL (a través de [Entity Framework][]), [Almacenamiento de tablas de Azure][], [MongoDB][] o su propio almacén de datos.

  [Back-end de .NET]: /es-es/documentation/articles/mobile-services-dotnet-backend-how-to-use/ "Back-end de .NET"
  [Back-end de JavaScript]: /es-es/documentation/articles/mobile-services-how-to-use-server-scripts/ "Back-end de JavaScript"
  [Introducción]: #intro
  [Operaciones de tabla]: #table-scripts
  [ASP.NET Web API]: http://www.asp.net/web-api
  [paquetes NuGet]: http://www.nuget.org/packages?q=%22mobile+services+.net+backend%22
  [**TableController<t>**]: http://msdn.microsoft.com/library/dn643359.aspx
  [**ApiController**]: http://msdn.microsoft.com/library/system.web.http.apicontroller.aspx
  [Entity Framework]: http://msdn.microsoft.com/data/ef.aspx
  [Almacenamiento de tablas de Azure]: http://azure.microsoft.com/documentation/services/storage/
  [MongoDB]: http://www.mongodb.org
