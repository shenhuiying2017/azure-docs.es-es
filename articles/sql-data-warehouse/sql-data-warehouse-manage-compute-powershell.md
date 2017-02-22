---
title: "Administración de la potencia de proceso en Azure SQL Data Warehouse (PowerShell) | Microsoft Docs"
description: Tareas de PowerShell para administrar la potencia de proceso. Escalado de los recursos de proceso ajustando DWU. Pausar y reanudar recursos de proceso para ahorrar costos.
services: sql-data-warehouse
documentationcenter: NA
author: barbkess
manager: jhubbard
editor: 
ms.assetid: 8354a3c1-4e04-4809-933f-db414a8c74dc
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.date: 10/31/2016
ms.author: barbkess
translationtype: Human Translation
ms.sourcegitcommit: f9814fc0011153a25489d60d696bb567edfcebc4
ms.openlocfilehash: a4cde433850ab3627f870b51f5a897c6f846c2f0


---
# <a name="manage-compute-power-in-azure-sql-data-warehouse-powershell"></a>Administración de la potencia de proceso en Almacenamiento de datos SQL de Azure (PowerShell)
> [!div class="op_single_selector"]
> * [Información general](sql-data-warehouse-manage-compute-overview.md)
> * [Portal](sql-data-warehouse-manage-compute-portal.md)
> * [PowerShell](sql-data-warehouse-manage-compute-powershell.md)
> * [REST](sql-data-warehouse-manage-compute-rest-api.md)
> * [TSQL](sql-data-warehouse-manage-compute-tsql.md)
> 
> 

## <a name="before-you-begin"></a>Antes de empezar
### <a name="install-the-latest-version-of-azure-powershell"></a>Instalar la versión más reciente de Azure PowerShell
> [!NOTE]
> Para usar Azure PowerShell con Almacenamiento de datos SQL, es necesaria la versión 1.0.3 o posterior de Azure PowerShell.  Para comprobar su versión actual, ejecute el comando **Get-Module -ListAvailable -Name Azure**. Puede instalar la versión más reciente desde [Instalador de plataforma web de Microsoft][Microsoft Web Platform Installer].  Para obtener más información, vea [Instalación y configuración de Azure PowerShell][How to install and configure Azure PowerShell].
> 
> 

### <a name="get-started-with-azure-powershell-cmdlets"></a>Introducción a los cmdlets de Azure PowerShell
Primeros pasos:

1. Abra Azure PowerShell.
2. En el símbolo del sistema de PowerShell, ejecute estos comandos para iniciar sesión en Azure Resource Manager y seleccione su suscripción.
   
    ```PowerShell
    Login-AzureRmAccount
    Get-AzureRmSubscription
    Select-AzureRmSubscription -SubscriptionName "MySubscription"
    ```

<a name="scale-performance-bk"></a>
<a name="scale-compute-bk"></a>

## <a name="scale-compute-power"></a>Escalado de la potencia de proceso
[!INCLUDE [SQL Data Warehouse scale DWUs description](../../includes/sql-data-warehouse-scale-dwus-description.md)]

Para cambiar DWU, use el cmdlet de PowerShell [Set-AzureRmSqlDatabase][Set-AzureRmSqlDatabase]. En el ejemplo siguiente se establece el objetivo de nivel de servicio en DW1000 para la base de datos MySQLDW que se hospeda en el servidor MyServer.

```Powershell
Set-AzureRmSqlDatabase -DatabaseName "MySQLDW" -ServerName "MyServer" -RequestedServiceObjectiveName "DW1000"
```

<a name="pause-compute-bk"></a>

## <a name="pause-compute"></a>Pausa del proceso
[!INCLUDE [SQL Data Warehouse pause description](../../includes/sql-data-warehouse-pause-description.md)]

Para pausar una base de datos, use el cmdlet [Suspend-AzureRmSqlDatabase][Suspend-AzureRmSqlDatabase]. El siguiente ejemplo pausa una base de datos denominada Database02 que está hospedada en un servidor llamado Server01. El servidor está en un grupo de recursos de Azure denominado ResourceGroup1.

> [!NOTE]
> Tenga en cuenta que si el servidor es foo.database.windows.net, debe usar "foo" como -ServerName en los cmdlets de Powershell.
> 
> 

```Powershell
Suspend-AzureRmSqlDatabase –ResourceGroupName "ResourceGroup1" `
–ServerName "Server01" –DatabaseName "Database02"
```
En el ejemplo siguiente, que es una variación, se recupera la base de datos en el objeto $database. A continuación, canaliza el objeto a [Suspend-AzureRmSqlDatabase][Suspend-AzureRmSqlDatabase]. Los resultados se almacenan en el objeto resultDatabase. El comando final muestra los resultados.

```Powershell
$database = Get-AzureRmSqlDatabase –ResourceGroupName "ResourceGroup1" `
–ServerName "Server01" –DatabaseName "Database02"
$resultDatabase = $database | Suspend-AzureRmSqlDatabase
$resultDatabase
```

<a name="resume-compute-bk"></a>

## <a name="resume-compute"></a>Reanudación del proceso
[!INCLUDE [SQL Data Warehouse resume description](../../includes/sql-data-warehouse-resume-description.md)]

Para iniciar una base de datos, use el cmdlet [Resume-AzureRmSqlDatabase][Resume-AzureRmSqlDatabase]. El siguiente ejemplo inicia una base de datos denominada Database02 que está hospedada en un servidor llamado Server01. El servidor está en un grupo de recursos de Azure denominado ResourceGroup1.

```Powershell
Resume-AzureRmSqlDatabase –ResourceGroupName "ResourceGroup1" `
–ServerName "Server01" -DatabaseName "Database02"
```

En el ejemplo siguiente, que es una variación, se recupera la base de datos en el objeto $database. A continuación, canaliza el objeto a [Resume-AzureRmSqlDatabase][Resume-AzureRmSqlDatabase] y almacena los resultados en $resultDatabase. El comando final muestra los resultados.

```Powershell
$database = Get-AzureRmSqlDatabase –ResourceGroupName "ResourceGroup1" `
–ServerName "Server01" –DatabaseName "Database02"
$resultDatabase = $database | Resume-AzureRmSqlDatabase
$resultDatabase
```

<a name="next-steps-bk"></a>

## <a name="next-steps"></a>Pasos siguientes
Para otras tareas de administración, consulte [Información general de administración][Management overview].

<!--Image references-->

<!--Article references-->
[Service capacity limits]: ./sql-data-warehouse-service-capacity-limits.md
[Management overview]: ./sql-data-warehouse-overview-manage.md
[How to install and configure Azure PowerShell]: /powershell/azureps-cmdlets-docs
[Manage compute overview]: ./sql-data-warehouse-manage-compute-overview.md

<!--MSDN references-->
[Resume-AzureRmSqlDatabase]: https://msdn.microsoft.com/library/mt619347.aspx
[Suspend-AzureRmSqlDatabase]: https://msdn.microsoft.com/library/mt619337.aspx
[Set-AzureRmSqlDatabase]: https://msdn.microsoft.com/library/mt619433.aspx

<!--Other Web references-->
[Microsoft Web Platform Installer]: https://aka.ms/webpi-azps
[Azure portal]: http://portal.azure.com/



<!--HONumber=Jan17_HO3-->


