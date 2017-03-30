---
title: Referencia para desarrolladores de JavaScript para Azure Functions | Microsoft Docs
description: "Cómo desarrollar Azure Functions con JavaScript."
services: functions
documentationcenter: na
author: christopheranderson
manager: erikre
editor: 
tags: 
keywords: "Azure funciones, funciones, procesamiento de eventos, webhooks, proceso dinámico, arquitectura sin servidor"
ms.assetid: 45dedd78-3ff9-411f-bb4b-16d29a11384c
ms.service: functions
ms.devlang: nodejs
ms.topic: reference
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 05/13/2016
ms.author: chrande, glenga
translationtype: Human Translation
ms.sourcegitcommit: 424d8654a047a28ef6e32b73952cf98d28547f4f
ms.openlocfilehash: 2c2d352a8aaf572612e64bd69e6e45616c15891d
ms.lasthandoff: 03/22/2017


---
# <a name="azure-functions-javascript-developer-guide"></a>Guía para el desarrollador de JavaScript para Azure Functions
> [!div class="op_single_selector"]
> * [Script de C#](functions-reference-csharp.md)
> * [Script de F#](functions-reference-fsharp.md)
> * [JavaScript](functions-reference-node.md)
> 
> 

La experiencia de JavaScript para Azure Functions facilita la exportación de una función en que se transmite un objeto `context` para comunicarse con el sistema en tiempo de ejecución, y recibir y enviar datos por medio de enlaces.

En este artículo se supone que ya ha leído [Referencia para desarrolladores de Azure Functions](functions-reference.md).

## <a name="exporting-a-function"></a>Exportación de una función
Todas las funciones de JavaScript tiene que exportar un elemento `function` único mediante `module.exports` para que el sistema en tiempo de ejecución encuentre la función y la ejecute. Esta función siempre tiene que incluir un objeto `context` .

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

Los enlaces de `direction === "in"` se transmiten como argumentos de la función, lo que significa que se pueden usar [`arguments`](https://msdn.microsoft.com/library/87dw3w1k.aspx) para controlar de forma dinámica las entradas nuevas (por ejemplo, con el uso de `arguments.length` para iterar en todas las entradas). Esta funcionalidad resulta muy práctica si solo dispone de un desencadenador sin entradas adicionales, ya que puede acceder de manera predecible a los datos del desencadenador sin hacer referencia al objeto `context` .

Los argumentos siempre se transmiten a la función en el orden en el que figuren en *function.json*, aunque no los especifique en la instrucción de exportaciones. Por ejemplo, si tiene la función `function(context, a, b)` y la cambia a `function(context, a)`, podrá seguir obteniendo el valor `b` en el código de función si hace referencia a `arguments[3]`.

Todos los enlaces, al margen de la dirección, también se transmiten junto con el objeto `context` (consulte la información siguiente). 

## <a name="context-object"></a>objeto de contexto
El sistema en tiempo de ejecución usa un objeto `context` para transmitir datos desde la función y hacia esta, así como para posibilitar la comunicación con dicho sistema en tiempo de ejecución.

El objeto de contexto siempre es el primer parámetro de una función y se debe incluir siempre, porque incorpora métodos como `context.done` y `context.log`, que se precisan para usar correctamente el sistema en tiempo de ejecución. Puede ponerle el nombre que desee al objeto (es decir, `ctx` o `c`).

```javascript
// You must include a context, but other arguments are optional
module.exports = function(context) {
    // function logic goes here :)
};
```

## <a name="contextbindings"></a>context.bindings
El objeto `context.bindings` recopila todos los datos de entrada y salida. Los datos se agregan al objeto `context.bindings` mediante la propiedad `name` del enlace. Por ejemplo, con la siguiente definición de enlace en *function.json*, se puede acceder al contenido de la cola mediante `context.bindings.myInput`. 

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
La función `context.done` le indica al sistema en tiempo de ejecución que ya se terminó la ejecución. Es importante llamar a esta función cuando termina con la ejecución; si no lo hace, el tiempo de ejecución nunca sabrá que se completó la función. 

La función `context.done` permite transmitir un error definido por el usuario al sistema en tiempo de ejecución, además de un contenedor de propiedades que sobrescribirá las propiedades existentes del objeto `context.bindings`.

```javascript
// Even though we set myOutput to have:
//  -> text: hello world, number: 123
context.bindings.myOutput = { text: 'hello world', number: 123 };
// If we pass an object to the done function...
context.done(null, { myOutput: { text: 'hello there, world', noNumber: true }});
// the done method will overwrite the myOutput binding to be: 
//  -> text: hello there, world, noNumber: true
```

## <a name="contextlogmessage"></a>context.log(mensaje)
El método `context.log` permite generar instrucciones de registro de salida que se ponen en correlación con el fin de crear registros. Si usa `console.log`, los mensajes solo se mostrarán para el registro de nivel de proceso, lo que no resulta tan útil.

```javascript
/* You can use context.log to log output specific to this 
function. You can access your bindings via context.bindings */
context.log({hello: 'world'}); // logs: { 'hello': 'world' } 
```

El método `context.log` admite el mismo formato de parámetro que el [método util.format](https://nodejs.org/api/util.html#util_util_format_format) de Node. Por ejemplo, un código similar al siguiente:

```javascript
context.log('Node.js HTTP trigger function processed a request. RequestUri=' + req.originalUrl);
context.log('Request Headers = ' + JSON.stringify(req.headers));
```

se puede escribir de la siguiente manera:

```javascript
context.log('Node.js HTTP trigger function processed a request. RequestUri=%s', req.originalUrl);
context.log('Request Headers = ', JSON.stringify(req.headers));
```

## <a name="http-triggers-contextreq-and-contextres"></a>Desencadenadores HTTP: context.req y context.res
En el caso de desencadenadores HTTP, ya que usar `req` y `res` para los objetos de solicitud y respuesta HTTP constituye un patrón común, decidimos facilitar el acceso a los desencadenadores en el objeto de contexto, en lugar de obligarle a utilizar el patrón `context.bindings.name` completo.

```javascript
// You can access your http request off of the context ...
if(context.req.body.emoji === ':pizza:') context.log('Yay!');
// and also set your http response
context.res = { status: 202, body: 'You successfully ordered more coffee!' };   
```

## <a name="node-version--package-management"></a>Versión de Node y administración de paquetes
Actualmente, la versión de Node está bloqueada en `6.5.0`. Estamos investigando para agregar compatibilidad con más versiones y hacerlo configurable.

Puede incluir paquetes en la función cargando un archivo *package.json* en la carpeta de la función del sistema de archivos de la aplicación de función. Para obtener instrucciones para la carga, consulte la sección **Actualización de los archivos de la aplicación de función** del tema [Referencia para desarrolladores de Azure Functions](functions-reference.md#fileupdate). 

También puede utilizar `npm install` en la interfaz de la línea de comandos de SCM (Kudu) de la aplicación de función:

1. Vaya a `https://<function_app_name>.scm.azurewebsites.net`.
2. Haga clic en **Consola de depuración > CMD**.
3. Vaya a `D:\home\site\wwwroot\<function_name>`.
4. Ejecute `npm install`.

Una vez que se hayan instalado los paquetes que necesita, impórtelos a la función de la forma habitual (es decir, mediante `require('packagename')`)

```javascript
// Import the underscore.js library
var _ = require('underscore');
var version = process.version; // version === 'v6.5.0'

module.exports = function(context) {
    // Using our imported underscore.js library
    var matched_names = _
        .where(context.bindings.myInput.names, {first: 'Carla'});
```

El nodo debe tener `package.json` en la raíz de la aplicación de función, por lo que las funciones pueden compartir paquetes almacenados en caché. Si hay conflictos de versiones, puede agregar `package.json` en un nivel de función. Sin embargo, se debe evitar por motivos de rendimiento. 

## <a name="environment-variables"></a>Variables de entorno
Para obtener una variable de entorno o un valor de configuración de aplicación, use `process.env`, como se muestra en el ejemplo de código siguiente:

```javascript
module.exports = function (context, myTimer) {
    var timeStamp = new Date().toISOString();

    context.log('Node.js timer trigger function ran!', timeStamp);   
    context.log(GetEnvironmentVariable("AzureWebJobsStorage"));
    context.log(GetEnvironmentVariable("WEBSITE_SITE_NAME"));

    context.done();
};

function GetEnvironmentVariable(name)
{
    return name + ": " + process.env[name];
}
```

## <a name="typescriptcoffeescript-support"></a>Compatibilidad con TypeScript/CoffeeScript
Todavía no hay compatibilidad directa con la compilación automática de TypeScript/CoffeeScript a través del tiempo de ejecución, por lo que se debería controlar fuera del tiempo de ejecución, en tiempo de implementación. 

## <a name="next-steps"></a>Pasos siguientes
Para obtener más información, consulte los siguientes recursos:

* [Procedimientos recomendados de Azure Functions](functions-best-practices.md)
* [Azure Functions developer reference](functions-reference.md)
* [Referencia para desarrolladores de C# de Funciones de Azure](functions-reference-csharp.md)
* [Referencia para desarrolladores de F# de Azure Functions](functions-reference-fsharp.md)
* [Enlaces y desencadenadores de las Funciones de azure](functions-triggers-bindings.md)


