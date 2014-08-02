<properties linkid="develop-node-website-with-mongodb-mac" urlDisplayName="Web site with MongoDB" pageTitle="Node.js web site with MongoDB on a VM - Azure tutorial" metaKeywords="Azure tutorial MongoDB, MongoDB store data, access data MongoDB Node, Azure Node app" description="A tutorial that teaches you how to use MongoDB to store and access data from a Node application hosted on Azure." metaCanonical="http://www.windowsazure.com/en-us/develop/nodejs/tutorials/website-with-mongodb-mongolab/" services="web-sites,virtual-machines" documentationCenter="Node.js" title="Node.js Web Application with Storage on MongoDB (Virtual Machine)" authors="" solutions="" writer="" manager="" editor="" />

Creación de una aplicación Node.js en Azure con MongoDB en una máquina virtual
==============================================================================

Este tutorial le muestra cómo usar [MongoDB](http://www.mongodb.org) hospedado en una máquina virtual de Azure para almacenar datos y tener acceso a ellos desde una aplicación [Node](http://nodejs.org) hospedada en un sitio web de Azure. [MongoDB](http://www.mongodb.org) es una conocida base de datos NoSQL de alto rendimiento de código abierto.

Aprenderá a:

-   Configurar una máquina virtual que usa Ubuntu y MongoDB desde el depósito de máquinas virtuales.
-   Tener acceso a MongoDB desde una aplicación Node.
-   Usar las herramientas entre plataformas de Azure para crear un sitio web de Azure.

Al seguir este tutorial, podrá compilar una aplicación de administración de tareas basadas en web sencilla que permite crear, recuperar y completar tareas. Las tareas se almacenan en MongoDB.

> [WACOM.NOTE] Este tutorial usa una instancia de MongoDB instalada en una máquina virtual. Si prefiere usar una instancia de MongoDB hospedada proporcionada por MongoLabs, consulte [Creación de una aplicación Node.js en Azure con MongoDB y el complemento de MongoLab](/en-us/develop/nodejs/tutorials/website-with-mongodb-mongolab/).

Los archivos del proyecto para este tutorial se almacenarán en un directorio llamado **tasklist** y la aplicación completada tendrá una apariencia similar a la siguiente:

![Página web que muestra una lista de tareas vacía](./media/store-mongodb-web-sites-nodejs-use-mac/todo_list_empty.png)

> [WACOM.NOTE] Muchos de los pasos que se mencionan a continuación usan la línea de comandos. Para estos pasos, use la línea de comandos de su sistema operativo, como **Windows PowerShell** (Windows) o **Bash** (Unix Shell). En los sistemas OS X puede tener acceso a la línea de comandos mediante la aplicación Terminal.

Requisitos previos
------------------

Los pasos de este tutorial usan Node.js. Debe tener una versión reciente de [Node.js](http://nodejs.org) en su entorno de desarrollo.

Adicionalmente, el [Git](http://git-scm.com) debe estar disponible desde la línea de comandos en su entorno de desarrollo, debido a que se utiliza para implementar la aplicación en un sitio web de Azure.

[WACOM.INCLUDE [create-account-and-websites-note](../includes/create-account-and-websites-note.md)]

Crear una máquina virtual
-------------------------

Si bien es posible crear una máquina virtual nueva y luego instalar MongoDB en ella siguiendo las [guías de instalación de MongoDB](http://docs.mongodb.org/manual/installation/), la mayoría de este trabajo ya lo realizó la comunidad y está disponible en el depósito de máquinas virtuales. Los siguientes pasos demuestran cómo usar una imagen del depósito de máquinas virtuales que ya tiene Mongo DB instalado y configurado.

1.  Inicie sesión en el [Portal de administración de Azure](https://manage.windowsazure.com/), seleccione **Máquinas virtuales**, seleccione **Images** y, a continuación, seleccione **VM Depot**.

    ![Captura de pantalla de la selección del depósito de máquinas virtuales](./media/web-sites-nodejs-store-data-mongodb/browsedepot.png)

2.  Seleccione una imagen que incluya MongoDB. En este caso, seleccioné Ubuntu para reducir la lista a solo imágenes según la distribución de Ubuntu Linux. Por último, seleccioné MongoDB v2.2.3 en una imagen protegida de Ubuntu

    ![Captura de pantalla de mongodb v2.2.3 seleccionado en imagen protegida de ubuntu](./media/web-sites-nodejs-store-data-mongodb/selectimage.png)

    > [WACOM.NOTE] Asegúrese de seleccionar **More** para ver toda la información sobre la imagen. Algunas imágenes pueden tener una configuración adicional que se requiere después de haber creado una máquina virtual usando la imagen.

    Haga clic en la flecha de la parte inferior para pasar a la siguiente pantalla.

3.  Seleccione la región y la cuenta de almacenamiento que se usarán para almacenar el VHD para esta imagen. Haga clic en la marca de verificación para continuar.

    ![Captura de pantalla de selección de una cuenta de almacenamiento](./media/web-sites-nodejs-store-data-mongodb/storageaccount.png)

    > [WACOM.NOTE] De esta manera se inicia un proceso de copia que copia la imagen desde el depósito de máquinas virtuales hasta la cuenta de almacenamiento especificada. Esta operación puede tardar bastante tiempo, 15 minutos o más.

4.  Después de que el estado de la imagen cambia a **Pending registration**, seleccione **Register** y escriba un nombre descriptivo para la imagen nueva. Haga clic en la marca de verificación para continuar.

    ![Captura de pantalla del registro de una imagen](./media/web-sites-nodejs-store-data-mongodb/register.png)

5.  Después de que el estado de la imagen cambia a **Available**, seleccione **+ New**, **Máquina virtual**, **From Gallery**. Cuando se le pida que **elija una imagen**, seleccione **My Images** y, a continuación, la imagen que se creó en los pasos anteriores. Haga clic en la flecha para continuar.

    ![Captura de pantalla de la imagen](./media/web-sites-nodejs-store-data-mongodb/myimages.png)

6.  Proporciona el nombre de la máquina virtual, el tamaño y el nombre de usuario. Haga clic en la flecha para continuar.

    ![Captura de pantalla del nombre de la máquina virtual, nombre de usuario, etc.](./media/web-sites-nodejs-store-data-mongodb/vmname.png)

    > [WACOM.NOTE] En este tutorial, no necesitará usar SSH para conectarse de manera remota a su máquina virtual. Seleccione **Use a password** y proporcione una contraseña si no está familiarizado con el uso de un certificado con SSH.
    >
    > Para obtener más información sobre el uso de SSH con una máquina virtual del Linux en Azure, consulte [Utilización de SSH con Linux en Azure](http://www.windowsazure.com/en-us/documentation/articles/linux-use-ssh-key/).

7.  Seleccione si va a usar un servicio en la nube nuevo o existente y la región en la que se va a crear la máquina virtual. Haga clic en la flecha para continuar.

    ![Captura de pantalla de la configuración de la máquina virtual](./media/web-sites-nodejs-store-data-mongodb/vmconfig.png)

8.  Establezca extremos adicionales para la máquina virtual. Debido a que tendremos acceso a MongoDB en esta máquina virtual, agregue un extremo nuevo con la siguiente información:

    -   Nombre: MongoDB
    -   Protocolo: TCP
    -   Puerto público: 27017
    -   Puerto privado: 27017

    Para exponer el portal web de MongoDB, agregue otro extremo con la siguiente información:

    -   Nombre: MongoDBWeb
    -   Protocolo: TCP
    -   Puerto público: 28017
    -   Puerto privado: 28017

    Por último, seleccione la marca de verificación para configurar la máquina virtual.

    ![Captura de pantalla de la configuración del extremo](./media/web-sites-nodejs-store-data-mongodb/endpoints.png)

9.  Después de que el estado de la máquina virtual haya cambiado a **Running**, debería poder abrir un explorador web en **http://&lt;YourVMDNSName\>.cloudapp.net:28017/** para verificar que MongoDB está en ejecución. En la parte inferior de la página debe haber un registro que muestra información sobre el servicio, similar al siguiente:

         Fri Mar  7 18:57:16 [initandlisten] MongoDB starting : pid=1019 port=27017 dbpath=/var/lib/mongodb 64-bit host=localhost.localdomain
            18:57:16 [initandlisten] db version v2.2.3, pdfile version 4.5
            18:57:16 [initandlisten] git version: f570771a5d8a3846eb7586eaffcf4c2f4a96bf08
            18:57:16 [initandlisten] build info: Linux ip-10-2-29-40 2.6.21.7-2.ec2.v1.2.fc8xen #1 SMP Fri Nov 20 17:48:28 EST 2009 x86_64 BOOST_LIB_VERSION=1_49
            18:57:16 [initandlisten] options: { config: "/etc/mongodb.conf", dbpath: "/var/lib/mongodb", logappend: "true", logpath: "/var/log/mongodb/mongodb.log" }
            18:57:16 [initandlisten] journal dir=/var/lib/mongodb/journal
            18:57:16 [initandlisten] recover : no journal files present, no recovery needed
            18:57:17 [initandlisten] waiting for connections on port 27017

    Si el registro muestra errores, consulte la [documentación de MongoDB](http://docs.mongodb.org/manual/) para conocer los pasos para solucionar problemas.

Instalación de módulos y generación de scaffolding
--------------------------------------------------

En esta sección podrá crear una nueva aplicación Node en su entorno de desarrollo y usar npm para agregar paquetes de módulos. Para la aplicación de lista de tareas, usará los módulos [Express](http://expressjs.com) y [Mongoose](http://mongoosejs.com). El módulo Express proporciona un marco de controlador de vista de modelo para Node, mientras que Mongoose es un controlador para comunicarse con MongoDB.

### Instalación de Express y generación de scaffolding

1.  Desde la línea de comandos, cambie los directorios por el directorio **tasklist**. Si el directorio **tasklist** no existe, créelo.

    > [WACOM.NOTE] Este tutorial hace referencia a la carpeta **tasklist**. La ruta completa a esta carpeta se omite, debido a que la semántica de la ruta es diferente entre los sistemas operativos. Debe crear esta carpeta en una ubicación a la que le sea fácil tener acceso en su sistema de archivos local, como **\~/node/tasklist** o **c:\\node\\tasklist**

2.  Escriba el siguiente comando para instalar Express.

    npm install express -g

    > [WACOM.NOTE] Al usar el parámetro '-g' en algunos sistemas operativos, puede que reciba un error de ***Error: EPERM, chmod '/usr/local/bin/express'*** y una solicitud para intentar ejecutar la cuenta como administrador. Si esto ocurre, use el comando `sudo` para ejecutar npm en un nivel de privilegio más elevado.

    El resultado de este comando debe ser similar al siguiente:

         express@3.5.0 C:\Users\larryfr\AppData\Roaming\npm\node_modules\express                                        
         ├── methods@0.1.0                                                                                              
         ├── merge-descriptors@0.0.2                                                                                    
         ├── fresh@0.2.2                                                                                                
         ├── cookie-signature@1.0.3                                                                                     
         ├── range-parser@1.0.0                                                                                         
         ├── debug@0.7.4                                                                                                
         ├── buffer-crc32@0.2.1                                                                                         
         ├── cookie@0.1.1                                                                                               
         ├── mkdirp@0.3.5                                                                                               
         ├── commander@1.3.2 (keypress@0.1.0)                                                                           
         ├── send@0.2.0 (mime@1.2.11)                                                                                   
         ├── connect@2.14.1 (response-time@1.0.0, pause@0.0.1, connect-timeout@1.0.0, method-override@1.0.0, vhost@1.0.0, static-favicon@1.0.0, morgan@1.0.0, serve-static@1.0.2, basic-auth-connect@1.0.0, qs@0.6.6, bytes@0.2.1, raw-body@1.1.3, errorhandler@1.0.0, cookie-parser@1.0.1, express-session@1.0.2, compression@1.0.0, csurf@1.0.0, serve-index@1.0.1, multiparty@2.2.0)                                                                              

    > [WACOM.NOTE] El parámetro '-g' usado al instalar el módulo Express se instala de manera global. Esto se realiza para tener acceso al comando ***express*** para generar scaffolding del sitio web sin tener que escribir información de ruta adicional.

3.  Para crear el scaffolding que se usará para esta aplicación, use el comando **express**:

    express

    El resultado de este comando debe ser similar al siguiente:

            create : .
            create : ./package.json
            create : ./app.js
            create : ./public
            create : ./public/stylesheets
            create : ./public/stylesheets/style.css
            create : ./public/images
            create : ./public/javascripts
            create : ./routes
            create : ./routes/index.js
            create : ./routes/user.js
            create : ./views
            create : ./views/layout.jade
            create : ./views/index.jade

            install dependencies:
              $ cd . && npm install

            run the app:
              $ node app

    Después de que se completa este comando, debe tener varios directorios y archivos nuevos en el directorio **tasklist**.

### Instalar módulos adicionales

El archivo **package.json** es uno de los archivos que se crea con el comando **express**. Este archivo contiene una lista de módulos adicionales necesarios para una aplicación Express. Posteriormente, cuando implemente esta aplicación en un sitio web de Azure, se usará este archivo para determinar los módulos que se necesitan instalar en Azure para admitir su aplicación.

1.  En la carpeta **tasklist**, use el siguiente comando para instalar lo módulos que se describen en el archivo **package.json**:

         npm install

    El resultado de este comando debe ser similar al siguiente:

         express@3.5.0 node_modules\express                                                                            
         ├── methods@0.1.0                                                                                             
         ├── merge-descriptors@0.0.2                                                                                   
         ├── cookie-signature@1.0.3                                                                                    
         ├── fresh@0.2.2                                                                                               
         ├── debug@0.7.4                                                                                               
         ├── range-parser@1.0.0                                                                                        
         ├── buffer-crc32@0.2.1                                                                                        
         ├── cookie@0.1.1                                                                                              
         ├── mkdirp@0.3.5                                                                                              
         ├── commander@1.3.2 (keypress@0.1.0)                                                                          
         ├── send@0.2.0 (mime@1.2.11)                                                                                  
         ├── connect@2.14.1 (response-time@1.0.0, pause@0.0.1, connect-timeout@1.0.0, method-override@1.0.0, vhost@1.0., static-favicon@1.0.0, qs@0.6.6, morgan@1.0.0, basic-auth-connect@1.0.0, serve-static@1.0.2, bytes@0.2.1, rawbody@1.1.3, errorhandler@1.0.0, cookie-parser@1.0.1, csurf@1.0.0, compression@1.0.0, express-session@1.0.2, seve-index@1.0.1, multiparty@2.2.0)                                                                             
                                                                                                                  
         jade@1.3.0 node_modules\jade                                                                                  
         ├── character-parser@1.2.0                                                                                    
         ├── commander@2.1.0                                                                                           
         ├── mkdirp@0.3.5                                                                                              
         ├── monocle@1.1.51 (readdirp@0.2.5)                                                                           
         ├── transformers@2.1.0 (promise@2.0.0, css@1.0.8, uglify-js@2.2.5)                                            
         ├── with@3.0.0 (uglify-js@2.4.12)                                                                             
         ├── constantinople@2.0.0 (uglify-js@2.4.12)                                                                   

    De esta manera se instalan todos los módulos predeterminados usados por una aplicación Express.

2.  A continuación, escriba el siguiente comando para instalar el módulo Mongoose de manera local, además de guardar una entrada para este en el archivo **package.json**:

         npm install mongoose --save

    El resultado de este comando debe ser similar al siguiente:

         mongoose@3.8.8 node_modules\mongoose                     
         ├── regexp-clone@0.0.1                                   
         ├── muri@0.3.1                                           
         ├── sliced@0.0.5                                         
         ├── hooks@0.2.1                                          
         ├── mpath@0.1.1                                          
         ├── mpromise@0.4.3                                       
         ├── ms@0.1.0                                             
         ├── mquery@0.5.3 (debug@0.7.4)                           
         ├── mongodb@1.3.23 (kerberos@0.0.3, bson@0.2.5)          

    > [WACOM.NOTE] Puede ignorar de manera segura cualquier mensaje sobre la instalación del analizador bson de C++.

Uso de MongoDB en una aplicación Node
-------------------------------------

En esta sección extenderá la aplicación básica creada por el comando **express** agregando un archivo **task.js** que contiene el modelo para sus tareas. También podrá modificar el archivo **app.js** existente y crear un archivo controlador **tasklist.js** nuevo para usar el modelo.

### Crear el modelo

1.  En el directorio **tasklist**, cree el directorio nuevo con el nombre **models**.

2.  En el directorio **models**, cree un archivo nuevo con el nombre **models**. Este archivo contendrá el modelo para las tareas que se crean con su aplicación.

3.  Al comienzo del archivo **task.js**, agregue el siguiente código para hacer referencia a las bibliotecas requeridas:

         var mongoose = require('mongoose'),
             Schema = mongoose.Schema;

4.  A continuación, agregará código para definir y exportar el modelo. Este modelo se usará para realizar interacciones con la base de datos MongoDB.

         var TaskSchema = new Schema({
             itemName      : String,
             itemCategory  : String,
             itemCompleted : { type: Boolean, default: false },
             itemDate      : { type: Date, default: Date.now }
         });

         module.exports = mongoose.model('TaskModel', TaskSchema);

5.  Guarde y cierre el archivo **task.js**.

### Crear el controlador

1.  En el directorio **tasklist/routes**, cree un archivo nuevo con el nombre **tasklist.js** y ábralo en un editor de texto.

2.  Agregue el siguiente código a **tasklist.js**. De esta manera se carga el módulo mongoose y el modelo de tarea se define en **task.js**. La función TaskList se usa para crear la conexión con el servidor de MongoDB según el valor de **connection**:

         var mongoose = require('mongoose'),
             task = require('../models/task.js');

         module.exports = TaskList;

         function TaskList(connection) {
           mongoose.connect(connection);
         }

3.  Continúe agregando al archivo **tasklist.js** agregando los métodos usados para **showTasks**, **addTask** y **completeTasks**:

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

1.  Guarde el archivo **tasklist.js**.

### Modificar app.js

1.  En el directorio **tasklist**, abra el archivo **app.js** en un editor de texto. Este archivo se creó anteriormente al ejecutar el comando **express**.

2.  Agregue el siguiente código al comienzo del archivo **app.js**. Esto inicializará **TaskList** con la cadena de conexión para el servidor de MongoDB:

         var TaskList = require('./routes/tasklist');
         var taskList = new TaskList(process.env.MONGODB_URI);

    Observe la segunda línea; obtiene acceso a una variable de entorno que configurará más tarde, que contiene la información de conexión para su instancia de mongo. Si tiene una instancia de mongo local que se ejecuta para fines de desarrollo, es posible que desee establecer temporalmente este valor en "localhost" en vez de process.env.MONGODB\_URI.

3.  Busque las líneas que comienzan con `app.get` y reemplácelas por las siguientes líneas:

         app.get('/', taskList.showTasks.bind(taskList)); 
         app.post('/addtask', taskList.addTask.bind(taskList));
         app.post('/completetask', taskList.completeTask.bind(taskList));

    De esta manera se agregan las funciones que se definen en **tasklist.js** como rutas.

4.  Guarde el archivo **app.js**.

### Modificar la vista de índice

1.  Cambie los directorios por el directorio **views** y abra el archivo **index.jade** en un editor de texto.

2.  Reemplace el contenido del archivo **index.jade** por el código siguiente. De esta manera se define la vista para mostrar las tareas existentes, además de un formulario para agregar tareas nuevas y marcar las existentes como terminadas.

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

3.  Guarde y cierre el archivo **index.jade**.

Implementación de su aplicación en Azure
----------------------------------------

Los pasos de esta sección usan las herramientas de la línea de comandos de Azure para crear un sitio web de Azure nuevo y, posteriormente, usan Git para implementar su aplicación. Para realizar estos pasos debe tener una suscripción a Azure.

> [WACOM.NOTE] Estos pasos también pueden llevarse a cabo usando el portal de Azure. Para conocer los pasos para usar el portal de Azure y así implementar una aplicación Node.js, consulte [Creación e implementación de una aplicación Node.js en un Sitio web Azure](/en-us/develop/nodejs/tutorials/create-a-website-(mac)/).

> [WACOM.NOTE] Si este es el primer sitio web de Azure que crea, debe usar el portal de Azure para implementar esta aplicación.

### Instalar la interfaz de línea de comandos entre plataformas de Azure

La interfaz de línea de comandos entre plataformas de Azure (xplat-cli) le permite realizar operaciones de administración para los servicios de Azure. Si todavía no ha instalado ni configurado la xplat-cli en su entorno de desarrollo, consulte [Instalación y configuración de la interfaz de línea de comandos entre plataformas de Azure](/en-us/documentation/articles/xplat-cli/) para obtener instrucciones.

### Crear un sitio web de Azure

1.  Desde la línea de comandos, cambie los directorios por el directorio **tasklist**.

2.  Utilice el comando siguiente para crear un sitio web de Azure. Reemplace 'myuniquesitename' por un nombre de sitio único para su sitio web. Este valor se usa como parte de la dirección URL para el sitio web resultante.

         azure site create myuniquesitename --git

    Se le solicitará el centro de datos en el que se ubicará el sitio. Seleccione el centro de datos que se encuentra geográficamente cerca de su ubicación.

    El parámetro `--git` creará un repositorio de Git de manera local en la carpeta **tasklist** si no existe ninguno. Creará también un [Git remoto](http://git-scm.com/docs/git-remote) con el nombre 'azure', que se usará para publicar la aplicación en Azure. Creará un [iisnode.yml](https://github.com/WindowsAzure/iisnode/blob/master/src/samples/configuration/iisnode.yml), que contiene la configuración usada por Azure para hospedar aplicaciones Node. Finalmente, creará también un archivo .gitignore para excluir la carpeta de módulos de Node para que se publique en .git.

    > [WACOM.NOTE] Si este comando se ejecutó desde un directorio que ya contiene un repositorio de Git, no reinicializará el repositorio.

    > [WACOM.NOTE] Si se omite el parámetro '--git', aunque el directorio contenga un repositorio de Git se creará el 'azure' remoto.

    Después de que este comando se haya completado, verá un resultado similar al siguiente. Observe que la línea que comienza con **Created web site at** contiene la dirección URL del sitio web.

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

    > [WACOM.NOTE\> Si este es el primer sitio web de Azure de su subscripción, se le pedirá que use el portal para crear el sitio web. Para obtener más información, consulte [Creación e implementación de una aplicación Node.js en sitios web de Azure](/en-us/develop/nodejs/tutorials/create-a-website-(mac)/).

### Establecer la variable de entorno MONGODB\_URL

La aplicación espera la cadena de conexión para que la instancia de MongoDB esté disponible en la variable de entorno de MONGODB\_URI. Para establecer este valor para el sitio web, use el siguiente comando:

    azure site config add MONGODB_URI=mongodb://mymongodb.cloudapp.net/tasks

Esto crea una nueva configuración de aplicación para el sitio web, que se usará para completar la variable de entorno de MONGODB\_URI leída por el sitio web. Reemplace el valor de 'mymongodb.cloudapp.net' por el nombre de la máquina virtual donde se instaló MongoDB.

### Publicación de la aplicación

1.  En la ventana Terminal, cambie los directorios por el directorio **tasklist** si es que ya no se encuentra en ese lugar.

2.  Use los siguientes comandos para agregar y luego confirmar los archivos en su repositorio local de Git:

         git add .
         git commit -m "adding files"

3.  Al realizar los últimos cambios en el repositorio de Git en el sitio web de Azure, debe especificar que la rama objetivo es **master** debido a que esta se usa para el contenido del sitio web.

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
          * [new branch] master -\> master

4.  Después de que se haya completado la operación de inserción, diríjase al sitio web usando el comando `azure site browse` para ver su aplicación.

Pasos siguientes
----------------

Si bien los pasos de este artículo describen el uso de MongoDB para almacenar información, puede también usar el servicio de tablas de Azure. Consulte [Aplicación web Node.js con el servicio de tablas de Azure](/en-us/develop/nodejs/tutorials/web-site-with-storage/) para obtener más información.

Para obtener información sobre el uso de una instancia hospedada de MongoDB proporcionada por MongoLab, consulte [Creación de una aplicación Node.js en Azure con MongoDB y el complemento de MongoLab](/en-us/develop/nodejs/tutorials/website-with-mongodb-mongolab/).

Para obtener información sobre la protección de MongoDB, consulte [MongoDB Security](http://docs.mongodb.org/manual/security/).

Recursos adicionales
--------------------

[Herramienta de línea de comandos de Azure para Mac y Linux](/en-us/develop/nodejs/tutorials/create-a-website-(mac)/)
 
[Publicación en sitios web de Azure con Git](/en-us/develop/nodejs/common-tasks/publishing-with-git/)

