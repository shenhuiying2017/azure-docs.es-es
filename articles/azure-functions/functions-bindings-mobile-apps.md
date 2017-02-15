---
title: Enlaces de Mobile Apps en Azure Functions | Microsoft Docs
description: "Descubra cómo utilizar los enlaces de Aplicaciones móviles en funciones de Azure."
services: functions
documentationcenter: na
author: ggailey777
manager: erikre
editor: 
tags: 
keywords: "funciones de azure, funciones, procesamiento de eventos, proceso dinámico, arquitectura sin servidor"
ms.assetid: faad1263-0fa5-41a9-964f-aecbc0be706a
ms.service: functions
ms.devlang: multiple
ms.topic: reference
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 10/31/2016
ms.author: glenga
translationtype: Human Translation
ms.sourcegitcommit: 96f253f14395ffaf647645176b81e7dfc4c08935
ms.openlocfilehash: c5e1c02984f9773b263c0bee7685c7d5ff62e658


---
# <a name="azure-functions-mobile-apps-bindings"></a>Enlaces de Aplicaciones móviles en funciones de Azure
[!INCLUDE [functions-selector-bindings](../../includes/functions-selector-bindings.md)]

Este artículo explica cómo configurar y codificar enlaces de [Azure Mobile Apps](../app-service-mobile/app-service-mobile-value-prop.md) en Azure Functions. Azure Functions admite enlaces de entrada y salida para Mobile Apps.

Los enlaces de entrada y salida enlaces de Mobile Apps le permiten [realizar operaciones de lectura y escritura en tablas de datos](../app-service-mobile/app-service-mobile-node-backend-how-to-use-server-sdk.md#TableOperations) en su aplicación móvil.

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

<a name="input"></a>

## <a name="mobile-apps-input-binding"></a>Enlace de entrada de Mobile Apps
El enlace de entrada de Mobile Apps carga un registro desde un punto de conexión de tabla móvil y lo pasa a la función. En una función de C# y F#, los cambios realizados en el registro se enviarán automáticamente a la tabla una vez que la función se complete correctamente.

La entrada de Mobile Apps a una función usa el siguiente objeto JSON en la matriz `bindings` de function.json:

```json
{
    "name": "<Name of input parameter in function signature>",
    "type": "mobileTable",
    "tableName": "<Name of your mobile app's data table>",
    "id" : "<Id of the record to retrieve - see below>",
    "connection": "<Name of app setting that has your mobile app's URL - see below>",
    "apiKey": "<Name of app setting that has your mobile app's API key - see below>",
    "direction": "in"
}
```

Tenga en cuenta lo siguiente:

* `id` puede ser estático o se puede basar en el desencadenador que invoca la función. Por ejemplo, si utiliza un [desencadenador cola]() para la función, entonces `"id": "{queueTrigger}"` usa el valor de cadena del mensaje de cola como el identificador de registro para recuperar.
* `connection` debe contener el nombre de una configuración de aplicación en la aplicación de la función que, a su vez, contiene la dirección URL de la aplicación móvil. La función utiliza esta dirección URL para construir las operaciones de REST necesarias en su aplicación móvil. [Creará una configuración de aplicación en la aplicación de la función]() que contiene la dirección URL de la aplicación móvil (cuyo aspecto es como `http://<appname>.azurewebsites.net`) y después especificará el nombre de la configuración de la aplicación en la propiedad `connection` en el enlace de entrada. 
* Debe especificar `apiKey` si [implementa una clave de API en el back-end de aplicación móvil de Node.js](https://github.com/Azure/azure-mobile-apps-node/tree/master/samples/api-key) o [implementa una clave de API en el back-end de aplicación móvil de .NET](https://github.com/Azure/azure-mobile-apps-net-server/wiki/Implementing-Application-Key). Para hacer esto, [creará una configuración de aplicación en la aplicación de la función]() que contiene la clave de API y después agregará la propiedad `apiKey` en el enlace de entrada con el nombre de la configuración de la aplicación. 
  
  > [!IMPORTANT]
  > Esta clave de API no se debe compartir con los clientes de aplicación móvil. Solo se debe distribuir de forma segura a los clientes del servicio, como Azure Functions. 
  > 
  > [!NOTE]
  > Azure Functions almacena la información de conexión y las claves de API como configuración de la aplicación de forma que no se protegen en el repositorio de control de código fuente. Esto protege la información confidencial.
  > 
  > 

<a name="inputusage"></a>

## <a name="input-usage"></a>Uso de entradas
En esta sección se muestra cómo utilizar el enlace de entrada de Mobile Apps en el código de función. 

Cuando se encuentra el registro con la tabla especificada y un identificador de registro, se pasa en el parámetro denominado [JObject](http://www.newtonsoft.com/json/help/html/t_newtonsoft_json_linq_jobject.htm) (o, en Node.js, se pasa en el objeto `context.bindings.<name>`). Si no se encuentra el registro, el parámetro es `null`. 

En funciones de C# y F#, los cambios realizados en el registro de entrada (parámetro de entrada) se devuelven automáticamente a la tabla de Mobile Apps cuando la función termina correctamente. En las funciones de Node.js, use `context.bindings.<name>` para acceder al registro de entrada. No se puede modificar un registro en Node.js.

<a name="inputsample"></a>

## <a name="input-sample"></a>Ejemplo de entrada
Suponga que tiene el siguiente function.json, que recupera un registro de tabla de Mobile Apps con el identificador del mensaje de desencadenador de cola:

```json
{
"bindings": [
    {
    "name": "myQueueItem",
    "queueName": "myqueue-items",
    "connection":"",
    "type": "queueTrigger",
    "direction": "in"
    },
    {
        "name": "record",
        "type": "mobileTable",
        "tableName": "MyTable",
        "id" : "{queueTrigger}",
        "connection": "My_MobileApp_Url",
        "apiKey": "My_MobileApp_Key",
        "direction": "in"
    }
],
"disabled": false
}
```

Vea el ejemplo específico del idioma que utiliza el registro de entrada del enlace. Los ejemplos de C# y F # también modifican la propiedad `text` del registro.

* [C#](#inputcsharp)
* [Node.js](#inputnodejs)

<a name="inputcsharp"></a>

### <a name="input-sample-in-c"></a>Ejemplo de entrada en C# #

```cs
#r "Newtonsoft.Json"    
using Newtonsoft.Json.Linq;

public static void Run(string myQueueItem, JObject record)
{
    if (record != null)
    {
        record["Text"] = "This has changed.";
    }    
}
```

<!--
<a name="inputfsharp"></a>
### Input sample in F# ## 

```fsharp
#r "Newtonsoft.Json"    
open Newtonsoft.Json.Linq
let Run(myQueueItem: string, record: JObject) =
  inputDocument?text <- "This has changed."
```
-->

<a name="inputnodejs"></a>

### <a name="input-sample-in-nodejs"></a>Ejemplo de entrada en Node.js

```javascript
module.exports = function (context, myQueueItem) {    
    context.log(context.bindings.record);
    context.done();
};
```

<a name="output"></a>

## <a name="mobile-apps-output-binding"></a>Enlace de salida de Mobile Apps
Utilice el enlace de salida Mobile Apps para escribir un registro en un punto de conexión de tabla de Mobile Apps.  

La salida de Mobile Apps para una función usa el siguiente objeto JSON en la matriz `bindings` de function.json:

```json
{
    "name": "<Name of output parameter in function signature>",
    "type": "mobileTable",
    "tableName": "<Name of your mobile app's data table>",
    "connection": "<Name of app setting that has your mobile app's URL - see below>",
    "apiKey": "<Name of app setting that has your mobile app's API key - see below>",
    "direction": "out"
}
```

Tenga en cuenta lo siguiente:

* `connection` debe contener el nombre de una configuración de aplicación en la aplicación de la función que, a su vez, contiene la dirección URL de la aplicación móvil. La función utiliza esta dirección URL para construir las operaciones de REST necesarias en su aplicación móvil. [Creará una configuración de aplicación en la aplicación de la función]() que contiene la dirección URL de la aplicación móvil (cuyo aspecto es como `http://<appname>.azurewebsites.net`) y después especificará el nombre de la configuración de la aplicación en la propiedad `connection` en el enlace de entrada. 
* Debe especificar `apiKey` si [implementa una clave de API en el back-end de aplicación móvil de Node.js](https://github.com/Azure/azure-mobile-apps-node/tree/master/samples/api-key) o [implementa una clave de API en el back-end de aplicación móvil de .NET](https://github.com/Azure/azure-mobile-apps-net-server/wiki/Implementing-Application-Key). Para hacer esto, [creará una configuración de aplicación en la aplicación de la función]() que contiene la clave de API y después agregará la propiedad `apiKey` en el enlace de entrada con el nombre de la configuración de la aplicación. 
  
  > [!IMPORTANT]
  > Esta clave de API no se debe compartir con los clientes de aplicación móvil. Solo se debe distribuir de forma segura a los clientes del servicio, como Azure Functions. 
  > 
  > [!NOTE]
  > Azure Functions almacena la información de conexión y las claves de API como configuración de la aplicación de forma que no se protegen en el repositorio de control de código fuente. Esto protege la información confidencial.
  > 
  > 

<a name="outputusage"></a>

## <a name="output-usage"></a>Uso de salidas
En esta sección se muestra cómo utilizar el enlace de salida de Mobile Apps en el código de función. 

En funciones de C#, utilice un parámetro de salida con nombre de tipo `out object` para acceder al registro de salida. En las funciones de Node.js, use `context.bindings.<name>` para acceder al registro de salida.

<a name="outputsample"></a>

## <a name="output-sample"></a>Ejemplo de salida
Suponga que tiene la siguiente function.json que define un desencadenador de cola y una salida de Mobile Apps:

```json
{
"bindings": [
    {
    "name": "myQueueItem",
    "queueName": "myqueue-items",
    "connection":"",
    "type": "queueTrigger",
    "direction": "in"
    },
    {
    "name": "record",
    "type": "mobileTable",
    "tableName": "MyTable",
    "connection": "My_MobileApp_Url",
    "apiKey": "My_MobileApp_Key",
    "direction": "out"
    }
],
"disabled": false
}
```

Vea el ejemplo específico del idioma que crea un registro en el punto de conexión de la tabla de Mobile Apps con el contenido del mensaje de cola.

* [C#](#outcsharp)
* [Node.js](#outnodejs)

<a name="outcsharp"></a>

### <a name="output-sample-in-c"></a>Ejemplo de salida en C# #

```cs
public static void Run(string myQueueItem, out object record)
{
    record = new {
        Text = $"I'm running in a C# function! {myQueueItem}"
    };
}
```

<!--
<a name="outfsharp"></a>
### Output sample in F# ## 
```fsharp

```
-->
<a name="outnodejs"></a>

### <a name="output-sample-in-nodejs"></a>Ejemplo de salida en Node.js

```javascript
module.exports = function (context, myQueueItem) {

    context.bindings.record = {
        text : "I'm running in a Node function! Data: '" + myQueueItem + "'"
    }   

    context.done();
};
```

## <a name="next-steps"></a>Pasos siguientes
[!INCLUDE [next steps](../../includes/functions-bindings-next-steps.md)]




<!--HONumber=Nov16_HO3-->


