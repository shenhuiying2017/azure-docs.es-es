<properties urlDisplayName="ASP.NET MVC 5 mobile website" pageTitle="Sitio web móvil de .NET ASP.NET MVC 5 - tutoriales de Azure" metaKeywords="tutorial de Azure, tutorial de aplicaciones web de Azure, aplicación móvil de Azure, Azure ASP.NET MVC 5,,ASP.NET MVC" description="A tutorial that teaches you how to deploy a web application to an Azure website using mobile features in ASP.NET MVC 5 web application." metaCanonical="" services="web-sites" documentationCenter=".NET" title="Deploy an ASP.NET MVC Mobile Web Application on Azure Websites" authors="cephalin,riande" solutions="" manager="wpickett" editor="mollybos" />

<tags ms.service="web-sites" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="dotnet" ms.topic="article" ms.date="08/19/2014" ms.author="cephalin,riande" />


# Implementación de una aplicación web móvil ASP.NET MVC 5 en Sitios web Azure

Este tutorial le enseñará los aspectos básicos de la creación de una aplicación web de ASP.NET MVC 5
preparada para el entorno móvil y cómo implementarla en Microsoft Azure. Para este tutorial, necesita 
[Visual Studio Express 2013 para Web][Visual Studio Express 2013]
o la edición profesional de Visual Studio si ya
tiene esa.

[WACOM.INCLUDE [create-account-and-websites-note](../includes/create-account-and-websites-note.md)]

### Lo que creará

En este tutorial, agregará características móviles a la aplicación sencilla de listados de conferencia que se proporciona en el [proyecto inicial][StarterProject]. En la captura siguiente se muestran las sesiones ASP.NET en la aplicación completada, tal como se muestra en el emulador de explorador en las herramientas de desarrollador de Internet Explorer 11 F12.

![][FixedSessionsByTag]

Puede usar las herramientas de desarrollador de Internet Explorer 11 F12 y la herramienta [Fiddler][Fiddler] para ayudarle a depurar su aplicación. 

### Habilidades que aprenderá

Aprenderá lo siguiente:

-	Cómo usar Visual Studio 2013 para publicar su aplicación web directamente en un sitio web de Windows Azure.
-   Uso del marco CSS Bootstrap en las plantillas de ASP.NET MVC 5 para mejorar la visualización en los dispositivos móviles.
-   Creación de vistas específicos de móviles para dirigirse a exploradores móviles específicos, tales como iPhone y Android
-   Creación de vistas de respuesta (vistas que responden a exploradores distintos entre varios dispositivos)

<h2>Configuración del entorno de desarrollo</h2>

Configure el entorno de desarrollo mediante la instalación del SDK de Azure para .NET Framework. 

1. Para instalar el SDK de Azure para .NET, haga clic en el vínculo a continuación. Si no tiene instalado todavía Visual Studio 2013, se instalará a través del vínculo. Este tutorial requiere Visual Studio 2013. [SDK de Azure para Visual Studio 2013][AzureSDKVs2013]
1. En la ventana del instalador de la plataforma web, haga clic en **Install** (Instalar) y continúe con la instalación.

	![Web Platform Installer - Azure SDK for .NET][WebPIAzureSdk23NetVS13]

También necesitará un emulador de explorador móvil. Funcionará cualquiera de las siguientes opciones
:

-   Emulador de explorador en [las herramientas para desarrolladores de Internet Explorer 11 F12][EmulatorIE11] (se usan en todas las
    capturas de pantalla de explorador). Cuenta con los valores predefinidos de cadena de agente de usuario para Windows Phone 8, Windows Phone 7 y Apple iPad.
-	Emulador de explorador en [Google Chrome DevTools][EmulatorChrome]. Contiene valores predefinidos para varios dispositivos Android, así como Apple iPhone, Apple iPad y Amazon Kindle Fire. También emula eventos táctiles.
-   [Emulador de Opera Mobile][EmulatorOpera]

Hay proyectos de Visual Studio con código fuente de C# disponibles para acompañar
este tema:

-   [Descarga del proyecto inicial][StarterProject]
-   [Descarga del proyecto completado][CompletedProject]

<h2>Pasos en este tutorial</h2>

- [Implementación del proyecto inicial en un sitio web de Windows Azure][]
- [Marco Bootstrap CSS][]
- [Reemplazo de las vistas, diseños y vistas parciales][]
- [Mejora de la lista de oradores][]
- [Mejora de la lista de etiquetas][]
- [Mejora la lista de fechas][]
- [Mejora de la vista SessionsTable][]
- [Mejora de la vista SessionByCode][]

<h3><a name="bkmk_DeployStarterProject"></a>Implementación del proyecto inicial en un sitio web de Windows Azure</h3>

1.	Descargue el [proyecto inicial][StarterProject] de la aplicación de lista de conferencias.

2. 	Luego, en el Explorador de Windows, haga clic con el botón secundario en el archivo Mvc5Mobile.zip y seleccione *Propiedades*.

3. 	En el cuadro de diálogo **Propiedades de Mvc5Mobile.zip**,
elija el botón **Desbloquear**. (El desbloqueo impide que se produzca
una advertencia de seguridad cuando intenta usar un archivo *.zip* que haya descargado
desde el web).

4.	Haga clic con el botón secundario en el archivo *Mvc5Mobile.zip* y seleccione **Extraer todo** to
para descomprimir el archivo. 

5. 	En Visual Studio, abra el archivo *Mvc5Mobile.sln*.

6.  En el Explorador de soluciones, haga clic con el botón secundario en el proyecto y haga clic en **Publicar**.

	![][DeployClickPublish]

7.	En Publicación web, haga clic en **Sitios web Microsoft Azure**.

	![][DeployClickWebSites]

8.	Haga clic en **Iniciar sesión**.

	![][DeploySignIn]

9.	Escriba el nombre de usuario de Microsoft Azure y haga clic en **Continuar**.

	![][DeployUsername]

10.	Escriba su contraseña y haga clic en **Iniciar sesión**.

	![][DeployPassword]

11. El cuadro de diálogo Seleccionar sitio web existente debería mostrarle como que ha iniciado sesión. Haga clic en **Nuevo**.

	![][DeployNewWebsite]  

12. En el campo **Nombre del sitio** especifique un prefijo de nombre de sitio único. El nombre completo de su sitio será *&lt;prefix>*.azurewebsites.net. También, en el campo **Región**, seleccione una región. Después, haga clic en **Crear**.

	![][DeploySiteSettings]

13.	El cuadro de diálogo Publicación web se rellenará con los valores de configuración de su nuevo sitio web. Haga clic en **Publicar**.

	![][DeployPublishSite]

	Cuando Visual Studio haya completado la publicación del proyecto inicial en Sitios web de Windows Azure, el explorador de escritorio se abrirá para mostrar el sitio web activo.

14.	Inicie su emulador de explorador móvil, copie la dirección URL de la aplicación de conferencia (*<prefix>*.azurewebsites.net) en el emulador y luego haga clic en el botón superior derecho y seleccione **Explorar por etiqueta**. Si usa Internet Explorer 11 como explorador predeterminado, solo tiene que escribir `F12`, luego `Ctrl+8` y, a continuación, cambiar el perfil del explorador a **Windows Phone**. La siguiente imagen muestra la vista *AllTags* en modo vertical (a partir de la selección de **Explorar por etiqueta**).

	![][AllTags]

>[WACOM.NOTE] Si bien puede depurar la aplicación MVC 5 en Visual Studio, puede volver a publicar su sitio web en Windows Azure para comprobar el sitio activo directamente desde el explorador móvil o un emulador de explorador.

La pantalla es muy fácil de leer en un dispositivo móvil. También puede ver algunos de los efectos visuales aplicados por el marco Bootstrap CSS. Haga clic en el vínculo **ASP.NET**.

![][SessionsByTagASP.NET]

La vista de etiquetas ASP.NET se ajusta en zoom a la pantalla, que Bootstrap hace automáticamente. No obstante, puede mejorar esta vista para que se adapte mejor al explorador móvil. Por ejemplo, la columna **Fecha** es difícil de leer. Más adelante en el tutorial, cambiará la vista *AllTags* para que se ajuste mejor a exploradores móviles.

<h2><a name="bkmk_bootstrap"></a>Marco Bootstrap CSS</h2>

La plantilla MVC 5 ahora cuenta con compatibilidad con Bootstrap integrada. Ya ha visto cómo mejora inmediatamente las distintas vistas en su aplicación. Por ejemplo, la barra de navegación de la parte superior se contrae automáticamente cuando el ancho del explorador es menor. En el explorador de escritorio, intente cambiar el tamaño de la ventana del explorador para ver cómo la barra de navegación cambia su aspecto. Este es el diseño web de respuesta integrado en Bootstrap.

Para ver qué aspecto tendrá la aplicación web sin Bootstrap, abra
*App\_Start\\BundleConfig.cs* y comente las líneas que contienen
*bootstrap.js* y *bootstrap.css*. El código siguiente muestra las dos
últimas instrucciones del método 'RegisterBundles' después del cambio:

     bundles.Add(new ScriptBundle("~/bundles/bootstrap").Include(
              //"~/Scripts/bootstrap.js",
              "~/Scripts/respond.js"));

    bundles.Add(new StyleBundle("~/Content/css").Include(
              //"~/Content/bootstrap.css",
              "~/Content/site.css"));

Presione `CTRL+F5` para ejecutar la aplicación.

Observe que la barra de navegación contraíble ahora es simplemente una lista no ordenada. Haga clic de nuevo en **Examinar por etiqueta** y luego haga clic en **ASP.NET**. En la vista del emulador de dispositivos móviles, puede ver ahora que ya no se ajusta en zoom a la pantalla y que debe desplazarse lateralmente para ver el lado derecho de la tabla.

![][SessionsByTagASP.NETNoBootstrap]

Deshaga los cambios y actualice el explorador móvil para comprobar que se haya restaurado la visualización para exploradores móviles.

Bootstrap no es específico de ASP.NET MVC 5 y puede aprovechar estas características en cualquier aplicación web. Sin embargo, ahora está integrado en la plantilla de proyecto de ASP.NET MVC 5, por lo que su aplicación web MVC 5 puede sacar provecho de Bootstrap de manera predeterminada.

Para obtener más información sobre Bootstrap, vaya al sitio de [Bootstrap][BootstrapSite].

En la sección siguiente, verá cómo proporcionar vistas específicas de explorador móvil
.

<h2><a name="bkmk_overrideviews"></a>Reemplazo de las vistas, diseños y vistas parciales</h2>

Puede reemplazar cualquier vista (incluidos los diseños y las vistas parciales) para exploradores móviles en general, para un explorador móvil individual o para cualquier explorador específico. Para proporcionar una vista específica para móviles, puede copiar un archivo de vista y agregar *.Mobile* al nombre del archivo. Por ejemplo, para crear una vista móvil *Index*, puede copiar *Views\\Home\\Index.cshtml* en *Views\\Home\\Index.Mobile.cshtml*.

En esta sección, creará un archivo de diseño específico para móviles.

Para empezar, copie *Views\\Shared\\\_Layout.cshtml* en *Views\\Shared\\\_Layout.Mobile.cshtml*. Abra *\_Layout.Mobile.cshtml* y cambie el título **Aplicación MVC5** por **Aplicación MVC5 (móvil)**.

En cada llamada de `Html.ActionLink` de la barra de navegación, elimine "Explorar por" de cada vínculo *ActionLink*. El código siguiente muestra la etiqueta completada `<ul class="nav navbar-nav">` del archivo de diseño móvil.

    <ul class="nav navbar-nav">
        <li>@Html.ActionLink("Home", "Index", "Home")</li>
        <li>@Html.ActionLink("Date", "AllDates", "Home")</li>
        <li>@Html.ActionLink("Speaker", "AllSpeakers", "Home")</li>
        <li>@Html.ActionLink("Tag", "AllTags", "Home")</li>
    </ul>

Copie el archivo *Views\\Home\\AllTags.cshtml* en *Views\\Home\\AllTags.Mobile.cshtml*. Abra el archivo nuevo y cambie el elemento `<h2>` de "Tags" a "Tags (M)":

    <h2>Tags (M)</h2>

Diríjase a la página de etiquetas usando un explorador de escritorio y un emulador de explorador móvil. El emulador de explorador móvil muestra los dos cambios que ha realizado (el título en *\_Layout.Mobile.cshtml* y el título en *AllTags.Mobile.cshtml*).

![][AllTagsMobile_LayoutMobile]

Por el contrario, la pantalla de escritorio no ha cambiado (con los títulos en *\_Layout.cshtml* and  *AllTags.cshtml*).

![][AllTagsMobile_LayoutMobileDesktop]

<h2><a name="bkmk_browserviews"></a>Creación de vistas específicas de explorador</h2>

Además de vistas específicas de explorador móvil y de explorador de escritorio, puede crear vistas para un explorador individual. Por ejemplo, puede crear vistas específicas para el explorador de iPhone o Android. En esta sección, creará un diseño para el explorador de iPhone y una versión de iPhone de la vista *AllTags*.

Abra el archivo *Global.asax* y agregue el código siguiente al final del método `Application_Start`.

    DisplayModeProvider.Instance.Modes.Insert(0, new DefaultDisplayMode("iPhone")
    {
        ContextCondition = (context => context.GetOverriddenUserAgent().IndexOf
            ("iPhone", StringComparison.OrdinalIgnoreCase) >= 0)
    });

Este código define un nuevo modo de visualización llamado "iPhone" que se comparará con cada solicitud entrante. Si la solicitud entrante coincide con la condición definida (es decir, si el agente de usuario contiene la cadena "iPhone"), ASP.NET MVC buscará vistas cuyo nombre contenga el sufijo "iPhone".

>[WACOM.NOTE] Al agregar modos de visualización específica de explorador móvil, tal como para iPhone y Android, asegúrese de establecer el primer argumento en `0` (insértelo al principio de la lista) para que el modo específico de explorador tenga prioridad sobre la plantilla móvil (*.Mobile.cshtml). Si la plantilla móvil está al principio de la lista, se seleccionará sobre el modo de visualización esperado (la primera coincidencia tiene prioridad y la plantilla móvil coincide con todos los exploradores móviles). 

En el código, haga clic con el botón secundario en `DefaultDisplayMode`, elija **Resolve** (Resolver) y
luego elija 'using System.Web.WebPages;'. Esto agrega una referencia al espacio de nombres
`System.Web.WebPages`, que es donde se definen los tipos
`DisplayModeProvider` y `DefaultDisplayMode`.

![][ResolveDefaultDisplayMode]

Como alternativa, puede agregar manualmente la siguiente línea a la
sección 'using' del archivo.

    using System.Web.WebPages;

Guarde los cambios. Copie el archivo *Views\\Shared\\\_Layout.Mobile.cshtml* en *Views\\Shared\\\_Layout.iPhone.cshtml*. Abra el archivo nuevo y luego cambie el título de 'Aplicación MVC5 (móvil)' a 'Aplicación MVC5 (iPhone)'.

Copie el archivo *Views\\Home\\AllTags.Mobile.cshtml* en *Views\\Home\\AllTags.iPhone.cshtml*. En el archivo nuevo, cambie el elemento `<h2>` de "Tags (M)" a "Tags (iPhone)".

Ejecute la aplicación. Ejecute el emulador de explorador móvil, asegúrese de que su agente de usuario esté establecido en "iPhone" y diríjase a la vista *AllTags*. Si usa el emulador de las herramientas para desarrolladores de Internet Explorer 11 F12, configure la emulación según se indica a continuación:

-   Perfil de explorador = **Windows Phone**
-   Cadena de agente de usuario =  **Custom**
-   Cadena personalizada = **Apple-iPhone5C1/1001.525**

En la captura siguiente se muestra la vista *AllTags* representada en el emulador de las herramientas para desarrolladores de Internet Explorer 11 F12 con la cadena de agente de usuario personalizada (es una cadena de agente de usuario de iPhone 5C).

![][AllTagsIPhone_LayoutIPhone]

En el explorador móvil, seleccione el vínculo **Speakers**. Como no hay una vista móvil (*AllSpeakers.Mobile.cshtml*), la vista de oradores predeterminada (*AllSpeakers.cshtml*) se procesa con la vista de diseño móvil (*\_Layout.Mobile.cshtml*). Tal y como se muestra a continuación, el título **Aplicación MVC5 (móvil)** se define en *\_Layout.Mobile.cshtml*.

![][AllSpeakers_LayoutMobile]

Puede deshabilitar globalmente una vista predeterminada (no móvil) de su procesamiento dentro de un diseño móvil al establecer `RequireConsistentDisplayMode` en `true` en el archivo *Views\\\_ViewStart.cshtml*, como a continuación:

    @{
        Layout = "~/Views/Shared/_Layout.cshtml";
        DisplayModeProvider.Instance.RequireConsistentDisplayMode = true;
    }

Cuando se establece `RequireConsistentDisplayMode` en `true`, el diseño móvil (*\_Layout.Mobile.cshtml*) solo se usa en las vistas móviles (es decir, cuando el archivo de vista tiene el formato ***ViewName**.Mobile.cshtml*). Es posible que desee establecer `RequireConsistentDisplayMode` en `true` si el diseño móvil no funciona bien con las vistas que no son móviles. En la siguiente captura de pantalla se muestra cómo se procesa la página *Speakers* cuando `RequireConsistentDisplayMode` está establecido en `true` (sin la cadena "(Mobile)" e la barra de navegación de la parte superior).

![][AllSpeakers_LayoutMobileOverridden]

Puede deshabilitar el modo de visualización coherente en una vista específica al configurar `RequireConsistentDisplayMode` como `false` en el archivo de vista. El marcado siguiente en el archivo *Views\\Home\\AllSpeakers.cshtml* establece 'RequireConsistentDisplayMode' en 'false':

    @model IEnumerable<string>

    @{
        ViewBag.Title = "All speakers";
        DisplayModeProvider.Instance.RequireConsistentDisplayMode = false;
    }

En esta sección hemos visto cómo crear vistas y diseños móviles y cómo crear vistas y diseños para dispositivos específicos, como el iPhone. Sin embargo, la ventaja principal del marco Bootstrap CSS es el diseño con capacidad de respuesta, lo que significa que se puede aplicar una sola hoja de estilos en los exploradores de escritorio, teléfono y tableta para crear una apariencia coherente. En la sección siguiente, verá cómo usar Bootstrap para crear vistas para dispositivos móviles.

<h2><a name="bkmk_Improvespeakerslist"></a> Mejora de la lista de oradores</h2>

Como acabamos de ver, la vista *Speakers* es legible, pero los vínculos son pequeños y difíciles de pulsar en un dispositivo móvil. En esta sección, adaptará la vista *AllSpeakers* al entorno móvil para que se muestren vínculos grandes y fáciles de pulsar y para que contenga un cuadro de búsqueda que permita encontrar rápidamente a los oradores.

Puede usar el estilo de [grupo de listas vinculadas][] de Bootstrap para mejorar la vista *Speakers*. En *Views\\Home\\AllSpeakers.cshtml*, sustituya el contenido del archivo de Razor por el siguiente código.

     @model IEnumerable<string>

    @{
        ViewBag.Title = "All Speakers";
    }

    <h2>Speakers</h2>

    <div class="list-group">
        @foreach (var speaker in Model)
        {
            @Html.ActionLink(speaker, "SessionsBySpeaker", new { speaker }, new { @class = "list-group-item" })
        }
    </div>

El atributo `class="list-group"` de la etiqueta `<div>` aplica el estilo de lista de
Bootstrap y el atributo `class="input-group-item"
aplica el estilo de elementos de lista de Bootstrap a cada vínculo.

Actualice el explorador móvil. La vista actualizada es la siguiente:

![][AllSpeakersFixed]

El estilo de [grupo de listas vinculadas][] de Bootstrap permite hacer clic en todo el cuadro de cada vínculo, lo que ofrece una experiencia de usuario mucho mejor. Cambie a la vista de escritorio y observe el aspecto coherente.

![][AllSpeakersFixedDesktop]

Aunque la vista de explorador móvil ha mejorado, es difícil desplazarse por la larga lista de oradores. Bootstrap no proporciona una funcionalidad de filtro de búsqueda de forma predeterminada, pero puede agregarla con unas pocas líneas de código. En primer lugar, agregará un cuadro de búsqueda a la vista y luego lo enlazará con el código JavaScript para la función de filtro. En *Views\\Home\\AllSpeakers.cshtml*, agregue una etiqueta \<form\> justo después de la etiqueta \<h2\>, como se muestra a continuación:

    @model IEnumerable<string>

    @{
        ViewBag.Title = "All Speakers";
    }

    <h2>Speakers</h2>

    <form class="input-group">
        <span class="input-group-addon"><span class="glyphicon glyphicon-search"></span></span>
        <input type="text" class="form-control" placeholder="Search speaker">
    </form>
    <br />
    <div class="list-group">
        @foreach (var speaker in Model)
        {
            @Html.ActionLink(speaker, 
                             "SessionsBySpeaker", 
                             new { speaker }, 
                             new { @class = "list-group-item" })
        }
    </div>

Observe que a las etiquetas '<form>' y '<input>' se les han aplicado los estilos de Bootstrap. El elemento `<span>` agrega un arranque [glyphicon][] de Bootstrap a search box.

In the *Scripts* folder, add a JavaScript file called *filter.js*. Open
the file and paste the following code into it:

    $(function () {

        // reset the search form when the page loads
        $("form").each(function () {
            this.reset();
        });

        // wire up the events to the <input> element for search/filter
        $("input").bind("keyup change", function () {
            var searchtxt = this.value.toLowerCase();
            var items = $(".list-group-item");

            // show all speakers that begin with the typed text and hide others
            for (var i = 0; i < items.length; i++) {
                var val = items[i].text.toLowerCase();
                val = val.substring(0, searchtxt.length);
                if (val == searchtxt) {
                    $(items[i]).show();
                }
                else {
                    $(items[i]).hide();
                }
            }
        });
    });

También debe incluir filter.js en los paquetes registrados. Abra *App\_Start\\BundleConfig.cs* y cambie los primeros paquetes. Cambie la primera instrucción `bundles.Add` (para el paquete **jquery**) para que incluya *Scripts\\filter.js*, de la manera siguiente: 

     bundles.Add(new ScriptBundle("~/bundles/jquery").Include(
                "~/Scripts/jquery-{version}.js",
                "~/Scripts/filter.js"));

El paquete **jquery** ya lo ha procesado la vista predeterminada *\_Layout*
. Más adelante, puede utilizar el mismo código de JavaScript para aplicar la
funcionalidad de filtro a otras vistas de lista.

Refresh the mobile browser and go to the *AllSpeakers* view. In the
search box, type "sc". The speakers list should now be filtered
according to your search string.

![][AllSpeakersFixedSearchBySC]

<h2><a name="bkmk_improvetags"></a> Mejora de la lista de etiquetas</h2>

Al igual que la vista *Speakers*, la vista *Tags* es legible, pero los vínculos
son pequeños y difíciles de pulsar en un dispositivo móvil. Puede corregir la vista *Tags* de la misma manera que ha corregido la vista *Speakers*, si usa los cambios de código descritos anteriormente, pero con la siguiente sintaxis de método `Html.ActionLink` en *Views\\Home\\AllTags.cshtml*:

    @Html.ActionLink(tag, 
                     "SessionsByTag", 
                     new { tag }, 
                     new { @class = "list-group-item" })

El explorador de escritorio actualizado tiene el aspecto siguiente:

![][AllTagsFixedDesktop]

El explorador móvil actualizado tiene el aspecto siguiente: 

![][AllTagsFixed]

>[WACOM.NOTE] Si observa que el formato de la lista original aún está presente en el explorador móvil y se pregunta qué sucedió con el estilo de Bootstrap, se trata de un artefacto de la acción anterior para crear vistas específicas para dispositivos móviles. No obstante, dado que está usando el marco Bootstrap CSS para crear un diseño web con respuesta, puede quitar estas vistas específicas de dispositivos móviles y las vistas de diseño específicas de dispositivos móviles. Cuando lo haya hecho, el explorador móvil actualizado mostrará el estilo Bootstrap.

<h2><a name="bkmk_improvedates"></a> Mejora la lista de fechas</h2>

Puede mejorar la vista *Dates* del mismo modo que ha mejorado las vistas *Speakers* y
*Tags* si usa los cambios de código descritos anteriormente, pero con la siguiente sintaxis de método `Html.ActionLink` en *Views\\Home\\AllDates.cshtml*:

    @Html.ActionLink(date.ToString("ddd, MMM dd, h:mm tt"), 
                     "SessionsByDate", 
                     new { date }, 
                     new { @class = "list-group-item" })

Obtendrá una vista de explorador móvil actualizada con el aspecto siguiente:

![][AllDatesFixed]

Puede mejorar aún más la vista *Dates* organizando los valores de
fecha y hora por fecha. Esto puede hacerse con el estilo de
[paneles][] de Bootstrap. Sustituya
el contenido del archivo *Views\\Home\\AllDates.cshtml* por el
código siguiente:

    @model IEnumerable<DateTime>

    @{
        ViewBag.Title = "All Dates";
    }

    <h2>Dates</h2>

    @foreach (var dategroup in Model.GroupBy(x=>x.Date))
    {
        <div class="panel panel-primary">
            <div class="panel-heading">
                @dategroup.Key.ToString("ddd, MMM dd")
            </div>
            <div class="panel-body list-group">
                @foreach (var date in dategroup)
                {
                    @Html.ActionLink(date.ToString("h:mm tt"), 
                                     "SessionsByDate", 
                                     new { date }, 
                                     new { @class = "list-group-item" })
                }
            </div>
        </div>
    }

Este código crea una etiqueta aparte `<div class="panel panel-primary">` para
cada fecha distinta de la lista, y usa el [grupo de listas vinculadas][] para los
vínculos respectivos como antes. Este es el aspecto del explorador móvil
cuando se ejecuta este código:

![][AllDatesFixed2]

Cambie al explorador de escritorio. Nuevamente, observe el aspecto coherente.

![][AllDatesFixed2Desktop]

<h2><a name="bkmk_improvesessionstable"></a> Mejora de la vista SessionsTable</h2>

En esta sección, modificará la vista *SessionsTable* para que se adapte mejor al entorno móvil. Este cambio es más amplio que los cambios anteriores.

En el explorador móvil, toque el botón **Tag** y luego escriba `asp` en el cuadro de búsqueda.

![][AllTagsFixedSearchByASP]

Toque el vínculo **ASP.NET**.

![][SessionsTableTagASP.NET]

Como puede observar, la pantalla tiene el formato de una tabla, que está diseñada para visualizarse en un explorador de escritorio. Sin embargo, es un poco difícil leerla en un explorador móvil. Para solucionar este problema, abra el archivo *Views\\Home\\SessionsTable.cshtml* y sustituya el contenido del archivo por el código siguiente:

    @model IEnumerable<Mvc5Mobile.Models.Session>

    <h2>@ViewBag.Title</h2>

    <div class="container">
        <div class="row">
            @foreach (var session in Model)
            {
                <div class="col-md-4">
                    <div class="list-group">
                        @Html.ActionLink(session.Title, 
                                         "SessionByCode", 
                                         new { session.Code }, 
                                         new { @class="list-group-item active" })
                        <div class="list-group-item">
                            <div class="list-group-item-text">
                                @Html.Partial("_SpeakersLinks", session)
                            </div>
                            <div class="list-group-item-info">
                                @session.DateText
                            </div>
                            <div class="list-group-item-info small hidden-xs">
                                @Html.Partial("_TagsLinks", session)
                            </div>
                        </div>
                    </div>
                </div>
            }
        </div>
    </div>

El código hace tres cosas:

-   usa el [grupo de listas vinculadas personalizado][] de Bootstrap para que la información de sesión tenga un formato vertical, de modo que se pueda leer en un explorador móvil (mediante clases como, por ejemplo, list-group-item-text).
-   aplica el [sistema de cuadrículas][] al diseño, de modo que los elementos de la sesión fluyan horizontalmente en el explorador de escritorio y verticalmente en el explorador móvil (mediante la clase col-md-4)
-   usa las [utilidades con respuesta][] para ocultar las etiquetas de sesión cuando se visualizan en el explorador móvil (mediante la clase hidden-xs)

También puede pulsar un vínculo de título para dirigirse a la respectiva sesión. La imagen siguiente refleja los cambios en el código.

![][FixedSessionsByTag]

El sistema de cuadrículas de Bootstrap que aplicó organiza automáticamente las sesiones de forma vertical en el explorador móvil. Asimismo, observe que no se muestran las etiquetas. Cambie al explorador de escritorio.

![][SessionsTableFixedTagASP.NETDesktop]

En el explorador de escritorio, observe que las etiquetas ahora se muestran. Además, puede ver que el sistema de cuadrículas de Bootstrap que aplicó organiza los elementos de la sesión en dos columnas. Si aumenta el tamaño del explorador, verá que la organización cambia a tres columnas.

<h2><a name="bkmk_improvesessionbycode"></a> Mejora de la vista SessionByCode</h2>

Por último, corregirá la vista *SessionByCode* para que se adapte mejor a los dispositivos móviles.

En el explorador móvil, toque el botón **Tag** y luego escriba `asp` en el
cuadro de búsqueda.

![][AllTagsFixedSearchByASP]

Toque el vínculo **ASP.NET**. Se muestran las sesiones de la etiqueta ASP.NET.

![][FixedSessionsByTag]

Seleccione el vínculo para **crear una aplicación de una sola página con ASP.NET y
AngularJS**.

![][SessionByCode3-644]

La vista de escritorio predeterminada está bien, pero puede mejorar su aspecto fácilmente mediante el uso de componentes de la GUI de Bootstrap.

Abra *Views\\Home\\SessionByCode.cshtml* y sustituya el contenido por
el siguiente marcado:

    @model Mvc5Mobile.Models.Session

    @{
        ViewBag.Title = "Session details";
    }
    <h3>@Model.Title (@Model.Code)</h3>
    <p>
        <strong>@Model.DateText</strong> in <strong>@Model.Room</strong>
    </p>

    <div class="panel panel-primary">
        <div class="panel-heading">
            Speakers
        </div>
        @foreach (var speaker in Model.Speakers)
        {
            @Html.ActionLink(speaker, 
                             "SessionsBySpeaker", 
                             new { speaker }, 
                             new { @class="panel-body" })
        }
    </div>

    <p>@Model.Abstract</p>

    <div class="panel panel-primary">
        <div class="panel-heading">
            Tags
        </div>
        @foreach (var tag in Model.Tags)
        {
            @Html.ActionLink(tag, 
                             "SessionsByTag", 
                             new { tag }, 
                             new { @class = "panel-body" })
        }
    </div>

El nuevo marcado usa el estilo de paneles de Bootstrap para mejorar la vista móvil. 

Actualice el explorador móvil. La siguiente imagen refleja los cambios de código
que acaba de realizar:

![][SessionByCodeFixed3-644]

Resumen y revisión
------------------

Este tutorial le ha mostrado cómo usar ASP.NET MVC 5 para desarrollar
aplicaciones web para móviles. Se han tratado los temas siguientes:

-	Implementación de una aplicación ASP.NET MVC 5 a un sitio web de Windows Azure
-   Uso de Bootstrap para crear un diseño web con respuesta en su
    aplicación de MVC 5.
-   Invalidación del diseño, las vistas y las vistas parciales, tanto de manera global como para una
    vista individual
-   Control del diseño y aplicación de la invalidación parcial mediante la propiedad
    `RequireConsistentDisplayMode`
-   Creación de vistas dirigidas a exploradores específicos, tal como el explorador de
    iPhone
-   Aplicación del estilo Bootstrap en el código de Razor

Otras referencias
--------

-   [Sitio de Bootstrap][BootstrapSite]
-   [Blog oficial de Bootstrap][]
-   [Tutorial de Twitter Bootstrap de Tutorial Republic][]
-   [Bootstrap Playground][]
-   [Prácticas recomendadas y recomendaciones de W3C para aplicaciones web móviles][]
-   [Recomendación de candidatos de W3C para consultas multimedia][]

<!-- Internal Links -->
[Implementación del proyecto inicial en un sitio web de Windows Azure]: #bkmk_DeployStarterProject
[Marco Bootstrap CSS]: #bkmk_bootstrap
[Reemplazo de las vistas, diseños y vistas parciales]: #bkmk_overrideviews
[Creación de vistas específicas de explorador]:#bkmk_browserviews
[Mejora de la lista de oradores]: #bkmk_Improvespeakerslist
[Mejora de la lista de etiquetas]: #bkmk_improvetags
[Mejora la lista de fechas]: #bkmk_improvedates
[Mejora de la vista SessionsTable]: #bkmk_improvesessionstable
[Mejora de la vista SessionByCode]: #bkmk_improvesessionbycode

<!-- External Links -->
[Visual Studio Express 2013]: http://www.visualstudio.com/downloads/download-visual-studio-vs#d-express-web
[AzureSDKVs2013]: http://go.microsoft.com/fwlink/p/?linkid=323510&clcid=0x409
[Fiddler]: http://www.fiddler2.com/fiddler2/
[EmulatorIE11]: http://msdn.microsoft.com/es-es/library/ie/dn255001.aspx
[EmulatorChrome]: https://developers.google.com/chrome-developer-tools/docs/mobile-emulation
[EmulatorOpera]: http://www.opera.com/developer/tools/mobile/
[StarterProject]: http://go.microsoft.com/fwlink/?LinkID=398780&clcid=0x409
[CompletedProject]: http://go.microsoft.com/fwlink/?LinkID=398781&clcid=0x409
[BootstrapSite]: http://getbootstrap.com/
[WebPIAzureSdk23NetVS13]: ./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/WebPIAzureSdk23NetVS13.png
[grupo de listas vinculadas]: http://getbootstrap.com/components/#list-group-linked
[glyphicon]: http://getbootstrap.com/components/#glyphicons
[paneles]: http://getbootstrap.com/components/#panels
[grupo de listas vinculadas personalizado]: http://getbootstrap.com/components/#list-group-custom-content
[sistema de cuadrícula]: http://getbootstrap.com/css/#grid
[utilidades con respuesta]: http://getbootstrap.com/css/#responsive-utilities
[Blog oficial de Bootstrap]: http://blog.getbootstrap.com/
[Tutorial de Twitter Bootstrap de Tutorial Republic]: http://www.tutorialrepublic.com/twitter-bootstrap-tutorial/
[Bootstrap Playground]: http://www.bootply.com/
[Prácticas recomendadas y recomendaciones de W3C para aplicaciones web móviles]: http://www.w3.org/TR/mwabp/
[Recomendación de candidatos de W3C para consultas multimedia]: http://www.w3.org/TR/css3-mediaqueries/

<!-- Images -->
[DeployClickPublish]: ./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/deploy-to-azure-website-1.png
[DeployClickWebSites]: ./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/deploy-to-azure-website-2.png
[DeploySignIn]: ./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/deploy-to-azure-website-3.png
[DeployUsername]: ./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/deploy-to-azure-website-4.png
[DeployPassword]: ./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/deploy-to-azure-website-5.png
[DeployNewWebsite]: ./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/deploy-to-azure-website-6.png
[DeploySiteSettings]: ./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/deploy-to-azure-website-7.png
[DeployPublishSite]: ./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/deploy-to-azure-website-8.png
[MobileHomePage]: ./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/mobile-home-page.png
[FixedSessionsByTag]: ./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/SessionsByTag-ASP.NET-Fixed.png
[AllTags]: ./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/AllTags.png
[SessionsByTagASP.NET]: ./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/SessionsByTag-ASP.NET.png
[SessionsByTagASP.NETNoBootstrap]: ./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/SessionsByTag-ASP.NET-NoBootstrap.png
[AllTagsMobile_LayoutMobile]: ./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/AllTagsMobile-_LayoutMobile.png
[AllTagsMobile_LayoutMobileDesktop]: ./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/AllTagsMobile-_LayoutMobile-Desktop.png
[ResolveDefaultDisplayMode]: ./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/Resolve-DefaultDisplayMode.png
[AllTagsIPhone_LayoutIPhone]: ./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/AllTagsIPhone-_LayoutIPhone.png
[AllSpeakers_LayoutMobile]: ./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/AllSpeakers-_LayoutMobile.png
[AllSpeakers_LayoutMobileOverridden]: ./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/AllSpeakers-_LayoutMobile-Overridden.png
[AllSpeakersFixed]: ./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/AllSpeakers-Fixed.png
[AllSpeakersFixedDesktop]: ./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/AllSpeakers-Fixed-Desktop.png
[AllSpeakersFixedSearchBySC]: ./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/AllSpeakers-Fixed-SearchBySC.png
[AllTagsFixedDesktop]: ./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/AllTags-Fixed-Desktop.png 
[AllTagsFixed]: ./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/AllTags-Fixed.png
[AllDatesFixed]: ./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/AllDates-Fixed.png
[AllDatesFixed2]: ./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/AllDates-Fixed2.png
[AllDatesFixed2Desktop]: ./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/AllDates-Fixed2-Desktop.png
[AllTagsFixedSearchByASP]: ./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/AllTags-Fixed-SearchByASP.png
[SessionsTableTagASP.NET]: ./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/SessionsTable-Tag-ASP.NET.png
[SessionsTableFixedTagASP.NETDesktop]: ./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/SessionsTable-Fixed-Tag-ASP.NET-Desktop.png
[SessionByCode3-644]: ./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/SessionByCode-3-644.png
[SessionByCodeFixed3-644]: ./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/SessionByCode-Fixed-3-644.png
