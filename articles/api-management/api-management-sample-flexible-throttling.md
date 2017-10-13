---
title: "Limitación avanzada de solicitudes con Administración de API de Azure"
description: "Aprenda a crear y aplicar directivas de limitación de velocidad y de cuota flexibles con Administración de API de Azure."
services: api-management
documentationcenter: 
author: vladvino
manager: erikre
editor: 
ms.assetid: fc813a65-7793-4c17-8bb9-e387838193ae
ms.service: api-management
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/15/2016
ms.author: apimpm
ms.openlocfilehash: 356f98aec072a1295915ae0701a3e3cd793aba07
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="advanced-request-throttling-with-azure-api-management"></a>Limitación avanzada de solicitudes con Administración de API de Azure
La posibilidad de limitar las solicitudes entrantes es un rol clave de Administración de API de Azure. Ya sea mediante el control de la velocidad de solicitudes o de las solicitudes y los datos totales transferidos, Administración de API permite a los proveedores de API proteger sus API de uso indebido y crear valor para los diferentes niveles de productos de API.

## <a name="product-based-throttling"></a>Limitación por producto
Hasta la fecha, las capacidades de limitación de velocidad se han circunscrito al ámbito de una suscripción de producto concreta (esencialmente una clave), que se define en el portal para editores de Administración de API. Resulta útil para que el proveedor de la API aplique límites a los desarrolladores que inicien sesión para usar su API, pero no ayuda, por ejemplo, a imponer limitaciones a los usuarios finales individuales de la API. Es posible que un solo usuario de la aplicación del desarrollador consuma toda la cuota e impida que otros clientes del desarrollador puedan usar la aplicación. Además, es posible que varios clientes que generen un gran volumen de solicitudes limiten el acceso a los usuarios ocasionales.

## <a name="custom-key-based-throttling"></a>Limitación por clave personalizada
Las nuevas directivas [rate-limit-by-key](https://msdn.microsoft.com/library/azure/dn894078.aspx#LimitCallRateByKey) y [quota-by-key](https://msdn.microsoft.com/library/azure/dn894078.aspx#SetUsageQuotaByKey) ofrecen una solución considerablemente más flexible para el control del tráfico. Estas nuevas directivas permiten definir expresiones para identificar las claves que se usarán para realizar un seguimiento del uso del tráfico. El funcionamiento de esto se ilustra más claramente con un ejemplo. 

## <a name="ip-address-throttling"></a>Limitación por dirección IP
Las siguientes directivas restringen una única dirección IP de cliente a solo 10 llamadas por minuto, con un total de 1 000 000 llamadas y 10 000 kilobytes de ancho de banda al mes. 

```xml
<rate-limit-by-key  calls="10"
          renewal-period="60"
          counter-key="@(context.Request.IpAddress)" />

<quota-by-key calls="1000000"
          bandwidth="10000"
          renewal-period="2629800"
          counter-key="@(context.Request.IpAddress)" />
```

Si todos los clientes en Internet utilizasen una única dirección IP, esta podría ser una forma eficaz de limitar el uso por usuario. Pero es bastante probable que varios usuarios compartan una única dirección IP pública debido a su acceso a Internet a través de un dispositivo NAT. A pesar de esto, es posible que para las API que permiten el acceso no autenticado, `IpAddress` sea la mejor opción.

## <a name="user-identity-throttling"></a>Limitación por identidad de usuario
Si se autentica un usuario final, puede generarse una clave de limitación basada en información que identifica de forma única a ese usuario.

```xml
<rate-limit-by-key calls="10"
    renewal-period="60"
    counter-key="@(context.Request.Headers.GetValueOrDefault("Authorization","").AsJwt()?.Subject)" />
```

En este ejemplo se extrae el encabezado de autorización, se convierte en objeto `JWT` y se usa el firmante del token para identificar al usuario y usarlo como clave de limitación de velocidad. Si la identidad del usuario se almacena en el `JWT` como una de las otras notificaciones, ese valor podría usarse en su lugar.

## <a name="combined-policies"></a>Directivas de combinación
Aunque las nuevas directivas de limitación proporcionan más control que las directivas existentes de limitación, sigue siendo útil combinar ambas capacidades. La limitación por clave de suscripción de producto ([Limitar tarifa de llamadas por suscripción](https://msdn.microsoft.com/library/azure/dn894078.aspx#LimitCallRate) y [Establecer cuota de uso por suscripción](https://msdn.microsoft.com/library/azure/dn894078.aspx#SetUsageQuota)) es una excelente manera de habilitar la monetización de una API mediante el cobro por niveles de uso. El control más preciso de poder limitar por usuario es complementario e impide que el comportamiento de un usuario degrade la experiencia de otro. 

## <a name="client-driven-throttling"></a>Limitación controlada por cliente
Cuando la clave de limitación se define con una [expresión de directiva](https://msdn.microsoft.com/library/azure/dn910913.aspx), es el proveedor de la API el que elige el ámbito de la limitación. Pero puede que un desarrollador desee controlar cómo limita la frecuencia de sus propios clientes. Esto lo podría habilitar el proveedor de la API introduciendo un encabezado personalizado que permita a la aplicación cliente del desarrollador comunicar la clave a la API.

```xml
<rate-limit-by-key calls="100"
          renewal-period="60"
          counter-key="@(request.Headers.GetValueOrDefault("Rate-Key",""))"/>
```

Así se permite a la aplicación cliente del desarrollador elegir cómo se quiere crear la clave de limitación de velocidad. Con un poco de ingenio un desarrollador del cliente puede crear sus propios niveles de velocidad asignando conjuntos de claves a los usuarios y rotando el uso de claves.

## <a name="summary"></a>Resumen
Administración de API de Azure ofrece limitación de velocidad y de cuota para proteger y agregar valor al servicio de API. Las nuevas directivas de limitación con reglas de ámbito personalizadas permiten un control más preciso sobre las directivas para permitir a los clientes crear aplicaciones aún mejores. Los ejemplos de este artículo muestran el uso de estas nuevas directivas fabricando claves de limitación de velocidad con direcciones IP de cliente, identidad de usuario y valores generados por el cliente. Pero hay muchas más partes del mensaje que podrían usarse como, por ejemplo, agente de usuario, fragmentos de ruta de dirección URL, tamaño del mensaje.

## <a name="next-steps"></a>Pasos siguientes
Háganos sus comentarios en la conversación Disqus sobre este tema. Sería estupendo conocer otros posibles valores de clave que hayan sido una elección lógica en sus escenarios.

## <a name="watch-a-video-overview-of-these-policies"></a>Ver un vídeo de información general de estas directivas
Para más información sobre las directivas [rate-limit-by-key](https://msdn.microsoft.com/library/azure/dn894078.aspx#LimitCallRateByKey) y [quota-by-key](https://msdn.microsoft.com/library/azure/dn894078.aspx#SetUsageQuotaByKey) que se describen en este artículo, vea el siguiente vídeo.

> [!VIDEO https://channel9.msdn.com/Blogs/AzureApiMgmt/Advanced-Request-Throttling-with-Azure-API-Management/player]
> 
> 

