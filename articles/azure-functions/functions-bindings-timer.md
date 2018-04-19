---
title: Desencadenador de temporizador para Azure Functions
description: Descubra cómo utilizar desencadenadores de temporizador en Azure Functions.
services: functions
documentationcenter: na
author: tdykstra
manager: cfowler
editor: ''
tags: ''
keywords: azure functions, funciones, procesamiento de eventos, proceso dinámico, arquitectura sin servidor
ms.assetid: d2f013d1-f458-42ae-baf8-1810138118ac
ms.service: functions
ms.devlang: multiple
ms.topic: reference
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 02/27/2017
ms.author: tdykstra
ms.custom: ''
ms.openlocfilehash: 2bc2559dc1cf737e018895ffae61d0da0e56fc85
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 04/05/2018
---
# <a name="timer-trigger-for-azure-functions"></a>Desencadenador de temporizador para Azure Functions 

En este artículo se explica cómo usar desencadenadores de temporizador en Azure Functions. Con un desencadenador de temporizador puede ejecutar una función de forma programada. 

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

## <a name="packages"></a>Paquetes

El desencadenador en temporizador se proporciona en el paquete NuGet [Microsoft.Azure.WebJobs.Extensions.EventGrid](http://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions). El código fuente del paquete está en el repositorio [azure-webjobs-sdk-extensions](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions/Extensions/Timers/) de GitHub.

[!INCLUDE [functions-package-auto](../../includes/functions-package-auto.md)]

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
    if(myTimer.IsPastDue)
    {
        log.Info("Timer is running late!");
    }
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

En las [bibliotecas de clases de C#](functions-dotnet-class-library.md), use el atributo [TimerTriggerAttribute](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions/Extensions/Timers/TimerTriggerAttribute.cs).

El constructor del atributo toma una expresión CRON o `TimeSpan`. Puede usar `TimeSpan` solamente si la aplicación de función se ejecuta en un plan de App Service. En el ejemplo siguiente se muestra una expresión CRON:

```csharp
[FunctionName("TimerTriggerCSharp")]
public static void Run([TimerTrigger("0 */5 * * * *")]TimerInfo myTimer, TraceWriter log)
{
    if (myTimer.IsPastDue)
    {
        log.Info("Timer is running late!");
    }
    log.Info($"C# Timer trigger function executed at: {DateTime.Now}");
}
 ```

## <a name="configuration"></a>Configuración

En la siguiente tabla se explican las propiedades de configuración de enlace que se definen en el archivo *function.json* y el atributo `TimerTrigger`.

|Propiedad de function.json | Propiedad de atributo |DESCRIPCIÓN|
|---------|---------|----------------------|
|**type** | N/D | Debe establecerse en "timerTrigger". Esta propiedad se establece automáticamente cuando se crea el desencadenador en Azure Portal.|
|**dirección** | N/D | Debe establecerse en "in". Esta propiedad se establece automáticamente cuando se crea el desencadenador en Azure Portal. |
|**name** | N/D | Nombre de la variable que representa el objeto de temporizador en el código de la función. | 
|**schedule**|**ScheduleExpression**|Una [expresión CRON](#cron-expressions) o un valor [TimeSpan](#timespan). `TimeSpan` solamente se puede usar para una aplicación de función que se ejecuta en un plan de App Service. Puede colocar la expresión de programación en una configuración de aplicación y establecer esta propiedad en el nombre de dicha configuración encapsulado en signos **%**, como en este ejemplo: "%ScheduleAppSetting%". |
|**runOnStartup**|**RunOnStartup**|Si `true`, la función se invoca cuando se inicia el entorno de ejecución. Por ejemplo, el entorno de ejecución se inicia cuando la aplicación de función se reactiva después de estar inactiva por inactividad, cuando la aplicación de función se reinicia debido a cambios de función y cuando la aplicación de función se escala horizontalmente. Por lo tanto, **runOnStartup** rara vez se debe establecer en `true`, ya que hará que el código se ejecute en momentos altamente impredecibles.|
|**useMonitor**|**useMonitor**|Establezca esta propiedad en `true` o `false` para indicar si la programación se debe supervisar. La supervisión de la programación conserva las apariciones de programación para ayudar a garantizar que la programación se mantiene correctamente aunque las instancias de aplicación de función se reinicien. Si no se establece explícitamente, el valor predeterminado es `true` para las programaciones que tienen un intervalo de periodicidad suprior a 1 minuto. Para las programaciones que se desencadenan más de una vez por minuto, el valor predeterminado es `false`.

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="usage"></a>Uso

Cuando se invoca una función de desencadenador de temporizador, se pasa a esta el [objeto de temporizador](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions/Extensions/Timers/TimerInfo.cs). La siguiente función JSON es un ejemplo que representa el objeto de temporizador. 

```json
{
    "Schedule":{
    },
    "ScheduleStatus": {
        "Last":"2016-10-04T10:15:00+00:00",
        "LastUpdated":"2016-10-04T10:16:00+00:00",
        "Next":"2016-10-04T10:20:00+00:00"
    },
    "IsPastDue":false
}
```

La propiedad `IsPastDue` es `true` cuando la invocación de función actual es posterior a la programada. Por ejemplo, un reinicio de aplicación de función podría provocar la ausencia de una invocación.

## <a name="cron-expressions"></a>Expresiones CRON 

Una expresión CRON para el desencadenador de temporizador de Azure Functions incluye seis campos: 

`{second} {minute} {hour} {day} {month} {day-of-week}`

Cada campo puede tener uno de los siguientes tipos de valores:

|type  |Ejemplo  |Cuándo se desencadena  |
|---------|---------|---------|
|Un valor específico |<nobr>"0 5 * * * *"</nobr>|A las hh:05:00, donde hh es cada hora (una vez por hora)|
|Todos los valores (`*`)|<nobr>"0 * 5 * * *"</nobr>|A las 5:mm: 00 cada día, donde mm es cada minuto de la hora (60 veces al día)|
|Un intervalo (operador `-`)|<nobr>"5-7 * * * * *"</nobr>|A las hh:mm:05, hh:mm:06 y hh:mm:07, donde hh:mm es cada minuto de cada hora (tres veces por minuto)|  
|Un conjunto de valores (operador `,`)|<nobr>"5,8,10 * * * * *"</nobr>|A las hh:mm:05, hh:mm:08 y hh:mm:10, donde hh:mm es cada minuto de cada hora (tres veces por minuto)|
|Un valor de intervalo (operador `/`)|<nobr>"0 */5 * * * *"</nobr>|A las hh:05:00, hh:10:00, hh:15:00 y así sucesivamente hasta hh:55:00, donde hh es cada hora (doce veces por hora)|

### <a name="cron-examples"></a>Ejemplos CRON

Estos son algunos ejemplos de expresiones CRON que puede usar para el desencadenador de temporizador en Azure Functions.

|Ejemplo|Cuándo se desencadena  |
|---------|---------|
|"0 */5 * * * *"|Una vez cada cinco minutos|
|"0 0 * * * *"|Una vez al principio de cada hora|
|"0 0 */2 * * *"|Una vez cada dos horas|
|"0 0 9-17 * * *"|Una vez cada hora de 9 a. m. a 5 p. m.|
|"0 30 9 * * *"|A las 9:30 a. m. todos los días|
|"0 30 9 * * 1-5"|A las 9:30 a. m. cada día de la semana|

>[!NOTE]   
>Puede encontrar ejemplos de expresiones CRON en línea, pero muchas de ellas omiten el campo `{second}`. Si copia información de uno de ellos, agregue el campo que falta, `{second}`. Por lo general querrá un cero en ese campo, no un asterisco.

### <a name="cron-time-zones"></a>Zonas horarias CRON

Los números de una expresión CRON hacen referencia a una hora y fecha, no un intervalo de tiempo. Por ejemplo, un 5 en el campo `hour` hace referencia a las 5:00 a. m., no a cada 5 horas.

La zona horaria predeterminada que se usa con las expresiones CRON es la Hora universal coordinada (UTC). Para que la expresión CRON se base en otra zona horaria, cree una configuración de aplicación para la aplicación de función denominada `WEBSITE_TIME_ZONE`. Establezca el valor en el nombre de la zona horaria deseada como se muestra en [Microsoft Time Zone Index](https://technet.microsoft.com/library/cc749073) (Índice de zona horaria de Microsoft). 

Por ejemplo, la *Hora estándar del Este* (EST) es UTC-05:00. Para que el desencadenador de temporizador se dispare a las 10 a.m. (Hora estándar), use la siguiente expresión CRON que representa la zona horaria UTC:

```json
"schedule": "0 0 15 * * *",
``` 

O bien, cree una configuración de aplicación para la aplicación de función denominada `WEBSITE_TIME_ZONE` y establecer el valor en **Hora estándar del Este**.  A continuación, utilice la siguiente expresión CRON: 

```json
"schedule": "0 0 10 * * *",
``` 

## <a name="timespan"></a>timespan

 `TimeSpan` solamente se puede usar para una aplicación de función que se ejecuta en un plan de App Service.

A diferencia de una expresión CRON, un valor `TimeSpan` especifica el intervalo de tiempo entre cada invocación de función. Cuando una función se completa después de ejecutarse más tiempo del intervalo especificado, el temporizador vuelve a invocar inmediatamente a la función.

Expresado como una cadena, el formato `TimeSpan` es `hh:mm:ss` cuando `hh` es menor que 24. Si los dos primeros dígitos son 24 o un valor superior, el formato es `dd:hh:mm`. Estos son algunos ejemplos:

|Ejemplo |Cuándo se desencadena  |
|---------|---------|
|"01:00:00" | Cada hora        |
|"00:01:00"|Cada minuto         |
|"24:00:00" | Cada 24 días        |

## <a name="scale-out"></a>Escalado horizontal

Si una aplicación de función se escala horizontalmente a varias instancias, una sola instancia de una función desencadenada por el temporizador se ejecuta en todas las instancias.

## <a name="function-apps-sharing-storage"></a>Aplicaciones de función que comparten almacenamiento

Si comparte una cuenta de almacenamiento entre varias aplicaciones de función, asegúrese de que cada aplicación de función tiene otro `id` en *host.json*. Puede omitir la propiedad `id` o establecer manualmente el `id` de cada aplicación de función en un valor diferente. El desencadenador de temporizador utiliza un bloqueo de almacenamiento para asegurarse de que solo hay una instancia de temporizador cuando una aplicación de función se escala a varias instancias. Si dos aplicaciones de función comparten el mismo `id` y cada una usa un desencadenador de temporizador, solo se ejecutará un temporizador.

## <a name="retry-behavior"></a>Comportamiento de reintento

A diferencia del desencadenador de cola, el desencadenador de temporizador no vuelve a realizar el intento después de que se produce un error en una función. Cuando se produce un error en una función, se llama nuevamente hasta la próxima vez en la programación.

## <a name="next-steps"></a>Pasos siguientes

> [!div class="nextstepaction"]
> [Ir a una guía de inicio rápido que use un desencadenador de temporizador](functions-create-scheduled-function.md)

> [!div class="nextstepaction"]
> [Más información sobre desencadenadores y enlaces de Azure Functions](functions-triggers-bindings.md)
