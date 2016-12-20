---
title: Seguimiento del estado del servicio de Azure mediante registros de actividades de Azure Monitor | Microsoft Docs
description: "Averigüe en qué momentos ha sufrido Azure interrupciones del servicio o degradación del rendimiento. "
author: rboucher
manager: carolz
editor: 
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.assetid: 30db9258-98e6-4855-abcd-f06c06277aa8
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/20/2016
ms.author: robb
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: d31ee12c783b845e0a8ff1791d21b2ef9013eee3


---
# <a name="track-azure-service-health-using-azure-monitor-activity-logs"></a>Seguimiento del estado del servicio de Azure mediante registros de actividad de Azure Monitor
Azure realiza un anuncio cada vez que hay una interrupción del servicio o una degradación del rendimiento. Estos eventos se pueden examinar en Azure Portal y también se pueden utilizar la [API de REST](https://msdn.microsoft.com/library/azure/dn931927.aspx) o el [SDK de .NET](https://www.nuget.org/packages/Microsoft.Azure.Insights/) para obtener acceso mediante programación a todo el conjunto de eventos.

## <a name="browse-the-service-health-logs-for-your-subscription"></a>Examen de los registros de estado del servicio para la suscripción
1. Inicie sesión en el [Portal de Azure](https://portal.azure.com/).
2. En **Inicio** debería ver un icono denominado **Estado del servicio**. Haga clic en él.
   
    ![Inicio](./media/insights-service-health/Insights_Home.png)
3. Vea una lista de todas las regiones de Azure. Haga clic en cualquier región para que aparezca la consulta del registro de actividad que muestra los incidentes de servicio que han influido en cualquiera de sus suscripciones en las últimas 24 horas.
   
    ![Estado del servicio de la suscripción del registro de actividad](./media/insights-service-health/AzureActivityLogServiceHealth3.png)
4. También puede ver los detalles de cualquier incidente individual, para lo que debe hacer clic en el mismo en la tabla.
5. Cambie el valor de **Intervalo de tiempo** para ver un intervalo de tiempo más largo.

## <a name="next-steps"></a>Pasos siguientes
* [Supervise la disponibilidad y la capacidad de respuesta de cualquier página web](../application-insights/app-insights-monitor-web-app-availability.md) con Application Insights, para poder averiguar si su página está inactiva.




<!--HONumber=Nov16_HO3-->


