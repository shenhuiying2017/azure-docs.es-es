<properties linkid="develop-nodejs-tutorials-web-site-with-sql-database" urlDisplayName="Web site with SQL Database" pageTitle="Node.js web site with SQL Database - Azure tutorial" metaKeywords="" description="Learn how to create a Node.js website that accesses a SQL Database and is deployed to Azure" metaCanonical="" services="web-sites,sql-database" documentationCenter="Node.js" title="Node.js Web Application using the Azure SQL Database" authors="" solutions="" manager="" editor="" />

Aplicación web Node.js utilizando la base de datos SQL de Azure
===============================================================

En este tutorial aprenderá a utilizar Base de datos SQL que la administración de datos de Azure proporciona para almacenar y tener acceso a datos desde una aplicación [Node](http://nodejs.org) hospedada en Azure. En este tutorial se asume que tiene alguna experiencia anterior en el uso de Node y [Git](http://git-scm.com).

Aprenderá a:

-   Usar el Portal de administración de Azure para crear un sitio web de Azure y una base de datos SQL

-   Usar npm (administrador de paquetes de Node) para instalar los módulos de Node

-   Trabajar con una base de datos SQL con el módulo node-sqlserver

-   Usar la configuración de aplicaciones para especificar valores de tiempo de ejecución para una aplicación

Al seguir este tutorial, podrá compilar una aplicación de administración de tareas basadas en web sencilla que permite crear, recuperar y completar tareas. Las tareas se almacenan en Base de datos SQL.

Los archivos del proyecto para este tutorial se almacenarán en un directorio llamado **tasklist** y la aplicación completada tendrá una apariencia similar a la siguiente:

![Página web que muestra una lista de tareas vacía](./media/sql-database-nodejs-use-web-site/sql_todo_final.png)

**Nota:**

Actualmente, el controlador de Microsoft para Node.JS de SQL Server usado en este tutorial está a disposición de los usuarios como versión preliminar y se basa en componentes en tiempo de ejecución solamente disponibles en los sistemas operativos Microsoft Windows y Azure.

**Nota:**

Este tutorial hace referencia a la carpeta **tasklist**. La ruta completa a esta carpeta se omite, debido a que la semántica de la ruta es diferente entre los sistemas operativos. Debe crear esta carpeta en una ubicación a la que le sea fácil tener acceso en su sistema de archivos local, como **\~/node/tasklist** o **c:\\node\\tasklist**

**Nota:**

Muchos de los pasos que se mencionan a continuación usan la línea de comandos. Para realizar estos pasos, utilice la línea de comandos de su sistema operativo, como **cmd.exe** (Windows) o **Bash** (shell de Unix). En los sistemas OS X puede tener acceso a la línea de comandos mediante la aplicación Terminal.

Requisitos previos
------------------

Antes de seguir las instrucciones del presente artículo, debe asegurarse de tener instalados los siguientes elementos:

-   [Node](http://nodejs.org) versión 0.6.14 o superior

-   [Git](http://git-scm.com)

-   Bibliotecas de cliente nativas para Microsoft SQL Server, disponibles como parte de [Microsoft SQL Server 2012 Feature Pack](http://www.microsoft.com/en-us/download/details.aspx?id=29065)

-   Un editor de texto

-   Un explorador web

Creación de un sitio web con base de datos
------------------------------------------

Siga estos pasos para crear un sitio web de Azure y una base de datos SQL:

1.  Inicie sesión en el [Portal de administración de Azure](https://manage.windowsazure.com/).
2.  Haga clic en el icono **+ New**, situado en la parte inferior izquierda del portal.

    ![Crear un sitio web de Azure](./media/sql-database-nodejs-use-web-site/new_website.jpg)

3.  Haga clic en **SITIO WEB** y, a continuación, en **CUSTOM CREATE**.

    ![Crear un sitio web personalizado](./media/sql-database-nodejs-use-web-site/custom_create.png)

    Especifique un valor para **URL**, seleccione **Create a New SQL Database** en la lista desplegable **DATABASE** y seleccione el centro de datos del sitio web en la lista desplegable **REGIÓN**. Haga clic en la flecha que aparece en la parte inferior del cuadro de diálogo.

    ![Rellenar los detalles del sitio web](./media/sql-database-nodejs-use-web-site/website_details_sqlazure.jpg)

4.  Escriba el valor **NAME** de su base de datos, seleccione el tipo de **EDITION** [(WEB o BUSINESS)](http://msdn.microsoft.com/en-us/library/windowsazure/ee621788.aspx), el valor **MAX SIZE** de la base de datos, la clase **COLLATION** y, a continuación, seleccione **NEW SQL Database server**. Haga clic en la flecha que aparece en la parte inferior del cuadro de diálogo.

    ![Rellenar configuración de la base de datos SQL](./media/sql-database-nodejs-use-web-site/database_settings.jpg)

5.  Especifique un nombre y una contraseńa de administrador (y confirme la contraseńa), elija la región donde se creará el nuevo servidor de base de datos SQL y active la casilla **Allow Azure Services to access the server**.

    ![Crear servidor de base de datos SQL](./media/sql-database-nodejs-use-web-site/create_server.jpg)

    Una vez creado el sitio web, se mostrará el texto **Creation of Web Site '[NOMBRESITIO]' completed successfully**. Ahora puede habilitarse la publicación Git.

6.  Haga clic en el nombre del sitio web que se muestra en la lista para abrir el panel QuickStart del sitio web.

    ![Abrir panel del sitio web](./media/sql-database-nodejs-use-web-site/go_to_dashboard.png)

7.  En la parte inferior de la página Quick Start, haga clic en **Set up Git publishing**.

    ![Configuración de la publicación Git](./media/sql-database-nodejs-use-web-site/setup_git_publishing.png)

8.  Para habilitar la publicación Git, debe proporcionar un nombre de usuario y una contraseńa. Tome nota de ambos. (Si ya ha configurado un repositorio Git anteriormente, este paso se omitirá).

    ![Crear credenciales de publicación](./media/sql-database-nodejs-use-web-site/git-deployment-credentials.png)

    Se tardará unos segundos en configurar el repositorio.

9.  Una vez listo, aparecerán instrucciones para publicar los archivos de aplicación en el repositorio. Tome nota de estas instrucciones, las necesitará más adelante.

    ![Instrucciones de Git](./media/sql-database-nodejs-use-web-site/git-instructions.png)

Obtención de información de conexión de la base de datos SQL
------------------------------------------------------------

Para conectarse a la instancia de Base de datos SQL que se ejecuta en Sitios web Azure, se requerirá la siguiente información de conexión. Si desea obtener la información de conexión de Base de datos SQL, siga estos pasos:

1.  En el Portal de administración de Azure, haga clic en **LINKED RESOURCES** y, a continuación, en el nombre de la base de datos.

    ![Recursos vinculados](./media/sql-database-nodejs-use-web-site/linked_resources.jpg)

2.  Haga clic en **View connection strings**.

    ![Cadena de conexión](./media/sql-database-nodejs-use-web-site/connection_string.jpg)

3.  En la sección **ODBC** del cuadro de diálogo resultante, anote la cadena de conexión, puesto que se utilizará más adelante.

Diseńo de la tabla de tareas
----------------------------

Lleve a cabo los siguientes pasos para crear la tabla de base de datos que se utiliza para almacenar elementos de la aplicación de la lista de tareas:

1.  En el Portal de administración de Azure, seleccione la base de datos SQL y haga clic en **MANAGE** en la parte inferior de la página. Si recibe un mensaje que indique que la dirección IP actual no forma parte de las reglas del firewall, seleccione **OK** para agregarla.

    ![botón de administración](./media/sql-database-nodejs-use-web-site/sql-manage.png)

2.  Inicie sesión con el nombre y la contraseńa de inicio de sesión que seleccionó cuando anteriormente creó el servidor de base de datos.

    ![inicio de sesión de administración de base de datos](./media/sql-database-nodejs-use-web-site/sqlazurelogin.png)

3.  En la parte inferior izquierda de la página, seleccione **Design** y, a continuación, **New Table**.

    ![Nueva tabla](./media/sql-database-nodejs-use-web-site/new-table.png)

4.  Escriba "tareas" como el **nombre de la tabla** y marque **Is Identity?** para la columna **ID**.

    ![nombre de tabla definido en tareas y con identidad marcada](./media/sql-database-nodejs-use-web-site/table-name-identity.png)

5.  Cambie **Column1** a **name** y **Column2** a **category**. Haga clic en el botón **Add column** para agregar dos columnas nuevas. La primera columna se debe llamar **created** y debe tener un tipo de **date**. La segunda columna se debe llamar **completed** y debe tener un tipo de **bit**. Ambas columnas nuevas deben estar marcadas como **Is Required?**.

    ![diseńo de tabla completado](./media/sql-database-nodejs-use-web-site/table-columns.png)

6.  Haga clic en el botón **Save** para guardar los cambios en la tabla. Ahora puede cerrar la página de administración de Base de datos SQL.

Instalación de módulos y generación de scaffolding
--------------------------------------------------

En esta sección podrá crear una nueva aplicación Node y usar npm para agregar paquetes de módulos. Para la aplicación de lista de tareas, utilizará los módulos [express](http://expressjs.com) y [node-sqlserver](https://github.com/WindowsAzure/node-sqlserver). El módulo Express proporciona un marco de controlador de vista de modelo para Node, mientras que el módulo node-sqlserver proporciona conectividad a Base de datos SQL de Azure.

### Instalar Express y generar scaffolding

1.  Desde la línea de comandos, cambie los directorios por el directorio **tasklist**. Si el directorio **tasklist** no existe, créelo.

2.  Escriba el siguiente comando para instalar Express.

         npm install express -g

    **Nota:**

    Cuando se usa el parámetro "-g" en algunos sistemas operativos, es posible que reciba un error **Error: EPERM, chmod '/usr/local/bin/express'** y una solicitud para intentar ejecutar la cuenta como administrador. Si esto ocurre, utilice el comando **sudo** para ejecutar npm en un nivel de privilegio más elevado.

    El resultado de este comando debe ser similar al siguiente:

         express@2.5.9 /usr/local/lib/node_modules/express
		├── mime@1.2.4
		├── mkdirp@0.3.0
		├── qs@0.4.2
		└── connect@1.8.7

    **Nota:**

    El parámetro "-g" que se utiliza cuando se instala el módulo Express lo instala de manera global. Esto se realiza para tener acceso al comando **express** para generar scaffolding del sitio web sin tener que escribir información de ruta adicional.

3.  Para crear el scaffolding que se usará para esta aplicación, use el comando **express**:

         express

    El resultado de este comando debe ser similar al siguiente:

         create : .
            create : ./package.json
            create : ./app.js
            create : ./public
            create : ./public/javascripts
            create : ./public/images
            create : ./public/stylesheets
            create : ./public/stylesheets/style.css
            create : ./routes
            create : ./routes/index.js
            create : ./views
            create : ./views/layout.jade
            create : ./views/index.jade
            
            no olvide instalar dependencias:
            $ cd . && npm install

    Después de que se completa este comando, debe tener varios directorios y archivos nuevos en el directorio **tasklist**.

### Instalar módulos adicionales

1.  En la línea de comandos, cambie los directorios por la carpeta **tasklist** y escriba lo siguiente para instalar los módulos descritos en el archivo **package.json**:

         npm install

    El resultado de este comando debe ser similar al siguiente:

         express@2.5.8 ./node_modules/express
		├── mime@1.2.4
		├── qs@0.4.2
		├── mkdirp@0.3.0
		└── connect@1.8.7
		jade@0.26.0 ./node_modules/jade
		├── commander@0.5.2
		└── mkdirp@0.3.0

    Con esto se instalan todos los módulos predeterminados que necesita Express.

2.  A continuación, utilice el siguiente comando para agregar el módulo nconf. La aplicación utilizará este módulo para leer la cadena de conexión de la base de datos desde un archivo de configuración.

    npm install nconf -save

3.  A continuación, descargue la versión binaria del controlador de Microsoft para Node.JS de SQL Server desde el [centro de descarga](http://www.microsoft.com/en-us/download/details.aspx?id=29995).

4.  Extraiga el archivo en el directorio **tasklist\\node\_modules**.

5.  Ejecute el archivo **msnodesql-install.cmd** en el directorio **tasklist\\node\_modules**. Al hacerlo, se creará un subdirectorio **msnodesql** bajo **node\_modules** y los archivos del controlador se trasladarán a esta nueva estructura de directorios.

6.  Elimine el archivo **msnodesql-install.cmd**, puesto que ya no es necesario.

Uso de Base de datos SQL en una aplicación de Node
--------------------------------------------------

En esta sección se extenderá la aplicación básica creada con el comando **express** mediante la modificación del archivo **app.js** existente y creará un nuevo archivo **index.js** para utilizar la base de datos que creó anteriormente.

### Modificar el controlador

1.  En el directorio **tasklist/routes**, abra el archivo **index.js** en un editor de texto.

2.  Reemplace el código existente en el archivo **index.js** por el código siguiente. Esto carga los módulos msnodesql y nconf y luego utiliza nconf para cargar la cadena de conexión desde una variable de entorno llamada **SQL\_CONN** o desde un valor **SQL\_CONN** en el archivo **config.json**.

         var sql = require('msnodesql')
             , nconf = require('nconf');

         nconf.env()
              .file({ file: 'config.json' });
         var conn = nconf.get("SQL_CONN");

3.  Siga agregando al archivo **index.js** mediante la adición de los métodos **index** y **updateItem**. El método **index** devuelve todas las tareas no completadas desde la base de datos, mientras que **updateItem** marcará las tareas seleccionadas como completadas.

         exports.index = function(req, res) {
             var select = "select * from tasks where completed = 0";
             sql.query(conn, select, function(err, items) {
                 if(err)
                     throw err;
                 res.render('index', { title: 'My ToDo List ', tasks: items });
             });
         };

         exports.updateItem = function(req, res) {
             var item = req.body.item;
             if(item) {
                 var insert = "insert into tasks (name, category, created, completed) values (
         , 
         , GETDATE(), 0)";
                 sql.query(conn, insert, [item.name, item.category], function(err) {
                     if(err)
                         throw err;
                     res.redirect('/');
                 });
             } else {
                 var completed = req.body.completed;
                 if(!completed.forEach)
                     completed = [completed];
                 var update = "update tasks set completed = 1 where id in (" + completed.join(",") + ")";
                 sql.query(conn, update, function(err) {
                     if(err)
                         throw err;
                     res.redirect('/');
                 });
             }
         }

4.  Guarde el archivo **index.js**.

### Modificar app.js

1.  En el directorio **tasklist**, abra el archivo **app.js** en un editor de texto. Este archivo se creó anteriormente al ejecutar el comando **express**.

2.  En el archivo app.js, desplácese hacia abajo hasta ver el código que viene a continuación.

         app.configure('development', function(){
	     app.use(express.errorHandler());
         });

3.  Ahora inserte el siguiente código.

         app.get('/', routes.index);
         app.post('/', routes.updateItem);

Con esto se agregará una ruta nueva al método **updateItem** que agregó anteriormente en el archivo **index.js**.

1.  Guarde el archivo **app.js**.

### Modificar la vista de índice

1.  Cambie los directorios por el directorio **views** y abra el archivo **index.jade** en un editor de texto.

2.  Reemplace el contenido del archivo **index.jade** por el código siguiente. De esta manera se define la vista para mostrar las tareas existentes, además de un formulario para agregar tareas nuevas y marcar las existentes como terminadas.

         h1= title
         br

         form(action="/", method="post")
           table(class="table table-striped table-bordered")
             thead
               tr
                 td Name
                 td Category
                 td Date
                 td Complete
             tbody
             each task in tasks
               tr
                 td #{task.name}
                 td #{task.category}
                 td #{task.created}
                 td
                   input(type="checkbox", name="completed", value="#{task.ID}", checked=task.completed == 1)
           button(type="submit", class="btn") Update tasks
         hr

         form(action="/", method="post", class="well")
           label Item Name:
           input(name="item[name]", type="textbox")
           label Item Category:
           input(name="item[category]", type="textbox")
           br
           button(type="submit", class="btn") Add Item

3.  Guarde y cierre el archivo **index.jade**.

### Modificar el diseńo global

El archivo **layout.jade** en el directorio **views** se utiliza como plantilla global para otros archivos **.jade**. En este paso podrá modificarlo para utilizar [Twitter Bootstrap](https://github.com/twbs/bootstrap), un kit de herramientas que facilita el diseńo de un sitio web atractivo.

1.  Descargue y extraiga los archivos para [Twitter Bootstrap](http://getbootstrap.com/). Copie el archivo **bootstrap.min.css** desde la carpeta **bootstrap\\css** al directorio **public\\stylesheets** de su aplicación de lista de tareas.

2.  En la carpeta **views**, abra **layout.jade** en el editor de texto y reemplace el contenido por lo siguiente:

         !!!html
         html
           head
             title= title
             meta(http-equiv='X-UA-Compatible', content='IE=10')
             link(rel='stylesheet', href='/stylesheets/style.css')
             link(rel='stylesheet', href='/stylesheets/bootstrap.min.css')
           body(class='app')
             div(class='navbar navbar-fixed-top')
               .navbar-inner
                 .container
                   a(class='brand', href='/') My Tasks
             .container!= body

3.  Guarde el archivo **layout.jade**.

### Crear el archivo de configuración

El archivo **config.json** contiene la cadena de conexión que se utiliza para conectarse a la base de datos SQL, y lo lee el archivo **index.js** en tiempo de ejecución. Lleve a cabo los siguientes pasos para crear este archivo:

1.  En el directorio **tasklist**, cree un archivo llamado **config.json** y ábralo en un editor de texto.

2.  El contenido del archivo **config.json** debería parecerse al siguiente:

         {
           "SQL_CONN" : "connection_string"
         }

    Reemplace **connection\_string** por el valor de cadena de conexión ODBC anteriormente devuelto.

3.  Guarde el archivo.

Ejecución de la aplicación de forma local
-----------------------------------------

Lleve a cabo los siguientes pasos para probar la aplicación en su máquina local:

1.  Desde la línea de comandos, cambie los directorios por el directorio **tasklist**.

2.  Utilice el siguiente comando para iniciar localmente la aplicación:

         node app.js

3.  Abra un explorador web y navegue a http://127.0.0.1:3000. Con esto debe aparecer una página web similar a la siguiente:

    ![Página web que muestra una lista de tareas vacía](./media/sql-database-nodejs-use-web-site/sql_todo_empty.png)

4.  Utilice los campos proporcionados para **Item Name** y **Item Category** para escribir información y, a continuación, haga clic en **Add item**.

5.  La página debe actualizarse para mostrar el elemento en la lista ToDo.

    ![Imagen del elemento nuevo en la lista de tareas](./media/sql-database-nodejs-use-web-site/sql_todo_list.png)

6.  Para completar una tarea, simplemente marque la casilla en la columna Complete y, a continuación, haga clic en **Update tasks**.

7.  Para detener el proceso de Node, vaya a la línea de comandos y presione las teclas **CTRL** y **C**.

Implementación de su aplicación en Azure
----------------------------------------

En esta sección, utilizará los pasos de implementación que recibió después de crear el sitio web para publicar su aplicación en Azure.

### Publicación de la aplicación

1.  En la línea de comandos, cambie los directorios por el directorio **tasklist** si todavía no está ahí.

2.  Utilice los siguientes comandos para inicializar un repositorio git local para la aplicación, agréguele los archivos de la aplicación y, finalmente, inserte los archivos en Azure.

         git init
         git add .
         git commit -m "adding files"
         git remote add azure [URL del repositorio remoto]
         git push azure master

    Al final de la implementación debiera ver una instrucción similar a la siguiente:

         To https://username@tabletasklist.azurewebsites.net/TableTasklist.git
	     \* [new branch] 	master -\> master

3.  Una vez que haya finalizado la operación de inserción, vaya a **http://[nombre de sitio].azurewebsites.net/** para ver la aplicación.

### Cambiar a una variable de entorno

Anteriormente implementamos un código que busca una variable de entorno **SQL\_CONN** para la cadena de conexión o que carga el valor del archivo **config.json**. En los siguientes pasos, creará un par clave/valor en la configuración de su sitio web al que la aplicación tiene acceso real a través de una variable de entorno.

1.  En el Portal de administración de Azure, haga clic en **Sitios web** y seleccione el sitio web.

    ![Abrir panel del sitio web](./media/sql-database-nodejs-use-web-site/go_to_dashboard.png)

2.  Haga clic en **CONFIGURE** y, a continuación, encuentre la sección **app settings** de la página.

    ![vínculo de configuración](./media/sql-database-nodejs-use-web-site/sql-task-configure.png)

3.  En la sección **app settings**, escriba **SQL\_CONN** en el campo **KEY** y la cadena de conexión ODBC en el campo **VALUE**. Finalmente, haga clic en la marca de verificación.

    ![configuración de aplicaciones](./media/sql-database-nodejs-use-web-site/appsettings.png)

4.  Finalmente, haga clic en el icono **SAVE** en la parte inferior de la página para confirmar este cambio en el entorno de tiempo de ejecución.

    ![guardar configuración de aplicaciones](./media/sql-database-nodejs-use-web-site/savebutton.png)

5.  En la línea de comandos, cambie los directorios por el directorio **tasklist** y escriba el siguiente comando para eliminar el archivo **config.json**:

         git rm config.json
         git commit -m "Removing config file"

6.  Ejecute el siguiente comando para implementar los cambios en Azure:

         git push azure master

Una vez que se han implementado los cambios en Azure, la aplicación web deberá seguir trabajando, dado que ahora lee la cadena de conexión desde la entrada **app settings**. Para comprobarlo, cambie el valor de la entrada **SQL\_CONN** en **app settings** por un valor no válido. Una vez que se ha guardado este valor, el sitio web presentará errores debido a la cadena de conexión no válida.

Pasos siguientes
----------------

-   [Node.js Web Application with MongoDB](../store-mongolab-web-sites-nodejs-store-data-mongodb/)

-   [Aplicación web Node.js con almacenamiento de tablas](/en-us/develop/nodejs/tutorials/web-site-with-storage/)

Recursos adicionales
--------------------

[ Herramienta de línea de comandos de Azure para Mac y Linux](/en-us/develop/nodejs/how-to-guides/command-line-tools/)