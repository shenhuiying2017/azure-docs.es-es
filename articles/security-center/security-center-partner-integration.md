---
title: "Integración de asociados en Azure Security Center | Microsoft Docs"
description: Aprenda la forma en que Azure Security Center se integra con los asociados para mejorar la seguridad general de los recursos de Azure.
services: security-center
documentationcenter: na
author: YuriDio
manager: mbaldwin
editor: 
ms.assetid: 6af354da-f27a-467a-8b7e-6cbcf70fdbcb
ms.service: security-center
ms.topic: hero-article
ms.devlang: na
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/23/2017
ms.author: yurid
ms.translationtype: HT
ms.sourcegitcommit: bde1bc7e140f9eb7bb864c1c0a1387b9da5d4d22
ms.openlocfilehash: 4d0909e926de14a0cbe9799b969ac7a1946d69d1
ms.contentlocale: es-es
ms.lasthandoff: 07/21/2017

---
# <a name="partner-integration-in-azure-security-center"></a>Integración de asociados en Azure Security Center

En este artículo se describe la forma en que Azure Security Center se integra con los asociados para ayudarle a mejorar la seguridad general. Security Center ofrece una experiencia integrada en Azure y saca provecho de Azure Marketplace para la certificación de socios y su facturación.

> [!NOTE] 
> Desde junio de 2017, Security Center usa Microsoft Monitoring Agent para recopilar y almacenar datos. Para más información, consulte [Migración de la plataforma de Azure Security Center](security-center-platform-migration.md). La información de este artículo representa la funcionalidad de Security Center después de la transición a Microsoft Monitoring Agent.
>

## <a name="why-deploy-partner-solutions-from-security-center"></a>¿Por qué se implementan las soluciones de asociados desde Security Center?

Las cuatro principales razones para aprovechar la integración de asociados en Security Center son:

- **Facilidad de implementación**. Resulta mucho más fácil implementar una solución de asociado siguiendo las recomendaciones de Security Center. El proceso de implementación se puede automatizar completamente mediante el uso de una topología de red y un programa de instalación predeterminados. Como alternativa, los clientes pueden elegir una opción semiautomatizada para lograr más flexibilidad y personalización.
- **Detecciones integradas**. Los eventos de seguridad de las soluciones de asociados se recopilan, agregan y aparecen automáticamente como parte de las alertas e incidentes de Security Center. Estos eventos también se fusionan con las detecciones procedentes de otros orígenes para proporcionar funcionalidades avanzadas de detección de amenazas.
- **Supervisión y administración unificadas del mantenimiento**. Los clientes pueden usar eventos de mantenimiento integrados para supervisar todas las soluciones de asociados de un vistazo. La administración básica está disponible con un acceso sencillo a la configuración avanzada mediante la solución de asociado.
- **Exportación a SIEM**. Los clientes pueden exportar todas las alertas de asociados y de Security Center en formato Common Event Format (CEF) a sistemas de administración de eventos e información de seguridad (SIEM) locales mediante la integración de registros de Azure (versión preliminar).


## <a name="partners-that-integrate-with-security-center"></a>Asociados que se integran con Security Center

Actualmente, Security Center se integra con estas soluciones:

- Protección de punto de conexión ([Trend Micro](https://help.deepsecurity.trendmicro.com/azure-marketplace-getting-started-with-deep-security.html), Symantec y [Microsoft Antimalware para Azure Cloud Services y Virtual Machines](https://docs.microsoft.com/azure/security/azure-security-antimalware)) 
- Firewall de aplicaciones web ([Barracuda](https://www.barracuda.com/products/webapplicationfirewall), [F5](https://support.f5.com/kb/en-us/products/big-ip_asm/manuals/product/bigip-ve-web-application-firewall-microsoft-azure-12-0-0.html), [Imperva](https://www.imperva.com/Products/WebApplicationFirewall-WAF), [Fortinet](https://www.fortinet.com/resources.html?limit=10&search=&document-type=data-sheets) y [Azure Application Gateway](https://azure.microsoft.com/blog/azure-web-application-firewall-waf-generally-available/)) 
- Firewall de última generación ([Check Point](https://www.checkpoint.com/products/vsec-microsoft-azure/), [Barracuda](https://campus.barracuda.com/product/nextgenfirewallf/article/NGF/AzureDeployment/), [Fortinet](http://docs.fortinet.com/d/fortigate-fortios-handbook-the-complete-guide-to-fortios-5.2) y [Cisco](http://www.cisco.com/c/en/us/td/docs/security/firepower/quick_start/azure/ftdv-azure-qsg.html)) 
- Evaluación de vulnerabilidades ([Qualys](https://www.qualys.com/public-clouds/microsoft-azure/))  

Con el tiempo, Security Center aumentará el número de asociados de estas categorías y agregará nuevas categorías. 

## <a name="deploy-a-partner-solution"></a>Implementación de una solución de asociado

En función de la configuración de su entorno de Azure y de la directiva de seguridad que haya definido, es posible que Security Center recomiende que implemente una solución de asociados. La recomendación de Security Center le guiará a través del proceso de selección e instalación de una solución de asociados. La experiencia de implementación global puede variar en función del tipo de solución y el asociado que se use. Para más información, consulte los siguientes artículos.

- [Instalar Endpoint Protection](security-center-install-endpoint-protection.md)
- [Agregar un firewall de aplicaciones web](security-center-add-web-application-firewall.md)
- [Agregar un firewall de última generación](security-center-add-next-generation-firewall.md)
- [Evaluación de vulnerabilidades no instalada](security-center-vulnerability-assessment-recommendations.md)

## <a name="manage-partner-solutions"></a>Administración de soluciones de asociados

Después de la implementación, para ver información acerca del mantenimiento de la solución y realizar tareas de administración básicas, en la hoja **Security Center**, seleccione el icono **Soluciones de asociados**. Para información acerca de cómo administrar soluciones de asociados en Security Center, consulte [Supervisión de las soluciones de asociados con Azure Security Center](security-center-partner-solutions.md).

![Integración de asociados](./media/security-center-partner-integration/security-center-partner-integration-fig1-1-newUI.png)

> [!NOTE]
> La compatibilidad con la protección del punto de conexión Symantec se limita a la detección. No hay alertas de estado disponibles.
>

## <a name="see-also"></a>Otras referencias

En este documento, ha aprendido a integrar soluciones de asociado en Azure Security Center. Para más información sobre Security Center, consulte los siguientes artículos:

* [Guía de planeamiento y operaciones de Security Center](security-center-planning-and-operations-guide.md)
* [Responder a alertas de seguridad en Security Center](security-center-managing-and-responding-alerts.md)
* [Comprensión de las alertas de seguridad en Azure Security Center](security-center-alerts-type.md)
* [Supervisión del estado de seguridad en Security Center](security-center-monitoring.md). Aprenda a supervisar el estado de los recursos de Azure.
* [Supervisión de soluciones de asociados con Security Center](security-center-partner-solutions.md). Aprenda cómo supervisar el estado de mantenimiento de las soluciones de sus asociados.
* [Preguntas más frecuentes sobre Azure Security Center](security-center-faq.md). Obtenga respuestas para las preguntas más frecuentes acerca del uso del servicio.
* [Blog de seguridad de Azure](http://blogs.msdn.com/b/azuresecurity/). Encuentre artículos de blog sobre el cumplimiento y la seguridad de Azure.

