<properties 
    pageTitle="Inicio de una conmutación por error planeada o no planeada para Base de datos SQL de Azure con el Portal de Azure | Microsoft Azure" 
    description="Inicio de una conmutación por error planeada o no planeada para Base de datos SQL de Azure con el Portal de Azure" 
    services="sql-database" 
    documentationCenter="" 
    authors="stevestein" 
    manager="jhubbard" 
    editor=""/>

<tags
    ms.service="sql-database"
    ms.devlang="NA"
    ms.topic="article"
    ms.tgt_pltfrm="NA"
    ms.workload="data-management" 
    ms.date="04/27/2016"
    ms.author="sstein"/>

# Inicio de una conmutación por error planeada o no planeada para Base de datos SQL de Azure con el Portal de Azure


> [AZURE.SELECTOR]
- [Portal de Azure](sql-database-geo-replication-failover-portal.md)
- [PowerShell](sql-database-geo-replication-failover-powershell.md)
- [Transact-SQL](sql-database-geo-replication-failover-transact-sql.md)


En este artículo se muestra cómo iniciar la conmutación por error a una Base de datos SQL secundaria con el [Portal de Azure](http://portal.azure.com). Para configurar la replicación geográfica, consulte [Configuración de replicación geográfica para Base de datos SQL de Azure con el Portal de Azure](sql-database-geo-replication-portal.md).


## Inicio de una conmutación por error

La base de datos secundaria se puede cambiar para convertirse en la principal.

1. En el [Portal de Azure](http://portal.azure.com), vaya a la base de datos principal de la asociación de replicación geográfica.
2. En la hoja Base de datos SQL, seleccione **Toda la configuración** > **Replicación geográfica**.
3. En la lista **SECUNDARIAS**, seleccione la base de datos que quiere convertir en la nueva base de datos principal y haga clic en **Conmutación por error**.

    ![conmutación por error][2]

4. Haga clic en **Sí** para iniciar la conmutación por error.

El comando cambiará inmediatamente la base de datos al rol principal.

Hay un breve período durante el que ambas bases de datos no están disponibles (del orden de 0 a 25 segundos) mientras se cambian los roles. Si la base de datos principal tiene varias bases de datos secundarias, el comando reconfigura automáticamente las demás secundarias para conectarse a la nueva principal. En circunstancias normales, toda la operación debería tardar menos de un minuto en completarse.

>[AZURE.NOTE] Si la principal está conectada y confirmando transacciones cuando se emite el comando, es posible que produzca alguna pérdida de datos.


## Recursos adicionales   


- [Obtención de detalles de la recuperación ante desastres](sql-database-disaster-recovery-drills.md)
- [Spotlight on new Geo-Replication capabilities](https://azure.microsoft.com/blog/spotlight-on-new-capabilities-of-azure-sql-database-geo-replication/) (Lo más destacado de las nuevas funcionalidades de replicación geográfica)
- [Diseño de aplicaciones de nube para la continuidad de negocio mediante replicación geográfica](sql-database-designing-cloud-solutions-for-disaster-recovery.md)
- [Información general acerca de la continuidad del negocio](sql-database-business-continuity.md)
- [Documentación de la base de datos SQL](https://azure.microsoft.com/documentation/services/sql-database/)


<!--Image references-->
[1]: ./media/sql-database-geo-replication-failover-portal/failover.png
[2]: ./media/sql-database-geo-replication-failover-portal/secondaries.png

<!---HONumber=AcomDC_0608_2016-->