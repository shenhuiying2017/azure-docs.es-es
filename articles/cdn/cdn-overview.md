---
title: "Información general de CDN de Azure | Microsoft Docs"
description: "Obtenga información acerca de la Red de entrega de contenido (CDN) de Azure y de cómo usarla para ofrecer contenido con alto ancho de banda mediante el almacenamiento en caché de blobs y contenidos estáticos."
services: cdn
documentationcenter: 
author: camsoper
manager: erikre
editor: 
ms.assetid: 866e0c30-1f33-43a5-91f0-d22f033b16c6
ms.service: cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: hero-article
ms.date: 09/30/2016
ms.author: casoper
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 838a9ca3b77d5cd152ad2b8a54387149eafb6202


---
# <a name="overview-of-the-azure-content-delivery-network-cdn"></a>Información general de la red de entrega de contenido (CDN) de Azure
> [!NOTE]
> En este documento se describe qué es la red de entrega de contenido (CDN) de Azure, cómo funciona y las características de cada producto CDN de Azure.  Si desea omitir esta información y pasar directamente a un tutorial sobre cómo crear un punto de conexión de CDN, consulte [Uso de CDN de Azure](cdn-create-new-endpoint.md).  Para ver una lista de ubicaciones actuales de nodos de CDN, consulte [Ubicaciones POP de la Red de entrega de contenido (CDN) de Azure](cdn-pop-locations.md).
> 
> 

La Red de entrega de contenido (CDN) de Azure almacena en caché contenido de web estático en ubicaciones colocadas estratégicamente para proporcionar el máximo rendimiento a la hora de proporcionar contenido a los usuarios.  CDN ofrece a los desarrolladores una solución global para entregar contenido de alto ancho de banda almacenando en caché el contenido en nodos físicos en todo el mundo. 

Entre las ventajas de utilizar la red CDN para almacenar en memoria caché los recursos de sitios web se incluyen:

* Mejor rendimiento y experiencia del usuario para los usuarios finales sobre todo a la hora de utilizar aplicaciones donde son necesarios varios recorridos de ida y vuelta para cargar el contenido
* Gran escalado para mejorar la administración de cargas instantáneas pesadas, por ejemplo, al comienzo de un evento de lanzamiento de un producto.
* Al distribuir solicitudes de usuarios y ofrecer contenido desde servidores perimetrales, se envía menos tráfico al origen.

## <a name="how-it-works"></a>Cómo funciona
![Información general de la red CDN](./media/cdn-overview/cdn-overview.png)

1. Un usuario (Alice) solicita un archivo (también denominado un recurso) mediante una dirección URL con un nombre de dominio especial, como `<endpointname>.azureedge.net`.  DNS enruta la solicitud hacia la ubicación del punto de presencia (POP) con mejor rendimiento.  Este es normalmente el POP geográficamente más cercano al usuario.
2. Si los servidores perimetrales del POP no tienen el archivo en la memoria caché, el servidor perimetral solicitará el archivo desde el origen.  El origen puede ser una aplicación web, servicio en la nube o cuenta de Almacenamiento de Azure, o cualquier servidor web accesible públicamente.
3. El origen devuelve el archivo al servidor perimetral, incluidos los encabezados HTTP opcionales que describen el período de vida (TTL) del archivo.
4. El servidor perimetral almacena el archivo en caché y devuelve el archivo al solicitante original (Alice).  El archivo permanece en caché en el servidor perimetral hasta que expire el TTL.  Si el origen no ha especificado ningún TTL, el valor predeterminado será de siete días.
5. Después, los usuarios adicionales pueden solicitar el mismo archivo mediante la misma dirección URL y también se les puede dirigir al mismo POP.
6. Si no ha expirado el TTL para el archivo, el servidor perimetral devolverá el archivo desde la memoria caché.  Esto genera una experiencia de usuario más rápida y una mayor capacidad de respuesta.

## <a name="azure-cdn-features"></a>Características de la red CDN de Azure
Hay tres productos del servicio CDN de Azure: **Azure CDN Standard de Akamai**, **Azure CDN Standard de Verizon** y **Azure CDN Premium de Verizon**.  En la tabla siguiente se enumeran las características disponibles con cada producto.

|  | Estándar de Akamai | Estándar de Verizon | Premium de Verizone |
| --- | --- | --- | --- |
| Fácil integración con servicios de Azure, como [Storage](cdn-create-a-storage-account-with-cdn.md), [Cloud Services](cdn-cloud-service-with-cdn.md), [Web Apps](../app-service-web/cdn-websites-with-cdn.md) y [Media Services](../media-services/media-services-portal-manage-streaming-endpoints.md) |**&#x2713;** |**&#x2713;** |**&#x2713;** |
| Se puede administrar mediante la [API de REST](https://msdn.microsoft.com/library/mt634456.aspx), [.NET](cdn-app-dev-net.md), [Node.js](cdn-app-dev-node.md) o [PowerShell](cdn-manage-powershell.md). |**&#x2713;** |**&#x2713;** |**&#x2713;** |
| Compatibilidad con HTTPS |**&#x2713;** |**&#x2713;** |**&#x2713;** |
| Equilibrio de carga |**&#x2713;** |**&#x2713;** |**&#x2713;** |
| [DDOS](https://www.us-cert.gov/ncas/tips/ST04-015)  |**&#x2713;** |**&#x2713;** |**&#x2713;** |
| Pila dual IPv4/IPv6 |**&#x2713;** |**&#x2713;** |**&#x2713;** |
| [Compatibilidad con nombre de dominio personalizado](cdn-map-content-to-custom-domain.md) |**&#x2713;** |**&#x2713;** |**&#x2713;** |
| [Almacenamiento en caché de cadena de consulta](cdn-query-string.md) |**&#x2713;** |**&#x2713;** |**&#x2713;** |
| [Filtrado geográfico](cdn-restrict-access-by-country.md) | |**&#x2713;** |**&#x2713;** |
| [Purga rápida](cdn-purge-endpoint.md) |**&#x2713;** |**&#x2713;** |**&#x2713;** |
| [Carga previa de activos](cdn-preload-endpoint.md) | |**&#x2713;** |**&#x2713;** |
| [Análisis esencial](cdn-analyze-usage-patterns.md) | |**&#x2713;** |**&#x2713;** |
| [Compatibilidad con HTTP/2](https://msdn.microsoft.com/library/mt762901.aspx) |**&#x2713;** | | |
| [Informes de HTTP avanzados](cdn-advanced-http-reports.md) | | |**&#x2713;** |
| [Estadísticas en tiempo real](cdn-real-time-stats.md) | | |**&#x2713;** |
| [Alertas en tiempo real](cdn-real-time-alerts.md) | | |**&#x2713;** |
| [Motor de entrega de contenido personalizable, basado en reglas](cdn-rules-engine.md) | | |**&#x2713;** |
| Configuración de la memoria caché o del encabezado (mediante un [motor de reglas](cdn-rules-engine.md)) | | |**&#x2713;** |
| Redirección/rescritura de direcciones URL (mediante un [motor de reglas](cdn-rules-engine.md)) | | |**&#x2713;** |
| Reglas de dispositivos móviles (mediante un [motor de reglas](cdn-rules-engine.md)) | | |**&#x2713;** |

> [!TIP]
> ¿Hay una característica que le gustaría ver en CDN de Azure?  [Envíenos sus comentarios](https://feedback.azure.com/forums/169397-cdn). 
> 
> 

## <a name="next-steps"></a>Pasos siguientes
Para empezar a trabajar con la red CDN, consulte [Uso de CDN de Azure](cdn-create-new-endpoint.md).

Si ya es cliente del servicio CDN, ahora puede administrar los puntos de conexión de CDN utilizando [Microsoft Azure Portal](https://portal.azure.com) o [PowerShell](cdn-manage-powershell.md).

Para ver la red CDN en acción, consulte el [vídeo sobre nuestra sesión de Build 2016](https://azure.microsoft.com/documentation/videos/build-2016-leveraging-the-new-azure-cdn-apis-to-build-wicked-fast-applications/).

Aprenda a automatizar Azure CDN con [.NET](cdn-app-dev-net.md) o [Node.js](cdn-app-dev-node.md).

Para más información sobre los precios, consulte [Precios de Red de entrega de contenido (CDN)](https://azure.microsoft.com/pricing/details/cdn/).




<!--HONumber=Nov16_HO2-->


