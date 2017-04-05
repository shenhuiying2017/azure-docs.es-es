---
title: "PowerShell: exportación de una base de datos Azure SQL Database a un archivo BACPAC | Microsoft Docs"
description: "Exportación de una base de datos Azure SQL Database a un archivo BACPAC mediante PowerShell"
services: sql-database
documentationcenter: 
author: stevestein
manager: jhubbard
editor: 
ms.assetid: 9439dd83-812f-4688-97ea-2a89a864d1f3
ms.service: sql-database
ms.custom: migrate and move
ms.devlang: NA
ms.date: 02/07/2017
ms.author: sstein
ms.workload: data-management
ms.topic: article
ms.tgt_pltfrm: NA
translationtype: Human Translation
ms.sourcegitcommit: 503f5151047870aaf87e9bb7ebf2c7e4afa27b83
ms.openlocfilehash: 41774c7f1c038ee017d719e59ed79fb68999dac8
ms.lasthandoff: 03/29/2017


---
# <a name="export-an-azure-sql-database-or-a-sql-server-to-a-bacpac-file-by-using-powershell"></a>Exportación de una base de datos de Azure SQL Database o un servidor SQL Server a un archivo BACPAC mediante PowerShell

En este artículo se proporcionan instrucciones para exportar la base de datos de Azure SQL Database o el servidor SQL Server a un archivo BACPAC (almacenado en Azure Blob Storage) mediante PowerShell. Para obtener información general de la exportación a un archivo BACPAC, consulte [Exportación de una base de datos Azure SQL Database a un archivo BACPAC mediante Azure Portal](sql-database-export.md).

> [!NOTE]
> También puede exportar el archivo de Azure SQL Database a un archivo BACPAC mediante [Azure Portal](sql-database-export-portal.md), [SQL Server Management Studio](sql-database-export-ssms.md) o [SQLPackage](sql-database-export-sqlpackage.md).
>

## <a name="prerequisites"></a>Requisitos previos

Para completar este artículo, necesitará lo siguiente:

* Una suscripción de Azure.
* Una base de datos SQL de Azure.
* Una [cuenta de almacenamiento estándar de Azure](../storage/storage-create-storage-account.md), con un contenedor de blobs para almacenar el BACPAC.

[!INCLUDE [Start your PowerShell session](../../includes/sql-database-powershell.md)]

## <a name="export-your-database"></a>Exportación de la base de datos
El cmdlet [New-AzureRmSqlDatabaseExport](https://docs.microsoft.com/powershell/resourcemanager/azurerm.sql/v2.7.0/new-azurermsqldatabaseexport) envía una solicitud de base de datos de exportación al servicio. Según el tamaño de la base de datos, la operación de exportación puede tardar algún tiempo en completarse.

> [!IMPORTANT]
> Para garantizar un archivo BACPAC transaccionalmente coherente, primero debe [crear una copia de la base de datos](scripts/sql-database-copy-database-to-new-server-powershell.md)y después exportar dicha copia.
> 
> 

     $exportRequest = New-AzureRmSqlDatabaseExport -ResourceGroupName $ResourceGroupName -ServerName $ServerName `
       -DatabaseName $DatabaseName -StorageKeytype $StorageKeytype -StorageKey $StorageKey -StorageUri $BacpacUri `
       -AdministratorLogin $creds.UserName -AdministratorLoginPassword $creds.Password


## <a name="monitor-the-progress-of-the-export-operation"></a>Supervisar el progreso de la operación de exportación
Después de ejecutar [New-AzureRmSqlDatabaseExport](https://docs.microsoft.com//powershell/resourcemanager/azurerm.sql/v2.7.0/new-azurermsqldatabaseexport), puede comprobar el estado de la solicitud ejecutando [Get-AzureRmSqlDatabaseImportExportStatus](https://docs.microsoft.com/powershell/resourcemanager/azurerm.sql/v2.7.0/get-azurermsqldatabaseimportexportstatus). Si se ejecuta esto de inmediato después de la solicitud, se devuelve normalmente **Estado: en curso**. Cuando vea **Estado: Correcto**, la exportación se habrá completado.

    Get-AzureRmSqlDatabaseImportExportStatus -OperationStatusLink $exportRequest.OperationStatusLink



## <a name="export-sql-database-example"></a>Ejemplo de exportación de base de datos SQL
En el ejemplo siguiente se exporta una base de datos SQL existente a BACPAC y, a continuación, se muestra cómo comprobar el estado de la operación de exportación.

Para ejecutar el ejemplo, hay algunas variables que debe reemplazar por los valores específicos de la cuenta de almacenamiento y base de datos. En el [Portal de Azure](https://portal.azure.com), vaya a la cuenta de almacenamiento para obtener el nombre de la misma, el nombre del contenedor de blobs y el valor de clave. Puede encontrar la clave haciendo clic en **Teclas de acceso** en la hoja de su cuenta de almacenamiento.

Reemplace `VARIABLE-VALUES` por los valores para los recursos específicos de Azure. El nombre de la base de datos es la base de datos existente que desea exportar.

    $subscriptionId = "YOUR AZURE SUBSCRIPTION ID"

    Login-AzureRmAccount
    Set-AzureRmContext -SubscriptionId $subscriptionId

    # Database to export
    $DatabaseName = "DATABASE-NAME"
    $ResourceGroupName = "RESOURCE-GROUP-NAME"
    $ServerName = "SERVER-NAME"
    $serverAdmin = "ADMIN-NAME"
    $serverPassword = "ADMIN-PASSWORD" 
    $securePassword = ConvertTo-SecureString -String $serverPassword -AsPlainText -Force
    $creds = New-Object -TypeName System.Management.Automation.PSCredential -ArgumentList $serverAdmin, $securePassword

    # Generate a unique filename for the BACPAC
    $bacpacFilename = $DatabaseName + (Get-Date).ToString("yyyyMMddHHmm") + ".bacpac"

    # Storage account info for the BACPAC
    $BaseStorageUri = "https://STORAGE-NAME.blob.core.windows.net/BLOB-CONTAINER-NAME/"
    $BacpacUri = $BaseStorageUri + $bacpacFilename
    $StorageKeytype = "StorageAccessKey"
    $StorageKey = "YOUR STORAGE KEY"

    $exportRequest = New-AzureRmSqlDatabaseExport -ResourceGroupName $ResourceGroupName -ServerName $ServerName `
       -DatabaseName $DatabaseName -StorageKeytype $StorageKeytype -StorageKey $StorageKey -StorageUri $BacpacUri `
       -AdministratorLogin $creds.UserName -AdministratorLoginPassword $creds.Password
    $exportRequest

    # Check status of the export
    Get-AzureRmSqlDatabaseImportExportStatus -OperationStatusLink $exportRequest.OperationStatusLink

## <a name="automate-export-using-azure-automation"></a>Automatización de la exportación mediante Azure Automation

La exportación automática de Azure SQL Database Automated Export se encuentra ahora en versión preliminar y se retirará el 1 de marzo de 2017. A partir del 1 de diciembre de 2016, ya no podrá configurar la exportación automatizada en cualquier base de datos SQL. Los trabajos de exportación automatizada existentes continuarán funcionando hasta que el 1 de marzo de 2017. A partir del 1 de diciembre de 2016, podrá usar la [retención de copias de seguridad a largo plazo](sql-database-long-term-retention.md) o [Azure Automation](../automation/automation-intro.md) para archivar las bases de datos SQL periódicamente mediante PowerShell de acuerdo con la programación que elija. Para obtener un script de ejemplo, puede descargar [este que está disponible en Github](https://github.com/Microsoft/sql-server-samples/tree/master/samples/manage/azure-automation-automated-export). 


## <a name="next-steps"></a>Pasos siguientes
* Para más información sobre cómo importar una base de datos SQL de Azure con PowerShell, consulte [Importar un archivo BACPAC mediante PowerShell](scripts/sql-database-import-from-bacpac-powershell.md).
* Para obtener información sobre cómo importar un archivo BACPAC mediante SQL Package, consulte [Export an Azure SQL database or a SQL Server database to a BACPAC file using SqlPackage](sql-database-import-sqlpackage.md) (Exportación de una base de datos de Azure SQL Database a un archivo BACPCAC mediante SqlPackage).
* Para obtener información sobre cómo importar un BACPAC mediante Azure Portal, consulte [Import a BACPAC to Azure SQL Database using the Azure portal](sql-database-import-portal.md) (Importación de un BACPCAC en Azure SQL Database mediante Azure Portal).
* Para obtener información sobre el proceso de migración de bases de datos de SQL Server completo, incluidas las recomendaciones de rendimiento, consulte [Migración de una base de datos de SQL Server a Azure SQL Database](sql-database-cloud-migrate.md).
* Para obtener información acerca de la conservación de copias de seguridad a largo plazo de una copia de seguridad de Azure SQL Database como una alternativa a la exportación de una base de datos para archivarla, consulte [Retención a largo plazo](sql-database-long-term-retention.md).
* Para obtener información sobre cómo importar un BACPAC a una base de datos de SQL Server, consulte [Importación de un BACPCAC en una Base de datos de SQL Server](https://msdn.microsoft.com/library/hh710052.aspx)



## <a name="additional-resources"></a>Recursos adicionales
* [New-AzureRmSqlDatabaseExport](https://docs.microsoft.com/powershell/resourcemanager/azurerm.sql/v2.7.0/new-azurermsqldatabaseexport)
* [Get-AzureRmSqlDatabaseImportExportStatus](https://docs.microsoft.com/powershell/resourcemanager/azurerm.sql/v2.7.0/get-azurermsqldatabaseimportexportstatus)


