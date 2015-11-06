<properties
	pageTitle="Uso de In-Memory OLTP para mejorar el rendimiento transaccional de SQL Azure | Microsoft Azure"
	description="Adopción de In-Memory OLTP para mejorar el rendimiento transaccional en una Base de datos SQL ya existente."
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


# Uso de In-Memory (vista previa) para mejorar el rendimiento de la aplicación SQL de Azure

Siga estos pasos para optimizar el rendimiento transaccional de una Base de datos SQL de Azure Premium ya existente mediante [In-Memory](sql-database-in-memory.md).

Para realizar una comparación, elija una carga de trabajo similar a la carga de trabajo de producción con un número similar de conexiones simultáneas y de relación de lectura y escritura. Para minimizar la latencia de red, se recomienda ejecutar la carga de trabajo de prueba en la misma región de Azure que la base de datos.

## Paso 1: copiar los datos en una Base de datos Premium nueva
1.	Exporte la base de datos de producción a un bacpac, mediante:

	R: La funcionalidad de exportación en el [portal](https://portal.azure.com/), o

	B. La funcionalidad Exportar aplicación de capa de datos en SQL Server Management Studio

2.	Importe el archivo bacpac en una nueva Base de datos Premium en un servidor V12:

	R: En el portal: vaya al servidor y seleccione la opción Importar base de datos. Asegúrese de seleccionar un plan de tarifa Premium.

	B. En SQL Server Management Studio (SSMS): conecte con el servidor, haga clic con el botón derecho en el nodo Bases de datos y seleccione Importar aplicación de capa de datos.


## Paso 2: identificar objetos para migrar a In-Memory OLTP
SQL Server Management Studio (SSMS) incluye un informe de análisis de rendimiento de transacciones que se puede ejecutar en una base de datos con una carga de trabajo activa, para identificar las tablas y procedimientos almacenados que son candidatos para la migración a In-Memory OLTP. Consulte [Determinar si una tabla o un procedimiento almacenado se debe pasar a In-Memory OLTP](https://msdn.microsoft.com/library/dn205133.aspx) para obtener más detalles.

1.	Conéctese a la base de datos de producción con SSMS. Igualmente, si tiene una carga de trabajo que se ejecuta en la nueva base de datos de prueba, también se puede conectar a ella.
2.	Haga clic con el botón derecho en la base de datos y seleccione Informes -> Informes estándar -> Informe de análisis de rendimiento de transacciones. El informe le permitirá que identificar las tablas y procedimientos almacenados que pueden beneficiarse de In-Memory OLTP, en función del uso.


## Paso 3: migrar tablas
1.	Conéctese a la base de datos de producción de prueba con SSMS. Para evitar la necesidad de usar la opción WITH (SNAPSHOT) en las consultas, se recomienda establecer la opción de base de datos MEMORY\_OPTIMIZED\_ELEVATE\_TO\_SNAPSHOT.
2.	Una vez establecida la conexión con la nueva base de datos de prueba, ejecute:

   	    ALTER DATABASE CURRENT SET MEMORY_OPTIMIZED_ELEVATE_TO_SNAPSHOT=ON

3.	Migre la tabla basada en disco a optimizada en memoria por cualquiera de estos métodos:

	R: Asistente para la optimización de memoria SSMS: cuando esté conectado a la base de datos de prueba, haga clic con el botón derecho en la tabla y seleccione el asesor de optimización de memoria. Usar el asesor para determinar si la tabla tiene o no alguna característica que no sea compatible con la optimización de memoria. De lo contrario, el asistente puede realizar el esquema real y la migración de datos. Revise el [tema del asesor de optimización de memoria en MSDN](https://msdn.microsoft.com/library/dn284308.aspx) para obtener más detalles.

	B. Migración manual: conéctese a la base de datos de producción de prueba con SSMS.

Siga estos pasos para migrar una tabla:

1.	Incluya la tabla, para ello haga clic con el botón derecho en la tabla y seleccione Incluir tabla como -> CREAR en -> Nueva ventana de consulta.
2.	Cambie el índice agrupado (CLUSTERED) a no agrupado (NONCLUSTERED) y agregue la opción WITH (MEMORY\_OPTIMIZED = ON).
3.	Si la tabla usa alguna características no compatible, implemente soluciones alternativas. MSDN ofrece información sobre cómo tratar [características no admitidas comunes](https://msdn.microsoft.com/library/dn247639.aspx).
4.	Cambie el nombre de la tabla existente mediante sp\_rename.
5.	Cree la nueva tabla optimizada en memoria mediante la ejecución del script CREATE TABLE.
6.	Copie los datos mediante la ejecución de la siguiente instrucción: ``INSERT INTO <new_memory_optimized_table> SELECT * FROM <old_disk_based_table>

## Paso 4 (opcional): migrar los procedimientos almacenados

Conéctese a la nueva base de datos de prueba con [SQL Server Management Studio (SSMS)](https://msdn.microsoft.com/library/mt238290.aspx) vista previa de septiembre de 2015 o posterior.

Identifique todas las características que no se admiten en los procedimientos almacenados compilados de forma nativa mediante la ejecución del [Asistente para compilación nativa](https://msdn.microsoft.com/library/dn284308.aspx) en el procedimiento antiguo. En [MSDN](https://msdn.microsoft.com/library/dn296678.aspx) se documentan soluciones alternativas para las características no compatibles más comunes.

Dos cosas a tener en cuenta al migrar un procedimiento almacenado a nativo:

- Los módulos nativos requieren las siguientes opciones:

	- NATIVE\_COMPILATION
	- SCHEMABINDING



- Los módulos nativos usan [bloques ATOMIC](https://msdn.microsoft.com/library/dn452281.aspx) para administrar las transacciones; las instrucciones específicas BEGIN TRAN/COMMIT/ROLLBACK no son necesarias.

Un procedimiento almacenado compilado de forma nativa típico tiene el siguiente aspecto:


   	    CREATE PROCEDURE schemaname.procedurename
   		@param1 type1, …
   		WITH NATIVE_COMPILATION, SCHEMABINDING
   		AS
   		BEGIN ATOMIC WITH (TRANSACTION ISOLATION LEVEL = SNAPSHOT, LANGUAGE = N'your_language')

   		…

   		END



Tenga en cuenta que aunque SNAPSHOT es el nivel de aislamiento más usado con la tabla optimizada en memoria, REPEATABLE READ y SERIALIZABLE también se admiten.

##### Siga estos pasos para migrar un procedimiento almacenado:

1.	Incluya el procedimiento almacenado antiguo, haga clic con el botón derecho en el procedimiento y seleccione la opción para incluir el procedimiento como -> CREAR en -> Nueva ventana de consulta.
2.	Vuelva a escribir el encabezado de procedimiento mediante la plantilla anterior y quite todas las instrucciones BEGIN TRAN/COMMIT/ROLLBACK.
3.	Si el procedimiento almacenado usa alguna características no compatible, implemente soluciones alternativas. MSDN ofrece información sobre cómo tratar [características no admitidas comunes](https://msdn.microsoft.com/library/dn296678.aspx).
4.	O bien QUITE el procedimiento o cambie el nombre del procedimiento antiguo mediante sp\_rename.
5.	Cree el nuevo procedimiento almacenado compilado de forma nativa mediante la ejecución del script CREATE PROCEDURE.

## Paso 5: ejecutar la carga de trabajo
Ejecute la carga de trabajo de prueba en las tablas optimizadas en memoria y procedimientos almacenados compilados de forma nativa, y mida la mejora del rendimiento.

## Pasos siguientes

[Supervisión del almacenamiento In-Memory](https://azure.microsoft.com/documentation/articles/sql-database-in-memory-oltp-monitoring/).

[Supervisión de Base de datos SQL de Azure con vistas de administración dinámica](sql-database-monitoring-with-dmvs.md)

<!----HONumber=Nov15_HO1-->