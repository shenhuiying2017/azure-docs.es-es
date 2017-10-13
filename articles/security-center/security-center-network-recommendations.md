---
title: "Protección de las redes en Azure Security Center | Microsoft Docs"
description: "En este documento se explica cómo las recomendaciones de Azure Security Center ayudan a proteger las redes y a cumplir las directivas de seguridad."
services: security-center
documentationcenter: na
author: TerryLanfear
manager: MBaldwin
editor: 
ms.assetid: 96c55a02-afd6-478b-9c1f-039528f3dea0
ms.service: security-center
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/16/2016
ms.author: terrylan
ms.openlocfilehash: 00b715507a7c3a4d784b800e7bf0c700f6ea6ff1
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="protecting-your-network-in-azure-security-center"></a>Protección de las redes en Azure Security Center
El Centro de seguridad de Azure analiza el estado de seguridad de los recursos de Azure. Cuando Security Center identifica posibles vulnerabilidades de seguridad, crea recomendaciones que lo guiarán por el proceso de configuración de los controles necesarios.  Las recomendaciones se aplican a los tipos de recursos de Azure: máquinas virtuales, redes, SQL y aplicaciones.

Este artículo aborda las recomendaciones sobre redes.  Las recomendaciones sobre redes se centran en los firewalls de próxima generación, los grupos de seguridad de red, la configuración de reglas de tráfico entrantes y mucho más.  Use la tabla siguiente como referencia para ayudarlo a entender las recomendaciones disponibles sobre redes y lo que harán cada una de ellas si se aplican.

## <a name="available-network-recommendations"></a>Recomendaciones sobre redes disponibles
| Recomendación | Description |
| --- | --- |
| [Add a Next Generation Firewall](security-center-add-next-generation-firewall.md) |Recomienda agregar un firewall de próxima generación (NGFW) de un asociado de Microsoft para aumentar la protección. |
| [Enrutar el tráfico solo a través de NGFW](security-center-add-next-generation-firewall.md#route-traffic-through-ngfw-only) |Recomienda configurar reglas de grupos de seguridad de red (NSG) que fuercen que el tráfico entrante pase a su máquina virtual mediante el NGFW. |
| [Habilitar grupos de seguridad de red en subredes o máquinas virtuales](security-center-enable-network-security-groups.md) |Recomienda habilitar NSG en subredes o máquinas virtuales. |
| [Restringir el acceso a través de puntos de conexión accesibles desde Internet](security-center-restrict-access-through-internet-facing-endpoints.md) |Recomienda configurar reglas de tráfico de entrada para los NSG. |

## <a name="see-also"></a>Otras referencias
Para obtener más información sobre las recomendaciones que se aplican a otros tipos de recursos de Azure, consulte los siguientes artículos:

* [Protección de las máquinas virtuales en Azure Security Center](security-center-virtual-machine-recommendations.md)
* [Protección de las aplicaciones en Azure Security Center](security-center-application-recommendations.md)
* [Protección del servicio SQL de Azure en Azure Security Center](security-center-sql-service-recommendations.md)

Para más información sobre el Centro de seguridad, consulte los siguientes recursos:

* [Establecimiento de directivas de seguridad en Azure Security Center](security-center-policies.md) : aprenda a configurar directivas de seguridad para las suscripciones y los grupos de recursos de Azure.
* [Administración y respuesta a las alertas de seguridad en Azure Security Center](security-center-managing-and-responding-alerts.md) : obtenga información sobre cómo administrar y responder a alertas de seguridad.
* [Preguntas más frecuentes sobre Azure Security Center](security-center-faq.md) : encuentre las preguntas más frecuentes sobre el uso del servicio.
