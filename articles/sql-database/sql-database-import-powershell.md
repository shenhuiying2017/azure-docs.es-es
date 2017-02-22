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
ms.date: 02/07/2017
ms.author: sstein
translationtype: Human Translation
ms.sourcegitcommit: e6f0d661465c813ec310b8c69ab1ee06e4f95401
ms.openlocfilehash: 45ec817e62e7967549602adfd2c9d2d3f2484987


---
# <a name="import-a-bacpac-file-to-create-an-azure-sql-database-by-using-powershell"></a>Importación de un archivo BACPAC para crear una base de datos SQL de Azure mediante PowerShell

Este artículo proporciona instrucciones para crear una base de datos de SQL Azure mediante la importación de un archivo [BACPAC](https://msdn.microsoft.com/library/ee210546.aspx#Anchor_4) con PowerShell.

## <a name="prequisites"></a>Requisitos previos

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
* Para aprender a conectarse a una base de datos SQL importada y realizar consultas en ella, consulte [Conexión a SQL Database con SQL Server Management Studio y ejecución de una consulta T-SQL de ejemplo](sql-database-connect-query-ssms.md).
* Consulte cómo [migrar de SQL Server a Azure SQL Database con archivos BACPAC](https://blogs.msdn.microsoft.com/sqlcat/2016/10/20/migrating-from-sql-server-to-azure-sql-database-using-bacpac-files/) en el blog de Customer Advisory Team de SQL Server.
* Para obtener información sobre el proceso de migración de bases de datos de SQL Server completo, incluidas las recomendaciones de rendimiento, consulte [Migración de una base de datos de SQL Server a Azure SQL Database](sql-database-cloud-migrate.md).





<!--HONumber=Feb17_HO2-->


