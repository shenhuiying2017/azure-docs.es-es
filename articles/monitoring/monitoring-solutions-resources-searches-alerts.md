---
title: Búsquedas y alertas guardadas en soluciones de administración | Microsoft Docs
description: Las soluciones de administración incluyen normalmente búsquedas guardadas en Log Analytics para analizar los datos recopilados por la solución.  Pueden definir asimismo alertas para notificar al usuario o realizar automáticamente una acción en respuesta a un problema crítico.  En este artículo se describe cómo definir las búsquedas y alertas guardadas de Log Analytics en una plantilla de Resource Manager para que puedan incluirse en soluciones de administración.
services: monitoring
documentationcenter: ''
author: bwren
manager: carmonm
editor: tysonn
ms.service: monitoring
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/16/2018
ms.author: bwren, vinagara
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 8b16c88b5ec45dec7bf0fe40da24e817ae325a3e
ms.sourcegitcommit: 870d372785ffa8ca46346f4dfe215f245931dae1
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/08/2018
---
# <a name="adding-log-analytics-saved-searches-and-alerts-to-management-solution-preview"></a>Adición de búsquedas y alertas guardadas de Log Analytics en la solución de administración (versión preliminar)

> [!NOTE]
> Esta es la documentación preliminar para crear soluciones de administración que se encuentran actualmente en versión preliminar. Cualquier esquema descrito a continuación está sujeto a cambios.   


Las [soluciones de administración](monitoring-solutions.md) suelen incluir [búsquedas guardadas](../log-analytics/log-analytics-log-searches.md) en Log Analytics para analizar los datos recopilados por la solución.  Pueden definir asimismo [alertas](../log-analytics/log-analytics-alerts.md) para notificar al usuario o realizar automáticamente una acción en respuesta a un problema crítico.  En este artículo se describe cómo definir las búsquedas y alertas guardadas de Log Analytics en una [plantilla de Resource Management](../resource-manager-template-walkthrough.md) para que puedan incluirse en [soluciones de administración](monitoring-solutions-creating.md).

> [!NOTE]
> En los ejemplos de este artículo se usan parámetros y variables que son necesarios o comunes para las soluciones de administración, y se describen en [Diseño y compilación de una solución de administración en Azure](monitoring-solutions-creating.md).  

## <a name="prerequisites"></a>requisitos previos
En este artículo se supone que ya está familiarizado con la manera de [crear una solución de administración](monitoring-solutions-creating.md) y la estructura de una [plantilla de Resource Manager](../resource-group-authoring-templates.md) y un archivo de solución.


## <a name="log-analytics-workspace"></a>Área de trabajo de Log Analytics
Todos los recursos de Log Analytics están contenidos en un [área de trabajo](../log-analytics/log-analytics-manage-access.md).  Como se describe en [el área de trabajo de Log Analytics y la cuenta de Automation](monitoring-solutions.md#log-analytics-workspace-and-automation-account), el área de trabajo no está incluida en la solución de administración, pero debe existir antes de que se instale la solución.  Si no está disponible, se producirá un error en la instalación de la solución.

El nombre del área de trabajo es el nombre de cada recurso de Log Analytics.  Esto se hace en la solución con el parámetro **workspace** tal como se muestra en el siguiente ejemplo de un recurso de SavedSearch.

    "name": "[concat(parameters('workspaceName'), '/', variables('SavedSearchId'))]"

## <a name="log-analytics-api-version"></a>Versión de la API de Log Analytics
Todos los recursos de Log Analytics definidos en una plantilla de Resource Manager tienen una propiedad **apiVersion** que define la versión de la API que el recurso debe usar.  Esta versión es diferente para los recursos que usan el [lenguaje de consulta heredado y actualizado](../log-analytics/log-analytics-log-search-upgrade.md).  

 En la tabla siguiente se especifican las versiones de API de Log Analytics para las búsquedas guardadas en los espacios de trabajo heredados y actualizados: 

| Versión del área de trabajo | Versión de API | Consultar |
|:---|:---|:---|
| v1 (heredado)   | 2015-11-01-preview | Formato heredado.<br> Ejemplo: Type=Event EventLevelName = Error  |
| v2 (actualizado) | 2015-11-01-preview | Formato heredado.  Convertido al formato actualizado en la instalación.<br> Ejemplo: Type=Event EventLevelName = Error<br>Convertido a: Event &#124; donde EventLevelName == "Error"  |
| v2 (actualizado) | 2017-03-03-preview | Formato de actualización. <br>Ejemplo: Event &#124; donde EventLevelName == "Error"  |



## <a name="saved-searches"></a>Búsquedas guardadas
Incluya [búsquedas guardadas](../log-analytics/log-analytics-log-searches.md) en una solución para permitir a los usuarios consultar los datos recopilados por la solución.  Las búsquedas guardadas aparecerán en **Favoritos** en el portal de OMS y en **Búsquedas guardadas** en Azure Portal.  También es necesaria una búsqueda guardada para cada alerta.   

Los recursos de [búsquedas guardadas de Log Analytics](../log-analytics/log-analytics-log-searches.md) tienen un tipo de `Microsoft.OperationalInsights/workspaces/savedSearches` y presentan la siguiente estructura.  Aquí se incluyen las variables y los parámetros habituales para que pueda copiar y pegar este fragmento de código en su archivo de solución y cambiar los nombres de parámetro. 

    {
        "name": "[concat(parameters('workspaceName'), '/', variables('SavedSearch').Name)]",
        "type": "Microsoft.OperationalInsights/workspaces/savedSearches",
        "apiVersion": "[variables('LogAnalyticsApiVersion')]",
        "dependsOn": [
        ],
        "tags": { },
        "properties": {
            "etag": "*",
            "query": "[variables('SavedSearch').Query]",
            "displayName": "[variables('SavedSearch').DisplayName]",
            "category": "[variables('SavedSearch').Category]"
        }
    }



En la tabla siguiente se describe cada propiedad de una búsqueda guardada. 

| Propiedad | DESCRIPCIÓN |
|:--- |:--- |
| categoría | Categoría de la búsqueda guardada.  Las búsquedas guardadas en la misma solución comparten a menudo una única categoría por lo que están agrupadas juntas en la consola. |
| displayname | Nombre para mostrar de la búsqueda guardada en el portal. |
| query | La consulta que se ejecutará. |

> [!NOTE]
> Puede que necesite utilizar caracteres de escape en la consulta si incluye caracteres que puedan interpretarse como JSON.  Por ejemplo, si la consulta era **Type:AzureActivity OperationName:"Microsoft.Compute/virtualMachines/write"**, en el archivo de solución debe escribirse como **Type:AzureActivity OperationName:\"Microsoft.Compute/virtualMachines/write\"**.

## <a name="alerts"></a>Alertas
Las reglas de alerta que ejecutan una búsqueda guardada a intervalos regulares crean [alertas de Log Analytics](../log-analytics/log-analytics-alerts.md).  Si los resultados de la consulta coinciden con los criterios especificados, se crea un registro de alertas y se ejecutan una o varias acciones.  

> [!NOTE]
> A partir del 14 de mayo de 2018, todas las alertas de un área de trabajo comenzarán a extenderse automáticamente a Azure. Los usuarios pueden decidir si desean iniciar la extensión de alertas a Azure antes del 14 de mayo de 2018. Para más información, consulte [Extensión de alertas de OMS a Azure](../monitoring-and-diagnostics/monitoring-alerts-extend.md). En el caso de los usuarios que extienden las alertas a Azure, ahora las acciones se controlan en los grupos de acciones de Azure. Cuando un área de trabajo y sus alertas se extienden a Azure, es posible recuperar o agregar acciones mediante el [grupo de acciones: plantilla de Azure Resource Manager](../monitoring-and-diagnostics/monitoring-create-action-group-with-resource-manager-template.md).

Las reglas de alerta en una solución de administración se componen de los tres siguientes recursos.

- **Búsqueda guardada.**  Define la búsqueda de registros que se ejecuta.  Varias reglas de alerta pueden compartir una única búsqueda guardada.
- **Programación.**  Define la frecuencia con la que se ejecuta la búsqueda de registros.  Cada regla de alerta tiene una única programación.
- **Acción de alerta.**  Cada regla de alerta tiene un único recurso de grupo de acción con un tipo de **alerta** que define los detalles de la alerta, como los criterios de cuándo se crea un registro de alertas y la gravedad de la alerta. El recurso del [grupo de acción](../monitoring-and-diagnostics/monitoring-action-groups.md) puede tener una lista de acciones configuradas que se deben realizar cuando se desencadena la alerta, como llamadas de voz, SMS, correo electrónico, webhook, herramienta ITSM, runbook de automatización, aplicación lógica, etc.
 
El recurso de acción (heredada) definirá, opcionalmente, una respuesta de correo electrónico y de runbook.
- **Acción de webhook (heredada)**  Si la regla de alerta llama a un webhook, se requiere un recurso de acción adicional con un tipo de **webhook**.    

Los recursos de búsquedas guardadas se han descrito anteriormente.  Los demás recursos se describen a continuación.


### <a name="schedule-resource"></a>Recurso de programación

Una búsqueda guardada puede tener una o más programaciones y cada programación representa una regla de alerta independiente. La programación define la frecuencia con la que se realiza la búsqueda y el intervalo de tiempo durante el que se recuperan los datos.  Los recursos de programación tienen un tipo de `Microsoft.OperationalInsights/workspaces/savedSearches/schedules/` y presentan la siguiente estructura. Aquí se incluyen las variables y los parámetros habituales para que pueda copiar y pegar este fragmento de código en su archivo de solución y cambiar los nombres de parámetro. 


    {
        "name": "[concat(parameters('workspaceName'), '/', variables('SavedSearch').Name, '/', variables('Schedule').Name)]",
        "type": "Microsoft.OperationalInsights/workspaces/savedSearches/schedules/",
        "apiVersion": "[variables('LogAnalyticsApiVersion')]",
        "dependsOn": [
            "[concat('Microsoft.OperationalInsights/workspaces/', parameters('workspaceName'), '/savedSearches/', variables('SavedSearch').Name)]"
        ],
        "properties": {
            "etag": "*",
            "interval": "[variables('Schedule').Interval]",
            "queryTimeSpan": "[variables('Schedule').TimeSpan]",
            "enabled": "[variables('Schedule').Enabled]"
        }
    }



En la tabla siguiente se describen las propiedades para los recursos de programación.

| Nombre del elemento | Obligatorio | DESCRIPCIÓN |
|:--|:--|:--|
| Enabled       | Sí | Especifica si la alerta está habilitada cuando se crea. |
| interval      | Sí | Frecuencia con la que se ejecuta la consulta en minutos. |
| queryTimeSpan | Sí | Período de tiempo en minutos en el que se evalúan los resultados. |

El recurso de programación debe depender de la búsqueda guardada para que se cree antes de la programación.

> [!NOTE]
> El nombre de la programación debe ser único en un área de trabajo determinado; dos programaciones no pueden tener el mismo identificador, incluso si están asociadas con diferentes búsquedas guardadas. También, el nombre de todas las búsquedas guardadas, programaciones y acciones creadas con la API de Log Analytics debe estar en minúsculas.


### <a name="actions"></a>Acciones
Una programación puede tener varias acciones. Una acción puede definir uno o varios procesos que se van a realizar (como enviar un correo o iniciar un Runbook), o bien puede definir un umbral que determina si los resultados de una búsqueda coinciden con algunos criterios.  Algunas acciones definen ambos aspectos, de forma que los procesos se realizan cuando se alcance el umbral.

Las acciones pueden definirse mediante el recurso [grupo de acciones] o un recurso de acción.

> [!NOTE]
> A partir del 14 de mayo de 2018, todas las alertas de un área de trabajo comenzarán a extenderse automáticamente a Azure. Los usuarios pueden decidir si desean iniciar la extensión de alertas a Azure antes del 14 de mayo de 2018. Para más información, consulte [Extensión de alertas de OMS a Azure](../monitoring-and-diagnostics/monitoring-alerts-extend.md). En el caso de los usuarios que extienden las alertas a Azure, ahora las acciones se controlan en los grupos de acciones de Azure. Cuando un área de trabajo y sus alertas se extienden a Azure, es posible recuperar o agregar acciones mediante el [grupo de acciones: plantilla de Azure Resource Manager](../monitoring-and-diagnostics/monitoring-create-action-group-with-resource-manager-template.md).


Hay dos tipos de recursos de acción especificados por la propiedad **Type**.  Una programación requiere una acción **Alert** que define los detalles de la regla de alerta y las acciones que se realizan cuando se crea una alerta. Los recursos de acción tienen un tipo de `Microsoft.OperationalInsights/workspaces/savedSearches/schedules/actions`.  

Las acciones de alerta tienen la siguiente estructura.  Aquí se incluyen las variables y los parámetros habituales para que pueda copiar y pegar este fragmento de código en su archivo de solución y cambiar los nombres de parámetro. 


```
    {
        "name": "[concat(parameters('workspaceName'), '/', variables('SavedSearch').Name, '/', variables('Schedule').Name, '/', variables('Alert').Name)]",
        "type": "Microsoft.OperationalInsights/workspaces/savedSearches/schedules/actions",
        "apiVersion": "[variables('LogAnalyticsApiVersion')]",
        "dependsOn": [
            "[concat('Microsoft.OperationalInsights/workspaces/', parameters('workspaceName'), '/savedSearches/', variables('SavedSearch').Name, '/schedules/', variables('Schedule').Name)]"
        ],
        "properties": {
            "etag": "*",
            "type": "Alert",
            "name": "[variables('Alert').Name]",
            "description": "[variables('Alert').Description]",
            "severity": "[variables('Alert').Severity]",
            "threshold": {
                "operator": "[variables('Alert').Threshold.Operator]",
                "value": "[variables('Alert').Threshold.Value]",
                "metricsTrigger": {
                    "triggerCondition": "[variables('Alert').Threshold.Trigger.Condition]",
                    "operator": "[variables('Alert').Trigger.Operator]",
                    "value": "[variables('Alert').Trigger.Value]"
                  },
              },
      "AzNsNotification": {
        "GroupIds": "[variables('MyAlert').AzNsNotification.GroupIds]",
        "CustomEmailSubject": "[variables('MyAlert').AzNsNotification.CustomEmailSubject]",
        "CustomWebhookPayload": "[variables('MyAlert').AzNsNotification.CustomWebhookPayload]"
        }
        }
    }
```

En las tablas siguientes se describen las propiedades para los recursos de acción de alerta.

| Nombre del elemento | Obligatorio | DESCRIPCIÓN |
|:--|:--|:--|
| Escriba | Sí | Tipo de la acción.  Es **Alert** para las acciones de alerta. |
| NOMBRE | Sí | Nombre para mostrar de la alerta.  Es el nombre que se muestra en la consola para la regla de alerta. |
| DESCRIPCIÓN | Sin  | Descripción opcional de la alerta. |
| Gravedad | Sí | Gravedad del registro de alertas según los siguientes valores:<br><br> **Critical)** (Crítico)<br>**Warning (ADVERTENCIA)**<br>**Informational** (Informativo)


#### <a name="threshold"></a>Umbral
Esta sección es obligatoria.  Define las propiedades para el umbral de alerta.

| Nombre del elemento | Obligatorio | DESCRIPCIÓN |
|:--|:--|:--|
| Operador | Sí | Operador para la comparación según los valores siguientes:<br><br>**gt = mayor que<br>lt = menor que** |
| Valor | Sí | Valor para comparar los resultados. |

##### <a name="metricstrigger"></a>MetricsTrigger
Esta sección es opcional.  Inclúyala para una alerta de unidades métricas.

> [!NOTE]
> Las alertas de unidades métricas están actualmente en versión preliminar pública. 

| Nombre del elemento | Obligatorio | DESCRIPCIÓN |
|:--|:--|:--|
| TriggerCondition | Sí | Especifica si el umbral es para el número total de infracciones o para infracciones consecutivas con los siguientes valores:<br><br>**Total<br>Consecutive** (Total, Consecutivos) |
| Operador | Sí | Operador para la comparación según los valores siguientes:<br><br>**gt = mayor que<br>lt = menor que** |
| Valor | Sí | Número de veces que se deben cumplir los criterios para desencadenar la alerta. |


#### <a name="throttling"></a>Limitaciones
Esta sección es opcional.  Incluya esta sección si desea suprimir alertas en la misma regla durante cierto tiempo después de crear una alerta.

| Nombre del elemento | Obligatorio | DESCRIPCIÓN |
|:--|:--|:--|
| DurationInMinutes | Sí, si hay una limitación de elementos incluida | Número de minutos para suprimir alertas después de crear una en la misma regla de alerta. |


#### <a name="azure-action-group"></a>Grupo de acciones de Azure
Todas las alertas de Azure usan el grupo de acciones como el mecanismo predeterminado para controlar las acciones. Con el grupo de acciones, puede especificar las acciones una vez y, luego, asociar el grupo de acciones a varias alertas en todo Azure, sin la necesidad de declarar repetidamente una y otra vez las mismas acciones. Los grupos de acciones admiten varias acciones, incluido el correo electrónico, SMS, llamadas de voz, conexión ITSM, runbook de Automation, URI de webhook, etc. 

Para los usuarios que han extendido sus alertas a Azure, ahora una programación tendrá los detalles del grupo de acciones junto con el umbral, para así poder crear una alerta. Antes de crear una alerta, es necesario definir los detalles de correo electrónico, las direcciones URL de webhooks, los detalles de automatización de runbooks y otras acciones dentro de un grupo de acciones. Es posible crear un [grupo de acciones desde Azure Monitor](../monitoring-and-diagnostics/monitoring-action-groups.md) en el portal o usar el [grupo de acciones: plantilla de recursos](../monitoring-and-diagnostics/monitoring-create-action-group-with-resource-manager-template.md).

| Nombre del elemento | Obligatorio | DESCRIPCIÓN |
|:--|:--|:--|
| AzNsNotification | Sí | El identificador de recurso del grupo de acciones de Azure que se asociará con la alerta para realizar las acciones necesarias cuando se cumplan los criterios de alerta. |
| CustomEmailSubject | Sin  | Línea de asunto personalizada del correo enviado a todas las direcciones especificadas en el grupo de acciones asociado. |
| CustomWebhookPayload | Sin  | Carga personalizada para enviarse a todos los puntos de conexión de webhook definidos en el grupo de acciones asociadas. El formato depende de lo que espera el webhook y debe ser un valor JSON serializado válido. |


#### <a name="actions-for-oms-legacy"></a>Acciones para OMS (heredado)

Cada programación tiene una acción **Alert**.  Esto define los detalles de la alerta y, opcionalmente, las acciones de notificación y corrección.  Una notificación envía un mensaje de correo electrónico a una o varias direcciones.  Una corrección inicia un runbook en Azure Automation para intentar corregir el problema detectado.

> [!NOTE]
> A partir del 14 de mayo de 2018, todas las alertas de un área de trabajo comenzarán a extenderse automáticamente a Azure. Los usuarios pueden decidir si desean iniciar la extensión de alertas a Azure antes del 14 de mayo de 2018. Para más información, consulte [Extensión de alertas de OMS a Azure](../monitoring-and-diagnostics/monitoring-alerts-extend.md). En el caso de los usuarios que extienden las alertas a Azure, ahora las acciones se controlan en los grupos de acciones de Azure. Cuando un área de trabajo y sus alertas se extienden a Azure, es posible recuperar o agregar acciones mediante el [grupo de acciones: plantilla de Azure Resource Manager](../monitoring-and-diagnostics/monitoring-create-action-group-with-resource-manager-template.md).

##### <a name="emailnotification"></a>EmailNotification
 Esta sección es opcional. Inclúyala si desea que la alerta envíe un mensaje de correo electrónico a uno o varios destinatarios.

| Nombre del elemento | Obligatorio | DESCRIPCIÓN |
|:--|:--|:--|
| Recipients | Sí | Lista delimitada por comas de direcciones de correo electrónico para envío de notificación cuando se crea una alerta, como en el ejemplo siguiente.<br><br>**[ "recipient1@contoso.com", "recipient2@contoso.com" ]** |
| Asunto | Sí | Línea del asunto del mensaje de correo electrónico. |
| Datos adjuntos | Sin  | Los datos adjuntos no son compatibles actualmente.  Si este elemento está incluido, debe ser **None** (Ninguno). |


##### <a name="remediation"></a>Corrección
Esta sección es opcional. Inclúyala si desea que se inicie un runbook en respuesta a la alerta. |

| Nombre del elemento | Obligatorio | DESCRIPCIÓN |
|:--|:--|:--|
| RunbookName | Sí | Nombre del runbook que se va a iniciar. |
| WebhookUri | Sí | URI del webhook para el runbook. |
| Expiry | Sin  | Fecha y hora a la que expira la corrección. |

##### <a name="webhook-actions"></a>Acciones de webhook

Las acciones de webhook inician un proceso llamando a una dirección URL y, opcionalmente, proporcionando una carga que se va a enviar. Son similares a las acciones de corrección, salvo por el hecho de que están pensadas para webhooks que pueden invocar procesos que no tienen que ver con Runbooks de Azure Automation. También ofrecen la posibilidad extra de proporcionar una carga adicional para entregarla en el proceso remoto.

Si la alerta llama a un webhook, necesitará un recurso de acción con un tipo de **Webhook**, además del recurso de acción **Alert**.  

    {
      "name": "name": "[concat(parameters('workspaceName'), '/', variables('SavedSearch').Name, '/', variables('Schedule').Name, '/', variables('Webhook').Name)]",
      "type": "Microsoft.OperationalInsights/workspaces/savedSearches/schedules/actions/",
      "apiVersion": "[variables('LogAnalyticsApiVersion')]",
      "dependsOn": [
            "[concat('Microsoft.OperationalInsights/workspaces/', parameters('workspaceName'), '/savedSearches/', variables('SavedSearch').Name, '/schedules/', variables('Schedule').Name)]"
      ],
      "properties": {
        "etag": "*",
        "type": "[variables('Alert').Webhook.Type]",
        "name": "[variables('Alert').Webhook.Name]",
        "webhookUri": "[variables('Alert').Webhook.webhookUri]",
        "customPayload": "[variables('Alert').Webhook.CustomPayLoad]"
      }
    }

En las tablas siguientes se describen las propiedades para los recursos de acción de Webhook.

| Nombre del elemento | Obligatorio | DESCRIPCIÓN |
|:--|:--|:--|
| Tipo | Sí | Tipo de la acción.  Es **Webhook** para las acciones de webhook. |
| Nombre | Sí | Nombre para mostrar de la acción.  Esto no se muestra en la consola. |
| wehookUri | Sí | URI del webhook. |
| customPayload | Sin  | Carga personalizada que se va a enviar al webhook. El formato depende de lo que el webhook espere. |


## <a name="sample"></a>Muestra

A continuación, se muestra un ejemplo de una solución que incluye los siguientes recursos:

- Búsqueda guardada
- Schedule
- Grupo de acciones

En el ejemplo se utilizan variables de [parámetros de solución estándar]( monitoring-solutions-solution-file.md#parameters) que se suelen utilizar en una solución en lugar de codificar valores en las definiciones de recursos.

```
    {
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "contentVersion": "1.0",
        "parameters": {
          "workspaceName": {
            "type": "string",
            "metadata": {
              "Description": "Name of Log Analytics workspace"
            }
          },
          "workspaceregionId": {
            "type": "string",
            "metadata": {
              "Description": "Region of Log Analytics workspace"
            }
          },
          "actiongroup": {
            "type": "string",
            "metadata": {
              "Description": "List of action groups for alert actions separated by semicolon"
            }
          }
        },
        "variables": {
          "SolutionName": "MySolution",
          "SolutionVersion": "1.0",
          "SolutionPublisher": "Contoso",
          "ProductName": "SampleSolution",
    
          "LogAnalyticsApiVersion": "2015-03-20",
    
          "MySearch": {
            "displayName": "Error records by hour",
            "query": "Type=MyRecord_CL | measure avg(Rating_d) by Instance_s interval 60minutes",
            "category": "Samples",
            "name": "Samples-Count of data"
          },
          "MyAlert": {
            "Name": "[toLower(concat('myalert-',uniqueString(resourceGroup().id, deployment().name)))]",
            "DisplayName": "My alert rule",
            "Description": "Sample alert.  Fires when 3 error records found over hour interval.",
            "Severity": "Critical",
            "ThresholdOperator": "gt",
            "ThresholdValue": 3,
            "Schedule": {
              "Name": "[toLower(concat('myschedule-',uniqueString(resourceGroup().id, deployment().name)))]",
              "Interval": 15,
              "TimeSpan": 60
            },
            "MetricsTrigger": {
              "TriggerCondition": "Consecutive",
              "Operator": "gt",
              "Value": 3
            },
            "ThrottleMinutes": 60,
            "AzNsNotification": {
              "GroupIds": [
                "[parameters('actiongroup')]"
              ],
              "CustomEmailSubject": "Sample alert"
            }
          }
        },
        "resources": [
          {
            "name": "[concat(variables('SolutionName'), '[' ,parameters('workspacename'), ']')]",
            "location": "[parameters('workspaceRegionId')]",
            "tags": { },
            "type": "Microsoft.OperationsManagement/solutions",
            "apiVersion": "[variables('LogAnalyticsApiVersion')]",
            "dependsOn": [
              "[resourceId('Microsoft.OperationalInsights/workspaces/savedSearches', parameters('workspacename'), variables('MySearch').Name)]",
              "[resourceId('Microsoft.OperationalInsights/workspaces/savedSearches/schedules', parameters('workspacename'), variables('MySearch').Name, variables('MyAlert').Schedule.Name)]",
              "[resourceId('Microsoft.OperationalInsights/workspaces/savedSearches/schedules/actions', parameters('workspacename'), variables('MySearch').Name, variables('MyAlert').Schedule.Name, variables('MyAlert').Name)]",
              "[resourceId('Microsoft.OperationalInsights/workspaces/savedSearches/schedules/actions', parameters('workspacename'), variables('MySearch').Name, variables('MyAlert').Schedule.Name, variables('MyAlert').Webhook.Name)]"
            ],
            "properties": {
              "workspaceResourceId": "[resourceId('Microsoft.OperationalInsights/workspaces', parameters('workspacename'))]",
              "referencedResources": [
              ],
              "containedResources": [
                "[resourceId('Microsoft.OperationalInsights/workspaces/savedSearches', parameters('workspacename'), variables('MySearch').Name)]",
                "[resourceId('Microsoft.OperationalInsights/workspaces/savedSearches/schedules', parameters('workspacename'), variables('MySearch').Name, variables('MyAlert').Schedule.Name)]",
                "[resourceId('Microsoft.OperationalInsights/workspaces/savedSearches/schedules/actions', parameters('workspacename'), variables('MySearch').Name, variables('MyAlert').Schedule.Name, variables('MyAlert').Name)]"
              ]
            },
            "plan": {
              "name": "[concat(variables('SolutionName'), '[' ,parameters('workspaceName'), ']')]",
              "Version": "[variables('SolutionVersion')]",
              "product": "[variables('ProductName')]",
              "publisher": "[variables('SolutionPublisher')]",
              "promotionCode": ""
            }
          },
          {
            "name": "[concat(parameters('workspaceName'), '/', variables('MySearch').Name)]",
            "type": "Microsoft.OperationalInsights/workspaces/savedSearches",
            "apiVersion": "[variables('LogAnalyticsApiVersion')]",
            "dependsOn": [ ],
            "tags": { },
            "properties": {
              "etag": "*",
              "query": "[variables('MySearch').query]",
              "displayName": "[variables('MySearch').displayName]",
              "category": "[variables('MySearch').category]"
            }
          },
          {
            "name": "[concat(parameters('workspaceName'), '/', variables('MySearch').Name, '/', variables('MyAlert').Schedule.Name)]",
            "type": "Microsoft.OperationalInsights/workspaces/savedSearches/schedules/",
            "apiVersion": "[variables('LogAnalyticsApiVersion')]",
            "dependsOn": [
              "[concat('Microsoft.OperationalInsights/workspaces/', parameters('workspaceName'), '/savedSearches/', variables('MySearch').Name)]"
            ],
            "properties": {
              "etag": "*",
              "interval": "[variables('MyAlert').Schedule.Interval]",
              "queryTimeSpan": "[variables('MyAlert').Schedule.TimeSpan]",
              "enabled": true
            }
          },
          {
            "name": "[concat(parameters('workspaceName'), '/', variables('MySearch').Name, '/',  variables('MyAlert').Schedule.Name, '/',  variables('MyAlert').Name)]",
            "type": "Microsoft.OperationalInsights/workspaces/savedSearches/schedules/actions",
            "apiVersion": "[variables('LogAnalyticsApiVersion')]",
            "dependsOn": [
              "[concat('Microsoft.OperationalInsights/workspaces/', parameters('workspaceName'), '/savedSearches/',  variables('MySearch').Name, '/schedules/', variables('MyAlert').Schedule.Name)]"
            ],
            "properties": {
              "etag": "*",
              "Type": "Alert",
              "Name": "[variables('MyAlert').DisplayName]",
              "Description": "[variables('MyAlert').Description]",
              "Severity": "[variables('MyAlert').Severity]",
              "Threshold": {
                "Operator": "[variables('MyAlert').ThresholdOperator]",
                "Value": "[variables('MyAlert').ThresholdValue]",
                "MetricsTrigger": {
                  "TriggerCondition": "[variables('MyAlert').MetricsTrigger.TriggerCondition]",
                  "Operator": "[variables('MyAlert').MetricsTrigger.Operator]",
                  "Value": "[variables('MyAlert').MetricsTrigger.Value]"
                }
              },
              "Throttling": {
                "DurationInMinutes": "[variables('MyAlert').ThrottleMinutes]"
              },
            "AzNsNotification": {
              "GroupIds": "[variables('MyAlert').AzNsNotification.GroupIds]",
              "CustomEmailSubject": "[variables('MyAlert').AzNsNotification.CustomEmailSubject]"
            }             
            }
          }
        ]
    }
```

El siguiente archivo de parámetros proporciona valores de ejemplo para esta solución.
```
    {
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {
            "workspacename": {
                "value": "myWorkspace"
            },
            "accountName": {
                "value": "myAccount"
            },
            "workspaceregionId": {
                "value": "East US"
            },
            "regionId": {
                "value": "East US 2"
            },
            "pricingTier": {
                "value": "Free"
            },
            "actiongroup": {
                "value": "/subscriptions/3b540246-808d-4331-99aa-917b808a9166/resourcegroups/myTestGroup/providers/microsoft.insights/actiongroups/sample"
            }
        }
    }
```

## <a name="next-steps"></a>Pasos siguientes
* [Incorporación de vistas](monitoring-solutions-resources-views.md) a la solución de administración.
* [Incorporación de runbooks de Automation y otros recursos](monitoring-solutions-resources-automation.md) a la solución de administración.

