---
title: "Creación de programaciones complejas y periodicidad avanzada con Azure Scheduler"
description: Aprenda a crear programaciones complejas y periodicidad avanzada con Azure Scheduler.
services: scheduler
documentationcenter: .NET
author: derek1ee
manager: kevinlam1
editor: 
ms.assetid: 5c124986-9f29-4cbc-ad5a-c667b37fbe5a
ms.service: scheduler
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 08/18/2016
ms.author: deli
ms.openlocfilehash: 4293442e13fc4bae871b1f32a3ed4231d9f32632
ms.sourcegitcommit: c765cbd9c379ed00f1e2394374efa8e1915321b9
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 02/28/2018
---
# <a name="build-complex-schedules-and-advanced-recurrence-with-azure-scheduler"></a>Creación de programaciones complejas y periodicidad avanzada con Azure Scheduler

El núcleo de un trabajo de Azure Scheduler es la programación. La programación determina cuándo y cómo Scheduler ejecuta el trabajo. 

Puede usar Scheduler para establecer una programación única o programaciones periódicas para un trabajo. Las programaciones únicas se desencadenan una sola vez a una hora especificada. Realmente, las programaciones únicas son programaciones periódicas que se ejecutan solo una vez. Las programaciones recurrentes se activan en una frecuencia predeterminada.

Gracias a esta flexibilidad, puede usar Azure Scheduler en una amplia variedad de escenarios empresariales:

* **Limpieza de datos periódica**. Por ejemplo, todos los días, se eliminan todos los tweets que tienen más de tres meses.
* **Archivado**. Por ejemplo, cada mes hay que insertar el historial de facturas en un servicio de copia de seguridad.
* **Solicitudes de datos externos**. Por ejemplo, cada 15 minutos hay que extraer un nuevo informe meteorológico de esquí de NOAA.
* **Procesamiento de imágenes**. Por ejemplo, todos los días laborables, en horas de poca actividad, hay que utilizar la informática en la nube para comprimir las imágenes cargadas durante ese día.

En este artículo se describen ejemplos de trabajos que se pueden crear con Scheduler. Se proporcionan los datos JSON que describen cada programación. Si se usa la [API REST de Scheduler](https://msdn.microsoft.com/library/mt629143.aspx), puede usar este mismo JSON para [crear un trabajo de Scheduler](https://msdn.microsoft.com/library/mt629145.aspx).

## <a name="supported-scenarios"></a>Escenarios admitidos
Los ejemplos de este artículo muestran la gran variedad de escenarios que admite Scheduler. En general, los ejemplos muestran cómo crear programaciones para diversos patrones de comportamiento, incluidos los siguientes:

* Ejecutar una vez en una fecha y hora específicas.
* Ejecutar y repetir un número específico de veces.
* Ejecutar inmediatamente y repetir.
* Ejecutar y repetir cada *n* minutos, horas, días, semanas o meses a partir de un período de tiempo específico.
* Ejecutar y repetir con frecuencia semanal o mensual, pero solo en días específicos de la semana o en días específicos del mes.
* Ejecutar y repetir varias veces en un período. Por ejemplo, en el último viernes y último lunes de cada mes, o a las 5:15 a. m. y a las 5:15 p. m. todos los días.

## <a name="date-and-date-time"></a>Fecha y fecha y hora
Las referencias a fechas en trabajos de Scheduler siguen la [especificación ISO-8601](http://en.wikipedia.org/wiki/ISO_8601) y solo incluyen la fecha.

Las referencias a fecha y hora en los trabajos de Scheduler siguen la [especificación ISO-8601](http://en.wikipedia.org/wiki/ISO_8601) e incluyen la fecha y la hora. Una fecha y hora que no especifica una diferencia horaria con UTC se supone que es una hora UTC.  

## <a name="use-json-and-the-rest-api-to-create-a-schedule"></a>Uso de JSON y la API REST para crear una programación
Para crear una programación básica mediante la [API REST de Scheduler](https://msdn.microsoft.com/library/mt629143), primero [registre la suscripción con un proveedor de recursos](https://msdn.microsoft.com/library/azure/dn790548.aspx). El nombre del proveedor para Scheduler es **Microsoft.Scheduler**. Después, [cree una colección de trabajos](https://msdn.microsoft.com/library/mt629159.aspx). Por último, [cree un trabajo](https://msdn.microsoft.com/library/mt629145.aspx). 

Cuando se crea un trabajo, puede especificar la programación y la periodicidad mediante JSON como en el extracto siguiente:

    {
        "startTime": "2012-08-04T00:00Z", // Optional
         …
        "recurrence":                     // Optional
        {
            "frequency": "week",     // Can be "year", "month", "day", "week", "hour", or "minute"
            "interval": 1,                // How often to fire
            "schedule":                   // Optional (advanced scheduling specifics)
            {
                "weekDays": ["monday", "wednesday", "friday"],
                "hours": [10, 22]                      
            },
            "count": 10,                  // Optional (default to recur infinitely)
            "endTime": "2012-11-04",      // Optional (default to recur infinitely)
        },
        …
    }

## <a name="job-schema-basics"></a>Conceptos básicos de esquema de trabajo
En la tabla siguiente se muestra una descripción general de los elementos más importantes que puede usar para establecer la periodicidad y la programación de un trabajo:

| Nombre JSON | DESCRIPCIÓN |
|:--- |:--- |
| **startTime** |Valor de fecha y hora. Para las programaciones básicas, **startTime** es la primera aparición. Para las programaciones complejas, el trabajo no se inicia antes de **startTime**. |
| **recurrence** |Especifica las reglas de periodicidad del trabajo y la periodicidad con la que se ejecuta este. El objeto recurrence admite los elementos siguientes: **frequency**, **interval**, **endTime**, **count** y **schedule**. Si se define **recurrence**, **frecuency** es necesario. Otros elementos de **recurrence** son opcionales. |
| **frequency** |Una cadena que representa la unidad de frecuencia en la que se repite el trabajo. Los valores admitidos son "minute", "hour", "day", "week" y "month". |
| **interval** |Un número entero positivo. **interval** indica el intervalo del valor **frequency** que determina la frecuencia con la que se ejecuta el trabajo. Por ejemplo, si **interval** es 3 y **frequency** es "week", el trabajo se repite cada tres semanas.<br /><br />Scheduler admite un valor máximo de **interval** de 18 para la frecuencia mensual, 78 para la frecuencia semanal y 548 para la frecuencia diaria. Para una frecuencia de horas y minutos, el intervalo admitido es 1 < = **interval** < = 1000. |
| **endTime** |Una cadena que especifica la fecha y hora después de la cual no se ejecuta el trabajo. Puede establecer un valor para **endTime** que se encuentre en el pasado. Si no se especifica ningún valor para **endTime** y **count**, el trabajo se ejecuta indefinidamente. No puede incluir **endTime** y **count** en el mismo trabajo. |
| **count** |Es un entero positivo (mayor que cero) que especifica el número de veces que debe ejecutarse este trabajo antes de finalizar.<br /><br />**count** representa el número de veces que se ejecuta el trabajo antes de que se determine que está completado. Por ejemplo, para un trabajo que se ejecuta diariamente con un valor de **count** de 5 y con lunes como fecha de inicio, el trabajo se completa después de la ejecución el viernes. Si la fecha de inicio está en el pasado, se calcula la primera ejecución desde la hora de creación.<br /><br />Si no hay ningún valor **endTime** o se especifica un valor **count**, el trabajo se ejecuta indefinidamente. No puede incluir **endTime** y **count** en el mismo trabajo. |
| **schedule** |Un trabajo con una frecuencia especificada modifica su periodicidad según una programación periódica. Un valor **schedule** contiene modificaciones basadas en minutos, horas, días de la semana, días del mes y número de semana. |

## <a name="job-schema-defaults-limits-and-examples"></a>Valores predeterminados del esquema, límites y ejemplos
Más adelante en el artículo, se describen cada uno de los siguientes elementos en detalle:

| Nombre JSON | Tipo de valor | ¿Necesario? | Valor predeterminado | Valores válidos | Ejemplo |
|:--- |:--- |:--- |:--- |:--- |:--- |
| **startTime** |string |Sin  |None |Fechas y horas ISO-8601 |`"startTime" : "2013-01-09T09:30:00-08:00"` |
| **recurrence** |objeto |Sin  |None |El objeto de periodicidad |`"recurrence" : { "frequency" : "monthly", "interval" : 1 }` |
| **frequency** |string |Sí |None |"minute", "hour", "day", "week", "month" |`"frequency" : "hour"` |
| **interval** |número |Sí |None |1 a 1000 |`"interval":10` |
| **endTime** |string |Sin  |None |Valor de fecha y hora que representa un período de tiempo en el futuro |`"endTime" : "2013-02-09T09:30:00-08:00"` |
| **count** |número |Sin  |None |>= 1 |`"count": 5` |
| **schedule** |objeto |Sin  |None |El objeto de programación |`"schedule" : { "minute" : [30], "hour" : [8,17] }` |

## <a name="deep-dive-starttime"></a>Profundización: startTime
La tabla siguiente describe cómo **startTime** controla la forma en la que se ejecuta un trabajo:

| Valor de startTime | Sin periodicidad | Periodicidad, sin programación | Periodicidad con programación |
|:--- |:--- |:--- |:--- |
| **Sin hora de inicio** |Ejecutar inmediatamente una vez. |Ejecutar inmediatamente una vez. Realiza ejecuciones posteriores calculadas desde la última hora de ejecución. |Ejecutar inmediatamente una vez.<br /><br />Realiza las sucesivas ejecuciones según una programación de periodicidad. |
| **Hora de inicio en el pasado** |Ejecutar inmediatamente una vez. |Calcule primero la hora de futuras ejecuciones después de la hora de inicio y ejecútelas a esa hora.<br /><br />Realiza ejecuciones posteriores calculadas desde la última hora de ejecución. <br /><br />Para más información, consulte el ejemplo que sigue a esta tabla. |El trabajo se inicia *no antes que* la hora de inicio especificada. La primera repetición se basa en la programación que se calcula a partir de la hora de inicio.<br /><br />Realiza las sucesivas ejecuciones según una programación de periodicidad. |
| **Hora de inicio en el futuro o la hora actual** |Se ejecuta una vez a la hora de inicio especificada. |Se ejecuta una vez a la hora de inicio especificada.<br /><br />Realiza ejecuciones posteriores calculadas desde la última hora de ejecución.|El trabajo se inicia *no antes que* la hora de inicio especificada. La primera aparición se basa en la programación que se calcula a partir de la hora de inicio.<br /><br />Realiza las sucesivas ejecuciones según una programación de periodicidad. |

Veamos un ejemplo de lo que sucede cuando **startTime** se encuentra en el pasado, con periodicidad, pero sin programación.  Suponga que la fecha y hora actual es 08-04-2015 13:00, **startTime** es 2015-04-07 14:00 y **recurrence** es de dos días (definida con **frequency**: día e **interval**: 2.) Tenga en cuenta que **startTime** se encuentra en el pasado y se produce antes de la hora actual.

En estas condiciones, la primera ejecución será el 2015-04-09 a las 14:00. El motor de Scheduler calcula las repeticiones de la ejecución desde la hora de inicio. Se descartan las instancias en el pasado. El motor utiliza la instancia siguiente que tiene lugar en el futuro. En este caso, **startTime** es 07-04-2015 a las 2:00 p. m., así que la siguiente instancia es dos días a partir de ese momento, es decir, el 09-04-2015 a las 2:00 p. m.

Tenga en cuenta que la primera ejecución debería ser la misma incluso si **startTime** es 05-04-2015 14:00 o 01-04-2015 14:00\. Después de la primera ejecución, las ejecuciones posteriores se calculan mediante la programación. Se producirán el 11-04-2015 a las 2:00 p. m., después el 13-04-2015 a las 2:00 p. m., después el 15-04-2015 a las 2:00 p. m. y así sucesivamente.

Por último, cuando un trabajo tiene una programación, si no se establecen en la programación las horas y minutos, se asume el valor predeterminado de las horas y minutos de la primera ejecución, respectivamente.

## <a name="deep-dive-schedule"></a>Profundización: schedule
Puede usar **schedule** para *limitar* el número de ejecuciones de trabajos. Por ejemplo, si un trabajo tiene un valor de **frequency** de "mes" y tiene una programación que solo se ejecuta el día 31, el trabajo solo se ejecutará en los meses que tengan 31 días.

También puede usar **schedule** para *expandir* el número de ejecuciones de trabajos. Por ejemplo, si un trabajo con un valor de **frequency** de "mes" tiene una programación que se ejecuta los días 1 y 2 del mes, el trabajo se ejecuta en los días primero y segundo del mes en lugar de solo una vez al mes.

Si especifica varios elementos de programación, el orden de evaluación es de mayor a menor: número de semana, mes, día, día de la semana, hora y minuto.

En la siguiente tabla se describen los elementos de schedule con detalle:

| Nombre JSON | DESCRIPCIÓN | Valores válidos |
|:--- |:--- |:--- |
| **minutes** |Minutos de la hora en la que se ejecuta el trabajo. |Una matriz de enteros. |
| **hours** |Horas del día en las que se ejecuta el trabajo. |Una matriz de enteros. |
| **weekDays** |Días de la semana en los que se ejecuta el trabajo. Solo se puede especificar con una frecuencia semanal. |Una matriz de cualquiera de los valores siguientes (el tamaño máximo de la matriz es 7):<br />- "Monday"<br />- "Tuesday"<br />- "Wednesday"<br />- "Thursday"<br />- "Friday"<br />- "Saturday"<br />- "Sunday"<br /><br />No distingue mayúsculas de minúsculas. |
| **monthlyOccurrences** |Determina los días del mes en los que se ejecutará el trabajo. Solo se puede especificar con una frecuencia mensual. |Una matriz de objetos de **monthlyOccurrences**:<br /> `{ "day": day, "occurrence": occurrence}`<br /><br /> **day** es el día de la semana en el que se ejecuta el trabajo. Por ejemplo, *{Sunday}* es todos los domingos del mes. Necesario.<br /><br />El valor de **occurrence** es la repetición del elemento day durante el mes. Por ejemplo, *{Sunday, -1}* es el último domingo del mes. Opcional. |
| **monthDays** |Día del mes en el que se ejecuta el trabajo. Solo se puede especificar con una frecuencia mensual. |Una matriz de los valores siguientes:<br />- Cualquier valor <= -1 y >= -31<br />- Cualquier valor >= 1 y <= 31|

## <a name="examples-recurrence-schedules"></a>Ejemplos: programaciones de periodicidad
Los ejemplos siguientes muestran varias programaciones de periodicidad. Los ejemplos se centran en el objeto de programación y en sus subelementos.

Estas programaciones asumen que **interval** está establecido en 1\.. Los ejemplos también suponen los valores correctos de **frequency** para los valores de **schedule**. Por ejemplo, no puede tener un valor de **frequency** de "day" y tener también una modificación de **monthDays** en el objeto **schedule**. Estas restricciones se han descrito anteriormente en el artículo.

| Ejemplo | DESCRIPCIÓN |
|:--- |:--- |
| `{"hours":[5]}` |Se ejecuta a las 5 a. m. todos los días.<br /><br />Scheduler hace corresponder cada valor en "horas" con cada valor de "minutos", uno por uno, para crear una lista de todas las veces en las que se ejecuta el trabajo. |
| `{"minutes":[15], "hours":[5]}` |Se ejecuta a las 5:15 a. m. todos los días. |
| `{"minutes":[15], "hours":[5,17]}` |Se ejecuta a las 5:15 a. m. y 5:15 p. m. todos los días. |
| `{"minutes":[15,45], "hours":[5,17]}` |Se ejecuta a las 5:15 a. m., 5:45 a. m., 5:15 p. m. y 5:45 p. m. todos los días. |
| `{"minutes":[0,15,30,45]}` |Se ejecuta cada 15 minutos. |
| `{hours":[0, 1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11, 12, 13, 14, 15, 16, 17, 18, 19, 20, 21, 22, 23]}` |Se ejecuta cada hora.<br /><br />Este trabajo se ejecuta cada hora. El minuto viene controlado por el valor **startTime**, si se especifica. Si no se especifica ningún valor **startTime**, el minuto lo controla la hora de creación. Por ejemplo, si la hora de inicio o la hora de creación (lo que se aplique) es 12:25 p. m., el trabajo se ejecutará a las 00:25, 01:25, 02:25, …, 23:25.<br /><br />La programación equivale a un trabajo con un valor de **frequency** de "hora", un valor de **interval** de 1 y ningún valor de **schedule**. La diferencia es que esta programación puede usarse con diferentes valores en **frequency** e **interval** para crear también otros trabajos. Por ejemplo, si el valor de **frequency** fuera “mes”, la programación se ejecutaría solo una vez al mes en lugar de todos los días (como sucedería si **frequency** fuera “día”). |
| `{minutes:[0]}` |Se ejecuta cada hora durante la hora.<br /><br />Este trabajo también se ejecuta cada hora, pero a la hora exacta (por ejemplo, 12 a. m., 1 a. m., 2 a. m., etc.) Esto es equivalente a un trabajo con un valor de **frecuency** de "hora", un valor de **startTime** de cero minutos y ningún valor de **schedule**, si la frecuencia es "día". No obstante, si el valor de **frequency** es "semana" o "mes", la programación se ejecuta únicamente un día a la semana o un día al mes, respectivamente. |
| `{"minutes":[15]}` |Se ejecuta a "y cuarto" cada hora.<br /><br />Se ejecuta cada hora a partir de las 00:15 a. m., 1:15 a. m., 2:15 a. m., y así sucesivamente. Finaliza a las 11:15 p. m. |
| `{"hours":[17], "weekDays":["saturday"]}` |Se ejecuta a las 5 p. m. los sábados de cada semana. |
| `{hours":[17], "weekDays":["monday", "wednesday", "friday"]}` |Se ejecuta a las 5 p. m. los lunes, miércoles y viernes de cada semana. |
| `{"minutes":[15,45], "hours":[17], "weekDays":["monday", "wednesday", "friday"]}` |Se ejecuta a las 5:15 p. m. y 5:45 p. m. los lunes, miércoles y viernes de cada semana. |
| `{"hours":[5,17], "weekDays":["monday", "wednesday", "friday"]}` |Se ejecuta a las 5 a. m. y a las 5 p. m. los lunes, miércoles y viernes de cada semana. |
| `{"minutes":[15,45], "hours":[5,17], "weekDays":["monday", "wednesday", "friday"]}` |Se ejecuta a las 5:15 a. m., 5:45 a. m., 5:15 p. m. y 5:45 p. m. los lunes, miércoles y viernes de cada semana. |
| `{"minutes":[0,15,30,45], "weekDays":["monday", "tuesday", "wednesday", "thursday", "friday"]}` |Se ejecuta cada 15 minutos los días laborables. |
| `{"minutes":[0,15,30,45], "hours": [9, 10, 11, 12, 13, 14, 15, 16] "weekDays":["monday", "tuesday", "wednesday", "thursday", "friday"]}` |Se ejecuta cada 15 minutos los días laborables entre las 9 a. m. y las 4:45 p.m. |
| `{"weekDays":["sunday"]}` |Se ejecuta los domingos a la hora de inicio. |
| `{"weekDays":["tuesday", "thursday"]}` |Se ejecuta los martes y los jueves a la hora de inicio. |
| `{"minutes":[0], "hours":[6], "monthDays":[28]}` |Se ejecuta a las 6 a. m. del día 28 de cada mes (suponiendo un valor de **frequency** de "mes"). |
| `{"minutes":[0], "hours":[6], "monthDays":[-1]}` |Se ejecuta a las 6 a. m. el último día del mes.<br /><br />Si desea ejecutar un trabajo en el último día del mes, use -1 en lugar de día 28, 29, 30 o 31. |
| `{"minutes":[0], "hours":[6], "monthDays":[1,-1]}` |Se ejecuta a las 6 a. m. el primer y el último día del mes. |
| `{monthDays":[1,-1]}` |Se ejecuta el primer y último día de cada mes a la hora de inicio. |
| `{monthDays":[1,14]}` |Se ejecuta el primer y decimocuarto día de cada mes a la hora de inicio. |
| `{monthDays":[2]}` |Se ejecuta el segundo día del mes a la hora de inicio. |
| `{"minutes":[0], "hours":[5], "monthlyOccurrences":[{"day":"friday", "occurrence":1}]}` |Se ejecuta el primer viernes de cada mes a las 5 a. m. |
| `{"monthlyOccurrences":[{"day":"friday", "occurrence":1}]}` |Se ejecuta el primer viernes de cada mes a la hora de inicio. |
| `{"monthlyOccurrences":[{"day":"friday", "occurrence":-3}]}` |Se ejecuta el tercer viernes desde el final del mes, todos los meses, a la hora de inicio. |
| `{"minutes":[15], "hours":[5], "monthlyOccurrences":[{"day":"friday", "occurrence":1},{"day":"friday", "occurrence":-1}]}` |Se ejecuta el primer viernes de cada mes a las 5:15 a. m. |
| `{"monthlyOccurrences":[{"day":"friday", "occurrence":1},{"day":"friday", "occurrence":-1}]}` |Se ejecuta el primer y último viernes de cada mes a la hora de inicio. |
| `{"monthlyOccurrences":[{"day":"friday", "occurrence":5}]}` |Se ejecuta el quinto viernes de cada mes a la hora de inicio.<br /><br />Si no existe quinto viernes en un mes, el trabajo no se ejecuta. Puede considerar usar -1 en lugar de 5 para la repetición si desea ejecutar el trabajo en el último viernes del mes. |
| `{"minutes":[0,15,30,45], "monthlyOccurrences":[{"day":"friday", "occurrence":-1}]}` |Se ejecuta cada 15 minutos el último viernes del mes. |
| `{"minutes":[15,45], "hours":[5,17], "monthlyOccurrences":[{"day":"wednesday", "occurrence":3}]}` |Se ejecuta a las 5:15 a. m., 5:45 a. m., 5:15 a. m. y las 5:45 a. m. el tercer miércoles de cada mes. |

## <a name="see-also"></a>Otras referencias

- [¿Qué es Scheduler?](scheduler-intro.md)
- [Conceptos, terminología y jerarquía de entidades de Azure Scheduler](scheduler-concepts-terms.md)
- [Introducción a Azure Scheduler en Azure Portal](scheduler-get-started-portal.md)
- [Planes y facturación en Azure Scheduler](scheduler-plans-billing.md)
- [Referencia de API de REST de Azure Scheduler](https://msdn.microsoft.com/library/mt629143)
- [Referencia de cmdlets de PowerShell de Azure Scheduler](scheduler-powershell-reference.md)
- [Alta disponibilidad y confiabilidad de Azure Scheduler](scheduler-high-availability-reliability.md)
- [Límites, valores predeterminados y códigos de error de Azure Scheduler](scheduler-limits-defaults-errors.md)
- [Autenticación de salida de Azure Scheduler](scheduler-outbound-authentication.md)

