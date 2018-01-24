---
title: "Recopilación y análisis de contadores de rendimiento en Azure Log Analytics | Microsoft Docs"
description: "Log Analytics recopila contadores de rendimiento para analizar el rendimiento de los agentes de Windows y Linux.  En este artículo se describe cómo configurar la colección de contadores de rendimiento de los agentes de Windows y Linux, se proporcionan detalles dela ubicación en que se almacenan en área de trabajo y se indica cómo analizarlos en Azure Portal."
services: log-analytics
documentationcenter: 
author: mgoedtel
manager: carmonm
editor: tysonn
ms.assetid: 20e145e4-2ace-4cd9-b252-71fb4f94099e
ms.service: log-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 12/19/2017
ms.author: magoedte
ms.openlocfilehash: 0f7119f280f2eb51222ade2ea7984b560a02f667
ms.sourcegitcommit: f46cbcff710f590aebe437c6dd459452ddf0af09
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/20/2017
---
# <a name="windows-and-linux-performance-data-sources-in-log-analytics"></a>Orígenes de datos de rendimiento de Windows y Linux en Log Analytics
Los contadores de rendimiento de Windows y Linux ofrecen información acerca del rendimiento de los componentes de hardware, los sistemas operativos y las aplicaciones.  Log Analytics puede recopilar contadores de rendimiento a intervalos frecuentes para el análisis casi en tiempo real (NRT), además de agregar datos de rendimiento para el análisis a más largo plazo y la creación de informes.

![contadores de rendimiento](media/log-analytics-data-sources-performance-counters/overview.png)

## <a name="configuring-performance-counters"></a>Configuración de contadores de rendimiento
Los contadores de rendimiento se configuran en el [menú Datos en Configuración de Log Analytics](log-analytics-data-sources.md#configuring-data-sources).

La primera vez que se configuran los contadores de rendimiento de Windows o Linux para un área de trabajo de Log Analytics nueva, se ofrece la opción de crear rápidamente varios contadores comunes.  Se muestran todos con una casilla junto a cada uno.  Asegúrese de que están marcados todos los contadores que desea crear inicialmente y, luego, haga clic en **Add the selected performance counters**(Agregar los contadores de rendimiento seleccionados).

Para los contadores de rendimiento de Windows, puede elegir una instancia específica para cada contador de rendimiento. Para los contadores de rendimiento de Linux, la instancia de cada contador que elija se aplica a todos los contadores secundarios del contador primario. La siguiente tabla muestra las instancias comunes disponibles para los contadores de rendimiento de Windows y de Linux.

| Nombre de instancia | DESCRIPCIÓN |
| --- | --- |
| \_Total |Total de todas las instancias |
| \* |Todas las instancias |
| (/&#124;/var) |Coincide con las instancias con nombre: / o /var |

### <a name="windows-performance-counters"></a>Contadores de rendimiento de Windows

![Configuración de contadores de rendimiento de Windows](media/log-analytics-data-sources-performance-counters/configure-windows.png)

Siga este procedimiento para agregar un nuevo contador de rendimiento de Windows para recopilar.

1. Escriba el nombre del contador en el cuadro de texto con el formato *objeto(instancia)\contador*.  Cuando empiece a escribir, aparece una lista de contadores comunes coincidentes.  Puede seleccionar un contador de la lista o escribir uno propio.  También puede devolver todas las instancias de un contador determinado, para lo que debe especificar *objeto\contador*.  

    Cuando se recopilan contadores de rendimiento de SQL Server de instancias con nombre, todos los contadores de instancias con nombre comienzan por *MSSQL$* y van seguidos del nombre de la instancia.  Por ejemplo, para recopilar el contador Frecuencia de aciertos de caché de registro para todas las bases de datos desde el objeto de rendimiento de base de datos para la instancia de SQL con nombre INST2, especifique `MSSQL$INST2:Databases(*)\Log Cache Hit Ratio`.

2. Haga clic en **+** o presione **ENTRAR** para agregar el contador a la lista.
3. Cuando se agrega un contador, se usa el valor predeterminado de 10 segundos en **Intervalo de ejemplo**.  Este valor se puede cambiar por otro mayor, siempre que no supere los 1800 segundos (30 minutos), en caso de que se desee reducir los requisitos de almacenamiento de los datos de rendimiento recopilados.
4. Cuando haya terminado de agregar contadores, haga clic en el botón **Guardar** de la parte superior de la pantalla para guardar la configuración.

### <a name="linux-performance-counters"></a>Contadores de rendimiento de Linux

![Configuración de contadores de rendimiento de Linux](media/log-analytics-data-sources-performance-counters/configure-linux.png)

Siga este procedimiento para agregar un nuevo contador de rendimiento de Linux para recopilar.

1. De forma predeterminada, todos los cambios realizados en la configuración se insertan automáticamente en todos los agentes.  En el caso de los agentes de Linux, se envía un archivo de configuración al recopilador de datos Fluentd.  Si desea modificar este archivo manualmente en cada agente de Linux, desactive la casilla *Aplicar la configuración que aparece a continuación a mis máquinas con Linux*.
2. Escriba el nombre del contador en el cuadro de texto con el formato *objeto(instancia)\contador*.  Cuando empiece a escribir, aparece una lista de contadores comunes coincidentes.  Puede seleccionar un contador de la lista o escribir uno propio.  
3. Haga clic en **+** o presione **ENTRAR** para agregar el contador a la lista de contadores del objeto.
4. Todos los contadores de un objeto usan el mismo valor en **Intervalo de ejemplo**.  El valor predeterminado es 10 segundos.  Este valor se puede cambiar por otro mayor, siempre que no supere los 1800 segundos (30 minutos), en caso de que se desee reducir los requisitos de almacenamiento de los datos de rendimiento recopilados.
5. Cuando haya terminado de agregar contadores, haga clic en el botón **Guardar** de la parte superior de la pantalla para guardar la configuración.

#### <a name="configure-linux-performance-counters-in-configuration-file"></a>Configuración de contadores de rendimiento de Linux en el archivo de configuración
En lugar de configurar los contadores de rendimiento de Linux mediante Azure Portal, tiene la opción de editar archivos de configuración en el agente de Linux.  Las métricas de rendimiento que se recopilan se controlan según la configuración que aparece en **/etc/opt/microsoft/omsagent/\<identificador de área de trabajo\>/conf/omsagent.conf**.

Cada objeto, o categoría, de métricas de rendimiento para recopilar debe definirse en el archivo de configuración como un solo elemento `<source>` . La sintaxis sigue el modelo siguiente.

    <source>
      type oms_omi  
      object_name "Processor"
      instance_regex ".*"
      counter_name_regex ".*"
      interval 30s
    </source>


Los parámetros de este elemento se describen en la tabla siguiente.

| Parámetros | DESCRIPCIÓN |
|:--|:--|
| object\_name | Nombre de objeto de la colección. |
| instance\_regex |  Una *expresión regular* que define las instancias que desea recopilar. El valor: `.*` especifica todas las instancias. Para recopilar métricas de procesador solamente de la instancia \_Total, puede especificar `_Total`. Para recopilar métricas de procesamiento solamente de las instancias rond o sshd, puede especificar: `(crond\|sshd)`. |
| counter\_name\_regex | Una *expresión regular* que define los contadores (para el objeto) que desea recopilar. Para recopilar todos los contadores para el objeto, especifique: `.*`. Para recopilar, por ejemplo, solo contadores de espacio de intercambio para el objeto de memoria, puede especificar: `.+Swap.+` |
| interval | Frecuencia con la que se recopilan los contadores del objeto. |


En la tabla siguiente se enumera los objetos y contadores que pueden especificar en el archivo de configuración.  Hay contadores adicionales disponibles para determinadas aplicaciones, como se describe en [Collect performance counters for Linux applications in Log Analytics](log-analytics-data-sources-linux-applications.md) (Recopilación de contadores de rendimiento para aplicaciones de Linux en Log Analytics).

| Nombre de objeto | Nombre del contador |
|:--|:--|
| Disco lógico | % de Inodes libres |
| Disco lógico | % de espacio libre |
| Disco lógico | % de Inodes usados |
| Disco lógico | % espacio usado |
| Disco lógico | Bytes de lectura de disco/s  |
| Disco lógico | Lecturas de disco/s  |
| Disco lógico | Transferencias de disco/s |
| Disco lógico |  Bytes de escritura en disco/s |
| Disco lógico | Escrituras en disco/s |
| Disco lógico | Megabytes libres |
| Disco lógico | Bytes de disco lógico/s |
| Memoria | % de memoria disponible |
| Memoria | % de espacio de intercambio disponible |
| Memoria | % de memoria usada |
| Memoria | % de espacio de intercambio usado |
| Memoria | MB de memoria disponibles |
| Memoria | Intercambio de MB disponibles |
| Memoria | Lecturas de página/s |
| Memoria | Escrituras de página/s |
| Memoria | Páginas/s |
| Memoria | Espacio de intercambio de MB usado |
| Memoria | MB de memoria usados |
| Red | Número total de bytes transmitidos |
| Red | Número total de bytes recibidos |
| Red | Número total de bytes |
| Red | Número total de paquetes transmitidos |
| Red | Número total de paquetes recibidos |
| Red | Errores de Rx totales |
| Red | Errores de Tx totales |
| Red | Colisiones totales |
| Disco físico | Prom. Segundos de disco/lecturas |
| Disco físico | Prom. Segundos de disco/transferencias |
| Disco físico | Prom. Segundos de disco/escrituras |
| Disco físico | Bytes de disco físico/s |
| Proceso | Porcentaje de tiempo con privilegios |
| Proceso | Porcentaje de tiempo de usuario |
| Proceso | Kilobytes de memoria usados |
| Proceso | Memoria compartida virtual |
| Procesador | % de tiempo de DPC |
| Procesador | % de tiempo de inactividad |
| Procesador | % de tiempo de interrupción |
| Procesador | % de tiempo de espera de E/S |
| Procesador | % de tiempo bueno |
| Procesador | % de tiempo con privilegios |
| Procesador | % de tiempo de procesador |
| Procesador | % de tiempo de usuario |
| Sistema | Memoria física libre |
| Sistema | Espacio libre en archivos de paginación |
| Sistema | Memoria virtual libre |
| Sistema | Procesos |
| Sistema | Tamaño almacenado en archivos de paginación |
| Sistema | Tiempo de actividad |
| Sistema | Usuarios |


Esta es la configuración predeterminada de las métricas de rendimiento.

    <source>
      type oms_omi
      object_name "Physical Disk"
      instance_regex ".*"
      counter_name_regex ".*"
      interval 5m
    </source>

    <source>
      type oms_omi
      object_name "Logical Disk"
      instance_regex ".*
      counter_name_regex ".*"
      interval 5m
    </source>

    <source>
      type oms_omi
      object_name "Processor"
      instance_regex ".*
      counter_name_regex ".*"
      interval 30s
    </source>

    <source>
      type oms_omi
      object_name "Memory"
      instance_regex ".*"
      counter_name_regex ".*"
      interval 30s
    </source>

## <a name="data-collection"></a>Colección de datos
Log Analytics recopila todos los contadores de rendimiento especificados en su intervalo de ejemplo en todos los agentes que tengan dicho contador instalado.  Los datos no se agregan; los datos sin procesar están disponibles en todas las vistas de búsqueda de registro durante el tiempo especificado por la suscripción.

## <a name="performance-record-properties"></a>Propiedades de registros de rendimiento
Los registros de rendimiento tienen el tipo **Perf** y sus propiedades son las que aparecen en la tabla siguiente.

| Propiedad | DESCRIPCIÓN |
|:--- |:--- |
| Equipo |Nombre del equipo desde el que se recopiló el evento. |
| CounterName |Nombre del contador de rendimiento. |
| CounterPath |Ruta de acceso completa del contador en el formato \\\\\<Equipo>\\objeto(instancia)\\contador. |
| CounterValue |Valor numérico del contador. |
| InstanceName |Nombre de la instancia del evento.  Vacío si no hay instancias. |
| ObjectName |Nombre del objeto de rendimiento |
| SourceSystem |Tipo de agente del que se recopilaron los datos. <br><br>OpsManager: agente de Windows, ya sea una conexión directa o SCOM <br> Linux: todos los agentes de Linux.  <br> AzureStorage: Diagnósticos de Azure |
| TimeGenerated |Fecha y hora en que se toma la muestra de datos. |

## <a name="sizing-estimates"></a>Estimaciones de tamaño
 Una estimación aproximada para la recopilación de un contador determinado a intervalos de 10 segundos es de aproximadamente 1 MB por día y por instancia.  Los requisitos de almacenamiento de un contador se pueden calcular con la siguiente fórmula.

    1 MB x (number of counters) x (number of agents) x (number of instances)

## <a name="log-searches-with-performance-records"></a>Búsquedas de registros con registros de rendimiento
La tabla siguiente proporciona distintos ejemplos de búsquedas de registros que recuperan registros de rendimiento.

| Consultar | DESCRIPCIÓN |
|:--- |:--- |
| Perf |Todos los datos de rendimiento |
| Perf &#124; where Computer == "MyComputer" |Todos los datos de rendimiento de un equipo concreto |
| Perf &#124; where CounterName == "Current Disk Queue Length" |Todos los datos de rendimiento de un contador concreto |
| Perf &#124; where ObjectName == "Processor" and CounterName == "% Processor Time" and InstanceName == "_Total" &#124; summarize AVGCPU = avg(Average) by Computer |Uso medio de CPU en todos los equipos |
| Perf &#124; where CounterName == "% Processor Time" &#124; summarize AggregatedValue = max(Max) by Computer |Uso máximo de CPU en todos los equipos |
| Perf &#124; where ObjectName == "LogicalDisk" and CounterName == "Current Disk Queue Length" and Computer == "MyComputerName" &#124; summarize AggregatedValue = avg(Average) by InstanceName |Longitud media de cola de disco actual en todas las instancias de un equipo dado |
| Perf &#124; where CounterName == "DiskTransfers/sec" &#124; summarize AggregatedValue = percentile(Average, 95) by Computer |Percentil 95 de transferencias de disco por segundo en todos los equipos |
| Perf &#124; where CounterName == "% Processor Time" and InstanceName == "_Total" &#124; summarize AggregatedValue = avg(CounterValue) by bin(TimeGenerated, 1h), Computer |Promedio por hora de uso de CPU en todos los equipos |
| Perf &#124; where Computer == "MyComputer" and CounterName startswith_cs "%" and InstanceName == "_Total" &#124; summarize AggregatedValue = percentile(CounterValue, 70) by bin(TimeGenerated, 1h), CounterName | Percentil 70 por hora de cada contador de porcentaje % para un equipo concreto |
| Perf &#124; where CounterName == "% Processor Time" and InstanceName == "_Total" and Computer == "MyComputer" &#124; summarize ["min(CounterValue)"] = min(CounterValue), ["avg(CounterValue)"] = avg(CounterValue), ["percentile75(CounterValue)"] = percentile(CounterValue, 75), ["max(CounterValue)"] = max(CounterValue) by bin(TimeGenerated, 1h), Computer |Promedio, mínimo, máximo y percentil 75 por hora de uso de CPU de un equipo específico |
| Perf &#124; where ObjectName == "MSSQL$INST2:Databases" and InstanceName == "master" | Todos los datos de rendimiento del objeto de rendimiento de la base de datos para la base de datos maestra (master) de la instancia de SQL Server con nombre INST2.  




## <a name="next-steps"></a>pasos siguientes
* [Recopilación de contadores de rendimiento desde aplicaciones de Linux](log-analytics-data-sources-linux-applications.md), lo que incluye MySQL y Apache HTTP Server.
* Obtenga información acerca de las [búsquedas de registros](log-analytics-log-searches.md) para analizar los datos recopilados de las soluciones y los orígenes de datos.  
* Exporte los datos recopilados a [Power BI](log-analytics-powerbi.md) para poder realizar más análisis y tener más formas de visualizarlos.
