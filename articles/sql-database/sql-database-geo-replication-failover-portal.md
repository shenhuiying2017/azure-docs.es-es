---
title: "Inicio de una conmutación por error planeada o no planeada para Azure SQL Database con Azure Portal | Microsoft Docs"
description: "Inicio de una conmutación por error planeada o no planeada para Base de datos SQL de Azure con el Portal de Azure"
services: sql-database
documentationcenter: 
author: anosov1960
manager: jhubbard
editor: 
ms.assetid: a9d184a4-09e0-4f41-b364-40425f68f430
ms.service: sql-database
ms.custom: business continuity; how to
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-management
ms.date: 11/22/2016
ms.author: sashan;carlrab
translationtype: Human Translation
ms.sourcegitcommit: 09c2332589b1170b411c6f45f4109fb8048887e2
ms.openlocfilehash: 7354f020d0a3a80775adf6d3b1e519ac6c24fdf6


---
# <a name="initiate-a-planned-or-unplanned-failover-for-azure-sql-database-with-the-azure-portal"></a>Inicio de una conmutación por error planeada o no planeada para Base de datos SQL de Azure con el Portal de Azure
> [!div class="op_single_selector"]
> * [Portal de Azure](sql-database-geo-replication-failover-portal.md)
> * [PowerShell](sql-database-geo-replication-failover-powershell.md)
> * [T-SQL](sql-database-geo-replication-failover-transact-sql.md)
> 
> 

En este artículo se muestra cómo iniciar la conmutación por error a una Base de datos SQL secundaria con el [Portal de Azure](http://portal.azure.com). Para configurar la replicación geográfica, consulte [Configuración de replicación geográfica para Base de datos SQL de Azure con el Portal de Azure](sql-database-geo-replication-portal.md).

## <a name="initiate-a-failover"></a>Inicio de una conmutación por error
La base de datos secundaria se puede cambiar para convertirse en la principal.  

1. En el [Portal de Azure](http://portal.azure.com) , vaya a la base de datos principal de la asociación de replicación geográfica.
2. En la hoja SQL Database, seleccione **All settings** (Toda la configuración)  > **Replicación geográfica**.
3. En la lista **SECUNDARIAS**, seleccione la base de datos que quiere convertir en la nueva base de datos principal y haga clic en **Conmutación por error**.
   
    ![Conmutación por error][2]
4. Haga clic en **Sí** para iniciar la conmutación por error.

El comando cambiará inmediatamente la base de datos al rol principal. 

Hay un breve período durante el que ambas bases de datos no están disponibles (del orden de 0 a 25 segundos) mientras se cambian los roles. Si la base de datos principal tiene varias bases de datos secundarias, el comando reconfigura automáticamente las demás secundarias para conectarse a la nueva principal. En circunstancias normales, toda la operación debería tardar menos de un minuto en completarse. 

> [!NOTE]
> Si la principal está conectada y confirmando transacciones cuando se emite el comando, es posible que produzca alguna pérdida de datos.
> 
> 

## <a name="next-steps"></a>Pasos siguientes
* Después de la conmutación por error, asegúrese de que los requisitos de autenticación para el servidor y la base de datos se configuran en el nuevo elemento principal. Para obtener más información, consulte [Administración de la seguridad de Base de datos SQL de Azure después de la recuperación ante desastres](sql-database-geo-replication-security-config.md).
* Para obtener información sobre cómo llevar a cabo tareas de recuperación después de desastres mediante la replicación geográfica activa, incluidos los pasos previos y posteriores, consulte [Obtención de detalles de la recuperación ante desastres](sql-database-disaster-recovery.md)
* Para ver una entrada de blog de Sasha Nosov sobre la replicación geográfica activa, consulte [Spotlight on new Geo-Replication capabilities](https://azure.microsoft.com/blog/spotlight-on-new-capabilities-of-azure-sql-database-geo-replication/)
* Para obtener información sobre cómo diseñar aplicaciones en la nube para usar la replicación geográfica activa, consulte [Diseño de una aplicación para la recuperación ante desastres en la nube mediante replicación geográfica activa en Base de datos SQL](sql-database-designing-cloud-solutions-for-disaster-recovery.md)
* Para saber cómo utilizar la replicación geográfica activa con los grupos de bases de datos elásticas, consulte [Estrategias de recuperación ante desastres para aplicaciones que usan el grupo elástico de Base de datos SQL](sql-database-disaster-recovery-strategies-for-applications-with-elastic-pool.md).
* Para ver una descripción general de la continuidad empresarial, consulte [Introducción a la continuidad empresarial con Base de datos SQL de Azure](sql-database-business-continuity.md)

<!--Image references-->
[1]: ./media/sql-database-geo-replication-failover-portal/failover.png
[2]: ./media/sql-database-geo-replication-failover-portal/secondaries.png



<!--HONumber=Dec16_HO1-->


