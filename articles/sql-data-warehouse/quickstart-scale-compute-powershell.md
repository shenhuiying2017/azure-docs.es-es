---
title: "Inicio rápido: Escalabilidad horizontal de un proceso en Azure SQL Data Warehouse (PowerShell) | Microsoft Docs"
description: Tareas de PowerShell para escalar horizontalmente los recursos de procesos mediante el ajuste de unidades de almacenamiento de datos.
services: sql-data-warehouse
documentationcenter: NA
author: hirokib
manager: jhubbard
editor: 
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.custom: manage
ms.date: 01/31/2018
ms.author: elbutter;barbkess
ms.openlocfilehash: a3a435d6bdb0d35c96349540d5e9f9b5be61bd9b
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 02/21/2018
---
# <a name="quickstart-scale-compute-in-azure-sql-data-warehouse-in-powershell"></a>Guía de inicio rápido: Escalabilidad horizontal de un proceso en Azure SQL Data Warehouse en PowerShell

Escale procesos en Azure SQL Data Warehouse en PowerShell. [Escale horizontalmente un proceso](sql-data-warehouse-manage-compute-overview.md) para aumentar el rendimiento, o bien revierta la escalabilidad del proceso para ahorrar costos. 

Si no tiene una suscripción a Azure, cree una cuenta [gratuita](https://azure.microsoft.com/free/) antes de empezar.

Para realizar este tutorial, es necesaria la versión 5.1.1 del módulo de Azure PowerShell, o cualquier versión posterior. Ejecute `Get-Module -ListAvailable AzureRM` para encontrar la versión que tiene actualmente. Si necesita instalarla o actualizarla, consulte [Install and configure Azure PowerShell](/powershell/azure/install-azurerm-ps.md) (Instalación y configuración de Azure PowerShell). 

## <a name="before-you-begin"></a>Antes de empezar

En este inicio rápido se da por supuesto que ya tiene una instancia de SQL Data Warehouse que puede escalar. Si tiene que crear una, consulte [Guía de inicio rápido: Creación de una instancia de Azure SQL Data Warehouse en Azure Portal, y realización de consultas en ella](create-data-warehouse-portal.md) para crear un almacenamiento de datos denominado **mySampleDataWarehouse**. 

## <a name="log-in-to-azure"></a>Inicie sesión en Azure.

Inicie sesión en la suscripción de Azure con el comando [Add-AzureRmAccount](/powershell/module/azurerm.profile/add-azurermaccount) y siga las instrucciones de la pantalla.

```powershell
Add-AzureRmAccount
```

Para ver qué suscripción está usando, ejecute [AzureRmSubscription Get](/powershell/module/azurerm.profile/get-azurermsubscription).

```powershell
Get-AzureRmSubscription
```

Si necesita usar una suscripción diferente de la predeterminada, ejecute [Select-AzureRmSubscription](/powershell/module/azurerm.profile/select-azurermsubscription).

```powershell
Select-AzureRmSubscription -SubscriptionName "MySubscription"
```

## <a name="look-up-data-warehouse-information"></a>Buscar información del almacenamiento de datos

Busque el nombre de la base de datos, el nombre del servidor y el grupo de recursos para el almacenamiento de datos que tiene previsto pausar y reanudar. 

Siga estos pasos para buscar información de ubicación para el almacenamiento de datos.

1. Inicie sesión en el [Azure Portal](https://portal.azure.com/).
2. En la página izquierda de Azure Portal, haga clic en **Bases de datos SQL**.
3. Seleccione **mySampleDataWarehouse** en la página **Bases de datos SQL**. Se abrirá el almacenamiento de datos. 

    ![Nombre del servidor y grupo de recursos](media/pause-and-resume-compute-powershell/locate-data-warehouse-information.png)

4. Anote el nombre del almacenamiento de datos que se usará como nombre de la base de datos. Además, anote el nombre del servidor y el grupo de recursos. Los usará en los comandos para pausar y reanudar.
5. Si su servidor es foo.database.windows.net, use solo la primera parte como nombre de servidor en los cmdlets de PowerShell. En la imagen anterior, el nombre completo del servidor es newserver-20171113.database.windows.net. Usamos **newserver-20171113** como nombre del servidor en el cmdlet de PowerShell.

## <a name="scale-compute"></a>Escalado de proceso

En SQL Data Warehouse, puede aumentar o reducir los recursos de procesos mediante el ajuste de las unidades del almacenamiento de datos. En [Guía de inicio rápido: Creación de una instancia de Azure SQL Data Warehouse en Azure Portal, y realización de consultas en ella](create-data-warehouse-portal.md) creó **mySampleDataWarehouse** y lo inició con 400 DWU. En los siguientes pasos se ajustan las DWU para **mySampleDataWarehouse**.

Para cambiar las unidades de almacenamiento de datos, use el cmdlet de PowerShell [Set-AzureRmSqlDatabase](/powershell/module/azurerm.sql/set-azurermsqldatabase). En el ejemplo siguiente se establecen las unidades de almacenamiento de datos en DW300 para la base de datos **mySampleDataWarehouse**, que se hospeda en el grupo de recursos **myResourceGroup** en el servidor **mynewserver-20171113**.

```Powershell
Set-AzureRmSqlDatabase -ResourceGroupName "myResourceGroup" -DatabaseName "mySampleDataWarehouse" -ServerName "mynewserver-20171113" -RequestedServiceObjectiveName "DW300"
```

## <a name="check-database-state"></a>Comprobar el estado de la base de datos

Para ver el estado actual del almacenamiento de datos, use el cmdlet [Get-AzureRmSqlDatabase](/powershell/module/azurerm.sql/get-azurermsqldatabase) de PowerShell. De esta forma, se obtiene el estado de la base de datos **mySampleDataWarehouse** del grupo de recursos **myResourceGroup** y el servidor **mynewserver-20171113.database.windows.net**.

```powershell
Get-AzureRmSqlDatabase -ResourceGroupName myResourceGroup -ServerName mynewserver-20171113 -DatabaseName mySampleDataWarehouse
```

El resultado será similar a este:

```powershell   
ResourceGroupName             : myResourceGroup
ServerName                    : mynewserver-20171113
DatabaseName                  : mySampleDataWarehouse
Location                      : North Europe
DatabaseId                    : 34d2ffb8-b70a-40b2-b4f9-b0a39833c974
Edition                       : DataWarehouse
CollationName                 : SQL_Latin1_General_CP1_CI_AS
CatalogCollation              :
MaxSizeBytes                  : 263882790666240
Status                        : Online
CreationDate                  : 11/20/2017 9:18:12 PM
CurrentServiceObjectiveId     : 284f1aff-fee7-4d3b-a211-5b8ebdd28fea
CurrentServiceObjectiveName   : DW300
RequestedServiceObjectiveId   : 284f1aff-fee7-4d3b-a211-5b8ebdd28fea
RequestedServiceObjectiveName :
ElasticPoolName               :
EarliestRestoreDate           :
Tags                          :
ResourceId                    : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/
                                resourceGroups/myResourceGroup/providers/Microsoft.Sql/servers/mynewserver-20171113/databases/mySampleDataWarehouse
CreateMode                    :
ReadScale                     : Disabled
ZoneRedundant                 : False
```

Puede comprobar el **Estado** de la base de datos. En este caso, puede ver esta base de datos está en línea.  Al ejecutar este comando, debería recibir uno de los siguientes valores de Estado: En línea, Pausando, Reanudando, Escalando o Pausado.

## <a name="next-steps"></a>pasos siguientes
Ya ha aprendido a escalar procesos para el almacenamiento de datos. Para más información sobre Azure SQL Data Warehouse, siga el tutorial para cargar los datos.

> [!div class="nextstepaction"]
>[Carga de datos en una instancia de SQL Data Warehouse](load-data-from-azure-blob-storage-using-polybase.md)
