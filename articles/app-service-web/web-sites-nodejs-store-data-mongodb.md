<properties
	pageTitle="Creación de una aplicación web Node.js en Azure con MongoDB en una máquina virtual"
	description="Cómo usar MongoDB para almacenar datos en una aplicación Node.js hospedada en Azure"
	tags="azure-portal"
	services="app-service\web, virtual-machines"
	documentationCenter="nodejs"
	authors="MikeWasson"
	manager="wpickett"
	editor=""/>

<tags
	ms.service="app-service-web"
	ms.workload="web"
	ms.tgt_pltfrm="na"
	ms.devlang="nodejs"
	ms.topic="article"
	ms.date="04/23/2015"
	ms.author="mwasson"/>


# Creación de una aplicación web Node.js en Azure con MongoDB en una máquina virtual

Este tutorial muestra cómo usar [MongoDB] hospedado en una máquina virtual de Azure para almacenar datos y tener acceso a ellos desde una aplicación [Node] hospedada en aplicaciones web del [Servicio de aplicaciones de Azure](http://go.microsoft.com/fwlink/?LinkId=529714). [MongoDB] es una conocida base de datos NoSQL de alto rendimiento con código abierto.

Aprenderá a:

* Configurar una máquina virtual que usa Ubuntu y MongoDB desde el depósito de máquinas virtuales.
* Tener acceso a MongoDB desde una aplicación Node.
* Uso de CLI de Azure para crear una aplicación web en el Servicio de aplicaciones de Azure

Al seguir este tutorial, podrá compilar una aplicación de administración de tareas basadas en web sencilla que permite crear, recuperar y completar tareas. Las tareas se almacenan en MongoDB.

> [AZURE.NOTE]Este tutorial usa una instancia de MongoDB instalada en una máquina virtual. Si prefiere usar una instancia de MongoDB hospedada proporcionada por MongoLabs, consulte [Creación de una aplicación web Node.js en Azure con MongoDB y el complemento de MongoLab](store-mongolab-web-sites-nodejs-store-data-mongodb).

Los archivos del proyecto para este tutorial se almacenarán en un directorio llamado **tasklist** y la aplicación completada tendrá una apariencia similar a la siguiente:

![Página web que muestra una lista de tareas vacía][node-mongo-finished]

> [AZURE.NOTE]Muchos de los pasos que se mencionan a continuación usan la línea de comandos. Para estos pasos, use la línea de comandos de su sistema operativo, como **Windows PowerShell** (Windows) o **Bash** (Unix Shell). En los sistemas OS X puede tener acceso a la línea de comandos mediante la aplicación Terminal.

##Requisitos previos

Los pasos de este tutorial usan Node.js. Debe tener una versión reciente de [Node.js][node] en su entorno de desarrollo.

Adicionalmente, el [Git] debe estar disponible para la línea de comandos en el entorno de desarrollo, ya que se usa para implementar la aplicación en un Sitio web de Azure.

[AZURE.INCLUDE [create-account-and-websites-note](../../includes/create-account-and-websites-note.md)]

>[AZURE.NOTE]Si desea empezar a trabajar con el Servicio de aplicaciones de Azure antes de inscribirse para abrir una cuenta de Azure, vaya a [Prueba del Servicio de aplicaciones](http://go.microsoft.com/fwlink/?LinkId=523751), donde podrá crear inmediatamente una aplicación web de inicio de corta duración en el Servicio de aplicaciones. No es necesario proporcionar ninguna tarjeta de crédito ni asumir ningún compromiso.

##de una máquina virtual

<!--
After you have created the virtual machine in Azure and installed MongoDB, be sure to remember the DNS name of the virtual machine ("testlinuxvm.cloudapp.net", for example) and the external port for MongoDB that you specified in the endpoint.  You will need this information later in the tutorial.-->

Si bien es posible crear una máquina virtual nueva y luego instalar MongoDB en ella siguiendo las [guías de instalación de MongoDB][installguides], ya está disponible una máquina virtual con MongoDB preinstalado en Azure Marketplace. Los pasos siguientes muestran cómo usar una de las muchas plantillas existentes de máquina virtual.

> [AZURE.NOTE]La imagen de comunidad utilizada en este tutorial almacena datos de MongoDB en el disco del sistema operativo. Aunque esto es suficiente para los objetivos del tutorial, el almacenamiento de datos de MongoDB en un disco de datos proporcionará un rendimiento mayor. Para obtener los pasos necesarios para crear una nueva máquina virtual, incluido un disco de datos, y almacenar los datos de MongoDB en el disco de datos, vea [Instalación de MongoDB en Linux en Azure][mongodbonazure].

1. Inicie sesión en el [Portal de Azure][azureportal].

3. Haga clic en **Nuevo** > **Datos + almacenamiento** > **Marketplace**.

	![Captura de pantalla de la selección del depósito de máquinas virtuales][selectdepo]

2. En el cuadro de búsqueda en la parte superior, escriba "mongodb", a continuación, seleccione **MongoDB v2.2.3 on Hardened Ubuntu 12.04 LTS**. Haga clic en **Crear** para continuar.

	![Captura de pantalla de mongodb v2.2.3 seleccionado en imagen protegida de ubuntu][selectedimage]

	Haga clic en la flecha de la parte inferior para pasar a la siguiente pantalla.

7. Configure el **nombre de Host**, el **nombre de usuario** y **contraseña** del administrador y el **grupo de recursos**. A continuación, haga clic en **Configuración opcional**.

	![Captura de pantalla de la configuración de la máquina virtual][vmconfig]

8. Establezca extremos adicionales para la máquina virtual. Debido a que tendremos acceso a MongoDB en esta máquina virtual, agregue un extremo nuevo con la siguiente información:

	* Nombre: MongoDB
	* Protocolo: TCP
	* Puerto público: 27017
	* Puerto privado: 27017

	Para exponer el portal web de MongoDB, agregue otro extremo con la siguiente información:

	* Nombre: MongoDBWeb
	* Protocolo: TCP
	* Puerto público: 28017
	* Puerto privado: 28017

	![Captura de pantalla de la configuración del extremo][vmendpoint]

9. Haga clic en **Aceptar** dos veces y, a continuación, haga clic en **Crear** para crear la máquina virtual.

	Una vez creada la máquina virtual, aparecerá en el panel de inicio y podrá hacer clic en él para abrir la hoja de la máquina virtual. Debe poder abrir un explorador web para **http://&lt;YourVMDNSName&gt;.cloudapp.net:28017/** para comprobar que se está ejecutando MongoDB. En la parte inferior de la página hay un registro que muestra información sobre el servicio, similar al siguiente:

		Fri Mar  7 18:57:16 [initandlisten] MongoDB starting : pid=1019 port=27017 dbpath=/var/lib/mongodb 64-bit host=localhost.localdomain
           18:57:16 [initandlisten] db version v2.2.3, pdfile version 4.5
           18:57:16 [initandlisten] git version: f570771a5d8a3846eb7586eaffcf4c2f4a96bf08
           18:57:16 [initandlisten] build info: Linux ip-10-2-29-40 2.6.21.7-2.ec2.v1.2.fc8xen #1 SMP Fri Nov 20 17:48:28 EST 2009 x86_64 BOOST_LIB_VERSION=1_49
           18:57:16 [initandlisten] options: { config: "/etc/mongodb.conf", dbpath: "/var/lib/mongodb", logappend: "true", logpath: "/var/log/mongodb/mongodb.log" }
           18:57:16 [initandlisten] journal dir=/var/lib/mongodb/journal
           18:57:16 [initandlisten] recover : no journal files present, no recovery needed
           18:57:17 [initandlisten] waiting for connections on port 27017

	Si el registro muestra errores, consulte en la [documentación de MongoDB][mongodocs] los pasos para solucionar problemas.


##Instalación de módulos y generación de scaffolding

En esta sección podrá crear una nueva aplicación Node en su entorno de desarrollo y usar npm para agregar paquetes de módulos. Para la aplicación de lista de tareas, usará los módulos [Express] y [Mongoose]. El módulo Express proporciona un marco de controlador de vista de modelo para Node, mientras que Mongoose es un controlador para comunicarse con MongoDB.

###Instalar Express y generar scaffolding

1. En la línea de comandos, cambie de directorio al directorio **tasklist**. Si el directorio **tasklist** no existe, créelo.

	> [AZURE.NOTE]Este tutorial hace referencia a la carpeta **tasklist**. La ruta completa a esta carpeta se omite, debido a que la semántica de la ruta es diferente entre los sistemas operativos. Debe crear esta carpeta en una ubicación a la que tenga fácil acceso en su sistema de archivos local, como **~/node/tasklist** o **c:\node\tasklist**.

2. Escriba el siguiente comando para instalar el comando Express.

	npm install express-generator -g

	> [AZURE.NOTE]Cuando se usa el parámetro '-g' en algunos sistemas operativos, es posible recibir un error ___Error: EPERM, chmod '/ usr/local/bin/express'___ y que se solicite ejecutar la cuenta como administrador. Si ocurre, utilice el comando `sudo` para ejecutar npm en un nivel de privilegio más elevado.

    El resultado de este comando debe ser similar al siguiente:

		express-generator@4.0.0 C:\Users\username\AppData\Roaming\npm\node_modules\express-generator
		├── mkdirp@0.3.5
		└── commander@1.3.2 (keypress@0.1.0)

	> [AZURE.NOTE]El parámetro "-g" que se utiliza cuando se instala el módulo Express lo instala de manera global. Esto se realiza para tener acceso al comando ___express___ para generar el scaffolding de la aplicación web sin tener que escribir información de ruta adicional.

4. Para crear el scaffolding que se usará para esta aplicación, use el comando **express**:

    express

	El resultado de este comando debe ser similar al siguiente:

		   create : .
		   create : ./package.json
		   create : ./app.js
		   create : ./public
		   create : ./public/images
		   create : ./routes
		   create : ./routes/index.js
		   create : ./routes/users.js
		   create : ./public/stylesheets
		   create : ./public/stylesheets/style.css
		   create : ./views
		   create : ./views/index.jade
		   create : ./views/layout.jade
		   create : ./views/error.jade
		   create : ./public/javascripts
		   create : ./bin
		   create : ./bin/www

		   install dependencies:
		     $ cd . && npm install

		   run the app:
		     $ DEBUG=my-application ./bin/www

	Una vez finalizado este comando, tendrá varios directorios y archivos nuevos en el directorio **tasklist**.

3. Copie el archivo **tasklist/bin/www** a un archivo llamado **server.js** en la carpeta **tasklist**. Aplicaciones web del Servicio de aplicaciones espera que el punto de entrada de una aplicación Node.js sea **server.js** o **app.js**. Como **app.js** ya existe pero no es el punto de entrada, debemos utilizar **server.js**.

4. Modifique el archivo **server.js** para quitar uno de los caracteres '.' de la línea siguiente.

		var app = require('../app');

	La línea modificada debería tener el aspecto siguiente.

		var app = require('./app');

	Esto es necesario porque **server.js** (anteriormente **bin/www**), ahora está en la misma carpeta que el archivo **app.js** necesario.

###Instalar módulos adicionales

El archivo **package.json** es uno de los archivos que se crea con el comando **express**. Este archivo contiene una lista de módulos adicionales necesarios para una aplicación Express. Posteriormente, cuando implemente esta aplicación en aplicaciones web del Servicio de aplicaciones, se usará este archivo para determinar los módulos que se necesitan instalar en Azure para admitir su aplicación.

1. En la carpeta **tasklist**, use el siguiente comando para instalar lo módulos que se describen en el archivo **package.json**:

        npm install

    El resultado de este comando debe ser similar al siguiente:

		debug@0.7.4 node_modules\debug

		cookie-parser@1.0.1 node_modules\cookie-parser
		├── cookie-signature@1.0.3
		└── cookie@0.1.0

		morgan@1.0.0 node_modules\morgan
		└── bytes@0.2.1

		body-parser@1.0.2 node_modules\body-parser
		├── qs@0.6.6
		├── raw-body@1.1.4 (bytes@0.3.0)
		└── type-is@1.1.0 (mime@1.2.11)

		express@4.0.0 node_modules\express
		├── methods@0.1.0
		├── parseurl@1.0.1
		├── merge-descriptors@0.0.2
		├── utils-merge@1.0.0
		├── escape-html@1.0.1
		├── cookie-signature@1.0.3
		├── fresh@0.2.2
		├── range-parser@1.0.0
		├── buffer-crc32@0.2.1
		├── qs@0.6.6
		├── cookie@0.1.0
		├── path-to-regexp@0.1.2
		├── send@0.2.0 (mime@1.2.11)
		├── type-is@1.0.0 (mime@1.2.11)
		├── accepts@1.0.0 (negotiator@0.3.0, mime@1.2.11)
		└── serve-static@1.0.1 (send@0.1.4)

		jade@1.3.1 node_modules\jade
		├── character-parser@1.2.0
		├── commander@2.1.0
		├── mkdirp@0.3.5
		├── monocle@1.1.51 (readdirp@0.2.5)
		├── constantinople@2.0.0 (uglify-js@2.4.13)
		├── with@3.0.0 (uglify-js@2.4.13)
		└── transformers@2.1.0 (promise@2.0.0, css@1.0.8, uglify-js@2.2.5)

	De esta manera se instalan todos los módulos predeterminados usados por una aplicación Express.

2. A continuación, escriba el siguiente comando para instalar el módulo Mongoose de manera local, además de guardar una entrada para este en el archivo **package.json**:

		npm install mongoose --save

	El resultado de este comando debe ser similar al siguiente:

		mongoose@3.8.8 node_modules\mongoose
		├── regexp-clone@0.0.1
		├── sliced@0.0.5
		├── muri@0.3.1
		├── hooks@0.2.1
		├── mpath@0.1.1
		├── mpromise@0.4.3
		├── ms@0.1.0
		├── mquery@0.5.3
		└── mongodb@1.3.23 (kerberos@0.0.3, bson@0.2.5)

    > [AZURE.NOTE]Puede ignorar de manera segura cualquier mensaje sobre la instalación del analizador bson de C++.

##Uso de MongoDB en una aplicación Node

En esta sección extenderá la aplicación básica creada por el comando **express** agregando un archivo **task.js** que contiene el modelo para sus tareas. También podrá modificar el archivo **app.js** existente y crear un archivo controlador **tasklist.js** nuevo para usar el modelo.

### Crear el modelo

1. En el directorio **tasklist**, cree un directorio nuevo con el nombre **models**.

2. En el directorio **models**, cree un archivo nuevo con el nombre **task.js**. Este archivo contendrá el modelo para las tareas que se crean con su aplicación.

3. Al comienzo del archivo **task.js**, agregue el siguiente código para hacer referencia a las bibliotecas requeridas:

        var mongoose = require('mongoose'),
	        Schema = mongoose.Schema;

4. A continuación, agregará código para definir y exportar el modelo. Este modelo se usará para realizar interacciones con la base de datos MongoDB.

        var TaskSchema = new Schema({
	        itemName      : String,
	        itemCategory  : String,
	        itemCompleted : { type: Boolean, default: false },
	        itemDate      : { type: Date, default: Date.now }
        });

        module.exports = mongoose.model('TaskModel', TaskSchema);

5. Guarde y cierre el archivo **task.js**.

###Crear el controlador

1. En el directorio **tasklist/routes**, cree un archivo nuevo con el nombre **tasklist.js** y ábralo en un editor de texto.

2. Agregue el siguiente código a **tasklist.js**. De esta manera se carga el módulo mongoose y el modelo de tarea se define en **task.js**. La función TaskList se usa para crear la conexión con el servidor de MongoDB según el valor de **connection**:

		var mongoose = require('mongoose'),
	        task = require('../models/task.js');

		module.exports = TaskList;

		function TaskList(connection) {
  		  mongoose.connect(connection);
		}

2. Continúe agregando al archivo **tasklist.js** los métodos usados para **showTasks**, **addTask** y **completeTasks**:

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

3. Guarde el archivo **tasklist.js**.

### Modificar app.js

1. En el directorio **tasklist**, abra el archivo **app.js** en un editor de texto. Este archivo se creó anteriormente al ejecutar el comando **express**.

2. Agregue el siguiente código al comienzo del archivo **app.js**. Esto inicializará **TaskList** con la cadena de conexión para el servidor de MongoDB:

        var TaskList = require('./routes/tasklist');
		var taskList = new TaskList(process.env.MONGODB_URI);

	Observe la segunda línea; obtiene acceso a una variable de entorno que configurará más tarde, que contiene la información de conexión para su instancia de mongo. Si tiene una instancia de mongo local que se ejecuta para fines de desarrollo, es posible que desee establecer temporalmente este valor en "localhost" en vez de process.env.MONGODB_URI.

3. Encuentre las líneas siguientes:

		app.use('/', routes);
		app.use('/users', users);

	Sustituya las dos líneas anteriores por lo siguiente:

    	app.get('/', taskList.showTasks.bind(taskList));
    	app.post('/addtask', taskList.addTask.bind(taskList));
    	app.post('/completetask', taskList.completeTask.bind(taskList));

	De esta manera se agregan las funciones que se definen en **tasklist.js** como rutas.

4. Guarde el archivo **app.js**.

###Modificar la vista de índice

1. Cambie al directorio **views** y abra el archivo **index.jade** en un editor de texto.

2. Reemplace el contenido del archivo **index.jade** por el código siguiente. De esta manera se define la vista para mostrar las tareas existentes, además de un formulario para agregar tareas nuevas y marcar las existentes como terminadas.

		h1= title
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

3. Guarde y cierre el archivo **index.jade**.

<!-- ##Run your application locally

To test the application on your local machine, perform the following steps:

1. From the command-line, change directories to the **tasklist** directory.

2. Set the MONGODB_URI environment variable on your development environment to point to the virtual machine hosting MongoDB. In the examples below, replace __mymongodb__ with your virtual machine name.

	On a Windows system, use the following to set the environment variable.

		set MONGODB_URI=mongodb://mymongodb.cloudapp.net/tasks

	On an OS X or Linux-based system, use the following to set the environment variable.

		set MONGODB_URI=mongodb://mymongodb.cloudapp.net/tasks
		export MONGODB_URI

	This will instruct the application to connect to MongoDB on the __mymongodb.cloudapp.net__ virtual machine created earlier, and to use a DB named 'tasks'.

2. Use the following command to launch the application locally:

        node app.js

3. Open a web browser and navigate to http://localhost:3000. This should display a web page similar to the following:

    ![A webpage displaying an empty tasklist][node-mongo-finished]

4. Use the provided fields for **Item Name** and **Item Category** to enter information, and then click **Add item**.

    ![An image of the add item field with populated values.][node-mongo-add-item]

5. The page should update to display the item in the ToDo List table.

    ![An image of the new item in the list of tasks][node-mongo-list-items]

6. To complete a task, simply check the checkbox in the Complete column, and then click **Update tasks**. While there is no visual change after clicking **Update tasks**, the document entry in MongoDB has now been marked as completed.

7. To stop the node process, go to the command-line and press the **CTRL** and **C** keys. -->

##Implementación de su aplicación en Azure

En los pasos de esta sección se usan las herramientas de línea de comandos de Azure para crear una nueva aplicación web en el Servicio de aplicaciones de Azure y después implementar la aplicación mediante Git. Para realizar estos pasos debe tener una suscripción a Azure.

> [AZURE.NOTE]Estos pasos también pueden llevarse a cabo en el portal de Azure. Para saber cómo usar paso a paso el portal de Azure para implementar una aplicación Node.js, consulte [Creación e implementación de una aplicación Node.js en un Sitio web Azure](web-sites-nodejs-develop-deploy-mac.md).

> [AZURE.NOTE]Si esta es la primera aplicación web del Servicio de aplicaciones que crea, debe usar el portal de Azure para implementarla.

###Instalación de la Interfaz de línea de comandos de Azure (CLI de Azure)

La CLI de Azure le permite realizar operaciones de administración para servicios de Azure. Si todavía no ha instalado ni configurado la CLI de Azure en su entorno de desarrollo, consulte [Instalación y configuración de la CLI de Azure](../xplat-cli-install.md) para obtener instrucciones.

###Crear una aplicación web del Servicio de aplicaciones

1. En la línea de comandos, cambie de directorio al directorio **tasklist**.

2. Utilice el comando siguiente para crear una nueva aplicación web del Servicio de aplicaciones. Reemplace "myuniquewebappname" por el nombre único de la aplicación web. Este valor se usa como parte de la dirección URL para la aplicación web resultante.

		azure site create myuniqueappname --git

	Se le pedirá que especifique el centro de datos en el que se ubicará la aplicación web. Seleccione el centro de datos que se encuentre geográficamente cerca de su ubicación.

	El parámetro `--git` creará un repositorio de Git localmente en la carpeta **tasklist** si no existe ninguno. Creará también un [Git remoto] llamado "azure" que se usará para publicar la aplicación en Azure. Creará un[ iisnode.yml] que contiene la configuración que utiliza Azure para hospedar aplicaciones Node. Finalmente, creará también un archivo .gitignore para excluir la carpeta de módulos de Node para que se publique en .git.

	> [AZURE.NOTE]Si este comando se ejecutó desde un directorio que ya contiene un repositorio de Git, no reinicializará el repositorio.

	> [AZURE.NOTE]Si se omite el parámetro '--git', aunque el directorio contenga un repositorio Git, el 'azure' remoto se creará.

	Después de que este comando se haya completado, verá un resultado similar al siguiente. Observe que la línea que comienza con **Sitio web creado en** contiene la dirección URL de la aplicación web del Servicio de aplicaciones.

		info:   Executing command site create
		info:   Using location southcentraluswebspace
		info:   Executing `git init`
		info:   Creating default web.config file
		info:   Creating a new web site
		info:   Created web site at  mongodbtasklist.azurewebsites.net
		info:   Initializing repository
		info:   Repository initialized
		info:   Executing `git remote add azure http://username@mongodbtasklist.azurewebsites.net/mongodbtasklist.git`
		info:   site create command OK

	> [AZURE.NOTE> Si esta es la primera aplicación web del Servicio de aplicaciones de su suscripción, se le pedirá que use el portal para crear la aplicación web. Para obtener más información, consulte [Desarrollo e implementación de una aplicación web de Node.js en el Servicio de aplicaciones de Azure](web-sites-nodejs-develop-deploy-mac.md).

###Establecer la variable de entorno MONGODB_URI

La aplicación espera la cadena de conexión para que la instancia de MongoDB esté disponible en la variable de entorno de MONGODB_URI. Para establecer este valor para la aplicación web, use el siguiente comando:

	azure site config add MONGODB_URI=mongodb://mymongodb.cloudapp.net/tasks

Esto crea una nueva configuración de aplicación para la aplicación web que se usará para completar la variable de entorno MONGODB_URI que lee la aplicación web. Reemplace el valor de 'mymongodb.cloudapp.net' por el nombre de la máquina virtual donde se instaló MongoDB.

###Publicación de la aplicación

1. En la ventana Terminal, cambie al directorio **tasklist** si aún no se encuentra en él.

2. Use los siguientes comandos para agregar y luego confirmar los archivos en su repositorio local de Git:

		git add .
		git commit -m "adding files"

3. Al insertar los últimos cambios del repositorio de Git en el Servicio de aplicaciones de Azure, debe especificar que la rama objetivo es **master**, ya que esta se usa para el contenido de la aplicación web.

		git push azure master

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

4. Una vez completada la operación de inserción, diríjase a la aplicación web usando el comando `azure site browse` para ver su aplicación.

##Pasos siguientes

Si bien los pasos de este artículo describen el uso de MongoDB para almacenar información, puede también usar el servicio de Tabla de Azure. Consulte [Aplicación web Node.js con el servicio de tablas de Azure] para obtener más información.

Para obtener información sobre el uso de una instancia hospedada de MongoDB proporcionada por MongoLab, consulte [Creación de una aplicación Node.js en Azure con MongoDB y el complemento de MongoLab](store-mongolab-web-sites-nodejs-store-data-mongodb.md).

Para obtener información sobre la protección de MongoDB, consulte [MongoDB Security][mongosecurity].

##Recursos adicionales

[Herramienta de línea de comandos de Azure para Mac y Linux][Build and deploy a Node.js web app in Azure App Service] [Implementación continua mediante GIT en el servicio de aplicaciones de Azure]

## Lo que ha cambiado
* Para obtener una guía del cambio de Sitios web a Servicio de aplicaciones, consulte: [Servicio de aplicaciones de Azure y su impacto en los servicios de Azure existentes](http://go.microsoft.com/fwlink/?LinkId=529714)
* Para obtener una guía del cambio del portal anterior al nuevo, consulte: [Referencia para navegar en el portal de vista previa](http://go.microsoft.com/fwlink/?LinkId=529715)

[mongosecurity]: http://docs.mongodb.org/manual/security/
[node]: http://nodejs.org
[MongoDB]: http://www.mongodb.org
[Git]: http://git-scm.com
[Express]: http://expressjs.com
[Mongoose]: http://mongoosejs.com
[for free]: /pricing/free-trial
[Git remoto]: http://git-scm.com/docs/git-remote
[azure-sdk-for-node]: https://github.com/WindowsAzure/azure-sdk-for-node
[ iisnode.yml]: https://github.com/WindowsAzure/iisnode/blob/master/src/samples/configuration/iisnode.yml
[Azure command-line tool for Mac and Linux]: ../virtual-machines-command-line-tools.md
[Azure Developer Center]: /develop/nodejs/
[Build and deploy a Node.js web app in Azure App Service]: web-sites-nodejs-develop-deploy-mac.md
[Implementación continua mediante GIT en el servicio de aplicaciones de Azure]: web-sites-publish-source-control.md
[Aplicación web Node.js con el servicio de tablas de Azure]: storage-nodejs-use-table-storage-web-site.md
[node-mongo-finished]: ./media/web-sites-nodejs-store-data-mongodb/todo_list_empty.png
[node-mongo-express-results]: ./media/store-mongodb-web-sites-nodejs-use-mac/express_output.png
[node-mongo-add-item]: ./media/web-sites-nodejs-store-data-mongodb/todo_add_item.png
[node-mongo-list-items]: ./media/web-sites-nodejs-store-data-mongodb/todo_list_items.png
[download-publishing-settings]: ./media/store-mongodb-web-sites-nodejs-use-mac/azure-account-download-cli.png
[installguides]: http://docs.mongodb.org/manual/installation/
[azureportal]: https://portal.azure.com
[mongodocs]: http://docs.mongodb.org/manual/
[Azure CLI]: ../xplat-cli.md

[selectdepo]: ./media/web-sites-nodejs-store-data-mongodb/browsedepot.png
[selectedimage]: ./media/web-sites-nodejs-store-data-mongodb/selectimage.png
[selectstorage]: ./media/web-sites-nodejs-store-data-mongodb/storageaccount.png
[register]: ./media/web-sites-nodejs-store-data-mongodb/register.png
[myimage]: ./media/web-sites-nodejs-store-data-mongodb/myimages.png
[vmname]: ./media/web-sites-nodejs-store-data-mongodb/vmname.png
[vmconfig]: ./media/web-sites-nodejs-store-data-mongodb/vmconfig.png
[vmendpoint]: ./media/web-sites-nodejs-store-data-mongodb/endpoints.png
[mongodbonazure]: http://docs.mongodb.org/ecosystem/tutorial/install-mongodb-on-linux-in-azure/
 

<!----HONumber=July15_HO4-->