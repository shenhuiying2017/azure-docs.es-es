---
title: Desencadenador de temporizador de Azure Functions | Microsoft Docs
description: "Descubra cómo utilizar desencadenadores de temporizador en funciones de Azure."
services: functions
documentationcenter: na
author: christopheranderson
manager: erikre
editor: 
tags: 
keywords: "funciones de azure, funciones, procesamiento de eventos, proceso dinámico, arquitectura sin servidor"
ms.assetid: d2f013d1-f458-42ae-baf8-1810138118ac
ms.service: functions
ms.devlang: multiple
ms.topic: reference
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 10/31/2016
ms.author: chrande; glenga
translationtype: Human Translation
ms.sourcegitcommit: b41a5aacec6748af5ee05b01487310cc339af1f9
ms.openlocfilehash: 542e5378aff893741a68c979bc2c5e8bfe58ba26


---
# <a name="azure-functions-timer-trigger"></a>Desencadenador de temporizador de funciones de Azure
[!INCLUDE [functions-selector-bindings](../../includes/functions-selector-bindings.md)]

En este artículo se explica cómo configurar y codificar desencadenadores de temporizador en Azure Functions. Azure Functions admite los desencadenadores de temporizadores. El temporizador desencadena las funciones de llamada según una programación, una hora o de forma periódica. 

El desencadenador de temporizador admite varias instancias de escalado horizontal. Una sola instancia de una función de temporizador determinada se ejecuta en todas las instancias.

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

<a id="trigger"></a>

## <a name="timer-trigger"></a>Desencadenador de temporizador
El desencadenador de temporizador de una función usa el siguiente objeto JSON en la matriz `bindings` de function.json:

```json
{
    "schedule": "<CRON expression - see below>",
    "name": "<Name of trigger parameter in function signature>",
    "type": "timerTrigger",
    "direction": "in"
}
```

El valor de `schedule` es una [expresión CRON](http://en.wikipedia.org/wiki/Cron#CRON_expression) que incluye 6 campos: `{second} {minute} {hour} {day} {month} {day of the week}`. En muchas de las expresiones CRON que se encuentran en Internet se omite el campo `{second}`. Si copia alguna de ellas, deberá ajustarla para el campo `{second}` adicional. Para obtener ejemplos específicos, vea [Programación de ejemplos](#examples) a continuación.

La zona horaria predeterminada que se usa con las expresiones CRON es la Hora universal coordinada (UTC). Si desea que la expresión CRON se base en otra zona horaria, cree una nueva configuración de aplicación para la aplicación de función denominada `WEBSITE_TIME_ZONE`. Establezca el valor en el nombre de la zona horaria deseada como se muestra en [Microsoft Time Zone Index](https://msdn.microsoft.com/library/ms912391.aspx) (Índice de zona horaria de Microsoft). 

Por ejemplo, la *Hora estándar del Este* (EST) es UTC-05:00. Si desea que el desencadenador de temporizador se active a las 10:00 EST todos los días, puede utilizar la siguiente expresión CRON que tiene en cuenta la zona horaria UTC:

```json
"schedule": "0 0 15 * * *",
``` 

Si lo prefiere, puede agregar una nueva configuración de aplicación para la aplicación de función denominada `WEBSITE_TIME_ZONE` y establecer el valor en **Hora estándar del Este**.  A continuación, se podría usar la siguiente expresión CRON para las 10:00 EST: 

```json
"schedule": "0 0 10 * * *",
``` 


<a name="examples"></a>

## <a name="schedule-examples"></a>Ejemplos de programación
Estos son algunos ejemplos de expresiones CRON que puede utilizar para la propiedad `schedule`. 

Para desencadenar una vez cada cinco minutos:

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

<a name="usage"></a>

## <a name="trigger-usage"></a>Uso del desencadenador
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

<a name="sample"></a>

## <a name="trigger-sample"></a>Ejemplo de desencadenador
Supongamos que el siguiente desencadenador de temporizador se encuentra en la matriz `bindings` de function.json:

```json
{
    "schedule": "0 */5 * * * *",
    "name": "myTimer",
    "type": "timerTrigger",
    "direction": "in"
}
```

Vea el ejemplo específico del lenguaje que lee el objeto de temporizador para comprobar si se retrasa.

* [C#](#triggercsharp)
* [F#](#triggerfsharp)
* [Node.js](#triggernodejs)

<a name="triggercsharp"></a>

### <a name="trigger-sample-in-c"></a>Ejemplo de desencadenador en C# #
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

<a name="triggerfsharp"></a>

### <a name="trigger-sample-in-f"></a>Ejemplo de desencadenador en F# #
```fsharp
let Run(myTimer: TimerInfo, log: TraceWriter ) =
    if (myTimer.IsPastDue) then
        log.Info("F# function is running late.")
    let now = DateTime.Now.ToLongTimeString()
    log.Info(sprintf "F# function executed at %s!" now)
```

<a name="triggernodejs"></a>

### <a name="trigger-sample-in-nodejs"></a>Ejemplo de desencadenador en Node.js
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

## <a name="next-steps"></a>Pasos siguientes
[!INCLUDE [next steps](../../includes/functions-bindings-next-steps.md)]




<!--HONumber=Nov16_HO3-->


