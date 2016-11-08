---
title: Desencadenador de temporizador de funciones de Azure | Microsoft Docs
description: Descubra cómo utilizar desencadenadores de temporizador en funciones de Azure.
services: functions
documentationcenter: na
author: christopheranderson
manager: erikre
editor: ''
tags: ''
keywords: funciones de azure, funciones, procesamiento de eventos, proceso dinámico, arquitectura sin servidor

ms.service: functions
ms.devlang: multiple
ms.topic: reference
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 08/22/2016
ms.author: chrande; glenga

---
# Desencadenador de temporizador de funciones de Azure
[!INCLUDE [functions-selector-bindings](../../includes/functions-selector-bindings.md)]

Este artículo explica cómo configurar desencadenadores de temporizador en funciones de Azure. El temporizador desencadena las funciones de llamada según una programación, una hora o de forma periódica.

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

## function.json con desencadenador de temporizador
El archivo *function.json* proporciona una expresión de programación. Por ejemplo, la siguiente programación ejecuta la función cada minuto:

```json
{
  "bindings": [
    {
      "schedule": "0 * * * * *",
      "name": "myTimer",
      "type": "timerTrigger",
      "direction": "in"
    }
  ],
  "disabled": false
}
```

El desencadenador de temporizador controla el escalado horizontal de varias instancias de forma automática: solo se ejecutará una única instancia de una función de temporizador determinada en todas las instancias.

## Formato de expresión de programación
La expresión de programación es una [expresión CRON](http://en.wikipedia.org/wiki/Cron#CRON_expression) que incorpora 6 campos: `{second} {minute} {hour} {day} {month} {day of the week}`.

Tenga en cuenta que muchas de las expresiones CRON que puede encontrar en Internet omiten el campo {second}, por lo que si copia uno de ellos tendrá que ajustar el campo adicional.

Estos son algunos otros ejemplos de expresiones de programación.

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

## Ejemplo de código de C# de desencadenador de temporizador
Este ejemplo de código de C# escribe un único registro cada vez que se desencadena la función.

```csharp
public static void Run(TimerInfo myTimer, TraceWriter log)
{
    log.Info($"C# Timer trigger function executed at: {DateTime.Now}");    
}
```

## Pasos siguientes
[!INCLUDE [pasos siguientes](../../includes/functions-bindings-next-steps.md)]

<!---HONumber=AcomDC_0824_2016-->