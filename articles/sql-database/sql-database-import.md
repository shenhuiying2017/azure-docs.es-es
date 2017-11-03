---
title: "Importación de un archivo BACPAC para crear una base de datos de Azure SQL | Microsoft Docs"
description: "Cree una base de datos de Azure SQL Database nueva mediante la importación de archivo BACPAC."
services: sql-database
documentationcenter: 
author: CarlRabeler
manager: jhubbard
editor: 
ms.assetid: cf9a9631-56aa-4985-a565-1cacc297871d
ms.service: sql-database
ms.custom: load & move data
ms.devlang: NA
ms.date: 06/26/2017
ms.author: carlrab
ms.workload: Active
ms.topic: article
ms.tgt_pltfrm: NA
ms.openlocfilehash: 34dee9511822acec46ba4854729939b84f3c06c6
ms.sourcegitcommit: e5355615d11d69fc8d3101ca97067b3ebb3a45ef
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/31/2017
---
# <a name="import-a-bacpac-file-to-a-new-azure-sql-database"></a>Importación de un archivo BACPAC en una base de datos de Azure SQL Database

Cuando sea preciso importar una base de datos de un archivo o al realizar una migración desde otra plataforma, el esquema y los datos de la base de datos y los datos se pueden importar desde un archivo [BACPAC](https://msdn.microsoft.com/library/ee210546.aspx#Anchor_4). Un archivo BACPAC es un archivo ZIP con una extensión de BACPAC que contiene los metadatos y los datos de una base de datos de SQL Server. Los archivos BACPAC se pueden importar de Azure Blob Storage (solo almacenamiento estándar) o del almacenamiento local en una ubicación local. Para maximizar la velocidad de importación, es aconsejable especificar un nivel de rendimiento y una capa de servicio superiores, como un P6 y, después escalarlos hacia abajo tanto como sea apropiado cuando la importación se haya realizado correctamente. Además, el nivel de compatibilidad de la base de datos después de la importación se basa en el nivel de compatibilidad de la base de datos de origen. 

> [!IMPORTANT] 
> Después de migrar la base de datos a Azure SQL Database, puede elegir utilizar la base de datos en su nivel de compatibilidad actual (nivel 100 para la base de datos AdventureWorks2008R2) o en un nivel superior. Para más información acerca de las implicaciones y las opciones para la utilización de una base de datos en un nivel de compatibilidad específico, consulte [ALTER DATABASE (Transact-SQL) Compatibility Level](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql-compatibility-level) [Nivel de compatibilidad de ALTER DATABASE (Transact-SQL)]. Vea también [ALTER DATABASE SCOPED CONFIGURATION](https://docs.microsoft.com/sql/t-sql/statements/alter-database-scoped-configuration-transact-sql) para obtener información sobre los valores de nivel de base de datos adicionales relacionados con los niveles de compatibilidad.   >

> [!NOTE]
> Para importar un archivo BACPAC en una base de datos nueva, primero es preciso crear un servidor lógico de Azure SQL Database. Para obtener un tutorial que le muestre cómo migrar una base de datos de SQL Server a Azure SQL Database mediante SQLPackage, consulte [Migrate your SQL Server database to Azure SQL Database](sql-database-migrate-your-sql-server-database.md) (Migración de una base de datos de SQL Server a Azure SQL Database)
>

## <a name="import-from-a-bacpac-file-using-azure-portal"></a>Importación de un archivo BACPAC mediante Azure Portal

En este artículo encontrará instrucciones para crear una base de datos de Azure SQL Database a partir de un archivo BACPAC almacenado en Azure Blob Storage mediante [Azure Portal](https://portal.azure.com). La importación mediante Azure Portal solo admite la importación de un archivo BACPAC desde Azure Blob Storage.

Para importar una base de datos mediante Azure Portal, abra la página del servidor para asociar la base de datos y, después, haga clic en **Importar** en la barra de herramientas. Especifique la cuenta de almacenamiento y el contenedor, y seleccione el archivo BACPAC que desea importar. Seleccione el tamaño de la nueva base de datos (normalmente el mismo que el origen) y proporcione las credenciales del servidor SQL Server de destino.  

   ![Importación de base de datos](./media/sql-database-import/import.png)

Para supervisar el progreso de la operación de importación, abra la página del servidor lógico que contiene la base de datos que se va a importar. Desplácese hacia abajo hasta **Operaciones** y, después, haga clic en el historial de **importación y exportación**.

### <a name="monitor-the-progress-of-an-import-operation"></a>Supervisión del progreso de la operación de importación

Para supervisar el progreso de la operación de importación, abra la página del servidor lógico a la que se va a la importar base de datos. Desplácese hacia abajo hasta **Operaciones** y, después, haga clic en el historial de **importación y exportación**.
   
   ![importar](./media/sql-database-import/import-history.png)![estado de la importación](./media/sql-database-import/import-status.png)

Para comprobar que la base de datos está activa en el servidor, haga clic en **Bases de datos SQL** y compruebe que la nueva base de datos esté **En línea**.

## <a name="import-from-a-bacpac-file-using-sqlpackage"></a>Importación de un archivo BACPAC mediante SQLPackage

Para importar una base de datos SQL mediante la utilidad de línea de comandos [SqlPackage](https://msdn.microsoft.com/library/hh550080.aspx), consulte [Parámetros y propiedades de la importación](https://msdn.microsoft.com/library/hh550080.aspx#Import Parameters and Properties). La utilidad SQLPackage incluye las versiones más recientes de [SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx) y [SQL Server Data Tools para Visual Studio](https://msdn.microsoft.com/library/mt204009.aspx), o bien puede descargar la versión más reciente de [SqlPackage](https://www.microsoft.com/download/details.aspx?id=53876) directamente desde el Centro de descarga de Microsoft.

Se recomienda usar la utilidad SQLPackage para la escala y el rendimiento en la mayoría de los entornos de producción. Consulte cómo [migrar de SQL Server a Azure SQL Database con archivos BACPAC](https://blogs.msdn.microsoft.com/sqlcat/2016/10/20/migrating-from-sql-server-to-azure-sql-database-using-bacpac-files/) en el blog de Customer Advisory Team de SQL Server.

Consulte el siguiente comando SQLPackage para ver un script de ejemplo para importar la base de datos **AdventureWorks2008R2** desde el almacenamiento local a un servidor lógico de Azure SQL Database, que en este ejemplo se llama **mynewserver20170403**. Este script muestra la creación de una base de datos nueva denominada **myMigratedDatabase**, con una capa de servicio de **Premium** y un objetivo de servicio de **P6**. Cambie estos valores según sea apropiado para su entorno.

```cmd
SqlPackage.exe /a:import /tcs:"Data Source=mynewserver20170403.database.windows.net;Initial Catalog=myMigratedDatabase;User Id=ServerAdmin;Password=<change_to_your_password>" /sf:AdventureWorks2008R2.bacpac /p:DatabaseEdition=Premium /p:DatabaseServiceObjective=P6
```

> [!IMPORTANT]
> Un servidor lógico de Azure SQL Database escucha en el puerto 1433. Si intenta conectarse a un servidor lógico de Azure SQL Database desde dentro de un firewall corporativo, este puerto debe estar abierto en el firewall corporativo para poder conectarse correctamente.
>

Este ejemplo muestra cómo importar una base de datos con SqlPackage.exe con Autenticación universal de Active Directory:

```cmd
SqlPackage.exe /a:Import /sf:testExport.bacpac /tdn:NewDacFX /tsn:apptestserver.database.windows.net /ua:True /tid:"apptest.onmicrosoft.com"
```

## <a name="import-from-a-bacpac-file-using-powershell"></a>Importación de un archivo BACPAC mediante PowerShell

Use el cmdlet [AzureRmSqlDatabaseImport New](/powershell/module/azurerm.sql/new-azurermsqldatabaseimport) para enviar una solicitud de importación base de datos al servicio Azure SQL Database. Según el tamaño de su base de datos, la operación de importación puede tardar algún tiempo en completarse.

 ```powershell
 $importRequest = New-AzureRmSqlDatabaseImport -ResourceGroupName "myResourceGroup" `
    -ServerName $servername `
    -DatabaseName "MyImportSample" `
    -DatabaseMaxSizeBytes "262144000" `
    -StorageKeyType "StorageAccessKey" `
    -StorageKey $(Get-AzureRmStorageAccountKey -ResourceGroupName "myResourceGroup" -StorageAccountName $storageaccountname).Value[0] `
    -StorageUri "http://$storageaccountname.blob.core.windows.net/importsample/sample.bacpac" `
    -Edition "Standard" `
    -ServiceObjectiveName "P6" `
    -AdministratorLogin "ServerAdmin" `
    -AdministratorLoginPassword $(ConvertTo-SecureString -String "ASecureP@assw0rd" -AsPlainText -Force)

 ```

Para comprobar el estado de la solicitud de importación, utilice el cmdlet [AzureRmSqlDatabaseImportExportStatus Get](/powershell/module/azurerm.sql/get-azurermsqldatabaseimportexportstatus). Si se ejecuta inmediatamente después de la solicitud, normalmente devuelve **Status: InProgress**. Cuando vea **Status: Succeeded**, la importación se habrá completado.

```powershell
$importStatus = Get-AzureRmSqlDatabaseImportExportStatus -OperationStatusLink $importRequest.OperationStatusLink
[Console]::Write("Importing")
while ($importStatus.Status -eq "InProgress")
{
    $importStatus = Get-AzureRmSqlDatabaseImportExportStatus -OperationStatusLink $importRequest.OperationStatusLink
    [Console]::Write(".")
    Start-Sleep -s 10
}
[Console]::WriteLine("")
$importStatus
```

> [!TIP]
Para ver otro ejemplo de script, consulte [Importación de una base de datos desde un archivo BACPAC](scripts/sql-database-import-from-bacpac-powershell.md).

## <a name="next-steps"></a>Pasos siguientes
* Para aprender a conectarse a una base de datos de SQL Database importada y realizar consultas en ella, consulte [Conexión a SQL Database con SQL Server Management Studio y ejecución de una consulta T-SQL de ejemplo](sql-database-connect-query-ssms.md).
* Consulte cómo [migrar de SQL Server a Azure SQL Database con archivos BACPAC](https://blogs.msdn.microsoft.com/sqlcat/2016/10/20/migrating-from-sql-server-to-azure-sql-database-using-bacpac-files/) en el blog de Customer Advisory Team de SQL Server.
* Para obtener información sobre el proceso de migración de bases de datos de SQL Server completo, incluidas las recomendaciones de rendimiento, consulte [Migración de una base de datos de SQL Server a Azure SQL Database](sql-database-cloud-migrate.md).



