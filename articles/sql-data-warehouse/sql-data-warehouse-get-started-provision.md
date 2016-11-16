---
title: "Creación de una instancia de SQL Data Warehouse en Azure Portal | Microsoft Docs"
description: Aprenda a crear una instancia de Almacenamiento de datos SQL de Azure en el Portal de Azure
services: sql-data-warehouse
documentationcenter: NA
author: barbkess
manager: jhubbard
editor: 
tags: azure-sql-data-warehouse
ms.assetid: 552e496e-d560-419c-9996-6bbc80c521cb
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: hero-article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.date: 10/31/2016
ms.author: barbkess
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 2cf6e81d6a74c4a5beff91a3df90780a83bf1327


---
# <a name="create-an-azure-sql-data-warehouse"></a>Creación de una instancia de Almacenamiento de datos SQL de Azure
> [!div class="op_single_selector"]
> * [Portal de Azure](sql-data-warehouse-get-started-provision.md)
> * [TSQL](sql-data-warehouse-get-started-create-database-tsql.md)
> * [PowerShell](sql-data-warehouse-get-started-provision-powershell.md)
> 
> 

En este tutorial se usa el Portal de Azure para crear una instancia de Almacenamiento de datos SQL que contiene la base de datos de ejemplo AdventureWorksDW.

## <a name="prerequisites"></a>Requisitos previos
Para empezar, necesitará lo siguiente:

* **Cuenta de Azure**: visite las secciones sobre [evaluación gratuita de Azure][evaluación gratuita de Azure] o [Crédito mensual de MSDN Azure][Crédito mensual de MSDN Azure] para crear una cuenta.
* **Azure SQL Server**: consulte [Creación de un servidor lógico de Azure SQL Database con Azure Portal][Creación de un servidor lógico de Azure SQL Database con Azure Portal] para más información.

> [!NOTE]
> La creación de una instancia de Almacenamiento de datos SQL puede dar lugar a un nuevo servicio facturable.  Consulte [Precios de SQL Data Warehouse ][Precios de SQL Data Warehouse] para más información.
> 
> 

## <a name="create-a-sql-data-warehouse"></a>Creación de Almacenamiento de datos SQL
1. Inicie sesión en el [Portal de Azure](https://portal.azure.com).
2. Haga clic en **+ Nuevo** > **Datos y almacenamiento** > **SQL Data Warehouse**.
   
    ![Crear](./media/sql-data-warehouse-get-started-provision/create-sample.gif)
3. En la hoja **Almacenamiento de datos SQL** , rellene la información necesaria y presione 'Crear' para crearlo.
   
    ![Crear base de datos](./media/sql-data-warehouse-get-started-provision/create-database.png)
   
   * **Servidor**: se recomienda que seleccione primero su servidor.  
   * **Nombre de la base de datos**: el nombre que se usa para hacer referencia a Almacenamiento de datos SQL.  Debe ser exclusivo en el servidor.
   * **Rendimiento**: se recomienda empezar por 400 [DWU][DWU]. Puede mover el control deslizante hacia la izquierda o la derecha para ajustar el rendimiento de su almacén de datos, o bien para reducirlo o escalarlo verticalmente una vez creado.  Para aprender más sobre las DWU, consulte nuestra documentación sobre [escalado](sql-data-warehouse-manage-compute-overview.md) o nuestra [página de precios][Precios de Almacenamiento de datos SQL]. 
   * **Suscripción**: seleccione la [suscripción] a la que se facturará esta instancia de Almacenamiento de datos SQL.
   * **Grupo de recursos**: los [Grupo de recursoss][Grupo de recursos] son contenedores diseñados para ayudarle a administrar una colección de recursos de Azure. Más información sobre los [grupos de recursos](../azure-resource-manager/resource-group-overview.md).
   * **Selección del origen**: haga clic en **Seleccionar origen** > **Ejemplo**. Azure rellena automáticamente la opción **Seleccionar muestra** con AdventureWorksDW.

> [!NOTE]
> La intercalación predeterminada para un Almacenamiento de datos SQL es SQL_Latin1_General_CP1_CI_AS. Si se necesita una intercalación distinta, puede utilizar [T-SQL][T-SQL] para crear la base de datos con otra intercalación.
> 
> 

1. Haga clic en **Crear** para crear su instancia de Almacenamiento de datos SQL.
2. Espere unos minutos. Cuando el almacén de datos está listo, debería volver al [Portal de Azure](https://portal.azure.com). Encontrará Almacenamiento de datos SQL en el panel, entre las bases de datos SQL o en el grupo de recursos que usó para crearlo. 
   
    ![Vista del portal](./media/sql-data-warehouse-get-started-provision/database-portal-view.png)

[!INCLUDE [SQL Database create server](../../includes/sql-database-create-new-server-firewall-portal.md)]

## <a name="next-steps"></a>Pasos siguientes
Ahora que ha creado una instancia de Almacenamiento de datos SQL, está listo para [conectarse](sql-data-warehouse-connect-overview.md) y empezar a realizar consultas.

Para cargar datos en Almacenamiento de datos SQL, consulte la [información general sobre la carga](sql-data-warehouse-overview-load.md).

Si está intentando migrar una base de datos existente a SQL Data Warehouse, consulte [ Información general sobre migración](sql-data-warehouse-overview-migrate.md) o use la [utilidad de migración](sql-data-warehouse-migrate-migration-utility.md).

Las reglas de firewall también se pueden configurar mediante Transact-SQL. Para más información, vea [sp_set_firewall_rule][sp_set_firewall_rule] y [sp_set_database_firewall_rule][sp_set_database_firewall_rule].

También es una buena idea echar un vistazo a los [procedimientos recomendados][procedimientos recomendados].

<!--Article references-->
[Creación de un servidor lógico de Base de datos SQL de Azure con el Portal de Azure]: ../sql-database/sql-database-get-started.md#create-an-azure-sql-database-logical-server
[Creación de un servidor lógico de Base de datos SQL de Azure con PowerShell]: ../sql-database/sql-database-get-started-powershell.md#database-setup-create-a-resource-group-server-and-firewall-rule
[resource groups]: ../resource-group-template-deploy-portal.md
[procedimientos recomendados]: sql-data-warehouse-best-practices.md
[DWU]: sql-data-warehouse-overview-what-is.md#data-warehouse-units
[suscripción]: ../azure-glossary-cloud-terminology.md#subscription
[resource group]: ../azure-glossary-cloud-terminology.md#resource-group
[T-SQL]: ./sql-data-warehouse-get-started-create-database-tsql.md

<!--MSDN references-->
[sp_set_firewall_rule]: https://msdn.microsoft.com/library/dn270017.aspx
[sp_set_database_firewall_rule]: https://msdn.microsoft.com/library/dn270010.aspx

<!--Other Web references-->
[Precios de Almacenamiento de datos SQL]: https://azure.microsoft.com/pricing/details/sql-data-warehouse/
[evaluación gratuita de Azure]: https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A261C142F
[créditos de Azure de MSDN]: https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F




<!--HONumber=Nov16_HO2-->


