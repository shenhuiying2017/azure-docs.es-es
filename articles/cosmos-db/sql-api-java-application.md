---
title: Tutorial de desarrollo de aplicaciones Java con Azure Cosmos DB | Microsoft Docs
description: "En este tutorial de aplicaciones web de Java, aprenderá a usar Azure Cosmos DB y la API de SQL para almacenar datos y acceder a ellos desde una aplicación de Java hospedada en Azure Websites."
keywords: "Desarrollo de aplicación, tutorial de base de datos, aplicación de Java, tutorial de aplicación web de Java, Azure, Microsoft Azure"
services: cosmos-db
documentationcenter: java
author: dennyglee
manager: jhubbard
editor: mimig
ms.assetid: 0867a4a2-4bf5-4898-a1f4-44e3868f8725
ms.service: cosmos-db
ms.devlang: java
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.date: 08/22/2017
ms.author: denlee
ms.openlocfilehash: 8507b772c537ac50bd40367fbde260a8d72375ca
ms.sourcegitcommit: 821b6306aab244d2feacbd722f60d99881e9d2a4
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/18/2017
---
# <a name="build-a-java-web-application-using-azure-cosmos-db-and-the-sql-api"></a>Compilación de una aplicación web de Java mediante Azure Cosmos DB y la API de SQL
> [!div class="op_single_selector"]
> * [.NET](sql-api-dotnet-application.md)
> * [Node.js](sql-api-nodejs-application.md)
> * [Java](sql-api-java-application.md)
> * [Python](sql-api-python-application.md)
> 
> 

[!INCLUDE [cosmos-db-sql-api](../../includes/cosmos-db-sql-api.md)]

En este tutorial de aplicación web Java aprenderá a usar el servicio [Microsoft Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/) para almacenar datos y acceder a ellos desde una aplicación de Java hospedada en Azure App Service Web Apps. En este tema, aprenderá:

* Cómo crear una aplicación de JavaServer Pages (JSP) básica en Eclipse.
* Cómo trabajar con el servicio Azure Cosmos DB con el [SDK de Java de Azure Cosmos DB](https://github.com/Azure/azure-documentdb-java).

Este tutorial de aplicación Java muestra cómo crear una aplicación de administración de tareas basadas en web que permite crear, recuperar y marcar tareas como completadas, tal como se muestra en la siguiente imagen. Las tareas de la lista ToDo se almacenan como documentos JSON en Azure Cosmos DB.

![Aplicación Java My ToDo List](./media/sql-api-java-application/image1.png)

> [!TIP]
> En este tutorial de desarrollo de aplicaciones se supone que tiene experiencia anterior en el uso de Java. Si no está familiarizado con Java o con las [herramientas de requisitos previos](#Prerequisites), se recomienda que descargue el proyecto completo [todo](https://github.com/Azure-Samples/documentdb-java-todo-app) de GitHub y lo compile mediante las [instrucciones que se encuentran al final de este artículo](#GetProject). Una vez compilado, puede revisar el artículo para obtener información sobre el código en el contexto del proyecto.  
> 
> 

## <a id="Prerequisites"></a>Requisitos previos para este tutorial de aplicación web de Java
Antes de comenzar este tutorial de desarrollo de aplicaciones, debe disponer de lo siguiente:

*  Si no tiene una suscripción a Azure, cree una [cuenta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de empezar. 

  [!INCLUDE [cosmos-db-emulator-docdb-api](../../includes/cosmos-db-emulator-docdb-api.md)]

* [Kit de desarrollo de Java (JDK) 7+](http://www.oracle.com/technetwork/java/javase/downloads/index.html).
* [IDE de Eclipse para desarrolladores de Java EE.](http://www.eclipse.org/downloads/packages/eclipse-ide-java-ee-developers/lunasr1)
* [Un sitio web de Azure con un entorno de tiempo de ejecución Java (por ejemplo, Tomcat o Jetty) habilitado.](../app-service/app-service-web-get-started-java.md)

Si va a instalar estas herramientas por primera vez, coreservlets.com proporciona un ejemplo paso a paso del proceso de instalación en la sección de inicio rápido de su artículo [Tutorial: Instalación de TomCat7 y uso con Eclipse](http://www.coreservlets.com/Apache-Tomcat-Tutorial/tomcat-7-with-eclipse.html) .

## <a id="CreateDB"></a>Paso 1: Creación de una cuenta de Azure Cosmos DB
Para comenzar, creemos una cuenta de Azure Cosmos DB. Si ya tiene una cuenta o si usa el Emulador de Azure Cosmos DB para este tutorial, puede ir directamente al [Paso 2: Creación de la aplicación de Java JSP](#CreateJSP).

[!INCLUDE [create-dbaccount](../../includes/cosmos-db-create-dbaccount.md)]

[!INCLUDE [keys](../../includes/cosmos-db-keys.md)]

## <a id="CreateJSP"></a>Paso 2: Creación de la aplicación JSP de Java
Para crear la aplicación JSP:

1. En primer lugar, empezaremos por la creación de un proyecto Java. Inicie Eclipse, haga clic en **File** (Archivo), haga clic en **New** (Nuevo) y luego haga clic en **Dynamic Web Project** (Proyecto web dinámico). Si **Dynamic Web Project** (Proyecto web dinámico) no aparece como proyecto disponible, haga lo siguiente: haga clic en **File** (Archivo), **New** (Nuevo), **Project** (Proyecto), expanda **Web**, haga clic en **Dynamic Web Project** (Proyecto web dinámico) y haga clic en **Next** (Siguiente).
   
    ![Desarrollo de aplicaciones Java JSP](./media/sql-api-java-application/image10.png)
2. Escriba un nombre de proyecto en el cuadro **Project name** (Nombre de proyecto) y en el menú desplegable **Target Runtime** (Tiempo de ejecución de destino), seleccione opcionalmente un valor (por ejemplo, Apache Tomcat v7.0) y, a continuación, haga clic en **Finish** (Finalizar). Si selecciona un tiempo de ejecución de destino, puede ejecutar el proyecto localmente a través de Eclipse.
3. En Eclipse, en la vista del explorador de proyectos, expanda el proyecto. Haga clic con el botón derecho en **WebContent**, haga clic en **New** (Nuevo) y, después, en **JSP File** (Archivo JSP).
4. En el cuadro de diálogo **New JSP File** (Nuevo archivo JSP), asigne al archivo el nombre **index.jsp**. Mantenga la carpeta principal como **WebContent**, como se muestra en la ilustración siguiente y, a continuación, haga clic en **Next** (Siguiente).
   
    ![Creación de un nuevo archivo JSP - Tutorial de aplicación web de Java](./media/sql-api-java-application/image11.png)
5. En el cuadro de diálogo **Select JSP Template** (Seleccionar plantilla JSP), para cumplir con el objetivo de este tutorial, seleccione **New JSP File (html)** (Nuevo archivo JSP [html]) y haga clic en **Finish** (Finalizar).
6. Cuando el archivo index.jsp se abra en Eclipse, agregue texto para mostrar **Hola a todos** dentro del elemento existente. dentro del elemento <body> existente. El contenido actualizado <body> debe ser similar al código siguiente:
   
        <body>
            <% out.println("Hello World!"); %>
        </body>
7. Guarde el archivo index.jsp.
8. Si ha establecido un tiempo de ejecución de destino en el paso 2, puede hacer clic en **Project** (Proyecto) y, a continuación, en **Run** (Ejecutar) para ejecutar su aplicación de JSP localmente:
   
    ![Hello World – Tutorial de aplicación de Java](./media/sql-api-java-application/image12.png)

## <a id="InstallSDK"></a>Paso 3: Instalación del SDK de Java para SQL
La manera más sencilla de insertar el SDK de Java para SQL y sus dependencias es a través de [Apache Maven](http://maven.apache.org/).

Para ello, deberá convertir su proyecto en un proyecto Maven realizando los pasos siguientes:

1. Haga clic con el botón derecho en el proyecto en el explorador de proyectos, haga clic en **Configure** (Configurar) y en **Convert to Maven Project** (Convertir en proyecto Maven).
2. En la ventana **Crear nueva POM**, acepte los valores predeterminados y haga clic en **Finalizar**.
3. En **Explorador de proyectos**, abra el archivo pom.xml.
4. En la ficha **Dependencias**, en el panel **Dependencias**, haga clic en **Agregar**.
5. En la ventana **Seleccionar dependencia** , haga lo siguiente:
   
   * En el cuadro **GroupId**, escriba com.microsoft.azure.
   * En el cuadro **Id. de artefacto**, escriba azure-documentdb.
   * En el cuadro **Versión**, escriba 1.5.1.
     
   ![Instalación del SDK de la aplicación Java en SQL](./media/sql-api-java-application/image13.png)
     
   * O bien, agregue el XML de dependencia para identificador de grupo y un identificador de artefacto directamente al archivo pom.xml mediante un editor de texto:
     
        <dependency><groupId>com.microsoft.azure</groupId><artifactId>azure-documentdb</artifactId><version>1.9.1</version></dependency>
6. Haga clic en **Aceptar** y Maven instalará el SDK de Java para SQL.
7. Guarde el archivo pom.xml.

## <a id="UseService"></a>Paso 4: Uso del servicio Azure Cosmos DB en una aplicación de Java
1. En primer lugar, vamos a definir el objeto TodoItem en TodoItem.java:
   
        @Data
        @Builder
        public class TodoItem {
            private String category;
            private boolean complete;
            private String id;
            private String name;
        }
   
    En este proyecto, estamos usando [Project Lombok](http://projectlombok.org/) para generar el constructor, los captadores, los establecedores y un generador. Como alternativa, puede escribir este código manualmente o dejar que el IDE lo genere.
2. Para invocar el servicio Azure Cosmos DB, debe crear una nueva instancia de **DocumentClient**. En general, es mejor volver a utilizar **DocumentClient** , en lugar de construir un nuevo cliente para cada solicitud posterior. Podemos volver a usar el cliente ajustando el cliente en una **DocumentClientFactory**. En DocumentClientFactory.java, tendrá que pegar el valor URI y CLAVE PRINCIPAL que guardó en el portapapeles en el [paso 1](#CreateDB). Reemplace [YOUR\_ENDPOINT\_HERE] por el URI y reemplace [YOUR\_KEY\_HERE] por su CLAVE PRINCIPAL.
   
        private static final String HOST = "[YOUR_ENDPOINT_HERE]";
        private static final String MASTER_KEY = "[YOUR_KEY_HERE]";
   
        private static DocumentClient documentClient = new DocumentClient(HOST, MASTER_KEY,
                        ConnectionPolicy.GetDefault(), ConsistencyLevel.Session);
   
        public static DocumentClient getDocumentClient() {
            return documentClient;
        }
3. Ahora debe crear un objeto de acceso a datos (DAO) para abstraer la conservación de nuestros elementos ToDo en Azure Cosmos DB.
   
    Para guardar los elementos ToDo en una colección, el cliente necesitará saber en qué base de datos y colección se conservarán (como se hace referencia por los self-links). En general, es mejor almacenar en memoria caché la base de datos y la colección cuando sea posible para evitar recorridos de ida y vuelta adicionales a la base de datos.
   
    El código siguiente muestra cómo recuperar nuestra base de datos y colección si existe, o crear una nueva si no existe:
   
        public class DocDbDao implements TodoDao {
            // The name of our database.
            private static final String DATABASE_ID = "TodoDB";
   
            // The name of our collection.
            private static final String COLLECTION_ID = "TodoCollection";
   
            // The Azure Cosmos DB Client
            private static DocumentClient documentClient = DocumentClientFactory
                    .getDocumentClient();
   
            // Cache for the database object, so we don't have to query for it to
            // retrieve self links.
            private static Database databaseCache;
   
            // Cache for the collection object, so we don't have to query for it to
            // retrieve self links.
            private static DocumentCollection collectionCache;
   
            private Database getTodoDatabase() {
                if (databaseCache == null) {
                    // Get the database if it exists
                    List<Database> databaseList = documentClient
                            .queryDatabases(
                                    "SELECT * FROM root r WHERE r.id='" + DATABASE_ID
                                            + "'", null).getQueryIterable().toList();
   
                    if (databaseList.size() > 0) {
                        // Cache the database object so we won't have to query for it
                        // later to retrieve the selfLink.
                        databaseCache = databaseList.get(0);
                    } else {
                        // Create the database if it doesn't exist.
                        try {
                            Database databaseDefinition = new Database();
                            databaseDefinition.setId(DATABASE_ID);
   
                            databaseCache = documentClient.createDatabase(
                                    databaseDefinition, null).getResource();
                        } catch (DocumentClientException e) {
                            // TODO: Something has gone terribly wrong - the app wasn't
                            // able to query or create the collection.
                            // Verify your connection, endpoint, and key.
                            e.printStackTrace();
                        }
                    }
                }
   
                return databaseCache;
            }
   
            private DocumentCollection getTodoCollection() {
                if (collectionCache == null) {
                    // Get the collection if it exists.
                    List<DocumentCollection> collectionList = documentClient
                            .queryCollections(
                                    getTodoDatabase().getSelfLink(),
                                    "SELECT * FROM root r WHERE r.id='" + COLLECTION_ID
                                            + "'", null).getQueryIterable().toList();
   
                    if (collectionList.size() > 0) {
                        // Cache the collection object so we won't have to query for it
                        // later to retrieve the selfLink.
                        collectionCache = collectionList.get(0);
                    } else {
                        // Create the collection if it doesn't exist.
                        try {
                            DocumentCollection collectionDefinition = new DocumentCollection();
                            collectionDefinition.setId(COLLECTION_ID);
   
                            collectionCache = documentClient.createCollection(
                                    getTodoDatabase().getSelfLink(),
                                    collectionDefinition, null).getResource();
                        } catch (DocumentClientException e) {
                            // TODO: Something has gone terribly wrong - the app wasn't
                            // able to query or create the collection.
                            // Verify your connection, endpoint, and key.
                            e.printStackTrace();
                        }
                    }
                }
   
                return collectionCache;
            }
        }
4. El siguiente paso es escribir algo de código para conservar los TodoItems en la colección. En este ejemplo, usaremos [Gson](https://code.google.com/p/google-gson/) para serializar y deserializar TodoItem Plain Old Java Objects (POJO, objetos de Java antiguos sin formato de tareas pendientes) en los documentos JSON.
   
        // We'll use Gson for POJO <=> JSON serialization for this example.
        private static Gson gson = new Gson();
   
        @Override
        public TodoItem createTodoItem(TodoItem todoItem) {
            // Serialize the TodoItem as a JSON Document.
            Document todoItemDocument = new Document(gson.toJson(todoItem));
   
            // Annotate the document as a TodoItem for retrieval (so that we can
            // store multiple entity types in the collection).
            todoItemDocument.set("entityType", "todoItem");
   
            try {
                // Persist the document using the DocumentClient.
                todoItemDocument = documentClient.createDocument(
                        getTodoCollection().getSelfLink(), todoItemDocument, null,
                        false).getResource();
            } catch (DocumentClientException e) {
                e.printStackTrace();
                return null;
            }
   
            return gson.fromJson(todoItemDocument.toString(), TodoItem.class);
        }
5. Al igual que las colecciones y las bases de datos de Azure Cosmos DB, a los documentos también se hace referencia mediante autovínculos. La siguiente función auxiliar nos permite recuperar documentos por otro atributo (por ejemplo, "id") en lugar del self-link:
   
        private Document getDocumentById(String id) {
            // Retrieve the document using the DocumentClient.
            List<Document> documentList = documentClient
                    .queryDocuments(getTodoCollection().getSelfLink(),
                            "SELECT * FROM root r WHERE r.id='" + id + "'", null)
                    .getQueryIterable().toList();
   
            if (documentList.size() > 0) {
                return documentList.get(0);
            } else {
                return null;
            }
        }
6. Podemos usar el método auxiliar del paso 5 para recuperar un documento JSON de TodoItem por id. y deserializarlo para un POJO:
   
        @Override
        public TodoItem readTodoItem(String id) {
            // Retrieve the document by id using our helper method.
            Document todoItemDocument = getDocumentById(id);
   
            if (todoItemDocument != null) {
                // De-serialize the document in to a TodoItem.
                return gson.fromJson(todoItemDocument.toString(), TodoItem.class);
            } else {
                return null;
            }
        }
7. También podemos utilizar el DocumentClient para obtener una colección o lista de TodoItems con SQL:
   
        @Override
        public List<TodoItem> readTodoItems() {
            List<TodoItem> todoItems = new ArrayList<TodoItem>();
   
            // Retrieve the TodoItem documents
            List<Document> documentList = documentClient
                    .queryDocuments(getTodoCollection().getSelfLink(),
                            "SELECT * FROM root r WHERE r.entityType = 'todoItem'",
                            null).getQueryIterable().toList();
   
            // De-serialize the documents in to TodoItems.
            for (Document todoItemDocument : documentList) {
                todoItems.add(gson.fromJson(todoItemDocument.toString(),
                        TodoItem.class));
            }
   
            return todoItems;
        }
8. Hay muchas maneras de actualizar un documento con el DocumentClient. En nuestra aplicación de lista Todo, queremos poder alternar si se ha completado un TodoItem. Esto se logra actualizando el atributo "completado" dentro del documento:
   
        @Override
        public TodoItem updateTodoItem(String id, boolean isComplete) {
            // Retrieve the document from the database
            Document todoItemDocument = getDocumentById(id);
   
            // You can update the document as a JSON document directly.
            // For more complex operations - you could de-serialize the document in
            // to a POJO, update the POJO, and then re-serialize the POJO back in to
            // a document.
            todoItemDocument.set("complete", isComplete);
   
            try {
                // Persist/replace the updated document.
                todoItemDocument = documentClient.replaceDocument(todoItemDocument,
                        null).getResource();
            } catch (DocumentClientException e) {
                e.printStackTrace();
                return null;
            }
   
            return gson.fromJson(todoItemDocument.toString(), TodoItem.class);
        }
9. Por último, queremos la capacidad de eliminar un TodoItem de nuestra lista. Para ello, podemos utilizar el método auxiliar que hemos escrito anteriormente para recuperar el self-link y, a continuación, indicar al cliente que lo elimine:
   
        @Override
        public boolean deleteTodoItem(String id) {
            // Azure Cosmos DB refers to documents by self link rather than id.
   
            // Query for the document to retrieve the self link.
            Document todoItemDocument = getDocumentById(id);
   
            try {
                // Delete the document by self link.
                documentClient.deleteDocument(todoItemDocument.getSelfLink(), null);
            } catch (DocumentClientException e) {
                e.printStackTrace();
                return false;
            }
   
            return true;
        }

## <a id="Wire"></a>Paso 5: Reunión del resto del proyecto de desarrollo de aplicación en Java
Ahora que hemos terminado la parte divertida, todo lo que queda por hacer es crear una interfaz de usuario rápida y conectarla a nuestro DAO.

1. En primer lugar, empecemos por crear un controlador para llamar a nuestro DAO:
   
        public class TodoItemController {
            public static TodoItemController getInstance() {
                if (todoItemController == null) {
                    todoItemController = new TodoItemController(TodoDaoFactory.getDao());
                }
                return todoItemController;
            }
   
            private static TodoItemController todoItemController;
   
            private final TodoDao todoDao;
   
            TodoItemController(TodoDao todoDao) {
                this.todoDao = todoDao;
            }
   
            public TodoItem createTodoItem(@NonNull String name,
                    @NonNull String category, boolean isComplete) {
                TodoItem todoItem = TodoItem.builder().name(name).category(category)
                        .complete(isComplete).build();
                return todoDao.createTodoItem(todoItem);
            }
   
            public boolean deleteTodoItem(@NonNull String id) {
                return todoDao.deleteTodoItem(id);
            }
   
            public TodoItem getTodoItemById(@NonNull String id) {
                return todoDao.readTodoItem(id);
            }
   
            public List<TodoItem> getTodoItems() {
                return todoDao.readTodoItems();
            }
   
            public TodoItem updateTodoItem(@NonNull String id, boolean isComplete) {
                return todoDao.updateTodoItem(id, isComplete);
            }
        }
   
    En una aplicación más compleja, el controlador puede alojar lógica empresarial complicada encima del DAO.
2. A continuación, crearemos un servlet para enrutar las solicitudes HTTP al controlador:
   
        public class TodoServlet extends HttpServlet {
            // API Keys
            public static final String API_METHOD = "method";
   
            // API Methods
            public static final String CREATE_TODO_ITEM = "createTodoItem";
            public static final String GET_TODO_ITEMS = "getTodoItems";
            public static final String UPDATE_TODO_ITEM = "updateTodoItem";
   
            // API Parameters
            public static final String TODO_ITEM_ID = "todoItemId";
            public static final String TODO_ITEM_NAME = "todoItemName";
            public static final String TODO_ITEM_CATEGORY = "todoItemCategory";
            public static final String TODO_ITEM_COMPLETE = "todoItemComplete";
   
            public static final String MESSAGE_ERROR_INVALID_METHOD = "{'error': 'Invalid method'}";
   
            private static final long serialVersionUID = 1L;
            private static final Gson gson = new Gson();
   
            @Override
            protected void doGet(HttpServletRequest request,
                    HttpServletResponse response) throws ServletException, IOException {
   
                String apiResponse = MESSAGE_ERROR_INVALID_METHOD;
   
                TodoItemController todoItemController = TodoItemController
                        .getInstance();
   
                String id = request.getParameter(TODO_ITEM_ID);
                String name = request.getParameter(TODO_ITEM_NAME);
                String category = request.getParameter(TODO_ITEM_CATEGORY);
                boolean isComplete = StringUtils.equalsIgnoreCase("true",
                        request.getParameter(TODO_ITEM_COMPLETE)) ? true : false;
   
                switch (request.getParameter(API_METHOD)) {
                case CREATE_TODO_ITEM:
                    apiResponse = gson.toJson(todoItemController.createTodoItem(name,
                            category, isComplete));
                    break;
                case GET_TODO_ITEMS:
                    apiResponse = gson.toJson(todoItemController.getTodoItems());
                    break;
                case UPDATE_TODO_ITEM:
                    apiResponse = gson.toJson(todoItemController.updateTodoItem(id,
                            isComplete));
                    break;
                default:
                    break;
                }
   
                response.getWriter().println(apiResponse);
            }
   
            @Override
            protected void doPost(HttpServletRequest request,
                    HttpServletResponse response) throws ServletException, IOException {
                doGet(request, response);
            }
        }
3. Necesitaremos una interfaz de usuario web que mostrar al usuario. Vamos volver a escribir el archivo index.jsp que creamos anteriormente:
    ```html
        <html>
        <head>
          <meta http-equiv="Content-Type" content="text/html; charset=ISO-8859-1">
          <meta http-equiv="X-UA-Compatible" content="IE=edge;" />
          <title>Azure Cosmos DB Java Sample</title>
   
          <!-- Bootstrap -->
          <link href="//ajax.aspnetcdn.com/ajax/bootstrap/3.2.0/css/bootstrap.min.css" rel="stylesheet">
   
          <style>
            /* Add padding to body for fixed nav bar */
            body {
              padding-top: 50px;
            }
          </style>
        </head>
        <body>
          <!-- Nav Bar -->
          <div class="navbar navbar-inverse navbar-fixed-top" role="navigation">
            <div class="container">
              <div class="navbar-header">
                <a class="navbar-brand" href="#">My Tasks</a>
              </div>
            </div>
          </div>
   
          <!-- Body -->
          <div class="container">
            <h1>My ToDo List</h1>
   
            <hr/>
   
            <!-- The ToDo List -->
            <div class = "todoList">
              <table class="table table-bordered table-striped" id="todoItems">
                <thead>
                  <tr>
                    <th>Name</th>
                    <th>Category</th>
                    <th>Complete</th>
                  </tr>
                </thead>
                <tbody>
                </tbody>
              </table>
   
              <!-- Update Button -->
              <div class="todoUpdatePanel">
                <form class="form-horizontal" role="form">
                  <button type="button" class="btn btn-primary">Update Tasks</button>
                </form>
              </div>
   
            </div>
   
            <hr/>
   
            <!-- Item Input Form -->
            <div class="todoForm">
              <form class="form-horizontal" role="form">
                <div class="form-group">
                  <label for="inputItemName" class="col-sm-2">Task Name</label>
                  <div class="col-sm-10">
                    <input type="text" class="form-control" id="inputItemName" placeholder="Enter name">
                  </div>
                </div>
   
                <div class="form-group">
                  <label for="inputItemCategory" class="col-sm-2">Task Category</label>
                  <div class="col-sm-10">
                    <input type="text" class="form-control" id="inputItemCategory" placeholder="Enter category">
                  </div>
                </div>
   
                <button type="button" class="btn btn-primary">Add Task</button>
              </form>
            </div>
   
          </div>
   
          <!-- Placed at the end of the document so the pages load faster -->
          <script src="//ajax.aspnetcdn.com/ajax/jQuery/jquery-2.1.1.min.js"></script>
          <script src="//ajax.aspnetcdn.com/ajax/bootstrap/3.2.0/bootstrap.min.js"></script>
          <script src="assets/todo.js"></script>
        </body>
        </html>
    ```
4. Y por último, escriba algo de JavaScript del lado del cliente para vincular juntos la interfaz de usuario web y el servlet:
   
        var todoApp = {
          /*
           * API methods to call Java backend.
           */
          apiEndpoint: "api",
   
          createTodoItem: function(name, category, isComplete) {
            $.post(todoApp.apiEndpoint, {
                "method": "createTodoItem",
                "todoItemName": name,
                "todoItemCategory": category,
                "todoItemComplete": isComplete
              },
              function(data) {
                var todoItem = data;
                todoApp.addTodoItemToTable(todoItem.id, todoItem.name, todoItem.category, todoItem.complete);
              },
              "json");
          },
   
          getTodoItems: function() {
            $.post(todoApp.apiEndpoint, {
                "method": "getTodoItems"
              },
              function(data) {
                var todoItemArr = data;
                $.each(todoItemArr, function(index, value) {
                  todoApp.addTodoItemToTable(value.id, value.name, value.category, value.complete);
                });
              },
              "json");
          },
   
          updateTodoItem: function(id, isComplete) {
            $.post(todoApp.apiEndpoint, {
                "method": "updateTodoItem",
                "todoItemId": id,
                "todoItemComplete": isComplete
              },
              function(data) {},
              "json");
          },
   
          /*
           * UI Methods
           */
          addTodoItemToTable: function(id, name, category, isComplete) {
            var rowColor = isComplete ? "active" : "warning";
   
            todoApp.ui_table().append($("<tr>")
              .append($("<td>").text(name))
              .append($("<td>").text(category))
              .append($("<td>")
                .append($("<input>")
                  .attr("type", "checkbox")
                  .attr("id", id)
                  .attr("checked", isComplete)
                  .attr("class", "isComplete")
                ))
              .addClass(rowColor)
            );
          },
   
          /*
           * UI Bindings
           */
          bindCreateButton: function() {
            todoApp.ui_createButton().click(function() {
              todoApp.createTodoItem(todoApp.ui_createNameInput().val(), todoApp.ui_createCategoryInput().val(), false);
              todoApp.ui_createNameInput().val("");
              todoApp.ui_createCategoryInput().val("");
            });
          },
   
          bindUpdateButton: function() {
            todoApp.ui_updateButton().click(function() {
              // Disable button temporarily.
              var myButton = $(this);
              var originalText = myButton.text();
              $(this).text("Updating...");
              $(this).prop("disabled", true);
   
              // Call api to update todo items.
              $.each(todoApp.ui_updateId(), function(index, value) {
                todoApp.updateTodoItem(value.name, value.value);
                $(value).remove();
              });
   
              // Re-enable button.
              setTimeout(function() {
                myButton.prop("disabled", false);
                myButton.text(originalText);
              }, 500);
            });
          },
   
          bindUpdateCheckboxes: function() {
            todoApp.ui_table().on("click", ".isComplete", function(event) {
              var checkboxElement = $(event.currentTarget);
              var rowElement = $(event.currentTarget).parents('tr');
              var id = checkboxElement.attr('id');
              var isComplete = checkboxElement.is(':checked');
   
              // Toggle table row color
              if (isComplete) {
                rowElement.addClass("active");
                rowElement.removeClass("warning");
              } else {
                rowElement.removeClass("active");
                rowElement.addClass("warning");
              }
   
              // Update hidden inputs for update panel.
              todoApp.ui_updateForm().children("input[name='" + id + "']").remove();
   
              todoApp.ui_updateForm().append($("<input>")
                .attr("type", "hidden")
                .attr("class", "updateComplete")
                .attr("name", id)
                .attr("value", isComplete));
   
            });
          },
   
          /*
           * UI Elements
           */
          ui_createNameInput: function() {
            return $(".todoForm #inputItemName");
          },
   
          ui_createCategoryInput: function() {
            return $(".todoForm #inputItemCategory");
          },
   
          ui_createButton: function() {
            return $(".todoForm button");
          },
   
          ui_table: function() {
            return $(".todoList table tbody");
          },
   
          ui_updateButton: function() {
            return $(".todoUpdatePanel button");
          },
   
          ui_updateForm: function() {
            return $(".todoUpdatePanel form");
          },
   
          ui_updateId: function() {
            return $(".todoUpdatePanel .updateComplete");
          },
   
          /*
           * Install the TodoApp
           */
          install: function() {
            todoApp.bindCreateButton();
            todoApp.bindUpdateButton();
            todoApp.bindUpdateCheckboxes();
   
            todoApp.getTodoItems();
          }
        };
   
        $(document).ready(function() {
          todoApp.install();
        });
5. ¡Increíble! Ahora todo lo que queda por hacer es probar la aplicación. Ejecute la aplicación localmente y agregue algunos elementos Todo rellenando el nombre del elemento y la categoría y haciendo clic en **Agregar tarea**.
6. Una vez que aparece el elemento, puede actualizar si está completo. Para ello, alterne la casilla y haga clic en **Actualizar tareas**.

## <a id="Deploy"></a>Paso 6: Implementación de la aplicación Java en Azure WebSites
Azure WebSites consigue que la implementación de aplicaciones de Java sea tan sencilla como exportar su aplicación como un archivo WAR y cargarla mediante el control de código fuente (por ejemplo, Git) o FTP.

1. Para exportar la aplicación como un archivo WAR, haga clic con el botón derecho en el proyecto en **Explorador de proyectos**, haga clic en **Exportar** y, a continuación, haga clic en **Archivo WAR**.
2. En la ventana **Exportar WAR** , haga lo siguiente:
   
   * En el cuadro Proyecto web, escriba azure-documentdb-java-sample.
   * En el cuadro de destino, seleccione un destino para guardar el archivo WAR.
   * Haga clic en **Finalizar**
3. Ahora que tiene a mano un archivo WAR, simplemente puede cargarlo en el directorio **webapps** del sitio web de Azure. Para obtener instrucciones sobre cómo cargar el archivo, consulte [Adición de una aplicación Java a Azure App Service Web Apps](../app-service/web-sites-java-add-app.md).
   
    Una vez que se cargue el archivo WAR en el directorio webapps, el entorno de tiempo de ejecución detectará que lo ha agregado y lo cargará automáticamente.
4. Para ver el producto terminado, vaya a http://YOUR\_SITE\_NAME.azurewebsites.net/azure-java-sample/ y comience a agregar las tareas.

## <a id="GetProject"></a>Obtenga el proyecto desde GitHub
Todos los ejemplos de este tutorial se incluyen en el proyecto [todo](https://github.com/Azure-Samples/documentdb-java-todo-app) en GitHub. Para importar el proyecto todo en Eclipse, asegúrese de disponer del software y los recursos que aparecen en la sección [Requisitos previos](#Prerequisites) y haga lo siguiente:

1. Instale [Project Lombok](http://projectlombok.org/). Lombok se utiliza para generar constructores, captadores y establecedores en el proyecto. Una vez haya descargado el archivo lombok.jar, haga doble clic en él para instalarlo o instálelo desde la línea de comandos.
2. Si Eclipse está abierto, ciérrelo y reinícielo para cargar Lombok.
3. En Eclipse, en el menú **File** (Archivo), haga clic en **Import** (Importar).
4. En la ventana **Import** (Importar), haga clic en **Git**, en **Projects from Git** (Proyectos de Git) y luego en **Next** (Siguiente).
5. En la pantalla **Select Repository Source** (Seleccionar origen del repositorio), haga clic en **Clone URI** (Clonar URI).
6. En la pantalla **Source Git Repository** (Repositorio Git de origen), en el cuadro **URI**, escriba https://github.com/Azure-Samples/java-todo-app.git y luego haga clic en **Next** (Siguiente).
7. En la pantalla **Branch Selection** (Selección de rama), asegúrese de que está seleccionado **master** (principal) y luego haga clic en **Next** (Siguiente).
8. En la pantalla **Local Destination** (Destino local), haga clic en **Browse** (Examinar) para seleccionar una carpeta donde se pueda copiar el repositorio y luego haga clic en **Next** (Siguiente).
9. En la pantalla **Select a wizard to use for importing projects** (Seleccionar un asistente para usar en la importación de proyectos), asegúrese de que la opción **Import existing projects** (Importar proyectos existentes) esté seleccionada y luego haga clic en **Next** (Siguiente).
10. En la pantalla **Import Projects** (Proyectos de importación), anule la selección del proyecto **DocumentDB** y luego haga clic en **Finish** (Finalizar). El proyecto DocumentDB contiene el SDK de Java de Azure Cosmos DB, que se agregará como dependencia.
11. En **explorador de proyectos**, vaya a azure-documentdb-java-sample\src\com.microsoft.azure.documentdb.sample.dao\DocumentClientFactory.java y reemplace los valores HOST y MASTER_KEY por los de URI y PRIMARY KEY de la cuenta de Azure Cosmos DB y luego guarde el archivo. Para obtener más información, consulte el [paso 1. Creación de una cuenta de base de datos de Azure Cosmos DB](#CreateDB).
12. En **Project Explorer** (Explorador de proyectos), haga clic con el botón derecho en **azure-documentdb-java-sample**, haga clic en **Build Path** (Ruta de acceso de compilación) y luego haga clic en **Configure Build Path** (Configurar ruta de acceso de compilación).
13. En la pantalla **Java Build Path** (Ruta de compilación de Java), en el panel derecho, seleccione la pestaña **Bibliotecas** y luego haga clic en **Add External JARs** (Agregar JAR externos). Desplácese hasta la ubicación del archivo lombok.jar y haga clic en **Abrir** y, a continuación, en **Aceptar**.
14. Use el paso 12 para abrir la ventana **Propiedades** de nuevo y, a continuación, en el panel izquierdo haga clic en **Targeted Runtimes** (Tiempos de ejecución de destino).
15. En la pantalla **Targeted Runtimes** (Tiempos de ejecución de destino), haga clic en **Nuevo**, seleccione **Apache Tomcat v7.0** y luego haga clic en **Aceptar**.
16. Use el paso 12 para abrir de nuevo la ventana **Propiedades** y, a continuación, en el panel izquierdo, haga clic en **Project Facets** (Facetas del proyecto).
17. En la pantalla **Project Facets** (Facetas del proyecto), seleccione **Dynamic Web Module** (Módulo web dinámico) y **Java** y luego haga clic en **Aceptar**.
18. En la ficha **Servidores** en la parte inferior de la pantalla, haga clic con el botón derecho en **Tomcat v7.0 Server at localhost** (Tomcat v7.0 Server en localhost) y luego haga clic en **Add and Remove** (Agregar y quitar).
19. En la ventana **Agregar y quitar**, mueva **azure-documentdb-java-sample** al cuadro **Configurado** y luego haga clic en **Finalizar**.
20. En la pestaña **Servidor**, haga clic en **Tomcat v7.0 Server at localhost** (Tomcat v7.0 Server en localhost) y luego haga clic en **Reiniciar**.
21. En un explorador, vaya a http://localhost:8080/azure-documentdb-java-sample/ y comience a agregar a la lista de tareas. Tenga en cuenta que si ha cambiado los valores de puerto predeterminados, debe cambiar 8080 por el valor seleccionado.
22. Para implementar el proyecto en un sitio web de Azure, consulte el [paso 6. Implementación de la aplicación para Azure WebSites](#Deploy).

