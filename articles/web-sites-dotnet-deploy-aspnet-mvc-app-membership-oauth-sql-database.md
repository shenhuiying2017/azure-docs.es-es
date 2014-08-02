<properties  linkid="dev-net-tutorials-web-app-with-sql-azure-vs2013" urlDisplayName="Web Site with SQL Database" pageTitle="Deploy a Secure ASP.NET MVC app with Membership, OAuth, and SQL Database to an Azure Web Site" metaKeywords="Azure hello world tutorial, Azure getting started tutorial, SQL Database tutorial, Azure .NET hello world tutorial, Azure C# hello world tutorial, SQL Azure C# tutorial" description="Learn how to develop an ASP.NET MVC 5 web site with a SQL Database back-end deploy it to Azure." metaCanonical="" services="web-sites,sql-database" documentationCenter=".NET" title=" OAuth" authors="" solutions="" writer="riande" manager="wpickett" editor="mollybos" />

# Implementación de una aplicación ASP.NET MVC 5 segura con suscripción, OAuth y Base de datos SQL en un Sitio web Azure

***Por [Rick Anderson][1] y Tom Dykstra. Actualización de 2 de abril de 2014.***

Este tutorial muestra cómo desarrollar una aplicación web ASP.NET MVC 5 segura que permita a los usuarios iniciar sesión con las credenciales de Facebook o Google. Además, la aplicación se implementará en Azure.

Puede abrir una cuenta de Azure de manera gratuita y, si todavía no tiene Visual Studio 2013, el SDK instala automáticamente Visual Studio 2013 Express para Web. Puede empezar a desarrollar contenido para Azure sin coste alguno. Si quiere utilizar Visual Studio 2012, consulte el [tutorial anterior](/en-us/develop/net/tutorials/web-site-with-sql-database-vs2012/). Esta versión del tutorial es mucho más sencilla que la anterior.

En este tutorial se asume que no tiene ninguna experiencia previa con Azure. Cuando acabe, tendrá una aplicación web controlada por datos segura que se ejecutará en la nube y utilizará una base de datos en la nube.

Aprenderá a realizar los siguientes procedimientos:

* Crear un proyecto ASP.NET MVC 5 seguro y publicarlo en un Sitio web Azure
* Utilizar [OAuth][2], [OpenID][3] y la base de datos de suscripciones de ASP.NET para proteger la aplicación
* Utilizar la nueva API de suscripción para agregar usuarios y roles
* Utilizar una Base de datos SQL para almacenar datos en Azure

Va a desarrollar una aplicación web de lista de contactos sencilla basada en ASP.NET MVC 5 y que utiliza ADO.NET Entity Framework para obtener acceso a la base de datos. La ilustración siguiente muestra la página de inicio de sesión de la aplicación final:

![página de inicio de sesión](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rxb.png)

> [WACOM.NOTE] Necesita una cuenta de Microsoft Azure para completar
> este tutorial. Si aún no la tiene, puede [activar los beneficios de
> suscripción a
> MSDN](/en-us/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F), o bien [regístrese para obtener
> una evaluación
> gratuita](/en-us/pricing/free-trial/?WT.mc_id=A261C142F).

Apartados de este tutorial:

* [Configuración del entorno de desarrollo](#setupdevenv)
* [Configuración del entorno de Azure](#bkmk_setupwindowsazure)
* [Creación de una aplicación ASP.NET MVC 5](#bkmk_createmvc4app)
* [Implementación de la aplicación en
  Azure](#bkmk_deploytowindowsazure1)
* [Incorporación de una base de datos a la
  aplicación](#bkmk_addadatabase)
* [Incorporación de un proveedor de OAuth](#addOauth)
* [Uso de la API de suscripción](#mbrDB)
* [Implementación de la aplicación en
  Azure](#bkmk_deploytowindowsazure11)
* [Pasos siguientes](#nextsteps)

[WACOM.INCLUDE [install-sdk-2013-only](../includes/install-sdk-2013-only.md)]

Para utilizar el nuevo certificado SSL para localhost, tendrá que instalar [Visual Studio 2013 Update 2 RC][4] o una versión superior.

<h2><a name="bkmk_setupwindowsazure"></a>Configuración del entorno de Azure</h2>


A continuación, configure el entorno de Azure creando un Sitio web Azure y una Base de datos SQL.

### Creación de un Sitio web y una Base de datos SQL en Azure

El Sitio web Azure se ejecutará en un entorno de hospedaje compartido, es decir, en máquinas virtuales compartidas con otros clientes de Azure. Los entornos de hospedaje compartidos ofrecen un coste reducido a los nuevos usuarios de servicios en la nube. Más adelante, si el tráfico web aumenta, es posible escalar la aplicación para atender la demanda ejecutándola en máquinas virtuales dedicadas. Si necesita una arquitectura más compleja, puede migrar la aplicación a un Servicio en la nube de Azure. Los servicios en la nube se ejecutan en MV dedicadas que se pueden configurar según sus necesidades.

La Base de datos SQL de Azure es un servicio de base de datos relacional en la nube que se basa en la tecnología de SQL Server. Las herramientas y aplicaciones que funcionan con SQL Server también funcionan con la Base de datos SQL.

1.  En el [Portal de administración de Azure][5], haga clic en **Sitios web** en la pestaña izquierda y, a continuación, en **New**.
    
    ![Botón New del portal de
    administración](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rxWSnew2.png)

2.  Haga clic en **Sitio web** y, a continuación, en **Custom Create**.
    
    ![Enlace Create with Database del portal de
    administración](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rxCreateWSwithDB.png)
    
    Se abre el asistente para **creación personalizada de un nuevo sitio web**.

3.  En el paso **Create Web Site** del asistente, escriba en el cuadro **URL** el valor que se va a utilizar como URL única de la aplicación. La URL completa consistirá en el valor que escriba más el sufijo que aparece junto al cuadro de texto. La ilustración muestra una URL que probablemente ya esté en uso, por lo que tendrá que elegir otra distinta.
    
    ![Enlace Create with Database del portal de
    administración](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rr1.png)

4.  En la lista desplegable **Database**, elija **Create a free SQL database**.

5.  En la lista desplegable **Region**, elija la misma región que seleccionó para el Sitio web. Este valor especifica el centro de datos en el que se ejecutará la máquina virtual.
6.  En el cuadro **DB Connection String Name**, no modifique el valor predeterminado *DefaultConnection*.
7.  Haga clic en la flecha derecha situada en la parte inferior del cuadro. El asistente avanza hasta el paso **Specify database settings**.

8.  En el cuadro **Name**, escriba *ContactDB* (consulte la imagen que aparece a continuación).
9.  En el cuadro **Server**, seleccione **New SQL Database server** (consulte la imagen que aparece a continuación). Si anteriormente creó una base de datos de SQL Server, también tiene la opción de seleccionar ese SQL Server en el control desplegable.
10. Establezca la opción **Región** en la misma zona en la que creó el sitio web.
11. Complete los campos **Login name** y **Password** con los datos de un administrador. Si seleccionó **New SQL Database server**, no debe escribir un nombre y una contraseña existentes en estos campos, sino definir unos nuevos que volverá a utilizar más adelante para obtener acceso a la base de datos. Si seleccionó un SQL Server creado anteriormente, se le pedirá la contraseña de la cuenta de SQL Server que abrió para crearlo. En este tutorial, no seleccionaremos la casilla **Advanced**. En el caso de las bases de datos libres, solo puede establecer la intercalación.
12. Haga clic en la marca de verificación que aparece en la esquina inferior derecha del cuadro para indicar que finalizó la configuración.
    
    ![Paso Configuración de base de datos del asistente para creación de un nuevo sitio web con base de
    datos](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/dntutmobile-setup-azure-site-004.png)
    
    La imagen siguiente muestra cómo usar un SQL Server y datos de inicio de sesión existentes.
    
    ![Paso Configuración de base de datos del asistente para creación de un nuevo sitio web con base de
    datos](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rxPrevDB.png)
    
    El Portal de administración vuelve a la página de Sitios web y la columna **Status** indica que se está creando el sitio. Después de unos segundos, la columna **Status** indica que el sitio se creó correctamente. En la barra de navegación situada a la izquierda, el número de sitios que tiene en su cuenta aparece junto al icono **Sitios web** y el número de bases de datos, junto al icono **Bases de datos SQL**.

<h2><a name="bkmk_createmvc4app"></a>Creación de una aplicación ASP.NET MVC 5</h2>


Ya creó un Sitio web Azure, pero todavía no hay contenido en él. El siguiente paso consiste en crear la aplicación web de Visual Studio que luego publicará en Azure.

### Creación del proyecto

1.  En el menú **Archivo**, haga clic en **Nuevo proyecto**.
    
    ![Nuevo proyecto en el menú Archivo](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/gs13newproj.png)

2.  En el cuadro de diálogo **Nuevo proyecto**, expanda **C#** y seleccione **Web** debajo de **Plantillas instaladas**; a continuación, seleccione <strong>Aplicación web ASP.NET </strong>.

3.  Póngale a la aplicación el nombre **ContactManager** y haga clic en **Aceptar**.
    
    ![Cuadro de diálogo Nuevo proyecto](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/GS13newprojdb.png)
    
    **Nota:** en la imagen aparece "MyExample" como nombre de la aplicación, pero debe escribir "ContactManager". En los bloques de código que copiará más tarde se asume que el nombre del proyecto es ContactManager.

4.  En el cuadro de diálogo **New ASP.NET Project**, seleccione la plantilla **MVC**, **desactive** la casilla **Create remote resources** y, a continuación, haga clic en **OK**. (Es posible que esta casilla lleve el nombre **Host in the cloud** en lugar de **Create remote resources**).
    
    ![Cuadro de diálogo New ASP.NET Project](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/ss1.PNG)

### Establecimiento del encabezado y pie de página

1.  En el **Explorador de soluciones**, abra el archivo *Layout.cshtml* de la carpeta *Views\\Shared*.
    
    ![_Layout.cshtml en el Explorador de soluciones](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/dntutmobile-createapp-004.png)

2.  Reemplace las dos apariciones de "My ASP.NET MVC Application" por "ContactManager".
3.  Reemplace "Application name" por "CM Demo".

4.  Actualice el primer enlace de acción y reemplace *Home* por *Cm* para usar el controlador *Cm*.
    
    ![cambios de código](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rs3.png)

### Ejecución de la aplicación de forma local

1.  Pulse CTRL+F5 para ejecutar la aplicación.
    
    Aparece la página principal de la aplicación en el explorador predeterminado.
    
    ![Sitio web ejecutándose de forma local](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rr2.png)

Esto es todo lo que necesita hacer por ahora para crear la aplicación que va a implementar en Azure. Más adelante agregará la funcionalidad de base de datos.

<h2><a name="bkmk_deploytowindowsazure1"></a>Implementación de la aplicación en Azure</h2>


1.  En Visual Studio, haga clic con el botón derecho en el proyecto, en el **Explorador de soluciones**, y seleccione **Publicar** en el menú contextual.
    
    ![Opción Publicar del menú contextual del proyecto](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/GS13publish.png)
    
    Se abre el asistente para **publicación web**.

2.  En la pestaña **Perfil** del asistente para **publicación web**, haga clic en **Sitios web Azure**.
    
    ![Importar configuración de publicación](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/ss2.PNG)

3.  Haga clic en el botón **Sign In** e inicie sesión en el portal de Azure.

	![iniciar sesión](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/ss22.PNG)

	Tras iniciar sesión, aparece el cuadro de diálogo **Select Existing Web Site**.

1.  Seleccione el sitio web que creó en la primera parte de este tutorial y, a continuación, haga clic en **OK**.

	![seleccionar sitio web](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/ss3.png)

1.  En el cuadro de diálogo **Publish Web**, haga clic en **Publicar**.
    
    ![Publicar](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rr3.png)
    
    La aplicación creada ahora se ejecuta en la nube. La próxima vez que implemente la aplicación, solo se implementarán los archivos modificados o nuevos.
    
    ![Ejecutar en la
    nube](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/ss4.PNG)

<h2><a name="bkmk_addadatabase"></a>Incorporación de una base de datos a la aplicación</h2>


A continuación, actualizará la aplicación MVC incorporando funciones que permitan mostrar y actualizar los contactos y almacenar los datos en una base de datos. La aplicación utilizará Entity Framework para crear la base de datos y para leer y actualizar los datos que esta contiene.

### Incorporación de clases de modelos de datos para los contactos

Se empieza por crear un modelo de datos sencillo en código.

1.  En el **Explorador de soluciones**, haga clic con el botón derecho en la carpeta Modelos, a continuación en **Add** y, por último, en **Class**.
    
    ![Agregar clase en el menú contextual de la carpeta
    Models](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rr5.png)

2.  En el cuadro de diálogo **Add New Item**, ponga al archivo de la nueva clase el nombre *Contact.cs* y, a continuación, haga clic en **Add**.
    
    ![Cuadro de diálogo Add New
    Item](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/dntutmobile-adddatabase-002.png)

3.  Reemplace el contenido del archivo Contacts.cs por el código siguiente.
    
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
    
    La clase **Contacts** define qué datos de los contactos va a almacenar, además de una clave primaria, *ContactID*, necesaria para la base de datos.

### Creación de páginas web que permiten a los usuarios de aplicaciones utilizar los contactos

La característica de scaffolding de ASP.NET MVC puede generar automáticamente código que realiza acciones de creación, lectura, actualización y eliminación (CRUD).

<h2><a name="bkmk_addcontroller"></a>Incorporación de un controlador y una vista para los datos</h2>


1.  Cree el proyecto **(Ctrl+Mayús+B)**. (Debe crear el proyecto antes de usar el mecanismo de scaffolding).
2.  En el **Explorador de soluciones**, haga clic con el botón derecho en la carpeta Controladores, a continuación en **Add** y, por último, en **Controller**.
    
    ![Agregar controlador en el menú contextual de la carpeta Controllers](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/dntutmobile-controller-add-context-menu.png)

3.  En el cuadro de diálogo **Add Scaffold**, seleccione **MVC 5 Controller with views, using EF** y, a continuación, haga clic en **Add**.
    
    ![Cuadro de diálogo Add Scaffold](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rr6.png)

4.  En el cuadro desplegable **Model class**, seleccione **Contact (ContactManager.Models)**. (Consulte la imagen que aparece a continuación).
5.  En **Data context class**, seleccione **ApplicationDbContext (ContactManager.Models)**. El valor **ApplicationDbContext** se utilizará tanto para la base de datos de suscripciones como para los
    datos de nuestros contactos.
6.  En el cuadro de la entrada de texto **Controller name**, escriba "CmController" como nombre del controlador.
    
    ![Cuadro de diálogo New Data
    Context](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/ss5.PNG)

7.  Haga clic en **Add**.
    
    Visual Studio crea métodos y vistas de controlador para las operaciones CRUD de base de datos que afectan a los objetos **Contact**.

## Habilitación de las migraciones, creación de la base de datos e incorporación de datos de ejemplo y un inicializador de datos

La siguiente tarea consiste en habilitar la función [Migraciones de Code First][6] para crear la base de datos a partir del modelo de datos ya establecido.

1.  En el menú **Tools**, seleccione **NuGet Package Manager** y, a continuación **Package Manager Console**. ![Package Manager Console en el menú Herramientas](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/SS6.png)

2.  En la ventana **Package Manager Console**, escriba el comando siguiente:
    
         enable-migrations
    
    El comando **enable-migrations** crea una carpeta *Migrations* y guarda en ella un archivo *Configuration.cs* que puede editar para inicializar la base de datos y configurar las migraciones.

3.  En la ventana **Package Manager Console**, escriba el comando siguiente:
    
         add-migration Initial
    
    El comando **add-migration Initial** genera un archivo denominado **<date\_stamp>Initial** en la carpeta *Migrations* que crea la base de datos. El primer parámetro (**Initial**) es arbitrario y se utiliza para crear el nombre del archivo. Puede ver los archivos de las nuevas clases en el **Explorador de soluciones**. En la clase **Initial**, el método **Up** crea la tabla Contacts y el método **Down** (que se utiliza cuando se desea volver al estado anterior) la anula.

4.  Abra el archivo *Migrations\\Configuration.cs*. 5.  Agregue el siguiente espacio de nombres.
    
    using ContactManager.Models;
6.  Reemplace *Seed* por el código siguiente:
    
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
    
    Este código inicializa la base de datos con la información de contacto. Para obtener más información acerca de la inicialización de la base de datos, consulte [Seeding and Debugging Entity Framework (EF) DBs][7].

7.  En **Package Manager Console**, escriba el comando:
    
         update-database
    
    ![Comandos de Package Manager
    Console](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/dntutmobile-migrations-package-manager-console.png)
    
    El comando **update-database** ejecuta la primera migración, lo que crea la base de datos. De manera predeterminada, la base de datos que se crea es una base de datos LocalDB de SQL Server Express.

8.  Pulse CTRL+F5 para ejecutar la aplicación y, a continuación, haga clic en el enlace **CM Demo**; o bien visite
    http://localhost:(port#)/Cm.
    
    La aplicación muestra los datos de inicialización y ofrece enlaces de edición, detalles y eliminación. Puede crear, editar, eliminar y ver los datos.
    
    ![Vista MVC de los
    datos](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rx2.png)

<h2><a name="addOauth"></a><span  class="short-header">OAuth</span>Incorporación de un proveedor de OAuth2 y OpenID</h2>


[OAuth][2] es un protocolo abierto que ofrece autorización segura a través de un método estándar sencillo para las aplicaciones web, móviles y de escritorio. La plantilla de Internet ASP.NET MVC utiliza OAuth y [OpenID][3] para ofrecer Facebook, Twitter, Google y Microsoft como proveedores de autenticación. Aunque este tutorial solo utiliza Google como proveedor de autenticación, puede modificar fácilmente el código para utilizar cualquiera de los proveedores. Los pasos que hay que seguir para implementar otros proveedores son muy similares a los que verá en este tutorial. Para utilizar Facebook como proveedor de autenticación, consulte el tutorial [Create an ASP.NET MVC 5 App with Facebook and Google OAuth2 and OpenID Sign-on][8].

Además de la autenticación, este tutorial también utiliza roles para implementar la autorización. Únicamente los usuarios que agregue al rol *canEdit* podrán cambiar los datos (es decir, crear, editar o eliminar
contactos).

1.  Abra el archivo *App\_Start\\Startup.Auth.cs*. Quite los caracteres de comentario del método *app.UseGoogleAuthentication()*.

2.  Ejecute la aplicación y haga clic en el enlace **Log In**. 3.  Debajo de **Use another service to log in**, haga clic en el botón  **Google**.
    
    ![Inicio sesión
    Google](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/ss23.PNG)

4.  Escriba sus credenciales.

5.  El servidor de autenticación de Google le pedirá permiso para que la aplicación pueda ver su dirección de correo electrónico y la información básica sobre su cuenta. Haga clic en **Accept**.

	![GOOGLE pidiendo permiso](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/ss7.PNG)
6.  Se le redirige a la página de registro. El nombre de usuario es de manera predeterminada el alias de correo electrónico que utilizó para registrarse, pero puede cambiarlo si así lo desea. Haga clic en **Register**.
    
    ![registro](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/ss8.PNG)

<h2><a name="mbrDB"></a><span  class="short-header">Base de datos de suscripciones</span>Uso de la API de suscripción</h2>


En esta sección, agregará un usuario local y el rol *canEdit* a la base de datos de suscripciones. Únicamente los usuarios incluidos en el rol *canEdit* podrán editar los datos. Es recomendable nombrar los roles en
función de las acciones que pueden realizar, por lo que el nombre *canEdit* es más aconsejable que *admin*. A medida que la aplicación evoluciona, puede agregar nuevos roles del tipo *canDeleteMembers*, en lugar del escasamente descriptivo *superAdmin*.

1.  Abra el archivo *migrations\\configuration.cs* y agregue las siguientes instrucciones `using`:
    
         using Microsoft.AspNet.Identity;
         using Microsoft.AspNet.Identity.EntityFramework;

2.  Agregue el siguiente método **AddUserAndRole** a la clase:
    
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

3.  Llame al nuevo método desde el método **Seed**:
    
         protected override void Seed(ContactManager.Models.ApplicationDbContext context)
         {
             AddUserAndRole(context);
             context.Contacts.AddOrUpdate(p => p.Name,
                 // Código quitado para reducir el espacio
         }
    
    La siguiente ilustración muestra los cambios en el método *Seed*:
    
    ![imagen de
    código](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/ss24.PNG)
    
    Este código crea un nuevo rol denominado *canEdit*, crea el nuevo usuario local *user1@contoso.com* y agrega dicho usuario *user1@contoso.com* al rol *canEdit*. Para obtener más información, consulte [ASP.NET Identity resource page][9].

## Uso de código temporal para agregar a los nuevos usuarios que inician sesión a través de redes sociales al rol canEdit

En esta sección, modificará temporalmente el método **ExternalLoginConfirmation** del controlador de cuentas para agregar a los nuevos usuarios que se registran con un proveedor de OAuth u OpenID al rol *canEdit*. Modificaremos temporalmente el método **ExternalLoginConfirmation** para agregar automáticamente a los nuevos usuarios a un rol administrativo. Hasta que ofrezcamos una herramienta para agregar y administrar roles, utilizaremos el código de registro automático temporal que aparece a continuación. En el futuro, esperamos ofrecer una herramienta similar a [WSAT][10] que le permita crear y editar cuentas y roles de usuario. Más adelante en este tutorial le mostraremos cómo utilizar el **Explorador de servidores** para agregar usuarios a los roles.

1.  Abra el archivo **Controllers\\AccountController.cs** y desplácese hasta el método **ExternalLoginConfirmation**.
2.  Agregue la siguiente llamada a **AddToRoleAsync** justo antes de la llamada a **SignInAsync**.
    
                 await UserManager.AddToRoleAsync(user.Id, "canEdit");
    
    El código anterior agrega el usuario recién registrado al rol "canEdit", lo que le permite obtener acceso a los métodos de acción que cambian (editan) los datos. A continuación, se muestra una imagen del cambio de código:
    
    ![código](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/ss9.PNG)

Más adelante en este tutorial implementará la aplicación en Azure, donde iniciará sesión con Google u otro proveedor de autenticación de terceros. De este modo, la cuenta recién registrada se agregará al rol *canEdit*. Cualquiera que encuentre la URL de su sitio y tenga un identificador de Google podrá registrarse y actualizar su base de datos. Para evitar que otros usuarios hagan esto, puede detener el sitio. Podrá comprobar quién está incluido en el rol *canEdit* examinando la base de datos.

En la **Package Manager Console** toque la tecla de flecha arriba para que aparezca el siguiente comando:

    	Update-Database

Ejecute el comando **Update-Database**, que a su vez ejecutará el método **Seed**, lo que ejecutará el método **AddUserAndRole** que acaba de agregar. El método **AddUserAndRole** creará el usuario *user1@contoso.com* y lo agregará al rol *canEdit*.

## Protección de la aplicación con SSL y el atributo Authorize

En esta sección, aplicará el atributo [Authorize][11] para restringir el acceso a los métodos de acción. Los usuarios anónimos solo podrán ver el método de acción **Index** del controlador principal. Los usuarios registrados podrán ver los datos de contacto (páginas **Index** y **Details** del controlador Cm) y las páginas About y Contact. Únicamente los usuarios incluidos en el rol *canEdit* podrán obtener acceso a los métodos de acción que cambian datos.

1.  Agregue el filtro [Authorize][11] y el filtro [RequireHttps][12] a la aplicación. Otra opción es agregar el atributo [Authorize][11] y el atributo [RequireHttps][12] a cada controlador, pero por motivos de seguridad es recomendable aplicarlos a toda la aplicación. De esta manera, todos los controladores y métodos de acción nuevos que agregue estarán automáticamente protegidos, sin necesidad de aplicar los atributos otra vez. Para obtener más información, consulte [Securing your ASP.NET MVC App and the new AllowAnonymous Attribute][13]. Abra el archivo *App\_Start\\FilterConfig.cs* y reemplace el método *RegisterGlobalFilters* por el siguiente código (que agrega los dos filtros):
    
         public static void
         RegisterGlobalFilters(GlobalFilterCollection filters)
         {
             filters.Add(new HandleErrorAttribute());
             filters.Add(new System.Web.Mvc.AuthorizeAttribute());
             filters.Add(new RequireHttpsAttribute());
         }
    
    La siguiente ilustración muestra el cambio de código:
    
    ![imagen de
    código](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/ss10.PNG)
    
    El filtro [Authorize][11] aplicado en el código anterior evitará que los usuarios anónimos obtengan acceso a los métodos de la aplicación. Utilizará el atributo [AllowAnonymous][13] para cancelar el requisito de autorización en un par de métodos, de tal forma que los usuarios anónimos puedan iniciar sesión y ver la página principal. El atributo [RequireHttps][12] requerirá que todo acceso a la aplicación web se realice a través de HTTPS.

2.  Agregue el atributo [AllowAnonymous][13] al método **Index** del controlador principal. El atributo [AllowAnonymous][13] le permite incluir en una lista blanca los métodos que desee excluir de la autorización. A continuación, se muestra una imagen de un fragmento del controlador principal:

	![código](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/ss11.PNG)

1.  Realice una búsqueda global de *AllowAnonymous*; como puede ver, se utiliza en los métodos de inicio de sesión y registro del controlador de cuentas.
2.  En *CmController.cs*, agregue `[Authorize(Roles = "canEdit")]` a los métodos HttpGet y HttpPost que cambian los datos (todos los métodos de acción [Create, Edit, Delete] excepto Index y Details) del controlador *Cm*. A continuación, se muestra un fragmento del código final:
    
    ![imagen del
    código](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rr11.png)

## Habilitación de SSL para el proyecto

1.  Habilite SSL. En el Explorador de soluciones, haga clic en el proyecto **ContactManager** y, a continuación, en F4 para mostrar el diálogo de propiedades. Cambie **SSL Enabled** a true. Copie la **URL de SSL**. La URL de SSL será https://localhost:44300/, a menos que anteriormente creara sitios web SSL.
    
    ![habilitar SSL](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rxSSL.png)

2.  En el Explorador de soluciones, haga clic con el botón derecho en el proyecto **ContactManager** y, a continuación, en **Propiedades**.
3.  En la pestaña izquierda, haga clic en **Web**. 4.  Cambie la **URL del proyecto** por la **URL de SSL** y guarde la página (Control+S).
    
    ![habilitar
    SSL](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rrr1.png)

5.  Compruebe que es Internet Explorer el explorador que inicia Visual Studio, como se muestra en la imagen siguiente:
    
    ![explorador
    predeterminado](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/ss12.PNG)
    
    El selector de explorador le permite especificar el explorador que inicia Visual Studio.

	![selector de explorador](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/ss13.png)

    Puede seleccionar varios exploradores y hacer que Visual Studio actualice cada explorador cuando haga cambios. Para obtener más información, consulte [Using Browser Link in Visual Studio 2013](http://www.asp.net/visual-studio/overview/2013/using-browser-link).

1.  Pulse CTRL+F5 para ejecutar la aplicación. Siga las instrucciones para confiar en el certificado autofirmado que ha generado IIS Express.
    
    ![instrucciones para confiar en el certificado autofirmado que ha
    generado IIS
    Express.](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/ss26.PNG)

2.  Lea el cuadro de diálogo **Advertencia de seguridad** y haga clic en **Sí** si desea instalar el certificado que representa **localhost**.

	![advertencia del certificado localhost de IIS Express](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/ss27.PNG)

1.  IE muestra la página *principal* y no hay advertencias de SSL.
    
    ![IE con SSL de localhost y sin
    advertencias](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/ss28.PNG)
    
    Google Chrome también acepta el certificado y mostrará el contenido  HTTPS sin advertencia. Firefox utiliza su propio almacén de certificados, así que mostrará una advertencia. En el caso de nuestra aplicación, puede hacer clic en **Entiendo los riesgos** sin temor.
    
    ![Advertencia de certificado de
    FireFox](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/ss30.PNG)

2.  Si todavía tiene abierta una sesión anterior, haga clic en el enlace **Log out**.
3.  Haga clic en los enlaces **About** o **Contact**. Se le redirigirá a la página de inicio de sesión porque los usuarios anónimos no pueden ver estas páginas.
4.  Haga clic en el enlace **Register as a new user** y agregue un usuario local llamado *joe@contoso.com*. Compruebe que *Joe* pueda ver las páginas principal, About y Contact.
    
    ![inicio de
    sesión](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/ss14.PNG)

5.  Haga clic en el enlace *CM Demo* y compruebe que vea los datos. 6.  Haga clic en un enlace de edición de la página y se le redirigirá a la página de inicio de sesión (el nuevo usuario local no está incluido en el rol *canEdit*).
7.  Inicie sesión con el nombre de usuario *user1@contoso.com* y la contraseña "P\_assw0rd1" (el "0" de "word" es un cero). Se le redirigirá a la página de edición que seleccionó anteriormente.
    
    Si no puede iniciar sesión con esa cuenta y contraseña, pruebe a copiar la contraseña del código fuente y luego pegarla. Si sigue sin poder iniciar sesión, compruebe la columna **UserName** de la tabla **AspNetUsers** para comprobar que se agregó el usuario *user1@contoso.com*.

8.  Compruebe que pueda modificar los datos.

<h2><a name="bkmk_deploytowindowsazure11"></a>Implementación de la aplicación en Azure</h2>


1.  En Visual Studio, haga clic con el botón derecho en el proyecto, en el **Explorador de soluciones**, y seleccione **Publicar** en el menú contextual.
    
    ![Opción Publicar del menú contextual del
    proyecto](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/dntutmobile-deploy1-publish-001.png)
    
    Se abre el asistente para **publicación web**.

2.  Haga clic en la pestaña **Settings** a la izquierda del cuadro de diálogo **Publish Web**. Haga clic en el icono **v** para seleccionar la **Remote connection string** de **ApplicationDbContext** y seleccione **ContactDB**.
    
    ![configuración](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rrc2.png)

3.  Debajo de **ContactManagerContext**, seleccione **Execute Code First Migrations**.
    
    ![configuración](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rrc3.png)

4.  Haga clic en **Publicar**.

5.  Inicie sesión con el nombre de usuario *user1@contoso.com* y compruebe que puede editar los datos.

6.  Cierre sesión.

7.  Inicie sesión utilizando Google o Facebook. De este modo, se agregará la cuenta de Google o Facebook al rol **canEdit**.

### Detención del sitio web para evitar que se registren otros usuarios

1.  En el **Explorador de servidores**, desplácese hasta **Sitios web**. 2.  Haga clic con el botón derecho en cada sesión del sitio web y seleccione **Stop Web Site**.
    
    ![detener sitio
    web](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rrr2.png)
    
    También existe la opción de seleccionar el sitio web en el Portal de administración de Azure y, a continuación, hacer clic en el icono **stop** situado en la parte inferior de la página.
    
    ![detener sitio
    web](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rrr3.png)

### Eliminación de AddToRoleAsync, Publish y Test

1.  Convierta en comentario o elimine el código siguiente del método **ExternalLoginConfirmation** en el controlador de cuentas: `await UserManager.AddToRoleAsync(user.Id, "canEdit");`
2.  Desarrolle el proyecto (lo que guarda los cambios del archivo) y compruebe que no haya errores de compilación.
3.  Haga clic con el botón derecho en el proyecto, en el **Explorador de soluciones**, y seleccione **Publicar**.
    
	![Publish in project context menu](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/GS13publish.png)

4.  Haga clic en el botón **Start Preview**. Solo se implementan los archivos que deben actualizarse.
5.  Inicie el sitio web desde Visual Studio o el Portal. **No podrá publicar contenido mientras el sitio web esté detenido**.
    
    ![iniciar sitio
    web](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/ss15.png)

6.  Vuelva a Visual Studio y haga clic en **Publicar**. 7.  La aplicación de Azure se abre en el explorador predeterminado. Si ha iniciado sesión, ciérrela para ver la página principal como un usuario anónimo.
8.  Haga clic en el enlace **About**. Se le redirigirá a la página de inicio de sesión.
9.  Haga clic en el enlace **Register** de la página de inicio de sesión y cree una cuenta local. Utilizaremos esta cuenta local para comprobar que pueda obtener acceso a las páginas de solo lectura pero no a las páginas que cambian datos (están protegidas por el rol *canEdit*). Más adelante en el tutorial eliminaremos la cuenta   local.
    
    ![Registro](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/ss16.PNG)

10. Compruebe que pueda obtener acceso a las páginas *About* y *Contact*.
    
    ![Cerrar
    sesión](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/ss17.PNG)

11. Haga clic en el enlace **CM Demo** para obtener acceso al controlador **Cm**. También puede agregar *Cm* a la URL.
    
    ![Página
    CM](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rrr4.png)

12. Haga clic en un enlace de edición. Se le redirigirá a la página de inicio de sesión. Debajo de **Use another service to log in**, haga clic en Google o Facebook e inicie sesión con la cuenta que registró
    anteriormente. (Si está trabajando con rapidez y la cookie de la sesión aún no ha expirado, iniciará sesión automáticamente con la cuenta de Google o Facebook utilizada anteriormente).
13. Compruebe que pueda editar datos mientras está abierta esa cuenta. **Nota:** no puede cerrar sesión en una cuenta de Google desde esta aplicación y, a continuación, iniciar sesión en otra cuenta de Google diferente con el mismo explorador. Si utiliza un solo explorador, debe ir al sitio de Google y cerrar sesión desde allí. Puede iniciar sesión con otra cuenta del mismo autenticador externo (como Google) si utiliza un explorador diferente.

## Examen de la base de datos SQL de Azure

1.  En el **Explorador de servidores**, desplácese hasta **ContactDB**. 2.  Haga clic con el botón derecho en **ContactDB** y seleccione **Open in SQL Server Object Explorer**.
    
    ![abrir en
    SSOX](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rrr12.png)

	**Nota:** si no puede expandir las **bases de datos SQL** *ni* ver la base de datos **ContactDB** desde Visual Studio, debe seguir las instrucciones que aparecen a continuación para abrir un puerto o rango de puertos de firewall. Siga las instrucciones de **Set up Azure firewall rules**. Es posible que tenga que esperar unos minutos para obtener acceso a la base de datos una vez agregada la regla de firewall.

1.  Haga clic con el botón derecho en la tabla **AspNetUsers** y seleccione **View Data**.
    
    ![Página
    CM](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rrr8.png)

2.  El identificador de la cuenta de Google con la que se registró estará incluido en el rol **canEdit**, al igual que el identificador de *user1@contoso.com*. Estos deberían ser los únicos usuarios del rol **canEdit**. (Esto se comprobará en el paso siguiente).
    
    ![Página
    CM](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rrr9.png)

3.  En el **Explorador de objetos de SQL Server**, haga clic con el botón derecho en **AspNetUserRoles** y seleccione **View Data**.
    
    ![Página
    CM](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rs1.png)

Compruebe que los **identificadores de usuario** coincidan con los del usuario *user1@contoso.com* y la cuenta de Google que registró.

## Configuración de las reglas de firewall de Azure

Siga los pasos de esta sección si no puede conectar con SQL Azure desde Visual Studio o si aparece un diálogo de error del tipo "No se puede abrir el servidor".

![error de
firewall](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rx5.png)

Deberá agregar su dirección IP a la lista de direcciones IP permitidas.

1.  En el Portal de Azure, seleccione **Bases de datos SQL** en la pestaña de la izquierda.
    
    ![Seleccionar
    SQL](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rx6.png)

2.  Haga clic en **ContactDB**.

3.  Haga clic en el vínculo **Set up Azure firewall rules for this IP address**.
    
    ![reglas de
    firewall](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rx7.png)

4.  Cuando aparezca el mensaje "The current IP address xxx.xxx.xxx.xxx is not included in existing firewall rules. Do you want to update the firewall rules?", haga clic en **Yes**. A menudo, con algunos firewalls corporativos, no basta con agregar esta dirección, sino que hay que agregar un rango de direcciones IP.

El paso siguiente consiste en agregar un rango de direcciones IP permitidas.

1.  En el Portal de Azure, haga clic en **Bases de datos SQL**. 2.  Haga clic en la pestaña **Servers** y, a continuación, haga clic en el servidor que desea configurar.
    
    ![Pestaña Servers en Azure
    ](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/ss25.PNG)

3.  Haga clic en la pestaña **Configure**.

4.  Agregue un nombre de regla y las direcciones IP de inicio y fin.
    
    ![rango de direcciones
    IP](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rx9.png)

5.  En la parte inferior de la página, haga clic en **Save**. 6.  Es importante que haga comentarios y nos informe si necesita agregar un rango de direcciones IP para conectarse.

Finalmente, puede conectarse a la instancia de Base de datos SQL desde SQL Server Object Explorer (SSOX).

1.  En el menú Ver, haga clic en el **Explorador de objetos de SQL Server**.
2.  Haga clic con el botón secundario en **SQL Server** y seleccione **Agregar SQL Server**.
3.  En el cuadro de diálogo **Conectar al servidor**, establezca el parámetro **Authentication** en **Autenticación de SQL Server**. Obtendrá el **nombre del servidor** y los **datos de inicio de sesión** del Portal de Azure.
4.  En el explorador, desplácese hasta el portal y seleccione **Bases de datos SQL**.
5.  Seleccione la base de datos **ContactDB** y, a continuación, haga clic en **View SQL Database connection strings**.
6.  En la página **Connection Strings**, copie el **servidor** y el **identificador de usuario**.
    
    ![con
    string](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/ss21.PNG)

7.  Pegue el **servidor** y el **identificador de usuario** en el diálogo **Connect to Server** de Visual Studio. El **identificador de usuario** debe escribirlo en el campo **Login**. Escriba la contraseña que utilizó para crear la base de datos SQL.
    
    ![Diálogo Connect to
    Server](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rss1.png)

Ahora puede desplazarse hasta la base de datos ContactDB siguiendo las instrucciones facilitadas anteriormente.

## Incorporación de un usuario al rol canEdit mediante la edición de las tablas de la base de datos

Anteriormente en este tutorial, utilizamos código para agregar usuarios al rol canEdit. Existe un método alternativo que consiste en manipular directamente los datos de las tablas de suscripciones. Los pasos siguientes muestran cómo utilizar este método alternativo para agregar un usuario a un rol.

1.  En el **Explorador de objetos de SQL Server**, haga clic con el botón derecho en **AspNetUserRoles** y seleccione **View Data**.
    
    ![Página
    CM](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rs1.png)

2.  Copie el *identificador del rol* y péguelo en la fila vacía (nueva).
    
    ![Página
    CM](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rs2.png)

3.  En la tabla **AspNetUsers**, busque el usuario que desee agregar al rol, copie su *identificador* y, a continuación, péguelo en la columna **UserId** de la tabla **AspNetUserRoles**.

Estamos trabajando en una herramienta que facilitará mucho la tarea de administrar los usuarios y roles.

## Consideraciones acerca del registro local

El sistema de registro ASP.NET actual del proyecto no permite restablecer la contraseña ni comprueba que quien se está registrando sea un usuario humano (por ejemplo con una prueba [CAPTCHA][14]). El usuario solo necesita autenticarse a través de uno de los proveedores externos para poder registrarse. Si decide deshabilitar el registro local, siga estos pasos:

1.  En el controlador de cuentas, elimine el atributo *[AllowAnonymous]* de los métodos *Register* GET y POST. De esta forma evitará que se registren bots y usuarios anónimos.
2.  En la carpeta *Views\\Shared*, en el archivo *\_LoginPartial.cshtml*, elimine el enlace de acción Register.
3.  En el archivo *Views\\Account\\Login.cshtml*, elimine el enlace de acción Register.
4.  Implemente la aplicación.

<h2><a name="nextsteps"></a><span  class="short-header">Pasos siguientes</span>Pasos siguientes</h2>


Siga el tutorial [Create an ASP.NET MVC 5 App with Facebook and Google OAuth2 and OpenID Sign-on][8] para obtener instrucciones acerca de cómo agregar datos de perfiles a la base de datos de registro de usuarios y para informarse en detalle acerca del uso de Facebook como proveedor de autenticación.

Para habilitar los botones de inicio de sesión de redes sociales que aparecen en la parte superior de este tutorial, consulte [Pretty social login buttons for ASP.NET MVC 5][15].

Un buen lugar para aprender más acerca de ASP.NET MVC es el tutorial [Getting Started with ASP.NET MVC 5][16]. El excelente tutorial [Getting Started with EF and MVC][17] de Tom Dykstra le proporcionará información más avanzada acerca de la programación con MVC y EF.

Este tutorial y la aplicación de ejemplo fueron desarrollados por [Rick Anderson][18] (Twitter [@RickAndMSFT][1]) con la colaboración de Tom Dykstra y Barry Dorrans (Twitter [@blowdart][19]).

Es importante que haga comentarios acerca de lo que le gustó o lo que le gustaría que mejorásemos, no solo en relación al tutorial en sí sino a los productos sobre los que trata. Sus comentarios nos ayudarán a clasificar las mejoras por orden de prioridad. También puede solicitar y votar nuevos temas en [Show Me How With Code][20].

<!-- bookmarks -->

<!-- images-->



[1]: https://twitter.com/RickAndMSFT
[2]: http://oauth.net/ "http://oauth.net/"
[3]: http://openid.net/
[4]: http://go.microsoft.com/fwlink/?LinkId=390521
[5]: https://manage.windowsazure.com
[6]: http://msdn.microsoft.com/library/hh770484.aspx
[7]: http://blogs.msdn.com/b/rickandy/archive/2013/02/12/seeding-and-debugging-entity-framework-ef-dbs.aspx
[8]: http://www.asp.net/mvc/tutorials/mvc-5/create-an-aspnet-mvc-5-app-with-facebook-and-google-oauth2-and-openid-sign-on
[9]: http://curah.microsoft.com/55636/aspnet-identity
[10]: http://msdn.microsoft.com/en-us/library/ms228053.aspx
[11]: http://msdn.microsoft.com/en-us/library/system.web.mvc.authorizeattribute.aspx
[12]: http://msdn.microsoft.com/en-us/library/system.web.mvc.requirehttpsattribute.aspx
[13]: http://blogs.msdn.com/b/rickandy/archive/2012/03/23/securing-your-asp-net-mvc-4-app-and-the-new-allowanonymous-attribute.aspx
[14]: http://www.asp.net/web-pages/tutorials/security/16-adding-security-and-membership
[15]: http://www.beabigrockstar.com/pretty-social-login-buttons-for-asp-net-mvc-5/
[16]: http://www.asp.net/mvc/tutorials/mvc-5/introduction/getting-started
[17]: http://www.asp.net/mvc/tutorials/getting-started-with-ef-using-mvc/creating-an-entity-framework-data-model-for-an-asp-net-mvc-application
[18]: http://blogs.msdn.com/b/rickandy/
[19]: https://twitter.com/blowdart
[20]: http://aspnet.uservoice.com/forums/228522-show-me-how-with-code