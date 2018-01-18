---
title: "Información sobre las alertas en Azure Log Analytics | Microsoft Docs"
description: "Las alertas de Log Analytics identifican información importante en el repositorio de OMS y pueden avisarle proactivamente de problemas o invocar acciones para intentar corregirlos.  En este artículo se describen los diferentes tipos de reglas de alerta y cómo se definen."
services: log-analytics
documentationcenter: 
author: bwren
manager: carmonm
editor: tysonn
ms.assetid: 6cfd2a46-b6a2-4f79-a67b-08ce488f9a91
ms.service: log-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/05/2018
ms.author: bwren
ms.openlocfilehash: 07e8312d5e113eeb9016dcc832b1cf66f8001c5f
ms.sourcegitcommit: 719dd33d18cc25c719572cd67e4e6bce29b1d6e7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/08/2018
---
# <a name="understanding-alerts-in-log-analytics"></a>Información sobre alertas en Log Analytics

Las alertas de Log Analytics identifican información importante en el repositorio de Log Analytics.  En este artículo se describen algunas de las decisiones de diseño que se deben tomar en función de la frecuencia de recopilación de los datos que se están consultando, de los retrasos aleatorios con la ingesta de datos posiblemente por la latencia de red o la capacidad de procesamiento, y de la confirmación de los datos en el repositorio de Log Analytics.  También se proporcionan detalles sobre cómo funcionan las reglas de alerta en Log Analytics y se describen las diferencias entre los distintos tipos de reglas de alerta.

Para el proceso de creación de reglas de alerta, consulte los siguientes artículos:

- Creación de reglas de alerta mediante [Azure Portal](log-analytics-alerts-creating.md)
- Creación de reglas de alerta mediante una [plantilla de Resource Manager](../operations-management-suite/operations-management-suite-solutions-resources-searches-alerts.md)
- Creación de reglas de alerta mediante la [API de REST](log-analytics-api-alerts.md)

## <a name="considerations"></a>Consideraciones

Hay disponibles detalles sobre la frecuencia de recopilación de datos para varias soluciones y el tipo de datos en [Detalles de la recopilación de datos](log-analytics-add-solutions.md#data-collection-details) del artículo de información general sobre soluciones. Como se indica en este artículo, la frecuencia de recopilación puede ser tan poco frecuente como una vez cada siete días hasta *según notificación*. Es importante entender y tener en cuenta la frecuencia de recopilación de datos antes de configurar una alerta. 

- La frecuencia de recopilación determina la frecuencia con la que envía datos el agente OMS de las máquinas a Log Analytics. Por ejemplo, si la frecuencia de recopilación es de 10 minutos y no hay ningún retraso más en el sistema, las marcas de tiempo de los datos transmitidos pueden ser cualquier valor entre 0 y 10 minutos antes de que se agreguen al repositorio y se puedan buscar en Log Analytics.

- Antes de que se pueda desencadenar una alerta, los datos deben escribirse en el repositorio para que estén disponible al realizar una consulta. Debido a la latencia descrita anteriormente, la frecuencia de recopilación no es lo mismo que el tiempo en que los datos están disponibles para las consultas. Por ejemplo, mientras que los datos se pueden recopilar exactamente cada 10 minutos, los datos estarán disponibles en el repositorio de datos a intervalos irregulares. Hipotéticamente, los datos recopilados a intervalos de 0, 10 y 20 minutos pueden estar disponibles para la búsqueda en 25, 28 y 35 minutos respectivamente o en cualquier otro intervalo irregular influido por la latencia de ingesta. El peor de los casos para estos retrasos se documenta en el [Acuerdo de Nivel de Servicio de Log Analytics](https://azure.microsoft.com/support/legal/sla/log-analytics/v1_1), que no incluye retrasos introducidos por la frecuencia de recopilación o la latencia de red entre el equipo y el servicio Log Analytics.


## <a name="alert-rules"></a>Reglas de alertas

Las alertas se crean mediante reglas de alerta que ejecutan automáticamente búsquedas de registros a intervalos regulares.  Si los resultados de la búsqueda de registros coinciden con determinados criterios, se crea un registro de alertas.  Luego, la regla puede ejecutar automáticamente una o varias acciones para avisarle proactivamente de la alerta o invocar otro proceso.  Diferentes tipos de reglas de alerta usan una lógica distinta para realizar este análisis.

![Alertas de Log Analytics](media/log-analytics-alerts/overview.png)

Dado que hay una latencia prevista con la ingesta de datos de registro, el tiempo absoluto entre la indexación de datos y el tiempo en el que están disponibles para la búsqueda puede ser imprevisible.  Se debe tener en cuenta la disponibilidad de los datos recopilados casi en tiempo real al definir reglas de alerta.    

No hay un equilibrio entre la confiabilidad de las alertas y la capacidad de respuesta de las mismas. Puede optar por configurar parámetros de alerta para minimizar las falsas alertas y las alertas que faltan, o puede elegir parámetros de alerta para responder rápidamente a las condiciones que se están supervisando pero a veces esto puede generar falsas alertas falsa o alertas que faltan.

Las reglas de alerta se definen mediante los siguientes detalles:

- **Búsqueda de registros**.  La consulta que se ejecuta cada vez que se activa la regla de alertas.  Los registros devueltos por esta consulta se usan para determinar si se crea una alerta.
- **Ventana de tiempo**.  Especifica el intervalo de tiempo para la consulta.  La consulta devuelve solo los registros que se crearon dentro de este intervalo de tiempo actual.  Puede ser cualquier valor entre 5 minutos y 24 horas. El intervalo tiene que ser lo suficientemente ancho como para dar cabida a retrasos razonables en la ingesta. La ventana de tiempo debe ser dos veces la duración del retraso más largo que desee poder controlar.<br> Por ejemplo, si desea que las alertas sean confiables para retrasos de 30 minutos, el intervalo tiene que ser de una hora.  

    Hay dos síntomas que puede experimentar si el intervalo de tiempo es demasiado pequeño.

    - **Alertas que faltan**. Se supone que el retraso de ingesta es a veces de 60 minutos pero la mayoría de las veces es de quince minutos.  Si la ventana de tiempo se establece en 30 minutos, se perderá una alerta cuando el retraso sea de 60 minutos porque los datos no estarán disponibles para la búsqueda al ejecutar la consulta de alerta. 
   
        >[!NOTE]
        >Resulta imposible intentar diagnosticar por qué se pierde la alerta. Por ejemplo, en el caso anterior, los datos se escriben en el repositorio 60 minutos después de que se haya ejecutado la consulta de alerta. Si al día siguiente se detecta que falta una alerta y la consulta se ejecuta en el intervalo de tiempo correcto, los criterios de búsqueda de registros coincidirían con el resultado. Parecería que se hubiera desencadenado la alerta. En realidad, la alerta no se desencadenó porque los datos aún no estaban disponibles cuando se ejecutó la consulta de alerta. 
        >
 
    - **Falsas alertas**. A veces, las alertas están diseñadas para identificar la ausencia de eventos. Un ejemplo de esto es detectar cuándo está una máquina virtual fuera de línea mediante la búsqueda de latidos que faltan. Tal como se ha indicado, si el latido no está disponible para la búsqueda dentro de la ventana de tiempo de la alerta, se generará una alerta porque todavía no se podían buscar los datos de latido y, por lo tanto, faltaban. Este es el mismo resultado que si la máquina virtual estuviera legítimamente fuera de línea y no generara ningún dato de latido. La ejecución de la consulta al día siguiente en la ventana de tiempo correcta mostrará que había latidos y que se produjo un error en la alerta. En realidad, los latidos no estaban aún disponibles para la búsqueda porque la ventana de tiempo de la alerta se había configurado demasiado pequeña.  

- **Frecuencia**.  Especifica la frecuencia con la que se ejecuta la consulta y se puede utilizar para dar mayor capacidad de respuesta a las alertas en los casos habituales. El valor puede estar comprendido entre 5 minutos y 24 horas, y tiene que ser igual o menor que la ventana de tiempo de la alerta.  Si el valor es mayor que la ventana de tiempo, se arriesga a que se pierdan registros.<br>Si el objetivo es confiable para retrasos de hasta 30 minutos y el retraso normal es de 10 minutos, la ventana de tiempo debe ser de una hora y el valor de frecuencia debe ser de 10 minutos. Esto podría desencadenar una alerta con datos que tengan un retraso de ingesta de 10 minutos entre 10 y 20 minutos después de que se generara la alerta de datos.<br>Para evitar crear varias alertas para los mismos datos porque la ventana de tiempo es demasiado amplia, se puede utilizar la opción [Suprimir alertas](log-analytics-tutorial-response.md#create-alerts) para suprimir alertas al menos durante la ventana de tiempo.
  
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
- **Intervalo**.  Define el intervalo de tiempo durante el cual se agregan los datos.  Por ejemplo, si especificó **5 minutes** (5 minutos), se crearía un registro para cada instancia del campo de grupo que se agrega a intervalos de 5 minutos en la ventana de tiempo especificada para la alerta.

#### <a name="threshold"></a>Umbral
El umbral de las reglas de alerta para unidades métricas se define por un valor agregado y un número de infracciones.  Si cualquier punto de datos de la búsqueda de registros supera este valor, se considera una infracción.  Si el número de infracciones para cualquier objeto de los resultados supera el valor especificado, se crea una alerta para ese objeto.

#### <a name="example"></a>Ejemplo
Considere la posibilidad de un escenario en el que desearía tener una alerta en caso de que cualquier equipo superara el uso del procesador en un 90 % tres veces en 30 minutos.  Crearía una regla de alerta con los detalles siguientes.  

**Consulta:** Type=Perf ObjectName=Processor CounterName="% Processor Time" | measure avg(CounterValue) by Computer Interval 5minute<br>
**Ventana de tiempo:** 30 minutos<br>
**Frecuencia de la alerta:** 5 minutos<br>
**Valor agregado**: mayor que 90<br>
**Desencadenar alerta según:** total de infracciones mayor que 5<br>

La consulta crearía un valor medio para cada equipo a intervalos de 5 minutos.  Esta consulta se ejecutaría cada 5 minutos para los datos recopilados en los 30 minutos anteriores.  A continuación se muestran datos de ejemplo para tres equipos.

![Resultados de la consulta de ejemplo](media/log-analytics-alerts/metrics-measurement-sample-graph.png)

En este ejemplo, se crearán alertas independientes para srv02 y srv03 porque han incumplido el umbral del 90 % 3 veces en la ventana de tiempo.  Si se cambiara **Desencadenar alerta según:** a **Consecutivo**, entonces se crearía una alerta solo para srv03, ya que incumplió el umbral para 3 ejemplos consecutivos.

## <a name="alert-records"></a>Registros de alerta
Los registros de alerta creados por reglas de alerta en Log Analytics tienen un **Tipo** de **Alerta** y un **SourceSystem** de **OMS**.  Tienen las propiedades de la tabla siguiente.

| Propiedad | DESCRIPCIÓN |
|:--- |:--- |
| type |*Alerta* |
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


## <a name="next-steps"></a>pasos siguientes
* Instale la [solución Alert Management](log-analytics-solution-alert-management.md) para analizar las alertas creadas en Log Analytics junto con las alertas recopiladas desde System Center Operations Manager.
* Obtenga más información sobre las [búsquedas de registros](log-analytics-log-searches.md) que pueden generar alertas.
* Complete un tutorial para [configurar un webhook](log-analytics-alerts-webhooks.md) con una regla de alerta.  
* Aprenda a escribir [runbooks en Azure Automation](https://azure.microsoft.com/documentation/services/automation) para solucionar los problemas identificados por las alertas.
