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
	ms.date="07/19/2016"
	ms.author="sstein"
	ms.workload="data-management"
	ms.topic="article"
	ms.tgt_pltfrm="NA"/>


# Archivo de una base de datos SQL de Azure en un archivo BACPAC mediante PowerShell

> [AZURE.SELECTOR]
- [Portal de Azure](sql-database-export.md)
- [PowerShell](sql-database-export-powershell.md)


En este artículo se proporcionan instrucciones para archivar la base de datos SQL de Azure en un archivo BACPAC guardado en Almacenamiento de blobs de Azure usando PowerShell.

Cuando necesite crear un archivo de una base de datos SQL de Azure, puede exportar los datos y el esquema de base de datos a un archivo BACPAC. Un archivo BACPAC es, sencillamente, un archivo ZIP con la extensión BACPAC. Este archivo BACPAC se puede guardar en el almacenamiento de blobs de Azure o en cualquier almacenamiento en una ubicación local y, luego, importarlo a Base de datos SQL de Azure o a una instalación local de SQL Server.

***Consideraciones***

- Para que un archivo sea transaccionalmente coherente, hay que procurar que no haya ninguna actividad de escritura durante la exportación, o bien exportar desde una [copia transaccionalmente coherente](sql-database-copy.md) de la base de datos SQL de Azure
- El tamaño máximo de un archivo BACPAC guardado en el almacenamiento de blobs de Azure es de 200 GB. Use la utilidad de símbolo del sistema [SqlPackage](https://msdn.microsoft.com/library/hh550080.aspx) para guardar archivos BACPAC de mayor tamaño en un almacenamiento local. Esta utilidad se incluye con Visual Studio y SQL Server. También puede [descargar](https://msdn.microsoft.com/library/mt204009.aspx) la versión más reciente de SQL Server Data Tools para obtener esta utilidad.
- No se puede archivar en Almacenamiento premium de Azure usando un archivo BACPAC.
- Si la operación de exportación tarda más de 20 horas, es posible que se cancele. Para aumentar el rendimiento durante la exportación, puede hacer lo siguiente:
 - Aumentar temporalmente el nivel de servicio
 - Detener toda actividad de lectura y escritura durante la exportación
 - Usar un índice agrupado en todas las tablas de gran tamaño. Sin índices agrupados, la exportación podría no producirse si tarda más de 6-12 horas. Esto se debe a que el servicio de exportación necesita completar el recorrido de tabla para exportar toda la tabla.
 
> [AZURE.NOTE] Los BACPAC no están diseñados para usarse en operaciones de copia de seguridad y restauración. La Base de datos SQL de Azure crea automáticamente copias de seguridad para cada base de datos de usuario. Para más información, consulte [Información general: copias de seguridad automatizadas de Base de datos SQL](sql-database-automated-backups.md).

Para completar este artículo, necesitará lo siguiente:

- Una suscripción de Azure.
- Una base de datos SQL de Azure.
- Una [cuenta de almacenamiento estándar de Azure](../storage/storage-create-storage-account.md) con un contenedor de blobs para almacenar el BACPAC.


[AZURE.INCLUDE [Inicio de una sesión de PowerShell](../../includes/sql-database-powershell.md)]


## Configuración de las variables para su entorno específico

Hay algunas variables en las que es necesario reemplazar los valores de ejemplo por los valores específicos de la base de datos y la cuenta de almacenamiento.

Reemplácelos por los valores específicos.

    $ResourceGroupName = "resourceGroupName"
    $ServerName = "servername"
    $DatabaseName = "databasename"


En el [Portal de Azure](https://portal.azure.com), vaya a su cuenta de almacenamiento para obtener estos valores. Para encontrar la clave de acceso principal, vaya a la hoja de su cuenta de almacenamiento y haga clic en **Toda la configuración** y, a continuación, en **Claves**.

    $StorageName = "storageaccountname"
    $StorageKeyType = "storageKeyType"
    $StorageUri = "http://storageaccountname.blob.core.windows.net/containerName/filename.bacpac"
    $StorageKey = "primaryaccesskey"


Al ejecutar el cmdlet **Get-Credential**, se abre una ventana en la que se le pide su nombre de usuario y contraseña. Escriba el inicio de sesión de administrador y la contraseña para SQL Server (NO el nombre de usuario y la contraseña para su cuenta de Azure).

    $credential = Get-Credential

## Exportación de la base de datos

Este comando envía una solicitud de exportación de base de datos al servicio. Según el tamaño de la base de datos, la operación de exportación puede tardar algún tiempo en completarse.

> [AZURE.IMPORTANT] Para garantizar un archivo BACPAC transaccionalmente coherente, primero debe [crear una copia de la base de datos](sql-database-copy-powershell.md) y después exportar dicha copia.


    $exportRequest = New-AzureRmSqlDatabaseExport –ResourceGroupName  $ResourceGroupName –ServerName $ServerName –DatabaseName $DatabaseName –StorageKeytype $StorageKeyType –StorageKey $StorageKey StorageUri $StorageUri –AdministratorLogin $credential.UserName –AdministratorPassword $credential.Password
    

## Supervisión del progreso de la operación de exportación

Después de ejecutar **New-AzureRmSqlDatabaseExport**, puede comprobar el estado de la solicitud. Cuando se ejecuta esto justo después de la solicitud, normalmente devolverá **Estado: Pendiente** o **Estado: En ejecución** para que pueda ejecutarlo varias veces hasta que vea **Estado: Completado** en la salida.

Al ejecutar este comando se le solicitará una contraseña. Escriba la contraseña de administrador de su SQL server.


    Get-AzureRmSqlDatabaseImportExportStatus -OperationStatusLink $exportRequest .OperationStatusLink
    


## Exportación de un script de PowerShell de base de datos SQL


    $ServerName = "servername"
    $StorageName = "storageaccountname"
    $StorageKeyType = "storageKeyType"
    $StorageUri = "http://storageaccountname.blob.core.windows.net/containerName/filename.bacpac"
    $StorageKey = "primaryaccesskey"

    $credential = Get-Credential

    $exportRequest = New-AzureRmSqlDatabaseExport –ResourceGroupName  $ResourceGroupName –ServerName $ServerName –DatabaseName $DatabaseName –StorageKeytype $StorageKeyType –StorageKey $StorageKey  StorageUri $StorageUri –AdministratorLogin $credential.UserName  –AdministratorPassword $credential.Password

    Get-AzureRmSqlDatabaseImportExportStatus -OperationStatusLink $exportRequest .OperationStatusLink


## Pasos siguientes

- Para obtener información sobre cómo importar una base de datos SQL de Azure con PowerShell, consulte [Importar un archivo BACPAC para crear una base de datos SQL de Azure mediante PowerShell](sql-database-import-powershell.md).

<!---HONumber=AcomDC_0727_2016-->