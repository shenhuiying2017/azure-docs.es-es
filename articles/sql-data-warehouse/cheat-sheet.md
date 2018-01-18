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
ms.date: 12/14/2017
ms.author: acomet
ms.openlocfilehash: 2d17385ff255ddf7b85baa81600a2af60d015540
ms.sourcegitcommit: 48fce90a4ec357d2fb89183141610789003993d2
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/12/2018
---
# <a name="cheat-sheet-for-azure-sql-data-warehouse"></a>Hoja de referencia rápida de Azure SQL Data Warehouse
Esta página debería ayudarle a diseñar los casos de uso principales de una solución de almacenamiento de datos. Esta hoja de referencia rápida le servirá de gran ayuda en su viaje por la creación de almacenamiento de datos de primera clase; sin embargo, se recomienda firmemente profundizar más en el aprender de cada paso. En primer lugar, es recomendable leer este excelente artículo sobre **[qué es y no es]** SQL Data Warehouse.

A continuación se muestra un boceto del proceso que debe seguir al comenzar a diseñar SQL Data Warehouse.

![Boceto]

## <a name="queries-and-operations-across-tables"></a>Consultas y operaciones entre tablas

Es realmente importante conocer de antemano las operaciones y consultas más importantes que han tenido lugar en el almacenamiento de datos. En la arquitectura de almacenamiento de datos se debe dar prioridad a esas operaciones. Ejemplos comunes de operaciones podrían ser:
* Combinar una o dos tablas de hechos con tablas de dimensiones, filtrar esta tabla durante un tiempo y anexar los resultados a un data mart
* Realizar actualizaciones grandes o pequeñas en las ventas de hechos
* Anexar solo datos a las tablas

Conocer el tipo de operaciones le ayuda a optimizar el diseño de las tablas.

## <a name="data-migration"></a>Migración de datos

Se recomienda cargar primero los datos **[en ADLS]** o Azure Blob Storage. A continuación, debe usar Polybase para cargar los datos en SQL Data Warehouse en una tabla de ensayo. Se recomienda la siguiente configuración:

| Diseño | Recomendación |
|:--- |:--- |
| Distribución | Round Robin |
| Indización | Montón |
| Creación de particiones | None |
| Clase de recursos | largerc o xlargerc |

Aprenda más sobre la **[migración de datos] y la [carga de datos]** con **[instrucciones detalladas] sobre la carga**. 

## <a name="distributed-or-replicated-tables"></a>Tablas replicadas o distribuidas

Se recomiendan las siguientes estrategias en función de las propiedades de tabla:

| type | Muy adecuado para | Esté atento a...|
|:--- |:--- |:--- |
| Replicado | • Tablas de pequeñas dimensiones en esquema de estrella con menos de 2 GB de almacenamiento tras la compresión (compresión ~5x) |•  Muchas transacciones de escritura en la tabla (por ejemplo, insertar, upsert, eliminar, actualizar)<br></br>•   Cambio frecuente del aprovisionamiento de las unidades de almacenamiento de datos (DWU)<br></br>• Solo usa 2 o 3 columnas y la tabla tiene muchas columnas<br></br>• Va a indexar una tabla replicada |
| Round Robin (valor predeterminado) | • Tabla de ensayo/temporal<br></br> • Sin clave de combinación obvia o columna buena candidata |•  Rendimiento lento debido al movimiento de datos |
| Hash | • Tabla de hechos<br></br>• Tablas de grandes dimensiones |• La clave de distribución no se puede actualizar |

**Sugerencias:**
* Comience con Round Robin pero con miras a una estrategia de distribución hash para aprovechar la arquitectura masiva en paralelo.
* Asegúrese de que las claves de hash comunes tengan el mismo formato de datos.
* No distribuya en formato varchar.
* Las tablas de dimensiones con claves de hash comunes para una tabla de hechos con operaciones frecuentes de combinación no se pueden distribuir mediante hash.
* Use *[sys.dm_pdw_nodes_db_partition_stats]* para analizar los posibles sesgos en los datos.
* Use *[sys.dm_pdw_request_steps]* para analizar el movimiento de datos detrás de las consultas y supervisar lo que tardan las operaciones de difusión y orden aleatorio. Resulta útil para revisar la estrategia de distribución.

Aprenda más sobre las **[tablas replicadas] y las [tablas distribuidas]**.

## <a name="indexing-your-table"></a>Indexación de la tabla

La indexación es un método **excelente** para leer tablas rápidamente. Existe un conjunto único de tecnologías que puede usar en función de sus necesidades:

| type | Muy adecuado para | Esté atento a...|
|:--- |:--- |:--- |
| Montón | • Tabla de ensayo/temporal<br></br>• Tablas pequeñas con búsquedas pequeñas |• Cualquier búsqueda recorre la tabla completa |
| Índice agrupado | • Tablas de hasta 100 millones de filas<br></br>• Uso habitual de tablas grandes (más de 100 millones de filas) con solo 1 o 2 columnas |• Se usa en tablas replicadas<br></br>• Consultas complejas que implican varias operaciones Join y Group By<br></br>• La realización de actualizaciones en las columnas indexadas consume memoria |
| Índice de almacén de columnas agrupado (CCI) (predeterminado) | • Tablas de gran tamaño (más de 100 millones de filas) | • Se usa en tablas replicadas<br></br>• Realización de operaciones masivas de actualización en la tabla<br></br>• Tabla particionada en exceso: los grupos de filas no se distribuyen entre diferentes particiones y nodos de distribución |

**Sugerencias:**
* A partir de un índice agrupado, puede que quiera agregar un índice no agrupado a una columna que se usa mucho como filtro. 
* Tenga cuidado con cómo administra la memoria en una tabla con CCI. Al cargar los datos, querrá que el usuario (o la consulta) se beneficie de una clase de recursos grande. Asegúrese de evitar recortes y la creación de muchos grupos de filas comprimidos pequeños.
* Optimizado para filas de nivel de proceso con CCI
* En CCI, la existencia de un rendimiento lento puede ocurrir por la compresión deficiente de los grupos de filas, por lo que es posible que quiera recompilar o reorganizar el CCI. Querrá al menos 100 000 filas por grupos de filas comprimidos. Lo más conveniente es 1 millón de filas en un grupo de filas.
* Según la frecuencia y el tamaño de la carga incremental, querrá automatizar la reorganización o recompilación de los índices. Hacer limpieza primaveral siempre es útil.
* Guíese por una estrategia cuando recorte un grupo de filas: ¿cómo de grandes son los grupos de filas abiertos? ¿cuántos datos espera cargar los próximos días?

Aprenda más sobre los **[índices]**.

## <a name="partitioning"></a>Creación de particiones
Cuando tenga tablas de hechos de gran tamaño (>1000 millones de filas), puede crear particiones de ellas. En el 99 % de los casos, la clave de partición debe basarse en la fecha. Tenga cuidado de no crear particiones en exceso, en especial si tiene un índice de almacén de columnas agrupado.
Con tablas de ensayo que requieren ETL, puede beneficiarse de la creación de particiones, ya que facilita la administración del ciclo de vida de los datos.
Sin embargo, tenga cuidado de no crear particiones en exceso de los datos, en especial en un índice de almacén de columnas agrupado.

Aprenda más sobre las **[particiones]**.

## <a name="incremental-load"></a>Carga incremental

En primer lugar, debe asegurarse de asignar clases de recursos más grandes a la carga de los datos. Se recomienda usar Polybase y ADF V2 para la automatización de las canalizaciones ETL en SQL DW.

Con lotes grandes de actualizaciones en los datos históricos, se recomienda eliminar primero los datos en cuestión. A continuación, puede realizar una inserción masiva de los nuevos datos. Este enfoque de dos pasos es más eficaz.

## <a name="maintain-statistics"></a>Mantenimiento de estadísticas
En breve estarán disponibles con carácter general estadísticas automáticas. Hasta entonces, SQL Data Warehouse requiere el mantenimiento manual de las estadísticas. Es importante actualizar las estadísticas cuando se produzcan cambios **significativos** en los datos. Esto ayuda a optimizar los planes de consulta. Si observa que el mantenimiento de todas las estadísticas tarda demasiado, podría ser más selectivo sobre qué columnas tienen estadísticas. También puede definir la frecuencia de las actualizaciones. Por ejemplo, puede actualizar las columnas de fecha, donde se añadan valores todos los días. Se beneficiará enormemente de tener estadísticas sobre columnas que intervienen en las combinaciones, columnas que se usan en la cláusula WHERE y columnas de GROUP BY.

Aprenda más sobre las **[estadísticas]**.

## <a name="resource-class"></a>clase de recursos
SQL Data Warehouse usa grupos de recursos para asignar memoria a las consultas. Si necesita más memoria para mejorar la velocidad de las consultas o de la carga, debe asignar clases de recursos superiores. Por otro lado, el uso de clases de recursos más grandes afecta a la simultaneidad. Esto lo deberá tener en cuenta antes de mover todos los usuarios a una clase de recursos grande.

Si observa que las consultas tardan demasiado tiempo, compruebe que los usuarios no se ejecutan en clases de recursos grandes. Las clases de recursos grande consumen mucho espacio de simultaneidad y pueden enviar a otras consultas a la cola.

Por último, si usa el nivel optimizado de proceso, cada clase de recurso obtiene 2,5 veces más memoria que en el nivel optimizado elástico.

Aprenda más sobre cómo trabajar con **[clases de recursos y simultaneidad]**.

## <a name="lower-your-cost"></a>Reducción de los costos
Una característica clave de SQL Data Warehouse es la capacidad de pausar cuando no se usa, que detiene la facturación de los recursos de proceso. Otra característica clave es la capacidad de escalar los recursos. La pausa y la escala se pueden usar desde Azure Portal o mediante comandos de PowerShell.

Realice el escalado automático ahora, en el momento que quiera con Azure Functions:

<a href="https://ms.portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FMicrosoft%2Fsql-data-warehouse-samples%2Fmaster%2Farm-templates%2FsqlDwTimerScaler%2Fazuredeploy.json" target="_blank">
<img src="https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/1-CONTRIBUTION-GUIDE/images/deploytoazure.png"/>
</a>

## <a name="optimize-you-architecture-for-performance"></a>Optimización de la arquitectura para aumentar el rendimiento

Se recomienda considerar la posibilidad de una arquitectura hub-and-spokes (concentrador y radios) con SQL Database y Azure Analysis Services. Esta solución puede proporcionar el aislamiento de la carga de trabajo entre diferentes grupos de usuarios, al tiempo que se aprovechan algunas características de seguridad avanzadas de SQL DB y Azure Analysis Services. También es una manera de proporcionar simultaneidad ilimitada a los usuarios.

Aprenda más sobre las **[arquitecturas habituales que aprovechan SQL DW]**.

Implemente con un clic sus radios en bases de datos SQL DB desde SQL DW:

<a href="https://ms.portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FMicrosoft%2Fsql-data-warehouse-samples%2Fmaster%2Farm-templates%2FsqlDwSpokeDbTemplate%2Fazuredeploy.json" target="_blank">
<img src="https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/1-CONTRIBUTION-GUIDE/images/deploytoazure.png"/>
</a>


<!--Image references-->
[Boceto]:media/sql-data-warehouse-cheat-sheet/picture-flow.png

<!--Article references-->
[carga de datos]:./design-elt-data-loading.md
[instrucciones detalladas]: ./guidance-for-loading-data.md
[índices]:./sql-data-warehouse-tables-index.md
[particiones]:./sql-data-warehouse-tables-partition.md
[estadísticas]:./sql-data-warehouse-tables-statistics.md
[clases de recursos y simultaneidad]:./sql-data-warehouse-develop-concurrency.md

<!--MSDN references-->


<!--Other Web references-->
[arquitecturas habituales que aprovechan SQL DW]: https://blogs.msdn.microsoft.com/sqlcat/2017/09/05/common-isv-application-patterns-using-azure-sql-data-warehouse/
[qué es y no es]:https://blogs.msdn.microsoft.com/sqlcat/2017/09/05/azure-sql-data-warehouse-workload-patterns-and-anti-patterns/
[migración de datos]:https://blogs.msdn.microsoft.com/sqlcat/2016/08/18/migrating-data-to-azure-sql-data-warehouse-in-practice/
[tablas replicadas]:https://docs.microsoft.com/azure/sql-data-warehouse/design-guidance-for-replicated-tables
[tablas distribuidas]:https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-tables-distribute
[en ADLS]: https://docs.microsoft.com/azure/data-factory/connector-azure-data-lake-store
[sys.dm_pdw_nodes_db_partition_stats]: https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-db-partition-stats-transact-sql
[sys.dm_pdw_request_steps]:https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-request-steps-transact-sql
