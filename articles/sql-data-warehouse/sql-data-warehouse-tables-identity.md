---
title: 'Uso de IDENTITY para crear claves suplentes: Azure SQL Data Warehouse| Microsoft Docs'
description: Recomendaciones y ejemplos de uso de la propiedad IDENTITY para crear claves suplentes en tablas de Azure SQL Data Warehouse.
services: sql-data-warehouse
author: ronortloff
manager: craigg-msft
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.component: implement
ms.date: 04/17/2018
ms.author: rortloff
ms.reviewer: igorstan
ms.openlocfilehash: ab028705f5af7c37017d2e697240b7d3436f5f71
ms.sourcegitcommit: 1362e3d6961bdeaebed7fb342c7b0b34f6f6417a
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 04/18/2018
ms.locfileid: "31526991"
---
# <a name="using-identity-to-create-surrogate-keys-in-azure-sql-data-warehouse"></a>Uso de IDENTITY para crear claves suplentes en Azure SQL Data Warehous
Recomendaciones y ejemplos de uso de la propiedad IDENTITY para crear claves suplentes en tablas de Azure SQL Data Warehouse.

## <a name="what-is-a-surrogate-key"></a>¿Qué es una clave suplente?
Una clave suplente en una tabla es una columna con un identificador único para cada fila. La clave no se genera desde los datos de la tabla. A los modeladores de datos les gusta crear claves suplentes en las tablas cuando diseñan modelos de almacenamiento de datos. Puede usar la propiedad IDENTITY para lograr este objetivo de manera sencilla y eficaz sin afectar al rendimiento de carga.  

## <a name="creating-a-table-with-an-identity-column"></a>Creación de una tabla con una columna IDENTITY
La propiedad IDENTITY está diseñada para escalar horizontalmente en todas las distribuciones de almacenamiento de datos sin afectar al rendimiento de carga. Por consiguiente, la implementación de IDENTITY está orientada a la consecución de estos objetivos. 

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

En el resto de esta sección se resaltan los matices de la implementación para ayudarle a comprenderlos de manera más completa.  

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

## <a name="explicitly-inserting-values-into-an-identity-column"></a>Inserción explícita de valores en una columna IDENTITY 
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

## <a name="loading-data"></a>Carga de datos

La presencia de la propiedad IDENTITY tiene algunas implicaciones en el código de carga de datos. En esta sección se resaltan algunos patrones básicos para cargar datos en tablas mediante IDENTITY. 

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
> Actualmente no se puede usar CREATE TABLE AS SELEC al cargar datos en una tabla con una columna IDENTITY.
> 

Para obtener más información sobre cómo cargar datos, vea [Diseñar un proceso de extracción, carga y transformación (ELT) para Azure SQL Data Warehouse](design-elt-data-loading.md) y [Procedimientos recomendados para la carga de datos](guidance-for-loading-data.md).


## <a name="system-views"></a>Vistas de sistema
Puede usar la vista del catálogo [sys.identity_columns](/sql/relational-databases/system-catalog-views/sys-identity-columns-transact-sql) para identificar una columna que tiene la propiedad IDENTITY.

Para ayudarle a comprender mejor el esquema de la base de datos, en este ejemplo se muestra cómo integrar sys.identity_column con otras vistas de catálogo del sistema:

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
No se puede usar la propiedad IDENTITY:
- Cuando el tipo de datos de la columna no es INT ni BIGINT
- Cuando la columna es también la clave de distribución
- Cuando la tabla es una tabla externa 

No se admiten las siguientes funciones relacionadas en SQL Data Warehouse:

- [IDENTITY()](/sql/t-sql/functions/identity-function-transact-sql)
- [@@IDENTITY](/sql/t-sql/functions/identity-transact-sql)
- [SCOPE_IDENTITY](/sql/t-sql/functions/scope-identity-transact-sql)
- [IDENT_CURRENT](/sql/t-sql/functions/ident-current-transact-sql)
- [IDENT_INCR](/sql/t-sql/functions/ident-incr-transact-sql)
- [IDENT_SEED](/sql/t-sql/functions/ident-seed-transact-sql)
- [DBCC CHECK_IDENT()](/sql/t-sql/database-console-commands/dbcc-checkident-transact-sql)

## <a name="common-tasks"></a>Tareas comunes

En esta sección se ofrecen algunos ejemplos de código que se pueden usar para realizar tareas comunes al trabajar con columnas IDENTITY. 

La columna C1 es la de IDENTITY en todas las tareas siguientes.
 
 
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

* Para obtener más información sobre el desarrollo de tablas, consulte la [Información general sobre tablas] [Información general].  

