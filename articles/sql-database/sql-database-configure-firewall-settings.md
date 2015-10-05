<properties
	pageTitle="Configuración del firewall | Microsoft Azure"
	description="Aprenda a configurar el firewall para direcciones IP que obtengan acceso a bases de datos SQL de Azure."
	services="sql-database"
	documentationCenter=""
	authors="BYHAM"
	manager="jeffreyg"
	editor=""/>


<tags
	ms.service="sql-database"
	ms.workload="data-management"
	ms.tgt_pltfrm="na"
	ms.devlang="dotnet"
	ms.topic="article" 
	ms.date="09/04/2015"
	ms.author="rickbyh"/>


# Configuración del firewall en Base de datos SQL


Base de datos SQL de Microsoft Azure usa reglas de firewall para permitir conexiones con servidores y bases de datos. Puede definir la configuración de firewall de nivel de servidor y de base de datos para el maestro o una base de datos de usuario en el servidor de Base de datos SQL de Azure para permitir el acceso a la base de datos de forma selectiva.

> [AZURE.IMPORTANT]Para permitir que las aplicaciones de Azure se conecten al servidor de base de datos, deben habilitarse las conexiones de Azure. Para obtener más información sobre las reglas de firewall y sobre la habilitación de las conexiones de Azure, vea [Firewall de Base de datos SQL de Azure](sql-database-firewall-configure.md). Es posible que tenga que abrir algunos puertos TCP adicionales si está realizando conexiones dentro del límite de la nube de Azure. Para obtener más información, vea la sección **V12 de Base de datos SQL: fuera frente a dentro** de [Puertos más allá de 1433 para ADO.NET 4.5 y Base de datos SQL V12](sql-database-develop-direct-route-ports-adonet-v12.md).


## Reglas de firewall de nivel de servidor

Pueden crear reglas de firewall de nivel de servidor y administrarlas a través del Portal de administración de Microsoft Azure, Transact-SQL, Azure PowerShell o la API de REST.

### Administración de reglas de firewall de nivel de servidor a través del nuevo Portal de Azure


[AZURE.INCLUDE [sql-database-include-ip-address-22-v12portal](../../includes/sql-database-include-ip-address-22-v12portal.md)]


## Administración de reglas de firewall de nivel de servidor a través del Portal de administración 

1. En el Portal de administración, haga clic en **Bases de datos SQL**. Aquí se enumeran todas las bases de datos y los servidores correspondientes.
2. Haga clic en **Servers** en la parte superior de la página.
3. Haga clic en la flecha al lado del servidor para el que desea administrar las reglas de firewall.
4. Haga clic en **Configurar** en la parte superior de la página.

	*  Para agregar el equipo actual, haga clic en Agregar a las direcciones IP permitidas.
	*  Para agregar direcciones IP adicionales, escriba el nombre de la regla, la dirección IP inicial y la dirección IP final.
	*  Para modificar una regla existente, haga clic en cualquiera de los campos de la regla y realice la modificación.
	*  Para eliminar una regla existente, mantenga el puntero sobre la regla hasta que aparezca la X al final de la fila. Haga clic en la X para quitar la regla.
5. Para guardar los cambios, haga clic en **Guardar** en la parte inferior de la página.

## Administración de reglas de firewall de nivel de servidor a través de Transact-SQL

1. Inicie una ventana de consulta a través del Portal de administración o mediante SQL Server Management Studio.
2. Compruebe que está conectado a la base de datos maestra.
3. Las reglas de firewall de nivel de servidor se pueden seleccionar, crear, actualizar o eliminar desde dentro de la ventana de consulta.
4. Para crear o actualizar las reglas de firewall de nivel de servidor, ejecute el procedimiento almacenado de regla sp\_set\_firewall. El ejemplo siguiente habilita un intervalo de direcciones IP en el servidor Contoso.<br/>Comience comprobando qué reglas existen ya.

		SELECT * FROM sys.database_firewall_rules ORDER BY name;

	A continuación, agregue una regla de firewall.

		EXECUTE sp_set_firewall_rule @name = N'ContosoFirewallRule',
			@start_ip_address = '192.168.1.1', @end_ip_address = '192.168.1.10'

	Para eliminar una regla de firewall de nivel de servidor, ejecute el procedimiento almacenado sp\_delete\_firewall\_rule. En el ejemplo siguiente se elimina la regla llamada ContosoFirewallRule.
 
		EXECUTE sp_delete_firewall_rule @name = N'ContosoFirewallRule'
 
## Administración de reglas de firewall de nivel de servidor a través de Azure PowerShell
1. Inicie Azure PowerShell.
2. Se pueden crear, actualizar y eliminar reglas de firewall de nivel de servidor mediante Azure PowerShell. 

	Para crear una nueva regla de firewall de nivel de servidor, ejecute el cmdlet New-AzureSqlDatabaseServerFirewallRule cmdlet. El ejemplo siguiente habilita un intervalo de direcciones IP en el servidor Contoso.
 
		New-AzureSqlDatabaseServerFirewallRule –StartIPAddress 192.168.1.1 –EndIPAddress 192.168.1.10 –RuleName ContosoFirewallRule –ServerName Contoso
 
	Para modificar una regla de firewall de nivel de servidor existente, ejecute el cmdlet Set-AzureSqlDatabaseServerFirewallRule cmdlet. En el ejemplo siguiente se cambia el intervalo de direcciones IP aceptables para la regla llamada ContosoFirewallRule.
 
		Set-AzureSqlDatabaseServerFirewallRule –StartIPAddress 192.168.1.4 –EndIPAddress 192.168.1.10 –RuleName ContosoFirewallRule –ServerName Contoso

	Para eliminar una regla de firewall de nivel de servidor existente, ejecute el cmdlet Remove-AzureSqlDatabaseServerFirewallRule cmdlet. En el ejemplo siguiente se elimina la regla llamada ContosoFirewallRule.

		Remove-AzureSqlDatabaseServerFirewallRule –RuleName ContosoFirewallRule –ServerName Contoso
 
## Administración de reglas de firewall de nivel de servidor a través de la API de REST
1. La administración de reglas de firewall a través de la API de REST debe autenticarse. Para obtener información, vea Autenticar solicitudes de administración del servicio.
2. Las reglas de nivel de servidor se pueden crear, actualizar o eliminar mediante la API de REST

	Para crear o actualizar una regla de firewall de nivel de servidor, ejecute el método POST mediante lo siguiente:
 
		https://management.core.windows.net:8443/{subscriptionId}/services/sqlservers/servers/Contoso/firewallrules
	
	Cuerpo de la solicitud

		<ServiceResource xmlns="http://schemas.microsoft.com/windowsazure">
		  <Name>ContosoFirewallRule</Name>
		  <StartIPAddress>192.168.1.4</StartIPAddress>
		  <EndIPAddress>192.168.1.10</EndIPAddress>
		</ServiceResource>
 

	Para quitar una regla de firewall de nivel de servidor existente, ejecute el método DELETE mediante lo siguiente:
	 
		https://management.core.windows.net:8443/{subscriptionId}/services/sqlservers/servers/Contoso/firewallrules/ContosoFirewallRule
 
## Reglas de firewall de nivel de base de datos

1. Después de crear un firewall de nivel de servidor para la dirección IP, inicie una ventana de consulta a través del Portal de administración o mediante SQL Server Management Studio.
2. Conéctese a la base de datos para la que desea crear una regla de firewall de nivel de base de datos.

	Para crear una nueva regla de firewall de nivel de base de datos o actualizar una existente, ejecute el procedimiento almacenado sp\_set\_database\_firewall\_rule. En el ejemplo siguiente se crea una nueva regla de firewall llamada ContosoFirewallRule.
 
		EXEC sp_set_database_firewall_rule @name = N'ContosoFirewallRule', @start_ip_address = '192.168.1.11', @end_ip_address = '192.168.1.11'
 
	Para eliminar una nueva regla de firewall de nivel de base de datos existente, ejecute el procedimiento almacenado sp\_delete\_database\_firewall\_rule. En el ejemplo siguiente se elimina la regla llamada ContosoFirewallRule.
 
		EXEC sp_delete_database_firewall_rule @name = N'ContosoFirewallRule'


## Administración de reglas de firewall mediante la API de REST de administración del servicio

* [Crear regla de firewall](https://msdn.microsoft.com/library/azure/dn505712.aspx)
* [Eliminar regla de firewall](https://msdn.microsoft.com/library/azure/dn505706.aspx)
* [Obtener regla de firewall](https://msdn.microsoft.com/library/azure/dn505698.aspx).
* [Enumerar reglas de firewall](https://msdn.microsoft.com/library/azure/dn505715.aspx)

## Administración de reglas de firewall mediante PowerShell

* [New-AzureSqlDatabaseServerFirewallRule](https://msdn.microsoft.com/library/azure/dn546724.aspx)
* [Remove-AzureSqlDatabaseServerFirewallRule](https://msdn.microsoft.com/library/azure/dn546727.aspx)
* [Set-AzureSqlDatabaseServerFirewallRule](https://msdn.microsoft.com/library/azure/dn546739.aspx)
* [Get-AzureSqlDatabaseServerFirewallRule](https://msdn.microsoft.com/library/azure/dn546731.aspx)
 
## Pasos siguientes

Para obtener un tutorial sobre la creación de una base de datos, vea [Creación de la primera base de datos SQL de Azure](sql-database-get-started.md). Para obtener ayuda para la conexión a una base de datos SQL de Azure desde aplicaciones de código abierto o de terceros, vea [Instrucciones para conectar con Base de datos SQL de Azure mediante programación](https://msdn.microsoft.com/library/azure/ee336282.aspx). Para entender cómo navegar a las bases de datos, vea [Administrar bases de datos, inicios de sesión y usuarios en Base de datos SQL de Microsoft Azure](https://msdn.microsoft.com/library/azure/ee336235.aspx).

<!--Image references-->
[1]: ./media/sql-database-configure-firewall-settings/AzurePortalBrowseForFirewall.png
[2]: ./media/sql-database-configure-firewall-settings/AzurePortalFirewallSettings.png
<!--anchors-->

<!---HONumber=Sept15_HO4-->