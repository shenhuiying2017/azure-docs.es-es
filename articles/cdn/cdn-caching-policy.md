---
title: "Administración de la directiva de almacenamiento en caché de la red CDN de Azure en Azure Media Services | Microsoft Docs"
description: "Aprenda a administrar la directiva de almacenamiento en caché de la red CDN de Azure en Azure Media Services."
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
ms.date: 02/04/2017
ms.author: juliako
translationtype: Human Translation
ms.sourcegitcommit: fba5384acae53ac416d91ae30840c6cfa04e7c68
ms.openlocfilehash: 0c479a58f4158bb1a72dc43432507160f65d2791


---
# <a name="manage-azure-cdn-caching-policy-in-azure-media-services"></a>Administración de la directiva de almacenamiento en caché de la red CDN de Azure en Azure Media Services
Servicios multimedia de Azure proporciona streaming adaptable basado en HTTP y descarga progresiva. El streaming basado en HTTP es altamente escalable con ventajas de almacenamiento en caché en proxy y capas CDN, además de almacenamiento en caché del cliente. Los extremos de streaming proporcionan capacidades generales de streaming, así como configuración para encabezados de caché HTTP. Los extremos de streaming establecen Control de caché HTTP: encabezados max-age y Expires. Puede obtener más información sobre los encabezados de caché HTTP en [W3.org](http://www.w3.org/Protocols/rfc2616/rfc2616-sec13.html).

## <a name="default-caching-headers"></a>Encabezados de almacenamiento en caché predeterminados
De forma predeterminada, los extremos de streaming aplican encabezados de caché de 3 días para datos de streaming a petición (fragmentos/segmentos multimedia reales) y manifest(playlist). Para streaming en directo, los extremos de streaming aplican encabezados de caché de 3 días para datos (fragmentos/segmentos multimedia reales) y encabezados de caché de 2 segundos para solicitudes de manifest(playlist). Cuando un programa en directo pasa a ser a petición (archivo directo), se aplican encabezados de caché de streaming a petición.

## <a name="azure-cdn-integration"></a>Integración de CDN de Azure
Servicios multimedia de Azure proporciona una [red CDN integrada](https://azure.microsoft.com/updates/azure-media-services-now-fully-integrated-with-azure-cdn/) para puntos de conexión de streaming. Los encabezados Cache-control se aplican de la misma manera que los extremos de streaming a extremos de streaming habilitados para CDN. CDN de Azure usa valores de caché configurados de extremo de streaming para definir el tiempo de duración de los objetos almacenados en caché internamente y también utiliza este valor para establecer encabezados de caché de entrega. Cuando se utilizan extremos de streaming habilitados para CDN, no se recomienda establecer valores de caché pequeños. Si se establecen valores pequeños, se disminuye el rendimiento y se reducen las ventajas de CDN. No se permite establecer encabezados de caché menores de 600 segundos para extremos de streaming habilitados para CDN.

> [!IMPORTANT]
>Azure Media Services está totalmente integrado completa con la red CDN de Azure. Con un solo clic puede integrar todos los proveedores disponibles de la red CDN de Azure (Akamai y Verizon) a su punto de conexión de streaming, incluidos los estándar y Premium de la red CDN. Para más información, consulte este [anuncio](https://azure.microsoft.com/blog/standardstreamingendpoint/).
> 
> Los cargos de datos del punto de conexión de streaming a la red CDN solo se deshabilitan si la red CDN se habilitada a través de las API del punto de conexión de streaming o mediante la sección del punto de conexión de streaming del Portal de administración de Azure. La integración manual o la creación directa de un punto de conexión de CDN mediante las API de CDN o la sección del portal no deshabilitará los cargos de datos.

## <a name="configuring-cache-headers-with-azure-media-services"></a>Configuración de encabezados de caché con Servicios multimedia de Azure
Puede usar el Portal de administración de Azure o las API de Servicios multimedia de Azure para configurar valores de encabezados de caché.

1. Para configurar encabezados de caché mediante el Portal de administración, consulte [Administración de extremos de streaming](../media-services/media-services-portal-manage-streaming-endpoints.md) , sección Configuración del extremo de streaming.
2. API de REST de Servicios multimedia de Azure, [StreamingEndpoint](https://msdn.microsoft.com/library/azure/dn783468.aspx#StreamingEndpointCacheControl).
3. .NET SDK de Servicios multimedia de Azure, [Propiedades de StreamingEndpointCacheControl](http://go.microsoft.com/fwlink/?LinkId=615302).

## <a name="cache-configuration-precedence-order"></a>Orden de prioridad de configuración de caché
1. El valor de caché configurado de Servicios multimedia de Azure reemplaza el valor predeterminado.
2. Si no hay ninguna configuración manual, se aplican los valores predeterminados.
3. De forma predeterminada, se aplican encabezados de caché de 2 segundos para manifest(playlist) de streaming en directo independientemente de la configuración multimedia o de almacenamiento de Azure y la anulación de este valor no está disponible.




<!--HONumber=Feb17_HO1-->


