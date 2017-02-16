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
ms.date: 11/17/2016
ms.author: terrylan
translationtype: Human Translation
ms.sourcegitcommit: 174546d59124296711731de6c8d8929bada56baf
ms.openlocfilehash: dda826daa18182c1d415037faed5e832680392e1


---
# <a name="protecting-your-applications-in-azure-security-center"></a>Protección de las aplicaciones en Azure Security Center
El Centro de seguridad de Azure analiza el estado de seguridad de los recursos de Azure. Cuando Security Center identifica posibles vulnerabilidades de seguridad, crea recomendaciones que lo guiarán por el proceso de configuración de los controles necesarios.  Las recomendaciones se aplican a los tipos de recursos de Azure: máquinas virtuales, redes, SQL y aplicaciones.

Este artículo aborda las recomendaciones sobre aplicaciones.  Las recomendaciones sobre aplicaciones se centran en la implementación de un firewall de aplicaciones web.  Use la tabla siguiente como referencia para ayudarlo a entender las recomendaciones sobre aplicaciones disponibles y lo que harán cada una de ellas si se aplican.

## <a name="available-application-recommendations"></a>Recomendaciones sobre aplicaciones disponibles
| Recomendación | Description |
| --- | --- |
| [Agregar un firewall de aplicaciones web](security-center-add-web-application-firewall.md) |Recomienda implementar un Firewall de aplicaciones web (WAF) para los puntos de conexión web. Puede proteger varias aplicaciones web del Centro de seguridad si agrega estas aplicaciones a las implementaciones de WAF existentes. Los dispositivos WAF (creados mediante el modelo de implementación de Resource Manager) deben implementarse en una red virtual independiente. Los dispositivos WAF (creados mediante el modelo de implementación clásica) están limitados a usar un grupo de seguridad de red. Esta compatibilidad se extenderá a una implementación completamente personalizada de dispositivos WAF (clásica) en el futuro. |
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



<!--HONumber=Nov16_HO3-->


