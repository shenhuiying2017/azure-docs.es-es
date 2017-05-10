---
title: "Configuración de alertas para consultas en Stream Analytics | Microsoft Docs"
description: "Descripción de las entradas del Análisis de transmisiones"
keywords: "configuración de alertas"
services: stream-analytics
documentationcenter: 
author: jeffstokes72
manager: jhubbard
editor: cgronlun
ms.assetid: 9952e2cf-b335-4a5c-8f45-8d3e1eda2e20
ms.service: stream-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-services
ms.date: 03/28/2017
ms.author: jeffstok
ms.translationtype: Human Translation
ms.sourcegitcommit: 7f8b63c22a3f5a6916264acd22a80649ac7cd12f
ms.openlocfilehash: d9578aa13fc22a148f76d95a2c548b88b699e723
ms.contentlocale: es-es
ms.lasthandoff: 05/01/2017


---
# <a name="set-up-alerts-for-azure-stream-analytics-jobs"></a>Configuración de alertas en Análisis de transmisiones de Azure
## <a name="introduction-monitor-page"></a>Introducción: Página de supervisión
Puede configurar alertas para desencadenar una alerta cuando una métrica alcance una condición que especifique.

Por ejemplo, "Si los eventos de salida para los últimos 15 minutos son <100, enviar notificación por correo electrónico al identificador de correo electrónico: xyz@company.com".

Las reglas se pueden configurar en las métricas a través del portal o se pueden configurar [mediante programación](https://code.msdn.microsoft.com/windowsazure/Receive-Email-Notifications-199e2c9a) sobre datos de registros de operaciones.

## <a name="set-up-alerts-through-the-azure-classic-portal"></a>Configuración de alertas a través del Portal de Azure clásico
Hay dos maneras de configurar alertas en el Portal de Azure clásico:  

1. La pestaña **Supervisar** del trabajo de Análisis de transmisiones  
2. El registro de operaciones en los servicios de administración  

## <a name="set-up-alert-through-the-monitor-tab-of-the-job-in-the-portal"></a>Configuración de alertas a través de la pestaña Supervisar del trabajo en el portal
1. Seleccione la métrica en la pestaña Supervisar, haga clic en el botón **Agregar regla** en la parte inferior del panel y configure las reglas.  
   
   ![Panel](./media/stream-analytics-set-up-alerts/01-stream-analytics-set-up-alerts.png)  
2. Definir un nombre y una descripción de la alerta  
   
   ![Crear regla](./media/stream-analytics-set-up-alerts/02-stream-analytics-set-up-alerts.png)  
3. Especificar los umbrales, la ventana de evaluación de alerta y las acciones para la alerta  
   
   ![Definir condiciones](./media/stream-analytics-set-up-alerts/03-stream-analytics-set-up-alerts.png)  

## <a name="set-up-alerts-through-the-operations-logs"></a>Configuración de alertas a través de registros de operaciones
1. Vaya a la pestaña **Alertas** en Servicios de administración en el [Portal de Azure clásico](https://manage.windowsazure.com).  
2. Haga clic en **Agregar regla**  
   
   ![Criterios](./media/stream-analytics-set-up-alerts/04-stream-analytics-set-up-alerts.png)  
3. Defina el nombre y una descripción de la alerta. Seleccione "Análisis de secuencia" como tipo de servicio y el nombre del trabajo como el nombre del servicio.  
   
   ![Definir alerta](./media/stream-analytics-set-up-alerts/05-stream-analytics-set-up-alerts.png)  

## <a name="set-up-alerts-in-the-azure-portal"></a>Configuración de alertas en Azure Portal
En Azure Portal, vaya al trabajo de Stream Analytics sobre el que le interesa alertar y haga clic en la sección **Supervisión** .  En la hoja **Métrica** que se abre, haga clic en el comando **Agregar alerta**.

  ![Configuración de Azure Portal](./media/stream-analytics-set-up-alerts/06-stream-analytics-set-up-alerts.png)  

Puede asignar un nombre a la regla de alerta y elegir una descripción que se mostrará en el correo electrónico de notificación.

Cuando seleccione Métricas, elegirá una condición y el valor de umbral para la métrica.

  ![Selección de métrica en Azure Portal](./media/stream-analytics-set-up-alerts/07-stream-analytics-set-up-alerts.png)  

Para más información sobre la configuración de alertas en Azure Portal, consulte el artículo sobre la [recepción de notificaciones de alerta](../monitoring-and-diagnostics/insights-receive-alert-notifications.md).  

## <a name="get-help"></a>Obtener ayuda
Para obtener más ayuda, pruebe nuestro [foro de Análisis de transmisiones de Azure](https://social.msdn.microsoft.com/Forums/home?forum=AzureStreamAnalytics)

## <a name="next-steps"></a>Pasos siguientes
* [Introducción al Análisis de transmisiones de Azure](stream-analytics-introduction.md)
* [Introducción al uso de Análisis de transmisiones de Azure](stream-analytics-get-started.md)
* [Escalación de trabajos de Análisis de transmisiones de Azure](stream-analytics-scale-jobs.md)
* [Referencia del lenguaje de consulta de Análisis de transmisiones de Azure](https://msdn.microsoft.com/library/azure/dn834998.aspx)
* [Referencia de API de REST de administración de Análisis de transmisiones de Azure](https://msdn.microsoft.com/library/azure/dn835031.aspx)


