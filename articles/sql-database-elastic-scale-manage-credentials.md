<properties title="Managing Elastic Scale Credentials" pageTitle="Administración de credenciales de Escalado elástico" description="Cómo configurar el nivel correcto de credenciales, de administrador a solo lectura, para las aplicaciones de escala elástica." metaKeywords="Azure SQL Database, elastic scale, about user credentials in elastic scale" services="sql-database" documentationCenter="" manager="jhubbard" authors="sidneyh@microsoft.com"/>

<tags ms.service="sql-database" ms.workload="sql-database" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="10/02/2014" ms.author="sidneyh" />

#Administración de credenciales de Escalado elástico  

Las [API de cliente de Escalado elástico](http://go.microsoft.com/?linkid=9862605) usan credenciales para los distintos tipos de operaciones, en especial para la creación o manipulación de un [Administrador de mapa de particiones](http://go.microsoft.com/?linkid=9862595), mediante referencia a un Administración de mapa de particiones existentes para obtener información acerca de particiones y conectándose a las particiones. A continuación se analizan las credenciales para estos tipos de operaciones. 


* **Credenciales de administración para el acceso de mapa de particiones**: las credenciales de administración se usan cuando se crea una instancia de un objeto **ShardMapManager** para las aplicaciones que manipulan los mapas de particiones. El usuario de las API de Escalado elástico deben crear los usuarios e inicios de sesión de SQL necesarios y deben asegurarse de que tienen permisos de lectura/escritura sobre la base de datos de mapa de particiones global y también de todas las bases de datos de particiones. Estas credenciales se usan para mantener el mapa de particiones global y los mapas de particiones locales cuando se realizan cambios en el mapa de particiones. Por ejemplo, use las credenciales de administración para crear una instancia del objeto de administrador de mapas de particiones, tal como muestra el siguiente código: 

        // Obtain a shard map manager. 
        ShardMapManager shardMapManager = ShardMapManagerFactory.GetSqlShardMapManager( 
                smmAdminConnectionString, 
                ShardMapManagerLoadPolicy.Lazy 
        ); 


     La variable **smmAdminConnectionString** es una cadena de conexión que contiene las credenciales de administración. El identificador de usuario y la contraseña proporcionan acceso de lectura y escritura a la base de datos de mapa de particiones y a particiones individuales. La cadena de conexión de administración también incluye el nombre del servidor y el nombre de la base de datos para identificar la base de datos de mapa de particiones global. Esta es una cadena de conexión típica para ese fin:

        "Server=<yourserver>.database.windows.net;Database=<yourdatabase>;User ID=<yourmgmtusername>;Password=<yourmgmtpassword>;Trusted_Connection=False;Encrypt=True;Connection Timeout=30;" 

* **Credenciales de usuario para acceso al administrador de mapa de particiones**:  al crear instancias en el Administrador de mapa de particiones en una aplicación que no va a administrar mapas de particiones, use las credenciales que tienen permisos de solo lectura en el mapa de particiones global. La información recuperada del mapa de particiones global con estas credenciales se usa para el [enrutamiento dependiente de los datos](./sql-database-elastic-scale-data-dependent-routing.md) y para rellenar la caché de mapa de particiones en el cliente. Las credenciales se proporcionan a través del mismo patrón de llamada para **GetSqlShardMapManager**, tal como se mostró anteriormente: 
 
        // Obtain shard map manager. 
        ShardMapManager shardMapManager = ShardMapManagerFactory.GetSqlShardMapManager( 
                smmReadOnlyConnectionString, 
                ShardMapManagerLoadPolicy.Lazy
        );  

     Tenga en cuenta el uso de la **smmReadOnlyConnectionString** para reflejar el uso de distintas credenciales para este acceso en nombre de usuarios **no administradores**; estas credenciales no deben proporcionar permisos escritos sobre el mapa de particiones global. 

* **Credenciales de usuario para acceso a datos de usuario**: se necesitan credenciales adicionales cuando se use el método **OpenConnectionForKey** para tener acceso a una partición asociada con una clave. Estas credenciales deben proporcionar los permisos de acceso de solo lectura a las tablas de mapa de particiones local que residen en la partición. Esto es necesario para realizar la validación de la conexión para el enrutamiento dependiente de los datos en la partición. El siguiente fragmento de código muestra el uso de las credenciales de usuario para el acceso a los datos del usuario en el contexto del enrutamiento dependiente de los datos: 
 
        using (SqlConnection conn = rangeMap.OpenConnectionForKey<int>( 
        targetWarehouse, smmUserConnectionString, ConnectionOptions.Validate)) 

    En este ejemplo, **smmUserConnectionString** contiene la cadena de conexión para las credenciales de usuario. En el caso de Base de datos SQL de Azure, la siguiente es una cadena de conexión típica para las credenciales de usuario: 

        "User ID=<yourusername>; Password=<youruserpassword>; Trusted_Connection=False; Encrypt=True; Connection Timeout=30;"  

    Tenga en cuenta que la cadena de conexión no contiene un nombre de servidor y de base de datos. Esto se debe a que la llamada **OpenConnectionForKey** dirigirá automáticamente la conexión a la partición correcta según la clave. Por lo tanto, no se debe proporcionar un nombre de base de datos ni de servidor. 

[AZURE.INCLUDE [elastic-scale-include](../includes/elastic-scale-include.md)]
