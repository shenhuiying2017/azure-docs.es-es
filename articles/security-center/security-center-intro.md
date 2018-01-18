---
title: "¿Qué es Azure Security Center? | Microsoft Docs"
description: "Obtenga información sobre el Centro de seguridad de Azure, sus capacidades clave y cómo funciona."
services: security-center
documentationcenter: na
author: TerryLanfear
manager: MBaldwin
editor: 
ms.assetid: 45b9756b-6449-49ec-950b-5ed1e7c56daa
ms.service: security-center
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/08/2018
ms.author: terrylan
ms.openlocfilehash: 50a54b8d2a73807aa9a0217f7ccf971b8c516494
ms.sourcegitcommit: 9a8b9a24d67ba7b779fa34e67d7f2b45c941785e
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/08/2018
---
# <a name="what-is-azure-security-center"></a>¿Qué es el Centro de seguridad de Azure?
Azure Security Center proporciona administración unificada de la seguridad y protección avanzada contra amenazas para cargas de trabajo en la nube híbrida. Con Security Center, puede aplicar directivas de seguridad en las cargas de trabajo, limitar la exposición a amenazas y detectar y responder a los ataques.

Razones para usar Security Center

- **Administración centralizada de directivas**: garantice el cumplimiento de los requisitos de seguridad normativos o de la empresa administrando las directivas de seguridad de forma centralizada en todas las cargas de trabajo en la nube híbridas.
- **Evaluación continua de la seguridad**: supervise la seguridad de máquinas, redes, almacenamiento y servicios de datos y aplicaciones para detectar posibles problemas de seguridad.
- **Recomendaciones prácticas**: corrija las vulnerabilidades de seguridad antes de que puedan ser usadas por los atacantes mediante recomendaciones de seguridad prácticas y clasificadas en orden de prioridad.
- **Defensas avanzadas en la nube**: reduzca las amenazas con acceso Just-In-Time a los puertos de administración y la creación de listas blancas para controlar las aplicaciones que se ejecutan en las máquinas virtuales.
- **Prioridad de alertas e incidentes**: céntrese primero en las amenazas más críticas con la clasificación en orden de prioridad de las alertas e incidentes de seguridad.
- **Soluciones de seguridad integradas**: recopile, busque y analice los datos de seguridad desde diversos orígenes, incluidas las soluciones de asociados conectadas.

En **Security Center - Overview** (Información general de Security Center) se proporciona una vista rápida de la situación de seguridad de las cargas de trabajo de Azure y que no son de Azure, lo que le permite detectar y evaluar la seguridad de las cargas de trabajo e identificar y mitigar riesgos. El panel integrado proporciona información instantánea sobre alertas de seguridad y vulnerabilidades que requieren atención.

![Información general][1]

## <a name="centralized-policy-management"></a>Administración de directivas centralizada
Una directiva de seguridad define la configuración deseada de las cargas de trabajo. Además, ayuda a garantizar el cumplimiento de los requisitos de seguridad normativos o de la empresa. En Security Center, se definen directivas y se adaptan a su tipo de carga de trabajo o la confidencialidad de los datos.

Las directivas de Security Center contienen los componentes siguientes:

- **Recopilación de datos**: determina el aprovisionamiento del agente y la configuración de seguridad de la [colección de datos](security-center-enable-data-collection.md).
- **Directiva de seguridad**: determina qué controles supervisa y recomienda Security Center mediante la edición de la [directiva de seguridad](security-center-policies.md).
- **Notificaciones por correo electrónico**: determina los contactos de seguridad y la configuración de [notificaciones por correo electrónico](security-center-provide-security-contact-details.md).
- **Plan de tarifa**: define la [selección de precios](security-center-pricing.md) gratuitos o estándar. El plan elegido determina qué características de Security Center están disponibles para los recursos del ámbito.

![Directiva de seguridad][2]

Para más información, consulte [Introducción a las directivas de seguridad](security-center-policies-overview.md).

## <a name="continuous-security-assessment"></a>Evaluación continua de la seguridad
Security Center analiza el estado de seguridad de los recursos de proceso, las redes virtuales, los servicios de almacenamiento y datos y las aplicaciones. La evaluación continua ayuda a detectar posibles problemas de seguridad, por ejemplo, sistemas a los que les faltan actualizaciones de seguridad o puertos de red expuestos. Seleccione un icono de la sección Prevención para ver más información, como una lista de recursos y las vulnerabilidades que se han identificado.

![¿Qué es la supervisión del estado de seguridad?][3]

Para más información, consulte [Supervisión del estado de seguridad](security-center-monitoring.md).

## <a name="actionable-recommendations"></a>Recomendaciones prácticas
Security Center analiza el estado de seguridad de los recursos de Azure y que no son de Azure para identificar posibles vulnerabilidades de seguridad. Una lista de recomendaciones de seguridad clasificadas en orden de prioridad le guía por el proceso de solucionar los problemas de seguridad.

![Recomendaciones][4]

Para más información, consulte [Administración de recomendaciones de seguridad](security-center-recommendations.md).

## <a name="just-in-time-vm-access"></a>Acceso Just-In-Time a la máquina virtual
Reduzca la superficie expuesta a ataques de red con el acceso controlado Just-In-Time a los puertos de administración de las máquinas virtuales de Azure, lo que reducirá drásticamente la exposición a ataques por fuerza bruta y otros tipos de ataques de red.

![Acceso Just-In-Time a la máquina virtual][5]

Especifique reglas de cómo los usuarios pueden conectarse a las máquinas virtuales. Cuando sea necesario, se puede solicitar el acceso desde Security Center o a través de PowerShell. Siempre que la solicitud cumpla las reglas, se concede automáticamente acceso en la hora solicitada.

Para más información, consulte [Administración del acceso a máquina virtual mediante Just-In-Time](security-center-just-in-time.md).

## <a name="adaptive-application-controls"></a>Controles de aplicación adaptables
Bloquee el malware y otras aplicaciones no deseadas aplicando recomendaciones de inclusión en lista blanca adaptadas a sus cargas de trabajo de Azure específicas y basadas en el aprendizaje automático.

![Controles de aplicación adaptables][6]

Revise las reglas de creación de listas blancas de aplicaciones recomendadas generadas por Security Center y haga clic en ellas para aplicarlas, o edite las reglas ya configuradas.

Para más información, consulte [Controles de aplicación adaptables](security-center-adaptive-application.md).

## <a name="prioritized-alerts-and-incidents"></a>Alertas e incidentes clasificados por orden de prioridad
Security Center usa inteligencia análisis avanzados e inteligencia global sobre amenazas para detectar ataques entrantes y las actividades posteriores a la infracción. Las alertas se clasifican en orden de prioridad y se agrupan en incidentes, lo que ayuda a centrarse primero en las amenazas más críticas. También puede crear sus propias alertas de seguridad personalizadas.

![Alertas e incidentes clasificados por orden de prioridad][7]

Rápidamente, puede evaluar el ámbito y el impacto de un ataque con una experiencia de investigación visual e interactiva, y usar consultas predefinidas o ad hoc para una exploración más profunda de los datos de seguridad.

Para más información, consulte [Administración y respuesta a las alertas de seguridad](security-center-managing-and-responding-alerts.md).

## <a name="integrate-your-security-solutions"></a>Integración de las soluciones de seguridad
Recopile, busque y analice los datos de seguridad procedentes de diversos orígenes, incluidas las soluciones de asociados conectadas como firewalls de red y otros servicios de Microsoft, de Security Center.

![Integración de las soluciones de seguridad][8]

Para más información, consulte [Integración de soluciones de seguridad](security-center-partner-integration.md).

## <a name="next-steps"></a>pasos siguientes

- Para empezar a trabajar con el Centro de seguridad, necesita una suscripción a Microsoft Azure. Si no tiene una suscripción, puede registrarse para obtener una [evaluación gratuita](https://azure.microsoft.com/free/).
- Con la suscripción de Azure se habilita el plan de tarifa Gratis de Security Center. Para aprovechar las funcionalidades avanzadas de administración de seguridad y detección de amenazas, debe actualizar al plan de tarifa Estándar. El nivel Estándar es gratuito durante los primeros 60 días. Para más información, consulte la [página de precios de Security Center](https://azure.microsoft.com/pricing/details/security-center/).
- Si está listo para habilitar Security Center Estándar en este momento, la [Guía de inicio para incorporar sus suscripción de Azure en Security Center Estándar](security-center-get-started.md) le guía por los pasos necesarios.


<!--Image references-->
[1]: ./media/security-center-intro/overview.png
[2]: ./media/security-center-intro/security-policy.png
[3]: ./media/security-center-intro/compute.png
[4]: ./media/security-center-intro/recommendations.png
[5]: ./media/security-center-intro/just-in-time-vm-access.png
[6]: ./media/security-center-intro/adaptive-app-controls.png
[7]: ./media/security-center-intro/security-alerts.png
[8]: ./media/security-center-intro/security-solutions.png
