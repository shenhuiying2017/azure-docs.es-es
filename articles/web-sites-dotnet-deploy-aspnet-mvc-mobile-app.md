<properties linkid="develop-dotnet-aspnet-mvc-4-mobile-website" urlDisplayName="ASP.NET MVC 4 mobile website" pageTitle=".NET ASP.NET MVC 4 mobile web site - Azure tutorials" metaKeywords="Azure tutorial, Azure web app tutorial, Azure mobile app, Azure ASP.NET MVC 4,,ASP.NET MVC" description="A tutorial that teaches you how to deploy a web application to an Azure web site using mobile features in ASP.NET MVC 4 web application." metaCanonical="" services="web-sites" documentationCenter=".NET" title="Deploy an ASP.NET MVC Mobile Web Application on Azure Web Sites" authors="tdykstra" solutions="" manager="" editor="" />

Implementación de una aplicación web móvil ASP.NET MVC en Sitios web Azure
==========================================================================

***Por [Rick Anderson](https://twitter.com/RickAndMSFT); fecha de actualización: 26 de junio de 2013***

Este tutorial le enseñará los conceptos básicos para implementar una aplicación web en un sitio web de Azure. A los efectos de este tutorial, vamos a trabajar con las características móviles en una aplicación web ASP.NET MVC 4. Para llevar a cabo los pasos de este tutorial, puede usar Microsoft Visual Studio 2012. También puede usar [Visual Studio Express 2012](http://www.microsoft.com/visualstudio/eng/products/visual-studio-express-products), que es una versión gratuita de Microsoft Visual Studio.

Aprenderá a:
------------

-   Reconocer la manera en que las plantillas de ASP.NET MVC 4 usan el atributo de ventanilla HTML5 y la representación adaptable para mejorar la visualización en los dispositivos móviles.
-   Crear vistas específicas para móviles.
-   Crear un modificador de vista que permite a los usuarios alternar entre un una vista móvil y una vista de escritorio de la aplicación.
-   Implementar la aplicación web en Azure.

Para este tutorial, agregará características móviles a la aplicación simple de lista de conferencias que se proporciona en el proyecto inicial. La siguiente captura de pantalla muestra la página principal de la aplicación completa como se ve en el emulador de Windows Phone 7.

![Página principal de la aplicación de conferencia MVC4.](./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/FinishedAPPMainScreen.png)

[WACOM.INCLUDE [create-account-and-websites-note](../includes/create-account-and-websites-note.md)]

Configuración del entorno de desarrollo
---------------------------------------

Configure el entorno de desarrollo mediante la instalación del SDK de Azure para .NET Framework.

1.  Para instalar el SDK de Azure para .NET, haga clic en el vínculo a continuación. Si no tiene instalado todavía Visual Studio 2012, se instalará a través del vínculo. Este tutorial requiere Visual Studio 2012. [SDK de Azure para Visual Studio 2012](http://go.microsoft.com/fwlink/?LinkId=254364)
2.  Cuando se le solicite ejecutar o guardar el archivo ejecutable de instalación, haga clic en **Run**.
3.  En la ventana del instalador de plataforma web, haga clic en **Install** y continúe con la instalación.

![Instalador de plataforma web: SDK de Azure para .NET](./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/WebPIAzureSdk20NetVS12.png)

También necesitará un emulador de explorador móvil. Funcionará cualquiera de las siguientes opciones:

-   [Emulador de Windows Phone 7](http://msdn.microsoft.com/en-us/library/ff402530(VS.92).aspx). (Este es el emulador que se utiliza en la mayoría de las capturas de pantalla de este tutorial).
-   Cambie la cadena de agente de usuario para emular un iPhone. Consulte [esta entrada de blog](http://www.howtogeek.com/113439/how-to-change-your-browsers-user-agent-without-installing-any-extensions/) en How-To-Geek.
-   [Emulador de Opera Mobile](http://www.opera.com/developer/tools/mobile/).
-   [Apple Safari](http://www.apple.com/safari/download/) con el agente de usuario establecido en iPhone. Para obtener instrucciones sobre cómo configurar el agente de usuario en Safari para "iPhone", consulte [How to let Safari pretend it's IE](http://www.davidalison.com/2008/05/how-to-let-safari-pretend-its-ie.html) en el blog de David Alison.
-   [FireFox](http://www.bing.com/search?q=firefox+download) con [FireFox User Agent Switcher](https://addons.mozilla.org/en-US/firefox/addon/user-agent-switcher/).

En este tutorial se muestra código en C \#. Sin embargo, el proyecto inicial y el proyecto terminado estarán disponibles en Visual Basic. Los proyectos de Visual Studio con código fuente de Visual Basic y C \# están disponibles para este tema:

-   [Descarga del proyecto inicial](http://go.microsoft.com/fwlink/?LinkId=228307)
-   [Descarga del proyecto completado](http://go.microsoft.com/fwlink/?LinkId=228306)

Pasos en este tutorial
----------------------

-   [Creación de un sitio web de Azure](#bkmk_CreateWebSite)
-   [Configuración del proyecto inicial](#bkmk_setupstarterproject)
-   [Reemplazo de las vistas, diseños y vistas parciales](#bkmk_overrideviews)
-   [Uso de jQuery Mobile para definir la interfaz del explorador móvil](#bkmk_usejquerymobile)
-   [Mejora de la lista de oradores](#bkmk_Improvespeakerslis)
-   [Creación de una vista de oradores móviles](#bkmk_mobilespeakersview)
-   [Mejora de la lista de etiquetas](#bkmk_improvetags)
-   [Mejora la lista de fechas](#bkmk_improvedates)
-   [Mejora de la vista SessionsTable](#bkmk_improvesessionstable)
-   [Mejora de la vista SessionByCode](#bkmk_improvesessionbycode)
-   [Implementación de la aplicación en el sitio web de Azure](#bkmk_deployapplciation)

### Creación de un sitio web en Azure

El sitio web de Azure se ejecutará en un entorno de hospedaje compartido, es decir, en máquinas virtuales compartidas con otros clientes de Azure. Los entornos de hospedaje compartidos ofrecen un coste reducido a los nuevos usuarios de servicios en la nube. Más adelante, si el tráfico web aumenta, es posible escalar la aplicación para atender la demanda ejecutándola en máquinas virtuales dedicadas. Si necesita una arquitectura más compleja, puede migrar la aplicación a un Servicio en la nube de Azure. Los servicios en la nube se ejecutan en MV dedicadas que se pueden configurar según sus necesidades.

1.  Inicie sesión en el [Portal de administración de Azure](https://manage.windowsazure.com). En el Portal de administración, haga clic en **New**.

    ![](./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/depoly_mobile_new_website_1.png)

2.  Haga clic en **Sitio web** y, a continuación, haga clic en **Quick Create**.

    ![](./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/depoly_mobile_new_website_2.png)

3.  En **Crear un nuevo sitio web**, escriba en el cuadro de **URL** una cadena que se va a utilizar como URL única de la aplicación.

    ![](./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/depoly_mobile_new_website_3.png)

    La URL completa consistirá en el valor que escriba más el sufijo que aparece debajo del cuadro de texto. La ilustración muestra "MyMobileMVC4WebSite", pero si alguien ya ha utilizado esa URL, tendrá que seleccionar uno diferente. Seleccione la **REGIÓN** en la que se encuentra.

4.  Haga clic en la marca de verificación que aparece en la parte inferior del cuadro para indicar que finalizó la configuración.

El Portal de administración vuelve a la página de Sitios web y la columna Status muestra que se está creando el sitio. Después de unos segundos, la columna Status indica que el sitio se creó correctamente. En la barra de navegación situada a la izquierda, el número de sitios que tiene en su cuenta aparece en el icono Sitios web y el número de bases de datos en el icono Bases de datos SQL.

![](./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/depoly_mobile_new_website_4.png)

### Configuración del proyecto inicial

1.  Descargue el [proyecto inicial de la aplicación de lista de conferencias](http://go.microsoft.com/fwlink/?LinkId=228307).

2.  Luego, en el Explorador de Windows, haga clic con el botón secundario en el archivo MvcMobileStarterBeta.zip y seleccione *Propiedades*.

3.  En el cuadro de diálogo Propiedades de MvcMobileRTMStarter.zip, seleccione el botón Desbloquear. (El desbloqueo impide una advertencia de seguridad que se produce cuando se intenta usar un archivo .zip que ha descargado de la web).

    ![Cuadro de diálogo Propiedades.](./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/propertiespopup.png)

4.  Haga clic con el botón secundario en el archivo MvcMobile.zip y seleccione Extract All para descomprimir el archivo.

5.  En Visual Studio, abra el archivo MvcMobile.sln.

### Para ejecutar el proyecto inicial

1.  Presione CTRL+F5 para ejecutar la aplicación, que se mostrará en el explorador de escritorio.
2.  Inicie su emulador de explorador móvil, copie la dirección URL de la aplicación de conferencia en el emulador y, a continuación, haga clic en el vínculo Browse by tag.

    -   Si está utilizando el emulador de Windows Phone, haga clic en la barra de dirección URL y presione la tecla de pausa para obtener acceso al teclado. La imagen de abajo muestra la vista Alltags (a partir de la selección de Browse by tag).

    ![Página de búsqueda por etiqueta.](./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/BrowseByTagWithCallout.png)

La pantalla es muy fácil de leer en un dispositivo móvil. Seleccione el vínculo de ASP.NET.

![Examine sesiones etiquetadas como ASP.NET.](./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/ASPNetPage.png)

La vista de etiquetas ASP.NET está muy desordenada. Por ejemplo, la columna Date es muy difícil de leer. Más adelante en el tutorial podrá crear una versión de la vista Alltags diseñada específicamente para exploradores móviles y que hará que la pantalla sea fácil de leer.

Reemplazo de las vistas, diseños y vistas parciales
---------------------------------------------------

En esta sección, creará un archivo de diseño específico para móviles.

Una característica nueva e importante en ASP.NET MVC 4 es un mecanismo sencillo que le permite reemplazar cualquier vista (incluidos los diseños y las vistas parciales) para exploradores móviles en general, para un explorador móvil individual o para cualquier explorador específico. Para proporcionar una vista específica para móviles, puede copiar un archivo de vista y agregar .Mobile al nombre del archivo. Por ejemplo, para crear una vista de índice móvil, copie *Views\\Home\\Index.cshtml* a *Views\\Home\\Index.Mobile.cshtml*.

Para empezar, copie *Views\\Shared\\\_Layout.cshtml* en *Views\\Shared\\\_Layout.Mobile.cshtml*. Abra *\_Layout.Mobile.cshtml* y cambie el título de **MVC4 Conference** a **Conference (Mobile)**.

En cada llamada de **Html.ActionLink**, elimine "Browse by" de cada vínculo ActionLink. El código siguiente muestra la sección completa del cuerpo del archivo de diseño móvil.

     <body>
        <div class="page">
            <div id="header">
                <div id="logindisplay"></div>
                <div id="title">
                    <h1> Conference (Mobile)</h1>
                </div>
                <div id="menucontainer">
                    <ul id="menu">
                        <li>@Html.ActionLink("Home", "Index", "Home")</li>
                        <li>@Html.ActionLink("Date", "AllDates", "Home")</li>
                        <li>@Html.ActionLink("Speaker", "AllSpeakers", "Home")</li>
                        <li>@Html.ActionLink("Tag", "AllTags", "Home")</li>
                    </ul>
                </div>
            </div>
            <div id="main">
                @RenderBody()
            </div>
            <div id="footer">
            </div>
        </div>
    </body>

Copie el archivo *Views\\Home\\AllTags.cshtml* en *Views\\Home\\AllTags.Mobile.cshtml*. Abra el archivo nuevo y cambie el elemento &lt;h2\> de "Tags" a "Tags (M)":

     <h2>Tags (M)</h2>

Diríjase a la página de etiquetas usando un explorador de escritorio y un emulador de explorador móvil. El emulador de explorador móvil muestra los dos cambios que ha realizado.

![Mostrar cambios en página de etiquetas](./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/windows-live-writer_asp_net-mvc-4-mobile-features_d2ff_p2m_layouttags_mobile_thumb.png)

Por el contrario, la pantalla de escritorio no ha cambiado.

![Mostrar vista de etiquetas de escritorio](./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/Windows-Live-Writer_ASP_NET-MVC-4-Mobile-Features_D2FF_p2_layoutTagsDesktop_thumb.png)

Uso de jQuery Mobile para definir la interfaz del explorador móvil
------------------------------------------------------------------

En esta sección podrá instalar el paquete de NuGet jQuery.Mobile.MVC, que instala jQuery Mobile y un widget de modificador de vista.

La biblioteca de [jQuery Mobile](http://jquerymobile.com/demos/1.0b3/#/demos/1.0b3/docs/about/intro.html) proporciona un marco de interfaz de usuario que funciona en todos los principales exploradores móviles. jQuery Mobile aplica una mejora progresiva a los exploradores móviles que admiten CSS y JavaScript. La mejora progresiva permite que todos los exploradores exhiban el contenido básico de una página web, al tiempo que permite que exploradores y dispositivos más potentes tengan una visualización más enriquecida. Los archivos JavaScript y CSS que se incluyen con jQuery Mobile llevan muchos elementos que se ajustan a los exploradores móviles sin realizar ningún cambio de marca.

1.  Elimine el archivo *Shared\\\_Layout.Mobile.cshtml* que creó anteriormente.

2.  Cambie el nombre de *Views\\Home\\AllTags.Mobile.cshtml* por *Views\\Home\\AllTags.Mobile.cshtml.hide* (volverá a usar el archivo más tarde). Debido a que el archivo ya no tiene una extensión .cshtml, el tiempo de ejecución de ASP.NET MVC no lo usará para representar la vista *Alltags*.

3.  Instale el paquete de NuGet jQuery.Mobile.MVC; para ello, realice lo siguiente:

    a. En el menú **Tools**, seleccione la consola **Package Manager** y, a continuación, seleccione **Library Package Manager**.

         ![Library package manager][jquery1]

    b. En **Package Manager Console**, escriba *Install-Package jQuery.Mobile.MVC -version 1.0.0*

         ![Package manager console][jquery2]

El paquete de NuGet jQuery.Mobile.MVC instala lo siguiente:

-   El archivo *App\_Start\\BundleMobileConfig.cs*, que es necesario para hacer referencia a los archivos de jQuery JavaScript y CSS que se agregaron. Debe seguir las instrucciones a continuación y hacer referencia al paquete móvil definido en este archivo.
-   Archivos de jQuery Mobile CSS.
-   Un widget controlador de ViewSwitcher (*Controllers\\ViewSwitcherController.cs)*.
-   Archivos de jQuery Mobile JavaScript.
-   Un archivo de diseño tipo jQuery Mobile (*Views\\Shared\_Layout.Mobile.cshtml*).
-   Una vista parcial de modificador de vista (*MvcMobile\\Views\\Shared\_ViewSwitcher.cshtml*) que proporciona un vínculo en la parte superior de cada página para cambiar de la vista de escritorio a la vista móvil y viceversa.
-   Varios archivos de imagen .png y .gif en la carpeta Content\\images.

Abra el archivo *Global.asax* y agregue el siguiente código como la última línea del método Application\_Start.

	BundleMobileConfig.RegisterBundles(BundleTable.Bundles);

El siguiente código muestra el archivo Global.asax completo.

    using System; 
    using System.Web.Http; 
    using System.Web.Mvc; 
    using System.Web.Optimization; 
    using System.Web.Routing; 
    using System.Web.WebPages; 
     
    namespace MvcMobile 
    { 
     
        public class MvcApplication : System.Web.HttpApplication 
        { 
            protected void Application_Start() 
            { 
                DisplayModeProvider.Instance.Modes.Insert(0, new DefaultDisplayMode("iPhone") 
                { 
                    ContextCondition = (context => context.GetOverriddenUserAgent().IndexOf 
                        ("iPhone", StringComparison.OrdinalIgnoreCase) >= 0) 
                }); 
                AreaRegistration.RegisterAllAreas(); 
     
                WebApiConfig.Register(GlobalConfiguration.Configuration); 
                FilterConfig.RegisterGlobalFilters(GlobalFilters.Filters); 
                RouteConfig.RegisterRoutes(RouteTable.Routes); 
                BundleConfig.RegisterBundles(BundleTable.Bundles); 
                BundleMobileConfig.RegisterBundles(BundleTable.Bundles); 
            } 
        } 
    }

Abra el archivo *MvcMobile\\Views\\Shared\\\_Layout.Mobile.cshtml* y agregue el siguiente marcado directamente después de la llamada *Html.Partial*:

    <div data-role="header" align="center">
        @Html.ActionLink("Home", "Index", "Home")
        @Html.ActionLink("Date", "AllDates")
        @Html.ActionLink("Speaker", "AllSpeakers")
        @Html.ActionLink("Tag", "AllTags")
    </div>

La sección completa del cuerpo es la siguiente:

    <body>
        <div data-role="page" data-theme="a">
            @Html.Partial("_ViewSwitcher")
            <div data-role="header" align="center">
                @Html.ActionLink("Home", "Index", "Home")
                @Html.ActionLink("Date", "AllDates")
                @Html.ActionLink("Speaker", "AllSpeakers")
                @Html.ActionLink("Tag", "AllTags")
            </div>
            <div data-role="header">
                <h1>@ViewBag.Title</h1>
            </div>
            <div data-role="content">
                @RenderSection("featured", false)
                @RenderBody()
            </div>
        </div>
    </body>

Compile la aplicación y, en su emulador de explorador móvil, diríjase a la vista Alltags. Verá lo siguiente:

![Después de la instalación de jquery mediante nuget.](./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/windows-live-writer_asp_net-mvc-4-mobile-features_d2ff_p3_afternuget_thumb.png)

**Nota:**

Puede depurar el código específico para móviles estableciendo la cadena de agente de usuario para IE o Chrome para iPhone y luego usando las herramientas para desarrolladores F-12. Si su explorador móvil no muestra los vínculos **Home**, **Speaker**, **Tag** y **Date** como botones, las referencias a los scripts de jQuery Mobile y los archivos de CSS probablemente no son correctas.

Además de los cambios de estilo, podrá ver **Displaying mobile view** y un vínculo que le permite cambiar de la vista móvil a la vista de escritorio. Seleccione el **vínculo de vista de escritorio** y, a continuación, se muestra la vista de escritorio.

La vista de escritorio no proporciona una manera de navegar directamente a la vista móvil. Podrá arreglar eso ahora. Abra el archivo *Views\\Shared\\\_Layout.cshtml*. Justo debajo del elemento &lt;body\>, agregue el siguiente código, que procesa el widget modificador de vista:

    @Html.Partial("_ViewSwitcher")

El siguiente es el código completo:

    <body>
        @Html.Partial("_ViewSwitcher")

        <div id="title">
            <h1> MVC4 Conference </h1>
        </div>

        @*Items removed for clarity.*@
    </body>

Actualice la vista **Alltags** en el explorador móvil. Ahora puede navegar entre las vistas de escritorio y móvil.

![Navegación a las vistas móviles.](./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/windows-live-writer_asp_net-mvc-4-mobile-features_d2ff_p3_desktopviewwithmobilelink_thumb.png)

**Nota:**

Puede agregar el siguiente código al final de Views\\Shared\\\_ViewSwitcher.cshtml para ayudar a depurar las vistas al usar un explorador de la cadena de agente de usuario configurado en un dispositivo móvil.

``` {}
    else 
    { 
         @:Not Mobile/Get 
    } 
```

y se agrega el siguiente título al archivo Views\\Shared\\\_Layout.cshtml.

``` {}
    <h1>Non Mobile Layout MVC4 Conference</h1>
```

Vaya a la página Alltags en un explorador de escritorio. El widget de modificador de vista no se muestra en un explorador de escritorio, ya que se agrega solo a la página de diseño móvil. Más adelante en el tutorial verá cómo puede agregar el widget de modificador de vista a la vista de escritorio.

![Vista de la experiencia de escritorio.](./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/Windows-Live-Writer_ASP_NET-MVC-4-Mobile-Features_D2FF_p3_desktopBrowser_thumb.png)

Mejora de la lista de oradores
------------------------------

En el explorador móvil, seleccione el vínculo **Speakers**. Debido a que no hay una vista móvil (*AllSpeakers.Mobile.cshtml*), la visualización de los oradores predeterminados (*AllSpeakers.cshtml*) se procesa usando la vista de diseño móvil (*\_Layout.Mobile.cshtml*).

![Vista de la lista de oradores móviles.](./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/windows-live-writer_asp_net-mvc-4-mobile-features_d2ff_p3_speakersdesktop_thumb.png)

Puede deshabilitar globalmente una vista predeterminada (no móvil) de su procesamiento dentro de un diseño móvil al establecer RequireConsistentDisplayMode en true en el archivo *Views\_ViewStart.cshtml*, como a continuación:

![](./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/windows-live-writer_asp_net-mvc-4-mobile-features_d2ff_p3_speakersconsistent_thumb.png)

    @{
        Layout = "~/Views/Shared/_Layout.cshtml";
        DisplayModes.RequireConsistentDisplayMode = true;
    }

Cuando *RequireConsistentDisplayMode* se establece en true, el diseño móvil (*\_Layout.Mobile.cshtml*) se usa solo para las vistas móviles. (Es decir, el archivo de vista tiene la forma ViewName.Mobile.cshtml). Es posible que desee establecer *RequireConsistentDisplayMode* en true si el diseño móvil no funciona bien con las vistas que no son móviles. La siguiente captura de pantalla muestra cómo se procesa la página de los oradores cuando *RequireConsistentDisplayMode* se establece en true.

![](./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/windows-live-writer_asp_net-mvc-4-mobile-features_d2ff_ps_data_filter_thumb.png)

Puede deshabilitar el modo de visualización consistente en una vista mediante el establecimiento de *RequireConsistentDisplayMode* en false en el archivo de vista. El siguiente marcado en el archivo *Views\\Home\\AllSpeakers.cshtml* establece *RequireConsistentDisplayMode* en false:

    @model IEnumerable<string>
    @{
        ViewBag.Title = "All speakers";
        DisplayModes.RequireConsistentDisplayMode = false;
    }

Creación de una vista de oradores móviles
-----------------------------------------

Como acabamos de ver, la vista Speakers es legible, pero los vínculos son pequeños y difíciles de pulsar en un dispositivo móvil. En esta sección, podrá crear una vista de Speakers específica para móviles que se parece a una aplicación móvil moderna: con vínculos grandes y fáciles de pulsar con un cuadro de búsqueda para encontrar rápidamente a los oradores.

1.  Copie *AllSpeakers.cshtml* en *AllSpeakers.Mobile.cshtml.* Abra el archivo *AllSpeakers.Mobile.cshtml* y quite el elemento de encabezado &lt;h2\>.
2.  En la etiqueta **&lt;ul\>**, agregue el atributo de rol de datos y establezca su valor en *listview*. Al igual que otros *atributos data-**, \*data-role= "listview"* permite que los elementos de lista grandes sean más fáciles de pulsar. Así se ve el marcado completo:

         @model IEnumerable<string>
         @{
             ViewBag.Title = "All speakers";
         }
         <ul data-role="listview">
             @foreach(var speaker in Model) {
                 <li>@Html.ActionLink(speaker, "SessionsBySpeaker", new { speaker })</li>
             }
         </ul>

3.  Actualice el explorador móvil. La vista actualizada es la siguiente:

    ![](./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/windows-live-writer_asp_net-mvc-4-mobile-features_d2ff_p3_updatedspeakerview1_thumb.png)

4.  En la etiqueta **&lt;ul\>**, agregue el atributo de filtro de datos y establézcalo en true. El código siguiente se muestra el marcado ul.

        <ul data-role="listview" data-filter="true">

La imagen siguiente muestra el cuadro de filtro de búsqueda en la parte superior de la página que es el resultado del atributo de filtro de datos.

![](./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/windows-live-writer_asp_net-mvc-4-mobile-features_d2ff_ps_data_filter_thumb.png)

A medida que escribe cada letra en el cuadro de búsqueda, jQuery Mobile filtra la lista que se muestra como aparece en la siguiente imagen.

![](./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/windows-live-writer_asp_net-mvc-4-mobile-features_d2ff_ps_data_filter_sc_thumb.png)

Mejora de la lista de etiquetas
-------------------------------

Al igual que la vista Speakers predeterminada, la vista Tags es legible, pero los vínculos son pequeños y difíciles de pulsar en un dispositivo móvil. En esta sección, podrá arreglar la vista Tags de la misma manera que arregló la vista Speakers.

1.  Cambie el nombre del archivo *Views\\Home\\AllTags.Mobile.cshtml.hide* a *Views\\Home\\AllTags.Mobile.cshtml*. Abra el archivo con el nombre nuevo y quite el elemento **&lt;h2\>**.

2.  Agregue los atributos de rol de datos y filtro de datos a la etiqueta **&lt;ul\>**, como se muestra aquí:

         <ul data-role="listview" data-filter="true">

    La imagen a continuación muestra el filtrado de la página de etiquetas en la letra J.

![](./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/windows-live-writer_asp_net-mvc-4-mobile-features_d2ff_p3_tags_j_thumb.png)

Mejora la lista de fechas
-------------------------

Puede mejorar la vista Dates como ha mejorado las vistas **Speakers** y **Tags**, de modo que sean más fáciles de usar en un dispositivo móvil.

1.  Copie el archivo *Views\\Home\\AllDates.Mobile.cshtml* en *Views\\Home\\AllDates.Mobile.cshtml*.
2.  Abra el archivo nuevo y quite el elemento **&lt;h2\>**.
3.  Agregue *data-role ="listview"* a la etiqueta &lt;ul\>, como a continuación:

         <ul data-role="listview">

La siguiente imagen muestra cómo se ve la página **Date** con el atributo de rol de datos en su lugar.

![](./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/windows-live-writer_asp_net-mvc-4-mobile-features_d2ff_p3_dates1_thumb.png)

Reemplace el contenido del archivo *Views\\Home\\AllDates.Mobile.cshtml* con el siguiente código:

    @model IEnumerable<DateTime>
    @{
        ViewBag.Title = "All dates";
        DateTime lastDay = default(DateTime);
    }
    <ul data-role="listview">
        @foreach(var date in Model) {
            if (date.Date != lastDay) {
                lastDay = date.Date;
                <li data-role="list-divider">@date.Date.ToString("ddd, MMM dd")</li>
            }
            <li>@Html.ActionLink(date.ToString("h:mm tt"), "SessionsByDate", new { date })</li>
        }
    </ul>

Este código agrupa todas las sesiones por día. Crea un divisor de lista para cada día nuevo y enumera todas las sesiones de cada día bajo un divisor. A continuación se muestra cómo se ve cuando se ejecuta este código:

![](./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/windows-live-writer_asp_net-mvc-4-mobile-features_d2ff_p3_dates2_thumb.png)

Mejora de la vista SessionsTable
--------------------------------

En esta sección, podrá crear una vista de sesiones específica para móviles. Los cambios que se realicen serán más extensos que en las otras vistas que se han creado.

En el explorador móvil, toque el botón **Speaker**, a continuación, escriba Sc en el cuadro de búsqueda.

![](./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/windows-live-writer_asp_net-mvc-4-mobile-features_d2ff_ps_data_filter_sc_thumb_1.png)

Pulse el vínculo **Scott Hanselman**.

![](./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/windows-live-writer_asp_net-mvc-4-mobile-features_d2ff_p3_scottha_thumb.png)

Como puede ver, es difícil leer la pantalla en un explorador móvil. La columna de fecha es difícil de leer y la columna de las etiquetas está fuera de la vista. Para solucionar este problema, copie Views\*Home\\SessionsTable.cshtml\* en *Views\\Home\\SessionsTable.Mobile.cshtml* y, a continuación, reemplace el contenido del archivo con el siguiente código:

    @using MvcMobile.Models
    @model IEnumerable<Session>

    <ul data-role="listview">
        @foreach(var session in Model) {
            <li>
                <a href="@Url.Action("SessionByCode", new { session.Code })">
                    <h3>@session.Title</h3>
                    <p><strong>@string.Join(", ", session.Speakers)</strong></p>
                    <p>@session.DateText</p>
                </a>
            </li>
        }
    </ul>

El código elimina el espacio y la columnas de etiquetas, da formato al título, orador y la fecha verticalmente, por lo que toda esta información se puede leer en un explorador móvil. La imagen siguiente refleja los cambios en el código.

![](./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/windows-live-writer_asp_net-mvc-4-mobile-features_d2ff_ps_sessionsbyscottha_thumb.png)

Mejora de la vista SessionByCode
--------------------------------

Por último, vamos a crear una visión específica para móviles de la vista **SessionByCode**. En el explorador móvil, toque el botón **Speaker**, a continuación, escriba Sc en el cuadro de búsqueda.

![](./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/windows-live-writer_asp_net-mvc-4-mobile-features_d2ff_ps_data_filter_sc_thumb_2.png)

Pulse el vínculo **Scott Hanselman**. Se muestran las sesiones de Scott Hanselman.

![](./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/windows-live-writer_asp_net-mvc-4-mobile-features_d2ff_p3_scottha_thumb.png)

Seleccione el vínculo **An Overview of the MS Web Stack of Love**.

![](./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/windows-live-writer_asp_net-mvc-4-mobile-features_d2ff_ps_love_thumb.png)

La vista de escritorio predeterminada está bien, pero puede mejorarla.

Copie *Views\\Home\\SessionByCode.cshtml* en *Views\\Home\\SessionByCode.Mobile.cshtml* y reemplace el contenido del archivo *Views\\Home\\SessionByCode.Mobile.cshtml* con el siguiente marcado:

    @model MvcMobile.Models.Session

    @{
        ViewBag.Title = "Session details";
    }
    <h2>@Model.Title</h2>
    <p>
        <strong>@Model.DateText</strong> in <strong>@Model.Room</strong>
    </p>

    <ul data-role="listview" data-inset="true">
        <li data-role="list-divider">Speakers</li>
        @foreach (var speaker in Model.Speakers) {
            <li>@Html.ActionLink(speaker, "SessionsBySpeaker", new { speaker })</li>
        }
    </ul>

    <p>@Model.Description</p>
    <h4>Code: @Model.Code</h4>

    <ul data-role="listview" data-inset="true">
        <li data-role="list-divider">Tags</li>
        @foreach (var tag in Model.Tags) {
            <li>@Html.ActionLink(tag, "SessionsByTag", new { tag })</li>
        }
    </ul>

El nuevo marcado usa el atributo **data-role** para mejorar el diseño de la vista.

Actualice el explorador móvil. La siguiente imagen refleja los cambios en el código que acaba de hacer:

![](./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/windows-live-writer_asp_net-mvc-4-mobile-features_d2ff_p3_love2_thumb.png)

Implementación de la aplicación en el sitio web de Azure
--------------------------------------------------------

1.  En Visual Studio, haga clic con el botón secundario en el proyecto, en el **Explorador de soluciones**, y seleccione **Publicar** en el menú contextual.

    ![Opción Publicar del menú contextual del proyecto](./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/PublishVSSolution.png)

    Se abre el asistente para **publicación web**.

2.  En la pestaña **Perfil** del asistente para **publicación web**, haga clic en **Importar**.

    ![Importar configuración de publicación](./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/ImportPublishSettings.png)

    Aparecerá el cuadro de diálogo **Import Publish Profile**.

3.  Lleve a cabo los siguientes pasos si no ha agregado anteriormente su suscripción de Azure en Visual Studio: con estos pasos agregará su suscripción, de manera tal que la lista desplegable que aparece bajo **Import from an Azure web site** incluirá su sitio web.

    1.  En el cuadro de diálogo **Import Publish Profile**, haga clic en **Add Azure subscription**.

    ![Incorporación de sub a win az](./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/rzAddWAsub.png)

    1.  En el cuadro de diálogo **Import Azure Subscriptions**, haga clic en **Download subscription file**.

    ![Descarga de sub](./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/rzDownLoad.png)

    1.  En la ventana del explorador, guarde el archivo *.publishsettings*.

    ![Descarga de archivo pub](./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/rzDown2.png)

    [WACOM.INCLUDE [publishsettingsfilewarningchunk](../includes/publishsettingsfilewarningchunk.md)] &lt;/br\>

    1.  En el cuadro de diálogo **Import Azure Subscriptions**, haga clic en **Browse** y vaya al archivo *.publishsettings*.

    ![Descarga de sub](./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/rzDownLoad.png)

    1.  Haga clic en **Import**.

    ![Importación](./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/rzImp.png)

    1.  En el cuadro de diálogo **Import Publish Profile**, seleccione **Import from an Azure web site**, seleccione su sitio web en la lista desplegable y, a continuación, haga clic en **OK**.

    ![Importar perfil de publicación](./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/ImportPublishProfile.png)

    1.  En la pestaña **Conexión**, haga clic en **Validate Connection** para asegurarse de que la configuración es correcta.

    ![Validar conexión](./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/ValidateConnection.png)

    1.  Una vez que se valide la conexión, aparecerá una marca de verificación verde junto al botón **Validate Connection**. 
 
      ![Icono de conexión correcta y botón Next en la pestaña Conexión](./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/dntutmobile-deploy1-publish-005.png)

    1.  Puede aceptar todos los valores predeterminados de esta página. Está implementando una configuración de compilación de la versión y no es necesario eliminar los archivos en el servidor de destino. La entrada **UsersContext (DefaultConnection)** en **Databases** proviene de la clase *UsersContext:DbContext* que usa la cadena DefaultConnection. Haga clic en **Next**.

    ![Icono de conexión correcta y botón Next en la pestaña Conexión](./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/rxPWS.png)

    1.  En la pestaña **Preview**, haga clic en **Start Preview**. En esta pestaña aparece la lista de archivos que se copiarán en el servidor. Para publicar la aplicación no es necesario mostrar la vista previa, pero se trata de una función útil que conviene conocer. En este caso, no tiene que hacer nada con la lista de archivos que se muestra. La próxima vez que publique, solo estarán en la lista de vista previa los archivos que se han cambiado.

    ![Botón StartPreview de la pestaña Preview ](./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/dntutmobile-deploy1-publish-007.png)

    1.  Haga clic en **Publicar**.

    Visual Studio comienza el proceso de copiar los archivos en el servidor de Azure. La ventana **Output** muestra qué acciones de implementación se realizaron e informa de la correcta finalización de la implementación.

    1.  El explorador predeterminado se dirige automáticamente a la URL del sitio web implementado. La aplicación creada ahora se ejecuta en la nube.

    ![](./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/depoly_mobile_new_website_15.png)

Puede probar su sitio web activo con el emulador de teléfono dirigiéndose a la dirección URL del sitio en el explorador móvil.

