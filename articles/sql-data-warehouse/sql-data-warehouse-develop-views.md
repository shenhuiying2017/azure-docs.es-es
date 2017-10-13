---
title: Uso de vistas T-SQL en Azure SQL Data Warehouse | Microsoft Docs
description: Sugerencias para usar las vistas Transact-SQL en el Almacenamiento de datos SQL Azure para desarrollar soluciones.
services: sql-data-warehouse
documentationcenter: NA
author: jrowlandjones
manager: jhubbard
editor: 
ms.assetid: b5208f32-8f4a-4056-8788-2adbb253d9fd
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.custom: t-sql
ms.date: 10/31/2016
ms.author: jrj;barbkess
ms.openlocfilehash: d2a03be810bd7f792876607ec735eb578b65a3b5
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="views-in-sql-data-warehouse"></a>Vistas en el Almacenamiento de datos SQL
Las vistas son especialmente útiles en el Almacenamiento de datos SQL. Se pueden usar de formas diferentes para mejorar la calidad de la solución.  Este artículo resalta algunos ejemplos de cómo enriquecer su solución con vistas, así como las limitaciones que se deben tener en cuenta.

> [!NOTE]
> En este artículo no se explica la sintaxis de `CREATE VIEW`. Consulte el artículo [CREATE VIEW][CREATE VIEW] de MSDN para esta información de referencia.
> 
> 

## <a name="architectural-abstraction"></a>Abstracción de arquitectura
Se trata de un patrón de aplicación muy común para volver a crear tablas con la característica CREATE TABLE AS SELECT (CTAS) seguida de un patrón de cambio de nombre de objetos mientras se cargan los datos.

En el ejemplo siguiente se agregan registros de fecha nuevos a una dimensión de fecha. Observe cómo una nueva tabla, DimDate_New, se crea por primera vez y luego cambia de nombre para reemplazar la versión original de la tabla.

```sql
CREATE TABLE dbo.DimDate_New
WITH (DISTRIBUTION = ROUND_ROBIN
, CLUSTERED INDEX (DateKey ASC)
)
AS
SELECT *
FROM   dbo.DimDate  AS prod
UNION ALL
SELECT *
FROM   dbo.DimDate_stg AS stg
;

RENAME OBJECT DimDate TO DimDate_Old;
RENAME OBJECT DimDate_New TO DimDate;

```

Sin embargo, este enfoque puede provocar que las tablas aparezcan y desaparezcan de la vista del usuario, así como mensajes de error del tipo "la tabla no existe". Las vistas pueden utilizarse para proporcionar una capa de presentación coherente mientras se cambia el nombre de los objetos subyacentes. Proporcionar a los usuarios acceso a los datos mediante vistas significa que los usuarios no necesitan tener visibilidad de las tablas subyacentes. Esto proporciona una experiencia de usuario coherente, al tiempo que garantiza que los diseñadores de almacenamiento de datos puedan desarrollar el modelo de datos y maximizar el rendimiento mediante el uso de CTAS durante el proceso de carga de datos.    

## <a name="performance-optimization"></a>Optimización del rendimiento
Las vistas se pueden usar también para aplicar combinaciones de rendimiento optimizado entre tablas. Por ejemplo, una vista puede incorporar una clave de distribución redundante como parte de los criterios de combinación para minimizar el movimiento de datos.  Otra ventaja de una vista podría ser forzar una consulta específica o una sugerencia de combinación. La utilización de vistas de esta manera garantiza que las combinaciones siempre se realizarán de manera óptima, evitando la necesidad de los usuarios de recordar la construcción correcta de sus combinaciones.

## <a name="limitations"></a>Limitaciones
Las vistas en el almacenamiento de datos SQL son solo metadatos.  Por lo tanto, no están disponibles las siguientes opciones:

* No hay ninguna opción de enlace de esquema.
* Las tablas base no se puede actualizar a través de la vista.
* No se pueden crear vistas en tablas temporales.
* No hay compatibilidad con las sugerencias EXPAND y NOEXPAND
* No hay ninguna vista indexada en Almacenamiento de datos SQL.

## <a name="next-steps"></a>Pasos siguientes
Para obtener más sugerencias sobre desarrollo, consulte la [información general sobre desarrollo de Almacenamiento de datos SQL][SQL Data Warehouse development overview].
Para la sintaxis de `CREATE VIEW`, consulte [CREATE VIEW][CREATE VIEW].

<!--Image references-->

<!--Article references-->
[SQL Data Warehouse development overview]: ./sql-data-warehouse-overview-develop.md

<!--MSDN references-->
[CREATE VIEW]: https://msdn.microsoft.com/en-us/library/ms187956.aspx

<!--Other Web references-->
