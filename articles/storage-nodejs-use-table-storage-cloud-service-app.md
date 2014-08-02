<properties linkid="dev-nodejs-basic-web-app-with-storage" urlDisplayName="Web App with Storage" pageTitle="Web app with table storage (Node.js) | Microsoft Azure" metaKeywords="Azure Node.js hello world tutorial, Azure Node.js hello world, Azure Node.js Getting Started tutorial, Azure Node.js tutorial, Azure Node.js Express tutorial" description="A tutorial that builds on the Web App with Express tutorial by adding Azure Storage services and the Azure module." metaCanonical="" services="cloud-services,storage" documentationCenter="Node.js" title="Node.js Web Application using Storage" authors="" solutions="" manager="" editor="" />

Aplicación web Node.js con almacenamiento
=========================================

En este tutorial, podrá ampliar la aplicación creada en el tutorial [Aplicación web Node.js con Express](http://www.windowsazure.com/en-us/develop/nodejs/tutorials/web-app-with-express/) mediante el uso de las bibliotecas de cliente de Azure para Node.js a fin de trabajar con los servicios de administración de datos. Va a ampliar su aplicación para crear una aplicación de lista de tareas basada en web que se puede implementar en Azure. La lista de tareas permite al usuario recuperar tareas, agregar tareas nuevas y marcar tareas como completadas.

Los elementos de tarea se almacenan en el almacenamiento de Azure. El almacenamiento de Azure ofrece almacenamiento de datos no estructurados que es tolerante a errores y tiene una alta disponibilidad. El almacenamiento de Azure incluye varias estructuras de datos donde puede almacenar datos y tener acceso a ellos, además de aprovechar los servicios de almacenamiento de las API que se incluyen en el SDK de Azure para Node.js o mediante las API de REST. Para obtener más información, consulte [Almacenamiento de datos y acceso a los mismos en Azure](http://msdn.microsoft.com/en-us/library/windowsazure/gg433040.aspx).

En este tutorial se supone que ha completado los tutoriales de [Aplicación web Node.js](http://www.windowsazure.com/en-us/develop/nodejs/tutorials/getting-started/) y [Node.js con Express](http://www.windowsazure.com/en-us/develop/nodejs/tutorials/web-app-with-express/).

Aprenderá a:

-   Trabajar con el motor de plantillas Jade
-   Trabajar con los servicios de administración de datos de Azure

Captura de pantalla de la aplicación completa:

![La página web completa en Internet Explorer](./media/storage-nodejs-use-table-storage-cloud-service-app/getting-started-1.png)

Establecimiento de las credenciales de almacenamiento en Web.Config
-------------------------------------------------------------------

Para tener acceso al almacenamiento de Azure, necesita proporcionar credenciales de almacenamiento. Para ello, utiliza la configuración de aplicación web.config. Estos ajustes pasarán como variables de entorno a Node, que luego son leídos por el SDK de Azure.

**Nota:**

Las credenciales de almacenamiento solo se utilizan cuando la aplicación se implementa en Azure. Cuando se ejecuta en el emulador, la aplicación utilizará el emulador de almacenamiento.

Siga estos pasos para recuperar las credenciales de la cuenta de almacenamiento y agregarlas a la configuración de web.config:

1.  Si no está abierto, inicie el Azure PowerShell desde el menú **Inicio** mediante la ampliación de **Todos los programas, Azure**, haga clic con el botón secundario en **Azure PowerShell** y, a continuación, seleccione **Ejecutar como administrador**.

2.  Cambie los directorios a la carpeta que contiene la aplicación. Por ejemplo, C:\\node\\tasklist\\WebRole1.

3.  Desde la ventana de Azure Powershell escriba el siguiente cmdlet para recuperar la información de la cuenta de almacenamiento:

        PS C:\node\tasklist\WebRole1> Get-AzureStorageAccounts

    De esta manera se recupera la lista de cuentas de almacenamiento y las claves de cuentas con el servicio hospedado.

    **Nota:**

    Debido a que el SDK de Azure crea una cuenta de almacenamiento al implementar un servicio, ya debe existir una cuenta de almacenamiento procedente de la implementación de la aplicación en las guías anteriores.

4.  Abra el archivo web.cloud.config que contiene la configuración del entorno que se utiliza cuando la aplicación se implementa en Azure:

        PS C:\node\tasklist\WebRole1> notepad web.cloud.config

5.  Inserte el siguiente bloque bajo el elemento **configuration**, reemplace {STORAGE ACCOUNT} y {STORAGE ACCESS KEY} por el nombre de la cuenta y la clave principal de la cuenta de almacenamiento que desea utilizar para la implementación:

        <appSettings>
          <add key="AZURE_STORAGE_ACCOUNT" value="{STORAGE ACCOUNT}"/>
          <add key="AZURE_STORAGE_ACCESS_KEY" value="{STORAGE ACCESS KEY}"/>
        </appSettings>

    ![Contenido del archivo web.cloud.config](./media/storage-nodejs-use-table-storage-cloud-service-app/node37.png)

6.  Guarde el archivo y cierre el Bloc de notas.

Instalación de módulos
----------------------

Para poder utilizar los servicios de administración de datos de Azure, debe instalar el módulo de Azure para el nodo. También debe instalar el módulo node-uuid, puesto que se usará para generar identificadores únicos globales (UUID). Para instalar estos módulos, escriba el siguiente comando:

    PS C:\node\tasklist\WebRole1> npm install node-uuid azure

Una vez completado el comando, los módulos se habrán agregado a la carpeta **node\_modules**. Realice los siguientes pasos para hacer uso de estos módulos en su aplicación:

1.  Abra el archivo server.js:

        PS C:\node\tasklist\WebRole1> notepad server.js

2.  Agregue el código siguiente después de la línea que termina con express.createServer() para incluir los módulos node-uuid, home y azure. El módulo home aún no existe, pero lo creará en breve.

    ![El código server.js con la línea app = modules.exports resaltada](./media/storage-nodejs-use-table-storage-cloud-service-app/node38.png)

        var uuid = require('node-uuid');
        var Home = require('./home');
        var azure = require('azure');

3.  Agregue código para crear un cliente de tabla de almacenamiento que pasa información de la cuenta de almacenamiento y la clave de acceso.

    **Nota:**

    Cuando se ejecuta en el emulador, el SDK utilizará automáticamente el emulador a pesar de que la información de la cuenta de almacenamiento se haya proporcionado a través de web.config.

        var client = azure.createTableService();

4.  A continuación, cree una tabla en el almacenamiento de Azure llamada tareas. La lógica siguiente crea una tabla nueva si no existe y la llena con algunos datos predeterminados.

        //creación de tabla
        client.createTableIfNotExists('tasks', function(error){
            if(error){
                throw error;
            }

            var item = {
                name: 'Add readonly task list',
                category: 'Site work',
                date: '12/01/2011',
                RowKey: uuid(),
                PartitionKey: 'partition1',
                completed: false
            };

            client.insertEntity('tasks', item, function(){});

        });

5.  Reemplace el código existente en la sección de ruta por el código a continuación, lo que crea una instancia de controlador principal y enruta todas las solicitudes a **/** o **/home** a este.

    ![El archivo server.js con la sección de rutas resaltado.](./media/storage-nodejs-use-table-storage-cloud-service-app/node39.png)

        var home = new Home(client);
        app.get('/', home.showItems.bind(home));
        app.get('/home', home.showItems.bind(home));

    Tenga en cuenta que en vez de controlar la solicitud en línea, ahora está delegando el comando a un objeto de inicio. El comando **bind** es necesario para asegurar que estas referencias se resuelven adecuadamente de manera local dentro del controlador principal.

Creación del controlador principal
----------------------------------

Ahora debe crear un controlador principal a fin de que controle todas las solicitudes del sitio de lista de tareas. Realice los siguientes pasos para crear el controlador:

1.  Cree un nuevo archivo home.js en el Bloc de notas. Este archivo contendrá el código del controlador que pasa la lógica de la lista de tareas.

        PS C:\node\tasklist\WebRole1> notepad home.js

2.  Reemplace el contenido por el código a continuación y guarde el archivo. El código siguiente utiliza el patrón de módulo de javascript. Exporta una función principal. El prototipo principal contiene las funciones para controlar las solicitudes reales.

        var azure=require('azure');
        module.exports = Home;

        function Home (client) {
            this.client = client;
        };

        Home.prototype = {
            showItems: function (req, res) {
                var self = this;
                this.getItems(false, function (resp, tasklist) {
                    if (!tasklist) {
                        tasklist = [];
                    }           
                    self.showResults(res, tasklist);
                });
            },

            getItems: function (allItems, callback) {
                var query = azure.TableQuery
                    .select()
                    .from('tasks');
                
                if (!allItems) {
                    query = query.where('completed eq 
            ', 'false');
                }
                this.client.queryEntities(query, callback);
             },

             showResults: function (res, tasklist) {
                res.render('home', { 
                    title: 'Todo list', 
                    layout: false, 
                    tasklist: tasklist });
             },
        };

    El controlador principal ahora incluye tres funciones:

    -   *showItems* controla la solicitud.
    -   *getItems* utiliza el cliente de tabla para recuperar elementos de tareas abiertas de la tabla de tareas. Tenga en cuenta que la consulta puede tener filtros adicionales aplicados; por ejemplo, los filtros de la consulta anterior solo muestran tareas donde completed es igual a false.
    -   *showResults* llama a la función de representación de Express para representar la página usando la vista principal que va a crear en la sección siguiente.

### Modificación de la vista principal

El motor de plantillas Jade utiliza una sintaxis de marcado que es menos detallada que HTML y es el motor predeterminado para trabajar con Express. Realice los siguientes pasos para crear una vista que admite mostrar los elementos de la lista de tareas:

1.  Desde la ventana de comandos de Windows PowerShell, edite el archivo home.jade utilizando el siguiente comando:

        PS C:\node\tasklist\WebRole1\views> notepad home.jade

2.  Reemplace el contenido del archivo home.jade por el siguiente código y guarde el archivo. El formulario siguiente contiene la funcionalidad para leer y actualizar los elementos de la tarea. (Tenga en cuenta que actualmente el controlador principal solo admite la lectura; podrá cambiar esto más adelante). El formulario contiene información detallada de cada elemento de la lista de tareas.

        html
        head
            title Index
        body
            h1 My ToDo List

            form
                table(border="1")
                    tr
                        td Name
                        td Category
                        td Date
                        td Complete

                        each item in tasklist
                            tr
                                td #{item.name}
                                td #{item.category} 
                                td #{item.date} 
                                td 
                                    input(type="checkbox", name="completed", value="#{item.RowKey}") 

Ejecución de la aplicación en el emulador de proceso
----------------------------------------------------

1.  En la ventana de Windows PowerShell, escriba el siguiente cmdlet para iniciar su servicio en el emulador de proceso y mostrar una página web que llama a su servicio.

        PS C:\node\tasklist\WebRole1> Start-AzureEmulator -launch

    El explorador muestra la página siguiente, que muestra el elemento de tarea que se ha recuperado desde el almacenamiento de Azure:

    ![Internet Explorer muestra una página My Tasklist con un elemento en una tabla.](./media/storage-nodejs-use-table-storage-cloud-service-app/node40.png)

Incorporación de la funcionalidad de tarea nueva
------------------------------------------------

En esta sección se actualiza la aplicación para que admita la incorporación de nuevos elementos de tareas.

### Incorporación de una ruta nueva en Server.js

En el archivo server.js, agregue la siguiente línea después de la última entrada de ruta para **/home** y, a continuación, guarde el archivo.

![El archivo server.js con la línea que contiene la ruta para principal resaltada.](./media/storage-nodejs-use-table-storage-cloud-service-app/node41.png)

        app.post('/home/newitem', home.newItem.bind(home));

    La sección de rutas debe tener el aspecto siguiente:

       // Rutas

       var home = new Home(client);
       app.get('/', home.showItems.bind(home));
       app.get('/home', home.showItems.bind(home));
       app.post('/home/newitem', home.newItem.bind(home));

### Incorporación del módulo Node-UUID

Para utilizar el módulo de node-uuid a fin de crear un identificador único, agregue la siguiente línea al principio del archivo home.js después de la primera línea donde se exporta el módulo.

![El archivo home.js con la línea module.exports = Home resaltada.](./media/storage-nodejs-use-table-storage-cloud-service-app/node42.png)

       var uuid = require('node-uuid');

### Incorporación de la función de elemento nuevo en el controlador principal

Para implementar la funcionalidad de elemento nuevo, cree una función **newItem**. En el archivo home.js, pegue el siguiente código después de la última función y guarde el archivo.

![La función showresults resaltada](./media/storage-nodejs-use-table-storage-cloud-service-app/node43.png)

       newItem: function (req, res) {
           var self = this;
           var createItem = function (resp, tasklist) {
               if (!tasklist) {
                   tasklist = [];
               }

               var count = tasklist.length;

               var item = req.body.item;
               item.RowKey = uuid();
               item.PartitionKey = 'partition1';
               item.completed = false;

               self.client.insertEntity('tasks', item, function (error) {
                   if(error){  
                       throw error;
                   }
                   self.showItems(req, res);
               });
           };

           this.getItems(true, createItem);
       },

La función **newItem** realiza las siguientes tareas:

-   Extrae el elemento publicado del cuerpo.
-   Establece los valores **RowKey** y **PartitionKey** para el elemento nuevo. Estos valores son necesarios para insertar el elemento en la tabla de Azure. Se genera un UUID para el valor **RowKey**.
-   Inserta el elemento en la tabla de tareas llamando a la función **insertEntity**.
-   Representa la página llamando a la función **getItems**.

### Incorporación del formulario de elemento nuevo en la vista principal

Ahora, actualice la vista mediante la incorporación de un formulario nuevo para permitir al usuario agregar un elemento. En el archivo home.jade, pegue el siguiente código al final del archivo y guárdelo.

**Nota:**

En Jade, el espacio en blanco es importante, por lo que no debe quitar ninguna separación a continuación.

        hr
        form(action="/home/newitem", method="post")
            table(border="1")    
                tr
                    td Item Name: 
                    td 
                        input(name="item[name]", type="textbox")
                tr
                    td Item Category: 
                    td 
                        input(name="item[category]", type="textbox")
                tr
                    td Item Date: 
                    td 
                        input(name="item[date]", type="textbox")
            input(type="submit", value="Add item")

### Ejecución de la aplicación en el emulador

1.  Debido a que el emulador de Azure ya se está ejecutando, puede examinar la aplicación actualizada:

        PS C:\node\tasklist\WebRole1> start http://localhost:81/home

    El explorador abre y muestra la siguiente página:

    ![Una página web con el título My Task List con una tabla que contiene las tareas y los campos para agregar una nueva tarea.](./media/storage-nodejs-use-table-storage-cloud-service-app/node44.png)

2.  Escriba para **Item Name:** "Nueva funcionalidad de tareas" **Item Category:** ¿"El sitio funciona"?, y para **Item Date:** "12/02/2011". Luego, haga clic en **Add item**.

    El elemento se agrega a la tabla de tareas en el almacenamiento de Azure y se muestra como aparece en la captura de pantalla a continuación.

    ![Una página web con el título My Task List con una tabla que contiene las tareas, después de agregar una tarea a la lista.](./media/storage-nodejs-use-table-storage-cloud-service-app/node45.png)

Nueva publicación de la aplicación en Azure
-------------------------------------------

Ahora que se ha completado la aplicación, publíquela en Azure mediante la actualización de la implementación en el servicio hospedado existente.

1.  En la ventana de Windows PowerShell, llame al siguiente cmdlet para volver a implementar el servicio hospedado en Azure. La configuración de almacenamiento y la ubicación se guardaron anteriormente y no es necesario volver a especificarlas.

        PS C:\node\tasklist\WebRole1> Publish-AzureServiceProject -name myuniquename -location datacentername -launch

    Después de que la implementación se haya completado, debe ver una respuesta similar a la siguiente:

    ![Los mensajes de estado que aparecen durante la implementación.](./media/storage-nodejs-use-table-storage-cloud-service-app/node35.png)

    Al igual que antes, debido a que ha especificado la opción **-launch**, el explorador se abre y muestra la aplicación que se ejecuta en Azure cuando se completa la publicación.

    ![Una ventana del explorador muestra la página My Task List. La URL indica que la página está hospedada en Azure.](./media/storage-nodejs-use-table-storage-cloud-service-app/node47.png)

Detención y eliminación de su aplicación
----------------------------------------

Después de implementar la aplicación, es posible que desee deshabilitarla a fin de evitar costes o compilar e implementar otras aplicaciones dentro del período de prueba gratuito.

Azure factura las instancias de rol web por hora consumida de tiempo de servidor. El tiempo de servidor se empieza a consumir una vez implementada su aplicación, incluso si las instancias no se están ejecutando y se encuentran detenidas.

Los siguientes pasos muestran cómo detener y eliminar su aplicación.

1.  En la ventana de Windows PowerShell, detenga la implementación del servicio creado en la sección anterior con el siguiente cmdlet:

        PS C:\node\tasklist\WebRole1> Stop-AzureService

    La detención del servicio puede durar varios minutos. Una vez detenido el servicio, recibirá un mensaje que le avisará de su detención.

    ![Mensajes de estado que indican que el servicio se ha detenido.](./media/storage-nodejs-use-table-storage-cloud-service-app/node48.png)

2.  Para eliminar el servicio, llame al siguiente cmdlet:

        PS C:\node\tasklist\WebRole1> Remove-AzureService contosotasklist

    Cuando se le solicite, escriba **Y** para eliminar el servicio.

    La eliminación del servicio puede durar varios minutos. Una vez eliminado el servicio, recibirá un mensaje que le avisará de su eliminación.

    ![Mensajes de estado que indican que el servicio se ha eliminado.](./media/storage-nodejs-use-table-storage-cloud-service-app/node49.png)


