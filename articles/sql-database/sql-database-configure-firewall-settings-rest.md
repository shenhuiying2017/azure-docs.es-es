---
title: Configuración de reglas de firewall de nivel de servidor en Base de datos SQL de Azure mediante la API de REST | Microsoft Docs
description: Aprenda a configurar el firewall para direcciones IP que obtengan acceso a bases de datos SQL de Azure.
services: sql-database
documentationcenter: ''
author: stevestein
manager: jhubbard
editor: ''

ms.service: sql-database
ms.workload: data-management
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 08/09/2016
ms.author: sstein

---
# Configuración de reglas de firewall de nivel de servidor en Base de datos SQL de Azure mediante la API de REST
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

## Administración de reglas de firewall de nivel de servidor a través de la API de REST
1. La administración de reglas de firewall a través de la API de REST debe autenticarse. Para obtener más información, consulte [Guía del desarrollador para la autorización con la API de Azure Resource Manager](../resource-manager-api-authentication.md).
2. Las reglas de nivel de servidor se pueden crear, actualizar o eliminar mediante la API de REST
   
    Para crear o actualizar una regla de firewall de nivel de servidor, ejecute el método PUT mediante lo siguiente:
   
        https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.Sql/servers/{server-name}/firewallRules/{rule-name}?api-version={api-version}
   
    Cuerpo de la solicitud
   
        {
         "properties": { 
            "startIpAddress": "{start-ip-address}", 
            "endIpAddress": "{end-ip-address}
            }
        } 

    Para quitar una regla de firewall de nivel de servidor existente, ejecute el método DELETE mediante lo siguiente:

        https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.Sql/servers/{server-name}/firewallRules/{rule-name}?api-version={api-version}


## Administración de reglas de firewall mediante la API de REST
* [Crear o actualizar regla de firewall](https://msdn.microsoft.com/library/azure/mt445501.aspx)
* [Eliminar regla de firewall](https://msdn.microsoft.com/library/azure/mt445502.aspx)
* [Obtener regla de firewall.](https://msdn.microsoft.com/library/azure/mt445503.aspx)
* [Enumerar todas las reglas de firewall](https://msdn.microsoft.com/library/azure/mt604478.aspx)

## Pasos siguientes
Para leer artículos sobre cómo utilizar Transact-SQL para crear reglas de firewall de nivel de servidor y base de datos, consulte [Configuración de reglas de firewall de nivel de servidor y base de datos en Base de datos SQL de Azure mediante T-SQL](sql-database-configure-firewall-settings-tsql.md).

Si desea consultar artículos sobre cómo crear reglas de firewall de nivel de servidor con otros métodos, visite:

* [Configuración de reglas de firewall de nivel de servidor en Base de datos SQL de Azure mediante el Portal de Azure](sql-database-configure-firewall-settings.md)
* [Configuración de reglas de firewall de nivel de servidor en Base de datos SQL de Azure mediante PowerShell](sql-database-configure-firewall-settings-powershell.md)

Para ver un tutorial sobre cómo crear una base de datos, consulte [Tutorial de Base de datos SQL: creación de una Base de datos SQL en cuestión de minutos con datos de ejemplo y el Portal de Azure](sql-database-get-started.md). Si desea obtener ayuda para conectarse a una base de datos SQL de Azure desde aplicaciones de código abierto o de terceros, consulte [Ejemplos de código de inicio rápido de cliente para Base de datos SQL](https://msdn.microsoft.com/library/azure/ee336282.aspx). Para saber cómo obtener acceso a las bases de datos, consulte [Manage database access and login security](https://msdn.microsoft.com/library/azure/ee336235.aspx) (Administrar la seguridad del inicio de sesión y el acceso a la base de datos).

## Recursos adicionales
* [Protección de bases de datos](sql-database-security.md)
* [Centro de seguridad para el Motor de base de datos de SQL Server y Base de datos SQL Azure](https://msdn.microsoft.com/library/bb510589)

<!--Image references-->
[1]: ./media/sql-database-configure-firewall-settings/AzurePortalBrowseForFirewall.png
[2]: ./media/sql-database-configure-firewall-settings/AzurePortalFirewallSettings.png
<!--anchors-->



<!---HONumber=AcomDC_0810_2016-->