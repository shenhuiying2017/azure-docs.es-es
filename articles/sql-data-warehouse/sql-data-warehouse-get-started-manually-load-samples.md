<properties
   pageTitle="Carga de datos de ejemplo en Almacenamiento de datos SQL | Microsoft Azure"
   description="Carga de datos de ejemplo en Almacenamiento de datos SQL"
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="lodipalm"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="10/21/2015"
   ms.author="lodipalm;barbkess"/>

#Carga de datos de ejemplo en Almacenamiento de datos SQL

Ahora que ha configurado una instancia del Almacenamiento de datos SQL puede cargar fácilmente datos de ejemplo en ella. Lo siguiente le ayudará a crear un conjunto de datos denominado AdventureWorksPDW2012 en la base de datos. Este conjunto de datos modela una estructura de almacenamiento de datos de ejemplo para una compañía ficticia llamada AdventureWorks. Tenga en cuenta que necesitará tener instalado BCP para los siguientes pasos. Si no tiene actualmente BCP instalado, instale las [Utilidades de la línea de comandos de Microsoft para SQL Server][].

1. Para empezar, haga clic para descargar nuestros [Scripts de datos de ejemplo][].

2. Cuando se haya descargado el archivo, extraiga el contenido del archivo AdventureWorksPDW2012.zip y abra la nueva carpeta AdventureWorksPDW2012.

3. Edite el archivo aw\_create.bat y establezca los siguientes valores en la parte superior del archivo:

   a. **Servidor**: el nombre completo del servidor en el que se encuentra el Almacenamiento de datos SQL

   b. **Usuario**: el usuario para el servidor anterior
   
   c. **Contraseña**: la contraseña para el inicio de sesión del servidor suministrado
   
   d. **Base de datos**: el nombre de la instancia del Almacenamiento de datos SQL en el que quiera cargar datos
   
   Asegúrese de que no haya ningún espacio en blanco entre el “=” y estos parámetros.
   

4. Ejecute aw\_create.bat desde el directorio en el que se encuentra. Esto creará el esquema y cargará datos en todas las tablas mediante BCP.


## Conexión a su ejemplo y consulta

Como se describe en la documentación de [conexión][], puede conectarse a esta base de datos mediante Visual Studio y SSDT. Ahora que ha cargado algunos datos de ejemplo en el Almacenamiento de datos SQL, puede ejecutar rápidamente algunas consultas para empezar.

Podemos ejecutar una instrucción select simple para obtener toda la información de los empleados:

	SELECT * FROM DimEmployee;

También podemos ejecutar una consulta más compleja mediante construcciones como GROUP BY para ver la cantidad total de todas las ventas de cada día:

	SELECT OrderDateKey, SUM(SalesAmount) AS TotalSales
	FROM FactInternetSales
	GROUP BY OrderDateKey
	ORDER BY OrderDateKey;

Incluso podemos usar la cláusula WHERE para filtrar órdenes desde antes de una fecha determinada:

	SELECT OrderDateKey, SUM(SalesAmount) AS TotalSales
	FROM FactInternetSales
	WHERE OrderDateKey > '20020801'
	GROUP BY OrderDateKey
	ORDER BY OrderDateKey;

De hecho, Almacenamiento de datos SQL admite casi todas las construcciones de T-SQL que SQL Server realiza, y se pueden encontrar algunas de las diferencias en nuestra documentación para [migrar código][].

## Pasos siguientes
Ahora que le hemos dado algún tiempo para familiarizarse con los datos de ejemplo, consulte como [desarrollar][], [cargar][] o [migrar][].

<!--Image references-->

<!--Article references-->
[migrar]: ./sql-data-warehouse-overview-migrate.md
[desarrollar]: ./sql-data-warehouse-overview-develop.md
[cargar]: ./sql-data-warehouse-overview-load.md
[conexión]: ./sql-data-warehouse-get-started-connect.md
[migrar código]: ./sql-data-warehouse-migrate-code.md

<!--MSDN references-->
[Utilidades de la línea de comandos de Microsoft para SQL Server]: http://www.microsoft.com/download/details.aspx?id=36433/

<!--Other Web references-->
[Scripts de datos de ejemplo]: https://migrhoststorage.blob.core.windows.net/sqldwsample/AdventureWorksPDW2012.zip/

<!---HONumber=Oct15_HO4-->