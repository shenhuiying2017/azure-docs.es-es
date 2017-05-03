---
title: "Exportación de una base de datos de Azure SQL Database a un archivo BACPAC | Microsoft Docs"
description: "Exportación de una base de datos de Azure SQL Database a un archivo BACPAC mediante Azure Portal"
services: sql-database
documentationcenter: 
author: CarlRabeler
manager: jhubbard
editor: 
ms.assetid: 41d63a97-37db-4e40-b652-77c2fd1c09b7
ms.service: sql-database
ms.custom: move data
ms.devlang: NA
ms.date: 04/05/2017
ms.author: carlrab
ms.workload: data-management
ms.topic: article
ms.tgt_pltfrm: NA
translationtype: Human Translation
ms.sourcegitcommit: 757d6f778774e4439f2c290ef78cbffd2c5cf35e
ms.openlocfilehash: eadd300dcda2f160589c5e8e4fb7508445ef9944
ms.lasthandoff: 04/10/2017


---
# <a name="export-an-azure-sql-database-to-a-bacpac-file"></a>Exportación de una base de datos de Azure SQL Database a un archivo BACPAC

En este artículo se explica describe la exportación de una base de datos de Azure SQL Database a un archivo [BACPAC](https://msdn.microsoft.com/library/ee210546.aspx#Anchor_4). En este artículo se tratan los siguientes métodos:
- [Azure Portal](https://portal.azure.com)
- la utilidad de línea de comandos [SqlPackage](https://msdn.microsoft.com/library/hh550080.aspx)
- el cmdlet [New-AzureRmSqlDatabaseExport](https://docs.microsoft.com/powershell/resourcemanager/azurerm.sql/v2.7.0/new-azurermsqldatabaseexport)
- el Asistente para [exportación de una aplicación de capa de datos](https://docs.microsoft.com/sql/relational-databases/data-tier-applications/export-a-data-tier-application) de [SQL Server Management Studio](https://msdn.microsoft.com/library/ms174173.aspx).

> [!IMPORTANT] 
> La exportación automática de Azure SQL Database Automated Export se encuentra ahora en versión preliminar y se retirará el 1 de marzo de 2017. A partir del 1 de diciembre de 2016, ya no podrá configurar la exportación automatizada en cualquier base de datos SQL. Los trabajos de exportación automatizada existentes continuarán funcionando hasta que el 1 de marzo de 2017. A partir del 1 de diciembre de 2016, podrá usar la [retención de copias de seguridad a largo plazo](sql-database-long-term-retention.md
) o [Azure Automation](https://github.com/Microsoft/azure-docs-pr/blob/2461f706f8fc1150e69312098640c0676206a531/articles/automation/automation-intro.md) para archivar las bases de datos SQL periódicamente mediante PowerShell de acuerdo con la programación que elija. Para obtener un script de ejemplo, puede descargar [este](https://github.com/Microsoft/sql-server-samples/tree/master/samples/manage/azure-automation-automated-export), que está disponible en GitHub.
>

> [!IMPORTANT] 
> La exportación automática de Azure SQL Database Automated Export se encuentra ahora en versión preliminar y se retirará el 1 de marzo de 2017. A partir del 1 de diciembre de 2016, ya no podrá configurar la exportación automatizada en cualquier base de datos SQL. Los trabajos de exportación automatizada existentes continuarán funcionando hasta que el 1 de marzo de 2017. A partir del 1 de diciembre de 2016, podrá usar la [retención de copias de seguridad a largo plazo](sql-database-long-term-retention.md
) o [Azure Automation](https://github.com/Microsoft/azure-docs-pr/blob/2461f706f8fc1150e69312098640c0676206a531/articles/automation/automation-intro.md) para archivar las bases de datos SQL periódicamente mediante PowerShell de acuerdo con la programación que elija. Para obtener un script de ejemplo, puede descargar [este](https://github.com/Microsoft/sql-server-samples/tree/master/samples/manage/azure-automation-automated-export), que está disponible en GitHub.
>

## <a name="overview"></a>Información general

Cuando necesite exportar una base de datos para archivar o migrar a otra plataforma, puede exportar el esquema de base de datos y los datos a un archivo BACPAC. Un archivo BACPAC es un archivo ZIP con la BACPAC que contiene los datos y los metadatos de una base de datos de SQL Server. Un archivo BACPAC se puede almacenar en Azure Blob Storage o en una ubicación local y, luego, volver a importarlo en Azure SQL Database o en una instalación local de SQL Server. 

> [!IMPORTANT]
> Si va a exportar desde SQL Server como paso previo a la migración a Azure SQL Database, consulte [Migración de una base de datos SQL Server a Azure SQL Database](sql-database-cloud-migrate.md).
> 

## <a name="considerations"></a>Consideraciones

* Para que una exportación sea transaccionalmente coherente, debe asegurarse de que no haya ninguna actividad de escritura durante la exportación, o bien de exportar desde una [copia transaccionalmente coherente](sql-database-copy.md) de Azure SQL Database.
* Si va a exportar a Blob Storage, el tamaño máximo de un archivo BACPAC es 200 GB. Para archivar un archivo BACPAC de mayor tamaño, exporte al almacenamiento local.
* No se admite la exportación de un archivo BACPAC en Azure Premium Storage con los métodos que se describen en este artículo.
* Si la operación de exportación desde Azure SQL Database demora más de 20 horas, es posible que se cancele. Para aumentar el rendimiento durante la exportación, puede hacer lo siguiente:
  * Aumentar temporalmente el nivel de servicio.
  * Detener toda actividad de lectura y escritura durante la exportación.
  * Use un [índice agrupado](https://msdn.microsoft.com/library/ms190457.aspx) con valores distintos de NULL en todas las tablas de gran tamaño. Sin índices agrupados, la exportación podría no producirse si tarda más de 6-12 horas. Esto se debe a que el servicio de exportación necesita completar el recorrido de tabla para tratar de exportar toda la tabla. Una buena forma de determinar si las tablas están optimizadas para la exportación es ejecutar **DBCC SHOW_STATISTICS** y asegurarse de que *RANGE_HI_KEY* no es NULL y su valor tiene buena distribución. Para obtener más información, consulte [DBCC SHOW_STATISTICS](https://msdn.microsoft.com/library/ms174384.aspx).

> [!NOTE]
> Los BACPAC no están diseñados para usarse en operaciones de copia de seguridad y restauración. Azure SQL Database crea automáticamente copias de seguridad para cada base de datos de usuario. Para detalles, consulte [Información general sobre la continuidad empresarial](sql-database-business-continuity.md) y [Copias de seguridad de SQL Database](sql-database-automated-backups.md).  
> 

## <a name="azure-portal"></a>Azure Portal

Para exportar una base de datos mediante Azure Portal, abra la página de la base de datos y haga clic en **Exportar** en la barra de herramientas. Especifique el nombre de archivo *.bacpac, indique la cuenta de Azure Storage y el contenedor de exportación, y escriba las credenciales para conectarse a la base de datos de origen.  

   ![Exportación de base de datos](./media/sql-database-export/database-export.png)

Para supervisar el progreso de la operación de exportación, abra la página del servidor lógico que contiene la base de datos que se va a exportar. Desplácese hacia abajo hasta **Operaciones** y, después, haga clic en el historial de **importación y exportación**.

## <a name="sqlpackage-utility"></a>Utilidad de SQLPackage

Para exportar una base de datos SQL mediante la utilidad de línea de comandos [SqlPackage](https://msdn.microsoft.com/library/hh550080.aspx), consulte [Parámetros y propiedades de la exportación](https://msdn.microsoft.com/library/hh550080.aspx#Export Parameters and Properties). La utilidad SQLPackage incluye las versiones más recientes de [SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx) y [SQL Server Data Tools para Visual Studio](https://msdn.microsoft.com/library/mt204009.aspx), o bien puede descargar la versión más reciente de [SqlPackage](https://www.microsoft.com/download/details.aspx?id=53876) directamente desde el Centro de descarga de Microsoft.

Se recomienda usar la utilidad SQLPackage para la escala y el rendimiento en la mayoría de los entornos de producción. Consulte cómo [migrar de SQL Server a Azure SQL Database con archivos BACPAC](https://blogs.msdn.microsoft.com/sqlcat/2016/10/20/migrating-from-sql-server-to-azure-sql-database-using-bacpac-files/) en el blog de Customer Advisory Team de SQL Server.

## <a name="sql-server-management-studio"></a>SQL Server Management Studio

Las versiones más recientes de SQL Server Management Studio también proporcionan un asistente para exportar una base de datos de Azure SQL Database a un archivo bacpac. Consulte [Export a Data-tier Application](https://docs.microsoft.com/sql/relational-databases/data-tier-applications/export-a-data-tier-application) (Exportación de una aplicación de la capa de datos).

## <a name="powershell"></a>PowerShell

Use el cmdlet [AzureRmSqlDatabaseImport New](https://docs.microsoft.com/powershell/resourcemanager/azurerm.sql/v2.7.0/new-azurermsqldatabaseexport) para enviar una solicitud de exportación base de datos al servicio Azure SQL Database. Según el tamaño de la base de datos, la operación de exportación puede tardar algún tiempo en completarse.

 ```powershell
 $exportRequest = New-AzureRmSqlDatabaseExport -ResourceGroupName $ResourceGroupName -ServerName $ServerName `
   -DatabaseName $DatabaseName -StorageKeytype $StorageKeytype -StorageKey $StorageKey -StorageUri $BacpacUri `
   -AdministratorLogin $creds.UserName -AdministratorLoginPassword $creds.Password
 ```

Para comprobar el estado de la solicitud de exportación, utilice el cmdlet [AzureRmSqlDatabaseImportExportStatus Get](https://docs.microsoft.com/powershell/resourcemanager/azurerm.sql/v2.7.0/get-azurermsqldatabaseimportexportstatus). Si se ejecuta inmediatamente después de la solicitud, normalmente devuelve **Status: InProgress**. Cuando vea **Estado: Correcto**, la exportación se habrá completado.

```powershell
$importStatus = Get-AzureRmSqlDatabaseImportExportStatus -OperationStatusLink $importRequest.OperationStatusLink
[Console]::Write("Exporting")
while ($importStatus.Status -eq "InProgress")
{
    $importStatus = Get-AzureRmSqlDatabaseImportExportStatus -OperationStatusLink $importRequest.OperationStatusLink
    [Console]::Write(".")
    Start-Sleep -s 10
}
[Console]::WriteLine("")
$importStatus
```

### <a name="export-sql-database-example"></a>Ejemplo de exportación de base de datos SQL
En el ejemplo siguiente se exporta una base de datos de Azure SQL Database existente a BACPAC y, a continuación, se muestra cómo comprobar el estado de la operación de exportación.

Para ejecutar el ejemplo, hay algunas variables que debe reemplazar por los valores específicos de la cuenta de almacenamiento y base de datos. En [Azure Portal](https://portal.azure.com), vaya a la cuenta de almacenamiento para obtener el nombre de la misma, el nombre del contenedor de blobs y el valor de clave. Puede encontrar la clave haciendo clic en **Teclas de acceso** en la hoja de su cuenta de almacenamiento.

Reemplace `VARIABLE-VALUES` por los valores para los recursos específicos de Azure. El nombre de la base de datos es la base de datos existente que desea exportar.

```powershell
$subscriptionId = "YOUR AZURE SUBSCRIPTION ID"

Login-AzureRmAccount
Set-AzureRmContext -SubscriptionId $subscriptionId

# Database to export
$DatabaseName = "DATABASE-NAME"
$ResourceGroupName = "RESOURCE-GROUP-NAME"
$ServerName = "SERVER-NAME
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
```

## <a name="next-steps"></a>Pasos siguientes

* Para obtener información acerca de la retención a largo plazo de la copia de seguridad de una base de datos de Azure SQL Database como alternativa a la exportación de una base de datos para archivarla, consulte [Retención a largo plazo de copias de seguridad](sql-database-long-term-retention.md).
- Consulte cómo [migrar de SQL Server a Azure SQL Database con archivos BACPAC](https://blogs.msdn.microsoft.com/sqlcat/2016/10/20/migrating-from-sql-server-to-azure-sql-database-using-bacpac-files/) en el blog de Customer Advisory Team de SQL Server.
* Para aprender a importar un BACPAC a una base de datos de SQL Server, consulte [Importación de un BACPCAC en una base de datos de SQL Server](https://msdn.microsoft.com/library/hh710052.aspx).
* Para aprender a exportar un BACPAC de una base de datos de SQL Server, consulte [Export a Data-tier Application](https://docs.microsoft.com/sql/relational-databases/data-tier-applications/export-a-data-tier-application) (Exportación de una aplicación de la capa de datos) y [Migración de la primera base de datos](sql-database-migrate-your-sql-server-database.md).

