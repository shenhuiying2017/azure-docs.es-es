<properties linkid="develop-net-tutorials-website-with-mongodb-mongolab" urlDisplayName="Web Site with MongoDB on MongoLab" pageTitle="Create a Web Site that uses MongoDB on MongoLab (.NET)" metaKeywords="" description="Learn how to create an Azure web site that stores data in MongoDB hosted by MongoLab." metaCanonical="" services="web-sites" documentationCenter=".NET" title="Create a C# ASP.NET Application on Azure with MongoDB using the MongoLab Add-On" authors="" solutions="" manager="" editor="" />

Creación de una aplicación C\# ASP.NET en Azure con MongoDB usando el complemento de MongoLab
=============================================================================================

*Por Eric Sedor, MongoLab*

ˇSaludos, aventurero! Bienvenido a MongoDB como servicio. En este tutorial, aprenderá lo siguiente:

1.  [Aprovisionamiento de la base de datos](#provision): El complemento [MongoLab](http://mongolab.com) de la Tienda de Azure proporcionará una base de datos MongoDB hospedada en la nube de Azure y administrada por la plataforma de base de datos en la nube de MongoLab.
2.  [Creación de la aplicación](#create): Será una aplicación Node.js sencilla para mantener una lista de tareas.
3.  [Implementación de la aplicación](#deploy): Al unir unos pocos enlaces de configuración, haremos la implementación de nuestro código en un abrir y cerrar de ojos.
4.  [Administración de la base de datos](#manage): Finalmente, le mostraremos el portal de administración de base de datos basado en web de MongoLab donde puede buscar, visualizar y modificar los datos con facilidad.

En cualquier momento de este tutorial puede enviar un correo electrónico a <support@mongolab.com> si tiene alguna pregunta.

Inicio rápido
-------------

Si ya tiene una aplicación de Azure y un sitio web con el que desea trabajar, o si se ha familiarizado con la Tienda de Azure, use esta sección para empezar rápidamente. De lo contrario, vaya a la sección [Aprovisionar la base de datos](#provision) a continuación.

1.  Abra la Tienda de Azure.

     ![Tienda](./media/partner-mongodb-web-sites-dotnet-use-mongolab/button-store.png)
2.  Compre el complemento MongoLab.

     ![MongoLab](./media/partner-mongodb-web-sites-dotnet-use-mongolab/entry-mongolab.png)
3.  Haga clic en el complemento MongoLab en la lista de complementos y haga clic en **Connection Info**.

     ![BotónConnectionInfo](./media/partner-mongodb-web-sites-dotnet-use-mongolab/button-connectioninfo.png)
4.  Copie MONGOLAB\_URI en su portapapeles.

     ![ConnectionInfoScreen](./media/partner-mongodb-web-sites-dotnet-use-mongolab/dialog-mongolab_connectioninfo.png)

     **Este URI contiene el nombre de usuario y la contraseńa de su base de datos. Dele el mismo tratamiento que a la información confidencial y no lo comparta.**
5.  Agregue el valor a la lista Connection Strings en el menú Configuration de su aplicación web de Azure:

     ![WebSiteConnectionStrings](./media/partner-mongodb-web-sites-dotnet-use-mongolab/focus-mongolab-websiteconnectionstring.png)
6.  Para **Name**, especifique MONGOLAB\_URI.
7.  Para **Value**, pegue la cadena de conexión que se obtuvo en la sección anterior.
8.  Seleccione **Custom** en la lista desplegable Type (en lugar del valor predeterminado **SQLAzure**).
9.  En Visual Studio, instale el controlador de Mongo C\# seleccionando **Tools \> Library Package Manager \> Package Manager Console**. En la consola del administrador de paquetes, escriba **Install-Package mongocsharpdriver** y presione **Entrar**.
10. Configure un enlace en su código para obtener su URI de conexión de MongoLab desde una variable de entorno:

        using MongoDB.Driver;  
 		...
 		private string connectionString = System.Environment.GetEnvironmentVariable("CUSTOMCONNSTR_MONGOLAB_URI");
 		...
 		MongoServer server = MongoServer.Create(connectionString);  

    Nota: Azure agrega el prefijo **CUSTOMCONNSTR\_** a la cadena de conexión declarada originalmente, razón por la cual el código hace referencia a **CUSTOMCONNSTR\_MONGOLAB\_URI.** en vez de a **MONGOLAB\_URI**.

Ahora, continuemos con el tutorial completo...

Aprovisionamiento de la base de datos
-------------------------------------

[WACOM.INCLUDE [howto-provision-mongolab](../includes/howto-provision-mongolab.md)]

Creación de la aplicación
-------------------------

En esta sección, trataremos la creación de un proyecto de C\# ASP.NET Visual Studio y abordaremos el uso del controlador de C\# MongoDB para crear una sencilla aplicación de toma de notas. Si desea visitar su sitio web, escriba una nota y vea todas las que quedan.

Esto lo realizará en Visual Studio Express 2012 para Web.

### Creación del proyecto

Para empezar, su aplicación de ejemplo usará una plantilla de Visual Studio. Asegúrese de usar .NET Framework 4.0.

1.  Seleccione **Archivo \> Nuevo proyecto**. Aparecerá el cuadro de diálogo Nuevo proyecto:

     ![NuevoProyecto](./media/partner-mongodb-web-sites-dotnet-use-mongolab/dialog-mongolab-csharp-newproject.png)
2.  Seleccione **Installed \> Templates \> Visual C\# \> Web**.
3.  Seleccione **.NET Framework 4** en el menú desplegable de la versión .NET (*nota: Framework 4.5 no funciona en este momento*).

    ![ProjectFramework](./media/partner-mongodb-web-sites-dotnet-use-mongolab/focus-dotNet-Framework4-mongolab.png)
4.  Elija **ASP.NET MVC 4 Web Application**.
5.  Escriba *mongoNotes* como **Project Name**. Si elige un nombre diferente, tendrá que modificar el código proporcionado a lo largo del tutorial.
6.  Haga clic en **OK**. Aparecerá el diálogo de la plantilla del proyecto:

     ![PlantillaProyecto](./media/partner-mongodb-web-sites-dotnet-use-mongolab/dialog-mongolab-csharp-projecttemplate.png)
7.  Seleccione **Internet Application** y haga clic en **OK**. De este modo se creará el proyecto.
8.  Seleccione **Tools \> Library Package Manager \> Package Manager Console**. En la consola del administrador de paquetes, escriba **Install-Package mongocsharpdriver** y presione **Entrar**.

     ![PMConsole](./media/partner-mongodb-web-sites-dotnet-use-mongolab/focus-mongolab-csharp-pmconsole.png)
     El controlador de MongoDB C\# se integra con el proyecto y la línea siguiente se agrega automáticamente al archivo *packages.config*:

         < package id="mongocsharpdriver" version="1.8" targetFramework="net40" / >

### Incorporación de un modelo de nota

En primer lugar, establezca un modelo para Notes, con tan solo una fecha y contenido de texto.

1.  Haga clic con el botón secundario en **Models**, en el explorador de soluciones y seleccione **Add \> Class**. Llame a esta nueva clase *Note.cs*.
2.  Reemplace el código generado automáticamente para esta clase por el siguiente:

         using System;
         using System.Collections.Generic;
         using System.Linq;
         using System.Web;
         using MongoDB.Bson.Serialization.Attributes;
         using MongoDB.Bson.Serialization.IdGenerators;
         using MongoDB.Bson;
                    
         namespace mongoNotes.Models
         {
             public class Note
             {
                 public Note()
                 {
                     Date = DateTime.UtcNow;
                 }
                    
                 private DateTime date;
            
                 [BsonId(IdGenerator = typeof(CombGuidGenerator))]
                 public Guid Id { get; set; }
            
                 [BsonElement("Note")]
                 public string Text { get; set; }
            
                 [BsonElement("Date")]
                 public DateTime Date {
                     get { return date.ToLocalTime(); }
                     set { date = value;}
                 }
             }
         }

### Incorporación de un nivel de acceso de datos

Es importante establecer una forma de obtener acceso a MongoDB para recuperar y guardar las notas. Su nivel de acceso de datos usará el modelo de Note y se enlazará a HomeController más adelante.

1.  Haga clic con el botón secundario en **mongoNotes**, en el Explorador de soluciones, y seleccione **Add \> New Folder**. Llame a la carpeta **DAL**.
2.  Haga clic con el botón secundario en **DAL**, en el explorador de soluciones, y seleccione **Add \> Class**. Llame a esta nueva clase *Dal.cs*.
3.  Reemplace el código generado automáticamente para esta clase por el siguiente:

         using System;
         using System.Collections.Generic;
         using System.Linq;
         using System.Web;
         using mongoNotes.Models;
         using MongoDB.Driver;
         using System.Configuration;

         namespace mongoNotes
         {
             public class Dal : IDisposable
             {
                 private MongoServer mongoServer = null;
                 private bool disposed = false;
            
                 private string connectionString = System.Environment.GetEnvironmentVariable("CUSTOMCONNSTR_MONGOLAB_URI");
            
                 private string dbName = "myMongoApp";
                 private string collectionName = "Notes";
            
                 // Constructor predeterminado.        
                 public Dal()
                 {
                 }
               
                 public List GetAllNotes()
                 {
                     try
                     {
                         MongoCollection collection = GetNotesCollection();
                         return collection.FindAll().ToList();
                     }
                     catch (MongoConnectionException)
                     {
                         return new List();
                     }
                 }
            
                 // Cree una nota e insértela en la colección de MongoDB.
                 public void CreateNote(Note note)
                 {
                     MongoCollection collection = getNotesCollectionForEdit();
                     try
                     {
                         collection.Insert(note, SafeMode.True);
                     }
                     catch (MongoCommandException ex)
                     {
                         string msg = ex.Message;
                     }
                 }
            
                 private MongoCollection GetNotesCollection()
                 {
                     MongoServer server = MongoServer.Create(connectionString);
                     MongoDatabase database = server[dbName];
                     MongoCollection noteCollection = database.GetCollection(collectionName);
                     return noteCollection;
                 }
            
                 private MongoCollection getNotesCollectionForEdit()
                 {
                     MongoServer server = MongoServer.Create(connectionString);
                     MongoDatabase database = server[dbName];
                     MongoCollection notesCollection = database.GetCollection(collectionName);
                     return notesCollection;
                 }
            
                 # region IDisposable
            
                 public void Dispose()
                 {
                     this.Dispose(true);
                     GC.SuppressFinalize(this);
                 }
            
                 protected virtual void Dispose(bool disposing)
                 {
                     if (!this.disposed)
                     {
                         if (disposing)
                         {
                             if (mongoServer != null)
                             {
                                 this.mongoServer.Disconnect();
                             }
                         }
                     }
            
                     this.disposed = true;
                 }
            
                 # endregion
             }
         }

4.  Anote el siguiente código arriba:

         private string connectionString = System.Environment.GetEnvironmentVariable("CUSTOMCONNSTR_MONGOLAB_URI");
         private string dbName = "myMongoApp";  

    Llegados a este punto, accederá a una variable de entorno que configurará más adelante. Si tiene una instancia de mongo local que se ejecuta para fines de desarrollo, es posible que desee establecer temporalmente este valor en "localhost".

	Establezca también el nombre de la base de datos. Concretamente, configure el valor **dbName** con el nombre que ha escrito al aprovisionar el complemento MongoLab.

1. Finalmente, examine el código siguiente en **GetNotesCollection()**:

        MongoServer server = MongoServer.Create(connectionString);
        MongoDatabase database = server[dbName];
        MongoCollection notesCollection = database.GetCollection  

    No es necesario cambiar nada aquí; simplemente debe saber cómo conseguir un objeto MongoCollection para realizar inserciones, actualizaciones y consultas como las siguientes en **GetAllNotes()**:

        collection.FindAll().ToList();

Para obtener más información acerca de cómo aprovechar el controlador de C\# MongoDB, consulte [CSharp Driver QuickStart](http://www.mongodb.org/display/DOCS/CSharp+Driver+Quickstart "CSharp Driver Quickstart") en mongodb.org.

### Incorporación de una vista de creación

Ahora agregará una vista para crear una nota nueva.

1.  Haga clic con el botón secundario en **Views \> Home**, en el Explorador de soluciones, y seleccione **Add \> View**. Llame a esta nueva vista **Create** y haga clic en **Add**.
2.  Reemplace el código generado automáticamente para esta vista (**Create.cshtml**) por el siguiente:

		@model mongoNotes.Models.Note
		
		<script src="@Url.Content("~/Scripts/jquery-1.5.1.min.js")" type="text/javascript"></script>
		<script src="@Url.Content("~/Scripts/jquery.validate.min.js")" type="text/javascript"></script>
		<script src="@Url.Content("~/Scripts/jquery.validate.unobtrusive.min.js")" type="text/javascript"></script>
		
		@using (Html.BeginForm("Create", "Home")) {
		    @Html.ValidationSummary(true)
		    <fieldset>
		        <legend>New Note</legend>
		        <h3>New Note</h3>		
		        <div class="editor-label">
		            @Html.LabelFor(model => model.Text)
		        </div>
		        <div class="editor-field">
		            @Html.EditorFor(model => model.Text)
		        </div>
		       <p>
		       	    <input type="submit" value="Create" />
		   	   </p>
		   </fieldset>
		}

### Modificación de index.cshtml

A continuación, cree un diseńo sencillo para ver y crear notas en su sitio web.

1.  Abra **Index.cshtml** debajo de **Views \> Home** y reemplace su contenido por el siguiente:

		@model IEnumerable<mongoNotes.Models.Note>
		
		@{
		    ViewBag.Title = "Notes";
		}
		
		<h2>My Notes</h2>

		<table border="1">
		    <tr>
		        <th>Date</th>
		        <th>Note Text</th>		
		    </tr>
		
		@foreach (var item in Model) {
		    <tr>
		        <td>
		            @Html.DisplayFor(modelItem => item.Date)
		        </td>
		        <td>
		            @Html.DisplayFor(modelItem => item.Text)
		        </td>		
		    </tr>
		}
		
		</table>
		<div>  @Html.Partial("Create", new mongoNotes.Models.Note())</div>

### Modificación de HomeController.cs

Finalmente, HomeController tiene que crear instancias de su nivel de acceso de datos y aplicarlas en sus vistas.

1.  Abra **HomeController.cs** debajo de **Controllers** en el Explorador de soluciones y reemplace su contenido por el siguiente:

         using System;
         using System.Collections.Generic;
         using System.Linq;
         using System.Web;
         using System.Web.Mvc;
         using mongoNotes.Models;
         using System.Configuration;
            
         namespace mongoNotes.Controllers
         {
             public class HomeController : Controller, IDisposable
             {
                 private Dal dal = new Dal();
                 private bool disposed = false;
                 //
                 // GET: /Task/
            
                 public ActionResult Index()
                 {
                     return View(dal.GetAllNotes());
                 }
            
                 //
                 // GET: /Task/Create
            
                 public ActionResult Create()
                 {
                     return View();
                 }
            
                 //
                 // POST: /Task/Create
            
                 [HttpPost]
                 public ActionResult Create(Note note)
                 {
                     try
                     {
                         dal.CreateNote(note);
                         return RedirectToAction("Index");
                     }
                     catch
                     {
                         return View();
                     }
                 }
            
                 public ActionResult About()
                 {
                     return View();
                 }
            
                 # region IDisposable
            
                 new protected void Dispose()
                 {
                     this.Dispose(true);
                     GC.SuppressFinalize(this);
                 }
            
                 new protected virtual void Dispose(bool disposing)
                 {
                     if (!this.disposed)
                     {
                         if (disposing)
                         {
                             this.dal.Dispose();
                         }
                     }
            
                     this.disposed = true;
                 }
            
                 # endregion
            
             }
         }

Implementación de la aplicación
-------------------------------

Ahora que la aplicación se ha desarrollado, es el momento de crear un sitio web de Azure para hospedarla, configurar el sitio web e implementar el código. Es fundamental para esta sección el uso de la cadena de conexión de MongoDB (URI). Va a configurar una variable de entorno en su sitio web con este URI para mantener el URI separado del código. Debe tratar el URI como información confidencial debido a que contiene credenciales para conectarse a su base de datos.

### Creación de un sitio web y obtención del archivo de configuración de publicación

Crear un sitio web en Azure es muy sencillo, especialmente porque Azure genera automáticamente un perfil de publicación para Visual Studio.

1.  En el Portal de Azure, haga clic en **New**.

     ![Nuevo](./media/partner-mongodb-web-sites-dotnet-use-mongolab/button-new.png)
2.  Seleccione **Proceso \> Sitio web \> Quick Create**.

     ![CrearSitio](./media/partner-mongodb-web-sites-dotnet-use-mongolab/screen-mongolab-newwebsite.png)
3.  Escriba un prefijo de una dirección URL. Elija el nombre que prefiera, pero tenga en cuenta que debe ser exclusivo (puede que "mongoNotes" no esté disponible).
4.  Haga clic en **Create Web Site**.
5.  Cuando se complete la creación del sitio web, haga clic en el nombre del sitio web en la lista de sitios web. Aparece el panel del sitio web.

     ![PanelDelSitioWeb](./media/partner-mongodb-web-sites-dotnet-use-mongolab/screen-mongolab-websitedashboard.png)
6.  Haga clic en **Download publish profile** debajo de **quick glance** y guarde el archivo .PublishSettings en el directorio que quiera.

     ![DescargarPerfilPublicación](./media/partner-mongodb-web-sites-dotnet-use-mongolab/button-website-downloadpublishprofile.png)

### Obtención de la cadena de conexión de MongoLab

[WACOM.INCLUDE [howto-get-connectioninfo-mongolab](../includes/howto-get-connectioninfo-mongolab.md)]

### Incorporación de la cadena de conexión a las variables de entorno del sitio web

[WACOM.INCLUDE [howto-save-connectioninfo-mongolab](../includes/howto-save-connectioninfo-mongolab.md)]

### Publicación del sitio web

1.  En Visual Studio, haga clic con el botón secundario en el proyecto **mongoNotes** del Explorador de soluciones y seleccione **Publicar**. Aparecerá el cuadro de diálogo de publicación:

     ![Publicar](./media/partner-mongodb-web-sites-dotnet-use-mongolab/dialog-mongolab-vspublish.png)
2.  Haga clic en **Import** y seleccione el archivo .PublishSettings del directorio de descarga que quiera. Este archivo rellena automáticamente los valores del diálogo para la publicación.
3.  Haga clic en **Validate Connection** para probar el archivo.
4.  Si la comprobación es correcta, haga clic en **Publicar**. Cuando haya terminado la publicación, se abrirá una nueva pestańa de explorador y aparecerá el sitio web.
5.  Realice anotaciones de texto, haga clic en **Create** y observe los resultados.

     ![HolaMongoAzure](./media/partner-mongodb-web-sites-dotnet-use-mongolab/screen-mongolab-sampleapp.png)

Administración de la base de datos
----------------------------------

[WACOM.INCLUDE [howto-access-mongolab-ui](../includes/howto-access-mongolab-ui.md)]

ˇEnhorabuena! Acaba de iniciar una aplicación C\# ASP.NET rcon el respaldo de una base de datos MongoDB hospedada en MongoLab! Ahora que tiene una base de datos MongoLab, puede ponerse en contacto con <support@mongolab.com> en caso de que tenga preguntas o dudas sobre su base de datos o si necesita ayuda con MongoDB o el controlador de C\#. ˇBuena suerte!

