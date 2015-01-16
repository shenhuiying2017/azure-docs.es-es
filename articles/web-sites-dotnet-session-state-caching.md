<properties urlDisplayName="index" pageTitle="Uso del estado de sesión de ASP.NET con Sitios web Azure" metaKeywords="azure cache service session state" description="Obtenga información acerca de cómo usar el servicio de caché de Azure para admitir la caché de estado de sesión de ASP.NET." metaCanonical="" services="cache" documentationCenter=".NET" title="How to Use ASP.NET Session State with Azure Websites" authors="riande"  solutions="" manager="wpickett" editor="mollybos"  />

<tags ms.service="web-sites" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="dotnet" ms.topic="article" ms.date="10/13/2014" ms.author="riande" />


# Uso del estado de la sesión de ASP.NET con Sitios web Azure

*Por [Rick Anderson](https://twitter.com/RickAndMSFT). Actualizado el 1 de julio de 2014.*

En este tema se explica cómo utilizar el servicio Caché en Redis de Azure (vista previa) para el estado de sesión.

Si su aplicación de ASP.NET utiliza el estado de sesión, deberá configurar un proveedor externo del estado de sesión (bien un servicio Caché en Redis o un proveedor de estado de sesión del SQL Server). Si utiliza el estado de sesión y no utiliza un proveedor externo, deberá limitar su aplicación web a una instancia. El servicio Caché en Redis es el más sencillo y rápido que se puede habilitar.

Los pasos básicos para utilizar el servicio de caché (vista previa) para el almacenamiento en caché de estado de sesión incluyen:

* [Creación de la caché](#createcache)
* [Agregue el paquete NuGet RedisSessionStateProvider a su aplicación web.](#configureproject)
* [Modificación del archivo web.config](#configurewebconfig)
* [Uso del objeto Session para almacenar y recuperar elementos almacenados en caché.](#usesessionobject)

<h2><a id="createcache"></a>Creación de la memoria caché</h2>
Siga [estas instrucciones](http://azure.microsoft.com/es-es/documentation/articles/cache-dotnet-how-to-use-azure-redis-cache/#create-cache) para crear la caché.

<h2><a id="configureproject"></a>Incorporación del paquete NuGet RedisSessionStateProvider a su aplicación web</h2>
Instale el paquete NuGet 'RedisSessionStateProvider'.  Utilice el comando siguiente para realizar la instalación desde la consola del administrador de paquetes (**Herramientas** > **Administrador de paquetes NuGet** > **Consola del Administrador de paquetes**):

  `PM> Install-Package RedisSessionStateProvider -IncludePrerelease`
  
Para instalar desde **Herramientas** > **Administrador de paquetes NuGet** > **Administrar paquetes NugGet para la solución**, busque RedisSessionStateProvider y asegúrese de marcar **Incluir versión preliminar**.

Para obtener más información vea la [página NuGet RedisSessionStateProvider](http://www.nuget.org/packages/Microsoft.Web.RedisSessionStateProvider/ ) y [Configuración de los clientes de caché](http://azure.microsoft.com/es-es/documentation/articles/cache-dotnet-how-to-use-azure-redis-cache/#NuGet).

<h2><a id="configurewebconfig"></a>Modificación del archivo web.config</h2>
Además de hacer referencias de ensamblado para la memoria caché, el paquete NuGet agrega entradas auxiliares en el archivo *web.config*. 

1. Abra el archivo *web.config* y busque el elemento **sessionState**.

1. Escriba los valores para `host`, `accessKey`, `port` (el puerto SSL debe ser 6380) y establezca `SSL` en `true`. Estos valores se pueden obtener del cuadro del portal de vista previa de administración de Azure para su instancia de caché. Para obtener más información, vea [Conexión con la memoria caché](http://azure.microsoft.com/es-es/documentation/articles/cache-dotnet-how-to-use-azure-redis-cache/#connect-to-cache).
La revisión siguiente muestra los cambios en el archivo *web.config*.


  <pre class="prettyprint">  
    <system.web>
    <customErrors mode="Off" />
    <authentication mode="None" />
    <compilation debug="true" targetFramework="4.5" />
    <httpRuntime targetFramework="4.5" />
  <sessionState mode="Custom" customProvider="RedisSessionProvider">
      <providers>  
          <!--<add name="RedisSessionProvider" 
            host = "127.0.0.1" [String]
            port = "" [number]
            accessKey = "" [String]
            ssl = "false" [true|false]
            throwOnError = "true" [true|false]
            retryTimeoutInMilliseconds = "0" [number]
            databaseId = "0" [number]
            applicationName = "" [String]
          />-->
         <add name="RedisSessionProvider" 
              type="Microsoft.Web.Redis.RedisSessionStateProvider" 
              <mark>port="6380"
              host="movie2.redis.cache.windows.net" 
              accessKey="m7PNV60CrvKpLqMUxosC3dSe6kx9nQ6jP5del8TmADk=" 
              ssl="true"</mark> />
      <!--<add name="MySessionStateStore" type="Microsoft.Web.Redis.RedisSessionStateProvider" host="127.0.0.1" accessKey="" ssl="false" />-->
      </providers>
    </sessionState>
  </system.web></pre>


<h2><a id="usesessionobject"></a>Uso del objeto Session en el código</h2>
El paso final es comenzar a utilizar el objeto Session en su código ASP.NET. Agregue objetos al estado de sesión con el método **Session.Add**. Este método utiliza pares clave-valor para almacenar elementos en la caché de estado de sesión.

    string strValue = "yourvalue";
	Session.Add("yourkey", strValue);

El siguiente código recupera este valor desde el estado de sesión.

    object objValue = Session["yourkey"];
    if (objValue != null)
       strValue = (string)obj;	

También puede usar el servicio Caché en Redis para almacenar objetos en la memoria caché en su aplicación web. Para obtener más información, consulte [Aplicación de películas MVC con Caché en Redis de Azure en 15 minutos](http://azure.microsoft.com/blog/2014/06/05/mvc-movie-app-with-azure-redis-cache-in-15-minutes/).
Para obtener más detalles acerca de cómo utilizar el estado de sesión de ASP.NET, consulte [Información general sobre el estado de sesión de ASP.NET][].

  
  
  [instala la más reciente]: http://www.windowsazure.com/es-es/downloads/?sdk=net  
  [Información general del estado de sesión de ASP.NET]: http://msdn.microsoft.com/es-es/library/ms178581.aspx

  [NewIcon]: ./media/web-sites-dotnet-session-state-caching/CacheScreenshot_NewButton.png
  [NewCacheDialog]: ./media/web-sites-dotnet-session-state-caching/CachingScreenshot_CreateOptions.png
  [CacheIcon]: ./media/web-sites-dotnet-session-state-caching/CachingScreenshot_CacheIcon.png
  [NuGetDialog]: ./media/web-sites-dotnet-session-state-caching/CachingScreenshot_NuGet.png
  [OutputConfig]: ./media/web-sites-dotnet-session-state-caching/CachingScreenshot_OC_WebConfig.png
  [CacheConfig]: ./media/web-sites-dotnet-session-state-caching/CachingScreenshot_CacheConfig.png
  [EndpointURL]: ./media/web-sites-dotnet-session-state-caching/CachingScreenshot_EndpointURL.png
  [ManageKeys]: ./media/web-sites-dotnet-session-state-caching/CachingScreenshot_ManageAccessKeys.png
