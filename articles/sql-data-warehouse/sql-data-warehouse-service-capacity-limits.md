<properties
   pageTitle="Límites de capacidad de Almacenamiento de datos SQL | Microsoft Azure"
   description="Valores máximos para las conexiones, bases de datos, tablas y consultas de Almacenamiento de datos SQL."
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="sonyam"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="07/31/2016"
   ms.author="sonyama;barbkess;jrj"/>

# Límites de capacidad de Almacenamiento de datos SQL

Las siguientes tablas contienen los valores máximos permitidos para los distintos componentes de Almacenamiento de datos SQL de Azure.


## Administración de cargas de trabajo

| Categoría | Descripción | Máxima |
| :------------------ | :------------------------------------------- | :----------------- |
| [Unidades de almacenamiento de datos (DWU)][]| Proceso, memoria y recursos de E/S | 6000 |
| Conexión de base de datos | Sesiones abiertas simultáneas | 1024<br/><br/>Se admite un máximo de 1024 conexiones activas, cada una de las cuales puede enviar solicitudes a una base de datos de Almacenamiento de datos SQL de forma simultánea. Tenga en cuenta que hay límites en el número de consultas que se pueden ejecutar a la vez. Cuando se supera el límite de simultaneidad, la solicitud entra en una cola interna donde espera para su proceso.|
| Conexión de base de datos | Memoria máxima para instrucciones preparadas | 20 MB |
| [Administración de cargas de trabajo][] | N.º máximo de consultas simultáneas | 32<br/><br/> De forma predeterminada, Almacenamiento de datos SQL ejecutará un máximo de 32 consultas simultáneas y consultas que permanecen en cola.<br/><br/>El nivel de simultaneidad se puede reducir cuando se asigna a los usuarios a una clase de recurso superior. Algunas consultas, como las consultas DMV, siempre se pueden ejecutar.|
| [Tempdb][] | Tamaño máximo de Tempdb | 399 GB por DW100. Por lo tanto, en DWU1000 el tamaño de Tempdb es de 3,99 TB |


## Objetos de base de datos

| Categoría | Descripción | Máxima |
| :---------------- | :------------------------------------------- | :----------------- |
| Base de datos | Tamaño máximo | 240 TB comprimidos en un disco<br/><br/>Este espacio es independiente del espacio de tempdb o de registro y, por tanto, está dedicado a tablas permanentes. La compresión del almacén de columnas agrupado se calcula mediante un factor de 5, lo que significa que el tamaño sin comprimir de la base de datos podría aumentar hasta unos 1 PB cuando todas las tablas se agrupan en el almacén de columnas (que es el tipo de tabla predeterminado).|
| Tabla | Tamaño máximo | 60 TB comprimidos en disco |
| Tabla | Tablas por base de datos | 2 mil millones |
| Tabla | Columnas por tabla | 1024 columnas |
| Tabla | Bytes por columna | 8000 bytes |
| Tabla | Bytes por fila, tamaño definido | 8060 bytes<br/><br/>El número de bytes por fila se calcula de la misma forma que para SQL Server con la compresión de página activada. Al igual que SQL Server, Almacenamiento de datos SQL admite el almacenamiento con desbordamiento de fila, lo que permite insertar columnas de longitud variable de forma no consecutiva. Solo se almacena una raíz de 24 bytes en el registro principal para las columnas de longitud variable insertadas de manera no consecutiva. Para más información, consulte el tema [Datos de desbordamiento de fila superiores a 8 KB][] disponible en los Libros en pantalla de SQL Server.<br/><br/>Para ver una lista de tamaños de los tipos de datos de Almacenamiento de datos SQL, consulte [CREATE TABLE (Azure SQL Data Warehouse)][] (CREATE TABLE [Almacenamiento de datos SQL de Azure]). |
| Tabla | Particiones por tabla | 15 000<br/><br/>Para obtener un alto rendimiento, se recomienda reducir el número de particiones que necesita pero sin perder de vista sus necesidades empresariales. A medida que crece el número de particiones, la sobrecarga de operaciones de lenguaje de definición de datos (DDL) y lenguaje de manipulación de datos (DML) crece y da lugar a un rendimiento más lento.|
| Tabla | Caracteres por valor de límite de partición| 4000 |
| Índice | Índices no agrupados por tabla | 999<br/><br/>Solo se aplica a tablas de almacén de filas.|
| Índice | Índices agrupados por tabla | 1<br><br/>Se aplica a tablas de almacén de filas y de almacén de columnas.|
| Índice | Filas de un grupo de filas de índice de almacén de columnas | 1024<br/><br/>Cada índice de almacén de columnas se implementa como varios índices de almacén de columnas. Tenga en cuenta que si inserta 1024 filas en un índice de almacén de columnas de Almacenamiento de datos SQL, las filas no irán al mismo grupo de filas.|
| Índice | Compilaciones simultáneas de índices de almacén de columnas agrupados. | 32<br/><br/>Se aplica cuando los índices de almacén de columnas agrupados se compilan en diferentes tablas. Solo se permite una compilación de índice de almacén de columnas agrupado por tabla. Las solicitudes adicionales esperan en una cola.|
| Índice | Tamaño de clave de índice | 900 bytes.<br/><br/>Se aplica solo a los índices de almacén de filas.<br/><br/>Si los datos existentes en las columnas no superan los 900 bytes cuando se crea el índice, pueden crearse índices en columnas con un tamaño máximo de más de 900 bytes. Sin embargo, las posteriores acciones INSERT o UPDATE en las columnas que hacen que el número total supere los 900 bytes darán error.|
| Índice | Columnas de clave por índice | 16<br/><br/>Solo se aplica a los índices de almacén de filas. Los índices de almacén de columnas agrupados incluyen todas las columnas.|
| Estadísticas | Tamaño de los valores de columna combinados | 900 bytes |
| Estadísticas | Columnas por objeto de estadísticas | 32 |
| Estadísticas | Estadísticas creadas en columnas por tabla | 30 000 |
| Procedimientos almacenados | Niveles máximos de anidamiento | 8 |
| Ver | Columnas por vista | 1024 |


## Cargas

| Categoría | Descripción | Máxima |
| :---------------- | :------------------------------------------- | :----------------- |
| Cargas de PolyBase | Bytes por fila | 32 768<br/><br/>Las cargas de PolyBase se limitan a la carga de filas más pequeñas que 32 KB y no pueden cargar en VARCHR(MAX), NVARCHAR(MAX) ni VARBINARY(MAX). Aunque este límite todavía existe, se quitarán próximamente.<br/><br/>


## Consultas

| Categoría | Descripción | Máxima |
| :---------------- | :------------------------------------------- | :----------------- |
| Consultar | Consultas en cola en tablas de usuario | 1000 |
| Consultar | Consultas simultáneas en vistas de sistema | 100 |
| Consultar | Consultas en cola en vistas de sistema | 1000 |
| Consultar | Parámetros máximos | 2098 |
| Lote | Tamaño máximo | 65 536*4096 |
| Resultados de SELECT | Columnas por fila | 4096<br/><br/>Nunca puede tener más de 4096 columnas por fila en el resultado de SELECT. No hay ninguna garantía de que pueda tener siempre 4096. Si el plan de consulta requiere una tabla temporal, se podría aplicar el máximo de 1024 columnas por tabla.|
| SELECT | Subconsultas anidadas | 32<br/><br/>Nunca puede tener más de 32 subconsultas anidadas en una instrucción SELECT. No hay ninguna garantía de que siempre pueda tener 32. Por ejemplo, una instrucción JOIN puede introducir una subconsulta en el plan de consulta. El número de subconsultas también puede estar limitado por la memoria disponible.|
| SELECT | Columnas por JOIN | 1024 columnas<br/><br/>Nunca puede tener más de 1024 columnas en la instrucción JOIN. No hay ninguna garantía de que siempre pueda tener 1024. Si el plan JOIN requiere una tabla temporal con más columnas que el resultado de JOIN, se aplica el límite de 1024 a la tabla temporal. |
| SELECT | Bytes por columnas GROUP BY | 8060<br/><br/>Las columnas de la cláusula GROUP BY pueden tener como máximo 8060 bytes.|
| SELECT | Bytes por columnas ORDER BY | 8060 bytes<br/><br/>Las columnas de la cláusula ORDER BY pueden tener como máximo 8060 bytes.|
| Identificadores y constantes por instrucción | Número de identificadores y constantes de referencia. | 65 535<br/><br/>Almacenamiento de datos SQL limita el número de identificadores y constantes que pueden incluirse en una única expresión de una consulta. Este límite es 65 535 GB. Si se supera este número se produce el error de SQL Server 8632. Para más información, consulte [Mensaje de error cuando ejecuta una consulta en SQL Server 2005: "error interno: se ha alcanzado un límite de servicios de expresión"][].|


## Metadatos

| Vista de sistema | Número máximo de filas |
| :--------------------------------- | :------------|
| sys.dm\_pdw\_component\_health\_alerts | 10\.000 |
| sys.dm\_pdw\_dms\_cores | 100 |
| sys.dm\_pdw\_dms\_workers | Número total de trabajadores de DMS para las 1000 solicitudes de SQL más recientes. |
| sys.dm\_pdw\_errors | 10\.000 |
| sys.dm\_pdw\_exec\_requests | 10\.000 |
| sys.dm\_pdw\_exec\_sessions | 10\.000 |
| sys.dm\_pdw\_request\_steps | Número total de pasos para las 1000 solicitudes SQL más recientes que se almacenan en sys.dm\_pdw\_exec\_requests. |
| sys.dm\_pdw\_os\_event\_logs | 10\.000 |
| sys.dm\_pdw\_sql\_requests | Las 1000 solicitudes SQL más recientes que se almacenan en sys.dm\_pdw\_exec\_requests. |


## Pasos siguientes
Para obtener más información de referencia, vea [Información general de referencia de Almacenamiento de datos SQL][].

<!--Image references-->

<!--Article references-->
[Unidades de almacenamiento de datos (DWU)]: ./sql-data-warehouse-overview-what-is.md#data-warehouse-units
[Información general de referencia de Almacenamiento de datos SQL]: ./sql-data-warehouse-overview-reference.md
[Administración de cargas de trabajo]: ./sql-data-warehouse-develop-concurrency.md
[Tempdb]: ./sql-data-warehouse-tables-temporary.md

<!--MSDN references-->
[Datos de desbordamiento de fila superiores a 8 KB]: https://msdn.microsoft.com/library/ms186981.aspx
[CREATE TABLE (Azure SQL Data Warehouse)]: https://msdn.microsoft.com/library/mt203953.aspx
[Mensaje de error cuando ejecuta una consulta en SQL Server 2005: "error interno: se ha alcanzado un límite de servicios de expresión"]: https://support.microsoft.com/kb/913050

<!---HONumber=AcomDC_0803_2016-->