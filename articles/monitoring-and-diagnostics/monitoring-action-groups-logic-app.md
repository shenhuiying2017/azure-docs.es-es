---
title: Desencadenamiento de acciones complejas con grupos de acciones y alertas de Azure Monitor | Microsoft Docs
description: Obtenga información sobre cómo crear una acción de aplicación lógica para procesar las alertas de Azure Monitor.
author: dkamstra
manager: chrad
editor: ''
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.assetid: ''
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/30/2018
ms.author: dukek
ms.openlocfilehash: 0020f1475d52d01897320062edbd3a66c8acf751
ms.sourcegitcommit: 870d372785ffa8ca46346f4dfe215f245931dae1
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/08/2018
ms.locfileid: "33887455"
---
# <a name="create-a-logic-app-action"></a>Creación de una acción de aplicación lógica
## <a name="overview"></a>Información general ##
Cuando se desencadena una alerta de Azure Monitor, llama a un [grupo de acciones](monitoring-action-groups.md). Los grupos de acciones permiten desencadenar una o varias acciones para notificar sobre la alerta a los usuarios e, incluso, resolverla.

En este artículo se muestra cómo configurar y desencadenar una aplicación lógica para crear una conversación en Microsoft Teams cuando se activa una alerta.

El proceso general es:

-   crear la aplicación lógica para el tipo de alerta respectivo

-   Importar el esquema para el tipo de alerta respectivo a la aplicación lógica

-   Definir el comportamiento de la aplicación lógica

-   Copiar el punto de conexión HTTP de la conexión lógica en un grupo de acciones de Azure

El proceso es similar si quiere que la aplicación lógica lleve a cabo otra acción.

## <a name="create-an-activity-log-alert--administrative"></a>Creación de una alerta del registro de actividad: administrativa

1.  Haga clic en el botón **Crear un recurso** de la esquina superior izquierda de Azure Portal.

2.  Busque y seleccione **Aplicación lógica**. Haga clic en el botón **Crear** .

3.  Asigne un nombre a la aplicación lógica, elija un grupo de recursos, etc.

    ![Cuadro de diálogo Crear aplicación lógica](media/monitoring-action-groups/create-logic-app-dialog.png "Cuadro de diálogo Crear aplicación lógica")

4.  Haga clic en el botón Crear para crear la aplicación lógica. Una vez creada la aplicación lógica, aparecerá una ventana emergente. Haga clic en el botón Iniciar recurso para abrir el diseñador de Logic Apps.

5.  Seleccione el desencadenador **Cuando se recibe una solicitud HTTP**.

    ![Desencadenadores de aplicación lógica](media/monitoring-action-groups/logic-app-triggers.png "Desencadenadores de aplicación lógica")

6.  Seleccione **Editar** para cambiar el desencadenador de solicitud HTTP.

    ![Forma del desencadenador de solicitud HTTP](media/monitoring-action-groups/http-request-trigger-shape.png "Forma del desencadenador de solicitud HTTP")

7.  Seleccione **Usar una carga de ejemplo para generar el esquema**.

    ![Botón Usar carga de ejemplo](media/monitoring-action-groups/use-sample-payload-button.png "Botón Usar carga de ejemplo")

8.  Copie y pegue el esquema de ejemplo siguiente en el cuadro de diálogo.

    ```json
        {
            "schemaId": "Microsoft.Insights/activityLogs",
            "data": {
                "status": "Activated",
                "context": {
                "activityLog": {
                    "authorization": {
                    "action": "microsoft.insights/activityLogAlerts/write",
                    "scope": "/subscriptions/…"
                    },
                    "channels": "Operation",
                    "claims": "…",
                    "caller": "logicappdemo@contoso.com",
                    "correlationId": "91ad2bac-1afa-4932-a2ce-2f8efd6765a3",
                    "description": "",
                    "eventSource": "Administrative",
                    "eventTimestamp": "2018-04-03T22:33:11.762469+00:00",
                    "eventDataId": "ec74c4a2-d7ae-48c3-a4d0-2684a1611ca0",
                    "level": "Informational",
                    "operationName": "microsoft.insights/activityLogAlerts/write",
                    "operationId": "61f59fc8-1442-4c74-9f5f-937392a9723c",
                    "resourceId": "/subscriptions/…",
                    "resourceGroupName": "LOGICAPP-DEMO",
                    "resourceProviderName": "microsoft.insights",
                    "status": "Succeeded",
                    "subStatus": "",
                    "subscriptionId": "…",
                    "submissionTimestamp": "2018-04-03T22:33:36.1068742+00:00",
                    "resourceType": "microsoft.insights/activityLogAlerts"
                }
                },
                "properties": {}
            }
        }
    ```

9. El diseñador de Logic Apps mostrará una nota para recordarle que la solicitud enviada a la aplicación lógica debe establecer el encabezado Content-Type en application/json. Continúe y descarte el cuadro de diálogo. La alerta de Azure Monitor lo hará correctamente.

    ![Encabezado Content-Type](media/monitoring-action-groups/content-type-header.png "Encabezado Content-Type")

10. Seleccione **+ Nuevo paso** y luego elija **Agregar una acción**.

    ![Agregar acción](media/monitoring-action-groups/add-action.png "Agregar acción")

11. Busque y seleccione el conector de Microsoft Teams. Elija la acción **Microsoft Teams – Post message** (Microsoft Teams: publicar mensaje).

    ![Acciones de Microsoft Teams](media/monitoring-action-groups/microsoft-teams-actions.png "Acciones de Microsoft Teams")

12. Configure la acción de Microsoft Teams. El diseñador de Logic Apps le pedirá que se autentique en su cuenta de Office365. Elija el **identificador de equipo** y el **identificador de canal** para enviar el mensaje.

13. Configure el **mensaje** con una combinación de texto estático y referencias a los \<campos\> en el contexto dinámico. Corte y pegue el texto siguiente en el campo Message (Mensaje).

    ```text
      Activity Log Alert: <eventSource>
      operationName: <operationName>
      status: <status>
      resourceId: <resourceId>
    ```

    Luego busque y reemplace los \<campos\> con etiquetas de contenido dinámico con el mismo nombre.

    **[NOTA]** Hay dos campos dinámicos denominados **status**. Agregue ambos campos de estado al mensaje. Use uno en el contenedor de propiedades activityLog y elimine el otro. Si mantiene el puntero del mouse sobre el campo **status**, verá la referencia de campo completa como se indica en la captura de pantalla.

    ![Acción de Teams: publicar mensaje](media/monitoring-action-groups/teams-action-post-message.png "Acción de Teams: publicar mensaje")

14. Para guardar la aplicación lógica, haga clic en el botón Guardar que se encuentra en la parte superior del diseñador.

15. Haga clic en la forma de la solicitud HTTP para expandirla. Copie la dirección URL de HTTP POST.

    ![Dirección URL de HTTP POST](media/monitoring-action-groups/http-post-url.png "Dirección URL de HTTP POST")

16. Abra el grupo de acciones existente y agregue una acción para hacer referencia a la aplicación lógica. Si no tiene un grupo de acciones existente, consulte <https://docs.microsoft.com/en-us/azure/monitoring-and-diagnostics/monitoring-action-groups> para crearlo. No olvide guardar los cambios.

    ![Actualizar grupo de acciones](media/monitoring-action-groups/update-action-group.png "Actualizar grupo de acciones")

La próxima vez que una alerta invoque el grupo de acciones, se llamará a la aplicación lógica.

## <a name="create-a-service-health-alert"></a>Creación de una alerta de Service Health

Las entradas de Service Health forman parte del registro de actividad, por lo que el proceso es similar con los cambios siguientes.

1.  Los pasos del 1 al 7 son iguales.
2.  Use el esquema de ejemplo siguiente para el desencadenador HTTP del paso 8.

    ```json
    {
        "schemaId": "Microsoft.Insights/activityLogs",
        "data": {
            "status": "Activated",
            "context": {
                "activityLog": {
                    "channels": "Admin",
                    "correlationId": "e416ed3c-8874-4ec8-bc6b-54e3c92a24d4",
                    "description": "…",
                    "eventSource": "ServiceHealth",
                    "eventTimestamp": "2018-04-03T22:44:43.7467716+00:00",
                    "eventDataId": "9ce152f5-d435-ee31-2dce-104228486a6d",
                    "level": "Informational",
                    "operationName": "Microsoft.ServiceHealth/incident/action",
                    "operationId": "e416ed3c-8874-4ec8-bc6b-54e3c92a24d4",
                    "properties": {
                        "title": "…",
                        "service": "…",
                        "region": "Global",
                        "communication": "…",
                        "incidentType": "Incident",
                        "trackingId": "…",
                        "impactStartTime": "2018-03-22T21:40:00.0000000Z",
                        "impactMitigationTime": "2018-03-22T21:41:00.0000000Z",
                        "impactedServices": "[{"ImpactedRegions"}]",
                        "defaultLanguageTitle": "…",
                        "defaultLanguageContent": "…",
                        "stage": "Active",
                        "communicationId": "11000001466525",
                        "version": "0.1.1"
                    },
                    "status": "Active",
                    "subscriptionId": "…",
                    "submissionTimestamp": "2018-04-03T22:44:50.8013523+00:00"
                }
            },
            "properties": {}
        }
    }
    ```

3.  Los pasos 9 y 10 son iguales.
4.  A partir del paso 11, use el proceso a continuación.
5.  Haga clic en el botón **+ Nuevo paso** y elija **Agregar una condición**. Establezca las condiciones siguientes para garantizar que la aplicación lógica solo se ejecute cuando los datos de entrada coincidan con estos valores.
    - schemaId == Microsoft.Insights/activityLogs
    - eventSource == ServiceHealth
    - version == 0.1.1

    !["Condición de la carga de Service Health"](media/monitoring-action-groups/service-health-payload-condition.png "Condición de la carga de Service Health")

6. En la condición **if true**, use los pasos del 11 al 13 del ejemplo anterior para agregar la acción de Microsoft Teams.

7. Defina el mensaje con una combinación de HTML y [contenido dinámico]. Copie y pegue el contenido que aparece a continuación en el campo del mensaje. Reemplace los campos [incidentType], [trackingID], [title] y [communication] por etiquetas de contenido dinámico con el mismo nombre.

    ```html
    <p>
    <b>Alert Type:</b>&nbsp;<strong>[incidentType]</strong>&nbsp;
    <strong>Tracking ID:</strong>&nbsp;[trackingId]&nbsp;
    <strong>Title:</strong>&nbsp;[title]</p>
    <p>
    <a href="https://ms.portal.azure.com/#blade/Microsoft_Azure_Health/AzureHealthBrowseBlade/serviceIssues">For details log into the Azure Service Health dashboard</a>
    </p>
    <p>[communication]</p>
    ```

    !["Acción posterior a la condición true de Service Health"](media/monitoring-action-groups/service-health-true-condition-post-action.png "Acción posterior a la condición true de Service Health")

8. Para la condición **If false**, proporcione un mensaje útil.

    ```html
    <p><strong>Service Health Alert</strong></p>
    <p><b>Unrecognized alert schema</b></p>
    <p><a href="https://ms.portal.azure.com/#blade/Microsoft_Azure_Health/AzureHealthBrowseBlade/serviceIssues">For details log into the Azure Service Health dashboard\</a></p>
    ```

    !["Acción posterior a la condición false de Service Health"](media/monitoring-action-groups/service-health-false-condition-post-action.png "Acción posterior a la condición false de Service Health")

9.  Siga los pasos 15 y 16 del ejemplo anterior para guardar la aplicación lógica y actualizar el grupo de acciones.

## <a name="metric-alert"></a>Alerta de métrica

1.  Los pasos del 1 al 7 son iguales que en el primer ejemplo.
2.  Use el esquema de ejemplo siguiente para el desencadenador HTTP del paso 8.

    ```json
    {
    "schemaId": "AzureMonitorMetricAlert",
    "data": {
        "version": "2.0",
        "status": "Activated",
        "context": {
        "timestamp": "2018-04-09T19:00:07.7461615Z",
        "id": "…",
        "name": "TEST-VM CPU Utilization",
        "description": "",
        "conditionType": "SingleResourceMultipleMetricCriteria",
        "condition": {
            "windowSize": "PT15M",
            "allOf": [
                {
                    "metricName": "Percentage CPU",
                    "dimensions": [
                    {
                        "name": "ResourceId",
                        "value": "d92fc5cb-06cf-4309-8c9a-538eea6a17a6"
                    }
                ],
                "operator": "GreaterThan",
                "threshold": "5",
                "timeAggregation": "PT15M",
                "metricValue": 1.0
            }
            ]
        },
        "subscriptionId": "…",
        "resourceGroupName": "TEST",
        "resourceName": "test-vm",
        "resourceType": "Microsoft.Compute/virtualMachines",
        "resourceId": "…",
        "portalLink": "…"
        },
        "properties": {}
    }
    }
    ```

3.  Los pasos 9 y 10 son iguales.
4.  A partir del paso 11, use el proceso a continuación.
5.  Haga clic en el botón **+ Nuevo paso** y elija **Agregar una condición**. Establezca las condiciones siguientes para garantizar que la aplicación lógica solo se ejecute cuando los datos de entrada coincidan con estos valores.

    - schemaId == AzureMonitorMetricAlert
    - version == 2.0

    !["Condición de carga de la alerta métrica"](media/monitoring-action-groups/metric-alert-payload-condition.png "Condición de carga de la alerta métrica")

6.  En la condición **if true**, agregaremos una forma **For each** y la acción de Microsoft Teams y definiremos el mensaje con una combinación de HTML y [contenido dinámico].

    !["Acción posterior a la condición true de la alerta de métrica"](media/monitoring-action-groups/metric-alert-true-condition-post-action.png "Acción posterior a la condición true de la alerta de métrica")

7.  En la forma **If false**, defina una acción de Microsoft Teams que comunique que la alerta de métrica no coincide con las expectativas de la aplicación lógica e incluya la carga de JSON. Observe cómo hacemos referencia al contenido dinámico triggerBody en la expresión json().

    !["Acción posterior a la condición false de la alerta de métrica"](media/monitoring-action-groups/metric-alert-false-condition-post-action.png "Acción posterior a la condición false de la alerta de métrica")

8.  Siga los pasos 15 y 16 del primer ejemplo para guardar la aplicación lógica y actualizar el grupo de acciones.

## <a name="next-steps"></a>Pasos siguientes ##
* Consulte la [introducción a las alertas del registro de actividad](monitoring-overview-alerts.md) y aprenda cómo puede recibir alertas.  
* Aprenda a [configurar alertas siempre que se publique una notificación de mantenimiento de un servicio](monitoring-activity-log-alerts-on-service-notifications.md).
* Más información sobre los [grupos de acciones](monitoring-action-groups.md)