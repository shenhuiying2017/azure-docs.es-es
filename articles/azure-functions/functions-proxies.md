---
title: Uso de servidores proxy en Azure Functions | Microsoft Docs
description: "Información general sobre cómo usar Azure Functions Proxies"
services: functions
documentationcenter: 
author: alexkarcher-msft
manager: cfowler
editor: 
ms.assetid: 
ms.service: functions
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: multiple
ms.topic: article
ms.date: 04/11/2017
ms.author: alkarche
ms.openlocfilehash: dd022b189783f2d8c6209a6cd656704ff144bfd6
ms.sourcegitcommit: 4256ebfe683b08fedd1a63937328931a5d35b157
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/23/2017
---
# <a name="work-with-azure-functions-proxies"></a>Uso de Azure Functions Proxies

En este artículo se explica cómo configurar y usar Azure Functions Proxies. Con esta característica, puede especificar puntos de conexión en su aplicación de función que se implementan con otro recurso. Puede utilizar a estos servidores proxy para dividir una API grande en varias Function App (como en una arquitectura de microservicio), mientras que presenta una superficie de API única para los clientes.

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

> [!NOTE] 
> Se aplica la facturación estándar de Functions a las ejecuciones de proxy. Para más información, consulte los [precios de Azure Functions](https://azure.microsoft.com/pricing/details/functions/).

## <a name="create"></a>Creación de un proxy

En esta sección se explica cómo crear un proxy en el portal de Functions.

1. Abra [Azure Portal] y vaya a la aplicación de función.
2. En el panel izquierdo, seleccione **Nuevo servidor proxy**.
3. Proporcione un nombre para el proxy.
4. Configure el punto de conexión expuesto en esta aplicación de función; para ello, especifique la **plantilla de ruta** y los **métodos HTTP**. Estos parámetros se comportan según las reglas de los [desencadenadores HTTP].
5. Establezca la **dirección URL de back-end** en otro punto de conexión. El punto de conexión podría tratarse de una función en otra aplicación de función o podría ser cualquier otra API. El valor no necesita ser estático y puede hacer referencia a la [configuración de la aplicación] y a [parámetros de la solicitud de cliente original].
6. Haga clic en **Create**(Crear).

El proxy ahora existe como un nuevo punto de conexión en la Function App. Desde una perspectiva de cliente, es equivalente a un HttpTrigger en Azure Functions. Puede probar el nuevo proxy; para ello, copie la dirección URL del proxy y pruébela con el cliente HTTP favorito.

## <a name="modify-requests-responses"></a>Modificación de solicitudes y respuestas

Con Azure Functions Proxies, puede modificar solicitudes y respuestas del back-end. Estas transformaciones pueden usar variables, como se define en [Uso de variables].

### <a name="modify-backend-request"></a>Modificación de la solicitud de back-end

De forma predeterminada, la solicitud de back-end se inicializa como copia de la solicitud original. Además de establecer la dirección URL de back-end, se pueden realizar cambios en el método HTTP, los encabezados y los parámetros de cadena de consulta. Los valores modificados pueden hacer referencia a la [configuración de la aplicación] y a los [parámetros de la solicitud de cliente original].

Actualmente, no existe ninguna experiencia de portal para modificar las solicitudes de back-end. Para aprender a aplicar esta funcionalidad desde *proxies.json*, consulte [Definición de un objeto requestOverrides].

### <a name="modify-response"></a>Modificación de la respuesta

De forma predeterminada, la respuesta del cliente se inicializa como copia de la respuesta de back-end. Puede realizar cambios en el código de estado, la expresión del motivo, los encabezados y el cuerpo de la respuesta. Los valores modificados pueden hacer referencia a la [configuración de la aplicación], a [parámetros de la solicitud de cliente original] y a [parámetros de la respuesta de back-end].

Actualmente, no existe ninguna experiencia de portal para modificar respuestas. Para aprender a aplicar esta funcionalidad desde *proxies.json*, consulte [Definición de un objeto responseOverrides].

## <a name="using-variables"></a>Uso de variables

La configuración de un proxy no necesita ser estática. Puede condicionarlo para que use variables de la solicitud de cliente original, la respuesta de back-end o la configuración de la aplicación.

### <a name="request-parameters"></a>Referencia a los parámetros de solicitud

Puede usar parámetros de solicitud como entradas para la propiedad de dirección URL de back-end o como parte de la modificación de solicitudes y respuestas. Algunos parámetros pueden enlazarse desde la plantilla de ruta especificada en la configuración de proxy base, y otros pueden proceder de propiedades de la solicitud entrante.

#### <a name="route-template-parameters"></a>Parámetros de plantilla de ruta
Los parámetros que se usan en la plantilla de ruta están disponibles para hacer referencia a ellos por su nombre. Los nombres de parámetro van rodeados de llaves ({}).

Por ejemplo, si un proxy tiene una plantilla de ruta, como `/pets/{petId}`, la dirección URL de back-end puede incluir el valor de `{petId}`, como en `https://<AnotherApp>.azurewebsites.net/api/pets/{petId}`. Si la plantilla de ruta finaliza en un carácter comodín, como `/api/{*restOfPath}`, el valor `{restOfPath}` es una representación de cadena de los segmentos de ruta de acceso restantes procedentes de la solicitud entrante.

#### <a name="additional-request-parameters"></a>Parámetros de solicitud adicionales
Además de los parámetros de la plantilla de ruta, pueden usarse los siguientes valores en la configuración:

* **{request.method}**: método HTTP que se usa en la solicitud original.
* **{request.headers.\<nombreDeEncabezado\>}**: encabezado que puede leerse desde la solicitud original. Reemplace *\<nombreDeEncabezado\>* por el nombre del encabezado que desea leer. Si el encabezado no se incluye en la solicitud, el valor será una cadena vacía.
* **{request.querystring.\<nombreDeParámetro\>}**: parámetro de cadena de consulta que se puede leer desde la solicitud original. Reemplace *\<nombreDeParámetro\>* por el nombre del parámetro que desea leer. Si el parámetro no se incluye en la solicitud, el valor será una cadena vacía.

### <a name="response-parameters"></a>Referencia a parámetros de respuesta de back-end

Los parámetros de respuesta pueden utilizarse como parte de la modificación de la respuesta al cliente. Se pueden usar los siguientes valores en la configuración:

* **{backend.response.statusCode}**: código de estado HTTP que se devuelve en la respuesta de back-end.
* **{backend.response.statusReason}**: la frase de motivo HTTP que se devuelve en la respuesta de back-end.
* **{backend.response.headers.\<nombreDeEncabezado\>}**: encabezado que puede leerse desde la respuesta de back-end. Reemplace *\<nombreDeEncabezado\>* por el nombre del encabezado que desea leer. Si el encabezado no se incluye en la respuesta, el valor será una cadena vacía.

### <a name="use-appsettings"></a>Referencia a la configuración de la aplicación

También puede hacer referencia a la [configuración de la aplicación definida para la aplicación de función](https://docs.microsoft.com/azure/azure-functions/functions-how-to-use-azure-function-app-settings#develop) rodeando el nombre de la configuración con signos de porcentaje (%).

Por ejemplo, en una dirección URL de back-end *https://%ORDER_PROCESSING_HOST%/api/orders*, "%ORDER_PROCESSING_HOST%" se reemplaza por el valor de la configuración de ORDER_PROCESSING_HOST.

> [!TIP] 
> Use la configuración de la aplicación para hosts back-end si tiene varias implementaciones o entornos de prueba. De este modo, puede asegurarse de que siempre se comunica con el back-end correcto para ese entorno.

## <a name="advanced-configuration"></a>Configuración avanzada

Los servidores proxy que configura se almacenan en un archivo *proxies.json*, ubicado en la raíz de un directorio de la aplicación de función. Puede editar este archivo manualmente e implementarlo como parte de la aplicación cuando se utiliza cualquiera de los [métodos de implementación](https://docs.microsoft.com/azure/azure-functions/functions-continuous-deployment) compatibles con Functions. La característica Azure Functions Proxies debe estar [habilitada](#enable) para poder procesar el archivo. 

> [!TIP] 
> Si no ha configurado alguno de los métodos de implementación, también puede trabajar con el archivo *proxies.json* en el portal. Vaya a la aplicación de función y seleccione **Características de la plataforma** y **Editor de App Service**. Al hacerlo, puede ver toda la estructura de archivos de la aplicación de función y realizar cambios.

El archivo *proxies.json* lo define un objeto de servidores proxy, compuesto de servidores proxy con nombre y de sus definiciones. De forma opcional, si el editor lo admite, puede hacer referencia a un [esquema JSON](http://json.schemastore.org/proxies) para completar el código. Un archivo de ejemplo puede tener el siguiente aspecto:

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

Cada proxy tiene un nombre descriptivo, como *proxy1* en el ejemplo anterior. El objeto de definición de proxy correspondiente se define mediante las siguientes propiedades:

* **matchCondition**: (requerida) un objeto que define las solicitudes que desencadenan la ejecución de este proxy. Contiene dos propiedades compartidas con los [desencadenadores HTTP]:
    * _methods_: una matriz de los métodos HTTP a los que responde el servidor proxy. Si no se especifica, el proxy responde a todos los métodos HTTP de la ruta.
    * _route_: (requerida) define la plantilla de ruta y controla a qué direcciones URL de solicitud responde el proxy. A diferencia de los desencadenadores HTTP, no hay ningún valor predeterminado.
* **backendUri**: la dirección URL del recurso de back-end a la que se redirigirá la solicitud mediante proxy. Este valor puede hacer referencia a la configuración de la aplicación y a parámetros de la solicitud de cliente original. Si no se incluye esta propiedad, Azure Functions responde con el mensaje HTTP 200 - Correcto.
* **requestOverrides**: objeto que define transformaciones a la solicitud de back-end. Consulte [Definición de un objeto requestOverrides].
* **responseOverrides**: objeto que define transformaciones a la respuesta del cliente. Consulte [Definición de un objeto responseOverrides].

> [!NOTE] 
> La propiedad *route* de Azure Functions Proxies no respeta la propiedad *routePrefix* de la configuración del host de Function App. Si quiere incluir un prefijo como `/api`, se debe incluir en la propiedad *route*.

### <a name="requestOverrides"></a>Definición de un objeto requestOverrides

El objeto requestOverrides define los cambios realizados en la solicitud cuando se llama al recurso de back-end. El objeto se define mediante las siguientes propiedades:

* **backend.request.method**: método HTTP que se usa para llamar al back-end.
* **backend.request.querystring.\<nombreDeParámetro\>**: parámetro de cadena de consulta que se puede establecer para llamar al back-end. Reemplace *\<nombreDeParámetro\>* por el nombre del parámetro que desea establecer. Si se proporciona una cadena vacía, el parámetro no se incluye en la solicitud de back-end.
* **backend.request.headers.\<nombreDeEncabezado\>**: encabezado que se puede establecer para llamar al back end. Reemplace *\<nombreDeEncabezado\>* por el nombre del encabezado que desea establecer. Si se proporciona una cadena vacía, el encabezado no se incluye en la solicitud de back-end.

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
                "backend.request.headers.Accept": "application/xml",
                "backend.request.headers.x-functions-key": "%ANOTHERAPP_API_KEY%"
            }
        }
    }
}
```

### <a name="responseOverrides"></a>Definición de un objeto responseOverrides

El objeto requestOverrides define los cambios realizados en la respuesta que se pasa de nuevo al cliente. El objeto se define mediante las siguientes propiedades:

* **response.statusCode**: código de estado HTTP que se va a devolver al cliente.
* **response.statusReason**: frase de motivo HTTP que se va a devolver al cliente.
* **response.body**: representación de cadena del cuerpo que se va a devolver al cliente.
* **response.headers.\<nombreDeEncabezado\>**: encabezado que se puede establecer para la respuesta al cliente. Reemplace *\<nombreDeEncabezado\>* por el nombre del encabezado que desea establecer. Si se proporciona una cadena vacía, el encabezado no se incluye en la respuesta.

Los valores pueden hacer referencia a la configuración de la aplicación, a parámetros de la solicitud de cliente original y a parámetros de la respuesta de back-end.

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
                "response.headers.Content-Type": "text/plain"
            }
        }
    }
}
```
> [!NOTE] 
> En este ejemplo, el cuerpo de la respuesta se define directamente, por lo que no se necesita ninguna propiedad `backendUri`. En el ejemplo, se muestra cómo usar Azure Functions Proxies para simular las API.

## <a name="enable"></a>Habilitación de Azure Functions Proxies

Los servidores proxy no están habilitados de forma predeterminada. Si usaba una versión anterior de la versión preliminar de los servidores proxy y los deshabilitó, tendrá que habilitarlos manualmente una vez, y en orden, para que puedan ejecutarse.

1. Abra [Azure Portal] y vaya a la aplicación de función.
2. Seleccione **Configuración de Function App**.
3. Cambie **Habilitar Azure Functions Proxies (versión preliminar)** a **Activado**.

También puede volver aquí para actualizar el tiempo de ejecución del proxy a medida que haya nuevas características disponibles.

[Azure Portal]: https://portal.azure.com
[desencadenadores HTTP]: https://docs.microsoft.com/azure/azure-functions/functions-bindings-http-webhook#http-trigger
[Modify the back-end request]: #modify-backend-request
[Modify the response]: #modify-response
[Definición de un objeto requestOverrides]: #requestOverrides
[Definición de un objeto responseOverrides]: #responseOverrides
[configuración de la aplicación]: #use-appsettings
[Uso de variables]: #using-variables
[parámetros de la solicitud de cliente original]: #request-parameters
[parámetros de la respuesta de back-end]: #response-parameters
