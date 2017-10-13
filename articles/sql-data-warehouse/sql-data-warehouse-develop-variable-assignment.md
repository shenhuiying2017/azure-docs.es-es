---
title: "Asignación de variables en SQL Data Warehouse | Microsoft Docs"
description: "Sugerencias para la asignación de variables de Transact-SQL en el Almacenamiento de datos SQL Azure para desarrollar soluciones."
services: sql-data-warehouse
documentationcenter: NA
author: jrowlandjones
manager: jhubbard
editor: 
ms.assetid: 81ddc7cf-a6ba-4585-91a3-b6ea50f49227
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.custom: t-sql
ms.date: 10/31/2016
ms.author: jrj;barbkess
ms.openlocfilehash: 045d5148cd3f12dac63c961ccf7c953d355ed725
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="assign-variables-in-sql-data-warehouse"></a>Asignación de variables en el Almacenamiento de datos SQL
Las variables en el Almacenamiento de datos SQL se establecen mediante las instrucciones `DECLARE` o `SET`.

A continuación se indican formas totalmente válidas para establecer el valor de una variable:

## <a name="setting-variables-with-declare"></a>Configuración de variables con DECLARE
Inicializar variables con DECLARE es una de las maneras más flexibles de establecer el valor de una variable en el Almacenamiento de datos SQL.

```sql
DECLARE @v  int = 0
;
```

También puede utilizar DECLARE para establecer más de una variable a la vez. No se puede utilizar `SELECT` o `UPDATE` para ello:

```sql
DECLARE @v  INT = (SELECT TOP 1 c_customer_sk FROM Customer where c_last_name = 'Smith')
,       @v1 INT = (SELECT TOP 1 c_customer_sk FROM Customer where c_last_name = 'Jones')
;
```

No se puede inicializar y utilizar una variable en la misma instrucción DECLARE. Para ilustrar esta cuestión, el ejemplo siguiente **no** está permitido como @p1, porque se inicializa y se utiliza en la misma instrucción DECLARE. Esto generará un error.

```sql
DECLARE @p1 int = 0
,       @p2 int = (SELECT COUNT (*) FROM sys.types where is_user_defined = @p1 )
;
```

## <a name="setting-values-with-set"></a>Configuración de valores con SET
SET es un método muy común para configurar una sola variable.

Todos los ejemplos siguientes son formas válidas de establecer una variable con SET:

```sql
SET     @v = (Select max(database_id) from sys.databases);
SET     @v = 1;
SET     @v = @v+1;
SET     @v +=1;
```

Solo puede establecer una variable al mismo tiempo con SET. Sin embargo, como hemos visto anteriormente, se admiten los operadores compuestos.

## <a name="limitations"></a>Limitaciones
Puede usar SELECT o UPDATE para la asignación de variables.

## <a name="next-steps"></a>Pasos siguientes
Para más sugerencias sobre desarrollo, consulte la [información general sobre desarrollo][development overview].

<!--Image references-->

<!--Article references-->
[development overview]: sql-data-warehouse-overview-develop.md

<!--MSDN references-->

<!--Other Web references-->
