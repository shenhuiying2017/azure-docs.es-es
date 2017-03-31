---
title: "Integración de asociados en Azure Security Center | Microsoft Docs"
description: "Este documento explica cómo se integra Azure Security Center con asociados para mejorar la seguridad general de los recursos de Azure."
services: security-center
documentationcenter: na
author: YuriDio
manager: swadhwa
editor: 
ms.assetid: 6af354da-f27a-467a-8b7e-6cbcf70fdbcb
ms.service: security-center
ms.topic: hero-article
ms.devlang: na
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/30/2017
ms.author: yurid
translationtype: Human Translation
ms.sourcegitcommit: 5001cd47b6ee51967d1286414ccefedd8e7e7813
ms.openlocfilehash: 095b5c9d1a888a4061450234f80c52c5834fbf53
ms.lasthandoff: 02/16/2017


---
# <a name="partner-integration-in-azure-security-center"></a>Integración de asociados en Azure Security Center
Este documento explica cómo se integra Azure Security Center con asociados para mejorar la seguridad general y proporcionar una experiencia integrada en Azure, mientras aprovecha Azure Marketplace para la certificación y la facturación de los asociados.

## <a name="why-deploy-partners-solutions-from-security-center"></a>¿Por qué implementar las soluciones de asociados desde Security Center?

Las cuatro principales razones para aprovechar la integración de asociados en Security Center son:

- **Facilidad de implementación**: la implementación de una solución de asociado siguiendo las recomendaciones de Security Center es mucho más fácil. El proceso de implementación se puede automatizar completamente mediante una configuración predeterminada y una topología de red. Alternativamente, los clientes pueden elegir una opción parcialmente automatizada para permitir una mayor flexibilidad y la personalización de la configuración.
- **Detecciones integradas**: los eventos de seguridad de las soluciones de asociados se recopilan, agregan y aparecen automáticamente como parte de las alertas e incidentes de Security Center. Estos eventos también se fusionan con las detecciones procedentes de otros orígenes para proporcionar funcionalidades avanzadas de detección de amenazas.
- **Supervisión de estado y administración unificadas**: los eventos de estado integrados permiten a los clientes supervisar todas las soluciones de asociados de un vistazo. La administración básica está disponible con un acceso sencillo a la configuración avanzada mediante la solución de asociado.
- **Exportación a SIEM**: los clientes ya pueden exportar todas las alertas de Security Center y de los asociados en formato CEF en sistemas SIEM locales mediante la integración de registro de Microsoft Azure (versión preliminar)


## <a name="what-partners-are-integrated-with-security-center"></a>¿Qué asociados se integran con Security Center?
Actualmente, Security Center se integra con los siguientes asociados:

- Soluciones de Endpoint Protection (Trend Micro), 
- firewall de aplicaciones web (Barracuda, F5, Imperva y pronto Microsoft WAF y Fortinet), 
- firewall de próxima generación (Check Point, Barracuda y pronto Fortinet y Cisco). 
- Soluciones de evaluación de vulnerabilidades (Qualys: versión preliminar). 

Con el tiempo, Security Center aumentará el número de asociados dentro de estas categorías y agregará otras nuevas categorías. 

## <a name="how-to-deploy-a-partner-solution"></a>¿Cómo implementar una solución de asociados?

En función de la configuración de su entorno de Azure y la directiva de seguridad que se haya definido, es posible que Security Center recomiende que se implemente una solución de asociados. La recomendación le guiará a través del proceso de selección e instalación de una solución de asociados. La experiencia de implementación global en este momento puede variar según el tipo de solución y el asociado. Para más información, consulte los vínculos que se muestran a continuación:

- [Agregar un firewall de aplicaciones web](security-center-add-web-application-firewall.md)
- [Agregar un firewall de próxima generación](security-center-add-next-generation-firewall.md)
- [Instalación de Endpoint Protection](security-center-install-endpoint-protection.md)
- [Evaluación de vulnerabilidades no instalada](security-center-vulnerability-assessment-recommendations.md)

## <a name="how-to-manage-partner-solutions"></a>Administración de las soluciones de asociados

Una vez que se ha implementado una solución de asociados, puede ver información sobre el estado de la solución y realizar tareas básicas de administración desde el icono de la solución en el panel principal de Security Center. Para más información acerca de cómo administrar soluciones de asociados en Security Center, lea [Supervisión de soluciones de asociados con Azure Security Center](security-center-partner-solutions.md).

![Integración de asociados](./media/security-center-partner-integration/security-center-partner-integration-fig1-new.png)


## <a name="see-also"></a>Consulte también
En este documento, ha aprendido cómo integrar la solución de asociado en Azure Security Center. Para más información sobre el Centro de seguridad, consulte los siguientes recursos:

* [Guía de planeamiento y operaciones de Azure Security Center](security-center-planning-and-operations-guide.md)
* [Administración y respuesta a las alertas de seguridad en el Centro de seguridad de Azure](security-center-managing-and-responding-alerts.md)
* [Alertas de seguridad por tipo en Azure Security Center](security-center-alerts-type.md)
* [Supervisión del estado de seguridad en Azure Security Center](security-center-monitoring.md) : obtenga información sobre cómo supervisar el estado de los recursos de Azure.
* [Supervisión de las soluciones de asociados con Azure Security Center](security-center-partner-solutions.md) : aprenda a supervisar el estado de mantenimiento de las soluciones de asociados.
* [Preguntas más frecuentes sobre Azure Security Center](security-center-faq.md) : encuentre las preguntas más frecuentes sobre el uso del servicio.
* [Blog de seguridad de Azure](http://blogs.msdn.com/b/azuresecurity/) : encuentre entradas de blog sobre el cumplimiento y la seguridad de Azure.

