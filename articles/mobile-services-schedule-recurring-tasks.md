<properties linkid="develop-mobile-tutorials-schedule-backend-tasks" urlDisplayName="Schedule Backend Tasks" pageTitle="Schedule Backend Tasks with Scheduler - Mobile Services" metaKeywords="" description="Use the Azure Mobile Services Scheduler to schedule jobs for your mobile app." metaCanonical="" services="" documentationCenter="Mobile" title="Schedule recurring jobs in Mobile Services" authors="glenga" solutions="" manager="" editor="" />

Programación de trabajos periódicos en Servicios móviles
========================================================

Este tema le muestra cómo usar la funcionalidad del programador de trabajos en el Portal de administración para definir el código de script de servidor que se ejecuta según el programa que establezca. En este caso, se realiza una comprobación periódica del script con un servicio remoto (Twitter) y se almacenan los resultados en una nueva tabla. Entre las demás tareas periódicas que pueden programarse se incluyen las siguientes:

-   Archivado de registros de datos antiguos o duplicados.
-   Solicitud y almacenamiento de datos externos, como tweets, entradas RSS e información de ubicación.
-   Procesamiento o cambio de tamańo de imágenes almacenadas.

Este tutorial le guiará por los siguientes pasos relativos al uso del programador de trabajos para crear un trabajo programado que solicite datos de tweets de Twitter y almacene los tweets en una nueva tabla de actualizaciones:

-   [Registro para obtener acceso a Twitter y almacenamiento de credenciales](#get-oauth-credentials)
-   [Creación de la nueva tabla de actualizaciones](#create-table)
-   [Creación de un nuevo trabajo programado](#add-job)

Registro para obtener acceso a las API de Twitter v1.1 y almacenamiento de credenciales
---------------------------------------------------------------------------------------

[WACOM.INCLUDE [mobile-services-register-twitter-access](../includes/mobile-services-register-twitter-access.md)]

Creación de la nueva tabla de actualizaciones
---------------------------------------------

A continuación, tendrá que crear una nueva tabla en la que almacenar tweets.

1.  En el Portal de administración, haga clic en la pestańa **Data** para el servicio móvil y, a continuación, haga clic en **+Create**.

   	![](./media/mobile-services-schedule-recurring-tasks/mobile-data-tab-empty-cli.png)

   	Se muestra el cuadro de diálogo **Crear nueva tabla**.

2.  En **Table name**, escriba *Updates* y, a continuación, haga clic en el botón de comprobación.

   	![](./media/mobile-services-schedule-recurring-tasks/mobile-create-updates-table.png)

  	De esta forma, se creará una nueva tabla de almacenamiento **Updates**.

Creación de un nuevo trabajo programado
---------------------------------------

Ahora puede crear el trabajo programado que obtiene acceso a Twitter y almacena los datos de tweets en la nueva tabla de actualizaciones.

1.  Haga clic en la pestańa **Scheduler** y, a continuación, en **+Create**.

   	![](./media/mobile-services-schedule-recurring-tasks/mobile-schedule-new-job-cli.png)

    >[WACOM.NOTE]Cuando ejecute el servicio móvil en el nivel *gratis*, solo podrá ejecutar un trabajo programado a la vez. En los niveles de pago, puede ejecutar hasta diez trabajos programados a la vez.

2.  En el cuadro de diálogo del programador, especifique *getUpdates* para **Job Name**, establezca las unidades y el intervalo de programación y, a continuación, haga clic en el botón de comprobación.
   
   	![](./media/mobile-services-schedule-recurring-tasks/mobile-create-job-dialog.png)

   	De esta forma, se crea un nuevo trabajo con el nombre **getUpdates**. 

3.  Haga clic en el nuevo trabajo que acaba de crear y haga clic en la pestańa **Script**.

   	![](./media/mobile-services-schedule-recurring-tasks/mobile-schedule-job-script-new.png) 

4.  Reemplace la función de marcador de posición **getUpdates** por el siguiente código:

         var updatesTable = tables.getTable('Updates');
         var request = require('request');
         var twitterUrl = "https://api.twitter.com/1.1/search/tweets.json
         q=%23mobileservices&result_type=recent";

         // Obtener el módulo de configuración de servicio.
         var config = require('mobileservice-config');
            
         // Obtenga el secreto y la clave del usuario de Twitter almacenados. 
         var consumerKey = config.twitterConsumerKey,
             consumerSecret = config.twitterConsumerSecret
         // Obtenga el token de acceso a Twitter desde la configuración de la aplicación.    
         var accessToken= config.appSettings.TWITTER_ACCESS_TOKEN,
             accessTokenSecret = config.appSettings.TWITTER_ACCESS_TOKEN_SECRET;
            
         function getUpdates() {   
             // Comprobar que es el último tweet que hemos almacenado cuando se ejecutó el último trabajo
             // y solicitar a Twitter que solo nos proporcione los tweets más recientes.
             appendLastTweetId(
                 twitterUrl, 
                 function twitterUrlReady(url){            
                     // Crear una nueva solicitud con las credenciales de OAuth.
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
         // Buscar el identificador del tweet de mayor tamańo (más reciente) que hemos almacenado ya
         // (si hemos almacenado alguno) y solicitar a Twitter que solo devuelva los
         // más recientes
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
             // Quitar retweets y respuestas
             return (tweet.text.indexOf('RT') === 0 || tweet.to_user_id);
         }


   	Este script llama a la API de consulta de Twitter mediante las credenciales almacenadas para solicitar los tweets recientes que contienen el hashtag `#mobileservices`. Las respuestas y tweets duplicados se quitan de los resultados antes de almacenarse en la tabla.

    > [WACOM.NOTE]En este ejemplo se presupone que solo se insertan algunas filas en la tabla durante cada ejecución programada. En los casos en los que se inserten muchas filas en un bucle, es posible que se agoten las conexiones con el nivel gratis. En ese caso, debe realizar inserciones en los lotes. Para obtener más información, consulte [Realización de inserciones en masa](/en-us/develop/mobile/how-to-guides/work-with-server-scripts/#bulk-inserts).

5.  Haga clic en **Run Once** para probar el script.

  	![](./media/mobile-services-schedule-recurring-tasks/mobile-schedule-job-script.png)

   	De esta forma, se guarda y ejecuta el trabajo mientras se mantiene deshabilitado en el programador.

1.  Haga clic en el botón de retroceso, en **Data**, en la tabla **Updates** y en **Browse**, y compruebe que se hayan insertado los datos de Twitter en la tabla.

   	![](./media/mobile-services-schedule-recurring-tasks/mobile-browse-updates-table.png)

2.  Haga clic en el botón de retroceso y en **Scheduler**, seleccione **getUpdates** y, a continuación, haga clic en **Enable**.

   	![](./media/mobile-services-schedule-recurring-tasks/mobile-schedule-job-enabled.png)

   	De esta forma, el trabajo se puede ejecutar según la programación especificada, en este caso cada hora.

Enhorabuena, ha creado correctamente un nuevo trabajo programado en el servicio móvil. Este trabajo se ejecutará como programado hasta que lo deshabilite o modifique.

Pasos siguientes
----------------

-   [Referencia del script del servidor de Servicios móviles](http://go.microsoft.com/fwlink/?LinkId=262293)
  <br/>Obtenga más información acerca del registro y uso de scripts de servidor.
