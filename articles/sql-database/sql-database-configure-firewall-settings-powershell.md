<properties
	pageTitle="Configuración de reglas de firewall de nivel de servidor en Base de datos SQL de Azure mediante PowerShell| Microsoft Azure"
	description="Aprenda a configurar el firewall para direcciones IP que obtengan acceso a bases de datos SQL de Azure."
	services="sql-database"
	documentationCenter=""
	authors="stevestein"
	manager="jhubbard"
	editor=""/>


<tags
	ms.service="sql-database"
	ms.workload="data-management"
	ms.tgt_pltfrm="na"
	ms.devlang="dotnet"
	ms.topic="article"
	ms.date="08/09/2016"
	ms.author="sstein"/>


# Configuración de reglas de firewall de nivel de servidor en Base de datos SQL de Azure mediante PowerShell


> [AZURE.SELECTOR]
- [Información general](sql-database-firewall-configure.md)
- [Portal de Azure](sql-database-configure-firewall-settings.md)
- [TSQL](sql-database-configure-firewall-settings-tsql.md)
- [PowerShell](sql-database-configure-firewall-settings-powershell.md)
- [API DE REST](sql-database-configure-firewall-settings-rest.md)


Base de datos SQL de Azure usa reglas de firewall para permitir conexiones con servidores y bases de datos. Puede definir la configuración de firewall de nivel de servidor y de base de datos maestra o una base de datos de usuario en el servidor de Base de datos SQL para permitir el acceso a la base de datos de forma selectiva.

> [AZURE.IMPORTANT] Para permitir que las aplicaciones de Azure se conecten al servidor de base de datos, deben habilitarse las conexiones de Azure. Para obtener más información sobre las reglas de firewall y sobre cómo habilitar las conexiones de Azure, consulte el artículo [Firewall de la Base de datos SQL de Azure](sql-database-firewall-configure.md). Es posible que tenga que abrir algunos puertos TCP adicionales si está realizando conexiones dentro del límite de la nube de Azure. Para más información, consulte la sección Versión V12 de la Base de datos SQL: fuera frente a dentro del artículo [Puertos más allá de 1433 para ADO.NET 4.5 y la Base de datos SQL V12](sql-database-develop-direct-route-ports-adonet-v12.md).


[AZURE.INCLUDE [Inicio de una sesión de PowerShell](../../includes/sql-database-powershell.md)]

## Creación de reglas de firewall del servidor

Se pueden crear, actualizar y eliminar reglas de firewall de nivel de servidor mediante Azure PowerShell.

Para crear una nueva regla de firewall de nivel de servidor, ejecute el cmdlet New-AzureRmSqlServerFirewallRule. El ejemplo siguiente habilita un intervalo de direcciones IP en el servidor Contoso.

    New-AzureRmSqlServerFirewallRule -ResourceGroupName 'resourcegroup1' -ServerName 'Contoso' -FirewallRuleName "ContosoFirewallRule" -StartIpAddress '192.168.1.1' -EndIpAddress '192.168.1.10'		

Para modificar una regla de firewall de nivel de servidor existente, ejecute el cmdlet Set-AzureSqlDatabaseServerFirewallRule cmdlet. En el ejemplo siguiente se cambia el intervalo de direcciones IP aceptables para la regla llamada ContosoFirewallRule.

    Set-AzureRmSqlServerFirewallRule -ResourceGroupName 'resourcegroup1' –StartIPAddress 192.168.1.4 –EndIPAddress 192.168.1.10 –RuleName 'ContosoFirewallRule' –ServerName 'Contoso'

Para eliminar una regla de firewall de nivel de servidor existente, ejecute el cmdlet Remove-AzureSqlDatabaseServerFirewallRule cmdlet. En el ejemplo siguiente se elimina la regla llamada ContosoFirewallRule.

    Remove-AzureRmSqlServerFirewallRule –RuleName 'ContosoFirewallRule' –ServerName 'Contoso'


## Administración de reglas de firewall mediante PowerShell

También puede usar PowerShell para administrar las reglas de firewall. Para obtener más información, consulte los temas siguientes:

* [New-AzureRmSqlServerFirewallRule](https://msdn.microsoft.com/library/mt603860.aspx)
* [Remove-AzureRmSqlServerFirewallRule](https://msdn.microsoft.com/library/mt603588.aspx)
* [Set-AzureRmSqlServerFirewallRule](https://msdn.microsoft.com/library/mt603789.aspx)
* [Get-AzureRmSqlServerFirewallRule](https://msdn.microsoft.com/library/mt603586.aspx)


## Pasos siguientes

Para obtener información sobre cómo utilizar Transact-SQL para crear reglas de firewall de nivel de servidor y base de datos, consulte [Configuración de reglas de firewall de nivel de servidor y base de datos en Base de datos SQL de Azure mediante T-SQL](sql-database-configure-firewall-settings-tsql.md).

Para obtener información sobre cómo crear reglas de firewall de nivel de servidor con otros métodos, visite:

- [Configuración de reglas de firewall de nivel de servidor en Base de datos SQL de Azure mediante el Portal de Azure](sql-database-configure-firewall-settings.md)
- [Configuración de reglas de firewall de nivel de servidor en Base de datos SQL de Azure mediante la API de REST](sql-database-configure-firewall-settings-rest.md)

Para ver un tutorial sobre cómo crear una base de datos, consulte [Tutorial de Base de datos SQL: creación de una Base de datos SQL en cuestión de minutos con datos de ejemplo y el Portal de Azure](sql-database-get-started.md). Si desea obtener ayuda para conectarse a una base de datos SQL de Azure desde aplicaciones de código abierto o de terceros, consulte [Ejemplos de código de inicio rápido de cliente para Base de datos SQL](https://msdn.microsoft.com/library/azure/ee336282.aspx). Para saber cómo obtener acceso a las bases de datos, consulte [Manage database access and login security](https://msdn.microsoft.com/library/azure/ee336235.aspx) (Administrar la seguridad del inicio de sesión y el acceso a la base de datos).


## Recursos adicionales

- [Protección de bases de datos](sql-database-security.md)
- [Centro de seguridad para el Motor de base de datos de SQL Server y Base de datos SQL Azure](https://msdn.microsoft.com/library/bb510589)


<!--Image references-->
[1]: ./media/sql-database-configure-firewall-settings/AzurePortalBrowseForFirewall.png
[2]: ./media/sql-database-configure-firewall-settings/AzurePortalFirewallSettings.png
<!--anchors-->

<!---HONumber=AcomDC_0810_2016-->