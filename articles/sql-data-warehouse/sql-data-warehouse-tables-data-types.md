<properties
   pageTitle="Tipos de datos de tablas en el Almacenamiento de datos SQL | Microsoft Azure"
   description="Introducción a los tipos de datos de Almacenamiento de datos SQL de Azure."
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="jrowlandjones"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="06/29/2016"
   ms.author="jrj;barbkess;sonyama"/>

# Tipos de datos de las tablas en el Almacenamiento de datos SQL

> [AZURE.SELECTOR]
- [Información general][]
- [Tipo de datos][]
- [Distribución][]
- [Índice][]
- [Partition][]
- [Estadísticas][]
- [Temporal][]

Almacenamiento de datos SQL admite los tipos de datos usados más frecuentemente. A continuación, se muestra una lista de los tipos de datos admitidos por el Almacenamiento de datos SQL. Para más información sobre la compatibilidad de tipos de datos, consulte [create table][].

|**Tipos de datos admitidos**|||
|---|---|---|
[bigint][]|[decimal][]|[smallint][]|
[binary][]|[float][]|[smallmoney][]|
[bit][]|[int][]|[sysname][]|
[char][]|[money][]|[time][]|
[date][]|[nchar][]|[tinyint][]|
[datetime][]|[nvarchar][]|[uniqueidentifier][]|
[datetime2][]|[real][]|[varbinary][]|
[datetimeoffset][]|[smalldatetime][]|[varchar][]|


## Procedimientos recomendados para los tipos de datos

 Al definir los tipos de columnas, usar el tipo de datos mínimo compatible con los datos mejorará el rendimiento de la consulta. Esto tiene especial importancia para las columnas CHAR y VARCHAR. Si el valor mayor máximo de una columna es 25 caracteres, defina la columna como VARCHAR(25). Evite definir todas las columnas de caracteres con una longitud predeterminada de gran tamaño. Defina las columnas como VARCHAR en lugar de [NVARCHAR][] cuando no se necesite nada más. Utilice NVARCHAR(4000) o VARCHAR(8000) cuando sea posible en lugar de NVARCHAR(MAX) o VARCHAR(MAX).

## Limitación de Polybase

Si usa PolyBase para cargar sus tablas, defínalas para que el tamaño máximo posible de fila, incluida la longitud total de columnas de longitud variable, no supere los 32 767 bytes. Aunque puede definir una fila con datos de longitud variable que superen esta anchura, así como cargar filas con BCP, no podrá usar PolyBase para cargar estos datos. El soporte técnico de PolyBase para filas anchas se agregará en breve.

## Tipos de datos no admitidos

Si va a migrar la base de datos desde otra plataforma SQL como Base de datos SQL de Azure, a medida que se realiza la migración, puede encontrar algunos tipos de datos que el Almacenamiento de datos SQL no admite. A continuación se muestran algunos tipos de datos no admitidos así como algunas alternativas que puede utilizar en vez de ellos.

|Tipo de datos|Solución alternativa|
|---|---|
|[geometry][]|[varbinary][]|
|[geography][]|[varbinary][]|
|[hierarchyid][]|[nvarchar][](4000)|
|[imagen][ntext,text,image]|[varbinary][]|
|[text][ntext,text,image]|[varchar][]|
|[ntext][ntext,text,image]|[nvarchar][]|
|[sql\_variant][]|Divida la columna en varias columnas fuertemente tipadas.|
|[table][]|Convierta en tablas temporales.|
|[timestamp][]|Vuelva a procesar el código para que use [datetime2][] y la función `CURRENT_TIMESTAMP`. Solo se admiten las constantes como valores predeterminados, por lo tanto, current\_timestamp no se puede definir como una restricción predeterminada. Si tiene que migrar valores rowversion de una columna con tipo timestamp, use [BINARY(8)][] o [VARBINARY(8)][BINARY] para valores de versión de fila NOT NULL o NULL.|
|[xml][]|[varchar][]|
|[tipos definidos por el usuario][]|vuelva a convertirlos a sus tipos nativos siempre que sea posible|
|valores predeterminados|los valores predeterminados solo admiten literales y constantes. No se admiten funciones ni expresiones no deterministas, tales como `GETDATE()` o `CURRENT_TIMESTAMP`.|

La instrucción SQL siguiente se puede ejecutar en la Base de datos SQL actual para identificar las columnas no admitidas en el Almacenamiento de datos SQL de Azure:

```sql
SELECT  t.[name], c.[name], c.[system_type_id], c.[user_type_id], y.[is_user_defined], y.[name]
FROM sys.tables  t
JOIN sys.columns c on t.[object_id]    = c.[object_id]
JOIN sys.types   y on c.[user_type_id] = y.[user_type_id]
WHERE y.[name] IN ('geography','geometry','hierarchyid','image','text','ntext','sql_variant','timestamp','xml')
 AND  y.[is_user_defined] = 1;
```

## Pasos siguientes

Para más información, consulte los artículos sobre [información general de tablas][Overview], [distribución de una tabla][Distribute], [indexación de una tabla][Index], [creación de particiones de una tabla][Partition], [mantenimiento de estadísticas de tabla][Statistics] y [tablas temporales][Temporary]. Para más información sobre los procedimientos recomendados, consulte [Procedimientos recomendados para Almacenamiento de datos SQL de Azure][].

<!--Image references-->

<!--Article references-->
[Overview]: ./sql-data-warehouse-tables-overview.md
[Información general]: ./sql-data-warehouse-tables-overview.md
[Tipo de datos]: ./sql-data-warehouse-tables-data-types.md
[Distribute]: ./sql-data-warehouse-tables-distribute.md
[Distribución]: ./sql-data-warehouse-tables-distribute.md
[Index]: ./sql-data-warehouse-tables-index.md
[Índice]: ./sql-data-warehouse-tables-index.md
[Partition]: ./sql-data-warehouse-tables-partition.md
[Statistics]: ./sql-data-warehouse-tables-statistics.md
[Estadísticas]: ./sql-data-warehouse-tables-statistics.md
[Temporary]: ./sql-data-warehouse-tables-temporary.md
[Temporal]: ./sql-data-warehouse-tables-temporary.md
[Procedimientos recomendados para Almacenamiento de datos SQL de Azure]: ./sql-data-warehouse-best-practices.md

<!--MSDN references-->

<!--Other Web references-->
[create table]: https://msdn.microsoft.com/library/mt203953.aspx
[bigint]: https://msdn.microsoft.com/library/ms187745.aspx
[binary]: https://msdn.microsoft.com/library/ms188362.aspx
[BINARY(8)]: https://msdn.microsoft.com/library/ms188362.aspx
[bit]: https://msdn.microsoft.com/library/ms177603.aspx
[char]: https://msdn.microsoft.com/library/ms176089.aspx
[date]: https://msdn.microsoft.com/library/bb630352.aspx
[datetime]: https://msdn.microsoft.com/library/ms187819.aspx
[datetime2]: https://msdn.microsoft.com/library/bb677335.aspx
[datetimeoffset]: https://msdn.microsoft.com/library/bb630289.aspx
[decimal]: https://msdn.microsoft.com/library/ms187746.aspx
[float]: https://msdn.microsoft.com/library/ms173773.aspx
[geometry]: https://msdn.microsoft.com/library/cc280487.aspx
[geography]: https://msdn.microsoft.com/library/cc280766.aspx
[hierarchyid]: https://msdn.microsoft.com/library/bb677290.aspx
[int]: https://msdn.microsoft.com/library/ms187745.aspx
[money]: https://msdn.microsoft.com/library/ms179882.aspx
[nchar]: https://msdn.microsoft.com/library/ms186939.aspx
[nvarchar]: https://msdn.microsoft.com/library/ms186939.aspx
[ntext,text,image]: https://msdn.microsoft.com/library/ms187993.aspx
[real]: https://msdn.microsoft.com/library/ms173773.aspx
[smalldatetime]: https://msdn.microsoft.com/library/ms182418.aspx
[smallint]: https://msdn.microsoft.com/library/ms187745.aspx
[smallmoney]: https://msdn.microsoft.com/library/ms179882.aspx
[sql\_variant]: https://msdn.microsoft.com/library/ms173829.aspx
[sysname]: https://msdn.microsoft.com/library/ms186939.aspx
[table]: https://msdn.microsoft.com/library/ms175010.aspx
[time]: https://msdn.microsoft.com/library/bb677243.aspx
[timestamp]: https://msdn.microsoft.com/library/ms182776.aspx
[tinyint]: https://msdn.microsoft.com/library/ms187745.aspx
[uniqueidentifier]: https://msdn.microsoft.com/library/ms187942.aspx
[varbinary]: https://msdn.microsoft.com/library/ms188362.aspx
[varchar]: https://msdn.microsoft.com/library/ms186939.aspx
[xml]: https://msdn.microsoft.com/library/ms187339.aspx
[tipos definidos por el usuario]: https://msdn.microsoft.com/library/ms131694.aspx

<!---HONumber=AcomDC_0706_2016-->