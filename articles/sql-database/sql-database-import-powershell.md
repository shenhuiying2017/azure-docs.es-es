<properties
    pageTitle="Importar un archivo BACPAC para crear una base de datos SQL de Azure mediante PowerShell | Microsoft Azure"
    description="Importar un archivo BACPAC para crear una nueva base de datos de SQL Azure con PowerShell"
    services="sql-database"
    documentationCenter=""
    authors="stevestein"
    manager="jhubbard"
    editor=""/>

<tags
    ms.service="sql-database"
    ms.devlang="NA"
    ms.topic="article"
    ms.tgt_pltfrm="powershell"
    ms.workload="data-management"
    ms.date="07/06/2016"
    ms.author="sstein"/>

# Importar un archivo BACPAC para crear una nueva base de datos de SQL Azure con PowerShell

**Base de datos única**

> [AZURE.SELECTOR]
- [Portal de Azure](sql-database-import.md)
- [PowerShell](sql-database-import-powershell.md)
- [SSMS](sql-database-cloud-migrate-compatible-import-bacpac-ssms.md)
- [SqlPackage](sql-database-cloud-migrate-compatible-import-bacpac-sqlpackage.md)

Este artículo proporciona instrucciones para crear una base de datos de SQL Azure mediante la importación de un archivo [BACPAC](https://msdn.microsoft.com/library/ee210546.aspx#Anchor_4) con PowerShell.

Se crea la base de datos desde un archivo BACPAC importado de un contenedor de blob de almacenamiento de Azure. Si no dispone de un archivo .bacpac en el almacenamiento de Azure, puede crear uno siguiendo los pasos descritos en [Archivo de una base de datos SQL de Azure en un archivo BACPAC mediante PowerShell](sql-database-export-powershell.md).

> [AZURE.NOTE] La Base de datos SQL de Azure crea y mantiene automáticamente copias de seguridad para cada base de datos de usuario. Para más información, consulte [Copias de seguridad automáticas de Base de datos de SQL](sql-database-automated-backups.md).


Para importar una base de datos SQL, necesita lo siguiente:

- Una suscripción de Azure. Si necesita una suscripción a Azure, haga clic en la opción **Evaluación gratuita** situada en la parte superior de esta página y, a continuación, vuelva para finalizar este artículo.
- Un archivo .bacpac de la base de datos que quiere importar. El BACPAC debe estar en un contenedor de blobs de [cuenta de almacenamiento de Azure (clásica)](../storage/storage-create-storage-account.md).



[AZURE.INCLUDE [Inicio de una sesión de PowerShell](../../includes/sql-database-powershell.md)]



## Configurar las variables para su entorno

Hay algunas variables que necesita para reemplazar los valores de ejemplo con los valores específicos de la base de datos y la cuenta de almacenamiento.

El nombre del servidor debe ser un servidor que existe actualmente en la suscripción que seleccionó en el paso anterior. Debe ser el servidor en el que desee que se cree la base de datos. Tenga en cuenta que no se admite la importación de una base de datos directamente en un grupo elástico. Sin embargo, puede importarla primero en una base de datos única y luego mover la base de datos a un grupo.

El nombre de la base de datos es el nombre que desea para la nueva base de datos.

    $ResourceGroupName = "resource group name"
    $ServerName = "server name"
    $DatabaseName = "database name"


Las variables siguientes proceden de la cuenta de almacenamiento donde se encuentra su BACPAC. En el [Portal de Azure](https://portal.azure.com), vaya a su cuenta de almacenamiento para obtener estos valores. Para encontrar la clave de acceso principal, vaya a la hoja de su cuenta de almacenamiento y haga clic en **Toda la configuración** y, a continuación, en **Claves**.

El nombre de blob es el nombre de un archivo de .bacpac existente desde el que quiere crear la base de datos. Necesita incluir la extensión .bacpac.

    $StorageName = "storageaccountname"
    $StorageKeyType = "StorageAccessKey"
    $StorageUri = "http://$StorageName.blob.core.windows.net/containerName/filename.bacpac"
    $StorageKey = "primaryaccesskey"


Al ejecutar el cmdlet **Get-Credential**, se abre una ventana en la que se le pide su nombre de usuario y contraseña. Escriba el inicio de sesión de administrador y la contraseña para el servidor de Base de Datos SQL ($ServerName de arriba), no el nombre de usuario y la contraseña para su cuenta de Azure.

    $credential = Get-Credential


## Importar la base de datos

Este comando envía una solicitud de importación de base de datos para el servicio. Según el tamaño de su base de datos, la operación de importación puede tardar algún tiempo en completarse.

    $importRequest = New-AzureRmSqlDatabaseImport –ResourceGroupName $ResourceGroupName –ServerName $ServerName –DatabaseName $DatabaseName –StorageKeytype $StorageKeyType –StorageKey $StorageKey -StorageUri $StorageUri –AdministratorLogin $credential.UserName –AdministratorLoginPassword $credential.Password –Edition Standard –ServiceObjectiveName S0 -DatabaseMaxSizeBytes 50000


## Supervisar el progreso de la restauración

Después de ejecutar **New-AzureRmSqlDatabaseImport**, puede comprobar el estado de la solicitud.

Si se ejecuta esto de inmediato después de la solicitud, se devuelve normalmente **Estado: pendiente** o **Estado: en curso**. Ejecute varias veces hasta que vea **Estado: completado** en el resultado.

Al ejecutar este comando se le solicitará una contraseña. Escriba la contraseña de administrador de su SQL server.


    Get-AzureRmSqlDatabaseImportExportStatus -OperationStatusLink $importRequest .OperationStatusLink



## Importación de un script de PowerShell de Base de datos SQL


    $ResourceGroupName = "resourceGroupName"
    $ServerName = "servername"
    $DatabaseName = "databasename"

    $StorageName = "storageaccountname"
    $StorageKeyType = "StorageAccessKey"
    $StorageUri = "http://$StorageName.blob.core.windows.net/containerName/filename.bacpac"
    $StorageKey = "primaryaccesskey"

    $credential = Get-Credential

    $importRequest = New-AzureRmSqlDatabaseImport –ResourceGroupName $ResourceGroupName –ServerName $ServerName –DatabaseName $DatabaseName –StorageKeytype $StorageKeyType –StorageKey $StorageKey -StorageUri $StorageUri –AdministratorLogin $credential.UserName –AdministratorLoginPassword $credential.Password –Edition Standard –ServiceObjectiveName S0 -DatabaseMaxSizeBytes 50000

    Get-AzureRmSqlDatabaseImportExportStatus -OperationStatusLink $importRequest .OperationStatusLink



## Pasos siguientes

- Para aprender a conectarse a una Base de datos SQL importada y realizar consultas en ella, consulte [Conexión a Base de datos SQL con SQL Server Management Studio y ejecución de una consulta T-SQL de ejemplo](sql-database-connect-query-ssms.md)

<!---HONumber=AcomDC_0803_2016-->