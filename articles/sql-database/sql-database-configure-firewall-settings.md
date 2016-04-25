<properties
	pageTitle="Configuración del firewall | Microsoft Azure"
	description="Aprenda a configurar el firewall para direcciones IP que obtengan acceso a bases de datos SQL de Azure."
	services="sql-database"
	documentationCenter=""
	authors="BYHAM"
	manager="jhubbard"
	editor=""/>


<tags
	ms.service="sql-database"
	ms.workload="data-management"
	ms.tgt_pltfrm="na"
	ms.devlang="dotnet"
	ms.topic="article" 
	ms.date="04/12/2016"
	ms.author="rickbyh;carlrab"/>


# Configuración del firewall en la Base de datos SQL mediante el Portal de Azure


> [AZURE.SELECTOR]
- [Portal de Azure](sql-database-configure-firewall-settings.md)
- [TSQL](sql-database-configure-firewall-settings-tsql.md)
- [PowerShell](sql-database-configure-firewall-settings-powershell.md)
- [API DE REST](sql-database-configure-firewall-settings-rest.md)

El servidor SQL de Azure usa reglas de firewall para permitir conexiones con servidores y bases de datos. Puede definir la configuración de firewall a nivel de servidor y de base de datos para el maestro o una base de datos de usuario en el servidor lógico del servidor SQL de Azure para permitir el acceso a la base de datos de forma selectiva.

> [AZURE.IMPORTANT] Para permitir que las aplicaciones de Azure se conecten al servidor SQL de Azure, deben habilitarse las conexiones de Azure. Para comprender cómo funcionan las reglas de firewall, consulte [Cómo configurar un firewall de Base de datos SQL de Azure](sql-database-firewall-configure.md). Es posible que tenga que abrir algunos puertos TCP adicionales si está realizando conexiones dentro del límite de la nube de Azure. Para obtener más información, consulte la sección **Versión V12 de la Base de datos SQL: fuera frente a dentro** del artículo [Puertos más allá de 1433 para ADO.NET 4.5 y la Base de datos SQL V12](sql-database-develop-direct-route-ports-adonet-v12.md)

[AZURE.INCLUDE [Creación de bases de datos SQL](../../includes/sql-database-create-new-server-firewall-portal.md)]

## Administración de reglas de firewall de nivel de servidor existentes a través del Portal de Azure

Repita los pasos para administrar las reglas de firewall a nivel de servidor.

- Para agregar el equipo actual, haga clic en Agregar IP de cliente.
- Para agregar direcciones IP adicionales, escriba el nombre de la regla, la dirección IP inicial y la dirección IP final.
- Para modificar una regla existente, haga clic en cualquiera de los campos de la regla y realice la modificación.
- Para eliminar una regla existente, mantenga el puntero sobre la regla hasta que aparezca la X al final de la fila. Haga clic en la X para quitar la regla.

Haga clic en **Guardar** para guardar los cambios.

## Pasos siguientes

Una regla de firewall de servidor afecta a todas las bases de datos SQL del servidor SQL de Azure. Si lo que desea es configurar una regla de firewall de nivel de base de datos que afecte solo a una base de datos única, consulte [sp\_set\_database\_firewall\_rule (Base de datos SQL Azure)](https://msdn.microsoft.com/library/dn270010.aspx").

Para obtener un tutorial sobre la creación de una base de datos, consulte [Creación de la primera base de datos SQL de Azure](sql-database-get-started.md). Para obtener ayuda para la conexión a una base de datos SQL de Azure desde código abierto o aplicaciones de terceros, consulte [Instrucciones para conectar con Base de datos SQL de Azure mediante programación](https://msdn.microsoft.com/library/azure/ee336282.aspx). Para conseguir más información acerca de cómo acceder a las bases de datos, consulte [Administrar bases de datos e inicios de sesión en la Base de datos SQL de Azure](https://msdn.microsoft.com/library/azure/ee336235.aspx).

<!--Image references-->
[1]: ./media/sql-database-configure-firewall-settings/AzurePortalBrowseForFirewall.png
[2]: ./media/sql-database-configure-firewall-settings/AzurePortalFirewallSettings.png
<!--anchors-->

 

<!---HONumber=AcomDC_0413_2016-->