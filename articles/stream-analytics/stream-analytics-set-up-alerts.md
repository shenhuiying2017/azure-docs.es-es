---
title: Configuración de alertas de supervisión en Azure Stream Analytics
description: En este artículo se describe cómo usar Azure Portal para configurar la supervisión y las alertas para los trabajos de Azure Stream Analytics.
services: stream-analytics
author: jseb225
ms.author: jeanb
manager: kfile
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 06/26/2017
ms.openlocfilehash: fb69f1b52e5b17dd6794c896969c29003a929c9b
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 04/16/2018
ms.locfileid: "31412117"
---
# <a name="tutorial-set-up-alerts-for-azure-stream-analytics-jobs"></a>Tutorial: configuración de alertas para los trabajos de Azure Stream Analytics
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
Para obtener más ayuda, pruebe nuestro [foro de Azure Stream Analytics](https://social.msdn.microsoft.com/Forums/azure/home?forum=AzureStreamAnalytics)

## <a name="next-steps"></a>Pasos siguientes
* [Introducción a Azure Stream Analytics](stream-analytics-introduction.md)
* [Introducción al uso de Azure Stream Analytics](stream-analytics-get-started.md)
* [Escalación de trabajos de Azure Stream Analytics](stream-analytics-scale-jobs.md)
* [Referencia del lenguaje de consulta de Azure Stream Analytics](https://msdn.microsoft.com/library/azure/dn834998.aspx)
* [Referencia de API de REST de administración de Azure Stream Analytics](https://msdn.microsoft.com/library/azure/dn835031.aspx)

