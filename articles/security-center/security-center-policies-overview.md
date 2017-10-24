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
ms.date: 09/26/2017
ms.author: yurid
ms.openlocfilehash: f85f7077e5227818d062d114370e7344601a998e
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="security-policies-overview"></a>Introducción a las directivas de seguridad
Este documento proporciona información general sobre las directivas de seguridad de Security Center.

## <a name="what-are-security-policies"></a>¿Qué son las directivas de seguridad?
Una directiva de seguridad define la configuración deseada de las cargas de trabajo. Además, ayuda a garantizar el cumplimiento de los requisitos de seguridad normativos o de la empresa. En Security Center, puede definir directivas para las suscripciones de Azure, que se pueden adaptar al tipo de carga de trabajo o la confidencialidad de los datos. Del mismo modo, es posible que las aplicaciones que usan datos regulados, como la información de identificación personal, requieran un mayor nivel de seguridad. 

Las directivas de Security Center contienen los componentes siguientes:

- [Recopilación de datos](https://docs.microsoft.com/azure/security-center/security-center-enable-data-collection): aprovisionamiento del agente y configuración de la colección de datos.
- [Directiva de seguridad](https://docs.microsoft.com/azure/azure-policy/azure-policy-introduction): una directiva de Azure que determina qué controles supervisa y recomienda Security Center. También puede editarlas en Security Center o usar Azure Policy para crear nuevas definiciones, definir directivas adicionales y asignar directivas en los grupos de administración.
- [Notificaciones por correo electrónico](https://docs.microsoft.com/azure/security-center/security-center-provide-security-contact-details): configuración de contactos de seguridad y notificaciones por correo electrónico.
- [Plan de tarifa](https://docs.microsoft.com/azure/security-center/security-center-pricing): selección de precios gratis o estándar, que determina qué características de Security Center están disponibles para los recursos en el ámbito (se puede especificar para suscripciones, grupos de recursos y áreas de trabajo). 


## <a name="who-can-edit-security-policies"></a>¿Quién puede editar directivas de seguridad?
Security Center usa el control de acceso basado en rol (RBAC), que proporciona roles integrados que se pueden asignar a usuarios, grupos y servicios en Azure. Cuando un usuario abre Security Center, solo ve la información relacionada con los recursos a los que tienen acceso. Esto significa que al usuario se le asigna el rol de Propietario, Colaborador o Lector para la suscripción o el grupo de recursos a los que pertenece un recurso. Además de estos roles, hay dos roles específicos de Security Center:

- Lector de seguridad: el usuario que pertenece a este rol puede ver los derechos para Security Center, lo que incluye recomendaciones, alertas, directivas y estados, pero no puede realizar cambios.
- Administrador de seguridad: igual que el lector de seguridad, pero también puede actualizar la directiva de seguridad o descartar recomendaciones y alertas.


## <a name="next-steps"></a>Pasos siguientes
En este documento ha aprendido a configurar directivas de seguridad en Azure Security Center. Para obtener más información sobre el Centro de seguridad de Azure, consulte los siguientes recursos:

* [Establecimiento de directivas de seguridad en Azure Security Center](security-center-policies.md) : aprenda a configurar directivas de seguridad para las suscripciones y los grupos de recursos de Azure.
* [Administración de recomendaciones de seguridad en Azure Security Center](security-center-recommendations.md) : recomendaciones que lo ayudan a proteger los recursos de Azure.
* [Supervisión del estado de seguridad en Azure Security Center](security-center-monitoring.md) : obtenga información sobre cómo supervisar el estado de los recursos de Azure.
* [Administración y respuesta a las alertas de seguridad en Azure Security Center](security-center-managing-and-responding-alerts.md) : obtenga información sobre cómo administrar y responder a alertas de seguridad.
* [Supervisión de las soluciones de asociados con Azure Security Center](security-center-partner-solutions.md) : aprenda a supervisar el estado de mantenimiento de las soluciones de asociados.
- [Seguridad de datos de Azure Security Center](security-center-data-security.md): aprenda cómo se administran y protegen los datos en Security Center.
* [Preguntas más frecuentes sobre Azure Security Center](security-center-faq.md) : encuentre las preguntas más frecuentes sobre el uso del servicio.
* [Blog de seguridad de Azure](http://blogs.msdn.com/b/azuresecurity/) : obtenga las últimas noticias e información sobre la seguridad en Azure.


