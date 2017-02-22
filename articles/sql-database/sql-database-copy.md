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
ms.custom: migrate and move
ms.devlang: NA
ms.date: 10/24/2016
ms.author: sstein; sashan
ms.workload: data-management
ms.topic: article
ms.tgt_pltfrm: NA
translationtype: Human Translation
ms.sourcegitcommit: 21be71a1b4c79ecec8af02d08f65c41128c5ef73
ms.openlocfilehash: 265adbccee6764322148fad23f31aa1e279dfc9b


---
# <a name="copy-an-azure-sql-database"></a>Copiar una base de datos SQL de Azure

Puede usar la característica de [replicación geográfica activa](sql-database-geo-replication-overview.md)para crear una copia de la base de datos SQL. Pero, a diferencia de la replicación geográfica, finaliza el vínculo de replicación una vez completada la fase de propagación. Por lo tanto, la copia de la base de datos es una instantánea de la base de datos de origen en el momento de la solicitud de copia.  

Puede crear la copia de la base de datos en el mismo servidor o en otro distinto. El nivel de rendimiento y el nivel de servicio (plan de tarifa) de la copia de base de datos son los mismos que los de la base de datos de origen de forma predeterminada. Al usar la API, puede seleccionar un nivel de rendimiento diferente dentro del mismo nivel de servicio (edición). Cuando se complete la copia, esta se convierte en una base de datos independiente y completamente funcional. Llegado a este punto, puede actualizar a cualquier edición o cambiar a una edición anterior. Los inicios de sesión, usuarios y permisos pueden administrarse de forma independiente.  

Al copiar una base de datos en el mismo servidor lógico, los mismos inicios de sesión se pueden usar en ambas bases de datos. La entidad de seguridad que usa para copiar la base de datos se convierte en el propietario de la base de datos (DBO) en la nueva base de datos. Todos los usuarios de base de datos, sus permisos y sus identificadores de seguridad (SID) se copian en la copia de la base de datos.  

Al copiar una base de datos en un servidor lógico diferente, la entidad de seguridad del nuevo servidor se convierte en el propietario de la nueva base de datos. Si usa [usuarios de base de datos contenidos](sql-database-manage-logins.md) para el acceso a datos, asegúrese de que tanto las bases de datos principales como las secundarias tengan siempre las mismas credenciales de usuario, de tal forma que, una vez completada la copia, pueda obtener acceso inmediato a ellas con las mismas credenciales. Si usa [Azure Active Directory](../active-directory/active-directory-whatis.md), puede eliminar completamente la necesidad de administrar las credenciales en la copia. Pero al copiar la base de datos a un nuevo servidor, puede que el acceso basado en inicios de sesión no funcione debido a que esas cuentas de inicio de sesión no se encuentran en el nuevo servidor. Consulte [Administración de la seguridad de Azure SQL Database después de la recuperación ante desastres](sql-database-geo-replication-security-config.md) para obtener información sobre cómo administrar inicios de sesión al copiar una base de datos a un servidor lógico diferente. 

Para copiar una base de datos SQL, necesita lo siguiente:

* Una suscripción de Azure. Si necesita una suscripción a Azure, haga clic en la opción **PRUEBA GRATUITA** situada en la parte superior de esta página y, a continuación, vuelva para finalizar este artículo.
* Una base de datos SQL para copiar. Si no tiene una base de datos SQL, cree una siguiendo los pasos de este artículo: [Creación de la primera Base de datos SQL de Azure](sql-database-get-started.md).

## <a name="next-steps"></a>Pasos siguientes
* Consulte el artículo sobre cómo [copiar una base de datos SQL de Azure mediante Azure Portal](sql-database-copy-portal.md) para realizar una copia de una base de datos a través de Azure Portal.
* Consulte [Copia de una Base de datos SQL de Azure con PowerShell](sql-database-copy-powershell.md) para copiar una base de datos mediante PowerShell.
* Consulte [Copia de una Base de datos SQL de Azure con Transact-SQL](sql-database-copy-transact-sql.md) para copiar una base de datos mediante Transact-SQL.
* Consulte [Administración de la seguridad de Base de datos SQL de Azure después de la recuperación ante desastres](sql-database-geo-replication-security-config.md) para obtener información sobre cómo administrar usuarios e inicios de sesión al copiar una base de datos a un servidor lógico diferente.

## <a name="additional-resources"></a>Recursos adicionales
* [Administración de inicios de sesión](sql-database-manage-logins.md)
* [Conexión a la Base de datos SQL con SQL Server Management Studio y realización de una consulta de T-SQL de ejemplo](sql-database-connect-query-ssms.md)
* [Exportar la base de datos a un BACPAC](sql-database-export.md)
* [Información general acerca de la continuidad del negocio](sql-database-business-continuity.md)
* [Documentación de la base de datos SQL](https://azure.microsoft.com/documentation/services/sql-database/)




<!--HONumber=Feb17_HO2-->


