---
title: "Estado de sesión con caché en Redis de Azure en el Servicio de aplicaciones de Azure"
description: "Obtenga información acerca de cómo usar el servicio de caché de Azure para admitir la caché de estado de sesión de ASP.NET."
services: app-service\web
documentationcenter: .net
author: Rick-Anderson
manager: wpickett
editor: none
ms.assetid: 4f98d289-2698-464d-85cd-99ec40fad1f2
ms.service: app-service-web
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: get-started-article
ms.date: 06/27/2016
ms.author: riande
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 7ac0fde19d61a9c3a9f54b42982c7096434cb965


---
# <a name="session-state-with-azure-redis-cache-in-azure-app-service"></a>Estado de sesión con caché en Redis de Azure en el Servicio de aplicaciones de Azure
En este tema se explica cómo utilizar el servicio Caché en Redis de Azure para el estado de sesión.

Si su aplicación de ASP.NET utiliza el estado de sesión, deberá configurar un proveedor externo del estado de sesión (bien un servicio Caché en Redis o un proveedor de estado de sesión del SQL Server). Si utiliza el estado de sesión y no utiliza un proveedor externo, deberá limitar su aplicación web a una instancia. El servicio Caché en Redis es el más sencillo y rápido que se puede habilitar.

[!INCLUDE [app-service-web-to-api-and-mobile](../../includes/app-service-web-to-api-and-mobile.md)]

## <a name="a-idcreatecacheacreate-the-cache"></a><a id="createcache"></a>Creación de la memoria caché
Siga [estas instrucciones](../redis-cache/cache-dotnet-how-to-use-azure-redis-cache.md#create-cache) para crear la caché.

## <a name="a-idconfigureprojectaadd-the-redissessionstateprovider-nuget-package-to-your-web-app"></a><a id="configureproject"></a>Incorporación del paquete NuGet RedisSessionStateProvider a su aplicación web
Instale el paquete NuGet `RedisSessionStateProvider` .  Utilice el comando siguiente para realizar la instalación desde la consola del administrador de paquetes (**Tools** > **NuGet Package Manager** > **Package Manager Console**):

  `PM> Install-Package Microsoft.Web.RedisSessionStateProvider`

Para instalar desde **Herramientas** > **Administrador de paquetes de NuGet** > **Administrar paquetes de NuGet para solución**, busque `RedisSessionStateProvider`.

Para obtener más información, vea la página [NuGet RedisSessionStateProvider](http://www.nuget.org/packages/Microsoft.Web.RedisSessionStateProvider/) y [Configuración de los clientes de caché](../redis-cache/cache-dotnet-how-to-use-azure-redis-cache.md#NuGet).

## <a name="a-idconfigurewebconfigamodify-the-webconfig-file"></a><a id="configurewebconfig"></a>Modificación del archivo web.config
Además de hacer referencias de ensamblado para la memoria caché, el paquete NuGet agrega entradas auxiliares en el archivo *web.config* . 

1. Abra el archivo *web.config* y busque el elemento **sessionState** .
2. Introduzca los valores de `host`, `accessKey`, `port` (el puerto de SSL debe ser el 6380) y establezca `SSL` en `true`. Estos valores se pueden obtener en el cuadro del portal de vista previa de la hoja [Portal Azure](http://go.microsoft.com/fwlink/?LinkId=529715) para su instancia de caché. Para obtener más información, vea [Conexión con la memoria caché](../redis-cache/cache-dotnet-how-to-use-azure-redis-cache.md#connect-to-cache). Tenga en cuenta que el puerto no SSL está deshabilitado de forma predeterminada para las cachés nuevas. Para más información sobre cómo habilitar el puerto no SSL, consulte la sección [Puertos de acceso](https://msdn.microsoft.com/library/azure/dn793612.aspx#AccessPorts) del tema de [Configuración de caché en Azure Redis Cache](https://msdn.microsoft.com/library/azure/dn793612.aspx). El marcado siguiente muestra los cambios efectuados en el archivo *web.config*, específicamente los cambios en *port*, *host*, accessKey* y *ssl*.
   
          <system.web>;
            <customErrors mode="Off" />;
            <authentication mode="None" />;
            <compilation debug="true" targetFramework="4.5" />;
            <httpRuntime targetFramework="4.5" />;
            <sessionState mode="Custom" customProvider="RedisSessionProvider">;
              <providers>;  
                  <!--<add name="RedisSessionProvider" 
                    host = "127.0.0.1" [String]
                    port = "" [number]
                    accessKey = "" [String]
                    ssl = "false" [true|false]
                    throwOnError = "true" [true|false]
                    retryTimeoutInMilliseconds = "0" [number]
                    databaseId = "0" [number]
                    applicationName = "" [String]
                  />;-->;
                 <add name="RedisSessionProvider" 
                      type="Microsoft.Web.Redis.RedisSessionStateProvider" 
                      port="6380"
                      host="movie2.redis.cache.windows.net" 
                      accessKey="m7PNV60CrvKpLqMUxosC3dSe6kx9nQ6jP5del8TmADk=" 
                      ssl="true" />;
              <!--<add name="MySessionStateStore" type="Microsoft.Web.Redis.RedisSessionStateProvider" host="127.0.0.1" accessKey="" ssl="false" />;-->;
              </providers>;
            </sessionState>;
          </system.web>;

## <a name="a-idusesessionobjecta-use-the-session-object-in-code"></a><a id="usesessionobject"></a> Uso del objeto Session en el código
El paso final es comenzar a utilizar el objeto Session en su código ASP.NET. Agregue objetos al estado de sesión con el método **Session.Add** . Este método utiliza pares clave-valor para almacenar elementos en la caché de estado de sesión.

    string strValue = "yourvalue";
    Session.Add("yourkey", strValue);

El siguiente código recupera este valor desde el estado de sesión.

    object objValue = Session["yourkey"];
    if (objValue != null)
       strValue = (string)objValue;    

También puede usar el servicio Caché en Redis para almacenar objetos en la memoria caché en su aplicación web. Para obtener más información, consulte [Aplicación de películas MVC con Caché en Redis de Azure en 15 minutos](https://azure.microsoft.com/blog/2014/06/05/mvc-movie-app-with-azure-redis-cache-in-15-minutes/).
Para obtener más detalles acerca de cómo utilizar el estado de sesión de ASP.NET, consulte [Información general sobre el estado de sesión de ASP.NET][Información general sobre el estado de sesión de ASP.NET].

> [!NOTE]
> Si desea empezar a trabajar con el Servicio de aplicaciones de Azure antes de suscribirse para abrir una cuenta de Azure, vaya a [Prueba del Servicio de aplicaciones](http://go.microsoft.com/fwlink/?LinkId=523751), donde podrá crear inmediatamente una aplicación web de inicio de corta duración en el Servicio de aplicaciones. No es necesario proporcionar ninguna tarjeta de crédito ni asumir ningún compromiso.
> 
> 

## <a name="whats-changed"></a>Lo que ha cambiado
* Para obtener una guía del cambio de Sitios web a Servicio de aplicaciones, consulte: [Servicio de aplicaciones de Azure y su impacto en los servicios de Azure existentes](http://go.microsoft.com/fwlink/?LinkId=529714)
  
  *Por [Rick Anderson](https://twitter.com/RickAndMSFT)*

[instala la más reciente]: http://www.windowsazure.com/downloads/?sdk=net  
[Información general sobre el estado de sesión de ASP.NET]: http://msdn.microsoft.com/library/ms178581.aspx

[NewIcon]: ./media/web-sites-dotnet-session-state-caching/CacheScreenshot_NewButton.png
[NewCacheDialog]: ./media/web-sites-dotnet-session-state-caching/CachingScreenshot_CreateOptions.png
[CacheIcon]: ./media/web-sites-dotnet-session-state-caching/CachingScreenshot_CacheIcon.png
[NuGetDialog]: ./media/web-sites-dotnet-session-state-caching/CachingScreenshot_NuGet.png
[OutputConfig]: ./media/web-sites-dotnet-session-state-caching/CachingScreenshot_OC_WebConfig.png
[CacheConfig]: ./media/web-sites-dotnet-session-state-caching/CachingScreenshot_CacheConfig.png
[EndpointURL]: ./media/web-sites-dotnet-session-state-caching/CachingScreenshot_EndpointURL.png
[ManageKeys]: ./media/web-sites-dotnet-session-state-caching/CachingScreenshot_ManageAccessKeys.png




<!--HONumber=Nov16_HO2-->


