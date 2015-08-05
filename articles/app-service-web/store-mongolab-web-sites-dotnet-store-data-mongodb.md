<properties 
	pageTitle="Creación de una aplicación web .NET en Azure con MongoDB y el complemento MongoLab" 
	description="Aprenda a crear una aplicación web ASP.NET en el Servicio de aplicaciones de Azure que almacena datos en MongoDB hospedada por MongoLab." 
	tags="azure-classic-portal"
	services="app-service\web" 
	documentationCenter=".net" 
	authors="chrisckchang" 
	manager="partners@mongolab.com" 
	editor="mollybos"/>

<tags 
	ms.service="app-service-web" 
	ms.workload="web" 
	ms.tgt_pltfrm="na" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="04/20/2015" 
	ms.author="chris@mongolab.com"/>



# Creación de una aplicación web .NET en Azure con MongoDB y el complemento MongoLab

  	<!-- The MongoLab workflow is not yet supported in the Preview Portal -->

<p><em>Por Eric Sedor, MongoLab</em></p>

¡Saludos, aventurero! Bienvenido a MongoDB como servicio. En este tutorial, aprenderá lo siguiente:

1. [Aprovisionamiento de la base de datos][provision]: el complemento [MongoLab](http://mongolab.com) de Azure Marketplace le proporcionará una base de datos MongoDB que se hospeda en la nube de Azure y que administra la plataforma de base de datos en la nube de MongoLab.
1. [Creación de la aplicación][create]: será una aplicación ASP.NET MVC sencilla en C# para realizar anotaciones.
1. [Implementación de la aplicación][deploy]: uniendo unos cuantos enlaces de configuración, insertaremos nuestro código en [Aplicaciones web del Servicio de aplicaciones](http://go.microsoft.com/fwlink/?LinkId=529714) en un abrir y cerrar de ojos.
1. [Administración de la base de datos][manage]: por último, le mostraremos el portal de administración de base de datos basado en web de MongoLab, donde puede buscar, visualizar y modificar los datos con facilidad.

En cualquier momento de este tutorial puede enviar un correo electrónico a [support@mongolab.com](mailto:support@mongolab.com) si tiene alguna pregunta.

## Inicio rápido
Si ya dispone de una aplicación web en el Servicio de aplicaciones de Azure con la que desea trabajar o está algo familiarizado con Azure Marketplace, use esta sección para empezar a trabajar rápidamente. De lo contrario, vaya a la sección [Aprovisionamiento de la base de datos][provision] a continuación.
 
1. Abra Azure Marketplace haciendo clic en **Nuevo** > **Marketplace**.  
	<!-- ![Store][button-store] -->

1. Compre el complemento MongoLab.
	![MongoLab][entry-mongolab]

1. En la lista de complementos, haga clic en el complemento MongoLab y luego en **Información de conexión**. 
	![BotónConnectionInfo][button-connectioninfo]

1. Copie MONGOLAB_URI en el Portapapeles. 
	![ConnectionInfoScreen][screen-connectioninfo]
	**Este URI contiene el nombre de usuario y la contraseña de su base de datos. Trátelo como información confidencial y no lo comparta.**

1. Agregue el valor a la lista Cadenas de conexión en el menú Configuración de su aplicación web de Azure:
	![WebSiteConnectionStrings][focus-website-connectinfo]

1. En **Name** (Nombre), escriba MONGOLAB_URI.

1. En **Value** (Valor), pegue la cadena de conexión que se obtuvo en la sección anterior.

1. Seleccione **Custom** (Personalizado) en la lista desplegable Type (Tipo), en lugar del valor predeterminado **SQLAzure**.

1. En Visual Studio, instale el controlador C# de Mongo seleccionando **Tools > Library Package Manager > Package Manager Console** (Herramientas > Administrador de paquetes de la biblioteca > Consola del administrador de paquetes). En la consola del administrador de paquetes, escriba **Install-Package mongocsharpdriver** y pulse **Entrar**.

1. Configure un enlace en su código para obtener su URI de conexión de MongoLab desde una variable de entorno:

        using MongoDB.Driver;  
        ...
        private string connectionString = System.Environment.GetEnvironmentVariable("CUSTOMCONNSTR_MONGOLAB_URI");
        ...
        MongoUrl url = new MongoUrl(connectionString);
        MongoClient client = new MongoClient(url);

> **Nota:** Azure agrega el prefijo **CUSTOMCONNSTR_** a la cadena de conexión declarada originalmente, razón por la cual el código hace referencia a **CUSTOMCONNSTR_MONGOLAB_URI.** en vez de a **MONGOLAB_URI**.

Ahora, continuemos con el tutorial completo...

<a name="provision"></a>
## Aprovisionamiento de la base de datos

[AZURE.INCLUDE [howto-provision-mongolab](../../includes/howto-provision-mongolab.md)]

<a name="create"></a>
## Creación de la aplicación

En esta sección, trataremos la creación de un proyecto de C# ASP.NET Visual Studio y abordaremos el uso del controlador de C# MongoDB para crear una sencilla aplicación de notas. Si desea poder visitar su aplicación web, escriba una nota y vea todas las que quedan.

Este desarrollo lo realizará en Visual Studio Express 2013 para Web.

### Creación del proyecto
Para empezar, la aplicación de ejemplo usará una plantilla de Visual Studio. Asegúrese de usar .NET Framework 4.5.

1. Seleccione **File > New Project** (Archivo > Nuevo proyecto). Aparecerá el cuadro de diálogo New Project (Nuevo proyecto):
	![NuevoProyecto][dialog-mongolab-csharp-newproject]

1. Seleccione **Installed > Templates > Visual C# > Web** (Instalado > Plantillas > Visual C# > Web).

1. Seleccione **.NET Framework 4.5** en el menú desplegable de versiones de .NET.

1. Seleccione **MVC Application** (Aplicación de MVC).

1. Escriba _mongoNotes_ como **Project Name** (Nombre de proyecto). Si elige otro nombre, tendrá que modificar el código proporcionado a lo largo del tutorial.

1. Seleccione **Tools > Library Package Manager > Package Manager Console** (Herramientas > Administrador de paquetes de la biblioteca > Consola del administrador de paquetes). En la consola del administrador de paquetes, escriba **Install-Package mongocsharpdriver** y pulse **Entrar**. 
	![PMConsole][focus-mongolab-csharp-pmconsole] El controlador C# de MongoDB se integra con el proyecto y la línea siguiente se agrega automáticamente al archivo _packages.config_:

        < package id="mongocsharpdriver" version="1.9.2" targetFramework="net45" / >

### Incorporación de un modelo de nota
En primer lugar, establezca un modelo para Notes, con tan solo una fecha y contenido de texto.

1. Haga clic con el botón secundario en **Modelos** en el Explorador de soluciones y seleccione **Agregar > Clase**. Llame a esta nueva clase *Note.cs*.

1. Reemplace el código generado automáticamente para esta clase por el siguiente:

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
Es importante establecer una forma de obtener acceso a MongoDB para recuperar y guardar las notas. La capa de acceso a datos usará el modelo Note y se enlazará a HomeController más adelante.

1. Haga clic con el botón secundario en el proyecto **mongoNotes** en el Explorador de soluciones y seleccione **Agregar > Nueva carpeta**. Llame a la carpeta **DAL**.

1. Haga clic con el botón secundario en **DAL** en el Explorador de soluciones y seleccione **Agregar > Clase**. Llame a esta nueva clase *Dal.cs*.

1. Reemplace el código generado automáticamente para esta clase por el siguiente:

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
                MongoUrl url;
        
                private string dbName = "myMongoApp";
                private string collectionName = "Notes";
        
                // Default constructor.        
                public Dal()
                {
                    url = new MongoUrl(connectionString);
                }
           
                public List<Note> GetAllNotes()
                {
                    try
                    {
                        MongoCollection<Note> collection = GetNotesCollection();
                        return collection.FindAll().ToList<Note>();
                    }
                    catch (MongoConnectionException)
                    {
                        return new List<Note>();
                    }
                }
        
                // Creates a Note and inserts it into the collection in MongoDB.
                public void CreateNote(Note note)
                {
                    MongoCollection<Note> collection = getNotesCollectionForEdit();
                    try
                    {
                        collection.Insert(note);
                    }
                    catch (MongoCommandException ex)
                    {
                        string msg = ex.Message;
                    }
                }
        
                private MongoCollection<Note> GetNotesCollection()
                {
                    MongoClient client = new MongoClient(url);
                    mongoServer = client.GetServer();
                    MongoDatabase database = mongoServer.GetDatabase(dbName);
                    MongoCollection<Note> noteCollection = database.GetCollection<Note>(collectionName);
                    return noteCollection;
                }
        
                private MongoCollection<Note> getNotesCollectionForEdit()
                {
                    MongoClient client = new MongoClient(url);
                    mongoServer = client.GetServer();
                    MongoDatabase database = mongoServer.GetDatabase(dbName);
                    MongoCollection<Note> notesCollection = database.GetCollection<Note>(collectionName);
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

1. Anote el siguiente código arriba:
            
        private string connectionString = System.Environment.GetEnvironmentVariable("CUSTOMCONNSTR_MONGOLAB_URI");
        private string dbName = "myMongoApp";  
Llegados a este punto, accederá a una variable de entorno que configurará más adelante. Si tiene una instancia de mongo local que se ejecuta para fines de desarrollo, es posible que desee establecer temporalmente este valor en "localhost".
  
  Establezca también el nombre de la base de datos. Concretamente, establezca el valor **dbName** en el nombre que escribió al aprovisionar el complemento MongoLab.

1. Por último, examine el código siguiente en **GetNotesCollection()**:  

        MongoClient client = new MongoClient(url);
        mongoServer = client.GetServer();
        MongoDatabase database = mongoServer.GetDatabase(dbName);
        MongoCollection<Note> noteCollection = database.GetCollection<Note>(collectionName);
  No es necesario cambiar nada aquí; basta con que sepa que así es cómo se obtiene un objeto MongoCollection para realizar inserciones, actualizaciones y consultas como, por ejemplo, las siguientes en **GetAllNotes()**:

        collection.FindAll().ToList<Note>();

Para obtener más información sobre cómo aprovechar el controlador C# de MongoDB, consulte la página [CSharp Driver QuickStart](http://www.mongodb.org/display/DOCS/CSharp+Driver+Quickstart "CSharp Driver Quickstart") en mongodb.org.

### Incorporación de una vista de creación
Ahora agregará una vista para crear una nota nueva.

1. Haga clic con el botón secundario en la entrada **Vistas > Inicio** en el Explorador de soluciones y seleccione **Agregar > Vista**. Llame a esta nueva vista **Create** y haga clic en **Agregar**.

1. Reemplace el código generado automáticamente para esta vista (**Create.cshtml**) por el siguiente:

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
A continuación, cree un diseño sencillo para ver y crear notas en la aplicación web.

1. Abra **Index.cshtml** en **Vistas > Inicio** y reemplace su contenido por el siguiente:  

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

1. Abra **HomeController.cs** en **Controladores** en el Explorador de soluciones y reemplace su contenido por el siguiente:  

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
    
<a name="deploy"></a>
## Implementación de la aplicación

Ahora que desarrolló la aplicación, es el momento de crear una aplicación web en el Servicio de aplicaciones de Azure para hospedarla, configurar la aplicación web e implementar el código. Es fundamental para esta sección el uso de la cadena de conexión de MongoDB (URI). Va a configurar una variable de entorno en su aplicación web con este URI para mantener el URI separado del código. Debe tratar el URI como información confidencial debido a que contiene credenciales para conectarse a su base de datos.

### Creación de una aplicación web y obtención del archivo de configuración de publicación
Crear una aplicación web en el Servicio de aplicaciones de Azure es muy sencillo, especialmente porque Azure genera automáticamente un perfil de publicación para Visual Studio.

1. En el portal de Azure, haga clic en **Nuevo**. ![Nuevo][button-new]

1. Seleccione **Proceso > Aplicación web > Creación rápida**.
	<!-- ![CreateWebApp][screen-mongolab-newwebsite] -->

1. Escriba un prefijo de dirección URL. Elija el nombre que prefiera, pero tenga en cuenta que debe ser exclusivo (puede que "mongoNotes" no esté disponible).

1. Haga clic en **Crear aplicación web**.

1. Cuando concluya la creación de la aplicación web, haga clic en el nombre de la aplicación web en la lista de aplicaciones Web. Aparecerá el panel de la aplicación web. ![WebAppDashboard][screen-mongolab-websitedashboard]

1. Haga clic en **Descargar perfil de publicación** en **vista rápida** y guarde el archivo .PublishSettings en el directorio que quiera. ![DescargarPerfilPublicación][button-website-downloadpublishprofile]

También puede configurar una aplicación web directamente desde Visual Studio. Al vincular la cuenta de Azure con Visual Studio, siga las indicaciones para configurar una aplicación web desde ahí. Cuando haya terminado, solo tiene que hacer clic con el botón secundario en el nombre del proyecto en el Explorador de soluciones para realizar la implementación en Azure. Aún así, tiene que configurar la cadena de conexión de MongoLab, como se detalla en los pasos siguientes.

### Obtención de la cadena de conexión de MongoLab

[AZURE.INCLUDE [howto-get-connectioninfo-mongolab](../../includes/howto-get-connectioninfo-mongolab.md)]

### Incorporación de la cadena de conexión a las variables de entorno de la aplicación web

[AZURE.INCLUDE [howto-save-connectioninfo-mongolab](../../includes/howto-save-connectioninfo-mongolab.md)]

### Publicación de la aplicación web
1. En Visual Studio, haga clic con el botón secundario en el proyecto **mongoNotes** en el Explorador de soluciones y seleccione **Publicar**. Aparecerá el cuadro de diálogo de publicación:  
	<!-- ![Publish][dialog-mongolab-vspublish] -->

1. Haga clic en **Importar** y seleccione el archivo .PublishSettings del directorio de descarga elegido. Este archivo rellena automáticamente los valores del cuadro de diálogo Publicar.

1. Haga clic en **Validar conexión** para probar el archivo.

1. Si la validación es correcta, haga clic en **Publicar**. Cuando haya terminado la publicación, se abrirá una nueva pestaña de explorador y aparecerá la aplicación web.

1. Realice algunas anotaciones de texto, haga clic en **Crear** y vea los resultados. ![HolaMongoAzure][screen-mongolab-sampleapp]

<a name="manage"></a>
## Administración de la base de datos

[AZURE.INCLUDE [howto-access-mongolab-ui](../../includes/howto-access-mongolab-ui.md)]

¡Enhorabuena! Acaba de iniciar una aplicación C# ASP.NET rcon el respaldo de una base de datos MongoDB hospedada en MongoLab! Ahora que tiene una base de datos MongoLab, puede ponerse en contacto con [support@mongolab.com](mailto:support@mongolab.com) en caso de que tenga preguntas o dudas sobre su base de datos o si necesita ayuda con MongoDB o el controlador de C#. ¡Buena suerte!

[AZURE.INCLUDE [app-service-web-whats-changed](../../includes/app-service-web-whats-changed.md)]

[AZURE.INCLUDE [app-service-web-try-app-service](../../includes/app-service-web-try-app-service.md)]

[screen-mongolab-sampleapp]: ./media/store-mongolab-web-sites-dotnet-store-data-mongodb/screen-mongolab-sampleapp.png
[dialog-mongolab-vspublish]: ./media/store-mongolab-web-sites-dotnet-store-data-mongodb/dialog-mongolab-vspublish.png
[button-website-downloadpublishprofile]: ./media/store-mongolab-web-sites-dotnet-store-data-mongodb/button-website-downloadpublishprofile.png
[screen-mongolab-websitedashboard]: ./media/store-mongolab-web-sites-dotnet-store-data-mongodb/screen-mongolab-websitedashboard.png
[screen-mongolab-newwebsite]: ./media/store-mongolab-web-sites-dotnet-store-data-mongodb/screen-mongolab-newwebsite.png
[button-new]: ./media/store-mongolab-web-sites-dotnet-store-data-mongodb/button-new.png
[dialog-mongolab-csharp-newproject]: ./media/store-mongolab-web-sites-dotnet-store-data-mongodb/dialog-mongolab-csharp-newproject.png
[dialog-mongolab-csharp-projecttemplate]: ./media/store-mongolab-web-sites-dotnet-store-data-mongodb/dialog-mongolab-csharp-projecttemplate.png
[focus-mongolab-csharp-pmconsole]: ./media/store-mongolab-web-sites-dotnet-store-data-mongodb/focus-mongolab-csharp-pmconsole.png
[button-store]: ./media/store-mongolab-web-sites-dotnet-store-data-mongodb/button-store.png
[entry-mongolab]: ./media/store-mongolab-web-sites-dotnet-store-data-mongodb/entry-mongolab.png
[button-connectioninfo]: ./media/store-mongolab-web-sites-dotnet-store-data-mongodb/button-connectioninfo.png
[screen-connectioninfo]: ./media/store-mongolab-web-sites-dotnet-store-data-mongodb/dialog-mongolab_connectioninfo.png
[focus-website-connectinfo]: ./media/store-mongolab-web-sites-dotnet-store-data-mongodb/focus-mongolab-websiteconnectionstring.png
[provision]: #provision
[create]: #create
[deploy]: #deploy
[manage]: #manage

 

<!----HONumber=July15_HO4-->