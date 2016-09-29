<properties
	pageTitle="Guía de rendimiento de Base de datos SQL de Azure"
	description="Este tema proporciona instrucciones para ayudarle a determinar qué nivel de servicio es el adecuado para su aplicación, así como recomendaciones para ajustarla y obtener el máximo partido de Base de datos SQL de Azure."
	services="sql-database"
	documentationCenter="na"
	authors="CarlRabeler"
	manager="jhubbard"
	editor="" />


<tags
	ms.service="sql-database"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="na"
	ms.workload="data-management"
	ms.date="09/13/2016"
	ms.author="carlrab" />

# Guía de rendimiento de Base de datos SQL de Azure

## Información general

Base de datos SQL de Microsoft Azure tiene tres [niveles de servicio](sql-database-service-tiers.md), Básico, Estándar y Premium. Todos aíslan de forma estricta el recurso proporcionado a Base de datos SQL Azure y garantizan un rendimiento predecible. El rendimiento se mide en DTU. Para más información sobre las DTU, consulte [Qué es una DTU](sql-database-what-is-a-dtu.md). En este artículo se proporcionan instrucciones para elegir el nivel de servicio para su aplicación, así como recomendaciones para optimizarla y obtener el máximo partido de Azure SQL Database.

>[AZURE.NOTE] Este artículo se centra en la guía de rendimiento para bases de datos única en Base de datos SQL. Para obtener instrucciones sobre rendimiento relativas a los grupos elásticos, consulte [Consideraciones de precio y rendimiento para grupos elásticos](sql-database-elastic-pool-guidance.md). Sin embargo, tenga en cuenta que muchas de las recomendaciones de optimización de este artículo para una base de datos única pueden aplicarse a las bases de datos de un grupo elástico con ventajas de rendimiento similares.

- **Básico** El nivel de servicio Básico está diseñado para prevenir mejor el rendimiento de cada base de datos hora tras hora. La DTU de una base de datos básica está diseñada para proporcionar recursos suficientes para que bases de datos pequeñas sin solicitudes simultáneas tengan un buen rendimiento.
- **Estándar** El nivel de servicio Estándar ofrece una capacidad mejorada para prevenir el rendimiento y aumenta el nivel para bases de datos con varias solicitudes simultáneas, tales como aplicaciones web y de grupo de trabajo. El uso de una base de datos de nivel de servicio Estándar permite ajustar el tamaño de su aplicación de base de datos en función de un rendimiento predecible minuto a minuto.
- **Premium** El nivel de servicio Premium proporciona un rendimiento predecible segundo tras segundo para cada base de datos Premium. Usar el nivel de servicio Premium permite ajustar el tamaño de la aplicación de base de datos en función de la carga máxima de esa base de datos y elimina aquellos casos en los que la variación de rendimiento puede hacer que consultas pequeñas tarden más tiempo del esperado en operaciones sensibles a la latencia. Este modelo puede simplificar en gran medida los ciclos de desarrollo y validación del producto necesarios para aquellas aplicaciones que necesitan hacer declaraciones firmes sobre las necesidades máximas de recursos, variación de rendimiento o latencia de las consultas.

La configuración del nivel de rendimiento en cada nivel de servicio permite pagar solo por la capacidad que necesita y [ampliarla o reducirla](sql-database-scale-up.md) a medida que cambie una carga de trabajo. Por ejemplo, si la carga de trabajo de la base de datos está ocupado durante la temporada de compras de vuelta al colegio, puede aumentar el nivel de rendimiento de la base de datos durante ese período de tiempo y reducirlo una vez que termine. Puede optimizar su entorno de nube para la estacionalidad de su negocio y así minimizar lo que paga. Este modelo también funciona bien para los ciclos de lanzamiento de productos de software. Un equipo de pruebas puede asignar capacidad mientras realiza series de pruebas y liberar esa capacidad una vez completadas las pruebas. Esta solicitud de capacidad encaja bien con el modelo de pago por la capacidad que se necesita y evita gastar en recursos dedicados que se usan con poca frecuencia.

## Razones para usar los niveles de servicio

Aunque cada carga de trabajo puede variar, el propósito de los niveles de servicio es proporcionar una previsibilidad de rendimiento en diversos niveles de rendimiento. Permite a los clientes con elevados requisitos de recursos para sus bases de datos trabajar en un entorno informático más dedicado.

### Casos de uso del nivel de servicio Básico:

- **Introducción a Base de datos SQL de Azure**: las aplicaciones en desarrollo a menudo no necesitan altos niveles de rendimiento. Las bases de datos básicas proporcionan un entorno ideal para el desarrollo de bases de datos a un bajo precio.
- **Base de datos con un solo usuario**: las aplicaciones que asocian un solo usuario a una base de datos normalmente no tienen grandes requisitos de simultaneidad y rendimiento. Las aplicaciones con estos requisitos son candidatas al nivel de servicio Básico.

### Casos de uso del nivel de servicio Estándar:

- **Base de datos con varias solicitudes simultáneas**: aplicaciones que dan servicio a más de un usuario a la vez. Por ejemplo, los sitios web con tráfico moderado o las aplicaciones de departamento que requieren más recursos son buenos candidatos para el nivel de servicio Estándar.

### Casos de uso del nivel de servicio Premium:

- **Carga máxima elevada**: una aplicación que requiere mucha CPU, memoria o E/S para completar sus operaciones. Por ejemplo, una operación de base de datos que se sabe que consume varios núcleos de CPU durante mucho tiempo es candidata para el uso del nivel de servicio Premium.
- **Muchas solicitudes simultáneas**: algunas aplicaciones de base de datos atienden muchas solicitudes simultáneas, por ejemplo, cuando dan servicio a un sitio web con alto volumen de tráfico. Los niveles de servicio Básico y Estándar tienen límites en cuanto al número de solicitudes simultáneas. Las aplicaciones que requieren más conexiones necesitarían elegir un tamaño de reserva suficiente para controlar el número máximo de solicitudes necesarias.
- **Baja latencia**: algunas aplicaciones necesitan garantizar una respuesta de la base de datos en un tiempo mínimo. Si se llama a un procedimiento almacenado determinado como parte de una operación de cliente más amplia, podría ser necesario volver de esa llamada en no más de 20 milisegundos el 99 % del tiempo. Este tipo de aplicación se beneficia del nivel de servicio Premium para asegurarse de que haya capacidad de proceso disponible.

El nivel exacto que necesita depende de los requisitos de carga máxima para cada dimensión de recursos. Algunas aplicaciones pueden usar pocas cantidades de un recurso pero tener requisitos considerables para otros.

## Información de precios y facturación

Los grupos elásticos se facturan según las características siguientes:

- Los grupos elásticos se facturan desde su creación, incluso si no contiene ninguna base de datos.
- Los grupos elásticos se facturan por horas. Esta frecuencia de medición es igual que la de los niveles de rendimiento de las bases de datos únicas.
- Si se cambia el tamaño de un grupo elástico a una nueva cantidad de eDTU, el grupo no se factura según la nueva cantidad de eDTU hasta que la operación de cambio de tamaño se complete. Esto sigue el mismo patrón de facturación que el cambio de nivel de rendimiento de las bases de datos independientes.


- El precio de un grupo elástico se basa en el número de eDTU del grupo. El precio de un grupo elástico es independiente de la utilización de las bases de datos elásticas de dentro de él.
- El precio se calcula por (número de eDTU de grupo) x (precio unitario por eDTU).

El precio unitario de eDTU de un grupo elástico es mayor que el precio unitario de eDTU para una base de datos independiente del mismo nivel de servicio. Para obtener información detallada, vea [Precios de bases de datos SQL](https://azure.microsoft.com/pricing/details/sql-database/).

## Límites y capacidades de nivel de servicio
Cada nivel de servicio y nivel de rendimiento están asociados a distintos límites y características de rendimiento. La tabla siguiente describe estas características para una sola base de datos.

[AZURE.INCLUDE [Tabla de niveles de servicio de SQL Database](../../includes/sql-database-service-tiers-table.md)]

Las secciones siguientes proporcionan más información sobre cómo ver el uso relacionado con estos límites.

### Almacenamiento máximo de In-Memory OLTP

Puede usar la vista **sys.dm\_db\_resource\_stats** para supervisar el uso del almacenamiento en memoria. Para obtener más información sobre la supervisión, consulte [Supervisión del almacenamiento de In-Memory OLTP](sql-database-in-memory-oltp-monitoring.md).

>[AZURE.NOTE] La vista previa de In-Memory OLTP actualmente admite solamente bases de datos únicas y no bases de datos dentro de grupos de bases de datos elásticas.

### Máximo de solicitudes simultáneas

Para ver el número de solicitudes simultáneas, ejecute la siguiente consulta Transact-SQL en la Base de datos SQL:

	SELECT COUNT(*) AS [Concurrent_Requests]
	FROM sys.dm_exec_requests R

Si está analizando la carga de trabajo de una base de datos de SQL Server local, debería modificar esta consulta para filtrar en la base de datos específica que se va a analizar. Por ejemplo, si tiene una base de datos local denominada MyDatabase, la siguiente consulta Transact-SQL devuelve el número de solicitudes simultáneas en esa base de datos.

	SELECT COUNT(*) AS [Concurrent_Requests]
	FROM sys.dm_exec_requests R
	INNER JOIN sys.databases D ON D.database_id = R.database_id
	AND D.name = 'MyDatabase'

Se trata simplemente de una instantánea en un solo momento dado. Para obtener una mejor comprensión de la carga de trabajo, tendrá que recopilar muchas muestras durante un periodo de tiempo para comprender los requisitos de solicitudes simultáneas.

### Máximo de inicios de sesión simultáneos

No hay ninguna consulta o DMV que pueda mostrar los recuentos o el historial de los inicios de sesión simultáneos. Puede analizar los patrones de usuario y aplicación para hacerse una idea de la frecuencia de los inicios de sesión. También podría ejecutar cargas reales en un entorno de prueba para asegurarse de que no está alcanzando este u otros límites descritos en este tema.

Si varios clientes usan la misma cadena de conexión, el servicio autentica cada inicio de sesión. Si diez usuarios se conectan de forma simultánea a una base de datos con el mismo nombre de usuario y contraseña, habrá diez conexiones simultáneas. Este límite se aplica solo a la duración del inicio de sesión y autenticación. Así que si los mismos diez usuarios se conectan en secuencia a la base de datos, el número de inicios de sesión simultáneos nunca será superior a uno.

>[AZURE.NOTE] Este límite no se aplica actualmente a las bases de datos en grupos de bases de datos elásticas.

### Máximo de sesiones

Para ver el número actual de sesiones activas, ejecute la siguiente consulta Transact-SQL en su base de datos SQL:

	SELECT COUNT(*) AS [Sessions]
	FROM sys.dm_exec_connections

Si va a analizar una carga de trabajo de SQL Server local, modifique la consulta para centrarse en una base de datos específica. Esta consulta lo ayuda a determinar las posibles necesidades de sesión para esa base de datos si la mueve a Azure SQL Database.

	SELECT COUNT(*)  AS [Sessions]
	FROM sys.dm_exec_connections C
	INNER JOIN sys.dm_exec_sessions S ON (S.session_id = C.session_id)
	INNER JOIN sys.databases D ON (D.database_id = S.database_id)
	WHERE D.name = 'MyDatabase'

Aquí también, las consultas devuelven el recuento de un punto en el tiempo, por ello recopilar varias muestras durante un periodo de tiempo le ayuda a comprender mejor el uso de las sesiones.

Para el análisis de Base de datos SQL, también puede consultar **sys.resource\_stats** a fin de obtener estadísticas históricas sobre las sesiones usando la columna **active\_session\_count**. En la siguiente sección de supervisión se proporciona más información sobre cómo usar esta vista.

## Supervisión del uso de recursos
Hay dos vistas que permiten supervisar el uso de recursos para una Base de datos SQL con respecto a su nivel de servicio:

- [sys.dm\_db\_resource\_stats](https://msdn.microsoft.com/library/dn800981.aspx)
- [Sys.resource\_stats](https://msdn.microsoft.com/library/dn269979.aspx)

### Uso de sys.dm\_db\_resource\_stats
La vista [sys.dm\_db\_resource\_stats](https://msdn.microsoft.com/library/dn800981.aspx) existe en cada Base de datos SQL y proporciona datos sobre el uso de recursos recientes en relación con el nivel de servicio. Porcentajes medios para CPU, E/S de datos, escritura de registros y memoria, se registran cada 15 segundos y se mantienen durante una hora.

Dado que esta vista proporciona una panorámica más granular del uso de recursos, debe usar primero **sys.dm\_db\_resource\_stats** para cualquier análisis o la solución de problemas de estado actual. Por ejemplo, la consulta siguiente muestra el uso de recursos promedio y máximo para la base de datos actual durante la última hora:

	SELECT  
	    AVG(avg_cpu_percent) AS 'Average CPU Utilization In Percent',
	    MAX(avg_cpu_percent) AS 'Maximum CPU Utilization In Percent',
	    AVG(avg_data_io_percent) AS 'Average Data IO In Percent',
	    MAX(avg_data_io_percent) AS 'Maximum Data IO In Percent',
	    AVG(avg_log_write_percent) AS 'Average Log Write Utilization In Percent',
	    MAX(avg_log_write_percent) AS 'Maximum Log Write Utilization In Percent',
	    AVG(avg_memory_usage_percent) AS 'Average Memory Usage In Percent',
	    MAX(avg_memory_usage_percent) AS 'Maximum Memory Usage In Percent'
	FROM sys.dm_db_resource_stats;  

Para otras consultas, consulte los ejemplos de [sys.dm\_db\_resource\_stats](https://msdn.microsoft.com/library/dn800981.aspx).

### Uso de sys.resource\_stats

La vista [sys.resource\_stats](https://msdn.microsoft.com/library/dn269979.aspx) en la base de datos **maestra** proporciona información adicional para supervisar el uso del rendimiento de Base de datos SQL dentro de su nivel de rendimiento y nivel de servicio específicos. Los datos se recopilan cada cinco minutos y se mantienen durante aproximadamente 35 días. Esta vista es más útil para realizar análisis históricos a largo plazo sobre el uso de recursos de Base de datos SQL.

El siguiente gráfico muestra el uso de recursos de CPU para una base de datos Premium con el nivel de rendimiento P2 durante cada hora de una semana. Este gráfico en concreto empieza el lunes, muestra cinco días laborables y, a continuación, un fin de semana donde hay mucha menos actividad en la aplicación.

![Uso de recursos de Base de datos SQL](./media/sql-database-performance-guidance/sql_db_resource_utilization.png)

Según los datos, esta base de datos tiene actualmente una carga máxima de CPU superior al 50 % de uso de la CPU respecto al nivel de rendimiento P2 (mediodía del martes). Si la CPU es el factor dominante en el perfil de recursos de la aplicación, puede decidir que P2 es el nivel de rendimiento adecuado para garantizar que la carga de trabajo siempre encaje. Si se espera que una aplicación crezca con el tiempo, tiene sentido permitir cierta reserva de recursos adicional para que la aplicación no supere nunca el límite superior. Aumentar el nivel de rendimiento ayuda a evitar errores visibles para el cliente debidos a que una base de datos no tiene suficiente capacidad para procesar las solicitudes de manera eficiente, especialmente en entornos sensibles a la latencia (como la base de datos de una aplicación que dibuja páginas web en función de los resultados de las llamadas a la base de datos).

Merece la pena mencionar que otros tipos de aplicaciones pueden interpretar el mismo gráfico de manera diferente. Por ejemplo, si una aplicación intentara procesar datos de nóminas todos los días y tuviera el mismo gráfico, este tipo de modelo de "trabajo por lotes" podría funcionar bien en un nivel de rendimiento P1. El nivel de rendimiento P1 tiene 100 DTU en comparación con las 200 DTU del nivel de rendimiento P2. Esto significa que el nivel de rendimiento P1 proporciona la mitad de rendimiento que el nivel de rendimiento P2. Hasta un 50 % de uso de CPU en P2 es igual al 100 % de uso de CPU en P1. Siempre que la aplicación no agote los tiempos de espera, quizás no importe si un trabajo tarda dos horas o dos horas y media en completarse, siempre que se lleve a cabo hoy. Una aplicación en esta categoría puede usar probablemente un nivel de rendimiento P1. Puede aprovechar el hecho de que hay períodos de tiempo durante el día en que el uso de recursos es menor, lo que significa que las "cargas elevadas" podrían retrasarse a uno de esos momentos más tarde ese día. El nivel de rendimiento P1 puede ser adecuado para este tipo de aplicación (y ahorrar dinero) siempre que los trabajos puedan completarse a tiempo cada día.

Base de datos SQL de Azure muestra información sobre los recursos consumidos para cada base de datos activa en la vista **sys.resource\_stats** de la base de datos **maestra** de cada servidor. Los datos de la tabla se agregan en intervalos de 5 minutos. Con los niveles de servicio Básico, Estándar y Premium, los datos pueden tardar más de cinco minutos en aparecer en la tabla, lo que significa que son mejores para el análisis histórico que para el análisis casi en tiempo real. Al consultar la vista **sys.resource\_stats**, se muestra el historial reciente de una base de datos para validar si la reserva seleccionada proporcionó el rendimiento deseado cuando era necesario.

>[AZURE.NOTE] Tiene que estar conectado a la base de datos **maestra** de su servidor lógico de SQL Database para poder consultar **sys.resource\_stats** en los ejemplos siguientes.

En el ejemplo siguiente se muestra cómo se exponen los datos en esta vista:

	SELECT TOP 10 *
	FROM sys.resource_stats
	WHERE database_name = 'resource1'
	ORDER BY start_time DESC

![estadísticas de recursos del sistema](./media/sql-database-performance-guidance/sys_resource_stats.png)

En el ejemplo siguiente, se muestran las diferentes formas en que se puede entender el uso de recursos de la base de datos SQL mediante la vista de catálogo **sys.resource\_stats**.

1. Por ejemplo, para buscar el uso de recursos durante la semana anterior de la base de datos "userdb1", puede ejecutar la consulta siguiente.

		SELECT *
		FROM sys.resource_stats
		WHERE database_name = 'userdb1' AND
		      start_time > DATEADD(day, -7, GETDATE())
		ORDER BY start_time DESC;

2. Para evaluar si la carga de trabajo encaja bien en el nivel de rendimiento, tiene que explorar en profundidad cada aspecto de las métricas de recursos: CPU, lecturas, escrituras, número de trabajos y número de sesiones. Esta es una consulta revisada que usa sys.resource\_stats para notificar los valores promedio y máximo de estas métricas de recursos.

		SELECT
		    avg(avg_cpu_percent) AS 'Average CPU Utilization In Percent',
		    max(avg_cpu_percent) AS 'Maximum CPU Utilization In Percent',
		    avg(avg_data_io_percent) AS 'Average Physical Data IO Utilization In Percent',
		    max(avg_data_io_percent) AS 'Maximum Physical Data IO Utilization In Percent',
		    avg(avg_log_write_percent) AS 'Average Log Write Utilization In Percent',
		    max(avg_log_write_percent) AS 'Maximum Log Write Utilization In Percent',
		    avg(max_session_percent) AS 'Average % of Sessions',
		    max(max_session_percent) AS 'Maximum % of Sessions',
		    avg(max_worker_percent) AS 'Average % of Workers',
		    max(max_worker_percent) AS 'Maximum % of Workers'
		FROM sys.resource_stats
		WHERE database_name = 'userdb1' AND start_time > DATEADD(day, -7, GETDATE());

3. Con la información anterior sobre los valores promedio y máximo de cada métrica de recursos, puede evaluar si la carga de trabajo encaja bien en el nivel de rendimiento que eligió. Por lo general, los valores promedio de sys.resource\_stats son una buena referencia para el tamaño de destino. Debería ser su vara de medida principal. Por ejemplo, si usa el nivel de servicio Estándar con el nivel de rendimiento S2, los porcentajes de uso promedio de CPU, lecturas de E/S y escrituras están por debajo del 40 %, el promedio de trabajos es inferior a 50 y el número promedio de sesiones es inferior a 200, la carga de trabajo podría encajar bien en el nivel de rendimiento S1. Es fácil ver si la base de datos se ajusta a los límites de trabajos y sesiones. Para ver si una base de datos encaja en un nivel de rendimiento inferior con respecto a la CPU, lecturas y escrituras, divida el número de DTU del nivel de rendimiento inferior por el número de DTU de su nivel de rendimiento actual y multiplique el resultado por 100:

	**S1 DTU/S2 DTU * 100 = 20/50 * 100 = 40**

	El resultado es la diferencia porcentual de rendimiento relativa entre los dos niveles de rendimiento. Si su uso no supera este porcentaje, la carga de trabajo podría encajar en el nivel de rendimiento inferior. Sin embargo, debe examinar todos los intervalos de valores de uso de recursos y determinar, según el porcentaje, con qué frecuencia encajaría la carga de trabajo de la base de datos en el nivel de rendimiento inferior. La siguiente consulta proporciona el porcentaje de ajuste por dimensión de recursos según el umbral del 40 % calculado antes.

		SELECT
		    (COUNT(database_name) - SUM(CASE WHEN avg_cpu_percent >= 40 THEN 1 ELSE 0 END) * 1.0) / COUNT(database_name) AS 'CPU Fit Percent'
		    ,(COUNT(database_name) - SUM(CASE WHEN avg_log_write_percent >= 40 THEN 1 ELSE 0 END) * 1.0) / COUNT(database_name) AS 'Log Write Fit Percent'
		    ,(COUNT(database_name) - SUM(CASE WHEN avg_data_io_percent >= 40 THEN 1 ELSE 0 END) * 1.0) / COUNT(database_name) AS 'Physical Data IO Fit Percent'
		FROM sys.resource_stats
		WHERE database_name = 'userdb1' AND start_time > DATEADD(day, -7, GETDATE());

	En función de su objetivo de nivel de servicio (SLO) de base de datos, puede decidir si la carga de trabajo encaja en el nivel de rendimiento inferior. Si el SLO de la carga de trabajo de base de datos es del 99,9 % y la consulta anterior devuelve valores mayores que 99,9 para las tres dimensiones de recursos, es probable que la carga de trabajo quepa en el nivel de rendimiento inferior.

	El porcentaje de ajuste también ofrece información detallada sobre si debe pasar al siguiente nivel de rendimiento superior para cumplir su SLO. Por ejemplo, "userdb1" muestra el siguiente uso durante la semana pasada.

	| Porcentaje promedio de CPU | Porcentaje máximo de CPU |
	|---|---|
	| 24,5 | 100,00 |

	El promedio de CPU es aproximadamente un cuarto del límite del nivel de rendimiento, que encajaría bien en el nivel de rendimiento de la base de datos. Sin embargo, el valor máximo muestra que la base de datos alcanza el límite del nivel de rendimiento. ¿Necesita pasar al siguiente nivel de rendimiento superior? De nuevo, hay que mirar cuántas veces la carga de trabajo alcanza el 100 % y compararlo con el SLO de la carga de trabajo de base de datos.

		SELECT
		(COUNT(database_name) - SUM(CASE WHEN avg_cpu_percent >= 100 THEN 1 ELSE 0 END) * 1.0) / COUNT(database_name) AS 'CPU Fit Percent'
		,(COUNT(database_name) - SUM(CASE WHEN avg_log_write_percent >= 100 THEN 1 ELSE 0 END) * 1.0) / COUNT(database_name) AS 'Log Write Fit Percent’
		,(COUNT(database_name) - SUM(CASE WHEN avg_data_io_percent >= 100 THEN 1 ELSE 0 END) * 1.0) / COUNT(database_name) AS 'Physical Data IO Fit Percent'
		FROM sys.resource_stats
		WHERE database_name = 'userdb1' AND start_time > DATEADD(day, -7, GETDATE());

	Si esta consulta devuelve un valor menor que 99,9 para cualquiera de las tres dimensiones de recursos, plantéese subir al siguiente nivel de rendimiento o usar técnicas de optimización de aplicaciones para reducir la carga en Azure SQL Database.

4. El ejercicio anterior también tiene en cuenta el aumento de la carga de trabajo previsto en el futuro.

## Optimización de la aplicación

En servidores locales tradicionales de SQL Server, el proceso de planeación inicial de la capacidad suele está separado del proceso de ejecución de una aplicación en producción. En otras palabras, la adquisición de hardware y las licencias asociadas para ejecutar SQL Server se realizan por adelantado, mientras que la optimización del rendimiento se hace después. Cuando se usa Azure SQL Database, se recomienda (y, puesto que se factura cada mes, es deseable) intercalar el proceso de ejecución y optimización de una aplicación. El modelo de pago por capacidad a petición permite optimizar la aplicación para que use los recursos mínimos necesarios ahora en lugar de aprovisionar en exceso el hardware en función de las estimaciones de los planes de crecimiento futuro para una aplicación (que a menudo son incorrectos porque tienen que predecir muy lejos en el futuro). Algunos clientes pueden decidir no optimizar una aplicación y, en su lugar, eligen aprovisionar en exceso los recursos de hardware. Este enfoque podría tener sentido si no desea cambiar una aplicación clave durante un período de ocupación de la aplicación. La optimización de una aplicación puede minimizar los requisitos de recursos y reducir las facturas mensuales cuando se usan los niveles de servicio de Base de datos SQL de Azure.

### Características de la aplicación

Aunque los niveles de servicio están diseñados para mejorar la estabilidad de rendimiento y previsibilidad de una aplicación, hay algunos procedimientos recomendados para optimizarla y poder aprovechar mejor las ventajas de los recursos dentro de un nivel de rendimiento. Si bien muchas aplicaciones ven importantes mejoras de rendimiento simplemente con subir de nivel de rendimiento o de servicio, no todas las aplicaciones se benefician sin una optimización adicional. Las aplicaciones que tienen las siguientes características deben plantearse también optimizaciones adicionales para mejorar aún más el rendimiento cuando se usa Base de datos SQL de Azure.

- **Aplicaciones que tienen un rendimiento lento debido a un comportamiento "conversador"**: estas aplicaciones llevan a cabo un número excesivo de operaciones de acceso a datos que son sensibles a la latencia de red. Estas aplicaciones pueden requerir modificaciones para reducir el número de operaciones de acceso de datos en Base de datos SQL de Azure. Por ejemplo, el rendimiento de la aplicación se puede mejorar mediante técnicas como el procesamiento por lotes de consultas ad hoc o el traslado de las consultas a procedimientos almacenados. Para obtener más información, consulte la sección 'Procesamiento de consultas por lotes', a continuación.
- **Bases de datos con una carga de trabajo intensivo que un solo equipo no puede admitir**: bases de datos que superan los recursos del nivel de rendimiento Premium más alto no son buenas candidatas. Estas bases de datos pueden beneficiarse del escalado horizontal de la carga de trabajo. Para obtener más información, consulte las secciones 'Particionamiento entre bases de datos' y 'Particionamiento funcional', a continuación.
- **Aplicaciones que contienen consultas no óptimas**: las aplicaciones, especialmente en la capa de acceso a datos, que tienen consultas poco optimizadas pueden no beneficiarse de la elección de un nivel de rendimiento mayor según lo esperado. Esto incluye consultas que carecen de una cláusula WHERE, con índices que faltan o tienen estadísticas anticuadas. Estas aplicaciones se benefician de las técnicas de optimización del rendimiento de consultas estándar. Para obtener más información, consulte las secciones 'Índices que faltan' y 'Optimización/sugerencias de consulta', a continuación.
- **Aplicaciones con un diseño de acceso a datos no óptimo**: las aplicaciones que tienen problemas inherentes de simultaneidad de acceso a los datos, como los interbloqueos, pueden no beneficiarse de la elección de un nivel de rendimiento mayor. Los desarrolladores de aplicaciones deben plantearse reducir los viajes de ida y vuelta a Base de datos SQL de Azure de los datos en caché en el lado del cliente usando el servicio de almacenamiento en caché de Azure u otras tecnologías de almacenamiento en caché. Consulte la sección Almacenamiento en caché de la capa de aplicación, a continuación.

## Técnicas de optimización
En esta sección se explican algunas técnicas que puede usar para optimizar Base de datos SQL de Azure para obtener el mejor rendimiento de la aplicación y que pueda ejecutarla en el menor nivel de rendimiento posible. Algunas de estas técnicas coinciden con los procedimientos recomendados de optimización tradicionales de SQL Server, pero otras son específicas de Azure SQL Database. En algunos casos, las técnicas tradicionales de SQL Server se pueden ampliar para que funcionen también en Base de datos SQL de Azure, examinando para ello los recursos consumidos para una base de datos con el fin de encontrar áreas de mayor optimización.

### Información de rendimiento de consultas y Asesor de Base de datos SQL
SQL Database ofrece dos herramientas en Azure Portal para analizar y corregir problemas de rendimiento de la base de datos:

- [Query Performance Insight](sql-database-query-performance.md)
- [Asesor de Base de datos SQL](sql-database-advisor.md)

Consulte los vínculos anteriores para obtener más información sobre cada una de las herramientas y sobre cómo usarlas. Las dos secciones siguientes sobre índices que faltan y la optimización de consultas, proporcionan otras formas de buscar manualmente y corregir los problemas de rendimiento similares. Se recomienda probar las herramientas en el portal para diagnosticar y corregir problemas de manera más eficiente. Use la optimización manual para casos especiales.

### Índices que faltan
Un problema común del rendimiento de las bases de datos OLTP está relacionado con el diseño físico de la base de datos. A menudo, los esquemas de base de datos se diseñan y se entregan sin realizar pruebas a escala (ya sea en la carga o en el volumen de datos). Lamentablemente, el rendimiento de un plan de consultas puede ser aceptable a pequeña escala, pero se puede degradar sustancialmente cuando se enfrenta a los volúmenes de datos del nivel de producción. El origen más común de este problema es la falta de índices adecuados para satisfacer los filtros u otras restricciones en una consulta. A menudo, la falta de índices se manifiesta como un recorrido de tabla cuando podría ser suficiente una búsqueda de índice.

En el ejemplo siguiente se crea un caso donde el plan de consulta seleccionado contiene un recorrido cuando bastaría con una búsqueda.

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

![plan de consulta con índices que faltan](./media/sql-database-performance-guidance/query_plan_missing_indexes.png)

Base de datos SQL de Azure contiene funcionalidad que ayuda a los administradores de bases de datos de sugerencias a encontrar y corregir casos frecuentes de índices que faltan. Las vistas de administración dinámica (DMV) integradas en Base de datos SQL de Azure miran la compilación de consultas en la que un índice reduciría considerablemente el costo estimado para ejecutar una consulta. Durante la ejecución de las consultas, SQL Database hace un seguimiento de la frecuencia con que se ejecuta cada plan de consulta, así como de la diferencia estimada entre el plan de consulta en ejecución y el imaginario en el que existía ese índice. Estas DMV permiten a un administrador de bases de datos adivinar rápidamente qué cambios de diseño de la base de datos física pueden mejorar el costo total de la carga de trabajo para una base de datos específica y su carga de trabajo real.

La consulta siguiente puede usarse para evaluar posibles índices que faltan.

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

En este ejemplo, se recomienda el siguiente índice.

	CREATE INDEX missing_index_5006_5005 ON [dbo].[missingindex] ([col2])  

Una vez creado, esa misma instrucción SELECT elige un plan diferente que usa una búsqueda en lugar de un recorrido y se ejecuta más eficazmente, como se muestra en el siguiente plan de consulta.

![plan de consulta con índices corregidos](./media/sql-database-performance-guidance/query_plan_corrected_indexes.png)

El principal dato es que la capacidad de E/S de un sistema compartido es más limitada que la de un equipo servidor dedicado. Es de especial valor minimizar la E/S innecesaria para sacar el máximo partido del sistema dentro de la DTU de cada nivel de rendimiento de los niveles de servicio en Azure SQL Database. La elección adecuada de las opciones de diseño de bases de datos físicas puede mejorar considerablemente la latencia de las consultas individuales, la capacidad de procesamiento de solicitudes simultáneas que puede realizar por unidad de escala y minimizar los costos necesarios para satisfacer la consulta. Para obtener más información acerca de las DMV de índices que faltan, consulte [sys.dm\_db\_missing\_index\_details](https://msdn.microsoft.com/library/ms345434.aspx).

### Optimización/sugerencias de consulta
El optimizador de consultas de Azure SQL Database es similar al de SQL Server tradicional. La mayoría de los procedimientos recomendados para optimizar consultas y entender el razonamiento tras las limitaciones del modelo para el optimizador de consultas se aplica también a Azure SQL Database. La optimización de consultas en Azure SQL Database puede reportar la ventaja adicional de reducir las demandas de recursos agregados y permitir que una aplicación se ejecute con un costo menor que una equivalente sin optimizar, porque se puede ejecutar en un nivel de rendimiento inferior.

Un ejemplo común en SQL Server que también se aplica a Base de datos SQL de Azure tiene que ver con cómo se examinan los parámetros durante la compilación para intentar crear un plan más óptimo. El examen de parámetros es que un proceso por el que el optimizador de consultas considera el valor actual del parámetro al compilar una consulta con la esperanza de generar un plan de consulta más óptimo. Aunque esta estrategia puede llevar a un plan de consulta que considerablemente más rápido que un plan compilado sin el conocimiento de los valores de parámetro, el comportamiento actual de SQL Server/Base de datos SQL de Azure es imperfecto: hay casos en los que el parámetro no se examina y hay casos en los que el parámetro se examina pero el plan generado no es el óptimo para el conjunto completo de valores de parámetro de una carga de trabajo. Microsoft incluye sugerencias de consulta (directivas) para que pueda especificar más deliberadamente la intención y reemplazar el comportamiento predeterminado de examen de parámetros. A menudo, el uso de sugerencias puede corregir los casos en los que el comportamiento predeterminado de Base de datos SQL de Azure/SQL Server es imperfecto para una carga de trabajo de un cliente determinado.

En el ejemplo siguiente se muestra cómo el procesador de consultas puede generar un plan que no es óptimo para los requisitos de rendimiento y de recursos, y cómo el uso de una sugerencia de consulta puede reducir el tiempo de ejecución de consultas y los requisitos de recursos en Azure SQL Database.

La siguiente es una configuración de ejemplo:

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

El código de configuración crea una tabla que contiene una distribución de datos sesgados. El plan de consulta óptimo varía en función de qué parámetro se seleccione. Lamentablemente, el comportamiento del almacenamiento en caché del plan no siempre recompila la consulta según el valor de parámetro más común, lo que significa que es posible que un plan poco óptimo se almacene en caché y se use para muchos valores, aunque un plan diferente fuera un plan promedio mejor. A continuación, crea dos procedimientos almacenados que son idénticos, salvo en que uno contiene una sugerencia de consulta especial.

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

**Ejemplo (parte 2; espere 10 minutos antes de intentar esta parte para que sea claramente diferente en los datos de telemetría resultantes):**

	EXEC psp2 @param2=1;
	TRUNCATE TABLE t1;

	DECLARE @i int = 0;
	WHILE @i < 1000
	BEGIN
	    EXEC psp2 @param2=2;
	    TRUNCATE TABLE t1;
	    SET @i += 1;
	END

Cada parte de este ejemplo intenta ejecutar 1000 veces una instrucción insert parametrizada (para generar una carga suficiente para que destaque en un conjunto de datos de prueba). Al ejecutar los procedimientos almacenados, el procesador de consultas examina el valor de los parámetros pasados al procedimiento durante su primera compilación (lo que se conoce como examinar parámetros). El plan resultante se almacena en caché y se usa para invocaciones posteriores, aunque el valor del parámetro sea diferente. Como resultado, el plan óptimo no puede usarse en todos los casos. A veces, los clientes necesitan guiar al optimizador para elegir un plan que sea mejor para el caso promedio en lugar del caso que se dio cuando la consulta se compiló por primera vez. En este ejemplo, el plan inicial genera un plan de "recorrido" que lee todas las filas para encontrar todos los valores que coinciden con el parámetro.

![Optimización de consultas](./media/sql-database-performance-guidance/query_tuning_1.png)

Como el procedimiento se ejecutó con el valor 1, el plan resultante era óptimo para 1 pero poco óptimo para todos los demás valores de la tabla. Es probable que el comportamiento resultante no sea el deseado si elige cada plan aleatoriamente, porque la ejecución del plan es más lenta y consume más recursos.

Al ejecutar la prueba con "SET STATISTICS IO ON", se muestra el trabajo de recorrido lógico realizado en este ejemplo en segundo plano: puede ver que el plan realizó 1148 lecturas (lo que no resulta eficaz si el caso promedio es devolver una sola fila).

![Optimización de consultas](./media/sql-database-performance-guidance/query_tuning_2.png)

La segunda parte del ejemplo usa una sugerencia de consulta para indicar al optimizador que use un valor específico durante el proceso de compilación. En este caso, obliga al procesador de consultas a omitir el valor que se pasa como parámetro y, en su lugar, asume un valor "UNKNOWN", lo que significa un valor que tiene la frecuencia promedio en la tabla (se omite el sesgo). El plan resultante está basado en búsquedas, es más rápido y usa menos recursos, de promedio, que el plan de la parte 1 de este ejemplo.

![Optimización de consultas](./media/sql-database-performance-guidance/query_tuning_3.png)

El impacto puede verse si se examina la tabla **sys.resource\_stats** (hay un retraso desde el momento en que se ejecuta la prueba hasta el momento en que se rellenan los datos de la tabla). En este ejemplo, la parte 1 se ejecutó en el período de tiempo de 22:25:00 y la parte 2 se ejecutó en 22:35:00. Observe que el período de tiempo anterior usó más recursos en ese período de tiempo que el posterior (debido a las mejoras en la eficiencia del plan).

	SELECT TOP 1000 *
	FROM sys.resource_stats
	WHERE database_name = 'resource1'
	ORDER BY start_time DESC

![Optimización de consultas](./media/sql-database-performance-guidance/query_tuning_4.png)

>[AZURE.NOTE] Aunque el ejemplo usado aquí es intencionadamente pequeño, el impacto de los parámetros poco óptimos puede ser considerable, especialmente en bases de datos más grandes. La diferencia, en casos extremos, puede estar entre segundos y horas para los casos rápidos y lentos.

Puede examinar **sys.resource\_stats** para determinar si el recurso de una prueba determinada usa más o menos recursos que otra prueba. Al comparar los datos, separe las pruebas el tiempo suficiente para que no estén agrupadas en el mismo período de tiempo de 5 minutos en la vista **sys.resource\_stats**. El objetivo del ejercicio es minimizar los recursos totales usados, no minimizar los recursos máximos. Por lo general, al optimizar la latencia de un fragmento de código, también se reduce el consumo de recursos. Asegúrese de que los cambios previstos en cualquier aplicación se necesitan y no afectan negativamente a la experiencia de ningún usuario de una aplicación cuando usen sugerencias de consulta.

Si una carga de trabajo contiene un conjunto de consultas repetidas, a menudo tiene sentido capturar y validar la idoneidad de esas elecciones del plan, ya que controlarán la unidad de tamaño mínima de los recursos necesaria para hospedar la base de datos. Una vez validado, una revisión ocasional de esos planes permite asegurar que no se han degradado. Para obtener más información acerca de las sugerencias de consulta, consulte [Sugerencias de consulta (Transact-SQL)](https://msdn.microsoft.com/library/ms181714.aspx).

### Particionamiento entre bases de datos
Como Azure SQL Database se ejecuta en hardware estándar, los límites de capacidad para una base de datos única son inferiores a los de una instalación local de SQL Server tradicional. Por lo tanto, algunos clientes usan técnicas de particionamiento para repartir las operaciones de base de datos entre varias bases de datos cuando no entran en los límites de una base de datos única en Azure SQL Database. La mayoría de los clientes que usan técnicas de particionamiento en Base de datos SQL de Azure divide sus datos en una única dimensión en varias bases de datos. Este enfoque implica entender que, con frecuencia, las aplicaciones OLTP realizan transacciones que solo se aplican a una fila o a un pequeño grupo de filas del esquema.

>[AZURE.NOTE] Tenga en cuenta que Base de datos SQL ahora proporciona una biblioteca para ayudar con el particionamiento. Para obtener más información, consulte [Información general de la biblioteca de cliente de bases de datos elásticas](sql-database-elastic-database-client-library.md).

Por ejemplo, si una base de datos contiene el cliente, el pedido y los detalles del pedido (tal y como se muestra en la base de datos de ejemplo tradicional Northwind incluida en SQL Server), estos datos pueden dividirse en varias bases de datos agrupando a un cliente con la información relacionada sobre el pedido y los detalles del pedido, y garantizar que permanecen en una única base de datos. La aplicación dividiría los distintos clientes entre las bases de datos, repartiendo la carga eficazmente entre varias bases de datos. El particionamiento no solo permite que los clientes eviten el límite de tamaño máximo de la base de datos, sino también que Azure SQL Database procese cargas de trabajo que sean mucho mayores que los límites de los distintos niveles de rendimiento, siempre y cuando cada base de datos se ajuste a sus DTU.

Aunque el particionamiento de la base de datos no reduce la capacidad total de los recursos para una solución, esta técnica es muy eficaz para admitir soluciones muy grandes repartidas entre varias bases de datos y permite que cada base de datos se ejecute en un nivel de rendimiento diferente para admitir bases de datos "eficaces" muy grandes con requisitos elevados de recursos.

### Creación de particiones funcional
Los usuarios de SQL Server suelen combinar varias funciones en una base de datos única. Por ejemplo, si una aplicación contiene lógica para administrar el inventario de un almacén, esa base de datos podría contener lógica asociada con el inventario, el seguimiento de los pedidos de compra, los procedimientos almacenados y las vistas indizadas o materializadas que administran los informes de fin de mes y otras funciones. Esta técnica tiene la ventaja de poder administrar fácilmente la base de datos para operaciones tales como copia de seguridad, pero también requiere ajustar el tamaño del hardware para administrar picos de carga en todas las funciones de una aplicación.

Dentro de una arquitectura de escalado horizontal usada dentro de Azure SQL Database, es beneficioso repartir las distintas funciones de una aplicación entre bases de datos diferentes. Esta técnica permite que cada una se escale de forma independiente. A medida que una aplicación realiza una actividad mayor (y obtiene más carga en la base de datos), el administrador puede elegir niveles de rendimiento independientemente para cada función dentro de una aplicación. En el límite, esta arquitectura reparte la carga entre varios equipos lo que permite a una aplicación hacerse mayor de lo que un solo equipo estándar puede administrar.

### Consultas por lotes
Para las aplicaciones que tienen acceso a datos en forma de consultas ad hoc frecuentes, una parte sustancial del tiempo de respuesta se dedica a la comunicación de red entre la capa de aplicación y la de Azure SQL Database. Incluso si la aplicación y Azure SQL Database residen en el mismo centro de datos, la latencia de red entre ambas podría verse aumentada por un número elevado de operaciones de acceso a datos. Para reducir los viajes de ida y vuelta en la red para estas operaciones de acceso a datos, el desarrollador de aplicaciones debería plantearse el procesamiento por lotes de las consultas ad hoc o compilarlas en procedimientos almacenados. El procesamiento por lotes de las consultas ad hoc permite enviar varias consultas en un lote grande en un solo recorrido a Azure SQL Database. Compilar consultas ad hoc en un procedimiento almacenado podría lograr el mismo resultado que el procesamiento por lotes. El uso de un procedimiento almacenado también ofrece la ventaja de aumentar las posibilidades de almacenar en caché los planes de consulta en Base de datos SQL de Azure para las ejecuciones posteriores del mismo procedimiento almacenado.

Algunas aplicaciones requieren operaciones de escritura intensivas. En ocasiones se puede reducir la carga total de E/S en una base de datos si se procesan por lotes las operaciones de escritura. Esto suele ser tan sencillo como usar transacciones explícitas en lugar de transacciones de confirmación automática dentro de los procedimientos almacenados y lotes ad hoc. Puede encontrar una evaluación de las distintas técnicas que se pueden usar en [Técnicas de procesamiento por lotes para aplicaciones de Base de datos SQL en Azure](https://msdn.microsoft.com/library/windowsazure/dn132615.aspx). Experimente con su propia carga de trabajo para encontrar el modelo adecuado para el procesamiento por lotes, teniendo cuidado de entender que un modelo determinado puede ofrecer garantías de coherencia transaccional ligeramente diferentes. Para encontrar la carga de trabajo adecuada que minimiza el uso de recursos, es necesario encontrar la combinación correcta entre coherencia y rendimiento.

### Almacenamiento en caché de la capa de aplicación
Algunas aplicaciones de base de datos contienen cargas de trabajo con operaciones de lectura intensivas. Es posible usar capas de almacenamiento en caché para reducir la carga en la base de datos y para reducir el nivel de rendimiento necesario para admitir una base de datos con Base de datos SQL de Azure. [Caché en Redis de Azure](https://azure.microsoft.com/services/cache/) permite a un cliente con una carga de trabajo con muchas operaciones de lectura leer los datos una vez (o una vez por cada equipo de capa de aplicación, según cómo esté configurada) y almacenar esos datos fuera de Base de datos SQL de Azure. Esto permite reducir la carga de la base de datos (CPU y E/S de lectura), pero afecta a la coherencia transaccional porque los datos que se leen de la memoria caché podrían estar sin actualizar respecto a los datos de la base de datos. Aunque hay muchas aplicaciones donde es aceptable una cierta cantidad de incoherencias, esto no es así para todas las cargas de trabajo. Debería comprender totalmente los requisitos de una aplicación antes de emplear una estrategia de almacenamiento en caché de la capa de aplicación.

## Pasos siguientes

- Para más información sobre los niveles de servicio, consulte [Niveles de servicio](sql-database-service-tiers.md).
- Para más información sobre los grupos elásticos, consulte [Grupos elásticos](sql-database-elastic-pool.md).
- Para información acerca del rendimiento y los grupos elásticos, consulte [Consideraciones de rendimiento para los grupos elásticos](sql-database-elastic-pool-guidance.md).

<!---HONumber=AcomDC_0914_2016-->