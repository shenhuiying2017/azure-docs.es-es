---
title: "Migración de las alertas de eventos de administración de Azure a Alertas del registro de actividad | Microsoft Docs"
description: "Las Alertas de eventos de administración se retirarán el 1 de octubre. Preparar la migración de las alertas existentes."
author: johnkemnetz
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
ms.date: 08/14/2017
ms.author: johnkem
ms.openlocfilehash: 08a457029d3721f5c38dbcd2d2aab7d09a241d8f
ms.sourcegitcommit: 50e23e8d3b1148ae2d36dad3167936b4e52c8a23
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 08/18/2017
---
# <a name="migrate-azure-alerts-on-management-events-to-activity-log-alerts"></a>Migración de las alertas de eventos de administración de Azure a Alertas del registro de actividad


> [!WARNING]
> Las Alertas de eventos de administración se desactivarán a partir del 1 de octubre. Utilice las instrucciones siguientes para determinar si tiene estas alertas y migrarlas en caso afirmativo.
>
> 

## <a name="what-is-changing"></a>Lo que está cambiando

Azure Monitor (anteriormente Azure Insights) ofrecía una funcionalidad para crear una alerta desencadenada por eventos de administración y generar notificaciones a direcciones de correo electrónico o a una dirección URL de webhook. Es posible que haya creado una de estas alertas de cualquiera de las siguientes maneras:
* En Azure Portal para ciertos tipos de recursos, bajo Supervisión -> Alertas -> Agregar alerta, donde "Alerta de" se ha establecido en "Eventos"
* Mediante la ejecución del cmdlet de PowerShell Add-AzureRmLogAlertRule
* Utilizando directamente [la API de REST alert](http://docs.microsoft.com/rest/api/monitor/alertrules) con odata.type = "ManagementEventRuleCondition" y dataSource.odata.type = "RuleManagementEventDataSource"
 
El siguiente script de PowerShell devuelve una lista de todas las alertas de eventos de administración que tiene en su suscripción, así como las condiciones establecidas en cada alerta.

```powershell
Login-AzureRmAccount
$alerts = $null
foreach ($rg in Get-AzureRmResourceGroup ) {
  $alerts += Get-AzureRmAlertRule -ResourceGroup $rg.ResourceGroupName
}
foreach ($alert in $alerts) {
  if($alert.Properties.Condition.DataSource.GetType().Name.Equals("RuleManagementEventDataSource")) {
    "Alert Name: " + $alert.Name
    "Alert Resource ID: " + $alert.Id
    "Alert conditions:"
    $alert.Properties.Condition.DataSource
    "---------------------------------"
  }
} 
```

Si no tiene alertas de eventos de administración, el cmdlet de PowerShell anterior dará como resultado una serie de mensajes de advertencia como este:

`WARNING: The output of this cmdlet will be flattened, i.e. elimination of the properties field, in a future release to improve the user experience.`

Estos mensajes de advertencia pueden ignorarse. Si tiene alertas de eventos de administración, la salida de este cmdlet de PowerShell tendrá un aspecto similar al siguiente:

```
Alert Name: webhookEvent1
Alert Resource ID: /subscriptions/<subscription-id>/resourceGroups/<resourcegroup-name>/providers/microsoft.insights/alertrules/webhookEvent1
Alert conditions:

EventName            : 
EventSource          : 
Level                : 
OperationName        : microsoft.web/sites/start/action
ResourceGroupName    : 
ResourceProviderName : 
Status               : succeeded
SubStatus            : 
Claims               : Microsoft.Azure.Management.Monitor.Management.Models.RuleManagementEventClaimsDataSource
ResourceUri          : /subscriptions/<subscription-id>/resourceGroups/<resourcegroup-name>/providers/Microsoft.Web/sites/samplealertapp

---------------------------------
Alert Name: someclilogalert
Alert Resource ID: /subscriptions/<subscription-id>/resourceGroups/<resourcegroup-name>/providers/microsoft.insights/alertrules/someclilogalert
Alert conditions:

EventName            : 
EventSource          : 
Level                : 
OperationName        : Start
ResourceGroupName    : 
ResourceProviderName : 
Status               : 
SubStatus            : 
Claims               : Microsoft.Azure.Management.Monitor.Management.Models.RuleManagementEventClaimsDataSource
ResourceUri          : /subscriptions/<subscription-id>/resourceGroups/<resourcegroup-name>/providers/Microsoft.Compute/virtualMachines/Seaofclouds

---------------------------------
```

Cada alerta está separada por una línea discontinua y los detalles incluyen el identificador de recurso de la alerta y la regla específica que se está supervisando.

Esta funcionalidad se ha pasado a [Alertas del registro de actividad de Azure Monitor](monitoring-activity-log-alerts.md). Estas nuevas alertas permiten establecer una condición en los eventos del registro de actividad y recibir una notificación cuando un nuevo evento coincide con la condición. También ofrecen varias mejoras sobre las alertas de eventos de administración:
* Puede volver a usar el grupo de destinatarios de notificación ("acciones") en muchas alertas mediante los [Grupos de acciones](monitoring-action-groups.md), lo que reduce la complejidad del cambio de quién debe recibir una alerta.
* Puede recibir una notificación directamente en su teléfono mediante SMS con Grupos de acciones.
* Puede [Crear alertas del registro de actividad con plantillas de Resource Manager](monitoring-create-activity-log-alerts-with-resource-manager-template.md).
* Puede crear condiciones con mayor flexibilidad y complejidad para satisfacer sus necesidades específicas.
* Las notificaciones se entregan más rápidamente.
 
## <a name="how-to-migrate"></a>Cómo migrar
 
Para crear una nueva alerta del registro de actividad:
* Siga [nuestra guía sobre cómo crear una alerta en Azure Portal](monitoring-activity-log-alerts.md)
* Obtenga información acerca de cómo [crear una alerta mediante una plantilla de Resource Manager](monitoring-create-activity-log-alerts-with-resource-manager-template.md)
 
Las alertas de eventos de administración que haya creado previamente no se migrarán automáticamente a las alertas del registro de actividad. Debe utilizar el script de PowerShell anterior para mostrar las alertas de eventos de administración que tiene configuradas y volver a crearlas manualmente como alertas del registro de actividad. Esto debe hacerse antes del 1 de octubre, después del cual las alertas de eventos de administración ya no estará visibles en la suscripción de Azure. Otros tipos de alertas de Azure, incluidas las alertas de métricas de Azure Monitor, las alertas de Application Insights y las alertas de Log Analytics no se ven afectadas por este cambio. Si tiene alguna pregunta, indíquela en los comentarios a continuación.


## <a name="next-steps"></a>Pasos siguientes

* Más información sobre el [registro de actividad](monitoring-overview-activity-logs.md)
* Configuración de [alertas del registro de actividad a través de Azure Portal](monitoring-activity-log-alerts.md)
* Configuración de [alertas del registro de actividad a través de Resource Manager](monitoring-create-activity-log-alerts-with-resource-manager-template.md)
* Revisión del [esquema de webhook de alertas del registro de actividad](monitoring-activity-log-alerts-webhook.md)
* Más información sobre las [notificaciones del servicio](monitoring-service-notifications.md)
* Más información sobre los [grupos de acciones](monitoring-action-groups.md)
