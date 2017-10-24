---
title: "Información sobre las alertas en Azure Log Analytics | Microsoft Docs"
description: "Las alertas de Log Analytics identifican información importante en el repositorio de OMS y pueden avisarle proactivamente de problemas o invocar acciones para intentar corregirlos.  En este artículo se describen los diferentes tipos de reglas de alerta y cómo se definen."
services: log-analytics
documentationcenter: 
author: bwren
manager: jwhit
editor: tysonn
ms.assetid: 6cfd2a46-b6a2-4f79-a67b-08ce488f9a91
ms.service: log-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 07/26/2017
ms.author: bwren
ms.openlocfilehash: e03911d589aaab0d0e80da5d58f14d6df417f4be
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="understanding-alerts-in-log-analytics"></a>Información sobre alertas en Log Analytics

Las alertas de Log Analytics identifican información importante en el repositorio de Log Analytics.  En este artículo se proporcionan detalles sobre cómo funcionan las reglas de alerta en Log Analytics y se describen las diferencias entre los distintos tipos de reglas de alerta.

Para el proceso de creación de reglas de alerta, consulte los siguientes artículos:

- Creación de reglas de alerta mediante [Azure Portal](log-analytics-alerts-creating.md)
- Creación de reglas de alerta mediante una [plantilla de Resource Manager](../operations-management-suite/operations-management-suite-solutions-resources-searches-alerts.md)
- Creación de reglas de alerta mediante la [API de REST](log-analytics-api-alerts.md)


## <a name="alert-rules"></a>Las reglas de alertas

Las alertas se crean mediante reglas de alerta que ejecutan automáticamente búsquedas de registros a intervalos regulares.  Si los resultados de la búsqueda de registros coinciden con determinados criterios, se crea un registro de alertas.  Luego, la regla puede ejecutar automáticamente una o varias acciones para avisarle proactivamente de la alerta o invocar otro proceso.  Diferentes tipos de reglas de alerta usan una lógica distinta para realizar este análisis.

![Alertas de Log Analytics](media/log-analytics-alerts/overview.png)

Las reglas de alerta se definen mediante los siguientes detalles:

- **Búsqueda de registros**.  La consulta que se ejecuta cada vez que se activa la regla de alertas.  Los registros devueltos por esta consulta se usan para determinar si se crea una alerta.
- **Ventana de tiempo**.  Especifica el intervalo de tiempo para la consulta.  La consulta devuelve solo los registros que se crearon dentro de este intervalo de tiempo actual.  Puede ser cualquier valor entre 5 minutos y 24 horas. Por ejemplo, si el período de tiempo se establece en sesenta minutos, y la consulta se ejecuta a las 13:15, se devuelven solo los registros creados entre las 12:15 y las 13:15.
- **Frecuencia**.  Especifica con qué frecuencia se debe ejecutar la consulta. Puede ser cualquier valor entre 5 minutos y 24 horas. Debe ser igual que el período de tiempo o inferior a este valor.  Si el valor es mayor que la ventana de tiempo, se arriesga a que se pierdan registros.<br>Por ejemplo, considere la posibilidad de una ventana de tiempo de 30 minutos y una frecuencia de 60 minutos.  Si la consulta se ejecuta a la 1:00, devolverá registros entre las 12:30 y la 1:00 p. m.  La próxima vez que se ejecute la consulta será a las 2:00 y devolverá los registros comprendidos entre la 1:30 y las 2:00.  Nunca se evaluarán los registros creados entre la 1:00 y la 1:30.
- **Umbral**.  Los resultados de la búsqueda de registros se evalúan para determinar si se debe crear una alerta.  El umbral es diferente para los distintos tipos de reglas de alerta.

Cada regla de alertas de Log Analytics es de uno de los dos tipos posibles.  Cada uno de estos tipos se describe en detalle en las secciones que aparecen a continuación.

- **[Número de resultados](#number-of-results-alert-rules)**. Alerta única creada cuando los registros de números devueltos por la búsqueda de registros superan un número especificado.
- **[Unidades métricas](#metric-measurement-alert-rules)**.  Alerta creada para cada objeto de los resultados de la búsqueda de registros con valores que superan el umbral especificado.

Las diferencias entre los tipos de reglas de alerta son las siguientes.

- La regla de alertas para **número de resultados** creará siempre una alerta única mientras que la regla de alertas para **unidades métricas** creará una alerta para cada objeto que supere el umbral.
- Las reglas de alerta para **número de resultados** crean una alerta cuando se supera el umbral una sola vez. Las reglas de alerta para **unidades métricas** pueden crear una alerta cuando se supera el umbral un número determinado de veces en un intervalo de tiempo determinado.

## <a name="number-of-results-alert-rules"></a>Reglas de alerta para número de resultados
Las reglas de alerta para **número de resultados** crean una única alerta cuando el número de registros devueltos por la consulta de búsqueda supera el umbral especificado.

### <a name="threshold"></a>Umbral
El umbral de una regla de alerta para un **número de resultados** es simplemente mayor o menor que un valor determinado.  Se crea una alerta si el número de registros devueltos por la búsqueda de registros coincide con este criterio.

### <a name="scenarios"></a>Escenarios

#### <a name="events"></a>Eventos
Este tipo de regla de alertas es perfecto para trabajar con eventos como, por ejemplo, registros de eventos de Windows, Syslog y registros personalizados.  Es posible que desee crear una alerta cuando se genere un evento de error concreto o cuando se generen varios eventos de error durante un período de tiempo establecido.

Para generar una alerta en un solo evento, determine que el número de resultados sea mayor que 0 y que la frecuencia y el período de tiempo sean de 5 minutos.  De ese modo, se ejecuta la consulta cada cinco minutos y se busca la aparición de un evento único creado en el tiempo transcurrido desde la última vez que se ejecutó la consulta.  Una frecuencia mayor puede retrasar el tiempo entre la recopilación del evento y la creación de la alerta.

Algunas aplicaciones pueden registrar un error ocasional que no tiene por qué haber generado necesariamente una alerta.  Por ejemplo, la aplicación puede volver a intentar realizar el proceso que creó el evento de error y completarlo correctamente la vez siguiente.  En este caso, es posible que no desee crear la alerta a menos que se creen varios eventos en un período de tiempo determinado.  

En algunos casos, quizá quiera crear una alerta sin que haya un evento.  Por ejemplo, un proceso puede registrar eventos regulares para indicar que está funcionando correctamente.  Si no se registra uno de estos eventos dentro de un período de tiempo determinado, debe crearse una alerta.  En este caso, tendría que establecer el umbral en un valor **less than 1** (menor que 1).

#### <a name="performance-alerts"></a>Alertas de rendimiento
[Los datos de rendimiento](log-analytics-data-sources-performance-counters.md) se almacenan como registros en el repositorio de OMS de manera similar a los eventos.  Si desea que se cree la alerta cuando un contador de rendimiento supere un umbral determinado, ese umbral debe incluirse en la consulta.

Por ejemplo, si quiere emitir una alerta cuando el rendimiento del procesador supere el 90 %, puede utilizar una consulta como la siguiente con el umbral de la regla de alerta **greater than 0** (mayor que 0).

    Type=Perf ObjectName=Processor CounterName="% Processor Time" CounterValue>90

Si quiere emitir una alerta cuando el rendimiento medio del procesador supere el 90 % durante un determinado período de tiempo, puede utilizar una consulta como la siguiente con el [comando measure](log-analytics-search-reference.md#commands) y con el umbral de la regla de alerta **greater than 0** (mayor que 0).

    Type=Perf ObjectName=Processor CounterName="% Processor Time" | measure avg(CounterValue) by Computer | where AggregatedValue>90

>[!NOTE]
> Si el área de trabajo se ha actualizado al [nuevo lenguaje de consulta de Log Analytics](log-analytics-log-search-upgrade.md), las consultas anteriores cambiarían como sigue: `Perf | where ObjectName=="Processor" and CounterName=="% Processor Time" and CounterValue>90`
> `Perf | where ObjectName=="Processor" and CounterName=="% Processor Time" | summarize avg(CounterValue) by Computer | where CounterValue>90`


## <a name="metric-measurement-alert-rules"></a>Reglas de alertas para medición de métricas

>[!NOTE]
> Las reglas de alertas para unidades métricas están actualmente en versión preliminar pública.

Las reglas de alertas para **unidades métricas** crean una alerta para cada objeto de una consulta con un valor que supera un umbral especificado.  Presentan las siguientes diferencias con respecto a las reglas de alerta para **número de resultados**.

#### <a name="log-search"></a>Búsqueda de registros
Aunque puede usar cualquier consulta para una regla de alerta para **número de resultados**, existen requisitos específicos de la consulta para una regla de alerta para unidades métricas.  Debe incluir un [comando Measure](log-analytics-search-reference.md#commands) para agrupar los resultados según un campo concreto. Este comando debe incluir los siguientes elementos.

- **Función de agregado**.  Determina el cálculo que se lleva a cabo y potencialmente un campo numérico para agregar.  Por ejemplo, **count()** devolverá el número de registros de la consulta y **avg(CounterValue)**, el promedio del campo CounterValue durante el intervalo.
- **Campo de grupo**.  Se crea un registro con un valor agregado para cada instancia de este campo y se puede generar una alerta para cada una.  Por ejemplo, si desea generar una alerta para cada equipo, usaría **by Computer** (por Equipo).   
- **Intervalo**.  Define el intervalo de tiempo durante el cual se agregan los datos.  Por ejemplo, si especificó **5minutes** (5 minutos), se crearía un registro para cada instancia del campo de grupo que se agrega a intervalos de 5 minutos en la ventana de tiempo especificada para la alerta.

#### <a name="threshold"></a>Umbral
El umbral de las reglas de alerta para unidades métricas se define por un valor agregado y un número de infracciones.  Si cualquier punto de datos de la búsqueda de registros supera este valor, se considera una infracción.  Si el número de infracciones para cualquier objeto de los resultados supera el valor especificado, se crea una alerta para ese objeto.

#### <a name="example"></a>Ejemplo
Considere la posibilidad de un escenario en el que desearía tener una alerta en caso de que cualquier equipo superara el uso del procesador en un 90 % tres veces en 30 minutos.  Crearía una regla de alerta con los detalles siguientes.  

**Consulta:** Type=Perf ObjectName=Processor CounterName="% Processor Time" | measure avg(CounterValue) by Computer Interval 5minute<br>
**Ventana de tiempo**: 30 minutos<br>
**Frecuencia de la alerta**: 5 minutos<br>
**Valor agregado:** mayor que 90<br>
**Desencadenar alerta según:** total de infracciones mayor que 5<br>

La consulta crearía un valor medio para cada equipo a intervalos de 5 minutos.  Esta consulta se ejecutaría cada 5 minutos para los datos recopilados en los 30 minutos anteriores.  A continuación se muestran datos de ejemplo para tres equipos.

![Resultados de la consulta de ejemplo](media/log-analytics-alerts/metrics-measurement-sample-graph.png)

En este ejemplo, se crearán alertas independientes para srv02 y srv03 porque han incumplido el umbral del 90 % 3 veces en la ventana de tiempo.  Si se cambiara **Desencadenar alerta según:** a **Consecutivo**, entonces se crearía una alerta solo para srv03, ya que incumplió el umbral para 3 ejemplos consecutivos.

## <a name="alert-records"></a>Registros de alerta
Los registros de alerta creados por reglas de alerta en Log Analytics tienen un **Tipo** de **Alerta** y un **SourceSystem** de **OMS**.  Tienen las propiedades de la tabla siguiente.

| Propiedad | Descripción |
|:--- |:--- |
| Tipo |*Alerta* |
| SourceSystem |*OMS* |
| *Object*  | Las [alertas de unidades métricas](#metric-measurement-alert-rules) tendrán una propiedad para el campo de grupo.  Por ejemplo, si la búsqueda de registros se agrupa por Equipo, el registro de alertas tendrá un campo Equipo con el nombre del equipo como valor.
| AlertName |Nombre de la alerta |
| AlertSeverity |Nivel de gravedad de la alerta. |
| LinkToSearchResults |Vincular a la búsqueda de registros de Log Analytics que devuelve los registros de la consulta que creó la alerta. |
| Consultar |Texto de la consulta que se ha ejecutado. |
| QueryExecutionEndTime |Fin del intervalo de tiempo para la consulta. |
| QueryExecutionStartTime |Inicio del intervalo de tiempo para la consulta. |
| ThresholdOperator | Operador usado por la regla de alerta. |
| ThresholdValue | Valor usado por la regla de alerta. |
| TimeGenerated |Fecha y hora en que se creó la alerta. |

Hay otros tipos de registros de alerta creados por la [solución de Administración de alertas](log-analytics-solution-alert-management.md) y las [exportaciones de Power BI](log-analytics-powerbi.md).  Estos tienen todos un **Tipo** de **Alerta** pero se distinguen por sus valores en **SourceSystem**.


## <a name="next-steps"></a>Pasos siguientes
* Instale la [solución Alert Management](log-analytics-solution-alert-management.md) para analizar las alertas creadas en Log Analytics junto con las alertas recopiladas desde System Center Operations Manager.
* Obtenga más información sobre las [búsquedas de registros](log-analytics-log-searches.md) que pueden generar alertas.
* Complete un tutorial para [configurar un webhook](log-analytics-alerts-webhooks.md) con una regla de alerta.  
* Aprenda a escribir [runbooks en Automatización de Azure](https://azure.microsoft.com/documentation/services/automation) para solucionar los problemas identificados por las alertas.
