---
title: "Configuración de reglas de firewall de nivel de servidor de SQL Database | Microsoft Docs"
description: "Descubra cómo configurar el firewall para direcciones IP que accedan al servidor SQL de Azure."
services: sql-database
documentationcenter: 
author: BYHAM
manager: jhubbard
editor: 
ms.assetid: c3b206b5-af6e-41af-8306-db12ecfc1b5d
ms.service: sql-database
ms.custom: auth and access
ms.workload: data-management
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: hero-article
ms.date: 11/28/2016
ms.author: rickbyh;carlrab
translationtype: Human Translation
ms.sourcegitcommit: 867f06c1fae3715ab03ae4a3ff4ec381603e32f7
ms.openlocfilehash: 1479595fb7de2e0a37520c7d323624142e624af1


---
# <a name="create-and-manage-azure-sql-database-server-level-firewall-rules-using-the-azure-portal"></a>Creación y administración de reglas de firewall de nivel de servidor en Azure SQL Database mediante Azure Portal
> [!div class="op_single_selector"]
> * [Información general](sql-database-firewall-configure.md)
> * [Portal de Azure](sql-database-configure-firewall-settings.md)
> * [TSQL](sql-database-configure-firewall-settings-tsql.md)
> * [PowerShell](sql-database-configure-firewall-settings-powershell.md)
> * [API DE REST](sql-database-configure-firewall-settings-rest.md)
> 

Las reglas de firewall de nivel de servidor permiten a los administradores tener acceso a un servidor de SQL Database desde una dirección IP o intervalo de direcciones IP especificado. También puede usar reglas de firewall de nivel de servidor para usuarios cuando tenga muchas bases de datos con los mismos requisitos de acceso y no quiera dedicar tiempo a configurar individualmente cada una de ellas. Microsoft recomienda usar reglas de firewall de nivel de base de datos siempre que sea posible a fin de mejorar la seguridad y aumentar la portabilidad de la base de datos. Para obtener información general acerca de los firewalls de SQL Database, consulte [Introducción a las reglas de firewall de SQL Database](sql-database-firewall-configure.md).

> [!Note]
> Para más información acerca de bases de datos portátiles en el contexto de la continuidad empresarial, consulte [Requisitos de autenticación para la recuperación ante desastres](sql-database-geo-replication-security-config.md).
>

[!INCLUDE [Create SQL Database firewall rule](../../includes/sql-database-create-new-server-firewall-portal.md)]

## <a name="manage-existing-server-level-firewall-rules-through-the-azure-portal"></a>Administración de reglas de firewall de nivel de servidor existentes a través del Portal de Azure
Repita los pasos para administrar las reglas de firewall a nivel de servidor.

* Para agregar el equipo actual, haga clic en Agregar IP de cliente.
* Para agregar direcciones IP adicionales, escriba el nombre de la regla, la dirección IP inicial y la dirección IP final.
* Para modificar una regla existente, haga clic en cualquiera de los campos de la regla y realice la modificación.
* Para eliminar una regla existente, mantenga el puntero sobre la regla hasta que aparezca la X al final de la fila. Haga clic en la X para quitar la regla.

Haga clic en **Guardar** para guardar los cambios.

## <a name="next-steps"></a>Pasos siguientes

- Para ver un tutorial de introducción, consulte [Tutorial de SQL Database: crear un servidor, una regla de firewall de nivel de servidor, una base de datos de ejemplo, una regla de firewall de nivel de base de datos y conectar con SQL Server](sql-database-get-started.md).
- Para una introducción al tutorial de seguridad, consulte [Introducción a la seguridad](sql-database-get-started-security.md)
- Si desea obtener ayuda para conectarse a una base de datos SQL de Azure desde aplicaciones de código abierto o de terceros, consulte [Ejemplos de código de inicio rápido de cliente para Base de datos SQL](https://msdn.microsoft.com/library/azure/ee336282.aspx).
- Para saber cómo crear más usuarios que puedan conectarse a bases de datos, consulte [Autorización y autenticación de SQL Database: concesión de acceso](https://msdn.microsoft.com/library/azure/ee336235.aspx).

## <a name="additional-resources"></a>Recursos adicionales
* [Protección de bases de datos](sql-database-security.md)   
* [Centro de seguridad para el Motor de base de datos de SQL Server y Base de datos SQL Azure](https://msdn.microsoft.com/library/bb510589)   






<!--HONumber=Dec16_HO2-->


