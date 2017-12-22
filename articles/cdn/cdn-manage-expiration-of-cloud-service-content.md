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
ms.openlocfilehash: dca6ca5f21f4a4f1701af57eb40d92094b6a4754
ms.sourcegitcommit: b5c6197f997aa6858f420302d375896360dd7ceb
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/21/2017
---
# <a name="manage-expiration-of-web-content-in-azure-content-delivery-network"></a>Administrar la caducidad de contenido web en Azure Content Delivery Network
> [!div class="op_single_selector"]
> * [Contenido web de Azure](cdn-manage-expiration-of-cloud-service-content.md)
> * [Almacenamiento de blobs de Azure](cdn-manage-expiration-of-blob-content.md)
> 

Los archivos de cualquier servidor web de origen accesible públicamente se pueden almacenar en caché en Azure Content Delivery Network (CDN) hasta que transcurra su período de vida (TTL). El período de vida viene determinado por el encabezado `Cache-Control` en la respuesta HTTP del servidor de origen. En este artículo se describe cómo establecer los encabezados `Cache-Control` para la característica Web Apps de Microsoft Azure App Service, Azure Cloud Services, las aplicaciones de ASP.NET y los sitios de Internet Information Services (IIS), ya que todos ellos se configuran de forma parecida. Puede establecer el encabezado `Cache-Control` mediante el uso de archivos de configuración o mediante programación. 

También puede controlar la configuración de caché desde Azure Portal estableciendo [reglas de almacenamiento en caché de la red CDN](cdn-caching-rules.md). Si configura una o más reglas de almacenamiento en caché y establece el comportamiento de dicho almacenamiento en **Invalidar** u **Omitir caché**, se omite la configuración de almacenamiento en caché proporcionada por el origen que se trata en este artículo. Para información sobre conceptos generales de almacenamiento en caché, consulte [Funcionamiento del almacenamiento en caché](cdn-how-caching-works.md).

> [!TIP]
> Puede optar por no configurar ningún TTL en un archivo. En este caso, Azure CDN aplica automáticamente un TTL predeterminado de siete días, a menos que haya configurado reglas de almacenamiento en caché en Azure Portal. Este TTL predeterminado solo se aplica a las optimizaciones de entrega web general. Para las optimizaciones de archivos de gran tamaño, el TTL predeterminado es un día, y para las optimizaciones de streaming multimedia, el TTL predeterminado es un año.
> 
> Para obtener más información sobre el funcionamiento de Azure CDN para acelerar el acceso a los archivos y a otros recursos, consulte [Información general de Azure Content Delivery Network](cdn-overview.md).
> 

## <a name="setting-cache-control-headers-by-using-configuration-files"></a>Establecimiento de los encabezados Cache-Control mediante archivos de configuración
Para el contenido estático, como imágenes y hojas de estilos, puede controlar la frecuencia de actualización al modificar los archivos de configuración **applicationHost.config** o **Web.config** de la aplicación web. El elemento `<system.webServer>/<staticContent>/<clientCache>` de cada archivo establece el encabezado `Cache-Control` para su contenido.

### <a name="using-applicationhostconfig-files"></a>Uso de archivos ApplicationHost.config
El archivo **ApplicationHost.config** es el archivo raíz del sistema de configuración de IIS. El valor de la configuración del archivo **ApplicationHost.config** afecta a todas las aplicaciones del sitio, pero se reemplaza por la configuración de cualquier archivo **Web.config** existente en una aplicación web.

### <a name="using-webconfig-files"></a>Uso de archivos Web.config
Con un archivo **Web.config**, puede personalizar el comportamiento de toda la aplicación web o de un directorio específico en la aplicación web. Por lo general, tiene al menos un archivo **Web.config** en la carpeta raíz de la aplicación web. Para cada archivo **Web.config** de una carpeta específica, el valor de la configuración afecta a todo lo que haya en dicha carpeta y en todas sus subcarpetas, a menos que se invaliden en el nivel de subcarpeta por otro archivo **Web.config**. Por ejemplo, puede establecer un elemento `<clientCache>` de un archivo **Web.config** en la carpeta raíz de la aplicación web para almacenar en caché todo el contenido estático en la aplicación web durante tres días. También puede agregar un archivo **Web.config** a una subcarpeta con contenido más variable (por ejemplo, `\frequent`) y establecer el elemento `<clientCache>` para almacenar en caché contenido de la subcarpeta durante seis horas. El resultado neto es que el contenido de todo el sitio web se almacenará en caché durante tres días, excepto el contenido del directorio `\frequent`, que se almacenará en caché solo durante seis horas.  

En el ejemplo de XML siguiente se muestra como establecer el elemento `<clientCache>` de un archivo de configuración para especificar una antigüedad máxima de tres días:  

```xml
<configuration>
    <system.webServer>
        <staticContent>
            <clientCache cacheControlMode="UseMaxAge" cacheControlMaxAge="3.00:00:00" />
        </staticContent>
    </system.webServer>
</configuration>
```

Para usar el atributo **cacheControlMaxAge**, debe establecer el valor del atributo **cacheControlMode** en `UseMaxAge`. Esta configuración hace que el encabezado HTTP y la directiva, `Cache-Control: max-age=<nnn>`, se agreguen a la respuesta. El formato del valor del intervalo de tiempo para el atributo **cacheControlMaxAge** es `<days>.<hours>:<min>:<sec>`. Su valor se convierte en segundos y se utiliza como el valor de la directiva `Cache-Control` `max-age`. Para más información sobre el elemento `<clientCache>`, consulte el elemento [Client Cache<clientCache>](http://www.iis.net/ConfigReference/system.webServer/staticContent/clientCache).  

## <a name="setting-cache-control-headers-programmatically"></a>Establecimiento de los encabezados Cache-Control mediante programación
Para las aplicaciones de ASP.NET, puede controlar el comportamiento de almacenamiento en caché de CDN mediante programación al establecer la propiedad **HttpResponse.Cache** de la API de .NET. Para más información sobre la propiedad **HttpResponse.Cache**, consulte [Propiedad HttpResponse.Cache](http://msdn.microsoft.com/library/system.web.httpresponse.cache.aspx) y [Clase HttpCachePolicy](http://msdn.microsoft.com/library/system.web.httpcachepolicy.aspx).  

Para almacenar en caché mediante programación el contenido de la aplicación de ASP.NET, siga estos pasos:
   1. Compruebe que el contenido esté marcado como almacenable en caché. Para ello, establezca `HttpCacheability` en `Public`. 
   2. Establezca un validador de caché mediante una llamada a uno de los métodos siguientes `HttpCachePolicy`:
      - Llame a `SetLastModified` para establecer un valor de marca de tiempo para el encabezado `Last-Modified`.
      - Llame a `SetETag` para establecer un valor para el encabezado `ETag`.
   3. Si lo desea, especifique una hora de expiración de caché mediante una llamada a `SetExpires` para establecer un valor para el encabezado `Expires`. En caso contrario, se aplica la heurística de caché predeterminada descrita anteriormente en este documento.

Por ejemplo, para almacenar en caché el contenido durante una hora, agregue el siguiente código de C#:  

```csharp
// Set the caching parameters.
Response.Cache.SetExpires(DateTime.Now.AddHours(1));
Response.Cache.SetCacheability(HttpCacheability.Public);
Response.Cache.SetLastModified(DateTime.Now);
```

## <a name="testing-the-cache-control-header"></a>Prueba del encabezado Cache-Control
Puede comprobar fácilmente la configuración de TTL de su contenido web. Con las [herramientas para desarrollador](https://developer.microsoft.com/microsoft-edge/platform/documentation/f12-devtools-guide/) del explorador, compruebe que el contenido web incluye el encabezado de respuesta `Cache-Control`. Asimismo, también puede usar una herramienta como **wget**, [Postman](https://www.getpostman.com/) o [Fiddler](http://www.telerik.com/fiddler) para examinar los encabezados de respuesta.

## <a name="next-steps"></a>Pasos siguientes
* [Obtener información sobre el elemento **clientCache**](http://www.iis.net/ConfigReference/system.webServer/staticContent/clientCache)
* [Lea la documentación de la propiedad **HttpResponse.Cache**](http://msdn.microsoft.com/library/system.web.httpresponse.cache.aspx) 
* [Consulte la documentación de la **clase HttpCachePolicy**](http://msdn.microsoft.com/library/system.web.httpcachepolicy.aspx)  
* [Obtenga información sobre conceptos del almacenamiento en caché](cdn-how-caching-works.md)
