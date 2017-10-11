---
title: "Administración de la expiración del contenido web en la red CDN de Azure | Microsoft Docs"
description: Aprenda a administrar la caducidad del contenido de Azure Web Apps/Cloud Services, ASP.NET o IIS en la red CDN de Azure.
services: cdn
documentationcenter: .NET
author: zhangmanling
manager: erikre
editor: 
ms.assetid: bef53fcc-bb13-4002-9324-9edee9da8288
ms.service: cdn
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 01/23/2017
ms.author: mazha
ms.openlocfilehash: c207d780857a61d4b1fc0f39e6185cae67abc955
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 07/11/2017
---
# <a name="manage-expiration-of-azure-web-appscloud-services-aspnet-or-iis-content-in-azure-cdn"></a>Administración de la caducidad del contenido de Azure Web Apps/Cloud Services, ASP.NET o IIS en la red CDN de Azure
> [!div class="op_single_selector"]
> * [Azure Web Apps/Cloud Services, ASP.NET o IIS](cdn-manage-expiration-of-cloud-service-content.md)
> * [Azure Storage Blob service](cdn-manage-expiration-of-blob-content.md)
> 
> 

Los archivos de cualquier servidor web de origen accesible públicamente se pueden almacenar en caché en la red CDN de Azure hasta que transcurra su tiempo de vida (TTL).  El período de vida viene determinado por el [encabezado *Cache-Control*](http://www.w3.org/Protocols/rfc2616/rfc2616-sec14.html#sec14.9) en la respuesta HTTP del servidor de origen.  Este artículo describe cómo establecer los encabezados `Cache-Control` para Azure Web Apps, Azure Cloud Services, las aplicaciones de ASP.NET y los sitios de Internet Information Services ya que todos ellos se configuran de forma parecida.

> [!TIP]
> Puede optar por no configurar ningún tiempo de vida en un archivo.  En este caso, la red CDN de Azure aplica automáticamente un valor predeterminado de TTL de siete días.
> 
> Para más información sobre el funcionamiento de la red CDN de Azure para acelerar el acceso a los archivos y otros recursos, vea [Información general de la red de entrega de contenido (CDN) de Azure](cdn-overview.md).
> 
> 

## <a name="setting-cache-control-headers-in-configuration"></a>Establecimiento de los encabezados Cache-Control en la configuración
Para el contenido estático, como imágenes y hojas de estilos, puede controlar la frecuencia de actualización modificando los archivos **applicationHost.config** o **web.config** de la aplicación web.  El elemento **system.webServer\staticContent\clientCache** del archivo de configuración establecerá el encabezado `Cache-Control` para el contenido. Para **web.config**, la configuración afectará a todo lo que contenga la carpeta y todas las subcarpetas, a menos que se esta se invalide en el nivel de subcarpeta.  Por ejemplo, puede establecer un período de tiempo de vida en la raíz para tener todo el contenido estático almacenado en caché durante 3 días, pero tiene una subcarpeta que tiene más contenido variable con una configuración de caché de 6 horas.  En el caso de **applicationHost.config**, se verán afectadas todas las aplicaciones del sitio, pero se puede invalidar la configuración en los archivos **web.config** de las aplicaciones.

El siguiente código XML muestra un ejemplo de configuración **clientCache** para especificar una edad máxima de 3 días:  

```xml
<configuration>
    <system.webServer>
        <staticContent>
            <clientCache cacheControlMode="UseMaxAge" cacheControlMaxAge="3.00:00:00" />
        </staticContent>
    </system.webServer>
</configuration>
```

La especificación de **UseMaxAge** agrega un encabezado `Cache-Control: max-age=<nnn>` a la respuesta basándose en el valor especificado en el atributo **CacheControlMaxAge**. El formato del intervalo de tiempo para el atributo **cacheControlMaxAge** es <days>.<hours>:<min>:<sec>. Para más información sobre el nodo **clientCache**, vea [Caché de cliente<clientCache>](http://www.iis.net/ConfigReference/system.webServer/staticContent/clientCache).  

## <a name="setting-cache-control-headers-in-code"></a>Establecimiento de los encabezados Cache-Control en el código
Para las aplicaciones de ASP.NET, puede establecer el comportamiento de almacenamiento en caché CDN mediante programación estableciendo la propiedad **HttpResponse.Cache** . Para más información sobre la propiedad **HttpResponse.Cache**, vea [Propiedad HttpResponse.Cache](http://msdn.microsoft.com/library/system.web.httpresponse.cache.aspx) y [Clase HttpCachePolicy](http://msdn.microsoft.com/library/system.web.httpcachepolicy.aspx).  

Si desea almacenar en caché el contenido de la aplicación en ASP.NET mediante programación, asegúrese de que dicho contenido está marcado como almacenable en caché estableciendo HttpCacheability en *Public*. Asimismo, asegúrese de que se ha establecido un validador de caché. El validador de caché puede ser un intervalo de tiempo de última modificación establecido llamando a SetLastModified, o un valor de etag establecido llamando a SetETag. Opcionalmente, también puede especificar un tiempo de expiración de caché llamando a SetExpires, o puede basarse en la heurística de caché predeterminada descrita anteriormente en este documento.  

Por ejemplo, para almacenar en caché el contenido durante una hora, agregue lo siguiente:  

```csharp
// Set the caching parameters.
Response.Cache.SetExpires(DateTime.Now.AddHours(1));
Response.Cache.SetCacheability(HttpCacheability.Public);
Response.Cache.SetLastModified(DateTime.Now);
```

## <a name="next-steps"></a>Pasos siguientes
* [Obtener información sobre el elemento **clientCache**](http://www.iis.net/ConfigReference/system.webServer/staticContent/clientCache)
* [Lea la documentación de la propiedad **HttpResponse.Cache**](http://msdn.microsoft.com/library/system.web.httpresponse.cache.aspx) 
* [Lea la documentación de la **clase HttpCachePolicy**](http://msdn.microsoft.com/library/system.web.httpcachepolicy.aspx).  

