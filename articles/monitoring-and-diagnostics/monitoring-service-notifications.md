---
title: "¿Qué son las notificaciones de mantenimiento del servicio de Azure? | Microsoft Docs"
description: Las notificaciones de mantenimiento del servicio permiten ver los mensajes de mantenimiento del servicio que publica Microsoft Azure.
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
ms.openlocfilehash: 4a95e9882515e6a2861292829a44847e11f39063
ms.sourcegitcommit: 9292e15fc80cc9df3e62731bafdcb0bb98c256e1
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/10/2018
---
# <a name="view-service-health-notifications-by-using-the-azure-portal"></a>Visualización de las notificaciones de mantenimiento del servicio mediante Azure Portal

Azure publica las notificaciones de mantenimiento del servicio, que contienen información acerca de los recursos en la suscripción. Estas notificaciones son una subclase de los eventos del registro de actividad y también se pueden encontrar en el registro de actividad. En función de la clase, las notificaciones de mantenimiento del servicio pueden ser meramente informativas o pueden requerir acciones.

Hay varias clases de las notificaciones de mantenimiento del servicio:  

- **Acción requerida:** Azure puede haber observado algo inusual en su cuenta y trabaja con usted para remediarlo. Azure le envía una notificación en la que se detallan las acciones que debe realizar o se proporciona información sobre cómo ponerse en contacto con los equipos de ingeniería o soporte técnico de Azure.  
- **Recuperación asistida:** se ha producido un evento y los ingenieros han confirmado que todavía le afecta. El equipo de ingeniería de Azure necesitará trabajar con usted directamente para restaurar completamente los servicios.  
- **Incidente:** uno o varios recursos de la suscripción están resultando afectados por un evento que tiene repercusiones en el servicio.  
- **Mantenimiento:** una actividad de mantenimiento planeado que puede afectar a uno o varios de los recursos de su suscripción.  
- **Información:** posibles optimizaciones que puedan ayudar a mejoran el uso de los recursos. 
- **Seguridad:** información urgente relacionada con la seguridad de las soluciones que se ejecutan en Azure.

Cada notificación del estado de un servicio incluye datos acerca del ámbito y el impacto en los recursos. Los detalles incluyen:

Nombre de propiedad | DESCRIPCIÓN
-------- | -----------
canales nueva | Uno de los siguientes valores: **Admin** u **Operation**.
correlationId | Normalmente, un GUID en formato de cadena. Los eventos que pertenecen a la misma acción generalmente suelen compartir el mismo valor de correlationId.
eventDataId | Identificador único de un evento.
eventName | Título del evento.
level | Nivel de un evento. Uno de los siguientes valores: **Critical**, **Error**, **Warning** o **Informational**.
resourceProviderName | Nombre del proveedor de recursos del recurso afectado.
resourceType| Tipo del recurso afectado.
subStatus | Normalmente el código de estado HTTP de la llamada REST correspondiente, pero también puede incluir otras cadenas que describen un subestado. Por ejemplo: OK (código de estado HTTP: 200), Created (código de estado HTTP: 201), Accepted (código de estado HTTP: 202), No Content (código de estado HTTP: 204), Bad Request (código de estado HTTP: 400), Not Found (código de estado HTTP: 404), Conflict (código de estado HTTP: 409), Internal Server Error (código de estado HTTP: 500), Service Unavailable (código de estado HTTP: 503) y Gateway Timeout (código de estado HTTP: 504).
eventTimestamp | Marca de tiempo de cuándo generó el evento el servicio de Azure que procesó la solicitud correspondiente al evento.
submissionTimestamp | Marca de tiempo de cuándo el evento empezó a estar disponible para las consultas.
subscriptionId | Suscripción de Azure en la que se registró este evento.
status | Cadena que describe el estado de la operación. Algunos valores habituales son: **Started**, **In Progress**, **Succeeded**, **Failed**, **Active** y **Resolved**.
operationName | Nombre de la operación.
categoría | Esta propiedad es siempre **ServiceHealth**.
ResourceId | Identificador de recurso del recurso afectado.
Properties.title | El título localizado de esta comunicación. El valor predeterminado es inglés.
Properties.communication | Los detalles localizados de la comunicación con el formato HTML. El valor predeterminado es inglés.
Properties.incidentType | Uno de los siguientes valores: **AssistedRecovery**, **ActionRequired**, **Information**, **Incident**, **Maintenance** o **Security**.
Properties.trackingId | El incidente al que está asociado este evento. Se utiliza para correlacionar los eventos relacionados con un incidente.
Properties.impactedServices | Un blob JSON con caracteres de escape que describe los servicios y regiones a los que afecta el incidente. Una lista de servicios, cada uno de los cuales tiene un valor de **ServiceName** y una lista de regiones afectadas, cada una de los cuales tiene un valor de **RegionName**.
Properties.defaultLanguageTitle | La comunicación en inglés.
Properties.defaultLanguageContent | La comunicación en inglés en formato HTML o como texto sin formato.
Properties.stage | Los valores posibles de **AssistedRecovery**, **ActionRequired**, **Information**, **Incident** y **Security** son **Active** y **Resolved**. En el caso de **Maintenance** son: **Active**, **Planned**, **InProgress**, **Canceled**, **Rescheduled**, **Resolved** y **Complete**.
Properties.communicationId | La comunicación con la cual está asociado este evento.


## <a name="view-your-service-health-notifications-in-the-azure-portal"></a>Visualización de las notificaciones de mantenimiento del servicio en Azure Portal
1.  En [Azure Portal](https://portal.azure.com), seleccione **Supervisión**.

    ![Captura de pantalla del menú de Azure Portal, con el Monitor seleccionado](./media/monitoring-service-notifications/home-monitor.png)

    Azure Monitor se abre junto con toda la configuración de supervisión y los datos en una sola vista consolidada. Primero se abre la sección **Registro de actividades** .

3.  Seleccione **Alertas**.

    ![Captura de pantalla del registro de actividad del Monitor, con Alertas seleccionado](./media/monitoring-service-notifications/service-health-summary.png)
4. Seleccione **+Agregar alerta del registro de actividad** y configure una alerta para asegurarse de que recibe las futuras notificaciones del servicio. Para más información, consulte [Creación de alertas del registro de actividad en notificaciones del servicio](monitoring-activity-log-alerts-on-service-notifications.md).

## <a name="next-steps"></a>pasos siguientes
Reciba [notificaciones de alertas con cada notificación de mantenimiento del servicio](monitoring-activity-log-alerts-on-service-notifications.md).  
Más información sobre las [alertas del registro de actividad](monitoring-activity-log-alerts.md).
