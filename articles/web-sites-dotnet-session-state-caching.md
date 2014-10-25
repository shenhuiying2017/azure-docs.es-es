<properties linkid="video-center-index" urlDisplayName="index" pageTitle="Use ASP.NET session state with Azure Websites" metaKeywords="azure cache service session state" description="Learn how to use the Azure Cache Service to support ASP.NET session state caching." metaCanonical="" services="cache" documentationCenter=".NET" title="How to Use ASP.NET Session State with Azure Websites" authors="tdykstra"  solutions="" manager="wpickett" editor="mollybos"  />

<tags ms.service="web-sites" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="dotnet" ms.topic="article" ms.date="01/01/1900" ms.author="tdykstra"></tags>

# Uso del estado de la sesión de ASP.NET con Sitios web Azure

*Por [Rick Anderson][]. Actualizado el 1 de julio de 2014.*

En este tema se explica cómo utilizar el servicio de caché Redis de Azure (vista previa) para el estado de sesión.

Si su aplicación de ASP.NET utiliza el estado de sesión, deberá configurar un proveedor externo del estado de sesión (bien un servicio caché Redis o un proveedor de estado de sesión del SQL Server). Si utiliza el estado de sesión y no utiliza un proveedor externo, deberá limitar su aplicación web a una instancia. El servicio de caché Redis es el más sencillo y rápido que se puede habilitar.

Los pasos básicos para utilizar el servicio de caché (vista previa) para el almacenamiento en caché de estado de sesión incluyen:

-   [Creación de la caché][]
-   [Agregue el paquete NuGet RedisSessionStateProvider a su aplicación web.][]
-   [Modificación del archivo web.config][]
-   [Uso del objeto Session para almacenar y recuperar elementos almacenados en caché.][]

## <span id="createcache"></span></a>Creación de la caché

Siga [estas instrucciones][] para crear la caché.

## <span id="configureproject"></span></a>Agregue el paquete NuGet RedisSessionStateProvider a su aplicación web.

Instalar el paquete NuGet `RedisSessionStateProvider`. Utilice el comando siguiente para realizar la instalación desde la consola del administrador de paquetes (**Tools** \> **NuGet Package Manager** \> **Package Manager Console**):

`PM> Install-Package RedisSessionStateProvider -IncludePrerelease`

Para instalar desde **Tools** \> **NuGet Package Manager** \> **Manage NugGet Packages for Solution**, busque `RedisSessionStateProvider` y asegúrese de marcar **Include Prerelease**.

Para obtener más información vea la [página NuGet RedisSessionStateProvider][] y [Configuración de los clientes de caché][].

## <span id="configurewebconfig"></span></a>Modificación del archivo web.config

Además de hacer referencias de ensamblado para la caché, el paquete NuGet agrega entradas auxiliares en el archivo *web.config*.

1.  Abra *web.config* y encuentre el elemento **sessionState**.

2.  Introduzca los valores de `host`, `accessKey`, `port` (el puerto de SSL debe ser el 6380) y ajuste `SSL` en `true`. Estos valores se pueden obtener del cuadro del portal de vista previa de administración de Azure para su instancia de caché. Para obtener más información vea [Conexión a la memoria caché][].
    El siguiente marcado muestra los cambios en el archivo *web.config*.

``` prettyprint
 <system.web> <customErrors mode="Off" /> <authentication mode="None" /> <compilation debug="true" targetFramework="4.5" /> <httpRuntime targetFramework="4.5" /> <sessionState mode="Custom" customProvider="RedisSessionProvider"> <providers> <!--<add name="RedisSessionProvider" host = "127.0.0.1" [String] port = "" [number] accessKey = "" [String] ssl = "false" [true|false] throwOnError = "true" [true|false] retryTimeoutInMilliseconds = "0" [number] databaseId = "0" [number] applicationName = "" [String] />--> <add name="RedisSessionProvider" type="Microsoft.Web.Redis.RedisSessionStateProvider" port="6380" host="movie2.redis.cache.windows.net" accessKey="m7PNV60CrvKpLqMUxosC3dSe6kx9nQ6jP5del8TmADk=" ssl="true" /> <!--<add name="MySessionStateStore" type="Microsoft.Web.Redis.RedisSessionStateProvider" host="127.0.0.1" accessKey="" ssl="false" />--> </providers> </sessionState> </system.web>
```

</p>
## <span id="usesessionobject"></span></a>Uso del objeto Session en el código

El paso final es comenzar a utilizar el objeto Session en su código ASP.NET. Agregue objetos al estado de sesión con el método **Session.Add**. Este método utiliza pares clave-valor para almacenar elementos en la caché de estado de sesión.

    string strValue = "yourvalue";
    Session.Add("yourkey", strValue);

El siguiente código recupera este valor desde el estado de sesión.

    object objValue = Session["yourkey"];
    if (objValue != null)
       strValue = (string)obj;  

También puede usar el caché Redis para almacenar objetos en la memoria caché en su aplicación web. Para obtener más información consulte [Aplicación de películas MVC con caché Redis de Azure en 15 minutos][].
Para obtener más detalles acerca de cómo utilizar el estado de sesión de ASP.NET, consulte [Información general sobre el estado de sesión de ASP.NET][].

  [Rick Anderson]: https://twitter.com/RickAndMSFT
  [Creación de la caché]: #createcache
  [Agregue el paquete NuGet RedisSessionStateProvider a su aplicación web.]: #configureproject
  [Modificación del archivo web.config]: #configurewebconfig
  [Uso del objeto Session para almacenar y recuperar elementos almacenados en caché.]: #usesessionobject
  [estas instrucciones]: http://azure.microsoft.com/es-es/documentation/articles/cache-dotnet-how-to-use-azure-redis-cache/#create-cache
  [página NuGet RedisSessionStateProvider]: http://www.nuget.org/packages/Microsoft.Web.RedisSessionStateProvider/
  [Configuración de los clientes de caché]: http://azure.microsoft.com/es-es/documentation/articles/cache-dotnet-how-to-use-azure-redis-cache/#NuGet
  [Conexión a la memoria caché]: http://azure.microsoft.com/es-es/documentation/articles/cache-dotnet-how-to-use-azure-redis-cache/#connect-to-cache
  [Aplicación de películas MVC con caché Redis de Azure en 15 minutos]: http://azure.microsoft.com/blog/2014/06/05/mvc-movie-app-with-azure-redis-cache-in-15-minutes/
  [Información general sobre el estado de sesión de ASP.NET]: http://msdn.microsoft.com/es-es/library/ms178581.aspx
