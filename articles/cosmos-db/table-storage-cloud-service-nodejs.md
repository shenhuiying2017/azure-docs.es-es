---
title: "Azure Table Storage: compilación de una aplicación web con Node.js | Microsoft Docs"
description: "Un tutorial que se agrega a la aplicación web con el tutorial Express añadiendo servicios Azure Storage y el módulo de Azure."
services: cosmos-db
documentationcenter: nodejs
author: mimig1
manager: jhubbard
editor: tysonn
ms.assetid: e90959a2-4cb2-4b19-9bfb-aede15b18b1c
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: nodejs
ms.topic: article
ms.date: 11/03/2017
ms.author: mimig
ms.openlocfilehash: 9acd197c26e6365e396fd8f6321d764bba7bbb6c
ms.sourcegitcommit: f1c1789f2f2502d683afaf5a2f46cc548c0dea50
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/18/2018
---
# <a name="azure-table-storage-nodejs-web-application"></a>Azure Table Storage: compilación de una aplicación web con Node.js
[!INCLUDE [storage-table-cosmos-db-tip-include](../../includes/storage-table-cosmos-db-tip-include.md)]

## <a name="overview"></a>Información general
En este tutorial, la aplicación que creó en el tutorial [Aplicación web Node.js con Express] se amplía mediante el uso de las bibliotecas Microsoft Azure Client para Node.js a fin de trabajar con los servicios de administración de datos. Va a ampliar su aplicación creando una aplicación de lista de tareas basada en web que se puede implementar en Azure. La lista de tareas permite al usuario recuperar tareas, agregar tareas nuevas y marcar tareas como completadas.

Los elementos de tarea se almacenan en Azure Storage. Azure Storage ofrece almacenamiento de datos no estructurados que es tolerante a errores y tiene una alta disponibilidad. Azure Storage incluye varias estructuras de datos donde puede almacenar y tener acceso a datos. Puede usar los servicios de almacenamiento de las API que se incluyen en el SDK de Azure para Node.js o a través de las API de REST. Para más información, consulte [Almacenamiento de Azure].

En este tutorial se supone que ha completado los tutoriales de [Aplicación web Node.js] y [Node.js con Express][Aplicación web Node.js con Express].

Contiene la siguiente información:

* Trabajar con el motor de plantillas Jade
* Trabajar con los servicios de administración de datos de Azure

La siguiente captura de pantalla muestra la aplicación completada:

![La página web completa en Internet Explorer](./media/table-storage-cloud-service-nodejs/getting-started-1.png)

## <a name="setting-storage-credentials-in-webconfig"></a>Establecimiento de las credenciales de almacenamiento en Web.Config
Debe proporcionar credenciales de almacenamiento para tener acceso a Azure Storage. Para ello, use la configuración de la aplicación de web.config.
Las opciones de configuración de web.config se pasan como variables de entorno a Node, que luego son leídas por el SDK de Azure.

> [!NOTE]
> Las credenciales de almacenamiento solo se utilizan cuando la aplicación se implementa en Azure. Cuando se ejecuta en el emulador, la aplicación usa el emulador de almacenamiento.
>
>

Siga estos pasos para recuperar las credenciales de la cuenta de almacenamiento y agregarlas a la configuración de web.config:

1. Si aún no está abierto, inicie Azure PowerShell desde el menú **Inicio**, expanda **Todos los programas, Azure**, haga clic con el botón derecho en **Azure PowerShell** y seleccione **Ejecutar como administrador**.
2. Cambie los directorios a la carpeta que contiene la aplicación. Por ejemplo, C:\\nodo\\tasklist\\WebRole1.
3. Desde la ventana de Azure Powershell, escriba el siguiente cmdlet para recuperar la información de la cuenta de almacenamiento:

    ```powershell
    PS C:\node\tasklist\WebRole1> Get-AzureStorageAccounts
    ```

   El cmdlet anterior recupera la lista de cuentas de almacenamiento y las claves de cuentas asociadas con el servicio hospedado.

   > [!NOTE]
   > Debido a que el SDK de Azure crea una cuenta de almacenamiento al implementar un servicio, ya debe existir una cuenta de almacenamiento procedente de la implementación de la aplicación en las guías anteriores.
   >
   >
4. Abra el archivo **ServiceDefinition.csdef** que contiene la configuración del entorno que se usa cuando la aplicación se implementa en Azure:

    ```powershell
    PS C:\node\tasklist> notepad ServiceDefinition.csdef
    ```

5. Inserte el siguiente bloque bajo el elemento **Environment**, reemplace {STORAGE ACCOUNT} y {STORAGE ACCESS KEY} por el nombre de la cuenta y la clave principal de la cuenta de almacenamiento que desea utilizar para la implementación:

  <Variable name="AZURE_STORAGE_ACCOUNT" value="{STORAGE ACCOUNT}" />
  <Variable name="AZURE_STORAGE_ACCESS_KEY" value="{STORAGE ACCESS KEY}" />

   ![Contenido del archivo web.cloud.config](./media/table-storage-cloud-service-nodejs/node37.png)

6. Guarde el archivo y cierre el Bloc de notas.

### <a name="install-additional-modules"></a>Instalar módulos adicionales
1. Use el comando siguiente para instalar los módulos [azure], [node-uuid], [nconf] y [async] localmente y para guardar una entrada de ellos en el archivo **package.json**:

  ```powershell
  PS C:\node\tasklist\WebRole1> npm install azure-storage node-uuid async nconf --save
  ```

  El resultado de este comando debe ser similar al siguiente:

  ```
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
  ```

## <a name="using-the-table-service-in-a-node-application"></a>Uso de Table service en una aplicación Node
En esta sección, la aplicación básica creada por el comando **express** se amplía agregando un archivo **task.js** que contiene el modelo para sus tareas. Modifique el archivo **app.js** existente y cree un archivo **tasklist.js** nuevo que utilice el modelo.

### <a name="create-the-model"></a>Crear el modelo
1. En el directorio **WebRole1**, cree el directorio nuevo con el nombre **models**.
2. En el directorio **models**, cree un archivo nuevo con el nombre **task.js**. Este archivo contiene el modelo para las tareas que se crean con su aplicación.
3. Al comienzo del archivo **task.js** , agregue el siguiente código para hacer referencia a las bibliotecas requeridas:

    ```nodejs
    var azure = require('azure-storage');
    var uuid = require('node-uuid');
    var entityGen = azure.TableUtilities.entityGenerator;
    ```

4. A continuación, agregue código para definir y exportar el objeto Task. Este objeto es el responsable de la conexión a la tabla.

    ```nodejs
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
    ```

5. A continuación, agregue el siguiente código para definir métodos adicionales en el objeto Task, que permite las interacciones con los datos almacenados en la tabla:

    ```nodejs
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
    ```

6. Guarde y cierre el archivo **task.js** .

### <a name="create-the-controller"></a>Crear el controlador
1. En el directorio **WebRole1/routes**, cree un archivo nuevo con el nombre **tasklist.js** y ábralo en un editor de texto.
2. Agregue el siguiente código a **tasklist.js**. Este código carga los módulos azure y async, que son usados por **tasklist.js**, y define la función **TaskList**, que se pasa a una instancia del objeto **Task** que definimos anteriormente:

    ```nodejs
    var azure = require('azure-storage');
    var async = require('async');

    module.exports = TaskList;

    function TaskList(task) {
      this.task = task;
    }
    ```

3. Continúe agregando al archivo **tasklist.js** los métodos usados para **showTasks**, **addTask** y **completeTasks**:

    ```nodejs
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
    ```

4. Guarde el archivo **tasklist.js**.

### <a name="modify-appjs"></a>Modificar app.js
1. En el directorio **WebRole1**, abra el archivo **app.js** en el editor de texto.
2. Al principio del archivo, agregue lo siguiente para cargar el módulo de Azure y establecer el nombre de tabla y la clave de partición:

    ```nodejs
    var azure = require('azure-storage');
    var tableName = 'tasks';
    var partitionKey = 'hometasks';
    ```

3. En el archivo app.js, desplácese hacia abajo hasta ver la siguiente línea:

    ```nodejs
    app.use('/', routes);
    app.use('/users', users);
    ```

    Reemplace las líneas anteriores por el código siguiente: Este código inicializa una instancia de <strong>Task</strong> con una conexión a su cuenta de almacenamiento. <strong>Task</strong> se pasa a <strong>TaskList</strong>, que lo usa para comunicarse con Table service:

    ```nodejs
    var TaskList = require('./routes/tasklist');
    var Task = require('./models/task');
    var task = new Task(azure.createTableService(), tableName, partitionKey);
    var taskList = new TaskList(task);

    app.get('/', taskList.showTasks.bind(taskList));
    app.post('/addtask', taskList.addTask.bind(taskList));
    app.post('/completetask', taskList.completeTask.bind(taskList));
    ```

4. Guarde el archivo **app.js** .

### <a name="modify-the-index-view"></a>Modificar la vista de índice
1. Cambie al directorio **views** y abra el archivo **index.jade** en un editor de texto.
2. Reemplace el contenido del archivo **index.jade** por el código siguiente. Este código define la vista para mostrar las tareas existentes, además de un formulario para agregar tareas nuevas y marcar las existentes como completadas.

    ```
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
    ```

3. Guarde y cierre el archivo **index.jade** .

### <a name="modify-the-global-layout"></a>Modificar el diseño global
El archivo **layout.jade** del directorio **views** se utiliza como plantilla global para otros archivos **.jade**. En este paso, modifica el archivo **layout.jade** para usar [Twitter Bootstrap](https://github.com/twbs/bootstrap), un kit de herramientas que facilita el diseño de un sitio web atractivo.

1. Descargue y extraiga los archivos para [Twitter Bootstrap](http://getbootstrap.com/). Copie el archivo **bootstrap.min.css** desde la carpeta **bootstrap\\dist\\css** en el directorio **public\\stylesheets** de su aplicación de lista de tareas.
2. En la carpeta **views**, abra el archivo **layout.jade** en el editor de texto y reemplace el contenido por lo siguiente:

    doctype html  html    head      title= title      link(rel='stylesheet', href='/stylesheets/bootstrap.min.css')      link(rel='stylesheet', href='/stylesheets/style.css')    body.app      nav.navbar.navbar-default        div.navbar-header          a.navbar-brand(href='/') My Tasks      block content

3. Guarde el archivo **layout.jade**.

### <a name="running-the-application-in-the-emulator"></a>Ejecución de la aplicación en el emulador
Use el siguiente comando para iniciar la aplicación en el emulador.

```powershell
PS C:\node\tasklist\WebRole1> start-azureemulator -launch
```

El explorador se abre y muestra la siguiente página:

![Una página web con el título My Task List con una tabla que contiene las tareas y los campos para agregar una nueva tarea.](./media/table-storage-cloud-service-nodejs/node44.png)

Use el formulario para agregar elementos o quitar los elementos existentes marcándolos como completados.

## <a name="publishing-the-application-to-azure"></a>Publicación de la aplicación en Azure
En la ventana de Windows PowerShell, llame al siguiente cmdlet para volver a implementar el servicio hospedado en Azure.

```powershell
PS C:\node\tasklist\WebRole1> Publish-AzureServiceProject -name myuniquename -location datacentername -launch
```

Reemplace **myuniquename** por un nombre único para esta aplicación. Reemplace **datacentername** por el nombre de un centro de datos de Azure, como por ejemplo **Oeste de EE. UU.**

Después de que la implementación se haya completado, debe ver una respuesta similar a la siguiente:

```
  PS C:\node\tasklist> publish-azureserviceproject -servicename tasklist -location "West US"
  WARNING: Publishing tasklist to Microsoft Azure. This may take several minutes...
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
```

Al especificar la opción **-launch** en el cmdlet anterior, el explorador se abre y muestra la aplicación que se ejecuta en Azure cuando se completa la publicación.

![Una ventana del explorador muestra la página My Task List. La URL indica que la página está hospedada en Azure.](./media/table-storage-cloud-service-nodejs/getting-started-1.png)

## <a name="stopping-and-deleting-your-application"></a>Detención y eliminación de su aplicación
Después de implementar la aplicación, es posible que desee deshabilitarla a fin de evitar costes o compilar e implementar otras aplicaciones dentro del período de prueba gratuito.

Azure factura las instancias de rol web por hora consumida de tiempo de servidor.
El tiempo de servidor se empieza a consumir una vez implementada su aplicación, incluso si las instancias no se están ejecutando y se encuentran detenidas.

Los siguientes pasos muestran cómo detener y eliminar su aplicación.

1. En la ventana de Windows PowerShell, detenga la implementación del servicio creado en la sección anterior con el siguiente cmdlet:

    ```powershell
    PS C:\node\tasklist\WebRole1> Stop-AzureService
    ```

   La detención del servicio puede durar varios minutos. Una vez detenido el servicio, recibirá un mensaje que le avisará de su detención.

2. Para eliminar el servicio, llame al siguiente cmdlet:

    ```powershell
    PS C:\node\tasklist\WebRole1> Remove-AzureService contosotasklist
    ```

   Cuando se le solicite, escriba **Y** para eliminar el servicio.

   La eliminación del servicio puede durar varios minutos. Una vez eliminado el servicio, recibirá un mensaje que le avisará de su eliminación.

[Aplicación web Node.js con Express]: http://azure.microsoft.com/develop/nodejs/tutorials/web-app-with-express/
[Almacenamiento de Azure]: http://msdn.microsoft.com/library/azure/gg433040.aspx
[Aplicación web Node.js]: http://azure.microsoft.com/develop/nodejs/tutorials/getting-started/


