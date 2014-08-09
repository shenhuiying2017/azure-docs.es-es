<properties linkid="" urlDisplayName="" pageTitle="" metaKeywords="" description="" metaCanonical="" services="" documentationCenter="" title=" OAuth" authors="riande" solutions="" manager="wpickett" editor="mollybos" />

Implementación de una aplicación ASP.NET MVC segura con suscripción, OAuth y Base de datos SQL en un sitio web de Azure
=======================================================================================================================

***Por [Rick Anderson](https://twitter.com/RickAndMSFT) y Tom Dykstra. Actualización de 15 de octubre de 2013.***
[Visual Studio 2013](/es-es/develop/net/tutorials/web-site-with-sql-database/ "Visual Studio 2013")[Visual Studio 2012](/es-es/develop/net/tutorials/web-site-with-sql-database-vs2012/ "Visual Studio 2012")

**Nota:**

Hay disponible una [versión más reciente de este tutorial](/es-es/develop/net/tutorials/web-site-with-sql-database/). Puede seguir esta versión si desea usar Visual Studio 2012, pero la versión más actualizada es mucho más fácil de seguir.

Este tutorial muestra cómo desarrollar una aplicación web ASP.NET MVC 4 segura que permita a los usuarios iniciar sesión con las credenciales de Facebook, Yahoo y Google. Además, la aplicación se implementará en Azure.

Puede abrir una cuenta de Azure de manera gratuita y, si todavía no tiene Visual Studio 2012, el SDK instala automáticamente Visual Studio 2012 Express para Web. Puede empezar a desarrollar contenido para Azure sin coste alguno.

En este tutorial se asume que no tiene ninguna experiencia previa con Azure. Cuando acabe, tendrá una aplicación web controlada por datos segura que se ejecutará en la nube y utilizará una base de datos en la nube.

Aprenderá a realizar los siguientes procedimientos:

-   Habilitar su equipo para desarrollar contenido de Azure mediante la instalación del SDK de Azure
-   Crear un proyecto ASP.NET MVC 4 seguro y publicarlo en un Sitio web Azure
-   Usar la base de datos de suscripciones a ASP.NET y OAuth para proteger su aplicación
-   Implementar una base de datos de suscripciones en Azure
-   Utilizar una Base de datos SQL para almacenar datos en Azure
-   Usar Visual Studio para actualizar y administrar la base de datos de suscripciones

Va a desarrollar una aplicación web de lista de contactos sencilla basada en ASP.NET MVC 4 y que utiliza ADO.NET Entity Framework para obtener acceso a la base de datos. La ilustración siguiente muestra la página de inicio de sesión de la aplicación final:

![página de inicio de sesión](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2012/rxb.png)

[WACOM.INCLUDE [create-account-and-websites-note](../includes/create-account-and-websites-note.md)]

Apartados de este tutorial:

-   [Configuración del entorno de desarrollo](#bkmk_setupdevenv)
-   [Configuración del entorno de Azure](#bkmk_setupwindowsazure)
-   [Creación de una aplicación ASP.NET MVC 4](#bkmk_createmvc4app)
-   [Implementación de la aplicación en Azure](#bkmk_deploytowindowsazure1)
-   [Incorporación de una base de datos a la aplicación](#bkmk_addadatabase)
-   [Incorporación de un proveedor de OAuth](#addOauth)
-   [Incorporación de roles a la base de datos de suscripciones](#mbrDB)
-   [Creación de un script de implementación de datos](#ppd)
-   [Implementación de la aplicación en Azure](#bkmk_deploytowindowsazure11)
-   [Actualización de la base de datos de suscripciones](#ppd2)
-   [Pasos siguientes](#nextsteps)

Configuración del entorno de desarrollo
---------------------------------------

Para comenzar, configure el entorno de desarrollo mediante la instalación del SDK de Azure para .NET Framework.

1.  Para instalar el SDK de Azure para .NET, haga clic en el vínculo a continuación. Si no tiene instalado todavía Visual Studio 2012, se instalará a través del vínculo. Este tutorial requiere Visual Studio 2012. [SDK de Azure para Visual Studio 2012](http://go.microsoft.com/fwlink/?LinkId=254364)
2.  Cuando se le solicite ejecutar o guardar el archivo ejecutable de instalación, haga clic en **Run**.
3.  En la ventana del instalador de plataforma web, haga clic en **Install** y continúe con la instalación.

![Instalador de plataforma web: SDK de Azure para .NET](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2012/WebPIAzureSdk20NetVS12.png)

Cuando la instalación se complete, dispondrá de todo lo necesario para iniciar el desarrollo.

Configuración del entorno de Azure
----------------------------------

A continuación, configure el entorno de Azure creando un sitio web de Azure y una Base de datos SQL.

### Creación de un sitio web y una Base de datos SQL en Azure

El sitio web de Azure se ejecutará en un entorno de hospedaje compartido, es decir, en máquinas virtuales compartidas con otros clientes de Azure. Los entornos de hospedaje compartidos ofrecen un coste reducido a los nuevos usuarios de servicios en la nube. Más adelante, si el tráfico web aumenta, es posible escalar la aplicación para atender la demanda ejecutándola en máquinas virtuales dedicadas. Si necesita una arquitectura más compleja, puede migrar la aplicación a un Servicio en la nube de Azure. Los servicios en la nube se ejecutan en MV dedicadas que se pueden configurar según sus necesidades.

La Base de datos SQL de Azure es un servicio de base de datos relacional en la nube que se basa en la tecnología de SQL Server. Las herramientas y aplicaciones que funcionan con SQL Server también funcionan con la Base de datos SQL.

1.  En el [Portal de administración de Azure](https://manage.windowsazure.com), haga clic en **Sitios web** en la pestaña izquierda y, a continuación, en **New**.

    ![Botón New del portal de administración](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2012/rxWSnew2.png)

1.  Haga clic en **CUSTOM CREATE**.

    ![Vínculo Create with Database del Portal de administración](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2012/rxCreateWSwithDB.png)

    Se abre el asistente para **creación personalizada de un nuevo sitio web**.

1.  En el paso **New Web Site** del asistente, escriba en el cuadro **URL** el valor que se va a utilizar como URL única de la aplicación. La URL completa consistirá en el valor que escriba más el sufijo que aparece junto al cuadro de texto. La ilustración muestra "contactmgr2", pero dicha URL probablemente ya esté en uso, por lo que tendrá que elegir otra distinta.

    ![Vínculo Create with Database del Portal de administración](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2012/rxCreateWSwithDB_2.png)

2.  En la lista desplegable **Database**, elija **Create a new SQL database**.

3.  En la lista desplegable **Region**, elija la misma región que seleccionó para el Sitio web. Esta configuración especifica qué centro de datos se ejecutará en la máquina virtual. En **DB CONNECTION STRING NAME**, escriba *connectionString1*.

    ![Paso de creación de un nuevo sitio web del asistente para la creación de un nuevo sitio web con base de datos](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2012/rxCreateWSwithDB_2.png)

4.  Haga clic en la flecha derecha situada en la parte inferior del cuadro. El asistente avanza hasta el paso **Database Settings**.

5.  En el cuadro **Name**, escriba *ContactDB*.

6.  En el cuadro **Server**, seleccione **New SQL Database server** Si anteriormente creó una base de datos de SQL Server, también tiene la opción de seleccionar ese SQL Server en el control desplegable.

7.  Complete los campos **LOGIN NAME** y **PASSWORD** con los datos de un administrador. Si seleccionó **New SQL Database server**, no debe escribir un nombre y una contraseña existentes en estos campos, sino definir unos nuevos que volverá a utilizar más adelante para obtener acceso a la base de datos. Si seleccionó un SQL Server creado anteriormente, se le pedirá la contraseña de la cuenta de SQL Server que abrió para crearlo. En este tutorial, no activaremos la casilla **Advanced**. La casilla **Advanced ** le permite establecer el tamaño de la base de datos (el valor predeterminado es 1 GB, pero puede aumentarlo hasta 150 GB) y la intercalación.

8.  Haga clic en la marca de verificación que aparece en la parte inferior del cuadro para indicar que finalizó la configuración.

    ![Paso Configuración de base de datos del asistente para creación de un nuevo sitio web con base de datos](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2012/dntutmobile-setup-azure-site-004.png)

    La imagen siguiente muestra cómo usar un SQL Server y datos de inicio de sesión existentes. ![Paso Configuración de base de datos del asistente para creación de un nuevo sitio web con base de datos](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2012/rxPrevDB.png)

    El Portal de administración vuelve a la página de Sitios web y la columna **Status** indica que se está creando el sitio. Después de unos segundos, la columna **Status** indica que el sitio se creó correctamente. En la barra de navegación situada a la izquierda, el número de sitios que tiene en su cuenta aparece junto al icono **Sitios web** y el número de bases de datos, junto al icono **Bases de datos SQL**.

Creación de una aplicación ASP.NET MVC 4
----------------------------------------

Ya creó un sitio web de Azure, pero todavía no hay contenido en él. El siguiente paso consiste en crear el proyecto de aplicación web de Visual Studio que luego publicará en Azure.

### Creación del proyecto

1.  Inicie Visual Studio 2012.
2.  En el menú **Archivo**, haga clic en **Nuevo proyecto**.
3.  En el cuadro de diálogo **Nuevo proyecto**, expanda **Visual C\#** y seleccione **Web** debajo de **Plantillas instaladas**; a continuación, seleccione **Aplicación web de ASP.NET MVC 4**. Mantenga el valor **.NET Framework 4.5** predeterminado. Póngale a la aplicación el nombre **ContactManager** y haga clic en **Aceptar**.

    ![Cuadro de diálogo Nuevo proyecto](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2012/dntutmobile-createapp-002.png)

4.  En el cuadro de diálogo **New ASP.NET MVC 4 Project**, seleccione la plantilla **Internet Application**. Mantenga Razor como **motor de vistas** predeterminado y, a continuación, haga clic en **Aceptar**.

    ![Cuadro de diálogo de nuevo proyecto de ASP.NET MVC 4](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2012/rxb2.png)

### Establecimiento del encabezado y pie de página

1.  En el **Explorador de soluciones**, expanda la carpeta Views\\Shared y abra el archivo *\_Layout.cshtml*.

    ![\_Layout.cshtml en el Explorador de soluciones](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2012/dntutmobile-createapp-004.png)

2.  Reemplace las apariciones de “My ASP.NET MVC Application” por “ContactManager”.
3.  Reemplace “your logo here” por “CM Demo”.

### Ejecución de la aplicación de forma local

1.  Presione CTRL+F5 para ejecutar la aplicación. Aparece la página principal de la aplicación en el explorador predeterminado. ![Página de inicio de lista de tareas pendientes](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2012/rxa.png)

Esto es todo lo que necesita hacer por ahora para crear la aplicación que va a implementar en Azure. Más adelante agregará la funcionalidad de base de datos.

Implementación de la aplicación en Azure
----------------------------------------

1.  En el explorador, abra el [Portal de administración de Azure](http://manage.windowsazure.com "portal").

2.  En la pestaña **Sitios web**, haga clic en el nombre del sitio que creó anteriormente.

    ![Aplicación de contacto con el administrador en la pestaña de Sitios web del Portal de administración](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2012/dntutmobile-setup-azure-site-006.png)

3.  A la derecha de la ventana, haga clic en **Download publish profile**.

    ![Pestaña de inicio rápido y botón de descarga de perfil de publicación](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2012/dntutmobile-deploy1-download-profile.png)

    En este paso se descarga un archivo que contiene toda la configuración necesaria para implementar una aplicación en el sitio web. Realizará la importación de este archivo en Visual Studio, por lo que no tiene que especificar esta información manualmente.

4.  Guarde el archivo .*publishsettings* en una carpeta a la que pueda obtener acceso desde Visual Studio.

    ![Almacenamiento del archivo .publishsettings](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2012/dntutmobile-deploy1-save-profile.png)

    [WACOM.INCLUDE [publishsettingsfilewarningchunk](../includes/publishsettingsfilewarningchunk.md)]

5.  En Visual Studio, haga clic con el botón secundario en el proyecto, en el **Explorador de soluciones**, y seleccione **Publicar** en el menú contextual.

    ![Opción Publicar del menú contextual del proyecto](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2012/PublishVSSolution.png)

    Se abre el asistente para **publicación web**.

6.  En la pestaña **Perfil** del asistente para **publicación web**, haga clic en **Importar**.

    ![Importar configuración de publicación](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2012/ImportPublishSettings.png)

    Aparecerá el cuadro de diálogo **Import Publish Profile**.

7.  Lleve a cabo los siguientes pasos si no ha agregado anteriormente su suscripción de Azure en Visual Studio: con estos pasos agregará su suscripción, de manera tal que la lista desplegable que aparece bajo **Import from an Azure web site** incluirá su sitio web.

    a. En el cuadro de diálogo **Import Publish Profile**, haga clic en **Add Azure subscription**.

    ![Incorporación de sub a win az](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2012/rzAddWAsub.png)

    b. En el cuadro de diálogo **Import Azure Subscriptions**, haga clic en **Download subscription file**.

    ![Descarga de sub](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2012/rzDownLoad.png)

    c. En la ventana del explorador, guarde el archivo *.publishsettings*.

    ![Descarga de archivo pub](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2012/rzDown2.png)

    > [WACOM.NOTE] El archivo .publishsettings contiene sus credenciales (sin codificar) que se utilizan para administrar sus suscripciones y servicios de Azure. El procedimiento recomendado para este archivo consiste en almacenarlo temporalmente fuera de los directorios de origen (por ejemplo en la carpeta Bibliotecas\\Documentos) y, a continuación, eliminarlo cuando la importación se haya completado. Un usuario malintencionado que obtuviera acceso al archivo .publishsettings podría modificar, crear y eliminar sus servicios de Azure.

    En el cuadro de diálogo **Import Azure Subscriptions**, haga clic en **Browse** y vaya al archivo *.publishsettings*.

    ![Descarga de sub](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2012/rzDownLoad.png)

    e. Haga clic en **Import**.

    ![Importación](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2012/rzImp.png)

8.  En el cuadro de diálogo **Import Publish Profile**, seleccione **Import from an Azure web site**, seleccione su sitio web en la lista desplegable y, a continuación, haga clic en **OK**.

    ![Importar perfil de publicación](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2012/ImportPublishProfile.png)

    La aplicación creada ahora se ejecuta en la nube. La próxima vez que implemente la aplicación, solo se implementarán los archivos modificados o nuevos.

    ![Página de inicio de tareas pendientes ejecutándose en Azure](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2012/newapp005.png)

Incorporación de una base de datos a la aplicación
--------------------------------------------------

A continuación, actualizará la aplicación MVC incorporando funciones que permitan mostrar y actualizar los contactos y almacenar los datos en una base de datos. La aplicación utilizará Entity Framework para crear la base de datos y para leer y actualizar los datos que esta contiene.

### Incorporación de clases de modelos de datos para los contactos

Se empieza por crear un modelo de datos sencillo en código.

1.  En el **Explorador de soluciones**, haga clic con el botón secundario en la carpeta Modelos, a continuación en **Add** y, por último, en **Class**.

    ![Agregar clase en el menú contextual de la carpeta Models](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2012/dntutmobile-adddatabase-001.png)

1.  En el cuadro de diálogo **Add New Item**, ponga al archivo de la nueva clase el nombre *Contact.cs* y, a continuación, haga clic en **Add**.

    ![Cuadro de diálogo Add New Item](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2012/dntutmobile-adddatabase-002.png)

1.  Reemplace el contenido del archivo Contacts.cs por el código siguiente.

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

Incorporación de un controlador y una vista para los datos
----------------------------------------------------------

1.  Cree el proyecto **(Ctrl+Mayús+B)**. (Debe crear el proyecto antes de usar el mecanismo de scaffolding).
2.  En el **Explorador de soluciones**, haga clic con el botón secundario en la carpeta Controladores, a continuación en **Add** y, por último, en **Controller**.

    ![Agregar controlador en el menú contextual de la carpeta Controllers](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2012/dntutmobile-controller-add-context-menu.png)

3.  En el cuadro de diálogo **Add Controller**, escriba "HomeController" como su nombre de controlador.
4.  Establezca la plantilla **Scaffolding options** en **MVC Controller with read/write actions and views, using Entity Framework**.
5.  Seleccione **Contact** como clase de modelo y **&lt;New data context...\>** como clase de contexto de datos.

    ![Cuadro de diálogo Add Controller](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2012/dntutmobile-controller-add-controller-dialog.png)

6.  En el cuadro de diálogo **Nuevo contexto de datos**, acepte el valor predeterminado *ContactManager.Models.ContactManagerContext*. ![Cuadro de diálogo Agregar controlador](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2012/rxNewCtx.png)

7.  Haga clic en **Aceptar** y, a continuación, en **Agregar** en el cuadro de diálogo **Agregar controlador**.
8.  En el cuadro de diálogo de sobrescritura **Agregar controlador**, asegúrese de que todas las opciones están activadas y haga clic en **Aceptar**.

    ![Cuadro de mensaje Agregar controlador](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2012/rxOverwrite.png)

Visual Studio crea métodos y vistas de controlador para las operaciones CRUD de base de datos que afectan a los objetos **Contact**.

Habilitación de las migraciones, creación de la base de datos e incorporación de datos de ejemplo y un inicializador de datos
-----------------------------------------------------------------------------------------------------------------------------

La siguiente tarea consiste en habilitar la función [Migraciones de Code First](http://msdn.microsoft.com/library/hh770484.aspx) para crear la base de datos a partir del modelo de datos ya establecido.

1.  En el menú **Tools**, seleccione **Library Package Manager** y, a continuación **Package Manager Console**.

    ![Package Manager Console en el menú Herramientas](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2012/dntutmobile-migrations-package-manager-menu.png)

2.  En la ventana **Package Manager Console**, escriba el comando siguiente:

         enable-migrations -ContextTypeName ContactManagerContext

    ![enable-migrations](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2012/rxE.png) Debe especificar el nombre de tipo de contexto (**ContactManagerContext**) porque el proyecto contiene dos clases derivadas de [DbContext](http://msdn.microsoft.com/es-es/library/system.data.entity.dbcontext(v=VS.103).aspx), **ContactManagerContext**, que se acaba de crear, y **UsersContext**, que se usa para la base de datos de suscripciones. La clase **ContactManagerContext** se agregó mediante el asistente de scaffolding de Visual Studio.

    El comando **enable-migrations** crea una carpeta *Migrations* y guarda en ella un archivo *Configuration.cs* que puede editar para configurar las migraciones.

3.  En la ventana **Package Manager Console**, escriba el comando siguiente:

         add-migration Initial

    El comando **add-migration Initial** genera un archivo denominado **&lt;date\_stamp\>Initial** en la carpeta *Migrations* que crea la base de datos. El primer parámetro (**Initial**) es arbitrario y se utiliza para crear el nombre del archivo. Puede ver los archivos de las nuevas clases en el **Explorador de soluciones**.

    En la clase **Initial**, el método **Up** crea la tabla Contacts y el método **Down** (que se utiliza cuando se desea volver al estado anterior) la anula.

4.  Abra el archivo *Migrations\\Configuration.cs*.
5.  Agregue los siguientes espacios de nombres.

    	using ContactManager.Models;

6.  Reemplace *Seed* por el código siguiente:

         protected override void Seed(ContactManager.Models.ContactManagerContext context)
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

    El código anterior inicializará la base de datos con la información de contacto. Para obtener más información acerca de la inicialización de la base de datos, consulte [Seeding and Debugging Entity Framework (EF) DBs](http://blogs.msdn.com/b/rickandy/archive/2013/02/12/seeding-and-debugging-entity-framework-ef-dbs.aspx).

7.  En **Package Manager Console**, escriba el comando:

         update-database

    ![Comandos de Package Manager Console](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2012/dntutmobile-migrations-package-manager-console.png)

    El comando **update-database** ejecuta la primera migración, lo que crea la base de datos. De manera predeterminada, la base de datos que se crea es una base de datos LocalDB de SQL Server Express. (A no ser que tenga SQL Server Express instalado, en cuyo caso se crea la base de datos con la instancia de SQL Server Express).

8.  Presione CTRL+F5 para ejecutar la aplicación.

La aplicación muestra los datos de inicialización y ofrece enlaces de edición, detalles y eliminación.

![Vista MVC de los datos](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2012/rx2.png)

OAuth Incorporación de un proveedor de OAuth
--------------------------------------------

[OAuth](http://oauth.net/ "http://oauth.net/") es un protocolo abierto que ofrece autorización segura a través de un método estándar sencillo para las aplicaciones web, móviles y de escritorio. La plantilla de Internet ASP.NET MVC utiliza OAuth para ofrecer Facebook, Twitter, Google, Yahoo y Microsoft como proveedores de autenticación. Aunque este tutorial solo utiliza Facebook, Google y Yahoo como proveedores de autenticación, puede modificar fácilmente el código para utilizar cualquiera de los proveedores. Los pasos que hay que seguir para implementar otros proveedores son muy similares a los que verá en este tutorial.

Además de la autenticación, este tutorial también utiliza roles para implementar la autorización. Únicamente los usuarios que agregue al rol canEdit podrán crear, editar o eliminar contactos.

Registro con un proveedor externo
---------------------------------

Para autenticar usuarios con credenciales desde proveedores externos, debe registrar su sitio web con el proveedor y obtener una clave y un secreto de conexión. Google y Yahoo no requieren que se registre y obtenga claves.

Este tutorial no le muestra todos los pasos que debe realizar para registrarse con estos proveedores. Los pasos no son complicados normalmente. Para registrar correctamente su sitio, siga las instrucciones que se proporcionan en esos sitios. Para comenzar con el registro del sitio, consulte el sitio del desarrollador para:

-   [Facebook](http://developers.facebook.com/)
-   [Microsoft](http://go.microsoft.com/fwlink/?LinkID=144070)
-   [Twitter](http://dev.twitter.com/)

Diríjase a la página [https://developers.facebook.com/apps](https://developers.facebook.com/apps/) e inicie sesión si fuese necesario. Haga clic en el botón **Register as a Developer** y complete el proceso de registro. Una vez que haya completado el registro, haga clic en **Create New App**. Escriba un nombre para la aplicación. No tiene que especificar un espacio de nombres de la aplicación.

![Creación de una nueva aplicación de Facebook](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2012/rxFBapp.png)

Especifique localhost para **App Domain** y http://localhost/ para **Site URL**. Haga clic en **Enabled** para **Sandbox Mode** y, a continuación, en **Save Changes**.

Necesitará el **App ID** y el **App Secret** para implementar OAuth en esta aplicación. ![Nueva aplicación de Facebook](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2012/rxFB.png)

Creación de usuarios de prueba
------------------------------

En el panel izquierdo, en **Settings**, haga clic en **Developer Roles**. Haga clic en el vínculo **Create** en la fila **Test Users** (no en la fila **Testers**).

![Evaluadores de Facebook](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2012/rxFBt.png)

Haga clic en el vínculo **Modify** para obtener el correo electrónico de los usuarios de prueba (que usará para iniciar sesión en la aplicación). Haga clic en el vínculo **See More** y, a continuación, haga clic en **Edit** para configurar la contraseña de los usuarios de prueba.

Incorporación del identificador y del secreto de la aplicación del proveedor
----------------------------------------------------------------------------

Abra el archivo *App\_Start\\AuthConfig.cs*. Quite los caracteres de comentario del método *RegisterFacebookClient* y agregue el identificador y el secreto de la aplicación. Use los valores obtenidos; los valores que se muestran a continuación no funcionarán. Quite los caracteres de comentario de la llamada de *OAuthWebSecurity.RegisterGoogleClient* y agregue *OAuthWebSecurity.RegisterYahooClient* como se muestra a continuación. Los proveedores Google y Yahoo no requieren que se registre y obtenga claves. Advertencia: mantenga seguros el identificador y el secreto de la aplicación. Un usuario malintencionado que disponga del identificador y el secreto de la aplicación puede fingir ser la aplicación.

     public static void RegisterAuth()
        {
            OAuthWebSecurity.RegisterFacebookClient(
                appId: "enter numeric key here",
                appSecret: "enter numeric secret here");

            OAuthWebSecurity.RegisterGoogleClient();
            OAuthWebSecurity.RegisterYahooClient();
        }

1.  Ejecute la aplicación y haga clic en el vínculo **Log In**.
2.  Haga clic en el botón **Facebook**.
3.  Especifique sus credenciales de Facebook o una de las credenciales de usuarios de prueba.
4.  Haga clic en **Okay** para permitir que la aplicación obtenga acceso a los recursos de Facebook.
5.  Se le redirige a la página de registro. Si inicia sesión con una cuenta de prueba, puede cambiar el **nombre de usuario** a un nombre más corto (por ejemplo,"Prueba FB Bill"). Haga clic en el botón **Register** que guardará el alias de correo electrónico y el nombre de usuario en la base de datos de suscripciones.
6.  Registre otro usuario. Actualmente, un error en el registro del sistema evita que pueda iniciar y cerrar sesión como otro usuario que use el mismo proveedor (es decir, no puede cerrar sesión en la cuenta de Facebook y volver a iniciar sesión con una cuenta de Facebook distinta). Para solucionar esto, diríjase al sitio con un explorador distinto y registre otro usuario. Se agregará un usuario al rol de administrador y dispondrá de acceso de edición en la aplicación. El otro usuario solo dispondrá de acceso a métodos de no edición en el sitio. Los usuarios anónimos solo dispondrán de acceso a la página principal.
7.  Registre otro usuario con un proveedor distinto.
8.  **Opcional**: también puede crear una cuenta local no asociada a uno de los proveedores. Más adelante en el tutorial, quitaremos la capacidad de crear cuentas locales. Para crear una cuenta local, haga clic en el vínculo **Log out** (si ha iniciado sesión) y, a continuación, haga clic en **Register link**. Es posible que desee crear una cuenta local para propósitos administrativos no asociados al proveedor de autenticación externo.

Base de datos de suscripcionesIncorporación de roles a la base de datos de suscripciones
----------------------------------------------------------------------------------------

En esta sección, agregará el rol *canEdit* a la base de datos de suscripciones. Únicamente los usuarios incluidos en el rol canEdit podrán editar los datos. Es recomendable nombrar los roles en función de las acciones que pueden realizar, por lo que el nombre *canEdit* es más aconsejable que *admin*. A medida que la aplicación evoluciona, puede agregar nuevos roles del tipo *canDeleteMembers* en lugar de *superAdmin*.

1.  En el menú **Ver**, haga clic en **Explorador de servidores**.

2.  En **Explorador de servidores**, expanda **DefaultConnection** y, a continuación, **Tables**.

3.  Haga clic con el botón secundario en **UserProfile** y haga clic en **Mostrar datos de tabla**.

    ![Mostrar datos de tabla](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2012/rxSTD.png)

4.  Registre el **UserId** para el usuario que vaya a tener el rol canEdit. En la siguiente imagen, el usuario *ricka* con **UserId** 2 tendrá el rol canEdit para el sitio.

    ![Identificadores de usuario](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2012/rxUid.png)

5.  Haga clic con el botón secundario en **webpages\_Roles** y haga clic en **Mostrar datos de tabla**.
6.  Especifique **canEdit** en la celda **RoleName**. **RoleId** será 1 si es la primera vez que ha agregado un rol. Registre el RoleID. Asegúrese de que no hay ningún carácter de espacio al final, "canEdit " en el rol no coincidiría con "canEdit" en el código de controlador.

    ![Identificador de rol](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2012/rxRoleID.png)

7.  Haga clic con el botón secundario en **webpages UsersInRoles** y haga clic en **Mostrar datos de tabla**. Especifique el **UserId** para el usuario al que desee conceder acceso *canEdit* y el **RoleId**.

    ![Tabla de identificador de rol de usuario](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2012/rxUR.png)

La tabla **webpages\_OAuthMembership** contiene el proveedor de OAuth, el UserID del proveedor y el UserID para cada usuario de OAuth registrado. La tabla **webpages-Membership** contiene la tabla de suscripciones de ASP.NET. Puede agregar usuarios a esta tabla mediante el vínculo de registro. Es buena idea agregar un usuario con el rol *canEdit* que no está asociado a Facebook o a otro proveedor de autorización de terceros para que siempre disponga de acceso a *canEdit* incluso cuando el proveedor de autorización de terceros no esté disponible. Posteriormente, en el tutorial, deshabilitaremos el registro de suscripciones a ASP.NET.

Protección de la aplicación con el atributo Authorize
-----------------------------------------------------

En esta sección, aplicaremos el atributo [Authorize](http://msdn.microsoft.com/es-es/library/system.web.mvc.authorizeattribute(v=vs.100).aspx) para restringir el acceso a los métodos de acción. Los usuarios anónimos podrán ver solo la página de inicio. Los usuarios registrados podrán ver las páginas de contactos, de acerca de y de información de contactos. Únicamente los usuarios incluidos en el rol *canEdit* podrán obtener acceso a los métodos de acción que cambian datos.

1.  Agregue el filtro [Authorize](http://msdn.microsoft.com/es-es/library/system.web.mvc.authorizeattribute(v=vs.100).aspx) y el filtro [RequireHttps](http://msdn.microsoft.com/es-es/library/system.web.mvc.requirehttpsattribute(v=vs.108).aspx) a la aplicación. Otra opción es agregar el atributo [Authorize](http://msdn.microsoft.com/es-es/library/system.web.mvc.authorizeattribute(v=vs.100).aspx) y el atributo [RequireHttps](http://msdn.microsoft.com/es-es/library/system.web.mvc.requirehttpsattribute(v=vs.108).aspx) a cada controlador, pero por motivos de seguridad es recomendable aplicarlos a toda la aplicación. De esta manera, todos los controladores y métodos de acción nuevos que agregue estarán automáticamente protegidos, sin necesidad de aplicar los atributos otra vez. Para obtener más información, consulte [Securing your ASP.NET MVC 4 App and the new AllowAnonymous Attribute](http://blogs.msdn.com/b/rickandy/archive/2012/03/23/securing-your-asp-net-mvc-4-app-and-the-new-allowanonymous-attribute.aspx). Abra el archivo *App\_Start\\FilterConfig.cs* y reemplace el método *RegisterGlobalFilters* por el siguiente.

         public static void
         RegisterGlobalFilters(GlobalFilterCollection filters)
         {
             filters.Add(new HandleErrorAttribute());
             filters.Add(new System.Web.Mvc.AuthorizeAttribute());
             filters.Add(new RequireHttpsAttribute());
         }

2.  Agregue el atributo [AllowAnonymous](http://blogs.msdn.com/b/rickandy/archive/2012/03/23/securing-your-asp-net-mvc-4-app-and-the-new-allowanonymous-attribute.aspx) al método **Index**. El atributo [AllowAnonymous](http://blogs.msdn.com/b/rickandy/archive/2012/03/23/securing-your-asp-net-mvc-4-app-and-the-new-allowanonymous-attribute.aspx) le permite incluir en una lista blanca los métodos que desee excluir de la autorización.
3.  Agregue [Authorize(Roles = "canEdit")] a los métodos Get y Post que cambian datos (Create, Edit y Delete).
4.  Agregue los métodos *About* y *Contact*. A continuación, se muestra un fragmento del código final.

         public class HomeController : Controller
         {
             private ContactManagerContext db = new ContactManagerContext();
             [AllowAnonymous]
             public ActionResult Index()
             {
                 return View(db.Contacts.ToList());
             }

             public ActionResult About()
             {
                 return View();
             }

             public ActionResult Contact()
             {
                 return View();
             }

             [Authorize(Roles = "canEdit")]
             public ActionResult Create()
             {
                 return View();
             }
             // Los métodos se han trasladado y omitido por motivos de claridad.
         }

5.  Quite el registro de suscripciones a ASP.NET. El sistema de registro ASP.NET actual del proyecto no permite restablecer la contraseña ni comprueba que quien se está registrando sea un usuario humano (por ejemplo con una prueba [CAPTCHA](http://www.asp.net/web-pages/tutorials/security/16-adding-security-and-membership)). El usuario solo necesita autenticarse a través de uno de los proveedores externos para poder registrarse. En el controlador de cuentas, elimine *[AllowAnonymous]* de los métodos *Register* GET y POST. De esta forma evitará que se registren bots y usuarios anónimos.
6.  En *Views\\Shared\_LoginPartial.cshtml*, elimine el vínculo de acción Register.
7.  Habilite SSL. En el Explorador de soluciones, haga clic en el proyecto **ContactManager** y, a continuación, en F4 para mostrar el diálogo de propiedades. Cambie **SSL Enabled** a true. Copie la **URL de SSL**.

    ![habilitar SSL](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2012/rxSSL.png)

8.  En el Explorador de soluciones, haga clic con el botón secundario en el proyecto **ContactManager** y, a continuación, en **Propiedades**.
9.  En la pestaña izquierda, haga clic en **Web**.
10. Cambie **Project Url** para usar la **URL de SSL**.
11. Haga clic en **Crear directorio virtual**.

    ![habilitar SSL](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2012/rxS2.png)

12. Presione CTRL+F5 para ejecutar la aplicación. El explorador mostrará una advertencia de certificado. Para nuestra aplicación, puede hacer clic de forma segura en el vínculo **Continue to this website**. Compruebe que solo los usuarios con el rol *canEdit* puedan cambiar datos. Compruebe que los usuarios anónimos solo puedan ver la página principal.

    ![Advertencia de certificado](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2012/rxNOT.png)

    ![Advertencia de certificado](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2012/rxNOT2.png)

Los sitios web de Azure incluyen un certificado de seguridad válido, por lo que no verá la advertencia cuando realice la implementación en Azure.

Preparación de la base de datosCreación de un script de implementación de datos
-------------------------------------------------------------------------------

Code First de Entity Framework no administra la base de datos de suscripciones, por lo que no puede usar Migrations para realizar la implementación. Usaremos el proveedor [dbDacFx](http://msdn.microsoft.com/es-es/library/dd394698.aspx) para implementar el esquema de base de datos y configuraremos el perfil de publicación para ejecutar un script que insertará los datos de suscripciones iniciales en las tablas de suscripciones.

Este tutorial usará SQL Server Management Studio (SSMS) para crear scripts de implementación de datos.

Instale SSMS desde el [Centro de descarga de Microsoft SQL Server 2012 Express](http://www.microsoft.com/en-us/download/details.aspx?id=29062) (en inglés):

-   [ENU\\x64\\SQLManagementStudio\_x64\_ENU.exe](http://download.microsoft.com/download/8/D/D/8DD7BDBA-CEF7-4D8E-8C16-D9F69527F909/ENU/x64/SQLManagementStudio_x64_ENU.exe) para sistemas de 64 bits.
-   [ENU\\x86\\SQLManagementStudio\_x86\_ENU.exe](http://download.microsoft.com/download/8/D/D/8DD7BDBA-CEF7-4D8E-8C16-D9F69527F909/ENU/x86/SQLManagementStudio_x86_ENU.exe) para sistemas de 32 bits.

Si selecciona el archivo incorrecto para su sistema, se producirá un error en la instalación y podrá probar el otro.

(Tenga en cuenta que se trata de una descarga de 600 megabytes. Es posible que tarde algo de tiempo en instalarse y puede requerir que se reinicie el equipo).

En la primera página del Centro de instalación de SQL Server, haga clic en **Nueva instalación independiente de SQL Server o agregar características a una instalación existente**, siga las instrucciones y acepte las opciones predeterminadas. La siguiente imagen muestra el paso de instalación de SSMS.

![Instalación de SQL](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2012/rxSS.png)

### Creación del script de la base de datos de desarrollo

1.  Ejecute SSMS.
2.  En el cuadro de diálogo **Connect to Server**, especifique *(localdb)\\v11.0* como nombre de servidor, deje **Authentication** establecido en **Windows Authentication** y, a continuación, haga clic en **Connect**. Si ha instalado SQL Express, especifique **.\\SQLEXPRESS**.

    ![Conectarse al cuadro de diálogo del servidor](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2012/rxC2S.png)

3.  En la ventana del **Explorador de objetos**, expanda **Databases**, haga clic con el botón secundario en **aspnet-ContactManager**, haga clic en **Tasks** y, a continuación, haga clic en **Generate Scripts**.

    ![Generación de scripts](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2012/rxGenScripts.png)

4.  En el cuadro de diálogo **Generate and Publish Scripts**, haga clic en **Set Scripting Options**. Puede omitir el paso **Choose Objects** porque el valor predeterminado es la base completa del script y todos los objetos de base de datos que desee.

5.  Haga clic en **Advanced**.

    ![Establecer opciones de scripting](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2012/rx11.png)

6.  En el cuadro de diálogo **Advanced Scripting Options**, desplácese a **Types of data to script** y haga clic en la opción **Data only** en la lista desplegable. (Consulte la imagen que aparece a continuación en el siguiente paso).

7.  Cambie **Script USE DATABASE** a **False**. Las instrucciones USE no son válidas para Base de datos SQL de Azure y no son necesarias para la implementación de SQL Server Express en el entorno de prueba.

    ![Establecer opciones de scripting](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2012/rxAdv.png)

8.  Haga clic en **OK**.
9.  En el cuadro de diálogo **Generate and Publish Scripts**, el cuadro **File name** especifica el lugar donde se creará el script. Cambie la ruta de la carpeta de solución (la carpeta que tenga el archivo *Contacts.sln*) y cambie el nombre de archivo a *aspnet-data-membership.sql*.
10. Haga clic en **Next** para ir a la pestaña **Summary** y, a continuación, haga clic en **Next** de nuevo para crear el script.

    ![Guardar o publicar](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2012/rx1.png)

11. Haga clic en **Finish**.

Implementación de la aplicación en Azure
----------------------------------------

1.  Abra el archivo *Web.config* de la raíz de la aplicación. Busque el marcador *DefaultConnection* y, a continuación, cópielo y péguelo debajo de la línea de marcador *DefaultConnection*. Cambie el nombre del elemento copiado *DefaultConnectionDeploy*. Necesitará esta cadena de conexión para implementar los datos de usuario en la base de datos de suscripciones.
    ![3 cadenas de conexión](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2012/rxd.png)

2.  Genere la aplicación.
3.  En Visual Studio, haga clic con el botón secundario en el proyecto, en el **Explorador de soluciones**, y seleccione **Publicar** en el menú contextual.

    ![Opción Publicar del menú contextual del proyecto](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2012/dntutmobile-deploy1-publish-001.png)

Se abre el asistente para **publicación web**.

1.  Haga clic en la pestaña **Settings**. Haga clic en el icono **v** para seleccionar la **Remote connection string** de **ContactManagerContext** y seleccione **DefaultConnectionDeploy**. Las tres bases de datos que aparecen usarán la misma cadena de conexión. La base de datos **ContactManagerContext** almacena los contactos, **DefaultConnectionDeploy** se usa solo para implementar los datos de cuenta de usuario en la base de datos de suscripciones y la base de datos **UsersContext** es la base de datos de suscripciones.

    ![configuración](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2012/rxD2.png)

2.  Debajo de **ContactManagerContext**, seleccione **Execute Code First Migrations**.

    ![configuración](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2012/rxSettings.png)

3.  En **DefaultConnectionDeploy**, seleccione **Update database** y, a continuación, haga clic en el vínculo **Configure database updates**.
4.  Haga clic en el vínculo **Add SQL Script** y diríjase al archivo *aspnet-data-membership.sql*. Solo tiene que hacer esto una vez. En la siguiente implementación, cancele la selección de **Update database**, puesto que no necesitará agregar los datos de usuario a las tablas de suscripciones.

    ![agregar SQL](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2012/rxAddSQL2.png)

5.  Haga clic en **Publicar**.
6.  Diríjase a la página [https://developers.facebook.com/apps](https://developers.facebook.com/apps/) y cambie la configuración de **App Domains** y **Site URL** a la dirección URL de Azure.
7.  Pruebe la aplicación. Compruebe que solo los usuarios con el rol *canEdit* puedan cambiar datos. Compruebe que los usuarios anónimos solo puedan ver la página principal. Compruebe que los usuarios autenticados puedan dirigirse a todos los vínculos que no cambian datos.
8.  La próxima vez que publique la aplicación, asegúrese de desactivar **Update database** en **DefaultConnectionDeploy**.

    ![configuración](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2012/rxSettings.png)

Actualización de la base de datosActualización de la base de datos de suscripciones
-----------------------------------------------------------------------------------

Una vez que se ha implementado el sitio en Azure y dispone de más usuarios registrados, es posible que desee que algunos sean miembros del rol *canEdit*. En esta sección usaremos Visual Studio para conectarse a la base de datos SQL y agregar usuarios al rol *canEdit*.

![configuración](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2012/rxSettings.png)

1.  En el **Explorador de soluciones**, haga clic con el botón secundario en el proyecto y haga clic en **Publicar**. 
    ![Publicar](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2012/rxP.png)

2.  Haga clic en la pestaña **Settings**.
3.  Copie la cadena de conexión. Por ejemplo, la cadena de conexión usada en este ejemplo es la siguiente: Data Source=tcp:d015leqjqx.database.windows.net,1433; Initial Catalog=ContactDB2;User Id=ricka0@d015lxyze;Password=xyzxyz
4.  Cierre el cuadro de diálogo de publicación.
5.  En el menú **Ver**, haga clic en **Explorador de servidores**.

6.  Haga clic en el icono **Conectar con base de datos**.

    ![Publicar](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2012/rxc.png)

7.  Si se le solicita el origen de datos, haga clic en **Microsoft SQL Server**. 
    ![Publicar](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2012/rx3.png)

8.  Copie y pegue el **nombre de servidor**, que comienza por *tcp* (consulte la imagen siguiente).
9.  Haga clic en **Use SQL Server Authentication**.
10. Especifique el **nombre de usuario** y la **contraseña**, que están conectadas con la cadena que copió.
11. Especifique el nombre de la base de datos (ContactDB o la cadena después de "Initial Catalog=" en la base de datos si no le puso el nombre de ContactDB). Si visualiza un cuadro de diálogo de error, consulte la siguiente sección.
12. Haga clic en **Probar conexión**. Si visualiza un cuadro de diálogo de error, consulte la siguiente sección. ![cuadro de diálogo de adición de conexión](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2012/rx4.png)

Error de inicio de sesión No se puede abrir el servidor
-------------------------------------------------------

Si obtiene un error que indica "No se puede abrir el servidor", tendrá que agregar la dirección IP a las IP permitidas.

![error de firewall](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2012/rx5.png)

1.  En el Portal de Azure, seleccione **Bases de datos SQL** en la pestaña de la izquierda.

    ![Seleccionar SQL](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2012/rx6.png)

2.  Seleccione la base de datos que desee abrir.

3.  Haga clic en el vínculo **Set up Azure firewall rules for this IP address**.

    ![reglas de firewall](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2012/rx7.png)

4.  Cuando aparezca el mensaje “The current IP address xxx.xxx.xxx.xxx is not included in existing firewall rules. Do you want to update the firewall rules?”, haga clic en **Yes**. Si la adición de esta dirección no es suficiente, tendrá que agregar un intervalo de direcciones IP.

Incorporación de un rango de direcciones IP permitidas
------------------------------------------------------

1.  En el Portal de Azure, haga clic en **Bases de datos SQL**.
2.  Haga clic en el **servidor** en el que se hospeda la base de datos.

    ![servidor de base de datos](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2012/rx8.png)

3.  Haga clic en **Configure** en la parte superior de la página.
4.  Agregue un nombre de regla y las direcciones IP de inicio y fin. 
    ![intervalo de IP](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2012/rx9.png)

5.  En la parte inferior de la página, haga clic en **Save**.
6.  Ahora puede editar la base de datos de suscripciones con los pasos mostrados anteriormente.

Pasos siguientesPasos siguientes
--------------------------------

Este tutorial y la aplicación de ejemplo fueron desarrollados por [Rick Anderson](http://blogs.msdn.com/b/rickandy/) (Twitter [@RickAndMSFT](https://twitter.com/RickAndMSFT)) con la colaboración de Tom Dykstra, Tom FitzMacken y Barry Dorrans (Twitter [@blowdart](https://twitter.com/blowdart)).

Es importante que haga comentarios acerca de lo que le gustó o lo que le gustaría que mejorásemos, no solo en relación al tutorial en sí sino a los productos sobre los que trata. Sus comentarios nos ayudarán a clasificar las mejoras por orden de prioridad. Estamos especialmente interesados en averiguar el interés que despierta una mayor automatización para el proceso de configurar e implementar la base de datos de suscripciones.

Para obtener botones de inicio de sesión coloridos de Facebook, Google y Yahoo, consulte la entrada de blog [Customizing External Login Buttons in ASP.NET MVC 4](http://www.beabigrockstar.com/customizing-external-login-buttons-in-asp-net-mvc-4/). Para obtener información sobre el uso de la autenticación de Windows, consulte lo siguiente:

-   [Azure Authentication](http://www.asp.net/vnext/overview/fall-2012-update/windows-azure-authentication)
-   [Cómo crear un sitio de intranet mediante ASP.NET MVC](http://msdn.microsoft.com/es-es/library/gg703322(v=vs.98).aspx)

Otra forma de almacenar datos en una aplicación de Azure consiste en utilizar el almacenamiento de Azure, que ofrece un almacenamiento de datos no relacional en forma de blobs y tablas. En los vínculos siguientes se proporciona más información sobre ASP.NET MVC y Azure.

-   [Aplicación .NET de niveles múltiples utilizando tablas, colas y blobs de almacenamiento](http://www.windowsazure.com/es-es/develop/net/tutorials/multi-tier-web-site/1-overview/)
-   [Intro to ASP.NET MVC 4](http://www.asp.net/mvc/tutorials/mvc-4/getting-started-with-aspnet-mvc4/intro-to-aspnet-mvc-4)
-   [Getting Started with Entity Framework using MVC](http://www.asp.net/mvc/tutorials/getting-started-with-ef-using-mvc/creating-an-entity-framework-data-model-for-an-asp-net-mvc-application)
-   [OAuth 2.0 and Sign-In](http://blogs.msdn.com/b/vbertocci/archive/2013/01/02/oauth-2-0-and-sign-in.aspx)

Ha aprendido a implementar una aplicación web en un sitio web de Azure. Para obtener más información acerca de cómo configurar, administrar y escalar sitios web Azure, consulte los temas de procedimiento en la página [Common Tasks](http://www.windowsazure.com/es-es/develop/net/common-tasks/).

Para saber cómo depurar sitios web Azure, consulte [Solución de problemas de Sitios web Azure en Visual Studio](/es-es/develop/net/tutorials/troubleshoot-web-sites-in-visual-studio/).

Para saber cómo implementar una aplicación en un Servicio en la nube de Azure, consulte la [versión del Servicio en la nube de este tutorial](http://www.windowsazure.com/es-es/develop/net/tutorials/cloud-service-with-sql-database/) (en inglés) y [Desarrollar aplicaciones web con Azure](http://msdn.microsoft.com/es-es/library/Hh674484). Entre las opciones para seleccionar la ejecución de la aplicación web ASP.NET en el Servicio en la nube de Azure en lugar del Sitio web de Azure se encuentran las siguientes:

-   Desea permisos de administrador en el servidor web que ejecuta la aplicación.
-   Desea usar la Conexión a escritorio remoto para obtener acceso al servidor web que ejecuta la aplicación.
-   La aplicación tiene varios niveles y desea distribuir el trabajo en los distintos servidores virtuales (web y trabajos).

Para obtener información acerca de Base de datos SQL y Almacenamiento de Azure, consulte [Data Storage Offerings on Azure](http://social.technet.microsoft.com/wiki/contents/articles/data-storage-offerings-on-the-windows-azure-platform.aspx).

Para obtener más información acerca de cómo usar Base de datos SQL, consulte [Trabajar con Azure SQL Database](http://go.microsoft.com/fwlink/p/?LinkId=282414#ssdb).

Para obtener más información acerca de Entity Framework y las migraciones de Code First, consulte los siguientes recursos:

-   [Getting Started with Entity Framework using MVC](http://www.asp.net/mvc/tutorials/getting-started-with-ef-using-mvc/creating-an-entity-framework-data-model-for-an-asp-net-mvc-application)
-   [Code First Migrations](http://msdn.microsoft.com/es-es/library/hh770484)

