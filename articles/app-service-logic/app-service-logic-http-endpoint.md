---
title: "Aplicaciones lógicas como puntos de conexión invocables"
description: "Cómo crear y configurar puntos de conexión desencadenadores y usarlos en una aplicación lógica del Servicio de aplicaciones de Azure"
services: logic-apps
documentationcenter: .net,nodejs,java
author: jeffhollan
manager: erikre
editor: 
ms.assetid: 73ba2a70-03e9-4982-bfc8-ebfaad798bc2
ms.service: logic-apps
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: integration
ms.date: 10/18/2016
ms.author: jehollan
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 372114b1420139bdf525521a427f924aae38db6f


---
# <a name="logic-apps-as-callable-endpoints"></a>Aplicaciones lógicas como puntos de conexión invocables
Logic Apps puede exponer de forma nativa un punto de conexión HTTP sincrónico como desencadenador.  También puede usar el patrón de puntos de conexión disponibles para invocar Aplicaciones lógicas como un flujo de trabajo anidado a través de la acción "flujo de trabajo" de una aplicación lógica.

Hay tres tipos de desencadenadores que pueden recibir solicitudes:

* Solicitud
* ApiConnectionWebhook
* HttpWebhook

En el resto del artículo, usaremos como ejemplo **solicitud** , pero todos los principios se aplican de forma idéntica a los otros dos tipos de desencadenadores.

## <a name="adding-a-trigger-to-your-definition"></a>Incorporación de un desencadenador a la definición
El primer paso consiste en agregar un desencadenador a la definición de aplicación lógica que puede recibir solicitudes entrantes.  Puede buscar en el diseñador "solicitud HTTP" para agregar la tarjeta desencadenadora. Puede definir un esquema JSON de cuerpo de solicitud y el diseñador generará tokens para ayudarlo a analizar y pasar datos del desencadenador manual a través del flujo de trabajo.  Recomiendo usar una herramienta como [jsonschema.net](http://jsonschema.net) para generar un esquema JSON a partir de la carga útil de un cuerpo de muestra.

![Tarjeta desencadenadora de solicitud][2]

Después de guardar la definición de aplicación lógica, se generará una dirección URL de devolución de llamadas similar a asta:

``` text
https://prod-03.eastus.logic.azure.com:443/workflows/080cb66c52ea4e9cabe0abf4e197deff/triggers/myendpointtrigger?*signature*...
```

Esta dirección URL contiene una clave SAS en los parámetros de consulta usados para la autenticación.

También puede obtener este punto de conexión en el Portal de Azure:

![][1]

O bien, mediante:

``` text
POST https://management.azure.com/{resourceID of your logic app}/triggers/myendpointtrigger/listCallbackURL?api-version=2015-08-01-preview
```

### <a name="security-for-the-trigger-url"></a>Seguridad para la dirección URL del desencadenador
Las direcciones URL de devolución de llamada de la aplicación lógica se generan de forma segura con una firma de acceso compartido.  La firma pasa como un parámetro de consulta y debe validarse antes de que se active la aplicación lógica.  Se genera mediante una combinación única de una clave secreta por aplicación lógica, el nombre del desencadenador y la operación que se está realizando.  A menos que alguien tenga acceso a la clave de aplicación lógica secreta, no será posible generar una firma válida.

## <a name="calling-the-logic-app-triggers-endpoint"></a>Llamada al punto de conexión del desencadenador de la aplicación lógica
Una vez que haya creado el punto de conexión del desencadenador, puede desencadenarlo mediante una instrucción `POST` a la dirección URL completa. Puede incluir más encabezados y contenido en el cuerpo.

Si el tipo de contenido es `application/json` , podrá hacer referencia a las propiedades desde dentro de la solicitud. Si no, se tratará como una única unidad binaria que se puede pasar a otras API, pero a la que no se puede hacer referencia dentro del flujo de trabajo sin convertir el contenido.  Por ejemplo, si pasa contenido de `application/xml`, podría utilizar `@xpath()` para realizar una extracción de xpath, o bien `@json()` para convertir de XML a JSON.  Más información sobre cómo trabajar con tipos de contenido [puede encontrarse aquí](app-service-logic-content-type.md)

Además, puede especificar un esquema JSON en la definición. De este modo, el diseñador puede generar tokens que se pueden pasar en pasos.  Por ejemplo, en el caso siguiente se pone un token `title` y `name` a disposición en el diseñador:

```
{
    "properties":{
        "title": {
            "type": "string"
        },
        "name": {
            "type": "string"
        }
    },
    "required": [
        "title",
        "name"
    ],
    "type": "object"
}
```

## <a name="referencing-the-content-of-the-incoming-request"></a>Referencia al contenido de la solicitud entrante
La función `@triggerOutputs()` mostrará el contenido de la solicitud entrante. Por ejemplo, será similar a esto:

```
{
    "headers" : {
        "content-type" : "application/json"
    },
    "body" : {
        "myprop" : "a value"
    }
}
```

Puede usar el acceso directo `@triggerBody()` para tener acceso a la propiedad `body` de forma específica. 

## <a name="responding-to-the-request"></a>Respuesta a la solicitud
En el caso de algunas solicitudes que inician una aplicación lógica, es posible que quiera responder con algún contenido al autor de la llamada. Hay un nuevo tipo de acción llamado **respuesta** que se puede utilizar para construir el código de estado, el cuerpo y los encabezados de la respuesta. Tenga en cuenta que si no hay ninguna forma de **respuesta**, el punto de conexión de la aplicación lógica responderá *inmediatamente* con **202 - Aceptado**.

![Acción de respuesta HTTP][3]

``` json
"Response": {
            "conditions": [],
            "inputs": {
                "body": {
                    "name": "@{triggerBody()['name']}",
                    "title": "@{triggerBody()['title']}"
                },
                "headers": {
                    "content-type": "application/json"
                },
                "statusCode": 200
            },
            "type": "Response"
        }
```

Las respuestas tienen los siguientes elementos:

| Propiedad | Description |
| --- | --- |
| statusCode |El código de estado HTTP para responder a la solicitud entrante. Puede ser cualquier código de estado válido que comience con 2xx, 4xx o 5xx. No se permiten códigos de estado 3xx. |
| body |Un objeto de cuerpo que puede ser una cadena, un objeto JSON o incluso contenido binario al que se hace referencia desde un paso anterior. |
| encabezados |Puede definir cualquier número de encabezados que se incluirán en la respuesta |

Todos los pasos de la aplicación lógica que se requieren para la respuesta deben completarse dentro de *60 segundos* para que la solicitud original reciba la respuesta, **a menos que se llame al flujo de trabajo como una aplicación lógica anidada**. Si no se obtiene ninguna acción de respuesta en 60 segundos, la solicitud entrante agotará el tiempo de espera y recibirá una respuesta HTTP **408 - Tiempo de espera de cliente agotado** .  En cuanto a las aplicaciones lógicas anidadas, la aplicación lógica principal seguirá esperando una respuesta hasta que finalice, independientemente del tiempo que tarde.

## <a name="advanced-endpoint-configuration"></a>Configuración avanzada del punto de conexión
Las aplicaciones lógicas tienen compatibilidad integrada con el punto de conexión de acceso directo y siempre utilizan el método `POST` para iniciar la ejecución de la aplicación lógica. La aplicación de API **Agente de escucha HTTP** anteriormente también admitía el cambio de los segmentos de dirección URL y el método HTTP. Incluso podía configurar seguridad adicional o un dominio personalizado agregándolo al host de aplicación de API (la aplicación web que hospeda la aplicación de API). 

Esta funcionalidad está disponible a través de **Administración de API**:

* [Cambio del método de la solicitud](https://msdn.microsoft.com/library/azure/dn894085.aspx#SetRequestMethod)
* [Cambio de los segmentos de dirección URL de la solicitud](https://msdn.microsoft.com/library/azure/7406a8ce-5f9c-4fae-9b0f-e574befb2ee9#RewriteURL)
* Configuración de los dominios de administración de API en la pestaña **Configurar** del Portal de Azure clásico
* Configuración de la directiva para comprobar la autenticación básica (**vínculo necesario**)

## <a name="summary-of-migration-from-2014-12-01-preview"></a>Resumen de la migración desde 2014-12-01-preview
| 2014-12-01-preview | 2016-06-01 |
| --- | --- |
| Haga clic en la aplicación de API **Agente de escucha HTTP** |Haga clic en **Desencadenador manual** (no se necesita ninguna aplicación de API) |
| Configuración del Agente de escucha HTTP "*Enviar respuesta automáticamente*" |Tanto si incluye una acción de **respuesta** en la definición del flujo de trabajo como si no |
| Configuración de la autenticación básica o OAuth |mediante Administración de API |
| Configuración del método HTTP |mediante Administración de API |
| Configuración de la ruta de acceso relativa |mediante Administración de API |
| Referencia al cuerpo entrante mediante `@triggerOutputs().body.Content` |Referencia mediante `@triggerOutputs().body` |
| **Enviar respuesta HTTP** del Agente de escucha HTTP |Haga clic en **Responder a la solicitud HTTP** (no se necesita ninguna aplicación de API) |

[1]: ./media/app-service-logic-http-endpoint/manualtriggerurl.png
[2]: ./media/app-service-logic-http-endpoint/manualtrigger.png
[3]: ./media/app-service-logic-http-endpoint/response.png



<!--HONumber=Nov16_HO3-->


