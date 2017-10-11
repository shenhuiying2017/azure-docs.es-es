---
title: "Creación de una instancia de SQL Data Warehouse con TSQL | Microsoft Docs"
description: Aprenda a crear una base de datos de Almacenamiento de datos SQL de Azure con TSQL
services: sql-data-warehouse
documentationcenter: NA
author: hirokib
manager: jhubbard
editor: 
tags: azure-sql-data-warehouse
ms.assetid: a4e2e68e-aa9c-4dd3-abb0-f7df997d237a
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: get-started-article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.custom: create
ms.date: 10/31/2016
ms.author: elbutter;barbkess
ms.openlocfilehash: 10d8aa2b3ab8d7d8a9b91e95ffccf03faa89d237
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 07/11/2017
---
# <a name="create-a-sql-data-warehouse-database-by-using-transact-sql-tsql"></a>Creación de una base de datos de Almacenamiento de datos SQL mediante Transact-SQL (TSQL)
> [!div class="op_single_selector"]
> * [Azure Portal](sql-data-warehouse-get-started-provision.md)
> * [TSQL](sql-data-warehouse-get-started-create-database-tsql.md)
> * [PowerShell](sql-data-warehouse-get-started-provision-powershell.md)
>
>

En este artículo se explica cómo crear un Almacenamiento de datos SQL mediante T-SQL.

## <a name="prerequisites"></a>Requisitos previos
Para empezar, necesitará lo siguiente:

* **Cuenta de Azure**: visite las secciones sobre [evaluación gratuita de Azure][Azure Free Trial] o [Crédito mensual de MSDN de Azure][MSDN Azure Credits] para crear una cuenta.
* **Servidor de SQL Azure**: consulte [crear un servidor lógico de la base de datos de SQL Azure con el Portal de Azure] [crear un servidor lógico de la base de datos de SQL Azure con el Portal de Azure] o [crear un servidor lógico de la base de datos de SQL Azure con PowerShell] [crear un SQL Azure Servidor lógico de base de datos con PowerShell] para obtener más detalles.
* **Nombre del grupo de recursos**: use el mismo grupo de recursos que el servidor SQL Server de Azure o consulte [Creación de un grupo de recursos][how to create a resource group].
* **Entorno para ejecutar T-SQL**: puede usar [Visual Studio][Installing Visual Studio and SSDT], [sqlcmd][sqlcmd] o [SSMS][SSMS] para ejecutar T-SQL.

> [!NOTE]
> La creación de una instancia de Almacenamiento de datos SQL puede dar lugar a un nuevo servicio facturable.  Consulte [Precios de SQL Data Warehouse][SQL Data Warehouse pricing] para más información sobre los precios.
>
>

## <a name="create-a-database-with-visual-studio"></a>Creación de una base de datos con Visual Studio
Si no está familiarizado con Visual Studio, consulte el artículo [Consultas en Azure SQL Data Warehouse (Visual Studio)][Query Azure SQL Data Warehouse (Visual Studio)].  Para comenzar, abra el Explorador de objetos de SQL Server en Visual Studio y conéctese al servidor que hospedará la base de datos de Almacenamiento de datos SQL.  Una vez conectado, puede crear una instancia de Almacenamiento de datos SQL mediante la ejecución del siguiente comando SQL en la base de datos **maestra** .  Este comando crea la base de datos MySqlDwDb con un objetivo de servicio de DW400 y permite que crezca hasta un tamaño máximo de 10 TB.

```sql
CREATE DATABASE MySqlDwDb COLLATE SQL_Latin1_General_CP1_CI_AS (EDITION='datawarehouse', SERVICE_OBJECTIVE = 'DW400', MAXSIZE= 10240 GB);
```

## <a name="create-a-database-with-sqlcmd"></a>Creación de una base de datos con sqlcmd
Como alternativa, puede ejecutar el mismo comando con sqlcmd mediante la ejecución del siguiente código en el símbolo del sistema.

```sql
sqlcmd -S <Server Name>.database.windows.net -I -U <User> -P <Password> -Q "CREATE DATABASE MySqlDwDb COLLATE SQL_Latin1_General_CP1_CI_AS (EDITION='datawarehouse', SERVICE_OBJECTIVE = 'DW400', MAXSIZE= 10240 GB)"
```

La intercalación predeterminada cuando no se especifica otra es COLLATE SQL_Latin1_General_CP1_CI_AS.  El valor de `MAXSIZE` puede estar entre 250 GB y 240 TB.  El valor de `SERVICE_OBJECTIVE` puede estar entre DW100 y DW2000 [DWU][DWU].  Para obtener una lista de todos los valores válidos, consulte la documentación de MSDN para [CREATE DATABASE][CREATE DATABASE].  Se pueden cambiar los valores de MAXSIZE y SERVICE_OBJECTIVE con un comando [ALTER DATABASE][ALTER DATABASE] de T-SQL.  No se puede cambiar la intercalación de una base de datos después de su creación.   Debe tener precaución al cambiar el valor de SERVICE_OBJECTIVE ya que cambiar la DWU provocará un reinicio de los servicios que cancelará todas las consultas en marcha.  Con el cambio de MAXSIZE no se reinician servicios, ya que es solo una operación de metadatos sencilla.

## <a name="next-steps"></a>Pasos siguientes
Después de que SQL Data Warehouse termine el aprovisionamiento, [puede cargar datos de ejemplo][load sample data] o averiguar cómo [desarrollar][develop], [cargar][load] o [migrar][migrate].

<!--Article references-->
[DWU]: ./sql-data-warehouse-overview-what-is.md
[how to create a SQL Data Warehouse from the Azure portal]: sql-data-warehouse-get-started-provision.md
[Query Azure SQL Data Warehouse (Visual Studio)]: sql-data-warehouse-query-visual-studio.md
[migrate]: sql-data-warehouse-overview-migrate.md
[develop]: sql-data-warehouse-overview-develop.md
[load]: sql-data-warehouse-overview-load.md
[load sample data]: sql-data-warehouse-load-sample-databases.md
[Create an Azure SQL database with the Azure Portal]: ../sql-database/sql-database-get-started.md
[Create an Azure SQL database with PowerShell]: ../sql-database/sql-database-create-and-configure-database-powershell
[how to create a resource group]: ../azure-resource-manager/resource-group-template-deploy-portal.md#create-resource-group
[Installing Visual Studio and SSDT]: sql-data-warehouse-install-visual-studio.md
[sqlcmd]: sql-data-warehouse-get-started-connect-sqlcmd.md

<!--MSDN references-->
[CREATE DATABASE]: https://msdn.microsoft.com/library/mt204021.aspx
[ALTER DATABASE]: https://msdn.microsoft.com/library/mt204042.aspx
[SSMS]: https://msdn.microsoft.com/library/mt238290.aspx

<!--Other Web references-->
[SQL Data Warehouse pricing]: https://azure.microsoft.com/pricing/details/sql-data-warehouse/
[Azure Free Trial]: https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A261C142F
[MSDN Azure Credits]: https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F
