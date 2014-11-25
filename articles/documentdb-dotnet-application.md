<properties title="Build a web application with ASP.NET MVC using DocumentDB" pageTitle="Build a web application with ASP.NET MVC using DocumentDB | Azure" description="Learn how to use DocumentDB to build a To Do List web application. You'll store and access data from an ASP.NET MVC web application hosted on Azure."  metaKeywords="NoSQL, DocumentDB,  database, document-orientated database, JSON, account" services="documentdb"  solutions="data-management" documentationCenter=""  authors="hawong" manager="jhubbard" editor="cgronlun" videoId="" scriptId="" />

<tags ms.service="documentdb" ms.workload="data-services" ms.tgt_pltfrm="na" ms.devlang="dotnet" ms.topic="article" ms.date="08/19/2014" ms.author="hawong" />

# <a name="_Toc395809351">Compilación de una aplicación web con ASP.NET MVC usando Base de datos de documentos</a>

# <a name="_Toc395637758"></a> <a name="_Toc389865467"></a> <a name="_Toc389828008">Información general</a>

## <a name="_Toc395637759">Escenario</a>

Para resaltar cómo los clientes pueden aprovechar eficazmente la Base de datos de documentos de Azure para
almacenar y consultar documentos JSON, en este documento se proporciona un tutorial
integral para compilar una aplicación web de Lista de tareas pendientes usando la Base de datos de documentos de
Azure.

Este tutorial muestra cómo puede usar el servicio Base de datos de documentos que ofrece
Azure para almacenar y tener acceso a datos desde una aplicación web ASP.NET MVC
hospedada en Azure y supone que tiene cierta experiencia en el uso de
ASP.NET MVC y Sitios web Azure.

Aprenderá a:

1. Crear y aprovisionar una cuenta de la Base de datos de documentos

2. Crear una aplicación ASP.NET MVC

3. Conectarse y utilizar la Base de datos de documentos desde su aplicación web

4. Implementar la aplicación web en Sitios web de Azure

Al seguir este tutorial, podrá compilar una aplicación de administración de
tareas basadas en web sencilla que permite crear, recuperar
y completar tareas. Las tareas se almacenarán como documentos JSON en la Base de datos de documentos de
Azure.

![Texto alternativo][Texto alternativo]

# <a name="_Toc395637760">Requisitos previos</a>

Antes de seguir las instrucciones del presente artículo, debe asegurarse
de tener instalados los siguientes elementos:

Git para Windows <http://www.git-scm.com/downloads>

Visual Studio 2013 (o [Visual Studio Express][Visual Studio Express] que es la
versión gratuita)

SDK de Azure para .NET versión 2.3 o superior, disponible a través del
[instalador de plataforma web de Microsoft][instalador de plataforma web de Microsoft]

Todas las capturas de pantalla de este documento se realizaron usando
Visual Studio 2013 con actualización 3 aplicada y SDK de Azure para .NET versión
2.4. Si el sistema está configurado con versiones diferentes, es probable
que las pantallas y las opciones no coincidan completamente, pero si cumple con los
requisitos previos mencionados, esta solución debe funcionar.

# <a name="_Toc395637761">Creación de una cuenta de base de datos de la Base de datos de documentos</a>

Para aprovisionar una cuenta de base de datos de Base de datos de documentos en Azure, abra el Portal de administración
de Azure y haga clic en el icono de la Galería de Azure en la
página de inicio o haga clic en "+" en la esquina inferior izquierda de la pantalla.

![Texto alternativo][1]

Esto abrirá la Galería de Azure, donde podrá seleccionar entre muchos
servicios de Azure disponibles. En la Galería, seleccione "Datos, almacenamiento y
copia de seguridad" de la lista de categorías.

![Texto alternativo][2]

Desde aquí, seleccione la opción de la Base de datos de documentos de Azure.

![Texto alternativo][3]

A continuación, seleccione "Crear" en la parte inferior de la pantalla

![Texto alternativo][4]

De esta manera se abre un cuadro "Nueva Base de datos de documentos" donde se puede especificar el
nombre, la región, el grupo de recursos y otros ajustes para la cuenta
nueva.

![Texto alternativo][5]

Después de terminar de proporcionar los valores de la cuenta, haga clic en "Crear" y el proceso de aprovisionamiento comenzará a crear la cuenta de base de datos.
Cuando el proceso de aprovisionamiento haya terminado, debe aparecer una notificación
en el área de notificaciones del portal y el icono en la
pantalla de inicio (si seleccionó crear uno) cambiará para mostrar la
acción que se completó.

![Texto alternativo][6]

Una vez que se haya completado el aprovisionamiento, al hacer clic en el icono de la Base de datos de documentos de la
pantalla inicio, se abrirá el cuadro principal para la cuenta de la
Base de datos de documentos recién creada.

![Texto alternativo][7]
![Texto alternativo][8]

Con el botón "Claves", tenga acceso a la URL del extremo y la clave principal,
cópielos en el Portapapeles y téngalos a mano ya que usaremos estos
valores en la aplicación web que vamos a crear a continuación.

Ahora veremos cómo crear una aplicación ASP.NET MVC nueva
desde el principio. Para su referencia, la solución completa se puede
descargar [aquí]().

# <a name="_Toc395637762">Creación de una aplicación ASP.NET MVC nueva</a>

En Visual Studio, haga clic en Archivo – Nuevo proyecto y seleccione la opción para
crear una aplicación web ASP.NET MVC nueva.

![Texto alternativo][9]

Seleccione dónde desea crear el proyecto y haga clic en Aceptar.

![Texto alternativo][10]

Si considera hospedar la aplicación en Azure, seleccione entonces el cuadro de la parte inferior derecha "Hospedar en la nube". Hemos seleccionado hospedar en la nube y ejecutar la aplicación hospedada en un sitio web de Azure. Al seleccionar
esta opción, aprovisionamos un sitio web de Azure para usted y facilitar en
gran medida la implementación de la aplicación de trabajo final.

Seleccione Aceptar y deje que Visual Studio haga su trabajo mediante scaffolding de la
plantilla de ASP.NET MVC vacía. Puede ver pantallas adicionales que le solicitan
iniciar sesión en su cuenta de Azure y proporcionar algunos valores para el sitio web
nuevo. Siga proporcionando todos estos valores de Azure y continúe.

Después de que Visual Studio haya terminado de crear la aplicación MVC reutilizable
, tiene una aplicación ASP.NET vacía que puede ejecutar de manera local.

Omitiremos la ejecución local porque estoy seguro que todos hemos visto la aplicación ASP.NET "Hola mundo". Vayamos directo a agregar la Base de datos de documentos a este proyecto y compilar la aplicación.

# <a name="_Toc395637763">Configuración de la aplicación ASP.NET MVC</a>

### 

### <a name="_Toc395637764">Adición de un modelo</a>

Comencemos creando la **M** en MVC, el modelo. En el Explorador de
soluciones, haga clic con el botón secundario en la carpeta *Modelos* y, a continuación, haga clic en **Agregar** y
**Clase**

![Texto alternativo][11]

Asigne un nombre a la nueva Clase, **Elemento** y, a continuación, agregue el siguiente código en esta
clase nueva

    public class Item
    {
        [JsonProperty(PropertyName="id")]
        public int ID { get; set; }
        [JsonProperty(PropertyName="name")]
        public string Name { get; set; }
        [JsonProperty(PropertyName = "desc")]
        public string Description { get; set; }
        [JsonProperty(PropertyName="isComplete")]
        public bool Completed { get; set; }    
    }

Todos los datos de la Base de datos de documentos se transmiten mediante la conexión y se almacenan como JSON. Para
controlar la manera en la que los objetos se serializan/deserializan mediante JSON.NET, puede
usar el atributo JsonProperty como se muestra arriba y que asegura que los nombres de nuestra
propiedad siguen la convención JSON sobre nombres de propiedades en minúsculas.
**No** es necesario que haga esto.

### <a name="_Toc395637765">Adición de un controlador</a>

Eso se encarga de M. Creemos ahora la **C** de MVC, una
clase de controlador.
 En el **Explorador de soluciones**, haga clic con el botón secundario en la carpeta *Controladores* y, a continuación, haga
clic en **Agregar** y **Controlador**.

![Texto alternativo][12]

![Texto alternativo][13]

En el cuadro de diálogo **Agregar scaffold**, haga clic en **Controlador MVC 5 - Vacío.**
Haga clic en **Agregar.**

![Texto alternativo][14]

Asigne un nombre al nuevo controlador, **ItemController.**

Visual Studio agregará ahora el ItemController. El Explorador de
soluciones debe ser similar a lo que se muestra a continuación.

![Texto alternativo][15]

### <a name="_Toc395637766">Adición de vistas</a>

Por último, creemos la **V** de MVC, una vista.

#### Adición de vista de índice de elementos

Amplíe la carpeta ***Vistas*** en el Explorador de soluciones y ubique la
carpeta Item (vacía) que Visual Studio ha creado cuando
agregó el *ItemController* antes. Haga clic con el botón secundario en ***Elemento***
y seleccione para agregar una vista nueva.

![Texto alternativo][16]

En el cuadro de diálogo "Agregar vista". Llame la vista "***Índice***", use la plantilla
***Lista***, seleccione ***Elemento (Todo.Models)*** que creamos
anteriormente como la clase y, por último, use ***\_Layout.cshtml***
en la solución como la página de diseño.

![Texto alternativo][17]

Después de que se hayan establecido todos estos valores, haga clic en Agregar y permita que Visual Studio cree
la vista. Visual Studio creará una vista de plantillas. Después de
esto, abrirá el archivo cshtml que se creó. Podemos cerrar este documento
en Visual Studio ya que volveremos a él más tarde.

#### Adición de una vista de elementos nuevos

De manera similar a la anterior, cree una vista nueva para crear elementos nuevos
según el ejemplo que se muestra a continuación;

![Texto alternativo][18]

#### Adición de una vista de edición de elementos

# <a name="_Toc395888515"></a>

Finalmente, agregue una última vista para editar un elemento como se hizo
antes;

![Texto alternativo][19]

Después de terminar, cierre los documentos cshtml en Visual Studio ya que
volveremos a estas vistas más tarde.

</h1>
# <a name="_Toc395637767">Adición de una Base de datos de documentos a su proyecto</a>

Esto cubre la mayoría de las conexiones de ASP.NET MVC que necesitamos para
esta solución. Vayamos ahora al objetivo real de este tutorial, la adición de Base de datos de documentos de Azure en nuestra aplicación web.

</h1>
## <a name="_Toc395637768">Instalación del paquete NuGet de Base de datos de documentos</a>

El SDK .NET de Base de datos de documentos se empaqueta y distribuye como un paquete de NuGet.
Con el administrador de paquetes de NuGet en Visual Studio (al que puede tener acceso
al hacer clic con el botón secundario en el proyecto y seleccionar "Administrar paquetes de NuGet".

![Texto alternativo][20]

Busque en línea "Base de datos de documentos de Azure" e instale el paquete. De esta manera
se descarga e instala el paquete de Base de datos de documentos además de todas
las dependencias, como Newtonsoft.Json.

Una vez instalado, la solución de Visual Studio debe parecerse a lo siguiente
con dos referencias nuevas agregadas;

![Texto alternativo][21]

</h1>
## <a name="_Toc395637769">Conexión de la Base de datos de documentos</a>

### <a name="_Toc395637770">Lista de elementos incompletos</a>

Abra **ItemController** y quite todo código presente en la clase
(deje la clase) que Visual Studio haya agregado. Lo volveremos a compilar poco a poco usando la Base de datos de documentos.

Agregue el siguiente fragmento de código en la clase ItemController
que ahora está vacía;

    public async Task<ActionResult> Index() 
    {
        var items = await repo.GetIncompleteItems();
        return View(items);
    }

Se usa una clase de "pseudo repositorio" para la Base de datos de documentos, que en realidad es solo
una clase de ayuda que contiene todo el código específico de la Base de datos de documentos. Para los
objetivos de este tutorial, no vamos a implementar una capa de
acceso a datos completo con inserción de dependencias usando un patrón de repositorio,
como podría hacerlo si fuera a compilar una aplicación real. Para los
propósitos de este tutorial vamos a poner toda la lógica del
acceso a datos en un proyecto para fines de simplicidad.

Agregue una clase nueva en el proyecto y nómbrelo **DocumentDBRepository.**
Reemplace el código del archivo de clase con lo siguiente;

    public static class DocumentDBRepository
    {
        public static async Task<List<Item>> GetIncompleteItems()
        {
            return await Task<List<Item>>.Run( () => 
                Client.CreateDocumentQuery<Item>(Collection.DocumentsLink)
                    .Where(d => !d.Completed)
                    .AsEnumerable()
                    .ToList<Item>());
        }
    } 

#### 

Invierta algo de tiempo agregando lo que se corresponde mediantes las instrucciones para resolver las
referencias. Es fácil resolver todas las referencias en Visual Studio, siempre
que el paquete de NuGet se haya instalado correctamente. La referencia a
*CreateDocumentQuery* se resolverá después de que necesite agregar manualmente la
siguiente directiva using;

    using Microsoft.Azure.Documents.Linq; 

Es necesario agregar unas pocas conexiones manuales en nuestra clase de pseudo
repositorio para que todo funcione como es debido. Agregue el siguiente código
a su clase de repositorio;


        private static Database database;
        private static Database Database
        {
            get
            {
                if (database == null)
                {
                    ReadOrCreateDatabase().Wait();
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
                    ReadOrCreateCollection(Database.SelfLink).Wait();
                }
                
                return collection;
            }
        }

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

        private static DocumentClient client;
        private static DocumentClient Client
        {
            get
            {
                if (client == null)
                {
                    String endpoint = ConfigurationManager.GetSetting("endpoint")
                    string authKey = ConfigurationManager.GetSetting("authKey");
                    Uri endpointUri = new Uri(endpoint);
                    client = new DocumentClient(endpointUri, authKey);
                }
                
                return client;
            }
        }

        private static async Task ReadOrCreateCollection(string databaseLink)
        {
            var collections = Client.CreateDocumentCollectionQuery(databaseLink)
                              .Where(col => col.Id == CollectionId).ToArray();
            
            if (collections.Any())
            {
                collection = collections.First();
            }
            else
            {
                collection = await Client.CreateDocumentCollectionAsync(databaseLink, 
                    new DocumentCollection { Id = CollectionId });
            }
        }
     
        private static async Task ReadOrCreateDatabase()
        {
            var databases = Client.CreateDatabaseQuery()
                            .Where(db => db.Id == DatabaseId).ToArray();
            
            if (databases.Any())
            {
                database = databases.First();
            }
            else
            {
                Database database = new Database { Id = DatabaseId };   
                database = await Client.CreateDatabaseAsync(database);
            }
        }

</h1>
#### 

La primera vez que tenga acceso a un método como GetIncompleteItems(), este llama al
cliente, el cual establecerá una conexión con la Base de datos de documentos de Azure. La primera
llamada a GetIncompleteItems() intentará también leer o crear la
base de datos y la DocumentCollection de esta aplicación. Esto solo debe
hacerse una vez, después simplemente puede copiar en caché las propiedades de SelfLink para
ambos recursos y volver a usarlos en la aplicación.

Cuando desea conectarse a una Base de datos de documentos posteriormente en la misma aplicación,
se volverá a usar la misma instancia de cliente, base de datos y colección
en vez de crear una instancia nueva cada vez.

Abra **web.config** y agregue las siguientes líneas bajo la sección;
<appsettings\>

    <add key="endpoint" value="enter you endpoint url from the Azure Management Portal"/>
    <add key="authKey" value="enter one of the keys from the Azure Management Portal"/>
    <add key="database" value="ToDoList"/>
    <add key="collection" value="Items"/>

</h4>
En este punto, la solución debe ser capaz de compilar sin errores.

Si ejecuta la aplicación ahora, irá al controlador de inicio y
la vista de índice de ese controlador. Este es el comportamiento predeterminado para el proyecto de plantillas
MVC que seleccionamos al comienzo, pero no queremos eso. Cambiemos
el enrutamiento en esta aplicación MVC para modificar este comportamiento.

Abra ***RouteConfig.cs*** en la carpeta App\_Start, localice la línea
que comienza con "defaults:" y cámbiela para que se parezca a lo siguiente;

    defaults: new { controller = "Item", action = "Index", id = UrlParameter.Optional }

Esto le indica ahora a ASP.NET MVC que si no ha especificado un valor en la URL para controlar el comportamiento de enrutamiento que en vez de "Inicio" use "Elemento" como el controlador y el índice de usuarios como la vista.
Ahora, si ejecuta la aplicación, llama a su **ItemController** y devuelve los resultados del método **GetIncompleteItems** a la vista Views\\Item\\Index.
Si ejecuta este proyecto ahora, debe ver algo similar a lo siguiente;

![Texto alternativo][22]

### <a name="_Toc395637771">Adición de elementos</a>

Pongamos ahora algunos elementos en nuestra base de datos de modo que tengamos algo más
que una cuadrícula vacía que ver.

Ya tenemos una vista para crear y un botón en la vista de índice
que llevará al usuario a la vista de creación. Agreguemos algo de código al
controlador y al repositorio para mantener el registro en la Base de datos de documentos.

Abra ***ItemController.cs*** y agregue el siguiente fragmento de código
que es cómo ASP.NET MVC sabe lo que debe hacer para la acción de creación, en este
caso solo representa la vista de Create.cshtml asociada que se creó anteriormente.

    public ActionResult Create()
    { 
          return View(); 
    }

Ahora necesitamos un poco más de código en este controlador que acepte el
envío desde la vista de creación.

Continúe y agregue el siguiente bloque de código que le indica a ASP.NET MVC lo que debe
hacer con un formulario POST para este controlador.

    [HttpPost]  
    public async Task<ActionResult> Create(Item item)  
    {
        if (ModelState.IsValid)  
        {  
            await repo.CreateDocument(item);  
            return RedirectToAction("Index");  
        }   
        return View(item);   
    }

El controlador de elementos pasará ahora el elemento, desde el formulario, hasta el
método CreateDocument de nuestra clase de pseudo repositorio; entonces, agregue el
siguiente método a su clase de DocumentDBRepository.

    public static async Task<Document> CreateDocument(dynamic item)
    {
        return await Client.CreateDocumentAsync(Collection.SelfLink, item); 
    }

Este método simplemente toma cualquier objeto que se le haya pasado y lo mantiene en la
Base de datos de documentos.

Esto finaliza el código necesario para agregar elementos nuevos en nuestra base de datos.

### <a name="_Toc395637772">Edición de elementos</a>

Lo último que nos queda por hacer tiene que ver con la capacidad de editar elementos
en la base de datos y marcarlos como completos a medida que completamos las
tareas. Como ocurre con la adición, la vista para editar ya se agregó, por lo
que solo necesitamos volver a agregar algo de código adicional a nuestro controlador y a la clase
DocumentDBRepository.

Agregue lo siguiente a la clase ItemController;

    public async Task<ActionResult> Edit(string id)    
    {  
        if (id == null)  
        {
            return new HttpStatusCodeResult(HttpStatusCode.BadRequest);   
        }

        Item item = await repo.GetDocument(id);
        if (item == null)
        {       
            return HttpNotFound();
        }
        
        return View(item);
    }

    [HttpPost] 
    public async Task<ActionResult> Edit(Item item)  
    {
        if (ModelState.IsValid)
        {
             await repo.UpdateDocument(item);
             return RedirectToAction("Index");
        }
        
        return View(item); 
    }

El primer método controla el Http Get que ocurrirá cuando el usuario
haga clic en el vínculo de edición en la vista de índice. Este método captura un
documento de la Base de datos de documentos y lo pasa a la vista de edición.

### 

La vista de edición hará posteriormente un Http Post de vuelta a nuestro IndexController y
el segundo método que agregamos controla esto pasando el objeto actualizado
mediante la Base de datos de documentos para que se mantenga en la base de datos.

Agregue lo siguiente a la clase DocumentDBRepository;

    public static async Task<Item> GetDocument(string id)
    {
        return await Task<Item>.Run(() =>
            Client.CreateDocumentQuery<Item>(Collection.DocumentsLink)
                .Where(d => d.ID == id)
                .AsEnumerable()
                .FirstOrDefault());
    }

    public static async Task<Document> UpdateDocument(Item item)
    {
        var doc = Client.CreateDocumentQuery<Document>(Collection.DocumentsLink)
                    .Where(d => d.Id == item.ID)
                    .AsEnumerable().FirstOrDefault();
        
        return await Client.ReplaceDocumentAsync(doc.SelfLink, item);
    }

El primero de estos dos métodos captura un elemento de la Base de datos de documentos que pasa
de vuelta al ItemController y luego continúa hasta la vista de edición.

El segundo de los dos métodos que acabamos de agregar reemplaza el documento en la
Base de datos de documentos con la versión del documento que se pasó desde el
ItemController.

Y eso es todo lo que necesitamos para ejecutar nuestra aplicación, enumerar
elementos incompletos, agregar elementos nuevos y, por último, editar elementos.

</h3>
# <a name="_Toc395637773">Ejecución de la aplicación de forma local</a>

### 

Para probar la aplicación en la máquina local, presione F5 en Visual Studio
y se compila la aplicación e inicia un explorador con la página de
cuadrícula vacía que vimos antes:

![Texto alternativo][23]

1\. Use los campos que se proporcionan para elemento, nombre del elemento y categoría para especificar
información y, a continuación, haga clic en el vínculo **"Crear nuevo"** y proporcione algunos
valores. Deje la casilla "Completado" sin seleccionar; de lo contrario, el elemento nuevo se
agregará en estado completado y no aparecerá en la lista inicial.

![Texto alternativo][24]

Si presiona Crear, se le redirigirá a la página de índice y
su elemento debe aparecer en la lista.

![Texto alternativo][25]

Puede agregar algunos elementos más en su lista Todo.

2\. Haga clic en "Editar" junto a un elemento de la lista y llegará
a la vista de edición donde puede actualizar cualquier propiedad de su objeto,
incluida la marca "Completado". Esto efectivamente marca el elemento
como completo y lo quitará de la lista de tareas incompletas.

![Texto alternativo][26]

3\. Para completar una tarea, simplemente marque la casilla y, a continuación, haga clic en
**Guardar**. Se le redirigirá a la página de lista donde ahora el
elemento no aparecerá más en ella.

</h3>
# <a name="_Toc395637774">Implementación de la aplicación en Sitios web de Azure</a>

### 

Ahora que tiene la aplicación completa funcionando correctamente con
Base de datos de documentos, vamos a implementar esto en Sitios web Azure.

Si seleccionó "Hospedar en la nube" cuando creamos el proyecto vacío ASP.NET
MVC, Visual Studio realmente facilita la tarea y hace gran parte del
trabajo por nosotros. Para publicar esta aplicación, todo lo que tiene que hacer es
hacer clic con el botón secundario en el proyecto en el Explorador de soluciones (asegúrese de que no se está
ejecutando localmente) y seleccione Publicar.

![Texto alternativo][27]

Todo debería estar configurado según sus credenciales;
de hecho, el sitio web ya se ha creado en Azure en la
"Dirección URL de destino" que se muestra, todo lo que tiene que hacer es hacer clic en **Publicar**

![Texto alternativo][28]

En pocos segundos, Visual Studio terminará de publicar su aplicación
web y ejecutará un explorador donde podrá ver su útil trabajo
ejecutándose en Azure.

</h3>
# <a name="_Toc395637775">Conclusión</a>

### 

¡Enhorabuena! Acaba de compilar su primera aplicación ASP.NET MVC usando Base de datos de documentos de Azure y la ha publicado en Sitios web Azure. El código fuente de la aplicación de referencia completa se puede descargar aquí.

[aquí] (<http://go.microsoft.com/fwlink/?LinkID=509838&clcid=0x409>)

  [Texto alternativo]: ./media/documentdb-dotnet-application/image1.png
  [Visual Studio Express]: http://www.visualstudio.com/es-es/products/visual-studio-express-vs.aspx
  [instalador de plataforma web de Microsoft]: http://www.microsoft.com/web/downloads/platform.aspx
  [1]: ./media/documentdb-dotnet-application/image2.png
  [2]: ./media/documentdb-dotnet-application/image3.png
  [3]: ./media/documentdb-dotnet-application/image4.png
  [4]: ./media/documentdb-dotnet-application/image5.png
  [5]: ./media/documentdb-dotnet-application/image6.png
  [6]: ./media/documentdb-dotnet-application/image7.png
  [7]: ./media/documentdb-dotnet-application/image8.png
  [8]: ./media/documentdb-dotnet-application/image9.png
  [9]: ./media/documentdb-dotnet-application/image10.png
  [10]: ./media/documentdb-dotnet-application/image11.png
  [11]: ./media/documentdb-dotnet-application/image12.png
  [12]: ./media/documentdb-dotnet-application/image13.png
  [13]: ./media/documentdb-dotnet-application/image14.png
  [14]: ./media/documentdb-dotnet-application/image15.png
  [15]: ./media/documentdb-dotnet-application/image16.png
  [16]: ./media/documentdb-dotnet-application/image17.png
  [17]: ./media/documentdb-dotnet-application/image18.png
  [18]: ./media/documentdb-dotnet-application/image19.png
  [19]: ./media/documentdb-dotnet-application/image20.png
  [20]: ./media/documentdb-dotnet-application/image21.png
  [21]: ./media/documentdb-dotnet-application/image22.png
  [22]: ./media/documentdb-dotnet-application/image23.png
  [23]: ./media/documentdb-dotnet-application/image24.png
  [24]: ./media/documentdb-dotnet-application/image25.png
  [25]: ./media/documentdb-dotnet-application/image26.png
  [26]: ./media/documentdb-dotnet-application/image27.png
  [27]: ./media/documentdb-dotnet-application/image28.png
  [28]: ./media/documentdb-dotnet-application/image29.png
