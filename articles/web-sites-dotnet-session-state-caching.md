<properties 
	pageTitle="Estado de sesión con caché en Redis de Azure en el Servicio de aplicaciones de Azure" 
	description="Obtenga información acerca de cómo usar el servicio de caché de Azure para admitir la caché de estado de sesión de ASP.NET." 
	services="app-service\web" 
	documentationCenter=".net" 
 	authors="Rick-Anderson" 
	manager="wpickett" 
	editor=""/>

<tags 
	ms.service="app-service-web" 
	ms.workload="web" 
	ms.tgt_pltfrm="na" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="03/24/2015" 
	ms.author="riande"/>


# Estado de sesión con caché en Redis de Azure en el Servicio de aplicaciones de Azure


En este tema se explica cómo utilizar el servicio Caché en Redis de Azure para el estado de sesión.

Si su aplicación de ASP.NET utiliza el estado de sesión, deberá configurar un proveedor externo del estado de sesión (bien un servicio Caché en Redis o un proveedor de estado de sesión del SQL Server). Si utiliza el estado de sesión y no utiliza un proveedor externo, deberá limitar su aplicación web a una instancia. El servicio Caché en Redis es el más sencillo y rápido que se puede habilitar.

<h2><a id="createcache"></a>Creación de la memoria caché</h2>
Siga [estas instrucciones](cache-dotnet-how-to-use-azure-redis-cache.md#create-cache) para crear la caché.

<h2><a id="configureproject"></a>Incorporación del paquete NuGet RedisSessionStateProvider a su aplicación web</h2>
Instale el paquete NuGet `RedisSessionStateProvider`. Utilice el comando siguiente para realizar la instalación desde la consola del administrador de paquetes (Herramientas > **Administrador de paquetes de NuGet** > **Consola del administrador de paquetes**):

  `PM> Install-Package Microsoft.Web.RedisSessionStateProvider`
  
Para instalar desde **Herramientas** > **Administrador de paquetes de NuGet** > **Administrar paquetes de NuGet para solución**, busque `RedisSessionStateProvider`.

Para obtener más información, vea la página [NuGet RedisSessionStateProvider](http://www.nuget.org/packages/Microsoft.Web.RedisSessionStateProvider/) y [Configuración de los clientes de caché](cache-dotnet-how-to-use-azure-redis-cache.md#NuGet).

<h2><a id="configurewebconfig"></a>Modificación del archivo web.config</h2>
Además de hacer referencias de ensamblado para la memoria caché, el paquete NuGet agrega entradas auxiliares en el archivo *web.config*.

1. Abra el archivo *web.config* y busque el elemento **sessionState**.

1. Introduzca los valores de `host`, `accessKey`, `port` (el puerto de SSL debe ser el 6380) y establezca `SSL` en `true`. Estos valores se pueden obtener en el cuadro del portal de vista previa de la hoja [Portal Azure](http://go.microsoft.com/fwlink/?LinkId=529715) para su instancia de caché. Para obtener más información, vea [Conexión con la memoria caché](cache-dotnet-how-to-use-azure-redis-cache.md#connect-to-cache). Tenga en cuenta que el puerto no SSL está deshabilitado de forma predeterminada para las cachés nuevas. Para obtener más información acerca de cómo habilitar el puerto no SSL, consulte la sección de [puertos de acceso](https://msdn.microsoft.com/library/azure/dn793612.aspx#AccessPorts) en el tema [Configuración de una memoria caché en Caché en Redis de Azure](https://msdn.microsoft.com/library/azure/dn793612.aspx). La revisión siguiente muestra los cambios en el archivo *web.config*.


  <pre class="prettyprint"> &lt;system.web&gt; &lt;customErrors mode="Off" /&gt; &lt;authentication mode="None" /&gt; &lt;compilation debug="true" targetFramework="4.5" /&gt; &lt;httpRuntime targetFramework="4.5" /&gt; &lt;sessionState mode="Custom" customProvider="RedisSessionProvider"&gt; &lt;providers&gt; &lt;!--&lt;add name="RedisSessionProvider" host = "127.0.0.1" [String] port = "" [number] accessKey = "" [String] ssl = "false" [true|false] throwOnError = "true" [true|false] retryTimeoutInMilliseconds = "0" [number] databaseId = "0" [number] applicationName = "" [String] /&gt;--&gt; &lt;add name="RedisSessionProvider" type="Microsoft.Web.Redis.RedisSessionStateProvider" <mark>port="6380" host="movie2.redis.cache.windows.net" accessKey="m7PNV60CrvKpLqMUxosC3dSe6kx9nQ6jP5del8TmADk=" ssl="true"</mark> /&gt; &lt;!--&lt;add name="MySessionStateStore" type="Microsoft.Web.Redis.RedisSessionStateProvider" host="127.0.0.1" accessKey="" ssl="false" /&gt;--&gt; &lt;/providers&gt; &lt;/sessionState&gt; &lt;/system.web&gt;</pre>


<h2><a id="usesessionobject"></a>Uso del objeto Session en el código</h2>
El paso final es comenzar a utilizar el objeto Session en su código ASP.NET. Agregue objetos al estado de sesión con el método **Session.Add**. Este método utiliza pares clave-valor para almacenar elementos en la caché de estado de sesión.

    string strValue = "yourvalue";
	Session.Add("yourkey", strValue);

El siguiente código recupera este valor desde el estado de sesión.

    object objValue = Session["yourkey"];
    if (objValue != null)
       strValue = (string)objValue;	

También puede usar el servicio Caché en Redis para almacenar objetos en la memoria caché en su aplicación web. Para obtener más información, consulte [Aplicación de películas MVC con Caché en Redis de Azure en 15 minutos](http://azure.microsoft.com/blog/2014/06/05/mvc-movie-app-with-azure-redis-cache-in-15-minutes/). Para obtener más detalles acerca de cómo utilizar el estado de sesión de ASP.NET, consulte [Información general sobre el estado de sesión de ASP.NET][].

>[AZURE.NOTE]Si desea empezar a trabajar con el Servicio de aplicaciones de Azure antes de inscribirse para abrir una cuenta de Azure, vaya a [Prueba del Servicio de aplicaciones](http://go.microsoft.com/fwlink/?LinkId=523751), donde podrá crear inmediatamente una aplicación web de inicio de corta duración en el Servicio de aplicaciones. No es necesario proporcionar ninguna tarjeta de crédito ni asumir ningún compromiso.

## Lo que ha cambiado
* Para obtener una guía del cambio de Sitios web a Servicio de aplicaciones, consulte: [Servicio de aplicaciones de Azure y su impacto en los servicios de Azure existentes](http://go.microsoft.com/fwlink/?LinkId=529714)
* Para obtener una guía del cambio del portal anterior al nuevo, consulte: [Referencia para navegar en el portal de vista previa](http://go.microsoft.com/fwlink/?LinkId=529715)

  *Por [Rick Anderson](https://twitter.com/RickAndMSFT)*
  
[installed the latest]: http://www.windowsazure.com/downloads/?sdk=net
[Información general sobre el estado de sesión de ASP.NET]: http://msdn.microsoft.com/library/ms178581.aspx

[NewIcon]: ./media/web-sites-dotnet-session-state-caching/CacheScreenshot_NewButton.png
[NewCacheDialog]: ./media/web-sites-dotnet-session-state-caching/CachingScreenshot_CreateOptions.png
[CacheIcon]: ./media/web-sites-dotnet-session-state-caching/CachingScreenshot_CacheIcon.png
[NuGetDialog]: ./media/web-sites-dotnet-session-state-caching/CachingScreenshot_NuGet.png
[OutputConfig]: ./media/web-sites-dotnet-session-state-caching/CachingScreenshot_OC_WebConfig.png
[CacheConfig]: ./media/web-sites-dotnet-session-state-caching/CachingScreenshot_CacheConfig.png
[EndpointURL]: ./media/web-sites-dotnet-session-state-caching/CachingScreenshot_EndpointURL.png
[ManageKeys]: ./media/web-sites-dotnet-session-state-caching/CachingScreenshot_ManageAccessKeys.png

<!--HONumber=54-->