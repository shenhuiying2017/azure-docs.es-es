---
title: Uso de etiquetas para instrumentar consultas en SQL Data Warehouse | Microsoft Docs
description: Sugerencias para usar etiquetas para instrumentar las consultas en Almacenamiento de datos SQL de Azure para el desarrollo de soluciones.
services: sql-data-warehouse
documentationcenter: NA
author: jrowlandjones
manager: jhubbard
editor: 
ms.assetid: 44988de8-04c1-4fed-92be-e1935661a4e8
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.custom: queries
ms.date: 10/31/2016
ms.author: jrj;barbkess
ms.openlocfilehash: 9e75bbe528a427724a623305fbd45e2277e9d0af
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 07/11/2017
---
# <a name="use-labels-to-instrument-queries-in-sql-data-warehouse"></a>Uso de etiquetas para instrumentar consultas en Almacenamiento de datos SQL
Almacenamiento de datos SQL admite un concepto conocido como etiquetas de consulta. Antes de entrar en materia, vamos a ver un ejemplo:

```sql
SELECT *
FROM sys.tables
OPTION (LABEL = 'My Query Label')
;
```

Esta última línea etiqueta la cadena 'Mi etiqueta de consulta' a la consulta. Esto es especialmente útil dado que la etiqueta se puede consultar a través de las DMV. De esta manera contamos con un mecanismo para realizar el seguimiento de los problemas y también para ayudar a identificar el progreso a través de una ejecución de ETL.

Una buena convención de nomenclatura realmente ayuda en este caso. Por ejemplo, algo como ' PROYECTO : PROCEDIMIENTO : INSTRUCCIÓN : COMENTARIO' ayudaría a identificar de forma única la consulta entre todo el código de control del código fuente.

Para buscar por etiqueta, puede usar la siguiente consulta que emplea las vistas de administración dinámica:

```sql
SELECT  *
FROM    sys.dm_pdw_exec_requests r
WHERE   r.[label] = 'My Query Label'
;
```

> [!NOTE]
> Es esencial que encierre entre corchetes o comillas dobles la etiqueta de la palabra al consultar. Etiqueta es una palabra reservada y producirá un error si no se ha delimitado.
> 
> 

## <a name="next-steps"></a>Pasos siguientes
Para más sugerencias sobre desarrollo, consulte la [información general sobre desarrollo][development overview].

<!--Image references-->

<!--Article references-->
[development overview]: sql-data-warehouse-overview-develop.md

<!--MSDN references-->

<!--Other Web references-->
