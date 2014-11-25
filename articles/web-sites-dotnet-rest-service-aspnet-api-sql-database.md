<properties linkid="develop-dotnet-rest-service-using-web-api" urlDisplayName="REST service using Web API" pageTitle=".NET REST service using Web API - Azure tutorial" metaKeywords="Azure tutorial web site, ASP.NET API web site, Azure VS" description="A tutorial that teaches you how to deploy an app that uses the ASP.NET Web API to an Azure website by using Visual Studio." metaCanonical="" services="web-sites" documentationCenter=".NET" title="REST service using ASP.NET Web API and SQL Database" authors="riande" solutions="" manager="" editor="" />

<tags ms.service="web-sites" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="dotnet" ms.topic="article" ms.date="01/01/1900" ms.author="riande" />

# Servicio REST mediante ASP.NET Web API y Base de datos SQL

***Por [Rick Anderson][Rick Anderson] y Tom Dykstra. Actualización de marzo de 2014.***

En este tutorial se muestra cómo implementar una aplicación web ASP.NET en un sitio web de Azure utilizando el Asistente para publicación web de Visual Studio 2013 o Visual Studio 2013 Express para Web. (Si prefiere utilizar Visual Studio 2012, consulte la [versión anterior de este tutorial][versión anterior de este tutorial]).

Puede abrir una cuenta de Azure de manera gratuita y, si todavía no tiene Visual Studio 2013, el SDK instala automáticamente Visual Studio 2013 Express para Web. De este modo, puede empezar a desarrollar contenido para Azure sin coste alguno.

En este tutorial se supone que no tiene ninguna experiencia previa con Azure. Cuando acabe, tendrá una sencilla aplicación web que se ejecutará en la nube.

Aprenderá a realizar los siguientes procedimientos:

-   Habilitar su equipo para desarrollar contenido de Azure mediante la instalación del SDK de Azure
-   Crear un proyecto ASP.NET MVC 5 de Visual Studio y publicarlo en un sitio web de Azure
-   Utilizar ASP.NET Web API para habilitar llamadas de API de RESTful
-   Utilizar una Base de datos SQL para almacenar datos en Azure
-   Publicar actualizaciones de la aplicación en Azure

Va a desarrollar una aplicación web de lista de contactos sencilla basada en ASP.NET MVC 5 y que utiliza ADO.NET Entity Framework para obtener acceso a la base de datos. La siguiente ilustración muestra la aplicación completada:

![captura de pantalla del sitio web][captura de pantalla del sitio web]
 Apartados de este tutorial:

-   [Configuración del entorno de desarrollo][Configuración del entorno de desarrollo]
-   [Configuración del entorno de Azure][Configuración del entorno de Azure]
-   [Creación de una aplicación ASP.NET MVC 5][Creación de una aplicación ASP.NET MVC 5]
-   [Implementación de la aplicación en Azure][Implementación de la aplicación en Azure]
-   [Incorporación de una base de datos a la aplicación][Incorporación de una base de datos a la aplicación]
-   [Incorporación de un controlador y una vista para los datos][Incorporación de un controlador y una vista para los datos]
-   [Incorporación de una interfaz de RESTful de Web API][Incorporación de una interfaz de RESTful de Web API]
-   [Incorporación de protección de XSRF][Incorporación de protección de XSRF]
-   [Publicación de la actualización de la aplicación en Azure y Base de datos SQL][Publicación de la actualización de la aplicación en Azure y Base de datos SQL]

<a name="bkmk_setupdevenv"></a>
<!-- the next line produces the "Set up the development environment" section as see at http://www.windowsazure.com/es-es/documentation/articles/web-sites-dotnet-get-started/ -->
 [WACOM.INCLUDE [create-account-and-websites-note](../includes/create-account-and-websites-note.md)]

## <a name="bkmk_setupwindowsazure"></a>Configuración del entorno de Azure

A continuación, configure el entorno de Azure creando un sitio web de Azure y una Base de datos SQL.

### Creación de un sitio web y una base de datos SQL en Azure

El paso siguiente consiste en crear el sitio web de Azure y la base de datos SQL que utilizará la aplicación.

El sitio web de Azure se ejecutará en un entorno de hospedaje compartido, es decir, en máquinas virtuales compartidas con otros clientes de Azure. Los entornos de hospedaje compartidos ofrecen un coste reducido a los nuevos usuarios de servicios en la nube. Más adelante, si el tráfico web aumenta, es posible escalar la aplicación para atender la demanda ejecutándola en máquinas virtuales dedicadas. Si necesita una arquitectura más compleja, puede migrar la aplicación a un Servicio en la nube de Azure. Los servicios en la nube se ejecutan en MV dedicadas que se pueden configurar según sus necesidades.

La Base de datos SQL es un servicio de base de datos relacional en la nube que se basa en la tecnología de SQL Server. Las herramientas y aplicaciones que funcionan con SQL Server también funcionan con la Base de datos SQL.

1.  En el [Portal de administración de Azure][Portal de administración de Azure], haga clic en **Sitios web** en el recuadro izquierdo y elija **Nuevo**.

2.  Haga clic en **CUSTOM CREATE**.

    ![Vínculo Create with Database del Portal de administración][Vínculo Create with Database del Portal de administración]

    Se abre el asistente para **Nuevo sitio web - Creación personalizada**.

3.  En el paso **Nuevo sitio web** del asistente, escriba una cadena en el cuadro **URL** para usarla como dirección URL única de la aplicación. La URL completa consistirá en el valor que escriba más el sufijo que aparece debajo del cuadro de texto. La ilustración muestra "contactmgr11", pero dicha URL probablemente ya esté en uso, por lo que tendrá que elegir otra distinta.

4.  En la lista desplegable **Región**, elija la región más cercana.

5.  En la lista desplegable **Base de datos**, elija **Crear una base de datos SQL de 20 MB gratuita**.

    ![Paso de creación de un nuevo sitio web del asistente Nuevo sitio web - Crear con base de datos][Paso de creación de un nuevo sitio web del asistente Nuevo sitio web - Crear con base de datos]

6.  Haga clic en la flecha derecha situada en la parte inferior del cuadro.

    El asistente avanza hasta el paso **Database Settings**.

7.  En el cuadro **Nombre**, escriba *ContactDB*.

8.  En el cuadro **Servidor**, seleccione **Nuevo servidor de bases de datos SQL** Si anteriormente creó una base de datos de SQL Server, también tiene la opción de seleccionar ese SQL Server en el control desplegable.

9.  Haga clic en la flecha derecha situada en la parte inferior del cuadro.

10. Complete los campos **LOGIN NAME** y **PASSWORD** con los datos de un administrador. Si seleccionó **New SQL Database server**, no debe escribir un nombre y una contraseña existentes en estos campos, sino definir unos nuevos que volverá a utilizar más adelante para obtener acceso a la base de datos. Si seleccionó un SQL Server creado anteriormente, se le pedirá la contraseña de la cuenta de SQL Server que abrió para crearlo. En este tutorial, no seleccionaremos la casilla **Advanced**. La casilla **Avanzada** permite establecer el tamaño de la base de datos (el valor predeterminado es 1 GB, pero puede aumentarlo hasta 150 GB) y la intercalación.

11. Haga clic en la marca de verificación que aparece en la parte inferior del cuadro para indicar que finalizó la configuración.

    ![Paso Configuración de base de datos del asistente Nuevo sitio web - Crear con base de datos][Paso Configuración de base de datos del asistente Nuevo sitio web - Crear con base de datos]

    La imagen siguiente muestra cómo usar un SQL Server y datos de inicio de sesión existentes.

    ![Paso Configuración de base de datos del asistente Nuevo sitio web - Crear con base de datos][1]

    El Portal de administración vuelve a la página Sitios web y la columna **Estado** indica que se está creando el sitio. Después de unos segundos, la columna **Status** indica que el sitio se creó correctamente. En la barra de navegación situada a la izquierda, el número de sitios que tiene en su cuenta aparece junto al icono **Sitios web** y el número de bases de datos aparece junto al icono **Bases de datos SQL** .

<!-- [Websites page of Management Portal, website created][setup009] -->

## <a name="bkmk_createmvc4app"></a>Creación de una aplicación ASP.NET MVC 5

Ya ha creado un sitio web de Azure, pero todavía no hay contenido en él. El siguiente paso consiste en crear el proyecto de aplicación web de Visual Studio que luego publicará en Azure.

### Creación del proyecto

1.  Inicie Visual Studio 2013.
2.  En el menú **Archivo**, haga clic en **Nuevo proyecto**.
3.  En el cuadro de diálogo **Nuevo proyecto**, expanda **Visual C#**, seleccione **Web** y, a continuación, seleccione **ASP.NET MVC 5 Web Application**. Mantenga el valor **.NET Framework 4.5** predeterminado. Póngale a la aplicación el nombre **ContactManager** y haga clic en **Aceptar**.
    ![Cuadro de diálogo Nuevo proyecto][Cuadro de diálogo Nuevo proyecto]]
4.  En el cuadro de diálogo **New ASP.NET Project**, seleccione la plantilla **MVC**, active **Web API** y, a continuación, haga clic en **Change Authentication**.

    ![Cuadro de diálogo New ASP.NET Project][Cuadro de diálogo New ASP.NET Project]

5.  En el cuadro de diálogo **Change Authentication**, haga clic en **Sin autenticación** y, a continuación, en **Aceptar**.

    ![Sin autenticación][Sin autenticación]

    La aplicación de ejemplo que está creando no incluirá características que requieran que los usuarios inicien sesión. Para obtener información acerca de cómo implementar la autenticación y las características de autenticación, consulte la sección [Pasos siguientes][Pasos siguientes] al final de este tutorial.

6.  En el cuadro de diálogo **New ASP.NET Project**, haga clic en **Aceptar**.

    ![Cuadro de diálogo New ASP.NET Project][2]

### Establecimiento del encabezado y pie de página

1.  En el **Explorador de soluciones**, expanda la carpeta *Views\\Shared* y abra el archivo \*\_Layout.cshtml\*.

    ![\_Layout.cshtml en el Explorador de soluciones][\_Layout.cshtml en el Explorador de soluciones]

2.  Reemplace el contenido del archivo \*\_Layout.cshtml\* por el código siguiente:

        <!DOCTYPE html>
        <html lang="en">
        <head>
            <meta charset="utf-8" />
            <title>@ViewBag.Title - Contact Manager</title>
            <link href="~/favicon.ico" rel="shortcut icon" type="image/x-icon" />
            <meta name="viewport" content="width=device-width" />
            @Styles.Render("~/Content/css")
            @Scripts.Render("~/bundles/modernizr")
        </head>
        <body>
            <header>
                <div class="content-wrapper">
                    <div class="float-left">
                        <p class="site-title">@Html.ActionLink("Contact Manager", "Index", "Home")</p>
                    </div>
                </div>
            </header>
            <div id="body">
                @RenderSection("featured", required: false)
                <section class="content-wrapper main-content clear-fix">
                    @RenderBody()
                </section>
            </div>
            <footer>
                <div class="content-wrapper">
                    <div class="float-left">
                        <p>&copy; @DateTime.Now.Year - Contact Manager</p>
                    </div>
                </div>
            </footer>
            @Scripts.Render("~/bundles/jquery")
            @RenderSection("scripts", required: false)
        </body>
        </html>

El código anterior cambia el nombre de la aplicación "My ASP.NET App" a "Contact Manager" y quita los vínculos de **Home**, **About** y **Contact**.

### Ejecución de la aplicación de forma local

1.  Presione CTRL+F5 para ejecutar la aplicación.
    Aparece la página principal de la aplicación en el explorador predeterminado.
    ![Página principal de lista de tareas pendientes][Página principal de lista de tareas pendientes]

Esto es todo lo que necesita hacer por ahora para crear la aplicación que va a implementar en Azure. Más adelante agregará la funcionalidad de base de datos.

## <a name="bkmk_deploytowindowsazure1"></a>Implementación de la aplicación en Azure

1.  En Visual Studio, haga clic con el botón secundario en el proyecto, en el **Explorador de soluciones**, y seleccione **Publicar** en el menú contextual.

    ![Opción Publicar del menú contextual del proyecto][Opción Publicar del menú contextual del proyecto]

    Se abre el asistente para **publicación web**.

2.  En la pestaña **Perfil** del asistente para **publicación web**, haga clic en **Importar**.

    ![Importar configuración de publicación][Importar configuración de publicación]

    Aparecerá el cuadro de diálogo **Import Publish Profile**.

3.  Seleccione Import from an Azure Web Site. Primero debe iniciar sesión si todavía no lo ha hecho. Haga clic en **Iniciar sesión**. Escriba el usuario asociado a la suscripción y siga los pasos para iniciar sesión.

    ![iniciar sesión][iniciar sesión]

    Seleccione el sitio web en la lista desplegable y, a continuación, haga clic en **OK**.

    ![Importar perfil de publicación][Importar perfil de publicación]

4.  En la pestaña **Conexión**, haga clic en **Validate Connection** para asegurarse de que la configuración es correcta.

    ![Validar conexión][Validar conexión]

5.  Una vez que se valide la conexión, aparecerá una marca de verificación verde junto al botón **Validate Connection**.

    ![Icono de conexión correcta y botón Next en la pestaña Conexión][Icono de conexión correcta y botón Next en la pestaña Conexión]

6.  Haga clic en **Next**.

    ![Pestaña Settings][Pestaña Settings]

    Puede aceptar la configuración predeterminada de esta pestaña. Está implementando una configuración de compilación de versión y no es necesario eliminar archivos del servidor de destino, precompilar la aplicación ni excluir archivos de la carpeta App\_Data. Si desea depurar en el sitio Azure activo, tendrá que implementar una configuración de depuración (no una versión). Consulte la sección [Pasos siguientes][Pasos siguientes] al final de este tutorial.

7.  En la pestaña **Preview**, haga clic en **Start Preview**.

    En esta pestaña aparece la lista de archivos que se copiarán en el servidor. Para publicar la aplicación no es necesario mostrar la vista previa, pero se trata de una función útil que conviene conocer. En este caso, no tiene que hacer nada con la lista de archivos que se muestra. La próxima vez que publique, solo estarán en la lista de vista previa los archivos que se han cambiado.

    ![Botón StartPreview de la pestaña Preview][Botón StartPreview de la pestaña Preview]

8.  Haga clic en **Publicar**.

    Visual Studio comienza el proceso de copiar los archivos en el servidor de Azure. La ventana **Output** muestra qué acciones de implementación se realizaron e informa de la correcta finalización de la implementación.

9.  El explorador predeterminado se dirige automáticamente a la URL del sitio web implementado.

    La aplicación creada ahora se ejecuta en la nube.

    ![Página de inicio de tareas pendientes ejecutándose en Azure][Página de inicio de tareas pendientes ejecutándose en Azure]

## <a name="bkmk_addadatabase"></a>Incorporación de una base de datos a la aplicación

A continuación, actualizará la aplicación MVC incorporando funciones que permitan mostrar y actualizar los contactos y almacenar los datos en una base de datos. La aplicación utilizará Entity Framework para crear la base de datos y para leer y actualizar los datos que esta contiene.

### Incorporación de clases de modelos de datos para los contactos

Se empieza por crear un modelo de datos sencillo en código.

1.  En el **Explorador de soluciones**, haga clic con el botón secundario en la carpeta Modelos, a continuación en **Add** y, por último, en **Class**.

    ![Agregar clase en el menú contextual de la carpeta Models][Agregar clase en el menú contextual de la carpeta Models]

2.  En el cuadro de diálogo **Add New Item**, ponga al archivo de la nueva clase el nombre *Contact.cs* y, a continuación, haga clic en **Add**.

    ![Cuadro de diálogo Add New Item][Cuadro de diálogo Add New Item]

3.  Reemplace el contenido del archivo Contacts.cs por el código siguiente.

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
                public string Email { get; set; }
                public string Twitter { get; set; }
                public string Self
                {
                    get { return string.Format(CultureInfo.CurrentCulture,
                         "api/contacts/{0}", this.ContactId); }
                    set { }
                }
            }
        }

La clase **Contacts** define qué datos de los contactos va a almacenar, además de una clave primaria, ContactID, necesaria para la base de datos. Puede obtener más información sobre los modelos de datos en la sección [Pasos siguientes][Pasos siguientes] al final de este tutorial.

### Creación de páginas web que permiten a los usuarios de aplicaciones utilizar los contactos

La característica de scaffolding de ASP.NET MVC puede generar automáticamente código que realiza acciones de creación, lectura, actualización y eliminación (CRUD).

## <a name="bkmk_addcontroller"></a>Incorporación de un controlador y una vista para los datos

1.  En el **Explorador de soluciones**, expanda la carpeta Controllers.

2.  Cree el proyecto **(Ctrl+Mayús+B)**. (Debe crear el proyecto antes de usar el mecanismo de scaffolding).

3.  Haga clic con el botón secundario en la carpeta Controllers, a continuación en **Add** y, por último, en **Controller**.

    ![Agregar controlador en el menú contextual de la carpeta Controllers][Agregar controlador en el menú contextual de la carpeta Controllers]

4.  En el cuadro de diálogo **Add Scaffold**, seleccione **MVC Controller with views, using Entity Framework** y, a continuación, haga clic en **Add**.

![Agregar controlador][Agregar controlador]

1.  Asigne al controlador el nombre de **HomeController**. Seleccione **Contact** como su clase de modelo. Haga clic en el botón **New data context** y acepte el valor predeterminado de "ContactManager.Models.ContactManagerContext" para **New data context type**. Haga clic en **Add**.

    ![Cuadro de diálogo Agregar controlador][Cuadro de diálogo Agregar controlador]

    Un cuadro de diálogo indicará: "A file with the name HomeController already exits. Do you want to replace it?". Haga clic en **Yes**. Estamos sobrescribiendo el controlador de inicio que se creó con el nuevo proyecto. Utilizaremos el nuevo controlador de inicio para nuestra lista de contactos.

    Visual Studio crea métodos y vistas de controlador para las operaciones CRUD de base de datos que afectan a los objetos **Contact**.

## Habilitación de las migraciones, creación de la base de datos e incorporación de datos de ejemplo y un inicializador de datos

La siguiente tarea consiste en habilitar la función [Migraciones de Code First][Migraciones de Code First] para crear la base de datos a partir del modelo de datos ya establecido.

1.  En el menú **Tools**, seleccione **Library Package Manager** y, a continuación **Package Manager Console**.

    ![Package Manager Console en el menú Herramientas][Package Manager Console en el menú Herramientas]

2.  En la ventana **Package Manager Console**, escriba el comando siguiente:

        enable-migrations 

    El comando **enable-migrations** crea una carpeta *Migrations* y guarda en ella un archivo *Configuration.cs* que puede editar para configurar las migraciones.

3.  En la ventana **Package Manager Console**, escriba el comando siguiente:

        add-migration Initial

    El comando **add-migration Initial** genera una clase denominada **\<date\_stamp\>Initial** que crea la base de datos. El primer parámetro (*Initial*) es arbitrario y se utiliza para crear el nombre del archivo. Puede ver los archivos de las nuevas clases en el **Explorador de soluciones**.

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
                   Name = "Debra Garcia",
                   Address = "1234 Main St",
                   City = "Redmond",
                   State = "WA",
                   Zip = "10999",
                   Email = "debra@example.com",
                   Twitter = "debra_example"
               },
                new Contact
                {
                    Name = "Thorsten Weinrich",
                    Address = "5678 1st Ave W",
                    City = "Redmond",
                    State = "WA",
                    Zip = "10999",
                    Email = "thorsten@example.com",
                    Twitter = "thorsten_example"
                },
                new Contact
                {
                    Name = "Yuhong Li",
                    Address = "9012 State st",
                    City = "Redmond",
                    State = "WA",
                    Zip = "10999",
                    Email = "yuhong@example.com",
                    Twitter = "yuhong_example"
                },
                new Contact
                {
                    Name = "Jon Orton",
                    Address = "3456 Maple St",
                    City = "Redmond",
                    State = "WA",
                    Zip = "10999",
                    Email = "jon@example.com",
                    Twitter = "jon_example"
                },
                new Contact
                {
                    Name = "Diliana Alexieva-Bosseva",
                    Address = "7890 2nd Ave E",
                    City = "Redmond",
                    State = "WA",
                    Zip = "10999",
                    Email = "diliana@example.com",
                    Twitter = "diliana_example"
                }
                );
        }

    El código anterior inicializará la base de datos con la información de contacto. Para obtener más información acerca de la inicialización de la base de datos, consulte [Debugging Entity Framework (EF) DBs][Debugging Entity Framework (EF) DBs].

7.  En **Package Manager Console**, escriba el comando:

        update-database

    ![Comandos de Package Manager Console][Comandos de Package Manager Console]

    El comando **update-database** ejecuta la primera migración, lo que crea la base de datos. De manera predeterminada, la base de datos que se crea es una base de datos LocalDB de SQL Server Express.

8.  Presione CTRL+F5 para ejecutar la aplicación.

La aplicación muestra los datos de inicialización y ofrece enlaces de edición, detalles y eliminación.

![Vista MVC de los datos][Vista MVC de los datos]

## <a name="bkmk_addview"></a>Edición de la vista

1.  Abra el archivo *Views\\Home\\Index.cshtml*. En el paso siguiente, reemplazaremos el marcado generado por código que utiliza [jQuery][jQuery] y [Knockout.js][Knockout.js]. Este nuevo código recupera la lista de contactos de la API web y JSON y después vincula los datos de contacto a la interfaz de usuario mediante knockout.js. Para obtener más información, consulte la sección [Pasos siguientes][Pasos siguientes] al final de este tutorial.

2.  Reemplace el contenido del archivo por el código siguiente.

        @model IEnumerable<ContactManager.Models.Contact>
        @{
            ViewBag.Title = "Home";
        }
        @section Scripts {
            @Scripts.Render("~/bundles/knockout")
            <script type="text/javascript">
                function ContactsViewModel() {
                    var self = this;
                    self.contacts = ko.observableArray([]);
                    self.addContact = function () {
                        $.post("api/contacts",
                            $("#addContact").serialize(),
                            function (value) {
                                self.contacts.push(value);
                            },
                            "json");
                    }
                    self.removeContact = function (contact) {
                        $.ajax({
                            type: "DELETE",
                            url: contact.Self,
                            success: function () {
                                self.contacts.remove(contact);
                            }
                        });
                    }

                    $.getJSON("api/contacts", function (data) {
                        self.contacts(data);
                    });
                }
                ko.applyBindings(new ContactsViewModel());  
        </script>
        }
        <ul id="contacts" data-bind="foreach: contacts">
            <li class="ui-widget-content ui-corner-all">
                <h1 data-bind="text: Name" class="ui-widget-header"></h1>
                <div><span data-bind="text: $data.Address || 'Address?'"></span></div>
                <div>
                    <span data-bind="text: $data.City || 'City?'"></span>,
                    <span data-bind="text: $data.State || 'State?'"></span>
                    <span data-bind="text: $data.Zip || 'Zip?'"></span>
                </div>
                <div data-bind="if: $data.Email"><a data-bind="attr: { href: 'mailto:' + Email }, text: Email"></a></div>
                <div data-bind="ifnot: $data.Email"><span>Email?</span></div>
                <div data-bind="if: $data.Twitter"><a data-bind="attr: { href: 'http://twitter.com/' + Twitter }, text: '@@' + Twitter"></a></div>
                <div data-bind="ifnot: $data.Twitter"><span>Twitter?</span></div>
                <p><a data-bind="attr: { href: Self }, click: $root.removeContact" class="removeContact ui-state-default ui-corner-all">Remove</a></p>
            </li>
        </ul>
        <form id="addContact" data-bind="submit: addContact">
            <fieldset>
                <legend>Add New Contact</legend>
                <ol>
                    <li>
                        <label for="Name">Name</label>
                        <input type="text" name="Name" />
                    </li>
                    <li>
                        <label for="Address">Address</label>
                        <input type="text" name="Address" >
                    </li>
                    <li>
                        <label for="City">City</label>
                        <input type="text" name="City" />
                    </li>
                    <li>
                        <label for="State">State</label>
                        <input type="text" name="State" />
                    </li>
                    <li>
                        <label for="Zip">Zip</label>
                        <input type="text" name="Zip" />
                    </li>
                    <li>
                        <label for="Email">E-mail</label>
                        <input type="text" name="Email" />
                    </li>
                    <li>
                        <label for="Twitter">Twitter</label>
                        <input type="text" name="Twitter" />
                    </li>
                </ol>
                <input type="submit" value="Add" />
            </fieldset>
        </form>

3.  Haga clic con el botón secundario en la carpeta Content, a continuación en **Add** y, por último, en **New Item...**.

    ![Incorporación de una hoja de estilo en el menú contextual de la carpeta Content][Incorporación de una hoja de estilo en el menú contextual de la carpeta Content]

4.  En el cuadro de diálogo **Add New Item**, introduzca **Style** en el cuadro de búsqueda superior derecho y, a continuación, seleccione **Style Sheet**
    ![Cuadro de diálogo Add New Item][3].

5.  Asigne al archivo el nombre *Contacts.css* y haga clic en **Add**. Reemplace el contenido del archivo por el código siguiente.

        .column {
            float: left;
            width: 50%;
            padding: 0;
            margin: 5px 0;
        }
        form ol {
            list-style-type: none;
            padding: 0;
            margin: 0;
        }
        form li {
            padding: 1px;
            margin: 3px;
        }
        form input[type="text"] {
            width: 100%;
        }
        #addContact {
            width: 300px;
            float: left;
            width:30%;
        }
        #contacts {
            list-style-type: none;
            margin: 0;
            padding: 0;
            float:left;
            width: 70%;
        }
        #contacts li {
            margin: 3px 3px 3px 0;
            padding: 1px;
            float: left;
            width: 300px;
            text-align: center;
            background-image: none;
            background-color: #F5F5F5;
        }
        #contacts li h1
        {
            padding: 0;
            margin: 0;
            background-image: none;
            background-color: Orange;
            color: White;
            font-family: Trebuchet MS, Tahoma, Verdana, Arial, sans-serif;
        }
        .removeContact, .viewImage
        {
            padding: 3px;
            text-decoration: none;
        }

    Utilizaremos la hoja de estilos para el diseño, los colores y el estilo utilizados en la aplicación del administrador de contactos.

6.  Abra el archivo *App\_Start\\BundleConfig.cs*.

7.  Agregue el código siguiente para registrar el complemento [Knockout][Knockout].

        bundles.Add(new ScriptBundle("~/bundles/knockout").Include(
                    "~/Scripts/knockout-{version}.js"));

    Este ejemplo utiliza knockout para simplificar el código JavaScript dinámico que trata las plantillas de la pantalla.

8.  Modifique la entrada contents/css para registrar la hoja de estilos *contacts.css*. Cambie la línea siguiente:

                 bundles.Add(new StyleBundle("~/Content/css").Include(
                   "~/Content/bootstrap.css",
                   "~/Content/site.css"));

    Por:

        bundles.Add(new StyleBundle("~/Content/css").Include(
                   "~/Content/bootstrap.css",
                   "~/Content/contacts.css",
                   "~/Content/site.css"));

9.  En Package Manager Console, ejecute el comando siguiente para instalar Knockout.

    Install-Package knockoutjs

## <a name="bkmk_addwebapi"></a>Incorporación de un controlador para la interfaz Restful de Web API

1.  En el **Explorador de soluciones**, haga clic con el botón secundario en Controllers, después en **Add** y, por último, en **Controller...**.

2.  En el cuadro de diálogo **Add Scaffold**, introduzca **Web API 2 Controller with actions, using Entity Framework** y, a continuación, haga clic en **Add**.

    ![Incorporación de un controlador API][Incorporación de un controlador API]

3.  En el cuadro de diálogo **Add Controller**, introduzca "ContactsController" como su nombre de controlador. Seleccione "Contact (ContactManager.Models)" para la opción **Model class**. Mantenga el valor predeterminado para **Data context class**.

4.  Haga clic en **Add**.

### Ejecución de la aplicación de forma local

1.  Presione CTRL+F5 para ejecutar la aplicación.

    ![Página de índice][captura de pantalla del sitio web]

2.  Introduzca un contacto y haga clic en **Add**. La aplicación regresa a la página de inicio y muestra el contacto que ha introducido.

    ![Página de índice con elementos de lista de tareas pendientes][Página de índice con elementos de lista de tareas pendientes]

3.  En el explorador, anexe **/api/contacts** a la dirección URL.

    La URL resultante se parecerá a <http://localhost:1234/api/contacts>. La API web de RESTful que ha agregado devuelve los contactos almacenados. Firefox y Chrome mostrarán los datos en formato XML.

    ![Página de índice con elementos de lista de tareas pendientes][4]

    Internet Explorer le solicitará que abra o guarde los contactos.

    ![Cuadro de diálogo de almacenamiento de la API web.][Cuadro de diálogo de almacenamiento de la API web.]

    Puede abrir los contactos devueltos con el Bloc de notas o en un explorador.

    Este resultado lo puede consumir otra aplicación como una página web móvil o aplicación.

    ![Cuadro de diálogo de almacenamiento de la API web.][5]

    **Advertencia de seguridad**: En este momento, la aplicación es insegura y vulnerable frente un ataque CSRF. Más adelante en el tutorial eliminaremos esta vulnerabilidad. Para obtener más información, consulte [Preventing Cross-Site Request Forgery (CSRF) Attacks][Preventing Cross-Site Request Forgery (CSRF) Attacks].

## <a name="xsrf"></a><span class="short-header">XSRF</span>Incorporación de protección de XSRF

La falsificación de solicitud entre sitios (también conocida como XSRF o CSRF) es un ataque contra aplicaciones hospedadas en web, en donde un sitio web malintencionado puede influir en la interacción entre un explorador cliente y un sitio web de confianza de ese explorador. Estos ataques son posibles porque los exploradores web enviarán tokens de autenticación automáticamente con cada solicitud a un sitio web. El ejemplo canónico es una cookie de autenticación, como el vale de autenticación de formularios de ASP.NET. Sin embargo, los sitios web que utilicen cualquier mecanismo de autenticación persistente (como Autenticación de Windows, Basic, entre otras) podrían ser objetivos de esos ataques.

Un ataque XSRF es distinto de un ataque de suplantación de identidad (phishing). Los ataques de suplantación de identidad requieren interacción de la víctima. En un ataque de suplantación de identidad (phishing), un sitio web malintencionado imitará el sitio web de destino y engañará a la víctima para que proporcione información confidencial al atacante. En un ataque XSRF, con frecuencia no hay interacción necesaria de la víctima. Por el contrario, el atacante confía en el explorador enviando automáticamente todas las cookies relevantes al sitio web de destino.

Para obtener más información, consulte [Open Web Application Security Project][Open Web Application Security Project] (OWASP) [XSRF][XSRF].

1.  En el **Explorador de soluciones**, haga clic con el botón secundario en el proyecto **ContactManager**, a continuación en **Add** y, por último, en **Class**.

2.  Asigne al archivo el nombre *ValidateHttpAntiForgeryTokenAttribute.cs* y agregue el código siguiente:

        using System;
        using System.Collections.Generic;
        using System.Linq;
        using System.Net;
        using System.Net.Http;
        using System.Web.Helpers;
        using System.Web.Http.Controllers;
        using System.Web.Http.Filters;
        using System.Web.Mvc;
        namespace ContactManager.Filters
        {
            public class ValidateHttpAntiForgeryTokenAttribute : AuthorizationFilterAttribute
            {
                public override void OnAuthorization(HttpActionContext actionContext)
                {
                    HttpRequestMessage request = actionContext.ControllerContext.Request;
                    try
                    {
                        if (IsAjaxRequest(request))
                        {
                            ValidateRequestHeader(request);
                        }
                        else
                        {
                            AntiForgery.Validate();
                        }
                    }
                    catch (HttpAntiForgeryException e)
                    {
                        actionContext.Response = request.CreateErrorResponse(HttpStatusCode.Forbidden, e);
                    }
                }
                private bool IsAjaxRequest(HttpRequestMessage request)
                {
                    IEnumerable<string> xRequestedWithHeaders;
                    if (request.Headers.TryGetValues("X-Requested-With", out xRequestedWithHeaders))
                    {
                        string headerValue = xRequestedWithHeaders.FirstOrDefault();
                        if (!String.IsNullOrEmpty(headerValue))
                        {
                            return String.Equals(headerValue, "XMLHttpRequest", StringComparison.OrdinalIgnoreCase);
                        }
                    }
                    return false;
                }
                private void ValidateRequestHeader(HttpRequestMessage request)
                {
                    string cookieToken = String.Empty;
                    string formToken = String.Empty;
                    IEnumerable<string> tokenHeaders;
                    if (request.Headers.TryGetValues("RequestVerificationToken", out tokenHeaders))
                    {
                        string tokenValue = tokenHeaders.FirstOrDefault();
                        if (!String.IsNullOrEmpty(tokenValue))
                        {
                            string[] tokens = tokenValue.Split(':');
                            if (tokens.Length == 2)
                            {
                                cookieToken = tokens[0].Trim();
                                formToken = tokens[1].Trim();
                            }
                        }
                    }
                    AntiForgery.Validate(cookieToken, formToken);
                }
            }
        }

3.  Agregue la siguiente instrucción *using* al controlador de contratos para que tenga acceso al atributo **[ValidateHttpAntiForgeryToken]**.

    using ContactManager.Filters;

4.  Agregue el atributo **[ValidateHttpAntiForgeryToken]** a los métodos Post de **ContactsController** para protegerlo contra amenazas XSRF. Lo agregará a los métodos de acciones "PutContact", "PostContact" y **DeleteContact**.

    [ValidateHttpAntiForgeryToken]
     public IHttpActionResult PutContact(int id, Contact contact)
     {

5.  Actualice la sección *Scripts* del archivo *Views\\Home\\Index.cshtml* para incluir código y obtener los tokens de XSRF.

         @section Scripts {
            @Scripts.Render("~/bundles/knockout")
            <script type="text/javascript">
                @functions{
                   public string TokenHeaderValue()
                   {
                      string cookieToken, formToken;
                      AntiForgery.GetTokens(null, out cookieToken, out formToken);
                      return cookieToken + ":" + formToken;                
                   }
                }

               function ContactsViewModel() {
                  var self = this;
                  self.contacts = ko.observableArray([]);
                  self.addContact = function () {

                     $.ajax({
                        type: "post",
                        url: "api/contacts",
                        data: $("#addContact").serialize(),
                        dataType: "json",
                        success: function (value) {
                           self.contacts.push(value);
                        },
                        headers: {
                           'RequestVerificationToken': '@TokenHeaderValue()'
                        }
                     });

                  }
                  self.removeContact = function (contact) {
                     $.ajax({
                        type: "DELETE",
                        url: contact.Self,
                        success: function () {
                           self.contacts.remove(contact);
                        },
                        headers: {
                           'RequestVerificationToken': '@TokenHeaderValue()'
                        }

                     });
                  }

                  $.getJSON("api/contacts", function (data) {
                     self.contacts(data);
                  });
               }
               ko.applyBindings(new ContactsViewModel());
            </script>

## <a name="bkmk_deploydatabaseupdate"></a>Publicación de la actualización de la aplicación en Azure y Base de datos SQL

Para publicar la aplicación, repita el procedimiento que ha realizado anteriormente.

1.  En el **Explorador de soluciones**, haga clic con el botón secundario en el proyecto y seleccione **Publicar**.

    ![Publicar][Publicar]

2.  Haga clic en la pestaña **Settings**.

3.  En **ContactsManagerContext(ContactsManagerContext)**, haga clic en el icono **v** para cambiar la *Remote connection string* de la cadena de conexión para la base de datos del contacto. Haga clic en **ContactDB**.

    ![Configuración][Configuración]

4.  Active la casilla para **Execute Code First Migrations (runs on application start)**.

5.  Haga clic en **Next** y, a continuación, haga clic en **Preview**. Visual Studio muestra una lista de los archivos que se agregarán o actualizarán.

6.  Haga clic en **Publicar**.
    Una vez finalizada la implementación, el explorador se abre por la página de inicio de la aplicación.

    ![Página de índice sin contactos][captura de pantalla del sitio web]

    El proceso de publicación de Visual Studio configura automáticamente la cadena de conexión en el archivo *Web.config* implementado para apuntar a la base de datos SQL. También ha configurado las migraciones de Code First para actualizar automáticamente la base de datos a la versión más reciente la primera vez que la aplicación tiene acceso a la base de datos tras la implementación.

    Como resultado de esta configuración, Code First crea la base de datos mediante la ejecución del código en la clase **Initial** que ha creado anteriormente. Lo realizó la primera vez que la aplicación intentó obtener acceso a la base de datos tras la implementación.

7.  Introduzca un contacto de la misma forma que hizo al ejecutar la aplicación localmente, para comprobar que la implementación de la base de datos se ha realizado correctamente.

Cuando ve que el elemento introducido se guarda y aparece en la página del administrador de contactos, sabe que se ha almacenado en la base de datos.

![Página de índice con contactos][Página de índice con elementos de lista de tareas pendientes]

La aplicación ahora se está ejecutando en la nube, utilizando Base de datos SQL para almacenar sus datos. Después de finalizar la prueba de la aplicación en Azure, elimínela. La aplicación es pública y no tiene un mecanismo para limitar el acceso.

## <a name="nextsteps"></a>Pasos siguientes

Una aplicación auténtica podría requerir la autenticación y autorización y podría usar la base de datos de suscripciones para este fin. El tutorial [Implementación de una aplicación ASP.NET MVC segura con suscripción, OAuth y Base de datos SQL][Implementación de una aplicación ASP.NET MVC segura con suscripción, OAuth y Base de datos SQL] se basa en este tutorial y muestra cómo implementar una aplicación web con la base de datos de suscripciones.

Otra forma de almacenar datos en una aplicación de Azure consiste en utilizar el almacenamiento de Azure, que ofrece un almacenamiento de datos no relacional en forma de blobs y tablas. En los vínculos siguientes se proporciona más información sobre Web API, ASP.NET MVC y Azure.

-   [Getting Started with Entity Framework using MVC][Getting Started with Entity Framework using MVC]
-   [Intro to ASP.NET MVC 5][Intro to ASP.NET MVC 5]
-   [Your First ASP.NET Web API][Your First ASP.NET Web API]
-   [Depuración de WAWS][Depuración de WAWS]

Este tutorial y la aplicación de ejemplo fueron desarrollados por [Rick Anderson][6] (Twitter [@RickAndMSFT][Rick Anderson]) con la colaboración de Tom Dykstra y Barry Dorrans (Twitter [@blowdart][@blowdart]).

Es importante que haga comentarios acerca de lo que le gustó o lo que le gustaría que mejorásemos, no solo en relación al tutorial en sí sino a los productos sobre los que trata. Sus comentarios nos ayudarán a clasificar las mejoras por orden de prioridad. Estamos especialmente interesados en averiguar el interés que despierta una mayor automatización para el proceso de configurar e implementar la base de datos de suscripciones.

<!-- bookmarks --> <!-- links --> <!-- images-->

  [Rick Anderson]: https://twitter.com/RickAndMSFT
  [versión anterior de este tutorial]: /es-es/develop/net/tutorials/get-started-vs2012/
  [captura de pantalla del sitio web]: ./media/web-sites-dotnet-rest-service-aspnet-api-sql-database/dntutmobil-intro-finished-web-app.png
  [Configuración del entorno de desarrollo]: #bkmk_setupdevenv
  [Configuración del entorno de Azure]: #bkmk_setupwindowsazure
  [Creación de una aplicación ASP.NET MVC 5]: #bkmk_createmvc4app
  [Implementación de la aplicación en Azure]: #bkmk_deploytowindowsazure1
  [Incorporación de una base de datos a la aplicación]: #bkmk_addadatabase
  [Incorporación de un controlador y una vista para los datos]: #bkmk_addcontroller
  [Incorporación de una interfaz de RESTful de Web API]: #bkmk_addwebapi
  [Incorporación de protección de XSRF]: #xsrf
  [Publicación de la actualización de la aplicación en Azure y Base de datos SQL]: #bkmk_deploydatabaseupdate
  [Portal de administración de Azure]: https://manage.windowsazure.com
  [Vínculo Create with Database del Portal de administración]: ./media/web-sites-dotnet-rest-service-aspnet-api-sql-database/rr6.PNG
  [Paso de creación de un nuevo sitio web del asistente Nuevo sitio web - Crear con base de datos]: ./media/web-sites-dotnet-rest-service-aspnet-api-sql-database/rrCWS.png
  [Paso Configuración de base de datos del asistente Nuevo sitio web - Crear con base de datos]: ./media/web-sites-dotnet-rest-service-aspnet-api-sql-database/dntutmobile-setup-azure-site-004.png
  [1]: ./media/web-sites-dotnet-rest-service-aspnet-api-sql-database/rxPrevDB.png
  [Cuadro de diálogo Nuevo proyecto]: ./media/web-sites-dotnet-rest-service-aspnet-api-sql-database/rr4.PNG
  [Cuadro de diálogo New ASP.NET Project]: ./media/web-sites-dotnet-rest-service-aspnet-api-sql-database/rt3.PNG
  [Sin autenticación]: ./media/web-sites-dotnet-get-started-vs2013/GS13noauth.png
  [Pasos siguientes]: #nextsteps
  [2]: ./media/web-sites-dotnet-get-started-vs2013/GS13newaspnetprojdb.png
  [\_Layout.cshtml en el Explorador de soluciones]: ./media/web-sites-dotnet-rest-service-aspnet-api-sql-database/dntutmobile-createapp-004.png
  [Página principal de lista de tareas pendientes]: ./media/web-sites-dotnet-rest-service-aspnet-api-sql-database/rr5.PNG
  [Opción Publicar del menú contextual del proyecto]: ./media/web-sites-dotnet-rest-service-aspnet-api-sql-database/PublishVSSolution.png
  [Importar configuración de publicación]: ./media/web-sites-dotnet-rest-service-aspnet-api-sql-database/ImportPublishSettings.png
  [iniciar sesión]: ./media/web-sites-dotnet-rest-service-aspnet-api-sql-database/rr7.png
  [Importar perfil de publicación]: ./media/web-sites-dotnet-rest-service-aspnet-api-sql-database/rr8.png
  [Validar conexión]: ./media/web-sites-dotnet-rest-service-aspnet-api-sql-database/ValidateConnection.png
  [Icono de conexión correcta y botón Next en la pestaña Conexión]: ./media/web-sites-dotnet-rest-service-aspnet-api-sql-database/dntutmobile-deploy1-publish-005.png
  [Pestaña Settings]: ./media/web-sites-dotnet-get-started-vs2013/GS13SettingsTab.png
  [Botón StartPreview de la pestaña Preview]: ./media/web-sites-dotnet-rest-service-aspnet-api-sql-database/dntutmobile-deploy1-publish-007.png
  [Página de inicio de tareas pendientes ejecutándose en Azure]: ./media/web-sites-dotnet-rest-service-aspnet-api-sql-database/rxz2.png
  [Agregar clase en el menú contextual de la carpeta Models]: ./media/web-sites-dotnet-rest-service-aspnet-api-sql-database/dntutmobile-adddatabase-001.png
  [Cuadro de diálogo Add New Item]: ./media/web-sites-dotnet-rest-service-aspnet-api-sql-database/dntutmobile-adddatabase-002.png
  [Agregar controlador en el menú contextual de la carpeta Controllers]: ./media/web-sites-dotnet-rest-service-aspnet-api-sql-database/dntutmobile-controller-add-context-menu.png
  [Agregar controlador]: ./media/web-sites-dotnet-rest-service-aspnet-api-sql-database/rrAC.PNG
  [Cuadro de diálogo Agregar controlador]: ./media/web-sites-dotnet-rest-service-aspnet-api-sql-database/rr9.PNG
  [Migraciones de Code First]: http://curah.microsoft.com/55220
  [Package Manager Console en el menú Herramientas]: ./media/web-sites-dotnet-rest-service-aspnet-api-sql-database/dntutmobile-migrations-package-manager-menu.png
  [Debugging Entity Framework (EF) DBs]: http://blogs.msdn.com/b/rickandy/archive/2013/02/12/seeding-and-debugging-entity-framework-ef-dbs.aspx
  [Comandos de Package Manager Console]: ./media/web-sites-dotnet-rest-service-aspnet-api-sql-database/dntutmobile-migrations-package-manager-console.png
  [Vista MVC de los datos]: ./media/web-sites-dotnet-rest-service-aspnet-api-sql-database/rxz3.png
  [jQuery]: http://jquery.com/
  [Knockout.js]: http://knockoutjs.com/
  [Incorporación de una hoja de estilo en el menú contextual de la carpeta Content]: ./media/web-sites-dotnet-rest-service-aspnet-api-sql-database/dntutmobile-controller-add-contents-context-menu.png
  [3]: ./media/web-sites-dotnet-rest-service-aspnet-api-sql-database/rxStyle.png
  [Knockout]: http://knockoutjs.com/index.html "KO"
  [Incorporación de un controlador API]: ./media/web-sites-dotnet-rest-service-aspnet-api-sql-database/rt1.PNG
  [Página de índice con elementos de lista de tareas pendientes]: ./media/web-sites-dotnet-rest-service-aspnet-api-sql-database/dntutmobile-webapi-added-contact.png
  [4]: ./media/web-sites-dotnet-rest-service-aspnet-api-sql-database/rxFFchrome.png
  [Cuadro de diálogo de almacenamiento de la API web.]: ./media/web-sites-dotnet-rest-service-aspnet-api-sql-database/dntutmobile-webapi-save-returned-contacts.png
  [5]: ./media/web-sites-dotnet-rest-service-aspnet-api-sql-database/dntutmobile-webapi-contacts-in-notepad.png
  [Preventing Cross-Site Request Forgery (CSRF) Attacks]: http://www.asp.net/web-api/overview/security/preventing-cross-site-request-forgery-(csrf)-attacks
  [Open Web Application Security Project]: https://www.owasp.org/index.php/Main_Page
  [XSRF]: https://www.owasp.org/index.php/Cross-Site_Request_Forgery_(CSRF)
  [Publicar]: ./media/web-sites-dotnet-rest-service-aspnet-api-sql-database/rxP.png
  [Configuración]: ./media/web-sites-dotnet-rest-service-aspnet-api-sql-database/rt5.png
  [Implementación de una aplicación ASP.NET MVC segura con suscripción, OAuth y Base de datos SQL]: http://www.windowsazure.com/es-es/develop/net/tutorials/web-site-with-sql-database/
  [Getting Started with Entity Framework using MVC]: http://www.asp.net/mvc/tutorials/getting-started-with-ef-using-mvc/creating-an-entity-framework-data-model-for-an-asp-net-mvc-application
  [Intro to ASP.NET MVC 5]: http://www.asp.net/mvc/tutorials/mvc-5/introduction/getting-started
  [Your First ASP.NET Web API]: http://www.asp.net/web-api/overview/getting-started-with-aspnet-web-api/tutorial-your-first-web-api
  [Depuración de WAWS]: http://www.windowsazure.com/es-es/documentation/articles/web-sites-dotnet-troubleshoot-visual-studio/
  [6]: http://blogs.msdn.com/b/rickandy/
  [@blowdart]: https://twitter.com/blowdart
