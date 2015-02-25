<properties 
	pageTitle="Servicio REST mediante ASP.NET Web API - Tutorial de Azure" 
	description="Un tutorial que le enseña cómo implementar una aplicación que utiliza la API web de ASP.NET en un sitio web de Azure mediante Visual Studio." 
	services="web-sites" 
	documentationCenter=".net" 
	authors="riande" 
	manager="wpickett" 
	editor=""/>

<tags 
	ms.service="web-sites" 
	ms.workload="web" 
	ms.tgt_pltfrm="na" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="11/06/2014" 
	ms.author="riande"/>

# Servicio REST mediante ASP.NET Web API y Base de datos SQL 

***Por [Rick Anderson](https://twitter.com/RickAndMSFT) y Tom Dykstra.***

En este tutorial se muestra cómo implementar una aplicación web ASP.NET en un sitio web de Azure utilizando el Asistente para publicación web de Visual Studio 2013 o Visual Studio 2013 Express para Web. 

Puede abrir una cuenta de Azure de manera gratuita y, si todavía no tiene Visual Studio 2013, el SDK instala automáticamente Visual Studio 2013 Express para Web. De este modo, puede empezar a desarrollar contenido para Azure sin coste alguno.

En este tutorial se supone que no tiene ninguna experiencia previa con Azure. Cuando acabe, tendrá una sencilla aplicación web que se ejecutará en la nube.
 
Aprenderá a realizar los siguientes procedimientos:

* Habilitación de su equipo para desarrollar contenido de Azure mediante la instalación del SDK de Azure.
* Crear un proyecto ASP.NET MVC 5 de Visual Studio y publicarlo en un sitio web de Azure.
* Utilizar ASP.NET Web API para habilitar llamadas de API de Restful.
* Uso de una Base de datos SQL para almacenar datos en Azure.
* Publicar actualizaciones de la aplicación en Azure.

Va a desarrollar una aplicación web de lista de contactos sencilla basada en ASP.NET MVC 5 y que utiliza ADO.NET Entity Framework para obtener acceso a la base de datos. La siguiente ilustración muestra la aplicación completada:

![screenshot of web site][intro001]

Apartados de este tutorial:

* [Configuración del entorno de desarrollo][setupdbenv]
* [Configuración del entorno de Azure][setupwindowsazureenv]
* [Creación de una aplicación ASP.NET MVC 5][createapplication]
* [Implementación de la aplicación en Azure][deployapp1]
* [Incorporación de una base de datos a la aplicación][adddb]
* [Incorporación de un controlador y una vista para los datos][addcontroller]
* [Incorporación de una interfaz de RESTful de Web API][addwebapi]
* [Incorporación de protección de XSRF][]
* [Publicación de la actualización de la aplicación en Azure y Base de datos SQL][deploy2]

<a name="bkmk_setupdevenv"></a>
<!-- the next line produces the "Set up the development environment" section as see at http://www.windowsazure.com/es-es/documentation/articles/web-sites-dotnet-get-started/ -->
[AZURE.INCLUDE [create-account-and-websites-note](../includes/create-account-and-websites-note.md)]

<h2><a name="bkmk_setupwindowsazure"></a>Configuración del entorno de Azure</h2>

A continuación, configure el entorno de Azure creando un sitio web de Azure y una Base de datos SQL.

### Creación de un sitio web y una base de datos SQL en Azure

El paso siguiente consiste en crear el sitio web de Azure y la base de datos SQL que utilizará la aplicación.

El sitio web de Azure se ejecutará en un entorno de hospedaje compartido, es decir, en máquinas virtuales compartidas con otros clientes de Azure. Los entornos de hospedaje compartidos ofrecen un coste reducido a los nuevos usuarios de servicios en la nube. Más adelante, si el tráfico web aumenta, es posible escalar la aplicación para atender la demanda ejecutándola en máquinas virtuales dedicadas. Si necesita una arquitectura más compleja, puede migrar la aplicación a un Servicio en la nube de Azure. Los servicios en la nube se ejecutan en MV dedicadas que se pueden configurar según sus necesidades.

La Base de datos SQL es un servicio de base de datos relacional en la nube que se basa en la tecnología de SQL Server. Las herramientas y aplicaciones que funcionan con SQL Server también funcionan con la Base de datos SQL.

1. En el [Portal de administración de Azure](https://manage.windowsazure.com), haga clic en **Sitios web** en el recuadro izquierdo y elija **Nuevo**.

2. Haga clic en **CREACIÓN PERSONALIZADA**.

	![Vínculo Crear con base de datos del Portal de administración](./media/web-sites-dotnet-rest-service-aspnet-api-sql-database/rr6.PNG)

	Se abre el asistente para **Nuevo sitio web - Creación personalizada**. 

3. En el paso **Nuevo sitio web** del asistente, escriba una cadena en el cuadro **URL** para usarla como dirección URL única de la aplicación. La URL completa consistirá en el valor que escriba más el sufijo que aparece debajo del cuadro de texto. La ilustración muestra "contactmgr11", pero dicha URL probablemente ya esté en uso, por lo que tendrá que elegir otra distinta.

1. En la lista desplegable **Región**, elija la región más cercana.

1. En la lista desplegable **Base de datos**, elija **Crear una base de datos SQL de 20 MB gratuita**.

	![Paso de Creación de un nuevo sitio web del asistente Nuevo sitio web - Crear con base de datos](./media/web-sites-dotnet-rest-service-aspnet-api-sql-database/rrCWS.png)

6. Haga clic en la flecha derecha situada en la parte inferior del cuadro.

	El asistente avanza hasta el paso **Configuración de la base de datos**.

7. En el cuadro **Nombre**, escriba  *ContactDB*.

8. En el cuadro **Servidor**, seleccione **Nuevo servidor de bases de datos SQL**. Si anteriormente creó una base de datos de SQL Server, también tiene la opción de seleccionar ese SQL Server en el control desplegable.

9. Haga clic en la flecha derecha situada en la parte inferior del cuadro.

10. Complete los campos **NOMBRE DE INICIO DE SESIÓN** y **CONTRASEÑA** con los datos de un administrador. Si seleccionó **New SQL Database server**, no debe escribir un nombre y una contraseña existentes en estos campos, sino definir unos nuevos que volverá a utilizar más adelante para obtener acceso a la base de datos. Si seleccionó un SQL Server creado anteriormente, se le pedirá la contraseña de la cuenta de SQL Server que abrió para crearlo. En este tutorial, no seleccionaremos la casilla **Avanzada**. La casilla **Avanzada ** permite establecer el tamaño de la base de datos (el valor predeterminado es 1 GB, pero puede aumentarlo hasta 150 GB) y la intercalación.

11. Haga clic en la marca de verificación que aparece en la parte inferior del cuadro para indicar que finalizó la configuración.

	![Paso Configuración de base de datos del asistente Nuevo sitio web - Crear con base de datos][setup007]

	 La imagen siguiente muestra cómo usar un SQL Server y datos de inicio de sesión existentes.
	
	![Paso Configuración de base de datos del asistente Nuevo sitio web - Crear con base de datos][rxPrevDB]

	El Portal de administración vuelve a la página Sitios web y la columna**Estado** indica que se está creando el sitio. Después de unos segundos, la columna **Estado** indica que el sitio se creó correctamente. En la barra de navegación situada a la izquierda, el número de sitios que tiene en su cuenta aparece junto al icono **Sitios web** y el número de bases de datos aparece junto al icono **Bases de datos SQL**.

<!-- [Websites page of Management Portal, website created][setup009] -->

<h2><a name="bkmk_createmvc4app"></a>Creación de una aplicación ASP.NET MVC 5</h2>

Ya ha creado un sitio web de Azure, pero todavía no hay contenido en él. El siguiente paso consiste en crear el proyecto de aplicación web de Visual Studio que luego publicará en Azure.

### Creación del proyecto

1. Inicie Visual Studio 2013.
1. En el menú **Archivo**, haga clic en **Nuevo proyecto**.
3. En el cuadro de diálogo **Nuevo proyecto**, expanda**Visual C#** y seleccione **Web** y, a continuación, **Aplicación web ASP.NET MVC 5**. Mantenga el valor **.NET Framework 4.5** predeterminado. Póngale a la aplicación el nombre **ContactManager** y haga clic en **Aceptar**.
	![Cuadro de diálogo Nuevo proyecto](./media/web-sites-dotnet-rest-service-aspnet-api-sql-database/rr4.PNG)]
1. En el cuadro de diálogo **Nuevo proyecto de ASP.NET**, seleccione la plantilla **MVC**, marque **Web API** y, a continuación, haga clic en **Cambiar autenticación**.

	![Cuadro de diálogo Nuevo proyecto de ASP.NET](./media/web-sites-dotnet-rest-service-aspnet-api-sql-database/rt3.PNG)

1. En el cuadro de diálogo **Cambiar autenticación**, haga clic en **Sin autenticación** y, a continuación, en **Aceptar**.

	![Sin autenticación](./media/web-sites-dotnet-get-started-vs2013/GS13noauth.png)

	La aplicación de ejemplo que está creando no incluirá características que requieran que los usuarios inicien sesión. Para obtener información acerca de cómo implementar la autenticación y las características de autenticación, consulte la sección [Pasos siguientes](#nextsteps) al final de este tutorial. 

1. En el cuadro de diálogo **Nuevo proyecto de ASP.NET**, haga clic en **Aceptar**.

	![Cuadro de diálogo New ASP.NET Project](./media/web-sites-dotnet-get-started-vs2013/GS13newaspnetprojdb.png)

### Establecimiento del encabezado y pie de página


1. En el **Explorador de soluciones**, expanda la carpeta *Views\Shared* y abra el archivo *_Layout.cshtml*.

	![_Layout.cshtml en el Explorador de soluciones][newapp004]

1. Reemplace el contenido del archivo *_Layout.cshtml* por el código siguiente:


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
			
El código anterior cambia el nombre de la aplicación "My ASP.NET App" a "Contact Manager" y quita los vínculos de **Inicio**, **Acerca de** y **Contacto**.

### Ejecución de la aplicación de forma local

1. Presione CTRL+F5 para ejecutar la aplicación.
Aparece la página principal de la aplicación en el explorador predeterminado.
	![Página principal de lista de tareas pendientes](./media/web-sites-dotnet-rest-service-aspnet-api-sql-database/rr5.PNG)

Esto es todo lo que necesita hacer por ahora para crear la aplicación que va a implementar en Azure. Más adelante agregará la funcionalidad de base de datos.

<h2><a name="bkmk_deploytowindowsazure1"></a>Implementación de la aplicación en Azure</h2>

1. En Visual Studio, haga clic con el botón secundario en el proyecto, en el **Explorador de soluciones** y seleccione **Publicar** en el menú contextual.

	![Publicación en el menú contextual del proyecto][PublishVSSolution]

	Se abre el asistente para **publicación web**.

2. En la pestaña **Perfil** del asistente para **publicación web**, haga clic en **Importar**.

	![Import publish settings][ImportPublishSettings]

	Aparecerá el cuadro de diálogo **Importar perfil de publicación**.

 3.	Seleccione Import from an Azure Web Site. Primero debe iniciar sesión si todavía no lo ha hecho. Haga clic en **Iniciar sesión**. Escriba el usuario asociado a la suscripción y siga los pasos para iniciar sesión.

	![sign in](./media/web-sites-dotnet-rest-service-aspnet-api-sql-database/rr7.png)

	Seleccione el sitio web en la lista desplegable y, a continuación, haga clic en **Aceptar**.

	![Import Publish Profile](./media/web-sites-dotnet-rest-service-aspnet-api-sql-database/rr8.png)

8. En la pestaña **Conexión**, haga clic en **Validar conexión** para asegurarse de que la configuración es correcta.

	![Validate connection][ValidateConnection]

9. Una vez que se valide la conexión, aparecerá una marca de verificación verde junto al botón **Validar conexión**.

	![connection successful icon and Next button in Connection tab][firsdeploy007]

1. Haga clic en **Next**.

	![Settings tab](./media/web-sites-dotnet-get-started-vs2013/GS13SettingsTab.png)

	Puede aceptar la configuración predeterminada de esta pestaña.  Está implementando una configuración de compilación de versión y no es necesario eliminar archivos del servidor de destino, precompilar la aplicación ni excluir archivos de la carpeta App_Data. Si desea depurar en el sitio Azure activo, tendrá que implementar una configuración de depuración (no una versión). Consulte la sección [Pasos siguientes](#nextsteps) al final de este tutorial.

12. En la pestaña **Vista previa**, haga clic en **Iniciar vista previa**.

	En esta pestaña aparece la lista de archivos que se copiarán en el servidor. Para publicar la aplicación no es necesario mostrar la vista previa, pero se trata de una función útil que conviene conocer. En este caso, no tiene que hacer nada con la lista de archivos que se muestra. La próxima vez que publique, solo estarán en la lista de vista previa los archivos que se han cambiado.

	![StartPreview button in the Preview tab][firsdeploy009]

12. Haga clic en **Publicar**.

	Visual Studio comienza el proceso de copiar los archivos en el servidor de Azure. La ventana **Salida** muestra qué acciones de implementación se realizaron e informa de la correcta finalización de la implementación.

14. El explorador predeterminado se dirige automáticamente a la URL del sitio web implementado.

	La aplicación creada ahora se ejecuta en la nube.
	
	![To Do List home page running in Azure][rxz2]

<h2><a name="bkmk_addadatabaseIncorporación de una base de datos a la aplicación"></a></h2>

A continuación, actualizará la aplicación MVC incorporando funciones que permitan mostrar y actualizar los contactos y almacenar los datos en una base de datos. La aplicación utilizará Entity Framework para crear la base de datos y para leer y actualizar los datos que esta contiene.

### Incorporación de clases de modelos de datos para los contactos

Se empieza por crear un modelo de datos sencillo en código.

1. En el **Explorador de soluciones**, haga clic con el botón secundario en la carpeta Modelos, a continuación en **Agregar** y, por último, en **Clase**.

	![Add Class in Models folder context menu][adddb001]

2. En el cuadro de diálogo **Agregar nuevo elemento**, asigne al archivo de la nueva clase el nombre  *Contact.cs* y, a continuación, haga clic en **Agregar**.

	![Add New Item dialog box][adddb002]

3. Reemplace el contenido del archivo Contacts.cs por el código siguiente.

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

La clase **Contacts** define qué datos de los contactos va a almacenar, además de una clave primaria, ContactID, necesaria para la base de datos. Puede obtener más información sobre los modelos de datos en la sección [Pasos siguientes](#nextsteps) al final de este tutorial.

### Creación de páginas web que permiten a los usuarios de aplicaciones utilizar los contactos

La característica de scaffolding de ASP.NET MVC puede generar automáticamente código que realiza acciones de creación, lectura, actualización y eliminación (CRUD).

<h2><a name="bkmk_addcontrollerIncorporación de un controlador y una vista para los datos"></a></h2>

1. En el **Explorador de soluciones**, expanda la carpeta Controllers.

3. Cree el proyecto **(Ctrl+Mayús+B)**. (Debe crear el proyecto antes de usar el mecanismo de scaffolding). 

4. Haga clic con el botón secundario en la carpeta Controllers, a continuación en **Agregar** y, a continuación, en **Controlador**.

	![Add Controller in Controllers folder context menu][addcode001]

1. En el cuadro de diálogo **Agregar Scaffold**, seleccione **Controlador de MVC con vistas, usando Entity Framework** y haga clic en **Agregar**.

 ![Add controller](./media/web-sites-dotnet-rest-service-aspnet-api-sql-database/rrAC.PNG)

6. Asigne al controlador el nombre de **HomeController**. Seleccione **Contact** como su clase de modelo. Haga clic en el botón **Nuevo contexto de datos** y acepte el valor predeterminado de "ContactManager.Models.ContactManagerContext" para **Nuevo tipo de contexto de datos**. Haga clic en **Add**.

	![Add Controller dialog box](./media/web-sites-dotnet-rest-service-aspnet-api-sql-database/rr9.PNG)

	Un cuadro de diálogo indicará: "A file with the name HomeController already exits. Do you want to replace it?". Haga clic en **Sí**. Estamos sobrescribiendo el controlador de inicio que se creó con el nuevo proyecto. Utilizaremos el nuevo controlador de inicio para nuestra lista de contactos.

	Visual Studio crea métodos y vistas de controlador para las operaciones CRUD de base de datos que afectan a los objetos **Contact**.

## Habilitación de las migraciones, creación de la base de datos e incorporación de datos de ejemplo y un inicializador de datos ##

La siguiente tarea consiste en habilitar la función [Migraciones de Code First](http://curah.microsoft.com/55220) para crear la base de datos a partir del modelo de datos ya establecido.

1. En el menú **Herramientas**, seleccione **Administrador de paquetes de biblioteca** y, a continuación, **Consola del administrador de paquetes**.

	![Package Manager Console in Tools menu][addcode008]

2. En la ventana **Package Manager Console**, escriba el comando siguiente:

		enable-migrations 
  
	El comando **enable-migrations** crea una carpeta *Migrations* y guarda en dicha carpeta un archivo *Configuration.cs* que puede editar para configurar las migraciones. 

2. En la ventana **Package Manager Console**, escriba el comando siguiente:

		add-migration Initial

	El comando **add-migration Initial** genera una clase denominada **&lt;date_stamp&gt;Initial** que crea la base de datos. El primer parámetro ( *Initial* ) es arbitrario y se utiliza para crear el nombre del archivo. Puede ver los archivos de las nuevas clases en el **Explorador de soluciones**.

	En la clase **Initial**, el método **Up** crea la tabla Contacts y el método **Down** (que se utiliza cuando se desea volver al estado anterior) la anula.

3. Abra el archivo  *Migrations\Configuration.cs*. 

4. Agregue los siguientes espacios de nombres. 

    	 using ContactManager.Models;

5. Reemplace el método  *Seed* por el código siguiente:
		
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

	El código anterior inicializará la base de datos con la información de contacto. Para obtener más información acerca de la inicialización de la base de datos, consulte [Depuración de bases de datos de Entity Framework (EF)](http://blogs.msdn.com/b/rickandy/archive/2013/02/12/seeding-and-debugging-entity-framework-ef-dbs.aspx).


1. En **Package Manager Console**, escriba el comando:

		update-database

	![Package Manager Console commands][addcode009]

	El comando **update-database** ejecuta la primera migración, lo que crea la base de datos. De manera predeterminada, la base de datos que se crea es una base de datos LocalDB de SQL Server Express.

1. Presione CTRL+F5 para ejecutar la aplicación. 

La aplicación muestra los datos de inicialización y ofrece enlaces de edición, detalles y eliminación.

![MVC view of data][rxz3]

<h2><a name="bkmk_addview"></a>Edición de la vista</h2>

1. Abra el archivo  *Views\Home\Index.cshtml*. En el paso siguiente, reemplazaremos el marcado generado por código que utiliza [jQuery](http://jquery.com/) y [Knockout.js](http://knockoutjs.com/). Este nuevo código recupera la lista de contactos con la utilización de web API y JSON y, a continuación, enlaza los datos de contacto con la UI mediante knockout.js. Para obtener más información, consulte la sección [Pasos siguientes](#nextsteps) al final de este tutorial. 


2. Reemplace el contenido del archivo por el código siguiente.

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

3. Haga clic con el botón secundario en la carpeta Content, haga clic en **Agregar** y, a continuación, haga clic en **Nuevo elemento...**.

	![Add style sheet in Content folder context menu][addcode005]

4. En el cuadro de diálogo **Agregar nuevo elemento**, escriba **Style** en el cuadro de búsqueda superior derecho y, a continuación, seleccione **Hoja de estilo**.
	![Add New Item dialog box][rxStyle]

5. Asigne como nombre del archivo *Contacts.css* y haga clic en **Agregar**. Reemplace el contenido del archivo por el código siguiente.
    
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

6. Abra el archivo  *App_Start\BundleConfig.cs*.


7. Agregue el código siguiente para registrar el complemento [Knockout](http://knockoutjs.com/index.html "KO").

		bundles.Add(new ScriptBundle("~/bundles/knockout").Include(
		            "~/Scripts/knockout-{version}.js"));
	Este ejemplo utiliza knockout para simplificar el código JavaScript dinámico que trata las plantillas de la pantalla.

8. Modifique la entrada contents/css para registrar la hoja de estilos *contacts.css*. Cambie la línea siguiente:

                 bundles.Add(new StyleBundle("~/Content/css").Include(
                   "~/Content/bootstrap.css",
                   "~/Content/site.css"));
To:

        bundles.Add(new StyleBundle("~/Content/css").Include(
                   "~/Content/bootstrap.css",
                   "~/Content/contacts.css",
                   "~/Content/site.css"));

1. En Package Manager Console, ejecute el comando siguiente para instalar Knockout.

	Install-Package knockoutjs

<h2><a name="bkmk_addwebapi"></a>Incorporación de un controlador para la interfaz Restful de Web API</h2>

1. En el **Explorador de soluciones**, haga clic con el botón secundario en Controllers, después en **Agregar** y, a continuación, en **Controlador....** 

1. En el cuadro de diálogo **Agregar Scaffold**, escriba **Web API 2 Controller with actions, using Entity Framework** y, a continuación, haga clic en **Agregar**.

	![Add API controller](./media/web-sites-dotnet-rest-service-aspnet-api-sql-database/rt1.PNG)

4. En el cuadro de diálogo **Agregar controlador**, escriba "ContactsController" como su nombre de controlador. Seleccione "Contact (ContactManager.Models)" para la opción **Clase de modelo**.  Mantenga el valor predeterminado para **Clase de contexto de datos**. 

6. Haga clic en **Add**.

### Ejecución de la aplicación de forma local

1. Presione CTRL+F5 para ejecutar la aplicación.

	![Index page][intro001]

2. Especifique un contacto y haga clic en **Agregar**. La aplicación regresa a la página de inicio y muestra el contacto que ha introducido.

	![Index page with to-do list items][addwebapi004]

3. En el explorador, anexe **/api/contacts** a la dirección URL.

	La URL resultante se parecerá a http://localhost:1234/api/contacts. La API web de RESTful que ha agregado devuelve los contactos almacenados. Firefox y Chrome mostrarán los datos en formato XML.

	![Index page with to-do list items][rxFFchrome]
	

	Internet Explorer le solicitará que abra o guarde los contactos.

	![Web API save dialog][addwebapi006]
	
	
	Puede abrir los contactos devueltos con el Bloc de notas o en un explorador.
	
	Este resultado lo puede consumir otra aplicación como una página web móvil o aplicación.

	![Web API save dialog][addwebapi007]

	**Advertencia de seguridad**: En este momento, la aplicación es insegura y vulnerable frente un ataque CSRF. Más adelante en el tutorial eliminaremos esta vulnerabilidad. Para obtener más información, consulte [Impedimento de ataques de falsificación de solicitud entre sitios (CSRF)][prevent-csrf-attacks].

<h2><a name="xsrf"></a>Incorporación de protección de XSRF</h2>

La falsificación de solicitud entre sitios (también conocida como XSRF o CSRF) es un ataque contra aplicaciones hospedadas en web, en donde un sitio web malintencionado puede influir en la interacción entre un explorador cliente y un sitio web de confianza de ese explorador. Estos ataques son posibles porque los exploradores web enviarán tokens de autenticación automáticamente con cada solicitud a un sitio web. El ejemplo canónico es una cookie de autenticación, como el vale de autenticación de formularios de ASP.NET. Sin embargo, los sitios web que utilicen cualquier mecanismo de autenticación persistente (como Autenticación de Windows, Basic, entre otras) podrían ser objetivos de esos ataques.

Un ataque XSRF es distinto de un ataque de suplantación de identidad (phishing). Los ataques de suplantación de identidad requieren interacción de la víctima. En un ataque de suplantación de identidad (phishing), un sitio web malintencionado imitará el sitio web de destino y engañará a la víctima para que proporcione información confidencial al atacante. En un ataque XSRF, con frecuencia no hay interacción necesaria de la víctima. Por el contrario, el atacante confía en el explorador enviando automáticamente todas las cookies relevantes al sitio web de destino.

Para obtener más información, consulte [Proyecto de seguridad de aplicación web](https://www.owasp.org/index.php/Main_Page) (OWASP) [XSRF](https://www.owasp.org/index.php/Cross-Site_Request_Forgery_(CSRF)).

1. En el **Explorador de soluciones**, haga clic con el botón secundario en el proyecto **ContactManager** y haga clic en **Agregar** y, a continuación, en **Clase**.

2. Asigne como nombre del archivo *ValidateHttpAntiForgeryTokenAttribute.cs* y agregue el código siguiente:

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

1. Agregue la siguiente instrucción *using* al controlador de contratos para que tenga acceso al atributo **[ValidateHttpAntiForgeryToken]**.

	using ContactManager.Filters;

1. Agregue el atributo **[ValidateHttpAntiForgeryToken]** a los métodos Post de **ContactsController** para protegerlo contra amenazas XSRF. Lo agregará a los métodos de acciones "PutContact", "PostContact" y **DeleteContact**.

	[ValidateHttpAntiForgeryToken]
        public IHttpActionResult PutContact(int id, Contact contact)
        {

1. Actualice la sección *Scripts* del archivo *Views\Home\Index.cshtml* para incluir código que obtenga los tokens XSRF.

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


<h2><a name="bkmk_deploydatabaseupdatePublicación de la actualización de la aplicación en Azure y Base de datos SQL"></a></h2>

Para publicar la aplicación, repita el procedimiento que ha realizado anteriormente.

1. En el **Explorador de soluciones**, haga clic con el botón secundario en el proyecto y seleccione **Publicar**.

	![Publish][rxP]

5. Haga clic en la pestaña **Configuración**.
	

1. En **ContactsManagerContext(ContactsManagerContext)**, haga clic en el icono **v**para cambiar la *Remote connection string* a la cadena de conexión de la base de datos del contacto. Haga clic en **ContactDB**.

	![Settings](./media/web-sites-dotnet-rest-service-aspnet-api-sql-database/rt5.png)

7. Active la casilla para **Execute Code First Migrations (runs on application start)**.

1. Haga clic en **Siguiente** y, a continuación, haga clic en **Vista previa**. Visual Studio muestra una lista de los archivos que se agregarán o actualizarán.

8. Haga clic en **Publicar**.
Una vez finalizada la implementación, el explorador se abre por la página de inicio de la aplicación.

	![Index page with no contacts][intro001]

	El proceso de publicación de Visual Studio configuró automáticamente la cadena de conexión en el archivo  *Web.config* implementado para apuntar a la base de datos SQL. También ha configurado las migraciones de Code First para actualizar automáticamente la base de datos a la versión más reciente la primera vez que la aplicación tiene acceso a la base de datos tras la implementación.

	Como resultado de esta configuración, Code First crea la base de datos mediante la ejecución del código en la clase **Initial** que ha creado anteriormente. Lo realizó la primera vez que la aplicación intentó obtener acceso a la base de datos tras la implementación.

9. Introduzca un contacto de la misma forma que hizo al ejecutar la aplicación localmente, para comprobar que la implementación de la base de datos se ha realizado correctamente.

Cuando ve que el elemento introducido se guarda y aparece en la página del administrador de contactos, sabe que se ha almacenado en la base de datos.

![Página de índice con contactos][addwebapi004]

La aplicación ahora se está ejecutando en la nube, utilizando Base de datos SQL para almacenar sus datos. Después de finalizar la prueba de la aplicación en Azure, elimínela. La aplicación es pública y no tiene un mecanismo para limitar el acceso.

<h2><a name="nextstepsPasos siguientes"></a></h2>

Una aplicación auténtica podría requerir la autenticación y autorización y podría usar la base de datos de suscripciones para este fin. El tutorial [Implementación de una aplicación ASP.NET MVC segura con suscripción, OAuth y Base de datos SQL](http://www.windowsazure.com/es-es/develop/net/tutorials/web-site-with-sql-database/) se basa en este tutorial y muestra cómo implementar una aplicación web con la base de datos de suscripciones.

Otra forma de almacenar datos en una aplicación de Azure consiste en utilizar el almacenamiento de Azure, que ofrece un almacenamiento de datos no relacional en forma de blobs y tablas. En los vínculos siguientes se proporciona más información sobre Web API, ASP.NET MVC y Azure.
 

* [Getting Started with Entity Framework using MVC][EFCodeFirstMVCTutorial]
* [Intro to ASP.NET MVC 5](http://www.asp.net/mvc/tutorials/mvc-5/introduction/getting-started)
* [Your First ASP.NET Web API](http://www.asp.net/web-api/overview/getting-started-with-aspnet-web-api/tutorial-your-first-web-api)
* [Debugging WAWS](http://www.windowsazure.com/es-es/documentation/articles/web-sites-dotnet-troubleshoot-visual-studio/)

Este tutorial y la aplicación de ejemplo fueron desarrollados por [Rick Anderson](http://blogs.msdn.com/b/rickandy/) (Twitter [@RickAndMSFT](https://twitter.com/RickAndMSFT)) con la colaboración de Tom Dykstra y Barry Dorrans (Twitter [@blowdart](https://twitter.com/blowdart)). 

Es importante que haga comentarios acerca de lo que le gustó o lo que le gustaría que mejorásemos, no solo en relación al tutorial en sí sino a los productos sobre los que trata. Sus comentarios nos ayudarán a clasificar las mejoras por orden de prioridad. Estamos especialmente interesados en averiguar el interés que despierta una mayor automatización para el proceso de configurar e implementar la base de datos de suscripciones. 

<!-- bookmarks -->
[Incorporación de un proveedor de OAuth]: #addOauth
[Incorporación de roles a la base de datos de suscripciones]:#mbrDB
[Creación de un script de implementación de datos]:#ppd
[Actualización de la base de datos de suscripciones]:#ppd2
[setupdbenv]: #bkmk_setupdevenv
[setupwindowsazureenv]: #bkmk_setupwindowsazure
[createapplication]: #bkmk_createmvc4app
[deployapp1]: #bkmk_deploytowindowsazure1
[adddb]: #bkmk_addadatabase
[addcontroller]: #bkmk_addcontroller
[addwebapi]: #bkmk_addwebapi
[deploy2]: #bkmk_deploydatabaseupdate

<!-- links -->
[EFCodeFirstMVCTutorial]: http://www.asp.net/mvc/tutorials/getting-started-with-ef-using-mvc/creating-an-entity-framework-data-model-for-an-asp-net-mvc-application
[dbcontext-link]: http://msdn.microsoft.com/es-es/library/system.data.entity.dbcontext(v=VS.103).aspx


<!-- images-->
[rxE]: ./media/web-sites-dotnet-rest-service-aspnet-api-sql-database/rxE.png
[rxP]: ./media/web-sites-dotnet-rest-service-aspnet-api-sql-database/rxP.png
[rx22]: ./media/web-sites-dotnet-rest-service-aspnet-api-sql-database/
[rxb2]: ./media/web-sites-dotnet-rest-service-aspnet-api-sql-database/rxb2.png
[rxz]: ./media/web-sites-dotnet-rest-service-aspnet-api-sql-database/rxz.png
[rxzz]: ./media/web-sites-dotnet-rest-service-aspnet-api-sql-database/rxzz.png
[rxz2]: ./media/web-sites-dotnet-rest-service-aspnet-api-sql-database/rxz2.png
[rxz3]: ./media/web-sites-dotnet-rest-service-aspnet-api-sql-database/rxz3.png
[rxStyle]: ./media/web-sites-dotnet-rest-service-aspnet-api-sql-database/rxStyle.png
[rxz4]: ./media/web-sites-dotnet-rest-service-aspnet-api-sql-database/rxz4.png
[rxz44]: ./media/web-sites-dotnet-rest-service-aspnet-api-sql-database/rxz44.png
[rxNewCtx]: ./media/web-sites-dotnet-rest-service-aspnet-api-sql-database/rxNewCtx.png
[rxPrevDB]: ./media/web-sites-dotnet-rest-service-aspnet-api-sql-database/rxPrevDB.png
[rxOverwrite]: ./media/web-sites-dotnet-rest-service-aspnet-api-sql-database/rxOverwrite.png
[rxPWS]: ./media/web-sites-dotnet-rest-service-aspnet-api-sql-database/rxPWS.png
[rxNewCtx]: ./media/web-sites-dotnet-rest-service-aspnet-api-sql-database/rxNewCtx.png
[rxAddApiController]: ./media/web-sites-dotnet-rest-service-aspnet-api-sql-database/rxAddApiController.png
[rxFFchrome]: ./media/web-sites-dotnet-rest-service-aspnet-api-sql-database/rxFFchrome.png
[intro001]: ./media/web-sites-dotnet-rest-service-aspnet-api-sql-database/dntutmobil-intro-finished-web-app.png
[rxCreateWSwithDB]: ./media/web-sites-dotnet-rest-service-aspnet-api-sql-database/rxCreateWSwithDB.png
[setup007]: ./media/web-sites-dotnet-rest-service-aspnet-api-sql-database/dntutmobile-setup-azure-site-004.png
[setup009]: ../Media/dntutmobile-setup-azure-site-006.png
[newapp002]: ./media/web-sites-dotnet-rest-service-aspnet-api-sql-database/dntutmobile-createapp-002.png
[newapp004]: ./media/web-sites-dotnet-rest-service-aspnet-api-sql-database/dntutmobile-createapp-004.png
[firsdeploy007]: ./media/web-sites-dotnet-rest-service-aspnet-api-sql-database/dntutmobile-deploy1-publish-005.png
[firsdeploy009]: ./media/web-sites-dotnet-rest-service-aspnet-api-sql-database/dntutmobile-deploy1-publish-007.png
[adddb001]: ./media/web-sites-dotnet-rest-service-aspnet-api-sql-database/dntutmobile-adddatabase-001.png
[adddb002]: ./media/web-sites-dotnet-rest-service-aspnet-api-sql-database/dntutmobile-adddatabase-002.png
[addcode001]: ./media/web-sites-dotnet-rest-service-aspnet-api-sql-database/dntutmobile-controller-add-context-menu.png
[addcode002]: ./media/web-sites-dotnet-rest-service-aspnet-api-sql-database/dntutmobile-controller-add-controller-dialog.png
[addcode004]: ./media/web-sites-dotnet-rest-service-aspnet-api-sql-database/dntutmobile-controller-modify-index-context.png
[addcode005]: ./media/web-sites-dotnet-rest-service-aspnet-api-sql-database/dntutmobile-controller-add-contents-context-menu.png
[addcode007]: ./media/web-sites-dotnet-rest-service-aspnet-api-sql-database/dntutmobile-controller-modify-bundleconfig-context.png
[addcode008]: ./media/web-sites-dotnet-rest-service-aspnet-api-sql-database/dntutmobile-migrations-package-manager-menu.png
[addcode009]: ./media/web-sites-dotnet-rest-service-aspnet-api-sql-database/dntutmobile-migrations-package-manager-console.png
[addwebapi004]: ./media/web-sites-dotnet-rest-service-aspnet-api-sql-database/dntutmobile-webapi-added-contact.png
[addwebapi006]: ./media/web-sites-dotnet-rest-service-aspnet-api-sql-database/dntutmobile-webapi-save-returned-contacts.png
[addwebapi007]: ./media/web-sites-dotnet-rest-service-aspnet-api-sql-database/dntutmobile-webapi-contacts-in-notepad.png
[Add XSRF Protection]: #xsrf
[WebPIAzureSdk20NetVS12]: ./media/web-sites-dotnet-rest-service-aspnet-api-sql-database/WebPIAzureSdk20NetVS12.png
[Add XSRF Protection]: #xsrf
[ImportPublishSettings]: ./media/web-sites-dotnet-rest-service-aspnet-api-sql-database/ImportPublishSettings.png
[ImportPublishProfile]: ./media/web-sites-dotnet-rest-service-aspnet-api-sql-database/ImportPublishProfile.png
[PublishVSSolution]: ./media/web-sites-dotnet-rest-service-aspnet-api-sql-database/PublishVSSolution.png
[ValidateConnection]: ./media/web-sites-dotnet-rest-service-aspnet-api-sql-database/ValidateConnection.png
[WebPIAzureSdk20NetVS12]: ./media/web-sites-dotnet-rest-service-aspnet-api-sql-database/WebPIAzureSdk20NetVS12.png
[prevent-csrf-attacks]: http://www.asp.net/web-api/overview/security/preventing-cross-site-request-forgery-(csrf)-attacks




<!--HONumber=42-->
