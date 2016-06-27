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
	ms.date="05/13/2016"
	ms.author="chrande"/>

# Referencia para desarrolladores de NodeJS de Funciones de Azure

> [AZURE.SELECTOR]
- [Script de C#](../articles/azure-functions/functions-reference-csharp.md)
- [Node.js](../articles/azure-functions/functions-reference-node.md)

La experiencia de Node/JavaScript para Funciones de Azure facilita la exportación de una función en que se transmite un objeto `context` para comunicarse con el runtime, y recibir y enviar datos por medio de enlaces.

En este artículo se da por hecho que ya se ha leído el tema [Referencia para desarrolladores de Funciones de Azure](functions-reference.md).

## Exportación de una función

Todas las funciones de JavaScript deben exportar un elemento `function` único mediante `module.exports` para que el runtime encuentre la función y la ejecute. Esta función siempre debe incluir un objeto `context`.

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

Se transmiten enlaces de `direction === "in"` junto a los argumentos de la función, lo que significa que se pueden usar [`arguments`](https://msdn.microsoft.com/library/87dw3w1k.aspx) para controlar dinámicamente entradas nuevas (por ejemplo, utilizando `arguments.length` para iterar en todas las entradas). Esta funcionalidad resulta muy práctica si solo dispone de un desencadenador carente de entradas adicionales, ya que puede acceder de manera predecible a los datos del desencadenador sin hacer referencia al objeto `context`.

Los argumentos siempre se transmiten junto con la función en el orden en el que figuren en *function.json*, aunque no los especifique en la instrucción de exportaciones. Por ejemplo, si tiene la función `function(context, a, b)` y la cambia a `function(context, a)`, de todos modos podrá obtener el valor de `b` en el código de función si hace referencia a `arguments[3]`.

Todos los enlaces, al margen de la dirección, también se transmiten junto con el objeto `context` (consulte la información siguiente).

## objeto de contexto

El runtime usa un objeto `context` para transmitir datos desde la función y hacia esta, así como para posibilitar la comunicación con dicho runtime.

El objeto de contexto siempre es el primer parámetro de una función y se debe incluir en todos los casos, porque incorpora métodos como `context.done` y `context.log`, que se precisan para usar correctamente el runtime. Puede ponerle el nombre que desee al objeto (es decir, `ctx` o `c`).

```javascript
// You must include a context, but other arguments are optional
module.exports = function(context) {
    // function logic goes here :)
};
```

## context.bindings

El objeto `context.bindings` recopila todos los datos de entrada y salida. Los datos se agregan al objeto `context.bindings` mediante la propiedad `name` del enlace. Por ejemplo, con la siguiente definición de enlace en *function.json*, podrá acceder al contenido de la cola mediante `context.bindings.myInput`.

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

La función `context.done` le indica al runtime que ya se terminó la ejecución. Es importante llamar a esta función cuando termina con la ejecución; si no lo hace, el tiempo de ejecución nunca sabrá que se completó la función.

La función `context.done` permite transmitir un error definido por el usuario al runtime, además de un conjunto de propiedades que sobrescribirá las propiedades existentes del objeto `context.bindings`.

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

El método `context.log` permite generar instrucciones de registro de salida que se ponen en correlación con el fin de crear registros. Si usa `console.log`, los mensajes solo se mostrarán para el registro de nivel de proceso, lo que no reviste la misma utilidad.

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

## Desencadenadores HTTP: context.req y context.res

En el caso de desencadenadores HTTP, como usar `req` y `res` para los objetos de solicitud y respuesta HTTP constituye un patrón común, decidimos facilitar el acceso a los desencadenadores existentes en el objeto de contexto en lugar de obligarle a utilizar el patrón `context.bindings.name` completo.

```javascript
// You can access your http request off of the context ...
if(context.req.body.emoji === ':pizza:') context.log('Yay!');
// and also set your http response
context.res = { status: 202, body: 'You successfully ordered more coffee!' };   
```

## Versión de Node y administración de paquetes

Actualmente, la versión de Node está bloqueada en `5.9.1`. Estamos investigando para agregar compatibilidad con más versiones y hacerlo configurable.

Puede incluir paquetes en la función cargando un archivo *package.json* en la carpeta de la función del sistema de archivos de la aplicación de función. Para obtener las instrucciones de la carga, consulte la sección **Actualización de los archivos de la aplicación de función** del tema [Referencia para desarrolladores de Funciones de Azure](functions-reference.md#fileupdate).

También puede utilizar `npm install` en la interfaz de la línea de comandos de SCM (Kudu) de la aplicación de función:

1. Vaya a: `https://<function_app_name>.scm.azurewebsites.net`.

2. Haga clic en **Consola de depuración > CMD**.

3. Vaya a `D:\home\site\wwwroot<function_name>`.

4. Ejecute `npm install`.

Una vez que se hayan instalado los paquetes que necesita, impórtelos a la función de la forma habitual (es decir, mediante `require('packagename')`)

```javascript
// Import the underscore.js library
var _ = require('underscore');
var version = process.version; // version === 'v5.9.1'

module.exports = function(context) {
    // Using our imported underscore.js library
    var matched_names = _
        .where(context.bindings.myInput.names, {first: 'Carla'});
```

## Variables de entorno

Para obtener una variable de entorno o un valor de configuración de la aplicación, utilice `process.env` como se muestra en el ejemplo de código siguiente:

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

## Compatibilidad con TypeScript/CoffeeScript

Todavía no hay compatibilidad directa con la compilación automática de TypeScript/CoffeeScript a través del tiempo de ejecución, por lo que se debería controlar fuera del tiempo de ejecución, en tiempo de implementación.

## Pasos siguientes

Para obtener más información, consulte los siguientes recursos:

* [Referencia para desarrolladores de Funciones de Azure](functions-reference.md)
* [Referencia para desarrolladores de C# de Funciones de Azure](functions-reference-csharp.md)
* [Enlaces y desencadenadores de Funciones de Azure](functions-triggers-bindings.md)

<!---HONumber=AcomDC_0615_2016-->