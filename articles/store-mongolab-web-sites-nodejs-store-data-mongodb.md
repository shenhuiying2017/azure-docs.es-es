<properties 
	pageTitle="Creación de una aplicación web Node.js en Azure con MongoDB y el complemento MongoLab" 
	description="Aprenda a crear una aplicación web Node.js en Azure que se conecta a una instancia de MongoDB hospedada en MongoLab." 
	tags="azure-classic-portal"
	services="app-service\web, virtual-machines" 
	documentationCenter="nodejs" 
	authors="chrisckchang" 
	manager="wpickett" 
	editor=""/>

<tags 
	ms.service="app-service-web" 
	ms.workload="web" 
	ms.tgt_pltfrm="na" 
	ms.devlang="nodejs" 
	ms.topic="article" 
	ms.date="04/20/2014" 
	ms.author="mwasson"/>






# Creación de una aplicación web Node.js en Azure con MongoDB y el complemento MongoLab


<p><em>Por Eric Sedor, MongoLab</em></p>

¡Saludos, aventurero! Bienvenido a MongoDB como servicio. En este tutorial, aprenderá lo siguiente:

1. [Aprovisionamiento de la base de datos][provision]\: el complemento [MongoLab](http://mongolab.com) de Azure Marketplace le proporcionará una base de datos MongoDB que se hospeda en la nube de Azure y que administra la plataforma de base de datos en la nube de MongoLab.
2. [Creación de la aplicación][create]\: será una aplicación Node.js sencilla para mantener una lista de tareas.
3. [Implementación de la aplicación][deploy]\: uniendo unos cuantos enlaces de configuración, insertaremos nuestro código en [Aplicaciones web del Servicio de aplicaciones](http://go.microsoft.com/fwlink/?LinkId=529714) en un abrir y cerrar de ojos.
4. [Administración de la base de datos][manage]\: por último, le mostraremos el portal de administración de base de datos basado en web de MongoLab, donde puede buscar, visualizar y modificar los datos con facilidad.

En cualquier momento de este tutorial puede enviar un correo electrónico a [support@mongolab.com](mailto:support@mongolab.com) si tiene alguna pregunta.

Antes de continuar, asegúrese de tener instalados los siguientes elementos:

* [Node.js] versión 0.10.29+

* [Git].

[AZURE.INCLUDE [create-account-and-websites-note](../includes/create-account-and-websites-note.md)]

## Inicio rápido
Si tiene algún conocimiento de la Tienda de Azure, use esta sección para comenzar rápidamente. De lo contrario, vaya a la sección [Aprovisionamiento de la base de datos][provision] a continuación.
 
1. Abra Azure Marketplace haciendo clic en **Nuevo** > **Markeplace**. <!-- ![Store][button-store] -->
2. Haga clic en el complemento **MongoLab**. ![MongoLab][entry-mongolab]
3. En la lista de complementos, haga clic en el complemento **MongoLab** y luego en **Información de conexión**. ![BotónConnectionInfo][button-connectioninfo]  
4. Copie **MONGOLAB_URI** en el Portapapeles. ![ConnectionInfoScreen][screen-connectioninfo]
  
	>[AZURE.NOTE]El URI contiene el nombre del usuario de la base de datos y la contraseña. Déle el mismo tratamiento que a la información confidencial y no lo comparta.

5. Agregue el valor a la lista **Cadenas de conexión** en el menú **Configuración** de su aplicación web de Azure: ![WebAppConnectionStrings][focus-website-connectinfo]
6. En **Name** (Nombre), escriba **MONGOLAB_URI**.
7. En **Value** (Valor), pegue la cadena de conexión que se obtuvo en la sección anterior.
8. Seleccione **Custom** (Personalizado) en la lista desplegable Type (Tipo), en lugar del valor predeterminado **SQLAzure**.
9. Ejecute `npm install mongoose` para obtener Mongoose, un controlador de nodo de MongoDB.
10. Configure un enlace en su código para obtener su URI de conexión de MongoLab desde una variable de entorno y conéctese:

        var mongoose = require('mongoose');  
 		...
 		var connectionString = process.env.CUSTOMCONNSTR_MONGOLAB_URI
 		...
 		mongoose.connect(connectionString);

Azure agrega el prefijo **CUSTOMCONNSTR\\_** a la cadena de conexión declarada originalmente, razón por la cual el código hace referencia a** CUSTOMCONNSTR\\_MONGOLAB\\_URI** en vez de a **MONGOLAB\\_URI**.

Ahora, continuemos con el tutorial completo...

<a name="provision"></a>
## Aprovisionamiento de la base de datos

[AZURE.INCLUDE [howto-provision-mongolab](../includes/howto-provision-mongolab.md)]

<a name="create"></a>
## Creación de la aplicación

En esta sección podrá configurar su entorno de desarrollo y poner el código para una aplicación web de listas de tareas básicas usando Node.js, Express y MongoDB. [Express] proporciona un marco de controlador de vista para Node, mientras que [Mongoose] es un controlador para comunicarse con MongoDB en Node.

### Configuración

#### Generación de scaffolding e instalación de los módulos

1. En la línea de comandos, cree y vaya hasta el directorio **tasklist**. Este se será su directorio de proyectos.
2. Escriba el siguiente comando para instalar Express.

		npm install express -g
 
	`-g` indica el modo global, que usamos para que el módulo <strong>express</strong> esté disponible sin especificar una ruta de directorio. Si recibe <strong>Error: EPERM, chmod '/usr/local/bin/express'</strong>, use <strong>sudo</strong> para ejecutar npm con un nivel de privilegio más alto.

    El resultado de este comando debe ser similar al siguiente:

		express@4.9.1 C:\Users\mongolab\AppData\Roaming\npm\node_modules\express
		├── merge-descriptors@0.0.2
		├── utils-merge@1.0.0
		├── fresh@0.2.4
		├── cookie@0.1.2
		├── range-parser@1.0.2
		├── escape-html@1.0.1
		├── cookie-signature@1.0.5
		├── finalhandler@0.2.0
		├── vary@1.0.0
		├── media-typer@0.3.0
		├── parseurl@1.3.0
		├── serve-static@1.6.2
		├── methods@1.1.0
		├── path-to-regexp@0.1.3
		├── depd@0.4.5
		├── qs@2.2.3
		├── on-finished@2.1.0 (ee-first@1.0.5)
		├── debug@2.0.0 (ms@0.6.2)
		├── proxy-addr@1.0.1 (ipaddr.js@0.1.2)
		├── etag@1.3.1 (crc@3.0.0)
		├── send@0.9.2 (destroy@1.0.3, ms@0.6.2, mime@1.2.11)
		├── accepts@1.1.0 (negotiator@0.4.7, mime-types@2.0.1)
		└── type-is@1.5.1 (mime-types@2.0.1)
 
3. Para crear el scaffolding que se usará para esta aplicación, use el comando **express**:

    	express

    Tenga en cuenta que este tutorial usa Express v4.x.x. Si ya tiene el generador de aplicación Express 3 instalado en el sistema, debe desinstalarlo primero:

    	npm uninstall -g express

    Instale ahora el nuevo generador de la versión 4.x.x:

    	npm install -g express-generator

	Una vez que el comando **express** se ejecuta, el resultado debería ser similar al siguiente:

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
		create : ./routes/users.js
		create : ./views
		create : ./views/index.jade
		create : ./views/layout.jade
		create : ./views/error.jade
		create : ./bin
		create : ./bin/www

		install dependencies:
		$ cd . && npm install


	Después de que este comando se complete, debe tener varios directorios y archivos nuevos en el directorio **tasklist**.
	
4. Escriba lo siguiente para instalar los módulos que se describen en el archivo **package.json**:

        npm install

    El resultado de este comando debe ser similar al siguiente:

		cookie-parser@1.3.3 node_modules/cookie-parser
		├── cookie@0.1.2
		└── cookie-signature@1.0.5

		debug@2.0.0 node_modules/debug
		└── ms@0.6.2

		serve-favicon@2.1.4 node_modules/serve-favicon
		├── ms@0.6.2
		├── fresh@0.2.4
		└── etag@1.3.1 (crc@3.0.0)

		morgan@1.3.1 node_modules/morgan
		├── basic-auth@1.0.0
		├── depd@0.4.5
		└── on-finished@2.1.0 (ee-first@1.0.5)

		express@4.9.1 node_modules/express
		├── utils-merge@1.0.0
		├── merge-descriptors@0.0.2
		├── cookie@0.1.2
		├── fresh@0.2.4
		├── escape-html@1.0.1
		├── range-parser@1.0.2
		├── cookie-signature@1.0.5
		├── finalhandler@0.2.0
		├── vary@1.0.0
		├── media-typer@0.3.0
		├── serve-static@1.6.2
		├── parseurl@1.3.0
		├── methods@1.1.0
		├── path-to-regexp@0.1.3
		├── depd@0.4.5
		├── qs@2.2.3
		├── etag@1.3.1 (crc@3.0.0)
		├── on-finished@2.1.0 (ee-first@1.0.5)
		├── proxy-addr@1.0.1 (ipaddr.js@0.1.2)
		├── send@0.9.2 (destroy@1.0.3, ms@0.6.2, mime@1.2.11)
		├── type-is@1.5.1 (mime-types@2.0.1)
		└── accepts@1.1.0 (negotiator@0.4.7, mime-types@2.0.1)

		body-parser@1.8.2 node_modules/body-parser
		├── media-typer@0.3.0
		├── raw-body@1.3.0
		├── bytes@1.0.0
		├── depd@0.4.5
		├── on-finished@2.1.0 (ee-first@1.0.5)
		├── qs@2.2.3
		├── iconv-lite@0.4.4
		└── type-is@1.5.1 (mime-types@2.0.1)

		jade@1.6.0 node_modules/jade
		├── character-parser@1.2.0
		├── commander@2.1.0
		├── void-elements@1.0.0
		├── mkdirp@0.5.0 (minimist@0.0.8)
		├── monocle@1.1.51 (readdirp@0.2.5)
		├── transformers@2.1.0 (promise@2.0.0, css@1.0.8, uglify-js@2.2.5)
		├── constantinople@2.0.1 (uglify-js@2.4.15)
		└── with@3.0.1 (uglify-js@2.4.15)

	El archivo **package.json** es uno de los archivos que se crea con el comando **express**. Este archivo contiene una lista de módulos adicionales necesarios para una aplicación Express. Posteriormente, cuando implemente esta aplicación en Aplicaciones web del Servicio de aplicaciones, se usará este archivo para determinar los módulos que es necesario instalar en Azure para que admita su aplicación.

5. A continuación, escriba el siguiente comando para instalar el módulo Mongoose de manera local, además de guardar una entrada para este en el archivo **package.json**:

		npm install mongoose --save

	El resultado de este comando debe ser similar al siguiente:

		mongoose@3.8.16 node_modules/mongoose
		├── regexp-clone@0.0.1
		├── muri@0.3.1
		├── sliced@0.0.5
		├── hooks@0.2.1
		├── mpath@0.1.1
		├── mpromise@0.4.3
		├── ms@0.1.0
		├── mquery@0.8.0 (debug@0.7.4)
		└── mongodb@1.4.9 (readable-stream@1.0.31, kerberos@0.0.3, bson@0.2.12)
	
### El código

Ahora que nuestro entorno y scaffolding están listos, extenderemos la aplicación básica que se creó con el comando **express** al agregar un archivo** task.js** que contiene el modelo para sus tareas. También podrá modificar el archivo **app.js** existente y crear otro archivo controlador **tasklist.js** para usar el modelo.

#### Crear el modelo

1. En el directorio **tasklist**, cree otro directorio con el nombre **models**.

2. En el directorio **models**, cree otro archivo con el nombre **task.js**. Este archivo contendrá el modelo para las tareas que se crean con su aplicación.

3. Agregue el siguiente código al archivo **task.js**:

        var mongoose = require('mongoose'), 
          Schema = mongoose.Schema;

        var TaskSchema = new Schema({
	      itemName      : String, 
	      itemCategory  : String, 
	      itemCompleted : { type: Boolean, default: false },
	      itemDate      : { type: Date, default: Date.now }
        });

        module.exports = mongoose.model('TaskModel', TaskSchema);

5. Guarde y cierre el archivo **task.js**.

#### Crear el controlador

1. En el directorio **tasklist/routes**, cree otro archivo con el nombre **tasklist.js** y ábralo en un editor de texto.

2. Agregue el siguiente código a **tasklist.js**. De esta manera se carga el módulo **mongoose** y el modelo de tarea se define en task.js. La función TaskList se usa para crear la conexión al servidor de MongoDB según el valor **connection** y proporciona los métodos **showTasks**, **addTask** y **completeTasks**:

		var mongoose = require('mongoose'), 
 		  task = require('../models/task.js');

		module.exports = TaskList;

		function TaskList(connection) {
		  mongoose.connect(connection);
		}

		TaskList.prototype = {
		  showTasks: function(req, res) {
		    task.find({ itemCompleted : false }, function foundTasks(err, items) {
		    res.render('index', { title: 'My ToDo List', tasks: items })
		    });
		  },

		  addTask: function(req,res) {
		    var item = req.body;
		    var newTask = new task();
		    newTask.itemName = item.itemName;
		    newTask.itemCategory = item.itemCategory;
		    newTask.save(function savedTask(err) {
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

3. Guarde el archivo **tasklist.js**.

#### Modificar la vista de índice

1. Cambie los directorios al directorio **views** y abra el archivo **index.jade** en un editor de texto.

2. Reemplace el contenido del archivo **index.jade** por el código siguiente. De esta manera se define la vista para mostrar las tareas existentes, además de un formulario para agregar tareas nuevas y marcar las existentes como terminadas.

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
		        input(name="itemName", type="textbox")
		    tr
		      td Item Category: 
		      td 
		        input(name="itemCategory", type="textbox")
		  input(type="submit", value="Add item")

3. Guarde y cierre el archivo **index.jade**.

#### Reemplazar app.js

1. En el directorio **tasklist**, abra el archivo **app.js** en un editor de texto. Este archivo se creó anteriormente al ejecutar el comando **express**.
2. Agregue el siguiente código al comienzo del archivo **app.js**. Esto inicializará **TaskList** con la cadena de conexión para el servidor de MongoDB:

		var TaskList = require('./routes/tasklist');
		var taskList = new TaskList(process.env.CUSTOMCONNSTR_MONGOLAB_URI);

 	Observe la segunda línea; obtiene acceso a una variable de entorno que configurará más tarde, que contiene la información de conexión para su instancia de mongo. Si tiene una instancia de mongo local que se ejecute con fines de desarrollo, es posible que desee establecer temporalmente este valor en "localhost", en vez de `process.env.CUSTOMCONNSTR_MONGOLAB_URI`.

3. Busque estas líneas:
		
		app.use('/', routes);
		app.use('/users', users);

	Y reemplácelas por:

		app.get('/', taskList.showTasks.bind(taskList));
		app.post('/addtask', taskList.addTask.bind(taskList));
		app.post('/completetask', taskList.completeTask.bind(taskList));

	De esta manera se agregan las funciones que se definen en **tasklist.js** como rutas.

4. Para inicializar la aplicación, vaya a la parte inferior del archivo **app.js** y agregue la línea siguiente:

		app.listen(3000); // Listen on port 3000
		module.exports = app;

5. Guarde el archivo **app.js**.

<a name="deploy"></a>
## Implementación de la aplicación

Ahora que desarrolló la aplicación, es el momento de crear una aplicación web en el Servicio de aplicaciones de Azure para hospedarla, configurar la aplicación web e implementar el código. Es fundamental para esta sección el uso de la cadena de conexión de MongoDB (URI). Va a configurar una variable de entorno en su aplicación web con este URI para mantener el URI separado del código. Debe tratar el URI como información confidencial debido a que contiene credenciales para conectarse a su base de datos.

En los pasos de esta sección se usan las herramientas de línea de comandos de Azure para crear una aplicación web de Azure y luego se usa Git para implementar la aplicación. Para realizar estos pasos debe tener una suscripción a Azure.

### Instalación de la herramienta de línea de comandos de Azure para Mac y Linux

Para instalar las herramientas de línea de comandos, use el siguiente comando:
	
	npm install azure-cli -g

Si ya instaló el <strong>SDK de Azure para Node.js</strong> desde el <a href="/develop/nodejs/">Centro para desarrolladores de Azure</a>, las herramientas de línea de comandos ya deberían estar instaladas. Para obtener más información, consulte <a href="virtual-machines-command-line-tools.md">Herramienta de línea de comandos de Azure para Mac y Linux</a>.

A pesar de que las herramientas de línea de comandos de Azure se crearon principalmente para los usuarios de Mac y Linux, están basadas en Node.js y deberían funcionar en cualquier sistema capaz de ejecutar Node.

### Importación de la configuración de publicación

Antes de usar las herramientas de línea de comandos de Azure, primero debe descargar un archivo que contenga información sobre su suscripción. Realice los siguientes pasos para descargar e importar este archivo.

1. Desde la línea de comandos, escriba el siguiente comando para iniciar el explorador y dirigirse a la página de descarga. Si se le solicita, inicie sesión con la cuenta asociada a su suscripción.

		azure account download
	
	![La página de descarga][download-publishing-settings]
	
	La descarga del archivo debe iniciarse automáticamente; si esto no ocurre, puede hacer clic en el vínculo al comienzo de la página para descargar el archivo manualmente.

2. Después de que se haya terminado la descarga del archivo, use el siguiente comando para importar la configuración:

		azure account import <path-to-file>
		
	Especifique la ruta y el nombre de archivo del archivo de configuración de publicación que descargó en el paso anterior. Después de que el comando se completa, debería ver un resultado similar al siguiente:
	
		info:   Executing command account import
		info:   Found subscription: subscriptionname
		info:   Setting default subscription to: subscriptionname
		warn:   The '/Users/mongolab/.azure/publishSettings.xml' file contains sensitive information.
		warn:   Remember to delete it now that it has been imported.
		info:   Account publish settings imported successfully
		info:   account import command OK


3. Después de que se haya completado la importación, debe eliminar el archivo de configuración de publicación debido a que ya no es necesario y contiene información confidencial relacionada con su suscripción a Azure.

### Creación de una aplicación web e inserción del código

Crear una aplicación web en el Servicio de aplicaciones de Azure es muy sencillo. Si se trata de su primera aplicación web de Azure, debe usar el portal. Si ya tiene al menos un sitio web, vaya al paso 7.

1. En el portal de Azure, haga clic en **Nuevo**. ![Nuevo][button-new]
2. Seleccione **Proceso > Aplicación web > Creación rápida**. <!-- ![Create Web App][screen-mongolab-newwebsite] -->
3. Escriba un prefijo de dirección URL. Seleccione un nombre de su preferencia, pero tenga en cuenta que debe ser único ('mymongoapp' probablemente no estará disponible).
4. Haga clic en **Crear aplicación web**.
5. Cuando concluya la creación de la aplicación web, haga clic en el nombre de la aplicación web en la lista de aplicaciones web. Aparecerá el panel de la aplicación web. <!-- ![Web App Dashboard][screen-mongolab-websitedashboard] -->
6. Haga clic en **Configurar implementación desde control de código fuente** en **vista rápida** y escriba el nombre de usuario y contraseña de git que quiera. Usará esta contraseña al realizar inserciones en su aplicación web (en el paso 9).  
7. Si creó la aplicación web siguiendo los pasos anteriores, el proceso se completará con el siguiente comando. Sin embargo, si ya tiene más de una aplicación web, puede omitir los pasos anteriores y crear otro sitio web con este mismo comando. Desde el directorio del proyecto **tasklist**: 

		azure site create myuniquewebappname --git  
	Reemplace 'myuniquewebappname' por el nombre único de la aplicación web. Si la aplicación web se crea como parte de este comando, se le pedirá el centro de datos en donde se ubicará la aplicación web. Seleccione el centro de datos que se encuentre geográficamente cerca de la base de datos MongoLab.
	
	El parámetro `--git` creará: * un repositorio git local en la carpeta **tasklist**, si no existe ninguna. * un [Git remoto] llamado 'azure', que se usará para publicar la aplicación en Azure. * un archivo [iisnode.yml], que contiene la configuración usada por Azure para hospedar aplicaciones de Node. * un archivo .gitignore para evitar que la carpeta de módulos de Node se publique en .git.
	  
	Después de que este comando se haya completado, verá un resultado similar al siguiente. Observe que la línea que comienza con **Created site at** contiene la dirección URL de la aplicación web.

		info:   Executing command site create
		info:   Using location southcentraluswebspace
		info:   Executing `git init`
		info:   Creating default web.config file
		info:   Creating a new web site
		info:   Created web site at  mongodbtasklist.azurewebsites.net
		info:   Initializing repository
		info:   Repository initialized
		info:   Executing `git remote add azure http://gitusername@myuniquewebappname.azurewebsites.net/mongodbtasklist.git`
		info:   site create command OK

8. Use los siguientes comandos para agregar y luego confirmar los archivos en su repositorio local de Git:

		git add .
		git commit -m "adding files"

9. Inserte su código:

		git push azure master  

	Al insertar los últimos cambios en el repositorio de Git en el Servicio de aplicaciones de Azure, debe especificar que la rama objetivo es **master** debido a que esta se usa para el contenido de la aplicación web. Si se le solicita una contraseña, escriba la contraseña que creó al configurar la publicación git de la aplicación web anterior.
	
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
¿Se acuerda de process.env.CUSTOMCONNSTR_MONGOLAB_URI en el código? Deseamos rellenar esa variable de entorno con el valor que se proporciona a Azure durante el aprovisionamiento de la base de datos MongoLab.

#### Obtención de la cadena de conexión de MongoLab

[AZURE.INCLUDE [howto-get-connectioninfo-mongolab](../includes/howto-get-connectioninfo-mongolab.md)]

#### Incorporación de la cadena de conexión a las variables de entorno de la aplicación web

[AZURE.INCLUDE [howto-save-connectioninfo-mongolab](../includes/howto-save-connectioninfo-mongolab.md)]

## ¡Éxito!

Ejecute `azure site browse` en el directorio del proyecto para abrir automáticamente un explorador o abra un explorador y diríjase manualmente a la dirección URL de su aplicación web (myuniquewebappname.azurewebsites.net):

![Página web que muestra una lista de tareas vacía][node-mongo-finished]

<a name="manage"></a>
## Administración de la base de datos

[AZURE.INCLUDE [howto-access-mongolab-ui](../includes/howto-access-mongolab-ui.md)]

¡Enhorabuena! ¡Acaba de iniciar una aplicación Node.js con el respaldo de una base de datos MongoDB hospedada en MongoLab! Ahora que tiene una base de datos MongoLab, puede ponerse en contacto con [support@mongolab.com](mailto:support@mongolab.com) en caso de tener preguntas o dudas sobre su base de datos o si necesita ayuda con MongoDB o el controlador del nodo. ¡Buena suerte!

## ¿Qué ha cambiado?
* Para obtener una guía del cambio de Sitios web a Servicio de aplicaciones, consulte: [Servicio de aplicaciones de Azure y su impacto en los servicios de Azure existentes](http://go.microsoft.com/fwlink/?LinkId=529714)
* Para obtener una guía del cambio del portal anterior al nuevo, consulte: [Referencia para navegar en el portal de vista previa](http://go.microsoft.com/fwlink/?LinkId=529715)

>[AZURE.NOTE]Si desea empezar a trabajar con el Servicio de aplicaciones de Azure antes de inscribirse para abrir una cuenta de Azure, vaya a [Prueba del Servicio de aplicaciones](http://go.microsoft.com/fwlink/?LinkId=523751), donde podrá crear inmediatamente una aplicación web de inicio de corta duración en el Servicio de aplicaciones. No es necesario proporcionar ninguna tarjeta de crédito ni asumir ningún compromiso.


[screen-mongolab-websitedashboard]: ./media/store-mongolab-web-sites-nodejs-store-data-mongodb/screen-mongolab-websitedashboard.png
[screen-mongolab-newwebsite]: ./media/store-mongolab-web-sites-nodejs-store-data-mongodb/screen-mongolab-newwebsite.png
[button-new]: ./media/store-mongolab-web-sites-nodejs-store-data-mongodb/button-new.png
[button-store]: ./media/store-mongolab-web-sites-nodejs-store-data-mongodb/button-store.png
[entry-mongolab]: ./media/store-mongolab-web-sites-nodejs-store-data-mongodb/entry-mongolab.png
[button-connectioninfo]: ./media/store-mongolab-web-sites-nodejs-store-data-mongodb/button-connectioninfo.png
[screen-connectioninfo]: ./media/store-mongolab-web-sites-nodejs-store-data-mongodb/dialog-mongolab_connectioninfo.png
[focus-website-connectinfo]: ./media/store-mongolab-web-sites-nodejs-store-data-mongodb/focus-mongolab-websiteconnectionstring.png
[provision]: #provision
[create]: #create
[deploy]: #deploy
[manage]: #manage
[Node.js]: http://nodejs.org
[MongoDB]: http://www.mongodb.org
[Git]: http://git-scm.com
[Express]: http://expressjs.com
[Mongoose]: http://mongoosejs.com
[for free]: /pricing/free-trial
[Git remoto]: http://git-scm.com/docs/git-remote
[azure-sdk-for-node]: https://github.com/WindowsAzure/azure-sdk-for-node
[iisnode.yml]: https://github.com/WindowsAzure/iisnode/blob/master/src/samples/configuration/iisnode.yml
[Azure command-line tool for Mac and Linux]: virtual-machines-command-line-tools.md
[Azure Developer Center]: /develop/nodejs/
[Create and deploy a Node.js application to Azure Web Sites]: /develop/nodejs/tutorials/create-a-website-(mac)/
[Continuous deployment using GIT in Azure App Service]: web-sites-publish-source-control.md
[MongoLab]: http://mongolab.com
[Node.js Web Application with Storage on MongoDB (Virtual Machine)]: /develop/nodejs/tutorials/website-with-mongodb-(mac)/
[node-mongo-finished]: ./media/store-mongolab-web-sites-nodejs-store-data-mongodb/todo_list_noframe.png
[node-mongo-express-results]: ./media/store-mongolab-web-sites-nodejs-store-data-mongodb/express_output.png
[download-publishing-settings]: ./media/store-mongolab-web-sites-nodejs-store-data-mongodb/azure-account-download-cli.png
[import-publishing-settings]: ./media/store-mongolab-web-sites-nodejs-store-data-mongodb/azureimport.png
[mongolab-create]: ./media/store-mongolab-web-sites-nodejs-store-data-mongodb/mongolab-create.png
[mongolab-view]: ./media/store-mongolab-web-sites-nodejs-store-data-mongodb/mongolab-view.png




<!--HONumber=54-->