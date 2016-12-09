---
title: "Recepción de notificaciones de alerta para los servicios de Azure | Microsoft Docs"
description: "Recibir una notificación cuando se cumplan las condiciones de las reglas de alerta."
author: rboucher
manager: carolz
editor: 
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.assetid: bc2fa75e-bc26-474e-af43-de30de5e604f
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/08/2015
ms.author: robb
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 5c929849eb71d5ebace7ef274700626d4c5e5f3e


---
# <a name="receive-alert-notifications"></a>Recibir notificaciones de alerta
Puede recibir una alerta basada en las métricas de supervisión para los servicios de Azure o los eventos sobre ellos.

Para una regla de alerta en un valor de métrica, cuando el valor de una métrica específica cruza un umbral asignado, la regla de alerta se activa y puede enviar una notificación. Para una regla de alerta en eventos, una regla puede enviar una notificación sobre *cada* evento o solo cuando se produzca un determinado número de eventos.

Cuando cree una regla de alerta, puede seleccionar opciones para enviar una notificación por correo electrónico al administrador y coadministradores del servicio o a otro administrador que pueda especificar. Se envía un correo electrónico de notificación cuando se activa la regla y cuando se resuelve una condición de alerta.

Puede usar la [API de REST](https://msdn.microsoft.com/library/azure/dn931945.aspx) o el [SDK de .NET](https://www.nuget.org/packages/Microsoft.Azure.Insights/) para configurar y obtener información acerca de las reglas de alertas mediante programación.

## <a name="create-an-alert-rule"></a>Crear una regla de alerta
1. En el [portal](https://portal.azure.com/), haga clic en **Examinar**y luego en el recurso que le interese supervisar.
2. En el modo **Operaciones**, haga clic en el icono **Reglas de alerta**.
3. Haga clic en el comando **Agregar alerta** .
   
    ![Agregar alerta](./media/insights-receive-alert-notifications/Insights_AddAlert.png)
4. Puede asignar un nombre a la regla de alerta y elegir una descripción que se mostrará en el correo electrónico de notificación.
5. Cuando seleccione **Métricas** , elegirá una condición y el valor de umbral para la métrica. Es el periodo que Azure usa para supervisar y trazar actividades de alerta.
   
    ![Condición y umbral](./media/insights-receive-alert-notifications/Insights_ConditionAndThreshold.png)
6. También puede elegir **Eventos**y obtener una notificación cuando se produzca un evento determinado.
   
    ![Eventos](./media/insights-receive-alert-notifications/Insights_Events.png)
7. Por último, puede enviar una notificación de correo electrónico a los administradores responsables.

Después de hacer clic en **Guardar**, al cabo de unos minutos se le informará si la métrica que eligió supera el umbral.

## <a name="managing-your-alert-rules"></a>Administrar las reglas de alertas
Cuando haya creado una regla de alerta, podrá ver una vista previa del umbral de alertas en comparación con la métrica del día anterior.

![Eventos](./media/insights-receive-alert-notifications/Insights_EditAlert.png)

Por supuesto, puede editar esta regla de alerta y elegir **Deshabilitar** o **Habilitar** si desea detener temporalmente la recepción de notificaciones sobre ella.

## <a name="next-steps"></a>Pasos siguientes
* [Configure enlaces web en las alertas](insights-webhooks-alerts.md) para enrutar las notificaciones a diversos canales
* [Supervise las métricas de servicio](insights-how-to-customize-monitoring.md) para asegurarse de que el servicio está disponible y que responde adecuadamente.
* [Habilite la supervisión y el diagnóstico](insights-how-to-use-diagnostics.md) para recopilar métricas detalladas de alta frecuencia en su servicio.
* [Supervise la disponibilidad y la capacidad de respuesta de cualquier página web](../application-insights/app-insights-monitor-web-app-availability.md) con Application Insights, para poder averiguar si su página está inactiva.
* [Supervise el rendimiento de la aplicación](../application-insights/app-insights-azure-web-apps.md) si desea comprender exactamente cómo funciona su código en la nube.
* [Vea eventos y registros de actividades](insights-debugging-with-events.md) para saber todo lo que ha sucedido en el servicio.
* [Realice el seguimiento del estado del servicio](insights-service-health.md) para averiguar cuándo ha sufrido  Azure interrupciones del servicio o degradación del rendimiento.




<!--HONumber=Nov16_HO3-->


