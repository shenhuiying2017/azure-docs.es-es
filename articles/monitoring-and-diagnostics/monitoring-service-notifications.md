---
title: Notificaciones de estado del servicio | Microsoft Docs
description: Las notificaciones de estado del servicio permiten ver los mensajes que publica Microsoft Azure acerca del estado de un servicio.
author: anirudhcavale
manager: orenr
editor: 
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.assetid: 
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/31/2017
ms.author: ancav
ms.openlocfilehash: d85281c02b792921f12cc62e6d60bef3e7c13b3f
ms.sourcegitcommit: 02e69c4a9d17645633357fe3d46677c2ff22c85a
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 08/03/2017
---
# <a name="service-health-notifications"></a>Notificaciones de estado del servicio
## <a name="overview"></a>Información general

En este artículo se explica cómo ver las notificaciones de estado del servicio mediante Azure Portal.

Las notificaciones de estado del servicio permiten ver los mensajes que publica el equipo de Azure acerca del estado de un servicio que puedan afectar a los recursos de su suscripción. Estas notificaciones son una subclase de los eventos del registro de actividad y también se pueden encontrar en la hoja del registro de actividad. En función de la clase, las notificaciones de estado del servicio pueden ser meramente informativas o pueden requerir acciones.

Hay cinco clases de las notificaciones de estado del servicio:  

- **Acción requerida:** de vez en cuando podemos percibir que algo inusual sucede en su cuenta. Es posible que tengamos que trabajar conjuntamente para remediar dicho asunto. Le enviaremos una notificación en el que detallamos las acciones que debe realizar o le indicamos cómo ponerse en contacto con los equipos de ingeniería o soporte técnico de Azure.  
- **Recuperación asistida:** se ha producido un evento y los ingenieros han confirmado que todavía le afecta. El equipo de ingeniería necesitará trabajar con usted directamente para restaurar los servicios.  
- **Incidente:** uno o varios recursos de la suscripción están resultando afectados por un evento que tiene repercusiones es un servicio.  
- **Mantenimiento:** se trata de una notificación que le informa de una actividad de mantenimiento planeada que puede afectar a uno o varios de los recursos de su suscripción.  
- **Información:** de vez en cuando podemos enviarle notificaciones que le comunican posibles optimizaciones que puede ayudarle a mejorar la utilización de los recursos.  
- **Seguridad:** información urgente relacionada con la seguridad de las soluciones que se ejecutan en Azure.

Cada notificación del estado de un servicio transmitirá datos acerca del ámbito y el impacto en los recursos. Dichos datos incluyen:

Nombre de propiedad | Descripción
-------- | -----------
canales nueva | Es uno de los siguientes valores: "Admin", "Operation"
correlationId | Normalmente es un GUID en formato de cadena. Los eventos que pertenecen a la misma acción general suelen compartir el mismo valor de correlationId.
eventDataId | Es el identificador único de un evento
eventName | Es el título del evento
level | Nivel del evento. Uno de los siguientes valores: "Critical", "Error", "Warning", "Informational" y "Verbose"
resourceProviderName | Nombre del proveedor de recursos del recurso afectado.
resourceType| El tipo del recurso afectado
subStatus | Por lo general, el código de estado HTTP de la llamada de REST correspondiente, pero también puede incluir otras cadenas que describen un subestado, como estos valores habituales: OK (código de estado HTTP: 200), Created (código de estado HTTP: 201), Accepted (código de estado HTTP: 202), No Content (código de estado HTTP: 204), Bad Request (código de estado HTTP: 400), Not Found (código de estado HTTP: 404), Conflict (código de estado HTTP: 409), Internal Server Error (código de estado HTTP: 500), Service Unavailable (código de estado HTTP: 503) y Gateway Timeout (código de estado HTTP: 504).
eventTimestamp | Marca de tiempo de cuándo el servicio de Azure generó el evento que procesó la solicitud correspondiente al evento.
submissionTimestamp |   Marca de tiempo de cuándo el evento empezó a estar disponible para las consultas.
subscriptionId | La suscripción de Azure en la que se registró la este evento
status | Cadena que describe el estado de la operación. Algunos valores habituales son: Started, In Progress, Succeeded, Failed, Active y Resolved.
operationName | Nombre de la operación.
categoría | "ServiceHealth"
resourceId | Identificador de recurso del recurso afectado.
Properties.title | El título localizado de esta comunicación. El idioma predeterminado es inglés.
Properties.communication | Los detalles localizados de la comunicación con el formato HTML. El valor predeterminado es inglés.
Properties.incidentType | Valores posibles: AssistedRecovery, ActionRequired, Information, Incident, Maintenance, Security
Properties.trackingId | Identifica el incidente al que está asociado este evento. Se utiliza para correlacionar los eventos relacionados con un incidente.
Properties.impactedServices | Un blob JSON con escape que describe los servicios y regiones a los que afecta el incidente. Una lista de servicios, cada uno de los cuales tiene un valor ServiceName y una lista de ImpactedRegions, cada una de los cuales tiene un valor RegionName.
Properties.defaultLanguageTitle | La comunicación en inglés
Properties.defaultLanguageContent | La comunicación en inglés en formato html o como texto sin formato
Properties.stage | Valores posibles de AssistedRecovery, ActionRequired, Information, Incident, Maintenance, Security: Active y Resolved. En el caso de Maintenance son: Active, Planned, InProgress, Canceled, Rescheduled, Resolved y Complete
Properties.communicationId | La comunicación de este evento está asociada.


## <a name="viewing-your-service-health-notifications-in-the-azure-portal"></a>Visualización de las notificaciones de estado del servicio en Azure Portal
1.  En el [portal](https://portal.azure.com), navegue hasta el servicio **Monitor**.

    ![Supervisión](./media/monitoring-service-notifications/home-monitor.png)
2.  Haga clic en la opción **Monitor** para abrir la hoja Monitor. En esta hoja se encuentran toda la configuración de supervisión y los datos en una sola vista consolidada. Primero se abre la sección **Registro de actividades** .

3.  Ahora, haga clic en la sección **Notificaciones del servicio**

    ![Supervisión](./media/monitoring-service-notifications/service-health-summary.png)
4.  Haga clic en cualquiera de los elementos de línea para ver más detalles

5. Haga clic en la operación **+ Agregar alerta de registro de actividad** para recibir notificaciones para asegurarse de que se recibe las futuras notificaciones del servicio de este tipo. Para más información acerca de cómo configurar alertas en las notificaciones del servicio [haga clic aquí](monitoring-activity-log-alerts-on-service-notifications.md)

## <a name="next-steps"></a>Pasos siguientes:
Reciba [notificaciones de alertas cada vez que se publique una notificación de estado del servicio](monitoring-activity-log-alerts-on-service-notifications.md)  
Más información sobre las [alertas del registro de actividad](monitoring-activity-log-alerts.md)
