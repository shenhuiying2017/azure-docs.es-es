<properties 
   pageTitle="Contadores de rendimiento de Windows y Linux en Log Analytics | Microsoft Azure"
   description="Log Analytics recopila contadores de rendimiento para analizar el rendimiento de los agentes de Windows y Linux.  En este artículo se describe cómo configurar la colección de contadores de rendimiento de los agentes de Windows y Linux, se proporcionan detalles dela ubicación en que se almacenan en el repositorio de OMS y se indica cómo analizarlos en el portal de OMS."
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
   ms.date="08/09/2016"
   ms.author="bwren" />


# <a name="windows-and-linux-performance-data-sources-in-log-analytics"></a>Orígenes de datos de rendimiento de Windows y Linux en Log Analytics 

Los contadores de rendimiento de Windows y Linux ofrecen información acerca del rendimiento de los componentes de hardware, los sistemas operativos y las aplicaciones.  Log Analytics puede recopilar contadores de rendimiento a intervalos frecuentes para el análisis casi en tiempo real (NRT), además de agregar datos de rendimiento para el análisis a más largo plazo y la creación de informes.

![Contadores de rendimiento](media/log-analytics-data-sources-performance-counters/overview.png)

## <a name="configuring-performance-counters"></a>Configuración de contadores de rendimiento

Los contadores de rendimiento se configuran en el [menú Datos en Configuración de Log Analytics](log-analytics-data-sources.md#configuring-data-sources).

La primera vez que se configuran los contadores de rendimiento de Windows o Linux para un área de trabajo de OMS nueva, se proporciona la opción de crear rápidamente varios contadores comunes.  Se muestran todos con una casilla junto a cada uno.  Asegúrese de que están marcados todos los contadores que desea crear inicialmente y, luego, haga clic en **Add the selected performance counters**(Agregar los contadores de rendimiento seleccionados).

![Configuración de contadores de rendimiento de Windows](media/log-analytics-data-sources-performance-counters/configure-windows.png)

Siga este procedimiento para agregar un nuevo contador de rendimiento de Windows para recopilar.

1. Escriba el nombre del contador en el cuadro de texto con el formato *objeto(instancia)\contador*.  Cuando empiece a escribir, aparece una lista de contadores comunes coincidentes.  Puede seleccionar un contador de la lista o escribir uno propio.  También puede devolver todas las instancias de un contador determinado, para lo que debe especificar *objeto\contador*. 
2. Haga clic en **+** o presione **ENTRAR** para agregar el contador a la lista.
3. Cuando se agrega un contador, se usa el valor predeterminado de 10 segundos en **Intervalo de ejemplo**.  Este valor se puede cambiar por otro mayor, siempre que no supere los 1800 segundos (30 minutos), en caso de que se desee reducir los requisitos de almacenamiento de los datos de rendimiento recopilados.
4. Cuando haya terminado de agregar contadores, haga clic en el botón **Guardar** de la parte superior de la pantalla para guardar la configuración.

![Configuración de contadores de rendimiento de Linux](media/log-analytics-data-sources-performance-counters/configure-linux.png)

Siga este procedimiento para agregar un nuevo contador de rendimiento de Linux para recopilar.

1. De forma predeterminada, todos los cambios realizados en la configuración se insertan automáticamente en todos los agentes.  En el caso de los agentes de Linux, se envía un archivo de configuración al recopilador de datos Fluentd.  Si desea modificar este archivo manualmente en cada uno de los agentes de Linux, desactive la casilla *Apply below configuration to my Linux machines*(Aplicar la configuración siguiente a mis equipos con Linux).
2. Escriba el nombre del contador en el cuadro de texto con el formato *objeto(instancia)\contador*.  Cuando empiece a escribir, aparece una lista de contadores comunes coincidentes.  Puede seleccionar un contador de la lista o escribir uno propio.  
2. Haga clic en **+** o presione **ENTRAR** para agregar el contador a la lista de contadores del objeto.
3. Todos los contadores de un objeto usan el mismo valor en **Intervalo de ejemplo**.  El valor predeterminado es 10 segundos.  Este valor se puede cambiar por otro mayor, siempre que no supere los 1800 segundos (30 minutos), en caso de que se desee reducir los requisitos de almacenamiento de los datos de rendimiento recopilados.
4. Cuando haya terminado de agregar contadores, haga clic en el botón **Guardar** de la parte superior de la pantalla para guardar la configuración.

## <a name="data-collection"></a>Colección de datos

Log Analytics recopila todos los contadores de rendimiento especificados en su intervalo de ejemplo en todos los agentes que tengan dicho contador instalado.  Los datos no se agregan; los datos sin procesar están disponibles en todas las vistas de búsqueda de registro durante el tiempo especificado por la suscripción de OMS.


## <a name="performance-record-properties"></a>Propiedades de registros de rendimiento

Los registros de rendimiento tienen el tipo **Perf** y sus propiedades son las que aparecen en la tabla siguiente.

| Propiedad | Descripción |
|:--|:--|
| Equipo         | Nombre del equipo desde el que se recopiló el evento. |
| CounterName      | Nombre del contador de rendimiento. |
| CounterPath      | Ruta de acceso completa del contador en el formato \\\\\<Equipo>\\objeto(instancia)\\contador. |
| CounterValue     | Valor numérico del contador.  |
| InstanceName     | Nombre de la instancia del evento.  Vacío si no hay instancias. |
| ObjectName       | Nombre del objeto de rendimiento |
| SourceSystem  | Tipo de agente del que se recopilaron los datos. <br> OpsManager: agente de Windows, ya sea una conexión directa o SCOM <br>  Linux: todos los agentes de Linux.  <br>  AzureStorage: Diagnósticos de Azure |
| TimeGenerated       | Fecha y hora en que se toma la muestra de datos. |


## <a name="sizing-estimates"></a>Estimaciones de tamaño

 Una estimación aproximada para la recopilación de un contador determinado a intervalos de 10 segundos es de aproximadamente 1 MB por día y por instancia.  Los requisitos de almacenamiento de un contador se pueden calcular con la siguiente fórmula.

    1 MB x (number of counters) x (number of agents) x (number of instances)

## <a name="log-searches-with-performance-records"></a>Búsquedas de registros con registros de rendimiento

La tabla siguiente proporciona distintos ejemplos de búsquedas de registros que recuperan registros de rendimiento.

| Consultar | Descripción |
|:--|:--|
| Type=Perf | Todos los datos de rendimiento |
| Type=Perf Computer="MyComputer" | Todos los datos de rendimiento de un equipo concreto |
| Type=Perf CounterName="Current Disk Queue Length" | Todos los datos de rendimiento de un contador concreto |
| Type=Perf (ObjectName=Processor) CounterName="% Processor Time" InstanceName=_Total &#124; measure Avg(Average) as AVGCPU  by Computer | Uso medio de CPU en todos los equipos |
| Type=Perf (CounterName="% Processor Time") &#124;  measure max(Max) by Computer | Uso máximo de CPU en todos los equipos |
| Type=Perf ObjectName=LogicalDisk CounterName="Current Disk Queue Length" Computer="MyComputerName" &#124; measure Avg(Average) by InstanceName | Longitud media de cola de disco actual en todas las instancias de un equipo dado |
| Type=Perf CounterName="DiskTransfers/sec" &#124; measure percentile95(Average) by Computer | Percentil 95 de transferencias de disco por segundo en todos los equipos |
| Type=Perf CounterName="% Processor Time" InstanceName="_Total"  &#124; measure avg(CounterValue) by Computer Interval 1HOUR | Promedio por hora de uso de CPU en todos los equipos |
| Type=Perf Computer="MyComputer" CounterName=%* InstanceName=_Total &#124; measure percentile70(CounterValue) by CounterName Interval 1HOUR | Percentil 70 por hora de cada contador de porcentaje % para un equipo concreto |
| Type=Perf CounterName="% Processor Time" InstanceName="_Total"  (Computer="MyComputer") &#124; measure min(CounterValue), avg(CounterValue), percentile75(CounterValue), max(CounterValue) by Computer Interval 1HOUR | Promedio, mínimo, máximo y percentil 75 por hora de uso de CPU de un equipo específico |

## <a name="viewing-performance-data"></a>Visualización de datos de rendimiento

Cuando se ejecuta una búsqueda del registro de los datos de rendimiento, de forma predeterminada se muestra la vista **Registro** .  Para ver los datos en forma de gráfico, haga clic en **Métricas**.  Para una vista gráfica detallada, haga clic en **+** junto a un contador.  

![Vista Métricas contraída](media/log-analytics-data-sources-performance-counters/metricscollapsed.png)

Si el intervalo de tiempo que ha seleccionado es de 6 horas o menos, el gráfico se actualiza cada pocos segundos.  Los datos en directo se muestran en el lado derecho del gráfico, en color azul claro.

![Vista Métricas expandido con datos en directo](media/log-analytics-data-sources-performance-counters/metricsexpanded.png)

Para agregar datos de rendimiento en una búsqueda de registro, consulte [On-demand metric aggregation and visualization in OMS](http://blogs.technet.microsoft.com/msoms/2016/02/26/on-demand-metric-aggregation-and-visualization-in-oms/) (Agregación de métricas a petición y visualización en OMS).

## <a name="next-steps"></a>Pasos siguientes

- Obtenga información acerca de las [búsquedas de registros](log-analytics-log-searches.md) para analizar los datos recopilados de las soluciones y los orígenes de datos.  
- Exporte los datos recopilados a [Power BI](log-analytics-powerbi.md) para poder realizar más análisis y tener más formas de visualizarlos.


<!--HONumber=Oct16_HO2-->


