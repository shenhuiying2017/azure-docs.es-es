---
title: "Personalización de reglas de firewall de aplicaciones web en Azure Application Gateway (portal) | Microsoft Docs"
description: "Esta página proporciona información sobre cómo personalizar reglas de firewall de aplicaciones web en Application Gateway con el portal."
documentationcenter: na
services: application-gateway
author: georgewallace
manager: timlt
editor: tysonn
ms.assetid: 1159500b-17ba-41e7-88d6-b96986795084
ms.service: application-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.custom: 
ms.workload: infrastructure-services
ms.date: 03/28/2017
ms.author: gwallace
ms.translationtype: Human Translation
ms.sourcegitcommit: fc27849f3309f8a780925e3ceec12f318971872c
ms.openlocfilehash: 094ae187ec8ba5ff38f174ee4cf139d30db7e057
ms.contentlocale: es-es
ms.lasthandoff: 06/14/2017


---

# <a name="customize-web-application-firewall-rules-through-the-portal"></a>Personalización de reglas de firewall de aplicaciones web mediante el portal

El firewall de aplicaciones web de Application Gateway proporciona protección para las aplicaciones web. Estas protecciones proceden de conjuntos de reglas OWASP CRS. Algunas reglas pueden producir falsos positivos y bloquear el tráfico real.  Por este motivo, la puerta de enlace de aplicaciones ofrece la posibilidad de personalizar reglas y grupos de reglas en una puerta de enlace de aplicaciones habilitada para el firewall de aplicaciones web. Para más información sobre las reglas y grupos de reglas específicos, visite [Reglas y grupos de reglas de CRS de firewall de aplicaciones web](application-gateway-crs-rulegroups-rules.md).

>[!NOTE]
> Si la puerta de enlace de aplicaciones no usa el nivel WAF, se le presenta la opción para actualizar la puerta de enlace de aplicaciones al nivel WAF, tal como se muestra en la siguiente imagen:

![habilitar waf][fig1]

## <a name="view-rule-groups-and-rules"></a>Visualización de reglas y grupos de reglas

Navegue a una puerta de enlace de aplicaciones y seleccione **Firewall de aplicaciones web**.  Haga clic en **Advanced rule configuration** ///(Configuración avanzada de reglas).  En la página se muestra una tabla de todos los grupos de reglas proporcionados con el conjunto de reglas elegido.

![configurar reglas deshabilitadas][1]

## <a name="search-for-rules-to-disable"></a>Búsqueda de reglas para deshabilitar

La hoja de configuración del firewall de aplicaciones web ofrece la posibilidad de filtrar las reglas mediante una búsqueda de texto. El resultado muestra solo las reglas y los grupos de reglas que contienen el texto que se va a buscar.

![buscar reglas][2]

## <a name="disable-rule-groups-and-rules"></a>Deshabilitación de reglas y grupos de reglas

Al deshabilitar reglas, puede deshabilitar un grupo de reglas completo o reglas específicas de uno o más grupos de reglas.  Una vez que estén desactivadas las reglas que quiere deshabilitar, haga clic en **Guardar**.  Esta acción guarda los cambios en la puerta de enlace de aplicaciones.

![guardar cambios][3]

## <a name="next-steps"></a>Pasos siguientes

Cuando configure las reglas deshabilitadas, aprenda cómo ver los registros de WAF; para ello, visite [Diagnóstico de Application Gateway](application-gateway-diagnostics.md#diagnostic-logs).

[fig1]: ./media/application-gateway-customize-waf-rules-portal/1.png
[1]: ./media/application-gateway-customize-waf-rules-portal/figure1.png
[2]: ./media/application-gateway-customize-waf-rules-portal/figure2.png
[3]: ./media/application-gateway-customize-waf-rules-portal/figure3.png

