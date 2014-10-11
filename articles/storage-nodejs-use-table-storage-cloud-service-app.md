<properties linkid="dev-nodejs-basic-web-app-with-storage" urlDisplayName="Web App with Storage" pageTitle="Web app with table storage (Node.js) | Microsoft Azure" metaKeywords="Azure Node.js hello world tutorial, Azure Node.js hello world, Azure Node.js Getting Started tutorial, Azure Node.js tutorial, Azure Node.js Express tutorial" description="A tutorial that builds on the Web App with Express tutorial by adding Azure Storage services and the Azure module." metaCanonical="" services="cloud-services,storage" documentationCenter="Node.js" title="Node.js Web Application using Storage" authors="larryfr" solutions="" manager="" editor="" />

<tags ms.service="storage" ms.workload="storage" ms.tgt_pltfrm="na" ms.devlang="nodejs" ms.topic="article" ms.date="01/01/1900" ms.author="larryfr"/>

# Aplicación web Node.js con almacenamiento

En este tutorial, podrá ampliar la aplicación creada en el tutorial
[Node.js Web Application using Express][] mediante el uso de las bibliotecas de cliente de Windows
Azure para Node.js a fin de trabajar con los servicios de administración de datos. Va a
ampliar su aplicación para crear una aplicación de lista de tareas basada en web
que se puede implementar en Azure. La lista de tareas permite al usuario
recuperar tareas, agregar tareas nuevas y marcar tareas como completadas.

Los elementos de tarea se almacenan en el almacenamiento de Azure. El Almacenamiento
de Azure ofrece almacenamiento de datos no estructurados que es tolerante a errores y
tiene una alta disponibilidad. El almacenamiento de Azure incluye varias estructuras
de datos donde puede almacenar datos y tener acceso a ellos, además de aprovechar los
servicios de almacenamiento de las API que se incluyen en el SDK de Azure para Node.js o
mediante las API de REST. Para obtener más información, consulte [Almacenamiento de datos y acceso a los mismos en Azure][].

En este tutorial se supone que ha completado los tutoriales de [Aplicación web
Node.js][] y [Node.js con Express][Node.js Web Application using Express].

Aprenderá a:

-   Trabajar con el motor de plantillas Jade
-   Trabajar con los servicios de administración de datos de Azure

A continuación se muestra una captura de pantalla de la aplicación completada:

![La página web completa en Internet Explorer][]

## Establecimiento de las credenciales de almacenamiento en Web.Config

Para tener acceso al almacenamiento de Azure, necesita proporcionar
credenciales de almacenamiento. Para ello, utiliza la configuración de aplicación web.config.
Estos ajustes pasarán como variables de entorno a Node, que luego
son leídos por el SDK de Azure.

<div class="dev-callout">
<strong>Nota:</strong>
<p>Las credenciales de almacenamiento solo se utilizan cuando la aplicaci&oacute;n
se implementa en Azure. Cuando se ejecuta en el emulador, la aplicaci&oacute;n
utilizar&aacute; el emulador de almacenamiento.</p>
</div>

Siga estos pasos para recuperar las credenciales de la cuenta de almacenamiento
y agregarlas a la configuración de web.config:

1.  Si no está abierto, inicie el Azure PowerShell desde el menú **Inicio** mediante la ampliación de **Todos los programas, Azure**, haga clic con el botón secundario en **Azure PowerShell** y, a continuación, seleccione **Ejecutar como administrador**.

2.  Cambie los directorios a la carpeta que contiene la aplicación. Por ejemplo, C:\\node\\tasklist\\WebRole1.

3.  Desde la ventana de Azure Powershell escriba el siguiente cmdlet para recuperar la información de la cuenta de almacenamiento:

        PS C:\node\tasklist\WebRole1> Get-AzureStorageAccounts

    De esta manera se recupera la lista de cuentas de almacenamiento y las claves de cuentas con el servicio hospedado.

    <div class="dev-callout">
<strong>Nota:</strong>
	<p>Debido a que el SDK de Azure crea una cuenta de almacenamiento al implementar un servicio, ya debe existir una cuenta de almacenamiento procedente de la implementaci&oacute;n de la aplicaci&oacute;n en las gu&iacute;as anteriores.</p>
</div>

4.  Abra el archivo **ServiceDefinition.csdef** que contiene la configuración del entorno que se usa cuando la aplicación se implementa en Azure:

        PS C:\node\tasklist> notepad ServiceDefinition.csdef

5.  Inserte el siguiente bloque bajo el elemento **Environment**, reemplace {STORAGE ACCOUNT} y {STORAGE ACCESS KEY} por el nombre de la cuenta y la clave principal de la cuenta de almacenamiento que desea utilizar para la implementación:

        <Variable name="AZURE_STORAGE_ACCOUNT" value="{STORAGE ACCOUNT}" />
        <Variable name="AZURE_STORAGE_ACCESS_KEY" value="{STORAGE ACCESS KEY}" />

    ![Contenido del archivo web.cloud.config][]

6.  Guarde el archivo y cierre el Bloc de notas.

### Instalar módulos adicionales

1.  Use el siguiente comando para instalar los módulos [azure], [node-uuid], [nconf] and [async] de manera local, además de guardar una entrada para estos para el archivo **package.json**:

        PS C:\node\tasklist\WebRole1> npm install azure-storage node-uuid async nconf --save

    El resultado de este comando debe ser similar al siguiente:

        node-uuid@1.4.1 node_modules\node-uuid

        nconf@0.6.9 node_modules\nconf
        ├── ini@1.1.0
        ├── async@0.2.9
        └── optimist@0.6.0 (wordwrap@0.0.2, minimist@0.0.8)

        azure-storage@0.1.0 node_modules\azure-storage
        ├── extend@1.2.1
        ├── xmlbuilder@0.4.3
        ├── mime@1.2.11
        ├── underscore@1.4.4
        ├── validator@3.1.0
        ├── node-uuid@1.4.1
        ├── xml2js@0.2.7 (sax@0.5.2)
        └── request@2.27.0 (json-stringify-safe@5.0.0, tunnel-agent@0.3.0, aws-sign@0.3.0, forever-agent@0.5.2, qs@0.6.6, oauth-sign@0.3.0, cookie-jar@0.3.0, hawk@1.0.0, form-data@0.1.3, http-signature@0.10.0)

## Uso del servicio Tabla en una aplicación Node

En esta sección extenderá la aplicación básica creada por el comando **express** agregando un archivo **task.js** que contiene el modelo para sus tareas. También podrá modificar el archivo **app.js** existente y crear un archivo **tasklist.js** nuevo que utilice el modelo.

### Crear el modelo

1.  En el directorio **WebRole1**, cree el directorio nuevo con el nombre **models**.

2.  En el directorio **models**, cree un archivo nuevo con el nombre **models**. Este archivo contendrá el modelo para las tareas que se crean con su aplicación.

3.  Al comienzo del archivo **task.js**, agregue el siguiente código para hacer referencia a las bibliotecas requeridas:

        var azure = require('azure-storage');
        var uuid = require('node-uuid');
        var entityGen = azure.TableUtilities.entityGenerator;

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

5.  A continuación, agregue el siguiente código para definir métodos adicionales en el objeto Task, que permite las interacciones con los datos almacenados en la tabla:

        Task.prototype = {
          find: function(query, callback) {
            self = this;
            self.storageClient.queryEntities(query, function entitiesQueried(error, result) {
              if(error) {
                callback(error);
              } else {
                callback(null, result.entries);
              }
            });
          },

          addItem: function(item, callback) {
            self = this;
            // use entityGenerator to set types
            // NOTE: RowKey must be a string type, even though
            // it contains a GUID in this example.
            var itemDescriptor = {
              PartitionKey: entityGen.String(self.partitionKey),
              RowKey: entityGen.String(uuid()),
              name: entityGen.String(item.name),
              category: entityGen.String(item.category),
              completed: entityGen.Boolean(false)
            };

            self.storageClient.insertEntity(self.tableName, itemDescriptor, function entityInserted(error) {
              if(error){  
                callback(error);
              }
              callback(null);
            });
          },

          updateItem: function(rKey, callback) {
            self = this;
            self.storageClient.retrieveEntity(self.tableName, self.partitionKey, rKey, function entityQueried(error, entity) {
              if(error) {
                callback(error);
              }
              entity.completed._ = true;
              self.storageClient.updateEntity(self.tableName, entity, function entityUpdated(error) {
                if(error) {
                  callback(error);
                }
                callback(null);
              });
            });
          }
        }

6.  Guarde y cierre el archivo **task.js**.

### Crear el controlador

1.  En el directorio **WebRole1/routes**, cree un archivo nuevo con el nombre **tasklist.js** y ábralo en un editor de texto.

2.  Agregue el siguiente código a **tasklist.js**. De este modo se cargan los módulos azure y async, que utiliza **tasklist.js**. También define la función **TaskList**, que pasa a una instancia del objeto **Task** que definimos anteriormente:

        var azure = require('azure-storage');
        var async = require('async');

        module.exports = TaskList;

        function TaskList(task) {
          this.task = task;
        }

3.  Continúe agregando al archivo **tasklist.js** agregando los métodos usados para **showTasks**, **addTask** y **completeTasks**:

        TaskList.prototype = {
          showTasks: function(req, res) {
            self = this;
            var query = azure.TableQuery()
              .where('completed eq ?', false);
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

1.  En el directorio **WebRole1**, abra el archivo **app.js** en un editor de texto.

2.  Al principio del archivo, agregue lo siguiente para cargar el módulo de Azure y establecer el nombre de tabla y la clave de partición:

        var azure = require('azure-storage');
        var tableName = 'tasks';
        var partitionKey = 'hometasks';

3.  En el archivo app.js, desplácese hacia abajo hasta ver la siguiente línea:

        app.use('/', routes);
        app.use('/users', users);

    Sustituya las líneas anteriores por el código que se muestra a continuación. De este modo se inicializará una instancia de **Task** con una conexión a su cuenta de almacenamiento. Esto se pasa a la **TaskList**, que lo utilizará para comunicarse con el servicio Tabla:

        var TaskList = require('./routes/tasklist');
        var Task = require('./models/task');
        var task = new Task(azure.createTableService(), tableName, partitionKey);
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
              if tasks != []
                tr
                  td 
              else
                each task in tasks
                  tr
                    td #{task.name._}
                    td #{task.category._}
                    - var day   = task.Timestamp._.getDate();
                    - var month = task.Timestamp._.getMonth() + 1;
                    - var year  = task.Timestamp._.getFullYear();
                    td #{month + "/" + day + "/" + year}
                    td
                      input(type="checkbox", name="#{task.RowKey._}", value="#{!task.completed._}", checked=task.completed._)
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

El archivo **layout.jade** en el directorio **views** se utiliza como plantilla global para otros archivos **.jade**. En este paso podrá modificarlo para utilizar [Twitter Bootstrap][], un kit de herramientas que facilita el diseño de un sitio web atractivo.

1.  Descargue y extraiga los archivos para [Twitter Bootstrap][1]. Copie el archivo **bootstrap.min.css** desde la carpeta **bootstrap\\dist\\css** al directorio **public\\stylesheets** de su aplicación de lista de tareas.

2.  En la carpeta **views**, abra **layout.jade** en el editor de texto y reemplace el contenido por lo siguiente:

        doctype html
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

### Ejecución de la aplicación en el emulador

Use el siguiente comando para iniciar la aplicación en el emulador.

    PS C:\node\tasklist\WebRole1> start-azureemulator -launch

El explorador se abrirá y mostrará la siguiente página:

![Una página web con el título My Task List con una tabla que contiene las tareas y los campos para agregar una nueva tarea.][]

Use el formulario para agregar elementos o quitar los elementos existentes marcándolos como completados.

## Publicación de la aplicación en Azure

En la ventana de Windows PowerShell, llame al siguiente cmdlet para volver a implementar el servicio hospedado en Azure.

    PS C:\node\tasklist\WebRole1> Publish-AzureServiceProject -name myuniquename -location datacentername -launch

Reemplace **myuniquename** por un nombre único para esta aplicación. Reemplace **datacentername** por el nombre de un centro de datos de Azure, como por ejemplo **Oeste de EE. UU.**.

Después de que la implementación se haya completado, debe ver una respuesta similar a la siguiente:

    PS C:\node\tasklist> publish-azureserviceproject -servicename tasklist -location "West US"
    WARNING: Publishing tasklist to Windows Azure. This may take several minutes...
    WARNING: 2:18:42 PM - Preparing runtime deployment for service 'tasklist'
    WARNING: 2:18:42 PM - Verifying storage account 'tasklist'...
    WARNING: 2:18:43 PM - Preparing deployment for tasklist with Subscription ID: 65a1016d-0f67-45d2-b838-b8f373d6d52e...
    WARNING: 2:19:01 PM - Connecting...
    WARNING: 2:19:02 PM - Uploading Package to storage service larrystore...
    WARNING: 2:19:40 PM - Upgrading...
    WARNING: 2:22:48 PM - Created Deployment ID: b7134ab29b1249ff84ada2bd157f296a.
    WARNING: 2:22:48 PM - Initializing...
    WARNING: 2:22:49 PM - Instance WebRole1_IN_0 of role WebRole1 is ready.
    WARNING: 2:22:50 PM - Created Website URL: http://tasklist.cloudapp.net/.

Al igual que antes, debido a que ha especificado la opción **-launch**, el explorador se abre y muestra la aplicación que se ejecuta en Azure cuando se completa la publicación.

![A browser window displaying the My Task List page. The URL indicates the page is now being hosted on Azure.][La página web completa en Internet Explorer]

## Detención y eliminación de su aplicación

Después de implementar la aplicación, es posible que desee deshabilitarla a fin de
evitar costes o compilar e implementar otras aplicaciones dentro del
período de prueba gratuito.

Azure factura las instancias de rol web por hora de tiempo de servidor utilizado
, y el tiempo de servidor se consume mientras la aplicación se implementa, aunque las instancias
no se estén ejecutando y estén detenidas.

Los siguientes pasos muestran cómo detener y eliminar su aplicación.

1.  En la ventana de Windows PowerShell, detenga la implementación
    del servicio creado en la sección anterior con el siguiente cmdlet:

        PS C:\node\tasklist\WebRole1> Stop-AzureService

    La detención del servicio puede durar varios minutos. Una vez detenido el servicio, recibirá un mensaje que le avisará de su detención.

2.  Para eliminar el servicio, llame al siguiente cmdlet:

        PS C:\node\tasklist\WebRole1> Remove-AzureService contosotasklist

    Cuando se le solicite, escriba **Y** para eliminar el servicio.

    La eliminación del servicio puede durar varios minutos. Una vez eliminado el servicio, recibirá un mensaje que le avisará de su eliminación.

  [Node.js Web Application using Express]: http://www.windowsazure.com/en-us/develop/nodejs/tutorials/web-app-with-express/
  [Almacenamiento de datos y acceso a los mismos en Azure]: http://msdn.microsoft.com/en-us/library/windowsazure/gg433040.aspx
  [Aplicación web
  Node.js]: http://www.windowsazure.com/en-us/develop/nodejs/tutorials/getting-started/
  [La página web completa en Internet Explorer]: ./media/storage-nodejs-use-table-storage-cloud-service-app/getting-started-1.png
  [Contenido del archivo web.cloud.config]: ./media/storage-nodejs-use-table-storage-cloud-service-app/node37.png
  [Twitter Bootstrap]: https://github.com/twbs/bootstrap
  [1]: http://getbootstrap.com/
  [Una página web con el título My Task List con una tabla que contiene las tareas y los campos para agregar una nueva tarea.]: ./media/storage-nodejs-use-table-storage-cloud-service-app/node44.png
