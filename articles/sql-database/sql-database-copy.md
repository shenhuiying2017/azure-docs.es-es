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
ms.author: sashan
ms.workload: data-management
ms.topic: article
ms.tgt_pltfrm: NA
translationtype: Human Translation
ms.sourcegitcommit: 0356f3bdbb691b9bde4e906ed30b3b235180f8ba
ms.openlocfilehash: 9851ec0771c4fec4719aec8c8f43f2e3dd2604f0
ms.lasthandoff: 02/27/2017


---
# <a name="copy-an-azure-sql-database"></a>Copiar una base de datos SQL de Azure

Puede crear la copia de la base de datos en el mismo servidor o en otro distinto. La copia de la base de datos es una instantánea de la base de datos de origen en el momento de la solicitud de copia. El nivel de rendimiento y el nivel de servicio (plan de tarifa) de la copia de base de datos son los mismos que los de la base de datos de origen de forma predeterminada. Al usar la API, puede seleccionar un nivel de rendimiento diferente dentro del mismo nivel de servicio (edición). Cuando se complete la copia, esta se convierte en una base de datos independiente y completamente funcional. Llegado a este punto, puede actualizar a cualquier edición o cambiar a una edición anterior. Los inicios de sesión, usuarios y permisos pueden administrarse de forma independiente.  

Al copiar una base de datos en el mismo servidor lógico, los mismos inicios de sesión se pueden usar en ambas bases de datos. La entidad de seguridad que usa para copiar la base de datos se convierte en el propietario de la base de datos (DBO) en la nueva base de datos. Todos los usuarios de base de datos, sus permisos y sus identificadores de seguridad (SID) se copian en la copia de la base de datos.  

Al copiar una base de datos en un servidor lógico diferente, la entidad de seguridad del nuevo servidor se convierte en el propietario de la nueva base de datos. Si usa [usuarios de base de datos contenidos](sql-database-manage-logins.md) para el acceso a datos, asegúrese de que tanto las bases de datos principales como las secundarias tengan siempre las mismas credenciales de usuario, de tal forma que, una vez completada la copia, pueda obtener acceso inmediato a ellas con las mismas credenciales. Si usa [Azure Active Directory](../active-directory/active-directory-whatis.md), puede eliminar completamente la necesidad de administrar las credenciales en la copia. Pero al copiar la base de datos a un nuevo servidor, puede que el acceso basado en inicios de sesión no funcione debido a que esas cuentas de inicio de sesión no se encuentran en el nuevo servidor. Consulte [Administración de la seguridad de Azure SQL Database después de la recuperación ante desastres](sql-database-geo-replication-security-config.md) para obtener información sobre cómo administrar inicios de sesión al copiar una base de datos a un servidor lógico diferente. 

Puede copiar una base de datos SQL con [Azure Portal](sql-database-copy-portal.md), [PowerShell](sql-database-copy-powershell.md) o [T-SQL](sql-database-copy-transact-sql.md). 

## <a name="next-steps"></a>Pasos siguientes

* Consulte [Administración de la seguridad de Base de datos SQL de Azure después de la recuperación ante desastres](sql-database-geo-replication-security-config.md) para obtener información sobre cómo administrar usuarios e inicios de sesión al copiar una base de datos a un servidor lógico diferente.
* Para obtener información sobre inicios de sesión, vea [Manage logins](sql-database-manage-logins.md) (Administración de inicios de sesión).
* Para exportar una base de datos, vea [Exportación de una base de datos a un archivo BACPAC](sql-database-export.md).

