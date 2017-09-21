---
title: 'Acciones y desencadenadores de flujo de trabajo: Azure Logic Apps | Microsoft Docs'
description: 
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
ms.translationtype: HT
ms.sourcegitcommit: 12c20264b14a477643a4bbc1469a8d1c0941c6e6
ms.openlocfilehash: cc41bdb12cf11e60489e104af2df4dd0720dd91b
ms.contentlocale: es-es
ms.lasthandoff: 09/07/2017

---

# <a name="workflow-actions-and-triggers-for-azure-logic-apps"></a>Acciones y desencadenadores de flujo de trabajo para Azure Logic Apps

Las aplicaciones lógicas constan de desencadenadores y acciones. Existen seis tipos de desencadenadores. Cada tipo tiene una interfaz y un comportamiento diferentes. También puede aprender más examinando los detalles del [lenguaje de definición de flujo de trabajo](logic-apps-workflow-definition-language.md).  
  
Siga leyendo para aprender más sobre desencadenadores y acciones y cómo podría usarlos para crear aplicaciones lógicas con el fin de mejorar los procesos de negocio y los flujos de trabajo.  
  
### <a name="triggers"></a>Desencadenadores  

Un desencadenador especifica las llamadas que pueden iniciar una ejecución de su flujo de trabajo de aplicación lógica. Estas son las dos formas diferentes de iniciar una ejecución de su flujo de trabajo:  
  
-   Un desencadenador de sondeo  

-   Un desencadenador de push, mediante la llamada a la [API de REST del servicio de flujo de trabajo](https://docs.microsoft.com/rest/api/logic/workflows)  
  
Todos los desencadenadores contienen estos elementos de nivel superior:  
  
```json
"<name-of-the-trigger>" : {
    "type": "<type-of-trigger>",
    "inputs": { <settings-for-the-call> },
    "recurrence": {  
        "frequency": "Second|Minute|Hour|Week|Month|Year",
        "interval": "<recurrence interval in units of frequency>"
    },
    "conditions": [ <array-of-required-conditions > ],
    "splitOn" : "<property to create runs for>",
    "operationOptions": "<operation options on the trigger>"
}
```

### <a name="trigger-types-and-their-inputs"></a>Tipos de desencadenadores y sus entradas  

Puede usar estos tipos de desencadenadores:
  
-   **Solicitud**: convierte la aplicación lógica en un punto de conexión al que llamar.  
  
-   **Periodicidad**: se desencadena en función de una programación definida.  
  
-   **HTTP**: sondea un punto de conexión web HTTP. El punto de conexión HTTP debe ajustarse a un contrato de desencadenamiento específico \- mediante un patrón asincrónico 202 o la devolución de una matriz.  
  
-   **ApiConnection**: realiza sondeos como el desencadenador HTTP; sin embargo, aprovecha las ventajas de las [API administradas por Microsoft](https://docs.microsoft.com/azure/connectors/apis-list).  
  
-   **HTTPWebhook**: abre un punto de conexión, similar al desencadenador manual, pero también llama a una dirección URL especificada para registrar y anular el registro.  
  
-   **ApiConnectionWebhook**: funciona como el desencadenador HTTPWebhook al aprovechar las ventajas de las API administradas por Microsoft.       
    Cada tipo de desencadenador tiene un conjunto de **entradas** diferente que define su comportamiento.  
  
## <a name="request-trigger"></a>Desencadenador de solicitud  

Este desencadenador actúa como un punto de conexión que se llama a través de una solicitud HTTP para invocar la aplicación lógica. Un desencadenador de solicitud es similar a este ejemplo:  
  
```json
"<name-of-the-trigger>" : {
    "type" : "request",
    "kind": "http",
    "inputs" : {
        "schema" : {
            "properties" : {
                "myInputProperty1" : { "type" : "string" },
                "myInputProperty2" : { "type" : "number" }
            },
        "required" : [ "myInputProperty1" ],
        "type" : "object"
        }
    }
} 
```

También hay una propiedad opcional denominada **schema**:  
  
|Nombre del elemento|Obligatorio|Descripción|  
|----------------|------------|---------------|  
|schema|No|Un esquema JSON que valida la solicitud entrante. Resulta útil para ayudar a los pasos de flujo de trabajo subsiguientes a los que las propiedades hacen referencia.|

Para invocar este punto de conexión, debe llamar a la API *listCallbackUrl*. Consulte [API de REST de servicio de flujo de trabajo](https://docs.microsoft.com/rest/api/logic/workflows).  
  
## <a name="recurrence-trigger"></a>Desencadenador de periodicidad  

Un desencadenador de periodicidad es aquel que se ejecuta según una programación definida. Ese tipo de desencadenador podría parecerse a este ejemplo:  

```json
"dailyReport" : {
    "type": "recurrence",
    "recurrence": {
        "frequency": "Day",
        "interval": "1"
    }
}
```

Como puede ver, es una manera sencilla de ejecutar un flujo de trabajo.  
  
|Nombre del elemento|Obligatorio|Descripción|  
|----------------|------------|---------------|  
|frequency|Sí|Frecuencia con la que se ejecuta el desencadenador. Use solo uno de estos valores posibles: segundo, minuto, hora, día, semana, mes o año.|  
|interval|Sí|Intervalo de frecuencia definido para la periodicidad.|  
|startTime|No|Si se especifica una hora de inicio sin una diferencia horaria con UTC, se usará esta zona horaria.|  
|timeZone|no|Si se especifica una hora de inicio sin una diferencia horaria con UTC, se usará esta zona horaria.|  
  
También puede programar un desencadenador para que comience a ejecutarse en algún momento en el futuro. Por ejemplo, si quiere iniciar un informe semanal todos los lunes, puede programar la aplicación lógica para que se inicie todos los lunes mediante la creación del siguiente desencadenador:  

```json
"dailyReport" : {
    "type": "recurrence",
    "recurrence": {
        "frequency": "Week",
        "interval": "1",
        "startTime" : "2015-06-22T00:00:00Z"
    }
}
```

## <a name="http-trigger"></a>Desencadenador HTTP  

Los desencadenadores HTTP sondean un punto de conexión especificado y comprueban la respuesta para determinar si se debe ejecutar el flujo de trabajo. El objeto inputs toma el conjunto de parámetros necesarios para construir una llamada HTTP:  
  
|Nombre del elemento|Obligatorio|Descripción|Tipo|  
|----------------|------------|---------------|--------|  
|estático|yes|Puede ser uno de los siguientes métodos HTTP: GET, POST, PUT, DELETE, PATCH o HEAD|String|  
|uri|yes|El punto de conexión http o https al que se llama. 2 kilobytes como máximo.|String|  
|Consultas|No|Un objeto que representa los parámetros de consulta que se deben agregar a la dirección URL. Por ejemplo, `"queries" : { "api-version": "2015-02-01" }` agrega `?api-version=2015-02-01` a la dirección URL.|Objeto|  
|encabezados|No|Un objeto que representa cada uno de los encabezados que se envían a la solicitud. Por ejemplo, para establecer el idioma y el tipo en una solicitud: `"headers" : { "Accept-Language": "en-us",  "Content-Type": "application/json" }`.|Objeto|  
|body|No|Un objeto que representa la carga útil que se envía al punto de conexión.|Objeto|  
|retryPolicy|No|Un objeto que permite personalizar el comportamiento de reintento para errores 4xx o 5xx.|Objeto|  
|authentication|No|Representa el método con el que se debe autenticar la solicitud. Para más información sobre este objeto, consulte [Autenticación saliente de Scheduler](https://docs.microsoft.com/azure/scheduler/scheduler-outbound-authentication). Aparte del programador, hay una propiedad más admitida: `authority`. De forma predeterminada, este valor es `https://login.windows.net` cuando no se especifica, pero puede usar una audiencia diferente como `https://login.windows\-ppe.net`.|Objeto|  
  
El desencadenador HTTP necesita la API de HTTP para ajustarse a un patrón específico que funcione bien con la aplicación lógica. Reconoce las siguientes propiedades:  
  
|Response|Obligatorio|Descripción|  
|------------|------------|---------------|  
|Código de estado|Sí|Código de estado 200 \(Aceptar\) para provocar una ejecución. Cualquier otro código de estado no provoca una ejecución.|  
|Encabezado Retry\-after|No|Número de segundos hasta que la aplicación lógica sondea de nuevo el punto de conexión.|  
|Encabezado Location|No|La dirección URL para llamar en el siguiente intervalo de sondeo. Si no se especifica, se usa la dirección URL original.|  
  
Estos son algunos ejemplos de diferentes comportamientos para diferentes tipos de solicitudes:  
  
|Response code|Retry\-After|Comportamiento|  
|-----------------|----------------|------------|  
|200|\(Ninguna\)|Se ejecuta el flujo de trabajo y se comprueba de nuevo si hay más contenido que siga la periodicidad definida.|  
|200|10|Se ejecuta el flujo de trabajo y se comprueba de nuevo si hay más contenido en 10 segundos.|  
|202|60|No se desencadena el flujo de trabajo. El próximo intento tiene lugar en un minuto, según cuál sea la periodicidad definida. Si la periodicidad definida es inferior a un minuto, el encabezado retry-after tiene prioridad. Si no, se sigue la periodicidad definida.|  
|400|\(Ninguna\)|Solicitud incorrecta, no se ejecuta el flujo de trabajo. Si no hay ninguna **directiva de reintento** definida, se usa la directiva predeterminada. Una vez alcanzado el número de reintentos, el desencadenador volverá a comprobar si hay más contenido que siga la periodicidad definida.|  
|500|\(Ninguna\)|Error del servidor, no se ejecuta el flujo de trabajo.  Si no hay ninguna **directiva de reintento** definida, se usa la directiva predeterminada. Una vez alcanzado el número de reintentos, el desencadenador volverá a comprobar si hay más contenido que siga la periodicidad definida.|  
  
Las salidas de un desencadenador HTTP son similares a este ejemplo:  
  
|Nombre del elemento|Descripción|Tipo|  
|----------------|---------------|--------|  
|encabezados|Los encabezados de la respuesta http.|Objeto|  
|body|El cuerpo de la respuesta http.|Objeto|  
  
## <a name="api-connection-trigger"></a>Desencadenador de conexión de API  

El desencadenador de conexión de API es similar al desencadenador HTTP en su funcionalidad básica. Sin embargo, los parámetros para identificar la acción son diferentes. Este es un ejemplo:  
  
```json
"dailyReport" : {
    "type": "ApiConnection",
    "inputs": {
        "host": {
            "api": {
                "runtimeUrl": "https://myarticles.example.com/"
            },
        }
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

|Nombre del elemento|Obligatorio|Tipo|Descripción|  
|----------------|------------|--------|---------------|  
|host|Sí||La puerta de enlace y el id. de la ApiApp hospedada.|  
|estático|Sí|string|Puede ser uno de los siguientes métodos HTTP: **GET**, **POST**, **PUT**, **DELETE**, **PATCH** o **HEAD**.|  
|Consultas|No|Objeto|Representa los parámetros de consulta que se agregarán a la dirección URL. Por ejemplo, `"queries" : { "api-version": "2015-02-01" }` agrega `?api-version=2015-02-01` a la dirección URL.|  
|encabezados|No|Objeto|Representa cada uno de los encabezados que se envían a la solicitud. Por ejemplo, para establecer el idioma y el tipo en una solicitud: `"headers" : { "Accept-Language": "en-us",  "Content-Type": "application/json" }`.|  
|body|No|Objeto|Representa la carga útil que se envía al punto de conexión.|  
|retryPolicy|No|Objeto|Le permite personalizar el comportamiento de reintento para errores 4xx o 5xx.|  
|authentication|No|Objeto|Representa el método con el que se debe autenticar la solicitud. Para más información sobre este objeto, consulte [Autenticación saliente de Scheduler](https://docs.microsoft.com/azure/scheduler/scheduler-outbound-authentication).|  
  
Las propiedades del host son:  
  
|Nombre del elemento|Obligatorio|Descripción|  
|----------------|------------|---------------|  
|api runtimeUrl|Sí|El punto de conexión de la API administrada.|  
|connection name||Debe ser una referencia a un parámetro denominado `$connection` y es el nombre de la conexión de API administrada que usa el flujo de trabajo.|
  
Las salidas de un desencadenador de conexión de API son:
  
|Nombre del elemento|Tipo|Descripción|  
|----------------|--------|---------------|  
|encabezados|Objeto|Los encabezados de la respuesta http.|  
|body|Objeto|El cuerpo de la respuesta http.|  
  
## <a name="httpwebhook-trigger"></a>Desencadenador HTTPWebhook  

El desencadenador HTTPWebhook abre un punto de conexión, similar al desencadenador manual, pero también llama a una dirección URL especificada para registrar y anular el registro. Este es un ejemplo del aspecto que podría tener un desencadenador HTTPWebhook:  

```json
"myappspottrigger": {
    "type": "httpWebhook",
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

Muchas de estas secciones son opcionales y el comportamiento del webhook depende de qué secciones se proporcionen o se omitan.  
Las propiedades de un webhook son las siguientes:  
  
|Nombre del elemento|Obligatorio|Descripción|  
|----------------|------------|---------------|  
|subscribe|No|La solicitud saliente a la que se llama cuando se crea el desencadenador y se realiza el registro inicial.|  
|unsubscribe|No|La solicitud saliente cuando se elimina el desencadenador.|  
  
-   **Subscribe** es la llamada saliente que se realiza para comenzar a escuchar eventos. Esta llamada se inicia con el mismo conjunto de parámetros que las acciones HTTP normales. Esta llamada saliente se realiza cada vez que el flujo de trabajo cambia de algún modo; por ejemplo, cada vez que se sustituyen las credenciales o cambian los parámetros de entrada del desencadenador.
  
    Para admitir esta llamada, hay una nueva función: `@listCallbackUrl()`. Esta función devuelve una dirección URL única para este desencadenador específico en este flujo de trabajo. Representa el identificador único de los puntos de conexión que usan la REST del servicio.  
  
-   **Unsubscribe** se llama cuando una operación representa este desencadenador como no válido, lo que incluye:  
  
    -   Eliminar o deshabilitar el desencadenador  
  
    -   Eliminar o deshabilitar el flujo de trabajo  
  
    -   Eliminar o deshabilitar la suscripción  
  
    La aplicación lógica llama automáticamente a la acción de cancelación de la suscripción. Los parámetros de esta función son los mismos que los del desencadenador HTTP.  
  
    Las salidas del desencadenador HTTPWebhook son el contenido de la solicitud entrante:  
  
|Nombre del elemento|Tipo|Descripción|  
|-----------------|--------|---------------|  
|encabezados|Objeto|Los encabezados de la solicitud http.|  
|body|Objeto|El cuerpo de la solicitud http.|  

Se pueden especificar límites sobre una acción de webhook de la misma manera que los [límites asincrónicos de HTTP](#asynchronous-limits).
  

## <a name="conditions"></a>Condiciones  

Para cualquier desencadenador, puede usar una o varias condiciones para determinar si el flujo de trabajo debe ejecutarse o no. Por ejemplo:  

```json
"dailyReport" : {
    "type": "recurrence",
    "conditions": [ {
        "expression": "@parameters('sendReports')"
    } ],
    "recurrence": {
        "frequency": "Day",
        "interval": "1"
    }
}
```

En este caso, el informe solo se desencadena mientras el parámetro `sendReports` del flujo de trabajo está establecido en true. Por último, las condiciones pueden hacer referencia el código de estado del desencadenador. Por ejemplo, podría iniciar un flujo de trabajo solo cuando su sitio web devuelva un código de estado 500, como se indica a continuación:
  
```  
"conditions": [  
        {  
          "expression": "@equals(triggers().code, 'InternalServerError')"  
        }  
      ]  
```  
  
> [!NOTE]  
> Cuando alguna expresión hace referencia al código de estado del desencadenador \(de algún modo\), se sustituye el comportamiento predeterminado \(desencadenar solo en 200 \(Aceptar\)\). Por ejemplo, si quiere desencadenar en el código de estado 200 y el código de estado 201, debe incluir: `@or(equals(triggers().code, 200),equals(triggers().code,201))` como condición.  
  
## <a name="start-multiple-runs-for-a-request"></a>Inicio de varias ejecuciones para una solicitud

Para iniciar varias ejecuciones para una única solicitud, es útil `splitOn`; por ejemplo, cuando quiera sondear un punto de conexión que puede tener varios elementos nuevos entre intervalos de sondeo.
  
Con `splitOn`, especifica la propiedad dentro de la carga útil de respuesta que contiene la matriz de elementos, cada uno de los cuales querrá usar para iniciar una ejecución del desencadenador. Por ejemplo, imagine que tiene una API que devuelve la respuesta siguiente:  
  
```json
{
    "Status" : "success",
    "Rows" : [
        {  
            "id" : 938109380,
            "name" : "mycoolrow"
        },
        {
            "id" : 938109381,
            "name" : "another row"
        }
    ]
}
```
  
La aplicación lógica solo necesita el contenido de filas, por lo que puede construir el desencadenador como en este ejemplo:  
  
```json
"mysplitter" : {
    "type" : "http",
    "recurrence": {
        "frequency": "Minute",
        "interval": "1"
    },
    "intputs" : {
        "uri" : "https://mydomain.com/myAPI",
        "method" : "GET"
    },
    "splitOn" : "@triggerBody()?.Rows"
}
```
  
Luego, en la definición del flujo de trabajo, `@triggerBody().name` devuelve `mycoolrow` en la primera ejecución y `another row` en la segunda. Las salidas del desencadenador se parecen a las de este ejemplo:  
  
```json
{
    "body" : {
        "id" : 938109381,
        "name" : "another row"
    }
}
```

De modo que, si usa `SplitOn`, no podrá obtener las propiedades que están fuera de la matriz, en este caso, el campo `Status`.  
  
> [!NOTE]  
> En este ejemplo, usamos el operador `?` para evitar un error si la propiedad `Rows` no está presente. 
  
## <a name="single-run-instance"></a>Instancia de ejecución única

Puede configurar desencadenadores que tengan una propiedad de periodicidad para que solo se disparen si todas las ejecuciones activas se han completado. Si se produce una periodicidad programada mientras hay una ejecución en curso, el desencadenador la omite y espera hasta el siguiente intervalo de periodicidad programada para volver a comprobarlo.

Puede configurar este valor mediante las opciones de la operación:

```json
"triggers": {
    "mytrigger": {
        "type": "http",
        "inputs": { ... },
        "recurrence": { ... },
        "operationOptions": "singleInstance"
    }
}
```

## <a name="types-and-inputs"></a>Tipos y entradas  

Hay muchos tipos de acciones, cada una con un comportamiento único. Las acciones de colección pueden contener muchas otras acciones dentro de sí mismas.

### <a name="standard-actions"></a>Acciones estándar  

-   **HTTP**: esta acción llama a un punto de conexión web HTTP.  
  
-   **ApiConnection**: esta acción se comporta como la acción de HTTP, pero usa las API administradas por Microsoft.  
  
-   **ApiConnectionWebhook**: como HTTPWebhook, pero usa las API administradas por Microsoft.  
  
-   **Response**: esta acción define una respuesta para una llamada entrante.  
  
-   **Wait**: esta acción simple espera una cantidad fija de tiempo o hasta una hora específica.  
  
-   **Workflow**: esta acción representa un flujo de trabajo anidado.  

-   **Función**: esta acción representa una función de Azure.

### <a name="collection-actions"></a>Acciones de colección

-   **Scope**: esta acción es una agrupación lógica de otras acciones.

-   **Condition**: esta acción se evalúa como una expresión y ejecuta la rama de resultado correspondiente.

-   **ForEach** \-: esta acción de bucle recorre en iteración una matriz y realiza acciones internas para cada elemento.

-   **Until**: esta acción de bucle ejecuta acciones internas hasta que una condición da como resultado true.
  
Cada tipo de acción tiene un conjunto diferente de **entradas** que definen el comportamiento de la acción.  
  
## <a name="http-action"></a>Acción HTTP  

Las acciones HTTP llaman a un punto de conexión especificado y comprueban la respuesta para determinar si se debe ejecutar el flujo de trabajo. El objeto **inputs** toma el conjunto de parámetros necesarios para construir una llamada HTTP:  
  
|Nombre del elemento|Obligatorio|Tipo|Descripción|  
|----------------|------------|--------|---------------|  
|estático|Sí|string|Puede ser uno de los siguientes métodos HTTP: **GET**, **POST**, **PUT**, **DELETE**, **PATCH** o **HEAD**.|  
|uri|Sí|String|El punto de conexión http o https al que se llama. La longitud máxima es de 2 kilobytes.|  
|Consultas|No|Objeto|Representa los parámetros de consulta para agregar a la dirección URL. Por ejemplo, `"queries" : { "api-version": "2015-02-01" }` agrega `?api-version=2015-02-01` a la dirección URL.|  
|encabezados|No|Objeto|Representa cada uno de los encabezados que se envían a la solicitud. Por ejemplo, para establecer el idioma y el tipo en una solicitud: `"headers" : { "Accept-Language": "en-us",  "Content-Type": "application/json" }`.|  
|body|No|Objeto|Representa la carga útil que se envía al punto de conexión.|  
|retryPolicy|No|Objeto|Le permite personalizar el comportamiento de reintento para errores 4xx o 5xx.|  
|operationsOptions|No|string|Define el conjunto de comportamientos especiales para invalidar.|  
|authentication|No|Objeto|Representa el método con el que se debe autenticar la solicitud. Para más información sobre este objeto, consulte [Autenticación saliente de Scheduler](https://docs.microsoft.com/azure/scheduler/scheduler-outbound-authentication). Aparte de scheduler, hay una propiedad más que se admite: `authority`. De forma predeterminada, es `https://login.windows.net` cuando no se especifica, pero se puede usar una audiencia diferente como `https://login.windows\-ppe.net`.|  
  
Las acciones HTTP \(y las acciones de conexión de API\) admiten directivas de reintentos. Una directiva de reintentos se aplica a errores intermitentes, caracterizados como códigos de estado HTTP 408, 429 y 5xx, además de excepciones de conectividad. Esta directiva se describe mediante el objeto *retryPolicy* definido como se muestra aquí:
  
```json
"retryPolicy" : {
    "type": "<type-of-retry-policy>",
    "interval": <retry-interval>,
    "count": <number-of-retry-attempts>
}
```
  
El intervalo de reintento se especifica en el formato ISO 8601. Este intervalo tiene un valor predeterminado y mínimo de 20 segundos, mientras que el valor máximo es una hora. El número de reintentos predeterminado y máximo es cuatro horas. Si no se especifica la definición de directiva de reintentos, se usa una estrategia `fixed` con valores predeterminados de recuento e intervalo de reintentos. Para deshabilitar la directiva de reintentos, establezca su tipo en `None`.  
  
Por ejemplo, la siguiente acción reintenta la obtención de las últimas noticias dos veces, si hay errores intermitentes, para tres ejecuciones en total, con un retraso de 30 segundos entre cada intento:  
  
```json
"latestNews" : {
    "type": "http",
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
### <a name="asynchronous-patterns"></a>Patrones asincrónicos

De forma predeterminada, todas las acciones basadas en HTTP admiten el patrón estándar de operación asincrónica. Por tanto, si el servidor remoto indica que se acepta la solicitud para el procesamiento con una respuesta 202 \(Aceptado\), el motor de Logic Apps sigue sondeando la dirección URL especificada en el encabezado de ubicación de la respuesta hasta alcanzar un estado terminal de \(sin\-respuesta 202\).  
  
Para deshabilitar el comportamiento asincrónico descrito anteriormente, establezca una opción `DisableAsyncPattern` en las entradas de la acción. En este caso, la salida de la acción se basa en la respuesta 202 inicial desde el servidor.  
  
```json
"invokeLongRunningOperation" : {
    "type": "http",
    "inputs": {
        "method": "POST",
        "uri": "https://host.example.com/resources"
    },
    "operationOptions": "DisableAsyncPattern"
}
```

#### <a name="asynchronous-limits"></a>Límites asincrónicos

Un patrón asincrónico puede estar limitado en su duración a un intervalo de tiempo específico.  Si transcurre el intervalo de tiempo sin que se alcance un estado terminal, el estado de la acción se marca como `Cancelled` con un código de `ActionTimedOut`.  El tiempo de espera límite se especifica en formato ISO 8601.  Los límites pueden especificarse mediante la sintaxis siguiente:

``` json
"<action-name>": {
    "type": "workflow|webhook|http|apiconnectionwebhook|apiconnection",
    "inputs": { },
    "limit": {
        "timeout": "PT10S"
    }
}
```
  
## <a name="api-connection"></a>API Connection  

API Connection es una acción que hace referencia a un conector administrado por Microsoft.
Esta acción requiere una referencia a una conexión válida e información sobre la API y los parámetros necesarios.

|Nombre del elemento|Obligatorio|Tipo|Descripción|  
|----------------|------------|--------|---------------|  
|host|Sí|Objeto|Representa la información del conector, como runtimeUrl y la referencia al objeto connection.|
|estático|Sí|string|Puede ser uno de los siguientes métodos HTTP: **GET**, **POST**, **PUT**, **DELETE**, **PATCH** o **HEAD**.|  
|path|Sí|string|La ruta de acceso de la operación de API.|  
|Consultas|No|Objeto|Representa los parámetros de consulta para agregar a la dirección URL. Por ejemplo, `"queries" : { "api-version": "2015-02-01" }` agrega `?api-version=2015-02-01` a la dirección URL.|  
|encabezados|No|Objeto|Representa cada uno de los encabezados que se envían a la solicitud. Por ejemplo, para establecer el idioma y el tipo en una solicitud: `"headers" : { "Accept-Language": "en-us",  "Content-Type": "application/json" }`.|  
|body|No|Objeto|Representa la carga útil que se envía al punto de conexión.|  
|retryPolicy|No|Objeto|Le permite personalizar el comportamiento de reintento para errores 4xx o 5xx.|  
|operationsOptions|No|string|Define el conjunto de comportamientos especiales para invalidar.|  

```json
"Send_Email": {
    "type": "apiconnection",
    "inputs": {
        "host": {
            "api": {
                "runtimeUrl": "https://logic-apis-df.azure-apim.net/apim/office365"
            },
            "connection": {
                "name": "@parameters('$connections')['office365']['connectionId']"
            }
        },
        "method": "post",
        "body": {
            "Subject": "New Tweet from @{triggerBody()['TweetedBy']}",
            "Body": "@{triggerBody()['TweetText']}",
            "To": "me@example.com"
        },
        "path": "/Mail"
    },
    "runAfter": {}
    }
```

## <a name="api-connection-webhook-action"></a>Acción Webhook de API Connection

```json
"Send_approval_email": {
    "type": "apiconnectionwebhook",
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

Se pueden especificar límites sobre una acción de webhook de la misma manera que los [límites asincrónicos de HTTP](#asynchronous-limits).
  
## <a name="response-action"></a>Acción de respuesta  

Este tipo de acción contiene la carga útil de respuesta completa de una solicitud HTTP e incluye un objeto statusCode, el cuerpo y los encabezados:  
  
```json
"myresponse" : {
    "type" : "response",
    "inputs" : {
        "statusCode" : 200,
        "body" : {
            "contentFieldOne" : "value100",
            "anotherField" : 10.001
        },
        "headers" : {
            "x-ms-date" : "@utcnow()",
            "Content-type" : "application/json"
        }
    },
    "runAfter": {}
}
```
  
La acción de respuesta tiene restricciones especiales que no son aplicables a otras acciones. Concretamente:  
  
-   Las acciones de respuesta no pueden ser paralelas en una definición porque se requiere una respuesta determinista a la solicitud entrante.  
  
-   Si se alcanza una acción de respuesta después de que la solicitud de entrada ha recibido una respuesta, la acción se considera errónea \(conflicto\), y como resultado, la ejecución es `Failed`.  
  
-   Un flujo de trabajo con acciones de respuesta no puede tener `splitOn` en su desencadenador porque una llamada provoca muchas ejecuciones. Como resultado, esto se debería validar cuando el flujo es PUT y provoca una solicitud incorrecta.  
  
## <a name="wait-action"></a>Acción Wait  

La acción `wait` suspende la ejecución del flujo de trabajo para el intervalo especificado. Por ejemplo, para esperar 15 minutos, puede usar este fragmento de código:  
  
```json
"waitForFifteenMinutes" : {
    "type": "wait",
    "inputs": {
        "interval": {
            "unit" : "minute",
            "count" : 15
        }
    }
}
```  
  
Como alternativa, para esperar hasta un momento específico en el tiempo, puede usar este ejemplo:  
  
```json
"waitUntilOctober" : {
    "type": "wait",
    "inputs": {
        "until": {
            "timestamp" : "2016-10-01T00:00:00Z"
        }
    }
}
```
  
> [!NOTE]  
> La duración de espera puede especificarse mediante el objeto **interval** o el objeto **until**, pero no ambos.  
  
|Nombre|Obligatorio|Tipo|Descripción|  
|--------|------------|--------|---------------|  
|interval|No|Objeto|La duración de espera se basa en la cantidad de tiempo.|  
|interval unit|Sí|string|Uno de estos intervalos: segundo, minuto, hora, día, semana, mes o año.|  
|interval count|Sí|String|Duración basada en la unidad interna determinada.|  
|until|No|Objeto|La duración de espera en función de un punto en el tiempo.|  
|until timestamp|Sí|String|El punto en el tiempo en UTC cuando expira la espera.|  

## <a name="query-action"></a>Acción de consulta

La acción `query` le permite filtrar una matriz en función de una condición. Por ejemplo, para seleccionar un número mayor que 2, puede usar:

```json
"FilterNumbers" : {
    "type": "query",
    "inputs": {
        "from": [ 1, 3, 0, 5, 4, 2 ],
        "where": "@greater(item(), 2)"
    }
}
```

La salida de la acción `query` es una matriz que contiene elementos de la matriz de entrada que satisfacen la condición.

> [!NOTE]
> Si ningún valor satisface la condición `where`, el resultado es una matriz vacía.

|Nombre|Obligatorio|Tipo|Descripción|
|--------|------------|--------|---------------|
|De|Sí|Matriz|La matriz de origen.|
|donde|Sí|String|La condición que se va a aplicar a cada elemento de la matriz de origen.|

## <a name="select-action"></a>Acción Select

La acción `select` permite proyectar cada elemento de una matriz en un nuevo valor.
Por ejemplo, para convertir una matriz de números en una matriz de objetos, puede usar:

```json
"SelectNumbers" : {
    "type": "select",
    "inputs": {
        "from": [ 1, 3, 0, 5, 4, 2 ],
        "select": { "number": "@item()" }
    }
}
```

El resultado de la acción `select` es una matriz que tiene la misma cardinalidad que la matriz de entrada, con cada elemento transformado según la definición de la propiedad `select`. Si la entrada es una matriz vacía, el resultado también es una matriz vacía.

|Nombre|Obligatorio|Tipo|Descripción|
|--------|------------|--------|---------------|
|De|Sí|Matriz|La matriz de origen.|
|select|Sí|Cualquiera|Proyección que se va a aplicar a cada elemento de la matriz de origen.|

## <a name="terminate-action"></a>Acción Terminate

La acción Terminate detiene la ejecución del flujo de trabajo, de forma que anula cualquier acción en curso y omite el resto de acciones. Por ejemplo, para terminar una ejecución con el estado **Failed**, puede usar el siguiente fragmento de código:

```json
"HandleUnexpectedResponse" : {
    "type": "terminate",
    "inputs": {
        "runStatus" : "failed",
        "runError": {
            "code": "UnexpectedResponse",
            "message": "Received an unexpected response.",
        }
    }
}
```

> [!NOTE]
> Las acciones completadas no se ven afectadas por la acción de terminación.

|Nombre|Obligatorio|Tipo|Descripción|
|--------|------------|--------|---------------|
|runStatus|Sí|String|El estado de ejecución de destino. **Failed** o **Cancelled**.|
|runError|No|Objeto|Los detalles del error. Solo se admite cuando **runStatus** está establecido en **Failed**.|
|runError code|No|String|El código de error de ejecución.|
|runError message|No|string|El mensaje de error de ejecución.|

## <a name="compose-action"></a>Acción Compose

La acción Compose permite construir un objeto arbitrario. El resultado de la acción de composición es el resultado de evaluar sus entradas. Por ejemplo, puede usar la acción de composición para combinar los resultados de varias acciones:

```json
"composeUserRecord" : {
    "type": "compose",
    "inputs": {
        "firstName": "@actions('getUser').firstName",
        "alias": "@actions('getUser').alias",
        "thumbnailLink": "@actions('lookupThumbnail').url"
        }
    }
}
```

> [!NOTE]
> La acción **Compose** puede usarse para construir cualquier salida, como objetos, matrices y cualquier otro tipo compatible de forma nativa con aplicaciones lógicas, como XML y binario.

## <a name="table-action"></a>Acción Table

`table` permite convertir una matriz de elementos en una tabla **CSV** o **HTML**.

Si @triggerBody() es

```json
[{
  "id": 0,
  "name": "apples"
},{
  "id": 1, 
  "name": "oranges"
}]
```

Y permite que la acción se defina como

```json
"ConvertToTable" : {
    "type": "table",
    "inputs": {
        "from": "@triggerBody()",
        "format": "html"
    }
}
```

Lo anterior generaría

<table><thead><tr><th>id</th><th>name</th></tr></thead><tbody><tr><td>0</td><td>apples</td></tr><tr><td>1</td><td>oranges</td></tr></tbody></table>"

Para personalizar la tabla, puede especificar las columnas de forma explícita. Por ejemplo:

```json
"ConvertToTable" : {
    "type": "table",
    "inputs": {
        "from": "@triggerBody()",
        "format": "html",
        "columns": [{
          "header": "produce id",
          "value": "@item().id"
        },{
          "header": "description",
          "value": "@concat('fresh ', item().name)"
        }]
    }
}
```

Lo anterior generaría

<table><thead><tr><th>produce id</th><th>Description</th></tr></thead><tbody><tr><td>0</td><td>fresh apples</td></tr><tr><td>1</td><td>fresh oranges</td></tr></tbody></table>"

Si el valor de la propiedad `from` es una matriz vacía, el resultado es una tabla vacía.

|Nombre|Obligatorio|Tipo|Descripción|
|--------|------------|--------|---------------|
|De|Sí|Matriz|La matriz de origen.|
|formato|Sí|String|El formato, ya sea **CSV** o **HTML**.|
|columnas|No|Matriz|Las columnas. Permite reemplazar la forma predeterminada de la tabla.|
|column header|No|String|Encabezado de la columna.|
|column value|Sí|String|Valor de la columna.|

## <a name="workflow-action"></a>Acción Workflow   

|Nombre|Obligatorio|Tipo|Descripción|  
|--------|------------|--------|---------------|  
|host id|Sí|String|El identificador de recurso del flujo de trabajo que desea llamar.|  
|host triggerName|Sí|String|El nombre del desencadenador que desea invocar.|  
|Consultas|No|Objeto|Representa los parámetros de consulta para agregar a la dirección URL. Por ejemplo, `"queries" : { "api-version": "2015-02-01" }` agrega `?api-version=2015-02-01` a la dirección URL.|  
|encabezados|No|Objeto|Representa cada uno de los encabezados que se envían a la solicitud. Por ejemplo, para establecer el idioma y el tipo en una solicitud: `"headers" : { "Accept-Language": "en-us",  "Content-Type": "application/json" }`.|  
|body|No|Objeto|Representa la carga útil enviada al punto de conexión.|  
  
```json
"mynestedwf" : {
    "type" : "workflow",
    "inputs" : {
        "host" : {
            "id" : "/subscriptions/xxxxyyyyzzz/resourceGroups/rg001/providers/Microsoft.Logic/mywf001",
            "triggerName " : "mytrigger001"
        },
        "queries" : {
            "extrafield" : "specialValue"
        },  
        "headers" : {
            "x-ms-date" : "@utcnow()",
            "Content-type" : "application/json"
        },
        "body" : {
            "contentFieldOne" : "value100",
            "anotherField" : 10.001
        }
    },
    "runAfter": {}
    }
```
  
Se realiza una comprobación de acceso en el flujo de trabajo \(más en concreto, del desencadenador\), lo que significa que necesita acceso al flujo de trabajo.  
  
Los resultados de la acción `workflow` se basan en lo que define en la acción `response` en el flujo de trabajo secundario. Si no ha definido ninguna acción `response`, las salidas estarán vacías.  

## <a name="function-action"></a>Acción de la función   

|Nombre|Obligatorio|Tipo|Descripción|  
|--------|------------|--------|---------------|  
|function id|Sí|String|El identificador de recurso de la función que quiere invocar.|  
|estático|No|String|El método HTTP que se usa para invocar la función. De forma predeterminada, es `POST` cuando no se especifica.|  
|Consultas|No|Objeto|Representa los parámetros de consulta para agregar a la dirección URL. Por ejemplo, `"queries" : { "api-version": "2015-02-01" }` agrega `?api-version=2015-02-01` a la dirección URL.|  
|encabezados|No|Objeto|Representa cada uno de los encabezados que se envían a la solicitud. Por ejemplo, para establecer el idioma y el tipo en una solicitud: `"headers" : { "Accept-Language": "en-us" }`.|  
|body|No|Objeto|Representa la carga útil enviada al punto de conexión.|  

```json
"myfunc" : {
    "type" : "Function",
    "inputs" : {
        "function" : {
            "id" : "/subscriptions/xxxxyyyyzzz/resourceGroups/rg001/providers/Microsoft.Web/sites/myfuncapp/functions/myfunc"
        },
        "queries" : {
            "extrafield" : "specialValue"
        },  
        "headers" : {
            "x-ms-date" : "@utcnow()"
        },
        "method" : "POST",
    "body" : {
            "contentFieldOne" : "value100",
            "anotherField" : 10.001
        }
    },
    "runAfter": {}
}
```

Cuando se guarda la aplicación lógica, realizamos algunas comprobaciones en la función a la que se hace referencia:
-   Debe tener acceso a la función.
-   Solo se permite el desencadenador HTTP estándar o webhook JSON genérico.
-   No debe tener ninguna ruta definida.
-   Solo se permite el nivel de autorización "function" y "anonymous".

La dirección URL de desencadenador se recupera, se almacena en la memoria caché y se usa en tiempo de ejecución. Por tanto, si una operación invalida la dirección URL almacenada en caché, se produce un error en tiempo de ejecución en la acción. Para resolver este problema, vuelva a guardar la aplicación lógica, lo que hará que recupere y almacene en caché la dirección URL de desencadenador nuevo.

## <a name="collection-actions-scopes-and-loops"></a>Acciones de colección (ámbitos y bucles)

Algunos tipos de acciones pueden contener acciones dentro de sí mismas. Se puede hacer referencia a las acciones dentro de una colección directamente fuera de la colección. Si ha definido `http` en un ámbito, `@body('http')` sigue siendo válido en cualquier parte de un flujo de trabajo. Las acciones dentro de una colección solo puede ejecutarse después (`runAfter`) de otras acciones de la misma colección.

## <a name="scope-action"></a>Acción Scope

La acción `scope` le permite agrupar de forma lógica las acciones de un flujo de trabajo.

|Nombre|Obligatorio|Tipo|Descripción|  
|--------|------------|--------|---------------|  
|actions|Sí|Objeto|Acciones internas que se ejecutan dentro del ámbito|

```json
{
    "myScope": {
        "type": "scope",
        "actions": {
            "call_bing": {
                "type": "http",
                "inputs": {
                    "url": "http://www.bing.com"
                }
            }
        }
    }
}
```

## <a name="foreach-action"></a>Acción ForEach

Esta acción de bucle recorre en iteración una matriz y realiza acciones internas para cada elemento. De forma predeterminada, el bucle foreach se ejecuta en paralelo (20 ejecuciones en paralelo a la vez). Puede establecer reglas de ejecución mediante el parámetro `operationOptions`.

|Nombre|Obligatorio|Tipo|Descripción|  
|--------|------------|--------|---------------|  
|actions|Sí|Objeto|Acciones internas que se ejecutan dentro del bucle|
|foreach|Sí|string|La matriz para recorrer en iteración.|
|operationOptions|no|string|Las opciones de comportamiento de la operación. Actualmente solo se admite `sequential` para ejecutar iteraciones de manera secuencial (el comportamiento predeterminado es en paralelo)|

```json
"forEach_email": {
    "type": "foreach",
    "foreach": "@body('email_filter')",
    "actions": {
        "send_email": {
            "type": "ApiConnection",
            "inputs": {
                "body": {
                    "to": "@item()",
                    "from": "me@contoso.com",
                    "message": "Hello, thank you for ordering"
                }
                "host": {
                    "connection": {
                        "id": "@parameters('$connections')['office365']['connection']['id']"
                    }
                }
            }
        }
    },
    "runAfter":{
        "email_filter": [ "Succeeded" ]
    }
}
```

## <a name="until-action"></a>Acción Until

Esta acción de bucle ejecuta acciones internas hasta que una condición da como resultado true.

|Nombre|Obligatorio|Tipo|Descripción|  
|--------|------------|--------|---------------|  
|actions|Sí|Objeto|Acciones internas que se ejecutan dentro del bucle|
|expresión|Sí|cadena|La expresión para evaluar después de cada iteración.|
|limit|yes|Objeto|Se deben definir los límites del bucle (al menos uno).|
|count|no|int|El límite para el número de iteraciones que se pueden realizar.|
|timeout|no|string|El tiempo de espera de la duración del bucle.  Formato ISO 8601|


```json
 "Until_succeeded": {
    "actions": {
        "Http": {
            "inputs": {
                "method": "GET",
                "uri": "http://myurl"
            },
            "runAfter": {},
            "type": "Http"
        }
    },
    "expression": "@equals(outputs('Http')['statusCode', 200)",
    "limit": {
        "count": 1000,
        "timeout": "PT1H"
    },
    "runAfter": {},
    "type": "Until"
}
```

## <a name="conditions---if-action"></a>Condiciones: acción If

La acción `If` le permite evaluar una condición y ejecutar una rama en función de si la expresión se evalúa como `true`.

|Nombre|Obligatorio|Tipo|Descripción|  
|--------|------------|--------|---------------|  
|actions|Sí|Objeto|Acciones internas que se ejecutan cuando la expresión se evalúa como `true`|
|expresión|Sí|string|La expresión para evaluar.|
|else|no|Objeto|Acciones internas que se ejecutan cuando la expresión se evalúa como `false`|
  
```json
"My_condition": {
    "actions": {
        "If_true": {
            "inputs": {
                "method": "GET",
                "uri": "http://myurl"
            },
            "runAfter": {},
            "type": "Http"
        }
    },
    "else": {
        "actions": {
            "if_false": {
                "inputs": {
                    "method": "GET",
                    "uri": "http://myurl"
                },
                "runAfter": {},
                "type": "Http"
            }
        }
    },
    "expression": "@equals(triggerBody(), json(true))",
    "runAfter": {},
    "type": "If"
}
```  
  
En la tabla siguiente se muestran ejemplos de cómo las condiciones pueden usar expresiones en una acción:  
  
|Valor JSON|Resultado|  
|--------------|----------|  
|`"expression": "@parameters('hasSpecialAction')"`|Cualquier valor que se evaluaría como true hace que pase esta condición. Solo se admiten expresiones de tipo Boolean. Para convertir otros tipos a Boolean, use funciones `empty`, `equals`.|  
|`"expression": "@greater(actions('act1').output.value, parameters('threshold'))"`|Se admiten funciones de comparación. En el ejemplo, la acción se ejecuta solo cuando la salida de act1 es mayor que el umbral.|  
|`"expression": "@or(greater(actions('act1').output.value, parameters('threshold')), less(actions('act1').output.value, 100))"`|También se admiten funciones lógicas para crear expresiones Boolean anidadas. En este caso, la acción se ejecuta cuando la salida de act1 esté por encima del umbral o por debajo de 100.|  
|`"expression": "@equals(length(actions('act1').outputs.errors), 0))"`|Puede usar funciones de matriz para comprobar si una matriz tiene algún elemento. En este caso, la acción se ejecuta cuando la matriz de errores está vacía.| 
|`"expression": "parameters('hasSpecialAction')"`|Error: no es una condición válida porque se requiere @ para las condiciones.|  
  
Si una condición se evalúa correctamente, la condición se marca como `Succeeded`. Las acciones dentro de los objetos `actions` o `else` se evalúan como `Succeeded` cuando se ejecutan y lo hacen correctamente, `Failed` cuando se ejecutan y producen error, o `Skipped` cuando no se ejecuta esa rama.

## <a name="next-steps"></a>Pasos siguientes

[API de REST de Servicio de flujo de trabajo](https://docs.microsoft.com/rest/api/logic/workflows)

