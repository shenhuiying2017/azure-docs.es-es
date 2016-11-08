---
title: Información general sobre las alertas en Microsoft Azure | Microsoft Docs
description: Las alertas permiten supervisar los registros, los eventos o las métricas de recursos de Azure y recibir una notificación cuando se cumple una condición especificada.
author: rboucher
manager: ''
editor: ''
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics

ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/24/2016
ms.author: robb

---
# Información general sobre las alertas en Microsoft Azure
En este artículo se explica qué son las alertas, sus beneficios y cómo empezar a utilizarlas.

## ¿Qué son las alertas?
Las alertas son un método de supervisión de los registros, los eventos o las métricas de recursos de Azure, con la intención de recibir después una notificación cuando una condición especificada se cumple.

Puede recibir alertas basadas en:

* Valores de métrica: la alerta se desencadena cuando el valor de una métrica específica cruza un umbral asignado en cualquier dirección. Es decir, se desencadena tanto la primera vez que se cumple la condición como después, cuando dicha condición ya deja de cumplirse.
* Eventos de registro de actividades: una alerta puede desencadenarse en cada evento o solo cuando se producen una serie de eventos.

## Alertas en los distintos servicios de Azure
Las alertas están disponibles en los diferentes servicios

* **Application Insights**: admite las alertas de métricas y pruebas web. Vea [Definición de alertas en Application Insights](../application-insights/app-insights-alerts.md) y [Supervisión de la disponibilidad y la capacidad de respuesta de cualquier sitio web](../application-insights/app-insights-monitor-web-app-availability.md).
* **Log Analytics de OMS**: puede redirigir los registros de diagnóstico a Log Analytics. OMS admite las alertas de métricas y registros y de otro tipo. Para más información, vea [Alertas de Log Analytics](../log-analytics/log-analytics-alerts.md).
* **Azure Monitor**: admite alertas basadas en valores de métricas y en eventos de registro de actividades. Azure Monitor incluye la [API de REST de Insights para Azure](https://msdn.microsoft.com/library/dn931943.aspx). Vea también la información sobre cómo [utilizar Azure Portal, PowerShell o la interfaz de la línea de comandos para crear alertas](../monitoring-and-diagnostics/insights-alerts-portal.md).

## Acciones de alerta
Puede configurar una alerta para hacer lo siguiente cuando se desencadena:

* Enviar notificaciones de correo electrónico al administrador de servicios o a los coadministradores, o bien correos electrónicos adicionales que especifique.
* Llamar a un webhook, que permite iniciar acciones de automatización adicionales.
  
  ![Alertas explicadas.](./media/monitoring-overview-alerts/AlertsOverviewResource3.png)

## Pasos siguientes
Obtener información sobre las reglas de alertas y su configuración mediante:

* [Portal de Azure](../monitoring-and-diagnostics/insights-alerts-portal.md)
* [PowerShell](../monitoring-and-diagnostics/insights-alerts-powershell.md)
* [Interfaz de la línea de comandos (CLI)](insights-alerts-command-line-interface.md)
* [API de REST de Insights para Azure](https://msdn.microsoft.com/library/azure/dn931945.aspx)

<!---HONumber=AcomDC_0928_2016-->