<properties
   pageTitle="Control del tipo de contenido de las aplicaciones lógicas | Microsoft Azure"
   description="Descubra cómo las aplicaciones lógicas administran los tipos de contenido en los procesos de diseño y runtime"
   services="app-service\logic"
   documentationCenter=".net,nodejs,java"
   authors="jeffhollan"
   manager="dwrede"
   editor=""/>

<tags
   ms.service="app-service-logic"
   ms.devlang="multiple"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="integration"
   ms.date="05/03/2016"
   ms.author="jehollan"/>

# Control del tipo de contenido de las aplicaciones lógicas

Hay muchos tipos diferentes de contenido que puede pasar a través de una aplicación lógica, incluidos JSON, XML, archivos sin formato y datos binarios. Aunque son compatibles todos los tipos de contenido, algunos los admite de forma nativa el motor de las aplicaciones lógicas y otros tienen que pasar por procesos de conversiones. En el siguiente artículo se describe cómo el motor controla los diferentes tipos de contenido y cómo puede realizarse esta tarea correctamente.

## Encabezado Content-Type

Para empezar por lo sencillo, echemos un vistazo a los dos tipos `Content-Types` que no requieren pasar por procesos de conversión dentro de una aplicación lógica: `application/json` y `text/plain`.

### Application/json

El motor de flujo de trabajo se basa en el encabezado `Content-Type` de las llamadas HTTP para determinar el control adecuado. Cualquier solicitud con el tipo de contenido `application/json` se almacena y administra como objeto JSON. Además, se puede analizar el contenido JSON de forma predeterminada sin necesidad de realizar ninguna conversión. Por tanto, una solicitud que tenga el encabezado content-type `application/json ` como este:

```
{
    "data": "a",
    "foo": [
        "bar"
    ]
}
```

podría analizarse en un flujo de trabajo con una expresión como `@body('myAction')['foo'][0]` para obtener un valor (en este caso, `bar`). No se requieren procesos de conversión adicionales. Si trabaja con datos JSON, pero no tienen un encabezado especificado, puede convertirlos manualmente a JSON mediante la función `@json()` (por ejemplo: `@json(triggerBody())['foo']`).

### Text/plain

De forma similar a `application/json`, los mensajes HTTP recibidos con el encabezado `Content-Type` de `text/plain` se almacenarán en formato sin procesar. Además, si se incluyen en las acciones posteriores sin ninguna conversión, la solicitud se enviará con un encabezado `Content-Type`: `text/plain`. Por ejemplo, si trabaja con un archivo plano puede recibir el siguiente contenido HTTP:

```
Date,Name,Address
Oct-1,Frank,123 Ave.
```

como `text/plain`. Si en la siguiente acción que envíe como el cuerpo de otra solicitud (`@body('flatfile')`), la solicitud tendría un encabezado `text/plain` Content-Type. Si trabaja con datos de texto sin formato, pero no tienen un encabezado especificado, puede convertirlos manualmente a texto mediante la función `@string()` (por ejemplo: `@string(triggerBody())`).

### Application/xml, Application/octet-stream y funciones de conversión

El motor de las aplicaciones lógicas siempre conservará el encabezado `Content-Type` que se recibió en la solicitud o respuesta HTTP. Es decir, si se recibe un contenido con `Content-Type` de `application/octet-stream` incluyendo esto en una acción posterior sin realizar ninguna conversión, se generará una solicitud saliente con `Content-Type`: `application/octet-stream`. De este modo, el motor puede garantizar que no perderán datos mientras se desplaza por el flujo de trabajo. Sin embargo, el estado de acción (entradas y salidas) se almacena en un objeto JSON cuando pasa por el flujo de trabajo. Es decir, para conservar algunos tipos de datos, el motor convertirá el contenido en una cadena con codificación binaria base64 con los metadatos adecuados que conserva `$content` y `$content-type`, que se convertirán automáticamente. Puede realizar conversiones manualmente entre los tipos de contenido utilizando las funciones de conversión:

* `@json()`: convierte los datos a `application/json`.
* `@xml()`: convierte los datos a `application/xml`.
* `@binary()`: convierte los datos a `application/octet-stream`.
* `@string()`: convierte los datos a `text/plain`.
* `@base64()`: convierte el contenido a una cadena base64.
* `@base64toString()`: convierte una cadena codificada en base64 a `text/plain`.
* `@base64toBinary()`: convierte una cadena codificada en base64 a `application/octet-stream`.
* `@encodeDataUri()`: codifica una cadena como una matriz de bytes dataUri.
* `@decodeDataUri()`: descodifica dataUri en una matriz de bytes.

Por ejemplo, si recibe una solicitud HTTP con `Content-Type`: `application/xml` de:

```
<?xml version="1.0" encoding="UTF-8" ?>
<CustomerName>Frank</CustomerName>
```

Podría convertirla y usarla posteriormente con `@xml(triggerBody())`, por ejemplo, o en una función como `@xpath(xml(triggerBody()), '/CustomerName')`.

### Otros tipos de contenido

Otros tipos de contenido son compatibles con las aplicaciones lógicas, pero es posible que haya que recuperar manualmente el cuerpo del mensaje descodificando `$content`. Por ejemplo, si desencadenara una solicitud `application/x-www-url-formencoded` similar a la siguiente:

```
CustomerName=Frank&Address=123+Avenue
```

como no se trata de texto sin formato ni JSON, se almacenará en la acción como:

```
...
"body": {
    "$content-type": "application/x-www-url-formencoded",
    "$content": "AAB1241BACDFA=="
}
```

Donde `$content` es la carga codificada como cadena base64 para conservar todos los datos. Como en estos momentos no hay una función nativa para datos de formulario, podría seguir usando estos datos dentro de un flujo de trabajo mediante el acceso manual a los datos con una función como `@string(body('formdataAction'))`. Si quisiéramos que la solicitud saliente tenga también el encabezado content-type `application/x-www-url-formencoded`, solo tendría que agregarla al cuerpo de la acción sin realizar ninguna conversión como `@body('formdataAction')`. Sin embargo, esto solo funcionará si el cuerpo es el único parámetro de la entrada `body`. Si trata de agregar `@body('formdataAction')` dentro de una solicitud `application/json`, obtendrá un error en runtime, ya que se enviará al cuerpo codificado.

<!---HONumber=AcomDC_0622_2016-->