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
ms.workload: Inactive
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/28/2017
ms.author: torsteng
ms.openlocfilehash: 33128357bd5b2bd744c5c1c3032f658ebe865d49
ms.sourcegitcommit: 5a6e943718a8d2bc5babea3cd624c0557ab67bd5
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/01/2017
---
# <a name="multi-shard-querying"></a>Consultas a través de particiones múltiples
## <a name="overview"></a>Información general
Con las [herramientas de Elastic Database](sql-database-elastic-scale-introduction.md), puede crear soluciones de base de datos particionadas. **Consultas a través de particiones múltiples** se usa para tareas como informes y recopilación de datos que requieren ejecutar una consulta que abarca varias particiones. (Compare esto con el [enrutamiento dependiente de los datos](sql-database-elastic-scale-data-dependent-routing.md), que ejecuta todo el trabajo en una sola partición). 

1. Obtenga una clase **RangeShardMap** ([Java](/java/api/com.microsoft.azure.elasticdb.shard.map._range_shard_map), [.NET](https://msdn.microsoft.com/library/azure/dn807318.aspx)) o **ListShardMap** ([Java](/java/api/com.microsoft.azure.elasticdb.shard.map._list_shard_map), [.NET ](https://msdn.microsoft.com/library/azure/dn807370.aspx)) mediante los métodos **TryGetRangeShardMap** ([Java](/java/api/com.microsoft.azure.elasticdb.shard.mapmanager._shard_map_manager.trygetrangeshardmap), [.NET](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanager.trygetrangeshardmap.aspx)), **TryGetListShardMap** ([ Java](/java/api/com.microsoft.azure.elasticdb.shard.mapmanager._shard_map_manager.trygetlistshardmap), [.NET](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanager.trygetlistshardmap.aspx)) o **GetShardMap** ([Java](/java/api/com.microsoft.azure.elasticdb.shard.mapmanager._shard_map_manager.getshardmap), [.NET](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanager.getshardmap.aspx)). Consulte **[Construcción de un ShardMapManager](sql-database-elastic-scale-shard-map-management.md#constructing-a-shardmapmanager)** y **[Obtención de las clases RangeShardMap o ListShardMap](sql-database-elastic-scale-shard-map-management.md#get-a-rangeshardmap-or-listshardmap)**.
2. Cree un objeto **MultiShardConnection** ([Java](/java/api/com.microsoft.azure.elasticdb.query.multishard._multi_shard_connection), [.NET](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.query.multishardconnection.aspx)).
3. Cree un objeto **MultiShardStatement o MultiShardCommand** ([Java](/java/api/com.microsoft.azure.elasticdb.query.multishard._multi_shard_statement), [.NET](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.query.multishardcommand.aspx)). 
4. Establezca la **propiedad CommandText** ([Java](/java/api/com.microsoft.azure.elasticdb.query.multishard._multi_shard_statement), [.NET](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.query.multishardcommand.commandtext.aspx#P:Microsoft.Azure.SqlDatabase.ElasticScale.Query.MultiShardCommand.CommandText)) en un comando T-SQL.
5. Ejecute el comando mediante una llamada a los métodos **ExecuteQueryAsync o ExecuteReader** ([Java](), [.NET](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.query.multishardcommand.executereader.aspx)).
6. Para ver los resultados use las clases **MultiShardResultSet o MultiShardDataReader** ([Java](/java/api/com.microsoft.azure.elasticdb.query.multishard._multi_shard_result_set), [.NET](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.query.multisharddatareader.aspx)). 

## <a name="example"></a>Ejemplo
El código siguiente ilustra el uso de consultas a través de particiones múltiples con un **ShardMap** llamado *myShardMap*. 

```csharp
using (MultiShardConnection conn = new MultiShardConnection(myShardMap.GetShards(), myShardConnectionString)) 
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
```

Una diferencia clave es la construcción de conexiones entre particiones múltiples. Donde **SqlConnection** opera en una sola base de datos, **MultiShardConnection** toma una ***colección de particiones*** como entrada. Complete la recopilación de particiones a partir de un mapa de particiones. Luego la consulta se ejecuta en la recopilación de particiones usando la semántica **UNION ALL** para ensamblar un solo resultado global. De manera opcional, el nombre de la partición donde se origina la fila se puede agregar a la salida usando la propiedad **ExecutionOptions** en el comando. 

Observe la llamada a **myShardMap.GetShards()**. Este método recupera todas las particiones desde el mapa de particiones y brinda una manera fácil de ejecutar una consulta a través de todas las bases de datos importantes. La colección de particiones para una consulta a través de particiones múltiples se puede restringir aún más si se realiza una consulta LINQ sobre la colección devuelta desde la llamada a **myShardMap.GetShards()**. En combinación con la directiva de resultados parciales, la actual capacidad en las consultas a través de particiones múltiples se diseñó para funcionar bien con decenas, y hasta centenas, de particiones.

Una limitación con las consultas a través de particiones múltiples actualmente es la falta de validación de las particiones y de los shardlets que se consultan. Mientras que el enrutamiento dependiente de los datos comprueba que una partición determinada sea parte del mapa de particiones en el momento de la consulta, las consultas a través de particiones múltiples no realizan esta comprobación. Esto puede llevar a que las consultas a través de particiones múltiples se ejecuten en bases de datos que se han quitado del mapa de particiones.

## <a name="multi-shard-queries-and-split-merge-operations"></a>Consultas a través de particiones múltiples y operaciones de división y combinación
Las consultas a través de particiones múltiples no comprueban si los shardlets en la base de datos consultada forman parte de las operaciones de división y combinación en curso. (Consulte el artículo sobre [escalado con la herramienta de división y combinación de Elastic Database](sql-database-elastic-scale-overview-split-and-merge.md)). Esto puede llevar a incoherencias donde las filas del mismo shardlet se muestran para bases de datos múltiples en la misma consulta a través de particiones múltiples. Tenga en cuenta estas limitaciones y considere la posibilidad de agotar las operaciones de división y combinación y los cambios en el mapa de particiones mientras se realizan consultas a través de particiones múltiples.

[!INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]


