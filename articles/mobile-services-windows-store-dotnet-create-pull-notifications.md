<properties linkid="develop-mobile-tutorials-create-pull-notifications-dotnet" urlDisplayName="Define a custom API that supports pull notifications" pageTitle="Define a custom API that supports pull notifications - Azure Mobile Services" metaKeywords="" description="Learn how to Define a custom API that supports periodic notifications in Windows Store apps that use Azure Mobile Services." metaCanonical="" services="" documentationCenter="" title="Define a custom API that supports periodic notifications" authors="glenga" solutions="" manager="" editor="" />


Definición de una API personalizada que admita notificaciones periódicas
========================================================================

<div class="dev-center-tutorial-selector"> 
	<a href="/en-us/develop/mobile/tutorials/create-pull-notifications-dotnet" title="Windows Store C#" class="current">Windows Store C#</a><a href="/en-us/develop/mobile/tutorials/create-pull-notifications-js" title="Windows Store JavaScript">Windows Store JavaScript</a>
</div>

En este tema se muestra cómo utilizar una API personalizada para admitir notificaciones periódicas en una aplicación de la Tienda Windows. Con las notificaciones periódicas habilitadas, Windows tendrá acceso de manera periódica a su extremo de API personalizada y usará el XML devuelto, en un formato específico de icono, para actualizar el icono de la aplicación en el menú Inicio. Para obtener más información, consulte [Notificaciones periódicas](http://msdn.microsoft.com/en-us/library/windows/apps/jj150587.aspx).

Podrá agregar esta funcionalidad a la aplicación que creó cuando completó el tutorial de [Introducción a los Servicios móviles](/en-us/develop/mobile/tutorials/get-started/#create-new-service) o de [Introducción a los datos](/en-us/develop/mobile/tutorials/get-started-with-data-dotnet). Para hacer esto, debe completar los siguientes pasos:

1.  [Definición de la API personalizada](#define-custom-api)
2.  [Actualización de la aplicación para activar las notificaciones periódicas](#update-app)
3.  [Prueba de la aplicación](#test-app)

Este tutorial está basado en el inicio rápido de Servicios móviles. Antes de comenzar este tutorial, primero debe completar los tutoriales [Introducción a los Servicios móviles](/en-us/develop/mobile/tutorials/get-started/#create-new-service) o [Introducción a los datos](/en-us/develop/mobile/tutorials/get-started-with-data-dotnet).

<a name="define-custom-api"></a>
Definición de la API personalizada
----------------------------------

1.  Inicie sesión en el [Portal de administración de Azure](https://manage.windowsazure.com/), haga clic en **Servicios móviles** y, a continuación, en su aplicación.

	![][0]

2.  Haga clic en la pestaña **API** y, a continuación, haga clic en **Create a custom API**.

	![][1]

	sto muestra el cuadro de diálogo **Create a new custom API**.

3.  Cambie **Get permission** por **Everyone**, escriba *iconos* en **API name** y haga clic en el botón de comprobación.

	![][2]

    Con esto se crea la API con acceso GET público.

4.  Haga clic en la entrada nueva de iconos en la tabla de API.

	![](./media/mobile-services-windows-store-dotnet-create-pull-notifications/mobile-custom-api-select.png)

5.  Haga clic en la pestaña **Script** y reemplace el código existente por el siguiente:

         exports.get = function(request, response) {
             var wns = require('wns');
             var todoItems = request.service.tables.getTable('TodoItem');
             todoItems.where({
                 complete: false
             }).read({
                 success: sendResponse
             });
            
             function sendResponse(results) {
                 var tileText = {
                     text1: "My todo list"
                 };
                 var i = 0;
                 console.log(results);
                 results.forEach(function(item) {
                     tileText["text" + (i + 2)] = item.text;
                     i++;
                 });
                 var xml = wns.createTileSquareText01(tileText);
                 response.set('content-type', 'application/xml');
                 response.send(200, xml);
             }
         };

    Este código devuelve los tres principales elementos no completados de la tabla TodoItem y luego los carga en un objeto JSON transmitido a la función **wns**.**createTileSquareText01**. Esta función devuelve el siguiente XML de plantilla de icono:

         <tile>
             <visual>
                 <binding template="TileSquareText01">
                     <text id="1">Mi lista ToDo</text>
                     <text id="2">Tarea 1</text>
                     <text id="3">Tarea 2</text>
                     <text id="4">Tarea 3</text>
                 </binding>
             </visual>
         </tile>

    La función **exports.get** se utiliza porque el cliente enviará una solicitud GET para tener acceso a la plantilla de icono.

  <div class="dev-callout"><b>Nota:</b>
      <p>Este script de API personalizada utiliza el <a href="http://go.microsoft.com/fwlink/p/
      LinkId=306750">módulo wns</a> de Node.js, al que se hace referencia con la función <strong>require</strong>. Este módulo es distinto del <a href="http://go.microsoft.com/fwlink/p/
      LinkId=260591">objeto wns</a> que devuelve el <a href="http://msdn.microsoft.com/en-us/library/windowsazure/jj554217.aspx">objeto push</a>, que se utiliza para enviar notificaciones de inserción desde scripts de servidor.</p>
  </div>

A continuación, deberá modificar la aplicación de inicio rápido para iniciar las notificaciones periódicas que actualizan el icono dinámico solicitando la nueva API personalizada.

<a name="update-app"></a><span class="short-header">
Actualización de la aplicación</span> Actualización de la aplicación para activar las notificaciones periódicas
--------------------------------------------------------------------------------------------------------

1.  En Visual Studio, presione la tecla F5 para ejecutar la aplicación de inicio rápido del tutorial anterior.

2.  Asegúrese de que aparezca al menos un elemento. Si no hay elementos, escriba un texto en **Insert a TodoItem** y, a continuación, haga clic en **Save**.

3.  En Visual Studio, abra el archivo de proyecto App.xaml.cs y agregue la siguiente instrucción using:

         using Windows.UI.Notifications;

4.  Agregue el siguiente código en el controlador de eventos **OnLaunched**:

         TileUpdateManager.CreateTileUpdaterForApplication().StartPeriodicUpdate(
             new System.Uri(MobileService.ApplicationUri, "/api/tiles"),
             PeriodicUpdateRecurrence.Hour
         );

    Este código activa las notificaciones periódicas para solicitar datos de plantilla de icono de la nueva API personalizada **iconos**. Seleccione un valor [PeriodicUpdateRecurrance] que coincida mejor con la frecuencia de actualización de sus datos.

<a name="test-app"></a>
Prueba de la aplicación
-----------------------

1.  En Visual Studio, presione la tecla F5 para volver a ejecutar la aplicación.

    Esto activará las notificaciones periódicas.

2.  Navegue a la pantalla Inicio, busque el icono dinámico de la aplicación y observe que los datos de ese elemento ahora aparecen en el icono.

  ![](./media/mobile-services-windows-store-dotnet-create-pull-notifications/mobile-custom-api-live-tile.png)

Pasos siguientes
----------------

Ahora que ha creado una notificación periódica, podría averiguar más información acerca de los siguientes temas de Servicios móviles:

-   [Introducción a las notificaciones de inserción](/en-us/develop/mobile/tutorials/get-started-with-push-dotnet)
  <br/>Windows administra las notificaciones periódicas y solo ocurren en una programación predefinida. El servicio móvil puede enviar notificaciones de inserción a petición y pueden ser notificaciones del sistema, notificaciones de icono o notificaciones sin procesar.

-   [Referencia del script del servidor de Servicios móviles](http://go.microsoft.com/fwlink/?LinkId=262293)
  <br/>Obtenga más información sobre la creación de API personalizadas.

-   [Referencia conceptual de Servicios móviles con .NET](/en-us/develop/mobile/how-to-guides/work-with-net-client-library)
  <br/>Obtenga más información sobre cómo utilizar Servicios móviles con .NET.


<!-- Anchors. -->
[Define the custom API]: #define-custom-api
[Update the app to turn on period notifications]: #update-app
[Test the app]: #test-app
[Next Steps]: #next-steps

<!-- Images. -->
[0]: ./media/mobile-services-windows-store-dotnet-create-pull-notifications/mobile-services-selection.png
[1]: ./media/mobile-services-windows-store-dotnet-create-pull-notifications/mobile-custom-api-create.png
[2]: ./media/mobile-services-windows-store-dotnet-create-pull-notifications/mobile-custom-api-create-dialog.png
[3]: ./media/mobile-services-windows-store-dotnet-create-pull-notifications/mobile-custom-api-select.png
[4]: ./media/mobile-services-windows-store-dotnet-create-pull-notifications/mobile-custom-api-live-tile.png

<!-- URLs. -->
[Windows Push Notifications & Live Connect]: http://go.microsoft.com/fwlink/?LinkID=257677
[Mobile Services server script reference]: http://go.microsoft.com/fwlink/?LinkId=262293
[My Apps dashboard]: http://go.microsoft.com/fwlink/?LinkId=262039
[Get started with Mobile Services]: /en-us/develop/mobile/tutorials/get-started/#create-new-service
[Get started with data]: /en-us/develop/mobile/tutorials/get-started-with-data-dotnet
[Get started with authentication]: /en-us/develop/mobile/tutorials/get-started-with-users-dotnet
[Get started with push notifications]: /en-us/develop/mobile/tutorials/get-started-with-push-dotnet
[JavaScript and HTML]: mobile-services-win8-javascript/

[Azure Management Portal]: https://manage.windowsazure.com/
[Periodic notifications]: http://msdn.microsoft.com/en-us/library/windows/apps/jj150587.aspx

[Mobile Services .NET How-to Conceptual Reference]: /en-us/develop/mobile/how-to-guides/work-with-net-client-library

