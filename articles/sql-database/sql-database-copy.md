---
title: Copia de una base de datos SQL de Azure | Microsoft Docs
description: "Creación de una copia de una base de datos SQL de Azure"
services: sql-database
documentationcenter: 
author: anosov1960
manager: jhubbard
editor: 
ms.assetid: 5aaf6bcd-3839-49b5-8c77-cbdf786e359b
ms.service: sql-database
ms.custom: move data
ms.devlang: NA
ms.date: 04/05/2017
ms.author: sashan;carlrab
ms.workload: data-management
ms.topic: article
ms.tgt_pltfrm: NA
translationtype: Human Translation
ms.sourcegitcommit: 0d9afb1554158a4d88b7f161c62fa51c1bf61a7d
ms.openlocfilehash: 2781eacff6eea456f7f22b73bbd2544015b9af32
ms.lasthandoff: 04/12/2017


---
# <a name="copy-an-azure-sql-database"></a>Copiar una base de datos SQL de Azure

Azure SQL Database proporciona varios métodos para crear una copia transaccionalmente coherente de una instancia de Azure SQL Database existente en el mismo servidor o en un servidor diferente. Puede copiar una instancia de SQL Database mediante Azure Portal, PowerShell o T-SQL. 

## <a name="overview"></a>Información general

Una copia de la base de datos es una instantánea de la base de datos de origen en el momento de la solicitud de copia. Puede seleccionar el mismo servidor o un servidor diferente, su nivel de servicio, el nivel de rendimiento y un nivel de rendimiento diferente dentro del mismo nivel de servicio (edición). Cuando se complete la copia, esta se convierte en una base de datos independiente y completamente funcional. Llegado a este punto, puede actualizar a cualquier edición o cambiar a una edición anterior. Los inicios de sesión, usuarios y permisos pueden administrarse de forma independiente.  

## <a name="logins-in-the-database-copy"></a>Inicios de sesión en la copia de la base de datos

Al copiar una base de datos en el mismo servidor lógico, los mismos inicios de sesión se pueden usar en ambas bases de datos. La entidad de seguridad que usa para copiar la base de datos se convierte en el propietario de la base de datos (DBO) en la nueva base de datos. Todos los usuarios de base de datos, sus permisos y sus identificadores de seguridad (SID) se copian en la copia de la base de datos.  

Al copiar una base de datos en un servidor lógico diferente, la entidad de seguridad del nuevo servidor se convierte en el propietario de la nueva base de datos. Si usa [usuarios de base de datos contenidos](sql-database-manage-logins.md) para el acceso a datos, asegúrese de que tanto las bases de datos principales como las secundarias tengan siempre las mismas credenciales de usuario, de tal forma que, una vez completada la copia, pueda obtener acceso inmediato a ellas con las mismas credenciales. 

Si usa [Azure Active Directory](../active-directory/active-directory-whatis.md), puede eliminar completamente la necesidad de administrar las credenciales en la copia. Pero al copiar la base de datos a un nuevo servidor, puede que el acceso basado en inicios de sesión no funcione debido a que esas cuentas de inicio de sesión no se encuentran en el nuevo servidor. Consulte [Administración de la seguridad de Azure SQL Database después de la recuperación ante desastres](sql-database-geo-replication-security-config.md) para obtener información sobre cómo administrar inicios de sesión al copiar una base de datos a un servidor lógico diferente. 

Cuando la copia se realiza correctamente y antes de que se reasignen otros usuarios, solo el inicio de sesión que inició la copia, el propietario de la base de datos (DBO), puede iniciar sesión en la nueva base de datos. Para resolver los inicios de sesión una vez completada la operación de copia, consulte [Resolución de inicios de sesión](sql-database-copy.md#resolve-logins.md)

## <a name="database-copy-using-the-azure-portal"></a>Copia de base de datos mediante Azure Portal

Para copiar una base de datos mediante Azure Portal, abra la página de la base de datos y haga clic en **Copiar** en la barra de herramientas. 

   ![Copia de base de datos](./media/sql-database-copy/database-copy.png)

## <a name="database-copy-using-powershell"></a>Copia de base de datos mediante PowerShell

Para copiar una base de datos con PowerShell, use el cmdlet [New-AzureRmSqlDatabaseCopy](https://docs.microsoft.com/powershell/module/azurerm.sql/new-azurermsqldatabasecopy). 

```PowerShell
New-AzureRmSqlDatabaseCopy -ResourceGroupName "myResourceGroup" `
    -ServerName $sourceserver `
    -DatabaseName "MySampleDatabase" `
    -CopyResourceGroupName "myResourceGroup" `
    -CopyServerName $targetserver `
    -CopyDatabaseName "CopyOfMySampleDatabase"
```

Para obtener un script de ejemplo completo, vea [Copia de una base de datos en un nuevo servidor](scripts/sql-database-copy-database-to-new-server-powershell.md).

## <a name="database-copy-using-transact-sql"></a>Copia de base de datos mediante Transact-SQL

Inicie sesión en la base de datos maestra mediante el inicio de sesión de entidad de seguridad de nivel de servidor o el inicio de sesión que creó la base de datos que quiere copiar. Los inicios de sesión que no son de la entidad de seguridad de nivel de servidor deben ser miembros del rol dbmanager para copiar bases de datos. Para obtener más información sobre los inicios de sesión y conectarse al servidor, consulte [Administración de inicios de sesión](sql-database-manage-logins.md).

Inicie la copia de la base de datos de origen con la instrucción [CREATE DATABASE](https://msdn.microsoft.com/library/ms176061.aspx) . Con la ejecución de esta instrucción se inicia el proceso de copia de la base de datos. Dado que copiar una base de datos es un proceso asincrónico, se devuelve la instrucción CREATE DATABASE antes de que la base de datos complete el proceso de copia.

### <a name="copy-a-sql-database-to-the-same-server"></a>Copiar una base de datos SQL en el mismo servidor
Inicie sesión en la base de datos maestra mediante el inicio de sesión de entidad de seguridad de nivel de servidor o el inicio de sesión que creó la base de datos que quiere copiar. Los inicios de sesión que no son de la entidad de seguridad de nivel de servidor deben ser miembros del rol dbmanager para copiar bases de datos.

Este comando copia Base de datos1 en una base de datos nueva denominada Base de datos2 en el mismo servidor. Según el tamaño de su base de datos, la operación de copia puede tardar algún tiempo en completarse.

    -- Execute on the master database.
    -- Start copying.
    CREATE DATABASE Database1_copy AS COPY OF Database1;

### <a name="copy-a-sql-database-to-a-different-server"></a>Copiar una base de datos SQL en un servidor diferente

Inicie sesión en la base de datos maestra del servidor de destino, el servidor de Base de datos SQL de Azure donde se creará la nueva base de datos. Use un inicio de sesión que tenga el mismo nombre y contraseña que el propietario de la base de datos (DBO) de la base de datos de origen en el servidor de Base de datos SQL de Azure de origen. El inicio de sesión en el servidor de destino también debe ser miembro del rol dbmanager o ser el inicio de sesión de entidad de seguridad de nivel de servidor.

Este comando copia Base de datos1 en servidor1- en un nueva base de datos denominada Base de datos2 en servidor2. Según el tamaño de su base de datos, la operación de copia puede tardar algún tiempo en completarse.

    -- Execute on the master database of the target server (server2)
    -- Start copying from Server1 to Server2
    CREATE DATABASE Database1_copy AS COPY OF server1.Database1;


### <a name="monitor-the-progress-of-the-copy-operation"></a>Supervisar el progreso de la operación de copia

Supervise el proceso de copia consultando las vistas sys.databases y sys.dm_database_copies. Mientras que la copia esté en curso, la columna state_desc de la vista sys.databases para la nueva base de datos se establece en COPYING.

* Si se produce un error en el proceso de copia, la columna state_desc de la vista sys.databases para la nueva base de datos se establece en SUSPECT. En este caso, ejecute la instrucción DROP en la nueva base de datos e inténtelo de nuevo más tarde.
* Si el proceso de copia es correcto, la columna state_desc de la vista sys.databases para la nueva base de datos se establece en ONLINE. En este caso, se completa la copia y la nueva base de datos es una base de datos normal, que se puede modificar independientemente de la base de datos de origen.

> [!NOTE]
> * Si decide cancelar la copia mientras está en curso, ejecute la instrucción [DROP DATABASE](https://msdn.microsoft.com/library/ms178613.aspx) en la nueva base de datos. Como alternativa, al ejecutar la instrucción DROP DATABASE en la base de datos de origen también se cancelará el proceso de copia.
> 

## <a name="resolve-logins"></a>Resolución de inicios de sesión

Después de que la nueva base de datos esté en línea en el servidor de destino, use la instrucción [ALTER USER](https://msdn.microsoft.com/library/ms176060.aspx) para volver a asignar los usuarios de la nueva base de datos a inicios de sesión en el servidor de destino. Para resolver los usuarios huérfanos, consulte [Solucionar problemas de usuarios huérfanos (SQL Server)](https://msdn.microsoft.com/library/ms175475.aspx). Consulte también [Administración de la seguridad de Base de datos SQL de Azure después de la recuperación ante desastres](sql-database-geo-replication-security-config.md).

Todos los usuarios de la nueva base de datos mantienen los permisos que tenían en la base de datos de origen. El usuario que inició la copia de la base de datos se convierte en el propietario de la base de datos de la nueva base de datos y se le asigna un nuevo identificador de seguridad (SID). Cuando la copia se realiza correctamente y antes de que se reasignen otros usuarios, solo el inicio de sesión que inició la copia, el propietario de la base de datos (DBO), puede iniciar sesión en la nueva base de datos.

Consulte también [Administración de la seguridad de Azure SQL Database después de la recuperación ante desastres](sql-database-geo-replication-security-config.md) para obtener información sobre cómo administrar usuarios e inicios de sesión al copiar una base de datos a un servidor lógico diferente.

## <a name="next-steps"></a>Pasos siguientes

* Para obtener información sobre los inicios de sesión, vea [Administración de inicios de sesión](sql-database-manage-logins.md) y [Administración de la seguridad de Azure SQL Database después de la recuperación ante desastres](sql-database-geo-replication-security-config.md).
* Para exportar una base de datos, vea [Exportación de una base de datos a un archivo BACPAC](sql-database-export.md).

