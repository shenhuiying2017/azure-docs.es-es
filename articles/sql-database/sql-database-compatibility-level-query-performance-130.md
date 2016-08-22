<properties
	pageTitle="Nivel de compatibilidad, cómo evaluar | Microsoft Azure"
	description="Pasos y herramientas para determinar qué nivel de compatibilidad es más adecuado para su base de datos en Base de datos SQL de Azure o Microsoft SQL Server"
	services="sql-database"
	documentationCenter=""
	authors="alainlissoir"
	manager="jhubbard"
	editor=""/>

<tags
	ms.service="sql-database"
	ms.workload="data-management"
	ms.devlang="NA"
	ms.tgt_pltfrm="NA"
	ms.topic="article"
	ms.date="08/08/2016"
	ms.author="alainl"/>


# Rendimiento mejorado de consultas con el nivel de compatibilidad 130 en Base de datos SQL de Azure


Base de datos SQL de Azure ejecuta de forma transparente cientos de miles de bases de datos en muchos niveles de compatibilidad diferentes, conservando y garantizando la compatibilidad con versiones anteriores para la versión correspondiente de Microsoft SQL Server para todos sus clientes.

Por lo tanto, nada impide que los clientes que modifiquen cualquier base de datos existente para que alcance el nivel de compatibilidad, se beneficien del nuevo optimizador de consultas y las nuevas características de procesador de consultas. Como recordatorio del historial, la alineación de las versiones SQL con los niveles de compatibilidad predeterminados es la siguiente:

- 100: en SQL Server 2008 y Base de datos SQL de Azure V11.
- 110: en SQL Server 2012 y Base de datos SQL de Azure V11.
- 120: en SQL Server 2014 y Base de datos SQL de Azure V12.
- 130: en SQL Server 2016 y Base de datos SQL de Azure V12.


> [AZURE.IMPORTANT] A partir de **mediados de junio de 2016**, el nivel de compatibilidad predeterminado en Base de datos SQL de Azure será 130, en lugar de 120, para las bases de datos **de nueva creación**.
> 
> Las bases de datos creadas antes de mediados de junio de 2016 *no* se verán afectadas y mantendrán su nivel de compatibilidad actual (100, 110 o 120). Así mismo, el nivel de compatibilidad de las bases de datos que migren desde la versión V11 a V12 de Base de datos SQL Azure tampoco cambiará.


En este artículo exploraremos las ventajas del nivel de compatibilidad 130 y la forma de aprovechar dichas ventajas. Abordaremos los posibles efectos secundarios en el rendimiento de las consultas para las aplicaciones existentes de SQL.


## Acerca del nivel de compatibilidad 130


En primer lugar, si desea conocer el nivel de compatibilidad actual de una base de datos, ejecute la siguiente instrucción de Transact-SQL.


```
SELECT compatibility_level
	FROM sys.databases
	WHERE name = '<YOUR DATABASE_NAME>’;
```


Antes de que se produzca este cambio al nivel 130 en las bases de datos **de nueva creación**, vamos a revisar en qué consiste este cambio a través de algunos ejemplos de consultas muy básicos y ver cómo cualquiera puede beneficiarse de él.

El procesamiento de consultas en las bases de datos relacionales puede ser muy complejo y llevar al uso de un volumen importante de matemáticas y ciencias informáticas para poder comprender los comportamientos y opciones de diseño inherentes. En este documento, el contenido se ha simplificado intencionadamente para garantizar que cualquier persona con unos conocimientos técnicos mínimos pueda entender el impacto del cambio de nivel de compatibilidad y determinar cómo puede beneficiar a las aplicaciones.

Echemos un vistazo a lo que aporta el nivel de compatibilidad 130. Puede encontrar más detalles en [Nivel de compatibilidad de ALTER DATABASE (Transact-SQL)](https://msdn.microsoft.com/library/bb510680.aspx), pero aquí tiene un breve resumen:

- La operación Insert de una instrucción Insert-select puede ser de subprocesos múltiples o puede tener un plan paralelo, mientras que antes esta operación era de subproceso único.
- Las consultas de tabla con optimización de memoria y variables de tabla pueden tener ahora planes paralelos, mientras que antes esta operación también era de subproceso único.
- Las estadísticas de una tabla con optimización de memoria ahora pueden muestrearse y se actualizan de forma automática. Consulte [Novedades (motor de base de datos): OLTP en memoria](https://msdn.microsoft.com/library/bb510411.aspx#InMemory) para más detalles.
- El modo por lotes frente al modo de fila cambia con los índices de almacenamiento de columnas
  - Las ordenaciones en una tabla con un índice de almacenamiento de columnas están ahora en modo por lotes.
  - Los agregados basados en ventanas operan ahora en modo por lotes, tal como instrucciones de TSQL LAG/LEAD.
  - Las consultas en tablas de almacenamiento de columnas con varias cláusulas Distinct funcionan en modo por lotes.
  - Las consultas que se ejecutan en DOP = 1 o con un plan en serie también se ejecutan en modo por lotes.
- Por último, las mejoras de estimación de cardinalidad vienen con el nivel de compatibilidad 120, pero para aquellos que estén trabajando en un nivel de compatibilidad inferior (es decir, 100 o 110), el cambio al nivel de compatibilidad 130 también aportará estas mejoras, y éstas a su vez pueden beneficiar también al rendimiento de las consultas de las aplicaciones.


## Prácticas con el nivel de compatibilidad 130


Primero vamos a juntar algunas tablas, índices y datos aleatorios creados para practicar algunas de estas nuevas funcionalidades. Los ejemplos de script TSQL se pueden ejecutar en SQL Server 2016, o en Base de datos SQL de Azure. Sin embargo, al crear una base de datos SQL de Azure, asegúrese de que elige como mínimo una base de datos P2 porque necesita al menos un par de núcleos para permitir subprocesamiento múltiple y por lo tanto beneficiarse de estas características.


```
-- Create a Premium P2 Database in Azure SQL Database

CREATE DATABASE MyTestDB
	(EDITION=’Premium’, SERVICE_OBJECTIVE=’P2′);
GO

-- Create 2 tables with a column store index on
-- the second one (only available on Premium databases)

CREATE TABLE T_source
	(Color varchar(10), c1 bigint, c2 bigint);

CREATE TABLE T_target
	(c1 bigint, c2 bigint);

CREATE CLUSTERED COLUMNSTORE INDEX CCI ON T_target;
GO

-- Insert few rows.

INSERT T_source VALUES
	(‘Blue’, RAND() * 100000, RAND() * 100000),
	(‘Yellow’, RAND() * 100000, RAND() * 100000),
	(‘Red’, RAND() * 100000, RAND() * 100000),
	(‘Green’, RAND() * 100000, RAND() * 100000),
	(‘Black’, RAND() * 100000, RAND() * 100000);

GO 200

INSERT T_source SELECT * FROM T_source;

GO 10
```


Ahora, echemos un vistazo a algunas de las características de procesamiento de consultas que trae el nivel de compatibilidad de 130.


## INSERT en paralelo


La ejecución de las siguientes instrucciones de TSQL, ejecuta la operación INSERT en los niveles de compatibilidad 120 y 130, que ejecutan respectivamente la operación INSERT en un modelo de subproceso único (120) y en un modelo de subprocesos múltiples (130).


```
-- Parallel INSERT … SELECT … in heap or CCI
-- is available under 130 only

SET STATISTICS XML ON;

ALTER DATABASE MyTestDB
	SET COMPATIBILITY_LEVEL = 120;
GO 

-- The INSERT part is in serial

INSERT t_target WITH (tablock)
	SELECT C1, COUNT(C2) * 10 * RAND()
		FROM T_source
		GROUP BY C1
	OPTION (RECOMPILE);

ALTER DATABASE MyTestDB
	SET COMPATIBILITY_LEVEL = 130
GO

-- The INSERT part is in parallel

INSERT t_target WITH (tablock)
	SELECT C1, COUNT(C2) * 10 * RAND()
		FROM T_source
		GROUP BY C1
	OPTION (RECOMPILE);

SET STATISTICS XML OFF;
```


Si solicita el estado del plan de consulta, y examina su representación gráfica o su contenido XML, puede determinar qué función de estimación de cardinalidad se está usando. Al examinar en paralelo los planes en la figura 1, podemos ver claramente que la ejecución del almacenamiento de columnas INSERT va de en serie en 120 a en paralelo en 130. Observe además el cambio del icono de iterador en el plan de 130 que muestra dos flechas paralelas, esto ilustra el hecho de que ahora la ejecución del iterador es realmente en paralelo. Si tiene que realizar grandes operaciones INSERT, la ejecución en paralelo, vinculada al número de núcleos que tiene a su disposición para la base de datos, le proporcionará un mejor rendimiento (hasta 100 veces más rápido, dependiendo de la situación).


*Figura 1: Operación INSERT cambia de en serie a en paralelo con el nivel de compatibilidad 130.*


![En la Ilustración 1](./media/sql-database-compatibility-level-query-performance-130/figure-1.jpg)


## Modo por lotes EN SERIE


De forma similar, pasar al nivel de compatibilidad 130 para el procesamiento de filas de datos permite el procesamiento de modo por lotes. En primer lugar, las operaciones en modo por lotes solo están disponibles cuando tiene un índice de almacenamiento de columnas preparado. En segundo lugar, un lote normalmente representa aproximadamente 900 filas y utiliza una lógica de código optimizada para una CPU de varios núcleos, un mayor rendimiento de la memoria y aprovecha directamente los datos comprimidos del almacenamiento de columnas, siempre que sea posible. En estas condiciones, SQL Server 2016 puede procesar aproximadamente 900 filas a la vez, en lugar de la 1 fila de cada vez, y en consecuencia, los costos generales de la operación se comparten ahora entre todo el lote, lo que reduce el costo global por fila. Esta cantidad de operaciones compartidas combinadas con la compresión de almacenamiento de columnas, básicamente reduce la latencia implicada en una operación de modo por lotes SELECT. Puede encontrar más detalles sobre el almacenamiento de columnas y el modo por lotes en [Descripción de los índices de almacén de columnas](https://msdn.microsoft.com/library/gg492088.aspx).


```
-- Serial batch mode execution

SET STATISTICS XML ON;

ALTER DATABASE MyTestDB
	SET COMPATIBILITY_LEVEL = 120;
GO

-- The scan and aggregate are in row mode

SELECT C1, COUNT (C2)
	FROM T_target
	GROUP BY C1
	OPTION (MAXDOP 1, RECOMPILE);
GO

ALTER DATABASE MyTestDB
	SET COMPATIBILITY_LEVEL = 130;
GO 

– The scan and aggregate are in batch mode,
-- and force MAXDOP to 1 to show that batch mode
-- also now works in serial mode.

SELECT C1, COUNT(C2)
	FROM T_target
	GROUP BY C1
	OPTION (MAXDOP 1, RECOMPILE);
GO

SET STATISTICS XML OFF;
```


Como se ve a continuación observando los planes de consulta en paralelo en la figura 2, el modo de procesamiento ha cambiado con el nivel de compatibilidad y, como consecuencia, cuando se ejecutan las consultas juntas en el nivel de compatibilidad de ambos, podemos ver que la mayor parte del tiempo de procesamiento se dedica al modo de fila (86 %), en comparación con el modo por lotes (14 %), donde se han procesado 2 lotes. Al aumentar el conjunto de datos, aumentará el beneficio.


*Figura 2: Operación SELECT cambia de modo en serie a modo por lotes con el nivel de compatibilidad 130.*


![Ilustración 2](./media/sql-database-compatibility-level-query-performance-130/figure-2.jpg)


## Modo por lotes en ejecución SORT


Similar a la anterior, pero aplicado a una operación SORT, la transición del modo de fila (nivel de compatibilidad 120) al modo por lotes (nivel de compatibilidad de 130) mejora el rendimiento de la operación SORT por las mismas razones.


```
-- Batch mode on sort execution

SET STATISTICS XML ON;

ALTER DATABASE MyTestDB
	SET COMPATIBILITY_LEVEL = 120;
GO

-- The scan and aggregate are in row mode

SELECT C1, COUNT(C2)
	FROM T_target
	GROUP BY C1
	ORDER BY C1
	OPTION (MAXDOP 1, RECOMPILE);
GO

ALTER DATABASE MyTestDB
	SET COMPATIBILITY_LEVEL = 130;
GO

-- The scan and aggregate are in batch mode,
-- and force MAXDOP to 1 to show that batch mode
-- also now works in serial mode.

SELECT C1, COUNT(C2)
	FROM T_target
	GROUP BY C1
	ORDER BY C1
	OPTION (MAXDOP 1, RECOMPILE);
GO

SET STATISTICS XML OFF;
```


En la figura 3, se puede ver en paralelo que la operación SORT en el modo de fila representa el 81 % del costo, mientras que el modo por lotes solo representa el 19 % del costo (respectivamente 81 % y 56 % en la ordenación).


*Figura 3: Operación SORT cambia de modo de fila a modo por lotes con el nivel de compatibilidad 130.*


![Ilustración 3](./media/sql-database-compatibility-level-query-performance-130/figure-3.png)


Obviamente, estos ejemplos solo contienen decenas de miles de filas, esto es una cantidad insignificante comparada con la de los datos disponibles en la mayoría de los servidores SQL de hoy en día. No hay más que pensar en estos resultados aplicados a millones de filas y veremos que esto podría traducirse en varios minutos de ejecución que se ahorrarían cada día, dependiendo de la naturaleza de la carga de trabajo.


## Mejoras de estimación de cardinalidad (CE)


Desde que se introdujo con SQL Server 2014, cualquier base de datos que se ejecuta en un nivel de compatibilidad 120 o superior hace uso de la nueva funcionalidad de estimación de cardinalidad. Básicamente, la estimación de cardinalidad es la lógica utilizada para determinar cómo SQL Server ejecutará una consulta en base a su costo estimado. La estimación se calcula usando la entrada de datos de estadísticas asociadas con los objetos que participan en esa consulta. En la práctica, en un nivel alto, las funciones de estimación de cardinalidad son estimaciones de recuento de filas junto con información sobre la distribución de los valores, recuentos de valores distintos y recuentos duplicados, contenidas en las tablas y los objetos a los que se hace referencia en la consulta. Si se obtienen estimaciones incorrectas, se puede producir E/S de disco innecesarias debidas a concesiones de memoria insuficientes (es decir, vertidos TempDB), o a la selección de una ejecución de un plan en serie en lugar de una ejecución de plan paralelo, por solo mencionar algunos problemas. La conclusión es que las estimaciones incorrectas pueden provocar una degradación del rendimiento general de la ejecución de consultas. Por el contrario, las estimaciones mejoradas y más precisas, llevan a la mejora de las ejecuciones de consultas.

Como se mencionó antes, las optimizaciones de consultas y las estimaciones son una cuestión compleja, pero si desea más información acerca de los planes de consulta y el calculador de cardinalidad, puede consultar el documento [Optimizing Your Query Plans with the SQL Server 2014 Cardinality Estimator](https://msdn.microsoft.com/library/dn673537.aspx) (Optimización de los planes de consultas con el calculador de cardinalidad de SQL Server 2014) para un análisis más profundo.


## ¿Qué estimación de cardinalidad está usando actualmente?


Para determinar con qué estimaciones de cardinalidad se ejecutan las consultas, usemos los siguientes ejemplos de consulta. Tenga en cuenta que este primer ejemplo se ejecutará en el nivel de compatibilidad 110, lo que implica el uso de las antiguas funciones de estimación de cardinalidad.


```
-- Old CE

ALTER DATABASE MyTestDB
	SET COMPATIBILITY_LEVEL = 110;
GO

SET STATISTICS XML ON;

SELECT [c1]
	FROM [dbo].[T_target]
	WHERE [c1] > 20000;
GO

SET STATISTICS XML OFF;
```


Una vez completada la ejecución, haga clic en el vínculo XML y examine las propiedades del primer iterador como se muestra a continuación. Tenga en cuenta el nombre de propiedad llamado CardinalityEstimationModelVersion establecido actualmente en 70. Esto no significa que el nivel de compatibilidad de base de datos está establecida en la versión 7.0 de SQL Server (se establece en 110 como puede verse en las instrucciones de TSQL anteriores), pero el valor 70 simplemente representa la funcionalidad heredada de estimación de cardinalidad disponible desde SQL Server 7.0, que no ha sufrido revisiones sustanciales hasta SQL Server 2014 (que viene con un nivel de compatibilidad de 120).


*Figura 4: CardinalityEstimationModelVersion está establecida en 70, cuando se usa un nivel de compatibilidad de 110 o inferior.*


![Ilustración 4](./media/sql-database-compatibility-level-query-performance-130/figure-4.png)


Como alternativa, puede cambiar el nivel de compatibilidad a 130 y deshabilitar el uso de la nueva función de estimación de cardinalidad usando LEGACY\_CARDINALITY\_ESTIMATION establecido en ON con [ALTER DATABASE SCOPED CONFIGURATION](https://msdn.microsoft.com/library/mt629158.aspx) (Modificar configuración de ámbito de base de datos). Esto será exactamente lo mismo que usar 110 desde el punto de vista de una función de estimación de cardinalidad, mientras se utiliza el nivel de compatibilidad de procesamiento de consultas más reciente. De esta forma, puede beneficiarse de las nuevas características de procesamiento de consultas que vienen con el nivel de compatibilidad más reciente (es decir, el modo por lotes), y a la vez seguir dependiendo de la funcionalidad de estimación de cardinalidad anterior si es necesario.


```
-- Old CE

ALTER DATABASE MyTestDB
	SET COMPATIBILITY_LEVEL = 130;
GO

ALTER DATABASE
	SCOPED CONFIGURATION
	SET LEGACY_CARDINALITY_ESTIMATION = ON;
GO

SET STATISTICS XML ON;

SELECT [c1]
	FROM [dbo].[T_target]
	WHERE [c1] > 20000;
GO

SET STATISTICS XML OFF;
```


Basta con mover el nivel de compatibilidad 120 o 130 para habilitar la nueva funcionalidad de estimación de cardinalidad. En tal caso, el valor predeterminado CardinalityEstimationModelVersion se configurará según corresponda a 120 o 130 como se puede ver a continuación.


```
-- New CE

ALTER DATABASE MyTestDB
	SET COMPATIBILITY_LEVEL = 130;
GO

ALTER DATABASE
	SCOPED CONFIGURATION
	SET LEGACY_CARDINALITY_ESTIMATION = OFF;
GO

SET STATISTICS XML ON;

SELECT [c1]
	FROM [dbo].[T_target]
	WHERE [c1] > 20000;
GO

SET STATISTICS XML OFF;
```


*Figura 5: CardinalityEstimationModelVersion está establecida en 130, cuando se usa un nivel de compatibilidad de 130.*


![Ilustración 5.](./media/sql-database-compatibility-level-query-performance-130/figure-5.jpg)


## Observación de las diferencias de estimación de cardinalidad


Ahora, vamos a ejecutar una consulta un poco más compleja que implica una INNER JOIN con una cláusula WHERE con algunos predicados, y vamos a echar un vistazo en primer lugar a la estimación del recuento de filas de la antigua función de estimación de cardinalidad.


```
-- Old CE row estimate with INNER JOIN and WHERE clause

ALTER DATABASE MyTestDB
	SET COMPATIBILITY_LEVEL = 130;
GO

ALTER DATABASE
	SCOPED CONFIGURATION
	SET LEGACY_CARDINALITY_ESTIMATION = ON;
GO

SET STATISTICS XML ON;

SELECT T.[c2]
	FROM
		           [dbo].[T_source] S
		INNER JOIN [dbo].[T_target] T  ON T.c1=S.c1
	WHERE
		S.[Color] = ‘Red’  AND
		S.[c2] > 2000  AND
		T.[c2] > 2000
	OPTION (RECOMPILE);
GO

SET STATISTICS XML OFF;
```


La ejecución de esta consulta devuelve eficazmente 200.704 filas, mientras que la estimación de filas con la antigua funcionalidad de estimación de cardinalidad notifica 194.284 filas. Obviamente, como ya se dijo antes, estos resultados del recuento de filas también dependerán de la frecuencia con la que haya ejecutado los ejemplos anteriores, que rellenan las tablas de ejemplo una y otra vez en cada ejecución. Obviamente, los predicados de la consulta influirán también en la estimación real aparte de la forma de tabla, el contenido de datos, y la forma en la que estos datos s relacionan entre sí.


*Figura 6: La estimación del número de filas es 194.284 es decir 6.000 filas menos de las 200.704 filas esperadas.*


![Ilustración 6.](./media/sql-database-compatibility-level-query-performance-130/figure-6.jpg)


Del mismo modo, vamos ahora a ejecutar la misma consulta con la nueva funcionalidad de estimación de cardinalidad.


```
-- New CE row estimate with INNER JOIN and WHERE clause

ALTER DATABASE MyTestDB
	SET COMPATIBILITY_LEVEL = 130;
GO

ALTER DATABASE
	SCOPED CONFIGURATION
	SET LEGACY_CARDINALITY_ESTIMATION = OFF;
GO

SET STATISTICS XML ON;

SELECT T.[c2]
	FROM
		           [dbo].[T_source] S
		INNER JOIN [dbo].[T_target] T  ON T.c1=S.c1
	WHERE
		S.[Color] = ‘Red’  AND
		S.[c2] > 2000  AND
		T.[c2] > 2000
	OPTION (RECOMPILE);
GO

SET STATISTICS XML OFF;
```


Mirando a continuación vemos que la estimación de filas es 202.877, es decir mucho más cercana y superior que la antigua estimación de cardinalidad.

*Figura 7: La estimación del número de filas es ahora 202.877, en lugar de 194.284.*


![Ilustración 7.](./media/sql-database-compatibility-level-query-performance-130/figure-7.jpg)


En realidad, el conjunto de resultados es 200.704 filas (pero todo depende de la frecuencia con la que se ejecutaron las consultas de los ejemplos anteriores, y lo que es más importante porque el TSQL utiliza la instrucción de RAND(), los valores reales que se devuelven pueden variar de una ejecución a la siguiente). Por lo tanto, en este ejemplo concreto, la nueva estimación de cardinalidad hace una mejor estimación del número de filas, no cabe duda de que 202.877 está mucho más cerca de 200.704 que 194.284. Por último, si se cambian los predicados de la cláusula WHERE para igualdad (en lugar de ">" por ejemplo), esto podría hacer que haya incluso más diferencia entre las estimaciones de la antigua y la nueva función de cardinalidad, dependiendo de las coincidencias que se obtengan.

En este caso, una diferencia de aproximadamente 6000 filas con respecto recuento real, en algunas situaciones no representa una gran cantidad de datos. Ahora, si se traspone esto a millones de filas en varias tablas y consultas más complejas, a veces, puede haber una diferencia de millones de filas en la estimación y, por lo tanto, el riesgo de seleccionar el plan de ejecución equivocado, o de solicitar concesiones de memoria insuficientes provocando desbordamientos de TempDB y con ello más operaciones de E/S, son mucho mayores.

Si tiene oportunidad practique esta comparación con sus consultas y conjuntos de datos más habituales, y vea por usted mismo la diferencia entre las estimaciones nuevas y las antiguas, mientras que algunas se pueden alejar más de la realidad, en otras se acerca más al recuento real devuelto en los conjuntos de resultados. Todo dependerá de la forma de las consultas, las características de base de datos SQL de Azure, la naturaleza y el tamaño de los conjuntos de datos y las estadísticas disponibles sobre ellos. Si acaba de crear la instancia de Base de datos SQL de Azure, el optimizador de consultas tendrá que generar su conocimiento desde cero en lugar de reutilizar las estadísticas de las ejecuciones de consultas anteriores. Por lo tanto, las estimaciones son muy contextuales y casi específicas para cada situación de aplicación y servidor. Es un aspecto importante que no hay que olvidar.


## Algunas consideraciones a tener en cuenta


Aunque la mayoría de las cargas se benefician del nivel de compatibilidad 130, antes de adoptar el nivel de compatibilidad para el entorno de producción, tiene básicamente 3 opciones:

1. Mover al nivel de compatibilidad 130 y ver cómo funciona todo. Si nota algunas regresiones simplemente vuelva a establecer la compatibilidad de nivel en el nivel original, o mantenga el nivel 130 y devuelva solo la estimación de cardinalidad al modo heredado (como se explicó anteriormente, esto por sí solo podría solucionar el problema).
2. Probar detalladamente las aplicaciones existentes con una carga similar a la de producción, ajustar y validar el rendimiento antes de pasar a producción. En el caso de que aparezcan problemas, igual que en el caso anterior, siempre puede volver al nivel de compatibilidad original, o simplemente devolver la estimación de cardinalidad al modo heredado.
3. Como última opción, y la forma más reciente resolver estas cuestiones, puede aprovechar el Almacén de consultas. Esta es la opción recomendada en este momento. Para ayudarle en el análisis de las consultas con el nivel de compatibilidad 120 o inferior frente al nivel 130, no podemos insistir lo suficiente en nuestra recomendación de que use el Almacén de consultas. El Almacén de consultas está disponible con la versión más reciente de Base de datos SQL de Azure V12 y se ha diseñado para ayudarle a solucionar problemas de rendimiento de las consultas. Piense en el Almacén de consultas como una caja negra para la base de datos en donde se recopila y presenta información detallada del historial de todas las consultas. Esto simplifica enormemente el análisis forense del rendimiento, reduciendo el tiempo de diagnóstico y la resolución de problemas. Puede encontrar más información en [A flight data recorder for your database](https://azure.microsoft.com/blog/query-store-a-flight-data-recorder-for-your-database/) (Una caja negra para la base de datos).


En el nivel superior, si ya tiene un conjunto de bases de datos que se ejecuta en el nivel de compatibilidad 120 o por debajo y planea cambiar algunas de ellas a 130, o si la carga de trabajo aprovisiona automáticamente nuevas bases de datos que pronto se establecerán de forma predeterminada en el nivel 130, tenga en cuenta lo siguiente:

- Antes de cambiar al nuevo nivel de compatibilidad en producción, habilite el Almacén de consultas. Puede consultar [Change the Database Compatibility Mode and Use the Query Store](https://msdn.microsoft.com/library/bb895281.aspx) (Cambio del modo de compatibilidad de la base de datos y uso del Almacén de consultas) para más información.
- A continuación, pruebe todas las cargas de trabajo críticas con datos representativos y las consultas en un entorno similar al de producción y compare el rendimiento que ha experimentado y la información proporcionada por el Almacén de consultas. Si se producen algunas regresiones, puede identificar las consultas con regresión con el Almacén de consultas y utilizar la opción para forzar el plan del Almacén de consultas (también conocido como plan de anclaje). En este caso, permanezca con el nivel de compatibilidad 130 y use el plan de consulta anterior como sugiere el Almacén de consultas.
- Si desea aprovechar las nuevas características y capacidades de Base de datos SQL de Azure (que ejecuta SQL Server 2016), pero es susceptible en relación a los cambios realizados por el nivel de compatibilidad 130, como último recurso, puede considerar la posibilidad de forzar el nivel de compatibilidad para volver al nivel que mejor se adapte a su carga de trabajo usando una instrucción ALTER DATABASE. Pero en primer lugar, tenga en cuenta que la opción de anclaje de plan de Almacén de consultas es la mejor opción, porque no usar el nivel 130 es básicamente mantenerse en el nivel de funcionalidad de una versión anterior de SQL Server.
- Si tiene aplicaciones multiinquilinos que abarcan varias bases de datos, puede ser necesario actualizar la lógica de aprovisionamiento de las bases de datos para asegurar un nivel de compatibilidad coherente en todas las bases de datos, las antiguas y las creadas recientemente. El rendimiento de carga de trabajo de su aplicación podría ser sensible al hecho de que algunas bases de datos se ejecutan en niveles diferentes de compatibilidad y, por lo tanto, podría ser necesaria la coherencia de nivel de compatibilidad en cualquier base de datos con el fin de proporcionar la misma experiencia a todos los clientes. Tenga en cuenta que esto no es obligatorio, realmente depende de cómo afecte el nivel de compatibilidad a la aplicación.
- Por último, con respecto a la estimación de cardinalidad, igual que con el cambio de nivel de compatibilidad, antes de proceder con los cambios en el nivel de producción, se recomienda probar la carga de trabajo de producción en las nuevas condiciones para determinar si la aplicación se beneficia de las mejoras de estimación de cardinalidad.


## Conclusión


El uso de Base de datos SQL de Azure para beneficiarse de todas las mejoras de SQL Server 2016 puede mejorar claramente sus ejecuciones de consultas. Así de simple. Por supuesto, al igual que con cualquier característica nueva, tiene que realizarse una evaluación adecuada para determinar las condiciones exactas en las que la carga de trabajo de la base de datos funciona mejor. La experiencia demuestra que es de esperar que la mayoría de las cargas de trabajo se ejecuten al menos de forma transparente en el nivel de compatibilidad 130, mientras se aprovechan las nuevas funciones de procesamiento de consultas y la nueva estimación de cardinalidad. De todas formas, siendo realistas, siempre hay algunas excepciones y actuar con la debida diligencia es una valoración importante para determinar cuánto puede beneficiarse de estas mejoras. Y repetimos que el Almacén de consultas puede ser una gran ayuda para realizar este trabajo.

A medida que SQL Azure evoluciona, se puede esperar un nivel de compatibilidad 140 en el futuro. Cuando el momento sea apropiado, empezaremos a hablar de lo que aportará este futuro nivel de compatibilidad 140, igual que hemos explicado brevemente aquí lo que el nivel de compatibilidad 130 aporta hoy.

Por ahora, no olvide que a partir de junio de 2016, Base de datos SQL de Azure cambiará el nivel de compatibilidad predeterminado de 120 a 130 para las bases de datos de nueva creación. ¡Prepárese!


## Referencias


- [Novedades (motor de base de datos)](https://msdn.microsoft.com/library/bb510411.aspx#InMemory)

- [Blog: Query Store: A flight data recorder for your database (Almacén de consultas: Una caja negra para la base de datos) por Borko Novakovic, 8 de junio de 2016](https://azure.microsoft.com/blog/query-store-a-flight-data-recorder-for-your-database/)

- [Nivel de compatibilidad de ALTER TABLE (Transact-SQL)](https://msdn.microsoft.com/library/bb510680.aspx)

- [ALTER DATABASE SCOPED CONFIGURATION](https://msdn.microsoft.com/library/mt629158.aspx)

- [Compatibility Level 130 for Azure SQL Database V12](https://azure.microsoft.com/updates/compatibility-level-130-for-azure-sql-database-v12/) (Nivel de compatibilidad 130 para V12 de Base de datos SQL de Azure

- [Optimizing Your Query Plans with the SQL Server 2014 Cardinality Estimator](https://msdn.microsoft.com/library/dn673537.aspx) (Optimización de los planes de consultas con el calculador de cardinalidad de SQL Server 2014)

- [Descripción de los índices de almacén de columnas](https://msdn.microsoft.com/library/gg492088.aspx)

- [Blog: Improved Query Performance with Compatibility Level 130 in Azure SQL Database (Rendimiento mejorado de consultas con el nivel de compatibilidad 130 en Base de datos SQL de Azure) por Alain Lissoir, 6 de mayo de 2016](https://blogs.msdn.microsoft.com/sqlserverstorageengine/2016/05/06/improved-query-performance-with-compatibility-level-130-in-azure-sql-database/)



<!--
Improved Query Performance with Compatibility Level 130 in Azure SQL Database

May 6, 2016 by Alain Lissoir (AlainL), on GitHub 'alainlissoir'.

https://blogs.msdn.microsoft.com/sqlserverstorageengine/2016/05/06/improved-query-performance-with-compatibility-level-130-in-azure-sql-database/

..... Now, above.
....................
..... Soon, below?

CAPS / MSDN ideally, but instead on ACom:
.. # Assess effects of latest compatibility level on query performance, how to

sql-database-compatibility-level-query-performance-130.md

genemi = MightyPen , 2016-05-20  Friday  17:00pm
-->

<!---HONumber=AcomDC_0810_2016-->