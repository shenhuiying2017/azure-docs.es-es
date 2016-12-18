---
title: Reglas de firewall de nivel de servidor y base de datos en Azure SQL Database mediante T-SQL| Microsoft Docs
description: Aprenda a configurar el firewall para direcciones IP que obtengan acceso a bases de datos SQL de Azure.
services: sql-database
documentationcenter: 
author: BYHAM
manager: jhubbard
editor: 
ms.assetid: 71e692a1-5e2f-4a18-a6d6-527b849cf68e
ms.service: sql-database
ms.custom: authentication and authorization
ms.workload: data-management
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: get-started-article
ms.date: 08/30/2016
ms.author: rickbyh
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 8afac684b5c4588d84ec4070e99470664ec47ff3


---
# <a name="configure-azure-sql-database-server-level-and-database-level-firewall-rules-using-t-sql"></a>Configuración de reglas de firewall de nivel de servidor en Base de datos SQL de Azure mediante T-SQL
> [!div class="op_single_selector"]
> * [Información general](sql-database-firewall-configure.md)
> * [Portal de Azure](sql-database-configure-firewall-settings.md)
> * [TSQL](sql-database-configure-firewall-settings-tsql.md)
> * [PowerShell](sql-database-configure-firewall-settings-powershell.md)
> * [API DE REST](sql-database-configure-firewall-settings-rest.md)
> 
> 

Base de datos SQL de Microsoft Azure usa reglas de firewall para permitir conexiones con servidores y bases de datos. Puede definir la configuración de firewall de nivel de servidor y de base de datos para el maestro o una base de datos de usuario en el servidor de Base de datos SQL de Azure para permitir el acceso a la base de datos de forma selectiva.

> [!IMPORTANT]
> Para permitir que las aplicaciones de Azure se conecten al servidor de base de datos, deben habilitarse las conexiones de Azure. Para obtener más información sobre las reglas de firewall y sobre cómo habilitar las conexiones de Azure, consulte el artículo [Firewall de la Base de datos SQL de Azure](sql-database-firewall-configure.md). Es posible que tenga que abrir algunos puertos TCP adicionales si está realizando conexiones dentro del límite de la nube de Azure. Para obtener más información, consulte la sección **Versión V12 de la Base de datos SQL: fuera frente a dentro** del artículo [Puertos más allá de 1433 para ADO.NET 4.5 y la Base de datos SQL V12](sql-database-develop-direct-route-ports-adonet-v12.md)
> 
> 

## <a name="server-level-firewall-rules"></a>Reglas de firewall de nivel de servidor
Para crear una regla de firewall de nivel de servidor mediante Transact-SQL, debe ser el administrador de Azure Active Directory o usar el inicio de sesión de la entidad de seguridad de nivel de servidor.

1. Inicie una ventana de consulta y conéctese a la base de datos maestra virtual mediante SQL Server Management Studio.
2. Las reglas de firewall de nivel de servidor se pueden seleccionar, crear, actualizar o eliminar desde dentro de la ventana de consulta.
3. Para crear o actualizar las reglas de firewall de nivel de servidor, ejecute el procedimiento almacenado de `sp_set_firewall_rule` . El ejemplo siguiente habilita un intervalo de direcciones IP en el servidor Contoso.<br/>Comience comprobando qué reglas existen ya.
   
        SELECT * FROM sys.firewall_rules ORDER BY name;
   
    A continuación, agregue una regla de firewall.
   
        EXECUTE sp_set_firewall_rule @name = N'ContosoFirewallRule',
            @start_ip_address = '192.168.1.1', @end_ip_address = '192.168.1.10'
   
    Para eliminar una regla de firewall de nivel de servidor, ejecute el procedimiento almacenado sp_delete_firewall_rule. En el ejemplo siguiente se elimina la regla llamada ContosoFirewallRule.
   
        EXECUTE sp_delete_firewall_rule @name = N'ContosoFirewallRule'
   
   Para más información sobre estos procedimientos almacenados, consulte [sp_set_firewall_rule](https://msdn.microsoft.com/library/dn270017.aspx) y [sp_delete_firewall_rule](https://msdn.microsoft.com/library/dn270024.aspx).

## <a name="database-level-firewall-rules"></a>Reglas de firewall de nivel de base de datos
Solo un usuario de base de datos con el permiso **CONTROL** en la base de datos (como su propietario) puede crear una regla de firewall de nivel de base de datos.

1. Después de crear un firewall de nivel de servidor para la dirección IP, inicie una ventana de consulta a través del Portal clásico o mediante SQL Server Management Studio.
2. Conéctese a la base de datos para la que desea crear una regla de firewall de nivel de base de datos.
   
    Para crear una nueva regla de firewall de nivel de base de datos o actualizar una existente, ejecute el procedimiento almacenado `sp_set_database_firewall_rule` . En el ejemplo siguiente se crea una nueva regla de firewall llamada ContosoFirewallRule.
   
        EXEC sp_set_database_firewall_rule @name = N'ContosoFirewallRule', 
            @start_ip_address = '192.168.1.11', @end_ip_address = '192.168.1.11'
   
    Para eliminar una regla de firewall de nivel de base de datos existente, ejecute el procedimiento almacenado `sp_delete_database_firewall_rule` . En el ejemplo siguiente se elimina la regla llamada ContosoFirewallRule.
   `
   
        EXEC sp_delete_database_firewall_rule @name = N'ContosoFirewallRule'

Para más información sobre estos procedimientos almacenados, consulte [sp_set_database_firewall_rule](https://msdn.microsoft.com/library/dn270010.aspx) y [sp_delete_database_firewall_rule](https://msdn.microsoft.com/library/dn270030.aspx).

## <a name="next-steps"></a>Pasos siguientes
Si desea consultar artículos sobre cómo crear reglas de firewall de nivel de servidor con otros métodos, visite: 

* [Configuración de reglas de firewall de nivel de servidor en Base de datos SQL de Azure mediante el Portal de Azure](sql-database-configure-firewall-settings.md)
* [Configuración de reglas de firewall de nivel de servidor en Base de datos SQL de Azure mediante PowerShell](sql-database-configure-firewall-settings-powershell.md)
* [Configuración de reglas de firewall de nivel de servidor en Base de datos SQL de Azure mediante la API de REST](sql-database-configure-firewall-settings-rest.md)

Para ver un tutorial sobre cómo crear una base de datos, consulte [Tutorial de Base de datos SQL: creación de una Base de datos SQL en cuestión de minutos con datos de ejemplo y el Portal de Azure](sql-database-get-started.md).
Si desea obtener ayuda para conectarse a una base de datos SQL de Azure desde aplicaciones de código abierto o de terceros, consulte [Ejemplos de código de inicio rápido de cliente para Base de datos SQL](https://msdn.microsoft.com/library/azure/ee336282.aspx).
Para saber cómo obtener acceso a las bases de datos, consulte [Manage database access and login security](https://msdn.microsoft.com/library/azure/ee336235.aspx)(Administrar la seguridad del inicio de sesión y el acceso a la base de datos).

## <a name="additional-resources"></a>Recursos adicionales
* [Protección de bases de datos](sql-database-security.md)
* [Centro de seguridad para el Motor de base de datos de SQL Server y Base de datos SQL Azure](https://msdn.microsoft.com/library/bb510589)




<!--HONumber=Nov16_HO3-->


