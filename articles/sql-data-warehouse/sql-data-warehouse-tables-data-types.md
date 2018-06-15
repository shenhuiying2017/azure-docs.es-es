---
title: 'Definición de tipos de datos: Azure SQL Data Warehouse | Microsoft Docs'
description: Recomendaciones para definir los tipos de datos de tabla en Azure SQL Data Warehouse.
services: sql-data-warehouse
author: ronortloff
manager: craigg-msft
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.component: implement
ms.date: 04/17/2018
ms.author: rortloff
ms.reviewer: igorstan
ms.openlocfilehash: 4d8a222a6d4cfa4138fe833fb4e9cc895dbc5f65
ms.sourcegitcommit: 1362e3d6961bdeaebed7fb342c7b0b34f6f6417a
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 04/18/2018
ms.locfileid: "31523513"
---
# <a name="table-data-types-in-azure-sql-data-warehouse"></a>Tipos de datos de tabla en Azure SQL Data Warehouse
Recomendaciones para definir los tipos de datos de tabla en Azure SQL Data Warehouse. 

## <a name="what-are-the-data-types"></a>¿Cuáles son los tipos de datos?

SQL Data Warehouse admite los tipos de datos usados más frecuentemente. Para obtener una lista de los tipos de datos admitidos, consulte [tipos de datos](/sql/t-sql/statements/create-table-azure-sql-data-warehouse#DataTypes) en la instrucción CREATE TABLE. 

## <a name="minimize-row-length"></a>Minimizar la longitud de fila
Minimizar el tamaño de los tipos de datos acorta la longitud de fila, lo que conduce a un mejor rendimiento de las consultas. Utilice el tipo de datos más pequeño que sirva para los datos. 

- Evite definir las columnas de caracteres con una longitud predeterminada de gran tamaño. Por ejemplo, si el valor más largo es de 25 caracteres, defina la columna como VARCHAR(25). 
- Evite el uso de [NVARCHAR][NVARCHAR] cuando solo necesite VARCHAR.
- Utilice NVARCHAR(4000) o VARCHAR(8000) cuando sea posible en lugar de NVARCHAR(MAX) o VARCHAR(MAX).

Si usa tablas externas de Polybase para cargar las tablas, la longitud definida para la fila de la tabla no puede superar 1 MB. Cuando una fila con datos de longitud variable supera 1 MB, puede cargar la fila con BCP, pero no con PolyBase.

## <a name="identify-unsupported-data-types"></a>Identificar los tipos de datos no admitidos
Si va a migrar la base de datos desde otra base de datos SQL, puede encontrar tipos de datos no admitidos en SQL Data Warehouse. Utilice esta consulta para detectar tipos de datos no admitidos en el esquema de SQL existente.

```sql
SELECT  t.[name], c.[name], c.[system_type_id], c.[user_type_id], y.[is_user_defined], y.[name]
FROM sys.tables  t
JOIN sys.columns c on t.[object_id]    = c.[object_id]
JOIN sys.types   y on c.[user_type_id] = y.[user_type_id]
WHERE y.[name] IN ('geography','geometry','hierarchyid','image','text','ntext','sql_variant','xml')
 AND  y.[is_user_defined] = 1;
```


## <a name="unsupported-data-types"></a>Soluciones alternativas para los tipos de datos no admitidos

La lista siguiente muestra los tipos de datos que SQL Data Warehouse no admite y proporciona alternativas que puede usar en lugar de los tipos de datos no admitidos.

| Tipo de datos no admitido | Solución alternativa |
| --- | --- |
| [geometry](/sql/t-sql/spatial-geometry/spatial-types-geometry-transact-sql) |[varbinary](/sql/t-sql/data-types/binary-and-varbinary-transact-sql) |
| [geography](/sql/t-sql/spatial-geography/spatial-types-geography) |[varbinary](/sql/t-sql/data-types/binary-and-varbinary-transact-sql) |
| [hierarchyid](/sql/t-sql/data-types/hierarchyid-data-type-method-reference) |[nvarchar](/sql/t-sql/data-types/nchar-and-nvarchar-transact-sql)(4000) |
| [image](/sql/t-sql/data-types/ntext-text-and-image-transact-sql) |[varbinary](/sql/t-sql/data-types/binary-and-varbinary-transact-sql) |
| [text](/sql/t-sql/data-types/ntext-text-and-image-transact-sql) |[varchar](/sql/t-sql/data-types/char-and-varchar-transact-sql) |
| [ntext](/sql/t-sql/data-types/ntext-text-and-image-transact-sql) |[nvarchar](/sql/t-sql/data-types/nchar-and-nvarchar-transact-sql) |
| [sql_variant](/sql/t-sql/data-types/sql-variant-transact-sql) |Divida la columna en varias columnas fuertemente tipadas. |
| [table](/sql/t-sql/data-types/table-transact-sql) |Convierta en tablas temporales. |
| [timestamp](/sql/t-sql/data-types/date-and-time-types) |Cambie el código para usar [datetime2](/sql/t-sql/data-types/datetime2-transact-sql) y la función [CURRENT_TIMESTAMP](/sql/t-sql/functions/current-timestamp-transact-sql). Solo se admiten las constantes como valores predeterminados, por lo tanto, current_timestamp no se puede definir como una restricción predeterminada. Si tiene que migrar valores de la versión de fila de una columna de tipo timestamp, use [BINARY](/sql/t-sql/data-types/binary-and-varbinary-transact-sql)(8) o [VARBINARY](/sql/t-sql/data-types/binary-and-varbinary-transact-sql)(8) para valores de versión de fila NOT NULL o NULL. |
| [xml](/sql/t-sql/xml/xml-transact-sql) |[varchar](/sql/t-sql/data-types/char-and-varchar-transact-sql) |
| [tipo definido por el usuario](/sql/relational-databases/native-client/features/using-user-defined-types) |Volver a convertir el tipo de datos nativo cuando sea posible. |
| valores predeterminados | Los valores predeterminados solo admiten literales y constantes. |


## <a name="next-steps"></a>Pasos siguientes
Para obtener más información sobre el desarrollo de tablas, vea [Información general sobre tablas](sql-data-warehouse-tables-overview.md).
