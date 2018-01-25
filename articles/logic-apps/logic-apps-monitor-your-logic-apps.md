---
title: Comprobar el estado, configurar el registro y recibir alertas - Azure Logic Apps | Microsoft Docs
description: "Supervise el estado y el rendimiento de aplicaciones lógicas, registre datos de diagnóstico y configure alertas"
author: jeffhollan
manager: anneta
editor: 
services: logic-apps
documentationcenter: 
ms.assetid: 5c1b1e15-3b6c-49dc-98a6-bdbe7cb75339
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.custom: H1Hack27Feb2017
ms.date: 07/21/2017
ms.author: LADocs; jehollan
ms.openlocfilehash: 8ad8281744293a05b50f8664a7e5a3fea7aa7b33
ms.sourcegitcommit: be9a42d7b321304d9a33786ed8e2b9b972a5977e
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/19/2018
---
# <a name="monitor-status-set-up-diagnostics-logging-and-turn-on-alerts-for-azure-logic-apps"></a>Supervisar el estado, configurar el registro de diagnósticos y activar alertas para Azure Logic Apps

Después de [crear y ejecutar una aplicación lógica](../logic-apps/quickstart-create-first-logic-app-workflow.md), puede comprobar su historial de ejecuciones, historial de desencadenadores, estado y rendimiento. Para la supervisión de eventos en tiempo real y una depuración más rica, configure el [registro de diagnósticos](#azure-diagnostics) de la aplicación lógica. De este modo, puede [buscar y ver eventos](#find-events), como eventos de desencadenador, eventos de ejecución y eventos de acción. También puede usar estos [datos de diagnóstico con otros servicios](#extend-diagnostic-data), como Azure Storage y Azure Event Hubs. 

Para recibir notificaciones sobre errores u otros posibles problemas, configure [alertas](#add-azure-alerts). Por ejemplo, puede crear una alerta que detecte "cuando se produzcan errores en más de cinco ejecuciones en una hora". También puede configurar la supervisión, el seguimiento y el registro mediante programación con la [configuración de eventos y las propiedades de Azure Diagnostics](#diagnostic-event-properties).

## <a name="view-runs-and-trigger-history-for-your-logic-app"></a>Visualización del historial de ejecuciones y desencadenadores de la aplicación lógica

1. Para buscar la aplicación lógica en [Azure Portal](https://portal.azure.com), en el menú principal de Azure, elija **Más servicios**. En el cuadro de búsqueda, busque "aplicaciones lógicas" y elija **Aplicaciones lógicas**.

   ![Búsqueda de la aplicación lógica](./media/logic-apps-monitor-your-logic-apps/find-your-logic-app.png)

   Azure Portal muestra todas las aplicaciones lógicas asociadas a la suscripción de Azure. 

2. Seleccione la aplicación lógica y luego elija **Información general**.

   Azure Portal muestra el historial de ejecuciones y desencadenadores de la aplicación lógica. Por ejemplo: 

   ![Historial de ejecuciones e historial de desencadenadores de la aplicación lógica](media/logic-apps-monitor-your-logic-apps/overview.png)

   * **Historial de ejecuciones** muestra todas las ejecuciones de la aplicación lógica. 
   * **Historial de desencadenadores** muestra toda la actividad de los desencadenadores de la aplicación lógica.

   Para obtener descripciones de estado, vea [Diagnóstico de errores en las aplicaciones lógicas](../logic-apps/logic-apps-diagnosing-failures.md).

   > [!TIP]
   > Si no encuentra los datos que espera, en la barra de herramientas, elija **Actualizar**.

3. Para ver los pasos de una ejecución concreta, en **Historial de ejecuciones**, seleccione esa ejecución. 

   La vista de supervisión muestra cada paso de esa ejecución. Por ejemplo: 

   ![Acciones de una ejecución concreta](media/logic-apps-monitor-your-logic-apps/monitor-view-updated.png)

4. Para obtener más detalles sobre la ejecución, elija **Detalles de ejecución**. Esta información resume los pasos, el estado, las entradas y las salidas de la ejecución. 

   ![Selección de "Detalles de ejecución"](media/logic-apps-monitor-your-logic-apps/run-details.png)

   Por ejemplo, puede obtener el **Id. de correlación** de la ejecución, que podría necesitar al usar la [API de REST para aplicaciones lógicas](https://docs.microsoft.com/rest/api/logic).

5. Para obtener detalles sobre un paso concreto, elija ese paso. Ahora puede revisar detalles como las entradas, las salidas y los errores acontecidos en ese paso. Por ejemplo: 

   ![Detalles del paso](media/logic-apps-monitor-your-logic-apps/monitor-view-details.png)
   
   > [!NOTE]
   > Todos los eventos y los detalles de runtime se cifran en el servicio Logic Apps. Solo se descifran cuando un usuario solicita ver esos datos. Puede controlar el acceso a estos eventos con el [control de acceso basado en roles (RBAC) de Azure](../active-directory/role-based-access-control-what-is.md).

6. Para obtener detalles sobre un evento de desencadenador concreto, vuelva al panel **Información general**. En **Historial de desencadenadores**, seleccione el evento de desencadenador. Ahora puede revisar detalles como las entradas y salidas, por ejemplo:

   ![Detalles de salida de evento de desencadenador](media/logic-apps-monitor-your-logic-apps/trigger-details.png)

<a name="azure-diagnostics"></a>

## <a name="turn-on-diagnostics-logging-for-your-logic-app"></a>Activación del registro de diagnósticos de la aplicación lógica

Para una depuración más rica con detalles y eventos de runtime, puede configurar el registro de diagnósticos con [Azure Log Analytics](../log-analytics/log-analytics-overview.md). Log Analytics es un servicio de [Operations Management Suite (OMS)](../operations-management-suite/operations-management-suite-overview.md) que supervisa los entornos local y de nube para ayudar a mantener su disponibilidad y rendimiento. 

Antes de empezar, necesita un área de trabajo de OMS. Aprenda [cómo crear un área de trabajo de OMS](../log-analytics/log-analytics-get-started.md).

1. En [Azure Portal](https://portal.azure.com), busque y seleccione la aplicación lógica. 

2. En el menú de la hoja de la aplicación lógica, en **Supervisión**, elija **Diagnóstico** > **Configuración de diagnóstico**.

   ![Supervisión, Diagnóstico, Configuración de diagnóstico](media/logic-apps-monitor-your-logic-apps/logic-app-diagnostics.png)

3. En **Configuración de diagnóstico**, elija **Activado**.

   ![Activación de los registros de diagnóstico](media/logic-apps-monitor-your-logic-apps/turn-on-diagnostics-logic-app.png)

4. Ahora seleccione el área de trabajo de OMS y la categoría de evento para el registro como se muestra:

   1. Seleccione **Enviar a Log Analytics**. 
   2. En **Log Analytics**, elija **Configurar**. 
   3. En **Áreas de trabajo de OMS**, seleccione el área de trabajo de OMS que va a usar para el registro.
   4. En **Registro**, seleccione la categoría **WorkflowRuntime**.
   5. Elija el intervalo métrico.
   6. Cuando termine, seleccione **Guardar**.

   ![Selección del área de trabajo de OMS y los datos para el registro](media/logic-apps-monitor-your-logic-apps/send-diagnostics-data-log-analytics-workspace.png)

Ahora puede buscar eventos y otros datos de los eventos de desencadenador, los eventos de ejecución y los eventos de acción.

<a name="find-events"></a>

## <a name="find-events-and-data-for-your-logic-app"></a>Búsqueda de eventos y datos de la aplicación lógica

Para buscar y ver eventos de la aplicación lógica, como eventos de desencadenador, eventos de ejecución y eventos de acción, siga estos pasos.

1. En el [Azure Portal](https://portal.azure.com), elija **Más servicios**. Busque "log analytics" y luego elija **Log Analytics** como se muestra aquí:

   ![Selección de "Log Analytics"](media/logic-apps-monitor-your-logic-apps/browseloganalytics.png)

2. En **Log Analytics**, busque y seleccione el área de trabajo de OMS. 

   ![Selección del área de trabajo de OMS](media/logic-apps-monitor-your-logic-apps/selectla.png)

3. En **Administración**, elija **Portal de OMS**.

   ![Selección de "Portal de OMS"](media/logic-apps-monitor-your-logic-apps/omsportalpage.png)

4. En la página principal de OMS, seleccione **Búsqueda de registros**.

   ![Selección de "Búsqueda de registros" en la página principal de OMS](media/logic-apps-monitor-your-logic-apps/logsearch.png)

   O bien

   ![Selección de "Búsqueda de registros" en el menú OMS](media/logic-apps-monitor-your-logic-apps/logsearch-2.png)

5. En el cuadro de búsqueda, especifique un campo que quiera buscar y pulse **Entrar**. Cuando empiece a escribir, OMS le mostrará posibles coincidencias y operaciones que puede usar. 

   Por ejemplo, para buscar los diez principales eventos que se han producido, escriba y seleccione esta consulta de búsqueda: **Category=WorkflowRuntime |top 10**

   ![Especificación de cadena de búsqueda](media/logic-apps-monitor-your-logic-apps/oms-start-query.png)

   Más información sobre [cómo buscar datos en Log Analytics](../log-analytics/log-analytics-log-searches.md).

6. En la página de resultados, en la barra de la izquierda, elija el marco temporal que quiere ver.
Para refinar la consulta con un filtro, elija **+Agregar**.

   ![Selección del marco temporal de los resultados de la consulta](media/logic-apps-monitor-your-logic-apps/query-results.png)

7. En **Agregar filtros**, escriba el nombre del filtro para poder encontrar el que quiere. Seleccione el filtro y elija **+Agregar**.

   En este ejemplo se usa la palabra "status" para buscar eventos con errores en **AzureDiagnostics**.
   El filtro de **status_s** ya está seleccionado.

   ![Selección de filtro](media/logic-apps-monitor-your-logic-apps/log-search-add-filter.png)

8. En la barra de la izquierda, seleccione el valor de filtro que quiere usar y elija **Aplicar**.

   ![Selección del valor de filtro y de "Aplicar"](media/logic-apps-monitor-your-logic-apps/log-search-apply-filter.png)

9. Ahora vuelva a la consulta que está creando. La consulta se ha actualizado con el filtro y el valor seleccionados. Los resultados anteriores también se han filtrado.

   ![Consulta con los resultados filtrados](media/logic-apps-monitor-your-logic-apps/log-search-query-filtered-results.png)

10. Para guardar la consulta para su uso futuro, elija **Guardar**. Aprenda [cómo guardar la consulta](../logic-apps/logic-apps-track-b2b-messages-omsportal-query-filter-control-number.md#save-oms-query).

<a name="extend-diagnostic-data"></a>

## <a name="extend-how-and-where-you-use-diagnostic-data-with-other-services"></a>Uso de los datos de diagnóstico con otros servicios

Además de con Azure Log Analytics, puede usar los datos de diagnóstico de la aplicación lógica con otros servicios de Azure, por ejemplo: 

* [Archivar registros de Diagnósticos de Azure en Azure Storage](../monitoring-and-diagnostics/monitoring-archive-diagnostic-logs.md)
* [Transmitir registros de Diagnósticos de Azure a Azure Event Hubs](../monitoring-and-diagnostics/monitoring-stream-diagnostic-logs-to-event-hubs.md) 

Luego puede obtener supervisión en tiempo real mediante la telemetría y los análisis de otros servicios, como [Azure Stream Analytics](../stream-analytics/stream-analytics-introduction.md) y [Power BI](../log-analytics/log-analytics-powerbi.md). Por ejemplo: 

* [Transmitir datos de Event Hubs a Stream Analytics](../stream-analytics/stream-analytics-define-inputs.md)
* [Analizar datos que se están transmitiendo con Stream Analytics y crear un panel de análisis en tiempo real en Power BI](../stream-analytics/stream-analytics-power-bi-dashboard.md)

Según las opciones que quiera configurar, primero asegúrese de [crear una cuenta de Azure Storage](../storage/common/storage-create-storage-account.md) o [crear un centro de eventos de Azure](../event-hubs/event-hubs-create.md). Luego seleccione las opciones para el envío de los datos de diagnóstico:

![Envío de los datos a una cuenta de Azure Storage o a un centro de eventos](./media/logic-apps-monitor-your-logic-apps/storage-account-event-hubs.png)

> [!NOTE]
> Los períodos de retención solo se aplican cuando se usa una cuenta de almacenamiento.

<a name="add-azure-alerts"></a>

## <a name="set-up-alerts-for-your-logic-app"></a>Configuración de alertas de la aplicación lógica

Para supervisar métricas concretas o umbrales superados de la aplicación lógica, configure [alertas de Azure](../monitoring-and-diagnostics/monitoring-overview-alerts.md). Más información sobre [métricas de Azure](../monitoring-and-diagnostics/monitoring-overview-metrics.md). 

Para configurar alertas sin [Azure Log Analytics](../log-analytics/log-analytics-overview.md), siga estos pasos. Para usar criterios y acciones de alerta más avanzados, [configure Log Analytics](#azure-diagnostics) también.

1. En el menú de la hoja de la aplicación lógica, en **Supervisión**, elija **Diagnóstico** > **Reglas de alerta** > **Agregar alerta** como se muestra aquí:

   ![Adición de una alerta de la aplicación lógica](media/logic-apps-monitor-your-logic-apps/set-up-alerts.png)

2. En la hoja **Agregar una regla de alerta**, cree la alerta como se muestra:

   1. En **Recurso**, seleccione la aplicación lógica si aún no está seleccionada. 
   2. Proporcione un nombre y una descripción para la alerta.
   3. Seleccione una **Métrica** o evento cuyo seguimiento quiera realizar.
   4. Seleccione una **Condición**, especifique un **Umbral** para la métrica y seleccione el **Periodo** para la supervisión de esta métrica.
   5. Seleccione si se va a enviar correo para la alerta. 
   6. Especifique cualquier otra dirección de correo electrónico para enviar la alerta. 
   También puede especificar la dirección URL de un webhook al que quiera enviar la alerta.

   Por ejemplo, esta regla envía una alerta cuando hay errores en cinco o más ejecuciones en una hora:

   ![Creación de regla de alerta de métrica](media/logic-apps-monitor-your-logic-apps/create-alert-rule.png)

> [!TIP]
> Para ejecutar una aplicación lógica desde una alerta, puede incluir el [desencadenador de solicitud](../connectors/connectors-native-reqres.md) en el flujo de trabajo, lo que permite realizar tareas como estos ejemplos:
> 
> * [Publicar en Slack](https://github.com/Azure/azure-quickstart-templates/tree/master/201-alert-to-slack-with-logic-app)
> * [Enviar un texto](https://github.com/Azure/azure-quickstart-templates/tree/master/201-alert-to-text-message-with-logic-app)
> * [Agregar un mensaje a una cola](https://github.com/Azure/azure-quickstart-templates/tree/master/201-alert-to-queue-with-logic-app)

<a name="diagnostic-event-properties"></a>

## <a name="azure-diagnostics-event-settings-and-details"></a>Configuración de eventos y detalles de Azure Diagnostics

Cada evento de diagnóstico incluye detalles sobre la aplicación lógica y ese evento, por ejemplo, el estado, la hora de inicio, la hora de finalización, etc. Para configurar mediante programación la supervisión, el seguimiento y el registro, puede usar estos detalles con la [API de REST para Azure Logic Apps](https://docs.microsoft.com/rest/api/logic) y la [API de REST para Azure Diagnostics](../monitoring-and-diagnostics/monitoring-supported-metrics.md#microsoftlogicworkflows).

Por ejemplo, el evento `ActionCompleted` tiene las propiedades `clientTrackingId` y `trackedProperties` que puede usar para el seguimiento y la supervisión:

``` json
{
    "time": "2016-07-09T17:09:54.4773148Z",
    "workflowId": "/SUBSCRIPTIONS/<subscription-ID>/RESOURCEGROUPS/MYRESOURCEGROUP/PROVIDERS/MICROSOFT.LOGIC/WORKFLOWS/MYLOGICAPP",
    "resourceId": "/SUBSCRIPTIONS/<subscription-ID>/RESOURCEGROUPS/MYRESOURCEGROUP/PROVIDERS/MICROSOFT.LOGIC/WORKFLOWS/MYLOGICAPP/RUNS/08587361146922712057/ACTIONS/HTTP",
    "category": "WorkflowRuntime",
    "level": "Information",
    "operationName": "Microsoft.Logic/workflows/workflowActionCompleted",
    "properties": {
        "$schema": "2016-06-01",
        "startTime": "2016-07-09T17:09:53.4336305Z",
        "endTime": "2016-07-09T17:09:53.5430281Z",
        "status": "Succeeded",
        "code": "OK",
        "resource": {
            "subscriptionId": "<subscription-ID>",
            "resourceGroupName": "MyResourceGroup",
            "workflowId": "cff00d5458f944d5a766f2f9ad142553",
            "workflowName": "MyLogicApp",
            "runId": "08587361146922712057",
            "location": "westus",
            "actionName": "Http"
        },
        "correlation": {
            "actionTrackingId": "e1931543-906d-4d1d-baed-dee72ddf1047",
            "clientTrackingId": "<my-custom-tracking-ID>"
        },
        "trackedProperties": {
            "myTrackedProperty": "<value>"
        }
    }
}
```

* `clientTrackingId`: si no se ha proporcionado, Azure genera automáticamente este identificador y correlaciona eventos en una ejecución de aplicación lógica, incluidos los flujos de trabajo anidados que se llamen desde la aplicación lógica. Puede especificar manualmente este identificador desde un desencadenador si pasa un encabezado `x-ms-client-tracking-id` con el valor de identificador personalizado en la solicitud de desencadenador. Puede usar un desencadenador de solicitud, un desencadenador HTTP o un desencadenador de webhook.

* `trackedProperties`: para realizar el seguimiento de las entradas o salidas de los datos de diagnóstico se pueden agregar propiedades controladas a acciones en la definición de JSON de la aplicación lógica. Las propiedades controladas solo pueden realizar el seguimiento de entradas y salidas de acciones individuales, aunque puede usar las propiedades `correlation` de los eventos para crear correlaciones entre las acciones de una ejecución.

  Para realizar el seguimiento de una o más propiedades, agregue la sección `trackedProperties` y las propiedades que quiera a la definición de la acción. Por ejemplo, imagine que quiere realizar un seguimiento de datos como un "id. de pedido" en los datos de telemetría:

  ``` json
  "myAction": {
    "type": "http",
    "inputs": {
        "uri": "http://uri",
        "headers": {
            "Content-Type": "application/json"
        },
        "body": "@triggerBody()"
    },
    "trackedProperties": {
        "myActionHTTPStatusCode": "@action()['outputs']['statusCode']",
        "myActionHTTPValue": "@action()['outputs']['body']['<content>']",
        "transactionId": "@action()['inputs']['body']['<content>']"
    }
  }
  ```

## <a name="next-steps"></a>pasos siguientes

* [Creación de plantillas para administrar la implementación y liberación de aplicaciones lógicas](../logic-apps/logic-apps-create-deploy-template.md)
* [Escenarios B2B y comunicación con Enterprise Integration Pack](../logic-apps/logic-apps-enterprise-integration-overview.md)
* [Supervisión de mensajes B2B](../logic-apps/logic-apps-monitor-b2b-message.md)