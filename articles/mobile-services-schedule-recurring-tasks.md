<properties linkid="develop-mobile-tutorials-schedule-backend-tasks" urlDisplayName="Schedule Backend Tasks" pageTitle="Schedule Backend Tasks with Scheduler - Mobile Services" metaKeywords="" description="Use the Azure Mobile Services Scheduler to schedule jobs for your mobile app." metaCanonical="" services="" documentationCenter="Mobile" title="Schedule recurring jobs in Mobile Services" authors="glenga" solutions="" manager="" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-multiple" ms.devlang="multiple" ms.topic="article" ms.date="01/01/1900" ms.author="glenga" />

# Programación de trabajos periódicos en Servicios móviles

<div class="dev-center-tutorial-subselector">
    <a href="/es-es/documentation/articles/mobile-services-dotnet-backend-schedule-recurring-tasks/" title="Back-end de .NET">Back-end de .NET</a> | <a href="/es-es/documentation/articles/mobile-services-schedule-recurring-tasks/"  title="Back-end de JavaScript" class="current">Back-end de JavaScript</a>
</div>

Este tema le muestra cómo usar la funcionalidad del programador de trabajos en el Portal de administración para definir el código de script de servidor que se ejecuta según el programa que establezca. En este caso, se realiza una comprobación periódica del script con un servicio remoto (Twitter) y se almacenan los resultados en una nueva tabla. Entre las demás tareas periódicas que pueden programarse se incluyen las siguientes:

-   Archivado de registros de datos antiguos o duplicados.
-   Solicitud y almacenamiento de datos externos, como tweets, entradas RSS e información de ubicación.
-   Procesamiento o cambio de tamaño de imágenes almacenadas.

<!-- // Removed because this shortcode b/c it's old and doesn't use the new Twitter v1.1. APIs >[AZURE.VIDEO Windows-Store-app-Getting-Started-with-the-Windows-Azure-Mobile-Services-Scheduler] --> <!-- // Original video HTML code for reference. <div class="dev-onpage-video-clear clearfix"> <div class="dev-onpage-left-content"> <p> </div> <div class="dev-onpage-video-wrapper"><a href="http://channel9.msdn.com/Series/Windows-Azure-Mobile-Services/Windows-Store-app-Getting-Started-with-the-Windows-Azure-Mobile-Services-Scheduler" target="_blank" class="label">watch the tutorial</a> <a style="background-image: url('/media/devcenter/mobile/videos/get-started-with-scheduler-180x120.png') !important;" href="http://channel9.msdn.com/Series/Windows-Azure-Mobile-Services/Windows-Store-app-Getting-Started-with-the-Windows-Azure-Mobile-Services-Scheduler" target="_blank" class="dev-onpage-video"><span class="icon">Play Video</span></a> <span class="time">5:22</span></div> </div>-->

Este tutorial le guiará por los siguientes pasos relativos al uso del programador de trabajos para crear un trabajo programado que solicite datos de tweets de Twitter y almacene los tweets en una nueva tabla de actualizaciones:

-   [Registro para obtener acceso a Twitter y almacenamiento de credenciales][Registro para obtener acceso a Twitter y almacenamiento de credenciales]
-   [Creación de la nueva tabla de actualizaciones][Creación de la nueva tabla de actualizaciones]
-   [Creación de un nuevo trabajo programado][Creación de un nuevo trabajo programado]

## <a name="get-oauth-credentials"></a>Registro para obtener acceso a las API de Twitter v1.1 y almacenamiento de credenciales

[WACOM.INCLUDE [mobile-services-register-twitter-access](../includes/mobile-services-register-twitter-access.md)]

## <a name="create-table"></a>Creación de la nueva tabla de actualizaciones

A continuación, tendrá que crear una nueva tabla en la que almacenar tweets.

1.  En el Portal de administración, haga clic en la pestaña **Data** para el servicio móvil y, a continuación, haga clic en **+Create**.

    ![][0]

    Esto muestra el cuadro de diálogo **Create new table**.

2.  En **Table name**, escriba *Updates* y, a continuación, haga clic en el botón de comprobación.

    ![][1]

    De esta forma, se creará una nueva tabla de almacenamiento **Updates**.

## <a name="add-job"></a>Creación de un nuevo trabajo programado

Ahora puede crear el trabajo programado que obtiene acceso a Twitter y almacena los datos de tweets en la nueva tabla de actualizaciones.

1.  Haga clic en la pestaña **Programador** y, a continuación, en **+Create**.

    ![][2]

    > [WACOM.NOTE]Cuando ejecute el servicio móvil en el nivel *gratis*, solo podrá ejecutar un trabajo programado a la vez. En los niveles de pago, puede ejecutar hasta diez trabajos programados a la vez.

2.  En el cuadro de diálogo del programador, especifique *getUpdates* para **Job Name**, establezca las unidades y el intervalo de programación y, a continuación, haga clic en el botón de comprobación.

    ![][3]

    De esta forma, se crea un nuevo trabajo con el nombre **getUpdates**.

3.  Haga clic en el nuevo trabajo que acaba de crear y haga clic en la pestaña **Script**.

    ![][4]

4.  Reemplace la función de marcador de posición **getUpdates** por el siguiente código:

        var updatesTable = tables.getTable('Updates');
        var request = require('request');
        var twitterUrl = "https://api.twitter.com/1.1/search/tweets.json?q=%23mobileservices&result_type=recent";

        // Get the service configuration module.
        var config = require('mobileservice-config');

        // Get the stored Twitter consumer key and secret. 
        var consumerKey = config.twitterConsumerKey,
            consumerSecret = config.twitterConsumerSecret
        // Get the Twitter access token from app settings.    
        var accessToken= config.appSettings.TWITTER_ACCESS_TOKEN,
            accessTokenSecret = config.appSettings.TWITTER_ACCESS_TOKEN_SECRET;

        function getUpdates() {   
            // Check what is the last tweet we stored when the job last ran
            // and ask Twitter to only give us more recent tweets
            appendLastTweetId(
                twitterUrl, 
                function twitterUrlReady(url){            
                    // Create a new request with OAuth credentials.
                    request.get({
                        url: url,                
                        oauth: {
                            consumer_key: consumerKey,
                            consumer_secret: consumerSecret,
                            token: accessToken,
                            token_secret: accessTokenSecret
                        }},
                        function (error, response, body) {
                        if (!error && response.statusCode == 200) {
                            var results = JSON.parse(body).statuses;
                            if(results){
                                console.log('Fetched ' + results.length + ' new results from Twitter');                       
                                results.forEach(function (tweet){
                                    if(!filterOutTweet(tweet)){
                                        var update = {
                                            twitterId: tweet.id,
                                            text: tweet.text,
                                            author: tweet.user.screen_name,
                                            date: tweet.created_at
                                        };
                                        updatesTable.insert(update);
                                    }
                                });
                            }            
                        } else { 
                            console.error('Could not contact Twitter');
                        }
                    });

                });
         }
        // Find the largest (most recent) tweet ID we have already stored
        // (if we have stored any) and ask Twitter to only return more
        // recent ones
        function appendLastTweetId(url, callback){
            updatesTable
            .orderByDescending('twitterId')
            .read({success: function readUpdates(updates){
                if(updates.length){
                    callback(url + '&since_id=' + (updates[0].twitterId + 1));           
                } else {
                    callback(url);
                }
            }});
        }

        function filterOutTweet(tweet){
            // Remove retweets and replies
            return (tweet.text.indexOf('RT') === 0 || tweet.to_user_id);
        }

    Este script llama a la API de consulta de Twitter mediante las credenciales almacenadas para solicitar los tweets recientes que contienen el hashtag `#mobileservices`. Las respuestas y tweets duplicados se quitan de los resultados antes de almacenarse en la tabla.

    > [WACOM.NOTE]En este ejemplo se presupone que solo se insertan algunas filas en la tabla durante cada ejecución programada. En los casos en los que se inserten muchas filas en un bucle, es posible que se agoten las conexiones con el nivel gratis. En ese caso, debe realizar inserciones en los lotes. Para obtener más información, consulte [Inserción de inserciones en masa][Inserción de inserciones en masa].

5.  Haga clic en **Run Once** para probar el script.

    ![][5]

    De esta forma, se guarda y ejecuta el trabajo mientras se mantiene deshabilitado en el programador.

6.  Haga clic en el botón de retroceso, en **Data**, en la tabla **Updates** y en **Browse**, y compruebe que se hayan insertado los datos de Twitter en la tabla.

    ![][6]

7.  Haga clic en el botón de retroceso y en **Scheduler**, seleccione **getUpdates** y, a continuación, haga clic en **Enable**.

    ![][7]

    De esta forma, el trabajo se puede ejecutar según la programación especificada, en este caso cada hora.

Enhorabuena, ha creado correctamente un nuevo trabajo programado en el servicio móvil. Este trabajo se ejecutará como programado hasta que lo deshabilite o modifique.

## <a name="nextsteps"> </a>Pasos siguientes

-   [Referencia del script del servidor de Servicios móviles][Referencia del script del servidor de Servicios móviles]
    Obtenga más información acerca del registro y uso de scripts de servidor.

<!-- Anchors. --> 
<!-- Images. --> 
<!-- URLs. -->

  [Registro para obtener acceso a Twitter y almacenamiento de credenciales]: #get-oauth-credentials
  [Creación de la nueva tabla de actualizaciones]: #create-table
  [Creación de un nuevo trabajo programado]: #add-job
  [mobile-services-register-twitter-access]: ../includes/mobile-services-register-twitter-access.md
  [0]: ./media/mobile-services-schedule-recurring-tasks/mobile-data-tab-empty-cli.png
  [1]: ./media/mobile-services-schedule-recurring-tasks/mobile-create-updates-table.png
  [2]: ./media/mobile-services-schedule-recurring-tasks/mobile-schedule-new-job-cli.png
  [3]: ./media/mobile-services-schedule-recurring-tasks/mobile-create-job-dialog.png
  [4]: ./media/mobile-services-schedule-recurring-tasks/mobile-schedule-job-script-new.png
  [Inserción de inserciones en masa]: /es-es/develop/mobile/how-to-guides/work-with-server-scripts/#bulk-inserts
  [5]: ./media/mobile-services-schedule-recurring-tasks/mobile-schedule-job-script.png
  [6]: ./media/mobile-services-schedule-recurring-tasks/mobile-browse-updates-table.png
  [7]: ./media/mobile-services-schedule-recurring-tasks/mobile-schedule-job-enabled.png
  [Referencia del script del servidor de Servicios móviles]: http://go.microsoft.com/fwlink/?LinkId=262293
