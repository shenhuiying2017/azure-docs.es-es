<properties 
	pageTitle="Crear una aplicación web de .NET MVC en Servicio de aplicaciones de Azure con autenticación de Azure Active Directory" 
	description="Obtenga información sobre cómo crear una aplicación de línea de negocio de ASP.NET MVC en Servicio de aplicaciones de Azure que se autentica con Azure Active Directory" 
	services="app-service\web, active-directory" 
	documentationCenter=".net" 
	authors="cephalin" 
	manager="wpickett" 
	editor=""/>

<tags 
	ms.service="app-service-web" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.tgt_pltfrm="na" 
	ms.workload="web" 
	ms.date="04/09/2015" 
	ms.author="cephalin"/>

# Crear una aplicación web de .NET MVC en Servicio de aplicaciones de Azure con autenticación de Azure Active Directory #

En este artículo, aprenderá a crear una aplicación de línea de negocio de ASP.NET MVC en [Aplicaciones web del Servicio de aplicaciones de Azure](http://go.microsoft.com/fwlink/?LinkId=529714) mediante el uso de [Azure Active Directory](/services/active-directory/) como el proveedor de identidades. También aprenderá a usar la [biblioteca de cliente de Azure Active Directory Graph](http://blogs.msdn.com/b/aadgraphteam/archive/2014/06/02/azure-active-directory-graph-client-library-1-0-publish.aspx) para consultar los datos de directorio en la aplicación.

El inquilino de Azure Active Directory que usa puede tener un directorio solo de Azure, o puede estar sincronizado con directorios con su Active Directory (AD) local para crear una experiencia de inicio de sesión único para los trabajadores que sean locales o remotos.

>[AZURE.NOTE] La característica Autenticación simple en Aplicaciones web del Servicio de aplicaciones de Azure le permite configurar la autenticación simple en un inquilino de Azure Active Directory con algunos clics del botón. Para obtener más información, consulte [Uso de Active Directory para autenticación en Servicio de aplicaciones de Azure](web-sites-authentication-authorization.md).

<a name="bkmk_build"></a>
## Qué va a crear ##

Creará una aplicación crear-leer-actualizar-eliminar (CRUD) de línea de negocio simple en Aplicaciones web del Servicio de aplicaciones que realiza un seguimiento de los elementos de trabajo con las siguientes características:

- Autentica los usuarios contra Azure Active Directory
- Funcionalidad de inicio y de cierre de sesión
- Usa `[Authorize]` para autorizar a los usuarios para distintas acciones CRUD
- Consulta los datos de Azure Active Directory con la [API de Azure Active Directory Graph](http://msdn.microsoft.com/library/azure/hh974476.aspx)
- Usa [Microsoft.Owin](http://www.asp.net/aspnet/overview/owin-and-katana/an-overview-of-project-katana) (en lugar de Windows Identity Foundation, es decir, WIF), que es el futuro de ASP.NET y mucho más simple de configurar que WIF para la autenticación y autorización

<a name="bkmk_need"></a>
## Qué necesita ##

[AZURE.INCLUDE [free-trial-note](../includes/free-trial-note.md)]

>[AZURE.NOTE] Si quiere empezar a trabajar con el servicio de aplicaciones de Azure antes de contratar una cuenta de Azure, vaya a [Probar el servicio de aplicaciones](http://go.microsoft.com/fwlink/?LinkId=523751), donde puede crear inmediatamente una aplicación web inicial de corta duración en el servicio de aplicaciones. No es necesario proporcionar ninguna tarjeta de crédito ni asumir ningún compromiso.

Necesita lo siguiente para completar este tutorial:

- Un inquilino de Azure Active Directory con usuarios en varios grupos
- Permisos para crear aplicaciones en el inquilino de Azure Active Directory
- Visual Studio 2013
- [SDK de Azure 2.5.1](http://go.microsoft.com/fwlink/p/?linkid=323510&clcid=0x409) o posterior

<a name="bkmk_sample"></a>
## Usar la aplicación de ejemplo para la plantilla de línea de negocio ##

La aplicación de ejemplo de este tutorial, [WebApp-GroupClaims-DotNet](https://github.com/AzureADSamples/WebApp-GroupClaims-DotNet), la aplica el equipo de Azure Active Directory y se puede usar como plantilla para crear con facilidad nuevas aplicaciones de línea de negocio. Tiene las siguientes características integradas:

- Usa [OpenID Connect](http://openid.net/connect/) para autenticarse con Azure Active Directory
- El controlador "Roles" que contiene un filtro de búsqueda de Azure Active Directory y le permite asignar fácilmente usuarios o grupos de Azure Active Directory a los roles de aplicación.
- El controlador `TaskTracker` de ejemplo que muestra cómo puede autorizar distintos roles para acciones específicas en la aplicación, incluido el uso estándar de `[Authorize]`. 

![](./media/web-sites-dotnet-lob-application-azure-ad/role-management.png)

<a name="bkmk_run" />
## Ejecutar la aplicación de ejemplo ##

1.	Clone o descargue la solución de ejemplo de [WebApp-GroupClaims-DotNet](https://github.com/AzureADSamples/WebApp-GroupClaims-DotNet) en su directorio local.
2.	Siga las instrucciones de [README.md](https://github.com/AzureADSamples/WebApp-GroupClaims-DotNet/blob/master/README.md) para configurar el proyecto y la aplicación de Azure Active Directory.

	> [AZURE.NOTE] Los permisos configurados en la aplicación Azure Active Directory solo requiere el rol <strong>Usuario</strong>, no **Administrador global**.
	
3.	Cuando haya terminado de configurar la aplicación, escriba "F5" para ejecutar la aplicación.
4.	Cuando se cargue la aplicación, haga clic en **Iniciar sesión**. 
5.	Si ha configurado la aplicación de Azure Active Directory correctamente y ha establecido los valores correspondientes en Web.config, se le debería redirigir al inicio de sesión. Solo tiene que iniciar sesión con la cuenta utilizada para crear la aplicación Azure Active Directory en el portal de Azure, puesto que es el propietario predeterminado de la aplicación Azure Active Directory. 
	
	> [AZURE.NOTE] En Startup.Auth.cs del proyecto de ejemplo, observe que la aplicación tiene un método llamado <code>AddOwnerAdminClaim</code>, que usa para agregar el propietario de la aplicación al rol de Admin. Esto le permite empezar a administrar inmediatamente los roles de aplicación en el controlador <code>Roles</code> .
	
4.	Cuando haya iniciado sesión, haga clic en **Roles** para administrar roles de aplicación.
5.	En **Buscar usuarios o grupos**, comience a escribir el nombre del grupo o del usuario que desee y observe que en una lista desplegable se muestra una lista filtrada de usuarios o grupos del inquilino de Azure Active Directory.

	![](./media/web-sites-dotnet-lob-application-azure-ad/select-user-group.png) 

	> [AZURE.NOTE] En Views\Roles\Index.cshtml, verá que la vista utiliza un objeto de JavaScript denominado <code>AadPicker</code> (definido en Scripts\AadPickerLibrary.js) para tener acceso a la acción <code>Search</code> en el controlador <code>Roles</code> .
		<pre class="prettyprint">var searchUrl = window.location.protocol + "//" + window.location.host + "<mark>/Roles/Search</mark>";
	...
    var picker = new <mark>AadPicker(searchUrl, maxResultsPerPage, input, token, tenant)</mark>;</pre>
		En Controllers\RolesController.cs, verá la acción <code>Search</code>, que envía la solicitud real a la API de Azure Active Directory Graph y devuelve la respuesta a la página.
		Más adelante, utilizará el mismo método para crear una funcionalidad sencilla en la aplicación.

6.	Seleccione un usuario o grupo en la lista desplegable, elija un rol y haga clic en **Asignar rol**.

<a name="bkmk_deploy"></a>
## Implementar la aplicación de ejemplo en Aplicaciones web del Servicio de aplicaciones

A continuación, publicará la aplicación en una aplicación web en Servicio de aplicaciones de Azure. Ya hay instrucciones en [README.md](https://github.com/AzureADSamples/WebApp-GroupClaims-DotNet/blob/GroupClaims/README.md) para implementar Aplicaciones web del Servicio de aplicaciones, pero esos pasos también anulan la configuración para su entorno de depuración local. Le mostraré cómo implementar y conservar la configuración de depuración.

1. Haga clic con el botón secundario en el proyecto y seleccione **Publicar**.

	![](./media/web-sites-dotnet-lob-application-azure-ad/publish-app.png)

2. Seleccione **Aplicaciones web de Microsoft Azure**.
3. Si no ha iniciado sesión en Azure, haga clic en **Iniciar sesión** y use la cuenta de Microsoft de su suscripción de Azure para iniciar sesión.
4. Cuando haya iniciado sesión, haga clic en **Nuevo** para crear una nueva aplicación web en Azure.
5. Rellene todos los campos obligatorios. Necesitará una conexión de base de datos para que esta aplicación almacene las asignaciones de rol, los tokens almacenados en memoria caché y los datos de aplicación.

	![](./media/web-sites-dotnet-lob-application-azure-ad/4-create-website.png)

6. Haga clic en **Crear**. Una vez que se haya creado la aplicación web, se abrirá el cuadro de diálogo Publicación web.
7. En **Dirección URL de destino**, cambie **http** a **https**. Copie toda la dirección URL en un editor de texto. La usará más adelante. A continuación, haga clic en **Siguiente**.

	![](./media/web-sites-dotnet-lob-application-azure-ad/5-change-to-https.png)

8. Desactive la casilla de verificación **Habilitar autenticación de organización**.

	![](./media/web-sites-dotnet-lob-application-azure-ad/6-disable-organizational-authentication.png)

9. En lugar de hacer clic en **Publicar** para pasar por la publicación web, haga clic en **Cerrar**. Haga clic en **Sí** para guardar los cambios en el perfil de publicación.
2. En el [Portal de administración de Azure](https://manage.windowsazure.com), vaya a su inquilino de Azure Active Directory y haga clic en la pestaña **Aplicaciones**.
2. Haga clic en **Agregar** en la parte inferior de la página.
3. Seleccione **Aplicación web y/o API web**.
4. Asigne un nombre a la aplicación y haga clic en **Siguiente**.
5. En Propiedades de la aplicación, establezca **Dirección URL de inicio de sesión** en la dirección URL de la aplicación web que guardó anteriormente (por ejemplo, `https://<site-name>.azurewebsites.net`)y el**URI del id. de aplicación** en `https://<aad-tenanet-name>/<app-name>`. A continuación, haga clic en **Completar**.

	![](./media/web-sites-dotnet-lob-application-azure-ad/7-app-properties.png)

6. Cuando se haya creado la aplicación, haga clic en **Configurar**.
7. En **Claves**, cree una nueva clave seleccionando **1 año** en la lista desplegable.
8. En **Permisos para otras aplicaciones**, en la entrada **Azure Active Directory**, seleccione **Acceso al directorio de su organización** en la lista desplegable **Permisos delegados**.

	> [AZURE.NOTE] Los permisos exactos que necesita aquí dependen de la funcionalidad que desee de la aplicación. Algunos permisos requieren que se establezca el rol **Administrador global**, pero los permisos necesarios para este tutorial solo requieren el rol **Usuario**.

9.  Haga clic en **Guardar**.  
10.  Antes de salir de la página de configuración guardada, copie la siguiente información en un editor de texto.

	-	Id. de cliente
	-	Clave (si sale de la página, no podrá ver la clave de nuevo)

11. En Visual Studio, abra **Web.Release.config** en el proyecto. Inserte el siguiente código XML en la etiqueta `<configuration>` y reemplace el valor de cada clave por la información que guardó para la nueva aplicación de Azure Active Directory.  
	<pre class="prettyprint">
&lt;appSettings&gt;
   &lt;add key="ida:ClientId" value="<mark>[e.g. 82692da5-a86f-44c9-9d53-2f88d52b478b]</mark>" xdt:Transform="SetAttributes" xdt:Locator="Match(key)" /&gt;
   &lt;add key="ida:AppKey" value="<mark>[e.g. rZJJ9bHSi/cYnYwmQFxLYDn/6EfnrnIfKoNzv9NKgbo=]</mark>" xdt:Transform="SetAttributes" xdt:Locator="Match(key)" /&gt;
   &lt;add key="ida:PostLogoutRedirectUri" value="<mark>[e.g. https://mylobapp.azurewebsites.net/]</mark>" xdt:Transform="SetAttributes" xdt:Locator="Match(key)" /&gt;
&lt;/appSettings&gt;</pre>

	Asegúrese de que el valor de ida:PostLogoutRedirectUri termina con una barra diagonal "/".

1. Haga clic con el botón secundario en el proyecto de nuevo y seleccione **Publicar**.
2. Haga clic en **Publicar** para publicar en Aplicaciones web del Servicio de aplicaciones de Azure.

Cuando haya terminado, tendrá dos aplicaciones de Azure Active Directory configuradas en el Portal de administración de Azure, una para el entorno de depuración en Visual Studio y otra para la aplicación web publicada en Azure. Durante la depuración, la configuración de la aplicación en Web.config se usa para que su configuración de **Debug** funcione con Azure Active Directory, y cuando se publique (de forma predeterminada, se publica la configuración de **Release**), se carga un archivo Web.config que incorpora los cambios de configuración de la aplicación en Web.Release.config.

Si desea asociar la aplicación web publicada al depurador (es decir, debe cargar los símbolos de depuración del código en el sitio web publicado), puede crear un clon de la configuración de depuración para la depuración de Azure, pero con su propia transformación de Web.config personalizada (por ejemplo, Web.AzureDebug.config) que usa la configuración de la aplicación de Azure Active Directory de Web.Release.config. Esto le permite mantener una configuración estática en los distintos entornos.

<a name="bkmk_crud"></a>
## Agregar funcionalidad de línea de negocio a la aplicación de ejemplo

En esta parte del tutorial, aprenderá a crear la funcionalidad de línea de negocio deseada según la aplicación de ejemplo. Creará un rastreador de elementos de trabajo CRUD sencillo, similar al controlador TaskTracker pero utilizando el modelo de diseño y scaffolding de CRUD estándar. También utilizará el archivo Scripts\AadPickerLibrary.js incluido para enriquecer su aplicación con datos de la API de Azure Active Directory Graph.  

5.	En la carpeta Models, cree un nuevo modelo llamado WorkItem.cs y reemplace el código con el código siguiente:

		using System.ComponentModel.DataAnnotations;
		
		namespace WebAppGroupClaimsDotNet.Models
		{
		    public class WorkItem
		    {
		        [Key]
		        public int ItemID { get; set; }
		        public string AssignedToID { get; set; }
		        public string AssignedToName { get; set; }
		        public string Description { get; set; }
		        public WorkItemStatus Status { get; set; }
		    }
		
		    public enum WorkItemStatus
		    {
		        Open, 
		        Investigating, 
		        Resolved, 
		        Closed
		    }
		}

6.	Abra DAL\GroupClaimContext.cs y agregue el código resaltado:  
	<pre class="prettyprint">
    public class GroupClaimContext : DbContext
    {
        public GroupClaimContext() : base("GroupClaimContext") { }

        public DbSet&lt;RoleMapping&gt; RoleMappings { get; set; }
        public DbSet&lt;Task&gt; Tasks { get; set; }
        <mark>public DbSet&lt;WorkItem&gt; WorkItems { get; set; }</mark>
        public DbSet&lt;TokenCacheEntry&gt; TokenCacheEntries { get; set; }
    }</pre>

7.	Compile el proyecto para que su nuevo modelo sea accesible a la lógica de scaffolding en Visual Studio.
8.	Agregue el nuevo elemento con scaffold `WorkItemsController` a la carpeta Controladores. Para ello, haga clic con el botón secundario en **Controladores**, seleccione **Agregar**y **Nuevo elemento con scaffold**. 
9.	Seleccione **Controlador MVC 5 con vistas, usando Entity Framework** y haga clic en **Agregar**.
10.	Seleccione el modelo que acaba de crear y haga clic en **Agregar**.

	![](./media/web-sites-dotnet-lob-application-azure-ad/8-add-scaffolded-controller.png)

9.	Abrir Controllers\WorkItemsController.cs

11. Agregue las decoraciones de [Authorize] resaltadas a las acciones respectivas siguientes.
	<pre class="prettyprint">
	...

    <mark>[Authorize(Roles = "Admin, Observer, Writer, Approver")]</mark>
    public class WorkItemsController : Controller
    {
		...

        <mark>[Authorize(Roles = "Admin, Writer")]</mark>
        public ActionResult Create()
        ...

        <mark>[Authorize(Roles = "Admin, Writer")]</mark>
        public async Task&lt;ActionResult&gt; Create([Bind(Include = "ItemID,AssignedToID,AssignedToName,Description,Status")] WorkItem workItem)
        ...

        <mark>[Authorize(Roles = "Admin, Writer")]</mark>
        public async Task&lt;ActionResult&gt; Edit(int? id)
        ...

        <mark>[Authorize(Roles = "Admin, Writer")]</mark>
        public async Task&lt;ActionResult&gt; Edit([Bind(Include = "ItemID,AssignedToID,AssignedToName,Description,Status")] WorkItem workItem)
        ...

        <mark>[Authorize(Roles = "Admin, Writer, Approver")]</mark>
        public async Task&lt;ActionResult&gt; Delete(int? id)
        ...

        <mark>[Authorize(Roles = "Admin, Writer, Approver")]</mark>
        public async Task&lt;ActionResult&gt; DeleteConfirmed(int id)
        ...
	}</pre>

	Puesto que se encarga de las asignaciones de rol en el controlador de Roles, todo lo que tiene que hacer es asegurarse de que cada acción autoriza los roles adecuados.

	> [AZURE.NOTE] Es posible que haya observado la decoración <code>[ValidateAntiForgeryToken]</code> en algunas de las acciones. Debido al comportamiento descrito por [Brock Allen](https://twitter.com/BrockLAllen) en [MVC 4, AntiForgeryToken y notificaciones Claims](http://brockallen.com/2012/07/08/mvc-4-antiforgerytoken-and-claims/) su HTTP POST puede generar un error de validación de token antifalsificación porque:
	> + Azure Active Directory no envía http://schemas.microsoft.com/accesscontrolservice/2010/07/claims/identityprovider, que el token antifalsificación requiere de manera predeterminada.
	> + Si Azure Active Directory está sincronizado con directorios con AD FS, la confianza de AD FS tampoco envía de manera predeterminada la notificación de http://schemas.microsoft.com/accesscontrolservice/2010/07/claims/identityprovider, aunque puede configurar AD FS manualmente para enviar esta notificación.
	> Se encargará de esto en el siguiente paso.

12.  En App_Start\Startup.Auth.cs, agregue la siguiente línea de código en el método `ConfigureAuth`:

		AntiForgeryConfig.UniqueClaimTypeIdentifier = ClaimTypes.NameIdentifier;
	
	"ClaimTypes.NameIdentifies" especifica la notificación `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier`, que proporciona Azure Active Directory. Ahora que se ha encargado de la parte de la autorización (en serio, eso no tardó mucho), puede dedicar su tiempo a la funcionalidad real de las acciones. 

13.	En Create() y Edit() agregue el siguiente código para que algunas de las variables se pongan a su disposición en el código JavaScript más adelante:
            ViewData["token"] = GraphHelper.AcquireToken(ClaimsPrincipal.Current.FindFirst(Globals.ObjectIdClaimType).Value);
            ViewData["tenant"] = ConfigHelper.Tenant;
14.	En Views\WorkItems\Create.cshtml (un elemento con scaffold automático), encuentre el método auxiliar `Html.BeginForm` y modifíquelo de la siguiente manera:  
	<pre class="prettyprint">@using (Html.BeginForm(<mark>"Create", "WorkItems", FormMethod.Post, new { id = "main-form" }</mark>))
	{
	    @Html.AntiForgeryToken()
	    
	    &lt;div class="form-horizontal"&gt;
	        &lt;h4&gt;WorkItem&lt;/h4&gt;
	        &lt;hr /&gt;
	        @Html.ValidationSummary(true, "", new { @class = "text-danger" })
	
	        &lt;div class="form-group"&gt;
	            &lt;div class="col-md-10"&gt;
	                @Html.EditorFor(model =&gt; model.AssignedToID, new { htmlAttributes = new { @class = "form-control"<mark>, @type=&quot;hidden&quot;</mark> } })
	                @Html.ValidationMessageFor(model =&gt; model.AssignedToID, "", new { @class = "text-danger" })
	            &lt;/div&gt;
	        &lt;/div&gt;
	
	        &lt;div class="form-group"&gt;
	            @Html.LabelFor(model =&gt; model.AssignedToName, htmlAttributes: new { @class = "control-label col-md-2" })
	            &lt;div class="col-md-10"&gt;
	                @Html.EditorFor(model =&gt; model.AssignedToName, new { htmlAttributes = new { @class = "form-control" } })
	                @Html.ValidationMessageFor(model =&gt; model.AssignedToName, "", new { @class = "text-danger" })
	            &lt;/div&gt;
	        &lt;/div&gt;
	
	        &lt;div class="form-group"&gt;
	            @Html.LabelFor(model =&gt; model.Description, htmlAttributes: new { @class = "control-label col-md-2" })
	            &lt;div class="col-md-10"&gt;
	                @Html.EditorFor(model =&gt; model.Description, new { htmlAttributes = new { @class = "form-control" } })
	                @Html.ValidationMessageFor(model =&gt; model.Description, "", new { @class = "text-danger" })
	            &lt;/div&gt;
	        &lt;/div&gt;
	
	        &lt;div class="form-group"&gt;
	            @Html.LabelFor(model =&gt; model.Status, htmlAttributes: new { @class = "control-label col-md-2" })
	            &lt;div class="col-md-10"&gt;
	                @Html.EnumDropDownListFor(model =&gt; model.Status, htmlAttributes: new { @class = "form-control" })
	                @Html.ValidationMessageFor(model =&gt; model.Status, "", new { @class = "text-danger" })
	            &lt;/div&gt;
	        &lt;/div&gt;
	
	        &lt;div class="form-group"&gt;
	            &lt;div class="col-md-offset-2 col-md-10"&gt;
	                &lt;input type="submit" value="Create" class="btn btn-default" <mark>id="submit-button"</mark> /&gt;
	            &lt;/div&gt;
	        &lt;/div&gt;
	    &lt;/div&gt;
	
	    <mark>&lt;script&gt;
	            // People/Group Picker Code
	            var maxResultsPerPage = 14;
	            var searchUrl = window.location.protocol + "//" + window.location.host + "/Roles/Search";
	            var input = document.getElementById("AssignedToName");
	            var token = "@ViewData["token"]";
	            var tenant = "@ViewData["tenant"]";
	
	            var picker = new AadPicker(searchUrl, maxResultsPerPage, input, token, tenant);
	
	            // Submit the selected user/group to be asssigned.
	            $("#submit-button").click({ picker: picker }, function () {
	                if (!picker.Selected())
	                    return;
	                $("#main-form").get()[0].elements["AssignedToID"].value = picker.Selected().objectId;
	            });
	    &lt;/script&gt;</mark>
	
	}</pre>

	En el script, el objeto AadPicker busca en la acción "~/Roles/Search" usuarios y grupos de Azure Active Directory que coinciden con la entrada. A continuación, cuando se hace clic en el botón de envío, el objeto AadPicker guarda el identificador de usuario en el campo `AssignedToID` oculto.  

15. Ahora ejecute la aplicación en el depurador de Visual Studio o publique en Aplicaciones web del Servicio de aplicaciones de Azure. Inicie sesión como el propietario de la aplicación y navegue hasta "~/WorkItems/Create". Para mi aplicación de línea de negocio publicada, navego hasta `https://mylobapp.azurewebsites.net/WorkItems/Create`. Verá que ahora puede usar el mismo filtro de búsqueda AadPicker para seleccionar un usuario de Azure Active Directory.

	![](./media/web-sites-dotnet-lob-application-azure-ad/9-create-workitem.png)

16. Complete el resto del formulario y haga clic en **Crear**. La página ~/WorkItems/Index muestra ahora el elemento de trabajo recién creado. También observará en la siguiente captura de pantalla que eliminé la columna `AssignedToID` en Views\WorkItems\Index.cshtml. 

	![](./media/web-sites-dotnet-lob-application-azure-ad/10-workitem-index.png)

11.	Ahora, realice cambios similares en la vista **Editar**. En Views\WorkItems\Edit.cshtml, realice los cambios en el método auxiliar `Html.BeginForm` que sean idénticos a los cambios en Views\WorkItems\Create.cshtml del paso anterior (reemplace "Create" por "Edit" en el código resaltado anterior).

Eso es todo.

Ahora que ha configurado las autorizaciones y la funcionalidad de línea de negocio para las distintas acciones en el controlador de elementos de trabajo, puede intentar iniciar sesión como usuarios de diferentes roles de aplicación.

![](./media/web-sites-dotnet-lob-application-azure-ad/11-edit-unauthorized.png)

<a name="bkmk_resources"></a>
## Recursos adicionales

- [Protección de la aplicación con SSL y el atributo Authorize](web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database.md#protect-the-application-with-ssl-and-the-authorize-attribute)
- [Usar Active Directory para la autenticación en Servicio de aplicaciones de Azure](web-sites-authentication-authorization.md)
- [Crear una aplicación web de .NET MVC en Servicio de aplicaciones de Azure con autenticación de AD FS](web-sites-dotnet-lob-application-adfs.md)
- [Ejemplos y documentación de Microsoft Azure Active Directory](https://github.com/AzureADSamples)
- [El blog de Vittorio Bertocci](http://blogs.msdn.com/b/vbertocci/)
- [Migrar un proyecto web de VS2013 de WIF a Katana](http://www.cloudidentity.com/blog/2014/09/15/MIGRATE-A-VS2013-WEB-PROJECT-FROM-WIF-TO-KATANA/)
- [Las nuevas conexiones híbridas de Azure no #hybridCloud de tu padre](/documentation/videos/new-hybrid-connections-not-your-fathers-hybridcloud/)
- [Similitudes entre Active Directory y Azure Active Directory](http://technet.microsoft.com/library/dn518177.aspx)
- [Sincronización de directorios con el escenario de inicio de sesión único](http://technet.microsoft.com/library/dn441213.aspx)
- [Tipos de notificaciones y tokens admitidos de Azure Active Directory](http://msdn.microsoft.com/library/azure/dn195587.aspx)

## Lo que ha cambiado
* Para obtener una guía para cambiar de sitios web al servicio de aplicaciones, consulte: [El servicio de aplicaciones de Azure y su impacto en los servicios de Azure existentes](http://go.microsoft.com/fwlink/?LinkId=529714)
* Para obtener una guía para cambiar del portal antiguo al nuevo portal, consulte: [Referencia para navegar por el portal de vista previa](http://go.microsoft.com/fwlink/?LinkId=529715)


<!--HONumber=52-->