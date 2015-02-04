<properties title="Build a web application with ASP.NET MVC using DocumentDB" pageTitle="Compilación de una aplicación web con ASP.NET MVC usando Base de datos de documentos | Azure" description="Obtenga información acerca de cómo usar la Base de datos de documentos para crear una aplicación web de lista de tareas pendientes. Almacenará y accederá a datos desde una aplicación web ASP.NET MVC hospedada en Azure.."  metaKeywords="NoSQL, DocumentDB, database, document-orientated database, JSON, account" services="documentdb"  solutions="data-management" documentationCenter=""  authors="ryancraw" manager="jhubbard" editor="cgronlun" videoId="" scriptId="" />


<tags ms.service="documentdb" ms.workload="data-services" ms.tgt_pltfrm="na" ms.devlang="dotnet" ms.topic="article" ms.date="08/19/2014" ms.author="ryancraw" />

<a name="_Toc395809351">Compilación de una aplicación web con ASP.NET MVC usando Base de datos de documentos</a>
=======================================================================================================
<a name="_Toc395637758"></a> <a name="_Toc389865467"></a> <a name="_Toc389828008">Información general</a>
==============================================================================================

<a name="_Toc395637759">Escenario</a>
------------------------------------

Para resaltar cómo los clientes pueden aprovechar eficazmente Base de datos de documentos de Azure para almacenar y consultar documentos JSON, en este documento se proporciona un tutorial integral para crear una aplicación web de Lista de tareas pendientes usando Base de datos de documentos de Azure.

Este tutorial muestra cómo puede usar el servicio Base de datos de documentos que ofrece Azure para almacenar y tener acceso a datos desde una aplicación web ASP.NET MVC hospedada en Azure. Asimismo, el tutorial supone que tiene cierta experiencia en el uso de ASP.NET MVC y Sitios web Azure.

Aprenderá a:

1\. Crear y aprovisionar una cuenta de la Base de datos de documentos

2\. Crear una aplicación ASP.NET MVC

3\. Conectarse y utilizar la Base de datos de documentos desde su aplicación web

4\. Implementar la aplicación web en Sitios web de Azure

Al seguir este tutorial, podrá crear una aplicación de administración de tareas basadas en web sencilla que permite crear, recuperar y completar tareas. Las tareas se almacenarán como documentos JSON en la Base de datos de documentos de Azure.

![Alt text](./media/documentdb-dotnet-application/image1.png)


<a name="_Toc395637760">Prerequisites</a>
==============================================================================================

Antes de seguir las instrucciones del presente artículo, debe asegurarse de tener instalados los siguientes elementos:

Git para Windows <http://www.git-scm.com/downloads>

Visual Studio 2013 (o [Visual Studio Express][], que es la versión gratuita)

SDK de Azure para .NET versión 2.3 o superior, disponible a través del [Instalador de plataforma web de Microsoft][]

Todas las capturas de pantalla de este documento se realizaron usando Visual Studio 2013 con Update 3 aplicado y el SDK de Azure para .NET versión 2.4. Si el sistema está configurado con versiones diferentes, es probable que las pantallas y las opciones no coincidan completamente, pero si cumple los requisitos previos mencionados, esta solución debe funcionar.

<a name="_Toc395637761">Creación de una cuenta de base de datos de la Base de datos de documentos</a>
==============================================================================================

Para aprovisionar una cuenta de base de datos de Base de datos de documentos en Azure, abra el Portal de administración de Azure y haga clic en el mosaico Galería de Azure de la página de inicio o en el signo "+" que se encuentra en la esquina inferior izquierda de la pantalla.

![Alt text](./media/documentdb-dotnet-application/image2.png)


Esto abrirá la Galería de Azure, donde podrá seleccionar entre muchos servicios de Azure disponibles. En la Galería, seleccione "Datos, almacenamiento y copia de seguridad" en la lista de categorías.

![Alt text](./media/documentdb-dotnet-application/image3.png)

Desde aquí, seleccione la opción de la Base de datos de documentos de Azure.

![Alt text](./media/documentdb-dotnet-application/image4.png)


A continuación, seleccione "Crear" en la parte inferior de la pantalla

![Alt text](./media/documentdb-dotnet-application/image5.png)

De esta manera se abrirá la hoja "Nueva base de datos de documentos" donde se puede especificar el nombre, la región, la escala, el grupo de recursos y otros ajustes para la cuenta nueva.

![Alt text](./media/documentdb-dotnet-application/image6.png)


Una vez haya terminado de suministrar los valores para la cuenta, haga clic en "Crear" y el proceso de aprovisionamiento comenzará a crear la cuenta de la base de datos.
Cuando se haya completado el proceso de aprovisionamiento debe aparecer una notificación en el área notificaciones del portal y el icono de la pantalla de inicio (si ha seleccionado para crear uno) cambiará para mostrar la acción completada.

![Alt text](./media/documentdb-dotnet-application/image7.png)


Cuando se haya completado el aprovisionamiento, al hacer clic en el mosaico de Base de datos de documentos de la pantalla de inicio, se abrirá la hoja principal para la cuenta de Base de datos de documentos recién creada.

![Alt text](./media/documentdb-dotnet-application/image8.png) 
![Alt text](./media/documentdb-dotnet-application/image9.png)


Mediante el botón "Claves", obtenga acceso a su URL de extremo y a la clave principal, cópielas en el portapapeles y consérvelas a mano, ya que utilizaremos estos valores en la aplicación web que crearemos a continuación.

Ahora veremos cómo crear una aplicación ASP.NET MVC nueva desde el principio. Para su referencia, la solución completa se puede descargar [aquí].

<a name="_Toc395637762">Creación de una aplicación ASP.NET MVC nueva</a>
==============================================================================================

En Visual Studio, haga clic en Archivo - Nuevo proyecto y seleccione la opción para crear una aplicación web ASP.NET MVC nueva.

![Alt text](./media/documentdb-dotnet-application/image10.png)


Seleccione dónde desea crear el proyecto, cómo desea llamar al proyecto (aquí se utiliza "todo") y haga clic en Aceptar.

![Alt text](./media/documentdb-dotnet-application/image11.png)


Si considera hospedar la aplicación en Azure, seleccione entonces el cuadro de la parte inferior derecha "Hospedar en la nube". Hemos seleccionado hospedar en la nube y ejecutar la aplicación hospedada en un sitio web de Azure. Al seleccionar esta opción, aprovisionaremos un sitio web de Azure para usted y facilitaremos mucho más la implementación de la aplicación de trabajo final. Si desea hospedar esto en otro lugar o no desea configurar Azure por adelantado, entonces simplemente desactive la opción "Hospedar en la nube".

Seleccione Aceptar y deje que Visual Studio haga su trabajo mediante scaffolding de la plantilla de ASP.NET MVC vacía. Si eligió hospedar esto en la nube, se mostrará al menos una pantalla adicional pidiéndole que inicie sesión en su cuenta de Azure y que proporcione algunos valores para su nuevo sitio web. Siga proporcionando todos estos valores de Azure y continúe. 

No he elegido un "Servidor de base de datos" porque aquí no usamos un servidor de Base de datos SQL de Azure. Vamos a crear una nueva cuenta de Base de datos de documentos de Azure más tarde en el portal de administración. 

![Alt text](./media/documentdb-dotnet-application/image11_1.png)

Después de que Visual Studio haya terminado de crear la aplicación MVC reutilizable, tiene una aplicación ASP.NET vacía que puede ejecutar de manera local.

Omitiremos la ejecución local porque estoy seguro que todos hemos visto la aplicación ASP.NET "Hola mundo". Vayamos directo a agregar la Base de datos de documentos a este proyecto y compilar la aplicación.

</h1>
<a name="_Toc395637767">Adición de una Base de datos de documentos a su proyecto</a>
=============================================================

Esto cubre la mayoría de las conexiones de ASP.NET MVC que necesitamos para esta solución. Vayamos ahora al objetivo real de este tutorial, la adición de Base de datos de documentos de Azure en nuestra aplicación web.

### 

### <a name="_Toc395637764">Instalación del paquete de NuGet</a>

El SDK .NET de Base de datos de documentos se empaqueta y distribuye como un paquete de NuGet. Con el administrador de paquetes de NuGet en Visual Studio (al que puede tener acceso al hacer clic con el botón secundario en el proyecto y seleccionar "Administrar paquetes de NuGet".

![Alt text](./media/documentdb-dotnet-application/image21.png)

Busque en línea "Base de datos de documentos de Azure" e instale el paquete. De esta manera se descarga e instala el paquete de Base de datos de documentos además de todas las dependencias, como Newtonsoft.Json.

**NOTA:** mientras el servicio sigue en vista previa, el paquete de NuGet se marca como "Versión preliminar", por lo que necesita incluir la opción "Incluir versión preliminar"; de lo contrario no encontrará el paquete. 

Alternativamente puede usar la consola de comandos de paquete para instalar el paquete escribiendo;

    Install-Package Microsoft.Azure.Documents.Client -Pre

Una vez instalado, la solución de Visual Studio debe parecerse a lo siguiente con dos referencias nuevas agregadas;

![Alt text](./media/documentdb-dotnet-application/image22.png)


<a name="_Toc395637763">Configuración de la aplicación ASP.NET MVC</a>
==================================================================

### 

### <a name="_Toc395637764">Adición de un modelo</a>

Comencemos creando la **M** en MVC, el modelo. En el Explorador de soluciones, haga clic con el botón secundario en la carpeta *Models* y, a continuación, haga clic en **Agregar** y, por último, en **Clase**.

![Alt text](./media/documentdb-dotnet-application/image12.png)

Asigne un nombre a la nueva Clase, **Item**, y, a continuación, agregue el siguiente código en esta clase nueva.

    public class Item
    {
        [JsonProperty(PropertyName="id")]
        public string Id { get; set; }
		
        [JsonProperty(PropertyName="name")]
        public string Name { get; set; }
		
        [JsonProperty(PropertyName = "desc")]
        public string Description { get; set; }
		
        [JsonProperty(PropertyName="isComplete")]
        public bool Completed { get; set; }    
    }

Todos los datos de la Base de datos de documentos se transmiten mediante la conexión y se almacenan como JSON. Para controlar la forma en la que JSON.NET serializa y deserializa los objetos, puede usar el atributo JsonProperty como se mostró en la clase Item que acabamos de crear. No **tiene** que hacer esto pero quería asegurarme de que mis propiedades siguieron las convenciones de nomenclatura de mezcla de mayúsculas y minúsculas de JSON. 

No solo puede controlar el formato del nombre de propiedad cuando va en JSON, sino que puede cambiar el nombre por completo de las propiedades de .NET como hice con la propiedad Description. 

Puede, si le gusta, usar objetos JsonConverter aquí así como controlar completamente la forma de controlar la serialización.  

Para que Visual Studio resuelva el atributo "JsonProperty" usado aquí, necesita agregar lo siguiente mediante declaración en la sección using del archivo de clases;

    using Newtonsoft.Json;

### <a name="_Toc395637765">Adición de un controlador</a>

Eso se encarga de M. Creemos ahora la **C** de MVC, una clase de controlador.
En el **Explorador de soluciones**, haga clic con el botón secundario en la carpeta *Controllers*, a continuación haga clic en **Agregar** y, por último, en **Controlador**.

![Alt text](./media/documentdb-dotnet-application/image13.png)

![Alt text](./media/documentdb-dotnet-application/image14.png)


En el cuadro de diálogo **Agregar scaffold**, haga clic en **Controlador MVC 5 - Vacío.** Haga clic en **Agregar**.

![Alt text](./media/documentdb-dotnet-application/image15.png)

Asigne un nombre al nuevo controlador, **ItemController.**

Visual Studio agregará ahora ItemController. El Explorador de soluciones debe ser similar a lo que se muestra a continuación.

![Alt text](./media/documentdb-dotnet-application/image16.png)

### <a name="_Toc395637766">Adición de vistas</a>

Por último, creemos la **V** de MVC, una vista.


#### Adición de vista de índice de elementos

Amplíe la carpeta ***Vistas*** en el Explorador de soluciones y ubique la carpeta Item (vacía) que Visual Studio ha creado cuando agregó *ItemController* anteriormente. Haga clic con el botón secundario en ***Item*** y elija Agregar - Vista.

![Alt text](./media/documentdb-dotnet-application/image17.png)

En el cuadro de diálogo "Agregar vista". Asigne el nombre "***Index***" a la vista, use la plantilla ***List***, seleccione ***Item (todo.Models)*** que creamos anteriormente como la clase y, por último, use ***~Views/Shared/_Layout.cshtml*** en la solución como la página de diseño.

![Alt text](./media/documentdb-dotnet-application/image18.png)


Después de que se hayan establecido todos estos valores, haga clic en Agregar y permita que Visual Studio cree la vista. Visual Studio creará una vista de plantillas. Después de esto, se abrirá el archivo cshtml que se creó. Podemos cerrar este documento en Visual Studio ya que volveremos a él más tarde.

#### Adición de una vista de elementos nuevos

De manera similar a la anterior, cree una vista nueva para crear elementos nuevos según el ejemplo que se muestra a continuación;

![Alt text](./media/documentdb-dotnet-application/image19.png)

#### Adición de una vista de edición de elementos

<a name="_Toc395888515"></a>
============================

Finalmente, agregue una última vista para editar un elemento como se hizo antes;

![Alt text](./media/documentdb-dotnet-application/image20.png)


Después de terminar, cierre los documentos cshtml en Visual Studio, ya que volveremos a estas vistas más tarde.

</h1>
<a name="_Toc395637769">Conexión de la Base de datos de documentos</a>
------------------------------------------------

### <a name="_Toc395637770">Lista de elementos incompletos</a>

Abra **ItemController** y quite todo el código presente en la clase (pero deje la clase) que Visual Studio agregó. Lo volveremos a compilar poco a poco usando la Base de datos de documentos.

Agregue el siguiente fragmento de código dentro de la clase ItemController que ahora está vacía;

    public ActionResult Index()
    {
        var items = DocumentDBRepository.GetIncompleteItems();
        return View(items);
    }

Este código también usa una clase de "pseudorrepositorio" llamada DocumentDBRepository que todavía tenemos que crear. Se trata realmente de la clase de aplicación auxiliar que contiene todo el código específico de Base de datos de documentos. Para los objetivos de este tutorial, no vamos a implementar una capa de acceso a datos completa con inserción de dependencias, y factorías y patrones de repositorio, como lo haría si fuera a crear una aplicación real. 
Para la finalidad de este tutorial vamos a poner toda la lógica del acceso a datos directamente en un proyecto para simplificar las cosas y centrarnos en las partes específicas de Base de datos de documentos.

Agregue una nueva clase al proyecto y llámela **DocumentDBRepository.**
Sustituya el código de la clase por lo siguiente.

    public static class DocumentDBRepository
    {
        private static string databaseId;
        private static String DatabaseId
        {
			get
			{
				if (string.IsNullOrEmpty(databaseId))
				{
					databaseId = ConfigurationManager.AppSettings["database"];
				}
			
				return databaseId;
			}
        }
			  
        private static string collectionId;
        private static String CollectionId
        {
			get
			{
				if (string.IsNullOrEmpty(collectionId))
				{
					collectionId = ConfigurationManager.AppSettings["collection"];
				}
			
				return collectionId;
			}
        }
				
        private static Database database;
        private static Database Database
        {
			get
			{
				if (database == null)
				{
					database = ReadOrCreateDatabase();
				}
				
				return database;
			}
        }
		
        private static DocumentCollection collection;
        private static DocumentCollection Collection
        {
			get
			{
				if (collection == null)
				{
					collection = ReadOrCreateCollection(Database.SelfLink);
				}
				
				return collection;
			}
        }
		
        private static DocumentClient client;
        private static DocumentClient Client
        {
            get
            {
                if (client == null)
                {
					string endpoint = ConfigurationManager.AppSettings["endpoint"];
					string authKey = ConfigurationManager.AppSettings["authKey"];
					Uri endpointUri = new Uri(endpoint);
					client = new DocumentClient(endpointUri, authKey);
                }
                
                return client;
            }
        }
    } 

#### 

Invierta algo de tiempo resolviendo todos los espacios de nombres en Visual Studio. Es fácil resolver todos los espacios de nombres en Visual Studio, siempre que el paquete de NuGet se haya instalado correctamente. Las referencias a los métodos ReadOrCreateDatabase y ReadOrCreateCollection permanecerán sin resolverse hasta que las agreguemos, lo que haremos a continuación. 

Aquí se usan dos llamadas de método para leer o crear bases de datos de Base de datos de documentos y colecciones de documentos.
Por tanto, agregue los dos métodos siguientes a esta clase.

    private static DocumentCollection ReadOrCreateCollection(string databaseLink)
    {
        var col = Client.CreateDocumentCollectionQuery(databaseLink)
                          .Where(c => c.Id == CollectionId)
                          .AsEnumerable()
                          .FirstOrDefault();

        if (col == null)
        {
            col = Client.CreateDocumentCollectionAsync(databaseLink, new DocumentCollection { Id = CollectionId }).Result;
        }
		
        return col;
    }

    private static Database ReadOrCreateDatabase()
    {
        var db = Client.CreateDatabaseQuery()
                        .Where(d => d.Id == DatabaseId)
                        .AsEnumerable()
                        .FirstOrDefault();
		
        if (db == null)
        {
            db = Client.CreateDatabaseAsync(new Database { Id = DatabaseId }).Result;
        }
		
        return db;
    }

</h1>

#### 

Esto se encarga de configurar la base de datos,  DocumentCollection, y de crear algún código para conectarse a Base de datos de documentos a través de DocumentClient. 

Estamos leyendo algunos valores de la configuración; por tanto, abra **web.config** y agregue las siguientes líneas debajo de la sección
<AppSettings\>;

    <add key="endpoint" value="enter you endpoint url from the Azure Management Portal"/>
    <add key="authKey" value="enter one of the keys from the Azure Management Portal"/>
    <add key="database" value="ToDoList"/>
    <add key="collection" value="Items"/>


</h4>

Ahora agregaremos código que haga el trabajo. 
Lo primero que queremos hacer con una aplicación Lista de tareas pendientes es mostrar los elementos incompletos; el siguiente método lo hace para copiarlo y pegarlo en cualquier lugar de la clase de repositorio con el que estamos trabajando;
    
    public static List<Item> GetIncompleteItems()
    {
        return Client.CreateDocumentQuery<Item>(Collection.DocumentsLink)
                .Where(d => !d.Completed)
                .AsEnumerable()
                .ToList<Item>();
    }
    
La referencia a *CreateDocumentQuery* se resolverá después de que necesite agregar manualmente la siguiente directiva using;

    using Microsoft.Azure.Documents.Linq;

En este punto, la solución debe ser capaz de compilar sin errores.

Si ejecuta la aplicación ahora, irá al controlador de inicio y la vista de índice de ese controlador. Este es el comportamiento predeterminado para el proyecto de plantillas MVC que seleccionamos al comienzo, pero no queremos eso. Cambiemos el enrutamiento en esta aplicación MVC para modificar este comportamiento.

Abra ***App\_Start\RouteConfig.cs***, busque la línea que empieza con "defaults:" y cámbiela para que se parezca a lo siguiente;

    defaults: new { controller = "Item", action = "Index", id = UrlParameter.Optional }

Esto indica ahora a ASP.NET MVC que si no ha especificado un valor en la dirección URL para controlar el comportamiento de enrutamiento que, en lugar de "Home", usa "Item" como controlador y el usuario Index como vista.
Si ejecuta la aplicación, llamará a su **ItemController** y devolverá los resultados del método **GetIncompleteItems** en la vista Views\Item\Index. 

Si crea y ejecuta este proyecto ahora, deberá ver algo parecido a esto;    

![Alt text](./media/documentdb-dotnet-application/image23.png)

### <a name="_Toc395637771">Adición de elementos</a>

Pongamos algunos elementos en nuestra base de datos de modo que tengamos algo más que una cuadrícula vacía que ver.

Ya tenemos una vista para crear y un botón en la vista Index que llevará al usuario a la vista de creación. Agreguemos algo de código al controlador y al repositorio para mantener el registro en la Base de datos de documentos.

Abra ***ItemController.cs*** y agregue el siguiente fragmento de código que es cómo ASP.NET MVC sabe lo que debe hacer para la acción de creación, en este caso solo representa la vista de Create.cshtml asociada que se creó anteriormente.

    public ActionResult Create()
    { 
	return View(); 
    }

Ahora necesitamos un poco más de código en este controlador que acepte el envío desde la vista de creación.

Agregue el siguiente bloque de código que le indica a ASP.NET MVC lo que debe hacer con un formulario POST para este controlador.
	
    [HttpPost]
    [ValidateAntiForgeryToken]
    public async Task<ActionResult> Create([Bind(Include = "Id,Name,Description,Completed")] Item item)  
    {
		if (ModelState.IsValid)  
		{  
		    await repo.CreateDocument(item);  
		    return RedirectToAction("Index");  
		}   
    	return View(item);   
    }

**Nota de seguridad**: El atributo [ValidateAntiForgeryToken] se usa aquí para ayudarle a proteger esta aplicación contra ataques de falsificación de solicitud entre sitios. Es más que solo agregar este atributo, sus vistas también necesitan trabajar con este token antifalsificación. Para obtener más información acerca del tema, así como ejemplos de cómo implementarlo correctamente, consulte [Prevención de la falsificación de solicitud entre sitios][]. El código fuente en el vínculo de descarga al final del artículo tiene la implementación completa.

**Nota de seguridad**: También usamos el atributo [Bind] en el parámetro de método para ayudar a proteger contra ataques de publicación en exceso. Para obtener más información, consulte [Operaciones CRUD básicas en ASP.NET MVC][].

Ahora que está colocado, el controlador de elementos pasará el elemento (de forma segura), desde el formulario hasta el método de repositorio CreateDocument de nuestra clase; por tanto, agregue el siguiente método a su clase DocumentDBRepository.

    public static async Task<Document> CreateItemAsync(Item item)
    {
        return await Client.CreateDocumentAsync(Collection.SelfLink, item);
    }

Este método simplemente toma un objeto que se le haya pasado y lo mantiene en la base de datos de documentos.
Esto finaliza el código necesario para agregar elementos nuevos en nuestra base de datos.


### <a name="_Toc395637772">Edición de elementos</a>

Lo último que nos queda por hacer tiene que ver con la capacidad de editar elementos en la base de datos y marcarlos como completos a medida que completamos las tareas. Como ocurre con la adición, la vista para editar ya se agregó, por lo que solo necesitamos volver a agregar algo de código adicional a nuestro controlador y a la clase DocumentDBRepository.

Agregue lo siguiente a la clase ItemController;

    [HttpPost]
    [ValidateAntiForgeryToken]
    public async Task<ActionResult> Edit([Bind(Include = "Id,Name,Description,Completed")] Item item)
    {
        if (ModelState.IsValid)
        {
            await DocumentDBRepository.UpdateItemAsync(item);
            return RedirectToAction("Index");
        }

        return View(item);
    }
	
    public ActionResult Edit(string id)
    {
        if (id == null)
        {
            return new HttpStatusCodeResult(HttpStatusCode.BadRequest);
        }

        Item item = (Item)DocumentDBRepository.GetItem(id);
        if (item == null)
        {
            return HttpNotFound();
        }

        return View(item);
    }

El primer método controla el Http Get que ocurrirá cuando el usuario haga clic en el vínculo de edición en la vista de índice. Este método captura un documento de la Base de datos de documentos y lo pasa a la vista de edición.

A continuación, la vista de edición realizará un Http Post en IndexController. 
El segundo método que agregamos controla el paso del objeto actualizado a través de la Base de datos de documentos para que persista en la base de datos.

Agregue lo siguiente a la clase DocumentDBRepository;

    public static Item GetItem(string id)
    {
        return Client.CreateDocumentQuery<Item>(Collection.DocumentsLink)
                    .Where(d => d.Id == id)
                    .AsEnumerable()
                    .FirstOrDefault();
    }
	
    public static async Task<Document> UpdateItemAsync(Item item)
    {
        Document doc = Client.CreateDocumentQuery(Collection.DocumentsLink)
                            .Where(d => d.Id == item.Id)
                            .AsEnumerable()
                            .FirstOrDefault();

        return await Client.ReplaceDocumentAsync(doc.SelfLink, item);
    }

El primero de estos dos métodos captura un elemento de la Base de datos de documentos que pasa de vuelta al ItemController y luego continúa hasta la vista de edición.

El segundo de los dos métodos que acabamos de agregar reemplaza el documento en la Base de datos de documentos con la versión del documento que se pasó desde el ItemController.

Y eso es todo lo que necesitamos para ejecutar nuestra aplicación, enumerar elementos incompletos, agregar elementos nuevos y, por último, editar elementos.

</h3>
<a name="_Toc395637773">Ejecución de la aplicación de forma local</a>
=========================================================

### 

Para probar la aplicación en la máquina local, presione F5 en Visual Studio y se compila la aplicación e inicia un explorador con la página de cuadrícula vacía que vimos antes:

![Alt text](./media/documentdb-dotnet-application/image24.png)

1\.Use los campos que se proporcionan para el elemento, el nombre del elemento y la categoría para escribir información y, a continuación, haga clic en el vínculo **"Crear nuevo"** y proporcione algunos valores. Deje la casilla "Completado" sin seleccionar; de lo contrario, el elemento nuevo se agregará en estado completado y no aparecerá en la lista inicial.

![Alt text](./media/documentdb-dotnet-application/image25.png)

Si presiona Crear, se le redirigirá a la página de índice y su elemento debe aparecer en la lista.

![Alt text](./media/documentdb-dotnet-application/image26.png)

Puede agregar algunos elementos más en su lista Todo.

2\.Haga clic en "Editar" junto a un elemento de la lista y llegará a la vista de edición donde puede actualizar cualquier propiedad de su objeto, incluida la marca "Completado". Esto efectivamente marca el elemento como completo y lo quitará de la lista de tareas incompletas.

![Alt text](./media/documentdb-dotnet-application/image27.png)

3\.Para completar una tarea, simplemente marque la casilla y, a continuación, haga clic en **Guardar.** Se le redirigirá a la página de lista donde el elemento no aparecerá más en ella.

</h3>
<a name="_Toc395637774">Implementación de la aplicación en Sitios web de Azure</a>
==============================================================================================

### 

Ahora que tiene la aplicación completa funcionando correctamente con Base de datos de documentos, vamos a implementar esto en sitios web Azure.

Si seleccionó "Hospedar en la nube" cuando creamos el proyecto vacío ASP.NET MVC, Visual Studio realmente facilita la tarea y hace gran parte del trabajo por nosotros. Para publicar esta aplicación, todo lo que tiene que hacer es hacer clic con el botón secundario en el proyecto en el Explorador de soluciones (asegúrese de que no se está ejecutando localmente) y seleccione Publicar.

![Alt text](./media/documentdb-dotnet-application/image28.png)

Todo debería estar configurado según sus credenciales; de hecho, el sitio web ya se ha creado en Azure en la "Dirección URL de destino" que se muestra. Solamente tiene que hacer clic en **Publicar**.

![Alt text](./media/documentdb-dotnet-application/image29.png)

En pocos segundos, Visual Studio terminará de publicar su aplicación web y ejecutará un explorador donde podrá ver su útil trabajo ejecutándose en Azure.

</h3>



<a name="_Toc395637775">Conclusión</a>
======================================

### 

¡Enhorabuena! Acaba de compilar su primera aplicación ASP.NET MVC usando Base de datos de documentos de Azure y la ha publicado en Sitios web Azure. El código fuente para la aplicación completa, incluida la funcionalidad Detalles y eliminar que omitimos aquí, puede descargarse [aquí][].


[\*]: https://microsoft.sharepoint.com/teams/DocDB/Shared%20Documents/Documentation/Docs.LatestVersions/PicExportError
[Visual Studio Express]: http://www.visualstudio.com/es-es/products/visual-studio-express-vs.aspx
[Instalador de plataforma web de Microsoft]: http://www.microsoft.com/web/downloads/platform.aspx
[aquí]: http://go.microsoft.com/fwlink/?LinkID=509838&clcid=0x409
[Prevención de la falsificación de solicitud entre sitios]: http://go.microsoft.com/fwlink/?LinkID=517254
[Operaciones CRUD básicas en ASP.NET MVC]: http://go.microsoft.com/fwlink/?LinkId=317598

<!--HONumber=27-->
