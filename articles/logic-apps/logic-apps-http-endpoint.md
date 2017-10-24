---
title: "Llamada, desencadenamiento o anidamiento de flujos de trabajo con puntos de conexión HTTP: Azure Logic Apps | Microsoft Docs"
description: "Configuración de puntos de conexión HTTP para llamadas, desencadenamientos o anidamiento de flujos de trabajo para Azure Logic Apps"
services: logic-apps
keywords: "flujos de trabajo, puntos de conexión HTTP"
author: jeffhollan
manager: anneta
editor: 
documentationcenter: 
ms.assetid: 73ba2a70-03e9-4982-bfc8-ebfaad798bc2
ms.service: logic-apps
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: integration
ms.custom: H1Hack27Feb2017
ms.date: 03/31/2017
ms.author: LADocs; jehollan
ms.openlocfilehash: dab336da4e010d0a78de9a2bdd62536d8fdd9bf1
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="call-trigger-or-nest-workflows-with-http-endpoints-in-logic-apps"></a>Llamada, desencadenamiento o anidamiento de flujos de trabajo con puntos de conexión HTTP en aplicaciones lógicas

Puede exponer de manera nativa puntos de conexión HTTP sincrónicos como desencadenadores en aplicaciones lógicas, de manera que pueda desencadenar las aplicaciones lógicas o llamarlas a través de una dirección URL. También puede anidar flujos de trabajo en las aplicaciones lógicas mediante el uso de un patrón de puntos de conexión invocables.

Para crear puntos de conexión HTTP, puede agregar estos desencadenadores para que las aplicaciones lógicas puedan recibir solicitudes entrantes:

* [Solicitud](../connectors/connectors-native-reqres.md)

* [Webhook de conexión de API](logic-apps-workflow-actions-triggers.md#api-connection-trigger)

* [Webhook HTTP](../connectors/connectors-native-webhook.md)

   > [!NOTE]
   > Aunque en los ejemplos se usa el desencadenador **Request**, puede usar cualquiera de los desencadenadores HTTP indicados, y todos los principios se aplican exactamente igual a los otros tipos de desencadenadores.

## <a name="set-up-an-http-endpoint-for-your-logic-app"></a>Configuración de un punto de conexión HTTP para la aplicación lógica

Para crear un punto de conexión HTTP, agregue un desencadenador que pueda recibir solicitudes entrantes.

1. Inicie sesión en [Azure Portal](https://portal.azure.com "Azure Portal"). Vaya a la aplicación lógica y abra el Diseñador de aplicación lógica.

2. Agregue un desencadenador que permita a la aplicación lógica recibir solicitudes entrantes. Por ejemplo, agregue el desencadenador de tipo **Solicitud** a su aplicación lógica.

3.  En **Esquema JSON de cuerpo de solicitud**, tiene la opción de escribir un esquema JSON para la carga (datos) que espera que desencadenador reciba.

    El diseñador usa este esquema para generar tokens que la aplicación lógica puede usar para consumir, analizar y pasar datos del desencadenador a través del flujo de trabajo. 
    Obtenga más información sobre [tokens generados a partir de esquemas JSON](#generated-tokens).

    En este ejemplo, escriba el esquema que se muestra en el diseñador:

    ```json
    {
      "type": "object",
      "properties": {
        "address": {
          "type": "string"
        }
      },
      "required": [
        "address"
      ]
    }
    ```

    ![Agregar la acción de solicitud][1]

    > [!TIP]
    > 
    > Puede generar un esquema para una carga JSON de ejemplo desde una herramienta como [jsonschema.net](http://jsonschema.net/), o bien en el desencadenador **Request** con la selección de **Usar una carga de ejemplo para generar el esquema**. 
    > Escriba la carga de ejemplo y elija **Listo**.

    Por ejemplo, esta carga de ejemplo:

    ```json
    {
       "address": "21 2nd Street, New York, New York"
    }
    ```

    genera este esquema:

    ```json
    {
       "type": "object",
       "properties": {
          "address": {
             "type": "string" 
          }
       }
    }
    ```

4.  Guarde la aplicación lógica. En **HTTP POST a esta dirección URL**, ahora debería encontrar una dirección URL de devolución de llamadas generada, al igual que en este ejemplo:

    ![Dirección URL de devolución de llamadas generada para el punto de conexión](./media/logic-apps-http-endpoint/generated-endpoint-url.png)

    Esta dirección URL contiene una clave SAS (Firma de acceso compartido) en los parámetros de consulta usados para la autenticación. 
    También puede obtener la dirección URL del punto de conexión HTTP en la información general sobre la aplicación lógica en Azure Portal. En **Historial de desencadenadores**, seleccione el desencadenador:

    ![Obtención de la dirección URL del punto de conexión HTTP en Azure Portal][2]

    También puede obtener la dirección URL mediante la realización de esta llamada:

    ```
    POST https://management.azure.com/{logic-app-resourceID}/triggers/{myendpointtrigger}/listCallbackURL?api-version=2016-06-01
    ```

## <a name="change-the-http-method-for-your-trigger"></a>Cambiar el método HTTP para el desencadenador

De forma predeterminada, el desencadenaador **Request** espera una solicitud POST de HTTP, pero puede usar un método HTTP diferente. 

> [!NOTE]
> Puede especificar solo un tipo de método.

1. En el desencadenador **Request**, elija **Mostrar opciones avanzadas**.

2. Abra la lista **Método**. En este ejemplo, seleccione **GET** para que pueda probar la dirección URL del punto de conexión HTTP más adelante.

    > [!NOTE]
    > Puede seleccionar cualquier otro método HTTP, o bien especificar un método personalizado para su propia aplicación lógica.

    ![Cambio del método HTTP](./media/logic-apps-http-endpoint/change-method.png)

## <a name="accept-parameters-through-your-http-endpoint-url"></a>Aceptación de parámetros a través de la dirección URL del punto de conexión HTTP

Si desea que la dirección URL del punto de conexión HTTP acepte parámetros, personalice la ruta de acceso relativa del desencadenador.

1. En el desencadenador **Request**, elija **Mostrar opciones avanzadas**. 

2. En **Método**, especifique el método HTTP que desea que la solicitud use. En este ejemplo, seleccione el método **GET**, si no lo ha hecho ya, para que pueda probar la dirección URL del punto de conexión HTTP.

      > [!NOTE]
      > Cuando se especifica una ruta de acceso relativa para el desencadenador, también debe especificar explícitamente un método HTTP para el desencadenador.

3. En **Ruta de acceso relativa**, especifique la ruta de acceso relativa del parámetro que la dirección URL debe aceptar, por ejemplo, `customers/{customerID}`.

    ![Definición del método HTTP y de la ruta de acceso relativa del parámetro](./media/logic-apps-http-endpoint/relativeurl.png)

4. Para usar el parámetro, agregue una acción **Respuesta** a la aplicación lógica. (En el desencadenador, elija **Nuevo paso** > **Agregar una acción** > **Respuesta**). 

5. En el **Cuerpo** de la respuesta, incluya el token para el parámetro especificado en la ruta de acceso relativa del desencadenador.

    Por ejemplo, para devolver `Hello {customerID}`, actualice el **cuerpo** de la respuesta con `Hello {customerID token}`. 
    Debe aparecer la lista de contenido dinámica, donde debe mostrarse el token `customerID` para que pueda seleccionarlo.

    ![Adición del parámetro al cuerpo de respuesta](./media/logic-apps-http-endpoint/relativeurlresponse.png)

    El **cuerpo** debería presentar un aspecto similar al de este ejemplo:

    ![Cuerpo de respuesta con el parámetro](./media/logic-apps-http-endpoint/relative-url-with-parameter.png)

6. Guarde la aplicación lógica. 

    La dirección URL del punto de conexión HTTP ahora incluye la ruta de acceso relativa, por ejemplo: 

    https&#58;//prod-00.southcentralus.logic.azure.com/workflows/f90cb66c52ea4e9cabe0abf4e197deff/triggers/manual/paths/invoke/customers/{customerID}...

7. Para probar el punto de conexión HTTP, copie y pegue la dirección URL actualizada en otra ventana del explorador, pero reemplace `{customerID}` con `123456`, y presione ENTRAR.

    El explorador debe mostrar este texto: 

    `Hello 123456`

<a name="generated-tokens"></a>
### <a name="tokens-generated-from-json-schemas-for-your-logic-app"></a>Tokens generados a partir de esquemas JSON para aplicaciones lógicas

Cuando se proporciona un esquema JSON en el desencadenador **Request**, el Diseñador de aplicación lógica genera tokens para las propiedades de dicho esquema. Después puede usar esos tokens para pasar datos a través del flujo de trabajo de la aplicación lógica.

En este ejemplo, si agrega las propiedades `title` y `name` al esquema JSON, sus tokens ahora están disponibles para usarlos en pasos posteriores del flujo de trabajo. 

Este es el esquema JSON completo:

```json
{
   "type": "object",
   "properties": {
      "address": {
         "type": "string"
      },
      "title": {
         "type": "string"
      },
      "name": {
         "type": "string"
      }
   },
   "required": [
      "address",
      "title",
      "name"
   ]
}
```

## <a name="create-nested-workflows-for-logic-apps"></a>Creación de flujos de trabajo anidados para aplicaciones lógicas

Puede anidar los flujos de trabajo en la aplicación lógica mediante la adición de otras aplicaciones de lógica que pueden recibir solicitudes. Para incluir estas aplicaciones lógicas, agregue la acción **Azure Logic Apps - Elegir un flujo de trabajo de Logic Apps** al desencadenador. A continuación, puede seleccionar desde las aplicaciones lógicas elegibles.

![Adición de otra aplicación lógica](./media/logic-apps-http-endpoint/choose-logic-apps-workflow.png)

## <a name="call-or-trigger-logic-apps-through-http-endpoints"></a>Llamada o desencadenamiento de aplicaciones lógicas a través de puntos de conexión HTTP

Una vez que haya creado el punto de conexión HTTP, puede desencadenar la aplicación lógica mediante un método `POST` a la dirección URL completa. Las aplicaciones lógicas tienen compatibilidad integrada para los puntos de conexión de acceso directo.

## <a name="reference-content-from-an-incoming-request"></a>Referencia al contenido de una solicitud entrante

Si el tipo de contenido es `application/json`, puede hacer referencia a las propiedades desde la solicitud entrante. En caso contrario, el contenido se trata como una sola unidad binaria que se puede pasar a otras API. Para hacer referencia a este contenido dentro del flujo de trabajo, debe convertir ese contenido. Por ejemplo, si pasa contenido de `application/xml`, puede usar `@xpath()` para una extracción de XPath, o bien `@json()` para convertir de XML a JSON. Obtenga información sobre cómo [trabajar con tipos de contenido](../logic-apps/logic-apps-content-type.md).

Para obtener la salida de una solicitud entrante, puede usar la función `@triggerOutputs()`. La salida podría parecerse a este ejemplo:

```json
{
    "headers": {
        "content-type" : "application/json"
    },
    "body": {
        "myProperty" : "property value"
    }
}
```

Para tener acceso a la propiedad `body` de manera específica, puede usar el acceso directo `@triggerBody()`. 

## <a name="respond-to-requests"></a>Respuesta a solicitudes

Es posible que quiera responder a determinadas solicitudes que inician una aplicación lógica mediante la devolución de contenido al autor de la llamada. Para construir el código de estado, el encabezado y el cuerpo de la respuesta, puede usar la acción **Respuesta**. Esta acción puede aparecer en cualquier lugar de la aplicación lógica, no solo al final del flujo de trabajo.

> [!NOTE] 
> Si la aplicación lógica no incluye una **Respuesta**, el punto de conexión HTTP responde *inmediatamente* con un estado **202 - Aceptado**. Además, para que la solicitud original obtenga la respuesta, todos los pasos necesarios para la respuesta deben terminar dentro del [límite del tiempo de espera de la solicitud](./logic-apps-limits-and-config.md), a menos que llame al flujo de trabajo como una aplicación lógica anidada. Si no se obtiene ninguna respuesta dentro de este límite, la solicitud entrante agota el tiempo de espera y recibe una respuesta HTTP **408 - Tiempo de espera del cliente agotado**. En cuanto a las aplicaciones lógicas anidadas, la aplicación lógica principal seguirá esperando una respuesta hasta que finalice, independientemente del tiempo que se necesite.

### <a name="construct-the-response"></a>Construcción de la respuesta

Puede incluir más de un encabezado y cualquier tipo de contenido en el cuerpo de respuesta. En la respuesta de ejemplo, el encabezado especifica que la respuesta tiene el tipo de contenido `application/json`. y el cuerpo contiene `title` y `name`, según el esquema JSON actualizado anteriormente para el desencadenador **Request**.

![Acción de respuesta HTTP][3]

Las respuestas tienen estas propiedades:

| Propiedad | Description |
| --- | --- |
| statusCode |Especifica el código de estado HTTP para responder a la solicitud entrante. Este código puede ser cualquier código de estado válido que comience con 2xx, 4xx o 5xx. En cambio, no se permiten códigos de estado 3xx. |
| encabezados |Define cualquier número de encabezados para incluirse en la respuesta. |
| body |Especifica un objeto de cuerpo que puede ser una cadena, un objeto JSON o incluso contenido binario al que se hace referencia desde un paso anterior. |

A continuación se presenta el aspecto del esquema JSON ahora para la acción **Response**:

``` json
"Response": {
   "inputs": {
      "body": {
         "title": "@{triggerBody()?['title']}",
         "name": "@{triggerBody()?['name']}"
      },
      "headers": {
           "content-type": "application/json"
      },
      "statusCode": 200
   },
   "runAfter": {},
   "type": "Response"
}
```

> [!TIP]
> Para ver la definición de JSON completa de la aplicación lógica, en el Diseñador de aplicación lógica, elija **Vista Código**.

## <a name="q--a"></a>Preguntas y respuestas

#### <a name="q-what-about-url-security"></a>P: ¿¿Qué se puede decir sobre la seguridad de las direcciones URL?

R: Azure genera direcciones URL de devolución de llamada de la aplicación lógica de manera segura con una firma de acceso compartido (SAS). Esta firma pasa como un parámetro de consulta y debe validarse antes de que se pueda activar la aplicación lógica. Azure genera la firma mediante una combinación única de una clave secreta por aplicación lógica, el nombre del desencadenador y la operación que se realiza. Por tanto, a menos que alguien tenga acceso a la clave de aplicación lógica secreta, no se puede generar una firma válida.

   > [!IMPORTANT]
   > Para sistemas seguros y de producción, no se recomienda que llame a su aplicación lógica directamente desde el explorador por los siguientes motivos:
   > 
   > * La clave de acceso compartido aparece en la dirección URL.
   > * No puede administrar las directivas de contenido seguro debido a los dominios compartidos entre los clientes de Logic App.

#### <a name="q-can-i-configure-http-endpoints-further"></a>P: ¿Puedo configurar más puntos de conexión HTTP?

R: Sí, los puntos de conexión HTTP admiten una configuración más avanzada a través de [**API Management**](../api-management/api-management-key-concepts.md). Este servicio también ofrece la funcionalidad de administrar de forma coherente todas las API, incluidas las aplicaciones lógicas, configurar nombres de dominio personalizados, usar varios métodos de autenticación y muchas más, como, por ejemplo:

* [Cambio del método de solicitud](https://docs.microsoft.com/azure/api-management/api-management-advanced-policies#SetRequestMethod)
* [Cambio de los segmentos de dirección URL de la solicitud](https://docs.microsoft.com/azure/api-management/api-management-transformation-policies#RewriteURL)
* Configuración de los dominios de API Management en [Azure Portal](https://portal.azure.com/ "Azure Portal")
* Configuración de la directiva para comprobar la autenticación básica

#### <a name="q-what-changed-when-the-schema-migrated-from-the-december-1-2014-preview"></a>P: ¿Qué ha cambiado cuando se migró el esquema desde la versión preliminar de 1 de diciembre de 2014?

R: Este es un resumen sobre estos cambios:

| Versión preliminar de 1 de diciembre de 2014 | 1 de junio de 2016 |
| --- | --- |
| Haga clic en la aplicación de API **Agente de escucha HTTP**. |Haga clic en **Desencadenador manual** (no se necesita ninguna aplicación de API). |
| Configuración del Agente de escucha HTTP "*Enviar respuesta automáticamente*" |Tanto si incluye una acción de **Respuesta** en la definición del flujo de trabajo como si no |
| Configuración de la autenticación básica o OAuth |mediante API Management |
| Configuración del método HTTP |En **Mostrar opciones avanzadas**, elija un método HTTP |
| Configuración de la ruta de acceso relativa |En **Mostrar opciones avanzadas**, agregue una ruta de acceso relativa |
| Referencia al cuerpo entrante mediante `@triggerOutputs().body.Content` |Referencia a través de `@triggerOutputs().body` |
| **Enviar respuesta HTTP** del Agente de escucha HTTP |Haga clic en **Responder a la solicitud HTTP** (no se necesita ninguna aplicación de API) |

## <a name="get-help"></a>Obtener ayuda

Para formular preguntas, o responderlas, y saber lo que hacen otros usuarios de Azure Logic Apps, visite el [foro de Azure Logic Apps](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps).

Para ayudar a mejorar Azure Logic Apps y los conectores, vote o envíe ideas en el [sitio de comentarios de usuario de Azure Logic Apps](http://aka.ms/logicapps-wish).

## <a name="next-steps"></a>Pasos siguientes

* [Creación de definiciones de aplicación lógica](./logic-apps-author-definitions.md)
* [Control de errores y excepciones](./logic-apps-exception-handling.md)

[1]: ./media/logic-apps-http-endpoint/manualtrigger.png
[2]: ./media/logic-apps-http-endpoint/manualtriggerurl.png
[3]: ./media/logic-apps-http-endpoint/response.png
