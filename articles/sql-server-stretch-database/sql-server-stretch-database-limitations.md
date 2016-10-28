<properties
	pageTitle="Limitaciones de Stretch Database | Microsoft Azure"
	description="Obtenga información sobre las limitaciones de Stretch Database."
	services="sql-server-stretch-database"
	documentationCenter=""
	authors="douglaslMS"
	manager=""
	editor=""/>

<tags
	ms.service="sql-server-stretch-database"
	ms.workload="data-management"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="06/14/2016"
	ms.author="douglasl"/>

# Limitaciones de Stretch Database

Obtenga información sobre las limitaciones de las tablas habilitadas para Stretch y sobre las limitaciones que actualmente evitan que habilite Stretch en una tabla.

##  <a name="Caveats"></a> Limitaciones de las tablas habilitadas para Stretch

Las tablas habilitadas para Stretch tienen las limitaciones siguientes.

### Restricciones

-   No se exige la unicidad en las restricciones UNIQUE ni PRIMARY KEY en la tabla de Azure que contiene los datos migrados.

### Operaciones de DML

-   No puede actualizar ni eliminar filas que se hayan migrado, o filas que sean aptas para la migración, en una tabla habilitada para Stretch o en una vista que incluya tablas habilitadas para Stretch.

-   No se puede realizar la acción INSERT en las filas de una tabla habilitada para Stretch en un servidor vinculado.

### Índices

-   No se puede crear un índice para una vista que incluya tablas habilitadas para Stretch.

-   Los filtros en los índices de SQL Server no se propagan a la tabla remota.

##  <a name="Limitations"></a> Limitaciones que actualmente evitan que habilite Stretch en una tabla

Los siguientes elementos actualmente evitan que habilite Stretch en una tabla.

### Propiedades de tabla

-   Tablas que tienen más de 1 023 columnas o más de 998 índices

-   FileTables o tablas que contienen datos FILESTREAM

-   Tablas que se replican o que usan activamente Seguimiento de cambios o Captura de datos modificados

-   Tablas con optimización para memoria

### Tipos de datos

-   text, ntext e image

-   timestamp

-   sql\_variant

-   XML

-   Tipos de datos CLR, incluidos geometry, geography, hierarchyid y tipos CLR definidos por usuario.

### Tipos de columna

-   COLUMN\_SET

-   Columnas calculadas

### Restricciones

-   Restricciones predeterminadas y restricciones CHECK

-   Restricciones de clave externa que hacen referencia a la tabla. En una relación de elementos primarios y secundarios (por ejemplo, Order y Order\_Detail), puede habilitar Stretch en la tabla secundaria (Order\_Detail), pero no en la tabla principal (Order).

### Índices

-   Índices de texto completo

-   Índices XML

-   Índices espaciales

-   Vistas indexadas que hacen referencia a la tabla

## Consulte también

[Identificación de bases de datos y tablas para Stretch Database mediante la ejecución de Stretch Database Advisor](sql-server-stretch-database-identify-databases.md)

[Habilitación de Stretch Database para una base de datos](sql-server-stretch-database-enable-database.md)

[Habilitación de Stretch Database para una tabla](sql-server-stretch-database-enable-table.md)

<!---HONumber=AcomDC_0615_2016-->