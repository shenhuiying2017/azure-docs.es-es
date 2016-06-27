<properties 
    pageTitle="Copia de una Base de datos SQL de Azure con PowerShell | Microsoft Azure" 
    description="Crear copia de una base de datos SQL de Azure con PowerShell" 
	services="sql-database"
	documentationCenter=""
	authors="stevestein"
	manager="jhubbard"
	editor=""/>

<tags
	ms.service="sql-database"
	ms.devlang="NA"
	ms.date="06/06/2016"
	ms.author="sstein"
	ms.workload="data-management"
	ms.topic="article"
	ms.tgt_pltfrm="NA"/>


# Copia de una Base de datos SQL de Azure con PowerShell


> [AZURE.SELECTOR]
- [Portal de Azure](sql-database-copy.md)
- [PowerShell](sql-database-copy-powershell.md)
- [T-SQL](sql-database-copy-transact-sql.md)



En los siguientes pasos se muestra cómo copiar una base de datos SQL con PowerShell. La operación de copia de base de datos copia una base de datos SQL en una nueva base de datos mediante el cmdlet [Start-AzureSqlDatabaseCopy](https://msdn.microsoft.com/library/dn720220.aspx). La copia es una copia de seguridad de instantánea de la base de datos que crea en el mismo servidor o en un servidor diferente.

> [AZURE.NOTE] La Base de datos SQL de Azure [crea y mantiene automáticamente](sql-database-automated-backups.md) copias de seguridad para cada base de datos de usuario que puede restaurar.

Cuando se completa el proceso de copia, la nueva base de datos es una base de datos totalmente operativa que es independiente de la base de datos de origen. La nueva base de datos es transaccionalmente coherente con la base de datos de origen en el momento en que se completa la copia. El nivel de rendimiento y el nivel de servicio (nivel de precios) de la copia de base de datos son los mismos que la base de datos de origen. Cuando se complete la copia, esta se convierte en una base de datos independiente y completamente funcional. Los inicios de sesión, usuarios y permisos pueden administrarse de forma independiente.


Al copiar una base de datos en el mismo servidor lógico, los mismos inicios de sesión se pueden usar en ambas bases de datos. La entidad de seguridad que usa para copiar la base de datos se convierte en el propietario de la base de datos (DBO) en la nueva base de datos. Todos los usuarios de base de datos, sus permisos y sus identificadores de seguridad (SID) se copian en la copia de la base de datos.


Para completar este artículo, necesitará lo siguiente:

- Una suscripción de Azure. Si necesita una suscripción a Azure, haga clic en la opción **PRUEBA GRATUITA** situada en la parte superior de esta página y, a continuación, vuelva para finalizar este artículo.
- Una Base de datos SQL de Azure. Si no tiene ninguna base de datos SQL, siga los pasos de este artículo para crear una: [Creación de la primera base de datos SQL de Azure](sql-database-get-started.md).
- Azure PowerShell. Puede descargar e instalar los módulos de Azure PowerShell mediante la ejecución del [Instalador de plataforma web de Microsoft](http://go.microsoft.com/fwlink/p/?linkid=320376&clcid=0x409). Para obtener información detallada, vea [Instalación y configuración de Azure PowerShell](../powershell-install-configure.md).



## Copiar la base de datos SQL

Hay algunas variables en las que es necesario reemplazar los valores de ejemplo por los valores específicos de la base de datos y los servidores. Reemplace los valores de marcador de posición por los valores para su entorno:

    # The name of the server on which the source database resides.
    $ServerName = "sourceServerName"

    # The name of the source database (the database to copy). 
    $DatabaseName = "sourceDatabaseName" 
    
    # The name of the server that hosts the target database. This server must be in the same Azure subscription as the source database server. 
    $PartnerServerName = "partnerServerName"

    # The name of the target database (the name of the copy).
    $PartnerDatabaseName = "partnerDatabaseName" 





### Copiar una base de datos SQL en el mismo servidor

Este comando envía la solicitud de copia de base de datos para el servicio. Según el tamaño de su base de datos, la operación de copia puede tardar algún tiempo en completarse.

    # Copy a database to the same server
    Start-AzureSqlDatabaseCopy -ServerName $ServerName -DatabaseName $DatabaseName -PartnerDatabase $PartnerDatabaseName

### Copiar una base de datos SQL en un servidor diferente

Este comando envía la solicitud de copia de base de datos para el servicio. Según el tamaño de su base de datos, la operación de copia puede tardar algún tiempo en completarse.

    # Copy a database to a different server
    Start-AzureSqlDatabaseCopy -ServerName $ServerName -DatabaseName $DatabaseName -PartnerServer $PartnerServerName -PartnerDatabase $PartnerDatabaseName
    

## Supervisar el progreso de la operación de copia

Después de ejecutar **Start-AzureSqlDatabaseCopy**, puede comprobar el estado de la solicitud de copia. Cuando se ejecuta esto justo después de la solicitud, normalmente devolverá **Estado: Pendiente** o **Estado: En ejecución** para que pueda ejecutarlo varias veces hasta que vea **Estado: COMPLETADO** en la salida.


    Get-AzureSqlDatabaseOperation -ServerName $ServerName -DatabaseName $DatabaseName


## Script de PowerShell de ejemplo

    # The name of the server where the source database resides
    $ServerName = "sourceServerName"

    # The name of the source database (the database to copy) 
    $DatabaseName = "sourceDatabaseName" 
    
    # The name of the server to host the database copy. This server must be in the same Azure subscription as the source database server
    $PartnerServerName = "partnerServerName"

    # The name of the target database (the name of the copy)
    $PartnerDatabaseName = "partnerDatabaseName" 


    Add-AzureAccount
    Select-AzureSubscription -SubscriptionName "myAzureSubscriptionName"
      
    # Copy a database to a different server (remove the -PartnerServer parameter to copy to the same server)
    Start-AzureSqlDatabaseCopy -ServerName $ServerName -DatabaseName $DatabaseName -PartnerServer $PartnerServerName -PartnerDatabase $PartnerDatabaseName
    
    # Monitor the status of the copy
    Get-AzureSqlDatabaseOperation -ServerName $ServerName -DatabaseName $DatabaseName
    

## Pasos siguientes

- [Conexión a la Base de datos SQL con SQL Server Management Studio y realización de una consulta de T-SQL de ejemplo](sql-database-connect-query-ssms.md)
- [Exportar la base de datos a un BACPAC](sql-database-export-powershell.md)


## Recursos adicionales

- [Información general acerca de la continuidad del negocio](sql-database-business-continuity.md)
- [Obtención de detalles de la recuperación ante desastres](sql-database-disaster-recovery-drills.md)
- [Documentación de la base de datos SQL](https://azure.microsoft.com/documentation/services/sql-database/)

<!---HONumber=AcomDC_0615_2016-->