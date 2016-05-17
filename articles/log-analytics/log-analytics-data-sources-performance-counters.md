<properties 
   pageTitle="Contadores de rendimiento de Windows y Linux en Log Analytics | Microsoft Azure"
   description="Log Analytics recopila contadores de rendimiento para analizar el rendimiento de los agentes de Windows y Linux. En este artículo se describe cómo configurar la colección de contadores de rendimiento de los agentes de Windows y Linux, se proporcionan detalles dela ubicación en que se almacenan en el repositorio de OMS y se indica cómo analizarlos en el portal de OMS."
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
   ms.date="04/28/2016"
   ms.author="bwren" />

# Orígenes de datos de rendimiento de Windows y Linux en Log Analytics 

Los contadores de rendimiento de Windows y Linux ofrecen información acerca del rendimiento de los componentes de hardware, los sistemas operativos y las aplicaciones. Log Analytics puede recopilar contadores de rendimiento a intervalos frecuentes para el análisis casi en tiempo real (NRT), además de agregar datos de rendimiento para el análisis a más largo plazo y la creación de informes.

![Contadores de rendimiento](media/log-analytics-data-sources-performance-counters/overview.png)

## Configuración de contadores de rendimiento

Los contadores de rendimiento se configuran en [el menú Data (Datos) de la configuración de Log Analytics](log-analytics-data-sources.md/configuring-data-sources).

La primera vez que se configuran los contadores de rendimiento de Windows o Linux para un área de trabajo de OMS nueva, se proporciona la opción de crear rápidamente varios contadores comunes. Se enumerarán todos ellos con una casilla junto a cada uno. Asegúrese de que están marcados todos los contadores que desea crear inicialmente y, luego, haga clic en **Add the selected performance counters** (Agregar los contadores de rendimiento seleccionados).

![Configuración de contadores de rendimiento de Windows](media/log-analytics-data-sources-performance-counters/configure-windows.png)

Siga este procedimiento para agregar un nuevo contador de rendimiento de Windows para recopilar.

1. Escriba el nombre del contador en el cuadro de texto en el formato *objeto (instancia)\\contador*. Cuando empiece a escribir, aparecerá una lista de contadores comunes coincidentes. Puede seleccionar un contador de la lista o escribir uno propio. También puede devolver todas las instancias de un contador determinado, para lo que debe especificar *objeto\\contador*. 
2. Haga clic en **+** o presione **ENTRAR** para agregar el contador a la lista.
3. Al agregar un contador, usará el valor predeterminado de 10 segundos en **intervalo de ejemplo**. Este valor se puede cambiar por otro mayor, siempre que no supere los 1800 segundos (30 minutos), en caso de que se desee reducir los requisitos de almacenamiento de los datos de rendimiento recopilados.
4. Cuando haya terminado de agregar contadores, haga clic en el botón **Guardar** de la parte superior de la pantalla para guardar la configuración.

![Configuración de contadores de rendimiento de Linux](media/log-analytics-data-sources-performance-counters/configure-linux.png)

Siga este procedimiento para agregar un nuevo contador de rendimiento de Linux para recopilar.

1. De forma predeterminada, todos los cambios realizados en la configuración se insertan automáticamente en todos los agentes. En el caso de los agentes de Linux, se envía un archivo de configuración al recopilador de datos Fluentd. Si desea modificar este archivo manualmente en cada uno de los agentes de Linux, desactive la casilla *Apply below configuration to my Linux machines* (Aplicar la configuración siguiente a mis equipos con Linux).
2. Escriba el nombre del contador en el cuadro de texto en el formato *objeto (instancia)\\contador*. Cuando empiece a escribir, aparecerá una lista de contadores comunes coincidentes. Puede seleccionar un contador de la lista o escribir uno propio.  
2. Haga clic en **+** o presione **ENTRAR** para agregar el contador a la lista de contadores del objeto.
3. Todos los contadores de un objeto utilizarán el mismo **intervalo de ejemplo**. El valor predeterminado es 10 segundos, pero se puede cambiar por otro mayor, siempre que no supere los 1800 segundos (30 minutos), si se desea reducir los requisitos de almacenamiento de los datos de rendimiento recopilados.
4. Cuando haya terminado de agregar contadores, haga clic en el botón **Guardar** de la parte superior de la pantalla para guardar la configuración.

## Colección de datos

Log Analytics recopilará todos los contadores de rendimiento especificados en su intervalo de muestra en todos los agentes que tengan dicho contador instalado. Los datos sin procesar estarán disponibles durante 14 días en la vista del gráfico expandido en la consola de OMS.

Todos los datos de rendimiento recopilados se agregan a intervalos de 30 minutos. Los datos agregados están disponibles en todas las vistas de búsqueda de registro durante el tiempo especificado por la suscripción de OMS.


## Propiedades de registros de rendimiento

Los registros de rendimiento se crean a partir de los datos de rendimiento agregados a intervalos de 30 minutos. El valor del registro es el valor medio del contador en los 30 minutos anteriores. No se crean registros de datos de NRT sin procesar. Los datos sin procesar solo están disponibles en la vista **Metrics** (Métricas) de la consola de OMS.

Los registros de rendimiento tienen el tipo **Perf** y sus propiedades son las que aparecen en la tabla siguiente.

| Propiedad | Descripción |
|:--|:--|
| Equipo | Nombre del equipo desde el que se recopiló el evento. |
| CounterName | Nombre del contador de rendimiento. |
| CounterPath | Ruta de acceso completa del contador en el formato \\\<equipo >\\objeto (instancia) \\contador. |
| CounterValue | Valor numérico del contador agregado en 30 minutos. |
| InstanceName | Nombre de la instancia del evento. Vacío si no hay instancias. |
| ObjectName | Nombre del objeto de rendimiento |
| SourceSystem | Tipo de agente del que se recopilaron los datos. <br> OpsManager: agente de Windows, conexión directa o SCOM <br> Linux: todos los agentes de Linux <br> AzureStorage: Diagnósticos de Azure |
| TimeGenerated | Fecha y hora en que se toma la muestra de datos. |


## Estimaciones de tamaño

 Una estimación aproximada para la recopilación de un contador determinado a intervalos de 10 segundos es de aproximadamente 1 MB por día y por instancia. Los requisitos de almacenamiento de un contador se pueden calcular con la siguiente fórmula.

	1 MB x (number of counters) x (number of agents) x (number of instances)

## Búsquedas de registros con registros de rendimiento

La tabla siguiente proporciona distintos ejemplos de búsquedas de registros que recuperan registros de rendimiento.

| Consultar | Descripción |
|:--|:--|
| Type=Perf | Todos los datos de rendimiento |
| Type=Perf Computer="MyComputer" | Todos los datos de rendimiento de un equipo concreto |
| Type=Perf CounterName="Current Disk Queue Length" | Todos los datos de rendimiento de un contador concreto |
| Type=Perf (ObjectName=Processor) CounterName="% Processor Time" InstanceName=\_Total | measure Avg(Average) as AVGCPU by Computer | Uso medio de CPU en todos los equipos |
| Type=Perf (CounterName="% Processor Time") | measure max(Max) by Computer | Uso máximo de CPU en todos los equipos |
| Type=Perf ObjectName=LogicalDisk CounterName="Current Disk Queue Length" Computer="MyComputerName" | measure Avg(Average) by InstanceName | Longitud media de cola de disco actual en todas las instancias de un equipo dado |
| Type=Perf CounterName="DiskTransfers/sec" | measure percentile95(Average) by Computer | Percentil 95 de transferencias de disco por segundo en todos los equipos |
| Type=Perf CounterName="% Processor Time" InstanceName="\_Total" | measure avg(CounterValue) by Computer Interval 1HOUR | Hourly average of CPU usage across all computers | | Type=Perf Computer="MyComputer" CounterName=%* InstanceName=\_Total | measure percentile70(CounterValue) by CounterName Interval 1HOUR | Hourly 70 percentile of every % percent counter for a particular computer | | Type=Perf CounterName="% Processor Time" InstanceName="\_Total" (Computer="MyComputer") | measure min(CounterValue), avg(CounterValue), percentile75(CounterValue), max(CounterValue) by Computer Interval 1HOUR | Hourly average, minimum, maximum, and 75-percentile CPU usage for a specific computer |

## Visualización de datos de rendimiento

Cuando se ejecuta una búsqueda del registro de los datos de rendimiento, de forma predeterminada se muestra la vista **Registro**. Esta vista incluye los registros de rendimiento agregados. Para ver los datos en forma de gráfico, haga clic en **Métricas**. Haga clic en el botón **+** que se encuentra junto el contador particular que desea ver.

![Vista Métricas contraída](media/log-analytics-data-sources-performance-counters/metricscollapsed.png)


Si el intervalo de tiempo que ha seleccionado es de 6 horas, o inferior, el gráfico mostrará los datos de NRT, que se actualizarán cada pocos segundos. Los datos en directo se mostrarán en el lado derecho del gráfico, en color azul claro. Si el intervalo de tiempo es mayor de 6 horas, el gráfico usará datos agregados.

![Vista Métricas expandido con datos en directo](media/log-analytics-data-sources-performance-counters/metricsexpanded.png)

## Pasos siguientes

- Obtenga información acerca de las [búsquedas de registros](log-analytics-log-searches.md) para analizar los datos recopilados de las soluciones y los orígenes de datos.  
- Exporte los datos recopilados a [Power BI](log-analytics-powerbi.md) para poder realizar más análisis y tener más formas de visualizarlos.

<!---HONumber=AcomDC_0504_2016-->