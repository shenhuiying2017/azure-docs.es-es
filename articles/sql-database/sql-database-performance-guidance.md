---
title: "Guía para el ajuste del rendimiento de Azure SQL Database | Microsoft Docs"
description: "Obtenga información acerca del uso de las recomendaciones para mejorar el rendimiento de la consulta de Azure SQL Database."
services: sql-database
documentationcenter: na
author: CarlRabeler
manager: jhubbard
editor: 
ms.assetid: dd8d95fa-24b2-4233-b3f1-8e8952a7a22b
ms.service: sql-database
ms.custom: monitor & tune
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: On Demand
ms.date: 02/09/2017
ms.author: carlrab
ms.openlocfilehash: 5dc245a29a9106156c207ed7394f8bb289db729e
ms.sourcegitcommit: e5355615d11d69fc8d3101ca97067b3ebb3a45ef
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/31/2017
---
# <a name="tuning-performance-in-azure-sql-database"></a>Ajuste del rendimiento en Azure SQL Database

Azure SQL Database proporciona las [recomendaciones](sql-database-advisor.md) que puede usar para mejorar el rendimiento de la base de datos o puede permitir que Azure SQL Database [se adapte automáticamente a la aplicación](sql-database-automatic-tuning.md) y aplicar los cambios que mejorarán el rendimiento de la carga de trabajo.

Si no dispone de recomendaciones aplicables y sigue experimentando problemas de rendimiento, debe usar los siguientes métodos para mejorar los rendimientos:
1. Aumente los [niveles de servicio](sql-database-service-tiers.md) y proporcione más recursos a la base de datos.
2. Ajuste la aplicación y aplique algunas prácticas recomendadas que puedan mejorar el rendimiento. 
3. Ajuste la base de datos cambiando los índices y consultas para que funcionen de manera más eficiente con los datos.

Estos son métodos manuales porque necesita decidir qué [niveles de servicio](sql-database-service-tiers.md) elegiría o necesitaría para reescribir la aplicación o código de base de datos e implementar los cambios.

## <a name="increasing-performance-tier-of-your-database"></a>Aumento del nivel de rendimiento de su base de datos

Azure SQL Database ofrece cuatro [niveles de servicio](sql-database-service-tiers.md) que puede elegir de entre: Básico, Estándar, Premium y Premium RS (el rendimiento se mide en unidades de procesamiento de base de datos o [DTU](sql-database-what-is-a-dtu.md)). Cada nivel de servicio aísla estrictamente los recursos que puede usar la base de datos SQL y garantiza un rendimiento predecible para ese nivel de servicio. En este artículo se ofrecen instrucciones que pueden ayudarle a elegir el nivel de servicio para su aplicación. También analizamos formas de optimizar la aplicación para obtener el máximo provecho de Azure SQL Database.

> [!NOTE]
> Este artículo se centra en la guía de rendimiento para bases de datos únicas de Azure SQL Database. Para obtener instrucciones sobre rendimiento relativas a los grupos elásticos, consulte las [consideraciones de precio y rendimiento para grupos elásticos](sql-database-elastic-pool-guidance.md). Observe, sin embargo, que muchas de las recomendaciones de optimización de este artículo se pueden aplicar a bases de datos de un grupo elástico y obtener ventajas de rendimiento similares.
> 

* **Básico**: el nivel de servicio Básico ofrece una buena predicción del rendimiento para cada base de datos, hora a hora. En una base de datos básica, hay suficientes recursos para proporcionar un buen rendimiento en bases de datos pequeñas que no tienen varias solicitudes simultáneas. Los casos de uso típicos de cuándo se usaría un nivel de servicio Básico son los siguientes:
  * **Acaba de empezar con Azure SQL Database**. Las aplicaciones que están en desarrollo no necesitan habitualmente altos niveles de rendimiento. Las bases de datos básicas son un entorno adecuado para el desarrollo de bases de datos o pruebas a un precio bajo.
  * **Tiene una base de datos con un único usuario**. Las aplicaciones que asocian un único usuario a una base de datos normalmente no tienen grandes requisitos de simultaneidad y rendimiento. Estas aplicaciones son candidatas al nivel de servicio básico.
* **Estándar**: el nivel de servicio Estándar ofrece una funcionalidad mejorada para predecir el rendimiento y ofrece un buen rendimiento para bases de datos con solicitudes simultáneas, tales como aplicaciones web y de grupo de trabajo. Cuando se usa una base de datos con un nivel de servicio Estándar, puede ajustar el tamaño de la aplicación de base de datos según un rendimiento predecible, minuto a minuto.
  * **La base de datos tiene varias solicitudes simultáneas**. Las aplicaciones que atienden a más de un usuario a la vez suelen necesitar niveles de rendimiento más altos. Por ejemplo, las aplicaciones web o de grupo de trabajo que tienen requisitos de tráfico de entrada/salida de bajos a medios que admiten varias consultas concurrentes son buenas candidatas para el nivel de servicio Estándar.
* **Premium**: El nivel de servicio Premium proporciona un rendimiento predecible, segundo a segundo, para cada base de datos Premium. Si elige el nivel de servicio Premium, puede cambiar el tamaño de la aplicación de base de datos según la carga máxima para dicha base de datos. El plan elimina los casos en los que variaciones en el rendimiento pueden ocasionar que consultas de pequeño tamaño tarden más de lo habitual en operaciones sensibles a la latencia. Este modelo puede simplificar en gran medida los ciclos de desarrollo y validación de productos en aplicaciones que necesitan hacer declaraciones firmes sobre las necesidades máximas de recursos, variación de rendimiento o latencia de las consultas. La mayoría de los casos de uso del nivel de servicio Premium tienen una o varias de estas características:
  * **Carga máxima elevada**. Una aplicación que requiere mucha CPU, memoria o entrada/salida (E/S) para completar sus operaciones necesita un nivel de rendimiento alto dedicado. Por ejemplo, una operación de base de datos que se sabe que consume varios núcleos de CPU durante mucho tiempo es candidata al nivel de servicio Premium.
  * **Muchas solicitudes simultáneas**. Algunas aplicaciones de base de datos atienden muchas solicitudes simultáneas, por ejemplo, cuando dan servicio a un sitio web con un volumen elevado de tráfico. Los niveles de servicio Básico y Estándar limitan el número de solicitudes simultáneas por base de datos. Las aplicaciones que requieren más conexiones necesitarían elegir un tamaño de reserva adecuado para controlar el número máximo de solicitudes necesarias.
  * **Baja latencia**. Algunas aplicaciones necesitan garantizar una respuesta de la base de datos en un tiempo mínimo. Si se llama a un procedimiento almacenado determinado como parte de una operación de cliente más amplia, podría ser necesario volver de esa llamada en no más de 20 milisegundos el 99 % del tiempo. Este tipo de aplicación se beneficia del nivel de servicio Premium para asegurarse de que haya la capacidad de proceso necesaria.
* **Premium RS**: El nivel Premium RS se ha diseñado para cargas de trabajo intensivas de E/S pero que no requieren las mayores garantías de disponibilidad. Los ejemplos incluyen las pruebas en cargas de trabajo de alto rendimiento, o en una carga de trabajo analítico en el que la base de datos no es el sistema de registro.

El nivel de servicio que necesite para su base de datos SQL depende de los requisitos de carga máxima para cada dimensión de recursos. Algunas aplicaciones usan pocas cantidades de un recurso pero tienen necesidades considerables de otros.

### <a name="service-tier-capabilities-and-limits"></a>Límites y capacidades de nivel de servicio

En cada nivel de servicio se establece el nivel de rendimiento, de modo que tiene la flexibilidad de pagar únicamente por la capacidad que necesita. También puede [ajustar la capacidad](sql-database-service-tiers.md), hacia arriba o hacia abajo, según los cambios en la carga de trabajo. Por ejemplo, si la carga de trabajo de la base de datos es alta durante la temporada de compra de vuelta al colegio, puede aumentar el nivel de rendimiento de la base de datos durante un tiempo establecido, de julio a septiembre. Luego, puede reducirla cuando finalice la temporada de actividad máxima. Puede optimizar su entorno de nube para la estacionalidad de su negocio y así minimizar lo que paga. Este modelo también funciona bien para los ciclos de lanzamiento de productos de software. Un equipo de pruebas puede asignar capacidad mientras realiza ejecuciones de prueba y luego liberar esa capacidad cuando finalicen las pruebas. En un modelo de solicitud de capacidad, se paga por la capacidad que necesite, así se evita gastos en recursos dedicados que raramente usa.

### <a name="why-service-tiers"></a>¿Por qué niveles de servicio?
Aunque cada carga de trabajo de base de datos puede variar, los niveles de servicio tienen como fin proporcionar una previsión del rendimiento en diversos niveles de rendimiento. Los clientes con requisitos de recursos de base de datos a gran escala pueden trabajar en un entorno informático más dedicado.

## <a name="tune-your-application"></a>Optimización de la aplicación
En instancias de SQL Server locales tradicionales, el proceso de planeamiento inicial de la capacidad con frecuencia está separado del proceso de ejecución de una aplicación en producción. Primero se adquieren las licencias de hardware y productos y luego se ajusta el rendimiento. Cuando se usa Azure SQL Database, es una buena idea entrelazar el proceso de ejecutar una aplicación y optimizarla. Con el modelo de pago por capacidad a petición, puede optimizar la aplicación para usar el número mínimo de recursos necesarios, en lugar de aprovisionar en exceso el hardware en función de las estimaciones de planes de crecimiento futuro para una aplicación, que con frecuencia son incorrectas. Algunos clientes pueden decidir no optimizar una aplicación y, en su lugar, eligen aprovisionar en exceso los recursos de hardware. Este enfoque puede ser una buena idea si no quiere cambiar una aplicación clave durante un período de ocupación. Sin embargo, la optimización de una aplicación puede minimizar los requisitos de recursos y reducir las facturas mensuales cuando se usan los niveles de servicio en Azure SQL Database.

### <a name="application-characteristics"></a>Características de la aplicación
Aunque los niveles de servicio de Azure SQL Database están diseñados para mejorar la estabilidad del rendimiento y la previsibilidad de una aplicación, algunos procedimientos recomendados pueden ayudarle a optimizar la aplicación para un mejor aprovechamiento de los recursos en un nivel de rendimiento. Aunque muchas aplicaciones tienen importantes ganancias de rendimiento con solo cambiar a un nivel de rendimiento o de servicio superior, otras aplicaciones necesitan ajustes adicionales para beneficiarse de un nivel de servicio más alto. Para aumentar el rendimiento, puede realizar ajustes adicionales en las aplicaciones para que tengan estas características:

* **Aplicaciones que tienen un rendimiento lento debido a un comportamiento "comunicativo"**. Las aplicaciones comunicativas realizan excesivas operaciones de acceso a los datos que son sensibles a la latencia de red. Para reducir el número de operaciones de acceso a datos de la base de datos SQL, puede que tenga que modificar estos tipos de aplicaciones. Por ejemplo, puede mejorar el rendimiento de la aplicación mediante técnicas como el procesamiento por lotes de consultas ad-hoc o el movimiento de las consultas a procedimientos almacenados. Para más información, consulte [Consultas por lotes](#batch-queries).
* **Bases de datos con una carga de trabajo intensiva que no se admite en una sola máquina**. Las bases de datos que superan los recursos del más alto nivel de rendimiento Premium podrían beneficiarse de escalar horizontalmente la carga de trabajo. Para más información, consulte [Particionamiento entre bases de datos](#cross-database-sharding) y [Creación de particiones funcional](#functional-partitioning).
* **Aplicaciones que tienen consultas poco óptimas**. Puede que las aplicaciones, especialmente las de la capa de acceso a datos, que tienen consultas poco optimizadas no se beneficien de un mayor nivel de rendimiento. Esto incluye consultas que carecen de una cláusula WHERE, con índices que faltan o tienen estadísticas anticuadas. Estas aplicaciones se benefician de las técnicas de optimización del rendimiento de consultas estándar. Para más información, consulte [Índices que faltan](#identifying-and-adding-missing-indexes) y [Optimización de consultas y sugerencias](#query-tuning-and-hinting).
* **Aplicaciones que tienen un diseño de acceso a datos poco óptimo**. Puede que las aplicaciones que tienen problemas inherentes de simultaneidad de acceso a datos, por ejemplo, interbloqueos, no se beneficien de un nivel de rendimiento más alto. Considere la posibilidad de reducir los viajes de ida y vuelta a Azure SQL Database almacenando en caché los datos del lado cliente con el Servicio de almacenamiento en caché de Azure u otra tecnología de almacenamiento en caché. Consulte [Almacenamiento en caché de la capa de aplicación](#application-tier-caching).

## <a name="tune-your-database"></a>Ajuste de la base de datos
En esta sección, examinamos algunas técnicas que puede usar para optimizar Azure SQL Database para obtener el mejor rendimiento de la aplicación y ejecutarla con el menor nivel de rendimiento posible. Algunas de estas técnicas coinciden con los procedimientos recomendados de optimización tradicionales de SQL Server, pero otras son específicas de Azure SQL Database. En algunos casos, puede examinar los recursos consumidos en una base de datos para encontrar áreas de mejora adicional y ampliar las técnicas de SQL Server tradicionales para trabajar en Azure SQL Database.

### <a name="identify-performance-issues-using-azure-portal"></a>Identificación de problemas de rendimiento mediante Azure Portal
Las siguientes herramientas en Azure Portal pueden ayudarlo a analizar y corregir problemas de rendimiento con SQL Database:

* [Query Performance Insight](sql-database-query-performance.md)
* [Asesor de Base de datos SQL](sql-database-advisor.md)

En Azure Portal puede encontrar más información sobre estas dos herramientas y cómo usarlas. Para diagnosticar y corregir problemas de forma eficaz, se recomienda probar primero las herramientas en Azure Portal. Es recomendable que use los enfoques de optimización manual que describimos a continuación, para índices que faltan y optimización de consultas, en casos especiales.

Obtenga más información acerca de cómo identificar problemas en Azure SQL Database en el artículo de [supervisión de rendimiento](sql-database-single-database-monitor.md).

### <a name="identifying-and-adding-missing-indexes"></a>Identificación y adición de índices que faltan
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

