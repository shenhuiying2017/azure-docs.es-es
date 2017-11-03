---
title: Contadores de rendimiento para Shard Map Manager
description: Clase ShardMapManager y contadores de rendimiento de enrutamiento dependiente de los datos
services: sql-database
documentationcenter: 
manager: jhubbard
author: ddove
editor: 
ms.assetid: b090aba0-2e30-454c-96b3-dffa281f539a
ms.service: sql-database
ms.custom: scale out apps
ms.workload: Inactive
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/23/2016
ms.author: ddove
ms.openlocfilehash: 4a7a2e0b4f2be852c61fc3ccdd4dea2034b80918
ms.sourcegitcommit: dfd49613fce4ce917e844d205c85359ff093bb9c
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/31/2017
---
# <a name="performance-counters-for-shard-map-manager"></a>Contadores de rendimiento para Shard Map Manager
Puede capturar el rendimiento de una instancia de [Shard Map Manager](sql-database-elastic-scale-shard-map-management.md), en especial cuando se utiliza el [enrutamiento dependiente de los datos](sql-database-elastic-scale-data-dependent-routing.md). Los contadores se crean con métodos de la clase Microsoft.Azure.SqlDatabase.ElasticScale.Client.  

Los contadores se utilizan para supervisar el rendimiento de operaciones de [enrutamiento dependiente de los datos](sql-database-elastic-scale-data-dependent-routing.md) . Dichos contadores son accesibles en el Monitor de rendimiento, en la categoría "Base de datos elástica: Administración de particiones".

**Para obtener la versión más reciente:** vaya a [Microsoft.Azure.SqlDatabase.ElasticScale.Client](https://www.nuget.org/packages/Microsoft.Azure.SqlDatabase.ElasticScale.Client/). Consulte también [Actualización de una aplicación para usar la biblioteca de cliente de base de datos elástica más reciente](sql-database-elastic-scale-upgrade-client-library.md).

## <a name="prerequisites"></a>Requisitos previos
* Para crear la categoría y los contadores de rendimiento, el usuario debe formar parte del grupo **Administradores** local en el equipo que hospeda la aplicación.  
* Para crear una instancia de contador de rendimiento y actualizar los contadores, el usuario debe ser miembro del grupo **Administradores** o del grupo **Usuarios del monitor de sistema**. 

## <a name="create-performance-category-and-counters"></a>Creación de la categoría y los contadores de rendimiento
Para crear los contadores, llame al método CreatePeformanceCategoryAndCounters de la [clase ShardMapManagmentFactory](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanagerfactory.aspx). Solo un administrador puede ejecutar el método: 

    ShardMapManagerFactory.CreatePerformanceCategoryAndCounters()  

También puede usar [este](https://gallery.technet.microsoft.com/scriptcenter/Elastic-DB-Tools-for-Azure-17e3d283) script de PowerShell para ejecutar el método. El método crea los siguientes contadores de rendimiento:  

* **Cached mappings**(Asignaciones en caché): número de asignaciones almacenadas en caché para el mapa de particiones.
* **DDR operations/sec**(Operaciones de DDR/s): tasa de operaciones de enrutamiento dependientes de los datos para el mapa de particiones. Este contador se actualiza cuando una llamada a [OpenConnectionForKey()](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmap.openconnectionforkey.aspx) da lugar a una conexión correcta con la partición de destino. 
* **Mapping lookup cache hits/sec**(Aciertos/s de caché de búsqueda de asignaciones): tasa de operaciones correctas de búsqueda en la caché de asignaciones en el mapa de particiones. 
* **Mapping lookup cache misses/sec**(Errores/s de caché de búsqueda de asignaciones): tasa de operaciones erróneas de búsqueda en la caché de asignaciones en el mapa de particiones.
* **Mappings added or updated in cache/sec**(Asignaciones agregadas o actualizadas en la caché/s): velocidad a la que se agregan o actualizan las asignaciones en la caché en el mapa de particiones. 
* **Mappings removed from cache/sec**(Asignaciones eliminadas de la caché/s): velocidad a la que se eliminan las asignaciones de la caché en el mapa de particiones. 

Los contadores de rendimiento se crean para cada mapa de particiones en caché por proceso.  

## <a name="notes"></a>Notas
Los siguientes eventos desencadenan la creación de los contadores de rendimiento:  

* Inicialización de [ShardMapManager](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanager.aspx) con [carga diligente](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanagerloadpolicy.aspx), si ShardMapManager no contiene ningún mapa de particiones. Aquí se incluyen los métodos [GetSqlShardMapManager](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanagerfactory.getsqlshardmapmanager.aspx?f=255&MSPPError=-2147217396#M:Microsoft.Azure.SqlDatabase.ElasticScale.ShardManagement.ShardMapManagerFactory.GetSqlShardMapManager%28System.String,Microsoft.Azure.SqlDatabase.ElasticScale.ShardManagement.ShardMapManagerLoadPolicy%29) y [TryGetSqlShardMapManager](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanagerfactory.trygetsqlshardmapmanager.aspx).
* Búsqueda correcta de un mapa de particiones (mediante [GetShardMap()](https://msdn.microsoft.com/library/azure/dn824215.aspx), [GetListShardMap()](https://msdn.microsoft.com/library/azure/dn824212.aspx) o [GetRangeShardMap()](https://msdn.microsoft.com/library/azure/dn824173.aspx)). 
* Creación correcta de un mapa de particiones con CreateShardMap().

Los contadores de rendimiento se actualizarán con todas las operaciones de caché realizadas en el mapa de particiones y las asignaciones. La eliminación correcta del mapa de particiones mediante DeleteShardMap() da lugar a la eliminación de la instancia de contadores de rendimiento.  

## <a name="best-practices"></a>Prácticas recomendadas
* La creación de la categoría y los contadores de rendimiento debe realizarse una sola vez antes de la creación del objeto ShardMapManager. Cada ejecución del comando CreatePerformanceCategoryAndCounters() borra los contadores anteriores (se pierden los datos notificados por todas las instancias) y crea otros nuevos.  
* Las instancias de contadores de rendimiento se crean por proceso. Cualquier bloqueo de la aplicación o eliminación de un mapa de particiones de la caché da lugar a la eliminación de las instancias de contadores de rendimiento.  

### <a name="see-also"></a>Consulte también 
[Información general de las características de bases de datos elásticas](sql-database-elastic-scale-introduction.md)  

[!INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]

<!--Anchors-->
<!--Image references-->

