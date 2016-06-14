<properties
	pageTitle="Desencadenador de temporizador de funciones de Azure | Microsoft Azure"
	description="Descubra cómo utilizar desencadenadores de temporizador en funciones de Azure."
	services="functions"
	documentationCenter="na"
	authors="christopheranderson"
	manager="erikre"
	editor=""
	tags=""
	keywords="funciones de azure, funciones, procesamiento de eventos, proceso dinámico, arquitectura sin servidor"/>

<tags
	ms.service="functions"
	ms.devlang="multiple"
	ms.topic="reference"
	ms.tgt_pltfrm="multiple"
	ms.workload="na"
	ms.date="05/16/2016"
	ms.author="chrande"/>

# Desencadenador de temporizador de funciones de Azure

Este artículo explica cómo configurar desencadenadores de temporizador en funciones de Azure. El temporizador desencadena las funciones de llamada según una programación, una hora o de forma periódica.

[AZURE.INCLUDE [intro](../../includes/functions-bindings-intro.md)]

## function.json con desencadenador de temporizador

El archivo *function.json* proporciona una expresión de programación y un modificador que indica si la función se debe desencadenar inmediatamente.

```json
{
  "bindings": [
    {
      "schedule": "0 * * * * *",
      "runOnStartup": true,
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

La expresión de programación es una [expresión CRON](http://en.wikipedia.org/wiki/Cron#CRON_expression) que incluye 6 campos: {segundo} {minuto} {hora} {día} {mes} {día de la semana}. Muchos de los documentos de la expresión CRON que puede encontrar en Internet omiten el campo {segundo}, por lo que si copia uno de ellos tendrá que ajustar el campo adicional.

Estos son algunos ejemplos de expresiones de programación.

Para desencadenar una vez cada cinco minutos:

```json
"schedule": "0 */5 * * * *",
"runOnStartup": false,
```

Para desencadenar inmediatamente y, luego, cada dos horas a partir de entonces:

```json
"schedule": "0 0 */2 * * *",
"runOnStartup": true,
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

[AZURE.INCLUDE [pasos siguientes](../../includes/functions-bindings-next-steps.md)]

<!---HONumber=AcomDC_0601_2016-->