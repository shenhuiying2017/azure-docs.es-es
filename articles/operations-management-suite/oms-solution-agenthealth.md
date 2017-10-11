---
title: "Solución Agent Health en OMS | Microsoft Docs"
description: "Este artículo está pensado para ayudarle a entender cómo usar esta solución para supervisar el mantenimiento de los agentes que informan directamente a OMS o System Center Operations Manager."
services: operations-management-suite
documentationcenter: 
author: MGoedtel
manager: carmonm
editor: 
ms.assetid: 
ms.service: operations-management-suite
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 07/17/2017
ms.author: magoedte
ms.openlocfilehash: b810e37e393ddab55500f636b72450789285a4f0
ms.sourcegitcommit: 02e69c4a9d17645633357fe3d46677c2ff22c85a
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 08/03/2017
---
#  <a name="agent-health-solution-in-oms"></a>Solución Agent Health en OMS
La solución Agent Health en OMS le ayuda a entender, para todos los agentes que informan directamente al área de trabajo de OMS o a un grupo de administración de System Center Operations Manager conectado a OMS, cuáles no responden y envían datos operativos.  También puede realizar un seguimiento del número de agentes que se implementan, dónde están distribuidos geográficamente y llevar a cabo otras consultas para mantener el conocimiento de la distribución de los agentes implementados en Azure, en otros entornos de nube o en un entorno local.    

## <a name="prerequisites"></a>Requisitos previos
Antes de implementar esta solución, confirme que tiene actualmente [agentes de Windows](../log-analytics/log-analytics-windows-agents.md) compatibles que informan al área de trabajo de OMS o a un [grupo de administración de Operations Manager](../log-analytics/log-analytics-om-agents.md) integrado con el área de trabajo de OMS.    

## <a name="solution-components"></a>Componentes de soluciones
Esta solución consta de los siguientes recursos que se agregan a su área de trabajo y a los agentes directamente conectados o al grupo de administración conectado de Operations Manager.

### <a name="management-packs"></a>Módulos de administración
Si el grupo de administración de System Center Operations Manager está conectado al área de trabajo de OMS, se instalarán los siguientes módulos de administración en Operations Manager.  Estos módulos de administración también se instalan en equipos Windows directamente conectados después de agregar esta solución. No hay nada para configurar o administrar en estos módulos de administración.

* Intelligence Pack HealthAssessment Direct Channel de Microsoft System Center Advisor (Microsoft.IntelligencePacks.HealthAssessmentDirect)
* Intelligence Pack HealthAssessment Server Channel de Microsoft System Center Advisor (Microsoft.IntelligencePacks.HealthAssessmentViaServer)  

Para obtener más información sobre cómo se actualizan los módulos de administración de soluciones, consulte [Conexión de Operations Manager con Log Analytics](../log-analytics/log-analytics-om-agents.md).

## <a name="configuration"></a>Configuración
Agregue la solución Agent Health al área de trabajo de OMS mediante el proceso descrito en [Incorporación de soluciones](../log-analytics/log-analytics-add-solutions.md). No es necesario realizar ninguna configuración más.


## <a name="data-collection"></a>Colección de datos
### <a name="supported-agents"></a>Agentes admitidos
En la tabla siguiente se describen los orígenes conectados que son compatibles con esta solución.

| Origen conectado | Compatible | Description |
| --- | --- | --- |
| Agentes de Windows | Sí | Se recopilan eventos de latido de agentes directos de Windows.|
| Grupo de administración de System Center Operations Manager | Sí | Se recopilan eventos de latido de agentes que informan al grupo de administración cada 60 segundos y después se reenvían a Log Analytics. No se requiere ninguna conexión directa entre los agentes de Operations Manager y Log Analytics. Los datos de eventos de latido se reenvían desde el grupo de administración al repositorio de Log Analytics.|

## <a name="using-the-solution"></a>Uso de la solución
Al agregar la solución al área de trabajo de OMS, se agrega el icono **Agent Health** al panel de OMS. Este icono muestra el número total de agentes y el número de agentes que no responden en las últimas 24 horas.<br><br> ![Icono de la solución Agent Health en el panel](./media/oms-solution-agenthealth/agenthealth-solution-tile-homepage.png)

Haga clic en el icono **Agent Health** para abrir el panel **Agent Health**.  El panel incluye las columnas de la tabla siguiente. Cada columna muestra los diez principales eventos por recuento que coinciden con los criterios de esa columna para el intervalo de tiempo especificado. Puede ejecutar una búsqueda de registros que proporcione toda la lista si selecciona **Ver todo** en la parte inferior derecha de la columna o hace clic en el encabezado de columna.

| Columna | Descripción |
|--------|-------------|
| Agent count over time (Número de agentes a lo largo del tiempo) | Una tendencia del número de agentes durante un período de siete días para agentes de Linux y Windows.|
| Count of unresponsive agents (Número de agentes que no responden) | Una lista de agentes que no ha enviado ningún latido en las últimas 24 horas.|
| Distribution by OS Type (Distribución por tipo de sistema operativo) | Una división de cuántos agentes de Windows y Linux tiene en su entorno.|
| Distribution by Agent Version (Distribución por versión del agente) | Una división de las diferentes versiones de agente instaladas en su entorno y el número de cada una de ellas.|
| Distribution by Agent Category (Distribución por categoría del agente) | Una división de las diferentes categorías de agentes que envían eventos de latido: agentes directos, agentes de OpsMgr o el servidor de administración de OpsMgr.|
| Distribution by Management Group (Distribución por grupo de administración) | Una división de los diferentes grupos de administración de SCOM en su entorno.|
| Geo-location of Agents (Geolocalización de los agentes) | Una división de los diferentes países donde haya agentes y el número total de agentes instalados en cada país.|
| Count of Gateways Installed (Número de puertas de enlace instaladas) | El número de servidores que tienen instalada la puerta de enlace de OMS y una lista de estos servidores.|

![Ejemplo de panel de la solución Agent Health](./media/oms-solution-agenthealth/agenthealth-solution-dashboard.png)  

## <a name="log-analytics-records"></a>Registros de Log Analytics
La solución crea un tipo de registro en el repositorio de OMS.  

### <a name="heartbeat-records"></a>Registros de latidos
Se crea un registro del tipo **Heartbeat**.  Estos registros tienen las propiedades de la tabla siguiente.  

| Propiedad | Descripción |
| --- | --- |
| Tipo | *Heartbeat*|
| Categoría | El valor es *Direct Agent*, *SCOM Agent* o *SCOM Management Server*.|
| Equipo | Nombre del equipo.|
| OSType | Sistema operativo Windows o Linux.|
| OSMajorVersion | Versión principal del sistema operativo.|
| OSMinorVersion | Versión secundaria del sistema operativo.|
| Versión | Versión del agente de OMS o de Operations Manager.|
| SCAgentChannel | El valor es *Direct* o *SCManagementServer*.|
| IsGatewayInstalled | Si está instalada la puerta de enlace de OMS, el valor es *true*; en caso contrario, es *false*.|
| ComputerIP | Dirección IP del equipo.|
| RemoteIPCountry | Ubicación geográfica donde el equipo está implementado.|
| ManagementGroupName | Nombre del grupo de administración de Operations Manager.|
| SourceComputerId | Identificador único del equipo.|
| RemoteIPLongitude | Longitud de la ubicación geográfica del equipo.|
| RemoteIPLatitude | Latitud de la ubicación geográfica del equipo.|

Cada agente que informa a un servidor de administración de Operations Manager enviará dos latidos y el valor de la propiedad SCAgentChannel incluirá tanto **Direct** como **SCManagementServer**, en función de qué soluciones y orígenes de datos de Log Analytics haya habilitado en su suscripción de OMS. Si recuerda, los datos de soluciones se envían directamente desde un servidor de administración de Operations Manager al servicio web de OMS, o bien, debido al volumen de los datos recopilados en el agente, se envían directamente desde el agente al servicio web de OMS. Para los eventos de latido que tienen el valor **SCManagementServer**, el valor de ComputerIP es la dirección IP del servidor de administración, ya que es el que realmente carga los datos.  Para los latidos donde SCAgentChannel esté establecido en **Direct**, es la dirección IP pública del agente.  

## <a name="sample-log-searches"></a>Búsquedas de registros de ejemplo
En la tabla siguiente se proporcionan búsquedas de registros de ejemplo para los registros recopilados por esta solución.

| Consultar | Descripción |
| --- | --- |
| Type=Heartbeat &#124; distinct Computer |Número total de agentes |
| Type=Heartbeat &#124; measure max(TimeGenerated) as LastCall by Computer &#124; where LastCall < NOW-24HOURS |Número de agentes que no responden en las últimas 24 horas |
| Type=Heartbeat &#124; measure max(TimeGenerated) as LastCall by Computer &#124; where LastCall < NOW-15MINUTES |Número de agentes que no responden en los últimos 15 minutos |
| Type=Heartbeat TimeGenerated>NOW-24HOURS Computer IN {Type=Heartbeat TimeGenerated>NOW-24HOURS &#124; distinct Computer} &#124; measure max(TimeGenerated) as LastCall by Computer |Equipos en línea (en las últimas 24 horas) |
| Type=Heartbeat TimeGenerated>NOW-24HOURS Computer NOT IN {Type=Heartbeat TimeGenerated>NOW-30MINUTES &#124; distinct Computer} &#124; measure max(TimeGenerated) as LastCall by Computer |Total de agentes sin conexión en los últimos 30 minutos (para las últimas 24 horas) |
| Type=Heartbeat &#124; measure countdistinct(Computer) by OSType |Obtener una tendencia de número de agentes a lo largo del tiempo por tipo de sistema operativo|
| Type=Heartbeat&#124;measure countdistinct(Computer) by OSType |Distribution by OS Type (Distribución por tipo de sistema operativo) |
| Type=Heartbeat&#124;measure countdistinct(Computer) by Version |Distribution by Agent Version (Distribución por versión del agente) |
| Type=Heartbeat&#124;measure count() by Category |Distribution by Agent Category (Distribución por categoría del agente) |
| Type=Heartbeat&#124;measure countdistinct(Computer) by ManagementGroupName | Distribution by Management Group (Distribución por grupo de administración) |
| Type=Heartbeat&#124;measure countdistinct(Computer) by RemoteIPCountry |Geo-location of Agents (Geolocalización de los agentes) |
| Type=Heartbeat IsGatewayInstalled=true&#124;Distinct Computer |Número de puertas de enlace de OMS instaladas |


>[!NOTE]
> Si el área de trabajo se ha actualizado al [nuevo lenguaje de consulta Log Analytics](../log-analytics/log-analytics-log-search-upgrade.md), las consultas anteriores cambiarían como sigue.
>
>| Consultar | Descripción |
|:---|:---|
| Heartbeat &#124; distinct Computer |Número total de agentes |
| Heartbeat &#124; summarize LastCall = max(TimeGenerated) by Computer &#124; where LastCall < ago(24h) |Número de agentes que no responden en las últimas 24 horas |
| Heartbeat &#124; summarize LastCall = max(TimeGenerated) by Computer &#124; where LastCall < ago(15m) |Número de agentes que no responden en los últimos 15 minutos |
| Heartbeat &#124; where TimeGenerated > ago(24h) and Computer in ((Heartbeat &#124; where TimeGenerated > ago(24h) &#124; distinct Computer)) &#124; summarize LastCall = max(TimeGenerated) by Computer |Equipos en línea (en las últimas 24 horas) |
| Heartbeat &#124; where TimeGenerated > ago(24h) and Computer !in ((Heartbeat &#124; where TimeGenerated > ago(30m) &#124; distinct Computer)) &#124; summarize LastCall = max(TimeGenerated) by Computer |Total de agentes sin conexión en los últimos 30 minutos (para las últimas 24 horas) |
| Heartbeat &#124; summarize AggregatedValue = dcount(Computer) by OSType |Obtener una tendencia de número de agentes a lo largo del tiempo por tipo de sistema operativo|
| Heartbeat &#124; summarize AggregatedValue = dcount(Computer) by OSType |Distribution by OS Type (Distribución por tipo de sistema operativo) |
| Heartbeat &#124; summarize AggregatedValue = dcount(Computer) by Version |Distribution by Agent Version (Distribución por versión del agente) |
| Heartbeat &#124; summarize AggregatedValue = count() by Category |Distribution by Agent Category (Distribución por categoría del agente) |
| Heartbeat &#124; summarize AggregatedValue = dcount(Computer) by ManagementGroupName | Distribution by Management Group (Distribución por grupo de administración) |
| Heartbeat &#124; summarize AggregatedValue = dcount(Computer) by RemoteIPCountry |Geo-location of Agents (Geolocalización de los agentes) |
| Heartbeat &#124; where iff(isnotnull(toint(IsGatewayInstalled)), IsGatewayInstalled == true, IsGatewayInstalled == "true") == true &#124; distinct Computer |Número de puertas de enlace de OMS instaladas |

## <a name="next-steps"></a>Pasos siguientes

* Obtenga información sobre [alertas en Log Analytics](../log-analytics/log-analytics-alerts.md) para más detalles sobre la generación de alertas desde Log Analytics.
