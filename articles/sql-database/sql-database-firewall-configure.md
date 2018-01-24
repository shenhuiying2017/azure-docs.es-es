---
title: Reglas de firewall de Azure SQL Database | Microsoft Docs
description: "Obtenga información acerca de cómo configurar un firewall de base de datos SQL mediante las reglas de firewall de nivel de servidor y de nivel de base de datos, para administrar el acceso."
keywords: firewall de base de datos
services: sql-database
documentationcenter: 
author: CarlRabeler
manager: jhubbard
editor: cgronlun
tags: 
ms.assetid: ac57f84c-35c3-4975-9903-241c8059011e
ms.service: sql-database
ms.custom: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: Active
ms.date: 10/11/2017
ms.author: carlrab
ms.openlocfilehash: 1988bc7ab5b498db32d7bb40623f1194d7290b94
ms.sourcegitcommit: 9ea2edae5dbb4a104322135bef957ba6e9aeecde
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/03/2018
---
# <a name="azure-sql-database-server-level-and-database-level-firewall-rules"></a>Reglas de firewall de nivel de servidor y de nivel de base de datos de Azure SQL Database 

Microsoft Azure SQL Database ofrece un servicio de base de datos relacional para Azure y otras aplicaciones basadas en Internet. Para ayudar a proteger los datos, los firewalls impiden todo acceso al servidor de bases de datos, excepto a aquellos equipos a los que haya concedido permiso. Asimismo, otorgan acceso a las bases de datos según la dirección IP de origen de cada solicitud.

#### <a name="virtual-network-rules-as-alternatives-to-ip-rules"></a>Reglas de red virtual alternativas a las reglas IP

Además de las reglas IP, el firewall también administra *reglas de red virtual*. Las reglas de red virtual se basan en los puntos de conexión del servicio de Virtual Network. Es posible que las reglas de red virtual sean preferibles a las reglas IP en algunos casos. Para obtener más información, consulte [Reglas y puntos de conexión del servicio de Virtual Network para Azure SQL Database](sql-database-vnet-service-endpoint-rule-overview.md).

## <a name="overview"></a>Información general

Inicialmente, todo el acceso mediante instrucciones Transact-SQL al servidor SQL de Azure está bloqueado por el firewall. Para comenzar a usar Azure SQL Server, debe especificar una o varias reglas de firewall de nivel de servidor que permitan acceder a Azure SQL Server. Use las reglas de firewall para especificar qué intervalos de direcciones IP de Internet se permiten y si las aplicaciones de Azure pueden tratar de conectarse al servidor SQL de Azure.

Para conceder selectivamente el acceso a solo una de las bases de datos en el servidor SQL de Azure, debe crear una regla de nivel de base de datos para la base de datos necesaria. Especifique un intervalo de direcciones IP para la regla de firewall de base de datos que quede fuera del intervalo de direcciones IP especificado en la regla de firewall de nivel de servidor. Además, asegúrese de que la dirección IP del cliente se encuentre en el intervalo especificado en la regla de nivel de base de datos.

Los intentos de conexión desde Internet y Azure deben atravesar primero el firewall antes de poder alcanzar SQL Database y el servidor SQL de Azure, tal y como se muestra en el siguiente diagrama:

   ![Diagrama donde se describe la configuración del firewall.][1]

* **Reglas de firewall de nivel de servidor:** estas reglas permiten a los clientes acceder a todo el servidor SQL de Azure; es decir, a todas las bases de datos que se encuentren en el mismo servidor lógico. Estas reglas se almacenan en la base de datos **maestra** . Las reglas de firewall de nivel de servidor pueden configurarse a través del Portal o mediante instrucciones Transact-SQL. Para poder crear reglas de firewall en el nivel del servidor mediante Azure Portal o PowerShell, debe ser el propietario o un colaborador de la suscripción. Para crear reglas de firewall en el nivel de servidor mediante Transact-SQL, debe conectarse a una instancia de SQL Database con el inicio de sesión principal del nivel de servidor o como administrador de Azure Active Directory (lo que significa que la regla de firewall del nivel de servidor debe crearla primero un usuario con permisos en el nivel de Azure).
* **Reglas de firewall de nivel de base de datos:** estas reglas permiten a los clientes acceder a determinadas bases de datos dentro del mismo servidor lógico. Puede crear estas reglas para cada base de datos (incluida la base datos **maestra**), y se almacenan en las bases de datos individuales. Las reglas de firewall de nivel de base de datos para las bases de datos de usuario y maestras solo se pueden crear y administrar mediante instrucciones Transact-SQL y solo después de haber configurado la primera regla de firewall de nivel de servidor. Si especifica un intervalo de direcciones IP en la regla de firewall de nivel de base de datos que se encuentra fuera del intervalo especificado en la regla de firewall de nivel de servidor, solo los clientes que tengan direcciones IP en el intervalo de nivel de base de datos pueden tener acceso a la base de datos. Puede tener un máximo de 128 reglas de firewall de nivel de base de datos para una base de datos. Para más información sobre cómo configurar las reglas de firewall de nivel de base de datos, consulte el ejemplo más adelante en este artículo y vea [sp_set_database_firewall_rule (Azure SQL Database)](https://msdn.microsoft.com/library/dn270010.aspx).

**Recomendación** : Microsoft recomienda usar reglas de firewall de nivel de base de datos siempre que sea posible con el fin de mejorar la seguridad y hacer que la base de datos sea más portátil. Utilice reglas de firewall de nivel de servidor para administradores y cuando tenga muchas bases de datos con los mismos requisitos de acceso y no quiera dedicar tiempo a configurar individualmente cada una de ellas.

> [!Important]
> Windows Azure SQL Database admite un máximo de 128 reglas de firewall.
>

> [!Note]
> Para más información acerca de bases de datos portátiles en el contexto de la continuidad empresarial, consulte [Requisitos de autenticación para la recuperación ante desastres](sql-database-geo-replication-security-config.md).
>

### <a name="connecting-from-the-internet"></a>Conexión desde Internet

Cuando un equipo intenta conectarse al servidor de bases de datos desde Internet, el firewall comprueba la dirección IP de origen de la solicitud con las reglas de firewall de nivel de base de datos, para la base de datos que está solicitando la conexión:

* Si la dirección IP de la solicitud está comprendida en uno de los intervalos especificados en las reglas de firewall de nivel de base de datos, la conexión se concede a la instancia de SQL Database que contiene la regla.
* Si la dirección IP de la solicitud no está comprendida en uno de los intervalos especificados en la regla de firewall de nivel de base de datos, se comprueban las reglas de firewall de nivel de servidor. Si la dirección IP de la solicitud está comprendida en uno de los intervalos especificados en las reglas de firewall de nivel de servidor, la conexión se concede. Las reglas de firewall de nivel de servidor se aplican a todas las instancias de SQL Database en SQL Server de Azure.  
* Si la dirección IP de la solicitud no se encuentra dentro de los intervalos especificados en cualquiera de las reglas de firewall de nivel de base de datos o de servidor, la solicitud de conexión genera un error.

> [!NOTE]
> Para obtener acceso a Azure SQL Database desde el equipo local, asegúrese de que el firewall de su red y el equipo local permite la comunicación saliente en el puerto TCP 1433.
> 

### <a name="connecting-from-azure"></a>Conexión desde Azure
Para permitir que las aplicaciones de Azure se conecten al servidor SQL de Azure, deben habilitarse las conexiones de Azure. Cuando una aplicación desde Azure intenta conectarse a su servidor de s de datos, el firewall comprueba que se permiten las conexiones de Azure. Una configuración del firewall con dirección inicial y final igual a 0.0.0.0 indica que se permiten estas conexiones. Si no se permite el intento de conexión, la solicitud no alcanza el servidor de Azure SQL Database.

> [!IMPORTANT]
> Esta opción configura el firewall para permitir todas las conexiones de Azure, incluidas las de las suscripciones de otros clientes. Al seleccionar esta opción, asegúrese de que los permisos de usuario y el inicio de sesión limiten el acceso solamente a los usuarios autorizados.
> 

## <a name="creating-and-managing-firewall-rules"></a>Creación y administración de reglas de firewall
La primera configuración del firewall de nivel de servidor puede crearse mediante [Azure Portal](https://portal.azure.com/) o mediante programación con [Azure PowerShell](https://docs.microsoft.com/powershell/module/azurerm.sql), la [CLI de Azure](/cli/azure/sql/server/firewall-rule#az_sql_server_firewall_rule_create) o la [API de REST](https://docs.microsoft.com/rest/api/sql/firewallrules). Las reglas de firewall de nivel de servidor posteriores se pueden crear y administrar mediante estos métodos, y mediante Transact-SQL. 

> [!IMPORTANT]
> Las reglas de firewall de nivel de base de datos solo pueden crearse y administrarse mediante Transact-SQL. 
>

Para mejorar el rendimiento, las reglas de firewall de nivel de servidor se almacenan temporalmente en caché en el nivel de base de datos. Para actualizar la memoria caché, consulte [DBCC FLUSHAUTHCACHE](https://msdn.microsoft.com/library/mt627793.aspx). 

> [!TIP]
> Puede usar [Auditoría de SQL Database](sql-database-auditing.md) para auditar cambios en el firewall a nivel de servidor y de base de datos.
>

## <a name="manage-firewall-rules-using-the-azure-portal"></a>Administración de reglas de firewall mediante Azure Portal

Para establecer una regla de firewall de nivel de servidor en Azure Portal, puede ir a la página de información general de Azure SQL Database o a la página de información general del servidor lógico de la base de datos de Azure.

> [!TIP]
> Para consultar un tutorial, vea [Creación de una instancia de Azure SQL Database en Azure Portal](sql-database-get-started-portal.md).
>

**Desde la página de información general de la base de datos**

1. Para establecer una regla de firewall de nivel de servidor desde la página de información general de la base de datos, haga clic en **Establecer el firewall del servidor** en la barra de herramientas, como se muestra en la siguiente imagen: se abre la página **Configuración del firewall** del servidor de SQL Database.

      ![regla de firewall del servidor](./media/sql-database-get-started-portal/server-firewall-rule.png) 

2. Haga clic en **Agregar IP de cliente** en la barra de herramientas para agregar la dirección IP del equipo que está usando actualmente y, luego, haga clic en **Guardar**. Se creará una regla de firewall de nivel de servidor para la dirección IP actual.

      ![establecer regla de firewall del servidor](./media/sql-database-get-started-portal/server-firewall-rule-set.png) 

**Desde la página de información general del servidor**

Se abre la página de información general del servidor, que muestra el nombre completo del servidor (por ejemplo, **mynewserver20170403.database.windows.net**) y proporciona opciones para otras configuraciones.

1. Para establecer una regla de nivel de servidor desde la página de información general del servidor, haga clic en **Firewall** en el menú izquierdo en Configuración: 

2. Haga clic en **Agregar IP de cliente** en la barra de herramientas para agregar la dirección IP del equipo que está usando actualmente y, luego, haga clic en **Guardar**. Se creará una regla de firewall de nivel de servidor para la dirección IP actual.

## <a name="manage-firewall-rules-using-transact-sql"></a>Administración de reglas de firewall mediante Transact-SQL
| Vista de catálogo o un procedimiento almacenado | Nivel | DESCRIPCIÓN |
| --- | --- | --- |
| [sys.firewall_rules](https://msdn.microsoft.com/library/dn269980.aspx) |Server |Muestra las reglas de firewall de nivel de servidor actuales |
| [sp_set_firewall_rule](https://msdn.microsoft.com/library/dn270017.aspx) |Server |Crea o actualiza las reglas de firewall de nivel de servidor |
| [sp_delete_firewall_rule](https://msdn.microsoft.com/library/dn270024.aspx) |Server |Elimina las reglas de firewall de nivel de servidor |
| [sys.database_firewall_rules](https://msdn.microsoft.com/library/dn269982.aspx) |Base de datos |Muestra las reglas de firewall de nivel de base de datos actuales |
| [sp_set_database_firewall_rule](https://msdn.microsoft.com/library/dn270010.aspx) |Base de datos |Crea o actualiza las reglas de firewall de nivel de base de datos |
| [sp_delete_database_firewall_rule](https://msdn.microsoft.com/library/dn270030.aspx) |Bases de datos |Elimina las reglas de firewall de nivel de base de datos |


En los ejemplos siguientes se revisan las reglas existentes, se habilita un intervalo de direcciones IP en el servidor Contoso y se elimina una regla de firewall:
   
```sql
SELECT * FROM sys.firewall_rules ORDER BY name;
```
  
A continuación, agregue una regla de firewall.
   
```sql
EXECUTE sp_set_firewall_rule @name = N'ContosoFirewallRule',
   @start_ip_address = '192.168.1.1', @end_ip_address = '192.168.1.10'
```

Para eliminar una regla de firewall de nivel de servidor, ejecute el procedimiento almacenado sp_delete_firewall_rule. En el ejemplo siguiente se elimina la regla llamada ContosoFirewallRule:
   
```sql
EXECUTE sp_delete_firewall_rule @name = N'ContosoFirewallRule'
```   

## <a name="manage-firewall-rules-using-azure-powershell"></a>Administración de reglas de firewall mediante Azure PowerShell
| Cmdlet | Nivel | DESCRIPCIÓN |
| --- | --- | --- |
| [Get-AzureRmSqlServerFirewallRule](/powershell/module/azurerm.sql/get-azurermsqlserverfirewallrule) |Server |Devuelve las reglas de firewall de nivel de servidor actuales |
| [New-AzureRmSqlServerFirewallRule](/powershell/module/azurerm.sql/new-azurermsqlserverfirewallrule) |Server |Crear una regla de firewall de nivel de servidor |
| [Set-AzureRmSqlServerFirewallRule](/powershell/module/azurerm.sql/set-azurermsqlserverfirewallrule) |Server |Actualiza las propiedades de una regla de firewall de nivel de servidor existente |
| [Remove-AzureRmSqlServerFirewallRule](/powershell/module/azurerm.sql/remove-azurermsqlserverfirewallrule) |Server |Elimina las reglas de firewall de nivel de servidor |


En el ejemplo siguiente se establece una regla de firewall de nivel de servidor mediante PowerShell:

```powershell
New-AzureRmSqlServerFirewallRule -ResourceGroupName "myResourceGroup" `
    -ServerName $servername `
    -FirewallRuleName "AllowSome" -StartIpAddress "0.0.0.0" -EndIpAddress "0.0.0.0"
```

> [!TIP]
> Para consultar ejemplos de PowerShell en el contexto de un inicio rápido, vea [Creación de una base de datos con PowerShell](sql-database-get-started-powershell.md) y [Creación de una instancia única de SQL Database y configuración de una regla de firewall mediante Powershell](scripts/sql-database-create-and-configure-database-powershell.md).
>

## <a name="manage-firewall-rules-using-azure-cli"></a>Administración de reglas de firewall mediante la CLI de Azure
| Cmdlet | Nivel | DESCRIPCIÓN |
| --- | --- | --- |
|[az sql server firewall-rule create](/cli/azure/sql/server/firewall-rule#az_sql_server_firewall_rule_create)|Server|Crea una regla de firewall del servidor.|
|[az sql server firewall-rule list](/cli/azure/sql/server/firewall-rule#az_sql_server_firewall_rule_list)|Server|Enumera las reglas de firewall en un servidor.|
|[az sql server firewall-rule show](/cli/azure/sql/server/firewall-rule#az_sql_server_firewall_rule_show)|Server|Muestra los detalles de una regla de firewall.|
|[az sql server firewall-rule update](/cli/azure/sql/server/firewall-rule##az_sql_server_firewall_rule_update)|Server|Actualiza una regla de firewall.|
|[az sql server firewall-rule delete](/cli/azure/sql/server/firewall-rule#az_sql_server_firewall_rule_delete)|Server|Elimina una regla de firewall.|

En el ejemplo siguiente se establece una regla de firewall de nivel de servidor mediante la CLI de Azure: 

```azurecli-interactive
az sql server firewall-rule create --resource-group myResourceGroup --server $servername \
    -n AllowYourIp --start-ip-address 0.0.0.0 --end-ip-address 0.0.0.0
```

> [!TIP]
> Para consultar un ejemplo de la CLI de Azure en el contexto de un inicio rápido, vea [Creación de una base de datos con la CLI de Azure](sql-database-get-started-cli.md) y [Cree una base de datos SQL única y configure una regla de firewall mediante la CLI de Azure](scripts/sql-database-create-and-configure-database-cli.md).
>

## <a name="manage-firewall-rules-using-rest-api"></a>Administración de reglas de firewall mediante la API de REST
| API | Nivel | DESCRIPCIÓN |
| --- | --- | --- |
| [Enumerar reglas de firewall](https://docs.microsoft.com/rest/api/sql/FirewallRules/ListByServer) |Server |Muestra las reglas de firewall de nivel de servidor actuales |
| [Crear o actualizar regla de firewall](https://docs.microsoft.com/rest/api/sql/FirewallRules/CreateOrUpdate) |Server |Crea o actualiza las reglas de firewall de nivel de servidor |
| [Eliminar regla de firewall](https://docs.microsoft.com/rest/api/sql/FirewallRules/Delete) |Server |Elimina las reglas de firewall de nivel de servidor |

## <a name="server-level-firewall-rule-versus-a-database-level-firewall-rule"></a>Regla de firewall de nivel de servidor frente a una regla de firewall de nivel de base de datos
P: ¿Los usuarios de una base de datos deben estar completamente aislados de otra base de datos?   
  Si es así, conceda acceso usando reglas de firewall de nivel de base de datos. Esto evita usar reglas de firewall de nivel de servidor, que permiten el acceso a través del firewall a todas las bases de datos, lo que reduce la solidez de sus defensas.   
 
P: ¿Los usuarios de las direcciones IP requieren acceso a todas las bases de datos?   
  Utilice reglas de firewall de nivel de servidor para reducir el número de veces que se deben configurar las reglas de firewall.   

P: ¿La persona o el equipo que configura las reglas de firewall solo tiene acceso mediante la API de REST, PowerShell o Azure Portal?   
  Debe usar reglas de firewall de nivel de servidor. Las reglas de firewall de nivel de base de datos solo pueden configurarse mediante Transact-SQL.  

P: ¿Se prohíbe que la persona o el equipo que configura las reglas de firewall tenga permiso de alto nivel en el nivel de base de datos?   
  Use reglas de firewall de nivel de servidor. La configuración de reglas de firewall de nivel de base de datos mediante Transact-SQL requiere, al menos, permiso `CONTROL DATABASE` en el nivel de base de datos.  

P: ¿La persona o el equipo que configura o audita las reglas de firewall administra de forma centralizada las reglas de firewall para muchas bases de datos (quizás 100)?   
  Esta decisión depende de sus necesidades y del entorno. Las reglas de firewall de nivel de servidor pueden ser más fáciles de configurar, pero con scripting se pueden configurar reglas en el nivel de base de datos. Incluso si usa las reglas de firewall de nivel de servidor, podría necesitar auditar las reglas de firewall de base de datos para ver si los usuarios con permiso `CONTROL` en la base de datos han creado reglas de firewall de nivel de base de datos.   

P: ¿Puedo usar una combinación de reglas de firewall de nivel de servidor y de base de datos?   
  Sí. Algunos usuarios, por ejemplo, los administradores, pueden necesitar reglas de firewall de nivel de servidor. Otros usuarios, como los usuarios de una aplicación de base de datos, pueden necesitar reglas de firewall de nivel de base de datos.   

## <a name="troubleshooting-the-database-firewall"></a>Solución de problemas del firewall de la base de datos
Tenga en cuenta los siguientes puntos cuando el acceso al servicio de Microsoft Azure SQL Database no se comporte de la manera prevista:

* **Configuración del firewall local:** antes de que el equipo pueda tener acceso a Azure SQL Database, puede que necesite crear una excepción de firewall en el equipo para el puerto TCP 1433. Si está realizando conexiones dentro del límite de la nube de Azure, es posible que tenga que abrir puertos adicionales. Para más información, vea la sección **SQL Database: fuera frente a dentro**: del artículo [Puertos más allá de 1433 para ADO.NET 4.5 y SQL Database](sql-database-develop-direct-route-ports-adonet-v12.md).
* **Traducción de direcciones de red (NAT):** debido a la NAT, la dirección IP usada por su equipo para conectarse a Azure SQL Database puede diferir de la que se muestra en los valores de la configuración de IP del equipo. A fin de ver la dirección IP usada por su equipo para conectarse a Azure, inicie sesión en el portal y vaya a la pestaña **Configurar** del servidor que hospede su base de datos. En la sección **Direcciones IP permitidas**, verá la sección **Dirección IP del cliente actual**. Haga clic en **Agregar** en la opción **Direcciones IP permitidas** para permitir que este equipo tenga acceso al servidor.
* **Los cambios realizados en la lista de permitidos no han surtido efecto todavía:** puede haber un retraso de hasta cinco minutos hasta que los cambios en la configuración del firewall de Azure SQL Database surtan efecto.
* **El inicio de sesión no está autorizado o se ha usado una contraseña incorrecta:** si un inicio de sesión no tiene permisos en el servidor de Azure SQL Database o la contraseña usada es incorrecta, se denegará la conexión al servidor de Azure SQL Database. La creación de una configuración de firewall solo ofrece a los clientes una oportunidad de intentar conectarse al servidor; cada cliente debe ofrecer las credenciales de seguridad necesarias. Para obtener más información sobre la preparación de inicios de sesión, vea Administración de bases de datos, inicios de sesión y usuarios en Azure SQL Database.
* **Dirección IP dinámica:** si tiene una conexión a Internet con una direccionamiento IP dinámica y tiene problemas al acceder al firewall, podría intentar una de las siguientes soluciones:
  
  * Pida a su proveedor de acceso a Internet (ISP) el intervalo de direcciones IP asignado a los equipos cliente que acceden al servidor de Azure SQL Database y agréguelo como regla de firewall.
  * Obtenga el direccionamiento IP estático en su lugar para los equipos cliente y luego agregue las direcciones IP como reglas de firewall.

## <a name="next-steps"></a>pasos siguientes

- Para consultar un inicio rápido sobre cómo crear una base de datos y una regla de firewall de nivel de servidor, vea [Creación de una instancia de Azure SQL Database](sql-database-get-started-portal.md).
- Si desea obtener ayuda para conectarse a Azure SQL Database desde aplicaciones de código abierto o de terceros, consulte [Ejemplos de código de inicio rápido de cliente para SQL Database](https://msdn.microsoft.com/library/azure/ee336282.aspx).
- Para obtener información sobre los puertos adicionales que puede necesitar abrir, vea la sección **SQL Database: fuera frente a dentro**: del artículo [Puertos más allá de 1433 para ADO.NET 4.5 y SQL Database](sql-database-develop-direct-route-ports-adonet-v12.md)
- Para obtener información general sobre la seguridad de Azure SQL Database, vea [Protección de bases de datos SQL](sql-database-security-overview.md).

<!--Image references-->
[1]: ./media/sql-database-firewall-configure/sqldb-firewall-1.png
