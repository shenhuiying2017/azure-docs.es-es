---
title: Esquema del lenguaje de definición de flujo de trabajo - Azure Logic Apps | Microsoft Docs
description: Escritura de definiciones de flujo de trabajo personalizadas para Azure Logic Apps con el lenguaje de definición de flujo de trabajo
services: logic-apps
author: ecfan
manager: cfowler
editor: ''
documentationcenter: ''
ms.assetid: 26c94308-aa0d-4730-97b6-de848bffff91
ms.service: logic-apps
ms.workload: logic-apps
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: reference
ms.date: 04/30/2018
ms.author: estfan
ms.openlocfilehash: efbfffec10b665ebab230375e774e476199c4ad5
ms.sourcegitcommit: 6116082991b98c8ee7a3ab0927cf588c3972eeaa
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/05/2018
ms.locfileid: "33886820"
---
# <a name="logic-apps-workflow-definitions-with-the-workflow-definition-language-schema"></a>Definiciones de flujo de trabajo de Logic Apps con el esquema de lenguaje de definición de flujo de trabajo

Cuando se crea un flujo de trabajo de aplicación lógica con [Azure Logic Apps](../logic-apps/logic-apps-overview.md), la definición subyacente del flujo de trabajo describe la lógica real que se ejecuta para la aplicación lógica. Esta descripción sigue una estructura que se define y valida mediante el esquema de lenguaje de definición de flujo de trabajo, que usa el formato [Notación de objetos JavaScript (JSON)](https://www.json.org/). 
  
## <a name="workflow-definition-structure"></a>Estructura de definición de flujo de trabajo

Una definición de flujo de trabajo tiene al menos un desencadenador que crea una instancia de la aplicación lógica, además de una o varias acciones que ejecuta dicha aplicación. 

Esta es la estructura de alto nivel de una definición de flujo de trabajo:  
  
```json
"definition": {
  "$schema": "<workflow-definition-language-schema-version>",
  "contentVersion": "<workflow-definition-version-number>",
  "parameters": { "<workflow-parameter-definitions>" },
  "triggers": { "<workflow-trigger-definitions>" },
  "actions": { "<workflow-action-definitions>" },
  "outputs": { "<workflow-output-definitions>" }
}
```
  
| Elemento | Obligatorio | DESCRIPCIÓN | 
|---------|----------|-------------| 
| definición | Sí | El elemento inicial de la definición de flujo de trabajo. | 
| $schema | Solo cuando se hace referencia externa a una definición de flujo de trabajo. | La ubicación del archivo de esquema JSON que describe la versión del lenguaje de definición de flujo de trabajo, que puede encontrar aquí: <p>`https://schema.management.azure.com/schemas/2016-06-01/Microsoft.Logic.json`</p> |   
| contentVersion | Sin  | El número de versión de la definición de flujo de trabajo, que es "1.0.0.0" de forma predeterminada. Para ayudar a identificar y confirmar la definición correcta al implementar un flujo de trabajo, especifique el valor que usará. | 
| parameters | Sin  | Las definiciones de uno o varios parámetros que pasan datos al flujo de trabajo. <p><p>Parámetros máximos: 50 | 
| Desencadenadores | Sin  | Las definiciones de uno o varios desencadenadores que crean una instancia del flujo de trabajo. Puede definir más de un desencadenador, pero solo con el lenguaje de definición de flujo de trabajo, no visualmente mediante el Diseñador de aplicaciones lógicas. <p><p>Desencadenadores máximos: 10 | 
| actions | Sin  | Las definiciones de una o varias acciones para ejecutar en el entorno de tiempo de ejecución del flujo de trabajo. <p><p>Acciones máximas: 250 | 
| outputs | Sin  | Las definiciones de las salidas que se devuelven de la ejecución de un flujo de trabajo. <p><p>Salidas máximas: 10 |  
|||| 

## <a name="parameters"></a>Parámetros

En la sección `parameters`, defina todos los parámetros de flujo de trabajo que usa su aplicación lógica en la implementación para aceptar entradas. Tanto las declaraciones de parámetros como los valores de parámetros son necesarios en la implementación. Antes de poder usar estos parámetros en otras secciones del flujo de trabajo, asegúrese de declarar todos los parámetros en las siguientes secciones. 

Esta es la estructura general de una definición de parámetro:  

```json
"parameters": {
  "<parameter-name>": {
    "type": "<parameter-type>",
    "defaultValue": "<default-parameter-value>",
    "allowedValues": [ <array-with-permitted-parameter-values> ],
    "metadata": { 
      "key": { 
        "name": "<key-value>"
      } 
    }
  }
},
```

| Elemento | Obligatorio | Escriba | DESCRIPCIÓN |  
|---------|----------|------|-------------|  
| Tipo | Sí | int, float, string, securestring, bool, array, JSON object, secureobject <p><p>**Nota**: Con todas las contraseñas, claves y secretos, use los tipos `securestring` y `secureobject` porque la operación `GET` no los devuelve. | El tipo del parámetro. |
| defaultValue | Sin  | Igual que `type`. | El valor de parámetro predeterminado cuando no se especifica ningún valor al crear una instancia del flujo de trabajo. | 
| allowedValues | Sin  | Igual que `type`. | Una matriz con valores que puede aceptar el parámetro. |  
| metadata | Sin  | Objeto JSON | Cualquier otro detalle del parámetro, por ejemplo, el nombre o una descripción legible de la aplicación lógica, o los datos de tiempo de diseño que usa Visual Studio u otras herramientas. |  
||||

## <a name="triggers-and-actions"></a>Desencadenadores y acciones  

En una definición de flujo de trabajo, las secciones `triggers` y `actions` definen las llamadas que se producen durante la ejecución del flujo de trabajo. Para más información sobre estas secciones y la sintaxis, consulte [Desencadenadores y acciones para flujos de trabajo](../logic-apps/logic-apps-workflow-actions-triggers.md).
  
## <a name="outputs"></a>Salidas 

En la sección `outputs`, defina los datos que puede devolver el flujo de trabajo cuando termine de ejecutarse. Por ejemplo, para realizar el seguimiento de un estado o valor específico de cada ejecución, especifique que la salida del flujo de trabajo devuelva esos datos. 

> [!NOTE]
> Al responder a las solicitudes entrantes de la API REST de un servicio, no use `outputs`. En su lugar, use el tipo de acción `Response`. Para más información, consulte [Desencadenadores y acciones para flujos de trabajo](../logic-apps/logic-apps-workflow-actions-triggers.md).

Esta es la estructura general de una definición de salida: 

```json
"outputs": {
  "<key-name>": {  
    "type": "<key-type>",  
    "value": "<key-value>"  
  }
} 
```

| Elemento | Obligatorio | Escriba | DESCRIPCIÓN | 
|---------|----------|------|-------------| 
| <*key-name*> | Sí | string | El nombre de clave del valor devuelto de salida. |  
| Tipo | Sí | int, float, string, securestring, bool, array, JSON object | El tipo del valor devuelto de salida. | 
| value | Sí | Igual que `type`. | El valor devuelto de salida. |  
||||| 

Para obtener la salida de una ejecución de flujo de trabajo, revise el historial y los detalles de ejecución de la aplicación lógica en Azure Portal o use la [API REST de flujo de trabajo](https://docs.microsoft.com/rest/api/logic/workflows). También puede pasar la salida a sistemas externos, por ejemplo, a Power BI, para crear paneles. 

<a name="expressions"></a>

## <a name="expressions"></a>Expresiones

Con JSON, puede tener valores literales que existen en tiempo de diseño, por ejemplo:

```json
"customerName": "Sophia Owen", 
"rainbowColors": ["red", "orange", "yellow", "green", "blue", "indigo", "violet"], 
"rainbowColorsCount": 7 
```

También puede tener valores que no existen hasta el momento de ejecución. Para representar estos valores, puede usar *expresiones*, que se evalúan en tiempo de ejecución. Una expresión es una secuencia que puede contener una o varias [funciones](#functions), [operadores](#operators), variables, valores explícitos o constantes. En la definición de flujo de trabajo, se puede usar una expresión en cualquier lugar de un valor de cadena JSON agregando delante de la expresión el prefijo de signo de arroba (@). Al evaluar una expresión que representa un valor JSON, se extrae el cuerpo de la expresión quitando el carácter @ y siempre da como resultado otro valor JSON. 

Por ejemplo, en el caso de la propiedad `customerName` anteriormente definida, puede obtener su valor mediante la función [parameters()](../logic-apps/workflow-definition-language-functions-reference.md#parameters) en una expresión y asignar ese valor a la propiedad `accountName`:

```json
"customerName": "Sophia Owen", 
"accountName": "@parameters('customerName')"
```

La *interpolación de cadena* también le permite usar varias expresiones dentro de cadenas que están encapsuladas mediante el carácter @ y llaves ({}). Esta es la sintaxis:

```json
@{ "<expression1>", "<expression2>" }
```

El resultado siempre es una cadena, de forma que esta funcionalidad es similar a la función `concat()`, por ejemplo: 

```json
"customerName": "First name: @{parameters('firstName')} Last name: @{parameters('lastName')}"
```

Si tiene un literal de cadena que comienza por el carácter @, coloque delante de este carácter otro carácter @ como carácter de escape: @@

En estos ejemplos se muestra cómo se evalúan las expresiones:

| Valor JSON | Resultado |
|------------|--------| 
| "Sophia Owen" | Devuelve estos caracteres: "Sophia Owen" |
| "array[1]" | Devuelve estos caracteres: "array [1]" |
| "\@@\" | Devuelve estos caracteres como una cadena de un carácter: "@" |   
| \" \@\" | Devuelve estos caracteres como una cadena de dos caracteres: "@" |
|||

Para estos ejemplos, suponga que tiene que definir "myBirthMonth" es igual a "January" y "myAge" es igual al número 42:  
  
```json
"myBirthMonth": "January",
"myAge": 42
```

Estos ejemplos muestran cómo se evalúan las expresiones siguientes:

| Expresión JSON | Resultado |
|-----------------|--------| 
| "@parameters('myBirthMonth')" | Devuelve la cadena: "January" |  
| "@{parameters('myBirthMonth')}" | Devuelve la cadena: "January" |  
| "@parameters('myAge')" | Devuelve este número: 42 |  
| "@{parameters('myAge')}" | Devuelve este número como una cadena: "42" |  
| "My age is @{parameters('myAge')}" | Devuelve esta cadena: "My age is 42" |  
| "@concat('My age is ', string(parameters('myAge')))" | Devuelve esta cadena: "My age is 42" |  
| "My age is @@{parameters('myAge')}" | Devuelve esta cadena, que incluye la expresión: "My age is @{parameters('myAge')}` | 
||| 

Al trabajar visualmente en el Diseñador de aplicaciones lógicas, puede crear expresiones mediante el generador de expresiones, por ejemplo: 

![Diseñador de aplicaciones lógicas > Generador de expresiones](./media/logic-apps-workflow-definition-language/expression-builder.png)

Cuando haya terminado, la expresión aparece en la propiedad correspondiente en la definición de flujo de trabajo, por ejemplo, la propiedad `searchQuery` aquí:

```json
"Search_tweets": {
  "inputs": {
    "host": {
      "connection": {
       "name": "@parameters('$connections')['twitter']['connectionId']"
      }
    }
  },
  "method": "get",
  "path": "/searchtweets",
  "queries": {
    "maxResults": 20,
    "searchQuery": "Azure @{concat('firstName','', 'LastName')}"
  }
},
```

<a name="operators"></a>

## <a name="operators"></a>Operadores

En [expresiones](#expressions) y [funciones](#functions), los operadores realizan tareas específicas, como hacer referencia a una propiedad o un valor en una matriz. 

| Operador | Task | 
|----------|------|
| ' | Para usar un literal de cadena como entrada o en expresiones y funciones, encapsule la cadena solo con comillas sencillas, por ejemplo, `'<myString>'`. No use comillas dobles (""), ya que entran en conflicto con el formato JSON que rodea una expresión entera. Por ejemplo:  <p>**Sí**: length('Hello') </br>**No**: length("Hello") <p>Al pasar matrices o números, no es necesario encapsular los signos de puntuación. Por ejemplo:  <p>**Sí**: length([1, 2, 3]) </br>**No**: length("[1, 2, 3]") | 
| [] | Para hacer referencia a un valor en una posición concreta (índice) de una matriz, use corchetes. Por ejemplo, para obtener el segundo elemento de una matriz: <p>`myArray[1]` | 
| . | Para hacer referencia a una propiedad de un objeto, use el operador punto. Por ejemplo, para obtener la propiedad `name` de un objeto JSON `customer`: <p>`"@parameters('customer').name"` | 
| ? | Para hacer referencia a propiedades nulas de un objeto sin un error de tiempo de ejecución, use el operador de signo de interrogación. Por ejemplo, para administrar salidas nulas desde un desencadenador, puede usar esta expresión: <p>`@coalesce(trigger().outputs?.body?.<someProperty>, '<property-default-value>')` | 
||| 

<a name="functions"></a>

## <a name="functions"></a>Functions

Algunas expresiones obtienen sus valores de las acciones en tiempo de ejecución que puede que no existan aún cuando comienza a ejecutarse una aplicación lógica. Para trabajar con estos valores o hacer referencia a ellos mediante expresiones, puede usar [*funciones*](../logic-apps/workflow-definition-language-functions-reference.md). Por ejemplo, puede usar funciones matemáticas para los cálculos, como la función [add()](../logic-apps/workflow-definition-language-functions-reference.md#add), que devuelve la suma de números enteros y flotantes. Para información detallada sobre cada función, consulte el [artículo de referencia alfabético](../logic-apps/workflow-definition-language-functions-reference.md).
O bien, puede seguir aprendiendo sobre las funciones y su uso general.

Estas son solo algunas tareas de ejemplo que se pueden realizar con funciones: 

| Task | Sintaxis de la función | Resultado | 
| ---- | --------------- | -------------- | 
| Devuelve una cadena en formato de minúsculas. | toLower('<*text*>') <p>Por ejemplo: toLower('Hello') | "Hola" | 
| Devuelve un identificador único global (GUID). | guid() |"c2ecc88d-88c8-4096-912c-d6f2e2b138ce" | 
|||| 

En este ejemplo se muestra cómo puede obtener el valor del parámetro `customerName` y asignar ese valor a la propiedad `accountName` mediante la función [parameters()](../logic-apps/workflow-definition-language-functions-reference.md#parameters) en una expresión:

```json
"accountName": "@parameters('customerName')"
```

Estas son algunas otras formas generales en que puede usar funciones en expresiones:

| Task | Sintaxis de la función en una expresión | 
| ---- | -------------------------------- | 
| Realice el trabajo con un elemento y para ello pase ese elemento a una función. | "@<*functionName*>(<*item*>)" | 
| 1. Obtenga el valor de *parameterName* mediante la función anidada `parameters()`. </br>2. Realice el trabajo con el resultado y para ello pase ese valor a *functionName*. | "@<*functionName*>(parameters('<*parameterName*>'))" | 
| 1. Obtenga el resultado de la función interna anidada *functionName*. </br>2. Pase el resultado a la función externa *functionName2*. | "@<*functionName2*>(<*functionName*>(<*item*>))" | 
| 1. Obtenga el resultado de *functionName*. </br>2. Dado que el resultado es un objeto con la propiedad *propertyName*, obtenga el valor de la propiedad. | "@<*functionName*>(<*item*>).<*propertyName*>" | 
||| 

Por ejemplo, la función `concat()` puede tomar dos o más valores de cadena como parámetros. Esta función combina esas cadenas en una cadena. Puede pasar literales de cadena, por ejemplo, "Sophia" y "Owen" para obtener una cadena combinada, "SophiaOwen":

```json
"customerName": "@concat('Sophia', 'Owen')"
```

O bien, puede obtener valores de cadena de los parámetros. En este ejemplo se usa la función `parameters()` en cada parámetro `concat()` y en los parámetros `firstName` y `lastName`. Luego se pasan las cadenas resultantes a la función `concat()` para obtener una cadena combinada, por ejemplo, "SophiaOwen":

```json
"customerName": "@concat(parameters('firstName'), parameters('lastName'))"
```

En cualquier de los casos, ambos ejemplos asignan el resultado a la propiedad `customerName`. 

Para información detallada sobre cada función, consulte el [artículo de referencia alfabético](../logic-apps/workflow-definition-language-functions-reference.md).
O bien, puede seguir aprendiendo sobre las funciones según su uso general.

<a name="string-functions"></a>

### <a name="string-functions"></a>Funciones de cadena

Para trabajar con cadenas, puede usar estas funciones de cadena y también algunas [funciones de colección](#collection-functions). Las funciones de cadena solo funcionan en cadenas. 

| Función de cadena | Task | 
| --------------- | ---- | 
| [concat](../logic-apps/workflow-definition-language-functions-reference.md#concat) | Combina dos o más cadenas y devuelve la cadena combinada. | 
| [endsWith](../logic-apps/workflow-definition-language-functions-reference.md#endswith) | Comprueba si una cadena termina con la subcadena especificada. | 
| [guid](../logic-apps/workflow-definition-language-functions-reference.md#guid) | Genera un identificador único global (GUID) como una cadena. | 
| [indexOf](../logic-apps/workflow-definition-language-functions-reference.md#indexof) | Devuelve la posición inicial de una subcadena. | 
| [lastIndexOf](../logic-apps/workflow-definition-language-functions-reference.md#lastindexof) | Devuelve la posición final de una subcadena. | 
| [replace](../logic-apps/workflow-definition-language-functions-reference.md#replace) | Reemplaza una subcadena por la cadena especificada y devuelve la cadena actualizada. | 
| [split](../logic-apps/workflow-definition-language-functions-reference.md#split) | Devuelve una matriz que tiene todos los caracteres de una cadena y separa cada carácter con el carácter delimitador específico. | 
| [startsWith](../logic-apps/workflow-definition-language-functions-reference.md#startswith) | Comprueba si una cadena comienza con una subcadena especificada. | 
| [substring](../logic-apps/workflow-definition-language-functions-reference.md#substring) | Devuelve caracteres de una cadena, a partir de la posición especificada. | 
| [toLower](../logic-apps/workflow-definition-language-functions-reference.md#toLower) | Devuelve una cadena en formato de minúsculas. | 
| [toUpper](../logic-apps/workflow-definition-language-functions-reference.md#toUpper) | Devuelve una cadena en formato de mayúsculas. | 
| [trim](../logic-apps/workflow-definition-language-functions-reference.md#trim) | Quita el espacio en blanco inicial y final de una cadena y devuelve la cadena actualizada. | 
||| 

<a name="collection-functions"></a>

### <a name="collection-functions"></a>Funciones de colección

Para trabajar con colecciones, por lo general matrices, cadenas y, en ocasiones, diccionarios, puede usar estas funciones de colección. 

| Función de colección | Task | 
| ------------------- | ---- | 
| [contains](../logic-apps/workflow-definition-language-functions-reference.md#contains) | Comprueba si una colección contiene un elemento específico. |
| [empty](../logic-apps/workflow-definition-language-functions-reference.md#empty) | Comprueba si una colección está vacía. | 
| [first](../logic-apps/workflow-definition-language-functions-reference.md#first) | Devuelve el primer elemento de una colección. | 
| [intersection](../logic-apps/workflow-definition-language-functions-reference.md#intersection) | Devuelve una colección que tiene *solo* los elementos comunes en las colecciones especificadas. | 
| [join](../logic-apps/workflow-definition-language-functions-reference.md#join) | Devuelve una cadena que tiene *todos* los elementos de una matriz, separados por el carácter especificado. | 
| [last](../logic-apps/workflow-definition-language-functions-reference.md#last) | Devuelve el último elemento de una colección. | 
| [length](../logic-apps/workflow-definition-language-functions-reference.md#length) | Devuelve el número de elementos de una cadena o una matriz. | 
| [skip](../logic-apps/workflow-definition-language-functions-reference.md#skip) | Quita elementos del principio de una colección y devuelve *todos los demás* elementos. | 
| [take](../logic-apps/workflow-definition-language-functions-reference.md#take) | Devuelve elementos del principio de una colección. | 
| [union](../logic-apps/workflow-definition-language-functions-reference.md#union) | Devuelve una colección que tiene *todos* los elementos de las colecciones especificadas. | 
||| 

<a name="comparison-functions"></a>

### <a name="comparison-functions"></a>Funciones de comparación

Para trabajar con condiciones, comparar valores y resultados de expresiones o evaluar varios tipos de lógica, puede usar estas funciones de comparación. Para obtener la referencia completa sobre cada función, consulte el [artículo de referencia alfabético](../logic-apps/workflow-definition-language-functions-reference.md).

| Función de comparación | Task | 
| ------------------- | ---- | 
| [and](../logic-apps/workflow-definition-language-functions-reference.md#and) | Comprueba si todas las expresiones son true. | 
| [equals](../logic-apps/workflow-definition-language-functions-reference.md#equals) | Comprueba si ambos valores son equivalentes. | 
| [greater](../logic-apps/workflow-definition-language-functions-reference.md#greater) | Comprueba si el primer valor es mayor que el segundo. | 
| [greaterOrEquals](../logic-apps/workflow-definition-language-functions-reference.md#greaterOrEquals) | Comprueba si el primer valor es mayor o igual que el segundo. | 
| [if](../logic-apps/workflow-definition-language-functions-reference.md#if) | Comprueba si una expresión es true o false. En función del resultado, devuelve un valor especificado. | 
| [less](../logic-apps/workflow-definition-language-functions-reference.md#less) | Comprueba si el primer valor es menor que el segundo. | 
| [lessOrEquals](../logic-apps/workflow-definition-language-functions-reference.md#lessOrEquals) | Comprueba si el primer valor es menor o igual que el segundo. | 
| [not](../logic-apps/workflow-definition-language-functions-reference.md#not) | Comprueba si una expresión es false. | 
| [or](../logic-apps/workflow-definition-language-functions-reference.md#or) | Comprueba si al menos una expresión es true. |
||| 

<a name="conversion-functions"></a>

### <a name="conversion-functions"></a>Funciones de conversión

Para cambiar el tipo o el formato de un valor, puede usar estas funciones de conversión. Por ejemplo, puede cambiar un valor de booleano a entero. Para información sobre cómo Logic Apps administra los tipos de contenido durante la conversión, consulte [Administración de los tipos de contenido](../logic-apps/logic-apps-content-type.md). Para obtener la referencia completa sobre cada función, consulte el [artículo de referencia alfabético](../logic-apps/workflow-definition-language-functions-reference.md).

| Función de conversión | Task | 
| ------------------- | ---- | 
| [matriz](../logic-apps/workflow-definition-language-functions-reference.md#array) | Devuelve una matriz a partir de una única entrada especificada. Para varias entradas, consulte [createArray](../logic-apps/workflow-definition-language-functions-reference.md#createArray). | 
| [base64](../logic-apps/workflow-definition-language-functions-reference.md#base64) | Devuelve la versión de una cadena codificada en base64. | 
| [base64ToBinary](../logic-apps/workflow-definition-language-functions-reference.md#base64ToBinary) | Devuelve la versión binaria de una cadena codificada en base64. | 
| [base64ToString](../logic-apps/workflow-definition-language-functions-reference.md#base64ToString) | Devuelve la versión de cadena de una cadena codificada en base64. | 
| [binary](../logic-apps/workflow-definition-language-functions-reference.md#binary) | Devuelve la versión binaria de un valor de entrada. | 
| [bool](../logic-apps/workflow-definition-language-functions-reference.md#bool) | Devuelve la versión booleana de un valor de entrada. | 
| [createArray](../logic-apps/workflow-definition-language-functions-reference.md#createArray) | Devuelve una matriz a partir de varias entradas. | 
| [dataUri](../logic-apps/workflow-definition-language-functions-reference.md#dataUri) | Devuelve el URI de datos de un valor de entrada. | 
| [dataUriToBinary](../logic-apps/workflow-definition-language-functions-reference.md#dataUriToBinary) | Devuelve la versión binaria de un URI de datos. | 
| [dataUriToString](../logic-apps/workflow-definition-language-functions-reference.md#dataUriToString) | Devuelve la versión de cadena de un URI de datos. | 
| [decodeBase64](../logic-apps/workflow-definition-language-functions-reference.md#decodeBase64) | Devuelve la versión de cadena de una cadena codificada en base64. | 
| [decodeDataUri](../logic-apps/workflow-definition-language-functions-reference.md#decodeDataUri) | Devuelve la versión binaria de un URI de datos. | 
| [decodeUriComponent](../logic-apps/workflow-definition-language-functions-reference.md#decodeUriComponent) | Devuelve una cadena que reemplaza los caracteres de escape por versiones descodificadas. | 
| [encodeUriComponent](../logic-apps/workflow-definition-language-functions-reference.md#encodeUriComponent) | Devuelve una cadena que reemplaza los caracteres no seguros de la dirección URL por caracteres de escape. | 
| [float](../logic-apps/workflow-definition-language-functions-reference.md#float) | Devuelve un número de punto flotante de un valor de entrada. | 
| [int](../logic-apps/workflow-definition-language-functions-reference.md#int) | Devuelve la versión como número entero de una cadena. | 
| [json](../logic-apps/workflow-definition-language-functions-reference.md#json) | Devuelve el valor o el objeto de tipo Notación de objetos JavaScript (JSON) de una cadena o XML. | 
| [cadena](../logic-apps/workflow-definition-language-functions-reference.md#string) | Devuelve la versión de cadena de un valor de entrada. | 
| [uriComponent](../logic-apps/workflow-definition-language-functions-reference.md#uriComponent) | Devuelve la versión codificada con el URI de un valor de entrada mediante la sustitución de los caracteres no seguros de la dirección URL por caracteres de escape. | 
| [uriComponentToBinary](../logic-apps/workflow-definition-language-functions-reference.md#uriComponentToBinary) | Devuelve la versión binaria de una cadena codificada con el URI. | 
| [uriComponentToString](../logic-apps/workflow-definition-language-functions-reference.md#uriComponentToString) | Devuelve la versión de cadena de una cadena codificada con el URI. | 
| [xml](../logic-apps/workflow-definition-language-functions-reference.md#xml) | Devuelve la versión XML de una cadena. | 
||| 

<a name="math-functions"></a>

### <a name="math-functions"></a>Funciones matemáticas

Para trabajar con números enteros y flotantes, puede usar estas funciones matemáticas. Para obtener la referencia completa sobre cada función, consulte el [artículo de referencia alfabético](../logic-apps/workflow-definition-language-functions-reference.md).

| Función matemática | Task | 
| ------------- | ---- | 
| [agregar](../logic-apps/workflow-definition-language-functions-reference.md#add) | Devuelve el resultado de sumar dos números. | 
| [div](../logic-apps/workflow-definition-language-functions-reference.md#div) | Devuelve el resultado de dividir dos números. | 
| [max](../logic-apps/workflow-definition-language-functions-reference.md#max) | Devuelve el valor más alto de un conjunto de números o una matriz. | 
| [min](../logic-apps/workflow-definition-language-functions-reference.md#min) | Devuelve el valor más bajo de un conjunto de números o una matriz. | 
| [mod](../logic-apps/workflow-definition-language-functions-reference.md#mod) | Devuelve el resto de dividir dos números. | 
| [mul](../logic-apps/workflow-definition-language-functions-reference.md#mul) | Devuelve el producto de multiplicar dos números. | 
| [rand](../logic-apps/workflow-definition-language-functions-reference.md#rand) | Devuelve un entero aleatorio desde un intervalo especificado. | 
| [range](../logic-apps/workflow-definition-language-functions-reference.md#range) | Devuelve una matriz de enteros que comienza en un entero especificado. | 
| [sub](../logic-apps/workflow-definition-language-functions-reference.md#sub) | Devuelve el resultado de restar el segundo número del primero. | 
||| 

<a name="date-time-functions"></a>

### <a name="date-and-time-functions"></a>Funciones de fecha y hora

Para trabajar con fechas y horas, puede usar estas funciones de fecha y hora.
Para obtener la referencia completa sobre cada función, consulte el [artículo de referencia alfabético](../logic-apps/workflow-definition-language-functions-reference.md).

| Función de fecha u hora | Task | 
| --------------------- | ---- | 
| [addDays](../logic-apps/workflow-definition-language-functions-reference.md#addDays) | Agrega un número de días a una marca de tiempo. | 
| [addHours](../logic-apps/workflow-definition-language-functions-reference.md#addHours) | Agrega un número de horas a una marca de tiempo. | 
| [addMinutes](../logic-apps/workflow-definition-language-functions-reference.md#addMinutes) | Agrega un número de minutos a una marca de tiempo. | 
| [addSeconds](../logic-apps/workflow-definition-language-functions-reference.md#addSeconds) | Agrega un número de segundos a una marca de tiempo. |  
| [addToTime](../logic-apps/workflow-definition-language-functions-reference.md#addToTime) | Agrega un número de unidades de tiempo a una marca de tiempo. Consulte también [getFutureTime](../logic-apps/workflow-definition-language-functions-reference.md#getFutureTime). | 
| [convertFromUtc](../logic-apps/workflow-definition-language-functions-reference.md#convertFromUtc) | Convierte una marca de tiempo del formato Hora Universal Coordinada (UTC) a la zona horaria de destino. | 
| [convertTimeZone](../logic-apps/workflow-definition-language-functions-reference.md#convertTimeZone) | Convierte una marca de tiempo de la zona horaria de origen a la zona horaria de destino. | 
| [convertToUtc](../logic-apps/workflow-definition-language-functions-reference.md#convertToUtc) | Convierte una marca de tiempo de la zona horaria de origen al formato Hora Universal Coordinada (UTC). | 
| [dayOfMonth](../logic-apps/workflow-definition-language-functions-reference.md#dayOfMonth) | Devuelve el día del componente de mes de una marca de tiempo. | 
| [dayOfWeek](../logic-apps/workflow-definition-language-functions-reference.md#dayOfWeek) | Devuelve el día del componente de semana de una marca de tiempo. | 
| [dayOfYear](../logic-apps/workflow-definition-language-functions-reference.md#dayOfYear) | Devuelve el día del componente de año de una marca de tiempo. | 
| [formatDateTime](../logic-apps/workflow-definition-language-functions-reference.md#formatDateTime) | Devuelve la fecha de una marca de tiempo. | 
| [getFutureTime](../logic-apps/workflow-definition-language-functions-reference.md#getFutureTime) | Devuelve la marca de tiempo actual más las unidades de tiempo especificadas. Consulte también [addToTime](../logic-apps/workflow-definition-language-functions-reference.md#addToTime). | 
| [getPastTime](../logic-apps/workflow-definition-language-functions-reference.md#getPastTime) | Devuelve la marca de tiempo actual menos las unidades de tiempo especificadas. Consulte también [subtractFromTime](../logic-apps/workflow-definition-language-functions-reference.md#subtractFromTime). | 
| [startOfDay](../logic-apps/workflow-definition-language-functions-reference.md#startOfDay) | Devuelve el inicio del día de una marca de tiempo. | 
| [startOfHour](../logic-apps/workflow-definition-language-functions-reference.md#startOfHour) | Devuelve el inicio de la hora de una marca de tiempo. | 
| [startOfMonth](../logic-apps/workflow-definition-language-functions-reference.md#startOfMonth) | Devuelve el inicio del mes de una marca de tiempo. | 
| [subtractFromTime](../logic-apps/workflow-definition-language-functions-reference.md#subtractFromTime) | Resta un número de unidades de tiempo de una marca de tiempo. Consulte también [getPastTime](../logic-apps/workflow-definition-language-functions-reference.md#getPastTime). | 
| [ticks](../logic-apps/workflow-definition-language-functions-reference.md#ticks) | Devuelve el valor de la propiedad `ticks` de una marca de tiempo especificada. | 
| [utcNow](../logic-apps/workflow-definition-language-functions-reference.md#utcNow) | Devuelve la marca de tiempo actual como una cadena. | 
||| 

<a name="workflow-functions"></a>

### <a name="workflow-functions"></a>Funciones de flujo de trabajo

Estas funciones de flujo de trabajo pueden ayudarle a:

* Obtener detalles sobre una instancia de flujo de trabajo en tiempo de ejecución 
* Trabajar con las entradas usadas para crear instancias de aplicaciones lógicas
* Hacer referencia a las salidas de desencadenadores y acciones

Por ejemplo, puede hacer referencia a las salidas de una acción y usar esos datos en una acción posterior. Para obtener la referencia completa sobre cada función, consulte el [artículo de referencia alfabético](../logic-apps/workflow-definition-language-functions-reference.md).

| Función de flujo de trabajo | Task | 
| ----------------- | ---- | 
| [action](../logic-apps/workflow-definition-language-functions-reference.md#action) | Devuelve la salida de la acción actual en tiempo de ejecución, o valores de otros pares de nombre y valor JSON. Consulte también [actions](../logic-apps/workflow-definition-language-functions-reference.md#actions). | 
| [actionBody](../logic-apps/workflow-definition-language-functions-reference.md#actionBody) | Devuelve la salida en tiempo de ejecución del elemento `body` de una acción. Consulte también [body](../logic-apps/workflow-definition-language-functions-reference.md#body). | 
| [actionOutputs](../logic-apps/workflow-definition-language-functions-reference.md#actionOutputs) | Devuelve la salida en tiempo de ejecución de una acción. Consulte [actions](../logic-apps/workflow-definition-language-functions-reference.md#actions). | 
| [actions](../logic-apps/workflow-definition-language-functions-reference.md#actions) | Devuelve la salida de una acción en tiempo de ejecución, o valores de otros pares de nombre y valor JSON. Consulte también [action](../logic-apps/workflow-definition-language-functions-reference.md#action).  | 
| [body](#body) | Devuelve la salida en tiempo de ejecución del elemento `body` de una acción. Consulte también [actionBody](../logic-apps/workflow-definition-language-functions-reference.md#actionBody). | 
| [formDataMultiValues](../logic-apps/workflow-definition-language-functions-reference.md#formDataMultiValues) | Crea una matriz con los valores que coinciden con un nombre de clave en las salidas de acción *form-data* o *form-encoded*. | 
| [formDataValue](../logic-apps/workflow-definition-language-functions-reference.md#formDataValue) | Devuelve un valor único que coincide con un nombre de clave en las salidas *form-data* o *form-encoded* de una acción. | 
| [item](../logic-apps/workflow-definition-language-functions-reference.md#item) | Dentro de una acción de repetición sobre una matriz, devuelve el elemento actual de la matriz durante la iteración actual de la acción. | 
| [items](../logic-apps/workflow-definition-language-functions-reference.md#items) | Cuando se utiliza dentro un bucle para cada uno o repetir hasta, devuelve el elemento actual del bucle especificado.| 
| [listCallbackUrl](../logic-apps/workflow-definition-language-functions-reference.md#listCallbackUrl) | Devuelve la "dirección URL de devolución de llamada" que llama a un desencadenador o una acción. | 
| [multipartBody](../logic-apps/workflow-definition-language-functions-reference.md#multipartBody) | Devuelve el elemento body de una parte específica de la salida de una acción que consta de varias partes. | 
| [parameters](../logic-apps/workflow-definition-language-functions-reference.md#parameters) | Devuelve el valor de un parámetro que se describe en la definición de aplicación lógica. | 
| [trigger](../logic-apps/workflow-definition-language-functions-reference.md#trigger) | Devuelve la salida de un desencadenador en tiempo de ejecución, o desde otros pares de nombre y valor JSON. Consulte también [triggerOutputs](#triggerOutputs) y [triggerBody](../logic-apps/workflow-definition-language-functions-reference.md#triggerBody). | 
| [triggerBody](../logic-apps/workflow-definition-language-functions-reference.md#triggerBody) | Devuelve la salida `body` de un desencadenador en tiempo de ejecución. Consulte [trigger](../logic-apps/workflow-definition-language-functions-reference.md#trigger). | 
| [triggerFormDataValue](../logic-apps/workflow-definition-language-functions-reference.md#triggerFormDataValue) | Devuelve un valor único que coincide con un nombre de clave en las salidas del desencadenador *form-data* o *form-encoded*. | 
| [triggerMultipartBody](../logic-apps/workflow-definition-language-functions-reference.md#triggerMultipartBody) | Devuelve el cuerpo de una parte específica de la salida de varias partes de un desencadenador. | 
| [triggerFormDataMultiValues](../logic-apps/workflow-definition-language-functions-reference.md#triggerFormDataMultiValues) | Crea una matriz cuyos valores coinciden con un nombre de clave en las salidas del desencadenador *form-data* o *form-encoded*. | 
| [triggerOutputs](../logic-apps/workflow-definition-language-functions-reference.md#triggerOutputs) | Devuelve la salida de un desencadenador en tiempo de ejecución o valores de otros pares de nombre y valor JSON. Consulte [trigger](../logic-apps/workflow-definition-language-functions-reference.md#trigger). | 
| [variables](../logic-apps/workflow-definition-language-functions-reference.md#variables) | Devuelve el valor de una variable especificada. | 
| [workflow](../logic-apps/workflow-definition-language-functions-reference.md#workflow) | Devuelve todos los detalles sobre el propio flujo de trabajo durante el tiempo de ejecución. | 
||| 

<a name="uri-parsing-functions"></a>

### <a name="uri-parsing-functions"></a>Funciones de análisis de URI

Para trabajar con identificadores uniformes de recursos (URI) y obtener distintos valores de propiedad para estos URI, puede usar estas funciones de análisis de URI. Para obtener la referencia completa sobre cada función, consulte el [artículo de referencia alfabético](../logic-apps/workflow-definition-language-functions-reference.md).

| Función de análisis de URI | Task | 
| -------------------- | ---- | 
| [uriHost](../logic-apps/workflow-definition-language-functions-reference.md#uriHost) | Devuelve el valor `host` de un identificador uniforme de recursos (URI). | 
| [uriPath](../logic-apps/workflow-definition-language-functions-reference.md#uriPath) | Devuelve el valor `path` de un identificador uniforme de recursos (URI). | 
| [uriPathAndQuery](../logic-apps/workflow-definition-language-functions-reference.md#uriPathAndQuery) | Devuelve los valores `path` y `query` de un identificador uniforme de recursos (URI). | 
| [uriPort](../logic-apps/workflow-definition-language-functions-reference.md#uriPort) | Devuelve el valor `port` de un identificador uniforme de recursos (URI). | 
| [uriQuery](../logic-apps/workflow-definition-language-functions-reference.md#uriQuery) | Devuelve el valor `query` de un identificador uniforme de recursos (URI). | 
| [uriScheme](../logic-apps/workflow-definition-language-functions-reference.md#uriScheme) | Devuelve el valor `scheme` de un identificador uniforme de recursos (URI). | 
||| 

<a name="manipulation-functions"></a>

### <a name="json-and-xml-functions"></a>Funciones JSON y XML

Para trabajar con objetos JSON y nodos XML, puede usar estas funciones de manipulación. Para obtener la referencia completa sobre cada función, consulte el [artículo de referencia alfabético](../logic-apps/workflow-definition-language-functions-reference.md).

| Función de manipulación | Task | 
| --------------------- | ---- | 
| [addProperty](../logic-apps/workflow-definition-language-functions-reference.md#addProperty) | Agrega una propiedad y su valor o un par de nombre y valor a un objeto JSON y devuelve el objeto actualizado. | 
| [coalesce](../logic-apps/workflow-definition-language-functions-reference.md#coalesce) | Devuelve el primer valor distinto de null de uno o más parámetros. | 
| [removeProperty](../logic-apps/workflow-definition-language-functions-reference.md#removeProperty) | Quita una propiedad de un objeto JSON y devuelve el objeto actualizado. | 
| [setProperty](../logic-apps/workflow-definition-language-functions-reference.md#setProperty) | Establece el valor de propiedad de un objeto JSON y devuelve el objeto actualizado. | 
| [xpath](../logic-apps/workflow-definition-language-functions-reference.md#xpath) | Comprueba el código XML de los nodos o valores que coinciden con una expresión XPath (XML Path Language) y devuelve los nodos o valores coincidentes. | 
||| 

## <a name="next-steps"></a>Pasos siguientes

* Más información sobre [Desencadenadores y acciones para flujos de trabajo](../logic-apps/logic-apps-workflow-actions-triggers.md).
* Aprenda sobre la creación y administración de aplicaciones lógicas mediante programación con la [API REST de flujo de trabajo](https://docs.microsoft.com/rest/api/logic/workflows).
