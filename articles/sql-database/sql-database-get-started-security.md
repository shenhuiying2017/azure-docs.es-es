---
title: "Tutorial de Base de datos SQL: Introducción a la seguridad"
description: Aprenda a crear cuentas de usuario para acceder a una base de datos y administrarla.
keywords: 
services: sql-database
documentationcenter: 
author: CarlRabeler
manager: jhubbard
editor: 
ms.assetid: 67797b09-f5c3-4ec2-8494-fe18883edf7f
ms.service: sql-database
ms.custom: authentication and authorization
ms.workload: data-management
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: hero-article
ms.date: 08/17/2016
ms.author: carlrab
translationtype: Human Translation
ms.sourcegitcommit: 09c2332589b1170b411c6f45f4109fb8048887e2
ms.openlocfilehash: e846654b659c1240451e3ac26973fbfce206936d


---
# <a name="sql-database-tutorial-create-sql-database-user-accounts-to-access-and-manage-a-database"></a>SQL Database tutorial: Create SQL database user accounts to access and manage a database (Tutorial de Base de datos SQL: Creación de cuentas de base de datos SQL para acceder a una base de datos y administrarla)
> [!div class="op_single_selector"]
> * [Tutorial introductorio](sql-database-get-started-security.md)
> * [Conceder acceso](sql-database-manage-logins.md)
> 
> 

En este tutorial, aprenderá a usar SQL Server Management Studio (SSMS) para:

* Iniciar sesión en Base de datos SQL mediante un inicio de sesión de entidad de seguridad de nivel de servidor.
* Crear una cuenta de usuario de Base de datos SQL.
* Conceder [permisos de db_owner](https://msdn.microsoft.com/library/ms189121.aspx#Anchor_0) de usuario de SQL Database.
* Conectarse a una base de datos SQL con una cuenta de usuario que no sea una entidad de seguridad de nivel de servidor.

[!INCLUDE [Login](../../includes/azure-getting-started-portal-login.md)]

[!INCLUDE [Create SQL Database logical server](../../includes/sql-database-sql-server-management-studio-connect-server-principal.md)]

[!INCLUDE [Create SQL Database database](../../includes/sql-database-create-new-database-user.md)]

[!INCLUDE [Create SQL Database database](../../includes/sql-database-grant-database-user-dbo-permissions.md)]

[!INCLUDE [Create SQL Database database](../../includes/sql-database-sql-server-management-studio-connect-user.md)]

## <a name="next-steps"></a>Pasos siguientes
Ahora que ha completado este tutorial de Base de datos SQL, ha creado una cuenta de usuario y ha concedido a la cuenta de usuario permisos de dbo, está preparado para aprender más sobre la [seguridad de Base de datos SQL](sql-database-manage-logins.md).




<!--HONumber=Dec16_HO1-->


