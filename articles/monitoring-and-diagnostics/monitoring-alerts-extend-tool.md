---
title: Extensión (copia) de alertas del portal de OMS a Azure | Microsoft Docs
description: Los clientes pueden realizar voluntariamente herramientas y API para extender las alertas de OMS a alertas de Azure.
author: msvijayn
manager: kmadnani1
editor: ''
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/14/2018
ms.author: vinagara
ms.openlocfilehash: 241ac027a0606f901f51d6a20b9a48a2cf7a9fcf
ms.sourcegitcommit: d78bcecd983ca2a7473fff23371c8cfed0d89627
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/14/2018
ms.locfileid: "34166189"
---
# <a name="how-to-extend-copy-alerts-from-oms-into-azure"></a>Extensión (copia) de alertas de OMS a Azure
A partir del **14 de mayo de 2018**, para todos los clientes que utilizan alertas configuradas en [Microsoft Operations Management Suite (OMS)](../operations-management-suite/operations-management-suite-overview.md), las alertas se extenderán a Azure. Las alertas que se extienden a Azure tienen el mismo comportamiento que en OMS. Las funcionalidades de supervisión siguen intactas. La extensión de las alertas creadas en OMS a Azure ofrece numerosas ventajas. Para más información acerca de las ventajas y el proceso de extensión de alertas de OMS a Azure, consulte [Extend alerts from OMS to Azure](monitoring-alerts-extend.md) (Extensión de alertas de OMS a Azure)///.

> [!NOTE]
> A partir del 14 de mayo de 2018, Microsoft comenzará el proceso de extensión automática de alertas a Azure. No todas las áreas de trabajo y alertas se ampliarán ese día, sino que Microsoft comenzará a extender automáticamente las alertas por tramos en las próximas semanas. Por lo tanto, las alertas del portal OMS no se extenderán automáticamente a Azure inmediatamente el 14 de mayo de 2018 y los usuarios todavía pueden extender manualmente sus alertas mediante las opciones que se detallan a continuación.

Los clientes que deseen mover alertas de OMS a Azure de inmediato pueden hacerlo mediante una de las opciones indicadas.

## <a name="option-1---using-oms-portal"></a>Opción 1: Uso del portal de OMS
Para iniciar voluntariamente la extensión de alertas del portal de OMS a Azure, siga los pasos indicados a continuación.

1. En la página de información general de OMS, vaya a Configuración y, después, a la sección Alertas. Haga clic en el botón "Extender a Azure", tal como se resalta en la ilustración siguiente.

    ![Página Configuración de alertas de OMS con opción Extender](./media/monitor-alerts-extend/ExtendInto.png)

2. Una vez que se haga clic en el botón, aparecerá un asistente en 3 pasos: el primer paso proporciona los detalles del proceso. Haga clic en Siguiente para continuar.

    ![Extensión de alertas del portal de OMS a Azure: paso 1](./media/monitor-alerts-extend/ExtendStep1.png)

3. En el segundo paso, el sistema mostrará un resumen del cambio propuesto, al enumerar [Grupos de acciones](monitoring-action-groups.md) adecuados, para las alertas de OMS. Si se ven acciones similares en más de una alerta, el sistema propondrá asociarlas todas en un único grupo de acciones.  El grupo de acciones propuesto sigue la convención de nomenclatura: *WorkspaceName_AG_#Number*. Para continuar, haga clic en Siguiente.
A continuación, se muestra una pantalla de ejemplo.

    ![Extensión de alertas del portal de OMS a Azure: paso 2](./media/monitor-alerts-extend/ExtendStep2.png)


4. En el último paso del asistente, puede pedir al portal de OMS que programe la extensión de todas las alertas a Azure, mediante la creación de nuevos grupos de acciones y su asociación con alertas, tal como se muestra en la pantalla anterior. Para continuar, haga clic en Finalizar y confirme en el símbolo del sistema para iniciar el proceso. Opcionalmente, los clientes también pueden proporcionar direcciones de correo electrónico a las que les gustaría que el portal de OMS enviara un informe al finalizar el procesamiento.

    ![Extensión de alertas del portal de OMS a Azure: paso 3](./media/monitor-alerts-extend/ExtendStep3.png)

5. Una vez que el asistente haya finalizado, el control se devolverá a la página Configuración de alertas y la opción "Extender a Azure" desaparecerá. En segundo plano, el portal de OMS programará las alertas de Log Analytics que se extenderán a Azure. Esto puede tardar un tiempo y cuando comience la operación, durante un breve período, las alertas en el portal de OMS no estarán disponible para su modificación. Se mostrará el estado actual mediante un banner y si se han proporcionado direcciones de correo electrónico en el paso 4, se les avisará cuando el proceso en segundo plano extienda correctamente todas las alertas a Azure. 

6. Las alertas seguirán apareciendo en el portal de OMS, incluso después de que se extiendan correctamente a Azure.

    ![Después de extender las alertas del portal de OMS a Azure](./media/monitor-alerts-extend/PostExtendList.png)


## <a name="option-2---using-api"></a>Opción 2: Uso de API
Para los clientes que deseen controlar o automatizar mediante programación el proceso de extensión de alertas del portal de OMS a Azure, Microsoft ha proporcionado la nueva AlertsVersion API en Log Analytics.

La AlertsVersion API de Log Analytics es de tipo RESTful y se puede obtener acceso a ella con la API REST de Azure Resource Manager. En este documento encontrará ejemplos donde se tiene acceso a la API desde una línea de comandos de PowerShell mediante [ARMClient](https://github.com/projectkudu/ARMClient), una herramienta de línea de comandos de código abierto que simplifica la tarea de invocar a la API de Azure Resource Manager. El uso de ARMClient y PowerShell es una de las muchas opciones para acceder a la API. La API generará resultados en formato JSON, lo que permite el uso de los resultados de muchas formas distintas mediante programación.

Al utilizar GET en la API, se puede obtener el resumen del cambio propuesto, como lista de [Grupos de acciones](monitoring-action-groups.md) adecuados para las alertas del portal de OMS, en formato JSON. Si se ven acciones similares en más de una alerta, el sistema propondrá asociarlas todas en un único grupo de acciones.  El grupo de acciones propuesto sigue la convención de nomenclatura: *WorkspaceName_AG_#Number*.

```
armclient GET  /subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.OperationalInsights/workspaces/<workspaceName>/alertsversion?api-version=2017-04-26-preview
```

> [!NOTE]
> La llamada GET a la API no dará como resultado la extensión de las alertas del portal de OMS a Azure. Solo proporcionará como respuesta el resumen de los cambios propuestos. Para confirmar que estos cambios que se van a realizar extiendan las alertas a Azure, se debe ejecutar una llamada POST a la API.

Si la llamada GET a la API es correcta, junto con la respuesta del código 200 OK, recibirá una lista de alertas JSON con grupos de acciones propuestos. A continuación, se muestra una respuesta de ejemplo:

```json
{
    "version": 1,
    "migrationSummary": {
        "alertsCount": 2,
        "actionGroupsCount": 2,
        "alerts": [
            {
                "alertName": "DemoAlert_1",
                "alertId": " /subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.OperationalInsights/workspaces/<workspaceName>/savedSearches/<savedSearchId>/schedules/<scheduleId>/actions/<actionId>",
                "actionGroupName": "<workspaceName>_AG_1"
            },
            {
                "alertName": "DemoAlert_2",
                "alertId": " /subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.OperationalInsights/workspaces/<workspaceName>/savedSearches/<savedSearchId>/schedules/<scheduleId>/actions/<actionId>",
                "actionGroupName": "<workspaceName>_AG_2"
            }
        ],
        "actionGroups": [
            {
                "actionGroupName": "<workspaceName>_AG_1",
                "actionGroupResourceId": "/subscriptions/<subscriptionid>/resourceGroups/<resourceGroupName>/providers/microsoft.insights/actionGroups/<workspaceName>_AG_1",
                "actions": {
                    "emailIds": [
                        "JohnDoe@mail.com"
                    ],
                    "webhookActions": [
                        {
                            "name": "Webhook_1",
                            "serviceUri": "http://test.com"
                        }
                    ],
                    "itsmAction": {}
                }
            },
            {
                "actionGroupName": "<workspaceName>_AG_1",
                "actionGroupResourceId": "/subscriptions/<subscriptionid>/resourceGroups/<resourceGroupName>/providers/microsoft.insights/actionGroups/<workspaceName>_AG_1",
                 "actions": {
                    "emailIds": [
                        "test1@mail.com",
                          "test2@mail.com"
                    ],
                    "webhookActions": [],
                    "itsmAction": {
                        "connectionId": "<Guid>",
                        "templateInfo":"{\"PayloadRevision\":0,\"WorkItemType\":\"Incident\",\"UseTemplate\":false,\"WorkItemData\":\"{\\\"contact_type\\\":\\\"email\\\",\\\"impact\\\":\\\"3\\\",\\\"urgency\\\":\\\"2\\\",\\\"category\\\":\\\"request\\\",\\\"subcategory\\\":\\\"password\\\"}\",\"CreateOneWIPerCI\":false}"
                    }
                }
            }
        ]
    }
}

```
En caso de que no haya alertas en el área de trabajo especificada, junto con la respuesta del código 200 OK para la operación GET, JSON sería:

```json
{
    "version": 1,
    "Message": "No Alerts found in the workspace for migration."
}
```

Si todas las alertas del área de trabajo especificado ya se han extendido a Azure, la respuesta a la llamada GET sería:
```json
{
    "version": 2
}
```

Para iniciar la programación de la extensión de las alertas del portal de OMS a Azure, inicie una llamada POST a la API. Al hacer esta llamada/comando, se confirma la intención del usuario, así como la aceptación de la extensión de sus alertas del portal de OMS a Azure y la ejecución de los cambios, tal como se indica en la respuesta de la llamada GET a la API. El usuario puede proporcionar opcionalmente una lista de direcciones de correo electrónico a las que el portal de OMS enviará un informe, cuando el proceso en segundo plano programado para la extensión de las alertas del portal de OMS a Azure finalice correctamente.

```
$emailJSON = “{‘Recipients’: [‘a@b.com’, ‘b@a.com’]}”
armclient POST  /subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.OperationalInsights/workspaces/<workspaceName>/alertsversion?api-version=2017-04-26-preview $emailJSON
```

> [!NOTE]
> El resultado de la extensión de las alertas del portal de OMS a Azure puede variar del resumen proporcionado por GET, en función de cualquier cambio realizado en el sistema. Una vez programadas, las alertas del portal de OMS dejarán de estar disponibles temporalmente para su edición o modificación, mientras que sí se podrán crear nuevas alertas. 

Si la llamada POST se realiza correctamente, devolverá una respuesta del código 200 OK junto con:
```json
{
    "version": 2
}
```
Que indica que las alertas se han extendido a Azure, tal como se especifica en la versión 2. Esta versión es solo para comprobar si las alertas se han extendido a Azure y no afecta a la [Search API de Log Analytics](../log-analytics/log-analytics-api-alerts.md). Cuando las alertas se extiendan correctamente a Azure, todas las direcciones de correo electrónico proporcionadas durante la operación GET enviarán un informe con detalles de los cambios realizados.

Y por último, si todas las alertas del área de trabajo especificada ya se han programado para que se extiendan a Azure, la respuesta a la llamada POST será el código 403 Prohibido. Para ver cualquier mensaje de error o conocer si el proceso de extensión se ha detenido, el usuario puede realizar una llamada GET y se devolverá el mensaje de error, en caso de que haya alguno, junto con el resumen.

```json
{
    "version": 1,
    "message": "OMS was unable to extend your alerts into Azure, Error: The subscription is not registered to use the namespace 'microsoft.insights'. OMS will schedule extending your alerts, once remediation steps illustrated in the troubleshooting guide are done.",
    "recipients": [
       "john.doe@email.com",
       "jane.doe@email.com"
     ],
    "migrationSummary": {
        "alertsCount": 2,
        "actionGroupsCount": 2,
        "alerts": [
            {
                "alertName": "DemoAlert_1",
                "alertId": " /subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.OperationalInsights/workspaces/<workspaceName>/savedSearches/<savedSearchId>/schedules/<scheduleId>/actions/<actionId>",
                "actionGroupName": "<workspaceName>_AG_1"
            },
            {
                "alertName": "DemoAlert_2",
                "alertId": " /subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.OperationalInsights/workspaces/<workspaceName>/savedSearches/<savedSearchId>/schedules/<scheduleId>/actions/<actionId>",
                "actionGroupName": "<workspaceName>_AG_2"
            }
        ],
        "actionGroups": [
            {
                "actionGroupName": "<workspaceName>_AG_1",
                "actionGroupResourceId": "/subscriptions/<subscriptionid>/resourceGroups/<resourceGroupName>/providers/microsoft.insights/actionGroups/<workspaceName>_AG_1",
                "actions": {
                    "emailIds": [
                        "JohnDoe@mail.com"
                    ],
                    "webhookActions": [
                        {
                            "name": "Webhook_1",
                            "serviceUri": "http://test.com"
                        }
                    ],
                    "itsmAction": {}
                }
            },
            {
                "actionGroupName": "<workspaceName>_AG_1",
                "actionGroupResourceId": "/subscriptions/<subscriptionid>/resourceGroups/<resourceGroupName>/providers/microsoft.insights/actionGroups/<workspaceName>_AG_1",
                 "actions": {
                    "emailIds": [
                        "test1@mail.com",
                          "test2@mail.com"
                    ],
                    "webhookActions": [],
                    "itsmAction": {
                        "connectionId": "<Guid>",
                        "templateInfo":"{\"PayloadRevision\":0,\"WorkItemType\":\"Incident\",\"UseTemplate\":false,\"WorkItemData\":\"{\\\"contact_type\\\":\\\"email\\\",\\\"impact\\\":\\\"3\\\",\\\"urgency\\\":\\\"2\\\",\\\"category\\\":\\\"request\\\",\\\"subcategory\\\":\\\"password\\\"}\",\"CreateOneWIPerCI\":false}"
                    }
                }
            }
        ]
    }
}              

```

## <a name="troubleshooting"></a>solución de problemas 
Durante el proceso de extensión de alertas de OMS en Azure, se pueden producir problemas ocasionales que impiden que el sistema cree los [grupos de acciones](monitoring-action-groups.md) necesarios. En tales casos aparecerá un mensaje de error en el portal de OMS mediante un banner en la sección de alertas y en las llamadas GET realizadas a la API.

A continuación se indican los pasos para corregir cada error:
1. **Error: The subscription is not registered to use the namespace 'microsoft.insights'** (La suscripción no está registrada para usar el espacio de nombres 'microsoft.insights'): ![página de configuración de alertas del portal de OMS con mensaje de error de registro](./media/monitor-alerts-extend/ErrorMissingRegistration.png)

    a. La suscripción asociada al área de trabajo de OMS no se ha registrado para usar la funcionalidad Azure Monitor (microsoft.insights). Debido a ello, OMS no puede extender las alertas en Azure Monitor.
    
    b. Para resolver, registre el uso de microsoft.insights (Azure Monitor) en su suscripción mediante Powershell, la CLI de Azure o Azure Portal. Para más información, consulte el artículo sobre [resolución de errores en el registro del proveedor de recursos](../azure-resource-manager/resource-manager-register-provider-errors.md)
    
    c. Una vez resuelto según los pasos descritos en el artículo, OMS extenderá las alertas en Azure durante la ejecución programada del día siguiente sin necesidad de realizar ninguna otra acción o iniciación.
2. **Error: Scope Lock is present at subscription/resource group level for write operations** (El bloqueo de ámbito está presente en el nivel de la suscripción o del grupo de recursos para las operaciones de escritura): ![página de configuración de alertas del portal de OMS con el mensaje de error ScopeLock](./media/monitor-alerts-extend/ErrorScopeLock.png)

    a. Si el ámbito de bloqueo está habilitado, al restringir cualquier cambio nuevo en la suscripción o grupo de recursos que contiene el área de trabajo de Log Analytics (OMS), el sistema no puede extender (copiar) las alertas en Azure y crear los grupos de acciones necesarios.
    
    b. Para solucionarlo, elimine el bloqueo *ReadOnly* de la suscripción o grupo de recursos que contiene el área de trabajo mediante Azure Portal, Powershell, la CLI de Azure o una API. Para más información, consulte el artículo sobre [uso del bloqueo de recursos](../azure-resource-manager/resource-group-lock-resources.md). 
    
    c. Una vez resuelto según los pasos descritos en el artículo, OMS extenderá las alertas en Azure durante la ejecución programada del día siguiente sin necesidad de realizar ninguna otra acción o iniciación.

3. **Error: Policy is present at subscription/resource group level** (Error: Directiva presente en el nivel de grupo de recursos/suscripción): ![página de configuración de alertas del portal de OMS con un mensaje de error de directiva](./media/monitor-alerts-extend/ErrorPolicy.png)

    a. Cuando se aplica [Azure Policy](../azure-policy/azure-policy-introduction.md), al restringir cualquier recurso nuevo en la suscripción o el grupo de recursos que contiene el área de trabajo de Log Analytics (OMS), el sistema no puede extender (copiar) las alertas en Azure y crear los grupos de acciones necesarios.
    
    b. Para resolver este problema, edite la directiva que provoca el error *[RequestDisallowedByPolicy](../azure-resource-manager/resource-manager-policy-requestdisallowedbypolicy-error.md)*, que impide la creación de nuevos recursos en la suscripción o el grupo de recursos que contiene el área de trabajo. Mediante Azure Portal, PowerShell, la CLI de Azure o las API, puede auditar las acciones para encontrar la directiva adecuada que ocasiona el error. Para más información, consulte el artículo sobre la [visualización de registros de actividad para auditar las acciones](../azure-resource-manager/resource-group-audit.md). 
    
    c. Una vez resuelto según los pasos descritos en el artículo, OMS extenderá las alertas en Azure durante la ejecución programada del día siguiente sin necesidad de realizar ninguna otra acción o iniciación.


## <a name="next-steps"></a>Pasos siguientes

* Más información sobre la nueva [experiencia de Alertas de Azure](monitoring-overview-unified-alerts.md).
* Más información sobre las [alertas de registro en Alertas de Azure](monitor-alerts-unified-log.md).
