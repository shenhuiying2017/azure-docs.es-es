---
title: Uso de SQL dinámico en Azure SQL Data Warehouse | Microsoft Docs
description: Sugerencias para usar SQL dinámico en Azure SQL Data Warehouse para desarrollar soluciones.
services: sql-data-warehouse
author: ronortloff
manager: craigg-msft
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.component: implement
ms.date: 04/17/2018
ms.author: rortloff
ms.reviewer: igorstan
ms.openlocfilehash: 604074e0a645918f7033360b79a1b7cad050c9e4
ms.sourcegitcommit: 1362e3d6961bdeaebed7fb342c7b0b34f6f6417a
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 04/18/2018
ms.locfileid: "31523173"
---
# <a name="dynamic-sql-in-sql-data-warehouse"></a>SQL dinámico en SQL Data Warehouse
Sugerencias para usar SQL dinámico en Azure SQL Data Warehouse para desarrollar soluciones.

## <a name="dynamic-sql-example"></a>Ejemplo de SQL dinámico

Al desarrollar código de aplicación para SQL Data Warehouse, puede que necesite usar SQL dinámico con el fin de proporcionar soluciones flexibles, genéricas y modulares. SQL Data Warehouse no admite por el momento tipos de datos blob. El hecho de no admitir tipos de datos blob podría limitar el tamaño de las cadenas, ya que los tipos de datos blob incluyen los tipos varchar(max) y nvarchar(max). Si ha utilizado estos tipos en el código de aplicación para crear cadenas grandes, es necesario dividir el código en fragmentos y utilizar en su lugar la instrucción EXEC.

Un ejemplo sencillo:

```sql
DECLARE @sql_fragment1 VARCHAR(8000)=' SELECT name '
,       @sql_fragment2 VARCHAR(8000)=' FROM sys.system_views '
,       @sql_fragment3 VARCHAR(8000)=' WHERE name like ''%table%''';

EXEC( @sql_fragment1 + @sql_fragment2 + @sql_fragment3);
```

Si la cadena es corta, puede usar [sp_executesql](/sql/relational-databases/system-stored-procedures/sp-executesql-transact-sql) como de costumbre.

> [!NOTE]
> Las instrucciones ejecutadas como SQL dinámico seguirán sujetas a todas las reglas de validación de TSQL.
> 
> 

## <a name="next-steps"></a>Pasos siguientes
Para obtener más sugerencias sobre desarrollo, consulte la [información general sobre desarrollo](sql-data-warehouse-overview-develop.md).

