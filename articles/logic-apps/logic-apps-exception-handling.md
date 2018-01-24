---
title: Control de errores y excepciones de Logic Apps en Azure | Microsoft Docs
description: Patrones para el control de errores y excepciones en Logic Apps.
services: logic-apps
documentationcenter: .net,nodejs,java
author: derek1ee
manager: anneta
editor: 
ms.assetid: e50ab2f2-1fdc-4d2a-be40-995a6cc5a0d4
ms.service: logic-apps
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: integration
ms.date: 10/18/2016
ms.author: LADocs; deli
ms.openlocfilehash: a74c7d18306359c9152f139299de1208b5932fe5
ms.sourcegitcommit: f46cbcff710f590aebe437c6dd459452ddf0af09
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/20/2017
---
# <a name="handle-errors-and-exceptions-in-logic-apps"></a>Control de errores y excepciones en Logic Apps

Logic Apps de Azure le proporciona herramientas y patrones completos para garantizar la solidez de sus integraciones y su resistencia frente a los errores. Cualquier arquitectura de integración presenta el reto de controlar correctamente el tiempo de inactividad o los problemas con sistemas dependientes. Gracias a Logic Apps, el control de errores se convierte en una excelente experiencia. Le proporciona las herramientas necesarias para tomar las medidas oportunas ante excepciones y errores de los flujos de trabajo.

## <a name="retry-policies"></a>Directivas de reintentos

Una directiva de reintentos es el tipo más básico de control de errores y excepciones. Si una solicitud inicial agota el tiempo de espera o produce un error (esto es, toda solicitud que tenga como resultado una respuesta 429 o 5xx), esta directiva define si se debe reintentar la acción y cómo hacerlo. 

Hay cuatro tipos de directivas de reintentos: predeterminada, ninguna, intervalo fijo e intervalo exponencial. Si no se proporciona ninguna directiva de reintentos en la definición del flujo de trabajo, se utiliza la directiva predeterminada de acuerdo con el servicio. 

Puede configurar directivas de reintentos en las *entradas* para una determinada acción o desencadenador, si se puede volver a reintentar. De forma similar, puede configurar directivas de reintentos (si procede) en Diseñador de aplicación lógica. Para configurar una directiva de reintentos, en Diseñador de aplicación lógica, vaya a **Configuración** para elegir una acción específica.

Si quiere obtener más información acerca de las limitaciones de las directivas de reintentos, consulte [Límites y configuración de Logic Apps](../logic-apps/logic-apps-limits-and-config.md). Para obtener más información sobre la sintaxis admitida, consulte la [sección sobre la directiva de reintentos en Acciones y desencadenadores de flujos de trabajo][retryPolicyMSDN].

### <a name="default"></a>Valor predeterminado

Si no define una directiva de reintentos (si el valor **retryPolicy** no está definido), se utiliza la directiva predeterminada. La directiva predeterminada es una directiva de intervalo exponencial que envía hasta cuatro reintentos, en intervalos que aumentan exponencialmente cada 7,5 segundos. El intervalo se demora entre 5 y 45 segundos. Esta directiva predeterminada es equivalente a la directiva de esta definición de flujo de trabajo HTTP de ejemplo:

```json
"HTTP":
{
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
    "runAfter": {},
    "type": "Http"
}
```

### <a name="none"></a>None

Si el valor de **retryPolicy** está establecido en **ninguno**, no se vuelve a intentar una solicitud que sea errónea.

| Nombre del elemento | Obligatorio | type | DESCRIPCIÓN |
| ------------ | -------- | ---- | ----------- |
| Tipo | Sí | string | **Ninguna** |

### <a name="fixed-interval"></a>Intervalo fijo

Si el valor de **retryPolicy** está establecido en **fijo**, la directiva vuelve a intentar una solicitud con error después de esperar durante el intervalo especificado, antes de enviar la solicitud siguiente.

| Nombre del elemento | Obligatorio | type | DESCRIPCIÓN |
| ------------ | -------- | ---- | ----------- |
| Tipo | Sí | string | **fijo** |
| count | Sí | Entero | Número de reintentos. Debe estar entre 1 y 90. |
| interval | Sí | string | Intervalo de reintentos en [formato ISO 8601](https://en.wikipedia.org/wiki/ISO_8601#Combined_date_and_time_representations). Debe estar entre PT5S y PT1D. |

### <a name="exponential-interval"></a>Intervalo exponencial

Si el valor de **retryPolicy** está establecido en **exponencial**, la directiva vuelve a intentar una solicitud con error después de un intervalo de tiempo aleatorio a partir de un intervalo exponencialmente creciente. Se garantiza que cada reintento se enviará a un intervalo aleatorio mayor que **minimumInterval** y menor que **maximumInterval**. Se genera entonces una variable aleatoria dentro del rango indicado en la siguiente tabla para cada reintento, cuyo límite se establece según el valor de **count** (este inclusive):

**Rango de variable aleatoria**

| Número de reintentos | Intervalo mínimo | Intervalo máximo |
| ------------ |  ------------ |  ------------ |
| 1 | Max(0, **minimumInterval**) | Min(interval, **maximumInterval**) |
| 2 | Max(interval, **minimumInterval**) | Min(2 * interval, **maximumInterval**) |
| 3 | Max(2 * interval, **minimumInterval**) | Min(4 * interval, **maximumInterval**) |
| 4 | Max(4 * interval, **minimumInterval**) | Min(8 * interval, **maximumInterval**) |
| ... |

Para las directivas de tipo exponencial, los valores de **count** e **interval** son necesarios. Los valores de **minimumInterval** y **maximumInterval** son opcionales. Puede agregarlos para invalidar los valores predeterminados de PT5S y PT1D respectivamente.

| Nombre del elemento | Obligatorio | type | DESCRIPCIÓN |
| ------------ | -------- | ---- | ----------- |
| Tipo | Sí | string | **exponencial** |
| count | Sí | Entero | Número de reintentos. Debe estar entre 1 y 90.  |
| interval | Sí | string | Intervalo de reintentos en [formato ISO 8601](https://en.wikipedia.org/wiki/ISO_8601#Combined_date_and_time_representations). Debe estar entre PT5S y PT1D. |
| minimumInterval | Sin  | string | Intervalo mínimo de reintentos en [formato ISO 8601](https://en.wikipedia.org/wiki/ISO_8601#Combined_date_and_time_representations). Debe estar entre PT5S y el valor de **interval**. |
| maximumInterval | Sin  | string | Intervalo mínimo de reintentos en [formato ISO 8601](https://en.wikipedia.org/wiki/ISO_8601#Combined_date_and_time_representations). Debe estar comprendido entre el valor de **interval** y PT1D. |

## <a name="catch-failures-with-the-runafter-property"></a>Detección de errores con la propiedad RunAfter

Cada acción de aplicación lógica declara qué acciones deben finalizar antes de que se inicie la acción. Esto es algo parecido a ordenar los pasos del flujo de trabajo. En la definición de la acción, este tipo de ordenación se conoce como la propiedad **runAfter**. 

La propiedad **runAfter** es un objeto que describe qué acciones y estados de acción ejecutan la acción. De forma predeterminada, todas las acciones que agregó mediante Diseñador de aplicación lógica están configuradas para que se ejecuten después del paso anterior, si es el resultado del paso anterior es **Correcta**. 

Sin embargo, puede personalizar el valor de **runAfter** para que active acciones cuando las acciones anteriores devuelvan el resultado **Errónea** u **Omitida** o un posible conjunto de estos valores. Si quiere agregar un elemento a un tema de Azure Service Bus designado después de que una acción específica **Insert_Row** produzca un error, puede usar la siguiente configuración de **runAfter**:

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

Tenga en cuenta que la propiedad **runAfter** está configurada para activar la acción **Insert_Row** si el resultado que se devuelve es **Errónea**. Para ejecutar la acción si el estado de la acción es **Correcta**, **Errónea** u **Omitida**, use esta sintaxis:

```json
"runAfter": {
        "Insert_Row": [
            "Failed", "Succeeded", "Skipped"
        ]
    }
```

> [!TIP]
> Las acciones que se ejecutan y completan correctamente después de que una acción anterior haya producido un error presentan la marca **Correcta**. Este comportamiento significa que, si detecta correctamente todos los errores de un flujo de trabajo, la propia ejecución se marca como **Correcta**.

## <a name="scopes-and-results-to-evaluate-actions"></a>Ámbitos y resultados para evaluar acciones

Puede agrupar las acciones dentro de un [ámbito](../logic-apps/logic-apps-loops-and-scopes.md), de forma similar a la manera que se utiliza después de realizar acciones individuales. Un ámbito actúa como una agrupación lógica de acciones. 

Los ámbitos son útiles para organizar las acciones de Logic Apps y para realizar evaluaciones agregadas sobre el estado de un ámbito. El ámbito en sí recibe un estado una vez que hayan terminado todas las acciones en un ámbito. El estado del ámbito se determina con los mismos criterios que una ejecución. Si la acción final en una rama de la ejecución tiene el valor **Errónea** o **Anulada**, el estado que se indica es **Errónea**.

Para activar acciones específicas para los errores que se produjeron dentro del ámbito, puede usar **runAfter** con un ámbito con la marca **Errónea**. Si *cualquier* acción en el ámbito produce un error, puede usar **runAfter** en el ámbito y así crear una única acción para detectar errores.

### <a name="get-the-context-of-failures-with-results"></a>Obtener el contexto de errores con resultados

Aunque la detección de errores desde un ámbito es útil, quizás también necesite el contexto como ayuda para comprender exactamente qué acciones han producido un error y los errores o códigos de estado que se hayan devuelto. La función del flujo de trabajo **@result()** proporciona contexto sobre el resultado de todas las acciones en un ámbito.

La función **@result()** toma un único parámetro (un nombre de ámbito) y devuelve una matriz de todos los resultados de acción desde dentro de ese ámbito. Estos objetos de acción incluyen los mismos atributos que el objeto **@actions()**, como la hora de inicio de la acción, la hora de finalización de la acción, el estado de la acción, las entradas de acción, los id. de correlación de acción y la salidas de acción. 

Para enviar el contexto de las acciones que produjeron un error dentro de un ámbito, puede emparejar fácilmente una función **@result()** con una propiedad **runAfter**.

Para ejecutar una acción *para cada una* de las acciones de un ámbito que tenga el estado **Errónea** y filtrar la matriz de resultados para las acciones que produjeron un error; puede emparejar **@result()** con una acción [Filter_array](../connectors/connectors-native-query.md) y un bucle [foreach](../logic-apps/logic-apps-loops-and-scopes.md). Con la matriz de resultados filtrada, puede realizar una acción para cada error mediante el bucle **foreach**. 

En este ejemplo se envía una solicitud HTTP POST con el cuerpo de respuesta de las acciones que produjeron un error dentro del ámbito My_Scope:

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

Este es un tutorial detallado que describe lo que sucede en el ejemplo anterior:

1. Para obtener el resultado de todas las acciones dentro de My_Scope, la acción **Filter_array** filtra **@result('My_Scope')**.

2. La condición de **Filter_array** es cualquier elemento **@result()** que tenga el estado **Erróneo**. Esta condición filtra la matriz con los resultados de todas las acciones de My_Scope a una matriz con solo los resultados de acción que hayan producido un error.

3. Realice de una acción **foreach** en las salidas de las *matrices filtradas*. En este paso se realiza una acción *para cada* acción resultante en error que se filtrara antes.

    Si una sola acción del ámbito produjo un error, las acciones en el elemento **foreach** solo se ejecutan una vez. Muchas acciones con error causan una acción por cada error.

4. Envíe una solicitud HTTP POST en el cuerpo de respuesta del elemento **foreach** o **@item()['outputs']['body']**. La forma del elemento **@result()** es la misma que la del elemento **@actions()**. Asimismo, ambos se pueden analizar de la misma manera.

5. Incluya dos encabezados personalizados con el nombre de la acción con errores **@item()['name']** y el id. de seguimiento de cliente de la ejecución con errores **@item()['clientTrackingId']**.

Para que sirva de referencia, este es un ejemplo de un único elemento **@result()**. Muestra las propiedades **name**, **body** y **clientTrackingId** analizadas en el ejemplo anterior. Fuera de una acción **foreach**, **@result()** devuelve una matriz de estos objetos.

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

Para poner en práctica diferentes patrones de control de excepciones, puede usar las expresiones que se describen en el artículo anterior. Puede optar por ejecutar una única acción de control de excepciones fuera del ámbito, que acepte toda la matriz filtrada de errores y quite el elemento **foreach**. También puede incluir otras propiedades útiles de la respuesta **@result()**, tal como se mostró anteriormente.

## <a name="azure-diagnostics-and-telemetry"></a>Diagnósticos de Azure y datos de telemetría

Los patrones que se describen en este artículo son una manera excelente de controlar errores y excepciones dentro de una ejecución, pero también pueden ayudarle a identificar y responder a los errores con independencia de la ejecución propiamente dicha. [Azure Diagnostics](../logic-apps/logic-apps-monitor-your-logic-apps.md) ofrece un método sencillo de enviar todos los eventos del flujo de trabajo (incluidos todos los estados de ejecución y acción) a una cuenta de Azure Storage o a un centro de eventos de Azure Event Hubs. 

Para evaluar los estados de ejecución, puede supervisar los registros y las métricas o publicarlos en la herramienta de supervisión que prefiera. Una posible opción es transmitir todos los eventos mediante Event Hubs a [Azure Stream Analytics](https://azure.microsoft.com/services/stream-analytics/). En Stream Analytics, puede escribir consultas en directo de anomalías, promedios o errores de los registros de diagnóstico. Asimismo, puede usar Stream Analytics para enviar información a otros orígenes de datos, como colas, temas, SQL, Azure Cosmos DB o Power BI.

## <a name="next-steps"></a>pasos siguientes

* Compruebe cómo un cliente [crea el control de errores con Logic Apps en Azure](../logic-apps/logic-apps-scenario-error-and-exception-handling.md).
* Encuentre más [escenarios y ejemplos de Logic Apps](../logic-apps/logic-apps-examples-and-scenarios.md).
* Aprenda a crear [implementaciones automatizadas de aplicaciones lógicas](../logic-apps/logic-apps-create-deploy-template.md).
* Aprenda a [crear e implementar aplicaciones lógicas con Visual Studio](logic-apps-deploy-from-vs.md).

<!-- References -->
[retryPolicyMSDN]: https://docs.microsoft.com/rest/api/logic/actions-and-triggers#Anchor_9
