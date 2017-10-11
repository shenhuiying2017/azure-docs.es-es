---
title: "Información sobre el lenguaje de consulta de IoT Hub de Azure | Microsoft Docs"
description: "Guía del desarrollador: descripción del lenguaje de consulta de IoT Hub de tipo SQL que se usa para recuperar información sobre los dispositivos gemelos y trabajos desde IoT Hub."
services: iot-hub
documentationcenter: .net
author: fsautomata
manager: timlt
editor: 
ms.assetid: 851a9ed3-b69e-422e-8a5d-1d79f91ddf15
ms.service: iot-hub
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/25/17
ms.author: elioda
ms.openlocfilehash: a7650104eda58923558892f6f0f6666d16dbce28
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 07/11/2017
---
# <a name="reference---iot-hub-query-language-for-device-twins-jobs-and-message-routing"></a>Referencia: Lenguaje de consulta de IoT Hub para dispositivos gemelos, trabajos y enrutamiento de mensajes

IoT Hub proporciona un lenguaje eficaz de tipo SQL para recuperar información sobre [dispositivos gemelos][lnk-twins], [trabajos][lnk-jobs] y [enrutamiento de mensajes][lnk-devguide-messaging-routes]. Este artículo presenta:

* una introducción a las características principales del lenguaje de consulta de IoT Hub y
* una descripción más detallada del lenguaje.

## <a name="get-started-with-device-twin-queries"></a>Introducción a las consultas de dispositivos gemelos
Los [dispositivos gemelos][lnk-twins] pueden contener objetos JSON arbitrarios como etiquetas y propiedades. IoT Hub permite consultar los dispositivos gemelos como un solo documento JSON que contiene toda la información de los dispositivos gemelos.
Por ejemplo, supongamos que los dispositivos gemelos de IoT Hub tienen la siguiente estructura:

```json
{
    "deviceId": "myDeviceId",
    "etag": "AAAAAAAAAAc=",
    "tags": {
        "location": {
            "region": "US",
            "plant": "Redmond43"
        }
    },
    "properties": {
        "desired": {
            "telemetryConfig": {
                "configId": "db00ebf5-eeeb-42be-86a1-458cccb69e57",
                "sendFrequencyInSecs": 300
            },
            "$metadata": {
            ...
            },
            "$version": 4
        },
        "reported": {
            "connectivity": {
                "type": "cellular"
            },
            "telemetryConfig": {
                "configId": "db00ebf5-eeeb-42be-86a1-458cccb69e57",
                "sendFrequencyInSecs": 300,
                "status": "Success"
            },
            "$metadata": {
            ...
            },
            "$version": 7
        }
    }
}
```

IoT Hub expone los dispositivos gemelos como una colección de documentos denominada **devices**.
Por lo tanto, la consulta siguiente recupera el conjunto completo de dispositivos gemelos:

```sql
SELECT * FROM devices
```

> [!NOTE]
> Los [SDK IoT de Azure][lnk-hub-sdks] admiten la paginación de resultados de gran tamaño.

IoT Hub permite recuperar dispositivos gemelos filtrando por condiciones arbitrarias. Por ejemplo,

```sql
SELECT * FROM devices
WHERE tags.location.region = 'US'
```

recupera los dispositivos gemelos con la etiqueta **location.region** establecida en **US**.
También se admiten operadores booleanos y comparaciones aritméticas, por ejemplo,

```sql
SELECT * FROM devices
WHERE tags.location.region = 'US'
    AND properties.reported.telemetryConfig.sendFrequencyInSecs >= 60
```

recupera todos los dispositivos gemelos ubicados en Estados Unidos configurados para enviar datos de telemetría con una frecuencia inferior a un minuto. Por comodidad, también es posible usar constantes de matriz con los operadores **IN** (En) y **NIN** (No en). Por ejemplo,

```sql
SELECT * FROM devices
WHERE properties.reported.connectivity IN ['wired', 'wifi']
```

recupera todos los dispositivos gemelos que notificaron conectividad WiFi o con cable. A menudo, es necesario identificar a todos los dispositivos gemelos que contienen una propiedad concreta. IoT Hub admite la función `is_defined()` para esta finalidad. Por ejemplo,

```SQL
SELECT * FROM devices
WHERE is_defined(properties.reported.connectivity)
```

recupera todos los dispositivos gemelos que definen la propiedad notificada `connectivity`. Consulte la sección [Cláusula WHERE][lnk-query-where] para obtener la referencia completa de las funcionalidades de filtrado.

También se admiten la agrupación y las agregaciones. Por ejemplo,

```sql
SELECT properties.reported.telemetryConfig.status AS status,
    COUNT() AS numberOfDevices
FROM devices
GROUP BY properties.reported.telemetryConfig.status
```

devuelve el recuento de los dispositivos en cada estado de configuración de telemetría.

```json
[
    {
        "numberOfDevices": 3,
        "status": "Success"
    },
    {
        "numberOfDevices": 2,
        "status": "Pending"
    },
    {
        "numberOfDevices": 1,
        "status": "Error"
    }
]
```

En el ejemplo anterior, se demuestra una situación en la que tres dispositivos notificaron una configuración correcta, dos aún están aplicándola y uno notificó un error.

### <a name="c-example"></a>Ejemplo de C#
El [SDK del servicio C#][lnk-hub-sdks] expone la funcionalidad de consulta en la clase **RegistryManager**.
Este ejemplo corresponde a una consulta simple:

```csharp
var query = registryManager.CreateQuery("SELECT * FROM devices", 100);
while (query.HasMoreResults)
{
    var page = await query.GetNextAsTwinAsync();
    foreach (var twin in page)
    {
        // do work on twin object
    }
}
```

Observe cómo se crea una instancia del objeto **query** con un tamaño de página (hasta 1000) y después se pueden recuperar varias páginas llamando a los métodos **GetNextAsTwinAsync** varias veces.
Tenga en cuenta que el objeto query expone varios elementos **next\***, según la opción de deserialización que requiera la consulta, como objetos de trabajo o dispositivo gemelo, o JSON sin formato que se usará cuando se utilicen proyecciones.

### <a name="nodejs-example"></a>Ejemplo de Node.js
El [SDK de servicios IoT de Azure para Node.js][lnk-hub-sdks] expone la funcionalidad de consulta en el objeto **Registry**.
Este ejemplo corresponde a una consulta simple:

```nodejs
var query = registry.createQuery('SELECT * FROM devices', 100);
var onResults = function(err, results) {
    if (err) {
        console.error('Failed to fetch the results: ' + err.message);
    } else {
        // Do something with the results
        results.forEach(function(twin) {
            console.log(twin.deviceId);
        });

        if (query.hasMoreResults) {
            query.nextAsTwin(onResults);
        }
    }
};
query.nextAsTwin(onResults);
```

Observe cómo se crea una instancia del objeto **query** con un tamaño de página (hasta 1000) y después se pueden recuperar varias páginas llamando a los métodos **nextAsTwin** varias veces.
Tenga en cuenta que el objeto query expone varios elementos **next\***, según la opción de deserialización que requiera la consulta, como objetos de trabajo o dispositivo gemelo, o JSON sin formato que se usará cuando se utilicen proyecciones.

### <a name="limitations"></a>Limitaciones
> [!IMPORTANT]
> Los resultados de las consultas pueden demorarse unos minutos con respecto a los valores más recientes en los dispositivos gemelos. Si realiza consultas a dispositivos gemelos individuales por identificador, siempre resultará preferible utilizar la API de dispositivos gemelos de recuperación, que contendrá en todo momento los valores más recientes y cuenta con umbrales de limitación superiores.

Actualmente, las comparaciones solo se admiten entre tipos primitivos (no objetos), por ejemplo `... WHERE properties.desired.config = properties.reported.config` solo se admite si esas propiedades tienen valores primitivos.

## <a name="get-started-with-jobs-queries"></a>Introducción a las consultas de trabajos
Los [trabajos][lnk-jobs] proporcionan una forma de ejecutar operaciones en conjuntos de dispositivos. Cada dispositivo gemelo contiene la información de los trabajos de los que forma parte en una colección denominada **jobs**.
Lógicamente,

```json
{
    "deviceId": "myDeviceId",
    "etag": "AAAAAAAAAAc=",
    "tags": {
        ...
    },
    "properties": {
        ...
    },
    "jobs": [
        {
            "deviceId": "myDeviceId",
            "jobId": "myJobId",
            "jobType": "scheduleTwinUpdate",
            "status": "completed",
            "startTimeUtc": "2016-09-29T18:18:52.7418462",
            "endTimeUtc": "2016-09-29T18:20:52.7418462",
            "createdDateTimeUtc": "2016-09-29T18:18:56.7787107Z",
            "lastUpdatedDateTimeUtc": "2016-09-29T18:18:56.8894408Z",
            "outcome": {
                "deviceMethodResponse": null
            }
        },
        ...
    ]
}
```

Ahora, esta colección se puede consultar como **devices.jobs** en el lenguaje de consulta de IoT Hub.

> [!IMPORTANT]
> Actualmente, la propiedad jobs no se devuelve nunca cuando se consulta a dispositivos gemelos (es decir, las consultas que contienen "FROM devices"). Solo es accesible directamente con las consultas que utilizan `FROM devices.jobs`.
>
>

Por ejemplo, para obtener todos los trabajos (pasados y programados) que afecten a un único dispositivo, puede usar la siguiente consulta:

```sql
SELECT * FROM devices.jobs
WHERE devices.jobs.deviceId = 'myDeviceId'
```

Observe cómo esta consulta proporciona el estado específico del dispositivo (y posiblemente la respuesta del método directo) para cada trabajo devuelto.
También es posible filtrar por condiciones booleanas arbitrarias en todas las propiedades de objeto de la colección **devices.jobs**.
Por ejemplo, la siguiente consulta:

```sql
SELECT * FROM devices.jobs
WHERE devices.jobs.deviceId = 'myDeviceId'
    AND devices.jobs.jobType = 'scheduleTwinUpdate'
    AND devices.jobs.status = 'completed'
    AND devices.jobs.createdTimeUtc > '2016-09-01'
```

recupera todos los trabajos de actualización de dispositivos gemelos completados para el dispositivo **myDeviceId** que se crearon después de septiembre de 2016.

También es posible recuperar los resultados por dispositivo de un único trabajo.

```sql
SELECT * FROM devices.jobs
WHERE devices.jobs.jobId = 'myJobId'
```

### <a name="limitations"></a>Limitaciones
Actualmente, las consultas en **devices.jobs** no admiten:

* proyecciones, por lo tanto, solo `SELECT *` es posible.
* condiciones que hagan referencia al dispositivo gemelo, además de a las propiedades de trabajo (vea la sección anterior).
* realizar agregaciones, como count, avg, group by.

## <a name="get-started-with-device-to-cloud-message-routes-query-expressions"></a>Introducción a expresiones de consulta de rutas de mensajes de dispositivo a la nube

Con las [rutas de dispositivo a la nube][lnk-devguide-messaging-routes], puede configurar IoT Hub para enviar mensajes de dispositivo a la nube en diferentes puntos de conexión en función de expresiones evaluadas frente a mensajes individuales.

La ruta [condición][lnk-query-expressions] utiliza el mismo lenguaje de consultas de IoT Hub como condiciones en consultas gemelas y de trabajo. Las condiciones de enrutamiento se evalúan en el cuerpo y los encabezados del mensaje. La expresión de consulta de enrutamiento puede implicar solo los encabezados del mensaje, solo el cuerpo del mensaje o tanto los encabezados como el cuerpo del mensaje. IoT Hub da por supuesto un esquema específico para los encabezados y el cuerpo del mensaje con el fin de enrutar mensajes, y las secciones siguientes describen los elementos que se necesitan para que IoT Hub realice correctamente el enrutamiento:

### <a name="routing-on-message-headers"></a>Enrutamiento en los encabezados del mensaje

IoT Hub da por supuesto la siguiente representación JSON de los encabezados del mensaje para el enrutamiento del mensaje:

```json
{
    "$messageId": "",
    "$enqueuedTime": "",
    "$to": "",
    "$expiryTimeUtc": "",
    "$correlationId": "",
    "$userId": "",
    "$ack": "",
    "$connectionDeviceId": "",
    "$connectionDeviceGenerationId": "",
    "$connectionAuthMethod": "",
    "$content-type": "",
    "$content-encoding": "",

    "userProperty1": "",
    "userProperty2": ""
}
```

Las propiedades del sistema de mensajes tienen como prefijo el símbolo `'$'`.
Siempre se accede a las propiedades de usuario con su nombre. Si sucede que un nombre de propiedad de usuario coincide con el de una propiedad del sistema (como `$to`), se recuperará la propiedad de usuario con la expresión `$to`.
Siempre puede acceder a la propiedad del sistema mediante corchetes `{}`: por ejemplo, puede usar la expresión `{$to}` para acceder a la propiedad del sistema `to`. Los nombres de propiedad entre corchetes siempre permiten recuperar la propiedad del sistema correspondiente.

Recuerde que los nombres de propiedad no distinguen mayúsculas de minúsculas.

> [!NOTE]
> Todas las propiedades de mensaje son cadenas. Las propiedades del sistema, como se describen en la [guía del desarrollador][lnk-devguide-messaging-format], no están disponibles para su uso en las consultas.
>

Por ejemplo, si utiliza una propiedad `messageType`, puede enrutar toda la telemetría a un punto de conexión y todas las alertas a otro punto de conexión. Puede escribir la siguiente expresión para enrutar la telemetría:

```sql
messageType = 'telemetry'
```

Y la siguiente expresión para enrutar los mensajes de alerta:

```sql
messageType = 'alert'
```

También se admiten las funciones y expresiones booleanas. Esta característica le permite distinguir entre el nivel de gravedad, por ejemplo:

```sql
messageType = 'alerts' AND as_number(severity) <= 2
```

Consulte la sección [Expresiones y condiciones][lnk-query-expressions] para obtener la lista completa de funciones y operadores compatibles.

### <a name="routing-on-message-bodies"></a>Enrutamiento en los cuerpos del mensaje

IoT Hub solo puede realizar el enrutamiento según el contenido del cuerpo del mensaje si este tiene un formato JSON correcto codificado en UTF-8, UTF-16 o UTF-32. Debe establecer el tipo de contenido del mensaje en `application/json` y la codificación de contenido en una de las codificaciones UTF compatibles en los encabezados del mensaje para permitir que IoT Hub enrute el mensaje según el contenido del cuerpo. Si no se especifica cualquiera de los encabezados, IoT Hub no intentará evaluar ninguna expresión de consulta que implique el cuerpo con respecto al mensaje. Si el mensaje no es un mensaje JSON o si no especifica el tipo de contenido ni la codificación de contenido, se puede seguir usando el enrutamiento de mensaje para enrutar el mensaje según los encabezados del mensaje.

Puede usar `$body` en la expresión de consulta para enrutar el mensaje. Puede usar una referencia al cuerpo simple, una referencia a la matriz del cuerpo o varias referencias al cuerpo en la expresión de consulta. La expresión de consulta también puede combinar una referencia al cuerpo con una referencia al encabezado del mensaje. Por ejemplo, todas las expresiones siguientes son expresiones de consulta válidas:

```sql
$body.message.Weather.Location.State = 'WA'
$body.Weather.HistoricalData[0].Month = 'Feb'
$body.Weather.Temperature = 50 AND $body.message.Weather.IsEnabled
length($body.Weather.Location.State) = 2
$body.Weather.Temperature = 50 AND Status = 'Active'
```

## <a name="basics-of-an-iot-hub-query"></a>Conceptos básicos de una consulta de IoT Hub
Cada consulta de IoT Hub consta de cláusulas SELECT y FROM, y de cláusulas WHERE y GROUP BY opcionales. Cada consulta se ejecuta en una colección de documentos JSON, por ejemplo, dispositivos gemelos. La cláusula FROM indica la colección de documentos en la que se va a iterar (**devices** o **devices.jobs**). Después se aplica el filtro en la cláusula WHERE. Con las agregaciones, se agrupan los resultados de este paso como se especifica en la cláusula GROUP BY y, para cada grupo, se genera una fila como se especifica en la cláusula SELECT.

```sql
SELECT <select_list>
FROM <from_specification>
[WHERE <filter_condition>]
[GROUP BY <group_specification>]
```

## <a name="from-clause"></a>Cláusula FROM
La cláusula **FROM <from_specification>** solo puede suponer dos valores: **FROM devices**, para consultar dispositivos gemelos, o **FROM devices.jobs**, para consultar detalles por dispositivo del trabajo.

## <a name="where-clause"></a>Cláusula WHERE
La cláusula **WHERE <filter_condition>** es opcional. Especifica una o varias condiciones que los documentos JSON en la colección FROM deben satisfacer para incluirse como parte del resultado. Cualquier documento JSON debe evaluar las condiciones especificadas como "true" para que se incluya en el resultado.

Las condiciones permitidas se describen en la sección [Expresiones y condiciones][lnk-query-expressions].

## <a name="select-clause"></a>Cláusula SELECT
La cláusula SELECT (**SELECT <select_list>**) es obligatoria y especifica qué valores se recuperan de la consulta. Especifica los valores JSON que se usarán para generar nuevos objetos JSON.
Para cada elemento del subconjunto filtrado (y, opcionalmente, agrupado) de la colección FROM, la fase de proyección genera un nuevo objeto JSON, construido con los valores especificados en la cláusula SELECT.

Esta es la gramática de la cláusula SELECT:

```
SELECT [TOP <max number>] <projection list>

<projection_list> ::=
    '*'
    | <projection_element> AS alias [, <projection_element> AS alias]+

<projection_element> :==
    attribute_name
    | <projection_element> '.' attribute_name
    | <aggregate>

<aggregate> :==
    count()
    | avg(<projection_element>)
    | sum(<projection_element>)
    | min(<projection_element>)
    | max(<projection_element>)
```

donde **attribute_name** hace referencia a cualquier propiedad del documento JSON en la colección FROM. Puede ver algunos ejemplos de las cláusulas SELECT en la sección [Introducción a las consultas de gemelos][lnk-query-getstarted].

Actualmente, las cláusulas de selección distintas a **SELECT \*** solo se admiten en las consultas agregadas de dispositivos gemelos.

## <a name="group-by-clause"></a>Cláusula GROUP BY
La cláusula **GROUP BY <group_specification>** es un paso opcional que se puede ejecutar después del filtro especificado en la cláusula WHERE y antes de la proyección especificada en la cláusula SELECT. Agrupa los documentos según el valor de un atributo. Estos grupos se usan para generar valores agregados, como se especifica en la cláusula SELECT.

Un ejemplo de consulta con GROUP BY es:

```sql
SELECT properties.reported.telemetryConfig.status AS status,
    COUNT() AS numberOfDevices
FROM devices
GROUP BY properties.reported.telemetryConfig.status
```

La sintaxis formal de GROUP BY es:

```
GROUP BY <group_by_element>
<group_by_element> :==
    attribute_name
    | < group_by_element > '.' attribute_name
```

donde **attribute_name** hace referencia a cualquier propiedad del documento JSON en la colección FROM.

Actualmente, solo se admite la cláusula GROUP BY al consultar dispositivos gemelos.

## <a name="expressions-and-conditions"></a>Expresiones y condiciones
Brevemente, una *expresión*:

* Se evalúa como una instancia de tipo JSON (como booleano, número, cadena, matriz u objeto), y
* Se define manipulando datos procedentes del documento JSON del dispositivo y constantes mediante funciones y operadores integrados.

Las *condiciones* son expresiones que se evalúan como un valor booleano. Cualquier constante diferente del booleano **true** se considera **false** (incluidos **null**, **undefined**, cualquier instancia de objeto o matriz, cualquier cadena y, obviamente, el booleano **false**).

La sintaxis de las expresiones es:

```
<expression> ::=
    <constant> |
    attribute_name |
    <function_call> |
    <expression> binary_operator <expression> |
    <create_array_expression> |
    '(' <expression> ')'

<function_call> ::=
    <function_name> '(' expression ')'

<constant> ::=
    <undefined_constant>
    | <null_constant>
    | <number_constant>
    | <string_constant>
    | <array_constant>

<undefined_constant> ::= undefined
<null_constant> ::= null
<number_constant> ::= decimal_literal | hexadecimal_literal
<string_constant> ::= string_literal
<array_constant> ::= '[' <constant> [, <constant>]+ ']'
```

donde:

| Símbolo | Definición |
| --- | --- |
| attribute_name | Cualquier propiedad del documento JSON en la colección **FROM**. |
| binary_operator | Cualquier operador binario que figure en la sección [Operadores](#operators). |
| function_name| Cualquier función que figure en la sección [Funciones](#functions). |
| decimal_literal |Un valor float expresado en notación decimal. |
| hexadecimal_literal |Un número expresado por la cadena "0x" seguida de una cadena de dígitos hexadecimales. |
| string_literal |Los literales de cadena son cadenas Unicode representadas por una secuencia de cero o varios caracteres Unicode o secuencias de escape. Los literales de cadena se encierran entre comillas simples (apóstrofo: ') o comillas dobles (comillas: "). Caracteres de escape permitidos: `\'`, `\"`, `\\`, `\uXXXX` para los caracteres Unicode definidos con 4 dígitos hexadecimales. |

### <a name="operators"></a>Operadores
Se admiten los siguientes operadores:

| Familia | Operadores |
| --- | --- |
| Aritméticos |+,-,*,/,% |
| Lógicos |AND, OR, NOT |
| De comparación |=, !=, <, >, <=, >=, <> |

### <a name="functions"></a>Funciones
Cuando se consultan gemelos y trabajos, la única función admitida es:

| Función | Descripción |
| -------- | ----------- |
| IS_DEFINED(property) | Devuelve un valor booleano que indica si se ha asignado un valor a la propiedad (incluido `null`). |

En condiciones de rutas, se admiten las siguientes funciones matemáticas:

| Función | Descripción |
| -------- | ----------- |
| ABS(x) | Devuelve el valor absoluto (positivo) de la expresión numérica especificada. |
| EXP(x) | Devuelve el valor exponencial de la expresión numérica especificada (e^x). |
| POWER(x,y) | Devuelve el valor de la expresión especificada a la potencia especificada (x^y).|
| SQUARE(x) | Devuelve el cuadrado del valor numérico especificado. |
| CEILING(x) | Devuelve el valor entero más pequeño mayor o igual que la expresión numérica especificada. |
| FLOOR(x) | Devuelve el valor entero más grande menor o igual que la expresión numérica especificada. |
| SIGN(x) | Devuelve el signo positivo (+1), cero (0) o negativo (-1) de la expresión numérica especificada.|
| SQRT(x) | Devuelve el cuadrado del valor numérico especificado. |

En condiciones de rutas, se admiten las funciones de conversión y comprobación de tipos siguientes:

| Función | Descripción |
| -------- | ----------- |
| AS_NUMBER | Convierte la cadena de entrada en un número. `noop` si la entrada es un número; `Undefined` si la cadena no representa un número.|
| IS_ARRAY | Devuelve un valor booleano que indica si el tipo de la expresión especificada es una matriz. |
| IS_BOOL | Devuelve un valor booleano que indica si el tipo de la expresión especificada es un valor booleano. |
| IS_DEFINED | Devuelve un valor booleano que indica si se ha asignado un valor a la propiedad. |
| IS_NULL | Devuelve un valor booleano que indica si el tipo de la expresión especificada es nulo. |
| IS_NUMBER | Devuelve un valor booleano que indica si el tipo de la expresión especificada es un número. |
| IS_OBJECT | Devuelve un valor booleano que indica si el tipo de la expresión especificada es un objeto JSON. |
| IS_PRIMITIVE | Devuelve un valor booleano que indica si el tipo de la expresión especificada es un tipo primitivo (cadena, booleano, numérico o `null`). |
| IS_STRING | Devuelve un valor booleano que indica si el tipo de la expresión especificada es una cadena. |

En condiciones de rutas, se admiten las siguientes funciones de cadena:

| Función | Descripción |
| -------- | ----------- |
| CONCAT(x, …) | Devuelve una cadena que es el resultado de concatenar dos o más valores de cadena. |
| LENGTH(x) | Devuelve el número de caracteres de la expresión de cadena especificada.|
| LOWER(x) | Devuelve una expresión de cadena después de convertir los datos de caracteres en mayúsculas a minúsculas. |
| UPPER(x) | Devuelve una expresión de cadena después de convertir datos de caracteres en minúsculas a mayúsculas. |
| SUBSTRING(string, start [, length]) | Devuelve parte de una expresión de cadena a partir de la posición de base cero del carácter especificado y continúa hasta la longitud especificada, o hasta el final de la cadena. |
| INDEX_OF(string, fragment) | Devuelve la posición inicial de la primera aparición de la expresión de la segunda cadena dentro de la primera expresión de cadena especificada, o -1 si no se encuentra la cadena.|
| STARTS_WITH(x, y) | Devuelve un valor booleano que indica si la primera expresión de cadena empieza con la segunda. |
| ENDS_WITH(x, y) | Devuelve un valor booleano que indica si la primera expresión de cadena finaliza con la segunda. |
| CONTAINS(x,y) | Devuelve un valor booleano que indica si la primera expresión de cadena contiene la segunda. |

## <a name="next-steps"></a>Pasos siguientes
Aprenda a ejecutar consultas en sus aplicaciones mediante los [SDK IoT de Azure ][lnk-hub-sdks].

[lnk-query-where]: iot-hub-devguide-query-language.md#where-clause
[lnk-query-expressions]: iot-hub-devguide-query-language.md#expressions-and-conditions
[lnk-query-getstarted]: iot-hub-devguide-query-language.md#get-started-with-device-twin-queries

[lnk-twins]: iot-hub-devguide-device-twins.md
[lnk-jobs]: iot-hub-devguide-jobs.md
[lnk-devguide-endpoints]: iot-hub-devguide-endpoints.md
[lnk-devguide-quotas]: iot-hub-devguide-quotas-throttling.md
[lnk-devguide-mqtt]: iot-hub-mqtt-support.md
[lnk-devguide-messaging-routes]: iot-hub-devguide-messages-read-custom.md
[lnk-devguide-messaging-format]: iot-hub-devguide-messages-construct.md
[lnk-devguide-messaging-routes]: ./iot-hub-devguide-messages-read-custom.md

[lnk-hub-sdks]: iot-hub-devguide-sdks.md
