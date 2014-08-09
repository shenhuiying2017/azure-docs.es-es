<properties linkid="video-center-index" urlDisplayName="index" pageTitle="Video Center Index" metaKeywords="" description="" metaCanonical="" services="" documentationCenter="" title="How to Use ASP.NET Session State with Azure Web Sites" authors="jroth" solutions="" manager="" editor="" />

Uso del estado de la sesión de ASP.NET con Sitios web Azure
===========================================================

En este tema se explica cómo utilizar el servicio de caché de Azure (vista previa) para permitir el almacenamiento en caché del estado de sesión de ASP.NET.

Sin un proveedor externo, el estado de sesión se almacena en proceso en el servidor web que hospeda el sitio. En el caso de Sitios web Azure, hay dos problemas con el estado de sesión en proceso. Primero, en el caso de los sitios con múltiples instancias, el estado de sesión almacenado en una instancia no es accesible para otras instancias. Debido a que una solicitud de usuario se puede enrutar a cualquier instancia, no se garantiza que la información de la sesión se encuentre ahí. Segundo, cualquier cambio que se realice en la configuración podría hacer que el sitio web se ejecutara en un servidor completamente distinto.

El servicio de caché (vista previa) proporciona un servicio de almacenamiento en caché distribuido externo al sitio web. Esto soluciona el problema con un estado de sesión en proceso. Para obtener más información acerca de cómo utilizar el estado de sesión, consulte [Información general sobre el estado de sesión de ASP.NET](http://msdn.microsoft.com/es-es/library/ms178581.aspx).

Los pasos básicos para utilizar el servicio de caché (vista previa) para el almacenamiento en caché de estado de sesión incluyen:

-   [Creación de la caché.](#createcache)
-   [Configuración del proyecto ASP.NET para utilizar el servicio de caché de Azure.](#configureproject)
-   [Modificación del archivo web.config.](#configurewebconfig)
-   [Uso del objeto Session para almacenar y recuperar elementos almacenados en caché.](#usesessionobject)

Creación de la caché
--------------------

1.  Haga clic en el icono **New** que aparece en la parte inferior del Portal de administración de Azure.

    ![IconoDeNuevo](./media/web-sites-dotnet-session-state-caching/CacheScreenshot_NewButton.png)

2.  Seleccione **Servicios de datos**, **Cache** y, a continuación, haga clic en **Quick Create**.

    ![CuadroDeDiálogoDeNuevaCaché](./media/web-sites-dotnet-session-state-caching/CachingScreenshot_CreateOptions.png)

3.  Escriba un nombre único para la caché en el cuadro de texto **Endpoint**. A continuación, seleccione valores adecuados para las demás propiedades de la caché y, a continuación, haga clic en **Create a New Cache**.

4.  Seleccione el icono **Cache** en el Portal de administración para ver todos los extremos del servicio de caché.

    ![IconoDeCaché](./media/web-sites-dotnet-session-state-caching/CachingScreenshot_CacheIcon.png)

5.  Luego, puede seleccionar uno de los extremos del servicio de caché para ver sus propiedades. Las siguientes secciones utilizarán la configuración de la pestańa **Panel** para configurar el almacenamiento en caché para un proyecto ASP.NET.

Configuración del proyecto ASP.NET
----------------------------------

1.  Primero, asegúrese de que ha [instalado el más reciente](http://www.windowsazure.com/es-es/downloads/?sdk=net) **SDK de Azure para .NET**.

2.  En Visual Studio, haga clic con el botón secundario en el proyecto ASP.NET en el **Explorador de soluciones** y, a continuación, seleccione **Manage NuGet Packages**. (Si utiliza WebMatrix, en lugar de realizar la acción anterior, haga clic en el botón **NuGet** en la barra de herramientas).

3.  Escriba **WindowsAzure.Caching** en el cuadro de edición **Search Online**.

    ![CuadroDeDiálogoDeNuGet](./media/web-sites-dotnet-session-state-caching/CachingScreenshot_NuGet.png)

4.  Seleccione el paquete **Almacenamiento en caché de Azure** y, a continuación, haga clic en el botón **Install**.

Modificación del archivo web.config
-----------------------------------

Además de hacer referencias de ensamblado para la caché, el paquete NuGet agrega entradas auxiliares en el archivo web.config. Para utilizar la caché para el estado de sesión, se deben realizar muchas modificaciones en el archivo web.config.

1.  Abra el archivo **web.config** para el proyecto ASP.NET.

2.  Encuentre el elemento **sessionState** existente y conviértalo en comentario (o quítelo).

3.  Luego, quite la marca de comentario del elemento **sessionState** que agregó el paquete NuGet para el almacenamiento en caché de Azure. El resultado final debería ser similar a la siguiente captura de pantalla.

    ![SessionStateConfig][SessionStateConfig]

4.  A continuación, busque la sección **dataCacheClients**. Quite la marca de comentario del elemento secundario **securityProperties**.

    ![ConfiguraciónDeCaché](./media/web-sites-dotnet-session-state-caching/CachingScreenshot_CacheConfig.png)

5.  En el elemento **autoDiscover**, defina el atributo **identifier** en la dirección URL del extremo de la caché. Para buscar la URL del extremo, vaya a las propiedades de la caché en el Portal de administración de Azure. En la pestańa **Panel**, copie el valor **ENDPOINT URL** en la sección **quick glance**.

    ![URLdeExtremo](./media/web-sites-dotnet-session-state-caching/CachingScreenshot_EndpointURL.png)

6.  En el elemento **messageSecurity**, defina el atributo **authorizationInfo** en la clave de acceso de la caché. Para encontrar la clave de acceso, seleccione la caché en el Portal de administración de Azure. A continuación, haga clic en el icono **Manage Keys** en la barra inferior. Haga clic en el botón de copia junto al cuadro de texto **PRIMARY ACCESS KEY**.

    ![AdministrarClaves](./media/web-sites-dotnet-session-state-caching/CachingScreenshot_ManageAccessKeys.png)

Uso del objeto Session en el código
-----------------------------------

El paso final es comenzar a utilizar el objeto Session en su código ASP.NET. Agregue objetos al estado de sesión con el método **Session.Add**. Este método utiliza pares clave-valor para almacenar elementos en la caché de estado de sesión.

    string strValue = "suvalor";
    Session.Add("suclave", strValue);

El siguiente código recupera este valor desde el estado de sesión.

    object objValue = Session["suclave"];
    if (objValue != null)
       strValue = (string)obj;  

Para obtener más detalles acerca de cómo utilizar el estado de sesión de ASP.NET, consulte [Información general sobre el estado de sesión de ASP.NET](http://msdn.microsoft.com/es-es/library/ms178581.aspx).

