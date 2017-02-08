---
title: "Realización de tareas administrativas, por ejemplo, restablecer la contraseña de administrador | Microsoft Docs"
description: "Describe como realizar tareas administrativas comunes en Base de datos SQL de Azure. Por ejemplo, restablecer la contraseña de administrador, conceder y revocar el acceso."
services: sql-database
documentationcenter: 
author: v-shysun
manager: felixwu
editor: 
keywords: "restablecer la contraseña del administrador"
ms.assetid: 9803fdcf-6501-4ac4-8cd0-f80071e052e1
ms.service: sql-database
ms.custom: troubleshoot
ms.workload: data-management
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/13/2016
ms.author: v-shysun
translationtype: Human Translation
ms.sourcegitcommit: e5b5751facb68ae4a62e3071fe4dfefc02434a9f
ms.openlocfilehash: 5b717afe388d9ad6d4d37fa07d351e55ac8dbbb8


---
# <a name="how-to-perform-common-administrative-tasks-such-as-resetting-admin-password-in-azure-sql-database"></a>Realización de tareas administrativas comunes como el restablecimiento de la contraseña de administrador en Base de datos SQL de Azure
Use este tema para pasos rápidos, para conceder y quitar el acceso a una base de datos SQL de Azure. Para información más completa, vea:

* [Administrar bases de datos e inicios de sesión en Base de datos SQL de Azure](sql-database-manage-logins.md)
* [Protección de bases de datos SQL](sql-database-security-overview.md)
* [Centro de seguridad para el Motor de base de datos de SQL Server y Base de datos SQL Azure](https://msdn.microsoft.com/library/bb510589)

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="to-reset-admin-password-for-a-logical-server"></a>Para restablecer la contraseña de administrador para un servidor lógico
* En [Azure Portal](https://portal.azure.com) haga clic en **Servidores SQL Server**, seleccione el servidor en la lista y luego haga clic en **Restablecer contraseña**.

## <a name="to-help-make-sure-only-authorized-ip-addresses-are-allowed-to-access-the-server"></a>Para asegurarse de que solo se permiten las direcciones IP autorizada para tener acceso al servidor
* Vea [Configuración del firewall en Base de datos SQL](sql-database-configure-firewall-settings.md).

## <a name="to-create-contained-database-users-in-the-user-database"></a>Para crear usuarios de bases de datos independientes en la base de datos de usuario
* Use la instrucción [CREATE USER](https://msdn.microsoft.com/library/ms173463.aspx) y consulte [Usuarios de base de datos independiente: hacer que la base de datos sea portátil](https://msdn.microsoft.com/library/ff929188.aspx).

## <a name="to-authenticate-contained-database-users-by-using-your-azure-active-directory"></a>Para autenticar usuarios de bases de datos independientes con Azure Active Directory
* Consulte [Conexión a Base de datos SQL o a Almacenamiento de datos SQL mediante autenticación de Azure Active Directory](sql-database-aad-authentication.md).

## <a name="to-create-additional-logins-for-high-privileged-users-in-the-virtual-master-database"></a>Para crear inicios de sesión adicionales para usuarios con privilegios elevados en la base de datos maestra virtual
* Use la instrucción [CREATE LOGIN](https://msdn.microsoft.com/library/ms189751.aspx) y consulte la sección Administración de inicios de sesión en [Administración de bases de datos e inicios de sesión en Azure SQL Database](sql-database-manage-logins.md) para más información.




<!--HONumber=Dec16_HO4-->


