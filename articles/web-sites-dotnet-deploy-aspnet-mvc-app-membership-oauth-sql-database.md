<properties urlDisplayName="Website with SQL Database" pageTitle="Implementación de una aplicación de ASP.NET MVC segura con suscripciones, OAuth y Base de datos SQL en un sitio web de Azure" metaKeywords="tutorial hello world de Azure, tutorial de introducción de Azure, tutorial de Base de datos SQL, tutorial hello world para .NET de Azure, turorial hello world para C# de Azure, tutorial para C# de SQL Azure" description="Learn how to develop an ASP.NET MVC 5 website with a SQL Database back-end deploy it to Azure." metaCanonical="" services="web-sites,sql-database" documentationCenter=".NET" title="Deploy a Secure ASP.NET MVC 5 app with Membership, OAuth, and SQL Database to an Azure Website" authors="riande"  solutions="" writer="riande" manager="wpickett" editor="mollybos"  />

<tags ms.service="web-sites" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="dotnet" ms.topic="article" ms.date="10/14/2014" ms.author="riande" /> 



# Implementación de una aplicación ASP.NET MVC 5 segura con suscripción, OAuth y Base de datos SQL en un sitio web de Azure

***Actualizado el 12 de octubre de 2014.***

Este tutorial muestra cómo desarrollar una aplicación web ASP.NET MVC 5 segura que permita a los usuarios iniciar sesión con las credenciales de Facebook o Google. Además, la aplicación se implementará en Azure.

Puede abrir una cuenta de Azure de manera gratuita y, si todavía no tiene Visual Studio 2013, el SDK instala automáticamente Visual Studio 2013 Express para Web. Puede empezar a desarrollar contenido para Azure sin coste alguno.

En este tutorial se supone que no tiene ninguna experiencia previa con Azure. Cuando acabe, tendrá una aplicación web controlada por datos segura que se ejecutará en la nube y utilizará una base de datos en la nube.

Aprenderá a realizar los siguientes procedimientos:

* Crear un proyecto ASP.NET MVC 5 seguro y publicarlo en un sitio web de Azure
* Uso de [OAuth](http://oauth.net/ "http://oauth.net/") y la base de datos de suscripciones de ASP.NET para proteger su aplicación.
* Utilizar una Base de datos SQL para almacenar datos en Azure

Va a desarrollar una aplicación web de lista de contactos sencilla basada en ASP.NET MVC 5 y que utiliza ADO.NET Entity Framework para obtener acceso a la base de datos. La ilustración siguiente muestra la página de inicio de sesión de la aplicación final:

![login page][rxb]

>[WACOM.NOTE]Para realizar este tutorial, necesita una cuenta de Microsoft Azure. Si aún no la tiene, puede <a href="/es-es/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F" target="_blank">activar los beneficios de suscripción a MSDN</a>, o bien <a href="/es-es/pricing/free-trial/?WT.mc_id=A261C142F" target="_blank">regístrese para obtener una evaluación gratuita</a>.


Apartados de este tutorial:

- [Configuración del entorno de desarrollo](#setupdevenv)
- [Creación de una aplicación de ASP.NET MVC 5][createapplication]
- [Implementación de la aplicación en Azure][deployapp1]
- [Incorporación de una base de datos a la aplicación][adddb]
- [Incorporación de un proveedor de OAuth][]
- [Implementación de la aplicación en Azure][deployapp11]
- [Pasos siguientes][]


[WACOM.INCLUDE [install-sdk-2013-only](../includes/install-sdk-2013-only.md)]

Para usar el nuevo certificado SSL para localhost, tendrá que instalar [Visual Studio 2013 Update 3](http://go.microsoft.com/fwlink/?LinkId=390521) o una versión superior.

<h2><a name="bkmk_createmvc4app"></a>Creación de una aplicación ASP.NET MVC 5</h2>

### Creación del proyecto

1. En el menú **Archivo**, haga clic en **Nuevo proyecto**.

	![New Project in File menu](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/gs13newproj.png)

1. En el cuadro de diálogo **Nuevo proyecto**, expanda **C#** y seleccione **Web** en **Plantillas instaladas**, luego seleccione **Aplicación web ASP.NET**.

1. Nombre a la aplicación **ContactManager** y haga clic en **Aceptar**.

	![New Project dialog box](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/GS13newprojdb.png)
 
	**Nota:**Asegúrese de escribir "ContactManager". En los bloques de código que copiará más tarde se supone que el nombre del proyecto es ContactManager. 

1. En el cuadro de diálogo **Nuevo proyecto de ASP.NET**, seleccione la plantilla **MVC**. Compruebe que la opción **Autenticación** está establecida en **Cuentas de usuario individuales*, que la casilla **Hospedar en la nube** está activada y que el valor **Sitio web** está seleccionado.

	![New ASP.NET Project dialog box](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/ss1.PNG)

1. El Asistente para configuración le sugerirá un nombre único basado en *ContactManager* (consulte la imagen siguiente). Seleccione una región cerca de usted. Puede usar [azurespeed.com](http://www.azurespeed.com/ "AzureSpeed.com") para encontrar el centro de datos con la latencia más baja. 
2. Si no ha creado antes un servidor de base de datos, seleccione **Crear nuevo servidor** y escriba un nombre de usuario y una contraseña para la base de datos.

	![Configure Azure Website](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/configAz.PNG)

Si tiene un servidor de base de datos, utilícelo para crear una nueva base de datos. Los servidores de base de datos son un valioso recurso y, por lo general, es conveniente crear varias bases de datos en el mismo servidor con fines de prueba y desarrollo en lugar de crear un servidor de base de datos por base de datos. Asegúrese de que su sitio web y la base de datos estén en la misma región.

![Configure Azure Website](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/configWithDB.PNG)

### Establecimiento del encabezado y pie de página


1. En el **Explorador de soluciones**, abra el archivo *Layout.cshtml* de la carpeta *Views\Shared*.

	![_Layout.cshtml in Solution Explorer][newapp004]

1. Reemplace el marcado del archivo *Layout.cshtml* por el código siguiente. A continuación, se resaltan los cambios.

<pre>
			&lt;!DOCTYPE html&gt;
			&lt;html&gt;
			&lt;head&gt;
			    &lt;meta charset="utf-8" /&gt;
			    &lt;meta name="viewport" content="width=device-width, initial-scale=1.0"&gt;
			    &lt;title&gt;@ViewBag.Title - <mark>Contact Manager</mark>&lt;/title&gt;
			    @Styles.Render("~/Content/css")
			    @Scripts.Render("~/bundles/modernizr")
			
			&lt;/head&gt;
			&lt;body&gt;
			    &lt;div class="navbar navbar-inverse navbar-fixed-top"&gt;
			        &lt;div class="container"&gt;
			            &lt;div class="navbar-header"&gt;
			                &lt;button type="button" class="navbar-toggle" data-toggle="collapse" data-target=".navbar-collapse"&gt;
			                    &lt;span class="icon-bar"&gt;&lt;/span&gt;
			                    &lt;span class="icon-bar"&gt;&lt;/span&gt;
			                    &lt;span class="icon-bar"&gt;&lt;/span&gt;
			                &lt;/button&gt;
			                @Html.ActionLink("<mark>CM Demo</mark>", "Index", "<mark>Cm</mark>", new { area = "" }, new { @class = "navbar-brand" })
			            &lt;/div&gt;
			            &lt;div class="navbar-collapse collapse"&gt;
			                &lt;ul class="nav navbar-nav"&gt;
			                    &lt;li&gt;@Html.ActionLink("Home", "Index", "Home")&lt;/li&gt;
			                    &lt;li&gt;@Html.ActionLink("About", "About", "Home")&lt;/li&gt;
			                    &lt;li&gt;@Html.ActionLink("Contact", "Contact", "Home")&lt;/li&gt;
			                &lt;/ul&gt;
			                @Html.Partial("_LoginPartial")
			            &lt;/div&gt;
			        &lt;/div&gt;
			    &lt;/div&gt;
			    &lt;div class="container body-content"&gt;
			        @RenderBody()
			        &lt;hr /&gt;
			        &lt;footer&gt;
			            &lt;p&gt;&amp;copy; @DateTime.Now.Year - <mark>Contact Manager</mark>&lt;/p&gt;
			        &lt;/footer&gt;
			    &lt;/div&gt;
			
			    @Scripts.Render("~/bundles/jquery")
			    @Scripts.Render("~/bundles/bootstrap")
			    @RenderSection("scripts", se requiere: false)
			&lt;/body&gt;
			&lt;/html&gt;
</pre>


![code changes](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rs3.png)


### Ejecución de la aplicación de forma local

1. Presione CTRL+F5 para ejecutar la aplicación.

	Aparece la página principal de la aplicación en el explorador predeterminado.

	![Web site running locally](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rr2.png)

Esto es todo lo que necesita hacer por ahora para crear la aplicación que va a implementar en Azure. 

## Habilitación de SSL para el proyecto ##

1. Habilite SSL. En el Explorador de soluciones, haga clic en el proyecto **ContactManager** y, a continuación, en F4 para mostrar el diálogo de propiedades. Cambie **SSL Enabled** a true. Copie la **URL de SSL**. La dirección URL de SSL será https://localhost:44300/ a menos que haya creado anteriormente sitios web de SSL.

	![enable SSL][rxSSL]
 
1. En el Explorador de soluciones, haga clic con el botón secundario en el proyecto **Contact Manager** y haga clic en **Propiedades**.
1. En la pestaña izquierda, haga clic en **Web**.
1. Cambie la **URL del proyecto** para que se use la **URL de SSL** y guarde la página (Control S).

	![enable SSL](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rrr1.png)
 
1. Compruebe que es Internet Explorer el explorador que inicia Visual Studio, como se muestra en la imagen siguiente:

	![default browser](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/ss12.PNG)

	El selector de explorador le permite especificar el explorador que inicia Visual Studio.

 	![browser selector](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/ss13.png)

Puede seleccionar varios exploradores y hacer que Visual Studio actualice cada explorador cuando haga cambios. Para obtener más información, consulte [Uso de vínculos de explorador en Visual Studio 2013](http://www.asp.net/visual-studio/overview/2013/using-browser-link).


1. Presione CTRL+F5 para ejecutar la aplicación. Siga las instrucciones para confiar en el certificado autofirmado que ha generado IIS Express.

	 ![instructions to trust the self-signed certificate that IIS Express has generated](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/ss26.PNG)

1. Lea el cuadro de diálogo **Advertencia de seguridad** y luego haga clic en **Sí** si desea instalar el certificado que representa **localhost**.

 	![localhost IIS Express certificate warning ](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/ss27.PNG)

1. IE muestra la página *Home* y no hay advertencias de SSL.

	 ![IE with localhost SSL and no warnings](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/ss28.PNG)

	Google Chrome también acepta el certificado y mostrará el contenido HTTPS sin advertencia. Firefox utiliza su propio almacén de certificados, así que mostrará una advertencia.

	 ![FireFox Cert Warning](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/ss30.PNG)

<h2><a name="bkmk_deploytowindowsazure1"></a>Implementación de la aplicación en Azure</h2>

1. En Visual Studio, haga clic con el botón secundario en el proyecto en el **Explorador de soluciones** y seleccione **Publicar** en el menú contextual.

	![Publish in project context menu](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/GS13publish.png)
	
   Se abre el asistente de **Publicación web**.

1. En el cuadro de diálogo **Publish Web**, haga clic en **Publicar**.

	![Publish](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rr3.png)

	La aplicación creada ahora se ejecuta en la nube. La próxima vez que implemente la aplicación, solo se implementarán los archivos modificados o nuevos.

	![Running in Cloud](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/ss4.PNG)

<h2><a name="bkmk_addadatabase"></a>Incorporación de una base de datos a la aplicación</h2>

A continuación, actualizará la aplicación de MVC incorporando funciones que permitan mostrar y actualizar los contactos y almacenar los datos en una base de datos. La aplicación utilizará Entity Framework (EF) para crear la base de datos y para leer y actualizar los datos.

### Incorporación de clases de modelos de datos para los contactos

Se empieza por crear un modelo de datos sencillo en código.

1. En el **Explorador de soluciones**, haga clic con el botón secundario en la carpeta Models y luego haga clic en **Agregar** y en **Clase**.

	![Add Class in Models folder context menu](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rr5.png)

2. En el cuadro de diálogo **Agregar nuevo elemento**, nombre al nuevo archivo de clase *Contact.cs* y luego haga clic en **Agregar**.

	![Add New Item dialog box][adddb002]

3. Reemplace el contenido del archivo Contacts.cs por el código siguiente.

        using System.ComponentModel.DataAnnotations;
        using System.Globalization;
        namespace ContactManager.Models
        {
            public class Contact
            {
                public int ContactId { get; set; }
                public string Name { get; set; }
                public string Address { get; set; }
                public string City { get; set; }
                public string State { get; set; }
                public string Zip { get; set; }
                [DataType(DataType.EmailAddress)]
                public string Email { get; set; }
            }
        }
La clase **Contacts** define los datos que almacenará para cada contacto, además de una clave primaria *ContactID* que es necesaria para la base de datos.

### Creación de páginas web que permiten a los usuarios de aplicaciones utilizar los contactos

La característica de scaffolding de ASP.NET MVC puede generar automáticamente código que realiza acciones de creación, lectura, actualización y eliminación (CRUD).

<h2><a name="bkmk_addcontroller"></a>Incorporación de un controlador y una vista para los datos</h2>

1. Compile el proyecto **(Ctrl+Mayús+B)**. (Debe crear el proyecto antes de usar el mecanismo de scaffolding). 
1. En el **Explorador de soluciones**, haga clic con el botón secundario en la carpeta Controllers y luego haga clic en **Agregar** y en **Controlador**.

	![Add Controller in Controllers folder context menu][addcode001]

5. En el cuadro de diálogo **Add Scaffold** (Agregar Scaffolding), seleccione **MVC 5 Controller with views, using EF** (Controlador MVC 5 con vistas, mediante EF) y luego haga clic en **Agregar**.
	
	![Add Scaffold dlg](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rr6.png)


1. En el cuadro desplegable **Clase de modelo**, seleccione **Contacto (ContactManager.Models)**. (Consulte la imagen que aparece a continuación).
1. En la **clase de contexto de datos**, seleccione **ApplicationDbContext (ContactManager.Models)**. El valor **ApplicationDbContext** se utilizará tanto para la base de datos de suscripciones como para los datos de nuestros contactos.
1. En el cuadro de entrada de texto **Nombre de Controller**, escriba "CmController". 

	![New data ctx dlg](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/ss5.PNG)

1. Haga clic en **Agregar**.

   Visual Studio crea métodos y vistas de controlador para las operaciones CRUD de base de datos que afectan a los objetos **Contact**.

## Habilitación de las migraciones, creación de la base de datos e incorporación de datos de ejemplo y un inicializador de datos ##

La siguiente tarea consiste en habilitar la función [Migraciones de Code First](http://msdn.microsoft.com/library/hh770484.aspx) para crear la base de datos a partir del modelo de datos que ha creado.

1. En el menú **Herramientas**, seleccione **Administrador de paquetes de NuGet** y luego **Consola del Administrador de paquetes**.
	![Package Manager Console in Tools menu](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/SS6.png)

2. En la ventana **Consola del Administrador de paquetes**, escriba el siguiente comando:

		enable-migrations
	El comando **enable-migrations** crea una carpeta *Migrations* y la inserta en el archivo *Configuration.cs*, el cual puede editar para inicializar la base de datos y configurar las migraciones de los datos. 

2. En la ventana **Consola del Administrador de paquetes**, escriba el siguiente comando:

		add-migration Initial


	El comando **add-migration Initial** genera un archivo llamado **&lt;date_stamp&gt;Initial** en la carpeta *Migrations* que crea la base de datos. El primer parámetro (**Initial**) es arbitrario y se utiliza para crear el nombre del archivo. Puede ver los archivos de las nuevas clases en el **Explorador de soluciones**.
	En la clase **Initial**, el método **Up** crea la tabla Contacts y el método **Down** (que se usa cuando se desea volver al estado anterior) la anula.
3. Abra el archivo *Migrations\Configuration.cs*. 
4. Agregue el siguiente espacio de nombres. 

    	 using ContactManager.Models;



5. Reemplace el método *Seed* por el código siguiente:

        protected override void Seed(ContactManager.Models.ApplicationDbContext context)
        {
            context.Contacts.AddOrUpdate(p => p.Name,
               new Contact
               {
                   Nombre = "Debra Garcia",
                   Dirección = "1234 Main St",
                   Ciudad = "Redmond",
                   Estado = "WA",
                   Código Postal = "10999",
                   Correo electrónico = "debra@example.com",
               },
                new Contact
                {
                    Nombre = "Thorsten Weinrich",
                    Dirección = "5678 1st Ave W",
                    Ciudad = "Redmond",
                    Estado = "WA",
                    Código Postal = "10999",
                    Correo electrónico = "thorsten@example.com",
                },
                new Contact
                {
                    Nombre = "Yuhong Li",
                    Dirección = "9012 State St",
                    Ciudad = "Redmond",
                    Estado = "WA",
                    Código Postal = "10999",
                    Correo electrónico = "yuhong@example.com",
                },
                new Contact
                {
                    Nombre = "Jon Orton",
                    Dirección = "3456 Maple St",
                    Ciudad = "Redmond",
                    Estado = "WA",
                    Código Postal = "10999",
                    Correo electrónico = "jon@example.com",
                },
                new Contact
                {
                    Nombre = "Diliana Alexieva-Bosseva",
                    Dirección = "7890 2nd Ave E",
                    Ciudad = "Redmond",
                    Estado = "WA",
                    Código Postal = "10999",
                    Correo electrónico = "diliana@example.com",
                }
                );
        }

Este código inicializa la base de datos con la información de contacto. Para obtener más información acerca de la inicialización de la base de datos, consulte [Inicialización y depuración de bases de datos de Entity Framework (EF)](http://blogs.msdn.com/b/rickandy/archive/2013/02/12/seeding-and-debugging-entity-framework-ef-dbs.aspx).


6. En la **Consola del Administrador de paquetes**, escriba el comando:

		update-database

	![Package Manager Console commands][addcode009]

	El comando **update-database** ejecuta la primera migración, lo que crea la base de datos. De manera predeterminada, la base de datos que se crea es una base de datos LocalDB de SQL Server Express. 

7. Presione CTRL+F5 para ejecutar la aplicación y luego haga clic en el vínculo **CM Demo**; o vaya a http://localhost:(port#)/Cm. 

	La aplicación muestra los datos de inicialización y ofrece enlaces de edición, detalles y eliminación. Puede crear, editar, eliminar y ver los datos.

	![MVC view of data][rx2]



<h2><a name="addOauth"></a>Adición de un proveedor de OAuth2</h2>

[OAuth](http://oauth.net/ "http://oauth.net/") es un protocolo abierto que ofrece autorización segura a través de un método estándar sencillo para aplicaciones web, móviles y de escritorio. La plantilla de Internet ASP.NET MVC utiliza OAuth para ofrecer Facebook, Twitter, Google y Microsoft como proveedores de autenticación. Aunque este tutorial solo utiliza Google como proveedor de autenticación, puede modificar fácilmente el código para utilizar cualquiera de estos proveedores. Los pasos que hay que seguir para implementar otros proveedores son muy similares a los que verá en este tutorial. Para usar Facebook como proveedor de autenticación, consulte el tutorial [MVC 5 App with Facebook, Twitter, LinkedIn and Google OAuth2 Sign-on ](http://www.asp.net/mvc/tutorials/mvc-5/create-an-aspnet-mvc-5-app-with-facebook-and-google-oauth2-and-openid-sign-on).

Además de la autenticación, este tutorial también utiliza roles para implementar la autorización. Solo los usuarios que se agreguen el rol *canEdit* podrán cambiar datos (es decir, crear, editar o eliminar contactos).

Siga las instrucciones del tutorial [MVC 5 App with Facebook, Twitter, LinkedIn and Google OAuth2 Sign-on ](http://www.asp.net/mvc/tutorials/mvc-5/create-an-aspnet-mvc-5-app-with-facebook-and-google-oauth2-and-openid-sign-on#goog) en **Creating a Google app for OAuth 2 to set up a Google app for OAuth2**. Ejecute y pruebe la aplicación para comprobar que puede iniciar sesión con la autenticación de Google.

<h2><a name="mbrDB"></a>Uso de la API de suscripción</h2>
En esta sección, agregará un usuario local y el rol *canEdit* a la base de datos de suscripciones. Únicamente los usuarios incluidos en el rol *canEdit* podrán editar los datos. Es recomendable nombrar los roles en función de las acciones que pueden realizar, por lo que el nombre canEdit es más aconsejable que *admin*. A medida que la aplicación evoluciona, puede agregar nuevos roles del tipo *canDeleteMembers* en lugar del escasamente descriptivo *superAdmin*.

1. Abra el archivo *migrations\configuration.cs* y agregue las siguientes instrucciones 'using':

        using Microsoft.AspNet.Identity;
        using Microsoft.AspNet.Identity.EntityFramework;

1. Agregue el siguiente método **AddUserAndRole** a la clase:

    
         bool AddUserAndRole(ContactManager.Models.ApplicationDbContext context)
         {
            IdentityResult ir;
            var rm = new RoleManager<IdentityRole>
                (new RoleStore<IdentityRole>(context));
            ir = rm.Create(new IdentityRole("canEdit"));
            var um = new UserManager<ApplicationUser>(
                new UserStore<ApplicationUser>(context));
            var user = new ApplicationUser()
            {
               UserName = "user1@contoso.com",
            };
            ir = um.Create(user, "P_assw0rd1");
            if (ir.Succeeded == false)
               return ir.Succeeded;
            ir = um.AddToRole(user.Id, "canEdit");
            return ir.Succeeded;
         }

1. Llame al nuevo método desde el método **Seed**:
	<pre>
        protected override void Seed(ContactManager.Models.ApplicationDbContext context)
        {
            <mark>AddUserAndRole(context);</mark>
            context.Contacts.AddOrUpdate(p => p.Name,
                // Código quitado para reducir el espacio
        }
	</pre>  
<span></span>
	The following images shows the changes to *Seed* method:

	![code image](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/ss24.PNG)

   This code creates a new role called *canEdit*, creates a new local user *user1@contoso.com*, and adds *user1@contoso.com* to the *canEdit* role. For more information, see the [ASP.NET Identity resource page](http://curah.microsoft.com/55636/aspnet-identity).

## Use Temporary Code to Add New Social Login Users to the canEdit Role  ##
In this section you will temporarily modify the **ExternalLoginConfirmation** method in the Account controller to add new users registering with an OAuth provider to the *canEdit* role. We will temporarily modify the **ExternalLoginConfirmation** method to automatically add new users to an administrative role. Until we provide a tool to add and manage roles, we'll use the temporary automatic registration code below. We hope to provide a tool similar to [WSAT](http://msdn.microsoft.com/es-es/library/ms228053.aspx) in the future which allow you to create and edit user accounts and roles. Later in the tutorial I'll show how you can use **Server Explorer** to add users to roles.  

1. Open the **Controllers\AccountController.cs** file and navigate to the **ExternalLoginConfirmation** method.
1. Add the following call to **AddToRoleAsync** just before the **SignInAsync** call.

                await UserManager.AddToRoleAsync(user.Id, "canEdit");

   The code above adds the newly registered user to the "canEdit" role, which gives them access to action methods that change (edit) data. An image of the code change is shown below:

   ![code](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/ss9.PNG)

Later in the tutorial you will deploy the application to Azure, where you will log-on with Google or another third party authentication provider. This will add your newly registered account to the *canEdit* role. Anyone who finds your site's URL and has a Google ID can then register and update your database. To prevent other people from doing that, you can stop the site. You'll be able to verify who is in the *canEdit* role by examining the database.

In the **Package Manager Console** hit the up arrow key to bring up the following command:

		Update-Database

Run the  **Update-Database** command which will run the **Seed** method, and that will run the **AddUserAndRole** you just added. The **AddUserAndRole** will create the user *user1@contoso.com* and add her to the *canEdit* role.

## Protect the Application with SSL and the Authorize Attribute ##

In this section you will apply the [Authorize](http://msdn.microsoft.com/es-es/library/system.web.mvc.authorizeattribute.aspx) attribute to restrict access to the action methods. Anonymous users will be able to view the **Index** action method of the home controller only. Registered users will be able to see contact data (The **Index** and **Details** pages of the Cm controller), the About, and the Contact pages. Only users in the *canEdit* role will be able to access action methods that change data.

1. Add the [Authorize](http://msdn.microsoft.com/es-es/library/system.web.mvc.authorizeattribute.aspx) filter and the [RequireHttps](http://msdn.microsoft.com/es-es/library/system.web.mvc.requirehttpsattribute.aspx) filter to the application. An alternative approach is to add the [Authorize](http://msdn.microsoft.com/es-es/library/system.web.mvc.authorizeattribute.aspx) attribute and the [RequireHttps](http://msdn.microsoft.com/es-es/library/system.web.mvc.requirehttpsattribute.aspx) attribute to each controller, but it's considered a security best practice to apply them to the entire application. By adding them globally, every new controller and action method you add will automatically be protected, you won't need to remember to apply them. For more information see [Securing your ASP.NET MVC  App and the new AllowAnonymous Attribute](http://blogs.msdn.com/b/rickandy/archive/2012/03/23/securing-your-asp-net-mvc-4-app-and-the-new-allowanonymous-attribute.aspx). Open the *App_Start\FilterConfig.cs* file and replace the *RegisterGlobalFilters* method with the following (which adds the two filters):
		<pre>
        public static void
        RegisterGlobalFilters(GlobalFilterCollection filters)
        {
            filters.Add(new HandleErrorAttribute());
            <mark>filters.Add(new System.Web.Mvc.AuthorizeAttribute());
            filters.Add(new RequireHttpsAttribute());</mark>
        }
		</pre>


	The following image shows the changed code:


	The [Authorize](http://msdn.microsoft.com/es-es/library/system.web.mvc.authorizeattribute.aspx) filter applied in the code above will prevent anonymous users from accessing any methods in the application. You will use the [AllowAnonymous](http://blogs.msdn.com/b/rickandy/archive/2012/03/23/securing-your-asp-net-mvc-4-app-and-the-new-allowanonymous-attribute.aspx) attribute to opt out of the authorization requirement in a couple methods, so anonymous users can log in and can view the home page. The  [RequireHttps](http://msdn.microsoft.com/es-es/library/system.web.mvc.requirehttpsattribute.aspx) will require all access to the web app be through HTTPS.

1. Add the [AllowAnonymous](http://blogs.msdn.com/b/rickandy/archive/2012/03/23/securing-your-asp-net-mvc-4-app-and-the-new-allowanonymous-attribute.aspx) attribute to the **Index** method of the Home controller. The [AllowAnonymous](http://blogs.msdn.com/b/rickandy/archive/2012/03/23/securing-your-asp-net-mvc-4-app-and-the-new-allowanonymous-attribute.aspx) attribute enables you to white-list the methods you want to opt out of authorization. An image of a portion of the HomeController is shown below:	

  	![code](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/ss11.PNG)

2. Do a global search for *AllowAnonymous*, you can see it is used in the log in and registration methods of the Account controller.
1. In *CmController.cs*, add `[Authorize(Roles = "canEdit")]` to the HttpGet and HttpPost methods that change data (Create, Edit, Delete, every action method except Index and Details) in the *Cm* controller. A portion of the completed code is shown below: 

   	![img of code](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rr11.png)



1. If you are still logged in from a previous session, hit the **Log out** link.
1. Click on the **About** or **Contact** links. You will be redirected to the log in page because anonymous users cannot view those pages. 
1. Click the **Register as a new user** link and add a local user with email *joe@contoso.com*. Verify *Joe* can view the Home, About and Contact pages. 

	![login](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/ss14.PNG)

1. Click the *CM Demo* link and verify you see the data. 
1. Click an edit link on the page, you will be redirected to the log in page (because a new local user is not added to the *canEdit* role).
1. Log in as *user1@contoso.com* with password of "P_assw0rd1" (the "0" in "word" is a zero). You will be redirected to the edit page you previously selected. <br/>
   If you can't log in with that account and password, try copying the password from the source code and pasting it. If you still can't log in, check the **UserName** column of the **AspNetUsers** table to verify *user1@contoso.com* was added. 

1. Verify you can make data changes.

<h2><a name="bkmk_deploytowindowsazure11"></a>Implementación de la aplicación en Azure</h2>

1. En Visual Studio, haga clic con el botón secundario en el proyecto en el **Explorador de soluciones** y seleccione **Publicar** en el menú contextual.

	![Publish in project context menu][firsdeploy003]

	Se abrirá el asistente **Publicación web**.

1. Haga clic en la pestaña **Configuración** en el lado izquierdo del cuadro de diálogo **Publicación web**. Haga clic en el icono **v** para seleccionar la **cadena de conexión remota** de **ApplicationDbContext** y seleccione **ContactManagerNN_db**.

   
	![settings](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rrc2.png)

1. En **ContactManagerContext**, seleccione **Ejecutar migraciones de Code First**.

	![settings](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rrc3.png)

1. Haga clic en **Publicar**.
1. Inicie sesión como * user1@contoso.com * (con la contraseña "P_assw0rd1") y compruebe que puede editar los datos.
1. Cierre sesión.
1. Vaya a la [consola para desarrolladores de Google](https://console.developers.google.com/) y en la pestaña **Credentials** (Credenciales) actualice los orígenes de URIS y JavaScript para que usen la dirección URL de Azure.
1. Inicie sesión utilizando Google o Facebook. De este modo, se agregará la cuenta de Google o Facebook al rol **canEdit**. Si recibe un error HTTP 400 con un mensaje que dice que el URI de redirección de la solicitud: https://contactmanager{my version}.azurewebsites.net/signin-google did not match a registered redirect URI.*, you'll have to wait until the changes you made are propagated. If you get this error after more than a minute, verify the URIs are correct.

### Detención del sitio web para evitar que se registren otros usuarios  

1. En el **Explorador de servidores**, vaya a **Sitios web**.
4. Haga clic con el botón secundario en cada instancia del sitio web y seleccione **Detener sitio web**. 

	![stop web site](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rrr2.png) 

	También tiene la opción de seleccionar el sitio web en el Portal de administración de Azure y hacer clic en el icono **Detener** en la parte inferior de la página.

	![stop web site](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rrr3.png)

### Eliminación de AddToRoleAsync, Publish y Test

1. Convierta en comentario o elimine el código siguiente del método **ExternalLoginConfirmation** en el controlador de cuentas: 
                `await UserManager.AddToRoleAsync(user.Id, "canEdit");`
1. Desarrolle el proyecto (lo que guarda los cambios del archivo) y compruebe que no haya errores de compilación.
5. Haga clic con el botón secundario en el proyecto, en el **Explorador de soluciones** y seleccione **Publicar**.

	   ![Publish in project context menu](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/GS13publish.png)
	
4. Haga clic en el botón **Start Preview** (Iniciar vista previa). Solo se implementan los archivos que deben actualizarse.
5. Inicie el sitio web desde Visual Studio o el portal. **No podrá publicar contenido mientras el sitio web esté detenido**.

	![start web site](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/ss15.png)

5. Vuelva a Visual Studio y haga clic en **Publicar**.
3. La aplicación de Azure se abre en el explorador predeterminado. Si ha iniciado sesión, ciérrela para ver la página principal como un usuario anónimo.  
4. Haga clic en el vínculo **About**. Se le redirigirá a la página de inicio de sesión.
5. Haga clic en el vínculo **Register** de la página de inicio de sesión y cree una cuenta local. Utilizaremos esta cuenta local para comprobar que pueda obtener acceso a las páginas de solo lectura pero no a las páginas que cambian datos (están protegidas por el rol *canEdit*). Más adelante en el tutorial eliminaremos la cuenta local. 

	![Register](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/ss16.PNG)

1. Compruebe que puede desplazarse a las páginas *About* y *Contact*.

	![Log off](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/ss17.PNG)

1. Haga clic en el vínculo **CM Demo** para desplazarse al controlador **Cm**. También puede agregar *Cm* a la URL. 

	![CM page](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rrr4.png)
 
1. Haga clic en el vínculo de edición. Se le redirigirá a la página de inicio de sesión. Debajo de **Use another service to log in**, haga clic en Google o Facebook e inicie sesión con la cuenta que registró anteriormente. (Si está trabajando con rapidez y la cookie de la sesión aún no ha expirado, iniciará sesión automáticamente con la cuenta de Google o Facebook utilizada anteriormente).
2. Compruebe que pueda editar datos mientras está abierta esa cuenta.
 	**Nota:** no puede cerrar sesión en una cuenta de Google desde esta aplicación y, a continuación, iniciar sesión en otra cuenta de Google diferente con el mismo explorador. Si utiliza un solo explorador, debe ir al sitio de Google y cerrar sesión desde allí. Puede iniciar sesión con otra cuenta del mismo autenticador externo (como Google) si utiliza un explorador diferente.

Si no ha proporcionado aún el nombre y apellido en la información de su cuenta de Google, se producirá una excepción NullReferenceException.


## Examen de la base de datos SQL de Azure ##

1. En el **Explorador de servidores**, vaya a **ContactDB**
2. Haga clic con el botón secundario en **ContactDB** y seleccione **Open in SQL Server Object Explorer** (Abrir en el Explorador de objetos de SQL Server).
 
	![open in SSOX](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rrr12.png)
 
**Nota:** si puede expandir las **bases de datos SQL** y *no* puede ver **ContactDB** en Visual Studio, deberá seguir las instrucciones siguientes para abrir un puerto o una serie de puertos de firewall. Siga las instrucciones de **Set up Azure firewall rules**. Es posible que tenga que esperar unos minutos para obtener acceso a la base de datos una vez agregada la regla de firewall.
 
1. Haga clic con el botón secundario en la tabla **AspNetUsers** y seleccione **Ver datos**.

	![CM page](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rrr8.png)
 
1. Tenga en cuenta que el identificador de la cuenta de Google con la que se registró estará incluido en el rol **canEdit**, al igual que el identificador de *user1@contoso.com*. Estos deberían ser los únicos usuarios del rol **canEdit**. (Esto se comprobará en el paso siguiente).

	![CM page](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rrr9.png)
 
2. En el **Explorador de objetos de SQL Server**, haga clic con el botón secundario en **AspNetUserRoles** y seleccione **Ver datos**.

	![CM page](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rs1.png)
 
Compruebe que los valores **UserId** proceden de *user1@contoso.com* y de la cuenta de Google que registró. 


## Configuración de las reglas de firewall de Azure ##

Siga los pasos de esta sección si no puede conectar con SQL Azure desde Visual Studio o si aparece un diálogo de error del tipo "No se puede abrir el servidor".

![firewall error](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rx5.png)

Deberá agregar su dirección IP a la lista de direcciones IP permitidas.

1. En el Portal de Azure, seleccione **Bases de datos SQL** en la pestaña izquierda.

	![Select SQL][rx6]

1. Haga clic en **ContactDB**.

1. Haga clic en el vínculo **Set up Azure firewall rules for this IP address**.

	![firewall rules][rx7]

1. Cuando aparezca el mensaje "The current IP address xxx.xxx.xxx.xxx is not included in existing firewall rules. Do you want to update the firewall rules?", haga clic en **Yes**. A menudo, con algunos firewalls corporativos, no basta con agregar esta dirección, sino que hay que agregar un rango de direcciones IP.

El paso siguiente consiste en agregar un rango de direcciones IP permitidas.

1. En el Portal de Azure, haga clic en **Bases de datos SQL**.
1. Seleccione la pestaña **Servidores** y luego haga clic en el servidor que desea configurar.

	![Servers tab in Azure ](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/ss25.PNG)

1. Haga clic en la pestaña **Configurar**.

1. Agregue un nombre de regla y las direcciones IP de inicio y fin.

	![ip range][rx9]

1. En la parte inferior de la página, haga clic en **Guardar**.
1. Es importante que haga comentarios y nos informe si necesita agregar un rango de direcciones IP para conectarse.

Finalmente, puede conectarse a la instancia de Base de datos SQL desde SQL Server Object Explorer (SSOX).

1. En el menú Ver, haga clic en **Explorador de objetos de SQL Server**.
1. Haga clic con el botón secundario en **SQL Server** y seleccione **Agregar SQL Server**.
1. En el cuadro de diálogo **Conectar al servidor**, establezca la opción **Autenticación** en **Autenticación SQL Server**. Obtendrá el **nombre del servidor** y los **datos de inicio de sesión** del Portal de Azure.
1. En el explorador, desplácese hasta el portal y seleccione **Bases de datos SQL**.
1. Seleccione **ContactDB** y luego haga clic en **Consultar las cadenas de conexión de las bases de datos SQL**.
1. En la página **Cadenas de conexión**, copie los datos de **Servidor** e **Id. de usuario**.
 
	![con string](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/ss21.PNG)
1. Pegue los valores de **Servidor** e **Id. de usuario** en el cuadro de diálogo **Conectar con el servidor** de Visual Studio. El **identificador de usuario** debe escribirlo en el campo **Login**. Escriba la contraseña que utilizó para crear la base de datos SQL.

	![Connect to Server DLG](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rss1.png)

Ahora puede desplazarse hasta la base de datos ContactDB siguiendo las instrucciones facilitadas anteriormente.


## Incorporación de un usuario al rol canEdit mediante la edición de las tablas de la base de datos

Anteriormente en este tutorial, utilizamos código para agregar usuarios al rol canEdit. Existe un método alternativo que consiste en manipular directamente los datos de las tablas de suscripciones. Los pasos siguientes muestran cómo utilizar este método alternativo para agregar un usuario a un rol.

2. En el **Explorador de objetos de SQL Server**, haga clic con el botón secundario en **AspNetUserRoles** y seleccione **Ver datos**.

	![CM page](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rs1.png)

1. Copie el valor de *RoleId* y péguelo en la fila vacía (nueva).
	
	![CM page](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rs2.png)
	
2. En la tabla **AspNetUsers**, busque el usuario al que desee asignar el rol, copie su *Id* y péguelo en la columna **UserId** de la tabla **AspNetUserRoles**.

Estamos trabajando en una herramienta que facilitará mucho la tarea de administrar los usuarios y roles.


<h2><a name="nextsteps"></a>Pasos siguientes</h2>

Siga los tutoriales en los que hemos basado este ejemplo:

1.	[Create a secure ASP.NET MVC 5 web app with log in, email confirmation and password reset](http://www.asp.net/mvc/overview/getting-started/create-an-aspnet-mvc-5-web-app-with-email-confirmation-and-password-reset)
2.	[ASP.NET MVC 5 app with SMS and email Two-Factor Authentication](http://www.asp.net/mvc/overview/getting-started/aspnet-mvc-5-app-with-sms-and-email-two-factor-authentication)
3.	[Best practices for deploying passwords and other sensitive data to ASP.NET and Azure](http://www.asp.net/identity/overview/features-api/best-practices-for-deploying-passwords-and-other-sensitive-data-to-aspnet-and-azure) 
4.	[Create an ASP.NET MVC 5 App with Facebook and Google OAuth2](http://www.asp.net/mvc/tutorials/mvc-5/create-an-aspnet-mvc-5-app-with-facebook-and-google-oauth2-and-openid-sign-on ) Este tutorial contiene instrucciones acerca de cómo agregar datos de perfiles a la base de datos de registro de usuarios e información detallada acerca del uso de Facebook como proveedor de autenticación.
5.	[Getting Started with ASP.NET MVC 5](http://www.asp.net/mvc/tutorials/mvc-5/introduction/getting-started)

Para habilitar los botones de inicio de sesión de redes sociales que aparecen en la parte superior de este tutorial, consulte [Pretty social login buttons for ASP.NET MVC 5](http://www.beabigrockstar.com/pretty-social-login-buttons-for-asp-net-mvc-5/).

El excelente tutorial de Tom Dykstra [Getting Started with EF and MVC](http://www.asp.net/mvc/tutorials/getting-started-with-ef-using-mvc/creating-an-entity-framework-data-model-for-an-asp-net-mvc-application) le proporcionará información más avanzada acerca de la programación con MVC y EF.

Este tutorial y la aplicación de ejemplo fueron desarrollados por [Rick Anderson](http://blogs.msdn.com/b/rickandy/) (Twitter [@RickAndMSFT](https://twitter.com/RickAndMSFT)) con la colaboración de Tom Dykstra y Barry Dorrans (Twitter [@blowdart](https://twitter.com/blowdart)). 

***Es importante que haga comentarios*** acerca de lo que le gustó o lo que le gustaría que mejorásemos, no solo en relación al tutorial en sí sino a los productos sobre los que trata. Sus comentarios nos ayudarán a clasificar las mejoras por orden de prioridad. También puede solicitar y votar nuevos temas en [Show Me How With Code](http://aspnet.uservoice.com/forums/228522-show-me-how-with-code).

<!-- bookmarks -->
[Incorporación de un proveedor de OAuth]: #addOauth
[Uso de la API de suscripción]:#mbrDB
[Creación de un script de implementación de datos]:#ppd
[Actualización de la base de datos de suscripciones]:#ppd2

[setupwindowsazureenv]: #bkmk_setupwindowsazure
[createapplication]: #bkmk_createmvc4app
[deployapp1]: #bkmk_deploytowindowsazure1
[deployapp11]: #bkmk_deploytowindowsazure11
[adddb]: #bkmk_addadatabase


<!-- images-->
[rx2]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rx2.png

[rx5]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rx5.png
[rx6]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rx6.png
[rx7]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rx7.png
[rx8]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rx8.png
[rx9]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rx9.png

[rxb]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rxb.png


[rxSSL]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rxSSL.png

[rxNOT]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rxNOT.png
[rxNOT2]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rxNOT2.png

[rxNOT]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rxNOT.png
[rxNOT]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rxNOT.png
[rxNOT]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rxNOT.png
[rr1]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rr1.png

[rxPrevDB]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rxPrevDB.png

[rxWSnew]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rxWSnew2.png
[rxCreateWSwithDB]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rxCreateWSwithDB.png

[setup007]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/dntutmobile-setup-azure-site-004.png

[newapp004]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/dntutmobile-createapp-004.png

[firsdeploy003]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/dntutmobile-deploy1-publish-001.png

[adddb002]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/dntutmobile-adddatabase-002.png
[addcode001]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/dntutmobile-controller-add-context-menu.png

[addcode008]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/dntutmobile-migrations-package-manager-menu.png
[addcode009]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/dntutmobile-migrations-package-manager-console.png


[Información importante acerca de ASP.NET en Sitios web Azure]: #aspnetwindowsazureinfo
[Pasos siguientes]: #nextsteps

[ImportPublishSettings]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/ImportPublishSettings.png
























