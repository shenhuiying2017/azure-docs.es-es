<properties
    pageTitle="Configuración de la replicación geográfica para Base de datos SQL de Azure con Transact-SQL | Microsoft Azure"
    description="Configuración de la replicación geográfica para Base de datos SQL de Azure con Transact-SQL"
    services="sql-database"
    documentationCenter=""
    authors="carlrabeler"
    manager="jhubbard"
    editor=""/>

<tags
    ms.service="sql-database"
    ms.devlang="NA"
    ms.topic="article"
    ms.tgt_pltfrm="NA"
    ms.workload="data-management"
    ms.date="04/27/2016"
    ms.author="carlrab"/>

# Configuración de la replicación geográfica para Base de datos SQL de Azure con Transact-SQL



> [AZURE.SELECTOR]
- [Portal de Azure](sql-database-geo-replication-portal.md)
- [PowerShell](sql-database-geo-replication-powershell.md)
- [Transact-SQL](sql-database-geo-replication-transact-sql.md)


En este artículo se muestra cómo configurar la replicación geográfica para una base de datos SQL de Azure mediante Transact-SQL.

Para iniciar la conmutación por error, vea [Inicio de una conmutación por error planeada o no planeada para Base de datos SQL de Azure](sql-database-geo-replication-failover-transact-sql.md).

>[AZURE.NOTE] La replicación geográfica activa (bases de datos secundarias legibles) está ahora disponible para todas las bases de datos en todos los niveles de servicio. En abril de 2017 se retirará el tipo secundario no legible y las bases de datos no legibles existentes se actualizarán automáticamente a secundarias legibles.

Puede configurar hasta cuatro bases de datos secundarias legibles en las mismas ubicaciones de centros de datos o en otras (regiones). Las bases de datos secundarias están disponibles en caso de una interrupción del centro de datos o de imposibilidad para conectarse a la base de datos principal.


Para configurar la replicación geográfica, necesita lo siguiente:

- Una suscripción de Azure: si no tiene una suscripción de Azure, simplemente haga clic en **EVALUACIÓN GRATUITA** en la parte superior de esta página y, a continuación, vuelva para terminar de leer este artículo.
- Un servidor lógico de Base de datos SQL de Azure <MyLocalServer> y una base de datos SQL <MyDB>: la base de datos principal que quiere replicar en una región geográfica diferente.
- Uno o varios servidores lógicos de Base de datos SQL de Azure <MySecondaryServer(n)>: servidores lógicos que serán los servidores asociados en los que se crearán bases de datos secundarias.
- Un inicio de sesión que es DBManager en la base de datos principal (tiene db\_ownership de la base de datos local que va a replicar geográficamente) y que es DBManager en los servidores asociados en los que se configurará la replicación geográfica.
- La versión más reciente de SQL Server Management Studio: para obtener la versión más reciente de SQL Server Management Studio (SSMS), vaya a [Descarga de SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx). Para obtener información sobre el uso de SQL Server Management Studio para administrar los servidores lógicos y las bases de datos de Base de datos SQL de Azure, consulte [Administración de Base de datos SQL de Azure con SQL Server Management Studio](sql-database-manage-azure-ssms.md)

## Agregar una base de datos secundaria

Puede usar la instrucción **ALTER DATABASE** para crear una base de datos secundaria con replicación geográfica en un servidor asociado. Ejecute esta instrucción en la base de datos maestra del servidor que contiene la base de datos que se va a replicar. La base de datos con replicación geográfica (la "base de datos principal") tendrá el mismo nombre que la base de datos que se replica y, de forma predeterminada, tendrá el mismo nivel de servicio que la base de datos principal. La base de datos secundaria puede ser legible o no legible, y puede ser una base de datos única o elástica. Para obtener más información, consulte [ALTER DATABASE (Transact-SQL)](https://msdn.microsoft.com/library/mt574871.aspx) y [Niveles de servicio](sql-database-service-tiers.md). Después de crear e inicializar la base de datos secundaria, los datos comenzarán a replicarse de manera asincrónica desde la base de datos principal. Los pasos siguientes describen cómo configurar la replicación geográfica con Management Studio. Se proporcionan pasos para crear bases de datos secundarias legibles y no legibles con una base de datos única o elástica.

> [AZURE.NOTE] Si existe una base de datos en el servidor asociado especificado con el mismo nombre que la base de datos principal, se producirá un error en el comando.


### Incorporación de una base de datos secundaria no legible (base de datos única)

Use los pasos siguientes para crear una base de datos secundaria no legible como base de datos única.

1. Use la versión 13.0.600.65 o posterior de SQL Server Management Studio.

 	 > [AZURE.IMPORTANT] Descargue la versión [más reciente](https://msdn.microsoft.com/library/mt238290.aspx) de SQL Server Management Studio. Le recomendamos usar siempre la versión más reciente de Management Studio para que pueda estar siempre al día de las últimas actualizaciones del Portal de Azure.


2. Abra la carpeta Bases de datos, expanda la carpeta **Bases de datos del sistema**, haga clic con el botón derecho en **maestra** y, a continuación, haga clic en **Nueva consulta**.

3. Use la siguiente instrucción **ALTER DATABASE** para convertir una base de datos local en una base de datos principal con replicación geográfica que tenga una base de datos secundaria no legible en un servidor MySecondaryServer1 donde MySecondaryServer1 sea el nombre descriptivo del servidor.

        ALTER DATABASE <MyDB>
           ADD SECONDARY ON SERVER <MySecondaryServer1> WITH (ALLOW_CONNECTIONS = NO);

4. Haga clic en **Ejecutar** para ejecutar la consulta.


### Incorporación de una base de datos secundaria legible (base de datos única)
Use los siguientes pasos para crear una base de datos secundaria legible como base de datos única.

1. En Management Studio, conéctese al servidor lógico de Base de datos SQL de Azure.

2. Abra la carpeta Bases de datos, expanda la carpeta **Bases de datos del sistema**, haga clic con el botón derecho en **maestra** y, a continuación, haga clic en **Nueva consulta**.

3. Use la instrucción **ALTER DATABASE** para convertir una base de datos local en una base de datos principal con replicación geográfica, que posea una base de datos secundaria legible en un servidor secundario.

        ALTER DATABASE <MyDB>
           ADD SECONDARY ON SERVER <MySecondaryServer2> WITH (ALLOW_CONNECTIONS = ALL);

4. Haga clic en **Ejecutar** para ejecutar la consulta.



### Agregue una base de datos secundaria no legible (base de datos elástica)

Use los pasos siguientes para crear una base de datos secundaria no legible como base de datos elástica.

1. En Management Studio, conéctese al servidor lógico de Base de datos SQL de Azure.

2. Abra la carpeta Bases de datos, expanda la carpeta **Bases de datos del sistema**, haga clic con el botón derecho en **maestra** y, a continuación, haga clic en **Nueva consulta**.

3. Use la instrucción **ALTER DATABASE** para convertir una base de datos local en una base de datos principal con replicación geográfica, que posea una base de datos secundaria no legible en un servidor secundario de un grupo elástico.

        ALTER DATABASE <MyDB>
           ADD SECONDARY ON SERVER <MySecondaryServer3> WITH (ALLOW_CONNECTIONS = NO
           , SERVICE_OBJECTIVE = ELASTIC_POOL (name = MyElasticPool1));

4. Haga clic en **Ejecutar** para ejecutar la consulta.



### Agregue de una base de datos secundaria legible (base de datos elástica)
Use los siguientes pasos para crear una base de datos secundaria legible como base de datos elástica.

1. En Management Studio, conéctese al servidor lógico de Base de datos SQL de Azure.

2. Abra la carpeta Bases de datos, expanda la carpeta **Bases de datos del sistema**, haga clic con el botón derecho en **maestra** y, a continuación, haga clic en **Nueva consulta**.

3. Use la instrucción **ALTER DATABASE** para convertir una base de datos local en una base de datos principal con replicación geográfica, que posea una base de datos secundaria legible en un servidor secundario de un grupo elástico.

        ALTER DATABASE <MyDB>
           ADD SECONDARY ON SERVER <MySecondaryServer4> WITH (ALLOW_CONNECTIONS = ALL
           , SERVICE_OBJECTIVE = ELASTIC_POOL (name = MyElasticPool2));

4. Haga clic en **Ejecutar** para ejecutar la consulta.



## Elimine una base de datos secundaria

Puede usar la instrucción **ALTER DATABASE** para terminar definitivamente la asociación de replicación entre una base de datos secundaria y su base de datos principal. Esta instrucción se ejecuta en la base de datos maestra en la que reside la base de datos principal. Después de terminarse la relación, la base de datos secundaria se convierte en una base de datos normal de lectura y escritura. Si se interrumpe la conectividad con la base de datos secundaria, el comando se ejecuta correctamente pero la base de datos secundaria será de lectura y escritura después de restaurarse la conectividad. Para obtener más información, consulte [ALTER DATABASE (Transact-SQL)](https://msdn.microsoft.com/library/mt574871.aspx) y [Niveles de servicio](sql-database-service-tiers.md).

Use los pasos siguientes para quitar la base de datos secundaria con replicación geográfica de una asociación de replicación geográfica.

1. En Management Studio, conéctese al servidor lógico de Base de datos SQL de Azure.

2. Abra la carpeta Bases de datos, expanda la carpeta **Bases de datos del sistema**, haga clic con el botón derecho en **maestra** y, a continuación, haga clic en **Nueva consulta**.

3. Use la instrucción **ALTER DATABASE** para quitar una base de datos secundaria con replicación geográfica.

        ALTER DATABASE <MyDB>
           REMOVE SECONDARY ON SERVER <MySecondaryServer1>;

4. Haga clic en **Ejecutar** para ejecutar la consulta.

## Supervisión y mantenimiento de la configuración de la replicación geográfica

Las tareas de supervisión incluyen la supervisión de la configuración de replicación geográfica y la supervisión del mantenimiento de la replicación de los datos. Puede usar la vista de administración dinámica **sys.dm\_geo\_replication\_links** de la base de datos maestra, para devolver información sobre todos los vínculos de replicación existentes de cada base de datos que se encuentre en el servidor lógico de la Base de datos SQL de Azure. Esta vista contiene una fila para cada vínculo de replicación entre bases de datos principales y secundarias. Puede usar la vista de administración dinámica **sys.dm\_replication\_status**, para devolver una fila de cada Base de datos SQL de Azure que participe en un vínculo de replicación. Aquí se incluyen tanto las bases de datos principales como secundarias. Si existe más de un vínculo de replicación continua para una base de datos principal dada, esta tabla contiene una fila para cada una de las relaciones. La vista se crea en todas las bases de datos, incluida la maestra lógica. Sin embargo, al consultar esta vista en la maestra lógica se devuelve un conjunto vacío. Puede usar la vista de administración dinámica **sys.dm\_operation\_status**, para mostrar el estado de todas las operaciones de bases de datos, incluido el estado de los vínculos de replicación. Para obtener más información, consulte [sys.geo\_replication\_links (Base de datos SQL de Azure)](https://msdn.microsoft.com/library/mt575501.aspx), [sys.dm\_geo\_replication\_link\_status (Base de datos SQL de Azure)](https://msdn.microsoft.com/library/mt575504.aspx) y [sys.dm\_operation\_status (Base de datos SQL de Azure)](https://msdn.microsoft.com/library/dn270022.aspx).

Use los pasos siguientes para supervisar una asociación de replicación geográfica.

1. En Management Studio, conéctese al servidor lógico de Base de datos SQL de Azure.

2. Abra la carpeta Bases de datos, expanda la carpeta **Bases de datos del sistema**, haga clic con el botón derecho en **maestra** y, a continuación, haga clic en **Nueva consulta**.

3. Use la siguiente instrucción para mostrar todas las bases de datos con vínculos de replicación geográfica.

        SELECT database_id, start_date, modify_date, partner_server, partner_database, replication_state_desc, role, secondary_allow_connections_desc FROM [sys].geo_replication_links;

4. Haga clic en **Ejecutar** para ejecutar la consulta.
5. Abra la carpeta Bases de datos, expanda la carpeta **Bases de datos del sistema**, haga clic con el botón derecho en **MyDB** y, a continuación, haga clic en **Nueva consulta**.
6. Use la siguiente instrucción para mostrar los retrasos de replicación y la hora de la última replicación de mis bases de datos secundarias de MyDB.

        SELECT link_guid, partner_server, last_replication, replication_lag_sec FROM sys.dm_geo_replication_link_status

7. Haga clic en **Ejecutar** para ejecutar la consulta.
8. Use la siguiente instrucción para mostrar las operaciones de replicación geográfica más recientes asociadas a la base de datos MyDB.

        SELECT * FROM sys.dm_operation_status where major_resource_id = 'MyDB'
        ORDER BY start_time DESC

9. Haga clic en **Ejecutar** para ejecutar la consulta.



## Pasos siguientes

- [Inicio de una conmutación por error planeada o no planeada para Base de datos SQL de Azure](sql-database-geo-replication-failover-transact-sql.md)
- [Obtención de detalles de la recuperación ante desastres](sql-database-disaster-recovery-drills.md)


## Recursos adicionales

- [Configuración de seguridad para Replicación geográfica activa o estándar](sql-database-geo-replication-security-config.md)
- [Spotlight on new Geo-Replication capabilities](https://azure.microsoft.com/blog/spotlight-on-new-capabilities-of-azure-sql-database-geo-replication/) (Lo más destacado de las nuevas funcionalidades de replicación geográfica)
- [P+F de BCDR de Base de datos SQL](sql-database-bcdr-faq.md)
- [Información general acerca de la continuidad del negocio](sql-database-business-continuity.md)
- [Replicación geográfica activa](sql-database-geo-replication-overview.md)
- [Diseño de aplicaciones para la recuperación ante desastres en la nube](sql-database-designing-cloud-solutions-for-disaster-recovery.md)
- [Finalización de una base de datos SQL de Azure recuperada](sql-database-recovered-finalize.md)

<!---HONumber=AcomDC_0608_2016-->