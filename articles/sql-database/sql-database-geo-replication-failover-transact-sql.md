---
title: "TSQL: inicio de una conmutación por error para Azure SQL Database | Microsoft Docs"
description: "Inicio de una conmutación por error planeada o no planeada para Base de datos SQL de Azure con Transact-SQL"
services: sql-database
documentationcenter: 
author: CarlRabeler
manager: jhubbard
editor: 
ms.assetid: 5eb2d256-025d-4f5a-99d4-17f702b37f14
ms.service: sql-database
ms.custom: business continuity
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-management
ms.date: 01/10/2017
ms.author: carlrab
ms.translationtype: Human Translation
ms.sourcegitcommit: 6adaf7026d455210db4d7ce6e7111d13c2b75374
ms.openlocfilehash: 459941d2c82e5d4ef62beab4ccf775ab8f5efce4
ms.contentlocale: es-es
ms.lasthandoff: 06/22/2017


---
# <a name="initiate-a-planned-or-unplanned-failover-for-azure-sql-database-with-transact-sql"></a>Inicio de una conmutación por error planeada o no planeada para Base de datos SQL de Azure con Transact-SQL

En este artículo se muestra cómo iniciar la conmutación por error a una Base de datos SQL secundaria con Transact-SQL. Para configurar la replicación geográfica, consulte el artículo de [configuración de replicación geográfica para Azure SQL Database con Azure Portal](sql-database-geo-replication-transact-sql.md).

Para iniciar la conmutación por error, necesitará lo siguiente:

* Un inicio de sesión que sea DBManager en el servidor principal
* Tener la propiedad db_ownership de la base de datos local que se va a replicar geográficamente
* Tener DBManager en los servidores del asociado en los que va a configurar la replicación geográfica
* La versión más reciente de SQL Server Management Studio (SSMS).

> [!IMPORTANT]
> Le recomendamos usar siempre la versión más reciente de Management Studio para que pueda estar siempre al día de las actualizaciones de Microsoft Azure y Base de datos SQL. [Actualice SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx).
>  

## <a name="initiate-a-planned-failover-promoting-a-secondary-database-to-become-the-new-primary"></a>Inicio de una conmutación por error planeada que promueve una base de datos secundaria a la nueva principal
Puede usar la instrucción **ALTER DATABASE** para promover una base de datos secundaria para que sea la nueva base de datos principal de forma planeada; de ese modo, se degrada la base de datos principal ya existente y se convierte en secundaria. Esta instrucción se ejecuta en la base de datos maestra en el servidor lógico de Base de datos SQL Azure en el que reside la base de datos secundaria con replicación geográfica que se está promocionando. Esta funcionalidad está diseñada para la conmutación por error planeada, por ejemplo, durante las exploraciones de DR, y requiere que esté disponible la base de datos principal.

El comando ejecuta el siguiente flujo de trabajo:

1. Cambia temporalmente la replicación al modo sincrónico, lo que hace que todas las transacciones pendientes se vacíen en la secundaria y se bloqueen todas las nuevas transacciones.
2. Cambia los roles de las dos bases de datos de la asociación de replicación geográfica.  

Esta secuencia garantiza que las dos bases de datos se sincronizan antes del cambio de roles y, por tanto, no se producirá ninguna pérdida de datos. Hay un breve período durante el que ambas bases de datos no están disponibles (del orden de 0 a 25 segundos) mientras se cambian los roles. Si la base de datos principal tiene varias bases de datos secundarias, el comando reconfigura automáticamente las demás secundarias para conectarse a la nueva principal.  En circunstancias normales, toda la operación debería tardar menos de un minuto en completarse. Para más información, consulte [ALTER DATABASE (Transact-SQL)](https://msdn.microsoft.com/library/mt574871.aspx) y [Niveles de servicio](sql-database-service-tiers.md).

Use los pasos siguientes para iniciar una conmutación por error planeada.

1. En Management Studio, conéctese al servidor lógico de Base de datos SQL de Azure en el que reside una base de datos secundaria con replicación geográfica.
2. Abra la carpeta Bases de datos, expanda la carpeta **Bases de datos del sistema**, haga clic con el botón derecho en **maestra** y, a continuación, haga clic en **Nueva consulta**.
3. Use la siguiente instrucción **ALTER DATABASE** para cambiar la base de datos secundaria al rol principal.
   
        ALTER DATABASE <MyDB> FAILOVER;
4. Haga clic en **Ejecutar** para ejecutar la consulta.

> [!NOTE]
> En contadas ocasiones, es posible que la operación no pueda completarse y que aparezca detenida. En este caso, el usuario puede ejecutar el comando para forzar la conmutación por error y aceptar la pérdida de datos.
> 
> 

## <a name="initiate-an-unplanned-failover-from-the-primary-database-to-the-secondary-database"></a>Inicio de una conmutación por error no planeada desde la base de datos principal a la base de datos secundaria
Puede usar la instrucción **ALTER DATABASE** para promover una base de datos secundaria para que sea la nueva base de datos principal de forma no planeada, y así forzar la degradación de la base de datos principal ya existente a una base de datos secundaria, en un momento en el que la base de datos principal ya no esté disponible. Esta instrucción se ejecuta en la base de datos maestra en el servidor lógico de Base de datos SQL Azure en el que reside la base de datos secundaria con replicación geográfica que se está promocionando.

Esta funcionalidad está diseñada para situaciones de recuperación ante desastres en los que resulta fundamental restaurar la disponibilidad de la base de datos y la pérdida de algunos datos resulta aceptable. Cuando se invoca la conmutación por error forzada, la base de datos secundaria especificada se convierte inmediatamente en la base de datos principal y comienza a aceptar transacciones de escritura. Tan pronto como la base de datos principal original puede volver a conectar con esta nueva base de datos principal, se realiza una copia de seguridad incremental en la base de datos principal original y la base de datos principal anterior se convierte en una base de datos secundaria para la nueva base de datos principal; por consiguiente, es simplemente una réplica de sincronización de la nueva principal.

Sin embargo, como la restauración a un momento dado no se admite en las bases de datos secundarias, si el usuario desea recuperar datos comprometidos en la base de datos principal anterior que no se habían replicado en la nueva base de datos principal antes de que se produjera la conmutación por error forzosa, el usuario deberá acudir al soporte técnico para recuperar estos datos perdidos.

Si la base de datos principal tiene varias bases de datos secundarias, el comando reconfigura automáticamente las demás secundarias para conectarse a la nueva principal.

Use los pasos siguientes para iniciar una conmutación por error no planeada.

1. En Management Studio, conéctese al servidor lógico de Base de datos SQL de Azure en el que reside una base de datos secundaria con replicación geográfica.
2. Abra la carpeta Bases de datos, expanda la carpeta **Bases de datos del sistema**, haga clic con el botón derecho en **maestra** y, a continuación, haga clic en **Nueva consulta**.
3. Use la siguiente instrucción **ALTER DATABASE** para cambiar la base de datos secundaria al rol principal.
   
        ALTER DATABASE <MyDB>   FORCE_FAILOVER_ALLOW_DATA_LOSS;
4. Haga clic en **Ejecutar** para ejecutar la consulta.

> [!NOTE]
> Si el comando se emite cuando las bases de datos principal y secundaria están en línea, la principal anterior se convertirá inmediatamente en la nueva secundaria sin sincronización de datos. Si la principal está confirmando transacciones cuando se emite el comando, es posible que produzca alguna pérdida de datos.
> 
> 

## <a name="next-steps"></a>Pasos siguientes
* Después de la conmutación por error, asegúrese de que los requisitos de autenticación para el servidor y la base de datos se configuran en el nuevo elemento principal. Para obtener más información, consulte [Administración de la seguridad de Base de datos SQL de Azure después de la recuperación ante desastres](sql-database-geo-replication-security-config.md).
* Para obtener información sobre cómo llevar a cabo tareas de recuperación después de desastres mediante la replicación geográfica activa, incluidos los pasos previos y posteriores a la recuperación, consulte [Recuperación ante desastres](sql-database-disaster-recovery.md)
* Para ver una entrada de blog de Sasha Nosov sobre la replicación geográfica activa, consulte [Spotlight on new Geo-Replication capabilities](https://azure.microsoft.com/blog/spotlight-on-new-capabilities-of-azure-sql-database-geo-replication/)
* Para obtener información sobre cómo diseñar aplicaciones en la nube para usar la replicación geográfica activa, consulte [Diseño de una aplicación para la recuperación ante desastres en la nube mediante replicación geográfica activa en Base de datos SQL](sql-database-designing-cloud-solutions-for-disaster-recovery.md)
* Para saber cómo utilizar la replicación geográfica activa con los grupos elásticos, consulte [Estrategias de recuperación ante desastres para grupos elásticos](sql-database-disaster-recovery-strategies-for-applications-with-elastic-pool.md).
* Para ver una descripción general de la continuidad empresarial, consulte el artículo de [introducción a la continuidad empresarial con Azure SQL Database](sql-database-business-continuity.md).


