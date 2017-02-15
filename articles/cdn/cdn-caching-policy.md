---
title: "Directiva de almacenamiento en caché de CDN en extensión de Servicios multimedia"
description: "En este tema se proporciona información general sobre una directiva de almacenamiento en caché de CDN en extensión de Media Services."
services: media-services,cdn
documentationcenter: .NET
author: juliako
manager: erikre
editor: 
ms.assetid: be33aecc-6dbe-43d7-a056-10ba911e0e94
ms.service: media-services
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/19/2016
ms.author: juliako
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: d8e5fa4f3de86be0a8ef65971e42c8b20f61f0b2


---
# <a name="cdn-caching-policy-in-media-services-extension"></a>Directiva de almacenamiento en caché de CDN en extensión de Servicios multimedia
Servicios multimedia de Azure proporciona streaming adaptable basado en HTTP y descarga progresiva. El streaming basado en HTTP es altamente escalable con ventajas de almacenamiento en caché en proxy y capas CDN, además de almacenamiento en caché del cliente. Los extremos de streaming proporcionan capacidades generales de streaming, así como configuración para encabezados de caché HTTP. Los extremos de streaming establecen Control de caché HTTP: encabezados max-age y Expires. Puede obtener más información sobre los encabezados de caché HTTP en [W3.org](http://www.w3.org/Protocols/rfc2616/rfc2616-sec13.html).

## <a name="default-caching-headers"></a>Encabezados de almacenamiento en caché predeterminados
De forma predeterminada, los extremos de streaming aplican encabezados de caché de 3 días para datos de streaming a petición (fragmentos/segmentos multimedia reales) y manifest(playlist). Para streaming en directo, los extremos de streaming aplican encabezados de caché de 3 días para datos (fragmentos/segmentos multimedia reales) y encabezados de caché de 2 segundos para solicitudes de manifest(playlist). Cuando un programa en directo pasa a ser a petición (archivo directo), se aplican encabezados de caché de streaming a petición.

## <a name="azure-cdn-integration"></a>Integración de CDN de Azure
Servicios multimedia de Azure proporciona una [red CDN integrada](https://azure.microsoft.com/updates/azure-media-services-now-fully-integrated-with-azure-cdn/) para puntos de conexión de streaming. Los encabezados Cache-control se aplican de la misma manera que los extremos de streaming a extremos de streaming habilitados para CDN. CDN de Azure usa valores de caché configurados de extremo de streaming para definir el tiempo de duración de los objetos almacenados en caché internamente y también utiliza este valor para establecer encabezados de caché de entrega. Cuando se utilizan extremos de streaming habilitados para CDN, no se recomienda establecer valores de caché pequeños. Si se establecen valores pequeños, se disminuye el rendimiento y se reducen las ventajas de CDN. No se permite establecer encabezados de caché menores de 600 segundos para extremos de streaming habilitados para CDN.

> [!IMPORTANT]
> La integración de Servicios multimedia de Azure con la red CDN de Azure se implementa en la **red CDN de Azure de Verizon**.  Si quiere usar la **red CDN de Azure de Akamai** para Servicios multimedia de Azure, debe [configurar el punto de conexión manualmente](cdn-create-new-endpoint.md).  Para más información acerca de las características de la red CDN de Azure, consulte la [información general de la red CDN](cdn-overview.md).
> 
> 

## <a name="configuring-cache-headers-with-azure-media-services"></a>Configuración de encabezados de caché con Servicios multimedia de Azure
Puede usar el Portal de administración de Azure o las API de Servicios multimedia de Azure para configurar valores de encabezados de caché.

1. Para configurar encabezados de caché mediante el Portal de administración, consulte [Administración de extremos de streaming](../media-services/media-services-portal-manage-streaming-endpoints.md) , sección Configuración del extremo de streaming.
2. API de REST de Servicios multimedia de Azure, [StreamingEndpoint](https://msdn.microsoft.com/library/azure/dn783468.aspx#StreamingEndpointCacheControl).
3. .NET SDK de Servicios multimedia de Azure, [Propiedades de StreamingEndpointCacheControl](http://go.microsoft.com/fwlink/?LinkId=615302).

## <a name="cache-configuration-precedence-order"></a>Orden de prioridad de configuración de caché
1. El valor de caché configurado de Servicios multimedia de Azure reemplaza el valor predeterminado.
2. Si no hay ninguna configuración manual, se aplican los valores predeterminados.
3. De forma predeterminada, se aplican encabezados de caché de 2 segundos para manifest(playlist) de streaming en directo independientemente de la configuración multimedia o de almacenamiento de Azure y la anulación de este valor no está disponible.




<!--HONumber=Nov16_HO3-->


