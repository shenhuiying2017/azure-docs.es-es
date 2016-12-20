---
title: Limitaciones de Stretch Database | Microsoft Docs
description: "Obtenga información sobre las limitaciones de Stretch Database."
services: sql-server-stretch-database
documentationcenter: 
author: douglaslMS
manager: jhubbard
editor: 
ms.assetid: 0a865872-7d42-4873-99b9-cbae45691e54
ms.service: sql-server-stretch-database
ms.workload: data-management
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/26/2016
ms.author: anvang
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 3c1599dfc6aee8adf7ec8714c392a7d8d4427d78


---
# <a name="limitations-for-stretch-database"></a>Limitaciones de Stretch Database
Obtenga información sobre las limitaciones de las tablas habilitadas para Stretch y sobre las limitaciones que actualmente evitan que habilite Stretch en una tabla.

## <a name="a-namecaveatsa-limitations-for-stretch-enabled-tables"></a><a name="Caveats"></a> Limitaciones de las tablas habilitadas para Stretch
Las tablas habilitadas para Stretch tienen las limitaciones siguientes.

### <a name="constraints"></a>Restricciones
* No se exige la unicidad en las restricciones UNIQUE ni PRIMARY KEY en la tabla de Azure que contiene los datos migrados.

### <a name="dml-operations"></a>Operaciones de DML
* No puede ACTUALIZAR ni ELIMINAR filas que se hayan migrado, o filas que sean aptas para la migración, en una tabla habilitada para Stretch o en una vista que incluya tablas habilitadas para Stretch.
* No puede INSERTAR filas de una tabla habilitada para Stretch en un servidor vinculado.

### <a name="indexes"></a>Índices
* No se puede crear un índice para una vista que incluya tablas habilitadas para Stretch.
* Los filtros en los índices de SQL Server no se propagan a la tabla remota.

## <a name="a-namelimitationsa-limitations-that-currently-prevent-you-from-enabling-stretch-for-a-table"></a><a name="Limitations"></a> Limitaciones que actualmente evitan que habilite Stretch en una tabla
Los siguientes elementos actualmente evitan que habilite Stretch en una tabla.

### <a name="table-properties"></a>Propiedades de tabla
* Tablas que tienen más de 1 023 columnas o más de 998 índices
* FileTables o tablas que contienen datos FILESTREAM
* Tablas que se replican o que usan activamente Seguimiento de cambios o Captura de datos modificados
* Tablas con optimización para memoria

### <a name="data-types"></a>Tipos de datos
* text, ntext e image
* timestamp
* sql\_variant
* XML
* Tipos de datos CLR, incluidos geometry, geography, hierarchyid y tipos CLR definidos por usuario

### <a name="column-types"></a>Tipos de columna
* COLUMN\_SET
* Columnas calculadas

### <a name="constraints"></a>Restricciones
* Restricciones predeterminadas y restricciones CHECK
* Restricciones de clave externa que hacen referencia a la tabla. En una relación de elementos primarios y secundarios \(por ejemplo, Order y Order\_Detail\), puede habilitar Stretch en la tabla secundaria \(Order\_Detail\), pero no en la tabla principal \(Order\).

### <a name="indexes"></a>Índices
* Índices de texto completo
* Índices XML
* Índices espaciales
* Vistas indexadas que hacen referencia a la tabla

## <a name="see-also"></a>Consulte también
[Identificación de bases de datos y tablas para Stretch Database mediante la ejecución de Stretch Database Advisor](sql-server-stretch-database-identify-databases.md)

[Habilitación de Stretch Database para una base de datos](sql-server-stretch-database-enable-database.md)

[Habilitación de Stretch Database para una tabla](sql-server-stretch-database-enable-table.md)




<!--HONumber=Nov16_HO3-->


