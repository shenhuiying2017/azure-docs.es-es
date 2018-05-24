---
title: Los vínculos de la página no funcionan para una aplicación de Proxy de aplicación | Microsoft Docs
description: Cómo solucionar problemas relacionados con vínculos rotos en aplicaciones de Proxy de aplicación integradas con Azure AD
services: active-directory
documentationcenter: ''
author: ajamess
manager: mtillman
ms.assetid: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/11/2017
ms.author: asteen
ms.openlocfilehash: 7c505080e6b6144ab3cf24ad89bb084efbb77c29
ms.sourcegitcommit: e14229bb94d61172046335972cfb1a708c8a97a5
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/14/2018
ms.locfileid: "34155476"
---
# <a name="links-on-the-page-dont-work-for-an-application-proxy-application"></a>Los vínculos de la página no funcionan para una aplicación de Proxy de aplicación

Este artículo le ayudará a entender por qué los vínculos de la aplicación Azure Active Directory Application Proxy no funcionan correctamente.

## <a name="overview"></a>Información general 
Después de publicar una aplicación de Proxy de aplicación, los únicos vínculos que funcionan de forma predeterminada son los vínculos cuyo destino aparece en la dirección URL raíz publicada. Los vínculos de las aplicaciones no funcionan, probablemente la dirección URL interna de la aplicación no incluye todos los vínculos de destino en la aplicación.

**¿Por qué ocurre esto?** Al hacer clic en un vínculo en una aplicación, el Proxy de aplicación intenta resolver la dirección URL ya sea como dirección URL interna de la misma aplicación o como dirección URL disponible externamente. Si el vínculo apunta a una dirección URL interna que no está dentro de la misma aplicación, no pertenece a ninguno de estos depósitos y se producirá un error de que no se encuentra.

## <a name="ways-you-can-resolve-broken-links"></a>Maneras de resolver vínculos rotos

Hay tres maneras de resolver este problema. Las siguientes opciones se ordenan por complejidad ascendente.

1.  Asegúrese de que la dirección URL interna es una raíz que contiene todos los vínculos relevantes para la aplicación. Esto permite que todos los vínculos se resuelvan como contenido publicado dentro de la misma aplicación.

    Si cambia la dirección URL interna pero no desea que cambie la página de aterrizaje de los usuarios, cambie la dirección URL de la página principal a la dirección URL interna publicada anteriormente. Para ello, vaya a "Azure Active Directory" -&gt; Registros de aplicaciones -&gt; seleccione la aplicación -&gt; Propiedades. En esta pestaña de propiedades, verá el campo "Dirección URL de la página principal", que podrá ajustar a la página de aterrizaje que desee.

2.  Si las aplicaciones usan nombres de dominio completos (FQDN), use [dominios personalizados](manage-apps/application-proxy-configure-custom-domain.md) para publicar las aplicaciones. Esta característica permite usar la misma dirección URL de manera tanto interna como externa.

    Esta opción garantiza que los vínculos de la aplicación son accesibles desde el exterior a través de Proxy de aplicación, ya que los vínculos dentro de la aplicación para las direcciones URL internas también se reconocen externamente. Todos los vínculos tienen que pertenecer a una aplicación publicada. Sin embargo, con esta opción no es necesario que los vínculos pertenezcan a la misma aplicación y pueden pertenecer a varias.

3.  Si ninguna de estas opciones es viable, puede ver una versión preliminar de una nueva característica de traducción/reescritura de direcciones URL. Con esta característica, las direcciones URL internas o los vínculos que existan en el cuerpo HTML de las aplicaciones se traducirán o "asignarán" a las direcciones URL externas que publique el Proxy de aplicación. Esta traducción solo funciona en vínculos del código HTML o CSS, no sirve si el vínculo se genera mediante JS. 

Como resultado, se recomienda encarecidamente usar la solución de [dominios personalizados](manage-apps/application-proxy-configure-custom-domain.md), si es posible. Si desea unirse a la versión preliminar, envíe un mensaje a <aadapfeedback@microsoft.com> con los identificadores de aplicación.

## <a name="next-steps"></a>Pasos siguientes
[Trabajo con servidores proxy locales existentes](manage-apps/application-proxy-configure-connectors-with-proxy-servers.md)

