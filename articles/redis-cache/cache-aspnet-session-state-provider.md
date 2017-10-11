---
title: "Proveedor de estado de sesión ASP.NET para Cache | Microsoft Docs"
description: "Aprenda a almacenar el estado de sesión de ASP.NET con Caché en Redis de Azure"
services: redis-cache
documentationcenter: na
author: steved0x
manager: douge
editor: tysonn
ms.assetid: 192f384c-836a-479a-bb65-8c3e6d6522bb
ms.service: cache
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: cache-redis
ms.workload: tbd
ms.date: 05/01/2017
ms.author: sdanie
ms.openlocfilehash: 0f3683939ac9646565a0669e19b4c82811d621fc
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 07/11/2017
---
# <a name="aspnet-session-state-provider-for-azure-redis-cache"></a>Proveedor de estado de sesión de ASP.NET para Caché en Redis de Azure
Caché en Redis de Azure proporciona un proveedor de estado de sesión que puede usar para almacenar su estado de sesión en una caché en lugar de en memoria o en una base de datos de SQL Server. Para usar el proveedor de estado de sesión de caché, configure primero su caché y, después, configure su aplicación ASP.NET para caché mediante el paquete NuGet de estado de sesión de caché en Redis.

No suele ser práctico evitar almacenar algún tipo de estado para una sesión de usuario en una aplicación de nube real, pero algunos enfoques afectan al rendimiento y a la escalabilidad más que otros  Si tiene que almacenar el estado, la mejor solución es que la cantidad sea reducida y que se almacene en cookies. Si esto no es factible, entonces lo mejor es usar el estado de sesión de ASP.NET con un proveedor de caché distribuida en memoria. La peor solución desde el punto de vista del rendimiento y la escalabilidad es usar un proveedor de estado de sesión con copia de seguridad de base de datos. En este tema se ofrecen instrucciones sobre cómo usar el proveedor de estado de sesión de ASP.NET para Azure Redis Cache. Para información sobre otras opciones de estado de sesión, consulte [Opciones de estado de sesión ASP.NET](#aspnet-session-state-options).

## <a name="store-aspnet-session-state-in-the-cache"></a>Almacenamiento del estado de sesión ASP.NET en la memoria caché
Para configurar una aplicación cliente en Visual Studio usando el paquete NuGet de estado de sesiones de Redis Cache, haga clic en **Administrar paquetes NuGet**, **Consola del administrador de paquetes** en el menú **Herramientas**.

Ejecute el siguiente comando desde la ventana `Package Manager Console`.
    
```
Install-Package Microsoft.Web.RedisSessionStateProvider
```

> [!IMPORTANT]
> Si usa la característica de clústeres del nivel premium, debe usar [RedisSessionStateProvider](https://www.nuget.org/packages/Microsoft.Web.RedisSessionStateProvider) 2.0.1 o superior; de lo contrario, se produce una excepción. El cambio a la versión 2.0.1 es un cambio importante. Para obtener más información, consulte [v2.0.0 Breaking Change Details](https://github.com/Azure/aspnet-redis-providers/wiki/v2.0.0-Breaking-Change-Details) (Detalles sobre cambios importantes de la versión 2.0.0). En el momento de la actualización de este artículo, la versión actual de este paquete es 2.2.3.
> 
> 

El paquete NuGet de proveedor de estado de sesión de Redis tiene una dependencia en el paquete StackExchange.Redis.StrongName. Si el paquete StackExchange.Redis.StrongName no existe en el proyecto, se instalará.

>[!NOTE]
>Tenga en cuenta que, además del paquete StackExchange.Redis.StrongName con nombre seguro, también está la versión con nombre no seguro de StackExchange.Redis. Si su proyecto está utilizando la versión de StackExchange.Redis con nombre no seguro deberá desinstalarla, ya que de lo contrario se producirán conflictos de nomenclatura en el proyecto. Para obtener más información sobre estos paquetes, consulte [Configuración de los clientes de la caché de .NET](cache-dotnet-how-to-use-azure-redis-cache.md#configure-the-cache-clients).
>
>

El paquete NuGet descarga y agrega las referencias de ensamblado necesarias y agrega la siguiente sección al archivo web.config. Esta sección contiene la configuración necesaria para que la aplicación ASP.NET use el proveedor de estado de sesión de memoria caché de Redis.

```xml
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
    <add name="MySessionStateStore" type="Microsoft.Web.Redis.RedisSessionStateProvider" host="127.0.0.1" accessKey="" ssl="false"/>
    </providers>
</sessionState>
```

En la sección comentada se proporciona un ejemplo de los atributos y la configuración de ejemplo de cada uno.

Configure los atributos con los valores de la hoja de la caché en el Portal de Microsoft Azure y configure los demás valores según prefiera. Para obtener instrucciones sobre cómo acceder a las propiedades de caché, consulte [Configuración de la caché en Redis](cache-configure.md#configure-redis-cache-settings).

* **host** : especifique el punto de conexión de la caché.
* **puerto** : use el puerto no SSL o SSL, según la configuración de SSL.
* **accessKey** : use la clave primaria o secundaria para la caché.
* **ssl** : true si desea proteger las comunicaciones de la caché o el cliente con SLS; de lo contrario, false. Asegúrese de especificar el puerto correcto.
  * El puerto no SSL está deshabilitado de forma predeterminada para las cachés nuevas. Especifique true en este valor para usar el puerto SSL. Para más información sobre cómo habilitar el puerto no SSL, consulte la sección [Puertos de acceso](cache-configure.md#access-ports) del tema de [Configuración de caché](cache-configure.md).
* **throwOnError** : true si quiere que se produzca una excepción en caso de error; false si quiere que la operación devuelva un error silencioso. Para comprobar si hay un error, revise la propiedad estática Microsoft.Web.Redis.RedisSessionStateProvider.LastException. El valor predeterminado es true.
* **retryTimeoutInMilliseconds** : durante este intervalo, especificado en milisegundos, se reintenta realizar las operaciones que generan errores. El primer reintento se produce transcurridos 20 milisegundos y, después, se reintenta cada segundo hasta que el intervalo retryTimeoutInMilliseconds expira. Inmediatamente después de este intervalo, la operación se reintenta una última vez. Si aun así no funciona, se devuelve una excepción al autor de la llamada, en función de la configuración de throwOnError. El valor predeterminado es 0, lo que significa que no hay reintentos.
* **databaseId** : especifica qué base de datos se va a usar para los datos de salida de la memoria caché. Si no se especifica, se usa el valor predeterminado de 0.
* **applicationName**: las claves se almacenan en Redis como `{<Application Name>_<Session ID>}_Data`. Este esquema de nomenclatura permite que varias aplicaciones compartan la misma instancia de Redis. Este parámetro es opcional y, si no se especifica, se usa un valor predeterminado.
* **connectionTimeoutInMilliseconds** : esta opción le permite invalidar la configuración de connectTimeout en el cliente de StackExchange.Redis. Si no se especifica, se usa el valor predeterminado de connectTimeout, que es 5000. Para obtener más información, consulte el [modelo de configuración de StackExchange.Redis](http://go.microsoft.com/fwlink/?LinkId=398705).
* **operationTimeoutInMilliseconds** : esta opción le permite invalidar la configuración de syncTimeout en el cliente de StackExchange.Redis. Si no se especifica, se usa el valor predeterminado de syncTimeout, que es 1000. Para obtener más información, consulte el [modelo de configuración de StackExchange.Redis](http://go.microsoft.com/fwlink/?LinkId=398705).

Para obtener más información sobre estas propiedades, consulte el anuncio de entrada de blog original en [Announcing ASP.NET Session State Provider for Redis](http://blogs.msdn.com/b/webdev/archive/2014/05/12/announcing-asp-net-session-state-provider-for-redis-preview-release.aspx)(Anuncio de proveedor de estado de sesión de ASP.NET para Redis).

No olvide comentar la sección del proveedor de estado de sesión InProc estándar en el archivo web.config.

```xml
<!-- <sessionState mode="InProc"
     customProvider="DefaultSessionProvider">
     <providers>
        <add name="DefaultSessionProvider"
              type="System.Web.Providers.DefaultSessionStateProvider,
                    System.Web.Providers, Version=1.0.0.0, Culture=neutral,
                    PublicKeyToken=31bf3856ad364e35"
              connectionStringName="DefaultConnection" />
      </providers>
</sessionState> -->
```

Una vez realizados estos pasos, la aplicación está configurada para usar el proveedor de estado de sesión de Caché en Redis. Cuando se usa el estado de sesión en la aplicación, se almacena en una instancia de Azure Redis Cache.

> [!IMPORTANT]
> Los datos almacenados en la memoria caché deben ser serializables, a diferencia de los datos que se puedan almacenar en el proveedor de estado de sesión de ASP.NET en memoria predeterminado. Al usar el proveedor de estado de sesión para Redis, asegúrese de que los tipos de datos que se almacenan en el estado de sesión sean serializables.
> 
> 

## <a name="aspnet-session-state-options"></a>Opciones de estado de sesión de ASP.NET
* Proveedor de estado de sesión en memoria: este proveedor almacena el estado de sesión en la memoria. La ventaja de usar este proveedor es que es sencillo y rápido. Sin embargo, con el proveedor en memoria no se pueden escalar las aplicaciones web, ya que no se distribuye.
* Proveedor de estado de sesión de SQL Server: este proveedor almacena el estado de sesión en SQL Server. Use este proveedor si desea conservar el estado de sesión en un almacenamiento persistente. Puede escalar la aplicación web, pero el uso de SQL Server para la sesión afectará al rendimiento de dicha aplicación.
* Proveedor de estado de sesión en memoria distribuida como proveedor de estado de sesión de Caché en Redis: este proveedor le ofrece lo mejor de ambos mundos. La aplicación web puede tener un proveedor de estado de sesión sencillo, rápido y escalable. Como este proveedor almacena el estado de sesión en una memoria caché, la aplicación tiene que tomar en consideración todas las características asociadas al comunicarse con una memoria caché en memoria distribuida, como los errores de red transitorios. Para ver los procedimientos recomendados para el uso de la memoria caché, consulte [Instrucciones de almacenamiento en caché](../best-practices-caching.md) en las [Instrucciones de implementación y diseño de aplicaciones en la nube de Azure](https://github.com/mspnp/azure-guidance) de los modelos y prácticas de Microsoft.

Para obtener más información sobre el estado de sesión y otros procedimientos recomendados, consulte [Procedimientos recomendados de desarrollo web (compilación de aplicaciones en la nube reales con Azure)](http://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/web-development-best-practices).

## <a name="next-steps"></a>Pasos siguientes
Consulte [Proveedor de caché de resultados de ASP.NET para Caché en Redis de Azure](cache-aspnet-output-cache-provider.md).

