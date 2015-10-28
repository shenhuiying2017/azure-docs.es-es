<properties 
	pageTitle="Niveles de compatibilidad de Base de datos SQL | Microsoft Azure" 
	description="Explica cómo establecer el nivel de compatibilidad de la base de datos de Base de datos SQL de Azure y las características que se ven afectadas."
	services="sql-database" 
	documentationCenter="" 
	authors="MightyPen" 
	manager="jeffreyg" 
	editor=""/>


<tags 
	ms.service="sql-database" 
	ms.workload="data-management" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="09/21/2015" 
	ms.author="genemi"/>


# Niveles de compatibilidad de Base de datos SQL


Para Base de datos SQL de Azure, este tema describe las opciones de las características que puede elegir mediante la instrucción Transact-SQL **ALTER DATABASE**. El concepto de nivel de compatibilidad está diseñado para reducir cualquier riesgo de actualización.


La mayoría de las características que se activan o desactivan el nivel de compatibilidad forman parte del optimizador de consultas. Microsoft hace que la activación de una nueva característica del optimizador de consultas depende de la compatibilidad de nivel cuando:


- El contexto actualizado modifica la semántica de una característica anterior.
- La optimización de consultas tiene una posibilidad plausible de perjudicar el rendimiento de ciertas consultas SQL poco frecuentes, pero legítimas.


Si la consulta no funcione tan bien después de actualizar una versión al servidor de Base de datos SQL de Azure o la base de datos, puede reducir el nivel de compatibilidad. El valor inferior puede desactivar un reducido conjunto de mejoras del optimizador, probablemente incluido el que es desfavorable para su consulta.


## Funcionamiento del nivel de compatibilidad


Microsoft SQL Server ha tenido niveles de compatibilidad configurables durante años. A partir de la versión V12, Base de datos SQL de Azure adopta también los niveles de compatibilidad. La activación de algunas nuevas características se controla mediante la configuración del nivel de compatibilidad dentro de cada base de datos de Base de datos SQL. Los valores posibles incluyen:


- 110: el valor más bajo posible y, por lo tanto, la configuración que excluye la mayoría de las nuevas características.
- 120: corresponde aproximadamente a Base de datos SQL V11 (lo que significa que `SELECT @@version;` devuelve **11.**0.0.0).
 - La versión V11 también se denomina 'SAWA V2' de Base de datos SQL.
 - 120 es el valor más alto en Microsoft SQL Server 2014.
- 130: corresponde aproximadamente a V12 (lo que significa que `SELECT @@version;` devuelve **12.**0.0.0).
 - 130 es el valor más alto en Microsoft SQL Server 2016.


Por ejemplo, un valor de 120 significa que la base de datos tiene acceso al subconjunto de características que se activará en el nivel 120, *más* características que se activan en cualquier valor inferior, como 110. Cuando se establece en 120, la base de datos no tiene acceso a características del nivel 130.


## Lectura o definición del nivel de compatibilidad


### Lectura del nivel de compatibilidad


```
SELECT d.name, d.compatibility_level
	FROM sys.databases AS d
	WHERE d.name = db_name();
```


### Definición del nivel de compatibilidad


```
ALTER DATABASE YourDatabaseName SET COMPATIBILITY_LEVEL = 130;
```


Para más información, consulte:


- [Nivel de compatibilidad de ALTER TABLE (Transact-SQL)](http://msdn.microsoft.com/library/bb510680.aspx)
- [sys.databases (Transact-SQL)](http://msdn.microsoft.com/library/ms178534.aspx)


## Tablas en memoria e índices de almacén de columnas


La mayoría de las características del nivel de compatibilidad que se activan en los niveles 120 y 130 están relacionadas con las tablas e índices que están *en memoria*. Por lo tanto, los elementos importantes incluyen:


- Tablas optimizadas para la memoria
- Índices de almacén de columnas


Cuando estén implicados elementos en la memoria, el nivel de compatibilidad 130 ofrece las siguientes ventajas:


- La capacidad del optimizador de consultas de procesar más consultas en el modo por *lotes* está activada.
 - El modo por lotes es más rápido que el modo por *filas* cuando están implicadas muchas filas.
- La incorporación del operador **SORT**.
- La incorporación de agregados de ventana.


Para obtener más información acerca de tablas en memoria y los índices de almacén de columnas, consulte:


- [Tablas optimizadas para la memoria](http://msdn.microsoft.com/library/dn133165.aspx)
- [CREATE CLUSTERED COLUMNSTORE INDEX (Transact-SQL)](http://msdn.microsoft.com/library/dn511016.aspx)
- [Descripción de los índices de almacén de columnas](http://msdn.microsoft.com/library/gg492088.aspx)


## Características generales que requieren el nivel 130 como mínimo


El calculador de cardinalidad (CE) genera y almacena estadísticas sobre el número aproximado de filas de una tabla. Muchas partes del optimizador de consultas usa la información de cardinalidad.


Se han mejorado los algoritmos usados por el CE. Las mejoras provocan cambios, y los cambios pueden provocar la regresión de casos especializados poco frecuentes.


| 130 es el nivel mínimo<br/>necesario<br/> | Área de consulta,<br/>General | Detalles de las mejoras del<br/>plan de consulta<br/> |
| :-- | :-- | :-- |
| 130 | Calculador de cardinalidad (CE) | Refinamiento del calculador de cardinalidad (CE), en comparación con el CE anterior en el nivel 120.<br/><br/>En el plan de consulta podría ver: **CardinalityEstimationModelVersion="130"**<br/><br/>**Trace flag** [**9481**](http://www.sqlservergeeks.com/sql-server-2014-trace-flags-9481/) puede activarse para usar el CE del nivel 120 cuando la base de datos se encuentra en el nivel 130.<br/><br/>**Trace flag** [**4199**](http://support.microsoft.com/kb/974006), cuando la base de datos está en el nivel de compatibilidad 130, se puede establecer en desactivado para no recibir las revisiones del optimizador de consultas. La marca solo se aplica a las revisiones que se implementan después del nivel 130, está totalmente fuera de la vista previa y se ha lanzado para disponibilidad general. Para obtener información detallada, consulte:<br/><br/>● [DBCC TRACEON](http://msdn.microsoft.com/library/ms187329.aspx) |
| 130 | Planes de consulta paralelos para las tablas en memoria | Las consultas pueden usar varios subprocesos y se pueden ejecutar en paralelo cuando están en una tabla en memoria, lo que significa una tabla que se ha creado con la cláusula **MEMORY\_OPTIMIZED = YES**. El paralelismo puede hacer que las consultas se ejecuten más rapidamente.<br/><br/>Esta mejora es compatible con Transact-SQL normal y procedimientos almacenados del usuario. Pero no se admite para los procedimientos almacenados nativos que se compilan en una DLL. |


## Características del índice de almacén de columnas que requieren el nivel 130 como mínimo


En el nivel de compatibilidad 130, las mejoras del optimizador de consultas pueden producir un nuevo plan de consulta. Los planes modificada que implican un índice de almacén de columnas, el cambio implica normalmente uno de los siguientes:


- Una reducción en las circunstancias en la que los datos deben copiarse para una operación de subproceso adicional.
- Un cambio en el procesamiento de *filas* para el procesamiento por ***lotes*** para operaciones como la ordenación.


En la mayoría de los casos, el cambio del plan de consulta mejora el rendimiento de la consulta al implicar:


- Inserciones en paralelo en un índice de almacén de columnas.
 - El nivel 130 no proporciona el examen paralelo de los índices no agrupados.
- Mayor uso de la base de datos **tempdb**.


| 130 es el nivel mínimo<br/>necesario<br/> | Área de consulta,<br/>Índice de almacén de columnas | Detalles de las mejoras del<br/>plan de consulta<br/> |
| :-- | :-- | :-- |
| 130 | Consultas de características | El rendimiento mejora al cambiar al modo por lotes en los casos siguientes:<br/><br/>• Cuando la ordenación está implicada.<br/><br/>• Se agrega con *varias* funciones distintas<br/>(una función de cada uno de dos puntos diferentes de la lista siguiente):<br/>&nbsp;&nbsp;&nbsp;▫ **COUNT** *o* **COUNT\_BIG**<br/>&nbsp;&nbsp;&nbsp;▫ **AVG** *o* **SUM**<br/>&nbsp;&nbsp;&nbsp;▫ **CHECKSUM\_AGG**<br/>&nbsp;&nbsp;&nbsp;▫ **STDEV** *o* **STDEVP**<br/><br/>• Funciones de agregados de ventana<br/>(descritas [aquí en MSDN](http://msdn.microsoft.com/library/ms189461.aspx) y [en este artículo de Kathi Kellenberger](http://www.bidn.com/blogs/KathiKellenberger/sql-server/4397/what-is-a-window-aggregate-function)):<br/>&nbsp;&nbsp;&nbsp;▫ **COUNT**, **COUNT\_BIG**, **SUM**, **AVG**, **MIN**, **MAX**, **CLR**<br/><br/>• Agregados de ventana [definidos por el usuario](http://msdn.microsoft.com/library/ms131057.aspx):<br/>&nbsp;&nbsp;&nbsp;▫ [**CHECKSUM\_AGG**](http://msdn.microsoft.com/library/ms188920.aspx), [**STDEV**](http://msdn.microsoft.com/library/ms190474.aspx), [**STDEVP**](http://msdn.microsoft.com/library/ms176080.aspx), [**VAR**](http://msdn.microsoft.com/library/ms186290.aspx), [**VARP**](http://msdn.microsoft.com/library/ms188735.aspx), [**GROUPING**](http://msdn.microsoft.com/library/ms178544.aspx)<br/><br/>• Funciones analíticas de agregados de ventana:<br/>&nbsp;&nbsp;&nbsp;▫ [**LAG**](http://msdn.microsoft.com/library/hh231256.aspx), [**LEAD**](http://msdn.microsoft.com/library/hh213125.aspx), [**FIRST\_VALUE**](http://msdn.microsoft.com/library/hh213018.aspx), [**LAST\_VALUE**](http://msdn.microsoft.com/library/hh231517.aspx), [**PERCENTILE\_CONT**](http://msdn.microsoft.com/library/hh231473.aspx), [**PERCENTILE\_DISC**](http://msdn.microsoft.com/library/hh231327.aspx), [**CUME\_DIST**](http://msdn.microsoft.com/library/hh231078.aspx), [**PERCENT\_RANK**](http://msdn.microsoft.com/library/hh213573.aspx) |
| 130 | Plan de consulta en serie con un único subproceso | Una consulta ejecutada en un solo subproceso puede ejecutarse en el modo por lotes. Esto puede hacer que el funcionamiento de la consulta sea más rápido.<br/><br/>Un plan de consulta puede estar diseñado como un único subproceso, o bien una consulta podría ejecutarse en **MAXDOP 1**. |
| 130 | Inserción en paralelo | El plan de consulta puede realizar algunas de las inserciones en paralelo.<br/<br/>El [ejemplo](#ExampleQueryParallelCciByCompatLevel) que aparece más adelante en este tema muestra este paralelismo. |
| 130 | Anti-semi join | Este operador ahora se puede ejecutar en el modo por lotes. |


## Características generales que requieren el nivel 120 como mínimo


| 120 es el nivel mínimo<br/>necesario<br/> | Área de consulta,<br/>General | Detalles de las mejoras del<br/>plan de consulta<br/> |
| :-- | :-- | :-- |
| 120 | [Modificar tablas con optimización para memoria](http://msdn.microsoft.com/library/dn269114.aspx) | Permite realizar operaciones Transact-SQL **ALTER TABLE** en tablas que tienen **MEMORY\_OPTIMIZED = YES**.<br/><br/>La aplicación de base de datos puede ejecutarse, pero las operaciones que tienen acceso a la tabla se bloquean hasta que **ALTER TABLE** se completa. |
| 120 | [Crear y administrar el almacenamiento de objetos con optimización para memoria](http://msdn.microsoft.com/library/dn133174.aspx) | El motor OLTP en memoria está integrado en SQL Server. Esto le permite tener tablas **MEMORY\_OPTIMIZED** y tablas tradicionales basadas en disco en la misma base de datos. |
| 120 | [Compatibilidad de Transact-SQL con OLTP en memoria](http://msdn.microsoft.com/library/dn133180.aspx) | Se ha mejorado una serie de comandos de Transact-SQL para admitir el procesamiento de transacciones en línea en memoria (OLTP).<br/><br/>Un ejemplo es la nueva palabra clave **NATIVE\_COMPILATION** en el comando [CREATE PROCEDURE](http://msdn.microsoft.com/library/ms187926.aspx). |
| 120 | Calculador de cardinalidad (CE) | Refinamiento del calculador de cardinalidad (CE), en comparación con el CE anterior en el nivel 110.<br/><br/>En el plan de consulta podría ver: **CardinalityEstimationModelVersion="120"**<br/><br/>Para obtener más información acerca de cómo **trace flag 4199** interactúa con el valor de nivel de compatibilidad, consulte [KB 974006](http://support.microsoft.com/kb/974006).|


## Características del índice de almacén de columnas que requieren el nivel mínimo de 120


Esta sección describe las [características de la indexación del almacén de columnas](http://msdn.microsoft.com/library/dn934994.aspx) que se activan en el nivel de compatibilidad 120 o superior.


| 120 es el nivel mínimo de<br/>compatibilidad<br/> | Índice de almacén de columnas<br/>nombre de la característica | Índice de almacén de columnas<br/>Descripción de la característica |
| :-- | :-- | :-- |
| 120 | Nivel de aislamiento de instantáneas y<br/><br/>nivel de aislamiento instantáneo de lectura confirmada (RCSI). | Cuando el plan de consulta implica un índice de almacén de columnas, SI y RCSI impiden que los datos de transacciones parcialmente completadas se incluyan en los resultados de la consulta, sin necesidad de bloqueos excesivos. |
| 120 | Desfragmentación de índices | Las filas eliminadas se quitan sin una regeneración de índice explícita.<br/><br/>**ALTER INDEX ... REORGANIZE ** quita las filas eliminadas del índice de almacén de columnas mientras la tabla y el índice permanecen operativos en línea. |
| 120 | Accesible en una [réplica secundaria legible](http://msdn.microsoft.com/library/ff878253.aspx) de AlwaysOn | Puede mejorar el rendimiento de análisis operativo mediante la descarga de las consultas de análisis a una réplica secundaria de AlwaysOn. |
| 120 | Aplicación de agregados,<br/>durante la fase de análisis de tablas de las funciones de agregado | Mejora el rendimiento realizando cálculos provisionales anteriormente en el plan de consulta, para que sea necesario copiar menos datos en fases posteriores.<br/><br/>Se aplica a **MIN**, **MAX**, **SUM**, **COUNT** y **AVG**.<br/><br/>Se aplica solo cuando el tipo de datos es de ocho bytes o menos, pero no para las cadenas. |
| 120 | Aplicación de cláusulas **WHERE** basadas en cadenas | La optimización de la aplicación de predicados puede acelerar las consultas que comparan datos de cadenas de tipo &#x5b;var&#x5d;char o n&#x5b;var&#x5d;char. Esta optimización:<br/><br/>• Se aplica a los operadores de comparación comunes incluido **LIKE**, que usa filtros de mapa de bits.<br/><br/>• Solo funciona si hay un predicado de cadena.<br/><br/>• Funciona con todas las intercalaciones que admite el producto.<br/><br/>Si desea obtener más detalles acerca de los filtros de mapa de bits, consulte esta publicación en el blog: [Intro to Query Execution Bitmap Filters](http://blogs.msdn.com/b/sqlqueryprocessing/archive/2006/10/27/query-execution-bitmaps.aspx) (Introducción a los filtros de mapa de bits de ejecución de consultas). |



<a id="ExampleQueryParallelCciByCompatLevel" name="ExampleQueryParallelCciByCompatLevel"></a>


&nbsp;


## Ejemplo de cambio del plan de consulta por nivel de compatibilidad


Para una instrucción Transact-SQL **INSERT...SELECT**, esta sección muestra los cambios en su plan de consulta entre los niveles de compatibilidad 120 y 130.


#### Esquema de tablas de Azure


La siguiente tabla contiene al menos 300.000 filas. El plan de consulta avanzado no podría preocuparse por el paralelismo si hay muy pocos datos para que el paralelismo sea útil.


```
CREATE TABLE [dbo].[ccitestoriginal](
	[FinanceKey] [int] NOT NULL,
	[DateKey] [int] NOT NULL,
	[OrganizationKey] [int] NOT NULL,
	[DepartmentGroupKey] [int] NOT NULL,
	[ScenarioKey] [int] NOT NULL,
	[AccountKey] [int] NOT NULL,
	[Amount] [float] NOT NULL,
	[Date] [datetime] NULL
) ON [PRIMARY];
GO

CREATE CLUSTERED COLUMNSTORE INDEX [cci_ccitestoriginal]
	ON [dbo].[ccitestoriginal]
	WITH (DROP_EXISTING = OFF) ON [PRIMARY];
GO
```


#### Script para generar el plan de consulta


Estos pasos se aplican al siguiente script de Transact-SQL:


1. En **Ssms.exe**, conéctese a la base de datos
2. Pegue el script de Transact-SQL en una ventana de consulta **Ssms.exe**.
3. Edite el script para asegurarse de que **120** es el valor en la instrucción **ALTER DATABASE**.
4. Ejecute solo la parte del script que está *antes* de la instrucción **INSERT INTO**.
5. Active la opción de menú: **Consulta** > **Incluir plan de ejecución real (Ctrl + M)**.
6. Ejecute solo la instrucción **INSERT INTO**.<br/><br/>
7. Desactive la opción de menú: **Consulta** > **Incluir plan de ejecución real (Ctrl + M)**.
8. Por último, ejecute solo la parte del script que está *después* de la instrucción **INSERT INTO**.
9. Tenga en cuenta que el plan de consulta para **120** en la pestaña **Plan de ejecución**, que está cerca de la pestaña **Resultados**.<br/>-- -- -- -- -- --
10. Edite el script para asegurarse de que **130** es el valor en la instrucción **ALTER DATABASE**.
11. Vuelva a ejecutar el script como se describe en los pasos anteriores.
12. Tenga en cuenta que el plan de consulta para **130** es diferente e incluye el paralelismo.


&nbsp;


```
go
SET NOCOUNT ON;
go

ALTER DATABASE YourDatabaseName SET COMPATIBILITY_LEVEL =
	120
	--130
;
go

DROP TABLE ccitest_into_work;
go

SELECT *
	INTO ccitest_into_work  -- Create an empty copy of the table.
	FROM ccitestoriginal
	WHERE 1=2;
go

CREATE CLUSTERED COLUMNSTORE INDEX ccitest_into_work_cci
	ON ccitest_into_work;
go

SET NOCOUNT OFF;
go
	
	-- Run this INSERT statement alone!
	--
	-- First run all the preceding Transact-SQL statements.
	-- Then run this one INSERT statement alone.
	-- Then run all remaining Transact-SQL statements.

INSERT INTO ccitest_into_work WITH (TABLOCK)
	SELECT TOP 300000 *
		FROM ccitestoriginal;
go

SET NOCOUNT ON;
go

DROP TABLE ccitest_into_work;
go
```


#### Visualización de los dos planes



<br/>**120:** Este es el plan de consulta cuando el nivel de compatibilidad es **120**.


![Planear en el nivel 120][1-Plan-at-level-120]


<br/>**130:** Este es el plan de consulta cuando el nivel de compatibilidad es **130**.


El plan **130** incluye el *paralelismo*, del que carece el plan **120**.


La presentación de este plan es bastante amplia en **Ssms.exe**. Para obtener una mejor presentación aquí, la captura de pantalla se divide en dos partes. La segunda parte es la continuación de la primera parte.


![Planear en el nivel 130][2-Plan-at-level-130]


## Nivel de compatibilidad predeterminado en la base de datos


Al actualizar el servidor de Base de datos SQL de Azure, como de la versión V11 a la versión V12, el nivel de compatibilidad en cada base de datos no varía. Después de la actualización, puede aumentar el nivel de compatibilidad de una base de datos mediante la instrucción **ALTER DATABASE**.


Cualquier nueva base de datos recién creada tendrá el nivel de compatibilidad máximo que permite la versión de Base de datos SQL.



<!-- Image references. -->

[1-Plan-at-level-120]: ./media/sql-database-compatibility-level/sql-db-compat-level-query-plan-b-120.png

[2-Plan-at-level-130]: ./media/sql-database-compatibility-level/sql-db-compat-level-query-plan-b12-130.png

<!---HONumber=Oct15_HO3-->