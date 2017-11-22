---
title: Administrar la caducidad de contenido web en Azure Content Delivery Network | Microsoft Docs
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
ms.date: 11/10/2017
ms.author: mazha
ms.openlocfilehash: d58a245923242b3963b188ca869e8290d999c0a2
ms.sourcegitcommit: 659cc0ace5d3b996e7e8608cfa4991dcac3ea129
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/13/2017
---
# <a name="manage-expiration-of-web-content-in-azure-content-delivery-network"></a>Administrar la caducidad de contenido web en Azure Content Delivery Network
 en la red CDN de Azure
> [!div class="op_single_selector"]
> * [Azure Web Apps/Cloud Services, ASP.NET o IIS](cdn-manage-expiration-of-cloud-service-content.md)
> * [Almacenamiento de blobs de Azure](cdn-manage-expiration-of-blob-content.md)
> 

Los archivos de cualquier servidor web de origen accesible públicamente se pueden almacenar en caché en Azure Content Delivery Network (CDN) hasta que transcurra su tiempo de vida (TTL). El período de vida viene determinado por el [encabezado `Cache-Control`](http://www.w3.org/Protocols/rfc2616/rfc2616-sec14.html#sec14.9) en la respuesta HTTP del servidor de origen. En este artículo se describe cómo establecer los encabezados `Cache-Control` para la característica Web Apps de Microsoft Azure App Service, Azure Cloud Services, las aplicaciones de ASP.NET y los sitios de Internet Information Services, ya que todos ellos se configuran de forma parecida.

> [!TIP]
> Puede optar por no configurar ningún TTL en un archivo. En este caso, la red CDN de Azure aplica automáticamente un valor predeterminado de TTL de siete días.
> 
> Para obtener más información sobre el funcionamiento de Azure CDN para acelerar el acceso a los archivos y a otros recursos, consulte [Información general de Azure Content Delivery Network](cdn-overview.md).
> 

## <a name="setting-cache-control-headers-in-configuration-files"></a>Establecimiento de los encabezados Cache-Control en los archivos de configuración
Para el contenido estático, como imágenes y hojas de estilos, puede controlar la frecuencia de actualización modificando los archivos **applicationHost.config** o **web.config** de la aplicación web. El elemento **system.webServer\staticContent\clientCache** del archivo de configuración establece el encabezado `Cache-Control` para el contenido. Para los archivos **web.config**, la configuración afecta a todo lo que contenga la carpeta y todas las subcarpetas, a menos que estas se invaliden en el nivel de subcarpeta. Por ejemplo, puede establecer un valor de TTL predeterminado en la carpeta raíz para almacenar en caché todo el contenido estático durante tres días y establecer una subcarpeta con contenido más variable para almacenar en caché su contenido durante solo seis horas. Aunque la configuración del archivo **applicationHost.config** afecta a todas las aplicaciones en el sitio, se reemplaza por la configuración de cualquier archivo **web.config** existente en las aplicaciones.

En el ejemplo de XML siguiente se muestra como establecer **clientCache** para especificar una edad máxima de 3 días:  

```xml
<configuration>
    <system.webServer>
        <staticContent>
            <clientCache cacheControlMode="UseMaxAge" cacheControlMaxAge="3.00:00:00" />
        </staticContent>
    </system.webServer>
</configuration>
```

La especificación de **UseMaxAge** causa la adición de un encabezado `Cache-Control: max-age=<nnn>` a la respuesta basado en el valor especificado en el atributo **CacheControlMaxAge**. El formato del intervalo de tiempo para el atributo **cacheControlMaxAge** es `<days>.<hours>:<min>:<sec>`. Para obtener más información sobre el nodo **clientCache**, consulte [Client Cache <clientCache>(Caché de cliente) ](http://www.iis.net/ConfigReference/system.webServer/staticContent/clientCache).  

## <a name="setting-cache-control-headers-in-code"></a>Establecimiento de los encabezados Cache-Control en el código
Para las aplicaciones de ASP.NET, puede establecer la propiedad **HttpResponse.Cache** para controlar el comportamiento de almacenamiento en caché CDN mediante programación. Para obtener más información sobre la propiedad **HttpResponse.Cache**, consulte [Propiedad HttpResponse.Cache](http://msdn.microsoft.com/library/system.web.httpresponse.cache.aspx) y [Clase HttpCachePolicy](http://msdn.microsoft.com/library/system.web.httpcachepolicy.aspx).  

Para almacenar en caché mediante programación el contenido de la aplicación de ASP.NET, siga estos pasos:
   1. Compruebe que el contenido esté marcado como almacenable en caché. Para ello, establezca `HttpCacheability` en *Público*. 
   2. Establezca un validador de caché mediante una llamada a uno de los métodos siguientes:
      - Llame a `SetLastModified` para establecer una marca de tiempo LastModified.
      - Llame a `SetETag` para establecer un valor de `ETag`.
   3. Si lo desea, especifique una hora de expiración de caché mediante una llamada a `SetExpires`. En caso contrario, se aplica la heurística de caché predeterminada descrita anteriormente en este documento.

Por ejemplo, para almacenar en caché el contenido durante una hora, agregue el siguiente código de C#:  

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

