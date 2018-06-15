---
title: Uso de etiquetas para instrumentar consultas en SQL Data Warehouse | Microsoft Docs
description: Sugerencias para usar etiquetas para instrumentar las consultas en Azure SQL Data Warehouse para el desarrollo de soluciones.
services: sql-data-warehouse
author: ronortloff
manager: craigg-msft
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.component: implement
ms.date: 04/17/2018
ms.author: rortloff
ms.reviewer: igorstan
ms.openlocfilehash: 22737faa146d83f1f31489125dee4146c7d11ac1
ms.sourcegitcommit: 1362e3d6961bdeaebed7fb342c7b0b34f6f6417a
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 04/18/2018
ms.locfileid: "31524251"
---
# <a name="using-labels-to-instrument-queries-in-azure-sql-data-warehouse"></a>Uso de etiquetas para instrumentar consultas en Azure SQL Data Warehouse
Sugerencias para usar etiquetas para instrumentar las consultas en Azure SQL Data Warehouse para el desarrollo de soluciones.


## <a name="what-are-labels"></a>¿Qué son las etiquetas?
SQL Data Warehouse admite un concepto conocido como etiquetas de consulta. Antes de entrar en materia, vamos a ver un ejemplo:

```sql
SELECT *
FROM sys.tables
OPTION (LABEL = 'My Query Label')
;
```

Esta última línea etiqueta la cadena "Mi etiqueta de consulta" a la consulta. Esta etiqueta es especialmente útil dado que la etiqueta se puede consultar a través de las DMV. La consulta de etiquetas proporciona un mecanismo para buscar consultas problemáticas y ayudar a identificar el progreso a través de una ejecución de ETL.

Una buena convención de nomenclatura realmente ayuda en este caso. Por ejemplo, que la etiqueta empiece por algo como "PROYECTO, PROCEDIMIENTO, INSTRUCCIÓN o COMENTARIO" ayudaría a identificar de forma única la consulta entre todo el código de control del código fuente.

La consulta siguiente utiliza una vista de administración dinámica para buscar por etiqueta.

```sql
SELECT  *
FROM    sys.dm_pdw_exec_requests r
WHERE   r.[label] = 'My Query Label'
;
```

> [!NOTE]
> Es esencial que encierre entre corchetes o comillas dobles la etiqueta de la palabra al consultar. La etiqueta es una palabra reservada y provoca un error cuando no se delimita. 
> 
> 

## <a name="next-steps"></a>Pasos siguientes
Para obtener más sugerencias sobre desarrollo, consulte la [información general sobre desarrollo](sql-data-warehouse-overview-develop.md).


