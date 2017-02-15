---
title: Copia de una base de datos de Azure SQL con Transact-SQL | Microsoft Docs
description: Crear una copia de una base de datos SQL de Azure con Transact-SQL
services: sql-database
documentationcenter: 
author: stevestein
manager: jhubbard
editor: 
ms.assetid: 40ea3718-33f8-41af-90cb-3aa15059365e
ms.service: sql-database
ms.custom: migrate and move
ms.devlang: NA
ms.date: 09/19/2016
ms.author: sstein
ms.workload: data-management
ms.topic: article
ms.tgt_pltfrm: NA
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: da730116fcc83d53b3665f953332a30c7fc6239d


---
# <a name="copy-an-azure-sql-database-using-transact-sql"></a>Copia de una Base de datos SQL de Azure con Transact-SQL
> [!div class="op_single_selector"]
> * [Información general](sql-database-copy.md)
> * [Portal de Azure](sql-database-copy-portal.md)
> * [PowerShell](sql-database-copy-powershell.md)
> * [T-SQL](sql-database-copy-transact-sql.md)
> 
> 

En los siguientes pasos se muestran cómo usar una base de datos SQL con Transact-SQL en el mismo servidor o en otro distinto. La operación de copia de la base de datos utiliza la instrucción [CREATE DATABASE](https://msdn.microsoft.com/library/ms176061.aspx) .

Necesitará lo siguiente para completar los pasos de este artículo:

* Una suscripción de Azure. Si necesita una suscripción a Azure, haga clic en la opción **PRUEBA GRATUITA** situada en la parte superior de esta página y, a continuación, vuelva para finalizar este artículo.
* Una Base de datos SQL de Azure. Si no tiene ninguna base de datos SQL, siga los pasos de este artículo para crear una: [Creación de la primera base de datos SQL de Azure](sql-database-get-started.md).
* [SQL Server Management Studio (SSMS)](https://msdn.microsoft.com/library/ms174173.aspx). Si no dispone de SSMS, o si no están disponibles las características descritas en este artículo, [descargue la versión más reciente](https://msdn.microsoft.com/library/mt238290.aspx).

## <a name="copy-your-sql-database"></a>Copiar la base de datos SQL
Inicie sesión en la base de datos maestra mediante el inicio de sesión de entidad de seguridad de nivel de servidor o el inicio de sesión que creó la base de datos que quiere copiar. Los inicios de sesión que no sonde  la entidad de seguridad de nivel de servidor deben ser miembros del rol dbmanager para copiar bases de datos. Para obtener más información sobre los inicios de sesión y conectarse al servidor, consulte [Administración de inicios de sesión](sql-database-manage-logins.md).

Inicie la copia de la base de datos de origen con la instrucción [CREATE DATABASE](https://msdn.microsoft.com/library/ms176061.aspx) . Con la ejecución de esta instrucción se inicia el proceso de copia de la base de datos. Dado que copiar una base de datos es un proceso asincrónico, se devuelve la instrucción CREATE DATABASE antes de que la base de datos complete el proceso de copia.

### <a name="copy-a-sql-database-to-the-same-server"></a>Copiar una base de datos SQL en el mismo servidor
Inicie sesión en la base de datos maestra mediante el inicio de sesión de entidad de seguridad de nivel de servidor o el inicio de sesión que creó la base de datos que quiere copiar. Los inicios de sesión que no sonde  la entidad de seguridad de nivel de servidor deben ser miembros del rol dbmanager para copiar bases de datos.

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


## <a name="monitor-the-progress-of-the-copy-operation"></a>Supervisar el progreso de la operación de copia
Supervise el proceso de copia consultando las vistas sys.databases y sys.dm_database_copies. Mientras que la copia esté en curso, la columna state_desc de la vista sys.databases para la nueva base de datos se establece en COPYING.

* Si se produce un error en el proceso de copia, la columna state_desc de la vista sys.databases para la nueva base de datos se establece en SUSPECT. En este caso, ejecute la instrucción DROP en la nueva base de datos e inténtelo de nuevo más tarde.
* Si el proceso de copia es correcto, la columna state_desc de la vista sys.databases para la nueva base de datos se establece en ONLINE. En este caso, se completa la copia y la nueva base de datos es una base de datos normal, que se puede modificar independientemente de la base de datos de origen.

> [!NOTE]
> * Si decide cancelar la copia mientras está en curso, ejecute la instrucción [DROP DATABASE](https://msdn.microsoft.com/library/ms178613.aspx) en la nueva base de datos. Como alternativa, al ejecutar la instrucción DROP DATABASE en la base de datos de origen también se cancelará el proceso de copia.
> 
> 

## <a name="resolve-logins-after-the-copy-operation-completes"></a>Resolución de los inicios de sesión tras completarse la operación de copia
Después de que la nueva base de datos esté en línea en el servidor de destino, use la instrucción [ALTER USER](https://msdn.microsoft.com/library/ms176060.aspx) para volver a asignar los usuarios de la nueva base de datos a inicios de sesión en el servidor de destino. Para resolver los usuarios huérfanos, consulte [Solucionar problemas de usuarios huérfanos (SQL Server)](https://msdn.microsoft.com/library/ms175475.aspx). Consulte también [Administración de la seguridad de Base de datos SQL de Azure después de la recuperación ante desastres](sql-database-geo-replication-security-config.md).

Todos los usuarios de la nueva base de datos mantienen los permisos que tenían en la base de datos de origen. El usuario que inició la copia de la base de datos se convierte en el propietario de la base de datos de la nueva base de datos y se le asigna un nuevo identificador de seguridad (SID). Cuando la copia se realiza correctamente y antes de que se reasignen otros usuarios, solo el inicio de sesión que inició la copia, el propietario de la base de datos (DBO), puede iniciar sesión en la nueva base de datos.

## <a name="next-steps"></a>Pasos siguientes
* Consulte [Copiar una base de datos SQL de Azure](sql-database-copy.md) para obtener información sobre cómo copiar una base de datos SQL de Azure.
* Consulte el artículo sobre cómo [copiar una base de datos SQL de Azure mediante Azure Portal](sql-database-copy-portal.md) para realizar una copia de una base de datos a través de Azure Portal.
* Consulte [Copia de una Base de datos SQL de Azure con PowerShell](sql-database-copy-powershell.md) para copiar una base de datos mediante PowerShell.
* Consulte [Administración de la seguridad de Base de datos SQL de Azure después de la recuperación ante desastres](sql-database-geo-replication-security-config.md) para obtener información sobre cómo administrar usuarios e inicios de sesión al copiar una base de datos a un servidor lógico diferente.

## <a name="additional-resources"></a>Recursos adicionales
* [Administración de inicios de sesión](sql-database-manage-logins.md)
* [Conexión a la Base de datos SQL con SQL Server Management Studio y realización de una consulta de T-SQL de ejemplo](sql-database-connect-query-ssms.md)
* [Exportar la base de datos a un BACPAC](sql-database-export.md)
* [Información general acerca de la continuidad del negocio](sql-database-business-continuity.md)
* [Documentación de la base de datos SQL](https://azure.microsoft.com/documentation/services/sql-database/)




<!--HONumber=Nov16_HO3-->


