---
title: Configuración de alertas para consultas en Stream Analytics | Microsoft Docs
description: Descripción de las entradas del Stream Analytics
keywords: configuración de alertas
services: stream-analytics
documentationcenter: ''
author: jseb225
manager: ryanw
ms.assetid: 9952e2cf-b335-4a5c-8f45-8d3e1eda2e20
ms.service: stream-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-services
ms.date: 06/26/2017
ms.author: jeanb
ms.openlocfilehash: b88d7e82ffcd2fab1845faf92f6d7a53b72cb54d
ms.sourcegitcommit: 34e0b4a7427f9d2a74164a18c3063c8be967b194
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 03/30/2018
---
# <a name="set-up-alerts-for-azure-stream-analytics-jobs"></a>Configuración de alertas en Azure Stream Analytics
## <a name="introduction-monitor-page"></a>Introducción: Página de supervisión
Puede configurar alertas para desencadenar una alerta cuando una métrica alcance una condición que especifique. Por ejemplo, puede configurar una alerta para una condición similar a la siguiente:

`If there are zero input events in the last 5 minutes, send email notification to sa-admin@example.com`

Las reglas se pueden configurar en las métricas a través del portal o se pueden configurar [mediante programación](https://code.msdn.microsoft.com/windowsazure/Receive-Email-Notifications-199e2c9a) sobre datos de registros de operaciones.

## <a name="set-up-alerts-in-the-azure-portal"></a>Configuración de alertas en Azure Portal
1. En Azure Portal, abra el trabajo de Stream Analytics para el que desea crear una alerta. 

2. En la hoja **Trabajo**, haga clic en la sección **Supervisión**.  

3. En la hoja **Métrica**, haga clic en el comando **Agregar alerta**.

      ![Configuración de Azure Portal](./media/stream-analytics-set-up-alerts/06-stream-analytics-set-up-alerts.png)  

4. Escriba un nombre y una descripción.

5. Use los selectores para definir la condición en la que se enviará la alerta.

6. Proporcione la información sobre dónde debería ir la alerta.

      ![Configuración de una alerta para un trabajo Azure Stream Analytics](./media/stream-analytics-set-up-alerts/stream-analytics-add-alert.png)  

Para más información sobre la configuración de alertas en Azure Portal, consulte el artículo sobre la [recepción de notificaciones de alerta](../monitoring-and-diagnostics/insights-receive-alert-notifications.md).  


## <a name="get-help"></a>Obtención de ayuda
Para obtener más ayuda, pruebe nuestro [foro de Azure Stream Analytics](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureStreamAnalytics)

## <a name="next-steps"></a>Pasos siguientes
* [Introducción a Azure Stream Analytics](stream-analytics-introduction.md)
* [Introducción al uso de Azure Stream Analytics](stream-analytics-get-started.md)
* [Escalación de trabajos de Azure Stream Analytics](stream-analytics-scale-jobs.md)
* [Referencia del lenguaje de consulta de Azure Stream Analytics](https://msdn.microsoft.com/library/azure/dn834998.aspx)
* [Referencia de API de REST de administración de Azure Stream Analytics](https://msdn.microsoft.com/library/azure/dn835031.aspx)

