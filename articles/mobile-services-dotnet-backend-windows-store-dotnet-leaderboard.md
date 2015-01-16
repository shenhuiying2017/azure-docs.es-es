<properties urlDisplayName=".NET Client Library" pageTitle="Creación de una aplicación de marcador con el backend .NET de Servicios móviles de Azure" metaKeywords="Azure Mobile Services, Mobile Service .NET client, .NET client" description="Obtenga información acerca de cómo usar Servicios móviles de Azure y centros de notificaciones para enviar notificaciones de inserción a la aplicación de la Tienda Windows." documentationCenter="Mobile" title="Creating a Leaderboard App with Azure Mobile Services .NET Backend" authors="mwasson" solutions="" manager="dwrede" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-windows-store" ms.devlang="dotnet" ms.topic="article" ms.date="09/23/2014" ms.author="mwasson" />

# Creación de una aplicación de marcador con el backend .NET de Servicios móviles de Azure

En este tutorial se muestra cómo compilar una aplicación de la Tienda Windows con el backend .NET de Servicios móviles de Azure. Servicios móviles de Azure proporciona un backend seguro y escalable con autenticación, supervisión, notificaciones de inserción y otras características integradas, además de una biblioteca de cliente multiplataforma para compilar aplicaciones móviles. El backend .NET de Servicios móviles se basa en [ASP.NET Web API](http://asp.net/web-api) y ofrece a los desarrolladores de .NET una forma excepcional de crear API REST.   

+ [Información general] 
+ [Acerca de la aplicación de ejemplo] 
+ [Adición de modelos de datos]
+ [Adición de controladores Web API]
+ [Uso de un objeto DTO para devolver entidades relacionadas]
+ [Definición de una API personalizada para enviar puntuaciones]
+ [Creación de una aplicación de la Tienda Windows]
+ [Adición de clases de modelo]
+ [Creación de un modelo de vista]
+ [Adición de una instancia de MobileServiceClient]
+ [Creación de la página principal]
+ [Publicación del servicio móvil]
+ [Pasos siguientes]

## Información general

Web API es un marco de código abierto que ofrece a los desarrolladores de .NET un forma excepcional de crear API REST. Puede hospedar una solución Web API en Sitios web de Azure, en Servicios móviles de Azure usando el backend .NET o, incluso, semihospedado en un proceso personalizado. Servicios móviles es un entorno de hospedaje diseñado especialmente para aplicaciones móviles. Si hospeda su servicio Web API en Servicios móviles, obtiene las siguientes ventajas además del almacenamiento de datos:

- Autenticación integrada con proveedores sociales y Azure Active Directory (AAD). 
- Notificaciones de inserción a aplicaciones mediante servicios de notificación específicos del dispositivo.
- Conjunto completo de bibliotecas de cliente que facilita el acceso al servicio desde cualquier aplicación. 
- Funciones de registro y diagnóstico integradas.

En este tutorial, aprenderá lo siguiente:

- Creación de una API REST usando Servicios móviles de Azure.
- Publicación del servicio en Azure.
- Creación de una aplicación de la Tienda Windows que utilice el servicio.
- Uso de Entity Framework (EF) para crear relaciones de clave externa y objetos de transferencia de datos (DTO).
- Uso de ASP.NET Web API para definir una API personalizada.

Este tutorial utiliza [Visual Studio 2013 Update 3](http://go.microsoft.com/fwlink/p/?LinkID=390465). 


## Acerca de la aplicación de ejemplo

Un *marcador* muestra una lista de jugadores de un juego junto con sus puntuaciones y la clasificación de cada jugador. Un marcador puede formar parte de un juego más grande o ser una aplicación aparte. Un marcador es una aplicación real, pero lo suficientemente sencilla para un tutorial. Esta es una captura de pantalla de la aplicación:

![][1]

Para mantener la aplicación sencilla, no hay un juego real, sino que puede agregar jugadores y enviar una puntuación para cada jugador. Cuando envía una puntuación, el servicio móvil calcula las nuevas clasificaciones. En el backend, el servicio móvil crea una base de datos con dos tablas:

- Player. Contiene el identificador del jugador y su nombre.
- PlayerRank. Contiene la puntuación y la clasificación de un jugador.

PlayerRank tiene una clave externa a Player. Cada jugador tiene un PlayerRank o ninguno.

En una aplicación de marcador real, PlayerRank puede tener también un identificador de la partida, de forma que un jugador podría enviar puntuaciones de más de una partida.

![][2]

La aplicación de cliente puede realizar todas las operaciones de CRUD en Players. Puede leer o eliminar entidades PlayerRank, pero no puede crearlas ni actualizarlas directamente, porque es el servicio el que calcula el valor de la clasificación. El cliente envía una puntuación y el servicio actualiza las clasificaciones de todos los jugadores.

Descargue el proyecto completo [aquí](http://code.msdn.microsoft.com/Leaderboard-App-with-Azure-9acf63af).


## Creación del proyecto

Inicie Visual Studio y cree un proyecto nuevo de aplicación web ASP.NET. Dele al proyecto el nombre "Leaderboard" (Marcador).

![][3]

En Visual Studio 2013 Update 3, el proyecto de aplicación web ASP.NET incluye ahora una plantilla para Servicios móviles de Microsoft Azure. Seleccione esta plantilla y haga clic en **Aceptar**.

![][4]
 
La plantilla del proyecto incluye un controlador y un objeto de datos de ejemplo.  

![][5]
 
No son necesarios para el tutorial, así que puede eliminarlos del proyecto. Quite también las referencias a TodoItem en WebApiConfig.cs y LeaderboardContext.cs.

## Adición de modelos de datos

Va a usar [EF Code First](http://msdn.microsoft.com/en-US/data/ee712907#codefirst) para definir las tablas de la base de datos. En la carpeta DataObjects, agregue una clase denominada `Player`.

	using Microsoft.WindowsAzure.Mobile.Service;
	
	namespace Leaderboard.DataObjects
	{
	    public class Player : EntityData
	    {
	        public string Name { get; set; }
	    }
	}

Agregue otra clase denominada `PlayerRank`.

	using Microsoft.WindowsAzure.Mobile.Service;
	using System.ComponentModel.DataAnnotations.Schema;
	
	namespace Leaderboard.DataObjects
	{
	    public class PlayerRank : EntityData
	    {
	        public int Score { get; set; }
	        public int Rank { get; set; }
	
	        [ForeignKey("Id")]
	        public virtual Player Player { get; set; }
	    }
	}

Observe que ambas clases se heredan de la clase **EntityData**. Al derivarse de **EntityData**, facilitan a la aplicación el uso de los datos con la biblioteca de cliente multiplataforma para Servicios móviles de Azure. **EntityData** facilita también que la aplicación pueda [controlar los conflictos de escritura en la base de datos](http://azure.microsoft.com/es-es/documentation/articles/mobile-services-windows-store-dotnet-handle-database-conflicts/).

La clase `PlayerRank` tiene una [propiedad navigation](http://msdn.microsoft.com/es-es/data/jj713564.aspx) que apunta a la entidad `Player` relacionada. El atributo **[ForeignKey]** le indica a EF que la propiedad `Player` representa una clave externa.

# Adición de controladores Web API

A continuación, agregará controladores API web para `Player` y `PlayerRank`. En lugar de controladores Web API estándar, va a agregar un tipo especial de controlador denominado *controlador de tabla*, diseñado específicamente para Servicios móviles de Azure.

Haga clic con el botón secundario en la carpeta Controllers, seleccione Agregar y elija Nuevo elemento de scaffolding.

![][6] 

En el cuadro de diálogo **Agregar scaffold**, expanda **Común** a la izquierda y seleccione **Servicios móviles de Windows Azure**. Después seleccione **Controlador de tabla de Servicios móviles de Microsoft Azure**. Haga clic en **Agregar**.

![][7] 
 
En el cuadro de diálogo **Agregar controlador:**:

1.	En **Clase de modelo**, seleccione Player. 
2.	En **Clase de contexto de datos**, seleccione MobileServiceContext.
3.	Dele el nombre "PlayerController" al controlador.
4.	Haga clic en **Agregar**.


Este paso agrega un archivo denominado PlayerController.cs al proyecto.

![][8] 

El controlador se deriva de **TableController<T>**. Esta clase se hereda de **ApiController**, pero es específica para Servicios móviles de Azure.
 
- Enrutamiento: La ruta predeterminada para una clase **TableController** es `/tables/{nombre_tabla}/{id}`, donde *nombre_tabla* coincide con el nombre de entidad. Por tanto, la ruta para el controlador Player es */tables/player/{id}*. Esta convención de enrutamiento hace que **TableController** sea coherente con la [API REST](http://msdn.microsoft.com/es-es/library/azure/jj710104.aspx) de Servicios móviles.
- Acceso a datos: Para las operaciones de base de datos, la clase **TableController** usa la interfaz **IDomainManager**, que define una abstracción para el acceso a los datos.  La técnica de scaffolding utiliza **EntityDomainManager**, que es una implementación concreta de **IDomainManager**, que contiene un contexto de EF. 

Ahora agregue un segundo controlador para entidades PlayerRank. Siga los mismos pasos, pero elija PlayerRank para la clase de modelo. Utilice la misma clase de contexto de datos; no cree una nueva. Dele el nombre "PlayerRankController" al controlador.

## Uso de un objeto DTO para devolver entidades relacionadas

Recuerde que `PlayerRank` tiene una entidad `Player` relacionada: 

    public class PlayerRank : EntityData
    {
        public int Score { get; set; }
        public int Rank { get; set; }

        [ForeignKey("Id")]
        public virtual Player Player { get; set; }
    }

La biblioteca de cliente de Servicios móviles no admite propiedades de navegación y, por tanto, no se serializarán. Por ejemplo, esta es la respuesta en HTTP puro para GET `/tables/PlayerRank`:

	HTTP/1.1 200 OK
	Cache-Control: no-cache
	Pragma: no-cache
	Content-Length: 97
	Content-Type: application/json; charset=utf-8
	Expires: 0
	Server: Microsoft-IIS/8.0
	Date: Mon, 21 Apr 2014 17:58:43 GMT
	
	[{"id":"1","rank":1,"score":150},{"id":"2","rank":3,"score":100},{"id":"3","rank":1,"score":150}]

Observe que `Player` no está incluido en el gráfico de objeto. Para incluir player, podemos definir un *objeto de transferencia de datos* (DTO) para dejar el gráfico de objeto sin formato. 

Un DTO es un objeto que define cómo se envían los datos a través de la red. Los DTO son útiles cuando se quiere que el formato sea diferente al modelo de la base de datos. Para crear un DTO para `PlayerRank`, agregue una clase nueva denominada `PlayerRankDto` en la carpeta DataObjects.

	namespace Leaderboard.DataObjects
	{
	    public class PlayerRankDto
	    {
	        public string Id { get; set; }
	        public string PlayerName { get; set; }
	        public int Score { get; set; }
	        public int Rank { get; set; }
	    }
	}

En la clase `PlayerRankController`, utilizaremos el método **Select** LINQ para convertir instancias de `PlayerRank` en instancias de `PlayerRankDto`. Actualice los métodos de controlador `GetAllPlayerRank` y `GetPlayerRank` del siguiente modo:

	// GET tables/PlayerRank
	public IQueryable<PlayerRankDto> GetAllPlayerRank()
	{
	    return Query().Select(x => new PlayerRankDto()
	    {
	        Id = x.Id,
	        PlayerName = x.Player.Name,
	        Score = x.Score,
	        Rank = x.Rank
	    });
	}
	
	// GET tables/PlayerRank/48D68C86-6EA6-4C25-AA33-223FC9A27959
	public SingleResult<PlayerRankDto> GetPlayerRank(string id)
	{
	    var result = Lookup(id).Queryable.Select(x => new PlayerRankDto()
	    {
	        Id = x.Id,
	        PlayerName = x.Player.Name,
	        Score = x.Score,
	        Rank = x.Rank
	    });
	
	    return SingleResult<PlayerRankDto>.Create(result);
	}

Con estos cambios, los dos métodos GET devuelven objetos `PlayerRankDto` al cliente. La propiedad `PlayerRankDto.PlayerName` está establecida en el nombre del reproductor. Este es un ejemplo de respuesta después de hacer este cambio:

	HTTP/1.1 200 OK
	Cache-Control: no-cache
	Pragma: no-cache
	Content-Length: 160
	Content-Type: application/json; charset=utf-8
	Expires: 0
	Server: Microsoft-IIS/8.0
	Date: Mon, 21 Apr 2014 19:57:08 GMT
	
	[{"id":"1","playerName":"Alice","score":150,"rank":1},{"id":"2","playerName":"Bob","score":100,"rank":3},{"id":"3","playerName":"Charles","score":150,"rank":1}]

Observe que la carga de JSON incluye ahora los nombres de jugador.

En lugar de usar instrucciones Select de LINQ, tiene también la opción de usar AutoMapper. Esta opción requiere algún código más de configuración, pero permite la asignación automática de entidades de dominio a objetos DTO. Para obtener más información, consulte [Asignación entre tipos de base de datos y tipos de cliente en el backend .NET usando AutoMapper](http://blogs.msdn.com/b/azuremobile/archive/2014/05/19/mapping-between-database-types-and-client-type-in-the-net-backend-using-automapper.aspx).

## Definición de una API personalizada para enviar puntuaciones

La entidad `PlayerRank` incluye una propiedad `Rank`. El servidor calcula este valor y no queremos que lo establezcan los clientes. En cambio, los clientes usarán una API personalizada para enviar la puntuación de un jugador.  Cuando el servidor obtiene una nueva puntuación, actualiza todas las clasificaciones de los jugadores.

En primer lugar, agregue una clase denominada `PlayerScore` a la carpeta DataObjects.

	namespace Leaderboard.DataObjects
	{
	    public class PlayerScore
	    {
	        public string PlayerId { get; set; }
	        public int Score { get; set; }
	    }
	}

En la clase `PlayerRankController`, mueva la variable `MobileServiceContext` desde el constructor a una variable de clase:

    public class PlayerRankController : TableController<PlayerRank>
    {
        // Add this:
        MobileServiceContext context = new MobileServiceContext();

        protected override void Initialize(HttpControllerContext controllerContext)
        {
            base.Initialize(controllerContext);

            // Delete this:
            // MobileServiceContext context = new MobileServiceContext();
            DomainManager = new EntityDomainManager<PlayerRank>(context, Request, Services);
        }


Elimine los siguientes métodos de `PlayerRankController`:

- `PatchPlayerRank` 
- `PostPlayerRank` 
- `DeletePlayerRank`

Después agregue el siguiente código a `PlayerRankController`:

    [Route("api/score")]
    public async Task<IHttpActionResult> PostPlayerScore(PlayerScore score)
    {
        // Does this player exist?
        var count = context.Players.Where(x => x.Id == score.PlayerId).Count();
        if (count < 1)
        {
            return BadRequest();
        }

        // Try to find the PlayerRank entity for this player. If not found, create a new one.
        PlayerRank rank = await context.PlayerRanks.FindAsync(score.PlayerId);
        if (rank == null)
        {
            rank = new PlayerRank { Id = score.PlayerId };
            rank.Score = score.Score;
            context.PlayerRanks.Add(rank);
        }
        else
        {
            rank.Score = score.Score;
        }

        await context.SaveChangesAsync();

        // Update rankings
        // See http://stackoverflow.com/a/575799
        const string updateCommand =
            "UPDATE r SET Rank = ((SELECT COUNT(*)+1 from {0}.PlayerRanks " +
            "where Score > (select score from {0}.PlayerRanks where Id = r.Id)))" +
            "FROM {0}.PlayerRanks as r";

        string command = String.Format(updateCommand, ServiceSettingsDictionary.GetSchemaName());
        await context.Database.ExecuteSqlCommandAsync(command);

        return Ok();
    }

El método `PostPlayerScore` toma una instancia de `PlayerScore` como entrada. El cliente enviará el elemento `PlayerScore` en una solicitud HTTP POST. El método hace lo siguiente:

1.	Agrega un nuevo `PlayerRank` para el jugador, si no hay todavía ninguno en la base de datos.
2.	Actualiza la puntuación del jugador.
3.	Ejecuta una consulta SQL que actualiza por lotes todas las clasificaciones de los jugadores.

Atributo **[Route]** para definir una ruta personalizada para este método:

	[Route("api/score")]

También puede poner el método en un controlador aparte. No hay ninguna ventaja particular de cualquier de las maneras, solo depende de cómo desea organizar el código.
Para obtener más información sobre el atributo **[Route]**, vea [Enrutamiento de atributos en la API web](http://www.asp.net/web-api/overview/web-api-routing-and-actions/attribute-routing-in-web-api-2).

## Creación de una aplicación de la Tienda Windows

En esta sección, se describe la aplicación de la Tienda Windows que usa el servicio móvil. Sin embargo, no nos centraremos demasiado en el código XAML ni la interfaz de usuario. Más bien nos centraremos en la lógica de la aplicación. Puede descargar el proyecto completo [aquí](http://code.msdn.microsoft.com/Leaderboard-App-with-Azure-9acf63af).

Agregue un nuevo proyecto de aplicación de la Tienda Windows a la solución. Aquí hemos usado la plantilla Aplicación vacía (Windows). 

![][10]
 
Utilice el Administrador de paquetes de NuGet para agregar la biblioteca de cliente de Servicios móviles. En Visual Studio, en el menú **Herramientas**, seleccione **Administrador de paquetes de NuGet**. Seleccione **Consola del Administrador de paquetes**. En la ventana Consola del Administrador de paquetes, escriba el siguiente comando.

	Install-Package WindowsAzure.MobileServices -Project LeaderboardApp

El modificador -Project especifica el proyecto donde se debe instalar el paquete.

## Adición de clases de modelo

Cree una carpeta denominada Models y agregue las siguientes clases:

	namespace LeaderboardApp.Models
	{
	    public class Player
	    {
	        public string Id { get; set; }
	        public string Name { get; set; }
	    }
	
	    public class PlayerRank
	    {
	        public string Id { get; set; }
	        public string PlayerName { get; set; }
	        public int Score { get; set; }
	        public int Rank { get; set; }
	    }
	
	    public class PlayerScore
	    {
	        public string PlayerId { get; set; }
	        public int Score { get; set; }
	    }
	}

Estas clases corresponden directamente a entidades de datos en el servicio móvil.
 
## Creación de un modelo de vista

Model-View-ViewModel (MVVM) es una variante de Model-View-Controller (MVC). El modelo MVVM permite separar la lógica de la aplicación de la presentación.

- Representa los datos del dominio (jugador, clasificación y puntuación).
- El modelo de vista es una representación abstracta de la vista. 
- La vista muestra el modelo de vista y envía la entrada de usuario al modelo de vista. Para una aplicación de la Tienda Windows, la vista se define en XAML.

![][11] 

Agregue una clase denominada `LeaderboardViewModel`.

	using LeaderboardApp.Models;
	using Microsoft.WindowsAzure.MobileServices;
	using System.ComponentModel;
	using System.Net.Http;
	using System.Threading.Tasks;
	
	namespace LeaderboardApp.ViewModel
	{
	    class LeaderboardViewModel : INotifyPropertyChanged
	    {
	        MobileServiceClient _client;
	
	        public LeaderboardViewModel(MobileServiceClient client)
	        {
	            _client = client;
	        }
	    }
	}

Implemente **INotifyPropertyChanged** en el modelo de vista para que este pueda participar en el enlace de datos. 

    class LeaderboardViewModel : INotifyPropertyChanged
    {
        MobileServiceClient _client;

        public LeaderboardViewModel(MobileServiceClient client)
        {
            _client = client;
        }

        // New code:
        // INotifyPropertyChanged implementation
        public event PropertyChangedEventHandler PropertyChanged;

        public void NotifyPropertyChanged(string propertyName)
        {
            if (PropertyChanged != null)
            {
                PropertyChanged(this,
                    new PropertyChangedEventArgs(propertyName));
            }
        }    
    }

Después, agregue propiedades observables. El código XAML enlazará estas propiedades a datos. 

    class LeaderboardViewModel : INotifyPropertyChanged
    {
        // ...

        // New code:
        // View model properties
        private MobileServiceCollection<Player, Player> _Players;
        public MobileServiceCollection<Player, Player> Players
        {
            get { return _Players; }
            set
            {
                _Players = value;
                NotifyPropertyChanged("Players");
            }
        }

        private MobileServiceCollection<PlayerRank, PlayerRank> _Ranks;
        public MobileServiceCollection<PlayerRank, PlayerRank> Ranks
        {
            get { return _Ranks; }
            set
            {
                _Ranks = value;
                NotifyPropertyChanged("Ranks");
            }
        }

        private bool _IsPending;
        public bool IsPending
        {
            get { return _IsPending; }
            set
            {
                _IsPending = value;
                NotifyPropertyChanged("IsPending");
            }
        }

        private string _ErrorMessage = null;
        public string ErrorMessage
        {
            get { return _ErrorMessage; }
            set
            {
                _ErrorMessage = value;
                NotifyPropertyChanged("ErrorMessage");
            }
        }
    }

La propiedad `IsPending` es true cuando hay una operación asincrónica pendiente en el servicio. La propiedad `ErrorMessage` retiene cualquier mensaje de error del servicio. 

Finalmente, agregue métodos que llamen a la capa de servicio. 

    class LeaderboardViewModel : INotifyPropertyChanged
    {
        // ...

        // New code:
        // Service operations
        public async Task GetAllPlayersAsync()
        {
            IsPending = true;
            ErrorMessage = null;

            try
            {
                IMobileServiceTable<Player> table = _client.GetTable<Player>();
                Players = await table.OrderBy(x => x.Name).ToCollectionAsync();
            }
            catch (MobileServiceInvalidOperationException ex)
            {
                ErrorMessage = ex.Message;
            }
            catch (HttpRequestException ex2)
            {
                ErrorMessage = ex2.Message;
            }
            finally
            {
                IsPending = false;
            }
        }

        public async Task AddPlayerAsync(Player player)
        {
            IsPending = true;
            ErrorMessage = null;

            try
            {
                IMobileServiceTable<Player> table = _client.GetTable<Player>();
                await table.InsertAsync(player);
                Players.Add(player);
            }
            catch (MobileServiceInvalidOperationException ex)
            {
                ErrorMessage = ex.Message;
            }
            catch (HttpRequestException ex2)
            {
                ErrorMessage = ex2.Message;
            }
            finally
            {
                IsPending = false;
            }
        }

        public async Task SubmitScoreAsync(Player player, int score)
        {
            IsPending = true;
            ErrorMessage = null;

            var playerScore = new PlayerScore()
            {
                PlayerId = player.Id,
                Score = score
            }; 
            
            try
            {
                await _client.InvokeApiAsync<PlayerScore, object>("score", playerScore);
                await GetAllRanksAsync();
            }
            catch (MobileServiceInvalidOperationException ex)
            {
                ErrorMessage = ex.Message;
            }
            catch (HttpRequestException ex2)
            {
                ErrorMessage = ex2.Message;
            }
            finally
            {
                IsPending = false;
            }
        }

        public async Task GetAllRanksAsync()
        {
            IsPending = true;
            ErrorMessage = null;

            try
            {
                IMobileServiceTable<PlayerRank> table = _client.GetTable<PlayerRank>();
                Ranks = await table.OrderBy(x => x.Rank).ToCollectionAsync();
            }
            catch (MobileServiceInvalidOperationException ex)
            {
                ErrorMessage = ex.Message;
            }
            catch (HttpRequestException ex2)
            {
                ErrorMessage = ex2.Message;
            }
            finally
            {
                IsPending = false;
            }
         }    
    }

## Adición de una instancia de MobileServiceClient

Abra el archivo App.xaml.cs y agregue una instancia de **MobileServiceClient** a la clase `App`.

	// New code:
	using Microsoft.WindowsAzure.MobileServices;
	
	namespace LeaderboardApp
	{
	    sealed partial class App : Application
	    {
	        // New code.
	        // TODO: Replace 'port' with the actual port number.
	        const string serviceUrl = "http://localhost:port/";
	        public static MobileServiceClient MobileService = new MobileServiceClient(serviceUrl);
	
	
	        // ...
	    }
	}

Si la depuración se hace de forma local, el servicio móvil se ejecuta en IIS Express. Visual Studio asigna un número de puerto aleatorio, de forma que la dirección URL local es http://localhost:*puerto*, donde *puerto* es el número de puerto. Para obtener el número de puerto, inicie el servicio en Visual Studio presionando F5 para depurarlo. Visual Studio inicia un explorador y navega a la dirección URL del servicio.  También puede buscar la dirección URL local en las propiedades del proyecto, en **Web**.

## Creación de la página principal

En la página principal, agregue una instancia del modelo de vista. Después, establezca el modelo de vista como **DataContext** para la página.

    public sealed partial class MainPage : Page
    {
        // New code:
        LeaderboardViewModel viewModel = new LeaderboardViewModel(App.MobileService);

        public MainPage()
        {
            this.InitializeComponent();
            // New code:
            this.DataContext = viewModel;
        }

       // ...


Como hemos mencionado antes, no vamos a mostrar todo el código XAML de la aplicación. Una ventaja del modelo MVVM es separar la presentación de la lógica de la aplicación, de forma que es fácil cambiar la interfaz de usuario si no le agrada la aplicación de ejemplo.

La lista de jugadores se muestra en un control **ListBox**:

	<ListBox Width="200" Height="400" x:Name="PlayerListBox" 
	    ItemsSource="{Binding Players}" DisplayMemberPath="Name"/>

Las clasificaciones se muestran en un control **ListView**:

	<ListView x:Name="RankingsListView" ItemsSource="{Binding Ranks}" SelectionMode="None">
	    <!-- Header and styles not shown -->
	    <ListView.ItemTemplate>
	        <DataTemplate>
	            <Grid>
	                <Grid.ColumnDefinitions>
	                    <ColumnDefinition Width="*"/>
	                    <ColumnDefinition Width="2*"/>
	                    <ColumnDefinition Width="*"/>
	                </Grid.ColumnDefinitions>
	                <TextBlock Text="{Binding Path=Rank}"/>
	                <TextBlock Text="{Binding Path=PlayerName}" Grid.Column="1"/>
	                <TextBlock Text="{Binding Path=Score}" Grid.Column="2"/>
	            </Grid>
	        </DataTemplate>
	    </ListView.ItemTemplate>
	</ListView>

Todo el enlace de datos tiene lugar a través del modelo de vista.

## Publicación del servicio móvil

En este paso, va a publicar su servicio móvil en Microsoft Azure y va a modificar la aplicación para que use el servicio en directo.

En el Explorador de soluciones, haga clic con el botón secundario en el proyecto de marcador (Leaderboard) y seleccione **Publicar**.
 
![][12]

En el cuadro de diálogo **Publicar**, haga clic en **Servicios móviles de Microsoft Azure**.

![][13]
 
Si no ha iniciado sesión con su cuenta de Azure, haga clic en **Iniciar sesión**.

![][14]


Seleccione un servicio móvil o haga clic en **Nuevo** para crear uno nuevo. Después, haga clic en **Aceptar** para publicar el servicio.

![][15]
 
El proceso de publicación crea automáticamente la base de datos. No es necesario que configure una cadena de conexión.

Ahora ya puede conectar la aplicación de marcador al servicio en directo. Necesita dos cosas:

- La dirección URL del servicio
- La clave de la aplicación

Puede obtener ambas en el Portal de administración de Azure. En el Portal de administración, haga clic en **Servicios móviles** y elija el servicio móvil. En la ficha Panel, aparece la dirección URL del servicio. Para obtener la clave de la aplicación, haga clic en **Administrar claves**.

![][16]
 
En el cuadro de diálogo **Administrar claves de acceso**, copie el valor de la clave de la aplicación.

![][17]

 
Pase la dirección URL del servicio y la clave de la aplicación al constructor **MobileServiceClient**.

    sealed partial class App : Application
    {
        // TODO: Replace these strings with the real URL and key.
        const string serviceUrl = "https://yourapp.azure-mobile.net/";
        const string appKey = "YOUR ACCESSS KEY";

        public static MobileServiceClient MobileService = new MobileServiceClient(serviceUrl, appKey);

       // ...

Ahora, cuando ejecuta la aplicación, se comunica con el servicio real. 

## Pasos siguientes

* [Más información acerca de Servicios móviles de Azure]
* [Más información acerca de Web API]
* [Control de conflictos de escritura de bases de datos]
* [Adición de notificaciones de inserción]; por ejemplo, cuando alguien agrega un jugador nuevo o actualiza una puntuación.
* [Introducción a la autenticación]

<!-- Anchors. -->
[Información general]: #overview
[Acerca de la aplicación de ejemplo]: #about-the-sample-app
[Creación del proyecto]: #create-the-project
[Adición de modelos de datos]: #add-data-models
[Adición de controladores Web API]: #add-web-api-controllers
[Uso de un objeto DTO para devolver entidades relacionadas]: #use-a-dto-to-return-related-entities
[Definición de una API personalizada para enviar puntuaciones]: #define-a-custom-api-to-submit-scores
[Creación de una aplicación de la Tienda Windows]: #create-the-windows-store-app
[Adición de clases de modelo]: #add-model-classes
[Creación de un modelo de vista]: #create-a-view-model
[Adición de una instancia de MobileServiceClient]: #add-a-mobileserviceclient-instance
[Creación de la página principal]: #create-the-main-page
[Publicación del servicio móvil]: #publish-your-mobile-service
[Pasos siguientes]: #next-steps

<!-- Images. -->

[1]: ./media/mobile-services-dotnet-backend-windows-store-dotnet-leaderboard/01leaderboard.png
[2]: ./media/mobile-services-dotnet-backend-windows-store-dotnet-leaderboard/02leaderboard.png
[3]: ./media/mobile-services-dotnet-backend-windows-store-dotnet-leaderboard/03leaderboard.png
[4]: ./media/mobile-services-dotnet-backend-windows-store-dotnet-leaderboard/04leaderboard.png
[5]: ./media/mobile-services-dotnet-backend-windows-store-dotnet-leaderboard/05leaderboard.png
[6]: ./media/mobile-services-dotnet-backend-windows-store-dotnet-leaderboard/06leaderboard.png
[7]: ./media/mobile-services-dotnet-backend-windows-store-dotnet-leaderboard/07leaderboard.png
[8]: ./media/mobile-services-dotnet-backend-windows-store-dotnet-leaderboard/08leaderboard.png
[9]: ./media/mobile-services-dotnet-backend-windows-store-dotnet-leaderboard/09leaderboard.png
[10]: ./media/mobile-services-dotnet-backend-windows-store-dotnet-leaderboard/10leaderboard.png
[11]: ./media/mobile-services-dotnet-backend-windows-store-dotnet-leaderboard/11leaderboard.png
[12]: ./media/mobile-services-dotnet-backend-windows-store-dotnet-leaderboard/12leaderboard.png
[13]: ./media/mobile-services-dotnet-backend-windows-store-dotnet-leaderboard/13leaderboard.png
[14]: ./media/mobile-services-dotnet-backend-windows-store-dotnet-leaderboard/14leaderboard.png
[15]: ./media/mobile-services-dotnet-backend-windows-store-dotnet-leaderboard/15leaderboard.png
[16]: ./media/mobile-services-dotnet-backend-windows-store-dotnet-leaderboard/16leaderboard.png
[17]: ./media/mobile-services-dotnet-backend-windows-store-dotnet-leaderboard/17leaderboard.png

<!-- URLs. -->

[Más información acerca de Servicios móviles de Azure]: /es-es/develop/mobile/resources/
[Más información acerca de Web API]: http://asp.net/web-api
[Control de conflictos de escritura de bases de datos]: /es-es/documentation/articles/mobile-services-windows-store-dotnet-handle-database-conflicts/
[Adición de notificaciones de inserción]: /es-es/documentation/articles/notification-hubs-windows-store-dotnet-get-started/
[Introducción a la autenticación]: /es-es/develop/mobile/tutorials/get-started-with-users-dotnet
