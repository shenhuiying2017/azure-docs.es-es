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
ms.workload: data-management
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 11/28/2016
ms.author: rickbyh;carlrab
translationtype: Human Translation
ms.sourcegitcommit: ad72adf626fb5f59f239f57ff0158db101017a1b
ms.openlocfilehash: 7925663bd4233e368f54b38c94888ac1b0f5dcab


---
# <a name="configure-an-azure-sql-database-server-level-firewall-rule-using-the-azure-portal"></a>Configuración de un firewall de nivel de servidor en Base de datos SQL de Azure mediante el Portal de Azure
> [!div class="op_single_selector"]
> * [Información general](sql-database-firewall-configure.md)
> * [Portal de Azure](sql-database-configure-firewall-settings.md)
> * [TSQL](sql-database-configure-firewall-settings-tsql.md)
> * [PowerShell](sql-database-configure-firewall-settings-powershell.md)
> * [API DE REST](sql-database-configure-firewall-settings-rest.md)
> 
> 

El servidor SQL de Azure usa reglas de firewall para permitir conexiones con servidores y bases de datos. Puede definir la configuración de firewall a nivel de servidor y de base de datos para el maestro o una base de datos de usuario en el servidor lógico del servidor SQL de Azure para permitir el acceso a la base de datos de forma selectiva. En este tema se describen las reglas de firewall de nivel de servidor.

> [!IMPORTANT]
> Para permitir que las aplicaciones de Azure se conecten al servidor SQL de Azure, deben habilitarse las conexiones de Azure. Para comprender cómo funcionan las reglas de firewall, consulte el artículo de [información general sobre cómo configurar un firewall en servidores SQL de Azure](sql-database-firewall-configure.md). Es posible que tenga que abrir algunos puertos TCP adicionales si está realizando conexiones dentro del límite de la nube de Azure. Para obtener más información, consulte la sección **Versión V12 de la Base de datos SQL: fuera frente a dentro** del artículo [Puertos más allá de 1433 para ADO.NET 4.5 y la Base de datos SQL V12](sql-database-develop-direct-route-ports-adonet-v12.md)
> 
> 

**Recomendación:** use reglas de firewall de nivel de servidor para administradores y cuando tenga muchas bases de datos con los mismos requisitos de acceso y no quiera dedicar tiempo a configurar individualmente cada base de datos. Microsoft recomienda usar reglas de firewall de nivel de base de datos siempre que sea posible a fin de mejorar la seguridad y aumentar la portabilidad de la base de datos.

[!INCLUDE [Create SQL Database database](../../includes/sql-database-create-new-server-firewall-portal.md)]

## <a name="manage-existing-server-level-firewall-rules-through-the-azure-portal"></a>Administración de reglas de firewall de nivel de servidor existentes a través del Portal de Azure
Repita los pasos para administrar las reglas de firewall a nivel de servidor.

* Para agregar el equipo actual, haga clic en Agregar IP de cliente.
* Para agregar direcciones IP adicionales, escriba el nombre de la regla, la dirección IP inicial y la dirección IP final.
* Para modificar una regla existente, haga clic en cualquiera de los campos de la regla y realice la modificación.
* Para eliminar una regla existente, mantenga el puntero sobre la regla hasta que aparezca la X al final de la fila. Haga clic en la X para quitar la regla.

Haga clic en **Guardar** para guardar los cambios.

## <a name="next-steps"></a>Pasos siguientes
Para leer artículos sobre cómo utilizar Transact-SQL para crear reglas de firewall de nivel de servidor y base de datos, consulte [Configuración de reglas de firewall de nivel de servidor y base de datos en Base de datos SQL de Azure mediante T-SQL](sql-database-configure-firewall-settings-tsql.md). 

Si desea consultar artículos sobre cómo crear reglas de firewall de nivel de servidor con otros métodos, visite: 

* [Configuración de reglas de firewall de nivel de servidor en Base de datos SQL de Azure mediante PowerShell](sql-database-configure-firewall-settings-powershell.md)
* [Configuración de reglas de firewall de nivel de servidor en Base de datos SQL de Azure mediante la API de REST](sql-database-configure-firewall-settings-rest.md)

Para ver un tutorial sobre cómo crear una base de datos, consulte [Tutorial de Base de datos SQL: creación de una Base de datos SQL en cuestión de minutos con datos de ejemplo y el Portal de Azure](sql-database-get-started.md).   

Para saber cómo crear más usuarios que puedan conectarse a bases de datos, consulte [Autorización y autenticación de SQL Database: concesión de acceso](https://msdn.microsoft.com/library/azure/ee336235.aspx).

## <a name="additional-resources"></a>Recursos adicionales
* [Protección de bases de datos](sql-database-security.md)   
* [Centro de seguridad para el Motor de base de datos de SQL Server y Base de datos SQL Azure](https://msdn.microsoft.com/library/bb510589)   






<!--HONumber=Nov16_HO5-->


