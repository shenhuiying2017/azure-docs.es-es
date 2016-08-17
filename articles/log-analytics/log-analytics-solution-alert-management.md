<properties 
   pageTitle="Solución Administración de alertas en Log Analytics | Microsoft Azure"
   description="La solución Administración de alertas de Log Analytics le ayuda a analizar todas las alertas de su entorno. Además de consolidar las alertas generadas dentro de OMS, importa las alertas de los grupos de administración de System Center Operations Manager (SCOM) conectados en Log Analytics."
   services="log-analytics"
   documentationCenter=""
   authors="bwren"
   manager="jwhit"
   editor="tysonn" />
<tags 
   ms.service="log-analytics"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="05/12/2016"
   ms.author="bwren" />

# Solución Administración de alertas en Log Analytics

La solución Administración de alertas le ayuda a analizar todas las alertas de su entorno. Además de consolidar las alertas generadas dentro de OMS, importa las alertas de los grupos de administración de System Center Operations Manager (SCOM) conectados en Log Analytics. En entornos con varios grupos de administración, la solución Administración de alertas proporcionará una vista consolidada de alertas en todos los grupos de administración.

![Icono Administración de alertas](media/log-analytics-solution-alert-management/icon.png)

## Requisitos previos

- Para importar alertas desde SCOM, esta solución requiere una conexión entre el área de trabajo OMS y un grupo de administración de SCOM mediante el proceso descrito en [Conexión de Operations Manager con Log Analytics](log-analytics-om-agents.md).  


## Configuración

Agregue la solución Administración de alertas al área de trabajo de OMS mediante el proceso descrito en [Incorporación de soluciones](log-analytics-add-solutions.md). No es necesario realizar ninguna configuración más.

## Módulos de administración

Si el grupo de administración de SCOM está conectado al área de trabajo de OMS, los módulos de administración siguientes se instalarán en SCOM al agregar esta solución. No es necesario realizar tareas de configuración o mantenimiento de estos módulos de administración.

- Administración de alertas de Microsoft System Center Advisor (Microsoft.IntelligencePacks.AlertManagement)

Para obtener más información sobre cómo se actualizan los módulos de administración de soluciones, consulte [Conexión de Operations Manager con Log Analytics](log-analytics-om-agents.md).

## Colección de datos

### Agentes

En la tabla siguiente se describen los orígenes conectados que son compatibles con esta solución.

| Origen conectado | Soporte técnico | Descripción |
|:--|:--|:--|
| [Agentes de Windows](log-analytics-windows-agents.md) | No | Los agentes directos de Windows no generan alertas de SCOM. |
| [Agentes de Linux](log-analytics-limux-agents.md) | No | Los agentes directos de Linux no generan alertas de SCOM. |
| [Grupo de administración de SCOM](log-analytics-om-agents.md) | Sí | Las alertas que se generan en agentes de SCOM se entregan al grupo de administración y luego se reenvían a Log Analytics.<br><br>No se requiere una conexión directa entre el agente de SCOM y Log Analytics. Los datos de alerta se reenvían desde el grupo de administración al repositorio de OMS. |
| [Cuenta de Almacenamiento de Azure](log-analytics-azure-storage.md) | No | Las alertas de SCOM no se almacenan en cuentas de almacenamiento de Azure. |

### Frecuencia de recopilación

Las alertas generadas dentro de OMS están disponibles para la solución inmediatamente. Los datos de alerta se envían desde el grupo de administración de SCOM a Log Analytics cada 3 minutos.

## Uso de la solución

Al agregar la solución Administración de alertas al área de trabajo de OMS, se agrega el icono **Administración de alertas** al panel de OMS. Este icono muestra un recuento y una representación gráfica del número de alertas actualmente activas que se generaron en las últimas 24 horas. Este intervalo de tiempo no se puede cambiar.

![Icono Administración de alertas](media/log-analytics-solution-alert-management/tile.png)

Haga clic en el icono **Administración de alertas** para abrir el panel **Administración de alertas**. El panel incluye las columnas de la tabla siguiente. Cada columna muestra las diez principales alertas por recuento que coinciden con los criterios de esa columna para el ámbito e intervalo de tiempo especificados. Puede ejecutar una búsqueda de registros que proporcione toda la lista haciendo clic en **Ver todo** en la parte inferior de la columna o haciendo clic en el encabezado de columna.

| Columna| Descripción |
|:--|:--|
| Alertas críticas | Todas las alertas con una gravedad crítica agrupadas por nombre de alerta. Haga clic en un nombre de la alerta para ejecutar una búsqueda de registros que devuelva todos los registros de esa alerta. |
| Alertas de advertencia | Todas las alertas con una gravedad de advertencia agrupadas por nombre de alerta. Haga clic en un nombre de la alerta para ejecutar una búsqueda de registros que devuelva todos los registros de esa alerta. |
| Alertas de SCOM activas | Todas las alertas de SCOM con cualquier estado distinto de *Cerrado* agrupadas por el origen que generó la alerta. |
| Todas las alertas activas | Todas las alertas con cualquier gravedad agrupadas por nombre de alerta. Solo incluye las alertas de SCOM con cualquier estado distinto de *Cerrado*.|

Si se desplaza a la derecha, el panel mostrará varias consultas comunes en las que puede hacer clic para realizar un [búsqueda de registros](log-analytics-log-searches.md) para datos de alertas.

![Panel Administración de alertas](media/log-analytics-solution-alert-management/dashboard.png)

## Ámbito e intervalo de tiempo

De forma predeterminada, el ámbito de las alertas que se analizan en la solución Administración de alertas es de todos los grupos de administración conectados generadas en los últimos 7 días.

![Ámbito Administración de alertas](media/log-analytics-solution-alert-management/scope.png)

- Para cambiar los grupos de administración incluidos en el análisis, haga clic en **Ámbito** en la parte superior del panel. Puede seleccionar una opción **Global** para todos los grupos de administración conectados o **By Management Group** (Por grupo de administración) para seleccionar un solo grupo de administración.

- Para cambiar el intervalo de tiempo de las alertas, seleccione **Data based on** (Datos basados en) en la parte superior del panel. Puede seleccionar las alertas generadas en los últimos 7 días, 1 día o 6 horas. O puede seleccionar **Personalizado** y especificar un intervalo de fechas personalizado.

## Registros de Log Analytics

La solución Administración de alertas analiza todos los registros con un tipo de **Alerta**. También importará alertas desde SCOM y creará un registro correspondiente para cada una con un tipo de **Alerta** y un SourceSystem de **OpsManager**. Estos registros tienen las propiedades de la tabla siguiente.

| Propiedad | Descripción |
|:--|:--|
| Tipo | *Alerta* |
| SourceSystem | *OpsManager* |
| AlertContext | Detalles del elemento de datos que provocó la alerta generados en formato XML. |
| AlertDescription | Descripción detallada de la alerta. |
| AlertId | GUID de la alerta. |
| AlertName | Nombre de la alerta |
| AlertPriority | Nivel de prioridad de la alerta. |
| AlertSeverity | Nivel de gravedad de la alerta. |
| AlertState | Estado de resolución más reciente de la alerta. |
| LastModifiedBy | Nombre del usuario que modificó por última vez la alerta. |
| ManagementGroupName | Nombre del grupo de administración donde se generó la alerta. |
| RepeatCount | Número de veces que se generó la misma alerta para el mismo objeto supervisado desde que se resolvió. |
| ResolvedBy | Nombre del usuario que resolvió la alerta. Vacío si la alerta todavía no se ha resuelto. |
| SourceDisplayName | Nombre para mostrar del objeto de supervisión que generó la alerta. |
| SourceFullName | Nombre completo del objeto de supervisión que generó la alerta. |
| TicketId | Id. de vale para la alerta si el entorno de SCOM está integrado con un proceso para asignar vales para las alertas. Vacío si no se asigna ningún vale. |
| TimeGenerated | Fecha y hora en que se creó la alerta. |
| TimeLastModified | Fecha y hora en que se cambió la alerta. |
| TimeRaised | Fecha y hora en que se generó la alerta. |
| TimeResolved | Fecha y hora en que se resolvió la alerta. Vacío si la alerta todavía no se ha resuelto. |

## Búsquedas de registros de ejemplo

La tabla siguiente proporciona búsquedas de registros de ejemplo para los registros de alerta recopilados por esta solución.

| Consultar | Descripción |
|:--|:--|
| Type=Alert SourceSystem=OpsManager AlertSeverity=error TimeRaised>NOW-24HOUR | Alertas críticas generadas durante las últimas 24 horas |
| Type=Alert AlertSeverity=warning TimeRaised>NOW-24HOUR | Alertas de advertencia generadas durante las últimas 24 horas |
| Type=Alert SourceSystem=OpsManager AlertState!=Closed TimeRaised>NOW-24HOUR &#124; measure count() as Count by SourceDisplayName | Orígenes con alertas activas generadas durante las últimas 24 horas |
| Type=Alert SourceSystem=OpsManager AlertSeverity=error TimeRaised>NOW-24HOUR AlertState!=Closed | Alertas críticas generadas durante las últimas 24 horas que todavía están activas |
| Type=Alert SourceSystem=OpsManager TimeRaised>NOW-24HOUR AlertState=Closed | Alertas generadas durante las últimas 24 horas que ahora están cerradas |
| Type=Alert SourceSystem=OpsManager TimeRaised>NOW-1DAY &#124; measure count() as Count by AlertSeverity | Alertas generadas durante el último día agrupadas por su gravedad |
| Type=Alert SourceSystem=OpsManager TimeRaised>NOW-1DAY &#124; sort RepeatCount desc | Alertas generadas durante el último día ordenadas por su valor de número de repeticiones |

## Pasos siguientes

- Obtenga información sobre [alertas en Log Analytics](log-analytics-alerts.md) para más detalles sobre la generación de alertas desde Log Analytics. 

<!---HONumber=AcomDC_0518_2016-->