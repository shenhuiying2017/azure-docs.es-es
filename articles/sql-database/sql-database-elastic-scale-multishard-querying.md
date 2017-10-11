---
title: Consulta de bases de datos Azure SQL Database con particiones | Microsoft Docs
description: "Ejecute consultas a través de particiones con la biblioteca de cliente de bases de datos elásticas."
services: sql-database
documentationcenter: 
manager: jhubbard
author: torsteng
editor: 
ms.assetid: a4379c15-f213-4026-ab6f-a450ee9d5758
ms.service: sql-database
ms.custom: scale out apps
ms.workload: sql-database
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/12/2016
ms.author: torsteng
ms.openlocfilehash: 67bcb3c7fe33341103f28bc70e8cc2acbb924cae
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 07/11/2017
---
# <a name="multi-shard-querying"></a>Consultas a través de particiones múltiples
## <a name="overview"></a>Información general
Con las [herramientas de Base de datos elástica](sql-database-elastic-scale-introduction.md), puede crear soluciones de base de datos particionadas. **Consultas a través de particiones múltiples** se usa para tareas como informes y recopilación de datos que requieren ejecutar una consulta que abarca varias particiones. (Compare esto con el [enrutamiento dependiente de los datos](sql-database-elastic-scale-data-dependent-routing.md), que ejecuta todo el trabajo en una sola partición). 

1. Obtenga una clase [**RangeShardMap**](https://msdn.microsoft.com/library/azure/dn807318.aspx) o [**ListShardMap**](https://msdn.microsoft.com/library/azure/dn807370.aspx) con el método [**TryGetRangeShardMap**](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanager.trygetrangeshardmap.aspx), [**TryGetListShardMap**](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanager.trygetlistshardmap.aspx) o [**GetShardMap**](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanager.getshardmap.aspx). Consulte [**Construcción de un ShardMapManager**](sql-database-elastic-scale-shard-map-management.md#constructing-a-shardmapmanager) y [**Obtención de las clases RangeShardMap o ListShardMap**](sql-database-elastic-scale-shard-map-management.md#get-a-rangeshardmap-or-listshardmap).
2. Cree un objeto **[MultiShardConnection](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.query.multishardconnection.aspx)**.
3. Cree un objeto **[MultiShardCommand](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.query.multishardcommand.aspx)**. 
4. Establezca la **[propiedad CommandText](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.query.multishardcommand.commandtext.aspx#P:Microsoft.Azure.SqlDatabase.ElasticScale.Query.MultiShardCommand.CommandText)** en un comando T-SQL.
5. Ejecute el comando mediante la invocación del **[método ExecuteReader](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.query.multishardcommand.executereader.aspx)**.
6. Vea los resultados con la **[clase MultiShardDataReader](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.query.multisharddatareader.aspx)**. 

## <a name="example"></a>Ejemplo
El código siguiente ilustra el uso de consultas a través de particiones múltiples con un **ShardMap** llamado *myShardMap*. 

    using (MultiShardConnection conn = new MultiShardConnection( 
                                        myShardMap.GetShards(), 
                                        myShardConnectionString) 
          ) 
    { 
    using (MultiShardCommand cmd = conn.CreateCommand())
           { 
            cmd.CommandText = "SELECT c1, c2, c3 FROM ShardedTable"; 
            cmd.CommandType = CommandType.Text; 
            cmd.ExecutionOptions = MultiShardExecutionOptions.IncludeShardNameColumn; 
            cmd.ExecutionPolicy = MultiShardExecutionPolicy.PartialResults; 

            using (MultiShardDataReader sdr = cmd.ExecuteReader()) 
                { 
                    while (sdr.Read())
                        { 
                            var c1Field = sdr.GetString(0); 
                            var c2Field = sdr.GetFieldValue<int>(1); 
                            var c3Field = sdr.GetFieldValue<Int64>(2);
                        } 
                 } 
           } 
    } 


Una diferencia clave es la construcción de conexiones entre particiones múltiples. Donde **SqlConnection** opera en una sola base de datos, **MultiShardConnection** toma una ***colección de particiones*** como entrada. Complete la recopilación de particiones a partir de un mapa de particiones. Luego la consulta se ejecuta en la recopilación de particiones usando la semántica **UNION ALL** para ensamblar un solo resultado global. De manera opcional, el nombre de la partición donde se origina la fila se puede agregar a la salida usando la propiedad **ExecutionOptions** en el comando. 

Observe la llamada a **myShardMap.GetShards()**. Este método recupera todas las particiones desde el mapa de particiones y brinda una manera fácil de ejecutar una consulta a través de todas las bases de datos importantes. La colección de particiones para una consulta a través de particiones múltiples se puede restringir aún más si se realiza una consulta LINQ sobre la colección devuelta desde la llamada a **myShardMap.GetShards()**. En combinación con la directiva de resultados parciales, la actual capacidad en las consultas a través de particiones múltiples se diseñó para funcionar bien con decenas, y hasta centenas, de particiones.

Una limitación con las consultas a través de particiones múltiples actualmente es la falta de validación de las particiones y de los shardlets que se consultan. Mientras que el enrutamiento dependiente de los datos comprueba que una partición determinada sea parte del mapa de particiones en el momento de la consulta, las consultas a través de particiones múltiples no realizan esta comprobación. Esto puede llevar a que las consultas a través de particiones múltiples se ejecuten en bases de datos que se han quitado del mapa de particiones.

## <a name="multi-shard-queries-and-split-merge-operations"></a>Consultas a través de particiones múltiples y operaciones de división y combinación
Las consultas a través de particiones múltiples no comprueban si los shardlets en la base de datos consultada forman parte de las operaciones de división y combinación en curso. (Consulte el artículo sobre [escalado con la herramienta de división y combinación de Elastic Database](sql-database-elastic-scale-overview-split-and-merge.md)). Esto puede llevar a incoherencias donde las filas del mismo shardlet se muestran para bases de datos múltiples en la misma consulta a través de particiones múltiples. Tenga en cuenta estas limitaciones y considere la posibilidad de agotar las operaciones de división y combinación y los cambios en el mapa de particiones mientras se realizan consultas a través de particiones múltiples.

[!INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]

## <a name="see-also"></a>Consulte también
Clases y métodos **[System.Data.SqlClient](http://msdn.microsoft.com/library/System.Data.SqlClient.aspx)**.

Administre las particiones con la [biblioteca de cliente de Base de datos elástica](sql-database-elastic-database-client-library.md). Incluye un nuevo espacio de nombres llamado [Microsoft.Azure.SqlDatabase.ElasticScale.Query](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.query.aspx) que proporciona la capacidad de consultar varias particiones con una sola solicitud y un resultado. Brinda una abstracción de consulta sobre una recopilación de particiones. También proporciona directivas de ejecución alternativas, en especial resultados parciales, para abordar errores cuando se consulte sobre muchas particiones.  

