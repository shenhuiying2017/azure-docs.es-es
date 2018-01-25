---
title: Uso de Azure Redis Cache | Microsoft Docs
description: "Obtener más información acerca de cómo mejorar el rendimiento de sus aplicaciones de Azure con Azure Redis Cache"
services: redis-cache,app-service
documentationcenter: 
author: wesmc7777
manager: cfowler
editor: 
ms.assetid: c502f74c-44de-4087-8303-1b1f43da12d5
ms.service: cache
ms.workload: tbd
ms.tgt_pltfrm: cache-redis
ms.devlang: dotnet
ms.topic: hero-article
ms.date: 07/27/2017
ms.author: wesmc
ms.openlocfilehash: a9276eaa4c7d8b11891d7dfade475316ffac5f71
ms.sourcegitcommit: 2a70752d0987585d480f374c3e2dba0cd5097880
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/19/2018
---
# <a name="how-to-use-azure-redis-cache"></a>Uso de Azure Redis Cache
> [!div class="op_single_selector"]
> * [.NET](cache-dotnet-how-to-use-azure-redis-cache.md)
> * [ASP.NET](cache-web-app-howto.md)
> * [Node.js](cache-nodejs-get-started.md)
> * [Java](cache-java-get-started.md)
> * [Python](cache-python-get-started.md)
> 
> 

En esta guía se muestra cómo empezar a usar **Azure Redis Cache**. Microsoft Azure Redis Cache se basa en la conocida solución Redis Cache de código fuente abierto. Le proporciona acceso a una caché en Redis segura y dedicada, administrada por Microsoft. Una caché creada con Azure Redis Cache es accesible desde cualquier aplicación dentro de Microsoft Azure.

Microsoft Azure Redis Cache está disponible en los siguientes niveles:

* **Básico** – Nodo único. Varios tamaños de hasta 53 GB.
* **Estándar**: principal/réplica de dos nodos. Varios tamaños de hasta 53 GB. Contrato de nivel de servicio del 99,9 %.
* **Premium** : principal/réplica de dos nodos con hasta 10 particiones. Varios tamaños, de 6 GB a 530 GB. Todas las características del nivel Estándar y algunas otras características son compatibles con los [clústeres de Redis](cache-how-to-premium-clustering.md), la [persistencia de Redis](cache-how-to-premium-persistence.md) y [Azure Virtual Network](cache-how-to-premium-vnet.md). Contrato de nivel de servicio del 99,9 %.

Estos niveles difieren en las características y el precio. Para más información sobre los precios, consulte los [precios de caché][Cache Pricing Details].

En esta guía se explica cómo utilizar el cliente [StackExchange.Redis][StackExchange.Redis] con código C\#. Entre los escenarios tratados, se incluye la **creación y configuración de una memoria caché**, la **configuración de clientes de caché** y la **adición y eliminación de objetos de la memoria caché**. Para más información sobre el uso de Azure Redis Cache, consulte [Pasos siguientes][Next Steps]. Para obtener un tutorial paso a paso de creación de una aplicación web ASP.NET MVC con Redis Cache, consulte [Creación de una aplicación web con Redis Cache](cache-web-app-howto.md).

<a name="getting-started-cache-service"></a>

## <a name="get-started-with-azure-redis-cache"></a>Introducción a Azure Redis Cache
Empezar a trabajar con Azure Redis Cache es fácil. En primer lugar, tiene que aprovisionar y configurar una caché. A continuación, debe configurar los clientes de caché para que puedan obtener acceso a la caché. Una vez que los clientes de caché estén configurados, ya puede empezar a trabajar con ellos.

* [Creación de la memoria caché][Create the cache]
* [Configuración de los clientes de caché][Configure the cache clients]

<a name="create-cache"></a>

## <a name="create-a-cache"></a>Creación de una caché
[!INCLUDE [redis-cache-create](../../includes/redis-cache-create.md)]

### <a name="to-access-your-cache-after-its-created"></a>Para acceder a la memoria caché una vez creada
[!INCLUDE [redis-cache-create](../../includes/redis-cache-browse.md)]

Para más información acerca de cómo configurar la memoria caché, consulte [Configuración de Azure Redis Cache](cache-configure.md).

<a name="NuGet"></a>

## <a name="configure-the-cache-clients"></a>Configuración de los clientes de caché
[!INCLUDE [redis-cache-configure](../../includes/redis-cache-configure-stackexchange-redis-nuget.md)]

Cuando el proyecto de cliente ya está configurado para el almacenamiento en caché, puede usar las técnicas descritas en las siguientes secciones para trabajar con la caché.

<a name="working-with-caches"></a>

## <a name="working-with-caches"></a>Trabajo con cachés
En esta sección se describe cómo realizar tareas comunes con el servicio de caché.

* [Conexión a la memoria caché][Connect to the cache]
* [Agregación y recuperación de objetos de la memoria caché][Add and retrieve objects from the cache]
* [Trabajar con objetos .NET en la memoria caché](#work-with-net-objects-in-the-cache)

<a name="connect-to-cache"></a>

## <a name="connect-to-the-cache"></a>Conexión a la memoria caché
Para trabajar con una caché mediante programación, necesita una referencia a la misma. Agregue lo siguiente a la parte superior de cualquier archivo del que desea usar el cliente StackExchange.Redis para acceder a Azure Redis Cache.

    using StackExchange.Redis;

> [!NOTE]
> El cliente StackExchange.Redis requiere .NET Framework 4 o superior.
> 
> 

La clase `ConnectionMultiplexer` administra la conexión con Azure Redis Cache. Esta clase se debe compartir y reutilizar a través de su aplicación cliente, no es necesario que se cree por cada operación. 

Para conectarse a una instancia de Azure Redis Cache y que se devuelva una instancia de `ConnectionMultiplexer`, llame al método estático `Connect` y pase el punto de conexión y la clave de caché. Use la clave generada desde Azure Portal como parámetro de contraseña.

    ConnectionMultiplexer connection = ConnectionMultiplexer.Connect("contoso5.redis.cache.windows.net,abortConnect=false,ssl=true,password=...");

> [!IMPORTANT]
> Advertencia: nunca almacene credenciales en el código fuente. Para que este ejemplo resulte sencillo, se muestra en código fuente. Consulte el artículo sobre [cómo funcionan las cadenas de aplicación y las cadenas de conexión][How Application Strings and Connection Strings Work] para más información sobre cómo almacenar credenciales.
> 
> 

Si no desea usar SSL, establezca `ssl=false` u omita el parámetro `ssl`.

> [!NOTE]
> El puerto no SSL está deshabilitado de forma predeterminada para las cachés nuevas. Para obtener instrucciones sobre cómo habilitar el puerto no SSL, consulte [Puertos de acceso](cache-configure.md#access-ports).
> 
> 

Un enfoque para compartir una instancia de `ConnectionMultiplexer` en su aplicación es tener una propiedad estática que devuelva una instancia conectada, como en el ejemplo siguiente. Este enfoque proporciona una manera segura de inicializar una sola instancia de `ConnectionMultiplexer` conectada. En estos ejemplos `abortConnect` está establecido en falso, lo que significa que la llamada se realizará correctamente incluso si no se establece ninguna conexión a Azure Redis Cache. Una de las características principales de `ConnectionMultiplexer` es que restaura automáticamente la conectividad a la caché una vez que el problema de red u otras causas se resuelven.

    private static Lazy<ConnectionMultiplexer> lazyConnection = new Lazy<ConnectionMultiplexer>(() =>
    {
        return ConnectionMultiplexer.Connect("contoso5.redis.cache.windows.net,abortConnect=false,ssl=true,password=...");
    });

    public static ConnectionMultiplexer Connection
    {
        get
        {
            return lazyConnection.Value;
        }
    }

Para más información sobre las opciones de configuración de conexión avanzadas, consulte el artículo sobre el [modelo de configuración de StackExchange.Redis][StackExchange.Redis configuration model].

[!INCLUDE [redis-cache-create](../../includes/redis-cache-access-keys.md)]

Una vez establecida la conexión, devuelva una referencia a la base de datos de Redis Cache llamando al método `ConnectionMultiplexer.GetDatabase`. El objeto devuelto desde el método `GetDatabase` es un objeto de paso a través ligero y no necesita almacenarse.

    // Connection refers to a property that returns a ConnectionMultiplexer
    // as shown in the previous example.
    IDatabase cache = Connection.GetDatabase();

    // Perform cache operations using the cache object...
    // Simple put of integral data types into the cache
    cache.StringSet("key1", "value");
    cache.StringSet("key2", 25);

    // Simple get of data types from the cache
    string key1 = cache.StringGet("key1");
    int key2 = (int)cache.StringGet("key2");

Las cachés de Azure Redis Cache tienen un número configurable de bases de datos (valor predeterminado de 16) que se pueden usar para separar de forma lógica los datos dentro de una caché de Redis. Para más información, consulte [What are Redis databases?](cache-faq.md#what-are-redis-databases) (¿Qué son las bases de datos de Redis?) y [Configuración predeterminada del servidor Redis](cache-configure.md#default-redis-server-configuration).

Ahora que sabe cómo conectarse a una instancia de Azure Redis Cache y devolver una referencia a la base de datos de caché, echemos un vistazo a cómo se trabaja con la caché.

<a name="add-object"></a>

## <a name="add-and-retrieve-objects-from-the-cache"></a>Agregación y recuperación de objetos de la memoria caché
Los elementos se pueden almacenar en una memoria caché y recuperarse de esta usando los métodos `StringSet` y `StringGet`.

    // If key1 exists, it is overwritten.
    cache.StringSet("key1", "value1");

    string value = cache.StringGet("key1");

Redis almacena la mayoría de los datos como cadenas Redis, pero estas cadenas pueden contener muchos tipos de datos, como por ejemplo datos binarios serializados, que se pueden usar cuando se almacenan objetos .NET en caché.

Cuando llame a `StringGet`, si el objeto existe, se devuelve y, si no existe, se devuelve `null`. Si se devuelve `null`, puede recuperar el valor del origen de datos que desee y almacenarlo en la caché para su uso posterior. Este patrón de uso se conoce como patrón cache-aside.

    string value = cache.StringGet("key1");
    if (value == null)
    {
        // The item keyed by "key1" is not in the cache. Obtain
        // it from the desired data source and add it to the cache.
        value = GetValueFromDataSource();

        cache.StringSet("key1", value);
    }

También puede usar `RedisValue`, tal y como se muestra en el ejemplo siguiente. `RedisValue` tiene operadores implícitos para trabajar con tipos de datos enteros y puede ser útil si `null` es un valor esperado para un elemento almacenado en caché.


    RedisValue value = cache.StringGet("key1");
    if (!value.HasValue)
    {
        value = GetValueFromDataSource();
        cache.StringSet("key1", value);
    }


Para especificar la expiración de un elemento en la memoria caché, use el parámetro `TimeSpan` de `StringSet`.

    cache.StringSet("key1", "value1", TimeSpan.FromMinutes(90));

## <a name="work-with-net-objects-in-the-cache"></a>Trabajar con objetos .NET en la memoria caché
Azure Redis Cache puede almacenar en caché objetos .NET así como tipos de datos primitivos, pero antes de poder almacenar en caché un objeto .NET, se debe serializar. La serialización del objeto .NET es responsabilidad del desarrollador de la aplicación, que tiene total flexibilidad a la hora de elegir el serializador.

Una manera sencilla para serializar objetos es usar los métodos de serialización `JsonConvert` en [Newtonsoft.Json.NET](https://www.nuget.org/packages/Newtonsoft.Json/8.0.1-beta1) y serializar a y desde JSON. En el ejemplo siguiente se muestra un get y set con una instancia de objeto `Employee` .

    class Employee
    {
        public int Id { get; set; }
        public string Name { get; set; }

        public Employee(int EmployeeId, string Name)
        {
            this.Id = EmployeeId;
            this.Name = Name;
        }
    }

    // Store to cache
    cache.StringSet("e25", JsonConvert.SerializeObject(new Employee(25, "Clayton Gragg")));

    // Retrieve from cache
    Employee e25 = JsonConvert.DeserializeObject<Employee>(cache.StringGet("e25"));

<a name="next-steps"></a>

## <a name="next-steps"></a>Pasos siguientes
Ahora que está familiarizado con los aspectos básicos, siga estos vínculos para obtener más información sobre Azure Redis Cache.

* Consulte los proveedores de ASP.NET para Azure Redis Cache.
  * [Proveedor de estado de sesión de Redis de Azure](cache-aspnet-session-state-provider.md)
  * [Proveedor de caché de resultados de ASP.NET de Azure Redis Cache](cache-aspnet-output-cache-provider.md)
* [Habilite los diagnósticos de cache](cache-how-to-monitor.md#enable-cache-diagnostics) para que pueda [supervisar](cache-how-to-monitor.md) el estado de la memoria caché. Puede ver las métricas en Azure Portal y también [descargarlas y revisarlas](https://github.com/rustd/RedisSamples/tree/master/CustomMonitoring) mediante las herramientas que prefiera.
* Compruebe la [documentación del cliente de caché StackExchange.Redis][StackExchange.Redis cache client documentation].
  * Se puede obtener acceso a Azure Redis Cache desde numerosos clientes de Redis y lenguajes de desarrollo. Para más información, consulte [http://redis.io/clients][http://redis.io/clients].
* Azure Redis Cache puede utilizarse también con herramientas como Redsmin y Redis Desktop Manager y servicios de terceros.
  * Para más información sobre Redsmin, consulte el artículo sobre [cómo recuperar una cadena de conexión de Azure Redis y usarla con Redsmin][How to retrieve an Azure Redis connection string and use it with Redsmin].
  * Acceda e inspeccione los datos en Azure Redis Cache con una GUI mediante [RedisDesktopManager](https://github.com/uglide/RedisDesktopManager).
* Consulte la documentación de [redis][redis], los [tipos de datos de redis][redis data types] y [una introducción de 15 minutos sobre los tipos de datos de Redis][a fifteen minute introduction to Redis data types].

<!-- INTRA-TOPIC LINKS -->
[Next Steps]: #next-steps
[Introduction to Azure Redis Cache (Video)]: #video
[What is Azure Redis Cache?]: #what-is
[Create an Azure Cache]: #create-cache
[Which type of caching is right for me?]: #choosing-cache
[Prepare Your Visual Studio Project to Use Azure Caching]: #prepare-vs
[Configure Your Application to Use Caching]: #configure-app
[Get Started with Azure Redis Cache]: #getting-started-cache-service
[Create the cache]: #create-cache
[Configure the cache]: #enable-caching
[Configure the cache clients]: #NuGet
[Working with Caches]: #working-with-caches
[Connect to the cache]: #connect-to-cache
[Add and retrieve objects from the cache]: #add-object
[Specify the expiration of an object in the cache]: #specify-expiration
[Store ASP.NET session state in the cache]: #store-session


<!-- IMAGES -->


[StackExchangeNuget]: ./media/cache-dotnet-how-to-use-azure-redis-cache/redis-cache-stackexchange-redis.png

[NuGetMenu]: ./media/cache-dotnet-how-to-use-azure-redis-cache/redis-cache-manage-nuget-menu.png

[CacheProperties]: ./media/cache-dotnet-how-to-use-azure-redis-cache/redis-cache-properties.png

[ManageKeys]: ./media/cache-dotnet-how-to-use-azure-redis-cache/redis-cache-manage-keys.png

[SessionStateNuGet]: ./media/cache-dotnet-how-to-use-azure-redis-cache/redis-cache-session-state-provider.png

[BrowseCaches]: ./media/cache-dotnet-how-to-use-azure-redis-cache/redis-cache-browse-caches.png

[Caches]: ./media/cache-dotnet-how-to-use-azure-redis-cache/redis-cache-caches.png







<!-- LINKS -->
[http://redis.io/clients]: http://redis.io/clients
[Develop in other languages for Azure Redis Cache]: http://msdn.microsoft.com/library/azure/dn690470.aspx
[How to retrieve an Azure Redis connection string and use it with Redsmin]: https://redsmin.uservoice.com/knowledgebase/articles/485711-how-to-connect-redsmin-to-azure-redis-cache
[Azure Redis Session State Provider]: http://go.microsoft.com/fwlink/?LinkId=398249
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
[Configure a cache in Azure Redis Cache]: http://msdn.microsoft.com/library/azure/dn793612.aspx

[StackExchange.Redis configuration model]: https://stackexchange.github.io/StackExchange.Redis/Configuration

[Work with .NET objects in the cache]: http://msdn.microsoft.com/library/dn690521.aspx#Objects


[NuGet Package Manager Installation]: http://go.microsoft.com/fwlink/?LinkId=240311
[Cache Pricing Details]: http://www.windowsazure.com/pricing/details/cache/
[Azure portal]: https://portal.azure.com/

[Overview of Azure Redis Cache]: http://go.microsoft.com/fwlink/?LinkId=320830
[Azure Redis Cache]: http://go.microsoft.com/fwlink/?LinkId=398247

[Migrate to Azure Redis Cache]: http://go.microsoft.com/fwlink/?LinkId=317347
[Azure Redis Cache Samples]: http://go.microsoft.com/fwlink/?LinkId=320840
[Using Resource groups to manage your Azure resources]: ../azure-resource-manager/resource-group-overview.md

[StackExchange.Redis]: http://github.com/StackExchange/StackExchange.Redis
[StackExchange.Redis cache client documentation]: http://github.com/StackExchange/StackExchange.Redis#documentation

[Redis]: http://redis.io/documentation
[Redis data types]: http://redis.io/topics/data-types
[a fifteen minute introduction to Redis data types]: http://redis.io/topics/data-types-intro

[How Application Strings and Connection Strings Work]: http://azure.microsoft.com/blog/2013/07/17/windows-azure-web-sites-how-application-strings-and-connection-strings-work/


