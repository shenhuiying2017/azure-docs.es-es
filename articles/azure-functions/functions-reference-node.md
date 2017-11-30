---
title: Referencia para desarrolladores de JavaScript para Azure Functions | Microsoft Docs
description: "Obtenga información sobre cómo desarrollar funciones con JavaScript."
services: functions
documentationcenter: na
author: tdykstra
manager: cfowler
editor: 
tags: 
keywords: "Azure funciones, funciones, procesamiento de eventos, webhooks, proceso dinámico, arquitectura sin servidor"
ms.assetid: 45dedd78-3ff9-411f-bb4b-16d29a11384c
ms.service: functions
ms.devlang: nodejs
ms.topic: reference
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 05/25/2017
ms.author: tdykstra
ms.openlocfilehash: f613e480f6699b323c18402f01873e565768f10f
ms.sourcegitcommit: 29bac59f1d62f38740b60274cb4912816ee775ea
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/29/2017
---
# <a name="azure-functions-javascript-developer-guide"></a>Guía para el desarrollador de JavaScript para Azure Functions
[!INCLUDE [functions-selector-languages](../../includes/functions-selector-languages.md)]

La experiencia de JavaScript para Azure Functions facilita la exportación de una función en que se transmite como un objeto `context` para comunicarse con el sistema en tiempo de ejecución, y recibir y enviar datos por medio de enlaces.

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

Los enlaces de `direction === "in"` se transmiten como argumentos de la función, lo que significa que se pueden usar [`arguments`](https://msdn.microsoft.com/library/87dw3w1k.aspx) para controlar de forma dinámica las entradas nuevas (por ejemplo, con el uso de `arguments.length` para iterar en todas las entradas). Esta funcionalidad resulta conveniente si solo dispone de un desencadenador sin entradas adicionales, ya que puede acceder de manera predecible a los datos del desencadenador sin hacer referencia al objeto `context`.

Los argumentos siempre se transmiten a la función en el orden en el que figuran en *function.json*, aunque no los especifique en la instrucción de exportaciones. Por ejemplo, si tiene la función `function(context, a, b)` y la cambia a `function(context, a)`, podrá seguir obteniendo el valor `b` en el código de función si hace referencia a `arguments[2]`.

Todos los enlaces, al margen de la dirección, también se transmiten junto con el objeto `context` (consulte el script siguiente). 

## <a name="context-object"></a>objeto de contexto
El sistema en tiempo de ejecución usa un objeto `context` para transmitir datos desde la función y hacia esta, así como para posibilitar la comunicación con dicho sistema en tiempo de ejecución.

El objeto `context.log` siempre es el primer parámetro de una función y se tiene que incluir, porque incorpora métodos como `context` y `context.done`, que se precisan para usar correctamente el sistema en tiempo de ejecución. Puede asignar el nombre que desee al objeto (por ejemplo, `ctx` o `c`).

```javascript
// You must include a context, but other arguments are optional
module.exports = function(context) {
    // function logic goes here :)
};
```

### <a name="contextbindings-property"></a>Propiedad context.bindings

```
context.bindings
```
Devuelve un objeto con nombre que contiene todos los datos de entrada y salida. Por ejemplo, la siguiente definición de enlace en *function.json* permite acceder al contenido de la cola desde el objeto `context.bindings.myInput`. 

```json
{
    "type":"queue",
    "direction":"in",
    "name":"myInput"
    ...
}
```

```javascript
// myInput contains the input data, which may have properties such as "name"
var author = context.bindings.myInput.name;
// Similarly, you can set your output data
context.bindings.myOutput = { 
        some_text: 'hello world', 
        a_number: 1 };
```

### <a name="contextdone-method"></a>Método context.done
```
context.done([err],[propertyBag])
```

Informa al tiempo de ejecución de que el código ha terminado. Debe llamar a `context.done` o, de lo contrario, el tiempo de ejecución nunca sabe que la función se ha completado y, por tanto, se agota el tiempo de espera de la ejecución. 

El método `context.done` permite transmitir un error definido por el usuario al sistema en tiempo de ejecución y un contenedor de propiedades que sobrescribirá las propiedades del objeto `context.bindings`.

```javascript
// Even though we set myOutput to have:
//  -> text: hello world, number: 123
context.bindings.myOutput = { text: 'hello world', number: 123 };
// If we pass an object to the done function...
context.done(null, { myOutput: { text: 'hello there, world', noNumber: true }});
// the done method will overwrite the myOutput binding to be: 
//  -> text: hello there, world, noNumber: true
```

### <a name="contextlog-method"></a>Método context.log  

```
context.log(message)
```
Permite escribir en los registros de la consola de streaming en el nivel de seguimiento predeterminado. Hay métodos de registro adicionales disponibles en `context.log` que permiten escribir en el registro de la consola en otros niveles de seguimiento:


| Método                 | Descripción                                |
| ---------------------- | ------------------------------------------ |
| **error(_message_)**   | Escribe en el registro de nivel de error o inferior.   |
| **warn(_message_)**    | Escribe en el registro de nivel de advertencia o inferior. |
| **info(_message_)**    | Escribe en el registro de nivel de información o inferior.    |
| **verbose(_message_)** | Escribe en el registro de nivel detallado.           |

En el ejemplo siguiente se escribe en la consola en el nivel de seguimiento de advertencia:

```javascript
context.log.warn("Something has happened."); 
```
Puede establecer el umbral de nivel de seguimiento de los registros en el archivo host.json o desactivarlo.  Para obtener más información sobre cómo escribir en los registros, vea la sección siguiente.

## <a name="binding-data-type"></a>Tipo de datos de enlace

Para definir el tipo de datos para un enlace de entrada, use la propiedad `dataType` de la definición del enlace. Por ejemplo, para leer el contenido de una solicitud HTTP en formato binario, use el tipo `binary`:

```json
{
    "type": "httpTrigger",
    "name": "req",
    "direction": "in",
    "dataType": "binary"
}
```

Otras opciones para `dataType` son `stream` y `string`.

## <a name="writing-trace-output-to-the-console"></a>Escribir las salidas de seguimiento en la consola 

En Functions, use los métodos `context.log` para escribir la salida de seguimiento en la consola. En este momento, no puede usar `console.log` para escribir en la consola.

Cuando se llama a `context.log()`, el mensaje se escribe en la consola en el nivel de seguimiento predeterminado, que es el nivel de seguimiento de _información_. El siguiente código escribe en la consola en el nivel de seguimiento de información:

```javascript
context.log({hello: 'world'});  
```

El código anterior es equivalente al siguiente:

```javascript
context.log.info({hello: 'world'});  
```

El siguiente código escribe en la consola en el nivel de seguimiento de error:

```javascript
context.log.error("An error has occurred.");  
```

Dado que _error_ es el nivel de seguimiento más alto, este seguimiento se escribe en la salida en todos los niveles de seguimiento, siempre y cuando el registro esté habilitado.  


Todos los métodos `context.log` admiten el mismo formato de parámetro que el [método util.format](https://nodejs.org/api/util.html#util_util_format_format) de Node.js. Considere el siguiente código que escribe en la consola mediante el nivel de seguimiento predeterminado:

```javascript
context.log('Node.js HTTP trigger function processed a request. RequestUri=' + req.originalUrl);
context.log('Request Headers = ' + JSON.stringify(req.headers));
```

También puede escribir el mismo código con el formato siguiente:

```javascript
context.log('Node.js HTTP trigger function processed a request. RequestUri=%s', req.originalUrl);
context.log('Request Headers = ', JSON.stringify(req.headers));
```

### <a name="configure-the-trace-level-for-console-logging"></a>Configuración del nivel de seguimiento para el registro de la consola

Functions permite definir el nivel de seguimiento de umbral para escribir en la consola, que facilita el control de la forma en que se escriben los seguimientos en la consola desde las funciones. Para establecer el umbral para todos los seguimientos que se escriben en la consola, use la propiedad `tracing.consoleLevel` en el archivo host.json . Esta configuración se aplica a todas las funciones de Function App. En el ejemplo siguiente se establece el umbral de seguimiento para habilitar el registro detallado:

```json
{ 
    "tracing": {      
        "consoleLevel": "verbose"     
    }
}  
```

Los valores de **consoleLevel** corresponden a los nombres de los métodos `context.log`. Para deshabilitar todos los registros de seguimiento en la consola, establezca **consoleLevel** en _off_. Para más información, consulte la [referencia sobre host.json](functions-host-json.md).

## <a name="http-triggers-and-bindings"></a>Desencadenadores y enlaces HTTP

Los desencadenadores HTTP y de webhook trigger y los enlaces de salida HTTP usan objetos de solicitud y respuesta para representar la mensajería HTTP.  

### <a name="request-object"></a>Objeto de solicitud

El objeto `request` tiene las siguientes propiedades:

| Propiedad      | Descripción                                                    |
| ------------- | -------------------------------------------------------------- |
| _body_        | Objeto que contiene el cuerpo de la solicitud.               |
| _headers_     | Objeto que contiene los encabezados de la solicitud.                   |
| _method_      | Método HTTP de la solicitud.                                |
| _originalUrl_ | Dirección URL de la solicitud.                                        |
| _params_      | Objeto que contiene los parámetros de enrutamiento de la solicitud. |
| _consulta_       | Objeto que contiene los parámetros de consulta.                  |
| _rawBody_     | Cuerpo del mensaje como una cadena.                           |


### <a name="response-object"></a>Objeto de respuesta

El objeto `response` tiene las siguientes propiedades:

| Propiedad  | Descripción                                               |
| --------- | --------------------------------------------------------- |
| _body_    | Objeto que contiene el cuerpo de la respuesta.         |
| _headers_ | Objeto que contiene los encabezados de la respuesta.             |
| _isRaw_   | Indica que se omite el formato en la respuesta.    |
| _estado_  | Código de estado HTTP de la respuesta.                     |

### <a name="accessing-the-request-and-response"></a>Acceso a solicitudes y respuestas 

Cuando se trabaja con desencadenadores HTTP, hay tres maneras de acceder a los objetos de solicitud y respuesta HTTP:

+ Desde los enlaces de entrada y salida con nombre. De esta manera, el desencadenador HTTP y los enlaces funcionan igual que cualquier otro enlace. En el ejemplo siguiente se establece el objeto de respuesta mediante un enlace `response` con nombre: 

    ```javascript
    context.bindings.response = { status: 201, body: "Insert succeeded." };
    ```

+ Desde las propiedades `req` y `res` del objeto `context`. De esta manera, puede usar el patrón convencional para acceder a los datos HTTP desde el objeto de contexto, en lugar de tener que utilizar el patrón `context.bindings.name` completo. En el ejemplo siguiente se muestra cómo acceder a los objetos `req` y `res` en `context`:

    ```javascript
    // You can access your http request off the context ...
    if(context.req.body.emoji === ':pizza:') context.log('Yay!');
    // and also set your http response
    context.res = { status: 202, body: 'You successfully ordered more coffee!' }; 
    ```

+ Mediante una llamada a `context.done()`. Un tipo especial de enlace HTTP que devuelve la respuesta que se pasa al método `context.done()`. El enlace de salida HTTP siguiente define un parámetro de salida `$return`:

    ```json
    {
      "type": "http",
      "direction": "out",
      "name": "$return"
    }
    ``` 
    Este enlace de salida espera que proporcione la respuesta cuando se llama a `done()`, como se indica a continuación:

    ```javascript
     // Define a valid response object.
    res = { status: 201, body: "Insert succeeded." };
    context.done(null, res);   
    ```  

## <a name="node-version-and-package-management"></a>Versión de Node y administración de paquetes
Actualmente, la versión de Node está bloqueada en `6.5.0`. Estamos investigando para agregar compatibilidad con más versiones y hacerlo configurable.

Los pasos siguientes le permiten incluir paquetes en Function App: 

1. Vaya a `https://<function_app_name>.scm.azurewebsites.net`.

2. Haga clic en **Consola de depuración** > **CMD**.

3. Vaya a `D:\home\site\wwwroot` y luego arrastre el archivo package.json a la carpeta **wwwroot** en la mitad superior de la página.  
    También puede cargar archivos en Function App de otras formas. Para obtener más información, vea [Actualización de los archivos de aplicación de función](functions-reference.md#fileupdate). 

4. Una vez cargado el archivo package.json, ejecute el comando`npm install` en la **consola de ejecución remota de Kudu**.  
    Esta acción descarga los paquetes indicados en el archivo package.json y se reinicia Function App.

Una vez que se hayan instalado los paquetes que necesita, impórtelos a la función llamando a `require('packagename')`, como en el ejemplo siguiente.

```javascript
// Import the underscore.js library
var _ = require('underscore');
var version = process.version; // version === 'v6.5.0'

module.exports = function(context) {
    // Using our imported underscore.js library
    var matched_names = _
        .where(context.bindings.myInput.names, {first: 'Carla'});
```

Debe definir un archivo `package.json` en la raíz de Function App. La definición del archivo permite que todas las funciones de la aplicación compartan los mismos paquetes almacenados en caché, de tal manera que se ofrece el mejor rendimiento. Cuando hay conflictos con una versión, puede resolverlo mediante la adición de un archivo `package.json` en la carpeta de una función específica.  

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
## <a name="considerations-for-javascript-functions"></a>Consideraciones para las funciones de JavaScript

Cuando se trabaja con las funciones de JavaScript, tenga en cuenta las consideraciones de las dos secciones siguientes.

### <a name="choose-single-vcpu-app-service-plans"></a>Elección de los planes de App Service de una sola vCPU

Al crear una aplicación de función que usa el plan de App Service, se recomienda que seleccione un plan de una sola vCPU, en lugar de un plan con varias vCPU. En la actualidad, Functions ejecuta funciones de JavaScript con más eficacia en VM con una sola vCPU; el uso de máquinas virtuales más grandes no produce las mejoras de rendimiento esperadas. Cuando sea necesario, puede escalar horizontalmente de forma manual mediante la adición de más instancias de VM de una sola vCPU o bien puede habilitar el escalado automático. Para obtener más información, consulte [Escalación del recuento de instancias de forma manual o automática](../monitoring-and-diagnostics/insights-how-to-scale.md?toc=%2fazure%2fapp-service-web%2ftoc.json).    

### <a name="typescript-and-coffeescript-support"></a>Compatibilidad con TypeScript y CoffeeScript
Como no hay compatibilidad directa con la compilación automática de TypeScript o CoffeeScript a través del tiempo de ejecución, dicha compatibilidad debe controlarse fuera del tiempo de ejecución, en tiempo de implementación. 

## <a name="next-steps"></a>Pasos siguientes
Para obtener más información, consulte los siguientes recursos:

* [Procedimientos recomendados para Azure Functions](functions-best-practices.md)
* 
            [Referencia para desarrolladores de Azure Functions](functions-reference.md)
* [Enlaces y desencadenadores de Azure Functions](functions-triggers-bindings.md)

