---
title: "Creación de una aplic. web con Redis Cache | Microsoft Docs"
description: "Aprenda a crear una aplicación web con Caché en Redis"
services: redis-cache
documentationcenter: 
author: wesmc7777
manager: cfowler
editor: 
ms.assetid: 454e23d7-a99b-4e6e-8dd7-156451d2da7c
ms.service: cache
ms.workload: tbd
ms.tgt_pltfrm: cache-redis
ms.devlang: na
ms.topic: hero-article
ms.date: 05/09/2017
ms.author: wesmc
ms.openlocfilehash: c0cf5baa71ce599cd5c20d34c42bd2c578114efe
ms.sourcegitcommit: 9890483687a2b28860ec179f5fd0a292cdf11d22
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/24/2018
---
# <a name="how-to-create-a-web-app-with-redis-cache"></a>Creación de una aplicación web con Caché en Redis
> [!div class="op_single_selector"]
> * [.NET](cache-dotnet-how-to-use-azure-redis-cache.md)
> * [ASP.NET](cache-web-app-howto.md)
> * [Node.js](cache-nodejs-get-started.md)
> * [Java](cache-java-get-started.md)
> * [Python](cache-python-get-started.md)
> 
> 

Este tutorial muestra cómo crear e implementar una aplicación web ASP.NET en una aplicación web de Azure App Service mediante Visual Studio 2017. La aplicación de ejemplo muestra una lista de estadísticas de equipos de una base de datos con distintas formas de usar Azure Redis Cache para almacenar y recuperar datos de la caché. Al finalizar el tutorial tendrá una aplicación web en ejecución que lee y escribe en una base de datos, optimizada con Azure Redis Cache y hospedada en Azure.

Aprenderá a realizar los siguientes procedimientos:

* Crear una aplicación web ASP.NET MVC 5 en Visual Studio.
* Tener acceso a datos desde una base de datos mediante Entity Framework.
* Mejorar el rendimiento de los datos y reducir la carga de la base de datos mediante el almacenamiento y la recuperación de los datos con Azure Redis Cache.
* Usar un conjunto ordenado de Redis para recuperar los 5 equipos principales.
* Aprovisionar los recursos de Azure para la aplicación mediante una plantilla de Resource Manager.
* Publicar la aplicación en Azure con Visual Studio.

## <a name="prerequisites"></a>requisitos previos
Antes de comenzar este tutorial, debe cumplir los siguientes requisitos previos:

* [Cuenta de Azure](#azure-account)
* [Visual Studio 2017 con el SDK de Azure para .NET](#visual-studio-2017-with-the-azure-sdk-for-net)

### <a name="azure-account"></a>Cuenta de Azure
Necesita una cuenta de Azure para completar el tutorial. Puede:

* [Abrir una cuenta de Azure gratis](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=redis_cache_hero). Obtenga créditos que puede usar para probar los servicios de Azure de pago. Incluso después de que se agoten los créditos, puede mantener la cuenta y usar los servicios y características gratuitos de Azure.
* [Activar los beneficios de suscripción a Visual Studio](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=redis_cache_hero). Su suscripción a MSDN le proporciona créditos todos los meses que puede usar para servicios de Azure de pago.

### <a name="visual-studio-2017-with-the-azure-sdk-for-net"></a>Visual Studio 2017 con el SDK de Azure para .NET
Este tutorial está escrito para Visual Studio 2017 con el [SDK de Azure para .NET](https://www.visualstudio.com/news/releasenotes/vs2017-relnotes#azuretools). El SDK de Azure 2.9.5 se incluye con el instalador de Visual Studio.

Si tiene Visual Studio 2015, puede seguir el tutorial con el [SDK de Azure para .NET](../dotnet-sdk.md) 2.8.2 o versiones posteriores. [Descargue aquí el último SDK de Azure para Visual Studio 2015](http://go.microsoft.com/fwlink/?linkid=518003). Si aún no lo tiene, Visual Studio se instala automáticamente con el SDK. Puede que algunas pantallas no coincidan exactamente con las ilustraciones que se muestran en este tutorial.

Si tiene Visual Studio 2013, puede [descargar el último SDK de Azure para Visual Studio 2013](http://go.microsoft.com/fwlink/?LinkID=324322). Puede que algunas pantallas no coincidan exactamente con las ilustraciones que se muestran en este tutorial.

## <a name="create-the-visual-studio-project"></a>Creación del proyecto de Visual Studio
1. Abra Visual Studio y haga clic en **Archivo**, **Nuevo**, **Proyecto**.
2. Expanda el nodo **Visual C#** en la lista **Plantillas**, seleccione **Nube** y haga clic en **Aplicación web ASP.NET**. Asegúrese de que se selecciona **.NET Framework 4.5.2** o superior.  Escriba **ContosoTeamStats** en el cuadro de texto **Nombre** y haga clic en **Aceptar**.
   
    ![Crear proyecto][cache-create-project]
3. Seleccione **MVC** como tipo de proyecto. 

    Asegúrese de especificar **Sin autenticación** en la opción **Autenticación**. Dependiendo de la versión de Visual Studio, puede establecerse otro valor predeterminado. Para cambiarlo, haga clic en **Cambiar autenticación** y seleccione **Sin autenticación**.

    Si está trabajando con Visual Studio 2015, desactive la casilla **Host en la nube**. En los pasos siguientes del tutorial, deberá [aprovisionar los recursos de Azure](#provision-the-azure-resources) y [publicar la aplicación en Azure](#publish-the-application-to-azure). Para ver un ejemplo de aprovisionamiento de una aplicación web de App Service desde Visual Studio si se deja la casilla **Host en la nube** activada, consulte [Implementación de una aplicación web creada con ASP.NET en Azure App Service mediante Visual Studio](../app-service/app-service-web-get-started-dotnet.md).
   
    ![Seleccionar plantilla de proyecto][cache-select-template]
4. Haga clic en **Aceptar** para crear el proyecto.

## <a name="create-the-aspnet-mvc-application"></a>Creación de la aplicación ASP.NET MVC
En esta sección del tutorial, creará la aplicación básica que lee y muestra estadísticas de equipos de una base de datos.

* [Incorporación del paquete NuGet Entity Framework](#add-the-entity-framework-nuget-package)
* [Agregar el modelo](#add-the-model)
* [Agregar el controlador](#add-the-controller)
* [Configurar las vistas](#configure-the-views)

### <a name="add-the-entity-framework-nuget-package"></a>Incorporación del paquete NuGet Entity Framework

1. En el menú **Herramientas**, haga clic en **Administrador de paquetes NuGet** y en **Consola del Administrador de paquetes**.
2. Ejecute el comando siguiente en la ventana **Consola del Administrador de paquetes**.
    
    ```
    Install-Package EntityFramework
    ```

Para más información acerca de este paquete, consulte la página [EntityFramework](https://www.nuget.org/packages/EntityFramework/) de NuGet.

### <a name="add-the-model"></a>Agregar el modelo
1. Haga clic con el botón derecho en **Modelos** en el **Explorador de soluciones** y elija **Agregar**, **Clase**. 
   
    ![Agregar modelo][cache-model-add-class]
2. Escriba `Team` para el nombre de clase y haga clic en **Agregar**.
   
    ![Agregar de clase de modelo][cache-model-add-class-dialog]
3. Reemplace las instrucciones `using` de la parte superior del archivo `Team.cs` por las siguientes instrucciones `using`.

    ```csharp
    using System;
    using System.Collections.Generic;
    using System.Data.Entity;
    using System.Data.Entity.SqlServer;
    ```


1. Reemplace la definición de la clase `Team` por el siguiente fragmento de código que contiene una definición de clase `Team` actualizada, así como algunas otras clases auxiliares de Entity Framework. Para más información sobre el enfoque de Code First en Entity Framework, consulte [Code First para una nueva base de datos](https://msdn.microsoft.com/data/jj193542).

    ```csharp
    public class Team
    {
        public int ID { get; set; }
        public string Name { get; set; }
        public int Wins { get; set; }
        public int Losses { get; set; }
        public int Ties { get; set; }
    
        static public void PlayGames(IEnumerable<Team> teams)
        {
            // Simple random generation of statistics.
            Random r = new Random();
    
            foreach (var t in teams)
            {
                t.Wins = r.Next(33);
                t.Losses = r.Next(33);
                t.Ties = r.Next(0, 5);
            }
        }
    }
    
    public class TeamContext : DbContext
    {
        public TeamContext()
            : base("TeamContext")
        {
        }
    
        public DbSet<Team> Teams { get; set; }
    }
    
    public class TeamInitializer : CreateDatabaseIfNotExists<TeamContext>
    {
        protected override void Seed(TeamContext context)
        {
            var teams = new List<Team>
            {
                new Team{Name="Adventure Works Cycles"},
                new Team{Name="Alpine Ski House"},
                new Team{Name="Blue Yonder Airlines"},
                new Team{Name="Coho Vineyard"},
                new Team{Name="Contoso, Ltd."},
                new Team{Name="Fabrikam, Inc."},
                new Team{Name="Lucerne Publishing"},
                new Team{Name="Northwind Traders"},
                new Team{Name="Consolidated Messenger"},
                new Team{Name="Fourth Coffee"},
                new Team{Name="Graphic Design Institute"},
                new Team{Name="Nod Publishers"}
            };
    
            Team.PlayGames(teams);
    
            teams.ForEach(t => context.Teams.Add(t));
            context.SaveChanges();
        }
    }
    
    public class TeamConfiguration : DbConfiguration
    {
        public TeamConfiguration()
        {
            SetExecutionStrategy("System.Data.SqlClient", () => new SqlAzureExecutionStrategy());
        }
    }
    ```


1. En el **Explorador de soluciones**, haga doble clic en el archivo **web.config** para abrirlo.
   
    ![Web.config][cache-web-config]
2. Agregue la siguiente sección `connectionStrings`. El nombre de la cadena de conexión debe coincidir con el de la clase de contexto de base de datos de Entity Framework, que es `TeamContext`.

    ```xml
    <connectionStrings>
        <add name="TeamContext" connectionString="Data Source=(LocalDB)\MSSQLLocalDB;AttachDbFilename=|DataDirectory|\Teams.mdf;Integrated Security=True"     providerName="System.Data.SqlClient" />
    </connectionStrings>
    ```

    Puede agregar la nueva sección `connectionStrings` después `configSections`, tal y como se muestra en el ejemplo siguiente.

    ```xml
    <configuration>
      <configSections>
        <!-- For more information on Entity Framework configuration, visit http://go.microsoft.com/fwlink/?LinkID=237468 -->
        <section name="entityFramework" type="System.Data.Entity.Internal.ConfigFile.EntityFrameworkSection, EntityFramework, Version=6.0.0.0, Culture=neutral, PublicKeyToken=b77a5c561934e089" requirePermission="false" />
      </configSections>
      <connectionStrings>
        <add name="TeamContext" connectionString="Data Source=(LocalDB)\MSSQLLocalDB;AttachDbFilename=|DataDirectory|\Teams.mdf;Integrated Security=True"     providerName="System.Data.SqlClient" />
      </connectionStrings>
      ...
      ```

    > [!NOTE]
    > La cadena de conexión puede variar según la versión de Visual Studio y SQL Server Express Edition usada para completar el tutorial. La plantilla de web.config se debe configurar para que coincida con la instalación y puede contener entradas `Data Source` como `(LocalDB)\v11.0` (de SQL Server Express 2012) o `Data Source=(LocalDB)\MSSQLLocalDB` (de SQL Server Express 2014 y versiones más recientes). Para más información acerca de las cadenas de conexión y las versiones de SQL Express, consulte [SQL Server 2016 Express LocalDB](https://docs.microsoft.com/sql/database-engine/configure-windows/sql-server-2016-express-localdb).

### <a name="add-the-controller"></a>Agregar el controlador
1. Presione **F6** para compilar el proyecto. 
2. En el **Explorador de soluciones**, haga clic con el botón derecho en la carpeta **Controladores** y elija **Agregar**, **Controlador**.
   
    ![Agregar controlador][cache-add-controller]
3. Seleccione **Controlador de MVC 5 con vistas que usa Entity Framework** y haga clic en **Agregar**. Si se produce un error después de hacer clic en **Agregar**, asegúrese de que ha compilado el proyecto primero.
   
    ![Agregar controlador][cache-add-controller-class]
4. Seleccione **Team (ContosoTeamStats.Models)** en la lista desplegable **Clase de modelo**. Seleccione **TeamContext (ContosoTeamStats.Models)** en la lista desplegable **Clase de contexto de datos**. Escriba `TeamsController` en el cuadro de texto de nombre **Controlador** (si no se rellena automáticamente). Haga clic en **Agregar** para crear la clase de controlador y agregue las vistas predeterminadas.
   
    ![Configurar controlador][cache-configure-controller]
5. En el **Explorador de soluciones**, expanda **Global.asax** y haga doble clic en **Global.asax.cs** para abrirlo.
   
    ![Global.asax.cs][cache-global-asax]
6. Agregue las dos siguientes instrucciones `using` a la parte superior del archivo, debajo de las restantes instrucciones `using`.

    ```csharp
    using System.Data.Entity;
    using ContosoTeamStats.Models;
    ```


1. Agregue la siguiente línea de código al final del método `Application_Start` .

    ```csharp
    Database.SetInitializer<TeamContext>(new TeamInitializer());
    ```


1. En el **Explorador de soluciones**, expanda `App_Start` y haga doble clic en `RouteConfig.cs`.
   
    ![RouteConfig.cs][cache-RouteConfig-cs]
2. Reemplace `controller = "Home"` en el código siguiente del método `RegisterRoutes` por `controller = "Teams"`, como se muestra en el ejemplo siguiente.

    ```csharp
    routes.MapRoute(
        name: "Default",
        url: "{controller}/{action}/{id}",
        defaults: new { controller = "Teams", action = "Index", id = UrlParameter.Optional }
    );
    ```


### <a name="configure-the-views"></a>Configurar las vistas
1. En el **Explorador de soluciones**, expanda la carpeta **Vistas** y luego la carpeta **Compartido** y haga doble clic en **_Layout.cshtml**. 
   
    ![_Layout.cshtml][cache-layout-cshtml]
2. Cambie el contenido del elemento `title` y sustituya `My ASP.NET Application` por `Contoso Team Stats`, como se muestra en el ejemplo siguiente.

    ```html
    <title>@ViewBag.Title - Contoso Team Stats</title>
    ```


1. En la sección `body`, actualice la primera instrucción `Html.ActionLink` y sustituya `Application name` por `Contoso Team Stats` y `Home` por `Teams`.
   
   * Antes: `@Html.ActionLink("Application name", "Index", "Home", new { area = "" }, new { @class = "navbar-brand" })`
   * Después: `@Html.ActionLink("Contoso Team Stats", "Index", "Teams", new { area = "" }, new { @class = "navbar-brand" })`
     
     ![Cambios de código][cache-layout-cshtml-code]
2. Presione **Ctrl+F5** para compilar y ejecutar la aplicación. Esta versión de la aplicación lee los resultados directamente de la base de datos. Observe las acciones **Crear nuevo**, **Editar**, **Detalles** y **Eliminar** que se han agregado automáticamente a la aplicación mediante el scaffold **Controlador de MVC 5 con vistas que usa Entity Framework**. En la siguiente sección del tutorial agregará Caché en Redis para optimizar el acceso a los datos y proporcionar características adicionales a la aplicación.

![Aplicación de inicio][cache-starter-application]

## <a name="configure-the-application-to-use-redis-cache"></a>Configuración de la aplicación para usar Caché en Redis
En esta sección del tutorial, configurará la aplicación de ejemplo para almacenar y recuperar estadísticas de equipos de Contoso de una instancia de Azure Redis Cache mediante el cliente de caché de [StackExchange.Redis](https://github.com/StackExchange/StackExchange.Redis) .

* [Configurar la aplicación para usar StackExchange.Redis](#configure-the-application-to-use-stackexchangeredis)
* [Actualizar la clase TeamsController para devolver resultados de la caché o la base de datos](#update-the-teamscontroller-class-to-return-results-from-the-cache-or-the-database)
* [Actualizar los métodos Create, Edit y Delete para trabajar con la caché](#update-the-create-edit-and-delete-methods-to-work-with-the-cache)
* [Actualizar la vista de índice de equipos para trabajar con la caché](#update-the-teams-index-view-to-work-with-the-cache)

### <a name="configure-the-application-to-use-stackexchangeredis"></a>Configurar la aplicación para usar StackExchange.Redis
1. Para configurar una aplicación cliente en Visual Studio usando el paquete NuGet StackExchange.Redis, haga clic en **Administrar paquetes NuGet**, **Consola del administrador de paquetes** en el menú **Herramientas**.
2. Ejecute el siguiente comando desde la ventana `Package Manager Console`.
    
    ```
    Install-Package StackExchange.Redis
    ```
   
    El paquete de NuGet se descarga y agrega las referencias de ensamblado requeridas para que la aplicación cliente acceda a Azure Redis Cache con el cliente de caché StackExchange.Redis. Si prefiere usar una versión con nombre seguro de la biblioteca de cliente `StackExchange.Redis`, instale el paquete `StackExchange.Redis.StrongName`.
3. En el **Explorador de soluciones**, expanda la carpeta **Controladores** y haga doble clic en **TeamsController.cs** para abrirlo.
   
    ![Controlador de equipos][cache-teamscontroller]
4. Agregue las dos siguientes instrucciones `using` a **TeamsController.cs**.

    ```csharp   
    using System.Configuration;
    using StackExchange.Redis;
    ```

5. Agregue las dos propiedades siguientes a la clase `TeamsController` .

    ```csharp   
    // Redis Connection string info
    private static Lazy<ConnectionMultiplexer> lazyConnection = new Lazy<ConnectionMultiplexer>(() =>
    {
        string cacheConnection = ConfigurationManager.AppSettings["CacheConnection"].ToString();
        return ConnectionMultiplexer.Connect(cacheConnection);
    });
    
    public static ConnectionMultiplexer Connection
    {
        get
        {
            return lazyConnection.Value;
        }
    }
    ```

6. Cree un archivo en el equipo llamado `WebAppPlusCacheAppSecrets.config` y colóquelo en una ubicación que no se inserte en el repositorio con el código fuente de la aplicación de ejemplo, si decide insertarlo en alguna parte. En este ejemplo, el archivo `AppSettingsSecrets.config` se encuentra en `C:\AppSecrets\WebAppPlusCacheAppSecrets.config`.
   
    Edite el archivo `WebAppPlusCacheAppSecrets.config` y agregue el siguiente contenido. Si ejecuta la aplicación localmente, esta información se utiliza para conectarse a la instancia de Azure Redis Cache. Más adelante en el tutorial aprovisionará una instancia de Azure Redis Cache y actualizará el nombre de la caché y la contraseña. Si no tiene pensado ejecutar la aplicación de ejemplo localmente, puede omitir la creación de este archivo y los pasos siguientes que hacen referencia a él, porque cuando realice la implementación en Azure la aplicación no recupera la información de conexión de la caché para la aplicación web de este archivo, sino de la configuración de la aplicación. Dado que `WebAppPlusCacheAppSecrets.config` no se implementa en Azure con la aplicación, no lo necesita a menos que vaya a ejecutar la aplicación de manera local.

    ```xml
    <appSettings>
      <add key="CacheConnection" value="MyCache.redis.cache.windows.net,abortConnect=false,ssl=true,password=..."/>
    </appSettings>
    ```


1. En el **Explorador de soluciones**, haga doble clic en el archivo **web.config** para abrirlo.
   
    ![Web.config][cache-web-config]
2. Agregue el siguiente atributo `file` al elemento `appSettings`. Si utiliza un nombre de archivo o una ubicación diferente, sustituya los valores por los que se muestran en el ejemplo.
   
   * Antes: `<appSettings>`
   * Después: ` <appSettings file="C:\AppSecrets\WebAppPlusCacheAppSecrets.config">`
     
   El sistema en tiempo de ejecución de ASP.NET combina el contenido del archivo externo con las marcas del elemento `<appSettings>` . El tiempo de ejecución omite el atributo de archivo si no se encuentra el archivo especificado. Los secretos (cadena de conexión a la caché) no se incluyen como parte del código fuente de la aplicación. Al implementar la aplicación web en Azure, el archivo `WebAppPlusCacheAppSecrests.config` no se implementará (que es lo que desea). Hay varias maneras de especificar estos secretos en Azure, y en este tutorial se configuran automáticamente al [aprovisionar los recursos de Azure](#provision-the-azure-resources) en un paso posterior del tutorial. Para más información acerca de cómo trabajar con secretos, consulte [Best practices for deploying passwords and other sensitive data to ASP.NET and Azure App Service](http://www.asp.net/identity/overview/features-api/best-practices-for-deploying-passwords-and-other-sensitive-data-to-aspnet-and-azure)(Procedimientos recomendados para implementar contraseñas y otra información confidencial en ASP.NET y el Servicio de aplicaciones de Azure).

### <a name="update-the-teamscontroller-class-to-return-results-from-the-cache-or-the-database"></a>Actualizar la clase TeamsController para devolver resultados de la caché o la base de datos
En este ejemplo, se pueden recuperar estadísticas de equipos de la base de datos o de la caché. Las estadísticas de equipos se almacenan en la memoria caché como un elemento `List<Team>`serializado y también como un conjunto ordenado mediante tipos de datos de Redis. Al recuperar elementos de un conjunto ordenado, puede recuperar algunos, todos o consultar algunos de ellos. En este ejemplo se consulta el conjunto de los 5 equipos principales ordenado por el número de victorias.

> [!NOTE]
> No es necesario almacenar las estadísticas de equipos en varios formatos en la caché para usar Azure Redis Cache. En este tutorial se utilizan varios formatos para demostrar algunas de las diferentes maneras y diferentes tipos de datos que puede usar para almacenar datos en caché.
> 
> 

1. Agregue las siguientes instrucciones `using` al archivo `TeamsController.cs` encima de las restantes instrucciones `using`.

    ```csharp   
    using System.Diagnostics;
    using Newtonsoft.Json;
    ```

2. Reemplace la implementación del método `public ActionResult Index()` actual por la siguiente implementación.

    ```csharp
    // GET: Teams
    public ActionResult Index(string actionType, string resultType)
    {
        List<Team> teams = null;

        switch(actionType)
        {
            case "playGames": // Play a new season of games.
                PlayGames();
                break;

            case "clearCache": // Clear the results from the cache.
                ClearCachedTeams();
                break;

            case "rebuildDB": // Rebuild the database with sample data.
                RebuildDB();
                break;
        }

        // Measure the time it takes to retrieve the results.
        Stopwatch sw = Stopwatch.StartNew();

        switch(resultType)
        {
            case "teamsSortedSet": // Retrieve teams from sorted set.
                teams = GetFromSortedSet();
                break;

            case "teamsSortedSetTop5": // Retrieve the top 5 teams from the sorted set.
                teams = GetFromSortedSetTop5();
                break;

            case "teamsList": // Retrieve teams from the cached List<Team>.
                teams = GetFromList();
                break;

            case "fromDB": // Retrieve results from the database.
            default:
                teams = GetFromDB();
                break;
        }

        sw.Stop();
        double ms = sw.ElapsedTicks / (Stopwatch.Frequency / (1000.0));

        // Add the elapsed time of the operation to the ViewBag.msg.
        ViewBag.msg += " MS: " + ms.ToString();

        return View(teams);
    }
    ```


1. Agregue los tres métodos siguientes a la clase `TeamsController` para implementar los tipos de acción `playGames`, `clearCache` y `rebuildDB` desde la instrucción switch agregada en el fragmento de código anterior.
   
    El método `PlayGames` actualiza las estadísticas de equipos mediante la simulación de una temporada de juegos, guarda los resultados en la base de datos y borra de la caché los datos ahora obsoletos.

    ```csharp
    void PlayGames()
    {
        ViewBag.msg += "Updating team statistics. ";
        // Play a "season" of games.
        var teams = from t in db.Teams
                    select t;

        Team.PlayGames(teams);

        db.SaveChanges();

        // Clear any cached results
        ClearCachedTeams();
    }
    ```

    El método `RebuildDB` reinicializa la base de datos con el conjunto predeterminado de equipos, genera estadísticas para ellos y borra de la memoria caché los datos ahora obsoletos.

    ```csharp
    void RebuildDB()
    {
        ViewBag.msg += "Rebuilding DB. ";
        // Delete and re-initialize the database with sample data.
        db.Database.Delete();
        db.Database.Initialize(true);

        // Clear any cached results
        ClearCachedTeams();
    }
    ```

    El método `ClearCachedTeams` quita de la memoria caché las estadísticas de equipos almacenadas.

    ```csharp
    void ClearCachedTeams()
    {
        IDatabase cache = Connection.GetDatabase();
        cache.KeyDelete("teamsList");
        cache.KeyDelete("teamsSortedSet");
        ViewBag.msg += "Team data removed from cache. ";
    } 
    ```


1. Agregue los cuatro métodos siguientes a la clase `TeamsController` para implementar las distintas maneras de recuperar las estadísticas de equipos de la memoria caché y la base de datos. Cada uno de estos métodos devuelve un elemento `List<Team>` que luego se muestra mediante la vista.
   
    El método `GetFromDB` lee las estadísticas de equipos de la base de datos.
   
    ```csharp
    List<Team> GetFromDB()
    {
        ViewBag.msg += "Results read from DB. ";
        var results = from t in db.Teams
            orderby t.Wins descending
            select t; 

        return results.ToList<Team>();
    }
    ```

    El método `GetFromList` lee las estadísticas de equipos de la memoria caché como un elemento `List<Team>` serializado. Si se produce un error de caché, las estadísticas de equipos se leen de la base de datos y luego se almacenan en la caché para la próxima vez. En este ejemplo, vamos a usar la serialización de JSON.NET para serializar los objetos .NET a y desde la caché. Para más información, consulte [Trabajar con objetos .NET en la memoria caché](cache-dotnet-how-to-use-azure-redis-cache.md#work-with-net-objects-in-the-cache).

    ```csharp
    List<Team> GetFromList()
    {
        List<Team> teams = null;

        IDatabase cache = Connection.GetDatabase();
        string serializedTeams = cache.StringGet("teamsList");
        if (!String.IsNullOrEmpty(serializedTeams))
        {
            teams = JsonConvert.DeserializeObject<List<Team>>(serializedTeams);

            ViewBag.msg += "List read from cache. ";
        }
        else
        {
            ViewBag.msg += "Teams list cache miss. ";
            // Get from database and store in cache
            teams = GetFromDB();

            ViewBag.msg += "Storing results to cache. ";
            cache.StringSet("teamsList", JsonConvert.SerializeObject(teams));
        }
        return teams;
    }
    ```

    El método `GetFromSortedSet` lee las estadísticas de equipos de un conjunto ordenado almacenado en caché. Si se produce un error de caché, las estadísticas de equipos se leen de la base de datos y se almacenan en la caché como un conjunto ordenado.

    ```csharp
    List<Team> GetFromSortedSet()
    {
        List<Team> teams = null;
        IDatabase cache = Connection.GetDatabase();
        // If the key teamsSortedSet is not present, this method returns a 0 length collection.
        var teamsSortedSet = cache.SortedSetRangeByRankWithScores("teamsSortedSet", order: Order.Descending);
        if (teamsSortedSet.Count() > 0)
        {
            ViewBag.msg += "Reading sorted set from cache. ";
            teams = new List<Team>();
            foreach (var t in teamsSortedSet)
            {
                Team tt = JsonConvert.DeserializeObject<Team>(t.Element);
                teams.Add(tt);
            }
        }
        else
        {
            ViewBag.msg += "Teams sorted set cache miss. ";

            // Read from DB
            teams = GetFromDB();

            ViewBag.msg += "Storing results to cache. ";
            foreach (var t in teams)
            {
                Console.WriteLine("Adding to sorted set: {0} - {1}", t.Name, t.Wins);
                cache.SortedSetAdd("teamsSortedSet", JsonConvert.SerializeObject(t), t.Wins);
            }
        }
        return teams;
    }
    ```

    El método `GetFromSortedSetTop5` lee los 5 equipos principales del conjunto ordenado almacenado en caché. Para comenzar, comprueba que en la memoria caché exista la clave `teamsSortedSet` . Si esta clave no existe, se llama al método `GetFromSortedSet` para leer las estadísticas de equipos y almacenarlas en la memoria caché. Luego, se consulta los 5 equipos principales en el conjunto ordenado en caché, y se devuelven.

    ```csharp
    List<Team> GetFromSortedSetTop5()
    {
        List<Team> teams = null;
        IDatabase cache = Connection.GetDatabase();

        // If the key teamsSortedSet is not present, this method returns a 0 length collection.
        var teamsSortedSet = cache.SortedSetRangeByRankWithScores("teamsSortedSet", stop: 4, order: Order.Descending);
        if(teamsSortedSet.Count() == 0)
        {
            // Load the entire sorted set into the cache.
            GetFromSortedSet();

            // Retrieve the top 5 teams.
            teamsSortedSet = cache.SortedSetRangeByRankWithScores("teamsSortedSet", stop: 4, order: Order.Descending);
        }

        ViewBag.msg += "Retrieving top 5 teams from cache. ";
        // Get the top 5 teams from the sorted set
        teams = new List<Team>();
        foreach (var team in teamsSortedSet)
        {
            teams.Add(JsonConvert.DeserializeObject<Team>(team.Element));
        }
        return teams;
    }
    ```

### <a name="update-the-create-edit-and-delete-methods-to-work-with-the-cache"></a>Actualizar los métodos Create, Edit y Delete para trabajar con la caché
El código de scaffolding que se generó como parte de este ejemplo incluye métodos para agregar, editar y eliminar equipos. Cada vez que se agrega, edita o elimina un equipo, los datos de la caché se vuelven obsoletos. En esta sección podrá modificar estos tres métodos para borrar los equipos almacenados en caché de modo que la caché no pierda la sincronización con la base de datos.

1. Vaya al método `Create(Team team)` en la clase `TeamsController`. Agregue una llamada al método `ClearCachedTeams` , tal como se muestra en el ejemplo siguiente.

    ```csharp
    // POST: Teams/Create
    // To protect from overposting attacks, please enable the specific properties you want to bind to, for 
    // more details see http://go.microsoft.com/fwlink/?LinkId=317598.
    [HttpPost]
    [ValidateAntiForgeryToken]
    public ActionResult Create([Bind(Include = "ID,Name,Wins,Losses,Ties")] Team team)
    {
        if (ModelState.IsValid)
        {
            db.Teams.Add(team);
            db.SaveChanges();
            // When a team is added, the cache is out of date.
            // Clear the cached teams.
            ClearCachedTeams();
            return RedirectToAction("Index");
        }

        return View(team);
    }
    ```


1. Vaya al método `Edit(Team team)` en la clase `TeamsController`. Agregue una llamada al método `ClearCachedTeams` , tal como se muestra en el ejemplo siguiente.

    ```csharp
    // POST: Teams/Edit/5
    // To protect from overposting attacks, please enable the specific properties you want to bind to, for 
    // more details see http://go.microsoft.com/fwlink/?LinkId=317598.
    [HttpPost]
    [ValidateAntiForgeryToken]
    public ActionResult Edit([Bind(Include = "ID,Name,Wins,Losses,Ties")] Team team)
    {
        if (ModelState.IsValid)
        {
            db.Entry(team).State = EntityState.Modified;
            db.SaveChanges();
            // When a team is edited, the cache is out of date.
            // Clear the cached teams.
            ClearCachedTeams();
            return RedirectToAction("Index");
        }
        return View(team);
    }
    ```


1. Vaya al método `DeleteConfirmed(int id)` en la clase `TeamsController`. Agregue una llamada al método `ClearCachedTeams` , tal como se muestra en el ejemplo siguiente.

    ```csharp
    // POST: Teams/Delete/5
    [HttpPost, ActionName("Delete")]
    [ValidateAntiForgeryToken]
    public ActionResult DeleteConfirmed(int id)
    {
        Team team = db.Teams.Find(id);
        db.Teams.Remove(team);
        db.SaveChanges();
        // When a team is deleted, the cache is out of date.
        // Clear the cached teams.
        ClearCachedTeams();
        return RedirectToAction("Index");
    }
    ```


### <a name="update-the-teams-index-view-to-work-with-the-cache"></a>Actualizar la vista de índice de equipos para trabajar con la caché
1. En el **Explorador de soluciones**, expanda la carpeta **Vistas** y luego la carpeta **Teams** y haga doble clic en **Index.cshtml**.
   
    ![Index.cshtml][cache-views-teams-index-cshtml]
2. Cerca de la parte superior del archivo, busque el siguiente elemento de párrafo.
   
    ![Tabla de acciones][cache-teams-index-table]
   
    Este es el vínculo para crear un nuevo equipo. Reemplace el elemento de párrafo por la siguiente tabla. Esta tabla incluye vínculos de acción para crear un nuevo equipo, reproducir una nueva temporada, borrar la caché, recuperar los equipos de la caché en varios formatos, recuperar los equipos de la base de datos y volver a compilar la base de datos con datos de ejemplo nuevos.

    ```html
    <table class="table">
        <tr>
            <td>
                @Html.ActionLink("Create New", "Create")
            </td>
            <td>
                @Html.ActionLink("Play Season", "Index", new { actionType = "playGames" })
            </td>
            <td>
                @Html.ActionLink("Clear Cache", "Index", new { actionType = "clearCache" })
            </td>
            <td>
                @Html.ActionLink("List from Cache", "Index", new { resultType = "teamsList" })
            </td>
            <td>
                @Html.ActionLink("Sorted Set from Cache", "Index", new { resultType = "teamsSortedSet" })
            </td>
            <td>
                @Html.ActionLink("Top 5 Teams from Cache", "Index", new { resultType = "teamsSortedSetTop5" })
            </td>
            <td>
                @Html.ActionLink("Load from DB", "Index", new { resultType = "fromDB" })
            </td>
            <td>
                @Html.ActionLink("Rebuild DB", "Index", new { actionType = "rebuildDB" })
            </td>
        </tr>    
    </table>
    ```


1. Desplácese hasta el final del archivo **Index.cshtml** y agregue el siguiente elemento `tr` de modo que esta sea la última fila de la última tabla del archivo.
   
    ```html
    <tr><td colspan="5">@ViewBag.Msg</td></tr>
    ```
   
    Esta fila muestra el valor de `ViewBag.Msg`, que contiene un informe con el estado de la operación actual. El valor de `ViewBag.Msg` se establece al hacer clic en cualquiera de los vínculos de acción del paso anterior.   
   
    ![Mensaje de estado][cache-status-message]
2. Presione **F6** para compilar el proyecto.

## <a name="provision-the-azure-resources"></a>aprovisionar los recursos de Azure
Para hospedar la aplicación en Azure, primero debe aprovisionar los servicios de Azure que necesita su aplicación. La aplicación de ejemplo de este tutorial utiliza los siguientes servicios de Azure.

* Azure Redis Cache
* Aplicación web de App Service
* SQL Database

Para implementar estos servicios en un grupo de recursos nuevo o existente de su elección, haga clic en el siguiente botón **Implementar en Azure** .

[![Implementación en Azure][deploybutton]](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F201-web-app-redis-cache-sql-database%2Fazuredeploy.json)

Este botón **Implementar en Azure** emplea la plantilla [Create a Web App plus Redis Cache plus SQL Database](https://github.com/Azure/azure-quickstart-templates/tree/master/201-web-app-redis-cache-sql-database) (Crear una aplicación web con Caché en Redis y Base de datos SQL), disponible en [Plantillas de inicio rápido de Azure](https://github.com/Azure/azure-quickstart-templates), para aprovisionar estos servicios y establecer la cadena de conexión para la base de datos SQL y la configuración de la aplicación para la cadena de conexión de Azure Redis Cache.

> [!NOTE]
> Si no tiene ninguna cuenta de Azure, puede [crear una gratis](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=redis_cache_hero) en unos minutos.
> 
> 

Cuando hace clic en el botón **Implementar en Azure** , va al Portal de Azure y se inicia el proceso de creación de los recursos que describe la plantilla.

![Implementar en Azure][cache-deploy-to-azure-step-1]

1. En la sección **Datos básicos**, seleccione tanto la suscripción de Azure que se usará como un grupo de recursos existente, o bien cree un nuevo, y especifique la ubicación del grupo de recursos.
2. En la sección **Configuración**, especifique un **Inicio de sesión de administrador** (no use **admin**), una **Contraseña de inicio de sesión de administrador** y un **Nombre de base de datos**. Los demás parámetros están configurados para un plan de hospedaje gratuito de App Service, y opciones de costo reducido para SQL Database y Azure Redis Cache, que no se ofrecen con un nivel Gratis.

    ![Implementar en Azure][cache-deploy-to-azure-step-2]

3. Después de configurar las opciones que desee, desplácese hasta el final de la página, lea los términos y condiciones, y active la casilla **Acepto los términos y condiciones indicados anteriormente**.
4. Para empezar a aprovisionar los recursos, haga clic en **Comprar**.

Para ver el progreso de la implementación, haga clic en el icono de notificación y en **Implementación iniciada**.

![Implementación iniciada][cache-deployment-started]

Puede ver el estado de la implementación en la hoja **Microsoft.Template** .

![Implementar en Azure][cache-deploy-to-azure-step-3]

Cuando finalice el aprovisionamiento, puede publicar la aplicación en Azure desde Visual Studio.

> [!NOTE]
> Los errores que pueden producirse durante el proceso de aprovisionamiento se muestran en la hoja **Microsoft.Template** . Algunos errores comunes son demasiadas instancias de SQL Server o demasiados planes de hospedaje de App Service gratis por suscripción. Resuelva los errores y reinicie el proceso haciendo clic en **Volver a implementar** en la hoja **Microsoft.Template** o en el botón **Implementar en Azure** de este tutorial.
> 
> 

## <a name="publish-the-application-to-azure"></a>Publicación de la aplicación en Azure
En este paso del tutorial, publicará la aplicación en Azure y la ejecutará en la nube.

1. Haga clic con el botón derecho en el proyecto **ContosoTeamStats** de Visual Studio y elija **Publicar**.
   
    ![Publicar][cache-publish-app]
2. Haga clic en **Microsoft Azure App Service**, elija **Seleccionar existente** y haga clic en **Publicar**.
   
    ![Publicar][cache-publish-to-app-service]
3. Seleccione la suscripción que usó para crear los recursos de Azure, expanda el grupo de recursos que contiene los recursos y seleccione la aplicación web deseada. Si ha utilizado el botón **Implementar en Azure**, el nombre de la aplicación web comienza por **webSite** seguido de varios caracteres.
   
    ![Seleccionar aplicación web][cache-select-web-app]
4. Haga clic en **Aceptar** para comenzar el proceso de publicación. Al cabo de unos minutos, el proceso de publicación finaliza y se inicia un explorador con la aplicación de ejemplo en ejecución. Si recibe un error de DNS al realizar la validación o la publicación y el proceso de aprovisionamiento de los recursos de Azure de la aplicación ha finalizado recientemente, espere un momento y vuelva a intentarlo.
   
    ![Caché agregada][cache-added-to-application]

En la tabla siguiente se describe cada vínculo de acción de la aplicación de ejemplo.

| . | DESCRIPCIÓN |
| --- | --- |
| Crear nuevo |Crear un nuevo equipo. |
| Reproducir temporada |Reproducir una temporada de juegos, actualizar las estadísticas de equipos y borrar de la caché los datos de equipo no actualizados. |
| Borrar caché |Borrar las estadísticas de equipos de la caché. |
| Mostrar de la caché |Recuperar las estadísticas de equipos de la caché. Si se produce un error de caché, cargue las estadísticas de la base de datos y guárdelas en la caché para la próxima vez. |
| Conjunto ordenado de caché |Recuperar las estadísticas de equipos de la caché con un conjunto ordenado. Si se produce un error de caché, cargue las estadísticas de la base de datos y guárdelas en la caché mediante un conjunto ordenado. |
| 5 equipos principales de la caché |Recuperar los 5 equipos principales de la caché mediante un conjunto ordenado. Si se produce un error de caché, cargue las estadísticas de la base de datos y guárdelas en la caché mediante un conjunto ordenado. |
| Cargar de la base de datos |Recuperar las estadísticas de equipos de la base de datos. |
| Recompilar base de datos |Volver a compilar la base de datos y cargarla con los datos de equipo de ejemplo. |
| Editar, Detalles, Eliminar |Editar un equipo, ver los detalles de un equipo, eliminar un equipo. |

Haga clic en algunas de las acciones y experimente con la recuperación de los datos desde distintos orígenes. Observe las diferencias en el tiempo que tardan en completarse las diferentes formas de recuperar los datos: desde la base de datos y desde la caché.

## <a name="delete-the-resources-when-you-are-finished-with-the-application"></a>Eliminación de los recursos después de terminar con la aplicación
Cuando haya terminado con la aplicación del tutorial de ejemplo, puede eliminar los recursos de Azure utilizados para ahorrar costos y recursos. Si utiliza el botón **Implementar en Azure** de la sección [Aprovisionamiento de los recursos de Azure](#provision-the-azure-resources) y todos los recursos están contenidos en el mismo grupo, puede eliminarlos todos juntos en una sola operación si elimina el grupo.

1. Inicie sesión en el [Portal de Azure](https://portal.azure.com) y haga clic en **Grupos de recursos**.
2. Escriba el nombre de su grupo de recursos en el cuadro de texto **Filtrar elementos** .
3. Haga clic en **…** a la derecha de su grupo de recursos.
4. Hacer clic en **Eliminar**.
   
    ![Eliminar][cache-delete-resource-group]
5. Escriba el nombre de su grupo de recursos y haga clic en **Eliminar**.
   
    ![Confirmar eliminación][cache-delete-confirm]

Transcurridos unos segundos, el grupo de recursos y todos los recursos que contiene se eliminan.

> [!IMPORTANT]
> Tenga en cuenta que esta operación es irreversible y que el grupo de recursos y todos los recursos que contiene se eliminarán de forma permanente. Asegúrese de no eliminar por accidente el grupo de recursos o los recursos equivocados. Si ha creado los recursos para hospedar este ejemplo dentro de un grupo de recursos existente, puede eliminar individualmente cada recurso de sus hojas respectivas.
> 
> 

## <a name="run-the-sample-application-on-your-local-machine"></a>Ejecución de la aplicación de ejemplo en la máquina local
Para ejecutar la aplicación localmente en su equipo, necesitará una instancia de Azure Redis Cache en la que almacenar los datos. 

* Si ha publicado la aplicación en Azure como se describe en la sección anterior, puede utilizar la instancia de Azure Redis Cache aprovisionada durante ese paso.
* Si tiene otra instancia de Azure Redis Cache, puede utilizarla para ejecutar este ejemplo localmente.
* Si necesita crear una instancia de Azure Redis Cache, puede seguir los pasos que se describen en [Creación de una caché](cache-dotnet-how-to-use-azure-redis-cache.md#create-a-cache).

Cuando haya seleccionado o creado la memoria caché que desea utilizar, vaya hasta ella en Azure Portal y recupere su [nombre de host](cache-configure.md#properties) y sus [claves de acceso](cache-configure.md#access-keys). Para obtener instrucciones, consulte [Configuración de opciones de la memoria caché en Redis](cache-configure.md#configure-redis-cache-settings).

1. Abra el archivo `WebAppPlusCacheAppSecrets.config` que ha creado durante el paso [Configuración de la aplicación para usar Caché en Redis](#configure-the-application-to-use-redis-cache) de este tutorial en el editor de su elección.
2. Edite el atributo `value` y sustituya `MyCache.redis.cache.windows.net` por el [nombre de host](cache-configure.md#properties) de su caché y especifique la [clave principal o secundaria](cache-configure.md#access-keys) de la memoria caché como contraseña.

    ```xml
    <appSettings>
      <add key="CacheConnection" value="MyCache.redis.cache.windows.net,abortConnect=false,ssl=true,password=..."/>
    </appSettings>
    ```


1. Presione **Ctrl+F5** para ejecutar la aplicación.

> [!NOTE]
> Tenga en cuenta que como la aplicación, incluida la base de datos, se ejecuta localmente y Redis Cache está hospedado en Azure, puede parecer que la memoria caché reduce el rendimiento de la base de datos. Para obtener el mejor rendimiento, la aplicación cliente y la instancia de Azure Redis Cache deben estar en la misma ubicación. 
> 
> 

## <a name="next-steps"></a>pasos siguientes
* Más información sobre la [introducción a ASP.NET MVC 5](http://www.asp.net/mvc/overview/getting-started/introduction/getting-started) en el sitio de [ASP.NET](http://asp.net/).
* Para obtener más ejemplos de creación de una aplicación web ASP.NET en el Servicio de aplicaciones, consulte [Creación e implementación de una aplicación web ASP.NET en Azure App Service](https://github.com/Microsoft/HealthClinic.biz/wiki/Create-and-deploy-an-ASP.NET-web-app-in-Azure-App-Service) desde la [demostración](https://blogs.msdn.microsoft.com/visualstudio/2015/12/08/connectdemos-2015-healthclinic-biz/) [HealthClinic.biz](https://github.com/Microsoft/HealthClinic.biz) 2015 Connect.
  * Para ver más guías rápidas de la demostración de HealthClinic.biz, consulte las [guías rápidas de las herramientas de desarrollador de Azure](https://github.com/Microsoft/HealthClinic.biz/wiki/Azure-Developer-Tools-Quickstarts).
* Más información sobre el enfoque [Code First para una nueva base de datos](https://msdn.microsoft.com/data/jj193542) en Entity Framework que se utiliza en este tutorial.
* Más información sobre las [aplicaciones web de Azure App Service](../app-service/app-service-web-overview.md).
* Más información sobre cómo [supervisar](cache-how-to-monitor.md) la memoria caché en el Portal de Azure.
* Exploración de las características premium de Azure Redis Cache
  
  * [Cómo configurar la persistencia para una instancia Azure Redis Cache Premium](cache-how-to-premium-persistence.md)
  * [Cómo configurar la agrupación en clústeres para una instancia de Azure Redis Cache Premium](cache-how-to-premium-clustering.md)
  * [Cómo configurar la compatibilidad de red virtual para una instancia de Azure Redis Cache Premium](cache-how-to-premium-vnet.md)
  * Consulte el artículo [P+F de Azure Redis Cache](cache-faq.md#what-redis-cache-offering-and-size-should-i-use) para más información sobre el tamaño, el rendimiento y el ancho de banda de las memorias caché de nivel premium.

<!-- IMAGES -->
[cache-starter-application]: ./media/cache-web-app-howto/cache-starter-application.png
[cache-added-to-application]: ./media/cache-web-app-howto/cache-added-to-application.png
[cache-create-project]: ./media/cache-web-app-howto/cache-create-project.png
[cache-select-template]: ./media/cache-web-app-howto/cache-select-template.png
[cache-model-add-class]: ./media/cache-web-app-howto/cache-model-add-class.png
[cache-model-add-class-dialog]: ./media/cache-web-app-howto/cache-model-add-class-dialog.png
[cache-add-controller]: ./media/cache-web-app-howto/cache-add-controller.png
[cache-add-controller-class]: ./media/cache-web-app-howto/cache-add-controller-class.png
[cache-configure-controller]: ./media/cache-web-app-howto/cache-configure-controller.png
[cache-global-asax]: ./media/cache-web-app-howto/cache-global-asax.png
[cache-RouteConfig-cs]: ./media/cache-web-app-howto/cache-RouteConfig-cs.png
[cache-layout-cshtml]: ./media/cache-web-app-howto/cache-layout-cshtml.png
[cache-layout-cshtml-code]: ./media/cache-web-app-howto/cache-layout-cshtml-code.png
[redis-cache-manage-nuget-menu]: ./media/cache-web-app-howto/redis-cache-manage-nuget-menu.png
[redis-cache-stack-exchange-nuget]: ./media/cache-web-app-howto/redis-cache-stack-exchange-nuget.png
[cache-teamscontroller]: ./media/cache-web-app-howto/cache-teamscontroller.png
[cache-web-config]: ./media/cache-web-app-howto/cache-web-config.png
[cache-views-teams-index-cshtml]: ./media/cache-web-app-howto/cache-views-teams-index-cshtml.png
[cache-teams-index-table]: ./media/cache-web-app-howto/cache-teams-index-table.png
[cache-status-message]: ./media/cache-web-app-howto/cache-status-message.png
[deploybutton]: ./media/cache-web-app-howto/deploybutton.png
[cache-deploy-to-azure-step-1]: ./media/cache-web-app-howto/cache-deploy-to-azure-step-1.png
[cache-deploy-to-azure-step-2]: ./media/cache-web-app-howto/cache-deploy-to-azure-step-2.png
[cache-deploy-to-azure-step-3]: ./media/cache-web-app-howto/cache-deploy-to-azure-step-3.png
[cache-deployment-started]: ./media/cache-web-app-howto/cache-deployment-started.png
[cache-publish-app]: ./media/cache-web-app-howto/cache-publish-app.png
[cache-publish-to-app-service]: ./media/cache-web-app-howto/cache-publish-to-app-service.png
[cache-select-web-app]: ./media/cache-web-app-howto/cache-select-web-app.png
[cache-publish]: ./media/cache-web-app-howto/cache-publish.png
[cache-delete-resource-group]: ./media/cache-web-app-howto/cache-delete-resource-group.png
[cache-delete-confirm]: ./media/cache-web-app-howto/cache-delete-confirm.png

