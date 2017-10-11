---
title: "Solución Capacidad y rendimiento en Azure Log Analytics | Microsoft Docs"
description: "Use la solución Capacidad y rendimiento en Log Analytics para conocer la capacidad de los servidores de Hyper-V."
services: log-analytics
documentationcenter: 
author: bandersmsft
manager: carmonm
editor: 
ms.assetid: 51617a6f-ffdd-4ed2-8b74-1257149ce3d4
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/13/2017
ms.author: banders
ms.openlocfilehash: 5ca005127721092b8efcf0ac83cc967ab15fe72d
ms.sourcegitcommit: 02e69c4a9d17645633357fe3d46677c2ff22c85a
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 08/03/2017
---
# <a name="plan-hyper-v-virtual-machine-capacity-with-the-capacity-and-performance-solution-preview"></a>Con la solución Capacidad y rendimiento (versión preliminar) puede planear la capacidad de máquinas virtuales de Hyper-V.

![Símbolo de capacidad y rendimiento](./media/log-analytics-capacity/capacity-solution.png)

La solución Capacidad y rendimiento se puede usar en Log Analytics para conocer la capacidad de los servidores de Hyper-V. La solución proporciona información sobre el entorno de Hyper-V, ya que muestra el uso general (CPU, memoria y disco) de los hosts y las máquinas virtuales que se ejecutan en esos hosts de Hyper-V. Se recopilan métricas de la CPU, la memoria y los discos en todos los hosts y en las máquinas virtuales que se ejecutan en ellos.

La solución consigue lo siguiente:

-   Muestra los hosts con el uso máximo y mínimo de la CPU y de la memoria
-   Muestra las máquinas virtuales con el uso máximo y mínimo de la CPU y de la memoria
-   Muestra las máquinas virtuales con el uso máximo y mínimo de las IOPS y del rendimiento
-   Muestra qué máquinas virtuales se ejecutan en los hosts
-   Muestra los discos con mayor rendimiento, IOPS y latencia en volúmenes compartidos de clúster
- Permite personalizar y filtrar por grupos

> [!NOTE]
> La versión anterior de la solución Capacidad y rendimiento, denominada Administración de capacidad, requiere System Center Operations Manager y System Center Virtual Machine Manager. Esta solución actualizada no tiene esas dependencias.


## <a name="connected-sources"></a>Orígenes conectados

En la tabla siguiente se describen los orígenes conectados que son compatibles con esta solución.

| Origen conectado | Soporte técnico | Descripción |
|---|---|---|
| [Agentes de Windows](log-analytics-windows-agents.md) | Sí | La solución recopila información de datos de rendimiento y capacidad de los agentes de Windows. |
| [Agentes de Linux](log-analytics-linux-agents.md) | No    | La solución no recopila información de datos de rendimiento y capacidad de los agentes directos de Linux.|
| [Grupo de administración de SCOM](log-analytics-om-agents.md) | Sí |La solución recopila datos de rendimiento y capacidad de los agentes de un grupo de administración de SCOM conectado. No se requiere una conexión directa entre el agente de SCOM y OMS. Los datos se reenvían del grupo de administración al repositorio de OMS.|
| [Cuenta de Azure Storage](log-analytics-azure-storage.md) | No | Azure Storage no incluye datos de capacidad y rendimiento.|

## <a name="prerequisites"></a>Requisitos previos

- Los agentes de Windows o de Operations Manager deben instalarse en hosts de Hyper-V, no máquinas virtuales, con Windows Server 2012, o cualquier versión superior.


## <a name="configuration"></a>Configuración

Realice el siguiente paso para agregar la solución Capacidad y rendimiento a un área de trabajo.

- Agregue la solución Capacidad y rendimiento a un área de trabajo de OMS mediante el proceso que se describe en [Adición de soluciones de administración de Azure Log Analytics al área de trabajo](log-analytics-add-solutions.md).

## <a name="management-packs"></a>Módulos de administración

Si el grupo de administración de SCOM está conectado al área de trabajo de OMS, los módulos de administración siguientes se instalarán en SCOM al agregar esta solución. No es necesario realizar tareas de configuración o mantenimiento de estos módulos de administración.

- Microsoft.IntelligencePacks.CapacityPerformance

El evento 1201 es similar:


```
New Management Pack with id:"Microsoft.IntelligencePacks.CapacityPerformance", version:"1.10.3190.0" received.
```

Cuando se actualice la solución Capacidad y rendimiento, el número de versión cambiará.

Para obtener más información sobre cómo se actualizan los módulos de administración de soluciones, consulte [Conexión de Operations Manager con Log Analytics](log-analytics-om-agents.md).

## <a name="using-the-solution"></a>Uso de la solución

Al agregar la solución Capacidad y rendimiento a un área de trabajo, se agrega al panel Información general. Este icono muestra un recuento del número de hosts de Hyper-V activos actualmente y el número de máquinas virtuales activas que se han supervisado durante el período seleccionado.

![Icono Capacidad y rendimiento](./media/log-analytics-capacity/capacity-tile.png)


### <a name="review-utilization"></a>Revisión de la utilización

Haga clic en el icono Capacidad y rendimiento para abrir el panel Capacidad y rendimiento. El panel incluye las columnas de la tabla siguiente. Cada columna muestra hasta los diez principales elementos que coinciden con los criterios de esa columna para el ámbito e intervalo de tiempo especificados. Puede ejecutar una búsqueda de registros que devuelva todos los registros haciendo clic en **Ver todo** en la parte inferior de la columna o haciendo clic en el encabezado de la columna.

- **Hosts**
    - **Utilización de la CPU del host** Muestra una tendencia gráfica de la utilización de la CPU de los equipos host y una lista de hosts, basándose en el período seleccionado. Mantenga el puntero sobre el gráfico de líneas para ver los detalles de un momento dado concreto. Haga clic en el gráfico para ver más detalles en la búsqueda de registros. Haga clic en cualquier nombre de host para abrir la búsqueda de registros y ver datos del contador de CPU relativos a máquinas virtuales hospedadas.
    - **Utilización de memoria del host** Muestra una tendencia gráfica de la utilización de la memoria de los equipos host y una lista de hosts, basándose en el período seleccionado. Mantenga el puntero sobre el gráfico de líneas para ver los detalles de un momento dado concreto. Haga clic en el gráfico para ver más detalles en la búsqueda de registros. Haga clic en cualquier nombre de host para abrir la búsqueda de registros y ver datos del contador de la memoria relativos a máquinas virtuales hospedadas.
- **Máquinas virtuales**
    - **Utilización de la CPU de VM** Muestra una tendencia gráfica de la utilización de la CPU de las máquinas virtuales y una lista de máquinas virtuales, basándose en el período seleccionado. Mantenga el puntero sobre el gráfico de líneas para ver los detalles de un momento dado concreto de las 3 principales máquinas virtuales. Haga clic en el gráfico para ver más detalles en la búsqueda de registros. Haga clic en cualquier nombre de máquina virtual para abrir la búsqueda de registros y ver detalles agregados del contador de CPU relativos a la máquina virtual.
    - **Utilización de la memoria de VM** Muestra una tendencia gráfica de la utilización de la memoria de las máquinas virtuales y una lista de máquinas virtuales, basándose en el período seleccionado. Mantenga el puntero sobre el gráfico de líneas para ver los detalles de un momento dado concreto de las 3 principales máquinas virtuales. Haga clic en el gráfico para ver más detalles en la búsqueda de registros. Haga clic en cualquier nombre de máquina virtual para abrir la búsqueda de registros y ver detalles agregados del contador de memoria relativos a la máquina virtual.
    - **E/S por segundo de disco total de VM** Muestra una tendencia gráfica de las IOPS de disco totales de las máquinas virtuales y una lista de las máquinas virtuales con las IOPS de cada una de ellas, basándose en el período seleccionado. Mantenga el puntero sobre el gráfico de líneas para ver los detalles de un momento dado concreto de las 3 principales máquinas virtuales. Haga clic en el gráfico para ver más detalles en la búsqueda de registros. Haga clic en cualquier nombre de máquina virtual para abrir la búsqueda de registros y ver detalles agregados del contador de IOPS de disco relativos a la máquina virtual.
    - **Rendimiento de disco total de VM** Muestra una tendencia gráfica del rendimiento de disco total de las máquinas virtuales y una lista de las máquinas virtuales con el rendimiento de disco total de cada una de ellas, basándose en el período seleccionado. Mantenga el puntero sobre el gráfico de líneas para ver los detalles de un momento dado concreto de las 3 principales máquinas virtuales. Haga clic en el gráfico para ver más detalles en la búsqueda de registros. Haga clic en cualquier nombre de máquina virtual para abrir la búsqueda de registros y ver detalles agregados del contador del el rendimiento de disco total relativos a la máquina virtual.
- **Volúmenes compartidos en clúster**
    - **Rendimiento total** Muestra la suma de las operaciones de lectura y de escritura en los volúmenes compartidos en clúster.
    - **Total de E/S por segundo** Muestra la suma de las operaciones de entrada y salida por segundo en los volúmenes compartidos en clúster.
    - **Latencia total** Muestra la latencia total en los volúmenes compartidos en clúster.
- **Densidad de host** El icono superior muestra el número total de hosts y máquinas virtuales disponibles para la solución. Haga clic en el icono superior para ver más detalles en la búsqueda de registros. También se enumeran todos los hosts y el número de máquinas virtuales que se hospedan. Haga clic en un host para profundizar en los resultados de la máquina virtual en una búsqueda de registros.


![Hoja Hosts de panel](./media/log-analytics-capacity/dashboard-hosts.png)

![Hoja Máquinas virtuales de panel](./media/log-analytics-capacity/dashboard-vms.png)


### <a name="evaluate-performance"></a>Evaluación del rendimiento

Los entornos informáticos de producción difieren considerablemente de una organización a otra. Además, la capacidad y las cargas de trabajo de rendimiento pueden depender del funcionamiento de las máquinas virtuales y de lo que se considere normal. Es probable que los procedimientos concretos que le ayudan a medir el rendimiento no se apliquen a su entorno. Por consiguiente, es mejor usar instrucciones más generales. Microsoft publica varios artículos con instrucciones que pueden ayudarle a medir el rendimiento.

En resumen, la solución recopila datos de capacidad y rendimiento de varios orígenes, entre los que se incluyen los contadores de rendimiento. Use los datos de capacidad y rendimiento que se presentan en distintas superficies en la solución y compare los resultados con los del artículo [Measuring Performance on Hyper-V](https://msdn.microsoft.com/library/cc768535.aspx) (Medición del rendimiento en Hyper-V). Aunque el artículo se publicó hace un tiempo, las métricas, consideraciones y directrices siguen teniendo la validez. El artículo contiene vínculos a otros recursos útiles.


## <a name="sample-log-searches"></a>Búsquedas de registros de ejemplo

En la tabla siguiente se proporcionan búsquedas de registros de ejemplo en los datos de capacidad y rendimiento que ha recopilado y calculado esta solución.

| Consultar | Descripción |
|---|---|
| Todas las configuraciones de memoria del host | <code>Type=Perf ObjectName="Capacity and Performance" CounterName="Host Assigned Memory MB" &#124; measure avg(CounterValue) as MB by InstanceName</code> |
| Todas las configuraciones de memoria de la máquina virtual | <code>Type=Perf ObjectName="Capacity and Performance" CounterName="VM Assigned Memory MB" &#124; measure avg(CounterValue) as MB by InstanceName</code> |
| Desglose de E/S por segundo total del disco en todas las máquinas virtuales | <code>Type=Perf ObjectName="Capacity and Performance" (CounterName="VHD Reads/s" OR CounterName="VHD Writes/s") &#124; top 2500 &#124; measure avg(CounterValue) by CounterName, InstanceName interval 1HOUR</code> |
| Desglose del rendimiento total del disco en todas las máquinas virtuales | <code>Type=Perf ObjectName="Capacity and Performance" (CounterName="VHD Read MB/s" OR CounterName="VHD Write MB/s") &#124; top 2500 &#124; measure avg(CounterValue) by CounterName, InstanceName interval 1HOUR</code> |
| Desglose de E/S por segundo total en todos los CSV | <code>Type=Perf ObjectName="Capacity and Performance" (CounterName="CSV Reads/s" OR CounterName="CSV Writes/s") &#124; top 2500 &#124; measure avg(CounterValue) by CounterName, InstanceName interval 1HOUR</code> |
| Desglose del rendimiento total del disco en todos los CSV | <code>Type=Perf ObjectName="Capacity and Performance" (CounterName="CSV Read MB/s" OR CounterName="CSV Write MB/s") &#124; top 2500 &#124; measure avg(CounterValue) by CounterName, InstanceName interval 1HOUR</code> |
| Desglose de la latencia total en todos los CSV | <code> Type=Perf ObjectName="Capacity and Performance" (CounterName="CSV Read Latency" OR CounterName="CSV Write Latency") &#124; top 2500 &#124; measure avg(CounterValue) by CounterName, InstanceName interval 1HOUR</code> |

>[!NOTE]
> Si el área de trabajo se ha actualizado al [nuevo lenguaje de consulta Log Analytics](log-analytics-log-search-upgrade.md), las consultas anteriores cambiarían como sigue.

> | Consultar | Descripción |
|:--- |:--- |
| Todas las configuraciones de memoria del host | Perf &#124; where ObjectName == "Capacity and Performance" and CounterName == "Host Assigned Memory MB" &#124; summarize MB = avg(CounterValue) by InstanceName |
| Todas las configuraciones de memoria de la máquina virtual | Perf &#124; where ObjectName == "Capacity and Performance" and CounterName == "VM Assigned Memory MB" &#124; summarize MB = avg(CounterValue) by InstanceName |
| Desglose de E/S por segundo total del disco en todas las máquinas virtuales | Perf &#124; where ObjectName == "Capacity and Performance" and (CounterName == "VHD Reads/s" or CounterName == "VHD Writes/s") &#124; summarize AggregatedValue = avg(CounterValue) by bin(TimeGenerated, 1h), CounterName, InstanceName |
| Desglose del rendimiento total del disco en todas las máquinas virtuales | Perf &#124; where ObjectName == "Capacity and Performance" and (CounterName == "VHD Read MB/s" or CounterName == "VHD Write MB/s") &#124; summarize AggregatedValue = avg(CounterValue) by bin(TimeGenerated, 1h), CounterName, InstanceName |
| Desglose de E/S por segundo total en todos los CSV | Perf &#124; where ObjectName == "Capacity and Performance" and (CounterName == "CSV Reads/s" or CounterName == "CSV Writes/s") &#124; summarize AggregatedValue = avg(CounterValue) by bin(TimeGenerated, 1h), CounterName, InstanceName |
| Desglose del rendimiento total del disco en todos los CSV | Perf &#124; where ObjectName == "Capacity and Performance" and (CounterName == "CSV Reads/s" or CounterName == "CSV Writes/s") &#124; summarize AggregatedValue = avg(CounterValue) by bin(TimeGenerated, 1h), CounterName, InstanceName |
| Desglose de la latencia total en todos los CSV | Perf &#124; where ObjectName == "Capacity and Performance" and (CounterName == "CSV Read Latency" or CounterName == "CSV Write Latency") &#124; summarize AggregatedValue = avg(CounterValue) by bin(TimeGenerated, 1h), CounterName, InstanceName |


## <a name="next-steps"></a>Pasos siguientes
* Use [Búsqueda de datos mediante búsquedas de registros](log-analytics-log-searches.md) para ver datos detallados de Capacidad y rendimiento.
