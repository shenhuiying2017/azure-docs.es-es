<properties title="Build a Node.js web application using DocumentDB" pageTitle="Build a Node.js web application using DocumentDB | Azure" description="Learn how to use Azure DocumentDB to store and access data from a Node.js application hosted on Azure." metaKeywords="NoSQL, DocumentDB,  database, document-orientated database, JSON, getting started"   services="documentdb" solutions="data-management" documentationCenter=""  authors="ryancraw" manager="jhubbard" editor="cgronlun" scriptId="" />

<tags ms.service="documentdb" ms.workload="data-services" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="08/20/2014" ms.author="ryancraw"></tags>

# <a name="_Toc395783175">Generación de una aplicación web Node.js mediante la Base de datos de documentos</a>

<a name="_Toc395783175">

En este tutorial aprenderá a usar el servicio de Base de datos de documentos de Azure para almacenar y obtener acceso a datos
desde una aplicación Node.js Express hospedada en sitios web de Azure.

En este tutorial se asume que tiene experiencia previa con el uso de Node.js.

Aprenderá a:

· Utilizar las herramientas de Node.js para Visual Studio

· Trabajar con el servicio de Base de datos de documentos de Azure mediante el módulo npm de la base de datos de documentos

· Implementar la aplicación web en sitios web de Azure

Al seguir este tutorial, podrá compilar una aplicación de administración
de tareas basadas en web sencilla que permite crear, recuperar y
completar tareas. Las tareas se almacenarán como documentos JSON en la Base de datos de documentos de
Azure.

![Texto alternativo][]

## <a name="_Toc395783176">Requisitos previos</a>

Antes de seguir las instrucciones del presente artículo, debe asegurarse
de tener instalados los siguientes elementos:

[Node.js][] versión v0.10.29 o superior

[Git][]

[Visual Studio 2013][] con actualización 3

[Herramientas de Node.js para Visual Studio][]

**Nota:** mientras utilizamos Visual Studio para generar, depurar e implementar nuestro proyecto de Node.js
en este tutorial puede utilizar el editor que prefiera
y ejecutar Node.js directamente en la plataforma que elija de la forma en la que
normalmente ejecutaría un proyecto de Node.js. A continuación, puede utilizar las herramientas de la [CLI de Azure][] para
implementar su aplicación en sitios web de Azure

# <a name="_Toc395637761">Creación de una cuenta de base de datos de la Base de datos de documentos</a>

Para aprovisionar una cuenta de base de datos de la Base de datos de documentos en Azure, abra el [Portal de administración de Azure][]
y haga clic en el mosaico de la Galería de Azure de la página de inicio o haga clic en "+" en la esquina inferior izquierda de la pantalla.

![Texto alternativo][1]

Esto abrirá la Galería de Azure, donde podrá seleccionar entre muchos
servicios de Azure disponibles. En la Galería, seleccione "Datos, almacenamiento y
copia de seguridad" de la lista de categorías.

![Texto alternativo][2]

Desde aquí, seleccione la opción de la Base de datos de documentos de Azure.

![Texto alternativo][3]

A continuación, seleccione "Crear" en la parte inferior de la pantalla.

![Texto alternativo][4]

Esto abrirá el cuadro "Nueva Base de datos de documentos" donde puede especificar el
nombre, región, escala, grupo de recursos y otras especificaciones de su nueva
cuenta.

![Texto alternativo][5]

Una vez que haya terminado de proporcionar los valores de la cuenta, haga clic en "Crear"
y el proceso de aprovisionamiento comenzará a crear su cuenta de base de datos.
Una vez que se haya completado el proceso de aprovisionamiento debería ver una notificación
en el área de notificaciones del portal y el icono de su
pantalla de inicio (si ha seleccionado crear uno) cambiará para mostrar la
acción completada.

![Texto alternativo][6]

Una vez que se haya completado el aprovisionamiento, al hacer clic en el icono de la Base de datos de documentos de la
pantalla inicio, se abrirá el cuadro principal para la cuenta de la
Base de datos de documentos recién creada.

![Texto alternativo][7]
![Texto alternativo][8]

Mediante el botón "Claves", obtenga acceso a su URL de extremo y a la clave principal,
cópielas en el portapapeles y consérvelas a mano ya que utilizaremos estos
valores en la aplicación web que crearemos a continuación.

## <a name="_Toc395783178">Creación de una nueva aplicación Node.js</a>

En Visual Studio, seleccione Archivo – Proyecto nuevo y Seleccionar para crear una "Aplicación Express básica de Microsoft Azure".

![Texto alternativo][9]

Esto creará una aplicación Express básica para usted. Si se le
solicita "instalar dependencias" seleccione "Sí". Esto instalará todos los
paquetes npm que sean necesarios para la nueva aplicación Express.

Una vez se haya completado esto el Explorador de soluciones debería tener un aspecto parecido al
siguiente;

![Texto alternativo][10]

Esto muestra que tiene Express, Jade y Stylus instalado.

Si pulsa F5 en Visual Studio se debería generar el proyecto, iniciar
Node.js y mostrar un explorador con el Express equivalente de "Hola
mundo".

![Texto alternativo][11]

## <a name="_Toc395783179">Instalar módulos adicionales</a>

El archivo **package.json** es uno de los archivos creados en la raíz del
proyecto. Este archivo contiene una lista de módulos adicionales
necesarios para una aplicación Express. Posteriormente, cuando implemente
esta aplicación en un sitio web de Azure, se usará este archivo para determinar
los módulos que se deben instalar en Azure para que sea compatible con su aplicación.

![Texto alternativo][12]

Tenemos que instalar dos paquetes más para este tutorial.

Haga clic con el botón derecho en "npm" en el Explorador de soluciones y seleccione "Instalar paquetes
npm".

![Texto alternativo][13]

En el cuadro de diálogo "Instalar nuevos paquetes npm", especifique **nconf** para buscar
el módulo. La aplicación utilizará este módulo para leer los
valores de configuración la base de datos desde un archivo de configuración.

![Texto alternativo][14]

Por último, instale el módulo de la Base de datos de documentos de Azure de la misma forma buscando
**documentdb**. En este módulo es donde ocurre toda la magia de la Base de datos de
documentos.

Una vez que haya instalado estos dos módulos adicionales y las dependencias,
deberían aparecen en el Explorador de soluciones bajo los módulos**npm**
.

![Texto alternativo][15]

Una comprobación rápida del archivo **package.json** de la aplicación debería
mostrar los módulos adicionales. Este archivo indicará más tarde a Azure qué
paquetes necesita descargar e instalar cuando esté ejecutando su
aplicación.

Edite el archivo package.json, si es necesario, para que se parezca al siguiente ejemplo.

![Texto alternativo][16]

Esto indica a Node (y a Azure más tarde) que su aplicación depende de estos
módulos adicionales.

# <a name="_Toc395783180">Uso del servicio de Base de datos de documentos en una aplicación Node</a>

Se encarga de toda la configuración e instalación inicial; ahora volvamos a la razón por la que estamos aquí que es escribir código mediante la Base de datos de documentos de Azure. Para empezar, edite **app.js** ubicado en la raíz de la aplicación Express que acabamos de crear. Busque las siguientes líneas en el archivo;

    app.get('/', routes.index);
    app.get('/users', user.list);

Y reemplácelas por las dos líneas siguientes;

    app.get('/', routes.index);
    app.post('/', routes.createOrUpdateItem);

Esto indica a la aplicación qué hacer con los métodos GET y POST predeterminados en un Formulario que crearemos a continuación.

Ahora busque el archivo **index.js** en la carpeta **rutas**. Ábrala en el editor y elimine todo el código encontrado en este archivo.

Agregue lo siguiente en la parte superior del archivo;

    // import the modules we will use
    var DocumentDBClient = require('documentdb').DocumentClient;
    var nconf = require('nconf');

    // tell nconf which config file to use
    nconf.env();
    nconf.file({ file: 'config.json' });

Esto define los módulos que vamos a utilizar, que son **documentdb** y **nconf**.

**nconf** es un módulo que le permite cargar valores de configuración, como cadenas de conexión de base de datos, desde archivos externos en lugar de colocar estos valores en línea en su código. nconf buscará un **config.json** de forma predeterminada para su configuración.

Continuemos y creemos un archivo de texto vacío llamado **config.json** en la raíz del proyecto (misma ubicación que app.js)

Abra este nuevo archivo **config.json** y especifique los siguientes valores como los adecuados para su extremo de Base de datos de documentos. Asegúrese de establecer los valores HOST y MASTER\_KEY correctamente.

    {
        "HOST"       : "<insert your DocDB endpoint here>",
        "AUTH_KEY"   : "<insert either primary or secondary key here>",
        "DATABASE"   : "ToDoList",
        "COLLECTION" : "Items"
    }

Ahora, volviendo a **index.js**, agregue las siguientes líneas tras las últimas líneas para leer realmente el archivo de configuración y almacenar los valores en las variables de nivel de página.

    var host = nconf.get("HOST");
    var authKey = nconf.get("AUTH_KEY");
    var databaseId = nconf.get("DATABASE");
    var collectionId = nconf.get("COLLECTION");

Ahora que está hecho, continúe agregando el siguiente código a **index.js**

    // create some global variables which we will use later to hold instances of the DocumentDBClient, Database and Collection

    // create an instance of the DocumentDB client
    var client = new DocumentDBClient(host, { masterKey: authKey });

    exports.index = function (req, res) {       
        // before we can query for Items in the document store, we need to ensure we 
        // have a database with a collection then use the collection to read the documents
        readOrCreateDatabase(function (database) {
            readOrCreateCollection(database, function (collection) {
                listItems(collection, function (items) {
                    res.render('index', { title: 'My ToDo List', tasks: items });
                });    
            });
        });
    };

    exports.createOrUpdateItem = function (req, res) {
        //first have to set the database & collection context so that we have the self links   
        readOrCreateDatabase(function (database) {
            readOrCreateCollection(database, function (collection) {
                        
                //if we find an item on the form, we'll create it in the database
                var item = req.body.item;
                if (item) {
                    createItem(collection, item, function () {
                    res.redirect('/');
                });
                
                //else let's look for items marked as completed, 
                //and update that item in the database
                } else {
                    var completed = req.body.completed;
                    
                    //check if completed is actually an Array, if not make it one. 
                    //this happens when you select only one item            
                    if (!completed.forEach)
                        completed = [completed];
                    
                    //use a recursive function to loop through items in 
                    //array calling updateItem each time through                                    
                    function updater(i) {
                        if (i < completed.length) {
                            updateItem(collection, completed[i], function () {
                                updater(i + 1);
                            });
                        } else {
                            res.redirect('/');
                        }
                    }
                    
                    //kick off the recursion
                    updater(0);
                }
            });
        });
    }

Estas son las dos funciones que hemos indicado a la aplicación que utilice antes en **app.js** cuando hemos definido las rutas. Cuando un GET alcanza la vista de índice, la función **exports.index** se ejecutará y, de forma similar, cuando se recibe un POST en la vista de índice, se ejecutará la función **exports.createOfUpdateItem**.

Estas dos funciones hacen todo el trabajo de la aplicación que estamos generando, sin embargo, invocan a otras funciones, simplemente para hacer que el código sea más legible y más fácil de seguir. Continúe agregando el código siguiente en el archivo **index.js**. Esto contiene todos los métodos utilizados por las dos funciones anteriores y contiene todas las llamadas a la Base de datos de documentos. Exploraremos cada función con más detalle más tarde.

    // update item
    var updateItem = function (collection, itemId, callback) {
        //first fetch the document based on the id
        getItem(collection, itemId, function (doc) {
            //now replace the document with the updated one
            doc.completed = true;
            client.replaceDocument(doc._self, doc, function (err, replacedDoc) {
                if (err) {
                    throw (err);
                }
                
                callback();
            });
        });
    }

    // get item
    var getItem = function (collection, itemId, callback) {      
        client.queryDocuments(collection._self, 'SELECT * FROM root r WHERE r.id="' + itemId + '"').toArray(function (err, results) {
            if (err) {
                throw (err);
            }

            callback(results[0]);
        });
    }

    // create new item
    var createItem = function (collection, documentDefinition, callback) {
        documentDefinition.completed = false;
        client.createDocument(collection._self, documentDefinition, function (err, doc) {
            if (err) {
                throw (err);
            }
            
            callback();
        });
    }

    // query the provided collection for all non-complete items
    var listItems = function (collection, callback) {
        client.queryDocuments(collection._self, 'SELECT * FROM root r WHERE r.completed=false').toArray(function (err, docs) {
            if (err) {
                throw (err);
            }
            
            callback(docs);
        });
    }

    // if the database does not exist, then create it, else return the database object
    var readOrCreateDatabase = function (callback) {
        client.queryDatabases('SELECT * FROM root r WHERE r.id="' + databaseId + '"').toArray(function (err, results) {
            if (err) {
                // some error occured, rethrow up
                throw (err);
            }
            if (!err && results.length === 0) {
                // no error occured, but there were no results returned 
                // indicating no database exists matching the query            
                client.createDatabase({ id: databaseId }, function (err, createdDatabase) {
                    callback(createdDatabase);
                });
            } else {
                // we found a database
                callback(results[0]);
            }
        });
    };

    // if the collection does not exist for the database provided, create it, else return the collection object
    var readOrCreateCollection = function (database, callback) {
        client.queryCollections(database._self, 'SELECT * FROM root r WHERE r.id="' + collectionId + '"').toArray(function (err, results) {
            if (err) {
                // some error occured, rethrow up
                throw (err);
            }           
            if (!err && results.length === 0) {
                // no error occured, but there were no results returned 
                //indicating no collection exists in the provided database matching the query
                client.createCollection(database._self, { id: collectionId }, function (err, createdCollection) {
                    callback(createdCollection);
                });
            } else {
                // we found a collection
                callback(results[0]);
            }
        });
    };

**updateItem**: actualiza un documento en la base de datos según el identificador del elemento que se ha pasado desde el formulario. Utiliza este identificador para ejecutar un método ***readDocument*** frente a la Base de datos de documentos para leer el documento específico que hemos almacenado. Entonces cambia el atributo "completado" del documento a verdadero, indicando que ahora está completado y, a continuación, continúa reemplazando el documento en la base de datos.

**getItem**: utiliza ***queryDocuments*** para obtener un único elemento de la base de datos mediante la propiedad de identificador del elemento.

**createItem**: utiliza el método ***createDocument*** para crear un nuevo documento en la base de datos según el Nombre de elemento y Categoría de elemento especificados en el formulario. También establece el indicador completado en falso para indicar que este elemento aún no está completado.

**listItems**: utiliza ***queryDocuments*** para buscar todos los documentos de la colección que aún no están completados, donde completado=falso. Utiliza la gramática de la Base de datos de documentos que está basada en ANSI-SQL para demostrar esta capacidad de consulta familiar pero poderosa.

**readOrCreateDatabase**: utiliza ***queryDatabases*** para comprobar si ya existe una base de datos con este nombre. Si no podemos encontrar una, entonces avanzamos y utilizamos ***createDatabase*** para crear una nueva base de datos con el identificador proporcionado (desde nuestro archivo de configuración) en el extremo especificado (también desde el archivo de configuración)

**readOrCreateCollection**: como con readOrCreateDatabase este método primero intenta encontrar una colección con el identificador proporcionado, si existe una se devuelve y si no existe se crea una para usted.

Esto es todo el código que es necesario para interactuar con la Base de datos de documentos para esta aplicación de muestra.

Ahora dirijamos nuestra atención a la generación de la interfaz de usuario para que un usuario pueda realmente interactuar con nuestra aplicación. La aplicación Express que hemos creado utiliza **Jade** como el motor de búsqueda. Para obtener más información sobre Jade, consulte <http://jade-lang.com/>.

Abra el archivo **layout.jade** que se encuentra en la carpeta **vistas** y reemplace el contenido con lo siguiente;

    doctype html
    html
      head
        title= title
        meta(http-equiv='X-UA-Compatible', content='IE=10')
        link(rel='stylesheet', href='/stylesheets/style.css')
        link(rel='stylesheet', href='/stylesheets/bootstrap.min.css')
      body.app
        nav.navbar.navbar-fixed-top
          div.navbar-inner
            container
                a.brand(href='/') My Tasks
        block content

Esto indica al motor **Jade** de forma efectiva, que represente HTML de nuestra aplicación y cree un "**bloque**" llamado “**contenido**” dónde podemos proporcionar el diseño de nuestras páginas de contenido.

Ahora abra el archivo **index.jade** , la vista asociada con index.js y sustituya el contenido con lo siguiente;

    extends layout

    block content
      h1= title
      br

      form(action="/", method="post")
        table.table.table-striped.table-bordered
          tr
            td Name
            td Category
            td Complete
          each task in tasks
            tr
                td #{task.name}
                td #{task.category}
                td 
                    input(type="checkbox", name="completed", value="#{task.id}", checked=(task.completed == true))
        button.btn(type="submit") Update tasks

      hr

      form.well(action="/", method="post")
        label Item Name:  
        input(name="item[name]", type="textbox")
        label Item Category: 
        input(name="item[category]", type="textbox")
        br
        button.btn(type="submit") Add item

Esto amplía el diseño y proporciona contenido para el marcador de posición "contenido del bloque"
que hemos visto en el archivo layout.jade antes.

En este diseño hemos creado dos formularios HTML. El primer formulario contiene una tabla
para nuestros datos y un botón que permite actualizar elementos. El segundo formulario contiene
algunos campos de entrada y un botón que nos permite crear un nuevo elemento.

Cuando se hace clic en cualquiera de estos botones, **createOrUpdateItem**
(es decir, se ha realizado un formulario POST), se llamará a la función que hemos creado en el archivo **index.js**
. Si esta página se carga directamente (es decir, se realiza un GET
), entonces se ejecuta la función **index**.

Esto debería ser todo lo que necesitamos para que nuestra aplicación funcione.

</h1>
# <a name="_Toc395783181">Ejecución de la aplicación de forma local</a>

Para probar la aplicación en su equipo local, pulse F5 en Visual Studio
y se debería generar la aplicación, inicie Node.js y ejecute un explorador con una
página que se parezca a la siguiente imagen:

![Texto alternativo][17]

1. Utilice los campos proporcionados para Elemento, Nombre de elemento y Categoría para especificar
información y, a continuación, haga clic en **Agregar elemento**.

2. La página debería actualizarse para mostrar el elemento recién creado en la lista
ToDo.

	![Texto alternativo][18]

3. Para completar una tarea, simplemente marque la casilla en la columna Completado y,
a continuación, haga clic en **Actualizar tareas**.

</h1>
# <a name="_Toc395783182">Implementación de su aplicación en sitios web de Azure</a>

Con las herramientas de Node.js para Visual Studio instaladas, la implementación en los sitios web de
Azure se puede realizar fácilmente en unos pocos pasos.

Haga clic con el botón derecho en el proyecto y seleccione "Publicar".

![Texto alternativo][19]

A continuación, siga el asistente de publicación para proporcionar la configuración requerida para el sitio web de
Azure. El asistente le permitirá seleccionar un sitio web
existente para actualizarlo o crear uno nuevo.

Una vez que haya proporcionado la configuración necesaria, simplemente pulse "Publicar".

![Texto alternativo][20]

A continuación, siga el asistente de publicación para proporcionar la configuración requerida para el sitio web de
Azure. El asistente le permitirá seleccionar un sitio web
existente para actualizarlo o crear uno nuevo.

Una vez que haya proporcionado la configuración necesaria, simplemente pulse "Publicar".

Y Visual Studio se conectará a su suscripción de Azure y publicará
esta aplicación de Node.js.

En pocos segundos, Visual Studio terminará de publicar su aplicación
web y ejecutará un explorador donde podrá ver su útil trabajo
ejecutándose en Azure.

# <a name="_Toc395783183"></a> <a name="_Toc395637775">Conclusión</a>

¡Enhorabuena! Acaba de generar su primera aplicación web Express de
Node.js mediante la Base de datos de documentos de Azure y publicarlos en los sitios web de Azure.

El código origen para la aplicación de referencia completa se puede descargar [aquí][].

</h1>

  [Texto alternativo]: ./media/documentdb-nodejs-application/image1.png
  [Node.js]: http://nodejs.org/
  [Git]: http://git-scm.com/
  [Visual Studio 2013]: http://msdn.microsoft.com/en-us/vstudio/cc136611.aspx
  [Herramientas de Node.js para Visual Studio]: https://nodejstools.codeplex.com/
  [CLI de Azure]: http://azure.microsoft.com/es-es/documentation/articles/xplat-cli/
  [Portal de administración de Azure]: http://portal.azure.com
  [1]: ./media/documentdb-nodejs-application/image2.png
  [2]: ./media/documentdb-nodejs-application/image3.png
  [3]: ./media/documentdb-nodejs-application/image4.png
  [4]: ./media/documentdb-nodejs-application/image5.png
  [5]: ./media/documentdb-nodejs-application/image6.png
  [6]: ./media/documentdb-nodejs-application/image7.png
  [7]: ./media/documentdb-nodejs-application/image8.png
  [8]: ./media/documentdb-nodejs-application/image9.png
  [9]: ./media/documentdb-nodejs-application/image10.png
  [10]: ./media/documentdb-nodejs-application/image11.png
  [11]: ./media/documentdb-nodejs-application/image12.png
  [12]: ./media/documentdb-nodejs-application/image13.png
  [13]: ./media/documentdb-nodejs-application/image14.png
  [14]: ./media/documentdb-nodejs-application/image15.png
  [15]: ./media/documentdb-nodejs-application/image16.png
  [16]: ./media/documentdb-nodejs-application/image17.png
  [17]: ./media/documentdb-nodejs-application/image18.png
  [18]: ./media/documentdb-nodejs-application/image19.png
  [19]: ./media/documentdb-nodejs-application/image20.png
  [20]: ./media/documentdb-nodejs-application/image21.png
  [aquí]: http://go.microsoft.com/fwlink/?LinkID=509839&clcid=0x409
