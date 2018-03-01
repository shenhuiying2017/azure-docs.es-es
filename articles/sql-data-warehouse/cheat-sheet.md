---
title: "Hoja de referencia rápida de Azure SQL Data Warehouse | Microsoft Docs"
description: "Encuentre vínculos y procedimientos recomendados para crear rápidamente soluciones de Azure SQL Data Warehouse."
services: sql-data-warehouse
documentationcenter: NA
author: acomet
manager: jhubbard
editor: 
ms.assetid: 51f1e444-9ef7-4e30-9a88-598946c45196
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.custom: manage
ms.date: 02/20/2018
ms.author: acomet
ms.openlocfilehash: 6c8278be32ae6c6452283ba946a36f363dd4d76d
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 02/21/2018
---
# <a name="cheat-sheet-for-azure-sql-data-warehouse"></a>Hoja de referencia rápida de Azure SQL Data Warehouse
En esta hoja de referencia, se proporcionan sugerencias útiles y procedimientos recomendados para la creación de soluciones de Azure SQL Data Warehouse. Antes de empezar, consulte los detalles de cada paso en [Azure SQL Data Warehouse Workload Patterns and Anti-Patterns](https://blogs.msdn.microsoft.com/sqlcat/2017/09/05/azure-sql-data-warehouse-workload-patterns-and-anti-patterns) (Patrones y antipatrones de la carga de trabajo de Azure SQL Data Warehouse), que explica qué es y qué no es SQL Data Warehouse.

En el gráfico siguiente se muestra el proceso de diseño de un almacenamiento de datos:

![Boceto]

## <a name="queries-and-operations-across-tables"></a>Consultas y operaciones entre tablas

Si se sabe de antemano las operaciones y las consultas principales que se van a ejecutar en el almacenamiento de datos, se puede dar prioridad a esas operaciones en la arquitectura de almacenamiento de datos. En estas consultas y operaciones se podría incluir:
* Combinar una o dos tablas de hechos con tablas de dimensiones, filtrar la tabla combinada y, luego, anexar los resultados a un data mart.
* Realizar actualizaciones grandes o pequeñas en las ventas de hechos.
* Anexar solo datos a las tablas.

Conocer los tipos de operaciones de antemano ayuda a optimizar el diseño de las tablas.

## <a name="data-migration"></a>Migración de datos

Primero, cargue los datos en [Azure Data Lake Store](https://docs.microsoft.com/en-us/azure/data-factory/connector-azure-data-lake-store) o Azure Blob Storage. A continuación, use PolyBase para cargar los datos en SQL Data Warehouse en una tabla de almacenamiento provisional. Use la configuración siguiente:

| Diseño | Recomendación |
|:--- |:--- |
| Distribución | Round Robin |
| Indización | Montón |
| Creación de particiones | None |
| Clase de recursos | largerc o xlargerc |

Obtenga más información sobre la [migración de datos], la [carga de datos] y el [proceso de extracción, carga y transformación (ETL)](https://docs.microsoft.com/en-us/azure/sql-data-warehouse/design-elt-data-loading). 

## <a name="distributed-or-replicated-tables"></a>Tablas replicadas o distribuidas

Use las siguientes estrategias, en función de las propiedades de tabla:

| type | Muy adecuado para...| Esté atento a...|
|:--- |:--- |:--- |
| Replicado | • Tablas de pequeñas dimensiones en esquema de estrella con menos de 2 GB de almacenamiento tras la compresión (compresión ~5x) |•  Se producen muchas transacciones de escritura en la tabla (por ejemplo, insertar, upsert, eliminar, actualizar)<br></br>• Cambia frecuentemente el aprovisionamiento de las unidades de almacenamiento de datos (DWU)<br></br>• Solo usa 2 o 3 columnas, pero la tabla tiene muchas columnas<br></br>• Va a indexar una tabla replicada |
| Round Robin (valor predeterminado) | • Tabla de almacenamiento provisional/temporal<br></br> • Sin clave de combinación obvia o columna buena candidata |•  El rendimiento es lento debido al movimiento de datos |
| Hash | • Tabla de hechos<br></br>• Tablas de grandes dimensiones |• La clave de distribución no se puede actualizar |

**Sugerencias:**
* Empiece con Round Robin, pero con miras a una estrategia de distribución hash para aprovechar la arquitectura masiva en paralelo.
* Asegúrese de que las claves hash comunes tengan el mismo formato de datos.
* No distribuya en formato varchar.
* Las tablas de dimensiones con una clave hash común para una tabla de hechos con operaciones frecuentes de combinación se pueden distribuir mediante hash.
* Use *[sys.dm_pdw_nodes_db_partition_stats]* para analizar los posibles sesgos en los datos.
* Use *[sys.dm_pdw_request_steps]* para analizar el movimiento de datos detrás de las consultas y supervisar lo que tardan las operaciones de difusión y orden aleatorio. Resulta útil para revisar la estrategia de distribución.

Aprenda más sobre las [tablas replicadas] y las [tablas distribuidas].

## <a name="index-your-table"></a>Indexación de la tabla

La indexación es útil para leer rápidamente las tablas. Existe un conjunto único de tecnologías que puede usar en función de sus necesidades:

| type | Muy adecuado para... | Esté atento a...|
|:--- |:--- |:--- |
| Montón | • Tabla de ensayo/temporal<br></br>• Tablas pequeñas con búsquedas pequeñas |• Cualquier búsqueda recorre la tabla completa |
| Índice agrupado | • Tablas con hasta 100 millones de filas<br></br>• Tablas grandes (más de 100 millones de filas) con solo 1 o 2 columnas muy usadas |•  Se usa en tablas replicadas<br></br>•    Tiene consultas complejas que implican varias operaciones de combinación y Agrupar por<br></br>•  Realiza actualizaciones en las columnas indexadas, ya que consume memoria |
| Índice de almacén de columnas agrupado (CCI) (predeterminado) | •   Tablas grandes (más de 100 millones de filas) | •   Se usa en tablas replicadas<br></br>• Realización de operaciones masivas de actualización en la tabla<br></br>•  Particiona la tabla en exceso: los grupos de filas no se distribuyen entre diferentes particiones y nodos de distribución |

**Sugerencias:**
* A partir de un índice agrupado, puede que quiera agregar un índice no agrupado a una columna que se usa mucho como filtro. 
* Tenga cuidado con cómo administra la memoria en una tabla con CCI. Al cargar los datos, querrá que el usuario (o la consulta) se beneficie de una clase de recursos grande. Asegúrese de evitar recortes y la creación de muchos grupos de filas comprimidos pequeños.
* Optimizado para filas de nivel de proceso con CCI.
* En CCI, puede producirse un rendimiento lento debido a la compresión deficiente de los grupos de filas. Si ocurre esto, recompile o reorganice el CCI. Es aconsejable tener al menos 100 000 filas por grupos de filas comprimidos. Lo más conveniente es 1 millón de filas en un grupo de filas.
* Según la frecuencia y el tamaño de la carga incremental, querrá automatizar la reorganización o recompilación de los índices. Hacer limpieza primaveral siempre es útil.
* Cree una estrategia cuando desee recortar un grupo de filas. ¿Qué tamaño tienen los grupos de filas abiertos? ¿cuántos datos espera cargar los próximos días?

Aprenda más sobre los [índices].

## <a name="partitioning"></a>Creación de particiones
Cuando tenga tablas de hechos de gran tamaño (más de 1000 millones de filas), puede particionarlas. En el 99 % de los casos, la clave de partición debe basarse en la fecha. Tenga cuidado de no crear particiones en exceso, en especial si tiene un índice de almacén de columnas agrupado.

Con tablas de almacenamiento provisional que requieren ELT, puede beneficiarse de la creación de particiones, ya que facilita la administración del ciclo de vida de los datos.
Sin embargo, tenga cuidado de no crear particiones en exceso de los datos, en especial en un índice de almacén de columnas agrupado.

Aprenda más sobre las [particiones].

## <a name="incremental-load"></a>Carga incremental

Si se va a cargar los datos incrementalmente, primero asegúrese de que asigna clases de recursos mayores para la carga de los datos. Se recomienda usar PolyBase y ADF V2 para la automatización de las canalizaciones ELT en SQL Data Warehouse.

En lotes grandes de actualizaciones de los datos históricos, primero elimine los datos en cuestión. A continuación, realice una inserción masiva de los nuevos datos. Este enfoque de dos pasos es más eficaz.

## <a name="maintain-statistics"></a>Mantenimiento de estadísticas
 Hasta que las estadísticas automáticas estén disponibles de manera general, SQL Data Warehouse requiere el mantenimiento manual de estadísticas. Es importante actualizar las estadísticas cuando se produzcan cambios *significativos* en los datos. Esto ayuda a optimizar los planes de consulta. Si observa que el mantenimiento de todas las estadísticas tarda demasiado, sea más selectivo sobre qué columnas tienen estadísticas. 

También puede definir la frecuencia de las actualizaciones. Por ejemplo, puede actualizar las columnas de fecha, donde se pueden añadir valores nuevos todos los días. Se beneficiará enormemente de tener estadísticas sobre las columnas que intervienen en combinaciones, las columnas que se usan en la cláusula WHERE y las columnas que se encuentran en GROUP BY.

Aprenda más sobre las [estadísticas].

## <a name="resource-class"></a>clase de recursos
SQL Data Warehouse usa grupos de recursos para asignar memoria a las consultas. Si necesita más memoria para mejorar la velocidad de las consultas o de la carga, debe asignar clases de recursos superiores. Por otro lado, el uso de clases de recursos más grandes afecta a la simultaneidad. Esto lo deberá tener en cuenta antes de mover todos los usuarios a una clase de recursos grande.

Si observa que las consultas tardan demasiado tiempo, compruebe que los usuarios no se ejecutan en clases de recursos grandes. Las clases de recursos grande consumen mucho espacio de simultaneidad y pueden enviar a otras consultas a la cola.

Por último, si usa el nivel optimizado de proceso, cada clase de recurso obtiene 2,5 veces más memoria que en el nivel optimizado elástico.

Aprenda más sobre cómo trabajar con [clases de recursos y simultaneidad].

## <a name="lower-your-cost"></a>Reducción de los costos
Una de las características principales de SQL Data Warehouse es la posibilidad de [administrar los recursos de proceso](sql-data-warehouse-manage-compute-overview.md). Puede pausar el almacén de datos cuando no esté en uso, lo que detiene la facturación de los recursos de proceso. Puede escalar los recursos para satisfacer la demanda de rendimiento. Para realizar una pausa, use [Azure Portal](pause-and-resume-compute-portal.md) o [PowerShell](pause-and-resume-compute-powershell.md). Para realizar el escalado, use [Azure Portal](quickstart-scale-compute-portal.md), [Powershell](quickstart-scale-compute-powershell.md), [T-SQL](quickstart-scale-compute-tsql.md) o una [REST API](sql-data-warehouse-manage-compute-rest-api.md#scale-compute).

Realice el escalado automático ahora, en el momento que quiera con Azure Functions:

<a href="https://ms.portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FMicrosoft%2Fsql-data-warehouse-samples%2Fmaster%2Farm-templates%2FsqlDwTimerScaler%2Fazuredeploy.json" target="_blank">
<img src="https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/1-CONTRIBUTION-GUIDE/images/deploytoazure.png"/>
</a>

## <a name="optimize-your-architecture-for-performance"></a>Optimización de la arquitectura para aumentar el rendimiento

Se recomienda considerar la posibilidad de una arquitectura hub-and-spoke (concentrador y radio) con SQL Database y Azure Analysis Services. Esta solución puede proporcionar el aislamiento de la carga de trabajo entre diferentes grupos de usuarios, al tiempo que se aprovechan algunas características de seguridad avanzadas de SQL Database y Azure Analysis Services. También es una manera de proporcionar simultaneidad ilimitada a los usuarios.

Obtenga más información sobre [arquitecturas típicas que se benefician de SQL Data Warehouse](https://blogs.msdn.microsoft.com/sqlcat/2017/09/05/common-isv-application-patterns-using-azure-sql-data-warehouse/).

Implemente con un clic sus radios en bases de datos SQL desde SQL Data Warehouse:

<a href="https://ms.portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FMicrosoft%2Fsql-data-warehouse-samples%2Fmaster%2Farm-templates%2FsqlDwSpokeDbTemplate%2Fazuredeploy.json" target="_blank">
<img src="https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/1-CONTRIBUTION-GUIDE/images/deploytoazure.png"/>
</a>


<!--Image references-->
[Boceto]:media/sql-data-warehouse-cheat-sheet/picture-flow.png

<!--Article references-->
[carga de datos]:./design-elt-data-loading.md
[deeper guidance]: ./guidance-for-loading-data.md
[índices]:./sql-data-warehouse-tables-index.md
[particiones]:./sql-data-warehouse-tables-partition.md
[estadísticas]:./sql-data-warehouse-tables-statistics.md
[clases de recursos y simultaneidad]:./sql-data-warehouse-develop-concurrency.md

<!--MSDN references-->


<!--Other Web references-->
[typical architectures that take advantage of SQL Data Warehouse]: https://blogs.msdn.microsoft.com/sqlcat/2017/09/05/common-isv-application-patterns-using-azure-sql-data-warehouse/
[is and is not]:https://blogs.msdn.microsoft.com/sqlcat/2017/09/05/azure-sql-data-warehouse-workload-patterns-and-anti-patterns/
[migración de datos]:https://blogs.msdn.microsoft.com/sqlcat/2016/08/18/migrating-data-to-azure-sql-data-warehouse-in-practice/
[tablas replicadas]:https://docs.microsoft.com/en-us/azure/sql-data-warehouse/design-guidance-for-replicated-tables
[tablas distribuidas]:https://docs.microsoft.com/en-us/azure/sql-data-warehouse/sql-data-warehouse-tables-distribute
[Azure Data Lake Store]: https://docs.microsoft.com/en-us/azure/data-factory/connector-azure-data-lake-store
[sys.dm_pdw_nodes_db_partition_stats]: https://docs.microsoft.com/en-us/sql/relational-databases/system-dynamic-management-views/sys-dm-db-partition-stats-transact-sql
[sys.dm_pdw_request_steps]:https://docs.microsoft.com/en-us/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-request-steps-transact-sql
