---
title: Funciones para crear definiciones de interfaz de usuario para aplicaciones administradas de Azure | Microsoft Docs
description: "Describe las funciones que se usarán al crear definiciones de interfaz de usuario para aplicaciones administradas de Azure"
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
manager: timlt
editor: tysonn
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/12/2017
ms.author: tomfitz
ms.openlocfilehash: dcf570ca4bdc8eacb7e4d7a8ff0011c8e07b7a40
ms.sourcegitcommit: 3ab5ea589751d068d3e52db828742ce8ebed4761
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/27/2017
---
# <a name="createuidefinition-functions"></a>Funciones CreateUiDefinition
Esta sección contiene las firmas de todas las funciones compatibles de una CreateUiDefinition.

Para utilizar una función, encierre la declaración entre corchetes. Por ejemplo:

```json
"[function()]"
```

Puede hacerse referencia a cadenas y otras funciones como parámetros para una función, pero las cadenas deben encerrarse entre comillas simples. Por ejemplo:

```json
"[fn1(fn2(), 'foobar')]"
```

Si corresponde, puede hacer referencia a propiedades de la salida de una función mediante el operador de punto. Por ejemplo:

```json
"[func().prop1]"
```

## <a name="referencing-functions"></a>Referencias a funciones
Estas funciones se pueden usar para hacer referencia a salidas de propiedades o contexto de una instancia de CreateUiDefinition.

### <a name="basics"></a>basics
Devuelve los valores de salida de un elemento que se define en el paso Basics.

En el ejemplo siguiente, se devuelve el resultado del elemento denominado `foo` en el paso Basics:

```json
"[basics('foo')]"
```

### <a name="steps"></a>steps
Devuelve los valores de salida de un elemento que se define en el paso especificado. Para obtener los valores de salida de los elementos en el paso Basics, use `basics()` en su lugar.

En el ejemplo siguiente, se devuelve el resultado del elemento denominado `bar` en el paso denominado `foo`:

```json
"[steps('foo').bar]"
```

### <a name="location"></a>location
Devuelve la ubicación seleccionada en el paso Basics o el contexto actual.

El siguiente ejemplo podría devolver `"westus"`:

```json
"[location()]"
```

## <a name="string-functions"></a>Funciones de cadena
Estas funciones solo se pueden usar con cadenas de JSON.

### <a name="concat"></a>concat
Concatena una o varias cadenas.

Por ejemplo, si el valor de salida de `element1` si `"bar"`, a continuación, en este ejemplo se devuelve la cadena de `"foobar!"`:

```json
"[concat('foo', steps('step1').element1), '!']"
```

### <a name="substring"></a>substring
Devuelve la subcadena de la cadena especificada. La subcadena comienza en el índice especificado y tiene la longitud especificada.

El siguiente ejemplo devuelve `"ftw"`:

```json
"[substring('azure-ftw!!!1one', 6, 3)]"
```

### <a name="replace"></a>replace
Devuelve una cadena en la que se reemplazan todas las instancias de la cadena especificada en la cadena actual con otra cadena.

El siguiente ejemplo devuelve `"Everything is awesome!"`:

```json
"[replace('Everything is terrible!', 'terrible', 'awesome')]"
```

### <a name="guid"></a>GUID
Genera una cadena única global (GUID).

El siguiente ejemplo podría devolver `"c7bc8bdc-7252-4a82-ba53-7c468679a511"`:

```json
"[guid()]"
```

### <a name="tolower"></a>toLower
Devuelve una cadena convertida a minúsculas.

El siguiente ejemplo devuelve `"foobar"`:

```json
"[toLower('FOOBAR')]"
```

### <a name="toupper"></a>toUpper
Devuelve una cadena convertida a mayúsculas.

El siguiente ejemplo devuelve `"FOOBAR"`:

```json
"[toUpper('foobar')]"
```

## <a name="collection-functions"></a>Funciones de colección
Estas funciones se pueden usar con las colecciones, como las cadenas JSON, matrices y objetos.

### <a name="contains"></a>contains
Devuelve `true` si una cadena contiene la subcadena especificada, una matriz contiene el valor especificado o un objeto contiene la clave especificada.

#### <a name="example-1-string"></a>Ejemplo 1: cadena
El siguiente ejemplo devuelve `true`:

```json
"[contains('foobar', 'foo')]"
```

#### <a name="example-2-array"></a>Ejemplo 2: matriz
Asuma que `element1` devuelve `[1, 2, 3]`. El siguiente ejemplo devuelve `false`:

```json
"[contains(steps('foo').element1, 4)]"
```

#### <a name="example-3-object"></a>Ejemplo 3: objeto
Asuma que `element1` devuelve:

```json
{
  "key1": "foobar",
  "key2": "raboof"
}
```

El siguiente ejemplo devuelve `true`:

```json
"[contains(steps('foo').element1, 'key1')]"
```

### <a name="length"></a>length
Devuelve el número de caracteres de una cadena, el número de valores de una matriz o el número de claves en un objeto.

#### <a name="example-1-string"></a>Ejemplo 1: cadena
El siguiente ejemplo devuelve `6`:

```json
"[length('foobar')]"
```

#### <a name="example-2-array"></a>Ejemplo 2: matriz
Asuma que `element1` devuelve `[1, 2, 3]`. El siguiente ejemplo devuelve `3`:

```json
"[length(steps('foo').element1)]"
```

#### <a name="example-3-object"></a>Ejemplo 3: objeto
Asuma que `element1` devuelve:

```json
{
  "key1": "foobar",
  "key2": "raboof"
}
```

El siguiente ejemplo devuelve `2`:

```json
"[length(steps('foo').element1)]"
```

### <a name="empty"></a>empty
Devuelve `true` si la cadena, la matriz o el objeto es nulo o está vacío.

#### <a name="example-1-string"></a>Ejemplo 1: cadena
El siguiente ejemplo devuelve `true`:

```json
"[empty('')]"
```

#### <a name="example-2-array"></a>Ejemplo 2: matriz
Asuma que `element1` devuelve `[1, 2, 3]`. El siguiente ejemplo devuelve `false`:

```json
"[empty(steps('foo').element1)]"
```

#### <a name="example-3-object"></a>Ejemplo 3: objeto
Asuma que `element1` devuelve:

```json
{
  "key1": "foobar",
  "key2": "raboof"
}
```

El siguiente ejemplo devuelve `false`:

```json
"[empty(steps('foo').element1)]"
```

#### <a name="example-4-null-and-undefined"></a>Ejemplo 4: nulo y sin definir
Asuma que `element1` es `null` o no está definida. El siguiente ejemplo devuelve `true`:

```json
"[empty(steps('foo').element1)]"
```

### <a name="first"></a>first
Devuelve el primer carácter de la cadena especificada, el primer valor de la matriz especificada o la primera clave y valor del objeto especificado.

#### <a name="example-1-string"></a>Ejemplo 1: cadena
El siguiente ejemplo devuelve `"f"`:

```json
"[first('foobar')]"
```

#### <a name="example-2-array"></a>Ejemplo 2: matriz
Asuma que `element1` devuelve `[1, 2, 3]`. El siguiente ejemplo devuelve `1`:

```json
"[first(steps('foo').element1)]"
```

#### <a name="example-3-object"></a>Ejemplo 3: objeto
Asuma que `element1` devuelve:

```json
{
  "key1": "foobar",
  "key2": "raboof"
}
```
El siguiente ejemplo devuelve `{"key1": "foobar"}`:

```json
"[first(steps('foo').element1)]"
```

### <a name="last"></a>last
Devuelve el último carácter de la cadena especificada, el último valor de la matriz especificada o la última clave y valor del objeto especificado.

#### <a name="example-1-string"></a>Ejemplo 1: cadena
El siguiente ejemplo devuelve `"r"`:

```json
"[last('foobar')]"
```

#### <a name="example-2-array"></a>Ejemplo 2: matriz
Asuma que `element1` devuelve `[1, 2, 3]`. El siguiente ejemplo devuelve `2`:

```json
"[last(steps('foo').element1)]"
```

#### <a name="example-3-object"></a>Ejemplo 3: objeto
Asuma que `element1` devuelve:

```json
{
  "key1": "foobar",
  "key2": "raboof"
}
```

El siguiente ejemplo devuelve `{"key2": "raboof"}`:

```json
"[last(steps('foo').element1)]"
```

### <a name="take"></a>take
Devuelve un número especificado de caracteres contiguos desde el principio de la cadena, un número especificado de valores contiguos desde el principio de la matriz o un número de claves y valores contiguos desde el principio del objeto especificado.

#### <a name="example-1-string"></a>Ejemplo 1: cadena
El siguiente ejemplo devuelve `"foo"`:

```json
"[take('foobar', 3)]"
```

#### <a name="example-2-array"></a>Ejemplo 2: matriz
Asuma que `element1` devuelve `[1, 2, 3]`. El siguiente ejemplo devuelve `[1, 2]`:

```json
"[take(steps('foo').element1, 2)]"
```

#### <a name="example-3-object"></a>Ejemplo 3: objeto
Asuma que `element1` devuelve:

```json
{
  "key1": "foobar",
  "key2": "raboof"
}
```

El siguiente ejemplo devuelve `{"key1": "foobar"}`:

```json
"[take(steps('foo').element1, 1)]"
```

### <a name="skip"></a>skip
Omite un número especificado de elementos de una colección y, a continuación, devuelve los elementos restantes.

#### <a name="example-1-string"></a>Ejemplo 1: cadena
El siguiente ejemplo devuelve `"bar"`:

```json
"[skip('foobar', 3)]"
```

#### <a name="example-2-array"></a>Ejemplo 2: matriz
Asuma que `element1` devuelve `[1, 2, 3]`. El siguiente ejemplo devuelve `[3]`:

```json
"[skip(steps('foo').element1, 2)]"
```

#### <a name="example-3-object"></a>Ejemplo 3: objeto
Asuma que `element1` devuelve:

```json
{
  "key1": "foobar",
  "key2": "raboof"
}
```
El siguiente ejemplo devuelve `{"key2": "raboof"}`:

```json
"[skip(steps('foo').element1, 1)]"
```

## <a name="logical-functions"></a>Funciones lógicas
Estas funciones se pueden usar en los condicionales. Algunas funciones pueden no admitir todos los tipos de datos JSON.

### <a name="equals"></a>equals
Devuelve `true` si ambos parámetros tienen el mismo tipo y valor. Algunas funciones admiten todos los tipos de datos JSON.

El siguiente ejemplo devuelve `true`:

```json
"[equals(0, 0)]"
```

El siguiente ejemplo devuelve `true`:

```json
"[equals('foo', 'foo')]"
```

El siguiente ejemplo devuelve `false`:

```json
"[equals('abc', ['a', 'b', 'c'])]"
```

### <a name="less"></a>less
Devuelve `true` si el primer parámetro es estrictamente menor que el segundo parámetro. Esta función admite parámetros solo de tipo number y string.

El siguiente ejemplo devuelve `true`:

```json
"[less(1, 2)]"
```

El siguiente ejemplo devuelve `false`:

```json
"[less('9', '10')]"
```

### <a name="lessorequals"></a>lessOrEquals
Devuelve `true` si el primer parámetro es estrictamente menor o igual que el segundo parámetro. Esta función admite parámetros solo de tipo number y string.

El siguiente ejemplo devuelve `true`:

```json
"[lessOrEquals(2, 2)]"
```

### <a name="greater"></a>greater
Devuelve `true` si el primer parámetro es estrictamente mayor que el segundo parámetro. Esta función admite parámetros solo de tipo number y string.

El siguiente ejemplo devuelve `false`:

```json
"[greater(1, 2)]"
```

El siguiente ejemplo devuelve `true`:

```json
"[greater('9', '10')]"
```

### <a name="greaterorequals"></a>greaterOrEquals
Devuelve `true` si el primer parámetro es estrictamente mayor o igual que el segundo parámetro. Esta función admite parámetros solo de tipo number y string.

El siguiente ejemplo devuelve `true`:

```json
"[greaterOrEquals(2, 2)]"
```

### <a name="and"></a>y
Devuelve `true` si todos los parámetros se evalúan como `true`. Esta función admite dos o más parámetros solo de tipo booleano.

El siguiente ejemplo devuelve `true`:

```json
"[and(equals(0, 0), equals('foo', 'foo'), less(1, 2))]"
```

El siguiente ejemplo devuelve `false`:

```json
"[and(equals(0, 0), greater(1, 2))]"
```

### <a name="or"></a>o
Devuelve `true` si al menos uno de los parámetros se evalúa como `true`. Esta función admite dos o más parámetros solo de tipo booleano.

El siguiente ejemplo devuelve `true`:

```json
"[or(equals(0, 0), equals('foo', 'foo'), less(1, 2))]"
```

El siguiente ejemplo devuelve `true`:

```json
"[or(equals(0, 0), greater(1, 2))]"
```

### <a name="not"></a>not
Devuelve `true` si el parámetro se evalúa como `false`. Esta función admite parámetros solo de tipo booleano.

El siguiente ejemplo devuelve `true`:

```json
"[not(false)]"
```

El siguiente ejemplo devuelve `false`:

```json
"[not(equals(0, 0))]"
```

### <a name="coalesce"></a>coalesce
Devuelve el valor del primer parámetro no nulo. Algunas funciones admiten todos los tipos de datos JSON.

Asuma que `element1` y `element2` son indefinidos. El siguiente ejemplo devuelve `"foobar"`:

```json
"[coalesce(steps('foo').element1, steps('foo').element2, 'foobar')]"
```

## <a name="conversion-functions"></a>Funciones de conversión
Estas funciones se pueden usar para convertir valores entre tipos de datos JSON y codificaciones.

### <a name="int"></a>int
Convierte el parámetro en un número entero. Esta función admite parámetros de tipo number y string.

El siguiente ejemplo devuelve `1`:

```json
"[int('1')]"
```

El siguiente ejemplo devuelve `2`:

```json
"[int(2.9)]"
```

### <a name="float"></a>float
Convierte el parámetro en un número de punto flotante. Esta función admite parámetros de tipo number y string.

El siguiente ejemplo devuelve `1.0`:

```json
"[float('1.0')]"
```

El siguiente ejemplo devuelve `2.9`:

```json
"[float(2.9)]"
```

### <a name="string"></a>cadena
Convierte el parámetro en una cadena. Esta función admite parámetros de todos los tipos de datos JSON.

El siguiente ejemplo devuelve `"1"`:

```json
"[string(1)]"
```

El siguiente ejemplo devuelve `"2.9"`:

```json
"[string(2.9)]"
```

El siguiente ejemplo devuelve `"[1,2,3]"`:

```json
"[string([1,2,3])]"
```

El siguiente ejemplo devuelve `"{"foo":"bar"}"`:

```json
"[string({\"foo\":\"bar\"})]"
```

### <a name="bool"></a>booleano
Convierte el parámetro en un valor booleano. Esta función admite parámetros de tipo number, string y booleano. Similar a los booleanos en JavaScript, cualquier valor excepto `0` o `'false'` devuelve `true`.

El siguiente ejemplo devuelve `true`:

```json
"[bool(1)]"
```

El siguiente ejemplo devuelve `false`:

```json
"[bool(0)]"
```

El siguiente ejemplo devuelve `true`:

```json
"[bool(true)]"
```

El siguiente ejemplo devuelve `true`:

```json
"[bool('true')]"
```

### <a name="parse"></a>parse
Convierte el parámetro en un tipo nativo. En otras palabras, esta función es el inverso de `string()`. Esta función admite parámetros solo de tipo string.

El siguiente ejemplo devuelve `1`:

```json
"[parse('1')]"
```

El siguiente ejemplo devuelve `true`:

```json
"[parse('true')]"
```

El siguiente ejemplo devuelve `[1,2,3]`:

```json
"[parse('[1,2,3]')]"
```

El siguiente ejemplo devuelve `{"foo":"bar"}`:

```json
"[parse('{\"foo\":\"bar\"}')]"
```

### <a name="encodebase64"></a>encodeBase64
Codifica el parámetro a una cadena de codificación Base 64. Esta función admite parámetros solo de tipo string.

El siguiente ejemplo devuelve `"Zm9vYmFy"`:

```json
"[encodeBase64('foobar')]"
```

### <a name="decodebase64"></a>decodeBase64
Decodifica el parámetro de una cadena de codificación Base 64. Esta función admite parámetros solo de tipo string.

El siguiente ejemplo devuelve `"foobar"`:

```json
"[decodeBase64('Zm9vYmFy')]"
```

### <a name="encodeuricomponent"></a>encodeUriComponent
Codifica el parámetro a una cadena de codificación con dirección URL. Esta función admite parámetros solo de tipo string.

El siguiente ejemplo devuelve `"https%3A%2F%2Fportal.azure.com%2F"`:

```json
"[encodeUriComponent('https://portal.azure.com/')]"
```

### <a name="decodeuricomponent"></a>decodeUriComponent
Decodifica el parámetro de una cadena de codificación con dirección URL. Esta función admite parámetros solo de tipo string.

El siguiente ejemplo devuelve `"https://portal.azure.com/"`:

```json
"[decodeUriComponent('https%3A%2F%2Fportal.azure.com%2F')]"
```

## <a name="math-functions"></a>Funciones matemáticas
### <a name="add"></a>agregar
Agrega dos números y devuelve el resultado.

El siguiente ejemplo devuelve `3`:

```json
"[add(1, 2)]"
```

### <a name="sub"></a>sub
Resta el segundo número al primer número y devuelve el resultado.

El siguiente ejemplo devuelve `1`:

```json
"[sub(3, 2)]"
```

### <a name="mul"></a>mul
Multiplica dos números y devuelve el resultado.

El siguiente ejemplo devuelve `6`:

```json
"[mul(2, 3)]"
```

### <a name="div"></a>div
Divide el segundo número por el primer número y devuelve el resultado. El resultado es siempre un número entero.

El siguiente ejemplo devuelve `2`:

```json
"[div(6, 3)]"
```

### <a name="mod"></a>mod
Divide el primer número por el segundo número y devuelve el resto.

El siguiente ejemplo devuelve `0`:

```json
"[mod(6, 3)]"
```

El siguiente ejemplo devuelve `2`:

```json
"[mod(6, 4)]"
```

### <a name="min"></a>Min
Devuelve el número más pequeño de los dos.

El siguiente ejemplo devuelve `1`:

```json
"[min(1, 2)]"
```

### <a name="max"></a>max
Devuelve el número más grande de los dos.

El siguiente ejemplo devuelve `2`:

```json
"[max(1, 2)]"
```

### <a name="range"></a>range
Genera una secuencia de números enteros en el intervalo especificado.

El siguiente ejemplo devuelve `[1,2,3]`:

```json
"[range(1, 3)]"
```

### <a name="rand"></a>rand
Genera un número integral aleatorio en el intervalo especificado. Esta función no genera números aleatorios criptográficamente seguros.

El siguiente ejemplo podría devolver `42`:

```json
"[rand(-100, 100)]"
```

### <a name="floor"></a>floor
Devuelve el valor entero más grande menor o igual que el número especificado.

El siguiente ejemplo devuelve `3`:

```json
"[floor(3.14)]"
```

### <a name="ceil"></a>ceil
Devuelve el valor entero más grande mayor o igual que el número especificado.

El siguiente ejemplo devuelve `4`:

```json
"[ceil(3.14)]"
```

## <a name="date-functions"></a>Funciones de fecha
### <a name="utcnow"></a>utcNow
Devuelve una cadena en formato ISO 8601 de la fecha y hora actuales en el equipo local.

El siguiente ejemplo podría devolver `"1990-12-31T23:59:59.000Z"`:

```json
"[utcNow()]"
```

### <a name="addseconds"></a>addSeconds
Agrega un número entero de segundos a la marca de tiempo especificada.

El siguiente ejemplo devuelve `"1991-01-01T00:00:00.000Z"`:

```json
"[addSeconds('1990-12-31T23:59:60Z', 1)]"
```

### <a name="addminutes"></a>addMinutes
Agrega un número entero de minutos a la marca de tiempo especificada.

El siguiente ejemplo devuelve `"1991-01-01T00:00:59.000Z"`:

```json
"[addMinutes('1990-12-31T23:59:59Z', 1)]"
```

### <a name="addhours"></a>addHours
Agrega un número entero de horas a la marca de tiempo especificada.

El siguiente ejemplo devuelve `"1991-01-01T00:59:59.000Z"`:

```json
"[addHours('1990-12-31T23:59:59Z', 1)]"
```

## <a name="next-steps"></a>Pasos siguientes
* Para ver una introducción a Azure Resource Manager, consulte [Información general sobre Azure Resource Manager](../azure-resource-manager/resource-group-overview.md).

