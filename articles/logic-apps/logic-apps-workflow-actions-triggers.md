---
title: 'Desencadenadores y acciones de flujo de trabajo: Azure Logic Apps | Microsoft Docs'
description: "Obtener información sobre los tipos de desencadenadores y acciones que puede usar para crear y automatizar flujos de trabajo y procesos con Azure Logic Apps"
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
ms.date: 11/17/2016
ms.author: LADocs; mandia
ms.openlocfilehash: 9f95c0c486401e0d709829ce8d560f030932eea7
ms.sourcegitcommit: 62eaa376437687de4ef2e325ac3d7e195d158f9f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/22/2017
---
# <a name="triggers-and-actions-for-logic-app-workflows"></a>Desencadenadores y acciones para flujos de trabajo de aplicación lógica

Todas las aplicaciones lógicas se inician con un desencadenador seguido de acciones. En este tema se describen los tipos de desencadenadores y acciones que puede usar para crear aplicaciones integradas del sistema y automatizar procesos o flujos de trabajo empresariales mediante la creación de aplicaciones lógicas. 
  
## <a name="triggers-overview"></a>Introducción a los desencadenadores 

Todas las aplicaciones lógicas se inician con un desencadenador, que especifica las llamadas que pueden iniciar la ejecución de una aplicación lógica. Estas son las dos formas con las que puede iniciar una ejecución de su flujo de trabajo:  

* Un desencadenador de sondeo  
* Un desencadenador de push, que llamada a la [API de REST del servicio de flujo de trabajo](https://docs.microsoft.com/rest/api/logic/workflows)  
  
Todos los desencadenadores contienen estos elementos de nivel superior:  
  
```json
"trigger-name": {
    "type": "trigger-type",
    "inputs": { call-settings },
    "recurrence": {  
        "frequency": "Second|Minute|Hour|Day|Week|Month",
        "interval": recurrence-interval-based-on-frequency
    },
    "conditions": [ array-of-required-conditions ],
    "splitOn": "property-used-for-creating-separate-workflows",
    "operationOptions": "operation-options-for-trigger"
}
```

### <a name="trigger-types-and-inputs"></a>Tipos y entradas de desencadenadores  

Cada tipo de desencadenador tiene una interfaz diferente y distintas *entradas* que definen su comportamiento. 

| Tipo de desencadenador | Descripción | 
| ------------ | ----------- | 
| **Periodicidad** | Se desencadena en función de una programación definida. Puede establecer una fecha y hora futuras para activar este desencadenador. Según la frecuencia, también puede especificar horas y días para ejecutar el flujo de trabajo. | 
| **Solicitud**  | Transforma la aplicación lógica en un punto de conexión que se puede llamar, también conocido como desencadenador "manual". | 
| **HTTP** | Comprueba o *sondea* un punto de conexión web HTTP. El punto de conexión HTTP debe ajustarse a un contrato de desencadenamiento específico, ya sea mediante un patrón asincrónico "202" o devolviendo una matriz. | 
| **ApiConnection** | Sondea al igual que un desencadenador HTTP, pero usa [API administradas por Microsoft](../connectors/apis-list.md). | 
| **HTTPWebhook** | Transforma la aplicación lógica en un punto de conexión que se puede llamar, como el desencadenador Solicitud, pero llama a una dirección URL especificada para registrar y anular el registro. |
| **ApiConnectionWebhook** | Funciona igual que el desencadenador **HTTPWebhook**, pero usa las API administrada por Microsoft. | 
||| 

Para información sobre otros detalles, consulte [Esquema del lenguaje de definición de flujo de trabajo - Azure Logic Apps](../logic-apps/logic-apps-workflow-definition-language.md). 
  
## <a name="recurrence-trigger"></a>Desencadenador de periodicidad  

Este desencadenador se ejecuta según la periodicidad y programación que especifique y proporciona una manera sencilla de ejecutar con regularidad un flujo de trabajo. Este es un ejemplo de desencadenador de periodicidad básico que se ejecuta a diario:

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
        "startTime" : "2017-09-18T00:00:00Z"
    }
}
```

Esta es la definición para este desencadenador: 

```json
"myRecurrenceTrigger": {
    "type": "Recurrence",
    "recurrence": {
        "frequency": "second|minute|hour|day|week|month",
        "interval": recurrence-interval-based-on-frequency,
        "schedule": {
            // Applies only when frequency is Day or Week. Separate values with commas.
            "hours": [ one-or-more-hour-marks ], 
            // Applies only when frequency is Day or Week. Separate values with commas.
            "minutes": [ one-or-more-minute-marks ], 
            // Applies only when frequency is Week. Separate values with commas.
            "weekDays": [ "Monday, Tuesday, Wednesday, Thursday, Friday, Saturday, Sunday" ] 
        },
        "startTime": "start-date-time-with-format-YYYY-MM-DDThh:mm:ss",
        "timeZone": "specify-time-zone"
    }
}
```

| Nombre del elemento | Obligatorio | Tipo | Descripción | 
| ------------ | -------- | ---- | ----------- | 
| frequency | Sí | String | La unidad de tiempo para la frecuencia con la que se activa el desencadenador. Use solo uno de estos valores: "second", "minute", "hour", "day", "week" o "month" | 
| interval | Sí | Entero | Entero positivo que describe la frecuencia con la que se ejecuta el flujo de trabajo. <p>Estos son los intervalos mínimo y máximo: <p>- Month: 1-16 meses </br>- Day: 1-500 días </br>- Hour: 1-12 000 horas </br>- Minute: 1-72 000 minutos </br>- Second: 1-9 999 999 segundos<p>Por ejemplo, si el intervalo es 6 y la frecuencia es "month", la periodicidad es cada 6 meses. | 
| timeZone | No | String | Solo se aplica cuando se especifica una hora de inicio porque este desencadenador no acepta [diferencia horaria con UTC](https://en.wikipedia.org/wiki/UTC_offset). Especifique la zona horaria que desea aplicar. | 
| startTime | No | String | Especifique la fecha y hora de inicio en este formato: <p>AAAA-MM-DDThh:mm:ss si especifica una zona horaria <p>O bien <p>AAAA-MM-DDThh:mm:ssZ si no especifica una zona horaria <p>Por ejemplo, si desea la fecha del 18 de septiembre de 2017 a las 2:00 p.m., especifique entonces "2017-09-18T14:00:00" y especifique una zona horaria como "Hora estándar del Pacífico". O bien, especifique "2017-09-18T14:00:00Z" sin una zona horaria. <p>**Nota:** Esta hora de inicio debe seguir la [especificación de fecha y hora ISO 8601](https://en.wikipedia.org/wiki/ISO_8601#Combined_date_and_time_representations) en [formato de hora y fecha UTC](https://en.wikipedia.org/wiki/Coordinated_Universal_Time), pero sin una [diferencia horaria con UTC](https://en.wikipedia.org/wiki/UTC_offset). Si no se especifica una zona horaria, debe agregar la letra "Z" al final sin espacios. Esta "Z" se refiere al equivalente de [hora náutica](https://en.wikipedia.org/wiki/Nautical_time). <p>Para las programaciones simples, la hora de inicio es la primera aparición, mientras que para programaciones complejas, el desencadenador no se activa antes de la hora de inicio. Para más información sobre las fechas y horas de inicio, consulte [Introducción al desencadenador de periodicidad](../connectors/connectors-native-recurrence.md). | 
| weekDays | No | Cadena o matriz de cadenas | Si especifica "Week" para `frequency`, puede especificar uno o varios días, separados por comas, cuando desee ejecutar el flujo de trabajo: "Monday", "Tuesday", "Wednesday", "Thursday", "Friday", "Saturday" y "Sunday" | 
| hours | No | Entero o matriz de enteros | Si especifica "Day" o "Semana" para `frequency`, puede especificar uno o varios enteros de 0 a 23, separados por comas, como las horas del día en las que desea ejecutar el flujo de trabajo. <p>Por ejemplo, si especifica "10", "12" y "14", obtendrá 10 a. m., 12 p. m. y 2 p. m. como las marcas de hora. | 
| minutes | No | Entero o matriz de enteros | Si especifica "Day" o "Semana" para `frequency`, puede especificar uno o varios enteros de 0 a 59, separados por comas, como los minutos de la hora en los que desea ejecutar el flujo de trabajo. <p>Por ejemplo, puede especificar "30" como la marca de minuto y, utilizando el ejemplo anterior para las horas del día, obtendrá 10:30 a.m., 12:30 p.m. y las 2:30 p.m. | 
|||||| 

Por ejemplo, este desencadenador de periodicidad especifica que la aplicación lógica se ejecuta semanalmente todos los lunes a las 10:30 a.m., a las 12:30 p.m. y a las 2:30 p.m. en hora estándar del Pacífico, y no empieza antes del 9 de septiembre de 2017 a las 2:00 p.m.:

``` json
{
    "triggers": {
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

Este desencadenador tiene una propiedad opcional denominada *schema*:
  
| Nombre del elemento | Obligatorio | Tipo | Descripción |
| ------------ | -------- | ---- | ----------- |
| schema | No | Objeto | Un esquema JSON que valida la solicitud entrante. Resulta útil para ayudar a los pasos de flujo de trabajo subsiguientes a los que las propiedades hacen referencia. | 
||||| 

Para invocar este punto de conexión, debe llamar a la API *listCallbackUrl*. Consulte [API de REST de servicio de flujo de trabajo](https://docs.microsoft.com/rest/api/logic/workflows).

## <a name="http-trigger"></a>Desencadenador HTTP  

Los desencadenadores HTTP sondean un punto de conexión especificado y comprueban la respuesta para determinar si se debe ejecutar el flujo de trabajo. En este caso, el objeto `inputs` toma estos parámetros necesarios para construir una llamada HTTP:  

| Nombre del elemento | Obligatorio | Tipo | Descripción | 
| ------------ | -------- | ---- | ----------- | 
| estático | Sí | String | Usa uno de los siguientes métodos HTTP: "GET", "POST", "PUT", "DELETE", "PATCH" o "HEAD". | 
| uri | Sí| String | El punto de conexión HTTP o HTTPS que el desencadenador comprueba. Tamaño máximo de la cadena: 2 KB | 
| Consultas | No | Objeto | Representa los parámetros de consulta que se van a incluir en la dirección URL. <p>Por ejemplo, `"queries": { "api-version": "2015-02-01" }` agrega `?api-version=2015-02-01` a la dirección URL. | 
| encabezados | No | Objeto | Representa cada encabezado que se envía en la solicitud. <p>Por ejemplo, para establecer el idioma y el tipo en una solicitud: <p>`"headers": { "Accept-Language": "en-us", "Content-Type": "application/json" }` | 
| body | No | Objeto | Representa la carga útil que se envía al punto de conexión. | 
| retryPolicy | No | Objeto | Utilice este objeto para personalizar el comportamiento de reintento para errores 4xx o 5xx. Para más información, consulte [Directivas de reintentos](../logic-apps/logic-apps-exception-handling.md). | 
| Autenticación | No | Objeto | Representa el método que debe usar la solicitud para autenticación. Para más información, consulte [Autenticación saliente de Scheduler](../scheduler/scheduler-outbound-authentication.md). <p>Aparte de Scheduler, hay una propiedad más que se admite: `authority`. De forma predeterminada, este valor es `https://login.windows.net` cuando no se especifica, pero se puede usar un valor diferente, como `https://login.windows\-ppe.net`. | 
||||| 
 
Para que funcione bien con la aplicación lógica, el desencadenador HTTP necesita la API de HTTP para ajustarse a un patrón específico. El desencadenador reconoce estas propiedades:  
  
| Respuesta | Obligatorio | Descripción | 
| -------- | -------- | ----------- |  
| Código de estado | Sí | El código de estado 200 ("Correcto") provoca una ejecución. Cualquier otro código de estado no provoca una ejecución. | 
| Encabezado Retry-after | No | Número de segundos hasta que la aplicación lógica sondea de nuevo el punto de conexión. | 
| Encabezado Location | No | La dirección URL para llamar en el siguiente intervalo de sondeo. Si no se especifica, se usa la dirección URL original. | 
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
  
| Nombre del elemento | Tipo | Descripción |
| ------------ | ---- | ----------- |
| encabezados | Objeto | Los encabezados de la respuesta HTTP | 
| body | Objeto | El cuerpo de la respuesta HTTP | 
|||| 

## <a name="api-connection-trigger"></a>Desencadenador de conexión de API  

El desencadenador de conexión de API es similar al desencadenador HTTP en su funcionalidad básica. Sin embargo, los parámetros para identificar la acción son diferentes. Este es un ejemplo:  
  
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
        "category": "awesomest"
    }
}
```

| Nombre del elemento | Obligatorio | Tipo | Descripción | 
| ------------ | -------- | ---- | ----------- | 
| host | Sí | Objeto | La puerta de enlace hospedada y el identificador de la aplicación API | 
| estático | Sí | String | Usa uno de los siguientes métodos HTTP: "GET", "POST", "PUT", "DELETE", "PATCH" o "HEAD". | 
| Consultas | No | Objeto | Representa los parámetros de consulta que se van a incluir en la dirección URL. <p>Por ejemplo, `"queries": { "api-version": "2015-02-01" }` agrega `?api-version=2015-02-01` a la dirección URL. | 
| encabezados | No | Objeto | Representa cada encabezado que se envía en la solicitud. <p>Por ejemplo, para establecer el idioma y el tipo en una solicitud: <p>`"headers": { "Accept-Language": "en-us", "Content-Type": "application/json" }` | 
| body | No | Objeto | Representa la carga útil que se envía al punto de conexión. | 
| retryPolicy | No | Objeto | Utilice este objeto para personalizar el comportamiento de reintento para errores 4xx o 5xx. Para más información, consulte [Directivas de reintentos](../logic-apps/logic-apps-exception-handling.md). | 
| Autenticación | No | Objeto | Representa el método que debe usar la solicitud para autenticación. Para más información, consulte [Autenticación saliente de Scheduler](../scheduler/scheduler-outbound-authentication.md). | 
||||| 

Para el objeto `host`, estas son las propiedades:  
  
| Nombre del elemento | Obligatorio | Descripción | 
| ------------ | -------- | ----------- | 
| api runtimeUrl | Sí | Punto de conexión de la API administrada | 
| connection name |  | Nombre de la conexión de API administrada que utiliza el flujo de trabajo. Debe hacer referencia a un parámetro denominado `$connection`. |
|||| 

Estas son las salidas para un desencadenador de conexión de API:
  
| Nombre del elemento | Tipo | Descripción |
| ------------ | ---- | ----------- |
| encabezados | Objeto | Los encabezados de la respuesta HTTP | 
| body | Objeto | El cuerpo de la respuesta HTTP | 
|||| 
  
## <a name="httpwebhook-trigger"></a>Desencadenador HTTPWebhook  

El desencadenador HTTPWebhook proporciona un punto de conexión, similar al desencadenador Solicitud, pero también llama a una dirección URL especificada para registrar y anular el registro. Este es un ejemplo del aspecto que podría tener un desencadenador HTTPWebhook:  

```json
"myAppsSpotTrigger": {
    "type": "HttpWebhook",
    "inputs": {
        "subscribe": {
            "method": "POST",
            "uri": "https://pubsubhubbub.appspot.com/subscribe",
            "headers": { },
            "body": {
                "hub.callback": "@{listCallbackUrl()}",
                "hub.mode": "subscribe",
                "hub.topic": "https://pubsubhubbub.appspot.com/articleCategories/technology"
            },
            "authentication": { },
            "retryPolicy": { }
        },
        "unsubscribe": {
            "url": "https://pubsubhubbub.appspot.com/subscribe",
            "body": {
                "hub.callback": "@{workflow().endpoint}@{listCallbackUrl()}",
                "hub.mode": "unsubscribe",
                "hub.topic": "https://pubsubhubbub.appspot.com/articleCategories/technology"
            },
            "method": "POST",
            "authentication": { }
        }
    },
    "conditions": [ ]
}
```

Muchas de estas secciones son opcionales y el comportamiento del desencadenador HTTPWebhook depende de las secciones que se proporcionan u omiten. Estas son las propiedades del desencadenador HTTPWebhook:
  
| Nombre del elemento | Obligatorio | Descripción | 
| ------------ | -------- | ----------- |  
| subscribe | No | Especifica la solicitud saliente a la que se llama cuando se crea el desencadenador y realiza el registro inicial. | 
| unsubscribe | No | Especifica la solicitud saliente para llamar cuando se elimina el desencadenador. | 
|||| 

Puede especificar límites sobre una acción de webhook de la misma manera que los [límites asincrónicos de HTTP](#asynchronous-limits). Aquí tiene más información sobre las acciones `subscribe` y `unsubscribe`:

* `subscribe`se llama para que el desencadenador pueda comenzar a escuchar eventos. Esta llamada saliente se inicia con los mismos parámetros como acciones HTTP estándar. Esta llamada tiene lugar cuando el flujo de trabajo cambia de algún modo; por ejemplo, cada vez que se sustituyen las credenciales o cambian los parámetros de entrada del desencadenador. 
  
  Para admitir esta llamada, la función `@listCallbackUrl()` devuelve una dirección URL única para este desencadenador específico en este flujo de trabajo. Esta dirección URL representa el identificador único de los puntos de conexión que usan la API de REST del servicio.
  
* `unsubscribe` se llama automáticamente cuando una operación representa este desencadenador como no válido, lo que incluye estas operaciones:

  * Eliminar o deshabilitar el desencadenador. 
  * Eliminar o deshabilitar el flujo de trabajo. 
  * Eliminar o deshabilitar la suscripción. 
  
  Los parámetros de esta función son los mismos que los del desencadenador HTTP.

Estas son las salidas del desencadenador HTTPWebhook y el contenido de la solicitud entrante:
  
| Nombre del elemento | Tipo | Descripción |
| ------------ | ---- | ----------- |
| encabezados | Objeto | Los encabezados de la respuesta HTTP | 
| body | Objeto | El cuerpo de la respuesta HTTP | 
|||| 

## <a name="conditions"></a>Condiciones  

Para cualquier desencadenador, puede usar una o varias condiciones para determinar si el flujo de trabajo debe ejecutarse o no. Por ejemplo:  

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

En este caso, el informe solo se desencadena mientras el parámetro `sendReports` del flujo de trabajo está establecido en true. Por último, las condiciones pueden hacer referencia al código de estado del desencadenador. Por ejemplo, puede iniciar un flujo de trabajo solo cuando su sitio web devuelva un código de estado 500, por ejemplo:
  
``` json
"conditions": [ 
    {  
      "expression": "@equals(triggers().code, 'InternalServerError')"  
    }  
]  
```  
  
> [!NOTE]  
> Cuando alguna expresión hace referencia al código de estado del desencadenador de algún modo, se sustituye el comportamiento predeterminado, que es desencadenar solo en 200 "Correcto". Por ejemplo, si quiere desencadenar en el código de estado 200 y el código de estado 201, debe incluir: `@or(equals(triggers().code, 200),equals(triggers().code,201))` como condición.
  
## <a name="start-multiple-runs-for-a-request"></a>Inicio de varias ejecuciones para una solicitud

Para iniciar varias ejecuciones para una única solicitud, es útil `splitOn`; por ejemplo, cuando quiera sondear un punto de conexión que puede tener varios elementos nuevos entre intervalos de sondeo.
  
Con `splitOn`, especifica la propiedad dentro de la carga útil de respuesta que contiene la matriz de elementos, cada uno de los cuales querrá usar para iniciar una ejecución del desencadenador. Por ejemplo, imagine que tiene una API que devuelve la esta respuesta:  
  
```json
{
    "status": "Succeeded",
    "rows": [
        {  
            "id" : 938109380,
            "name" : "myFirstRow"
        },
        {
            "id" : 938109381,
            "name" : "mySecondRow"
        }
    ]
}
```
  
La aplicación lógica solo necesita el contenido de `rows`, por lo que puede construir el desencadenador como en este ejemplo:  

```json
"mySplitterTrigger": {
    "type": "Http",
    "recurrence": {
        "frequency": "minute",
        "interval": 1
    },
    "intputs": {
        "uri": "https://mydomain.com/myAPI",
        "method": "GET"
    },
    "splitOn": "@triggerBody()?.rows"
}
```
> [!NOTE]  
> Si usa el comando `SplitOn`, no puede obtener las propiedades que están fuera de la matriz, por lo que para este ejemplo, no puede obtener la propiedad `status` en la respuesta que se devuelve de la API.
> Asimismo, en este ejemplo, usamos el operador `?`, de manera que podemos evitar un error si la propiedad `rows` no existe. 

Por tanto, en la definición del flujo de trabajo, `@triggerBody().name` devuelve `myFirstRow` en la primera ejecución y `mySecondRow` en la segunda. Las salidas del desencadenador se parecen a las de este ejemplo:  

```json
{
    "body": {
        "id": 938109380,
        "name": "mySecondRow"
    }
}
```

```json
{
    "body": {
        "id": 938109381,
        "name": "mySecondRow"
    }
}
```
  
## <a name="single-run-instance"></a>Instancia de ejecución única

Puede configurar desencadenadores de periodicidad para que se activen solo cuando se hayan completado todas las ejecuciones activas. Si tiene lugar una periodicidad programada mientras se está ejecutando una instancia del flujo de trabajo, el desencadenador la omite y espera hasta el siguiente intervalo de periodicidad programada para volver a comprobarlo.
Para definir esta configuración, establezca la propiedad `operationOptions` en `singleInstance`:

```json
"triggers": {
    "myHTTPTrigger": {
        "type": "Http",
        "inputs": { ... },
        "recurrence": { ... },
        "operationOptions": "singleInstance"
    }
}
```

## <a name="actions-overview"></a>Información general sobre acciones

Hay muchos tipos de acciones, cada una con un comportamiento único. Cada tipo de acción tiene diferentes entradas que definen el comportamiento de una acción. Las acciones de colección pueden contener muchas otras acciones dentro de sí mismas. 

### <a name="standard-actions"></a>Acciones estándar  

| Tipo de acción | Descripción | 
| ----------- | ----------- | 
| **HTTP** | Llama a un punto de conexión web HTTP. | 
| **ApiConnection**  | Funciona igual que la acción HTTP, pero usa [API administradas por Microsoft](https://docs.microsoft.com/azure/connectors/apis-list). | 
| **ApiConnectionWebhook** | Funciona igual que HTTPWebhook, pero usa API administradas por Microsoft. | 
| **Respuesta** | Define la respuesta para una llamada entrante. | 
| **Function** | Representa una función de Azure. | 
| **Wait** | Espera una cantidad fija de tiempo o hasta una hora específica. | 
| **Workflow** | Representa un flujo de trabajo anidado. | 
| **Compose** | Construye un objeto arbitrario a partir de entradas de la acción. | 
| **Consultar** | Filtra una matriz basada en una condición. | 
| **Selección** | Proyecta cada elemento de una matriz en un nuevo valor. Por ejemplo, puede convertir una matriz de números en una matriz de objetos. | 
| **Table** | Convierte una matriz de elementos en una tabla CSV o HTML. | 
| **Terminate** | Detiene la ejecución de un flujo de trabajo. | 
||| 

### <a name="collection-actions"></a>Acciones de colección

| Tipo de acción | Descripción | 
| ----------- | ----------- | 
| **Condition** | Evalúa una expresión y en función del resultado, ejecuta la bifurcación correspondiente. | 
| **Ámbito** | Se usa para agrupar lógicamente otras acciones. | 
| **ForEach** | Esta acción de bucle recorre en iteración una matriz y realiza acciones internas en cada elemento de la matriz. | 
| **Until** | Esta acción de bucle realiza acciones internas hasta que una condición da como resultado true. | 
||| 

## <a name="http-action"></a>Acción HTTP  

Las acciones HTTP llaman a un punto de conexión especificado y comprueban la respuesta para determinar si se debe ejecutar el flujo de trabajo. Por ejemplo:
  
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

| Nombre del elemento | Obligatorio | Tipo | Descripción | 
| ------------ | -------- | ---- | ----------- | 
| estático | Sí | String | Usa uno de los siguientes métodos HTTP: "GET", "POST", "PUT", "DELETE", "PATCH" o "HEAD". | 
| uri | Sí| String | El punto de conexión HTTP o HTTPS que el desencadenador comprueba. Tamaño máximo de la cadena: 2 KB | 
| Consultas | No | Objeto | Representa los parámetros de consulta que se van a incluir en la dirección URL. <p>Por ejemplo, `"queries": { "api-version": "2015-02-01" }` agrega `?api-version=2015-02-01` a la dirección URL. | 
| encabezados | No | Objeto | Representa cada encabezado que se envía en la solicitud. <p>Por ejemplo, para establecer el idioma y el tipo en una solicitud: <p>`"headers": { "Accept-Language": "en-us", "Content-Type": "application/json" }` | 
| body | No | Objeto | Representa la carga útil que se envía al punto de conexión. | 
| retryPolicy | No | Objeto | Utilice este objeto para personalizar el comportamiento de reintento para errores 4xx o 5xx. Para más información, consulte [Directivas de reintentos](../logic-apps/logic-apps-exception-handling.md). | 
| operationsOptions | No | string | Define el conjunto de comportamientos especiales para invalidar. | 
| authentication | No | Objeto | Representa el método que debe usar la solicitud para autenticación. Para más información, consulte [Autenticación saliente de Scheduler](../scheduler/scheduler-outbound-authentication.md). <p>Aparte de Scheduler, hay una propiedad más que se admite: `authority`. De forma predeterminada, este valor es `https://login.windows.net` cuando no se especifica, pero se puede usar un valor diferente, como `https://login.windows\-ppe.net`. | 
||||| 

Esta acción HTTP de ejemplo reintenta la obtención de las últimas noticias dos veces si hay errores intermitentes para tres ejecuciones en total y con un retraso de 30 segundos entre cada intento:
  
```json
"myLatestNewsAction": {
    "type": "Http",
    "inputs": {
        "method": "GET",
        "uri": "https://mynews.example.com/latest",
        "retryPolicy" : {
            "type": "fixed",
            "interval": "PT30S",
            "count": 2
        }
    }
}
```

El intervalo de reintento se especifica en [formato ISO 8601](https://en.wikipedia.org/wiki/ISO_8601). Este intervalo tiene un valor predeterminado y mínimo de 20 segundos, mientras que el valor máximo es una hora. El número de reintentos predeterminado y máximo es cuatro horas. Si no se especifica la definición de directiva de reintentos, se usa una estrategia `fixed` con valores predeterminados de recuento e intervalo de reintentos. Para deshabilitar la directiva de reintentos, establezca su tipo en `None`.

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

Puede limitar la duración de un patrón asincrónico a un intervalo de tiempo específico. Si el intervalo de tiempo transcurre sin que se alcance un estado terminal, el estado de la acción se marca como `Cancelled` con un código de `ActionTimedOut`. El tiempo de espera límite se especifica en formato ISO 8601. Puede especificar los límites como se muestra aquí:

``` json
"action-name": {
    "type": "Workflow|Webhook|Http|ApiConnectionWebhook|ApiConnection",
    "inputs": { },
    "limit": {
        "timeout": "PT10S"
    }
}
```
  
## <a name="apiconnection-action"></a>Acción APIConnection

La acción APIConnection hace referencia a un conector administrado por Microsoft. Esta acción requiere una referencia a una conexión válida e información sobre la API y los parámetros.
Este es un ejemplo de acción APIConnection:

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

| Nombre del elemento | Obligatorio | Tipo | Descripción | 
| ------------ | -------- | ---- | ----------- | 
| host | Sí | Objeto | Representa la información del conector, como `runtimeUrl` y la referencia al objeto connection. | 
| estático | Sí | String | Usa uno de los siguientes métodos HTTP: "GET", "POST", "PUT", "DELETE", "PATCH" o "HEAD". | 
| path | Sí | String | La ruta de acceso para la operación de API. | 
| Consultas | No | Objeto | Representa los parámetros de consulta que se van a incluir en la dirección URL. <p>Por ejemplo, `"queries": { "api-version": "2015-02-01" }` agrega `?api-version=2015-02-01` a la dirección URL. | 
| encabezados | No | Objeto | Representa cada encabezado que se envía en la solicitud. <p>Por ejemplo, para establecer el idioma y el tipo en una solicitud: <p>`"headers": { "Accept-Language": "en-us", "Content-Type": "application/json" }` | 
| body | No | Objeto | Representa la carga útil que se envía al punto de conexión. | 
| retryPolicy | No | Objeto | Utilice este objeto para personalizar el comportamiento de reintento para errores 4xx o 5xx. Para más información, consulte [Directivas de reintentos](../logic-apps/logic-apps-exception-handling.md). | 
| operationsOptions | No | string | Define el conjunto de comportamientos especiales para invalidar. | 
| authentication | No | Objeto | Representa el método que debe usar la solicitud para autenticación. Para más información, consulte [Autenticación saliente de Scheduler](../scheduler/scheduler-outbound-authentication.md). |
||||| 

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

| Nombre del elemento | Obligatorio | Tipo | Descripción | 
| ------------ | -------- | ---- | ----------- | 
| host | Sí | Objeto | Representa la información del conector, como `runtimeUrl` y la referencia al objeto connection. | 
| path | Sí | String | La ruta de acceso para la operación de API. | 
| Consultas | No | Objeto | Representa los parámetros de consulta que se van a incluir en la dirección URL. <p>Por ejemplo, `"queries": { "api-version": "2015-02-01" }` agrega `?api-version=2015-02-01` a la dirección URL. | 
| encabezados | No | Objeto | Representa cada encabezado que se envía en la solicitud. <p>Por ejemplo, para establecer el idioma y el tipo en una solicitud: <p>`"headers": { "Accept-Language": "en-us", "Content-Type": "application/json" }` | 
| body | No | Objeto | Representa la carga útil que se envía al punto de conexión. | 
| retryPolicy | No | Objeto | Utilice este objeto para personalizar el comportamiento de reintento para errores 4xx o 5xx. Para más información, consulte [Directivas de reintentos](../logic-apps/logic-apps-exception-handling.md). | 
| operationsOptions | No | string | Define el conjunto de comportamientos especiales para invalidar. | 
| authentication | No | Objeto | Representa el método que debe usar la solicitud para autenticación. Para más información, consulte [Autenticación saliente de Scheduler](../scheduler/scheduler-outbound-authentication.md). |
||||| 

## <a name="response-action"></a>Acción de respuesta  

Esta acción contiene la carga útil de respuesta completa de una solicitud HTTP e incluye `statusCode`, `body` y `headers`:
  
```json
"myResponseAction": {
    "type": "response",
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

## <a name="function-action"></a>Acción de la función   

Esta acción le permite representar una [función de Azure](../azure-functions/functions-overview.md) y llamarla, por ejemplo:

```json
"my-Azure-Function-name": {
   "type": "Function",
    "inputs": {
        "function": {
            "id": "/subscriptions/{Azure-subscription-ID}/resourceGroups/{Azure-resource-group}/providers/Microsoft.Web/sites/{your-Azure-function-app-name}/functions/{your-Azure-function-name}"
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
| Nombre del elemento | Obligatorio | Tipo | Descripción | 
| ------------ | -------- | ---- | ----------- |  
| function id | Sí | String | El identificador de recurso de la función de Azure que quiere llamar. | 
| estático | No | String | El método HTTP que se usa para llamar a la función. Si no se especifica, "POST" es el método predeterminado. | 
| Consultas | No | Objeto | Representa los parámetros de consulta que se van a incluir en la dirección URL. <p>Por ejemplo, `"queries": { "api-version": "2015-02-01" }` agrega `?api-version=2015-02-01` a la dirección URL. | 
| encabezados | No | Objeto | Representa cada encabezado que se envía en la solicitud. <p>Por ejemplo, para establecer el idioma y el tipo en una solicitud: <p>`"headers": { "Accept-Language": "en-us", "Content-Type": "application/json" }` | 
| body | No | Objeto | Representa la carga útil que se envía al punto de conexión. | 
|||||

Cuando se guarda la aplicación lógica, Azure Logic Apps realiza comprobaciones en la función a la que se hace referencia:

* Debe tener acceso a la función.
* Solo puede usar desencadenadores HTTP estándar o webhook JSON genéricos.
* La función no debe tener ninguna ruta definida.
* Solo se permite el nivel de autorización "function" y "anonymous".

La dirección URL de desencadenador se recupera, se almacena en la memoria caché y se usa en tiempo de ejecución. Por tanto, si una operación invalida la dirección URL almacenada en caché, se produce un error en tiempo de ejecución en la acción. Para resolver este problema, vuelva a guardar la aplicación lógica, lo que hará que recupere y almacene en caché la dirección URL de desencadenador.

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
> La duración de espera puede especificarse mediante el objeto `until`o el objeto `interval`, pero no ambos.
  
| Nombre del elemento | Obligatorio | Tipo | Descripción | 
| ------------ | -------- | ---- | ----------- | 
| until | No | Objeto | La duración de espera se basa en un punto en el tiempo. | 
| until timestamp | Sí | String | El punto en el tiempo en [formato de fecha y hora UTC](https://en.wikipedia.org/wiki/Coordinated_Universal_Time) cuando la espera expira. | 
| interval | No | Objeto | La duración de espera se basa en la unidad del intervalo y recuento. | 
| interval unit | Sí | String | La unidad de tiempo. Use solo uno de estos valores: "second", "minute", "hour", "day", "week" o "month" | 
| interval count | Sí | Entero | Un entero positivo que representa el número de unidades de intervalo que se usan para la duración de espera. | 
||||| 

## <a name="workflow-action"></a>Acción Workflow   

Esta acción representa otro flujo de trabajo. Logic Apps realiza una comprobación de acceso en el flujo de trabajo, o más concretamente, en el desencadenador, lo que significa que debe tener acceso al flujo de trabajo.

Las salidas de esta acción se basan en lo que define en la acción `response` en el flujo de trabajo secundario. Si no ha definido una acción `response`, las salidas estarán vacías.

```json
"myNestedWorkflowAction": {
    "type": "Workflow",
    "inputs": {
        "host": {
            "id": "/subscriptions/xxxxyyyyzzz/resourceGroups/rg001/providers/Microsoft.Logic/mywf001",
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

| Nombre del elemento | Obligatorio | Tipo | Descripción | 
| ------------ | -------- | ---- | ----------- |  
| host id | Sí | String| El identificador de recurso del flujo de trabajo que desea llamar | 
| host triggerName | Sí | String | El nombre del desencadenador que desea invocar | 
| Consultas | No | Objeto | Representa los parámetros de consulta que se van a incluir en la dirección URL. <p>Por ejemplo, `"queries": { "api-version": "2015-02-01" }` agrega `?api-version=2015-02-01` a la dirección URL. | 
| encabezados | No | Objeto | Representa cada encabezado que se envía en la solicitud. <p>Por ejemplo, para establecer el idioma y el tipo en una solicitud: <p>`"headers": { "Accept-Language": "en-us", "Content-Type": "application/json" }` | 
| body | No | Objeto | Representa la carga útil que se envía al punto de conexión. | 
|||||   

## <a name="compose-action"></a>Acción Compose

Esta acción le permite construir un objeto arbitrario y la salida es el resultado de la evaluación de las entradas de la acción. 

> [!NOTE]
> Puede utilizar la acción `Compose` para construir cualquier salida, como objetos, matrices y cualquier otro tipo compatible de forma nativa con aplicaciones lógicas, como XML y binario.

Por ejemplo, puede usar la acción de composición para combinar los resultados de varias acciones:

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

## <a name="select-action"></a>Acción Select

Esta acción permite proyectar cada elemento de una matriz en un nuevo valor.
Por ejemplo, para convertir una matriz de números en una matriz de objetos, puede usar:

```json
"selectNumbersAction": {
    "type": "Select",
    "inputs": {
        "from": [ 1, 3, 0, 5, 4, 2 ],
        "select": { "number": "@item()" }
    }
}
```

| Nombre | Obligatorio | Tipo | Descripción | 
| ---- | -------- | ---- | ----------- | 
| De | Sí | Matriz | La matriz de origen |
| select | Sí | Cualquiera | Proyección aplicada a cada elemento de la matriz de origen |
||||| 

La salida de la acción `select` es una matriz que tiene la misma cardinalidad que la matriz de entrada. Cada elemento se transforma según se define en la propiedad `select`. Si la entrada es una matriz vacía, el resultado también es una matriz vacía.

## <a name="query-action"></a>Acción de consulta

Esta acción le permite filtrar una matriz en función de una condición. En este ejemplo se seleccionan números superiores a dos:

```json
"filterNumbersAction": {
    "type": "Query",
    "inputs": {
        "from": [ 1, 3, 0, 5, 4, 2 ],
        "where": "@greater(item(), 2)"
    }
}
```

La salida de la acción `query` es una matriz que contiene elementos de la matriz de entrada que satisfacen la condición.

> [!NOTE]
> Si ningún valor satisface la condición `where`, el resultado es una matriz vacía.

| Nombre | Obligatorio | Tipo | Descripción | 
| ---- | -------- | ---- | ----------- | 
| De | Sí | Matriz | La matriz de origen |
| donde | Sí | String | La condición que se aplica a cada elemento de la matriz de origen |
||||| 

## <a name="table-action"></a>Acción Table

Esta acción permite convertir una matriz de elementos en una tabla **CSV** o **HTML**. Por ejemplo, suponga que tiene `@triggerBody()` con esta matriz:

```json
[ 
    {
      "id": 0,
      "name": "apples"
    },
    {
      "id": 1, 
      "name": "oranges"
    }
]
```

Y define una acción de tabla como en este ejemplo:

```json
"convertToTableAction": {
    "type": "Table",
    "inputs": {
        "from": "@triggerBody()",
        "format": "html"
    }
}
```

El resultado de este ejemplo es similar a esta tabla HTML: 

<table><thead><tr><th>id</th><th>name</th></tr></thead><tbody><tr><td>0</td><td>apples</td></tr><tr><td>1</td><td>oranges</td></tr></tbody></table>

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

El resultado de este ejemplo es similar a esta tabla HTML: 

<table><thead><tr><th>Generar identificador</th><th>Descripción</th></tr></thead><tbody><tr><td>0</td><td>fresh apples</td></tr><tr><td>1</td><td>fresh oranges</td></tr></tbody></table>

| Nombre | Obligatorio | Tipo | Descripción | 
| ---- | -------- | ---- | ----------- | 
| De | Sí | Matriz | La matriz de origen. Si el valor de la propiedad `from` es una matriz vacía, el resultado es una tabla vacía. | 
| formato | Sí | String | El formato de tabla que desea, ya sea **CSV** o **HTML** | 
| columnas | No | Matriz | Las columnas de tabla que desea. Se usa para invalidar la forma de tabla predeterminada. | 
| column header | No | String | El encabezado de columna | 
| column value | Sí | String | El valor de columna | 
||||| 

## <a name="terminate-action"></a>Acción Terminate

Esta acción detiene la ejecución de flujo de trabajo, cancela cualquier acción en curso y omite todas las acciones restantes. La acción Terminate no afecta a las acciones finalizadas.

Por ejemplo, para detener una ejecución con el estado "Erróneo", puede utilizar este ejemplo:

```json
"handleUnexpectedResponseAction": {
    "type": "Terminate",
    "inputs": {
        "runStatus": "Failed",
        "runError": {
            "code": "UnexpectedResponse",
            "message": "Received an unexpected response"
        }
    }
}
```

| Nombre | Obligatorio | Tipo | Descripción | 
| ---- | -------- | ---- | ----------- | 
| runStatus | Sí | String | El estado de la ejecución de destino, que puede ser `Failed` o`Cancelled` |
| runError | No | Objeto | Los detalles del error. Compatible sólo cuando `runStatus` se establece en `Failed`. |
| runError code | No | String | El código de error de la ejecución |
| runError message | No | String | El mensaje de error de la ejecución |
||||| 

## <a name="collection-actions-overview"></a>Información general de las acciones de colección

Algunas acciones pueden incluir acciones dentro de ellas mismas. Se puede hacer referencia a las acciones de referencia dentro de una colección directamente fuera de la colección. Por ejemplo, si define `Http` en `scope`, entonces `@body('http')` sigue teniendo validez en cualquier lugar en el flujo de trabajo. Puede tener acciones en una colección `runAfter` solo con otras acciones en la misma colección.

## <a name="condition-if-action"></a>Condición: acción If

Esta acción le permite evaluar una condición y ejecutar una rama en función de si la expresión se evalúa como `true`. 
  
```json
"myCondition": {
    "type": "If",
    "actions": {
        "if_true": {
            "type": "Http",
            "inputs": {
                "method": "GET",
                "uri": "http://myurl"
            },
            "runAfter": {}
        }
    },
    "else": {
        "actions": {
            "if_false": {
                "type": "Http",
                "inputs": {
                    "method": "GET",
                    "uri": "http://myurl"
                },
                "runAfter": {}
            }
        }
    },
    "expression": "@equals(triggerBody(), json(true))",
    "runAfter": {}
}
``` 

| Nombre | Obligatorio | Tipo | Descripción | 
| ---- | -------- | ---- | ----------- | 
| actions | Sí | Objeto | Las acciones internas que se ejecutan cuando `expression` se evalúa como `true` | 
| expresión | Sí | String | La expresión para evaluar. |
| else | No | Objeto | Las acciones internas que se ejecutan cuando `expression` se evalúa como `false` |
||||| 

Si la condición se evalúa correctamente, la condición se marca como `Succeeded`. Las acciones en los objetos `actions` o `else` se evalúan como: 

* `Succeeded` cuando se ejecutan y lo hacen correctamente
* `Failed` cuando se ejecutan pero no lo hacen correctamente
* `Skipped` cuando la rama correspondiente no se ejecuta

A continuación se muestran ejemplos de cómo las condiciones pueden usar expresiones en una acción:
  
| Valor JSON | Resultado | 
| ---------- | -------| 
| `"expression": "@parameters('hasSpecialAction')"` | Cualquier valor que se evalúa como true hace que se supere esta condición. Solo admite expresiones booleanas. Para convertir otros tipos a Boolean, use las funciones `empty` y `equals` | 
| `"expression": "@greater(actions('act1').output.value, parameters('threshold'))"` | Admite funciones de comparación. En este ejemplo, la acción se ejecuta solo cuando la salida de `act1` es mayor que el umbral. | 
| `"expression": "@or(greater(actions('act1').output.value, parameters('threshold')), less(actions('act1').output.value, 100))"` | Admite las funciones lógicas para crear expresiones booleanas anidadas. Para este ejemplo, la acción se ejecuta cuando la salida de `act1` está por encima del umbral o por debajo de 100. | 
| `"expression": "@equals(length(actions('act1').outputs.errors), 0))"` | Para comprobar si una matriz tiene elementos, puede usar funciones de matriz. En este ejemplo, la acción se ejecuta cuando la matriz `errors` está vacía. | 
| `"expression": "parameters('hasSpecialAction')"` | Error: no es una condición válida porque se requiere @ para las condiciones. |  
|||

## <a name="scope-action"></a>Acción Scope

Esta acción le permite agrupar de forma lógica las acciones de un flujo de trabajo.

```json
"myScope": {
    "type": "Scope",
    "actions": {
        "call_bing": {
            "type": "Http",
             "inputs": {
                "url": "http://www.bing.com"
            }
        }
    }
}
```

| Nombre | Obligatorio | Tipo | Descripción | 
| ---- | -------- | ---- | ----------- |  
| actions | Sí | Objeto | Las acciones internas que se van a ejecutar dentro del ámbito interno |
||||| 

## <a name="foreach-action"></a>Acción ForEach

Esta acción de bucle recorre en iteración una matriz y realiza acciones internas en cada elemento de la matriz. De forma predeterminada, el bucle `foreach` se ejecuta en paralelo y puede ejecutar 20 ejecuciones en paralelo al mismo tiempo. Para establecer reglas de ejecución, utilice el parámetro `operationOptions`.

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
    "runAfter": {
        "email_filter": [ "Succeeded" ]
    }
}
```

| Nombre | Obligatorio | Tipo | Descripción | 
| ---- | -------- | ---- | ----------- | 
| actions | Sí | Objeto | Las acciones internas que se van a ejecutar dentro del bucle | 
| foreach | Sí | String | La matriz para recorrer en iteración | 
| operationOptions | No | String | Especifica las opciones de la operación para personalizar el comportamiento. Actualmente solo admite `Sequential` para ejecutar secuencialmente iteraciones donde el comportamiento predeterminado es paralelo. |
||||| 

## <a name="until-action"></a>Acción Until

Esta acción de bucle ejecuta acciones internas hasta que una condición da como resultado true.

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
        "count": 1000,
        "timeout": "PT1H"
    },
    "runAfter": {}
}
```

| Nombre | Obligatorio | Tipo | Descripción | 
| ---- | -------- | ---- | ----------- | 
| actions | Sí | Objeto | Las acciones internas que se van a ejecutar dentro del bucle | 
| expresión | Sí | String | La expresión para evaluar después de cada iteración. | 
| limit | Sí | Objeto | Los límites del bucle. Debe definir al menos un límite. | 
| count | No | Entero | El límite del número de iteraciones que se desea realizar | 
| timeout | No | String | El límite de tiempo de espera en [formato ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) que especifica cuánto tiempo se debe ejecutar el bucle |
||||| 

## <a name="next-steps"></a>Pasos siguientes

* [Lenguaje de definición de flujo de trabajo](../logic-apps/logic-apps-workflow-definition-language.md)
* [API de REST de flujo de trabajo](https://docs.microsoft.com/rest/api/logic/workflows)
