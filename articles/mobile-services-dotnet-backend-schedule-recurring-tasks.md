<properties  pageTitle="Schedule Backend Tasks with Scheduler - Mobile Services" metaKeywords="" description="Use the Windows Azure Mobile Services Scheduler to schedule jobs for your mobile app." metaCanonical="" services="mobile-services" documentationCenter="Mobile" title="Schedule recurring jobs in Mobile Services" authors="glenga"  solutions="mobile" writer="" manager="" editor=""  />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-multiple" ms.devlang="multiple" ms.topic="article" ms.date="01/01/1900" ms.author="glenga"></tags>

# Programación de trabajos periódicos en Servicios móviles

<div class="dev-center-tutorial-subselector">
    <a href="/es-es/documentation/articles/mobile-services-dotnet-backend-schedule-recurring-tasks/" title="Back-end de .NET" class="current">Back-end de .NET</a> | <a href="/es-es/documentation/articles/mobile-services-schedule-recurring-tasks/"  title="Back-end de JavaScript" >Back-end de JavaScript</a>
</div>

Este tema le muestra cómo usar la funcionalidad del programador de trabajos en el Portal de administración para definir el código de script de servidor que se ejecuta según el programa que establezca. En este caso, se realiza una comprobación periódica del script con un servicio remoto (Twitter) y se almacenan los resultados en una nueva tabla. Entre las demás tareas periódicas que pueden programarse se incluyen las siguientes:

-   Archivado de registros de datos antiguos o duplicados.
-   Solicitud y almacenamiento de datos externos, como tweets, entradas RSS e información de ubicación.
-   Procesamiento o cambio de tamaño de imágenes almacenadas.

Este tutorial le guiará por los siguientes pasos relativos al uso del programador de trabajos para crear un trabajo programado que solicite datos de tweets de Twitter y almacene los tweets en una nueva tabla de actualizaciones:

-   [Registro para obtener acceso a Twitter y almacenamiento de credenciales][]
-   [Descarga e instalación de la biblioteca LINQ to Twitter][]
-   [Creación de la nueva tabla de actualizaciones][]
-   [Creación de un nuevo trabajo programado][]
-   [Prueba local del trabajo programado][]
-   [Publicación del servicio y registro del trabajo][]

> [WACOM.NOTE]Este tutorial usa la biblioteca de terceros LINQ to Twitter a fin de simplificar el acceso de OAuth 2.0 a las API de Twitter v1.1. Para completar el tutorial, debe descargar e instalar el paquete de NuGet LINQ to Twitter. Para obtener más información, consulte el [proyecto de CodePlex de LINQ to Twitter][] (en inglés).

## <a name="get-oauth-credentials"></a>Registro para obtener acceso a las API de Twitter v1.1 y almacenamiento de credenciales

[WACOM.INCLUDE [mobile-services-register-twitter-access][]]

1.  En el Explorador de soluciones de Visual Studio, abra el archivo web.config del proyecto de servicio móvil, busque los valores **MS\_TwitterConsumerKey** y **MS\_TwitterConsumerSecret** de la aplicación y reemplace los valores de estas claves por los valores de clave de usuario y secreto de usuario de Twitter que ha establecido en el portal.

2.  En la misma sección, agregue los siguientes nuevos valores de configuración, reemplazando los marcadores de posición por los valores de secreto de token de acceso y token de acceso de Twitter que ha establecido como valores de la aplicación en el portal:

        <add key="TWITTER_ACCESS_TOKEN" value="**your_access_token**" />
        <add key="TWITTER_ACCESS_TOKEN_SECRET" value="**your_access_token_secret**" />

    El servicio móvil usa estos valores de configuración almacenados cuando se ejecuta en el equipo local, lo que le permite probar el trabajo programado antes de publicarlo. Sin embargo, al ejecutarse en Azure, el servicio móvil usa los valores establecidos en el portal e ignora esta configuración del proyecto.

## <a name="install-linq2twitter"></a>Descarga e instalación de la biblioteca LINQ to Twitter

1.  En el **Explorador de soluciones** de Visual Studio, haga clic con el botón secundario en el nombre del proyecto y, a continuación, seleccione **Administrar paquetes de NuGet**.

2.  En el panel izquierdo, seleccione la categoría **En línea**, busque `linq2twitter`, haga clic en **Instalar** en el paquete **linqtotwitter** y, a continuación, lea y acepte los contratos de licencia.

    ![][]

    De esta forma se agrega la biblioteca Linq to Twitter al proyecto de servicio móvil.

A continuación, tendrá que crear una nueva tabla en la que almacenar tweets.

## <a name="create-table"></a>Creación de la nueva tabla de actualizaciones

1.  En el Explorador de soluciones de Visual Studio, haga clic con el botón secundario en la carpeta DataObjects, expanda **Agregar**, haga clic en **Clase**, escriba `Updates` como **Nombre** y, después, haga clic en **Agregar**.

    De esta forma se crea un nuevo archivo de proyecto para la clase Updates.

2.  Haga clic con el botón secundario en **Referencias**, haga clic en **Agregar referencia...**, seleccione **Framework** bajo **Ensamblados**, active **System.ComponentModel.DataAnnotations** y haga clic en **Aceptar**.

    ![][1]

    De esta forma, se agrega una nueva referencia de ensamblado.

3.  Agregue las siguientes instrucciones **using** en esta nueva clase:

        using Microsoft.WindowsAzure.Mobile.Service;
        using System.ComponentModel.DataAnnotations;

4.  Reemplace la definición de clase **Updates** por el código siguiente:

        public class Updates 
        {
            [Key]
            public int UpdateId { get; set; }
            public long TweetId { get; set; }
            public string Text { get; set; }
            public string Author { get; set; }
            public DateTime Date { get; set; }
        }

5.  Expanda la carpeta Models, abra el archivo de contexto del modelo de datos (denominado <em>nombre_servicio</em>Context.cs) y agregue la siguiente propiedad, que devuelve un objeto **DbSet** con tipo:

        public DbSet<Updates> Updates { get; set; }

    La tabla de actualizaciones (Updates), que se crea en la base de datos al obtener acceso a DbSet por primera vez, se usa en el servicio para almacenar los datos de tweets.

    > [WACOM.NOTE] Al usar el inicializador de base de datos predeterminado, Entity Framework eliminará la base de datos y la volverá a crear siempre que detecte un cambio del modelo de datos en la definición del modelo de Code First. Para realizar este cambio en el modelo de datos y mantener los datos existentes en la base de datos, debe utilizar Migraciones de Code First. El inicializador predeterminado no se puede usar con una base de datos SQL en Azure. Para obtener más información, consulte [Uso de Migraciones de Code First para actualizar el modelo de datos][].

A continuación, cree el trabajo programado que obtiene acceso a Twitter y almacena los datos de tweets en la nueva tabla de actualizaciones.

## <a name="add-job"></a>Creación de un nuevo trabajo programado

1.  Expanda la carpeta ScheduledJobs y abra el archivo de proyecto SampleJob.cs.

    Esta clase, que se hereda de **ScheduledJob**, representa un trabajo que se puede programar en el Portal de administración de Azure para ejecutarse con una programación fija o a petición.

2.  Reemplace el contenido de SampleJob.cs por el código siguiente.

        using System;
        using System.Linq;
        using System.Threading;
        using System.Threading.Tasks;
        using System.Web.Http;
        using Microsoft.WindowsAzure.Mobile.Service;
        using Microsoft.WindowsAzure.Mobile.Service.ScheduledJobs;
        using LinqToTwitter;
        using todolistService.Models;
        using todolistService.DataObjects;

        namespace todolistService
        {
            // A simple scheduled job which can be invoked manually by submitting an HTTP
            // POST request to the path "/jobs/sample".
            public class SampleJob : ScheduledJob
            {
                private todolistContext context;
                private string accessToken;
                private string accessTokenSecret;

                protected override void Initialize(ScheduledJobDescriptor scheduledJobDescriptor, CancellationToken cancellationToken)
                {
                    base.Initialize(scheduledJobDescriptor, cancellationToken);

                    // Create a new context with the supplied schema name.
                    context = new todolistContext(Services.Settings.Name);
                }

                public async override Task ExecuteAsync()
                {            
                    // Try to get the stored Twitter access token from app settings.  
                    if (!(Services.Settings.TryGetValue("TWITTER_ACCESS_TOKEN", out accessToken) |
                    Services.Settings.TryGetValue("TWITTER_ACCESS_TOKEN_SECRET", out accessTokenSecret)))
                    {
                        Services.Log.Error("Could not retrieve Twitter access credentials.");
                    }

                    // Create a new authorizer to access Twitter v1.1 APIs
                    // using single-user OAUth 2.0 credentials.
                    MvcAuthorizer auth = new MvcAuthorizer();
                    SingleUserInMemoryCredentialStore store = 
                        new SingleUserInMemoryCredentialStore()
                    {
                        ConsumerKey = Services.Settings.TwitterConsumerKey,
                        ConsumerSecret = Services.Settings.TwitterConsumerSecret,
                        OAuthToken = accessToken,
                        OAuthTokenSecret = accessTokenSecret
                    };

                    // Set the credentials for the authorizer.
                    auth.CredentialStore = store;

                    // Create a new LINQ to Twitter context.
                    TwitterContext twitter = new TwitterContext(auth);

                    // Get the ID of the most recent stored tweet.
                    long lastTweetId = 0;
                    if (context.Updates.Count() > 0)
                    {
                        lastTweetId = (from u in context.Updates
                                       orderby u.TweetId descending
                                       select u).Take(1).SingleOrDefault()
                                                    .TweetId;
                    }

                    // Execute a search that returns a filtered result.
                    var response = await (from s in twitter.Search
                                          where s.Type == SearchType.Search
                                          && s.Query == "%23mobileservices"
                                          && s.SinceID == Convert.ToUInt64(lastTweetId + 1)
                                          && s.ResultType == ResultType.Recent
                                          select s).SingleOrDefaultAsync();

                    // Remove retweets and replies and log the number of tweets.
                    var filteredTweets = response.Statuses
                        .Where(t => !t.Text.StartsWith("RT") && t.InReplyToUserID == 0);
                    Services.Log.Info("Fetched " + filteredTweets.Count()
                        + " new tweets from Twitter.");

                    // Store new tweets in the Updates table.
                    foreach (Status tweet in filteredTweets)
                    {
                        Updates newTweet =
                            new Updates
                            {
                                TweetId = Convert.ToInt64(tweet.StatusID),
                                Text = tweet.Text,
                                Author = tweet.User.Name,
                                Date = tweet.CreatedAt
                            };

                        context.Updates.Add(newTweet);
                    }

                    await context.SaveChangesAsync();
                }
                protected override void Dispose(bool disposing)
                {
                    base.Dispose(disposing);
                    if (disposing)
                    {
                        context.Dispose();
                    }
                }
            }
        }

    En el código anterior, debe reemplazar las cadenas *todolistService* y *todolistContext* por el espacio de nombres y el valor DbContext del proyecto descargado, que son <em>nombre_servicio_móvil</em>Service y <em>nombre_servicio_móvil</em>Context, respectivamente.

    En el código anterior, el método de invalidación **ExecuteAsync** llama a la API de consulta de Twitter mediante las credenciales almacenadas para solicitar los tweets recientes que contienen el hashtag `#mobileservices`. Las respuestas y tweets duplicados se quitan de los resultados antes de almacenarse en la tabla.

## <a name="run-job-locally"></a>Prueba local del trabajo programado

Los trabajos programados se pueden probar de forma local antes de publicarlos en Azure y registrarlos en el portal.

1.  En Visual Studio, con el proyecto de servicio móvil establecido como proyecto de inicio, presione F5.

    De esta forma, se inicia dicho proyecto y se abre una nueva ventana del explorador con la página de bienvenida.

2.  Haga clic en **Pruébelo** y luego en **POST jobs/{jobName}**.

    ![][2]

3.  Haga clic en **Pruébelo**, escriba `Sample` como el valor del parámetro **{jobName}** y haga clic en **enviar**.

    ![][3]

    De este modo, se envía una solicitud POST al extremo del trabajo de ejemplo. En el servicio local, se inicia el método **ExecuteAsync**. Puede establecer un punto de interrupción en este método para depurar el código.

4.  En el Explorador de servidores, expanda **Conexiones de datos**, **MSTableConnectionString** y **tablas**. Haga clic con el botón secundario en **Updates** y después haga clic en **Mostrar datos de tabla**.

    Los nuevos tweets se introducen como filas en la tabla de datos.

## <a name="register-job"></a>Publicación del servicio y registro del nuevo trabajo

El trabajo debe registrarse en la pestaña **Programador** para que Servicios móviles pueda ejecutarlo en la programación que defina.

1.  Vuelva a publicar el proyecto de servicio móvil en Azure.

2.  En el [Portal de administración de Azure][], haga clic en Servicios móviles y, a continuación, en la aplicación.

    ![][4]

3.  Haga clic en la pestaña **Programador** y, a continuación, en **+Create**.

    ![][5]

    > [WACOM.NOTE]Cuando ejecute el servicio móvil en el nivel *gratis*, solo podrá ejecutar un trabajo programado a la vez. En los niveles de pago, puede ejecutar hasta diez trabajos programados a la vez.

4.  En el cuadro de diálogo del programador, especifique *SampleJob* para **Job Name**, establezca las unidades y el intervalo de programación y, a continuación, haga clic en el botón de comprobación.

    ![][6]

    De esta forma, se crea un nuevo trabajo con el nombre **SampleJob**.

5.  Haga clic en el nuevo trabajo que acaba de crear y, después, haga clic en **Ejecutar una vez** para probar el script.

    ![][7]

    De esta forma, se ejecuta el trabajo mientras se mantiene deshabilitado en el programador. Desde esta página, puede habilitar el trabajo y cambiar su programación en cualquier momento.

    > [WACOM.NOTE] Puede seguir utilizándose una solicitud POST para iniciar el trabajo programado. Sin embargo, la autorización recae de forma predeterminada en el usuario, lo que significa que la solicitud debe incluir la clave de la aplicación en el encabezado.

6.  (Opcional) En el [Portal de administración de Azure][], haga clic en Manage para la base de datos asociada al servicio móvil.

    ![][8]

7.  Asimismo, en dicho portal, ejecute una consulta para ver los cambios realizados por la aplicación. La consulta puede ser similar a la siguiente, pero use el nombre del servicio móvil como nombre de esquema en lugar de `todolist`.

        SELECT * FROM [todolist].[Updates]

Enhorabuena, ha creado correctamente un nuevo trabajo programado en el servicio móvil. Este trabajo se ejecutará como programado hasta que lo deshabilite o modifique.

<!-- Anchors. -->
<!-- Images. -->
<!-- URLs. -->

  [Back-end de .NET]: /es-es/documentation/articles/mobile-services-dotnet-backend-schedule-recurring-tasks/ "Back-end de .NET"
  [Back-end de JavaScript]: /es-es/documentation/articles/mobile-services-schedule-recurring-tasks/ "Back-end de JavaScript"
  [Registro para obtener acceso a Twitter y almacenamiento de credenciales]: #get-oauth-credentials
  [Descarga e instalación de la biblioteca LINQ to Twitter]: #install-linq2twitter
  [Creación de la nueva tabla de actualizaciones]: #create-table
  [Creación de un nuevo trabajo programado]: #add-job
  [Prueba local del trabajo programado]: #run-job-locally
  [Publicación del servicio y registro del trabajo]: #register-job
  [proyecto de CodePlex de LINQ to Twitter]: http://linqtotwitter.codeplex.com/
  [mobile-services-register-twitter-access]: ../includes/mobile-services-register-twitter-access.md
  []: ./media/mobile-services-dotnet-backend-schedule-recurring-tasks/add-linq2twitter-nuget-package.png
  [1]: ./media/mobile-services-dotnet-backend-schedule-recurring-tasks/add-component-model-reference.png
  [Uso de Migraciones de Code First para actualizar el modelo de datos]: /es-es/documentation/articles/mobile-services-dotnet-backend-use-code-first-migrations
  [2]: ./media/mobile-services-dotnet-backend-schedule-recurring-tasks/mobile-service-start-page.png
  [3]: ./media/mobile-services-dotnet-backend-schedule-recurring-tasks/mobile-service-try-this-out.png
  [Portal de administración de Azure]: https://manage.windowsazure.com/
  [4]: ./media/mobile-services-dotnet-backend-schedule-recurring-tasks/mobile-services-selection.png
  [5]: ./media/mobile-services-dotnet-backend-schedule-recurring-tasks/mobile-schedule-new-job-cli.png
  [6]: ./media/mobile-services-dotnet-backend-schedule-recurring-tasks/create-new-job.png
  [7]: ./media/mobile-services-dotnet-backend-schedule-recurring-tasks/sample-job-run-once.png
  [8]: ./media/mobile-services-dotnet-backend-schedule-recurring-tasks/manage-sql-azure-database.png
