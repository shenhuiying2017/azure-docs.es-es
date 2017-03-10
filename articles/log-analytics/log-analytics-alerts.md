---
title: "Introducción a las alertas en OMS Log Analytics | Microsoft Docs"
description: "Las alertas de Log Analytics identifican información importante en el repositorio de OMS y pueden avisarle proactivamente de problemas o invocar acciones para intentar corregirlos.  En este artículo se describe cómo crear una regla de alerta y los detalles de las distintas acciones que pueden realizar."
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
ms.date: 02/28/2017
ms.author: bwren
ms.custom: H1Hack27Feb2017
translationtype: Human Translation
ms.sourcegitcommit: ec7167fb36e54219957629699f33dfce950f86d5
ms.openlocfilehash: 48d921650dbbf3f9cc2a8a0e265e0c7deaebdafd
ms.lasthandoff: 03/01/2017


---
# <a name="respond-to-issues-in-log-analytics-using-alerts"></a>Respuesta a los problemas de Log Analytics mediante el uso de alertas

Las alertas de Log Analytics identifican información importante en el repositorio de Log Analytics.  Puede usarlas para que le avisen de una manera activa de un problema o para realizar acciones automatizadas en respuesta al problema.  Este artículo proporciona una introducción sobre cómo se crean y se utilizan las alertas.  


>[!NOTE]
> Para más información sobre las reglas de alerta para unidades métricas que actualmente están disponibles en la versión preliminar pública, consulte [New metric measurement alert rule type in Public Preview!](https://blogs.technet.microsoft.com/msoms/2016/11/22/new-metric-measurement-alert-rule-type-in-public-preview/) (Nuevo tipo de reglas de alerta para unidades métricas en la versión preliminar pública).

## <a name="alert-rules"></a>Las reglas de alertas

Las alertas se crean mediante reglas de alerta que ejecutan automáticamente búsquedas de registros a intervalos regulares.  Si los resultados de la búsqueda de registros coinciden con determinados criterios, se crea un registro de alertas.  La regla puede ejecutar automáticamente una o varias acciones para avisarle de manera activa de la alerta o invocar otro proceso.  

![Alertas de Log Analytics](media/log-analytics-alerts/overview.png)


Las reglas de alerta se definen mediante los siguientes detalles.

- **Búsqueda de registros**.  Esta es la consulta que se ejecutará cada vez que se active la regla de alerta.  Los registros devueltos por esta consulta se utilizarán para determinar si se crea una alerta.
- **Ventana de tiempo**.  Especifica el intervalo de tiempo para la consulta.  La consulta devuelve solo los registros que se crearon dentro de este intervalo de tiempo actual.  Puede ser cualquier valor entre 5 minutos y 24 horas. Por ejemplo, si el período de tiempo se establece en 60 minutos, y la consulta se ejecuta a las 13:15, se devolverán solo los registros que se crearon entre las 12:15 y las 13:15.
- **Frecuencia**.  Especifica con qué frecuencia se debe ejecutar la consulta. Puede ser cualquier valor entre 5 minutos y 24 horas. Debe ser igual que el período de tiempo o inferior a este valor.  Si el valor es mayor que la ventana de tiempo, se arriesga a que se pierdan registros.<br>Por ejemplo, considere la posibilidad de una ventana de tiempo de 30 minutos y una frecuencia de 60 minutos.  Si la consulta se ejecuta a la 1:00, devolverá registros entre las 12:30 y la 1:00 p. m.  La próxima vez que se ejecute la consulta será a las 2:00 y devolverá los registros comprendidos entre la 1:30 y las 2:00.  Nunca se evaluarán los registros creados entre la 1:00 y la 1:30.
- **Umbral**.  Si el número de registros devueltos por la búsqueda de registros supera el umbral, se crea una alerta.

## <a name="creating-alert-rules"></a>Creación de reglas de alerta
Puede crear y modificar las reglas de alerta mediante varios métodos.  Consulte los artículos siguientes para obtener instrucciones detalladas.  

- Creación de reglas de alerta mediante el [portal de OMS](log-analytics-alerts-creating.md).
- Creación de reglas de alerta mediante una [plantilla de Resource Manager](log-analytics-template-workspace-configuration.md).
- Creación de reglas de alerta mediante la [API de REST](log-analytics-api-alerts.md).

## <a name="alert-actions"></a>Acciones de alerta

Además de crear un registro de alertas, una regla de alerta puede conllevar una o varias acciones para crear una alerta.  Puede utilizar acciones para enviar un correo electrónico en respuesta a la alerta o iniciar un proceso que intente realizar una acción correctiva.  

También puede aprovechar las acciones para aumentar la funcionalidad de Log Analytics con otros servicios.  Por ejemplo, actualmente no proporciona características para enviarle una notificación mediante SMS o por teléfono.  Puede usar una acción de webhook en una regla de alerta para que llame a un servicio como [PagerDuty](https://www.pagerduty.com/) que proporciona estas características.  Puede consultar un ejemplo de cómo crear un webhook para enviar un mensaje mediante [Slack](https://slack.com/) en [Creación de una acción de webhook de alerta en OMS Log Analytics para enviar mensajes a Slack](log-analytics-alerts-webhooks.md).

En la tabla siguiente se enumeran las acciones que puede realizar.  Puede obtener información sobre cada una de estas acciones en [Adding actions to alert rules in Log Analytics](log-analytics-alerts-actions.md) (Adición de acciones a reglas de alerta en Log Analytics). 

| Acción | Descripción |
|:--|:--|
| Email  |     Envía un correo electrónico con los detalles de la alerta a uno o varios destinatarios. |
| webhook | Invoca un proceso externo a través de una sola solicitud HTTP POST. |
| Runbook | Inicia un runbook en Azure Automation. |


## <a name="alerting-scenarios"></a>Escenarios de alertas

### <a name="events"></a>Eventos
Para generar una alerta en un solo evento, cree una regla de alerta con un número de resultados **mayor que 0** y cuya frecuencia y ventana de tiempo sean de **5 minutos**.  De ese modo, se ejecutará la consulta cada 5 minutos y se buscará la aparición de un evento único creado en el tiempo transcurrido desde la última vez ejecución de la consulta.  Una frecuencia mayor puede retrasar el tiempo entre la recopilación del evento y la creación de la alerta.  Debería usar una consulta parecida a la siguiente para especificar los eventos que le interesan.

    Type=Event Source=MyApplication EventID=7019 

Algunas aplicaciones pueden registrar un error ocasional que no tiene por qué haber generado necesariamente una alerta.  Por ejemplo, la aplicación puede volver a intentar realizar el proceso que creó el evento de error y completarlo correctamente la vez siguiente.  En este caso, es posible que no desee crear la alerta a menos que se creen varios eventos en un período de tiempo determinado.  Para ello, se debería usar la misma consulta pero estableciendo el umbral en un número mayor.  Por ejemplo, para generar una alerta en 5 eventos en 30 minutos, establezca la frecuencia en **5 minutos**, la ventana de tiempo en **30 minutos** y el número de resultados en **mayor que 4**.    

En algunos casos, quizá quiera crear una alerta sin que haya un evento.  Por ejemplo, un proceso puede registrar eventos regulares para indicar que está funcionando correctamente.  Si no se registra uno de estos eventos dentro de un período de tiempo determinado, debe crearse una alerta.  En este caso, tendría que establecer el número de resultados en un valor **menor que 1**.

### <a name="performance-alerts"></a>Alertas de rendimiento
[Los datos de rendimiento](log-analytics-data-sources-performance-counters.md) se almacenan como registros en el repositorio de Log Analytics de manera similar a los eventos.  Si desea que se cree la alerta cuando un contador de rendimiento supere un umbral determinado, ese umbral debe incluirse en la consulta.

Por ejemplo, si quiere que se genere una alerta cuando el rendimiento del procesador supere el 90 %, puede utilizar una consulta como la siguiente con el número de resultados de la regla de alerta en un valor **mayor que 0**.

    Type=Perf ObjectName=Processor CounterName="% Processor Time" CounterValue>90

Si quiere emitir una alerta cuando el rendimiento medio del procesador supere el 90 % durante un determinado período de tiempo, puede utilizar una consulta como la siguiente con el [comando measure](log-analytics-search-reference.md#commands) y con un umbral de la regla de alerta **mayor que 0**. 

    Type=Perf ObjectName=Processor CounterName="% Processor Time" | measure avg(CounterValue) by Computer | where AggregatedValue>90


## <a name="alert-records"></a>Registros de alerta
Los registros de alerta creados por reglas de alerta en Log Analytics tienen un **Tipo** de **Alerta** y un **SourceSystem** de **OMS**.  Tienen las propiedades de la tabla siguiente.

| Propiedad | Descripción |
|:--- |:--- |
| Tipo |*Alerta* |
| SourceSystem |*OMS* |
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
* Cree una regla de alerta mediante el [portal de OMS](log-analytics-alerts-creating.md).
* Instale la [solución de Administración de alertas](log-analytics-solution-alert-management.md) para analizar las alertas creadas en Log Analytics junto con las alertas recopiladas desde System Center Operations Manager (SCOM).
* Obtenga más información sobre las [búsquedas de registros](log-analytics-log-searches.md) que pueden generar alertas.
* Complete un tutorial para [configurar un webhook](log-analytics-alerts-webhooks.md) con una regla de alerta.  
* Aprenda a escribir [runbooks en Automatización de Azure](https://azure.microsoft.com/documentation/services/automation) para solucionar los problemas identificados por las alertas.


