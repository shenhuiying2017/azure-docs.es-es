---
title: "Configuración de la replicación geográfica para Azure SQL Database con Transact-SQL | Microsoft Docs"
description: "Configuración de la replicación geográfica para Azure SQL Database con Transact-SQL"
services: sql-database
documentationcenter: 
author: CarlRabeler
manager: jhubbard
editor: 
ms.assetid: d94d89a6-3234-46c5-8279-5eb8daad10ac
ms.service: sql-database
ms.custom: business continuity
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 04/14/2017
ms.author: carlrab
ms.translationtype: Human Translation
ms.sourcegitcommit: 3716c7699732ad31970778fdfa116f8aee3da70b
ms.openlocfilehash: e5011c1c67e051616d53621b72e46ba894ca3c02
ms.contentlocale: es-es
ms.lasthandoff: 06/30/2017


---
# <a name="configure-active-geo-replication-for-azure-sql-database-with-transact-sql"></a>Configuración de la replicación geográfica activa para Azure SQL Database con Transact-SQL

En este artículo se muestra cómo configurar la replicación geográfica activa para una instancia de Azure SQL Database mediante Transact-SQL.

Para iniciar la conmutación por error mediante Transact-SQL, consulte [Inicio de una conmutación por error planeada o no planeada para Base de datos SQL de Azure con Transact-SQL](sql-database-geo-replication-failover-transact-sql.md).

> [!NOTE]
> Cuando usa la replicación geográfica activa (secundarias legibles) para la recuperación ante desastres, debe configurar un grupo de conmutación por error para todas las bases de datos dentro de una aplicación a fin de habilitar la conmutación por error automática y transparente. Esta característica se encuentra en su versión preliminar. Para obtener más información, consulte [Grupos de conmutación por error automática y replicación geográfica](sql-database-geo-replication-overview.md).
> 
> 

Para configurar la replicación geográfica activa mediante Transact-SQL, necesitará lo siguiente:

* Una suscripción de Azure
* Un servidor lógico de Azure SQL Database <MyLocalServer> y una base de datos SQL <MyDB>: la base de datos principal que quiere replicar.
* Uno o varios servidores lógicos de Azure SQL Database <MySecondaryServer(n)>: servidores lógicos que serán los servidores asociados en los que se crearán bases de datos secundarias.
* Un inicio de sesión que sea DBManager en el servidor principal
* Tener la propiedad db_ownership de la base de datos local que se va a replicar geográficamente
* Tener DBManager en los servidores del asociado en los que va a configurar la replicación geográfica
* La versión más reciente de SQL Server Management Studio (SSMS)

> [!IMPORTANT]
> Le recomendamos usar siempre la versión más reciente de Management Studio para que pueda estar siempre al día de las actualizaciones de Microsoft Azure y Base de datos SQL. [Actualice SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx).
> 
> 

## <a name="add-secondary-database"></a>Agregar una base de datos secundaria
Puede usar la instrucción **ALTER DATABASE** para crear una base de datos secundaria con replicación geográfica en un servidor asociado. Ejecute esta instrucción en la base de datos maestra del servidor que contiene la base de datos que se va a replicar. La base de datos con replicación geográfica (la "base de datos principal") tendrá el mismo nombre que la base de datos que se replica y, de forma predeterminada, tendrá el mismo nivel de servicio que la base de datos principal. La base de datos secundaria puede ser legible o no legible, y puede ser una base de datos única o de un grupo elástico. Para más información, vea [ALTER DATABASE (Transact-SQL)](https://msdn.microsoft.com/library/mt574871.aspx) y [Niveles de servicio](sql-database-service-tiers.md).
Después de crear e inicializar la base de datos secundaria, los datos comenzarán a replicarse de manera asincrónica desde la base de datos principal. Los pasos siguientes describen cómo configurar la replicación geográfica con Management Studio. Se proporcionan pasos para crear bases de datos secundarias legibles y no legibles como una base de datos única o de un grupo elástico.

> [!NOTE]
> Si existe una base de datos en el servidor asociado especificado con el mismo nombre que la base de datos principal, se producirá un error en el comando.
> 

### <a name="add-readable-secondary-single-database"></a>Incorporación de una base de datos secundaria legible (base de datos única)
Use los siguientes pasos para crear una base de datos secundaria legible como base de datos única.

1. En Management Studio, conéctese al servidor lógico de Base de datos SQL de Azure.
2. Abra la carpeta Bases de datos, expanda la carpeta **Bases de datos del sistema**, haga clic con el botón derecho en **maestra** y haga clic en **Nueva consulta**.
3. Use la instrucción **ALTER DATABASE** para convertir una base de datos local en una base de datos principal con replicación geográfica, que posea una base de datos secundaria legible en un servidor secundario.
   
        ALTER DATABASE <MyDB>
           ADD SECONDARY ON SERVER <MySecondaryServer2> WITH (ALLOW_CONNECTIONS = ALL);
4. Haga clic en **Ejecutar** para ejecutar la consulta.

### <a name="add-readable-secondary-elastic-pool"></a>Adición de una base de datos secundaria legible (grupo elástico)
Use los pasos siguientes para crear una base de datos secundaria legible en un grupo elástico.

1. En Management Studio, conéctese al servidor lógico de Base de datos SQL de Azure.
2. Abra la carpeta Bases de datos, expanda la carpeta **Bases de datos del sistema**, haga clic con el botón derecho en **maestra** y haga clic en **Nueva consulta**.
3. Use la instrucción **ALTER DATABASE** para convertir una base de datos local en una base de datos principal con replicación geográfica, que posea una base de datos secundaria legible en un servidor secundario de un grupo elástico.
   
        ALTER DATABASE <MyDB>
           ADD SECONDARY ON SERVER <MySecondaryServer4> WITH (ALLOW_CONNECTIONS = ALL
           , SERVICE_OBJECTIVE = ELASTIC_POOL (name = MyElasticPool2));
4. Haga clic en **Ejecutar** para ejecutar la consulta.

## <a name="remove-secondary-database"></a>Elimine una base de datos secundaria
Puede usar la instrucción **ALTER DATABASE** para terminar definitivamente la asociación de replicación entre una base de datos secundaria y su base de datos principal. Esta instrucción se ejecuta en la base de datos maestra en la que reside la base de datos principal. Después de terminarse la relación, la base de datos secundaria se convierte en una base de datos normal de lectura y escritura. Si se interrumpe la conectividad con la base de datos secundaria, el comando se ejecuta correctamente pero la base de datos secundaria será de lectura y escritura después de restaurarse la conectividad. Para más información, vea [ALTER DATABASE (Transact-SQL)](https://msdn.microsoft.com/library/mt574871.aspx) y [Niveles de servicio](sql-database-service-tiers.md).

Use los pasos siguientes para quitar la base de datos secundaria con replicación geográfica de una asociación de replicación geográfica.

1. En Management Studio, conéctese al servidor lógico de Base de datos SQL de Azure.
2. Abra la carpeta Bases de datos, expanda la carpeta **Bases de datos del sistema**, haga clic con el botón derecho en **maestra** y haga clic en **Nueva consulta**.
3. Use la instrucción **ALTER DATABASE** para quitar una base de datos secundaria con replicación geográfica.
   
        ALTER DATABASE <MyDB>
           REMOVE SECONDARY ON SERVER <MySecondaryServer1>;
4. Haga clic en **Ejecutar** para ejecutar la consulta.

## <a name="monitor-active-geo-replication-configuration-and-health"></a>Supervisión y mantenimiento de la configuración de la replicación geográfica activa

Las tareas de supervisión incluyen la supervisión de la configuración de replicación geográfica y la supervisión del mantenimiento de la replicación de los datos.  Puede usar la vista de administración dinámica **sys.dm_geo_replication_links** de la base de datos maestra para devolver información sobre todos los vínculos de replicación existentes de cada base de datos que se encuentre en el servidor lógico de Azure SQL Database. Esta vista contiene una fila para cada vínculo de replicación entre bases de datos principales y secundarias. Puede usar la vista de administración dinámica **sys.dm_replication_link_status**, para devolver una fila de cada Azure SQL Database que participe en un vínculo de replicación. Aquí se incluyen tanto las bases de datos principales como secundarias. Si existe más de un vínculo de replicación continua para una base de datos principal dada, esta tabla contiene una fila para cada una de las relaciones. La vista se crea en todas las bases de datos, incluida la maestra lógica. Sin embargo, al consultar esta vista en la maestra lógica se devuelve un conjunto vacío. Puede usar la vista de administración dinámica **sys.dm_operation_status** para mostrar el estado de todas las operaciones de bases de datos, incluido el estado de los vínculos de replicación. Para más información, consulte [sys.geo_replication_links (Azure SQL Database)](https://msdn.microsoft.com/library/mt575501.aspx), [sys.dm_geo_replication_link_status (Azure SQL Database)](https://msdn.microsoft.com/library/mt575504.aspx) y [sys.dm_operation_status (Azure SQL Database)](https://msdn.microsoft.com/library/dn270022.aspx).

Use los pasos siguientes para supervisar una asociación de replicación geográfica activa.

1. En Management Studio, conéctese al servidor lógico de Base de datos SQL de Azure.
2. Abra la carpeta Bases de datos, expanda la carpeta **Bases de datos del sistema**, haga clic con el botón derecho en **maestra** y haga clic en **Nueva consulta**.
3. Use la siguiente instrucción para mostrar todas las bases de datos con vínculos de replicación geográfica.
   
        SELECT database_id, start_date, modify_date, partner_server, partner_database, replication_state_desc, role, secondary_allow_connections_desc FROM sys.geo_replication_links;
4. Haga clic en **Ejecutar** para ejecutar la consulta.
5. Abra la carpeta Bases de datos, expanda la carpeta **Bases de datos del sistema**, haga clic con el botón derecho en **MyDB** y luego haga clic en **Nueva consulta**.
6. Use la siguiente instrucción para mostrar los retrasos de replicación y la hora de la última replicación de mis bases de datos secundarias de MyDB.
   
        SELECT link_guid, partner_server, last_replication, replication_lag_sec FROM sys.dm_geo_replication_link_status
7. Haga clic en **Ejecutar** para ejecutar la consulta.
8. Use la siguiente instrucción para mostrar las operaciones de replicación geográfica más recientes asociadas a la base de datos MyDB.
   
        SELECT * FROM sys.dm_operation_status where major_resource_id = 'MyDB'
        ORDER BY start_time DESC
9. Haga clic en **Ejecutar** para ejecutar la consulta.

## <a name="next-steps"></a>Pasos siguientes
* Para más información sobre los grupos de conmutación por error y replicación geográfica activa, consulte [Grupos de conmutación por error](sql-database-geo-replication-overview.md)
* Para obtener una descripción general y los escenarios de la continuidad empresarial, consulte [Información general sobre la continuidad empresarial](sql-database-business-continuity.md)


