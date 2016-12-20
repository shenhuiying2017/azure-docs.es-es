---
title: "Información general sobre las alertas en Microsoft Azure | Microsoft Docs"
description: "Las alertas permiten supervisar los registros, los eventos o las métricas de recursos de Azure y recibir una notificación cuando se cumple una condición especificada."
author: rboucher
manager: carolz
editor: 
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.assetid: a6dea224-57bf-43d8-a292-06523037d70b
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/24/2016
ms.author: robb
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: f80179a487d08244cbd5e57d34b10c4ab67b3cf5


---
# <a name="overview-of-alerts-in-microsoft-azure"></a>Información general sobre las alertas en Microsoft Azure
En este artículo se explica qué son las alertas, sus beneficios y cómo empezar a utilizarlas.  

## <a name="what-are-alerts"></a>¿Qué son las alertas?
Las alertas son un método de supervisión de los registros, los eventos o las métricas de recursos de Azure, con la intención de recibir después una notificación cuando una condición especificada se cumple.

Puede recibir alertas basadas en:

* **Valores de métrica**: la alerta se desencadena cuando el valor de una métrica específica cruza un umbral asignado en cualquier dirección. Es decir, se desencadena tanto la primera vez que se cumple la condición como después, cuando dicha condición ya deja de cumplirse.
* **Eventos de registro de actividades**: esta alerta puede desencadenarse en cada evento o solo cuando se producen una serie de eventos.

## <a name="alerts-in-different-azure-services"></a>Alertas en los distintos servicios de Azure
Las alertas están disponibles en los diferentes servicios, incluidos:

* **Application Insights**: admite las alertas de métricas y pruebas web. Vea [Definición de alertas en Application Insights](../application-insights/app-insights-alerts.md) y [Supervisión de la disponibilidad y la capacidad de respuesta de cualquier sitio web](../application-insights/app-insights-monitor-web-app-availability.md).
* **Log Analytics (Operations Management Suite)**: habilita el enrutamiento de los registros de diagnóstico para Log Analytics. Operations Management Suite permite métrica, registro y otros tipos de alerta. Para más información, vea [Alertas de Log Analytics](../log-analytics/log-analytics-alerts.md).  
* **Azure Monitor**: admite alertas basadas en valores de métricas y en eventos de registro de actividades. Azure Monitor incluye la [API de REST de Azure Monitor](https://msdn.microsoft.com/library/dn931943.aspx).  Para más información, vea la información sobre cómo [utilizar Azure Portal, PowerShell o la interfaz de la línea de comandos para crear alertas](insights-alerts-portal.md).

## <a name="alert-actions"></a>Acciones de alerta
Puede configurar una alerta para hacer lo siguiente:

* Enviar notificaciones de correo electrónico al administrador de servicios o a los coadministradores, o a las direcciones de correo electrónico adicionales que especifique.
* Llamar a un webhook, que permite iniciar acciones de automatización adicionales.
  
  ![Alertas explicadas](./media/monitoring-overview-alerts/AlertsOverviewResource3.png)

## <a name="next-steps"></a>Pasos siguientes
Obtener información sobre las reglas de alertas y su configuración mediante:

* [Portal de Azure](insights-alerts-portal.md)
* [PowerShell](insights-alerts-powershell.md)
* [Interfaz de la línea de comandos (CLI)](insights-alerts-command-line-interface.md)
* [API de REST de Azure Monitor](https://msdn.microsoft.com/library/azure/dn931945.aspx)




<!--HONumber=Nov16_HO3-->


