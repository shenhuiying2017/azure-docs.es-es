<properties pageTitle="How to Use Azure Redis Cache" metaKeywords="" description="Learn how to create a use a cache in Azure Redis Cache" metaCanonical="" services="" documentationCenter="API Management" title="How to Use Azure Redis Cache" authors="sdanie" solutions="" manager="" editor="" />

<tags ms.service="cache" ms.workload="tbd" ms.tgt_pltfrm="cache-redis" ms.devlang="dotnet" ms.topic="article" ms.date="01/01/1900" ms.author="sdanie"></tags>

# Uso de Caché en Redis de Azure

En esta guía se explica cómo comenzar a usar
**Caché en Redis de Azure (vista previa)**. Los ejemplos se escriben en código C# y usan la API .NET. Entre los escenarios tratados se incluyen **la creación y configuración de una caché**, **la configuración de clientes de caché**, **la incorporación de objetos a la memoria caché y la eliminación de aquellos de esta** y el **almacenamiento del estado de sesión ASP.NET en la memoria caché**. Para obtener más información acerca del uso de Caché en Redis de Azure, consulte la sección [Pasos siguientes][Pasos siguientes].

## Tabla de contenido

-   [¿Qué es Caché en Redis de Azure?][¿Qué es Caché en Redis de Azure?]
-   [Introducción a Caché en Redis de Azure][Introducción a Caché en Redis de Azure]
    -   [Creación de la caché][Creación de la caché]
    -   [Configuración de los clientes de caché][Configuración de los clientes de caché]
-   [Uso de cachés][Uso de cachés]
    -   [Conexión a la memoria caché][Conexión a la memoria caché]
    -   [Agregación y recuperación de objetos de la memoria caché][Agregación y recuperación de objetos de la memoria caché]
    -   [Especificación de la expiración de un objeto en la memoria caché][Especificación de la expiración de un objeto en la memoria caché]
    -   [Almacenamiento del estado de sesión ASP.NET en la memoria caché][Almacenamiento del estado de sesión ASP.NET en la memoria caché]
-   [Pasos siguientes][Pasos siguientes]

<a name="what-is"></a>

## ¿Qué es Caché en Redis de Azure?

Caché en Redis de Microsoft Azure, actualmente en versión de vista previa, se basa en la conocida Caché en Redis de código fuente abierto. Le proporciona acceso a una caché en Redis segura y dedicada, administrada por Microsoft. Una caché creada usando Caché en Redis de Azure es accesible desde cualquier aplicación dentro de Microsoft Azure.

Caché en Redis de Microsoft Azure está disponible en dos niveles:

-   **Básico** – Nodo único. Varios tamaños de hasta 26 GB.
-   **Estándar** – Maestro/Esclavo de dos nodos. Varios tamaños de hasta 26 GB.

Estos niveles difieren en las características y el precio. Las características se tratan más adelante en esta guía; por otro lado, para obtener más información acerca de los precios consulte [Detalles de precios de caché][Detalles de precios de caché].

En esta guía se ofrece una introducción sobre cómo comenzar a usar Caché en Redis de Azure. Para obtener información más detallada acerca de estas características, que van más allá del ámbito de esta guía de introducción, consulte [Introducción a Caché en Redis de Azure][1].

<a name="getting-started-cache-service"></a>

## Introducción a Caché en Redis de Azure

Ponerse en marcha con Caché en Redis de Azure es fácil. En primer lugar, tiene que aprovisionar y configurar una caché. A continuación, debe configurar los clientes de caché para que puedan obtener acceso a la caché. Una vez que los clientes de caché estén configurados, ya puede empezar a trabajar con ellos.

-   [Creación de la caché][Creación de la caché]
-   [Configuración de la caché][Configuración de la caché]
-   [Configuración de los clientes de caché][Configuración de los clientes de caché]

<a name="create-cache"></a>

## Creación de una caché

Para crear una caché, primero inicie sesión en el portal de vista previa de administración de Azure y haga clic en **Nuevo**, **Caché en Redis (vista previa)**.

![New cache][New cache]

En el cuadro **Nueva caché en Redis (vista previa)**, especifique la configuración que desee para la memoria caché.

![Create cache][Create cache]

En **Nombre DNS**, escriba un nombre de subdominio para usar el extremo de caché. El nombre debe tener entre seis y veinte caracteres, solo puede contener números y letras minúsculas, y debe comenzar por una letra.

Use **Nivel de precios** para seleccionar el tamaño y características de caché que desee. Caché en Redis está disponible en los dos siguientes niveles.

-   **Básico** - Modo único, varios tamaños de hasta 26 GB.
-   **Estándar** - Maestro/Esclavo de dos nodos, contrato de nivel de servicio del 99,9 % (posterior a vista previa) y varios tamaños de hasta 26 GB.

En **Suscripción**, seleccione la suscripción de Azure que desee utilizar para la memoria caché.

> Si su cuenta solo dispone de una suscripción, esta se seleccionará automáticamente y no aparecerá la lista desplegable de suscripciones.

En **Grupo de recursos**, seleccione o cree un grupo de recursos para su caché.

> Para obtener más información, vea [Uso de grupos de recursos para administrar su recursos de Azure][Uso de grupos de recursos para administrar su recursos de Azure].

Use **Geolocalización** para especificar la ubicación geográfica en la que se hospeda su caché. Para optimizar el rendimiento, Microsoft recomienda encarecidamente que cree la memoria caché en la misma región que la aplicación cliente de caché.

Una vez que las nuevas opciones de caché estén configuradas, haga clic en **Crear**. La creación de la caché puede tardar unos minutos. Para comprobar el estado, puede supervisar el progreso en el panel de inicio. Una vez que se cree la memoria caché, esta presentará el estado **En ejecución** y estará lista para usarse con la configuración predeterminada.

![Cache created][Cache created]

Una vez creada su caché, podrá acceder a ella desde el cuadro **Examinar**.

![Browse blade][Browse blade]

Haga clic en **Cachés** para ver sus cachés.

![Caches][Caches]

<a name="NuGet"></a>

## Configuración de los clientes de caché

Una caché creada usando Caché en Redis de Azure es accesible desde cualquier aplicación de Azure. Las aplicaciones .NET desarrolladas en Visual Studio pueden usar el cliente de caché **StackExchange.Redis**, que se puede configurar usando un paquete NuGet que simplifica la configuración de aplicaciones cliente de caché.

> Para obtener más información, vea la página Github [StackExchange.Redis][StackExchange.Redis] y la [documentación del cliente de caché StackExchange.Redis][documentación del cliente de caché StackExchange.Redis].

Para configurar una aplicación cliente en Visual Studio usando el paquete NuGet StackExchange.Redis, haga clic con el botón secundario en el proyecto en el **Explorador de soluciones** y elija **Administrar paquetes de NuGet**.

![Manage NuGet packages][Manage NuGet packages]

Escriba **StackExchange.Redis** o **StackExchange.Redis.StrongName** en el cuadro de texto **Buscar en línea**, seleccione la versión que desee en los resultados y haga clic en **Instalar**.

> Si prefiere usar una versión con nombre seguro de la biblioteca de cliente **StackExchange.Redis**, elija **StackExchange.Redis.StrongName**; de lo contrario, elija **StackExchange.Redis**.

![StackExchange.Redis NuGet package][StackExchange.Redis NuGet package]

El paquete de NuGet se descarga y agrega las referencias de ensamblado requeridas para que la aplicación cliente acceda a Caché en Redis de Azure con el cliente de caché StackExchange.Redis.

Cuando el proyecto de cliente ya está configurado para el almacenamiento en caché, puede usar las técnicas descritas en las siguientes secciones para trabajar con la caché.

<a name="working-with-caches"></a>

## Trabajo con cachés

En esta sección se describe cómo realizar tareas comunes con el servicio de caché.

-   [Conexión a la memoria caché][Conexión a la memoria caché]
-   [Agregación y recuperación de objetos de la memoria caché][Agregación y recuperación de objetos de la memoria caché]
-   [Almacenamiento del estado de sesión ASP.NET en la memoria caché][Almacenamiento del estado de sesión ASP.NET en la memoria caché]

<a name="connect-to-cache"></a>

## Conexión a la memoria caché

Para trabajar con una caché mediante programación, necesita una referencia a la misma. Agregue lo siguiente a la parte superior de cualquier archivo del que desea usar el cliente StackExchange.Redis para acceder a una Caché en Redis de Azure:

    using StackExchange.Redis;

> El cliente StackExchange.Redis requiere .NET Framework 4 o superior.

La clase `ConnectionMultiplexer` administra la conexión con Caché en Redis de Azure. Esta clase está diseñada para compartirse y reusarse a través de su aplicación cliente y no necesita crearse basándose en operación.

Para conectarse a una Caché en Redis de Azure y que se devuelva una instancia de `ConnectionMultiplexer`, llame al método `Connect` estático y pase el extremo y la clave de caché como en el siguiente ejemplo. Utilice la clave de Azure generada desde el portal de servicio como el parámetro de contraseña.

    ConnectionMultiplexer connection = ConnectionMultiplexer.Connect("contoso5.redis.cache.windows.net,ssl=true,password=...");

> Advertencia: nunca almacene credenciales en código fuente. Para que este ejemplo resulte sencillo, se muestra en código fuente. Vea [Sitios Web Windows Azure: Funcionamiento de las cadenas de aplicación y de las cadenas de conexión][Sitios Web Windows Azure: Funcionamiento de las cadenas de aplicación y de las cadenas de conexión] para obtener información sobre cómo almacenar credenciales.

Si no desea usar SSL, establezca `ssl=false` o simplemente pase el extremo y la clave.

    connection = ConnectionMultiplexer.Connect("contoso5.redis.cache.windows.net,password=...");

> Para obtener más información sobre las opciones de configuración de conexión avanzadas, vea el [modelo de configuración de StackExchange.Redis][modelo de configuración de StackExchange.Redis].

El extremo y las claves de caché se pueden obtener del cuadro del portal de vista previa de administración de Azure para su instancia de caché.

![Cache properties][Cache properties]

![Manage keys][Manage keys]

Una vez establecida la conexión, devuelva una referencia a la base de datos de caché Redis llamando al método `ConnectionMultiplexer.GetDatabase`.

    // connection referes to a previously configured ConnectionMultiplexer
    IDatabase cache = connection.GetDatabase();

> El objeto devuelto desde el método `GetDatabase` es un objeto de paso a través ligero y no necesita almacenarse.

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

Los elementos se pueden almacenar en una caché y recuperarse de ella mediante los métodos `StringSet` y `StringGet`.

    // If key1 exists, it is overwritten.
    cache.StringSet("key1", "value1");

    string value = cache.StringGet("key1");

> Redis almacena la mayoría de los datos como cadenas Redis, pero estas cadenas pueden contener muchos tipos de datos, como por ejemplo datos binarios serializados, que se pueden usar cuando se almacenan objetos .NET en caché.

Cuando se llama a `StringGet`, si el objeto existe, este se devuelve, y si no existe, se devuelve null. En este caso, puede recuperar el valor desde el origen de datos que desee y almacenarlo en la memoria caché para su uso posterior. Esto se conoce como patrón cache-aside.

    string value = cache.StringGet("key1");
    if (value == null)
    {
        // The item keyed by "key1" is not in the cache. Obtain
        // it from the desired data source and add it to the cache.
        value = GetValueFromDataSource();

        cache.StringSet("key1", value);
    }

> Caché en Redis de Azure puede almacenar en caché objetos .NET así como tipos de datos primitivos, pero antes de poder almacenar un objeto .NET en caché, se debe serializar. Esta es la responsabilidad del desarrollador de la aplicación, que tiene total flexibilidad a la hora de elegir el serializador. Para obtener más información, vea [Trabajar con objetos .NET en la memoria caché][Trabajar con objetos .NET en la memoria caché].

<a name="specify-expiration"></a>

## Especificación de la expiración de un elemento en la memoria caché

Para especificar la expiración de un elemento en la memoria caché, use el parámetro `TimeSpan` de `StringSet`.

    cache.StringSet("key1", "value1", TimeSpan.FromMinutes(90));

<a name="store-session"></a>

## Almacenamiento del estado de sesión ASP.NET en la memoria caché

Caché en Redis de Azure proporciona un proveedor de estado de sesión que puede usar para almacenar su estado de sesión en una caché en lugar de en memoria o en una base de datos de SQL Server. Para usar el proveedor de estado de sesión de caché, configure primero su caché y, después, configure su aplicación ASP.NET para caché mediante el paquete NuGet de estado de sesión de caché Redis.

Para configurar una aplicación cliente en Visual Studio usando el paquete NuGet de estado de sesión de caché Redis, haga clic con el botón secundario en el proyecto en el **Explorador de soluciones** y elija **Administrar paquetes de NuGet**.

![Manage NuGet packages][Manage NuGet packages]

Escriba **RedisSessionStateProvider** en el cuadro de texto **Buscar en línea**, selecciónelo en los resultados y haga clic en **Instalar**.

![Redis Cache Session State NuGet Package][Redis Cache Session State NuGet Package]

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
          />
        -->
        <add name="MySessionStateStore" type="Microsoft.Web.Redis.RedisSessionStateProvider" host="contoso5.redis.cache.windows.net" 
        accessKey="..." ssl="false" />
      </providers>
    </sessionState>

> Tenga en cuenta que la unidad del tiempo de espera de reintentos es el milisegundo.

Asegúrese de comentar el proveedor del estado de sesión **InProc** estándar.

    <!-- <sessionState mode="InProc" customProvider="DefaultSessionProvider">
      <providers>
        <add name="DefaultSessionProvider" type="System.Web.Providers.DefaultSessionStateProvider, System.Web.Providers, Version=1.0.0.0, Culture=neutral, PublicKeyToken=31bf3856ad364e35" connectionStringName="DefaultConnection" />
      </providers>
    </sessionState> -->

Para obtener más información sobre la configuración y uso del proveedor del estado de sesión de Redis de Azure, vea [Proveedor del estado de sesión de Redis de Azure][Proveedor del estado de sesión de Redis de Azure].

<a name="next-steps"></a>

## Pasos siguientes

Ahora que está familiarizado con los aspectos básicos de Caché en Redis de Azure, utilice estos enlaces para obtener más información sobre cómo realizar tareas de almacenamiento en caché más complejas.

-   Para obtener más información acerca del cliente StackExchange.Redis: [documentación del cliente de caché StackExchange.Redis][documentación del cliente de caché StackExchange.Redis]
-   Vea la documentación de [redis][redis] y lea la información sobre los [tipos de datos Redis][tipos de datos Redis] y [una introducción de quince minutos para tipos de datos Redis][una introducción de quince minutos para tipos de datos Redis].
-   Consulte la referencia de MSDN: [Caché en Redis de Azure][Caché en Redis de Azure]

<!-- INTRA-TOPIC LINKS -->
<!-- IMAGES -->
<!-- LINKS -->

  [Pasos siguientes]: #next-steps
  [¿Qué es Caché en Redis de Azure?]: #what-is
  [Introducción a Caché en Redis de Azure]: #getting-started-cache-service
  [Creación de la caché]: #create-cache
  [Configuración de los clientes de caché]: #NuGet
  [Uso de cachés]: #working-with-caches
  [Conexión a la memoria caché]: #connect-to-cache
  [Agregación y recuperación de objetos de la memoria caché]: #add-object
  [Especificación de la expiración de un objeto en la memoria caché]: #specify-expiration
  [Almacenamiento del estado de sesión ASP.NET en la memoria caché]: #store-session
  [Detalles de precios de caché]: http://www.windowsazure.com/en-us/pricing/details/cache/
  [1]: http://go.microsoft.com/fwlink/?LinkId=320830
  [Configuración de la caché]: #enable-caching
  [New cache]: ./media/cache-dotnet-how-to-use-azure-redis-cache/redis-cache-new-cache-menu.png
  [Create cache]: ./media/cache-dotnet-how-to-use-azure-redis-cache/redis-cache-cache-create.png
  [Uso de grupos de recursos para administrar su recursos de Azure]: http://azure.microsoft.com/es-es/documentation/articles/azure-preview-portal-using-resource-groups/
  [Cache created]: ./media/cache-dotnet-how-to-use-azure-redis-cache/redis-cache-cache-created.png
  [Browse blade]: ./media/cache-dotnet-how-to-use-azure-redis-cache/redis-cache-browse-caches.png
  [Caches]: ./media/cache-dotnet-how-to-use-azure-redis-cache/redis-cache-caches.png
  [StackExchange.Redis]: http://github.com/StackExchange/StackExchange.Redis
  [documentación del cliente de caché StackExchange.Redis]: http://github.com/StackExchange/StackExchange.Redis#documentation
  [Manage NuGet packages]: ./media/cache-dotnet-how-to-use-azure-redis-cache/redis-cache-manage-nuget-menu.png
  [StackExchange.Redis NuGet package]: ./media/cache-dotnet-how-to-use-azure-redis-cache/redis-cache-stackexchange-redis.png
  [Sitios Web Windows Azure: Funcionamiento de las cadenas de aplicación y de las cadenas de conexión]: http://azure.microsoft.com/blog/2013/07/17/windows-azure-web-sites-how-application-strings-and-connection-strings-work/
  [modelo de configuración de StackExchange.Redis]: http://github.com/StackExchange/StackExchange.Redis/blob/master/Docs/Configuration.md
  [Cache properties]: ./media/cache-dotnet-how-to-use-azure-redis-cache/redis-cache-properties.png
  [Manage keys]: ./media/cache-dotnet-how-to-use-azure-redis-cache/redis-cache-manage-keys.png
  [Trabajar con objetos .NET en la memoria caché]: http://msdn.microsoft.com/en-us/library/dn690521.aspx#Objects
  [Redis Cache Session State NuGet Package]: ./media/cache-dotnet-how-to-use-azure-redis-cache/redis-cache-session-state-provider.png
  [Proveedor del estado de sesión de Redis de Azure]: http://go.microsoft.com/fwlink/?LinkId=398249
  [redis]: http://redis.io/documentation
  [tipos de datos Redis]: http://redis.io/topics/data-types
  [una introducción de quince minutos para tipos de datos Redis]: http://redis.io/topics/data-types-intro
  [Caché en Redis de Azure]: http://go.microsoft.com/fwlink/?LinkId=398247
