---
title: Trabajo con servidores proxy en Azure Functions | Microsoft Docs
description: "Información general sobre cómo usar Azure Functions Proxies"
services: functions
documentationcenter: 
author: mattchenderson
manager: erikre
editor: 
ms.assetid: 
ms.service: functions
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: multiple
ms.topic: article
ms.date: 04/11/2017
ms.author: mahender
ms.translationtype: Human Translation
ms.sourcegitcommit: be3ac7755934bca00190db6e21b6527c91a77ec2
ms.openlocfilehash: 6a0a81a011d9a1cc1a8a81ba8ef92d90308c534d
ms.contentlocale: es-es
ms.lasthandoff: 05/03/2017


---
# <a name="working-with-azure-functions-proxies-preview"></a>Trabajo con Azure Functions Proxies (versión preliminar)

> [!Note] 
> Azure Functions Proxies actualmente se encuentra disponible en versión preliminar. La versión preliminar es gratuita, pero se aplica la facturación estándar de Functions a las ejecuciones de proxy. Para más información, vea los [precios de Azure Functions](https://azure.microsoft.com/pricing/details/functions/).

En este artículo se explica cómo configurar y usar Azure Functions Proxies. Esta característica permite especificar puntos de conexión en la Function App que otro recurso implementa. Puede utilizar a estos servidores proxy para dividir una API grande en varias Function App (como en una arquitectura de microservicio), mientras que presenta una superficie de API única para los clientes.

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]


## <a name="enable"></a>Habilitar Azure Functions Proxies

Los servidores proxy no están habilitados de forma predeterminada. Puede crear servidores proxy mientras la característica está deshabilitada, o no se ejecutarán. En los pasos siguientes se explica cómo habilitar los servidores proxy:

1. Abra [Azure Portal] y vaya a la Function App.
2. Seleccione **Configuración de Function App**.
3. Cambie **Enable Azure Functions Proxies (preview)** [Habilitar Azure Functions Proxies (versión preliminar)] a activado.

También puede volver aquí para actualizar el tiempo de ejecución del proxy a medida que haya nuevas características disponibles.


## <a name="create"></a>Creación de un proxy

En esta sección se explica cómo crear un proxy en el portal de Functions.

1. Abra [Azure Portal] y vaya a la Function App.
2. En el panel de navegación izquierdo, seleccione **Nuevo proxy**.
3. Proporcione un nombre para el proxy.
4. Configure el punto de conexión expuesto en esta Function App; para ello, especifique la **plantilla de ruta** y los **métodos HTTP**. Estos parámetros se comportan según las reglas de los [desencadenadores HTTP].
5. Establezca la **dirección URL de back-end** en otro punto de conexión. Podría tratarse de una función en otra Function App, o podría ser cualquier otra API. El valor no necesita ser estático y puede hacer referencia a la [configuración de la aplicación] y a los [parámetros de la solicitud de cliente original].
6. Haga clic en Crear.

El proxy ahora existe como un nuevo punto de conexión en la Function App. Desde una perspectiva de cliente, es equivalente a un HttpTrigger en Azure Functions. Puede probar el nuevo proxy; para ello, copie la dirección URL del proxy y pruébela con el cliente HTTP favorito.








## <a name="modify-requests-responses"></a>Modificación de solicitudes y respuestas

Azure Functions Proxies permite modificar solicitudes y respuestas en back-end. Estas transformaciones pueden usar variables, como se define en [Uso de variables].

### <a name="modify-backend-request"></a>Modificación de solicitudes de back-end

De forma predeterminada, la solicitud de back-end se inicializa como una copia de la solicitud original. Además de establecer la dirección URL de back-end, también se pueden realizar cambios en el método HTTP, los encabezados y los parámetros de cadena de consulta. Los valores modificados pueden hacer referencia a la [configuración de la aplicación] y a los [parámetros de la solicitud de cliente original].

Actualmente no existe ninguna experiencia en el portal para modificar las solicitudes de back-end. Vea [Definición de un objeto requestOverrides] para obtener información sobre cómo usar esta funcionalidad de proxies.json.

### <a name="modify-response"></a>Modificación de la respuesta

De forma predeterminada, la respuesta del cliente se inicializa como una copia de la respuesta de back-end. Puede realizar cambios en el código de estado, la expresión del motivo, los encabezados y el cuerpo de la respuesta. Los valores modificados pueden hacer referencia a la [configuración de la aplicación], a los [parámetros de la solicitud de cliente original] y a los [parámetros de la respuesta de back-end].

Actualmente no existe ninguna experiencia en el portal para modificar las respuestas. Vea [Definición de un objeto responseOverrides] para obtener información sobre cómo usar esta funcionalidad de proxies.json.






## <a name="using-variables"></a>Uso de variables

La configuración de un proxy no necesita ser estática. Puede condicionarlo para usar variables de la solicitud original, la respuesta de back-end o la configuración de la aplicación.

### <a name="request-parameters"></a>Referencia a los parámetros de solicitud

Los parámetros de solicitud pueden utilizarse como entradas para la propiedad de dirección URL de back-end o como parte de la modificación de las solicitudes y respuestas. Algunos parámetros pueden enlazarse desde la plantilla de ruta especificada en la configuración del proxy de base, mientras que otros procederán de propiedades de la solicitud entrante.

#### <a name="route-template-parameters"></a>Parámetros de plantilla de ruta
Los parámetros usados en la plantilla de ruta están disponibles para hacer referencia a ellos por su nombre, entre llaves "{}".

Por ejemplo, si un proxy tiene una plantilla de ruta como `/pets/{petId}`, la dirección URL de back-end puede incluir el valor de `{petId}`, como en `https://<AnotherApp>.azurewebsites.net/api/pets/{petId}`. Si la plantilla de ruta finaliza en un carácter comodín, como `/api/{*restOfPath}`, el valor `{restOfPath}` será una representación de cadena de los segmentos de ruta de acceso restantes de la solicitud entrante.

#### <a name="additonal-request-parameters"></a>Parámetros de solicitud adicionales
Además de los parámetros de la plantilla de ruta, los siguientes valores pueden usarse en los valores de configuración:

* **{request.method}**: método HTTP usado en la solicitud original.
* **{request.headers.&lt;HeaderName&gt;}**: encabezado que puede leerse desde la solicitud original. Reemplace "&lt;HeaderName&gt;" con el nombre del encabezado que desea leer. Si el encabezado no se incluye en la solicitud, el valor será una cadena vacía.
* **{request.querystring.&lt;ParameterName&gt;}**: parámetro de cadena de consulta que se puede leer desde la solicitud original. Reemplace "&lt;ParameterName&gt;" con el nombre del parámetro que desea leer. Si el parámetro no se incluye en la solicitud, el valor será una cadena vacía.

### <a name="response-parameters"></a>Referencia a parámetros de respuesta de back-end

Los parámetros de respuesta pueden utilizarse como parte de la modificación de la respuesta al cliente. Los siguientes valores pueden utilizarse en los valores de configuración:

* **{backend.response.statusCode}**: código de estado HTTP devuelto en la respuesta de back-end.
* **{backend.response.statusReason}**: expresión del motivo HTTP devuelta en la respuesta de back-end.
* **{backend.response.headers.&lt;HeaderName&gt;}**: encabezado que puede leerse desde la respuesta de back-end. Reemplace "&lt;HeaderName&gt;" con el nombre del encabezado que desea leer. Si el encabezado no se incluye en la solicitud, el valor será una cadena vacía.

### <a name="use-appsettings"></a>Referencia a la configuración de la aplicación

También puede hacer referencia a la [configuración de la aplicación definida para Function App](https://docs.microsoft.com/azure/azure-functions/functions-how-to-use-azure-function-app-settings#develop) rodeando el nombre de la configuración con signos de porcentaje "%".

Por ejemplo, en una dirección URL de back-end de `https://%ORDER_PROCESSING_HOST%/api/orders`, "%ORDER_PROCESSING_HOST%" se reemplaza por el valor de la configuración de ORDER_PROCESSING_HOST.

> [!TIP] 
> Use la configuración de la aplicación para hosts back-end si tiene varias implementaciones o entornos de prueba. De este modo, puede asegurarse de que siempre habla con el back-end correcto para ese entorno.





## <a name="advanced-configuration"></a>Configuración avanzada

Los servidores proxy que configure se almacenan en un archivo proxies.json, ubicado en la raíz de un directorio de la Function App. Puede editar este archivo manualmente e implementarlo como parte de la aplicación cuando se utiliza cualquiera de los [métodos de implementación](https://docs.microsoft.com/azure/azure-functions/functions-continuous-deployment) compatibles con Functions. La característica debe estar [habilitada](#enable) para poder procesar el archivo. 

> [!TIP] 
> Si no ha configurado alguno de los métodos de implementación, también puede trabajar con el archivo proxies.json en el portal. Vaya a Function App, seleccione "Características de la plataforma" y luego "Editor de App Service". De esta forma, podrá ver la estructura completa del archivo de Function App y realizar cambios.

El archivo proxies.json lo define un objeto de servidores proxy, compuesto de servidores proxy con nombre y de sus definiciones. De forma opcional, puede hacer referencia a un [esquema JSON](http://json.schemastore.org/proxies) para completar el código si el editor lo admite. Un archivo de ejemplo puede tener el siguiente aspecto:

```json
{
    "$schema": "http://json.schemastore.org/proxies",
    "proxies": {
        "proxy1": {
            "matchCondition": {
                "methods": [ "GET" ],
                "route": "/api/{test}"
            },
            "backendUri": "https://<AnotherApp>.azurewebsites.net/api/<FunctionName>"
        }
    }
}
```

Cada proxy tiene un nombre descriptivo, como "proxy1" en el ejemplo anterior. El objeto de definición de proxy correspondiente se define mediante las siguientes propiedades:

* **matchCondition**: (requerida) un objeto que define las solicitudes que desencadenarán la ejecución de este proxy. Contiene dos propiedades compartidas con [desencadenadores HTTP]:
    * _methods_: se trata de una matriz de los métodos HTTP a los que responderá el proxy. Si no se especifica, el proxy responderá a todos los métodos HTTP de la ruta.
    * _route_: (requerida) define la plantilla de ruta y controla a qué direcciones URL de solicitud responderá el proxy. A diferencia de los desencadenadores HTTP, no hay ningún valor predeterminado.
* **backendUri**: la dirección URL del recurso de back-end a la que se redirigirá la solicitud mediante proxy. Este valor puede hacer referencia a la configuración de la aplicación y a los parámetros de la solicitud de cliente original. Si esta propiedad no está incluida, Azure Functions responderá con el mensaje HTTP 200 OK (HTTP 200 correcto).
* **requestOverrides**: objeto que define las transformaciones a la solicitud de back-end. Vea [Definición de un objeto requestOverrides].
* **responseOverrides**: objeto que define las transformaciones a la respuesta del cliente. Vea [Definición de un objeto responseOverrides].

> [!Note] 
> La propiedad de ruta Azure Functions Proxies no respeta la propiedad routePrefix de la configuración del host de Functions. Si desea incluir un prefijo como /api, se debe incluir en la propiedad de ruta.

### <a name="requestOverrides"></a>Definición de un objeto requestOverrides

El objeto requestOverrides define los cambios realizados en la solicitud cuando se llama al recurso de back-end. El objeto se define mediante las siguientes propiedades:

* **backend.request.method**: método HTTP que se usará para llamar al back-end.
* **backend.request.querystring.&lt;ParameterName&gt;**: parámetro de cadena de consulta que se puede definir para llamar al back-end. Reemplace "&lt;ParameterName&gt;" con el nombre del parámetro que desea definir. Si se proporciona una cadena vacía, el parámetro no se incluirá en la solicitud de back-end.
* **backend.request.headers.&lt;HeaderName&gt;**: encabezado que puede definirse para llamar al back-end. Reemplace "&lt;HeaderName&gt;" con el nombre del encabezado que desea establecer. Si se proporciona una cadena vacía, el encabezado no se incluirá en la solicitud de back-end.

Los valores pueden hacer referencia a la configuración de la aplicación y a los parámetros de la solicitud de cliente original.

Una configuración de ejemplo puede tener el siguiente aspecto:

```json
{
    "$schema": "http://json.schemastore.org/proxies",
    "proxies": {
        "proxy1": {
            "matchCondition": {
                "methods": [ "GET" ],
                "route": "/api/{test}"
            },
            "backendUri": "https://<AnotherApp>.azurewebsites.net/api/<FunctionName>",
            "requestOverrides": {
                "backend.request.headers.Accept" : "application/xml",
                "backend.request.headers.x-functions-key" : "%ANOTHERAPP_API_KEY%"
            }
        }
    }
}
```

### <a name="responseOverrides"></a>Definición de un objeto responseOverrides

El objeto requestOverrides define los cambios realizados en la respuesta que se pasa de nuevo al cliente. El objeto se define mediante las siguientes propiedades:

* **response.statusCode**: código de estado HTTP que se va a devolver al cliente.
* **response.statusReason**: expresión del motivo HTTP que se va a devolver al cliente.
* **response.body**: representación de cadena del cuerpo que se va a devolver al cliente.
* **response.headers.&lt;HeaderName&gt;**: encabezado que se puede definir para la respuesta al cliente. Reemplace "&lt;HeaderName&gt;" con el nombre del encabezado que desea establecer. Si se proporciona una cadena vacía, el encabezado no se incluirá en la respuesta.

Los valores pueden hacer referencia a la configuración de la aplicación, a los parámetros de la solicitud de cliente original y a los parámetros de la respuesta de back-end.

Una configuración de ejemplo puede tener el siguiente aspecto:

```json
{
    "$schema": "http://json.schemastore.org/proxies",
    "proxies": {
        "proxy1": {
            "matchCondition": {
                "methods": [ "GET" ],
                "route": "/api/{test}"
            },
            "responseOverrides": {
                "response.body": "Hello, {test}",
                "response.headers.Content-Type" : "text/plain"
            }
        }
    }
}
```
> [!Note] 
> En este ejemplo, el cuerpo se define directamente, por lo que no se necesita la propiedad `backendUri`. Se trata de un ejemplo sobre cómo se puede usar Azure Functions Proxies para las API de simulación.

[Azure Portal]: https://portal.azure.com
[desencadenadores HTTP]: https://docs.microsoft.com/azure/azure-functions/functions-bindings-http-webhook#http-trigger
[Modifying the backend request]: #modify-backend-request
[Modifying the response]: #modify-response
[Definición de un objeto requestOverrides]: #requestOverrides
[Definición de un objeto responseOverrides]: #responseOverrides
[configuración de la aplicación]: #use-appsettings
[Uso de variables]: #using-variables
[parámetros de la solicitud de cliente original]: #request-parameters
[parámetros de la respuesta de back-end]: #response-parameters
