---
title: Desencadenador de temporizador para Azure Functions
description: "Descubra cómo utilizar desencadenadores de temporizador en Azure Functions."
services: functions
documentationcenter: na
author: tdykstra
manager: cfowler
editor: 
tags: 
keywords: "azure functions, funciones, procesamiento de eventos, proceso dinámico, arquitectura sin servidor"
ms.assetid: d2f013d1-f458-42ae-baf8-1810138118ac
ms.service: functions
ms.devlang: multiple
ms.topic: reference
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 02/27/2017
ms.author: tdykstra
ms.custom: 
ms.openlocfilehash: eeb8833470b2ba003ba74b1db57bbd2bbbb7f65d
ms.sourcegitcommit: 85012dbead7879f1f6c2965daa61302eb78bd366
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/02/2018
---
# <a name="timer-trigger-for-azure-functions"></a>Desencadenador de temporizador para Azure Functions 

En este artículo se explica cómo usar desencadenadores de temporizador en Azure Functions. Con un desencadenador de temporizador puede ejecutar una función de forma programada. 

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

## <a name="example"></a>Ejemplo

Vea el ejemplo específico del lenguaje:

* [C#](#trigger---c-example)
* [Script de C# (.csx)](#trigger---c-script-example)
* [F#](#trigger---f-example)
* [JavaScript](#trigger---javascript-example)

### <a name="c-example"></a>Ejemplo de C#

En el ejemplo siguiente se muestra una [función de C#](functions-dotnet-class-library.md) que se ejecuta cada cinco minutos:

```cs
[FunctionName("TimerTriggerCSharp")]
public static void Run([TimerTrigger("0 */5 * * * *")]TimerInfo myTimer, TraceWriter log)
{
    log.Info($"C# Timer trigger function executed at: {DateTime.Now}");
}
```

### <a name="c-script-example"></a>Ejemplo de script de C#

En el ejemplo siguiente se muestra un enlace de desencadenador de temporizador en un archivo *function.json* y una [función de script de C#](functions-reference-csharp.md) que usa el enlace. La función escribe un registro que indica si esta invocación de función se debe a una repetición de la programación no ejecutada.

Estos son los datos de enlace del archivo *function.json*:

```json
{
    "schedule": "0 */5 * * * *",
    "name": "myTimer",
    "type": "timerTrigger",
    "direction": "in"
}
```

Este es el código de script de C#:

```csharp
public static void Run(TimerInfo myTimer, TraceWriter log)
{
    if(myTimer.IsPastDue)
    {
        log.Info("Timer is running late!");
    }
    log.Info($"C# Timer trigger function executed at: {DateTime.Now}" );  
}
```

### <a name="f-example"></a>Ejemplo de F#

En el ejemplo siguiente se muestra un enlace de desencadenador de temporizador en un archivo *function.json* y una [función de script de F#](functions-reference-fsharp.md) que usa el enlace. La función escribe un registro que indica si esta invocación de función se debe a una repetición de la programación no ejecutada.

Estos son los datos de enlace del archivo *function.json*:

```json
{
    "schedule": "0 */5 * * * *",
    "name": "myTimer",
    "type": "timerTrigger",
    "direction": "in"
}
```

Este es el código del script de F#:

```fsharp
let Run(myTimer: TimerInfo, log: TraceWriter ) =
    if (myTimer.IsPastDue) then
        log.Info("F# function is running late.")
    let now = DateTime.Now.ToLongTimeString()
    log.Info(sprintf "F# function executed at %s!" now)
```

### <a name="javascript-example"></a>Ejemplo de JavaScript

En el ejemplo siguiente se muestra un enlace de desencadenador de temporizador en un archivo *function.json* y una [función de JavaScript](functions-reference-node.md) que usa el enlace. La función escribe un registro que indica si esta invocación de función se debe a una repetición de la programación no ejecutada.

Estos son los datos de enlace del archivo *function.json*:

```json
{
    "schedule": "0 */5 * * * *",
    "name": "myTimer",
    "type": "timerTrigger",
    "direction": "in"
}
```

Este es el código del script de JavaScript:

```JavaScript
module.exports = function (context, myTimer) {
    var timeStamp = new Date().toISOString();

    if(myTimer.isPastDue)
    {
        context.log('Node.js is running late!');
    }
    context.log('Node.js timer trigger function ran!', timeStamp);   

    context.done();
};
```

## <a name="attributes"></a>Atributos

En las [bibliotecas de clases de C#](functions-dotnet-class-library.md), use el atributo [TimerTriggerAttribute](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions/Extensions/Timers/TimerTriggerAttribute.cs), que se define en el paquete NuGet [Microsoft.Azure.WebJobs.Extensions](http://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions).

El constructor del atributo toma una expresión CRON, tal como se muestra en el ejemplo siguiente:

```csharp
[FunctionName("TimerTriggerCSharp")]
public static void Run([TimerTrigger("0 */5 * * * *")]TimerInfo myTimer, TraceWriter log)
{
   ...
}
 ```

Puede especificar un `TimeSpan` en lugar de una expresión CRON si la aplicación de función se ejecuta en un plan de App Service (no en un plan de consumo).

Para obtener un ejemplo completo, consulte [Ejemplo de C#](#c-example).

## <a name="configuration"></a>Configuración

En la siguiente tabla se explican las propiedades de configuración de enlace que se definen en el archivo *function.json* y el atributo `TimerTrigger`.

|Propiedad de function.json | Propiedad de atributo |DESCRIPCIÓN|
|---------|---------|----------------------|
|**type** | N/D | Debe establecerse en "timerTrigger". Esta propiedad se establece automáticamente cuando se crea el desencadenador en Azure Portal.|
|**dirección** | N/D | Debe establecerse en "in". Esta propiedad se establece automáticamente cuando se crea el desencadenador en Azure Portal. |
|**name** | N/D | Nombre de la variable que representa el objeto de temporizador en el código de la función. | 
|**schedule**|**ScheduleExpression**|En el plan de consumo, puede definir programaciones con una expresión CRON. Si utiliza un plan de App Service, también puede usar una cadena `TimeSpan`. Las secciones siguientes explican las expresiones CRON. Puede colocar la expresión de programación en una configuración de aplicación y establecer esta propiedad en un valor encapsulado en signos **%**, como en este ejemplo: "%NameOfAppSettingWithCRONExpression%". |

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

### <a name="cron-format"></a>Formato CRON 

Una [expresión CRON](http://en.wikipedia.org/wiki/Cron#CRON_expression) para el desencadenador de temporizador de Azure Functions incluye estos seis campos: 

```
{second} {minute} {hour} {day} {month} {day-of-week}
```

>[!NOTE]   
>En muchas de las expresiones CRON que se encuentran en Internet se omite el campo `{second}`. Si copia información de uno de ellos, agregue el campo que falta, `{second}`.

### <a name="cron-time-zones"></a>Zonas horarias CRON

La zona horaria predeterminada que se usa con las expresiones CRON es la Hora universal coordinada (UTC). Para que la expresión CRON se base en otra zona horaria, cree una nueva configuración de aplicación para la aplicación de función denominada `WEBSITE_TIME_ZONE`. Establezca el valor en el nombre de la zona horaria deseada como se muestra en [Microsoft Time Zone Index](https://technet.microsoft.com/library/cc749073(v=ws.10).aspx) (Índice de zona horaria de Microsoft). 

Por ejemplo, la *Hora estándar del Este* (EST) es UTC-05:00. Para que el desencadenador de temporizador se dispare a las 10 a.m. (Hora estándar), use la siguiente expresión CRON que representa la zona horaria UTC:

```json
"schedule": "0 0 15 * * *",
``` 

Si lo prefiere, puede agregar una nueva configuración de aplicación para la aplicación de función denominada `WEBSITE_TIME_ZONE` y establecer el valor en **Hora estándar del Este**.  A continuación, se podría usar la siguiente expresión CRON para las 10:00 EST: 

```json
"schedule": "0 0 10 * * *",
``` 
### <a name="cron-examples"></a>Ejemplos CRON

Estos son algunos ejemplos de expresiones CRON que puede usar para el desencadenador de temporizador en Azure Functions. 

Para que se desencadene una vez cada cinco minutos:

```json
"schedule": "0 */5 * * * *"
```

Para desencadenar una vez al principio de cada hora:

```json
"schedule": "0 0 * * * *",
```

Para desencadenar una vez cada dos horas:

```json
"schedule": "0 0 */2 * * *",
```

Para desencadenar una vez cada hora de las 9:00 a las 17:00:

```json
"schedule": "0 0 9-17 * * *",
```

Para desencadenar a las 9:30 cada día:

```json
"schedule": "0 30 9 * * *",
```

Para desencadenar a 9:30 cada día comprendido entre lunes y viernes:

```json
"schedule": "0 30 9 * * 1-5",
```

## <a name="usage"></a>Uso

Cuando se invoca una función de desencadenador de temporizador, se pasa a esta el [objeto de temporizador](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions/Extensions/Timers/TimerInfo.cs). La siguiente función JSON es un ejemplo que representa el objeto de temporizador. 

```json
{
    "Schedule":{
    },
    "ScheduleStatus": {
        "Last":"2016-10-04T10:15:00.012699+00:00",
        "Next":"2016-10-04T10:20:00+00:00"
    },
    "IsPastDue":false
}
```

## <a name="scale-out"></a>Escalado horizontal

El desencadenador de temporizador admite varias instancias de escalado horizontal. Una sola instancia de una función de temporizador determinada se ejecuta en todas las instancias.

## <a name="next-steps"></a>pasos siguientes

> [!div class="nextstepaction"]
> [Ir a una guía de inicio rápido que use un desencadenador de temporizador](functions-create-scheduled-function.md)

> [!div class="nextstepaction"]
> [Más información sobre desencadenadores y enlaces de Azure Functions](functions-triggers-bindings.md)
