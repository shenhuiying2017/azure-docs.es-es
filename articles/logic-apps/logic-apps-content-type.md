---
title: "Administración de los tipos de contenido: Azure Logic Apps | Microsoft Docs"
description: "Administración de Azure Logic Apps de los tipos de contenido en los procesos de diseño y runtime"
services: logic-apps
documentationcenter: .net,nodejs,java
author: jeffhollan
manager: anneta
editor: 
ms.assetid: cd1f08fd-8cde-4afc-86ff-2e5738cc8288
ms.service: logic-apps
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: integration
ms.date: 10/18/2016
ms.author: LADocs; jehollan
ms.openlocfilehash: ac67838344bbd10384299c086ff096fbe5dec6a9
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="handle-content-types-in-logic-apps"></a>Administración de los tipos de contenido en las aplicaciones lógicas

Muchos tipos diferentes de contenido pueden pasar a través de una aplicación lógica, como JSON, XML, archivos planos y datos binarios. Aunque el motor de Logic App admite todos los tipos de contenido, algunos los entiende de manera inherente. Otros pueden requerir conversiones. En este artículo se describe cómo administra el motor distintos tipos de contenido y cómo se administran correctamente estos tipos cuando proceda.

## <a name="content-type-header"></a>Encabezado Content-Type

Para empezar por lo básico, echemos un vistazo a los dos `Content-Types` que no requieren conversión para usarlos en una aplicación lógica: `application/json` y `text/plain`.

## <a name="applicationjson"></a>Application/JSON

El motor de flujo de trabajo se basa en el encabezado `Content-Type` de las llamadas HTTP para determinar el control adecuado. Cualquier solicitud con el tipo de contenido `application/json` se almacena y administra como objeto JSON. Además, se puede analizar el contenido JSON de forma predeterminada sin necesidad de conversión. 

Por ejemplo, podría analizar una solicitud con el encabezado de tipo de contenido `application/json ` en un flujo de trabajo con una expresión como `@body('myAction')['foo'][0]` para obtener el valor `bar` en este caso:

```
{
    "data": "a",
    "foo": [
        "bar"
    ]
}
```

No se requieren procesos de conversión adicionales. Si trabaja con datos JSON, pero no tienen especificado un encabezado, puede convertirlos manualmente a JSON mediante la función `@json()`, por ejemplo, `@json(triggerBody())['foo']`.

### <a name="schema-and-schema-generator"></a>Esquema y generador de esquemas

El desencadenador de solicitud le permite incorporar un esquema JSON para la carga útil que espera recibir. Este esquema permite al diseñador generar tokens para ayudar al usuario a consumir el contenido de la solicitud. Si no tiene listo un esquema, seleccione **Usar una carga de ejemplo para generar el esquema** para generar un esquema de JSON a partir de una carga de ejemplo.

![SCHEMA (ESQUEMA)](./media/logic-apps-http-endpoint/manualtrigger.png)

### <a name="parse-json-action"></a>Acción "Parse JSON"

La acción `Parse JSON` le permite analizar el contenido JSON en tokens descriptivos para el consumo de las aplicaciones lógicas. Al igual que el desencadenador de solicitud, esta acción le permite especificar o generar un esquema JSON para el contenido que desee analizar. Esta herramienta facilita enormemente el consumo de datos de Service Bus, Azure Cosmos DB, etc.

![Parse JSON](./media/logic-apps-content-type/ParseJSON.png)

## <a name="textplain"></a>Text/plain

De forma similar a `application/json`, los mensajes HTTP recibidos con el encabezado `Content-Type` de `text/plain` se almacenan en formato sin procesar. Además, si los mensajes están incluidos en las acciones posteriores sin conversión alguna, las solicitudes salen con el encabezado `Content-Type`: `text/plain`. Por ejemplo, al trabajar con un archivo plano, podría obtener este contenido HTTP como `text/plain`:

```
Date,Name,Address
Oct-1,Frank,123 Ave.
```

Si en la siguiente acción, envía la solicitud como cuerpo de otra solicitud (`@body('flatfile')`), esta tendría un encabezado Content-Type `text/plain`. Si trabaja con datos de texto sin formato, pero no tienen un encabezado especificado, puede convertirlos manualmente a texto mediante la función `@string()`, por ejemplo, `@string(triggerBody())`.

## <a name="applicationxml-and-applicationoctet-stream-and-converter-functions"></a>Application/xml, Application/octet-stream y funciones de conversión

El motor de Logic Apps siempre conserva el encabezado `Content-Type` que se recibiera en la solicitud o respuesta HTTP. Por tanto, si el motor recibe el contenido con `Content-Type` `application/octet-stream` y el usuario incluye ese contenido en una acción posterior sin conversión alguna, la solicitud de salida tiene `Content-Type`: `application/octet-stream`. De esta manera, el motor puede garantizar que no se pierden datos al pasar por el flujo de trabajo. Sin embargo, el estado de la acción (entradas y salidas) se almacena en un objeto JSON cuando este pasa por el flujo de trabajo. Por lo tanto, para conservar algunos tipos de datos, el motor convierte el contenido en una cadena con codificación binaria base64 con los metadatos adecuados que conserva `$content` y `$content-type`, que se convierten automáticamente. 

* `@json()`: convierte los datos a `application/json`.
* `@xml()`: convierte los datos a `application/xml`.
* `@binary()`: convierte los datos a `application/octet-stream`.
* `@string()`: convierte los datos a `text/plain`.
* `@base64()` : convierte el contenido a una cadena base64.
* `@base64toString()`: convierte una cadena codificada en base64 a `text/plain`.
* `@base64toBinary()`: convierte una cadena codificada en base64 a `application/octet-stream`.
* `@encodeDataUri()` : codifica una cadena como una matriz de bytes dataUri.
* `@decodeDataUri()` : descodifica dataUri en una matriz de bytes.

Por ejemplo, si recibió una solicitud HTTP con `Content-Type`: `application/xml`:

```
<?xml version="1.0" encoding="UTF-8" ?>
<CustomerName>Frank</CustomerName>
```

Podría convertirla y usarla posteriormente con `@xml(triggerBody())`, por ejemplo, o en una función como `@xpath(xml(triggerBody()), '/CustomerName')`.

## <a name="other-content-types"></a>Otros tipos de contenido

Otros tipos de contenido son compatibles con las aplicaciones lógicas, pero es posible que haya que descodificar `$content` para recuperar manualmente el cuerpo del mensaje. Por ejemplo, supongamos que desencadena una solicitud `application/x-www-url-formencoded` donde `$content` es la carga codificada como cadena base64 para conservar todos los datos:

```
CustomerName=Frank&Address=123+Avenue
```

Como la solicitud no es texto sin formato o JSON, se almacena en la acción como sigue:

```
...
"body": {
    "$content-type": "application/x-www-url-formencoded",
    "$content": "AAB1241BACDFA=="
}
```

Actualmente no hay una función nativa para datos de formulario, por lo que puede seguir usando estos datos en un flujo de trabajo al acceder a ellos manualmente con una función como `@string(body('formdataAction'))`. Si quisiéramos que la solicitud saliente tuviera también el encabezado content type `application/x-www-url-formencoded`, solo tendría que agregarla al cuerpo de la acción sin realizar ninguna conversión como `@body('formdataAction')`. Sin embargo, este método solo funciona si el cuerpo es el único parámetro de la entrada `body`. Si intenta usar `@body('formdataAction')` en una solicitud `application/json`, obtendrá un error en tiempo de ejecución, ya que se envía el cuerpo codificado.

