---
title: "Introducción a las directivas de seguridad de Azure Security Center | Microsoft Docs"
description: "Obtenga información sobre las directivas de Azure Security Center y sus principales funcionalidades."
services: security-center
documentationcenter: na
author: YuriDio
manager: MBaldwin
editor: 
ms.assetid: f24b1e4a-cc36-4542-b21e-041453cdfcd8
ms.service: security-center
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/13/2017
ms.author: yurid
ms.openlocfilehash: 7a4d056fa71f211dea5e7be649c39034eabc9395
ms.sourcegitcommit: b07d06ea51a20e32fdc61980667e801cb5db7333
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/08/2017
---
# <a name="security-policies-overview"></a>Introducción a las directivas de seguridad
En este documento se proporciona información general sobre las directivas de seguridad de Security Center.

## <a name="what-are-security-policies"></a>¿Qué son las directivas de seguridad?
Una directiva de seguridad define la configuración deseada de las cargas de trabajo. Además, ayuda a garantizar el cumplimiento de los requisitos de seguridad normativos o de la empresa. En Azure Security Center, puede definir directivas para las suscripciones de Azure y adaptarlas al tipo de carga de trabajo o a la confidencialidad de los datos. Por ejemplo, es posible que las aplicaciones que usan datos regulados, como la información de identificación personal, requieran un mayor nivel de seguridad que otras cargas de trabajo. 

Las directivas de Security Center contienen los componentes siguientes:

- **Recopilación de datos**: determina el aprovisionamiento del agente y la configuración de la [colección de datos](https://docs.microsoft.com/azure/security-center/security-center-enable-data-collection).
- **Directiva de seguridad**: determina qué controles Security Center supervisa y recomienda. Puede editar la [directiva de seguridad](https://docs.microsoft.com/azure/security-center/security-center-policies) en Security Center. También puede usar [Azure Policy](security-center-azure-policy.md) (en versión preliminar limitada) para crear definiciones, definir directivas adicionales y asignar directivas en los grupos de administración.
- **Notificaciones por correo electrónico**: determina la configuración de contactos de seguridad y de [notificaciones por correo electrónico](https://docs.microsoft.com/azure/security-center/security-center-provide-security-contact-details).
- **Plan de tarifa**: define la [selección de precios](https://docs.microsoft.com/azure/security-center/security-center-pricing) gratuitos o estándar. El plan elegido determina qué características de Security Center están disponibles para los recursos del ámbito. Puede especificar un plan para las suscripciones, los grupos de recursos y las áreas de trabajo. 


## <a name="who-can-edit-security-policies"></a>¿Quién puede editar directivas de seguridad?
Security Center usa el control de acceso basado en rol (RBAC), que proporciona roles integrados que se pueden asignar a usuarios, grupos y servicios en Azure. Cuando un usuario abre Security Center, solo ve la información relacionada con los recursos a los que tiene acceso. Esto significa que a los usuarios se les asigna el rol de *Propietario*, *Colaborador* o *Lector* para la suscripción o el grupo de recursos a los que pertenece un recurso. Además de estos roles, hay dos roles específicos de Security Center:

- **Lector de seguridad**: el usuario tiene derechos para ver Security Center, lo que incluye recomendaciones, alertas, directivas y estados, pero no puede realizar cambios.
- **Administrador de seguridad**: tiene los mismos derechos que el *lector de seguridad*, pero también puede actualizar la directiva de seguridad o descartar recomendaciones y alertas.


## <a name="next-steps"></a>Pasos siguientes
En este artículo ha aprendido a configurar directivas de seguridad en Azure Security Center. Para obtener más información sobre Azure Security Center, consulte los siguientes artículos:

* [Establecimiento de directivas de seguridad en Azure Security Center](security-center-policies.md): aprenda a configurar directivas de seguridad para las suscripciones y los grupos de recursos de Azure.
* [Administración de recomendaciones de seguridad en Azure Security Center](security-center-recommendations.md): aprenda cómo las recomendaciones de Security Center ayudan a proteger los recursos de Azure.
* [Supervisión del estado de seguridad en Azure Security Center](security-center-monitoring.md): obtenga información sobre cómo supervisar el mantenimiento de los recursos de Azure.
* [Administración y respuesta a las alertas de seguridad en Azure Security Center](security-center-managing-and-responding-alerts.md): obtenga información sobre cómo administrar y responder a alertas de seguridad.
* [Supervisión de las soluciones de asociados con Azure Security Center](security-center-partner-solutions.md): aprenda a supervisar el estado de mantenimiento de las soluciones de asociados.
- [Seguridad de datos de Azure Security Center](security-center-data-security.md): aprenda cómo Security Center administra y protege los datos.
* [Preguntas frecuentes acerca de Azure Security Center](security-center-faq.md): obtenga respuestas a las preguntas más frecuentes sobre cómo usar el servicio.
* [Blog de seguridad de Azure](http://blogs.msdn.com/b/azuresecurity/): obtenga las últimas noticias e información sobre la seguridad en Azure.


