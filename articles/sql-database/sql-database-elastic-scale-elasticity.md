<properties 
	pageTitle="Elasticidad de partición" 
	description="Explica los conceptos y proporciona ejemplos tras la elasticidad de particiones, la capacidad de escalar con facilidad las bases de datos SQL de Azure." 
	services="sql-database" 
	documentationCenter="" 
	manager="jeffreyg" 
	authors="sidneyh" 
	editor=""/>

<tags 
	ms.service="sql-database" 
	ms.workload="sql-database" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="04/17/2015" 
	ms.author="sidneyh"/>

# Elasticidad de partición 

**Elasticidad de partición** permite que los desarrolladores de aplicaciones aumenten y disminuyan de manera dinámica los recursos de las bases de datos según lo dicte la necesidad, permitiendo así optimizar el rendimiento de las aplicaciones y minimizar los costos. La combinación de **herramientas de bases de datos elásticas** para Base de datos SQL de Azure con los [niveles de servicio Básico, Estándar y Premium](http://msdn.microsoft.com/library/azure/dn741340.aspx) proporciona escenarios de elasticidad muy atractivos. Las herramientas de bases de datos elásticas permiten el escalado horizontal, un patrón de diseño en el que las bases de datos (también [conocidas como "particiones"](sql-database-elastic-scale-glossary.md)) se agregan o eliminan de un conjunto de particiones para aumentar o reducir la capacidad. De manera similar, los niveles de servicio de Base de datos SQL proporcionan capacidades de **escalado vertical**, en las que los recursos de una base de datos se pueden escalar o reducir verticalmente para satisfacer la demanda como corresponde. En conjunto, el escalado vertical de una sola partición y el escalado horizontal de muchas particiones ofrecen a los desarrolladores de aplicaciones un entorno muy flexible que puede escalar para satisfacer las necesidades de rendimiento, capacidad y optimización de costos.

Con la funcionalidad de **grupos de bases de datos elásticas** recién presentada, el escalado vertical es incluso más simple de lograr. Los grupos permiten que el consumo individual de los recursos de una base de datos aumente o disminuya *automáticamente* dentro de un presupuesto compartido entre todo el grupo. En el caso de las aplicaciones que decidan no usar los grupos de bases de datos elásticas, en este artículo se describirán otras técnicas para implementar mecánicas basadas en directivas para la administración del escalado vertical, así como algunos escenarios comunes para automatizar las operaciones de escalado horizontal.

## Ejemplo de escalado horizontal: alza en la demanda por un concierto

Un escenario canónica del escalado horizontal es una aplicación que procesa las transacciones realizadas por las entradas a conciertos. Cuando se trata de volúmenes normales de clientes, la aplicación usa el mínimo de recursos de base de datos para manejar las transacciones de compra. Sin embargo, cuando salen a la venta las entradas de un concierto popular, una base de datos única no puede manejar el gran alza de la demanda de los clientes.

Para procesar el considerable aumento de las transacciones, la aplicación escala horizontalmente. Ahora la aplicación puede distribuir la carga de las transacciones entre varias particiones. Cuando los recursos adicionales dejan de ser necesarios, el nivel de la base de datos se disminuye para el uso normal. Aquí el escalado horizontal permite que una aplicación escale horizontalmente para satisfacer la demanda de los clientes y reduzca horizontalmente cuando dejen de requerirse los recursos.

## Ejemplo de escalado vertical: telemetría

Un escenario canónico para el escalado vertical es una aplicación que usa un **conjunto de particiones** para almacenar la telemetría operacional. En este escenario, se recomienda co-ubicar todos los datos de telemetría de un solo día en una partición. En esta aplicación, los datos del día actual se recopilan en una partición y se aprovisiona una partición nueva para los días subsiguientes. Los datos operacionales entonces se pueden conservar y consultar según corresponda.

Para recopilar los datos de telemetría en altas cargas, se recomienda emplear un nivel de servicio superior. En otras palabras, una base de datos Premium es mejor que una base de datos Básica. Una vez que la base de datos alcanza su capacidad, cambia de la recopilación al análisis y los informes. Para ello, el rendimiento del nivel Estándar es igual a la tarea. De este modo, es posible reducir verticalmente el nivel de servicio en las particiones (que no sean la creada más recientemente) para así concordar con los requisitos de rendimiento para los datos más antiguos.

El escalado vertical también se puede usar para aumentar el rendimiento de una base de datos única a fin de alcanzar un mayor rendimiento. Un ejemplo puede ser una aplicación para declaración de impuestos. En el momento de la declaración, se recomienda mantener todos los datos de un mismo cliente en la misma base de datos y aumentar el rendimiento de esa partición. Dependiendo de la aplicación, escalar y disminuir verticalmente los recursos es una ventaja para optimizar los requisitos de costo y rendimiento.

En conjunto, el escalado horizontal y vertical del nivel de la base de datos se encuentran en el núcleo de la escalabilidad de aplicaciones y la elasticidad de partición.

Este documento proporciona el contexto para los escenarios de elasticidad de partición, así como referencias a implementaciones de ejemplo de escalado vertical y horizontal.

## Mecánica de la elasticidad de partición 

El escalado vertical y horizontal es una función que tiene tres componentes básicos:

1. **Telemetría**
2. **Regla**
3. **Acción**   

## Telemetría

La **elasticidad controlada por datos** está en el centro de una aplicación de escalado elástico. Según los requisitos de rendimiento, use la telemetría para tomar decisiones controladas por datos sobre si escalar vertical u horizontalmente.

#### Orígenes de datos de telemetría
En el contexto de Base de datos SQL de Azure, hay un puñado de orígenes clave que se pueden aprovechar como orígenes de datos para la elasticidad de partición.

1. La **telemetría de rendimiento** se expone en duraciones de cinco minutos en la vista **sys.resource\_stats**. 
2. La **telemetría de capacidad de base de datos** por hora se expone a través de la vista **sys.resource\_usage**.  

Es posible analizar el uso de los recursos de rendimiento si consulta la base de datos maestra usando la siguiente consulta, donde "Shard\_20140623" es el nombre de la base de datos de destino.

    SELECT TOP 10 *  
    FROM sys.resource_stats  
    WHERE database_name = 'Shard_20140623'  
    ORDER BY start_time DESC 

La **telemetría de rendimiento** se puede resumir durante un período de tiempo (siete días en el caso de la consulta que aparece a continuación) para así eliminar comportamientos transitorios.

    SELECT  
    avg(avg_cpu_percent) AS 'Average CPU Utilization In Percent', 
        max(avg_cpu_percent) AS 'Maximum CPU Utilization In Percent', 
        avg(avg_physical_data_read_percent) AS 'Average Physical Data Read Utilization In Percent', 
        max(avg_physical_data_read_percent) AS 'Maximum Physical Data Read Utilization In Percent', 
        avg(avg_log_write_percent) AS 'Average Log Write Utilization In Percent', 
        max(avg_log_write_percent) AS 'Maximum Log Write Utilization In Percent', 
        avg(active_session_count) AS 'Average # of Sessions', 
        max(active_session_count) AS 'Maximum # of Sessions', 
        avg(active_worker_count) AS 'Average # of Workers', 
        max(active_worker_count) AS 'Maximum # of Workers' 
    FROM sys.resource_stats  
    WHERE database_name = ' Shard_20140623' AND start_time > DATEADD(day, -7, GETDATE()); 

La **capacidad de base de datos** se puede medir con una consulta similar contra la vista **sys.resource\_usage**. El máximo de la columna **storage\_in\_megabytes** arroja el tamaño actual de la base de datos. Tal telemetría resulta útil para escalar horizontalmente una aplicación cuando una partición determinada alcanza su capacidad.

    SELECT TOP 10 * 
    FROM [sys].[resource_usage] 
    WHERE database_name = 'Shard_20140623'  
    ORDER BY time DESC 

Como los datos se recopilan en una partición determinada, es útil proyectar a futuro un día y determinar si la partición tiene la capacidad suficiente como para controlar la carga de trabajo entrante. A pesar de que no es una implementación verdadera de regresión lineal, la siguiente consulta devuelve el delta máximo de la capacidad de base de datos entre dos días consecutivos. Tal telemetría se puede aplicar entonces a una regla que resultaría en la realización de una acción (o no acción).

    WITH MaxDatabaseDailySize AS( 
        SELECT 
            ROW_NUMBER() OVER (ORDER BY convert(date, [time]) DESC) as [Order], 
            CONVERT(date,[time]) as [date],  
            MAX(storage_in_megabytes) as [MaxSizeDay] 
        FROM [sys].[resource_usage] 
        WHERE  
            database_name = 'Shard_20140623' 
        GROUP BY CONVERT(date,[time]) 
        ) 
    
    SELECT 
        MAX(ISNULL(Size.[MaxSizeDay] - PreviousDaySize.[MaxSizeDay], 0)) 
    FROM  
        MaxDatabaseDailySize Size INNER JOIN 
        MaxDatabaseDailySize PreviousDaySize ON Size.[order]+1 = PreviousDaySize.[order] 
    WHERE 
        Size.[order] < 8 

## Regla  

La regla es el motor de decisiones que termina si se tomará o no una acción. Algunas reglas son muy simples y otras, mucho más complicadas. Tal como se muestra en el siguiente fragmento de código, es posible configurar una regla centrada en la capacidad para que cuando una partición alcance $SafetyMargin, por ejemplo, 80%, de su capacidad máxima, se aprovisione una nueva partición.

    # Determine if the current DB size plus the maximum daily delta size is greater than the threshold 
    if( ($CurrentDbSizeMb + $MaxDbDeltaMb) -gt ($MaxDbSizeMb * $SafetyMargin))  
    {#provision new shard} 

Dados los orígenes de datos anteriores, es posible formular varias reglas para lograr numerosos escenarios de elasticidad de partición.

## Acción  

Según el resultado de la regla, la acción (o no acción) es el resultado. Las dos acciones más comunes son:

* El aumento o la disminución del nivel de servicio o del nivel de rendimiento de la partición 
* La adición o eliminación de una partición en un conjunto de particiones.

Tenga en cuenta que en ambas soluciones de escalado, horizontal y vertical, el resultado de una acción no es inmediato. Por ejemplo, cuando se escala verticalmente, la emisión del comando ALTER DATABASE para aumentar el nivel de servicio de una base de datos Básica a base de datos Premium toma una cantidad variable de tiempo. La duración depende en gran medida del tamaño de la base de datos (para obtener más información, consulte [Cambio de niveles de servicio y niveles de rendimiento](http://msdn.microsoft.com/library/azure/dn369872.aspx)). De manera similar, la asignación o aprovisionamiento de una partición nueva tampoco es inmediata. De este modo, tanto para el escalado vertical como para el horizontal,las aplicaciones deben considerar que modificar o aprovisionar una nueva base de datos no es inmediato.

## Escenario de elasticidad de partición de ejemplo 

El ejemplo que aparece en la ilustración que se muestra a continuación resalta dos escenarios de escalado elástico: 1. escalado horizontal de un mapa de particiones 2. escalado vertical de una partición individual.

![Ingesta de datos operacionales][1]

Para realizar un escalado horizontal, se usa una regla (basada en la fecha o en el tamaño de la base de datos) para aprovisionar una partición nueva y registrarla con el mapa de particiones, con lo que se aumenta el nivel de la base de datos. En segundo lugar, para realizar un escalado vertical, se implementa una segunda regla en la cual cualquier partición que tenga más de un día se degrada desde la edición Premium a una edición Estándar o Básica.

Considere de nuevo el escenario de telemetría: las particiones de aplicación por fecha. Recopila continuamente los datos de la telemetría, lo que requiere una edición de alto rendimiento en el momento de la carga, pero un rendimiento inferior a medida que los datos envejecen. Los datos del día actual [Tnow] se escriben en una base de datos de alto rendimiento (Premium). Una vez que el reloj llega a la medianoche, la partición del día anterior (ahora [T-1]) ya no se usa para la recopilación. Los datos actuales los recopila el actual [Tnow]. Antes del próximo día, se debe aprovisionar una nueva partición y se debe registrar con el mapa de particiones ([T+1]).

Esto se puede hacer aprovisionando una nueva partición antes de cada día nuevo o aprovisionando una partición nueva cuando la partición actual ([Tnow]) esté cerca de su capacidad máxima. El uso de cualquiera de estos métodos conserva la localidad de los datos para toda la telemetría escrita en un día determinado. También se puede aplicar particionamiento por hora para una granularidad más fina. Una vez que se aprovisiona una partición nueva, y como [T-1] se usa para consultas e informes, se desea reducir el nivel de rendimiento de la base de datos para así reducir costos. A medida que el contenido de las bases de datos envejece, el nivel de rendimiento se puede reducir más y/o el contenido de las bases de datos se puede archivar en Almacenamiento de Azure o se pueden eliminar, dependiendo de la aplicación.

## Ejecución de escenarios de elasticidad de partición  

Para facilitar la implementación real de ambos escenarios de escalado, horizontal y vertical, se publicaron varios [scripts de ejemplo de Elasticidad de partición](http://go.microsoft.com/?linkid=9862617) y se publicaron en Centro de scripts. Escritos para ejecutarse en el servicio Automatización de Azure, estos runbooks de PowerShell proporcionan diversos métodos que interactúan con la biblioteca de cliente de bases de datos elásticas y Base de datos SQL de Azure. Basándose en estos ejemplos de código, o extrayendo de ellos, es posible crear los scripts necesarios para automatizar los escenarios de escalado horizontal, vertical o ambos para su aplicación.

[AZURE.INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]

<!--Image references-->
[1]: ./media/sql-database-elastic-scale-elasticity/data-ingestion.png

<!--anchors-->
[Telemetry]: #telemetry
[Rule]: #rule
[Action]: #action
 

<!---HONumber=August15_HO6-->