<properties
    pageTitle="Archivo de una base de datos SQL de Azure en un archivo BACPAC mediante PowerShell"
    description="Archivo de una base de datos SQL de Azure en un archivo BACPAC mediante PowerShell"
	services="sql-database"
	documentationCenter=""
	authors="stevestein"
	manager="jhubbard"
	editor=""/>

<tags
	ms.service="sql-database"
	ms.devlang="NA"
	ms.date="08/01/2016"
	ms.author="sstein"
	ms.workload="data-management"
	ms.topic="article"
	ms.tgt_pltfrm="NA"/>


# Archivo de una base de datos SQL de Azure en un archivo BACPAC mediante PowerShell

> [AZURE.SELECTOR]
- [Portal de Azure](sql-database-export.md)
- [PowerShell](sql-database-export-powershell.md)


En este artículo se proporcionan instrucciones para archivar la base de datos SQL de Azure en un archivo [BACPAC](https://msdn.microsoft.com/library/ee210546.aspx#Anchor_4) guardado en Almacenamiento de blobs de Azure. Este artículo muestra cómo hacerlo mediante PowerShell.

Cuando necesite crear un archivo de una base de datos de SQL Azure, puede exportar los datos y el esquema de base de datos a un archivo BACPAC. Un archivo BACPAC es, sencillamente, un archivo ZIP con la extensión .bacpac. El archivo BACPAC puede almacenarse posteriormente en Almacenamiento de blobs de Azure o en el almacenamiento local en una ubicación local. También se puede importar en la base de datos de SQL Azure o en una instalación de SQL Server local.

**Consideraciones**

- Para que un archivo sea transaccionalmente coherente, debe asegurarse de que no haya ninguna actividad de escritura durante la exportación, o bien de exportar desde una [copia transaccionalmente coherente](sql-database-copy.md) de Base de datos de SQL Azure.
- El tamaño máximo de un archivo BACPAC guardado en el almacenamiento de blobs de Azure es de 200 GB. Use la utilidad de símbolo del sistema [SqlPackage](https://msdn.microsoft.com/library/hh550080.aspx) para guardar archivos BACPAC de mayor tamaño en un almacenamiento local. Esta utilidad se incluye con Visual Studio y SQL Server. También puede [descargar](https://msdn.microsoft.com/library/mt204009.aspx) la versión más reciente de SQL Server Data Tools para obtener esta utilidad.
- No se puede archivar en Almacenamiento premium de Azure usando un archivo BACPAC.
- Si la operación de exportación tarda más de 20 horas, es posible que se cancele. Para aumentar el rendimiento durante la exportación, puede hacer lo siguiente:
 - Aumentar temporalmente el nivel de servicio.
 - Detener toda actividad de lectura y escritura durante la exportación.
 - Usar un índice agrupado en todas las tablas de gran tamaño. Sin índices agrupados, la exportación podría no producirse si tarda más de 6-12 horas. Esto se debe a que el servicio de exportación necesita completar el recorrido de tabla para exportar toda la tabla.

> [AZURE.NOTE] Los BACPAC no están diseñados para usarse en operaciones de copia de seguridad y restauración. La Base de datos SQL de Azure crea automáticamente copias de seguridad para cada base de datos de usuario. Para más información, consulte [Copias de seguridad automatizadas de Base de datos SQL](sql-database-automated-backups.md).

Para completar este artículo, necesitará lo siguiente:

- Una suscripción de Azure.
- Una base de datos SQL de Azure.
- Una [cuenta de almacenamiento estándar de Azure](../storage/storage-create-storage-account.md) con un contenedor de blobs para almacenar el BACPAC.


[AZURE.INCLUDE [Inicio de una sesión de PowerShell](../../includes/sql-database-powershell.md)]




## Exportación de la base de datos

El cmdlet [New-AzureRmSqlDatabaseExport](https://msdn.microsoft.com/library/mt707796.aspx) envía una solicitud de base de datos de exportación al servicio. Según el tamaño de la base de datos, la operación de exportación puede tardar algún tiempo en completarse.

> [AZURE.IMPORTANT] Para garantizar un archivo BACPAC transaccionalmente coherente, primero debe [crear una copia de la base de datos](sql-database-copy-powershell.md) y después exportar dicha copia.


     $exportRequest = New-AzureRmSqlDatabaseExport –ResourceGroupName $ResourceGroupName –ServerName $ServerName `
       –DatabaseName $DatabaseName –StorageKeytype $StorageKeytype –StorageKey $StorageKey -StorageUri $BacpacUri `
       –AdministratorLogin $creds.UserName –AdministratorLoginPassword $creds.Password


## Supervisar el progreso de la operación de exportación

Después de ejecutar **New-AzureRmSqlDatabaseExport**, puede comprobar el estado de la solicitud ejecutando [Get-AzureRmSqlDatabaseImportExportStatus](https://msdn.microsoft.com/library/mt707794.aspx). Si se ejecuta esto de inmediato después de la solicitud, se devuelve normalmente **Estado: en curso**. Cuando vea **Estado: correcto** la exportación se ha completado.


    Get-AzureRmSqlDatabaseImportExportStatus -OperationStatusLink $exportRequest.OperationStatusLink



## Ejemplo de exportación de base de datos SQL

En el ejemplo siguiente se exporta una base de datos SQL existente a BACPAC y, a continuación, se muestra cómo comprobar el estado de la operación de exportación.

Para ejecutar el ejemplo, hay algunas variables que debe reemplazar por los valores específicos de la cuenta de almacenamiento y base de datos. En el [Portal de Azure](https://portal.azure.com), vaya a la cuenta de almacenamiento para obtener el nombre de la cuenta de almacenamiento, el nombre del contenedor de blob y el valor de clave. Puede encontrar la clave haciendo clic en **Claves de acceso** en la hoja de su cuenta de almacenamiento.

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
    $securePassword = ConvertTo-SecureString –String $serverPassword –AsPlainText -Force
    $creds = New-Object –TypeName System.Management.Automation.PSCredential –ArgumentList $serverAdmin, $securePassword

    # Generate a unique filename for the BACPAC
    $bacpacFilename = $DatabaseName + (Get-Date).ToString("yyyyMMddHHmm") + ".bacpac"

    # Storage account info for the BACPAC
    $BaseStorageUri = "https://STORAGE-NAME.blob.core.windows.net/BLOB-CONTAINER-NAME/"
    $BacpacUri = $BaseStorageUri + $bacpacFilename
    $StorageKeytype = "StorageAccessKey"
    $StorageKey = "YOUR STORAGE KEY"

    $exportRequest = New-AzureRmSqlDatabaseExport –ResourceGroupName $ResourceGroupName –ServerName $ServerName `
       –DatabaseName $DatabaseName –StorageKeytype $StorageKeytype –StorageKey $StorageKey -StorageUri $BacpacUri `
       –AdministratorLogin $creds.UserName –AdministratorLoginPassword $creds.Password
    $exportRequest

    # Check status of the export
    Get-AzureRmSqlDatabaseImportExportStatus -OperationStatusLink $exportRequest.OperationStatusLink



## Pasos siguientes

- Para obtener información sobre cómo importar una base de datos SQL de Azure con PowerShell, consulte [Importar un archivo BACPAC para crear una base de datos SQL de Azure mediante PowerShell](sql-database-import-powershell.md).


## Recursos adicionales

- [New-AzureRmSqlDatabaseExport](https://msdn.microsoft.com/library/mt707796.aspx)
- [Get-AzureRmSqlDatabaseImportExportStatus](https://msdn.microsoft.com/library/mt707794.aspx)

<!---HONumber=AcomDC_0803_2016-->