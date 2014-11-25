<properties linkid="develop-nodejs-tutorials-web-site-with-mongodb-mongolab" urlDisplayName="Website with MongoDB" pageTitle="Node.js website with MongoDB on MongoLab - Azure" metaKeywords="" description="Learn how to create a Node.js Azure Website that connects to a MongoDB instance hosted on MongoLab." metaCanonical="" services="web-sites,virtual-machines" documentationCenter="nodejs" title="Create a Node.js Application on Azure with MongoDB using the MongoLab Add-On" authors="eric@mongolab.com" solutions="" manager="" editor="" />

<tags ms.service="web-sites" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="nodejs" ms.topic="article" ms.date="09/17/2014" ms.author="eric@mongolab.com" />

# Creación de una aplicación Node.js en Azure con MongoDB y el complemento de MongoLab

*Por Eric Sedor, MongoLab*

¡Saludos, aventurero! Bienvenido a MongoDB como servicio. En este tutorial, aprenderá lo siguiente:

1.  [Aprovisionar la base de datos][Aprovisionar la base de datos]: El complemento [MongoLab][MongoLab] de la Tienda de Azure proporcionará una base de datos MongoDB hospedada en la nube de Azure y administrada por la plataforma de base de datos en la nube de MongoLab.
2.  [Crear la aplicación][Crear la aplicación]: Será una aplicación Node.js sencilla para mantener una lista de tareas.
3.  [Implementación de la aplicación][Implementación de la aplicación]: Al unir unos pocos enlaces de configuración, haremos la implementación de nuestro código en un abrir y cerrar de ojos.
4.  [Administración de la base de datos][Administración de la base de datos]: Finalmente, le mostraremos el portal de administración de base de datos basado en web de MongoLab donde puede buscar, visualizar y modificar los datos con facilidad.

En cualquier momento de este tutorial puede enviar un correo electrónico a [\<a href="mailto:support@mongolab.com"\>support@mongolab.com\</a\>][\<a href="mailto:support@mongolab.com"\>support@mongolab.com\</a\>] si tiene alguna pregunta.

Antes de continuar, asegúrese de tener instalados los siguientes elementos:

-   [Node.js][Node.js] versión 0.8.14+

-   [Git][Git]

[WACOM.INCLUDE [create-account-and-websites-note](../includes/create-account-and-websites-note.md)]

## Inicio rápido

Si tiene algún conocimiento de la Tienda de Azure, use esta sección para comenzar rápidamente. De lo contrario, vaya a la sección [Aprovisionar la base de datos][Aprovisionar la base de datos] a continuación.

1.  Abra la Tienda de Azure.  
![Store][Store]
2.  Haga clic en el complemento MongoLab.  
![MongoLab][1]
3.  Haga clic en el complemento MongoLab en la lista de complementos y haga clic en **Connection Info**.  
![ConnectionInfoButton][ConnectionInfoButton]
4.  Copie MONGOLAB\_URI en su portapapeles.  
![ConnectionInfoScreen][ConnectionInfoScreen]  
**Este URI contiene el nombre de usuario y la contraseña de su base de datos. Déle el mismo tratamiento que a la información confidencial y no lo comparta.**
5.  Agregue el valor a la lista Connection Strings en el menú Configuration de su aplicación web de Azure:  
![WebSiteConnectionStrings][WebSiteConnectionStrings]
6.  Para **Name**, especifique MONGOLAB\_URI.
7.  Para **Value**, pegue la cadena de conexión que se obtuvo en la sección anterior.
8.  Seleccione **Custom** en la lista desplegable Type (en lugar del valor predeterminado **SQLAzure**).
9.  Ejecute `npm install mongoose` para obtener M ongoose, un controlador de nodo de MongoDB.
10. Configure un enlace en su código para obtener su URI de conexión de MongoLab desde una variable de entorno y conéctese:

        var mongoose = require('mongoose');  
        ...
        var connectionString = process.env.CUSTOMCONNSTR_MONGOLAB_URI
        ...
        mongoose.connect(connectionString);

Nota: Azure agrega el prefijo **CUSTOMCONNSTR\_** a la cadena de conexión declarada originalmente, razón por la cual el código hace referencia a **CUSTOMCONNSTR\_MONGOLAB\_URI.** en vez de a **MONGOLAB\_URI**.

Ahora, continuemos con el tutorial completo...

## <a name="provision"></a>Aprovisionamiento de la base de datos

[WACOM.INCLUDE [howto-provision-mongolab](../includes/howto-provision-mongolab.md)]

## <a name="create"></a>Creación de la aplicación

En esta sección podrá configurar su entorno de desarrollo y poner el código para una aplicación web de listas de tareas básicas usando Node.js, Express y MongoDB. [Express][Express] proporciona un marco de controlador de vista para Node, mientras que [Mongoose][Mongoose] es un controlador para comunicarse con MongoDB en Node.

### Configuración

#### Generación de scaffolding e instalación de los módulos

1.  En la línea de comandos, cree y vaya hasta el directorio **tasklist**. Este se será su directorio de proyectos.
2.  Escriba el siguiente comando para instalar Express.

        npm install express -g

    `-g` indica el modo global, que usamos para que el módulo **express** esté disponible sin especificar una ruta de directorio. Si recibe **Error: EPERM, chmod '/usr/local/bin/express'**, use **sudo** para ejecutar npm en un nivel de privilegio más alto.

    El resultado de este comando debe ser similar al siguiente:

        express@3.3.4 C:\Users\larryfr\AppData\Roaming\npm\node_modules\express
        ├── methods@0.0.1
        ├── fresh@0.1.0
        ├── cookie-signature@1.0.1
        ├── range-parser@0.0.4
        ├── buffer-crc32@0.2.1
        ├── cookie@0.1.0
        ├── debug@0.7.2
        ├── mkdirp@0.3.5
        ├── commander@1.2.0 (keypress@0.1.0)
        ├── send@0.1.3 (mime@1.2.9)
        └── connect@2.8.4 (uid2@0.0.2, pause@0.0.1, qs@0.6.5, bytes@0.2.0, formidable@1.0.14)

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

        dont forget to install dependencies:
        $ cd . && npm install

    Después de que se completa este comando, debe tener varios directorios y archivos nuevos en el directorio **tasklist**.

4.  Escriba lo siguiente para instalar los módulos que se describen en el archivo **package.json**:

        npm install

    El resultado de este comando debe ser similar al siguiente:

        express@3.3.4 node_modules\express
        ├── methods@0.0.1
        ├── fresh@0.1.0
        ├── range-parser@0.0.4
        ├── cookie-signature@1.0.1
        ├── buffer-crc32@0.2.1
        ├── cookie@0.1.0
        ├── debug@0.7.2
        ├── mkdirp@0.3.5
        ├── commander@1.2.0 (keypress@0.1.0)
        ├── send@0.1.3 (mime@1.2.9)
        └── connect@2.8.4 (uid2@0.0.2, pause@0.0.1, qs@0.6.5, bytes@0.2.0, formidable@1.0.14)

        jade@0.33.0 node_modules\jade
        ├── character-parser@1.0.2
        ├── mkdirp@0.3.5
        ├── commander@1.2.0 (keypress@0.1.0)
        ├── with@1.1.0 (uglify-js@2.3.6)
        ├── constantinople@1.0.1 (uglify-js@2.3.6)
        ├── transformers@2.0.1 (promise@2.0.0, css@1.0.8, uglify-js@2.2.5)
        └── monocle@0.1.48 (readdirp@0.2.5)

    El archivo **package.json** es uno de los archivos que se crea con el comando **express**. Este archivo contiene una lista de módulos adicionales necesarios para una aplicación Express. Posteriormente, cuando implemente esta aplicación en un sitio web de Azure, se usará este archivo para determinar los módulos que se necesitan instalar en Azure para admitir su aplicación.

5.  A continuación, escriba el siguiente comando para instalar el módulo Mongoose de manera local, además de guardar una entrada para este en el archivo **package.json**:

        npm install mongoose --save

    El resultado de este comando debe ser similar al siguiente:

        mongoose@3.6.15 node_modules\mongoose
        ├── regexp-clone@0.0.1
        ├── sliced@0.0.3
        ├── muri@0.3.1
        ├── hooks@0.2.1
        ├── mpath@0.1.1
        ├── ms@0.1.0
        ├── mpromise@0.2.1 (sliced@0.0.4)
        └── mongodb@1.3.11 (bson@0.1.9, kerberos@0.0.3)

    Puede ignorar de manera segura cualquier mensaje sobre la instalación del analizador bson de C++.

### El código

Ahora que nuestro entorno y scaffolding están listos, extenderemos la aplicación básica que se creó con el comando **express** al agregar un archivo **task.js** que contiene el modelo para sus tareas. También podrá modificar el archivo **app.js** existente y crear un archivo controlador **tasklist.js** nuevo para usar el modelo.

#### Crear el modelo

1.  En el directorio **tasklist**, cree el directorio nuevo con el nombre **models**.

2.  En el directorio **models**, cree un archivo nuevo con el nombre **models**. Este archivo contendrá el modelo para las tareas que se crean con su aplicación.

3.  Agregue el siguiente código al archivo **task.js**:

        var mongoose = require('mongoose')
          , Schema = mongoose.Schema;

        var TaskSchema = new Schema({
            itemName      : String
          , itemCategory  : String
          , itemCompleted : { type: Boolean, default: false }
          , itemDate      : { type: Date, default: Date.now }
        });

        module.exports = mongoose.model('TaskModel', TaskSchema);

4.  Guarde y cierre el archivo **task.js**.

#### Crear el controlador

1.  En el directorio **tasklist/routes**, cree un archivo nuevo con el nombre **tasklist.js** y ábralo en un editor de texto.

2.  Agregue el siguiente código a **tasklist.js**. De esta manera se carga el módulo mongoose y el modelo de tarea se define en **task.js**. La función TaskList se usa para crear la conexión al servidor de MongoDB basado en el valor **connection** y proporciona los métodos **showTasks**, **addTask** y **completeTasks**:

        var mongoose = require('mongoose')
          , task = require('../models/task.js');

        module.exports = TaskList;

        function TaskList(connection) {
          mongoose.connect(connection);
        }

        TaskList.prototype = {
          showTasks: function(req, res) {
            task.find({itemCompleted: false}, function foundTasks(err, items) {
              res.render('index',{title: 'My ToDo List ', tasks: items})
            });
          },

          addTask: function(req,res) {
            var item = req.body.item;
            newTask = new task();
            newTask.itemName = item.name;
            newTask.itemCategory = item.category;
            newTask.save(function savedTask(err){
              if(err) {
                throw err;
              }
            });
            res.redirect('/');
          },


          completeTask: function(req,res) {
            var completedTasks = req.body;
            for(taskId in completedTasks) {
              if(completedTasks[taskId]=='true') {
                var conditions = { _id: taskId };
                var updates = { itemCompleted: completedTasks[taskId] };
                task.update(conditions, updates, function updatedTask(err) {
                  if(err) {
                    throw err;
                  }
                });
              }
            }
            res.redirect('/');
          }
        }

3.  Guarde el archivo **tasklist.js**.

#### Modificar la vista de índice

1.  Cambie los directorios por el directorio **views** y abra el archivo **index.jade** en un editor de texto.

2.  Reemplace el contenido del archivo **index.jade** por el código siguiente. De esta manera se define la vista para mostrar las tareas existentes, además de un formulario para agregar tareas nuevas y marcar las existentes como terminadas.

        h1 #{title}
        form(action="/completetask", method="post")
          table(border="1")
            tr
              td Name
              td Category
              td Date
              td Complete
            each task in tasks
              tr
                td #{task.itemName}
                td #{task.itemCategory}
                - var day   = task.itemDate.getDate();
                - var month = task.itemDate.getMonth() + 1;
                - var year  = task.itemDate.getFullYear();
                td #{month + "/" + day + "/" + year}
                td
                  input(type="checkbox", name="#{task._id}", value="#{!task.itemCompleted}", checked=task.itemCompleted)
          input(type="submit", value="Update tasks")
        hr
        form(action="/addtask", method="post")
          table(border="1") 
            tr
              td Item Name: 
              td 
                input(name="item[name]", type="textbox")
            tr
              td Item Category: 
              td 
                input(name="item[category]", type="textbox")
          input(type="submit", value="Add item")

3.  Guarde y cierre el archivo **index.jade**.

#### Reemplazar app.js

1.  En el directorio **tasklist**, abra el archivo **app.js** en un editor de texto. Este archivo se creó anteriormente al ejecutar el comando **express**.
2.  Agregue el siguiente código al comienzo del archivo **app.js**. Esto inicializará **TaskList** con la cadena de conexión para el servidor de MongoDB:

        var TaskList = require('./routes/tasklist');
        var taskList = new TaskList(process.env.CUSTOMCONNSTR_MONGOLAB_URI);

    Observe la segunda línea; obtiene acceso a una variable de entorno que configurará más tarde, que contiene la información de conexión para su instancia de mongo. Si tiene una instancia de mongo local que se ejecuta para fines de desarrollo, es posible que desee establecer temporalmente este valor en "localhost" en vez de `process.env.CUSTOMCONNSTR_MONGOLAB_URI`.

3.  Busque las líneas que comienzan con `app.get` y reemplácelas por las siguientes líneas:

        app.get('/', taskList.showTasks.bind(taskList));
        app.post('/addtask', taskList.addTask.bind(taskList));
        app.post('/completetask', taskList.completeTask.bind(taskList));

    De esta manera se agregan las funciones que se definen en **tasklist.js** como rutas.

4.  Guarde el archivo **app.js**.

## <a name="deploy"></a>Implementación de la aplicación

Ahora que la aplicación se ha desarrollado, es el momento de crear un sitio web de Azure para hospedarla, configurar el sitio web e implementar el código. Es fundamental para esta sección el uso de la cadena de conexión de MongoDB (URI). Va a configurar una variable de entorno en su sitio web con este URI para mantener el URI separado del código. Debe tratar el URI como información confidencial debido a que contiene credenciales para conectarse a su base de datos.

Los pasos de esta sección usan las herramientas de la línea de comandos de Azure para crear un sitio web de Azure nuevo y, posteriormente, usan Git para implementar su aplicación. Para realizar estos pasos debe tener una suscripción a Azure.

### Instalación de la herramienta de línea de comandos de Azure para Mac y Linux

Para instalar las herramientas de línea de comandos, use el siguiente comando:

    npm install azure-cli -g

Si ya instaló el **SDK de Azure para Node.js** desde [Azure Developer Center][Azure Developer Center], entonces las herramientas de línea de comandos ya deberían estar instaladas. Para obtener más información, consulte [Herramienta de línea de comandos de Azure para Mac y Linux][Herramienta de línea de comandos de Azure para Mac y Linux].

A pesar de que las herramientas de línea de comandos de Azure se crearon principalmente para los usuarios de Mac y Linux, están basadas en Node.js y deberían funcionar en cualquier sistema capaz de ejecutar Node.

### Importación de la configuración de publicación

Antes de usar las herramientas de línea de comandos de Azure, primero debe descargar un archivo que contenga información sobre su suscripción. Realice los siguientes pasos para descargar e importar este archivo.

1.  Desde la línea de comandos, escriba el siguiente comando para iniciar el explorador y dirigirse a la página de descarga. Si se le solicita, inicie sesión con la cuenta asociada a su suscripción.

        azure account download

    ![La página de descarga][La página de descarga]

    La descarga del archivo debe iniciarse automáticamente; si esto no ocurre, puede hacer clic en el vínculo al comienzo de la página para descargar el archivo manualmente.

2.  Después de que se haya terminado la descarga del archivo, use el siguiente comando para importar la configuración:

        azure account import <path-to-file>

    Especifique la ruta y el nombre de archivo del archivo de configuración de publicación que descargó en el paso anterior. Después de que el comando se completa, debería ver un resultado similar al siguiente:

        info:   Executing command account import
        info:   Found subscription: subscriptionname
        info:   Setting default subscription to: subscriptionname
        warn:   The '/Users/user1/.azure/publishSettings.xml' file contains sensitive information.
        warn:   Remember to delete it now that it has been imported.
        info:   Account publish settings imported successfully
        info:   account import command OK

3.  Después de que se haya completado la importación, debe eliminar el archivo de configuración de publicación debido a que ya no es necesario y contiene información confidencial relacionada con su suscripción a Azure.

### Creación de un sitio web nuevo e inserción del código

La creación de un sitio web en Azure es muy fácil. Si este es su primer sitio web de Azure, debe usar el portal. Si ya tiene al menos un sitio web, vaya al paso 7.

1.  En el Portal de Azure, haga clic en **New**.  
![New][New]
2.  Seleccione **Proceso \> Sitio web \> Quick Create**. 
![CreateSite][CreateSite]
3.  Escriba un prefijo de una dirección URL. Seleccione un nombre de su preferencia, pero tenga en cuenta que debe ser único ('mymongoapp' probablemente no estará disponible).
4.  Haga clic en **Crear sitio web**.
5.  Cuando se complete la creación del sitio web, haga clic en el nombre del sitio web en la lista de sitios web. Aparece el panel del sitio web.  
![WebSiteDashboard][WebSiteDashboard]
6.  Haga clic en **Set up Git publishing** en **quick glance** y escriba su nombre de usuario y contraseña de git deseados. Utilizará esta contraseña al realizar inserciones en su sitio web (en el paso 9).
7.  Si creó el sitio web usando los pasos anteriores, el proceso se completará con el siguiente comando. Sin embargo, si ya tiene más de un sitio web de Azure, puede omitir los pasos anteriores y crear un sitio web nuevo usando este mismo comando. Desde su directorio de proyectos **tasklist**:

        azure site create myuniquesitename --git  

    Reemplace 'myuniquesitename' por el nombre de sitio único para su sitio web. Si el sitio web se crea como parte de este comando, se le solicitará el centro de datos en el que estará ubicado el sitio. Seleccione el centro de datos que se encuentra geográficamente cerca de su base de datos MongoLab.

    El parámetro `--git` creará:
     A. un repositorio git local en la carpeta **tasklist**, si no existe una.
    A. un [Git remoto][Git remoto] llamado 'azure', que se usará para publicar la aplicación en Azure.
    A. un archivo [iisnode.yml][iisnode.yml], que contiene la configuración usada por Azure para hospedar aplicaciones de Node.
    A. un archivo .gitignore para evitar que la carpeta de módulos de Node se publique en .git.

    Después de que este comando se haya completado, verá un resultado similar al siguiente. Observe que la línea que comienza con **Created website at** contiene la dirección URL del sitio web.

        info:   Executing command site create
        info:   Using location southcentraluswebspace
        info:   Executing `git init`
        info:   Creating default web.config file
        info:   Creating a new web site
        info:   Created web site at  mongodbtasklist.azurewebsites.net
        info:   Initializing repository
        info:   Repository initialized
        info:   Executing `git remote add azure http://gitusername@myuniquesitename.azurewebsites.net/mongodbtasklist.git`
        info:   site create command OK

8.  Use los siguientes comandos para agregar y luego confirmar los archivos en su repositorio local de Git:

        git add .
        git commit -m "adding files"

9.  Inserte su código:

        git push azure master  

    Al realizar los últimos cambios en el repositorio de Git en el sitio web de Azure, debe especificar que la rama objetivo es **master** debido a que esta se usa para el contenido del sitio web. Si se le solicita una contraseña, escriba la contraseña que creó al configurar la publicación git para los sitios web anteriores.

    Verá un resultado similar al siguiente. Azure descarga todos los módulos de npm a medida que la implementación se lleva a cabo.

        Counting objects: 17, done.
        Delta compression using up to 8 threads.
        Compressing objects: 100% (13/13), done.
        Writing objects: 100% (17/17), 3.21 KiB, done.
        Total 17 (delta 0), reused 0 (delta 0)
        remote: New deployment received.
        remote: Updating branch 'master'.
        remote: Preparing deployment for commit id 'ef276f3042'.
        remote: Preparing files for deployment.
        remote: Running NPM.
        ...
        remote: Deploying Web.config to enable Node.js activation.
        remote: Deployment successful.
        To https://username@mongodbtasklist.azurewebsites.net/MongoDBTasklist.git
         * [new branch]      master -> master

¡Ya casi termina!

### Configuración de su entorno

¿Se acuerda de process.env.CUSTOMCONNSTR\_MONGOLAB\_URI en el código? Deseamos rellenar esa variable de entorno con el valor que se proporciona a Azure durante el aprovisionamiento de la base de datos MongoLab.

#### Obtención de la cadena de conexión de MongoLab

[WACOM.INCLUDE [howto-get-connectioninfo-mongolab](../includes/howto-get-connectioninfo-mongolab.md)]

#### Incorporación de la cadena de conexión a las variables de entorno del sitio web

[WACOM.INCLUDE [howto-save-connectioninfo-mongolab](../includes/howto-save-connectioninfo-mongolab.md)]

## ¡Éxito!

Ejecute `azure site browse` desde su directorio de proyectos para abrir automáticamente un explorador o abra un explorador y diríjase manualmente a la dirección URL de su sitio web (myuniquesite.azurewebsites.net):

![Página web que muestra una lista de tareas vacía][Página web que muestra una lista de tareas vacía]

## <a name="manage"></a>Administración de la base de datos

[WACOM.INCLUDE [howto-access-mongolab-ui](../includes/howto-access-mongolab-ui.md)]

¡Enhorabuena! ¡Acaba de iniciar una aplicación Node.js con el respaldo de una base de datos MongoDB hospedada en MongoLab! Ahora que tiene una base de datos MongoLab, puede ponerse en contacto con [\<a href="mailto:support@mongolab.com"\>support@mongolab.com\</a\>][\<a href="mailto:support@mongolab.com"\>support@mongolab.com\</a\>] en caso de tener preguntas o dudas sobre su base de datos o si necesita ayuda con MongoDB o el controlador del nodo. ¡Buena suerte!

  [Aprovisionar la base de datos]: #provision
  [MongoLab]: http://mongolab.com
  [Crear la aplicación]: #create
  [Implementación de la aplicación]: #deploy
  [Administración de la base de datos]: #manage
  [Node.js]: http://nodejs.org
  [Git]: http://git-scm.com
  [Store]: ./media/store-mongolab-web-sites-nodejs-store-data-mongodb/button-store.png
  [1]: ./media/store-mongolab-web-sites-nodejs-store-data-mongodb/entry-mongolab.png
  [ConnectionInfoButton]: ./media/store-mongolab-web-sites-nodejs-store-data-mongodb/button-connectioninfo.png
  [ConnectionInfoScreen]: ./media/store-mongolab-web-sites-nodejs-store-data-mongodb/dialog-mongolab_connectioninfo.png
  [WebSiteConnectionStrings]: ./media/store-mongolab-web-sites-nodejs-store-data-mongodb/focus-mongolab-websiteconnectionstring.png
  [Express]: http://expressjs.com
  [Mongoose]: http://mongoosejs.com
  [Azure Developer Center]: /es-es/develop/nodejs/
  [Herramienta de línea de comandos de Azure para Mac y Linux]: /es-es/develop/nodejs/how-to-guides/command-line-tools/
  [La página de descarga]: ./media/store-mongolab-web-sites-nodejs-store-data-mongodb/azure-account-download-cli.png
  [New]: ./media/store-mongolab-web-sites-nodejs-store-data-mongodb/button-new.png
  [CreateSite]: ./media/store-mongolab-web-sites-nodejs-store-data-mongodb/screen-mongolab-newwebsite.png
  [WebSiteDashboard]: ./media/store-mongolab-web-sites-nodejs-store-data-mongodb/screen-mongolab-websitedashboard.png
  [Git remoto]: http://git-scm.com/docs/git-remote
  [iisnode.yml]: https://github.com/WindowsAzure/iisnode/blob/master/src/samples/configuration/iisnode.yml
  [Página web que muestra una lista de tareas vacía]: ./media/store-mongolab-web-sites-nodejs-store-data-mongodb/todo_list_noframe.png
