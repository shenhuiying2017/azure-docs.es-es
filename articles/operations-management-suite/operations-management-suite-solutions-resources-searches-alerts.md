---
title: "Búsquedas y alertas guardadas en soluciones de OMS | Microsoft Docs"
description: "Las soluciones de OMS suelen incluir búsquedas guardadas en Log Analytics para analizar los datos recopilados por la solución.  Pueden definir asimismo alertas para notificar al usuario o realizar automáticamente una acción en respuesta a un problema crítico.  En este artículo se describe cómo definir las búsquedas y alertas guardadas de Log Analytics en una plantilla de ARM para que puedan incluirse en soluciones de administración."
services: operations-management-suite
documentationcenter: 
author: bwren
manager: carmonm
editor: tysonn
ms.service: operations-management-suite
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/24/2017
ms.author: bwren
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 21c42a747a08c5386c65d10190baf0054a7adef8
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 07/11/2017
---
# <a name="adding-log-analytics-saved-searches-and-alerts-to-oms-management-solution-preview"></a>Incorporación de las búsquedas y las alertas guardadas de Log Analytics en la solución de administración de OMS (versión preliminar)

> [!NOTE]
> La versión de la documentación para crear soluciones de administración de OMS está actualmente en fase preliminar. Cualquier esquema descrito a continuación está sujeto a cambios.   


Las [soluciones de administración de OMS](operations-management-suite-solutions.md) suelen incluir [búsquedas guardadas](../log-analytics/log-analytics-log-searches.md) en Log Analytics para analizar los datos recopilados por la solución.  Pueden definir asimismo [alertas](../log-analytics/log-analytics-alerts.md) para notificar al usuario o realizar automáticamente una acción en respuesta a un problema crítico.  En este artículo se describe cómo definir las búsquedas y alertas guardadas de Log Analytics en una [plantilla de Resource Management](../resource-manager-template-walkthrough.md) para que puedan incluirse en [soluciones de administración](operations-management-suite-solutions-creating.md).

> [!NOTE]
> En los ejemplos de este artículo se usan parámetros y variables que son necesarios o comunes para las soluciones de administración y se describen en [Creating management solutions in Operations Management Suite (OMS) (Creación de soluciones de administración en Operations Management Suite (OMS)](operations-management-suite-solutions-creating.md).  

## <a name="prerequisites"></a>Requisitos previos
En este artículo se supone que ya está familiarizado con la manera de [crear una solución de administración](operations-management-suite-solutions-creating.md) y la estructura de una [plantilla de ARM](../resource-group-authoring-templates.md) y un archivo de solución.


## <a name="log-analytics-workspace"></a>Área de trabajo de Log Analytics
Todos los recursos de Log Analytics están contenidos en un [área de trabajo](../log-analytics/log-analytics-manage-access.md).  Como se describe en [el área de trabajo de OMS y la cuenta de Automation](operations-management-suite-solutions.md#oms-workspace-and-automation-account), el área de trabajo no está incluido en la solución de administración pero debe existir antes de que se instale la solución.  Si no está disponible, se producirá un error en la instalación de la solución.

El nombre del área de trabajo es el nombre de cada recurso de Log Analytics.  Esto se hace en la solución con el parámetro **workspace** tal como se muestra en el siguiente ejemplo de un recurso de savedsearch.

    "name": "[concat(parameters('workspaceName'), '/', variables('SavedSearchId'))]"


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



En la tabla siguiente se describe cada una de las propiedades de una búsqueda guardada. 

| Propiedad | Descripción |
|:--- |:--- |
| categoría | Categoría de la búsqueda guardada.  Las búsquedas guardadas en la misma solución comparten a menudo una única categoría por lo que están agrupadas juntas en la consola. |
| displayname | Nombre para mostrar de la búsqueda guardada en el portal. |
| query | La consulta que se ejecutará. |

> [!NOTE]
> Puede que necesite utilizar caracteres de escape en la consulta si incluye caracteres que puedan interpretarse como JSON.  Por ejemplo, si la consulta era **Type:AzureActivity OperationName:"Microsoft.Compute/virtualMachines/write"**, en el archivo de solución debe escribirse como **Type:AzureActivity OperationName:\"Microsoft.Compute/virtualMachines/write\"**.

## <a name="alerts"></a>Alertas
Las reglas de alerta que ejecutan una búsqueda guardada a intervalos regulares crean [alertas de Log Analytics](../log-analytics/log-analytics-alerts.md).  Si los resultados de la consulta coinciden con los criterios especificados, se crea un registro de alertas y se ejecutan una o varias acciones.  

Las reglas de alerta en una solución de administración se componen de los tres siguientes recursos.

- **Búsqueda guardada.**  Define la búsqueda de registros que se va a ejecutar.  Varias reglas de alerta pueden compartir una única búsqueda guardada.
- **Programación.**  Define la frecuencia con la que se va a ejecutar la búsqueda de registros.  Cada regla de alerta tendrá una única programación.
- **Acción de alerta.**  Cada regla de alerta tendrá un único recurso de acción con un tipo de **alerta** que define los detalles de la alerta, como los criterios de cuándo se creará un registro de alertas y la gravedad de la alerta.  El recurso de acción definirá, opcionalmente, una respuesta de correo electrónico y de runbook.
- **Acción de webhook (opcional).**  Si la regla de alerta llama a un webhook, se requiere un recurso de acción adicional con un tipo de **Webhook**.    

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

| Nombre del elemento | Obligatorio | Descripción |
|:--|:--|:--|
| enabled       | Sí | Especifica si la alerta está habilitada cuando se crea. |
| interval      | Sí | Frecuencia con la que se ejecuta la consulta en minutos. |
| queryTimeSpan | Sí | Período de tiempo en minutos en el que se evalúan los resultados. |

El recurso de programación debe depender de la búsqueda guardada para que se cree antes de la programación.


### <a name="actions"></a>Acciones
Hay dos tipos de recursos de acción especificados por la propiedad **Type**.  Una programación requiere una acción **Alert** que define los detalles de la regla de alerta y las acciones que se realizan cuando se crea una alerta.  También puede incluir una acción **Webhook** si se debe llamar a un webhook desde la alerta.  

Los recursos de acción tienen un tipo de `Microsoft.OperationalInsights/workspaces/savedSearches/schedules/actions`.  

#### <a name="alert-actions"></a>Acciones de alerta

Cada programación tendrá una acción **Alert**.  Esto define los detalles de la alerta y, opcionalmente, las acciones de notificación y corrección.  Una notificación envía un mensaje de correo electrónico a una o varias direcciones.  Una corrección inicia un runbook en Azure Automation para intentar corregir el problema detectado.

Las acciones de alerta tienen la siguiente estructura.  Aquí se incluyen las variables y los parámetros habituales para que pueda copiar y pegar este fragmento de código en su archivo de solución y cambiar los nombres de parámetro. 



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
            "emailNotification": {
                "recipients": [
                    "[variables('Alert').Recipients]"
                ],
                "subject": "[variables('Alert').Subject]"
            },
            "remediation": {
                "runbookName": "[variables('Alert').Remedition.RunbookName]",
                "webhookUri": "[variables('Alert').Remedition.WebhookUri]"
            }
        }
    }

En las tablas siguientes se describen las propiedades para los recursos de acción de alerta.

| Nombre del elemento | Obligatorio | Descripción |
|:--|:--|:--|
| Tipo | Sí | Tipo de la acción.  Será **Alert** para las acciones de alerta. |
| Nombre | Sí | Nombre para mostrar de la alerta.  Es el nombre que se muestra en la consola para la regla de alerta. |
| Descripción | No | Descripción opcional de la alerta. |
| Gravedad | Sí | Gravedad del registro de alertas según los siguientes valores:<br><br> **Critical)** (Crítico)<br>**Warning (ADVERTENCIA)**<br>**Informational** (Informativo) |


##### <a name="threshold"></a>Umbral
Esta sección es obligatoria.  Define las propiedades para el umbral de alerta.

| Nombre del elemento | Obligatorio | Descripción |
|:--|:--|:--|
| Operador | Sí | Operador para la comparación según los valores siguientes:<br><br>**gt = mayor que<br>lt = menor que** |
| Valor | Sí | Valor para comparar los resultados. |


##### <a name="metricstrigger"></a>MetricsTrigger
Esta sección es opcional.  Inclúyala para una alerta de unidades métricas.

> [!NOTE]
> Las alertas de unidades métricas están actualmente en versión preliminar pública. 

| Nombre del elemento | Obligatorio | Descripción |
|:--|:--|:--|
| TriggerCondition | Sí | Especifica si el umbral es para el número total de infracciones o para infracciones consecutivas con los siguientes valores:<br><br>**Total<br>Consecutive** (Total, Consecutivos) |
| Operador | Sí | Operador para la comparación según los valores siguientes:<br><br>**gt = mayor que<br>lt = menor que** |
| Valor | Sí | Número de veces que se deben cumplir los criterios para desencadenar la alerta. |

##### <a name="throttling"></a>Limitaciones
Esta sección es opcional.  Incluya esta sección si desea suprimir alertas en la misma regla durante cierto tiempo después de crear una alerta.

| Nombre del elemento | Obligatorio | Descripción |
|:--|:--|:--|
| DurationInMinutes | Sí, si hay una limitación de elementos incluida | Número de minutos para suprimir alertas después de crear una en la misma regla de alerta. |

##### <a name="emailnotification"></a>EmailNotification
 Esta sección es opcional. Inclúyala si desea que la alerta envíe un mensaje de correo electrónico a uno o varios destinatarios.

| Nombre del elemento | Obligatorio | Descripción |
|:--|:--|:--|
| Recipients | Sí | Lista delimitada por comas de direcciones de correo electrónico para envío de notificación cuando se crea una alerta, como en el ejemplo siguiente.<br><br>**[ "recipient1@contoso.com", "recipient2@contoso.com" ]** |
| Asunto | Sí | Línea del asunto del mensaje de correo electrónico. |
| Datos adjuntos | No | Los datos adjuntos no son compatibles actualmente.  Si este elemento está incluido, debe ser **None** (Ninguno). |


##### <a name="remediation"></a>Corrección
Esta sección es opcional. Inclúyala si desea que se inicie un runbook en respuesta a la alerta. |

| Nombre del elemento | Obligatorio | Descripción |
|:--|:--|:--|
| RunbookName | Sí | Nombre del runbook que se va a iniciar. |
| WebhookUri | Sí | URI del webhook para el runbook. |
| Expiry | No | Fecha y hora a la que expira la corrección. |

#### <a name="webhook-actions"></a>Acciones de webhook

Las acciones de webhook inician un proceso llamando a una dirección URL y, opcionalmente, proporcionando una carga que se va a enviar. Son similares a las acciones de corrección, salvo por el hecho de que están pensadas para webhooks que pueden invocar procesos que no tienen que ver con Runbooks de Automatización de Azure. También ofrecen la posibilidad extra de proporcionar una carga adicional para entregarla en el proceso remoto.

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

| Nombre del elemento | Obligatorio | Descripción |
|:--|:--|:--|
| type | Sí | Tipo de la acción.  Será **Webhook** para las acciones de webhook. |
| name | Sí | Nombre para mostrar de la acción.  Esto no se muestra en la consola. |
| wehookUri | Sí | URI del webhook. |
| customPayload | No | Carga personalizada que se va a enviar al webhook. El formato dependerá de lo que el webhook espere. |




## <a name="sample"></a>Muestra

A continuación se muestra un ejemplo de una solución que incluye los siguientes recursos:

- Búsqueda guardada
- Schedule
- Acción de alerta
- Acción de webhook

En el ejemplo se utilizan variables de [parámetros de solución estándar](operations-management-suite-solutions-solution-file.md#parameters) que se suelen utilizar en una solución en lugar de codificar valores en las definiciones de recursos.

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
          "accountName": {
            "type": "string",
            "metadata": {
              "Description": "Name of Automation account"
            }
          },
          "workspaceregionId": {
            "type": "string",
            "metadata": {
              "Description": "Region of Log Analytics workspace"
            }
          },
          "regionId": {
            "type": "string",
            "metadata": {
              "Description": "Region of Automation account"
            }
          },
          "pricingTier": {
            "type": "string",
            "metadata": {
              "Description": "Pricing tier of both Log Analytics workspace and Azure Automation account"
            }
          },
          "recipients": {
            "type": "string",
            "metadata": {
              "Description": "List of recipients for the email alert separated by semicolon"
            }
          }
        },
        "variables": {
          "SolutionName": "MySolution",
          "SolutionVersion": "1.0",
          "SolutionPublisher": "Contoso",
          "ProductName": "SampleSolution",
    
          "LogAnalyticsApiVersion": "2015-11-01-preview",
    
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
            "Notification": {
              "Recipients": [
                "[parameters('recipients')]"
              ],
              "Subject": "Sample alert"
            },
            "Remediation": {
              "RunbookName": "MyRemediationRunbook",
              "WebhookUri": "https://s1events.azure-automation.net/webhooks?token=TluBFH3GpX4IEAnFoImoAWLTULkjD%2bTS0yscyrr7ogw%3d"
            },
            "Webhook": {
              "Name": "MyWebhook",
              "Uri": "https://MyService.com/webhook",
              "Payload": "{\"field1\":\"value1\",\"field2\":\"value2\"}"
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
                "[resourceId('Microsoft.OperationalInsights/workspaces/savedSearches/schedules/actions', parameters('workspacename'), variables('MySearch').Name, variables('MyAlert').Schedule.Name, variables('MyAlert').Name)]",
                "[resourceId('Microsoft.OperationalInsights/workspaces/savedSearches/schedules/actions', parameters('workspacename'), variables('MySearch').Name, variables('MyAlert').Schedule.Name, variables('MyAlert').Webhook.Name)]"
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
              "EmailNotification": {
                "Recipients": "[variables('MyAlert').Notification.Recipients]",
                "Subject": "[variables('MyAlert').Notification.Subject]",
                "Attachment": "None"
              },
              "Remediation": {
                "RunbookName": "[variables('MyAlert').Remediation.RunbookName]",
                "WebhookUri": "[variables('MyAlert').Remediation.WebhookUri]"
              }
            }
          },
          {
            "name": "[concat(parameters('workspaceName'), '/', variables('MySearch').Name, '/', variables('MyAlert').Schedule.Name, '/', variables('MyAlert').Webhook.Name)]",
            "type": "Microsoft.OperationalInsights/workspaces/savedSearches/schedules/actions",
            "apiVersion": "[variables('LogAnalyticsApiVersion')]",
            "dependsOn": [
              "[concat('Microsoft.OperationalInsights/workspaces/', parameters('workspaceName'), '/savedSearches/', variables('MySearch').Name, '/schedules/', variables('MyAlert').Schedule.Name)]",
              "[concat('Microsoft.OperationalInsights/workspaces/', parameters('workspaceName'), '/savedSearches/', variables('MySearch').Name, '/schedules/', variables('MyAlert').Schedule.Name, '/actions/',variables('MyAlert').Name)]"
            ],
            "properties": {
              "etag": "*",
              "Type": "Webhook",
              "Name": "[variables('MyAlert').Webhook.Name]",
              "WebhookUri": "[variables('MyAlert').Webhook.Uri]",
              "CustomPayload": "[variables('MyAlert').Webhook.Payload]"
            }
          }
        ]
    }


El siguiente archivo de parámetros proporciona valores de ejemplo para esta solución.

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
            "recipients": {
                "value": "recipient1@contoso.com;recipient2@contoso.com"
            }
        }
    }


## <a name="next-steps"></a>Pasos siguientes
* [Incorporación de vistas](operations-management-suite-solutions-resources-views.md) a la solución de administración.
* [Incorporación de runbooks de Automation y otros recursos](operations-management-suite-solutions-resources-automation.md) a la solución de administración.

