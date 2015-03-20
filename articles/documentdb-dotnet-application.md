<properties 
	pageTitle="Compilación de una aplicación web con ASP.NET MVC usando Base de datos de documentos | Azure" 
	description="Aprenda a usar Base de datos de documentos con .NET para crear una aplicación web de lista de tareas pendientes. Podrá almacenar y tener acceso a datos desde una aplicación web de ASP.NET MVC hospedada en Sitios web Azure." 
	services="documentdb" 
	documentationCenter="" 
	authors="ryancrawcour" 
	manager="jhubbard" 
	editor="cgronlun"/>


<tags 
	ms.service="documentdb" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="dotnet" 
	ms.topic="hero-article" 
	ms.date="02/26/2015" 
	ms.author="ryancraw"/>

# <a name="_Toc395809351"></a>Crear una aplicación web con ASP.NET MVC mediante Base de datos de documentos

Para resaltar cómo pueden aprovechar eficazmente Base de datos de documentos de Azure para almacenar y consultar documentos JSON, este artículo proporciona un tutorial completo que muestra cómo crear una aplicación de web de lista todo mediante Base de datos de documentos de Azure. Las tareas se almacenarán como documentos JSON en la Base de datos de documentos de Azure.

![Screen shot of the todo list web application created by this tutorial](./media/documentdb-dotnet-application/image1.png)

Este tutorial muestra cómo utilizar el servicio de Base de datos de documentos proporcionado por Azure para almacenar y tener acceso a datos desde una aplicación web de ASP.NET MVC hospedada en Azure.

> [AZURE.TIP] En este tutorial se supone que tiene experiencia previa con ASP.NET MVC y Sitios web Azure. Si está familiarizado con ASP.NET o las [herramientas de requisitos previos](#_Toc395637760), se recomienda descargar el proyecto de tutorial [todo](https://github.com/Azure/azure-documentdb-net/tree/master/tutorials/todo) completo de [GitHub](https://github.com/Azure/azure-documentdb-net) y crearlo mediante las [instrucciones al final de este artículo](#GetProject). Una vez compilado, puede revisar el artículo para obtener información sobre el código en el contexto del proyecto.

## <a name="_Toc395637760"></a>Requisitos previos

Antes de seguir las instrucciones del presente artículo, debe asegurarse de tener lo siguiente:

- Una cuenta de Azure activa. En caso de no tener ninguna, puede crear una cuenta de evaluación gratuita en tan solo unos minutos. Para obtener más información, consulte [Evaluación gratuita de Azure.](../../pricing/free-trial/)
- [Visual Studio 2013](http://www.visualstudio.com/) o superior, o [Visual Studio Express], que es la versión gratuita.
- Azure SDK para .NET versión 2.3 o posterior, disponible a través del [instalador de plataforma Web de Microsoft][].

Todas las capturas de pantalla en este artículo se han tomado con Visual Studio 2013 con Update 3 aplicado y el SDK de Azure para la versión 2.4 de .NET. Si el sistema está configurado con versiones diferentes, es probable que las pantallas y las opciones no coincidan completamente, pero si cumple los requisitos previos mencionados, esta solución debe funcionar.

## <a name="_Toc395637761"></a>Paso 1: Creación de una cuenta de base de datos de la Base de datos de documentos

Comenzaremos por crear una cuenta de Base de datos de documentos. Si ya tiene una cuenta, puede ir a [Crear una nueva aplicación ASP.NET MVC](#_Toc395637762).

[AZURE.INCLUDE [documentdb-create-dbaccount](../includes/documentdb-create-dbaccount.md)]

[AZURE.INCLUDE [documentdb-keys](../includes/documentdb-keys.md)]

<br/>
Ahora veremos cómo crear una aplicación ASP.NET MVC nueva desde el principio. 

## <a name="_Toc395637762"></a>Paso 2: Creación de una aplicación ASP.NET MVC nueva

Ahora vamos a crear el nuevo proyecto ASP.NET.

1. En Visual Studio, en el menú **Archivo**, seleccione **Nuevo** y, a continuación, haga clic en **Proyecto**.

   	Aparecerá el cuadro de diálogo **Nuevo proyecto**.
2. En el panel **Tipos de proyecto**, expanda **Plantillas**, **Visual C#**, **Web** y, a continuación, seleccione **Aplicación web ASP.NET**.

  	![Screen shot of the New Project dialog box with the ASP.NET Web Application project type highlighted](./media/documentdb-dotnet-application/image10.png)

3. En el cuadro **Nombre**, escriba el nombre del proyecto. Este tutorial utiliza el nombre "todo". 
4. Haga clic en **Examinar** para navegar hasta la carpeta donde desea crear el proyecto y, a continuación, haga clic en **Aceptar**.

  	Aparece el cuadro de diálogo **Nuevo proyecto ASP.NET**.

  	![Screen shot of the New ASP.NET Project dialog box with the MVC template highlighted and the Host in the cloud box checked](./media/documentdb-dotnet-application/image11.png)

5. En el panel Plantillas, seleccione **MVC**.
6. Si va a hospedar la aplicación en Azure, seleccione **Host en la nube** en la esquina inferior derecha para que Azure hospede la aplicación. Hemos seleccionado hospedar en la nube y ejecutar la aplicación hospedada en Sitio web Azure. Al seleccionar esta opción se aprovisionará previamente Sitio web Azure para usted y le será mucho más fácil implementar la aplicación de trabajo final. Si desea hospedar en otro sitio o no desea configurar Azure por adelantado, simplemente borre **Host en la nube**.
7. Haga clic en **Aceptar** y deje que Visual Studio realice la tarea de scaffolding de la plantilla ASP.NET MVC vacía. 
8. Si decide alojarla en la nube, verá al menos una pantalla adicional que le solicitará que inicie sesión en su cuenta de Azure y proporcione valores para el nuevo sitio web. Proporcione todos los valores adicionales y continúe. 

  	No he elegido "Servidor de base de datos" aquí porque no estamos usando un servidor de Base de datos SQL de Azure  aquí, vamos a crear una nueva cuenta de Base de datos de documentos de Azure más adelante en el Portal de vista previa de Azure. 

  	![Screen shot of the Configure Microsoft Azure Website dialog box](./media/documentdb-dotnet-application/image11_1.png)

9. Después de que Visual Studio haya terminado de crear la aplicación MVC reutilizable, tiene una aplicación ASP.NET vacía que puede ejecutar de manera local.

Omitiremos la ejecución del proyecto localmente, ya que estoy seguro de que todos hemos visto la aplicación "Hola a todos" de ASP.NET. Vayamos directo a agregar la Base de datos de documentos a este proyecto y compilar la aplicación.

## <a name="_Toc395637767"></a>Paso 3: Adición de Base de datos de documentos al proyecto

Se encarga de la mayoría de los mecanismos de ASP.NET MVC que necesitamos para
esta solución. Vayamos ahora al objetivo real de este tutorial, la adición de Base de datos de documentos de Azure en nuestra aplicación web.

1. El SDK .NET de Base de datos de documentos se empaqueta y distribuye como un paquete de NuGet. Para obtener el paquete NuGet en Visual Studio, utilice el Administrador de paquetes de NuGet en Visual Studio haciendo clic con el botón secundario en el proyecto en el **Explorador de soluciones** y, a continuación, haciendo clic en **Administrar paquetes de NuGet**.

  	Aparecerá el cuadro de diálogo **Administrar paquetes de NuGet**.

  	![Sreen shot of the right-click options for the project in Solution Explorer, with Manage NuGet Packages highlighted.](./media/documentdb-dotnet-application/image21.png)

2. En el cuadro **Buscar en línea**, escriba "Base de datos de documentos de Azure". En los resultados, instale el paquete **Bibliotecas de cliente de Base de datos de documentos de Microsoft Azure**. De esta manera se descarga e instala el paquete de Base de datos de documentos además de todas las dependencias, como Newtonsoft.Json.

  	> [AZURE.NOTE] Mientras el servicio está en vista previa, el paquete NuGet se marca como "Preliminar", por lo que debe incluir la opción "Incluir versión preliminar"; de lo contrario, no encontrará el paquete. 

  	También puede usar la consola de comandos de paquete para instalar el paquete mediante el comando siguiente.

    	Install-Package Microsoft.Azure.Documents.Client -Pre

3. Una vez instalado el paquete, la solución de Visual Studio debe ser similar a lo siguiente con dos nuevas referencias agregadas, Microsoft.Azure.Documents.Client y Newtonsoft.Json.

  	![Sreen shot of the two references added to the project in Solution Explorer.](./media/documentdb-dotnet-application/image22.png)


## <a name="_Toc395637763"></a>Paso 4: Configuración de la aplicación ASP.NET MVC
 
Ahora vamos a agregar a esta aplicación de MVC modelos, vistas y controladores:

- [Adición de un modelo](#_Toc395637764).
- [Adición de un controlador](#_Toc395637765).
- [Adición de vistas](#_Toc395637766).


### <a name="_Toc395637764"></a>Adición de un modelo

Comencemos creando la **M** en MVC, el modelo. 

1. En el Explorador de soluciones, haga clic con el botón derecho en la carpeta **Modelos**, haga clic en **Agregar** y, a continuación, haga clic en **Clase**.

  	Aparecerá el cuadro de diálogo **Agregar nuevo elemento**.

2. Ponga un nombre a la nueva clase (**Elemento**) y, a continuación, agregue el código siguiente en el nuevo archivo Item.cs.

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

	Todos los datos de Base de datos de documentos se transmiten mediante la conexión y se almacenan como JSON. Para controlar la forma en la que JSON.NET serializa y deserializa los objetos, puede usar el atributo **JsonProperty** como se mostró en la clase de **elemento** que acabamos de crear. No **tiene** que hacer esto pero quería asegurarme de que mis propiedades siguen las convenciones de nomenclatura de mezcla de mayúsculas y minúsculas de JSON. 

	No solo puede controlar el formato del nombre de propiedad cuando va en JSON, sino que puede cambiar el nombre por completo de las propiedades de .NET como hice con la propiedad **Description**. 

	Puede, si así lo quiere, usar objetos **JsonConverter** aquí, así como controlar completamente la forma de controlar la serialización.  

3. Para que Visual Studio resuelva el atributo **JsonProperty** usado aquí, necesita agregar lo siguiente mediante la instrucción en la sección using del archivo de clase.

    	using Newtonsoft.Json;

### <a name="_Toc395637765"></a>Adición de un controlador

Se encarga de **M**. Creemos ahora la **C** de MVC, una clase de controlador.

1. En el **Explorador de soluciones**, haga clic con el botón secundario en la carpeta **Controladores** y, a continuación, haga clic en **Agregar** y, por último, en **Controlador**.

    Aparecerá el cuadro de diálogo **Agregar scaffold**.

2. Seleccione **MVC 5 Controller - Empty** y, a continuación, haga clic en **agregar**.

	![Screen shot of the Add Scaffold dialog box with the MVC 5 Controller - Empty option highlighted](./media/documentdb-dotnet-application/image14.png)

3. Asigne un nombre al nuevo controlador, **ItemController.**

	![Screen shot of the Add Controller dialog box](./media/documentdb-dotnet-application/image15.png)

Una vez creado el archivo, la solución de Visual Studio debe ser similar a lo siguiente con el nuevo archivo ItemController.cs en el **Explorador de soluciones**.

![Screen shot of Solution Explorer with the new ItemController.cs file and Item.cs file highlighted](./media/documentdb-dotnet-application/image16.png)

### <a name="_Toc395637766"></a>Adición de vistas

Por último, vamos a crear la **V** de MVC, las vistas.

- [Adición de una vista de índice de elementos](#AddItemIndexView).
- [Adición de una vista de elementos nuevos](#AddNewIndexView).
- [Adición de una vista de edición de elementos](#_Toc395888515).


#### <a name="AddItemIndexView"></a>Adición de una vista de índice de elementos

1. En el **Explorador de soluciones**, expanda la carpeta ***Vistas***, haga con el botón derecho en la carpeta **Elemento** vacía que ha creado Visual Studio para usted cuando agregó **ItemController** anteriormente, haga clic en **Agregar** y, a continuación, haga clic en **Vista**.

![Screen shot of Solution Explorer showing the Item folder with the Add View commands highlighted](./media/documentdb-dotnet-application/image17.png)

2. En el cuadro de diálogo **Agregar vista**, realice lo siguiente:
	- En el cuadro **Nombre de vista**, escriba ***Índice***.
	- En el cuadro **Plantilla**, seleccione ***Lista***.
	- En el cuadro **Clase de modelo**, seleccione ***Elemento (todo.Models)***.
	- En el cuadro de página de diseño, escriba ***~/Views/Shared/_Layout.cshtml***.
	- Haga clic en **Add**.
	
![Screen shot showing the Add View dialog box](./media/documentdb-dotnet-application/image18.png)

3. Después de que se hayan establecido todos estos valores, haga clic en **Agregar** y permita que Visual Studio cree la vista para usted. Visual Studio creará una vista de plantillas. Después de esto, se abrirá el archivo cshtml que se creó. Podemos cerrar este archivo en Visual Studio, ya que volveremos a él más tarde.

#### <a name="AddNewIndexView"></a>Adición de una vista de elementos nuevos

De forma parecida a cómo se crea una vista de **índice de elementos**, crearemos ahora una nueva vista para crear nuevos **elementos**.

En el cuadro de diálogo **Agregar vista**, realice lo siguiente:

- En el cuadro **Nombre de vista**, escriba ***Crear***.
- En el cuadro **Plantilla**, seleccione ***Crear***.
- En el cuadro **Clase de modelo**, seleccione ***Elemento (todo.Models)***.
- En el cuadro de página de diseño, escriba ***~/Views/Shared/_Layout.cshtml***.
- Haga clic en **Add**.

![Screen shot showing the Add View dialog box](./media/documentdb-dotnet-application/image19.png)

#### <a name="_Toc395888515"></a>Adición de una vista de edición de elementos

Finalmente, agregue una última vista para editar un **elemento** como se hizo anteriormente.


En el cuadro de diálogo **Agregar vista**, realice lo siguiente:

- En el cuadro **Nombre de vista**, escriba ***Editar***.
- En el cuadro **Plantilla**, seleccione ***Editar***.
- En el cuadro **Clase de modelo**, seleccione ***Elemento (todo.Models)***.
- Seleccione **Crear como una vista parcial**.
- Haga clic en **Add**.

![Screen shot showing the Add View dialog box](./media/documentdb-dotnet-application/image20.png)

Una vez hecho esto, cierre los documentos cshtml en Visual Studio, ya que volveremos a estas vistas más tarde.

## <a name="_Toc395637769"></a>Paso 5: Conexión de la Base de datos de documentos

En esta sección, agregaremos código para controlar lo siguiente:

- [Lista de elementos incompletos](#_Toc395637770).
- [Adición de elementos](#_Toc395637771).
- [Edición de elementos](#_Toc395637772).

### <a name="_Toc395637770"></a>Lista de elementos incompletos

1. Abra **ItemController** y quite todo el código presente en la clase (pero deje la clase) que agregó Visual Studio. Lo volveremos a compilar poco a poco usando la Base de datos de documentos.

2. Agregue el siguiente fragmento de código en la clase **ItemController** que ahora está vacía;

    	public ActionResult Index()
    	{
			var items = DocumentDBRepository<Item>.GetItems(d => !d.Completed);
			return View(items);
    	}

	Este código también usa una clase de "pseudorrepositorio" llamada **DocumentDBRepository** que todavía tenemos que crear. Se trata realmente de la clase de aplicación auxiliar que contiene todo el código específico de Base de datos de documentos. Para los objetivos de este tutorial, no vamos a implementar una capa de acceso a datos completa con inserción de dependencias, y factorías y patrones de repositorio, como lo haría si fuera a crear una aplicación real. 
	Para la finalidad de este tutorial vamos a poner toda la lógica del acceso a datos directamente en un proyecto para simplificar las cosas y centrarnos en las partes específicas de Base de datos de documentos.

3. Agregue una nueva clase al proyecto y póngale el nombre de **DocumentDBRepository.** 
4. Reemplace el código de la clase **DocumentDBRepository** por el siguiente.

    	public static class DocumentDBRepository<T>
    	{
	       using Microsoft.Azure.Documents; 
	       using Microsoft.Azure.Documents.Client; 
	       using Microsoft.Azure.Documents.Linq; 
		
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

5. Dedique tiempo a resolver todos los espacios de nombres en Visual Studio, que debe incluir lo siguiente mediante directivas al archivo DocumentDBRepository.cs.

		using Microsoft.Azure.Documents;
		using Microsoft.Azure.Documents.Client;
		using Microsoft.Azure.Documents.Linq;
		using System.Configuration;

 	Es fácil resolver todos los espacios de nombres en Visual Studio, siempre que el paquete de NuGet se haya instalado correctamente. Las referencias a los métodos **ReadOrCreateDatabase** y **ReadOrCreateCollection** permanecerán sin resolverse hasta que las agreguemos, lo que haremos a continuación.
 
6. Aquí se usan dos llamadas de método para leer o crear bases de datos de Base de datos de documentos y colecciones de documentos. Agregue los dos métodos siguientes para la clase **DocumentDBRepository**.

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

	Este código se encarga de configurar la base de datos y [**DocumentCollection**](http://msdn.microsoft.com/library/azure/microsoft.azure.documents.documentcollection.aspx), y de crear algún código para conectarse a Base de datos de documentos a través de [**DocumentClient**](http://msdn.microsoft.com/library/azure/microsoft.azure.documents.client.documentclient.aspx). 

7. Estamos leyendo algunos valores de la configuración; por tanto, abra el archivo **Web.config** y agregue las siguientes líneas debajo de la sección `<AppSettings>`.
	
    	<add key="endpoint" value="enter you endpoint url from the Azure Management Portal"/>
    	<add key="authKey" value="enter one of the keys from the Azure Management Portal"/>
    	<add key="database" value="ToDoList"/>
    	<add key="collection" value="Items"/>
	
8. Ahora, actualice los valores mediante la hoja Claves del Portal de administración de Azure. Utilice el valor **URI** de la hoja Claves como el valor de la clave de extremo y utilice el valor **PRIMARY KEY** de la hoja Claves como el valor de la clave authKey.
	
	Ahora agregaremos código que haga el trabajo. 	

9. Lo primero que queremos poder hacer con una aplicación de lista todo es mostrar los elementos incompletos.  El método siguiente hace esa operación por usted, así que cópielo y péguelo en cualquier lugar dentro de la clase **DocumentDBRepository**.

	    public static IEnumerable<T> GetItems(Expression<Func<T, bool>> predicate) 
	    {
			return Client.CreateDocumentQuery<T>(Collection.DocumentsLink) 
				.Where(predicate) 
				.AsEnumerable(); 
		} 
	    
	En este punto, la solución debe ser capaz de compilar sin errores.

	Si ejecuta la aplicación ahora, irá al **controlador de inicio** y a la vista de **índice** de ese controlador. Este es el comportamiento predeterminado para el proyecto de plantillas MVC que seleccionamos al comienzo, pero no queremos eso. Cambiemos el enrutamiento en esta aplicación MVC para modificar este comportamiento.

11. Abra ***App\_Start\RouteConfig.cs*** y busque la línea que empieza con "defaults:" y cámbiela para que se parezca a lo siguiente.

    	defaults: new { controller = "Item", action = "Index", id = UrlParameter.Optional }

	Esto indica ahora a ASP.NET MVC que si no ha especificado un valor en la dirección URL para controlar el comportamiento de enrutamiento que, en lugar de **Inicio**, usa **Elemento** como controlador e **Índice** de usuario como vista.
	Si ejecuta la aplicación, llamará a su **ItemController** y devolverá los resultados del método **GetIncompleteItems** en la vista **Vistas**\\**Elemento**\\**Índice**. 

12. Si crea y ejecuta este proyecto ahora, deberá ver algo parecido a esto.    

	![Screen shot of the todo list web application created by this tutorial](./media/documentdb-dotnet-application/image23.png)

### <a name="_Toc395637771"></a>Adición de elementos

Pongamos algunos elementos en nuestra base de datos de modo que tengamos algo más que una cuadrícula vacía que ver.

Ya tenemos una vista para **Crear** y un botón en la vista **Índice** que lleva al usuario a la vista **Crear**. Agreguemos algo de código al controlador y al repositorio para mantener el registro en Base de datos de documentos.

1. Abra el archivo ItemController.cs y agregue el siguiente fragmento de código, que es cómo ASP.NET MVC sabe qué hacer para la acción **Crear**. En este caso basta con presentar la vista Create.cshtml asociada creada anteriormente.

    	public ActionResult Create()
    	{ 
			return View(); 
   		}

	Ahora necesitamos un poco más de código en este controlador que aceptará el envío desde la vista **Crear**.

2. Agregue el siguiente bloque de código que le indica a ASP.NET MVC lo que debe hacer con un formulario POST para este controlador.
	
    	[HttpPost]
    	[ValidateAntiForgeryToken]
   	 	public async Task<ActionResult> Create([Bind(Include = 	"Id,Name,Description,Completed")] Item item)  
  	  	{
			if (ModelState.IsValid)  
			{  
			    await DocumentDBRepository<Item>.CreateItemAsync(item);
			    return RedirectToAction("Index");  
			}   
			return View(item);   
		}

	**Nota de seguridad**: El atributo **ValidateAntiForgeryToken** se usa aquí para ayudarle a proteger esta aplicación contra ataques de falsificación de solicitud entre sitios. Es más que solo agregar este atributo, sus vistas también necesitan trabajar con este token antifalsificación. Para obtener más información acerca del tema, así como ejemplos de cómo implementarlo correctamente, consulte [Prevención de la falsificación de solicitud entre sitios][]. El código de origen proporcionado en [Github][] tiene la implementación completa en su lugar.

	**Nota de seguridad**: También usamos el atributo **Bind** en el parámetro de método para ayudar a proteger contra ataques de publicación en exceso. Para obtener más información, consulte [Operaciones CRUD básicas en ASP.NET MVC][].

3. Ahora que el método de **creación** está en su lugar, **ItemController** pasará el objeto **Elemento** desde el formulario al método **CreateDocument**. A continuación, agregue el siguiente método a su clase **DocumentDBRepository**.

    	public static async Task<Document> CreateItemAsync(T item)
   	 	{
   	   		return await Client.CreateDocumentAsync(Collection.SelfLink, item);
   		}

	Este método simplemente toma un objeto que se le haya pasado y lo mantiene en la base de datos de documentos.

Esto finaliza el código necesario para agregar elementos nuevos en nuestra base de datos.


### <a name="_Toc395637772"></a>Edición de elementos

Hay una última cosa que tenemos que hacer, que es agregar la capacidad de editar **elementos** en la base de datos y marcarlos como completados. La vista de edición ya se ha agregado al proyecto, por lo que necesitamos agregar código a nuestro controlador y a la clase **DocumentDBRepository** de nuevo.

1. Agregue lo siguiente a la clase **ItemController**.

    	[HttpPost]
   		[ValidateAntiForgeryToken]
    	public async Task<ActionResult> Edit([Bind(Include = "Id,Name,Description,Completed")] Item item)
    	{
     	   if (ModelState.IsValid)
    	    {
    	        await DocumentDBRepository<Item>.UpdateItemAsync(item.Id, item);
    	        return RedirectToAction("Index");
    	    }

  	      return View(item);
 	   	}
		
		public ActionResult Edit(string id)
		{
		    if (string.IsNullOrEmpty(id))
		    {
		        return new HttpStatusCodeResult(HttpStatusCode.BadRequest);
		    }
					 
		    Item item = (Item)DocumentDBRepository<Item>.GetItem(d => d.Id == id);
		 	
		    if (item == null)
		    {
		        return HttpNotFound();
		    }
		 	
		    return View(item);
		}
		
	
	El primer método controla el Http GET que ocurrirá cuando el usuario haga clic en el vínculo de **edición** en la vista de **índice**. Este método captura un [**documento**](http://msdn.microsoft.com/library/azure/microsoft.azure.documents.document.aspx) de Base de datos de documentos y lo pasa a la vista de **edición**.

	A continuación, la vista de **edición** realizará un Http POST en **IndexController**. 
	
	El segundo método que agregamos controla el paso del objeto actualizado en Base de datos de documentos para que se conserve en la base de datos.

2. Agregue la siguiente directiva using al archivo ItemController.cs.

		using todo.Models;
		using System.Threading.Tasks;
		using System.Net;

3. Agregue lo siguiente a la clase **DocumentDBRepository**.

    	public static T GetItem(Expression<Func<T, bool>> predicate)
    	{
        	return Client.CreateDocumentQuery<T>(Collection.DocumentsLink)
                    	.Where(predicate)
                    	.AsEnumerable()
                    	.FirstOrDefault();
    	}	
		
		public static Document GetDocument(string id) 
		{ 
			return Client.CreateDocumentQuery(Collection.DocumentsLink) 
				.Where(d => d.Id == id) 
				.AsEnumerable() 
				.FirstOrDefault(); 
		} 

    	public static async Task<Document> UpdateItemAsync(string id, T item)
    	{
        	Document doc = GetDocument(id);	
        	return await Client.ReplaceDocumentAsync(doc.SelfLink, item);
    	}

	El primero de estos métodos captura un **elemento** de  Base de datos de documentos que pasa de vuelta al **ItemController** y, a continuación, continúa hasta la vista de **edición**.
	
	El segundo de los dos métodos que acabamos de agregar reemplaza el **documento** en Base de datos de documentos con la versión del **documento** que se pasó desde el **ItemController**.

4. Agregue la siguiente directiva using al archivo DocumentDBRepository.cs.

		using System.Threading.Tasks;

	Eso es todo lo necesario para ejecutar la aplicación, enumerar **elementos** incompletos, agregar nuevos **elementos** y editar **elementos**.

## <a name="_Toc395637773"></a>Paso 6: Ejecución de la aplicación de forma local

Lleve a cabo los siguientes pasos para probar la aplicación en su máquina local:

1. Presione F5 en Visual Studio para compilar la aplicación en modo de depuración. De esa forma, se debe compilar la aplicación e iniciar un explorador con la página de cuadrícula vacía que vimos anteriormente:

	![Screen shot of the todo list web application created by this tutorial](./media/documentdb-dotnet-application/image24.png)

	Si se producen errores en ese momento, puede comparar su código con el tutorial todo en [GitHub][].

2. Haga clic en vínculo **Crear nuevo** y agregue valores a los campos **Nombre** y **Descripción**. Deje la casilla **Completado** sin seleccionar; de lo contrario, el **elemento** nuevo se agregará en estado completado y no aparecerá en la lista inicial.

	![Screen shot of the Create view](./media/documentdb-dotnet-application/image25.png)

3. Haga clic en **Crear**, se le redirigirá a la vista de **índice** y aparecerá el **elemento** en la lista.

	![Screen shot of the Index view](./media/documentdb-dotnet-application/image26.png)

	Puede agregar algunos **elementos** más en su lista todo.

3. Haga clic en **Editar** junto a un **elemento** de la lista y llegará a la vista de **edición**, donde puede actualizar cualquier propiedad de su objeto, incluida la marca **Completado**. Si selecciona la marca **Completado** y hace clic en **Guardar**, el **elemento** se quita de la lista de tareas incompletas.

	![Screen shot of the Index view with the Completed box checked](./media/documentdb-dotnet-application/image27.png)

4. Una vez que haya probado la aplicación, presione Ctrl+F5 para detener la depuración de la aplicación. Está listo para la implementación.

## <a name="_Toc395637774"></a>Paso 7: Implementación de la aplicación en Sitios web Azure

Ahora que tiene la aplicación completa funcionando correctamente con Base de datos de documentos, vamos a implementar esta aplicación web en Sitios web Azure. Si seleccionó **Hospedar en la nube** cuando creó el proyecto vacío ASP.NET MVC, Visual Studio realmente facilita la tarea y hace gran parte del trabajo por usted. 

Para publicar esta aplicación, todo lo que necesita hacer es hacer clic con el botón secundario en el proyecto en el **Explorador de soluciones** y hacer clic en **Publicar**.

![Screen shot of the Publish option in Solution Explorer](./media/documentdb-dotnet-application/image28.png)

Todo debería estar configurado según sus credenciales; de hecho, el sitio web ya se ha creado en Azure en la **Dirección URL de destino** que se muestra. Solo tiene que hacer clic en **Publicar**.

![Screen shot of the Publish Web dialog box](./media/documentdb-dotnet-application/image29.png)

En pocos segundos, Visual Studio terminará de publicar su aplicación web y ejecutará un explorador donde podrá ver su útil trabajo ejecutándose en Azure.

## <a name="_Toc395637775"></a>Pasos siguientes

¡Enhorabuena! Acaba de compilar su primera aplicación ASP.NET MVC usando Base de datos de documentos de Azure y la ha publicado en Sitios web Azure. El código fuente de la aplicación completa, incluida la funcionalidad de detalle y eliminación que no se incluyeron en este tutorial, se puede descargar o clonar desde [Github][]. Por lo tanto, si está interesado en agregarlo a la aplicación, seleccione el código y agréguelo a esta aplicación.

Para agregar la funcionalidad adicional a la aplicación, revise las API disponibles en la [biblioteca de .NET de Base de datos de documentos](http://msdn.microsoft.com/library/azure/dn783362.aspx) y haga sus aportaciones libremente a la biblioteca de .NET de Base de datos de documentos en [GitHub][]. 

## <a id="GetProject"></a>Obtenga la solución de GitHub

Si desea para ahorrar tiempo y simplemente desea crear la solución de lista todo completa y no tener que agregar el código, está de suerte. La solución completa está disponible en GitHub y puede generarla e implementarla en pocos minutos mediante las siguientes instrucciones.

1. Asegúrese de que dispone del [software de requisitos previos](#_Toc395637760) instalado, que incluye Visual Studio y el SDK de Azure para la versión 2.3 o superior de .NET.

2. Clone el repositorio azure-documentdb-net mediante Git para Windows ([http://www.git-scm.com/](http://www.git-scm.com/)) o descargue el archivo zip de [GitHub](https://github.com/Azure/azure-documentdb-net/).

2. En Visual Studio, abra el archivo todo.sln desde el directorio azure-documentdb-net/tutorials/todo.

3. Para restaurar las referencias al SDK de .NET de Base de datos de documentos de Visual Studio 2013, haga clic con el botón secundario en la solución todo en el **Explorador de soluciones** y, a continuación haga clic en **Habilitar la restauración del paquete NuGet**, que restaurará las referencias. 

4. Recupere los valores **URI** y **PRIMARY KEY** o **SECONDARY KEY** de la hoja **Claves** de su cuenta de _Base de datos de documentos en el [Portal de vista previa de Azure](https://portal.azure.com/). 

	
	Si no dispone de una cuenta, consulte [Creación de una cuenta de base de datos] (/documentation/articles/documentdb-create-account/) para configurar una.

	![Screen shot of the Azure Preview portal, showing a DocumentDB account, with the ACTIVE hub highlighted, the KEYS tile highlighted on the DocumentDB account blade, and the URI, PRIMARY KEY and SECONDARY KEY values highlighted on the Keys blade](./media/documentdb-dotnet-application/keys.png)

5. En el archivo Web.config, actualice los valores predeterminados para las claves **endpoint** y **authKey**.

    	<add key="endpoint" value="~enter URI for your DocumentDB Account, from Azure Management Portal~" /> 
		<add key="authKey" value="~enter either Primary or Secondary key for your DocumentDB Account, from Azure Management Portal~" /> 

	- Copie el valor **URI** de la hoja Claves y péguelo en el valor de la propiedad **endpoint**. 
	- Copie el valor **PRIMARY KEY** o **SECONDARY KEY** de la hoja **Claves** y péguelo en el valor de la propiedad **authKey**.
	


7. Ahora puede [ejecutar la aplicación localmente](#_Toc395637773) y, a continuación, [implementarla en Sitios web Azure](#_Toc395637774).


[\*]: https://microsoft.sharepoint.com/teams/DocDB/Shared%20Documents/Documentation/Docs.LatestVersions/PicExportError
[Visual Studio Express]: http://www.visualstudio.com/products/visual-studio-express-vs.aspx
[Instalador de plataforma web de Microsoft]: http://www.microsoft.com/web/downloads/platform.aspx
[instalador de plataforma Web de Microsoft]: http://www.microsoft.com/web/downloads/platform.aspx
[Github]: http://go.microsoft.com/fwlink/?LinkID=509838&clcid=0x409
[GitHub]: http://go.microsoft.com/fwlink/?LinkID=509838&clcid=0x409
[Prevención de la falsificación de solicitud entre sitios]: http://go.microsoft.com/fwlink/?LinkID=517254
[Operaciones CRUD básicas en ASP.NET MVC]: http://go.microsoft.com/fwlink/?LinkId=317598

<!--HONumber=47-->
