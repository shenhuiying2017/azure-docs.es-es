---
title: "Recopilación y análisis de registros de eventos de Windows en Log Analytics (OMS) | Microsoft Docs"
description: "Los registros de eventos de Windows son uno de los orígenes de datos más comunes que usa Log Analytics.  En este artículo se describe cómo configurar la recopilación de registros de eventos de Windows y detalles de los registros que crean en el repositorio de OMS."
services: log-analytics
documentationcenter: 
author: bwren
manager: carmonm
editor: tysonn
ms.assetid: ee52f564-995b-450f-a6ba-0d7b1dac3f32
ms.service: log-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/15/2017
ms.author: bwren
ms.openlocfilehash: 1be8500ec2cb78ef0edf57f4d8561336cf00ebcb
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="windows-event-log-data-sources-in-log-analytics"></a>Orígenes de datos de registros de eventos de Windows en Log Analytics
Muchas aplicaciones escriben en el registro de eventos de Windows, por lo que constituye uno de los [orígenes de datos](log-analytics-data-sources.md) más comunes para recopilar información mediante agentes de Windows.  Puede recopilar eventos de registros estándar, como el sistema y la aplicación, además de especificar cualquier registro personalizado creado por las aplicaciones que debe supervisar.

![Eventos de Windows](media/log-analytics-data-sources-windows-events/overview.png)     

## <a name="configuring-windows-event-logs"></a>Configuración de registros de eventos de Windows
Configure los registros de eventos de Windows en el [menú Datos en Configuración de Log Analytics](log-analytics-data-sources.md#configuring-data-sources).

Log Analytics solo recopila eventos de los registros de eventos de Windows que se especifican en la configuración.  Para agregar un registro nuevo, escriba el nombre y haga clic en **+**.  Para cada registro, solo se recopilan los eventos con los niveles de gravedad seleccionados.  Compruebe las gravedades del registro determinado que desea recopilar.  No puede proporcionar criterios adicionales para filtrar eventos.

A medida que escribe el nombre de un registro de eventos, Log Analytics da sugerencias de nombres comunes de este tipo de registros. Aun cuando el registro que desea agregar no aparezca en la lista, puede incluirlo escribiendo su nombre completo. Encontrará el nombre completo del registro en el Visor de eventos. En el Visor de eventos, abra la página *Propiedades* del registro y copie la cadena del campo *Nombre completo*.

![Configurar eventos de Windows](media/log-analytics-data-sources-windows-events/configure.png)

## <a name="data-collection"></a>Colección de datos
Log Analytics recopila cada evento que coincide con una gravedad seleccionada de un registro de eventos supervisado al crear el evento.  El agente registra su lugar en cada registro de eventos del que recopila entradas.  Si el agente queda sin conexión durante un período, Log Analytics recopila los eventos desde donde quedó, aunque esos eventos se hayan creado mientras el agente estaba sin conexión.  Sin embargo, existe la posibilidad de que estos eventos no se recopilen si el registro de eventos hace que los eventos no recopilados se sobrescriban mientras el agente está sin conexión.

>[!NOTE]
>Log Analytics no recopila eventos de auditoría creados por SQL Server del origen *MSSQLSERVER* con Id. de evento 18453 que contengan palabras clave *Classic* o *Audit Success* y la palabra clave *0xa0000000000000*.
>

## <a name="windows-event-records-properties"></a>Propiedades de los registros de eventos de Windows
Los registros de eventos de Windows tienen un tipo **Event** y las propiedades que aparecen en la tabla siguiente:

| Propiedad | Descripción |
|:--- |:--- |
| Equipo |Nombre del equipo desde el que se recopiló el evento. |
| EventCategory |Categoría del evento. |
| EventData |Todos los datos con formato sin procesar. |
| EventId |El número del evento. |
| EventLevel |La gravedad del evento en formato numérico. |
| EventLevelName |La gravedad del evento en formato de texto. |
| EventLog |El nombre del registro de eventos desde el que se recopiló el evento. |
| ParameterXml |Los valores de parámetro de evento en formato XML. |
| ManagementGroupName |Nombre del grupo de administración de agentes de System Center Operations Manager.  En el caso de los otros agentes, este valor es AOI-<workspace ID>. |
| RenderedDescription |La descripción del evento con valores de parámetro. |
| Origen |El origen del evento. |
| SourceSystem |El tipo de agente desde el que se recopiló el evento. <br> OpsManager: agente de Windows, ya sea una conexión directa o administrado por Operations Manager <br> Linux: todos los agentes de Linux.  <br> AzureStorage: Diagnósticos de Azure |
| TimeGenerated |La fecha y la hora de creación del evento en Windows. |
| UserName |El nombre de usuario de la cuenta que registró el evento. |

## <a name="log-searches-with-windows-events"></a>Búsquedas de registros con eventos de Windows
La tabla siguiente proporciona distintos ejemplos de búsquedas de registros que recuperar registros de eventos de Windows.

| Consultar | Descripción |
|:--- |:--- |
| Type=Event |Todos los eventos de Windows. |
| Type=Event EventLevelName=error |Todos los eventos de Windows con gravedad de error. |
| Type=Event &#124; Measure count() by Source |Contador de eventos de Windows por origen. |
| Type=Event EventLevelName=error &#124; Measure count() by Source |Contador de eventos de error de Windows por origen. |


>[!NOTE]
> Si el área de trabajo se ha actualizado al [nuevo lenguaje de consulta de Log Analytics](log-analytics-log-search-upgrade.md), las consultas anteriores cambiarían como sigue.
>
>| Consultar | Descripción |
|:---|:---|
| Evento |Todos los eventos de Windows. |
| Event &#124; where EventLevelName == "error" |Todos los eventos de Windows con gravedad de error. |
| Event &#124; summarize count() by Source |Contador de eventos de Windows por origen. |
| Event &#124; where EventLevelName == "error" &#124; summarize count() by Source |Contador de eventos de error de Windows por origen. |


## <a name="next-steps"></a>Pasos siguientes
* Configure Log Analytics para recopilar otros [orígenes de datos](log-analytics-data-sources.md) para su análisis.
* Obtenga información sobre las [búsquedas de registros](log-analytics-log-searches.md) para analizar los datos recopilados desde soluciones y orígenes de datos.  
* Use [Campos personalizados](log-analytics-custom-fields.md) para redistribuir los registros de eventos en campos individuales.
* Configure la [recopilación de contadores de rendimiento](log-analytics-data-sources-performance-counters.md) desde los agentes de Windows.
