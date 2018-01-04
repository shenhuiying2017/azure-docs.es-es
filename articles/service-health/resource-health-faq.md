---
title: P+F sobre Azure Resource Health | Microsoft Docs
description: "Introducción a Azure Resource Health"
services: Resource health
documentationcenter: dev-center-name
author: BernardoAMunoz
manager: 
editor: 
ms.assetid: 85cc88a4-80fd-4b9b-a30a-34ff3782855f
ms.service: service-health
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: Supportability
ms.date: 07/05/2017
ms.author: BernardoAMunoz
ms.openlocfilehash: 692d03ad80989a969a73b15bb9042a01c286d1ec
ms.sourcegitcommit: 68aec76e471d677fd9a6333dc60ed098d1072cfc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/18/2017
---
# <a name="azure-resource-health-faq"></a>P+F sobre Azure Resource Health
Aprenda las respuestas a las preguntas más frecuentes sobre Azure Resource Health.

## <a name="what-is-azure-resource-health"></a>¿Qué es Azure Resource Health?
Resource Health le ayuda a diagnosticar y obtener soporte técnico cuando un problema de Azure afecta a sus recursos. Le informa acerca del mantenimiento actual y pasado de los recursos y le ayuda a mitigar los problemas. Resource Health le proporciona soporte técnico si necesita ayuda con los problemas de los servicios de Azure.  

## <a name="what-is-the-resource-health-intended-for"></a>¿Para qué está pensado Resource Health?
Cuando se detecta un problema con un recurso, Resource Health puede ayudarle a diagnosticar la causa principal. Proporciona ayuda para mitigar el problema y el soporte técnico cuando necesita más ayuda para solucionar los problemas con el servicio de Azure.

## <a name="what-health-checks-are-performed-by-resource-health"></a>¿Qué comprobaciones de estado realiza Resource Health?
Resource Health realiza varias comprobaciones según el [tipo de recurso](resource-health-checks-resource-types.md). Estas comprobaciones están diseñadas para implementar tres tipos de problemas: 
- Eventos no planeados, por ejemplo, un reinicio inesperado del host
- Eventos planeados, como actualizaciones programadas del sistema operativo del host
- Eventos desencadenados por acciones de usuario, por ejemplo, un usuario que reinicia una máquina virtual

## <a name="what-does-each-of-the-health-status-mean"></a>¿Qué significa cada uno de los estados de mantenimiento?
Hay tres estados de mantenimiento distintos:
- Disponible: no hay problemas conocidos en la plataforma de Azure que pudieran afectar a este recurso
- No disponible: Resource Health detectó problemas que afectan al recurso
- Desconocido: Resource Health no puede determinar el estado de un recurso porque dejó de recibir información sobre el mismo. 

## <a name="what-does-the-unknown-status-mean-is-something-wrong-with-my-resource"></a>¿Qué significa el estado desconocido? ¿Hay problemas con mi recurso?
El estado es desconocido cuando Resource Health deja de recibir información sobre un recurso específico. Si bien este estado no es una indicación definitiva del estado del recurso, en casos donde experimenta problemas podría indicar que hay un problema con Azure.

## <a name="how-can-i-get-help-for-a-resource-that-is-unavailable"></a>¿Cómo puedo obtener ayuda para un recurso no disponible?
Puede enviar una solicitud de soporte técnico desde la hoja Resource Health. No necesita tener un contrato de soporte técnico con Microsoft para abrir una solicitud cuando el recurso no está disponible debido a eventos de plataforma.

## <a name="does-resource-health-differentiate-between-unavailability-cased-by-platform-problems-versus-something-i-did"></a>¿Diferencia Resource Health entre la falta de disponibilidad por problemas de la plataforma o por alguna acción mía?
Sí, cuando un recurso no está disponible, Resource Health incluye la causa principal en una de estas categorías: 
-   Acción iniciada por el usuario
-   Evento planeado 
-   Evento no planeado

En el portal, las acciones iniciadas por el usuario aparecen con un icono de notificación azul, mientras que los eventos planeados y no planeados se muestran con un icono de advertencia de color rojo. Se proporcionan más detalles en [Información general sobre Resource Health](Resource-health-overview.md).  

## <a name="can-i-integrate-resource-health-with-my-monitoring-tools"></a>¿Puedo integrar Resource Health con mis herramientas de supervisión?
Resource Health es un servicio diseñado para ayudarle a diagnosticar y mitigar problemas de servicio de Azure que afectan sus recursos. Si bien puede usar la API de Resource Health para obtener el estado mediante programación, se recomienda usar métricas para supervisar los recursos. Cuando se detecta un problema, Resource Health le ayuda a determinar la causa principal y le guía con acciones para abordarlo. Visite [Azure Monitor](https://docs.microsoft.com/azure/monitoring-and-diagnostics/) para más información sobre cómo puede usar métricas para comprobar los recursos.

## <a name="where-do-i-find-resource-health"></a>¿Dónde se encuentra Resource Health?
Después de iniciar sesión en Azure Portal, puede acceder a Resource Health de varias maneras:
- Vaya al recurso. En el panel de navegación de la izquierda, seleccione **Resource Health**
- Vaya a la hoja Azure Monitor.  En el panel de navegación de la izquierda, seleccione **Resource Health**.
- Seleccione el signo de interrogación que se encuentra en la esquina superior derecha del portal y seleccione **Ayuda y soporte técnico** para abrir la hoja **Ayuda y soporte técnico**. Cuando se abra la hoja, seleccione **Resource Health**

También puede usar la API de Resource Health para obtener información sobre el estado de los recursos.

## <a name="is-resource-health-available-for-all-resource-types"></a>¿Está Resource Health disponible para todos los tipos de recursos?
La lista de comprobaciones de estado y los tipos de recursos compatibles con Resource Health se encuentran [aquí](resource-health-checks-resource-types.md).

## <a name="what-should-i-do-if-my-resource-is-showing-available-but-i-believe-it-is-not"></a>¿Qué debo hacer si mi recurso aparece como disponible pero creo que no lo está?
Cuando se comprueba el estado de un recurso, justo debajo del estado de mantenimiento puede hacer clic en **Informe de estado de mantenimiento incorrecto**. Antes de enviar el informe, tiene la opción de proporcionar detalles adicionales sobre por qué cree que el estado de mantenimiento actual no es correcto.

## <a name="is-resource-health-available-for-all-azure-regions"></a>¿Está Resource Health disponible en todas las regiones de Azure? 
Resource Health está disponible en todas las geoáreas de Azure, excepto en las regiones siguientes:
- Gobierno de EE. UU. - Virginia
- Gobierno de EE. UU. - Iowa
- Departamento de Defensa de EE. UU. Este
- Departamento de Defensa de EE. UU. Centro
- Centro de Alemania
- Noreste de Alemania

## <a name="how-is-resource-health-different-from-the-service-health-dashboard-or-the-azure-portal-service-notifications"></a>¿Qué diferencia hay entre Resource Health y el panel de estado del servicio o las notificaciones del servicio Azure Portal?
La información que Resource Health proporciona es más específica que la que proporciona el panel de estado del servicio de Azure.

Mientras que las notificaciones del servicio del portal y [Estado de Azure](https://status.azure.com) le informan sobre problemas de servicio que afectan a un amplio conjunto de clientes (por ejemplo, una región de Azure), Resource Health expone eventos más pormenorizados que solo afectan a un recurso específico. Por ejemplo, si un host se reinicia de improviso, Resource Health solo alerta a los clientes cuyas máquinas virtuales se ejecutaban en ese host.

Es importante tener en cuenta que, para proporcionar visibilidad completa de los eventos que afectan a los recursos, Resource Health también expone los eventos publicados en las notificaciones del servicio y el panel de estado del servicio.

## <a name="do-i-need-to-activate-resource-health-for-each-resource"></a>¿Es necesario activar Resource Health para cada recurso?
No, la información de estado está disponible para todos los tipos de recursos a través de Resource Health. 

## <a name="do-we-need-to-enable-resource-health-for-my-organization"></a>¿Es necesario habilitar Resource Health para la organización?
Nº  Azure Resource Health es accesible desde Azure Portal, sin requisitos de configuración.

## <a name="is-resource-health-available-free-of-charge"></a>¿Está Resource Health disponible de forma gratuita?
Sí.  Azure Resource Health es gratuito.

## <a name="what-are-the-recommendations-that-resource-health-provides"></a>¿Qué recomendaciones proporciona Resource Health?
Según el estado, Resource Health proporciona recomendaciones con el objetivo de disminuir el tiempo dedicado a solucionar problemas. En el caso de los recursos disponibles, las recomendaciones se centran en cómo solucionar los problemas más comunes que encuentran los clientes. Si el recurso no está disponible debido a un evento no planeado de Azure, el foco estará en ayudarle durante el proceso de recuperación y después de este. 

## <a name="next-steps"></a>pasos siguientes

Más información sobre Resource Health:
-  [Información general sobre Azure Resource Health](Resource-health-overview.md)
-  [Tipos de recursos y comprobaciones de estado disponibles con Azure Resource Health](resource-health-checks-resource-types.md)
