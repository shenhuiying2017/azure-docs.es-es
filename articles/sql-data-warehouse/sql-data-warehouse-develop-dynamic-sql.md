---
title: "SQL dinámico en SQL Data Warehouse | Microsoft Docs"
description: "Sugerencias para usar SQL dinámico en Almacenamiento de datos SQL Azure para desarrollar soluciones."
services: sql-data-warehouse
documentationcenter: NA
author: jrowlandjones
manager: jhubbard
editor: 
ms.assetid: a948c2c3-3cd1-4373-90a9-79e59414b778
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.custom: queries
ms.date: 10/31/2016
ms.author: jrj;barbkess
ms.openlocfilehash: 29228676373aee8dbc7b1b2a7d92ffc978333804
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="dynamic-sql-in-sql-data-warehouse"></a>SQL dinámico en Almacenamiento de datos SQL
Al desarrollar código de aplicación para Almacenamiento de datos SQL, puede que necesite usar SQL dinámico con el fin de proporcionar soluciones flexibles, genéricas y modulares. Almacenamiento de datos SQL no admite por el momento tipos de datos blob. Esto puede limitar el tamaño de las cadenas ya que los tipos de blob incluyen tipos varchar (max) y nvarchar (max). Si ha utilizado estos tipos en el código de aplicación al crear cadenas muy grandes, será necesario que divida el código en fragmentos y utilice en su lugar la instrucción EXEC.

Un ejemplo sencillo:

```sql
DECLARE @sql_fragment1 VARCHAR(8000)=' SELECT name '
,       @sql_fragment2 VARCHAR(8000)=' FROM sys.system_views '
,       @sql_fragment3 VARCHAR(8000)=' WHERE name like ''%table%''';

EXEC( @sql_fragment1 + @sql_fragment2 + @sql_fragment3);
```

Si la cadena es corta, puede usar [sp_executesql][sp_executesql] como de costumbre.

> [!NOTE]
> Las instrucciones ejecutadas como SQL dinámico seguirán sujetas a todas las reglas de validación de TSQL.
> 
> 

## <a name="next-steps"></a>Pasos siguientes
Para más sugerencias sobre desarrollo, consulte la [información general sobre desarrollo][development overview].

<!--Image references-->

<!--Article references-->
[development overview]: sql-data-warehouse-overview-develop.md

<!--MSDN references-->
[sp_executesql]: https://msdn.microsoft.com/library/ms188001.aspx

<!--Other Web references-->
