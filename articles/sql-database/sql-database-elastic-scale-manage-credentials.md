<properties 
	pageTitle="Administración de credenciales de Escalado elástico" 
	description="Cómo configurar el nivel correcto de credenciales, de administrador a solo lectura, para las aplicaciones de escala elástica." 
	services="sql-database" 
	documentationCenter="" 
	manager="stuartozer" 
	authors="stuartozer" 
	editor=""/>

<tags 
	ms.service="sql-database" 
	ms.workload="sql-database" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="02/16/2015" 
	ms.author="stuartozer@microsoft.com"/>

#Administración de credenciales de Escalado elástico

Las [API de cliente de Escalado elástico](http://go.microsoft.com/?linkid=9862605) usan credenciales para distintos tipos de operaciones, en concreto, para la creación o manipulación de un [Administrador de mapas de particiones](http://go.microsoft.com/?linkid=9862595), haciendo referencia a un Administrador de mapas de particiones existente para obtener información sobre particiones, y conectándose a ellas. A continuación se analizan las credenciales para estos tipos de operaciones. 


* **Credenciales de administración para el acceso al mapa de particiones**: las credenciales de administración se usan cuando se crea una instancia de un objeto **ShardMapManager** para las aplicaciones que manipulan los mapas de particiones. El usuario de las API de Escalado elástico deben crear los usuarios e inicios de sesión de SQL necesarios y deben asegurarse de que tienen permisos de lectura/escritura sobre la base de datos de mapa de particiones global y también de todas las bases de datos de particiones. Estas credenciales se usan para mantener el mapa de particiones global y los mapas de particiones locales cuando se realizan cambios en el mapa de particiones. Por ejemplo, use las credenciales de administración para crear una instancia del objeto de administrador de mapas de particiones, tal como muestra el siguiente código: 

        // Obtain a shard map manager. 
        ShardMapManager shardMapManager = ShardMapManagerFactory.GetSqlShardMapManager( 
                smmAdminConnectionString, 
                ShardMapManagerLoadPolicy.Lazy 
        ); 


     La variable **smmAdminConnectionString** es una cadena de conexión que contiene las credenciales de administración. El identificador de usuario y la contraseña proporcionan acceso de lectura y escritura a la base de datos de mapa de particiones y a particiones individuales. La cadena de conexión de administración también incluye el nombre del servidor y el nombre de la base de datos para identificar la base de datos de mapa de particiones global. Esta es una cadena de conexión típica para ese fin:

        "Server=<yourserver>.database.windows.net;Database=<yourdatabase>;User ID=<yourmgmtusername>;Password=<yourmgmtpassword>;Trusted_Connection=False;Encrypt=True;Connection Timeout=30;" 

     No utilice valores de Id. de usuario con el formato "username@server" -- sino que simplemente utilice "username".  El motivo es que las credenciales deben funcionar con la base de datos del Administrador de mapas de particiones y con particiones individuales, que pueden estar en distintos servidores.
     
* **Credenciales de usuario para acceso al Administrador de mapas de particiones**:  al crear instancias en el Administrador de mapa de particiones en una aplicación que no va a administrar mapas de particiones, use las credenciales que tienen permisos de solo lectura en el mapa de particiones global. La información recuperada del mapa de particiones global con estas credenciales se usan en el [enrutamiento dependiente de los datos](sql-database-elastic-scale-data-dependent-routing.md) y para rellenar la caché del mapa de particiones en el cliente. Las credenciales se proporcionan a través del mismo patrón de llamada a **GetSqlShardMapManager**, como se ha mostrado anteriormente: 
 
        // Obtain shard map manager. 
        ShardMapManager shardMapManager = ShardMapManagerFactory.GetSqlShardMapManager( 
                smmReadOnlyConnectionString, 
                ShardMapManagerLoadPolicy.Lazy
        );  

     Observe el uso de **smmReadOnlyConnectionString** para reflejar la utilización de diferentes credenciales para este acceso en nombre de usuarios **no administradores**; estas credenciales no deben proporcionar permiso de escritura para el mapa de particiones global. 

* **Credenciales de usuario para el acceso a los datos de usuario**: Se necesitarán credenciales adicionales cuando se utilice el método **OpenConnectionForKey** para acceder a una partición asociada con una clave. Estas credenciales deben proporcionar los permisos de acceso de solo lectura a las tablas de mapa de particiones local que residen en la partición. Esto es necesario para realizar la validación de la conexión para el enrutamiento dependiente de los datos en la partición. El siguiente fragmento de código muestra el uso de las credenciales de usuario para el acceso a los datos del usuario en el contexto del enrutamiento dependiente de los datos: 
 
        using (SqlConnection conn = rangeMap.OpenConnectionForKey<int>( 
        targetWarehouse, smmUserConnectionString, ConnectionOptions.Validate)) 

    En este ejemplo, **smmUserConnectionString** contiene la cadena de conexión para las credenciales de usuario. En el caso de Base de datos SQL de Azure, la siguiente es una cadena de conexión típica para las credenciales de usuario: 

        "User ID=<yourusername>; Password=<youruserpassword>; Trusted_Connection=False; Encrypt=True; Connection Timeout=30;"  

    Al igual que con las credenciales de administración, no use valores de Id. de usuario con el formato "username@server" -- sino que utilice simplemente "username".  Tenga en cuenta también que la cadena de conexión no contiene un nombre de servidor y de base de datos. Esto se debe a que la llamada **OpenConnectionForKey** dirigirá automáticamente la conexión a la partición correcta según la clave. Por lo tanto, no se debe proporcionar un nombre de base de datos ni de servidor. 

[AZURE.INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]

<!--HONumber=47-->
 