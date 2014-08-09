<properties linkid="video-center-detail" urlDisplayName="details" pageTitle="Video Center Details" metaKeywords="" description="" metaCanonical="" services="" documentationCenter="" title="How to Use ASP.NET Web Forms Output Caching with Azure Web Sites" authors="jroth" solutions="" manager="" editor="" />

Uso de almacenamiento en caché de resultados de formularios web ASP.NET con Sitios web Azure
============================================================================================

En este tema se explica cómo utilizar el servicio de caché de Azure (vista previa) para permitir el almacenamiento en caché de resultados de página ASP.NET para formularios web ASP.NET. El almacenamiento en caché de resultados de una página es una optimización que devuelve directamente una página de representación en caché durante un tiempo determinado. Resulta útil en situaciones en las que se obtiene acceso a la página de forma más frecuente de lo suele cambiar. Es importante tener en cuenta que el almacenamiento en caché de resultados de una página no es compatible con aplicaciones ASP.NET MVC.

El servicio de caché (vista previa) proporciona un servicio de almacenamiento en caché distribuido que es externo al sitio web. Esto permite a todas las instancias del sitio web obtener acceso a la misma representación en caché de una página.

Los pasos básicos para utilizar el servicio de caché (vista previa) para el almacenamiento en caché de resultados de una página incluyen:

-   [Creación de la caché](#createcache)
-   [Configuración del proyecto ASP.NET para utilizar el servicio de caché de Azure.](#configureproject)
-   [Modificación del archivo web.config](#configurewebconfig)
-   [Uso del almacenamiento en caché de resultados para devolver temporalmente versiones en caché de una página](#useoutputcaching)

Creación de la caché
--------------------

1.  Haga clic en el icono **New** que aparece en la parte inferior del Portal de administración de Azure.

    ![IconoDeNuevo](./media/web-sites-web-forms-output-caching/CacheScreenshot_NewButton.PNG)

2.  Seleccione **Servicios de datos**, **Cache** y, a continuación, haga clic en **Quick Create**.

    ![CuadroDeDiálogoDeNuevaCaché](./media/web-sites-web-forms-output-caching/CachingScreenshot_CreateOptions.PNG)

3.  Escriba un nombre único para la caché en el cuadro de texto **Endpoint**. A continuación, seleccione valores adecuados para las demás propiedades de la caché y, a continuación, haga clic en **Create a New Cache**.

4.  Seleccione el icono **Cache** en el Portal de administración para ver todos los extremos del servicio de caché.

    ![IconoDeCaché](./media/web-sites-web-forms-output-caching/CachingScreenshot_CacheIcon.PNG)

5.  Luego, puede seleccionar uno de los extremos del servicio de caché para ver sus propiedades. Las siguientes secciones utilizarán la configuración de la pestaña **Panel** para configurar el almacenamiento en caché para un proyecto ASP.NET.

Configuración del proyecto ASP.NET
----------------------------------

1.  Primero, asegúrese de que ha [instalado el más reciente](http://www.windowsazure.com/es-es/downloads/?sdk=net) **SDK de Azure para .NET**.

2.  En Visual Studio, haga clic con el botón secundario en el proyecto ASP.NET en el **Explorador de soluciones** y, a continuación, seleccione **Manage NuGet Packages**. Si utiliza WebMatrix, en lugar de realizar la acción anterior, haga clic en el botón **NuGet** en la barra de herramientas.

3.  Escriba **WindowsAzure.Caching** en el cuadro de edición **Search Online**.

    ![CuadroDeDiálogoDeNuGet](./media/web-sites-web-forms-output-caching/CachingScreenshot_NuGet.PNG)

4.  Seleccione el paquete **Almacenamiento en caché de Azure** y, a continuación, haga clic en el botón **Install**.

Modificación del archivo web.config
-----------------------------------

Además de hacer referencias de ensamblado para la caché, el paquete NuGet agrega entradas auxiliares en el archivo web.config. Para utilizar la caché para el almacenamiento en caché de resultados de una página ASP.NET, se deben en realizar muchas modificaciones en el archivo web.config.

1.  Abra el archivo **web.config** para el proyecto ASP.NET.

2.  Si dispone de elementos **caching** y **outputCache** existentes, conviértalos en comentario (o quítelos).

3.  Luego, quite la marca de comentario del elemento **caching** que agregó el paquete NuGet para el almacenamiento en caché de Azure. El resultado final debería ser similar a la siguiente captura de pantalla.

    ![ConfiguraciónDeResultado](./media/web-sites-web-forms-output-caching/CachingScreenshot_OC_WebConfig.PNG)

4.  A continuación, busque la sección **dataCacheClients**. Quite la marca de comentario del elemento secundario **securityProperties**.

    ![ConfiguraciónDeCaché](./media/web-sites-web-forms-output-caching/CachingScreenshot_CacheConfig.PNG)

5.  En el elemento **autoDiscover**, defina el atributo **identifier** en la dirección URL del extremo de la caché. Para buscar la URL del extremo, vaya a las propiedades de la caché en el Portal de administración de Azure. En la pestaña **Panel**, copie el valor **ENDPOINT URL** en la sección **quick glance**.

    ![URLdeExtremo](./media/web-sites-web-forms-output-caching/CachingScreenshot_EndpointURL.PNG)

6.  En el elemento **messageSecurity**, defina el atributo **authorizationInfo** en la clave de acceso de la caché. Para encontrar la clave de acceso, seleccione la caché en el Portal de administración de Azure. A continuación, haga clic en el icono **Manage Keys** en la barra inferior. Haga clic en el botón de copia junto al cuadro de texto **PRIMARY ACCESS KEY**.

    ![AdministrarClaves](./media/web-sites-web-forms-output-caching/CachingScreenshot_ManageAccessKeys.PNG)

Uso de almacenamiento en caché de resultados
--------------------------------------------

El paso final es configurar páginas en la aplicación de formularios web ASP.NET para usar el almacenamiento en caché de resultados. Esto se puede hacer agregando el atributo **OutputCache** al principio del origen .ASPX. Por ejemplo:

    <%@ OutputCache Duration="45" VaryByParam="*" %>

En el ejemplo anterior se almacena en caché la página durante 45 segundos. Puesto que **VaryByParam** se establece en "\*", esta salida de página en caché no cambia incluso si se pasan diferentes parámetros de consulta. Sin embargo, en el siguiente ejemplo se almacena en caché una versión distinta de la página para cada valor del parámetro UserId:

    <%@ OutputCache Duration="45" VaryByParam="UserId" %>  
