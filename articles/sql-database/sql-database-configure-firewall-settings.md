---
title: 'Azure Portal: reglas de firewall de nivel de servidor en Azure SQL Database | Microsoft Docs'
description: Aprenda a configurar las reglas de firewall de nivel de servidor de las direcciones IP que acceden a instancias de Azure SQL Server mediante Azure Portal.
services: sql-database
documentationcenter: 
author: BYHAM
manager: jhubbard
editor: 
ms.assetid: c3b206b5-af6e-41af-8306-db12ecfc1b5d
ms.service: sql-database
ms.custom: authentication and authorization
ms.workload: data-management
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 03/21/2017
ms.author: rickbyh
translationtype: Human Translation
ms.sourcegitcommit: ae230c012a17eb73c8993a32197c844c6abaa2a4
ms.openlocfilehash: 1939b69ee806d9091a21f3b21a276175d5a19a62
ms.lasthandoff: 02/17/2017


---
# <a name="create-and-manage-azure-sql-database-server-level-firewall-rules-using-the-azure-portal"></a>Creación y administración de reglas de firewall de nivel de servidor en Azure SQL Database mediante Azure Portal

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

- Para ver un tutorial sobre el aprovisionamiento y conexión de un servidor con los firewall de nivel de servidor, consulte [Tutorial: aprovisionamiento y acceso a una instancia de Azure SQL Database mediante Azure Portal y SQL Server Management Studio](sql-database-get-started.md).
- Para ver un tutorial sobre los firewall de nivel de base de datos y autenticación de SQL Server, consulte [Autenticación y autorización de SQL](sql-database-control-access-sql-authentication-get-started.md).
- Si desea obtener ayuda para conectarse a una base de datos SQL de Azure desde aplicaciones de código abierto o de terceros, consulte [Ejemplos de código de inicio rápido de cliente para Base de datos SQL](https://msdn.microsoft.com/library/azure/ee336282.aspx).
- Para saber cómo crear más usuarios que puedan conectarse a bases de datos, consulte [Autorización y autenticación de SQL Database: concesión de acceso](https://msdn.microsoft.com/library/azure/ee336235.aspx).

## <a name="additional-resources"></a>Recursos adicionales
* [Protección de bases de datos](sql-database-security-overview.md)   
* [Centro de seguridad para el Motor de base de datos de SQL Server y Base de datos SQL Azure](https://msdn.microsoft.com/library/bb510589)   




