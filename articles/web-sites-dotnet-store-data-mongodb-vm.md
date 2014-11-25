<properties linkid="develop-dotnet-website-with-mongodb-vm" urlDisplayName="Website with MongoDB VM" pageTitle=".NET website with MongoDB on a virtual machine - Azure" metaKeywords="Azure Git ASP.NET MongoDB, Git .NET, Git MongoDB, ASP.NET MongoDB, Azure MongoDB, Azure ASP.NET, Azure tutorial" description="A tutorial that teaches you how to use Git to deploy an ASP.NET app to an Azure website connected to MongoDB on a virtual machine." metaCanonical="" services="web-sites,virtual-machines" documentationCenter=".NET" title="Create an Azure website that connects to MongoDB running on a virtual machine in Azure" authors="cephalin" solutions="" manager="wpickett" editor="" />

<tags ms.service="web-sites" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="dotnet" ms.topic="article" ms.date="01/01/1900" ms.author="cephalin" />

# Creación de un sitio web de Azure que se conecta a MongoDB en una máquina virtual en Azure

Puede utilizar Git para implementar una aplicación ASP.NET para un sitio web de Azure. En este tutorial, generará una aplicación de lista de tareas ASP.NET MVC front-end simple que se conecta a una base de datos MongoDB en una máquina virtual en Azure. [MongoDB][MongoDB] es una conocida base de datos NoSQL de alto rendimiento de código abierto. Después de ejecutar y realizar la prueba de la aplicación ASP.NET en el equipo de desarrollo, cargará la aplicación en un sitio web de Azure mediante Git.

[WACOM.INCLUDE [create-account-and-websites-and-vms-note](../includes/create-account-and-websites-and-vms-note.md)]

## Información general

En este tutorial, aprenderá lo siguiente:

-   [Creación de una máquina virtual e instalación de MongoDB][Creación de una máquina virtual e instalación de MongoDB]
-   [Creación y ejecución de la aplicación My Task List ASP.NET en el equipo de desarrollo][Creación y ejecución de la aplicación My Task List ASP.NET en el equipo de desarrollo]
-   [Creación de un sitio web de Azure][Creación de un sitio web de Azure]
-   [Implementación de la aplicación ASP.NET en el sitio web con Git][Implementación de la aplicación ASP.NET en el sitio web con Git]

## Conocimientos previos

El conocimiento de los siguientes aspectos es útil para este tutorial, aunque no es obligatorio:

-   El controlador C# para MongoDB. Para obtener más información sobre el desarrollo de aplicaciones C# en relación con MongoDB, consulte el [Centro de lenguaje CSharp][Centro de lenguaje CSharp] (en inglés) de MongoDB.
-   El marco de la aplicación web ASP.NET. Puede obtener toda la información en el [sitio web de ASP.net][sitio web de ASP.net].
-   El marco de la aplicación web ASP .NET MVC. Puede obtener todo la información al respecto en el [sitio web de ASP.NET MVC][sitio web de ASP.NET MVC] (en inglés).
-   Azure. Puede comenzar leyendo [Azure][Azure].

## Preparación

En esta sección aprenderá cómo crear una máquina virtual en Azure e instalar MongoDB y configurar el entorno de desarrollo.

<span id="virtualmachine"></span></a>

### Creación de una máquina virtual e instalación de MongoDB

En este tutorial se presupone que ha creado una máquina virtual en Azure. Después de crear la máquina virtual, tiene que instalar MongoDB en la misma:

-   Para crear una máquina virtual de Windows e instalar MongoDB, consulte [Instalación de MongoDB en una máquina virtual con Windows Server en Azure][Instalación de MongoDB en una máquina virtual con Windows Server en Azure].
-   También puede crear una máquina virtual de Linux e instalar MongoDB, consulte [Instalación de MongoDB en una máquina virtual con CentOS Linux en Azure][Instalación de MongoDB en una máquina virtual con CentOS Linux en Azure].

Después de haber creado la máquina virtual en Azure e instalado MongoDB, asegúrese de recordar el nombre de DNS de la máquina virtual ("testlinuxvm.cloudapp.net", por ejemplo) y el puerto externo de MongoDB que especificó en el extremo. Necesitará esta información posteriormente en el tutorial.

### Instalación de Visual Studio

Comience a instalar y ejecutar [Visual Studio Express 2013 para Web][Visual Studio Express 2013 para Web] o [Visual Studio 2013][Visual Studio 2013].

Visual Studio es un IDE (siglas en inglés de "entorno de desarrollo integrado"). Usará el IDE para crear aplicaciones de la misma forma que usa Microsoft Word para escribir documentos. Este tutorial usa Microsoft Visual Studio 2013, pero puede usar Microsoft Visual Studio Express 2013, que es una versión gratuita de Microsoft Visual Studio.

<span id="createapp"></span></a>

## Creación y ejecución de la aplicación My Task List ASP.NET en el equipo de desarrollo

En esta sección creará una aplicación ASP.NET denominada "My Task List" mediante Visual Studio. Ejecutará la aplicación localmente, pero se conectará con la máquina virtual en Azure y usará la instancia de MongoDB creada ahí.

### Creación de la aplicación

En Visual Studio, haga clic en **New Project**.

![Página de inicio de nuevo proyecto][Página de inicio de nuevo proyecto]

En la ventana **New Project**, en el panel izquierdo, seleccione **Visual C#** y, a continuación, seleccione **Web**. En el panel intermedio, seleccione **ASP.NET Web Application**. En la parte inferior, utilice el nombre "MyTaskListApp" para el proyecto y, a continuación, haga clic en **OK**.

![Cuadro de diálogo de nuevo proyecto][Cuadro de diálogo de nuevo proyecto]

En el cuadro de diálogo **New ASP.NET Project**, seleccione **MVC** y, a continuación, haga clic en **OK**.

![Seleccione la plantilla MVC][Seleccione la plantilla MVC]

Una vez que se complete el proyecto, aparece la página predeterminada creada por la plantilla.

![Aplicación ASP.NET MVC predeterminada][Aplicación ASP.NET MVC predeterminada]

### Instalación del controlador C# de MongoDB

MongoDB ofrece soporte de cliente para aplicaciones C# a través de un controlador, que tiene que instalar en el equipo de desarrollo local. El controlador C# se encuentra disponible a través de NuGet.

Para instalar el controlador C# de MongoDB:

1.  En el **Explorador de soluciones**, en el proyecto **MyTaskListApp**, haga clic con el botón secundario en **References** y seleccione **Manage NuGetPackages**.

    ![Administración de paquetes de NuGet][Administración de paquetes de NuGet]

2.  En la ventana **Manage NuGet Packages**, en el panel izquierdo, haga clic en **Online**. En el cuadro **Search Online** de la derecha, escriba "mongocsharpdriver". Haga clic en **Install** para instalar el controlador.

    ![Búsqueda del controlador C# de MongoDB][Búsqueda del controlador C# de MongoDB]

3.  Haga clic en **I Accept** para aceptar los términos de licencia de 10gen, Inc.

4.  Haga clic en **Close** una vez que se haya instalado el controlador.
    ![MongoDB C# Driver Installed][MongoDB C# Driver Installed]

El controlador C# de MongoDB está ahora instalado. Las referencias a las bibliotecas **MongoDB.Driver.dll** y **MongoDB.Bson.dll** se han agregado al proyecto.

![Referencias del controlador C# de MongoDB][Referencias del controlador C# de MongoDB]

### Incorporación de un modelo

En el **Explorador de soluciones**, haga clic con el botón secundario en la carpeta *Models* y utilice **Add** para agregar un nuevo valor en **Class** y utilice el nombre de *TaskModel.cs*. En *TaskModel.cs*, reemplace el código existente por el siguiente código:

    using System;
    using System.Collections.Generic;
    using System.Linq;
    using System.Web;
    using MongoDB.Bson.Serialization.Attributes;
    using MongoDB.Bson.Serialization.IdGenerators;
    using MongoDB.Bson;

    namespace MyTaskListApp.Models
    {
        public class MyTask
        {
            [BsonId(IdGenerator = typeof(CombGuidGenerator))]
            public Guid Id { get; set; }

            [BsonElement("Name")]
            public string Name { get; set; }

            [BsonElement("Category")]
            public string Category { get; set; }

            [BsonElement("Date")]
            public DateTime Date { get; set; }

            [BsonElement("CreatedDate")]
            public DateTime CreatedDate { get; set; }

        }
    }

### Incorporación de un nivel de acceso de datos

En el **Explorador de soluciones**, haga clic con el botón secundario en el proyecto *MyTaskListApp* y utilice **Add** para agregar una carpeta con la opción **New Folder** que tenga el nombre de *DAL*. Haga clic con el botón secundario en la carpeta *DAL* y utilice la opción **Add** para agregar una clase en **Class**. Utilice el nombre de archivo de clase *Dal.cs*. En *Dal.cs*, reemplace el código existente por el siguiente código:

    using System;
    using System.Collections.Generic;
    using System.Linq;
    using System.Web;
    using MyTaskListApp.Models;
    using MongoDB.Driver;
    using System.Configuration;

    namespace MyTaskListApp
    {
        public class Dal : IDisposable
        {
            private MongoServer mongoServer = null;
            private bool disposed = false;

            // To do: update the connection string with the DNS name
            // or IP address of your server. 
            //For example, "mongodb://testlinux.cloudapp.net"
            private string connectionString = "mongodb://<vm-dns-name>";

            // This sample uses a database named "Tasks" and a 
            //collection named "TasksList".  The database and collection 
            //will be automatically created if they don't already exist.
            private string dbName = "Tasks";
            private string collectionName = "TasksList";

            // Default constructor.        
            public Dal()
            {
            }        

            // Gets all Task items from the MongoDB server.        
            public List<MyTask> GetAllTasks()
            {
                try
                {
                    MongoCollection<MyTask> collection = GetTasksCollection();
                    return collection.FindAll().ToList<MyTask>();
                }
                catch (MongoConnectionException)
                {
                    return new List<MyTask >();
                }
            }

            // Creates a Task and inserts it into the collection in MongoDB.
            public void CreateTask(MyTask task)
            {
                MongoCollection<MyTask> collection = GetTasksCollectionForEdit();
                try
                {
                    collection.Insert(task, SafeMode.True);
                }
                catch (MongoCommandException ex)
                {
                    string msg = ex.Message;
                }
            }

            private MongoCollection<MyTask> GetTasksCollection()
            {
                MongoServer server = MongoServer.Create(connectionString);
                MongoDatabase database = server[dbName];
                MongoCollection<MyTask> todoTaskCollection = database.GetCollection<MyTask>(collectionName);
                return todoTaskCollection;
            }

            private MongoCollection<MyTask> GetTasksCollectionForEdit()
            {
                MongoServer server = MongoServer.Create(connectionString);
                MongoDatabase database = server[dbName];
                MongoCollection<MyTask> todoTaskCollection = database.GetCollection<MyTask>(collectionName);
                return todoTaskCollection;
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

### Incorporación de un controlador

Abra el archivo *Controllers\\HomeController.cs* en el **Explorador de soluciones** y reemplace el código existente por lo siguiente:

    using System;
    using System.Collections.Generic;
    using System.Linq;
    using System.Web;
    using System.Web.Mvc;
    using MyTaskListApp.Models;
    using System.Configuration;

    namespace MyTaskListApp.Controllers
    {
        public class HomeController : Controller, IDisposable
        {
            private Dal dal = new Dal();
            private bool disposed = false;
            //
            // GET: /MyTask/

            public ActionResult Index()
            {
                return View(dal.GetAllTasks());
            }

            //
            // GET: /MyTask/Create

            public ActionResult Create()
            {
                return View();
            }

            //
            // POST: /MyTask/Create

            [HttpPost]
            public ActionResult Create(MyTask task)
            {
                try
                {
                    dal.CreateTask(task);
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

### Configuración del estilo del sitio

Para cambiar el título en la parte superior de la página, abra el archivo \*Views\\Shared\\\_Layout.cshtml\* en el **Explorador de soluciones** y reemplace "Application name" en el encabezado de la barra de exploración por "My Task List Application" de manera que tenga la siguiente apariencia:

    @Html.ActionLink("My Task List Application", "Index", "Home", null, new { @class = "navbar-brand" })

Para configurar el menú Task List, abra el archivo *\\Views\\Home\\Index.cshtml* y reemplace el código existente por el siguiente código:

    @model IEnumerable<MyTaskListApp.Models.MyTask>

    @{
        ViewBag.Title = "My Task List";
    }

    <h2>My Task List</h2>

    <table border="1">
        <tr>
            <th>Task</th>
            <th>Category</th>
            <th>Date</th>
            
        </tr>

    @foreach (var item in Model) {
        <tr>
            <td>
                @Html.DisplayFor(modelItem => item.Name)
            </td>
            <td>
                @Html.DisplayFor(modelItem => item.Category)
            </td>
            <td>
                @Html.DisplayFor(modelItem => item.Date)
            </td>
            
        </tr>
    }

    </table>
    <div>  @Html.Partial("Create", new MyTaskListApp.Models.MyTask())</div>

Para agregar la capacidad de crear una nueva tarea, haga clic con el botón secundario en la carpeta *Views\\Home\\* y utilice la opción **Add** para agregar una vista en **View**. Póngale a la vista el nombre *Create*. Reemplace el código por lo siguiente:

    @model MyTaskListApp.Models.MyTask

    <script src="@Url.Content("~/Scripts/jquery-1.10.2.min.js")" type="text/javascript"></script>
    <script src="@Url.Content("~/Scripts/jquery.validate.min.js")" type="text/javascript"></script>
    <script src="@Url.Content("~/Scripts/jquery.validate.unobtrusive.min.js")" type="text/javascript"></script>

    @using (Html.BeginForm("Create", "Home")) {
        @Html.ValidationSummary(true)
        <fieldset>
            <legend>New Task</legend>

            <div class="editor-label">
                @Html.LabelFor(model => model.Name)
            </div>
            <div class="editor-field">
                @Html.EditorFor(model => model.Name)
                @Html.ValidationMessageFor(model => model.Name)
            </div>

            <div class="editor-label">
                @Html.LabelFor(model => model.Category)
            </div>
            <div class="editor-field">
                @Html.EditorFor(model => model.Category)
                @Html.ValidationMessageFor(model => model.Category)
            </div>

            <div class="editor-label">
                @Html.LabelFor(model => model.Date)
            </div>
            <div class="editor-field">
                @Html.EditorFor(model => model.Date)
                @Html.ValidationMessageFor(model => model.Date)
            </div>

            <p>
                <input type="submit" value="Create" />
            </p>
        </fieldset>
    }

El **Explorador de soluciones** debe tener la siguiente apariencia:

![Explorador de soluciones][Explorador de soluciones]

### Establecimiento de la cadena de conexión de MongoDB

En el **Explorador de soluciones**, abra el archivo *DAL/Dal.cs*. Busque la siguiente línea de código:

    private string connectionString = "mongodb://<vm-dns-name>";

Reemplace `<vm-dns-name>` por el nombre de DNS de la máquina virtual que ejecuta el MongoDB que creó en el paso [Creación de una máquina virtual e instalación de MongoDB][Creación de una máquina virtual e instalación de MongoDB] de este tutorial. Para buscar el nombre de DNS de la máquina virtual, diríjase al Portal de administración de Azure, seleccione **Máquinas virtuales** y busque **Nombre de DNS**.

Si el nombre de DNS de la máquina virtual es "testlinuxvm.cloudapp.net" y MongoDB está escuchando en el puerto predeterminado 27017, la línea de la cadena de conexión del código tendrá la siguiente apariencia:

    private string connectionString = "mongodb://testlinuxvm.cloudapp.net";

Si el extremo de la máquina virtual especifica un puerto externo distinto para MongoDB, puede especificar el puerto en la cadena de conexión:

    private string connectionString = "mongodb://testlinuxvm.cloudapp.net:12345";

Para obtener más información sobre las cadenas de conexión de MongoDB, consulte [Connections][Connections].

### Prueba de la implementación local

Para ejecutar la aplicación en el equipo de desarrollo, seleccione **Start Debugging** en el menú **Debug** o presione **F5**. IIS Express se iniciará y se abrirá un explorador y se iniciará la página principal de la aplicación. Puede agregar una nueva tarea, que se agregará a la base de datos de MongoDB que se ejecuta en la máquina virtual en Azure.

![Aplicación My Task List][Aplicación My Task List]

## <span class="short-header">Implementación de la aplicación en un sitio web de Azure</span>Implementación de la aplicación ASP.NET en un sitio web de Azure

En esta sección creará un sitio web e implementará la aplicación My Task List ASP.NET mediante Git.

<span id="createwebsite"></span></a>

### Creación de un sitio web de Azure

En esta sección creará un sitio web de Azure.

1.  Abra su explorador web y diríjase al [Portal de administración de Azure][Portal de administración de Azure]. Inicie sesión con su cuenta de Azure.
2.  En la parte inferior de la página, haga clic en **+New**, a continuación, en **Web Site** y finalmente en **Quick Create**.
3.  Especifique un prefijo único para la dirección URL de la aplicación.
4.  Seleccione una región.
5.  Haga clic en **Crear sitio web**.

![Creación de un sitio web nuevo][Creación de un sitio web nuevo]

1.  En sitio web se creará rápidamente y aparecerá en **Sitios web**.

![WAWSDashboardMyTaskListApp][WAWSDashboardMyTaskListApp]

<span id="deployapp"></span></a>

### Implementación de la aplicación ASP.NET en el sitio web con Git

En esta sección implementará la aplicación My Task List mediante Git.

1.  Haga clic en el nombre del sitio web en **Sitios web** y, a continuación, haga clic en **Panel**. A la derecha, en Quick Glance, haga clic en **Set up deployment from source control**.
2.  En la página **Where is your source code?**, seleccione **Local Git repository** y haga clic en la flecha **Siguiente**.
3.  El repositorio Git debe crearse rápidamente. Anote las instrucciones en la página resultante cuando se usen en la siguiente sección.

    ![El repositorio Git está preparado][El repositorio Git está preparado]

4.  En **Push my local files to Azure** hay instrucciones para la inserción de código en Azure. Las instrucciones tendrán un aspecto similar al siguiente:

    ![Inserción de archivos locales en Azure][Inserción de archivos locales en Azure]

5.  Si no tiene Git instalado, hágalo mediante el vínculo **Get it here** del paso 1.
6.  Siga las instrucciones del paso 2 para confirmar los archivos locales.
7.  Agregue el repositorio remoto de Azure e inserte los archivos en el sitio web de Azure mediante las instrucciones del paso 3.
8.  Cuando se haya completado la implementación, verá la siguiente confirmación:

    ![La implementación se ha completado][La implementación se ha completado]

9.  El sitio web de Azure está ahora disponible. Compruebe la página **Panel** del sitio y el campo **URL del sitio** para buscar la dirección URL del sitio. Siga los procedimientos de este tutorial. El sitio estaría disponible en esta dirección URL: <http://mytasklistapp.azurewebsites.net>.

## Resumen

Ha implementado correctamente la aplicación ASP.NET en un sitio web de Azure. Para ver el sitio, haga clic en el vínculo del campo **URL de sitio** de la página **Panel**. Para obtener más información sobre el desarrollo de aplicaciones C# en relación con MongoDB, consulte el [Centro de lenguaje CSharp][Centro de lenguaje CSharp] (en inglés).

<!-- HYPERLINKS --> <!-- IMAGES --> <!-- TOC BOOKMARKS -->

  [MongoDB]: http://www.mongodb.org
  [Creación de una máquina virtual e instalación de MongoDB]: #virtualmachine
  [Creación y ejecución de la aplicación My Task List ASP.NET en el equipo de desarrollo]: #createapp
  [Creación de un sitio web de Azure]: #createwebsite
  [Implementación de la aplicación ASP.NET en el sitio web con Git]: #deployapp
  [Centro de lenguaje CSharp]: http://docs.mongodb.org/ecosystem/drivers/csharp/
  [sitio web de ASP.net]: http://www.asp.net/
  [sitio web de ASP.NET MVC]: http://www.asp.net/mvc
  [Azure]: http://www.windowsazure.com
  [Instalación de MongoDB en una máquina virtual con Windows Server en Azure]: /es-es/manage/windows/common-tasks/install-mongodb/
  [Instalación de MongoDB en una máquina virtual con CentOS Linux en Azure]: /es-es/manage/linux/common-tasks/mongodb-on-a-linux-vm/
  [Visual Studio Express 2013 para Web]: http://www.microsoft.com/visualstudio/eng/2013-downloads#d-2013-express
  [Visual Studio 2013]: http://www.microsoft.com/visualstudio/eng/2013-downloads
  [Página de inicio de nuevo proyecto]: ./media/web-sites-dotnet-store-data-mongodb-vm/NewProject.png
  [Cuadro de diálogo de nuevo proyecto]: ./media/web-sites-dotnet-store-data-mongodb-vm/NewProjectMyTaskListApp.png
  [Seleccione la plantilla MVC]: ./media/web-sites-dotnet-store-data-mongodb-vm/VS2013SelectMVCTemplate.png
  [Aplicación ASP.NET MVC predeterminada]: ./media/web-sites-dotnet-store-data-mongodb-vm/VS2013DefaultMVCApplication.png
  [Administración de paquetes de NuGet]: ./media/web-sites-dotnet-store-data-mongodb-vm/VS2013ManageNuGetPackages.png
  [Búsqueda del controlador C# de MongoDB]: ./media/web-sites-dotnet-store-data-mongodb-vm/SearchforMongoDBCSharpDriver.png
  [MongoDB C# Driver Installed]: ./media/web-sites-dotnet-store-data-mongodb-vm/MongoDBCsharpDriverInstalled.png
  [Referencias del controlador C# de MongoDB]: ./media/web-sites-dotnet-store-data-mongodb-vm/MongoDBCSharpDriverReferences.png
  [Explorador de soluciones]: ./media/web-sites-dotnet-store-data-mongodb-vm/SolutionExplorerMyTaskListApp.png
  [Connections]: http://www.mongodb.org/display/DOCS/Connections
  [Aplicación My Task List]: ./media/web-sites-dotnet-store-data-mongodb-vm/TaskListAppBlank.png
  [Portal de administración de Azure]: http://manage.windowsazure.com
  [Creación de un sitio web nuevo]: ./media/web-sites-dotnet-store-data-mongodb-vm/WAWSCreateWebSite.png
  [WAWSDashboardMyTaskListApp]: ./media/web-sites-dotnet-store-data-mongodb-vm/WAWSDashboardMyTaskListApp.png
  [El repositorio Git está preparado]: ./media/web-sites-dotnet-store-data-mongodb-vm/RepoReady.png
  [Inserción de archivos locales en Azure]: ./media/web-sites-dotnet-store-data-mongodb-vm/GitInstructions.png
  [La implementación se ha completado]: ./media/web-sites-dotnet-store-data-mongodb-vm/GitDeploymentComplete.png
