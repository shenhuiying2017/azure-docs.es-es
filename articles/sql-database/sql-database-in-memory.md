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
	ms.date="10/28/2015"
	ms.author="jodebrui"/>


# Introducción a In-Memory (vista previa)


Las tecnologías In-Memory de SQL mejoran notablemente el rendimiento de las cargas de trabajo de transacciones y de análisis. Con In-Memory OLTP puede multiplicar hasta 30 veces el rendimiento en las transacciones, mientras con In-Memory Analytics puede mejorar hasta 100 veces el rendimiento de las consultas, dependiendo de la carga de trabajo.

In-Memory Analytics ya está disponible de forma general. In-Memory OLTP se encuentra en fase de vista previa para las Bases de datos SQL Premium de Azure.


## Introducción

Pruebe In-Memory OLTP para las cargas de trabajo transaccionales:


- [Enriquecimiento de una base de datos AdventureWorksLT con los ejemplos de In-Memory OLTP](#Enrich-an-AdventureWorksLT-database-with-In-Memory-OLTP-samples).
- [Uso de In-Memory OLTP en una aplicación existente de SQL Azure.](sql-database-in-memory-oltp-migration.md)
- [Supervisión del almacenamiento In-Memory](sql-database-in-memory-oltp-monitoring.md).


Pruebe In-Memory Analytics para las cargas de trabajo de las consultas:

- [Instalación de In-Memory Analytics](#Install-the-In-Memory-Analytics-sample).
- Obtener más información sobre [índices de almacén de columnas](https://msdn.microsoft.com/library/gg492088.aspx) en MSDN.


## Enriquecimiento de una base de datos AdventureWorksLT con los ejemplos de In-Memory OLTP

Puede crear la base de datos de ejemplo AdventureWorksLT [V12] con unos cuantos clics en el Portal de vista previa de Azure. Los pasos a continuación explican cómo puede mejorar la base de datos AdventureWorksLT con tablas y procedimientos almacenados compilados de forma nativa, para mostrar los objetos OLTP de In-Memory.


1. En el [Portal de vista previa de Azure](https://portal.azure.com/), cree una base de datos Premium en un servidor de V12. Establezca el origen en la base de datos de ejemplo de AdventureWorksLT [V12].
 - Para obtener instrucciones más detalladas para este paso, consulte [este artículo](sql-database-get-started.md).

2. Conéctese a la base de datos con [SQL Server Management Studio (SSMS.exe)](https://msdn.microsoft.com/library/mt238290.aspx) o con una utilidad similar.

3. Copie el [script In-Memory OLTP](https://raw.githubusercontent.com/Azure/azure-sql-database-samples/master/T-SQL/In-Memory/sql_in-memory_oltp_sample.sql) en el Portapapeles.
 - El script crea los objetos In-Memory necesarios en la base de datos de ejemplo AdventureWorksLT que se creó en el paso 1.

4. Pegue el script Transact-SQL en SSMS.exe y ejecútelo.



&nbsp;

El ejemplo contiene las siguientes tablas optimizadas en memoria:

- SalesLT.Product\_inmem
- SalesLT.SalesOrderHeader\_inmem
- SalesLT.SalesOrderDetail\_inmem
- Demo.DemoSalesOrderHeaderSeed
- Demo.DemoSalesOrderDetailSeed

Inspeccione las tablas optimizadas en memoria a través del explorador de objetos o de las consultas de la vista de catálogo.

Ejemplo:


```
		SELECT name, object_id, type, type_desc, is_memory_optimized, durability, durability_desc
		FROM sys.tables
	WHERE is_memory_optimized=1;
```


Del mismo modo, el procedimiento almacenado compilado de forma nativa SalesLT.usp\_InsertSalesOrder\_inmem, puede controlarse a través del explorador de objetos o de las consultas de las vistas de catálogo.

Ejemplo:


```
		SELECT object_name(object_id), object_id, definition, uses_native_compilation
		FROM sys.sql_modules
	WHERE uses_native_compilation=1;
```


## Ejecución de la carga de trabajo de ejemplo

Use los procedimientos almacenados SalesLT.usp\_InsertSalesOrder\_inmem y SalesLT.usp\_InsertSalesOrder\_ondisk para comparar el rendimiento de inserción para las tablas optimizadas en memoria con las tablas basadas en disco.

Se recomienda ejecutar la carga de trabajo con un número de conexiones de cliente simultáneas desde una aplicación que resida en la misma región de Azure que la base de datos de ejemplo.

### Inserción de pedido de ventas de ejemplo

El script siguiente inserta un pedido de ventas de ejemplo con cinco elementos de línea en las tablas optimizadas en memoria SalesLT.SalesOrderHeader\_inmem y SalesLT.SalesOrderDetail\_inmem:


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

EXECUTE SalesLT.usp_InsertSalesOrder_inmem @SalesOrderID OUTPUT, @DueDate,
			@CustomerID, @BillToAddressID, @ShipToAddressID, @od;
```


### Ejecución de la carga de trabajo de esfuerzo de ejemplo

Para ejecutar una carga de trabajo de esfuerzo de ejemplo, cree una [máquina virtual de Azure](https://azure.microsoft.com/documentation/services/virtual-machines/) en la misma región que la base de datos de ejemplo. Use la herramienta de línea de comandos ostress para ejecutar la carga de trabajo. Las instrucciones para [instalar y ejecutar ostress](https://msdn.microsoft.com/library/dn511655&#x28;v=sql.120&#x29;.aspx) pueden encontrarse en MSDN.

Al ejecutarse desde RML Cmd Prompt, el comando siguiente inserta un millón de pedidos de venta, con cinco elementos de línea en tablas optimizadas en memoria, usando 100 conexiones simultáneas:


```
		ostress.exe –n100 –r500 –S<servername>.database.windows.net -U<login> -P<password>
		 -d<database> -q -Q"DECLARE @i int = 0, @od SalesLT.SalesOrderDetailType_inmem,
		@SalesOrderID int, @DueDate datetime2 = sysdatetime(), @CustomerID int = rand() *
		8000, @BillToAddressID int = rand() * 10000, @ShipToAddressID int = rand()*  
		10000; INSERT INTO @od SELECT OrderQty, ProductID FROM
		Demo.DemoSalesOrderDetailSeed WHERE OrderID= cast((rand()*60) as int); while (@i <
		 20) begin; EXEC SalesLT.usp_InsertSalesOrder_inmem @SalesOrderID OUTPUT, @DueDate,
		 @CustomerID, @BillToAddressID, @ShipToAddressID, @od; set @i += 1 end"
```


Asegúrese de reemplazar <servername> con el nombre del servidor, <database> con el nombre de la base de datos y <login> y <password> con su información de inicio de sesión.

Para comparar el rendimiento de inserción de las tablas optimizadas en memoria con el de las tablas basadas en disco tradicionales, use el siguiente comando para insertar el mismo millón de pedidos de ventas en las tablas basadas en disco:


```
		ostress.exe –n100 –r500 –S<servername>.database.windows.net -U<login> -P<password>
		-d<database> -q -Q"DECLARE @i int = 0, @od SalesLT.SalesOrderDetailType_ondisk,
		@SalesOrderID int, @DueDate datetime2 = sysdatetime(), @CustomerID int = rand() *
		8000, @BillToAddressID int = rand() * 10000, @ShipToAddressID int = rand() *
		10000; INSERT INTO @od SELECT OrderQty, ProductID FROM
		Demo.DemoSalesOrderDetailSeed with (snapshot) WHERE OrderID= cast((rand()*60) as
		int); while (@i < 20) begin; EXEC SalesLT.usp_InsertSalesOrder_ondisk
		@SalesOrderID OUTPUT, @DueDate, @CustomerID, @BillToAddressID, @ShipToAddressID,
		@od; set @i += 1 end"
```


Las pruebas han demostrado una mejora de alrededor de unas 9 veces en el rendimiento de las tablas optimizadas en memoria, en comparación con las tablas basadas en disco para esta carga de trabajo, con ostress ejecutándose en una máquina virtual en la misma región de Azure que la base de datos.

Después de ejecutar cada prueba, asegúrese de restablecer el ejemplo, para evitar quedarse sin espacio de almacenamiento en memoria. Ejecute la siguiente instrucción de T-SQL en la base de datos. Una sola ejecución de prueba insertando un millón de pedidos de venta da como resultado > 500 MB de datos en tablas optimizadas en memoria.


```
EXECUTE Demo.usp_DemoReset;
```


## Instalación del ejemplo de In-Memory Analytics.

**Primero**, cree una nueva Base de datos SQL de Azure.

- Elija cualquier Premium Edition (Premium es necesario para el almacén de columnas)
- Asegúrese de que crea la base de datos del ejemplo.

- Para simplificar, asigne el nombre AdventureworksLT a la base de datos



**A continuación**, realice la conexión con Base de datos SQL de Azure a través de [SQL Server Management Studio (SSMS)](https://msdn.microsoft.com/library/mt238290.aspx) vista previa de septiembre de 2015 o posterior.


- Ejecute el script de instalación [sql\_in memory\_analytics\_sample](https://raw.githubusercontent.com/Azure/azure-sql-database-samples/master/T-SQL/In-Memory/sql_in-memory_analytics_sample.sql)


- Cree el esquema necesario para las tablas de hechos y dimensiones mediante la ejecución del script. El script creará 2 tablas de hechos con unos 3,5 millones de filas cada una.


- FactResellerSales\_CCI que tiene una tabla de almacén de columnas


- FactResellerSalesXL\_PageCompressed que es una tabla equivalente de árbol B que es una página comprimida. **Nota:** este script puede tardar hasta 15 minutos para ejecutarse y generar los datos.


**Ejecute** las consultas de demostración en el archivo [clustered\_columnstore\_sample\_queries.sql](https://raw.githubusercontent.com/Azure/azure-sql-database-samples/master/T-SQL/In-Memory/clustered_columnstore_sample_queries.sql). y explore la característica.

## Más información acerca de In-Memory OLTP

[In-Memory OLTP (optimización In-Memory)](https://msdn.microsoft.com/library/dn133186.aspx)

[Notas del producto sobre patrones de cargas de trabajo comunes y consideraciones de migración](https://msdn.microsoft.com/library/dn673538.aspx) describe los patrones de carga de trabajo donde In-Memory OLTP normalmente proporciona importantes mejoras de rendimiento.

## Consideraciones de la vista previa

Se admite In-Memory OLTP **solo** para las bases de datos Premium Edition

Se admite In-Memory OLTP solo para las bases de datos de nueva creación. No se admite en bases de datos creadas en función de una base de datos existente a través de la funcionalidad de copia o restauración. Sin embargo, una vez que tenga la nueva base de datos, puede copiar o restaurar esta base de datos manteniendo la funcionalidad completa de In-Memory OLTP.

Para comprobar si In-Memory OLTP se admite en una base de datos, ejecute la consulta siguiente:


```
SELECT DATABASEPROPERTYEX(DB_NAME(), 'IsXTPSupported');
```


Si la consulta devuelve **1**, In-Memory OLTP se admite en esta base de datos, así como en cualquier copia de la base de datos y restauración de la misma que se hayan creado basadas en esta base de datos.

Si una base de datos contiene cualquiera de los siguientes tipos de objetos o tipos, no se admite el cambio de nivel de servicio de la base de datos básico o estándar. Para degradar la base de datos, quite primero los objetos.

- Tablas optimizadas para la memoria
- Tipos de tablas optimizadas para la memoria
- No se admiten los módulos compilados de forma nativa usando In-Memory OLTP con bases de datos en grupos elásticos

No se admite el uso de In-Memory OLTP con Almacenamiento de datos SQL.

El almacén de consulta no captura las consultas dentro de los módulos compilados de forma nativa.

No se admiten algunas características de Transact-SQL con In-Memory OLTP. Para más detalles ver [Compatibilidad de Transact-SQL con In-Memory OLTP](https://msdn.microsoft.com/library/dn133180.aspx)

## Herramientas compatibles

[SQL Server Management Studio (SSMS)](https://msdn.microsoft.com/library/mt238290.aspx) vista previa de septiembre de 2015 o posterior.

[SQL Server Data Tools Preview (SSDT)](https://msdn.microsoft.com/library/mt204009.aspx) vista previa de septiembre de 2015 o posterior.

## Recursos adicionales

Más información acerca de In-Memory OLTP y Analytics: [In-Memory OLTP (optimización In-Memory)](https://msdn.microsoft.com/library/dn133186.aspx)

[Más información sobre los índices de almacenamiento de columnas MSDN](https://msdn.microsoft.com/library/gg492088.aspx)

[Notas del producto sobre patrones de cargas de trabajo comunes y consideraciones de migración](https://msdn.microsoft.com/library/dn673538.aspx) describe los patrones de carga de trabajo donde In-Memory OLTP normalmente proporciona importantes mejoras de rendimiento.

## Pasos siguientes

Consulte [Uso de In-Memory OLTP en una aplicación existente de SQL Azure.](sql-database-in-memory-oltp-migration.md)

[Supervisión del almacenamiento In-Memory](sql-database-in-memory-oltp-monitoring.md) para In-Memory OLTP.

<!---HONumber=Nov15_HO1-->