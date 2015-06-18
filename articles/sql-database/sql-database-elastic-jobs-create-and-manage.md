<properties 
	pageTitle="Creación y administración de trabajos de bases de datos elásticas" 
	description="Siga los pasos necesarios de los procesos de creación y administración de un trabajo de base de datos elástica." 
	services="sql-database" 
	documentationCenter="" 
	manager="jhubbard" 
	authors="sidneyh" 
	editor=""/>

<tags 
	ms.service="sql-database" 
	ms.workload="sql-database" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="04/29/2015" 
	ms.author="sidneyh"/>

# Creación y administración de trabajos de bases de datos elásticas

Los **grupos de bases de datos elásticas** proporcionan un modelo de predicción para la implementación de gran número de bases de datos. Puede establecer el número mínimo de unidades de rendimiento de datos (DTU) para cada base de datos a un coste establecido. La administración de objetos comunes en estas bases de datos puede realizarse más fácilmente mediante los **trabajos de bases de datos elásticas**. El servicio permite ejecutar secuencias de comandos de T-SQL en todas las bases de datos del grupo en una sola operación. Por ejemplo, puede establecer la directiva en cada base de datos para permitir que solo una persona pueda ver los datos confidenciales con las credenciales correctas.

## Requisitos previos

* Una suscripción de Azure. Para obtener una prueba gratuita, vea [Prueba gratuita de un mes](http://azure.microsoft.com/pricing/free-trial/).
* Grupo de bases de datos elásticas. Vea [Acerca de los grupos de bases de datos elásticas](sql-database-elastic-pool.md).
* Instalación de componentes del servicio de trabajo de bases de datos elásticas. Vea [Instalación del servicio de trabajo de bases de datos elásticas](sql-database-elastic-jobs-service-installation.md).

## Creación de trabajos

1. En la hoja del grupo de trabajos de bases de datos elásticas, haga clic en **Crear trabajo**.
2. Escriba el nombre y la contraseña del administrador de la base de datos (creado durante la instalación).
2. En la hoja **Crear trabajo**, escriba un nombre para el trabajo.
3. Péguelo o escríbalo en la secuencia de comandos T-SQL.
4. Haga clic en **Guardar** y, a continuación, haga clic en **Ejecutar**.

	![Asigne un nombre al trabajo, escríbalo o péguelo en el código y haga clic en Ejecutar.][1]

## Ejecución de trabajos idempotentes

Al ejecutar una secuencia de comandos en un conjunto de bases de datos, debe asegurarse de que la secuencia de comandos sea idempotente. Es decir, la secuencia de comandos debe poder ejecutarse varias veces, incluso si se produce un error antes en un estado incompleto. Por ejemplo, cuando se produce un error en una secuencia de comandos, el trabajo se reintentará de forma automática hasta que se procese correctamente (dentro de los límites, ya que la lógica de reintentos finalmente dejará de reintentar la operación). La manera de hacerlo es usar la una cláusula "IF EXISTS" y eliminar cualquier instancia encontrada antes de crear un nuevo objeto. A continuación se muestra un ejemplo:

	IF EXISTS (SELECT name FROM sys.indexes
            WHERE name = N'IX_ProductVendor_VendorID')
    DROP INDEX IX_ProductVendor_VendorID ON Purchasing.ProductVendor;
	GO
	CREATE INDEX IX_ProductVendor_VendorID 
    ON Purchasing.ProductVendor (VendorID);

De manera alternativa, puede usar una cláusula "IF NOT EXISTS" antes de crear una nueva instancia:

	IF NOT EXISTS (SELECT name FROM sys.tables WHERE name = 'TestTable') 
	BEGIN 
	 CREATE TABLE TestTable( 
	  TestTableId INT PRIMARY KEY IDENTITY, 
	  InsertionTime DATETIME2 
	 ); 
	END 
	GO 
	
	INSERT INTO TestTable(InsertionTime) VALUES (sysutcdatetime()); 
	GO 

Esta secuencia de comandos, a continuación, actualizará la tabla creada anteriormente.

	IF NOT EXISTS (SELECT columns.name FROM sys.columns INNER JOIN sys.tables on columns.object_id = tables.object_id WHERE tables.name = 'TestTable' AND columns.name = 'AdditionalInformation') 
	BEGIN 
	
	ALTER TABLE TestTable 
	
	ADD AdditionalInformation NVARCHAR(400); 
	END 
	GO 
	
	INSERT INTO TestTable(InsertionTime, AdditionalInformation) VALUES (sysutcdatetime(), 'test'); 
	GO 


## Comprobación del estado del trabajo

Tras iniciar un trabajo, puede comprobar su progreso.

1. Desde la página del grupo bases de datos elásticas, haga clic en **Administrar trabajos**.

	![Haga clic en "Administrar trabajos".][2]

2. Haga clic en el nombre (a) de un trabajo. El **ESTADO** puede ser "Completado" o "Error". Los detalles del trabajo aparecerán (b) con su fecha y hora de creación y ejecución. La lista (c) que se muestra debajo indica el progreso de la secuencia de comandos en cada base de datos del grupo y proporciona información de fecha y hora.

	![Comprobación de un trabajo finalizado][3]


## Comprobación de trabajos con errores

Si se produce un error en un trabajo, puede encontrar un registro de su ejecución. Haga clic en el nombre del trabajo con error para ver sus detalles.

![Comprobación de un trabajo con error][4]


[AZURE.INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]

<!--Image references-->
[1]: ./media/sql-database-elastic-jobs-create-and-manage/screen-1.png
[2]: ./media/sql-database-elastic-jobs-create-and-manage/click-manage-jobs.png
[3]: ./media/sql-database-elastic-jobs-create-and-manage/running-jobs.png
[4]: ./media/sql-database-elastic-jobs-create-and-manage/failed.png
[5]: ./media/sql-database-elastic-jobs-create-and-manage/provide-creds.png

<!---HONumber=58--> 