<properties 
	pageTitle="Uso de Caché en Redis de Azure" 
	description="Obtener más información acerca de cómo mejorar el rendimiento de sus aplicaciones de Azure con Caché en Redis de Azure" 
	services="redis-cache" 
	documentationCenter="" 
	authors="steved0x" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="cache" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="cache-redis" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="05/20/2015" 
	ms.author="sdanie"/>

# Uso de Caché en Redis de Azure

En esta guía se muestra cómo empezar a usar **Caché en Redis de Azure**. Los ejemplos están escritos en código C# y utilizan el cliente [StackExchange.Redis][]. Entre los escenarios tratados se incluyen la **creación y configuración de una caché**, la **configuración de clientes de caché**, la **incorporación de objetos a la memoria caché y la eliminación de aquellos de esta** y el **almacenamiento del estado de sesión ASP.NET en la memoria caché**. Para obtener más información acerca del uso de Caché en Redis de Azure, consulte la sección [Pasos siguientes][].

<a name="what-is"></a>
## ¿Qué es Caché en Redis de Azure?

Caché en Redis de Microsoft Azure se basa en la conocida Caché en Redis de código fuente abierto. Le proporciona acceso a una caché en Redis segura y dedicada, administrada por Microsoft. Una caché creada usando Caché en Redis de Azure es accesible desde cualquier aplicación dentro de Microsoft Azure.

Caché en Redis de Microsoft Azure está disponible en dos niveles:

-	**Básico** – Nodo único. Varios tamaños de hasta 53 GB.
-	**Estándar** – Principal/Réplica de dos nodos. Varios tamaños de hasta 53 GB. Contrato de nivel de servicio del 99,9 %.

Estos niveles difieren en las características y el precio. Las características se tratan más adelante en esta guía; por otro lado, para obtener más información acerca de los precios consulte [Detalles de precios de caché][].

En esta guía se ofrece una introducción sobre cómo comenzar a usar Caché en Redis de Azure. Para obtener información más detallada acerca de estas características, que van más allá del ámbito de esta guía de introducción, consulte [Introducción a Caché en Redis de Azure][].

<a name="getting-started-cache-service"></a>
## Introducción a Caché en Redis de Azure

Ponerse en marcha con Caché en Redis de Azure es fácil. En primer lugar, tiene que aprovisionar y configurar una caché. A continuación, debe configurar los clientes de caché para que puedan obtener acceso a la caché. Una vez que los clientes de caché estén configurados, ya puede empezar a trabajar con ellos.

-	[Creación de la memoria caché][]
-	[Configuración de los clientes de caché][]

<a name="create-cache"></a>
## Creación de una caché

Para crear una caché, primero inicie sesión en el [Portal de vista previa de Microsoft Azure][] y haga clic en **Nuevo**, **Datos + Almacenamiento** y **Caché en Redis**.

![New cache][NewCacheMenu]

>[AZURE.NOTE]En caso de no tener ninguna cuenta de Azure, puede crear una de evaluación gratuita en tan solo unos minutos. Para obtener más información, consulte [Evaluación gratuita de Azure][].

En la hoja **Nueva caché en Redis**, especifique la configuración que desee para la memoria caché.

![Create cache][CacheCreate]

En **Nombre DNS**, escriba un nombre de subdominio para usar el extremo de caché. El nombre debe tener entre seis y veinte caracteres, solo puede contener números y letras minúsculas, y debe comenzar por una letra.

Use **Nivel de precios** para seleccionar el tamaño y las características de caché que desee. Las cachés **Básicas** tienen un único nodo con varios tamaños hasta 53 GB. Las cachés **Estándar** tienen una configuración principal/de réplica de dos nodos con un contrato de nivel de servicio del 99,9% y varios tamaños hasta 53 GB.

En **Grupo de recursos**, seleccione o cree un grupo de recursos para su caché.

>[AZURE.NOTE]Para obtener más información, consulte [Uso de grupos de recursos para administrar los recursos de Azure][].

En **Suscripción**, seleccione la suscripción de Azure que desee utilizar para la memoria caché. Si su cuenta solo dispone de una suscripción, esta se seleccionará automáticamente y no aparecerá la lista desplegable **Suscripción**.

Use **Ubicación** para especificar la ubicación geográfica en la que se hospeda su caché. Para optimizar el rendimiento, Microsoft recomienda encarecidamente que cree la memoria caché en la misma región que la aplicación cliente de caché.

Una vez que las nuevas opciones de caché estén configuradas, haga clic en **Crear**. La creación de la caché puede tardar unos minutos. Para comprobar el estado, puede supervisar el progreso en el panel de inicio. Una vez que se cree la memoria caché, esta presentará el estado **En ejecución** y estará lista para usarse con la configuración predeterminada.

![Cache created][CacheCreated]

Una vez creada su caché, podrá acceder a ella desde la hoja **Examinar**.

![Browse blade][BrowseCaches]

Haga clic en **Cachés en Redis** para ver sus cachés.

![Caches][Caches]

<a name="NuGet"></a>
## Configuración de los clientes de caché

Una caché creada usando Caché en Redis de Azure es accesible desde cualquier aplicación de Azure. Las aplicaciones .NET desarrolladas en Visual Studio pueden usar el cliente de caché **StackExchange.Redis**, que se puede configurar usando un paquete NuGet que simplifica la configuración de aplicaciones cliente de caché.

>[AZURE.NOTE]Para obtener más información, consulte la página Github [StackExchange.Redis][] y la [documentación del cliente de caché StackExchange.Redis][].

Para configurar una aplicación cliente en Visual Studio usando el paquete NuGet StackExchange.Redis, haga clic con el botón derecho en el proyecto en el **Explorador de soluciones** y elija **Administrar paquetes de NuGet**.

![Manage NuGet packages][NuGetMenu]

Escriba **StackExchange.Redis** o **StackExchange.Redis.StrongName** en el cuadro de texto **Buscar en línea**, seleccione la versión que desee en los resultados y haga clic en **Instalar**.

>[AZURE.NOTE]Si prefiere usar una versión con nombre seguro de la biblioteca de cliente **StackExchange.Redis**, elija **StackExchange.Redis.StrongName**; de lo contrario, elija **StackExchange.Redis**.

![StackExchange.Redis NuGet package][StackExchangeNuget]

El paquete de NuGet se descarga y agrega las referencias de ensamblado requeridas para que la aplicación cliente acceda a Caché en Redis de Azure con el cliente de caché StackExchange.Redis.

Cuando el proyecto de cliente ya está configurado para el almacenamiento en caché, puede usar las técnicas descritas en las siguientes secciones para trabajar con la caché.

<a name="working-with-caches"></a>
## Trabajo con cachés

En esta sección se describe cómo realizar tareas comunes con el servicio de caché.

-	[Conexión a la memoria caché][]
-   [Agregación y recuperación de objetos de la memoria caché][]
-   [Almacenamiento del estado de la sesión de ASP.NET en la memoria caché][]

<a name="connect-to-cache"></a>
## Conexión a la memoria caché

Para trabajar con una caché mediante programación, necesita una referencia a la misma. Agregue lo siguiente a la parte superior de cualquier archivo del que desea usar el cliente StackExchange.Redis para acceder a una Caché en Redis de Azure:

    using StackExchange.Redis;

>[AZURE.NOTE]El cliente StackExchange.Redis requiere .NET Framework 4 o superior.

La clase `ConnectionMultiplexer` administra la conexión con Caché en Redis de Azure. Esta clase está diseñada para compartirse y reusarse a través de su aplicación cliente y no necesita crearse basándose en operación.

Para conectarse a una Caché en Redis de Azure y que se devuelva una instancia de `ConnectionMultiplexer`, llame al método estático `Connect` y pase el extremo y la clave de caché como en el siguiente ejemplo. Utilice la clave de Azure generada desde el portal como parámetro de contraseña.

	ConnectionMultiplexer connection = ConnectionMultiplexer.Connect("contoso5.redis.cache.windows.net,ssl=true,password=...");

>[AZURE.IMPORTANT]Advertencia: nunca almacene credenciales en el código fuente. Para que este ejemplo resulte sencillo, se muestra en código fuente. Consulte [Cómo funcionan las cadenas de aplicación y las cadenas de conexión][] para obtener información sobre cómo almacenar credenciales.

Si no desea usar SSL, establezca `ssl=false` o simplemente pase el extremo y la clave.

>[AZURE.NOTE]El puerto no SSL está deshabilitado de forma predeterminada para las cachés nuevas. Para obtener instrucciones acerca de cómo habilitar el puerto no SSL, consulte la sección de puertos de acceso en el tema [Configurar una memoria caché en Caché en Redis de Azure][].

	connection = ConnectionMultiplexer.Connect("contoso5.redis.cache.windows.net,password=...");

Para obtener más información sobre las opciones de configuración de conexión avanzadas, consulte [Modelo de configuración de StackExchange.Redis][].

El extremo y las claves de caché se pueden obtener del cuadro de la hoja **Caché en Redis** para su instancia de caché.

![Cache properties][CacheProperties]

![Manage keys][ManageKeys]

Una vez establecida la conexión, devuelva una referencia a la base de datos de caché en Redis llamando al método `ConnectionMultiplexer.GetDatabase`.

	// connection referes to a previously configured ConnectionMultiplexer
	IDatabase cache = connection.GetDatabase();

>[AZURE.NOTE]El objeto devuelto desde el método `GetDatabase` es un objeto de paso a través ligero y no necesita almacenarse.

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

Los elementos se pueden almacenar en una memoria caché y recuperarse de esta usando los métodos `StringSet` y `StringGet`.

	// If key1 exists, it is overwritten.
	cache.StringSet("key1", "value1");

	string value = cache.StringGet("key1");

>[AZURE.NOTE]Redis almacena la mayoría de los datos como cadenas Redis, pero estas cadenas pueden contener muchos tipos de datos, como por ejemplo datos binarios serializados, que se pueden usar cuando se almacenan objetos .NET en caché.

Cuando llame a `StringGet`, si el objeto existe, se devuelve y, si no existe, se devuelve null. En este caso, puede recuperar el valor desde el origen de datos que desee y almacenarlo en la memoria caché para su uso posterior. Esto se conoce como patrón cache-aside.

    string value = cache.StringGet("key1");
    if (value == null)
    {
        // The item keyed by "key1" is not in the cache. Obtain
        // it from the desired data source and add it to the cache.
        value = GetValueFromDataSource();

        cache.StringSet("key1", value);
    }

>[AZURE.NOTE]Caché en Redis de Azure puede almacenar en caché objetos .NET así como tipos de datos primitivos, pero antes de poder almacenar un objeto .NET en caché, se debe serializar. Esta es la responsabilidad del desarrollador de la aplicación, que tiene total flexibilidad a la hora de elegir el serializador. Para obtener más información, consulte [Trabajar con objetos .NET en la memoria caché][].

<a name="specify-expiration"></a>
## Especificación de la expiración de un elemento en la memoria caché

Para especificar la expiración de un elemento en la memoria caché, use el parámetro `TimeSpan` de `StringSet`.

	cache.StringSet("key1", "value1", TimeSpan.FromMinutes(90));


<a name="store-session"></a>
## Almacenamiento del estado de la sesión de ASP.NET en la memoria caché

Caché en Redis de Azure proporciona un proveedor de estado de sesión que puede usar para almacenar su estado de sesión en una caché en lugar de en memoria o en una base de datos de SQL Server. Para usar el proveedor de estado de sesión de caché, configure primero su caché y, después, configure su aplicación ASP.NET para caché mediante el paquete NuGet de estado de sesión de caché en Redis.

Para configurar una aplicación cliente en Visual Studio usando el paquete NuGet de estado de sesión de caché en Redis, haga clic con el botón derecho en el proyecto en el **Explorador de soluciones** y elija **Administrar paquetes de NuGet**.

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

Configure los atributos con los valores del cuadro de su caché del portal y configure el resto de valores según estime oportuno.

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

Asegúrese de comentar el proveedor de estado de sesión **InProc** estándar.

    <!-- <sessionState mode="InProc" customProvider="DefaultSessionProvider">
      <providers>
        <add name="DefaultSessionProvider" type="System.Web.Providers.DefaultSessionStateProvider, System.Web.Providers, Version=1.0.0.0, Culture=neutral, PublicKeyToken=31bf3856ad364e35" connectionStringName="DefaultConnection" />
      </providers>
    </sessionState> -->

Para obtener más información sobre la configuración y uso del proveedor de estado de sesión de Redis de Azure, consulte [Proveedor de estado de sesión de Redis de Azure][].

<a name="next-steps"></a>
## Pasos siguientes

Ahora que está familiarizado con los aspectos básicos de Caché en Redis de Azure, utilice estos vínculos para obtener más información sobre cómo realizar tareas de almacenamiento en caché más complejas.

-	[Habilite los diagnósticos de caché](https://msdn.microsoft.com/library/azure/dn763945.aspx#EnableDiagnostics) para que pueda [supervisar](https://msdn.microsoft.com/library/azure/dn763945.aspx) el estado de la memoria caché. Puede ver las métricas en el portal y también [descargarlas y revisarlas](https://github.com/rustd/RedisSamples/tree/master/CustomMonitoring) usando las herramientas que prefiera.
-	Compruebe la [documentación del cliente de caché StackExchange.Redis][].
	-	Se puede obtener acceso a Caché en Redis de Azure desde numerosos clientes Redis e idiomas de desarrollo. Para obtener más información, vea [http://redis.io/clients][] y [Desarrollar en otros idiomas para Caché en Redis de Azure][].
	-	Caché en Redis de Azure también se puede usar con servicios como Redsmin. Para obtener más información, vea [Recuperación de una cadena de conexión de Redis de Azure y su uso con Redsmin][].
-	Vea la documentación de [Redis][] y lea la información sobre los [tipos de datos Redis][] y una [introducción de quince minutos para tipos de datos Redis][].
-   Consulte la referencia MSDN para [Caché en Redis de Azure][]. 


<!-- INTRA-TOPIC LINKS -->
[Pasos siguientes]: #next-steps
[Introduction to Azure Redis Cache (Video)]: #video
[What is Azure Redis Cache?]: #what-is
[Create an Azure Cache]: #create-cache
[Which type of caching is right for me?]: #choosing-cache
[Prepare Your Visual Studio Project to Use Azure Caching]: #prepare-vs
[Configure Your Application to Use Caching]: #configure-app
[Get Started with Azure Redis Cache]: #getting-started-cache-service
[Creación de la memoria caché]: #create-cache
[Configure the cache]: #enable-caching
[Configuración de los clientes de caché]: #NuGet
[Working with Caches]: #working-with-caches
[Conexión a la memoria caché]: #connect-to-cache
[Agregación y recuperación de objetos de la memoria caché]: #add-object
[Specify the expiration of an object in the cache]: #specify-expiration
[Almacenamiento del estado de la sesión de ASP.NET en la memoria caché]: #store-session

  
<!-- IMAGES -->
[NewCacheMenu]: ./media/cache-dotnet-how-to-use-azure-redis-cache/redis-cache-new-cache-menu.png

[CacheCreate]: ./media/cache-dotnet-how-to-use-azure-redis-cache/redis-cache-cache-create.png

[StackExchangeNuget]: ./media/cache-dotnet-how-to-use-azure-redis-cache/redis-cache-stackexchange-redis.png

[NuGetMenu]: ./media/cache-dotnet-how-to-use-azure-redis-cache/redis-cache-manage-nuget-menu.png

[CacheProperties]: ./media/cache-dotnet-how-to-use-azure-redis-cache/redis-cache-properties.png

[ManageKeys]: ./media/cache-dotnet-how-to-use-azure-redis-cache/redis-cache-manage-keys.png

[SessionStateNuGet]: ./media/cache-dotnet-how-to-use-azure-redis-cache/redis-cache-session-state-provider.png

[BrowseCaches]: ./media/cache-dotnet-how-to-use-azure-redis-cache/redis-cache-browse-caches.png

[Caches]: ./media/cache-dotnet-how-to-use-azure-redis-cache/redis-cache-caches.png

[CacheCreated]: ./media/cache-dotnet-how-to-use-azure-redis-cache/redis-cache-cache-created.png




   
<!-- LINKS -->
[http://redis.io/clients]: http://redis.io/clients
[Desarrollar en otros idiomas para Caché en Redis de Azure]: http://msdn.microsoft.com/library/azure/dn690470.aspx
[Recuperación de una cadena de conexión de Redis de Azure y su uso con Redsmin]: https://redsmin.uservoice.com/knowledgebase/articles/485711-how-to-connect-redsmin-to-azure-redis-cache
[Proveedor de estado de sesión de Redis de Azure]: http://go.microsoft.com/fwlink/?LinkId=398249
[How to: Configure a Cache Client Programmatically]: http://msdn.microsoft.com/library/windowsazure/gg618003.aspx
[Session State Provider for Azure Cache]: http://go.microsoft.com/fwlink/?LinkId=320835
[Azure AppFabric Cache: Caching Session State]: http://www.microsoft.com/showcase/details.aspx?uuid=87c833e9-97a9-42b2-8bb1-7601f9b5ca20
[Output Cache Provider for Azure Cache]: http://go.microsoft.com/fwlink/?LinkId=320837
[Azure Shared Caching]: http://msdn.microsoft.com/library/windowsazure/gg278356.aspx
[Team Blog]: http://blogs.msdn.com/b/windowsazure/
[Azure Caching]: http://www.microsoft.com/showcase/Search.aspx?phrase=azure+caching
[How to Configure Virtual Machine Sizes]: http://go.microsoft.com/fwlink/?LinkId=164387
[Azure Caching Capacity Planning Considerations]: http://go.microsoft.com/fwlink/?LinkId=320167
[Azure Caching]: http://go.microsoft.com/fwlink/?LinkId=252658
[How to: Set the Cacheability of an ASP.NET Page Declaratively]: http://msdn.microsoft.com/library/zd1ysf1y.aspx
[How to: Set a Page's Cacheability Programmatically]: http://msdn.microsoft.com/library/z852zf6b.aspx
[Configurar una memoria caché en Caché en Redis de Azure]: http://msdn.microsoft.com/library/azure/dn793612.aspx

[Modelo de configuración de StackExchange.Redis]: http://github.com/StackExchange/StackExchange.Redis/blob/master/Docs/Configuration.md

[Trabajar con objetos .NET en la memoria caché]: http://msdn.microsoft.com/library/dn690521.aspx#Objects


[NuGet Package Manager Installation]: http://go.microsoft.com/fwlink/?LinkId=240311
[Detalles de precios de caché]: http://www.windowsazure.com/pricing/details/cache/
[Portal de vista previa de Microsoft Azure]: https://portal.azure.com/

[Introducción a Caché en Redis de Azure]: http://go.microsoft.com/fwlink/?LinkId=320830
[Caché en Redis de Azure]: http://go.microsoft.com/fwlink/?LinkId=398247

[Migrate to Azure Redis Cache]: http://go.microsoft.com/fwlink/?LinkId=317347
[Azure Redis Cache Samples]: http://go.microsoft.com/fwlink/?LinkId=320840
[Uso de grupos de recursos para administrar los recursos de Azure]: http://azure.microsoft.com/documentation/articles/resource-group-overview/

[StackExchange.Redis]: http://github.com/StackExchange/StackExchange.Redis
[documentación del cliente de caché StackExchange.Redis]: http://github.com/StackExchange/StackExchange.Redis#documentation

[Redis]: http://redis.io/documentation
[tipos de datos Redis]: http://redis.io/topics/data-types
[introducción de quince minutos para tipos de datos Redis]: http://redis.io/topics/data-types-intro

[Cómo funcionan las cadenas de aplicación y las cadenas de conexión]: http://azure.microsoft.com/blog/2013/07/17/windows-azure-web-sites-how-application-strings-and-connection-strings-work/

[Evaluación gratuita de Azure]: http://azure.microsoft.com/pricing/free-trial/?WT.mc_id=redis_cache_hero

<!---HONumber=July15_HO3-->