---
title: "SSMS: Migración de una base de datos de SQL Server a Azure SQL Database | Microsoft Docs"
description: "En este artículo se muestra cómo usar el Asistente para implementar bases de datos en Base de datos de Microsoft Azure en SQL Server Management Studio para migrar una base de datos de SQL Server compatible directamente al servidor de Azure SQL Database."
keywords: "Base de datos SQL de Microsoft Azure, migración de bases de datos, Asistente para bases de datos de Microsoft Azure"
services: sql-database
documentationcenter: 
author: CarlRabeler
manager: jhubbard
editor: 
ms.assetid: 91d3fd5e-d035-4c55-b1ea-a7ccc8e0f543
ms.service: sql-database
ms.custom: migrate and move
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: sqldb-migrate
ms.date: 08/24/2016
ms.author: carlrab
translationtype: Human Translation
ms.sourcegitcommit: 86bc7d89bb5725add8ba05b6f0978467147fd3ca
ms.openlocfilehash: f4cbfc1a277cbe80edbfbb4dc651adb1972f41b4


---
# <a name="migrate-sql-server-database-to-sql-database-using-deploy-database-to-microsoft-azure-database-wizard"></a>Migrar la base de datos de SQL Server a Base de datos SQL con el Asistente para implementar base de datos en Base de datos de Microsoft Azure
> [!div class="op_single_selector"]
> * [Asistente para migración de SSMS](sql-database-cloud-migrate-compatible-using-ssms-migration-wizard.md)
> * [Exportación a un archivo BACPAC](sql-database-cloud-migrate-compatible-export-bacpac-ssms.md)
> * [Importación desde un archivo BACPAC](sql-database-cloud-migrate-compatible-import-bacpac-ssms.md)
> * [Replicación transaccional](sql-database-cloud-migrate-compatible-using-transactional-replication.md)
> 
> 

En este artículo se muestra cómo usar el Asistente para implementar bases de datos en Base de datos de Microsoft Azure en SQL Server Management Studio para migrar una [base de datos de SQL Server compatible](sql-database-cloud-migrate.md) directamente al servidor de Azure SQL Database.

## <a name="use-the-deploy-database-to-microsoft-azure-database-wizard"></a>Uso del Asistente de implementación de bases de datos en la Base de datos de Microsoft Azure
> [!NOTE]
> En los pasos siguientes se supone que tiene un [servidor de Base de datos SQL aprovisionado](https://azure.microsoft.com/documentation/learning-paths/sql-database-training-learn-sql-database/).
> 
> 

1. Compruebe que dispone de la versión más reciente de SQL Server Management Studio. Las nuevas versiones de Management Studio se actualizan mensualmente a fin de que sigan sincronizadas con las actualizaciones para el Portal de Azure.
   
   > [!IMPORTANT]
   > Le recomendamos usar siempre la versión más reciente de Management Studio para que pueda estar siempre al día de las actualizaciones de Microsoft Azure y Base de datos SQL. [Actualice SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx).
   > 
   > 
2. Abra Management Studio y conéctese a la base de datos de SQL Server que se va a migrar en el Explorador de objetos.
3. Haga clic con el botón derecho en la base de datos del Explorador de objetos, seleccione **Tareas** y haga clic en **Implementar la base de datos en Microsoft Azure SQL Database**.
   
    ![Implementación en Azure desde el menú Tareas](./media/sql-database-cloud-migrate/MigrateUsingDeploymentWizard01.png)
4. En el asistente para la implementación, haga clic en **Siguiente** y en **Conectar** para configurar la conexión con el servidor de SQL Database.
   
   ![Implementación en Azure desde el menú Tareas](./media/sql-database-cloud-migrate/MigrateUsingDeploymentWizard002.png)
5. En el cuadro de diálogo Conectar con el servidor, escriba la información de conexión para conectarse a su servidor de Base de datos SQL. Para obtener el nombre de un servidor existente, vaya a [Visualización y actualización de un servidor de Base de datos de SQL Server y su configuración](sql-database-view-update-server-settings.md).
   
    ![Implementación en Azure desde el menú Tareas](./media/sql-database-cloud-migrate/MigrateUsingDeploymentWizard00.png)
6. Proporcione los siguiente elementos para el archivo [BACPAC](https://msdn.microsoft.com/library/ee210546.aspx#Anchor_4) que crea el asistente durante el proceso de migración:
   
   * El **nuevo nombre de base de datos** 
   * La **edición de Base de datos SQL de Microsoft Azure** ([nivel de servicio](sql-database-service-tiers.md))
   * El **tamaño máximo de la base de datos**
   * El **objetivo de servicio** (nivel de rendimiento)
   * El **nombre de archivo temporal**  
   
   ![Exportar configuración](./media/sql-database-cloud-migrate/MigrateUsingDeploymentWizard02.png)
7. Realice los pasos del asistente. Según el tamaño y la complejidad de la base de datos, es posible que la implementación tarde desde unos minutos hasta varias horas. Si el asistente detecta problemas de compatibilidad, se muestran errores en la pantalla y la migración no continúa. Para instrucciones sobre cómo solucionar problemas de compatibilidad de bases de datos, vaya a [Solución de problemas de compatibilidad de bases de datos](sql-database-cloud-migrate-fix-compatibility-issues.md).
8. Con el Explorador de objetos, conéctese a la base de datos migrada en el servidor de Base de datos SQL de Azure.
9. Use el Portal de Azure para ver la base de datos y sus propiedades.

## <a name="next-steps"></a>Pasos siguientes
* [Versión más reciente de SSDT](https://msdn.microsoft.com/library/mt204009.aspx)
* [Versión más reciente de SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx)

## <a name="additional-resources"></a>Recursos adicionales
* [Características de SQL Database](sql-database-features.md)
* [Diferencias de Transact-SQL de Base de datos SQL de Azure](sql-database-transact-sql-information.md)
* [Migración de bases de datos no SQL Server mediante SQL Server Migration Assistant](http://blogs.msdn.com/b/ssma/)




<!--HONumber=Jan17_HO1-->


