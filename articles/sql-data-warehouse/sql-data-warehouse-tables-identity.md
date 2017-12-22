---
title: "Creación de claves suplentes mediante IDENTIDAD | Microsoft Docs"
description: Aprenda a usar IDENTIDAD para crear claves suplentes en las tablas.
services: sql-data-warehouse
documentationcenter: NA
author: barbkess
manager: jenniehubbard
editor: 
ms.assetid: faa1034d-314c-4f9d-af81-f5a9aedf33e4
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.date: 12/06/2017
ms.author: barbkess
ms.openlocfilehash: e10b58743fad5f7c2c4f00b51f06d4ec9bcb6768
ms.sourcegitcommit: b5c6197f997aa6858f420302d375896360dd7ceb
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/21/2017
---
# <a name="create-surrogate-keys-by-using-identity"></a>Creación de claves suplentes mediante IDENTITY
> [!div class="op_single_selector"]
> * [Información general][Overview]
> * [Tipos de datos][Data Types]
> * [Distribución][Distribute]
> * [Índice][Index]
> * [Partición][Partition]
> * [Estadísticas][Statistics]
> * [Temporal][Temporary]
> * [Identidad][Identity]
> 
> 

A muchos modeladores de datos les gusta crear claves suplentes en las tablas cuando diseñan modelos de almacenamiento de datos. Puede usar la propiedad IDENTITY para lograr este objetivo de manera sencilla y eficaz sin afectar al rendimiento de carga. 

## <a name="get-started-with-identity"></a>Introducción a IDENTITY
Puede definir una tabla que tenga la propiedad IDENTITY al crear la tabla mediante una sintaxis similar a la de la siguiente instrucción:

```sql
CREATE TABLE dbo.T1
(   C1 INT IDENTITY(1,1) NOT NULL
,   C2 INT NULL
)
WITH
(   DISTRIBUTION = HASH(C2)
,   CLUSTERED COLUMNSTORE INDEX
)
;
```

Luego puede usar `INSERT..SELECT` para rellenar la tabla.

## <a name="behavior"></a>Comportamiento
La propiedad IDENTITY está diseñada para escalar horizontalmente en todas las distribuciones de almacenamiento de datos sin afectar al rendimiento de carga. Por consiguiente, la implementación de IDENTITY está orientada a la consecución de estos objetivos. En esta sección se resaltan los matices de la implementación para ayudarle a comprenderlos de manera más completa.  

### <a name="allocation-of-values"></a>Asignación de valores
La propiedad IDENTITY no garantiza el orden en que se asignan las claves suplentes, lo que refleja el comportamiento de SQL Server y Azure SQL Database. Pero, en Azure SQL Data Warehouse, la ausencia de garantías es más marcada. 

El ejemplo siguiente sirve de muestra:

```sql
CREATE TABLE dbo.T1
(   C1 INT IDENTITY(1,1)    NOT NULL
,   C2 VARCHAR(30)              NULL
)
WITH
(   DISTRIBUTION = HASH(C2)
,   CLUSTERED COLUMNSTORE INDEX
)
;

INSERT INTO dbo.T1
VALUES (NULL);

INSERT INTO dbo.T1
VALUES (NULL);

SELECT *
FROM dbo.T1;

DBCC PDW_SHOWSPACEUSED('dbo.T1');
```

En el ejemplo anterior, dos filas quedaron en la distribución 1. La primera fila tiene el valor suplente de 1 en la columna `C1` y la segunda fila tiene el valor suplente de 61. Estos dos valores se generaron con la propiedad IDENTITY. Pero la asignación de los valores no es contigua. Este comportamiento es así por diseño.

### <a name="skewed-data"></a>Datos sesgados 
El intervalo de valores del tipo de datos se reparte uniformemente entre las distribuciones. Si una tabla distribuida adolece de datos sesgados, el intervalo de valores disponible para el tipo de datos se puede agotar antes de tiempo. Por ejemplo, si todos los datos terminan en una sola distribución, en la práctica, la tabla tiene acceso a únicamente la sexta parte de los valores del tipo de datos. Por este motivo, la propiedad IDENTITY se limita exclusivamente a los tipos de datos `INT` y `BIGINT`.

### <a name="selectinto"></a>SELECT..INTO
Cuando se selecciona una columna IDENTITY existente en una nueva tabla, la nueva columna hereda la propiedad IDENTITY, a no ser que se cumpla alguna de las siguientes condiciones:
- La instrucción SELECT contiene una combinación.
- Varias instrucciones SELECT se combinan mediante UNION.
- La columna IDENTITY aparece más de una vez en la lista de SELECT.
- La columna IDENTITY forma parte de una expresión.
    
Si ninguna de estas condiciones se cumple, la columna se crea como NOT NULL en lugar de heredar la propiedad IDENTITY.

### <a name="create-table-as-select"></a>CREATE TABLE AS SELECT
CREATE TABLE AS SELECT (CTAS) sigue el mismo comportamiento de SQL Server que se documenta para SELECT..INTO. Pero no se puede especificar una propiedad IDENTITY en la definición de columna de la parte `CREATE TABLE` de la instrucción. Tampoco se puede usar la función IDENTITY en la parte `SELECT` de la CTAS. Para rellenar una tabla, hay que usar `CREATE TABLE` para definir la tabla seguido de `INSERT..SELECT` para rellenarla.

## <a name="explicitly-insert-values-into-an-identity-column"></a>Inserción explícita de valores en una columna IDENTITY 
SQL Data Warehouse admite la sintaxis `SET IDENTITY_INSERT <your table> ON|OFF`. Puede usarse esta sintaxis para insertar valores explícitamente en la columna IDENTITY.

A muchos modeladores de datos les gusta usar valores negativos predefinidos para determinadas filas en las dimensiones. Un ejemplo es el valor -1 de la fila de "miembro desconocido". 

En el siguiente script se muestra cómo agregar explícitamente esta fila mediante SET IDENTITY_INSERT:

```sql
SET IDENTITY_INSERT dbo.T1 ON;

INSERT INTO dbo.T1
(   C1
,   C2
)
VALUES (-1,'UNKNOWN')
;

SET IDENTITY_INSERT dbo.T1 OFF;

SELECT  *
FROM    dbo.T1
;
```    

## <a name="load-data-into-a-table-with-identity"></a>Carga de datos en una tabla con IDENTITY

La presencia de la propiedad IDENTITY tiene algunas implicaciones en el código de carga de datos. En esta sección se resaltan algunos patrones básicos para cargar datos en tablas mediante IDENTITY. 

### <a name="load-data-with-polybase"></a>Carga de datos con PolyBase
Para cargar datos en una tabla y generar una clave suplente mediante IDENTITY, cree la tabla y use INSERT..SELECT o INSERT..VALUES para realizar la carga.

En el siguiente ejemplo se resalta el patrón básico:
 
```sql
--CREATE TABLE with IDENTITY
CREATE TABLE dbo.T1
(   C1 INT IDENTITY(1,1)
,   C2 VARCHAR(30)
)
WITH
(   DISTRIBUTION = HASH(C2)
,   CLUSTERED COLUMNSTORE INDEX
)
;

--Use INSERT..SELECT to populate the table from an external table
INSERT INTO dbo.T1
(C2)
SELECT  C2
FROM    ext.T1
;

SELECT  *
FROM    dbo.T1
;

DBCC PDW_SHOWSPACEUSED('dbo.T1');
```

> [!NOTE] 
> Actualmente no se puede usar `CREATE TABLE AS SELECT` al cargar datos en una tabla con una columna IDENTITY.
> 

Para más información sobre cómo cargar datos mediante la herramienta programa de copia masiva (BCP), vea los artículos siguientes:

- [Carga con PolyBase][]
- [Procedimientos recomendados para PolyBase][]

### <a name="load-data-with-bcp"></a>Carga de datos con BCP
BCP es una herramienta de línea de comandos que se puede usar para cargar datos en SQL Data Warehouse. Uno de sus parámetros (-E) controla el comportamiento de BCP al cargar datos en una tabla con una columna IDENTITY. 

Cuando se especifica -E, se conservan los valores contenidos en el archivo de entrada para la columna con IDENTITY. Si -E *no* se especifica, se omiten los valores de esta columna. Si no se incluye la columna IDENTITY, los datos se cargan con normalidad. Los valores se generan según la política de inicialización e incremento de la propiedad.

Para más información sobre cómo cargar datos mediante BCP, vea los artículos siguientes:

- [Carga con BCP][]
- [BCP en MSDN][]

## <a name="catalog-views"></a>Vistas de catálogo
SQL Data Warehouse admite la vista de catálogo `sys.identity_columns`. Esta vista puede usarse para identificar una columna que tiene la propiedad IDENTITY.

Para ayudarle a comprender mejor el esquema de la base de datos, en este ejemplo se muestra cómo integrar `sys.identity_columns` con otras vistas de catálogo del sistema:

```sql
SELECT  sm.name
,       tb.name
,       co.name
,       CASE WHEN ic.column_id IS NOT NULL
             THEN 1
        ELSE 0
        END AS is_identity 
FROM        sys.schemas AS sm
JOIN        sys.tables  AS tb           ON  sm.schema_id = tb.schema_id
JOIN        sys.columns AS co           ON  tb.object_id = co.object_id
LEFT JOIN   sys.identity_columns AS ic  ON  co.object_id = ic.object_id
                                        AND co.column_id = ic.column_id
WHERE   sm.name = 'dbo'
AND     tb.name = 'T1'
;
```

## <a name="limitations"></a>Limitaciones
La propiedad IDENTITY no se puede usar en los siguientes escenarios:
- Cuando el tipo de datos de la columna no es INT ni BIGINT
- Cuando la columna es también la clave de distribución
- Cuando la tabla es una tabla externa 

No se admiten las siguientes funciones relacionadas en SQL Data Warehouse:

- [IDENTITY()][]
- [@@IDENTITY][]
- [SCOPE_IDENTITY][]
- [IDENT_CURRENT][]
- [IDENT_INCR][]
- [IDENT_SEED][]
- [DBCC CHECK_IDENT()][]

## <a name="tasks"></a>Tareas

En esta sección se ofrecen algunos ejemplos de código que se pueden usar para realizar tareas comunes al trabajar con columnas IDENTITY.

> [!NOTE] 
> La columna C1 es la de IDENTITY en todas las tareas siguientes.
> 
 
### <a name="find-the-highest-allocated-value-for-a-table"></a>Búsqueda del valor más alto asignado en una tabla
Use la función `MAX()` para determinar el valor más alto asignado en una tabla distribuida:

```sql
SELECT  MAX(C1)
FROM    dbo.T1
``` 

### <a name="find-the-seed-and-increment-for-the-identity-property"></a>Búsqueda del valor de inicialización e incremento de la propiedad IDENTITY
Puede usar las vistas de catálogo para detectar los valores de configuración de inicialización e incremento de identidad en una tabla mediante la siguiente consulta: 

```sql
SELECT  sm.name
,       tb.name
,       co.name
,       ic.seed_value
,       ic.increment_value 
FROM        sys.schemas AS sm
JOIN        sys.tables  AS tb           ON  sm.schema_id = tb.schema_id
JOIN        sys.columns AS co           ON  tb.object_id = co.object_id
JOIN        sys.identity_columns AS ic  ON  co.object_id = ic.object_id
                                        AND co.column_id = ic.column_id
WHERE   sm.name = 'dbo'
AND     tb.name = 'T1'
;
```

## <a name="next-steps"></a>Pasos siguientes

* Para obtener más información sobre el desarrollo de tablas, vea [Introducción a las tablas][Overview], [Tipos de datos de tabla][Data Types], [Distribución de tablas][Distribute], [Indexación de tablas][Index], [Creación de particiones de tabla][Partition] y [Tablas temporales][Temporary]. 
* Para más información sobre los procedimientos recomendados, vea [Procedimientos recomendados para SQL Data Warehouse][SQL Data Warehouse Best Practices].  

<!--Image references-->

<!--Article references-->
[Overview]: ./sql-data-warehouse-tables-overview.md
[Data Types]: ./sql-data-warehouse-tables-data-types.md
[Distribute]: ./sql-data-warehouse-tables-distribute.md
[Index]: ./sql-data-warehouse-tables-index.md
[Partition]: ./sql-data-warehouse-tables-partition.md
[Statistics]: ./sql-data-warehouse-tables-statistics.md
[Temporary]: ./sql-data-warehouse-tables-temporary.md
[Identity]: ./sql-data-warehouse-tables-identity.md
[SQL Data Warehouse Best Practices]: ./sql-data-warehouse-best-practices.md

[Carga con BCP]: https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-load-with-bcp/
[Carga con PolyBase]: https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-load-from-azure-blob-storage-with-polybase/
[Procedimientos recomendados para PolyBase]: https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-load-polybase-guide/


<!--MSDN references-->
[Identity property]: https://msdn.microsoft.com/library/ms186775.aspx
[sys.identity_columns]: https://msdn.microsoft.com/library/ms187334.aspx
[IDENTITY()]: https://msdn.microsoft.com/library/ms189838.aspx
[@@IDENTITY]: https://msdn.microsoft.com/library/ms187342.aspx
[SCOPE_IDENTITY]: https://msdn.microsoft.com/library/ms190315.aspx
[IDENT_CURRENT]: https://msdn.microsoft.com/library/ms175098.aspx
[IDENT_INCR]: https://msdn.microsoft.com/library/ms189795.aspx
[IDENT_SEED]: https://msdn.microsoft.com/library/ms189834.aspx
[DBCC CHECK_IDENT()]: https://msdn.microsoft.com/library/ms176057.aspx

[BCP en MSDN]: https://msdn.microsoft.com/library/ms162802.aspx
  

<!--Other Web references-->  
