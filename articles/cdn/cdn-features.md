---
title: Comparación de las características de los productos de Azure Content Delivery Network (CDN) | Microsoft Docs
description: Conozca las características que admite cada producto de Azure Content Delivery Network (CDN).
services: cdn
documentationcenter: ''
author: dksimpson
manager: cfowler
editor: ''
ms.assetid: ''
ms.service: cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: overview
ms.date: 05/09/2018
ms.author: v-deasim
ms.custom: mvc
ms.openlocfilehash: 3368a8a14a3d1314e4c7ecae9256071f1fe646f9
ms.sourcegitcommit: 96089449d17548263691d40e4f1e8f9557561197
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/17/2018
ms.locfileid: "34257876"
---
# <a name="compare-azure-cdn-product-features"></a>Comparación de las características de los productos de Azure CDN

Azure Content Delivery Network (CDN) incluye cuatro productos: **Azure CDN de Microsoft estándar** (en versión preliminar), **Azure CDN de Akamai estándar**, **Azure CDN de Verizon estándar** y **Azure CDN de Verizon premium**. 

En la tabla siguiente se comparan las características disponibles con cada producto.

| **Características y optimizaciones de rendimiento** | **Microsoft estándar (versión preliminar)** | **Akamai estándar** | **Verizon estándar** | **Verizon premium** |
| --- | --- | --- | --- | --- |
| [Aceleración de sitios dinámicos](https://docs.microsoft.com/azure/cdn/cdn-dynamic-site-acceleration)  |  | **&#x2713;**  | **&#x2713;** | **&#x2713;** |
| &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;[Aceleración de sitios dinámicos: compresión de imagen adaptable](https://docs.microsoft.com/azure/cdn/cdn-dynamic-site-acceleration#adaptive-image-compression-akamai-only)  |  | **&#x2713;**  |  |  |
| &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;[Aceleración de sitios dinámicos: captura previa de objetos](https://docs.microsoft.com/azure/cdn/cdn-dynamic-site-acceleration#object-prefetch-akamai-only)  |  | **&#x2713;**  |  |  |
| [Optimización de streaming de vídeo](https://docs.microsoft.com/azure/cdn/cdn-media-streaming-optimization)  | \* | **&#x2713;**  | \* |  \* |
| [Optimización de archivos grandes](https://docs.microsoft.com/azure/cdn/cdn-large-file-optimization)  | \* | **&#x2713;**  | \* |  \* |
| [Equilibrio de carga del servidor global (GSLB)](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-load-balancing-azure)  | **&#x2713;** |**&#x2713;** |**&#x2713;** |**&#x2713;** |
| [Purga rápida](cdn-purge-endpoint.md)  | **&#x2713;** |**&#x2713;** |**&#x2713;** |**&#x2713;** |
| [Carga previa de activos](cdn-preload-endpoint.md)  |  | |**&#x2713;** |**&#x2713;** |
| Configuración de la memoria caché o del encabezado (mediante [reglas de almacenamiento en caché](cdn-caching-rules.md))  |  |**&#x2713;** |**&#x2713;** | |
| Configuración de la memoria caché o del encabezado (mediante un [motor de reglas](cdn-rules-engine.md))  |  | | |**&#x2713;** |
| [Almacenamiento en caché de cadena de consulta](cdn-query-string.md)  | **&#x2713;** |**&#x2713;** |**&#x2713;** |**&#x2713;** |
| Almacenamiento en caché regional  |**&#x2713;** |  |  |  |
| Pila dual IPv4/IPv6 | **&#x2713;** |**&#x2713;** |**&#x2713;** |**&#x2713;** |
| [Compatibilidad con HTTP/2](cdn-http2.md)  | **&#x2713;** |**&#x2713;** |**&#x2713;** |**&#x2713;** |
||||
 **Seguridad** | **Microsoft estándar (versión preliminar)** | **Akamai estándar** | **Verizon estándar** | **Verizon premium** | 
| Compatibilidad con HTTPS con el punto de conexión de red CDN | **&#x2713;** |**&#x2713;** |**&#x2713;** |**&#x2713;** |
| [Dominio personalizado HTTPS](cdn-custom-ssl.md)  | **&#x2713;** | |**&#x2713;** |**&#x2713;** |
| [Compatibilidad con nombre de dominio personalizado](cdn-map-content-to-custom-domain.md)  | **&#x2713;** |**&#x2713;** |**&#x2713;** |**&#x2713;** |
| [Filtrado geográfico](cdn-restrict-access-by-country.md)  | **&#x2713;** |**&#x2713;** |**&#x2713;** |**&#x2713;** |
| [Autenticación de token](cdn-token-auth.md)  |  |  |  |**&#x2713;**| 
| [Protección contra DDOS](https://www.us-cert.gov/ncas/tips/ST04-015)  | **&#x2713;** |**&#x2713;** |**&#x2713;** |**&#x2713;** |
| [Traiga su propio certificado](cdn-custom-ssl.md?tabs=option-2-enable-https-with-your-own-certificate#ssl-certificates) |**&#x2713;** |  |  |  |
||||
| **Análisis e informes** | **Microsoft estándar (versión preliminar)** | **Akamai estándar** | **Verizon estándar** | **Verizon premium** | 
| [Registros de diagnóstico de Azure](cdn-azure-diagnostic-logs.md)  | **&#x2713;** | **&#x2713;** |**&#x2713;** |**&#x2713;** |
| [Informes principales de Verizon](cdn-analyze-usage-patterns.md)  |  | |**&#x2713;** |**&#x2713;** |
| [Informes personalizados de Verizon](cdn-verizon-custom-reports.md)  |  | |**&#x2713;** |**&#x2713;** |
| [Informes de HTTP avanzados](cdn-advanced-http-reports.md)  |  | | |**&#x2713;** |
| [Estadísticas en tiempo real](cdn-real-time-stats.md)  |  | | |**&#x2713;** |
| [Rendimiento del nodo perimetral](cdn-edge-performance.md)  |  | | |**&#x2713;** |
| [Alertas en tiempo real](cdn-real-time-alerts.md)  |  | | |**&#x2713;** |
||||
| **Facilidad de uso** | **Microsoft estándar (versión preliminar)** | **Akamai estándar** | **Verizon estándar** | **Verizon premium** | 
| Fácil integración con los servicios de Azure, como [Storage](cdn-create-a-storage-account-with-cdn.md), [Web Apps](cdn-add-to-web-app.md) y [Media Services](../media-services/media-services-portal-manage-streaming-endpoints.md)  | **&#x2713;** |**&#x2713;** |**&#x2713;** |**&#x2713;** |
| Se puede administrar mediante la [API REST](https://msdn.microsoft.com/library/mt634456.aspx), [.NET](cdn-app-dev-net.md), [Node.js](cdn-app-dev-node.md) o [PowerShell](cdn-manage-powershell.md).  | **&#x2713;** |**&#x2713;** |**&#x2713;** |**&#x2713;** |
| [Motor de entrega de contenido personalizable, basado en reglas](cdn-rules-engine.md)  |  | | |**&#x2713;** |
| Redirección/rescritura de direcciones URL (mediante un [motor de reglas](cdn-rules-engine.md))  |  | | |**&#x2713;** |
| Reglas de dispositivos móviles (mediante un [motor de reglas](cdn-rules-engine.md))  |  | | |**&#x2713;** |

\* Microsoft y Verizon admiten la entrega de archivos de gran tamaño y de elementos multimedia directamente a través de la optimización de distribución web general.



