---
title: Diferencias de T-SQL en Instancia administrada de Azure SQL Database | Microsoft Docs
description: En este artículo se describen las diferencias de T-SQL entre Instancia administrada de Azure SQL Database y SQL Server.
services: sql-database
author: jovanpop-msft
ms.reviewer: carlrab, bonova
ms.service: sql-database
ms.custom: managed instance
ms.topic: article
ms.date: 03/09/2018
ms.author: jovanpop
manager: cguyer
ms.openlocfilehash: 27be2b9a5f2b9aaf2d4464a6c927ec2a1694131a
ms.sourcegitcommit: a0be2dc237d30b7f79914e8adfb85299571374ec
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 03/12/2018
---
# <a name="azure-sql-database-managed-instance-t-sql-differences-from-sql-server"></a>Diferencias de T-SQL en Instancia administrada de Azure SQL Database 

Instancia administrada de Azure SQL Database (versión preliminar) proporciona gran compatibilidad con instancias locales de Motor de base de datos de SQL Server. Instancia administrada admite la mayoría de las características de Motor de base de datos de SQL Server. Puesto que todavía hay algunas diferencias de comportamiento y de sintaxis, en este artículo se resumen y se explican estas diferencias.
 - [Diferencias de T-SQL y características no admitidas](#Differences)
 - [Características que tienen un comportamiento diferente en Instancia administrada](#Changes)
 - [Limitaciones temporales y problemas conocidos](#Issues)

## <a name="Differences"></a> Diferencias de T-SQL con respecto a SQL Database 

En esta sección se resumen las principales diferencias en la sintaxis de T-SQL y el comportamiento entre Instancia administrada y las instancias locales de Motor de base de datos SQL Server, así como las características no admitidas.

### <a name="always-on-availability"></a>Disponibilidad Always-On

La [alta disponibilidad](sql-database-high-availability.md) está integrada en Instancia administrada y no la pueden controlar los usuarios. No se admiten las siguientes instrucciones:
 - [CREATE ENDPOINT … FOR DATABASE_MIRRORING](https://docs.microsoft.com/sql/t-sql/statements/create-endpoint-transact-sql.md)
 - [CREATE AVAILABILITY GROUP](https://docs.microsoft.com/sql/t-sql/statements/create-availability-group-transact-sql.md)
 - [ALTER AVAILABILITY GROUP](https://docs.microsoft.com/sql/t-sql/statements/alter-availability-group-transact-sql.md)
 - [DROP AVAILABILITY GROUP](https://docs.microsoft.com/sql/t-sql/statements/drop-availability-group-transact-sql.md)
 - La cláusula [SET HADR](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql-set-hadr.md) de la instrucción [ALTER DATABASE](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql)

### <a name="auditing"></a>Auditoría 
 
Las diferencias principales entre la auditoría de SQL en Instancia administrada, Azure SQL Database y las instancias locales de SQL Server son:
- En Instancia administrada, la auditoría de SQL funciona en el nivel de servidor y almacena los archivos `.xel` en la cuenta de Azure Blob Storage.  
- En Azure SQL Database, la auditoría de SQL funciona en el nivel de base de datos.
- En las máquinas virtuales o instancias locales de SQL Server, la auditoría de SQL funciona en el nivel de servidor, pero almacena los eventos en registros de eventos de Windows o del sistema de archivos.  
  
En Instancia administrada, la auditoría de XEvent admite Azure Blob Storage como destino. No se admiten archivos ni registros de Windows.    
 
Las principales diferencias en la sintaxis de `CREATE AUDIT` para guardar la auditoría en Azure Blob Storage son:
- Se proporciona una nueva sintaxis de `TO URL` que permite especificar la dirección URL del contenedor de Azure Blob Storage donde se colocarán los archivos `.xel`. 
- No se admite la sintaxis de `TO FILE` porque Instancia administrada no puede acceder a los recursos compartidos de archivos de Windows. 
 
Para más información, consulte:  
- [CREATE SERVER AUDIT](https://docs.microsoft.com/sql/t-sql/statements/create-server-audit-transact-sql)  
- [ALTER SERVER AUDIT](https://docs.microsoft.com/sql/t-sql/statements/alter-server-audit-transact-sql) 
- [Auditoría](https://docs.microsoft.com/sql/relational-databases/security/auditing/sql-server-audit-database-engine)     

### <a name="backup"></a>Backup 

Instancia administrada realiza copias de seguridad automáticas y permite a los usuarios crear copias de seguridad `COPY_ONLY` de bases de datos completas. No se admiten copias de seguridad de instantáneas de archivos, de registro ni diferenciales.  
- Instancia administrada puede realizar copias de seguridad de una base de datos solo en una cuenta de Azure Blob Storage: 
 - Solo se admite `BACKUP TO URL`. 
 - No se admiten `FILE`, `TAPE` y dispositivos de copia de seguridad.  
- Se admite la mayoría de las opciones de `WITH` generales. 
 - `COPY_ONLY` es obligatorio.
 - No se admite `FILE_SNAPSHOT`.  
 - No se admiten las opciones de cinta `REWIND`, `NOREWIND`, `UNLOAD` y `NOUNLOAD`. 
 - No se admiten las opciones específicas de registro `NORECOVERY`, `STANDBY` y `NO_TRUNCATE`. 
 
Limitaciones:  
- Instancia administrada puede copiar una base de datos en una copia de seguridad con hasta 32 franjas, que es suficiente para bases de datos de hasta 4 TB si se utiliza la compresión de copia de seguridad.
- El tamaño máximo de franja de copia de seguridad es 195 GB (tamaño máximo de blob). Aumente el número de franjas en el comando de copia de seguridad para reducir el tamaño de cada franja y permanecer dentro de este límite. 

> [!TIP]
> Para solucionar esta limitación de forma local, realice la copia de seguridad en `DISK` en lugar de `URL`, cargue el archivo de copia de seguridad en el blob y, a continuación, restaure. La restauración admite archivos mayores porque se utiliza un tipo de blob distinto.  

Para más información acerca de las copias de seguridad mediante T-SQL, consulte [BACKUP](https://docs.microsoft.com/sql/t-sql/statements/backup-transact-sql).

### <a name="buffer-pool-extension"></a>Extensión del grupo de búferes 
 
- No se admite la [extensión del grupo de búferes](https://docs.microsoft.com/sql/database-engine/configure-windows/buffer-pool-extension).
- No se admite `ALTER SERVER CONFIGURATION SET BUFFER POOL EXTENSION`. Consulte [ALTER SERVER CONFIGURATION](https://docs.microsoft.com/sql/t-sql/statements/alter-server-configuration-transact-sql). 
 
### <a name="bulk-insert--openrowset"></a>Bulk insert/openrowset

Instancia administrada no puede acceder a los recursos compartidos de archivos ni carpetas de Windows, por lo que los archivos se deben importar desde Azure Blob Storage.
- `DATASOURCE` es necesario en el comando `BULK INSERT` durante la importación de archivos desde Azure Blob Storage. Consulte [BULK INSERT](https://docs.microsoft.com/sql/t-sql/statements/bulk-insert-transact-sql).
- `DATASOURCE` se necesita en la función `OPENROWSET` cuando se lee el contenido de un archivo desde Azure Blob Storage. Consulte [OPENROWSET](https://docs.microsoft.com/sql/t-sql/functions/openrowset-transact-sql).
 
### <a name="certificates"></a>Certificados 

Instancia administrada no puede acceder a los recursos compartidos de archivos ni a las carpetas de Windows, por lo que se aplican las siguientes restricciones: 
- El archivo `CREATE FROM`/`BACKUP TO` no se admite para certificados
- No se admite el certificado `CREATE`/`BACKUP` de `FILE`/`ASSEMBLY`. No se pueden usar archivos de clave privada.  
 
Consulte [CREATE CERTIFICATE](https://docs.microsoft.com/sql/t-sql/statements/create-certificate-transact-sql) y [BACKUP CERTIFICATE](https://docs.microsoft.com/sql/t-sql/statements/backup-certificate-transact-sql).  
  
> [!TIP]
> Solución alternativa: aplique un script al certificado o la clave privada, guarde como archivo .sql y cree a partir del archivo binario: 
> 
> ``` 
CREATE CERTIFICATE  
 FROM BINARY = asn_encoded_certificate    
WITH PRIVATE KEY ( <private_key_options> ) 
>```   
 
### <a name="clr"></a>CLR 

Instancia administrada no puede acceder a los recursos compartidos de archivos ni a las carpetas de Windows, por lo que se aplican las siguientes restricciones: 
- Solo se admite `CREATE ASSEMBLY FROM BINARY`. Consulte [CREATE ASSEMBLY FROM BINARY](https://docs.microsoft.com/sql/t-sql/statements/create-assembly-transact-sql).  
- No se admite `CREATE ASSEMBLY FROM FILE`. Consulte [CREATE ASSEMBLY FROM FILE](https://docs.microsoft.com/sql/t-sql/statements/create-assembly-transact-sql).
- `ALTER ASSEMBLY` no puede hacer referencia a archivos. Consulte [ALTER ASSEMBLY](https://docs.microsoft.com/sql/t-sql/statements/alter-assembly-transact-sql).
 
### <a name="compatibility-levels"></a>Niveles de compatibilidad 
 
- Los niveles de compatibilidad admitidos son: 100, 110, 120, 130, 140.  
- No se admiten los niveles de compatibilidad menores que 100. 
- El nivel de compatibilidad predeterminado es 140 para las bases de datos nuevas. Para las bases de datos restauradas, el nivel de compatibilidad no cambiará si era 100 o superior.

Consulte [Nivel de compatibilidad de ALTER DATABASE](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql-compatibility-level).
 
### <a name="credential"></a>Credential: 
 
Solo se admiten identidades de Azure Key Vault y `SHARED ACCESS SIGNATURE`. No se admiten usuarios de Windows.
 
Consulte [CREATE CREDENTIAL](https://docs.microsoft.com/sql/t-sql/statements/create-credential-transact-sql) y [ALTER CREDENTIAL](https://docs.microsoft.com/sql/t-sql/statements/alter-credential-transact-sql). 
 
### <a name="cryptographic-providers"></a>Proveedores de servicios criptográficos

Instancia administrada no puede acceder a archivos, por lo que no se pueden crear proveedores de servicios criptográficos:
- No se admite `CREATE CRYPTOGRAPHIC PROVIDER`. Consulte [CREATE CRYPTOGRAPHIC PROVIDER](https://docs.microsoft.com/sql/t-sql/statements/create-cryptographic-provider-transact-sql).
- No se admite `ALTER CRYPTOGRAPHIC PROVIDER`. Consulte [ALTER CRYPTOGRAPHIC PROVIDER](https://docs.microsoft.com/sql/t-sql/statements/alter-cryptographic-provider-transact-sql). 

### <a name="collation"></a>Collation 
 
La intercalación del servidor es `SQL_Latin1_General_CP1_CI_AS` y no se puede cambiar. Consulte [Intercalaciones](https://docs.microsoft.com/sql/t-sql/statements/collations).
 
### <a name="database-options"></a>Opciones de base de datos 
 
- No se permite usar varios archivos de registro. 
- No se admiten objetos en memoria caché en el nivel de servicio De uso general.  
- Hay un límite de 280 archivos por instancia, lo que implica 280 archivos máximo por base de datos. Los archivos de datos y de registro cuentan para este límite.  
- La base de datos no puede contener grupos de archivos que contengan datos de secuencia de archivos.  Se producirá un error en la restauración si el archivo .bak contiene datos `FILESTREAM`.  
- Todos los archivos se colocan en Azure Premium Storage. Los valores de rendimiento por archivo y E/S dependen del tamaño de cada archivo individual, igual que en los discos de Azure Premium Storage. Consulte el [rendimiento de los discos de Azure Premium Storage](https://docs.microsoft.com/azure/virtual-machines/windows/premium-storage-performance#premium-storage-disk-sizes)  
 
#### <a name="create-database-statement"></a>Instrucción CREATE DATABASE

Se aplican las siguientes limitaciones de `CREATE DATABASE`: 
- No se puede definir archivos y grupos de archivos.  
- La opción `CONTAINMENT` no se admite.  
- Las opciones `WITH` no se admiten.  
   > [!TIP]
   > Como alternativa, use `ALTER DATABASE` después de `CREATE DATABASE` para establecer las opciones de la base de datos para agregar archivos o para establecer el contenedor.  

- La opción `FOR ATTACH` no se admite. 
- La opción `AS SNAPSHOT OF` no se admite. 

Para más información, consulte [CREATE DATABASE](https://docs.microsoft.com/sql/t-sql/statements/create-database-sql-server-transact-sql).

#### <a name="alter-database-statement"></a>Instrucción ALTER DATABASE

Algunas propiedades de archivo no se pueden establecer ni cambiar:
- No se puede especificar la ruta de acceso del archivo en la instrucción T-SQL `ALTER DATABASE ADD FILE (FILENAME='path')`. Quite `FILENAME` del script porque Instancia administrada coloca automáticamente los archivos.  
- El nombre del archivo no se puede cambiar mediante la instrucción `ALTER DATABASE`.

Las siguientes opciones se establecen de forma predeterminada y no se pueden cambiar: 
- `MULTI_USER` 
- `ENABLE_BROKER ON` 
- `AUTO_CLOSE OFF` 

Las opciones siguientes no se pueden modificar: 
- `AUTO_CLOSE` 
- `AUTOMATIC_TUNING(CREATE_INDEX=ON|OFF)` 
- `AUTOMATIC_TUNING(DROP_INDEX=ON|OFF)` 
- `DISABLE_BROKER` 
- `EMERGENCY` 
- `ENABLE_BROKER` 
- `FILESTREAM` 
- `HADR`   
- `NEW_BROKER` 
- `OFFLINE` 
- `PAGE_VERIFY` 
- `PARTNER` 
- `READ_ONLY` 
- `RECOVERY BULK_LOGGED` 
- `RECOVERY_SIMPLE` 
- `REMOTE_DATA_ARCHIVE`  
- `RESTRICTED_USER` 
- `SINGLE_USER` 
- `WITNESS`

No se permite modificar el nombre.

Para más información, consulte [ALTER DATABASE](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql-file-and-filegroup-options).

### <a name="database-mirroring"></a>Creación de reflejo de la base de datos

No se admite la creación de reflejos de la base de datos.
 - Las opciones `ALTER DATABASE SET PARTNER` y `SET WITNESS` no se admiten.
 - No se admite `CREATE ENDPOINT … FOR DATABASE_MIRRORING`.

Para más información, consulte [ALTER DATABASE SET PARTNER y SET WITNESS](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql-database-mirroring) y [CREATE ENDPOINT … FOR DATABASE_MIRRORING](https://docs.microsoft.com/sql/t-sql/statements/create-endpoint-transact-sql).

### <a name="dbcc"></a>DBCC 
 
Instancia administrada no admite instrucciones DBCC no documentadas que estén habilitadas en SQL Server.
- No se admite `Trace Flags`. Consulte [Marcas de seguimiento](https://docs.microsoft.com/sql/t-sql/database-console-commands/dbcc-traceon-trace-flags-transact-sql).
- No se admite `DBCC TRACEOFF`. Consulte [DBCC TRACEOFF](https://docs.microsoft.com/sql/t-sql/database-console-commands/dbcc-traceoff-transact-sql).
- No se admite `DBCC TRACEON`. Consulte [DBCC TRACEON](https://docs.microsoft.com/sql/t-sql/database-console-commands/dbcc-traceon-transact-sql).

### <a name="extended-events"></a>Eventos extendidos 

No se admiten algunos destinos específicos de Windows para XEvents:
- No se admite `etw_classic_sync target`. Guarde los archivos `.xel` en Azure Blob Storage. Consulte [Destino etw_classic_sync](https://docs.microsoft.com/sql/relational-databases/extended-events/targets-for-extended-events-in-sql-server#etwclassicsynctarget-target). 
- No se admite `event_file target`. Guarde los archivos `.xel` en Azure Blob Storage. Consulte [Destino event_file](https://docs.microsoft.com/sql/relational-databases/extended-events/targets-for-extended-events-in-sql-server#eventfile-target).

### <a name="external-libraries"></a>Bibliotecas externas

Aún no se admiten las bibliotecas externas de R y Python para análisis en base de datos. Consulte [Machine Learning Services en SQL Server](https://docs.microsoft.com/sql/advanced-analytics/r/sql-server-r-services).

### <a name="filestream-and-filetable"></a>FileStream y Filetable

- No se admiten datos FileStream. 
- La base de datos no puede contener grupos de archivos con datos `FILESTREAM`.
- No se admite `FILETABLE`.
- Las tablas no pueden tener tipos `FILESTREAM`.
- No se admiten las siguientes funciones:
 - `GetPathLocator()` 
 - `GET_FILESTREAM_TRANSACTION_CONTEXT()` 
 - `PathName()` 
 - `GetFileNamespacePath()` 
 - `FileTableRootPath()` 

Para más información, consulte [FILESTREAM](https://docs.microsoft.com/sql/relational-databases/blob/filestream-sql-server) y [FileTables](https://docs.microsoft.com/sql/relational-databases/blob/filetables-sql-server).

### <a name="full-text-semantic-search"></a>Búsqueda semántica de texto completo

No se admite la [búsqueda semántica](https://docs.microsoft.com/sql/relational-databases/search/semantic-search-sql-server).

### <a name="linked-servers"></a>Servidores vinculados
 
Los servidores vinculados en Instancia administrada admiten un número limitado de destinos: 
- Destinos admitidos: SQL Server, SQL Database, Instancia administrada y SQL Server en una máquina virtual.
- Destinos no admitidos: archivos, Analysis Services y otros RDBMS.

Operaciones

- No se admiten las transacciones de escritura entre instancias.
- Se admite `sp_dropserver` para quitar un servidor vinculado. Consulte [sp_dropserver](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-dropserver-transact-sql).
- La función `OPENROWSET` puede utilizarse para ejecutar consultas solo en instancias de SQL Server (ya sean administradas, locales o en máquinas virtuales). Consulte [OPENROWSET](https://docs.microsoft.com/sql/t-sql/functions/openrowset-transact-sql).
- La función `OPENDATASOURCE` puede utilizarse para ejecutar consultas solo en instancias de SQL Server (ya sean administradas, locales o en máquinas virtuales). Solo se admiten los valores `SQLNCLI`, `SQLNCLI11` y `SQLOLEDB` como proveedor. Por ejemplo: `SELECT * FROM OPENDATASOURCE('SQLNCLI', '...').AdventureWorks2012.HumanResources.Employee`. Consulte [OPENDATASOURCE](https://docs.microsoft.com/sql/t-sql/functions/opendatasource-transact-sql).
 
### <a name="logins--users"></a>Inicios de sesión y usuarios 

- Se admiten los inicios de sesión SQL creados con `FROM CERTIFICATE`, `FROM ASYMMETRIC KEY` y `FROM SID`. Consulte [CREATE LOGIN](https://docs.microsoft.com/sql/t-sql/statements/create-login-transact-sql).
- No se admiten los inicios de sesión de Windows creados con `CREATE LOGIN ... FROM WINDOWS`.
- El usuario de Azure Active Directory (Azure AD) que creó la instancia tiene [privilegios de administrador sin restricciones](https://docs.microsoft.com/azure/sql-database/sql-database-manage-logins#unrestricted-administrative-accounts).
- Los usuarios de nivel de base de datos de Azure Active Directory (Azure AD) que no son administradores pueden crearse con la sintaxis `CREATE USER ... FROM EXTERNAL PROVIDER`. Consulte [CREATE USER ... FROM EXTERNAL PROVIDER](https://docs.microsoft.com/azure/sql-database/sql-database-manage-logins#non-administrator-users)
 
### <a name="polybase"></a>PolyBase

No se admiten tablas externas que hacen referencia a archivos en HDFS o Azure Blob Storage. Para más información acerca de Polybase, consulte [Polybase](https://docs.microsoft.com/sql/relational-databases/polybase/polybase-guide).

### <a name="replication"></a>Replicación 
 
Aún no se admite la replicación. Para más información sobre la replicación, consulte [Replicación de SQL Server](https://docs.microsoft.com/sql/relational-databases/replication/sql-server-replication).
 
### <a name="restore-statement"></a>Instrucción RESTORE 
 
- Sintaxis admitida  
   - `RESTORE DATABASE` 
   - `RESTORE FILELISTONLY ONLY` 
   - `RESTORE HEADER ONLY` 
   - `RESTORE LABELONLY ONLY` 
   - `RESTORE VERIFYONLY ONLY` 
- Sintaxis no admitida 
   - `RESTORE LOG ONLY` 
   - `RESTORE REWINDONLY ONLY`
- Origen  
 - La única opción admitida es `FROM URL` (Azure Blob Storage).
 - No se admiten `FROM DISK`/`TAPE`/dispositivo de copia de seguridad.
 - No se admiten conjuntos de copia de seguridad. 
- No se admiten las opciones `WITH` (`DIFFERENTIAL`, `STATS`, etc.).     
- `ASYNC RESTORE`; la restauración continúa aunque se interrumpa la conexión con el cliente. Si la conexión se interrumpe, puede usar `sys.dm_operation_status` para ver el estado de una operación de restauración (y para crear y eliminar una base de datos). Consulte [sys.dm_operation_status](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-operation-status-azure-sql-database).  
 
Las siguientes opciones de base de datos se establecen o invalidan, y no se pueden cambiar más adelante:  
- `NEW_BROKER` (si el agente no está habilitado en el archivo .bak)  
- `ENABLE_BROKER` (si el agente no está habilitado en el archivo .bak)  
- `AUTO_CLOSE=OFF` (si una base de datos en el archivo .bak tiene `AUTO_CLOSE=ON`)  
- `RECOVERY FULL` (si una base de datos en el archivo .bak tiene los modos de recuperación `SIMPLE` o `BULK_LOGGED`)
- Se agrega un grupo de archivos optimizado para memoria y se le asigna el nombre XTP si aún no se encuentra en el archivo .bak de origen.  
- Se cambia el nombre de todos los grupos de archivos optimizados para memoria existentes a XTP.  
- Las opciones `SINGLE_USER` y `RESTRICTED_USER` se convierten en `MULTI_USER`   
Limitaciones:  
- Los archivos `.BAK` que contienen varios conjuntos de copia de seguridad no se pueden restaurar. 
- Los archivos `.BAK` que contienen varios archivos de registro no se pueden restaurar. 
- Se producirá un error en la restauración si el archivo .bak contiene datos `FILESTREAM`.
- Actualmente no se pueden restaurar las copias de seguridad que contienen bases de datos que tienen objetos en memoria activos.  
- Actualmente no se pueden restaurar las copias de seguridad que contienen bases de datos en las que han existido objetos en memoria en algún momento.   
- Actualmente no se pueden restaurar las copias de seguridad que contienen bases de datos en modo de solo lectura. En breve se quitará esta limitación.   
 
Para más información acerca de las instrucciones Restore, consulte [Instrucciones RESTORE](https://docs.microsoft.com/sql/t-sql/statements/restore-statements-transact-sql).

### <a name="service-broker"></a>Service Broker 
 
- No se admite el agente de servicio entre instancias. 
 - `sys.routes` (requisito previo): seleccione la dirección de sys.routes. La dirección debe ser LOCAL en todas las rutas. Consulte [sys.routes](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-routes-transact-sql).
 - `CREATE ROUTE`: no se puede ejecutar `CREATE ROUTE` con un valor de `ADDRESS` distinto de `LOCAL`. Consulte [CREATE ROUTE](https://docs.microsoft.com/sql/t-sql/statements/create-route-transact-sql).
 - `ALTER ROUTE`: no se puede ejecutar `ALTER ROUTE` con un valor de `ADDRESS` distinto de `LOCAL`. Consulte [ALTER ROUTE](https://docs.microsoft.com/sql/t-sql/statements/alter-route-transact-sql).  
 
### <a name="service-key-and-service-master-key"></a>Clave maestra de servicio y clave de servicio 
 
- No se permite realizar [copias de seguridad de la clave maestra](https://docs.microsoft.com/sql/t-sql/statements/backup-master-key-transact-sql) (administrada por el servicio de SQL Database). 
- No se permite [restaurar la clave maestra](https://docs.microsoft.com/sql/t-sql/statements/restore-master-key-transact-sql) (administrada por el servicio de SQL Database). 
- No se permite realizar [copias de seguridad de la clave maestra de servicio](https://docs.microsoft.com/sql/t-sql/statements/backup-service-master-key-transact-sql) (administrada por el servicio de SQL Database). 
- No se permite [restaurar la clave maestra de servicio](https://docs.microsoft.com/sql/t-sql/statements/restore-service-master-key-transact-sql) (administrada por el servicio de SQL Database). 
 
### <a name="stored-procedures-functions-triggers"></a>Funciones, procedimientos almacenados, desencadenadores 
 
- Actualmente no se admite `NATIVE_COMPILATION`. 
- No se admiten las siguientes opciones de [sp_configure](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-configure-transact-sql): 
 - `allow polybase export` 
 - `allow updates` 
 - `filestream_access_level` 
 - `max text repl size` 
 - `remote data archive` 
 - `remote proc trans` 
- No se admite `sp_execute_external_scripts`. Consulte [sp_execute_external_scripts](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-execute-external-script-transact-sql#examples).
- No se admite `xp_cmdshell`. Consulte [xp_cmdshell](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/xp-cmdshell-transact-sql).
- No se admiten `Extended stored procedures`, incluidos `sp_addextendedproc` y `sp_dropextendedproc`. Consulte [Procedimientos almacenados extendidos](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/general-extended-stored-procedures-transact-sql).
- No se admiten `sp_attach_db`, `sp_attach_single_file_db` y `sp_detach_db`. Consulte [sp_attach_db](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-attach-db-transact-sql), [sp_attach_single_file_db](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-attach-single-file-db-transact-sql), y [sp_detach_db](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-detach-db-transact-sql).
- No se admite `sp_renamedb`. Consulte [sp_renamedb](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-renamedb-transact-sql).

### <a name="sql-server-agent"></a>Agente SQL Server 
 
- La configuración del Agente SQL es de solo lectura. El procedimiento `sp_set_agent_properties` no se admite en Instancia administrada.  
- Trabajos: actualmente solo se admiten los pasos de trabajo de T-SQL (se agregarán más pasos durante la versión preliminar pública).
 - Aún no se admite SSIS. 
 - Aún no se admite la replicación.  
  - Aún no se admite el lector del registro de transacciones.  
  - Aún no se admiten instantáneas.  
  - Aún no se admite el distribuidor.  
  - Aún no se permite combinar.  
  - Aún no se admite el lector de colas.  
 - Aún no se admite el shell de comandos. 
  - Instancia administrada no puede acceder a los recursos externos (por ejemplo, recursos compartidos de red a través de robocopy).  
 - Aún no se admite PowerShell.
 - No se admite Analysis Services.  
- Las notificaciones se admiten parcialmente.
 - Se admite la notificación por correo electrónico; es necesario configurar un perfil de Correo electrónico de base de datos. Solo puede haber un perfil de Correo electrónico de base de datos y debe llamarse `AzureManagedInstance_dbmail_profile` en la versión preliminar pública (limitación temporal).  
 - No se admite el paginador.  
 - No se admite NetSend. 
 - Aún no se admiten las alertas.
 - No se admiten servidores proxy.  
- No se admite Eventlog. 
 
Las siguientes características no se admiten en estos momentos pero se habilitarán en el futuro:  
- Servidores proxy
- Programación de trabajos de CPU inactiva 
- Habilitación o deshabilitación del agente
- Alertas

Para más información acerca del Agente SQL Server, consulte [Agente SQL Server](https://docs.microsoft.com/sql/ssms/agent/sql-server-agent).
 
### <a name="tables"></a>Tablas 

No se admite lo siguiente: 
- `FILESTREAM` 
- `FILETABLE` 
- `EXTERNAL TABLE` 
- `MEMORY_OPTIMIZED`  

Para más información sobre cómo crear y modificar tablas, consulte [CREATE TABLE](https://docs.microsoft.com/sql/t-sql/statements/create-table-transact-sql) y [ALTER TABLE](https://docs.microsoft.com/sql/t-sql/statements/alter-table-transact-sql).
 
## <a name="Changes"></a> Cambios de comportamiento 
 
Las siguientes variables, funciones y vistas devuelven resultados diferentes:  
- `SERVERPROPERTY('EngineEdition')` devuelve el valor 8. Esta propiedad identifica de forma única la instancia administrada. Consulte [SERVERPROPERTY](https://docs.microsoft.com/sql/t-sql/functions/serverproperty-transact-sql).
- `SERVERPROPERTY('InstanceName')` devuelve el nombre de instancia corto, por ejemplo, "myserver". Consulte [SERVERPROPERTY('InstanceName')](https://docs.microsoft.com/sql/t-sql/functions/serverproperty-transact-sql).
- `@@SERVERNAME` devuelve el nombre DNS completo "conectable", por ejemplo, my-managed-instance.wcus17662feb9ce98.database.windows.net. Consulte [@@SERVERNAME](https://docs.microsoft.com/sql/t-sql/functions/servername-transact-sql).  
- `SYS.SERVERS` devuelve el nombre DNS completo "conectable", por ejemplo, `myinstance.domain.database.windows.net`, para las propiedades "name" y "data_source". Consulte [SYS.SERVERS](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-servers-transact-sql). 
- `@@SERVERNAME` devuelve el nombre DNS completo "conectable", por ejemplo, `my-managed-instance.wcus17662feb9ce98.database.windows.net`. Consulte [@@SERVERNAME](https://docs.microsoft.com/sql/t-sql/functions/servername-transact-sql).  
- `SYS.SERVERS` devuelve el nombre DNS completo "conectable", por ejemplo, `myinstance.domain.database.windows.net`, para las propiedades "name" y "data_source". Consulte [SYS.SERVERS](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-servers-transact-sql). 
- `@@SERVICENAME` devuelve NULL porque no tiene sentido en el entorno de Instancia administrada. Consulte [@@SERVICENAME](https://docs.microsoft.com/sql/t-sql/functions/servicename-transact-sql).   
- Se admite `SUSER_ID`. Devuelve NULL si el inicio de sesión de AAD no está en sys.syslogins. Consulte [SUSER_ID](https://docs.microsoft.com/sql/t-sql/functions/suser-id-transact-sql).  
- No se admite `SUSER_SID`. Devuelve datos incorrectos (problema conocido temporal). Consulte [SUSER_SID](https://docs.microsoft.com/sql/t-sql/functions/suser-sid-transact-sql). 
- `GETDATE()` y otras funciones integradas de fecha y hora siempre devuelven la hora en la zona horaria UTC. Consulte [GETDATE](https://docs.microsoft.com/sql/t-sql/functions/getdate-transact-sql).

## <a name="Issues"></a>Problemas conocidos y limitaciones

### <a name="tempdb-size"></a>Tamaño de TEMPDB

`tempdb` se divide en 12 archivos con un tamaño máximo de 14 GB por archivo. No se puede cambiar este tamaño máximo por archivo y no se pueden agregar nuevos archivos a `tempdb`. En breve se quitará esta limitación. Algunas consultas podrían devolver un error si necesitan más de 168 GB en `tempdb`.

### <a name="exceeding-storage-space-with-small-database-files"></a>Exceder el espacio de almacenamiento con archivos de base de datos pequeños

Cada instancia administrada tiene hasta 35 TB de espacio de almacenamiento reservado y cada archivo de base de datos se coloca inicialmente en una unidad de asignación de almacenamiento de 128 GB. Las bases de datos con muchos archivos pequeños podrían colocarse en unidades de 128 GB que, en total, superan el límite de 35 TB. En este caso, no se pueden crear ni restaurar nuevas bases de datos, aunque el tamaño total de todas las bases de datos no alcance el límite de tamaño de la instancia. El error que se devuelve en ese caso podría no estar claro.

### <a name="incorrect-configuration-of-sas-key-during-database-restore"></a>Configuración incorrecta de la clave SAS durante la restauración de una base de datos

`RESTORE DATABASE`, que lee el archivo .bak, podría estar intentando leer constantemente el archivo .bak y devuelve errores después de un período largo de tiempo si la firma de acceso compartido en `CREDENTIAL` es incorrecta. Ejecute RESTORE HEADERONLY antes de restaurar una base de datos para asegurarse de que la clave SAS es correcta.
Asegúrese de que quita el signo `?` inicial de la clave SAS generada mediante Azure Portal.

### <a name="tooling"></a>Herramientas

SQL Server Management Studio y SQL Server Data Tools podrían tener algunos problemas al obtener acceso a Instancia administrada. Todos los problemas de herramientas se corregirán antes de la versión de disponibilidad general.

### <a name="incorrect-database-names"></a>Nombres de base de datos incorrectos

Instancia administrada podría mostrar el valor de GUID en lugar del nombre de la base de datos durante la restauración o en algunos mensajes de error. Estos problemas se corregirán antes de la versión de disponibilidad general.

### <a name="database-mail-profile"></a>Perfil de Correo electrónico de base de datos
Solo puede haber un perfil de Correo electrónico de base de datos y debe llamarse `AzureManagedInstance_dbmail_profile`. Se trata de una limitación temporal que se quitará próximamente.

## <a name="next-steps"></a>Pasos siguientes

- Para más información acerca de Instancia administrada, consulte [¿Qué es Instancia administrada de SQL Database (versión preliminar)?](sql-database-managed-instance.md).
- Para obtener una lista de características y una comparación, consulte [Características comunes de SQL](sql-database-features.md).
- Para ver un tutorial, consulte [Creación de una instancia administrada](sql-database-managed-instance-tutorial-portal.md).
