---
title: "Creación de alertas para servicios de Azure - Azure Portal | Microsoft Docs"
description: "Desencadenamiento de correos electrónicos y notificaciones, y llamadas a direcciones URL de sitios web (webhooks) o a la automatización cuando se cumplen las condiciones especificadas."
author: rboucher
manager: carmonm
editor: 
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.assetid: f7457655-ced6-4102-a9dd-7ddf2265c0e2
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/23/2016
ms.author: robb
ms.openlocfilehash: 3e09c145d35665ec1c2467b60f06191ac51a5c16
ms.sourcegitcommit: b5c6197f997aa6858f420302d375896360dd7ceb
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/21/2017
---
# <a name="create-metric-alerts-in-azure-monitor-for-azure-services---azure-portal"></a>Creación de alertas de métricas en Azure Monitor para servicios de Azure: Azure Portal
> [!div class="op_single_selector"]
> * [Portal](insights-alerts-portal.md)
> * [PowerShell](insights-alerts-powershell.md)
> * [CLI](insights-alerts-command-line-interface.md)
>
>

## <a name="overview"></a>Información general
En este artículo se muestra cómo configurar alertas de métricas de Azure con Azure Portal. 

Puede recibir una alerta basada en las métricas de supervisión para los servicios de Azure o los eventos sobre ellos.

* **Valores de métrica** : la alerta se desencadena cuando el valor de una métrica específica cruza un umbral asignado en cualquier dirección. Es decir, se desencadena tanto la primera vez que se cumple la condición como después, cuando dicha condición ya deja de cumplirse.    
* **Eventos de registro de actividades**: una alerta puede desencadenarse con *cada* evento o solo cuando se producen ciertos eventos concretos. Más información sobre las [alertas del registro de actividad](monitoring-activity-log-alerts.md).

Puede configurar una alerta de métrica para hacer lo siguiente cuando se desencadena:

* Enviar notificaciones de correo electrónico al administrador y los coadministradores del servicio.
* Enviar un correo electrónico a direcciones de correo electrónico adicionales que especifique.
* Llamar a un webhook.
* Iniciar la ejecución de un runbook de Azure (solo desde Azure Portal).

> [!NOTE]
> Azure Monitor ahora es compatible con alertas de métrica casi en tiempo real en la versión preliminar pública. Estas alertas usan grupos de acciones. Más información sobre [alertas de métrica en tiempo real](monitoring-near-real-time-metric-alerts.md).
>
>

Puede obtener información sobre las reglas de alerta de métricas y configurarlas mediante:

* [Portal de Azure](insights-alerts-portal.md)
* [PowerShell](insights-alerts-powershell.md)
* [Interfaz de la línea de comandos (CLI)](insights-alerts-command-line-interface.md)
* [API de REST de Azure Monitor](https://msdn.microsoft.com/library/azure/dn931945.aspx)

## <a name="create-an-alert-rule-on-a-metric-with-the-azure-portal"></a>Creación de una regla de alerta de una métrica con Azure Portal
1. En el [portal](https://portal.azure.com/), busque el recurso que desea supervisar y selecciónelo.

2. En la sección SUPERVISIÓN, seleccione **Alertas** o **Reglas de alerta**. El texto y el icono pueden variar ligeramente en los distintos recursos.  

    ![Supervisión](./media/insights-alerts-portal/AlertRulesButton.png)

3. Seleccione el comando **Agregar alerta** y rellene los campos.

    ![Agregar alerta](./media/insights-alerts-portal/AddAlertOnlyParamsPage.png)

4. Asígnele un **nombre** a la regla de alerta y elija una **descripción**, que también se muestra los correos electrónicos de notificación.

5. Seleccione la **métrica** que desea supervisar y elija un valor de **Condición** y **Umbral** para la métrica. También debe elegir el **período** de la regla de métrica que se debe cumplir antes de que se desencadene la alerta. Por ejemplo, si usa el período "En los últimos 5 minutos" y la alerta busca una CPU por encima del 80 %, la alerta se desencadena cuando la CPU ha estado por encima del 80 % durante 5 minutos de manera uniforme. Una vez que se desencadena por primera vez, se vuelve a desencadenar cuando la CPU se mantiene por debajo del 80% durante 5 minutos. La medición de métrica de CPU se produce cada minuto.

6. Desactive la opción **Enviar correo electrónico a propietarios...** si desea que se envíe un correo electrónico a los administradores y coadministradores cuando se active la alerta.

7. Si desea enviar una notificación a otras direcciones de correo electrónico cuando se active la alerta, agréguelas en el campo **Correos electrónicos de administradores adicionales** . Separe las direcciones de correo electrónico con punto y coma, de la siguiente manera: *email@contoso.com;email2@contoso.com*

8. Escriba un identificador URI válido en el campo **Webhook** si desea llamarlo cuando se active la alerta.

9. Si usa Azure Automation, puede seleccionar un runbook para que se ejecute cuando se active la alerta.

10. Seleccione **Aceptar** cuando termine para crear la alerta.   

En cuestión de minutos, se activa la alerta y se desencadena tal como se describió anteriormente.

## <a name="managing-your-alerts"></a>Administración de las alertas
Una vez que haya creado una alerta, puede seleccionarla y:

* Ver un gráfico que muestre el umbral de las métricas y los valores reales del día anterior.
* Editar la alerta o eliminarla.
* **Deshabilitar** la alerta, si desea dejar de recibir notificaciones de esa alerta de manera temporal, o **habilitarla** si desea reanudar sus notificaciones.

## <a name="next-steps"></a>Pasos siguientes
* [Obtenga información general sobre la supervisión de Azure](monitoring-overview.md) , incluidos los tipos de información que puede recopilar y supervisar.
* Más información sobre las nuevas [alertas de métrica casi en tiempo real (versión preliminar)](monitoring-near-real-time-metric-alerts.md).
* Obtenga más información sobre cómo [configurar webhooks en las alertas](insights-webhooks-alerts.md).
* Obtenga más información sobre la [configuración de alertas sobre los eventos de registro de actividad](monitoring-activity-log-alerts.md).
* Obtenga más información sobre los [runbooks de Azure Automation](../automation/automation-starting-a-runbook.md).
* Obtenga [información general sobre los registros de diagnóstico](monitoring-overview-of-diagnostic-logs.md) para recopilar métricas detalladas de alta frecuencia sobre el servicio.
* Obtenga [información general sobre la colección de métricas](insights-how-to-customize-monitoring.md) para garantizar que el servicio está disponible y que responder adecuadamente.
