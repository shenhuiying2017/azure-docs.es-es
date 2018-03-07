---
title: "Alertas de registro en Azure Monitor: Alertas (versión preliminar) | Microsoft Docs"
description: "Desencadenamiento de correos electrónicos y notificaciones, y llamadas a direcciones URL de sitios web (webhooks) o a la automatización cuando se cumplen las condiciones de consulta compleja especificadas para Alertas de Azure (versión preliminar)."
author: msvijayn
manager: kmadnani1
editor: 
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.assetid: f7457655-ced6-4102-a9dd-7ddf2265c0e2
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/02/2018
ms.author: vinagara
ms.openlocfilehash: 438776e7f0885dbdb0d66ccdd18d854e14beb299
ms.sourcegitcommit: fbba5027fa76674b64294f47baef85b669de04b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 02/24/2018
---
# <a name="log-alerts-in-azure-monitor---alerts-preview"></a>Alertas de registro en Azure Monitor: Alertas (versión preliminar)
En este artículo se proporcionan detalles sobre cómo funcionan las reglas de alertas de consultas de Analytics en Alertas de Azure (versión preliminar) y se describen las diferencias entre los distintos tipos de reglas de alertas de registro.

Actualmente, Alertas de Azure (versión preliminar) admite las alertas de registro en consultas de [Azure Log Analytics](../log-analytics/log-analytics-tutorial-viewdata.md) y [Application Insights](../application-insights/app-insights-cloudservices.md#view-azure-diagnostic-events).

> [!WARNING]

> Actualmente, la alerta de registro de Alertas de Azure (versión preliminar) no admite consultas entre áreas de trabajo ni entre aplicaciones.

Además, los usuarios pueden perfeccionar sus consultas en la plataforma de Analytics de su preferencia en Azure y luego *importarlas para usarlas en Alertas (versión preliminar) al guardar la consulta*. Pasos a seguir:
- Para Application Insights: vaya al portal de Analytics, valide la consulta y sus resultados. Luego guárdela con un nombre único en *Consultas compartidas*.
- Para Log Analytics: vaya a Búsqueda de registros, valide la consulta y sus resultados. Luego use Guardar con un nombre único en cualquier categoría.

Entonces, cuando [cree una alerta de registro en Alertas (versión preliminar)](monitor-alerts-unified-usage.md), verá que la consulta guardada aparece como el tipo de señal **Registro (consulta guardada)**, tal como aparece en el ejemplo siguiente: ![Consulta guardada importada a Alertas](./media/monitor-alerts-unified/AlertsPreviewResourceSelectionLog-new.png)

> [!NOTE]
> Usar **Registro (consulta guardada)** genera una importación a Alertas. Por lo tanto, cualquier cambio que se realice después en Analytics no se reflejará en las reglas de alertas guardadas y viceversa.

## <a name="log-alert-rules"></a>Reglas de alertas de registro

Las alertas creadas por Alertas de Azure (versión preliminar) ejecutan automáticamente consultas de registros a intervalos regulares.  Si los resultados de la consulta de registros coinciden con determinados criterios, se crea un registro de alertas. Luego, la regla puede ejecutar automáticamente una o varias acciones para avisarle de manera proactiva de la alerta o invocar otro proceso, como enviar datos a una aplicación externa mediante [json based webhook](monitor-alerts-unified-log-webhook.md) con [Grupos de acciones](monitoring-action-groups.md). Diferentes tipos de reglas de alerta usan una lógica distinta para realizar este análisis.

Las reglas de alerta se definen mediante los siguientes detalles:

- **Consulta de registro**.  La consulta que se ejecuta cada vez que se activa la regla de alertas.  Los registros devueltos por esta consulta se usan para determinar si se crea una alerta.
- **Ventana de tiempo**.  Especifica el intervalo de tiempo para la consulta.  La consulta devuelve solo los registros que se crearon dentro de este intervalo de tiempo actual.  La ventana de tiempo puede ser cualquier valor entre 5 minutos y 1440 minutos o 24 horas. Por ejemplo, si el período de tiempo se establece en sesenta minutos, y la consulta se ejecuta a las 13:15, se devuelven solo los registros creados entre las 12:15 y las 13:15.
- **Frecuencia**.  Especifica con qué frecuencia se debe ejecutar la consulta. Puede ser cualquier valor entre 5 minutos y 24 horas. Debe ser igual que el período de tiempo o inferior a este valor.  Si el valor es mayor que la ventana de tiempo, se arriesga a que se pierdan registros.<br>Por ejemplo, considere la posibilidad de una ventana de tiempo de 30 minutos y una frecuencia de 60 minutos.  Si la consulta se ejecuta a la 1:00, devolverá registros entre las 12:30 y la 1:00 p. m.  La próxima vez que se ejecute la consulta será a las 2:00 y devolverá los registros comprendidos entre la 1:30 y las 2:00.  Nunca se evaluarán los registros creados entre la 1:00 y la 1:30.
- **Umbral**.  Los resultados de la búsqueda de registros se evalúan para determinar si se debe crear una alerta.  El umbral es diferente para los distintos tipos de reglas de alerta.

Cada regla de alertas de Log Analytics es de uno de los dos tipos posibles.  Cada uno de estos tipos se describe en detalle en las secciones que aparecen a continuación.

- **[Número de resultados](#number-of-results-alert-rules)**. Alerta única creada cuando los registros de números devueltos por la búsqueda de registros superan un número especificado.
- **[Unidades métricas](#metric-measurement-alert-rules)**.  Alerta creada para cada objeto de los resultados de la búsqueda de registros con valores que superan el umbral especificado.

Las diferencias entre los tipos de reglas de alerta son las siguientes.

- **La regla de alertas para número de resultados crea siempre una alerta única mientras que la regla de alertas para **Unidades métricas** crea una alerta para cada objeto que supere el umbral.
- Las reglas de alerta para **número de resultados** crean una alerta cuando se supera el umbral una sola vez. Las reglas de alerta para **unidades métricas** pueden crear una alerta cuando se supera el umbral un número determinado de veces en un intervalo de tiempo determinado.

## <a name="number-of-results-alert-rules"></a>Reglas de alerta para número de resultados
Las reglas de alerta para **número de resultados** crean una única alerta cuando el número de registros devueltos por la consulta de búsqueda supera el umbral especificado. Este tipo de regla de alertas es perfecto para trabajar con eventos como, por ejemplo, registros de eventos de Windows, Syslog, respuesta de WebApp y registros personalizados.  Es posible que desee crear una alerta cuando se genere un evento de error concreto o cuando se generen varios eventos de error durante un período de tiempo establecido.

**Umbral**: el umbral de reglas de alertas para un **número de resultados es mayor o menor que un valor determinado.  Se crea una alerta si el número de registros devueltos por la búsqueda de registros coincide con este criterio.

Para generar una alerta en un evento único, establezca el número de resultados en un valor mayor que 0 y compruebe la aparición de un evento único que se creó desde la última vez que se ejecutó la consulta. Algunas aplicaciones pueden registrar un error ocasional que no tiene por qué haber generado necesariamente una alerta.  Por ejemplo, la aplicación puede volver a intentar realizar el proceso que creó el evento de error y completarlo correctamente la vez siguiente.  En este caso, es posible que no desee crear la alerta a menos que se creen varios eventos en un período de tiempo determinado.  

En algunos casos, quizá quiera crear una alerta sin que haya un evento.  Por ejemplo, un proceso puede registrar eventos regulares para indicar que está funcionando correctamente.  Si no se registra uno de estos eventos dentro de un período de tiempo determinado, debe crearse una alerta.  En este caso, tendría que establecer el umbral en un valor **less than 1** (menor que 1).

### <a name="example"></a>Ejemplo
Considere un escenario donde desea saber cuándo la aplicación basada en web proporciona una respuesta a los usuarios con el código 500, un error interno del servidor. Crearía una regla de alertas con los detalles siguientes:  
**Consulta:** solicitudes | donde resultCode == "500"<br>
**Ventana de tiempo:** 30 minutos<br>
**Frecuencia de alerta:** cinco minutos<br>
**Valor de umbral:** mayor que 0<br>

De ese modo, la alerta ejecutaría la consulta cada 5 minutos, con 30 minutos de datos, para buscar cualquier registro con un código de resultado de 500. Si se encuentra aunque sea uno de estos registros, se desencadena la alerta y la acción configurada.

## <a name="metric-measurement-alert-rules"></a>Reglas de alertas para medición de métricas

Las reglas de alertas para **unidades métricas** crean una alerta para cada objeto de una consulta con un valor que supera un umbral especificado.  Presentan las siguientes diferencias con respecto a las reglas de alerta para **número de resultados**.

**Función de agregado**: determina el cálculo que se lleva a cabo y potencialmente un campo numérico para agregar.  Por ejemplo, **count()** devuelve el número de registros de la consulta y **avg(CounterValue)**, el promedio del campo CounterValue durante el intervalo.

> [!NOTE]

> La función de agregado en la consulta debe denominarse/llamarse AggregatedValue y proporcionar un valor numérico. 


**Campo de grupo**: se crea un registro con un valor agregado para cada instancia de este campo y se puede generar una alerta para cada una.  Por ejemplo, si desea generar una alerta para cada equipo, usaría **by Computer** (por Equipo).   

> [!NOTE]

> Para las reglas de alerta de unidades métricas que se basan en Application Insights, puede especificar el campo para agrupar los datos. Para ello, use la opción **Agregado en** en la definición de la regla.   

**Intervalo**: define el intervalo de tiempo durante el cual se agregan los datos.  Por ejemplo, si especifica **cinco minutos**, se crearía un registro para cada instancia del campo de grupo que se agrega a intervalos de cinco minutos en la ventana de tiempo especificada para la alerta.
> [!NOTE]
> La función Bin se debe usar en la consulta. Además, si se generan intervalos de tiempo distintos para la ventana de tiempo por el uso de la función Bin, la alerta usará en su lugar la función bin_at para asegurarse de que hay un punto fijo

**Umbral**: el umbral de las reglas de alertas para unidades métricas se define por un valor agregado y un número de infracciones.  Si cualquier punto de datos de la búsqueda de registros supera este valor, se considera una infracción.  Si el número de infracciones para cualquier objeto de los resultados supera el valor especificado, se crea una alerta para ese objeto.

#### <a name="example"></a>Ejemplo
Considere la posibilidad de un escenario en el que desearía tener una alerta en caso de que cualquier equipo superara el uso del procesador en un 90 % tres veces en 30 minutos.  Crearía una regla de alertas con los detalles siguientes:  

**Consulta:** Perf | where ObjectName == "Processor" and CounterName == "% Processor Time" | summarize AggregatedValue = avg(CounterValue) by bin(TimeGenerated, 5 m), Computer<br>
**Ventana de tiempo:** 30 minutos<br>
**Frecuencia de alerta:** cinco minutos<br>
**Valor agregado:** mayor que 90<br>
**Desencadenar alerta según:** total de infracciones mayor que 5<br>

La consulta crearía un valor medio para cada equipo a intervalos de cinco minutos.  Esta consulta se ejecutaría cada 5 minutos para los datos recopilados en los 30 minutos anteriores.  A continuación se muestran datos de ejemplo para tres equipos.

![Resultados de la consulta de ejemplo](../log-analytics/media/log-analytics-alerts/metrics-measurement-sample-graph.png)

En este ejemplo, se crearán alertas independientes para srv02 y srv03 porque han incumplido el umbral del 90 % 3 veces en la ventana de tiempo.  Si se cambiara **Desencadenar alerta según:** a **Consecutivo**, entonces se crearía una alerta solo para srv03, ya que incumplió el umbral para tres ejemplos consecutivos.


## <a name="next-steps"></a>pasos siguientes
* Conocer las [acciones de webhook para alertas de registro](monitor-alerts-unified-log-webhook.md)
* [Obtenga información general sobre Alertas de Azure (versión preliminar)](monitoring-overview-unified-alerts.md).
* Obtenga información sobre cómo [utilizar Alertas de Azure (versión preliminar)](monitor-alerts-unified-usage.md).
* Más información sobre [Application Insights](../application-insights/app-insights-analytics.md)
* Más información sobre [Log Analytics](../log-analytics/log-analytics-overview.md).    
