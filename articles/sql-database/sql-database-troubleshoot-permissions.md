<properties
	pageTitle="Realización de tareas administrativas, por ejemplo, restablecer la contraseña de administrador | Microsoft Azure"
	description="Describe como realizar tareas administrativas comunes en Base de datos SQL de Azure. Por ejemplo, restablecer la contraseña de administrador, conceder y revocar el acceso."
	services="sql-database"
	documentationCenter=""
	authors="v-shysun"
	manager="felixwu"
	editor=""
	keywords="restablecer la contraseña del administrador"/>

<tags
	ms.service="sql-database"
	ms.workload="data-management"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="09/13/2016"
	ms.author="v-shysun"/>

# Realización de tareas administrativas comunes como el restablecimiento de la contraseña de administrador en Base de datos SQL de Azure
Use este tema para pasos rápidos, para conceder y quitar el acceso a una base de datos SQL de Azure. Para información más completa, vea:

- [Administrar bases de datos e inicios de sesión en Base de datos SQL de Azure](sql-database-manage-logins.md)
- [Protección de bases de datos SQL](sql-database-security.md)
- [Centro de seguridad para el Motor de base de datos de SQL Server y Base de datos SQL Azure](https://msdn.microsoft.com/library/bb510589)


[AZURE.INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## Para restablecer la contraseña de administrador para un servidor lógico

- En el [Portal de Azure](https://portal.azure.com) haga clic en **Servidores SQL Server**, seleccione el servidor en la lista y luego haga clic en **Restablecer contraseña**.

## Para asegurarse de que solo se permiten las direcciones IP autorizada para tener acceso al servidor
- Vea [Configuración del firewall en Base de datos SQL](sql-database-configure-firewall-settings.md).

## Para crear usuarios de bases de datos independientes en la base de datos de usuario
- Use la instrucción [CREATE USER](https://msdn.microsoft.com/library/ms173463.aspx) y consulte [Usuarios de base de datos independiente: hacer que la base de datos sea portátil](https://msdn.microsoft.com/library/ff929188.aspx).

## Para autenticar usuarios de bases de datos independientes con Azure Active Directory
- Consulte [Conexión a Base de datos SQL o a Almacenamiento de datos SQL mediante autenticación de Azure Active Directory](sql-database-aad-authentication.md).

## Para crear inicios de sesión adicionales para usuarios con privilegios elevados en la base de datos maestra virtual
- Use la instrucción [CREATE LOGIN](https://msdn.microsoft.com/library/ms189751.aspx) y consulte la sección "Administradores de inicio de sesión" en [Autorización y autenticación de Base de datos SQL: concesión de acceso](sql-database-manage-logins.md) para más información.

<!---HONumber=AcomDC_0914_2016-->