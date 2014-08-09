<properties linkid="dev-nodejs-tutorials-web-site-with-storage" urlDisplayName="Web Site with Storage" pageTitle="Node.js web site with table storage | Microsoft Azure" metaKeywords="Azure table storage Node.js, Azure Node.js application, Azure Node.js tutorial, Azure Node.js example" description="A tutorial that teaches you how to use the Azure Table service to store data from a Node application hosted on an Azure web site." metaCanonical="" services="web-sites,storage" documentationCenter="Node.js" title="Node.js Web Application using the Azure Table Service" authors="" solutions="" manager="" editor="" />

Aplicación web Node.js utilizando el servicio Tabla de Azure
============================================================

En este tutorial aprenderá a utilizar el servicio Tabla que la administración de datos de Azure proporciona para almacenar y tener acceso a datos desde una aplicación [Node](http://nodejs.org) hospedada en Azure. En este tutorial se asume que tiene alguna experiencia anterior en el uso de Node y [Git](http://git-scm.com).

Aprenderá a:

-   Usar npm (administrador de paquetes de Node) para instalar los módulos de Node

-   Trabajar con el servicio Tabla de Azure

-   Utilizar la herramienta de línea de comandos de Azure para Mac y Linux con el fin de crear un sitio web Azure

Al seguir este tutorial, podrá compilar una aplicación de administración de tareas basadas en web sencilla que permite crear, recuperar y completar tareas. Las tareas se almacenan en el servicio Tabla.

Los archivos del proyecto para este tutorial se almacenarán en un directorio llamado **tasklist** y la aplicación completada tendrá una apariencia similar a la siguiente:

![Página web que muestra una lista de tareas vacía](./media/storage-nodejs-use-table-storage-web-site/table_todo_empty.png)

**Nota:**

Este tutorial hace referencia a la carpeta **tasklist**. La ruta completa a esta carpeta se omite, debido a que la semántica de la ruta es diferente entre los sistemas operativos. Debe crear esta carpeta en una ubicación a la que le sea fácil tener acceso en su sistema de archivos local, como **\~/node/tasklist** o **c:\\node\\tasklist**

**Nota:**

Muchos de los pasos que se mencionan a continuación usan la línea de comandos. Para realizar estos pasos, utilice la línea de comandos de su sistema operativo, como **cmd.exe** (Windows) o **Bash** (shell de Unix). En los sistemas OS X puede tener acceso a la línea de comandos mediante la aplicación Terminal.

Requisitos previos
------------------

Antes de seguir las instrucciones del presente artículo, debe asegurarse de tener instalados los siguientes elementos:

-   [Node](http://nodejs.org) versión 0.6.14 o superior

-   [Git](http://git-scm.com)

-   Un editor de texto

-   Un explorador web

[WACOM.INCLUDE [create-account-and-websites-note](../includes/create-account-and-websites-note.md)]

una cuenta de almacenamiento
----------------------------

Realice los siguientes pasos para crear una cuenta de almacenamiento. Esta cuenta se utilizará con las siguientes instrucciones de este tutorial.

1.  Abra su explorador web y diríjase al [Portal de Azure](http://windowsazure.com). Si se le solicita, inicie sesión con la información de su suscripción de Azure.

2.  En la parte inferior del portal, haga clic en **+ NEW** y, a continuación, seleccione **Cuenta de almacenamiento**.

    ![+ New](./media/storage-nodejs-use-table-storage-web-site/plus-new.png)

    ![Cuenta de almacenamiento](./media/storage-nodejs-use-table-storage-web-site/new-storage.png)

3.  Seleccione **Quick Create** y, a continuación, escriba la URL y la región/afinidad de esta cuenta de almacenamiento. Puesto que esto es un tutorial y no es necesario replicarlo de forma global, desmarque **Enable Geo-Replication**. Finalmente, haga clic en "Create Storage Account".

    ![Creación rápida](./media/storage-nodejs-use-table-storage-web-site/quick-storage.png)

    Tome nota de la URL que ha escrito, dado que se utilizará como referencia del nombre de la cuenta en los pasos siguientes.

4.  Una vez creada la cuenta de almacenamiento, haga clic en **Manage Keys** en la parte inferior de la página. De este modo se mostrarán las claves de acceso principal y secundaria de esta cuenta de almacenamiento. Copie y guarde la clave de acceso principal y, a continuación, haga clic en la marca de verificación.

    ![Claves de acceso](./media/storage-nodejs-use-table-storage-web-site/manage-access-keys.png)

Instalación de módulos y generación de scaffolding
--------------------------------------------------

En esta sección podrá crear una nueva aplicación Node y usar npm para agregar paquetes de módulos. Para la aplicación de lista de tareas, usará los módulos [Express](http://expressjs.com) y [Azure](https://github.com/WindowsAzure/azure-sdk-for-node). El módulo Express proporciona un marco de controlador de vista de modelo para Node, mientras que los módulos de Azure proporcionan conectividad al servicio Tabla.

### Instalar Express y generar scaffolding

1.  Desde la línea de comandos, cambie los directorios por el directorio **tasklist**. Si el directorio **tasklist** no existe, créelo.

2.  Escriba el siguiente comando para instalar Express.

         npm install express -g

    **Nota:**

    Cuando se usa el parámetro "-g" en algunos sistemas operativos, es posible que reciba un error **Error: EPERM, chmod '/usr/local/bin/express'** y una solicitud para intentar ejecutar la cuenta como administrador. Si esto ocurre, utilice el comando **sudo** para ejecutar npm en un nivel de privilegio más elevado.

    El resultado de este comando debe ser similar al siguiente:

		express@3.4.0 C:\Users\larryfr\AppData\Roaming\npm\node_modules\express
		├── methods@0.0.1
		├── fresh@0.2.0
		├── cookie-signature@1.0.1
		├── range-parser@0.0.4
		├── buffer-crc32@0.2.1
		├── cookie@0.1.0
		├── debug@0.7.2
		├── mkdirp@0.3.5
		├── commander@1.2.0 (keypress@0.1.0)
		├── send@0.1.4 (mime@1.2.11)
		└── connect@2.9.0 (uid2@0.0.2, pause@0.0.1, qs@0.6.5, bytes@0.2.0, multiparty@2.1.8)

    **Nota:**

    El parámetro "-g" que se utiliza cuando se instala el módulo Express lo instala de manera global. Esto se realiza para tener acceso al comando **express** para generar scaffolding del sitio web sin tener que escribir información de ruta adicional.

3.  Para crear el scaffolding que se usará para esta aplicación, use el comando **express**:

         express

    El resultado de este comando debe ser similar al siguiente:

         create : .
         create : ./package.json
         create : ./app.js
         create : ./public/javascripts
         create : ./public
         create : ./public/stylesheets
         create : ./public/stylesheets/style.css
         create : ./views
         create : ./views/layout.jade
         create : ./views/index.jade
         create : ./routes
         create : ./routes/index.js
         create : ./routes/user.js
         create : ./public/images

         install dependencies:
           $ cd . && npm install

         run the app:
           $ node app

Después de que se completa este comando, debe tener varios directorios y archivos nuevos en el directorio **tasklist**.

### Instalar módulos adicionales

El archivo **package.json** es uno de los archivos que se crea con el comando **express**. Este archivo contiene una lista de módulos adicionales necesarios para una aplicación Express. Posteriormente, cuando implemente esta aplicación en un sitio web de Azure, se usará este archivo para determinar los módulos que se necesitan instalar en Azure para admitir su aplicación.

1.  En la línea de comandos, cambie los directorios por la carpeta **tasklist** y escriba lo siguiente para instalar los módulos descritos en el archivo **package.json**:

         npm install

    El resultado de este comando debe ser similar al siguiente:

		express@3.4.0 node_modules\express
		├── methods@0.0.1
		├── range-parser@0.0.4
		├── cookie-signature@1.0.1
		├── fresh@0.2.0
		├── buffer-crc32@0.2.1
		├── cookie@0.1.0
		├── debug@0.7.2
		├── mkdirp@0.3.5
		├── commander@1.2.0 (keypress@0.1.0)
		├── send@0.1.4 (mime@1.2.11)
		└── connect@2.9.0 (uid2@0.0.2, pause@0.0.1, bytes@0.2.0, qs@0.6.5, multiparty@2.1.8)

		jade@0.35.0 node_modules\jade
		├── character-parser@1.2.0
		├── commander@2.0.0
		├── mkdirp@0.3.5
		├── monocle@1.1.50 (readdirp@0.2.5)
		├── transformers@2.1.0 (promise@2.0.0, css@1.0.8, uglify-js@2.2.5)
		├── with@1.1.1 (uglify-js@2.4.0)
		└── constantinople@1.0.2 (uglify-js@2.4.0)

    Con esto se instalan todos los módulos predeterminados que necesita Express.

2.  A continuación, escriba el siguiente comando para instalar los módulos [azure](https://github.com/WindowsAzure/azure-sdk-for-node), [node-uuid], [nconf] y [async] de manera local, además de guardar una entrada para estos para el archivo **package.json**:

         npm install azure node-uuid async nconf --save

    El resultado de este comando debe ser similar al siguiente:

         async@0.2.9 node_modules\async

         node-uuid@1.4.1 node_modules\node-uuid

		nconf@0.6.7 node_modules\nconf
		├── ini@1.1.0
		├── async@0.1.22
		├── pkginfo@0.2.3
		└── optimist@0.3.7 (wordwrap@0.0.2)

		azure@0.7.15 node_modules\azure
		├── dateformat@1.0.2-1.2.3
		├── xmlbuilder@0.4.2
		├── envconf@0.0.4
		├── node-uuid@1.2.0
		├── mpns@2.0.1
		├── underscore@1.5.2
		├── mime@1.2.11
		├── validator@1.5.1
		├── tunnel@0.0.2
		├── wns@0.5.3
		├── xml2js@0.2.8 (sax@0.5.5)
		└── request@2.25.0 (json-stringify-safe@5.0.0, aws-sign@0.3.0, forever-agent@0.5.0, tunnel-agent@0.3.0, qs@0.6.5, oauth-sign@0.3.0, cookie-jar@0.3.0, node-uuid@1.4.1, http-signature@0.10.0, form-data@0.1.1, hawk@1.0.0)

Uso del servicio Tabla en una aplicación Node
---------------------------------------------

En esta sección extenderá la aplicación básica creada por el comando **express** agregando un archivo **task.js** que contiene el modelo para sus tareas. También podrá modificar el archivo **app.js** existente y crear un archivo **tasklist.js** nuevo que utilice el modelo.

### Crear el modelo

1.  En el directorio **tasklist**, cree el directorio nuevo con el nombre **models**.

2.  En el directorio **models**, cree un archivo nuevo con el nombre **models**. Este archivo contendrá el modelo para las tareas que se crean con su aplicación.

3.  Al comienzo del archivo **task.js**, agregue el siguiente código para hacer referencia a las bibliotecas requeridas:

         var azure = require('azure');

    var uuid = require('node-uuid');

4.  A continuación, agregará código para definir y exportar el objeto Task. Este objeto es el responsable de la conexión a la tabla.

module.exports = Task;

     function Task(storageClient, tableName, partitionKey) {
          this.storageClient = storageClient;
          this.tableName = tableName;
          this.partitionKey = partitionKey;
          this.storageClient.createTableIfNotExists(tableName, function tableCreated(error) {
            if(error) {
              throw error;
            }
          });
        };

1.  A continuación, agregue el siguiente código para definir métodos adicionales en el objeto Task, que permite las interacciones con los datos almacenados en la tabla:

         Task.prototype = {
           find: function(query, callback) {
             self = this;
             self.storageClient.queryEntities(query, function entitiesQueried(error, entities) {
               if(error) {
                 callback(error);
               } else {
                 callback(null, entities);
               }
             });
           },

           addItem: function(item, callback) {
             self = this;
             item.RowKey = uuid();
             item.PartitionKey = self.partitionKey;
             item.completed = false;
             self.storageClient.insertEntity(self.tableName, item, function entityInserted(error) {
               if(error){  
                 callback(error);
               }
               callback(null);
             });
           },

           updateItem: function(item, callback) {
             self = this;
             self.storageClient.queryEntity(self.tableName, self.partitionKey, item, function entityQueried(error, entity) {
               if(error) {
                 callback(error);
               }
               entity.completed = true;
               self.storageClient.updateEntity(self.tableName, entity, function entityUpdated(error) {
                 if(error) {
                   callback(error);
                 }
                 callback(null);
               });
             });
           }
         }

2.  Guarde y cierre el archivo **task.js**.

### Crear el controlador

1.  En el directorio **tasklist/routes**, cree un archivo nuevo con el nombre **tasklist.js** y ábralo en un editor de texto.

2.  Agregue el siguiente código a **tasklist.js**. De este modo se cargan los módulos azure y async, que utiliza **tasklist.js**. También define la función **TaskList**, que pasa a una instancia del objeto **Task** que definimos anteriormente:

         var azure = require('azure');
         var async = require('async');

         module.exports = TaskList;

         function TaskList(task) {
           this.task = task;
         }

3.  Continúe agregando al archivo **tasklist.js** agregando los métodos usados para **showTasks**, **addTask** y **completeTasks**:

         TaskList.prototype = {
           showTasks: function(req, res) {
             self = this;
             var query = azure.TableQuery
               .select()
               .from(self.task.tableName)
               .where('completed eq 
         ', false);
             self.task.find(query, function itemsFound(error, items) {
               res.render('index',{title: 'My ToDo List ', tasks: items});
             });
           },

           addTask: function(req,res) {
             var self = this      
             var item = req.body.item;
             self.task.addItem(item, function itemAdded(error) {
               if(error) {
                 throw error;
               }
               res.redirect('/');
             });
           },

           completeTask: function(req,res) {
             var self = this;
             var completedTasks = Object.keys(req.body);
             async.forEach(completedTasks, function taskIterator(completedTask, callback) {
               self.task.updateItem(completedTask, function itemsUpdated(error) {
                 if(error){
                   callback(error);
                 } else {
                   callback(null);
                 }
               });
             }, function goHome(error){
               if(error) {
                 throw error;
               } else {
                res.redirect('/');
               }
             });
           }
         }

4.  Guarde el archivo **tasklist.js**.

### Modificar app.js

1.  En el directorio **tasklist**, abra el archivo **app.js** en un editor de texto. Este archivo se creó anteriormente al ejecutar el comando **express**.

2.  Al principio del archivo, agregue lo siguiente para cargar el módulo azure, configurar el nombre de la tabla, partitionKey, y configurar las credenciales de almacenamiento utilizadas en este ejemplo:

         var azure = require('azure');
         var nconf = require('nconf');
         nconf.env()
              .file({ file: 'config.json' });
         var tableName = nconf.get("TABLE_NAME")
         var partitionKey = nconf.get("PARTITION_KEY")
         var accountName = nconf.get("STORAGE_NAME")
         var accountKey = nconf.get("STORAGE_KEY");

    **Nota:**

    nconf cargará los valores de configuración desde las variables de entorno o el archivo **config.json**, que crearemos más adelante.

3.  En el archivo app.js, desplácese hacia abajo hasta ver la siguiente línea:

         app.get('/', routes.index);
         app.get('/users', user.list);

    Sustituya las líneas anteriores por el código que se muestra a continuación. De este modo se inicializará una instancia de **Task** con una conexión a su cuenta de almacenamiento. Esto se pasa a la **TaskList**, que lo utilizará para comunicarse con el servicio Tabla:

         var TaskList = require('./routes/tasklist');
         var Task = require('./models/task');
         var task = new Task(azure.createTableService(accountName, accountKey), tableName, partitionKey);
         var taskList = new TaskList(task);

         app.get('/', taskList.showTasks.bind(taskList));
         app.post('/addtask', taskList.addTask.bind(taskList));
         app.post('/completetask', taskList.completeTask.bind(taskList));

4.  Guarde el archivo **app.js**.

### Modificar la vista de índice

1.  Cambie los directorios por el directorio **views** y abra el archivo **index.jade** en un editor de texto.

2.  Reemplace el contenido del archivo **index.jade** por el código siguiente. De esta manera se define la vista para mostrar las tareas existentes, además de un formulario para agregar tareas nuevas y marcar las existentes como terminadas.

         extends layout

         block content
           h1= title
           br

           form(action="/completetask", method="post")
             table.table.table-striped.table-bordered
               tr
                 td Name
                 td Category
                 td Date
                 td Complete
               each task in tasks
                 tr
                   td #{task.name}
                   td #{task.category}
                   - var day   = task.Timestamp.getDate();
                   - var month = task.Timestamp.getMonth() + 1;
                   - var year  = task.Timestamp.getFullYear();
                   td #{month + "/" + day + "/" + year}
                   td
                     input(type="checkbox", name="#{task.RowKey}", value="#{!task.itemCompleted}", checked=task.itemCompleted)
             button.btn(type="submit") Update tasks
           hr
           form.well(action="/addtask", method="post")
             label Item Name: 
             input(name="item[name]", type="textbox")
             label Item Category: 
             input(name="item[category]", type="textbox")
             br
             button.btn(type="submit") Add item

3.  Guarde y cierre el archivo **index.jade**.

### Modificar el diseño global

El archivo **layout.jade** en el directorio **views** se utiliza como plantilla global para otros archivos **.jade**. En este paso podrá modificarlo para utilizar [Twitter Bootstrap](https://github.com/twbs/bootstrap), un kit de herramientas que facilita el diseño de un sitio web atractivo.

1.  Descargue y extraiga los archivos para [Twitter Bootstrap](http://getbootstrap.com/). Copie el archivo **bootstrap.min.css** desde la carpeta **bootstrap\\dist\\css** al directorio **public\\stylesheets** de su aplicación de lista de tareas.

2.  En la carpeta **views**, abra **layout.jade** en el editor de texto y reemplace el contenido por lo siguiente:

         doctype 5
         html
           head
             title= title
             link(rel='stylesheet', href='/stylesheets/bootstrap.min.css')
             link(rel='stylesheet', href='/stylesheets/style.css')
           body.app
             nav.navbar.navbar-default
               div.navbar-header
                 a.navbar-brand(href='/') My Tasks
             block content

3.  Guarde el archivo **layout.jade**.

### Crear el archivo de configuración

El archivo **config.json** contiene la cadena de conexión que se utiliza para conectarse a la base de datos SQL, y lo lee la aplicación en tiempo de ejecución. Lleve a cabo los siguientes pasos para crear este archivo:

1.  En el directorio **tasklist**, cree un archivo llamado **config.json** y ábralo en un editor de texto.

2.  El contenido del archivo **config.json** debería parecerse al siguiente:

         {
             "STORAGE_NAME": "nombre de la cuenta de almacenamiento",
             "STORAGE_KEY": "clave de acceso de almacenamiento",
             "PARTITION_KEY": "mytasks",
             "TABLE_NAME": "tasks"
         }

    Sustituya el **nombre de la cuenta de almacenamiento** por el nombre de la cuenta de almacenamiento que creó anteriormente. Sustituya la **clave de acceso de almacenamiento** por la clave de acceso principal de su cuenta de almacenamiento.

3.  Guarde el archivo.

Ejecución de la aplicación de forma local
-----------------------------------------

Lleve a cabo los siguientes pasos para probar la aplicación en su máquina local:

1.  Desde la línea de comandos, cambie los directorios por el directorio **tasklist**.

2.  Utilice el siguiente comando para iniciar localmente la aplicación:

         node app.js

3.  Abra un explorador web y navegue a http://127.0.0.1:3000. Con esto debe aparecer una página web similar a la siguiente:

    ![Página web que muestra una lista de tareas vacía](./media/storage-nodejs-use-table-storage-web-site/table_todo_empty.png)

4.  Utilice los campos proporcionados para **Item Name** y **Item Category** para escribir información y, a continuación, haga clic en **Add item**.

5.  La página debe actualizarse para mostrar el elemento en la tabla ToDo List.

    ![Imagen del elemento nuevo en la lista de tareas](./media/storage-nodejs-use-table-storage-web-site/table_todo_list.png)

6.  Para completar una tarea, simplemente marque la casilla en la columna Complete y, a continuación, haga clic en **Update tasks**.

7.  Para detener el proceso de Node, vaya a la línea de comandos y presione las teclas **CTRL** y **C**.

Implementación de su aplicación en Azure
----------------------------------------

Los pasos de esta sección usan las herramientas de la línea de comandos de Azure para crear un sitio web de Azure nuevo y, posteriormente, usan Git para implementar su aplicación. Para realizar estos pasos debe tener una suscripción a Azure.

**Nota:**

Estos pasos también pueden llevarse a cabo usando el portal de Azure. Para conocer los pasos para usar el portal de Azure y así implementar una aplicación Node.js, consulte [Creación e implementación de una aplicación Node.js en un Sitio web Azure](http://content-ppe.windowsazure.com/es-es/develop/nodejs/tutorials/create-a-website-(mac)/).

**Nota:**

Si este es el primer sitio web Azure que crea, debe usar el portal de Azure para implementar esta aplicación.

### Activación de la característica Sitio web Azure

Si todavía no dispone de una suscripción de Azure, puede registrarse [de forma gratuita](http://windowsazure.com). Después del registro, siga estos pasos para activar la característica Sitio web Azure.

[WACOM.INCLUDE [antares-iaas-signup](../includes/antares-iaas-signup.md)]

### Instalación de la herramienta de línea de comandos de Azure para Mac y Linux

Para instalar las herramientas de línea de comandos, use el siguiente comando:

    npm install azure-cli -g

**Nota:**

Si ya ha instaló el SDK de **Azure para Node.js** desde [Azure Developer Center](/es-es/develop/nodejs/), entonces las herramientas de línea de comandos ya deberían estar instaladas. Para obtener más información, consulte [Herramienta de línea de comandos de Azure para Mac y Linux](/es-es/develop/nodejs/how-to-guides/command-line-tools/).

**Nota:**

A pesar de que las herramientas de línea de comandos se crearon principalmente para los usuarios de Mac y Linux, están basadas en Node.js y deberían funcionar en cualquier sistema capaz de ejecutar Node.

### Importación de la configuración de publicación

Antes de usar las herramientas de línea de comandos de Azure, primero debe descargar un archivo que contenga información sobre su suscripción. Realice los siguientes pasos para descargar e importar este archivo.

1.  Desde la línea de comandos, cambie los directorios por el directorio **tasklist**.

2.  Escriba el siguiente comando para abrir el explorador y navegar hasta la página de descarga. Si se le solicita, inicie sesión con la cuenta asociada a su suscripción.

         azure account download

    ![La página de descarga](./media/storage-nodejs-use-table-storage-web-site/azure-account-download-cli.png)

    La descarga del archivo debe iniciarse automáticamente; si esto no ocurre, puede hacer clic en el vínculo al comienzo de la página para descargar el archivo manualmente.

3.  Después de que se haya terminado la descarga del archivo, use el siguiente comando para importar la configuración:

         azure account import <path-to-file>

    Especifique la ruta y el nombre de archivo del archivo de configuración de publicación que descargó en el paso anterior. Después de que el comando se completa, debería ver un resultado similar al siguiente:

         info:   Executing command account import
         info:   Setting service endpoint to: management.core.windows.net
         info:   Setting service port to: 443
         info:   Found subscription: YourSubscription
         info:   Setting default subscription to: YourSubscription
         warn:   The 'C:\users\username\downloads\YourSubscription-6-7-2012-credentials.publishsettings' file contains sensitive information.
         warn:   Remember to delete it now that it has been imported.
         info:   Account publish settings imported successfully
         info:   account import command OK

4.  Después de que se haya completado la importación, debe eliminar el archivo de configuración de publicación debido a que ya no es necesario y contiene información confidencial relacionada con su suscripción a Azure.

### Creación de un sitio web de Azure

1.  Desde la línea de comandos, cambie los directorios por el directorio **tasklist**.

2.  Utilice el comando siguiente para crear un sitio web de Azure.

         azure site create --git

    Se le solicitará el nombre del sitio web y el centro de datos en el que estará ubicado. Proporcione un nombre único y seleccione el centro de datos geográficamente más cercano a su ubicación.

    El parámetro `--git` creará un repositorio Git en Azure para este sitio web. También inicializará el repositorio Git en el directorio actual si no existe uno. Creará también un [Git remoto](http://git-scm.com/docs/git-remote) con el nombre 'azure', que se usará para publicar la aplicación en Azure. Finalmente, creará un archivo **web.config**, que contiene la configuración usada por Azure para hospedar aplicaciones Node.

    **Nota:**

    Si este comando se ejecutó desde un directorio que ya contiene un repositorio de Git, no reinicializará el repositorio.

    **Nota:**

    Si se omite el parámetro "--git", aunque el directorio contenga un repositorio de Git se creará el "azure" remoto.

    Después de que este comando se haya completado, verá un resultado similar al siguiente. Observe que la línea que comienza con **Web site created at** contiene la dirección URL del sitio web.

         info:   Executing command site create
         help:   Need a site name
         Name: TableTasklist
         info:   Using location southcentraluswebspace
         info:   Executing `git init`
         info:   Creating default .gitignore file
         info:   Creating a new web site
         info:   Created web site at  tabletasklist.azurewebsites.net
         info:   Initializing repository
         info:   Repository initialized
         info:   Executing `git remote add azure https://username@tabletasklist.azurewebsites.net/TableTasklist.git`
         info:   site create command OK

    **Nota:**

    Si este es el primer sitio web de Azure de su suscripción, se le pedirá que use el portal para crear el sitio web. Para obtener más información, consulte [Creación e implementación de una aplicación Node.js en sitios web de Azure](/es-es/develop/nodejs/tutorials/create-a-website-(mac)/).

### Publicación de la aplicación

1.  En la ventana Terminal, cambie los directorios por el directorio **tasklist** si es que ya no se encuentra en ese lugar.

2.  Use los siguientes comandos para agregar y luego confirmar los archivos en su repositorio local de Git:

         git add .
         git commit -m "adding files"

3.  Al realizar los últimos cambios en el repositorio de Git en el sitio web de Azure, debe especificar que la rama objetivo es **master** debido a que esta se usa para el contenido del sitio web.

         git push azure master

    Al final de la implementación debiera ver una instrucción similar a la siguiente:

         To https://username@tabletasklist.azurewebsites.net/TableTasklist.git
	     * [new branch] master -\> master

4.  Después de que se haya completado la operación de inserción, diríjase a la URL del sitio web que se devolvió anteriormente usando el comando `azure create site` para ver su aplicación.

### Cambio a una variable de entorno

Anteriormente implementamos un código que busca una variable de entorno **SQL\_CONN** para la cadena de conexión o que carga el valor del archivo **config.json**. En los siguientes pasos, creará un par clave/valor en la configuración de su sitio web al que la aplicación tiene acceso real a través de una variable de entorno.

1.  En el Portal de administración, haga clic en **Sitios web** y seleccione el sitio web.

    ![Abrir el panel del sitio web](./media/storage-nodejs-use-table-storage-web-site/go_to_dashboard.png)

2.  Haga clic en **CONFIGURE** y, a continuación, encuentre la sección **app settings** de la página.

    ![vínculo de configuración](./media/storage-nodejs-use-table-storage-web-site/sql-task-configure.png)

3.  En la sección **app settings**, escriba **STORAGE\_NAME** en el campo **KEY** y el nombre de su cuenta de almacenamiento en el campo **VALUE**. Haga clic en la marca de verificación para pasar al siguiente campo. Repita este proceso con las siguientes claves y valores:

    -   **STORAGE\_KEY**: Clave de acceso para su cuenta de almacenamiento

    -   **PARTITION\_KEY**: "mytasks"

    -   **TABLE\_NAME**: "tasks"

    ![configuración de aplicaciones](./media/storage-nodejs-use-table-storage-web-site/storage-tasks-appsettings.png)

4.  Finalmente, haga clic en el icono **SAVE** en la parte inferior de la página para confirmar este cambio en el entorno de tiempo de ejecución.

    ![guardar configuración de aplicaciones](./media/storage-nodejs-use-table-storage-web-site/savebutton.png)

5.  En la línea de comandos, cambie los directorios por el directorio **tasklist** y escriba el siguiente comando para eliminar el archivo **config.json**:

         git rm config.json
         git commit -m "Removing config file"

6.  Ejecute el siguiente comando para implementar los cambios en Azure:

         git push azure master

Una vez que se han implementado los cambios en Azure, la aplicación web deberá seguir trabajando, dado que ahora lee la cadena de conexión desde la entrada **app settings**. Para comprobarlo, cambie el valor de la entrada **STORAGE\_KEY** en **app settings** por un valor no válido. Una vez que se ha guardado este valor, el sitio web presentará errores debido a la configuración de la clave de acceso de almacenamiento no válida.

Pasos siguientes
----------------

Si bien los pasos de este artículo describen el uso del servicio Tabla para almacenar información, puede también usar MongoDB. Consulte [Aplicación web Node.js con MongoDB](/es-es/develop/nodejs/tutorials/website-with-mongodb-(Mac)/) para obtener más información.

Recursos adicionales
--------------------

[Herramienta de línea de comandos de Azure para Mac y Linux]
 [Creación e implementación de una aplicación Node.js en Sitios web Azure]: /es-es/develop/nodejs/tutorials/create-a-website-(mac)/ [Publicación en sitios web de Azure con Git](../CommonTasks/publishing-with-git): /es-es/develop/nodejs/common-tasks/publishing-with-git/ [Centro para desarrolladores de Azure]: /es-es/develop/nodejs/

