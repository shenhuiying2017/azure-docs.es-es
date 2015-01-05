<properties urlDisplayName="Define a custom API that supports pull notifications" pageTitle="Definición de una API personalizada que admita notificaciones periódicas: Servicios móviles de Azure" metaKeywords="" description="Learn how to Define a custom API that supports periodic notifications in Windows Store apps that use Azure Mobile Services." metaCanonical="" services="mobile-services" documentationCenter="Mobile" title="Define a custom API that supports periodic notifications" authors="glenga" solutions="" manager="dwrede" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-windows-store" ms.devlang="dotnet" ms.topic="article" ms.date="11/22/2014" ms.author="glenga" />

# Definición de una API personalizada que admita notificaciones periódicas

<div class="dev-center-tutorial-selector"> 
	<a href="/en-us/documentation/articles/mobile-services-windows-store-dotnet-create-pull-notifications/" title="Windows Store C#" class="current">C# para Tienda Windows</a><a href="/en-us/documentation/articles/mobile-services-windows-store-javascript-create-pull-notifications/" title="Windows Store JavaScript">JavaScript para Tienda Windows</a>
</div>

En este tema se muestra cómo utilizar una API personalizada para admitir notificaciones periódicas en una aplicación de la Tienda Windows. Con las notificaciones periódicas habilitadas, Windows tendrá acceso de manera periódica a su extremo de API personalizada y usará el XML devuelto, en un formato específico de icono, para actualizar el icono de la aplicación en el menú Inicio. Para obtener más información, consulte [Notificaciones periódicas]. 

Podrá agregar esta funcionalidad a la aplicación que creó cuando completó el tutorial [Introducción a los Servicios móviles] o [Incorporación de Servicios móviles a una aplicación existente]. Para hacer esto, debe completar los siguientes pasos:

1. [Definición de la API personalizada]
2. [Actualización de la aplicación para activar las notificaciones periódicas]
3. [Prueba de la aplicación] 

Este tutorial está basado en el inicio rápido de Servicios móviles. Antes de comenzar este tutorial, primero debe completar [Introducción a los Servicios móviles] o [Incorporación de Servicios móviles a una aplicación existente].  

## <a name="define-custom-api"></a>Definición de la API personalizada

1. Inicie sesión en el [Portal de administración de Azure], haga clic en **Servicios móviles** y, a continuación, en su aplicación.

   	![][0]

2. Haga clic en la pestaña **API** y, a continuación, haga clic en **Crear una API personalizada**.

   	![][1]

	Esto muestra el cuadro de diálogo **Crear una API personalizada**.

3. Cambie **Obtener permiso** a **Todos**, escriba _iconos_ en el **nombre de la API** y, a continuación, haga clic en el botón de comprobación.

   	![][2]

	Con esto se crea la API con acceso GET público.

4. Haga clic en la entrada nueva de iconos en la tabla de API.

	![][3]

5. Haga clic en la pestaña **Script** y reemplace el código existente por el siguiente:

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
		        console.log(results)
		        results.forEach(function(item) {
		            tileText["text" + (i + 2)] = item.text;
		            i++;
		        });
		        var xml = wns.createTileSquareText01(tileText);
		        response.set('content-type', 'application/xml');
		        response.send(200, xml);
		    }
		};

	Este código devuelve los tres elementos superiores incompletos de la tabla TodoItem, los carga en un objeto JSON que se pasa a la función **wns**.**createTileSquareText01**. Esta función devuelve el siguiente XML de plantilla de icono:

		<tile>
			<visual>
				<binding template="TileSquareText01">




				</binding>
			</visual>
		</tile>

	La función **exports.get** se utiliza porque el cliente enviará una solicitud GET para tener acceso a la plantilla de icono.

   	<div class="dev-callout"><b>Nota:</b>
   		<p>Este script de API personalizada utiliza el módulo <a href="http://go.microsoft.com/fwlink/p/?LinkId=306750">wns de Node.js</a>, al que se hace referencia con la función <strong>require</strong> . Este módulo es distinto del <a href="http://go.microsoft.com/fwlink/p/?LinkId=260591">objeto wns</a> devuelto por el <a href="http://msdn.microsoft.com/en-us/library/windowsazure/jj554217.aspx">objeto push</a>, que se utiliza para enviar notificaciones de inserción desde scripts de servidor.</p>
   	</div>

A continuación, deberá modificar la aplicación de inicio rápido para iniciar las notificaciones periódicas que actualizan el icono dinámico solicitando la nueva API personalizada.

<h2><a name="update-app"></a>Actualización de la aplicación para activar las notificaciones periódicas</h2>

1. En Visual Studio, presione la tecla F5 para ejecutar la aplicación de inicio rápido del tutorial anterior.

2. Asegúrese de que aparezca al menos un elemento. Si no hay ningún elemento, escriba texto en **Insertar TodoItem** y, a continuación, haga clic en **Guardar**.

3. En Visual Studio, abra el archivo de proyecto App.xaml.cs y agregue la siguiente instrucción using:

		using Windows.UI.Notifications;

4. Agregue el siguiente código en el controlador de eventos **OnLaunched**:

        TileUpdateManager.CreateTileUpdaterForApplication().StartPeriodicUpdate(
            new System.Uri(MobileService.ApplicationUri, "/api/tiles"),
            PeriodicUpdateRecurrence.Hour
        );

	Este código activa las notificaciones periódicas para solicitar losd atos de la plantilla de icono desde la nueva API personalizada **tiles**. Seleccione un valor [PeriodicUpdateRecurrance] que se adapte mejor a la frecuencia de actualización de sus datos.

## <a name="test-app"></a>Prueba de la aplicación

1. En Visual Studio, presione la tecla F5 para volver a ejecutar la aplicación.

	Esto activará las notificaciones periódicas.

2. Diríjase a la pantalla de Inicio, localice el icono dinámico de la aplicación y compruebe que los datos del elemento se muestran ahora en el icono.

 	![][4]

## Pasos siguientes

Ahora que ha creado una notificación periódica, podría averiguar más información acerca de los siguientes temas de Servicios móviles:

* [Introducción a las notificaciones de inserción]
	<br/>Las notificaciones periódicas se administran en Windows y solo se producen conforme a una programación predefinida. Las notificaciones de inserción se pueden enviar mediante el servicio móvil previa petición y pueden ser notificaciones del sistema, notificaciones de icono y notificaciones sin procesar.

* [Referencia del script del servidor de Servicios móviles]
  <br/>Obtenga más información acerca de la creación de API personalizadas.

* [Referencia conceptual de Servicios móviles con .NET]
  <br/>Obtenga más información sobre el uso de Servicios móviles con .NET.

<!-- Anchors. -->
[Definición de la API personalizada]: #define-custom-api
[Actualización de la aplicación para activar las notificaciones periódicas]: #update-app
[Prueba de la aplicación]: #test-app
[Pasos siguientes]: #next-steps

<!-- Images. -->
[0]: ./media/mobile-services-windows-store-dotnet-create-pull-notifications/mobile-services-selection.png
[1]: ./media/mobile-services-windows-store-dotnet-create-pull-notifications/mobile-custom-api-create.png
[2]: ./media/mobile-services-windows-store-dotnet-create-pull-notifications/mobile-custom-api-create-dialog.png
[3]: ./media/mobile-services-windows-store-dotnet-create-pull-notifications/mobile-custom-api-select.png
[4]: ./media/mobile-services-windows-store-dotnet-create-pull-notifications/mobile-custom-api-live-tile.png

<!-- URLs. -->
[Notificaciones de inserción de Windows y Live Connect]: http://go.microsoft.com/fwlink/?LinkID=257677
[Referencia del script del servidor de Servicios móviles]: http://go.microsoft.com/fwlink/?LinkId=262293
[Panel Mis aplicaciones]: http://go.microsoft.com/fwlink/?LinkId=262039
[Introducción a los Servicios móviles]: /en-us/documentation/articles/mobile-services-javascript-backend-windows-store-dotnet-get-started
[Incorporación de Servicios móviles a una aplicación existente]: /en-us/documentation/articles/mobile-services-windows-store-dotnet-get-started
[Introducción a las notificaciones de inserción]: /en-us/documentation/articles/mobile-services-javascript-backend-windows-store-dotnet-get-started-push

[Portal de administración de Azure]: https://manage.windowsazure.com/
[Notificaciones periódicas]: http://msdn.microsoft.com/en-us/library/windows/apps/jj150587.aspx

[Referencia conceptual de Servicios móviles con .NET]: /en-us/documentation/articles/mobile-services-windows-dotnet-how-to-use-client-library


<!--HONumber=35.1-->
