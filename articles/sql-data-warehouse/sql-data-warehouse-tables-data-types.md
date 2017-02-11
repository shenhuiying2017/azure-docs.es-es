---
title: Tipos de datos de tablas en el SQL Data Warehouse | Microsoft Docs
description: "Introducción a los tipos de datos de Almacenamiento de datos SQL de Azure."
services: sql-data-warehouse
documentationcenter: NA
author: jrowlandjones
manager: barbkess
editor: 
ms.assetid: d4a1f0a3-ba9f-44b9-95f6-16a4f30746d6
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.date: 10/31/2016
ms.author: jrj;barbkess
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 616bb450217573ebd45060234313af418f525f89


---
# <a name="data-types-for-tables-in-sql-data-warehouse"></a>Tipos de datos de las tablas en el Almacenamiento de datos SQL
> [!div class="op_single_selector"]
> * [Información general][Información general]
> * [Tipo de datos][Tipo de datos]
> * [Distribuir][Distribución]
> * [Índice][Índice]
> * [Partition][Partition]
> * [Estadísticas][Estadísticas]
> * [Temporal][Temporal]
> 
> 

Almacenamiento de datos SQL admite los tipos de datos usados más frecuentemente.  A continuación, se muestra una lista de los tipos de datos admitidos por el Almacenamiento de datos SQL.  Para obtener más información sobre la compatibilidad de tipos de datos, consulte [create table][create table].

| **Tipos de datos admitidos** |  |  |
| --- | --- | --- |
| [bigint][bigint] |[decimal][decimal] |[smallint][smallint] |
| [binary][binary] |[float][float] |[smallmoney][smallmoney] |
| [bit][bit] |[int][int] |[sysname][sysname] |
| [char][char] |[money][money] |[time][time] |
| [date][date] |[nchar][nchar] |[tinyint][tinyint] |
| [datetime][datetime] |[nvarchar][nvarchar] |[uniqueidentifier][uniqueidentifier] |
| [datetime2][datetime2] |[real][real] |[varbinary][varbinary] |
| [datetimeoffset][datetimeoffset] |[smalldatetime][smalldatetime] |[varchar][varchar] |

## <a name="data-type-best-practices"></a>Procedimientos recomendados para los tipos de datos
 Al definir los tipos de columnas, usar el tipo de datos mínimo compatible con los datos mejorará el rendimiento de la consulta. Esto tiene especial importancia para las columnas CHAR y VARCHAR. Si el valor mayor máximo de una columna es 25 caracteres, defina la columna como VARCHAR(25). Evite definir todas las columnas de caracteres con una longitud predeterminada de gran tamaño. Defina las columnas como VARCHAR, en lugar de [NVARCHAR][NVARCHAR], cuando no se necesite nada más.  Utilice NVARCHAR(4000) o VARCHAR(8000) cuando sea posible en lugar de NVARCHAR(MAX) o VARCHAR(MAX).

## <a name="polybase-limitation"></a>Limitación de Polybase
Si usa PolyBase para cargar sus tablas, defínalas para que el tamaño máximo posible de fila, incluida la longitud total de columnas de longitud variable, no supere los 32 767 bytes.  Aunque puede definir una fila con datos de longitud variable que superen esta anchura, así como cargar filas con BCP, no podrá usar PolyBase para cargar estos datos.  El soporte técnico de PolyBase para filas anchas se agregará en breve.

## <a name="unsupported-data-types"></a>Tipos de datos no admitidos
Si va a migrar la base de datos desde otra plataforma SQL como Base de datos SQL de Azure, a medida que se realiza la migración, puede encontrar algunos tipos de datos que el Almacenamiento de datos SQL no admite.  A continuación se muestran algunos tipos de datos no admitidos así como algunas alternativas que puede utilizar en vez de ellos.

| Tipo de datos | Solución alternativa |
| --- | --- |
| [geometry][geometry] |[varbinary][varbinary] |
| [geography][geography] |[varbinary][varbinary] |
| [hierarchyid][hierarchyid] |[nvarchar][nvarchar](4000) |
| [image][ntext,text,image] |[varbinary][varbinary] |
| [text][ntext,text,image] |[varchar][varchar] |
| [ntext][ntext,text,image] |[nvarchar][nvarchar] |
| [sql_variant][sql_variant] |Divida la columna en varias columnas fuertemente tipadas. |
| [table][table] |Convierta en tablas temporales. |
| [timestamp][timestamp] |Vuelva a procesar el código para que use [datetime2][datetime2] y la función `CURRENT_TIMESTAMP`.  Solo se admiten las constantes como valores predeterminados, por lo tanto, current_timestamp no se puede definir como una restricción predeterminada. Si tiene que migrar valores rowversion de una columna con tipo timestamp, use [BINARY][BINARY](8) o [VARBINARY][BINARY](8) para valores de versión de fila NOT NULL o NULL. |
| [xml][xml] |[varchar][varchar] |
| [tipos definidos por el usuario][tipos definidos por el usuario] |vuelva a convertirlos a sus tipos nativos siempre que sea posible |
| valores predeterminados |los valores predeterminados solo admiten literales y constantes.  No se admiten funciones ni expresiones no deterministas, tales como `GETDATE()` o `CURRENT_TIMESTAMP`. |

La instrucción SQL siguiente se puede ejecutar en la Base de datos SQL actual para identificar las columnas no admitidas en el Almacenamiento de datos SQL de Azure:

```sql
SELECT  t.[name], c.[name], c.[system_type_id], c.[user_type_id], y.[is_user_defined], y.[name]
FROM sys.tables  t
JOIN sys.columns c on t.[object_id]    = c.[object_id]
JOIN sys.types   y on c.[user_type_id] = y.[user_type_id]
WHERE y.[name] IN ('geography','geometry','hierarchyid','image','text','ntext','sql_variant','timestamp','xml')
 AND  y.[is_user_defined] = 1;
```

## <a name="next-steps"></a>Pasos siguientes
Para obtener información, consulte los artículos sobre [información general de tablas][Información general], [distribución de una tabla][Distribución], [indexación de una tabla][Índice], [creación de particiones de una tabla][Partition], [mantenimiento de estadísticas de tablas][Estadísticas] y [tablas temporales][Temporal].  Para obtener más información sobre los procedimientos recomendados, consulte los [Procedimientos recomendados para Almacenamiento de datos SQL de Azure][Procedimientos recomendados para Almacenamiento de datos SQL de Azure].

<!--Image references-->

<!--Article references-->
[Información general]: ./sql-data-warehouse-tables-overview.md
[Tipo de datos]: ./sql-data-warehouse-tables-data-types.md
[Distribución]: ./sql-data-warehouse-tables-distribute.md
[Índice]: ./sql-data-warehouse-tables-index.md
[Partition]: ./sql-data-warehouse-tables-partition.md
[Estadísticas]: ./sql-data-warehouse-tables-statistics.md
[Temporal]: ./sql-data-warehouse-tables-temporary.md
[Procedimientos recomendados para Almacenamiento de datos SQL de Azure]: ./sql-data-warehouse-best-practices.md

<!--MSDN references-->

<!--Other Web references-->
[create table]: https://msdn.microsoft.com/library/mt203953.aspx
[bigint]: https://msdn.microsoft.com/library/ms187745.aspx
[binary]: https://msdn.microsoft.com/library/ms188362.aspx
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
[sql_variant]: https://msdn.microsoft.com/library/ms173829.aspx
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



<!--HONumber=Nov16_HO3-->


