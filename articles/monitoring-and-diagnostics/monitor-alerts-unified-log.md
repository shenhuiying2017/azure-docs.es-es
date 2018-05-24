---
title: 'Alertas de registro en Azure Monitor: Alertas | Microsoft Docs'
description: Desencadene correos electrónicos y notificaciones, llame a direcciones URL de sitios web (webhooks) o automatice cuando se cumplen las condiciones de consulta analítica especificadas para Alertas de Azure.
author: msvijayn
manager: kmadnani1
editor: ''
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.assetid: f7457655-ced6-4102-a9dd-7ddf2265c0e2
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/01/2018
ms.author: vinagara
ms.openlocfilehash: 8bf534177e8236a7d72d6dfdd4612b5f6f492b17
ms.sourcegitcommit: d28bba5fd49049ec7492e88f2519d7f42184e3a8
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/11/2018
---
# <a name="log-alerts-in-azure-monitor---alerts"></a>Alertas de registro en Azure Monitor: Alertas 
En este artículo se proporcionan detalles de Alertas de registro, uno de los tipos de alerta que se admiten en el nuevo [Alertas de Azure](monitoring-overview-unified-alerts.md) y permite que los usuarios usen la plataforma de análisis de Azure como base para las alertas. Para detalles de las alertas de métrica mediante los registros, consulte las [alertas de métricas casi en tiempo real](monitoring-near-real-time-metric-alerts.md)


Alerta de registro consiste en reglas de búsqueda de registros creadas para [Azure Log Analytics](../log-analytics/log-analytics-tutorial-viewdata.md) o [Application Insights](../application-insights/app-insights-cloudservices.md#view-azure-diagnostic-events).


## <a name="log-search-alert-rule---definition-and-types"></a>Regla de alertas de búsqueda de registros: definición y tipos

Alertas de Azure crea las reglas de búsqueda de registros para ejecutar automáticamente consultas de registros especificadas a intervalos regulares.  Si los resultados de la consulta de registros coinciden con determinados criterios, se crea un registro de alertas. La regla, luego, puede ejecutar de forma automática una o varias acciones con [grupos de acciones](monitoring-action-groups.md). 

Las reglas de búsqueda de registros se definen mediante los siguientes detalles:
- **Consulta de registro**.  La consulta que se ejecuta cada vez que se activa la regla de alertas.  Los registros devueltos por esta consulta se usan para determinar si se crea una alerta. La consulta de *Azure Application Insights* también puede incluir [llamadas entre aplicaciones](https://dev.applicationinsights.io/ai/documentation/2-Using-the-API/CrossResourceQuery), siempre que el usuario tenga derechos de acceso a las aplicaciones externas. 

    > [!IMPORTANT]
    > La compatibilidad con [la consulta entre aplicaciones para Application Insights](https://dev.applicationinsights.io/ai/documentation/2-Using-the-API/CrossResourceQuery) está en versión preliminar, por lo que la funcionalidad y la experiencia del usuario están sujetas a cambios. Actualmente, el uso de [consultas entre áreas de trabajo](https://dev.loganalytics.io/oms/documentation/3-Using-the-API/CrossResourceQuery) y la [consulta entre recursos de Log Analytics](../log-analytics/log-analytics-cross-workspace-search.md) **no se admite** en Alertas de Azure.

- **Período de tiempo**.  Especifica el intervalo de tiempo para la consulta. La consulta devuelve solo los registros que se crearon dentro de este intervalo de tiempo actual. El período de tiempo restringe los datos capturados para la consulta de registros, a fin de evitar abusos y cualquier comando de tiempo (como “ago”) utilizado en las consultas de registros. <br>*Por ejemplo, si el período de tiempo se establece en 60 minutos, y la consulta se ejecuta a las 13:15, se devuelven solo los registros creados entre las 12:15 y las 13:15 para ejecutar la consulta de registros. Ahora, si la consulta de registros utiliza un comando de tiempo, como ago (7d), la consulta de registro se ejecutaría solo para los datos entre las 12:15 y las 13:15, como si existieran datos solo para los últimos 60 minutos. Y no durante siete días de datos, tal como se especifica en la consulta de registros.*
- **Frecuencia**.  Especifica con qué frecuencia se debe ejecutar la consulta. Puede ser cualquier valor entre 5 minutos y 24 horas. Debe ser menor o igual que el período de tiempo.  Si el valor es mayor que el período de tiempo, se arriesga a que se pierdan registros.<br>*Por ejemplo, considere la posibilidad de un período de tiempo de 30 minutos y una frecuencia de 60 minutos.  Si la consulta se ejecuta a la 1:00, devolverá registros entre las 12:30 y la 1:00 p. m.  La próxima vez que se ejecute la consulta será a las 2:00 y devolverá los registros comprendidos entre la 1:30 y las 2:00.  Nunca se evaluarían los registros creados entre la 1:00 y las 13:30.*
- **Umbral**.  Los resultados de la búsqueda de registros se evalúan para determinar si se debe crear una alerta.  El umbral es diferente para los distintos tipos de reglas de alerta de búsqueda de registros.

Las reglas de búsqueda de registros para [Azure Log Analytics](../log-analytics/log-analytics-tutorial-viewdata.md) o [Application Insights](../application-insights/app-insights-cloudservices.md#view-azure-diagnostic-events) pueden ser de dos tipos. Cada uno de estos tipos se describe en detalle en las secciones que aparecen a continuación.

- **[Número de resultados](#number-of-results-alert-rules)**. Alerta única creada cuando los registros de números devueltos por la búsqueda de registros superan un número especificado.
- **[Unidades métricas](#metric-measurement-alert-rules)**.  Alerta creada para cada objeto de los resultados de la búsqueda de registros con valores que superan el umbral especificado.

Las diferencias entre los tipos de reglas de alerta son las siguientes.

- La regla de alertas para *Número de resultados* crea siempre una alerta única mientras que la regla de alertas para *Unidades métricas* crea una alerta para cada objeto que supere el umbral.
- Las reglas de alerta para *número de resultados* crean una alerta cuando se supera el umbral una sola vez. Las reglas de alerta para *unidades métricas* pueden crear una alerta cuando se supera el umbral un número determinado de veces en un intervalo de tiempo determinado.

### <a name="number-of-results-alert-rules"></a>Reglas de alerta para número de resultados
Las reglas de alerta para **número de resultados** crean una única alerta cuando el número de registros devueltos por la consulta de búsqueda supera el umbral especificado. Este tipo de regla de alertas es perfecto para trabajar con eventos como, por ejemplo, registros de eventos de Windows, Syslog, respuesta de WebApp y registros personalizados.  Es posible que desee crear una alerta cuando se genere un evento de error concreto o cuando se generen varios eventos de error durante un período de tiempo establecido.

**Umbral**: el umbral de reglas de alertas para un Número de resultados es mayor o menor que un valor determinado.  Se crea una alerta si el número de registros devueltos por la búsqueda de registros coincide con este criterio.

Para generar una alerta en un evento único, establezca el número de resultados en un valor mayor que 0 y compruebe la aparición de un evento único que se creó desde la última vez que se ejecutó la consulta. Algunas aplicaciones pueden registrar un error ocasional que no tiene por qué haber generado necesariamente una alerta.  Por ejemplo, la aplicación puede volver a intentar realizar el proceso que creó el evento de error y completarlo correctamente la vez siguiente.  En este caso, es posible que no desee crear la alerta, a menos que se creen varios eventos en un período de tiempo determinado.  

En algunos casos, quizá quiera crear una alerta sin que haya un evento.  Por ejemplo, un proceso puede registrar eventos regulares para indicar que está funcionando correctamente.  Si no se registra uno de estos eventos dentro de un período de tiempo determinado, debe crearse una alerta.  En este caso, tendría que establecer el umbral en un valor **less than 1** (menor que 1).

#### <a name="example"></a>Ejemplo
Considere un escenario donde desea saber cuándo la aplicación basada en web proporciona una respuesta a los usuarios con el código 500, un error interno del servidor. Crearía una regla de alertas con los detalles siguientes:  
- **Consulta:** solicitudes | donde resultCode == "500"<br>
- **Período de tiempo:** 30 minutos<br>
- **Frecuencia de alerta:** cinco minutos<br>
- **Valor de umbral:** mayor que 0<br>

De ese modo, la alerta ejecutaría la consulta cada 5 minutos, con 30 minutos de datos, para buscar cualquier registro con un código de resultado de 500. Si se encuentra aunque sea uno de estos registros, se desencadena la alerta y la acción configurada.

### <a name="metric-measurement-alert-rules"></a>Reglas de alertas para medición de métricas

- Las reglas de alertas para **unidades métricas** crean una alerta para cada objeto de una consulta con un valor que supera un umbral especificado.  Presentan las siguientes diferencias con respecto a las reglas de alerta para **número de resultados**.
- **Función de agregado**: determina el cálculo que se lleva a cabo y potencialmente un campo numérico para agregar.  Por ejemplo, **count()** devuelve el número de registros de la consulta y **avg(CounterValue)**, el promedio del campo CounterValue durante el intervalo. La función de agregado en la consulta debe denominarse/llamarse AggregatedValue y proporcionar un valor numérico. 
- **Campo de grupo**: se crea un registro con un valor agregado para cada instancia de este campo y se puede generar una alerta para cada una.  Por ejemplo, si desea generar una alerta para cada equipo, usaría **by Computer** (por Equipo). 

    > [!NOTE]
    > Para las reglas de alerta de unidades métricas que se basan en Application Insights, puede especificar el campo para agrupar los datos. Para ello, use la opción **Agregado en** en la definición de la regla.   
    
- **Intervalo**: define el intervalo de tiempo durante el cual se agregan los datos.  Por ejemplo, si especifica **cinco minutos**, se crearía un registro para cada instancia del campo de grupo que se agrega a intervalos de cinco minutos en el período de tiempo especificado para la alerta.

    > [!NOTE]
    > La función Bin se debe usar en la consulta para especificar el intervalo. Como bin() puede generar intervalos de tiempo distintos, - Alert convertirá el comando bin automáticamente en el comando bin_at con un tiempo adecuado en el tiempo de ejecución para garantizar resultados con un punto fijo
    
- **Umbral**: el umbral de las reglas de alertas para unidades métricas se define por un valor agregado y un número de infracciones.  Si cualquier punto de datos de la búsqueda de registros supera este valor, se considera una infracción.  Si el número de infracciones para cualquier objeto de los resultados supera el valor especificado, se crea una alerta para ese objeto.

#### <a name="example"></a>Ejemplo
Considere la posibilidad de un escenario en el que desearía tener una alerta en caso de que cualquier equipo superara el uso del procesador en un 90 % tres veces en 30 minutos.  Crearía una regla de alertas con los detalles siguientes:  

- **Consulta:** Perf | where ObjectName == "Processor" and CounterName == "% Processor Time" | summarize AggregatedValue = avg(CounterValue) by bin(TimeGenerated, 5 m), Computer<br>
- **Período de tiempo:** 30 minutos<br>
- **Frecuencia de alerta:** cinco minutos<br>
- **Valor agregado:** mayor que 90<br>
- **Desencadenar alerta según:** total de infracciones mayor que 2<br>

La consulta crearía un valor medio para cada equipo a intervalos de cinco minutos.  Esta consulta se ejecutaría cada 5 minutos para los datos recopilados en los 30 minutos anteriores.  A continuación se muestran datos de ejemplo para tres equipos.

![Resultados de la consulta de ejemplo](./media/monitor-alerts-unified/metrics-measurement-sample-graph.png)

En este ejemplo, se crearán alertas independientes para srv02 y srv03 porque han incumplido el umbral del 90 % 3 veces en el período de tiempo.  Si se cambiara **Desencadenar alerta según:** a **Consecutivo**, entonces se crearía una alerta solo para srv03, ya que incumplió el umbral para tres ejemplos consecutivos.


## <a name="log-search-alert-rule---creation-and-modification"></a>Regla de alerta de búsqueda de registros: creación y modificación

La alerta del registro, así como su regla de alertas de búsqueda de registros, se puede ver, crear o modificar desde:
- Azure Portal
- API de REST (incluido a través de PowerShell)
- Plantillas del Administrador de recursos de Azure

### <a name="azure-portal"></a>Azure Portal
Desde la introducción de las [nuevas Alertas de Azure](monitoring-overview-unified-alerts.md), ahora los usuarios pueden administrar todos los tipos de alerta en Azure Portal desde una ubicación única y mediante pasos similares. Más información sobre el [uso de las nuevas Alertas de Azure](monitor-alerts-unified-usage.md).

Además, los usuarios pueden perfeccionar sus consultas en la plataforma de Analytics de su preferencia en Azure y luego *importarlas para usarlas en Alertas al guardar la consulta*. Pasos a seguir:
- *Para Application Insights*: vaya al portal de Analytics, valide la consulta y sus resultados. Luego guárdela con un nombre único en *Consultas compartidas*.
- *Para Log Analytics*: vaya a Búsqueda de registros, valide la consulta y sus resultados. Luego use Guardar con un nombre único en cualquier categoría.

Entonces, cuando [cree una alerta de registro en Alertas](monitor-alerts-unified-usage.md), verá que la consulta guardada aparece como el tipo de señal **Registro (consulta guardada)**, tal como aparece en el ejemplo siguiente: ![Consulta guardada importada a Alertas](./media/monitor-alerts-unified/AlertsPreviewResourceSelectionLog-new.png)

> [!NOTE]
> Usar **Registro (consulta guardada)** genera una importación a Alertas. Por lo tanto, cualquier cambio que se realice después en Analytics no se reflejará en las reglas de alertas de búsqueda de registros y viceversa.

### <a name="rest-apis"></a>API de REST
Las API proporcionadas para Alertas de registro son de tipo RESTful y puede accederse a ellas con la API de REST de Azure Resource Manager. Por lo tanto, puede tener acceso a través de PowerShell, así como otras opciones para aprovechar las API.

Para obtener más información, así como ejemplos sobre el uso de la API de REST, consulte:
- [API de REST de alertas de Log Analytics](../log-analytics/log-analytics-api-alerts.md): para crear y administrar reglas de alertas de búsqueda de registros de Azure Log Analytics
- [API de REST de reglas de consulta programadas de Azure Monitor ](https://docs.microsoft.com/en-us/rest/api/monitorr/scheduledqueryrules/): para crear y administrar reglas de alertas de búsqueda de registros de Azure Application Insights

### <a name="azure-resource-manager-template"></a>Plantilla de Azure Resource Manager
Los usuarios también pueden utilizar la flexibilidad proporcionada por [Azure Resource Manager](../azure-resource-manager/resource-group-overview.md) para crear y actualizar recursos, para crear o actualizar Alertas de registro.

Para obtener más información, así como ejemplos sobre el uso de las plantillas de Resource Manager, consulte:
- [Búsquedas guardadas y administración de alertas](monitor-alerts-unified-log-template.md#managing-log-alert-on-log-analytics) para las alertas de registro basadas en Azure Log Analytics
- [Regla de consultas programada](monitor-alerts-unified-log-template.md#managing-log-alert-on-application-insights) para las alertas de registro basadas en Azure Application Insights
 

## <a name="next-steps"></a>Pasos siguientes
* Información sobre las [alertas de registro en Azure](monitor-alerts-unified-log-webhook.md).
* Obtenga información sobre las nuevas [Alertas de Azure](monitoring-overview-unified-alerts.md)
* Más información sobre [Application Insights](../application-insights/app-insights-analytics.md).
* Más información sobre [Log Analytics](../log-analytics/log-analytics-overview.md).    
