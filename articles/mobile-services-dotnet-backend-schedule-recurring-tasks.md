<properties  pageTitle="Programación de tareas de backend con el programador - Servicios móviles" metaKeywords="" description="Use the Windows Azure Mobile Services Scheduler to schedule jobs for your mobile app." metaCanonical="" services="mobile-services" documentationCenter="Mobile" title="Schedule recurring jobs in Mobile Services" authors="glenga"  solutions="mobile" writer="" manager="dwrede" editor=""  />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-multiple" ms.devlang="multiple" ms.topic="article" ms.date="09/26/2014" ms.author="glenga" />

# Programación de trabajos periódicos en Servicios móviles 

<div class="dev-center-tutorial-subselector">
	<a href="/es-es/documentation/articles/mobile-services-dotnet-backend-schedule-recurring-tasks/" title=".NET backend" class="current">backend .NET</a> | <a href="/es-es/documentation/articles/mobile-services-schedule-recurring-tasks/"  title="JavaScript backend" >JavaScript backend</a>
</div>
 
This topic shows you how to use the job scheduler functionality in the Management Portal to define server script code that is executed based on a schedule that you define. In this case, the script periodically check with a remote service, in this case Twitter, and stores the results in a new table. Some other periodic tasks that can be scheduled include:

+ Archiving old or duplicate data records.
+ Requesting and storing external data, such as tweets, RSS entries, and location information.
+ Processing or resizing stored images.

This tutorial walks you through the following steps of how to use the job scheduler to create a scheduled job that requests tweet data from Twitter and stores the tweets in a new Updates table:

+ [Register for Twitter access and store credentials]
+ [Download and install the LINQ to Twitter library]
+ [Create the new Updates table]
+ [Create a new scheduled job]
+ [Test the scheduled job locally]
+ [Publish the service and register the job]

>[WACOM.NOTE]This tutorial uses the third-party LINQ to Twitter library to simplify OAuth 2.0 access to Twitter v1.1. APIs. You must download and install the LINQ to Twitter NuGet package to complete this tutorial. For more information, see the [LINQ to Twitter CodePlex project].

##<a name="get-oauth-credentials"></a>Registro para obtener acceso a las API de Twitter v1.1 y almacenamiento de credenciales

[WACOM.INCLUDE [mobile-services-register-twitter-access](../includes/mobile-services-register-twitter-access.md)]

<ol start="7">
<li><p>En el Explorador de soluciones de Visual Studio, abra el archivo web.config del proyecto de servicio móvil, busque los valores <strong>MS_TwitterConsumerKey</strong> y <strong>MS_TwitterConsumerSecret</strong> de la aplicación y reemplace los valores de estas claves por los valores de clave de usuario y secreto de usuario de Twitter que ha establecido en el portal.</p></li>

<li><p>En la misma sección, agregue los siguientes nuevos valores de configuración, reemplazando los marcadores de posición por los valores de secreto de token de acceso y token de acceso de Twitter que ha establecido como valores de la aplicación en el portal:</p>

<pre><code><add key="TWITTER_ACCESS_TOKEN" value="**your_access_token**" />
<add key="TWITTER_ACCESS_TOKEN_SECRET" value="**your_access_token_secret**" /></code></pre>

<p>El servicio móvil usa estos valores de configuración almacenados cuando se ejecuta en el equipo local, lo que le permite probar el trabajo programado antes de publicarlo. Sin embargo, al ejecutarse en Azure, el servicio móvil usa los valores establecidos en el portal e ignora esta configuración del proyecto.  </p></li>
</ol>

##<a name="install-linq2twitter"></a>Descarga e instalación de la biblioteca LINQ to Twitter

1. En el **Explorador de soluciones** de Visual Studio, haga clic con el botón secundario en el nombre del proyecto y luego seleccione **Administrar paquetes de NuGet**.

2. En el panel izquierdo, seleccione la categoría **En línea**, busque `linq2twitter`, haga clic en **Instalar** en el paquete **linqtotwitter** y, a continuación, lea y acepte los contratos de licencia. 

  	![][1]

  	De esta forma se agrega la biblioteca Linq to Twitter al proyecto de servicio móvil.

A continuación, tendrá que crear una nueva tabla en la que almacenar tweets.

##<a name="create-table"></a>Creación de la nueva tabla de actualizaciones

1. En el Explorador de soluciones de Visual Studio, haga clic con el botón secundario en la carpeta DataObjects, expanda **Agregar**, haga clic en **Clase**, escriba `Updates` como **Nombre** y haga clic en **Agregar**.

	De esta forma se crea un nuevo archivo de proyecto para la clase Updates.

2. Haga clic con el botón secundario en **Referencias**, haga clic en**Agregar referencia...**, seleccione**Framework** en **Ensamblados**, active **System.ComponentModel.DataAnnotations** y haga clic en**Aceptar**.

	![][7]

	De esta forma, se agrega una nueva referencia de ensamblado.

2. Agregue las siguientes instrucciones **using** en esta nueva clase:
 
		using Microsoft.WindowsAzure.Mobile.Service;
		using System.ComponentModel.DataAnnotations;

3. Reemplace la definición de clase **Updates** por el código siguiente:

		public class Updates 
	    {
	        [Key]
	        public int UpdateId { get; set; }
	        public long TweetId { get; set; }
	        public string Text { get; set; }
	        public string Author { get; set; }
	        public DateTime Date { get; set; }
    	}

4. Expanda la carpeta Models, abra el archivo de contexto del modelo de datos (denominado <em>nombre_servicio</em>Context.cs) y agregue la siguiente propiedad, que devuelve un objeto **DbSet** con tipo:

		public DbSet<Updates> Updates { get; set; }

	La tabla de actualizaciones (Updates), que se crea en la base de datos al obtener acceso a DbSet por primera vez, se usa en el servicio para almacenar los datos de tweets.  

	>[WACOM.NOTE] Al usar el inicializador de base de datos predeterminado, Entity Framework eliminará la base de datos y la volverá a crear siempre que detecte un cambio del modelo de datos en la definición del modelo de Code First. Para realizar este cambio en el modelo de datos y mantener los datos existentes en la base de datos, debe utilizar Migraciones de Code First. El inicializador predeterminado no se puede usar con una base de datos SQL en Azure. Para obtener más información, consulte [Uso de Migraciones de Code First para actualizar el modelo de datos](/es-es/documentation/articles/mobile-services-dotnet-backend-use-code-first-migrations).  

A continuación, cree el trabajo programado que obtiene acceso a Twitter y almacena los datos de tweets en la nueva tabla de actualizaciones.

##<a name="add-job"></a>Creación de un nuevo trabajo programado  

1. Expanda la carpeta ScheduledJobs y abra el archivo de proyecto SampleJob.cs.

	Esta clase, que se hereda de **ScheduledJob**, representa un trabajo que se puede programar en el Portal de administración de Azure para ejecutarse con una programación fija o a petición.

2. Reemplace el contenido de SampleJob.cs por el código siguiente.
 
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

	En el código anterior, debe reemplazar las cadenas _todolistService_ y _todolistContext_ por el espacio de nombres y el valor DbContext del proyecto descargado, que son <em>nombre&#95;servicio&#95;móvil</em>Service y <em>nombre&#95;servicio&#95;móvil</em>Context, respectivamente.  
   	
	En el código anterior, el método de invalidación**ExecuteAsync** llama a la API de consulta de Twitter mediante las credenciales almacenadas para solicitar los tweets recientes que contienen el hashtag `#mobileservices`. Las respuestas y tweets duplicados se quitan de los resultados antes de almacenarse en la tabla.

##<a name="run-job-locally"></a>Prueba local del trabajo programado

Los trabajos programados se pueden probar de forma local antes de publicarlos en Azure y registrarlos en el portal. 

1. En Visual Studio, con el proyecto de servicio móvil establecido como proyecto de inicio, presione F5.

	De esta forma, se inicia dicho proyecto y se abre una nueva ventana del explorador con la página de bienvenida.

2. Haga clic en **probarlo** y luego en **POST jobs/{jobName}**.

	![][8]
 
4. Haga clic en **probar esto**, escriba `Sample` como valor del parámetro **{jobName}** y haga clic en **Send**.

	![][9]

	De este modo, se envía una solicitud POST al extremo del trabajo de ejemplo. En el servicio local, se inicia el método **ExecuteAsync**. Puede establecer un punto de interrupción en este método para depurar el código.

3. En el Explorador de servidores, expanda **Conexiones de datos**, **MSTableConnectionString** y **tablas**. Haga clic con el botón secundario en **Updates** y después haga clic en **Mostrar datos de tabla**.

	Los nuevos tweets se introducen como filas en la tabla de datos.

##<a name="register-job"></a>Publicación del servicio y registro del nuevo trabajo 

El trabajo debe registrarse en la pestaña **Programador** para que Servicios móviles pueda ejecutarlo en la programación que se defina.

3. Vuelva a publicar el proyecto de servicio móvil en Azure.

4. En el [Portal de administración de Azure], haga clic en Servicios móviles y, a continuación, en la aplicación.
 
	![][2]

2. Haga clic en la pestaña **Programador** y haga clic en **+Create**. 

   	![][3]

    >[WACOM.NOTE]Cuando ejecute el servicio móvil en el nivel <em>gratis</em>, solo podrá ejecutar un trabajo programado a la vez. En los niveles de pago, puede ejecutar hasta diez trabajos programados a la vez.

3. En el cuadro de diálogo del programador, especifique _SampleJob_ como **Job Name**, establezca las unidades y el intervalo de programación y, a continuación, haga clic en el botón de comprobación. 
   
   	![][4]

   	De esta forma, se crea un nuevo trabajo con el nombre **SampleJob**. 

4. Haga clic en el nuevo trabajo que acaba de crear y luego en **Ejecutar una vez** para probar el script. 

  	![][5]

   	De esta forma, se ejecuta el trabajo mientras se mantiene deshabilitado en el programador. Desde esta página, puede habilitar el trabajo y cambiar su programación en cualquier momento.

	>[WACOM.NOTE]Puede seguir utilizándose una solicitud POST para iniciar el trabajo programado. Sin embargo, la autorización recae de forma predeterminada en el usuario, lo que significa que la solicitud debe incluir la clave de la aplicación en el encabezado.

4. (Opcional) En el [Portal de administración de Azure], haga clic en Manage para la base de datos asociada al servicio móvil.

    ![][6]

5. Asimismo, en dicho portal, ejecute una consulta para ver los cambios realizados por la aplicación. La consulta puede ser similar a la siguiente, pero use el nombre del servicio móvil como nombre de esquema en lugar de `todolist`.

        SELECT * FROM [todolist].[Updates]

Enhorabuena, ha creado correctamente un nuevo trabajo programado en el servicio móvil. Este trabajo se ejecutará como programado hasta que lo deshabilite o modifique.

<!-- Anchors. -->
[Registro para obtener acceso a Twitter y almacenamiento de credenciales]: #get-oauth-credentials
[Descarga e instalación de la biblioteca LINQ to Twitter]: #install-linq2twitter
[Creación de la nueva tabla de actualizaciones]: #create-table
[Creación de un nuevo trabajo programado]: #add-job
[Prueba local del trabajo programado]: #run-job-locally
[Publicación del servicio y registro del trabajo]: #register-job
[Pasos siguientes]: #next-steps

<!-- Images. -->
[1]: ./media/mobile-services-dotnet-backend-schedule-recurring-tasks/add-linq2twitter-nuget-package.png
[2]: ./media/mobile-services-dotnet-backend-schedule-recurring-tasks/mobile-services-selection.png
[3]: ./media/mobile-services-dotnet-backend-schedule-recurring-tasks/mobile-schedule-new-job-cli.png
[4]: ./media/mobile-services-dotnet-backend-schedule-recurring-tasks/create-new-job.png
[5]: ./media/mobile-services-dotnet-backend-schedule-recurring-tasks/sample-job-run-once.png
[6]: ./media/mobile-services-dotnet-backend-schedule-recurring-tasks/manage-sql-azure-database.png
[7]: ./media/mobile-services-dotnet-backend-schedule-recurring-tasks/add-component-model-reference.png
[8]: ./media/mobile-services-dotnet-backend-schedule-recurring-tasks/mobile-service-start-page.png
[9]: ./media/mobile-services-dotnet-backend-schedule-recurring-tasks/mobile-service-try-this-out.png

<!-- URLs. -->
[Portal de administración de Azure]: https://manage.windowsazure.com/
[Registro de las aplicaciones para el inicio de sesión en Twitter con Servicios móviles]: /es-es/documentation/articles/mobile-services-how-to-register-twitter-authentication
[Desarrolladores de Twitter]: http://go.microsoft.com/fwlink/p/?LinkId=268300
[Configuración de aplicaciones]: http://msdn.microsoft.com/es-es/library/windowsazure/b6bb7d2d-35ae-47eb-a03f-6ee393e170f7
[LINQ para el proyecto CodePlex de Twitter]: http://linqtotwitter.codeplex.com/
