---
title: "Protección de las aplicaciones en Azure Security Center | Microsoft Docs"
description: "En este documento se explica cómo las recomendaciones de Azure Security Center ayudan a proteger los recursos de Azure y a cumplir las directivas de seguridad."
services: security-center
documentationcenter: na
author: TerryLanfear
manager: MBaldwin
editor: 
ms.assetid: b5fc7a9e-24b1-415f-b3b5-62a53f5dd424
ms.service: security-center
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/01/2016
ms.author: terrylan
ms.openlocfilehash: b5bc02082fa8c2681aa67910729870921fec329d
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="protecting-your-applications-in-azure-security-center"></a>Protección de las aplicaciones en Azure Security Center
El Centro de seguridad de Azure analiza el estado de seguridad de los recursos de Azure. Cuando Security Center identifica posibles vulnerabilidades de seguridad, crea recomendaciones que lo guiarán por el proceso de configuración de los controles necesarios.  Las recomendaciones se aplican a los tipos de recursos de Azure: máquinas virtuales, redes, SQL y aplicaciones.

Este artículo aborda las recomendaciones sobre aplicaciones.  Las recomendaciones sobre aplicaciones se centran en la implementación de un firewall de aplicaciones web.  Use la tabla siguiente como referencia para ayudarlo a entender las recomendaciones sobre aplicaciones disponibles y lo que harán cada una de ellas si se aplican.

## <a name="available-application-recommendations"></a>Recomendaciones sobre aplicaciones disponibles
| Recomendación | Description |
| --- | --- |
| [Agregar un firewall de aplicaciones web](security-center-add-web-application-firewall.md) |Recomienda implementar un Firewall de aplicaciones web (WAF) para los puntos de conexión web. Se muestra una recomendación WAFS para cualquier IP pública (dirección IP de nivel de instancia o con equilibrio de carga) que tiene un grupo de seguridad de red asociado con puertos abiertos web entrantes (80 y 443).</br></br>Security Center le recomendará que aprovisione un WAF para defenderse de ataques dirigidos a las aplicaciones web que se encuentran tanto en las máquinas virtuales como en App Service Environment. Un entorno de App Service es una opción de plan de servicio [Premium](https://azure.microsoft.com/pricing/details/app-service/) de Azure App Service que proporciona un entorno plenamente aislado y dedicado para ejecutar de forma segura las aplicaciones de Azure App Service. Para más información acerca de ASE, consulte [Documentación de App Service Environment](../app-service/environment/intro.md).</br></br>Puede proteger varias aplicaciones web del Centro de seguridad si agrega estas aplicaciones a las implementaciones de WAF existentes. |
| [Finalización de la protección de la aplicación](security-center-add-web-application-firewall.md#finalize-application-protection) |Para completar la configuración de un WAF, el tráfico se debe redirigir a la aplicación del WAF. Si se sigue esta recomendación, se completarán los cambios necesarios en la configuración. |

## <a name="see-also"></a>Consulte también
Para obtener más información sobre las recomendaciones que se aplican a otros tipos de recursos de Azure, consulte los siguientes artículos:

* [Protección de las máquinas virtuales en Azure Security Center](security-center-virtual-machine-recommendations.md)
* [Protección de las redes en Azure Security Center](security-center-network-recommendations.md)
* [Protección del servicio SQL de Azure en Azure Security Center](security-center-sql-service-recommendations.md)

Para más información sobre el Centro de seguridad, consulte los siguientes recursos:

* [Establecimiento de directivas de seguridad en Azure Security Center](security-center-policies.md) : aprenda a configurar directivas de seguridad para las suscripciones y los grupos de recursos de Azure.
* [Administración y respuesta a las alertas de seguridad en Azure Security Center](security-center-managing-and-responding-alerts.md) : obtenga información sobre cómo administrar y responder a alertas de seguridad.
* [Preguntas más frecuentes sobre Azure Security Center](security-center-faq.md) : encuentre las preguntas más frecuentes sobre el uso del servicio.
