<properties
	pageTitle="Referencia para desarrolladores de NodeJS de Funciones de Azure | Microsoft Azure"
	description="Cómo desarrollar Funciones de Azure con NodeJS."
	services="functions"
	documentationCenter="na"
	authors="christopheranderson"
	manager="erikre"
	editor=""
	tags=""
	keywords="Azure funciones, funciones, procesamiento de eventos, webhooks, proceso dinámico, arquitectura sin servidor"/>

<tags
	ms.service="functions"
	ms.devlang="nodejs"
	ms.topic="reference"
	ms.tgt_pltfrm="multiple"
	ms.workload="na"
	ms.date="04/06/2016"
	ms.author="chrande"/>

# Referencia para desarrolladores de NodeJS de Funciones de Azure

La experiencia de Node/JavaScript para Funciones de Azure facilita la exportación de una función en que se transmite un objeto `context` para comunicarse con el tiempo de ejecución y recibir y enviar datos a través de enlaces.

En este artículo se supone que ya leyó la [referencia para desarrolladores de Funciones de Azure](functions-reference.md).

## Exportación de una función

Todas las funciones de JavaScript deben exportar una `function` única a través de `module.exports` para que el tiempo de ejecución encuentre la función y la ejecute. Esta función siempre debe incluir un objeto `context`.

```javascript
// You must include a context, but other arguments are optional
module.exports = function(context) {
    // Additional inputs can be accessed by the arguments property
    if(arguments.length === 4) {
        context.log('This function has 4 inputs');
    }
};
// or you can include additional inputs in your arguments
module.exports = function(context, myTrigger, myInput, myOtherInput) {
    // function logic goes here :)
};
```

Enlaces de `direction === "in"` se transmiten junto a los argumentos de la función, lo que significa que se pueden usar [`arguments`](https://msdn.microsoft.com/library/87dw3w1k.aspx) para controlar dinámicamente entradas nuevas (por ejemplo, mediante el uso de `arguments.length` para iterar sobre todas las entradas). Esta funcionalidad resulta muy conveniente si solo tiene un desencadenador sin entradas adicionales, debido a que puede tener acceso de manera predecible a los datos del desencadenador sin hacer referencia al objeto `context`.

Los argumentos siempre se transmiten junto con la función para que se produzcan en *function.json*, aunque no los especifique en la instrucción de exportaciones. Por ejemplo, si tiene la función `function(context, a, b)` y la cambia a la función `function(context, a)`, de todos modos puede obtener el valor de `b` en el código de función si hace referencia a `arguments[3]`.

Todos los enlaces, independientemente de la dirección, también se transmiten junto al objeto `context` (consulte a continuación).

## objeto de contexto

El tiempo de ejecución usa un objeto `context` para transmitir datos desde y hacia la función y para permitir la comunicación con el tiempo de ejecución.

El objeto de contexto siempre es el primer parámetro de una función y se debe incluir siempre, porque incluye métodos como `context.done` y `context.log`, los que son necesarios para usar correctamente el tiempo de ejecución. Puede ponerle el nombre que desee al objeto (es decir, `ctx` o `c`).

```javascript
// You must include a context, but other arguments are optional
module.exports = function(context) {
    // function logic goes here :)
};
```

## context.bindings

El objeto `context.bindings` recopila todos los datos de entrada y salida. Los datos se agregan al objeto `context.bindings` mediante la propiedad `name` del enlace. Por ejemplo, dada la siguiente definición de enlace en *function.json*, puede tener acceso al contenido de la cola a través de `context.bindings.myInput`.

```json
    {
        "type":"queue",
        "direction":"in",
        "name":"myInput"
        ...
    }
```

```javascript
// myInput contains the input data which may have properties such as "name"
var author = context.bindings.myInput.name;
// Similarly, you can set your output data
context.bindings.myOutput = { 
        some_text: 'hello world', 
        a_number: 1 };
```

## `context.done([err],[propertyBag])`

La función `context.done` le indica al tiempo de ejecución que ya se terminó la ejecución. Es importante llamar a esta función cuando termina con la ejecución; si no lo hace, el tiempo de ejecución nunca sabrá que se completó la función.

La función `context.done` le permite transmitir un error definido por el usuario de vuelta al tiempo de ejecución, además de un conjunto de propiedades que sobrescribirá las propiedades existentes en el objeto `context.bindings`.

```javascript
// Even though we set myOutput to have:
//  -> text: hello world, number: 123
context.bindings.myOutput = { text: 'hello world', number: 123 };
// If we pass an object to the done function...
context.done(null, { myOutput: { text: 'hello there, world', noNumber: true }});
// the done method will overwrite the myOutput binding to be: 
//  -> text: hello there, world, noNumber: true
```

## context.log(mensaje)

El método `context.log` le permite generar instrucciones de registro que se correlacionan con el fin de generar registros. Si usa `console.log`, los mensajes solo se mostrarán para el registro a nivel de proceso, lo que no reviste ninguna utilidad.

```javascript
/* You can use context.log to log output specific to this 
function. You can access your bindings via context.bindings */
context.log({hello: 'world'}); // logs: { 'hello': 'world' } 
```

El método `context.log` admite el mismo formato de parámetro que admite el [método util.format](https://nodejs.org/api/util.html#util_util_format_format) de Node. Por ejemplo, un código similar al siguiente:

```javascript
context.log('Node.js HTTP trigger function processed a request. RequestUri=' + req.originalUrl);
context.log('Request Headers = ' + JSON.stringify(req.headers));
```

se puede escribir de la siguiente manera:

```javascript
context.log('Node.js HTTP trigger function processed a request. RequestUri=%s', req.originalUrl);
context.log('Request Headers = ', JSON.stringify(req.headers));
```

## Desencadenadores HTTP: context.req y context.res

En el caso de desencadenadores HTTP, como usar `req` y `res` para la solicitud HTTP y los objetos de respuesta es un patrón común, decidimos facilitar el acceso a los desencadenadores existentes en el objeto de contexto en lugar de forzarle a usar el patrón `context.bindings.name` completo.

```javascript
// You can access your http request off of the context ...
if(context.req.body.emoji === ':pizza:') context.log('Yay!');
// and also set your http response
context.res = { status: 202, body: 'You successfully ordered more coffee!' };   
```

## Versión de Node y administración de paquetes

La versión de Node actualmente está bloqueada en `5.9.1`. Estamos investigando para agregar compatibilidad con más versiones y hacerlo configurable.

Puede incluir paquetes en el directorio de la función (es decir, a través de `npm install`) y, luego, importarlos a su función de manera habitual (es decir, a través de `require('packagename')`).

```javascript
// Import the underescore.js library
var _ = require('underscore');
var version = process.version; // version === 'v5.9.1'

module.exports = function(context) {
    // Using our imported underscore.js library
    var matched_names = _
        .where(context.bindings.myInput.names, {first: 'Carla'});
```

## Compatibilidad con TypeScript/CoffeeScript

Todavía no hay compatibilidad directa con la compilación automática de TypeScript/CoffeeScript a través del tiempo de ejecución, por lo que se debería controlar fuera del tiempo de ejecución, en tiempo de implementación.

## Pasos siguientes

Para obtener más información, consulte los siguientes recursos:

* [Referencia para desarrolladores de Funciones de Azure](functions-reference.md)
* [Referencia para desarrolladores de C# de Funciones de Azure](functions-reference-csharp.md)
* [Enlaces y desencadenadores de Funciones de Azure](functions-triggers-bindings.md)

<!---HONumber=AcomDC_0420_2016-->