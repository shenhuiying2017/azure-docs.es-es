<properties
   pageTitle="Configuración del firewall en servidores SQL (información general) | Microsoft Azure"
   description="Obtenga información acerca de cómo configurar un firewall de base de datos SQL mediante las reglas de firewall de nivel de servidor y de nivel de base de datos, para administrar el acceso."
   keywords="firewall de base de datos"
   services="sql-database"
   documentationCenter=""
   authors="BYHAM"
   manager="jhubbard"
   editor="cgronlun"
   tags=""/>

<tags
   ms.service="sql-database"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="data-management"
   ms.date="09/14/2016"
   ms.author="rickbyh"/>

# Configuración de reglas de firewall de Base de datos SQL de Azure: información general


> [AZURE.SELECTOR]
- [Información general](sql-database-firewall-configure.md)
- [Portal de Azure](sql-database-configure-firewall-settings.md)
- [TSQL](sql-database-configure-firewall-settings-tsql.md)
- [PowerShell](sql-database-configure-firewall-settings-powershell.md)
- [API DE REST](sql-database-configure-firewall-settings-rest.md)


Base de datos SQL de Microsoft Azure ofrece un servicio de base de datos relacional para Azure y otras aplicaciones basadas en Internet. Para ayudar a proteger los datos, los firewalls impiden todo acceso al servidor de bases de datos, excepto a aquellos equipos a los que haya concedido permiso. Asimismo, otorgan acceso a las bases de datos según la dirección IP de origen de cada solicitud.

Para configurar su firewall, cree reglas de firewall que especifiquen intervalos de direcciones IP aceptables. Puede crear reglas de firewall en los niveles de servidor y base de datos.

- **Reglas de firewall de nivel de servidor:** estas reglas permiten a los clientes acceder a todo el servidor SQL de Azure; es decir, a todas las bases de datos que se encuentren en el mismo servidor lógico. Estas reglas se almacenan en la base de datos **maestra**. Las reglas de firewall de nivel de servidor pueden configurarse a través del Portal o mediante instrucciones Transact-SQL.
- **Reglas de firewall de nivel de base de datos:** estas reglas permiten a los clientes tener acceso a las bases de datos individuales que se encuentran dentro del servidor de la Base de datos SQL de Azure. Puede crear estas reglas para cada base de datos y se almacenan en las bases de datos individuales. (Puede crear reglas de firewall de nivel de base de datos para la base de datos **maestra**). Estas reglas pueden resultar de utilidad al restringir el acceso a determinadas bases de datos (seguras) dentro del mismo servidor lógico. Las reglas de firewall de nivel de base de datos solo pueden configurarse mediante instrucciones Transact-SQL.

**Recomendación**: Microsoft recomienda usar reglas de firewall de nivel de base de datos siempre que sea posible con el fin de mejorar la seguridad y hacer que la base de datos sea más portátil. Utilice reglas de firewall de nivel de servidor para administradores y cuando tenga muchas bases de datos con los mismos requisitos de acceso y no quiera dedicar tiempo a configurar individualmente cada una de ellas.


## Información general de firewalls

Inicialmente, todo el acceso mediante instrucciones Transact-SQL al servidor SQL de Azure está bloqueado por el firewall. Para comenzar a usar el servidor SQL de Azure, debe ir a Azure Portal y especificar una o varias reglas de firewall de nivel de servidor que permitan el acceso al servidor SQL de Azure. Use las reglas de firewall para especificar qué intervalos de direcciones IP de Internet se permiten y si las aplicaciones de Azure pueden tratar de conectarse al servidor SQL de Azure.

Para conceder selectivamente el acceso a solo una de las bases de datos en el servidor SQL de Azure, debe crear una regla de nivel de base de datos para la base de datos necesaria. Especifique un intervalo de direcciones IP para la regla de firewall de base de datos que quede fuera del intervalo de direcciones IP especificado en la regla de firewall de nivel de servidor. Además, asegúrese de que la dirección IP del cliente se encuentre en el intervalo especificado en la regla de nivel de base de datos.

Los intentos de conexión desde Internet y Azure deben atravesar primero el firewall antes de poder alcanzar Base de datos SQL y el servidor SQL de Azure, tal y como se muestra en el siguiente diagrama.

   ![Diagrama donde se describe la configuración del firewall.][1]

## Conexión desde Internet

Cuando un equipo intenta conectarse al servidor de bases de datos desde Internet, primero el firewall coteja la dirección IP de origen de la solicitud con el conjunto completo de reglas de firewall:

- Si la dirección IP de la solicitud está comprendida en uno de los intervalos especificados en las reglas de firewall de nivel de servidor, la conexión se concede al servidor de Base de datos SQL de Azure.

- Si la dirección IP de la solicitud no está comprendida en uno de los intervalos especificados en las regla de firewall de nivel de servidor, se comprueban las reglas de firewall de nivel de base de datos. Si la dirección IP de la solicitud está comprendida en uno de los intervalos especificados en las reglas de firewall de nivel de servidor, la conexión solo se concede a la base de datos que tiene una regla de nivel de base de datos coincidente.

- Si la dirección IP de la solicitud no se encuentra dentro de los intervalos especificados en cualquiera de las reglas de firewall de nivel de servidor o de base de datos, la solicitud de la conexión genera error.

> [AZURE.NOTE] Para obtener acceso a la Base de datos SQL de Azure desde el equipo local, asegúrese de que el firewall de su red y el equipo local permite la comunicación saliente en el puerto TCP 1433.


## Conexión desde Azure

Cuando una aplicación desde Azure intenta conectarse a su servidor de s de datos, el firewall comprueba que se permiten las conexiones de Azure. Una configuración del firewall con dirección inicial y final igual a 0.0.0.0 indica que se permiten estas conexiones. Si no se permite el intento de conexión, la solicitud no alcanza el servidor de Base de datos SQL de Azure.

> [AZURE.IMPORTANT] Esta opción configura el firewall para permitir todas las conexiones de Azure, incluidas las de las suscripciones de otros clientes. Al seleccionar esta opción, asegúrese de que los permisos de usuario y el inicio de sesión limiten el acceso solamente a los usuarios autorizados.

Puede habilitar conexiones desde Azure de dos maneras:

- En [Microsoft Azure Portal](https://portal.azure.com/), active la casilla **Permitir que los servicios de Azure accedan al servidor** al crear un servidor.

- En el [Portal clásico](http://go.microsoft.com/fwlink/p/?LinkID=161793), en la pestaña **Configurar** de un servidor, en la sección **Servicios permitidos**, haga clic en **Sí** para **Servicios de Microsoft Azure**.

## Crear de la primera regla de firewall de nivel de servidor

La primera configuración del firewall de nivel de servidor puede crearse mediante [Azure Portal](https://portal.azure.com/) o mediante programación con la API de REST o con Azure PowerShell. Las reglas de firewall de nivel de servidor posteriores se pueden crear y administrar mediante estos métodos, así como a través de Transact-SQL. Para mejorar el rendimiento, las reglas de firewall de nivel de servidor se almacenan temporalmente en caché en el nivel de base de datos. Para actualizar la memoria caché, consulte [DBCC FLUSHAUTHCACHE](https://msdn.microsoft.com/library/mt627793.aspx). Para más información sobre reglas de firewall de nivel de servidor, consulte [Configuración de un firewall de servidor SQL de Azure mediante Azure Portal](sql-database-configure-firewall-settings.md).

## Reglas de firewall de nivel de base de datos

Después de haber configurado el primer firewall de nivel de servidor, puede que desee restringir el acceso a determinadas bases de datos. Si especifica un intervalo de direcciones IP en la regla de firewall de nivel de base de datos que se encuentra fuera del intervalo especificado en la regla de firewall de nivel de servidor, solo los clientes que tengan direcciones IP en el intervalo de nivel de base de datos pueden tener acceso a la base de datos. Puede tener un máximo de 128 reglas de firewall de nivel de base de datos para una base de datos. Se pueden crear reglas de firewall de nivel de base de datos para las bases de datos de usuario y maestras y administrarse a través de Transact-SQL. Para más información sobre cómo configurar las reglas de firewall de nivel de base de datos, consulte [sp\_set\_database\_firewall\_rule (bases de datos de SQL Azure)](https://msdn.microsoft.com/library/dn270010.aspx).

## Administración mediante programación de reglas de firewall

Además de Azure Portal, las reglas de firewall se pueden administrar mediante programación con Transact-SQL, la API de REST y Azure PowerShell. En las tablas siguientes se describe el conjunto de comandos disponibles para cada método.


### Transact-SQL

| Vista de catálogo o un procedimiento almacenado | Level | Description |
|--------------------------------------------------------------------------------------------|-----------|------------------------------------------------------|
| [sys.firewall\_rules](https://msdn.microsoft.com/library/dn269980.aspx) | Server | Muestra las reglas de firewall de nivel de servidor actuales |
| [sp\_set\_firewall\_rule](https://msdn.microsoft.com/library/dn270017.aspx) | Server | Crea o actualiza las reglas de firewall de nivel de servidor |
| [sp\_delete\_firewall\_rule](https://msdn.microsoft.com/library/dn270024.aspx) | Server | Elimina las reglas de firewall de nivel de servidor |
| [sys.database\_firewall\_rules](https://msdn.microsoft.com/library/dn269982.aspx) | Base de datos | Muestra las reglas de firewall de nivel de base de datos actuales |
| [sp\_set\_database\_firewall\_rule](https://msdn.microsoft.com/library/dn270010.aspx) | Base de datos | Crea o actualiza las reglas de firewall de nivel de base de datos |
| [sp\_delete\_database\_firewall\_rule](https://msdn.microsoft.com/library/dn270030.aspx) | Bases de datos | Elimina las reglas de firewall de nivel de base de datos |

### API de REST

| API | Level | Description |
|----------------------|--------|------------------------------------------------------------------|
| [Enumerar reglas de firewall](https://msdn.microsoft.com/library/azure/dn505715.aspx) | Server | Muestra las reglas de firewall de nivel de servidor actuales |
| [Crear regla de firewall](https://msdn.microsoft.com/library/azure/dn505712.aspx) | Server | Crea o actualiza las reglas de firewall de nivel de servidor |
| [Establecer regla de firewall](https://msdn.microsoft.com/library/azure/dn505707.aspx) | Server | Actualiza las propiedades de una regla de firewall de nivel de servidor existente |
| [Eliminar regla de firewall](https://msdn.microsoft.com/library/azure/dn505706.aspx) | Server | Elimina las reglas de firewall de nivel de servidor |


### Azure PowerShell

| Cmdlet | Level | Description |
|-----------------------------------------------------------------------------------------------------|--------|------------------------------------------------------------------|
| [Get-AzureSqlDatabaseServerFirewallRule](https://msdn.microsoft.com/library/azure/dn546731.aspx) | Server | Devuelve las reglas de firewall de nivel de servidor actuales |
| [New-AzureSqlDatabaseServerFirewallRule](https://msdn.microsoft.com/library/azure/dn546724.aspx) | Server | Crear una regla de firewall de nivel de servidor |
| [Set-AzureSqlDatabaseServerFirewallRule](https://msdn.microsoft.com/library/azure/dn546739.aspx) | Server | Actualiza las propiedades de una regla de firewall de nivel de servidor existente |
| [Remove-AzureSqlDatabaseServerFirewallRule](https://msdn.microsoft.com/library/azure/dn546727.aspx) | Server | Elimina las reglas de firewall de nivel de servidor |

> [AZURE.NOTE] los cambios de la configuración del firewall pueden tardar hasta cinco minutos en aplicarse.

## Solución de problemas del firewall de la base de datos

Tenga en cuenta los siguientes puntos cuando el acceso al servicio de Base de datos SQL de Microsoft Azure no se comporte de la manera prevista:

- **Configuración del firewall local:** antes de que el equipo pueda tener acceso a la Base de datos SQL de Azure, puede que necesite crear una excepción de firewall en el equipo para el puerto TCP 1433. Si está realizando conexiones dentro del límite de la nube de Azure, es posible que tenga que abrir puertos adicionales. Para obtener más información, consulte la sección **Versión V12 de la Base de datos SQL: fuera frente a dentro** del artículo [Puertos más allá de 1433 para ADO.NET 4.5 y para la Base de datos SQL V12](sql-database-develop-direct-route-ports-adonet-v12.md).

- **Traducción de direcciones de red (NAT):** debido a la NAT, la dirección IP usada por su equipo para conectarse a Azure SQL Database puede diferir de la que se muestra en los valores de la configuración de IP del equipo. A fin de ver la dirección IP usada por su equipo para conectarse a Azure, inicie sesión en el portal y vaya a la pestaña **Configurar** del servidor que hospede su base de datos. En la sección **Direcciones IP permitidas**, verá la sección **Dirección IP del cliente actual**. Haga clic en **Agregar** en la opción **Direcciones IP permitidas** para permitir que este equipo tenga acceso al servidor.

- **Los cambios realizados en la lista de permitidos no han surtido efecto todavía:** puede haber un retraso de hasta cinco minutos hasta que los cambios en la configuración del firewall de Azure SQL Database surtan efecto.

- **El inicio de sesión no está autorizado o se ha usado una contraseña incorrecta:** si un inicio de sesión no tiene permisos en el servidor de Azure SQL Database o la contraseña usada es incorrecta, se denegará la conexión al servidor de Azure SQL Database. La creación de una configuración de firewall solo ofrece a los clientes una oportunidad de intentar conectarse al servidor; cada cliente debe ofrecer las credenciales de seguridad necesarias. Para obtener más información sobre la preparación de inicios de sesión, vea Administración de bases de datos, inicios de sesión y usuarios en la Base de datos SQL de Azure.

- **Dirección IP dinámica:** si tiene una conexión a Internet con una direccionamiento IP dinámica y tiene problemas al acceder al firewall, podría intentar una de las siguientes soluciones:

 - Pida a su proveedor de acceso a Internet (ISP) el intervalo de direcciones IP asignado a los equipos cliente que acceden al servidor de Azure SQL Database y agréguelo como regla de firewall.

 - Obtenga el direccionamiento IP estático en su lugar para los equipos cliente y luego agregue las direcciones IP como reglas de firewall.

## Pasos siguientes

Para leer artículos sobre cómo crear reglas de firewall de nivel de servidor y base de datos, visite:

- [Configuración de reglas de firewall de nivel de servidor en Base de datos SQL de Azure mediante el Portal de Azure](sql-database-configure-firewall-settings.md)
- [Configuración de reglas de firewall de nivel de servidor en Base de datos SQL de Azure mediante T-SQL](sql-database-configure-firewall-settings-tsql.md)
- [Configuración de reglas de firewall de nivel de servidor en Base de datos SQL de Azure mediante PowerShell](sql-database-configure-firewall-settings-powershell.md)
- [Configuración de reglas de firewall de nivel de servidor en Base de datos SQL de Azure mediante la API de REST](sql-database-configure-firewall-settings-rest.md)

Para ver un tutorial sobre cómo crear una base de datos, consulte [Tutorial de Base de datos SQL: creación de una Base de datos SQL en cuestión de minutos con datos de ejemplo y el Portal de Azure](sql-database-get-started.md). Si desea obtener ayuda para conectarse a una base de datos SQL de Azure desde aplicaciones de código abierto o de terceros, consulte [Ejemplos de código de inicio rápido de cliente para Base de datos SQL](https://msdn.microsoft.com/library/azure/ee336282.aspx). Para saber cómo obtener acceso a las bases de datos, consulte [Manage database access and login security](https://msdn.microsoft.com/library/azure/ee336235.aspx) (Administrar la seguridad del inicio de sesión y el acceso a la base de datos).



## Recursos adicionales

- [Protección de bases de datos](sql-database-security.md)
- [Centro de seguridad para el Motor de base de datos de SQL Server y Base de datos SQL Azure](https://msdn.microsoft.com/library/bb510589)

<!--Image references-->
[1]: ./media/sql-database-firewall-configure/sqldb-firewall-1.png

<!---HONumber=AcomDC_0914_2016-->