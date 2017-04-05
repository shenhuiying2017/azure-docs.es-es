---
title: "Rendimiento de Azure SQL Database para bases de datos únicas | Microsoft Docs"
description: "Este artículo puede ayudarle a determinar qué nivel de servicio elegir para la aplicación. También se recomiendan formas de optimizar la aplicación para obtener el máximo partido de Azure SQL Database."
services: sql-database
documentationcenter: na
author: CarlRabeler
manager: jhubbard
editor: 
ms.assetid: dd8d95fa-24b2-4233-b3f1-8e8952a7a22b
ms.service: sql-database
ms.custom: monitor and tune
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-management
ms.date: 03/06/2017
ms.author: carlrab
translationtype: Human Translation
ms.sourcegitcommit: 07635b0eb4650f0c30898ea1600697dacb33477c
ms.openlocfilehash: 03d38dbce86711395a967cf8bad440fd50a38631
ms.lasthandoff: 03/28/2017


---
# <a name="azure-sql-database-and-performance-for-single-databases"></a>Azure SQL Database y rendimiento de bases de datos únicas
Azure SQL Database ofrece cuatro [niveles de servicio](sql-database-service-tiers.md): Básico, Estándar, Premium y Premium RS. Cada nivel de servicio aísla estrictamente los recursos que puede usar la base de datos SQL y garantiza un rendimiento predecible para ese nivel de servicio. En este artículo se ofrecen instrucciones que pueden ayudarle a elegir el nivel de servicio para su aplicación. También analizamos formas de optimizar la aplicación para obtener el máximo provecho de Azure SQL Database.

> [!NOTE]
> Este artículo se centra en la guía de rendimiento para bases de datos únicas de Azure SQL Database. Para obtener instrucciones sobre rendimiento relativas a los grupos elásticos, consulte las [consideraciones de precio y rendimiento para grupos elásticos](sql-database-elastic-pool-guidance.md). Observe, sin embargo, que muchas de las recomendaciones de optimización de este artículo se pueden aplicar a bases de datos de un grupo elástico y obtener ventajas de rendimiento similares.
>
>

## <a name="why-service-tiers"></a>¿Por qué niveles de servicio?
Aunque cada carga de trabajo de base de datos puede variar, los niveles de servicio tienen como fin proporcionar una previsión del rendimiento en diversos niveles de rendimiento. Los clientes con requisitos de recursos de base de datos a gran escala pueden trabajar en un entorno informático más dedicado.

### <a name="common-service-tier-use-cases"></a>Casos de uso comunes de niveles de servicio
#### <a name="basic"></a>Básico
* **Acaba de empezar con Azure SQL Database**. Las aplicaciones que están en desarrollo no necesitan habitualmente altos niveles de rendimiento. Las bases de datos básicas son un entorno adecuado para el desarrollo de bases de datos a un precio bajo.
* **Tiene una base de datos con un único usuario**. Las aplicaciones que asocian un único usuario a una base de datos normalmente no tienen grandes requisitos de simultaneidad y rendimiento. Estas aplicaciones son candidatas al nivel de servicio básico.

#### <a name="standard"></a>Estándar
* **La base de datos tiene varias solicitudes simultáneas**. Las aplicaciones que atienden a más de un usuario a la vez suelen necesitar niveles de rendimiento más altos. Por ejemplo, los sitios web que reciben tráfico moderado o las aplicaciones de departamento que requieren más recursos son buenos candidatos al nivel de servicio Estándar.

#### <a name="premium"></a>Premium
La mayoría de los casos de uso del nivel de servicio Premium tienen una o varias de estas características:

* **Carga máxima elevada**. Una aplicación que requiere mucha CPU, memoria o entrada/salida (E/S) para completar sus operaciones necesita un nivel de rendimiento alto dedicado. Por ejemplo, una operación de base de datos que se sabe que consume varios núcleos de CPU durante mucho tiempo es candidata al nivel de servicio Premium.
* **Muchas solicitudes simultáneas**. Algunas aplicaciones de base de datos atienden muchas solicitudes simultáneas, por ejemplo, cuando dan servicio a un sitio web con un volumen elevado de tráfico. Los niveles de servicio Básico y Estándar limitan el número de solicitudes simultáneas por base de datos. Las aplicaciones que requieren más conexiones necesitarían elegir un tamaño de reserva adecuado para controlar el número máximo de solicitudes necesarias.
* **Baja latencia**. Algunas aplicaciones necesitan garantizar una respuesta de la base de datos en un tiempo mínimo. Si se llama a un procedimiento almacenado determinado como parte de una operación de cliente más amplia, podría ser necesario volver de esa llamada en no más de 20 milisegundos el 99 % del tiempo. Este tipo de aplicación se beneficia del nivel de servicio Premium para asegurarse de que haya la capacidad de proceso necesaria.

* **Premium RS**. Diseñado para clientes que tienen cargas de trabajo intensivas de E/S pero que no requieren las mayores garantías de disponibilidad. Los ejemplos incluyen las pruebas en cargas de trabajo de alto rendimiento, o en una carga de trabajo analítico en el que la base de datos no es el sistema de registro.

El nivel de servicio que necesite para su base de datos SQL depende de los requisitos de carga máxima para cada dimensión de recursos. Algunas aplicaciones usan pocas cantidades de un recurso pero tienen necesidades considerables de otros.

## <a name="service-tier-capabilities-and-limits"></a>Límites y capacidades de nivel de servicio
Cada nivel de servicio y nivel de rendimiento están asociados a distintos límites y características de rendimiento. En la tabla siguiente se describen estas características para una base de datos única.

[!INCLUDE [SQL DB service tiers table](../../includes/sql-database-service-tiers-table.md)]

> [!IMPORTANT]
> Los clientes que usan los niveles de rendimiento P11 y P15 pueden usar hasta 4 TB de almacenamiento incluido sin cargo adicional. Esta opción de 4 TB se encuentra actualmente en versión preliminar pública en las siguientes regiones: Este de EE. UU. 2, oeste de EE. UU., Europa Occidental, Asia Suroriental, Japón Oriental, Este de Australia, centro de Canadá y Canadá Oriental.
>

### <a name="maximum-in-memory-oltp-storage"></a>Almacenamiento máximo OLTP en memoria
Puede usar la vista **sys.dm_db_resource_stats** para supervisar el uso del almacenamiento en memoria de Azure. Para más información sobre la supervisión, consulte [Supervisión del almacenamiento OLTP en memoria](sql-database-in-memory-oltp-monitoring.md).

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

## <a name="monitor-resource-use"></a>Supervisión del uso de recursos

Puede supervisar el uso de recursos con [Información de rendimiento de consultas de SQL Database](sql-database-query-performance.md) y [Almacén de consultas](https://msdn.microsoft.com/library/dn817826.aspx).

También puede supervisar el uso con estas dos vistas:

* [sys.dm_db_resource_stats](https://msdn.microsoft.com/library/dn800981.aspx)
* [sys.resource_stats](https://msdn.microsoft.com/library/dn269979.aspx)

### <a name="sysdmdbresourcestats"></a>sys.dm_db_resource_stats
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

### <a name="sysresourcestats"></a>Sys.resource_stats
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

## <a name="tune-your-application"></a>Optimización de la aplicación
En instancias de SQL Server locales tradicionales, el proceso de planeamiento inicial de la capacidad con frecuencia está separado del proceso de ejecución de una aplicación en producción. Primero se adquieren las licencias de hardware y productos y luego se ajusta el rendimiento. Cuando se usa Azure SQL Database, es una buena idea entrelazar el proceso de ejecutar una aplicación y optimizarla. Con el modelo de pago por capacidad a petición, puede optimizar la aplicación para usar el número mínimo de recursos necesarios, en lugar de aprovisionar en exceso el hardware en función de las estimaciones de planes de crecimiento futuro para una aplicación, que con frecuencia son incorrectas. Algunos clientes pueden decidir no optimizar una aplicación y, en su lugar, eligen aprovisionar en exceso los recursos de hardware. Este enfoque puede ser una buena idea si no quiere cambiar una aplicación clave durante un período de ocupación. Sin embargo, la optimización de una aplicación puede minimizar los requisitos de recursos y reducir las facturas mensuales cuando se usan los niveles de servicio en Azure SQL Database.

### <a name="application-characteristics"></a>Características de la aplicación
Aunque los niveles de servicio de Azure SQL Database están diseñados para mejorar la estabilidad del rendimiento y la previsibilidad de una aplicación, algunos procedimientos recomendados pueden ayudarle a optimizar la aplicación para un mejor aprovechamiento de los recursos en un nivel de rendimiento. Aunque muchas aplicaciones tienen importantes ganancias de rendimiento con solo cambiar a un nivel de rendimiento o de servicio superior, otras aplicaciones necesitan ajustes adicionales para beneficiarse de un nivel de servicio más alto. Para aumentar el rendimiento, puede realizar ajustes adicionales en las aplicaciones para que tengan estas características:

* **Aplicaciones que tienen un rendimiento lento debido a un comportamiento "comunicativo"**. Las aplicaciones comunicativas realizan excesivas operaciones de acceso a los datos que son sensibles a la latencia de red. Para reducir el número de operaciones de acceso a datos de la base de datos SQL, puede que tenga que modificar estos tipos de aplicaciones. Por ejemplo, puede mejorar el rendimiento de la aplicación mediante técnicas como el procesamiento por lotes de consultas ad-hoc o el movimiento de las consultas a procedimientos almacenados. Para más información, consulte [Consultas por lotes](#batch-queries).
* **Bases de datos con una carga de trabajo intensiva que no se admite en una sola máquina**. Las bases de datos que superan los recursos del más alto nivel de rendimiento Premium podrían beneficiarse de escalar horizontalmente la carga de trabajo. Para más información, consulte [Particionamiento entre bases de datos](#cross-database-sharding) y [Creación de particiones funcional](#functional-partitioning).
* **Aplicaciones que tienen consultas poco óptimas**. Puede que las aplicaciones, especialmente las de la capa de acceso a datos, que tienen consultas poco optimizadas no se beneficien de un mayor nivel de rendimiento. Esto incluye consultas que carecen de una cláusula WHERE, con índices que faltan o tienen estadísticas anticuadas. Estas aplicaciones se benefician de las técnicas de optimización del rendimiento de consultas estándar. Para más información, consulte [Índices que faltan](#missing-indexes) y [Optimización de consultas y sugerencias](#query-tuning-and-hinting).
* **Aplicaciones que tienen un diseño de acceso a datos poco óptimo**. Puede que las aplicaciones que tienen problemas inherentes de simultaneidad de acceso a datos, por ejemplo, interbloqueos, no se beneficien de un nivel de rendimiento más alto. Considere la posibilidad de reducir los viajes de ida y vuelta a Azure SQL Database almacenando en caché los datos del lado cliente con el Servicio de almacenamiento en caché de Azure u otra tecnología de almacenamiento en caché. Consulte [Almacenamiento en caché de la capa de aplicación](#application-tier-caching).

## <a name="tuning-techniques"></a>Técnicas de optimización
En esta sección, examinamos algunas técnicas que puede usar para optimizar Azure SQL Database para obtener el mejor rendimiento de la aplicación y ejecutarla con el menor nivel de rendimiento posible. Algunas de estas técnicas coinciden con los procedimientos recomendados de optimización tradicionales de SQL Server, pero otras son específicas de Azure SQL Database. En algunos casos, puede examinar los recursos consumidos en una base de datos para encontrar áreas de mejora adicional y ampliar las técnicas de SQL Server tradicionales para trabajar en Azure SQL Database.

### <a name="azure-portal-tools"></a>Herramientas del Portal de Azure
Las siguientes herramientas en Azure Portal pueden ayudarlo a analizar y corregir problemas de rendimiento con SQL Database:

* [Query Performance Insight](sql-database-query-performance.md)
* [Asesor de Base de datos SQL](sql-database-advisor.md)

En Azure Portal puede encontrar más información sobre estas dos herramientas y cómo usarlas. Para diagnosticar y corregir problemas de forma eficaz, se recomienda probar primero las herramientas en Azure Portal. Es recomendable que use los enfoques de optimización manual que describimos a continuación, para índices que faltan y optimización de consultas, en casos especiales.

### <a name="missing-indexes"></a>Índices que faltan
Un problema común del rendimiento de las bases de datos OLTP está relacionado con el diseño físico de la base de datos. A menudo, los esquemas de base de datos se diseñan y se entregan sin realizar pruebas a escala (ya sea en la carga o en el volumen de datos). Lamentablemente, el rendimiento de un plan de consultas puede ser aceptable a pequeña escala, pero se puede degradar sustancialmente cuando se enfrenta a los volúmenes de datos del nivel de producción. El origen más común de este problema es la falta de índices adecuados para satisfacer los filtros u otras restricciones en una consulta. A menudo, la falta de índices se manifiesta como un recorrido de tabla cuando podría ser suficiente una búsqueda de índice.

En este ejemplo, el plan de consulta seleccionado usa una exploración cuando bastaría con una búsqueda:

    DROP TABLE dbo.missingindex;
    CREATE TABLE dbo.missingindex (col1 INT IDENTITY PRIMARY KEY, col2 INT);
    DECLARE @a int = 0;
    SET NOCOUNT ON;
    BEGIN TRANSACTION
    WHILE @a < 20000
    BEGIN
        INSERT INTO dbo.missingindex(col2) VALUES (@a);
        SET @a += 1;
    END
    COMMIT TRANSACTION;
    GO
    SELECT m1.col1
    FROM dbo.missingindex m1 INNER JOIN dbo.missingindex m2 ON(m1.col1=m2.col1)
    WHERE m1.col2 = 4;

![Plan de consulta con índices que faltan](./media/sql-database-performance-guidance/query_plan_missing_indexes.png)

Azure SQL Database puede ayudarle a encontrar y corregir condiciones de falta de índice comunes. Las DMV que se crean en Azure SQL Database examinan compilaciones de consultas en las que un índice reduciría considerablemente el costo estimado de ejecutar una consulta. Durante la ejecución de las consultas, SQL Database hace un seguimiento de la frecuencia con que se ejecuta cada plan de consulta, así como de la diferencia estimada entre el plan de consulta en ejecución y el imaginario en el que existía ese índice. Estas DMV se pueden usar para realizar suposiciones rápidas sobre qué cambios en el diseño de la base de datos física podrían mejorar el costo de la carga de trabajo general de una base de datos y su carga de trabajo real.

Esta consulta se puede usar para evaluar los posibles índices que falten:

    SELECT CONVERT (varchar, getdate(), 126) AS runtime,
        mig.index_group_handle, mid.index_handle,
        CONVERT (decimal (28,1), migs.avg_total_user_cost * migs.avg_user_impact *
                (migs.user_seeks + migs.user_scans)) AS improvement_measure,
        'CREATE INDEX missing_index_' + CONVERT (varchar, mig.index_group_handle) + '_' +
                  CONVERT (varchar, mid.index_handle) + ' ON ' + mid.statement + '
                  (' + ISNULL (mid.equality_columns,'')
                  + CASE WHEN mid.equality_columns IS NOT NULL
                              AND mid.inequality_columns IS NOT NULL
                         THEN ',' ELSE '' END + ISNULL (mid.inequality_columns, '')
                  + ')'
                  + ISNULL (' INCLUDE (' + mid.included_columns + ')', '') AS create_index_statement,
        migs.*,
        mid.database_id,
        mid.[object_id]
    FROM sys.dm_db_missing_index_groups AS mig
    INNER JOIN sys.dm_db_missing_index_group_stats AS migs
        ON migs.group_handle = mig.index_group_handle
    INNER JOIN sys.dm_db_missing_index_details AS mid
        ON mig.index_handle = mid.index_handle
    ORDER BY migs.avg_total_user_cost * migs.avg_user_impact * (migs.user_seeks + migs.user_scans) DESC

En este ejemplo, la consulta ha dado como resultado esta sugerencia:

    CREATE INDEX missing_index_5006_5005 ON [dbo].[missingindex] ([col2])  

Después de crearla, esa misma instrucción SELECT elige un plan diferente, que usa una búsqueda en lugar de una exploración y, luego, ejecuta el plan de forma más eficaz:

![Plan de consulta con índices corregidos](./media/sql-database-performance-guidance/query_plan_corrected_indexes.png)

El principal dato es que la capacidad de E/S de un sistema compartido es más limitada que la de una máquina servidor dedicada. Es de especial valor minimizar la E/S innecesaria para sacar el máximo partido del sistema dentro de la DTU de cada nivel de rendimiento de los niveles de servicio en Azure SQL Database. La elección adecuada de las opciones de diseño de bases de datos físicas puede mejorar considerablemente la latencia de las consultas individuales y la capacidad de procesamiento de solicitudes simultáneas que puede realizar por unidad de escalado, así como minimizar los costos necesarios para satisfacer la consulta. Para obtener más información acerca de las DMV de índices que faltan, consulte [sys.dm_db_missing_index_details](https://msdn.microsoft.com/library/ms345434.aspx).

### <a name="query-tuning-and-hinting"></a>Optimización de consultas y sugerencias
El optimizador de consultas de Azure SQL Database es similar al optimizador de consultas tradicional de SQL Server. La mayoría de los procedimientos recomendados para optimizar consultas y entender las limitaciones del modelo de razonamiento para el optimizador de consultas se aplica también a Azure SQL Database. Si optimiza las consultas en Azure SQL Database, puede obtener el beneficio adicional de reducir las demandas de recursos agregados. La aplicación podría ejecutarse con un menor costo que una equivalente sin optimizar porque se puede ejecutar con un nivel de rendimiento menor.

Un ejemplo común en SQL Server y que también se aplica a Azure SQL Database es cómo el optimizador de consultas examina los parámetros. Durante la compilación, el optimizador de consultas evalúa el valor actual de un parámetro para determinar si puede generar un plan de consulta más óptimo. Aunque con frecuencia esta estrategia puede conducir a un plan de consultas bastante más rápido que un plan compilado sin valores de parámetros conocidos, actualmente no funciona muy bien en SQL Server y en Azure SQL Database. A veces el parámetro no se examina y otras veces sí, pero el plan generado es poco óptimo para el conjunto completo de valores de parámetros de una carga de trabajo. Microsoft incluye sugerencias de consulta (directivas) para que pueda especificar más deliberadamente la intención y reemplazar el comportamiento predeterminado de examen de parámetros. A menudo, el uso de sugerencias puede corregir los casos en los que el comportamiento predeterminado de SQL Server o Azure SQL Database es imperfecto para una carga de trabajo de un cliente determinado.

En el ejemplo siguiente se muestra cómo el procesador de consultas puede generar un plan poco óptimo para los requisitos de rendimiento y recursos. Este ejemplo también muestra que si usa una sugerencia de consulta, puede reducir los requisitos de recursos y el tiempo de ejecución de consultas de su base de datos SQL:

    DROP TABLE psptest1;
    CREATE TABLE psptest1(col1 int primary key identity, col2 int, col3 binary(200));

    DECLARE @a int = 0;
    SET NOCOUNT ON;
    BEGIN TRANSACTION
    WHILE @a < 20000
    BEGIN
        INSERT INTO psptest1(col2) values (1);
        INSERT INTO psptest1(col2) values (@a);
        SET @a += 1;
    END
    COMMIT TRANSACTION
    CREATE INDEX i1 on psptest1(col2);
    GO

    CREATE PROCEDURE psp1 (@param1 int)
    AS
    BEGIN
        INSERT INTO t1 SELECT * FROM psptest1
        WHERE col2 = @param1
        ORDER BY col2;
    END
    GO

    CREATE PROCEDURE psp2 (@param2 int)
    AS
    BEGIN
        INSERT INTO t1 SELECT * FROM psptest1 WHERE col2 = @param2
        ORDER BY col2
        OPTION (OPTIMIZE FOR (@param2 UNKNOWN))
    END
    GO

    CREATE TABLE t1 (col1 int primary key, col2 int, col3 binary(200));
    GO

El código de configuración crea una tabla que contiene una distribución de datos sesgados. El plan de consulta óptimo varía en función del parámetro que seleccione. Desafortunadamente, el comportamiento de almacenar en caché el plan no siempre recompila la consulta según el valor de parámetro más común. Por lo tanto, se puede almacenar en caché un plan poco óptimo y usarse para muchos valores, aunque un plan diferente podría ser una mejor opción por término medio. A continuación, el plan de consulta crea dos procedimientos almacenados que son idénticos, salvo que uno tiene una sugerencia de consulta especial.

**Ejemplo (parte 1):**

    -- Prime Procedure Cache with scan plan
    EXEC psp1 @param1=1;
    TRUNCATE TABLE t1;

    -- Iterate multiple times to show the performance difference
    DECLARE @i int = 0;
    WHILE @i < 1000
    BEGIN
        EXEC psp1 @param1=2;
        TRUNCATE TABLE t1;
        SET @i += 1;
    END

**Ejemplo (parte 2):**

(Se recomienda que espere al menos 10 minutos antes de empezar la parte 2 del ejemplo, para que los resultados sean distintos en los datos de telemetría resultantes).

    EXEC psp2 @param2=1;
    TRUNCATE TABLE t1;

    DECLARE @i int = 0;
    WHILE @i < 1000
    BEGIN
        EXEC psp2 @param2=2;
        TRUNCATE TABLE t1;
        SET @i += 1;
    END

Cada parte de este ejemplo intenta ejecutar 1000 veces una instrucción insert parametrizada (para generar una carga suficiente para usarla como un conjunto de datos de prueba). Al ejecutar los procedimientos almacenados, el procesador de consultas examina el valor de los parámetros pasados al procedimiento durante su primera compilación (lo que se conoce como examinar parámetros). El procesador almacena en caché el plan resultante y se usa para invocaciones posteriores, aunque el valor del parámetro sea diferente. El plan óptimo no podría utilizarse en todos los casos. En ocasiones, es necesario guiar al optimizador para que seleccione un plan que sea mejor para el caso medio en lugar de para el caso específico de cuando la consulta se compila por primera vez. En este ejemplo, el plan inicial genera un plan de "exploración" que lee todas las filas para encontrar todos los valores que coinciden con el parámetro.

![Optimización de consultas mediante un plan de exploración](./media/sql-database-performance-guidance/query_tuning_1.png)

Como el procedimiento se ejecutó con el valor 1, el plan resultante era óptimo para 1 pero poco óptimo para todos los demás valores de la tabla. Es probable que el resultado no sea lo que esperaría si seleccionara cada plan de manera aleatoria, dado que el plan se ejecuta más despacio y usa menos recursos.

Si ejecuta la prueba con `SET STATISTICS IO` establecido en `ON`, el trabajo de exploración lógica en este ejemplo se realiza en segundo plano. Puede ver que hay 1148 lecturas que realiza el plan (lo que es ineficaz, si el caso medio es devolver solo una fila):

![Optimización de consultas mediante una exploración lógica](./media/sql-database-performance-guidance/query_tuning_2.png)

La segunda parte del ejemplo usa una sugerencia de consulta para indicar al optimizador que use un valor específico durante el proceso de compilación. En este caso, obliga al procesador de consultas a omitir el valor que se pasa como parámetro, y en su lugar a asumir `UNKNOWN`. Este valor hace referencia a un valor que tiene la frecuencia media en la tabla (omitiendo el sesgo). El plan resultante está basado en búsquedas, es más rápido y usa menos recursos, como términos medio, que el plan de la parte 1 de este ejemplo:

![Optimización de consultas mediante una sugerencia de consulta](./media/sql-database-performance-guidance/query_tuning_3.png)

Puede ver el efecto en la tabla **sys.resource_stats** (hay un retraso desde el momento en que ejecuta la prueba y cuando los datos llenan la tabla). En este ejemplo, la parte 1 se ejecutó en el período de tiempo de 22:25:00 y la parte 2 se ejecutó a las 22:35:00. El período de tiempo anterior usó más recursos en ese período que el posterior (debido a las mejoras de eficiencia del plan).

    SELECT TOP 1000 *
    FROM sys.resource_stats
    WHERE database_name = 'resource1'
    ORDER BY start_time DESC

![Resultados del ejemplo de optimización de consultas](./media/sql-database-performance-guidance/query_tuning_4.png)

> [!NOTE]
> Aunque el volumen en este ejemplo es deliberadamente pequeño, el efecto de parámetros poco óptimos puede ser considerable, especialmente en bases de datos más grandes. La diferencia, en casos extremos, puede estar entre segundos, para los casos más rápidos, y horas, para los más lentos.
>
>

Puede examinar **sys.resource_stats** para determinar si el recurso de una prueba usa más o menos recursos que otra prueba. Al comparar los datos, separe el tiempo de pruebas para que no se encuentren en el mismo período de 5 minutos en la vista **sys.resource_stats**. El objetivo del ejercicio es minimizar la cantidad total de recursos usados, no minimizar los recursos máximos. Por lo general, al optimizar la latencia de un fragmento de código, también se reduce el consumo de recursos. Asegúrese de que los cambios realizados en una aplicación sean necesarios y que no afecten negativamente a la experiencia del cliente para alguien que podría estar usando sugerencias de consulta en la aplicación.

Si una carga de trabajo contiene un conjunto de consultas repetidas, con frecuencia tiene sentido capturar y validar la idoneidad de esas elecciones del plan, ya que controlarán la unidad de tamaño mínima de los recursos necesaria para hospedar la base de datos. Después de validarlo, vuelva a examinar de vez en cuando los planes para asegurarse de que no se han degradado. Puede aprender más sobre las [sugerencias de consulta (Transact-SQL)](https://msdn.microsoft.com/library/ms181714.aspx).

### <a name="cross-database-sharding"></a>Particionamiento entre bases de datos
Como Azure SQL Database se ejecuta en hardware estándar, los límites de capacidad para una base de datos única son inferiores a los de una instalación local de SQL Server tradicional. Algunos clientes usan técnicas de particionamiento para repartir las operaciones de base de datos entre varias bases de datos cuando las operaciones no entran en los límites de una base de datos única en Azure SQL Database. La mayoría de los clientes que usan técnicas de particionamiento en Azure SQL Database divide sus datos en una única dimensión entre varias bases de datos. En este enfoque, debe comprender que las aplicaciones OLTP a menudo realizan transacciones que se aplican a una sola fila o a un pequeño grupo de filas del esquema.

> [!NOTE]
> Tenga en cuenta que Base de datos SQL ahora proporciona una biblioteca para ayudar con el particionamiento. Para obtener más información, consulte [Información general de la biblioteca de cliente de bases de datos elásticas](sql-database-elastic-database-client-library.md).
>
>

Por ejemplo, si una base de datos tiene el nombre del cliente, el pedido y los detalles del pedido (como la base de datos tradicional de ejemplo Northwind que se incluye con SQL Server), podría dividir estos datos en varias bases de datos mediante la agrupación de un cliente con el pedido relacionado y la información detallada del pedido. Puede garantizar que los datos del cliente permanecen en una base de datos única. La aplicación dividiría los distintos clientes entre las bases de datos, repartiendo la carga eficazmente entre varias bases de datos. El particionamiento no solo permite que los clientes eviten el límite de tamaño máximo de la base de datos, sino también que Azure SQL Database procese cargas de trabajo que sean mucho mayores que los límites de los distintos niveles de rendimiento, siempre y cuando cada base de datos se ajuste a sus DTU.

Aunque el particionamiento de base de datos no reduce la capacidad de recursos agregados para una solución, es muy eficaz a la hora de admitir soluciones muy grandes que se distribuyen entre varias bases de datos. Cada base de datos se puede ejecutar con un nivel de rendimiento diferente para admitir bases de datos "eficaces" muy grandes con requisitos elevados de recursos.

### <a name="functional-partitioning"></a>Creación de particiones funcional
Los usuarios de SQL Server suelen combinar varias funciones en una base de datos única. Por ejemplo, si una aplicación contiene lógica para administrar el inventario de un almacén, esa base de datos podría contener lógica asociada con el inventario, el seguimiento de los pedidos de compra, los procedimientos almacenados y las vistas indizadas o materializadas que administran los informes de fin de me. Esta técnica facilita la administración de la base de datos para operaciones como la copia de seguridad, pero también requiere ajustar el tamaño del hardware para administrar la carga máxima en todas las funciones de una aplicación.

Si usa una arquitectura de escalado horizontal en Azure SQL Database, es una buena idea repartir las distintas funciones de una aplicación en diferentes bases de datos. Mediante esta técnica, cada aplicación se escala de forma independiente. A medida que una aplicación realiza una actividad mayor (y aumenta la carga en la base de datos), el administrador puede elegir niveles de rendimiento independientes para cada función de la aplicación. En el límite, una aplicación con esta arquitectura puede ser más grande de lo que puede controlar una única máquina estándar por lo que la carga se reparte entre varias máquinas.

### <a name="batch-queries"></a>Consultas por lotes
Para las aplicaciones que tienen acceso a datos mediante consultas ad hoc frecuentes de gran volumen, una parte sustancial del tiempo de respuesta se dedica a la comunicación de red entre la capa de aplicación y la de Azure SQL Database. Incluso si la aplicación y Azure SQL Database residen en el mismo centro de datos, la latencia de red entre ambas podría verse aumentada por un número elevado de operaciones de acceso a datos. Para reducir los recorridos de ida y vuelta de red en las operaciones de acceso a datos, puede usar la opción para procesar por lotes todas las consultas ad hoc o para compilarlas como procedimientos almacenados. Si procesa por lotes las consultas ad hoc, puede enviar varias consultas como un lote grande en un solo recorrido a Azure SQL Database. Si compila las consultas ad hoc en un procedimiento almacenado, podría lograr el mismo resultado que si las procesa por lotes. El uso de un procedimiento almacenado también ofrece la ventaja de aumentar la probabilidad de almacenar en caché los planes de consulta en Azure SQL Database de modo que pueda usar de nuevo el procedimiento almacenado.

Algunas aplicaciones requieren operaciones de escritura intensivas. En ocasiones se puede reducir la carga total de E/S en una base de datos si se considera cómo procesar por lotes las escrituras juntas. Con frecuencia es tan sencillo como usar transacciones explícitas en lugar de transacciones de confirmación automática dentro de los procedimientos almacenados y lotes ad hoc. Para ver una evaluación de las distintas técnicas que se pueden usar, consulte [Técnicas de procesamiento por lotes para aplicaciones de SQL Database en Azure](https://msdn.microsoft.com/library/windowsazure/dn132615.aspx). Experimente con su propia carga de trabajo para encontrar el modelo adecuado de procesamiento por lotes. Asegúrese de entender que un modelo puede tener garantías de coherencia transaccional ligeramente diferentes. Para encontrar la carga de trabajo adecuada que minimiza el uso de recursos, es necesario encontrar la combinación correcta de coherencia y rendimiento.

### <a name="application-tier-caching"></a>Almacenamiento en caché de la capa de aplicación
Algunas aplicaciones de base de datos tienen cargas de trabajo con operaciones de lectura intensivas. El almacenamiento en caché de las capas podría reducir la carga en la base de datos y también, posiblemente, el nivel de rendimiento necesario para admitir una base de datos con Azure SQL Database. Con [Azure Redis Cache](https://azure.microsoft.com/services/cache/), si tiene una carga de trabajo con muchas operaciones de lectura, puede leer los datos una vez (o quizás una vez por máquina de nivel de aplicación, según cómo esté configurada) y luego almacenar esos datos fuera de la base de datos SQL. Se trata de una forma de reducir la carga de la base de datos (CPU y E/S de lectura), pero tiene efectos sobre la coherencia transaccional porque los datos que se leen de la caché podrían estar desincronizados con respectos a los datos de la base de datos. Aunque en muchas aplicaciones algún nivel de incoherencia es aceptable, esto no se aplica a todas las cargas de trabajo. Debería comprender totalmente los requisitos de una aplicación antes de emplear una estrategia de almacenamiento en caché de la capa de aplicación.

## <a name="next-steps"></a>Pasos siguientes
* Para más información sobre los niveles de servicio, consulte [Opciones y rendimiento de SQL Database](sql-database-service-tiers.md)
* Para obtener más información sobre los grupos elásticos, consulte [¿Qué es un grupo elástico de Azure?](sql-database-elastic-pool.md)
* Para información sobre el rendimiento y los grupos elásticos, consulte [¿Cuándo se debe utilizar un grupo elástico?](sql-database-elastic-pool-guidance.md)

