<properties
   pageTitle="Especificaciones de capacidad de Almacenamiento de datos SQL | Microsoft Azure"
   description="Especificaciones de capacidad mínima y máxima para Almacenamiento de datos SQL."
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="barbkess"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="02/10/2016"
   ms.author="barbkess;jrj;sonyama"/>

# Especificaciones de capacidad de Almacenamiento de datos SQL

Almacenamiento de datos SQL tiene límites para el procesamiento de consultas y las vistas del sistema que son muy similares a SQL Server. Algunos límites son diferentes debido a la arquitectura subyacente paralela y escalable. Este es un resumen de los límites para Almacenamiento de datos SQL.

## Conexiones

| Categoría | Descripción | Máxima |
| :---------------- | :------------------------------------------- | :----------------- |
| Sesión | Sesiones abiertas simultáneas | 1024<br/><br/>Admitimos 1024 conexiones simultáneas que pueden enviar todas solicitudes simultáneas al almacén de datos. Tenga en cuenta que no hay límite en el número de consultas que se pueden ejecutar a la vez. Cuando se supera un límite, la solicitud entra en una cola interna donde espera a ser procesada.|
| Sesión | Memoria máxima para instrucciones preparadas | 20 MB |
| Inicios de sesión | Inicios de sesión por servidor | 500\.000 |


## Procesamiento de consultas

| Categoría | Descripción | Máxima |
| :---------------- | :------------------------------------------- | :----------------- |
| Consultar | Consultas simultáneas en tablas de usuario | 32<br/><br/>Las consultas adicionales se dirigirán a una cola interna donde quedarán a la espera de ser procesadas. Con independencia del número de consultas que se ejecutan al mismo tiempo, cada consulta está optimizada para hacer un uso completo de la arquitectura de procesamiento paralelo de forma masiva.|
| Consultar | Consultas en cola en tablas de usuario | 1000 |
| Consultar | Consultas simultáneas en vistas de sistema | 100 |
| Consultar | Consultas en cola en vistas de sistema | 1000 |
| Consultar | Parámetros máximos | 2098 |
| Lote | Tamaño máximo | 65 536*4096 |


## Lenguaje de definición de datos (DDL)

| Categoría | Descripción | Máxima |
| :---------------- | :------------------------------------------- | :----------------- |
| Tabla | Tablas por base de datos | 2 mil millones |
| Tabla | Columnas por tabla | 1024 columnas |
| Tabla | Bytes por columna | 8000 bytes |
| Tabla | Bytes por fila, tamaño definido | 8060 bytes<br/><br/>Se calcula el número de bytes por fila de la misma manera que para SQL Server con la compresión de página activada. No puede ser mayor que 8060 bytes. Para calcular el tamaño de fila, vea los cálculos de tamaño de fila de [Estimar el tamaño de un índice agrupado](https://msdn.microsoft.com/library/ms178085.aspx) en MSDN.<br/><br/>Para obtener una lista de los tamaños de tipo de datos de Almacenamiento de datos SQL, consulte [CREATE TABLE (Azure SQL Data Warehouse)](https://msdn.microsoft.com/library/mt203953.aspx) (CREATE TABLE (Almacenamiento de datos SQL de Azure)). |
| Tabla | Bytes por fila, tamaño de búfer interno para el movimiento de los datos | 32 768<br/><br/>Almacenamiento de datos SQL emplea un búfer interno para mover filas dentro del sistema distribuido de Almacenamiento de datos SQL. El servicio que mueve las filas se denomina Servicio de movimiento de datos (DMS) y almacena filas en un formato diferente al de SQL Server.<br/><br/>Si una fila no cabe en el búfer interno, obtendrá un error de compilación de consulta o un error de movimiento de datos internos. Para evitar este problema, consulte [Detalles sobre el tamaño de búfer de DMS](#details-about-the-dms-buffer-size).|
| Tabla | Particiones por tabla | 15 000<br/><br/>Para obtener un alto rendimiento, se recomienda reducir el número de particiones que necesita pero sin perder de vista sus necesidades empresariales. A medida que crece el número de particiones, la sobrecarga de operaciones de DDL y DML crece, lo que hace que el rendimiento sea más lento.|
| Tabla | Caracteres por valor de límite de partición| 4000 |
| Índice | Índices no agrupados por tabla | 999<br/><br/>Solo se aplica a tablas de almacén de filas.|
| Índice | Índices agrupados por tabla | 1<br><br/>Se aplica a tablas de almacén de filas y de almacén de columnas.|
| Índice | Filas de un grupo de filas de índice de almacén de columnas | 1024<br/><br/>Cada índice de almacén de columnas se implementa como varios índices de almacén de columnas. Tenga en cuenta que si inserta 1024 filas en un índice de almacén de columnas de Almacenamiento de datos SQL, las filas no irán al mismo grupo de filas.|
| Índice | Compilaciones simultáneas de índices de almacén de columnas agrupados. | 32<br/><br/>Se aplica cuando los índices de almacén de columnas agrupados se compilan en diferentes tablas. Solo se permite una compilación de índice de almacén de columnas agrupado por tabla. Las solicitudes adicionales esperan en una cola.|
| Índice | Tamaño de clave de índice | 900 bytes.<br/><br/>Se aplica solo a los índices de almacén de filas.<br/><br/>Si los datos existentes en las columnas no superan los 900 bytes cuando se crea el índice, pueden crearse índices en columnas varchar con un tamaño máximo de más de 900 bytes. Sin embargo, las posteriores acciones INSERT o UPDATE en las columnas que hacen que el número total supere los 900 bytes darán error.|
| Índice | Columnas de clave por índice | 16<br/><br/>Solo se aplica a los índices de almacén de filas. Los índices de almacén de columnas agrupados incluyen todas las columnas.|
| Estadísticas | Tamaño de los valores de columna combinados | 900 bytes |
| Estadísticas | Columnas por objeto de estadísticas | 32 |
| Estadísticas | Estadísticas creadas en columnas por tabla | 30 000 |
| Procedimientos almacenados | Niveles máximos de anidamiento | 8 |
| Ver | Columnas por vista | 1024 |


## Lenguaje de manipulación de datos (DML)

| Categoría | Descripción | Máxima |
| :---------------- | :------------------------------------------- | :----------------- |
| Resultados de SELECT | Columnas por fila | 4096<br/><br/>Nunca puede tener más de 4096 columnas por fila en el resultado de SELECT. No hay ninguna garantía de que pueda tener siempre 4096. Si el plan de consulta requiere una tabla temporal, se podría aplicar el máximo de 1024 columnas por tabla.|
| Resultados de SELECT | Bytes por fila | > 8060<br/><br/>El número de bytes por fila en el resultado de SELECT puede ser superior al máximo de 8060 bytes que se permite para filas de tabla. Si el plan de consulta de la instrucción SELECT requiere una tabla temporal, se podría aplicar el máximo de tabla de 8060 bytes.|
| Resultados de SELECT | Bytes por columna | > 8000<br/><br/>El número de bytes por columna en el resultado de SELECT puede ser superior al máximo de 8000 bytes que se permite para columnas de tabla. Si el plan de consulta de la instrucción SELECT requiere una tabla temporal, se podría aplicar el máximo de tabla de 8000 bytes.|
| SELECT | Subconsultas anidadas | 32<br/><br/>Nunca puede tener más de 32 subconsultas anidadas en una instrucción SELECT. No hay ninguna garantía de que siempre pueda tener 32. Por ejemplo, una instrucción JOIN puede introducir una subconsulta en el plan de consulta. El número de subconsultas también puede estar limitado por la memoria disponible.|
| SELECT | Columnas por JOIN | 1024 columnas<br/><br/>Nunca puede tener más de 1024 columnas en la instrucción JOIN. No hay ninguna garantía de que siempre pueda tener 1024. Si el plan JOIN requiere una tabla temporal con más columnas que el resultado de JOIN, se aplica el límite de 1024 a la tabla temporal. |
| SELECT | Bytes por columnas GROUP BY | 8060<br/><br/>Las columnas de la cláusula GROUP BY pueden tener como máximo 8060 bytes.|
| SELECT | Bytes por columnas ORDER BY | 8060 bytes<br/><br/>Las columnas de la cláusula ORDER BY pueden tener como máximo 8060 bytes.|
| INSERT | Bytes por columna, ancho fijo y variable | 8000 bytes Los intentos de insertar un número mayor de bytes que los definidos para la columna darán error.|
| INSERT | Bytes por fila, columnas de ancho variable | >8060 bytes Los datos que superan los 8060 bytes se colocan en el área de almacenamiento de desbordamiento.|
| UPDATE | Bytes por columna, ancho fijo y variable | 8000 bytes Los intentos de actualizar una columna a un valor que requiere un número mayor de bytes que los definidos para la columna darán error.|
| Identificadores y constantes por instrucción | Número de identificadores y constantes de referencia. | 65 535<br/><br/>Almacenamiento de datos SQL limita el número de identificadores y constantes que pueden incluirse en una única expresión de una consulta. Este límite es 65 535 GB. Si se supera este número se produce el error de SQL Server 8632. Para más información, consulte [Error interno: se ha alcanzado un límite de servicios de una expresión](http://support.microsoft.com/kb/913050/).|

## Vistas de sistema

| Vista de sistema | Número máximo de filas |
| :--------------------------------- | :------------|
| sys.dm\_pdw\_component\_health\_alerts | 10\.000 |
| sys.dm\_pdw\_dms\_cores | 100 |
| sys.dm\_pdw\_dms\_workers | Número total de trabajadores de DMS para las 1000 solicitudes de SQL más recientes. |
| sys.dm\_pdw\_dms\_worker\_pairs | 10\.000 |
| sys.dm\_pdw\_errors | 10\.000 |
| sys.dm\_pdw\_exec\_requests | 10\.000 |
| sys.dm\_pdw\_exec\_sessions | 10\.000 |
| sys.dm\_pdw\_request\_steps | Número total de pasos para las 1000 solicitudes SQL más recientes que se almacenan en sys.dm\_pdw\_exec\_requests. |
| sys.dm\_pdw\_os\_event\_logs | 10\.000 |
| sys.dm\_pdw\_sql\_requests | Las 1000 solicitudes SQL más recientes que se almacenan en sys.dm\_pdw\_exec\_requests. |


## Detalles sobre el tamaño del búfer de DMS

Almacenamiento de datos SQL utiliza un búfer interno para mover filas entre los nodos de ejecución del back-end. El servicio que mueve las filas se denomina Servicio de movimiento de datos (DMS) y utiliza un formato de almacenamiento que es diferente al de SQL Server.

Para mejorar el rendimiento de las consultas en paralelo, DMS rellena todos los datos de longitud variable hasta el tamaño máximo definido de base de datos SQL. Por ejemplo, el valor 'hello' para `nvarchar(2000) NOT NULL` utilizará realmente 4002 bytes en el búfer de DMS. Utiliza 2 bytes para cada uno de los 2000 caracteres, más 2 bytes para el terminador NULL.

> [AZURE.NOTE] Cuando DMS intente mover una fila que supera el tamaño del búfer de DMS de 32 768 bytes, se producirá un error interno. Si su tamaño de fila supera el tamaño del búfer de DMS, deberá revisar la definición de tabla para que la fila quepa en el búfer de DMS.

### Cómo determinar el tamaño de fila del búfer de DMS
En este ejemplo se describen los tamaños de datos de longitud variable definidos por DMS y luego se muestra cómo calcular el tamaño del búfer de DMS para una fila.
 
En el búfer de DMS, el tamaño de los datos de longitud variable es la suma de los siguientes valores:

- Tamaño definido por caracteres.
- Los tipos NULL usan 8 bytes para el indicador NULL.
- Los tipos ASCII utilizan 1 carácter para el terminador NULL.
- Los tipos Unicode utilizan 2 caracteres para el terminador NULL.
             
| Tipo de datos | Tamaño de búfer de DMS |
| :---------------------- | :-------------------------- |                
| char(1000) NULL | 1009 bytes = 1000 +8 + 1 |
| char(1000) NOT NULL | 1001 bytes = 1000 + 1 |
| nchar(1000 NULL | 2010 bytes = 2000 + 8 + 2 |
| varchar(1000) NULL | 1009 bytes = 1000 + 8 + 1 |
| varchar(1000) NOT NULL | 1009 bytes = 1000 + 1 |
| nvarchar(1000) NULL | 2010 bytes = 2000 + 8 + 2 |
| nvarchar(1000) NOT NULL | 2010 bytes = 2000 + 2 |
                
En el búfer de DMS, las columnas de ancho fijo utilizan el tamaño nativo de SQL Server. Si el tipo acepta valores NULL, DMS necesita 8 bytes adicionales. Para el tamaño de SQL Server, consulte el campo max\_length en sys.types.

Por ejemplo:

| Tipo de datos de ancho fijo | Tamaño de búfer de DMS |
| :-------------------- | :----------------- |
| int NULL | 12 bytes = 4 + 8 |
| int NOT NULL | 4 bytes = 4 + 0 | 
                
Con todos los valores juntos, el tamaño del búfer definido por DMS para la siguiente fila es 31 134 bytes, que cabe en el búfer de DMS.

| Columna | Tipo de datos | Tamaño de columna |
| :----- | :------------------ | :------------------------ |
| col1 | datetime2 (7) NULL | 20 bytes = 8 + 8 |
| col2 | float (4) NULL | 12 bytes = 4 + 8 |
| col3 | nchar (6) NULL | 22 bytes = 12 + 8 + 2 |
| col4 | char (7000) NULL | 7009 bytes = 7000 + 8 + 1 |
| col5 | nvarchar (4000) | 8002 bytes = 8000 + 2. |
| col6 | varchar (8000) NULL | 8009 bytes = 8000 + 8 + 1 |
| col7 | varbinary (8000) | 8000 bytes |
| col8 | binary (60) | 60 bytes |
                
              
### Ejemplo de exceder el tamaño del búfer de DMS

En este ejemplo se muestra cómo podría insertar correctamente una fila en Almacenamiento de datos SQL, pero entonces se produciría un error de desbordamiento de DMS cuando DMS necesitara mover la fila para una combinación incompatible de distribución. La lección que se debe extraer de esto que es mejor crear filas más pequeñas que quepan en el búfer de DMS.

En el ejemplo siguiente, vamos a crear la tabla T1. El tamaño máximo posible de la fila cuando todas las variables nvarchar tienen 4000 caracteres Unicode es superior a 40 000 bytes, lo que supera el tamaño máximo del búfer de DMS.

Puesto que el tamaño real definido de un valor nvarchar utiliza 26 bytes, la definición de fila es inferior a 8060 bytes y puede caber en una página de SQL Server. Por lo tanto, la instrucción CREATE TABLE se realiza correctamente, aunque DMS dará error cuando intente cargar esta fila en el búfer de DMS.

````
CREATE TABLE T1
  (
    c0 int NOT NULL,
    CustomerKey int NOT NULL,
    c1 nvarchar(4000),
    c2 nvarchar(4000),
    c3 nvarchar(4000),
    c4 nvarchar(4000),
    c5 nvarchar(4000)
  )
WITH ( DISTRIBUTION = HASH (c0) )
;
````
En este paso se muestra que podemos utilizar correctamente INSERT para insertar datos en la tabla. Esta instrucción carga los datos directamente en SQL Server sin usar DMS, así que no generará un error de desbordamiento del búfer de DMS. Esta fila también se carga correctamente en Integration Services.</para>

````
--The INSERT operation succeeds because the row is inserted directly into SQL Server without requiring DMS to buffer the row.
INSERT INTO T1
VALUES (
    25,
    429817,
    N'Each row must fit into the DMS buffer size of 32,768 bytes.',
    N'Each row must fit into the DMS buffer size of 32,768 bytes.',
    N'Each row must fit into the DMS buffer size of 32,768 bytes.',
    N'Each row must fit into the DMS buffer size of 32,768 bytes.',
    N'Each row must fit into the DMS buffer size of 32,768 bytes.'
  )
````

Para prepararse para demostrar el movimiento de datos, en este ejemplo se crea una segunda tabla con CustomerKey para la columna de distribución.

````
--This second table is distributed on CustomerKey. 
CREATE TABLE T2
  (
    c0 int NOT NULL,
    CustomerKey int NOT NULL,
    c1 nvarchar(4000),
    c2 nvarchar(4000),
    c3 nvarchar(4000),
    c4 nvarchar(4000),
    c5 nvarchar(4000)
  )
WITH ( DISTRIBUTION = HASH (CustomerKey) )
;

INSERT INTO T2
VALUES (
    32,
    429817,
    N'Each row must fit into the DMS buffer size of 32,768 bytes.',
    N'Each row must fit into the DMS buffer size of 32,768 bytes.',
    N'Each row must fit into the DMS buffer size of 32,768 bytes.',
    N'Each row must fit into the DMS buffer size of 32,768 bytes.',
    N'Each row must fit into the DMS buffer size of 32,768 bytes.'
  )
````
Puesto que ambas tablas no se distribuyen en CustomerKey, una combinación entre T1 y T2 en CustomerKey es incompatible con la distribución. DMS deberá cargar al menos una fila y copiarla en una distribución diferente.

````
SELECT * FROM T1 JOIN T2 ON T1.CustomerKey = T2.CustomerKey;
````

Como se esperaba, DMS no puede realizar la combinación porque la fila, cuando se rellenan todas las columnas nvarchar, es mayor que el tamaño del búfer de DMS de 32 768 bytes. Se produce el siguiente mensaje de error.

````
Msg 110802, Level 16, State 1, Line 126

An internal DMS error occurred that caused this operation to fail. Details: Exception: Microsoft.SqlServer.DataWarehouse.DataMovement.Workers.DmsSqlNativeException, Message: SqlNativeBufferReader.ReadBuffer, error in OdbcReadBuffer: SqlState: , NativeError: 0, 'COdbcReadConnection::ReadBuffer: not enough buffer space for one row | Error calling: pReadConn-&gt;ReadBuffer(pBuffer, bufferOffset, bufferLength, pBytesRead, pRowsRead) | state: FFFF, number: 81, active connections: 8', Connection String: Driver={SQL Server Native Client 11.0};APP=DmsNativeReader:P13521-CMP02\sqldwdms (4556) - ODBC;Trusted_Connection=yes;AutoTranslate=no;Server=P13521-SQLCMP02,1500
````

## Pasos siguientes
Para obtener más información de referencia, vea [Información general de referencia de Almacenamiento de datos SQL][].

<!--Image references-->

<!--Article references-->
[Información general de referencia de Almacenamiento de datos SQL]: sql-data-warehouse-overview-reference.md

<!--MSDN references-->

<!---HONumber=AcomDC_0211_2016-->