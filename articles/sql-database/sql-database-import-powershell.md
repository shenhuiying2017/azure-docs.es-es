<properties 
    pageTitle="Importar un BACPAC en una Base de datos SQL de Azure con PowerShell" 
    description="Importar un BACPAC en una Base de datos SQL de Azure con PowerShell" 
    services="sql-database" 
    documentationCenter="" 
    authors="stevestein" 
    manager="jeffreyg" 
    editor=""/>

<tags
    ms.service="sql-database"
    ms.devlang="NA"
    ms.topic="article"
    ms.tgt_pltfrm="powershell"
    ms.workload="data-management" 
    ms.date="09/05/2015"
    ms.author="sstein"/>

# Importar un BACPAC en una Base de datos SQL con PowerShell

**Base de datos única**

> [AZURE.SELECTOR]
- [Azure Preview Portal](sql-database-import.md)
- [PowerShell](sql-database-import-powershell.md)


En este artículo se muestra cómo crear una base de datos SQL importando un BACPAC con PowerShell.

Un BACPAC es un archivo .bacpac que contiene datos y un esquema de base de datos. Para obtener detalles, vea el paquete de copia de seguridad (.bacpac) en [Aplicaciones del nivel de datos](https://msdn.microsoft.com/library/ee210546.aspx).

La base de datos se crea a partir de un BACPAC importado de un contenedor de blobs de almacenamiento de Azure. Si no dispone de un archivo .bacpac en el almacenamiento de Azure, puede crear uno siguiendo los pasos descritos en [Crear y exportar un BACPAC de una base de datos SQL de Azure](sql-database-backup.md).

> [AZURE.NOTE]La Base de datos SQL de Azure crea y mantiene automáticamente copias de seguridad para cada base de datos de usuario. Para obtener detalles, vea [Información general sobre la continuidad del negocio](sql-database-business-continuity.md).


Para importar una base de datos SQL, necesita lo siguiente:

- Una suscripción de Azure. Si necesita una suscripción a Azure, haga clic en la opción **PRUEBA GRATUITA** situada en la parte superior de esta página y, a continuación, vuelva para finalizar este artículo.
- Un archivo .bacpac (BACPAC) de la base de datos que desea restaurar. El BACPAC debe estar en un contenedor de blobs de [cuenta de almacenamiento de Azure (clásica)](storage-create-storage-account.md).
- Azure PowerShell. Puede descargar e instalar los módulos de Azure PowerShell mediante la ejecución del [Instalador de plataforma web de Microsoft](http://go.microsoft.com/fwlink/p/?linkid=320376&clcid=0x409). Para obtener información detallada, vea [Instalación y configuración de Azure PowerShell](powershell-install-configure.md).



## Configuración de las credenciales y selección de la suscripción

En primer lugar debe establecer el acceso a su cuenta de Azure; por tanto, inicie PowerShell y luego ejecute el siguiente cmdlet. En la pantalla de inicio de sesión escriba el mismo correo electrónico y la misma contraseña que usa para iniciar sesión en el portal de Azure.

	Add-AzureAccount

Después de iniciar sesión correctamente, verá información en la pantalla que incluye el identificador con el que ha iniciado sesión y las suscripciones a Azure a las que tiene acceso.


### Selección de su suscripción a Azure

Para seleccionar la suscripción, necesita su id. de suscripción. Puede copiar el id. de suscripción de la información mostrada en el paso anterior o, si dispone de varias suscripciones y necesita más detalles, puede ejecutar el cmdlet **Get-AzureSubscription** y copiar la información de suscripción que desee del conjunto de resultados. Cuando tenga su id. de suscripción, ejecute el siguiente cmdlet:

	Select-AzureSubscription -SubscriptionId 4cac86b0-1e56-bbbb-aaaa-000000000000

Después de ejecutar correctamente **Select-AzureSubscription** volverá al símbolo del sistema de PowerShell. Si tiene más de una suscripción, puede ejecutar **Get-AzureSubscription** y comprobar que la suscripción que ha seleccionado muestra **IsCurrent: True**.


## Configurar las variables para su entorno

Hay algunas variables que necesita para reemplazar los valores de ejemplo con los valores específicos de la base de datos y la cuenta de almacenamiento.

El nombre del servidor debe ser un servidor que exista actualmente en la suscripción seleccionada en el paso anterior y es el servidor en el que quiere crear la base de datos.

El nombre de la base de datos es el nombre que desea para la nueva base de datos.

    $ServerName = "servername"
    $DatabaseName = "databasename"


Las variables siguientes proceden de la cuenta de almacenamiento donde se encuentra su BACPAC. En el [Portal de vista previa de Azure](https://portal.azure.com), vaya a su cuenta de almacenamiento para obtener estos valores. Para encontrar la clave de acceso principal, haga clic en **Toda la configuración** y luego en **Claves** de la hoja de su cuenta de almacenamiento.

El nombre de blob es el nombre de un archivo de .bacpac existente desde el que quiere crear la base de datos. Necesita incluir la extensión .bacpac.

    $StorageName = "storageaccountname"
    $ContainerName = "blobcontainername"
    $BlobName = "filename.bacpac"
    $StorageKey = "primaryaccesskey"

## Crear un puntero a su cuenta de almacenamiento y servidor

Al ejecutar el cmdlet **Get-Credential** se abre una ventana en la que se le pide su nombre de usuario y contraseña. Escriba el inicio de sesión de administrador y la contraseña para el servidor SQL en el que quiere crear la base de datos ($ServerName de arriba), no el nombre de usuario y la contraseña para su cuenta de Azure.

    $credential = Get-Credential
    $SqlCtx = New-AzureSqlDatabaseServerContext -ServerName $ServerName -Credential $credential

    $StorageCtx = New-AzureStorageContext -StorageAccountName $StorageName -StorageAccountKey $StorageKey
    $Container = Get-AzureStorageContainer -Name $ContainerName -Context $StorageCtx


## Importar la base de datos

Este comando envía una solicitud de importación de base de datos para el servicio. Según el tamaño de su base de datos, la operación de importación puede tardar algún tiempo en completarse.

    $exportRequest = Start-AzureSqlDatabaseExport -SqlConnectionContext $SqlCtx -StorageContainer $Container -DatabaseName $DatabaseName -BlobName $BlobName
    

## Supervisar el progreso de la restauración

Después de ejecutar **Start AzureSqlDatabaseImport**, puede comprobar el estado de la solicitud.

La comprobación del estado inmediatamente después de la solicitud suele devolver un estado de **Pendiente** o **En ejecución** y ofrecerá un porcentaje actual completo por lo que pueda ejecutar esto varias veces hasta que vea **Estado: completado** en la salida.

Al ejecutar este comando se le solicitará una contraseña. Escriba el inicio de sesión de administrador y la contraseña para su SQL server.


    Get-AzureSqlDatabaseImportExportStatus -RequestId $ImportRequest.RequestGuid -ServerName $ServerName -Username $credential.UserName
 


## Script de restauración de PowerShell de Base de datos SQL


    Add-AzureAccount
    Select-AzureSubscription -SubscriptionId "4cac86b0-1e56-bbbb-aaaa-000000000000"
    
    $ServerName = "servername"
    $DatabaseName = "nameofnewdatabase"

    $StorageName = "storageaccountname"
    $ContainerName = "blobcontainername"
    $BlobName = "filename.bacpac"
    $StorageKey = "primaryaccesskey"
    
    $credential = Get-Credential
    $SqlCtx = New-AzureSqlDatabaseServerContext -ServerName $ServerName -Credential $credential
    
    $StorageCtx = New-AzureStorageContext -StorageAccountName $StorageName -StorageAccountKey $StorageKey
    $Container = Get-AzureStorageContainer -Name $ContainerName -Context $StorageCtx
    
    $ImportRequest = Start-AzureSqlDatabaseExport -SqlConnectionContext $SqlCtx -StorageContainer $Container -DatabaseName $DatabaseName -BlobName $BlobName
    
    Get-AzureSqlDatabaseImportExportStatus -RequestId $ImportRequest.RequestGuid -ServerName $ServerName -Username $credential.UserName
    

## Pasos siguientes

- [Conexión con SQL Server Management Studio (SSMS)](sql-database-connect-to-database.md)




## Recursos adicionales

- [Información general acerca de la continuidad del negocio](sql-database-business-continuity.md)
- [Obtención de detalles de la recuperación ante desastres](sql-database-disaster-recovery-drills.md)
- [Documentación de Base de datos SQL](https://azure.microsoft.com/documentation/services/sql-database/)

<!---HONumber=Sept15_HO2-->