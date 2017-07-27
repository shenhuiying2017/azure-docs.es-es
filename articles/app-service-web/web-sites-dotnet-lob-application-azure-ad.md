---
title: "Creación de una aplicación de línea de negocio de Azure con autenticación de Azure Active Directory | Microsoft Docs"
description: "Aprenda a crear una aplicación de línea de negocio de ASP.NET MVC en Servicio de aplicaciones de Azure que se autentica con Azure Active Directory"
services: app-service\web, active-directory
documentationcenter: .net
author: cephalin
manager: erikre
editor: 
ms.assetid: ad947bdb-4463-43ff-a5e3-91d9b2169b60
ms.service: app-service-web
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: web
ms.date: 09/01/2016
ms.author: cephalin
ms.translationtype: Human Translation
ms.sourcegitcommit: 07584294e4ae592a026c0d5890686eaf0b99431f
ms.openlocfilehash: 2576b658eaf1df95aa9700e06559edf6066cc534
ms.contentlocale: es-es
ms.lasthandoff: 06/01/2017


---
# <a name="create-a-line-of-business-azure-app-with-azure-active-directory-authentication"></a>Creación de una aplicación de línea de negocio de Azure con autenticación de Azure Active Directory
En este artículo se muestra cómo crear una aplicación de línea de negocio de .NET en [Azure App Service Web Apps](http://go.microsoft.com/fwlink/?LinkId=529714) con la característica de [autenticación/autorización](../app-service/app-service-authentication-overview.md). También se enseña a usar la [API Graph de Azure Active Directory Graph](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/api-catalog) para consultar los datos de directorio en la aplicación.

El inquilino de Azure Active Directory que use puede ser un directorio exclusivo de Azure. O bien, se puede [sincronizar con su entorno de Active Directory local](../active-directory/active-directory-aadconnect.md) para crear una experiencia de inicio de sesión única para los trabajadores locales y remotos. En este artículo se usa el directorio predeterminado para la cuenta de Azure.

<a name="bkmk_build"></a>

## <a name="what-you-will-build"></a>Lo que va a crear
Creará una aplicación crear-leer-actualizar-eliminar (CRUD) de línea de negocio simple en Aplicaciones web del Servicio de aplicaciones que realiza un seguimiento de los elementos de trabajo con las siguientes características:

* Autentica los usuarios contra Azure Active Directory
* Consulta los usuarios y grupos del directorio con la [API Graph de Azure Active Directory](http://msdn.microsoft.com/library/azure/hh974476.aspx)
* Uso de la plantilla *Sin autenticación* de ASP.NET MVC

Si necesita control de acceso basado en roles (RBAC) para la aplicación de línea de negocio en Azure, consulte el [siguiente paso](#next).

<a name="bkmk_need"></a>

## <a name="what-you-need"></a>Lo que necesita
[!INCLUDE [free-trial-note](../../includes/free-trial-note.md)]

Necesita lo siguiente para completar este tutorial:

* Un inquilino de Azure Active Directory con usuarios en varios grupos
* Permisos para crear aplicaciones en el inquilino de Azure Active Directory
* Visual Studio 2013, actualización 4 o posterior
* [SDK de Azure 2.8.1 o posterior](https://azure.microsoft.com/downloads/)

<a name="bkmk_deploy"></a>

## <a name="create-and-deploy-a-web-app-to-azure"></a>Creación e implementación de una aplicación web en Azure
1. En Visual Studio, haga clic en **Archivo** > **Nuevo** > **Proyecto**.
2. Seleccione **Aplicación web ASP.NET**, elija el nombre del proyecto y haga clic en **Aceptar**.
3. Seleccione la plantilla **MVC** y cambie a **Sin autenticación**. Asegúrese de que **Host en la nube** está seleccionado y haga clic en **Aceptar**.
   
    ![](./media/web-sites-dotnet-lob-application-azure-ad/1-create-mvc-no-authentication.png)
4. En el cuadro de diálogo **Crear App Service**, haga clic en **Agregar una cuenta** (y, en la lista desplegable, en **Agregar una cuenta**) para iniciar sesión en su cuenta de Azure.
5. Después de iniciar sesión, configure la aplicación web. Cree un grupo de recursos y un nuevo plan del Servicio de aplicaciones al hacer clic en el botón **Nuevo** correspondiente. Haga clic en **Explore additional Azure services** (Explorar servicios adicionales de Azure) para continuar.
   
    ![](./media/web-sites-dotnet-lob-application-azure-ad/2-create-app-service.png)
6. En la pestaña **Servicios**, haga clic en **+** para agregar una base de datos SQL Database para la aplicación. 
   
    ![](./media/web-sites-dotnet-lob-application-azure-ad/3-add-sql-database.png)
7. En **Configurar SQL Database**, haga clic en **Nuevo** para crear una instancia de SQL Server.
8. En **Configurar SQL Server**, configure la instancia de SQL Server. A continuación, haga clic en **Aceptar**, **Aceptar** y **Crear** para iniciar la creación de la aplicación en Azure.
9. En **Actividad del Servicio de aplicaciones de Azure**, verá cuándo finaliza la creación de la aplicación. Haga clic en **Publicar &lt;*NombreAplicación*> en esta aplicación web ahora** y, luego, en **Publicar**. 
   
    Cuando Visual Studio termine, la aplicación de publicación se abre en el explorador. 
   
    ![](./media/web-sites-dotnet-lob-application-azure-ad/4-published-shown-in-browser.png)

<a name="bkmk_auth"></a>

## <a name="configure-authentication-and-directory-access"></a>Configuración de la autenticación y el acceso al directorio
1. Inicie sesión en [Azure Portal](https://portal.azure.com).
2. En el menú de la izquierda, haga clic en **App Services** > **&lt;*NombreAplicación*>** > **Autenticación/autorización**.
   
    ![](./media/web-sites-dotnet-lob-application-azure-ad/5-app-service-authentication.png)
3. Active la autenticación de Azure Active Directory con un clic en **Activada** > **Azure Active Directory** > **Rápido** > **Aceptar**.
   
    ![](./media/web-sites-dotnet-lob-application-azure-ad/6-authentication-express.png)
4. Haga clic en **Guardar** en la barra de comandos.
   
    ![](./media/web-sites-dotnet-lob-application-azure-ad/7-authentication-save.png)
   
    Una vez guardada correctamente la configuración de autenticación, intente volver a su aplicación en el explorador. La configuración predeterminada exige la autenticación en toda la aplicación. Si aún no ha iniciado sesión, se le redirigirá a una pantalla para ello. Una vez iniciada la sesión, verá la aplicación protegida por HTTPS. A continuación, debe habilitar el acceso a los datos del directorio. 
5. Vaya al [Portal clásico](https://manage.windowsazure.com).
6. En el menú de la izquierda, haga clic en **Active Directory** > **Directorio predeterminado** > **Aplicaciones** > **&lt;*NombreAplicación*>**.
   
    ![](./media/web-sites-dotnet-lob-application-azure-ad/8-find-aad-application.png)
   
    Esta es la aplicación de Azure Active Directory que crea automáticamente el Servicio de aplicaciones para habilitar la característica Autorización/autenticación.
7. Haga clic en **Usuarios** y en **Grupos** para asegurarse de que tiene usuarios y grupos en el directorio. Si no es así, cree grupos y usuarios de prueba.
   
    ![](./media/web-sites-dotnet-lob-application-azure-ad/9-create-users-groups.png)
8. Haga clic en **Configurar** para configurar esta aplicación.
9. Desplácese hacia abajo hasta la sección **Claves** y agregue una clave al seleccionar una duración. A continuación, haga clic en **Permisos delegados** y seleccione **Leer datos de directorio**. 
   Haga clic en **Guardar**.
   
    ![](./media/web-sites-dotnet-lob-application-azure-ad/10-configure-aad-application.png)
10. Una vez guardada la configuración, desplácese de nuevo arriba a la sección **Claves** y haga clic en el botón **Copiar** para copiar la clave de cliente. 
    
     ![](./media/web-sites-dotnet-lob-application-azure-ad/11-get-app-key.png)
    
    > [!IMPORTANT]
    > Si sale ahora esta página, ya no podrá volver a acceder a esta clave de cliente.
    > 
    > 
11. A continuación, debe configurar la aplicación web con esta clave. Inicie sesión en el [Explorador de recursos de Azure](https://resources.azure.com) con su cuenta de Azure.
12. En la parte superior de la página, haga clic en **Lectura/escritura** para realizar cambios en el Explorador de recursos de Azure.
    
    ![](./media/web-sites-dotnet-lob-application-azure-ad/12-resource-manager-writable.png)
13. Encuentre la configuración de autenticación de la aplicación, que se encuentra en subscriptions > **&lt;*subscriptionname*>** > **resourceGroups** > **&lt;*resourcegroupname*>** > **providers** > **Microsoft.Web** > **sites** > **&lt;*appname*>** > **config** > **authsettings**.
14. Haga clic en **Editar**.
    
    ![](./media/web-sites-dotnet-lob-application-azure-ad/13-edit-authsettings.png)
15. En el panel de edición, establezca las propiedades `clientSecret` y `additionalLoginParams` como sigue.
    
        ...
        "clientSecret": "<client key from the Azure Active Directory application>",
        ...
        "additionalLoginParams": ["response_type=code id_token", "resource=https://graph.windows.net"],
        ...
16. Haga clic en **Put** en la parte superior para enviar los cambios.
    
    ![](./media/web-sites-dotnet-lob-application-azure-ad/14-edit-parameters.png)
17. Ahora, para probar si tiene el token de autorización para tener acceso a la API Graph de Azure Active Directory, solo tiene que ir a **https://&lt;*NombreAplicación*>.azurewebsites.net/.auth/me** en el explorador. Si ha configurado todas las opciones correctamente, debería ver la propiedad `access_token` en la respuesta JSON.
    
    La ruta de dirección URL `~/.auth/me` se administra mediante la autenticación o autorización de App Service para proporcionar toda la información relacionada con la sesión autenticada. Para obtener más información, consulte [Autenticación y autorización en el Servicio de aplicaciones de Azure](../app-service/app-service-authentication-overview.md).
    
    > [!NOTE]
    > La ruta de dirección URL `access_token` tiene un periodo de caducidad. Sin embargo, la autorización y autenticación de App Service proporciona una funcionalidad de actualización del token con `~/.auth/refresh`. Para obtener más información sobre cómo utilizarlo, vea [App Service Token Store](https://cgillum.tech/2016/03/07/app-service-token-store/)(Almacén de tokens de App Service).
    > 
    > 

A continuación, hará algo útil con los datos del directorio.

<a name="bkmk_crud"></a>

## <a name="add-line-of-business-functionality-to-your-app"></a>Funcionalidad de línea de negocio para la aplicación
Ahora, cree un rastreador de elementos de trabajo CRUD sencillo.  

1. En la carpeta ~\Models, cree un archivo de clase denominado WorkItem.cs y cambie `public class WorkItem {...}` por el siguiente código:
   
     using System.ComponentModel.DataAnnotations;
   
     public class WorkItem   {
   
         [Key]
         public int ItemID { get; set; }
         public string AssignedToID { get; set; }
         public string AssignedToName { get; set; }
         public string Description { get; set; }
         public WorkItemStatus Status { get; set; }
     }
   
     public enum WorkItemStatus   {
   
         Open,
         Investigating,
         Resolved,
         Closed
     }
2. Compile el proyecto para que su nuevo modelo sea accesible a la lógica de scaffolding en Visual Studio.
3. Agregue un nuevo elemento con scaffold `WorkItemsController` a la carpeta ~\Controllers (haga clic con el botón derecho en **Controladores**, seleccione **Agregar** y **Nuevo elemento con scaffold**). 
4. Seleccione **Controlador MVC 5 con vistas, usando Entity Framework** y haga clic en **Agregar**.
5. Seleccione el modelo que ha creado, haga clic en **+** y en **Agregar** para agregar un contexto de datos y, a continuación, haga clic en **Agregar**.
   
   ![](./media/web-sites-dotnet-lob-application-azure-ad/16-add-scaffolded-controller.png)
6. En ~\Views\WorkItems\Create.cshtml (un elemento con scaffold automático), encuentre el método auxiliar `Html.BeginForm` y realice los siguientes cambios resaltados:  
   
   <pre class="prettyprint">
   @model WebApplication1.Models.WorkItem
   
   @{
    ViewBag.Title = &quot;Create&quot;;
   }
   
   &lt;h2&gt;Create&lt;/h2&gt;
   
   @using (Html.BeginForm(<mark>&quot;Create&quot;, &quot;WorkItems&quot;, FormMethod.Post, new { id = &quot;main-form&quot; }</mark>)) 
   {
    @Html.AntiForgeryToken()
   
    &lt;div class=&quot;form-horizontal&quot;&gt;
        &lt;h4&gt;WorkItem&lt;/h4&gt;
        &lt;hr /&gt;
        @Html.ValidationSummary(true, &quot;&quot;, new { @class = &quot;text-danger&quot; })
        &lt;div class=&quot;form-group&quot;&gt;
            @Html.LabelFor(model =&gt; model.AssignedToID, htmlAttributes: new { @class = &quot;control-label col-md-2&quot; })
            &lt;div class=&quot;col-md-10&quot;&gt;
                @Html.EditorFor(model =&gt; model.AssignedToID, new { htmlAttributes = new { @class = &quot;form-control&quot;<mark>, @type = &quot;hidden&quot;</mark> } })
                @Html.ValidationMessageFor(model =&gt; model.AssignedToID, &quot;&quot;, new { @class = &quot;text-danger&quot; })
            &lt;/div&gt;
        &lt;/div&gt;
   
        &lt;div class=&quot;form-group&quot;&gt;
            @Html.LabelFor(model =&gt; model.AssignedToName, htmlAttributes: new { @class = &quot;control-label col-md-2&quot; })
            &lt;div class=&quot;col-md-10&quot;&gt;
                @Html.EditorFor(model =&gt; model.AssignedToName, new { htmlAttributes = new { @class = &quot;form-control&quot; } })
                @Html.ValidationMessageFor(model =&gt; model.AssignedToName, &quot;&quot;, new { @class = &quot;text-danger&quot; })
            &lt;/div&gt;
        &lt;/div&gt;
   
        &lt;div class=&quot;form-group&quot;&gt;
            @Html.LabelFor(model =&gt; model.Description, htmlAttributes: new { @class = &quot;control-label col-md-2&quot; })
            &lt;div class=&quot;col-md-10&quot;&gt;
                @Html.EditorFor(model =&gt; model.Description, new { htmlAttributes = new { @class = &quot;form-control&quot; } })
                @Html.ValidationMessageFor(model =&gt; model.Description, &quot;&quot;, new { @class = &quot;text-danger&quot; })
            &lt;/div&gt;
        &lt;/div&gt;
   
        &lt;div class=&quot;form-group&quot;&gt;
            @Html.LabelFor(model =&gt; model.Status, htmlAttributes: new { @class = &quot;control-label col-md-2&quot; })
            &lt;div class=&quot;col-md-10&quot;&gt;
                @Html.EnumDropDownListFor(model =&gt; model.Status, htmlAttributes: new { @class = &quot;form-control&quot; })
                @Html.ValidationMessageFor(model =&gt; model.Status, &quot;&quot;, new { @class = &quot;text-danger&quot; })
            &lt;/div&gt;
        &lt;/div&gt;
   
        &lt;div class=&quot;form-group&quot;&gt;
            &lt;div class=&quot;col-md-offset-2 col-md-10&quot;&gt;
                &lt;input type=&quot;submit&quot; value=&quot;Create&quot; class=&quot;btn btn-default&quot;<mark> id=&quot;submit-button&quot;</mark> /&gt;
            &lt;/div&gt;
        &lt;/div&gt;
    &lt;/div&gt;
   }
   
   &lt;div&gt;
    @Html.ActionLink(&quot;Back to List&quot;, &quot;Index&quot;)
   &lt;/div&gt;
   
   @section Scripts {
    @Scripts.Render(&quot;~/bundles/jqueryval&quot;)
    <mark>&lt;script&gt;
        // People/Group Picker Code
        var maxResultsPerPage = 14;
        var input = document.getElementById(&quot;AssignedToName&quot;);
   
        // Access token from request header, and tenantID from claims identity
        var token = &quot;@Request.Headers[&quot;X-MS-TOKEN-AAD-ACCESS-TOKEN&quot;]&quot;;
        var tenant =&quot;@(System.Security.Claims.ClaimsPrincipal.Current.Claims
                        .Where(c => c.Type == &quot;http://schemas.microsoft.com/identity/claims/tenantid&quot;)
                        .Select(c => c.Value).SingleOrDefault())&quot;;
   
        var picker = new AadPicker(maxResultsPerPage, input, token, tenant);
   
        // Submit the selected user/group to be asssigned.
        $(&quot;#submit-button&quot;).click({ picker: picker }, function () {
            if (!picker.Selected())
                return;
            $(&quot;#main-form&quot;).get()[0].elements[&quot;AssignedToID&quot;].value = picker.Selected().objectId;
        });
    &lt;/script&gt;</mark>
   }
   </pre>
   
   Observe que `token` y `tenant` los usa el objeto `AadPicker` para realizar llamadas de API Graph de Azure Active Directory. Agregará `AadPicker` más adelante.     
   
   > [!NOTE]
   > También puede obtener `token` y `tenant` desde el cliente con `~/.auth/me`, pero se trataría de una llamada adicional al servidor. Por ejemplo:
   > 
   > $.ajax({ dataType: "json", url: "/.auth/me", success: function (data) { var token = data[0].access_token; var tenant = data[0].user_claims .find(c => c.typ === 'http://schemas.microsoft.com/identity/claims/tenantid') .val; } });
   > 
   > 
7. Realice los mismos cambios con ~\Views\WorkItems\Edit.cshtml.
8. El objeto `AadPicker` se define en un script que se debe agregar al proyecto. Haga clic con el botón derecho en la carpeta ~\Scripts, seleccione **Agregar** y haga clic en **Archivo JavaScript**. Escriba `AadPickerLibrary` como nombre de archivo y haga clic en **Aceptar**.
9. Copie [este](https://raw.githubusercontent.com/cephalin/active-directory-dotnet-webapp-roleclaims/master/WebApp-RoleClaims-DotNet/Scripts/AadPickerLibrary.js) contenido en ~\Scripts\AadPickerLibrary.js.
   
   En el script, el objeto `AadPicker` llama a la [API Graph de Azure Active Directory](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/api-catalog) para que busque usuarios y grupos que coincidan con la entrada.  
10. ~\Scripts\AadPickerLibrary.js también usa el [widget Autocomplete de jQuery UI](https://jqueryui.com/autocomplete/). Por lo que necesitará agregar jQuery UI al proyecto. Haga clic con el botón derecho en el proyecto y haga clic en **Administrar paquetes NuGet**.
11. En el Administrador de paquetes NuGet, haga clic en Examinar, escriba **jquery-ui** en la barra de búsqueda y haga clic en **jQuery.UI.Combined**.
    
    ![](./media/web-sites-dotnet-lob-application-azure-ad/17-add-jquery-ui-nuget.png)
12. En el panel derecho, haga clic en **Instalar** y en **Aceptar** para continuar.
13. Abra ~\App_Start\BundleConfig.cs y realice los cambios resaltados siguientes:  
    
    <pre class="prettyprint">
    public static void RegisterBundles(BundleCollection bundles)
    {
        bundles.Add(new ScriptBundle(&quot;~/bundles/jquery&quot;).Include(
                    &quot;~/Scripts/jquery-{version}.js&quot;<mark>,
                    &quot;~/Scripts/jquery-ui-{version}.js&quot;,
                    &quot;~/Scripts/AadPickerLibrary.js&quot;</mark>));
    
        bundles.Add(new ScriptBundle(&quot;~/bundles/jqueryval&quot;).Include(
                    &quot;~/Scripts/jquery.validate*&quot;));
    
        // Use the development version of Modernizr to develop with and learn from. Then, when you&#39;re
        // ready for production, use the build tool at http://modernizr.com to pick only the tests you need.
        bundles.Add(new ScriptBundle(&quot;~/bundles/modernizr&quot;).Include(
                    &quot;~/Scripts/modernizr-*&quot;));
    
        bundles.Add(new ScriptBundle(&quot;~/bundles/bootstrap&quot;).Include(
                    &quot;~/Scripts/bootstrap.js&quot;,
                    &quot;~/Scripts/respond.js&quot;));
    
        bundles.Add(new StyleBundle(&quot;~/Content/css&quot;).Include(
                    &quot;~/Content/bootstrap.css&quot;,
                    &quot;~/Content/site.css&quot;<mark>,
                    &quot;~/Content/themes/base/jquery-ui.css&quot;</mark>));
    }
    </pre>
    
    Existen más formas productivas de administrar los archivos JavaScript y CSS en la aplicación. Sin embargo, por motivos de simplicidad solo se incluyen en los paquetes que se cargan con cada vista.
14. Finalmente, en ~\Global.asax, agregue la siguiente línea de código en el método `Application_Start()`. `Ctrl`+`.` en cada error de resolución de nombres para corregirlo.
    
        AntiForgeryConfig.UniqueClaimTypeIdentifier = ClaimTypes.NameIdentifier;
    
    > [!NOTE]
    > Necesita esta línea de código porque la plantilla de MVC predeterminada usa la decoración <code>[ValidateAntiForgeryToken]</code> en algunas de las acciones. Debido al comportamiento descrito por [Brock Allen](https://twitter.com/BrockLAllen) en [MVC 4, AntiForgeryToken and Claims](http://brockallen.com/2012/07/08/mvc-4-antiforgerytoken-and-claims/), su HTTP POST puede generar un error de validación de token antifalsificación porque:
    > 
    > * Azure Active Directory no envía http://schemas.microsoft.com/accesscontrolservice/2010/07/claims/identityprovider, que el token antifalsificación requiere de manera predeterminada.
    > * Si Azure Active Directory está sincronizado con directorios con AD FS, la confianza de AD FS tampoco envía de manera predeterminada la notificación de http://schemas.microsoft.com/accesscontrolservice/2010/07/claims/identityprovider, aunque puede configurar AD FS manualmente para enviar esta notificación.
    > 
    > `ClaimTypes.NameIdentifies` especifica la notificación `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier`, que proporciona Azure Active Directory.  
    > 
    > 
15. Ahora, publique los cambios. Haga clic con el botón derecho en el proyecto y haga clic en **Publicar**.
16. Haga clic en **Configuración**, asegúrese de que hay una cadena de conexión a la instancia de SQL Database, seleccione **Actualizar base de datos** para realizar los cambios de esquema para el modelo y haga clic en **Publicar**.
    
    ![](./media/web-sites-dotnet-lob-application-azure-ad/18-publish-crud-changes.png)
17. En el explorador, vaya a https://&lt;*NombreAplicación*>.azurewebsites.net/workitems y haga clic en **Crear nuevo**.
18. Haga clic en el cuadro **AssignedToName** . Ahora debería ver los usuarios y grupos desde el inquilino de Azure Active Directory en una lista desplegable. Puede escribir para filtrar o utilizar la clave `Up` o `Down`; también puede hacer clic para seleccionar el usuario o grupo. 
    
    ![](./media/web-sites-dotnet-lob-application-azure-ad/19-use-aadpicker.png)
19. Haga clic en **Crear** para guardar los cambios. A continuación, haga clic en **Editar** en el elemento de trabajo creado para observar el mismo comportamiento.

Enhorabuena, está ejecutando una aplicación de línea de negocio en Azure con acceso al directorio. Con la API Graph puede hacer mucho más. Consulte [Azure AD Graph API reference](https://msdn.microsoft.com/library/azure/ad/graph/api/api-catalog)(Referencias de la API Graph de Azure AD).

<a name="next"></a>

## <a name="next-step"></a>siguiente paso
Si necesita control de acceso basado en roles (RBAC) para la aplicación de línea de negocio en Azure, consulte [WebApp-RoleClaims-DotNet](https://github.com/Azure-Samples/active-directory-dotnet-webapp-roleclaims) para obtener un ejemplo del equipo de Azure Active Directory. Muestra cómo habilitar los roles para su aplicación de Azure Active Directory y, a continuación, autorizar a los usuarios con la decoración `[Authorize]` .

Si la aplicación de línea de negocio necesita acceso a datos locales, consulte [Acceso a recursos locales mediante conexiones híbridas en el Servicio de aplicaciones de Azure](web-sites-hybrid-connection-get-started.md).

<a name="bkmk_resources"></a>

## <a name="further-resources"></a>Recursos adicionales
* [Autenticación y autorización en el Servicio de aplicaciones de Azure](../app-service/app-service-authentication-overview.md)
* [Autenticación con Active Directory local en aplicaciones de Azure](web-sites-authentication-authorization.md)
* [Creación de una aplicación de línea de negocio en Azure con autenticación de AD FS](web-sites-dotnet-lob-application-adfs.md)
* [App Service Auth and the Azure AD Graph API (Autenticación del Servicio de aplicaciones y la API Graph de Azure AD)](https://cgillum.tech/2016/03/25/app-service-auth-aad-graph-api/)
* [Ejemplos y documentación de Microsoft Azure Active Directory](https://github.com/AzureADSamples)
* [Tipos de notificaciones y tokens admitidos de Azure Active Directory](http://msdn.microsoft.com/library/azure/dn195587.aspx)

