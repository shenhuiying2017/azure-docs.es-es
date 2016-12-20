---
title: "Configuración de la replicación geográfica para Azure SQL Database con Transact-SQL | Microsoft Docs"
description: "Configuración de la replicación geográfica para Base de datos SQL de Azure con Transact-SQL"
services: sql-database
documentationcenter: 
author: CarlRabeler
manager: jhubbard
editor: 
ms.assetid: d94d89a6-3234-46c5-8279-5eb8daad10ac
ms.service: sql-database
ms.custom: business continuity; how to
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 10/13/2016
ms.author: carlrab
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: 702a01f7ea56e8af6286149bcb42590294cb618b


---
# <a name="configure-geo-replication-for-azure-sql-database-with-transact-sql"></a>Configuración de la replicación geográfica para Base de datos SQL de Azure con Transact-SQL
> [!div class="op_single_selector"]
> * [Información general](sql-database-geo-replication-overview.md)
> * [Portal de Azure](sql-database-geo-replication-portal.md)
> * [PowerShell](sql-database-geo-replication-powershell.md)
> * [T-SQL](sql-database-geo-replication-transact-sql.md)
> 
> 

En este artículo se muestra cómo configurar la replicación geográfica activa para una instancia de Base de datos SQL de Azure mediante Transact-SQL.

Para iniciar la conmutación por error mediante Transact-SQL, consulte [Inicio de una conmutación por error planeada o no planeada para Base de datos SQL de Azure con Transact-SQL](sql-database-geo-replication-failover-transact-sql.md).

> [!NOTE]
> La replicación geográfica activa (bases de datos secundarias legibles) está ahora disponible para todas las bases de datos en todos los niveles de servicio. En abril de 2017 se retirará el tipo secundario no legible y las bases de datos no legibles existentes se actualizarán automáticamente a secundarias legibles.
> 
> 

Para configurar la replicación geográfica activa mediante Transact-SQL, necesitará lo siguiente:

* Una suscripción de Azure.
* Un servidor lógico de Azure SQL Database <MyLocalServer> y una base de datos SQL <MyDB>: la base de datos principal que quiere replicar.
* Uno o varios servidores lógicos de Base de datos SQL de Azure <MySecondaryServer(n)>: servidores lógicos que serán los servidores asociados en los que se crearán bases de datos secundarias.
* Un inicio de sesión que es DBManager en la base de datos principal (tiene db_ownership de la base de datos local que va a replicar geográficamente) y que es DBManager en los servidores asociados en los que se configurará la replicación geográfica.
* SQL Server Management Studio (SSMS)

> [!IMPORTANT]
> Le recomendamos usar siempre la versión más reciente de Management Studio para que pueda estar siempre al día de las actualizaciones de Microsoft Azure y Base de datos SQL. [Actualice SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx).
> 
> 

## <a name="add-secondary-database"></a>Agregar una base de datos secundaria
Puede usar la instrucción **ALTER DATABASE** para crear una base de datos secundaria con replicación geográfica en un servidor asociado. Ejecute esta instrucción en la base de datos maestra del servidor que contiene la base de datos que se va a replicar. La base de datos con replicación geográfica (la "base de datos principal") tendrá el mismo nombre que la base de datos que se replica y, de forma predeterminada, tendrá el mismo nivel de servicio que la base de datos principal. La base de datos secundaria puede ser legible o no legible, y puede ser una base de datos única o elástica. Para más información, vea [ALTER DATABASE (Transact-SQL)](https://msdn.microsoft.com/library/mt574871.aspx) y [Niveles de servicio](sql-database-service-tiers.md).
Después de crear e inicializar la base de datos secundaria, los datos comenzarán a replicarse de manera asincrónica desde la base de datos principal. Los pasos siguientes describen cómo configurar la replicación geográfica con Management Studio. Se proporcionan pasos para crear bases de datos secundarias legibles y no legibles con una base de datos única o elástica.

> [!NOTE]
> Si existe una base de datos en el servidor asociado especificado con el mismo nombre que la base de datos principal, se producirá un error en el comando.
> 
> 

### <a name="add-non-readable-secondary-single-database"></a>Incorporación de una base de datos secundaria no legible (base de datos única)
Use los pasos siguientes para crear una base de datos secundaria no legible como base de datos única.

1. Use la versión 13.0.600.65 o posterior de SQL Server Management Studio.
   
   > [!IMPORTANT]
   > Descargue la versión [más reciente](https://msdn.microsoft.com/library/mt238290.aspx) de SQL Server Management Studio. Le recomendamos usar siempre la versión más reciente de Management Studio para que pueda estar siempre al día de las últimas actualizaciones del Portal de Azure.
   > 
   > 
2. Abra la carpeta Bases de datos, expanda la carpeta **Bases de datos del sistema**, haga clic con el botón derecho en **maestra** y haga clic en **Nueva consulta**.
3. Use la siguiente instrucción **ALTER DATABASE** para convertir una base de datos local en una base de datos principal con replicación geográfica que tenga una base de datos secundaria no legible en un servidor MySecondaryServer1 donde MySecondaryServer1 sea el nombre descriptivo del servidor.
   
        ALTER DATABASE <MyDB>
           ADD SECONDARY ON SERVER <MySecondaryServer1> WITH (ALLOW_CONNECTIONS = NO);
4. Haga clic en **Ejecutar** para ejecutar la consulta.

### <a name="add-readable-secondary-single-database"></a>Incorporación de una base de datos secundaria legible (base de datos única)
Use los siguientes pasos para crear una base de datos secundaria legible como base de datos única.

1. En Management Studio, conéctese al servidor lógico de Base de datos SQL de Azure.
2. Abra la carpeta Bases de datos, expanda la carpeta **Bases de datos del sistema**, haga clic con el botón derecho en **maestra** y haga clic en **Nueva consulta**.
3. Use la instrucción **ALTER DATABASE** para convertir una base de datos local en una base de datos principal con replicación geográfica, que posea una base de datos secundaria legible en un servidor secundario.
   
        ALTER DATABASE <MyDB>
           ADD SECONDARY ON SERVER <MySecondaryServer2> WITH (ALLOW_CONNECTIONS = ALL);
4. Haga clic en **Ejecutar** para ejecutar la consulta.

### <a name="add-non-readable-secondary-elastic-database"></a>Agregue una base de datos secundaria no legible (base de datos elástica)
Use los pasos siguientes para crear una base de datos secundaria no legible como base de datos elástica.

1. En Management Studio, conéctese al servidor lógico de Base de datos SQL de Azure.
2. Abra la carpeta Bases de datos, expanda la carpeta **Bases de datos del sistema**, haga clic con el botón derecho en **maestra** y haga clic en **Nueva consulta**.
3. Use la instrucción **ALTER DATABASE** para convertir una base de datos local en una base de datos principal con replicación geográfica, que posea una base de datos secundaria no legible en un servidor secundario de un grupo elástico.
   
        ALTER DATABASE <MyDB>
           ADD SECONDARY ON SERVER <MySecondaryServer3> WITH (ALLOW_CONNECTIONS = NO
           , SERVICE_OBJECTIVE = ELASTIC_POOL (name = MyElasticPool1));
4. Haga clic en **Ejecutar** para ejecutar la consulta.

### <a name="add-readable-secondary-elastic-database"></a>Agregue de una base de datos secundaria legible (base de datos elástica)
Use los siguientes pasos para crear una base de datos secundaria legible como base de datos elástica.

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

## <a name="monitor-geo-replication-configuration-and-health"></a>Supervisión y mantenimiento de la configuración de la replicación geográfica
Las tareas de supervisión incluyen la supervisión de la configuración de replicación geográfica y la supervisión del mantenimiento de la replicación de los datos.  Puede usar la vista de administración dinámica **sys.dm_geo_replication_links** de la base de datos maestra para devolver información sobre todos los vínculos de replicación existentes de cada base de datos que se encuentre en el servidor lógico de Azure SQL Database. Esta vista contiene una fila para cada vínculo de replicación entre bases de datos principales y secundarias. Puede usar la vista de administración dinámica **sys.dm_replication_link_status**, para devolver una fila de cada Azure SQL Database que participe en un vínculo de replicación. Aquí se incluyen tanto las bases de datos principales como secundarias. Si existe más de un vínculo de replicación continua para una base de datos principal dada, esta tabla contiene una fila para cada una de las relaciones. La vista se crea en todas las bases de datos, incluida la maestra lógica. Sin embargo, al consultar esta vista en la maestra lógica se devuelve un conjunto vacío. Puede usar la vista de administración dinámica **sys.dm_operation_status** para mostrar el estado de todas las operaciones de bases de datos, incluido el estado de los vínculos de replicación. Para más información, consulte [sys.geo_replication_links (Azure SQL Database)](https://msdn.microsoft.com/library/mt575501.aspx), [sys.dm_geo_replication_link_status (Azure SQL Database)](https://msdn.microsoft.com/library/mt575504.aspx) y [sys.dm_operation_status (Azure SQL Database)](https://msdn.microsoft.com/library/dn270022.aspx).

Use los pasos siguientes para supervisar una asociación de replicación geográfica.

1. En Management Studio, conéctese al servidor lógico de Base de datos SQL de Azure.
2. Abra la carpeta Bases de datos, expanda la carpeta **Bases de datos del sistema**, haga clic con el botón derecho en **maestra** y haga clic en **Nueva consulta**.
3. Use la siguiente instrucción para mostrar todas las bases de datos con vínculos de replicación geográfica.
   
        SELECT database_id, start_date, modify_date, partner_server, partner_database, replication_state_desc, role, secondary_allow_connections_desc FROM [sys].geo_replication_links;
4. Haga clic en **Ejecutar** para ejecutar la consulta.
5. Abra la carpeta Bases de datos, expanda la carpeta **Bases de datos del sistema**, haga clic con el botón derecho en **MyDB** y luego haga clic en **Nueva consulta**.
6. Use la siguiente instrucción para mostrar los retrasos de replicación y la hora de la última replicación de mis bases de datos secundarias de MyDB.
   
        SELECT link_guid, partner_server, last_replication, replication_lag_sec FROM sys.dm_geo_replication_link_status
7. Haga clic en **Ejecutar** para ejecutar la consulta.
8. Use la siguiente instrucción para mostrar las operaciones de replicación geográfica más recientes asociadas a la base de datos MyDB.
   
        SELECT * FROM sys.dm_operation_status where major_resource_id = 'MyDB'
        ORDER BY start_time DESC
9. Haga clic en **Ejecutar** para ejecutar la consulta.

## <a name="upgrade-a-non-readable-secondary-to-readable"></a>Actualización de una base de datos secundaria no legible a legible
En abril de 2017 se retirará el tipo secundario no legible y las bases de datos no legibles existentes se actualizarán automáticamente a secundarias legibles. Si usa bases de datos secundarias no legibles en la actualidad y desea actualizarlas para que sean legibles, puede utilizar los siguientes pasos sencillos para cada base de datos secundaria.

> [!IMPORTANT]
> No hay ningún método de autoservicio de actualización local de una base de datos secundaria no legible a legible. Si quita su única base de datos secundaria, la base de datos principal permanecerá desprotegida a menos que la nueva base de datos secundaria se sincronice por completo. Si el Acuerdo de Nivel de Servicio de la aplicación requiere que la base de datos principal esté siempre protegida, debe considerar la posibilidad de crear una base de datos secundaria en paralelo en otro servidor antes de aplicar los pasos de actualización anteriores. Tenga en cuenta que cada base de datos principal puede tener hasta cuatro bases de datos secundarias.
> 
> 

1. Primero, conéctese al servidor *secundario* y quite la base de datos secundaria no legible:  
   
        DROP DATABASE <MyNonReadableSecondaryDB>;
2. A continuación, conéctese al servidor *principal* y agregue una nueva base de datos secundaria legible
   
        ALTER DATABASE <MyDB>
            ADD SECONDARY ON SERVER <MySecondaryServer> WITH (ALLOW_CONNECTIONS = ALL);

## <a name="next-steps"></a>Pasos siguientes
* Para más información sobre la replicación geográfica activa, consulte [Replicación geográfica activa](sql-database-geo-replication-overview.md)
* Para obtener una descripción general y los escenarios de la continuidad empresarial, consulte [Información general sobre la continuidad empresarial](sql-database-business-continuity.md)




<!--HONumber=Nov16_HO3-->


