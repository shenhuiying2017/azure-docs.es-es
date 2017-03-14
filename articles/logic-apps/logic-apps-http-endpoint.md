---
title: "Llamar, desencadenar o anidar aplicaciones lógicas mediante puntos de conexión HTTP: Azure Logic Apps | Microsoft Docs"
description: "Agregar y configurar puntos de conexión HTTP para llamar, desencadenar o anidar flujos de trabajo para las aplicaciones lógicas en Azure"
services: logic-apps
documentationcenter: .net,nodejs,java
author: jeffhollan
manager: anneta
editor: 
ms.assetid: 73ba2a70-03e9-4982-bfc8-ebfaad798bc2
ms.service: logic-apps
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: integration
ms.custom: H1Hack27Feb2017
ms.date: 10/18/2016
ms.author: jehollan
translationtype: Human Translation
ms.sourcegitcommit: 1e6ae31b3ef2d9baf578b199233e61936aa3528e
ms.openlocfilehash: 8c4cb0aefcd7241b17c6afcf33496c1fc2e6f675
ms.lasthandoff: 03/03/2017

---

# <a name="add-http-endpoints-to-call-trigger-or-nest-logic-app-workflows"></a>Agregar puntos de conexión HTTP para llamar, desencadenar o anidar flujos de trabajo para las aplicaciones lógicas

Las aplicaciones lógicas pueden exponer de manera nativa puntos de conexión HTTP sincrónicos como desencadenadores, de manera que usted pueda llamar a sus aplicaciones lógicas. También se puede usar un patrón de puntos de conexión disponibles para invocar aplicaciones lógicas como flujos de trabajo anidados. Para ello, agregue la acción **Elegir un flujo de trabajo de Logic Apps** a su aplicación lógica.

Puede usar estos desencadenadores para recibir solicitudes:

* Solicitud
* ApiConnectionWebhook
* HttpWebhook

En este tema se usa el desencadenador de tipo **Solicitud** como ejemplo, pero todos los principios se aplican de manera idéntica a los otros tipos de desencadenador.

## <a name="add-a-trigger-to-your-logic-app-definition"></a>Agregar un desencadenador a la definición de aplicación lógica

1. En el Diseñador de aplicaciones lógicas, agregue un desencadenador que pueda recibir solicitudes entrantes para la definición de aplicación lógica. Por ejemplo, agregue el desencadenador de tipo **Solicitud** a su aplicación lógica.

2.    En **Esquema JSON de cuerpo de solicitud**, puede escribir un esquema JSON para la carga que espera recibir. Si no tiene listo el esquema, puede pulsar **Usar una carga de ejemplo para generar el esquema** para generar un esquema JSON de una carga de ejemplo.

    El diseñador usa este esquema para generar tokens que le ayuden a consumir, analizar y pasar datos del desencadenador manual a través de su flujo de trabajo.

    ![Agregar la acción de solicitud][2]

2.    Después de que haya guardado la definición de aplicación lógica, en **HTTP POST a esta dirección URL**, obtendrá una dirección URL de devolución de llamadas generada, como se muestra en este ejemplo:

    ``` text
    https://prod-03.eastus.logic.azure.com:443/workflows/080cb66c52ea4e9cabe0abf4e197deff/triggers/myendpointtrigger?*signature*...
    ```

    Esta dirección URL contiene una clave SAS (Firma de acceso compartido) en los parámetros de consulta usados para la autenticación. 
    También puede obtener este punto de conexión de Azure Portal:

    ![Punto de conexión de dirección URL][1]

    O bien, mediante la llamada a:

    ``` text
    POST https://management.azure.com/{resourceID of your logic app}/triggers/myendpointtrigger/listCallbackURL?api-version=2015-08-01-preview
    ```

### <a name="change-the-http-method-for-your-trigger"></a>Cambiar el método HTTP para el desencadenador

De manera predeterminada, el desencadenador de tipo Solicitud espera una solicitud HTTP POST. Para configurar un método HTTP diferente, pulse **Mostrar opciones avanzadas**.

> [!NOTE]
> Solo se permite un tipo de método.

### <a name="customize-the-relative-trigger-url"></a>Personalizar la dirección URL relativa del desencadenador

También puede personalizar la ruta de acceso relativa de la URL de solicitud para aceptar parámetros.

1. En el desencadenador de tipo **Solicitud**, pulse **Mostrar opciones avanzadas**. En **Ruta de acceso relativa**, escriba `customer/{customerId}`.

    ![Desencadenador de direcciones URL relativas](./media/logic-apps-http-endpoint/relativeurl.png)

2.    Para usar el parámetro, actualice la acción de **respuesta**.

    *    Verá que `customerId` aparece en el selector de tokens.
    *    Para devolver `Hello {customerId}`, actualice el cuerpo de la acción de **respuesta**.

    ![Respuesta de la dirección URL relativa](./media/logic-apps-http-endpoint/relativeurlresponse.png)

3. Guarde la aplicación lógica. La dirección URL de solicitud se actualiza con la ruta de acceso relativa.

4. Copie y pegue la nueva dirección URL de solicitud en una nueva ventana del explorador. Sustituya `{customerId}` por `123` y presione Entrar.

    Debe devolverse este texto: `Your customer Id is 123`

### <a name="security-for-the-trigger-url"></a>Seguridad para la dirección URL del desencadenador

Las direcciones URL de devolución de llamada de la aplicación lógica se generan de manera segura con una firma de acceso compartido (SAS). La firma pasa como un parámetro de consulta y debe validarse antes de que se pueda activar la aplicación lógica. La firma se genera mediante una combinación única de una clave secreta por aplicación lógica, el nombre del desencadenador y la operación que se realiza. A menos que alguien tenga acceso a la clave de aplicación lógica secreta, no pueden generar una firma válida.

## <a name="call-your-logic-app-triggers-endpoint"></a>Llamar al punto de conexión del desencadenador de la aplicación lógica

Una vez que haya creado el punto de conexión del desencadenador, puede desencadenar la aplicación lógica mediante una instrucción `POST` a la dirección URL completa. Puede incluir más encabezados y contenido en el cuerpo. Si el tipo de contenido es `application/json`, puede hacer referencia a las propiedades desde dentro de la solicitud. De otro modo, el contenido se trata como una única unidad binaria que puede pasarse a otras API, pero a la que no se puede hacer referencia dentro del flujo de trabajo a no ser que se convierta el contenido. Por ejemplo, si pasa contenido de `application/xml`, puede usar `@xpath()` para una extracción de XPath, o bien `@json()` para convertir de XML a JSON. Obtenga información sobre cómo [trabajar con tipos de contenido](../logic-apps/logic-apps-content-type.md).

Además, puede especificar un esquema JSON en la definición. Este esquema hace que el diseñador genere tokens que puede pasar en pasos. El siguiente ejemplo hace que los tokens `title` y `name` estén disponibles en el diseñador:

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

## <a name="reference-the-content-from-the-incoming-request"></a>Referencia al contenido de la solicitud entrante

La función `@triggerOutputs()` muestra el contenido de la solicitud entrante. El resultado tendrá un aspecto similar al de este ejemplo:

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

Para tener acceso a la propiedad `body` de manera específica, puede usar el acceso directo `@triggerBody()`. 

## <a name="respond-to-the-request"></a>Responder a la solicitud

En el caso de algunas solicitudes que inician una aplicación lógica, es posible que quiera responder con algún contenido al autor de la llamada. Para construir el código de estado, el cuerpo y los encabezados de la respuesta, puede usar un tipo de acción denominado **Respuesta**. En cambio, si no se incluye ninguna **Respuesta**, el punto de conexión de la aplicación lógica responde *inmediatamente* con **202 - Aceptado**.

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

Las respuestas tienen estas propiedades:

| Propiedad | Description |
| --- | --- |
| statusCode |El código de estado HTTP para responder a la solicitud entrante. Este código puede ser cualquier código de estado válido que comience con 2xx, 4xx o 5xx. En cambio, no se permiten códigos de estado 3xx. |
| body |Un objeto de cuerpo que puede ser una cadena, un objeto JSON o incluso contenido binario al que se hace referencia desde un paso anterior. |
| encabezados |Puede definir cualquier número de encabezados para incluirse en la respuesta. |

En la aplicación lógica, todos los pasos necesarios para la respuesta deben terminar en *60 segundos* para que la solicitud original obtenga la respuesta, *a no ser que llame al flujo de trabajo como una aplicación lógica anidada*. Si no se obtiene ninguna respuesta en 60 segundos, la solicitud entrante agotará el tiempo de espera y recibirá una respuesta HTTP **408 - Tiempo de espera del cliente agotado**. En cuanto a las aplicaciones lógicas anidadas, la aplicación lógica principal seguirá esperando una respuesta hasta que finalice, independientemente del tiempo que se necesite.

## <a name="advanced-endpoint-configuration"></a>Configuración avanzada del punto de conexión

Las aplicaciones lógicas tienen compatibilidad integrada con el punto de conexión de acceso directo y siempre usan el método `POST` para iniciar la ejecución de la aplicación lógica. Anteriormente, la aplicación de API **Agente de escucha HTTP** también admitía el cambio de los segmentos de dirección URL y el método HTTP. Incluso puede configurar seguridad adicional o un dominio personalizado agregando estos elementos al host de aplicación de API (la aplicación web que hospeda la aplicación de API). 

Esta función está disponible a través de **API Management**:

* [Cambio del método de solicitud](https://msdn.microsoft.com/library/azure/dn894085.aspx#SetRequestMethod)
* [Cambio de los segmentos de dirección URL de la solicitud](https://msdn.microsoft.com/library/azure/7406a8ce-5f9c-4fae-9b0f-e574befb2ee9#RewriteURL)
* Configure los dominios de API Management en la pestaña **Configurar** de la versión clásica de Azure Portal.
* Configure la directiva para comprobar la autenticación básica.

## <a name="summary-of-migration-from-2014-12-01-preview"></a>Resumen de la migración desde 2014-12-01-preview

| 2014-12-01-preview | 2016-06-01 |
| --- | --- |
| Haga clic en la aplicación de API **Agente de escucha HTTP**. |Haga clic en **Desencadenador manual** (no se necesita ninguna aplicación de API). |
| Configuración del Agente de escucha HTTP "*Enviar respuesta automáticamente*" |Tanto si incluye una acción de **Respuesta** en la definición del flujo de trabajo como si no. |
| Configuración de la autenticación básica o OAuth |mediante API Management |
| Configuración del método HTTP |mediante API Management |
| Configuración de la ruta de acceso relativa |mediante API Management |
| Referencia al cuerpo entrante mediante `@triggerOutputs().body.Content` |Referencia mediante `@triggerOutputs().body` |
| **Enviar respuesta HTTP** del Agente de escucha HTTP |Haga clic en **Responder a la solicitud HTTP** (no se necesita ninguna aplicación de API) |

[1]: ./media/logic-apps-http-endpoint/manualtriggerurl.png
[2]: ./media/logic-apps-http-endpoint/manualtrigger.png
[3]: ./media/logic-apps-http-endpoint/response.png

