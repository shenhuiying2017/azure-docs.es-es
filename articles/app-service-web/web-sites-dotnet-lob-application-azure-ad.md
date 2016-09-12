<properties 
	pageTitle="Creación de una aplicación de línea de negocio de Azure con autenticación de Azure Active Directory | Microsoft Azure" 
	description="Aprenda a crear una aplicación de línea de negocio de ASP.NET MVC en Servicio de aplicaciones de Azure que se autentica con Azure Active Directory" 
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
	ms.date="08/31/2016" 
	ms.author="cephalin"/>

# Creación de una aplicación de línea de negocio de Azure con autenticación de Azure Active Directory #

En este artículo se muestra cómo crear una aplicación de línea de negocio de .NET en [Aplicaciones web del Servicio de aplicaciones de Azure](http://go.microsoft.com/fwlink/?LinkId=529714) utilizando la característica de [autenticación/autorización](../app-service/app-service-authentication-overview.md). También se enseña a usar la [API Graph de Azure Active Directory Graph](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/api-catalog) para consultar los datos de directorio en la aplicación.

El inquilino de Azure Active Directory que use puede ser un directorio exclusivo de Azure. O bien, se puede [sincronizar con su entorno de Active Directory local](../active-directory/active-directory-aadconnect.md) para crear una experiencia de inicio de sesión única para los trabajadores locales y remotos. En este artículo se usa el directorio predeterminado para la cuenta de Azure.

<a name="bkmk_build"></a>
## Lo que va a crear ##

Creará una aplicación crear-leer-actualizar-eliminar (CRUD) de línea de negocio simple en Aplicaciones web del Servicio de aplicaciones que realiza un seguimiento de los elementos de trabajo con las siguientes características:

- Autentica los usuarios contra Azure Active Directory
- Consulta los usuarios y grupos del directorio con la [API Graph de Azure Active Directory](http://msdn.microsoft.com/library/azure/hh974476.aspx)

Si necesita control de acceso basado en roles (RBAC) para la aplicación de línea de negocio en Azure, consulte el [siguiente paso](#next).

<a name="bkmk_need"></a>
## Lo que necesita ##

[AZURE.INCLUDE [free-trial-note](../../includes/free-trial-note.md)]

Necesita lo siguiente para completar este tutorial:

- Un inquilino de Azure Active Directory con usuarios en varios grupos
- Permisos para crear aplicaciones en el inquilino de Azure Active Directory
- Visual Studio 2013, actualización 4 o posterior
- [SDK de Azure 2.8.1 o posterior](https://azure.microsoft.com/downloads/)

<a name="bkmk_deploy"></a>
## Creación e implementación de una aplicación web en Azure ##

1. En Visual Studio, haga clic en **Archivo** > **Nuevo** > **Proyecto**.

2. Seleccione **aplicación web ASP.NET**, elija el nombre del proyecto y haga clic en **Aceptar**.

3. Seleccione la plantilla **MVC** y cambie a **Sin autenticación**. Asegúrese de que **Host en la nube** está seleccionado y haga clic en **Aceptar**.

	![](./media/web-sites-dotnet-lob-application-azure-ad/1-create-mvc-no-authentication.png)

4. En el cuadro de diálogo **Crear servicio de aplicaciones**, haga clic en **Agregar una cuenta** (y, en la lista desplegable, en **Agregar una cuenta**) para iniciar sesión en su cuenta de Azure.

5. Después de iniciar sesión, configure la aplicación web. Cree un grupo de recursos y un nuevo plan del Servicio de aplicaciones al hacer clic en el botón **Nuevo** correspondiente. Haga clic en **Explore additional services** (Explorar servicios adicionales) para continuar.

	![](./media/web-sites-dotnet-lob-application-azure-ad/2-create-app-service.png)

6. En la pestaña **Servicios**, haga clic en **+** para agregar una base de datos SQL para la aplicación.

	![](./media/web-sites-dotnet-lob-application-azure-ad/3-add-sql-database.png)

7. En **Configurar base de datos SQL**, haga clic en **Nuevo** para crear una instancia de SQL Server.

8. En **Configurar SQL Server**, configure la instancia de SQL Server. A continuación, haga clic en **Aceptar**, **Aceptar**, y **Crear** para iniciar la creación de la aplicación en Azure.

9. En **Actividad del Servicio de aplicaciones de Azure**, verá cuándo finaliza la creación de la aplicación. Haga clic en **Publish & lt;*appname*> to this Web App now** (Publicar <appname> en esta aplicación web ahora) y en **Publicar**.

	Cuando Visual Studio termine, la aplicación de publicación se abre en el explorador.

	![](./media/web-sites-dotnet-lob-application-azure-ad/4-published-shown-in-browser.png)

<a name="bkmk_auth"></a>
## Configuración de la autenticación y el acceso al directorio

1. Inicie sesión en el [Portal de Azure](https://portal.azure.com).

2. En el menú izquierdo, haga clic en **Servicios de aplicaciones** > **& lt;*appname*>** > **Autenticación/autorización**.

	![](./media/web-sites-dotnet-lob-application-azure-ad/5-app-service-authentication.png)

3. Active la autenticación de Azure Active Directory al hacer clic en **Activada** > **Azure Active Directory** > **Rápido** > **Aceptar**.

	![](./media/web-sites-dotnet-lob-application-azure-ad/6-authentication-express.png)

4. Haga clic en **Guardar** en la barra de comandos.

	![](./media/web-sites-dotnet-lob-application-azure-ad/7-authentication-save.png)

    Una vez guardada correctamente la configuración de autenticación, intente volver a su aplicación en el explorador. La configuración predeterminada exige la autenticación en toda la aplicación. Si aún no ha iniciado sesión, se le redirigirá a una pantalla para ello. Una vez iniciada la sesión, verá la aplicación protegida por HTTPS. A continuación, debe habilitar el acceso a los datos del directorio.

5. Vaya al [Portal clásico](https://manage.windowsazure.com).

6. En el menú izquierdo, haga clic en **Active Directory** > **Directorio predeterminado** > **Aplicaciones** > **& lt;*appname*>**.

	![](./media/web-sites-dotnet-lob-application-azure-ad/8-find-aad-application.png)

	Esta es la aplicación de Azure Active Directory que crea automáticamente el Servicio de aplicaciones para habilitar la característica Autorización/autenticación.

7. Haga clic en **Usuarios** y en **Grupos** para asegurarse de que tiene usuarios y grupos en el directorio. Si no es así, cree grupos y usuarios de prueba.

	![](./media/web-sites-dotnet-lob-application-azure-ad/9-create-users-groups.png)

7. Haga clic en **Configurar** para configurar esta aplicación.

8. Desplácese hacia abajo hasta la sección **Claves** y agregue una clave al seleccionar una duración. A continuación, haga clic en **Permisos delegados** y seleccione **Leer datos de directorio**. Haga clic en **Guardar**.

	![](./media/web-sites-dotnet-lob-application-azure-ad/10-configure-aad-application.png)

8. Una vez guardada la configuración, desplácese de nuevo arriba a la sección **Claves** y haga clic en el botón **Copiar** para copiar la clave de cliente.

	![](./media/web-sites-dotnet-lob-application-azure-ad/11-get-app-key.png)

	>[AZURE.IMPORTANT] Si sale ahora esta página, ya no podrá volver a acceder a esta clave de cliente.

9. A continuación, debe configurar la aplicación web con esta clave. Inicie sesión en el [Explorador de recursos de Azure](https://resources.azure.com) con su cuenta de Azure.

10. En la parte superior de la página, haga clic en **Lectura/escritura** para realizar cambios en el Explorador de recursos de Azure.

	![](./media/web-sites-dotnet-lob-application-azure-ad/12-resource-manager-writable.png)

11. Encuentre la configuración de autenticación de la aplicación, que se encuentra en subscriptions > **&lt;*subscriptionname*>** > **resourceGroups** > **&lt;*resourcegroupname*>** > **providers** > **Microsoft.Web** > **sites** > **&lt;*appname*>** > **config** > **authsettings**.

12. Haga clic en **Editar**.

	![](./media/web-sites-dotnet-lob-application-azure-ad/13-edit-authsettings.png)

13. En el panel de edición, establezca las propiedades `clientSecret` y `additionalLoginParams` como sigue.

		...
		"clientSecret": "<client key from the Azure Active Directory application>",
		...
		"additionalLoginParams": ["response_type=code id_token", "resource=https://graph.windows.net"],
		...

14. Haga clic en **Put** en la parte superior para enviar los cambios.

	![](./media/web-sites-dotnet-lob-application-azure-ad/14-edit-parameters.png)

14. Ahora, para probar si tiene el token de autorización para acceder a la API Graph de Azure Active Directory, cambie ~\\Controllers\\HomeController.cs para que use el siguiente método de acción `Index()`:
	<pre class="prettyprint">
	public ActionResult Index()
	{
		return <mark>Content(Request.Headers["X-MS-TOKEN-AAD-ACCESS-TOKEN"]);</mark>
	}
	</pre>

15. Haga clic con el botón derecho en el proyecto y haga clic en **Publicar** para publicar los cambios. Haga clic de nuevo en **Publicar** en el cuadro de diálogo.

	![](./media/web-sites-dotnet-lob-application-azure-ad/15-publish-token-code.png)

	Si la página de inicio de la aplicación muestra ahora un token de acceso, la aplicación podrá acceder a la API Graph de Azure Active Directory. Los cambios en ~\\Controllers\\HomeController.cs se pueden deshacer.

A continuación, hará algo útil con los datos del directorio.

<a name="bkmk_crud"></a>
## Funcionalidad de línea de negocio para la aplicación

Ahora, cree un rastreador de elementos de trabajo CRUD sencillo.

5.	En la carpeta ~\\Models, cree un archivo de clase denominado WorkItem.cs y cambie `public class WorkItem {...}` por el siguiente código:

		using System.ComponentModel.DataAnnotations;

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

7.	Compile el proyecto para que su nuevo modelo sea accesible a la lógica de scaffolding en Visual Studio.

8.	Agregue un nuevo elemento con scaffold `WorkItemsController` a la carpeta ~\\Controllers (haga clic con el botón derecho en **Controladores**, seleccione **Agregar** y **Nuevo elemento con scaffold**).

9.	Seleccione **Controlador MVC 5 con vistas, usando Entity Framework** y haga clic en **Agregar**.

10.	Seleccione el modelo que ha creado, haga clic en **+** y en **Agregar** para agregar un contexto de datos y, a continuación, haga clic en **Agregar**.

	![](./media/web-sites-dotnet-lob-application-azure-ad/16-add-scaffolded-controller.png)

9.	Abra ~\\Controllers\\WorkItemsController.cs.

13.	Al principio de los métodos `Create()` y `Edit(int? id)`, agregue el siguiente código para que algunas de las variables estén disponibles después en JavaScript. `Ctrl`+`.` en cada error de resolución de nombres para corregirlo.

		ViewData["token"] = Request.Headers["X-MS-TOKEN-AAD-ACCESS-TOKEN"];
		ViewData["tenant"] =
			ClaimsPrincipal.Current.Claims
			.Where(c => c.Type == "http://schemas.microsoft.com/identity/claims/tenantid")
			.Select(c => c.Value).SingleOrDefault();

	> [AZURE.NOTE] Es posible que haya observado la representación <code>[ValidateAntiForgeryToken]</code> en algunas de las acciones. Debido al comportamiento descrito por [Brock Allen](https://twitter.com/BrockLAllen) en [MVC 4, AntiForgeryToken and Claims](http://brockallen.com/2012/07/08/mvc-4-antiforgerytoken-and-claims/), su HTTP POST puede generar un error de validación de token antifalsificación porque:

	> - Azure Active Directory no envía http://schemas.microsoft.com/accesscontrolservice/2010/07/claims/identityprovider, que el token antifalsificación requiere de forma predeterminada.
	> - Si los directorios de Azure Active Directory no se sincronizan con AD FS, la confianza de AD FS tampoco envía de manera predeterminada la notificación de http://schemas.microsoft.com/accesscontrolservice/2010/07/claims/identityprovider, aunque puede configurar AD FS manualmente para enviar esta notificación.

	> Se encargará de este problema en el siguiente paso.

12.  En ~ \\Global.asax, agregue la siguiente línea de código en el método `Application_Start()`. `Ctrl`+`.` en cada error de resolución de nombres para corregirlo.

		AntiForgeryConfig.UniqueClaimTypeIdentifier = ClaimTypes.NameIdentifier;
	
	`ClaimTypes.NameIdentifies` especifica la notificación `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier`, que proporciona Azure Active Directory.

14.	En ~\\Views\\WorkItems\\Create.cshtml (un elemento con scaffold automático), encuentre el método auxiliar `Html.BeginForm` y realice los siguientes cambios resaltados:
	<pre class="prettyprint">
	@model WebApplication1.Models.WorkItem
	
	@{
		ViewBag.Title = "Create";
	}
	
	&lt;h2>Create&lt;/h2>
	
	@using (Html.BeginForm(<mark>"Create", "WorkItems", FormMethod.Post, new { id = "main-form" }</mark>)) 
	{
		@Html.AntiForgeryToken()
	
		&lt;div class="form-horizontal">
			&lt;h4>WorkItem&lt;/h4>
			&lt;hr />
			@Html.ValidationSummary(true, "", new { @class = "text-danger" })
			&lt;div class="form-group">
				@Html.LabelFor(model => model.AssignedToID, htmlAttributes: new { @class = "control-label col-md-2" })
				&lt;div class="col-md-10">
					@Html.EditorFor(model => model.AssignedToID, new { htmlAttributes = new { @class = "form-control"<mark>, @type = "hidden"</mark> } })
					@Html.ValidationMessageFor(model => model.AssignedToID, "", new { @class = "text-danger" })
				&lt;/div>
			&lt;/div>
	
			&lt;div class="form-group">
				@Html.LabelFor(model => model.AssignedToName, htmlAttributes: new { @class = "control-label col-md-2" })
				&lt;div class="col-md-10">
					@Html.EditorFor(model => model.AssignedToName, new { htmlAttributes = new { @class = "form-control" } })
					@Html.ValidationMessageFor(model => model.AssignedToName, "", new { @class = "text-danger" })
				&lt;/div>
			&lt;/div>
	
			&lt;div class="form-group">
				@Html.LabelFor(model => model.Description, htmlAttributes: new { @class = "control-label col-md-2" })
				&lt;div class="col-md-10">
					@Html.EditorFor(model => model.Description, new { htmlAttributes = new { @class = "form-control" } })
					@Html.ValidationMessageFor(model => model.Description, "", new { @class = "text-danger" })
				&lt;/div>
			&lt;/div>
	
			&lt;div class="form-group">
				@Html.LabelFor(model => model.Status, htmlAttributes: new { @class = "control-label col-md-2" })
				&lt;div class="col-md-10">
					@Html.EnumDropDownListFor(model => model.Status, htmlAttributes: new { @class = "form-control" })
					@Html.ValidationMessageFor(model => model.Status, "", new { @class = "text-danger" })
				&lt;/div>
			&lt;/div>
	
			&lt;div class="form-group">
				&lt;div class="col-md-offset-2 col-md-10">
					&lt;input type="submit" value="Create" class="btn btn-default"<mark> id="submit-button"</mark> />
				&lt;/div>
			&lt;/div>
		&lt;/div>
	}
	
	&lt;div>
		@Html.ActionLink("Back to List", "Index")
	&lt;/div>
	
	@section Scripts {
		@Scripts.Render("~/bundles/jqueryval")
		<mark>&lt;script>
			// Código de selector de personas/grupos
			var maxResultsPerPage = 14;
			var input = document.getElementById("AssignedToName");
	
			var token = "@ViewData["token"]";
			var tenant = "@ViewData["tenant"]";
	
			var picker = new AadPicker(maxResultsPerPage, input, token, tenant);
	
			// Enviar el usuario/grupo seleccionado que se debe asignar.
			$("#submit-button").click({ picker: picker }, function () {
				if (!picker.Selected())
					return;
				$("#main-form").get()[0].elements["AssignedToID"].value = picker.Selected().objectId;
			});
		&lt;/script></mark>
	}
	</pre>
	
	Observe que `token` y `tenant` los usa el objeto `AadPicker` para realizar llamadas de API Graph de Azure Active Directory. Agregará `AadPicker` más adelante.

15. Realice los mismos cambios con ~\\Views\\WorkItems\\Edit.cshtml.

15. El objeto `AadPicker` se define en un script que se debe agregar al proyecto. Haga clic con el botón derecho en la carpeta ~\\Scripts, seleccione **Agregar** y haga clic en **JavaScript file** (Archivo JavaScript). Escriba `AadPickerLibrary` como nombre de archivo y haga clic en **Aceptar**.

16. Copie [este](https://raw.githubusercontent.com/cephalin/active-directory-dotnet-webapp-roleclaims/master/WebApp-RoleClaims-DotNet/Scripts/AadPickerLibrary.js) contenido en ~\\Scripts\\AadPickerLibrary.js.

	En el script, el objeto `AadPicker` llama a la [API Graph de Azure Active Directory](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/api-catalog) para que busque usuarios y grupos que coincidan con la entrada.

17. ~\\Scripts\\AadPickerLibrary.js también usa el [widget Autocomplete de jQuery UI](https://jqueryui.com/autocomplete/). Por lo que necesitará agregar jQuery UI al proyecto. Haga clic con el botón derecho en el proyecto y haga clic en **Administrar paquetes NuGet**.

18. En el Administrador de paquetes NuGet, haga clic en Examinar, escriba **jquery-ui** en la barra de búsqueda y haga clic en **jQuery.UI.Combined**.

	![](./media/web-sites-dotnet-lob-application-azure-ad/17-add-jquery-ui-nuget.png)

19. En el panel derecho, haga clic en **Instalar** y en **Aceptar** para continuar.

19. Abra ~\\App\_Start\\BundleConfig.cs y realice los cambios resaltados siguientes:
	<pre class="prettyprint">
	public static void RegisterBundles(BundleCollection bundles)
	{
		bundles.Add(new ScriptBundle("~/bundles/jquery").Include(
					"~/Scripts/jquery-{version}.js"<mark>,
					"~/Scripts/jquery-ui-{version}.js",
					"~/Scripts/AadPickerLibrary.js"</mark>));
	
		bundles.Add(new ScriptBundle("~/bundles/jqueryval").Include(
					"~/Scripts/jquery.validate*"));
	
		// Use la versión de desarrollo de Modernizr para desarrollar y aprender con ella. A continuación, cuando esté
		// listo para producción, use la herramienta de compilación de http://modernizr.com para seleccionar solo las pruebas que necesite.
		bundles.Add(new ScriptBundle("~/bundles/modernizr").Include(
					"~/Scripts/modernizr-*"));
	
		bundles.Add(new ScriptBundle("~/bundles/bootstrap").Include(
					"~/Scripts/bootstrap.js",
					"~/Scripts/respond.js"));
	
		bundles.Add(new StyleBundle("~/Content/css").Include(
					"~/Content/bootstrap.css",
					"~/Content/site.css"<mark>,
					"~/Content/themes/base/jquery-ui.css"</mark>));
	}
	</pre>

	Existen más formas productivas de administrar los archivos JavaScript y CSS en la aplicación. Sin embargo, por motivos de simplicidad solo se incluyen en los paquetes que se cargan con cada vista.

20. Ahora, publique los cambios. Haga clic con el botón derecho en el proyecto y haga clic en **Publicar**.

21. Haga clic en **Configuración**, asegúrese de que hay una cadena de conexión a la instancia de Base de datos SQL, seleccione **Actualizar base de datos** para realizar los cambios de esquema para el modelo y haga clic en **Publicar**.

	![](./media/web-sites-dotnet-lob-application-azure-ad/18-publish-crud-changes.png)

22. En el explorador, vaya a https://&lt;*appname*>.azurewebsites.net/workitems y haga clic en **Crear nuevo**.

23. Haga clic en el cuadro **AssignedToName**. Ahora debería ver los usuarios y grupos desde el inquilino de Azure Active Directory en una lista desplegable. Puede escribir para filtrar o utilizar la clave `Up` o `Down`; también puede hacer clic para seleccionar el usuario o grupo.

	![](./media/web-sites-dotnet-lob-application-azure-ad/19-use-aadpicker.png)

24. Haga clic en **Crear** para guardar los cambios. A continuación, haga clic en **Editar** en el elemento de trabajo creado para observar el mismo comportamiento.

Enhorabuena, está ejecutando una aplicación de línea de negocio en Azure con acceso al directorio. Con la API Graph puede hacer mucho más. Consulte [Azure AD Graph API reference](https://msdn.microsoft.com/library/azure/ad/graph/api/api-catalog) (Referencias de la API Graph de Azure AD).

<a name="next"></a>
## Paso siguiente

Si necesita control de acceso basado en roles (RBAC) para la aplicación de línea de negocio en Azure, consulte [WebApp-RoleClaims-DotNet](https://github.com/Azure-Samples/active-directory-dotnet-webapp-roleclaims) para obtener un ejemplo del equipo de Azure Active Directory. Muestra cómo habilitar los roles para su aplicación de Azure Active Directory y, a continuación, autorizar a los usuarios con la decoración `[Authorize]`.

Si la aplicación de línea de negocio necesita acceso a datos locales, consulte [Acceso a recursos locales mediante conexiones híbridas en el Servicio de aplicaciones de Azure](web-sites-hybrid-connection-get-started.md).

<a name="bkmk_resources"></a>
## Recursos adicionales

- [Autenticación y autorización en el Servicio de aplicaciones de Azure](../app-service/app-service-authentication-overview.md)
- [Autenticación con Active Directory local en aplicaciones de Azure](web-sites-authentication-authorization.md)
- [Creación de una aplicación de línea de negocio en Azure con autenticación de AD FS](web-sites-dotnet-lob-application-adfs.md)
- [App Service Auth and the Azure AD Graph API](https://cgillum.tech/2016/03/25/app-service-auth-aad-graph-api/) (Autenticación del Servicio de aplicaciones y la API Graph de Azure AD)
- [Ejemplos y documentación de Microsoft Azure Active Directory](https://github.com/AzureADSamples)
- [Tipos de notificaciones y tokens admitidos de Azure Active Directory](http://msdn.microsoft.com/library/azure/dn195587.aspx)

[Protect the Application with SSL and the Authorize Attribute]: web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database.md#protect-the-application-with-ssl-and-the-authorize-attribute

<!---HONumber=AcomDC_0831_2016-->