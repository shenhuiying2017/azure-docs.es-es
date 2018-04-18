---
title: Crear SQL Data Warehouse mediante el uso de Powershell | Microsoft Docs
description: "Creación de SQL Data Warehouse con Powershell"
services: sql-data-warehouse
documentationcenter: NA
author: hirokib
manager: jhubbard
editor: 
ms.assetid: 97434863-7938-4129-8949-5a119f5949e3
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: get-started-article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.custom: create
ms.date: 10/31/2016
ms.author: elbutter;barbkess
ms.openlocfilehash: a763f1c600c1a3f37cb565a8eb7db3c3f27dcf75
ms.sourcegitcommit: 68aec76e471d677fd9a6333dc60ed098d1072cfc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/18/2017
---
# <a name="create-sql-data-warehouse-using-powershell"></a>Creación de SQL Data Warehouse con Powershell
> [!div class="op_single_selector"]
> * [Azure Portal](sql-data-warehouse-get-started-provision.md)
> * [TSQL](sql-data-warehouse-get-started-create-database-tsql.md)
> * [PowerShell](sql-data-warehouse-get-started-provision-powershell.md)
>
>

En este artículo se explica cómo crear un SQL Data Warehouse mediante PowerShell.

## <a name="prerequisites"></a>Requisitos previos
Para empezar, necesitará lo siguiente:

* **Cuenta de Azure**: visite las secciones sobre [evaluación gratuita de Azure][Azure Free Trial] o [Crédito mensual de MSDN de Azure][MSDN Azure Credits] para crear una cuenta.
* **Azure SQL Server**: consulte [Creación de una base de datos de Azure SQL en Azure Portal][Create an Azure SQL database in the Azure Portal] o [Creación de una base de datos de Azure SQL con PowerShell][Create an Azure SQL database with PowerShell] para más información.
* **Nombre del grupo de recursos**: use el mismo grupo de recursos que el servidor SQL Server de Azure o consulte [Creación de un grupo de recursos](../azure-resource-manager/resource-group-portal.md).
* **PowerShell versión 1.0.3 o posterior**: para comprobar la versión, ejecute **Get-Module -ListAvailable -Name Azure**.  Se puede instalar la versión más reciente desde el [Instalador de plataforma web de Microsoft][Microsoft Web Platform Installer].  Para más información sobre cómo instalar la versión más reciente, consulte [Cómo instalar y configurar Azure PowerShell][How to install and configure Azure PowerShell].

> [!NOTE]
> La creación de una instancia de SQL Data Warehouse puede dar lugar a un nuevo servicio facturable.  Consulte [Precios de SQL Data Warehouse][SQL Data Warehouse pricing] para más información sobre los precios.
>
>

## <a name="create-a-sql-data-warehouse"></a>Creación de SQL Data Warehouse
1. Abra Windows PowerShell.
2. Ejecute este cmdlet para iniciar sesión en el Administrador de recursos de Azure.

    ```Powershell
    Login-AzureRmAccount
    ```
3. Seleccione la suscripción que desea utilizar para la sesión actual.

    ```Powershell
    Get-AzureRmSubscription    -SubscriptionName "MySubscription" | Select-AzureRmSubscription
    ```
4. Cree la base de datos. En este ejemplo se crea una base de datos denominada "mynewsqldw", con el nivel de objetivo de servicio "DW400", en el servidor llamado "sqldwserver1" que se encuentra en el grupo de recursos denominado "mywesteuroperesgp1".

   ```Powershell
   New-AzureRmSqlDatabase -RequestedServiceObjectiveName "DW400" -DatabaseName "mynewsqldw" -ServerName "sqldwserver1" -ResourceGroupName "mywesteuroperesgp1" -Edition "DataWarehouse" -CollationName "SQL_Latin1_General_CP1_CI_AS" -MaxSizeBytes 10995116277760
   ```

Los parámetros obligatorios son:

* **RequestedServiceObjectiveName**: la cantidad de [DWU][DWU] solicitada.  Los valores admitidos son: DW100, DW200, DW300, DW400, DW500, DW600, DW1000, DW1200, DW1500, DW2000, DW3000 y DW6000.
* **DatabaseName**: el nombre de SQL Data Warehouse que está creando.
* **ServerName**: el nombre del servidor que se usa para la creación (tiene que ser V12).
* **ResourceGroupName**: el grupo de recursos que está usando.  Para buscar grupos de recursos que estén disponibles en su suscripción, use Get-AzureResource.
* **Edition**: la edición debe ser "DataWarehouse" para crear un SQL Data Warehouse.

Los parámetros opcionales son:

* **CollationName**: la intercalación predeterminada cuando no se especifica otra es SQL_Latin1_General_CP1_CI_AS.  No se puede cambiar la intercalación de una base de datos.
* **MaxSizeBytes**: el tamaño máximo predeterminado de una base de datos es 10 GB.

Para más información sobre las opciones de parámetros, consulte [New-AzureRmSqlDatabase][New-AzureRmSqlDatabase] y [Create Database (Azure SQL Data Warehouse)][Create Database (Azure SQL Data Warehouse)] (Creación de base de datos [Azure SQL Data Warehouse]).

## <a name="next-steps"></a>Pasos siguientes
Después de que Azure SQL Data Warehouse finalice el aprovisionamiento, puede intentar [cargar datos de ejemplo][loading sample data] o averiguar cómo [desarrollar][develop], [cargar][load] o [migrar][migrate].

Si está interesado en más información sobre cómo administrar SQL Data Warehouse mediante programación, consulte nuestro artículo sobre cómo usar [las API de REST y los cmdlets de PowerShell][PowerShell cmdlets and REST APIs].

<!--Image references-->

<!--Article references-->
[DWU]: ./sql-data-warehouse-overview-what-is.md
[migrate]: ./sql-data-warehouse-overview-migrate.md
[develop]: ./sql-data-warehouse-overview-develop.md
[load]: ./sql-data-warehouse-load-with-bcp.md
[loading sample data]: ./sql-data-warehouse-load-sample-databases.md
[PowerShell cmdlets and REST APIs]: ./sql-data-warehouse-reference-powershell-cmdlets.md
[firewall rules]: ../sql-database-configure-firewall-settings.md

[How to install and configure Azure PowerShell]: /powershell/azureps-cmdlets-docs
[how to create a SQL Data Warehouse from the Azure Portal]: ./sql-data-warehouse-get-started-provision.md
[Create an Azure SQL database in the Azure Portal]: ../sql-database/sql-database-get-started.md
[Create an Azure SQL database with PowerShell]: ../sql-database/sql-database-get-started-powershell.md
[how to create a resource group]: ../azure-resource-manager/resource-group-template-deploy-portal.md#create-resource-group

<!--MSDN references-->
[MSDN]: https://msdn.microsoft.com/library/azure/dn546722.aspx
[New-AzureRmSqlDatabase]: https://msdn.microsoft.com/library/mt619339.aspx
[Create Database (Azure SQL Data Warehouse)]: https://msdn.microsoft.com/library/mt204021.aspx

<!--Other Web references-->
[Microsoft Web Platform Installer]: https://aka.ms/webpi-azps
[SQL Data Warehouse pricing]: https://azure.microsoft.com/pricing/details/sql-data-warehouse/
[Azure Free Trial]: https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A261C142F
[MSDN Azure Credits]: https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F
