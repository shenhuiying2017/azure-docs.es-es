---
title: "Creación de programaciones complejas y periodicidad avanzada con Programador de Azure"
description: "Creación de programaciones complejas y periodicidad avanzada con Programador de Azure"
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
ms.openlocfilehash: 20c3e3c1cb85308cad47054c2efa87f61cae0f22
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 07/11/2017
---
# <a name="how-to-build-complex-schedules-and-advanced-recurrence-with-azure-scheduler"></a>Creación de programaciones complejas y periodicidad avanzada con Programador de Azure
## <a name="overview"></a>Información general
En el corazón de un trabajo del Programador de Azure se encuentra la *programación*. La programación determina cuándo y cómo el Programador ejecuta el trabajo.

Programador de Azure le permite especificar distintas programaciones periódicas y únicas para un trabajo. Las programaciones *únicas* se desencadenan una vez a una hora especificadas; en efecto, son programaciones *periódicas* que se ejecutan solo una vez. Las programaciones recurrentes se activan en una frecuencia predeterminada.

Con esta flexibilidad, Programador de Azure le permite admitir una amplia variedad de escenarios empresariales:

* Limpieza de datos periódicos; por ejemplo, todos los días hay que eliminar todos los tweets de más de 3 meses.
* Archivado; por ejemplo, cada mes hay que insertar el histórico de facturas en el servicio de copia de seguridad.
* Solicitudes de datos externos; por ejemplo, cada 15 minutos hay que extraer un nuevo informe meteorológico de esquí de NOAA.
* Procesamiento de imágenes; por ejemplo, todos los días laborables, fuera de las horas pico, hay que utilizar la informática en la nube para comprimir las imágenes cargadas durante ese día.

En este artículo se describen ejemplos de trabajos que se pueden crear con Programador de Azure. Se proporcionan los datos JSON que describen cada programación. Si se usa la [API de REST de Scheduler](https://msdn.microsoft.com/library/mt629143.aspx), puede usar este mismo JSON para [crear un trabajo de Azure Scheduler](https://msdn.microsoft.com/library/mt629145.aspx).

## <a name="supported-scenarios"></a>Escenarios admitidos
Los numerosos ejemplos de este tema muestran la gran variedad de escenarios que admite Programador de Azure. En general, estos ejemplos muestran cómo crear programaciones para diversos patrones de comportamiento, incluidos los siguientes:

* Ejecutar una vez en una determinada fecha y hora
* Ejecutar y repetir un número de veces explícitas
* Ejecutar inmediatamente y repetir
* Ejecutar y repetir cada *n* minutos, horas, días, semanas o meses a partir de un período de tiempo determinado
* Ejecutar y repetir con frecuencia semanal o mensual, pero solo en días específicos, en determinados días de la semana o en días específicos del mes
* Ejecutar y repetir varias veces en un período; por ejemplo, el último viernes y lunes de cada mes, o a las 5:15 a.m. y a las 5:15 p.m. todos los días

## <a name="dates-and-datetimes"></a>Fechas y fecha/hora
Las fechas en Programador de Azure siguen la [especificación ISO-8601](http://en.wikipedia.org/wiki/ISO_8601) y solo incluyen la fecha.

Las referencias a fecha/hora en los trabajos de Programador de Azure siguen la [especificación ISO-8601](http://en.wikipedia.org/wiki/ISO_8601) y solo incluyen las partes de fecha y de hora. Una fecha y hora que no especifica un desfase de hora UTC se supone que es una hora UTC.  

## <a name="how-to-use-json-and-rest-api-for-creating-schedules"></a>Uso de JSON y API de REST para crear programaciones
Para crear una programación simple mediante la [API de REST de Azure Scheduler](https://msdn.microsoft.com/library/mt629143), primero [registre su suscripción con un proveedor de recursos](https://msdn.microsoft.com/library/azure/dn790548.aspx) (el nombre del proveedor para Scheduler es *Microsoft.Scheduler*); después, [cree una colección de trabajos](https://msdn.microsoft.com/library/mt629159.aspx) y, finalmente, [cree un trabajo](https://msdn.microsoft.com/library/mt629145.aspx). Cuando se crea un trabajo, puede especificar la programación y periodicidad mediante JSON como en el extracto siguiente:

    {
        "startTime": "2012-08-04T00:00Z", // optional
         …
        "recurrence":                     // optional
        {
            "frequency": "week",     // can be "year" "month" "day" "week" "hour" "minute"
            "interval": 1,                // optional, how often to fire (default to 1)
            "schedule":                   // optional (advanced scheduling specifics)
            {
                "weekDays": ["monday", "wednesday", "friday"],
                "hours": [10, 22]                      
            },
            "count": 10,                  // optional (default to recur infinitely)
            "endTime": "2012-11-04",      // optional (default to recur infinitely)
        },
        …
    }

## <a name="overview-job-schema-basics"></a>Información general: conceptos básicos de esquema de trabajo
En la tabla siguiente se muestra una descripción general de los elementos más importantes relacionados con la periodicidad y la programación de un trabajo:

| **Nombre JSON** | **Descripción** |
|:--- |:--- |
| ***startTime*** |*startTime* es una fecha y hora. En las programaciones simples, *startTime* es la primera repetición y, en las programaciones complejas, el trabajo se iniciará no antes que *startTime*. |
| ***recurrence*** |El objeto *recurrence* especifica las reglas de periodicidad para el trabajo y la periodicidad con la que se ejecutará el trabajo. El objeto recurrence admite los elementos *frequency, interval, endTime, count,* y *schedule*. Si se define *recurrence*, se requiere *frequency*; los otros elementos de *recurrence* son opcionales. |
| ***frequency*** |La cadena *frequency* representa la unidad de frecuencia en la que se repite el trabajo. Los valores admitidos son *"minute", "hour", "day", "week",* o *"month".* |
| ***interval*** |*interval* es un entero positivo e indica el intervalo para el valor de *frequency* que determina la frecuencia con la que se ejecutará el trabajo. Por ejemplo, si *interval* es 3 y *frequency* es "week", el trabajo se repite cada tres semanas. Azure Scheduler admite un valor máximo de *interval* de 18 meses para la frecuencia mensual, 78 semanas para la frecuencia semanal o 548 días para la frecuencia diaria. Para una frecuencia de horas y minutos, el intervalo admitido es 1 < = *interval* < = 1000. |
| ***endTime*** |La cadena *endTime* especifica la fecha y hora pasadas las cuales el trabajo no se debe ejecutar. No es válido tener un valor de *endTime* en el pasado. Si no hay ningún valor *endTime* o se especifica un valor count, el trabajo se ejecuta indefinidamente. Tanto *endTime* como *count* no se pueden incluir en el mismo trabajo. |
| ***count*** |<p>El valor de *count* es un entero positivo (mayor que cero) que especifica el número de veces que debe ejecutarse este trabajo antes de finalizar.</p><p>*count* representa el número de veces que se ejecuta el trabajo antes de que se determine que está completado. Por ejemplo, para un trabajo que se ejecuta diariamente con un valor de *count* de 5 y con lunes como fecha de inicio, el trabajo se completa después de la ejecución el viernes. Si la fecha de inicio está en el pasado, se calcula la primera ejecución desde la hora de creación.</p><p>Si no hay ningún valor *endTime* o se especifica un valor *count*, el trabajo se ejecuta indefinidamente. Tanto *endTime* como *count* no se pueden incluir en el mismo trabajo.</p> |
| ***schedule*** |Un trabajo con una frecuencia especificada modifica su periodicidad según una programación periódica. *schedule* contiene modificaciones basadas en minutos, horas, días de la semana, días del mes y número de semana. |

## <a name="overview-job-schema-defaults-limits-and-examples"></a>Información general: Valores predeterminados del esquema de trabajo, límites y ejemplos
Después de esta información general, tratemos cada uno de estos elementos de detalle.

| **Nombre JSON** | **Tipo de valor** | **¿Necesario?** | **Valor predeterminado** | **Valores válidos** | **Ejemplo** |
|:--- |:--- |:--- |:--- |:--- |:--- |
| ***startTime*** |Cadena |No |None |Fechas-horas ISO-8601 |<code>"startTime" : "2013-01-09T09:30:00-08:00"</code> |
| ***recurrence*** |Objeto |No |None |Objeto de periodicidad |<code>"recurrence" : { "frequency" : "monthly", "interval" : 1 }</code> |
| ***frequency*** |Cadena |Sí |None |"minute", "hour", "day", "week", "month" |<code>"frequency" : "hour"</code> |
| ***interval*** |Number |No |1 |1 a 1000. |<code>"interval":10</code> |
| ***endTime*** |Cadena |No |None |Valor de fecha y hora que representa un periodo de tiempo en el futuro |<code>"endTime" : "2013-02-09T09:30:00-08:00"</code> |
| ***count*** |Number |No |None |>= 1 |<code>"count": 5</code> |
| ***schedule*** |Objeto |No |None |Objeto de programación |<code>"schedule" : { "minute" : [30], "hour" : [8,17] }</code> |

## <a name="deep-dive-starttime"></a>Profundización: *startTime*
La siguiente tabla captura cómo *startTime* controla el modo en que se ejecuta un trabajo.

| **Valor de startTime** | **Sin periodicidad** | **Periodicidad. Sin programación** | **Periodicidad con programación** |
|:--- |:--- |:--- |:--- |
| **Sin hora de inicio** |Ejecutar inmediatamente una vez |Ejecutar inmediatamente una vez. Realizar las ejecuciones posteriores basándose en el cálculo del último tiempo de ejecución. |<p>Ejecutar inmediatamente una vez</p><p>Realizar las sucesivas ejecuciones según la programación de periodicidad</p> |
| **Hora de inicio en el pasado** |Ejecutar inmediatamente una vez |<p>Calcular primero la hora de futuras ejecuciones después de la hora de inicio y ejecutarlas a esa hora</p><p>Realizar las sucesivas ejecuciones basándose en el cálculo de la última hora de ejecución</p><p>Consulte el ejemplo que sigue a esta tabla para obtener una explicación más detallada.</p> |<p>El trabajo se inicia *no antes que* la hora de inicio especificada. La primera repetición se basa en la programación que se calcula a partir de la hora de inicio</p><p>Realizar las sucesivas ejecuciones según la programación de periodicidad</p> |
| **Hora de inicio en el futuro o en el presente** |Ejecutar una vez a la hora de inicio especificada |<p>Ejecutar una vez a la hora de inicio especificada</p><p>Realizar las ejecuciones posteriores basándose en el cálculo del último tiempo de ejecución.</p> |<p>El trabajo se inicia *no antes que* la hora de inicio especificada. La primera repetición se basa en la programación que se calcula a partir de la hora de inicio</p><p>Realizar las sucesivas ejecuciones según la programación de periodicidad</p> |

Veamos un ejemplo de lo que sucede cuando *startTime* se encuentra en el pasado, con *recurrence* pero sin *schedule*.  Suponga que la fecha y hora actual es 08-04-2015 13:00, *startTime* es 2015-04-07 14:00 y *recurrence* es de 2 días (definida con *frequency*: día e *interval*: 2.) Tenga en cuenta que *startTime* se encuentra en el pasado y se produce antes de la hora actual.

En estas condiciones, el *primera ejecución* será 2015-04-09 a las 14:00\. El motor del Programador calcula las repeticiones de la ejecución desde la hora de inicio.  Se descartan las instancias en el pasado. El motor utiliza la instancia siguiente que tiene lugar en el futuro.  En este caso, *startTime* es 2015-04-07 a las 2:00 p.m., así que la siguiente instancia es 2 días a partir de ese momento, que es el 2015-04-09 a las 2:00 p.m.

Tenga en cuenta que la primera ejecución debería ser el mismo incluso si el valor de startTime 2015-04-05 14:00 o 14:00\ 2015-04-01. Después de la primera ejecución, las ejecuciones posteriores se calculan con la programación, por lo que se realizarían el 2015-04-11 a 2:00 p.m., a continuación el 2015-04-13 a las 2:00 p.m., después el+ 2015-04-15 a las 2:00 p.m., etc.

Por último, cuando un trabajo tiene una programación, si no se establecen en la programación las horas y minutos, se asume el valor predeterminado de las horas y minutos de la primera ejecución, respectivamente.

## <a name="deep-dive-schedule"></a>Profundización: *schedule*
Por un lado, *schedule* puede *limitar* el número de ejecuciones de trabajos.  Por ejemplo, si un trabajo con una frecuencia de "mes" tiene un valor de *schedule* que se ejecuta solo el día 31, el trabajo se ejecuta solo en los meses que tienen<sup> 31</sup> días.

Por otro lado, una *programación* también puede *ampliar* el número de ejecuciones de trabajos. Por ejemplo, si un trabajo con una frecuencia "mensual" tiene una *programación* que se ejecuta los días 1 y 2 del mes, <sup></sup><sup></sup>el trabajo se ejecuta en los días primero y segundo del mes en lugar de solo una vez al mes.

Si se especifican varios elementos de programación, el orden de evaluación es de mayor a menor: número de semana, mes, día, día de la semana, hora y minuto.

En la siguiente tabla se describen los elementos de *schedule* con detalle:

| **Nombre JSON** | **Descripción** | **Valores válidos** |
|:--- |:--- |:--- |
| **minutes** |Minutos de la hora en la que se ejecuta el trabajo |<ul><li>Entero o</li><li>Matriz de enteros</li></ul> |
| **hours** |Horas del día en las que se ejecuta el trabajo |<ul><li>Entero o</li><li>Matriz de enteros</li></ul> |
| **weekDays** |Días de la semana en los que se ejecutará el trabajo Solo se puede especificar con una frecuencia semanal. |<ul><li>Lunes, martes, miércoles, jueves, viernes, sábado o domingo</li><li>Matriz de cualquiera de los valores anteriores (tamaño de la matriz máx. 7)</li></ul>*No* distingue mayúsculas de minúsculas |
| **monthlyOccurrences** |Determina los días del mes en los que se ejecutará el trabajo. Solo se puede especificar con una frecuencia mensual. |<ul><li>Matriz de objetos de monthlyOccurence:</li></ul> <pre>{ "day": *day*,<br />  "occurrence": *occurrence*<br />}</pre><p> *day* es el día de la semana en el que se ejecutará el trabajo; por ejemplo, {Sunday} es cada domingo del mes. Necesario.</p><p>El valor de *occurrence* es la repetición del día durante el mes, por ejemplo, {domingo, -1} es el último domingo del mes. Opcional.</p> |
| **monthDays** |Día del mes en el que se ejecutará el trabajo. Solo se puede especificar con una frecuencia mensual. |<ul><li>Cualquier valor < = -1 y > = -31.</li><li>Cualquier valor > = 1 y < = 31.</li><li>Una matriz de valores por encima</li></ul> |

## <a name="examples-recurrence-schedules"></a>Ejemplos: Programaciones de periodicidad
A continuación de muestran unos ejemplos de programaciones de periodicidad, centrándose en el objeto de programación y sus subelementos.

Asume que las programaciones por debajo de todos los *intervalo* está establecido en 1\. Asimismo, uno debe asumir la frecuencia correcta de acuerdo con lo que se encuentra en la *programación* – p. ej., uno no se usan una frecuencia de "day" y tiene una modificación de "días mes para" en la programación. Estas restricciones se han descrito anteriormente.

| **Ejemplo** | **Descripción** |
|:--- |:--- |
| <code>{"hours":[5]}</code> |Se ejecuta a las 5 a.m. cada día. Programador de Azure hace corresponder cada valor en "horas" con cada valor de "minutos", uno por uno, para crear una lista de todas las veces en las que se va a ejecutar el trabajo. |
| <code>{"minutes":[15], "hours":[5]}</code> |Se ejecuta a las 5:15 a.m. cada día. |
| <code>{"minutes":[15], "hours":[5,17]}</code> |Se ejecuta a las 5:15 a.m. y 5:15 p.m. todos los días. |
| <code>{"minutes":[15,45], "hours":[5,17]}</code> |Se ejecuta a las 5:15 a.m., 5:45 a.m., 5:15 p.m. y a las 5:45 p.m. cada día. |
| <code>{"minutes":[0,15,30,45]}</code> |Se ejecuta cada 15 minutos. |
| <code>{hours":[0, 1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11, 12, 13, 14, 15, 16, 17, 18, 19, 20, 21, 22, 23]}</code> |Se ejecuta cada hora. Este trabajo se ejecuta cada hora. Los minutos se controlan mediante *startTime*, si se especifica una fecha y hora de inicio, o si no se especifica, en el momento de creación. Por ejemplo, si la hora de inicio o la hora de creación (lo que se aplique) es 12:25 p.m., el trabajo se ejecutará a las 00:25, 01:25, 02:25, …, 23:25. La programación equivale a tener un trabajo con un valor de *frequency* de "hour", un valor de *interval* de 1 y ningún valor de *schedule*. La diferencia es que esta programación puede usarse con diferentes valores en *frequency* e *interval* para crear también otros trabajos. Por ejemplo, si el valor de *frequency* fuera "month", la programación se ejecutaría solo una vez al mes en lugar de todos los días si *frequency* fuera "day". |
| <code>{minutes:[0]}</code> |Se ejecuta cada hora durante la hora. Esta tarea también se ejecuta cada hora, pero a la hora exacta (por ejemplo, 12 a.m., 1 a.m., 2 a.m., etc.) Esto es equivalente a un trabajo con una frecuencia de "hora", una hora de inicio con cero minutos y a ninguna programación si la frecuencia fuera "día", pero si la frecuencia fuera "semana" o "mes", la programación podría ejecutarse solo un día a la semana o un día del mes, respectivamente. |
| <code>{"minutes":[15]}</code> |Se ejecuta a «y cuarto» cada hora. Se ejecuta cada hora, comenzando en 00:15 a.m., 1:15 a.m., 2:15 a.m., etc. y terminando en 10:15 p.m. y las 11:15 p.m. |
| <code>{"hours":[17], "weekDays":["saturday"]}</code> |Se ejecuta a las 5 p.m. los sábados cada semana. |
| <code>{hours":[17], "weekDays":["monday", "wednesday", "friday"]}</code> |Se ejecuta a las 5 p.m. el lunes, el miércoles y el viernes de cada semana, |
| <code>{"minutes":[15,45], "hours":[17], "weekDays":["monday", "wednesday", "friday"]}</code> |Se ejecuta a las 5:15 p.m. y las 5:45 p.m. el lunes, el miércoles y el viernes cada semana. |
| <code>{"hours":[5,17], "weekDays":["monday", "wednesday", "friday"]}</code> |Se ejecuta a las 5 a.m. y a las 5 p.m. el lunes, el miércoles y el viernes de cada semana. |
| <code>{"minutes":[15,45], "hours":[5,17], "weekDays":["monday", "wednesday", "friday"]}</code> |Se ejecuta a las 5:15 a.m., 5:45 a.m., 5:15 p.m. y las 5:45 p.m. el lunes, el miércoles y el viernes cada semana |
| <code>{"minutes":[0,15,30,45], "weekDays":["monday", "tuesday", "wednesday", "thursday", "friday"]}</code> |Se ejecuta cada 15 minutos los días laborables. |
| <code>{"minutes":[0,15,30,45], "hours": [9, 10, 11, 12, 13, 14, 15, 16] "weekDays":["monday", "tuesday", "wednesday", "thursday", "friday"]}</code> |Se ejecuta cada 15 minutos los días laborables entre las 9 a.m. y las 4:45 p.m. |
| <code>{"weekDays":["sunday"]}</code> |Se ejecuta los domingos a la hora de inicio. |
| <code>{"weekDays":["tuesday", "thursday"]}</code> |Se ejecuta los martes y los jueves a la hora de inicio. |
| <code>{"minutes":[0], "hours":[6], "monthDays":[28]}</code> |Se ejecuta a las 6 a.m. del día 28 de cada mes (suponiendo una frecuencia de mes). |
| <code>{"minutes":[0], "hours":[6], "monthDays":[-1]}</code> |Se ejecuta a las 6 a.m. el último día del mes. Si desea ejecutar un trabajo en el último día del mes, use -1 en lugar de día 28, 29, 30 o 31. |
| <code>{"minutes":[0], "hours":[6], "monthDays":[1,-1]}</code> |Se ejecuta a las 6 a.m. el primer y último día de cada mes. |
| <code>{monthDays":[1,-1]}</code> |Se ejecuta el primer y último día de cada mes a la hora de inicio. |
| <code>{monthDays":[1,14]}</code> |Se ejecuta el primer y decimocuarto día de cada mes a la hora de inicio. |
| <code>{monthDays":[2]}</code> |Se ejecuta el segundo día del mes en la hora de inicio. |
| <code>{"minutes":[0], "hours":[5], "monthlyOccurrences":[{"day":"friday", "occurrence":1}]}</code> |Se ejecuta el primer viernes de cada mes a las 5 a.m. |
| <code>{"monthlyOccurrences":[{"day":"friday", "occurrence":1}]}</code> |: Se ejecuta el primer viernes de cada mes a la hora de inicio. |
| <code>{"monthlyOccurrences":[{"day":"friday", "occurrence":-3}]}</code> |Se ejecuta el tercer viernes desde el final del mes, cada mes, a la hora de inicio. |
| <code>{"minutes":[15], "hours":[5], "monthlyOccurrences":[{"day":"friday", "occurrence":1},{"day":"friday", "occurrence":-1}]}</code> |Se ejecuta el primer y último viernes de cada mes a las 5:15 a.m. |
| <code>{"monthlyOccurrences":[{"day":"friday", "occurrence":1},{"day":"friday", "occurrence":-1}]}</code> |Se ejecuta el primer y último viernes de cada mes a la hora de inicio. |
| <code>{"monthlyOccurrences":[{"day":"friday", "occurrence":5}]}</code> |Se ejecuta el quinto viernes de cada mes a la hora de inicio. Si no hay ningún quinto viernes en un mes, no se ejecuta ya que se ha programado para ejecutarse solo el quinto viernes. Puede considerar usar -1 en lugar de 5 para la repetición si desea ejecutar el trabajo en el último viernes del mes. |
| <code>{"minutes":[0,15,30,45], "monthlyOccurrences":[{"day":"friday", "occurrence":-1}]}</code> |Se ejecuta cada 15 minutos el último viernes del mes. |
| <code>{"minutes":[15,45], "hours":[5,17], "monthlyOccurrences":[{"day":"wednesday", "occurrence":3}]}</code> |Se ejecuta a las 5:15 a.m., 5:45 a.m., 5:15 p., y 5:45 p.m. el tercer miércoles de cada mes |

## <a name="see-also"></a>Otras referencias
 [¿Qué es Programador?](scheduler-intro.md)

 [Conceptos, terminología y jerarquía de entidades de Programador de Azure](scheduler-concepts-terms.md)

 [Introducción al Programador de Azure en el Portal de Azure](scheduler-get-started-portal.md)

 [Planes y facturación en Programador de Azure](scheduler-plans-billing.md)

 [Referencia de API de REST de Programador de Azure](https://msdn.microsoft.com/library/mt629143)

 [Referencia de cmdlets de PowerShell de Programador de Azure](scheduler-powershell-reference.md)

 [Alta disponibilidad y confiabilidad de Programador de Azure](scheduler-high-availability-reliability.md)

 [Límites, valores predeterminados y códigos de error de Programador de Azure](scheduler-limits-defaults-errors.md)

 [Autenticación de salida de Programador de Azure](scheduler-outbound-authentication.md)

