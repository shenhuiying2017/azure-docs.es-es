---
title: "Importación de un archivo BACPAC para crear una base de datos SQL de Azure mediante PowerShell | Microsoft Docs"
description: "Importación de un archivo BACPAC para crear una base de datos SQL de Azure mediante PowerShell"
services: sql-database
documentationcenter: 
author: stevestein
manager: jhubbard
editor: 
ms.assetid: 8d78da13-43fe-4447-92e0-0a41d0321fd4
ms.service: sql-database
ms.custom: migrate and move
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: powershell
ms.workload: data-management
ms.date: 08/31/2016
ms.author: sstein
translationtype: Human Translation
ms.sourcegitcommit: 75bf523679c8d8ad6fbe4a8aa8a561d03008e59b
ms.openlocfilehash: 211f416d05b0ca998cd71a78d091b8efa39f6a7b


---
# <a name="import-a-bacpac-file-to-create-an-azure-sql-database-by-using-powershell"></a>Importación de un archivo BACPAC para crear una base de datos SQL de Azure mediante PowerShell

> [!div class="op_single_selector"]
> * [Portal de Azure](sql-database-import.md)
> * [PowerShell](sql-database-import-powershell.md)
> * [SSMS](sql-database-cloud-migrate-compatible-import-bacpac-ssms.md)
> * [SqlPackage](sql-database-cloud-migrate-compatible-import-bacpac-sqlpackage.md)
> 
> 

Este artículo proporciona instrucciones para crear una base de datos de SQL Azure mediante la importación de un archivo [BACPAC](https://msdn.microsoft.com/library/ee210546.aspx#Anchor_4) con PowerShell.

Se crea la base de datos a partir de un archivo BACPAC (.bacpac) importado desde un contenedor de blob de Azure Storage. Si no dispone de un archivo BACPAC en Azure Storage, consulte [Archivo de una base de datos SQL de Azure en un archivo BACPAC mediante PowerShell](sql-database-export-powershell.md). Si ya tiene un archivo BACPAC que no está en Azure Storage, [use AzCopy para cargarlo fácilmente a su cuenta de Azure Storage](../storage/storage-use-azcopy.md#blob-upload).

> [!NOTE]
> La Base de datos SQL de Azure crea y mantiene automáticamente copias de seguridad para cada base de datos de usuario. Para más información, consulte [Copias de seguridad automatizadas de Base de datos SQL](sql-database-automated-backups.md).
> 
> 

Para importar una base de datos SQL, necesita lo siguiente:

* Una suscripción de Azure. Si necesita una suscripción a Azure, haga clic en **Prueba gratuita** en la parte superior de esta página y, después, vuelva para finalizar este artículo.
* Un archivo BACPAC de la base de datos que quiere importar. El archivo BACPAC debe estar en un contenedor de blobs de la [cuenta de Azure Storage](../storage/storage-create-storage-account.md) .

[!INCLUDE [Start your PowerShell session](../../includes/sql-database-powershell.md)]

## <a name="set-up-the-variables-for-your-environment"></a>Configurar las variables para su entorno
Hay algunas variables que necesita para reemplazar los valores de ejemplo con los valores específicos de la base de datos y la cuenta de almacenamiento.

El nombre del servidor debe ser un servidor que exista actualmente en la suscripción que seleccionó en el paso anterior. Debe ser el servidor en el que desee que se cree la base de datos. No se admite la importación de una base de datos directamente a un grupo elástico. Sin embargo, puede importarla primero como base de datos única y, después, mover la base de datos a un grupo.

El nombre de la base de datos es el nombre que desea para la nueva base de datos.

    $ResourceGroupName = "resource group name"
    $ServerName = "server name"
    $DatabaseName = "database name"


Las variables siguientes proceden de la cuenta de almacenamiento donde se encuentra su BACPAC. En [Azure Portal](https://portal.azure.com), vaya a su cuenta de almacenamiento para obtener estos valores. Para encontrar la clave de acceso principal, haga clic en **Toda la configuración** y luego en **Claves** en la hoja de su cuenta de almacenamiento.

El nombre de blob es el nombre de un archivo BACPAC existente a partir del cual quiere crear la base de datos. Necesita incluir la extensión .bacpac.

    $StorageName = "storageaccountname"
    $StorageKeyType = "StorageAccessKey"
    $StorageUri = "http://$StorageName.blob.core.windows.net/containerName/filename.bacpac"
    $StorageKey = "primaryaccesskey"


Al ejecutar el cmdlet [Get-Credential](https://msdn.microsoft.com/library/azure/hh849815\(v=azure.300\).aspx), se abre una ventana donde se le pide su nombre de usuario y contraseña. Escriba el inicio de sesión de administrador y la contraseña para el servidor de Base de Datos SQL ($ServerName de arriba), no el nombre de usuario y la contraseña para su cuenta de Azure.

    $credential = Get-Credential


## <a name="import-the-database"></a>Importar la base de datos
Este comando envía una solicitud de importación de base de datos para el servicio. Según el tamaño de su base de datos, la operación de importación puede tardar algún tiempo en completarse.

    $importRequest = New-AzureRmSqlDatabaseImport -ResourceGroupName $ResourceGroupName -ServerName $ServerName -DatabaseName $DatabaseName -StorageKeytype $StorageKeyType -StorageKey $StorageKey -StorageUri $StorageUri -AdministratorLogin $credential.UserName -AdministratorLoginPassword $credential.Password -Edition Standard -ServiceObjectiveName S0 -DatabaseMaxSizeBytes 50000


## <a name="monitor-the-progress-of-the-operation"></a>Supervisar el progreso de la restauración
Después de ejecutar [New-AzureRmSqlDatabaseImport](https://msdn.microsoft.com/library/azure/mt707793\(v=azure.300\).aspx), puede comprobar el estado de la solicitud ejecutando [Get-AzureRmSqlDatabaseImportExportStatus](https://msdn.microsoft.com/library/azure/mt707794\(v=azure.300\).aspx).

    Get-AzureRmSqlDatabaseImportExportStatus -OperationStatusLink $importRequest.OperationStatusLink



## <a name="sql-database-powershell-import-script"></a>Importación de un script de PowerShell de Base de datos SQL
    $ResourceGroupName = "resourceGroupName"
    $ServerName = "servername"
    $DatabaseName = "databasename"

    $StorageName = "storageaccountname"
    $StorageKeyType = "StorageAccessKey"
    $StorageUri = "http://$StorageName.blob.core.windows.net/containerName/filename.bacpac"
    $StorageKey = "primaryaccesskey"

    $credential = Get-Credential

    $importRequest = New-AzureRmSqlDatabaseImport -ResourceGroupName $ResourceGroupName -ServerName $ServerName -DatabaseName $DatabaseName -StorageKeytype $StorageKeyType -StorageKey $StorageKey -StorageUri $StorageUri -AdministratorLogin $credential.UserName -AdministratorLoginPassword $credential.Password -Edition Standard -ServiceObjectiveName S0 -DatabaseMaxSizeBytes 50000

    Get-AzureRmSqlDatabaseImportExportStatus -OperationStatusLink $importRequest.OperationStatusLink



## <a name="next-steps"></a>Pasos siguientes
* Para aprender a conectarse a una Base de datos SQL importada y realizar consultas en ella, consulte [Conexión a Base de datos SQL con SQL Server Management Studio y ejecución de una consulta T-SQL de ejemplo](sql-database-connect-query-ssms.md)




<!--HONumber=Dec16_HO3-->


