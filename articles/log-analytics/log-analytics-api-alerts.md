---
title: Uso de la API de REST de alertas de Log Analytics de OMS
description: Con la API de REST de alertas de Log Analytics se pueden crear y administrar alertas de Log Analytics, que forma parte de Operations Management Suite (OMS).  En este artículo encontrará información detallada sobre la API y varios ejemplos para realizar distintas operaciones.
services: log-analytics
documentationcenter: ''
author: bwren
manager: jwhit
editor: tysonn
ms.assetid: 628ad256-7181-4a0d-9e68-4ed60c0f3f04
ms.service: log-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/10/2018
ms.author: bwren
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 642b27405c703aa7a30d9fc544009d70b5d1b2df
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 04/16/2018
---
# <a name="create-and-manage-alert-rules-in-log-analytics-with-rest-api"></a>Creación y administración de reglas de alerta de Log Analytics con la API de REST
Con la API de REST de alertas de Log Analytics se pueden crear y administrar alertas de Operations Management Suite (OMS).  En este artículo encontrará información detallada sobre la API y varios ejemplos para realizar distintas operaciones.

La API de REST de búsqueda de Log Analytics es de tipo RESTful y se puede obtener acceso a ella a través de la API de REST de Azure Resource Manager. En este documento encontrará ejemplos donde se tiene acceso a la API desde una línea de comandos de PowerShell a través de [ARMClient](https://github.com/projectkudu/ARMClient), una herramienta de línea de comandos de código abierto que simplifica la tarea de invocar a la API de Azure Resource Manager. El uso de ARMClient y PowerShell es una de las muchas opciones para tener acceso a la API de búsqueda de Log Analytics. Con estas herramientas, puede usar la API de Azure Resource Manager de RESTful para realizar llamadas a las áreas de trabajo de OMS y ejecutar comandos de búsqueda dentro de ellas. La API generará resultados de búsqueda, en formato JSON, lo que le permite usar los resultados de búsqueda de muchas formas distintas mediante programación.

## <a name="prerequisites"></a>requisitos previos
Actualmente, solo se pueden crear alertas con una búsqueda guardada en Log Analytics.  Para obtener más información, consulte [API de búsqueda de registros de Log Analytics](log-analytics-log-search-api.md) .

## <a name="schedules"></a>Programaciones
Una búsqueda guardada puede tener una o varias programaciones. La programación define la frecuencia con que se realiza la búsqueda y el intervalo de tiempo en el que se identifican los criterios.
Las programaciones tienen las propiedades de la siguiente tabla.

| Propiedad | DESCRIPCIÓN |
|:--- |:--- |
| Intervalo |Frecuencia con que se realiza la búsqueda. Se mide en minutos. |
| QueryTimeSpan |Intervalo de tiempo en el que se evalúan los criterios. Debe ser igual o mayor que Intervalo. Se mide en minutos. |
| Versión |Versión de API en uso.  Actualmente, siempre debe estar establecida en 1. |

Por ejemplo, en una consulta de evento con un valor de Intervalo de 15 minutos y un valor de Timespan de 30 minutos, la consulta se ejecutaría cada 15 minutos y se desencadenaría una alerta si los criterios siguieran evaluándose como True durante un intervalo de 30 minutos.

### <a name="retrieving-schedules"></a>Recuperar programaciones
Use el método Get para recuperar todas las programaciones de una búsqueda guardada.

    armclient get /subscriptions/{Subscription ID}/resourceGroups/OI-Default-East-US/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search  ID}/schedules?api-version=2015-03-20

Use el método Get con un identificador de programación para recuperar una programación concreta de una búsqueda guardada.

    armclient get /subscriptions/{Subscription ID}/resourceGroups/OI-Default-East-US/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Subscription ID}/schedules/{Schedule ID}?api-version=2015-03-20

La siguiente es una respuesta de ejemplo de una programación.

```json
{
    "value": [{
        "id": "subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/OI-Default-East-US/providers/Microsoft.OperationalInsights/workspaces/MyWorkspace/savedSearches/0f0f4853-17f8-4ed1-9a03-8e888b0d16ec/schedules/a17b53ef-bd70-4ca4-9ead-83b00f2024a8",
        "etag": "W/\"datetime'2016-02-25T20%3A54%3A49.8074679Z'\"",
        "properties": {
            "Interval": 15,
            "QueryTimeSpan": 15
        }
    }]
}
```

### <a name="creating-a-schedule"></a>Creación de una programación
Use el método Put con un identificador de programación único para crear una programación nueva.  Tenga en cuenta que dos programaciones no pueden tener el mismo identificador, aunque estén asociados a diferentes búsquedas guardadas.  Al crear una programación en la consola de OMS, se crea un GUID para el identificador de programación.

> [!NOTE]
> El nombre de todas las búsquedas guardadas, programaciones y acciones creadas con Log Analytics API debe estar en minúsculas.

    $scheduleJson = "{'properties': { 'Interval': 15, 'QueryTimeSpan':15, 'Active':'true' } }"
    armclient put /subscriptions/{Subscription ID}/resourceGroups/OI-Default-East-US/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/mynewschedule?api-version=2015-03-20 $scheduleJson

### <a name="editing-a-schedule"></a>Edición de una programación
Utilice el método Put con un identificador de programación existente para modificar la programación de la misma búsqueda guardada.  El cuerpo de la solicitud debe incluir el valor etag de la programación.

      $scheduleJson = "{'etag': 'W/\"datetime'2016-02-25T20%3A54%3A49.8074679Z'\""','properties': { 'Interval': 15, 'QueryTimeSpan':15, 'Active':'true' } }"
      armclient put /subscriptions/{Subscription ID}/resourceGroups/OI-Default-East-US/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/mynewschedule?api-version=2015-03-20 $scheduleJson


### <a name="deleting-schedules"></a>Eliminar programaciones
Para eliminar una programación, use el método Delete con un identificador de programación.

    armclient delete /subscriptions/{Subscription ID}/resourceGroups/OI-Default-East-US/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Subscription ID}/schedules/{Schedule ID}?api-version=2015-03-20


## <a name="actions"></a>Acciones
Una programación puede tener varias acciones. Una acción puede definir uno o varios procesos que se van a realizar (como enviar un correo o iniciar un Runbook), o bien puede definir un umbral que determina si los resultados de una búsqueda coinciden con algunos criterios.  Algunas acciones definen ambos aspectos, de forma que los procesos se realizan cuando se alcance el umbral.

Todas las acciones tienen las propiedades de la siguiente tabla.  Los distintos tipos de alertas tienen diferentes propiedades adicionales, descritas aquí.

| Propiedad | DESCRIPCIÓN |
|:--- |:--- |
| Escriba |Tipo de la acción.  Actualmente, los valores posibles son Alert y Webhook. |
| NOMBRE |Nombre para mostrar de la alerta. |
| Versión |Versión de API en uso.  Actualmente, siempre debe estar establecida en 1. |

### <a name="retrieving-actions"></a>Recuperar acciones

> [!NOTE]
> A partir del 14 de mayo de 2018, todas las alertas de un área de trabajo se extenderán automáticamente a Azure. Los usuarios pueden decidir si desean iniciar la extensión de alertas a Azure antes del 14 de mayo de 2018. Para más información, consulte [Extensión de alertas de OMS a Azure](../monitoring-and-diagnostics/monitoring-alerts-extend.md). En el caso de los usuarios que extienden las alertas a Azure, ahora las acciones se controlan en los grupos de acciones de Azure. Cuando un área de trabajo y sus alertas se extienden a Azure, es posible recuperar o agregar acciones mediante la [API Action Group](https://docs.microsoft.com/en-us/rest/api/monitor/actiongroups).

Use el método Get para recuperar todas las acciones de una programación.

    armclient get /subscriptions/{Subscription ID}/resourceGroups/OI-Default-East-US/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search  ID}/schedules/{Schedule ID}/actions?api-version=2015-03-20

Use el método Get con el identificador de una acción para recuperar esa acción concreta para una programación.

    armclient get /subscriptions/{Subscription ID}/resourceGroups/OI-Default-East-US/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Subscription ID}/schedules/{Schedule ID}/actions/{Action ID}?api-version=2015-03-20

### <a name="creating-or-editing-actions"></a>Crear o editar acciones
Para crear una acción, use el método Put con un identificador de acción único de la programación.  Al crear una acción en la consola de OMS, se crea un GUID para el identificador de acción.

> [!NOTE]
> El nombre de todas las búsquedas guardadas, programaciones y acciones creadas con Log Analytics API debe estar en minúsculas.

Utilice el método Put con un identificador de acción existente para modificar la programación de la misma búsqueda guardada.  El cuerpo de la solicitud debe incluir el valor etag de la programación.

El formato de solicitud para crear una acción varía según el tipo de acción. En las secciones siguientes se proporcionan ejemplos.

### <a name="deleting-actions"></a>Eliminar acciones

> [!NOTE]
> A partir del 14 de mayo de 2018, todas las alertas de un área de trabajo se extenderán automáticamente a Azure. Los usuarios pueden decidir si desean iniciar la extensión de alertas a Azure antes del 14 de mayo de 2018. Para más información, consulte [Extensión de alertas de OMS a Azure](../monitoring-and-diagnostics/monitoring-alerts-extend.md). En el caso de los usuarios que extienden las alertas a Azure, ahora las acciones se controlan en los grupos de acciones de Azure. Cuando un área de trabajo y sus alertas se extienden a Azure, es posible recuperar o agregar acciones mediante la [API Action Group](https://docs.microsoft.com/en-us/rest/api/monitor/actiongroups).

Use el método Delete con el identificador de acción para eliminar esa acción.

    armclient delete /subscriptions/{Subscription ID}/resourceGroups/OI-Default-East-US/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Subscription ID}/schedules/{Schedule ID}/Actions/{Action ID}?api-version=2015-03-20

### <a name="alert-actions"></a>Acciones de alerta
Una programación debe tener una acción de alerta única y exclusivamente.  Las acciones de alerta tienen una o varias de las secciones de la siguiente tabla.  Cada una de ellas se describe con más detalle abajo.

| Sección | DESCRIPCIÓN | Uso |
|:--- |:--- |:--- |
| Umbral |Criterios para establecer cuándo se va a ejecutar la acción.| Se requiere para cada alerta, ya sea antes o después de extenderlas a Azure. |
| Severity |Etiqueta que se usa para clasificar la alerta cuando se desencadena.| Se requiere para cada alerta, ya sea antes o después de extenderlas a Azure. |
| Grupos de acciones |Identificadores para Azure ActionGroup donde se especifican las acciones requeridas, como correos electrónicos, mensajes SMS, llamadas de voz, webhooks, runbooks de Automation, conectores ITSM, etc.| Se requiere una vez que las alertas se extienden a Azure|
| Personalizar las acciones|Modifica la salida estándar para seleccionar acciones en ActionGroup| Opcional para cada alerta, se puede usar después de que las alertas se extienden a Azure. |
| EmailNotification |Se envía un correo electrónico a varios destinatarios. | No se requiere si las alertas se extendieron a Azure|
| Corrección |Se inicia un Runbook en Azure Automation para intentar corregir el problema detectado. |No se requiere si las alertas se extendieron a Azure|
| Acciones de Webhook | Inserta datos de Alertas al servicio deseado como JSON |No se requiere si las alertas se extendieron a Azure|

> [!NOTE]
> A partir del 14 de mayo de 2018, todas las alertas de un área de trabajo se extenderán automáticamente a Azure. Los usuarios pueden decidir si desean iniciar la extensión de alertas a Azure antes del 14 de mayo de 2018. Para más información, consulte [Extensión de alertas de OMS a Azure](../monitoring-and-diagnostics/monitoring-alerts-extend.md).

#### <a name="thresholds"></a>Umbrales
Una acción de alerta debe tener un umbral única y exclusivamente.  Cuando los resultados de una búsqueda guardada coinciden con el umbral de una acción asociada a esa búsqueda, se ejecutan los demás procesos de esa acción.  Una acción también puede contener solo un umbral para que pueda usarse con acciones de otros tipos que no contienen umbrales.

Los umbrales tienen las propiedades de la siguiente tabla.

| Propiedad | DESCRIPCIÓN |
|:--- |:--- |
| Operador |Operador de la comparación de umbral. <br> gt = Mayor que <br> lt = Menor que |
| Valor |Valor del umbral. |

Por ejemplo, en una consulta de evento con un valor de Intervalo de 15 minutos, un valor de Timespan de 30 minutos y un valor de Threshold mayor que 10, la consulta se ejecutaría cada 15 minutos y se desencadenaría una alerta si devolviera 10 eventos creados durante un intervalo de 30 minutos.

La siguiente es una respuesta de ejemplo de una acción con un solo umbral.  

    "etag": "W/\"datetime'2016-02-25T20%3A54%3A20.1302566Z'\"",
    "properties": {
        "Type": "Alert",
        "Name": "My threshold action",
        "Threshold": {
            "Operator": "gt",
            "Value": 10
        },
        "Version": 1
    }

Use el método Put con un identificador de acción único para crear una acción de umbral para una programación.  

    $thresholdJson = "{'properties': { 'Name': 'My Threshold', 'Version':'1', 'Type':'Alert', 'Threshold': { 'Operator': 'gt', 'Value': 10 } }"
    armclient put /subscriptions/{Subscription ID}/resourceGroups/OI-Default-East-US/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/{Schedule ID}/actions/mythreshold?api-version=2015-03-20 $thresholdJson

Use el método Put con un identificador de acción existente para modificar una acción de umbral para una programación.  El cuerpo de la solicitud debe incluir el valor etag de la acción.

    $thresholdJson = "{'etag': 'W/\"datetime'2016-02-25T20%3A54%3A20.1302566Z'\"','properties': { 'Name': 'My Threshold', 'Version':'1', 'Type':'Alert', 'Threshold': { 'Operator': 'gt', 'Value': 10 } }"
    armclient put /subscriptions/{Subscription ID}/resourceGroups/OI-Default-East-US/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/{Schedule ID}/actions/mythreshold?api-version=2015-03-20 $thresholdJson

#### <a name="severity"></a>Severity
Log Analytics permite clasificar las alertas en categorías, para permitir una administración y una evaluación de prioridades más sencillas. La gravedad de la alerta que se define es: informativa, advertencia y crítica. Estos valores se asignan a la escala de gravedad normalizada de Alertas de Azure como:

|Nivel de gravedad de Log Analytics  |Nivel de gravedad de Alertas de Azure  |
|---------|---------|
|Crítico |Gravedad 0|
|Warning (Advertencia) |Gravedad 1|
|Informational (Informativa) | Gravedad 2|

La siguiente es una respuesta de ejemplo de una acción con un solo umbral y gravedad. 

    "etag": "W/\"datetime'2016-02-25T20%3A54%3A20.1302566Z'\"",
    "properties": {
        "Type": "Alert",
        "Name": "My threshold action",
        "Threshold": {
            "Operator": "gt",
            "Value": 10
        },
        "Severity": "critical",
        "Version": 1    }

Use el método Put con un identificador de acción único para crear una acción para una programación con gravedad.  

    $thresholdWithSevJson = "{'properties': { 'Name': 'My Threshold', 'Version':'1','Severity': 'critical', 'Type':'Alert', 'Threshold': { 'Operator': 'gt', 'Value': 10 } }"
    armclient put /subscriptions/{Subscription ID}/resourceGroups/OI-Default-East-US/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/{Schedule ID}/actions/mythreshold?api-version=2015-03-20 $thresholdWithSevJson

Use el método Put con un identificador de acción existente para modificar una acción de gravedad para una programación.  El cuerpo de la solicitud debe incluir el valor etag de la acción.

    $thresholdWithSevJson = "{'etag': 'W/\"datetime'2016-02-25T20%3A54%3A20.1302566Z'\"','properties': { 'Name': 'My Threshold', 'Version':'1','Severity': 'critical', 'Type':'Alert', 'Threshold': { 'Operator': 'gt', 'Value': 10 } }"
    armclient put /subscriptions/{Subscription ID}/resourceGroups/OI-Default-East-US/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/{Schedule ID}/actions/mythreshold?api-version=2015-03-20 $thresholdWithSevJson

#### <a name="action-groups"></a>Grupos de acciones
Todas las alertas de Azure usan el grupo de acciones como el mecanismo predeterminado para controlar las acciones. Con el grupo de acciones, puede especificar las acciones una vez y, luego, asociar el grupo de acciones a varias alertas en todo Azure, sin la necesidad de declarar repetidamente una y otra vez las mismas acciones. Los grupos de acciones admiten varias acciones, incluido el correo electrónico, SMS, llamadas de voz, conexión ITSM, runbook de Automation, URI de webhook, etc. 

Para los usuarios que han extendido sus alertas a Azure, ahora una programación tendrá los detalles del grupo de acciones junto con el umbral, para así poder crear una alerta. Antes de crear una alerta, es necesario definir los detalles de correo electrónico, las direcciones URL de webhooks, los detalles de automatización de runbooks y otras acciones dentro de un grupo de acciones. Es posible crear un [grupo de acciones desde Azure Monitor](../monitoring-and-diagnostics/monitoring-action-groups.md) en el portal o usar la [API Action Group](https://docs.microsoft.com/en-us/rest/api/monitor/actiongroups).

Para agregar la asociación de un grupo de acciones a una alerta, especifique el identificador de Azure Resource Manager único del grupo de acciones en la definición de la alerta. A continuación, aparece una ilustración de ejemplo:

     "etag": "W/\"datetime'2017-12-13T10%3A52%3A21.1697364Z'\"",
      "properties": {
        "Type": "Alert",
        "Name": "test-alert",
        "Description": "I need to put a descriptio here",
        "Threshold": {
          "Operator": "gt",
          "Value": 12
        },
        "AzNsNotification": {
          "GroupIds": [
            "/subscriptions/1234a45-123d-4321-12aa-123b12a5678/resourcegroups/my-resource-group/providers/microsoft.insights/actiongroups/test-actiongroup"
          ]
        },
        "Severity": "critical",
        "Version": 1
      },

Use el método Put con un identificación de acción único para asociar el grupo de acciones ya existente para una programación.  Esta es una ilustración de ejemplo del uso.

    $AzNsJson = "{'properties': { 'Name': 'test-alert', 'Version':'1', 'Type':'Alert', 'Threshold': { 'Operator': 'gt', 'Value': 12 },'Severity': 'critical', 'AzNsNotification': {'GroupIds': ['subscriptions/1234a45-123d-4321-12aa-123b12a5678/resourcegroups/my-resource-group/providers/microsoft.insights/actiongroups/test-actiongroup']} }"
    armclient put /subscriptions/{Subscription ID}/resourceGroups/{Resource Group Name}/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/{Schedule ID}/actions/myAzNsaction?api-version=2015-03-20 $AzNsJson

Use el método Put con un identificador de acción existente para modificar un grupo de acciones asociado para una programación.  El cuerpo de la solicitud debe incluir el valor etag de la acción.

    $AzNsJson = "{'etag': 'datetime'2017-12-13T10%3A52%3A21.1697364Z'\"', properties': { 'Name': 'test-alert', 'Version':'1', 'Type':'Alert', 'Threshold': { 'Operator': 'gt', 'Value': 12 },'Severity': 'critical', 'AzNsNotification': {'GroupIds': ['subscriptions/1234a45-123d-4321-12aa-123b12a5678/resourcegroups/my-resource-group/providers/microsoft.insights/actiongroups/test-actiongroup']} }"
    armclient put /subscriptions/{Subscription ID}/resourceGroups/{Resource Group Name}/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/{Schedule ID}/actions/myAzNsaction?api-version=2015-03-20 $AzNsJson

#### <a name="customize-actions"></a>Personalizar las acciones
En el caso de las acciones predeterminadas, siga la plantilla y el formato estándar para las notificaciones. Pero los usuarios pueden personalizar algunas acciones, incluso si los controlan los grupos de acciones. Actualmente, es posible personalizar el asunto del correo electrónico y la carga del webhook.

##### <a name="customize-e-mail-subject-for-action-group"></a>Personalización del asunto del correo electrónico para el grupo de acciones
De manera predeterminada, el asunto del correo electrónico para las alertas es: Notificación de alerta <AlertName> para <WorkspaceName>. Pero se puede personalizar, de modo que pueda especificar palabras o etiquetas, para permitirle emplear fácilmente reglas de filtro en la Bandeja de entrada. Los detalles del encabezado del correo electrónico personalizado se deben enviar junto con los detalles de ActionGroup, como se muestra en el ejemplo siguiente.

     "etag": "W/\"datetime'2017-12-13T10%3A52%3A21.1697364Z'\"",
      "properties": {
        "Type": "Alert",
        "Name": "test-alert",
        "Description": "I need to put a descriptio here",
        "Threshold": {
          "Operator": "gt",
          "Value": 12
        },
        "AzNsNotification": {
          "GroupIds": [
            "/subscriptions/1234a45-123d-4321-12aa-123b12a5678/resourcegroups/my-resource-group/providers/microsoft.insights/actiongroups/test-actiongroup"
          ]
          "CustomEmailSubject": "Azure Alert fired"
        },
        "Severity": "critical",
        "Version": 1
      },

Use el método Put con un identificación de acción único para asociar el grupo de acciones ya existente con una personalización para una programación.  Esta es una ilustración de ejemplo del uso.

    $AzNsJson = "{'properties': { 'Name': 'test-alert', 'Version':'1', 'Type':'Alert', 'Threshold': { 'Operator': 'gt', 'Value': 12 },'Severity': 'critical', 'AzNsNotification': {'GroupIds': ['subscriptions/1234a45-123d-4321-12aa-123b12a5678/resourcegroups/my-resource-group/providers/microsoft.insights/actiongroups/test-actiongroup'], 'CustomEmailSubject': 'Azure Alert fired'} }"
    armclient put /subscriptions/{Subscription ID}/resourceGroups/{Resource Group Name}/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/{Schedule ID}/actions/myAzNsaction?api-version=2015-03-20 $AzNsJson

Use el método Put con un identificador de acción existente para modificar un grupo de acciones asociado para una programación.  El cuerpo de la solicitud debe incluir el valor etag de la acción.

    $AzNsJson = "{'etag': 'datetime'2017-12-13T10%3A52%3A21.1697364Z'\"', properties': { 'Name': 'test-alert', 'Version':'1', 'Type':'Alert', 'Threshold': { 'Operator': 'gt', 'Value': 12 },'Severity': 'critical', 'AzNsNotification': {'GroupIds': ['subscriptions/1234a45-123d-4321-12aa-123b12a5678/resourcegroups/my-resource-group/providers/microsoft.insights/actiongroups/test-actiongroup']}, 'CustomEmailSubject': 'Azure Alert fired' }"
    armclient put /subscriptions/{Subscription ID}/resourceGroups/{Resource Group Name}/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/{Schedule ID}/actions/myAzNsaction?api-version=2015-03-20 $AzNsJson

##### <a name="customize-webhook-payload-for-action-group"></a>Personalización de la carga de Webhook para un grupo de acciones
De manera predeterminada, el webhook enviado a través del grupo de acciones para Log Analytics tiene una estructura fija. Sin embargo, un usuario puede personalizar la carga de JSON si usa variables específicas admitidas para cumplir con los requisitos del punto de conexión del webhook. Para más información, consulte [Acciones de webhook para reglas de alertas de registro](../monitoring-and-diagnostics/monitor-alerts-unified-log-webhook.md). 

Los detalles del webhook personalizado se deben enviar junto con los detalles de ActionGroup y se aplicarán a todo los URI del webhook especificados dentro del grupo de acciones, como se indica en el ejemplo a continuación.

     "etag": "W/\"datetime'2017-12-13T10%3A52%3A21.1697364Z'\"",
      "properties": {
        "Type": "Alert",
        "Name": "test-alert",
        "Description": "I need to put a descriptio here",
        "Threshold": {
          "Operator": "gt",
          "Value": 12
        },
        "AzNsNotification": {
          "GroupIds": [
            "/subscriptions/1234a45-123d-4321-12aa-123b12a5678/resourcegroups/my-resource-group/providers/microsoft.insights/actiongroups/test-actiongroup"
          ]
          "CustomWebhookPayload": "{\"field1\":\"value1\",\"field2\":\"value2\"}",
          "CustomEmailSubject": "Azure Alert fired"
        },
        "Severity": "critical",
        "Version": 1
      },

Use el método Put con un identificación de acción único para asociar el grupo de acciones ya existente con una personalización para una programación.  Esta es una ilustración de ejemplo del uso.

    $AzNsJson = "{'properties': { 'Name': 'test-alert', 'Version':'1', 'Type':'Alert', 'Threshold': { 'Operator': 'gt', 'Value': 12 },'Severity': 'critical', 'AzNsNotification': {'GroupIds': ['subscriptions/1234a45-123d-4321-12aa-123b12a5678/resourcegroups/my-resource-group/providers/microsoft.insights/actiongroups/test-actiongroup'], 'CustomEmailSubject': 'Azure Alert fired','CustomWebhookPayload': '{\"field1\":\"value1\",\"field2\":\"value2\"}'} }"
    armclient put /subscriptions/{Subscription ID}/resourceGroups/{Resource Group Name}/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/{Schedule ID}/actions/myAzNsaction?api-version=2015-03-20 $AzNsJson

Use el método Put con un identificador de acción existente para modificar un grupo de acciones asociado para una programación.  El cuerpo de la solicitud debe incluir el valor etag de la acción.

    $AzNsJson = "{'etag': 'datetime'2017-12-13T10%3A52%3A21.1697364Z'\"', properties': { 'Name': 'test-alert', 'Version':'1', 'Type':'Alert', 'Threshold': { 'Operator': 'gt', 'Value': 12 },'Severity': 'critical', 'AzNsNotification': {'GroupIds': ['subscriptions/1234a45-123d-4321-12aa-123b12a5678/resourcegroups/my-resource-group/providers/microsoft.insights/actiongroups/test-actiongroup']}, 'CustomEmailSubject': 'Azure Alert fired','CustomWebhookPayload': '{\"field1\":\"value1\",\"field2\":\"value2\"}' }"
    armclient put /subscriptions/{Subscription ID}/resourceGroups/{Resource Group Name}/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/{Schedule ID}/actions/myAzNsaction?api-version=2015-03-20 $AzNsJson

#### <a name="email-notification"></a>Notificación por correo electrónico
Las notificaciones de correo electrónico envían correo a uno o más destinatarios.  Tienen las propiedades de la siguiente tabla.

> [!NOTE]
> A partir del 14 de mayo de 2018, todas las alertas de un área de trabajo se extenderán automáticamente a Azure. Los usuarios pueden decidir si desean iniciar la extensión de alertas a Azure antes del 14 de mayo de 2018. Para más información, consulte [Extensión de alertas de OMS a Azure](../monitoring-and-diagnostics/monitoring-alerts-extend.md). En el caso de los usuarios que extienden las alertas a Azure, ahora las acciones como la notificación de correo electrónico se controlan en los grupos de acciones de Azure. Cuando un área de trabajo y sus alertas se extienden a Azure, es posible recuperar o agregar acciones mediante la [API Action Group](https://docs.microsoft.com/en-us/rest/api/monitor/actiongroups).
   

| Propiedad | DESCRIPCIÓN |
|:--- |:--- |
| Recipients |Lista de direcciones de correo electrónico. |
| Asunto |Asunto del correo electrónico. |
| Datos adjuntos |Actualmente no se admiten datos adjuntos, por lo que siempre aparecerá un valor "None". |

La siguiente es una respuesta de ejemplo de una acción de notificación de correo con un umbral.  

    "etag": "W/\"datetime'2016-02-25T20%3A54%3A20.1302566Z'\"",
    "properties": {
        "Type": "Alert",
        "Name": "My email action",
        "Threshold": {
            "Operator": "gt",
            "Value": 10
        },
        "EmailNotification": {
            "Recipients": [
                "recipient1@contoso.com",
                "recipient2@contoso.com"
            ],
            "Subject": "This is the subject",
            "Attachment": "None"
        },
        "Version": 1
    }

Use el método Put con un identificador de acción único para crear una acción de correo electrónico para una programación.  En el siguiente ejemplo se crea una notificación de correo electrónico con un umbral para que el correo se envíe cuando los resultados de la búsqueda guardada superen el umbral.

    $emailJson = "{'properties': { 'Name': 'MyEmailAction', 'Version':'1', 'Type':'Alert', 'Threshold': { 'Operator': 'gt', 'Value': 10 }, 'EmailNotification': {'Recipients': ['recipient1@contoso.com', 'recipient2@contoso.com'], 'Subject':'This is the subject', 'Attachment':'None'} }"
    armclient put /subscriptions/{Subscription ID}/resourceGroups/OI-Default-East-US/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/{Schedule ID}/actions/myemailaction?api-version=2015-03-20 $emailJson

Use el método Put con un identificador de acción existente para modificar una acción de correo electrónico para una programación.  El cuerpo de la solicitud debe incluir el valor etag de la acción.

    $emailJson = "{'etag': 'W/\"datetime'2016-02-25T20%3A54%3A20.1302566Z'\"','properties': { 'Name': 'MyEmailAction', 'Version':'1', 'Type':'Alert', 'Threshold': { 'Operator': 'gt', 'Value': 10 }, 'EmailNotification': {'Recipients': ['recipient1@contoso.com', 'recipient2@contoso.com'], 'Subject':'This is the subject', 'Attachment':'None'} }"
    armclient put /subscriptions/{Subscription ID}/resourceGroups/OI-Default-East-US/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/{Schedule ID}/actions/myemailaction?api-version=2015-03-20 $emailJson

#### <a name="remediation-actions"></a>Acciones de corrección
Las correcciones inician un Runbook en Azure Automation que intenta corregir el problema identificado por la alerta.  Debe crear un webhook para el Runbook usado en una acción de corrección y, luego, especificar el URI en la propiedad WebhookUri.  Cuando esta acción se crea con la consola de OMS, se crea automáticamente un nuevo webhook para el Runbook.

> [!NOTE]
> A partir del 14 de mayo de 2018, todas las alertas de un área de trabajo se extenderán automáticamente a Azure. Los usuarios pueden decidir si desean iniciar la extensión de alertas a Azure antes del 14 de mayo de 2018. Para más información, consulte [Extensión de alertas de OMS a Azure](../monitoring-and-diagnostics/monitoring-alerts-extend.md). En el caso de los usuarios que extienden las alertas a Azure, ahora las acciones como la corrección mediante runbook se controlan en los grupos de acciones de Azure. Cuando un área de trabajo y sus alertas se extienden a Azure, es posible recuperar o agregar acciones mediante la [API Action Group](https://docs.microsoft.com/en-us/rest/api/monitor/actiongroups).

Las correcciones tienen las propiedades de la siguiente tabla.

| Propiedad | DESCRIPCIÓN |
|:--- |:--- |
| RunbookName |Nombre del Runbook. Debe coincidir con un Runbook publicado en la cuenta de Automation que configuró en la solución de Automation en el área de trabajo de OMS. |
| WebhookUri |URI del webhook. |
| Expiry |Fecha y hora de expiración del webhook.  Si el webhook no tiene una fecha expiración, puede ser cualquier fecha futura válida. |

La siguiente es una respuesta de ejemplo de una acción de corrección con un umbral.

    "etag": "W/\"datetime'2016-02-25T20%3A54%3A20.1302566Z'\"",
    "properties": {
        "Type": "Alert",
        "Name": "My remediation action",
        "Threshold": {
            "Operator": "gt",
            "Value": 10
        },
        "Remediation": {
            "RunbookName": "My-Runbook",
            "WebhookUri": "https://s1events.azure-automation.net/webhooks?token=4jCibOjO3w4W2Cfg%2b2NkjLYdafnusaG6i8tnP8h%2fNNg%3d",
            "Expiry": "2018-02-25T18:27:20"
            },
        "Version": 1
    }

Use el método Put con un identificador de acción único para crear una acción de corrección para una programación.  En el siguiente ejemplo se crea una corrección con un umbral para que el Runbook se inicie cuando los resultados de la búsqueda guardada superen el umbral.

    $remediateJson = "{'properties': { 'Type':'Alert', 'Name': 'My Remediation Action', 'Version':'1', 'Threshold': { 'Operator': 'gt', 'Value': 10 }, 'Remediation': {'RunbookName': 'My-Runbook', 'WebhookUri':'https://s1events.azure-automation.net/webhooks?token=4jCibOjO3w4W2Cfg%2b2NkjLYdafnusaG6i8tnP8h%2fNNg%3d', 'Expiry':'2018-02-25T18:27:20Z'} }"
    armclient put /subscriptions/{Subscription ID}/resourceGroups/OI-Default-East-US/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/{Schedule ID}/actions/myremediationaction?api-version=2015-03-20 $remediateJson

Use el método Put con un identificador de acción existente para modificar una acción de corrección para una programación.  El cuerpo de la solicitud debe incluir el valor etag de la acción.

    $remediateJson = "{'etag': 'W/\"datetime'2016-02-25T20%3A54%3A20.1302566Z'\"','properties': { 'Type':'Alert', 'Name': 'My Remediation Action', 'Version':'1', 'Threshold': { 'Operator': 'gt', 'Value': 10 }, 'Remediation': {'RunbookName': 'My-Runbook', 'WebhookUri':'https://s1events.azure-automation.net/webhooks?token=4jCibOjO3w4W2Cfg%2b2NkjLYdafnusaG6i8tnP8h%2fNNg%3d', 'Expiry':'2018-02-25T18:27:20Z'} }"
    armclient put /subscriptions/{Subscription ID}/resourceGroups/OI-Default-East-US/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/{Schedule ID}/actions/myremediationaction?api-version=2015-03-20 $remediateJson

#### <a name="example"></a>Ejemplo
El siguiente es un ejemplo completo que ilustra cómo crear una alerta de correo electrónico.  En él, se crea una programación junto con una acción que contiene un umbral y un correo electrónico.

    $subscriptionId = "3d56705e-5b26-5bcc-9368-dbc8d2fafbfc"
    $resourceGroup  = "MyResourceGroup"    
    $workspaceName    = "MyWorkspace"
    $searchId       = "MySearch"
    $scheduleId     = "MySchedule"
    $thresholdId    = "MyThreshold"
    $actionId       = "MyEmailAction"

    $scheduleJson = "{'properties': { 'Interval': 15, 'QueryTimeSpan':15, 'Active':'true' }"
    armclient put /subscriptions/$subscriptionId/resourceGroups/$resourceGroup/providers/Microsoft.OperationalInsights/workspaces/$workspaceName/savedSearches/$searchId/schedules/$scheduleId/?api-version=2015-03-20 $scheduleJson

    $emailJson = "{'properties': { 'Name': 'MyEmailAction', 'Version':'1', 'Severity':'Warning', 'Type':'Alert', 'Threshold': { 'Operator': 'gt', 'Value': 10 }, 'EmailNotification': {'Recipients': ['recipient1@contoso.com', 'recipient2@contoso.com'], 'Subject':'This is the subject', 'Attachment':'None'} }"
    armclient put /subscriptions/$subscriptionId/resourceGroups/$resourceGroup/providers/Microsoft.OperationalInsights/workspaces/$workspaceName/savedSearches/$searchId/schedules/$scheduleId/actions/$actionId/?api-version=2015-03-20 $emailJson

#### <a name="webhook-actions"></a>Acciones de webhook
Las acciones de webhook inician un proceso llamando a una dirección URL y, opcionalmente, proporcionando una carga que se va a enviar.  Son similares a las acciones de corrección, salvo por el hecho de que están pensadas para webhooks que pueden invocar procesos que no tienen que ver con Runbooks de Azure Automation.  También ofrecen la posibilidad extra de proporcionar una carga adicional para entregarla en el proceso remoto.

> [!NOTE]
> A partir del 14 de mayo de 2018, todas las alertas de un área de trabajo se extenderán automáticamente a Azure. Los usuarios pueden decidir si desean iniciar la extensión de alertas a Azure antes del 14 de mayo de 2018. Para más información, consulte [Extensión de alertas de OMS a Azure](../monitoring-and-diagnostics/monitoring-alerts-extend.md). En el caso de los usuarios que extienden las alertas a Azure, ahora las acciones como Webhook se controlan en los grupos de acciones de Azure. Cuando un área de trabajo y sus alertas se extienden a Azure, es posible recuperar o agregar acciones mediante la [API Action Group](https://docs.microsoft.com/en-us/rest/api/monitor/actiongroups).


Las acciones de webhook no tienen umbral, sino que deben agregarse a una programación que tenga una acción de alerta con un umbral.  

La siguiente es una respuesta de ejemplo de una acción de webhook y una acción de alerta asociada con un umbral.

    {
        "__metadata": {},
        "value": [
            {
                "id": "subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/OI-Default-East-US/providers/Microsoft.OperationalInsights/workspaces/bwren/savedSearches/2d1b30fb-7f48-4de5-9614-79ee244b52de/schedules/b80f5621-7217-4007-b32d-165d14377093/Actions/72884702-acf9-4653-bb67-f42436b342b4",
                "etag": "W/\"datetime'2016-02-26T20%3A25%3A00.6862124Z'\"",
                "properties": {
                    "Type": "Webhook",
                    "Name": "My Webhook Action",
                    "WebhookUri": "https://oaaswebhookdf.cloudapp.net/webhooks?token=VfkYTIlpk%2fc%2bJBP",
                    "CustomPayload": "{\"fielld1\":\"value1\",\"field2\":\"value2\"}",
                    "Version": 1
                }
            },
            {
                "id": "subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/OI-Default-East-US/providers/Microsoft.OperationalInsights/workspaces/bwren/savedSearches/2d1b30fb-7f48-4de5-9614-79ee244b52de/schedules/b80f5621-7217-4007-b32d-165d14377093/Actions/90a27cf8-71b7-4df2-b04f-54ed01f1e4b6",
                "etag": "W/\"datetime'2016-02-26T20%3A25%3A00.565204Z'\"",
                "properties": {
                    "Type": "Alert",
                    "Name": "Threshold for my webhook action",
                    "Threshold": {
                        "Operator": "gt",
                        "Value": 10
                    },
                    "Version": 1
                }
            }
        ]
    }

##### <a name="create-or-edit-a-webhook-action"></a>Crear o editar una acción de webhook
Use el método Put con un identificador de acción único para crear una acción de webhook para una programación.  En el siguiente ejemplo se crea una acción de webhook y una acción de alerta con un umbral para que el webhook se active cuando los resultados de la búsqueda guardada superen el umbral.

    $thresholdAction = "{'properties': { 'Name': 'My Threshold', 'Version':'1', 'Type':'Alert', 'Threshold': { 'Operator': 'gt', 'Value': 10 } }"
    armclient put /subscriptions/{Subscription ID}/resourceGroups/OI-Default-East-US/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/{Schedule ID}/actions/mythreshold?api-version=2015-03-20 $thresholdAction

    $webhookAction = "{'properties': {'Type': 'Webhook', 'Name': 'My Webhook", 'WebhookUri': 'https://oaaswebhookdf.cloudapp.net/webhooks?token=VrkYTKlhk%2fc%2bKBP', 'CustomPayload': '{\"field1\":\"value1\",\"field2\":\"value2\"}', 'Version': 1 }"
    armclient put /subscriptions/{Subscription ID}/resourceGroups/OI-Default-East-US/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/{Schedule ID}/actions/mywebhookaction?api-version=2015-03-20 $webhookAction

Use el método Put con un identificador de acción existente para modificar una acción de webhook para una programación.  El cuerpo de la solicitud debe incluir el valor etag de la acción.

    $webhookAction = "{'etag': 'W/\"datetime'2016-02-26T20%3A25%3A00.6862124Z'\"','properties': {'Type': 'Webhook', 'Name': 'My Webhook", 'WebhookUri': 'https://oaaswebhookdf.cloudapp.net/webhooks?token=VrkYTKlhk%2fc%2bKBP', 'CustomPayload': '{\"field1\":\"value1\",\"field2\":\"value2\"}', 'Version': 1 }"
    armclient put /subscriptions/{Subscription ID}/resourceGroups/OI-Default-East-US/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/{Schedule ID}/actions/mywebhookaction?api-version=2015-03-20 $webhookAction


## <a name="next-steps"></a>Pasos siguientes
* Use la [API de búsqueda de registros de Log Analytics](log-analytics-log-search-api.md) en Log Analytics.
* Más información sobre las [alertas de registro en Alertas de Azure](../monitoring-and-diagnostics/monitor-alerts-unified-log.md).

