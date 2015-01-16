<properties pageTitle="Uso de Caché en Redis de Azure" metaKeywords="" description="Obtener información acerca de cómo crear una memoria caché en Caché en Redis de Azure" metaCanonical="" services="cache" documentationCenter="API Management" title="How to Use Azure Redis Cache" authors="sdanie" solutions="" manager="dwrede" editor="" />

<tags ms.service="cache" ms.workload="tbd" ms.tgt_pltfrm="cache-redis" ms.devlang="dotnet" ms.topic="article" ms.date="09/23/2014" ms.author="sdanie" />

# Uso de Caché en Redis de Azure

Esta guía muestra cómo empezar a usar 
**Caché en Redis de Azure**. Los ejemplos están escritos en código C\ # y
usan la API. NET. Entre los escenarios tratados se incluyen **la creación y configuración de una caché**, **la configuración de clientes de caché**, **la incorporación de objetos a la memoria caché y la eliminación de aquellos de esta** y el **almacenamiento del estado de sesión ASP.NET en la memoria caché**. Para obtener más
información acerca del uso de Caché en Redis de Azure, consulte la sección [Pasos siguientes][].

## Tabla de contenido

-   [¿Qué es Caché en Redis de Azure?][]
-	[Introducción a Caché en Redis de Azure][]
	-	[Creación de la caché][]
	-	[Configuración de los clientes de caché][]
-	[Uso de cachés][]
	-	[Conexión a la memoria caché][]
	-   [Agregación y recuperación de objetos de la memoria caché][]
	-   [Especificación de la expiración de un objeto en la memoria caché][]
	-   [Almacenamiento del estado de sesión ASP.NET en la memoria caché][]
-   [Pasos siguientes][]

<a name="what-is"></a>
## ¿Qué es Caché en Redis de Azure?

Caché en Redis de Microsoft Azure se basa en la conocida Caché en Redis de código fuente abierto. Le proporciona acceso a una caché en Redis segura y dedicada, administrada por Microsoft. Una caché creada usando Caché en Redis de Azure es accesible desde cualquier aplicación dentro de Microsoft Azure.

Caché en Redis de Microsoft Azure está disponible en dos niveles:

-	**Básico** - Nodo único. Varios tamaños de hasta 53 GB.
-	**Estándar** - Principal/secundario de dos nodos. Varios tamaños de hasta 53 GB. Contrato de nivel de servicio del 99,9 %.

Estos niveles difieren en las características y el precio. Las características se tratan más adelante en esta guía; por otro lado, para obtener más información acerca de los precios consulte [Detalles de precios de caché][].

En esta guía se ofrece una introducción sobre cómo comenzar a usar Caché en Redis de Azure. Para obtener información más detallada acerca de estas características, que van más allá del ámbito de esta guía de introducción, consulte [Introducción a Caché en Redis de Azure][].

<a name="getting-started-cache-service"></a>
## Introducción a Caché en Redis de Azure

Ponerse en marcha con Caché en Redis de Azure es fácil. En primer lugar, tiene que aprovisionar y configurar una caché. A continuación, debe configurar los clientes de caché para que puedan obtener acceso a la caché. Una vez que los clientes de caché estén configurados, ya puede empezar a trabajar con ellos.

-	[Creación de la caché][]
-	[Configuración de la caché][]
-	[Configuración de los clientes de caché][]

<a name="create-cache"></a>
## Creación de una caché

Para crear una caché, primero inicie sesión en el portal de vista previa de administración de Azure y haga clic en **Nuevo**, **Caché en Redis**.

![New cache][NewCacheMenu]

En la hoja **Nueva caché en Redis**, especifique la configuración que desee para la memoria caché.

![Create cache][CacheCreate]

En **Nombre DNS**, escriba un nombre de subdominio para usar el extremo de caché. El nombre debe tener entre seis y veinte caracteres, solo puede contener números y letras minúsculas, y debe comenzar por una letra.

Use **Nivel de precios** para seleccionar el tamaño y características de caché que desee. Caché en Redis está disponible en los dos siguientes niveles.

-	**Básico** - Modo único, varios tamaños de hasta 53 GB.
-	**Estándar** - Principal/secundario de dos nodos, contrato de nivel de servicio del 99,9 % y varios tamaños de hasta 53 GB.

En **Suscripción**, seleccione la suscripción de Azure que desee utilizar para la memoria caché.

>[AZURE.NOTE] Si su cuenta solo dispone de una suscripción, esta se seleccionará automáticamente y no aparecerá la lista desplegable de suscripciones.

En **Grupo de recursos**, seleccione o cree un grupo de recursos para su caché.

>[AZURE.NOTE] Para obtener más información, consulte [Uso del grupo de recursos para administrar los recursos de Azure][]. 

Use **Geolocalización** para especificar la ubicación geográfica en la que se hospeda su caché. Para optimizar el rendimiento, Microsoft recomienda encarecidamente que cree la memoria caché en la misma región que la aplicación cliente de caché.

Una vez que las nuevas opciones de caché estén configuradas, haga clic en **Crear**. La creación de la caché puede tardar unos minutos. Para comprobar el estado, puede supervisar el progreso en el panel de inicio. Una vez que se cree la memoria caché, esta presentará el estado **En ejecución** y estará lista para usarse con la configuración predeterminada.

![Cache created][CacheCreated]

Una vez creada su caché, podrá acceder a ella desde la hoja **Examinar**.

![Browse blade][BrowseCaches]

Haga clic en **Cachés** para ver sus cachés.

![Caches][Caches]

<a name="NuGet"></a>
## Configuración de los clientes de caché

Una caché creada usando Caché en Redis de Azure es accesible desde cualquier aplicación de Azure. Las aplicaciones .NET desarrolladas en Visual Studio pueden usar el cliente de caché **StackExchange.Redis**, que se puede configurar usando un paquete NuGet que simplifica la configuración de aplicaciones cliente de caché. 

>[AZURE.NOTE] Para obtener más información, vea la página Github [StackExchange.Redis][] y la [documentación del cliente de caché StackExchange.Redis][].

Para configurar una aplicación cliente en Visual Studio usando el paquete NuGet StackExchange.Redis, haga clic con el botón secundario en el proyecto en el **Explorador de soluciones** y elija **Administrar paquetes de NuGet**. 

![Manage NuGet packages][NuGetMenu]

Escriba **StackExchange.Redis** o **StackExchange.Redis.StrongName** en el cuadro de texto **Buscar en línea**, seleccione la versión que desee en los resultados y haga clic en **Instalar**.

>[AZURE.NOTE] Si prefiere usar una versión con nombre seguro de la biblioteca de cliente **StackExchange.Redis**, elija **StackExchange.Redis.StrongName**; de lo contrario, elija **StackExchange.Redis**.

![StackExchange.Redis NuGet package][StackExchangeNuget]

El paquete de NuGet se descarga y agrega las referencias de ensamblado requeridas para que la aplicación cliente acceda a Caché en Redis de Azure con el cliente de caché StackExchange.Redis.

Cuando el proyecto de cliente ya está configurado para el almacenamiento en caché, puede usar las técnicas descritas en las siguientes secciones para trabajar con la caché.

<a name="working-with-caches"></a>
## Trabajo con cachés

En esta sección se describe cómo realizar tareas comunes con el servicio de caché.

-	[Conexión a la memoria caché][]
-   [Agregación y recuperación de objetos de la memoria caché][]
-   [Almacenamiento del estado de sesión ASP.NET en la memoria caché][]

<a name="connect-to-cache"></a>
## Conexión a la memoria caché

Para trabajar con una caché mediante programación, necesita una referencia a la misma. Agregue lo siguiente a la parte superior de cualquier archivo del que desea usar el cliente StackExchange.Redis para acceder a una Caché en Redis de Azure:

    using StackExchange.Redis;

>[AZURE.NOTE] El cliente StackExchange.Redis requiere .NET Framework 4 o superior.

La clase `ConnectionMultiplexer` administra la conexión con Caché en Redis de Azure. Esta clase está diseñada para compartirse y reusarse a través de su aplicación cliente y no necesita crearse basándose en operación. 

Para conectarse a una Caché en Redis de Azure y que se devuelva una instancia de `ConnectionMultiplexer` conectado, llame al método `Connect` estático y pase el extremo y la clave de caché como en el siguiente ejemplo. Utilice la clave de Azure generada desde el portal de servicio como el parámetro de contraseña.

	ConnectionMultiplexer connection = ConnectionMultiplexer.Connect("contoso5.redis.cache.windows.net,ssl=true,password=...");

>[AZURE.NOTE] Advertencia: nunca almacene credenciales en código fuente. Para que este ejemplo resulte sencillo, se muestra en código fuente. Consulte [Sitios web Microsoft Azure: cómo funcionan las cadenas de aplicación y las cadenas de conexión][] para obtener información sobre cómo almacenar credenciales.

Si no desea usar SSL, establezca `ssl=false` o simplemente pase el extremo y la clave.

	connection = ConnectionMultiplexer.Connect("contoso5.redis.cache.windows.net,password=...");

>[AZURE.NOTE] Para obtener más información sobre las opciones de configuración de conexión avanzadas, vea el [modelo de configuración de StackExchange.Redis][].

El extremo y las claves de caché se pueden obtener del cuadro del portal de vista previa de administración de Azure para su instancia de caché.

![Cache properties][CacheProperties]

![Manage keys][ManageKeys]

Una vez establecida la conexión, devuelva una referencia a la base de datos de caché Redis llamando al método `ConnectionMultiplexer.GetDatabase`.

	// connection referes to a previously configured ConnectionMultiplexer
	IDatabase cache = connection.GetDatabase();

>[AZURE.NOTE] El objeto devuelto desde el método `GetDatabase` es un objeto de paso a través ligero y no necesita almacenarse.

	ConnectionMultiplexer connection = ConnectionMultiplexer.Connect("contoso5.redis.cache.windows.net,ssl=true,password=...");

	IDatabase cache = connection.GetDatabase();

	// Perform cache operations using the cache object...
	// Simple put of integral data types into the cache
	cache.StringSet("key1", "value");
	cache.StringSet("key2", 25);

	// Simple get of data types from the cache
	string key1 = cache.StringGet("key1");
	int key2 = (int)cache.StringGet("key2");

Ahora que sabe cómo conectarse a una instancia de Caché en Redis de Azure y devolver una referencia a la base de datos de caché, echemos un vistazo a cómo se trabaja con la memoria caché.



<a name="add-object"></a>
## Agregación y recuperación de objetos de la memoria caché

Los elementos se pueden almacenar en y recuperar de una memoria caché mediante los métodos `StringSet` y `StringGet`.

	// If key1 exists, it is overwritten.
	cache.StringSet("key1", "value1");

	string value = cache.StringGet("key1");

>[AZURE.NOTE] Redis almacena la mayoría de los datos como cadenas Redis, pero estas cadenas pueden contener muchos tipos de datos, como por ejemplo datos binarios serializados, que se pueden usar cuando se almacenan objetos .NET en caché.

Cuando se llama a `StringGet`, si el objeto existe, este se devuelve, y si no existe, se devuelve null. En este caso, puede recuperar el valor desde el origen de datos que desee y almacenarlo en la memoria caché para su uso posterior. Esto se conoce como patrón cache-aside.

    string value = cache.StringGet("key1");
    if (value == null)
    {
        // The item keyed by "key1" is not in the cache. Obtain
        // it from the desired data source and add it to the cache.
        value = GetValueFromDataSource();

        cache.StringSet("key1", value);
    }

>[AZURE.NOTE] Caché en Redis de Azure puede almacenar en caché objetos .NET así como tipos de datos primitivos, pero antes de poder almacenar un objeto .NET en caché, se debe serializar. Esta es la responsabilidad del desarrollador de la aplicación, que tiene total flexibilidad a la hora de elegir el serializador. Para obtener más información, vea [Trabajar con objetos .NET en la memoria caché][].

<a name="specify-expiration"></a>
## Especificación de la expiración de un elemento en la memoria caché

Para especificar la expiración de un elemento en la memoria caché, utilice el parámetro `TimeSpan` de `StringSet`.

	cache.StringSet("key1", "value1", TimeSpan.FromMinutes(90));


<a name="store-session"></a>
## Almacenamiento del estado de sesión ASP.NET en la memoria caché

Caché en Redis de Azure proporciona un proveedor de estado de sesión que puede usar para almacenar su estado de sesión en una caché en lugar de en memoria o en una base de datos de SQL Server. Para usar el proveedor de estado de sesión de caché,
configure primero su caché y, después, configure su aplicación ASP.NET para caché mediante el paquete NuGet de estado de sesión de caché Redis.

Para configurar una aplicación cliente en Visual Studio usando el paquete NuGet de estado de sesión de caché Redis, haga clic con el botón secundario en el proyecto en el **Explorador de soluciones** y elija **Administrar paquetes de NuGet**. 

![Manage NuGet packages][NuGetMenu]

Escriba **RedisSessionStateProvider** en el cuadro de texto **Buscar en línea**, selecciónelo en los resultados y haga clic en **Instalar**.

![Redis Cache Session State NuGet Package][SessionStateNuGet]

El paquete de NuGet se descarga, agrega las referencias de ensamblado requeridas y agrega la siguiente sección en el archivo web.config que contiene la configuración requerida para que su aplicación ASP.NET use el proveedor del estado de sesión de caché Redis.

	<sessionState mode="Custom" customProvider="MySessionStateStore">
      <providers>
        <!--
          <add name="MySessionStateStore" 
            host = "127.0.0.1" [String]
            port = "" [number]
            accessKey = "" [String]
            ssl = "false" [true|false]
            throwOnError = "true" [true|false]
            retryTimeoutInMilliseconds = "0" [number]
            databaseId = "0" [number]
            applicationName = "" [String]
            connectionTimeoutInMilliseconds = "5000" [number]
            operationTimeoutInMilliseconds = "5000" [number]
          />
        -->
        <add name="MySessionStateStore" type="Microsoft.Web.Redis.RedisSessionStateProvider" host="127.0.0.1" accessKey="" ssl="false" />
      </providers>
    </sessionState>



La sección comentada proporciona un ejemplo de los atributos y la configuración de ejemplo para ellos.

Configure los atributos con los valores del cuadro de su caché del portal de vista previa y configure el resto de valores según estime oportuno.

	<sessionState mode="Custom" customProvider="MySessionStateStore">
      <providers>
        <!--
          <add name="MySessionStateStore" 
            host = "127.0.0.1" [String]
            port = "" [number]
            accessKey = "" [String]
            ssl = "false" [true|false]
            throwOnError = "true" [true|false]
            retryTimeoutInMilliseconds = "0" [number]
            databaseId = "0" [number]
            applicationName = "" [String]
            connectionTimeoutInMilliseconds = "5000" [number]
            operationTimeoutInMilliseconds = "5000" [number]
          />
        -->
        <add name="MySessionStateStore" type="Microsoft.Web.Redis.RedisSessionStateProvider" host="contoso5.redis.cache.windows.net" 
		accessKey="..." ssl="true" />
      </providers>
    </sessionState>

Asegúrese de comentar el proveedor del estado de sesión **InProc** estándar.

    <!-- <sessionState mode="InProc" customProvider="DefaultSessionProvider">
      <providers>
        <add name="DefaultSessionProvider" type="System.Web.Providers.DefaultSessionStateProvider, System.Web.Providers, Version=1.0.0.0, Culture=neutral, PublicKeyToken=31bf3856ad364e35" connectionStringName="DefaultConnection" />
      </providers>
    </sessionState> -->

Para obtener más información sobre la configuración y uso del proveedor del estado de sesión de Redis de Azure, vea [Proveedor del estado de sesión de Redis de Azure][].

<a name="next-steps"></a>
## Pasos siguientes

Ahora que conoce los aspectos básicos de Caché en Redis de Azure,
siga estos vínculos para obtener información sobre cómo realizar tareas de almacenamiento en caché más complejas.

-	Para obtener más información acerca del cliente StackExchange.Redis: [documentación del cliente de caché StackExchange.Redis][]
-	Vea la documentación de [redis][] y lea la información sobre los [tipos de datos Redis][] y [una introducción de quince minutos para tipos de datos Redis][].
-   Consulte la referencia de MSDN:: [Caché en Redis de Azure][]


<!-- INTRA-TOPIC LINKS -->
[Pasos siguientes]: #next-steps
[¿Qué es Caché en Redis de Azure?]: #what-is
[Creación de una caché de Azure]: #create-cache
[¿Qué tipo de almacenamiento en caché es el adecuada para mí?]: #choosing-cache
[Preparación del proyecto de Visual Studio para usar el almacenamiento en caché de Azure]: #prepare-vs
[Configuración de su aplicación para usar almacenamiento en caché]: #configure-app
[Introducción a Caché en Redis de Azure]: #getting-started-cache-service
[Creación de la caché]: #create-cache
[Configuración de la caché]: #enable-caching
[Configuración de los clientes de caché]: #NuGet
[Trabajo con cachés]: #working-with-caches
[Conexión a la memoria caché]: #connect-to-cache
[Agregación y recuperación de objetos de la memoria caché]: #add-object
[Especificación de la expiración de un objeto en la memoria caché]: #specify-expiration
[Almacenamiento del estado de sesión ASP.NET en la memoria caché]: #store-session

  
<!-- IMAGES -->
[MenúNewCache]: ./media/cache-dotnet-how-to-use-azure-redis-cache/redis-cache-new-cache-menu.png

[CacheCreate]: ./media/cache-dotnet-how-to-use-azure-redis-cache/redis-cache-cache-create.png

[StackExchangeNuget]: ./media/cache-dotnet-how-to-use-azure-redis-cache/redis-cache-stackexchange-redis.png

[NuGetMenu]: ./media/cache-dotnet-how-to-use-azure-redis-cache/redis-cache-manage-nuget-menu.png

[CacheProperties]: ./media/cache-dotnet-how-to-use-azure-redis-cache/redis-cache-properties.png

[AdministrarClaves]: ./media/cache-dotnet-how-to-use-azure-redis-cache/redis-cache-manage-keys.png

[SessionStateNuGet]: ./media/cache-dotnet-how-to-use-azure-redis-cache/redis-cache-session-state-provider.png

[BrowseCaches]: ./media/cache-dotnet-how-to-use-azure-redis-cache/redis-cache-browse-caches.png

[Caches]: ./media/cache-dotnet-how-to-use-azure-redis-cache/redis-cache-caches.png

[CacheCreated]: ./media/cache-dotnet-how-to-use-azure-redis-cache/redis-cache-cache-created.png




   
<!-- LINKS -->
[Proveedor de estado de sesión de Redis de Azure]: http://go.microsoft.com/fwlink/?LinkId=398249
[Portal de administración de Azure]: http://windows.azure.com/
[Configuración de un cliente de caché mediante programación]: http://msdn.microsoft.com/es-es/library/windowsazure/gg618003.aspx
[Proveedor de estado de sesión para Caché de Azure]: http://go.microsoft.com/fwlink/?LinkId=320835
[Caché de Azure AppFabric: estado de sesión de almacenamiento en caché]: http://www.microsoft.com/es-es/showcase/details.aspx?uuid=87c833e9-97a9-42b2-8bb1-7601f9b5ca20
[Proveedor de caché de salida para Caché de Azure]: http://go.microsoft.com/fwlink/?LinkId=320837
[Almacenamiento en caché compartido de Azure]: http://msdn.microsoft.com/es-es/library/windowsazure/gg278356.aspx
[Blog del equipo]: http://blogs.msdn.com/b/windowsazure/
[Almacenamiento en caché de Azure]: http://www.microsoft.com/es-es/showcase/Search.aspx?phrase=azure+caching
[Configuración de tamaños de máquina Virtual]: http://go.microsoft.com/fwlink/?LinkId=164387
[Consideraciones de planeación de capacidad de almacenamiento en caché de Azure]: http://go.microsoft.com/fwlink/?LinkId=320167
[Almacenamiento en caché de Azure]: http://go.microsoft.com/fwlink/?LinkId=252658
[Establecimiento de la capacidad de almacenamiento en caché de una página ASP.NET mediante declaración]: http://msdn.microsoft.com/es-es/library/zd1ysf1y.aspx
[Establecimiento de la capacidad de almacenamiento en caché de una página mediante programación]: http://msdn.microsoft.com/es-es/library/z852zf6b.aspx

[Modelo de configuración StackExchange.Redis]: http://github.com/StackExchange/StackExchange.Redis/blob/master/Docs/Configuration.md

[Trabajar con objetos .NET en la memoria caché]: http://msdn.microsoft.com/es-es/library/dn690521.aspx#Objects


[Instalación del Administrador de paquetes de NuGet]: http://go.microsoft.com/fwlink/?LinkId=240311
[Detalles de precios de caché]: http://www.windowsazure.com/es-es/pricing/details/cache/
[Portal de administración]: https://manage.windowsazure.com/

[Información general de Caché en Redis de Azure]: http://go.microsoft.com/fwlink/?LinkId=320830
[Caché en Redis de Azure]: http://go.microsoft.com/fwlink/?LinkId=398247

[Migración a Caché en Redis de Azure]: http://go.microsoft.com/fwlink/?LinkId=317347
[Ejemplos de Caché en Redis de Azure]: http://go.microsoft.com/fwlink/?LinkId=320840
[Uso del grupo de recursos para administrar los recursos de Azure]: http://azure.microsoft.com/es-es/documentation/articles/azure-preview-portal-using-resource-groups/

[StackExchange.Redis]: http://github.com/StackExchange/StackExchange.Redis
[documentación del cliente de caché StackExchange.Redis]: http://github.com/StackExchange/StackExchange.Redis#documentation

[Redis]: http://redis.io/documentation
[Tipos de datos de Redis]: http://redis.io/topics/data-types
[una introducción de quince minutos a tipos de datos de Redis]: http://redis.io/topics/data-types-intro

[Sitios web Microsoft Azure: funcionamiento de las cadenas de aplicación y de las cadenas de conexión]: http://azure.microsoft.com/blog/2013/07/17/windows-azure-web-sites-how-application-strings-and-connection-strings-work/
