---
title: Control de excepciones de Logic Apps | Microsoft Docs
description: Conozca los patrones para el control de errores y excepciones con Azure Logic Apps
services: logic-apps
documentationcenter: .net,nodejs,java
author: jeffhollan
manager: erikre
editor: ''

ms.service: logic-apps
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: integration
ms.date: 10/18/2016
ms.author: jehollan

---
# <a name="logic-apps-error-and-exception-handling"></a>Control de errores y excepciones de Logic Apps
Logic Apps proporciona un amplio conjunto de herramientas y patrones que garantizan la solidez y resistencia de sus integraciones frente a los errores.  Uno de los desafíos de cualquier arquitectura de integración es asegurar el control adecuado del tiempo de inactividad o de los problemas de sistemas dependientes.  Logic Apps convierte el control de errores en una experiencia de primera clase al proporcionarle las herramientas que necesita para actuar sobre las excepciones y los errores dentro de los flujos de trabajo.

## <a name="retry-policies"></a>Directivas de reintentos
El tipo más básico de control de errores y excepciones es una directiva de reintentos.  Esta directiva define si se debe reintentar la acción después de que la solicitud inicial ha agotado el tiempo de espera o ha producido error (toda solicitud que tenga como resultado una respuesta 429 o 5xx).  De forma predeterminada, todas las acciones se reintentan 4 veces adicionales durante intervalos de 20 segundos.  Por lo tanto, si la primera solicitud recibe una respuesta `500 Internal Server Error` , el motor de flujo de trabajo se pausa durante 20 segundos y vuelve a intentar la solicitud.  Si después de todos los reintentos la respuesta sigue siendo una excepción o un error, el flujo de trabajo continúa y marca el estado de la acción como `Failed`.

Puede configurar directivas de reintento en las **entradas** de una acción determinada.  Se puede configurar una directiva de reintentos para probar hasta 4 veces durante intervalos de 1 hora.  Todos los detalles sobre las propiedades de entrada se pueden [encontrar en MSDN][retryPolicyMSDN].

```json
"retryPolicy" : {
      "type": "<type-of-retry-policy>",
      "interval": <retry-interval>,
      "count": <number-of-retry-attempts>
    }
```

Si quisiera que la acción HTTP se reintentara 4 veces con intervalos de 10 minutos entre cada reintento, tendría la siguiente definición:

```json
"HTTP": 
{
    "inputs": {
        "method": "GET",
        "uri": "http://myAPIendpoint/api/action",
        "retryPolicy" : {
            "type": "fixed",
            "interval": "PT10M",
            "count": 4
        }
    },
    "runAfter": {},
    "type": "Http"
}
```

Para más información sobre la sintaxis que se admite, consulte la [sección sobre retryPolicy en MSDN][retryPolicyMSDN].

## <a name="runafter-property-to-catch-failures"></a>Propiedad RunAfter para detectar errores
Cada acción de una aplicación lógica declara las acciones que se deben realizar antes de que se inicie la acción.  Se puede considerar esto como el orden de los pasos del flujo de trabajo.  Esta ordenación se conoce como la propiedad `runAfter` en la definición de acción.  Es un objeto que describe qué acciones y estados de acciones ejecutarían la acción.  De forma predeterminada, todas las acciones que se agregan mediante el diseñador se establecen en `runAfter` el paso anterior, si el paso anterior era `Succeeded`.  Sin embargo, este valor se puede personalizar para que ejecute acciones cuando las acciones anteriores sean `Failed`, `Skipped`, o un posible conjunto de estos valores.  Si quisiera agregar un elemento a un tema de Bus de servicio designado después de que una acción específica `Insert_Row` produce error, usaría la siguiente configuración `runAfter`:

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

Observe que la propiedad `runAfter` está establecida para desencadenarse si el estado de la acción `Insert_Row` es `Failed`.  Para ejecutar la acción si el estado de la acción es `Succeeded`, `Failed`, o `Skipped`, la sintaxis sería:

```json
"runAfter": {
        "Insert_Row": [
            "Failed", "Succeeded", "Skipped"
        ]
    }
```

> [!TIP]
> Las acciones que se ejecutan después de que una acción anterior ha producido error, y que finalizan correctamente, se marcarán como `Succeeded`.  Este comportamiento significa que si detectar correctamente todos los errores de un flujo de trabajo, la propia ejecución se marca como `Succeeded`.
> 
> 

## <a name="scopes-and-results-to-evaluate-actions"></a>Ámbitos y resultados para evaluar acciones
Del mismo modo que puede establecer la propiedad runafter para acciones individuales, también puede agrupar acciones dentro de un [ámbito](app-service-logic-loops-and-scopes.md) , que actúa como una agrupación lógica de acciones.  Los ámbitos son útiles para organizar las acciones de aplicación lógica y para realizar evaluaciones agregadas sobre el estado de un ámbito.  El ámbito recibirá un estado después de que se hayan completado todas las acciones dentro de un ámbito.  El estado del ámbito se determina con los mismos criterios que una ejecución: si la acción final en una rama de ejecución es `Failed` o `Aborted`, el estado es `Failed`.

Puede ejecutar una acción `runAfter` sobre un ámbito que se ha marcado como `Failed` para desencadenar acciones específicas contra los posibles errores que se produzcan dentro del ámbito.  La ejecución después de que un ámbito produce error le permite crear una sola acción para detectar errores si *cualquier* acción dentro de un ámbito produce error.

### <a name="getting-the-context-of-failures-with-results"></a>Obtención del contexto de errores con resultados
La detección de errores de un ámbito es muy útil, pero quizás también quiera el contexto para comprender exactamente las acciones que han producido error, y los errores o códigos de estado que se han devuelto.  La función del flujo de trabajo `@result()` proporciona contexto sobre el resultado de todas las acciones dentro de un ámbito.

`@result()` toma un único parámetro, un nombre de ámbito, y devuelve una matriz de todos los resultados de acción desde dentro de ese ámbito.  Estos objetos de acción incluyen los mismos atributos que el objeto `@actions()`, como la hora de inicio de la acción, la hora de finalización de la acción, el estado de la acción, las entradas de acción, los id. de correlación de acción y la salidas de acción.  Puede emparejar fácilmente una función `@result()` con una propiedad `runAfter` para enviar contexto de las acciones que produjeron error dentro de un ámbito.

Si desea ejecutar una acción *para cada una* de las acciones de un ámbito con el estado `Failed`, puede emparejar `@result()` con una acción **[Filter Array](../connectors/connectors-native-query.md)** y un bucle **[ForEach](app-service-logic-loops-and-scopes.md)**.  Esto le permite filtrar la matriz de resultados por las acciones que han producido error.  Puede tomar la matriz de resultados filtrada y realizar una acción para cada error mediante el bucle **ForEach** .  A continuación se muestra un ejemplo, seguido de una explicación detallada.  En este ejemplo se envía una solicitud HTTP POST con el cuerpo de respuesta de las acciones que produjeron error dentro del ámbito `My_Scope`.

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

Este es un tutorial detallado de lo que sucede:

1. Acción **Filter Array** para filtrar el valor de `@result('My_Scope')` y obtener el resultado de todas las acciones de `My_Scope`
2. La condición de **Filter Array** es cualquier elemento de `@result()` cuyo estado sea igual a `Failed`.  Esto filtra la matriz de todos los resultados de acción de `My_Scope` por solo una matriz de resultados de acción con error.
3. Realización de una acción **For Each** en las salidas **Filtered Array**.  Se lleva a cabo una acción *for each* para cada resultado de acción con error que hemos filtrado anteriormente.
   * Si ha habido una única acción en el ámbito que ha producido error, las acciones del bucle `foreach` solo se ejecutarán una vez.  Muchas acciones con error darían lugar a una acción por error.
4. Envío de una solicitud HTTP POST en el cuero de respuesta del elemento `foreach`, o `@item()['outputs']['body']`.  La forma del elemento `@result()` es la misma que la forma de `@actions()`, y se puede analizar del mismo modo.
5. También se incluyen dos encabezados personalizados con el nombre de la acción con errores `@item()['name']` y el id. de seguimiento de cliente de la ejecución con errores `@item()['clientTrackingId']`.

Para que sirva de referencia, este es un ejemplo de un único elemento `@result()` .  Puede ver las propiedades `name`, `body` y `clientTrackingId` analizadas en el ejemplo anterior.  Es necesario señalar que fuera de un elemento `foreach`, `@result()` devuelve una matriz de estos objetos.

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
            "message": "/docs/foo/bar does not exist"
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

Puede utilizar las expresiones anteriores para realizar diferentes patrones de control de excepciones.  Puede elegir ejecutar una única acción de control de excepciones fuera del ámbito que acepte toda la matriz filtrada de errores y quite el elemento `foreach`.  También puede incluir otras propiedades útiles desde la respuesta `@result()` mostrada anteriormente.

## <a name="azure-diagnostics-and-telemetry"></a>Diagnósticos de Azure y datos de telemetría
Los patrones vistos anteriormente son una manera excelente de controlar errores y excepciones dentro de una ejecución, pero también puede identificar y responder a los errores con independencia de la ejecución propiamente dicha.  [Diagnósticos de Azure](app-service-logic-monitor-your-logic-apps.md) ofrece un método sencillo de enviar todos los eventos de flujo de trabajo (incluidos todos los estados de ejecución y acción) a una cuenta de almacenamiento de Azure o a un Centro de eventos de Azure.  Puede supervisar los registros y las métricas, o publicarlos en la herramienta de supervisión que prefiera, para evaluar los estados de ejecución.  Una posible opción es transmitir todos los eventos mediante el Centro de eventos de Azure a [Análisis de transmisiones](https://azure.microsoft.com/services/stream-analytics/).  En Análisis de transmisiones, puede descartar consultas en directo de anomalías, promedios o errores de los registros de diagnóstico.  Con Análisis de transmisiones es fácil enviar resultados a otros orígenes de datos, como colas, temas, SQL, DocumentDB y Power BI.

## <a name="next-steps"></a>Pasos siguientes
* [Vea cómo un cliente ha creado un sólido control de errores con Logic Apps](app-service-logic-scenario-error-and-exception-handling.md)
* [Encuentre más escenarios y ejemplos de Logic Apps](app-service-logic-examples-and-scenarios.md)
* [Aprenda a crear implementaciones automatizadas de las aplicaciones lógicas](app-service-logic-create-deploy-template.md)
* [Diseñe e implemente aplicaciones lógicas desde Visual Studio](app-service-logic-deploy-from-vs.md)

<!-- References -->
[retryPolicyMSDN]: https://msdn.microsoft.com/library/azure/mt643939.aspx#Anchor_9


<!--HONumber=Oct16_HO2-->


