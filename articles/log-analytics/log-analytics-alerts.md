---
title: "Creación de alertas en Log Analytics (OMS) | Microsoft Docs"
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
ms.date: 01/25/2017
ms.author: bwren
translationtype: Human Translation
ms.sourcegitcommit: 9fe104a1ea26afa2817aedaa8ed77d042404cda6
ms.openlocfilehash: 9a62ed7540de05b1db7610e12f2671d33fc8049d


---
# <a name="alerts-in-log-analytics"></a>Alertas de Log Analytics
Las alertas de Log Analytics identifican información importante en el repositorio de OMS.  Las reglas de alertas ejecutan automáticamente búsquedas de registros según una programación y crean un registro de alerta si los resultados coinciden con determinados criterios.  Luego, la regla puede ejecutar automáticamente una o varias acciones para avisarle proactivamente de la alerta o invocar otro proceso.   

![Alertas de Log Analytics](media/log-analytics-alerts/overview.png)

>[!NOTE]
> Para más información sobre las reglas de alerta para unidades métricas que actualmente están disponibles en la versión preliminar pública, consulte [New metric measurement alert rule type in Public Preview!](https://blogs.technet.microsoft.com/msoms/2016/11/22/new-metric-measurement-alert-rule-type-in-public-preview/) (Nuevo tipo de reglas de alerta para unidades métricas en la versión preliminar pública).

## <a name="creating-an-alert-rule"></a>Creación de una regla de alerta
Para crear una regla de alerta, primero debe crear una búsqueda de registros para los registros que deben invocar la alerta.  El botón **Alerta** estará entonces disponible para que pueda crear y configurar la regla de alerta.

1. En la página de información general de OMS, haga clic en **Búsqueda de registros**.
2. Cree una nueva consulta de búsqueda de registros o seleccione una búsqueda de registros guardada. 
3. Haga clic en **Alerta** en la parte superior de la página para abrir la pantalla **Agregar regla de alerta**.
4. Consulte las tablas siguientes para obtener más información acerca de las opciones para configurar la alerta.
5. Al facilitar el período de tiempo para la regla de alerta, se mostrará el número de registros existentes que cumplen los criterios de búsqueda para ese período de tiempo.  Esto puede ayudarle a determinar la frecuencia con que proporcionará el número de resultados esperados.
6. Haga clic en **Guardar** para completar la regla de alerta.  Se iniciará la ejecución de inmediato.

![Agregar regla de alerta](media/log-analytics-alerts/add-alert-rule.png)

| Propiedad | Descripción |
|:--- |:--- |
| **Información de alertas** | |
| Nombre |Nombre único para identificar la regla de alerta. |
| Gravedad |Gravedad de la alerta que se crea mediante esta regla. |
| Consulta de búsqueda |Seleccione **Use current search query** (Usar consulta de búsqueda actual) para utilizar la consulta actual o seleccionar una búsqueda guardada de la lista.  La sintaxis de la consulta se proporciona en el cuadro de texto, desde donde puede modificarla si fuera necesario. |
| Período de tiempo |Especifica el intervalo de tiempo para la consulta.  La consulta devuelve solo los registros que se crearon dentro de este intervalo de tiempo actual.  Puede ser cualquier valor entre 5 minutos y 24 horas.  Debe ser mayor que la frecuencia de alertas o igual a este valor.  <br><br> Por ejemplo, si el período de tiempo se establece en 60 minutos, y la consulta se ejecuta a las 13:15, se devolverán solo los registros que se crearon entre las 12:15 y las 13:15. |
| **Programación** | |
| Umbral |Criterios para la creación de una alerta.  Una alerta se crea si el número de registros devueltos por la consulta coincide con este criterio. |
| Frecuencia de alertas |Especifica con qué frecuencia se debe ejecutar la consulta.  Puede ser cualquier valor entre 5 minutos y 24 horas.  Debe ser igual que el período de tiempo o inferior a este valor. |
| Suprimir alertas |Al activar la supresión de la regla de alerta, las acciones de la regla se deshabilitan durante un período de tiempo definido después de crear una nueva alerta.  La regla se seguirá ejecutando y creará registros de alerta si se cumplen los criterios.  Esto se hace así para que tenga tiempo para corregir el problema sin ejecutar acciones duplicadas. |
| **Acciones** | |
| Notificación por correo electrónico |Especifique **Sí** si desea que se le envíe un correo electrónico cuando se active la alerta. |
| Asunto |El asunto del correo electrónico.  No se puede modificar el cuerpo del mensaje de correo. |
| Recipients |Direcciones de todos los destinatarios de correo electrónico.  Si especifica más de una dirección, separe las direcciones con un punto y coma (;). |
| webhook |Especifique **Sí** si desea llamar a un webhook cuando se desencadene la alerta. |
| Dirección URL de Webhook |La dirección URL del webhook. |
| Incluir la carga personalizada de JSON |Seleccione esta opción si desea reemplazar la carga predeterminada por una carga personalizada. |
| Especificar la carga JSON personalizada |La carga personalizada para el webhook.  Consulte la sección anterior para obtener más información. |
| Runbook |Especifique **Sí** si desea iniciar un runbook de Automatización de Azure cuando se desencadene la alerta. |
| Seleccionar un runbook |Seleccione el runbook para iniciar entre los runbooks de la cuenta de automatización configurada en la solución de Automatización. |
| Ejecutar en |Seleccione **Azure** para ejecutar el runbook en la nube de Azure.  Seleccione **Hybrid Worker** para ejecutar el runbook en un [Hybrid Runbook Worker](../automation/automation-hybrid-runbook-worker.md) de su entorno local. |


## <a name="manage-alert-rules"></a>Administración de reglas de alerta
Puede obtener una lista de todas las reglas de alerta en el menú **Alertas** de la **configuración** de Log Analytics.  

![Administrar alertas](./media/log-analytics-alerts/configure.png)

1. En la consola de OMS, seleccione el icono **Configuración** .
2. Seleccione **Alertas**.

Puede realizar varias acciones desde esta vista.

* Deshabilite una regla seleccionando el icono **Desactivada** que aparece a su lado.
* Edite una regla de alerta haciendo clic en el icono de lápiz junto a ella.
* Quite una regla de alerta haciendo clic en el icono **X** que está a su lado. 

## <a name="setting-time-windows-and-thresholds"></a>Configuración de umbrales y períodos de tiempo

>[!NOTE]
> Para más información sobre las reglas de alerta para unidades métricas que actualmente están disponibles en la versión preliminar pública, consulte [New metric measurement alert rule type in Public Preview!](https://blogs.technet.microsoft.com/msoms/2016/11/22/new-metric-measurement-alert-rule-type-in-public-preview/) (Nuevo tipo de reglas de alerta para unidades métricas en la versión preliminar pública).
 
### <a name="event-alerts"></a>Alertas de eventos
Los eventos incluyen orígenes de datos como registros de eventos de Windows, Syslog y registros personalizados.  Es posible que desee crear una alerta cuando se genere un evento de error concreto o cuando se generen varios eventos de error durante un período de tiempo establecido.

Para generar una alerta en un solo evento, determine que el número de resultados sea mayor que 0 y que la frecuencia y el período de tiempo sean de 5 minutos.  De ese modo, se ejecutará la consulta cada 5 minutos y se buscará la aparición de un evento único creado en el tiempo transcurrido desde la última vez ejecución de la consulta.  Una frecuencia mayor puede retrasar el tiempo entre la recopilación del evento y la creación de la alerta.

Algunas aplicaciones pueden registrar un error ocasional que no tiene por qué haber generado necesariamente una alerta.  Por ejemplo, la aplicación puede volver a intentar realizar el proceso que creó el evento de error y completarlo correctamente la vez siguiente.  En este caso, es posible que no desee crear la alerta a menos que se creen varios eventos en un período de tiempo determinado.  

En algunos casos, quizá quiera crear una alerta sin que haya un evento.  Por ejemplo, un proceso puede registrar eventos regulares para indicar que está funcionando correctamente.  Si no se registra uno de estos eventos dentro de un período de tiempo determinado, debe crearse una alerta.  En este caso, tendría que establecer el umbral en un valor **less than 1** (menor que 1).

### <a name="performance-alerts"></a>Alertas de rendimiento
[Los datos de rendimiento](log-analytics-data-sources-performance-counters.md) se almacenan como registros en el repositorio de OMS de manera similar a los eventos.  Si desea que se cree la alerta cuando un contador de rendimiento supere un umbral determinado, ese umbral debe incluirse en la consulta.

Por ejemplo, si quiere emitir una alerta cuando el rendimiento del procesador supere el 90 %, puede utilizar una consulta como la siguiente con el umbral de la regla de alerta **greater than 0** (mayor que 0).

    Type=Perf ObjectName=Processor CounterName="% Processor Time" CounterValue>90

Si quiere emitir una alerta cuando el rendimiento medio del procesador supere el 90 % durante un determinado período de tiempo, puede utilizar una consulta como la siguiente con el [comando measure](log-analytics-search-reference.md#commands) y con el umbral de la regla de alerta **greater than 0** (mayor que 0). 

    Type=Perf ObjectName=Processor CounterName="% Processor Time" | measure avg(CounterValue) by Computer | where AggregatedValue>90

## <a name="alert-actions"></a>Acciones de alerta
Además de crear un registro de alertas, puede configurar la regla de alerta para ejecutar automáticamente una o varias acciones.  Las acciones pueden avisarle proactivamente de la alerta o invocar algún proceso que intente corregir el problema detectado.  En las secciones siguientes se describen las acciones que están actualmente disponibles.

### <a name="email-actions"></a>Acciones de correo electrónico
Las acciones de correo electrónico envían un correo electrónico con los detalles de la alerta a uno o varios destinatarios.  Puede especificar el asunto del mensaje de correo, pero su contenido es un formato estándar construido por Log Analytics.  En él se incluye información de resumen, como el nombre de la alerta, además de los detalles de hasta diez registros devueltos por la búsqueda de registros.  También se incluye un vínculo a una búsqueda de registros de Log Analytics que devolverá todo el conjunto de registros de esa consulta.   El remitente del mensaje de correo es el *equipo de Microsoft Operations Management Suite&lt;noreply@oms.microsoft.com&gt;*. 

### <a name="webhook-actions"></a>Acciones de webhook
Las acciones de Webhook permiten invocar un proceso externo a través de una sola solicitud HTTP POST.  El servicio que se llama debe admitir webhooks y determinar cómo utilizará toda la carga que reciba.  También puede llamar a una API de REST que no admita específicamente webhooks mientras la solicitud esté en un formato que entienda la API.  Entre los ejemplos de uso de webhook en respuesta a una alerta se encuentra el uso de un servicio como [Slack](http://slack.com) para enviar un mensaje con los detalles de la alerta o la creación de un incidente en un servicio como [PagerDuty](http://pagerduty.com/).  

Puede encontrar un tutorial completo sobre la creación de una regla de alerta con un webhook para llamar a un servicio de ejemplo en [Webhooks in Log Analytics alerts](log-analytics-alerts-webhooks.md)(Webhooks en alertas de Log Analytics).

Los webhooks incluyen una dirección URL y una carga en formato JSON que son los datos enviados al servicio externo.  De forma predeterminada, la carga incluirá los valores en la tabla siguiente.  Puede optar por reemplazar esta carga con una personalizada de su propiedad.  En ese caso, puede utilizar las variables de la tabla para cada uno de los parámetros para incluir su valor en la carga personalizada.

| Parámetro | Variable | Descripción |
|:--- |:--- |:--- |
| AlertRuleName |#AlertRuleName |Nombre de la regla de alerta. |
| AlertThresholdOperator |#thresholdoperator |Operador de umbral para la regla de alerta.  *Mayor que* o *menor que*. |
| AlertThresholdValue |#thresholdvalue |Valor de umbral para la regla de alerta. |
| LinkToSearchResults |#LinkToSearchResults |Vincular a la búsqueda de registros de Log Analytics que devuelve los registros de la consulta que creó la alerta. |
| ResultCount |#searchresultcount |Número de registros en los resultados de la búsqueda. |
| SearchIntervalEndtimeUtc |#SearchIntervalEndtimeUtc |Hora de finalización de la consulta en formato UTC. |
| SearchIntervalInSeconds |#searchinterval |Período de tiempo para la regla de alerta. |
| SearchIntervalStartTimeUtc |#SearchIntervalStartTimeUtc |Hora de inicio para la consulta en formato UTC. |
| SearchQuery |#SearchQuery |Consulta de búsqueda de registros utilizada por la regla de alerta. |
| SearchResults |Consulte a continuación |Registros devueltos por la consulta en formato JSON.  Limitado a los primeros 5000 registros. |
| WorkspaceID |#WorkspaceID |Identificador del área de trabajo de OMS. |

Por ejemplo, podría especificar la siguiente carga personalizada que incluye un único parámetro denominado *text*.  El servicio al que llama este webhook estaría esperando este parámetro.

    {
        "text":"#alertrulename fired with #searchresultcount over threshold of #thresholdvalue."
    }

Esta carga de ejemplo se resolvería en algo similar a lo siguiente al enviarse al webhook.

    {
        "text":"My Alert Rule fired with 18 records over threshold of 10 ."
    }

Para incluir resultados de búsqueda en una carga personalizada, agregue la siguiente línea como una propiedad de nivel superior en la carga de json.  

    "IncludeSearchResults":true

Por ejemplo, para crear una carga personalizada que incluya solo el nombre de la alerta y los resultados de la búsqueda, puede utilizar lo siguiente. 

    {
       "alertname":"#alertrulename",
       "IncludeSearchResults":true
    }


Puede recorrer un ejemplo completo de creación de una regla de alerta con un webhook para iniciar un servicio externo en [Webhooks in Log Analytics alerts](log-analytics-alerts-webhooks.md)(Webhooks en alertas de Log Analytics).

### <a name="runbook-actions"></a>Acciones de runbook
Las acciones de runbook inician un runbook en Automatización de Azure.  Para usar este tipo de acción, debe tener la [solución de Automatización](log-analytics-add-solutions.md) instalada y configurada en el área de trabajo de OMS.  Si no la tiene instalada, cuando cree una nueva regla de alerta, se mostrará un vínculo a su instalación.  Puede seleccionar entre los runbooks de la cuenta de automatización que configuró en la solución de Automatización.

Las acciones de runbook inician el runbook mediante un [webhook](../automation/automation-webhooks.md).  Al crear la regla de alerta, se creará automáticamente un nuevo webhook para el runbook con el nombre **OMS Alert Remediation** seguido de un GUID.  

No se pueden llenar directamente los parámetros del runbook, pero el [parámetro $WebhookData](../automation/automation-webhooks.md) incluirá los detalles de la alerta, como los resultados de la búsqueda de registros que lo crearan.  El runbook tendrá que definir **$WebhookData** como parámetro para tener acceso a las propiedades de la alerta.  Los datos de la alerta están disponibles en formato json en una sola propiedad denominada **SearchResults** de la propiedad **RequestBody** de **$WebhookData**.  Esta tendrá las propiedades de la siguiente tabla.

| Nodo | Descripción |
|:--- |:--- |
| id |Ruta de acceso y el GUID de la búsqueda. |
| __metadata |Información sobre la alerta, como el número de registros y el estado de los resultados de búsqueda. |
| value |Entrada independiente para cada registro en los resultados de búsqueda.  Los detalles de la entrada coincidirán con las propiedades y los valores del registro. |

Por ejemplo, el siguiente runbook podría extraer los registros devueltos por la búsqueda de registros y asignar propiedades diferentes según el tipo de cada registro.  Tenga en cuenta que el runbook se inicia mediante la conversión de **RequestBody** de json, por lo que puede actuar como un objeto de PowerShell.

    param ( 
        [object]$WebhookData
    )

    $RequestBody = ConvertFrom-JSON -InputObject $WebhookData.RequestBody
    $Records     = $RequestBody.SearchResults.value

    foreach ($Record in $Records)
    {
        $Computer = $Record.Computer

        if ($Record.Type -eq 'Event')
        {
            $EventNo    = $Record.EventID
            $EventLevel = $Record.EventLevelName
            $EventData  = $Record.EventData
        }

        if ($Record.Type -eq 'Perf')
        {
            $Object    = $Record.ObjectName
            $Counter   = $Record.CounterName
            $Instance  = $Record.InstanceName
            $Value     = $Record.CounterValue
        }
    }


## <a name="alert-records"></a>Registros de alerta
Los registros de alerta creados por reglas de alerta en Log Analytics tienen un **Tipo** de **Alerta** y un **SourceSystem** de **OMS**.  Tienen las propiedades de la tabla siguiente.

| Propiedad | Descripción |
|:--- |:--- |
| Tipo |*Alerta* |
| SourceSystem |*OMS* |
| AlertSeverity |Nivel de gravedad de la alerta. |
| AlertName |Nombre de la alerta |
| Consultar |Texto de la consulta que se ha ejecutado. |
| QueryExecutionEndTime |Fin del intervalo de tiempo para la consulta. |
| QueryExecutionStartTime |Inicio del intervalo de tiempo para la consulta. |
| TimeGenerated |Fecha y hora en que se creó la alerta. |

Hay otros tipos de registros de alerta creados por la [solución de Administración de alertas](log-analytics-solution-alert-management.md) y las [exportaciones de Power BI](log-analytics-powerbi.md).  Estos tienen todos un **Tipo** de **Alerta** pero se distinguen por sus valores en **SourceSystem**.

## <a name="next-steps"></a>Pasos siguientes
* Instale la [solución de Administración de alertas](log-analytics-solution-alert-management.md) para analizar las alertas creadas en Log Analytics junto con las alertas recopiladas desde System Center Operations Manager (SCOM).
* Obtenga más información sobre las [búsquedas de registros](log-analytics-log-searches.md) que pueden generar alertas.
* Complete un tutorial para [configurar un webhook](log-analytics-alerts-webhooks.md) con una regla de alerta.  
* Aprenda a escribir [runbooks en Automatización de Azure](https://azure.microsoft.com/documentation/services/automation) para solucionar los problemas identificados por las alertas.




<!--HONumber=Jan17_HO4-->


