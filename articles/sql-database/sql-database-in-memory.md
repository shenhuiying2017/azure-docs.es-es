<properties
	pageTitle="Introducción a SQL In-Memory | Microsoft Azure"
	description="Las tecnologías In-Memory de SQL mejoran notablemente el rendimiento de las cargas de trabajo de transacciones y de análisis. Aprenda a sacar partido de estas tecnologías."
	services="sql-database"
	documentationCenter=""
	authors="jodebrui"
	manager="jeffreyg"
	editor=""/>


<tags
	ms.service="sql-database"
	ms.workload="data-management"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="hero-article"
	ms.date="11/16/2015"
	ms.author="jodebrui"/>


# Introducción a In-Memory (vista previa) en Base de datos SQL

Las tablas normales de SQL solo se almacenan en una unidad de disco duro. Mediante las características de In-Memory se puede mejorar una tabla para que resida en la memoria activa mientras se está ejecutando el servidor.


Las tecnologías In-Memory mejoran notablemente el rendimiento de las cargas de trabajo de transacciones y de análisis.

- Con In-Memory OLTP (procesamiento de transacciones en línea) se puede conseguir ganancias hasta 30 veces superiores en el rendimiento de las transacciones, según las particularidades de la carga de trabajo.
 - Tablas con optimización para memoria
 - Procedimientos almacenados compilados de forma nativa.

- Con In-Memory Analytics se puede conseguir una mejora hasta 100 veces mayor del rendimiento de consultas.
 - Índices de almacén de columnas.


En el [análisis en tiempo real](http://msdn.microsoft.com/library/dn817827.aspx) puede combinar estas tecnologías para obtener:

- Conocimiento detallado empresarial en tiempo real en función de los datos operativos.
- OLTP muy rápido.


#### Disponibilidad


- *GA:* In-Memory Analytics se encuentra en Disponibilidad general (GA).
- *Vista previa*: In-Memory OLTP y Real-Time Operational Analytics se encuentran ambos en vista previa.
 - Están disponibles solo para estos dos Bases de datos SQL de Azure [Premium](sql-database-service-tiers.md).


#### OLTP y Analytics

Las dos secciones principales de este tema son:

- A: In-Memory [OLTP](#install_oltp_manuallink), que implica lectura y escritura.
- B. In-Memory [Analytics](#install_analytics_manuallink), que implica lectura.



<a id="install_oltp_manuallink" name="install_oltp_manuallink"></a>

&nbsp;

## R: Instalación del ejemplo de In-Memory OLTP.

Puede crear la base de datos de ejemplo AdventureWorksLT [V12] con unos cuantos clics en el [Portal de vista previa de Azure](http://portal.azure.com/). Después, los pasos de esta sección explican cómo puede mejorar la base de datos AdventureWorksLT con:

- Tablas de In-Memory.
- Un procedimiento almacenado compilado de forma nativa.


#### Pasos de instalación

1. En el [Portal de vista previa de Azure](http://portal.azure.com/), cree una base de datos Premium en un servidor de V12. Establezca el **origen** en la base de datos de ejemplo de AdventureWorksLT [V12].
 - Para obtener instrucciones detalladas, consulte [Creación de la primera Base de datos SQL de Azure](sql-database-get-started.md).

2. Conéctese a la base de datos con SQL Server Management Studio [(SSMS.exe)](http://msdn.microsoft.com/library/mt238290.aspx).

3. Copie el [script Transact-SQL de In-Memory OLTP](http://raw.githubusercontent.com/Azure/azure-sql-database-samples/master/T-SQL/In-Memory/sql_in-memory_oltp_sample.sql) en el Portapapeles.
 - El script T-SQL crea los objetos In-Memory necesarios en la base de datos de ejemplo AdventureWorksLT que se creó en el paso 1.

4. Pegue el script T-SQL en SSMS.exe y ejecútelo.
 - Son fundamentales las instrucciones CREATE TABLE de la cláusula `MEMORY_OPTIMIZED = ON`, como en:


```
CREATE TABLE [SalesLT].[SalesOrderHeader_inmem](
	[SalesOrderID] int IDENTITY NOT NULL PRIMARY KEY NONCLUSTERED ...,
	...
) WITH (MEMORY_OPTIMIZED = ON);
```


#### Error 40536


Si recibe el error 40536 cuando ejecuta el script T-SQL, ejecute el siguiente script de T-SQL para comprobar que la base de datos admite In-Memory:


```
SELECT DatabasePropertyEx(DB_Name(), 'IsXTPSupported');
```


Un resultado de **0** significa que In-Memory no se admite y un resultado de 1 significa que se admite.


#### Acerca de los elementos creados con optimización para memoria

**Tablas**: el ejemplo contiene las siguientes tablas con optimización para memoria:

- SalesLT.Product\_inmem
- SalesLT.SalesOrderHeader\_inmem
- SalesLT.SalesOrderDetail\_inmem
- Demo.DemoSalesOrderHeaderSeed
- Demo.DemoSalesOrderDetailSeed


Puede inspeccionar las tablas con optimización para memoria a través del **Explorador de objetos** en SSMS por:

- Haga clic con el botón derecho en **Tablas** > **Filtro** > **Configuración de filtro** > **Con optimización para memoria** es igual a 1.


O bien, puede consultar las vistas de catálogo como:


```
SELECT is_memory_optimized, name, type_desc, durability_desc
	FROM sys.tables
	WHERE is_memory_optimized = 1;
```


**Procedimiento almacenado compilado de forma nativa**: SalesLT.usp\_InsertSalesOrder\_inmem puede inspeccionarse a través de las consultas de las vistas de catálogo.


```
SELECT uses_native_compilation, OBJECT_NAME(object_id), definition
	FROM sys.sql_modules
	WHERE uses_native_compilation = 1;
```


&nbsp;

## Ejecución de la carga de trabajo de OLTP de ejemplo

La única diferencia entre los dos *procedimientos almacenados* siguientes es que el primer procedimiento usa las versiones de las tablas con optimización para memoria, mientras que el segundo procedimiento usa las tablas en disco habituales:

- SalesLT**.**usp\_InsertSalesOrder**\_inmem**
- SalesLT**.**usp\_InsertSalesOrder**\_ondisk**


En esta sección verá cómo usar la práctica utilidad **ostress.exe** para ejecutar los dos procedimientos almacenados a niveles de esfuerzo. Puede comparar el tiempo que tardan en completarse las dos ejecuciones de esfuerzo.


Cuando se ejecuta ostress.exe, le recomendamos pasar los valores de parámetros diseñados para ambos:

- Ejecute un gran número de conexiones simultáneas, mediante el uso quizás de -n100.
- Haga que cada conexión se repita en bucle cientos de veces, mediante el uso de quizás -r500.


Sin embargo, es posible que quiera comenzar con valores mucho más pequeños, como -n10 y -r50, para garantizar que todo está funcionando.


### Script para ostress.exe


En esta sección se muestra el script de T-SQL que se inserta en la línea de comandos de ostress.exe. El script usa elementos creados por el script de T-SQL que ha instalado antes.


El script siguiente inserta un pedido de ventas de ejemplo con cinco elementos de línea en las siguientes *tablas* con optimización para memoria:

- SalesLT.SalesOrderHeader\_inmem
- SalesLT.SalesOrderDetail\_inmem


```
DECLARE
	@i int = 0,
	@od SalesLT.SalesOrderDetailType_inmem,
	@SalesOrderID int,
	@DueDate datetime2 = sysdatetime(),
	@CustomerID int = rand() * 8000,
	@BillToAddressID int = rand() * 10000,
	@ShipToAddressID int = rand() * 10000;
	
INSERT INTO @od
	SELECT OrderQty, ProductID
	FROM Demo.DemoSalesOrderDetailSeed
	WHERE OrderID= cast((rand()*60) as int);
	
EXECUTE SalesLT.usp_InsertSalesOrder_inmem @SalesOrderID OUTPUT,
	@DueDate, @CustomerID, @BillToAddressID, @ShipToAddressID, @od;
```


Para hacer que la versión \_ondisk del T-SQL anterior sirva para ostress.exe, solo hay que reemplazar ambas apariciones de la subcadena *\_inmem* con *\_ondisk*. Estos reemplazos afectan a los nombres de tablas y procedimientos almacenados.


### Instalación de ostress y utilidades de RML


Lo ideal sería planear la ejecución de ostress.exe en una Máquina virtual de Azure. Se crearía una [máquina virtual de Azure](http://azure.microsoft.com/documentation/services/virtual-machines/) en la misma región geográfica de Azure donde reside la base de datos AdventureWorksLT. Pero puede ejecutar si lo desea ostress.exe en el equipo portátil.


En la máquina virtual, o en cualquier host que elija, instale las utilidades de Replay Markup Language (RML), entre las que se incluye ostress.exe.

- Consulte la explicación de ostress.exe en [Extensiones de AdventureWorks para demostrar In-Memory OLTP](http://msdn.microsoft.com/library/dn511655&#x28;v=sql.120&#x29;.aspx).
 - O bien consulte [Base de datos de ejemplo para In-Memory OLTP](http://msdn.microsoft.com/library/mt465764.aspx).
 - También, puede consultar el [blog para instalar ostress.exe](http://blogs.msdn.com/b/psssql/archive/2013/10/29/cumulative-update-2-to-the-rml-utilities-for-microsoft-sql-server-released.aspx)



<!--
dn511655.aspx is for SQL 2014, whereas mt465764.aspx is for SQL 2016.
-->



### Ejecución de la carga de trabajo de esfuerzo \_inmem en primer lugar


Puede usar una ventana *del símbolo del sistema de RML* para ejecutar la línea de comandos de ostress.exe.

Cuando se ejecuta desde el símbolo del sistema de RML, el siguiente comando de ostress.exe:

- Inserta pedidos de venta de un millón, con cinco elementos de línea cada uno, en tablas con optimización para memoria.
- Usa 100 conexiones simultáneas (-n100).


```
ostress.exe -n100 -r500 -S<servername>.database.windows.net
	 -U<login> -P<password> -d<database>
	 -q -Q"DECLARE @i int = 0, @od SalesLT.SalesOrderDetailType_inmem,
	 @SalesOrderID int, @DueDate datetime2 = sysdatetime(), @CustomerID int = rand() *
	 8000, @BillToAddressID int = rand() * 10000, @ShipToAddressID int = rand()*
	 10000;
	 INSERT INTO @od SELECT OrderQty, ProductID FROM
	 Demo.DemoSalesOrderDetailSeed WHERE OrderID= cast((rand()*60) as int);
	 WHILE (@i < 20) begin;
	 EXECUTE SalesLT.usp_InsertSalesOrder_inmem
	 @SalesOrderID OUTPUT, @DueDate, @CustomerID, @BillToAddressID,
	 @ShipToAddressID, @od; set @i += 1; end"
```


Para ejecutar la línea de comandos ostress.exe anterior:


1. Restablezca la base de datos mediante la ejecución del siguiente comando en SSMS, para eliminar todos los datos que se insertaron en todas las ejecuciones anteriores: ```
EXECUTE Demo.usp_DemoReset;
```

2. Copie el texto en el Portapapeles.

3. Reemplace todos los caracteres de fin de línea (\\r\\n) y todas las pestañas (\\t) por espacios en blanco.

4. Reemplace el <placeholders> para los parámetros -S - U -P -d por los valores reales correctos.


#### El resultado es una duración


Cuando finaliza ostress.exe, escribe la duración de la ejecución como la última línea de la salida en la ventana de símbolo del sistema de RML. Por ejemplo, una ejecución de prueba más corta duró aproximadamente 1,5 minutos:

`11/12/15 00:35:00.873 [0x000030A8] OSTRESS exiting normally, elapsed time: 00:01:31.867`


#### Restablecer, editar \_ondisk y volver a ejecutarlo


Una vez que tenga el resultado de la ejecución de \_inmem, realice los pasos siguientes para la ejecución de \_indisk:


1. Restablezca la base de datos mediante la ejecución del siguiente comando en SSMS, para eliminar todos los datos que se insertaron en la ejecución anterior: ```
EXECUTE Demo.usp_DemoReset;
```

2. Edite la línea de comandos de ostress.exe para reemplazar todos los *\_inmem* por *\_ondisk*.

3. Vuelva a ejecutar ostress.exe y capture el resultado de la duración.

4. De nuevo, vuelva a restablecer la base de datos para una limpieza responsable.
 - Una sola ejecución de prueba insertando un millón de pedidos de venta da como resultado 500 MB o más datos en las tablas.


#### Resultados de la comparación esperados

Las pruebas demostraron una mejora de alrededor de unas **9 veces** en el rendimiento de las tablas con optimización para memoria, en comparación con las tablas basadas en disco para esta carga de trabajo, con ostress ejecutándose en una máquina virtual en la misma región de Azure que la base de datos.

La mejora del rendimiento puede ser mayor cuando se agrega la conversión a procedimientos almacenados compilados de forma nativa.


## B. Instalación del ejemplo de In-Memory Analytics.


En esta sección, compara los resultados de optimización de infraestructura y de estadísticas cuando se usa un índice del almacén de columnas en lugar de un índice normal.


Los índices del almacén de columnas son lógicamente los mismos que los índices normales, pero físicamente son diferentes. Un índice de almacén de columnas organiza los datos de manera exótica para comprimir los datos en gran medida. Esto ofrece importantes mejoras del rendimiento.


Para realizar análisis en tiempo real en una carga de trabajo de OLTP, suele ser mejor usar un índice del almacén de columnas no agrupado. Para más información, consulte [Descripción de los índices de almacén de columnas](http://msdn.microsoft.com/library/gg492088.aspx).



### Preparación de la prueba de análisis del almacén de columnas


1. Use el Portal de Azure para crear una nueva base de datos AdventureWorksLT a partir del ejemplo.
 - Use ese nombre exacto.
 - Elija un nivel de servicio Premium.

2. Copie [sql\_in-memory\_analytics\_sample](http://raw.githubusercontent.com/Azure/azure-sql-database-samples/master/T-SQL/In-Memory/sql_in-memory_analytics_sample.sql) al Portapapeles.
 - El script T-SQL crea los objetos In-Memory necesarios en la base de datos de ejemplo AdventureWorksLT que se creó en el paso 1.
 - El script crea la tabla de dimensiones y dos tablas de hechos. Las tablas de hechos se rellenan con 3,5 millones de filas cada una.
 - El script podría tardar 15 minutos en completarse.

3. Pegue el script T-SQL en SSMS.exe y ejecútelo.
 - Es fundamental la palabra clave **COLUMNSTORE** en una instrucción **CREATE INDEX**, como en:<br/>`CREATE NONCLUSTERED COLUMNSTORE INDEX ...;`

4. Establezca AdventureWorksLT al nivel de compatibilidad 130:<br/>`ALTER DATABASE AdventureworksLT SET compatibility_level = 130;`


#### Tablas cruciales e índices de almacén de columnas


- dbo.FactResellerSalesXL\_CCI es una tabla con un índice **columnstore** agrupado, que tiene una compresión avanzada a nivel de *datos*.

- dbo. FactResellerSalesXL\_PageCompressed es una tabla con un índice agrupado equivalente normal, que se comprime solo a nivel de *página*.


#### Consultas cruciales para comparar el índice de almacén de columnas


[Aquí](http://raw.githubusercontent.com/Azure/azure-sql-database-samples/master/T-SQL/In-Memory/clustered_columnstore_sample_queries.sql) hay varios tipos de consultas de T-SQL que se pueden ejecutar para ver las mejoras de rendimiento. En el paso 2 del script de T-SQL, hay un par de consultas que son de gran interés. Las dos consultas difieren solo en una línea:


- `FROM FactResellerSalesXL_PageCompressed a`
- `FROM FactResellerSalesXL_CCI a`


Un índice de almacén de columnas agrupado se encuentra en la tabla FactResellerSalesXL**\_CCI**.

El siguiente fragmento de script de T-SQL imprime las estadísticas de IO y TIME para la consulta de cada tabla.


```
/*********************************************************************
Step 2 -- Overview
-- Page Compressed BTree table v/s Columnstore table performance differences
-- Enable actual Query Plan in order to see Plan differences when Executing
*/
-- Ensure Database is in 130 compatibility mode
ALTER DATABASE AdventureworksLT SET compatibility_level = 130
GO

-- Execute a typical query that joins the Fact Table with dimension tables
-- Note this query will run on the Page Compressed table, Note down the time
SET STATISTICS IO ON
SET STATISTICS TIME ON
GO

SELECT c.Year
	,e.ProductCategoryKey
	,FirstName + ' ' + LastName AS FullName
	,count(SalesOrderNumber) AS NumSales
	,sum(SalesAmount) AS TotalSalesAmt
	,Avg(SalesAmount) AS AvgSalesAmt
	,count(DISTINCT SalesOrderNumber) AS NumOrders
	,count(DISTINCT a.CustomerKey) AS CountCustomers
FROM FactResellerSalesXL_PageCompressed a
INNER JOIN DimProduct b ON b.ProductKey = a.ProductKey
INNER JOIN DimCustomer d ON d.CustomerKey = a.CustomerKey
Inner JOIN DimProductSubCategory e on e.ProductSubcategoryKey = b.ProductSubcategoryKey
INNER JOIN DimDate c ON c.DateKey = a.OrderDateKey
WHERE e.ProductCategoryKey =2
	AND c.FullDateAlternateKey BETWEEN '1/1/2014' AND '1/1/2015'
GROUP BY e.ProductCategoryKey,c.Year,d.CustomerKey,d.FirstName,d.LastName
GO
SET STATISTICS IO OFF
SET STATISTICS TIME OFF
GO


-- This is the same Prior query on a table with a Clustered Columnstore index CCI 
-- The comparison numbers are even more dramatic the larger the table is, this is a 11 million row table only.
SET STATISTICS IO ON
SET STATISTICS TIME ON
GO
SELECT c.Year
	,e.ProductCategoryKey
	,FirstName + ' ' + LastName AS FullName
	,count(SalesOrderNumber) AS NumSales
	,sum(SalesAmount) AS TotalSalesAmt
	,Avg(SalesAmount) AS AvgSalesAmt
	,count(DISTINCT SalesOrderNumber) AS NumOrders
	,count(DISTINCT a.CustomerKey) AS CountCustomers
FROM FactResellerSalesXL_CCI a
INNER JOIN DimProduct b ON b.ProductKey = a.ProductKey
INNER JOIN DimCustomer d ON d.CustomerKey = a.CustomerKey
Inner JOIN DimProductSubCategory e on e.ProductSubcategoryKey = b.ProductSubcategoryKey
INNER JOIN DimDate c ON c.DateKey = a.OrderDateKey
WHERE e.ProductCategoryKey =2
	AND c.FullDateAlternateKey BETWEEN '1/1/2014' AND '1/1/2015'
GROUP BY e.ProductCategoryKey,c.Year,d.CustomerKey,d.FirstName,d.LastName
GO

SET STATISTICS IO OFF
SET STATISTICS TIME OFF
GO
```


## Consideraciones de la vista previa para In-Memory


Las características de In-Memory de la Base de datos SQL de Azure se [volvieron activas para la vista previa el 28 de octubre de 2015](http://azure.microsoft.com/updates/public-preview-in-memory-oltp-and-real-time-operational-analytics-for-azure-sql-database/).


Durante la fase de vista previa antes de la disponibilidad general (GA), In-Memory OLTP solo se admite para:

- Bases de datos que se encuentran en un nivel de servicio *Premium*.

- Bases de datos que se crearon después de que las características de In-Memory se volvieran activas.
 - Una nueva base de datos restaurada a partir de una copia de seguridad realizada antes de la fecha de activación de In-Memory no puede admitir In-Memory.
 - Suponga que realiza una copia de seguridad de una base de datos que admita In-Memory. Después, restaura la copia de seguridad en una base de datos Premium anterior. La base de datos anterior ahora admite In-Memory.


En caso de duda, siempre puede ejecutar la instrucción SELECT de T-SQL siguiente para determinar si la base de datos admite In-Memory OLTP. Un resultado de **1** significa que la base de datos admite In-Memory:

```
SELECT DatabasePropertyEx(DB_NAME(), 'IsXTPSupported');
```


Si la consulta devuelve **1**, In-Memory OLTP se admite en esta base de datos, así como en cualquier copia de la base de datos y restauración de la misma que se hayan creado basadas en esta base de datos.


#### Objetos que solo se permiten en Premium


Si una base de datos contiene cualquiera de los siguientes tipos de objetos o tipos de In-Memory OLTP, no se admite la degradación del servicio de la base de datos de Premium a Básico o Estándar. Para degradar la base de datos, quite primero estos objetos:

- Tablas con optimización para memoria
- Tipos de tablas con optimización para memoria
- Módulos compilados de forma nativa


#### Otras relaciones


- No se admite el uso de In-Memory OLTP con bases de datos de grupos elásticos durante la vista previa, pero si podría admitirse en el futuro:

- No se admite el uso de In-Memory OLTP con Almacenamiento de datos SQL.
 - Se admite la característica de índice de almacén de columnas en In-Memory Analytics en Almacenamiento de datos SQL.

- El almacén de consulta no captura las consultas dentro de los módulos compilados de forma nativa durante la vista previa, pero lo hará en un futuro.

- No se admiten algunas características de Transact-SQL con In-Memory OLTP. Esto se aplica a Microsoft SQL Server y a Base de datos SQL de Azure. Para obtener información, vea:
 - [Compatibilidad de Transact-SQL con OLTP en memoria](http://msdn.microsoft.com/library/dn133180.aspx)
 - [Construcciones de Transact-SQL no admitidas en In-Memory OLTP](http://msdn.microsoft.com/library/dn246937.aspx).


## Pasos adicionales


- Consulte [Uso de In-Memory OLTP en una aplicación existente de SQL Azure.](sql-database-in-memory-oltp-migration.md)


## Recursos adicionales

#### Información más detallada

- [Obtenga información sobre In-Memory OLTP, que se aplica a Microsoft SQL Server y a Base de datos SQL de Azure](http://msdn.microsoft.com/library/dn133186.aspx).

- [Obtenga información sobre análisis operativos en tiempo real en MSDN](http://msdn.microsoft.com/library/dn817827.aspx)

- Notas del producto sobre [patrones de cargas de trabajo comunes y consideraciones de migración](http://msdn.microsoft.com/library/dn673538.aspx), que describe los patrones de carga de trabajo donde In-Memory OLTP normalmente proporciona importantes mejoras de rendimiento.

#### Diseño de aplicación

- [In-Memory OLTP (optimización In-Memory)](http://msdn.microsoft.com/library/dn133186.aspx)

- [Uso de In-Memory OLTP en una aplicación existente de SQL Azure.](sql-database-in-memory-oltp-migration.md)

#### Herramientas

- [Vista previa de SQL Server Data Tools (SSDT)](http://msdn.microsoft.com/library/mt204009.aspx), última versión mensual.

- [Descripción de las utilidades de Replay Markup Language (RML) para SQL Server](http://support.microsoft.com/es-ES/kb/944837)

- [Supervisión del almacenamiento In-Memory](sql-database-in-memory-oltp-monitoring.md) para In-Memory OLTP.

<!---HONumber=Nov15_HO4-->