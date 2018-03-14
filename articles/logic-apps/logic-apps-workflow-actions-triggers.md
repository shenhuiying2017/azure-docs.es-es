---
title: 'Desencadenadores y acciones de flujo de trabajo: Azure Logic Apps | Microsoft Docs'
description: "Obtenga información acerca de los desencadenadores y las acciones para crear flujos de trabajo y procesos automatizados con aplicaciones lógicas."
services: logic-apps
author: MandiOhlinger
manager: anneta
editor: 
documentationcenter: 
ms.assetid: 86a53bb3-01ba-4e83-89b7-c9a7074cb159
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: multiple
ms.topic: article
ms.date: 10/13/2017
ms.author: klam; LADocs
ms.openlocfilehash: af30fd30f389cdc2070c45ae3b6e2cb1165239e7
ms.sourcegitcommit: 0b02e180f02ca3acbfb2f91ca3e36989df0f2d9c
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 03/05/2018
---
# <a name="triggers-and-actions-for-logic-app-workflows"></a>Desencadenadores y acciones para flujos de trabajo de aplicación lógica

Todas las aplicaciones lógicas se inician con un desencadenador seguido de acciones. En este artículo se describen los tipos de desencadenadores y acciones que puede usar para crear integraciones del sistema y automatizar procesos o flujos de trabajo empresariales mediante la creación de aplicaciones lógicas. 
  
## <a name="triggers-overview"></a>Introducción a los desencadenadores 

Todas las aplicaciones lógicas se inician con un desencadenador, que especifica las llamadas que pueden iniciar la ejecución de una aplicación lógica. Estos son los tipos de desencadenadores que puede usar:

* Un desencadenador de *sondeo*, que comprueba el punto de conexión HTTP de un servicio a intervalos regulares
* Un desencadenador de *push*, que llama a la [API REST del servicio de flujo de trabajo](https://docs.microsoft.com/rest/api/logic/workflows)
  
Todos los desencadenadores contienen estos elementos de nivel superior:  
  
```json
"<myTriggerName>": {
    "type": "<triggerType>",
    "inputs": { <callSettings> },
    "recurrence": {  
        "frequency": "Second | Minute | Hour | Day | Week | Month | Year",
        "interval": "<recurrence-interval-based-on-frequency>"
    },
    "conditions": [ <array-with-required-conditions> ],
    "splitOn": "<property-used-for-creating-runs>",
    "operationOptions": "<options-for-operations-on-the-trigger>"
}
```

## <a name="trigger-types-and-inputs"></a>Tipos y entradas de desencadenadores  

Cada tipo de desencadenador tiene una interfaz diferente y distintas *entradas* que definen su comportamiento. 

| Tipo de desencadenador | DESCRIPCIÓN | 
| ------------ | ----------- | 
| **Periodicidad** | Se desencadena en función de una programación definida. Puede establecer una fecha y hora futuras para activar este desencadenador. Según la frecuencia, también puede especificar horas y días para ejecutar el flujo de trabajo. | 
| **Solicitud**  | Transforma la aplicación lógica en un punto de conexión que se puede llamar, también conocido como desencadenador "manual". | 
| **HTTP** | Comprueba o *sondea* un punto de conexión web HTTP. El punto de conexión HTTP debe ajustarse a un contrato de desencadenamiento específico, ya sea mediante un patrón asincrónico "202" o devolviendo una matriz. | 
| **ApiConnection** | Sondea al igual que un desencadenador HTTP, pero usa [API administradas por Microsoft](../connectors/apis-list.md). | 
| **HTTPWebhook** | Transforma la aplicación lógica en un punto de conexión que se puede llamar, como el desencadenador **Request**, pero llama a una dirección URL especificada para registrar y anular el registro. |
| **ApiConnectionWebhook** | Funciona igual que el desencadenador **HTTPWebhook**, pero usa las API administrada por Microsoft. | 
||| 

Para obtener más información, consulte [Esquema del lenguaje de definición de flujo de trabajo](../logic-apps/logic-apps-workflow-definition-language.md). 

<a name="recurrence-trigger"></a>

## <a name="recurrence-trigger"></a>Desencadenador de periodicidad  

Este desencadenador se ejecuta según la periodicidad y programación que especifique y proporciona una manera sencilla de ejecutar con regularidad un flujo de trabajo. 

Este es un ejemplo de desencadenador de periodicidad básico que se ejecuta a diario:

```json
"myRecurrenceTrigger": {
    "type": "Recurrence",
    "recurrence": {
        "frequency": "Day",
        "interval": 1
    }
}
```

También puede programar una fecha y hora de inicio para activar el desencadenador. Por ejemplo, para iniciar un informe semanal todos los lunes, puede programar la aplicación lógica para que se inicie un lunes específico como en este ejemplo: 

```json
"myRecurrenceTrigger": {
    "type": "Recurrence",
    "recurrence": {
        "frequency": "Week",
        "interval": "1",
        "startTime": "2017-09-18T00:00:00Z"
    }
}
```

Esta es la definición para este desencadenador:

```json
"myRecurrenceTrigger": {
    "type": "Recurrence",
    "recurrence": {
        "frequency": "second|minute|hour|day|week|month",
        "interval": <recurrence-interval-based-on-frequency>,
        "schedule": {
            // Applies only when frequency is Day or Week. Separate values with commas.
            "hours": [ <one-or-more-hour-marks> ], 
            // Applies only when frequency is Day or Week. Separate values with commas.
            "minutes": [ <one-or-more-minute-marks> ], 
            // Applies only when frequency is Week. Separate values with commas.
            "weekDays": [ "Monday, Tuesday, Wednesday, Thursday, Friday, Saturday, Sunday" ] 
        },
        "startTime": "<start-date-time-with-format-YYYY-MM-DDThh:mm:ss>",
        "timeZone": "<specify-time-zone>"
    }
}
```

| Nombre del elemento | Obligatorio | type | DESCRIPCIÓN | 
| ------------ | -------- | ---- | ----------- | 
| frequency | Sí | string | La unidad de tiempo para la frecuencia con la que se activa el desencadenador. Use solo uno de estos valores: "second", "minute", "hour", "day", "week" o "month" | 
| interval | Sí | Entero | Entero positivo que describe la frecuencia con la que se ejecuta el flujo de trabajo. <p>Estos son los intervalos mínimo y máximo: <p>- Month: 1-16 meses </br>- Day: 1-500 días </br>- Hour: 1-12 000 horas </br>- Minute: 1-72 000 minutos </br>- Second: 1-9 999 999 segundos<p>Por ejemplo, si el intervalo es 6 y la frecuencia es "month", la periodicidad es cada 6 meses. | 
| timeZone | Sin  | string | Solo se aplica cuando se especifica una hora de inicio porque este desencadenador no acepta [diferencia horaria con UTC](https://en.wikipedia.org/wiki/UTC_offset). Especifique la zona horaria que desea aplicar. | 
| startTime | Sin  | string | Especifique la fecha y hora de inicio en este formato: <p>AAAA-MM-DDThh:mm:ss si especifica una zona horaria <p>O bien <p>AAAA-MM-DDThh:mm:ssZ si no especifica una zona horaria <p>Por ejemplo, si desea la fecha del 18 de septiembre de 2017 a las 2:00 p.m., especifique entonces "2017-09-18T14:00:00" y especifique una zona horaria como "Hora estándar del Pacífico". O bien, especifique "2017-09-18T14:00:00Z" sin una zona horaria. <p>**Nota:** Esta hora de inicio debe seguir la [especificación de fecha y hora ISO 8601](https://en.wikipedia.org/wiki/ISO_8601#Combined_date_and_time_representations) en [formato de hora y fecha UTC](https://en.wikipedia.org/wiki/Coordinated_Universal_Time), pero sin una [diferencia horaria con UTC](https://en.wikipedia.org/wiki/UTC_offset). Si no se especifica una zona horaria, debe agregar la letra "Z" al final sin espacios. Esta "Z" se refiere al equivalente de [hora náutica](https://en.wikipedia.org/wiki/Nautical_time). <p>Para las programaciones simples, la hora de inicio es la primera aparición, mientras que para programaciones complejas, el desencadenador no se activa antes de la hora de inicio. Para más información sobre las fechas y horas de inicio, consulte [Introducción al desencadenador de periodicidad](../connectors/connectors-native-recurrence.md). | 
| weekDays | Sin  | Cadena o matriz de cadenas | Si especifica "Week" para `frequency`, puede especificar uno o varios días, separados por comas, cuando desee ejecutar el flujo de trabajo: "Monday", "Tuesday", "Wednesday", "Thursday", "Friday", "Saturday" y "Sunday" | 
| hours | Sin  | Entero o matriz de enteros | Si especifica "Day" o "Semana" para `frequency`, puede especificar uno o varios enteros de 0 a 23, separados por comas, como las horas del día en las que desea ejecutar el flujo de trabajo. <p>Por ejemplo, si especifica "10", "12" y "14", obtendrá 10 a. m., 12 p. m. y 2 p. m. como las marcas de hora. | 
| minutes | Sin  | Entero o matriz de enteros | Si especifica "Day" o "Semana" para `frequency`, puede especificar uno o varios enteros de 0 a 59, separados por comas, como los minutos de la hora en los que desea ejecutar el flujo de trabajo. <p>Por ejemplo, puede especificar "30" como la marca de minuto y, utilizando el ejemplo anterior para las horas del día, obtendrá 10:30 a.m., 12:30 p.m. y las 2:30 p.m. | 
||||| 

Por ejemplo, este desencadenador de periodicidad especifica que la aplicación lógica se ejecuta semanalmente todos los lunes a las 10:30 a.m., a las 12:30 p.m. y a las 2:30 p.m. en hora estándar del Pacífico, y no empieza antes del 9 de septiembre de 2017 a las 2:00 p.m.:

``` json
"myRecurrenceTrigger": {
    "type": "Recurrence",
    "recurrence": {
        "frequency": "Week",
        "interval": 1,
        "schedule": {
            "hours": [
                10,
                12,
                14
            ],
            "minutes": [
                30
            ],
            "weekDays": [
                "Monday"
            ]
        },
       "startTime": "2017-09-07T14:00:00",
       "timeZone": "Pacific Standard Time"
    }
}
```

Para más información sobre ejemplos de periodicidad y hora de inicio para este desencadenador, consulte [Introducción al desencadenador de periodicidad](../connectors/connectors-native-recurrence.md).

## <a name="request-trigger"></a>Desencadenador de solicitud

Este desencadenador actúa como un punto de conexión que se usa para llamar a la aplicación lógica a través de una solicitud HTTP. Un desencadenador de solicitud es similar a este ejemplo:  
  
```json
"myRequestTrigger": {
    "type": "Request",
    "kind": "Http",
    "inputs": {
        "schema": {
            "type": "Object",
            "properties": {
                "myInputProperty1": { "type" : "string" },
                "myInputProperty2": { "type" : "number" }
            },
            "required": [ "myInputProperty1" ]
        }
    }
} 
```

Este desencadenador tiene una propiedad opcional denominada `schema`:
  
| Nombre del elemento | Obligatorio | type | DESCRIPCIÓN |
| ------------ | -------- | ---- | ----------- |
| schema | Sin  | Objeto | Un esquema JSON que valida la solicitud entrante. Resulta útil para ayudar a los pasos de flujo de trabajo subsiguientes a los que las propiedades hacen referencia. | 
||||| 

Para invocar este punto de conexión, debe llamar a la API `listCallbackUrl`. Consulte [API de REST de servicio de flujo de trabajo](https://docs.microsoft.com/rest/api/logic/workflows).

## <a name="http-trigger"></a>Desencadenador HTTP  

Este desencadenador sondea un punto de conexión especificado y comprueba la respuesta para determinar si se debe ejecutar el flujo de trabajo o no. En este caso, el objeto `inputs` toma estos parámetros necesarios para construir una llamada HTTP: 

| Nombre del elemento | Obligatorio | type | DESCRIPCIÓN | 
| ------------ | -------- | ---- | ----------- | 
| estático | Sí | string | Usa uno de los siguientes métodos HTTP: "GET", "POST", "PUT", "DELETE", "PATCH" o "HEAD". | 
| uri | Sí| string | El punto de conexión HTTP o HTTPS que el desencadenador comprueba. Tamaño máximo de la cadena: 2 KB | 
| Consultas | Sin  | Objeto | Representa los parámetros de consulta que se van a incluir en la dirección URL. <p>Por ejemplo, `"queries": { "api-version": "2015-02-01" }` agrega `?api-version=2015-02-01` a la dirección URL. | 
| encabezados | Sin  | Objeto | Representa cada encabezado que se envía en la solicitud. <p>Por ejemplo, para establecer el idioma y el tipo en una solicitud: <p>`"headers": { "Accept-Language": "en-us", "Content-Type": "application/json" }` | 
| Cuerpo | Sin  | Objeto | Representa la carga útil que se envía al punto de conexión. | 
| retryPolicy | Sin  | Objeto | Utilice este objeto para personalizar el comportamiento de reintento para errores 4xx o 5xx. Para más información, consulte [Directivas de reintentos](../logic-apps/logic-apps-exception-handling.md). | 
| Autenticación | Sin  | Objeto | Representa el método que debe usar la solicitud para autenticación. Para más información, consulte [Autenticación saliente de Scheduler](../scheduler/scheduler-outbound-authentication.md). <p>Aparte de Scheduler, hay una propiedad más que se admite: `authority`. De forma predeterminada, este valor es `https://login.windows.net` cuando no se especifica, pero se puede usar un valor diferente, como `https://login.windows\-ppe.net`. | 
||||| 

Una *directiva de reintentos* se aplica a errores intermitentes, caracterizados como códigos de estado HTTP 408, 429 y 5xx, además de excepciones de conectividad. Puede definir esta directiva con el objeto `retryPolicy` tal y como se muestra aquí:
  
```json
"retryPolicy": {
    "type": "<retry-policy-type>",
    "interval": <retry-interval>,
    "count": <number-of-retry-attempts>
}
```

Para que funcione bien con la aplicación lógica, el desencadenador HTTP necesita la API de HTTP para ajustarse a un patrón específico. El desencadenador reconoce estas propiedades:  
  
| Response | Obligatorio | DESCRIPCIÓN | 
| -------- | -------- | ----------- |  
| Código de estado | Sí | El código de estado 200 ("Correcto") provoca una ejecución. Cualquier otro código de estado no provoca una ejecución. | 
| Encabezado Retry-after | Sin  | Número de segundos hasta que la aplicación lógica sondea de nuevo el punto de conexión. | 
| Encabezado Location | Sin  | La dirección URL para llamar en el siguiente intervalo de sondeo. Si no se especifica, se usa la dirección URL original. | 
|||| 

Estos son algunos comportamientos de ejemplo para diferentes tipos de solicitudes:
  
| Response code | Reintentar después | Comportamiento | 
| ------------- | ----------- | -------- | 
| 200 | {none} | Ejecutar el flujo de trabajo y luego comprobar de nuevo si hay más datos después de la periodicidad definida. | 
| 200 | 10 segundos | Ejecutar el flujo de trabajo y luego comprobar de nuevo si hay más datos después de 10 segundos. |  
| 202 | 60 segundos | No desencadenar el flujo de trabajo. El próximo intento tiene lugar en un minuto, según cuál sea la periodicidad definida. Si la periodicidad definida es inferior a un minuto, el encabezado retry-after tiene prioridad. Si no, se usa la periodicidad definida. | 
| 400 | {none} | Solicitud incorrecta, no ejecutar el flujo de trabajo. Si no se define ningún `retryPolicy`, se utiliza la directiva predeterminada. Una vez alcanzado el número de reintentos, el desencadenador volverá a comprobar si hay datos después de la periodicidad definida. | 
| 500 | {none}| Error del servidor, no ejecutar el flujo de trabajo. Si no se define ningún `retryPolicy`, se utiliza la directiva predeterminada. Una vez alcanzado el número de reintentos, el desencadenador volverá a comprobar si hay datos después de la periodicidad definida. | 
|||| 

Estas son las salidas de desencadenador HTTP: 
  
| Nombre del elemento | type | DESCRIPCIÓN |
| ------------ | ---- | ----------- |
| encabezados | Objeto | Los encabezados de la respuesta HTTP | 
| Cuerpo | Objeto | El cuerpo de la respuesta HTTP | 
|||| 

<a name="apiconnection-trigger"></a>

## <a name="apiconnection-trigger"></a>Desencadenador APIConnection  

En la funcionalidad básica, este desencadenador funciona como el desencadenador HTTP. Sin embargo, los parámetros para identificar la acción son diferentes. Este es un ejemplo:   
  
```json
"myDailyReportTrigger": {
    "type": "ApiConnection",
    "inputs": {
        "host": {
            "api": {
                "runtimeUrl": "https://myarticles.example.com/"
            }
        },
        "connection": {
            "name": "@parameters('$connections')['myconnection'].name"
        }
    },  
    "method": "POST",
    "body": {
        "category": "myCategory"
    }
}
```

| Nombre del elemento | Obligatorio | type | DESCRIPCIÓN | 
| ------------ | -------- | ---- | ----------- | 
| host | Sí | Objeto | La puerta de enlace hospedada y el identificador de la aplicación API | 
| estático | Sí | string | Usa uno de los siguientes métodos HTTP: "GET", "POST", "PUT", "DELETE", "PATCH" o "HEAD". | 
| Consultas | Sin  | Objeto | Representa los parámetros de consulta que se van a incluir en la dirección URL. <p>Por ejemplo, `"queries": { "api-version": "2015-02-01" }` agrega `?api-version=2015-02-01` a la dirección URL. | 
| encabezados | Sin  | Objeto | Representa cada encabezado que se envía en la solicitud. <p>Por ejemplo, para establecer el idioma y el tipo en una solicitud: <p>`"headers": { "Accept-Language": "en-us", "Content-Type": "application/json" }` | 
| Cuerpo | Sin  | Objeto | Representa la carga útil que se envía al punto de conexión. | 
| retryPolicy | Sin  | Objeto | Utilice este objeto para personalizar el comportamiento de reintento para errores 4xx o 5xx. Para más información, consulte [Directivas de reintentos](../logic-apps/logic-apps-exception-handling.md). | 
| Autenticación | Sin  | Objeto | Representa el método que debe usar la solicitud para autenticación. Para más información, consulte [Autenticación saliente de Scheduler](../scheduler/scheduler-outbound-authentication.md). | 
||||| 

Para el objeto `host`, estas son las propiedades:  
  
| Nombre del elemento | Obligatorio | DESCRIPCIÓN | 
| ------------ | -------- | ----------- | 
| api runtimeUrl | Sí | Punto de conexión de la API administrada | 
| connection name |  | Nombre de la conexión de API administrada que utiliza el flujo de trabajo. Debe hacer referencia a un parámetro denominado `$connection`. |
|||| 

Una *directiva de reintentos* se aplica a errores intermitentes, caracterizados como códigos de estado HTTP 408, 429 y 5xx, además de excepciones de conectividad. Puede definir esta directiva con el objeto `retryPolicy` tal y como se muestra aquí:
  
```json
"retryPolicy": {
    "type": "<retry-policy-type>",
    "interval": <retry-interval>,
    "count": <number-of-retry-attempts>
}
```

Estas son las salidas para un desencadenador de conexión de API:
  
| Nombre del elemento | type | DESCRIPCIÓN |
| ------------ | ---- | ----------- |
| encabezados | Objeto | Los encabezados de la respuesta HTTP | 
| Cuerpo | Objeto | El cuerpo de la respuesta HTTP | 
|||| 

Obtenga más información sobre [el funcionamiento de los precios de los desencadenadores de conexión de API](../logic-apps/logic-apps-pricing.md#triggers).

## <a name="httpwebhook-trigger"></a>Desencadenador HTTPWebhook  

Este desencadenador proporciona un punto de conexión, similar al desencadenador `Request`, pero el desencadenador HTTPWebhook también llama a una dirección URL especificada para registrar y anular el registro. Este es un ejemplo del aspecto que podría tener un desencadenador HTTPWebhook:

```json
"myAppsSpotTrigger": {
    "type": "HttpWebhook",
    "inputs": {
        "subscribe": {
            "method": "POST",
            "uri": "https://pubsubhubbub.appspot.com/subscribe",
            "headers": {},
            "body": {
                "hub.callback": "@{listCallbackUrl()}",
                "hub.mode": "subscribe",
                "hub.topic": "https://pubsubhubbub.appspot.com/articleCategories/technology"
            },
            "authentication": {},
            "retryPolicy": {}
        },
        "unsubscribe": {
            "method": "POST",
            "url": "https://pubsubhubbub.appspot.com/subscribe",
            "body": {
                "hub.callback": "@{workflow().endpoint}@{listCallbackUrl()}",
                "hub.mode": "unsubscribe",
                "hub.topic": "https://pubsubhubbub.appspot.com/articleCategories/technology"
            },
            "authentication": {}
        }
    },
    "conditions": []
}
```

Muchas de estas secciones son opcionales y el comportamiento del desencadenador HTTPWebhook depende de las secciones que se proporcionan u omiten. Estas son las propiedades del desencadenador HTTPWebhook:
  
| Nombre del elemento | Obligatorio | DESCRIPCIÓN | 
| ------------ | -------- | ----------- |  
| subscribe | Sin  | Especifica la solicitud saliente a la que se llama cuando se crea el desencadenador y realiza el registro inicial. | 
| unsubscribe | Sin  | Especifica la solicitud saliente para llamar cuando se elimina el desencadenador. | 
|||| 

Puede especificar límites sobre un desencadenador de webhook de la misma manera que los [límites asincrónicos de HTTP](#asynchronous-limits). Aquí tiene más información sobre las acciones `subscribe` y `unsubscribe`:

* `subscribe`se llama para que el desencadenador pueda comenzar a escuchar eventos. Esta llamada saliente se inicia con los mismos parámetros como acciones HTTP estándar. Esta llamada tiene lugar cuando el flujo de trabajo cambia de algún modo; por ejemplo, cada vez que se sustituyen las credenciales o cambian los parámetros de entrada del desencadenador. 
  
  Para admitir esta llamada, la función `@listCallbackUrl()` devuelve una dirección URL única para este desencadenador específico en este flujo de trabajo. Esta dirección URL representa el identificador único de los puntos de conexión que usan la API de REST del servicio.
  
* `unsubscribe` se llama automáticamente cuando una operación representa este desencadenador como no válido, lo que incluye estas operaciones:

  * Eliminar o deshabilitar el desencadenador. 
  * Eliminar o deshabilitar el flujo de trabajo. 
  * Eliminar o deshabilitar la suscripción. 
  
  Los parámetros de esta función son los mismos que los del desencadenador HTTP.

Estas son las salidas del desencadenador HTTPWebhook y el contenido de la solicitud entrante:
  
| Nombre del elemento | type | DESCRIPCIÓN |
| ------------ | ---- | ----------- |
| encabezados | Objeto | Los encabezados de la respuesta HTTP | 
| Cuerpo | Objeto | El cuerpo de la respuesta HTTP | 
|||| 

## <a name="triggers-conditions"></a>Desencadenadores: condiciones

Para cualquier desencadenador, puede usar una o varias condiciones para determinar si el flujo de trabajo debe ejecutarse o no. En este ejemplo, el informe solo se desencadena mientras el parámetro `sendReports` del flujo de trabajo está establecido en true. 

```json
"myDailyReportTrigger": {
    "type": "Recurrence",
    "conditions": [ 
        {
            "expression": "@parameters('sendReports')"
        } 
    ],
    "recurrence": {
        "frequency": "Day",
        "interval": 1
    }
}
```

Por último, las condiciones pueden hacer referencia al código de estado del desencadenador. Por ejemplo, puede iniciar un flujo de trabajo solo cuando su sitio web devuelva un código de estado 500:
  
``` json
"conditions": [ 
    {  
      "expression": "@equals(triggers().code, 'InternalServerError')"  
    }  
]  
```  

> [!NOTE]
> De forma predeterminada, se activa un desencadenador solo al recibir una respuesta "200 - CORRECTO". Cuando una expresión hace referencia al código de estado del desencadenador de algún modo, se sustituye el comportamiento predeterminado del desencadenador. Por lo tanto, si desea que el desencadenador se active según varios códigos de estado, por ejemplo, el código de estado 200 y el código de estado 201, debe incluir esta declaración como condición: 
>
> `@or(equals(triggers().code, 200),equals(triggers().code, 201))` 

<a name="split-on-debatch"></a>

## <a name="triggers-process-an-array-with-multiple-runs"></a>Desencadenadores: proceso de una matriz con varias ejecuciones

Si el desencadenador devuelve una matriz para que la procese la aplicación lógica, un bucle "para cada una" podría tardar demasiado tiempo en procesar cada elemento de la matriz. En lugar de eso, puede usar la propiedad **SplitOn** en el desencadenador para *desagrupar* la matriz. 

La desagrupación sirve para dividir los elementos de la matriz e iniciar una nueva instancia de la aplicación lógica que se ejecuta para cada elemento de la matriz. Este enfoque resulta útil, por ejemplo, si desea sondear un punto de conexión que puede devolver varios elementos nuevos entre intervalos de sondeo.
Para conocer el número máximo de elementos de matriz que **SplitOn** puede procesar en una única ejecución de aplicación lógica, consulte [Límites y configuración](../logic-apps/logic-apps-limits-and-config.md). 

> [!NOTE]
> Puede agregar **SplitOn** solo a los desencadenadores mediante la definición o el reemplazo manual en la vista de código para la definición de JSON de la aplicación lógica. No puede utilizar **SplitOn** cuando desee implementar un patrón de respuesta sincrónica. Cualquier flujo de trabajo que use **SplitOn** e incluya una acción de respuesta se ejecuta de forma asincrónica y envía de inmediato una respuesta `202 ACCEPTED`.

Si el archivo Swagger del desencadenador describe una carga que es una matriz, la propiedad **SplitOn** se agrega automáticamente a su desencadenador. En caso contrario, agregue esta propiedad en la carga de respuesta que tiene la matriz que desea desagrupar. 

Por ejemplo, suponga que tiene una API que devuelve esta respuesta: 
  
```json
{
    "Status": "Succeeded",
    "Rows": [ 
        { 
            "id": 938109380,
            "name": "customer-name-one"
        },
        {
            "id": 938109381,
            "name": "customer-name-two"
        }
    ]
}
```
  
La aplicación lógica solo necesita el contenido de `Rows`, por lo que puede crear un desencadenador como en este ejemplo.

``` json
"myDebatchTrigger": {
    "type": "Http",
    "recurrence": {
        "frequency": "Second",
        "interval": "1"
    },
    "inputs": {
        "uri": "https://mydomain.com/myAPI",
        "method": "GET"
    },
    "splitOn": "@triggerBody()?.Rows"
}
```

> [!NOTE]
> Si usa el comando `SplitOn`, no puede obtener las propiedades que están fuera de la matriz. Así pues, en este ejemplo, no puede obtener la propiedad `status` en la respuesta que se devuelve a través de la API.
> 
> Para evitar un error si la propiedad `Rows` no existe, en este ejemplo se utiliza el operador `?`.

La definición de flujo de trabajo ahora puede usar `@triggerBody().name` para obtener `customer-name-one` de la primera ejecución y `customer-name-two` de la segunda ejecución. Así, las salidas del desencadenador se parecen a estos ejemplos:

```json
{
    "body": {
        "id": 938109380,
        "name": "customer-name-one"
    }
}
```

```json
{
    "body": {
        "id": 938109381,
        "name": "customer-name-two"
    }
}
```
  
## <a name="triggers-fire-only-after-all-active-runs-finish"></a>Desencadenadores: activación únicamente cuando finalicen todas las ejecuciones activas

Puede configurar desencadenadores de periodicidad para que se activen solo cuando se hayan completado todas las ejecuciones activas. Para definir esta configuración, establezca la propiedad `operationOptions` en `singleInstance`:

```json
"myTrigger": {
    "type": "Http",
    "inputs": { },
    "recurrence": { },
    "operationOptions": "singleInstance"
}
```

Si tiene lugar una periodicidad programada mientras se está ejecutando una instancia del flujo de trabajo, el desencadenador la omite y espera hasta el siguiente intervalo de periodicidad programada para volver a comprobarlo.

## <a name="actions-overview"></a>Información general sobre acciones

Hay muchos tipos de acciones, cada una con un comportamiento único. Cada tipo de acción tiene diferentes entradas que definen el comportamiento de una acción. Las acciones de colección pueden contener muchas otras acciones dentro de sí mismas. 

### <a name="standard-actions"></a>Acciones estándar  

| Tipo de acción | DESCRIPCIÓN | 
| ----------- | ----------- | 
| **HTTP** | Llama a un punto de conexión web HTTP. | 
| **ApiConnection**  | Funciona igual que la acción HTTP, pero usa [API administradas por Microsoft](https://docs.microsoft.com/azure/connectors/apis-list). | 
| **ApiConnectionWebhook** | Funciona igual que HTTPWebhook, pero usa API administradas por Microsoft. | 
| **Respuesta** | Define la respuesta para una llamada entrante. | 
| **Compose** | Construye un objeto arbitrario a partir de entradas de la acción. | 
| **Function** | Representa una función de Azure. | 
| **Wait** | Espera una cantidad fija de tiempo o hasta una hora específica. | 
| **Workflow** | Representa un flujo de trabajo anidado. | 
| **Compose** | Construye un objeto arbitrario a partir de entradas de la acción. | 
| **Consultar** | Filtra una matriz basada en una condición. | 
| **Selección** | Proyecta cada elemento de una matriz en un nuevo valor. Por ejemplo, puede convertir una matriz de números en una matriz de objetos. | 
| **Table** | Convierte una matriz de elementos en una tabla CSV o HTML. | 
| **Terminate** | Detiene la ejecución de un flujo de trabajo. | 
| **Wait** | Espera una cantidad fija de tiempo o hasta una hora específica. | 
| **Workflow** | Representa un flujo de trabajo anidado. | 
||| 

### <a name="collection-actions"></a>Acciones de colección

| Tipo de acción | DESCRIPCIÓN | 
| ----------- | ----------- | 
| **If** | Evalúa una expresión y, en función del resultado, ejecuta la bifurcación correspondiente. | 
| **Switch** | Realiza diferentes acciones basadas en valores específicos de un objeto. | 
| **ForEach** | Esta acción de bucle recorre en iteración una matriz y realiza acciones internas en cada elemento de la matriz. | 
| **Until** | Esta acción de bucle realiza acciones internas hasta que una condición da como resultado true. | 
| **Ámbito** | Se usa para agrupar lógicamente otras acciones. | 
|||  

## <a name="http-action"></a>Acción HTTP  

Una acción HTTP llama a un punto de conexión especificado y comprueba la respuesta para determinar si se debe ejecutar el flujo de trabajo o no. Por ejemplo: 
  
```json
"myLatestNewsAction": {
    "type": "Http",
    "inputs": {
        "method": "GET",
        "uri": "https://mynews.example.com/latest"
    }
}
```

En este caso, el objeto `inputs` toma estos parámetros necesarios para construir una llamada HTTP: 

| Nombre del elemento | Obligatorio | type | DESCRIPCIÓN | 
| ------------ | -------- | ---- | ----------- | 
| estático | Sí | string | Usa uno de los siguientes métodos HTTP: "GET", "POST", "PUT", "DELETE", "PATCH" o "HEAD". | 
| uri | Sí| string | El punto de conexión HTTP o HTTPS que el desencadenador comprueba. Tamaño máximo de la cadena: 2 KB | 
| Consultas | Sin  | Objeto | Representa los parámetros de consulta que se van a incluir en la dirección URL. <p>Por ejemplo, `"queries": { "api-version": "2015-02-01" }` agrega `?api-version=2015-02-01` a la dirección URL. | 
| encabezados | Sin  | Objeto | Representa cada encabezado que se envía en la solicitud. <p>Por ejemplo, para establecer el idioma y el tipo en una solicitud: <p>`"headers": { "Accept-Language": "en-us", "Content-Type": "application/json" }` | 
| Cuerpo | Sin  | Objeto | Representa la carga útil que se envía al punto de conexión. | 
| retryPolicy | Sin  | Objeto | Utilice este objeto para personalizar el comportamiento de reintento para errores 4xx o 5xx. Para más información, consulte [Directivas de reintentos](../logic-apps/logic-apps-exception-handling.md). | 
| operationsOptions | Sin  | string | Define el conjunto de comportamientos especiales para invalidar. | 
| Autenticación | Sin  | Objeto | Representa el método que debe usar la solicitud para autenticación. Para más información, consulte [Autenticación saliente de Scheduler](../scheduler/scheduler-outbound-authentication.md). <p>Aparte de Scheduler, hay una propiedad más que se admite: `authority`. De forma predeterminada, este valor es `https://login.windows.net` cuando no se especifica, pero se puede usar un valor diferente, como `https://login.windows\-ppe.net`. | 
||||| 

Las acciones HTTP y las acciones APIConnection admiten *directivas de reintentos*. Una directiva de reintentos se aplica a errores intermitentes, caracterizados como códigos de estado HTTP 408, 429 y 5xx, además de excepciones de conectividad. Puede definir esta directiva con el objeto `retryPolicy` tal y como se muestra aquí:
  
```json
"retryPolicy": {
    "type": "<retry-policy-type>",
    "interval": <retry-interval>,
    "count": <number-of-retry-attempts>
}
```

Esta acción HTTP de ejemplo reintenta la obtención de las últimas noticias dos veces si hay errores intermitentes para tres ejecuciones en total y con un retraso de 30 segundos entre cada intento:
  
```json
"myLatestNewsAction": {
    "type": "Http",
    "inputs": {
        "method": "GET",
        "uri": "https://mynews.example.com/latest",
        "retryPolicy": {
            "type": "fixed",
            "interval": "PT30S",
            "count": 2
        }
    }
}
```

El intervalo de reintento se especifica en [formato ISO 8601](https://en.wikipedia.org/wiki/ISO_8601). Este intervalo tiene un valor predeterminado y mínimo de 20 segundos, mientras que el valor máximo es una hora. El número de reintentos predeterminado y máximo es cuatro horas. Si no se especifica una definición de directiva de reintentos, se usa una estrategia `fixed` con valores predeterminados de recuento e intervalo de reintentos. Para deshabilitar la directiva de reintentos, establezca su tipo en `None`.

### <a name="asynchronous-patterns"></a>Patrones asincrónicos

De forma predeterminada, todas las acciones basadas en HTTP admiten el patrón estándar de operación asincrónica. Por tanto, si el servidor remoto indica que se acepta la solicitud para el procesamiento con una respuesta "202 ACEPTADO", el motor de Logic Apps sigue sondeando la dirección URL especificada en el encabezado de ubicación de la respuesta hasta alcanzar un estado terminal, que es una respuesta no 202.
  
Para deshabilitar el comportamiento asincrónico descrito anteriormente, establezca `operationOptions` en `DisableAsyncPattern` en las entradas de la acción. En este caso, la salida de la acción se basa en la respuesta 202 inicial desde el servidor. Por ejemplo: 
  
```json
"invokeLongRunningOperationAction": {
    "type": "Http",
    "inputs": {
        "method": "POST",
        "uri": "https://host.example.com/resources"
    },
    "operationOptions": "DisableAsyncPattern"
}
```

<a name="asynchronous-limits"></a>

#### <a name="asynchronous-limits"></a>Límites asincrónicos

Puede limitar la duración de un patrón asincrónico a un intervalo de tiempo específico. Si el intervalo de tiempo transcurre sin que se alcance un estado terminal, el estado de la acción se marca como `Cancelled` con un código de `ActionTimedOut`. El tiempo de espera límite se especifica en formato ISO 8601. En este ejemplo se muestra cómo se pueden especificar límites:


``` json
"<action-name>": {
    "type": "Workflow|Webhook|Http|ApiConnectionWebhook|ApiConnection",
    "inputs": { },
    "limit": {
        "timeout": "PT10S"
    }
}
```
  
## <a name="apiconnection-action"></a>Acción APIConnection

Esta acción hace referencia a un conector administrado por Microsoft, que requiere una referencia a una conexión válida e información sobre la API y los parámetros. Este es un ejemplo de acción APIConnection:

```json
"Send_Email": {
    "type": "ApiConnection",
    "inputs": {
        "host": {
            "api": {
                "runtimeUrl": "https://logic-apis-df.azure-apim.net/apim/office365"
            },
            "connection": {
                "name": "@parameters('$connections')['office365']['connectionId']"
            }
        },
        "method": "POST",
        "body": {
            "Subject": "New tweet from @{triggerBody()['TweetedBy']}",
            "Body": "@{triggerBody()['TweetText']}",
            "To": "me@example.com"
        },
        "path": "/Mail"
    },
    "runAfter": {}
}
```

| Nombre del elemento | Obligatorio | type | DESCRIPCIÓN | 
| ------------ | -------- | ---- | ----------- | 
| host | Sí | Objeto | Representa la información del conector, como `runtimeUrl` y la referencia al objeto connection. | 
| estático | Sí | string | Usa uno de los siguientes métodos HTTP: "GET", "POST", "PUT", "DELETE", "PATCH" o "HEAD". | 
| path | Sí | string | La ruta de acceso para la operación de API. | 
| Consultas | Sin  | Objeto | Representa los parámetros de consulta que se van a incluir en la dirección URL. <p>Por ejemplo, `"queries": { "api-version": "2015-02-01" }` agrega `?api-version=2015-02-01` a la dirección URL. | 
| encabezados | Sin  | Objeto | Representa cada encabezado que se envía en la solicitud. <p>Por ejemplo, para establecer el idioma y el tipo en una solicitud: <p>`"headers": { "Accept-Language": "en-us", "Content-Type": "application/json" }` | 
| Cuerpo | Sin  | Objeto | Representa la carga útil que se envía al punto de conexión. | 
| retryPolicy | Sin  | Objeto | Utilice este objeto para personalizar el comportamiento de reintento para errores 4xx o 5xx. Para más información, consulte [Directivas de reintentos](../logic-apps/logic-apps-exception-handling.md). | 
| operationsOptions | Sin  | string | Define el conjunto de comportamientos especiales para invalidar. | 
| Autenticación | Sin  | Objeto | Representa el método que debe usar la solicitud para autenticación. Para más información, consulte [Autenticación saliente de Scheduler](../scheduler/scheduler-outbound-authentication.md). |
||||| 

Una directiva de reintentos se aplica a errores intermitentes, caracterizados como códigos de estado HTTP 408, 429 y 5xx, además de excepciones de conectividad. Puede definir esta directiva con el objeto `retryPolicy` tal y como se muestra aquí:

```json
"retryPolicy": {
    "type": "<retry-policy-type>",
    "interval": <retry-interval>,
    "count": <number-of-retry-attempts>
}
```

## <a name="apiconnection-webhook-action"></a>Acción APIConnectionWebhook

La acción APIConnectionWebhook hace referencia a un conector administrado por Microsoft. Esta acción requiere una referencia a una conexión válida e información sobre la API y los parámetros. Puede especificar límites sobre una acción de webhook de la misma manera que los [límites asincrónicos de HTTP](#asynchronous-limits).

```json
"Send_approval_email": {
    "type": "ApiConnectionWebhook",
    "inputs": {
        "host": {
            "api": {
                "runtimeUrl": "https://logic-apis-df.azure-apim.net/apim/office365"
            },
            "connection": {
                "name": "@parameters('$connections')['office365']['connectionId']"
            }
        },
        "body": {
            "Message": {
                "Subject": "Approval Request",
                "Options": "Approve, Reject",
                "Importance": "Normal",
                "To": "me@email.com"
            }
        },
        "path": "/approvalmail",
        "authentication": "@parameters('$authentication')"
    },
    "runAfter": {}
}
```

| Nombre del elemento | Obligatorio | type | DESCRIPCIÓN | 
| ------------ | -------- | ---- | ----------- | 
| host | Sí | Objeto | Representa la información del conector, como `runtimeUrl` y la referencia al objeto connection. | 
| path | Sí | string | La ruta de acceso para la operación de API. | 
| Consultas | Sin  | Objeto | Representa los parámetros de consulta que se van a incluir en la dirección URL. <p>Por ejemplo, `"queries": { "api-version": "2015-02-01" }` agrega `?api-version=2015-02-01` a la dirección URL. | 
| encabezados | Sin  | Objeto | Representa cada encabezado que se envía en la solicitud. <p>Por ejemplo, para establecer el idioma y el tipo en una solicitud: <p>`"headers": { "Accept-Language": "en-us", "Content-Type": "application/json" }` | 
| Cuerpo | Sin  | Objeto | Representa la carga útil que se envía al punto de conexión. | 
| retryPolicy | Sin  | Objeto | Utilice este objeto para personalizar el comportamiento de reintento para errores 4xx o 5xx. Para más información, consulte [Directivas de reintentos](../logic-apps/logic-apps-exception-handling.md). | 
| operationsOptions | Sin  | string | Define el conjunto de comportamientos especiales para invalidar. | 
| Autenticación | Sin  | Objeto | Representa el método que debe usar la solicitud para autenticación. Para más información, consulte [Autenticación saliente de Scheduler](../scheduler/scheduler-outbound-authentication.md). |
||||| 

## <a name="response-action"></a>Acción de respuesta  

Esta acción contiene la carga útil de respuesta completa de una solicitud HTTP e incluye `statusCode`, `body` y `headers`:
  
```json
"myResponseAction": {
    "type": "Response",
    "inputs": {
        "statusCode": 200,
        "body": {
            "contentFieldOne": "value100",
            "anotherField": 10.001
        },
        "headers": {
            "x-ms-date": "@utcnow()",
            "Content-type": "application/json"
        }
    },
    "runAfter": {}
}
```

La acción de respuesta tiene restricciones especiales que no son aplicables a otras acciones, en concreto:  
  
* No puede tener acciones de respuesta en bifurcaciones paralelas dentro de una definición de aplicación lógica porque la solicitud entrante requiere una respuesta determinista.
  
* Si el flujo de trabajo alcanza una acción de respuesta después de que la solicitud de entrada ya haya recibido una respuesta, la acción de respuesta se considera errónea o en conflicto. Como resultado, la ejecución de la aplicación lógica se marca como `Failed`.
  
* Un flujo de trabajo con acciones de respuesta no puede utilizar el comando `splitOn` en la definición del desencadenador porque la llamada crea varias ejecuciones. Como resultado, busque este caso cuando la operación de flujo de trabajo sea PUT y devuelva una respuesta "solicitud incorrecta".

## <a name="compose-action"></a>Acción Compose

Esta acción le permite construir un objeto arbitrario y la salida es el resultado de la evaluación de las entradas de la acción. 

> [!NOTE]
> Puede utilizar la acción `Compose` para construir cualquier salida, como objetos, matrices y cualquier otro tipo compatible de forma nativa con aplicaciones lógicas, como XML y binario.

Por ejemplo, puede usar la acción `Compose` para combinar los resultados de varias acciones:

```json
"composeUserRecordAction": {
    "type": "Compose",
    "inputs": {
        "firstName": "@actions('getUser').firstName",
        "alias": "@actions('getUser').alias",
        "thumbnailLink": "@actions('lookupThumbnail').url"
    }
}
```

## <a name="function-action"></a>Acción de la función

Esta acción le permite representar una [función de Azure](../azure-functions/functions-overview.md) y llamarla, por ejemplo:

```json
"<my-Azure-Function-name>": {
   "type": "Function",
    "inputs": {
        "function": {
            "id": "/subscriptions/<Azure-subscription-ID>/resourceGroups/<Azure-resource-group-name>/providers/Microsoft.Web/sites/<your-Azure-function-app-name>/functions/<your-Azure-function-name>"
        },
        "queries": {
            "extrafield": "specialValue"
        },  
        "headers": {
            "x-ms-date": "@utcnow()"
        },
        "method": "POST",
        "body": {
            "contentFieldOne": "value100",
            "anotherField": 10.001
        }
    },
    "runAfter": {}
}
```

| Nombre del elemento | Obligatorio | type | DESCRIPCIÓN | 
| ------------ | -------- | ---- | ----------- |  
| function id | Sí | string | El identificador de recurso de la función de Azure que quiere llamar. | 
| estático | Sin  | string | El método HTTP que se usa para llamar a la función. Si no se especifica, "POST" es el método predeterminado. | 
| Consultas | Sin  | Objeto | Representa los parámetros de consulta que se van a incluir en la dirección URL. <p>Por ejemplo, `"queries": { "api-version": "2015-02-01" }` agrega `?api-version=2015-02-01` a la dirección URL. | 
| encabezados | Sin  | Objeto | Representa cada encabezado que se envía en la solicitud. <p>Por ejemplo, para establecer el idioma y el tipo en una solicitud: <p>`"headers": { "Accept-Language": "en-us", "Content-Type": "application/json" }` | 
| Cuerpo | Sin  | Objeto | Representa la carga útil que se envía al punto de conexión. | 
|||||

Cuando se guarda la aplicación lógica, el motor de Logic Apps realiza algunas comprobaciones en la función a la que se hace referencia:

* Debe tener acceso a la función.
* Solo puede usar un desencadenador HTTP estándar o webhook JSON genérico.
* La función no debe tener ninguna ruta definida.
* Solo se permiten los niveles de autorización "function" y "anonymous".

> [!NOTE]
> El motor de Logic Apps recupera y almacena en caché la dirección URL del desencadenador, que se utiliza en tiempo de ejecución. Por tanto, si una operación invalida la dirección URL almacenada en caché, se produce un error en tiempo de ejecución en la acción. Para resolver este problema, vuelva a guardar la aplicación lógica, lo que hará que recupere y almacene en caché la dirección URL de desencadenador.

## <a name="select-action"></a>Acción Select

Esta acción permite proyectar cada elemento de una matriz en un nuevo valor. En este ejemplo, se puede convertir una matriz de números en una matriz de objetos:

```json
"selectNumbersAction": {
    "type": "Select",
    "inputs": {
        "from": [ 1, 3, 0, 5, 4, 2 ],
        "select": { "number": "@item()" }
    }
}
```

| NOMBRE | Obligatorio | type | DESCRIPCIÓN | 
| ---- | -------- | ---- | ----------- | 
| De | Sí | Matriz | La matriz de origen |
| select | Sí | Cualquiera | Proyección aplicada a cada elemento de la matriz de origen |
||||| 

La salida de la acción `select` es una matriz que tiene la misma cardinalidad que la matriz de entrada. Cada elemento se transforma según se define en la propiedad `select`. Si la entrada es una matriz vacía, el resultado también es una matriz vacía.

## <a name="terminate-action"></a>Acción Terminate

Esta acción detiene una ejecución de flujo de trabajo, cancelando cualquier acción en curso y omitiendo las acciones restantes. La acción de terminación no afecta a las acciones ya finalizadas.

Por ejemplo, para detener una ejecución que tiene el estado `Failed`:

```json
"HandleUnexpectedResponse": {
    "type": "Terminate",
    "inputs": {
        "runStatus": "Failed",
        "runError": {
            "code": "UnexpectedResponse",
            "message": "Received an unexpected response",
        }
    }
}
```

| NOMBRE | Obligatorio | type | DESCRIPCIÓN | 
| ---- | -------- | ---- | ----------- | 
| runStatus | Sí | string | El estado de la ejecución de destino, que puede ser `Failed` o`Cancelled` |
| runError | Sin  | Objeto | Los detalles del error. Compatible sólo cuando `runStatus` se establece en `Failed`. |
| runError code | Sin  | string | El código de error de la ejecución |
| runError message | Sin  | string | El mensaje de error de la ejecución | 
||||| 

## <a name="query-action"></a>Acción de consulta

Esta acción le permite filtrar una matriz en función de una condición. 

> [!NOTE]
> No se puede usar la acción Compose para construir cualquier salida, como objetos, matrices y cualquier otro tipo compatible de forma nativa con aplicaciones lógicas, como XML y binario.

Por ejemplo, para seleccionar un número mayor que 2:

```json
"filterNumbersAction": {
    "type": "Query",
    "inputs": {
        "from": [ 1, 3, 0, 5, 4, 2 ],
        "where": "@greater(item(), 2)"
    }
}
```

| NOMBRE | Obligatorio | type | DESCRIPCIÓN | 
| ---- | -------- | ---- | ----------- | 
| De | Sí | Matriz | La matriz de origen |
| donde | Sí | string | La condición que se aplica a cada elemento de la matriz de origen. Si ningún valor satisface la condición `where`, el resultado es una matriz vacía. |
||||| 

La salida de la acción `query` es una matriz que contiene elementos de la matriz de entrada que satisfacen la condición.

## <a name="table-action"></a>Acción Table

Esta acción permite convertir una matriz de elementos en una tabla CSV o HTML. 

```json
"ConvertToTable": {
    "type": "Table",
    "inputs": {
        "from": "<source-array>",
        "format": "CSV | HTML"
    }
}
```

| NOMBRE | Obligatorio | type | DESCRIPCIÓN | 
| ---- | -------- | ---- | ----------- | 
| De | Sí | Matriz | La matriz de origen. Si el valor de la propiedad `from` es una matriz vacía, el resultado es una tabla vacía. | 
| formato | Sí | string | El formato de tabla que desea, ya sea "CSV" o "HTML" | 
| columnas | Sin  | Matriz | Las columnas de tabla que desea. Se usa para invalidar la forma de tabla predeterminada. | 
| column header | Sin  | string | El encabezado de columna | 
| column value | Sí | string | El valor de columna | 
||||| 

Imagine que define una acción de tabla como en este ejemplo:

```json
"convertToTableAction": {
    "type": "Table",
    "inputs": {
        "from": "@triggerBody()",
        "format": "HTML"
    }
}
```

Y utiliza esta matriz para `@triggerBody()`:

```json
[ {"ID": 0, "Name": "apples"},{"ID": 1, "Name": "oranges"} ]
```

Este es el resultado de este ejemplo:

<table><thead><tr><th>ID</th><th>NOMBRE</th></tr></thead><tbody><tr><td>0</td><td>apples</td></tr><tr><td>1</td><td>oranges</td></tr></tbody></table>

Para personalizar esta tabla, puede especificar las columnas de forma explícita, por ejemplo:

```json
"ConvertToTableAction": {
    "type": "Table",
    "inputs": {
        "from": "@triggerBody()",
        "format": "html",
        "columns": [ 
            {
                "header": "Produce ID",
                "value": "@item().id"
            },
            {
              "header": "Description",
              "value": "@concat('fresh ', item().name)"
            }
        ]
    }
}
```

Este es el resultado de este ejemplo:

<table><thead><tr><th>Generar identificador</th><th>DESCRIPCIÓN</th></tr></thead><tbody><tr><td>0</td><td>fresh apples</td></tr><tr><td>1</td><td>fresh oranges</td></tr></tbody></table>

## <a name="wait-action"></a>Acción Wait  

Esta acción suspende la ejecución del flujo de trabajo para el intervalo especificado. Este ejemplo hace que el flujo de trabajo espere 15 minutos:
  
```json
"waitForFifteenMinutesAction": {
    "type": "Wait",
    "inputs": {
        "interval": {
            "unit": "minute",
            "count": 15
        }
    }
}
```
  
Como alternativa, para esperar hasta un momento específico en el tiempo, puede usar este ejemplo:
  
```json
"waitUntilOctoberAction": {
    "type": "Wait",
    "inputs": {
        "until": {
            "timestamp": "2017-10-01T00:00:00Z"
        }
    }
}
```
  
> [!NOTE]  
> Se puede especificar la duración de espera con el objeto `interval` o el objeto `until`, pero no ambos.

| Nombre del elemento | Obligatorio | type | DESCRIPCIÓN | 
| ------------ | -------- | ---- | ----------- | 
| until | Sin  | Objeto | La duración de espera se basa en un punto en el tiempo. | 
| until timestamp | Sí | string | El punto en el tiempo en [formato de fecha y hora UTC](https://en.wikipedia.org/wiki/Coordinated_Universal_Time) cuando la espera expira. | 
| interval | Sin  | Objeto | La duración de espera se basa en la unidad del intervalo y recuento. | 
| interval unit | Sí | string | La unidad de tiempo. Use solo uno de estos valores: "second", "minute", "hour", "day", "week" o "month" | 
| interval count | Sí | Entero | Un entero positivo que representa el número de unidades de intervalo que se usan para la duración de espera. | 
||||| 

## <a name="workflow-action"></a>Acción Workflow

Esta acción le permite anidar un flujo de trabajo. El motor de Logic Apps realiza una comprobación de acceso en el flujo de trabajo secundario, o más concretamente, en el desencadenador, lo que significa que debe tener acceso al flujo de trabajo secundario. Por ejemplo: 

```json
"<my-nested-workflow-action-name>": {
    "type": "Workflow",
    "inputs": {
        "host": {
            "id": "/subscriptions/<my-subscription-ID>/resourceGroups/<my-resource-group-name>/providers/Microsoft.Logic/<my-nested-workflow-action-name>",
            "triggerName": "mytrigger001"
        },
        "queries": {
            "extrafield": "specialValue"
        },  
        "headers": {
            "x-ms-date": "@utcnow()",
            "Content-type": "application/json"
        },
        "body": {
            "contentFieldOne": "value100",
            "anotherField": 10.001
        }
    },
    "runAfter": {}
}
```

| Nombre del elemento | Obligatorio | type | DESCRIPCIÓN | 
| ------------ | -------- | ---- | ----------- |  
| host id | Sí | string| El identificador de recurso del flujo de trabajo que desea llamar | 
| host triggerName | Sí | string | El nombre del desencadenador que desea invocar | 
| Consultas | Sin  | Objeto | Representa los parámetros de consulta que se van a incluir en la dirección URL. <p>Por ejemplo, `"queries": { "api-version": "2015-02-01" }` agrega `?api-version=2015-02-01` a la dirección URL. | 
| encabezados | Sin  | Objeto | Representa cada encabezado que se envía en la solicitud. <p>Por ejemplo, para establecer el idioma y el tipo en una solicitud: <p>`"headers": { "Accept-Language": "en-us", "Content-Type": "application/json" }` | 
| Cuerpo | Sin  | Objeto | Representa la carga útil que se envía al punto de conexión. | 
||||| 

Las salidas de esta acción se basan en lo que define en la acción `Response` en el flujo de trabajo secundario. Si el flujo de trabajo secundario no se define una acción `Response`, los resultados están vacíos.

## <a name="collection-actions-overview"></a>Información general de las acciones de colección

Para ayudarle a controlar la ejecución del flujo de trabajo, se pueden incluir más acciones en la colección. Puede hacer referencia directamente a las acciones de referencia de una colección fuera de la colección. Por ejemplo, si define una acción `Http` en un ámbito, `@body('http')` sigue teniendo validez en cualquier lugar del flujo de trabajo. Además, las acciones de una colección pueden solo "ejecutarse después" de otras acciones en la misma colección.

## <a name="if-action"></a>Acción If

Esta acción, que es una instrucción condicional, le permite evaluar una condición y ejecutar una rama en función de si la expresión se evalúa como true. Si la condición se evalúa correctamente como true, la condición se marca como "Succeeded". Las acciones que se encuentran en los objetos `actions` o `else` se evalúan en función de estos valores:

* "Succeeded" cuando se ejecutan y lo hacen correctamente
* "Failed" cuando se ejecutan pero no lo hacen correctamente
* "Skipped" cuando la rama correspondiente no se ejecuta

Obtenga más información sobre [instrucciones condicionales en las aplicaciones lógicas](../logic-apps/logic-apps-control-flow-conditional-statement.md).

``` json
"<my-condition-name>": {
  "type": "If",
  "expression": "<condition>",
  "actions": {
    "if-true-run-this-action": {
      "type": <action-type>,
      "inputs": {},
      "runAfter": {}
    }
  },
  "else": {
    "actions": {
        "if-false-run-this-action": {
            "type": <action-type>,
            "inputs": {},
            "runAfter": {}
        }
    }
  },
  "runAfter": {}
}
```

| NOMBRE | Obligatorio | type | DESCRIPCIÓN | 
| ---- | -------- | ---- | ----------- | 
| actions | Sí | Objeto | Las acciones internas que se ejecutan cuando `expression` se evalúa como `true` | 
| expresión | Sí | string | La expresión para evaluar. |
| else | Sin  | Objeto | Las acciones internas que se ejecutan cuando `expression` se evalúa como `false` |
||||| 

Por ejemplo: 

```json
"myCondition": {
    "type": "If",
    "actions": {
        "if-true-check-this-website": {
            "type": "Http",
            "inputs": {
                "method": "GET",
                "uri": "http://this-url"
            },
            "runAfter": {}
        }
    },
    "else": {
        "actions": {
            "if-false-check-this-other-website": {
                "type": "Http",
                "inputs": {
                    "method": "GET",
                    "uri": "http://this-other-url"
                },
                "runAfter": {}
            }
        }
    }
}
```  

### <a name="how-conditions-can-use-expressions-in-actions"></a>Cómo pueden las condiciones usar expresiones en acciones

A continuación se muestran algunos ejemplos de cómo puede usar expresiones en condiciones:
  
| Expresión JSON | Resultado | 
| --------------- | ------ | 
| `"expression": "@parameters('hasSpecialAction')"` | Cualquier valor que se evalúa como true hace que se supere esta condición. Solo admite expresiones booleanas. Para convertir otros tipos a Boolean, use las funciones `empty` o `equals` | 
| `"expression": "@greater(actions('action1').output.value, parameters('threshold'))"` | Admite funciones de comparación. En este ejemplo, la acción se ejecuta solo cuando la salida de la acción 1 es mayor que el valor del umbral. | 
| `"expression": "@or(greater(actions('action1').output.value, parameters('threshold')), less(actions('action1').output.value, 100))"` | Admite las funciones lógicas para crear expresiones booleanas anidadas. En este ejemplo, la acción se ejecuta solo cuando la salida de la acción 1 es mayor que el valor del umbral o es menor que 100. | 
| `"expression": "@equals(length(actions('action1').outputs.errors), 0))"` | Para comprobar si una matriz tiene elementos, puede usar funciones de matriz. En este ejemplo, la acción se ejecuta cuando la matriz de errores está vacía. | 
| `"expression": "parameters('hasSpecialAction')"` | Esta expresión produce un error y no es una condición válida. Las condiciones deben usar el símbolo "@". | 
||| 

## <a name="switch-action"></a>Acción Switch

Esta acción, que es una instrucción de conmutación, lleva a cabo diferentes acciones basadas en valores específicos de un objeto, una expresión o un token. Esta acción evalúa el objeto, la expresión o el token, elige el caso de que coincida con el resultado y ejecuta acciones solo para ese caso. Cuando ningún caso coincide con el resultado, se ejecuta la acción predeterminada. Cuando se ejecuta la instrucción de conmutación, solo un caso debe coincidir con el resultado. Obtenga más información sobre [instrucciones de conmutación en las aplicaciones lógicas](../logic-apps/logic-apps-control-flow-switch-statement.md).

``` json
"<my-switch-statement-name>": {
   "type": "Switch",
   "expression": "<evaluate-this-object-expression-token>",
   "cases": {
      "myCase1" : {
         "actions" : {
           "myAction1": {}
         },
         "case": "<result1>"
      },
      "myCase2": {
         "actions" : {
           "myAction2": {}
         },
         "case": "<result2>"
      }
   },
   "default": {
      "actions": {
          "myDefaultAction": {}
      }
   },
   "runAfter": {}
}
```

| NOMBRE | Obligatorio | type | DESCRIPCIÓN | 
| ---- | -------- | ---- | ----------- | 
| expresión | Sí | string | El objeto, expresión o token para evaluar. | 
| cases | Sí | Objeto | Contiene los conjuntos de acciones internas que se ejecutan en función del resultado de la expresión. | 
| case | Sí | string | El valor que debe coincidir con el resultado. | 
| actions | Sí | Objeto | Las acciones internas que se ejecutan para el caso que coincide con el resultado de la expresión. | 
| default | Sin  | Objeto | Las acciones internas que se ejecutan cuando no hay casos que coincidan con el resultado. | 
||||| 

Por ejemplo: 

``` json
"myApprovalEmailAction": {
   "type": "Switch",
   "expression": "@body('Send_approval_email')?['SelectedOption']",
   "cases": {
      "Case": {
         "actions" : {
           "Send_an_email": {...}
         },
         "case": "Approve"
      },
      "Case_2": {
         "actions" : {
           "Send_an_email_2": {...}
         },
         "case": "Reject"
      }
   },
   "default": {
      "actions": {}
   },
   "runAfter": {
      "Send_approval_email": [
         "Succeeded"
      ]
   }
}
```

## <a name="foreach-action"></a>Acción Foreach

Esta acción de bucle recorre en iteración una matriz y realiza acciones internas en cada elemento de la matriz. De forma predeterminada, el bucle Foreach se ejecuta en paralelo. Para consultar el número máximo de ciclos paralelos que los bucles "para cada uno" pueden ejecutar, consulte [Límites y configuración](../logic-apps/logic-apps-limits-and-config.md). Para ejecutar cada ciclo de forma secuencial, establezca el parámetro `operationOptions` en `Sequential`. Obtenga más información sobre [los bucles Foreach en las aplicaciones lógicas](../logic-apps/logic-apps-control-flow-loops.md#foreach-loop).

```json
"<my-forEach-loop-name>": {
    "type": "Foreach",
    "actions": {
        "myInnerAction1": {
            "type": "<action-type>",
            "inputs": {}
        },
        "myInnerAction2": {
            "type": "<action-type>",
            "inputs": {}
        }
    },
    "foreach": "<array>",
    "runAfter": {}
}
```

| NOMBRE | Obligatorio | type | DESCRIPCIÓN | 
| ---- | -------- | ---- | ----------- | 
| actions | Sí | Objeto | Las acciones internas que se van a ejecutar dentro del bucle | 
| foreach | Sí | string | La matriz para recorrer en iteración | 
| operationOptions | Sin  | string | Especifica las opciones de la operación para personalizar el comportamiento. Actualmente solo admite `Sequential` para ejecutar secuencialmente iteraciones donde el comportamiento predeterminado es paralelo. |
||||| 

Por ejemplo: 

```json
"forEach_EmailAction": {
    "type": "Foreach",
    "foreach": "@body('email_filter')",
    "actions": {
        "Send_email": {
            "type": "ApiConnection",
            "inputs": {
                "body": {
                    "to": "@item()",
                    "from": "me@contoso.com",
                    "message": "Hello, thank you for ordering"
                },
                "host": {
                    "connection": {
                        "id": "@parameters('$connections')['office365']['connection']['id']"
                    }
                }
            }
        }
    },
    "foreach": "@body('email_filter')",
    "runAfter": {
        "email_filter": [ "Succeeded" ]
    }
}
```

## <a name="until-action"></a>Acción Until

Esta acción de bucle ejecuta acciones internas hasta que una condición se evalúa como true. Obtenga más información sobre [los bucles "hasta que" en las aplicaciones lógicas](../logic-apps/logic-apps-control-flow-loops.md#until-loop).

```json
 "<my-Until-loop-name>": {
    "type": "Until",
    "actions": {
        "myActionName": {
            "type": "<action-type>",
            "inputs": {},
            "runAfter": {}
        }
    },
    "expression": "<myCondition>",
    "limit": {
        "count": 1000,
        "timeout": "PT1H"
    },
    "runAfter": {}
}
```

| NOMBRE | Obligatorio | type | DESCRIPCIÓN | 
| ---- | -------- | ---- | ----------- | 
| actions | Sí | Objeto | Las acciones internas que se van a ejecutar dentro del bucle | 
| expresión | Sí | string | La expresión para evaluar después de cada iteración. | 
| limit | Sí | Objeto | Los límites del bucle. Debe definir al menos un límite. | 
| count | Sin  | Entero | El límite del número de iteraciones que se desea realizar | 
| timeout | Sin  | string | El límite de tiempo de espera en [formato ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) que especifica cuánto tiempo se debe ejecutar el bucle |
||||| 

Por ejemplo: 

```json
 "runUntilSucceededAction": {
    "type": "Until",
    "actions": {
        "Http": {
            "type": "Http",
            "inputs": {
                "method": "GET",
                "uri": "http://myurl"
            },
            "runAfter": {}
        }
    },
    "expression": "@equals(outputs('Http')['statusCode', 200)",
    "limit": {
        "count": 100,
        "timeout": "PT1H"
    },
    "runAfter": {}
}
```

## <a name="scope-action"></a>Acción Scope

Esta acción le permite agrupar de forma lógica las acciones de un flujo de trabajo. El ámbito también obtiene su propio estado después de que todos las acciones de ese ámbito acaben de ejecutarse. Obtenga más información sobre los [ámbitos](../logic-apps/logic-apps-control-flow-run-steps-group-scopes.md).

```json
"<my-scope-action-name>": {
    "type": "Scope",
    "actions": {
        "myInnerAction1": {
            "type": "<action-type>",
            "inputs": {}
        },
        "myInnerAction2": {
            "type": "<action-type>",
            "inputs": {}
        }
    }
}
```

| NOMBRE | Obligatorio | type | DESCRIPCIÓN | 
| ---- | -------- | ---- | ----------- |  
| actions | Sí | Objeto | Las acciones internas que se van a ejecutar dentro del ámbito interno |
||||| 

## <a name="next-steps"></a>Pasos siguientes

* Obtenga más información sobre el [lenguaje de definición de flujo de trabajo](../logic-apps/logic-apps-workflow-definition-language.md).
* Obtenga más información sobre la [API REST de flujo de trabajo](https://docs.microsoft.com/rest/api/logic/workflows).