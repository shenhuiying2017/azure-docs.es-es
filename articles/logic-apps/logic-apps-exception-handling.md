---
title: Control de errores y excepciones de Logic Apps en Azure | Microsoft Docs
description: Patrones para el control de errores y excepciones en Logic Apps.
services: logic-apps
documentationcenter: 
author: dereklee
manager: anneta
editor: 
ms.assetid: e50ab2f2-1fdc-4d2a-be40-995a6cc5a0d4
ms.service: logic-apps
ms.devlang: 
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: logic-apps
ms.date: 01/31/2018
ms.author: deli; LADocs
ms.openlocfilehash: 91819d0fba30dd2ada981435fa13b8ae0a7fcc45
ms.sourcegitcommit: 0b02e180f02ca3acbfb2f91ca3e36989df0f2d9c
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 03/05/2018
---
# <a name="handle-errors-and-exceptions-in-logic-apps"></a>Control de errores y excepciones en Logic Apps

Controlar correctamente el tiempo de inactividad o los problemas de sistemas dependientes puede plantear un problema para cualquier arquitectura de integración. A fin de crear integraciones sólidas que resistan a errores y problemas, Logic Apps ofrece una experiencia de primera clase para controlar errores y excepciones. 

## <a name="retry-policies"></a>Directivas de reintentos

Para el control de errores y excepciones más básico, puede utilizar la directiva de reintentos. Si una solicitud inicial ha agotado el tiempo de espera o ha producido error (toda solicitud que tenga como resultado una respuesta 429 o 5xx), esta directiva define si la acción debe reintentar la solicitud y cómo hacerlo. 

Hay cuatro tipos de directivas de reintentos: predeterminada, ninguna, intervalo fijo e intervalo exponencial. Si la definición del flujo de trabajo no tiene una directiva de reintento, se utiliza en su lugar la directiva predeterminada de acuerdo con el servicio.

Para configurar directivas de reintento, si procede, abra el Diseñador de aplicación lógica de su aplicación lógica y vaya a **Configuración** para una acción concreta en la aplicación lógica. O bien, puede definir directivas de reintento en la sección **entradas** para una acción o un desencadenador en particular, si se puede reintentar, de la definición de flujo de trabajo. Esta es la sintaxis general:

```json
"retryPolicy": {
    "type": "<retry-policy-type>",
    "interval": <retry-interval>,
    "count": <number-of-retry-attempts>
}
```

Para obtener más información sobre la sintaxis y la sección **entradas**, consulte [la sección sobre la directiva de reintentos en Acciones y desencadenadores de flujo de trabajo][retryPolicyMSDN]. Si quiere obtener más información acerca de las limitaciones de las directivas de reintentos, consulte [Límites y configuración de Logic Apps](../logic-apps/logic-apps-limits-and-config.md). 

### <a name="default"></a>Valor predeterminado

Si no define una directiva de reintentos en la sección **retryPolicy**, la aplicación lógica utiliza la directiva predeterminada, que es una [directiva de intervalos exponenciales](#exponential-interval) que envía hasta cuatro reintentos en intervalos crecientes exponencialmente que aumentan 7,5 segundos cada vez. El intervalo se demora entre 5 y 45 segundos. Esta directiva es equivalente a la directiva de esta definición de flujo de trabajo HTTP de ejemplo:

```json
"HTTP": {
    "type": "Http",
    "inputs": {
        "method": "GET",
        "uri": "http://myAPIendpoint/api/action",
        "retryPolicy" : {
            "type": "exponential",
            "count": 4,
            "interval": "PT7.5S",
            "minimumInterval": "PT5S",
            "maximumInterval": "PT45S"
        }
    },
    "runAfter": {}
}
```

### <a name="none"></a>None

Si establece **retryPolicy** en **ninguno**, esta directiva no reintenta las solicitudes con error.

| Nombre del elemento | Obligatorio | type | DESCRIPCIÓN | 
| ------------ | -------- | ---- | ----------- | 
| Tipo | Sí | string | **Ninguna** | 
||||| 

### <a name="fixed-interval"></a>Intervalo fijo

Si el valor de **retryPolicy** está establecido en **fijo**, la directiva vuelve a intentar una solicitud con error después de esperar durante el intervalo especificado, antes de enviar la solicitud siguiente.

| Nombre del elemento | Obligatorio | type | DESCRIPCIÓN |
| ------------ | -------- | ---- | ----------- |
| Tipo | Sí | string | **fijo** |
| count | Sí | Entero | El número de reintentos, que debe estar comprendido entre 1 y 90 | 
| interval | Sí | string | El intervalo de reintentos en [formato ISO 8601](https://en.wikipedia.org/wiki/ISO_8601#Combined_date_and_time_representations), que debe estar entre PT5S y PT1D | 
||||| 

<a name="exponential-interval"></a>

### <a name="exponential-interval"></a>Intervalo exponencial

Si el valor de **retryPolicy** está establecido en **exponencial**, la directiva vuelve a intentar una solicitud con error después de un intervalo de tiempo aleatorio a partir de un intervalo exponencialmente creciente. La directiva garantiza también que cada reintento se enviará a un intervalo aleatorio mayor que **minimumInterval** y menor que **maximumInterval**. Las directivas exponenciales requieren valores para **count** e **interval**, mientras que para **minimumInterval** y **maximumInterval** son opcionales. Si desea reemplazar los valores predeterminados de PT5S y PT1D respectivamente, puede agregar estos valores.

| Nombre del elemento | Obligatorio | type | DESCRIPCIÓN |
| ------------ | -------- | ---- | ----------- |
| Tipo | Sí | string | **exponencial** |
| count | Sí | Entero | El número de reintentos, que debe estar comprendido entre 1 y 90  |
| interval | Sí | string | El intervalo de reintentos en [formato ISO 8601](https://en.wikipedia.org/wiki/ISO_8601#Combined_date_and_time_representations), que debe estar entre PT5S y PT1D. |
| minimumInterval | Sin  | string | El intervalo de reintentos mínimo en [formato ISO 8601](https://en.wikipedia.org/wiki/ISO_8601#Combined_date_and_time_representations), que debe estar entre PT5S y el valor de **interval** |
| maximumInterval | Sin  | string | El intervalo de reintentos mínimo en [formato ISO 8601](https://en.wikipedia.org/wiki/ISO_8601#Combined_date_and_time_representations), que debe estar entre el valor de **interval** y PT1D | 
||||| 

Esta tabla muestra cómo se genera una variable aleatoria uniforme dentro del rango indicado para cada reintento, cuyo límite se establece según el valor de **count** (este inclusive):

**Rango de variable aleatoria**

| Número de reintentos | Intervalo mínimo | Intervalo máximo |
| ------------ | ---------------- | ---------------- |
| 1 | Max(0, **minimumInterval**) | Min(interval, **maximumInterval**) |
| 2 | Max(interval, **minimumInterval**) | Min(2 * interval, **maximumInterval**) |
| 3 | Max(2 * interval, **minimumInterval**) | Min(4 * interval, **maximumInterval**) |
| 4 | Max(4 * interval, **minimumInterval**) | Min(8 * interval, **maximumInterval**) |
| .... | | | 
|||| 

## <a name="catch-and-handle-failures-with-the-runafter-property"></a>Detección y control de errores con la propiedad RunAfter

Cada acción de aplicación lógica declara las acciones que deben finalizar antes de que dicha acción se inicie, de manera similar a la especificación del orden de los pasos en el flujo de trabajo. En la definición de acción, la propiedad **runAfter** define este orden y es un objeto que describe qué acciones y estados de acción ejecutan la acción.

De forma predeterminada, todas las acciones que se agregan en el Diseñador de aplicación lógica están configuradas para que se ejecuten después del paso anterior, si es el resultado del paso anterior es **Succeeded**. Sin embargo, puede personalizar el valor **runAfter** para que las acciones se activen cuando las acciones anteriores den como resultado **Failed**, **Skipped** o alguna combinación de estos valores. Por ejemplo, para agregar un elemento a un tema de Service Bus específico después de que se produzca un error en una determinada acción **Insert_Row**, podría utilizar esta definición **runAfter** de ejemplo:

```json
"Send_message": {
    "inputs": {
        "body": {
            "ContentData": "@{encodeBase64(body('Insert_Row'))}",
            "ContentType": "{ \"content-type\" : \"application/json\" }"
        },
        "host": {
            "api": {
                "runtimeUrl": "https://logic-apis-westus.azure-apim.net/apim/servicebus"
            },
            "connection": {
                "name": "@parameters('$connections')['servicebus']['connectionId']"
            }
        },
        "method": "post",
        "path": "/@{encodeURIComponent('failures')}/messages"
    },
    "runAfter": {
        "Insert_Row": [
            "Failed"
        ]
    }
}
```

La propiedad **runAfter** está configurada para ejecutarse cuando el estado de la acción **Insert_Row** es **Failed**. Para ejecutar la acción si el estado de la acción es **Correcta**, **Errónea** u **Omitida**, use esta sintaxis:

```json
"runAfter": {
        "Insert_Row": [
            "Failed", "Succeeded", "Skipped"
        ]
    }
```

> [!TIP]
> Las acciones que se ejecutan y completan correctamente después de que una acción anterior haya producido un error presentan la marca **Succeeded**. Este comportamiento significa que, si detecta correctamente todos los errores de un flujo de trabajo, la propia ejecución se marca como **Succeeded**.

<a name="scopes"></a>

## <a name="evaluate-actions-with-scopes-and-their-results"></a>Evaluación de las acciones con ámbitos y sus resultados

De forma similar a la ejecución de pasos tras acciones individuales con la propiedad **runAfter**, puede agrupar acciones dentro de un [ámbito](../logic-apps/logic-apps-control-flow-run-steps-group-scopes.md). Puede usar ámbitos si desea agrupar acciones lógicamente, evaluar el estado global del ámbito y realizar acciones basadas en ese estado. Una vez que todas las acciones de un ámbito acaben de ejecutarse, el propio ámbito obtiene su estado. 

Para comprobar el estado del ámbito, puede usar los mismos criterios que usa para comprobar el estado de ejecución de la aplicación lógica, como **Succeeded**, **Failed**, etc. 

De forma predeterminada, cuando las acciones del ámbito se ejecutan correctamente, el estado del ámbito se marca como **Succeeded**. Si la acción final de un ámbito da como resultado **Failed** o **Aborted**, el estado del ámbito se marca como **Failed**. 

Para detectar las excepciones en un ámbito **Failed** y ejecutar acciones que controlen estos errores, puede usar la propiedad **runAfter** para ese ámbito **Failed**. De ese modo, si *cualquier* acción del ámbito produce un error, y usa la propiedad **runAfter** para ese ámbito, puede crear una única acción para detectar errores.

Para conocer los límites en los ámbitos, consulte [Límites y configuración](../logic-apps/logic-apps-limits-and-config.md).

### <a name="get-context-and-results-for-failures"></a>Obtención del contexto y resultados de errores

Aunque la detección de errores desde un ámbito es útil, quizás también necesite el contexto como ayuda para comprender exactamente qué acciones han producido un error y los errores o códigos de estado que se hayan devuelto. La función del flujo de trabajo **@result()** proporciona contexto sobre el resultado de todas las acciones en un ámbito.

La función **@result()** acepta un único parámetro (el nombre del ámbito) y devuelve una matriz de todos los resultados de acción desde dentro de ese ámbito. Estos objetos de acción incluyen los mismos atributos que el objeto  **@actions()**, como la hora de inicio, la hora de finalización, el estado, las entradas, los identificadores de correlación y las salidas de la acción. Para enviar el contexto de las acciones que produjeron un error dentro de un ámbito, puede emparejar fácilmente una función **@result()** con una propiedad **runAfter**.

Para ejecutar una acción *para cada una* de las acciones de un ámbito que tenga el estado **Failed** y filtrar la matriz de resultados para las acciones que produjeron un error; puede emparejar **@result()** con una acción **[Filter Array](../connectors/connectors-native-query.md)** y un bucle **[ForEach](../logic-apps/logic-apps-control-flow-loops.md)**. Puede tomar la matriz de resultados filtrada y realizar una acción para cada error mediante el bucle **ForEach** . 

En este ejemplo, seguido de una explicación detallada, se envía una solicitud HTTP POST con el cuerpo de respuesta de todas las acciones que produjeron un error dentro del ámbito "My_Scope":

```json
"Filter_array": {
    "inputs": {
        "from": "@result('My_Scope')",
        "where": "@equals(item()['status'], 'Failed')"
    },
    "runAfter": {
        "My_Scope": [
            "Failed"
        ]
    },
    "type": "Query"
},
"For_each": {
    "actions": {
        "Log_Exception": {
            "inputs": {
                "body": "@item()['outputs']['body']",
                "method": "POST",
                "headers": {
                    "x-failed-action-name": "@item()['name']",
                    "x-failed-tracking-id": "@item()['clientTrackingId']"
                },
                "uri": "http://requestb.in/"
            },
            "runAfter": {},
            "type": "Http"
        }
    },
    "foreach": "@body('Filter_array')",
    "runAfter": {
        "Filter_array": [
            "Succeeded"
        ]
    },
    "type": "Foreach"
}
```

Este es un tutorial detallado que describe lo que sucede en este ejemplo:

1. Para obtener el resultado de todas las acciones dentro de My_Scope, la acción **Filter_array** filtra **@result('My_Scope')**.

2. La condición de **Filter Array** es cualquier elemento **@result()** que tenga el estado **Failed**. Esta condición filtra la matriz de todos los resultados de la acción de "My_Scope" a una matriz con solo los resultados de acción que hayan producido un error.

3. Realice de una acción **For Each** en las salidas de las *matrices filtradas*. En este paso se realiza una acción *para cada* acción resultante en error que se filtrara antes.

   Si una sola acción del ámbito produjo un error, las acciones en el elemento **foreach** solo se ejecutan una vez. 
   Muchas acciones con error causan una acción por cada error.

4. Envíe una solicitud HTTP POST en el cuerpo de respuesta del elemento **foreach**, que es **@item()['outputs']['body']**. La forma del elemento **@result()** es la misma que la forma de **@actions()**, y se puede analizar del mismo modo.

5. Incluya dos encabezados personalizados con el nombre de la acción con errores **@item()['name']** y el id. de seguimiento de cliente de la ejecución con errores **@item()['clientTrackingId']**.

Como referencia, este es un ejemplo de un solo elemento **@result()**, que muestra las propiedades **name**, **body** y **clientTrackingId** que se analizan en el ejemplo anterior. Fuera de una acción **foreach**, **@result()** devuelve una matriz de estos objetos.

```json
{
    "name": "Example_Action_That_Failed",
    "inputs": {
        "uri": "https://myfailedaction.azurewebsites.net",
        "method": "POST"
    },
    "outputs": {
        "statusCode": 404,
        "headers": {
            "Date": "Thu, 11 Aug 2016 03:18:18 GMT",
            "Server": "Microsoft-IIS/8.0",
            "X-Powered-By": "ASP.NET",
            "Content-Length": "68",
            "Content-Type": "application/json"
        },
        "body": {
            "code": "ResourceNotFound",
            "message": "/docs/folder-name/resource-name does not exist"
        }
    },
    "startTime": "2016-08-11T03:18:19.7755341Z",
    "endTime": "2016-08-11T03:18:20.2598835Z",
    "trackingId": "bdd82e28-ba2c-4160-a700-e3a8f1a38e22",
    "clientTrackingId": "08587307213861835591296330354",
    "code": "NotFound",
    "status": "Failed"
}
```

Para poner en práctica diferentes patrones de control de excepciones, puede usar las expresiones descritas anteriormente en este artículo. Puede optar por ejecutar una única acción de control de excepciones fuera del ámbito, que acepte toda la matriz filtrada de errores y quite la acción **foreach**. También puede incluir otras propiedades útiles de la respuesta **@result()**, tal como se mostró anteriormente.

## <a name="azure-diagnostics-and-telemetry"></a>Diagnósticos de Azure y datos de telemetría

Los patrones anteriores son una manera excelente de controlar errores y excepciones dentro de una ejecución, pero también puede identificar y responder a los errores con independencia de la ejecución en sí. 
[Azure Diagnostics](../logic-apps/logic-apps-monitor-your-logic-apps.md) ofrece un método sencillo de enviar todos los eventos del flujo de trabajo (incluidos todos los estados de ejecución y acción) a una cuenta de Azure Storage o a un centro de eventos creado con Azure Event Hubs. 

Para evaluar los estados de ejecución, puede supervisar los registros y las métricas, o publicarlos en la herramienta de supervisión que prefiera. Una posible opción es transmitir todos los eventos mediante Event Hubs a [Azure Stream Analytics](https://azure.microsoft.com/services/stream-analytics/). En Stream Analytics, puede escribir consultas en directo de anomalías, promedios o errores de los registros de diagnóstico. Asimismo, puede usar Stream Analytics para enviar información a otros orígenes de datos, como colas, temas, SQL, Azure Cosmos DB o Power BI.

## <a name="next-steps"></a>Pasos siguientes

* [Vea cómo un cliente crea el control de errores con Azure Logic Apps](../logic-apps/logic-apps-scenario-error-and-exception-handling.md)
* [Encuentre más escenarios y ejemplos de Logic Apps](../logic-apps/logic-apps-examples-and-scenarios.md)
* [Aprenda a crear implementaciones automatizadas para aplicaciones lógicas](../logic-apps/logic-apps-create-deploy-template.md)
* [Creación e implementación de aplicaciones lógicas con Visual Studio](logic-apps-deploy-from-vs.md)

<!-- References -->
[retryPolicyMSDN]: https://docs.microsoft.com/rest/api/logic/actions-and-triggers#Anchor_9