---
title: "Supervisión del rendimiento de base de datos en Azure SQL Database | Microsoft Docs"
description: "Conozca las opciones para la supervisión de la base de datos con herramientas de Azure y vistas de administración dinámica."
keywords: "supervisión de base de datos, rendimiento de base de datos"
services: sql-database
documentationcenter: 
author: CarlRabeler
manager: jhubbard
editor: 
ms.assetid: a2e47475-c955-4a8d-a65c-cbef9a6d9b9f
ms.service: sql-database
ms.custom: monitor & tune
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: On Demand
ms.date: 09/20/2017
ms.author: carlrab
ms.openlocfilehash: 2286843317230b8167b315b1e8e413e7571da4fe
ms.sourcegitcommit: 1fbaa2ccda2fb826c74755d42a31835d9d30e05f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/22/2018
---
# <a name="monitoring-database-performance-in-azure-sql-database"></a>Supervisión de rendimiento de bases de datos con Azure SQL Database
La supervisión del rendimiento de una base de datos SQL en Azure comienza con la supervisión del uso de recursos, en relación con el nivel de rendimiento elegido para la base de datos. La supervisión le ayudará a determinar si la base de datos tiene un exceso de capacidad o si tiene problemas porque se ha alcanzado el número máximo de recursos y, después, a decidir si es el momento de ajustar el nivel de rendimiento y el [nivel de servicio](sql-database-service-tiers.md) de la base de datos. Puede supervisar la base de datos mediante herramientas gráficas en el [Azure Portal](https://portal.azure.com) o mediante las [vistas de administración dinámicas](https://msdn.microsoft.com/library/ms188754.aspx) de SQL.

> [!TIP]
> Use [Intelligent Insights de Azure SQL](sql-database-intelligent-insights.md) para supervisar automáticamente el rendimiento de su base de datos. Una vez que se detecta un problema de rendimiento, se genera un registro de diagnóstico con los detalles y el análisis de la causa principal (RCA) del problema. Cuando es posible, se proporciona una recomendación para la mejora del rendimiento.
>

## <a name="monitor-databases-using-the-azure-portal"></a>Supervisión de bases de datos mediante el Portal de Azure
En el [Portal de Azure](https://portal.azure.com/), puede supervisar la utilización de una base de datos única; para ello, debe seleccionar la base de datos y hacer clic en el gráfico **Supervisión** . Al hacer esto, se abrirá la ventana **Métrica** que se puede cambiar haciendo clic en el botón **Editar gráfico**. Agregue las siguientes métricas:

* Porcentaje de CPU
* Porcentaje de DTU
* Porcentaje de E/S de datos
* Porcentaje de tamaño de base de datos

Una vez agregadas estas métricas, podrá verlas en el gráfico **Supervisión**; si desea ver más detalles, podrá hacerlo en la ventana **Métrica**. Las cuatro métricas muestran el porcentaje de uso medio, en relación con la **DTU** de la base de datos. Consulte el artículo de [niveles de servicio](sql-database-service-tiers.md) para más información sobre las unidades de transmisión estándar (DTU).

![Supervisión del nivel de servicio del rendimiento de la base de datos.](./media/sql-database-single-database-monitoring/sqldb_service_tier_monitoring.png)

También se pueden configurar alertas en las métricas de rendimiento. Haga clic en el botón **Agregar alerta** de la ventana **Métrica**. Siga el asistente para configurar la alerta. Tiene la opción de que se genere una alerta si la métrica supera un umbral determinado, o si no llega a él.

Por ejemplo, si espera que crezca la carga de trabajo de una base de datos, puede configurar una alerta por correo electrónico cada vez que la base de datos alcance el 80% en cualquiera de las métricas de rendimiento. Esto se puede usar como advertencia prematura para saber cuándo puede tener que cambiar al nivel de rendimiento inmediatamente superior.

Las métricas de rendimiento también pueden ayudarle a determinar si puede cambiar a un nivel de rendimiento inferior. Suponga que usa una base de datos Estándar S2 y todas las métricas de rendimiento muestran que, de media, la base de datos no usa más del 10% en ningún momento. Es probable que la base de datos funcione bien en Estándar S1. Sin embargo, tenga en cuenta las cargas de trabajo que tienen picos o fluctúan antes de tomar la decisión de cambiar a un nivel de rendimiento inferior.

## <a name="monitor-databases-using-dmvs"></a>Supervisión de bases de datos mediante DMV
Las mismas métricas que se exponen en el Portal están también disponibles a través de las vistas del sistema: [sys.resource_stats](https://msdn.microsoft.com/library/dn269979.aspx) en la base de datos **maestra** lógica del servidor y [sys.dm_db_resource_stats](https://msdn.microsoft.com/library/dn800981.aspx) en la base de datos de usuario. Use **sys.resource_stats** si necesita supervisar datos menos pormenorizados en un periodo de tiempo más amplio. Use **sys.dm_db_resource_stats** si necesita supervisar datos más pormenorizados en un período de tiempo más breve. Para obtener más información, consulte la [Guía de rendimiento de Azure SQL Database](sql-database-single-database-monitor.md#monitor-resource-use).

> [!NOTE]
> **sys.dm_db_resource_stats** devuelve un conjunto de resultados vacío cuando se utiliza en las bases de datos Web Edition y Business Edition, que se retiraron.
>
>

### <a name="monitor-resource-use"></a>Supervisión del uso de recursos

Puede supervisar el uso de recursos con [Información de rendimiento de consultas de SQL Database](sql-database-query-performance.md) y [Almacén de consultas](https://msdn.microsoft.com/library/dn817826.aspx).

También puede supervisar el uso con estas dos vistas:

* [sys.dm_db_resource_stats](https://msdn.microsoft.com/library/dn800981.aspx)
* [sys.resource_stats](https://msdn.microsoft.com/library/dn269979.aspx)

#### <a name="sysdmdbresourcestats"></a>sys.dm_db_resource_stats
Puede usar la vista [sys.dm_db_resource_stats](https://msdn.microsoft.com/library/dn800981.aspx) en cada SQL Database. La vista **sys.dm_db_resource_stats** muestra los datos de uso reciente de recursos en relación con el nivel de servicio. Porcentajes medios para CPU, E/S de datos, escritura de registros y memoria, se registran cada 15 segundos y se mantienen durante una hora.

Dado que esta vista proporciona una panorámica más granular del uso de recursos, use primero **sys.dm_db_resource_stats** para cualquier análisis o solución de problemas de estado actual. Por ejemplo, esta consulta muestra el uso medio y máximo de recursos para la base de datos actual durante la última hora:

    SELECT  
        AVG(avg_cpu_percent) AS 'Average CPU use in percent',
        MAX(avg_cpu_percent) AS 'Maximum CPU use in percent',
        AVG(avg_data_io_percent) AS 'Average data I/O in percent',
        MAX(avg_data_io_percent) AS 'Maximum data I/O in percent',
        AVG(avg_log_write_percent) AS 'Average log write use in percent',
        MAX(avg_log_write_percent) AS 'Maximum log write use in percent',
        AVG(avg_memory_usage_percent) AS 'Average memory use in percent',
        MAX(avg_memory_usage_percent) AS 'Maximum memory use in percent'
    FROM sys.dm_db_resource_stats;  

Para otras consultas, consulte los ejemplos de [sys.dm_db_resource_stats](https://msdn.microsoft.com/library/dn800981.aspx).

#### <a name="sysresourcestats"></a>Sys.resource_stats
La vista [sys.resource_stats](https://msdn.microsoft.com/library/dn269979.aspx) de la base de datos **maestra** proporciona información adicional que puede ayudar a supervisar el rendimiento de la SQL Database en su nivel de rendimiento y nivel de servicio específicos. Los datos se recopilan cada cinco minutos y se mantienen durante aproximadamente 14 días. Esta vista es útil para realizar análisis históricos a largo plazo de cómo la base de datos SQL usa los recursos.

En el siguiente gráfico se muestra el uso de recursos de CPU para una base de datos Premium con el nivel de rendimiento P2 durante cada hora en una semana. Este gráfico en concreto empieza el lunes, muestra 5 días laborables y, a continuación, un fin de semana cuando hay mucha menos actividad en la aplicación.

![Uso de recursos de base de datos SQL](./media/sql-database-performance-guidance/sql_db_resource_utilization.png)

Según los datos, esta base de datos tiene actualmente una carga máxima de CPU superior al 50 % de uso de la CPU respecto al nivel de rendimiento P2 (mediodía del martes). Si la CPU es el factor dominante en el perfil de recursos de la aplicación, puede decidir que P2 es el nivel de rendimiento adecuado para garantizar que la carga de trabajo siempre se ajuste. Si espera que una aplicación crezca con el tiempo, es una buena idea tener un búfer de recursos adicionales para que la aplicación no alcance el límite del nivel de rendimiento. Si aumenta el nivel de rendimiento, puede ayudar a evitar errores visibles para el cliente que pueden producirse cuando una base de datos no tiene suficiente capacidad para procesar las solicitudes de manera eficaz, especialmente en entornos sensibles a la latencia. Un ejemplo es una base de datos que admite una aplicación que dibuja páginas web basadas en los resultados de las llamadas de base de datos.

Otros tipos de aplicaciones pueden interpretar el mismo gráfico de manera diferente. Por ejemplo, si una aplicación intenta procesar datos de nóminas todos los días y tiene el mismo gráfico, este tipo de modelo de "trabajo por lotes" podría funcionar bien en un nivel de rendimiento P1. El nivel de rendimiento P1 tiene 100 DTU en comparación con las 200 DTU del nivel de rendimiento P2. El nivel de rendimiento P1 proporciona la mitad de rendimiento que el nivel de rendimiento P2. Por lo tanto, el 50 por ciento de uso de CPU en P2 es igual al 100 por cien de uso de CPU en P1. Si la aplicación no tiene tiempos de espera, puede dar igual que un trabajo tarde 2 o 2,5 horas en completarse, siempre que se termine hoy. Una aplicación de esta categoría puede usar probablemente un nivel de rendimiento P1. Puede aprovechar el hecho de que hay períodos de tiempo durante el día en que el uso de recursos es menor, lo que significa que las "cargas elevadas" podrían retrasarse a uno de esos momentos más tarde ese día. El nivel de rendimiento P1 podría ser conveniente para ese tipo de aplicación (y ahorrar dinero), siempre y cuando los trabajos se puedan finalizar a tiempo cada día.

Azure SQL Database muestra información sobre los recursos consumidos para cada base de datos activa en la vista **sys.resource_stats** de la base de datos **maestra** de cada servidor. Los datos de la tabla se agregan en intervalos de 5 minutos. Con los niveles de servicio Básico, Estándar y Premium, los datos pueden tardar más de cinco minutos en aparecer en la tabla, así que estos datos son mejores para el análisis histórico que para el análisis casi en tiempo real. Consulte la vista **sys.resource_stats** para ver el historial reciente de una base de datos y para validar si la reserva elegida proporciona el rendimiento que quiere cuando lo necesita.

> [!NOTE]
> Tiene que estar conectado a la base de datos **maestra** de su servidor lógico de SQL Database para poder consultar **sys.resource_stats** en los ejemplos siguientes.
> 
> 

En este ejemplo se muestra cómo se exponen los datos en esta vista:

    SELECT TOP 10 *
    FROM sys.resource_stats
    WHERE database_name = 'resource1'
    ORDER BY start_time DESC

![La vista de catálogo sys.resource_stats](./media/sql-database-performance-guidance/sys_resource_stats.png)

En el siguiente ejemplo se muestran distintas maneras en que puede usar la vista de catálogo **sys.resource_stats** para obtener información sobre cómo SQL Database usa los recursos:

1. Para ver el uso de recursos de la semana pasada para la base de datos userdb1, puede ejecutar esta consulta:
   
        SELECT *
        FROM sys.resource_stats
        WHERE database_name = 'userdb1' AND
              start_time > DATEADD(day, -7, GETDATE())
        ORDER BY start_time DESC;
2. Para evaluar si la carga de trabajo se ajusta bien al nivel de rendimiento, tiene que explorar en profundidad cada aspecto de las métricas de recursos: CPU, lecturas, escrituras, número de trabajadores y número de sesiones. Esta es una consulta revisada mediante **sys.resource_stats** para notificar los valores medio y máximo de estas métricas de recursos:
   
        SELECT
            avg(avg_cpu_percent) AS 'Average CPU use in percent',
            max(avg_cpu_percent) AS 'Maximum CPU use in percent',
            avg(avg_data_io_percent) AS 'Average physical data I/O use in percent',
            max(avg_data_io_percent) AS 'Maximum physical data I/O use in percent',
            avg(avg_log_write_percent) AS 'Average log write use in percent',
            max(avg_log_write_percent) AS 'Maximum log write use in percent',
            avg(max_session_percent) AS 'Average % of sessions',
            max(max_session_percent) AS 'Maximum % of sessions',
            avg(max_worker_percent) AS 'Average % of workers',
            max(max_worker_percent) AS 'Maximum % of workers'
        FROM sys.resource_stats
        WHERE database_name = 'userdb1' AND start_time > DATEADD(day, -7, GETDATE());
3. Con la información anterior sobre los valores medio y máximo de cada métrica de recursos, puede evaluar si la carga de trabajo se ajusta bien al nivel de rendimiento que eligió. Por lo general, los valores medios de **sys.resource_stats** son una buena referencia para el tamaño de destino. Debería ser su vara de medida principal. Por ejemplo, podría estar usando el nivel de servicio Estándar con el nivel de rendimiento S2. Los porcentajes de uso medio de CPU y de lecturas y escrituras de E/S están por debajo del 40 por ciento, el número medio de trabajadores está por debajo de 50 y el número medio de sesiones está por debajo de 200. La carga de trabajo podría ajustarse al nivel de rendimiento S1. Es fácil ver si la base de datos se ajusta a los límites de trabajadores y de sesión. Para ver si una base de datos se ajusta a un nivel de rendimiento inferior con respecto a CPU, lecturas y escrituras, divida el número de DTU del nivel de rendimiento inferior por el número de DTU de su nivel de rendimiento actual y multiplique el resultado por 100:
   
    **S1 DTU / S2 DTU * 100 = 20 / 50 * 100 = 40**
   
    El resultado es la diferencia porcentual de rendimiento relativa entre los dos niveles de rendimiento. Si el uso de recursos no supera esta cantidad, la carga de trabajo podría ajustarse al nivel de rendimiento inferior. Sin embargo, debe examinar todos los intervalos de valores de uso de recursos y determinar, según el porcentaje, con qué frecuencia se ajustaría la carga de trabajo de la base de datos al nivel de rendimiento inferior. La siguiente consulta proporciona el porcentaje de ajuste por dimensión de recursos según el umbral del 40 % que hemos calculado en este ejemplo.
   
        SELECT
            (COUNT(database_name) - SUM(CASE WHEN avg_cpu_percent >= 40 THEN 1 ELSE 0 END) * 1.0) / COUNT(database_name) AS 'CPU Fit Percent'
            ,(COUNT(database_name) - SUM(CASE WHEN avg_log_write_percent >= 40 THEN 1 ELSE 0 END) * 1.0) / COUNT(database_name) AS 'Log Write Fit Percent'
            ,(COUNT(database_name) - SUM(CASE WHEN avg_data_io_percent >= 40 THEN 1 ELSE 0 END) * 1.0) / COUNT(database_name) AS 'Physical Data IO Fit Percent'
        FROM sys.resource_stats
        WHERE database_name = 'userdb1' AND start_time > DATEADD(day, -7, GETDATE());
   
    En función de su objetivo de nivel de servicio (SLO) de base de datos, puede decidir si la carga de trabajo se ajusta al nivel de rendimiento inferior. Si el SLO de la carga de trabajo de base de datos es del 99,9 % y la consulta anterior devuelve valores mayores que el 99,9 % para las tres dimensiones de recursos, es probable que la carga de trabajo se ajuste al nivel de rendimiento inferior.
   
    El porcentaje de ajuste también ofrece información detallada sobre si debería pasar al siguiente nivel de rendimiento superior para cumplir su SLO. Por ejemplo, userdb1 muestra el siguiente uso de CPU durante la semana pasada:
   
   | Porcentaje medio de CPU | Porcentaje máximo de CPU |
   | --- | --- |
   | 24,5 |100,00 |
   
    El promedio de CPU es aproximadamente un cuarto del límite del nivel de rendimiento, que se ajustaría bien al nivel de rendimiento de la base de datos. Sin embargo, el valor máximo muestra que la base de datos alcanza el límite del nivel de rendimiento. ¿Necesita pasar al siguiente nivel de rendimiento superior? Observe cuántas veces la carga de trabajo alcanza el 100 % y compárelo con el SLO de la carga de trabajo de base de datos.
   
        SELECT
        (COUNT(database_name) - SUM(CASE WHEN avg_cpu_percent >= 100 THEN 1 ELSE 0 END) * 1.0) / COUNT(database_name) AS 'CPU fit percent'
        ,(COUNT(database_name) - SUM(CASE WHEN avg_log_write_percent >= 100 THEN 1 ELSE 0 END) * 1.0) / COUNT(database_name) AS 'Log write fit percent'
        ,(COUNT(database_name) - SUM(CASE WHEN avg_data_io_percent >= 100 THEN 1 ELSE 0 END) * 1.0) / COUNT(database_name) AS 'Physical data I/O fit percent'
        FROM sys.resource_stats
        WHERE database_name = 'userdb1' AND start_time > DATEADD(day, -7, GETDATE());
   
    Si esta consulta devuelve un valor inferior al 99,9 por ciento para cualquiera de las tres dimensiones de recursos, plantéese subir al siguiente nivel de rendimiento o usar técnicas de optimización de aplicaciones para reducir la carga en la base de datos SQL.
4. Este ejercicio también tiene en cuenta el aumento de la carga de trabajo proyectada en el futuro.

En los grupos elásticos, puede supervisar bases de datos individuales del grupo con las técnicas descritas en esta sección. Sin embargo, también puede supervisar el grupo en conjunto. Para más información, consulte el artículo sobre la [supervisión y administración de un grupo de bases de datos elásticas](sql-database-elastic-pool-manage-portal.md).


### <a name="maximum-concurrent-requests"></a>Número máximo de solicitudes simultáneas
Para ver el número de solicitudes simultáneas, ejecute esta consulta Transact-SQL en la base de datos SQL:

    SELECT COUNT(*) AS [Concurrent_Requests]
    FROM sys.dm_exec_requests R

Para analizar la carga de trabajo de una base de datos SQL Server local, modifique esta consulta para filtrar por la base de datos específica que quiere analizar. Por ejemplo, si tiene una base de datos local denominada MyDatabase, esta consulta Transact-SQL devuelve el número de solicitudes simultáneas en esa base de datos.

    SELECT COUNT(*) AS [Concurrent_Requests]
    FROM sys.dm_exec_requests R
    INNER JOIN sys.databases D ON D.database_id = R.database_id
    AND D.name = 'MyDatabase'

Se trata simplemente de una instantánea en un solo momento dado. Para comprender mejor la carga de trabajo y los requisitos de solicitudes simultáneas, debe recopilar muchas muestras durante un período de tiempo.

### <a name="maximum-concurrent-logins"></a>Número máximo de inicios de sesión simultáneos
Puede analizar los patrones de usuario y aplicación para hacerse una idea de la frecuencia de los inicios de sesión. También podría ejecutar cargas reales en un entorno de prueba para asegurarse de que no está alcanzando este u otros límites descritos en este artículo. No hay una única consulta o vista de administración dinámica (DMV) que pueda mostrar el número o el historial de inicios de sesión simultáneos.

Si varios clientes usan la misma cadena de conexión, el servicio autentica cada inicio de sesión. Si 10 usuarios se conectan de forma simultánea a una base de datos con el mismo nombre de usuario y contraseña, habrá 10 conexiones simultáneas. Este límite se aplica solo a la duración del inicio de sesión y la autenticación. Si los mismos 10 usuarios se conectan a la base de datos de forma secuencial, el número de inicios de sesión simultáneos nunca será superior a uno.

> [!NOTE]
> Este límite no se aplica actualmente a las bases de datos de grupos elásticos.
> 
> 

### <a name="maximum-sessions"></a>Número máximo de sesiones
Para ver el número de sesiones activas actuales, ejecute esta consulta de Transact-SQL en la base de datos SQL:

    SELECT COUNT(*) AS [Sessions]
    FROM sys.dm_exec_connections

Si va a analizar una carga de trabajo de SQL Server local, modifique la consulta para centrarse en una base de datos específica. Esta consulta le ayuda a determinar las posibles necesidades de sesión para esa base de datos si la mueve a Azure SQL Database.

    SELECT COUNT(*)  AS [Sessions]
    FROM sys.dm_exec_connections C
    INNER JOIN sys.dm_exec_sessions S ON (S.session_id = C.session_id)
    INNER JOIN sys.databases D ON (D.database_id = S.database_id)
    WHERE D.name = 'MyDatabase'

Nuevamente, estas consultas devuelven un número puntual. Si recopila varias muestras con el tiempo, tendrá una mejor comprensión del uso de la sesión.

Para el análisis de SQL Database, puede obtener estadísticas históricas sobre las sesiones consultando la vista [sys.resource_stats](https://msdn.microsoft.com/library/dn269979.aspx) y revisando la columna **active_session_count**. 

## <a name="next-steps"></a>pasos siguientes

- Ajuste los índices de la base de datos automáticamente y consulte los planes de ejecución de la consulta mediante [Azure SQL Database Automatic Tuning](sql-database-automatic-tuning.md).
- Supervise el rendimiento de la base de datos automáticamente mediante [Intelligent Insights de Azure SQL](sql-database-intelligent-insights.md). Esta característica proporciona información de diagnóstico y análisis de la causa principal de los problemas de rendimiento.
