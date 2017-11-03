---
title: "Configuración de la seguridad de Azure SQL Database para recuperación ante desastres | Microsoft Docs"
description: "Conozca las consideraciones sobre seguridad para configurar y administrar la seguridad después de una restauración de la base de datos o una conmutación por error a un servidor secundario."
services: sql-database
documentationcenter: na
author: anosov1960
manager: jhubbard
editor: monicar
ms.assetid: c7c898c9-69d4-4e16-8b7e-720bbb3353dd
ms.service: sql-database
ms.custom: business continuity
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: Inactive
ms.date: 10/13/2016
ms.author: sashan
ms.openlocfilehash: 5bdcdba4956a12b54559b8accf822a4f41656045
ms.sourcegitcommit: dfd49613fce4ce917e844d205c85359ff093bb9c
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/31/2017
---
# <a name="configure-and-manage-azure-sql-database-security-for-geo-restore-or-failover"></a>Configuración y administración de la seguridad de Azure SQL Database para la restauración geográfica o la conmutación por error 

En este tema se describen los requisitos de autenticación para configurar y controlar la [replicación geográfica activa](sql-database-geo-replication-overview.md) y los pasos necesarios para configurar el acceso de usuario a la base de datos secundaria. También se describe cómo habilitar el acceso a la base de datos recuperada después de utilizar la [georrestauración](sql-database-recovery-using-backups.md#geo-restore). Para más información sobre opciones de recuperación, consulte [Información general sobre la continuidad empresarial](sql-database-business-continuity.md).

> [!NOTE]
> La [replicación geográfica activa](sql-database-geo-replication-overview.md) está ahora disponible para todas las bases de datos en todos los niveles de servicio.
>  

## <a name="disaster-recovery-with-contained-users"></a>Recuperación ante desastres con usuarios contenidos
A diferencia de los usuarios tradicionales, que deben asignarse a inicios de sesión en la base de datos maestra, un usuario independiente se administra completamente en la base de datos, lo que ofrece dos ventajas. En el escenario de replicación geográfica, los usuarios pueden proceder a conectarse a la nueva base de datos principal o a la base de datos recuperada mediante georrestauración, sin ninguna configuración adicional, ya que la base de datos administra los usuarios. También existen ventajas potenciales de escalabilidad y rendimiento con esta configuración desde la perspectiva del inicio de sesión. Para obtener más información, vea [Usuarios de base de datos independiente - Conversión de la base de datos en portátil](https://msdn.microsoft.com/library/ff929188.aspx). 

El principal inconveniente es que la administración del proceso de recuperación ante desastres a escala es más compleja. Si tiene varias bases de datos que usan el mismo inicio de sesión, el mantenimiento de las credenciales que usan los usuarios independientes en varias bases de datos puede invalidar las ventajas de los usuarios independientes. Por ejemplo, la directiva de rotación de contraseñas requiere que se realicen cambios constantemente en varias bases de datos en lugar de cambiar la contraseña para el inicio de sesión una vez en la base de datos maestra. Por este motivo, si tiene varias bases de datos que utilizan el mismo nombre de usuario y la misma contraseña, no se recomienda usar usuarios contenidos. 

## <a name="how-to-configure-logins-and-users"></a>Configuración de inicios de sesión y de usuarios
Si usa inicios de sesión y usuarios (en lugar de usuarios contenidos), debe realizar pasos adicionales para asegurarse de que existan los mismos inicios de sesión en la base de datos maestra. En las secciones siguientes, se describen los pasos necesarios y otras consideraciones.

### <a name="set-up-user-access-to-a-secondary-or-recovered-database"></a>Configuración del acceso de usuario a una base de datos secundaria o recuperada
Para que la base de datos secundaria se pueda utilizar como base de datos secundaria de solo lectura y garantizar el acceso adecuado a la nueva base de datos principal o a la base de datos recuperada mediante la georrestauración, la base de datos maestra del servidor de destino debe tener la configuración de seguridad adecuada antes de la recuperación.

Los permisos específicos para cada paso se describen más adelante en este tema.

La preparación del acceso de usuario a una base de datos secundaria de replicación geográfica debe realizarse como parte de la configuración de replicación geográfica. La preparación de acceso de usuario a las bases de datos georrestauradas debe realizarse en cualquier momento en que el servidor original esté en línea (por ejemplo, como parte de la obtención de detalles de recuperación ante desastres).

> [!NOTE]
> Si realiza una conmutación por error o la georrestauración en un servidor que no tiene configurado correctamente el acceso de inicio de sesión al mismo, se limitará a la cuenta de administrador del servidor.
> 
> 

La configuración de los inicios de sesión en el servidor de destino implica los tres pasos descritos a continuación:

#### <a name="1-determine-logins-with-access-to-the-primary-database"></a>1. Determinar los inicios de sesión con acceso a la base de datos principal:
El primer paso del proceso es determinar qué inicios de sesión se deben duplicar en el servidor de destino. Esto se logra con un par de instrucciones SELECT, una en la base de datos maestra lógica del servidor de origen y otra, en la base de datos principal en sí.

El administrador del servidor o un miembro del rol de servidor **LoginManager** son los únicos que pueden determinar los inicios de sesión en el servidor de origen con la siguiente instrucción SELECT. 

    SELECT [name], [sid] 
    FROM [sys].[sql_logins] 
    WHERE [type_desc] = 'SQL_Login'

Solo un miembro del rol de base de datos db_owner, el usuario dbo o el administrador del servidor pueden determinar todas las entidades de seguridad de usuario de base de datos en la base de datos principal.

    SELECT [name], [sid]
    FROM [sys].[database_principals]
    WHERE [type_desc] = 'SQL_USER'

#### <a name="2-find-the-sid-for-the-logins-identified-in-step-1"></a>2. Buscar al SID de los inicios de sesión que identificó en el paso 1:
Al comparar los resultados de las consultas de la sección anterior y cotejar los SID, puede asignar el inicio de sesión de servidor a un usuario de base de datos. Los inicios de sesión que cuenten con un usuario de base de datos con un SID coincidente tienen acceso de usuario a esa base de datos como entidad de seguridad de usuario de base de datos. 

La consulta siguiente se puede usar para ver todas las entidades de seguridad de usuario y sus SID en una base de datos. Solo un miembro del rol de servidor db_owner o el administrador del servidor pueden ejecutar esta consulta.

    SELECT [name], [sid]
    FROM [sys].[database_principals]
    WHERE [type_desc] = 'SQL_USER'

> [!NOTE]
> Los usuarios de **INFORMATION_SCHEMA** y **sys** tienen SID *NULL* SID, mientras que el SID de **guest** es **0x00**. El SID de **dbo** puede empezar por *0x01060000000001648000000000048454* si el creador de la base de datos era el administrador del servidor en lugar de un miembro de **DbManager**.
> 
> 

#### <a name="3-create-the-logins-on-the-target-server"></a>3. Crear los inicios de sesión en el servidor de destino:
El último paso consiste en ir al servidor o los servidores de destino y generar los inicios de sesión con los SID correspondientes. Esta es la sintaxis básica.

    CREATE LOGIN [<login name>]
    WITH PASSWORD = <login password>,
    SID = <desired login SID>

> [!NOTE]
> Si desea conceder acceso de usuario a la base de datos secundaria, pero no a la principal, puede hacerlo modificando el inicio de sesión de usuario en el servidor principal con la sintaxis siguiente.
> 
> ALTER LOGIN <login name> DISABLE
> 
> DISABLE no cambia la contraseña, por lo que siempre puede habilitarlo si es necesario.
> 
> 

## <a name="next-steps"></a>Pasos siguientes
* Para obtener más información sobre cómo administrar el acceso a la base de datos y los inicios de sesión, consulte [Seguridad de la Base de datos SQL: administrar la seguridad del inicio de sesión y el acceso a la base de datos](sql-database-manage-logins.md).
* Para obtener más información sobre los usuarios de base de datos independiente, consulte [Usuarios de base de datos independiente: hacer que la base de datos sea portátil](https://msdn.microsoft.com/library/ff929188.aspx).
* Para obtener información sobre cómo usar y configurar la funcionalidad de replicación geográfica activa, consulte [Replicación geográfica activa para Azure SQL Database](sql-database-geo-replication-overview.md).
* Para obtener información sobre cómo utilizar la funcionalidad de restauración geográfica, consulte [Restauración geográfica](sql-database-recovery-using-backups.md#geo-restore)

