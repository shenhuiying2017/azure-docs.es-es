---
title: 'Tutorial de ASP.NET MVC para Azure Cosmos DB: desarrollo de aplicaciones web | Microsoft Docs'
description: "Tutorial de ASP.NET MVC para crear una aplicación web MVC con Azure Cosmos DB. Almacenará el código JSON y accederá a los datos desde una aplicación ToDo hospedada en Azure Websites. Tutorial de ASP NET MVC paso a paso."
keywords: "tutorial de asp.net mvc, desarrollo de aplicaciones web, aplicación web de mvc, tutorial de asp net mvc paso a paso"
services: cosmos-db
documentationcenter: .net
author: mimig1
manager: jhubbard
editor: cgronlun
ms.assetid: 52532d89-a40e-4fdf-9b38-aadb3a4cccbc
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 08/03/2017
ms.author: mimig
ms.custom: devcenter
ms.openlocfilehash: a403af0f31823f89cdc79d6769dff61aeaefc4ad
ms.sourcegitcommit: 821b6306aab244d2feacbd722f60d99881e9d2a4
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/18/2017
---
# <a name="_Toc395809351"></a>Tutorial de ASP.NET MVC: desarrollo de aplicaciones web con Azure Cosmos DB
> [!div class="op_single_selector"]
> * [.NET](sql-api-dotnet-application.md)
> * [Node.js](sql-api-nodejs-application.md)
> * [Java](sql-api-java-application.md)
> * [Python](sql-api-python-application.md)
> 
> 

[!INCLUDE [cosmos-db-sql-api](../../includes/cosmos-db-sql-api.md)]

Para resaltar cómo puede aprovechar eficazmente Azure Cosmos DB para almacenar y consultar documentos JSON, este artículo proporciona un tutorial completo que muestra cómo crear una aplicación ToDo con Azure Cosmos DB. En Azure Cosmos DB, las tareas se almacenarán como documentos JSON.

![Captura de pantalla de la aplicación web MVC de lista ToDo creada por este tutorial: tutorial ASP NET MVC paso a paso](./media/sql-api-dotnet-application/asp-net-mvc-tutorial-image01.png)

Este tutorial muestra cómo utilizar el servicio Azure Cosmos DB para almacenar y acceder a datos desde una aplicación web MVC de ASP.NET hospedada en Azure. Si busca un tutorial que se centre solo en Azure Cosmos DB, no en los componentes de ASP.NET MVC, consulte el artículo sobre la [Creación de una aplicación de consola de C# con Azure Cosmos DB](sql-api-get-started.md).

> [!TIP]
> En este tutorial se supone que tiene experiencia previa con ASP.NET MVC y Azure Websites. Si no está familiarizado con ASP.NET o con las [herramientas de requisitos previos](#_Toc395637760), le recomendamos que descargue el proyecto de ejemplo completo de [GitHub][GitHub] y siga las instrucciones de este ejemplo. Una vez compilado, puede revisar este artículo para obtener información sobre el código en el contexto del proyecto.
> 
> 

## <a name="_Toc395637760"></a>Requisitos previos del tutorial de base de datos
Antes de seguir las instrucciones del presente artículo, debe asegurarse de tener lo siguiente:

* Una cuenta de Azure activa.  Si no tiene una suscripción a Azure, cree una [cuenta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de empezar. 

  [!INCLUDE [cosmos-db-emulator-docdb-api](../../includes/cosmos-db-emulator-docdb-api.md)]

* [!INCLUDE [cosmos-db-emulator-vs](../../includes/cosmos-db-emulator-vs.md)]  
* Microsoft Azure SDK para .NET de Visual Studio 2017, disponible a través del instalador de Visual Studio.

Se han realizado todas las capturas de pantalla de este artículo mediante Microsoft Visual Studio Community 2017. Si el sistema está configurado con una versión diferente, es probable que las pantallas y las opciones no coincidan completamente, pero si cumple los requisitos previos mencionados, esta solución debe funcionar.

## <a name="_Toc395637761"></a>Paso 1: Creación de una cuenta de base de datos de Azure Cosmos DB
Para comenzar, creemos una cuenta de Azure Cosmos DB. Si ya tiene una cuenta de SQL para Azure Cosmos DB o si usa el emulador de Azure Cosmos DB para este tutorial, puede ir directamente a [Creación de una nueva aplicación MVC de ASP.NET](#_Toc395637762).

[!INCLUDE [create-dbaccount](../../includes/cosmos-db-create-dbaccount.md)]

[!INCLUDE [keys](../../includes/cosmos-db-keys.md)]

<br/>
Ahora veremos cómo crear una aplicación ASP.NET MVC nueva desde el principio. 

## <a name="_Toc395637762"></a>Paso 2: Creación de una aplicación ASP.NET MVC nueva

1. En Visual Studio, en el menú **Archivo**, seleccione **Nuevo** y luego haga clic en **Proyecto**. Aparecerá el cuadro de diálogo **Nuevo proyecto** .

2. En el panel **Tipos de proyecto**, expanda **Plantillas**, **Visual C#**, **Web** y, a continuación, seleccione **Aplicación web ASP.NET**.

      ![Captura de pantalla del cuadro de diálogo Nuevo proyecto con el tipo de proyecto Aplicación web ASP.NET resaltado](./media/sql-api-dotnet-application/asp-net-mvc-tutorial-new-project-dialog.png)

3. En el cuadro **Nombre** , escriba el nombre del proyecto. Este tutorial utiliza el nombre "todo". Si decide usar otro nombre distinto, siempre que en el tutorial se hable del espacio de nombres todo, deberá adaptar los ejemplos de código proporcionados para usar el nombre que haya dado a la aplicación. 
4. Haga clic en **Examinar** para navegar hasta la carpeta donde desea crear el proyecto y, a continuación, haga clic en **Aceptar**.
   
      Aparece el cuadro de diálogo **Nueva aplicación web ASP.NET**.
   
    ![Captura de pantalla del cuadro de diálogo Nueva aplicación web ASP.NET con la plantilla de aplicación MVC resaltada](./media/sql-api-dotnet-application/asp-net-mvc-tutorial-MVC.png)
5. En el panel Plantillas, seleccione **MVC**.

6. Haga clic en **Aceptar** y deje que Visual Studio realice la tarea de scaffolding de la plantilla ASP.NET MVC vacía. 

          
7. Después de que Visual Studio haya terminado de crear la aplicación MVC reutilizable, tiene una aplicación ASP.NET vacía que puede ejecutar de manera local.
   
    Omitiremos la ejecución del proyecto localmente, ya que estoy seguro de que todos hemos visto la aplicación "Hola a todos" de ASP.NET. Vayamos directamente a agregar Azure Cosmos DB a este proyecto y a compilar la aplicación.

## <a name="_Toc395637767"></a>Paso 3: Incorporación de Azure Cosmos DB al proyecto de aplicación web MVC
Ahora que ya tenemos la mayoría de los mecanismos de ASP.NET MVC que necesitamos para esta solución, vayamos al objetivo real de este tutorial, la incorporación de Azure Cosmos DB a nuestra aplicación web MVC.

1. El SDK de .NET para Azure Cosmos DB se empaquete y distribuye como un paquete de NuGet. Para obtener el paquete de NuGet en Visual Studio, utilice el Administrador de paquetes de NuGet en Visual Studio haciendo clic con el botón derecho en el proyecto en el **Explorador de soluciones** y, a continuación, haciendo clic en **Administrar paquetes de NuGet**.
   
    ![Captura de pantalla de las opciones del botón secundario para el proyecto de aplicación web en el Explorador de soluciones, con Administrar paquetes NuGet resaltado.](./media/sql-api-dotnet-application/asp-net-mvc-tutorial-manage-nuget.png)
   
    Aparecerá el cuadro de diálogo **Administrar paquetes de NuGet** .
2. En el cuadro **Browse** (Examinar) de NuGet, escriba ***Azure DocumentDB***. (El nombre del paquete no se ha actualizado a Azure Cosmos DB.)
   
    En los resultados, instale el paquete **Microsoft.Azure.DocumentDB de Microsoft**. De esta manera se descarga e instala el paquete de Azure Cosmos DB además de todas las dependencias, como Newtonsoft.Json. Haga clic en **OK** (Aceptar) en el ventana **Preview** (Vista previa) y **I Accept** (Acepto) en la ventana **License Acceptance** (Aceptación de licencia) para completar la instalación.
   
    ![Captura de pantalla de la ventana Administrar paquetes de NuGet, con la biblioteca de cliente de Microsoft Azure Cosmos DB resaltada](./media/sql-api-dotnet-application/asp-net-mvc-tutorial-install-nuget.png)
   
      También puede usar la Consola del Administrador de paquetes para instalar el paquete. Para ello, en el menú **Herramientas**, haga clic en **Administrador de paquetes NuGet** y, a continuación, haga clic en **Consola del Administrador de paquetes**. En el símbolo del sistema, escriba lo siguiente:
   
        Install-Package Microsoft.Azure.DocumentDB
        
3. Una vez instalado el paquete, la solución de Visual Studio debe ser similar a lo siguiente con dos nuevas referencias agregadas, Microsoft.Azure.Documents.Client y Newtonsoft.Json.
   
    ![Captura de pantalla de las dos referencias agregadas al proyecto de datos JSON en el Explorador de soluciones](./media/sql-api-dotnet-application/asp-net-mvc-tutorial-added-references.png)

## <a name="_Toc395637763"></a>Paso 4: Configuración de la aplicación ASP.NET MVC
Ahora vamos a agregar a esta aplicación de MVC modelos, vistas y controladores:

* [Adición de un modelo](#_Toc395637764).
* [Adición de un controlador](#_Toc395637765).
* [Adición de vistas](#_Toc395637766).

### <a name="_Toc395637764"></a>Adición de un modelo de datos JSON
Comencemos creando la **M** en MVC, el modelo. 

1. En el **Explorador de soluciones**, haga clic con el botón derecho en la carpeta **Modelos**, haga clic en **Agregar** y después en **Clase**.
   
      Aparecerá el cuadro de diálogo **Agregar nuevo elemento** .
2. Ponga a la nueva clase el nombre **Item.cs** y haga clic en **Agregar**. 
3. En este nuevo archivo **Item.cs** , agregue lo siguiente después de la última *instrucción using*.
   
        using Newtonsoft.Json;
4. Ahora, reemplace este código 
   
        public class Item
        {
        }
   
    por el siguiente.
   
        public class Item
        {
            [JsonProperty(PropertyName = "id")]
            public string Id { get; set; }
   
            [JsonProperty(PropertyName = "name")]
            public string Name { get; set; }
   
            [JsonProperty(PropertyName = "description")]
            public string Description { get; set; }
   
            [JsonProperty(PropertyName = "isComplete")]
            public bool Completed { get; set; }
        }
   
    Todos los datos de Azure Cosmos DB se transmiten mediante la conexión y se almacenan como JSON. Para controlar la forma en la que JSON.NET serializa y deserializa los objetos, puede usar el atributo **JsonProperty** como se mostró en la clase de **elemento** que acabamos de crear. No **tiene** que hacer esto pero quería asegurarme de que mis propiedades siguen las convenciones de nomenclatura de mezcla de mayúsculas y minúsculas de JSON. 
   
    No solo puede controlar el formato del nombre de propiedad cuando va en JSON, sino que puede cambiar el nombre por completo de las propiedades de .NET como hice con la propiedad **Description** . 

### <a name="_Toc395637765"></a>Adición de un controlador
Esto se encarga de **M**. Creemos ahora la **C** de MVC, una clase de controlador.

1. En el **Explorador de soluciones**, haga clic con el botón derecho en la carpeta **Controladores** y, a continuación, haga clic en **Agregar** y, por último, en **Controlador**.
   
    Aparecerá el cuadro de diálogo **Agregar scaffold** .
2. Seleccione **Controlador MVC 5 - Vacío** y, a continuación, haga clic en **Agregar**.
   
    ![Captura de pantalla del cuadro de diálogo Agregar scaffold con la opción Controlador MVC 5 - Vacío resaltada](./media/sql-api-dotnet-application/asp-net-mvc-tutorial-controller-add-scaffold.png)
3. Asigne un nombre al nuevo controlador, **ItemController.**
   
    ![Captura de pantalla del cuadro de diálogo Agregar controlador](./media/sql-api-dotnet-application/asp-net-mvc-tutorial-add-controller.png)
   
    Una vez creado el archivo, la solución de Visual Studio debe ser similar a lo siguiente con el nuevo archivo ItemController.cs en el **Explorador de soluciones**. También se muestra el nuevo archivo Item.cs creado anteriormente.
   
    ![Captura de pantalla de la solución de Visual Studio - Explorador de soluciones con los nuevos archivos ItemController.cs e Item.cs resaltados](./media/sql-api-dotnet-application/asp-net-mvc-tutorial-new-item-solution-explorer.png)
   
    Puede cerrar ItemController.cs, volveremos a él más tarde. 

### <a name="_Toc395637766"></a>Adición de vistas
Por último, vamos a crear la **V** de MVC, las vistas.

* [Adición de una vista de índice de elementos](#AddItemIndexView).
* [Adición de una vista de elementos nuevos](#AddNewIndexView).
* [Adición de una vista de edición de elementos](#_Toc395888515).

#### <a name="AddItemIndexView"></a>Adición de una vista de índice de elementos
1. En el **Explorador de soluciones**, expanda la carpeta **Vistas**, haga clic con el botón derecho en la carpeta **Elemento** vacía que ha creado Visual Studio para usted cuando agregó **ItemController** anteriormente, haga clic en **Agregar** y, a continuación, haga clic en **Vista**.
   
    ![Captura de pantalla del Explorador de soluciones que muestra la carpeta Elemento con los comandos Agregar vista resaltados](./media/sql-api-dotnet-application/asp-net-mvc-tutorial-add-view.png)
2. En el cuadro de diálogo **Agregar vista** , realice lo siguiente:
   
   * En el cuadro **Nombre de vista**, escriba ***Índice***.
   * En el cuadro **Plantilla**, seleccione ***Lista***.
   * En el cuadro **Clase de modelo**, seleccione ***Elemento (todo.Models)***.
   * En el cuadro de página de diseño, escriba ***~/Views/Shared/_Layout.cshtml***.
     
   ![Captura de pantalla que muestra el cuadro de diálogo Agregar vista](./media/sql-api-dotnet-application/asp-net-mvc-tutorial-add-view-dialog.png)
3. Después de que se hayan establecido todos estos valores, haga clic en **Agregar** y permita que Visual Studio cree una nueva vista de plantilla. Después de esto, se abrirá el archivo cshtml que se creó. Podemos cerrar este archivo en Visual Studio, ya que volveremos a él más tarde.

#### <a name="AddNewIndexView"></a>Adición de una vista de elementos nuevos
De forma parecida a cómo se crea una vista de **índice de elementos**, crearemos ahora una nueva vista para crear nuevos **elementos**.

1. En el **Explorador de soluciones**, vuelva a hacer clic con el botón derecho en la carpeta **Elemento**, haga clic en **Agregar** y después en **Vista**.
2. En el cuadro de diálogo **Agregar vista** , realice lo siguiente:
   
   * En el cuadro **Nombre de vista**, escriba ***Crear***.
   * En el cuadro **Plantilla**, seleccione ***Crear***.
   * En el cuadro **Clase de modelo**, seleccione ***Elemento (todo.Models)***.
   * En el cuadro de página de diseño, escriba ***~/Views/Shared/_Layout.cshtml***.
   * Haga clic en **Agregar**.
   
#### <a name="_Toc395888515"></a>Adición de una vista de edición de elementos
Finalmente, agregue una última vista para editar un **elemento** como se hizo anteriormente.

1. En el **Explorador de soluciones**, vuelva a hacer clic con el botón derecho en la carpeta **Elemento**, haga clic en **Agregar** y después en **Vista**.
2. En el cuadro de diálogo **Agregar vista** , realice lo siguiente:
   
   * En el cuadro **Nombre de vista**, escriba ***Editar***.
   * En el cuadro **Plantilla**, seleccione ***Editar***.
   * En el cuadro **Clase de modelo**, seleccione ***Elemento (todo.Models)***.
   * En el cuadro de página de diseño, escriba ***~/Views/Shared/_Layout.cshtml***.
   * Haga clic en **Agregar**.

Una vez hecho esto, cierre todos los documentos cshtml en Visual Studio, ya que volveremos a estas vistas más tarde.

## <a name="_Toc395637769"></a>Paso 5: Conexión de Azure Cosmos DB
Ahora que ya nos hemos ocupado de los elementos estándar de MVC, agreguemos el código para Azure Cosmos DB. 

En esta sección, agregaremos código para controlar lo siguiente:

* [Lista de elementos incompletos](#_Toc395637770).
* [Adición de elementos](#_Toc395637771).
* [Edición de elementos](#_Toc395637772).

### <a name="_Toc395637770"></a>Lista de elementos incompletos en la aplicación web MVC
Lo primero que debe hacerse es agregar una clase que contenga toda la lógica para conectarse a Azure Cosmos DB y utilizarlo. Para este tutorial, encapsularemos toda esta lógica en una clase de repositorio denominada DocumentDBRepository. 

1. En el **Explorador de soluciones**, haga clic con el botón derecho en el proyecto, haga clic en **Agregar** y después en **Clase**. Ponga a la nueva clase el nombre **DocumentDBRepository** y haga clic en **Agregar**.
2. En la recién creada **DocumentDBRepository**, clasifique y agregue las *instrucciones using* siguientes sobre la declaración *namespace*
   
        using Microsoft.Azure.Documents; 
        using Microsoft.Azure.Documents.Client; 
        using Microsoft.Azure.Documents.Linq; 
        using System.Configuration;
        using System.Linq.Expressions;
        using System.Threading.Tasks;
        using System.Net
        
    Ahora, reemplace este código 
   
        public class DocumentDBRepository
        {
        }
   
    por el siguiente.
   
        public static class DocumentDBRepository<T> where T : class
        {
            private static readonly string DatabaseId = ConfigurationManager.AppSettings["database"];
            private static readonly string CollectionId = ConfigurationManager.AppSettings["collection"];
            private static DocumentClient client;
   
            public static void Initialize()
            {
                client = new DocumentClient(new Uri(ConfigurationManager.AppSettings["endpoint"]), ConfigurationManager.AppSettings["authKey"]);
                CreateDatabaseIfNotExistsAsync().Wait();
                CreateCollectionIfNotExistsAsync().Wait();
            }
   
            private static async Task CreateDatabaseIfNotExistsAsync()
            {
                try
                {
                    await client.ReadDatabaseAsync(UriFactory.CreateDatabaseUri(DatabaseId));
                }
                catch (DocumentClientException e)
                {
                    if (e.StatusCode == System.Net.HttpStatusCode.NotFound)
                    {
                        await client.CreateDatabaseAsync(new Database { Id = DatabaseId });
                    }
                    else
                    {
                        throw;
                    }
                }
            }
   
            private static async Task CreateCollectionIfNotExistsAsync()
            {
                try
                {
                    await client.ReadDocumentCollectionAsync(UriFactory.CreateDocumentCollectionUri(DatabaseId, CollectionId));
                }
                catch (DocumentClientException e)
                {
                    if (e.StatusCode == System.Net.HttpStatusCode.NotFound)
                    {
                        await client.CreateDocumentCollectionAsync(
                            UriFactory.CreateDatabaseUri(DatabaseId),
                            new DocumentCollection { Id = CollectionId },
                            new RequestOptions { OfferThroughput = 1000 });
                    }
                    else
                    {
                        throw;
                    }
                }
            }
        }
   
    
3. Estamos leyendo algunos valores de la configuración; por tanto, abra el archivo **Web.config** de su aplicación y agregue las siguientes líneas debajo de la sección `<AppSettings>`.
   
        <add key="endpoint" value="enter the URI from the Keys blade of the Azure Portal"/>
        <add key="authKey" value="enter the PRIMARY KEY, or the SECONDARY KEY, from the Keys blade of the Azure  Portal"/>
        <add key="database" value="ToDoList"/>
        <add key="collection" value="Items"/>
4. Ahora, actualice los valores de *endpoint* y *authKey* mediante la hoja Claves de Azure Portal. Utilice el **URI** de la hoja Claves como el valor de la configuración de endpoint y utilice **CLAVE PRINCIPAL** o **CLAVE SECUNDARIA** de la hoja Claves como el valor de la configuración de authKey.

    Se encarga del cableado del repositorio de Azure Cosmos DB; agreguemos ahora la lógica de aplicación.

1. Lo primero que queremos poder hacer con una aplicación de lista todo es mostrar los elementos incompletos.  Copie y pegue el siguiente fragmento de código en cualquier parte dentro de la clase **DocumentDBRepository** .
   
        public static async Task<IEnumerable<T>> GetItemsAsync(Expression<Func<T, bool>> predicate)
        {
            IDocumentQuery<T> query = client.CreateDocumentQuery<T>(
                UriFactory.CreateDocumentCollectionUri(DatabaseId, CollectionId))
                .Where(predicate)
                .AsDocumentQuery();
   
            List<T> results = new List<T>();
            while (query.HasMoreResults)
            {
                results.AddRange(await query.ExecuteNextAsync<T>());
            }
   
            return results;
        }
2. Abra **ItemController** que agregamos anteriormente y agregue las *instrucciones using* siguientes sobre la declaración namespace.
   
        using System.Net;
        using System.Threading.Tasks;
        using todo.Models;
   
    Si el proyecto no se denomina "todo", deberá actualizar con "todo. Models"; para reflejar el nombre del proyecto.
   
    Ahora, reemplace este código
   
        //GET: Item
        public ActionResult Index()
        {
            return View();
        }
   
    por el siguiente.
   
        [ActionName("Index")]
        public async Task<ActionResult> IndexAsync()
        {
            var items = await DocumentDBRepository<Item>.GetItemsAsync(d => !d.Completed);
            return View(items);
        }
3. Abra **Global.asax.cs** y agregue la siguiente línea al método **Application_Start** 
   
        DocumentDBRepository<todo.Models.Item>.Initialize();

En este punto, la solución debe ser capaz de compilar sin errores.

Si ejecuta la aplicación ahora, irá a **HomeController** y a la vista de **índice** de ese controlador. Este es el comportamiento predeterminado para el proyecto de plantillas MVC que seleccionamos al comienzo, pero no queremos eso. Cambiemos el enrutamiento en esta aplicación MVC para modificar este comportamiento.

Abra ***App\_Start\RouteConfig.cs***, busque la línea que empieza con "defaults:" y cámbiela para que se parezca a lo siguiente.

        defaults: new { controller = "Item", action = "Index", id = UrlParameter.Optional }

Esto indica ahora a ASP.NET MVC que si no ha especificado un valor en la dirección URL para controlar el comportamiento de enrutamiento que, en lugar de **Inicio**, usa **Elemento** como controlador e **Índice** de usuario como vista.

Si ejecuta la aplicación, llamará a su **ItemController**, que llamará a la clase de repositorio y usará el método GetItems para devolver todos los elementos incompletos a la vista **Vistas**\\**Elemento**\\**Índice**. 

Si crea y ejecuta este proyecto ahora, deberá ver algo parecido a esto.    

![Captura de pantalla de la aplicación web de lista de tareas pendientes creada con este tutorial](./media/sql-api-dotnet-application/build-and-run-the-project-now.png)

### <a name="_Toc395637771"></a>Adición de elementos
Pongamos algunos elementos en nuestra base de datos de modo que tengamos algo más que una cuadrícula vacía que ver.

Agreguemos algo de código a Azure Cosmos DBRepository e ItemController para mantener el registro en Azure cosmos DB.

1. Agregue el siguiente método a su clase **DocumentDBRepository** .
   
       public static async Task<Document> CreateItemAsync(T item)
       {
           return await client.CreateDocumentAsync(UriFactory.CreateDocumentCollectionUri(DatabaseId, CollectionId), item);
       }
   
   Este método simplemente toma un objeto que se le haya pasado y lo mantiene en Azure Cosmos DB.
2. Abra el archivo ItemController.cs y agregue el siguiente fragmento de código en la clase. Así es cómo ASP.NET MVC sabe qué hacer para la acción **Crear** . En este caso basta con presentar la vista Create.cshtml asociada creada anteriormente.
   
        [ActionName("Create")]
        public async Task<ActionResult> CreateAsync()
        {
            return View();
        }
   
    Ahora necesitamos un poco más de código en este controlador que aceptará el envío desde la vista **Crear** .
3. Agregue el siguiente bloque de código a la clase ItemController.cs que le indica a ASP.NET MVC lo que debe hacer con un formulario POST para este controlador.
   
        [HttpPost]
        [ActionName("Create")]
        [ValidateAntiForgeryToken]
        public async Task<ActionResult> CreateAsync([Bind(Include = "Id,Name,Description,Completed")] Item item)
        {
            if (ModelState.IsValid)
            {
                await DocumentDBRepository<Item>.CreateItemAsync(item);
                return RedirectToAction("Index");
            }
   
            return View(item);
        }
   
    Este código llama a DocumentDBRepository y usa el método CreateItemAsync para conservar el nuevo elemento todo en la base de datos. 
   
    **Nota de seguridad**: El atributo **ValidateAntiForgeryToken** se usa aquí para ayudarle a proteger esta aplicación contra ataques de falsificación de solicitud entre sitios. Es más que solo agregar este atributo, sus vistas también necesitan trabajar con este token antifalsificación. Para más información acerca del tema, así como ejemplos de cómo implementarlo correctamente, consulte el artículo sobre la [prevención de la falsificación de solicitud entre sitios][Preventing Cross-Site Request Forgery]. El código fuente proporcionado en [GitHub][GitHub] tiene la implementación completa.
   
    **Nota de seguridad**: También usamos el atributo **Bind** en el parámetro de método para ayudar a proteger contra ataques de publicación en exceso. Para más información, consulte el artículo sobre [operaciones CRUD básicas en ASP.NET MVC][Basic CRUD Operations in ASP.NET MVC].

Esto finaliza el código necesario para agregar elementos nuevos en nuestra base de datos.

### <a name="_Toc395637772"></a>Edición de elementos
Hay una última cosa que tenemos que hacer, que es agregar la capacidad de editar **elementos** en la base de datos y marcarlos como completados. La vista de edición ya se ha agregado al proyecto, por lo que necesitamos agregar código a nuestro controlador y a la clase **DocumentDBRepository** de nuevo.

1. Agregue lo siguiente a la clase **DocumentDBRepository** .
   
        public static async Task<Document> UpdateItemAsync(string id, T item)
        {
            return await client.ReplaceDocumentAsync(UriFactory.CreateDocumentUri(DatabaseId, CollectionId, id), item);
        }
   
        public static async Task<T> GetItemAsync(string id)
        {
            try
            {
                Document document = await client.ReadDocumentAsync(UriFactory.CreateDocumentUri(DatabaseId, CollectionId, id));
                return (T)(dynamic)document;
            }
            catch (DocumentClientException e)
            {
                if (e.StatusCode == HttpStatusCode.NotFound)
                {
                    return null;
                }
                else
                {
                    throw;
                }
            }
        }
   
    El primero de estos métodos, **GetItem** captura un elemento de Azure cosmos DB que se devuelve a **ItemController** y luego continúa hasta la vista de **edición**.
   
    El segundo de los métodos que acabamos de agregar reemplaza el **documento** de Azure cosmos DB con la versión del **documento** que se transmitió desde **ItemController**.
2. Agregue lo siguiente a la clase **ItemController** .
   
        [HttpPost]
        [ActionName("Edit")]
        [ValidateAntiForgeryToken]
        public async Task<ActionResult> EditAsync([Bind(Include = "Id,Name,Description,Completed")] Item item)
        {
            if (ModelState.IsValid)
            {
                await DocumentDBRepository<Item>.UpdateItemAsync(item.Id, item);
                return RedirectToAction("Index");
            }
   
            return View(item);
        }
   
        [ActionName("Edit")]
        public async Task<ActionResult> EditAsync(string id)
        {
            if (id == null)
            {
                return new HttpStatusCodeResult(HttpStatusCode.BadRequest);
            }
   
            Item item = await DocumentDBRepository<Item>.GetItemAsync(id);
            if (item == null)
            {
                return HttpNotFound();
            }
   
            return View(item);
        }
   
    El primer método controla el Http GET que ocurrirá cuando el usuario haga clic en el vínculo de **edición** en la vista de **índice**. Este método captura un [**documento**](http://msdn.microsoft.com/library/azure/microsoft.azure.documents.document.aspx) de Azure cosmos DB y lo transmite a la vista de **edición**.
   
    La vista de **edición** realizará un Http POST en **IndexController**. 
   
    El segundo método que agregamos controla la transmisión del objeto actualizado a Azure cosmos DB para que se conserve en la base de datos.

Esto es todo lo que necesitamos para ejecutar la aplicación, enumerar **elementos** incompletos, agregar nuevos **elementos** y editar **elementos**.

## <a name="_Toc395637773"></a>Paso 6: Ejecución de la aplicación de forma local
Lleve a cabo los siguientes pasos para probar la aplicación en su máquina local:

1. Presione F5 en Visual Studio para compilar la aplicación en modo de depuración. De esa forma, se debe compilar la aplicación e iniciar un explorador con la página de cuadrícula vacía que vimos anteriormente:
   
    ![Captura de pantalla de la aplicación web de lista de tareas pendientes creada con este tutorial](./media/sql-api-dotnet-application/asp-net-mvc-tutorial-create-an-item-a.png)
   
     
2. Haga clic en el vínculo **Crear nuevo** y agregue valores a los campos **Nombre** y **Descripción**. Deje la casilla **Completado** sin seleccionar; de lo contrario, el **elemento** nuevo se agregará en estado completado y no aparecerá en la lista inicial.
   
    ![Captura de pantalla de la vista Crear](./media/sql-api-dotnet-application/asp-net-mvc-tutorial-create-new-item.png)
3. Haga clic en **Crear**, se le redirigirá a la vista de **índice** y aparecerá el **elemento** en la lista.
   
    ![Captura de pantalla de la vista de índice](./media/sql-api-dotnet-application/asp-net-mvc-tutorial-create-an-item.png)
   
    Puede agregar algunos **elementos** más en su lista todo.
    
4. Haga clic en **Editar** junto a un **elemento** de la lista y llegará a la vista de **edición**, donde puede actualizar cualquier propiedad de su objeto, incluida la marca **Completado**. Si selecciona la marca **Completado** y hace clic en **Guardar**, el **elemento** se quita de la lista de tareas incompletas.
   
    ![Captura de pantalla de la vista de índice con el cuadro Completado activado](./media/sql-api-dotnet-application/asp-net-mvc-tutorial-completed-item.png)
5. Una vez que haya probado la aplicación, presione Ctrl+F5 para detener la depuración de la aplicación. Está listo para la implementación.

## <a name="_Toc395637774"></a>Paso 7: Implementación de la aplicación en Azure App Service 
Ahora que toda la aplicación funciona correctamente con Azure Cosmos DB, vamos a implementar esta aplicación web en Azure App Service.  

1. Para publicar esta aplicación, todo lo que necesita hacer es hacer clic con el botón derecho en el proyecto en el **Explorador de soluciones** y hacer clic en **Publicar**.
   
    ![Captura de pantalla de la opción Publicar en el Explorador de soluciones](./media/sql-api-dotnet-application/asp-net-mvc-tutorial-publish.png)

2. En el cuadro de diálogo **Publicar**, haga clic en **Microsoft Azure App Service** y, a continuación, seleccione **Crear nuevo** para crear un perfil de App Service o haga clic en **Seleccione existente** para utilizar un perfil existente.

    ![Cuadro de diálogo Publicar en Visual Studio](./media/sql-api-dotnet-application/asp-net-mvc-tutorial-publish-to-existing.png)

3. Si tiene un perfil de Azure App Service existente, escriba el nombre de la suscripción. Use el filtro **Vista** para ordenar por tipo de recurso o grupo de recursos y, a continuación, seleccione la instancia de Azure App Service. 
   
    ![Cuadro de diálogo App Service en Visual Studio](./media/sql-api-dotnet-application/asp-net-mvc-tutorial-app-service.png)

4. Para crear un nuevo perfil de Azure App Service, haga clic en **Crear nuevo** en el cuadro de diálogo **Publicar**. En el cuadro de diálogo **Crear App Service**, escriba el nombre de la aplicación web, la suscripción adecuada, el grupo de recursos, el plan de App Service y, a continuación, haga clic en **Crear**.

    ![Cuadro de diálogo Crear App Service en Visual Studio](./media/sql-api-dotnet-application/asp-net-mvc-tutorial-create-app-service.png)

En pocos segundos, Visual Studio terminará de publicar la aplicación web y ejecutará un explorador donde podrá ver su trabajo ejecutándose en Azure.



## <a name="_Toc395637775"></a>Pasos siguientes
Felicidades. Acaba de compilar su primera aplicación web MVC de ASP.NET con Azure Cosmos DB y la ha publicado en Azure. El código fuente de la aplicación completa, incluida la funcionalidad de detalle y eliminación, que no se incluyeron en este tutorial, se puede descargar o clonar desde [GitHub][GitHub]. Por lo tanto, si está interesado en agregarlo a la aplicación, seleccione el código y agréguelo a esta aplicación.

Para agregar funcionalidad adicional a la aplicación, revise las API disponibles en la [biblioteca de .NET para Microsoft Azure Cosmos DB](/dotnet/api/overview/azure/cosmosdb?view=azure-dotnet) y haga sus aportaciones libremente a la biblioteca de .NET para Azure Cosmos DB en [GitHub][GitHub]. 

[\*]: https://microsoft.sharepoint.com/teams/DocDB/Shared%20Documents/Documentation/Docs.LatestVersions/PicExportError
[Visual Studio Express]: http://www.visualstudio.com/products/visual-studio-express-vs.aspx
[Microsoft Web Platform Installer]: http://www.microsoft.com/web/downloads/platform.aspx
[Preventing Cross-Site Request Forgery]: http://go.microsoft.com/fwlink/?LinkID=517254
[Basic CRUD Operations in ASP.NET MVC]: http://go.microsoft.com/fwlink/?LinkId=317598
[GitHub]: https://github.com/Azure-Samples/documentdb-net-todo-app
